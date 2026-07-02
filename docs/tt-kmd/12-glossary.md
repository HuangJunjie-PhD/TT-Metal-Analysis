---
project: tt-kmd
github: https://github.com/tenstorrent/tt-kmd
deepwiki: https://deepwiki.com/tenstorrent/tt-kmd/12-glossary
---

# Glossary

Relevant source files
*   [blackhole.c](https://github.com/tenstorrent/tt-kmd/blob/0ab170d7/blackhole.c)
*   [device.h](https://github.com/tenstorrent/tt-kmd/blob/0ab170d7/device.h)
*   [dkms.conf](https://github.com/tenstorrent/tt-kmd/blob/0ab170d7/dkms.conf)
*   [enumerate.c](https://github.com/tenstorrent/tt-kmd/blob/0ab170d7/enumerate.c)
*   [ioctl.h](https://github.com/tenstorrent/tt-kmd/blob/0ab170d7/ioctl.h)
*   [memory.c](https://github.com/tenstorrent/tt-kmd/blob/0ab170d7/memory.c)
*   [memory.h](https://github.com/tenstorrent/tt-kmd/blob/0ab170d7/memory.h)
*   [module.c](https://github.com/tenstorrent/tt-kmd/blob/0ab170d7/module.c)
*   [wormhole.c](https://github.com/tenstorrent/tt-kmd/blob/0ab170d7/wormhole.c)

This page provides definitions for codebase-specific terms, hardware domain concepts, and abbreviations used throughout the `tt-kmd` driver. It serves as a technical reference for onboarding engineers to understand the relationship between hardware architecture and driver implementation.

### Core Driver Concepts

| Term | Definition | Code Entities |
| --- | --- | --- |
| **Ordinal** | A logical index assigned to a Tenstorrent device (chip) based on its physical location in a system (e.g., Galaxy topology). | `tt_dev->ordinal`[device.h 39](https://github.com/tenstorrent/tt-kmd/blob/0ab170d7/device.h#L39-L39)`galaxy_bdf_to_ordinal()`[enumerate.c 48](https://github.com/tenstorrent/tt-kmd/blob/0ab170d7/enumerate.c#L48-L48) |
| **Reset Generation** | A counter incremented every time a device undergoes a reset. Used to invalidate stale user-space mappings and file descriptors. | `tt_dev->reset_gen`[device.h 34](https://github.com/tenstorrent/tt-kmd/blob/0ab170d7/device.h#L34-L34)`tenstorrent_vma_zap()`[memory.c 57](https://github.com/tenstorrent/tt-kmd/blob/0ab170d7/memory.c#L57-L57) |
| **Device Class** | A polymorphic interface defining hardware-specific operations for different chip generations (Wormhole, Blackhole). | `struct tenstorrent_device_class`[device.h 81-119](https://github.com/tenstorrent/tt-kmd/blob/0ab170d7/device.h#L81-L119)`wormhole_class`[module.c 68](https://github.com/tenstorrent/tt-kmd/blob/0ab170d7/module.c#L68-L68) |
| **Chardev Private** | Per-file-descriptor state containing process-specific resources like DMA buffers, TLB allocations, and VMA tracking. | `struct chardev_private`[chardev_private.h](https://github.com/tenstorrent/tt-kmd/blob/0ab170d7/chardev_private.h)`priv->open_fd`[enumerate.c 101](https://github.com/tenstorrent/tt-kmd/blob/0ab170d7/enumerate.c#L101-L101) |

**Sources:**[device.h 34-81](https://github.com/tenstorrent/tt-kmd/blob/0ab170d7/device.h#L34-L81)[enumerate.c 48-76](https://github.com/tenstorrent/tt-kmd/blob/0ab170d7/enumerate.c#L48-L76)[module.c 64-72](https://github.com/tenstorrent/tt-kmd/blob/0ab170d7/module.c#L64-L72)

* * *

### Hardware and Address Translation

#### NOC (Network-on-Chip)

The internal communication fabric used by Tenstorrent chips. Addresses on the NOC are typically 2D coordinates (X, Y) plus a local address.

*   **Wormhole NOC Bits:** 36-bit address space [wormhole.c 37](https://github.com/tenstorrent/tt-kmd/blob/0ab170d7/wormhole.c#L37-L37)
*   **NOC DMA:** A mechanism where host memory is mapped into the chip's NOC address space via the iATU.

#### iATU (Internal Address Translation Unit)

A PCIe controller component used to map PCIe address windows to internal SOC/NOC targets (Inbound) or map SOC/NOC accesses to Host memory (Outbound).

*   **Outbound iATU:** Maps a range of the chip's internal address space to Host physical addresses (or IOVAs). The driver manages 16 outbound regions [memory.h 60](https://github.com/tenstorrent/tt-kmd/blob/0ab170d7/memory.h#L60-L60)
*   **Implementation:**`configure_outbound_iatu`[memory.c 123](https://github.com/tenstorrent/tt-kmd/blob/0ab170d7/memory.c#L123-L123)`IATU_BASE`[wormhole.c 46](https://github.com/tenstorrent/tt-kmd/blob/0ab170d7/wormhole.c#L46-L46)

#### TLB (Translation Lookaside Buffer)

In the context of `tt-kmd`, TLBs refer to hardware registers that map BAR windows (visible to the host) to specific NOC coordinates and addresses.

*   **TLB Kinds:** Different sizes of windows (e.g., 1M, 2M, 16M, 4G).
*   **Kernel TLB:** A reserved TLB window used by the driver for internal register access and firmware communication.
*   **Implementation:**`struct tenstorrent_noc_tlb_config`[ioctl.h 268](https://github.com/tenstorrent/tt-kmd/blob/0ab170d7/ioctl.h#L268-L268)`blackhole_configure_tlb_2M`[blackhole.c 165](https://github.com/tenstorrent/tt-kmd/blob/0ab170d7/blackhole.c#L165-L165)

#### Translation Data Flow

The following diagram illustrates how a Host Virtual Address is translated to a NOC address through the driver's memory management layers.

**Address Translation Logic**

**Sources:**[memory.c 159-187](https://github.com/tenstorrent/tt-kmd/blob/0ab170d7/memory.c#L159-L187)[memory.h 21-33](https://github.com/tenstorrent/tt-kmd/blob/0ab170d7/memory.h#L21-L33)[ioctl.h 173-182](https://github.com/tenstorrent/tt-kmd/blob/0ab170d7/ioctl.h#L173-L182)

* * *

### Firmware and Communication

#### ARC (Argonaut RISC Core)

The on-chip management processor responsible for power management, telemetry, and thermal control.

*   **CSM (Cluster Scratchpad Memory):** Memory accessible by the ARC, often used for message buffers.
*   **QCB (Queue Control Block):** A structure in CSM used to manage the command/response queue between the driver and ARC firmware.
*   **Implementation:**`ARC_MSG_QCB_PTR`[wormhole.c 119](https://github.com/tenstorrent/tt-kmd/blob/0ab170d7/wormhole.c#L119-L119)`csm_read32`[wormhole.c 145](https://github.com/tenstorrent/tt-kmd/blob/0ab170d7/wormhole.c#L145-L145)

#### Scratch Registers

Small hardware registers used for low-level handshaking and passing small arguments to firmware before the full message queue is initialized.

*   **Implementation:**`SCRATCH_REG(n)`[wormhole.c 90](https://github.com/tenstorrent/tt-kmd/blob/0ab170d7/wormhole.c#L90-L90)`POST_CODE_REG`[wormhole.c 101](https://github.com/tenstorrent/tt-kmd/blob/0ab170d7/wormhole.c#L101-L101)

**Firmware Interaction Entities**

**Sources:**[wormhole.c 78-122](https://github.com/tenstorrent/tt-kmd/blob/0ab170d7/wormhole.c#L78-L122)[device.h 72-76](https://github.com/tenstorrent/tt-kmd/blob/0ab170d7/device.h#L72-L76)[blackhole.c 56-74](https://github.com/tenstorrent/tt-kmd/blob/0ab170d7/blackhole.c#L56-L74)

* * *

### Resource Management

| Abbreviation | Full Name | Description |
| --- | --- | --- |
| **VMA** | Virtual Memory Area | A Linux kernel structure representing a range of user virtual memory. `tt-kmd` tracks these to "zap" (unmap) them during hardware resets [memory.c 57](https://github.com/tenstorrent/tt-kmd/blob/0ab170d7/memory.c#L57-L57) |
| **GUP** | Get User Pages | The kernel API used to pin user-space memory into RAM so the hardware can perform DMA safely [memory.c 190-228](https://github.com/tenstorrent/tt-kmd/blob/0ab170d7/memory.c#L190-L228) |
| **IOVA** | IO Virtual Address | The address used by the hardware when an IOMMU is active. If no IOMMU is present, this is equivalent to the Physical Address (PA) [memory.c 131-140](https://github.com/tenstorrent/tt-kmd/blob/0ab170d7/memory.c#L131-L140) |
| **WC / UC** | Write-Combining / Uncacheable | Memory mapping attributes. WC is used for performance when writing to buffers; UC is used for registers where ordering and visibility are critical [enumerate.c 187-195](https://github.com/tenstorrent/tt-kmd/blob/0ab170d7/enumerate.c#L187-L195) |

**Sources:**[memory.c 57-228](https://github.com/tenstorrent/tt-kmd/blob/0ab170d7/memory.c#L57-L228)[enumerate.c 187-195](https://github.com/tenstorrent/tt-kmd/blob/0ab170d7/enumerate.c#L187-L195)[device.h 34-35](https://github.com/tenstorrent/tt-kmd/blob/0ab170d7/device.h#L34-L35)

Dismiss
Refresh this wiki

Enter email to refresh
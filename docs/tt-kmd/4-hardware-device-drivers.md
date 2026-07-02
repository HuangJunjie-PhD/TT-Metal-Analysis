---
project: tt-kmd
github: https://github.com/tenstorrent/tt-kmd
deepwiki: https://deepwiki.com/tenstorrent/tt-kmd/4-hardware-device-drivers
---

# Hardware Device Drivers

Relevant source files
*   [blackhole.c](https://github.com/tenstorrent/tt-kmd/blob/0ab170d7/blackhole.c)
*   [pcie.c](https://github.com/tenstorrent/tt-kmd/blob/0ab170d7/pcie.c)
*   [pcie.h](https://github.com/tenstorrent/tt-kmd/blob/0ab170d7/pcie.h)
*   [wormhole.c](https://github.com/tenstorrent/tt-kmd/blob/0ab170d7/wormhole.c)
*   [wormhole.h](https://github.com/tenstorrent/tt-kmd/blob/0ab170d7/wormhole.h)

## Purpose and Scope

This section documents the hardware-specific device driver implementations for different Tenstorrent chip generations. The driver uses a device class abstraction pattern to support multiple hardware architectures through a common interface. Currently, two chip generations are supported: Wormhole and Blackhole.

For details on the overall driver architecture and device abstraction layer, see [Driver Architecture](https://deepwiki.com/tenstorrent/tt-kmd/3-driver-architecture) and [Device Abstraction Layer](https://deepwiki.com/tenstorrent/tt-kmd/3.2-device-abstraction-layer). For PCI device discovery and management, see [PCI Device Discovery and Management](https://deepwiki.com/tenstorrent/tt-kmd/3.3-pci-device-discovery-and-management).

## Device Class Abstraction Pattern

The driver implements hardware-specific functionality through the `tenstorrent_device_class` structure, which defines virtual method tables for polymorphic hardware operations. Each chip generation provides its own implementation of this class.

### Device Class Structure

The `tenstorrent_device_class` structure [device.h 62-87](https://github.com/tenstorrent/tt-kmd/blob/0ab170d7/device.h#L62-L87) defines function pointers for all hardware-specific operations:

**Sources:**[device.h 62-87](https://github.com/tenstorrent/tt-kmd/blob/0ab170d7/device.h#L62-L87)[device.h 22-58](https://github.com/tenstorrent/tt-kmd/blob/0ab170d7/device.h#L22-L58)[wormhole.h 10-24](https://github.com/tenstorrent/tt-kmd/blob/0ab170d7/wormhole.h#L10-L24)[blackhole.c 1042-1065](https://github.com/tenstorrent/tt-kmd/blob/0ab170d7/blackhole.c#L1042-L1065) (implied structure)

### Polymorphic Dispatch Pattern

Hardware operations are invoked through the device class function pointers, enabling runtime dispatch to the appropriate implementation:

**Sources:**[device.h 62-87](https://github.com/tenstorrent/tt-kmd/blob/0ab170d7/device.h#L62-L87)[wormhole.c 1046-1070](https://github.com/tenstorrent/tt-kmd/blob/0ab170d7/wormhole.c#L1046-L1070)[blackhole.c 1099-1122](https://github.com/tenstorrent/tt-kmd/blob/0ab170d7/blackhole.c#L1099-L1122)

## Hardware-Specific Implementations

The driver currently supports two Tenstorrent chip architectures, each with distinct hardware characteristics.

### Wormhole Device Driver

The Wormhole implementation is defined in [wormhole.c 1046-1070](https://github.com/tenstorrent/tt-kmd/blob/0ab170d7/wormhole.c#L1046-L1070) and provides:

| Characteristic | Value | Description |
| --- | --- | --- |
| **Name** | `"Wormhole"` | Device class identifier |
| **Instance Size** | `sizeof(struct wormhole_device)` | Memory allocation size |
| **DMA Address Bits** | 32 | Host-side DMA addressing width |
| **NOC Address Bits** | 36 | Device-side NOC addressing width [wormhole.c 37](https://github.com/tenstorrent/tt-kmd/blob/0ab170d7/wormhole.c#L37-L37) |
| **NOC DMA Limit** | `0xFFFE0000 - 1` | Maximum NOC address for DMA |
| **NOC PCIe Offset** | `0x800000000ULL` | Base offset for PCIe in NOC space [wormhole.c 92](https://github.com/tenstorrent/tt-kmd/blob/0ab170d7/wormhole.c#L92-L92) |
| **TLB Kinds** | 3 | Three window sizes: 1M, 2M, 16M [wormhole.c 20-35](https://github.com/tenstorrent/tt-kmd/blob/0ab170d7/wormhole.c#L20-L35) |
| **TLB Counts** | 156 × 1M, 10 × 2M, 20 × 16M | Translation window inventory [wormhole.c 20-30](https://github.com/tenstorrent/tt-kmd/blob/0ab170d7/wormhole.c#L20-L30) |

**Key Wormhole-Specific Features:**

*   **BAR Layout**: BAR2 for iATU config [wormhole.c 46](https://github.com/tenstorrent/tt-kmd/blob/0ab170d7/wormhole.c#L46-L46) BAR4 mapped to system registers starting at `0x1E000000`[wormhole.c 76](https://github.com/tenstorrent/tt-kmd/blob/0ab170d7/wormhole.c#L76-L76)
*   **Firmware Protocol**: Scratch register-based messaging (SR5) with IRQ trigger [wormhole.c 108-112](https://github.com/tenstorrent/tt-kmd/blob/0ab170d7/wormhole.c#L108-L112)
*   **Telemetry**: Deferred initialization with `fw_ready_work` to handle firmware boot time [wormhole.h 19](https://github.com/tenstorrent/tt-kmd/blob/0ab170d7/wormhole.h#L19-L19)[wormhole.c 715](https://github.com/tenstorrent/tt-kmd/blob/0ab170d7/wormhole.c#L715-L715)
*   **PCIe Controllers**: NOC coordinates at (0,3) and (9,8) [wormhole.c 93-96](https://github.com/tenstorrent/tt-kmd/blob/0ab170d7/wormhole.c#L93-L96)

For detailed Wormhole documentation, see [Wormhole Device Driver](https://deepwiki.com/tenstorrent/tt-kmd/4.1-wormhole-device-driver).

**Sources:**[wormhole.c 20-44](https://github.com/tenstorrent/tt-kmd/blob/0ab170d7/wormhole.c#L20-L44)[wormhole.c 1046-1070](https://github.com/tenstorrent/tt-kmd/blob/0ab170d7/wormhole.c#L1046-L1070)[wormhole.h 10-24](https://github.com/tenstorrent/tt-kmd/blob/0ab170d7/wormhole.h#L10-L24)

### Blackhole Device Driver

The Blackhole implementation is defined in [blackhole.c 1099-1122](https://github.com/tenstorrent/tt-kmd/blob/0ab170d7/blackhole.c#L1099-L1122) and provides:

| Characteristic | Value | Description |
| --- | --- | --- |
| **Name** | `"Blackhole"` | Device class identifier |
| **Instance Size** | `sizeof(struct blackhole_device)` | Memory allocation size |
| **DMA Address Bits** | 58 | Host-side DMA addressing width |
| **NOC Address Bits** | 58 | Device-side NOC addressing width |
| **NOC DMA Limit** | `(1ULL << 58) - 1` | Maximum NOC address for DMA |
| **NOC PCIe Offset** | `4ULL << 58` | Base offset for PCIe in NOC space [blackhole.c 51](https://github.com/tenstorrent/tt-kmd/blob/0ab170d7/blackhole.c#L51-L51) |
| **TLB Kinds** | 2 | Two window sizes: 2M, 4G [blackhole.c 20-25](https://github.com/tenstorrent/tt-kmd/blob/0ab170d7/blackhole.c#L20-L25) |
| **TLB Counts** | 202 × 2M, 8 × 4G | Translation window inventory [blackhole.c 20-25](https://github.com/tenstorrent/tt-kmd/blob/0ab170d7/blackhole.c#L20-L25) |

**Key Blackhole-Specific Features:**

*   **BAR Layout**: TLB registers at `0x1FC00000`[blackhole.c 33](https://github.com/tenstorrent/tt-kmd/blob/0ab170d7/blackhole.c#L33-L33); BAR4 used for large 4G TLB windows.
*   **Firmware Protocol**: ARC message queue (QCB) using `ARC_MSG_QCB_PTR`[blackhole.c 64](https://github.com/tenstorrent/tt-kmd/blob/0ab170d7/blackhole.c#L64-L64)
*   **Telemetry**: ARC-owned telemetry via `ARC_TELEMETRY_PTR`[blackhole.c 60](https://github.com/tenstorrent/tt-kmd/blob/0ab170d7/blackhole.c#L60-L60)
*   **Enhanced Addressing**: Supports 58-bit address space with 43-bit addresses in 2M TLBs [blackhole.c 121](https://github.com/tenstorrent/tt-kmd/blob/0ab170d7/blackhole.c#L121-L121)

For detailed Blackhole documentation, see [Blackhole Device Driver](https://deepwiki.com/tenstorrent/tt-kmd/4.2-blackhole-device-driver).

**Sources:**[blackhole.c 20-75](https://github.com/tenstorrent/tt-kmd/blob/0ab170d7/blackhole.c#L20-L75)[blackhole.c 1099-1122](https://github.com/tenstorrent/tt-kmd/blob/0ab170d7/blackhole.c#L1099-L1122)

### Hardware Comparison Matrix

**Hardware Architecture Comparison**

**Sources:**[wormhole.c 20-44](https://github.com/tenstorrent/tt-kmd/blob/0ab170d7/wormhole.c#L20-L44)[blackhole.c 20-75](https://github.com/tenstorrent/tt-kmd/blob/0ab170d7/blackhole.c#L20-L75)

## Device Lifecycle and Initialization

Hardware-specific implementations follow a well-defined initialization and cleanup sequence through device class callbacks.

### Initialization Sequence

**Sources:**[wormhole.c 705-756](https://github.com/tenstorrent/tt-kmd/blob/0ab170d7/wormhole.c#L705-L756)[blackhole.c 846-941](https://github.com/tenstorrent/tt-kmd/blob/0ab170d7/blackhole.c#L846-L941)

### Initialization Phase Responsibilities

#### Phase 1: Device Initialization (`init_device`)

*   **Wormhole**[wormhole.c 709-744](https://github.com/tenstorrent/tt-kmd/blob/0ab170d7/wormhole.c#L709-L744): Maps BAR2 and BAR4 [wormhole.c 724-728](https://github.com/tenstorrent/tt-kmd/blob/0ab170d7/wormhole.c#L724-L728) initializes `fw_ready_work`[wormhole.c 715](https://github.com/tenstorrent/tt-kmd/blob/0ab170d7/wormhole.c#L715-L715) and reserves the kernel TLB [wormhole.c 730](https://github.com/tenstorrent/tt-kmd/blob/0ab170d7/wormhole.c#L730-L730)
*   **Blackhole**[blackhole.c 853-901](https://github.com/tenstorrent/tt-kmd/blob/0ab170d7/blackhole.c#L853-L901): Maps BAR0 and BAR2 [blackhole.c 870-873](https://github.com/tenstorrent/tt-kmd/blob/0ab170d7/blackhole.c#L870-L873) determines available 4G windows from BAR4 size [blackhole.c 857-861](https://github.com/tenstorrent/tt-kmd/blob/0ab170d7/blackhole.c#L857-L861) and reserves the kernel TLB [blackhole.c 892](https://github.com/tenstorrent/tt-kmd/blob/0ab170d7/blackhole.c#L892-L892)

#### Phase 2: Hardware Initialization (`init_hardware`)

*   **Wormhole**[wormhole.c 746-760](https://github.com/tenstorrent/tt-kmd/blob/0ab170d7/wormhole.c#L746-L760): Configures iATU inbound region [wormhole.c 749](https://github.com/tenstorrent/tt-kmd/blob/0ab170d7/wormhole.c#L749-L749) checks `arc_l2_is_running()`[wormhole.c 751](https://github.com/tenstorrent/tt-kmd/blob/0ab170d7/wormhole.c#L751-L751) and retrains PCIe link if necessary via `wormhole_complete_pcie_init()`[wormhole.c 755](https://github.com/tenstorrent/tt-kmd/blob/0ab170d7/wormhole.c#L755-L755)
*   **Blackhole**[blackhole.c 903-928](https://github.com/tenstorrent/tt-kmd/blob/0ab170d7/blackhole.c#L903-L928): Detects Galaxy board topology [blackhole.c 908](https://github.com/tenstorrent/tt-kmd/blob/0ab170d7/blackhole.c#L908-L908) sets MRRS [blackhole.c 915](https://github.com/tenstorrent/tt-kmd/blob/0ab170d7/blackhole.c#L915-L915) and sends initial `ASIC_STATE0` messages to ARC [blackhole.c 917](https://github.com/tenstorrent/tt-kmd/blob/0ab170d7/blackhole.c#L917-L917)

#### Phase 3: Telemetry Initialization (`init_telemetry`)

*   **Wormhole**[wormhole.c 762-781](https://github.com/tenstorrent/tt-kmd/blob/0ab170d7/wormhole.c#L762-L781): Schedules `fw_ready_work` to poll for ARC telemetry readiness [wormhole.c 778](https://github.com/tenstorrent/tt-kmd/blob/0ab170d7/wormhole.c#L778-L778)
*   **Blackhole**[blackhole.c 930-961](https://github.com/tenstorrent/tt-kmd/blob/0ab170d7/blackhole.c#L930-L961): Synchronously probes telemetry tags and registers with the `hwmon` subsystem [blackhole.c 950](https://github.com/tenstorrent/tt-kmd/blob/0ab170d7/blackhole.c#L950-L950)

## Firmware Communication Protocols

Both architectures communicate with an on-chip ARC processor for power management, resets, and initialization.

### Wormhole Protocol (Scratch Registers)

Wormhole uses `wormhole_send_arc_fw_message_with_args()`[wormhole.c 205-241](https://github.com/tenstorrent/tt-kmd/blob/0ab170d7/wormhole.c#L205-L241) It writes to `SCRATCH_REG(3)` for arguments and `SCRATCH_REG(5)` for the message ID, then triggers an interrupt via `ARC_MISC_CNTL_REG`[wormhole.c 210-220](https://github.com/tenstorrent/tt-kmd/blob/0ab170d7/wormhole.c#L210-L220)

### Blackhole Protocol (Message Queues)

Blackhole uses a structured Queue Control Block (QCB). The driver pushes messages into a circular buffer and triggers the ARC via `ARC_MSI_FIFO`[blackhole.c 815](https://github.com/tenstorrent/tt-kmd/blob/0ab170d7/blackhole.c#L815-L815) Responses are popped from a separate response queue [blackhole.c 817](https://github.com/tenstorrent/tt-kmd/blob/0ab170d7/blackhole.c#L817-L817)

For detailed firmware protocol documentation, see [ARC Firmware Communication](https://deepwiki.com/tenstorrent/tt-kmd/4.3-arc-firmware-communication).

**Sources:**[wormhole.c 205-241](https://github.com/tenstorrent/tt-kmd/blob/0ab170d7/wormhole.c#L205-L241)[blackhole.c 670-821](https://github.com/tenstorrent/tt-kmd/blob/0ab170d7/blackhole.c#L670-L821)

## Related Documentation

*   **[Wormhole Device Driver](https://deepwiki.com/tenstorrent/tt-kmd/4.1-wormhole-device-driver)**: Detailed Wormhole implementation, TLB configuration, and iATU setup.
*   **[Blackhole Device Driver](https://deepwiki.com/tenstorrent/tt-kmd/4.2-blackhole-device-driver)**: Detailed Blackhole implementation, ARC message queue, and 58-bit addressing.
*   **[ARC Firmware Communication](https://deepwiki.com/tenstorrent/tt-kmd/4.3-arc-firmware-communication)**: Protocol details for communicating with on-chip processors.
*   **[Device Abstraction Layer](https://deepwiki.com/tenstorrent/tt-kmd/3.2-device-abstraction-layer)**: Common device abstraction patterns and polymorphic interfaces.
*   **[TLB Management](https://deepwiki.com/tenstorrent/tt-kmd/6.3-tlb-management)**: Generic TLB allocation and configuration logic.

Dismiss
Refresh this wiki

Enter email to refresh
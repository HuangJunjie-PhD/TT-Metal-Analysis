---
project: tt-kmd
github: https://github.com/tenstorrent/tt-kmd
deepwiki: https://deepwiki.com/tenstorrent/tt-kmd/5-user-space-interface
---

# User-Space Interface

Relevant source files
*   [chardev.c](https://github.com/tenstorrent/tt-kmd/blob/0ab170d7/chardev.c)
*   [ioctl.h](https://github.com/tenstorrent/tt-kmd/blob/0ab170d7/ioctl.h)
*   [memory.c](https://github.com/tenstorrent/tt-kmd/blob/0ab170d7/memory.c)
*   [memory.h](https://github.com/tenstorrent/tt-kmd/blob/0ab170d7/memory.h)

## Purpose and Scope

This page provides an overview of how user-space applications interact with the Tenstorrent kernel driver. It covers the character device model, file operations, per-file-descriptor state management, and the IOCTL command dispatch mechanism. For detailed information about specific file operations, see [Character Device Operations](https://deepwiki.com/tenstorrent/tt-kmd/5.1-character-device-operations). For a complete reference of all IOCTL commands and their parameters, see [IOCTL Command Reference](https://deepwiki.com/tenstorrent/tt-kmd/5.2-ioctl-command-reference). For details on memory mapping operations, see [Memory Mapping Interface](https://deepwiki.com/tenstorrent/tt-kmd/5.3-memory-mapping-interface).

## Character Device Model

The driver exposes each Tenstorrent device as a character device under `/dev/tenstorrent/N`, where `N` is the device ordinal (0, 1, 2, etc.). User-space applications interact with the device by opening this character device file and performing operations through standard Linux file operations (`open`, `close`, `mmap`, `ioctl`).

The character device infrastructure is initialized during module load and registers device nodes as PCI devices are discovered. Each device has a unique major/minor number combination derived from the base `tt_device_id` allocated during driver initialization [chardev.c 48-83](https://github.com/tenstorrent/tt-kmd/blob/0ab170d7/chardev.c#L48-L83)

**Sources:**[chardev.c 40-46](https://github.com/tenstorrent/tt-kmd/blob/0ab170d7/chardev.c#L40-L46)[chardev.c 48-83](https://github.com/tenstorrent/tt-kmd/blob/0ab170d7/chardev.c#L48-L83)[chardev.c 90-118](https://github.com/tenstorrent/tt-kmd/blob/0ab170d7/chardev.c#L90-L118)

## File Operations Structure

The driver implements four primary file operations through the `chardev_fops` structure [chardev.c 40-46](https://github.com/tenstorrent/tt-kmd/blob/0ab170d7/chardev.c#L40-L46):

| Operation | Handler Function | Line | Purpose |
| --- | --- | --- | --- |
| `open` | `tt_cdev_open` | [chardev.c 629](https://github.com/tenstorrent/tt-kmd/blob/0ab170d7/chardev.c#L629-L629) | Allocate per-FD state, capture reset generation |
| `release` | `tt_cdev_release` | [chardev.c 676](https://github.com/tenstorrent/tt-kmd/blob/0ab170d7/chardev.c#L676-L676) | Clean up resources, release locks, update power |
| `unlocked_ioctl` | `tt_cdev_ioctl` | [chardev.c 465](https://github.com/tenstorrent/tt-kmd/blob/0ab170d7/chardev.c#L465-L465) | Dispatch IOCTL commands |
| `mmap` | `tt_cdev_mmap` | [chardev.c 578](https://github.com/tenstorrent/tt-kmd/blob/0ab170d7/chardev.c#L578-L578) | Map device memory to user space |

All file operations validate the file descriptor's validity by checking:

1.   **Detached flag**: Device has been removed [chardev.c 478-481](https://github.com/tenstorrent/tt-kmd/blob/0ab170d7/chardev.c#L478-L481)[chardev.c 590-593](https://github.com/tenstorrent/tt-kmd/blob/0ab170d7/chardev.c#L590-L593)
2.   **Reset generation match**: FD was opened before a reset [chardev.c 484-487](https://github.com/tenstorrent/tt-kmd/blob/0ab170d7/chardev.c#L484-L487)[chardev.c 596-599](https://github.com/tenstorrent/tt-kmd/blob/0ab170d7/chardev.c#L596-L599)
3.   **Hardware initialization state**: Device requires re-initialization [chardev.c 490-498](https://github.com/tenstorrent/tt-kmd/blob/0ab170d7/chardev.c#L490-L498)

**Sources:**[chardev.c 40-46](https://github.com/tenstorrent/tt-kmd/blob/0ab170d7/chardev.c#L40-L46)[chardev.c 465-576](https://github.com/tenstorrent/tt-kmd/blob/0ab170d7/chardev.c#L465-L576)[chardev.c 578-606](https://github.com/tenstorrent/tt-kmd/blob/0ab170d7/chardev.c#L578-L606)[chardev.c 629-674](https://github.com/tenstorrent/tt-kmd/blob/0ab170d7/chardev.c#L629-L674)[chardev.c 676-725](https://github.com/tenstorrent/tt-kmd/blob/0ab170d7/chardev.c#L676-L725)

## Per-File-Descriptor State

Each open file descriptor maintains its own private state through the `struct chardev_private` structure allocated in `tt_cdev_open`[chardev.c 635-653](https://github.com/tenstorrent/tt-kmd/blob/0ab170d7/chardev.c#L635-L653) This structure tracks all resources owned by the file descriptor, ensuring proper cleanup on close even if the application terminates abnormally.

**Sources:**[chardev_private.h 1-100](https://github.com/tenstorrent/tt-kmd/blob/0ab170d7/chardev_private.h#L1-L100)[chardev.c 635-653](https://github.com/tenstorrent/tt-kmd/blob/0ab170d7/chardev.c#L635-L653)

## IOCTL Command Dispatch

The driver exposes 16 IOCTL commands organized into functional categories. All IOCTL commands use magic number `0xFA`[ioctl.h 12](https://github.com/tenstorrent/tt-kmd/blob/0ab170d7/ioctl.h#L12-L12) and follow a structured input/output pattern with size validation for forward/backward compatibility.

The IOCTL handler implements a two-tier locking strategy [chardev.c 472-475](https://github.com/tenstorrent/tt-kmd/blob/0ab170d7/chardev.c#L472-L475):

*   **Read lock** (`down_read`): Allows concurrent normal operations.
*   **Write lock** (`down_write`): Exclusive access for reset operations.

**Sources:**[chardev.c 465-576](https://github.com/tenstorrent/tt-kmd/blob/0ab170d7/chardev.c#L465-L576)[ioctl.h 12-29](https://github.com/tenstorrent/tt-kmd/blob/0ab170d7/ioctl.h#L12-L29)

## IOCTL Command Categories

### Device Information Commands

| IOCTL | Magic | Purpose |
| --- | --- | --- |
| `TENSTORRENT_IOCTL_GET_DEVICE_INFO` | 0xFA00 | Query PCI IDs, bus location, capabilities [ioctl.h 14](https://github.com/tenstorrent/tt-kmd/blob/0ab170d7/ioctl.h#L14-L14) |
| `TENSTORRENT_IOCTL_GET_DRIVER_INFO` | 0xFA05 | Query driver version information [ioctl.h 19](https://github.com/tenstorrent/tt-kmd/blob/0ab170d7/ioctl.h#L19-L19) |
| `TENSTORRENT_IOCTL_QUERY_MAPPINGS` | 0xFA02 | Query available memory mappings [ioctl.h 16](https://github.com/tenstorrent/tt-kmd/blob/0ab170d7/ioctl.h#L16-L16) |

**Sources:**[ioctl.h 14-19](https://github.com/tenstorrent/tt-kmd/blob/0ab170d7/ioctl.h#L14-L19)[chardev.c 127-189](https://github.com/tenstorrent/tt-kmd/blob/0ab170d7/chardev.c#L127-L189)[memory.c 36-37](https://github.com/tenstorrent/tt-kmd/blob/0ab170d7/memory.c#L36-L37)

### Memory Management Commands

| IOCTL | Magic | Purpose |
| --- | --- | --- |
| `TENSTORRENT_IOCTL_ALLOCATE_DMA_BUF` | 0xFA03 | Allocate kernel DMA buffer [ioctl.h 17](https://github.com/tenstorrent/tt-kmd/blob/0ab170d7/ioctl.h#L17-L17) |
| `TENSTORRENT_IOCTL_FREE_DMA_BUF` | 0xFA04 | Free DMA buffer [ioctl.h 18](https://github.com/tenstorrent/tt-kmd/blob/0ab170d7/ioctl.h#L18-L18) |
| `TENSTORRENT_IOCTL_PIN_PAGES` | 0xFA07 | Pin user-space pages for DMA [ioctl.h 21](https://github.com/tenstorrent/tt-kmd/blob/0ab170d7/ioctl.h#L21-L21) |
| `TENSTORRENT_IOCTL_UNPIN_PAGES` | 0xFA0A | Unpin user-space pages [ioctl.h 24](https://github.com/tenstorrent/tt-kmd/blob/0ab170d7/ioctl.h#L24-L24) |
| `TENSTORRENT_IOCTL_MAP_PEER_BAR` | 0xFA09 | Map peer device BAR for P2P [ioctl.h 23](https://github.com/tenstorrent/tt-kmd/blob/0ab170d7/ioctl.h#L23-L23) |

**Sources:**[ioctl.h 17-24](https://github.com/tenstorrent/tt-kmd/blob/0ab170d7/ioctl.h#L17-L24)[memory.c 38-47](https://github.com/tenstorrent/tt-kmd/blob/0ab170d7/memory.c#L38-L47)

### TLB Management Commands

| IOCTL | Magic | Purpose |
| --- | --- | --- |
| `TENSTORRENT_IOCTL_ALLOCATE_TLB` | 0xFA0B | Allocate inbound TLB entry [ioctl.h 25](https://github.com/tenstorrent/tt-kmd/blob/0ab170d7/ioctl.h#L25-L25) |
| `TENSTORRENT_IOCTL_FREE_TLB` | 0xFA0C | Free TLB entry [ioctl.h 26](https://github.com/tenstorrent/tt-kmd/blob/0ab170d7/ioctl.h#L26-L26) |
| `TENSTORRENT_IOCTL_CONFIGURE_TLB` | 0xFA0D | Configure TLB for NOC access [ioctl.h 27](https://github.com/tenstorrent/tt-kmd/blob/0ab170d7/ioctl.h#L27-L27) |

**Sources:**[ioctl.h 25-27](https://github.com/tenstorrent/tt-kmd/blob/0ab170d7/ioctl.h#L25-L27)[memory.c 48-53](https://github.com/tenstorrent/tt-kmd/blob/0ab170d7/memory.c#L48-L53)

### Control Commands

| IOCTL | Magic | Purpose |
| --- | --- | --- |
| `TENSTORRENT_IOCTL_RESET_DEVICE` | 0xFA06 | Reset device hardware [ioctl.h 20](https://github.com/tenstorrent/tt-kmd/blob/0ab170d7/ioctl.h#L20-L20) |
| `TENSTORRENT_IOCTL_LOCK_CTL` | 0xFA08 | User-space resource locking [ioctl.h 22](https://github.com/tenstorrent/tt-kmd/blob/0ab170d7/ioctl.h#L22-L22) |
| `TENSTORRENT_IOCTL_SET_POWER_STATE` | 0xFA0F | Set power state preferences [ioctl.h 29](https://github.com/tenstorrent/tt-kmd/blob/0ab170d7/ioctl.h#L29-L29) |
| `TENSTORRENT_IOCTL_SET_NOC_CLEANUP` | 0xFA0E | Register cleanup action [ioctl.h 28](https://github.com/tenstorrent/tt-kmd/blob/0ab170d7/ioctl.h#L28-L28) |

**Sources:**[ioctl.h 20-29](https://github.com/tenstorrent/tt-kmd/blob/0ab170d7/ioctl.h#L20-L29)[chardev.c 191-463](https://github.com/tenstorrent/tt-kmd/blob/0ab170d7/chardev.c#L191-L463)

## File Descriptor Lifecycle

The lifecycle involves allocation in `open`, resource tracking during `Active` use, and comprehensive cleanup in `release`[chardev.c 629-725](https://github.com/tenstorrent/tt-kmd/blob/0ab170d7/chardev.c#L629-L725)

**Sources:**[chardev.c 629-674](https://github.com/tenstorrent/tt-kmd/blob/0ab170d7/chardev.c#L629-L674)[chardev.c 465-576](https://github.com/tenstorrent/tt-kmd/blob/0ab170d7/chardev.c#L465-L576)[chardev.c 578-606](https://github.com/tenstorrent/tt-kmd/blob/0ab170d7/chardev.c#L578-L606)[chardev.c 676-725](https://github.com/tenstorrent/tt-kmd/blob/0ab170d7/chardev.c#L676-L725)

## Reset Generation and Invalidation

The driver uses a generation counter (`reset_gen`) to invalidate file descriptors opened before a device reset. When a reset occurs, the global counter increments [chardev.c 223](https://github.com/tenstorrent/tt-kmd/blob/0ab170d7/chardev.c#L223-L223)[chardev.c 227](https://github.com/tenstorrent/tt-kmd/blob/0ab170d7/chardev.c#L227-L227)[chardev.c 232](https://github.com/tenstorrent/tt-kmd/blob/0ab170d7/chardev.c#L232-L232) and all existing VMAs are zapped [chardev.c 220](https://github.com/tenstorrent/tt-kmd/blob/0ab170d7/chardev.c#L220-L220)[chardev.c 224](https://github.com/tenstorrent/tt-kmd/blob/0ab170d7/chardev.c#L224-L224)[chardev.c 228](https://github.com/tenstorrent/tt-kmd/blob/0ab170d7/chardev.c#L228-L228) Subsequent operations on old file descriptors fail with `ENODEV`[chardev.c 484-487](https://github.com/tenstorrent/tt-kmd/blob/0ab170d7/chardev.c#L484-L487)

**Sources:**[chardev.c 191-266](https://github.com/tenstorrent/tt-kmd/blob/0ab170d7/chardev.c#L191-L266)[chardev.c 484-487](https://github.com/tenstorrent/tt-kmd/blob/0ab170d7/chardev.c#L484-L487)[chardev.c 596-599](https://github.com/tenstorrent/tt-kmd/blob/0ab170d7/chardev.c#L596-L599)[chardev.c 649](https://github.com/tenstorrent/tt-kmd/blob/0ab170d7/chardev.c#L649-L649)

## Power Management Integration

The driver implements power-aware client handling through aggregation. Applications can indicate power awareness by opening the device with `O_APPEND`[chardev.c 655-657](https://github.com/tenstorrent/tt-kmd/blob/0ab170d7/chardev.c#L655-L657)

**Power-aware clients** (`O_APPEND`):

*   Initial power state: all flags set to 0 (minimum power) [chardev.c 656](https://github.com/tenstorrent/tt-kmd/blob/0ab170d7/chardev.c#L656-L656)
*   No automatic power state sent at open.

**Legacy clients** (no `O_APPEND`):

*   Initial power state: all enabled except `TT_POWER_FLAG_MAX_AI_CLK`[chardev.c 659-660](https://github.com/tenstorrent/tt-kmd/blob/0ab170d7/chardev.c#L659-L660)
*   Automatic power aggregation triggered at open [chardev.c 664-670](https://github.com/tenstorrent/tt-kmd/blob/0ab170d7/chardev.c#L664-L670)

**Sources:**[chardev.c 377-437](https://github.com/tenstorrent/tt-kmd/blob/0ab170d7/chardev.c#L377-L437)[chardev.c 439-463](https://github.com/tenstorrent/tt-kmd/blob/0ab170d7/chardev.c#L439-L463)[chardev.c 655-671](https://github.com/tenstorrent/tt-kmd/blob/0ab170d7/chardev.c#L655-L671)[ioctl.h 359-409](https://github.com/tenstorrent/tt-kmd/blob/0ab170d7/ioctl.h#L359-L409)

## Resource Lock Mechanism

The driver provides 64 user-space resource locks [ioctl.h 43](https://github.com/tenstorrent/tt-kmd/blob/0ab170d7/ioctl.h#L43-L43) for inter-process coordination. These locks are advisory. Operations include `ACQUIRE`, `ACQUIRE_BLOCKING`, `RELEASE`, and `TEST`[ioctl.h 210-213](https://github.com/tenstorrent/tt-kmd/blob/0ab170d7/ioctl.h#L210-L213) Locks are automatically released when the file descriptor closes [chardev.c 702-707](https://github.com/tenstorrent/tt-kmd/blob/0ab170d7/chardev.c#L702-L707)

**Sources:**[chardev.c 270-329](https://github.com/tenstorrent/tt-kmd/blob/0ab170d7/chardev.c#L270-L329)[chardev.c 702-707](https://github.com/tenstorrent/tt-kmd/blob/0ab170d7/chardev.c#L702-L707)[ioctl.h 208-247](https://github.com/tenstorrent/tt-kmd/blob/0ab170d7/ioctl.h#L208-L247)

## NOC Cleanup Actions

Applications can register a cleanup action (a single NOC write) that the driver executes when the file descriptor closes [chardev.c 687-697](https://github.com/tenstorrent/tt-kmd/blob/0ab170d7/chardev.c#L687-L697) This provides a reliable notification to device-side software if the host process terminates unexpectedly [chardev.c 331-375](https://github.com/tenstorrent/tt-kmd/blob/0ab170d7/chardev.c#L331-L375)

**Sources:**[chardev.c 331-375](https://github.com/tenstorrent/tt-kmd/blob/0ab170d7/chardev.c#L331-L375)[chardev.c 687-697](https://github.com/tenstorrent/tt-kmd/blob/0ab170d7/chardev.c#L687-L697)[ioctl.h 328-357](https://github.com/tenstorrent/tt-kmd/blob/0ab170d7/ioctl.h#L328-L357)

## Debugging Interfaces

The driver exposes debugging information through:

*   **DebugFS**: `/sys/kernel/debug/tenstorrent/N/mappings`[chardev.c 108-109](https://github.com/tenstorrent/tt-kmd/blob/0ab170d7/chardev.c#L108-L109)
*   **ProcFS**: `/proc/driver/tenstorrent/N/pids`[chardev.c 110-112](https://github.com/tenstorrent/tt-kmd/blob/0ab170d7/chardev.c#L110-L112)

**Sources:**[chardev.c 108-112](https://github.com/tenstorrent/tt-kmd/blob/0ab170d7/chardev.c#L108-L112)[chardev.c 122-123](https://github.com/tenstorrent/tt-kmd/blob/0ab170d7/chardev.c#L122-L123)

Dismiss
Refresh this wiki

Enter email to refresh
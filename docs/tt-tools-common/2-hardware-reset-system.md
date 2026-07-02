---
project: tt-tools-common
github: https://github.com/tenstorrent/tt-tools-common
deepwiki: https://deepwiki.com/tenstorrent/tt-tools-common/2-hardware-reset-system
---

# Hardware Reset System

Relevant source files
*   [CHANGELOG.md](https://github.com/tenstorrent/tt-tools-common/blob/892885f4/CHANGELOG.md?plain=1)
*   [tt_tools_common/reset_common/bh_reset.py](https://github.com/tenstorrent/tt-tools-common/blob/892885f4/tt_tools_common/reset_common/bh_reset.py)
*   [tt_tools_common/reset_common/chip_reset.py](https://github.com/tenstorrent/tt-tools-common/blob/892885f4/tt_tools_common/reset_common/chip_reset.py)
*   [tt_tools_common/reset_common/wh_reset.py](https://github.com/tenstorrent/tt-tools-common/blob/892885f4/tt_tools_common/reset_common/wh_reset.py)

## Purpose and Scope

The Hardware Reset System is the core functionality of tt-tools-common, providing chip-level reset capabilities for Tenstorrent hardware. This system performs PCIe link resets and ASIC resets on Blackhole (BH), Wormhole (WH), and Grayskull (GS) chips, enabling recovery from hung states and facilitating firmware upgrades.

This document covers the overall architecture, implementation hierarchy, driver requirements, and reset workflows. For detailed implementation specifics of each chip type, see:

*   Blackhole reset details: [Blackhole Reset Implementation](https://deepwiki.com/tenstorrent/tt-tools-common/2.3-blackhole-reset-implementation)
*   Wormhole reset details: [Wormhole Reset Implementation](https://deepwiki.com/tenstorrent/tt-tools-common/2.4-wormhole-reset-implementation)
*   Grayskull reset details: [Grayskull Tensix Reset](https://deepwiki.com/tenstorrent/tt-tools-common/2.5-grayskull-tensix-reset)
*   Reset configuration and logging: [Reset Configuration and Logging](https://deepwiki.com/tenstorrent/tt-tools-common/2.6-reset-configuration-and-logging)
*   Virtualization support: [Platform Support and Virtualization](https://deepwiki.com/tenstorrent/tt-tools-common/2.7-platform-support-and-virtualization)

**Sources:**[CHANGELOG.md 1-143](https://github.com/tenstorrent/tt-tools-common/blob/892885f4/CHANGELOG.md?plain=1#L1-L143)[tt_tools_common/reset_common/chip_reset.py 1-289](https://github.com/tenstorrent/tt-tools-common/blob/892885f4/tt_tools_common/reset_common/chip_reset.py#L1-L289)[tt_tools_common/reset_common/bh_reset.py 1-224](https://github.com/tenstorrent/tt-tools-common/blob/892885f4/tt_tools_common/reset_common/bh_reset.py#L1-L224)[tt_tools_common/reset_common/wh_reset.py 1-181](https://github.com/tenstorrent/tt-tools-common/blob/892885f4/tt_tools_common/reset_common/wh_reset.py#L1-L181)

* * *

## Reset System Architecture

The reset system implements a **delegation pattern** that enables gradual migration from legacy chip-specific implementations to a modern unified implementation. The architecture consists of three chip-specific reset classes (`BHChipReset`, `WHChipReset`, `GSTensixReset`) and one unified modern implementation (`ChipReset`).

### High-Level Reset Architecture

The delegation logic in `BHChipReset` and `WHChipReset` conditionally routes to `ChipReset` based on driver version or virtualization environment, maintaining backward compatibility while enabling adoption of the modern implementation.

**Sources:**[tt_tools_common/reset_common/bh_reset.py 92-99](https://github.com/tenstorrent/tt-tools-common/blob/892885f4/tt_tools_common/reset_common/bh_reset.py#L92-L99)[tt_tools_common/reset_common/wh_reset.py 89-90](https://github.com/tenstorrent/tt-tools-common/blob/892885f4/tt_tools_common/reset_common/wh_reset.py#L89-L90)[tt_tools_common/reset_common/chip_reset.py 114-134](https://github.com/tenstorrent/tt-tools-common/blob/892885f4/tt_tools_common/reset_common/chip_reset.py#L114-L134)

* * *

## Implementation Class Hierarchy

### Reset Class Structure

**Sources:**[tt_tools_common/reset_common/chip_reset.py 25-33](https://github.com/tenstorrent/tt-tools-common/blob/892885f4/tt_tools_common/reset_common/chip_reset.py#L25-L33)[tt_tools_common/reset_common/chip_reset.py 53-289](https://github.com/tenstorrent/tt-tools-common/blob/892885f4/tt_tools_common/reset_common/chip_reset.py#L53-L289)[tt_tools_common/reset_common/bh_reset.py 27-224](https://github.com/tenstorrent/tt-tools-common/blob/892885f4/tt_tools_common/reset_common/bh_reset.py#L27-L224)[tt_tools_common/reset_common/wh_reset.py 28-181](https://github.com/tenstorrent/tt-tools-common/blob/892885f4/tt_tools_common/reset_common/wh_reset.py#L28-L181)

### Key Class Responsibilities

| Class | File | Primary Responsibility | Hardware Target |
| --- | --- | --- | --- |
| `ChipReset` | `chip_reset.py` | Modern unified reset using driver v2.4.1+ IOCTL interface | Blackhole, Wormhole |
| `BHChipReset` | `bh_reset.py` | Blackhole reset with legacy and modern paths | Blackhole |
| `WHChipReset` | `wh_reset.py` | Wormhole reset with legacy and modern paths | Wormhole |
| `GSTensixReset` | (not shown) | Tensix core reset via NOC operations | Grayskull |
| `IoctlResetFlags` | `chip_reset.py` | Enumeration of IOCTL reset operation types | All |

**Sources:**[tt_tools_common/reset_common/chip_reset.py 53-54](https://github.com/tenstorrent/tt-tools-common/blob/892885f4/tt_tools_common/reset_common/chip_reset.py#L53-L54)[tt_tools_common/reset_common/bh_reset.py 27-28](https://github.com/tenstorrent/tt-tools-common/blob/892885f4/tt_tools_common/reset_common/bh_reset.py#L27-L28)[tt_tools_common/reset_common/wh_reset.py 28-29](https://github.com/tenstorrent/tt-tools-common/blob/892885f4/tt_tools_common/reset_common/wh_reset.py#L28-L29)

* * *

## Driver and Platform Requirements

### Driver Version Requirements

The reset system has specific minimum driver version requirements that determine which reset implementation path is used:

| Operation | Minimum Driver Version | Validation Function |
| --- | --- | --- |
| Basic reset (legacy WH/BH) | 1.26.0 | `check_driver_version()` |
| Modern unified reset (ChipReset) | 2.4.1 | `is_driver_version_at_least()` |
| Xen HVM reset | 2.4.1 | `check_xen_hvm()` + version check |

**Sources:**[tt_tools_common/reset_common/chip_reset.py 137](https://github.com/tenstorrent/tt-tools-common/blob/892885f4/tt_tools_common/reset_common/chip_reset.py#L137-L137)[tt_tools_common/reset_common/bh_reset.py 92](https://github.com/tenstorrent/tt-tools-common/blob/892885f4/tt_tools_common/reset_common/bh_reset.py#L92-L92)[tt_tools_common/reset_common/wh_reset.py 89](https://github.com/tenstorrent/tt-tools-common/blob/892885f4/tt_tools_common/reset_common/wh_reset.py#L89-L89)[CHANGELOG.md 103](https://github.com/tenstorrent/tt-tools-common/blob/892885f4/CHANGELOG.md?plain=1#L103-L103)

### Platform Restrictions

**ARM Platform Restriction:** The reset system does not support ARM/AArch64 platforms due to how these systems handle PCIe device rescans. When an ARM platform is detected, the reset operations exit with an error message directing users to reboot the system instead.

**Sources:**[tt_tools_common/reset_common/chip_reset.py 139-148](https://github.com/tenstorrent/tt-tools-common/blob/892885f4/tt_tools_common/reset_common/chip_reset.py#L139-L148)[tt_tools_common/reset_common/bh_reset.py 109-118](https://github.com/tenstorrent/tt-tools-common/blob/892885f4/tt_tools_common/reset_common/bh_reset.py#L109-L118)[tt_tools_common/reset_common/wh_reset.py 103-112](https://github.com/tenstorrent/tt-tools-common/blob/892885f4/tt_tools_common/reset_common/wh_reset.py#L103-L112)

* * *

## Reset Workflow and Sequence

### Modern Reset Workflow (ChipReset)

The `ChipReset.full_lds_reset()` method implements a multi-stage reset sequence:

**Sources:**[tt_tools_common/reset_common/chip_reset.py 114-288](https://github.com/tenstorrent/tt-tools-common/blob/892885f4/tt_tools_common/reset_common/chip_reset.py#L114-L288)[tt_tools_common/reset_common/chip_reset.py 167-256](https://github.com/tenstorrent/tt-tools-common/blob/892885f4/tt_tools_common/reset_common/chip_reset.py#L167-L256)

### Reset Stages Detail

**Stage 1: Pre-flight Validation**

*   Driver version check via `check_driver_version()`[chip_reset.py 137](https://github.com/tenstorrent/tt-tools-common/blob/892885f4/chip_reset.py#L137-L137)
*   Platform architecture check via `get_host_info()`[chip_reset.py 141](https://github.com/tenstorrent/tt-tools-common/blob/892885f4/chip_reset.py#L141-L141)
*   PCI interface deduplication [chip_reset.py 151](https://github.com/tenstorrent/tt-tools-common/blob/892885f4/chip_reset.py#L151-L151)
*   Collection of BDF (Bus:Device.Function) addresses [chip_reset.py 159-162](https://github.com/tenstorrent/tt-tools-common/blob/892885f4/chip_reset.py#L159-L162)

**Stage 2: Xen HVM Coordination (if applicable)**

*   Detect Xen HVM environment using `check_xen_hvm()`[chip_reset.py 169](https://github.com/tenstorrent/tt-tools-common/blob/892885f4/chip_reset.py#L169-L169)
*   Write "1" to xenstore files to notify host [chip_reset.py 176-193](https://github.com/tenstorrent/tt-tools-common/blob/892885f4/chip_reset.py#L176-L193)
*   Format: `pci_hard_reset/{bus}_{device}-{function}`[chip_reset.py 179](https://github.com/tenstorrent/tt-tools-common/blob/892885f4/chip_reset.py#L179-L179)

**Stage 3: Secondary Bus Reset (optional)**

*   Issue IOCTL with `RESET_PCIE_LINK` flag [chip_reset.py 198](https://github.com/tenstorrent/tt-tools-common/blob/892885f4/chip_reset.py#L198-L198)
*   Performed before ASIC reset if `secondary_bus_reset=True`[chip_reset.py 196](https://github.com/tenstorrent/tt-tools-common/blob/892885f4/chip_reset.py#L196-L196)

**Stage 4: ASIC Reset**

*   Issue IOCTL with `ASIC_RESET` or `ASIC_DMC_RESET` flag [chip_reset.py 206-212](https://github.com/tenstorrent/tt-tools-common/blob/892885f4/chip_reset.py#L206-L212)
*   `ASIC_DMC_RESET` includes M3/DMC reset when `reset_m3=True`[chip_reset.py 208](https://github.com/tenstorrent/tt-tools-common/blob/892885f4/chip_reset.py#L208-L208)

**Stage 5: Xen Hot-plug Wait (if applicable)**

*   Poll xenstore files for removal by host [chip_reset.py 228-256](https://github.com/tenstorrent/tt-tools-common/blob/892885f4/chip_reset.py#L228-L256)
*   Timeout: 300 seconds (5 minutes) [chip_reset.py 216](https://github.com/tenstorrent/tt-tools-common/blob/892885f4/chip_reset.py#L216-L216)
*   Uses parallel threads for multiple devices [chip_reset.py 250-256](https://github.com/tenstorrent/tt-tools-common/blob/892885f4/chip_reset.py#L250-L256)

**Stage 6: Post-reset Recovery**

*   Wait for potential hot-plug removal [chip_reset.py 259-265](https://github.com/tenstorrent/tt-tools-common/blob/892885f4/chip_reset.py#L259-L265)
*   Wait time: `m3_delay` if M3 reset, else `max(2, 0.4 * num_devices)` seconds [chip_reset.py 259](https://github.com/tenstorrent/tt-tools-common/blob/892885f4/chip_reset.py#L259-L259)
*   Poll for device reappearance on PCI bus [chip_reset.py 87-111](https://github.com/tenstorrent/tt-tools-common/blob/892885f4/chip_reset.py#L87-L111)
*   Issue `POST_RESET` IOCTL for driver cleanup [chip_reset.py 270](https://github.com/tenstorrent/tt-tools-common/blob/892885f4/chip_reset.py#L270-L270)

**Sources:**[tt_tools_common/reset_common/chip_reset.py 114-288](https://github.com/tenstorrent/tt-tools-common/blob/892885f4/tt_tools_common/reset_common/chip_reset.py#L114-L288)

* * *

## Communication Mechanisms

### IOCTL Interface

The reset system communicates with the kernel driver through IOCTL calls to device nodes at `/dev/tenstorrent/{interface_id}`.

**IOCTL Structure:**

```
Input struct:  "II" (output_size_bytes, flags)
Output struct: "II" (output_size_bytes, result)
IOCTL number:  0xFA06 (TENSTORRENT_IOCTL_MAGIC << 8 | 6)
```

**IOCTL Reset Flags:**

| Flag Value | Enum Name | Operation | Used In |
| --- | --- | --- | --- |
| 0 | `RESTORE_STATE` | Restore PCI config space (legacy) | Legacy WH/BH reset |
| 1 | `RESET_PCIE_LINK` | Secondary bus reset | Modern and legacy |
| 2 | `CONFIG_WRITE` | Config space write reset (legacy BH) | Legacy BH reset |
| 3 | `USER_RESET` | User-initiated reset | Modern reset |
| 4 | `ASIC_RESET` | ASIC-only reset | Modern reset |
| 5 | `ASIC_DMC_RESET` | ASIC + M3/DMC reset | Modern reset |
| 6 | `POST_RESET` | Post-reset cleanup | Modern reset |

**Sources:**[tt_tools_common/reset_common/chip_reset.py 25-32](https://github.com/tenstorrent/tt-tools-common/blob/892885f4/tt_tools_common/reset_common/chip_reset.py#L25-L32)[tt_tools_common/reset_common/chip_reset.py 56-58](https://github.com/tenstorrent/tt-tools-common/blob/892885f4/tt_tools_common/reset_common/chip_reset.py#L56-L58)[tt_tools_common/reset_common/chip_reset.py 60-85](https://github.com/tenstorrent/tt-tools-common/blob/892885f4/tt_tools_common/reset_common/chip_reset.py#L60-L85)

### IOCTL Call Implementation

The `reset_device_ioctl()` method:

1.   Opens device node with `O_RDWR | O_CLOEXEC` flags [chip_reset.py 62-64](https://github.com/tenstorrent/tt-tools-common/blob/892885f4/chip_reset.py#L62-L64)
2.   Packs input structure with output buffer size and flags [chip_reset.py 68-75](https://github.com/tenstorrent/tt-tools-common/blob/892885f4/chip_reset.py#L68-L75)
3.   Calls `fcntl.ioctl()` with buffer [chip_reset.py 76-78](https://github.com/tenstorrent/tt-tools-common/blob/892885f4/chip_reset.py#L76-L78)
4.   Unpacks result from output buffer [chip_reset.py 80-81](https://github.com/tenstorrent/tt-tools-common/blob/892885f4/chip_reset.py#L80-L81)
5.   Returns `True` if result is 0 (success) [chip_reset.py 83](https://github.com/tenstorrent/tt-tools-common/blob/892885f4/chip_reset.py#L83-L83)
6.   Always closes file descriptor in `finally` block [chip_reset.py 84-85](https://github.com/tenstorrent/tt-tools-common/blob/892885f4/chip_reset.py#L84-L85)

**Sources:**[tt_tools_common/reset_common/chip_reset.py 60-85](https://github.com/tenstorrent/tt-tools-common/blob/892885f4/tt_tools_common/reset_common/chip_reset.py#L60-L85)[tt_tools_common/reset_common/bh_reset.py 42-66](https://github.com/tenstorrent/tt-tools-common/blob/892885f4/tt_tools_common/reset_common/bh_reset.py#L42-L66)[tt_tools_common/reset_common/wh_reset.py 42-66](https://github.com/tenstorrent/tt-tools-common/blob/892885f4/tt_tools_common/reset_common/wh_reset.py#L42-L66)

* * *

## Legacy vs Modern Implementation Migration

### Delegation Pattern Logic

Both `BHChipReset` and `WHChipReset` implement conditional delegation to `ChipReset` based on runtime conditions:

**Blackhole Delegation Logic:**

```
if is_driver_version_at_least("2.4.1") or check_xen_hvm():
    return ChipReset().full_lds_reset(...)
else:
    # Execute legacy BH reset implementation
```

**Wormhole Delegation Logic:**

```
if is_driver_version_at_least("2.4.1") or check_xen_hvm():
    return ChipReset().full_lds_reset(...)
else:
    # Execute legacy WH reset implementation
```

**Sources:**[tt_tools_common/reset_common/bh_reset.py 92-99](https://github.com/tenstorrent/tt-tools-common/blob/892885f4/tt_tools_common/reset_common/bh_reset.py#L92-L99)[tt_tools_common/reset_common/wh_reset.py 89-90](https://github.com/tenstorrent/tt-tools-common/blob/892885f4/tt_tools_common/reset_common/wh_reset.py#L89-L90)

### Migration Status by Chip Type

**Migration Notes:**

*   **Blackhole**: Added in v1.4.6 [CHANGELOG.md 64](https://github.com/tenstorrent/tt-tools-common/blob/892885f4/CHANGELOG.md?plain=1#L64-L64) M3 reset option added in v1.4.7 [CHANGELOG.md 55](https://github.com/tenstorrent/tt-tools-common/blob/892885f4/CHANGELOG.md?plain=1#L55-L55)
*   **Wormhole**: Legacy implementation includes refclk validation [wh_reset.py 157-164](https://github.com/tenstorrent/tt-tools-common/blob/892885f4/wh_reset.py#L157-L164)
*   **Grayskull**: Uses separate Tensix-specific reset, no migration to ChipReset [CHANGELOG.md 111](https://github.com/tenstorrent/tt-tools-common/blob/892885f4/CHANGELOG.md?plain=1#L111-L111)

**Deprecation Warning:** Legacy implementations print a yellow warning message when used, advising upgrade to driver v2.4.1+.

**Sources:**[tt_tools_common/reset_common/bh_reset.py 101-107](https://github.com/tenstorrent/tt-tools-common/blob/892885f4/tt_tools_common/reset_common/bh_reset.py#L101-L107)[tt_tools_common/reset_common/wh_reset.py 92-98](https://github.com/tenstorrent/tt-tools-common/blob/892885f4/tt_tools_common/reset_common/wh_reset.py#L92-L98)[CHANGELOG.md 55-65](https://github.com/tenstorrent/tt-tools-common/blob/892885f4/CHANGELOG.md?plain=1#L55-L65)

### Key Differences: Legacy vs Modern

| Aspect | Legacy Implementation | Modern Implementation |
| --- | --- | --- |
| **Driver Requirement** | >= 1.26.0 | >= 2.4.1 |
| **IOCTL Flags** | `RESET_PCIE_LINK`, `RESTORE_STATE`, `CONFIG_WRITE` | `ASIC_RESET`, `ASIC_DMC_RESET`, `POST_RESET` |
| **Reset Validation** | Polling config space memory byte [bh_reset.py 173-179](https://github.com/tenstorrent/tt-tools-common/blob/892885f4/bh_reset.py#L173-L179) | IOCTL return values + device reappearance |
| **M3/DMC Reset** | ARC message 0x56 with arg0=3 [bh_reset.py 140](https://github.com/tenstorrent/tt-tools-common/blob/892885f4/bh_reset.py#L140-L140) | IOCTL with `ASIC_DMC_RESET` flag [chip_reset.py 208](https://github.com/tenstorrent/tt-tools-common/blob/892885f4/chip_reset.py#L208-L208) |
| **Xen HVM Support** | Not supported | Full xenstore coordination [chip_reset.py 167-256](https://github.com/tenstorrent/tt-tools-common/blob/892885f4/chip_reset.py#L167-L256) |
| **Device Re-enumeration** | Manual PCIe rescan | Handled by kernel driver |
| **Refclk Validation** | Manual check (WH only) [wh_reset.py 157-164](https://github.com/tenstorrent/tt-tools-common/blob/892885f4/wh_reset.py#L157-L164) | Not required (driver ensures reset) |

**Sources:**[tt_tools_common/reset_common/bh_reset.py 68-224](https://github.com/tenstorrent/tt-tools-common/blob/892885f4/tt_tools_common/reset_common/bh_reset.py#L68-L224)[tt_tools_common/reset_common/wh_reset.py 68-181](https://github.com/tenstorrent/tt-tools-common/blob/892885f4/tt_tools_common/reset_common/wh_reset.py#L68-L181)[tt_tools_common/reset_common/chip_reset.py 114-288](https://github.com/tenstorrent/tt-tools-common/blob/892885f4/tt_tools_common/reset_common/chip_reset.py#L114-L288)

* * *

## Error Handling and Exit Codes

The reset system uses explicit exit codes for fatal errors:

**Fatal Error Conditions:**

1.   **ARM Platform Detected**: `sys.exit(1)`[chip_reset.py 148](https://github.com/tenstorrent/tt-tools-common/blob/892885f4/chip_reset.py#L148-L148)[bh_reset.py 118](https://github.com/tenstorrent/tt-tools-common/blob/892885f4/bh_reset.py#L118-L118)[wh_reset.py 112](https://github.com/tenstorrent/tt-tools-common/blob/892885f4/wh_reset.py#L112-L112)
2.   **Device Timeout**: `sys.exit(1)`[chip_reset.py 109](https://github.com/tenstorrent/tt-tools-common/blob/892885f4/chip_reset.py#L109-L109)
3.   **Post-reset Failure**: `sys.exit(1)`[chip_reset.py 278](https://github.com/tenstorrent/tt-tools-common/blob/892885f4/chip_reset.py#L278-L278)
4.   **Config Space Reset Failed** (BH legacy): `sys.exit(failures)`[bh_reset.py 213](https://github.com/tenstorrent/tt-tools-common/blob/892885f4/bh_reset.py#L213-L213)
5.   **Refclk Validation Failed** (WH legacy): `sys.exit(1)`[wh_reset.py 172](https://github.com/tenstorrent/tt-tools-common/blob/892885f4/wh_reset.py#L172-L172)
6.   **Xen Timeout**: `sys.exit(1)`[chip_reset.py 248](https://github.com/tenstorrent/tt-tools-common/blob/892885f4/chip_reset.py#L248-L248)

**Warning Messages:**

*   Legacy implementation usage warning (yellow) [bh_reset.py 102-107](https://github.com/tenstorrent/tt-tools-common/blob/892885f4/bh_reset.py#L102-L107)[wh_reset.py 93-98](https://github.com/tenstorrent/tt-tools-common/blob/892885f4/wh_reset.py#L93-L98)
*   Secondary bus reset failure warning (yellow, non-fatal) [chip_reset.py 199-203](https://github.com/tenstorrent/tt-tools-common/blob/892885f4/chip_reset.py#L199-L203)

**Sources:**[tt_tools_common/reset_common/chip_reset.py 109](https://github.com/tenstorrent/tt-tools-common/blob/892885f4/tt_tools_common/reset_common/chip_reset.py#L109-L109)[tt_tools_common/reset_common/chip_reset.py 148](https://github.com/tenstorrent/tt-tools-common/blob/892885f4/tt_tools_common/reset_common/chip_reset.py#L148-L148)[tt_tools_common/reset_common/chip_reset.py 248](https://github.com/tenstorrent/tt-tools-common/blob/892885f4/tt_tools_common/reset_common/chip_reset.py#L248-L248)[tt_tools_common/reset_common/chip_reset.py 278](https://github.com/tenstorrent/tt-tools-common/blob/892885f4/tt_tools_common/reset_common/chip_reset.py#L278-L278)[tt_tools_common/reset_common/bh_reset.py 213](https://github.com/tenstorrent/tt-tools-common/blob/892885f4/tt_tools_common/reset_common/bh_reset.py#L213-L213)[tt_tools_common/reset_common/wh_reset.py 172](https://github.com/tenstorrent/tt-tools-common/blob/892885f4/tt_tools_common/reset_common/wh_reset.py#L172-L172)

Dismiss
Refresh this wiki

Enter email to refresh
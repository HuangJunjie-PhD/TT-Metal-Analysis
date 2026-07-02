---
project: tt-tools-common
github: https://github.com/tenstorrent/tt-tools-common
deepwiki: https://deepwiki.com/tenstorrent/tt-tools-common/3-system-utilities
---

# System Utilities

Relevant source files
*   [tt_tools_common/utils_common/system_utils.py](https://github.com/tenstorrent/tt-tools-common/blob/892885f4/tt_tools_common/utils_common/system_utils.py)
*   [tt_tools_common/utils_common/tools_utils.py](https://github.com/tenstorrent/tt-tools-common/blob/892885f4/tt_tools_common/utils_common/tools_utils.py)

The System Utilities subsystem provides core infrastructure for managing system-level operations across all tt-tools applications. This module handles driver version validation, host system information collection, hardware detection with real-time status updates, firmware version conversions, and reference clock monitoring. These utilities form the foundation for safe hardware interaction by ensuring compatibility requirements are met before executing operations.

For chip reset operations that utilize these utilities, see [Hardware Reset System](https://deepwiki.com/tenstorrent/tt-tools-common/2-hardware-reset-system). For UI components that display system information, see [User Interface Components](https://deepwiki.com/tenstorrent/tt-tools-common/4-user-interface-components).

## Module Architecture

The system utilities are organized into two primary modules that provide complementary functionality for system interaction and hardware management.

**Sources:**[tt_tools_common/utils_common/system_utils.py 1-296](https://github.com/tenstorrent/tt-tools-common/blob/892885f4/tt_tools_common/utils_common/system_utils.py#L1-L296)[tt_tools_common/utils_common/tools_utils.py 1-355](https://github.com/tenstorrent/tt-tools-common/blob/892885f4/tt_tools_common/utils_common/tools_utils.py#L1-L355)

## Core Functionality Groups

### Driver Version Management

Driver version management ensures that operations requiring specific kernel driver features validate compatibility before execution. The system reads version information from the kernel module sysfs interface and performs semantic version comparison.

| Function | Purpose | Return Type |
| --- | --- | --- |
| `get_driver_version()` | Reads driver version from `/sys/module/tenstorrent/version` | `str` or `None` |
| `is_driver_version_at_least()` | Compares semantic versions including pre-release tags | `bool` |
| `check_driver_version()` | Validates version requirement and exits on failure | `None` (exits on error) |

The default minimum version for reset operations is `MINIMUM_DRIVER_VERSION_LDS_RESET = "1.26.0"`[tt_tools_common/utils_common/system_utils.py 17](https://github.com/tenstorrent/tt-tools-common/blob/892885f4/tt_tools_common/utils_common/system_utils.py#L17-L17)

For detailed documentation, see [Driver Version Management](https://deepwiki.com/tenstorrent/tt-tools-common/3.1-driver-version-management).

**Sources:**[tt_tools_common/utils_common/system_utils.py 35-142](https://github.com/tenstorrent/tt-tools-common/blob/892885f4/tt_tools_common/utils_common/system_utils.py#L35-L142)

### Host Information Collection

Host information functions gather system details for compatibility validation and display in user interfaces. The `get_host_info()` function collects basic system information, while `get_host_compatibility_info()` adds validation logic to identify incompatible configurations.

For detailed documentation, see [Host Information and Compatibility](https://deepwiki.com/tenstorrent/tt-tools-common/3.2-host-information-and-compatibility).

**Sources:**[tt_tools_common/utils_common/system_utils.py 145-211](https://github.com/tenstorrent/tt-tools-common/blob/892885f4/tt_tools_common/utils_common/system_utils.py#L145-L211)

### Chip Detection and Communication

The `detect_chips_with_callback()` function provides interactive chip detection with real-time progress feedback. It wraps `pyluwen.detect_chips_fallible()` with a callback mechanism that reports detection status for local PCIe devices and remote Ethernet-connected chips.

For detailed documentation, see [Chip Detection and Communication](https://deepwiki.com/tenstorrent/tt-tools-common/3.3-chip-detection-and-communication).

**Sources:**[tt_tools_common/utils_common/tools_utils.py 255-354](https://github.com/tenstorrent/tt-tools-common/blob/892885f4/tt_tools_common/utils_common/tools_utils.py#L255-L354)

### Version Conversion Utilities

Multiple firmware types use different hexadecimal encoding schemes for version numbers. The utilities provide specialized conversion functions for each firmware type:

| Function | Format | Example |
| --- | --- | --- |
| `hex_to_semver()` | `0xAABBCC00` → `AA.BB.CC` | Standard firmware versions |
| `hex_to_semver_eth()` | `0xAAbCCC` → `AA.b.CCC` | Ethernet firmware (12-bit patch) |
| `hex_to_semver_m3_fw()` | `0xAABBCCDD` → `AA.BB.CC.DD` | M3 firmware (4-part version) |
| `hex_to_semver_zephyr_fw()` | `0xAABBCCDD` → `AA.BB.CC-rcDD` | Zephyr firmware (with RC suffix) |

For detailed documentation, see [Version Conversions and Data Loading](https://deepwiki.com/tenstorrent/tt-tools-common/3.4-version-conversions-and-data-loading).

**Sources:**[tt_tools_common/utils_common/tools_utils.py 136-189](https://github.com/tenstorrent/tt-tools-common/blob/892885f4/tt_tools_common/utils_common/tools_utils.py#L136-L189)

### Reference Clock Monitoring

Reference clock monitoring validates that the hardware clock counters are incrementing at the expected rate, independent of the ARC clock. This provides a hardware health check mechanism.

For detailed documentation, see [Reference Clock Monitoring](https://deepwiki.com/tenstorrent/tt-tools-common/3.5-reference-clock-monitoring).

**Sources:**[tt_tools_common/utils_common/tools_utils.py 202-253](https://github.com/tenstorrent/tt-tools-common/blob/892885f4/tt_tools_common/utils_common/tools_utils.py#L202-L253)

## Data Loading and Board Identification

### Firmware Definitions

The `init_fw_defines()` function loads chip-specific firmware message definitions from YAML files. These definitions map firmware message types and constants for chip communication.

**Sources:**[tt_tools_common/utils_common/tools_utils.py 17-24](https://github.com/tenstorrent/tt-tools-common/blob/892885f4/tt_tools_common/utils_common/tools_utils.py#L17-L24)

### Board Type Detection

The `get_board_type()` function extracts board type information from the Unique Part Identifier (UPI) field encoded in the board serial number. The serial number format follows the pattern `AA-BBBBB-C-D-EE-FF-XXX` where `BBBBB` represents the UPI.

**Sources:**[tt_tools_common/utils_common/tools_utils.py 46-91](https://github.com/tenstorrent/tt-tools-common/blob/892885f4/tt_tools_common/utils_common/tools_utils.py#L46-L91)

## Software Version Reporting

The system supports optional integration with a remote version tracking service to report software stack versions associated with specific board serial numbers. This feature respects user privacy preferences through configuration flags.

| Configuration Flag | Effect |
| --- | --- |
| `disable_sw_version_report` | Returns default "N/A" values for all versions |
| `disable_serial_report` | Queries version service without board serial number |

The configuration is stored in `~/.config/tenstorrent/reset_config.json`[tt_tools_common/utils_common/system_utils.py 217-228](https://github.com/tenstorrent/tt-tools-common/blob/892885f4/tt_tools_common/utils_common/system_utils.py#L217-L228)

**Sources:**[tt_tools_common/utils_common/system_utils.py 213-296](https://github.com/tenstorrent/tt-tools-common/blob/892885f4/tt_tools_common/utils_common/system_utils.py#L213-L296)

## Integration with Reset System

System utilities are extensively used by the reset system to validate preconditions before executing hardware operations. The driver version check is mandatory for reset operations, while host compatibility information provides user-facing diagnostics.

For complete reset system documentation, see [Hardware Reset System](https://deepwiki.com/tenstorrent/tt-tools-common/2-hardware-reset-system) and [Platform Support and Virtualization](https://deepwiki.com/tenstorrent/tt-tools-common/2.7-platform-support-and-virtualization).

**Sources:**[tt_tools_common/utils_common/system_utils.py 99-142](https://github.com/tenstorrent/tt-tools-common/blob/892885f4/tt_tools_common/utils_common/system_utils.py#L99-L142)[tt_tools_common/utils_common/tools_utils.py 255-354](https://github.com/tenstorrent/tt-tools-common/blob/892885f4/tt_tools_common/utils_common/tools_utils.py#L255-L354)

## Error Handling Patterns

### Version Parsing Errors

The `_parse_version_string()` function handles semantic version strings with pre-release identifiers and build metadata according to SemVer conventions. It strips suffixes before parsing numeric components [tt_tools_common/utils_common/system_utils.py 47-80](https://github.com/tenstorrent/tt-tools-common/blob/892885f4/tt_tools_common/utils_common/system_utils.py#L47-L80)

Examples of supported formats:

*   `"1.34.0"` → `(1, 34, 0)`
*   `"1.34.1-alpha"` → `(1, 34, 1)`
*   `"1.2.3+build456"` → `(1, 2, 3)`
*   `"1.4.0-rc1+build42"` → `(1, 4, 0)`

### Communication Validation

The chip detection function validates communication status and raises exceptions for devices without working communication channels. This prevents subsequent operations from failing due to hardware issues:

**Sources:**[tt_tools_common/utils_common/tools_utils.py 346-349](https://github.com/tenstorrent/tt-tools-common/blob/892885f4/tt_tools_common/utils_common/tools_utils.py#L346-L349)

## Usage in Consumer Tools

System utilities are consumed by multiple Tenstorrent tools including tt-smi, tt-flash, and others. Common usage patterns include:

1.   **Driver validation at startup**: Call `check_driver_version()` before any hardware operation
2.   **System info display**: Use `get_host_info()` or `get_host_compatibility_info()` for UI rendering
3.   **Hardware enumeration**: Call `detect_chips_with_callback()` with appropriate progress display
4.   **Firmware version display**: Convert hex versions using appropriate `hex_to_semver_*()` variant

For UI integration examples, see [Widget Library Reference](https://deepwiki.com/tenstorrent/tt-tools-common/4.1-widget-library-reference) and [Building Custom TUI Applications](https://deepwiki.com/tenstorrent/tt-tools-common/4.4-building-custom-tui-applications).

**Sources:**[tt_tools_common/utils_common/system_utils.py 1-296](https://github.com/tenstorrent/tt-tools-common/blob/892885f4/tt_tools_common/utils_common/system_utils.py#L1-L296)[tt_tools_common/utils_common/tools_utils.py 1-355](https://github.com/tenstorrent/tt-tools-common/blob/892885f4/tt_tools_common/utils_common/tools_utils.py#L1-L355)

Dismiss
Refresh this wiki

Enter email to refresh
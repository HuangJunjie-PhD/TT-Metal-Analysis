---
project: tt-tools-common
github: https://github.com/tenstorrent/tt-tools-common
deepwiki: https://deepwiki.com/tenstorrent/tt-tools-common/7-api-reference
---

# API Reference

Relevant source files
*   [tt_tools_common/reset_common/bh_reset.py](https://github.com/tenstorrent/tt-tools-common/blob/892885f4/tt_tools_common/reset_common/bh_reset.py)
*   [tt_tools_common/reset_common/chip_reset.py](https://github.com/tenstorrent/tt-tools-common/blob/892885f4/tt_tools_common/reset_common/chip_reset.py)
*   [tt_tools_common/reset_common/host_reset_log.py](https://github.com/tenstorrent/tt-tools-common/blob/892885f4/tt_tools_common/reset_common/host_reset_log.py)
*   [tt_tools_common/reset_common/reset_utils.py](https://github.com/tenstorrent/tt-tools-common/blob/892885f4/tt_tools_common/reset_common/reset_utils.py)
*   [tt_tools_common/ui_common/common_style.css](https://github.com/tenstorrent/tt-tools-common/blob/892885f4/tt_tools_common/ui_common/common_style.css)
*   [tt_tools_common/ui_common/widgets.py](https://github.com/tenstorrent/tt-tools-common/blob/892885f4/tt_tools_common/ui_common/widgets.py)
*   [tt_tools_common/utils_common/system_utils.py](https://github.com/tenstorrent/tt-tools-common/blob/892885f4/tt_tools_common/utils_common/system_utils.py)
*   [tt_tools_common/utils_common/tools_utils.py](https://github.com/tenstorrent/tt-tools-common/blob/892885f4/tt_tools_common/utils_common/tools_utils.py)

This page provides a comprehensive overview of the public API exposed by `tt-tools-common`. The library organizes its functionality into four major subsystems: **Reset Operations**, **System Utilities**, **UI Components**, and **Data Models**. Each subsystem is detailed in its respective sub-page (see sections [7.1](https://deepwiki.com/tenstorrent/tt-tools-common/7.1-reset-api-reference) through [7.4](https://deepwiki.com/tenstorrent/tt-tools-common/7.4-data-models-and-configuration)).

This page focuses on module organization, import paths, and high-level API categories. For detailed documentation of individual classes, functions, and parameters, refer to the specialized sub-pages listed in the sections below.

* * *

## Module Organization

The `tt-tools-common` package is organized into three primary top-level modules, each containing specialized submodules:

| Module | Submodules | Purpose |
| --- | --- | --- |
| `reset_common` | `chip_reset`, `bh_reset`, `wh_reset`, `gs_reset`, `reset_utils`, `host_reset_log` | Chip reset operations and logging |
| `utils_common` | `system_utils`, `tools_utils` | System utilities and hardware detection |
| `ui_common` | `widgets`, `themes`, `common_style.css` | Textual UI components and styling |

**Diagram: Package Structure and Import Paths**

Sources: [tt_tools_common/reset_common/chip_reset.py 1-289](https://github.com/tenstorrent/tt-tools-common/blob/892885f4/tt_tools_common/reset_common/chip_reset.py#L1-L289)[tt_tools_common/reset_common/bh_reset.py 1-224](https://github.com/tenstorrent/tt-tools-common/blob/892885f4/tt_tools_common/reset_common/bh_reset.py#L1-L224)[tt_tools_common/utils_common/system_utils.py 1-296](https://github.com/tenstorrent/tt-tools-common/blob/892885f4/tt_tools_common/utils_common/system_utils.py#L1-L296)[tt_tools_common/utils_common/tools_utils.py 1-355](https://github.com/tenstorrent/tt-tools-common/blob/892885f4/tt_tools_common/utils_common/tools_utils.py#L1-L355)[tt_tools_common/ui_common/widgets.py 1-365](https://github.com/tenstorrent/tt-tools-common/blob/892885f4/tt_tools_common/ui_common/widgets.py#L1-L365)

* * *

## Import Patterns

All public APIs are designed to be imported directly from their containing modules. The recommended import patterns are:

### Reset Operations

### System Utilities

### UI Components

### Data Models

Sources: [tt_tools_common/reset_common/chip_reset.py 1-24](https://github.com/tenstorrent/tt-tools-common/blob/892885f4/tt_tools_common/reset_common/chip_reset.py#L1-L24)[tt_tools_common/utils_common/system_utils.py 1-16](https://github.com/tenstorrent/tt-tools-common/blob/892885f4/tt_tools_common/utils_common/system_utils.py#L1-L16)[tt_tools_common/ui_common/widgets.py 1-19](https://github.com/tenstorrent/tt-tools-common/blob/892885f4/tt_tools_common/ui_common/widgets.py#L1-L19)

* * *

## API Categories Overview

**Diagram: API Category Relationships and Common Usage Flows**

Sources: [tt_tools_common/reset_common/reset_utils.py 1-135](https://github.com/tenstorrent/tt-tools-common/blob/892885f4/tt_tools_common/reset_common/reset_utils.py#L1-L135)[tt_tools_common/reset_common/chip_reset.py 114-288](https://github.com/tenstorrent/tt-tools-common/blob/892885f4/tt_tools_common/reset_common/chip_reset.py#L114-L288)[tt_tools_common/utils_common/system_utils.py 35-211](https://github.com/tenstorrent/tt-tools-common/blob/892885f4/tt_tools_common/utils_common/system_utils.py#L35-L211)[tt_tools_common/utils_common/tools_utils.py 255-354](https://github.com/tenstorrent/tt-tools-common/blob/892885f4/tt_tools_common/utils_common/tools_utils.py#L255-L354)

### 1. Reset API (Detailed in [7.1](https://deepwiki.com/tenstorrent/tt-tools-common/7.1-reset-api-reference))

The reset subsystem provides chip-level reset functionality for Blackhole, Wormhole, and Grayskull architectures. Key entry points include:

| Function/Class | Module | Purpose |
| --- | --- | --- |
| `ChipReset.full_lds_reset()` | `chip_reset` | Modern unified reset for BH/WH (driver >= 2.4.1) |
| `BHChipReset.full_lds_reset()` | `bh_reset` | Blackhole-specific reset with legacy fallback |
| `WHChipReset.full_lds_reset()` | `wh_reset` | Wormhole-specific reset with legacy fallback |
| `GSTensixReset.tensix_reset()` | `gs_reset` | Grayskull Tensix core reset |
| `parse_reset_input()` | `reset_utils` | Parse reset arguments (ALL/ID_LIST/CONFIG_JSON) |
| `generate_reset_logs()` | `reset_utils` | Generate HostResetLog JSON configuration |

**Common Pattern:**

Sources: [tt_tools_common/reset_common/reset_utils.py 33-88](https://github.com/tenstorrent/tt-tools-common/blob/892885f4/tt_tools_common/reset_common/reset_utils.py#L33-L88)[tt_tools_common/reset_common/chip_reset.py 114-288](https://github.com/tenstorrent/tt-tools-common/blob/892885f4/tt_tools_common/reset_common/chip_reset.py#L114-L288)[tt_tools_common/reset_common/bh_reset.py 68-223](https://github.com/tenstorrent/tt-tools-common/blob/892885f4/tt_tools_common/reset_common/bh_reset.py#L68-L223)

### 2. System Utilities API (Detailed in [7.2](https://deepwiki.com/tenstorrent/tt-tools-common/7.2-system-utilities-api-reference))

Provides system-level information gathering and validation. Split into two submodules:

#### system_utils Module

| Function | Returns | Purpose |
| --- | --- | --- |
| `get_driver_version()` | `str | None` | Read `/sys/module/tenstorrent/version` |
| `is_driver_version_at_least()` | `bool` | Compare semantic versions with pre-release support |
| `check_driver_version()` | `None` | Validate driver version or exit with error |
| `get_host_info()` | `dict` | Collect OS, distro, kernel, memory, platform info |
| `get_host_compatibility_info()` | `Dict[str, str | Tuple]` | Host info with compatibility warnings |

#### tools_utils Module

| Function | Returns | Purpose |
| --- | --- | --- |
| `detect_chips_with_callback()` | `List[PciChip]` | Detect chips with callback-based progress |
| `hex_to_semver()` | `str` | Convert 0x0A0F0100 → "10.15.1" |
| `hex_to_semver_eth()` | `str` | Ethernet FW version (0x061000 → "6.1.0") |
| `hex_to_semver_m3_fw()` | `str` | M3 FW version with 4-part format |
| `hex_to_semver_zephyr_fw()` | `str` | Zephyr FW with rc suffix support |
| `get_board_type()` | `str` | Extract board type from UPI in serial number |
| `refclk_counter_rate()` | `float` | Measure REFCLK_COUNTER rate in MHz |

**Common Pattern:**

Sources: [tt_tools_common/utils_common/system_utils.py 35-211](https://github.com/tenstorrent/tt-tools-common/blob/892885f4/tt_tools_common/utils_common/system_utils.py#L35-L211)[tt_tools_common/utils_common/tools_utils.py 136-253](https://github.com/tenstorrent/tt-tools-common/blob/892885f4/tt_tools_common/utils_common/tools_utils.py#L136-L253)[tt_tools_common/utils_common/tools_utils.py 255-354](https://github.com/tenstorrent/tt-tools-common/blob/892885f4/tt_tools_common/utils_common/tools_utils.py#L255-L354)

### 3. UI Components API (Detailed in [7.3](https://deepwiki.com/tenstorrent/tt-tools-common/7.3-ui-components-api-reference))

Textual-based TUI widgets with consistent styling. All widgets inherit from `textual.widget.Widget` or `textual.containers.Container`:

| Widget Class | Type | Purpose |
| --- | --- | --- |
| `TTHeader` | Widget | App header with name, version, timestamp |
| `TTFooter` | Widget | Footer with customizable content |
| `TTDataTable` | ScrollableContainer | Wrapped DataTable with styled headers |
| `TTMenu` | Container | Key-value display with automatic width justification |
| `TTHostCompatibilityMenu` | Container | Host info with compatibility warnings (tuple values) |
| `TTCompatibilityMenu` | Container | Generic compatibility checklist |
| `TTConfirmBox` | ModalScreen | Yes/No confirmation dialog |
| `TTHelperMenuBox` | ModalScreen | Markdown help display |
| `TTSettingsMenu` | ModalScreen | Settings with Switch controls |

**Common Pattern:**

Sources: [tt_tools_common/ui_common/widgets.py 22-365](https://github.com/tenstorrent/tt-tools-common/blob/892885f4/tt_tools_common/ui_common/widgets.py#L22-L365)[tt_tools_common/ui_common/common_style.css 1-226](https://github.com/tenstorrent/tt-tools-common/blob/892885f4/tt_tools_common/ui_common/common_style.css#L1-L226)

### 4. Data Models and Configuration (Detailed in [7.4](https://deepwiki.com/tenstorrent/tt-tools-common/7.4-data-models-and-configuration))

Pydantic-based models for structured data with Elasticsearch compatibility:

| Model Class | Base | Purpose |
| --- | --- | --- |
| `ElasticModel` | `pydantic.BaseModel` | Base class with `get_mapping()` for ES schema |
| `HostResetLog` | `ElasticModel` | Complete reset log with device lists and timestamps |
| `PciResetDeviceInfo` | `ElasticModel` | PCI interface list for reset operations |
| `MoboReset` | `ElasticModel` | Motherboard reset configuration (Credo, ports) |

**Key Features:**

*   Automatic Elasticsearch mapping generation via `get_mapping()`
*   JSON serialization with `save_as_json()` method
*   Pydantic v1/v2 compatibility layer
*   `@optional` decorator for flexible schema evolution

**Common Pattern:**

Sources: [tt_tools_common/reset_common/host_reset_log.py 127-185](https://github.com/tenstorrent/tt-tools-common/blob/892885f4/tt_tools_common/reset_common/host_reset_log.py#L127-L185)[tt_tools_common/reset_common/reset_utils.py 89-134](https://github.com/tenstorrent/tt-tools-common/blob/892885f4/tt_tools_common/reset_common/reset_utils.py#L89-L134)

* * *

## Version Compatibility

The API maintains backward compatibility with the following version constraints:

| Component | Minimum Version | Notes |
| --- | --- | --- |
| Python | 3.10 | Required for type hints and pattern matching |
| tt-kmd driver | 1.26.0 | Minimum for basic operations |
| tt-kmd driver | 2.4.1 | Required for `ChipReset` unified reset |
| pyluwen | ~0.8.0 | Hardware abstraction layer |
| textual | >=0.59.0 | TUI framework for widgets |
| pydantic | >=1.2 | Data validation (v1 or v2 compatible) |

**Driver Version Checking:** The `check_driver_version()` function automatically validates driver compatibility and exits with a descriptive error if requirements are not met. All reset operations internally call this function with appropriate minimum versions.

**Platform Restrictions:**

*   ARM/aarch64 platforms do not support device reset operations ([tt_tools_common/reset_common/chip_reset.py 139-148](https://github.com/tenstorrent/tt-tools-common/blob/892885f4/tt_tools_common/reset_common/chip_reset.py#L139-L148))
*   Xen HVM guests require special xenstore coordination ([tt_tools_common/reset_common/chip_reset.py 167-256](https://github.com/tenstorrent/tt-tools-common/blob/892885f4/tt_tools_common/reset_common/chip_reset.py#L167-L256))

Sources: [tt_tools_common/utils_common/system_utils.py 17](https://github.com/tenstorrent/tt-tools-common/blob/892885f4/tt_tools_common/utils_common/system_utils.py#L17-L17)[tt_tools_common/utils_common/system_utils.py 82-142](https://github.com/tenstorrent/tt-tools-common/blob/892885f4/tt_tools_common/utils_common/system_utils.py#L82-L142)[tt_tools_common/reset_common/chip_reset.py 136-138](https://github.com/tenstorrent/tt-tools-common/blob/892885f4/tt_tools_common/reset_common/chip_reset.py#L136-L138)

* * *

## Type Annotations and Contracts

All public APIs use Python type hints for parameter and return types. Key type patterns:

### Return Value Conventions

| Return Type | Meaning | Example Functions |
| --- | --- | --- |
| `List[PciChip]` | Detected or reset chip objects | `detect_chips_with_callback()`, `full_lds_reset()` |
| `str | None` | Optional string (None = not found) | `get_driver_version()` |
| `bool` | Success/failure or validation result | `is_driver_version_at_least()`, `reset_device_ioctl()` |
| `Dict[str, str | Tuple]` | Compatibility info (Tuple = warning) | `get_host_compatibility_info()` |
| `ResetInput` | Dataclass with type and value fields | `parse_reset_input()` |

### Common Parameter Types

| Parameter Type | Usage |
| --- | --- |
| `List[int]` | PCI interface indices |
| `bool` | Flags (e.g., `reset_m3`, `silent`, `local_only`) |
| `str` | Paths, hostnames, version strings |
| `Callable` | Callback functions for progress updates |
| `OrderedDict[str, str]` | Widget data for TTMenu |

Sources: [tt_tools_common/reset_common/chip_reset.py 114-133](https://github.com/tenstorrent/tt-tools-common/blob/892885f4/tt_tools_common/reset_common/chip_reset.py#L114-L133)[tt_tools_common/utils_common/system_utils.py 35-45](https://github.com/tenstorrent/tt-tools-common/blob/892885f4/tt_tools_common/utils_common/system_utils.py#L35-L45)[tt_tools_common/utils_common/tools_utils.py 255-259](https://github.com/tenstorrent/tt-tools-common/blob/892885f4/tt_tools_common/utils_common/tools_utils.py#L255-L259)

* * *

## Error Handling Patterns

### Validation and Early Exit

Functions that validate system state (e.g., `check_driver_version()`, platform checks) use `sys.exit()` with non-zero codes when validation fails. This is intentional for CLI tools where continuing would cause undefined behavior.

### Exception Propagation

Hardware communication functions (e.g., `detect_chips_with_callback()`) raise exceptions on communication failures:

### Silent Failure Options

Many functions accept a `silent: bool` parameter to suppress informational prints while preserving error reporting.

Sources: [tt_tools_common/utils_common/system_utils.py 99-142](https://github.com/tenstorrent/tt-tools-common/blob/892885f4/tt_tools_common/utils_common/system_utils.py#L99-L142)[tt_tools_common/reset_common/chip_reset.py 139-148](https://github.com/tenstorrent/tt-tools-common/blob/892885f4/tt_tools_common/reset_common/chip_reset.py#L139-L148)[tt_tools_common/utils_common/tools_utils.py 346-349](https://github.com/tenstorrent/tt-tools-common/blob/892885f4/tt_tools_common/utils_common/tools_utils.py#L346-L349)

* * *

## Constants and Configuration

### Driver Version Requirements

*   `MINIMUM_DRIVER_VERSION_LDS_RESET = "1.26.0"` ([tt_tools_common/utils_common/system_utils.py 17](https://github.com/tenstorrent/tt-tools-common/blob/892885f4/tt_tools_common/utils_common/system_utils.py#L17-L17))

### File Paths

*   `LOG_FOLDER = "~/.config/tenstorrent"` - Default configuration directory ([tt_tools_common/utils_common/system_utils.py 18](https://github.com/tenstorrent/tt-tools-common/blob/892885f4/tt_tools_common/utils_common/system_utils.py#L18-L18))

### IOCTL Magic Numbers

*   `TENSTORRENT_IOCTL_MAGIC = 0xFA` ([tt_tools_common/reset_common/chip_reset.py 57](https://github.com/tenstorrent/tt-tools-common/blob/892885f4/tt_tools_common/reset_common/chip_reset.py#L57-L57))
*   `TENSTORRENT_IOCTL_RESET_DEVICE = (0xFA << 8) | 6` ([tt_tools_common/reset_common/chip_reset.py 58](https://github.com/tenstorrent/tt-tools-common/blob/892885f4/tt_tools_common/reset_common/chip_reset.py#L58-L58))

### Color Constants

Defined in `CMD_LINE_COLOR` class for terminal output:

*   `RED`, `GREEN`, `YELLOW`, `PURPLE`, `BLUE`, `ENDC`

Sources: [tt_tools_common/utils_common/system_utils.py 17-18](https://github.com/tenstorrent/tt-tools-common/blob/892885f4/tt_tools_common/utils_common/system_utils.py#L17-L18)[tt_tools_common/reset_common/chip_reset.py 57-58](https://github.com/tenstorrent/tt-tools-common/blob/892885f4/tt_tools_common/reset_common/chip_reset.py#L57-L58)[tt_tools_common/ui_common/themes.py](https://github.com/tenstorrent/tt-tools-common/blob/892885f4/tt_tools_common/ui_common/themes.py)

* * *

## Next Steps

For detailed API documentation including method signatures, parameter descriptions, and usage examples:

*   **[7.1 Reset API Reference](https://deepwiki.com/tenstorrent/tt-tools-common/7.1-reset-api-reference)** - Complete reset class and function documentation
*   **[7.2 System Utilities API Reference](https://deepwiki.com/tenstorrent/tt-tools-common/7.2-system-utilities-api-reference)** - System utility functions and tools
*   **[7.3 UI Components API Reference](https://deepwiki.com/tenstorrent/tt-tools-common/7.3-ui-components-api-reference)** - Textual widget reference with styling
*   **[7.4 Data Models and Configuration](https://deepwiki.com/tenstorrent/tt-tools-common/7.4-data-models-and-configuration)** - Pydantic models and configuration formats

Dismiss
Refresh this wiki

Enter email to refresh
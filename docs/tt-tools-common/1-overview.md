---
project: tt-tools-common
github: https://github.com/tenstorrent/tt-tools-common
deepwiki: https://deepwiki.com/tenstorrent/tt-tools-common/1-overview
---

# Overview

Relevant source files
*   [README.md](https://github.com/tenstorrent/tt-tools-common/blob/892885f4/README.md?plain=1)
*   [debian/changelog](https://github.com/tenstorrent/tt-tools-common/blob/892885f4/debian/changelog)
*   [pyproject.toml](https://github.com/tenstorrent/tt-tools-common/blob/892885f4/pyproject.toml)

## Purpose and Scope

This document provides a high-level introduction to `tt-tools-common`, a shared Python library that provides common functionality for Tenstorrent hardware management tools. This page covers the library's architecture, core components, consumer applications, and runtime requirements.

For installation instructions, see [Installation and Setup](https://deepwiki.com/tenstorrent/tt-tools-common/1.1-installation-and-setup). For detailed documentation of specific subsystems, refer to [Hardware Reset System](https://deepwiki.com/tenstorrent/tt-tools-common/2-hardware-reset-system), [System Utilities](https://deepwiki.com/tenstorrent/tt-tools-common/3-system-utilities), [User Interface Components](https://deepwiki.com/tenstorrent/tt-tools-common/4-user-interface-components), and [Build and Distribution](https://deepwiki.com/tenstorrent/tt-tools-common/5-build-and-distribution).

## What is tt-tools-common?

`tt-tools-common` is a helper library that consolidates shared functionality across multiple Tenstorrent command-line and TUI applications. It is **not a standalone tool** but rather a foundation that other tools build upon. The library serves three primary purposes:

1.   **Hardware Management**: Provides chip reset capabilities, driver interaction, and hardware detection for Blackhole, Wormhole, and Grayskull architectures
2.   **User Interface Framework**: Offers reusable Textual-based widgets, themes, and styling for building consistent terminal user interfaces
3.   **System Utilities**: Implements common operations like version checking, host information gathering, and configuration management

**Sources**: [README.md 1-46](https://github.com/tenstorrent/tt-tools-common/blob/892885f4/README.md?plain=1#L1-L46)[pyproject.toml 1-84](https://github.com/tenstorrent/tt-tools-common/blob/892885f4/pyproject.toml#L1-L84)

## Architecture Overview

The library is organized into three distinct layers that can be consumed independently or in combination:

**Figure 1**: Component architecture showing module organization and dependencies

**Sources**: [pyproject.toml 23-34](https://github.com/tenstorrent/tt-tools-common/blob/892885f4/pyproject.toml#L23-L34) Architecture diagram from context

## Core Components

### Hardware Interface Layer

The hardware interface layer provides the most critical functionality in `tt-tools-common`: chip reset operations and hardware communication.

| Module | Primary Classes/Functions | Purpose |
| --- | --- | --- |
| `reset_common/` | `ChipReset`, `BHChipReset`, `WHChipReset`, `GSTensixReset` | Chip-specific reset implementations for all supported architectures |
| `reset_common/reset_utils.py` | `parse_reset_input()`, `generate_reset_logs()` | Configuration parsing and structured logging |
| `reset_common/host_reset_log.py` | `HostResetLog`, `PciResetDeviceInfo` | Pydantic models for reset logging |

The reset system implements a **delegation pattern** where chip-specific classes route to the unified `ChipReset` implementation when driver version >= 2.4.1. This architecture enables backward compatibility while migrating to modern reset sequences. For comprehensive documentation, see [Hardware Reset System](https://deepwiki.com/tenstorrent/tt-tools-common/2-hardware-reset-system).

**Sources**: Architecture diagrams from context, [pyproject.toml 23-34](https://github.com/tenstorrent/tt-tools-common/blob/892885f4/pyproject.toml#L23-L34)

### System Utilities Layer

The utilities layer provides system-level operations required by all Tenstorrent tools:

| Module | Primary Functions | Purpose |
| --- | --- | --- |
| `utils_common/system_utils.py` | `check_driver_version()`, `get_host_info()`, `is_driver_version_at_least()` | Driver validation and host information collection |
| `utils_common/tools_utils.py` | `detect_chips_with_callback()`, `hex_to_semver_*()`, `get_board_type_from_upi()` | Chip detection, version conversions, firmware utilities |

These utilities abstract platform-specific details and provide consistent APIs for driver interaction, hardware detection, and version management. See [System Utilities](https://deepwiki.com/tenstorrent/tt-tools-common/3-system-utilities) for detailed documentation.

**Sources**: Architecture diagrams from context

### UI Framework Layer

The UI framework provides pre-built Textual widgets and styling for building consistent terminal interfaces:

| Module | Components | Purpose |
| --- | --- | --- |
| `ui_common/tt_widget.py` | `TTHeader`, `TTFooter`, `TTDataTable`, `TTMenu`, `TTCompatibilityMenu` | Core reusable widgets |
| `ui_common/modal_screens.py` | `TTConfirmBox`, `TTHelperMenuBox`, `TTSettingsMenu` | Modal dialog screens |
| `ui_common/common_style.css` | CSS variables and rules | Global theming and styling |

The framework enables developers to build TUI applications with consistent appearance and behavior without reimplementing common UI patterns. See [User Interface Components](https://deepwiki.com/tenstorrent/tt-tools-common/4-user-interface-components) for widget reference and usage examples.

**Sources**: Architecture diagrams from context, [pyproject.toml 59-63](https://github.com/tenstorrent/tt-tools-common/blob/892885f4/pyproject.toml#L59-L63)

## Consumer Applications

`tt-tools-common` is consumed by multiple Tenstorrent tools, each importing different subsets of functionality:

**Figure 2**: Consumer applications and their API usage patterns

Consumer tools typically:

*   Import `reset_common` for chip reset operations
*   Import `utils_common` for system detection and validation
*   Import `ui_common` for building terminal user interfaces
*   Bundle `data/` files for chip-specific configuration

**Sources**: Architecture diagrams from context

## Dependencies and Requirements

### Runtime Dependencies

The library requires Python >= 3.10 and the following core dependencies:

| Dependency | Version Constraint | Purpose |
| --- | --- | --- |
| `pyluwen` | `~=0.8.0` | Hardware abstraction layer for PCI device communication |
| `textual` | `>=0.59.0` | TUI framework for building terminal applications |
| `pydantic` | `>=1.2` | Data validation and serialization |
| `pyyaml` | `>=6.0.1` | YAML configuration parsing |
| `psutil` | `>=5.9.6` | System and process utilities |
| `distro` | `>=1.8.0` | Linux distribution detection |
| `elasticsearch` | `>=8.11.0` | Optional structured logging backend |
| `rich` | `>=13.7.0` | Terminal text formatting |
| `requests` | `>=2.32.3` | HTTP client for remote operations |
| `tqdm` | `>=4.66.3` | Progress bar displays |
| `tomli` | (no constraint) | TOML file parsing |

**Sources**: [pyproject.toml 23-34](https://github.com/tenstorrent/tt-tools-common/blob/892885f4/pyproject.toml#L23-L34)

### Development Dependencies

For developers contributing to `tt-tools-common`:

`# pyproject.toml [project.optional-dependencies]dev = [    'black>=23.11.0',      # Code formatting    'pre-commit>=3.5.0',   # Git hook framework    'pyluwen~=0.8.0',      # Hardware testing    'pytest>=7.0.0',       # Test framework    'pytest-asyncio>=0.21.0',  # Async test support]`
**Sources**: [pyproject.toml 36-43](https://github.com/tenstorrent/tt-tools-common/blob/892885f4/pyproject.toml#L36-L43)

### System Requirements

*   **Operating System**: Ubuntu 22.04, 24.04, or latest
*   **Python Version**: 3.10, 3.11, or 3.12
*   **Driver**: Tenstorrent kernel driver (`tt-kmd`) >= 1.26.0 for hardware operations
*   **Architecture**: x86_64 (ARM has restrictions for certain reset operations)

For detailed installation procedures, see [Installation and Setup](https://deepwiki.com/tenstorrent/tt-tools-common/1.1-installation-and-setup).

**Sources**: [pyproject.toml 3](https://github.com/tenstorrent/tt-tools-common/blob/892885f4/pyproject.toml#L3-L3)[pyproject.toml 14-22](https://github.com/tenstorrent/tt-tools-common/blob/892885f4/pyproject.toml#L14-L22) Architecture diagrams from context

## Package Distribution

`tt-tools-common` is distributed through two channels:

### PyPI (Python Package Index)

`pip install tt-tools-common`
Python wheel packages are published to PyPI for installation via pip. The build process uses `setuptools` with version information extracted from Git tags. See [Python Package Build](https://deepwiki.com/tenstorrent/tt-tools-common/5.2-python-package-build) for build details.

### Debian Repository

`apt install tt-tools-common`
Debian packages are built for multiple Ubuntu distributions and published to GitHub Releases. The packaging includes proper dependency resolution and system integration. See [Debian Package Build System](https://deepwiki.com/tenstorrent/tt-tools-common/5.1-debian-package-build-system) for packaging details.

**Sources**: [pyproject.toml 45-48](https://github.com/tenstorrent/tt-tools-common/blob/892885f4/pyproject.toml#L45-L48)[debian/changelog 1-236](https://github.com/tenstorrent/tt-tools-common/blob/892885f4/debian/changelog#L1-L236) Architecture diagrams from context

## Package Metadata

| Property | Value |
| --- | --- |
| Package Name | `tt-tools-common` |
| Current Version | `1.6.0` |
| License | Apache 2.0 |
| Maintainer | Sam Bansal ([sbansal@tenstorrent.com](mailto:sbansal@tenstorrent.com)) |
| Source Repository | [https://github.com/tenstorrent/tt-tools-common](https://github.com/tenstorrent/tt-tools-common) |
| Issue Tracker | [https://github.com/tenstorrent/tt-tools-common/issues](https://github.com/tenstorrent/tt-tools-common/issues) |
| Development Status | Beta (4) |
| Environment | Console :: Curses |

**Sources**: [pyproject.toml 1-48](https://github.com/tenstorrent/tt-tools-common/blob/892885f4/pyproject.toml#L1-L48)[README.md 44-45](https://github.com/tenstorrent/tt-tools-common/blob/892885f4/README.md?plain=1#L44-L45)

## Data Assets

The library includes chip-specific data files packaged with the installation:

```
data/
├── chip_data/
│   └── fw_defines.yaml          # Firmware definitions and addresses
└── reset_data/
    └── *.json                   # Reset configuration templates
```

These files are automatically included in distributions via `setuptools` configuration and are accessible to consumer applications at runtime. The `tools_utils` module provides functions to load and parse these data files.

**Sources**: [pyproject.toml 58-63](https://github.com/tenstorrent/tt-tools-common/blob/892885f4/pyproject.toml#L58-L63)

## Version History and Changelog

The package uses semantic versioning (MAJOR.MINOR.PATCH) with the current version defined in [pyproject.toml 4](https://github.com/tenstorrent/tt-tools-common/blob/892885f4/pyproject.toml#L4-L4) Release history and detailed changelogs are maintained in [debian/changelog 1-236](https://github.com/tenstorrent/tt-tools-common/blob/892885f4/debian/changelog#L1-L236) and GitHub Releases. Recent significant changes include:

*   **1.6.0**: Latest stable release
*   **1.5.1**: Added secondary bus reset option, enhanced docstrings
*   **1.5.0**: Updated pyluwen dependency to 0.8.0
*   **1.4.32**: Xen HVM integration with xenstore coordination
*   **1.4.28**: Redesigned reset sequence support with driver version checking

For the complete release workflow and versioning strategy, see [Automated Release Workflow](https://deepwiki.com/tenstorrent/tt-tools-common/5.3-automated-release-workflow).

**Sources**: [pyproject.toml 4](https://github.com/tenstorrent/tt-tools-common/blob/892885f4/pyproject.toml#L4-L4)[debian/changelog 1-42](https://github.com/tenstorrent/tt-tools-common/blob/892885f4/debian/changelog#L1-L42)

Dismiss
Refresh this wiki

Enter email to refresh
---
project: tt-smi
github: https://github.com/tenstorrent/tt-smi
deepwiki: https://deepwiki.com/tenstorrent/tt-smi/9-version-history-and-changes
---

# Changelog and Version History

Relevant source files
*   [CHANGELOG.md](https://github.com/tenstorrent/tt-smi/blob/a2791bfe/CHANGELOG.md?plain=1)
*   [debian/changelog](https://github.com/tenstorrent/tt-smi/blob/a2791bfe/debian/changelog)

This document tracks the evolution of the Tenstorrent System Management Interface (TT-SMI) tool, documenting all releases, feature additions, improvements, and bug fixes. TT-SMI follows [Semantic Versioning](https://semver.org/spec/v2.0.0.html) principles, where version numbers are formatted as MAJOR.MINOR.PATCH.

For information about contributing to TT-SMI development, see the Development and Maintenance overview in [Development and Maintenance](https://deepwiki.com/tenstorrent/tt-smi/6-system-architecture).

## Version Numbering Scheme

TT-SMI follows these semantic versioning guidelines:

*   **MAJOR** version increments indicate significant changes or new hardware support
*   **MINOR** version increments indicate new features without breaking backward compatibility
*   **PATCH** version increments indicate bug fixes and minor improvements

## Version Evolution Timeline

The following diagram illustrates the major milestones in TT-SMI's version history:

Sources: [CHANGELOG.md 1-161](https://github.com/tenstorrent/tt-smi/blob/a2791bfe/CHANGELOG.md?plain=1#L1-L161)[pyproject.toml 1-5](https://github.com/tenstorrent/tt-smi/blob/a2791bfe/pyproject.toml#L1-L5)

## Hardware Support Evolution

The following diagram illustrates when support for different Tenstorrent hardware devices was introduced:

Sources: [CHANGELOG.md 135-146](https://github.com/tenstorrent/tt-smi/blob/a2791bfe/CHANGELOG.md?plain=1#L135-L146)[CHANGELOG.md 80-87](https://github.com/tenstorrent/tt-smi/blob/a2791bfe/CHANGELOG.md?plain=1#L80-L87)[CHANGELOG.md 14-15](https://github.com/tenstorrent/tt-smi/blob/a2791bfe/CHANGELOG.md?plain=1#L14-L15)

## Feature Additions by Major Version

| Version | Release Date | Key Features Added | Hardware Support |
| --- | --- | --- | --- |
| 1.0.0 | Oct 20, 2023 | - First opensource release - Tensix reset support | Grayskull (GS) |
| 1.1.0 | Jan 29, 2024 | - Latest SW version section for outdated software detection | Grayskull (GS) |
| 2.0.0 | Feb 08, 2024 | - Coordinate reporting in GUI - Board level reset support - Mobo reset support - Reset configuration file generation | Grayskull (GS) Wormhole (WH) |
| 3.0.0 | Jul 22, 2024 | - Telemetry reporting in GUI - Reset support for Blackhole (No breaking changes, major version bump to signify new product generation) | Grayskull (GS) Wormhole (WH) Blackhole (BH) |

Sources: [CHANGELOG.md 135-146](https://github.com/tenstorrent/tt-smi/blob/a2791bfe/CHANGELOG.md?plain=1#L135-L146)[CHANGELOG.md 149-153](https://github.com/tenstorrent/tt-smi/blob/a2791bfe/CHANGELOG.md?plain=1#L149-L153)[CHANGELOG.md 80-87](https://github.com/tenstorrent/tt-smi/blob/a2791bfe/CHANGELOG.md?plain=1#L80-L87)[CHANGELOG.md 155-161](https://github.com/tenstorrent/tt-smi/blob/a2791bfe/CHANGELOG.md?plain=1#L155-L161)

## Key System Dependencies Evolution

This table tracks the evolution of critical dependencies across major TT-SMI versions:

| Component | Version 3.0.15 (Current) | Version 2.x | Version 1.x |
| --- | --- | --- | --- |
| pyluwen | 0.6.4 | 0.3.8-0.4.3 | Earlier versions |
| tt_tools_common | 1.4.14 | 1.4.3-1.4.5 | Earlier versions |
| textual | 0.59.0 | 0.59.0 | Earlier versions |
| Python Support | >=3.7 | >=3.7 | >=3.7 |

Sources: [pyproject.toml 25-37](https://github.com/tenstorrent/tt-smi/blob/a2791bfe/pyproject.toml#L25-L37)[CHANGELOG.md 106-107](https://github.com/tenstorrent/tt-smi/blob/a2791bfe/CHANGELOG.md?plain=1#L106-L107)

## Detailed Changelog

Below is the complete version history in reverse chronological order (newest versions first):

### 3.0.15 (April 25, 2025)

*   Patched UBB reset to discover local devices only post-reset
*   Fixed issue with ethernet port status interpretation causing ethernet timeout

### 3.0.14 (April 21, 2025)

*   Added Wormhole UBB reset via command line (`tt-smi --ubb_reset`)
*   Removed unused imports and code (no functional changes)

### 3.0.13 (March 21, 2025)

*   Removed `get_sw_versions`

### 3.0.12 (March 21, 2025)

*   Updated Luwen version to include ethernet firmware version check fix

### 3.0.11 (March 13, 2025)

*   Updated Luwen version to enable chips with external connections but no routing

### 3.0.10 (March 10, 2025)

*   Updated Luwen version to include protoc library detection check

### 3.0.9 (March 7, 2025)

*   Updated Luwen version to fail with an error if chip reinitialization fails

### 3.0.8 (March 6, 2025)

*   Updated Luwen version to include telemetry check during Blackhole ARC initialization

### 3.0.7 (February 4, 2025)

*   Updated tools common to 1.4.14 allowing users to disable SW version fetching via reset config
*   Updated README with instructions to disable reporting and improved `-f` option

### 3.0.6 (January 29, 2025)

*   Merged "Host Info" and "Compatibility" infoboxes into a more compact unified box
*   Modal display of compatibility issues
*   Enhanced `tt-smi -f -` behavior to act like `tt-smi -s`, printing snapshot info to STDOUT

### 3.0.5 (January 7, 2025)

*   Enhanced `tt-smi -s` to print snapshots to stdout
*   Updated `tt-smi -f <optional filename>` to behave like `tt-smi -s -f`
*   Added TTY detection for better scripting support
*   Added `--snapshot_no_tty` argument to force no-TTY behavior
*   Updated tt-tools-common to 1.4.10

### 3.0.4 (December 11, 2024)

*   Enhanced `tt-smi -r` to reset all PCI devices
*   Updated tt-tools-common; failed resets now exit with a failure code

### 3.0.3 (October 23, 2024)

*   Updated tt-tools-common and Luwen versions to improve reset reliability when chip is inaccessible

### 3.0.2 (October 9, 2024)

*   Fixed bug in PCIe Gen1 reporting
*   Applied spelling fixes

### 3.0.1 (September 16, 2024)

*   Updated Luwen library versions (0.4.0 → 0.4.3)

### 3.0.0 (July 22, 2024)

*   Added Blackhole (BH) support 
    *   Added telemetry reporting on GUI
    *   Added reset support

*   No breaking changes (major version bump to signify new product generation)
*   Note: Telemetry limits reporting coming in future update

### 2.2.3 (July 12, 2024)

*   Added FW_Bundle_Version under firmware version tab
*   Updated Luwen library versions (0.3.8 → 0.3.11)
*   Updated tt-tools-common library versions (1.4.4 → 1.4.5)

### 2.2.2 (June 21, 2024)

*   Updated Pydantic library version (1.* → >=1.2) to resolve [TT-SMI issue #27](https://github.com/tenstorrent/tt-smi/blob/a2791bfe/TT-SMI%20issue%20#27)
*   Updated tt-tools-common version (1.4.3 → 1.4.4) to align Pydantic, requests, and tqdm library versions

### 2.2.1 (May 14, 2024)

*   Updated textual (0.59.0), Luwen (0.3.8), and tt_tools_common (1.4.3) library versions
*   Removed unused Python libraries

### 2.2.0 (March 22, 2024)

*   Added RUST_BACKTRACE environment variable to get detailed device failure logs
*   Made `detect_device_fallible` the default device detection method 
    *   Improved error messages for failures

*   Fixed PCI speed/width in snapshots
*   Fixed list option to match dev/tenstorrent device IDs
*   Fixed reset_config generation to work on local devices only
*   Migrated all reset related code to tt_tools_common
*   Migrated all reset config generation related code to tt_tools_common

### 2.1.0 (March 8, 2024)

*   Migrated all reset related code to tt_tools_common
*   Migrated all reset config generation related code to tt_tools_common
*   Fixed PCIe gen speed reporting bug
*   Removed unused GUI footer option

### 2.0.0 (February 8, 2024)

*   Added Wormhole (WH) support
*   Added coordinate reporting in GUI
*   Added WH reset capabilities: 
    *   WH board level reset support
    *   Motherboard reset support

*   Added ability to generate a reset config file and reset boards with it
*   Fixed PCIe gen speed reporting from sysfs

### 1.1.0 (January 29, 2024)

*   Added latest SW version section to notify users when their Tenstorrent software is out of date

### 1.0.0 (October 20, 2023)

*   Initial release of open source tt-smi
*   Added Grayskull (GS) support
*   Added Tensix reset support

Sources: [CHANGELOG.md 1-161](https://github.com/tenstorrent/tt-smi/blob/a2791bfe/CHANGELOG.md?plain=1#L1-L161)

## Version Mapping to Core Functionality

The following diagram shows how key features map to specific version releases:

Sources: [CHANGELOG.md 1-161](https://github.com/tenstorrent/tt-smi/blob/a2791bfe/CHANGELOG.md?plain=1#L1-L161)

## Version Control and Repository Information

TT-SMI is maintained as an open-source project on GitHub at [https](https://github.com/tenstorrent/tt-smi/blob/a2791bfe/https#LNaN-LNaN) The project follows standard GitHub workflows for issue tracking, feature requests, and code contributions. For licensing information, see [License and Legal Information](https://deepwiki.com/tenstorrent/tt-smi/6.2-backend-system-(ttsmibackend)).

Sources: [pyproject.toml 45-48](https://github.com/tenstorrent/tt-smi/blob/a2791bfe/pyproject.toml#L45-L48)

Dismiss
Refresh this wiki

Enter email to refresh
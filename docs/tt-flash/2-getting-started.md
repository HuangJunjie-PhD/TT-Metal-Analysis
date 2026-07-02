---
project: tt-flash
github: https://github.com/tenstorrent/tt-flash
deepwiki: https://deepwiki.com/tenstorrent/tt-flash/2-getting-started
---

# Getting Started

Relevant source files
*   [README.md](https://github.com/tenstorrent/tt-flash/blob/60c06032/README.md?plain=1)
*   [pyproject.toml](https://github.com/tenstorrent/tt-flash/blob/60c06032/pyproject.toml)

This document guides users through the initial setup and basic usage of tt-flash, a firmware flashing utility for Tenstorrent AI accelerator chips. It covers prerequisites, installation methods, and fundamental usage patterns.

For detailed installation procedures, see [Installation](https://deepwiki.com/tenstorrent/tt-flash/2.1-installation). For comprehensive command examples and options, see [Basic Usage](https://deepwiki.com/tenstorrent/tt-flash/2.2-basic-usage). For architecture and system design information, see [Overview](https://deepwiki.com/tenstorrent/tt-flash/1-overview).

* * *

## Prerequisites

tt-flash requires specific runtime dependencies and system configurations to operate correctly.

### Python Environment

The package requires **Python 3.10 or newer** as specified in [pyproject.toml 6](https://github.com/tenstorrent/tt-flash/blob/60c06032/pyproject.toml#L6-L6) The following Python versions are officially supported:

*   Python 3.10
*   Python 3.11

The version constraint exists because tt-flash uses modern Python features and type annotations compatible with basedpyright's static analysis targeting Python 3.7+ at the source level but runtime execution at 3.10+.

**Sources:**[pyproject.toml 6](https://github.com/tenstorrent/tt-flash/blob/60c06032/pyproject.toml#L6-L6)[pyproject.toml 23-35](https://github.com/tenstorrent/tt-flash/blob/60c06032/pyproject.toml#L23-L35)

### Rust Toolchain

The `pyluwen` dependency (~0.8.0) is a Rust-Python binding that requires the Rust compiler and cargo build system. This is documented in [README.md 16-26](https://github.com/tenstorrent/tt-flash/blob/60c06032/README.md?plain=1#L16-L26)

**Installation via distribution packages:**

| Distribution | Command |
| --- | --- |
| Fedora / EL9 | `sudo dnf install cargo` |
| Ubuntu / Debian | `sudo apt install cargo` |

**Installation via rustup:**

`curl --proto '=https' --tlsv1.2 -sSf https://sh.rustup.rs | shsource "$HOME/.cargo/env"`
**Sources:**[README.md 16-26](https://github.com/tenstorrent/tt-flash/blob/60c06032/README.md?plain=1#L16-L26)

### Hardware and Driver Requirements

tt-flash communicates with Tenstorrent devices through the `tt-kmd` kernel module and PCI subsystem:

*   **tt-kmd kernel driver**: Version 2.0.0 or newer must be loaded
*   **PCI access**: User must have permissions to access `/dev/tenstorrent/*` device nodes
*   **Supported hardware**: Wormhole or Blackhole chips (see [Supported Hardware](https://deepwiki.com/tenstorrent/tt-flash/4-supported-hardware))

For legacy Grayskull support, use [tt-flash v3.4.7](https://github.com/tenstorrent/tt-flash/blob/60c06032/tt-flash%20v3.4.7)

**Sources:**[README.md 150-152](https://github.com/tenstorrent/tt-flash/blob/60c06032/README.md?plain=1#L150-L152)[pyproject.toml 25](https://github.com/tenstorrent/tt-flash/blob/60c06032/pyproject.toml#L25-L25)

* * *

## Installation Methods Overview

tt-flash supports three installation methods, each suited to different use cases:

### Method 1: PyPI Installation (Recommended for Users)

The simplest installation method uses the Python Package Index:

`pip install tt-flash`
This installs the `tt-flash` console script as defined in [pyproject.toml 49-50](https://github.com/tenstorrent/tt-flash/blob/60c06032/pyproject.toml#L49-L50) which maps to the `main()` function in [tt_flash/main.py](https://github.com/tenstorrent/tt-flash/blob/60c06032/tt_flash/main.py)

**Virtual environment (recommended):**

`python -m venv .venvsource .venv/bin/activatepip install tt-flash`
**Sources:**[README.md 28-43](https://github.com/tenstorrent/tt-flash/blob/60c06032/README.md?plain=1#L28-L43)[pyproject.toml 49-50](https://github.com/tenstorrent/tt-flash/blob/60c06032/pyproject.toml#L49-L50)

### Method 2: Source Build (For Development)

Developers modifying tt-flash should build from source:

`git clone https://github.com/tenstorrent/tt-flash.gitcd tt-flashpip install .`
For development with hot-reload:

`pip install --editable .`
The editable install creates a symlink to the source directory, allowing code changes to take effect without reinstalling.

**Sources:**[README.md 46-62](https://github.com/tenstorrent/tt-flash/blob/60c06032/README.md?plain=1#L46-L62)

### Method 3: Debian Package Installation

For Ubuntu/Debian systems, `.deb` packages are available for specific Ubuntu versions (22.04, 24.04, latest). See [Debian Packaging](https://deepwiki.com/tenstorrent/tt-flash/9.2-debian-packaging) for details on the build process and distribution.

**Sources:** See [Debian Packaging](https://deepwiki.com/tenstorrent/tt-flash/9.2-debian-packaging) for detailed information.

* * *

## Command Structure

Once installed, tt-flash provides a command-line interface with two primary subcommands:

### Global Options

Available for all subcommands:

| Option | Description | Source |
| --- | --- | --- |
| `-h, --help` | Display help text | [README.md 68-86](https://github.com/tenstorrent/tt-flash/blob/60c06032/README.md?plain=1#L68-L86) |
| `-v, --version` | Show program version | [README.md 74](https://github.com/tenstorrent/tt-flash/blob/60c06032/README.md?plain=1#L74-L74) |
| `--sys-config SYS_CONFIG` | Path to pre-generated sys-config JSON | [README.md 75-76](https://github.com/tenstorrent/tt-flash/blob/60c06032/README.md?plain=1#L75-L76) |
| `--no-color` | Disable colorful output | [README.md 77](https://github.com/tenstorrent/tt-flash/blob/60c06032/README.md?plain=1#L77-L77) |
| `--no-tty` | Force disable TTY command output | [README.md 78](https://github.com/tenstorrent/tt-flash/blob/60c06032/README.md?plain=1#L78-L78) |

### Subcommands

*   **`flash`**: Flash firmware to Tenstorrent devices. See [flash Command](https://deepwiki.com/tenstorrent/tt-flash/3.1-flash-command) for detailed options.
*   **`verify`**: Verify SPI flash contents and display firmware versions. See [verify Command](https://deepwiki.com/tenstorrent/tt-flash/3.2-verify-command) for detailed options.

**Sources:**[README.md 64-104](https://github.com/tenstorrent/tt-flash/blob/60c06032/README.md?plain=1#L64-L104)[pyproject.toml 49-50](https://github.com/tenstorrent/tt-flash/blob/60c06032/pyproject.toml#L49-L50)

* * *

## Basic Workflow

The typical tt-flash workflow consists of three stages: firmware bundle acquisition, device detection, and flashing.

### Minimal Flash Example

`tt-flash /path/to/firmware.fwbundle`
This command:

1.   Searches for system configuration in default locations ([README.md 122-127](https://github.com/tenstorrent/tt-flash/blob/60c06032/README.md?plain=1#L122-L127))
2.   Detects all Tenstorrent devices on the system
3.   Flashes firmware to all compatible devices
4.   Resets devices to load new firmware
5.   Validates post-flash state

### Expected Output

Example output from [README.md 119-148](https://github.com/tenstorrent/tt-flash/blob/60c06032/README.md?plain=1#L119-L148):

```
Stage: SETUP
        Searching for default sys-config path
        Could not find config in default search locations...
Stage: DETECT
Stage: FLASH
        Sub Stage: VERIFY
                Verifying fw-package can be flashed: complete
                Verifying Blackhole[0] can be flashed
        Stage: FLASH
                Sub Stage FLASH Step 1: Blackhole[0]
                        ROM version is: (18, 10, 0, 0). tt-flash version is: (18, 12, 0, 0)
                        FW bundle version > ROM version. ROM will now be updated.
                Sub Stage FLASH Step 2: Blackhole[0] {p150a}
                        Writing new firmware... (this may take up to 1 minute)
                        Writing new firmware... SUCCESS
                        Verifying flashed firmware... (this may also take up to 1 minute)
                        Firmware verification... SUCCESS
Stage: RESET
 Starting PCI link reset on BH devices at PCI indices: 0 
 Waiting for up to 60 seconds for asic to come back after reset
FLASH SUCCESS
```

**Sources:**[README.md 106-148](https://github.com/tenstorrent/tt-flash/blob/60c06032/README.md?plain=1#L106-L148)

* * *

## Verification

After installation, verify tt-flash is correctly installed:

### Check Version

`tt-flash --version`
Expected output format: `tt-flash version X.Y.Z` where X.Y.Z corresponds to [pyproject.toml 3](https://github.com/tenstorrent/tt-flash/blob/60c06032/pyproject.toml#L3-L3)

### Check Help Text

`tt-flash --help`
This displays global options and available subcommands as documented in [README.md 68-86](https://github.com/tenstorrent/tt-flash/blob/60c06032/README.md?plain=1#L68-L86)

### Test Device Detection

`tt-flash verify`
This runs device detection without modifying hardware, validating:

*   PCI device enumeration works
*   tt-kmd driver is loaded and accessible
*   Devices are recognized as supported chip types

**Sources:**[README.md 64-86](https://github.com/tenstorrent/tt-flash/blob/60c06032/README.md?plain=1#L64-L86)

* * *

## Firmware Bundle Acquisition

Firmware bundles (`.fwbundle` files) are distributed separately from tt-flash. The firmware repository is located at:

**[https://github.com/tenstorrent/tt-firmware](https://github.com/tenstorrent/tt-firmware)**

Firmware files are licensed and distributed independently. tt-flash acts solely as a utility to update devices with provided firmware images, as noted in [README.md 111-112](https://github.com/tenstorrent/tt-flash/blob/60c06032/README.md?plain=1#L111-L112)

For firmware bundle structure and format details, see [Firmware Bundle Format](https://deepwiki.com/tenstorrent/tt-flash/5.1-firmware-bundle-format).

**Sources:**[README.md 111-112](https://github.com/tenstorrent/tt-flash/blob/60c06032/README.md?plain=1#L111-L112)

* * *

## Next Steps

*   **For detailed installation instructions**: See [Installation](https://deepwiki.com/tenstorrent/tt-flash/2.1-installation)
*   **For command usage and examples**: See [Basic Usage](https://deepwiki.com/tenstorrent/tt-flash/2.2-basic-usage)
*   **For flash command options**: See [flash Command](https://deepwiki.com/tenstorrent/tt-flash/3.1-flash-command)
*   **For verification procedures**: See [verify Command](https://deepwiki.com/tenstorrent/tt-flash/3.2-verify-command)
*   **For supported hardware details**: See [Supported Hardware](https://deepwiki.com/tenstorrent/tt-flash/4-supported-hardware)
*   **For understanding the flash process**: See [Flashing Operations](https://deepwiki.com/tenstorrent/tt-flash/5-flashing-operations)

**Sources:**[README.md 1-157](https://github.com/tenstorrent/tt-flash/blob/60c06032/README.md?plain=1#L1-L157)[pyproject.toml 1-84](https://github.com/tenstorrent/tt-flash/blob/60c06032/pyproject.toml#L1-L84)

Dismiss
Refresh this wiki

Enter email to refresh
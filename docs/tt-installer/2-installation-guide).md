---
project: tt-installer
github: https://github.com/tenstorrent/tt-installer
deepwiki: https://deepwiki.com/tenstorrent/tt-installer/2-installation-guide)
---

# Installation Guide

Relevant source files
*   [README.md](https://github.com/tenstorrent/tt-installer/blob/19fdd6a2/README.md?plain=1)
*   [install.sh](https://github.com/tenstorrent/tt-installer/blob/19fdd6a2/install.sh)

## Purpose and Scope

This guide provides detailed instructions for installing the Tenstorrent software stack using the tt-installer. The installation process configures your system with all components needed to work with Tenstorrent hardware, including drivers, firmware tools, system management utilities, and development environments. For information about the overall installation architecture, see [Installation Architecture](https://deepwiki.com/tenstorrent/tt-installer/3-installation-architecture).

## Prerequisites

### Supported Operating Systems

The tt-installer supports various Linux distributions with different levels of compatibility:

| OS | Version | Support Level | Notes |
| --- | --- | --- | --- |
| Ubuntu | 24.04.2 LTS | Full | None |
| Ubuntu | 22.04.5 LTS | Full | Preferred OS |
| Ubuntu | 20.04.6 LTS | Limited | Deprecated; no Metalium support |
| Debian | 12.10.0 | With Notes | Requires manual Rust installation |
| Fedora | 41/42 | With Notes | May require restart after base packages |

Sources: [README.md 59-72](https://github.com/tenstorrent/tt-installer/blob/19fdd6a2/README.md?plain=1#L59-L72)

### Requirements

*   Administrative (sudo) permissions on your system
*   Internet connection
*   Git (for repository-based installation)
*   Compatible Tenstorrent hardware

## Installation Methods

### One-Line Installation

The fastest way to install is using curl:

`/bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/tenstorrent/tt-installer/refs/heads/main/install.sh)"`
Sources: [README.md 5-8](https://github.com/tenstorrent/tt-installer/blob/19fdd6a2/README.md?plain=1#L5-L8)

### Local Installation

Alternatively, clone the repository and run the installer locally:

`git clone https://github.com/tenstorrent/tt-installer.gitcd tt-installer./install.sh`
Sources: [README.md 38-43](https://github.com/tenstorrent/tt-installer/blob/19fdd6a2/README.md?plain=1#L38-L43)

## Installation Process

### Installation Flow Diagram

Sources: [install.sh 391-656](https://github.com/tenstorrent/tt-installer/blob/19fdd6a2/install.sh#L391-L656)

### Installation Steps

1.   **Check Sudo Permissions**: The installer verifies you have sudo access using the `check_has_sudo_perms()` function
2.   **Detect Linux Distribution**: Identifies your OS using the `detect_distro()` function
3.   **Install Base Packages**: Installs required dependencies using your package manager
4.   **Configure Python Environment**: Sets up Python for installing tools
5.   **Install Kernel Mode Driver (KMD)**: Installs the driver for communicating with hardware
6.   **Update Firmware**: Uses tt-flash to update your device firmware
7.   **Setup HugePages**: Configures memory for optimal hardware access
8.   **Install tt-smi**: Installs the System Management Interface
9.   **Install Podman**: Sets up container infrastructure
10.   **Install tt-metalium Container**: Creates development environment

Sources: [install.sh 391-656](https://github.com/tenstorrent/tt-installer/blob/19fdd6a2/install.sh#L391-L656)[README.md 22-32](https://github.com/tenstorrent/tt-installer/blob/19fdd6a2/README.md?plain=1#L22-L32)

## Installation Modes

The tt-installer supports three operational modes:

### Interactive Mode (Default)

In this mode, the installer prompts you for input on configuration options.

### Non-Interactive Mode

For unattended installations using default or pre-configured settings:

`TT_MODE_NON_INTERACTIVE=0 ./install.sh`
Sources: [install.sh 106-113](https://github.com/tenstorrent/tt-installer/blob/19fdd6a2/install.sh#L106-L113)[README.md 44-49](https://github.com/tenstorrent/tt-installer/blob/19fdd6a2/README.md?plain=1#L44-L49)

### Container Mode

For installing within container environments, automatically skipping host-level components:

`TT_MODE_CONTAINER=0 ./install.sh`
Sources: [install.sh 96-103](https://github.com/tenstorrent/tt-installer/blob/19fdd6a2/install.sh#L96-L103)

## Configuration Options

### Component Architecture Diagram

Sources: [install.sh 47-48](https://github.com/tenstorrent/tt-installer/blob/19fdd6a2/install.sh#L47-L48)[install.sh 318-337](https://github.com/tenstorrent/tt-installer/blob/19fdd6a2/install.sh#L318-L337)[install.sh 586-588](https://github.com/tenstorrent/tt-installer/blob/19fdd6a2/install.sh#L586-L588)

### Python Environment Options

The installer provides four methods for installing Python packages:

| Option | Description | Environment Variable |
| --- | --- | --- |
| 1 | Use active virtual environment | `TT_PYTHON_CHOICE=1` |
| 2 | Create new virtual environment (Default) | `TT_PYTHON_CHOICE=2` |
| 3 | Use system Python | `TT_PYTHON_CHOICE=3` |
| 4 | Use pipx | `TT_PYTHON_CHOICE=4` |

Sources: [install.sh 76-83](https://github.com/tenstorrent/tt-installer/blob/19fdd6a2/install.sh#L76-L83)[install.sh 224-251](https://github.com/tenstorrent/tt-installer/blob/19fdd6a2/install.sh#L224-L251)[install.sh 489-525](https://github.com/tenstorrent/tt-installer/blob/19fdd6a2/install.sh#L489-L525)

### Component Selection

You can selectively skip components using environment variables:

| Environment Variable | Default | Purpose |
| --- | --- | --- |
| `TT_SKIP_INSTALL_KMD=0` | 1 (install) | Skip Kernel Mode Driver installation |
| `TT_SKIP_INSTALL_HUGEPAGES=0` | 1 (install) | Skip HugePages setup |
| `TT_SKIP_UPDATE_FIRMWARE=0` | 1 (install) | Skip firmware updates |
| `TT_SKIP_INSTALL_PODMAN=0` | 1 (install) | Skip Podman installation |
| `TT_SKIP_INSTALL_METALIUM_CONTAINER=0` | 1 (install) | Skip tt-metalium container setup |

Sources: [install.sh 52-67](https://github.com/tenstorrent/tt-installer/blob/19fdd6a2/install.sh#L52-L67)

### Reboot Options

The installer provides three options for post-installation reboot behavior:

| Option | Description | Environment Variable |
| --- | --- | --- |
| 1 | Ask the user (Default) | `TT_REBOOT_OPTION=1` |
| 2 | Never reboot | `TT_REBOOT_OPTION=2` |
| 3 | Always reboot | `TT_REBOOT_OPTION=3` |

Sources: [install.sh 85-91](https://github.com/tenstorrent/tt-installer/blob/19fdd6a2/install.sh#L85-L91)[install.sh 645-655](https://github.com/tenstorrent/tt-installer/blob/19fdd6a2/install.sh#L645-L655)

## Installed Components

### Kernel-Mode Driver (KMD)

The KMD enables communication with Tenstorrent hardware. It's installed using DKMS for automatic rebuilding after kernel updates.

Installation process:

1.   Clone the KMD repository from the specified version tag
2.   Install using DKMS
3.   Load the driver module

For more information about the KMD, see [Kernel-Mode Driver (KMD)](https://deepwiki.com/tenstorrent/tt-installer/5.1-kernel-mode-driver-(kmd)).

Sources: [install.sh 21-26](https://github.com/tenstorrent/tt-installer/blob/19fdd6a2/install.sh#L21-L26)[install.sh 529-553](https://github.com/tenstorrent/tt-installer/blob/19fdd6a2/install.sh#L529-L553)

### tt-flash and Firmware

The tt-flash tool is used to update device firmware. The installer:

1.   Installs tt-flash using pip/pipx
2.   Downloads the specified firmware version
3.   Updates firmware on all connected devices

For more details, see [Firmware and tt-flash](https://deepwiki.com/tenstorrent/tt-installer/5.2-firmware-and-tt-flash).

Sources: [install.sh 29-34](https://github.com/tenstorrent/tt-installer/blob/19fdd6a2/install.sh#L29-L34)[install.sh 555-571](https://github.com/tenstorrent/tt-installer/blob/19fdd6a2/install.sh#L555-L571)

### HugePages Configuration

HugePages improve memory access performance for Tenstorrent hardware. Setup includes:

1.   Installing the tenstorrent-tools package
2.   Enabling the tenstorrent-hugepages service
3.   Mounting the HugePages filesystem

For more information, see [HugePages Configuration](https://deepwiki.com/tenstorrent/tt-installer/5.3-hugepages-configuration).

Sources: [install.sh 573-603](https://github.com/tenstorrent/tt-installer/blob/19fdd6a2/install.sh#L573-L603)

### System Management Interface (tt-smi)

The tt-smi tool provides monitoring and management capabilities for Tenstorrent hardware.

For more details, see [System Management Interface (tt-smi)](https://deepwiki.com/tenstorrent/tt-installer/5.4-system-management-interface-(tt-smi)).

Sources: [install.sh 605-607](https://github.com/tenstorrent/tt-installer/blob/19fdd6a2/install.sh#L605-L607)

### tt-metalium Container

The tt-metalium container provides a development environment for Tenstorrent hardware:

1.   Pulls the specified container image
2.   Creates a wrapper script in `~/.local/bin/tt-metalium`
3.   Configures the container to access Tenstorrent hardware

For more information, see [tt-metalium Container](https://deepwiki.com/tenstorrent/tt-installer/5.5-tt-metalium-container).

Sources: [install.sh 318-337](https://github.com/tenstorrent/tt-installer/blob/19fdd6a2/install.sh#L318-L337)[install.sh 618-627](https://github.com/tenstorrent/tt-installer/blob/19fdd6a2/install.sh#L618-L627)

## Using Installed Components

### System Management Interface

After installation and system reboot, verify your setup with:

`tt-smi`
### Firmware Updates

Update firmware manually with:

`tt-flash --fw-tar <firmware_file>`
If the update fails, try force updating:

`tt-flash --fw-tar <firmware_file> --force`
### tt-metalium Container

The container provides a ready-to-use development environment:

`# Start interactive shelltt-metalium # Run specific commandtt-metalium <command> # Run Python scripttt-metalium python script.py`
Sources: [README.md 10-13](https://github.com/tenstorrent/tt-installer/blob/19fdd6a2/README.md?plain=1#L10-L13)[install.sh 638-643](https://github.com/tenstorrent/tt-installer/blob/19fdd6a2/install.sh#L638-L643)

## Troubleshooting

### Python Environment Issues

If you installed tools in a virtual environment (options 1 or 2), you must activate it before using the tools:

`source ~/.tenstorrent-venv/bin/activate`
### Installation Logs

The installer creates a detailed log file at the location shown during installation (typically in `/tmp/tenstorrent_install_XXXXXX/install.log`).

### Distribution-Specific Issues

*   **Ubuntu 20.04**: tt-metalium is not supported on Ubuntu 20.04
*   **Debian**: Requires manual installation of Rust using rustup ([https://rustup.rs/](https://rustup.rs/))
*   **Fedora**: May require a system restart after base package installation

Sources: [install.sh 465-472](https://github.com/tenstorrent/tt-installer/blob/19fdd6a2/install.sh#L465-L472)[README.md 66-69](https://github.com/tenstorrent/tt-installer/blob/19fdd6a2/README.md?plain=1#L66-L69)

Dismiss
Refresh this wiki

Enter email to refresh
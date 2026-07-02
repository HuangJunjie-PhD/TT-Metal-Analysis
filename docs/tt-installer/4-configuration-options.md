---
project: tt-installer
github: https://github.com/tenstorrent/tt-installer
deepwiki: https://deepwiki.com/tenstorrent/tt-installer/4-configuration-options
---

# Configuration Options

Relevant source files
*   [README.md](https://github.com/tenstorrent/tt-installer/blob/19fdd6a2/README.md?plain=1)
*   [install.sh](https://github.com/tenstorrent/tt-installer/blob/19fdd6a2/install.sh)

This page documents the configuration options available when using the tt-installer. The tt-installer provides various ways to customize the installation process according to your needs through environment variables, installation modes, and component selection. For information about the overall installation process, refer to [Installation Guide](https://deepwiki.com/tenstorrent/tt-installer/2-installation-guide). For details about specific Tenstorrent components, see [Tenstorrent Components](https://deepwiki.com/tenstorrent/tt-installer/5-tenstorrent-components).

## Installation Modes

The tt-installer offers three primary installation modes that affect how the script interacts with the user and what components it installs.

Sources: [install.sh 106-113](https://github.com/tenstorrent/tt-installer/blob/19fdd6a2/install.sh#L106-L113)[install.sh 95-103](https://github.com/tenstorrent/tt-installer/blob/19fdd6a2/install.sh#L95-L103)

| Mode | Environment Variable | Default | Description |
| --- | --- | --- | --- |
| Interactive | - | Enabled by default | Prompts user for all configuration choices |
| Non-Interactive | `TT_MODE_NON_INTERACTIVE=0` | Disabled by default | Uses default values or environment variables without prompting |
| Container | `TT_MODE_CONTAINER=0` | Disabled by default | Skips KMD and HugePages installation, never reboots |

### Interactive Mode

In interactive mode (default), the installer will prompt the user to make choices regarding:

*   Python environment configuration
*   Whether to install Podman Metalium container
*   Whether to reboot after installation
*   Whether to reinstall KMD if already present

### Non-Interactive Mode

Non-interactive mode is useful for scripted or automated installations. When enabled:

*   No user prompts are displayed
*   Default values are used unless overridden by environment variables
*   Reboot behavior defaults to "never reboot" unless explicitly set

Example usage:

`TT_MODE_NON_INTERACTIVE=0 ./install.sh`
### Container Mode

Container mode is designed for installations inside containers where kernel-level modifications are not possible:

*   Skips KMD installation (`SKIP_INSTALL_KMD=0`)
*   Skips HugePages configuration (`SKIP_INSTALL_HUGEPAGES=0`)
*   Skips Podman installation (`SKIP_INSTALL_PODMAN=0`)
*   Never reboots (`REBOOT_OPTION=2`)

Example usage:

`TT_MODE_CONTAINER=0 ./install.sh`
Sources: [install.sh 95-103](https://github.com/tenstorrent/tt-installer/blob/19fdd6a2/install.sh#L95-L103)[install.sh 106-113](https://github.com/tenstorrent/tt-installer/blob/19fdd6a2/install.sh#L106-L113)

## Component Selection Options

The tt-installer allows you to selectively skip installation of various components using environment variables.

Sources: [install.sh 54-67](https://github.com/tenstorrent/tt-installer/blob/19fdd6a2/install.sh#L54-L67)

| Component | Environment Variable | Default | Description |
| --- | --- | --- | --- |
| KMD | `TT_SKIP_INSTALL_KMD=0` | Installed by default | Skips installation of the Kernel Mode Driver |
| HugePages | `TT_SKIP_INSTALL_HUGEPAGES=0` | Configured by default | Skips HugePages configuration |
| Firmware | `TT_SKIP_UPDATE_FIRMWARE=0` | Updated by default | Skips tt-flash installation and firmware update |
| Podman | `TT_SKIP_INSTALL_PODMAN=0` | Installed by default | Skips Podman installation |
| Metalium | `TT_SKIP_INSTALL_METALIUM_CONTAINER=0` | Installed by default | Skips Metalium container setup |

Each of these environment variables expects a value of `0` to skip the component. By default, all components are installed (`1`).

Example usage to skip firmware update:

`TT_SKIP_UPDATE_FIRMWARE=0 ./install.sh`
Sources: [install.sh 54-67](https://github.com/tenstorrent/tt-installer/blob/19fdd6a2/install.sh#L54-L67)

## Python Environment Configuration

The tt-installer provides multiple options for configuring the Python environment used to install Python packages like `tt-smi` and `tt-flash`.

Sources: [install.sh 76-83](https://github.com/tenstorrent/tt-installer/blob/19fdd6a2/install.sh#L76-L83)[install.sh 489-525](https://github.com/tenstorrent/tt-installer/blob/19fdd6a2/install.sh#L489-L525)

| Option | Value | Description | Environment Variable |
| --- | --- | --- | --- |
| Use active venv | `TT_PYTHON_CHOICE=1` | Uses currently activated virtual environment | `VIRTUAL_ENV` must be set |
| Create new venv | `TT_PYTHON_CHOICE=2` | Creates a new virtual environment (default) | Can specify location with `TT_NEW_VENV_LOCATION` |
| Use system Python | `TT_PYTHON_CHOICE=3` | Installs packages system-wide | Not recommended, may conflict with system packages |
| Use pipx | `TT_PYTHON_CHOICE=4` | Uses pipx for isolated package installation | Not available on Ubuntu 20.04 |

### Virtual Environment Path Configuration

When creating a new virtual environment (`TT_PYTHON_CHOICE=2`), you can customize the installation path:

Sources: [install.sh 253-264](https://github.com/tenstorrent/tt-installer/blob/19fdd6a2/install.sh#L253-L264)

Example usage:

`TT_PYTHON_CHOICE=2 TT_NEW_VENV_LOCATION=/opt/tenstorrent-venv ./install.sh`
## Reboot Configuration

The tt-installer offers three options for handling system reboots after installation.

Sources: [install.sh 85-91](https://github.com/tenstorrent/tt-installer/blob/19fdd6a2/install.sh#L85-L91)[install.sh 645-655](https://github.com/tenstorrent/tt-installer/blob/19fdd6a2/install.sh#L645-L655)

| Option | Value | Description |
| --- | --- | --- |
| Ask user | `TT_REBOOT_OPTION=1` | Prompts user whether to reboot (default) |
| Never reboot | `TT_REBOOT_OPTION=2` | Never reboots automatically |
| Always reboot | `TT_REBOOT_OPTION=3` | Always reboots automatically after installation |

Example usage:

`TT_REBOOT_OPTION=3 ./install.sh`
Note: In non-interactive mode, if `TT_REBOOT_OPTION` is not specified, it defaults to `2` (never reboot).

Sources: [install.sh 85-91](https://github.com/tenstorrent/tt-installer/blob/19fdd6a2/install.sh#L85-L91)[install.sh 645-655](https://github.com/tenstorrent/tt-installer/blob/19fdd6a2/install.sh#L645-L655)

## Version Selection

The tt-installer allows you to specify which versions of various components to install. If not specified, the latest tagged versions will be used.

Sources: [install.sh 18-42](https://github.com/tenstorrent/tt-installer/blob/19fdd6a2/install.sh#L18-L42)[install.sh 70-74](https://github.com/tenstorrent/tt-installer/blob/19fdd6a2/install.sh#L70-L74)

| Component | Environment Variable | Default |
| --- | --- | --- |
| Kernel Mode Driver | `TT_KMD_VERSION` | Latest version from git tags |
| Firmware | `TT_FW_VERSION` | Latest version from git tags |
| System Tools | `TT_SYSTOOLS_VERSION` | Latest version from git tags |

Example usage to specify specific versions:

`TT_KMD_VERSION=0.8.0 TT_FW_VERSION=1.5.0 TT_SYSTOOLS_VERSION=1.0.0 ./install.sh`
## Metalium Container Configuration

The tt-installer configures the tt-metalium container using Podman. You can customize the container image and tag.

Sources: [install.sh 44-50](https://github.com/tenstorrent/tt-installer/blob/19fdd6a2/install.sh#L44-L50)[install.sh 310-337](https://github.com/tenstorrent/tt-installer/blob/19fdd6a2/install.sh#L310-L337)

| Setting | Environment Variable | Default |
| --- | --- | --- |
| Image URL | `TT_METALIUM_IMAGE_URL` | `ghcr.io/tenstorrent/tt-metal/tt-metalium-ubuntu-22.04-release-amd64` |
| Image Tag | `TT_METALIUM_IMAGE_TAG` | `latest-rc` |

Example usage:

`TT_METALIUM_IMAGE_URL="ghcr.io/tenstorrent/tt-metal/tt-metalium-custom" TT_METALIUM_IMAGE_TAG="v1.0" ./install.sh`
## Environment Variables Summary

The following table summarizes all available environment variables for configuring the tt-installer:

| Environment Variable | Description | Default | Example Value |
| --- | --- | --- | --- |
| `TT_MODE_NON_INTERACTIVE` | Enable non-interactive mode | `1` (disabled) | `0` |
| `TT_MODE_CONTAINER` | Enable container mode | `1` (disabled) | `0` |
| `TT_SKIP_INSTALL_KMD` | Skip KMD installation | `1` (install) | `0` |
| `TT_SKIP_INSTALL_HUGEPAGES` | Skip HugePages setup | `1` (install) | `0` |
| `TT_SKIP_UPDATE_FIRMWARE` | Skip firmware update | `1` (install) | `0` |
| `TT_SKIP_INSTALL_PODMAN` | Skip Podman installation | `1` (install) | `0` |
| `TT_SKIP_INSTALL_METALIUM_CONTAINER` | Skip Metalium installation | `1` (install) | `0` |
| `TT_PYTHON_CHOICE` | Python environment option | `2` (new venv) | `1`, `3`, `4` |
| `TT_NEW_VENV_LOCATION` | Custom venv path | `$HOME/.tenstorrent-venv` | `/opt/ttvenv` |
| `TT_REBOOT_OPTION` | Reboot behavior | `1` (ask user) | `2`, `3` |
| `TT_KMD_VERSION` | KMD version | Latest | `0.8.0` |
| `TT_FW_VERSION` | Firmware version | Latest | `1.5.0` |
| `TT_SYSTOOLS_VERSION` | System tools version | Latest | `1.0.0` |
| `TT_METALIUM_IMAGE_URL` | Metalium image URL | `ghcr.io/tenstorrent/tt-metal/tt-metalium-ubuntu-22.04-release-amd64` | Custom URL |
| `TT_METALIUM_IMAGE_TAG` | Metalium image tag | `latest-rc` | `v1.0` |

Sources: [install.sh 44-113](https://github.com/tenstorrent/tt-installer/blob/19fdd6a2/install.sh#L44-L113)

## Combining Configuration Options

Multiple configuration options can be combined to customize the installation process. Here are some common combination examples:

### Fully Automated Installation with Reboot

`TT_MODE_NON_INTERACTIVE=0 TT_PYTHON_CHOICE=2 TT_REBOOT_OPTION=3 ./install.sh`
### Minimal Installation (Only Driver and Tools)

`TT_SKIP_UPDATE_FIRMWARE=0 TT_SKIP_INSTALL_PODMAN=0 TT_SKIP_INSTALL_METALIUM_CONTAINER=0 ./install.sh`
### Custom Component Versions with System Python

`TT_PYTHON_CHOICE=3 TT_KMD_VERSION=0.8.0 TT_FW_VERSION=1.5.0 ./install.sh`

Dismiss
Refresh this wiki

Enter email to refresh
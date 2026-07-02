---
project: tt-installer
github: https://github.com/tenstorrent/tt-installer
deepwiki: https://deepwiki.com/tenstorrent/tt-installer/1-overview)
---

# Overview

Relevant source files
*   [README.md](https://github.com/tenstorrent/tt-installer/blob/19fdd6a2/README.md?plain=1)
*   [install.sh](https://github.com/tenstorrent/tt-installer/blob/19fdd6a2/install.sh)

The Tenstorrent Installer (`tt-installer`) is an automated installation system for the Tenstorrent software stack. It provides a streamlined approach to set up all necessary components required to work with Tenstorrent AI hardware through a single, configurable installation script.

This overview introduces the purpose, architecture, and key components of the installer system. For detailed installation instructions, see [Installation Guide](https://deepwiki.com/tenstorrent/tt-installer/2-installation-guide). For in-depth information about the system architecture, see [Installation Architecture](https://deepwiki.com/tenstorrent/tt-installer/3-installation-architecture).

## Purpose and Scope

The `tt-installer` repository centralizes the installation process for the complete Tenstorrent software ecosystem by:

1.   Installing the Tenstorrent Kernel-Mode Driver (KMD) for hardware communication
2.   Updating device firmware using tt-flash
3.   Configuring HugePages for optimized memory management
4.   Installing system management tools (tt-smi)
5.   Setting up the development environment (via tt-metalium container)

The installer supports multiple operating systems and provides various configuration options to customize the installation according to user needs.

Sources: [install.sh 392-407](https://github.com/tenstorrent/tt-installer/blob/19fdd6a2/install.sh#L392-L407)[README.md 1-3](https://github.com/tenstorrent/tt-installer/blob/19fdd6a2/README.md?plain=1#L1-L3)

## Key Components

| Component | Purpose | Source Repository |
| --- | --- | --- |
| Kernel-Mode Driver (KMD) | Low-level driver enabling communication with Tenstorrent hardware | [https://github.com/tenstorrent/tt-kmd](https://github.com/tenstorrent/tt-kmd) |
| tt-flash | Tool for updating device firmware | [https://github.com/tenstorrent/tt-flash](https://github.com/tenstorrent/tt-flash) |
| HugePages Configuration | Memory management setup for optimal performance | tenstorrent-hugepages.service |
| tt-smi | System Management Interface for hardware monitoring | [https://github.com/tenstorrent/tt-smi](https://github.com/tenstorrent/tt-smi) |
| tt-metalium | Containerized development environment for AI model building | tt-metalium container |

Sources: [install.sh 18-42](https://github.com/tenstorrent/tt-installer/blob/19fdd6a2/install.sh#L18-L42)[README.md 22-32](https://github.com/tenstorrent/tt-installer/blob/19fdd6a2/README.md?plain=1#L22-L32)

## Installation System Architecture

The diagram below illustrates how the components installed by `tt-installer` work together:

Sources: [install.sh 18-42](https://github.com/tenstorrent/tt-installer/blob/19fdd6a2/install.sh#L18-L42)[install.sh 44-50](https://github.com/tenstorrent/tt-installer/blob/19fdd6a2/install.sh#L44-L50)[install.sh 268-354](https://github.com/tenstorrent/tt-installer/blob/19fdd6a2/install.sh#L268-L354)[README.md 22-32](https://github.com/tenstorrent/tt-installer/blob/19fdd6a2/README.md?plain=1#L22-L32)

## Installation Process Flow

The following diagram shows the sequence of operations performed by the installer:

Sources: [install.sh 392-656](https://github.com/tenstorrent/tt-installer/blob/19fdd6a2/install.sh#L392-L656)[install.sh 169-195](https://github.com/tenstorrent/tt-installer/blob/19fdd6a2/install.sh#L169-L195)

## Configuration Options

The `tt-installer` system provides extensive configuration through environment variables:

| Environment Variable | Default | Description |
| --- | --- | --- |
| TT_SKIP_INSTALL_KMD | 1 | Set to 0 to skip KMD installation |
| TT_SKIP_INSTALL_HUGEPAGES | 1 | Set to 0 to skip HugePages setup |
| TT_SKIP_UPDATE_FIRMWARE | 1 | Set to 0 to skip firmware update |
| TT_SKIP_INSTALL_PODMAN | 1 | Set to 0 to skip Podman installation |
| TT_SKIP_INSTALL_METALIUM_CONTAINER | 1 | Set to 0 to skip Metalium container |
| TT_KMD_VERSION | Latest | Specify KMD version to install |
| TT_FW_VERSION | Latest | Specify firmware version to install |
| TT_SYSTOOLS_VERSION | Latest | Specify system tools version |
| TT_PYTHON_CHOICE | 2 | Python installation method (1-4) |
| TT_REBOOT_OPTION | 1 | Reboot behavior (1=Ask, 2=Never, 3=Always) |
| TT_MODE_CONTAINER | 1 | Set to 0 for container mode |
| TT_MODE_NON_INTERACTIVE | 1 | Set to 0 for non-interactive mode |

Sources: [install.sh 52-113](https://github.com/tenstorrent/tt-installer/blob/19fdd6a2/install.sh#L52-L113)[install.sh 224-265](https://github.com/tenstorrent/tt-installer/blob/19fdd6a2/install.sh#L224-L265)[README.md 35-55](https://github.com/tenstorrent/tt-installer/blob/19fdd6a2/README.md?plain=1#L35-L55)

## Operating System Compatibility

The installer supports multiple Linux distributions with varying levels of compatibility:

| OS | Version | Support Level | Notes |
| --- | --- | --- | --- |
| Ubuntu | 24.04.2 LTS | Full | None |
| Ubuntu | 22.04.5 LTS | Full | None |
| Ubuntu | 20.04.6 LTS | Limited | Deprecated, no Metalium support |
| Debian | 12.10.0 | Limited | Requires manual Rust installation |
| Fedora | 41/42 | Limited | May require restart after base package install |
| Other DEB-based | N/A | Unsupported | May work but unsupported |
| Other RPM-based | N/A | Unsupported | May work but unsupported |

Sources: [install.sh 176-195](https://github.com/tenstorrent/tt-installer/blob/19fdd6a2/install.sh#L176-L195)[install.sh 434-473](https://github.com/tenstorrent/tt-installer/blob/19fdd6a2/install.sh#L434-L473)[README.md 59-72](https://github.com/tenstorrent/tt-installer/blob/19fdd6a2/README.md?plain=1#L59-L72)

## Quickstart Usage

The simplest way to use the installer is with a single command:

`/bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/tenstorrent/tt-installer/refs/heads/main/install.sh)"`
For advanced usage and customization options, see [Configuration Options](https://deepwiki.com/tenstorrent/tt-installer/4-configuration-options).

Sources: [README.md 4-8](https://github.com/tenstorrent/tt-installer/blob/19fdd6a2/README.md?plain=1#L4-L8)

The `tt-installer` provides a comprehensive solution for setting up all components needed to work with Tenstorrent hardware, from low-level drivers to high-level development tools, with flexibility to adapt to different user requirements and operating system environments.

Dismiss
Refresh this wiki

Enter email to refresh
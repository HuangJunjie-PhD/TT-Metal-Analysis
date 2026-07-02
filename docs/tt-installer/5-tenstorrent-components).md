---
project: tt-installer
github: https://github.com/tenstorrent/tt-installer
deepwiki: https://deepwiki.com/tenstorrent/tt-installer/5-tenstorrent-components)
---

# Tenstorrent Components

Relevant source files
*   [README.md](https://github.com/tenstorrent/tt-installer/blob/19fdd6a2/README.md?plain=1)
*   [install.sh](https://github.com/tenstorrent/tt-installer/blob/19fdd6a2/install.sh)

## Purpose and Scope

This page provides an overview of the core Tenstorrent software components installed by the tt-installer. It covers what each component does, how they interact with each other, and their role in the Tenstorrent software stack. For installation instructions, see [Installation Guide](https://deepwiki.com/tenstorrent/tt-installer/2-installation-guide), and for a detailed explanation of the installation architecture, see [Installation Architecture](https://deepwiki.com/tenstorrent/tt-installer/3-installation-architecture).

## Component Overview

The Tenstorrent software stack comprises several key components that enable interaction with Tenstorrent hardware:

Sources: [install.sh 18-68](https://github.com/tenstorrent/tt-installer/blob/19fdd6a2/install.sh#L18-L68)[README.md 22-32](https://github.com/tenstorrent/tt-installer/blob/19fdd6a2/README.md?plain=1#L22-L32)

## Kernel-Mode Driver (KMD)

The Kernel-Mode Driver (KMD) is the fundamental component that enables communication between the operating system and Tenstorrent hardware devices. It is installed as a DKMS (Dynamic Kernel Module Support) module.

### Features

*   Provides low-level hardware access
*   Manages device initialization and configuration
*   Handles memory mapping between host and device
*   Enables communication with the Tenstorrent hardware

### Installation Details

The KMD is installed from the [tenstorrent/tt-kmd](https://github.com/tenstorrent/tt-installer/blob/19fdd6a2/tenstorrent/tt-kmd) repository. The installer:

1.   Checks for an existing KMD installation
2.   Clones the appropriate version of KMD based on the version string
3.   Installs the driver using DKMS
4.   Loads the module with `modprobe tenstorrent`

Sources: [install.sh 528-553](https://github.com/tenstorrent/tt-installer/blob/19fdd6a2/install.sh#L528-L553)

## Firmware and tt-flash

The `tt-flash` utility manages firmware updates for Tenstorrent devices. Firmware updates provide bug fixes, performance improvements, and new features for the hardware.

### tt-flash Utility

*   Python-based firmware management tool
*   Installed from [tenstorrent/tt-flash](https://github.com/tenstorrent/tt-installer/blob/19fdd6a2/tenstorrent/tt-flash)
*   Used to update firmware on Tenstorrent devices

### Firmware Update Process

### Firmware Sources

Firmware bundles are downloaded from the [tenstorrent/tt-firmware](https://github.com/tenstorrent/tt-installer/blob/19fdd6a2/tenstorrent/tt-firmware) repository. The installer downloads the appropriate firmware version and uses tt-flash to apply it.

Sources: [install.sh 555-571](https://github.com/tenstorrent/tt-installer/blob/19fdd6a2/install.sh#L555-L571)[install.sh 28-34](https://github.com/tenstorrent/tt-installer/blob/19fdd6a2/install.sh#L28-L34)

## HugePages Configuration

HugePages are a memory management feature that improves performance when working with large memory allocations. They are essential for optimal performance with Tenstorrent hardware.

### Purpose

*   Reduces TLB (Translation Lookaside Buffer) misses
*   Improves memory access performance
*   Required for efficient data transfer to and from Tenstorrent devices

### Configuration

The installer sets up HugePages by:

1.   Installing the tenstorrent-tools package
2.   Configuring and enabling the tenstorrent-hugepages systemd service
3.   Enabling the HugePages mount point at /dev/hugepages-1G

Sources: [install.sh 573-603](https://github.com/tenstorrent/tt-installer/blob/19fdd6a2/install.sh#L573-L603)[README.md 28](https://github.com/tenstorrent/tt-installer/blob/19fdd6a2/README.md?plain=1#L28-L28)

## System Management Interface (tt-smi)

The System Management Interface (tt-smi) is a utility for monitoring and managing Tenstorrent devices.

### Features

*   Displays hardware status and information
*   Shows temperature, power, and utilization data
*   Provides device enumeration and identification
*   Helps verify successful installation and operation

### Usage

The tt-smi utility is installed from the [tenstorrent/tt-smi](https://github.com/tenstorrent/tt-installer/blob/19fdd6a2/tenstorrent/tt-smi) repository.

Sources: [install.sh 605-607](https://github.com/tenstorrent/tt-installer/blob/19fdd6a2/install.sh#L605-L607)[README.md 16-20](https://github.com/tenstorrent/tt-installer/blob/19fdd6a2/README.md?plain=1#L16-L20)

## tt-metalium Container

The tt-metalium container provides a complete development environment for building and running AI models on Tenstorrent hardware.

### Container Architecture

### Installation Details

The tt-metalium container is installed via Podman with the following:

1.   A wrapper script is created at `~/.local/bin/tt-metalium`
2.   The container image is pulled from `ghcr.io/tenstorrent/tt-metal/tt-metalium-ubuntu-22.04-release-amd64`
3.   The wrapper script configures proper device and volume mounts

### Usage

The wrapper script enables easy access to the container environment:

Sources: [install.sh 309-353](https://github.com/tenstorrent/tt-installer/blob/19fdd6a2/install.sh#L309-L353)[README.md 10-13](https://github.com/tenstorrent/tt-installer/blob/19fdd6a2/README.md?plain=1#L10-L13)[install.sh 637-643](https://github.com/tenstorrent/tt-installer/blob/19fdd6a2/install.sh#L637-L643)

## Component Dependencies and Interactions

The following diagram illustrates the dependencies between Tenstorrent components:

Sources: [install.sh 392-656](https://github.com/tenstorrent/tt-installer/blob/19fdd6a2/install.sh#L392-L656)[README.md 22-32](https://github.com/tenstorrent/tt-installer/blob/19fdd6a2/README.md?plain=1#L22-L32)

## Version Management

Each component has its own version that is managed independently:

| Component | Source Repository | Version Determination |
| --- | --- | --- |
| KMD | github.com/tenstorrent/tt-kmd | Environment variable or latest Git tag |
| Firmware | github.com/tenstorrent/tt-firmware | Environment variable or latest Git tag |
| System Tools | github.com/tenstorrent/tt-system-tools | Environment variable or latest Git tag |
| tt-flash | github.com/tenstorrent/tt-flash | Latest Git repository version |
| tt-smi | github.com/tenstorrent/tt-smi | Latest Git repository version |
| tt-metalium | ghcr.io/tenstorrent/tt-metal/tt-metalium-* | Latest or specified tag |

Sources: [install.sh 18-42](https://github.com/tenstorrent/tt-installer/blob/19fdd6a2/install.sh#L18-L42)[install.sh 70-74](https://github.com/tenstorrent/tt-installer/blob/19fdd6a2/install.sh#L70-L74)

## Installation Skip Flags

The installer allows selectively skipping components through environment variables:

| Environment Variable | Default | Component |
| --- | --- | --- |
| TT_SKIP_INSTALL_KMD | 1 (install) | Kernel-Mode Driver |
| TT_SKIP_UPDATE_FIRMWARE | 1 (install) | Firmware and tt-flash |
| TT_SKIP_INSTALL_HUGEPAGES | 1 (install) | HugePages Configuration |
| TT_SKIP_INSTALL_PODMAN | 1 (install) | Podman |
| TT_SKIP_INSTALL_METALIUM_CONTAINER | 1 (install) | tt-metalium Container |

Sources: [install.sh 53-67](https://github.com/tenstorrent/tt-installer/blob/19fdd6a2/install.sh#L53-L67)[install.sh 414-426](https://github.com/tenstorrent/tt-installer/blob/19fdd6a2/install.sh#L414-L426)

For more detailed information about individual components, see the specific wiki pages:

*   [Kernel-Mode Driver (KMD)](https://deepwiki.com/tenstorrent/tt-installer/5.1-kernel-mode-driver-(kmd))
*   [Firmware and tt-flash](https://deepwiki.com/tenstorrent/tt-installer/5.2-firmware-and-tt-flash)
*   [HugePages Configuration](https://deepwiki.com/tenstorrent/tt-installer/5.3-hugepages-configuration)
*   [System Management Interface (tt-smi)](https://deepwiki.com/tenstorrent/tt-installer/5.4-system-management-interface-(tt-smi))
*   [tt-metalium Container](https://deepwiki.com/tenstorrent/tt-installer/5.5-tt-metalium-container)

Dismiss
Refresh this wiki

Enter email to refresh
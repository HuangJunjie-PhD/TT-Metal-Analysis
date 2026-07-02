---
project: tt-firmware
github: https://github.com/tenstorrent/tt-firmware
deepwiki: https://deepwiki.com/tenstorrent/tt-firmware/1-overview)
---

# Overview

Relevant source files
*   [README.md](https://github.com/tenstorrent/tt-firmware/blob/c9a325bb/README.md?plain=1)
*   [SUMMARY.md](https://github.com/tenstorrent/tt-firmware/blob/c9a325bb/SUMMARY.md?plain=1)

This page provides a technical overview of the tt-firmware repository, which contains the firmware that powers Tenstorrent's AI accelerator devices. This document covers the firmware's purpose, architecture, and high-level components. For specific board support details, see [Supported Boards](https://deepwiki.com/tenstorrent/tt-firmware/1.1-supported-boards), and for licensing information, see [License and Usage Restrictions](https://deepwiki.com/tenstorrent/tt-firmware/1.2-license-and-usage-restrictions).

## Purpose of tt-firmware

The tt-firmware repository contains firmware for the system management processor (SMP) in Tenstorrent devices. This processor handles critical system operations that are essential for hardware functionality and reliability.

The system management processor firmware is responsible for three primary functions:

1.   **Hardware Initialization**: Configuring and powering up the hardware components
2.   **I/O Connectivity**: Establishing and maintaining external connections (PCIe, Ethernet, DRAM)
3.   **Power and Thermal Management**: Ensuring the device operates within safe power and temperature limits

Sources: [README.md 8-13](https://github.com/tenstorrent/tt-firmware/blob/c9a325bb/README.md?plain=1#L8-L13)

## Repository Structure

The tt-firmware repository provides binary firmware bundles rather than source code due to third-party IP constraints. The repository is organized as follows:

The main components of the repository are:

*   **Official Firmware Bundles**: Stable, tested firmware releases
*   **Experimental Bundles**: Modified firmware to address specific issues
*   **Documentation**: User guides, release notes, and migration information

Sources: [README.md 23-27](https://github.com/tenstorrent/tt-firmware/blob/c9a325bb/README.md?plain=1#L23-L27)[README.md 62-65](https://github.com/tenstorrent/tt-firmware/blob/c9a325bb/README.md?plain=1#L62-L65)

## Firmware Distribution Model

Unlike many open-source projects, tt-firmware is distributed as binary releases rather than source code. This approach is necessitated by agreements with third-party IP providers used in Tenstorrent products.

The firmware bundles include support for multiple board types in a single package. For application interactions with the firmware, see the [tt-umd](https://github.com/tenstorrent/tt-firmware/blob/c9a325bb/tt-umd) repository.

Sources: [README.md 15-21](https://github.com/tenstorrent/tt-firmware/blob/c9a325bb/README.md?plain=1#L15-L21)

## Supported Hardware

The tt-firmware supports three main types of Tenstorrent hardware:

| Board Name | Codename | Description |
| --- | --- | --- |
| Grayskull | gs | First generation AI accelerator |
| Wormhole | wh | Second generation AI accelerator |
| Blackhole | bh | Latest generation AI accelerator with enhanced features |

Each board type has specific firmware components with hardware-specific optimizations. The current firmware bundle (18.2.0.0) supports all three board types.

Sources: [README.md 27](https://github.com/tenstorrent/tt-firmware/blob/c9a325bb/README.md?plain=1#L27-L27)

## System Architecture

The firmware operates within a larger system context, managing the hardware while interfacing with the host system through the User Mode Driver (UMD).

The firmware provides multiple interfaces:

*   **Hardware Management**: Direct control of the device hardware
*   **Host Interface**: Communication with the host system via the UMD
*   **Debug Interface**: Monitoring and debugging through the Virtual UART (particularly in Blackhole)

Sources: [README.md 8-13](https://github.com/tenstorrent/tt-firmware/blob/c9a325bb/README.md?plain=1#L8-L13)[README.md 35-42](https://github.com/tenstorrent/tt-firmware/blob/c9a325bb/README.md?plain=1#L35-L42)

## Recent Enhancements

The latest firmware release (18.2.0.0) includes several significant enhancements, particularly for Blackhole boards:

1.   **ERISC Firmware v1.4.0**:

    *   Enhanced Ethernet mailbox functionality
    *   Link status monitoring
    *   Core release control for executing functions at specified addresses

2.   **Virtual UART**:

    *   In-memory debugging interface
    *   Accessible via `tt-console` from the host
    *   Captures `printk()` and `LOG_*()` messages

3.   **Stability Improvements**:

    *   Enhanced link training sequence
    *   Fixed synchronization issues
    *   Improved SMC I2C recovery function
    *   PCIe Maximum Payload Size optimization

Sources: [README.md 31-49](https://github.com/tenstorrent/tt-firmware/blob/c9a325bb/README.md?plain=1#L31-L49)

## Firmware Components and Interfaces

The firmware provides several key components that interact with different parts of the hardware and software stack:

The firmware components handle various responsibilities:

*   **Board-specific firmware**: Tailored implementations for each hardware type
*   **ERISC firmware**: Specialized processing for Blackhole boards
*   **System management**: Core hardware control and management
*   **Debugging interfaces**: Tools for monitoring and troubleshooting

Sources: [README.md 31-49](https://github.com/tenstorrent/tt-firmware/blob/c9a325bb/README.md?plain=1#L31-L49)

## Relationship to Other Systems

The tt-firmware operates within a broader ecosystem of Tenstorrent software and hardware:

| Component | Relationship to tt-firmware |
| --- | --- |
| tt-umd | User Mode Driver that interacts with the firmware |
| Hardware | Direct control and management of the physical device |
| Host Applications | Indirect interaction through the UMD layer |
| tt-console | Debug tool for accessing Virtual UART messages |

For detailed information on firmware architecture, refer to [Firmware Architecture](https://deepwiki.com/tenstorrent/tt-firmware/2-firmware-architecture), and for specifics on firmware bundles, see [Firmware Bundles](https://deepwiki.com/tenstorrent/tt-firmware/3-firmware-bundles).

Sources: [README.md 21](https://github.com/tenstorrent/tt-firmware/blob/c9a325bb/README.md?plain=1#L21-L21)[README.md 39-41](https://github.com/tenstorrent/tt-firmware/blob/c9a325bb/README.md?plain=1#L39-L41)

## Versioning and Updates

The firmware uses a versioning scheme with major, minor, and patch versions. When migrating between versions, specific guides are provided to ensure compatibility and proper operation.

The current main version is 18.2.0.0, which supersedes the previous 80.18.1.0 version. Migration guides detail the required and recommended changes when upgrading.

Sources: [README.md 31-33](https://github.com/tenstorrent/tt-firmware/blob/c9a325bb/README.md?plain=1#L31-L33)[README.md 51-53](https://github.com/tenstorrent/tt-firmware/blob/c9a325bb/README.md?plain=1#L51-L53)

## Experimental Variants

Beyond the official releases, the repository contains experimental firmware bundles in the `experiments` directory. These variants are modified versions of the standard firmware, designed to address specific issues or test particular features.

Users requiring solutions to specific problems may find these experimental builds useful, though they may not undergo the same level of testing as official releases.

Sources: [README.md 62-65](https://github.com/tenstorrent/tt-firmware/blob/c9a325bb/README.md?plain=1#L62-L65)

Dismiss
Refresh this wiki

Enter email to refresh
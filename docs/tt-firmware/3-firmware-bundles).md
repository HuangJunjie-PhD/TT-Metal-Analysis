---
project: tt-firmware
github: https://github.com/tenstorrent/tt-firmware
deepwiki: https://deepwiki.com/tenstorrent/tt-firmware/3-firmware-bundles)
---

# Firmware Bundles

Relevant source files
*   [README.md](https://github.com/tenstorrent/tt-firmware/blob/c9a325bb/README.md?plain=1)
*   [fw_pack-18.2.0.0.fwbundle](https://github.com/tenstorrent/tt-firmware/blob/c9a325bb/fw_pack-18.2.0.0.fwbundle)
*   [latest.fwbundle](https://github.com/tenstorrent/tt-firmware/blob/c9a325bb/latest.fwbundle)

## Overview

This document provides comprehensive information about firmware bundles in the tt-firmware repository. Firmware bundles are binary packages containing firmware images for Tenstorrent hardware devices. They enable the functionality of the system management processor, which is responsible for hardware initialization, I/O connectivity management, and power/thermal regulation.

For information about specific official releases, see [Official Releases](https://deepwiki.com/tenstorrent/tt-firmware/3.1-official-releases). For experimental bundle variants, see [Experimental Bundles](https://deepwiki.com/tenstorrent/tt-firmware/3.2-experimental-bundles). For details on versioning and migration between firmware versions, see [Versioning and Migration](https://deepwiki.com/tenstorrent/tt-firmware/3.3-versioning-and-migration).

Sources: [README.md 23-27](https://github.com/tenstorrent/tt-firmware/blob/c9a325bb/README.md?plain=1#L23-L27)

## What are Firmware Bundles?

Firmware bundles are packaged binary distributions of the firmware required to operate Tenstorrent hardware devices. Due to third-party IP restrictions, firmware is distributed in bundle format rather than as source code. Each bundle contains firmware images for one or more Tenstorrent hardware platforms (Grayskull, Wormhole, and Blackhole).

Title: Firmware Bundle Component Structure

Sources: [README.md 6-13](https://github.com/tenstorrent/tt-firmware/blob/c9a325bb/README.md?plain=1#L6-L13)[README.md 15-19](https://github.com/tenstorrent/tt-firmware/blob/c9a325bb/README.md?plain=1#L15-L19)[README.md 27](https://github.com/tenstorrent/tt-firmware/blob/c9a325bb/README.md?plain=1#L27-L27)

## Bundle Format and Naming Convention

Firmware bundles use the `.fwbundle` file extension. The naming convention follows the pattern:

```
fw_pack-[version].fwbundle
```

Where `[version]` follows a versioning scheme as demonstrated by examples such as `fw_pack-18.2.0.0.fwbundle` and `fw_pack-80.18.1.0.fwbundle`. A symbolic link named `latest.fwbundle` typically points to the most recent stable release.

For experimental bundles, additional identifiers may be included in the filename to indicate the specific modifications or target issues being addressed.

| Bundle Name Example | Description |
| --- | --- |
| `fw_pack-18.2.0.0.fwbundle` | Official firmware bundle containing images for Grayskull, Wormhole, and Blackhole boards |
| `latest.fwbundle` | Symbolic link to the most current stable firmware bundle |
| `experiments/experimental-bundle-name.fwbundle` | Modified firmware with specific issue fixes or experimental features |

Sources: [README.md 27](https://github.com/tenstorrent/tt-firmware/blob/c9a325bb/README.md?plain=1#L27-L27)[README.md 33](https://github.com/tenstorrent/tt-firmware/blob/c9a325bb/README.md?plain=1#L33-L33)

## Types of Firmware Bundles

There are two main categories of firmware bundles:

### Official Firmware Bundles

These are stable, tested firmware releases that are recommended for production use. They are located in the root directory of the repository and are thoroughly validated before release. Each official release includes comprehensive release notes detailing new features, improvements, and bug fixes.

Title: Official Firmware Bundle Creation and Distribution Process

Sources: [README.md 27](https://github.com/tenstorrent/tt-firmware/blob/c9a325bb/README.md?plain=1#L27-L27)[README.md 29-59](https://github.com/tenstorrent/tt-firmware/blob/c9a325bb/README.md?plain=1#L29-L59)

### Experimental Firmware Bundles

These bundles contain modifications to address specific issues or test new features. They are located in the `experiments` directory and are derived from official releases with targeted changes. Each experimental bundle includes documentation explaining its purpose and modifications.

Title: Experimental Firmware Bundle Development Process

Sources: [README.md 61-66](https://github.com/tenstorrent/tt-firmware/blob/c9a325bb/README.md?plain=1#L61-L66)

## Bundle Contents and Features

Firmware bundles contain platform-specific firmware that provides the following key functionalities:

1.   **Hardware Initialization**: Responsible for powering on the hardware and performing initial setup

2.   **I/O Connectivity Management**: Manages external connections including:

    *   PCIe interfaces
    *   Ethernet connectivity
    *   DRAM interfaces
    *   Other peripheral connections

3.   **Power and Thermal Management**: Ensures operation within power and temperature design parameters

4.   **Platform-Specific Components**: Each supported hardware platform may have unique firmware components:

    *   **Blackhole-specific features**: 
        *   ERISC Firmware (v1.4.0+)
        *   ETH mailbox functionality
        *   Virtual UART for debugging and observability

Title: Firmware Bundle Functional Component Map

Sources: [README.md 8-13](https://github.com/tenstorrent/tt-firmware/blob/c9a325bb/README.md?plain=1#L8-L13)[README.md 34-42](https://github.com/tenstorrent/tt-firmware/blob/c9a325bb/README.md?plain=1#L34-L42)

## Current Bundle Features

The current firmware bundle (`fw_pack-18.2.0.0.fwbundle`) includes several important features:

| Feature | Description |
| --- | --- |
| **Combined Image** | Single bundle that supports Grayskull, Wormhole, and Blackhole boards |
| **Blackhole ERISC FW v1.4.0** | Updated firmware with improved link training and stability |
| **ETH Mailbox** | Added functionality with messages for link status checking and core release |
| **Virtual UART** | Enabled by default for Blackhole firmware for observability and debugging |
| **SMC I2C Recovery** | Improved recovery function with 99.6% reset and re-enumeration success rate |
| **PCIe MPS Configuration** | Now set by TT-KMD for improved VM stability |

Sources: [README.md 27](https://github.com/tenstorrent/tt-firmware/blob/c9a325bb/README.md?plain=1#L27-L27)[README.md 33-49](https://github.com/tenstorrent/tt-firmware/blob/c9a325bb/README.md?plain=1#L33-L49)

## System Integration

Firmware bundles integrate with the overall Tenstorrent hardware and software ecosystem. Applications interact with the firmware through the User Mode Driver (UMD), which communicates with the System Management Processor.

Title: Firmware Bundle System Integration

Sources: [README.md 21](https://github.com/tenstorrent/tt-firmware/blob/c9a325bb/README.md?plain=1#L21-L21)[README.md 34-42](https://github.com/tenstorrent/tt-firmware/blob/c9a325bb/README.md?plain=1#L34-L42)

## Usage Scenarios

Firmware bundles are used in the following primary scenarios:

1.   **Initial Hardware Setup**: When first deploying Tenstorrent hardware, the firmware bundle provides the necessary initialization code

2.   **Firmware Updates**: When upgrading to newer firmware versions to access bug fixes, performance improvements, or new features

3.   **Experimental Feature Testing**: When evaluating experimental features or testing specific issue fixes using bundles from the `experiments` directory

4.   **Development and Debugging**: When using debug features like the Virtual UART (with `tt-console`) to observe firmware behavior

Sources: [README.md 34-42](https://github.com/tenstorrent/tt-firmware/blob/c9a325bb/README.md?plain=1#L34-L42)[README.md 61-66](https://github.com/tenstorrent/tt-firmware/blob/c9a325bb/README.md?plain=1#L61-L66)

## Migration Between Versions

When migrating between firmware versions, users should refer to the migration guide for the specific version they are upgrading to. For example, migration from v80.18.1 to v18.2.0 is documented in the v18.2.0 Migration Guide.

Important changes between versions may include:

*   New features enabled by default
*   Modified behavior requiring application changes
*   Performance improvements
*   Bug fixes

For detailed migration instructions and comprehensive changelog information, refer to [Versioning and Migration](https://deepwiki.com/tenstorrent/tt-firmware/3.3-versioning-and-migration).

Sources: [README.md 51-59](https://github.com/tenstorrent/tt-firmware/blob/c9a325bb/README.md?plain=1#L51-L59)

Dismiss
Refresh this wiki

Enter email to refresh
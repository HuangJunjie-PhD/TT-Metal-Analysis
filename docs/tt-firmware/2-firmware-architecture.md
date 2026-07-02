---
project: tt-firmware
github: https://github.com/tenstorrent/tt-firmware
deepwiki: https://deepwiki.com/tenstorrent/tt-firmware/2-firmware-architecture
---

# Firmware Architecture

Relevant source files
*   [README.md](https://github.com/tenstorrent/tt-firmware/blob/c9a325bb/README.md?plain=1)
*   [latest.fwbundle](https://github.com/tenstorrent/tt-firmware/blob/c9a325bb/latest.fwbundle)

This document provides a detailed overview of the internal architecture and components of the Tenstorrent firmware. It explains the primary functional components and their interactions that enable hardware initialization, I/O connectivity management, and operational monitoring. For specific details about the System Management Processor, see [System Management Processor](https://deepwiki.com/tenstorrent/tt-firmware/2.1-system-management-processor). For I/O connectivity details, see [I/O Connectivity](https://deepwiki.com/tenstorrent/tt-firmware/2.2-io-connectivity). For information about power regulation and thermal control, see [Power and Thermal Management](https://deepwiki.com/tenstorrent/tt-firmware/2.3-power-and-thermal-management).

## Firmware Responsibilities

The Tenstorrent firmware serves as the foundational software layer responsible for:

1.   Hardware initialization and configuration
2.   External I/O management (PCIe, Ethernet, DRAM)
3.   Power envelope maintenance and thermal regulation
4.   Hardware state monitoring and management

Sources: [README.md 7-13](https://github.com/tenstorrent/tt-firmware/blob/c9a325bb/README.md?plain=1#L7-L13)

## Architectural Overview

### Functional Architecture Diagram

Sources: [README.md 8-13](https://github.com/tenstorrent/tt-firmware/blob/c9a325bb/README.md?plain=1#L8-L13)[README.md 35-42](https://github.com/tenstorrent/tt-firmware/blob/c9a325bb/README.md?plain=1#L35-L42)

## Core Firmware Components

### System Management Processor

The System Management Processor (SMP) serves as the central control unit of the firmware. It orchestrates hardware initialization, manages I/O connectivity, and ensures power and thermal envelopes are maintained. The SMP acts as the primary interface between the host system and the Tenstorrent hardware.

For detailed information about the System Management Processor's architecture and implementation, see [System Management Processor](https://deepwiki.com/tenstorrent/tt-firmware/2.1-system-management-processor).

Sources: [README.md 8-9](https://github.com/tenstorrent/tt-firmware/blob/c9a325bb/README.md?plain=1#L8-L9)

### Hardware Initialization Components

The hardware initialization components are responsible for bringing the Tenstorrent hardware from a power-off state to an operational state. This includes configuring hardware registers, initializing memory systems, and preparing I/O interfaces for communication.

Sources: [README.md 10](https://github.com/tenstorrent/tt-firmware/blob/c9a325bb/README.md?plain=1#L10-L10)

### I/O Connectivity Management

I/O connectivity management encompasses the firmware components that establish and maintain connections with external interfaces, including:

*   PCIe interface management
*   Ethernet connectivity
*   DRAM initialization and control

For detailed information about I/O connectivity implementation, see [I/O Connectivity](https://deepwiki.com/tenstorrent/tt-firmware/2.2-io-connectivity).

Sources: [README.md 11](https://github.com/tenstorrent/tt-firmware/blob/c9a325bb/README.md?plain=1#L11-L11)

### Power and Thermal Management

Power and thermal management components monitor and regulate power consumption and temperature to ensure the device operates within safe parameters. These components implement throttling mechanisms and power-saving features to protect hardware from damage due to overheating or power excursions.

For detailed information about power and thermal management implementation, see [Power and Thermal Management](https://deepwiki.com/tenstorrent/tt-firmware/2.3-power-and-thermal-management).

Sources: [README.md 12](https://github.com/tenstorrent/tt-firmware/blob/c9a325bb/README.md?plain=1#L12-L12)

## Board-Specific Implementations

The firmware architecture includes specialized components for each supported board type:

### Grayskull-Specific Components

Grayskull-specific firmware components handle the unique hardware characteristics of Grayskull boards, including specialized initialization sequences and board-specific I/O configurations.

### Wormhole-Specific Components

Wormhole-specific firmware components address the unique requirements of Wormhole boards, with adaptations for Wormhole-specific hardware configurations.

### Blackhole-Specific Components

Blackhole firmware includes several specialized components:

1.   **ERISC Firmware v1.4.0**: Embedded RISC processor firmware that manages low-level hardware functions
2.   **ETH Mailbox**: Communication mechanism with two primary message types: 
    *   `LINK_STATUS_CHECK`: Monitors Ethernet link status
    *   `RELEASE_CORE`: Controls RISC0 execution at specified L1 addresses

3.   **Virtual UART**: In-memory communication channel for firmware observability and debugging, accessible via the `tt-console` utility

Sources: [README.md 35-42](https://github.com/tenstorrent/tt-firmware/blob/c9a325bb/README.md?plain=1#L35-L42)[README.md 45-49](https://github.com/tenstorrent/tt-firmware/blob/c9a325bb/README.md?plain=1#L45-L49)

## Firmware Communication Architecture

The firmware implements several communication mechanisms:

1.   **Host-to-Firmware Communication**: The User Mode Driver (tt-umd) provides the primary interface between host applications and the firmware
2.   **Debug Communication**: The Virtual UART enables debug message retrieval using the tt-console utility
3.   **Internal Communication**: Various internal mechanisms enable communication between firmware components

Sources: [README.md 21](https://github.com/tenstorrent/tt-firmware/blob/c9a325bb/README.md?plain=1#L21-L21)[README.md 39-41](https://github.com/tenstorrent/tt-firmware/blob/c9a325bb/README.md?plain=1#L39-L41)

## Firmware Security and Recovery Features

The firmware includes several features to ensure system stability and recovery:

1.   **SMC I2C Recovery**: Improves reset and re-enumeration success rates (99.6%)
2.   **Link Training Sequence**: Enhanced sequence for greater success rates on loopback cases
3.   **Synchronization Mechanisms**: Prevents potential deadlocks during device enumeration

Sources: [README.md 46-48](https://github.com/tenstorrent/tt-firmware/blob/c9a325bb/README.md?plain=1#L46-L48)

## Integration with Hardware

The firmware bridges the gap between host systems and Tenstorrent hardware through a layered approach:

This layered architecture allows the firmware to provide a consistent interface to the host system while handling the specific requirements of different hardware configurations.

Sources: [README.md 8-13](https://github.com/tenstorrent/tt-firmware/blob/c9a325bb/README.md?plain=1#L8-L13)[README.md 21](https://github.com/tenstorrent/tt-firmware/blob/c9a325bb/README.md?plain=1#L21-L21)

## Implementation Constraints

The firmware is distributed as a binary release due to third-party IP restrictions. The implementation encapsulates proprietary components while providing the necessary functionality for hardware initialization, I/O connectivity, and power/thermal management.

Sources: [README.md 15-19](https://github.com/tenstorrent/tt-firmware/blob/c9a325bb/README.md?plain=1#L15-L19)

Dismiss
Refresh this wiki

Enter email to refresh
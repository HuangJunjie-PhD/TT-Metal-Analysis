---
project: tt-firmware
github: https://github.com/tenstorrent/tt-firmware
deepwiki: https://deepwiki.com/tenstorrent/tt-firmware/4-blackhole-specific-features)
---

# Blackhole Specific Features

Relevant source files
*   [README.md](https://github.com/tenstorrent/tt-firmware/blob/c9a325bb/README.md?plain=1)

## Purpose and Scope

This page provides an overview of features specific to the Blackhole board firmware. It covers the specialized components and functionality that distinguish Blackhole firmware from other Tenstorrent boards (Grayskull and Wormhole). For information about general firmware architecture, see [Firmware Architecture](https://deepwiki.com/tenstorrent/tt-firmware/2-firmware-architecture).

## Blackhole Overview

Blackhole is one of Tenstorrent's hardware platforms supported by the tt-firmware. It features several specialized components that enhance its functionality, performance, and debug capabilities compared to other Tenstorrent boards.

Sources: [README.md 8-13](https://github.com/tenstorrent/tt-firmware/blob/c9a325bb/README.md?plain=1#L8-L13)[README.md 35-41](https://github.com/tenstorrent/tt-firmware/blob/c9a325bb/README.md?plain=1#L35-L41)

## Key Blackhole Features

Blackhole firmware includes the following key distinguishing features:

| Feature | Description | Purpose |
| --- | --- | --- |
| ERISC Firmware v1.4.0 | Embedded RISC firmware | Controls hardware initialization and communication |
| ETH Mailbox | Messaging system with specialized messages | Provides Ethernet link status and RISC0 control |
| Virtual UART | In-memory debug interface | Enables firmware observability and debugging |
| Stability Improvements | Various fixes and enhancements | Improves reliability and performance |

Sources: [README.md 35-49](https://github.com/tenstorrent/tt-firmware/blob/c9a325bb/README.md?plain=1#L35-L49)

### ERISC Firmware

The Blackhole board features ERISC Firmware v1.4.0, which is specific to this hardware platform. For detailed information about the ERISC Firmware, see [ERISC Firmware](https://deepwiki.com/tenstorrent/tt-firmware/4.1-erisc-firmware).

Key features of ERISC Firmware v1.4.0 include:

*   ETH mailbox messaging system
*   Improved link training sequence
*   Enhanced stability and reliability

Sources: [README.md 35-36](https://github.com/tenstorrent/tt-firmware/blob/c9a325bb/README.md?plain=1#L35-L36)[README.md 45-46](https://github.com/tenstorrent/tt-firmware/blob/c9a325bb/README.md?plain=1#L45-L46)

### ETH Mailbox

The ETH mailbox is a communication mechanism implemented in the ERISC Firmware v1.4.0. For detailed information about the ETH mailbox, see [ETH Mailbox](https://deepwiki.com/tenstorrent/tt-firmware/4.2-eth-mailbox).

The ETH mailbox supports two primary message types:

1.   **LINK_STATUS_CHECK**: Monitors and reports Ethernet link status
2.   **RELEASE_CORE**: Releases control of RISC0 to execute functions at specified L1 addresses

Sources: [README.md 36-38](https://github.com/tenstorrent/tt-firmware/blob/c9a325bb/README.md?plain=1#L36-L38)

### Virtual UART

The Virtual UART is an in-memory interface for firmware debugging and observability. For detailed information about the Virtual UART, see [Virtual UART](https://deepwiki.com/tenstorrent/tt-firmware/4.3-virtual-uart).

Key aspects of the Virtual UART:

*   Enabled by default on Blackhole firmware bundles
*   Creates an in-memory UART for firmware observability
*   Accessible via `tt-console` utility
*   Captures `printk()` and `LOG_*()` messages from the firmware

Sources: [README.md 39-41](https://github.com/tenstorrent/tt-firmware/blob/c9a325bb/README.md?plain=1#L39-L41)

## Stability Improvements

The Blackhole firmware includes several stability improvements that enhance reliability and performance:

### Link Training Sequence

The ERISC Firmware v1.4.0 includes an improved link training sequence that significantly increases success rates on loopback cases.

Sources: [README.md 45-46](https://github.com/tenstorrent/tt-firmware/blob/c9a325bb/README.md?plain=1#L45-L46)

### BMFW Synchronization

A synchronization issue in the Blackhole Management Firmware (BMFW) has been fixed, addressing potential deadlocks and failures to enumerate devices properly.

Sources: [README.md 47](https://github.com/tenstorrent/tt-firmware/blob/c9a325bb/README.md?plain=1#L47-L47)

### SMC I2C Recovery

The SMC I2C recovery function has been improved, resulting in reset and re-enumeration success rates of 99.6%.

Sources: [README.md 48](https://github.com/tenstorrent/tt-firmware/blob/c9a325bb/README.md?plain=1#L48-L48)

### PCIe Configuration

The PCIe Maximum Payload Size (MPS) is now set by TT-KMD, which improves virtual machine stability.

Sources: [README.md 49](https://github.com/tenstorrent/tt-firmware/blob/c9a325bb/README.md?plain=1#L49-L49)

## Firmware Integration

The following diagram shows how the Blackhole-specific features integrate with the overall firmware architecture:

Sources: [README.md 8-13](https://github.com/tenstorrent/tt-firmware/blob/c9a325bb/README.md?plain=1#L8-L13)[README.md 35-41](https://github.com/tenstorrent/tt-firmware/blob/c9a325bb/README.md?plain=1#L35-L41)

## Version Information

The Blackhole-specific features described in this document are available in firmware version 18.2.0.0 and newer. For information about versioning and migration from previous versions, see [Versioning and Migration](https://deepwiki.com/tenstorrent/tt-firmware/3.3-versioning-and-migration).

Sources: [README.md 31-42](https://github.com/tenstorrent/tt-firmware/blob/c9a325bb/README.md?plain=1#L31-L42)

Dismiss
Refresh this wiki

Enter email to refresh
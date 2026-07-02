---
project: luwen
github: https://github.com/tenstorrent/luwen
deepwiki: https://deepwiki.com/tenstorrent/luwen/2-architecture
---

# Architecture

Relevant source files
*   [Cargo.toml](https://github.com/tenstorrent/luwen/blob/55e35616/Cargo.toml)
*   [crates/luwen-if/Cargo.toml](https://github.com/tenstorrent/luwen/blob/55e35616/crates/luwen-if/Cargo.toml)
*   [crates/luwen-if/src/chip/mod.rs](https://github.com/tenstorrent/luwen/blob/55e35616/crates/luwen-if/src/chip/mod.rs)
*   [crates/luwen-ref/Cargo.toml](https://github.com/tenstorrent/luwen/blob/55e35616/crates/luwen-ref/Cargo.toml)
*   [crates/pyluwen/Cargo.toml](https://github.com/tenstorrent/luwen/blob/55e35616/crates/pyluwen/Cargo.toml)

## Purpose and Scope

This document provides a high-level overview of the Luwen architecture, which creates a unified interface for interacting with Tenstorrent AI accelerator chips. It covers the layered design, key abstractions, and component relationships that form the core of the system. For more detailed information about specific components, please refer to the dedicated sections of this documentation, such as [Core Components](https://deepwiki.com/tenstorrent/luwen/2.2-core-components) and [Communication Interfaces](https://deepwiki.com/tenstorrent/luwen/2.3-communication-interfaces).

## Layered Design

Luwen is built with a layered architecture that separates concerns and allows components to be developed and maintained independently.

Sources: [Cargo.toml 11-15](https://github.com/tenstorrent/luwen/blob/55e35616/Cargo.toml#L11-L15)[crates/pyluwen/Cargo.toml 11-15](https://github.com/tenstorrent/luwen/blob/55e35616/crates/pyluwen/Cargo.toml#L11-L15)[crates/luwen-ref/Cargo.toml 8-11](https://github.com/tenstorrent/luwen/blob/55e35616/crates/luwen-ref/Cargo.toml#L8-L11)

The layers in the architecture are:

1.   **luwen-core**: Provides fundamental types and definitions used throughout the codebase.
2.   **luwen-if**: Defines the interfaces that abstract communication with Tenstorrent AI accelerators.
3.   **luwen-ref**: Implements the interfaces defined in luwen-if, providing a reference implementation for communication with the chips.
4.   **ttkmd-if**: Interfaces with the kernel driver for direct hardware access.

Language bindings like Python (pyluwen) and C++ (luwencpp) are built on top of the luwen-if interfaces to provide access from multiple programming languages.

## Chip Abstraction Model

The central abstraction in Luwen is the chip model, which provides a unified interface for interacting with different Tenstorrent chip architectures.

Sources: [crates/luwen-if/src/chip/mod.rs 276-316](https://github.com/tenstorrent/luwen/blob/55e35616/crates/luwen-if/src/chip/mod.rs#L276-L316)[crates/luwen-if/src/chip/mod.rs 318-386](https://github.com/tenstorrent/luwen/blob/55e35616/crates/luwen-if/src/chip/mod.rs#L318-L386)

The key components in this model are:

1.   **ChipImpl Trait**: Defines the common functionality for all chip implementations, including methods for initialization, communication, telemetry, and chip-specific operations.

2.   **HlComms Trait**: Provides a high-level interface for communication operations like NOC and AXI access.

3.   **Chip Struct**: Wraps a `ChipImpl` implementation, allowing code to work with chips in a type-agnostic manner while still supporting downcasting to specific chip types.

4.   **Chip Implementations**: Specific implementations for different chip architectures (Wormhole, Grayskull, Blackhole), each with their own unique capabilities.

## Communication System

Luwen provides a comprehensive communication system for interacting with Tenstorrent chips.

Sources: [crates/luwen-if/src/chip/mod.rs 16-22](https://github.com/tenstorrent/luwen/blob/55e35616/crates/luwen-if/src/chip/mod.rs#L16-L22)[crates/luwen-if/src/chip/mod.rs 302-308](https://github.com/tenstorrent/luwen/blob/55e35616/crates/luwen-if/src/chip/mod.rs#L302-L308)

The communication system includes:

1.   **High-Level Interface**: The `Chip`, `ChipImpl`, and `HlComms` abstractions provide a unified interface for all communication operations.

2.   **Memory Access**: Support for AXI (Advanced eXtensible Interface) and NOC (Network on Chip) for memory-mapped access to chip resources.

3.   **Chip Control**: ARC messaging for sending commands to the ARC processors on the chip, and SPI flash access for boot file system operations.

4.   **Inter-Chip Communication**: Ethernet interface for communication between chips, and neighboring chip discovery for multi-chip systems.

5.   **Telemetry**: Comprehensive telemetry collection for monitoring chip status and performance.

## Initialization and Status Tracking

Luwen includes a system for chip initialization and status tracking that ensures safe interaction with the hardware.

Sources: [crates/luwen-if/src/chip/mod.rs 290-293](https://github.com/tenstorrent/luwen/blob/55e35616/crates/luwen-if/src/chip/mod.rs#L290-L293)[crates/luwen-if/src/chip/mod.rs 355-358](https://github.com/tenstorrent/luwen/blob/55e35616/crates/luwen-if/src/chip/mod.rs#L355-L358)

The initialization process includes:

1.   **PCI Device Detection**: Scanning for Tenstorrent PCI devices.
2.   **Chip Object Creation**: Creating `Chip` instances for each detected device.
3.   **Initialization State Updates**: Regularly updating the initialization state of each chip via the `update_init_state` method.
4.   **Component Status Tracking**: Monitoring the status of various chip components (ARC, DRAM, Ethernet).
5.   **Neighboring Chip Discovery**: Once a chip is initialized, discovering neighboring chips for multi-chip systems.

## Telemetry System

Luwen provides a comprehensive telemetry system for monitoring chip status and performance.

Sources: [crates/luwen-if/src/chip/mod.rs 67-261](https://github.com/tenstorrent/luwen/blob/55e35616/crates/luwen-if/src/chip/mod.rs#L67-L261)[crates/luwen-if/src/chip/mod.rs 301](https://github.com/tenstorrent/luwen/blob/55e35616/crates/luwen-if/src/chip/mod.rs#L301-L301)

The telemetry system captures various metrics about the chip, including:

1.   **Identification**: Board ID, ASIC ID, device ID, and serial number.
2.   **Firmware Information**: ARC firmware version, Ethernet firmware version, and firmware date.
3.   **Status**: DDR status, Ethernet status, and other component statuses.
4.   **Performance Metrics**: AI clock speed, voltage, temperature, and power consumption.
5.   **Board Information**: Board type and various temperature readings.

The telemetry data can be retrieved using the `get_telemetry` method of the `ChipImpl` trait.

## Summary

The Luwen architecture provides a flexible and powerful framework for interacting with Tenstorrent AI accelerator chips. Its layered design, comprehensive chip abstraction model, and robust communication system enable developers to work with these advanced hardware accelerators without having to deal with the low-level details of chip communication.

Key strengths of the architecture include:

1.   **Unified Interface**: A consistent interface regardless of the specific chip architecture.
2.   **Type Safety**: Strong typing and downcasting for chip-specific operations.
3.   **Comprehensive Communication**: Support for various communication methods (AXI, NOC, ARC).
4.   **Robust Initialization**: A careful initialization process that ensures safe interaction with the hardware.
5.   **Extensibility**: Easy to extend for new chip architectures and features.

For more detailed information on specific aspects of the architecture, please refer to the dedicated documentation sections.

Dismiss
Refresh this wiki

Enter email to refresh
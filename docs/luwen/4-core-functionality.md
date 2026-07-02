---
project: luwen
github: https://github.com/tenstorrent/luwen
deepwiki: https://deepwiki.com/tenstorrent/luwen/4-core-functionality
---

# Core Functionality

Relevant source files
*   [crates/luwen-if/src/chip/mod.rs](https://github.com/tenstorrent/luwen/blob/55e35616/crates/luwen-if/src/chip/mod.rs)
*   [crates/luwen-if/src/interface.rs](https://github.com/tenstorrent/luwen/blob/55e35616/crates/luwen-if/src/interface.rs)
*   [crates/luwen-ref/src/lib.rs](https://github.com/tenstorrent/luwen/blob/55e35616/crates/luwen-ref/src/lib.rs)

This page outlines the fundamental capabilities and abstractions provided by the Luwen system for interacting with Tenstorrent AI accelerator chips. It covers the core interfaces, communication mechanisms, and key features that form the foundation of the Luwen framework.

For specific implementation details of individual chip architectures, see [Chip Support](https://deepwiki.com/tenstorrent/luwen/3-chip-support). For language-specific usage patterns, refer to [Language Bindings](https://deepwiki.com/tenstorrent/luwen/5-language-bindings).

## Chip Abstraction Model

Luwen provides a unified interface for interacting with different chip architectures (Wormhole, Grayskull, Blackhole) through a consistent abstraction model.

The diagram above shows the core abstraction model:

*   `ChipImpl`: A trait defining common functionality that all chip implementations must provide
*   `HlComms`: A trait for high-level communication operations
*   `Chip`: A wrapper struct that contains a boxed `ChipImpl`, providing a uniform interface and the ability to downcast to specific chip implementations

Sources: [crates/luwen-if/src/chip/mod.rs 282-316](https://github.com/tenstorrent/luwen/blob/55e35616/crates/luwen-if/src/chip/mod.rs#L282-L316)[crates/luwen-if/src/chip/mod.rs 318-386](https://github.com/tenstorrent/luwen/blob/55e35616/crates/luwen-if/src/chip/mod.rs#L318-L386)

### ChipImpl Trait

The `ChipImpl` trait defines the standard interface that all chip implementations must satisfy:

*   **Initialization management**: `update_init_state()` to track and update chip initialization status
*   **Architecture identification**: `get_arch()` to determine the chip architecture
*   **Telemetry**: `get_telemetry()` to retrieve performance and status information
*   **ARC messaging**: `arc_msg()` to send messages to the chip's ARC processor
*   **Chip connectivity**: `get_neighbouring_chips()` to discover networked chips
*   **Type access**: `as_any()` for type introspection to support downcasting

Sources: [crates/luwen-if/src/chip/mod.rs 282-316](https://github.com/tenstorrent/luwen/blob/55e35616/crates/luwen-if/src/chip/mod.rs#L282-L316)

### Chip Wrapper

The `Chip` struct encapsulates a `ChipImpl` implementation and provides:

*   A unified interface for any chip architecture
*   Convenience methods to downcast to specific chip types (`as_wh()`, `as_gs()`, `as_bh()`)
*   Delegation of operations to the inner implementation

This abstraction allows users to write architecture-agnostic code while still having access to specific features when needed.

Sources: [crates/luwen-if/src/chip/mod.rs 318-386](https://github.com/tenstorrent/luwen/blob/55e35616/crates/luwen-if/src/chip/mod.rs#L318-L386)

## Communication System

Luwen provides multiple interfaces for communicating with chips, including AXI (Advanced eXtensible Interface), NOC (Network on Chip), and Ethernet for remote chip access.

The communication system is built around function callbacks that implement specific operations:

| Communication Type | Description | Use Case |
| --- | --- | --- |
| AXI Access | Direct access to chip registers via AXI bus | Configuration, status reading |
| NOC Access | Grid-based access to cores/resources within a chip | Memory and compute resource access |
| Broadcast | Send data to multiple cores/resources at once | Efficient parallel operations |
| Ethernet | Access resources on remote interconnected chips | Multi-chip operations |

Sources: [crates/luwen-if/src/interface.rs 8-31](https://github.com/tenstorrent/luwen/blob/55e35616/crates/luwen-if/src/interface.rs#L8-L31)[crates/luwen-if/src/interface.rs 33-51](https://github.com/tenstorrent/luwen/blob/55e35616/crates/luwen-if/src/interface.rs#L33-L51)[crates/luwen-ref/src/lib.rs 63-107](https://github.com/tenstorrent/luwen/blob/55e35616/crates/luwen-ref/src/lib.rs#L63-L107)

### Memory Access

Memory access operations are implemented through several methods:

*   **AXI operations**: Direct read/write to chip registers
*   **NOC operations**: Grid-based access within the chip
*   **TLB management**: Translation Lookaside Buffer setup for memory operations

Sources: [crates/luwen-ref/src/lib.rs 63-157](https://github.com/tenstorrent/luwen/blob/55e35616/crates/luwen-ref/src/lib.rs#L63-L157)[crates/luwen-if/src/interface.rs 193-250](https://github.com/tenstorrent/luwen/blob/55e35616/crates/luwen-if/src/interface.rs#L193-L250)

## ARC Messaging

ARC messaging provides a mechanism to interact with the ARC processors on Tenstorrent chips, enabling control operations and firmware interactions.

The `ArcMsgOptions` struct configures how messages are sent to the ARC processor:

```
ArcMsgOptions {
    msg: ArcMsg,             // The message to send
    wait_for_done: bool,     // Whether to wait for completion
    timeout: Duration,       // How long to wait
    use_second_mailbox: bool, // Which mailbox to use
    addrs: Option<ArcMsgAddr> // Override mailbox addresses
}
```

Sources: [crates/luwen-if/src/chip/mod.rs 36-56](https://github.com/tenstorrent/luwen/blob/55e35616/crates/luwen-if/src/chip/mod.rs#L36-L56)[crates/luwen-if/src/chip/mod.rs 304](https://github.com/tenstorrent/luwen/blob/55e35616/crates/luwen-if/src/chip/mod.rs#L304-L304)

## Telemetry System

Luwen provides extensive telemetry data collection through the `Telemetry` struct, which captures comprehensive information about chip status, performance, and health.

Key telemetry data includes:

*   **Identification**: ASIC ID, board ID, serial numbers
*   **Firmware**: Version information for ARC, Ethernet, and other firmware components
*   **Performance**: Clock speeds, throttler status
*   **Thermal**: ASIC temperature, voltage regulator temperature, board temperatures
*   **Power**: Voltage levels, power consumption, current draw
*   **Status**: Ethernet connectivity, DDR status, fault flags

The `Telemetry` struct provides convenient methods to access formatted data, including:

*   `firmware_date()` - Returns the firmware date in YYYY-MM-DD format
*   `arc_fw_version()` - Returns firmware version in MAJOR.MINOR.PATCH format
*   `board_type()` - Returns the type of board (e.g., "e300", "galaxy")
*   `asic_temperature()` - Returns temperature in degrees Celsius

Sources: [crates/luwen-if/src/chip/mod.rs 66-260](https://github.com/tenstorrent/luwen/blob/55e35616/crates/luwen-if/src/chip/mod.rs#L66-L260)[crates/luwen-if/src/chip/mod.rs 301](https://github.com/tenstorrent/luwen/blob/55e35616/crates/luwen-if/src/chip/mod.rs#L301-L301)

## Chip Detection and Initialization

The chip detection and initialization process is a critical part of Luwen's functionality. It allows the system to discover available chips and prepare them for use.

The initialization process tracks the status of various components:

| Component | Status Types | Purpose |
| --- | --- | --- |
| ARC | Booting, Ready, Error | Control processor initialization |
| DRAM | Training, Ready, Error | Memory subsystem availability |
| Ethernet | Training, Ready, Error | Chip interconnect status |

The `InitStatus` enum and `ComponentStatusInfo` struct track the progressing state of initialization, allowing the system to determine when a chip is ready for use.

Sources: [crates/luwen-if/src/chip/mod.rs 22-27](https://github.com/tenstorrent/luwen/blob/55e35616/crates/luwen-if/src/chip/mod.rs#L22-L27)[crates/luwen-if/src/chip/mod.rs 263-273](https://github.com/tenstorrent/luwen/blob/55e35616/crates/luwen-if/src/chip/mod.rs#L263-L273)[crates/luwen-ref/src/lib.rs 19](https://github.com/tenstorrent/luwen/blob/55e35616/crates/luwen-ref/src/lib.rs#L19-L19)

## Chip Connectivity

For chips that support networking (like Wormhole), Luwen provides functionality to discover and interact with connected chips.

The `NeighbouringChip` struct holds information about connected chips:

```
NeighbouringChip {
    routing_enabled: bool,     // Whether routing to this chip is configured
    local_noc_addr: (u8, u8),  // Local NOC address of the connection
    remote_noc_addr: (u8, u8), // Remote NOC address of the connection
    eth_addr: EthAddr          // Ethernet address of the remote chip
}
```

Sources: [crates/luwen-if/src/chip/mod.rs 58-64](https://github.com/tenstorrent/luwen/blob/55e35616/crates/luwen-if/src/chip/mod.rs#L58-L64)[crates/luwen-if/src/chip/mod.rs 308](https://github.com/tenstorrent/luwen/blob/55e35616/crates/luwen-if/src/chip/mod.rs#L308-L308)

## Device Information

The `DeviceInfo` struct provides detailed information about the physical device, including:

*   PCIe domain, bus, slot, and function
*   Vendor and device IDs
*   Board ID
*   PCIe link information (width and generation)

This information is useful for system identification, monitoring, and diagnostics.

Sources: [crates/luwen-if/src/interface.rs 53-127](https://github.com/tenstorrent/luwen/blob/55e35616/crates/luwen-if/src/interface.rs#L53-L127)[crates/luwen-if/src/chip/mod.rs 315](https://github.com/tenstorrent/luwen/blob/55e35616/crates/luwen-if/src/chip/mod.rs#L315-L315)

Through this core functionality, Luwen provides a comprehensive, flexible, and chip-agnostic interface for interacting with Tenstorrent AI accelerators while still allowing access to architecture-specific features when needed.

Dismiss
Refresh this wiki

Enter email to refresh
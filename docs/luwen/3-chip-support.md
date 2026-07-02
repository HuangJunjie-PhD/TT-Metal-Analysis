---
project: luwen
github: https://github.com/tenstorrent/luwen
deepwiki: https://deepwiki.com/tenstorrent/luwen/3-chip-support
---

# Chip Support

Relevant source files
*   [crates/luwen-if/src/chip/mod.rs](https://github.com/tenstorrent/luwen/blob/55e35616/crates/luwen-if/src/chip/mod.rs)
*   [crates/luwen-if/src/detect_chips.rs](https://github.com/tenstorrent/luwen/blob/55e35616/crates/luwen-if/src/detect_chips.rs)
*   [crates/luwen-ref/src/detect.rs](https://github.com/tenstorrent/luwen/blob/55e35616/crates/luwen-ref/src/detect.rs)

This page provides a technical overview of Luwen's support for different Tenstorrent chip architectures, including the common interfaces, chip-specific implementations, and the chip detection and initialization processes. For detailed information on communication interfaces used by these chips, see [Communication Interfaces](https://deepwiki.com/tenstorrent/luwen/2.3-communication-interfaces).

## Chip Abstraction Model

The Luwen codebase employs a flexible abstraction model that enables interaction with various Tenstorrent chip architectures through a unified interface.

Sources: [crates/luwen-if/src/chip/mod.rs 282-316](https://github.com/tenstorrent/luwen/blob/55e35616/crates/luwen-if/src/chip/mod.rs#L282-L316)[crates/luwen-if/src/chip/mod.rs 321-346](https://github.com/tenstorrent/luwen/blob/55e35616/crates/luwen-if/src/chip/mod.rs#L321-L346)

### The ChipImpl Interface

At the core of Luwen's chip support is the `ChipImpl` trait, which defines a common interface for all chip implementations:

Sources: [crates/luwen-if/src/chip/mod.rs 282-316](https://github.com/tenstorrent/luwen/blob/55e35616/crates/luwen-if/src/chip/mod.rs#L282-L316)

The `ChipImpl` trait defines several key methods:

*   `update_init_state`: Updates and reports the initialization state of the chip
*   `get_arch`: Returns the architecture of the chip (Wormhole, Grayskull, Blackhole)
*   `get_telemetry`: Retrieves telemetry data from the chip
*   `arc_msg`: Sends a message to the ARC processor on the chip
*   `get_neighbouring_chips`: Returns information about neighbouring chips (for mesh configurations)
*   `get_device_info`: Retrieves device information about the underlying chip transport

### The Chip Wrapper

The `Chip` struct acts as a type-erased wrapper around any `ChipImpl` implementation, allowing code to interact with chips without knowing their specific type:

`pub struct Chip {    pub inner: Box<dyn ChipImpl>,}`
The `Chip` struct provides methods to safely downcast to specific chip implementations when needed:

*   `as_wh()`: Attempts to downcast to a `Wormhole` implementation
*   `as_gs()`: Attempts to downcast to a `Grayskull` implementation
*   `as_bh()`: Attempts to downcast to a `Blackhole` implementation

This design allows most code to work with the common `Chip` interface while still enabling access to architecture-specific functionality when required.

Sources: [crates/luwen-if/src/chip/mod.rs 321-346](https://github.com/tenstorrent/luwen/blob/55e35616/crates/luwen-if/src/chip/mod.rs#L321-L346)

## Supported Chip Architectures

Luwen currently supports three Tenstorrent chip architectures:

| Architecture | Description | Model Examples |
| --- | --- | --- |
| Grayskull | First generation Tenstorrent AI accelerator | Galaxy |
| Wormhole | Second generation Tenstorrent AI accelerator | E75, E150, E300 |
| Blackhole | Third generation Tenstorrent AI accelerator | P100, P150, P300 |

Each chip architecture implements the `ChipImpl` trait but may provide additional functionality specific to that architecture.

## Chip Detection and Initialization

Luwen provides a robust system for detecting and initializing chips, supporting both local (directly connected) and remote (accessible via Ethernet) chips.

Sources: [crates/luwen-if/src/detect_chips.rs 251-386](https://github.com/tenstorrent/luwen/blob/55e35616/crates/luwen-if/src/detect_chips.rs#L251-L386)[crates/luwen-ref/src/detect.rs 238-308](https://github.com/tenstorrent/luwen/blob/55e35616/crates/luwen-ref/src/detect.rs#L238-L308)

### Chip Detection Process

The chip detection process consists of the following steps:

1.   Scan for PCI devices to find locally accessible chips
2.   For each root chip: 
    *   Check if it matches the architecture filter (if specified)
    *   Wait for initialization and determine the chip's status
    *   If the chip is initialized and remote detection is enabled, enumerate neighbouring chips

3.   For each remote chip: 
    *   Open a remote connection to the chip
    *   Initialize the chip
    *   Check for additional neighbouring chips (depth-first search)

4.   Return a list of all detected chips, both local and remote

The detection process uses the `UninitChip` enum to represent chips in various states of initialization:

`pub enum UninitChip {    // A partially initialized chip that may not be fully usable    Partially {        status: Box<InitStatus>,        underlying: Chip,    },    // A fully initialized and usable chip    Initialized(Chip),}`
Sources: [crates/luwen-if/src/detect_chips.rs 21-173](https://github.com/tenstorrent/luwen/blob/55e35616/crates/luwen-if/src/detect_chips.rs#L21-L173)

### Chip Initialization States

During initialization, each chip goes through several stages, with various components being checked:

1.   ARC status: Checks if the ARC processor is ready
2.   DRAM status: Verifies that DRAM is trained and ready
3.   Ethernet status: Ensures Ethernet connectivity is established
4.   CPU status: Validates CPU functionality (for applicable chips)

Each component can be in one of the following states:

*   Waiting: Initialization is in progress
*   Ready: Component is successfully initialized
*   Error: Component initialization failed

Sources: [crates/luwen-if/src/detect_chips.rs 251-386](https://github.com/tenstorrent/luwen/blob/55e35616/crates/luwen-if/src/detect_chips.rs#L251-L386)

### Detection Configuration

The detection process can be customized using the `ChipDetectOptions` structure:

`pub struct ChipDetectOptions {    // Continue searching for chips even on recoverable errors    pub continue_on_failure: bool,    // Only search for directly accessible chips (no remote detection)    pub local_only: bool,    // Filter for specific chip architectures    pub chip_filter: Vec<Arch>,    // Avoid operations that could cause NOC hangs    pub noc_safe: bool,}`
Sources: [crates/luwen-if/src/detect_chips.rs 175-195](https://github.com/tenstorrent/luwen/blob/55e35616/crates/luwen-if/src/detect_chips.rs#L175-L195)

## Telemetry and Monitoring

Luwen provides comprehensive telemetry capabilities for monitoring chip status and performance.

Sources: [crates/luwen-if/src/chip/mod.rs 66-261](https://github.com/tenstorrent/luwen/blob/55e35616/crates/luwen-if/src/chip/mod.rs#L66-L261)

The `Telemetry` struct provides detailed information about the chip's state, including:

*   Firmware versions (ARC, Ethernet, DDR)
*   Hardware information (board ID, ASIC ID)
*   Operating conditions (clock speeds, voltage, temperature)
*   Component statuses (DRAM, Ethernet, PCIe)

Additionally, the `Telemetry` struct offers helper methods to interpret raw telemetry data, such as:

*   `firmware_date()`: Returns the firmware date in YYYY-MM-DD format
*   `arc_fw_version()`: Returns the ARC firmware version in semantic versioning format
*   `board_type()`: Returns the board type based on the serial number
*   `voltage()`: Returns the core voltage in volts
*   `asic_temperature()`: Returns the ASIC temperature in degrees Celsius
*   `power()`: Returns the power consumption in watts

Telemetry data can be obtained from any chip using the `get_telemetry()` method defined in the `ChipImpl` trait.

## Multi-Chip Support

Luwen provides built-in support for multi-chip configurations, particularly for Wormhole and Blackhole architectures that support Ethernet-based interconnects.

Sources: [crates/luwen-if/src/detect_chips.rs 251-386](https://github.com/tenstorrent/luwen/blob/55e35616/crates/luwen-if/src/detect_chips.rs#L251-L386)

### Neighbouring Chip Discovery

For supported architectures, Luwen can discover neighbouring chips connected via Ethernet. This information is encapsulated in the `NeighbouringChip` struct:

`pub struct NeighbouringChip {    pub routing_enabled: bool,    pub local_noc_addr: (u8, u8),    pub remote_noc_addr: (u8, u8),    pub eth_addr: EthAddr,}`
The `get_neighbouring_chips()` method returns a list of neighbouring chips. For Grayskull chips, this list is empty. For Wormhole and Blackhole chips, it can return up to four neighbouring chips (one for each direction in the mesh).

Sources: [crates/luwen-if/src/chip/mod.rs 58-64](https://github.com/tenstorrent/luwen/blob/55e35616/crates/luwen-if/src/chip/mod.rs#L58-L64)

### Duplicate Detection

During chip detection, Luwen carefully tracks which chips have already been discovered to avoid duplicates. This is particularly important in multi-host environments where the same chip might be accessible through different paths.

For Wormhole and Blackhole chips, identification is based on:

1.   Board ID
2.   Ethernet address (chip coordinates in the mesh)

For Grayskull chips, identification is based on:

1.   Interface ID (PCI device ID)

Sources: [crates/luwen-if/src/detect_chips.rs 251-386](https://github.com/tenstorrent/luwen/blob/55e35616/crates/luwen-if/src/detect_chips.rs#L251-L386)

## Working with Chips

To interact with chips in your code, you will typically:

1.   Detect available chips:

`let chips = detect_chips()?;`
2.   Work with the common `Chip` interface:

`for chip in &chips {    let telemetry = chip.get_telemetry()?;    println!("Chip type: {}, Temperature: {}°C",              chip.get_arch(), telemetry.asic_temperature());}`
3.   Use architecture-specific functionality when needed:

`if let Some(wh) = chip.as_wh() {    // Work with Wormhole-specific features} else if let Some(gs) = chip.as_gs() {    // Work with Grayskull-specific features} else if let Some(bh) = chip.as_bh() {    // Work with Blackhole-specific features}`

This approach allows you to write code that works with any supported chip architecture while still leveraging architecture-specific capabilities when required.

Sources: [crates/luwen-if/src/chip/mod.rs 331-346](https://github.com/tenstorrent/luwen/blob/55e35616/crates/luwen-if/src/chip/mod.rs#L331-L346)

Dismiss
Refresh this wiki

Enter email to refresh
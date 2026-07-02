---
project: luwen
github: https://github.com/tenstorrent/luwen
deepwiki: https://deepwiki.com/tenstorrent/luwen/5-language-bindings
---

# Language Bindings

Relevant source files
*   [crates/luwencpp/Cargo.toml](https://github.com/tenstorrent/luwen/blob/55e35616/crates/luwencpp/Cargo.toml)
*   [crates/pyluwen/src/lib.rs](https://github.com/tenstorrent/luwen/blob/55e35616/crates/pyluwen/src/lib.rs)

## Purpose and Scope

This document describes the language bindings for the Luwen repository, which provide interfaces for interacting with Tenstorrent AI accelerator chips from different programming languages. Luwen currently offers two language binding options:

1.   Python bindings (`pyluwen`)
2.   C++ bindings (`luwencpp`)

These bindings allow developers to access Luwen's functionality for chip detection, initialization, communication, telemetry, and data transfer without needing to write Rust code directly.

## Language Bindings Architecture

Sources: [crates/pyluwen/src/lib.rs 1-1632](https://github.com/tenstorrent/luwen/blob/55e35616/crates/pyluwen/src/lib.rs#L1-L1632)[crates/luwencpp/Cargo.toml 1-25](https://github.com/tenstorrent/luwen/blob/55e35616/crates/luwencpp/Cargo.toml#L1-L25)

## Python Bindings (`pyluwen`)

The Python bindings provide a comprehensive interface to Luwen's functionality, allowing Python applications to detect, initialize, and communicate with Tenstorrent chips.

### Class Hierarchy

Sources: [crates/pyluwen/src/lib.rs 22-142](https://github.com/tenstorrent/luwen/blob/55e35616/crates/pyluwen/src/lib.rs#L22-L142)[crates/pyluwen/src/lib.rs 517-642](https://github.com/tenstorrent/luwen/blob/55e35616/crates/pyluwen/src/lib.rs#L517-L642)

### Key Functions

The `pyluwen` module exposes several key functions for chip detection and management:

| Function | Description |
| --- | --- |
| `detect_chips()` | Detects and initializes all available chips |
| `detect_chips_fallible()` | Returns uninitialized chips for manual initialization |
| `pci_scan()` | Scans for available PCI devices |
| `run_wh_ubb_ipmi_reset()` | Performs an IPMI reset on UBB (Universal Baseboard) |
| `run_ubb_wait_for_driver_load()` | Waits for the UBB driver to load |

Sources: [crates/pyluwen/src/lib.rs 1405-1570](https://github.com/tenstorrent/luwen/blob/55e35616/crates/pyluwen/src/lib.rs#L1405-L1570)

### Usage Pattern

Sources: [crates/pyluwen/src/lib.rs 1527-1549](https://github.com/tenstorrent/luwen/blob/55e35616/crates/pyluwen/src/lib.rs#L1527-L1549)[crates/pyluwen/src/lib.rs 322-486](https://github.com/tenstorrent/luwen/blob/55e35616/crates/pyluwen/src/lib.rs#L322-L486)

### Example Workflow

1.   **Detect and Initialize Chips**:

 
2.   **Access Chip-Specific Functionality**:

 
3.   **Perform Common Operations**:

 

Sources: [crates/pyluwen/src/lib.rs 455-486](https://github.com/tenstorrent/luwen/blob/55e35616/crates/pyluwen/src/lib.rs#L455-L486)[crates/pyluwen/src/lib.rs 326-444](https://github.com/tenstorrent/luwen/blob/55e35616/crates/pyluwen/src/lib.rs#L326-L444)

## C++ Bindings (`luwencpp`)

The C++ bindings provide access to Luwen functionality from C++ applications. The bindings are implemented as a shared library (`libluwencpp.so`) with a corresponding header file (`luwen.h`).

Sources: [crates/luwencpp/Cargo.toml 1-25](https://github.com/tenstorrent/luwen/blob/55e35616/crates/luwencpp/Cargo.toml#L1-L25)

### Implementation Details

The C++ bindings are generated using `cbindgen`, which creates C-compatible headers from Rust code. The bindings expose the core functionality of Luwen:

*   Chip detection and initialization
*   Communication interfaces (NOC, AXI, ARC)
*   Telemetry data retrieval
*   Memory operations

The shared library is built with the `cdylib` crate type, making it compatible with C++ applications.

Sources: [crates/luwencpp/Cargo.toml 15-17](https://github.com/tenstorrent/luwen/blob/55e35616/crates/luwencpp/Cargo.toml#L15-L17)[crates/luwencpp/Cargo.toml 23-24](https://github.com/tenstorrent/luwen/blob/55e35616/crates/luwencpp/Cargo.toml#L23-L24)

### Package Distribution

The C++ bindings are distributed as part of a Debian package with the following assets:

| File | Installation Path | Permissions |
| --- | --- | --- |
| `luwen.h` | `/usr/include/luwen.h` | 644 |
| `libluwencpp.so` | `/usr/lib/` | 644 |

Sources: [crates/luwencpp/Cargo.toml 9-13](https://github.com/tenstorrent/luwen/blob/55e35616/crates/luwencpp/Cargo.toml#L9-L13)

## Common Functionality

Both Python and C++ bindings provide interfaces to the following core Luwen functionality:

### Chip Management

*   Detecting and initializing supported chip architectures (Wormhole, Grayskull, Blackhole)
*   Accessing chip-specific features through a unified interface
*   Managing remote chip connections

### Communication Interfaces

*   Network on Chip (NOC) for on-chip communication
*   Advanced eXtensible Interface (AXI) for memory access
*   ARC messaging for control operations
*   SPI for flash memory access

### Memory Operations

*   Direct Memory Access (DMA) for efficient data transfer
*   TLB (Translation Lookaside Buffer) configuration for memory mapping
*   Memory allocation and management

### Telemetry and Monitoring

*   Retrieving chip status and health information
*   Monitoring temperatures, clock speeds, and other metrics
*   Accessing firmware versions and hardware identifiers

## Best Practices

1.   **Error Handling**: Both bindings propagate errors from the underlying Rust code. In Python, these are converted to `PyException` instances.

2.   **Resource Management**:

    *   In Python, resources are managed through Python's garbage collection
    *   In C++, follow RAII principles with the provided interfaces

3.   **Performance Considerations**:

    *   Use DMA operations for large data transfers
    *   Configure TLB appropriately for memory access patterns
    *   Utilize batch operations where available

Sources: [crates/pyluwen/src/lib.rs 342-345](https://github.com/tenstorrent/luwen/blob/55e35616/crates/pyluwen/src/lib.rs#L342-L345)[crates/pyluwen/src/lib.rs 463-473](https://github.com/tenstorrent/luwen/blob/55e35616/crates/pyluwen/src/lib.rs#L463-L473)

Dismiss
Refresh this wiki

Enter email to refresh
---
project: luwen
github: https://github.com/tenstorrent/luwen
deepwiki: https://deepwiki.com/tenstorrent/luwen/1-overview
---

# Overview

Relevant source files
*   [.gitlab-ci.yml](https://github.com/tenstorrent/luwen/blob/55e35616/.gitlab-ci.yml)
*   [.pre-commit-config.yaml](https://github.com/tenstorrent/luwen/blob/55e35616/.pre-commit-config.yaml)
*   [.tito/packages/.readme](https://github.com/tenstorrent/luwen/blob/55e35616/.tito/packages/.readme)
*   [.tito/tito.props](https://github.com/tenstorrent/luwen/blob/55e35616/.tito/tito.props)
*   [Cargo.toml](https://github.com/tenstorrent/luwen/blob/55e35616/Cargo.toml)
*   [README.md](https://github.com/tenstorrent/luwen/blob/55e35616/README.md?plain=1)
*   [crates/luwen-core/src/lib.rs](https://github.com/tenstorrent/luwen/blob/55e35616/crates/luwen-core/src/lib.rs)
*   [crates/luwen-if/src/lib.rs](https://github.com/tenstorrent/luwen/blob/55e35616/crates/luwen-if/src/lib.rs)
*   [crates/pyluwen/Cargo.toml](https://github.com/tenstorrent/luwen/blob/55e35616/crates/pyluwen/Cargo.toml)
*   [src/bin/luwen-demo.rs](https://github.com/tenstorrent/luwen/blob/55e35616/src/bin/luwen-demo.rs)

Luwen is a high-level interface for safe and efficient access to Tenstorrent AI accelerator chips. Named after Antonie van Leeuwenhoek who invented the microscope, this library provides a unified framework for interacting with various Tenstorrent hardware architectures. Luwen enables chip discovery, initialization, communication, and monitoring functionality across different chip types.

For detailed information on the architectural design, see [Architecture](https://deepwiki.com/tenstorrent/luwen/2-architecture).

## Purpose and Scope

Luwen serves three primary use cases:

1.   **High-Level Interface** - Provides a simplified API for software tooling to access diagnostics and interact with Tenstorrent chips via PCI or remote connections
2.   **Chip Management** - Handles chip discovery, initialization, and basic operations like resets
3.   **Low-Level Debug Capabilities** - Offers direct access to underlying implementation details for advanced debugging scenarios

Sources: [README.md 1-22](https://github.com/tenstorrent/luwen/blob/55e35616/README.md?plain=1#L1-L22)[Cargo.toml 1-7](https://github.com/tenstorrent/luwen/blob/55e35616/Cargo.toml#L1-L7)

## System Architecture

Luwen employs a modular, layered architecture to provide a consistent interface while supporting different chip implementations.

### Overall System Architecture

Sources: [Cargo.toml 11-15](https://github.com/tenstorrent/luwen/blob/55e35616/Cargo.toml#L11-L15)[crates/pyluwen/Cargo.toml 11-16](https://github.com/tenstorrent/luwen/blob/55e35616/crates/pyluwen/Cargo.toml#L11-L16)

### Core Components

The Luwen system is built on four main crates:

| Component | Description | Role |
| --- | --- | --- |
| luwen-core | Basic types and definitions | Defines fundamental types like `Arch` enum |
| luwen-if | Interface definitions | Defines abstract interfaces like `ChipImpl` and `HlComms` |
| luwen-ref | Reference implementation | Implements interfaces defined in luwen-if |
| ttkmd-if | Kernel driver interface | Provides access to the hardware |

Sources: [Cargo.toml 11-15](https://github.com/tenstorrent/luwen/blob/55e35616/Cargo.toml#L11-L15)[crates/luwen-core/src/lib.rs 7-40](https://github.com/tenstorrent/luwen/blob/55e35616/crates/luwen-core/src/lib.rs#L7-L40)[crates/luwen-if/src/lib.rs 7-23](https://github.com/tenstorrent/luwen/blob/55e35616/crates/luwen-if/src/lib.rs#L7-L23)

## Supported Chip Architectures

Luwen supports multiple Tenstorrent chip architectures through a unified interface, while allowing access to architecture-specific features when needed.

Sources: [crates/luwen-core/src/lib.rs 7-51](https://github.com/tenstorrent/luwen/blob/55e35616/crates/luwen-core/src/lib.rs#L7-L51)[crates/luwen-if/src/lib.rs 22](https://github.com/tenstorrent/luwen/blob/55e35616/crates/luwen-if/src/lib.rs#L22-L22)

## Communication System

Luwen provides multiple communication channels to interact with Tenstorrent chips:

Sources: [src/bin/luwen-demo.rs 4-133](https://github.com/tenstorrent/luwen/blob/55e35616/src/bin/luwen-demo.rs#L4-L133)[crates/luwen-if/src/lib.rs 17-23](https://github.com/tenstorrent/luwen/blob/55e35616/crates/luwen-if/src/lib.rs#L17-L23)

## Chip Detection and Management

One of Luwen's key functions is to detect and initialize chips in a system. The flow shown below illustrates how Luwen discovers and manages chips:

Sources: [crates/luwen-if/src/lib.rs 22](https://github.com/tenstorrent/luwen/blob/55e35616/crates/luwen-if/src/lib.rs#L22-L22)[src/bin/luwen-demo.rs 124-131](https://github.com/tenstorrent/luwen/blob/55e35616/src/bin/luwen-demo.rs#L124-L131)

## Usage Example

Below is a simple example demonstrating how to use Luwen to detect chips and send an ARC message:

```
// Detect all available chips
let all_chips = luwen_if::detect_chips_silent([], Default::default())?;

// Iterate through detected chips
for (chip_id, chip) in all_chips.into_iter().enumerate() {
    println!("Running on device {chip_id}");
    
    // Send a test message to the chip
    chip.arc_msg(ArcMsgOptions {
        msg: TypedArcMsg::Test { arg: 101 }.into(),
        ..Default::default()
    })?;
}
```

Sources: [src/bin/luwen-demo.rs 124-131](https://github.com/tenstorrent/luwen/blob/55e35616/src/bin/luwen-demo.rs#L124-L131)

## Language Bindings

Luwen provides language bindings to integrate with different programming environments:

| Binding | Description |
| --- | --- |
| pyluwen | Python bindings for accessing Luwen functionality from Python |
| luwencpp | C++ bindings for integrating with C++ applications |

For more information about using the Python bindings, see [Python (pyluwen)](https://deepwiki.com/tenstorrent/luwen/5.1-python-(pyluwen)).

Sources: [crates/pyluwen/Cargo.toml 1-18](https://github.com/tenstorrent/luwen/blob/55e35616/crates/pyluwen/Cargo.toml#L1-L18)

## Related Pages

For more detailed information about specific aspects of Luwen, refer to the following pages:

*   [Architecture](https://deepwiki.com/tenstorrent/luwen/2-architecture) - Detailed architecture overview
*   [Core Components](https://deepwiki.com/tenstorrent/luwen/2.2-core-components) - Information about key abstractions
*   [Chip Support](https://deepwiki.com/tenstorrent/luwen/3-chip-support) - Details on supported chip architectures
*   [Language Bindings](https://deepwiki.com/tenstorrent/luwen/5-language-bindings) - Using Luwen from Python and C++

Dismiss
Refresh this wiki

Enter email to refresh
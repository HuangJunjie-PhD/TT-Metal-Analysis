---
project: tt-toplike
github: https://github.com/tenstorrent/tt-toplike
deepwiki: https://deepwiki.com/tenstorrent/tt-toplike/3-telemetry-backends
---

# Telemetry Backends

Relevant source files
*   [src/backend/factory.rs](https://github.com/tenstorrent/tt-toplike/blob/487bfe5a/src/backend/factory.rs)
*   [src/backend/mod.rs](https://github.com/tenstorrent/tt-toplike/blob/487bfe5a/src/backend/mod.rs)

The `tt-toplike` architecture employs a multi-backend system to decouple telemetry data acquisition from the visualization layers. This allows the application to run in diverse environments, ranging from production servers with full driver stacks to restricted environments using only `sysfs`, or even development machines without Tenstorrent hardware.

## Architecture Overview

All backends are abstracted behind the `TelemetryBackend` trait [src/backend/mod.rs 86-206](https://github.com/tenstorrent/tt-toplike/blob/487bfe5a/src/backend/mod.rs#L86-L206) This trait defines a standard contract for device discovery, lifecycle management, and data retrieval.

### The TelemetryBackend Trait

The lifecycle of a backend follows a strict sequence:

1.   **Creation**: Initialized via `BackendConfig`[src/backend/mod.rs 218-232](https://github.com/tenstorrent/tt-toplike/blob/487bfe5a/src/backend/mod.rs#L218-L232)
2.   **Initialization**: The `init()` method is called to discover devices and validate permissions [src/backend/mod.rs 110](https://github.com/tenstorrent/tt-toplike/blob/487bfe5a/src/backend/mod.rs#L110-L110)
3.   **Update Loop**: The `update()` method is called periodically (target <50ms) to refresh hardware state [src/backend/mod.rs 136](https://github.com/tenstorrent/tt-toplike/blob/487bfe5a/src/backend/mod.rs#L136-L136)
4.   **Data Access**: The UI queries `devices()`, `telemetry()`, and `smbus_telemetry()`[src/backend/mod.rs 151-201](https://github.com/tenstorrent/tt-toplike/blob/487bfe5a/src/backend/mod.rs#L151-L201)

### Backend Factory and Auto-Detection

The `factory` module manages the instantiation of backends based on CLI arguments or environment constraints.

*   **Auto-Detection**: The `create_auto_backend` function implements a "safe-mode" detection chain [src/backend/factory.rs 103-133](https://github.com/tenstorrent/tt-toplike/blob/487bfe5a/src/backend/factory.rs#L103-L133) It prioritizes non-invasive methods and explicitly skips the `Luwen` backend to avoid potential hardware panics during discovery.
*   **Runtime Switching**: Users can cycle through available backends at runtime (e.g., via the `b` key in the TUI). The `next_backend` function defines the rotation order [src/backend/factory.rs 139-170](https://github.com/tenstorrent/tt-toplike/blob/487bfe5a/src/backend/factory.rs#L139-L170)

### Backend Relationships

The following diagram illustrates the relationship between the UI, the trait interface, and the concrete implementations.

**Backend Implementation Hierarchy**

**Sources:**[src/backend/mod.rs 20-36](https://github.com/tenstorrent/tt-toplike/blob/487bfe5a/src/backend/mod.rs#L20-L36)[src/backend/factory.rs 29-95](https://github.com/tenstorrent/tt-toplike/blob/487bfe5a/src/backend/factory.rs#L29-L95)

## Backend Comparison

| Backend | Method | Invasiveness | Requirements | Best For |
| --- | --- | --- | --- | --- |
| **Hybrid** | Sysfs + `tt-smi` stream | Low | Linux, `tenstorrent` driver | Production monitoring |
| **Sysfs** | `/sys/class/hwmon` | Lowest | Linux, `tenstorrent` driver | Restricted environments |
| **JSON** | `tt-smi -s` | Medium | `tt-smi` binary | Systems without `hwmon` |
| **Luwen** | PCI BAR0 Access | High | Root/PCI permissions | Debugging/Low-level dev |
| **Mock** | Synthetic Data | None | None | CI/CD and UI testing |

## Core Components

### BackendConfig

The `BackendConfig` struct [src/backend/mod.rs 218-232](https://github.com/tenstorrent/tt-toplike/blob/487bfe5a/src/backend/mod.rs#L218-L232) carries settings across the factory, including:

*   `sample_rate`: How often to poll hardware.
*   `devices`: Optional list of specific device indices to monitor.
*   `skip_smbus`: Whether to disable high-overhead SMBUS telemetry.

### Auto-Detection Chain

When `BackendType::Auto` is selected [src/backend/factory.rs 35-38](https://github.com/tenstorrent/tt-toplike/blob/487bfe5a/src/backend/factory.rs#L35-L38) the system attempts initialization in the following order:

1.   **Hybrid**: Tries to find `hwmon` devices and `tt-smi`[src/backend/factory.rs 109-117](https://github.com/tenstorrent/tt-toplike/blob/487bfe5a/src/backend/factory.rs#L109-L117)
2.   **JSON**: Falls back to spawning `tt-smi` if `hwmon` is missing [src/backend/factory.rs 120-124](https://github.com/tenstorrent/tt-toplike/blob/487bfe5a/src/backend/factory.rs#L120-L124)
3.   **Mock**: Final fallback to ensure the application always starts [src/backend/factory.rs 128-132](https://github.com/tenstorrent/tt-toplike/blob/487bfe5a/src/backend/factory.rs#L128-L132)

**Backend Selection Logic**

**Sources:**[src/backend/factory.rs 103-133](https://github.com/tenstorrent/tt-toplike/blob/487bfe5a/src/backend/factory.rs#L103-L133)

* * *

## Detailed Backend Documentation

For specific implementation details, configuration options, and troubleshooting for each backend, see the following child pages:

### [Hybrid Backend (Default)](https://deepwiki.com/tenstorrent/tt-toplike/3.1-hybrid-backend-(default))

The primary backend for Linux systems. It provides high-frequency updates via `sysfs` for power and temperature, while using a background `tt-smi` stream to enrich data with SMBUS info like DDR speeds and firmware versions.

### [Sysfs Backend](https://deepwiki.com/tenstorrent/tt-toplike/3.2-sysfs-backend)

A pure Linux implementation that reads from `/sys/class/hwmon/`. It is the most lightweight hardware-based backend and requires no external binaries.

### [JSON Backend](https://deepwiki.com/tenstorrent/tt-toplike/3.3-json-backend)

Interfaces with the hardware by spawning `tt-smi --json` as a subprocess. Useful on systems where `sysfs` nodes are not exposed or for consistency with existing toolchains.

### [Luwen Backend](https://deepwiki.com/tenstorrent/tt-toplike/3.4-luwen-backend)

Uses the `luwen` crate for direct PCI BAR0 register access. This is the most powerful but most invasive backend, capable of reading telemetry even when the standard driver stack is partially unresponsive.

### [Mock Backend](https://deepwiki.com/tenstorrent/tt-toplike/3.5-mock-backend)

Generates deterministic synthetic data. Essential for development, UI profiling, and running the application in CI environments without physical Tenstorrent cards.

**Sources:**[src/backend/mod.rs 5-47](https://github.com/tenstorrent/tt-toplike/blob/487bfe5a/src/backend/mod.rs#L5-L47)[src/backend/factory.rs 5-24](https://github.com/tenstorrent/tt-toplike/blob/487bfe5a/src/backend/factory.rs#L5-L24)

Dismiss
Refresh this wiki

Enter email to refresh
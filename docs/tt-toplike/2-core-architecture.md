---
project: tt-toplike
github: https://github.com/tenstorrent/tt-toplike
deepwiki: https://deepwiki.com/tenstorrent/tt-toplike/2-core-architecture
---

# Core Architecture

Relevant source files
*   [src/config.rs](https://github.com/tenstorrent/tt-toplike/blob/487bfe5a/src/config.rs)
*   [src/error.rs](https://github.com/tenstorrent/tt-toplike/blob/487bfe5a/src/error.rs)
*   [src/lib.rs](https://github.com/tenstorrent/tt-toplike/blob/487bfe5a/src/lib.rs)
*   [src/logging.rs](https://github.com/tenstorrent/tt-toplike/blob/487bfe5a/src/logging.rs)

The `tt-toplike` library crate is designed as a decoupled, multi-layered system that separates hardware telemetry acquisition from data visualization. This architecture allows the same core logic to drive a Terminal User Interface (TUI), multiple Graphical User Interfaces (GUIs), or potential future interfaces like REST APIs [src/lib.rs 9-12](https://github.com/tenstorrent/tt-toplike/blob/487bfe5a/src/lib.rs#L9-L12)

## System Overview

The system is organized into three primary layers:

1.   **Backend Layer**: Abstracted via the `TelemetryBackend` trait, responsible for hardware discovery and data polling [src/lib.rs 16-21](https://github.com/tenstorrent/tt-toplike/blob/487bfe5a/src/lib.rs#L16-L21)
2.   **Data & Logic Layer**: Common models, error handling, and animation state management [src/lib.rs 74-78](https://github.com/tenstorrent/tt-toplike/blob/487bfe5a/src/lib.rs#L74-L78)
3.   **Frontend Layer**: UI implementations (TUI/GUI) that consume the telemetry data [src/lib.rs 90-91](https://github.com/tenstorrent/tt-toplike/blob/487bfe5a/src/lib.rs#L90-L91)

### Code Entity Relationship

The following diagram illustrates how high-level system components map to specific code entities within the library.

**Component to Entity Mapping**

Sources: [src/lib.rs 16-21](https://github.com/tenstorrent/tt-toplike/blob/487bfe5a/src/lib.rs#L16-L21)[src/lib.rs 73-91](https://github.com/tenstorrent/tt-toplike/blob/487bfe5a/src/lib.rs#L73-L91)[src/logging.rs 39-42](https://github.com/tenstorrent/tt-toplike/blob/487bfe5a/src/logging.rs#L39-L42)

* * *

## Telemetry Backends and Decoupling

The library is built around the `TelemetryBackend` trait, which provides a uniform interface for different hardware access methods [src/lib.rs 16-17](https://github.com/tenstorrent/tt-toplike/blob/487bfe5a/src/lib.rs#L16-L17) This decoupling ensures that the UI layer remains agnostic of whether it is communicating with real hardware via PCI BAR0, parsing `tt-smi` JSON output, or using a mock generator for CI/CD [src/lib.rs 18-21](https://github.com/tenstorrent/tt-toplike/blob/487bfe5a/src/lib.rs#L18-L21)

For detailed information on specific backend implementations and the auto-detection chain, see [Telemetry Backends](https://deepwiki.com/tenstorrent/tt-toplike/3-telemetry-backends).

## Data Models and State

Telemetry data is normalized into a set of core structs. The primary data structure is `Telemetry`, which aggregates power, temperature, and clock metrics [src/lib.rs 96](https://github.com/tenstorrent/tt-toplike/blob/487bfe5a/src/lib.rs#L96-L96) These models are designed to be serializable and architecture-aware, supporting different Tenstorrent chip generations (e.g., Grayskull, Wormhole).

For a detailed reference on fields and parsing, see [Data Models](https://deepwiki.com/tenstorrent/tt-toplike/2.1-data-models).

## Error Handling and Logging

### Error Handling

The crate uses a unified error type, `TTTopError`, implemented via `thiserror`[src/error.rs 11-18](https://github.com/tenstorrent/tt-toplike/blob/487bfe5a/src/error.rs#L11-L18) It categorizes failures into IO, JSON, Terminal, Configuration, and Backend-specific errors [src/error.rs 19-42](https://github.com/tenstorrent/tt-toplike/blob/487bfe5a/src/error.rs#L19-L42) Backend-specific issues are further refined into `BackendError` for granular reporting of hardware access failures [src/error.rs 47-84](https://github.com/tenstorrent/tt-toplike/blob/487bfe5a/src/error.rs#L47-L84)

### Thread-Safe Logging

To prevent log output from corrupting the TUI display, `tt-toplike` implements a custom `BufferedLogger`[src/logging.rs 39-42](https://github.com/tenstorrent/tt-toplike/blob/487bfe5a/src/logging.rs#L39-L42)

*   **Message Buffering**: Stores the last 100 messages in a thread-safe `VecDeque`[src/logging.rs 15-32](https://github.com/tenstorrent/tt-toplike/blob/487bfe5a/src/logging.rs#L15-L32)
*   **Output Control**: Stderr output can be toggled via `disable_stderr()` when entering TUI alternate screens [src/logging.rs 217-219](https://github.com/tenstorrent/tt-toplike/blob/487bfe5a/src/logging.rs#L217-L219)
*   **Access**: Frontends can retrieve logs for display in "Log" or "System" views using `get_log_messages()`[src/logging.rs 137-146](https://github.com/tenstorrent/tt-toplike/blob/487bfe5a/src/logging.rs#L137-L146)

**Logging Data Flow**

Sources: [src/logging.rs 44-78](https://github.com/tenstorrent/tt-toplike/blob/487bfe5a/src/logging.rs#L44-L78)[src/logging.rs 137-146](https://github.com/tenstorrent/tt-toplike/blob/487bfe5a/src/logging.rs#L137-L146)[src/logging.rs 203-219](https://github.com/tenstorrent/tt-toplike/blob/487bfe5a/src/logging.rs#L203-L219)

* * *

## Configuration System

The application behavior is governed by a multi-tiered configuration system:

1.   **Animation Profiles**: Named presets (`Normal`, `Paranoid`, `Relaxed`) that control visualization sensitivity and particle density [src/config.rs 13-25](https://github.com/tenstorrent/tt-toplike/blob/487bfe5a/src/config.rs#L13-L25)
2.   **TOML Overrides**: User-specific settings loaded from `~/.config/tt-toplike/config.toml`[src/config.rs 88-93](https://github.com/tenstorrent/tt-toplike/blob/487bfe5a/src/config.rs#L88-L93)
3.   **CLI Arguments**: Runtime overrides for backend selection and display modes.

For details on available CLI flags and the TOML schema, see [CLI and Configuration](https://deepwiki.com/tenstorrent/tt-toplike/2.2-cli-and-configuration).

## Subsystem Documentation

*   [Data Models](https://deepwiki.com/tenstorrent/tt-toplike/2.1-data-models) — Detailed reference for the Device, Architecture, Telemetry, SmbusTelemetry, GddrTempPair, FirmwaresInfo, and DeviceLimits structs; field semantics, parsing helpers, and architecture-specific constants.
*   [CLI and Configuration](https://deepwiki.com/tenstorrent/tt-toplike/2.2-cli-and-configuration) — Command-line argument reference (BackendType, display modes, --mock, --interval, --devices, --profile), AnimationProfile presets, and the ~/.config/tt-toplike/config.toml TOML override system.

Sources: [src/lib.rs 73-91](https://github.com/tenstorrent/tt-toplike/blob/487bfe5a/src/lib.rs#L73-L91)[src/config.rs 9-25](https://github.com/tenstorrent/tt-toplike/blob/487bfe5a/src/config.rs#L9-L25)[src/error.rs 17-42](https://github.com/tenstorrent/tt-toplike/blob/487bfe5a/src/error.rs#L17-L42)

Dismiss
Refresh this wiki

Enter email to refresh
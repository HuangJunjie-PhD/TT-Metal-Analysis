---
project: tt-toplike
github: https://github.com/tenstorrent/tt-toplike
deepwiki: https://deepwiki.com/tenstorrent/tt-toplike
---

# Overview

 Relevant source files

* [AGENTS.md](https://github.com/tenstorrent/tt-toplike/blob/487bfe5a/AGENTS.md?plain=1)
* [Cargo.toml](https://github.com/tenstorrent/tt-toplike/blob/487bfe5a/Cargo.toml)
* [QUICK\_START.md](https://github.com/tenstorrent/tt-toplike/blob/487bfe5a/QUICK_START.md?plain=1)
* [README.md](https://github.com/tenstorrent/tt-toplike/blob/487bfe5a/README.md?plain=1)

`tt-toplike` is a high-performance, real-time hardware monitoring suite for Tenstorrent silicon (Grayskull, Wormhole, and Blackhole architectures) [README.md1-3](https://github.com/tenstorrent/tt-toplike/blob/487bfe5a/README.md?plain=1#L1-L3) Written in Rust, it provides a low-overhead alternative to Python-based monitoring tools, offering multiple frontends including a terminal user interface (TUI) and graphical user interfaces (GUI) [Cargo.toml10-30](https://github.com/tenstorrent/tt-toplike/blob/487bfe5a/Cargo.toml#L10-L30)

The project is designed for hardware engineers, model developers, and system administrators who need to observe chip behavior—such as power consumption, thermal profiles, and DDR activity—during complex workloads like LLM inference or video diffusion [README.md45-60](https://github.com/tenstorrent/tt-toplike/blob/487bfe5a/README.md?plain=1#L45-L60)

### Key Capabilities

* **Multi-Backend Telemetry**: Supports non-invasive monitoring via Linux `hwmon` (sysfs), subprocess-based `tt-smi` JSON parsing, and direct PCI BAR0 access via the `luwen` library [QUICK\_START.md52-82](https://github.com/tenstorrent/tt-toplike/blob/487bfe5a/QUICK_START.md?plain=1#L52-L82)
* **Adaptive Visualization**: Features an `AdaptiveBaseline` system that learns the chip's idle state over the first 20 samples, rendering activity as relative changes rather than absolute values to ensure consistent visual intensity across hardware generations [README.md27-28](https://github.com/tenstorrent/tt-toplike/blob/487bfe5a/README.md?plain=1#L27-L28)
* **Workload Identification**: Detects active processes using Tenstorrent hardware by scanning `/proc` for open file descriptors and classifies inference states (e.g., Thinking, Generating, Stalled) [Cargo.toml130](https://github.com/tenstorrent/tt-toplike/blob/487bfe5a/Cargo.toml#L130-L130)
* **Advanced Visualizations**: Includes specialized modes like **Starfield** (Tensix core activity), **Memory Castle** (DDR hierarchy), and **Arcade Mode** (a unified compositor with a telemetry-driven "hero" character) [QUICK\_START.md8-49](https://github.com/tenstorrent/tt-toplike/blob/487bfe5a/QUICK_START.md?plain=1#L8-L49)

### System Architecture

The system is decoupled into three primary layers: the **Telemetry Backends**, the **Core Library**, and the **Frontends**.

#### System Component Mapping

The following diagram illustrates how high-level system concepts map to specific code entities and binaries.

**Component to Code Entity Map**

Sources: [Cargo.toml10-35](https://github.com/tenstorrent/tt-toplike/blob/487bfe5a/Cargo.toml#L10-L35) [Cargo.toml113-131](https://github.com/tenstorrent/tt-toplike/blob/487bfe5a/Cargo.toml#L113-L131) [src/lib.rs1-20](https://github.com/tenstorrent/tt-toplike/blob/487bfe5a/src/lib.rs#L1-L20)

### Subsystem Relationships

The `tt-toplike` library acts as the central coordinator. Frontends request telemetry from a `TelemetryBackend` implementation. The data is processed through the `AdaptiveBaseline` and `AnimationProfile` systems before being passed to the renderers.

**Data Flow and Subsystem Interaction**

Sources: [README.md13-28](https://github.com/tenstorrent/tt-toplike/blob/487bfe5a/README.md?plain=1#L13-L28) [QUICK\_START.md52-82](https://github.com/tenstorrent/tt-toplike/blob/487bfe5a/QUICK_START.md?plain=1#L52-L82) [Cargo.toml113-131](https://github.com/tenstorrent/tt-toplike/blob/487bfe5a/Cargo.toml#L113-L131)

### Documentation Map

For more detailed information, please refer to the following child pages:

* **[Getting Started](/tenstorrent/tt-toplike/1.1-getting-started)**: Installation via Debian packages or source, backend selection, and TUI keyboard shortcuts.
* **[Project Structure and Build System](/tenstorrent/tt-toplike/1.2-project-structure-and-build-system)**: Details on the workspace layout, Cargo feature flags (e.g., `luwen-backend`, `linux-procfs`), and the `vendor/` strategy for offline builds.

---

Sources: [AGENTS.md50-117](https://github.com/tenstorrent/tt-toplike/blob/487bfe5a/AGENTS.md?plain=1#L50-L117) [Cargo.toml1-153](https://github.com/tenstorrent/tt-toplike/blob/487bfe5a/Cargo.toml#L1-L153) [QUICK\_START.md1-149](https://github.com/tenstorrent/tt-toplike/blob/487bfe5a/QUICK_START.md?plain=1#L1-L149) [README.md1-85](https://github.com/tenstorrent/tt-toplike/blob/487bfe5a/README.md?plain=1#L1-L85)

Refresh this wiki
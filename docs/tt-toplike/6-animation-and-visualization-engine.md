---
project: tt-toplike
github: https://github.com/tenstorrent/tt-toplike
deepwiki: https://deepwiki.com/tenstorrent/tt-toplike/6-animation-and-visualization-engine
---

# Animation and Visualization Engine

Relevant source files
*   [src/animation/baseline.rs](https://github.com/tenstorrent/tt-toplike/blob/487bfe5a/src/animation/baseline.rs)
*   [src/animation/common.rs](https://github.com/tenstorrent/tt-toplike/blob/487bfe5a/src/animation/common.rs)
*   [src/animation/mod.rs](https://github.com/tenstorrent/tt-toplike/blob/487bfe5a/src/animation/mod.rs)

The `tt-toplike` animation engine is built on the philosophy that **every visual element must be informationally meaningful**[src/animation/mod.rs 18-19](https://github.com/tenstorrent/tt-toplike/blob/487bfe5a/src/animation/mod.rs#L18-L19) Unlike traditional monitoring tools that use static icons or decorative animations, every flicker, color shift, and particle movement in `tt-toplike` is a direct reflection of real-time hardware telemetry [src/animation/mod.rs 7-12](https://github.com/tenstorrent/tt-toplike/blob/487bfe5a/src/animation/mod.rs#L7-L12)

The engine draws aesthetic inspiration from 1990s BBS ANSI art, LZX video synthesis, and 1960s psychedelic visuals to represent complex silicon behavior in an intuitive, "glanceable" format [src/animation/mod.rs 13-16](https://github.com/tenstorrent/tt-toplike/blob/487bfe5a/src/animation/mod.rs#L13-L16)

## System Architecture

The visualization engine sits between the `TelemetryBackend` and the UI rendering layers (TUI/GUI). It processes raw metrics through an adaptive learning system before passing them to specialized visualizers.

### Logic Flow: Telemetry to Visuals

The following diagram shows how raw telemetry data (e.g., Watts, Amps, Celsius) is transformed into visual properties (Hue, Brightness, Velocity).

Sources: [src/animation/mod.rs 21-36](https://github.com/tenstorrent/tt-toplike/blob/487bfe5a/src/animation/mod.rs#L21-L36)[src/animation/baseline.rs 170-186](https://github.com/tenstorrent/tt-toplike/blob/487bfe5a/src/animation/baseline.rs#L170-L186)[src/animation/common.rs 105-108](https://github.com/tenstorrent/tt-toplike/blob/487bfe5a/src/animation/common.rs#L105-L108)

## Core Components

### Adaptive Baseline and Color System

The `AdaptiveBaseline` system solves the problem of hardware variability. Instead of using fixed thresholds, it learns the "idle" state of a device over the first 20 samples [src/animation/baseline.rs 7-9](https://github.com/tenstorrent/tt-toplike/blob/487bfe5a/src/animation/baseline.rs#L7-L9) This allows the engine to visualize a 2W spike on a low-power chip with the same intensity as a 20W spike on a high-power chip [src/animation/baseline.rs 11-12](https://github.com/tenstorrent/tt-toplike/blob/487bfe5a/src/animation/baseline.rs#L11-L12)

The `common` module provides the mathematical foundation for the "psychedelic" aesthetic, including HSV-to-RGB conversions and temperature-to-hue mapping [src/animation/common.rs 34-59](https://github.com/tenstorrent/tt-toplike/blob/487bfe5a/src/animation/common.rs#L34-L59)

For details, see [Adaptive Baseline and Color System](https://deepwiki.com/tenstorrent/tt-toplike/6.1-adaptive-baseline-and-color-system).

### Starfield and Topology Visualizations

The `HardwareStarfield` maps Tensix cores to stars. The brightness of a star reflects power consumption, while its "twinkle" or flicker rate is tied to current draw [src/animation/starfield.rs 34-35](https://github.com/tenstorrent/tt-toplike/blob/487bfe5a/src/animation/starfield.rs#L34-L35)`BoardTopology` tracks `sync_score` to visualize how tightly coupled multiple chips are during a workload [src/animation/topology.rs 35-36](https://github.com/tenstorrent/tt-toplike/blob/487bfe5a/src/animation/topology.rs#L35-L36)

For details, see [Starfield and Topology Visualizations](https://deepwiki.com/tenstorrent/tt-toplike/6.2-starfield-and-topology-visualizations).

### Memory Castle and Memory Flow

The `MemoryCastle` uses CP437 ANSI art to represent the memory hierarchy as a physical structure [src/animation/common.rs 134-136](https://github.com/tenstorrent/tt-toplike/blob/487bfe5a/src/animation/common.rs#L134-L136) It tracks up to 600 `MemoryParticle` instances representing Read/Write operations, Cache Hits, and Misses [src/animation/memory_castle.rs 24-32](https://github.com/tenstorrent/tt-toplike/blob/487bfe5a/src/animation/memory_castle.rs#L24-L32)`MemoryFlowVis` visualizes the data streams moving between the DDR perimeter and the Tensix grid.

For details, see [Memory Castle and Memory Flow](https://deepwiki.com/tenstorrent/tt-toplike/6.3-memory-castle-and-memory-flow).

### Arcade Mode

The `ArcadeVisualization` is a compositor that combines multiple visualization systems into a single "game-like" view [src/animation/arcade.rs 29-30](https://github.com/tenstorrent/tt-toplike/blob/487bfe5a/src/animation/arcade.rs#L29-L30) It features a "Hero" character (`@`) whose movement on the screen is controlled by hardware activity: the Y-axis maps to power and the X-axis maps to current draw.

For details, see [Arcade Mode](https://deepwiki.com/tenstorrent/tt-toplike/6.4-arcade-mode).

## Data Mapping Reference

The engine uses specific character sets and color mappings to represent different hardware states:

| Visual Element | Code Entity | Hardware Mapping |
| --- | --- | --- |
| **Star Brightness** | `Star::brightness` | Power Consumption (Watts) |
| **Star Color** | `temp_to_hue()` | Temperature (Celsius) |
| **Star Twinkle** | `Star::twinkle` | Current Draw (Amps) |
| **Particle Type** | `MemoryParticle` | Read / Write / Hit / Miss |
| **Castle Windows** | `WINDOW_CHARS` | L1 SRAM Activity |
| **Portals** | `PORTAL_CHARS` | Wormhole Architecture NoC |

Sources: [src/animation/common.rs 105-108](https://github.com/tenstorrent/tt-toplike/blob/487bfe5a/src/animation/common.rs#L105-L108)[src/animation/common.rs 147-148](https://github.com/tenstorrent/tt-toplike/blob/487bfe5a/src/animation/common.rs#L147-L148)[src/animation/common.rs 153-154](https://github.com/tenstorrent/tt-toplike/blob/487bfe5a/src/animation/common.rs#L153-L154)[src/animation/starfield.rs 34-35](https://github.com/tenstorrent/tt-toplike/blob/487bfe5a/src/animation/starfield.rs#L34-L35)

## Code Entity Association

This diagram maps the conceptual visualization layers to the specific Rust structs and enums that implement them.

Sources: [src/animation/baseline.rs 21-48](https://github.com/tenstorrent/tt-toplike/blob/487bfe5a/src/animation/baseline.rs#L21-L48)[src/animation/baseline.rs 170-186](https://github.com/tenstorrent/tt-toplike/blob/487bfe5a/src/animation/baseline.rs#L170-L186)[src/animation/starfield.rs 34-35](https://github.com/tenstorrent/tt-toplike/blob/487bfe5a/src/animation/starfield.rs#L34-L35)[src/animation/arcade.rs 29-30](https://github.com/tenstorrent/tt-toplike/blob/487bfe5a/src/animation/arcade.rs#L29-L30)

Dismiss
Refresh this wiki

Enter email to refresh
---
project: tt-toplike
github: https://github.com/tenstorrent/tt-toplike
deepwiki: https://deepwiki.com/tenstorrent/tt-toplike/10-glossary
---

# Glossary

Relevant source files
*   [AGENTS.md](https://github.com/tenstorrent/tt-toplike/blob/487bfe5a/AGENTS.md?plain=1)
*   [Cargo.toml](https://github.com/tenstorrent/tt-toplike/blob/487bfe5a/Cargo.toml)
*   [src/animation/arcade.rs](https://github.com/tenstorrent/tt-toplike/blob/487bfe5a/src/animation/arcade.rs)
*   [src/animation/baseline.rs](https://github.com/tenstorrent/tt-toplike/blob/487bfe5a/src/animation/baseline.rs)
*   [src/backend/hybrid.rs](https://github.com/tenstorrent/tt-toplike/blob/487bfe5a/src/backend/hybrid.rs)
*   [src/backend/mod.rs](https://github.com/tenstorrent/tt-toplike/blob/487bfe5a/src/backend/mod.rs)
*   [src/models/device.rs](https://github.com/tenstorrent/tt-toplike/blob/487bfe5a/src/models/device.rs)
*   [src/models/telemetry.rs](https://github.com/tenstorrent/tt-toplike/blob/487bfe5a/src/models/telemetry.rs)
*   [src/ui/tui/chip_portrait.rs](https://github.com/tenstorrent/tt-toplike/blob/487bfe5a/src/ui/tui/chip_portrait.rs)
*   [src/workload/inference.rs](https://github.com/tenstorrent/tt-toplike/blob/487bfe5a/src/workload/inference.rs)

This glossary defines codebase-specific terms, hardware jargon, and Rust-specific concepts used within the `tt-toplike` project. It serves as a technical reference for onboarding engineers to understand the relationship between physical hardware metrics and their representation in code.

## Core Concepts & Jargon

| Term | Definition | Code Reference |
| --- | --- | --- |
| **Adaptive Baseline** | A system that learns the "idle" state of a device over the first 20 samples to calculate relative activity. | [src/animation/baseline.rs 170-186](https://github.com/tenstorrent/tt-toplike/blob/487bfe5a/src/animation/baseline.rs#L170-L186) |
| **AICLK** | The AI Clock frequency of the Tenstorrent ASIC, typically measured in MHz. | [src/models/telemetry.rs 31-32](https://github.com/tenstorrent/tt-toplike/blob/487bfe5a/src/models/telemetry.rs#L31-L32) |
| **ARC** | Argonaut RISC Core. These are the management processors on the chip. Their "heartbeat" is used to detect hardware stalls. | [src/models/telemetry.rs 34-35](https://github.com/tenstorrent/tt-toplike/blob/487bfe5a/src/models/telemetry.rs#L34-L35) |
| **EMA Smoothing** | Exponential Moving Average. Used in the Hybrid backend to prevent visual "pops" when slow SMBUS data updates. | [src/backend/hybrid.rs 27-33](https://github.com/tenstorrent/tt-toplike/blob/487bfe5a/src/backend/hybrid.rs#L27-L33) |
| **Harvesting** | The process of disabling specific Tensix rows/columns at the factory. The UI must respect these bitmasks to avoid rendering "dead" cores. | [src/ui/tui/chip_portrait.rs 11-12](https://github.com/tenstorrent/tt-toplike/blob/487bfe5a/src/ui/tui/chip_portrait.rs#L11-L12) |
| **Hybrid Backend** | The default backend that combines high-frequency `sysfs` reads with low-frequency `tt-smi` JSON enrichment. | [src/backend/hybrid.rs 4-15](https://github.com/tenstorrent/tt-toplike/blob/487bfe5a/src/backend/hybrid.rs#L4-L15) |
| **Luwen** | A low-level library for direct PCI BAR0 access. In `tt-toplike`, it is an optional, invasive backend. | [Cargo.toml 37-42](https://github.com/tenstorrent/tt-toplike/blob/487bfe5a/Cargo.toml#L37-L42) |
| **SMBUS** | System Management Bus. Used to retrieve persistent metadata like Board IDs and Firmware versions. | [src/models/telemetry.rs 93-98](https://github.com/tenstorrent/tt-toplike/blob/487bfe5a/src/models/telemetry.rs#L93-L98) |
| **Tensix** | The proprietary compute cores in Tenstorrent architectures (Grayskull, Wormhole, Blackhole). | [src/models/device.rs 15-17](https://github.com/tenstorrent/tt-toplike/blob/487bfe5a/src/models/device.rs#L15-L17) |

* * *

## Hardware Architectures

The project supports three generations of Tenstorrent hardware, each defined in the `Architecture` enum [src/models/device.rs 19-28](https://github.com/tenstorrent/tt-toplike/blob/487bfe5a/src/models/device.rs#L19-L28)

| Architecture | Abbrev | Grid (R x C) | DDR Channels |
| --- | --- | --- | --- |
| **Grayskull** | GS | 10 x 12 | 4 |
| **Wormhole** | WH | 8 x 10 | 8 |
| **Blackhole** | BH | 14 x 16 | 12 |

**Sources:**[src/models/device.rs 51-69](https://github.com/tenstorrent/tt-toplike/blob/487bfe5a/src/models/device.rs#L51-L69)[src/ui/tui/chip_portrait.rs 16-23](https://github.com/tenstorrent/tt-toplike/blob/487bfe5a/src/ui/tui/chip_portrait.rs#L16-L23)

* * *

## Data Flow: From Hardware to UI

The following diagram illustrates how raw telemetry data travels from the hardware backends through the `TelemetryBackend` trait into the visualizers.

**Telemetry Data Pipeline**

**Sources:**[src/backend/mod.rs 86-110](https://github.com/tenstorrent/tt-toplike/blob/487bfe5a/src/backend/mod.rs#L86-L110)[src/backend/hybrid.rs 64-116](https://github.com/tenstorrent/tt-toplike/blob/487bfe5a/src/backend/hybrid.rs#L64-L116)[src/animation/baseline.rs 188-196](https://github.com/tenstorrent/tt-toplike/blob/487bfe5a/src/animation/baseline.rs#L188-L196)[src/workload/inference.rs 123-133](https://github.com/tenstorrent/tt-toplike/blob/487bfe5a/src/workload/inference.rs#L123-L133)

* * *

## Inference State Classification

The `InferenceEngine` classifies hardware activity into 13 distinct states based on power variance and telemetry trends.

| State | Logic / Code Trigger | Label |
| --- | --- | --- |
| `Stalled` | ARC heartbeat == 0 or frozen for 30 frames. | **GONE SILENT** |
| `ThermalStress` | ASIC Temperature > 82°C. | **SCORCHING** |
| `Thinking` | High power variance (prefill/attention). | **DREAMING DEEP** |
| `Generating` | Low variance, high current-to-power ratio (decode). | **CONJURING** |
| `Throttling` | Throttler register set or AICLK dropped > 20%. | **REINED IN** |

**Sources:**[src/workload/inference.rs 41-55](https://github.com/tenstorrent/tt-toplike/blob/487bfe5a/src/workload/inference.rs#L41-L55)[src/workload/inference.rs 88-102](https://github.com/tenstorrent/tt-toplike/blob/487bfe5a/src/workload/inference.rs#L88-L102)

* * *

## Visualizer Components

Visualizers translate numerical changes (relative to the `AdaptiveBaseline`) into character-art animations.

**Visualizer Class Hierarchy**

**Sources:**[src/animation/arcade.rs 41-71](https://github.com/tenstorrent/tt-toplike/blob/487bfe5a/src/animation/arcade.rs#L41-L71)[src/ui/tui/chip_portrait.rs 27-36](https://github.com/tenstorrent/tt-toplike/blob/487bfe5a/src/ui/tui/chip_portrait.rs#L27-L36)[src/animation/baseline.rs 104-125](https://github.com/tenstorrent/tt-toplike/blob/487bfe5a/src/animation/baseline.rs#L104-L125)

### Key Animation Terms

*   **Hero Character (@):** A character in Arcade mode whose Y-position is driven by `power_change` and X-position by `current_change`[src/animation/arcade.rs 15-20](https://github.com/tenstorrent/tt-toplike/blob/487bfe5a/src/animation/arcade.rs#L15-L20)
*   **Particle Kind:** In the Chip Portrait and Memory Castle, particles are typed as `Read` (Teal) or `Write` (Pink) to visualize data direction [src/ui/tui/chip_portrait.rs 38-44](https://github.com/tenstorrent/tt-toplike/blob/487bfe5a/src/ui/tui/chip_portrait.rs#L38-L44)
*   **PowerTrend:** A struct calculating the slope and variance of power consumption over a 30-sample rolling window [src/workload/inference.rs 25-37](https://github.com/tenstorrent/tt-toplike/blob/487bfe5a/src/workload/inference.rs#L25-L37)

* * *

**Sources:**

*   `src/backend/hybrid.rs:1-116`
*   `src/backend/mod.rs:1-110`
*   `src/models/device.rs:1-125`
*   `src/models/telemetry.rs:1-100`
*   `src/animation/baseline.rs:1-196`
*   `src/animation/arcade.rs:1-133`
*   `src/workload/inference.rs:1-133`
*   `src/ui/tui/chip_portrait.rs:1-115`
*   `Cargo.toml:1-132`

Dismiss
Refresh this wiki

Enter email to refresh
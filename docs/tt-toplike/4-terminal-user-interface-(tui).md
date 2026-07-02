---
project: tt-toplike
github: https://github.com/tenstorrent/tt-toplike
deepwiki: https://deepwiki.com/tenstorrent/tt-toplike/4-terminal-user-interface-(tui)
---

# Terminal User Interface (TUI)

Relevant source files
*   [src/bin/tui.rs](https://github.com/tenstorrent/tt-toplike/blob/487bfe5a/src/bin/tui.rs)
*   [src/ui/colors.rs](https://github.com/tenstorrent/tt-toplike/blob/487bfe5a/src/ui/colors.rs)
*   [src/ui/tui/mod.rs](https://github.com/tenstorrent/tt-toplike/blob/487bfe5a/src/ui/tui/mod.rs)

The `tt-toplike` Terminal User Interface (TUI) is a high-performance, real-time monitoring dashboard built using the `ratatui` and `crossterm` libraries. It provides hardware-responsive visualizations and detailed telemetry for Tenstorrent hardware, designed to operate efficiently even over SSH connections.

## Architecture Overview

The TUI operates on a decoupled architecture where the rendering layer is separated from the telemetry collection backends. It utilizes a 60 FPS event loop for smooth animations, independent of the backend's data sampling rate.

### TUI Component Relationship

This diagram shows how the TUI coordinates between the user, the backend data, and the specialized rendering screens.

**Sources:**[src/bin/tui.rs 171-180](https://github.com/tenstorrent/tt-toplike/blob/487bfe5a/src/bin/tui.rs#L171-L180)[src/ui/tui/mod.rs 49-64](https://github.com/tenstorrent/tt-toplike/blob/487bfe5a/src/ui/tui/mod.rs#L49-L64)[src/ui/tui/mod.rs 145-164](https://github.com/tenstorrent/tt-toplike/blob/487bfe5a/src/ui/tui/mod.rs#L145-L164)

## Core Features

### Display Modes

The TUI supports several distinct display modes, toggled via keyboard shortcuts. These modes range from dense information grids to abstract hardware-responsive animations.

| Mode | Enum Variant | Key | Description |
| --- | --- | --- | --- |
| **Insights** | `DisplayMode::Insights` | `i` | Human-readable inference stats and process panel. |
| **Grid** | `DisplayMode::Grid` | `g` | Multi-chip portrait view for high-density monitoring. |
| **Starfield** | `DisplayMode::Starfield` | `s` | Psychedelic animation where stars represent Tensix cores. |
| **Memory Castle** | `DisplayMode::MemoryCastle` | `c` | Architectural memory hierarchy visualization. |
| **Memory Flow** | `DisplayMode::MemoryFlow` | `f` | Full-screen DRAM motion visualization. |
| **Arcade** | `DisplayMode::Arcade` | `a` | Unified compositor combining multiple visualizations. |

**Sources:**[src/ui/tui/mod.rs 49-64](https://github.com/tenstorrent/tt-toplike/blob/487bfe5a/src/ui/tui/mod.rs#L49-L64)[src/ui/tui/mod.rs 270-320](https://github.com/tenstorrent/tt-toplike/blob/487bfe5a/src/ui/tui/mod.rs#L270-L320)

### Event Loop and Rendering

The `run_app` function manages the primary execution loop [src/ui/tui/mod.rs 145-164](https://github.com/tenstorrent/tt-toplike/blob/487bfe5a/src/ui/tui/mod.rs#L145-L164)

*   **UI Refresh Rate:** Fixed at ~16ms (60 FPS) to ensure smooth animations [src/ui/tui/mod.rs 164](https://github.com/tenstorrent/tt-toplike/blob/487bfe5a/src/ui/tui/mod.rs#L164-L164)
*   **Backend Sync:** Telemetry is fetched based on the `cli.interval` (default 500ms), independent of the frame rate [src/ui/tui/mod.rs 152-153](https://github.com/tenstorrent/tt-toplike/blob/487bfe5a/src/ui/tui/mod.rs#L152-L153)
*   **Adaptive Baseline:** The UI uses an `AdaptiveBaseline` system to learn "idle" hardware states, allowing visualizations to scale their sensitivity automatically [src/bin/tui.rs 10-15](https://github.com/tenstorrent/tt-toplike/blob/487bfe5a/src/bin/tui.rs#L10-L15)

## Keyboard Shortcuts

The TUI is controlled entirely via keyboard. The following global bindings are available:

*   **`q` / `Esc` / `Ctrl+C`**: Quit the application.
*   **`Tab`**: Cycle through all available display modes.
*   **`0-9`**: Select a specific device for detailed view (if applicable).
*   **`r`**: Reset the `AdaptiveBaseline` (useful if hardware state changes significantly).
*   **`k`**: (Insights mode) Initiate process kill confirmation for the selected workload.

**Sources:**[src/ui/tui/mod.rs 250-350](https://github.com/tenstorrent/tt-toplike/blob/487bfe5a/src/ui/tui/mod.rs#L250-L350)

## Subsystems and Child Pages

### [Insights Screen and Chip Portrait](https://deepwiki.com/tenstorrent/tt-toplike/4.1-insights-screen-and-chip-portrait)

The default "Insights" view provides a high-level overview of chip health and workload status. It features the "Chip Portrait," a character-art grid representing Tensix cores, ETH links, and DRAM banks. For details, see [Insights Screen and Chip Portrait](https://deepwiki.com/tenstorrent/tt-toplike/4.1-insights-screen-and-chip-portrait).

### [Visualization Modes](https://deepwiki.com/tenstorrent/tt-toplike/4.2-visualization-modes)

`tt-toplike` includes several full-screen animation engines. These modes translate raw telemetry (power, temperature, current) into visual parameters like particle velocity, color hue, and twinkle frequency. For details, see [Visualization Modes](https://deepwiki.com/tenstorrent/tt-toplike/4.2-visualization-modes).

### [SSH and Color Support](https://deepwiki.com/tenstorrent/tt-toplike/4.3-ssh-and-color-support)

The TUI includes sophisticated color detection logic to handle various terminal environments. It supports 24-bit TrueColor (RGB) but can automatically fall back to a 256-color indexed palette for compatibility with `tmux` or older SSH clients. For details, see [SSH and Color Support](https://deepwiki.com/tenstorrent/tt-toplike/4.3-ssh-and-color-support).

## Data Flow Diagram

This diagram illustrates how data flows from the hardware backends through the TUI rendering pipeline.

**Sources:**[src/ui/tui/mod.rs 171-220](https://github.com/tenstorrent/tt-toplike/blob/487bfe5a/src/ui/tui/mod.rs#L171-L220)[src/ui/tui/mod.rs 360-400](https://github.com/tenstorrent/tt-toplike/blob/487bfe5a/src/ui/tui/mod.rs#L360-L400)

Dismiss
Refresh this wiki

Enter email to refresh
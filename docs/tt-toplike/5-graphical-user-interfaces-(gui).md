---
project: tt-toplike
github: https://github.com/tenstorrent/tt-toplike
deepwiki: https://deepwiki.com/tenstorrent/tt-toplike/5-graphical-user-interfaces-(gui)
---

# Graphical User Interfaces (GUI)

Relevant source files
*   [docs/GUI_FEATURES.md](https://github.com/tenstorrent/tt-toplike/blob/487bfe5a/docs/GUI_FEATURES.md?plain=1)
*   [src/bin/app.rs](https://github.com/tenstorrent/tt-toplike/blob/487bfe5a/src/bin/app.rs)
*   [src/bin/egui.rs](https://github.com/tenstorrent/tt-toplike/blob/487bfe5a/src/bin/egui.rs)
*   [src/bin/gui.rs](https://github.com/tenstorrent/tt-toplike/blob/487bfe5a/src/bin/gui.rs)

The `tt-toplike` project provides two distinct native Graphical User Interface (GUI) frontends that complement the primary Terminal User Interface (TUI). These frontends leverage the same shared telemetry backend and animation engines to provide a rich, windowed experience on Linux desktops (Wayland/X11).

## Overview of GUI Frontends

The project splits its GUI offerings into two main binary targets, each serving a different use case:

| Feature | `tt-toplike-gui` (iced) | `tt-toplike-egui` / `tt-toplike-app` |
| --- | --- | --- |
| **Framework** | [iced](https://iced.rs/) 0.14 | [egui](https://github.com/tenstorrent/tt-toplike/blob/487bfe5a/egui) / [eframe](https://github.com/tenstorrent/tt-toplike/blob/487bfe5a/eframe) |
| **Primary Goal** | High-fidelity visualization & charts | Lightweight dashboard & TUI hosting |
| **Rendering** | GPU-accelerated `iced::Canvas` | `egui` Painter / `LayoutJob` |
| **Key Views** | Starfield, History Charts, Dashboard | Real-time Metrics, PTY Terminal Host |
| **Backend** | Shared `TelemetryBackend` trait | Shared `TelemetryBackend` trait |

### System Architecture

The following diagram illustrates how the GUI components interface with the core library and the hardware backends.

**GUI Integration Diagram**

Sources: [src/bin/gui.rs 52-85](https://github.com/tenstorrent/tt-toplike/blob/487bfe5a/src/bin/gui.rs#L52-L85)[src/bin/egui.rs 11-14](https://github.com/tenstorrent/tt-toplike/blob/487bfe5a/src/bin/egui.rs#L11-L14)[src/bin/app.rs 5-15](https://github.com/tenstorrent/tt-toplike/blob/487bfe5a/src/bin/app.rs#L5-L15)[docs/GUI_FEATURES.md 168-187](https://github.com/tenstorrent/tt-toplike/blob/487bfe5a/docs/GUI_FEATURES.md?plain=1#L168-L187)

* * *

## iced GUI (tt-toplike-gui)

The `tt-toplike-gui` binary is the most feature-rich graphical frontend. It is built using the **iced** framework and focuses on providing a high-performance, GPU-accelerated monitoring experience.

### Key Features

*   **Multi-View Modes**: Users can switch between a high-level `Dashboard`, a detailed `Table`, historical `Charts`, and the `Starfield` visualization [src/bin/gui.rs 39-49](https://github.com/tenstorrent/tt-toplike/blob/487bfe5a/src/bin/gui.rs#L39-L49)
*   **History Management**: Utilizes a `HistoryManager` to maintain a circular buffer of 300 samples (30 seconds of data at 100ms intervals) per device [docs/GUI_FEATURES.md 153-159](https://github.com/tenstorrent/tt-toplike/blob/487bfe5a/docs/GUI_FEATURES.md?plain=1#L153-L159)
*   **GPU Acceleration**: The `Starfield` and `Dashboard` views use `iced::Canvas` for Vulkan/OpenGL accelerated rendering [docs/GUI_FEATURES.md 162-167](https://github.com/tenstorrent/tt-toplike/blob/487bfe5a/docs/GUI_FEATURES.md?plain=1#L162-L167)
*   **Device Tabs**: Supports multi-device systems with independent telemetry history and visualizations for each chip [src/bin/gui.rs 77-84](https://github.com/tenstorrent/tt-toplike/blob/487bfe5a/src/bin/gui.rs#L77-L84)

For details on rendering performance, view modes, and hardware starfield implementation, see **[iced GUI (tt-toplike-gui)](https://deepwiki.com/tenstorrent/tt-toplike/5.1-iced-gui-(tt-toplike-gui))**.

Sources: [src/bin/gui.rs 106-160](https://github.com/tenstorrent/tt-toplike/blob/487bfe5a/src/bin/gui.rs#L106-L160)[docs/GUI_FEATURES.md 9-70](https://github.com/tenstorrent/tt-toplike/blob/487bfe5a/docs/GUI_FEATURES.md?plain=1#L9-L70)

* * *

## egui Dashboard and App Window

The `egui`-based tools provide a lightweight alternative to the main GUI, focusing on low overhead and portability.

### tt-toplike-egui

A streamlined dashboard focused on real-time metrics and multi-device comparison [src/bin/egui.rs 5-9](https://github.com/tenstorrent/tt-toplike/blob/487bfe5a/src/bin/egui.rs#L5-L9) It is designed to be a quick-launch utility for monitoring without the heavier dependencies or resource usage of the full `iced` GUI.

### tt-toplike-app (The PTY Host)

The `tt-toplike-app` binary is a unique hybrid. It uses `eframe` to create a native window that hosts a Pseudo-Terminal (PTY) [src/bin/app.rs 5-15](https://github.com/tenstorrent/tt-toplike/blob/487bfe5a/src/bin/app.rs#L5-L15)

*   **PTY Hosting**: It spawns the `tt-toplike-tui` as a child process [src/bin/app.rs 10](https://github.com/tenstorrent/tt-toplike/blob/487bfe5a/src/bin/app.rs#L10-L10)
*   **VT100 State Machine**: It implements a custom VT state machine to parse ANSI escape sequences into a `Screen` grid [src/bin/app.rs 68-82](https://github.com/tenstorrent/tt-toplike/blob/487bfe5a/src/bin/app.rs#L68-L82)
*   **Rendering**: The terminal grid is rendered using `egui`'s `LayoutJob` batching, providing a "native window" feel for the terminal-based visualizations [src/bin/app.rs 12-15](https://github.com/tenstorrent/tt-toplike/blob/487bfe5a/src/bin/app.rs#L12-L15)

**PTY Host Entity Map**

Sources: [src/bin/app.rs 17-23](https://github.com/tenstorrent/tt-toplike/blob/487bfe5a/src/bin/app.rs#L17-L23)[src/bin/app.rs 68-82](https://github.com/tenstorrent/tt-toplike/blob/487bfe5a/src/bin/app.rs#L68-L82)[src/bin/app.rs 240-260](https://github.com/tenstorrent/tt-toplike/blob/487bfe5a/src/bin/app.rs#L240-L260)

For details on the PTY implementation and the egui dashboard layout, see **[egui Dashboard and App Window (tt-toplike-egui / tt-toplike-app)](https://deepwiki.com/tenstorrent/tt-toplike/5.2-egui-dashboard-and-app-window-(tt-toplike-egui-tt-toplike-app))**.

* * *

## Shared Backend Integration

Both GUI frontends utilize the `factory::create_backend` pattern to initialize hardware access. They support all backend types, including `Luwen`, `JSON`, and `Mock`[src/bin/gui.rs 218-230](https://github.com/tenstorrent/tt-toplike/blob/487bfe5a/src/bin/gui.rs#L218-L230)

*   **Initialization**: Backends are initialized via CLI flags (e.g., `--mock` or `--backend json`) [src/bin/egui.rs 27-35](https://github.com/tenstorrent/tt-toplike/blob/487bfe5a/src/bin/egui.rs#L27-L35)
*   **Polling**: The GUIs typically poll the backend at a default interval of 100ms (10 FPS) to ensure smooth animations [docs/GUI_FEATURES.md 148-152](https://github.com/tenstorrent/tt-toplike/blob/487bfe5a/docs/GUI_FEATURES.md?plain=1#L148-L152)
*   **Thread Safety**: Telemetry data is updated in the main application loop and passed to visualization components [src/bin/gui.rs 171-196](https://github.com/tenstorrent/tt-toplike/blob/487bfe5a/src/bin/gui.rs#L171-L196)

Sources: [src/bin/gui.rs 107-160](https://github.com/tenstorrent/tt-toplike/blob/487bfe5a/src/bin/gui.rs#L107-L160)[src/bin/egui.rs 33-40](https://github.com/tenstorrent/tt-toplike/blob/487bfe5a/src/bin/egui.rs#L33-L40)

Dismiss
Refresh this wiki

Enter email to refresh
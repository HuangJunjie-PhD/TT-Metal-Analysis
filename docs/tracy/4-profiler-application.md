---
project: tracy
github: https://github.com/tenstorrent/tracy
deepwiki: https://deepwiki.com/tenstorrent/tracy/4-profiler-application
---

# Profiler Application

Relevant source files
*   [capture/build/win32/capture.vcxproj](https://github.com/tenstorrent/tracy/blob/68c5cbd6/capture/build/win32/capture.vcxproj)
*   [capture/build/win32/capture.vcxproj.filters](https://github.com/tenstorrent/tracy/blob/68c5cbd6/capture/build/win32/capture.vcxproj.filters)
*   [csvexport/build/win32/csvexport.vcxproj](https://github.com/tenstorrent/tracy/blob/68c5cbd6/csvexport/build/win32/csvexport.vcxproj)
*   [csvexport/build/win32/csvexport.vcxproj.filters](https://github.com/tenstorrent/tracy/blob/68c5cbd6/csvexport/build/win32/csvexport.vcxproj.filters)
*   [import-chrome/build/win32/import-chrome.vcxproj](https://github.com/tenstorrent/tracy/blob/68c5cbd6/import-chrome/build/win32/import-chrome.vcxproj)
*   [import-chrome/build/win32/import-chrome.vcxproj.filters](https://github.com/tenstorrent/tracy/blob/68c5cbd6/import-chrome/build/win32/import-chrome.vcxproj.filters)
*   [profiler/build/win32/Tracy.vcxproj](https://github.com/tenstorrent/tracy/blob/68c5cbd6/profiler/build/win32/Tracy.vcxproj)
*   [profiler/build/win32/Tracy.vcxproj.filters](https://github.com/tenstorrent/tracy/blob/68c5cbd6/profiler/build/win32/Tracy.vcxproj.filters)
*   [profiler/src/main.cpp](https://github.com/tenstorrent/tracy/blob/68c5cbd6/profiler/src/main.cpp)
*   [server/TracyEvent.hpp](https://github.com/tenstorrent/tracy/blob/68c5cbd6/server/TracyEvent.hpp)
*   [server/TracyView.cpp](https://github.com/tenstorrent/tracy/blob/68c5cbd6/server/TracyView.cpp)
*   [server/TracyView.hpp](https://github.com/tenstorrent/tracy/blob/68c5cbd6/server/TracyView.hpp)
*   [server/TracyWorker.cpp](https://github.com/tenstorrent/tracy/blob/68c5cbd6/server/TracyWorker.cpp)
*   [server/TracyWorker.hpp](https://github.com/tenstorrent/tracy/blob/68c5cbd6/server/TracyWorker.hpp)
*   [update/build/win32/update.vcxproj](https://github.com/tenstorrent/tracy/blob/68c5cbd6/update/build/win32/update.vcxproj)
*   [update/build/win32/update.vcxproj.filters](https://github.com/tenstorrent/tracy/blob/68c5cbd6/update/build/win32/update.vcxproj.filters)
*   [update/src/update.cpp](https://github.com/tenstorrent/tracy/blob/68c5cbd6/update/src/update.cpp)

The Tracy Profiler Application is the primary GUI tool for visualizing and analyzing profiling data collected by Tracy. It provides a comprehensive interface for examining performance traces either from live instrumented applications or from saved Tracy dump files. The application features real-time timeline visualization, detailed performance statistics, memory analysis, and various specialized analysis tools.

For information about instrumenting applications to generate profiling data, see [Client Library](https://deepwiki.com/tenstorrent/tracy/3-client-library). For details about Tracy's file formats and I/O capabilities, see [File System & I/O](https://deepwiki.com/tenstorrent/tracy/5-file-system-and-io).

## Application Architecture

The profiler application follows a layered architecture with clear separation between the UI layer, data processing engine, and platform abstraction. The main components work together to provide a responsive and feature-rich profiling experience.

**Application Entry Point and Initialization**

The profiler application is built around a modular architecture that supports multiple platforms and rendering backends. The main application lifecycle handles initialization, event processing, and cleanup.

Sources: [profiler/src/main.cpp 215-367](https://github.com/tenstorrent/tracy/blob/68c5cbd6/profiler/src/main.cpp#L215-L367)

## Core Application Components

### Main Application Structure

The `main` function in [profiler/src/main.cpp 215-367](https://github.com/tenstorrent/tracy/blob/68c5cbd6/profiler/src/main.cpp#L215-L367) serves as the application entry point and orchestrates the initialization of all major subsystems. It handles command-line argument parsing, platform-specific setup, and creates the main application loop.

The application supports two primary modes of operation:

*   **File Mode**: Loading and analyzing saved `.tracy` files
*   **Connection Mode**: Real-time profiling of live applications

Sources: [profiler/src/main.cpp 215-280](https://github.com/tenstorrent/tracy/blob/68c5cbd6/profiler/src/main.cpp#L215-L280)[profiler/src/main.cpp 340-348](https://github.com/tenstorrent/tracy/blob/68c5cbd6/profiler/src/main.cpp#L340-L348)

### TracyView - UI Coordinator

The `View` class defined in [server/TracyView.hpp 56-670](https://github.com/tenstorrent/tracy/blob/68c5cbd6/server/TracyView.hpp#L56-L670) serves as the central UI coordinator and state manager. It manages all user interface windows, handles user interactions, and coordinates data visualization.

The `View` class provides two constructors:

*   Live connection: `View(cbMainThread, addr, port, fonts, callbacks, config)` at [server/TracyView.hpp 107](https://github.com/tenstorrent/tracy/blob/68c5cbd6/server/TracyView.hpp#L107-L107)
*   File loading: `View(cbMainThread, fileRead, fonts, callbacks, config)` at [server/TracyView.hpp 108](https://github.com/tenstorrent/tracy/blob/68c5cbd6/server/TracyView.hpp#L108-L108)

**Key View Responsibilities:**

*   **Window Management**: Controls visibility and state of all UI windows
*   **User Interaction**: Handles mouse, keyboard, and menu interactions
*   **Data Visualization**: Coordinates rendering of timeline, statistics, and analysis views
*   **State Persistence**: Manages user preferences and view state through `UserData`

Sources: [server/TracyView.hpp 56-160](https://github.com/tenstorrent/tracy/blob/68c5cbd6/server/TracyView.hpp#L56-L160)[server/TracyView.cpp 47-122](https://github.com/tenstorrent/tracy/blob/68c5cbd6/server/TracyView.cpp#L47-L122)

### TracyWorker - Data Processing Engine

The `Worker` class in [server/TracyWorker.hpp 98-540](https://github.com/tenstorrent/tracy/blob/68c5cbd6/server/TracyWorker.hpp#L98-L540) is the core data processing engine that handles all profiling data ingestion, storage, and analysis. It provides a unified interface for both live data streams and file-based data.

**Worker Capabilities:**

*   **Live Data Ingestion**: Real-time processing of profiling data streams
*   **File Format Support**: Reading/writing Tracy's compressed binary format
*   **Data Analysis**: Statistical analysis and data aggregation
*   **Memory Management**: Efficient storage using custom slab allocators

Sources: [server/TracyWorker.hpp 455-458](https://github.com/tenstorrent/tracy/blob/68c5cbd6/server/TracyWorker.hpp#L455-L458)[server/TracyWorker.cpp 263-304](https://github.com/tenstorrent/tracy/blob/68c5cbd6/server/TracyWorker.cpp#L263-L304)

## Data Flow and Processing Pipeline

The profiler application processes data through a well-defined pipeline that handles both live streaming data and file-based data uniformly.

**Processing Stages:**

1.   **Data Ingestion**: Network streams or file reading with decompression
2.   **Event Processing**: Parsing and validation of profiling events
3.   **Storage**: Efficient storage in memory-mapped structures
4.   **Analysis**: Statistical computation and data indexing
5.   **Visualization**: Rendering through ImGui-based UI components

Sources: [server/TracyWorker.cpp 306-555](https://github.com/tenstorrent/tracy/blob/68c5cbd6/server/TracyWorker.cpp#L306-L555)[server/TracyView.cpp 673-800](https://github.com/tenstorrent/tracy/blob/68c5cbd6/server/TracyView.cpp#L673-L800)

## User Interface Framework Integration

The profiler application is built on ImGui (Immediate Mode GUI) with extensive customizations for profiling-specific visualizations. The UI framework provides high-performance rendering of complex timeline data and interactive analysis tools.

**Key UI Features:**

*   **High-Performance Timeline**: Custom rendering for millions of timeline events
*   **Interactive Navigation**: Zoom, pan, and selection tools for timeline exploration
*   **Multi-Window Layout**: Flexible window management with docking support
*   **DPI Awareness**: Automatic scaling for high-DPI displays
*   **Platform Integration**: Native file dialogs and system integration

Sources: [profiler/src/main.cpp 137-175](https://github.com/tenstorrent/tracy/blob/68c5cbd6/profiler/src/main.cpp#L137-L175)[server/TracyView.cpp 742-774](https://github.com/tenstorrent/tracy/blob/68c5cbd6/server/TracyView.cpp#L742-L774)

## Application Workflows

### Live Connection Workflow

When connecting to a live instrumented application, the profiler establishes a TCP connection and begins real-time data processing.

Sources: [server/TracyWorker.cpp 263-304](https://github.com/tenstorrent/tracy/blob/68c5cbd6/server/TracyWorker.cpp#L263-L304)[server/TracyView.cpp 47-71](https://github.com/tenstorrent/tracy/blob/68c5cbd6/server/TracyView.cpp#L47-L71)

### File Loading Workflow

Loading saved Tracy files involves decompression, data validation, and progressive loading of large datasets.

Sources: [server/TracyWorker.cpp 557-863](https://github.com/tenstorrent/tracy/blob/68c5cbd6/server/TracyWorker.cpp#L557-L863)[server/TracyView.cpp 73-106](https://github.com/tenstorrent/tracy/blob/68c5cbd6/server/TracyView.cpp#L73-L106)

Dismiss
Refresh this wiki

Enter email to refresh
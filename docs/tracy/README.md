---
project: tracy
github: https://github.com/tenstorrent/tracy
deepwiki: https://deepwiki.com/tenstorrent/tracy
---

# Overview

Relevant source files
*   [LICENSE](https://github.com/tenstorrent/tracy/blob/68c5cbd6/LICENSE)
*   [NEWS](https://github.com/tenstorrent/tracy/blob/68c5cbd6/NEWS)
*   [README.md](https://github.com/tenstorrent/tracy/blob/68c5cbd6/README.md?plain=1)
*   [import-chrome/src/import-chrome.cpp](https://github.com/tenstorrent/tracy/blob/68c5cbd6/import-chrome/src/import-chrome.cpp)
*   [manual/techdoc.tex](https://github.com/tenstorrent/tracy/blob/68c5cbd6/manual/techdoc.tex)
*   [manual/tracy.tex](https://github.com/tenstorrent/tracy/blob/68c5cbd6/manual/tracy.tex)
*   [server/TracyEvent.hpp](https://github.com/tenstorrent/tracy/blob/68c5cbd6/server/TracyEvent.hpp)
*   [server/TracyView.cpp](https://github.com/tenstorrent/tracy/blob/68c5cbd6/server/TracyView.cpp)
*   [server/TracyView.hpp](https://github.com/tenstorrent/tracy/blob/68c5cbd6/server/TracyView.hpp)
*   [server/TracyWorker.cpp](https://github.com/tenstorrent/tracy/blob/68c5cbd6/server/TracyWorker.cpp)
*   [server/TracyWorker.hpp](https://github.com/tenstorrent/tracy/blob/68c5cbd6/server/TracyWorker.hpp)

Tracy is a real-time, nanosecond resolution profiling system implementing a client-server architecture for remote telemetry of applications. This document covers the overall system architecture, core components, and data flow patterns within the Tracy codebase.

For detailed information about client instrumentation APIs, see [Client Library](https://deepwiki.com/tenstorrent/tracy/3-client-library). For profiler GUI usage and analysis features, see [Profiler Application](https://deepwiki.com/tenstorrent/tracy/4-profiler-application). For build configuration and platform-specific details, see [Build System](https://deepwiki.com/tenstorrent/tracy/2.1-build-system).

## System Architecture

Tracy implements a distributed profiling architecture where instrumented client applications collect and stream profiling data to a server application that processes, stores, and visualizes the data in real-time.

### High-Level Component Overview

Sources: [server/TracyView.hpp 56-663](https://github.com/tenstorrent/tracy/blob/68c5cbd6/server/TracyView.hpp#L56-L663)[server/TracyWorker.hpp 98-411](https://github.com/tenstorrent/tracy/blob/68c5cbd6/server/TracyWorker.hpp#L98-L411)[manual/tracy.tex 236-268](https://github.com/tenstorrent/tracy/blob/68c5cbd6/manual/tracy.tex#L236-L268)

### Core Data Processing Pipeline

Sources: [server/TracyWorker.hpp 277-407](https://github.com/tenstorrent/tracy/blob/68c5cbd6/server/TracyWorker.hpp#L277-L407)[server/TracyView.cpp 47-121](https://github.com/tenstorrent/tracy/blob/68c5cbd6/server/TracyView.cpp#L47-L121)[server/TracyEvent.hpp 202-223](https://github.com/tenstorrent/tracy/blob/68c5cbd6/server/TracyEvent.hpp#L202-L223)

## Key System Components

### Client-Side Components

| Component | Purpose | Key Files |
| --- | --- | --- |
| `Profiler` | Central client coordinator managing data collection | `client/TracyProfiler.cpp` |
| Event Queues | Lock-free queues for high-performance data collection | Uses `moodycamel::ConcurrentQueue` |
| Worker Thread | Background thread handling network transmission | Part of `Profiler` class |
| Callstack Capture | Platform-specific stack unwinding | `client/TracyCallstack.cpp` |

### Server-Side Components

| Component | Purpose | Key Files |
| --- | --- | --- |
| `TracyWorker` | Core data processing and storage engine | [server/TracyWorker.hpp 98-1000](https://github.com/tenstorrent/tracy/blob/68c5cbd6/server/TracyWorker.hpp#L98-L1000) |
| `TracyView` | Main visualization controller and GUI coordinator | [server/TracyView.hpp 56-663](https://github.com/tenstorrent/tracy/blob/68c5cbd6/server/TracyView.hpp#L56-L663) |
| `DataBlock` | Primary in-memory data structure | [server/TracyWorker.hpp 277-407](https://github.com/tenstorrent/tracy/blob/68c5cbd6/server/TracyWorker.hpp#L277-L407) |
| Timeline System | Rendering and interaction for profiling timeline | [server/TracyTimelineController.hpp](https://github.com/tenstorrent/tracy/blob/68c5cbd6/server/TracyTimelineController.hpp) |

Sources: [server/TracyWorker.hpp 98-1000](https://github.com/tenstorrent/tracy/blob/68c5cbd6/server/TracyWorker.hpp#L98-L1000)[server/TracyView.hpp 56-663](https://github.com/tenstorrent/tracy/blob/68c5cbd6/server/TracyView.hpp#L56-L663)

### Data Structures and Event Types

Tracy processes several core event types through a unified pipeline:

Sources: [server/TracyEvent.hpp 202-446](https://github.com/tenstorrent/tracy/blob/68c5cbd6/server/TracyEvent.hpp#L202-L446)[server/TracyWorker.hpp 277-407](https://github.com/tenstorrent/tracy/blob/68c5cbd6/server/TracyWorker.hpp#L277-L407)

## Data Flow and Network Protocol

### Client to Server Communication

Tracy uses a custom binary protocol over TCP with UDP-based service discovery. The communication flow follows this pattern:

1.   **Discovery Phase**: Client broadcasts UDP packets, server responds with connection details
2.   **Handshake Phase**: Protocol version verification and capability negotiation
3.   **Data Streaming Phase**: Continuous transmission of compressed event data
4.   **Query/Response Phase**: Server can request additional data (symbols, callstacks, etc.)

### Compression and Serialization

Tracy employs multiple compression strategies:

*   **Event Data**: LZ4 compression for real-time streaming
*   **File Storage**: LZ4, LZ4 HC, or Zstd compression with configurable levels
*   **Frame Images**: Optional dictionary-based compression for frame captures

Sources: [server/TracyView.cpp 278-295](https://github.com/tenstorrent/tracy/blob/68c5cbd6/server/TracyView.cpp#L278-L295)[manual/tracy.tex 53-54](https://github.com/tenstorrent/tracy/blob/68c5cbd6/manual/tracy.tex#L53-L54)

## File System and Persistence

### Tracy File Format

Tracy saves profiling data in a custom binary format with the `.tracy` extension. The format includes:

| Section | Content | Purpose |
| --- | --- | --- |
| Header | Version info, basic metadata | Format validation |
| Strings | String data with deduplication | Efficient storage |
| Source Locations | Function/file/line mappings | Code correlation |
| Event Data | Zones, GPU events, memory, etc. | Core profiling data |
| Callstacks | Stack frame information | Debug symbols |
| Metadata | CPU topology, thread names, etc. | System context |

### File I/O Architecture

Sources: [server/TracyFileRead.hpp](https://github.com/tenstorrent/tracy/blob/68c5cbd6/server/TracyFileRead.hpp)[server/TracyFileWrite.hpp](https://github.com/tenstorrent/tracy/blob/68c5cbd6/server/TracyFileWrite.hpp)[import-chrome/src/import-chrome.cpp 30-366](https://github.com/tenstorrent/tracy/blob/68c5cbd6/import-chrome/src/import-chrome.cpp#L30-L366)

## Platform Support and Build System

Tracy supports multiple platforms and build systems with platform-specific optimizations:

### Supported Platforms

*   **Windows**: MSVC, MinGW with full feature support
*   **Linux**: GCC, Clang with Wayland/X11 support
*   **macOS**: Clang with limited crash handling
*   **Android**: NDK with reduced features
*   **iOS**: Limited feature set

### Timer Implementation

Tracy achieves nanosecond resolution through platform-specific timer implementations:

*   **x86/x64**: `rdtscp` instruction for hardware timestamp counter
*   **ARM/ARM64**: `CNTVCT_EL0` register access with kernel fallback
*   **iOS**: `mach_absolute_time()` system call
*   **Fallback**: `std::chrono::high_resolution_clock`

Sources: [manual/tracy.tex 160-176](https://github.com/tenstorrent/tracy/blob/68c5cbd6/manual/tracy.tex#L160-L176)[manual/techdoc.tex 160-178](https://github.com/tenstorrent/tracy/blob/68c5cbd6/manual/techdoc.tex#L160-L178)[NEWS 21-28](https://github.com/tenstorrent/tracy/blob/68c5cbd6/NEWS#L21-L28)

Dismiss
Refresh this wiki

Enter email to refresh
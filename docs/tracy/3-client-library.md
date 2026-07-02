---
project: tracy
github: https://github.com/tenstorrent/tracy
deepwiki: https://deepwiki.com/tenstorrent/tracy/3-client-library
---

# Client Library

Relevant source files
*   [CMakeLists.txt](https://github.com/tenstorrent/tracy/blob/68c5cbd6/CMakeLists.txt)
*   [meson.build](https://github.com/tenstorrent/tracy/blob/68c5cbd6/meson.build)
*   [meson.options](https://github.com/tenstorrent/tracy/blob/68c5cbd6/meson.options)
*   [public/client/TracyCallstack.cpp](https://github.com/tenstorrent/tracy/blob/68c5cbd6/public/client/TracyCallstack.cpp)
*   [public/client/TracyCallstack.hpp](https://github.com/tenstorrent/tracy/blob/68c5cbd6/public/client/TracyCallstack.hpp)
*   [public/client/TracyLock.hpp](https://github.com/tenstorrent/tracy/blob/68c5cbd6/public/client/TracyLock.hpp)
*   [public/client/TracyProfiler.cpp](https://github.com/tenstorrent/tracy/blob/68c5cbd6/public/client/TracyProfiler.cpp)
*   [public/client/TracyProfiler.hpp](https://github.com/tenstorrent/tracy/blob/68c5cbd6/public/client/TracyProfiler.hpp)
*   [public/client/TracyScoped.hpp](https://github.com/tenstorrent/tracy/blob/68c5cbd6/public/client/TracyScoped.hpp)
*   [public/common/TracyProtocol.hpp](https://github.com/tenstorrent/tracy/blob/68c5cbd6/public/common/TracyProtocol.hpp)
*   [public/common/TracyQueue.hpp](https://github.com/tenstorrent/tracy/blob/68c5cbd6/public/common/TracyQueue.hpp)
*   [public/libbacktrace/dwarf.cpp](https://github.com/tenstorrent/tracy/blob/68c5cbd6/public/libbacktrace/dwarf.cpp)
*   [public/libbacktrace/elf.cpp](https://github.com/tenstorrent/tracy/blob/68c5cbd6/public/libbacktrace/elf.cpp)
*   [public/libbacktrace/internal.hpp](https://github.com/tenstorrent/tracy/blob/68c5cbd6/public/libbacktrace/internal.hpp)
*   [public/tracy/Tracy.hpp](https://github.com/tenstorrent/tracy/blob/68c5cbd6/public/tracy/Tracy.hpp)
*   [public/tracy/TracyC.h](https://github.com/tenstorrent/tracy/blob/68c5cbd6/public/tracy/TracyC.h)
*   [public/tracy/TracyLua.hpp](https://github.com/tenstorrent/tracy/blob/68c5cbd6/public/tracy/TracyLua.hpp)
*   [server/TracyEventDebug.cpp](https://github.com/tenstorrent/tracy/blob/68c5cbd6/server/TracyEventDebug.cpp)
*   [server/TracyEventDebug.hpp](https://github.com/tenstorrent/tracy/blob/68c5cbd6/server/TracyEventDebug.hpp)

The Tracy client library provides the instrumentation API that applications integrate to collect profiling data. It handles data collection, compression, and network transmission to the Tracy profiler server. This library is designed to be lightweight and minimally invasive when profiling is disabled.

For information about the profiler GUI application that receives and visualizes this data, see [Profiler Application](https://deepwiki.com/tenstorrent/tracy/4-profiler-application). For details about building and integrating Tracy into your project, see [Getting Started](https://deepwiki.com/tenstorrent/tracy/2-getting-started).

## Architecture Overview

The client library follows a producer-consumer architecture where instrumentation calls from application threads queue profiling events that are processed and transmitted by dedicated worker threads.

### Core Components

**Sources:**[public/client/TracyProfiler.hpp 161-890](https://github.com/tenstorrent/tracy/blob/68c5cbd6/public/client/TracyProfiler.hpp#L161-L890)[public/client/TracyScoped.hpp 17-200](https://github.com/tenstorrent/tracy/blob/68c5cbd6/public/client/TracyScoped.hpp#L17-L200)[public/tracy/Tracy.hpp 124-300](https://github.com/tenstorrent/tracy/blob/68c5cbd6/public/tracy/Tracy.hpp#L124-L300)

### Data Flow Architecture

**Sources:**[public/client/TracyProfiler.hpp 116-155](https://github.com/tenstorrent/tracy/blob/68c5cbd6/public/client/TracyProfiler.hpp#L116-L155)[public/client/TracyProfiler.cpp 2500-2800](https://github.com/tenstorrent/tracy/blob/68c5cbd6/public/client/TracyProfiler.cpp#L2500-L2800)[public/common/TracyQueue.hpp 10-800](https://github.com/tenstorrent/tracy/blob/68c5cbd6/public/common/TracyQueue.hpp#L10-L800)

## Core APIs

### C++ Instrumentation API

The primary C++ API is provided through [public/tracy/Tracy.hpp](https://github.com/tenstorrent/tracy/blob/68c5cbd6/public/tracy/Tracy.hpp) Key instrumentation macros include:

| Macro | Purpose | Example |
| --- | --- | --- |
| `ZoneScoped` | Profile current scope | `ZoneScoped;` |
| `ZoneScopedN(name)` | Named scope profiling | `ZoneScopedN("Database Query");` |
| `FrameMark` | Mark frame boundaries | `FrameMark;` |
| `TracyAlloc(ptr, size)` | Track memory allocation | `TracyAlloc(ptr, 1024);` |
| `TracyPlot(name, value)` | Plot numeric values | `TracyPlot("FPS", fps);` |
| `TracyMessage(text, size)` | Log messages | `TracyMessage("Loading complete", 16);` |

**Sources:**[public/tracy/Tracy.hpp 19-220](https://github.com/tenstorrent/tracy/blob/68c5cbd6/public/tracy/Tracy.hpp#L19-L220)

### C API

The C API is provided through [public/tracy/TracyC.h](https://github.com/tenstorrent/tracy/blob/68c5cbd6/public/tracy/TracyC.h) for integration with C codebases:

`// Zone profilingTracyCZone(ctx, 1);TracyCZoneText(ctx, "Processing data", 15);TracyCZoneEnd(ctx); // Memory tracking  TracyCAlloc(ptr, size);TracyCFree(ptr); // PlottingTracyCPlot("Memory Usage", memUsage);`
**Sources:**[public/tracy/TracyC.h 119-400](https://github.com/tenstorrent/tracy/blob/68c5cbd6/public/tracy/TracyC.h#L119-L400)

### GPU Profiling APIs

Tracy provides specialized APIs for GPU profiling across different graphics APIs:

*   **Vulkan:**[public/tracy/TracyVulkan.hpp](https://github.com/tenstorrent/tracy/blob/68c5cbd6/public/tracy/TracyVulkan.hpp)
*   **D3D12:**[public/tracy/TracyD3D12.hpp](https://github.com/tenstorrent/tracy/blob/68c5cbd6/public/tracy/TracyD3D12.hpp)
*   **OpenGL:**[public/tracy/TracyOpenGL.hpp](https://github.com/tenstorrent/tracy/blob/68c5cbd6/public/tracy/TracyOpenGL.hpp)

**Sources:**[public/tracy/TracyVulkan.hpp 1-200](https://github.com/tenstorrent/tracy/blob/68c5cbd6/public/tracy/TracyVulkan.hpp#L1-L200)[public/tracy/TracyD3D12.hpp 1-200](https://github.com/tenstorrent/tracy/blob/68c5cbd6/public/tracy/TracyD3D12.hpp#L1-L200)

## Profiler Core Implementation

### Profiler Singleton

The `tracy::Profiler` class serves as the central coordination point for all profiling activities:

**Sources:**[public/client/TracyProfiler.hpp 161-890](https://github.com/tenstorrent/tracy/blob/68c5cbd6/public/client/TracyProfiler.hpp#L161-L890)[public/client/TracyProfiler.cpp 1200-1500](https://github.com/tenstorrent/tracy/blob/68c5cbd6/public/client/TracyProfiler.cpp#L1200-L1500)

### ScopedZone Implementation

The `ScopedZone` class provides RAII-based zone profiling:

**Sources:**[public/client/TracyScoped.hpp 17-200](https://github.com/tenstorrent/tracy/blob/68c5cbd6/public/client/TracyScoped.hpp#L17-L200)

## Data Collection System

### Queue Types and Event Flow

Tracy uses different queue types for different categories of profiling data:

**Sources:**[public/common/TracyQueue.hpp 10-125](https://github.com/tenstorrent/tracy/blob/68c5cbd6/public/common/TracyQueue.hpp#L10-L125)[public/client/TracyProfiler.cpp 2500-2800](https://github.com/tenstorrent/tracy/blob/68c5cbd6/public/client/TracyProfiler.cpp#L2500-L2800)

### Memory Tracking Implementation

Tracy provides comprehensive memory allocation tracking through wrapper functions:

**Sources:**[public/client/TracyProfiler.hpp 487-637](https://github.com/tenstorrent/tracy/blob/68c5cbd6/public/client/TracyProfiler.hpp#L487-L637)[public/tracy/Tracy.hpp 190-210](https://github.com/tenstorrent/tracy/blob/68c5cbd6/public/tracy/Tracy.hpp#L190-L210)

## Configuration and Build Integration

### Compile-Time Configuration

Tracy behavior is controlled through preprocessor definitions set during compilation:

| Define | Purpose | Default |
| --- | --- | --- |
| `TRACY_ENABLE` | Enable/disable profiling | Enabled |
| `TRACY_ON_DEMAND` | On-demand profiling mode | Disabled |
| `TRACY_CALLSTACK` | Enable callstack capture | Disabled |
| `TRACY_NO_BROADCAST` | Disable network discovery | Disabled |
| `TRACY_DELAYED_INIT` | Delay initialization | Disabled |

**Sources:**[CMakeLists.txt 55-90](https://github.com/tenstorrent/tracy/blob/68c5cbd6/CMakeLists.txt#L55-L90)[meson.options 1-27](https://github.com/tenstorrent/tracy/blob/68c5cbd6/meson.options#L1-L27)

### CMake Integration

Tracy provides CMake targets for easy integration:

`target_link_libraries(your_app PRIVATE Tracy::TracyClient)target_compile_definitions(your_app PRIVATE TRACY_ENABLE)`
**Sources:**[CMakeLists.txt 31-54](https://github.com/tenstorrent/tracy/blob/68c5cbd6/CMakeLists.txt#L31-L54)[CMakeLists.txt 149-170](https://github.com/tenstorrent/tracy/blob/68c5cbd6/CMakeLists.txt#L149-L170)

### Network Protocol

The client communicates with the profiler server using a custom TCP protocol with LZ4 compression:

**Sources:**[public/common/TracyProtocol.hpp 1-170](https://github.com/tenstorrent/tracy/blob/68c5cbd6/public/common/TracyProtocol.hpp#L1-L170)[public/client/TracyProfiler.cpp 3000-3500](https://github.com/tenstorrent/tracy/blob/68c5cbd6/public/client/TracyProfiler.cpp#L3000-L3500)

Dismiss
Refresh this wiki

Enter email to refresh
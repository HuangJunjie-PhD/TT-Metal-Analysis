---
project: tracy
github: https://github.com/tenstorrent/tracy
deepwiki: https://deepwiki.com/tenstorrent/tracy/2-getting-started
---

# Getting Started

Relevant source files
*   [CMakeLists.txt](https://github.com/tenstorrent/tracy/blob/68c5cbd6/CMakeLists.txt)
*   [LICENSE](https://github.com/tenstorrent/tracy/blob/68c5cbd6/LICENSE)
*   [NEWS](https://github.com/tenstorrent/tracy/blob/68c5cbd6/NEWS)
*   [README.md](https://github.com/tenstorrent/tracy/blob/68c5cbd6/README.md?plain=1)
*   [import-chrome/src/import-chrome.cpp](https://github.com/tenstorrent/tracy/blob/68c5cbd6/import-chrome/src/import-chrome.cpp)
*   [manual/techdoc.tex](https://github.com/tenstorrent/tracy/blob/68c5cbd6/manual/techdoc.tex)
*   [manual/tracy.tex](https://github.com/tenstorrent/tracy/blob/68c5cbd6/manual/tracy.tex)
*   [meson.build](https://github.com/tenstorrent/tracy/blob/68c5cbd6/meson.build)
*   [meson.options](https://github.com/tenstorrent/tracy/blob/68c5cbd6/meson.options)
*   [public/client/TracyCallstack.cpp](https://github.com/tenstorrent/tracy/blob/68c5cbd6/public/client/TracyCallstack.cpp)
*   [public/client/TracyCallstack.hpp](https://github.com/tenstorrent/tracy/blob/68c5cbd6/public/client/TracyCallstack.hpp)
*   [public/libbacktrace/dwarf.cpp](https://github.com/tenstorrent/tracy/blob/68c5cbd6/public/libbacktrace/dwarf.cpp)
*   [public/libbacktrace/elf.cpp](https://github.com/tenstorrent/tracy/blob/68c5cbd6/public/libbacktrace/elf.cpp)
*   [public/libbacktrace/internal.hpp](https://github.com/tenstorrent/tracy/blob/68c5cbd6/public/libbacktrace/internal.hpp)

This document covers how to build Tracy, integrate it into your application, and start profiling. It provides the essential steps to get Tracy's client-server profiling system operational in your development environment.

For detailed build system configuration options, see [Build System](https://deepwiki.com/tenstorrent/tracy/2.1-build-system). For comprehensive instrumentation techniques, see [Integration Guide](https://deepwiki.com/tenstorrent/tracy/2.2-integration-guide).

## Overview

Tracy is a real-time profiling system that uses a client-server architecture. Your application (the client) collects profiling data and transmits it over the network to the Tracy profiler application (the server) for visualization and analysis.

### Client-Server Architecture

Sources: [manual/tracy.tex 236-270](https://github.com/tenstorrent/tracy/blob/68c5cbd6/manual/tracy.tex#L236-L270)[README.md 5-7](https://github.com/tenstorrent/tracy/blob/68c5cbd6/README.md?plain=1#L5-L7)

## Build Requirements

Tracy supports multiple build systems and platforms:

### Build Systems Overview

Sources: [CMakeLists.txt 1-170](https://github.com/tenstorrent/tracy/blob/68c5cbd6/CMakeLists.txt#L1-L170)[meson.build 1-228](https://github.com/tenstorrent/tracy/blob/68c5cbd6/meson.build#L1-L228)

### Platform Support

| Platform | Client | Server | Notes |
| --- | --- | --- | --- |
| Windows (x86/x64) | ✓ | ✓ | MSVC, MinGW |
| Linux (x86/x64/ARM/ARM64) | ✓ | ✓ | GCC, Clang |
| macOS (x64) | ✓ | ✓ | Clang |
| Android (ARM/ARM64/x86) | ✓ | - | NDK |
| iOS (ARM/ARM64) | ✓ | - | Limited features |

Sources: [manual/tracy.tex 366-388](https://github.com/tenstorrent/tracy/blob/68c5cbd6/manual/tracy.tex#L366-L388)

## Building Tracy

### Using CMake

The most straightforward way to integrate Tracy is via CMake:

Key CMake options available:

*   `TRACY_ENABLE` - Enable profiling (default: ON)
*   `TRACY_ON_DEMAND` - On-demand profiling mode
*   `TRACY_CALLSTACK` - Enable callstack capture
*   `TRACY_NO_BROADCAST` - Disable network discovery

Sources: [CMakeLists.txt 55-90](https://github.com/tenstorrent/tracy/blob/68c5cbd6/CMakeLists.txt#L55-L90)[manual/tracy.tex 437-453](https://github.com/tenstorrent/tracy/blob/68c5cbd6/manual/tracy.tex#L437-L453)

### Using Meson

For Meson projects, Tracy can be added as a wrap dependency:

Sources: [meson.build 12-27](https://github.com/tenstorrent/tracy/blob/68c5cbd6/meson.build#L12-L27)[meson.options 1-27](https://github.com/tenstorrent/tracy/blob/68c5cbd6/meson.options#L1-L27)[manual/tracy.tex 481-516](https://github.com/tenstorrent/tracy/blob/68c5cbd6/manual/tracy.tex#L481-L516)

### Manual Integration

For other build systems, include these files directly:

```
public/TracyClient.cpp    # Add to compilation
public/tracy/Tracy.hpp    # Include in source files
```

Required preprocessor definitions:

*   `TRACY_ENABLE` - Enable profiling functionality
*   Link with `pthread` and `dl` on Unix systems

Sources: [manual/tracy.tex 393-427](https://github.com/tenstorrent/tracy/blob/68c5cbd6/manual/tracy.tex#L393-L427)

## Basic Integration Workflow

Sources: [manual/tracy.tex 114-127](https://github.com/tenstorrent/tracy/blob/68c5cbd6/manual/tracy.tex#L114-L127)

## First Instrumentation

### Essential Macros

Add these key instrumentation macros to your code:

### Core Instrumentation Macros

| Macro | Purpose | Usage |
| --- | --- | --- |
| `ZoneScoped` | Profile function execution | Place at function start |
| `FrameMark` | Mark frame boundaries | End of main loop |
| `TracyMessage(msg)` | Log message with timestamp | Debugging/markers |
| `TracyPlot(name, value)` | Plot numeric values | Performance metrics |

Sources: [manual/tracy.tex 114-127](https://github.com/tenstorrent/tracy/blob/68c5cbd6/manual/tracy.tex#L114-L127)[public/tracy/Tracy.hpp](https://github.com/tenstorrent/tracy/blob/68c5cbd6/public/tracy/Tracy.hpp)

## Running the Profiler

### Network Discovery Process

### Connection Steps

1.   **Build and run your instrumented application**

    *   Ensure `TRACY_ENABLE` is defined
    *   Application will broadcast its presence on local network

2.   **Launch Tracy profiler**

    *   Run the Tracy GUI application
    *   It will automatically discover available clients

3.   **Establish connection**

    *   Click "Connect" in the profiler interface
    *   Real-time profiling data will begin streaming

4.   **Save traces** (optional)

    *   Use profiler interface to save `.tracy` files
    *   Files can be loaded later for offline analysis

Sources: [manual/tracy.tex 234-270](https://github.com/tenstorrent/tracy/blob/68c5cbd6/manual/tracy.tex#L234-L270)[manual/tracy.tex 537-541](https://github.com/tenstorrent/tracy/blob/68c5cbd6/manual/tracy.tex#L537-L541)

## Configuration Options

### Client Configuration Macros

Common build-time configuration options:

| Macro | Description |
| --- | --- |
| `TRACY_ON_DEMAND` | Only profile when server connected |
| `TRACY_NO_BROADCAST` | Disable network discovery |
| `TRACY_ONLY_LOCALHOST` | Restrict to localhost only |
| `TRACY_CALLSTACK` | Enable callstack capture |
| `TRACY_NO_EXIT` | Keep client alive for data transfer |

### Environment Variables

Runtime configuration via environment variables:

*   `TRACY_NO_INVARIANT_CHECK=1` - Skip CPU timer validation
*   `TRACY_ONLY_LOCALHOST=1` - Runtime localhost restriction
*   `TRACY_NO_DBGHELP_INIT_LOAD=1` - Skip symbol preloading on Windows

Sources: [CMakeLists.txt 65-89](https://github.com/tenstorrent/tracy/blob/68c5cbd6/CMakeLists.txt#L65-L89)[meson.options 1-27](https://github.com/tenstorrent/tracy/blob/68c5cbd6/meson.options#L1-L27)[manual/tracy.tex 522-547](https://github.com/tenstorrent/tracy/blob/68c5cbd6/manual/tracy.tex#L522-L547)

## Troubleshooting

### Common Issues

**Build Errors:**

*   Ensure C++11 or later standard
*   Link required system libraries (`pthread`, `dl` on Unix)
*   Verify `TRACY_ENABLE` is defined project-wide

**Connection Issues:**

*   Check firewall settings for UDP broadcast and TCP connections
*   Verify both client and server are on same network
*   Use `TRACY_ONLY_LOCALHOST` for local testing

**Performance Impact:**

*   Tracy overhead is typically 2-3 nanoseconds per event
*   Use `TRACY_ON_DEMAND` to reduce memory usage
*   Profile optimized builds, not debug builds

Sources: [manual/tracy.tex 289-305](https://github.com/tenstorrent/tracy/blob/68c5cbd6/manual/tracy.tex#L289-L305)[manual/tracy.tex 519-571](https://github.com/tenstorrent/tracy/blob/68c5cbd6/manual/tracy.tex#L519-L571)

# Getting Started

This document covers how to build Tracy, integrate it into your application, and start profiling. It provides the essential steps to get Tracy's client-server profiling system operational in your development environment.

For detailed build system configuration options, see [Build System](https://deepwiki.com/tenstorrent/tracy/2.1-build-system). For comprehensive instrumentation techniques, see [Integration Guide](https://deepwiki.com/tenstorrent/tracy/2.2-integration-guide).

## Overview

Tracy is a real-time profiling system that uses a client-server architecture. Your application (the client) collects profiling data and transmits it over the network to the Tracy profiler application (the server) for visualization and analysis.

### Client-Server Architecture

Sources: [manual/tracy.tex 236-270](https://github.com/tenstorrent/tracy/blob/68c5cbd6/manual/tracy.tex#L236-L270)[README.md 5-7](https://github.com/tenstorrent/tracy/blob/68c5cbd6/README.md?plain=1#L5-L7)

## Build Requirements

Tracy supports multiple build systems and platforms:

### Build Systems Overview

Sources: [CMakeLists.txt 1-170](https://github.com/tenstorrent/tracy/blob/68c5cbd6/CMakeLists.txt#L1-L170)[meson.build 1-228](https://github.com/tenstorrent/tracy/blob/68c5cbd6/meson.build#L1-L228)

### Platform Support

| Platform | Client | Server | Notes |
| --- | --- | --- | --- |
| Windows (x86/x64) | ✓ | ✓ | MSVC, MinGW |
| Linux (x86/x64/ARM/ARM64) | ✓ | ✓ | GCC, Clang |
| macOS (x64) | ✓ | ✓ | Clang |
| Android (ARM/ARM64/x86) | ✓ | - | NDK |
| iOS (ARM/ARM64) | ✓ | - | Limited features |

Sources: [manual/tracy.tex 366-388](https://github.com/tenstorrent/tracy/blob/68c5cbd6/manual/tracy.tex#L366-L388)

## Building Tracy

### Using CMake

The most straightforward way to integrate Tracy is via CMake:

Key CMake options available:

*   `TRACY_ENABLE` - Enable profiling (default: ON)
*   `TRACY_ON_DEMAND` - On-demand profiling mode
*   `TRACY_CALLSTACK` - Enable callstack capture
*   `TRACY_NO_BROADCAST` - Disable network discovery

Sources: [CMakeLists.txt 55-90](https://github.com/tenstorrent/tracy/blob/68c5cbd6/CMakeLists.txt#L55-L90)[manual/tracy.tex 437-453](https://github.com/tenstorrent/tracy/blob/68c5cbd6/manual/tracy.tex#L437-L453)

### Using Meson

For Meson projects, Tracy can be added as a wrap dependency:

Sources: [meson.build 12-27](https://github.com/tenstorrent/tracy/blob/68c5cbd6/meson.build#L12-L27)[meson.options 1-27](https://github.com/tenstorrent/tracy/blob/68c5cbd6/meson.options#L1-L27)[manual/tracy.tex 481-516](https://github.com/tenstorrent/tracy/blob/68c5cbd6/manual/tracy.tex#L481-L516)

### Manual Integration

For other build systems, include these files directly:

```
public/TracyClient.cpp    # Add to compilation
public/tracy/Tracy.hpp    # Include in source files
```

Required preprocessor definitions:

*   `TRACY_ENABLE` - Enable profiling functionality
*   Link with `pthread` and `dl` on Unix systems

Sources: [manual/tracy.tex 393-427](https://github.com/tenstorrent/tracy/blob/68c5cbd6/manual/tracy.tex#L393-L427)

## Basic Integration Workflow

Sources: [manual/tracy.tex 114-127](https://github.com/tenstorrent/tracy/blob/68c5cbd6/manual/tracy.tex#L114-L127)

## First Instrumentation

### Essential Macros

Add these key instrumentation macros to your code:

### Core Instrumentation Macros

| Macro | Purpose | Usage |
| --- | --- | --- |
| `ZoneScoped` | Profile function execution | Place at function start |
| `FrameMark` | Mark frame boundaries | End of main loop |
| `TracyMessage(msg)` | Log message with timestamp | Debugging/markers |
| `TracyPlot(name, value)` | Plot numeric values | Performance metrics |

Sources: [manual/tracy.tex 114-127](https://github.com/tenstorrent/tracy/blob/68c5cbd6/manual/tracy.tex#L114-L127)

## Running the Profiler

### Network Discovery Process

### Connection Steps

1.   **Build and run your instrumented application**

    *   Ensure `TRACY_ENABLE` is defined
    *   Application will broadcast its presence on local network

2.   **Launch Tracy profiler**

    *   Run the Tracy GUI application
    *   It will automatically discover available clients

3.   **Establish connection**

    *   Click "Connect" in the profiler interface
    *   Real-time profiling data will begin streaming

4.   **Save traces** (optional)

    *   Use profiler interface to save `.tracy` files
    *   Files can be loaded later for offline analysis

Sources: [manual/tracy.tex 234-270](https://github.com/tenstorrent/tracy/blob/68c5cbd6/manual/tracy.tex#L234-L270)[manual/tracy.tex 537-541](https://github.com/tenstorrent/tracy/blob/68c5cbd6/manual/tracy.tex#L537-L541)

## Configuration Options

### Client Configuration Macros

Common build-time configuration options:

| Macro | Description |
| --- | --- |
| `TRACY_ON_DEMAND` | Only profile when server connected |
| `TRACY_NO_BROADCAST` | Disable network discovery |
| `TRACY_ONLY_LOCALHOST` | Restrict to localhost only |
| `TRACY_CALLSTACK` | Enable callstack capture |
| `TRACY_NO_EXIT` | Keep client alive for data transfer |

### Environment Variables

Runtime configuration via environment variables:

*   `TRACY_NO_INVARIANT_CHECK=1` - Skip CPU timer validation
*   `TRACY_ONLY_LOCALHOST=1` - Runtime localhost restriction
*   `TRACY_NO_DBGHELP_INIT_LOAD=1` - Skip symbol preloading on Windows

Sources: [CMakeLists.txt 65-89](https://github.com/tenstorrent/tracy/blob/68c5cbd6/CMakeLists.txt#L65-L89)[meson.options 1-27](https://github.com/tenstorrent/tracy/blob/68c5cbd6/meson.options#L1-L27)[manual/tracy.tex 522-547](https://github.com/tenstorrent/tracy/blob/68c5cbd6/manual/tracy.tex#L522-L547)

## Troubleshooting

### Common Issues

**Build Errors:**

*   Ensure C++11 or later standard
*   Link required system libraries (`pthread`, `dl` on Unix)
*   Verify `TRACY_ENABLE` is defined project-wide

**Connection Issues:**

*   Check firewall settings for UDP broadcast and TCP connections
*   Verify both client and server are on same network
*   Use `TRACY_ONLY_LOCALHOST` for local testing

**Performance Impact:**

*   Tracy overhead is typically 2-3 nanoseconds per event
*   Use `TRACY_ON_DEMAND` to reduce memory usage
*   Profile optimized builds, not debug builds

Sources: [manual/tracy.tex 289-305](https://github.com/tenstorrent/tracy/blob/68c5cbd6/manual/tracy.tex#L289-L305)[manual/tracy.tex 519-571](https://github.com/tenstorrent/tracy/blob/68c5cbd6/manual/tracy.tex#L519-L571)

Dismiss
Refresh this wiki

Enter email to refresh
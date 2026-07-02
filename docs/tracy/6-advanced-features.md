---
project: tracy
github: https://github.com/tenstorrent/tracy
deepwiki: https://deepwiki.com/tenstorrent/tracy/6-advanced-features
---

# Advanced Features

Relevant source files
*   [profiler/src/stb_image.h](https://github.com/tenstorrent/tracy/blob/68c5cbd6/profiler/src/stb_image.h)
*   [server/TracyStorage.cpp](https://github.com/tenstorrent/tracy/blob/68c5cbd6/server/TracyStorage.cpp)
*   [server/TracyStorage.hpp](https://github.com/tenstorrent/tracy/blob/68c5cbd6/server/TracyStorage.hpp)
*   [server/TracyUserData.cpp](https://github.com/tenstorrent/tracy/blob/68c5cbd6/server/TracyUserData.cpp)
*   [server/TracyUserData.hpp](https://github.com/tenstorrent/tracy/blob/68c5cbd6/server/TracyUserData.hpp)
*   [server/TracyViewData.hpp](https://github.com/tenstorrent/tracy/blob/68c5cbd6/server/TracyViewData.hpp)
*   [test/Makefile](https://github.com/tenstorrent/tracy/blob/68c5cbd6/test/Makefile)
*   [test/stb_image.h](https://github.com/tenstorrent/tracy/blob/68c5cbd6/test/stb_image.h)
*   [test/test.cpp](https://github.com/tenstorrent/tracy/blob/68c5cbd6/test/test.cpp)

This document covers advanced Tracy features that extend beyond basic profiling capabilities. These include persistent user configuration, specialized testing utilities, platform-specific implementations, and integrated third-party components.

For basic profiling API usage, see [Core Profiling API](https://deepwiki.com/tenstorrent/tracy/3.1-core-profiling-api). For GUI application features, see [Profiler Application](https://deepwiki.com/tenstorrent/tracy/4-profiler-application).

## User Data Management and Persistent Configuration

Tracy provides a comprehensive user data system that persists configuration, annotations, and customizations across profiling sessions. The `UserData` class manages all persistent state associated with specific programs and trace captures.

### UserData System Architecture

Sources: [server/TracyUserData.hpp 17-51](https://github.com/tenstorrent/tracy/blob/68c5cbd6/server/TracyUserData.hpp#L17-L51)[server/TracyUserData.cpp 14-27](https://github.com/tenstorrent/tracy/blob/68c5cbd6/server/TracyUserData.cpp#L14-L27)[server/TracyViewData.hpp 34-73](https://github.com/tenstorrent/tracy/blob/68c5cbd6/server/TracyViewData.hpp#L34-L73)

### Configuration Storage Locations

The storage system automatically creates platform-specific configuration directories using standardized paths:

| Platform | Configuration Path |
| --- | --- |
| Windows | `%APPDATA%/tracy/user/{first_char}/{program_name}/{week}/{time}/` |
| Linux/Unix | `$XDG_CONFIG_HOME/tracy/user/` or `$HOME/.config/tracy/user/` |

Each trace session gets a unique directory based on program name and capture timestamp, organized into weekly buckets for efficient management.

Sources: [server/TracyStorage.cpp 57-89](https://github.com/tenstorrent/tracy/blob/68c5cbd6/server/TracyStorage.cpp#L57-L89)[server/TracyStorage.cpp 118-209](https://github.com/tenstorrent/tracy/blob/68c5cbd6/server/TracyStorage.cpp#L118-L209)

### Persistent State Categories

#### Timeline and View State

The system persists timeline navigation state including zoom level, scroll position, and frame scaling:

*   `zvStart`, `zvEnd`: Timeline zoom boundaries
*   `frameScale`, `frameStart`: Frame view configuration
*   Rendering toggles for GPU zones, locks, plots, context switches

#### Display Options

User-configurable rendering preferences stored in the options file:

*   Zone rendering modes (`drawGpuZones`, `drawZones`, `drawLocks`)
*   Visual enhancements (`dynamicColors`, `forceColors`, `ghostZones`)
*   Performance monitoring (`drawCpuData`, `drawSamples`)
*   Frame rate targeting (`frameTarget`)

Sources: [server/TracyUserData.cpp 94-119](https://github.com/tenstorrent/tracy/blob/68c5cbd6/server/TracyUserData.cpp#L94-L119)[server/TracyViewData.hpp 41-57](https://github.com/tenstorrent/tracy/blob/68c5cbd6/server/TracyViewData.hpp#L41-L57)

### Annotation System

Tracy supports persistent time-range annotations with custom text and colors. Annotations are stored with full precision timestamps and RGBA color values.

The annotation system supports:

*   Arbitrary time range selection with nanosecond precision
*   Custom RGBA color coding for visual organization
*   Rich text descriptions for documentation
*   Persistent storage across sessions

Sources: [server/TracyUserData.cpp 173-240](https://github.com/tenstorrent/tracy/blob/68c5cbd6/server/TracyUserData.cpp#L173-L240)[server/TracyViewData.hpp 60-65](https://github.com/tenstorrent/tracy/blob/68c5cbd6/server/TracyViewData.hpp#L60-L65)

### Source Path Substitution

The source substitution system enables regex-based path remapping for build environments where source paths differ between build and analysis environments.

#### Regex Processing Pipeline

The system validates regex patterns during load and gracefully handles invalid expressions by marking them as non-functional while preserving the configuration.

Sources: [server/TracyUserData.cpp 242-290](https://github.com/tenstorrent/tracy/blob/68c5cbd6/server/TracyUserData.cpp#L242-L290)[server/TracyViewData.hpp 67-72](https://github.com/tenstorrent/tracy/blob/68c5cbd6/server/TracyViewData.hpp#L67-L72)

## Test System and Example Applications

Tracy includes a comprehensive test application that demonstrates advanced profiling features and serves as both validation and documentation for complex usage patterns.

### Test Application Architecture

The test application creates multiple worker threads, each demonstrating different Tracy features:

Sources: [test/test.cpp 302-327](https://github.com/tenstorrent/tracy/blob/68c5cbd6/test/test.cpp#L302-L327)

### Advanced Memory Tracking Features

#### Custom Allocator Integration

The test demonstrates both global and named memory tracking:

*   Global tracking via overloaded operators with stack depth 10
*   Named allocator pools with custom labels (`"Custom alloc"`)
*   Automatic callstack capture for allocation tracking

Sources: [test/test.cpp 23-47](https://github.com/tenstorrent/tracy/blob/68c5cbd6/test/test.cpp#L23-L47)

### Lock Contention Analysis

#### Multi-Lock Scenarios

The test creates complex lock interactions including:

*   **Basic Mutex Contention**: Multiple threads competing for shared resources
*   **Recursive Lock Testing**: Nested lock acquisition with different hold times
*   **Shared Mutex Patterns**: Reader-writer scenarios with mixed access patterns
*   **Deadlock Detection**: Intentional circular dependency creation

Sources: [test/test.cpp 87-301](https://github.com/tenstorrent/tracy/blob/68c5cbd6/test/test.cpp#L87-L301)

### Performance Pattern Testing

#### Callstack Capture Performance

The test measures overhead of different callstack capture depths:

*   `ZoneScopedS(10)`: Captures 10 stack frames for detailed analysis
*   Continuous execution to measure sampling impact
*   Thread-specific performance isolation

#### Recursive Algorithm Profiling

The Fibonacci implementation demonstrates:

*   Deep call stack visualization
*   Inline function vs regular function performance
*   Zone text annotation with algorithm parameters

Sources: [test/test.cpp 173-197](https://github.com/tenstorrent/tracy/blob/68c5cbd6/test/test.cpp#L173-L197)[test/test.cpp 252-265](https://github.com/tenstorrent/tracy/blob/68c5cbd6/test/test.cpp#L252-L265)

### Build System Integration

#### Cross-Platform Makefile

The test Makefile demonstrates platform-specific Tracy integration:

| Feature | Configuration |
| --- | --- |
| Compiler Flags | `-DTRACY_ENABLE` for profiling, `-g3` for debug symbols |
| Debug Info | `-DTRACY_DEBUGINFOD` on Linux for enhanced symbol resolution |
| Libraries | `-lpthread -ldl` for threading and dynamic loading |
| Linking | `-rdynamic` for runtime symbol export |

#### Platform Detection

Sources: [test/Makefile 16-23](https://github.com/tenstorrent/tracy/blob/68c5cbd6/test/Makefile#L16-L23)

## Image Processing Integration

Tracy integrates the STB image library for texture and frame capture visualization, supporting multiple image formats for enhanced profiling analysis.

### STB Image Library Integration

#### Supported Format Pipeline

#### Frame Image Integration

The test application demonstrates frame image capture using the STB loader:

*   Loads images via `stbi_load()` with automatic format detection
*   Integrates with `FrameImage()` for timeline visualization
*   Supports RGBA pixel data for frame annotations

Sources: [test/test.cpp 330-342](https://github.com/tenstorrent/tracy/blob/68c5cbd6/test/test.cpp#L330-L342)[profiler/src/stb_image.h 369-544](https://github.com/tenstorrent/tracy/blob/68c5cbd6/profiler/src/stb_image.h#L369-L544)

### SIMD Optimization Support

The STB integration includes platform-specific optimizations:

#### Architecture Detection

*   **x86/x64**: Automatic SSE2 detection with runtime fallback
*   **ARM**: NEON support via `STBI_NEON` compile flag
*   **Fallback**: Pure C implementation for unsupported platforms

Sources: [profiler/src/stb_image.h 690-788](https://github.com/tenstorrent/tracy/blob/68c5cbd6/profiler/src/stb_image.h#L690-L788)

### Memory Management Integration

The STB library integrates with Tracy's memory tracking through configurable allocators:

*   `STBI_MALLOC`, `STBI_REALLOC`, `STBI_FREE` macros for custom allocation
*   Memory size validation preventing integer overflow attacks
*   Configurable maximum dimensions (`STBI_MAX_DIMENSIONS`) for security

Sources: [profiler/src/stb_image.h 671-796](https://github.com/tenstorrent/tracy/blob/68c5cbd6/profiler/src/stb_image.h#L671-L796)

Dismiss
Refresh this wiki

Enter email to refresh
---
project: tracy
github: https://github.com/tenstorrent/tracy
deepwiki: https://deepwiki.com/tenstorrent/tracy/5-file-system-and-io
---

# File System & I/O

Relevant source files
*   [server/TracyFileHeader.hpp](https://github.com/tenstorrent/tracy/blob/68c5cbd6/server/TracyFileHeader.hpp)
*   [server/TracyFileRead.hpp](https://github.com/tenstorrent/tracy/blob/68c5cbd6/server/TracyFileRead.hpp)
*   [server/TracyFileWrite.hpp](https://github.com/tenstorrent/tracy/blob/68c5cbd6/server/TracyFileWrite.hpp)

This page covers Tracy's file system and I/O infrastructure, focusing on the core mechanisms for reading and writing profiling data to disk. The system provides high-performance, compressed file operations that support both live profiling sessions and offline trace analysis.

For specific information about Tracy's file format specifications and compression schemes, see [File Formats](https://deepwiki.com/tenstorrent/tracy/5.1-file-formats). For command-line utilities that handle trace import/export and format conversion, see [Import & Export Tools](https://deepwiki.com/tenstorrent/tracy/5.2-import-and-export-tools).

## Overview

Tracy's file I/O system is built around two primary components: `FileRead` for loading compressed trace files and `FileWrite` for saving profiling data. The system supports multiple compression algorithms (LZ4 and Zstd) and uses advanced techniques like memory mapping and asynchronous decompression to achieve high throughput while maintaining low memory overhead.

**File I/O Architecture**

Sources: [server/TracyFileRead.hpp 1-516](https://github.com/tenstorrent/tracy/blob/68c5cbd6/server/TracyFileRead.hpp#L1-L516)[server/TracyFileWrite.hpp 1-185](https://github.com/tenstorrent/tracy/blob/68c5cbd6/server/TracyFileWrite.hpp#L1-L185)[server/TracyFileHeader.hpp 1-22](https://github.com/tenstorrent/tracy/blob/68c5cbd6/server/TracyFileHeader.hpp#L1-L22)

## File Reading Architecture

The `FileRead` class provides asynchronous, high-performance reading of compressed Tracy files. It uses memory mapping combined with background decompression to minimize I/O blocking and maximize throughput.

### Core FileRead Implementation

The `FileRead` class implements a sophisticated double-buffered, threaded decompression system:

| Component | Purpose | Key Methods |
| --- | --- | --- |
| **Memory Mapping** | Direct file access without copying | `mmap()` integration |
| **Worker Thread** | Background decompression | `Worker()`, `ReadBlock()` |
| **Double Buffering** | Continuous data flow | `m_buf`, `m_second` buffer swap |
| **Template Readers** | Type-safe bulk reads | `Read2()` through `Read10()` |

**FileRead Internal Data Flow**

Sources: [server/TracyFileRead.hpp 36-516](https://github.com/tenstorrent/tracy/blob/68c5cbd6/server/TracyFileRead.hpp#L36-L516)[server/TracyFileRead.hpp 397-416](https://github.com/tenstorrent/tracy/blob/68c5cbd6/server/TracyFileRead.hpp#L397-L416)[server/TracyFileRead.hpp 473-489](https://github.com/tenstorrent/tracy/blob/68c5cbd6/server/TracyFileRead.hpp#L473-L489)

### Compression Support and Format Detection

File format detection occurs during `FileRead` construction by examining the first 4 bytes:

`// Format detection in FileRead constructorchar hdr[4];if( memcmp( hdr, Lz4Header, sizeof( hdr ) ) == 0 ){    m_stream = LZ4_createStreamDecode();}else if( memcmp( hdr, ZstdHeader, sizeof( hdr ) ) == 0 ){    m_streamZstd = ZSTD_createDStream();}`
The system supports two compression formats defined in `TracyFileHeader.hpp`:

*   **LZ4 Format**: Header `{'t', 'l', 'Z', 4}` for fast decompression
*   **Zstd Format**: Header `{'t', 'Z', 's', 't'}` for higher compression ratios

Sources: [server/TracyFileRead.hpp 345-363](https://github.com/tenstorrent/tracy/blob/68c5cbd6/server/TracyFileRead.hpp#L345-L363)[server/TracyFileHeader.hpp 11-12](https://github.com/tenstorrent/tracy/blob/68c5cbd6/server/TracyFileHeader.hpp#L11-L12)

## File Writing Architecture

The `FileWrite` class provides buffered, compressed writing with multiple compression strategies. It supports both streaming compression and statistical tracking for compression analysis.

### Compression Modes and Configuration

| Compression Mode | Implementation | Use Case |
| --- | --- | --- |
| **Fast** | `LZ4_compress_fast_continue` | Real-time profiling |
| **Slow** | `LZ4_compress_HC_continue` | Balanced performance |
| **Extreme** | `LZ4HC_CLEVEL_MAX` | Maximum LZ4 compression |
| **Zstd** | `ZSTD_compressStream2` | Best compression ratio |

**FileWrite Compression Pipeline**

Sources: [server/TracyFileWrite.hpp 34-48](https://github.com/tenstorrent/tracy/blob/68c5cbd6/server/TracyFileWrite.hpp#L34-L48)[server/TracyFileWrite.hpp 70-111](https://github.com/tenstorrent/tracy/blob/68c5cbd6/server/TracyFileWrite.hpp#L70-L111)[server/TracyFileWrite.hpp 137-165](https://github.com/tenstorrent/tracy/blob/68c5cbd6/server/TracyFileWrite.hpp#L137-L165)

## Performance Optimizations

The Tracy file I/O system implements several performance optimizations:

### Memory Management

*   **Memory Mapping**: Direct file access via `mmap()` eliminates extra copying [server/TracyFileRead.hpp 376-381](https://github.com/tenstorrent/tracy/blob/68c5cbd6/server/TracyFileRead.hpp#L376-L381)
*   **Double Buffering**: Overlapped I/O and decompression operations [server/TracyFileRead.hpp 384-387](https://github.com/tenstorrent/tracy/blob/68c5cbd6/server/TracyFileRead.hpp#L384-L387)
*   **Aligned Buffers**: Cache-friendly memory layout with `alignas(64)`[server/TracyFileRead.hpp 504-506](https://github.com/tenstorrent/tracy/blob/68c5cbd6/server/TracyFileRead.hpp#L504-L506)

### Threading and Synchronization

*   **Lock-Free Coordination**: Atomic variables for thread communication [server/TracyFileRead.hpp 504-506](https://github.com/tenstorrent/tracy/blob/68c5cbd6/server/TracyFileRead.hpp#L504-L506)
*   **Background Processing**: Dedicated worker thread for decompression [server/TracyFileRead.hpp 397-416](https://github.com/tenstorrent/tracy/blob/68c5cbd6/server/TracyFileRead.hpp#L397-L416)
*   **Yield-Based Waiting**: Cooperative threading with `YieldThread()`[server/TracyFileRead.hpp 408](https://github.com/tenstorrent/tracy/blob/68c5cbd6/server/TracyFileRead.hpp#L408-L408)

### Template-Based Reading

The `FileRead` class provides specialized template methods (`Read2` through `Read10`) that allow reading multiple values in a single operation, reducing function call overhead and improving cache locality.

Sources: [server/TracyFileRead.hpp 95-327](https://github.com/tenstorrent/tracy/blob/68c5cbd6/server/TracyFileRead.hpp#L95-L327)[server/TracyFileRead.hpp 397-416](https://github.com/tenstorrent/tracy/blob/68c5cbd6/server/TracyFileRead.hpp#L397-L416)[server/TracyFileRead.hpp 504-506](https://github.com/tenstorrent/tracy/blob/68c5cbd6/server/TracyFileRead.hpp#L504-L506)

Dismiss
Refresh this wiki

Enter email to refresh
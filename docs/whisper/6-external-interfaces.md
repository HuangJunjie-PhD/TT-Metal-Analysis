---
project: whisper
github: https://github.com/tenstorrent/whisper
deepwiki: https://deepwiki.com/tenstorrent/whisper/6-external-interfaces
---

# External Interfaces

Relevant source files
*   [DecodedInst.hpp](https://github.com/tenstorrent/whisper/blob/733fd921/DecodedInst.hpp)
*   [GNUmakefile](https://github.com/tenstorrent/whisper/blob/733fd921/GNUmakefile)
*   [InstEntry.hpp](https://github.com/tenstorrent/whisper/blob/733fd921/InstEntry.hpp)
*   [Interactive.cpp](https://github.com/tenstorrent/whisper/blob/733fd921/Interactive.cpp)
*   [Interactive.hpp](https://github.com/tenstorrent/whisper/blob/733fd921/Interactive.hpp)
*   [PerfApi.cpp](https://github.com/tenstorrent/whisper/blob/733fd921/PerfApi.cpp)
*   [PerfApi.hpp](https://github.com/tenstorrent/whisper/blob/733fd921/PerfApi.hpp)
*   [Server.cpp](https://github.com/tenstorrent/whisper/blob/733fd921/Server.cpp)
*   [Server.hpp](https://github.com/tenstorrent/whisper/blob/733fd921/Server.hpp)
*   [WhisperMessage.h](https://github.com/tenstorrent/whisper/blob/733fd921/WhisperMessage.h)
*   [whisper.cpp](https://github.com/tenstorrent/whisper/blob/733fd921/whisper.cpp)

## Purpose and Scope

This page provides an overview of the external interfaces that enable interaction with the Whisper RISC-V simulator. Whisper exposes three primary interfaces for different use cases:

1.   **Server API** - Socket/shared-memory protocol for programmatic control (detailed in [6.1](https://deepwiki.com/tenstorrent/whisper/6.1-server-api-and-message-protocol))
2.   **Interactive CLI** - Command-line interface for manual debugging (detailed in [6.2](https://deepwiki.com/tenstorrent/whisper/6.2-interactive-command-line-interface))
3.   **Performance Modeling API** - Fine-grained pipeline control for cycle-accurate simulation (detailed in [6.3](https://deepwiki.com/tenstorrent/whisper/6.3-performance-modeling-api))

This page describes the high-level architecture, communication mechanisms, and how these interfaces are selected at startup. For GDB remote debugging support, see [6.4](https://deepwiki.com/tenstorrent/whisper/6.4-gdb-remote-debugging).

**Sources:**[whisper.cpp 1-115](https://github.com/tenstorrent/whisper/blob/733fd921/whisper.cpp#L1-L115)[Server.hpp 1-122](https://github.com/tenstorrent/whisper/blob/733fd921/Server.hpp#L1-L122)[Interactive.hpp 1-229](https://github.com/tenstorrent/whisper/blob/733fd921/Interactive.hpp#L1-L229)[PerfApi.hpp 1-437](https://github.com/tenstorrent/whisper/blob/733fd921/PerfApi.hpp#L1-L437)

## Interface Architecture

The following diagram shows how external clients interact with Whisper through different interface layers:

**Sources:**[Server.hpp 36-119](https://github.com/tenstorrent/whisper/blob/733fd921/Server.hpp#L36-L119)[Interactive.hpp 39-226](https://github.com/tenstorrent/whisper/blob/733fd921/Interactive.hpp#L39-L226)[PerfApi.hpp 23-437](https://github.com/tenstorrent/whisper/blob/733fd921/PerfApi.hpp#L23-L437)[whisper.cpp 30-114](https://github.com/tenstorrent/whisper/blob/733fd921/whisper.cpp#L30-L114)

## Interface Selection at Startup

Whisper determines which interface to activate based on command-line arguments parsed in `main()`. The selection logic is implemented in `Session::run()`:

**Sources:**[whisper.cpp 30-114](https://github.com/tenstorrent/whisper/blob/733fd921/whisper.cpp#L30-L114)[Session.hpp](https://github.com/tenstorrent/whisper/blob/733fd921/Session.hpp)[Args.hpp](https://github.com/tenstorrent/whisper/blob/733fd921/Args.hpp)

## Communication Mechanisms

### Socket Communication

The Server interface supports TCP/IP socket communication for remote control. Message exchange uses blocking `recv()` and `send()` system calls with the `WhisperMessage` structure:

| Function | Purpose | File Location |
| --- | --- | --- |
| `receiveMessage(int soc, WhisperMessage& msg)` | Receive message from socket | [Server.cpp 36-66](https://github.com/tenstorrent/whisper/blob/733fd921/Server.cpp#L36-L66) |
| `sendMessage(int soc, const WhisperMessage& msg)` | Send message to socket | [Server.cpp 69-94](https://github.com/tenstorrent/whisper/blob/733fd921/Server.cpp#L69-L94) |
| `Server::interact(int soc, ...)` | Main socket loop | [Server.cpp 1065-1084](https://github.com/tenstorrent/whisper/blob/733fd921/Server.cpp#L1065-L1084) |

**Sources:**[Server.cpp 36-94](https://github.com/tenstorrent/whisper/blob/733fd921/Server.cpp#L36-L94)[Server.cpp 1065-1084](https://github.com/tenstorrent/whisper/blob/733fd921/Server.cpp#L1065-L1084)

### Shared Memory Communication

For higher performance, the Server interface supports shared memory IPC with atomic synchronization:

| Function | Purpose | File Location |
| --- | --- | --- |
| `receiveMessage(std::span<char> shm, WhisperMessage& msg)` | Receive from shared memory | [Server.cpp 97-109](https://github.com/tenstorrent/whisper/blob/733fd921/Server.cpp#L97-L109) |
| `sendMessage(std::span<char> shm, const WhisperMessage& msg)` | Send to shared memory | [Server.cpp 112-125](https://github.com/tenstorrent/whisper/blob/733fd921/Server.cpp#L112-L125) |
| `Server::interact(std::span<char> shm, ...)` | Main shared memory loop | [Server.cpp 1088-1108](https://github.com/tenstorrent/whisper/blob/733fd921/Server.cpp#L1088-L1108) |

The shared memory protocol uses a guard byte for synchronization:

*   Byte 0: Atomic flag (`'s'` = server ready, `'c'` = client ready)
*   Bytes 4+: Aligned `WhisperMessage` structure

**Sources:**[Server.cpp 97-125](https://github.com/tenstorrent/whisper/blob/733fd921/Server.cpp#L97-L125)[Server.cpp 1088-1108](https://github.com/tenstorrent/whisper/blob/733fd921/Server.cpp#L1088-L1108)

### Standard Input (Interactive Mode)

The Interactive interface reads commands from standard input using the `linenoise` library for line editing and history:

**Sources:**[Interactive.cpp 1-1711](https://github.com/tenstorrent/whisper/blob/733fd921/Interactive.cpp#L1-L1711)[linenoise.hpp](https://github.com/tenstorrent/whisper/blob/733fd921/linenoise.hpp)

## WhisperMessage Protocol

The `WhisperMessage` structure is the fundamental unit of communication for the Server interface:

### Message Types

| Type | Purpose | Used By |
| --- | --- | --- |
| `Peek` | Read register/memory | Testbenches, Python scripts |
| `Poke` | Write register/memory | Testbenches, Python scripts |
| `Step` | Execute single instruction | Testbenches, compliance tests |
| `Until` | Run until address | Testbenches |
| `ChangeCount` | Report changes from step | Returned by Whisper |
| `Change` | Individual change record | Returned by Whisper |
| `McmRead` | MCM memory read | Out-of-order simulation |
| `McmInsert` | MCM merge buffer insert | Out-of-order simulation |
| `Translate` | Virtual address translation | Testbenches |

**Sources:**[WhisperMessage.h 22-74](https://github.com/tenstorrent/whisper/blob/733fd921/WhisperMessage.h#L22-L74)

### Resource Identifiers

The `resource` field identifies what to peek/poke:

| Resource | Identifier | Address Field | Value Field |
| --- | --- | --- | --- |
| Integer Register | `'r'` | Register number (0-31) | Register value |
| FP Register | `'f'` | Register number (0-31) | Register value |
| CSR | `'c'` | CSR number | CSR value |
| Vector Register | `'v'` | Register number (0-31) | Data in `buffer` |
| Memory | `'m'` | Memory address | Memory value |
| PC | `'p'` | Unused | PC value |
| Special | `'s'` | Special resource ID | Resource-specific |

**Sources:**[WhisperMessage.h 32-34](https://github.com/tenstorrent/whisper/blob/733fd921/WhisperMessage.h#L32-L34)[Server.cpp 143-281](https://github.com/tenstorrent/whisper/blob/733fd921/Server.cpp#L143-L281)[Server.cpp 284-439](https://github.com/tenstorrent/whisper/blob/733fd921/Server.cpp#L284-L439)

## Command Processing Flow

The following diagram shows how a command flows through the Server interface:

**Sources:**[Server.cpp 1112-1397](https://github.com/tenstorrent/whisper/blob/733fd921/Server.cpp#L1112-L1397)[Server.cpp 142-281](https://github.com/tenstorrent/whisper/blob/733fd921/Server.cpp#L142-L281)[Server.cpp 284-439](https://github.com/tenstorrent/whisper/blob/733fd921/Server.cpp#L284-L439)[Server.cpp 664-738](https://github.com/tenstorrent/whisper/blob/733fd921/Server.cpp#L664-L738)

## Interface Comparison

The following table summarizes the key differences between the three interfaces:

| Feature | Server API | Interactive CLI | Performance API |
| --- | --- | --- | --- |
| **Primary Use Case** | Automated testing, lock-step verification | Manual debugging | Cycle-accurate performance modeling |
| **Communication** | Socket, shared memory | Standard input/output | Direct C++ API calls |
| **Granularity** | Instruction-level | Instruction/run-level | Pipeline stage-level |
| **State Control** | Peek/poke all state | Peek/poke all state | Full pipeline control |
| **Typical Client** | Verilog testbench, Python | Human user | Verilog performance model |
| **Memory Consistency** | MCM command support | MCM command support | Full MCM integration |
| **Implementation** | `Server<URV>` | `Interactive<URV>` | `PerfApi` |
| **File Location** | [Server.cpp](https://github.com/tenstorrent/whisper/blob/733fd921/Server.cpp) | [Interactive.cpp](https://github.com/tenstorrent/whisper/blob/733fd921/Interactive.cpp) | [PerfApi.cpp](https://github.com/tenstorrent/whisper/blob/733fd921/PerfApi.cpp) |

**Sources:**[Server.hpp 36-119](https://github.com/tenstorrent/whisper/blob/733fd921/Server.hpp#L36-L119)[Interactive.hpp 39-226](https://github.com/tenstorrent/whisper/blob/733fd921/Interactive.hpp#L39-L226)[PerfApi.hpp 23-437](https://github.com/tenstorrent/whisper/blob/733fd921/PerfApi.hpp#L23-L437)

## Key Classes and Methods

### Server Class

The `Server` class implements the socket/shared-memory interface:

| Method | Purpose | File Location |
| --- | --- | --- |
| `interact(int soc, ...)` | Main socket loop | [Server.cpp 1065-1084](https://github.com/tenstorrent/whisper/blob/733fd921/Server.cpp#L1065-L1084) |
| `interact(std::span<char> shm, ...)` | Main shared memory loop | [Server.cpp 1088-1108](https://github.com/tenstorrent/whisper/blob/733fd921/Server.cpp#L1088-L1108) |
| `pokeCommand()` | Handle poke requests | [Server.cpp 142-281](https://github.com/tenstorrent/whisper/blob/733fd921/Server.cpp#L142-L281) |
| `peekCommand()` | Handle peek requests | [Server.cpp 284-439](https://github.com/tenstorrent/whisper/blob/733fd921/Server.cpp#L284-L439) |
| `stepCommand()` | Handle step requests | [Server.cpp 664-738](https://github.com/tenstorrent/whisper/blob/733fd921/Server.cpp#L664-L738) |
| `mcmReadCommand()` | Handle MCM read | [Server.cpp 778-846](https://github.com/tenstorrent/whisper/blob/733fd921/Server.cpp#L778-L846) |
| `mcmInsertCommand()` | Handle MCM insert | [Server.cpp 849-915](https://github.com/tenstorrent/whisper/blob/733fd921/Server.cpp#L849-L915) |

**Sources:**[Server.hpp 36-119](https://github.com/tenstorrent/whisper/blob/733fd921/Server.hpp#L36-L119)[Server.cpp 128-1397](https://github.com/tenstorrent/whisper/blob/733fd921/Server.cpp#L128-L1397)

### Interactive Class

The `Interactive` class implements the command-line interface:

| Method | Purpose | File Location |
| --- | --- | --- |
| `interact(FILE* trace, FILE* log)` | Main command loop | [Interactive.cpp 164-1711](https://github.com/tenstorrent/whisper/blob/733fd921/Interactive.cpp#L164-L1711) |
| `stepCommand()` | Single-step execution | [Interactive.cpp 236-287](https://github.com/tenstorrent/whisper/blob/733fd921/Interactive.cpp#L236-L287) |
| `runCommand()` | Run until breakpoint | [Interactive.cpp 216-232](https://github.com/tenstorrent/whisper/blob/733fd921/Interactive.cpp#L216-L232) |
| `peekCommand()` | Examine state | [Interactive.cpp 504-828](https://github.com/tenstorrent/whisper/blob/733fd921/Interactive.cpp#L504-L828) |
| `pokeCommand()` | Modify state | [Interactive.cpp 832-948](https://github.com/tenstorrent/whisper/blob/733fd921/Interactive.cpp#L832-L948) |
| `disassCommand()` | Disassemble instructions | [Interactive.cpp 950-1013](https://github.com/tenstorrent/whisper/blob/733fd921/Interactive.cpp#L950-L1013) |
| `elfCommand()` | Load ELF file | [Interactive.cpp 1015-1065](https://github.com/tenstorrent/whisper/blob/733fd921/Interactive.cpp#L1015-L1065) |

**Sources:**[Interactive.hpp 39-226](https://github.com/tenstorrent/whisper/blob/733fd921/Interactive.hpp#L39-L226)[Interactive.cpp 164-1711](https://github.com/tenstorrent/whisper/blob/733fd921/Interactive.cpp#L164-L1711)

### PerfApi Class

The `PerfApi` class implements fine-grained performance modeling:

| Method | Purpose | File Location |
| --- | --- | --- |
| `fetch(hartIx, time, tag, vpc, ...)` | Fetch instruction | [PerfApi.cpp 82-168](https://github.com/tenstorrent/whisper/blob/733fd921/PerfApi.cpp#L82-L168) |
| `decode(hartIx, time, tag)` | Decode instruction | [PerfApi.cpp 171-270](https://github.com/tenstorrent/whisper/blob/733fd921/PerfApi.cpp#L171-L270) |
| `execute(hartIx, time, tag)` | Execute instruction | [PerfApi.cpp 273-359](https://github.com/tenstorrent/whisper/blob/733fd921/PerfApi.cpp#L273-L359) |
| `retire(hartIx, time, tag)` | Retire instruction | [PerfApi.cpp 498-626](https://github.com/tenstorrent/whisper/blob/733fd921/PerfApi.cpp#L498-L626) |
| `drainStore(hartIx, time, tag)` | Drain store to memory | [PerfApi.cpp 811-874](https://github.com/tenstorrent/whisper/blob/733fd921/PerfApi.cpp#L811-L874) |
| `getLoadData(...)` | Get load data with forwarding | [PerfApi.cpp 878-1005](https://github.com/tenstorrent/whisper/blob/733fd921/PerfApi.cpp#L878-L1005) |

**Sources:**[PerfApi.hpp 23-437](https://github.com/tenstorrent/whisper/blob/733fd921/PerfApi.hpp#L23-L437)[PerfApi.cpp 24-2040](https://github.com/tenstorrent/whisper/blob/733fd921/PerfApi.cpp#L24-L2040)

## Build Configuration

The external interfaces are compiled into the main `whisper` executable and the Python bindings:

| Target | Interfaces Included | Build Command |
| --- | --- | --- |
| `whisper` | Server, Interactive | `make` |
| `pywhisper.so` | Server (via Python bindings) | `make all` |

**Sources:**[GNUmakefile 137-149](https://github.com/tenstorrent/whisper/blob/733fd921/GNUmakefile#L137-L149)[GNUmakefile 151-153](https://github.com/tenstorrent/whisper/blob/733fd921/GNUmakefile#L151-L153)[GNUmakefile 207](https://github.com/tenstorrent/whisper/blob/733fd921/GNUmakefile#L207-L207)

## Usage Examples

### Server Mode (Socket)

`# Start Whisper in server mode listening on port 8888whisper --server localhost:8888 --configfile config.json`
### Server Mode (Shared Memory)

`# Start Whisper using shared memorywhisper --server /dev/shm/whisper_shm --configfile config.json`
### Interactive Mode

`# Start Whisper in interactive modewhisper --interactive --target program.elf`
### Batch Mode

`# Run program in batch mode (default)whisper --target program.elf --logfile trace.log`
**Sources:**[whisper.cpp 30-114](https://github.com/tenstorrent/whisper/blob/733fd921/whisper.cpp#L30-L114)[Args.cpp](https://github.com/tenstorrent/whisper/blob/733fd921/Args.cpp)

* * *

For detailed information about each interface:

*   **Server API protocol and commands:** See [6.1](https://deepwiki.com/tenstorrent/whisper/6.1-server-api-and-message-protocol)
*   **Interactive CLI commands:** See [6.2](https://deepwiki.com/tenstorrent/whisper/6.2-interactive-command-line-interface)
*   **Performance modeling API details:** See [6.3](https://deepwiki.com/tenstorrent/whisper/6.3-performance-modeling-api)
*   **GDB remote debugging:** See [6.4](https://deepwiki.com/tenstorrent/whisper/6.4-gdb-remote-debugging)

Dismiss
Refresh this wiki

Enter email to refresh
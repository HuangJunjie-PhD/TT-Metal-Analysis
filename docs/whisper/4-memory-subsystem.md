---
project: whisper
github: https://github.com/tenstorrent/whisper
deepwiki: https://deepwiki.com/tenstorrent/whisper/4-memory-subsystem
---

# Memory Subsystem

Relevant source files
*   [Mcm.cpp](https://github.com/tenstorrent/whisper/blob/733fd921/Mcm.cpp)
*   [Mcm.hpp](https://github.com/tenstorrent/whisper/blob/733fd921/Mcm.hpp)
*   [Memory.cpp](https://github.com/tenstorrent/whisper/blob/733fd921/Memory.cpp)
*   [Memory.hpp](https://github.com/tenstorrent/whisper/blob/733fd921/Memory.hpp)
*   [PmaManager.cpp](https://github.com/tenstorrent/whisper/blob/733fd921/PmaManager.cpp)
*   [PmaManager.hpp](https://github.com/tenstorrent/whisper/blob/733fd921/PmaManager.hpp)
*   [hypervisor.cpp](https://github.com/tenstorrent/whisper/blob/733fd921/hypervisor.cpp)
*   [printTrace.cpp](https://github.com/tenstorrent/whisper/blob/733fd921/printTrace.cpp)

## Purpose and Scope

The Memory Subsystem in Whisper implements a comprehensive RISC-V memory model including physical memory storage, virtual memory translation, memory protection mechanisms, and memory consistency verification. This page provides an overview of the memory subsystem architecture and how its components interact.

For detailed information on specific subsystems:

*   Physical memory storage, PMA, and PMP: see [Physical Memory and Protection](https://deepwiki.com/tenstorrent/whisper/4.1-physical-memory-and-protection)
*   Virtual memory and TLBs: see [Virtual Memory and Address Translation](https://deepwiki.com/tenstorrent/whisper/4.2-virtual-memory-and-address-translation)
*   Out-of-order memory simulation: see [Memory Consistency Model](https://deepwiki.com/tenstorrent/whisper/4.3-memory-consistency-model)
*   Two-stage address translation: see [Hypervisor Virtual Memory](https://deepwiki.com/tenstorrent/whisper/4.4-hypervisor-virtual-memory)

**Sources:**[Memory.hpp 1-73](https://github.com/tenstorrent/whisper/blob/733fd921/Memory.hpp#L1-L73)[Mcm.hpp 1-220](https://github.com/tenstorrent/whisper/blob/733fd921/Mcm.hpp#L1-L220)[PmaManager.hpp 1-155](https://github.com/tenstorrent/whisper/blob/733fd921/PmaManager.hpp#L1-L155)

## Memory Subsystem Architecture

The memory subsystem is organized in multiple layers, each providing specific functionality. The diagram below shows how these layers interact when a hart accesses memory:

**Sources:**[Memory.hpp 59-73](https://github.com/tenstorrent/whisper/blob/733fd921/Memory.hpp#L59-L73)[Mcm.hpp 210-222](https://github.com/tenstorrent/whisper/blob/733fd921/Mcm.hpp#L210-L222)[PmaManager.hpp 155-169](https://github.com/tenstorrent/whisper/blob/733fd921/PmaManager.hpp#L155-L169)

## Core Memory Classes

The following table summarizes the primary classes in the memory subsystem:

| Class | File | Purpose |
| --- | --- | --- |
| `Memory` | [Memory.hpp 60](https://github.com/tenstorrent/whisper/blob/733fd921/Memory.hpp#L60-L60) | Physical memory storage and management |
| `PmaManager` | [PmaManager.hpp 156](https://github.com/tenstorrent/whisper/blob/733fd921/PmaManager.hpp#L156-L156) | Physical memory attributes per region |
| `PmpManager` | (Referenced) | Physical memory protection regions |
| `VirtMem` | (Referenced) | Virtual-to-physical address translation |
| `Mcm<URV>` | [Mcm.hpp 211](https://github.com/tenstorrent/whisper/blob/733fd921/Mcm.hpp#L211-L211) | Memory consistency model for out-of-order simulation |
| `IoDevice` | (Referenced) | Memory-mapped I/O device interface |

**Sources:**[Memory.hpp 44-78](https://github.com/tenstorrent/whisper/blob/733fd921/Memory.hpp#L44-L78)[Mcm.hpp 210-222](https://github.com/tenstorrent/whisper/blob/733fd921/Mcm.hpp#L210-L222)[PmaManager.hpp 155-169](https://github.com/tenstorrent/whisper/blob/733fd921/PmaManager.hpp#L155-L169)

## Physical Memory Layer

The `Memory` class provides the foundation of the memory subsystem, implementing physical memory storage and basic read/write operations.

### Memory Class Structure

**Sources:**[Memory.hpp 59-130](https://github.com/tenstorrent/whisper/blob/733fd921/Memory.hpp#L59-L130)[PmaManager.hpp 156-243](https://github.com/tenstorrent/whisper/blob/733fd921/PmaManager.hpp#L156-L243)

### Memory Configuration

The `Memory` class is configured with:

| Parameter | Type | Purpose |
| --- | --- | --- |
| `size_` | `uint64_t` | Total physical memory size in bytes |
| `pageSize_` | `uint64_t` | Page size (typically 4KB) |
| `data_` | `uint8_t*` | Base pointer to memory array |
| `pageShift_` | `unsigned` | log2(pageSize) for fast page number calculation |

Memory is allocated using `mmap` with `MAP_NORESERVE` to support large sparse address spaces efficiently:

**Sources:**[Memory.cpp 46-73](https://github.com/tenstorrent/whisper/blob/733fd921/Memory.cpp#L46-L73)[Memory.hpp 70-82](https://github.com/tenstorrent/whisper/blob/733fd921/Memory.hpp#L70-L82)

### Memory Access Methods

The `Memory` class provides multiple access methods with different semantics:

*   **`read<T>()`**: Standard load with PMA checks for read permission ([Memory.hpp 92-128](https://github.com/tenstorrent/whisper/blob/733fd921/Memory.hpp#L92-L128))
*   **`readInst<T>()`**: Instruction fetch with execute permission check ([Memory.hpp 133-159](https://github.com/tenstorrent/whisper/blob/733fd921/Memory.hpp#L133-L159))
*   **`write<T>()`**: Standard store with PMA checks for write permission ([Memory.hpp 210-258](https://github.com/tenstorrent/whisper/blob/733fd921/Memory.hpp#L210-L258))
*   **`peek<T>()`** / **`poke<T>()`**: Debug access, optionally bypassing PMA ([Memory.hpp 308-348](https://github.com/tenstorrent/whisper/blob/733fd921/Memory.hpp#L308-L348))

**Sources:**[Memory.hpp 92-348](https://github.com/tenstorrent/whisper/blob/733fd921/Memory.hpp#L92-L348)

## Physical Memory Attributes (PMA)

The `PmaManager` assigns attributes to physical address regions, controlling access permissions and behavior.

### PMA Attributes

The `Pma` class defines a bitfield of attributes:

| Attribute | Bit Mask | Description |
| --- | --- | --- |
| `Read` | 0x1 | Load instructions allowed |
| `Write` | 0x2 | Store instructions allowed |
| `Exec` | 0x4 | Instruction fetch allowed |
| `Idempotent` | 0x8 | Non-I/O region, reads have no side effects |
| `Amo` | 0x70 | Atomic operations supported |
| `MemMapped` | 0x200 | Region contains memory-mapped registers |
| `Rsrv` | 0x400 | LR/SC instructions allowed |
| `Io` | 0x800 | I/O region (non-idempotent) |
| `Cacheable` | 0x1000 | Region is cacheable |
| `MisalOk` | 0x2000 | Misaligned access supported |
| `MisalAccFault` | 0x4000 | Misaligned access causes access fault |

**Sources:**[PmaManager.hpp 34-47](https://github.com/tenstorrent/whisper/blob/733fd921/PmaManager.hpp#L34-L47)[PmaManager.cpp 23-77](https://github.com/tenstorrent/whisper/blob/733fd921/PmaManager.cpp#L23-L77)

### PMA Region Management

PMA regions are checked in order from index 0 upward. The first matching region determines the PMA. If no region matches, `defaultPma_` is used.

**Sources:**[PmaManager.hpp 174-190](https://github.com/tenstorrent/whisper/blob/733fd921/PmaManager.hpp#L174-L190)[PmaManager.cpp 103-122](https://github.com/tenstorrent/whisper/blob/733fd921/PmaManager.cpp#L103-L122)

### Memory-Mapped Registers

Special handling for memory-mapped registers (e.g., device CSRs):

*   Registers are accessed only with aligned 4-byte or 8-byte operations
*   Each register has a write mask that limits writable bits
*   Reads and writes are intercepted by `PmaManager::readRegister()` / `writeRegister()`

**Sources:**[PmaManager.hpp 264-280](https://github.com/tenstorrent/whisper/blob/733fd921/PmaManager.hpp#L264-L280)[PmaManager.cpp 88-354](https://github.com/tenstorrent/whisper/blob/733fd921/PmaManager.cpp#L88-L354)

## Virtual Memory Integration

When virtual memory is enabled, address translation occurs before physical memory access:

Virtual memory translation is covered in detail in [Virtual Memory and Address Translation](https://deepwiki.com/tenstorrent/whisper/4.2-virtual-memory-and-address-translation).

**Sources:**[hypervisor.cpp 157-209](https://github.com/tenstorrent/whisper/blob/733fd921/hypervisor.cpp#L157-L209)[Memory.hpp 92-128](https://github.com/tenstorrent/whisper/blob/733fd921/Memory.hpp#L92-L128)

## Memory Consistency Model (MCM)

The `Mcm` class enables out-of-order memory simulation and verification of RISC-V memory ordering rules (RVWMO/TSO).

### MCM Architecture

**Sources:**[Mcm.hpp 210-450](https://github.com/tenstorrent/whisper/blob/733fd921/Mcm.hpp#L210-L450)[Mcm.cpp 11-35](https://github.com/tenstorrent/whisper/blob/733fd921/Mcm.cpp#L11-L35)

### MCM Operation Types

The `Mcm` class tracks three types of memory operations:

| Operation | Method | Description |
| --- | --- | --- |
| Read | `readOp()` | Load operation, may forward from preceding stores |
| Merge Buffer Insert | `mergeBufferInsert()` | Store writes to merge buffer (not yet visible) |
| Bypass Write | `bypassOp()` | Store writes directly to memory (visible immediately) |

Each operation is recorded in `sysMemOps_` with a timestamp and instruction tag for ordering verification.

**Sources:**[Mcm.hpp 224-256](https://github.com/tenstorrent/whisper/blob/733fd921/Mcm.hpp#L224-L256)[Mcm.cpp 117-261](https://github.com/tenstorrent/whisper/blob/733fd921/Mcm.cpp#L117-L261)

### Store-to-Load Forwarding

When a load reads from an address that a preceding (in program order) store wrote to, and the store has not yet drained to memory, the load must forward data from the store:

**Sources:**[Mcm.cpp 642-666](https://github.com/tenstorrent/whisper/blob/733fd921/Mcm.cpp#L642-L666)[Mcm.hpp 305-328](https://github.com/tenstorrent/whisper/blob/733fd921/Mcm.hpp#L305-L328)

### PPO Rules Verification

The MCM verifies Preserved Program Order (PPO) rules as defined in the RISC-V memory model:

*   **Rule 1**: Overlapping store-store must execute in program order
*   **Rule 2**: Load-load with address dependency must execute in program order
*   **Rule 3**: Store-load with overlapping address must execute in program order
*   **Rule 4-13**: Fence, acquire/release, and other synchronization ordering

The `retire()` method checks applicable PPO rules when each instruction retires.

**Sources:**[Mcm.cpp 1234-1505](https://github.com/tenstorrent/whisper/blob/733fd921/Mcm.cpp#L1234-L1505)[Mcm.hpp 342-383](https://github.com/tenstorrent/whisper/blob/733fd921/Mcm.hpp#L342-L383)

## Memory Access Flow Example

The following diagram shows the complete flow for a load instruction in MCM mode:

**Sources:**[Memory.hpp 92-128](https://github.com/tenstorrent/whisper/blob/733fd921/Memory.hpp#L92-L128)[Mcm.cpp 117-261](https://github.com/tenstorrent/whisper/blob/733fd921/Mcm.cpp#L117-L261)

## Memory Initialization and Loading

The `Memory` class supports loading programs from multiple file formats:

| Format | Method | Description |
| --- | --- | --- |
| ELF | `loadElfFile()` | Load ELF executable, extract symbols |
| Hex | `loadHexFile()` | Load Verilog-style hex file (`@address` + hex bytes) |
| Binary | `loadBinaryFile()` | Load raw binary at specified address |
| LZ4 | `loadLz4File()` | Load LZ4-compressed binary |

Program loading populates physical memory and extracts symbol information for debugging. Memory-mapped regions are respected during loading.

**Sources:**[Memory.cpp 93-200](https://github.com/tenstorrent/whisper/blob/733fd921/Memory.cpp#L93-L200)[Memory.cpp 203-256](https://github.com/tenstorrent/whisper/blob/733fd921/Memory.cpp#L203-L256)[Memory.cpp 366-406](https://github.com/tenstorrent/whisper/blob/733fd921/Memory.cpp#L366-L406)[Memory.hpp 350-420](https://github.com/tenstorrent/whisper/blob/733fd921/Memory.hpp#L350-L420)

## I/O Device Integration

Memory-mapped I/O devices implement the `IoDevice` interface and are registered with the `Memory` instance:

When `Memory::read()` or `Memory::write()` is called, the address is first checked against all registered I/O devices. If a device claims the address, the operation is forwarded to that device instead of accessing `data_`.

**Sources:**[Memory.hpp 262-286](https://github.com/tenstorrent/whisper/blob/733fd921/Memory.hpp#L262-L286)[Memory.hpp 464-465](https://github.com/tenstorrent/whisper/blob/733fd921/Memory.hpp#L464-L465)

## Memory Tracing and Debugging

The memory subsystem includes comprehensive tracing capabilities:

*   **Data Line Trace**: Records all cache line addresses accessed for data
*   **Instruction Line Trace**: Records all cache line addresses accessed for instructions
*   **Page Table Walk Trace**: Logs virtual-to-physical translation steps
*   **MCM Operation Trace**: Records all memory operations with timestamps

Traces are written in CSV format for post-processing and analysis.

**Sources:**[Memory.hpp 449-505](https://github.com/tenstorrent/whisper/blob/733fd921/Memory.hpp#L449-L505)[printTrace.cpp 213-266](https://github.com/tenstorrent/whisper/blob/733fd921/printTrace.cpp#L213-L266)

## Related Subsystems

The memory subsystem integrates with several other Whisper components:

*   **Hart Execution**: Harts issue memory requests through `Memory` and `Mcm` ([Hart Execution Unit](https://deepwiki.com/tenstorrent/whisper/2.2-hart-execution-unit))
*   **Virtual Memory**: `VirtMem` translates addresses before physical access ([Virtual Memory and Address Translation](https://deepwiki.com/tenstorrent/whisper/4.2-virtual-memory-and-address-translation))
*   **CSRs**: Memory configuration via CSRs like `satp`, `mstatus`, `pmpcfg` ([Control and Status Registers](https://deepwiki.com/tenstorrent/whisper/3.1-control-and-status-registers-(csrs)))
*   **Interrupts**: Memory-mapped interrupt controllers like IMSIC and APLIC ([Interrupt Controllers](https://deepwiki.com/tenstorrent/whisper/7.1-interrupt-controllers-(aclint-imsic-aplic)))
*   **IOMMU**: Device address translation for DMA ([IOMMU](https://deepwiki.com/tenstorrent/whisper/7.2-iommu-(inputoutput-memory-management-unit)))

**Sources:**[Memory.hpp 44-66](https://github.com/tenstorrent/whisper/blob/733fd921/Memory.hpp#L44-L66)[Mcm.hpp 15-19](https://github.com/tenstorrent/whisper/blob/733fd921/Mcm.hpp#L15-L19)

Dismiss
Refresh this wiki

Enter email to refresh
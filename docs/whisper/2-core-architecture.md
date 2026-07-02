---
project: whisper
github: https://github.com/tenstorrent/whisper
deepwiki: https://deepwiki.com/tenstorrent/whisper/2-core-architecture
---

# Core Architecture

Relevant source files
*   [GNUmakefile](https://github.com/tenstorrent/whisper/blob/733fd921/GNUmakefile)
*   [Hart.cpp](https://github.com/tenstorrent/whisper/blob/733fd921/Hart.cpp)
*   [Hart.hpp](https://github.com/tenstorrent/whisper/blob/733fd921/Hart.hpp)
*   [HartConfig.cpp](https://github.com/tenstorrent/whisper/blob/733fd921/HartConfig.cpp)
*   [System.cpp](https://github.com/tenstorrent/whisper/blob/733fd921/System.cpp)
*   [System.hpp](https://github.com/tenstorrent/whisper/blob/733fd921/System.hpp)
*   [whisper.cpp](https://github.com/tenstorrent/whisper/blob/733fd921/whisper.cpp)

## Purpose and Scope

This page provides an overview of Whisper's core architectural components and how they are organized. It focuses on the structural hierarchy from the `System` class down to individual `Hart` instances, the configuration mechanisms, and the fundamental execution model.

For detailed information on specific subsystems:

*   System configuration and JSON-based setup: See [System Organization and Configuration](https://deepwiki.com/tenstorrent/whisper/2.1-system-organization-and-configuration)
*   Hart instruction execution pipeline and state management: See [Hart Execution Unit](https://deepwiki.com/tenstorrent/whisper/2.2-hart-execution-unit)
*   Instruction decoding and ISA support: See [Instruction Set Architecture](https://deepwiki.com/tenstorrent/whisper/2.3-instruction-set-architecture)

* * *

## High-Level Organization

Whisper is structured as a hierarchical system where a `System` contains one or more `Core` instances, and each `Core` contains one or more `Hart` instances. This organization mirrors typical multi-core RISC-V systems with hardware threading support.

**Sources:**[System.hpp 59-79](https://github.com/tenstorrent/whisper/blob/733fd921/System.hpp#L59-L79)[System.cpp 49-82](https://github.com/tenstorrent/whisper/blob/733fd921/System.cpp#L49-L82)[Hart.hpp 128-149](https://github.com/tenstorrent/whisper/blob/733fd921/Hart.hpp#L128-L149)

* * *

## Component Hierarchy

### System Class

The `System<URV>` class (where `URV` is either `uint32_t` or `uint64_t`) is the top-level container that manages:

*   **Cores:** A vector of `std::shared_ptr<Core<URV>>` instances
*   **System-wide harts:** A flattened vector `sysHarts_` containing all harts across all cores for efficient access
*   **Shared memory:** A single `Memory` instance shared by all harts
*   **Hart ID mapping:** An `unordered_map<URV, unsigned>` named `hartIdToIndex_` for looking up harts by their MHARTID CSR value
*   **I/O devices:** Peripherals like UART, block devices, and interrupt controllers
*   **System time:** A shared `time_` counter used by all harts

**Sources:**[System.hpp 59-529](https://github.com/tenstorrent/whisper/blob/733fd921/System.hpp#L59-L529)[System.cpp 49-99](https://github.com/tenstorrent/whisper/blob/733fd921/System.cpp#L49-L99)

### Core Class

Each `Core<URV>` represents a physical core and contains:

*   **Multiple harts:** One or more `Hart<URV>` instances representing hardware threads
*   **Core index:** Position within the system (`coreIx_`)
*   **Hart ID base:** The starting MHARTID value for this core's harts

Cores provide a logical grouping but share most resources through the system-level `Memory` instance.

**Sources:** Referenced in [System.hpp 49](https://github.com/tenstorrent/whisper/blob/733fd921/System.hpp#L49-L49)[System.cpp 66-81](https://github.com/tenstorrent/whisper/blob/733fd921/System.cpp#L66-L81)

### Hart Class

The `Hart<URV>` class is the primary execution unit that implements a complete RISC-V hardware thread. Each hart contains:

| Component | Type | Purpose |
| --- | --- | --- |
| **Program Counter** | `URV pc_` | Current instruction address |
| **Integer Registers** | `IntRegs intRegs_` | x0-x31 general-purpose registers |
| **FP Registers** | `FpRegs fpRegs_` | f0-f31 floating-point registers |
| **Vector Registers** | `VecRegs vecRegs_` | v0-v31 vector registers |
| **CSRs** | `CsRegs csRegs_` | Control and Status Registers |
| **Virtual Memory** | `VirtMem virtMem_` | Address translation and TLBs |
| **Privilege Mode** | `PrivilegeMode privMode_` | Current execution privilege level |
| **Decoder** | `Decoder decoder_` | Instruction decoder |
| **Instruction Cache** | Decode cache | Cached decoded instructions |

**Sources:**[Hart.hpp 128-149](https://github.com/tenstorrent/whisper/blob/733fd921/Hart.hpp#L128-L149)[Hart.cpp 88-149](https://github.com/tenstorrent/whisper/blob/733fd921/Hart.cpp#L88-L149)

* * *

## Initialization Flow

The initialization process follows a specific sequence from command-line parsing through system configuration to execution readiness.

**Sources:**[whisper.cpp 30-114](https://github.com/tenstorrent/whisper/blob/733fd921/whisper.cpp#L30-L114)[System.cpp 49-99](https://github.com/tenstorrent/whisper/blob/733fd921/System.cpp#L49-L99)[Hart.cpp 88-149](https://github.com/tenstorrent/whisper/blob/733fd921/Hart.cpp#L88-L149)[HartConfig.cpp 49-78](https://github.com/tenstorrent/whisper/blob/733fd921/HartConfig.cpp#L49-L78)

* * *

## Template Parameter URV

Throughout the architecture, the template parameter `URV` (Unsigned Register Value) determines the register width:

*   **`uint32_t`:** RV32 configuration (32-bit registers)
*   **`uint64_t`:** RV64 configuration (64-bit registers)

The register width is determined early in initialization:

This design allows the same codebase to support both RV32 and RV64 through compile-time specialization, with type-safe conversions between different width types.

**Sources:**[whisper.cpp 82-105](https://github.com/tenstorrent/whisper/blob/733fd921/whisper.cpp#L82-L105)[Hart.hpp 130-141](https://github.com/tenstorrent/whisper/blob/733fd921/Hart.hpp#L130-L141)

* * *

## Hart Indexing

Whisper maintains multiple indexing schemes for harts:

| Index Type | Description | Usage |
| --- | --- | --- |
| **System Hart Index** | Position in `sysHarts_` vector (0 to N-1) | Internal indexing, array access |
| **Hart ID (MHARTID)** | CSR value, may be non-contiguous | RISC-V architectural identifier |
| **Core Index + Hart-in-Core** | (coreIx, hartIxInCore) | Hierarchical addressing |

The mapping is established during construction:

**Sources:**[System.cpp 66-81](https://github.com/tenstorrent/whisper/blob/733fd921/System.cpp#L66-L81)[System.hpp 174-182](https://github.com/tenstorrent/whisper/blob/733fd921/System.hpp#L174-L182)

* * *

## Execution Models

Whisper supports multiple execution models controlled at the `System` level:

### Batch Execution

In batch mode (`System::batchRun()`), harts execute continuously until:

*   A stop condition is reached (tohost write, exit syscall)
*   An exception occurs (`CoreException`)
*   All harts complete execution

### Multi-Hart Execution

Harts can execute in two modes:

1.   **Independent threads:** Each hart runs in its own simulator thread (`stepWindow = 0`)
2.   **Round-robin:** Harts execute in sequence, each taking a random number of steps from `[stepWindowLo, stepWindowHi]`

**Sources:**[System.hpp 454-467](https://github.com/tenstorrent/whisper/blob/733fd921/System.hpp#L454-L467)

### Memory Consistency Model (MCM)

When MCM is enabled via `System::enableMcm()`, memory operations become out-of-order:

*   Load/store instructions use `System::mcmRead()` and `System::mcmMbWrite()`
*   A merge buffer coalesces stores before committing to memory
*   PPO (Preserved Program Order) rules are checked
*   Cache coherency is modeled

See [Memory Consistency Model](https://deepwiki.com/tenstorrent/whisper/4.3-memory-consistency-model) for details.

**Sources:**[System.hpp 323-393](https://github.com/tenstorrent/whisper/blob/733fd921/System.hpp#L323-L393)

* * *

## Shared Resources

All harts in the system share certain resources:

### Memory Instance

The single `Memory` object provides:

*   Physical address space (DRAM model)
*   Memory-mapped I/O routing
*   PMA (Physical Memory Attributes) management
*   ELF/hex file loading

**Sources:**[System.cpp 60-63](https://github.com/tenstorrent/whisper/blob/733fd921/System.cpp#L60-L63)[Memory.hpp](https://github.com/tenstorrent/whisper/blob/733fd921/Memory.hpp)

### Syscall Handler

A shared `Syscall<URV>` instance handles system calls from all harts:

*   File descriptor management
*   Memory mapping (mmap/munmap)
*   Process control (exit, fork, etc.)
*   Linux/newlib emulation

**Sources:**[System.cpp 55](https://github.com/tenstorrent/whisper/blob/733fd921/System.cpp#L55-L55)[Syscall.hpp](https://github.com/tenstorrent/whisper/blob/733fd921/Syscall.hpp)

### Time Counter

The `time_` variable is a shared 64-bit counter incremented on each instruction:

*   Accessible via TIME/TIMEH CSRs
*   Used for ACLINT timer interrupts
*   Shared reference passed to all harts

**Sources:**[Hart.cpp 88-99](https://github.com/tenstorrent/whisper/blob/733fd921/Hart.cpp#L88-L99)[System.cpp 54](https://github.com/tenstorrent/whisper/blob/733fd921/System.cpp#L54-L54)

* * *

## Configuration Loading

Configuration is loaded from JSON files via the `HartConfig` class:

Configuration covers:

*   **CSRs:** Implemented registers, reset values, masks ([HartConfig.cpp 227-428](https://github.com/tenstorrent/whisper/blob/733fd921/HartConfig.cpp#L227-L428))
*   **Vector unit:** VLEN, LMUL constraints, SEW ([HartConfig.cpp 788-1030](https://github.com/tenstorrent/whisper/blob/733fd921/HartConfig.cpp#L788-L1030))
*   **Triggers:** Debug trigger count and configuration ([HartConfig.cpp 491-566](https://github.com/tenstorrent/whisper/blob/733fd921/HartConfig.cpp#L491-L566))
*   **Extensions:** Enabled RISC-V extensions via ISA string ([HartConfig.cpp](https://github.com/tenstorrent/whisper/blob/733fd921/HartConfig.cpp))
*   **Memory:** PMP/PMA regions, memory map
*   **Interrupts:** IMSIC/APLIC/ACLINT settings

**Sources:**[HartConfig.cpp 49-78](https://github.com/tenstorrent/whisper/blob/733fd921/HartConfig.cpp#L49-L78)[HartConfig.cpp 227-1030](https://github.com/tenstorrent/whisper/blob/733fd921/HartConfig.cpp#L227-L1030)

* * *

## Key Methods

### System-Level Operations

| Method | Purpose |
| --- | --- |
| `System::ithHart(i)` | Access hart by system index |
| `System::findHartByHartId(id)` | Access hart by MHARTID value |
| `System::loadElfFiles()` | Load program binaries |
| `System::batchRun()` | Execute all harts |
| `System::saveSnapshot()` | Save system state |
| `System::loadSnapshot()` | Restore system state |
| `System::enableMcm()` | Enable memory consistency model |

**Sources:**[System.hpp 99-467](https://github.com/tenstorrent/whisper/blob/733fd921/System.hpp#L99-L467)

### Hart-Level Operations

| Method | Purpose |
| --- | --- |
| `Hart::singleStep()` | Execute one instruction |
| `Hart::run()` | Execute until stop condition |
| `Hart::reset()` | Reset to initial state |
| `Hart::peekIntReg()` / `Hart::pokeIntReg()` | Integer register access |
| `Hart::peekCsr()` / `Hart::pokeCsr()` | CSR access |
| `Hart::peekMemory()` / `Hart::pokeMemory()` | Memory access |

**Sources:**[Hart.hpp 203-475](https://github.com/tenstorrent/whisper/blob/733fd921/Hart.hpp#L203-L475)

* * *

## Error Handling

The architecture uses exceptions for control flow in specific situations:

`CoreException` is thrown when:

*   **Stop:** Store to tohost address
*   **Exit:** Exit system call with non-zero code
*   **Snapshot:** Snapshot instruction executed
*   **RoiEntry:** Region-of-interest entry point reached

**Sources:**[Hart.hpp 72-98](https://github.com/tenstorrent/whisper/blob/733fd921/Hart.hpp#L72-L98)

* * *

## Summary

The Whisper core architecture is organized as a three-level hierarchy:

1.   **System:** Top-level container managing cores, shared memory, and system resources
2.   **Core:** Logical grouping of hardware threads sharing a hart ID base
3.   **Hart:** Individual RISC-V execution units with complete register files and execution pipelines

This design supports flexible multi-core, multi-threaded simulation with shared memory and configurable hart topologies. The template-based design (`URV` parameter) enables both RV32 and RV64 configurations from the same codebase, while the shared resource model accurately reflects real RISC-V hardware architectures.

Dismiss
Refresh this wiki

Enter email to refresh
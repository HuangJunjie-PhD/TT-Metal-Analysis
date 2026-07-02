---
project: whisper
github: https://github.com/tenstorrent/whisper
deepwiki: https://deepwiki.com/tenstorrent/whisper
---

# Overview

 Relevant source files

* [GNUmakefile](https://github.com/tenstorrent/whisper/blob/733fd921/GNUmakefile)
* [HartConfig.cpp](https://github.com/tenstorrent/whisper/blob/733fd921/HartConfig.cpp)
* [README.md](https://github.com/tenstorrent/whisper/blob/733fd921/README.md?plain=1)
* [System.cpp](https://github.com/tenstorrent/whisper/blob/733fd921/System.cpp)
* [System.hpp](https://github.com/tenstorrent/whisper/blob/733fd921/System.hpp)
* [Triggers.cpp](https://github.com/tenstorrent/whisper/blob/733fd921/Triggers.cpp)
* [Triggers.hpp](https://github.com/tenstorrent/whisper/blob/733fd921/Triggers.hpp)
* [whisper.cpp](https://github.com/tenstorrent/whisper/blob/733fd921/whisper.cpp)

## Purpose and Scope

This page provides an introduction to the Whisper RISC-V instruction set simulator, its architecture, and how its major components interact. For detailed information about specific subsystems, see:

* Core execution and Hart implementation: [Hart Execution Unit](/tenstorrent/whisper/2.2-hart-execution-unit)
* System configuration: [System Organization and Configuration](/tenstorrent/whisper/2.1-system-organization-and-configuration)
* Memory management: [Memory Subsystem](/tenstorrent/whisper/4-memory-subsystem)
* External interfaces: [External Interfaces](/tenstorrent/whisper/6-external-interfaces)
* Debugging features: [Debug and Verification](/tenstorrent/whisper/8-debug-and-verification)

**Sources:** [README.md1-41](https://github.com/tenstorrent/whisper/blob/733fd921/README.md?plain=1#L1-L41)

---

## What is Whisper

Whisper is a RISC-V instruction set simulator (ISS) that enables execution and verification of RISC-V programs without physical hardware. It was initially developed for verification of the Swerv micro-controller and serves multiple roles:

* **Functional simulator**: Executes RISC-V binaries with full ISA support
* **Golden model**: Provides reference behavior for hardware verification
* **Debugging platform**: Supports interactive debugging and GDB integration
* **Verification tool**: Enables lock-step comparison with RTL implementations

The simulator supports both RV32 and RV64 architectures with configurable extensions including atomic operations (A), compressed instructions (C), floating-point (F/D), vector operations (V), hypervisor (H), and various other standard extensions.

**Sources:** [README.md32-41](https://github.com/tenstorrent/whisper/blob/733fd921/README.md?plain=1#L32-L41) [whisper.cpp1-114](https://github.com/tenstorrent/whisper/blob/733fd921/whisper.cpp#L1-L114)

---

## Key Features and Use Cases

| Feature | Description |
| --- | --- |
| **Multi-hart systems** | Simulates systems with multiple cores, each containing multiple hardware threads |
| **Configurable ISA** | JSON-based configuration for extensions, CSRs, memory layout, and system features |
| **Multiple execution modes** | Batch, interactive, server (socket/shared memory), and GDB remote debugging |
| **Memory consistency** | Out-of-order memory model (MCM) supporting RVWMO and TSO |
| **System-level features** | IMSIC/APLIC interrupt controllers, IOMMU, PCI devices, UART |
| **Verification support** | Lock-step execution, instruction tracing, memory ordering checks |
| **Performance modeling** | Fine-grained pipeline control via PerfApi interface |
| **Snapshots** | Save and restore complete system state |

**Sources:** [README.md232-393](https://github.com/tenstorrent/whisper/blob/733fd921/README.md?plain=1#L232-L393) [System.hpp59-529](https://github.com/tenstorrent/whisper/blob/733fd921/System.hpp#L59-L529)

---

## High-Level Architecture

**System Architecture Overview**  
 The architecture is organized in layers from application entry through orchestration, system management, core execution, and specialized subsystems. The `Session` class orchestrates simulation lifecycle, `System` manages multi-core/multi-hart organization, `Core` groups harts, and each `Hart` executes RISC-V instructions with dedicated register files.

**Sources:** [whisper.cpp30-114](https://github.com/tenstorrent/whisper/blob/733fd921/whisper.cpp#L30-L114) [System.hpp64-79](https://github.com/tenstorrent/whisper/blob/733fd921/System.hpp#L64-L79) [System.cpp50-99](https://github.com/tenstorrent/whisper/blob/733fd921/System.cpp#L50-L99)

---

## Major Components

### Entry Point and Session Management

The `whisper.cpp` main function serves as the application entry point. It:

1. Parses command-line arguments via `Args::parseCmdLineArgs()`
2. Loads JSON configuration via `HartConfig::loadConfigFile()`
3. Determines register width (32 or 64 bits) via `Session::determineRegisterWidth()`
4. Creates a `Session` instance (URV = uint32\_t or uint64\_t)
5. Invokes session methods: `defineSystem()`, `configureSystem()`, `run()`, `cleanup()`

The `Session` class manages the complete simulation lifecycle including initialization, execution, and teardown.

**Sources:** [whisper.cpp30-114](https://github.com/tenstorrent/whisper/blob/733fd921/whisper.cpp#L30-L114) [Args.cpp1-50](https://github.com/tenstorrent/whisper/blob/733fd921/Args.cpp#L1-L50) [HartConfig.cpp49-78](https://github.com/tenstorrent/whisper/blob/733fd921/HartConfig.cpp#L49-L78)

### System and Core Organization

**System Organization**  
 The `System` class manages `coreCount` cores, each containing `hartsPerCore` harts. Hart IDs follow the pattern `coreIndex * hartIdOffset + hartIndexInCore`. All harts share a single `Memory` instance and `Syscall` handler. The `System` maintains a flat vector `sysHarts_` for O(1) access to any hart by system index, and a map `hartIdToIndex_` for lookup by MHARTID CSR value.

**Sources:** [System.cpp50-81](https://github.com/tenstorrent/whisper/blob/733fd921/System.cpp#L50-L81) [System.hpp78-124](https://github.com/tenstorrent/whisper/blob/733fd921/System.hpp#L78-L124)

### Hart Execution Units

Each `Hart` represents a RISC-V hardware thread (hart) with:

* **Register files**: Integer (`IntRegs`), floating-point (`FpRegs`), vector (`VecRegs`), and CSRs (`CsRegs`)
* **Execution pipeline**: Fetch, decode, execute, memory access, writeback
* **Memory interface**: Virtual memory translation (`VirtMem`), TLBs, pointer masking
* **Debug support**: Triggers/breakpoints, single-stepping, exception handling
* **Extension support**: Vector (V), Hypervisor (H), Bit Manipulation (Zb\*), Crypto (Zk\*)

The `Hart` class has the highest importance score (1692.66) in the codebase, indicating its central role in instruction execution.

**Sources:** [Hart.hpp45-1200](https://github.com/tenstorrent/whisper/blob/733fd921/Hart.hpp#L45-L1200) [Hart.cpp100-500](https://github.com/tenstorrent/whisper/blob/733fd921/Hart.cpp#L100-L500)

### Memory Subsystem

The memory subsystem provides:

| Component | Purpose |
| --- | --- |
| `Memory` | Physical memory storage with configurable size and page granularity |
| `VirtMem` | Virtual address translation with multi-level page tables |
| `PmpManager` | Physical Memory Protection region checking |
| `PmaManager` | Physical Memory Attributes (cacheable, idempotent, etc.) |
| `Mcm` | Memory Consistency Model for out-of-order memory operations |
| `SparseMem` | Sparse memory implementation for large address spaces |

All harts share the same `Memory` instance, enabling inter-hart communication and coherency checks.

**Sources:** [Memory.hpp1-500](https://github.com/tenstorrent/whisper/blob/733fd921/Memory.hpp#L1-L500) [System.cpp60-99](https://github.com/tenstorrent/whisper/blob/733fd921/System.cpp#L60-L99)

### Configuration System

Configuration is loaded from JSON files via `HartConfig`:

**Configuration Flow**  
 JSON configuration specifies ISA extensions, CSR reset values and masks, vector parameters, trigger definitions, memory layout, and performance counter mappings. The `HartConfig` class parses the JSON and applies settings to each hart via specialized `applyConfig()` methods.

**Sources:** [HartConfig.cpp49-78](https://github.com/tenstorrent/whisper/blob/733fd921/HartConfig.cpp#L49-L78) [HartConfig.cpp227-488](https://github.com/tenstorrent/whisper/blob/733fd921/HartConfig.cpp#L227-L488)

---

## Execution Modes

Whisper supports four execution modes, selected via command-line options:

### Batch Mode (Default)

Executes the target program from start to finish. Terminates when:

* Program writes to the `tohost` symbol
* Program calls `exit()` in newlib/Linux emulation
* Eight consecutive illegal instructions are encountered
* Maximum instruction count is reached (`--maxinst`)

```
whisper program.elf 
```

### Interactive Mode (`--interactive`)

Provides a command-line interface for debugging. Commands include:

* `step [n]`: Execute n instructions
* `run`: Run until breakpoint or program end
* `peek` : Read register/memory
* `poke` : Write register/memory
* `disass` : Disassemble instructions

```
whisper --interactive program.elf 
```

### Server Mode (`--server` or `--gdb`)

Accepts commands via socket or shared memory, enabling:

* Lock-step verification with RTL simulators
* Python scripting via pywhisper bindings
* GDB remote debugging (`--gdb`)
* RISCOF test framework integration

```
whisper --server program.elf whisper --gdb program.elf # GDB remote protocol 
```

### Performance Modeling Mode

Uses the `PerfApi` interface for fine-grained pipeline control:

* Separate fetch, decode, execute, retire stages
* Store-to-load forwarding simulation
* Out-of-order instruction completion
* Cache operation modeling

**Sources:** [README.md395-541](https://github.com/tenstorrent/whisper/blob/733fd921/README.md?plain=1#L395-L541) [Interactive.hpp1-100](https://github.com/tenstorrent/whisper/blob/733fd921/Interactive.hpp#L1-L100) [Server.hpp1-200](https://github.com/tenstorrent/whisper/blob/733fd921/Server.hpp#L1-L200)

---

## Initialization Flow

**Initialization Sequence**  
 The initialization process proceeds through argument parsing, configuration loading, system creation, hart configuration, program loading, and mode-specific execution. The `Session` orchestrates this flow, delegating system creation to `System`, which creates `Core` and `Hart` instances. Configuration is applied after construction to allow for flexible customization.

**Sources:** [whisper.cpp48-114](https://github.com/tenstorrent/whisper/blob/733fd921/whisper.cpp#L48-L114) [Session.cpp1-300](https://github.com/tenstorrent/whisper/blob/733fd921/Session.cpp#L1-L300) [System.cpp50-99](https://github.com/tenstorrent/whisper/blob/733fd921/System.cpp#L50-L99)

---

## Memory and I/O Architecture

Whisper models a complete memory hierarchy and peripheral subsystem:

**Memory and I/O Integration**  
 Hart memory accesses flow through virtual address translation (if enabled), physical memory protection (PMP), and physical memory attributes (PMA) before reaching physical memory. Memory-mapped I/O is handled by `IoDevice` instances registered with `Memory`. Interrupt controllers (`ImsicMgr`, `Aplic`) deliver interrupts directly to harts. The optional `Iommu` provides device address translation for DMA operations via PCI devices.

**Sources:** [System.hpp125-293](https://github.com/tenstorrent/whisper/blob/733fd921/System.hpp#L125-L293) [System.cpp103-208](https://github.com/tenstorrent/whisper/blob/733fd921/System.cpp#L103-L208) [Memory.hpp50-300](https://github.com/tenstorrent/whisper/blob/733fd921/Memory.hpp#L50-L300)

---

## Verification and Debugging Features

Whisper provides comprehensive verification and debugging capabilities:

| Feature | Implementation | Purpose |
| --- | --- | --- |
| **Debug triggers** | `Triggers` class, TDATA1/2/3 CSRs | Hardware breakpoints on address/data/instruction count |
| **Instruction tracing** | `--logfile --csv` | CSV trace of executed instructions with register changes |
| **Memory ordering** | `Mcm` class | Verify RVWMO/TSO memory consistency in multi-hart systems |
| **Lock-step mode** | `Server` + `WhisperMessage` | Compare Whisper state with RTL after each instruction |
| **GDB integration** | `--gdb` mode | Source-level debugging via riscv-gdb |
| **Snapshots** | `saveSnapshot()/loadSnapshot()` | Capture and restore complete system state |
| **Performance API** | `PerfApi` class | Model pipeline stages and OoO execution |

**Sources:** [Triggers.hpp1-870](https://github.com/tenstorrent/whisper/blob/733fd921/Triggers.hpp#L1-L870) [Mcm.hpp1-400](https://github.com/tenstorrent/whisper/blob/733fd921/Mcm.hpp#L1-L400) [Server.hpp1-200](https://github.com/tenstorrent/whisper/blob/733fd921/Server.hpp#L1-L200)

---

## Extension Support

Whisper supports the following RISC-V extensions (configurable via `--isa` or JSON):

**Base ISAs**: RV32I, RV64I, RV32E  
 **Standard Extensions**: M (multiply/divide), A (atomic), F (single-precision FP), D (double-precision FP), C (compressed), V (vector), H (hypervisor), S (supervisor), U (user)  
 **Bit Manipulation**: Zba, Zbb, Zbc, Zbs  
 **Crypto**: Zbkb, Zbkc, Zbkx, Zknd, Zkne, Zknh, Zksed, Zksh  
 **Vector Crypto**: Zvbb, Zvbc, Zvkg, Zvkned, Zvknha, Zvknhb, Zvksed, Zvksh  
 **Other**: Zfh (half-precision FP), Zicond (conditional operations), Zicbom/Zicbop/Zicboz (cache management)

Extension support is enabled through ISA string parsing and conditional compilation, with extension-specific instruction handlers implemented in separate source files (e.g., `vector.cpp`, `crypto.cpp`, `hypervisor.cpp`).

**Sources:** [README.md263-269](https://github.com/tenstorrent/whisper/blob/733fd921/README.md?plain=1#L263-L269) [Isa.cpp1-500](https://github.com/tenstorrent/whisper/blob/733fd921/Isa.cpp#L1-L500)

---

## Next Steps

To understand specific subsystems in detail:

* System organization and configuration: [System Organization and Configuration](/tenstorrent/whisper/2.1-system-organization-and-configuration)
* Instruction execution: [Hart Execution Unit](/tenstorrent/whisper/2.2-hart-execution-unit)
* ISA details: [Instruction Set Architecture](/tenstorrent/whisper/2.3-instruction-set-architecture)
* Register architecture: [Register Architecture](/tenstorrent/whisper/3-register-architecture)
* Memory subsystem: [Memory Subsystem](/tenstorrent/whisper/4-memory-subsystem)
* RISC-V extensions: [RISC-V Extensions](/tenstorrent/whisper/5-risc-v-extensions)
* External interfaces: [External Interfaces](/tenstorrent/whisper/6-external-interfaces)
* I/O and interrupts: [I/O and Interrupt Architecture](/tenstorrent/whisper/7-io-and-interrupt-architecture)
* Debug features: [Debug and Verification](/tenstorrent/whisper/8-debug-and-verification)

**Sources:** [README.md1-900](https://github.com/tenstorrent/whisper/blob/733fd921/README.md?plain=1#L1-L900) [whisper.cpp1-114](https://github.com/tenstorrent/whisper/blob/733fd921/whisper.cpp#L1-L114) [System.hpp1-529](https://github.com/tenstorrent/whisper/blob/733fd921/System.hpp#L1-L529)

Refresh this wiki
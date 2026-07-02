---
project: ttsim
github: https://github.com/tenstorrent/ttsim
deepwiki: https://deepwiki.com/tenstorrent/ttsim/3-architecture
---

# Architecture

Relevant source files
*   [README.md](https://github.com/tenstorrent/ttsim/blob/67b1733e/README.md?plain=1)

## Purpose and Scope

This document provides a comprehensive technical overview of ttsim's internal architecture and design principles. It describes the overall system design, key components, and how they interact to provide full-system simulation of Tenstorrent hardware.

For specific details on subsystems, see:

*   System Architecture and layering: [System Architecture](https://deepwiki.com/tenstorrent/ttsim/3.1-system-architecture)
*   Hardware component simulation: [Hardware Simulation](https://deepwiki.com/tenstorrent/ttsim/3.2-hardware-simulation)
*   TT-Metalium integration mechanisms: [TT-Metalium Integration](https://deepwiki.com/tenstorrent/ttsim/3.3-tt-metalium-integration)

For practical usage guidance, see [User Guide](https://deepwiki.com/tenstorrent/ttsim/4-user-guide).

## Design Philosophy

ttsim is a **full-system simulator** that emulates Tenstorrent hardware as a shared library (`libttsim.so`). The core design principles are:

1.   **Shared Library Architecture**: ttsim is distributed as a dynamically-linked shared object that exports a simple API for hardware emulation
2.   **Bit-Exact Numerical Fidelity**: All computations aim to match silicon bit-for-bit, including NaN representations
3.   **Architecture-Specific Binaries**: Separate simulator binaries for each chip architecture (Wormhole, Blackhole)
4.   **Host-Based Execution**: Runs on Linux/x86_64 hosts, translating operations to x86 execution
5.   **Transparent Integration**: TT-Metalium workloads run unmodified by setting environment variables

The simulator trades execution speed for accessibility—it is slower than silicon but enables development without physical hardware.

**Sources:**[README.md 1-10](https://github.com/tenstorrent/ttsim/blob/67b1733e/README.md?plain=1#L1-L10)

## Component Overview

**Distribution Components**

| Component | Purpose | Architecture-Specific |
| --- | --- | --- |
| `libttsim_wh.so` | Wormhole simulator binary | Yes |
| `libttsim_bh.so` | Blackhole simulator binary | Yes |
| `wormhole_b0_80_arch.yaml` | Wormhole SOC configuration | Yes |
| `blackhole_140_arch.yaml` | Blackhole SOC configuration | Yes |
| `soc_descriptor.yaml` | Runtime SOC descriptor (copied from above) | Required at runtime |

**Integration Components**

| Component | Type | Purpose |
| --- | --- | --- |
| `TT_METAL_HOME` | Environment Variable | Locates TT-Metalium installation |
| `TT_METAL_SIMULATOR` | Environment Variable | Points to simulator `.so` file |
| `TT_METAL_SLOW_DISPATCH_MODE` | Environment Variable | Must be set to `1` (fast dispatch unsupported) |
| Simulator API | C/C++ Interface | Exported by `.so`, consumed by TT-Metalium |

**Sources:**[README.md 8-10](https://github.com/tenstorrent/ttsim/blob/67b1733e/README.md?plain=1#L8-L10)[README.md 28-61](https://github.com/tenstorrent/ttsim/blob/67b1733e/README.md?plain=1#L28-L61)[README.md 64](https://github.com/tenstorrent/ttsim/blob/67b1733e/README.md?plain=1#L64-L64)

## Layered Architecture

ttsim implements a four-layer architecture that progressively abstracts from user workloads down to simulated hardware execution:

### Layer 1: Application Layer

User workloads, examples, and tests written against the TT-Metalium API. These operate at the highest level of abstraction and are unaware whether they're running on simulator or silicon.

### Layer 2: Software Stack

TT-Metalium provides the programming interface and runtime. When `TT_METAL_SIMULATOR` is set, the stack loads the simulator shared library instead of communicating with physical hardware. The stack currently requires `TT_METAL_SLOW_DISPATCH_MODE=1` for simulator operation.

### Layer 3: Simulation Engine

The `libttsim_{wh,bh}.so` binary implements the simulator core. It parses the SOC descriptor (`soc_descriptor.yaml`) to configure the simulated device topology and exports an API that TT-Metalium calls. The device manager orchestrates execution across simulated components.

### Layer 4: Hardware Model

Individual hardware components are modeled to match silicon behavior:

*   **Tensix Core Model**: Simulates the core compute pipeline with bit-exact arithmetic for most operations
*   **DRAM Model**: Emulates memory banks and access patterns
*   **NoC Model**: Simulates the network-on-chip interconnect

All models execute on the Linux/x86_64 host using native CPU instructions.

**Sources:**[README.md 1-10](https://github.com/tenstorrent/ttsim/blob/67b1733e/README.md?plain=1#L1-L10)[README.md 41-61](https://github.com/tenstorrent/ttsim/blob/67b1733e/README.md?plain=1#L41-L61)[README.md 99-102](https://github.com/tenstorrent/ttsim/blob/67b1733e/README.md?plain=1#L99-L102)

## Execution Model

### Initialization Sequence

1.   **Environment Discovery**: TT-Metalium reads `TT_METAL_SIMULATOR` to locate the simulator binary
2.   **Library Loading**: The appropriate `.so` file is loaded via `dlopen()`
3.   **Configuration Parsing**: The simulator reads `soc_descriptor.yaml` from the same directory as the `.so`
4.   **Device Initialization**: Virtual hardware components are instantiated based on SOC topology
5.   **API Registration**: Function pointers are established for communication

### Execution Flow

1.   **Dispatch Mode Check**: `TT_METAL_SLOW_DISPATCH_MODE` must equal `1` (fast dispatch unimplemented)
2.   **API Invocation**: TT-Metalium calls simulator API functions to program and execute operations
3.   **Hardware Simulation**: Operations are translated to x86 instructions and executed on host
4.   **Result Return**: Computed values are returned to TT-Metalium with bit-exact fidelity (where implemented)

**Sources:**[README.md 41-61](https://github.com/tenstorrent/ttsim/blob/67b1733e/README.md?plain=1#L41-L61)[README.md 64](https://github.com/tenstorrent/ttsim/blob/67b1733e/README.md?plain=1#L64-L64)

## Numerical Accuracy Architecture

ttsim's bit-exactness goal requires careful modeling of hardware arithmetic units:

### Implementation Status

| Component | Status | Details |
| --- | --- | --- |
| Unpacker | ✓ Bit-exact | All unpack operations match silicon |
| SFPU | ✓ Bit-exact | Special function unit operations match silicon |
| Packer | ✓ Bit-exact | All pack operations match silicon |
| FPU (MOV*, GMPOOL) | ✓ Bit-exact | Move and global max pool operations match |
| FPU (ELW*, MVMUL, GAPOOL) | ⚠ Partial | Element-wise, matrix-vector multiply, and global average pool in progress |

### Sources of Divergence

Even with bit-exact arithmetic, results may diverge from silicon in specific cases:

| Condition | Cause | Mitigation |
| --- | --- | --- |
| Timing-dependent operations | Operation order not serialized | Explicitly order operations |
| Hardware entropy sources | Random number generator reads | Use deterministic alternatives |
| Performance counters | Cycle counter reads | Avoid timing-dependent logic |
| Missing synchronization | Race conditions | Add proper synchronization |
| UndefinedBehavior execution | ISA contract violations | Fix code to follow ISA specification |

**Sources:**[README.md 78-102](https://github.com/tenstorrent/ttsim/blob/67b1733e/README.md?plain=1#L78-L102)

## Error Reporting Architecture

ttsim implements a hierarchical error categorization system to distinguish error classes:

### Error Categories

| Category | Meaning | Action Required |
| --- | --- | --- |
| `UndefinedBehavior` | ISA contract violation | Fix application code |
| `UnpredictableValueUsed` | Non-deterministic value used | Fix application code |
| `NonContractualBehavior` | Behavior outside ISA guarantees | Fix application code |
| `UntestedFunctionality` | Implemented but not validated | Report if encountered |
| `UnimplementedFunctionality` | Planned for future support | Report to track priority |
| `UnsupportedFunctionality` | Unlikely to be implemented | Use alternative approach |
| `MissingSpecification` | Requires internal specification work | Report to track priority |
| `SystemError` | OS-level error | Check system configuration |
| `ConfigurationError` | Invalid environment/configuration | Fix environment variables/files |
| `AssertionFailure` | Simulator bug | Report as bug |

**Sources:**[README.md 66-76](https://github.com/tenstorrent/ttsim/blob/67b1733e/README.md?plain=1#L66-L76)

## Configuration Architecture

ttsim requires co-located configuration files and specific environment variables:

### Required Configuration

1.   **Simulator Binary**: `libttsim_{wh,bh}.so` must be downloadable and readable
2.   **SOC Descriptor**: `soc_descriptor.yaml` must exist in the same directory as the `.so` file
3.   **Environment Variables**: 
    *   `TT_METAL_HOME`: Points to TT-Metalium installation root
    *   `TT_METAL_SIMULATOR`: Absolute or relative path to simulator `.so` file
    *   `TT_METAL_SLOW_DISPATCH_MODE`: Must be set to `1`

The simulator locates its SOC descriptor by searching in the same directory as the loaded `.so` file, requiring these files to be co-located.

**Sources:**[README.md 41-61](https://github.com/tenstorrent/ttsim/blob/67b1733e/README.md?plain=1#L41-L61)[README.md 64](https://github.com/tenstorrent/ttsim/blob/67b1733e/README.md?plain=1#L64-L64)

## Summary

ttsim's architecture prioritizes:

*   **Modularity**: Separate binaries per chip architecture
*   **Bit-Exactness**: Hardware-faithful arithmetic across most operations
*   **Transparency**: Unmodified workloads run via environment configuration
*   **Error Clarity**: Detailed error categorization for debugging
*   **Host Performance**: Execution on commodity x86_64 hardware

The layered design separates concerns: applications use TT-Metalium APIs, TT-Metalium loads the simulator transparently, and the simulator models hardware components with increasing fidelity. This architecture enables rapid iteration on Tenstorrent workloads without silicon access.

**Sources:**[README.md 1-120](https://github.com/tenstorrent/ttsim/blob/67b1733e/README.md?plain=1#L1-L120)

Dismiss
Refresh this wiki

Enter email to refresh
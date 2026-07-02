---
project: tt-isa-documentation
github: https://github.com/tenstorrent/tt-isa-documentation
deepwiki: https://deepwiki.com/tenstorrent/tt-isa-documentation/7-programming-guide)
---

# Programming Guide

Relevant source files
*   [BlackholeA0/TensixTile/BabyRISCV/InstructionSet.md](https://github.com/tenstorrent/tt-isa-documentation/blob/9600a914/BlackholeA0/TensixTile/BabyRISCV/InstructionSet.md?plain=1)
*   [BlackholeA0/TensixTile/TensixCoprocessor/SFPMAD.md](https://github.com/tenstorrent/tt-isa-documentation/blob/9600a914/BlackholeA0/TensixTile/TensixCoprocessor/SFPMAD.md?plain=1)
*   [Diagrams/Out/Bits32_MOVB2A.svg](https://github.com/tenstorrent/tt-isa-documentation/blob/9600a914/Diagrams/Out/Bits32_MOVB2A.svg)
*   [Miscellaneous/FMA/README.md](https://github.com/tenstorrent/tt-isa-documentation/blob/9600a914/Miscellaneous/FMA/README.md?plain=1)
*   [Miscellaneous/FMA/fma.c](https://github.com/tenstorrent/tt-isa-documentation/blob/9600a914/Miscellaneous/FMA/fma.c)
*   [WormholeB0/EthernetTile/BabyRISCV/README.md](https://github.com/tenstorrent/tt-isa-documentation/blob/9600a914/WormholeB0/EthernetTile/BabyRISCV/README.md?plain=1)
*   [WormholeB0/TensixTile/BabyRISCV/Mailboxes.md](https://github.com/tenstorrent/tt-isa-documentation/blob/9600a914/WormholeB0/TensixTile/BabyRISCV/Mailboxes.md?plain=1)
*   [WormholeB0/TensixTile/BabyRISCV/README.md](https://github.com/tenstorrent/tt-isa-documentation/blob/9600a914/WormholeB0/TensixTile/BabyRISCV/README.md?plain=1)
*   [WormholeB0/TensixTile/PIC.md](https://github.com/tenstorrent/tt-isa-documentation/blob/9600a914/WormholeB0/TensixTile/PIC.md?plain=1)
*   [WormholeB0/TensixTile/README.md](https://github.com/tenstorrent/tt-isa-documentation/blob/9600a914/WormholeB0/TensixTile/README.md?plain=1)
*   [WormholeB0/TensixTile/TensixCoprocessor/MOVB2A.md](https://github.com/tenstorrent/tt-isa-documentation/blob/9600a914/WormholeB0/TensixTile/TensixCoprocessor/MOVB2A.md?plain=1)
*   [WormholeB0/TensixTile/TensixCoprocessor/MatrixUnit.md](https://github.com/tenstorrent/tt-isa-documentation/blob/9600a914/WormholeB0/TensixTile/TensixCoprocessor/MatrixUnit.md?plain=1)
*   [WormholeB0/TensixTile/TensixCoprocessor/RWCs.md](https://github.com/tenstorrent/tt-isa-documentation/blob/9600a914/WormholeB0/TensixTile/TensixCoprocessor/RWCs.md?plain=1)
*   [WormholeB0/TensixTile/TensixCoprocessor/SFPMAD.md](https://github.com/tenstorrent/tt-isa-documentation/blob/9600a914/WormholeB0/TensixTile/TensixCoprocessor/SFPMAD.md?plain=1)

## Purpose and Scope

This guide provides practical guidance for writing efficient code on Tenstorrent Tensix tiles. It covers the programming model, key architectural concepts, performance characteristics, and common patterns for achieving high performance. The guide focuses on understanding how to orchestrate the RISC-V control processors and the Tensix Coprocessor to achieve optimal throughput.

For detailed reference material on specific instructions, see [Instruction Set Reference](https://deepwiki.com/tenstorrent/tt-isa-documentation/5-instruction-set-reference). For data type information and numerical behavior, see [Data Formats and Computation Models](https://deepwiki.com/tenstorrent/tt-isa-documentation/6-data-formats-and-computation-models). For architecture-specific differences, see [Architecture Comparison](https://deepwiki.com/tenstorrent/tt-isa-documentation/1.1-architecture-comparison-(wormhole-vs-blackhole)).

This page provides an overview of the programming model and high-level guidance. For detailed information on specific topics, see:

*   [Memory Access Patterns and Optimization](https://deepwiki.com/tenstorrent/tt-isa-documentation/7.1-memory-access-patterns-and-optimization)
*   [Instruction Scheduling and Hazards](https://deepwiki.com/tenstorrent/tt-isa-documentation/7.2-instruction-scheduling-and-hazards)
*   [Kernel Structure and MOP Usage](https://deepwiki.com/tenstorrent/tt-isa-documentation/7.3-kernel-structure-and-mop-usage)
*   [Debug and Performance Monitoring](https://deepwiki.com/tenstorrent/tt-isa-documentation/7.4-debug-and-performance-monitoring)

**Sources:**[WormholeB0/TensixTile/README.md 1-16](https://github.com/tenstorrent/tt-isa-documentation/blob/9600a914/WormholeB0/TensixTile/README.md?plain=1#L1-L16)

## Programming Model Overview

The Tensix architecture is fundamentally a **controller-accelerator model**: RISC-V cores orchestrate computation rather than performing it directly. High performance is achieved by having RISC-V cores issue instructions to specialized execution units that operate concurrently.

### Controller-Accelerator Architecture

**Key Insight:** RISC-V cores do not compute directly. They push instructions into the Tensix Coprocessor frontend, which then executes them on specialized hardware. This means:

1.   RISC-V instruction throughput is **not** the bottleneck for compute performance
2.   RISC-V cores should focus on orchestration: preparing configuration, issuing coprocessor instructions, coordinating phases
3.   Coprocessor instruction throughput (typically 1 instruction/cycle) determines compute performance

**Sources:**[WormholeB0/TensixTile/README.md 1-16](https://github.com/tenstorrent/tt-isa-documentation/blob/9600a914/WormholeB0/TensixTile/README.md?plain=1#L1-L16)[WormholeB0/TensixTile/BabyRISCV/README.md 1-13](https://github.com/tenstorrent/tt-isa-documentation/blob/9600a914/WormholeB0/TensixTile/BabyRISCV/README.md?plain=1#L1-L13)

### Three-Phase Kernel Structure

Compute kernels are typically organized into three phases, each running on a dedicated RISC-V thread:

This separation allows concurrent execution: while T2 is packing results from iteration N, T1 can be computing iteration N+1, and T0 can be unpacking data for iteration N+2.

**Sources:**[WormholeB0/TensixTile/BabyRISCV/README.md 13](https://github.com/tenstorrent/tt-isa-documentation/blob/9600a914/WormholeB0/TensixTile/BabyRISCV/README.md?plain=1#L13-L13)

## Execution Units and Performance Characteristics

Understanding the throughput and latency of each execution unit is critical for achieving high performance.

### Matrix Unit (FPU) Performance

The Matrix Unit delivers the highest computational throughput, but requires understanding of fidelity phases and instruction throughput:

| Instruction | Throughput (instructions/cycle) | Latency (cycles) | Peak Performance (1 phase @ 1 GHz) |
| --- | --- | --- | --- |
| `MVMUL` | 1 per fidelity phase | 5 | 4.096 TFLOP/s |
| `DOTPV` | 1 per fidelity phase | 5 | 4.096 TFLOP/s |
| `GAPOOL` | 1 per fidelity phase | 5 | 2.048 TFLOP/s |
| `ELWMUL` | 1 per fidelity phase | 5 | 0.256 TFLOP/s |
| `GMPOOL` | 1 (no phases) | 5 | 0.256 TFLOP/s |
| `ELWADD`/`ELWSUB` | 1 (no phases) | 5 | 0.256 TFLOP/s |
| `SETRWC` | 1 | 1 | N/A |
| `MOVB2A` | 1 | 4 | N/A |

**Critical Notes:**

1.   **Fidelity phases** affect throughput: Using 4 phases reduces `MVMUL` throughput to 1.024 TFLOP/s (¼ of peak)
2.   **Instruction latency is 5 cycles** for most compute operations, but this can be hidden by issuing independent instructions
3.   Data movement instructions like `MOVB2A` have lower latency (4 cycles) but special scheduling constraints

**Sources:**[WormholeB0/TensixTile/TensixCoprocessor/MatrixUnit.md 21-70](https://github.com/tenstorrent/tt-isa-documentation/blob/9600a914/WormholeB0/TensixTile/TensixCoprocessor/MatrixUnit.md?plain=1#L21-L70)

### Vector Unit (SFPU) Performance

The Vector Unit operates on 32-wide SIMD lanes with FP32 precision:

| Instruction | Throughput | Latency (cycles) | Peak Performance (@ 1 GHz) |
| --- | --- | --- | --- |
| `SFPMAD` | 1 instruction/cycle | 2 | 0.064 TFLOP/s |
| `SFPLUT` | 1 instruction/cycle | Varies | N/A |
| `SFPLOAD` | 1 instruction/cycle | 3 | N/A |
| `SFPSTORE` | 1 instruction/cycle | 3 | N/A |

**Critical Notes:**

1.   Vector Unit is **64x slower** than Matrix Unit for multiply-accumulate operations (0.064 vs 4.096 TFLOP/s)
2.   Use Vector Unit for operations requiring full FP32 precision or operations not supported by Matrix Unit
3.   Wormhole `SFPMAD` requires manual insertion of `SFPNOP` to avoid hazards; Blackhole has automatic stalling

**Sources:**[WormholeB0/TensixTile/TensixCoprocessor/SFPMAD.md 65-67](https://github.com/tenstorrent/tt-isa-documentation/blob/9600a914/WormholeB0/TensixTile/TensixCoprocessor/SFPMAD.md?plain=1#L65-L67)[BlackholeA0/TensixTile/TensixCoprocessor/SFPMAD.md 68-90](https://github.com/tenstorrent/tt-isa-documentation/blob/9600a914/BlackholeA0/TensixTile/TensixCoprocessor/SFPMAD.md?plain=1#L68-L90)

### Memory Bandwidth Characteristics

Memory bandwidth limits are often the practical bottleneck:

| Path | Bandwidth (bits per cycle @ 1 GHz) | Notes |
| --- | --- | --- |
| Unpackers 0+1 together | 640 bits/cycle | 5×128-bit/cycle |
| Single Unpacker | 512 bits/cycle | 4×128-bit/cycle |
| Single Packer | 128 bits/cycle | Can have 4 packers active |
| RISC-V load | ~18.3 bits/cycle | Very slow; use for control only |
| RISC-V store | ~6.4 bits/cycle | Very slow; use for control only |
| Mover (L1↔L1) | ~93 bits/cycle read + ~93 bits/cycle write |  |

**Critical Notes:**

1.   **RISC-V memory access is extremely slow** (18.3 bpc load vs 512 bpc unpacker) - use only for orchestration
2.   Use **local RAM** for RISC-V variables (2-cycle latency vs 8+ cycles for L1)
3.   Unpacker and Packer bandwidth is the typical bottleneck for sustained computation

**Sources:**[WormholeB0/TensixTile/BabyRISCV/README.md 58-69](https://github.com/tenstorrent/tt-isa-documentation/blob/9600a914/WormholeB0/TensixTile/BabyRISCV/README.md?plain=1#L58-L69)

## Instruction Frontend Pipeline

Between RISC-V cores and backend execution units, instructions flow through a multi-stage frontend that provides powerful instruction-level parallelism features:

**Key Capabilities:**

1.   **MOP Expander**: Emit up to 32,639 instructions from a single macro-operation
2.   **Replay Expander**: Loop over 32 instructions, executing them 1-32 times
3.   **Wait Gate**: Automatically stall until synchronization conditions are met
4.   **Deep buffering**: ~43 instructions can be queued between RISC-V and backend

This frontend enables 1 instruction/cycle throughput to the backend even though RISC-V cores push instructions at sub-cycle rates.

**Sources:** System Diagram 4 from high-level architecture

## Memory Hierarchy and Access Patterns

### L1 Scratchpad Organization

L1 is organized as 16 independent banks with 16 access ports, enabling concurrent access:

**Bank Conflict Avoidance:**

1.   Address `A` maps to bank `(A >> 4) & 15` - use this to distribute data
2.   Accesses to different banks on the same port can proceed concurrently
3.   Accesses to the same bank on the same port are serialized (adds latency)

**Sources:**[WormholeB0/TensixTile/BabyRISCV/README.md 56-69](https://github.com/tenstorrent/tt-isa-documentation/blob/9600a914/WormholeB0/TensixTile/BabyRISCV/README.md?plain=1#L56-L69)

### Local RAM Usage

Each RISC-V core has fast local RAM with 2-cycle latency (vs 8+ cycles for L1):

| Core | Local RAM Size | Typical Usage |
| --- | --- | --- |
| RISCV B | 4 KiB | Call stack, frequently-accessed variables |
| RISCV T0 | 2 KiB | Call stack, loop counters, configuration |
| RISCV T1 | 2 KiB | Call stack, loop counters, configuration |
| RISCV T2 | 2 KiB | Call stack, loop counters, configuration |
| RISCV NC | 4 KiB | Call stack, DMA descriptors |

**Best Practice:** Place call stacks and frequently-accessed thread-local variables in local RAM to avoid L1 access overhead.

**Sources:**[WormholeB0/TensixTile/BabyRISCV/README.md 117-119](https://github.com/tenstorrent/tt-isa-documentation/blob/9600a914/WormholeB0/TensixTile/BabyRISCV/README.md?plain=1#L117-L119)

## RISC-V Pipeline Characteristics

Understanding RISC-V pipeline behavior is important for efficient orchestration code:

### Pipeline Stages

**Key Characteristics:**

1.   **In-order issue and retirement**, but Load/Store Unit can reorder internally
2.   **Load latency hiding**: Need N-1 independent instructions to hide N-cycle load latency
3.   **Store throughput to L1**: Maximum 1 store per 5 cycles (very slow)
4.   **Multiply latency**: 2 cycles (blocks Integer Unit for 2 cycles)
5.   **Divide latency**: 6-33 cycles depending on operand values

**Sources:**[WormholeB0/TensixTile/BabyRISCV/README.md 28-75](https://github.com/tenstorrent/tt-isa-documentation/blob/9600a914/WormholeB0/TensixTile/BabyRISCV/README.md?plain=1#L28-L75)

### Load/Store Latencies

| Memory Region | Load Latency | Max Concurrent Loads |
| --- | --- | --- |
| Local RAM | 2 cycles | 8 |
| L1 (no conflict) | 8+ cycles | 4 (for slow regions) |
| Mailboxes | 3+ cycles | 4 (aggregate) |
| MMIO registers | 7+ cycles | 4 (aggregate) |

**Critical:** Load latency of 8 cycles means you need **7 independent instructions** between the load and its first use to fully hide latency. For simple orchestration loops, this is often impossible, so expect stalls.

**Sources:**[WormholeB0/TensixTile/BabyRISCV/README.md 58-69](https://github.com/tenstorrent/tt-isa-documentation/blob/9600a914/WormholeB0/TensixTile/BabyRISCV/README.md?plain=1#L58-L69)

## Instruction Scheduling Principles

### Hiding Backend Latency

Most backend instructions have multi-cycle latency but can issue 1 per cycle:

To maintain 1 instruction/cycle throughput, ensure each instruction is independent of the results of the previous 4 instructions (for 5-cycle latency operations).

### Avoiding Hazards

Some instruction sequences require explicit stalling:

**Wormhole SFPMAD:** Manual `SFPNOP` insertion required

**Blackhole SFPMAD:** Automatic stalling (but some edge cases remain; see detailed warnings)

**MOVB2A:** Limited instruction acceptance on next cycle

**Sources:**[WormholeB0/TensixTile/TensixCoprocessor/SFPMAD.md 61-63](https://github.com/tenstorrent/tt-isa-documentation/blob/9600a914/WormholeB0/TensixTile/TensixCoprocessor/SFPMAD.md?plain=1#L61-L63)[BlackholeA0/TensixTile/TensixCoprocessor/SFPMAD.md 68-87](https://github.com/tenstorrent/tt-isa-documentation/blob/9600a914/BlackholeA0/TensixTile/TensixCoprocessor/SFPMAD.md?plain=1#L68-L87)[WormholeB0/TensixTile/TensixCoprocessor/MOVB2A.md 64-69](https://github.com/tenstorrent/tt-isa-documentation/blob/9600a914/WormholeB0/TensixTile/TensixCoprocessor/MOVB2A.md?plain=1#L64-L69)

## Common Programming Patterns

### Pattern 1: Tiled Matrix Multiply

### Pattern 2: Vectorized Element-Wise Operations

Use Vector Unit when Matrix Unit is insufficient:

*   Operations requiring full FP32 precision (Matrix Unit is limited to ≤19-bit inputs)
*   Non-multiply operations (e.g., division via reciprocal approximation)
*   Lane-specific operations (using `LaneConfig` to enable/disable lanes)

### Pattern 3: Double Buffering

Overlap computation with data movement:

**Sources:**[WormholeB0/TensixTile/README.md 16](https://github.com/tenstorrent/tt-isa-documentation/blob/9600a914/WormholeB0/TensixTile/README.md?plain=1#L16-L16)

## Configuration Management

### Backend Configuration System

Backend configuration (`Config` and `ThreadConfig` arrays) controls execution unit behavior:

**Race Condition Warning:** Both RISC-V and Tensix backend can access configuration simultaneously, potentially causing races. Typical pattern: RISC-V sets up configuration before pushing Tensix instructions, Tensix instructions read (but don't write) during execution.

**Sources:** System Diagram 4 from high-level architecture

## Synchronization Mechanisms

### Inter-Thread Communication

| Mechanism | Direction | Capacity | Use Case |
| --- | --- | --- | --- |
| Mailboxes | Any RISC-V ↔ Any RISC-V (except NC) | 4×32-bit FIFO | Data passing, notifications |
| PCBufs | RISCV B → T0/T1/T2 | 1×32-bit | Pushing instruction addresses |
| TTSync | Any thread | Event flags | Phase synchronization |
| Semaphores | Backend threads | 8×4-bit counters | Tensix instruction sync |

**Mailboxes:** Primary mechanism for RISC-V coordination. Non-blocking query available.

**PCBufs:** Specialized for RISCV B to push instruction buffer addresses to T0/T1/T2, enabling B to control when T threads start executing.

**Semaphores:** Used by Wait Gate in Tensix frontend to stall instructions until conditions are met.

**Sources:**[WormholeB0/TensixTile/BabyRISCV/Mailboxes.md 1-46](https://github.com/tenstorrent/tt-isa-documentation/blob/9600a914/WormholeB0/TensixTile/BabyRISCV/Mailboxes.md?plain=1#L1-L46)

## Performance Monitoring

### Available Counters

The PIC (Programmable Interrupt Controller) provides IRQ tracking but limited performance counters:

| Counter | Access | Purpose |
| --- | --- | --- |
| NoC transaction counters | Via NoC registers | Track data movement |
| `cycle` CSR | Per RISC-V core | Cycle count since reset |
| `instret` CSR | Per RISC-V core | Retired instruction count |

**Note:** Unlike general-purpose processors, there are no performance counters for backend execution units. Performance analysis relies on:

1.   Understanding theoretical peak performance (see tables above)
2.   Measuring overall execution time via `cycle` CSR
3.   Calculating achieved percentage of peak

**Sources:**[WormholeB0/TensixTile/PIC.md 1-101](https://github.com/tenstorrent/tt-isa-documentation/blob/9600a914/WormholeB0/TensixTile/PIC.md?plain=1#L1-L101)[BlackholeA0/TensixTile/BabyRISCV/InstructionSet.md 16-18](https://github.com/tenstorrent/tt-isa-documentation/blob/9600a914/BlackholeA0/TensixTile/BabyRISCV/InstructionSet.md?plain=1#L16-L18)

## Architecture-Specific Considerations

### Blackhole Improvements

Blackhole A0 includes several improvements over Wormhole B0:

1.   **Automatic SFPMAD stalling:** Hardware detects most (but not all) read-after-write hazards
2.   **Improved FMA handling:** Better NaN handling, negative zero handling, canonical NaNs
3.   **FMA instructions on RISC-V:**`fmadd.s`, `fmsub.s`, `fnmsub.s`, `fnmadd.s` with non-IEEE semantics
4.   **Enhanced SFPLUTFP32:** Full FP32 LUT operations

**Portability Note:** Code using manual `SFPNOP` insertion (required for Wormhole) will work correctly on Blackhole (just with unnecessary NOPs). Code relying on automatic stalling (Blackhole) will fail on Wormhole.

**Sources:**[BlackholeA0/TensixTile/TensixCoprocessor/SFPMAD.md 8](https://github.com/tenstorrent/tt-isa-documentation/blob/9600a914/BlackholeA0/TensixTile/TensixCoprocessor/SFPMAD.md?plain=1#L8-L8)[Miscellaneous/FMA/README.md 1-10](https://github.com/tenstorrent/tt-isa-documentation/blob/9600a914/Miscellaneous/FMA/README.md?plain=1#L1-L10)

## Summary: Achieving High Performance

1.   **Use the right execution unit:** Matrix Unit for throughput, Vector Unit for precision/flexibility
2.   **Minimize RISC-V memory access:** Use local RAM for variables, push coprocessor instructions via FIFO
3.   **Hide latency with independent instructions:** 7+ independent instructions for L1 loads, 4+ for backend operations
4.   **Avoid bank conflicts:** Distribute L1 data across banks using address layout
5.   **Use instruction expanders:** MOP and Replay expanders eliminate RISC-V overhead for loops
6.   **Pipeline phases:** Overlap UNPACK/MATH/PACK phases across iterations
7.   **Double buffer:** Overlap data movement (NoC, Unpacker, Packer) with computation
8.   **Understand fidelity phases:** Fewer phases = higher throughput, but may sacrifice precision

For detailed information on each of these topics, see [Memory Access Patterns and Optimization](https://deepwiki.com/tenstorrent/tt-isa-documentation/7.1-memory-access-patterns-and-optimization), [Instruction Scheduling and Hazards](https://deepwiki.com/tenstorrent/tt-isa-documentation/7.2-instruction-scheduling-and-hazards), [Kernel Structure and MOP Usage](https://deepwiki.com/tenstorrent/tt-isa-documentation/7.3-kernel-structure-and-mop-usage), and [Debug and Performance Monitoring](https://deepwiki.com/tenstorrent/tt-isa-documentation/7.4-debug-and-performance-monitoring).

**Sources:**[WormholeB0/TensixTile/TensixCoprocessor/MatrixUnit.md 1-76](https://github.com/tenstorrent/tt-isa-documentation/blob/9600a914/WormholeB0/TensixTile/TensixCoprocessor/MatrixUnit.md?plain=1#L1-L76)[WormholeB0/TensixTile/BabyRISCV/README.md 1-120](https://github.com/tenstorrent/tt-isa-documentation/blob/9600a914/WormholeB0/TensixTile/BabyRISCV/README.md?plain=1#L1-L120)

Dismiss
Refresh this wiki

Enter email to refresh
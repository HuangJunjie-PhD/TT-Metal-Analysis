---
project: riscv-ocelot
github: https://github.com/tenstorrent/riscv-ocelot
deepwiki: https://deepwiki.com/tenstorrent/riscv-ocelot/2-core-architecture
---

# Core Architecture

Relevant source files
*   [docs/sections/decode-stage.rst](https://github.com/tenstorrent/riscv-ocelot/blob/920fd904/docs/sections/decode-stage.rst)
*   [docs/sections/execution-stages.rst](https://github.com/tenstorrent/riscv-ocelot/blob/920fd904/docs/sections/execution-stages.rst)
*   [docs/sections/future-work.rst](https://github.com/tenstorrent/riscv-ocelot/blob/920fd904/docs/sections/future-work.rst)
*   [docs/sections/issue-units.rst](https://github.com/tenstorrent/riscv-ocelot/blob/920fd904/docs/sections/issue-units.rst)
*   [docs/sections/load-store-unit.rst](https://github.com/tenstorrent/riscv-ocelot/blob/920fd904/docs/sections/load-store-unit.rst)
*   [docs/sections/memory-system.rst](https://github.com/tenstorrent/riscv-ocelot/blob/920fd904/docs/sections/memory-system.rst)
*   [docs/sections/physical-realization.rst](https://github.com/tenstorrent/riscv-ocelot/blob/920fd904/docs/sections/physical-realization.rst)
*   [docs/sections/reg-file-bypass-network.rst](https://github.com/tenstorrent/riscv-ocelot/blob/920fd904/docs/sections/reg-file-bypass-network.rst)
*   [docs/sections/rename-stage.rst](https://github.com/tenstorrent/riscv-ocelot/blob/920fd904/docs/sections/rename-stage.rst)
*   [docs/sections/reorder-buffer.rst](https://github.com/tenstorrent/riscv-ocelot/blob/920fd904/docs/sections/reorder-buffer.rst)
*   [src/main/scala/common/consts.scala](https://github.com/tenstorrent/riscv-ocelot/blob/920fd904/src/main/scala/common/consts.scala)
*   [src/main/scala/common/micro-op.scala](https://github.com/tenstorrent/riscv-ocelot/blob/920fd904/src/main/scala/common/micro-op.scala)
*   [src/main/scala/common/parameters.scala](https://github.com/tenstorrent/riscv-ocelot/blob/920fd904/src/main/scala/common/parameters.scala)
*   [src/main/scala/common/types.scala](https://github.com/tenstorrent/riscv-ocelot/blob/920fd904/src/main/scala/common/types.scala)
*   [src/main/scala/exu/core.scala](https://github.com/tenstorrent/riscv-ocelot/blob/920fd904/src/main/scala/exu/core.scala)
*   [src/main/scala/exu/decode.scala](https://github.com/tenstorrent/riscv-ocelot/blob/920fd904/src/main/scala/exu/decode.scala)
*   [src/main/scala/exu/execution-units/execution-unit.scala](https://github.com/tenstorrent/riscv-ocelot/blob/920fd904/src/main/scala/exu/execution-units/execution-unit.scala)
*   [src/main/scala/exu/execution-units/execution-units.scala](https://github.com/tenstorrent/riscv-ocelot/blob/920fd904/src/main/scala/exu/execution-units/execution-units.scala)
*   [src/main/scala/exu/execution-units/functional-unit.scala](https://github.com/tenstorrent/riscv-ocelot/blob/920fd904/src/main/scala/exu/execution-units/functional-unit.scala)
*   [src/main/scala/exu/execution-units/rocc.scala](https://github.com/tenstorrent/riscv-ocelot/blob/920fd904/src/main/scala/exu/execution-units/rocc.scala)
*   [src/main/scala/exu/rob.scala](https://github.com/tenstorrent/riscv-ocelot/blob/920fd904/src/main/scala/exu/rob.scala)

This page provides an overview of BOOM's core architecture, explaining the high-level organization of the processor and its primary components. It serves as a central reference for understanding how the major subsystems of the RISC-V Ocelot processor are structured and interact with each other. For information about specific subsystems like the memory hierarchy, see [Memory System](https://deepwiki.com/tenstorrent/riscv-ocelot/3-memory-system), or for instruction fetching details, see [Instruction Fetch Pipeline](https://deepwiki.com/tenstorrent/riscv-ocelot/4-instruction-fetch-pipeline).

## Pipeline Overview

BOOM (Berkeley Out-of-Order Machine) is an out-of-order, superscalar RISC-V processor implementation. The core pipeline can be conceptually divided into frontend and backend sections with multiple stages:

Sources: [src/main/scala/exu/core.scala 386-471](https://github.com/tenstorrent/riscv-ocelot/blob/920fd904/src/main/scala/exu/core.scala#L386-L471)[src/main/scala/exu/core.scala 492-723](https://github.com/tenstorrent/riscv-ocelot/blob/920fd904/src/main/scala/exu/core.scala#L492-L723)

The pipeline stages are:

1.   **Fetch (F0-F3)**: Selects next PC, accesses instruction cache, and places fetched instructions into the fetch buffer
2.   **Decode**: Transforms instructions into micro-ops (uops) with appropriate control signals
3.   **Rename**: Maps logical registers to physical registers to eliminate false dependencies
4.   **Dispatch**: Allocates ROB entries and sends uops to issue queues
5.   **Issue**: Dynamically schedules ready uops to appropriate execution units
6.   **Register Read**: Reads operands from the register file
7.   **Execute**: Performs computations in functional units
8.   **Memory Access**: For memory operations, accesses the data cache
9.   **Commit**: Retires instructions in program order, updating architectural state

## BoomCore Organization

The `BoomCore` class is the top-level module that implements the processor core. It instantiates all the necessary components and connects them together:

Sources: [src/main/scala/exu/core.scala 51-113](https://github.com/tenstorrent/riscv-ocelot/blob/920fd904/src/main/scala/exu/core.scala#L51-L113)[src/main/scala/exu/core.scala 65-76](https://github.com/tenstorrent/riscv-ocelot/blob/920fd904/src/main/scala/exu/core.scala#L65-L76)

## Micro-Ops (UOPs)

Instructions fetched from memory are decoded into micro-ops (uops) that flow through the pipeline. The `MicroOp` class encapsulates all information needed for an instruction:

```
MicroOp Fields:
- uopc: Micro-op code indicating operation type
- inst: Original instruction bits
- pc_lob: Low-order bits of the PC
- fu_code: Functional unit code
- iq_type: Issue queue type
- br_mask: Speculative branch mask
- rob_idx: Index in the reorder buffer
- ldq_idx/stq_idx: Load/store queue indices
- pdst/prs1/prs2: Physical destination/source registers
- ldst/lrs1/lrs2: Logical destination/source registers
- bypass-related fields
- exception-related fields
- control signals
```

Sources: [src/main/scala/common/micro-op.scala 32-156](https://github.com/tenstorrent/riscv-ocelot/blob/920fd904/src/main/scala/common/micro-op.scala#L32-L156)

## Execution Units

The execution units perform the actual computations. BOOM implements a collection of execution units managed by the `ExecutionUnits` class:

Sources: [src/main/scala/exu/execution-units/execution-units.scala 28-186](https://github.com/tenstorrent/riscv-ocelot/blob/920fd904/src/main/scala/exu/execution-units/execution-units.scala#L28-L186)[src/main/scala/exu/execution-units/execution-unit.scala 81-187](https://github.com/tenstorrent/riscv-ocelot/blob/920fd904/src/main/scala/exu/execution-units/execution-unit.scala#L81-L187)[src/main/scala/exu/execution-units/functional-unit.scala 82-187](https://github.com/tenstorrent/riscv-ocelot/blob/920fd904/src/main/scala/exu/execution-units/functional-unit.scala#L82-L187)

Each execution unit contains one or more functional units that implement specific operations:

*   **ALU Execution Unit**: Contains ALU, multiplier, divider, jump unit, CSR unit
*   **Memory Execution Unit**: Contains memory address calculator
*   **Vector Execution Unit**: Contains vector ALU
*   **Floating Point Execution Unit**: Contains FP ALU, FP multiplier, FP divider

## Reorder Buffer (ROB)

The Reorder Buffer (ROB) is a critical component for out-of-order execution, tracking all in-flight instructions and ensuring they commit in program order:

Sources: [src/main/scala/exu/rob.scala 225-774](https://github.com/tenstorrent/riscv-ocelot/blob/920fd904/src/main/scala/exu/rob.scala#L225-L774)

Key ROB functions:

*   **Allocation**: At dispatch, allocates entries for new uops
*   **Tracking**: Monitors execution status (busy/not busy) of all in-flight uops
*   **Exception Handling**: Tracks and handles exceptions
*   **Commit**: Commits completed instructions in program order
*   **Speculation Control**: Manages branch speculation and recovery

## Register Rename

The register rename stage maps logical registers to physical registers, eliminating false dependencies (WAR and WAW hazards):

Sources: [src/main/scala/exu/core.scala 101-104](https://github.com/tenstorrent/riscv-ocelot/blob/920fd904/src/main/scala/exu/core.scala#L101-L104)[src/main/scala/exu/core.scala 626-684](https://github.com/tenstorrent/riscv-ocelot/blob/920fd904/src/main/scala/exu/core.scala#L626-L684)

BOOM has separate rename stages for:

*   Integer registers
*   Floating-point registers
*   Predicate registers (for SFB optimization)

## Issue Units

Issue units are responsible for scheduling uops for execution when their operands are ready:

Sources: [src/main/scala/exu/core.scala 106-109](https://github.com/tenstorrent/riscv-ocelot/blob/920fd904/src/main/scala/exu/core.scala#L106-L109)[src/main/scala/exu/core.scala 780-832](https://github.com/tenstorrent/riscv-ocelot/blob/920fd904/src/main/scala/exu/core.scala#L780-L832)

BOOM implements different issue units for different types of operations:

*   **Memory Issue Unit**: Schedules load/store operations
*   **Integer Issue Unit**: Schedules integer ALU, multiply, divide operations
*   **Vector Issue Unit** (optional): Schedules vector operations

## Core Parameterization

BOOM is highly parameterizable, allowing for different configurations to meet specific performance, area, and power requirements:

| Parameter | Description | Default/Example |
| --- | --- | --- |
| fetchWidth | Instructions fetched per cycle | 1 |
| decodeWidth | Instructions decoded per cycle | 1 |
| numRobEntries | Reorder buffer size | 64 |
| issueParams | Issue width, queue entries, etc. | {1-mem, 2-int, 1-fp} |
| numLdqEntries | Load queue size | 16 |
| numStqEntries | Store queue size | 16 |
| numIntPhysRegisters | Physical int registers | 96 |
| numFpPhysRegisters | Physical FP registers | 64 |
| maxBrCount | Max speculated branches | 4 |
| enableVector | Vector extension support | false |

Sources: [src/main/scala/common/parameters.scala 25-128](https://github.com/tenstorrent/riscv-ocelot/blob/920fd904/src/main/scala/common/parameters.scala#L25-L128)

These parameters can be adjusted to create cores ranging from small, low-power implementations to large, high-performance ones.

## Vector Extension Support

When configured with `enableVector=true`, BOOM supports the RISC-V Vector (V) extension through:

Sources: [src/main/scala/exu/core.scala 249-295](https://github.com/tenstorrent/riscv-ocelot/blob/920fd904/src/main/scala/exu/core.scala#L249-L295)

The vector implementation allows the processor to efficiently execute data-parallel code.

## Out-of-Order Execution

BOOM implements out-of-order execution to improve performance by executing instructions as soon as their operands are ready, rather than in program order:

Key mechanisms enabling out-of-order execution:

*   **Register Renaming**: Eliminates false dependencies
*   **Issue Queues**: Schedule ready instructions dynamically
*   **Reorder Buffer**: Maintains program order for commits
*   **Branch Prediction**: Allows speculative execution
*   **Load/Store Unit**: Manages memory ordering

Sources: [src/main/scala/exu/core.scala 389-471](https://github.com/tenstorrent/riscv-ocelot/blob/920fd904/src/main/scala/exu/core.scala#L389-L471)

## Conclusion

The BOOM core architecture represents a sophisticated out-of-order, superscalar RISC-V implementation that combines several advanced microarchitectural techniques to achieve high performance while maintaining correct execution. The parameterizable design allows for flexibility in targeting different performance, power, and area requirements.

Dismiss
Refresh this wiki

Enter email to refresh
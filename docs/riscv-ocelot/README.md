---
project: riscv-ocelot
github: https://github.com/tenstorrent/riscv-ocelot
deepwiki: https://deepwiki.com/tenstorrent/riscv-ocelot
---

# Overview

Relevant source files
*   [README.md](https://github.com/tenstorrent/riscv-ocelot/blob/920fd904/README.md?plain=1)
*   [docs/sections/boom-ecosystem.rst](https://github.com/tenstorrent/riscv-ocelot/blob/920fd904/docs/sections/boom-ecosystem.rst)
*   [docs/sections/debugging.rst](https://github.com/tenstorrent/riscv-ocelot/blob/920fd904/docs/sections/debugging.rst)
*   [docs/sections/decode-stage.rst](https://github.com/tenstorrent/riscv-ocelot/blob/920fd904/docs/sections/decode-stage.rst)
*   [docs/sections/execution-stages.rst](https://github.com/tenstorrent/riscv-ocelot/blob/920fd904/docs/sections/execution-stages.rst)
*   [docs/sections/future-work.rst](https://github.com/tenstorrent/riscv-ocelot/blob/920fd904/docs/sections/future-work.rst)
*   [docs/sections/issue-units.rst](https://github.com/tenstorrent/riscv-ocelot/blob/920fd904/docs/sections/issue-units.rst)
*   [docs/sections/load-store-unit.rst](https://github.com/tenstorrent/riscv-ocelot/blob/920fd904/docs/sections/load-store-unit.rst)
*   [docs/sections/memory-system.rst](https://github.com/tenstorrent/riscv-ocelot/blob/920fd904/docs/sections/memory-system.rst)
*   [docs/sections/parameterization.rst](https://github.com/tenstorrent/riscv-ocelot/blob/920fd904/docs/sections/parameterization.rst)
*   [docs/sections/physical-realization.rst](https://github.com/tenstorrent/riscv-ocelot/blob/920fd904/docs/sections/physical-realization.rst)
*   [docs/sections/reg-file-bypass-network.rst](https://github.com/tenstorrent/riscv-ocelot/blob/920fd904/docs/sections/reg-file-bypass-network.rst)
*   [docs/sections/rename-stage.rst](https://github.com/tenstorrent/riscv-ocelot/blob/920fd904/docs/sections/rename-stage.rst)
*   [docs/sections/reorder-buffer.rst](https://github.com/tenstorrent/riscv-ocelot/blob/920fd904/docs/sections/reorder-buffer.rst)
*   [docs/sections/verification.rst](https://github.com/tenstorrent/riscv-ocelot/blob/920fd904/docs/sections/verification.rst)

This page provides a high-level introduction to the RISC-V Ocelot repository, a RISC-V processor implementation that extends the Berkeley Out-of-Order Machine (BOOM) with custom vector processing capabilities. This document explains the architectural structure, key components, and how they interact to form a complete system.

## Purpose and Scope

The RISC-V Ocelot is a synthesizable and parameterizable RV64GC RISC-V core written in the Chisel hardware construction language. It builds upon the BOOM architecture, which is designed for high performance and architecture research, and adds vector processing capabilities through custom extensions.

This overview covers:

*   High-level system architecture
*   Core components and their relationships
*   Pipeline structure and execution flow
*   Memory system organization
*   Vector processing implementation

For detailed information on specific components, please refer to the relevant wiki pages linked in each section.

Sources: [README.md 6-12](https://github.com/tenstorrent/riscv-ocelot/blob/920fd904/README.md?plain=1#L6-L12)[docs/sections/boom-ecosystem.rst 1-9](https://github.com/tenstorrent/riscv-ocelot/blob/920fd904/docs/sections/boom-ecosystem.rst#L1-L9)

## System Architecture

The RISC-V Ocelot implements an out-of-order superscalar processor with the following key features:

*   RV64GC ISA support (64-bit RISC-V with General-purpose, Compressed instructions)
*   Out-of-order execution with register renaming
*   Dynamic branch prediction
*   Non-blocking caches
*   Vector processing extensions
*   Support for virtual memory

The following diagram illustrates the high-level organization of the system:

Sources: [docs/sections/boom-ecosystem.rst 15-22](https://github.com/tenstorrent/riscv-ocelot/blob/920fd904/docs/sections/boom-ecosystem.rst#L15-L22)[docs/sections/execution-stages.rst 1-15](https://github.com/tenstorrent/riscv-ocelot/blob/920fd904/docs/sections/execution-stages.rst#L1-L15)

## Core Components

### BoomCore

The BoomCore is the central component of the processor, managing the out-of-order execution pipeline. It contains and coordinates the following key components:

*   **Reorder Buffer (ROB)**: Tracks in-flight instructions and maintains program order for architectural state updates
*   **Register Rename**: Maps logical (ISA) registers to physical registers to eliminate false dependencies
*   **Issue Queues**: Hold decoded instructions waiting for their operands to become available
*   **Execution Units**: Perform the actual computation

### Front-end

The Front-end is responsible for fetching instructions and feeding them to the Back-end:

*   **Branch Predictor**: Predicts branch outcomes to keep the pipeline full
*   **Instruction Cache**: Stores recently used instructions
*   **Fetch Buffer**: Holds fetched instructions before they are decoded

### Execution Pipeline

The execution pipeline processes instructions through multiple stages:

Sources: [docs/sections/execution-stages.rst 15-65](https://github.com/tenstorrent/riscv-ocelot/blob/920fd904/docs/sections/execution-stages.rst#L15-L65)[docs/sections/reorder-buffer.rst 1-20](https://github.com/tenstorrent/riscv-ocelot/blob/920fd904/docs/sections/reorder-buffer.rst#L1-L20)[docs/sections/rename-stage.rst 1-37](https://github.com/tenstorrent/riscv-ocelot/blob/920fd904/docs/sections/rename-stage.rst#L1-L37)

## Memory System

The memory system consists of separate instruction and data caches, with a Load/Store Unit (LSU) managing memory operations.

Key components:

*   **Load/Store Unit (LSU)**: Handles load and store instructions, maintains memory ordering
*   **Non-Blocking Data Cache**: Allows multiple outstanding memory requests
*   **Miss Status Holding Registers (MSHR)**: Track cache misses while waiting for memory
*   **TileLink Interface**: Standard interface to connect to the memory system

Sources: [docs/sections/load-store-unit.rst 1-25](https://github.com/tenstorrent/riscv-ocelot/blob/920fd904/docs/sections/load-store-unit.rst#L1-L25)[docs/sections/memory-system.rst 1-37](https://github.com/tenstorrent/riscv-ocelot/blob/920fd904/docs/sections/memory-system.rst#L1-L37)

## Vector Processing Extension

The vector processing extension is a key feature of RISC-V Ocelot, enabling efficient data-parallel computation. It's implemented through the OviWrapper interface that connects the scalar BOOM core to the vector processing unit.

Key components:

*   **OviWrapper**: Interface between scalar core and vector unit
*   **tt_vpu_ovi**: Main vector processing unit
*   **tt_scoreboard_ovi**: Tracks vector instruction dependencies
*   **tt_lq**: Load Queue for vector memory operations
*   **tt_store_fsm, tt_mask_fsm**: State machines for vector store and mask operations

Sources: [docs/sections/future-work.rst 17-25](https://github.com/tenstorrent/riscv-ocelot/blob/920fd904/docs/sections/future-work.rst#L17-L25)

## Building and Configuration

RISC-V Ocelot is not a standalone executable but rather a component meant to be integrated into a complete SoC using a system generator like Chipyard. The design is highly parameterizable, allowing for various configurations:

*   Issue width and execution unit mix
*   Register file size and number of physical registers
*   Cache sizes and associativity
*   Branch predictor configuration
*   Pipeline latencies

The processor can be configured through the parameters defined in the configuration system, which allows creating small embedded-like cores or larger high-performance configurations.

Sources: [README.md 32-39](https://github.com/tenstorrent/riscv-ocelot/blob/920fd904/README.md?plain=1#L32-L39)[docs/sections/parameterization.rst 1-32](https://github.com/tenstorrent/riscv-ocelot/blob/920fd904/docs/sections/parameterization.rst#L1-L32)

## Debugging and Verification

The repository supports multiple debugging mechanisms:

*   Software simulation using Verilator and VCS
*   FPGA-based debugging through FireSim
*   Support for "chicken bits" that can simplify processor behavior for debugging
*   Continuous integration testing

For verification, RISC-V Ocelot can be tested using:

*   RISC-V test suite
*   RISC-V Torture tester for randomized stress testing
*   Linux boot tests

Sources: [docs/sections/debugging.rst 1-19](https://github.com/tenstorrent/riscv-ocelot/blob/920fd904/docs/sections/debugging.rst#L1-L19)[docs/sections/verification.rst 1-37](https://github.com/tenstorrent/riscv-ocelot/blob/920fd904/docs/sections/verification.rst#L1-L37)

## Conclusion

RISC-V Ocelot represents a sophisticated out-of-order processor implementation that extends the BOOM architecture with vector processing capabilities. As an open-source project, it provides a platform for researchers and engineers to explore advanced computer architecture concepts in the context of the RISC-V ISA.

For more detailed information on specific components, please refer to the dedicated wiki pages linked throughout this document.

Dismiss
Refresh this wiki

Enter email to refresh
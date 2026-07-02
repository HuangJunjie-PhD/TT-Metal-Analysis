---
project: riscv-ocelot
github: https://github.com/tenstorrent/riscv-ocelot
deepwiki: https://deepwiki.com/tenstorrent/riscv-ocelot/5-vector-processing
---

# Vector Processing

Relevant source files
*   [src/main/resources/vsrc/vpu/tt_briscv_pkg.vh](https://github.com/tenstorrent/riscv-ocelot/blob/920fd904/src/main/resources/vsrc/vpu/tt_briscv_pkg.vh)
*   [src/main/resources/vsrc/vpu/tt_ex.sv](https://github.com/tenstorrent/riscv-ocelot/blob/920fd904/src/main/resources/vsrc/vpu/tt_ex.sv)
*   [src/main/resources/vsrc/vpu/tt_id.sv](https://github.com/tenstorrent/riscv-ocelot/blob/920fd904/src/main/resources/vsrc/vpu/tt_id.sv)
*   [src/main/resources/vsrc/vpu/tt_mem.sv](https://github.com/tenstorrent/riscv-ocelot/blob/920fd904/src/main/resources/vsrc/vpu/tt_mem.sv)
*   [src/main/resources/vsrc/vpu/tt_vec.sv](https://github.com/tenstorrent/riscv-ocelot/blob/920fd904/src/main/resources/vsrc/vpu/tt_vec.sv)
*   [src/main/resources/vsrc/vpu/tt_vpu_ovi.sv](https://github.com/tenstorrent/riscv-ocelot/blob/920fd904/src/main/resources/vsrc/vpu/tt_vpu_ovi.sv)
*   [src/main/scala/exu/ovi.scala](https://github.com/tenstorrent/riscv-ocelot/blob/920fd904/src/main/scala/exu/ovi.scala)
*   [src/main/scala/lsu/lsu.scala](https://github.com/tenstorrent/riscv-ocelot/blob/920fd904/src/main/scala/lsu/lsu.scala)

## Purpose and Scope

This document describes the vector processing capabilities in the RISC-V Ocelot processor architecture. It covers the vector extension implementation, including the interface between the scalar core and vector processing unit, vector instruction execution, and vector memory operations. The document focuses on how the vector processing unit interfaces with the rest of the processor pipeline and how vector instructions are processed through the system.

For detailed information about the OviWrapper interface specifically, see [OviWrapper Interface](https://deepwiki.com/tenstorrent/riscv-ocelot/5.1-oviwrapper-interface). For details on the internal architecture of the vector processing unit, see [Vector Processing Unit](https://deepwiki.com/tenstorrent/riscv-ocelot/5.2-vector-processing-unit). For information about vector memory operations, see [Vector Memory Operations](https://deepwiki.com/tenstorrent/riscv-ocelot/5.3-vector-memory-operations).

## Vector Processing Architecture Overview

The vector processing subsystem in RISC-V Ocelot implements the RISC-V Vector Extension (RVV). The subsystem consists of several key components that work together to execute vector instructions efficiently while maintaining integration with the out-of-order scalar core architecture.

Key components of the vector processing architecture include:

1.   **OviWrapper**: Serves as the interface between the scalar core and the vector processing unit.
2.   **Vector Processing Unit (tt_vpu_ovi)**: The main vector processing engine that executes vector instructions.
3.   **Scoreboard (tt_scoreboard_ovi)**: Tracks vector instruction dependencies and manages execution.
4.   **Memory Operation FSMs**: Handle different types of vector memory operations: 
    *   Load Queue (tt_lq): Manages vector load operations
    *   Store FSM (tt_store_fsm): Manages vector store operations
    *   Mask FSM (tt_mask_fsm): Handles vector mask operations

Sources: [src/main/scala/exu/ovi.scala 26-44](https://github.com/tenstorrent/riscv-ocelot/blob/920fd904/src/main/scala/exu/ovi.scala#L26-L44)[src/main/resources/vsrc/vpu/tt_vpu_ovi.sv 3-49](https://github.com/tenstorrent/riscv-ocelot/blob/920fd904/src/main/resources/vsrc/vpu/tt_vpu_ovi.sv#L3-L49)

## Integration with Core Pipeline

The vector processing unit is integrated into the RISC-V Ocelot pipeline through the OviWrapper, which interfaces with the scalar execution units and the Load/Store Unit (LSU). When the core encounters a vector instruction, it is routed to the vector processing unit via this interface.

Vector instructions flow through the following stages:

1.   Initial scalar pipeline stages (Decode, Rename, Dispatch, Issue)
2.   At Execute stage, vector instructions are sent to the OviWrapper
3.   OviWrapper communicates with the Vector Processing Unit (tt_vpu_ovi)
4.   Within the VPU, instructions go through ID, EX, and MEM stages
5.   For memory operations, the VPU interacts with the LSU to access memory

Sources: [src/main/scala/exu/ovi.scala 45-76](https://github.com/tenstorrent/riscv-ocelot/blob/920fd904/src/main/scala/exu/ovi.scala#L45-L76)[src/main/resources/vsrc/vpu/tt_vpu_ovi.sv 50-100](https://github.com/tenstorrent/riscv-ocelot/blob/920fd904/src/main/resources/vsrc/vpu/tt_vpu_ovi.sv#L50-L100)

## Instruction Processing Path

Vector instructions follow a specific path through the processor, from fetch to completion. This section details how vector instructions are handled within the system.

The instruction processing involves:

1.   **Fetch and Decode**: Vector instructions are fetched and decoded like scalar instructions
2.   **Issue**: Instructions are issued to the OviWrapper from the execution units
3.   **Instruction FIFO**: The OviWrapper buffers instructions in an instruction FIFO
4.   **Vector Processing**: The VPU executes the vector instruction
5.   **Memory Operations**: For vector loads/stores, the VPU interacts with the LSU
6.   **Completion**: Results are written back, and completion is signaled to the scalar core

Sources: [src/main/scala/exu/ovi.scala 144-166](https://github.com/tenstorrent/riscv-ocelot/blob/920fd904/src/main/scala/exu/ovi.scala#L144-L166)[src/main/resources/vsrc/vpu/tt_vpu_ovi.sv 296-328](https://github.com/tenstorrent/riscv-ocelot/blob/920fd904/src/main/resources/vsrc/vpu/tt_vpu_ovi.sv#L296-L328)

## Vector Memory Operations

Vector memory operations (loads and stores) require special handling due to their potential to access large amounts of memory. The Vector Processing Unit handles vector memory operations through dedicated components and interfaces with the Load/Store Unit.

### Memory Operation Types

The vector processing system supports several memory addressing modes:

1.   **Unit-stride**: Contiguous memory accesses
2.   **Strided**: Regular intervals between elements
3.   **Indexed**: Indexed by vector register elements
4.   **Whole-register**: Load/store entire registers
5.   **Mask**: Load/store with mask registers

### Memory Operation Flow

The memory operation flow involves:

1.   **Memory Operation FSM**: Manages the sequence of memory operations
2.   **Vector Address Generator (VAGen)**: Generates memory addresses based on the addressing mode
3.   **Vector Data Buffer (VDB)**: Buffers data for vector store operations
4.   **OviWrapper and VGenIO**: Interface with the core's LSU
5.   **LSU**: Manages the actual memory operations using various queues: 
    *   Load Queue (LDQ) for scalar loads
    *   Store Queue (STQ) for scalar stores
    *   Vector Store Queue (DSQ) for vector stores
    *   Vector Load Queue (DLQ) for vector loads

Sources: [src/main/scala/exu/ovi.scala 76-166](https://github.com/tenstorrent/riscv-ocelot/blob/920fd904/src/main/scala/exu/ovi.scala#L76-L166)[src/main/scala/lsu/lsu.scala 331-370](https://github.com/tenstorrent/riscv-ocelot/blob/920fd904/src/main/scala/lsu/lsu.scala#L331-L370)[src/main/scala/lsu/lsu.scala 633-696](https://github.com/tenstorrent/riscv-ocelot/blob/920fd904/src/main/scala/lsu/lsu.scala#L633-L696)

## Vector Register File

The vector processing unit includes a vector register file that stores vector operands and results. The register file is organized based on the RISC-V Vector Extension specification.

Key features of the vector register file:

1.   **32 Vector Registers (v0-v31)**: Each can hold vector data with configurable element width
2.   **Element Width Selection (SEW)**: Configurable via vtype CSR (8, 16, 32, or 64 bits)
3.   **Register Group Multiplier (LMUL)**: Allows treating multiple registers as a group (1/8, 1/4, 1/2, 1, 2, 4, or 8)
4.   **Vector Length Register (vl)**: Controls the number of elements processed
5.   **Mask Registers**: v0 is often used as a mask register for predicated execution

Sources: [src/main/resources/vsrc/vpu/tt_vec_regfile.sv 585-600](https://github.com/tenstorrent/riscv-ocelot/blob/920fd904/src/main/resources/vsrc/vpu/tt_vec_regfile.sv#L585-L600)[src/main/resources/vsrc/vpu/tt_vpu_ovi.sv 350-386](https://github.com/tenstorrent/riscv-ocelot/blob/920fd904/src/main/resources/vsrc/vpu/tt_vpu_ovi.sv#L350-L386)

## Vector Execution Model

The vector execution model in RISC-V Ocelot follows the RISC-V Vector Extension specification. It supports various vector operations with configurable element widths and register grouping.

### Vector Configuration

The vector unit configuration is controlled through special CSRs (Control and Status Registers):

1.   **vtype**: Controls element width (SEW) and register grouping (LMUL)
2.   **vl**: Sets the vector length (number of elements to process)
3.   **vxrm**: Rounding mode for fixed-point operations
4.   **vxsat**: Fixed-point saturation flag

### Vector Instruction Types

The vector processing unit supports several categories of vector instructions:

1.   **Vector Arithmetic**: Integer and floating-point operations
2.   **Vector Memory**: Load/store operations with various addressing modes
3.   **Vector Reduction**: Operations that reduce vectors to scalar values
4.   **Vector Mask**: Operations that manipulate mask registers
5.   **Vector Permutation**: Operations that reorder vector elements

Sources: [src/main/resources/vsrc/vpu/tt_vpu_ovi.sv 350-386](https://github.com/tenstorrent/riscv-ocelot/blob/920fd904/src/main/resources/vsrc/vpu/tt_vpu_ovi.sv#L350-L386)[src/main/resources/vsrc/vpu/tt_briscv_pkg.vh 62-390](https://github.com/tenstorrent/riscv-ocelot/blob/920fd904/src/main/resources/vsrc/vpu/tt_briscv_pkg.vh#L62-L390)

## Implementation Details

### OviWrapper Interface

The OviWrapper serves as an interface between the scalar core and the vector processing unit. It handles instruction decoding, operand forwarding, and result writeback.

Key features of the OviWrapper:

1.   **Instruction Buffering**: Buffers vector instructions to manage flow control
2.   **Scoreboard Tracking**: Tracks vector instruction dependencies
3.   **Memory Operation Coordination**: Interfaces with the LSU for vector memory operations
4.   **Result Writeback**: Returns results to the scalar core

### Vector Load/Store Interface

The vector load/store interface manages the communication between the vector processing unit and the memory system. It handles address generation, data buffering, and mask operations.

Vector load/store operations involve:

1.   **Address Generation**: Computing memory addresses based on the addressing mode
2.   **Data Buffering**: Buffering data for stores and loads
3.   **Mask Handling**: Processing masked memory operations
4.   **Memory Synchronization**: Coordinating memory operations with the core

Sources: [src/main/scala/exu/ovi.scala 193-474](https://github.com/tenstorrent/riscv-ocelot/blob/920fd904/src/main/scala/exu/ovi.scala#L193-L474)[src/main/scala/lsu/lsu.scala 73-118](https://github.com/tenstorrent/riscv-ocelot/blob/920fd904/src/main/scala/lsu/lsu.scala#L73-L118)

## Vector Instruction Execution

Vector instructions are executed in stages within the vector processing unit. The execution involves several components working together.

The execution flow involves:

1.   **Instruction FIFO**: Buffers vector instructions from the scalar core
2.   **Scoreboard**: Tracks instruction dependencies and issues instructions when ready
3.   **ID Stage**: Decodes vector instructions and reads operands
4.   **EX Stage**: Executes vector operations in specialized units
5.   **MEM Stage**: Handles memory operations and writes back results

Sources: [src/main/resources/vsrc/vpu/tt_id.sv 6-456](https://github.com/tenstorrent/riscv-ocelot/blob/920fd904/src/main/resources/vsrc/vpu/tt_id.sv#L6-L456)[src/main/resources/vsrc/vpu/tt_ex.sv 6-135](https://github.com/tenstorrent/riscv-ocelot/blob/920fd904/src/main/resources/vsrc/vpu/tt_ex.sv#L6-L135)[src/main/resources/vsrc/vpu/tt_mem.sv 14-139](https://github.com/tenstorrent/riscv-ocelot/blob/920fd904/src/main/resources/vsrc/vpu/tt_mem.sv#L14-L139)

## Conclusion

The Vector Processing capabilities in RISC-V Ocelot provide efficient execution of vector instructions as specified by the RISC-V Vector Extension. The integration between the scalar core and vector processing unit allows for a seamless execution of mixed scalar and vector workloads, with specialized handling for vector memory operations and vector execution.

The vector processing implementation includes:

1.   A dedicated Vector Processing Unit (tt_vpu_ovi)
2.   An interface between the scalar core and vector unit (OviWrapper)
3.   Specialized components for vector memory operations
4.   A vector register file with configurable element width and grouping
5.   Support for various vector instruction types

This architecture enables efficient execution of vector workloads while maintaining compatibility with the RISC-V Vector Extension specification.

Dismiss
Refresh this wiki

Enter email to refresh
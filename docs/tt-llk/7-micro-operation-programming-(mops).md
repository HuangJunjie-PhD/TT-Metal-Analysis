---
project: tt-llk
github: https://github.com/tenstorrent/tt-llk
deepwiki: https://deepwiki.com/tenstorrent/tt-llk/7-micro-operation-programming-(mops)
---

# Micro-Operation Programming (MOPs)

Relevant source files
*   [tt_llk_wormhole_b0/common/inc/cmath_common.h](https://github.com/tenstorrent/tt-llk/blob/366f58e2/tt_llk_wormhole_b0/common/inc/cmath_common.h)
*   [tt_llk_wormhole_b0/common/inc/cpack_common.h](https://github.com/tenstorrent/tt-llk/blob/366f58e2/tt_llk_wormhole_b0/common/inc/cpack_common.h)
*   [tt_llk_wormhole_b0/common/inc/cunpack_common.h](https://github.com/tenstorrent/tt-llk/blob/366f58e2/tt_llk_wormhole_b0/common/inc/cunpack_common.h)
*   [tt_llk_wormhole_b0/llk_lib/llk_math_eltwise_unary_datacopy.h](https://github.com/tenstorrent/tt-llk/blob/366f58e2/tt_llk_wormhole_b0/llk_lib/llk_math_eltwise_unary_datacopy.h)
*   [tt_llk_wormhole_b0/llk_lib/llk_math_matmul.h](https://github.com/tenstorrent/tt-llk/blob/366f58e2/tt_llk_wormhole_b0/llk_lib/llk_math_matmul.h)
*   [tt_llk_wormhole_b0/llk_lib/llk_pack.h](https://github.com/tenstorrent/tt-llk/blob/366f58e2/tt_llk_wormhole_b0/llk_lib/llk_pack.h)
*   [tt_llk_wormhole_b0/llk_lib/llk_unpack_A.h](https://github.com/tenstorrent/tt-llk/blob/366f58e2/tt_llk_wormhole_b0/llk_lib/llk_unpack_A.h)
*   [tt_llk_wormhole_b0/llk_lib/llk_unpack_AB.h](https://github.com/tenstorrent/tt-llk/blob/366f58e2/tt_llk_wormhole_b0/llk_lib/llk_unpack_AB.h)
*   [tt_llk_wormhole_b0/llk_lib/llk_unpack_AB_matmul.h](https://github.com/tenstorrent/tt-llk/blob/366f58e2/tt_llk_wormhole_b0/llk_lib/llk_unpack_AB_matmul.h)
*   [tt_llk_wormhole_b0/llk_lib/llk_unpack_reduce.h](https://github.com/tenstorrent/tt-llk/blob/366f58e2/tt_llk_wormhole_b0/llk_lib/llk_unpack_reduce.h)
*   [tt_llk_wormhole_b0/llk_lib/llk_unpack_untilize.h](https://github.com/tenstorrent/tt-llk/blob/366f58e2/tt_llk_wormhole_b0/llk_lib/llk_unpack_untilize.h)

## Purpose and Scope

This document describes the Micro-Operation Programming (MOP) system used throughout the tt-llk codebase to program repeated instruction sequences with nested loop control and automatic address manipulation. MOPs are the fundamental mechanism for efficient hardware operation in the unpacker, math, and packer pipeline stages.

For information about the overall pipeline architecture and synchronization, see [Three-Stage Pipeline Architecture](https://deepwiki.com/tenstorrent/tt-llk/1.1-three-stage-pipeline-architecture). For details on specific pipeline stage operations, see [Unpacker System](https://deepwiki.com/tenstorrent/tt-llk/4-unpacker-system), [Math Unit Operations](https://deepwiki.com/tenstorrent/tt-llk/5-math-unit-operations), and [Packer System](https://deepwiki.com/tenstorrent/tt-llk/6-packer-system).

## MOP System Overview

The MOP system provides a hardware-accelerated mechanism for executing repeated instruction sequences without explicit software loops. Each MOP consists of:

*   **Nested loop structure**: Outer and inner loop counts that control instruction repetition
*   **Instruction sequences**: Start operations, loop operations, and end operations
*   **Address modifiers**: Automatic address counter updates during execution
*   **Replay buffers**: Pre-recorded instruction sequences for efficient execution

**MOP Execution Model**: The MOP hardware executes nested loops with automatic address counter updates based on configured address modifiers. Each instruction can specify which address modifier to apply.

**Sources**: [tt_llk_wormhole_b0/llk_lib/llk_unpack_A.h 28-187](https://github.com/tenstorrent/tt-llk/blob/366f58e2/tt_llk_wormhole_b0/llk_lib/llk_unpack_A.h#L28-L187)[tt_llk_wormhole_b0/llk_lib/llk_pack.h 56-104](https://github.com/tenstorrent/tt-llk/blob/366f58e2/tt_llk_wormhole_b0/llk_lib/llk_pack.h#L56-L104)

## ckernel_template Class

The `ckernel_template` class is the primary interface for constructing MOP programs. It builds nested loop structures and manages instruction sequencing.

**ckernel_template API**: The class provides methods to configure loop operations and generate the final MOP program that is executed via `ckernel_template::run()`.

### Basic Usage Pattern

The typical usage pattern involves:

1.   **Construction**: Create template with loop counts and primary operations
2.   **Configuration**: Optionally set start/end operations
3.   **Programming**: Call `program()` to generate the MOP
4.   **Execution**: Call `ckernel_template::run()` to execute

**Example from Unpacker**:

**Sources**: [tt_llk_wormhole_b0/llk_lib/llk_unpack_A.h 176-185](https://github.com/tenstorrent/tt-llk/blob/366f58e2/tt_llk_wormhole_b0/llk_lib/llk_unpack_A.h#L176-L185)[tt_llk_wormhole_b0/llk_lib/llk_pack.h 75-84](https://github.com/tenstorrent/tt-llk/blob/366f58e2/tt_llk_wormhole_b0/llk_lib/llk_pack.h#L75-L84)

### Advanced Usage with Multiple Operations

For complex operations, `ckernel_template` supports multiple loop operations and nested structures:

**Example from Pack Untilize**:

**Sources**: [tt_llk_wormhole_b0/llk_lib/llk_pack_untilize.h 94-100](https://github.com/tenstorrent/tt-llk/blob/366f58e2/tt_llk_wormhole_b0/llk_lib/llk_pack_untilize.h#L94-L100)[tt_llk_wormhole_b0/llk_lib/llk_math_matmul.h 301-450](https://github.com/tenstorrent/tt-llk/blob/366f58e2/tt_llk_wormhole_b0/llk_lib/llk_math_matmul.h#L301-L450)

## Address Modifiers (ADDR_MOD_0-7)

Address modifiers provide automatic address counter manipulation during MOP execution. Each of the 8 address modifier registers (`ADDR_MOD_0` through `ADDR_MOD_7`) can be configured with increment, clear, and carry-reset operations for multiple address counters.

### Address Modifier Register Structure

Each address modifier controls:

*   **srca**: Source A address counter (increment, clear, carry-reset)
*   **srcb**: Source B address counter (increment, clear, carry-reset)
*   **dest**: Destination address counter (increment, clear, carry-reset)
*   **fidelity**: Fidelity counter for high-precision math (increment, clear)
*   **bias**: Bias counter (increment)

**Address Modifier Structure**: The `addr_mod_t` struct (or `addr_mod_pack_t` for packer) provides a clean C++ interface to configure address modifier registers.

### Common Address Modifier Patterns

| Pattern | ADDR_MOD | Purpose | Example Operations |
| --- | --- | --- | --- |
| **Basic Increment** | ADDR_MOD_0 | Standard row/face progression | `srca.incr=8, dest.incr=8` |
| **Reset with Increment** | ADDR_MOD_1 | Move to next face, reset counters | `srca.incr=16, srcb.cr=1` |
| **Face Transition** | ADDR_MOD_2 | Complete face, move to next | `srca.cr=1, dest.incr=8` |
| **Fidelity Advance** | ADDR_MOD_5 | High-fidelity math iterations | `fidelity.incr=1, all.clr=1` |

**Sources**: [tt_llk_wormhole_b0/llk_lib/llk_math_matmul.h 46-85](https://github.com/tenstorrent/tt-llk/blob/366f58e2/tt_llk_wormhole_b0/llk_lib/llk_math_matmul.h#L46-L85)[tt_llk_wormhole_b0/llk_lib/llk_math_eltwise_binary.h 19-43](https://github.com/tenstorrent/tt-llk/blob/366f58e2/tt_llk_wormhole_b0/llk_lib/llk_math_eltwise_binary.h#L19-L43)

### Address Modifier Configuration Example

**Matmul Address Modifiers**:

**Packer Address Modifiers**:

**Sources**: [tt_llk_wormhole_b0/llk_lib/llk_math_matmul.h 46-85](https://github.com/tenstorrent/tt-llk/blob/366f58e2/tt_llk_wormhole_b0/llk_lib/llk_math_matmul.h#L46-L85)[tt_llk_wormhole_b0/llk_lib/llk_pack.h 22-54](https://github.com/tenstorrent/tt-llk/blob/366f58e2/tt_llk_wormhole_b0/llk_lib/llk_pack.h#L22-L54)

### Operation-Specific Address Modifier Assignments

Different operations use specific address modifier registers for their unique access patterns:

| Operation | ADDR_MOD_6 | ADDR_MOD_7 | Purpose |
| --- | --- | --- | --- |
| **SFPU Typecast** | dest.incr config | - | Destination increment for typecast operations |
| **SFPU TopK** | dest.incr config | - | Special destination addressing for topk |
| **Default SFPU** | - | Standard config | General SFPU operations |

**Sources**: [tt_llk_wormhole_b0/llk_lib/llk_math_eltwise_unary_sfpu_init.h](https://github.com/tenstorrent/tt-llk/blob/366f58e2/tt_llk_wormhole_b0/llk_lib/llk_math_eltwise_unary_sfpu_init.h) (implied from ADDR_MOD usage patterns)

## Replay Buffers and Instruction Recording

The `lltt` namespace provides low-level instruction recording and replay functionality that enables efficient MOP execution through hardware replay buffers.

**Replay Buffer Architecture**: Instructions are recorded into hardware replay buffers which can be efficiently replayed multiple times without re-fetching instructions.

### Replay Buffer Usage Pattern

**Recording Instructions**:

**Using in MOP**:

**Sources**: [tt_llk_wormhole_b0/llk_lib/llk_unpack_A.h 57-63](https://github.com/tenstorrent/tt-llk/blob/366f58e2/tt_llk_wormhole_b0/llk_lib/llk_unpack_A.h#L57-L63)[tt_llk_wormhole_b0/llk_lib/llk_unpack_AB_matmul.h 141-152](https://github.com/tenstorrent/tt-llk/blob/366f58e2/tt_llk_wormhole_b0/llk_lib/llk_unpack_AB_matmul.h#L141-L152)

### Replay Buffer Allocation Strategy

The replay buffer is split between different functional units:

**Buffer Partitioning**: The replay buffer is partitioned to avoid conflicts between SFPU operations (entries 0-15) and FPU/packer operations (entries 16-31).

**Sources**: [tt_llk_wormhole_b0/common/inc/cmath_common.h 40-41](https://github.com/tenstorrent/tt-llk/blob/366f58e2/tt_llk_wormhole_b0/common/inc/cmath_common.h#L40-L41)[tt_llk_wormhole_b0/common/inc/cpack_common.h 30-31](https://github.com/tenstorrent/tt-llk/blob/366f58e2/tt_llk_wormhole_b0/common/inc/cpack_common.h#L30-L31)

## MOP Configuration Examples

### Unpacker MOP Configuration

**Standard Tile Unpack**:

**Broadcast MOP**:

**Sources**: [tt_llk_wormhole_b0/llk_lib/llk_unpack_A.h 131-154](https://github.com/tenstorrent/tt-llk/blob/366f58e2/tt_llk_wormhole_b0/llk_lib/llk_unpack_A.h#L131-L154)[tt_llk_wormhole_b0/llk_lib/llk_unpack_A.h 98-105](https://github.com/tenstorrent/tt-llk/blob/366f58e2/tt_llk_wormhole_b0/llk_lib/llk_unpack_A.h#L98-L105)

### Math MOP Configuration

**Matrix Multiplication MOP**:

**Element-wise Binary MOP**:

**Sources**: [tt_llk_wormhole_b0/llk_lib/llk_math_matmul.h 329-420](https://github.com/tenstorrent/tt-llk/blob/366f58e2/tt_llk_wormhole_b0/llk_lib/llk_math_matmul.h#L329-L420)[tt_llk_wormhole_b0/llk_lib/llk_math_eltwise_binary.h 235-294](https://github.com/tenstorrent/tt-llk/blob/366f58e2/tt_llk_wormhole_b0/llk_lib/llk_math_eltwise_binary.h#L235-L294)

### Packer MOP Configuration

**Standard Tile Pack**:

**Untilize Pack MOP**:

**Sources**: [tt_llk_wormhole_b0/llk_lib/llk_pack.h 72-84](https://github.com/tenstorrent/tt-llk/blob/366f58e2/tt_llk_wormhole_b0/llk_lib/llk_pack.h#L72-L84)[tt_llk_wormhole_b0/llk_lib/llk_pack_untilize.h 94-100](https://github.com/tenstorrent/tt-llk/blob/366f58e2/tt_llk_wormhole_b0/llk_lib/llk_pack_untilize.h#L94-L100)

### Complex MOP with Multiple Operations

**Matmul with Base Address Updates**:

**Sources**: [tt_llk_wormhole_b0/llk_lib/llk_unpack_AB_matmul.h 37-86](https://github.com/tenstorrent/tt-llk/blob/366f58e2/tt_llk_wormhole_b0/llk_lib/llk_unpack_AB_matmul.h#L37-L86)

## MOP Execution Model

### Execution Flow

When `ckernel_template::run()` is called, the hardware executes the programmed MOP according to this flow:

1.   **Execute start operations** (if configured)
2.   **Enter outer loop** (repeat N times) 
    *   **Enter inner loop** (repeat M times) 
        *   **Execute loop operations** with address modifier application

    *   **Exit inner loop**
    *   Execute loop_op0/loop_op1 between inner loop iterations (if configured)

3.   **Exit outer loop**
4.   **Execute end operations** (if configured)

**MOP Execution State Machine**: The hardware progresses through nested loops, applying address modifiers at each loop operation execution.

**Sources**: [tt_llk_wormhole_b0/llk_lib/llk_unpack_A.h 274](https://github.com/tenstorrent/tt-llk/blob/366f58e2/tt_llk_wormhole_b0/llk_lib/llk_unpack_A.h#L274-L274)[tt_llk_wormhole_b0/llk_lib/llk_pack.h 191](https://github.com/tenstorrent/tt-llk/blob/366f58e2/tt_llk_wormhole_b0/llk_lib/llk_pack.h#L191-L191)[tt_llk_wormhole_b0/llk_lib/llk_math_eltwise_unary_datacopy.h 154](https://github.com/tenstorrent/tt-llk/blob/366f58e2/tt_llk_wormhole_b0/llk_lib/llk_math_eltwise_unary_datacopy.h#L154-L154)

### MOP vs Manual Loops

MOPs provide significant advantages over manual software loops:

| Aspect | Manual Loop | MOP |
| --- | --- | --- |
| **Overhead** | Loop control instructions executed | Hardware loop counter, no overhead |
| **Address Updates** | Explicit increment/reset instructions | Automatic via address modifiers |
| **Instruction Fetch** | Each iteration fetches instructions | Replay buffer avoids re-fetch |
| **Synchronization** | Manual stall/wait instructions | Hardware manages operation timing |
| **Code Size** | Large with unrolling | Compact representation |

**Performance Impact**: MOPs reduce cycle count by eliminating loop overhead and enabling hardware-level instruction pipelining.

**Sources**: [tt_llk_wormhole_b0/llk_lib/llk_math_matmul.h](https://github.com/tenstorrent/tt-llk/blob/366f58e2/tt_llk_wormhole_b0/llk_lib/llk_math_matmul.h) (implied from MOP usage patterns), [tt_llk_wormhole_b0/llk_lib/llk_unpack_AB_matmul.h](https://github.com/tenstorrent/tt-llk/blob/366f58e2/tt_llk_wormhole_b0/llk_lib/llk_unpack_AB_matmul.h) (implied from performance-critical paths)

Dismiss
Refresh this wiki

Enter email to refresh
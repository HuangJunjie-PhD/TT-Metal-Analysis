---
project: tt-llk
github: https://github.com/tenstorrent/tt-llk
deepwiki: https://deepwiki.com/tenstorrent/tt-llk/4-unpacker-system
---

# Unpacker System

Relevant source files
*   [tt_llk_blackhole/common/inc/cmath_common.h](https://github.com/tenstorrent/tt-llk/blob/366f58e2/tt_llk_blackhole/common/inc/cmath_common.h)
*   [tt_llk_blackhole/common/inc/cpack_common.h](https://github.com/tenstorrent/tt-llk/blob/366f58e2/tt_llk_blackhole/common/inc/cpack_common.h)
*   [tt_llk_blackhole/common/inc/cunpack_common.h](https://github.com/tenstorrent/tt-llk/blob/366f58e2/tt_llk_blackhole/common/inc/cunpack_common.h)
*   [tt_llk_blackhole/llk_lib/llk_math_common.h](https://github.com/tenstorrent/tt-llk/blob/366f58e2/tt_llk_blackhole/llk_lib/llk_math_common.h)
*   [tt_llk_blackhole/llk_lib/llk_math_eltwise_unary_datacopy.h](https://github.com/tenstorrent/tt-llk/blob/366f58e2/tt_llk_blackhole/llk_lib/llk_math_eltwise_unary_datacopy.h)
*   [tt_llk_blackhole/llk_lib/llk_pack_common.h](https://github.com/tenstorrent/tt-llk/blob/366f58e2/tt_llk_blackhole/llk_lib/llk_pack_common.h)
*   [tt_llk_blackhole/llk_lib/llk_unpack_A.h](https://github.com/tenstorrent/tt-llk/blob/366f58e2/tt_llk_blackhole/llk_lib/llk_unpack_A.h)
*   [tt_llk_blackhole/llk_lib/llk_unpack_common.h](https://github.com/tenstorrent/tt-llk/blob/366f58e2/tt_llk_blackhole/llk_lib/llk_unpack_common.h)
*   [tt_llk_wormhole_b0/common/inc/cmath_common.h](https://github.com/tenstorrent/tt-llk/blob/366f58e2/tt_llk_wormhole_b0/common/inc/cmath_common.h)
*   [tt_llk_wormhole_b0/common/inc/cpack_common.h](https://github.com/tenstorrent/tt-llk/blob/366f58e2/tt_llk_wormhole_b0/common/inc/cpack_common.h)
*   [tt_llk_wormhole_b0/common/inc/cunpack_common.h](https://github.com/tenstorrent/tt-llk/blob/366f58e2/tt_llk_wormhole_b0/common/inc/cunpack_common.h)
*   [tt_llk_wormhole_b0/llk_lib/llk_math_common.h](https://github.com/tenstorrent/tt-llk/blob/366f58e2/tt_llk_wormhole_b0/llk_lib/llk_math_common.h)
*   [tt_llk_wormhole_b0/llk_lib/llk_math_eltwise_unary_datacopy.h](https://github.com/tenstorrent/tt-llk/blob/366f58e2/tt_llk_wormhole_b0/llk_lib/llk_math_eltwise_unary_datacopy.h)
*   [tt_llk_wormhole_b0/llk_lib/llk_math_matmul.h](https://github.com/tenstorrent/tt-llk/blob/366f58e2/tt_llk_wormhole_b0/llk_lib/llk_math_matmul.h)
*   [tt_llk_wormhole_b0/llk_lib/llk_pack.h](https://github.com/tenstorrent/tt-llk/blob/366f58e2/tt_llk_wormhole_b0/llk_lib/llk_pack.h)
*   [tt_llk_wormhole_b0/llk_lib/llk_pack_common.h](https://github.com/tenstorrent/tt-llk/blob/366f58e2/tt_llk_wormhole_b0/llk_lib/llk_pack_common.h)
*   [tt_llk_wormhole_b0/llk_lib/llk_unpack_A.h](https://github.com/tenstorrent/tt-llk/blob/366f58e2/tt_llk_wormhole_b0/llk_lib/llk_unpack_A.h)
*   [tt_llk_wormhole_b0/llk_lib/llk_unpack_AB.h](https://github.com/tenstorrent/tt-llk/blob/366f58e2/tt_llk_wormhole_b0/llk_lib/llk_unpack_AB.h)
*   [tt_llk_wormhole_b0/llk_lib/llk_unpack_AB_matmul.h](https://github.com/tenstorrent/tt-llk/blob/366f58e2/tt_llk_wormhole_b0/llk_lib/llk_unpack_AB_matmul.h)
*   [tt_llk_wormhole_b0/llk_lib/llk_unpack_common.h](https://github.com/tenstorrent/tt-llk/blob/366f58e2/tt_llk_wormhole_b0/llk_lib/llk_unpack_common.h)
*   [tt_llk_wormhole_b0/llk_lib/llk_unpack_reduce.h](https://github.com/tenstorrent/tt-llk/blob/366f58e2/tt_llk_wormhole_b0/llk_lib/llk_unpack_reduce.h)
*   [tt_llk_wormhole_b0/llk_lib/llk_unpack_untilize.h](https://github.com/tenstorrent/tt-llk/blob/366f58e2/tt_llk_wormhole_b0/llk_lib/llk_unpack_untilize.h)

The unpacker system is responsible for reading data from L1 memory and loading it into source registers (SrcA and SrcB) for consumption by the math unit. It represents the first stage of the three-stage Unpack-Math-Pack pipeline that processes data through Tensix cores.

For information about the math unit that consumes unpacker output, see [Math Unit Operations](https://deepwiki.com/tenstorrent/tt-llk/5-math-unit-operations). For the packer that writes results back to L1, see [Packer System](https://deepwiki.com/tenstorrent/tt-llk/6-packer-system). For overall pipeline architecture, see [Three-Stage Pipeline Architecture](https://deepwiki.com/tenstorrent/tt-llk/1.1-three-stage-pipeline-architecture).

## Hardware Architecture

The unpacker system consists of two independent unpacker units that can operate concurrently to feed different source operands to the math unit.

**Hardware Units**

| Component | Count | Register Base | Purpose |
| --- | --- | --- | --- |
| Unpacker Units | 2 | `THCON_SEC0_*`, `THCON_SEC1_*` | Independent unpack engines |
| Configuration Contexts | 2 per unpacker | Context 0/1 via `unp_cfg_context` | Ping-pong double buffering |
| Source Registers | 2 | SrcA, SrcB | Hold unpacked data for math |
| Address Channels | 2 per unpacker | CH_0, CH_1 | Source and destination addressing |

Sources: [tt_llk_wormhole_b0/common/inc/cunpack_common.h 17-19](https://github.com/tenstorrent/tt-llk/blob/366f58e2/tt_llk_wormhole_b0/common/inc/cunpack_common.h#L17-L19)[tt_llk_wormhole_b0/common/inc/ckernel_defs.h](https://github.com/tenstorrent/tt-llk/blob/366f58e2/tt_llk_wormhole_b0/common/inc/ckernel_defs.h)

### Context Management

The unpacker uses a ping-pong context system to enable double-buffering: while one context's data is being consumed by math, the other context can be programmed for the next tile.

**Context Switching Functions**

| Function | Location | Purpose |
| --- | --- | --- |
| `wait_for_next_context(2)` | [cunpack_common.h 164-169](https://github.com/tenstorrent/tt-llk/blob/366f58e2/cunpack_common.h#L164-L169) | Wait for context availability |
| `switch_config_context()` | [cunpack_common.h 171-183](https://github.com/tenstorrent/tt-llk/blob/366f58e2/cunpack_common.h#L171-L183) | Toggle between contexts 0 and 1 |
| `reset_config_context()` | [cunpack_common.h 185-190](https://github.com/tenstorrent/tt-llk/blob/366f58e2/cunpack_common.h#L185-L190) | Reset to context 0 |
| `unpacker_iteration_cleanup()` | [cunpack_common.h 124-137](https://github.com/tenstorrent/tt-llk/blob/366f58e2/cunpack_common.h#L124-L137) | Switch context and update offset |

Sources: [tt_llk_wormhole_b0/common/inc/cunpack_common.h 124-190](https://github.com/tenstorrent/tt-llk/blob/366f58e2/tt_llk_wormhole_b0/common/inc/cunpack_common.h#L124-L190)

## Configuration Structures

The unpacker is configured through three main structures that define how data is read, transformed, and written to source registers.

### Tile Descriptor Structure

**Key Fields**

| Field | Bits | Values | Description |
| --- | --- | --- | --- |
| `in_data_format` | 4 | DataFormat enum | Input data format from L1 |
| `uncompressed` | 1 | 0 or 1 | Whether data is compressed |
| `x_dim` | 16 | Pixels | Face width (typically 16×C_DIM) |
| `y_dim` | 16 | Rows | Rows per face (1, 2, 4, 8, 16) |
| `z_dim` | 16 | Faces | Number of faces (1, 2, 4) |
| `w_dim` | 16 | Tiles | Batch/tile dimension |

Sources: [tt_llk_wormhole_b0/common/inc/cunpack_common.h 22-49](https://github.com/tenstorrent/tt-llk/blob/366f58e2/tt_llk_wormhole_b0/common/inc/cunpack_common.h#L22-L49)

### Unpacker Configuration Structure

The `unpack_config_t` structure controls output format, throttling, and unpacker behavior.

| Field | Type | Purpose |
| --- | --- | --- |
| `out_data_format` | 4 bits | Output format to SrcA/SrcB registers |
| `throttle_mode` | 2 bits | Flow control mode (typically 2) |
| `context_count` | 2 bits | Number of active contexts |
| `haloize_mode` | 1 bit | Enable XY transpose within faces |
| `tileize_mode` | 1 bit | Enable tilize mode |
| `uncompress_cntx0_3` | 4 bits | Per-context compression flags |
| `shift_amount` | 16 bits | Bit shift for data alignment |

Sources: [tt_llk_wormhole_b0/common/inc/cunpack_common.h 52-88](https://github.com/tenstorrent/tt-llk/blob/366f58e2/tt_llk_wormhole_b0/common/inc/cunpack_common.h#L52-L88)

### ALU Configuration Structure

The `alu_config_t` structure configures data format interpretation and arithmetic modes.

Sources: [tt_llk_wormhole_b0/common/inc/cunpack_common.h 91-115](https://github.com/tenstorrent/tt-llk/blob/366f58e2/tt_llk_wormhole_b0/common/inc/cunpack_common.h#L91-L115)

## Core Unpacker Operations

### Basic Unpacking Flow

The typical unpacking sequence involves initialization, configuration, and per-tile unpacking.

**Core API Functions**

| Function | File | Purpose |
| --- | --- | --- |
| `_llk_unpack_hw_configure_<>()` | [llk_unpack_common.h 44-63](https://github.com/tenstorrent/tt-llk/blob/366f58e2/llk_unpack_common.h#L44-L63) | Hardware-level configuration |
| `_llk_unpack_A_init_<>()` | [llk_unpack_A.h 196-232](https://github.com/tenstorrent/tt-llk/blob/366f58e2/llk_unpack_A.h#L196-L232) | Initialize unpacker A with MOP |
| `_llk_unpack_A_()` | [llk_unpack_A.h 239-294](https://github.com/tenstorrent/tt-llk/blob/366f58e2/llk_unpack_A.h#L239-L294) | Unpack single tile to SrcA/B |
| `_llk_unpack_A_uninit_()` | [llk_unpack_A.h 296-301](https://github.com/tenstorrent/tt-llk/blob/366f58e2/llk_unpack_A.h#L296-L301) | Restore default settings |

Sources: [tt_llk_wormhole_b0/llk_lib/llk_unpack_A.h 28-301](https://github.com/tenstorrent/tt-llk/blob/366f58e2/tt_llk_wormhole_b0/llk_lib/llk_unpack_A.h#L28-L301)[tt_llk_wormhole_b0/llk_lib/llk_unpack_common.h 44-63](https://github.com/tenstorrent/tt-llk/blob/366f58e2/tt_llk_wormhole_b0/llk_lib/llk_unpack_common.h#L44-L63)

### Unpacker A Configuration

The `_llk_unpack_A_init_()` function configures unpacker for single-operand operations, supporting various broadcast modes and transpose options.

**Template Parameters**

| Parameter | Type | Default | Purpose |
| --- | --- | --- | --- |
| `BType` | `BroadcastType` | `NONE` | Broadcast mode (NONE/COL/ROW/SCALAR) |
| `acc_to_dest` | `bool` | `false` | Accumulate into destination |
| `binary_reuse_dest` | `EltwiseBinaryReuseDestType` | `NONE` | Reuse destination as operand |
| `unpack_to_dest` | `bool` | `false` | Unpack directly to dest (32-bit) |

**Broadcast Modes**

Sources: [tt_llk_wormhole_b0/llk_lib/llk_unpack_A.h 196-232](https://github.com/tenstorrent/tt-llk/blob/366f58e2/tt_llk_wormhole_b0/llk_lib/llk_unpack_A.h#L196-L232)[tt_llk_wormhole_b0/common/inc/ckernel_defs.h](https://github.com/tenstorrent/tt-llk/blob/366f58e2/tt_llk_wormhole_b0/common/inc/ckernel_defs.h)

### Unpacker AB for Binary Operations

The `configure_unpack_AB()` function configures both unpackers simultaneously for binary operations.

**Key Configuration Steps**

1.   **Address Counter Initialization**: Reset all XY/ZW counters to zero
2.   **Stride Configuration**: Set CH1 Z-stride based on face dimensions and output format
3.   **ALU Format Setup**: Configure unsigned flags and format specifications
4.   **Tile Descriptor Programming**: Set input format, dimensions, face count
5.   **Unpacker Config**: Set output format, throttle mode, haloize (transpose) mode
6.   **Address Range**: Set X-dimension end values for each unpacker
7.   **Context Reset**: Initialize to context 0 for first tile

Sources: [tt_llk_wormhole_b0/common/inc/cunpack_common.h 208-377](https://github.com/tenstorrent/tt-llk/blob/366f58e2/tt_llk_wormhole_b0/common/inc/cunpack_common.h#L208-L377)

## Broadcast Operations

Broadcast modes enable efficient reuse of operands by replicating data from one dimension across another.

### Row Broadcast Implementation

In row broadcast mode, the first row of the source operand is broadcast across all faces in the column direction.

**Row Broadcast Code Flow**

Sources: [tt_llk_wormhole_b0/llk_lib/llk_unpack_A.h 107-118](https://github.com/tenstorrent/tt-llk/blob/366f58e2/tt_llk_wormhole_b0/llk_lib/llk_unpack_A.h#L107-L118)

### Column Broadcast Implementation

Column broadcast replicates the first column across all faces in the row direction.

Sources: [tt_llk_wormhole_b0/llk_lib/llk_unpack_A.h 97-106](https://github.com/tenstorrent/tt-llk/blob/366f58e2/tt_llk_wormhole_b0/llk_lib/llk_unpack_A.h#L97-L106)

### Scalar Broadcast Implementation

Scalar broadcast replicates a single value across the entire tile.

| Mode | Z Increment | Loop Count | Description |
| --- | --- | --- | --- |
| `SCALAR` | 0 | `num_faces` iterations | No Z increment, read same value |
| Start Op | `unpack_srca_zerosrc_set_dvalid` |  | Zero SrcA, validate SrcB |
| Inner Loop | `unpack_srcb_inc_z_0` |  | Read SrcB without advancing |

Sources: [tt_llk_wormhole_b0/llk_lib/llk_unpack_A.h 119-128](https://github.com/tenstorrent/tt-llk/blob/366f58e2/tt_llk_wormhole_b0/llk_lib/llk_unpack_A.h#L119-L128)

## Specialized Unpacker Operations

### Tilize and Untilize

Tilize converts row-major data to tiled format; untilize performs the reverse transformation.

**Untilize MOP Programming**

The untilize MOP uses a 5-instruction replay buffer to coordinate face-by-face unpacking:

| Step | Instruction | Purpose |
| --- | --- | --- |
| 1 | `TTI_DMANOP` | Wait for previous offset write |
| 2 | `TTI_UNPACR(0b01000001)` | Unpack with CH0/CH1 Z increment |
| 3 | `TTI_UNPACR(0b01000001)` | Continue unpacking |
| 4 | `TTI_SETADCZW` | Clear CH0/CH1 Z counters |
| 5 | Loop | Repeat for all faces |

Sources: [tt_llk_wormhole_b0/llk_lib/llk_unpack_untilize.h 23-75](https://github.com/tenstorrent/tt-llk/blob/366f58e2/tt_llk_wormhole_b0/llk_lib/llk_unpack_untilize.h#L23-L75)

### Matrix Multiplication Unpacking

Matmul unpacking supports operand reuse patterns where either input A or input B is reused across multiple output tiles.

**Kernel Broadcast Support**

For matmul operations that broadcast inputs, the unpacker supports `kernel_broadcast_a` and `kernel_broadcast_b` parameters:

| Parameter | Value | Behavior |
| --- | --- | --- |
| `kernel_broadcast_a = N` | > 0 | Input A addresses wrap modulo N |
| `kernel_broadcast_b = N` | > 0 | Input B addresses wrap modulo N |
| Broadcast disabled | 0 | Normal sequential addressing |

**Partial Face Support**

When `unpA_partial_face` or `unpB_partial_face` is true:

*   Uses `0b00010001` Z increment pattern (CH0/CH1 both increment)
*   Requires `TTI_SETADCZW` to clear counters after each face
*   Programs `TTI_UNPACR_NOP(UNP_ZEROSRC)` to initialize

Sources: [tt_llk_wormhole_b0/llk_lib/llk_unpack_AB_matmul.h 24-153](https://github.com/tenstorrent/tt-llk/blob/366f58e2/tt_llk_wormhole_b0/llk_lib/llk_unpack_AB_matmul.h#L24-L153)[tt_llk_wormhole_b0/llk_lib/llk_unpack_AB_matmul.h 217-307](https://github.com/tenstorrent/tt-llk/blob/366f58e2/tt_llk_wormhole_b0/llk_lib/llk_unpack_AB_matmul.h#L217-L307)

### Reduce Operations

Reduce unpacking supports aggregation operations across rows or columns.

**Reduce Row Configuration**

For row reduction (aggregating across columns):

*   Unpacker configuration: `row_pool = true` in `configure_unpack_AB()`
*   Output format: Forces `DataFormat::Float32` or `DataFormat::Float16` based on exp_width
*   Unpacker B format: Overridden to Float32 for accumulation
*   MOP pattern: Sequential face unpacking with standard Z increment

**Reduce Column/Scalar Configuration**

For column or scalar reduction:

*   Uses standard unpacker configuration
*   Relies on math unit to perform reduction logic
*   MOP uses replay buffer for efficient face iteration

Sources: [tt_llk_wormhole_b0/llk_lib/llk_unpack_reduce.h 21-99](https://github.com/tenstorrent/tt-llk/blob/366f58e2/tt_llk_wormhole_b0/llk_lib/llk_unpack_reduce.h#L21-L99)

## Unpack-to-Dest for 32-bit Formats

For 32-bit input formats (Float32, Int32), the unpacker can bypass source registers and write directly to destination registers.

**Unpack-to-Dest Configuration**

| Aspect | Standard | Unpack-to-Dest |
| --- | --- | --- |
| Trigger | 16-bit formats | `is_32bit_input()` returns true |
| Unpacker Mode | Normal SrcA/B write | `unpack_if_sel` set to dest |
| Address Setup | Standard CH1 | `set_dst_write_addr()` |
| Math Sync | Normal | `wait_for_dest_available()` |
| Z Increment | Face-based | 0b00010001 (CH0/CH1) |
| Cleanup | Standard | `unpack_to_dest_tile_done()` |

**Broadcast with Unpack-to-Dest**

For 32-bit broadcast operations, data flows through destination first, then uses `MOVD2B` and `MOVB2D` instructions:

1.   **Row Broadcast**: Unpack row → D2B → B2D with 8-row broadcast mode
2.   **Column Broadcast**: Unpack column → D2B → B2D with D0_BRCST mode
3.   **Scalar Broadcast**: Unpack single → D2B → B2D with D0_BRCST to all rows

Sources: [tt_llk_wormhole_b0/common/inc/cunpack_common.h 406-440](https://github.com/tenstorrent/tt-llk/blob/366f58e2/tt_llk_wormhole_b0/common/inc/cunpack_common.h#L406-L440)[tt_llk_wormhole_b0/llk_lib/llk_math_eltwise_unary_datacopy.h 22-147](https://github.com/tenstorrent/tt-llk/blob/366f58e2/tt_llk_wormhole_b0/llk_lib/llk_math_eltwise_unary_datacopy.h#L22-L147)

## MOP Programming and Address Generation

### Micro-Operation Programs (MOPs)

MOPs are sequences of unpacker instructions programmed into replay buffers for efficient execution.

**Common MOP Patterns**

| Pattern | Outer Loop | Inner Loop | Operations |
| --- | --- | --- | --- |
| Full Tile | `num_faces` | 1 | `unpack_srca` with Z increment |
| Transpose | `num_faces/2` | 2 | Modified Z increment (2 or 1) |
| Row Broadcast | 1 | 1 | `unpack_srcb` repeated, Z clear |
| Column Broadcast | 1 | 1 | `unpack_srcb`, Z set to 2 |
| Scalar Broadcast | 1 | `num_faces` | `unpack_srcb` no Z increment |

Sources: [tt_llk_wormhole_b0/llk_lib/llk_unpack_A.h 28-189](https://github.com/tenstorrent/tt-llk/blob/366f58e2/tt_llk_wormhole_b0/llk_lib/llk_unpack_A.h#L28-L189)

### Address Counter Configuration

Unpacker address counters (X, Y, Z, W) automatically increment based on configured strides.

**Address Modifier Registers**

| Register | Field | Purpose |
| --- | --- | --- |
| `UNP0_ADDR_CTRL_ZW_REG_1` | `Zstride` | Bytes to increment per face (typically `face_r_dim * 16 * datum_size`) |
| `UNP0_ADDR_CTRL_ZW_REG_1` | `Wstride` | Bytes per tile (not commonly used) |
| `THCON_SEC0_REG5` | `Dest_cntx0_address` | Base address for channel 1 destination |
| `THCON_SEC0_REG5` | `Tile_x_dim_cntx0` | Override X dimension per context |

Sources: [tt_llk_wormhole_b0/common/inc/cunpack_common.h 118-122](https://github.com/tenstorrent/tt-llk/blob/366f58e2/tt_llk_wormhole_b0/common/inc/cunpack_common.h#L118-L122)[tt_llk_wormhole_b0/common/inc/cunpack_common.h 230-247](https://github.com/tenstorrent/tt-llk/blob/366f58e2/tt_llk_wormhole_b0/common/inc/cunpack_common.h#L230-L247)

## Synchronization

### Semaphore-Based Flow Control

The unpacker uses semaphores to coordinate with TRISC processors and the math unit.

**Semaphore Operations**

| Function | Location | Effect | Use Case |
| --- | --- | --- | --- |
| `wait_for_next_context(N)` | [cunpack_common.h 164-169](https://github.com/tenstorrent/tt-llk/blob/366f58e2/cunpack_common.h#L164-L169) | Poll until semaphore < N | Wait for context availability |
| `semaphore_post()` | [ckernel.h](https://github.com/tenstorrent/tt-llk/blob/366f58e2/ckernel.h) | Increment semaphore | Acquire context |
| `t6_semaphore_get()` | [ckernel.h](https://github.com/tenstorrent/tt-llk/blob/366f58e2/ckernel.h) | Decrement semaphore | Release context |
| `wait_for_idle()` | [cunpack_common.h 193-198](https://github.com/tenstorrent/tt-llk/blob/366f58e2/cunpack_common.h#L193-L198) | Wait until semaphore == 0 | Ensure all unpacking done |

Sources: [tt_llk_wormhole_b0/common/inc/cunpack_common.h 164-198](https://github.com/tenstorrent/tt-llk/blob/366f58e2/tt_llk_wormhole_b0/common/inc/cunpack_common.h#L164-L198)[tt_llk_wormhole_b0/llk_lib/llk_unpack_A.h 248-282](https://github.com/tenstorrent/tt-llk/blob/366f58e2/tt_llk_wormhole_b0/llk_lib/llk_unpack_A.h#L248-L282)

### Stall and Wait Instructions

Hardware stall instructions ensure proper ordering of operations.

| Instruction | Purpose | Typical Usage |
| --- | --- | --- |
| `TTI_STALLWAIT(STALL_UNPACK, TRISC_CFG)` | Wait for config writes | After programming registers |
| `TTI_STALLWAIT(STALL_MATH, SRCA_VLD)` | Wait for SrcA valid | Before `MOVD2A` operations |
| `TTI_STALLWAIT(STALL_MATH, SRCB_VLD)` | Wait for SrcB valid | Before `MOVD2B` operations |
| `TTI_STALLWAIT(STALL_CFG, UNPACK0)` | Wait for unpacker 0 idle | Before reconfiguring format |

Sources: [tt_llk_wormhole_b0/common/inc/cmath_common.h 44-59](https://github.com/tenstorrent/tt-llk/blob/366f58e2/tt_llk_wormhole_b0/common/inc/cmath_common.h#L44-L59)[tt_llk_wormhole_b0/llk_lib/llk_unpack_common.h 89](https://github.com/tenstorrent/tt-llk/blob/366f58e2/tt_llk_wormhole_b0/llk_lib/llk_unpack_common.h#L89-L89)

## Architecture-Specific Differences

### Wormhole B0 vs Blackhole

While the unpacker architecture is largely consistent, there are key differences:

| Aspect | Wormhole B0 | Blackhole |
| --- | --- | --- |
| Packer Count | 4 packers | 1 packer |
| Register Layout | Standard | Modified `THCON_SEC*_REG*` offsets |
| Instruction Set | Base TTI | Extended TTI with gathering |
| Format Support | Standard formats | Added Lf8, 4-bit exponent modes |
| TRISC Tracking | Basic | Enhanced `trisc_l1_sync_semaphore` |

Sources: [tt_llk_blackhole/common/inc/cunpack_common.h](https://github.com/tenstorrent/tt-llk/blob/366f58e2/tt_llk_blackhole/common/inc/cunpack_common.h)[tt_llk_wormhole_b0/common/inc/cunpack_common.h](https://github.com/tenstorrent/tt-llk/blob/366f58e2/tt_llk_wormhole_b0/common/inc/cunpack_common.h)

### Configuration Validation

Both architectures include configuration validation helpers:

These functions read back hardware configuration registers to verify correct programming:

*   Tile descriptor format fields
*   Unpacker output formats
*   Face dimensions and counts
*   Context-specific settings

Sources: [tt_llk_wormhole_b0/common/inc/cunpack_common.h 541-646](https://github.com/tenstorrent/tt-llk/blob/366f58e2/tt_llk_wormhole_b0/common/inc/cunpack_common.h#L541-L646)

Dismiss
Refresh this wiki

Enter email to refresh
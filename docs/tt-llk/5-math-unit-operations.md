---
project: tt-llk
github: https://github.com/tenstorrent/tt-llk
deepwiki: https://deepwiki.com/tenstorrent/tt-llk/5-math-unit-operations
---

# Math Unit Operations

Relevant source files
*   [tt_llk_blackhole/common/inc/ckernel_sfpu.h](https://github.com/tenstorrent/tt-llk/blob/366f58e2/tt_llk_blackhole/common/inc/ckernel_sfpu.h)
*   [tt_llk_blackhole/common/inc/sfpu/ckernel_sfpu_reshuffle_rows.h](https://github.com/tenstorrent/tt-llk/blob/366f58e2/tt_llk_blackhole/common/inc/sfpu/ckernel_sfpu_reshuffle_rows.h)
*   [tt_llk_blackhole/llk_lib/llk_math_eltwise_binary.h](https://github.com/tenstorrent/tt-llk/blob/366f58e2/tt_llk_blackhole/llk_lib/llk_math_eltwise_binary.h)
*   [tt_llk_blackhole/llk_lib/llk_math_matmul.h](https://github.com/tenstorrent/tt-llk/blob/366f58e2/tt_llk_blackhole/llk_lib/llk_math_matmul.h)
*   [tt_llk_blackhole/llk_lib/llk_math_reduce.h](https://github.com/tenstorrent/tt-llk/blob/366f58e2/tt_llk_blackhole/llk_lib/llk_math_reduce.h)
*   [tt_llk_blackhole/llk_lib/llk_unpack_AB.h](https://github.com/tenstorrent/tt-llk/blob/366f58e2/tt_llk_blackhole/llk_lib/llk_unpack_AB.h)
*   [tt_llk_blackhole/llk_lib/llk_unpack_AB_matmul.h](https://github.com/tenstorrent/tt-llk/blob/366f58e2/tt_llk_blackhole/llk_lib/llk_unpack_AB_matmul.h)
*   [tt_llk_wormhole_b0/common/inc/ckernel_sfpu.h](https://github.com/tenstorrent/tt-llk/blob/366f58e2/tt_llk_wormhole_b0/common/inc/ckernel_sfpu.h)
*   [tt_llk_wormhole_b0/common/inc/cmath_common.h](https://github.com/tenstorrent/tt-llk/blob/366f58e2/tt_llk_wormhole_b0/common/inc/cmath_common.h)
*   [tt_llk_wormhole_b0/common/inc/cpack_common.h](https://github.com/tenstorrent/tt-llk/blob/366f58e2/tt_llk_wormhole_b0/common/inc/cpack_common.h)
*   [tt_llk_wormhole_b0/common/inc/cunpack_common.h](https://github.com/tenstorrent/tt-llk/blob/366f58e2/tt_llk_wormhole_b0/common/inc/cunpack_common.h)
*   [tt_llk_wormhole_b0/common/inc/sfpu/ckernel_sfpu_reshuffle_rows.h](https://github.com/tenstorrent/tt-llk/blob/366f58e2/tt_llk_wormhole_b0/common/inc/sfpu/ckernel_sfpu_reshuffle_rows.h)
*   [tt_llk_wormhole_b0/llk_lib/llk_defs.h](https://github.com/tenstorrent/tt-llk/blob/366f58e2/tt_llk_wormhole_b0/llk_lib/llk_defs.h)
*   [tt_llk_wormhole_b0/llk_lib/llk_math_eltwise_binary.h](https://github.com/tenstorrent/tt-llk/blob/366f58e2/tt_llk_wormhole_b0/llk_lib/llk_math_eltwise_binary.h)
*   [tt_llk_wormhole_b0/llk_lib/llk_math_eltwise_binary_sfpu.h](https://github.com/tenstorrent/tt-llk/blob/366f58e2/tt_llk_wormhole_b0/llk_lib/llk_math_eltwise_binary_sfpu.h)
*   [tt_llk_wormhole_b0/llk_lib/llk_math_eltwise_unary_datacopy.h](https://github.com/tenstorrent/tt-llk/blob/366f58e2/tt_llk_wormhole_b0/llk_lib/llk_math_eltwise_unary_datacopy.h)
*   [tt_llk_wormhole_b0/llk_lib/llk_math_eltwise_unary_sfpu.h](https://github.com/tenstorrent/tt-llk/blob/366f58e2/tt_llk_wormhole_b0/llk_lib/llk_math_eltwise_unary_sfpu.h)
*   [tt_llk_wormhole_b0/llk_lib/llk_math_matmul.h](https://github.com/tenstorrent/tt-llk/blob/366f58e2/tt_llk_wormhole_b0/llk_lib/llk_math_matmul.h)
*   [tt_llk_wormhole_b0/llk_lib/llk_math_reduce.h](https://github.com/tenstorrent/tt-llk/blob/366f58e2/tt_llk_wormhole_b0/llk_lib/llk_math_reduce.h)
*   [tt_llk_wormhole_b0/llk_lib/llk_pack.h](https://github.com/tenstorrent/tt-llk/blob/366f58e2/tt_llk_wormhole_b0/llk_lib/llk_pack.h)
*   [tt_llk_wormhole_b0/llk_lib/llk_unpack_A.h](https://github.com/tenstorrent/tt-llk/blob/366f58e2/tt_llk_wormhole_b0/llk_lib/llk_unpack_A.h)
*   [tt_llk_wormhole_b0/llk_lib/llk_unpack_AB.h](https://github.com/tenstorrent/tt-llk/blob/366f58e2/tt_llk_wormhole_b0/llk_lib/llk_unpack_AB.h)
*   [tt_llk_wormhole_b0/llk_lib/llk_unpack_AB_matmul.h](https://github.com/tenstorrent/tt-llk/blob/366f58e2/tt_llk_wormhole_b0/llk_lib/llk_unpack_AB_matmul.h)
*   [tt_llk_wormhole_b0/llk_lib/llk_unpack_reduce.h](https://github.com/tenstorrent/tt-llk/blob/366f58e2/tt_llk_wormhole_b0/llk_lib/llk_unpack_reduce.h)
*   [tt_llk_wormhole_b0/llk_lib/llk_unpack_untilize.h](https://github.com/tenstorrent/tt-llk/blob/366f58e2/tt_llk_wormhole_b0/llk_lib/llk_unpack_untilize.h)

## Purpose and Scope

This document introduces the **math unit subsystem** of the LLK (Low-Level Kernel) library, which performs all computational operations in the three-stage pipeline. The math unit operates on data loaded into source registers by the unpacker and writes results to destination registers, which are subsequently packed to L1 memory by the packer.

This page provides an overview of the math unit architecture, operation categories, and programming model. For detailed information on specific topics, see:

*   Core configuration: [Core Math Unit Configuration](https://deepwiki.com/tenstorrent/tt-llk/5.1-core-math-unit-configuration)
*   Element-wise operations: [Element-wise Operations](https://deepwiki.com/tenstorrent/tt-llk/5.2-element-wise-operations)
*   Special functions: [SFPU Operations Library](https://deepwiki.com/tenstorrent/tt-llk/5.3-sfpu-operations-library)
*   Matrix operations: [Matrix Multiplication Operations](https://deepwiki.com/tenstorrent/tt-llk/5.4-matrix-multiplication-operations)
*   Reductions: [Reduction and Pooling Operations](https://deepwiki.com/tenstorrent/tt-llk/5.5-reduction-and-pooling-operations)
*   Precision control: [Math Fidelity and Precision Control](https://deepwiki.com/tenstorrent/tt-llk/5.6-math-fidelity-and-precision-control)

For information on data preparation before math operations, see [Unpacker System](https://deepwiki.com/tenstorrent/tt-llk/4-unpacker-system). For information on writing results to L1 memory after math operations, see [Packer System](https://deepwiki.com/tenstorrent/tt-llk/6-packer-system).

**Sources:**[Diagram 2 from high-level overview](https://github.com/tenstorrent/tt-llk/blob/366f58e2/Diagram%202%20from%20high-level%20overview)[tt_llk_wormhole_b0/common/inc/cmath_common.h 1-50](https://github.com/tenstorrent/tt-llk/blob/366f58e2/tt_llk_wormhole_b0/common/inc/cmath_common.h#L1-L50)

## Math Unit Architecture

The math unit is the computational core of the Tensix processor, positioned between the unpacker and packer in the data pipeline. It consists of two primary computational units: the FPU (Floating Point Unit) for standard arithmetic operations and the SFPU (Special Function Processing Unit) for complex mathematical functions.

### Data Flow Through Math Unit

**Math Unit Pipeline: Source Registers → FPU/SFPU → Destination Registers**

**Sources:**[Diagram 2 from high-level overview](https://github.com/tenstorrent/tt-llk/blob/366f58e2/Diagram%202%20from%20high-level%20overview)[tt_llk_wormhole_b0/common/inc/cmath_common.h 1-100](https://github.com/tenstorrent/tt-llk/blob/366f58e2/tt_llk_wormhole_b0/common/inc/cmath_common.h#L1-L100)[tt_llk_wormhole_b0/llk_lib/llk_math_common.h 1-50](https://github.com/tenstorrent/tt-llk/blob/366f58e2/tt_llk_wormhole_b0/llk_lib/llk_math_common.h#L1-L50)[tt_llk_wormhole_b0/common/inc/cunpack_common.h 350-375](https://github.com/tenstorrent/tt-llk/blob/366f58e2/tt_llk_wormhole_b0/common/inc/cunpack_common.h#L350-L375)

### Register Organization

The math unit operates on data organized in **faces** (16x16 datums, defined by `FACE_R_DIM=16` and `FACE_C_DIM=16`). Source registers hold input data:

| Register | Capacity (Rows) | Tile Capacity | Configuration Address |
| --- | --- | --- | --- |
| **SrcA** | 64 rows (4 faces) | 4 tiles (FP16), 2 tiles (FP32) | `THCON_SEC0_REG5_Dest_cntx0_address` |
| **SrcB** | 64 rows (4 faces) | 4 tiles (FP16), 2 tiles (FP32) | `THCON_SEC1_REG5_Dest_cntx0_address` |
| **Dest** | 64 rows (FP16), 32 rows (FP32) | 8 tiles (FP16), 4 tiles (FP32) | `DEST_REGISTER_HALF_SIZE=256` |

The destination register size depends on the `is_fp32_dest_acc_en` template parameter, which controls `ALU_ACC_CTRL_Fp32_enabled`:

*   **FP16 dest_acc**: 64 rows = 4 faces = 8 tiles (with `DstSync::SyncHalf`)
*   **FP32 dest_acc**: 32 rows = 2 faces = 4 tiles (with `DstSync::SyncHalf`)

Destination capacity is halved in FP32 mode because each datum requires two 16-bit slots.

**Sources:**[tt_llk_wormhole_b0/common/inc/cmath_common.h 1-50](https://github.com/tenstorrent/tt-llk/blob/366f58e2/tt_llk_wormhole_b0/common/inc/cmath_common.h#L1-L50)[tt_llk_wormhole_b0/common/inc/cunpack_common.h 350-375](https://github.com/tenstorrent/tt-llk/blob/366f58e2/tt_llk_wormhole_b0/common/inc/cunpack_common.h#L350-L375)[tt_llk_wormhole_b0/common/inc/ckernel_defs.h](https://github.com/tenstorrent/tt-llk/blob/366f58e2/tt_llk_wormhole_b0/common/inc/ckernel_defs.h)

## Operation Categories

The math unit supports four primary categories of operations:

### Element-wise Operations

Operations that apply a function to corresponding elements from two tiles or perform unary transformations:

| Operation | Function | TTI Instruction | LLK Function | Broadcast Support |
| --- | --- | --- | --- | --- |
| **ELWADD** | `A + B` | `TTI_ELWADD` | `_llk_math_eltwise_binary_<ELWADD>()` | `p_elwise::SRCB_*` modes |
| **ELWSUB** | `A - B` | `TTI_ELWSUB` | `_llk_math_eltwise_binary_<ELWSUB>()` | `p_elwise::SRCB_*` modes |
| **ELWMUL** | `A * B` | `TTI_ELWMUL` | `_llk_math_eltwise_binary_<ELWMUL>()` | `p_elwise::SRCB_*` modes |
| **Datacopy A2D** | `A → Dest` | `TTI_MOVA2D` | `_llk_math_eltwise_unary_datacopy_<A2D>()` | N/A |
| **Datacopy B2D** | `B → Dest` | `TTI_MOVB2D` | `_llk_math_eltwise_unary_datacopy_<B2D>()` | `p_movb2d::MOV_*_BRCST` modes |

Broadcast modes are specified via the `BroadcastType` enum:

*   `BroadcastType::NONE`: Standard element-wise operation
*   `BroadcastType::ROW`: Broadcast single row across all rows
*   `BroadcastType::COL`: Broadcast single column across all columns
*   `BroadcastType::SCALAR`: Broadcast single element across entire tile

The `p_elwise::SRCB_BCAST_ALL`, `p_elwise::SRCB_BCAST_ROW`, `p_elwise::SRCB_BCAST_COL`, and `p_elwise::SRCB_NO_BCAST` constants control hardware broadcast behavior in TTI instructions.

**Sources:**[tt_llk_wormhole_b0/llk_lib/llk_math_eltwise_binary.h 1-100](https://github.com/tenstorrent/tt-llk/blob/366f58e2/tt_llk_wormhole_b0/llk_lib/llk_math_eltwise_binary.h#L1-L100)[tt_llk_wormhole_b0/llk_lib/llk_math_eltwise_unary_datacopy.h 1-174](https://github.com/tenstorrent/tt-llk/blob/366f58e2/tt_llk_wormhole_b0/llk_lib/llk_math_eltwise_unary_datacopy.h#L1-L174)[tt_llk_wormhole_b0/common/inc/ckernel_ops.h](https://github.com/tenstorrent/tt-llk/blob/366f58e2/tt_llk_wormhole_b0/common/inc/ckernel_ops.h)

### Matrix Multiplication Operations

Matrix multiplication is performed using the `TTI_MVMUL` instruction, which computes `D = B * A` where B is treated as row vectors and A as a column matrix. The operation is configured via `_llk_math_matmul_()` and `_llk_unpack_AB_matmul_()`:

**Matmul Dimensions:**

*   `ct_dim`: Number of tiles in output columns (controls reuse of input 0/A)
*   `rt_dim`: Number of tiles in output rows (controls reuse of input 1/B)
*   `kt_dim`: Number of tiles in inner dimension (accumulation depth)

**Reuse Logic** (from [llk_math_matmul.h 300-310](https://github.com/tenstorrent/tt-llk/blob/366f58e2/llk_math_matmul.h#L300-L310)):

When `reuse_a=true`, input 0 (loaded to SrcB) is kept in registers and reused across `ct_dim` outputs. Otherwise, input 1 (loaded to SrcA) is reused. The `_llk_unpack_AB_matmul_mop_config_()` function configures the replay buffer to increment addresses only for the non-reused operand.

The math unit handles:

*   **Transpose support**: Via `within_face_16x16_transpose` (configured in `THCON_SEC0_REG2_Haloize_mode`)
*   **Narrow tile support**: 16x32 (`is_in0_16x32`) and 32x16 (`is_in0_32x16`, `is_in1_32x16`) tiles
*   **Partial face unpacking**: For non-standard tile dimensions via `unpA_partial_face` and `unpB_partial_face`

**Sources:**[tt_llk_wormhole_b0/llk_lib/llk_math_matmul.h 1-600](https://github.com/tenstorrent/tt-llk/blob/366f58e2/tt_llk_wormhole_b0/llk_lib/llk_math_matmul.h#L1-L600)[tt_llk_wormhole_b0/llk_lib/llk_unpack_AB_matmul.h 1-300](https://github.com/tenstorrent/tt-llk/blob/366f58e2/tt_llk_wormhole_b0/llk_lib/llk_unpack_AB_matmul.h#L1-L300)

### Reduction and Pooling Operations

Reduction operations collapse data along rows or columns using the `_llk_math_reduce_()` function:

| Reduce Type | `ReduceDim` Value | `PoolType` | TTI Instruction | Output Shape |
| --- | --- | --- | --- | --- |
| **ReduceRow** | `REDUCE_ROW` | `MAX` | `TTI_GMPOOL` with `p_gpool::DIM_16X16` | 1x32 (single row) |
| **ReduceRow** | `REDUCE_ROW` | `SUM` or `AVG` | `TTI_GAPOOL` or HiFi accumulation | 1x32 (single row) |
| **ReduceCol** | `REDUCE_COL` | `MAX` or `AVG` | `TTI_GMPOOL` or `TTI_GAPOOL` | 32x1 (single column) |
| **ReduceScalar** | `REDUCE_SCALAR` | `MAX` or `AVG` | Multiple `TTI_GMPOOL`/`TTI_GAPOOL` | 1x1 (scalar) |

The implementation (in [llk_math_reduce.h 22-100](https://github.com/tenstorrent/tt-llk/blob/366f58e2/llk_math_reduce.h#L22-L100)) configures address modifiers differently based on the reduction dimension:

*   `REDUCE_ROW`: Uses `TTI_TRNSPSRCB` to transpose faces before pooling
*   `REDUCE_COL`: Applies pooling vertically through faces
*   High fidelity mode uses `ckernel_template::run()` with multiple accumulation passes instead of single pooling instructions

The unpacker must be configured via `_llk_unpack_reduce_init_()` to provide data in the appropriate layout for the reduction type.

**Sources:**[tt_llk_wormhole_b0/llk_lib/llk_math_reduce.h 1-350](https://github.com/tenstorrent/tt-llk/blob/366f58e2/tt_llk_wormhole_b0/llk_lib/llk_math_reduce.h#L1-L350)[tt_llk_wormhole_b0/llk_lib/llk_unpack_reduce.h 1-100](https://github.com/tenstorrent/tt-llk/blob/366f58e2/tt_llk_wormhole_b0/llk_lib/llk_unpack_reduce.h#L1-L100)

### SFPU Operations

The SFPU provides 56 operations on Wormhole and 55 on Blackhole (Blackhole lacks Welford's algorithm operations). All SFPU operations are accessed via `_llk_math_eltwise_unary_sfpu_()` with a `SfpuType` template parameter.

**SFPU Operation Categories:**

| Category | Operations | Implementation Header |
| --- | --- | --- |
| **Activation** | `exp`, `exp2`, `gelu`, `gelu_derivative`, `sigmoid`, `tanh`, `tanh_derivative`, `relu`, `elu`, `silu`, `hardtanh` | `ckernel_sfpu_activations.h`, `ckernel_sfpu_relu.h` |
| **Transcendental** | `log`, `sqrt`, `rsqrt`, `recip`, `square` | `ckernel_sfpu_exp.h`, `ckernel_sfpu_log.h`, `ckernel_sfpu_sqrt.h`, `ckernel_sfpu_recip.h` |
| **Trigonometric** | `sin`, `cos`, `tan`, `asin`, `acos`, `atan` | `ckernel_sfpu_trigonometry.h` |
| **Statistical** | `erf`, `erfc`, `erfinv` | `ckernel_sfpu_comp.h` |
| **Integer** | `add_int32`, `sub_int32`, `mul_int32`, `add_int16`, `mul_int16` | `ckernel_sfpu_add_int.h`, `ckernel_sfpu_mul_int.h` |
| **Comparison** | `max`, `min`, `abs`, `sign`, `negative` | `ckernel_sfpu_comp.h`, `ckernel_sfpu_abs.h` |
| **Typecast** | `typecast`, `cast_fp32_to_fp16a`, `fp16b_to_fp16a` | `ckernel_sfpu_typecast.h`, `ckernel_sfpu_cast_fp32_to_fp16a.h` |
| **Wormhole-only** | `welfords_update`, `welfords_combine` | `ckernel_sfpu_welfords.h` |
| **Special** | `topk_local_sort`, `topk_merge`, `topk_rebuild`, `max_pool_indices`, `reshuffle_rows` | `ckernel_sfpu_topk.h`, `ckernel_sfpu_reshuffle_rows.h` |

SFPU functions operate on destination registers in-place. The typical pattern (from [llk_math_eltwise_unary_sfpu.h 60-75](https://github.com/tenstorrent/tt-llk/blob/366f58e2/llk_math_eltwise_unary_sfpu.h#L60-L75)):

1.   Call `_llk_math_eltwise_unary_sfpu_start_()` to set destination address and wait for math completion
2.   Iterate through faces calling `sfpu::calculate_<operation>()` functions from `sfpi.h` namespace
3.   Call `_llk_math_eltwise_unary_sfpu_done_()` to complete and restore state

The `reshuffle_rows` operation differs between architectures: Wormhole uses `ADDR_MOD_3` while Blackhole uses `ADDR_MOD_7` (see [ckernel_sfpu_reshuffle_rows.h 1-60](https://github.com/tenstorrent/tt-llk/blob/366f58e2/ckernel_sfpu_reshuffle_rows.h#L1-L60)).

**Sources:**[tt_llk_wormhole_b0/common/inc/ckernel_sfpu.h 1-60](https://github.com/tenstorrent/tt-llk/blob/366f58e2/tt_llk_wormhole_b0/common/inc/ckernel_sfpu.h#L1-L60)[tt_llk_blackhole/common/inc/ckernel_sfpu.h 1-59](https://github.com/tenstorrent/tt-llk/blob/366f58e2/tt_llk_blackhole/common/inc/ckernel_sfpu.h#L1-L59)[tt_llk_wormhole_b0/llk_lib/llk_math_eltwise_unary_sfpu.h 1-94](https://github.com/tenstorrent/tt-llk/blob/366f58e2/tt_llk_wormhole_b0/llk_lib/llk_math_eltwise_unary_sfpu.h#L1-L94)[tt_llk_wormhole_b0/common/inc/sfpu/ckernel_sfpu_reshuffle_rows.h 1-100](https://github.com/tenstorrent/tt-llk/blob/366f58e2/tt_llk_wormhole_b0/common/inc/sfpu/ckernel_sfpu_reshuffle_rows.h#L1-L100)

## Address Modifiers for Math Operations

Address modifiers (`ADDR_MOD_0` through `ADDR_MOD_7`) control how the math unit accesses source and destination registers. Each modifier is configured via the `addr_mod_t` struct and applied using `.set(ADDR_MOD_N)`.

### Address Modifier Structure and Fields

**addr_mod_t Structure (from ckernel_addrmod.h):**

### Example: Element-wise Binary Address Modifiers

From `eltwise_binary_configure_addrmod()` in [llk_math_eltwise_binary.h 19-43](https://github.com/tenstorrent/tt-llk/blob/366f58e2/llk_math_eltwise_binary.h#L19-L43):

The broadcast type determines `srcb.incr` via:

**Sources:**[tt_llk_wormhole_b0/llk_lib/llk_math_eltwise_binary.h 19-43](https://github.com/tenstorrent/tt-llk/blob/366f58e2/tt_llk_wormhole_b0/llk_lib/llk_math_eltwise_binary.h#L19-L43)[tt_llk_wormhole_b0/llk_lib/llk_math_matmul.h 23-296](https://github.com/tenstorrent/tt-llk/blob/366f58e2/tt_llk_wormhole_b0/llk_lib/llk_math_matmul.h#L23-L296)[tt_llk_wormhole_b0/common/inc/ckernel_addrmod.h](https://github.com/tenstorrent/tt-llk/blob/366f58e2/tt_llk_wormhole_b0/common/inc/ckernel_addrmod.h)

## Micro-Operation Programming (MOPs)

Math operations are programmed using **MOPs** (Micro-Operation Programs) via the `ckernel_template` class. A MOP defines a replay buffer sequence that the hardware executes repeatedly without RISC-V intervention.

### ckernel_template Class API

**Constructor and Methods (from ckernel_template.h):**

### Example: Element-wise Add MOP Configuration

From `eltwise_binary_configure_mop()` in [llk_math_eltwise_binary.h 112-200](https://github.com/tenstorrent/tt-llk/blob/366f58e2/llk_math_eltwise_binary.h#L112-L200):

The MOP executes as:

1.   **Start operation** (if set): Executes once
2.   **Outer loop** (4 iterations): For each face
3.   **Inner loop** (2 iterations): For each 8-row chunk
4.   **Loop operations**: `TT_OP_ELWADD` with `ADDR_MOD_0` advancing registers
5.   **End operation**: `TT_OP_SETRWC` clears counters

High fidelity operations use `ADDR_MOD_2` and `ADDR_MOD_3` to increment the fidelity counter and execute multiple passes.

**Sources:**[tt_llk_wormhole_b0/llk_lib/llk_math_eltwise_binary.h 112-200](https://github.com/tenstorrent/tt-llk/blob/366f58e2/tt_llk_wormhole_b0/llk_lib/llk_math_eltwise_binary.h#L112-L200)[tt_llk_wormhole_b0/llk_lib/llk_math_matmul.h 298-600](https://github.com/tenstorrent/tt-llk/blob/366f58e2/tt_llk_wormhole_b0/llk_lib/llk_math_matmul.h#L298-L600)[tt_llk_wormhole_b0/common/inc/ckernel_template.h](https://github.com/tenstorrent/tt-llk/blob/366f58e2/tt_llk_wormhole_b0/common/inc/ckernel_template.h)[tt_llk_wormhole_b0/common/inc/cmath_common.h 30-31](https://github.com/tenstorrent/tt-llk/blob/366f58e2/tt_llk_wormhole_b0/common/inc/cmath_common.h#L30-L31)

## Math Unit Configuration

### Core Configuration Registers

Math unit behavior is controlled through several configuration registers:

| Register Group | Purpose | Key Fields |
| --- | --- | --- |
| **ALU_FORMAT_SPEC_REG0/1/2** | Data format control | `SrcA`, `SrcB`, `Dstacc` formats |
| **ALU_ACC_CTRL** | Accumulation mode | `Fp32_enabled`, `SFPU_Fp32_enabled` |
| **ALU_ROUNDING_MODE** | Rounding behavior | `Fpu_srnd_en`, `Gasket_srnd_en` |

### Configuration Example

### Initialization Patterns

Math unit initialization typically follows this pattern:

1.   **Configure address modifiers** via `*_configure_addrmod()`
2.   **Program MOP** via `*_configure_mop()` and `ckernel_template`
3.   **Set destination write address** via `math::set_dst_write_addr()`
4.   **Execute operation** via `ckernel_template::run()`
5.   **Clear destination** via `TT_ZEROACC()` if needed

**Sources:**[tt_llk_wormhole_b0/common/inc/cunpack_common.h 90-115](https://github.com/tenstorrent/tt-llk/blob/366f58e2/tt_llk_wormhole_b0/common/inc/cunpack_common.h#L90-L115)[tt_llk_wormhole_b0/llk_lib/llk_math_common.h](https://github.com/tenstorrent/tt-llk/blob/366f58e2/tt_llk_wormhole_b0/llk_lib/llk_math_common.h)

## Math Fidelity Modes

The math unit supports multiple **fidelity modes** that control the precision of floating-point operations through multiple accumulation passes:

| Mode | Passes | Precision | Use Case |
| --- | --- | --- | --- |
| **LoFi** | 1 | Standard | Fast, lower precision acceptable |
| **HiFi2** | 2 | Medium | Balanced performance/precision |
| **HiFi3** | 3 | High | ML training, sensitive operations |
| **HiFi4** | 4 | Highest | Maximum precision requirements |

High fidelity modes use the fidelity address modifier field to iterate through multiple passes, accumulating partial results with different rounding behaviors. The fidelity counter is incremented via address modifiers and cleared at the end of tile processing.

**Sources:**[tt_llk_wormhole_b0/llk_lib/llk_math_matmul.h 23-100](https://github.com/tenstorrent/tt-llk/blob/366f58e2/tt_llk_wormhole_b0/llk_lib/llk_math_matmul.h#L23-L100)[tt_llk_wormhole_b0/llk_lib/llk_math_eltwise_binary.h 19-50](https://github.com/tenstorrent/tt-llk/blob/366f58e2/tt_llk_wormhole_b0/llk_lib/llk_math_eltwise_binary.h#L19-L50)

## Data Movement Operations

Beyond computational operations, the math unit provides data movement primitives:

### Dest-to-Source Movement

| Operation | Function | Use Case |
| --- | --- | --- |
| `MOVD2A` | Dest → SrcA | Reuse destination as operand |
| `MOVD2B` | Dest → SrcB | Accumulate with broadcast |

Functions like `move_d2a_fixed_face()` and `move_d2b_fixed_face()` move entire 16x16 faces in 4-row chunks.

### Source-to-Dest Movement

| Operation | Function | Use Case |
| --- | --- | --- |
| `MOVA2D` | SrcA → Dest | Datacopy, format conversion |
| `MOVB2D` | SrcB → Dest | Broadcast to destination |

These are used extensively in `_llk_math_eltwise_unary_datacopy_()` for data format conversions and tile copying.

**Sources:**[tt_llk_wormhole_b0/common/inc/cmath_common.h 43-59](https://github.com/tenstorrent/tt-llk/blob/366f58e2/tt_llk_wormhole_b0/common/inc/cmath_common.h#L43-L59)[tt_llk_wormhole_b0/llk_lib/llk_math_eltwise_unary_datacopy.h 20-174](https://github.com/tenstorrent/tt-llk/blob/366f58e2/tt_llk_wormhole_b0/llk_lib/llk_math_eltwise_unary_datacopy.h#L20-L174)

## Key Math Unit APIs

### Core Function Patterns

All math operations follow a consistent naming convention with three categories of functions:

**Math Operation Function Pattern:**

**Example Function Names:**

| Operation | Addrmod Config | MOP Config | Init Function | Execute Function |
| --- | --- | --- | --- | --- |
| **Element-wise Binary** | `eltwise_binary_configure_addrmod()` | `eltwise_binary_configure_mop()` | `_llk_math_eltwise_binary_init_()` | `_llk_math_eltwise_binary_()` |
| **Matrix Multiply** | `matmul_configure_addrmod()` | `matmul_configure_mop()` | `_llk_math_matmul_init_()` | `_llk_math_matmul_()` |
| **Reduce** | `reduce_configure_addrmod()` | `reduce_configure_mop()` | `_llk_math_reduce_init_()` | `_llk_math_reduce_()` |
| **SFPU Unary** | `eltwise_unary_sfpu_configure_addrmod()` | N/A | `_llk_math_eltwise_unary_sfpu_init_()` | `_llk_math_eltwise_unary_sfpu_()` |

### Common Template Parameters

| Parameter | Type | Values | Purpose |
| --- | --- | --- | --- |
| `DstSync` | enum | `DstSync::SyncFull`, `DstSync::SyncHalf` | Controls which destination half to use and when to clear |
| `is_fp32_dest_acc_en` | bool | `true`, `false` | Enables 32-bit accumulation (halves dest capacity) |
| `MathFidelity` | enum | `MathFidelity::LoFi`, `HiFi2`, `HiFi3`, `HiFi4` | Controls precision via multi-pass accumulation |
| `BroadcastType` | enum | `BroadcastType::NONE`, `ROW`, `COL`, `SCALAR` | Broadcast mode for element-wise operations |
| `PoolType` | enum | `PoolType::MAX`, `SUM`, `AVG` | Reduction/pooling operation type |
| `ReduceDim` | enum | `ReduceDim::REDUCE_ROW`, `REDUCE_COL`, `REDUCE_SCALAR` | Reduction dimension |

**Fidelity Increment Calculation** (from [llk_math_eltwise_binary.h 19](https://github.com/tenstorrent/tt-llk/blob/366f58e2/llk_math_eltwise_binary.h#L19-L19)[llk_math_matmul.h 32](https://github.com/tenstorrent/tt-llk/blob/366f58e2/llk_math_matmul.h#L32-L32)):

Where `is_high_fidelity()` returns true for `HiFi2`, `HiFi3`, and `HiFi4` modes.

**Sources:**[tt_llk_wormhole_b0/llk_lib/llk_math_eltwise_binary.h 1-100](https://github.com/tenstorrent/tt-llk/blob/366f58e2/tt_llk_wormhole_b0/llk_lib/llk_math_eltwise_binary.h#L1-L100)[tt_llk_wormhole_b0/llk_lib/llk_math_matmul.h 1-100](https://github.com/tenstorrent/tt-llk/blob/366f58e2/tt_llk_wormhole_b0/llk_lib/llk_math_matmul.h#L1-L100)[tt_llk_wormhole_b0/llk_lib/llk_math_reduce.h 1-100](https://github.com/tenstorrent/tt-llk/blob/366f58e2/tt_llk_wormhole_b0/llk_lib/llk_math_reduce.h#L1-L100)[tt_llk_wormhole_b0/llk_lib/llk_math_eltwise_unary_sfpu.h 1-94](https://github.com/tenstorrent/tt-llk/blob/366f58e2/tt_llk_wormhole_b0/llk_lib/llk_math_eltwise_unary_sfpu.h#L1-L94)[tt_llk_wormhole_b0/common/inc/ckernel_defs.h](https://github.com/tenstorrent/tt-llk/blob/366f58e2/tt_llk_wormhole_b0/common/inc/ckernel_defs.h)

## Synchronization with Unpacker and Packer

The math unit coordinates with unpacker and packer via semaphores:

### Unpacker → Math Synchronization

### Math → Packer Synchronization

### Destination Register Clearing

After packing completes, destination registers must be cleared for the next operation:

**Sources:**[tt_llk_wormhole_b0/common/inc/cmath_common.h 43-100](https://github.com/tenstorrent/tt-llk/blob/366f58e2/tt_llk_wormhole_b0/common/inc/cmath_common.h#L43-L100)[tt_llk_wormhole_b0/llk_lib/llk_pack_common.h 19-60](https://github.com/tenstorrent/tt-llk/blob/366f58e2/tt_llk_wormhole_b0/llk_lib/llk_pack_common.h#L19-L60)

* * *

This overview provides the foundation for understanding math unit operations. For implementation details on specific operation types, consult the subsection pages listed at the beginning of this document.

Dismiss
Refresh this wiki

Enter email to refresh
---
project: tt-llk
github: https://github.com/tenstorrent/tt-llk
deepwiki: https://deepwiki.com/tenstorrent/tt-llk/6-packer-system
---

# Packer System

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

The Packer System is responsible for writing processed data from destination registers back to L1 memory. It operates as the final stage in the three-stage pipeline (Unpacker → Math → Packer) and handles data format conversion, layout transformation (tilize/untilize), and optional ReLU activation during the write-back process.

For information about the unpacker system, see [Unpacker System](https://deepwiki.com/tenstorrent/tt-llk/4-unpacker-system). For math operations that produce the data being packed, see [Math Unit Operations](https://deepwiki.com/tenstorrent/tt-llk/5-math-unit-operations).

## Overview and Architecture

The packer subsystem provides four parallel packer units (PCK0-PCK3) that can simultaneously write different portions of a tile to L1 memory. Each packer can independently write data with configurable addressing, format conversion, and optional ReLU activation.

Sources: [tt_llk_wormhole_b0/common/inc/cpack_common.h 1-800](https://github.com/tenstorrent/tt-llk/blob/366f58e2/tt_llk_wormhole_b0/common/inc/cpack_common.h#L1-L800)[tt_llk_wormhole_b0/llk_lib/llk_pack.h 1-200](https://github.com/tenstorrent/tt-llk/blob/366f58e2/tt_llk_wormhole_b0/llk_lib/llk_pack.h#L1-L200)

### Parallel Packer Units

The system provides four independent packer units that operate in parallel to maximize throughput:

| Packer | Section Register | Face Assignment | L1 Offset |
| --- | --- | --- | --- |
| PCK0 | THCON_SEC0_REG1 | Face 0 | Base address |
| PCK1 | THCON_SEC0_REG8 | Face 1 | Base + offset_1 |
| PCK2 | THCON_SEC1_REG1 | Face 2 | Base + offset_2 |
| PCK3 | THCON_SEC1_REG8 | Face 3 | Base + offset_3 |

Each packer can be independently configured via the `pack_config_t` structure but typically operates with shared settings for format conversion and compression.

Sources: [tt_llk_wormhole_b0/common/inc/cpack_common.h 22-28](https://github.com/tenstorrent/tt-llk/blob/366f58e2/tt_llk_wormhole_b0/common/inc/cpack_common.h#L22-L28)[tt_llk_wormhole_b0/common/inc/cpack_common.h 282-293](https://github.com/tenstorrent/tt-llk/blob/366f58e2/tt_llk_wormhole_b0/common/inc/cpack_common.h#L282-L293)

## Core Packer Operations and Configuration

### Configuration Structures

#### pack_config_t

The primary configuration structure for packer operation:

Key fields:

*   `in_data_format`/`out_data_format`: Source and destination data formats (4 bits each)
*   `exp_section_size`: Size of exponent section for BFP formats (varies by format)
*   `l1_dest_addr`: Target L1 memory address (32 bits)
*   `uncompress`: Always set to 1 for uncompressed output
*   `exp_threshold_en`/`exp_threshold`: Workaround for FP32 destination accumulator with BFP-A formats

Sources: [tt_llk_wormhole_b0/common/inc/cpack_common.h 34-60](https://github.com/tenstorrent/tt-llk/blob/366f58e2/tt_llk_wormhole_b0/common/inc/cpack_common.h#L34-L60)

### Packer Initialization Functions

Sources: [tt_llk_wormhole_b0/llk_lib/llk_pack.h 106-157](https://github.com/tenstorrent/tt-llk/blob/366f58e2/tt_llk_wormhole_b0/llk_lib/llk_pack.h#L106-L157)[tt_llk_wormhole_b0/common/inc/cpack_common.h 527-603](https://github.com/tenstorrent/tt-llk/blob/366f58e2/tt_llk_wormhole_b0/common/inc/cpack_common.h#L527-L603)

### Core Pack Operation Flow

The primary packing operation (`_llk_pack_`) executes the following sequence:

Sources: [tt_llk_wormhole_b0/llk_lib/llk_pack.h 184-197](https://github.com/tenstorrent/tt-llk/blob/366f58e2/tt_llk_wormhole_b0/llk_lib/llk_pack.h#L184-L197)

### Format-Specific Exponential Section Sizes

For BFP (Block Floating Point) formats, the packer must configure different exponential section sizes for each packer unit:

| Format | PCK0 (THCON_SEC0_REG1) | PCK1 (THCON_SEC0_REG8) | PCK2 (THCON_SEC1_REG1) | PCK3 (THCON_SEC1_REG8) |
| --- | --- | --- | --- | --- |
| Bfp8/Bfp8_b | 1 + num_faces (2 or 4) | 1 + 2 + 16 = 19 | 1 + 1 + 32 = 34 | 1 + 0 + 48 = 49 |
| Bfp4/Bfp4_b | 1 + num_faces (2 or 4) | 1 + 2 + 8 = 11 | 1 + 1 + 16 = 18 | 1 + 0 + 24 = 25 |
| Bfp2/Bfp2_b | 1 + num_faces (2 or 4) | 1 + 2 + 4 = 7 | 1 + 1 + 8 = 10 | 1 + 0 + 12 = 13 |
| Other formats | num_faces | 0 | 0 | 0 |

The exponential section size calculation is: `tile_header (1) + face_offset + datum_count`

Sources: [tt_llk_wormhole_b0/common/inc/cpack_common.h 318-354](https://github.com/tenstorrent/tt-llk/blob/366f58e2/tt_llk_wormhole_b0/common/inc/cpack_common.h#L318-L354)[tt_llk_wormhole_b0/common/inc/cpack_common.h 169-201](https://github.com/tenstorrent/tt-llk/blob/366f58e2/tt_llk_wormhole_b0/common/inc/cpack_common.h#L169-L201)

### Runtime Format Reconfiguration

The `reconfig_packer_data_format` function enables changing formats during execution without full reinitialization:

Sources: [tt_llk_wormhole_b0/common/inc/cpack_common.h 387-525](https://github.com/tenstorrent/tt-llk/blob/366f58e2/tt_llk_wormhole_b0/common/inc/cpack_common.h#L387-L525)

## Destination Register Management

### dest_rd_ctrl_t Configuration

The destination register read control structure determines how data is read from destination registers:

Key behaviors:

*   `Read_32b_data`: Always enabled for 32-bit formats (Int32, UInt32, Float32) or FP32 destination accumulator mode
*   `Read_int8`: Enabled only for Int8/UInt8 formats when not in FP32 accumulator mode
*   `Round_10b_mant`: Rounds FP32 mantissa to 10 bits when converting to Float16 in FP32 accumulator mode
*   `Read_unsigned`: Set when output format is UInt8

Sources: [tt_llk_wormhole_b0/common/inc/cpack_common.h 93-109](https://github.com/tenstorrent/tt-llk/blob/366f58e2/tt_llk_wormhole_b0/common/inc/cpack_common.h#L93-L109)[tt_llk_wormhole_b0/common/inc/cpack_common.h 295-316](https://github.com/tenstorrent/tt-llk/blob/366f58e2/tt_llk_wormhole_b0/common/inc/cpack_common.h#L295-L316)

### Destination Register Capacity and Addressing

The destination register capacity varies based on the `DstSync` mode:

The `DEST_REGISTER_HALF_SIZE` constant defines the offset between the two destination register banks in SyncHalf mode, allowing overlapped execution where the packer can pack from one bank while math writes to the other.

Sources: [tt_llk_wormhole_b0/common/inc/cpack_common.h 605-635](https://github.com/tenstorrent/tt-llk/blob/366f58e2/tt_llk_wormhole_b0/common/inc/cpack_common.h#L605-L635)

### Packer Destination Offset Registers

For specialized operations like fast tilize, each packer requires specific destination offsets:

Sources: [tt_llk_wormhole_b0/llk_lib/llk_pack.h 290-315](https://github.com/tenstorrent/tt-llk/blob/366f58e2/tt_llk_wormhole_b0/llk_lib/llk_pack.h#L290-L315)

## Untilize and Stride Configuration

### Untilize Mode Overview

Untilize mode converts tiled data (16×16 faces) back to row-major format for linear memory access:

Sources: [tt_llk_wormhole_b0/llk_lib/llk_pack_untilize.h 1-50](https://github.com/tenstorrent/tt-llk/blob/366f58e2/tt_llk_wormhole_b0/llk_lib/llk_pack_untilize.h#L1-L50)

### Stride Configuration

Packer strides control how the packer advances through destination registers and L1 memory:

The stride values determine how address counters advance when packing:

*   X stride: advance to next datum in a row
*   Y stride: advance to next row in a face
*   Z stride: advance to next face in a tile
*   W stride: advance to next tile

Sources: [tt_llk_wormhole_b0/common/inc/cpack_common.h 203-222](https://github.com/tenstorrent/tt-llk/blob/366f58e2/tt_llk_wormhole_b0/common/inc/cpack_common.h#L203-L222)

### L1 Offset Configuration for Parallel Packers

Each packer writes to a different offset in L1 to handle the four faces of a tile:

The hardware automatically adds tile header size to base addresses, so the configured offsets compensate by subtracting `PACK_TILE_HEADER_OFFSET` (1 in 16B units).

Sources: [tt_llk_wormhole_b0/common/inc/cpack_common.h 359-385](https://github.com/tenstorrent/tt-llk/blob/366f58e2/tt_llk_wormhole_b0/common/inc/cpack_common.h#L359-L385)

### Untilize MOP Configuration

The MOP (Micro-Operation Program) for untilize mode differs based on block dimensions:

Sources: [tt_llk_wormhole_b0/llk_lib/llk_pack_untilize.h 56-127](https://github.com/tenstorrent/tt-llk/blob/366f58e2/tt_llk_wormhole_b0/llk_lib/llk_pack_untilize.h#L56-L127)

### Untilize Address Programming

For multi-dimensional untilize operations, each packer receives a distinct L1 address:

This ensures that the four packers write consecutive 8-row sections (TILE_R_DIM/4 = 8) of the output matrix.

Sources: [tt_llk_wormhole_b0/common/inc/cpack_common.h 656-710](https://github.com/tenstorrent/tt-llk/blob/366f58e2/tt_llk_wormhole_b0/common/inc/cpack_common.h#L656-L710)

## Reduce Masking and Edge Offset Configuration

### pck_edge_offset_t Structure

The edge offset structure controls per-row masking for reduction operations:

Key concepts:

*   **mask** (bits 0-15): 16-bit mask applied to each row of a face during packing
*   **tile_row_set_select_packN** (bits 17-24): Selects which TILE_ROW_SET_MAPPING register each packer uses
*   **TILE_ROW_SET_MAPPING**: Contains 2 bits per row (16 rows per face) to select which PACK_EDGE_OFFSET_SEC mask applies

Sources: [tt_llk_wormhole_b0/common/inc/cpack_common.h 111-137](https://github.com/tenstorrent/tt-llk/blob/366f58e2/tt_llk_wormhole_b0/common/inc/cpack_common.h#L111-L137)

### Edge Masking System

The edge masking system operates through a two-level indirection:

**Usage in standard packing:**

This configuration results in no edge masking - all datums are packed.

Sources: [tt_llk_wormhole_b0/common/inc/cpack_common.h 583-589](https://github.com/tenstorrent/tt-llk/blob/366f58e2/tt_llk_wormhole_b0/common/inc/cpack_common.h#L583-L589)

### pack_counters_t Configuration

The pack counters control tile traversal and edge mask application:

The `pack_reads_per_xy_plane` field is set to `face_r_dim` to configure the tile position generator for proper edge mask reset timing. This is critical for reduction operations where only specific rows should be written.

Sources: [tt_llk_wormhole_b0/common/inc/cpack_common.h 139-155](https://github.com/tenstorrent/tt-llk/blob/366f58e2/tt_llk_wormhole_b0/common/inc/cpack_common.h#L139-L155)[tt_llk_wormhole_b0/common/inc/cpack_common.h 574-581](https://github.com/tenstorrent/tt-llk/blob/366f58e2/tt_llk_wormhole_b0/common/inc/cpack_common.h#L574-L581)

## ReLU Configuration and Application

### relu_config_t Structure

The ReLU configuration structure controls activation function application during packing:

**ApplyRelu field interpretation:**

*   Bit mask where each bit corresponds to a face (bits 0-3 for faces 0-3)
*   Set bit to 1 to enable ReLU for that face
*   Set bit to 0 to disable ReLU for that face

**ReluThreshold field:**

*   16-bit threshold value in the destination format
*   Values below threshold are clamped to zero
*   Standard ReLU uses threshold = 0

Sources: [tt_llk_wormhole_b0/common/inc/cpack_common.h 70-91](https://github.com/tenstorrent/tt-llk/blob/366f58e2/tt_llk_wormhole_b0/common/inc/cpack_common.h#L70-L91)

### ReLU Configuration in Packer Initialization

ReLU is configured during packer setup:

The ReLU configuration persists across pack operations until explicitly reconfigured. The hardware applies ReLU during the data format conversion stage of the pack operation.

Sources: [tt_llk_wormhole_b0/common/inc/cpack_common.h 556-564](https://github.com/tenstorrent/tt-llk/blob/366f58e2/tt_llk_wormhole_b0/common/inc/cpack_common.h#L556-L564)

### ReLU Application Example

Sources: [tt_llk_wormhole_b0/llk_lib/llk_pack.h 125-138](https://github.com/tenstorrent/tt-llk/blob/366f58e2/tt_llk_wormhole_b0/llk_lib/llk_pack.h#L125-L138)

### Generating ReLU Configuration

Helper function to generate ReLU configuration values:

The configuration is passed as a 32-bit value where the lower 16 bits contain the ApplyRelu mask and the upper 16 bits contain the threshold value.

Sources: [tt_llk_wormhole_b0/common/inc/cpack_common.h 805-830](https://github.com/tenstorrent/tt-llk/blob/366f58e2/tt_llk_wormhole_b0/common/inc/cpack_common.h#L805-L830)

Dismiss
Refresh this wiki

Enter email to refresh
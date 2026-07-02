---
project: tt-isa-documentation
github: https://github.com/tenstorrent/tt-isa-documentation
deepwiki: https://deepwiki.com/tenstorrent/tt-isa-documentation/6-data-formats-and-computation-models
---

# Data Formats and Computation Models

Relevant source files
*   [BlackholeA0/TensixTile/BabyRISCV/InstructionSet.md](https://github.com/tenstorrent/tt-isa-documentation/blob/9600a914/BlackholeA0/TensixTile/BabyRISCV/InstructionSet.md?plain=1)
*   [BlackholeA0/TensixTile/TensixCoprocessor/Dst.md](https://github.com/tenstorrent/tt-isa-documentation/blob/9600a914/BlackholeA0/TensixTile/TensixCoprocessor/Dst.md?plain=1)
*   [BlackholeA0/TensixTile/TensixCoprocessor/SFPMAD.md](https://github.com/tenstorrent/tt-isa-documentation/blob/9600a914/BlackholeA0/TensixTile/TensixCoprocessor/SFPMAD.md?plain=1)
*   [Miscellaneous/FMA/README.md](https://github.com/tenstorrent/tt-isa-documentation/blob/9600a914/Miscellaneous/FMA/README.md?plain=1)
*   [Miscellaneous/FMA/fma.c](https://github.com/tenstorrent/tt-isa-documentation/blob/9600a914/Miscellaneous/FMA/fma.c)
*   [WormholeB0/TensixTile/TensixCoprocessor/Dst.md](https://github.com/tenstorrent/tt-isa-documentation/blob/9600a914/WormholeB0/TensixTile/TensixCoprocessor/Dst.md?plain=1)
*   [WormholeB0/TensixTile/TensixCoprocessor/SFPMAD.md](https://github.com/tenstorrent/tt-isa-documentation/blob/9600a914/WormholeB0/TensixTile/TensixCoprocessor/SFPMAD.md?plain=1)
*   [WormholeB0/TensixTile/TensixCoprocessor/UNPACR_FlushCache.md](https://github.com/tenstorrent/tt-isa-documentation/blob/9600a914/WormholeB0/TensixTile/TensixCoprocessor/UNPACR_FlushCache.md?plain=1)
*   [WormholeB0/TensixTile/TensixCoprocessor/UNPACR_IncrementContextCounter.md](https://github.com/tenstorrent/tt-isa-documentation/blob/9600a914/WormholeB0/TensixTile/TensixCoprocessor/UNPACR_IncrementContextCounter.md?plain=1)
*   [WormholeB0/TensixTile/TensixCoprocessor/UNPACR_Regular.md](https://github.com/tenstorrent/tt-isa-documentation/blob/9600a914/WormholeB0/TensixTile/TensixCoprocessor/UNPACR_Regular.md?plain=1)
*   [WormholeB0/TensixTile/TensixCoprocessor/Unpackers/FormatConversion.md](https://github.com/tenstorrent/tt-isa-documentation/blob/9600a914/WormholeB0/TensixTile/TensixCoprocessor/Unpackers/FormatConversion.md?plain=1)
*   [WormholeB0/TensixTile/TensixCoprocessor/ZEROACC.md](https://github.com/tenstorrent/tt-isa-documentation/blob/9600a914/WormholeB0/TensixTile/TensixCoprocessor/ZEROACC.md?plain=1)

This page provides an overview of the data formats supported by Tenstorrent ASICs and the computational models used to operate on them. It covers the representation of floating-point and integer types across different storage locations (L1 memory, internal register files), the partially-fused FMA computation model used by the Vector Unit (SFPU), and the block floating-point (BFP) compression formats that enable efficient storage and bandwidth utilization.

For detailed instruction-level information about specific operations, see the [Instruction Set Reference](https://deepwiki.com/tenstorrent/tt-isa-documentation/5-instruction-set-reference). For programming guidance on optimizing data format usage, see [Memory Access Patterns and Optimization](https://deepwiki.com/tenstorrent/tt-isa-documentation/7.1-memory-access-patterns-and-optimization).

## Data Format Overview

The Tensix architecture supports multiple numeric representations optimized for different stages of the computation pipeline. Data flows from external memory through L1 scratchpad RAM, then through format conversion stages into internal register files where computation occurs.

**Sources:**[WormholeB0/TensixTile/TensixCoprocessor/Dst.md 28-52](https://github.com/tenstorrent/tt-isa-documentation/blob/9600a914/WormholeB0/TensixTile/TensixCoprocessor/Dst.md?plain=1#L28-L52)[WormholeB0/TensixTile/TensixCoprocessor/Unpackers/FormatConversion.md 1-81](https://github.com/tenstorrent/tt-isa-documentation/blob/9600a914/WormholeB0/TensixTile/TensixCoprocessor/Unpackers/FormatConversion.md?plain=1#L1-L81)

### Storage Location Characteristics

Different storage locations support different data format subsets, optimized for their role in the computation pipeline:

| Location | Width | Supported Formats | Primary Use |
| --- | --- | --- | --- |
| **L1 Memory** | Byte-addressable | All formats, compressed and uncompressed | High-bandwidth scratchpad storage |
| **SrcA / SrcB** | ≤19 bits | TF32, BF16, FP16, Integer "8", Integer "16" | Matrix Unit inputs |
| **Dst** | 16 or 32 bits | BF16, FP16 (16-bit) FP32, Integer "32" (32-bit) Integer "8", Integer "16" (opaque) | Matrix/Vector Unit accumulator |
| **LReg[0-16]** | 32 bits | FP32 only | Vector Unit (SFPU) operands |

**Sources:**[WormholeB0/TensixTile/TensixCoprocessor/Dst.md 28-40](https://github.com/tenstorrent/tt-isa-documentation/blob/9600a914/WormholeB0/TensixTile/TensixCoprocessor/Dst.md?plain=1#L28-L40)[BlackholeA0/TensixTile/TensixCoprocessor/Dst.md 28-52](https://github.com/tenstorrent/tt-isa-documentation/blob/9600a914/BlackholeA0/TensixTile/TensixCoprocessor/Dst.md?plain=1#L28-L52)

## Numeric Type Categories

### Floating-Point Types

The architecture supports multiple floating-point precisions, each with different bit layouts and precision characteristics:

**Notes:**

*   **FP32** is IEEE754-compliant in storage, but computation models may differ (see [Floating-Point Models and FMA](https://deepwiki.com/tenstorrent/tt-isa-documentation/6.1-floating-point-models-and-fma))
*   **TF32** exists only in internal register files (`SrcA`, `SrcB`), never in L1
*   **BF16** variants (BFP8/4/2) use BF16-compatible exponent ranges
*   **FP16** variants (BFP8a/4a/2a) use FP16-compatible exponent ranges
*   **FP16** uses a non-standard infinity encoding (no NaN support)

**Sources:**[WormholeB0/TensixTile/TensixCoprocessor/Dst.md 28-40](https://github.com/tenstorrent/tt-isa-documentation/blob/9600a914/WormholeB0/TensixTile/TensixCoprocessor/Dst.md?plain=1#L28-L40)[WormholeB0/TensixTile/TensixCoprocessor/Unpackers/FormatConversion.md 1-20](https://github.com/tenstorrent/tt-isa-documentation/blob/9600a914/WormholeB0/TensixTile/TensixCoprocessor/Unpackers/FormatConversion.md?plain=1#L1-L20)

### Integer Types

Integer types come in two flavors: standard two's complement for L1 storage, and sign-magnitude for internal computation:

**Key Characteristics:**

*   **Integer "32"** (`Dst32b`): Sign-magnitude format, requires conversion when accessed by RISC-V cores
*   **Integer "16"** (`Dst16b`): Opaque 16-bit data, no computation instructions
*   **Integer "8"** (`Dst16b`): Overlaid onto FP16 with fixed exponent; used for quantized neural network activations

**Sources:**[WormholeB0/TensixTile/TensixCoprocessor/Dst.md 28-40](https://github.com/tenstorrent/tt-isa-documentation/blob/9600a914/WormholeB0/TensixTile/TensixCoprocessor/Dst.md?plain=1#L28-L40)[BlackholeA0/TensixTile/TensixCoprocessor/Dst.md 28-52](https://github.com/tenstorrent/tt-isa-documentation/blob/9600a914/BlackholeA0/TensixTile/TensixCoprocessor/Dst.md?plain=1#L28-L52)[WormholeB0/TensixTile/TensixCoprocessor/UNPACR_Regular.md 459-471](https://github.com/tenstorrent/tt-isa-documentation/blob/9600a914/WormholeB0/TensixTile/TensixCoprocessor/UNPACR_Regular.md?plain=1#L459-L471)

## Computation Models

### Matrix Unit (FPU) Computation

The Matrix Unit operates on low-precision formats (TF32, BF16, FP16) to achieve 4.096 TFLOP/s peak throughput per Tensix tile. It performs operations like:

*   `MVMUL`: Matrix-vector multiplication
*   `ELWMUL`, `ELWADD`: Element-wise operations
*   `GAPOOL`, `GMPOOL`: Pooling operations

The Matrix Unit reads from `SrcA` and `SrcB` register files (≤19-bit precision) and accumulates into `Dst`. Accumulation is performed in higher precision than the input formats to reduce error accumulation across multiple operations.

**Sources:** Diagram 2 (High-Level System Architecture), Diagram 3 (Data Flow)

### Vector Unit (SFPU) Computation

The Vector Unit implements a **partially-fused FMA** model for FP32 operations. Unlike IEEE754-compliant fused multiply-add, the Tensix FMA:

1.   Computes the product `x * y` in higher precision than FP32
2.   Removes 23 bits of precision from the product (rather than adding 23 bits to the addend)
3.   Adds the result to `z`
4.   Performs a single rounding step

**Key Divergences from IEEE754:**

*   Denormal inputs are treated as zero
*   Denormal outputs are flushed to zero (preserving sign in Blackhole, discarding in Wormhole)
*   If `x * y` alone would overflow to infinity, the result is infinity (even if properly fused would be finite)
*   NaN outputs are canonical `0x7fc00000` in Blackhole, non-canonical in Wormhole

For a bit-perfect software model, see [Miscellaneous/FMA/fma.c](https://github.com/tenstorrent/tt-isa-documentation/blob/9600a914/Miscellaneous/FMA/fma.c) which provides three implementations:

*   `fma_model_ieee`: IEEE754-compliant reference
*   `fma_model_bh`: Blackhole behavior
*   `fma_model_wh`: Wormhole behavior

**Sources:**[Miscellaneous/FMA/fma.c 1-287](https://github.com/tenstorrent/tt-isa-documentation/blob/9600a914/Miscellaneous/FMA/fma.c#L1-L287)[Miscellaneous/FMA/README.md 12-31](https://github.com/tenstorrent/tt-isa-documentation/blob/9600a914/Miscellaneous/FMA/README.md?plain=1#L12-L31)[BlackholeA0/TensixTile/TensixCoprocessor/SFPMAD.md 49-66](https://github.com/tenstorrent/tt-isa-documentation/blob/9600a914/BlackholeA0/TensixTile/TensixCoprocessor/SFPMAD.md?plain=1#L49-L66)[WormholeB0/TensixTile/TensixCoprocessor/SFPMAD.md 49-59](https://github.com/tenstorrent/tt-isa-documentation/blob/9600a914/WormholeB0/TensixTile/TensixCoprocessor/SFPMAD.md?plain=1#L49-L59)

### Baby RISC-V FMA

Blackhole Baby RISC-V cores implement FP32 FMA instructions (`fmadd.s`, `fmsub.s`, `fnmsub.s`, `fnmadd.s`) with the same partially-fused model as the Vector Unit (SFPU). Wormhole Baby RISC-V cores do not support FMA instructions.

**Sources:**[BlackholeA0/TensixTile/BabyRISCV/InstructionSet.md 22-23](https://github.com/tenstorrent/tt-isa-documentation/blob/9600a914/BlackholeA0/TensixTile/BabyRISCV/InstructionSet.md?plain=1#L22-L23)[Miscellaneous/FMA/README.md 5-6](https://github.com/tenstorrent/tt-isa-documentation/blob/9600a914/Miscellaneous/FMA/README.md?plain=1#L5-L6)

## Format Conversion Pipeline

Data undergoes format conversion at two key stages:

### Unpacker Format Conversion (L1 → Internal)

**Unpackers** read data from L1 memory and convert it to internal register file formats. The conversion process includes:

1.   **Address calculation** using Auto-incrementing Addressing Counters (ADCs)
2.   **Decompression** of BFP formats (reading shared exponents, expanding mantissas)
3.   **Format conversion** to internal bit layouts
4.   **Write** to `SrcA`, `SrcB`, or `Dst`

Available conversions to `SrcA`/`SrcB`:

*   FP32 → TF32, BF16, or FP16
*   BF16 → TF32 or BF16
*   BFP8/4/2 → TF32 or BF16
*   FP16, BFP8a/4a/2a, FP8 → FP16
*   INT8/UINT8 → Integer "8"
*   INT16 → Integer "16" (opaque)

Available conversions to `Dst`:

*   FP32 → FP32, BF16, or FP16
*   BF16, BFP8/4/2 → BF16
*   FP16, BFP8a/4a/2a, FP8 → FP16
*   INT32 → Integer "32"
*   INT16 → Integer "16" (opaque)
*   INT8/UINT8 → Integer "8"

**Sources:**[WormholeB0/TensixTile/TensixCoprocessor/Unpackers/FormatConversion.md 1-81](https://github.com/tenstorrent/tt-isa-documentation/blob/9600a914/WormholeB0/TensixTile/TensixCoprocessor/Unpackers/FormatConversion.md?plain=1#L1-L81)[WormholeB0/TensixTile/TensixCoprocessor/UNPACR_Regular.md 420-493](https://github.com/tenstorrent/tt-isa-documentation/blob/9600a914/WormholeB0/TensixTile/TensixCoprocessor/UNPACR_Regular.md?plain=1#L420-L493)

### Packer Format Conversion (Internal → L1)

**Packers** read data from `Dst` and optionally compress/convert it before writing to L1. The conversion process includes:

1.   **Input address generation** (from `Dst` or directly from L1)
2.   **Transformation** (edge masking, ReLU, downsampling)
3.   **Compression** to BFP formats (computing shared exponents, RLE/RSI metadata)
4.   **Format conversion** to L1 bit layouts
5.   **Optional accumulation** (atomic or non-atomic)
6.   **Multi-stream output** (separate metadata, exponent, and data streams)

The packer pipeline is more complex than the unpacker pipeline because it supports:

*   Compression (compute shared exponents per 16 elements, generate metadata)
*   Accumulation (read-modify-write with optional atomic semantics)
*   Multiple simultaneous output streams

**Sources:** Diagram 3 (Data Flow), [WormholeB0/TensixTile/TensixCoprocessor/UNPACR_Regular.md 420-569](https://github.com/tenstorrent/tt-isa-documentation/blob/9600a914/WormholeB0/TensixTile/TensixCoprocessor/UNPACR_Regular.md?plain=1#L420-L569)

## Data Type Bit Layouts in Internal Registers

Internal register files use non-standard bit layouts optimized for hardware implementation. These layouts differ from IEEE754 and from L1 storage formats.

### Dst Register Bit Layouts

When stored in `Dst16b` (16-bit view):

**BF16:**`[S][MMMMMMM][EEEEEEEE]` - Sign, 7-bit mantissa, 8-bit exponent

**FP16:**`[S][MMMMMMMMMM][EEEEE]` - Sign, 10-bit mantissa, 5-bit exponent

**Integer "8":**`[S][MMMMMMMMMM][EEEEE]` - Sign, 10-bit magnitude, fixed exponent (16 or 0)

**Integer "16":**`[S][MMMMMMMMMMMMMMM]` - Sign, 15-bit magnitude (opaque)

When stored in `Dst32b` (32-bit view):

**FP32:**`[S][MMMMMMM][EEEEEEEE][MMMMMMMMMMMMMMMM]` - Sign, high 7-bit mantissa, 8-bit exponent, low 16-bit mantissa

**Integer "32":**`[S][MMMMMMM][Exponent][MMMMMMMMMMMMMMMM]` - Sign-magnitude (31-bit magnitude)

**Note:** These are the in-memory bit layouts. When RISC-V cores access `Dst`, hardware automatically converts between these layouts and standard IEEE754 layouts (or two's complement for integers).

**Sources:**[WormholeB0/TensixTile/TensixCoprocessor/Dst.md 43-72](https://github.com/tenstorrent/tt-isa-documentation/blob/9600a914/WormholeB0/TensixTile/TensixCoprocessor/Dst.md?plain=1#L43-L72)[BlackholeA0/TensixTile/TensixCoprocessor/Dst.md 59-85](https://github.com/tenstorrent/tt-isa-documentation/blob/9600a914/BlackholeA0/TensixTile/TensixCoprocessor/Dst.md?plain=1#L59-L85)

### SrcA/SrcB Register Bit Layouts

`SrcA` and `SrcB` use ≤19-bit formats with rearranged fields:

**TF32:** 19 bits: `[S][MMMMMMMMMM][EEEEEEEE]` - Sign, 10-bit mantissa, 8-bit exponent

**BF16:** 19 bits: `[S][MMMMMMM][00][EEEEEEEE]` - Zero-extended from BF16

**FP16:** 19 bits: `[S][0MMMMMMMMMM][EEEEE000]` - Rearranged fields

These formats are only accessible to the Matrix Unit (FPU) and unpackers; RISC-V cores cannot directly access `SrcA`/`SrcB`.

**Sources:**[WormholeB0/TensixTile/TensixCoprocessor/UNPACR_Regular.md 550-565](https://github.com/tenstorrent/tt-isa-documentation/blob/9600a914/WormholeB0/TensixTile/TensixCoprocessor/UNPACR_Regular.md?plain=1#L550-L565)

## Block Floating-Point (BFP) Compression

BFP formats compress data by sharing a single exponent across a block of 16 mantissas. Each block stores:

1.   One 8-bit shared exponent
2.   16 mantissas (8-bit, 4-bit, or 2-bit each)
3.   Optional delta/zero-run metadata for sparse data

Two BFP families exist:

*   **BFP8/4/2**: Compatible with BF16 exponent range
*   **BFP8a/4a/2a**: Compatible with FP16 exponent range

### Compressed Data Layout in L1

When compressed data is stored in L1, it uses a tile format with:

```
[Tile Header (16 × (1 + DigestSize) bytes)]
[Row Start Table or Blob Start Table (NumRows+1 or NumBlobs+1 × 2 bytes)]
[Exponent Stream (if separate exponents, NumBlocks × 1 byte, aligned)]
[Data Stream (Mantissas interleaved with RLE/RSI delta metadata)]
```

The **Row Start Table** contains byte offsets indicating where each row's data begins within the Data Stream. For sparse data, **RLE (Run-Length Encoding)** and **RSI (Repeated Significant Index)** metadata is interleaved with the mantissas.

For detailed decompression logic, see the unpacker functional model at [WormholeB0/TensixTile/TensixCoprocessor/UNPACR_Regular.md 89-192](https://github.com/tenstorrent/tt-isa-documentation/blob/9600a914/WormholeB0/TensixTile/TensixCoprocessor/UNPACR_Regular.md?plain=1#L89-L192)

**Sources:**[WormholeB0/TensixTile/TensixCoprocessor/UNPACR_Regular.md 89-192](https://github.com/tenstorrent/tt-isa-documentation/blob/9600a914/WormholeB0/TensixTile/TensixCoprocessor/UNPACR_Regular.md?plain=1#L89-L192)[WormholeB0/TensixTile/TensixCoprocessor/UNPACR_Regular.md 495-520](https://github.com/tenstorrent/tt-isa-documentation/blob/9600a914/WormholeB0/TensixTile/TensixCoprocessor/UNPACR_Regular.md?plain=1#L495-L520)

## Undefined Value Semantics

The `Dst` register includes validity tracking per row. Rows can be marked undefined using the `ZEROACC` instruction. When undefined rows are read:

*   **Packers** interpret undefined rows as zero
*   **Matrix Unit (FPU)** interprets undefined rows as the identity element for the operation (−∞ for max-pooling, 0 for other operations)
*   **Vector Unit (SFPU)** exhibits undefined behavior when reading undefined rows
*   **Unpackers** exhibit undefined behavior when partially writing undefined rows

This validity tracking enables optimizations where intermediate results don't need to be explicitly zeroed.

**Sources:**[WormholeB0/TensixTile/TensixCoprocessor/ZEROACC.md 1-95](https://github.com/tenstorrent/tt-isa-documentation/blob/9600a914/WormholeB0/TensixTile/TensixCoprocessor/ZEROACC.md?plain=1#L1-L95)

## Architecture Differences: Wormhole vs Blackhole

Blackhole introduces several improvements over Wormhole in data format handling:

| Aspect | Wormhole B0 | Blackhole A0 |
| --- | --- | --- |
| **SFPMAD NaN handling** | Non-canonical NaN, sign bit leakage | Canonical NaN `0x7fc00000` |
| **SFPMAD negative zero** | Output always positive zero | Preserves sign of zero |
| **SFPMAD negate modifiers** | Not available | `SFPMAD_MOD1_NEGATE_VA`, `SFPMAD_MOD1_NEGATE_VC` |
| **Dst address remapping** | Fixed swizzle pattern | Configurable via `DEST_ACCESS_CFG_remap_addrs` |
| **Dst 32-bit swizzle** | Not available | Configurable via `DEST_ACCESS_CFG_swizzle_32b` |
| **Baby RISC-V FMA** | Not implemented | Implemented with partial-fusion model |

**Sources:**[BlackholeA0/TensixTile/TensixCoprocessor/SFPMAD.md 7-8](https://github.com/tenstorrent/tt-isa-documentation/blob/9600a914/BlackholeA0/TensixTile/TensixCoprocessor/SFPMAD.md?plain=1#L7-L8)[BlackholeA0/TensixTile/TensixCoprocessor/Dst.md 21-40](https://github.com/tenstorrent/tt-isa-documentation/blob/9600a914/BlackholeA0/TensixTile/TensixCoprocessor/Dst.md?plain=1#L21-L40)[Miscellaneous/FMA/README.md 23-30](https://github.com/tenstorrent/tt-isa-documentation/blob/9600a914/Miscellaneous/FMA/README.md?plain=1#L23-L30)

* * *

**Summary of Subsections:**

*   [Floating-Point Models and FMA](https://deepwiki.com/tenstorrent/tt-isa-documentation/6.1-floating-point-models-and-fma): Detailed bit-perfect models of FMA computation, divergences from IEEE754
*   [Data Type Catalog](https://deepwiki.com/tenstorrent/tt-isa-documentation/6.2-data-type-catalog): Complete enumeration of all supported types with precision specifications
*   [Format Conversion Pipeline](https://deepwiki.com/tenstorrent/tt-isa-documentation/6.3-format-conversion-pipeline): Bit-level transformation details for Unpacker and Packer conversions
*   [Compression and BFP Formats](https://deepwiki.com/tenstorrent/tt-isa-documentation/6.4-compression-and-bfp-formats): Block floating-point compression algorithms, metadata structures, decompression logic

Dismiss
Refresh this wiki

Enter email to refresh
---
project: tt-isa-documentation
github: https://github.com/tenstorrent/tt-isa-documentation
deepwiki: https://deepwiki.com/tenstorrent/tt-isa-documentation/8-blackhole-a0-enhancements
---

# Blackhole A0 Enhancements

Relevant source files
*   [BlackholeA0/TensixTile/BabyRISCV/InstructionSet.md](https://github.com/tenstorrent/tt-isa-documentation/blob/9600a914/BlackholeA0/TensixTile/BabyRISCV/InstructionSet.md?plain=1)
*   [BlackholeA0/TensixTile/TensixCoprocessor/Dst.md](https://github.com/tenstorrent/tt-isa-documentation/blob/9600a914/BlackholeA0/TensixTile/TensixCoprocessor/Dst.md?plain=1)
*   [BlackholeA0/TensixTile/TensixCoprocessor/LReg.md](https://github.com/tenstorrent/tt-isa-documentation/blob/9600a914/BlackholeA0/TensixTile/TensixCoprocessor/LReg.md?plain=1)
*   [BlackholeA0/TensixTile/TensixCoprocessor/SFPCOMPC.md](https://github.com/tenstorrent/tt-isa-documentation/blob/9600a914/BlackholeA0/TensixTile/TensixCoprocessor/SFPCOMPC.md?plain=1)
*   [BlackholeA0/TensixTile/TensixCoprocessor/SFPCONFIG.md](https://github.com/tenstorrent/tt-isa-documentation/blob/9600a914/BlackholeA0/TensixTile/TensixCoprocessor/SFPCONFIG.md?plain=1)
*   [BlackholeA0/TensixTile/TensixCoprocessor/SFPENCC.md](https://github.com/tenstorrent/tt-isa-documentation/blob/9600a914/BlackholeA0/TensixTile/TensixCoprocessor/SFPENCC.md?plain=1)
*   [BlackholeA0/TensixTile/TensixCoprocessor/SFPLUT.md](https://github.com/tenstorrent/tt-isa-documentation/blob/9600a914/BlackholeA0/TensixTile/TensixCoprocessor/SFPLUT.md?plain=1)
*   [BlackholeA0/TensixTile/TensixCoprocessor/SFPLUTFP32.md](https://github.com/tenstorrent/tt-isa-documentation/blob/9600a914/BlackholeA0/TensixTile/TensixCoprocessor/SFPLUTFP32.md?plain=1)
*   [BlackholeA0/TensixTile/TensixCoprocessor/SFPMAD.md](https://github.com/tenstorrent/tt-isa-documentation/blob/9600a914/BlackholeA0/TensixTile/TensixCoprocessor/SFPMAD.md?plain=1)
*   [BlackholeA0/TensixTile/TensixCoprocessor/SFPPOPC.md](https://github.com/tenstorrent/tt-isa-documentation/blob/9600a914/BlackholeA0/TensixTile/TensixCoprocessor/SFPPOPC.md?plain=1)
*   [BlackholeA0/TensixTile/TensixCoprocessor/SFPPUSHC.md](https://github.com/tenstorrent/tt-isa-documentation/blob/9600a914/BlackholeA0/TensixTile/TensixCoprocessor/SFPPUSHC.md?plain=1)
*   [Miscellaneous/FMA/README.md](https://github.com/tenstorrent/tt-isa-documentation/blob/9600a914/Miscellaneous/FMA/README.md?plain=1)
*   [Miscellaneous/FMA/fma.c](https://github.com/tenstorrent/tt-isa-documentation/blob/9600a914/Miscellaneous/FMA/fma.c)
*   [WormholeB0/TensixTile/TensixCoprocessor/Dst.md](https://github.com/tenstorrent/tt-isa-documentation/blob/9600a914/WormholeB0/TensixTile/TensixCoprocessor/Dst.md?plain=1)
*   [WormholeB0/TensixTile/TensixCoprocessor/SFPCONFIG.md](https://github.com/tenstorrent/tt-isa-documentation/blob/9600a914/WormholeB0/TensixTile/TensixCoprocessor/SFPCONFIG.md?plain=1)
*   [WormholeB0/TensixTile/TensixCoprocessor/SFPENCC.md](https://github.com/tenstorrent/tt-isa-documentation/blob/9600a914/WormholeB0/TensixTile/TensixCoprocessor/SFPENCC.md?plain=1)
*   [WormholeB0/TensixTile/TensixCoprocessor/SFPMAD.md](https://github.com/tenstorrent/tt-isa-documentation/blob/9600a914/WormholeB0/TensixTile/TensixCoprocessor/SFPMAD.md?plain=1)
*   [WormholeB0/TensixTile/TensixCoprocessor/ZEROACC.md](https://github.com/tenstorrent/tt-isa-documentation/blob/9600a914/WormholeB0/TensixTile/TensixCoprocessor/ZEROACC.md?plain=1)

This page documents the key architectural improvements and bug fixes introduced in Blackhole A0 compared to Wormhole B0. The enhancements fall into three primary categories: Vector Unit (SFPU) computational improvements, memory/register system enhancements, and bug fixes. For detailed information on specific enhancement categories, see [SFPU and FMA Improvements](https://deepwiki.com/tenstorrent/tt-isa-documentation/8.1-sfpu-and-fma-improvements) and [Memory and Register Enhancements](https://deepwiki.com/tenstorrent/tt-isa-documentation/8.2-memory-and-register-enhancements).

## Enhancement Categories

Blackhole A0 introduces improvements across multiple subsystems within the Tensix tile architecture:

**Sources:**[BlackholeA0/TensixTile/TensixCoprocessor/SFPMAD.md 1-91](https://github.com/tenstorrent/tt-isa-documentation/blob/9600a914/BlackholeA0/TensixTile/TensixCoprocessor/SFPMAD.md?plain=1#L1-L91)[BlackholeA0/TensixTile/TensixCoprocessor/Dst.md 1-40](https://github.com/tenstorrent/tt-isa-documentation/blob/9600a914/BlackholeA0/TensixTile/TensixCoprocessor/Dst.md?plain=1#L1-L40)[BlackholeA0/TensixTile/TensixCoprocessor/SFPLUTFP32.md 1-154](https://github.com/tenstorrent/tt-isa-documentation/blob/9600a914/BlackholeA0/TensixTile/TensixCoprocessor/SFPLUTFP32.md?plain=1#L1-L154)

## Feature Comparison Matrix

The following table summarizes the major differences between Wormhole B0 and Blackhole A0:

| Feature Area | Wormhole B0 | Blackhole A0 | Impact |
| --- | --- | --- | --- |
| **FMA Computation** | Partially fused, non-canonical NaN, negative zero issues | Canonical NaN (`0x7fc00000`), sign-preserved zero, improved sticky bit handling | Better IEEE754 conformance |
| **SFPMAD Modifiers** | `INDIRECT_VA`, `INDIRECT_VD` only | Added `NEGATE_VA`, `NEGATE_VC` | Eliminates separate negate instructions |
| **Instruction Scheduling** | Manual for SFPMAD (requires `SFPNOP`) | Automatic RAW hazard detection and stalling | Simplified programming, fewer bugs |
| **Dst Address Mapping** | Fixed `Adj32` function | Configurable via `DEST_ACCESS_CFG_remap_addrs` and `DEST_ACCESS_CFG_swizzle_32b` | Flexible memory layouts |
| **SFPLUT Variants** | 8-bit LUT only (`SFPLUT`) | Added 16-bit and FP32 LUT modes (`SFPLUTFP32`) | Higher precision approximations |
| **Stack Operations** | `SFPPOPC` Mod1 bug when stack full | Fixed, added Mod1 modes to `SFPPUSHC`/`SFPPOPC` | More robust control flow |
| **Baby RISC-V FMA** | Not available | Available with Blackhole FMA model | Unified FMA semantics |

**Sources:**[Miscellaneous/FMA/fma.c 1-288](https://github.com/tenstorrent/tt-isa-documentation/blob/9600a914/Miscellaneous/FMA/fma.c#L1-L288)[BlackholeA0/TensixTile/TensixCoprocessor/SFPMAD.md 7-86](https://github.com/tenstorrent/tt-isa-documentation/blob/9600a914/BlackholeA0/TensixTile/TensixCoprocessor/SFPMAD.md?plain=1#L7-L86)[BlackholeA0/TensixTile/TensixCoprocessor/Dst.md 21-37](https://github.com/tenstorrent/tt-isa-documentation/blob/9600a914/BlackholeA0/TensixTile/TensixCoprocessor/Dst.md?plain=1#L21-L37)[BlackholeA0/TensixTile/TensixCoprocessor/SFPLUTFP32.md 1-154](https://github.com/tenstorrent/tt-isa-documentation/blob/9600a914/BlackholeA0/TensixTile/TensixCoprocessor/SFPLUTFP32.md?plain=1#L1-L154)

## FMA Model Improvements

Blackhole A0 implements a revised FMA model that addresses several edge cases present in Wormhole B0. The computational model is shared between Baby RISC-V cores and the Tensix Vector Unit (SFPU).

### Key Behavioral Changes

The `fma_model` function used by `SFPMAD` and Baby RISC-V FMA instructions exhibits the following improvements:

**Canonical NaN:** Blackhole always emits `0x7fc00000` for NaN results, matching IEEE754 recommendations. Wormhole emitted `0x7f800001` with additional mantissa bits leaking from the computation.

**Sign Handling:** When the result is exactly zero, Blackhole preserves the sign bit based on the signs of the inputs and the operation performed. Wormhole always produced positive zero.

**Sticky Bit Logic:** In the normalization path, Wormhole had a bug when shifting right by two bits: `r_m = (r_m >> n) | (r_m & 1)` would drop one of the two shifted-out bits. Blackhole correctly implements: `r_m = (r_m >> n) | ((r_m & (n | 1)) != 0)`.

**Sources:**[Miscellaneous/FMA/fma.c 102-189](https://github.com/tenstorrent/tt-isa-documentation/blob/9600a914/Miscellaneous/FMA/fma.c#L102-L189)[Miscellaneous/FMA/README.md 23-30](https://github.com/tenstorrent/tt-isa-documentation/blob/9600a914/Miscellaneous/FMA/README.md?plain=1#L23-L30)[BlackholeA0/TensixTile/TensixCoprocessor/SFPMAD.md 56-66](https://github.com/tenstorrent/tt-isa-documentation/blob/9600a914/BlackholeA0/TensixTile/TensixCoprocessor/SFPMAD.md?plain=1#L56-L66)

## Automatic Instruction Scheduling

Blackhole introduces hardware-based instruction scheduling for `SFPMAD` and `SFPLUT` instructions to prevent read-after-write (RAW) hazards. In Wormhole, software was required to manually insert `SFPNOP` instructions to avoid hazards.

### Stalling Logic

The hardware automatically detects when a Vector Unit instruction attempts to read from an `LReg` register that was written by a `SFPMAD` or `SFPLUT` instruction in the previous cycle:

**Conservative Assumptions:** When `SFPMAD_MOD1_INDIRECT_VA` or `SFPMAD_MOD1_INDIRECT_VD` are used, the stalling logic conservatively assumes all `LReg` registers are read/written, potentially causing unnecessary stalls.

**Known Gaps:** Several instructions are not properly detected by the automatic stalling logic due to hardware bugs, requiring manual `SFPNOP` insertion:

*   `SFPAND` and `SFPOR` when using `MOD1_USE_VB`
*   `SFPIADD` and `SFPSHFT` (reads from `VD` not detected)
*   `SFPCONFIG` (reads from `LReg[0]` not detected)
*   `SFPSWAP` in comparison modes (1st cycle reads not detected)
*   `SFPSHFT2` in various modes

**SFPLOADMACRO Exception:** Automatic stalling does not apply to instructions executed as part of an `SFPLOADMACRO` sequence. Software must manually ensure correct scheduling within macro sequences.

**Sources:**[BlackholeA0/TensixTile/TensixCoprocessor/SFPMAD.md 69-86](https://github.com/tenstorrent/tt-isa-documentation/blob/9600a914/BlackholeA0/TensixTile/TensixCoprocessor/SFPMAD.md?plain=1#L69-L86)[BlackholeA0/TensixTile/TensixCoprocessor/SFPLUT.md 87-90](https://github.com/tenstorrent/tt-isa-documentation/blob/9600a914/BlackholeA0/TensixTile/TensixCoprocessor/SFPLUT.md?plain=1#L87-L90)

## New and Enhanced Instructions

### SFPLUTFP32

Blackhole introduces `SFPLUTFP32`, which extends the piecewise linear approximation capabilities of `SFPLUT` with higher-precision modes:

| Mode | Table Size | Coefficient Format | Split Points |
| --- | --- | --- | --- |
| `FP32_3ENTRY_TABLE` | 3 ranges | Full FP32 in `LReg[0..2]` and `LReg[4..6]` | 1.0, 2.0 |
| `FP16_3ENTRY_TABLE` | 3 ranges | Packed 16-bit in `LReg[0..2]` | 1.0, 2.0 |
| `FP16_6ENTRY_TABLE1` | 6 ranges | Packed 16-bit in `LReg[0..2]` and `LReg[4..6]` | 0.5, 1.0, 1.5, 2.0, 3.0 |
| `FP16_6ENTRY_TABLE2` | 6 ranges | Packed 16-bit in `LReg[0..2]` and `LReg[4..6]` | 0.5, 1.0, 1.5, 2.0, 4.0 |

The `FP32_3ENTRY_TABLE` mode provides full floating-point precision for both slope and intercept, enabling more accurate approximations of functions like exponential, logarithm, and trigonometric operations.

**Sources:**[BlackholeA0/TensixTile/TensixCoprocessor/SFPLUTFP32.md 1-154](https://github.com/tenstorrent/tt-isa-documentation/blob/9600a914/BlackholeA0/TensixTile/TensixCoprocessor/SFPLUTFP32.md?plain=1#L1-L154)

### SFPMAD Negation Modifiers

The `SFPMAD` instruction gains two new modifiers that eliminate the need for separate sign-flip operations:

The modifiers `SFPMAD_MOD1_NEGATE_VA` and `SFPMAD_MOD1_NEGATE_VC` flip the sign bit of the respective operand via `a ^= 0x80000000` or `c ^= 0x80000000`, enabling efficient implementation of operations like `VD = -(VA * VB) + VC` or `VD = VA * VB - VC`.

**Sources:**[BlackholeA0/TensixTile/TensixCoprocessor/SFPMAD.md 30-54](https://github.com/tenstorrent/tt-isa-documentation/blob/9600a914/BlackholeA0/TensixTile/TensixCoprocessor/SFPMAD.md?plain=1#L30-L54)

## Dst Register Address Remapping

Blackhole introduces configurability in how `Dst` register addresses are mapped to physical storage. The `Adj16` and `Adj32` functions now depend on configuration bits:

### Configuration Impact

The address remapping affects multiple subsystems:

**Behavioral Notes:**

*   Wormhole B0 always applied the fixed `Adj32` swizzle for 32-bit access (equivalent to `swizzle_32b = true, remap_addrs = false`)
*   Blackhole allows disabling the swizzle for linear address mapping
*   The `remap_addrs` bit introduces a bank-conflict-aware remapping for 16-bit access
*   Both bits affect packer address generation in addition to direct `Dst` access

**Sources:**[BlackholeA0/TensixTile/TensixCoprocessor/Dst.md 21-40](https://github.com/tenstorrent/tt-isa-documentation/blob/9600a914/BlackholeA0/TensixTile/TensixCoprocessor/Dst.md?plain=1#L21-L40)[WormholeB0/TensixTile/TensixCoprocessor/Dst.md 21-26](https://github.com/tenstorrent/tt-isa-documentation/blob/9600a914/WormholeB0/TensixTile/TensixCoprocessor/Dst.md?plain=1#L21-L26)

## Vector Unit Stack Enhancements

Blackhole improves the conditional execution stack used for implementing control flow in SIMT-style Vector Unit programming.

### SFPPUSHC Modes

The `SFPPUSHC` instruction gains a `Mod1` field that enables in-place stack manipulation:

| `Mod1` Value | Behavior |
| --- | --- |
| 0 | Plain push (Wormhole behavior): push current state onto stack |
| 1-12 | Replace stack top `UseLaneFlagsForLaneEnable`, mutate stack top `LaneFlags` via Boolean operation |
| 13 | Invert current `LaneFlags`, then replace stack top with current state |
| 14 | Replace stack top with constants: `UseLaneFlagsForLaneEnable = true, LaneFlags = true` |
| 15 | Replace stack top with constants: `UseLaneFlagsForLaneEnable = true, LaneFlags = false` |

These modes eliminate redundant push/pop pairs when implementing nested conditional structures.

**Sources:**[BlackholeA0/TensixTile/TensixCoprocessor/SFPPUSHC.md 1-82](https://github.com/tenstorrent/tt-isa-documentation/blob/9600a914/BlackholeA0/TensixTile/TensixCoprocessor/SFPPUSHC.md?plain=1#L1-L82)

### SFPPOPC Bug Fix

Wormhole B0 exhibited undefined behavior when `SFPPOPC` was executed with `Mod1 != 0` and a full stack (8 elements). Blackhole correctly handles this case. The `Mod1` modes for `SFPPOPC` parallel those of `SFPPUSHC`, enabling peek-and-modify operations:

| `Mod1` Value | Behavior |
| --- | --- |
| 0 | Plain pop (Wormhole behavior): pop from stack into current state |
| 1-12 | Set current `UseLaneFlagsForLaneEnable` from stack top, mutate current `LaneFlags` via Boolean operation with stack top |
| 13 | Invert current `LaneFlags` |
| 14 | Set current state to constants: `UseLaneFlagsForLaneEnable = true, LaneFlags = true` |
| 15 | Set current state to constants: `UseLaneFlagsForLaneEnable = true, LaneFlags = false` |

**Sources:**[BlackholeA0/TensixTile/TensixCoprocessor/SFPPOPC.md 1-86](https://github.com/tenstorrent/tt-isa-documentation/blob/9600a914/BlackholeA0/TensixTile/TensixCoprocessor/SFPPOPC.md?plain=1#L1-L86)

### SFPCOMPC

The `SFPCOMPC` instruction (conditionally invert execution state) remains functionally identical but benefits from the stack handling fixes.

**Sources:**[BlackholeA0/TensixTile/TensixCoprocessor/SFPCOMPC.md 1-40](https://github.com/tenstorrent/tt-isa-documentation/blob/9600a914/BlackholeA0/TensixTile/TensixCoprocessor/SFPCOMPC.md?plain=1#L1-L40)

## Baby RISC-V FMA Support

Blackhole Baby RISC-V cores gain FMA instruction support (`fmadd.s`, `fmsub.s`, `fnmsub.s`, `fnmadd.s`) using the same improved FMA model as the Vector Unit. This unifies the computational semantics across control processors and compute units.

The Baby RISC-V FMA instructions:

*   Treat denormal inputs as zero
*   Flush denormal outputs to zero
*   Use round-to-nearest-even (ignore rounding mode bits)
*   Emit canonical NaN (`0x7fc00000`)
*   Preserve sign of zero results
*   Implement the same partially-fused multiply-add as `SFPMAD`

**Sources:**[BlackholeA0/TensixTile/BabyRISCV/InstructionSet.md 22-23](https://github.com/tenstorrent/tt-isa-documentation/blob/9600a914/BlackholeA0/TensixTile/BabyRISCV/InstructionSet.md?plain=1#L22-L23)[Miscellaneous/FMA/README.md 5](https://github.com/tenstorrent/tt-isa-documentation/blob/9600a914/Miscellaneous/FMA/README.md?plain=1#L5-L5)

## Migration Considerations

When porting kernels from Wormhole B0 to Blackhole A0:

1.   **Remove Manual Stalls:** Many `SFPNOP` instructions after `SFPMAD` can be removed due to automatic scheduling. However, review the list of undetected hazards and retain `SFPNOP` where necessary.

2.   **Leverage Negation Modifiers:** Replace `XOR` + `SFPMAD` sequences with single `SFPMAD` instructions using `NEGATE_VA` or `NEGATE_VC`.

3.   **Review NaN Handling:** If code relied on specific NaN bit patterns from Wormhole, update to expect canonical `0x7fc00000`.

4.   **Test Zero Sign Semantics:** Operations that produce zero may now have different signs than in Wormhole.

5.   **Consider `SFPLUTFP32`:** For piecewise linear approximations requiring higher precision, migrate from `SFPLUT` to `SFPLUTFP32` with FP32 mode.

6.   **Audit Dst Address Dependencies:** If code assumed specific Dst address mapping behavior, verify correctness with configurable `DEST_ACCESS_CFG_*` settings.

7.   **Simplify Stack Operations:** Review uses of `SFPPUSHC`/`SFPPOPC` and consider using new `Mod1` modes to reduce instruction count.

**Sources:** All files referenced above

Dismiss
Refresh this wiki

Enter email to refresh
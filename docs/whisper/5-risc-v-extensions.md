---
project: whisper
github: https://github.com/tenstorrent/whisper
deepwiki: https://deepwiki.com/tenstorrent/whisper/5-risc-v-extensions
---

# RISC-V Extensions

Relevant source files
*   [InstEntry.cpp](https://github.com/tenstorrent/whisper/blob/733fd921/InstEntry.cpp)
*   [InstId.hpp](https://github.com/tenstorrent/whisper/blob/733fd921/InstId.hpp)
*   [VecRegs.cpp](https://github.com/tenstorrent/whisper/blob/733fd921/VecRegs.cpp)
*   [VecRegs.hpp](https://github.com/tenstorrent/whisper/blob/733fd921/VecRegs.hpp)
*   [hypervisor.cpp](https://github.com/tenstorrent/whisper/blob/733fd921/hypervisor.cpp)
*   [vector.cpp](https://github.com/tenstorrent/whisper/blob/733fd921/vector.cpp)

## Purpose and Scope

This page provides an overview of the RISC-V ISA extensions supported by Whisper and how they are implemented in the simulator architecture. It covers the extension taxonomy, instruction-to-extension mapping, and the mechanisms for enabling and checking extension support during instruction execution.

For detailed documentation on specific extension implementations:

*   Vector Extension (V) and related extensions: See [5.1](https://deepwiki.com/tenstorrent/whisper/5.1-vector-extension-(v))
*   Hypervisor Extension (H): See [5.2](https://deepwiki.com/tenstorrent/whisper/5.2-hypervisor-extension-(h))
*   Bit Manipulation and Cryptography extensions: See [5.3](https://deepwiki.com/tenstorrent/whisper/5.3-bit-manipulation-and-crypto-extensions)

For information on how instructions are decoded and executed, see [2.3](https://deepwiki.com/tenstorrent/whisper/2.3-instruction-set-architecture).

## Supported Extensions

Whisper supports a comprehensive set of RISC-V extensions, organized into several categories:

| Category | Extensions | Description |
| --- | --- | --- |
| **Base ISA** | RV32I, RV64I | 32-bit and 64-bit integer base instruction sets |
| **Integer Multiply/Divide** | M | Integer multiplication and division |
| **Atomic** | A | Atomic memory operations (LR/SC, AMO) |
| **Single-Precision Float** | F, Zfa | 32-bit floating-point operations |
| **Double-Precision Float** | D | 64-bit floating-point operations |
| **Half-Precision Float** | Zfh, Zfhmin, Zvfh, Zvfhmin | 16-bit floating-point operations |
| **Bit Manipulation** | Zba, Zbb, Zbc, Zbs, Zbkb, Zbkx | Address generation, basic bit ops, carry-less multiply, single-bit ops |
| **Compressed** | C, Zcb, Zcmop | 16-bit compressed instructions |
| **Vector** | V, Zvfh, Zvfhmin, Zvfbfmin, Zvfbfwma | Vector operations with configurable element width and grouping |
| **Vector Crypto** | Zvbb, Zvbc, Zvkg, Zvkned, Zvknha, Zvknhb, Zvksed, Zvksh | Vector cryptographic instructions (AES, SHA, SM3/SM4, GCM) |
| **Vector Dot Product** | Zvqdot | Vector quaternary dot product |
| **Scalar Crypto** | Zkn*, Zks* | Scalar cryptographic instructions |
| **Hypervisor** | H, Svinval | Two-stage address translation, VS-mode, H-mode privileged instructions |
| **Control/Status** | Zicsr | CSR access instructions |
| **Cache Management** | Zicbom, Zicboz, Zicbop | Cache block operations (clean, flush, invalidate, zero, prefetch) |
| **Conditional** | Zicond | Conditional zero operations |
| **Fence** | Zifencei | Instruction fence |
| **Privileged** | Smrnmi, Smstateen, Sscofpmf | Privilege modes, interrupts, performance counters |
| **Atomics** | Zawrs, Zacas | Wait-on-reservation-set, compare-and-swap |
| **May-be-ops** | Zimop | May-be operations (future extension placeholders) |

Sources: [InstId.hpp 22-1106](https://github.com/tenstorrent/whisper/blob/733fd921/InstId.hpp#L22-L1106)[InstEntry.cpp 44-332](https://github.com/tenstorrent/whisper/blob/733fd921/InstEntry.cpp#L44-L332)

## Extension Architecture

### Extension Definition in Instruction Table

**Instruction-to-Extension Mapping**

Each instruction in Whisper is defined by an `InstEntry` object that associates it with a specific RISC-V extension. The `InstTable` class maintains the complete instruction database and provides lookup facilities.

Sources: [InstEntry.cpp 20-42](https://github.com/tenstorrent/whisper/blob/733fd921/InstEntry.cpp#L20-L42)[InstEntry.cpp 44-77](https://github.com/tenstorrent/whisper/blob/733fd921/InstEntry.cpp#L44-L77)[InstEntry.cpp 387-406](https://github.com/tenstorrent/whisper/blob/733fd921/InstEntry.cpp#L387-L406)

### Extension Flags and Properties

The `InstEntry` class tracks several extension-related properties beyond the base extension type:

Sources: [InstEntry.cpp 40-41](https://github.com/tenstorrent/whisper/blob/733fd921/InstEntry.cpp#L40-L41)[InstEntry.cpp 49-77](https://github.com/tenstorrent/whisper/blob/733fd921/InstEntry.cpp#L49-L77)[InstEntry.cpp 82-180](https://github.com/tenstorrent/whisper/blob/733fd921/InstEntry.cpp#L82-L180)

## Extension Categories

### Base and Privileged Extensions

The base ISA (RV32I/RV64I) provides fundamental integer operations. Privileged extensions add machine-mode, supervisor-mode, and user-mode support with associated CSRs and trap handling.

**Key Instruction Examples:**

*   Base: `lui`, `auipc`, `jal`, `jalr`, `beq`, `bne`, `add`, `sub`, `and`, `or`, `xor`
*   Privileged: `ecall`, `ebreak`, `mret`, `sret`, `wfi`
*   CSR: `csrrw`, `csrrs`, `csrrc`, `csrrwi`, `csrrsi`, `csrrci`

Sources: [InstId.hpp 24-78](https://github.com/tenstorrent/whisper/blob/733fd921/InstId.hpp#L24-L78)[InstEntry.cpp 407-651](https://github.com/tenstorrent/whisper/blob/733fd921/InstEntry.cpp#L407-L651)

### Integer Extensions

**M Extension (Multiply/Divide):**

*   RV32M: `mul`, `mulh`, `mulhsu`, `mulhu`, `div`, `divu`, `rem`, `remu`
*   RV64M: `mulw`, `divw`, `divuw`, `remw`, `remuw`

**A Extension (Atomic):**

*   Load-Reserved/Store-Conditional: `lr.w`, `sc.w`, `lr.d`, `sc.d`
*   Atomic Memory Operations: `amoswap`, `amoadd`, `amoxor`, `amoand`, `amoor`, `amomin`, `amomax`, `amominu`, `amomaxu`

Sources: [InstId.hpp 93-134](https://github.com/tenstorrent/whisper/blob/733fd921/InstId.hpp#L93-L134)[InstEntry.cpp 172-180](https://github.com/tenstorrent/whisper/blob/733fd921/InstEntry.cpp#L172-L180)

### Floating-Point Extensions

**F Extension (Single-Precision):**

*   Load/Store: `flw`, `fsw`
*   Arithmetic: `fadd.s`, `fsub.s`, `fmul.s`, `fdiv.s`, `fsqrt.s`
*   Fused Multiply-Add: `fmadd.s`, `fmsub.s`, `fnmadd.s`, `fnmsub.s`
*   Comparison: `feq.s`, `flt.s`, `fle.s`
*   Conversion: `fcvt.w.s`, `fcvt.s.w`, `fcvt.l.s`, `fcvt.s.l`

**D Extension (Double-Precision):**

*   Similar instructions with `.d` suffix
*   Conversion between single and double: `fcvt.s.d`, `fcvt.d.s`

**Zfh Extension (Half-Precision):**

*   16-bit floating-point operations with `.h` suffix
*   Conversion: `fcvt.s.h`, `fcvt.h.s`, `fcvt.d.h`, `fcvt.h.d`

**Zfa Extension (Additional FP):**

*   FP immediate load: `fli.h`, `fli.s`, `fli.d`
*   Quiet comparisons: `fleq.h`, `fltq.h`
*   Min/Max: `fminm.h`, `fmaxm.h`
*   Rounding: `fround.h`, `froundnx.h`

Sources: [InstId.hpp 136-256](https://github.com/tenstorrent/whisper/blob/733fd921/InstId.hpp#L136-L256)[InstEntry.cpp 182-323](https://github.com/tenstorrent/whisper/blob/733fd921/InstEntry.cpp#L182-L323)

### Bit Manipulation Extensions

**Zba (Address Generation):**

*   `sh1add`, `sh2add`, `sh3add`: Shift and add for address calculation
*   `sh1add.uw`, `sh2add.uw`, `sh3add.uw`: RV64 variants with unsigned word extension
*   `add.uw`, `slli.uw`: Zero-extend and shift operations

**Zbb (Basic Bit Manipulation):**

*   Counting: `clz`, `ctz`, `cpop` (count leading/trailing zeros, population count)
*   Min/Max: `min`, `max`, `minu`, `maxu`
*   Rotation: `rol`, `ror`, `rori`
*   Sign extension: `sext.b`, `sext.h`
*   Logical: `andn`, `orn`, `xnor`
*   Byte operations: `orc.b`, `rev8`

**Zbc (Carry-less Multiplication):**

*   `clmul`, `clmulh`, `clmulr`: Carry-less multiply for cryptographic operations

**Zbs (Single-Bit Operations):**

*   `bset`, `bclr`, `binv`, `bext`: Set, clear, invert, extract single bits
*   Immediate variants: `bseti`, `bclri`, `binvi`, `bexti`

Sources: [InstId.hpp 308-368](https://github.com/tenstorrent/whisper/blob/733fd921/InstId.hpp#L308-L368)[InstEntry.cpp 40-41](https://github.com/tenstorrent/whisper/blob/733fd921/InstEntry.cpp#L40-L41)

### Vector Extension Hierarchy

**Vector Extension Structure:**

The vector extension is enabled via `Hart<URV>::enableVectorExtension()` which configures the vector register file, sets CSR masks, and marks vector status in `mstatus.VS`. Vector instructions require checking:

1.   Vector extension enabled (`isRvv()`)
2.   Valid `SEW` (Selected Element Width) and `LMUL` (group multiplier)
3.   `vtype.vill` not set (valid vector type)
4.   Vector status not `Off` in `mstatus.VS` or `vsstatus.VS`

Sources: [vector.cpp 126-134](https://github.com/tenstorrent/whisper/blob/733fd921/vector.cpp#L126-L134)[vector.cpp 160-200](https://github.com/tenstorrent/whisper/blob/733fd921/vector.cpp#L160-L200)[InstEntry.cpp 56-76](https://github.com/tenstorrent/whisper/blob/733fd921/InstEntry.cpp#L56-L76)

### Hypervisor Extension

The H extension adds support for virtualization with two-stage address translation and VS-mode (Virtual Supervisor mode):

**Key Features:**

*   Two-stage address translation (VS-stage and G-stage)
*   Hypervisor CSRs: `hstatus`, `hedeleg`, `hideleg`, `hgatp`, `htval`, `htinst`
*   VS-mode CSRs: `vsstatus`, `vsie`, `vsip`, `vstvec`, `vsscratch`, `vsepc`, `vscause`, `vstval`, `vsatp`
*   TLB management: `hfence.vvma`, `hfence.gvma`, `hinval.vvma`, `hinval.gvma`
*   Hypervisor load/store: `hlv.b`, `hlv.h`, `hlv.w`, `hlv.d`, `hsv.b`, `hsv.h`, `hsv.w`, `hsv.d`
*   Execute-as-load: `hlvx.hu`, `hlvx.wu`

Sources: [hypervisor.cpp 26-153](https://github.com/tenstorrent/whisper/blob/733fd921/hypervisor.cpp#L26-L153)[hypervisor.cpp 156-381](https://github.com/tenstorrent/whisper/blob/733fd921/hypervisor.cpp#L156-L381)[InstId.hpp 1029-1046](https://github.com/tenstorrent/whisper/blob/733fd921/InstId.hpp#L1029-L1046)

### Scalar Crypto Extensions

_\_Zkn\_ (NIST Cryptography):_*

*   AES: `aes32esi`, `aes32esmi`, `aes32dsi`, `aes32dsmi` (RV32), `aes64*` (RV64)
*   SHA-256: `sha256sig0`, `sha256sig1`, `sha256sum0`, `sha256sum1`
*   SHA-512: `sha512sig0`, `sha512sig1`, `sha512sum0`, `sha512sum1`

_\_Zks\_ (ShangMi Cryptography):_*

*   SM3: `sm3p0`, `sm3p1`
*   SM4: `sm4ed`, `sm4ks`

Sources: [InstId.hpp 967-996](https://github.com/tenstorrent/whisper/blob/733fd921/InstId.hpp#L967-L996)

## Extension Configuration and Checking

### Extension Enabling Flow

**Extension Checking Methods:**

The `Hart` class provides numerous inline methods for checking extension support:

*   `isRv32()`, `isRv64()`: Base ISA width
*   `isRvf()`, `isRvd()`: Floating-point extensions
*   `isRvv()`: Vector extension
*   `isRvh()`: Hypervisor extension
*   `isZba()`, `isZbb()`, `isZbc()`, `isZbs()`: Bit manipulation
*   `isZfh()`, `isZfhmin()`: Half-precision float
*   `isBitManipLegal()`: Combined bit-manip check
*   `isFpLegal()`, `isDpLegal()`: FP with status check
*   `isZvfhLegal()`: Vector half-precision with checks

These methods check the `extensionMask_` bitmap and, for FP/vector, also verify status fields in `mstatus`.

Sources: [vector.cpp 126-134](https://github.com/tenstorrent/whisper/blob/733fd921/vector.cpp#L126-L134)[vector.cpp 308-339](https://github.com/tenstorrent/whisper/blob/733fd921/vector.cpp#L308-L339)

### Vector Extension Configuration

The vector extension requires detailed configuration managed by the `VecRegs` class:

**Configuration Parameters:**

*   `bytesPerRegister`: VLEN/8 (e.g., 128 for VLEN=1024)
*   `minBytesPerElem`: Minimum SEW in bytes (typically 1)
*   `maxBytesPerElem`: Maximum SEW in bytes (typically 8)
*   Legal SEW/LMUL combinations stored in `legalConfigs_` matrix

**Vector Instruction Checking:**

Vector instructions invoke `Hart<URV>::checkVecIntInst()` or `checkVecFpInst()` which verify:

1.   `preVecExec()`: Extension enabled, `mstatus.VS != Off`
2.   `vecRegs_.legalConfig()`: SEW/LMUL combination is legal
3.   `vtype.vill == 0`: Vector type is valid
4.   Destination register `vd` does not overlap mask `v0` (for masked ops)
5.   Source registers do not overlap mask `v0` with different EEWs
6.   `vstart < vlmax`: Start index is in bounds

Sources: [vector.cpp 160-200](https://github.com/tenstorrent/whisper/blob/733fd921/vector.cpp#L160-L200)[vector.cpp 203-213](https://github.com/tenstorrent/whisper/blob/733fd921/vector.cpp#L203-L213)[VecRegs.cpp 59-194](https://github.com/tenstorrent/whisper/blob/733fd921/VecRegs.cpp#L59-L194)

### Hypervisor Extension Checking

Hypervisor instructions require careful privilege and mode checking:

**Permission Requirements:**

*   Not in VS-mode (would cause virtual instruction trap)
*   Not in U-mode (would cause illegal instruction trap)
*   For G-stage operations: `mstatus.TVM == 0` or in M-mode

**Example: hfence.vvma Flow**

1.   Check `isRvh()`: H extension enabled
2.   Check `!virtMode_`: Not in virtual mode → `virtualInst()` trap
3.   Check `privMode_ != User`: Not user mode → `illegalInst()` trap
4.   Invalidate VS-stage and stage-2 TLBs based on operands

Sources: [hypervisor.cpp 26-82](https://github.com/tenstorrent/whisper/blob/733fd921/hypervisor.cpp#L26-L82)[hypervisor.cpp 85-153](https://github.com/tenstorrent/whisper/blob/733fd921/hypervisor.cpp#L85-L153)

## Instruction Marking in InstTable

The `InstTable` constructor performs extensive post-processing to mark instruction properties:

**Marking Categories:**

| Property | Method | Examples |
| --- | --- | --- |
| Vector instructions | `setVector(true)` | All V, Zvfh, Zvbb, Zvk* instructions |
| Unsigned operations | `setIsUnsigned(true)` | `bltu`, `bgeu`, `sltu`, `mulhu`, `divu` |
| Load size | `setLoadSize(bytes)` | `lb`→1, `lh`→2, `lw`→4, `ld`→8 |
| Store size | `setStoreSize(bytes)` | `sb`→1, `sh`→2, `sw`→4, `sd`→8 |
| Conditional branch | `setConditionalBranch(true)` | `beq`, `bne`, `blt`, `bge`, `bltu`, `bgeu` |
| Branch to register | `setBranchToRegister(true)` | `jalr`, `c.jr`, `c.jalr` |
| Divide | `setIsDivide(true)` | `div`, `divu`, `rem`, `remu`, `divw`, `divuw` |
| FP rounding mode | `setHasRoundingMode(true)` | Most FP arithmetic instructions |
| Modifies FFLAGS | `setModifiesFflags(true/false)` | FP operations except moves/sign injection |
| Compressed RV32 | `setCompressedRv32(true)` | `c.flw`, `c.fsw`, `c.jal`, `c.flwsp`, `c.fswsp` |
| Compressed RV64 | `setCompressedRv64(true)` | `c.ld`, `c.sd`, `c.addiw`, `c.ldsp`, `c.sdsp` |
| Immediate shift | `setImmedShiftSize(n)` | `lui`→12, `auipc`→12, `beq`→1, `jal`→1 |

Sources: [InstEntry.cpp 49-332](https://github.com/tenstorrent/whisper/blob/733fd921/InstEntry.cpp#L49-L332)

## Extension Interaction Example

**Execution Flow for vadd.vv:**

1.   **Decode**: Lookup `vadd.vv` in `InstTable`, retrieve `InstEntry` with `ext_ = RvExtension::V`
2.   **Extension Check**: `Hart::checkVecIntInst()` calls `preVecExec()` to verify: 
    *   `isRvv()` returns true (extension enabled)
    *   `mstatus.VS != VecStatus::Off` (vector state accessible)

3.   **Configuration Check**: `VecRegs::legalConfig()` validates current `sew_` and `group_` combination
4.   **Operand Check**: Verify `vd` doesn't overlap `v0` for masked operations
5.   **Execute**: `Hart::execVadd_vv()` performs element-wise addition
6.   **Writeback**: `VecRegs::write()` updates destination register, marks `lastWrittenReg_`
7.   **Status Update**: `postVecSuccess()` clears `vstart`, marks `mstatus.VS = Dirty`

Sources: [vector.cpp 1196-1318](https://github.com/tenstorrent/whisper/blob/733fd921/vector.cpp#L1196-L1318)[vector.cpp 160-200](https://github.com/tenstorrent/whisper/blob/733fd921/vector.cpp#L160-L200)[vector.cpp 1051-1066](https://github.com/tenstorrent/whisper/blob/733fd921/vector.cpp#L1051-L1066)

* * *

This overview provides the foundation for understanding Whisper's extension architecture. For implementation details of specific extensions, refer to the corresponding sub-pages listed at the beginning of this document.

Dismiss
Refresh this wiki

Enter email to refresh
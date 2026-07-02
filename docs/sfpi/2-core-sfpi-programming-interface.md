---
project: sfpi
github: https://github.com/tenstorrent/sfpi
deepwiki: https://deepwiki.com/tenstorrent/sfpi/2-core-sfpi-programming-interface
---

# Core SFPI Programming Interface

Relevant source files
*   [include/lltt.h](https://github.com/tenstorrent/sfpi/blob/ed989b9d/include/lltt.h)
*   [include/sfpi.h](https://github.com/tenstorrent/sfpi/blob/ed989b9d/include/sfpi.h)
*   [include/sfpi_fp16.h](https://github.com/tenstorrent/sfpi/blob/ed989b9d/include/sfpi_fp16.h)

## Purpose and Scope

The Core SFPI Programming Interface provides the primary C++ API for programming the Tenstorrent SFPU (Special Function Processing Unit). This interface consists of high-level C++ wrapper classes defined in `sfpi.h` that abstract hardware intrinsics into intuitive vector types, operators, and control flow constructs. The API enables developers to write portable SFPU code using familiar C++ syntax that compiles to efficient SFPU instructions with zero runtime overhead.

This page documents the fundamental programming constructs available to SFPU kernel developers. For architecture-specific hardware intrinsics, see [Hardware Abstraction Layers](https://deepwiki.com/tenstorrent/sfpi/3-hardware-abstraction-layers). For higher-level math library functions built on this interface, see [Math and Vector Libraries](https://deepwiki.com/tenstorrent/sfpi/4-math-and-vector-libraries).

## Programming Model Overview

The SFPI programming model operates on 64-element vectors stored in hardware registers. The interface provides three layers of register abstractions with overloaded operators that map directly to SFPU instructions at compile time.

**Sources:**[include/sfpi.h 1-1225](https://github.com/tenstorrent/sfpi/blob/ed989b9d/include/sfpi.h#L1-L1225)

## Vector Type System

### Base Type Hierarchy

The SFPI type system is built on a class hierarchy that manages vector liveness, initialization state, and register allocation. All vector types inherit from `__vBase` which tracks whether a variable has been initialized and implements the assign-with-liveness semantics.

**Sources:**[include/sfpi.h 326-636](https://github.com/tenstorrent/sfpi/blob/ed989b9d/include/sfpi.h#L326-L636)

### Vector Type Construction and Assignment

Vector types can be constructed from immediate values, destination registers, constant registers, or other vector types. The implementation uses the `assign()` method which generates either `sfpassign_lv` (for initialized variables) or direct assignment (for uninitialized variables) to maintain proper liveness semantics.

| Construction Method | Description | Code Reference |
| --- | --- | --- |
| `vFloat(float f)` | Load immediate float via `loadf()` | [include/sfpi.h 355](https://github.com/tenstorrent/sfpi/blob/ed989b9d/include/sfpi.h#L355-L355) |
| `vFloat(__vDReg dreg)` | Load from destination register | [include/sfpi.h 352](https://github.com/tenstorrent/sfpi/blob/ed989b9d/include/sfpi.h#L352-L352) |
| `vFloat(__vConstFloat creg)` | Load from constant register via `sfpassignlreg` | [include/sfpi.h 353-1072](https://github.com/tenstorrent/sfpi/blob/ed989b9d/include/sfpi.h#L353-L1072) |
| `vFloat(s2vFloat16 f)` | Load FP16 value via `sfpxloadi` | [include/sfpi.h 354-843](https://github.com/tenstorrent/sfpi/blob/ed989b9d/include/sfpi.h#L354-L843) |
| `vInt(int32_t val)` | Load immediate via `loadsi()` | [include/sfpi.h 471](https://github.com/tenstorrent/sfpi/blob/ed989b9d/include/sfpi.h#L471-L471) |
| `vInt(__vDReg dreg)` | Load from destination register | [include/sfpi.h 465](https://github.com/tenstorrent/sfpi/blob/ed989b9d/include/sfpi.h#L465-L465) |
| `vInt(__vCond vc)` | Convert condition code via `sfpxcondi` | [include/sfpi.h 476-1084](https://github.com/tenstorrent/sfpi/blob/ed989b9d/include/sfpi.h#L476-L1084) |
| `vUInt(uint32_t val)` | Load immediate via `loadui()` | [include/sfpi.h 567](https://github.com/tenstorrent/sfpi/blob/ed989b9d/include/sfpi.h#L567-L567) |

**Sources:**[include/sfpi.h 344-843](https://github.com/tenstorrent/sfpi/blob/ed989b9d/include/sfpi.h#L344-L843)

### Arithmetic and Logical Operations

All vector types support standard C++ operators that map to SFPU instructions. The `vFloat` type provides floating-point arithmetic, while `vInt` and `vUInt` provide integer arithmetic and bitwise operations.

#### vFloat Operators

**Sources:**[include/sfpi.h 364-838](https://github.com/tenstorrent/sfpi/blob/ed989b9d/include/sfpi.h#L364-L838)

#### vInt/vUInt Operators

**Note:** Arithmetic right shift (`operator>>`) for `vInt` is only available on Blackhole architecture, as indicated by the `__riscv_xtttensixbh` conditional compilation at [include/sfpi.h 526-532](https://github.com/tenstorrent/sfpi/blob/ed989b9d/include/sfpi.h#L526-L532)

**Sources:**[include/sfpi.h 483-1065](https://github.com/tenstorrent/sfpi/blob/ed989b9d/include/sfpi.h#L483-L1065)

### Comparison Operations and Condition Codes

Comparison operators return `__vCond` objects that encode the condition code and operands. These objects can be combined with boolean operators (`&&`, `||`, `!`) and used in predicated execution constructs.

| Operator | Condition Code | Float Reference | Int Reference |
| --- | --- | --- | --- |
| `==` | `__vCondEQ` | [include/sfpi.h 379-381](https://github.com/tenstorrent/sfpi/blob/ed989b9d/include/sfpi.h#L379-L381) | [include/sfpi.h 535-1086](https://github.com/tenstorrent/sfpi/blob/ed989b9d/include/sfpi.h#L535-L1086) |
| `!=` | `__vCondNE` | [include/sfpi.h 382-384](https://github.com/tenstorrent/sfpi/blob/ed989b9d/include/sfpi.h#L382-L384) | [include/sfpi.h 536-1087](https://github.com/tenstorrent/sfpi/blob/ed989b9d/include/sfpi.h#L536-L1087) |
| `<` | `__vCondLT` | [include/sfpi.h 385-387](https://github.com/tenstorrent/sfpi/blob/ed989b9d/include/sfpi.h#L385-L387) | [include/sfpi.h 537-1088](https://github.com/tenstorrent/sfpi/blob/ed989b9d/include/sfpi.h#L537-L1088) |
| `<=` | `__vCondLTE` | [include/sfpi.h 388-390](https://github.com/tenstorrent/sfpi/blob/ed989b9d/include/sfpi.h#L388-L390) | [include/sfpi.h 538-1089](https://github.com/tenstorrent/sfpi/blob/ed989b9d/include/sfpi.h#L538-L1089) |
| `>` | `__vCondGT` | [include/sfpi.h 391-393](https://github.com/tenstorrent/sfpi/blob/ed989b9d/include/sfpi.h#L391-L393) | [include/sfpi.h 539-1090](https://github.com/tenstorrent/sfpi/blob/ed989b9d/include/sfpi.h#L539-L1090) |
| `>=` | `__vCondGTE` | [include/sfpi.h 394-396](https://github.com/tenstorrent/sfpi/blob/ed989b9d/include/sfpi.h#L394-L396) | [include/sfpi.h 540-1091](https://github.com/tenstorrent/sfpi/blob/ed989b9d/include/sfpi.h#L540-L1091) |

The `__vCond` class supports three types of comparisons:

1.   **Float scalar comparison**: `sfpxfcmps` instruction [include/sfpi.h 669-673](https://github.com/tenstorrent/sfpi/blob/ed989b9d/include/sfpi.h#L669-L673)
2.   **Float vector comparison**: `sfpxfcmpv` instruction [include/sfpi.h 675-676](https://github.com/tenstorrent/sfpi/blob/ed989b9d/include/sfpi.h#L675-L676)
3.   **Integer comparison**: `sfpxicmps` and `sfpxicmpv` instructions [include/sfpi.h 679-683](https://github.com/tenstorrent/sfpi/blob/ed989b9d/include/sfpi.h#L679-L683)

**Sources:**[include/sfpi.h 379-1119](https://github.com/tenstorrent/sfpi/blob/ed989b9d/include/sfpi.h#L379-L1119)

## Register Abstractions

### Destination Register File

The destination register file is modeled as a global `dst_reg` object that provides array-like access to 512 destination registers. Destination registers can be used directly in expressions or loaded into local registers for computation.

Destination register addressing uses `SFP_DESTREG_STRIDE` to compute register indices [include/sfpi.h 197](https://github.com/tenstorrent/sfpi/blob/ed989b9d/include/sfpi.h#L197-L197) The write pointer can be incremented with `dst_reg++` which generates a `ttincrwc` instruction [include/sfpi.h 248-250](https://github.com/tenstorrent/sfpi/blob/ed989b9d/include/sfpi.h#L248-L250)

**Sources:**[include/sfpi.h 167-1182](https://github.com/tenstorrent/sfpi/blob/ed989b9d/include/sfpi.h#L167-L1182)

### Local Register File

The SFPU has 8 local registers (LREGs 0-7) used for computation. These are typically accessed indirectly through vector type variables, but can be explicitly referenced via the `l_reg` object when needed.

**Note:** The `LRegs` enum is defined in architecture-specific header files (e.g., `wormhole/sfpi_imp.h`) with values 0-7 corresponding to the 8 hardware local registers.

**Sources:**[include/sfpi.h 254-1183](https://github.com/tenstorrent/sfpi/blob/ed989b9d/include/sfpi.h#L254-L1183)

### Constant Registers

Three predefined constant float registers are available for use in expressions without explicit loading:

| Constant | Value | Register Index | Declaration |
| --- | --- | --- | --- |
| `vConst0` | 0.0 | `CREG_IDX_0` | [include/sfpi.h 718](https://github.com/tenstorrent/sfpi/blob/ed989b9d/include/sfpi.h#L718-L718) |
| `vConst1` | 1.0 | `CREG_IDX_1` | [include/sfpi.h 719](https://github.com/tenstorrent/sfpi/blob/ed989b9d/include/sfpi.h#L719-L719) |
| `vConstNeg1` | -1.0 | `CREG_IDX_NEG_1` | [include/sfpi.h 720](https://github.com/tenstorrent/sfpi/blob/ed989b9d/include/sfpi.h#L720-L720) |

Constant registers implement arithmetic operators that first load the constant via `sfpassignlreg` then perform the operation [include/sfpi.h 1009-1017](https://github.com/tenstorrent/sfpi/blob/ed989b9d/include/sfpi.h#L1009-L1017)

**Sources:**[include/sfpi.h 272-1078](https://github.com/tenstorrent/sfpi/blob/ed989b9d/include/sfpi.h#L272-L1078)

## Control Flow and Predicated Execution

### Condition Code Stack Architecture

The SFPU maintains a condition code (CC) stack that enables nested predicated execution. The `__vCCCtrl` class manages this stack through push/pop operations coordinated with the `v_if`/`v_elseif`/`v_else`/`v_endif` macros.

**Sources:**[include/sfpi.h 639-1215](https://github.com/tenstorrent/sfpi/blob/ed989b9d/include/sfpi.h#L639-L1215)

### Predicated Execution Patterns

The macro-based control flow interface provides intuitive if/else semantics while managing the underlying CC stack:

`// Basic if/elsev_if (vector < threshold) {    // Executes only where condition is true} v_endif // If/elseif/else chainv_if (vector == 0.0f) {    // Handle zero} v_elseif (vector < 0.0f) {    // Handle negative} v_else {    // Handle positive} v_endif // Nested conditionsv_if (vector > 0.0f) {    v_if (vector > 1.0f) {        // Handle > 1    } v_else {        // Handle 0 < vector <= 1    } v_endif} v_endif`
**Macro Implementation Details:**

*   `v_if` creates a `__vCCCtrl` object in scope, marks the CC stack top, and evaluates the condition [include/sfpi.h 1188-1192](https://github.com/tenstorrent/sfpi/blob/ed989b9d/include/sfpi.h#L1188-L1192)
*   `v_elseif` complements the current condition, pushes CC stack, and evaluates new condition [include/sfpi.h 1194-1198](https://github.com/tenstorrent/sfpi/blob/ed989b9d/include/sfpi.h#L1194-L1198)
*   `v_else` complements the current condition via `sfpcompc`[include/sfpi.h 1200-1201](https://github.com/tenstorrent/sfpi/blob/ed989b9d/include/sfpi.h#L1200-L1201)
*   `v_endif` closes the scope, triggering `__vCCCtrl` destructor which pops all pushed CC states [include/sfpi.h 1152-1204](https://github.com/tenstorrent/sfpi/blob/ed989b9d/include/sfpi.h#L1152-L1204)

**Sources:**[include/sfpi.h 1122-1215](https://github.com/tenstorrent/sfpi/blob/ed989b9d/include/sfpi.h#L1122-L1215)

### Boolean Condition Composition

The `__vCond` class supports boolean operators to combine multiple conditions:

| Operator | SFPU Operation | Implementation |
| --- | --- | --- |
| `&&` (AND) | `sfpxbool` with `MOD1_AND` | [include/sfpi.h 689](https://github.com/tenstorrent/sfpi/blob/ed989b9d/include/sfpi.h#L689-L689) |
| `||` (OR) | `sfpxbool` with `MOD1_OR` | [include/sfpi.h 690](https://github.com/tenstorrent/sfpi/blob/ed989b9d/include/sfpi.h#L690-L690) |
| `!` (NOT) | `sfpxbool` with `MOD1_NOT` | [include/sfpi.h 691](https://github.com/tenstorrent/sfpi/blob/ed989b9d/include/sfpi.h#L691-L691) |

These operators construct a tree of boolean operations that is evaluated at compile time with zero runtime overhead. The implementation is limited to 3 levels of nesting due to the macro-based emission strategy [include/sfpi.h 74-78](https://github.com/tenstorrent/sfpi/blob/ed989b9d/include/sfpi.h#L74-L78)

**Example:**

`v_if ((a < 0.0f) || ((b > 1.0f) && (c == 0.0f))) {    // Complex condition} v_endif`
**Sources:**[include/sfpi.h 639-692](https://github.com/tenstorrent/sfpi/blob/ed989b9d/include/sfpi.h#L639-L692)

## FP16 Conversion System

### Dual Format Support

The SFPI system supports two 16-bit floating-point formats: FP16A (standard IEEE 754 with 5-bit exponent) and FP16B (brain float with 8-bit exponent). The `s2vFloat16` class hierarchy provides transparent conversion from FP32 to either format.

**Sources:**[include/sfpi_fp16.h 24-132](https://github.com/tenstorrent/sfpi/blob/ed989b9d/include/sfpi_fp16.h#L24-L132)

### Format Characteristics

| Format | Exponent Bits | Mantissa Bits | Conversion | Use Case |
| --- | --- | --- | --- | --- |
| FP16A | 5 | 10 | Full IEEE 754 with denorm handling | High precision requirements |
| FP16B | 8 | 7 | Upper 16 bits of FP32 (truncation) | Dynamic range preservation |

**FP16A Conversion:** Implements full IEEE 754 conversion with denormal support using bit manipulation [include/sfpi_fp16.h 90-118](https://github.com/tenstorrent/sfpi/blob/ed989b9d/include/sfpi_fp16.h#L90-L118) The algorithm handles edge cases including overflow to infinity and underflow to denormals.

**FP16B Conversion:** Simple right shift by 16 bits to extract upper half of FP32 [include/sfpi_fp16.h 128-130](https://github.com/tenstorrent/sfpi/blob/ed989b9d/include/sfpi_fp16.h#L128-L130) This preserves the exponent range of FP32 at the cost of reduced mantissa precision.

**Sources:**[include/sfpi_fp16.h 90-130](https://github.com/tenstorrent/sfpi/blob/ed989b9d/include/sfpi_fp16.h#L90-L130)

### Usage in Vector Operations

FP16 values can be loaded directly into `vFloat` vectors and used in comparisons:

`// Construct FP16 immediatess2vFloat16a fp16a_val(3.14f);  // FP16A formats2vFloat16b fp16b_val(2.71f);  // FP16B format (default) // Load into vectorvFloat v = fp16a_val;  // Uses sfpxloadi instruction // Use in comparisonsv_if (dst_reg[0] < fp16b_val) {    // Comparison with FP16 immediate} v_endif // Arithmetic with negationvFloat result = vector - fp16a_val.negate();`
The `negate()` method flips the sign bit without converting to FP32 [include/sfpi_fp16.h 43](https://github.com/tenstorrent/sfpi/blob/ed989b9d/include/sfpi_fp16.h#L43-L43)

**Sources:**[include/sfpi_fp16.h 1-132](https://github.com/tenstorrent/sfpi/blob/ed989b9d/include/sfpi_fp16.h#L1-L132)[include/sfpi.h 354-843](https://github.com/tenstorrent/sfpi/blob/ed989b9d/include/sfpi.h#L354-L843)

## Instruction Replay Interface

### Low-Level Instruction Recording

The `lltt` namespace provides a low-level interface for recording and replaying sequences of SFPU instructions. This enables loop unrolling and instruction-level optimization by capturing instruction sequences into a buffer and re-executing them without overhead.

**Sources:**[include/lltt.h 1-43](https://github.com/tenstorrent/sfpi/blob/ed989b9d/include/lltt.h#L1-L43)

### Record and Replay Operations

The instruction replay system supports three primary operations:

| Function | Parameters | Behavior | Reference |
| --- | --- | --- | --- |
| `record<NoExec>(start, len)` | start offset, length | Record without executing | [include/lltt.h 28](https://github.com/tenstorrent/sfpi/blob/ed989b9d/include/lltt.h#L28-L28) |
| `record<Exec>(start, len)` | start offset, length | Record and execute once | [include/lltt.h 28](https://github.com/tenstorrent/sfpi/blob/ed989b9d/include/lltt.h#L28-L28) |
| `replay(start, len)` | start offset, length | Replay recorded instructions | [include/lltt.h 31-33](https://github.com/tenstorrent/sfpi/blob/ed989b9d/include/lltt.h#L31-L33) |
| `replay_insn(start, len)` | start offset, length | Encode replay instruction | [include/lltt.h 35-39](https://github.com/tenstorrent/sfpi/blob/ed989b9d/include/lltt.h#L35-L39) |

**Implementation Notes:**

*   The `ExecBool` template parameter determines whether instructions execute during recording [include/lltt.h 23](https://github.com/tenstorrent/sfpi/blob/ed989b9d/include/lltt.h#L23-L23)
*   `record()` uses `__builtin_rvtt_ttreplay` with `RECORD=true`[include/lltt.h 28](https://github.com/tenstorrent/sfpi/blob/ed989b9d/include/lltt.h#L28-L28)
*   `replay()` uses `__builtin_rvtt_ttreplay` with `EXEC=false, RECORD=false`[include/lltt.h 32](https://github.com/tenstorrent/sfpi/blob/ed989b9d/include/lltt.h#L32-L32)
*   The instruction buffer pointer is marked with `[[gnu::rvtt_reg_ptr]]` attribute [include/lltt.h 21](https://github.com/tenstorrent/sfpi/blob/ed989b9d/include/lltt.h#L21-L21)

**Example Pattern:**

`// Record a sequencelltt::record<lltt::Exec>(0, 10);// ... SFPU operations to record ... // Replay the sequence multiple timesfor (int i = 0; i < iterations; i++) {    lltt::replay(0, 10);}`
**Sources:**[include/lltt.h 1-43](https://github.com/tenstorrent/sfpi/blob/ed989b9d/include/lltt.h#L1-L43)

### Instruction Buffer Management

The instruction buffer is a global volatile array that serves as the recording destination. The macro redefinition at [include/lltt.h 15-16](https://github.com/tenstorrent/sfpi/blob/ed989b9d/include/lltt.h#L15-L16) simplifies the builtin call by automatically providing the buffer pointer and setting unused parameters to zero.

The `replay_insn()` function encodes a replay instruction as a 32-bit value with the format:

*   Bits 24-31: Opcode (0x04)
*   Bits 14-23: Start offset
*   Bits 4-13: Length
*   Bits 0-3: Unused (0)

This encoding matches the hardware instruction format for direct emission [include/lltt.h 35-39](https://github.com/tenstorrent/sfpi/blob/ed989b9d/include/lltt.h#L35-L39)

**Sources:**[include/lltt.h 15-39](https://github.com/tenstorrent/sfpi/blob/ed989b9d/include/lltt.h#L15-L39)

## Liveness and Optimization Considerations

### Assignment Liveness Tracking

A critical feature of the SFPI wrapper is its hybrid wrapper/compiler approach to variable liveness. When a variable is assigned inside a predicated block, the previous value must remain "live" to handle the false path of the predicate. The `assign()` method implements this via the `sfpassign_lv` instruction:

`sfpi_inline void __vBase::assign(const __rvtt_vec_t in){    v = (initialized) ? __builtin_rvtt_sfpassign_lv(v, in) : in;    initialized = true;}`
This generates `sfpassign_lv` only when the variable has been previously written, propagating the old value for the false path of predicates [include/sfpi.h 785-789](https://github.com/tenstorrent/sfpi/blob/ed989b9d/include/sfpi.h#L785-L789)

**Sources:**[include/sfpi.h 89-789](https://github.com/tenstorrent/sfpi/blob/ed989b9d/include/sfpi.h#L89-L789)

### Compilation Requirements

The SFPI wrapper requires optimization to be enabled (`-O`) because:

1.   All wrapper classes must inline completely to eliminate runtime overhead
2.   Vector variables must never spill to stack (maintained through inlining)
3.   Boolean condition trees are expanded at compile time via macros
4.   Register allocation depends on dead code elimination

All methods are marked with `sfpi_inline` which expands to `__attribute__((always_inline)) inline`[include/sfpi.h 86](https://github.com/tenstorrent/sfpi/blob/ed989b9d/include/sfpi.h#L86-L86)

**Sources:**[include/sfpi.h 85-88](https://github.com/tenstorrent/sfpi/blob/ed989b9d/include/sfpi.h#L85-L88)

### Known Limitations

As documented in the header comments [include/sfpi.h 103-116](https://github.com/tenstorrent/sfpi/blob/ed989b9d/include/sfpi.h#L103-L116):

1.   **Assignment returns void**: Cannot be used in conditionals due to compiler's lack of predication awareness
2.   **Boolean depth limit**: `&&` and `||` operators limited to 3 levels of nesting due to macro expansion strategy
3.   **Explicit CC functions**: Functions like `add_cc`, `exexp_cc`, `lz_cc` exist to handle instructions within conditionals that need CC output
4.   **Complex conditionals**: The condition evaluation system is complex due to compile-time tree construction

**Sources:**[include/sfpi.h 103-116](https://github.com/tenstorrent/sfpi/blob/ed989b9d/include/sfpi.h#L103-L116)

## Architecture-Specific Conditionals

The core interface uses preprocessor macros to select architecture-specific implementations:

Key architectural differences:

*   **Blackhole** adds arithmetic right shift operators for `vInt`[include/sfpi.h 526-532](https://github.com/tenstorrent/sfpi/blob/ed989b9d/include/sfpi.h#L526-L532)
*   **Wormhole** uses `ADDR_MODE_NOINC = 3`[Diagram 5](https://github.com/tenstorrent/sfpi/blob/ed989b9d/Diagram%205)
*   **Blackhole** uses `ADDR_MODE_NOINC = 7` and adds operations like `sfparecip`, `sfpmul24`[Diagram 5](https://github.com/tenstorrent/sfpi/blob/ed989b9d/Diagram%205)

**Sources:**[include/sfpi.h 127-1224](https://github.com/tenstorrent/sfpi/blob/ed989b9d/include/sfpi.h#L127-L1224)[include/sfpi_fp16.h 15-19](https://github.com/tenstorrent/sfpi/blob/ed989b9d/include/sfpi_fp16.h#L15-L19)[include/lltt.h 11](https://github.com/tenstorrent/sfpi/blob/ed989b9d/include/lltt.h#L11-L11)

Dismiss
Refresh this wiki

Enter email to refresh
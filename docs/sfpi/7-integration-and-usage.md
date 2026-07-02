---
project: sfpi
github: https://github.com/tenstorrent/sfpi
deepwiki: https://deepwiki.com/tenstorrent/sfpi/7-integration-and-usage
---

# Integration and Usage

Relevant source files
*   [README.md](https://github.com/tenstorrent/sfpi/blob/ed989b9d/README.md?plain=1)
*   [include/sfpi.h](https://github.com/tenstorrent/sfpi/blob/ed989b9d/include/sfpi.h)
*   [scripts/riscv32-unknown-elf-run](https://github.com/tenstorrent/sfpi/blob/ed989b9d/scripts/riscv32-unknown-elf-run)

This page provides practical guidance for developers integrating SFPI into their projects and writing SFPU kernels. It covers the integration workflow with tt-metal, practical kernel development patterns, optimization techniques, and debugging strategies. For architecture-specific implementation details, refer to page 3 (Hardware Abstraction Layers), and for core SFPI interface details, see page 2 (Core SFPI Programming Interface).

## Integration Overview

SFPI is distributed as a toolchain package containing the RISC-V GCC compiler, binutils, and SFPI header files. Projects consume SFPI releases through package managers or direct downloads. The primary consumer is the tt-metal project, which uses CMake's FetchContent mechanism to automatically download and install the appropriate SFPI version.

Integration Workflow Diagram:

Sources: [README.md 1-41](https://github.com/tenstorrent/sfpi/blob/ed989b9d/README.md?plain=1#L1-L41)[README.md 129-161](https://github.com/tenstorrent/sfpi/blob/ed989b9d/README.md?plain=1#L129-L161)

## Integration with tt-metal

The tt-metal project is the primary consumer of SFPI and demonstrates the recommended integration pattern. This section describes how SFPI is consumed and provides guidance for other projects.

### Version Management

The tt-metal repository maintains an `sfpi-version.sh` script that defines the SFPI version and download location:

`# Example from tt_metal/sfpi-version.shSFPI_VERSION="1.2.3"SFPI_URL="https://github.com/tenstorrent/sfpi/releases/download/${SFPI_VERSION}/sfpi-${SFPI_VERSION}-x86_64_Linux.txz"SFPI_HASH="abc123def456..."`
This script is sourced by both the CMake build system and installation scripts to ensure version consistency across the toolchain.

Sources: [README.md 158-161](https://github.com/tenstorrent/sfpi/blob/ed989b9d/README.md?plain=1#L158-L161)

### CMake Integration

The CMake build system uses FetchContent to automatically download and extract SFPI:

CMake Integration Pattern:

Example CMake code pattern:

`include(FetchContent)FetchContent_Declare(    sfpi    URL ${SFPI_URL}    URL_HASH MD5=${SFPI_HASH}    SOURCE_DIR ${INSTALL_LOCATION})FetchContent_MakeAvailable(sfpi) # Set compiler pathsset(CMAKE_C_COMPILER ${INSTALL_LOCATION}/compiler/bin/riscv32-unknown-elf-gcc)set(CMAKE_CXX_COMPILER ${INSTALL_LOCATION}/compiler/bin/riscv32-unknown-elf-g++)`
Sources: [README.md 135-156](https://github.com/tenstorrent/sfpi/blob/ed989b9d/README.md?plain=1#L135-L156)

### Issue Tracking and Feedback

Issues related to SFPI should be filed in the tt-metal repository with an `sfpi` label. This centralizes issue tracking and creates a feedback loop back to SFPI development.

When reporting compiler errors or ICEs (Internal Compiler Errors), enable kernel map generation:

`export TT_METAL_KERNEL_MAP=1pytest ... |& tee bug.logcp bug.log ~/.cache/tt-metal-cachetar cf bug.tar -C ~/.cache tt-metal-cache`
This captures all necessary information for reproducing compilation issues, including compiler flags, source code, and intermediate files.

Sources: [README.md 23-37](https://github.com/tenstorrent/sfpi/blob/ed989b9d/README.md?plain=1#L23-L37)

### Build Cache

Compiled kernels and debugging information are stored in `~/.cache/tt-metal-cache`. When `TT_METAL_KERNEL_MAP=1` is set, additional map files are generated that show the relationship between source code and generated assembly.

Build Artifacts Flow:

Sources: [README.md 31-34](https://github.com/tenstorrent/sfpi/blob/ed989b9d/README.md?plain=1#L31-L34)

## Writing SFPU Kernels

SFPU kernels are written using the SFPI C++ API, which provides vector types and operations that map directly to SFPU hardware instructions. This section covers practical kernel development patterns.

### Basic Kernel Structure

A typical SFPU kernel operates on vectors stored in destination registers (`dst_reg`), performs computations using local registers, and writes results back to destination registers:

`#include <sfpi.h> void kernel_function() {    // Load input from destination register    sfpi::vFloat input = sfpi::dst_reg[0];        // Perform computation    sfpi::vFloat result = input * 2.0f + 1.0f;        // Write output to destination register    sfpi::dst_reg[1] = result;}`
### SFPI Vector Types

SFPI provides three primary vector types that operate on 64-element vectors:

| Type | Description | Common Operations |
| --- | --- | --- |
| `vFloat` | Floating-point vector | Arithmetic (`+`, `-`, `*`), comparisons, special functions |
| `vInt` | Signed integer vector | Arithmetic, bit operations (`&`, `|`, `^`), shifts (`<<`, `>>`), comparisons |
| `vUInt` | Unsigned integer vector | Arithmetic, bit operations, shifts, comparisons |

Vector Type Hierarchy:

Sources: [include/sfpi.h 326-397](https://github.com/tenstorrent/sfpi/blob/ed989b9d/include/sfpi.h#L326-L397)[include/sfpi.h 400-548](https://github.com/tenstorrent/sfpi/blob/ed989b9d/include/sfpi.h#L400-L548)[include/sfpi.h 551-636](https://github.com/tenstorrent/sfpi/blob/ed989b9d/include/sfpi.h#L551-L636)

### Register Management

SFPI operations use two types of registers:

1.   **Local Registers (LReg)**: 8 temporary registers used during computations (LReg0-LReg7)
2.   **Destination Registers (DReg)**: 512 addressable registers for input/output, accessed via `dst_reg[index]`

Register Management Flow:

Best practices for register management:

*   Minimize the number of live variables to avoid register spills
*   Reuse variables when possible
*   Keep variable scope as narrow as needed
*   Avoid storing SFPU vectors to memory (not supported)

Sources: [include/sfpi.h 240-251](https://github.com/tenstorrent/sfpi/blob/ed989b9d/include/sfpi.h#L240-L251)[include/sfpi.h 254-269](https://github.com/tenstorrent/sfpi/blob/ed989b9d/include/sfpi.h#L254-L269)

### Common Kernel Patterns

#### Arithmetic Operations

`void arithmetic_kernel() {    sfpi::vFloat a = sfpi::dst_reg[0];    sfpi::vFloat b = sfpi::dst_reg[1];        // Basic arithmetic    sfpi::vFloat sum = a + b;    sfpi::vFloat diff = a - b;    sfpi::vFloat prod = a * b;        // Multiply-add (fused operation)    sfpi::vFloat result = a * b + sum;        // Compound assignments    result += 1.0f;    result *= 2.0f;        sfpi::dst_reg[2] = result;}`
#### Bitwise Operations

`void bitwise_kernel() {    sfpi::vInt a = sfpi::dst_reg[0];    sfpi::vInt b = sfpi::dst_reg[1];        // Bitwise operations    sfpi::vInt and_result = a & b;    sfpi::vInt or_result = a | b;    sfpi::vInt xor_result = a ^ b;    sfpi::vInt not_result = ~a;        // Shift operations    sfpi::vInt left_shift = a << 2;    sfpi::vUInt right_shift = sfpi::vUInt(a) >> 2;        sfpi::dst_reg[2] = and_result;}`
Sources: [include/sfpi.h 364-376](https://github.com/tenstorrent/sfpi/blob/ed989b9d/include/sfpi.h#L364-L376)[include/sfpi.h 483-532](https://github.com/tenstorrent/sfpi/blob/ed989b9d/include/sfpi.h#L483-L532)

### Predicated Execution

SFPI provides efficient conditional execution through predication macros that map to hardware condition codes:

`void conditional_kernel() {    sfpi::vFloat a = sfpi::dst_reg[0];    sfpi::vFloat result;        v_if (a > 0.0f) {        // Executed when a > 0        result = a * 2.0f;    } v_elseif (a < -1.0f) {        // Executed when a <= 0 AND a < -1        result = a * -0.5f;    } v_else {        // Executed when neither condition is met        result = 0.0f;    }    v_endif        sfpi::dst_reg[1] = result;}`
Predication Mechanism:

Boolean operators can combine conditions:

`v_if ((a > 0.0f && a < 1.0f) || (b == 0.0f)) {    // Complex condition combining AND and OR}v_endif`
The macros `v_if`, `v_elseif`, `v_else`, and `v_endif` are defined in [include/sfpi.h 1188-1204](https://github.com/tenstorrent/sfpi/blob/ed989b9d/include/sfpi.h#L1188-L1204) and expand to `__vCCCtrl` class methods that manage the condition code stack.

Sources: [include/sfpi.h 54-70](https://github.com/tenstorrent/sfpi/blob/ed989b9d/include/sfpi.h#L54-L70)[include/sfpi.h 695-715](https://github.com/tenstorrent/sfpi/blob/ed989b9d/include/sfpi.h#L695-L715)[include/sfpi.h 1188-1215](https://github.com/tenstorrent/sfpi/blob/ed989b9d/include/sfpi.h#L1188-L1215)

### Special Functions and Math Library

The SFPI math library (page 4) provides functions for manipulating floating-point components and performing lookups:

`void special_functions_kernel() {    sfpi::vFloat a = sfpi::dst_reg[0];        // Extract components    sfpi::vInt exp = exexp(a);           // Extract exponent (biased)    sfpi::vInt exp_raw = exexp_nodebias(a);  // Extract exponent (unbiased)    sfpi::vInt man = exman(a);           // Extract mantissa        // Modify components    sfpi::vFloat modified = setexp(a, exp + 1);  // Increment exponent    modified = setman(modified, man);            // Set mantissa    modified = setsgn(modified, 0);              // Clear sign bit        // Lookup table operations (for functions like exp, log, etc.)    // These are used internally by higher-level math functions        sfpi::dst_reg[1] = modified;}`
Sources: [include/sfpi.h 1-117](https://github.com/tenstorrent/sfpi/blob/ed989b9d/include/sfpi.h#L1-L117)

### Complete Kernel Example

A more complex kernel demonstrating multiple SFPI features:

`#include <sfpi.h>using namespace sfpi; void complex_activation_kernel(unsigned int vector_count) {    for (unsigned int i = 0; i < vector_count; i++) {        vFloat input = dst_reg[i];                // Extract exponent to check magnitude        vInt exp = exexp_nodebias(input);                v_if (input > 0.0f && exp < 10) {            // Positive values in normal range            vFloat scaled = input * 2.0f;            dst_reg[i] = scaled + 0.5f;                    } v_elseif (input < 0.0f) {            // Negative values: use different activation            vFloat abs_input = abs(input);            dst_reg[i] = abs_input * 0.1f;                    } v_else {            // Large positive values: clamp            dst_reg[i] = 10.0f;        }        v_endif                // Advance to next destination register        dst_reg++;    }}`
This example demonstrates:

*   Loop over multiple vectors
*   Floating-point comparisons
*   Exponent extraction
*   Nested conditional logic
*   Destination register pointer arithmetic

Sources: [include/sfpi.h 1-117](https://github.com/tenstorrent/sfpi/blob/ed989b9d/include/sfpi.h#L1-L117)

## Optimization Techniques

Effective SFPU kernel optimization focuses on register management, operation fusion, and efficient use of hardware features. The SFPU has limited local registers (8 LRegs), so careful programming is essential to avoid register spills.

### Register Pressure Management

The most critical optimization is managing register pressure. Register spills (writing SFPU registers to memory) are not supported and will cause compilation errors:

Register Spill Problem:

Bad example (too many live variables):

`void bad_kernel() {    // BAD: 6 live variables simultaneously    vFloat a = dst_reg[0];    vFloat b = dst_reg[1];    vFloat c = dst_reg[2];    vFloat d = dst_reg[3];    vFloat e = dst_reg[4];    vFloat f = dst_reg[5];    vFloat result = a * b + c * d + e * f;  // May cause register spill    dst_reg[6] = result;}`
Good example (reuse variables):

`void good_kernel() {    // GOOD: Compute incrementally, reuse registers    vFloat result = dst_reg[0] * dst_reg[1];    result += dst_reg[2] * dst_reg[3];    result += dst_reg[4] * dst_reg[5];    dst_reg[6] = result;}`
### Operation Fusion

The SFPU supports multiply-add (MAD) operations that can execute in a single instruction. The compiler automatically fuses operations when possible:

`// These both compile to the same efficient codevFloat result1 = a * b + c;  // Explicit multiply-addvFloat result2 = a * b;result2 += c;                 // Fused by compiler`
### Minimize Temporary Variables

Each temporary variable consumes a local register. Reduce temporaries by:

1.   Reusing variables when their previous value is no longer needed
2.   Operating directly on `dst_reg` where possible
3.   Limiting variable scope

`// Less efficient: multiple temporariesvoid inefficient() {    vFloat temp1 = dst_reg[0] * 2.0f;    vFloat temp2 = temp1 + 1.0f;    vFloat temp3 = temp2 * 0.5f;    dst_reg[1] = temp3;} // More efficient: single variable reusevoid efficient() {    vFloat result = dst_reg[0] * 2.0f;    result += 1.0f;    result *= 0.5f;    dst_reg[1] = result;}`
### Use Predication Instead of Branching

Hardware predication is more efficient than control flow branches. Use `v_if`/`v_else`/`v_endif` macros rather than standard C++ conditionals:

`// Inefficient: C++ conditional (not supported anyway)// if (condition) { ... } else { ... }  // Won't work with SFPU types // Efficient: Hardware predicationv_if (a > 0.0f) {    result = a * 2.0f;} v_else {    result = 0.0f;}v_endif`
### Use Constants Efficiently

SFPI provides hardware constant registers (`vConst0`, `vConst1`, `vConstNeg1`) that don't consume local registers. Use these for common values:

`// Less efficient: load constant into LRegvFloat a = dst_reg[0];vFloat result = a * 1.0f;  // Loads 1.0 into an LReg // More efficient: use hardware constantvFloat a = dst_reg[0];vFloat result = a * vConst1;  // Uses constant register directly`
### Scope Management

Limit variable scope to reduce the number of simultaneously live registers:

`// Poor: all variables live for entire functionvoid poor_scope() {    vFloat a = dst_reg[0];    vFloat b = dst_reg[1];    vFloat c = dst_reg[2];        vFloat result = a + b;  // a, b, c, result all live    result *= c;    dst_reg[3] = result;} // Better: limit scope of temporariesvoid better_scope() {    vFloat result;    {        vFloat a = dst_reg[0];        vFloat b = dst_reg[1];        result = a + b;  // a, b go out of scope here    }    result *= dst_reg[2];  // Only result is live    dst_reg[3] = result;}`
### Loop Optimization

When processing multiple vectors, structure loops to minimize register usage:

`void optimized_loop(unsigned int count) {    for (unsigned int i = 0; i < count; i++) {        // Load, process, store within each iteration        // Variables go out of scope each iteration        vFloat input = dst_reg[i];        vFloat output = input * 2.0f + 1.0f;        dst_reg[i] = output;    }}`
Optimization Checklist:

| Technique | Impact | Implementation |
| --- | --- | --- |
| Reuse variables | High | Assign to existing variables instead of creating new ones |
| Limit variable scope | High | Use `{}` blocks to end lifetime early |
| Use hardware constants | Medium | Prefer `vConst0`, `vConst1` over loading immediates |
| Minimize temporaries | High | Operate directly on `dst_reg` or reuse variables |
| Incremental computation | High | Break complex expressions into sequential operations |
| Predication over branching | Medium | Use `v_if` macros instead of C++ conditionals |

Sources: [include/sfpi.h 86-101](https://github.com/tenstorrent/sfpi/blob/ed989b9d/include/sfpi.h#L86-L101)

## Common Pitfalls and Debugging

### Register Spilling

Register spills occur when the compiler tries to save SFPU registers to stack memory, which is not supported:

```
fatal error: cannot store sfpu register (register spill)
```

**Solution**: Reduce the number of live variables, break complex expressions into simpler steps, or reuse variables.

### Uninitialized Variables

Using uninitialized SFPI variables can lead to unpredictable results:

```
warning: '<anonymous>' is used uninitialized
```

**Solution**: Always initialize variables before use.

### Function Call Issues

SFPI vector types cannot be passed by value to functions:

```
error: invalid declaration for function 'argcall', sfpu types cannot be passed on the stack
```

**Solution**: Always use `sfpi_inline` for functions that work with SFPI types, or pass by reference.

### Predication Stack Errors

```
error: malformed program, sfpsetcc_v outside of pushc/popc
```

**Solution**: Always ensure v_if/v_endif are properly balanced, and don't manually use CC setting instructions outside the predication macros.

Sources: [tests/wormhole/sfpi/gold/warn.txt 1-76](https://github.com/tenstorrent/sfpi/blob/ed989b9d/tests/wormhole/sfpi/gold/warn.txt#L1-L76)[tests/wormhole/sfpi/gold/warn_spill.txt 1-11](https://github.com/tenstorrent/sfpi/blob/ed989b9d/tests/wormhole/sfpi/gold/warn_spill.txt#L1-L11)

## Advanced Usage Patterns

### Working with Constants

SFPI provides predefined constants that map to hardware constant registers:

`// Using built-in constantsvFloat a = dst_reg[0];a = a * vConst1;        // Multiply by constant 1.0a = a + vConst0;        // Add constant 0.0a = a - vConstNeg1;     // Subtract constant -1.0 // Setting programmable constants (architecture-specific)vConstFloatPrgm0 = 0.5f;dst_reg[0] = vConstFloatPrgm0;`
### Complex Kernel Example

`void stupid_example(unsigned int value) {    // Load and operate on destination registers    vFloat a = dst_reg[0] + 2.0F;        // This emits a load, move, mad    dst_reg[3] = a * -dst_reg[1] + vConst0 + 0.5F;        // Complex conditional with boolean operations    v_if ((a >= 1.0F && a < 8.0F) || (a >= 12.0F && a < 16.0F)) {        vInt b = exexp_nodebias(a);        v_if (b >= 0) {            dst_reg[6] = setexp(a, 127);        }        v_endif;    } v_elseif (a == s2vFloat16a(3.0F)) {        dst_reg[7] = abs(a);    } v_else {        vInt exp = lz(a) - 19;        exp = ~exp;        exp &= 0xAA;        dst_reg[8] = -setexp(a, exp);    }    v_endif;}`
Sources: [tests/wormhole/sfpi/coverage.cc 1143-1265](https://github.com/tenstorrent/sfpi/blob/ed989b9d/tests/wormhole/sfpi/coverage.cc#L1143-L1265)[tests/wormhole/sfpi/coverage.cc 1253-1263](https://github.com/tenstorrent/sfpi/blob/ed989b9d/tests/wormhole/sfpi/coverage.cc#L1253-L1263)[include/wormhole/sfpi_imp.h 16-24](https://github.com/tenstorrent/sfpi/blob/ed989b9d/include/wormhole/sfpi_imp.h#L16-L24)

## Architecture-Specific Considerations

Different Tenstorrent architectures (Wormhole, Blackhole) have slightly different SFPU capabilities and constraints. The SFPI adapts to these through architecture-specific implementations:

Notable architecture differences:

*   **Wormhole**: Has constants like `vConst0p8373`, `vConstFloatPrgm0`
*   **Blackhole**: Adds arithmetic right shift support (`>>` operator for vInt)
*   Both architectures have architecture-specific optimizations

Sources: [include/sfpi.h 122-141](https://github.com/tenstorrent/sfpi/blob/ed989b9d/include/sfpi.h#L122-L141)[include/wormhole/sfpi_imp.h 1-174](https://github.com/tenstorrent/sfpi/blob/ed989b9d/include/wormhole/sfpi_imp.h#L1-L174)

## Building and Testing

For information about building the SFPI toolchain and running tests, refer to the [Building the Toolchain](https://deepwiki.com/tenstorrent/sfpi/4-math-and-vector-libraries) and [Testing Framework](https://deepwiki.com/tenstorrent/sfpi/6-testing-and-validation) sections of the wiki.

Remember that SFPI code must be compiled with the Tenstorrent-enhanced RISC-V toolchain that includes support for SFPU instructions.

Dismiss
Refresh this wiki

Enter email to refresh
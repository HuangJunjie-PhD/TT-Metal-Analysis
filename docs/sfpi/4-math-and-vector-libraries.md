---
project: sfpi
github: https://github.com/tenstorrent/sfpi
deepwiki: https://deepwiki.com/tenstorrent/sfpi/4-math-and-vector-libraries
---

# Math and Vector Libraries

Relevant source files
*   [include/blackhole/sfpi_imp.h](https://github.com/tenstorrent/sfpi/blob/ed989b9d/include/blackhole/sfpi_imp.h)
*   [include/blackhole/sfpi_lib.h](https://github.com/tenstorrent/sfpi/blob/ed989b9d/include/blackhole/sfpi_lib.h)
*   [include/wormhole/sfpi_lib.h](https://github.com/tenstorrent/sfpi/blob/ed989b9d/include/wormhole/sfpi_lib.h)

The SFPI Math and Vector Libraries provide high-level mathematical and data manipulation operations for SFPU vector programming. Implemented in `sfpi_lib.h` across multiple architectures, these libraries abstract hardware-specific intrinsics into intuitive function calls that operate on SFPI vector types (`vFloat`, `vInt`, `vUInt`). The libraries are organized into three functional categories: mathematical operations (page 4.1), type conversions (page 4.2), and vector manipulation primitives (page 4.3).

## Library Organization and Purpose

The math and vector libraries serve as the functional interface layer between application code and hardware intrinsics. They encapsulate common computational patterns into reusable functions that maintain consistent interfaces across Wormhole, Blackhole, and Grayskull architectures, despite underlying hardware differences.

**SFPI Architecture Layers with Math Library Positioning**

Sources: [include/wormhole/sfpi_lib.h 1-301](https://github.com/tenstorrent/sfpi/blob/ed989b9d/include/wormhole/sfpi_lib.h#L1-L301)[include/blackhole/sfpi_lib.h 1-326](https://github.com/tenstorrent/sfpi/blob/ed989b9d/include/blackhole/sfpi_lib.h#L1-L326)

## Library Structure and Categories

The math and vector libraries are organized into three functional categories, each documented in detail in their respective sub-pages:

**Library Category Organization**

| Category | Page | Primary Functions | Use Cases |
| --- | --- | --- | --- |
| **Functional Math Operations** | 4.1 | `lut()`, `exexp()`, `setexp()`, `setman()`, `abs()`, `lz()`, `shft()` | Lookup tables, floating-point component manipulation, bit operations, mathematical functions |
| **Type Conversions and Casting** | 4.2 | `int32_to_float()`, `float_to_fp16a()`, `float_to_fp16b()`, `float_to_uint8()`, `reinterpret()` | Converting between int32/float/fp16/uint8 formats, rounding modes, descaling operations |
| **Vector Manipulation Primitives** | 4.3 | `subvec_transp()`, `vec_swap()`, `vec_min_max()`, `subvec_shflror1()`, `subvec_shflshr1()` | Transposing vectors, swapping data, finding min/max, shifting elements |

Sources: [include/wormhole/sfpi_lib.h 13-301](https://github.com/tenstorrent/sfpi/blob/ed989b9d/include/wormhole/sfpi_lib.h#L13-L301)[include/blackhole/sfpi_lib.h 13-326](https://github.com/tenstorrent/sfpi/blob/ed989b9d/include/blackhole/sfpi_lib.h#L13-L326)

### Functional Math Operations (Page 4.1)

The functional math category provides operations for mathematical computation and floating-point manipulation. Key function groups include:

*   **Lookup Tables**: `lut()`, `lut_sign()`, `lut2()` for efficient function approximation with configurable table formats (FP16/FP32, 3-entry/6-entry)
*   **Exponent/Mantissa Manipulation**: `exexp()`, `exman8()`, `exman9()`, `setexp()`, `setman()`, `addexp()` for direct floating-point component access
*   **Sign Operations**: `setsgn()` for manipulating sign bits across vector types
*   **Bit Operations**: `lz()`, `lz_nosgn()`, `abs()`, `shft()` for leading zero counting, absolute values, and bit shifting

See page 4.1 for detailed documentation of these operations.

Sources: [include/wormhole/sfpi_lib.h 18-181](https://github.com/tenstorrent/sfpi/blob/ed989b9d/include/wormhole/sfpi_lib.h#L18-L181)[include/blackhole/sfpi_lib.h 18-191](https://github.com/tenstorrent/sfpi/blob/ed989b9d/include/blackhole/sfpi_lib.h#L18-L191)

### Type Conversions and Casting (Page 4.2)

The type conversion category handles data format transformations with controlled rounding and scaling. Key conversion paths include:

*   **Integer to Float**: `int32_to_float()` with selectable rounding modes (RNS/RNE)
*   **Float to FP16**: `float_to_fp16a()`, `float_to_fp16b()` supporting dual fp16 formats
*   **Float to Integer**: `float_to_uint8()`, `float_to_int8()`, `float_to_uint16()`, `float_to_int16()`
*   **Integer Quantization**: `int32_to_uint8()`, `int32_to_int8()` with descaling support
*   **Type Reinterpretation**: `reinterpret<vType>()` for zero-cost type casting

See page 4.2 for detailed documentation of conversion functions and rounding modes.

Sources: [include/wormhole/sfpi_lib.h 189-272](https://github.com/tenstorrent/sfpi/blob/ed989b9d/include/wormhole/sfpi_lib.h#L189-L272)[include/blackhole/sfpi_lib.h 199-282](https://github.com/tenstorrent/sfpi/blob/ed989b9d/include/blackhole/sfpi_lib.h#L199-L282)

### Vector Manipulation Primitives (Page 4.3)

The vector manipulation category provides operations for reorganizing and transforming vector data layouts. Key operations include:

*   **Transposition**: `subvec_transp()` for transposing 4 sub-vectors
*   **Shuffling**: `subvec_shflror1()`, `subvec_shflshr1()` for rotating/shifting sub-vector elements
*   **Swapping**: `vec_swap()` for exchanging vector contents
*   **Min/Max**: `vec_min_max()` for computing element-wise minimum and maximum

See page 4.3 for detailed documentation of vector operations.

Sources: [include/wormhole/sfpi_lib.h 274-299](https://github.com/tenstorrent/sfpi/blob/ed989b9d/include/wormhole/sfpi_lib.h#L274-L299)[include/blackhole/sfpi_lib.h 284-309](https://github.com/tenstorrent/sfpi/blob/ed989b9d/include/blackhole/sfpi_lib.h#L284-L309)

## Platform-Specific Implementations

The math and vector libraries are implemented separately for each Tenstorrent architecture, with platform-specific optimizations and feature sets:

**Architecture Implementation Matrix**

Sources: [include/wormhole/sfpi_lib.h 1-301](https://github.com/tenstorrent/sfpi/blob/ed989b9d/include/wormhole/sfpi_lib.h#L1-L301)[include/blackhole/sfpi_lib.h 1-326](https://github.com/tenstorrent/sfpi/blob/ed989b9d/include/blackhole/sfpi_lib.h#L1-L326)[include/grayskull/sfpi_lib.h 1-302](https://github.com/tenstorrent/sfpi/blob/ed989b9d/include/grayskull/sfpi_lib.h#L1-L302)

### Blackhole Extensions

The Blackhole architecture provides additional functions not available on Wormhole or Grayskull:

| Function | Implementation | Purpose |
| --- | --- | --- |
| `rand()` | `__builtin_rvtt_sfpmov_config(SFPCONFIG_SRC_RAND)` | Generate random integer vectors |
| `approx_recip()` | `__builtin_rvtt_sfparecip(src, SFPARECIP_MOD1_RECIP)` | Fast reciprocal approximation |
| `approx_exp()` | `__builtin_rvtt_sfparecip(src, SFPARECIP_MOD1_EXP)` | Fast exponential approximation |

Sources: [include/blackhole/sfpi_lib.h 311-324](https://github.com/tenstorrent/sfpi/blob/ed989b9d/include/blackhole/sfpi_lib.h#L311-L324)

### Shift Operation Variants

Blackhole provides enhanced shift operations with arithmetic vs. logical modes, while Wormhole uses a simplified interface:

**Wormhole**: [include/wormhole/sfpi_lib.h 173-181](https://github.com/tenstorrent/sfpi/blob/ed989b9d/include/wormhole/sfpi_lib.h#L173-L181)

*   `shft(vUInt, vInt)` - logical shift by vector amount
*   `shft(vUInt, int)` - logical shift by immediate amount

**Blackhole**: [include/blackhole/sfpi_lib.h 173-191](https://github.com/tenstorrent/sfpi/blob/ed989b9d/include/blackhole/sfpi_lib.h#L173-L191)

*   `shft(vUInt, vInt)` - logical shift
*   `shft(vInt, vInt)` - arithmetic shift
*   `shft(vUInt, int)` - logical shift immediate
*   `shft(vInt, int)` - arithmetic shift immediate

Sources: [include/wormhole/sfpi_lib.h 173-181](https://github.com/tenstorrent/sfpi/blob/ed989b9d/include/wormhole/sfpi_lib.h#L173-L181)[include/blackhole/sfpi_lib.h 173-191](https://github.com/tenstorrent/sfpi/blob/ed989b9d/include/blackhole/sfpi_lib.h#L173-L191)

## Common Implementation Patterns

All library functions follow consistent implementation patterns across architectures:

**Function Implementation Flow**

Sources: [include/wormhole/sfpi_lib.h 18-299](https://github.com/tenstorrent/sfpi/blob/ed989b9d/include/wormhole/sfpi_lib.h#L18-L299)[include/blackhole/sfpi_lib.h 18-309](https://github.com/tenstorrent/sfpi/blob/ed989b9d/include/blackhole/sfpi_lib.h#L18-L309)

### Key Implementation Characteristics

1.   **Inline Functions**: All functions use `sfpi_inline` macro to ensure inlining for performance
2.   **Type Safety**: C++ templates and function overloading provide compile-time type checking
3.   **Hardware Abstraction**: Functions wrap `__builtin_rvtt_*` intrinsics defined in `sfpi_hw.h`
4.   **Mode Flags**: Hardware constants (e.g., `SFPLUT_MOD0_SGN_RETAIN`) configure operation behavior
5.   **Vector Type Compatibility**: Functions accept `vFloat`, `vInt`, `vUInt` and `__vBase` types

### Template-Based Polymorphism

Several functions use C++ templates to support multiple vector types:

**Example from setsgn()**: [include/wormhole/sfpi_lib.h 127-149](https://github.com/tenstorrent/sfpi/blob/ed989b9d/include/wormhole/sfpi_lib.h#L127-L149)

```
template <typename vType, typename std::enable_if_t<std::is_base_of<__vBase, vType>::value>* = nullptr>
sfpi_inline vType setsgn(const vType v, const int32_t sgn)

template <typename vTypeA, typename vTypeB, ...>
sfpi_inline vTypeA setsgn(const vTypeA v, const vTypeB sgn)
```

This pattern enables type-safe operations across `vFloat`, `vInt`, and `vUInt` types.

Sources: [include/wormhole/sfpi_lib.h 127-149](https://github.com/tenstorrent/sfpi/blob/ed989b9d/include/wormhole/sfpi_lib.h#L127-L149)[include/blackhole/sfpi_lib.h 127-149](https://github.com/tenstorrent/sfpi/blob/ed989b9d/include/blackhole/sfpi_lib.h#L127-L149)

## Integration with SFPI Architecture

The Functional Math Library integrates with the broader SFPI architecture as follows:

1.   It depends on the vector types (`vFloat`, `vInt`, `vUInt`) defined in the Core Interface Layer
2.   It leverages hardware-specific operations provided by the Hardware Abstraction Layer
3.   It exposes a consistent, architecture-independent API for higher-level code
4.   It provides the foundation for more complex algorithms and applications

This library is a crucial component in the SFPI stack, acting as the mathematical foundation upon which higher-level computational kernels and algorithms are built.

Sources: [include/wormhole/sfpi_lib.h 7-301](https://github.com/tenstorrent/sfpi/blob/ed989b9d/include/wormhole/sfpi_lib.h#L7-L301)[include/blackhole/sfpi_lib.h 7-326](https://github.com/tenstorrent/sfpi/blob/ed989b9d/include/blackhole/sfpi_lib.h#L7-L326)

Dismiss
Refresh this wiki

Enter email to refresh
---
project: sfpi
github: https://github.com/tenstorrent/sfpi
deepwiki: https://deepwiki.com/tenstorrent/sfpi/3-hardware-abstraction-layers
---

# Hardware Abstraction Layers

Relevant source files
*   [include/blackhole/sfpi_hw.h](https://github.com/tenstorrent/sfpi/blob/ed989b9d/include/blackhole/sfpi_hw.h)
*   [include/wormhole/sfpi_hw.h](https://github.com/tenstorrent/sfpi/blob/ed989b9d/include/wormhole/sfpi_hw.h)
*   [include/wormhole/sfpi_imp.h](https://github.com/tenstorrent/sfpi/blob/ed989b9d/include/wormhole/sfpi_imp.h)

This page documents the hardware abstraction layers that provide access to hardware intrinsics across different Tenstorrent architectures. The SFPI system uses a three-layer architecture per platform to isolate hardware-specific details while maintaining a consistent programming interface. This abstraction enables portable code across Wormhole, Blackhole, and Grayskull architectures while exposing platform-specific optimizations when needed.

## Hardware Abstraction Layer Architecture

The SFPI repository implements a three-layer abstraction for each supported architecture. This design separates hardware intrinsics, implementation details, and mathematical library functions, allowing developers to write code at the appropriate level of abstraction for their needs. </old_str>

<old_str>

## Hardware Intrinsics Layer

The hardware intrinsics layer (`<arch>/sfpi_hw.h`) provides the lowest-level abstraction, directly exposing SFPU hardware operations through GCC builtin functions. Each architecture defines its own set of intrinsics with the naming pattern `__builtin_rvtt_<arch>_<operation>`.

**Diagram: Hardware Intrinsics Organization**

### Hardware Constants and Modifiers

Each architecture defines hardware-specific constants that control instruction behavior:

| Constant Category | Examples | Purpose |
| --- | --- | --- |
| Register Configuration | `SFP_LREG_COUNT`, `SFP_DESTREG_STRIDE`, `SFP_DESTREG_MAX_SIZE` | Define register file dimensions |
| Load/Store Formats | `SFPLOAD_MOD0_FMT_*`, `SFPSTORE_MOD0_FMT_*` | Specify data format (FP16A/B, FP32, INT32) |
| Address Modes | `SFPLOAD_ADDR_MODE_NOINC`, `SFPSTORE_ADDR_MODE_NOINC` | Control address increment behavior |
| Arithmetic Modifiers | `SFPABS_MOD1_INT`, `SFPABS_MOD1_FLOAT`, `SFPDIVP2_MOD1_ADD` | Configure operation variants |
| Conditional Codes | `SFPXCMP_MOD1_CC_*`, `SFPENCC_MOD1_*` | Set condition code operations |
| LUT Modifiers | `SFPLUT_MOD0_SGN_UPDATE`, `SFPLUTFP32_MOD0_*` | Control lookup table behavior |
| Cast/Convert Modifiers | `SFPCAST_MOD1_*`, `SFPSTOCHRND_MOD1_*` | Specify type conversion modes |
| Constant Register Indices | `CREG_IDX_0`, `CREG_IDX_1`, `CREG_IDX_TILEID`, `CREG_IDX_PRGM0-3` | Access constant registers |

Sources: [include/wormhole/sfpi_hw.h 92-280](https://github.com/tenstorrent/sfpi/blob/ed989b9d/include/wormhole/sfpi_hw.h#L92-L280)[include/blackhole/sfpi_hw.h 96-304](https://github.com/tenstorrent/sfpi/blob/ed989b9d/include/blackhole/sfpi_hw.h#L96-L304)

## Implementation Layer

The implementation layer (`<arch>/sfpi_imp.h`) provides C++ class implementations that wrap hardware intrinsics with type-safe operations. This layer implements the core SFPI types (`vFloat`, `vInt`, `vUInt`) and their operators.

**Diagram: Implementation Layer Components**

### Key Implementation Components

The implementation layer defines:

1.   **Vector Type Constructors**: Initialize vectors from destination registers, immediate values, or other vectors
2.   **Operator Overloads**: Provide intuitive arithmetic, logical, and comparison operators
3.   **Register Access**: `__vDReg` class for accessing destination registers via `dst_reg[n]` syntax
4.   **Constant Registers**: `__vConstFloat` and `__vConstIntBase` for programmable constant values
5.   **Local Register Enumeration**: `LRegs` enum for referencing SFPU local registers

Sources: [include/wormhole/sfpi_imp.h 13-174](https://github.com/tenstorrent/sfpi/blob/ed989b9d/include/wormhole/sfpi_imp.h#L13-L174)[include/blackhole/sfpi_imp.h 13-198](https://github.com/tenstorrent/sfpi/blob/ed989b9d/include/blackhole/sfpi_imp.h#L13-L198)</old_str><new_str>**Diagram: Three-Layer Hardware Abstraction Architecture**

This architecture consists of three distinct layers per platform:

| Layer | Files | Purpose |
| --- | --- | --- |
| **Hardware Intrinsics** | `<arch>/sfpi_hw.h` | Hardware constants, instruction modifiers, and direct `__builtin_rvtt_*` intrinsic mappings |
| **Implementation Details** | `<arch>/sfpi_imp.h` | C++ class implementations for `vFloat`, `vInt`, `vUInt`, register definitions, operator overloads |
| **Math Library** | `<arch>/sfpi_lib.h` | High-level mathematical functions built on intrinsics (LUT, exponential, type conversions) |

Sources: [include/wormhole/sfpi_hw.h](https://github.com/tenstorrent/sfpi/blob/ed989b9d/include/wormhole/sfpi_hw.h)[include/blackhole/sfpi_hw.h](https://github.com/tenstorrent/sfpi/blob/ed989b9d/include/blackhole/sfpi_hw.h)[include/wormhole/sfpi_imp.h](https://github.com/tenstorrent/sfpi/blob/ed989b9d/include/wormhole/sfpi_imp.h)[include/blackhole/sfpi_imp.h](https://github.com/tenstorrent/sfpi/blob/ed989b9d/include/blackhole/sfpi_imp.h)

Sources: [include/sfpi.h 122-141](https://github.com/tenstorrent/sfpi/blob/ed989b9d/include/sfpi.h#L122-L141)[include/sfpi_fp16.h 15-19](https://github.com/tenstorrent/sfpi/blob/ed989b9d/include/sfpi_fp16.h#L15-L19)

## Architecture Detection and Selection

The SFPI codebase selects the appropriate architecture implementation at compile time through preprocessor directives. This ensures that only the relevant implementation is included, keeping binary size minimal and avoiding any runtime overhead for architecture detection.

The architecture detection process performs the following steps:

1.   Detect architecture from compiler flags (`__riscv_tt_wormhole`, `__riscv_tt_blackhole`)
2.   Set architecture definition macros (`ARCH_WORMHOLE`, `ARCH_BLACKHOLE`)
3.   Validate that exactly one architecture is selected
4.   Include the appropriate architecture-specific headers

Sources: [include/sfpi.h 122-141](https://github.com/tenstorrent/sfpi/blob/ed989b9d/include/sfpi.h#L122-L141)

## Common Interface Patterns

All architectures implement consistent interface patterns to maintain code portability:

**Diagram: Common Interface Patterns Across Architectures**

### Consistent Elements

| Component | Wormhole | Blackhole | Grayskull | Notes |
| --- | --- | --- | --- | --- |
| `LRegs` enum | 8 registers (LReg0-7) | 8 registers (LReg0-7) | 8 registers (LReg0-7) | Identical across all architectures |
| `vConst0`, `vConst1` | Defined via CREG_IDX | Defined via CREG_IDX | Defined via CREG_IDX | Same constant register mapping |
| `vConstFloatPrgm0-2` | 3 programmable constants | 3 programmable constants | 3 programmable constants | PRGM1, PRGM2, PRGM3 indices |
| `dst_reg[n]` syntax | Supported | Supported | Supported | Uniform destination register access |
| Vector operators | Standard set | Standard set + arithmetic shift | Standard set | Blackhole adds `>>` (arithmetic) |

Sources: [include/wormhole/sfpi_imp.h 16-24](https://github.com/tenstorrent/sfpi/blob/ed989b9d/include/wormhole/sfpi_imp.h#L16-L24)[include/wormhole/sfpi_imp.h 162-172](https://github.com/tenstorrent/sfpi/blob/ed989b9d/include/wormhole/sfpi_imp.h#L162-L172)[include/blackhole/sfpi_imp.h 16-24](https://github.com/tenstorrent/sfpi/blob/ed989b9d/include/blackhole/sfpi_imp.h#L16-L24)[include/blackhole/sfpi_imp.h 186-196](https://github.com/tenstorrent/sfpi/blob/ed989b9d/include/blackhole/sfpi_imp.h#L186-L196)

## Architecture-Specific Capabilities

Each Tenstorrent architecture provides different hardware capabilities reflected in its abstraction layer implementation. Understanding these differences helps developers write optimized code for specific platforms.

**Diagram: Architecture Capability Comparison**

### Key Architectural Differences

| Feature | Wormhole | Blackhole | Grayskull | Details |
| --- | --- | --- | --- | --- |
| **Intrinsic Count** | 51 operations | 54 operations | Standard set | Blackhole adds `sfparecip`, `sfpmul24`, `sfpmov_config` |
| **Address Mode** | `NOINC = 3` | `NOINC = 7` | Architecture-specific | Controls memory access patterns |
| **Arithmetic Shifts** | Not supported | `operator>>` with `SFPSHFT_MOD1_ARITHMETIC` | N/A | Blackhole: [include/blackhole/sfpi_imp.h 167-172](https://github.com/tenstorrent/sfpi/blob/ed989b9d/include/blackhole/sfpi_imp.h#L167-L172) |
| **Reciprocal** | Manual implementation | `sfparecip` intrinsic | N/A | Blackhole: [include/blackhole/sfpi_hw.h 93](https://github.com/tenstorrent/sfpi/blob/ed989b9d/include/blackhole/sfpi_hw.h#L93-L93) |
| **24-bit Multiply** | Standard `sfpmul` | `sfpmul24` with modifier support | N/A | Blackhole: [include/blackhole/sfpi_hw.h 94](https://github.com/tenstorrent/sfpi/blob/ed989b9d/include/blackhole/sfpi_hw.h#L94-L94) |
| **Subtraction** | MAD with `-1` constant | Addition with negated operand | N/A | Wormhole: [include/wormhole/sfpi_imp.h 90-95](https://github.com/tenstorrent/sfpi/blob/ed989b9d/include/wormhole/sfpi_imp.h#L90-L95) Blackhole: [include/blackhole/sfpi_imp.h 90-94](https://github.com/tenstorrent/sfpi/blob/ed989b9d/include/blackhole/sfpi_imp.h#L90-L94) |
| **Random Number** | Not available | `SFPCONFIG_SRC_RAND = 9` | N/A | Blackhole: [include/blackhole/sfpi_hw.h 276](https://github.com/tenstorrent/sfpi/blob/ed989b9d/include/blackhole/sfpi_hw.h#L276-L276) |
| **Cast Modes** | 2 modes (RNE, RNS) | 4 modes (+ SM32 conversions) | N/A | Blackhole: [include/blackhole/sfpi_hw.h 230-238](https://github.com/tenstorrent/sfpi/blob/ed989b9d/include/blackhole/sfpi_hw.h#L230-L238) |
| **Shift Modes** | Logical only | Logical + Arithmetic | N/A | Blackhole: [include/blackhole/sfpi_hw.h 253-256](https://github.com/tenstorrent/sfpi/blob/ed989b9d/include/blackhole/sfpi_hw.h#L253-L256) |

### Platform-Specific Intrinsics

**Wormhole-Specific:**

*   All intrinsics use `__builtin_rvtt_wh_*` prefix
*   Subtraction implemented via `sfpmad(neg1, a, v)` pattern
*   Consistent with RISC-V ISA extensions for Wormhole

**Blackhole-Specific:**

*   All intrinsics use `__builtin_rvtt_bh_*` prefix
*   Additional intrinsics: 
    *   `__builtin_rvtt_bh_sfparecip(src, mod)` - Hardware reciprocal approximation
    *   `__builtin_rvtt_bh_sfpmul24(a, b, mod)` - 24-bit multiplication
    *   `__builtin_rvtt_bh_sfpmov_config(src)` - Move to configuration register

*   Enhanced `sfpshft_i/v` with arithmetic shift support via `SFPSHFT_MOD1_ARITHMETIC`

**Grayskull:**

*   Uses `__builtin_rvtt_gs_*` prefix (implied by architecture detection)
*   Standard SFPU feature set

Sources: [include/wormhole/sfpi_hw.h](https://github.com/tenstorrent/sfpi/blob/ed989b9d/include/wormhole/sfpi_hw.h)[include/blackhole/sfpi_hw.h](https://github.com/tenstorrent/sfpi/blob/ed989b9d/include/blackhole/sfpi_hw.h)[include/wormhole/sfpi_imp.h 90-95](https://github.com/tenstorrent/sfpi/blob/ed989b9d/include/wormhole/sfpi_imp.h#L90-L95)[include/blackhole/sfpi_imp.h 90-94](https://github.com/tenstorrent/sfpi/blob/ed989b9d/include/blackhole/sfpi_imp.h#L90-L94)[include/blackhole/sfpi_imp.h 167-172](https://github.com/tenstorrent/sfpi/blob/ed989b9d/include/blackhole/sfpi_imp.h#L167-L172)

## Wormhole Implementation

The Wormhole architecture implementation defines a set of hardware-specific constants, builtin function mappings, and optimized implementations for key operations.

### Key Elements of Wormhole Implementation

1.   **Hardware Constants**: Defined in `wormhole/sfpi_hw.h`, including register sizes, instruction modifiers, and more.
2.   **Builtin Mappings**: Maps generic builtin function names to Wormhole-specific versions with appropriate parameters.
3.   **Operation Implementations**: Implementations of arithmetic, logical, and other operations specific to Wormhole hardware.

For example, the subtraction operation in Wormhole is implemented using the multiply-add instruction with a negative constant:

```
vFloat vFloat::operator-=(const vFloat a)
{
    __rvtt_vec_t neg1 = __builtin_rvtt_sfpassignlreg(vConstNeg1.get());
    assign(__builtin_rvtt_sfpmad(neg1, a.get(), v, SFPMAD_MOD1_OFFSET_NONE));
    return v;
}
```

Sources: [include/wormhole/sfpi_imp.h 90-95](https://github.com/tenstorrent/sfpi/blob/ed989b9d/include/wormhole/sfpi_imp.h#L90-L95)

## Blackhole Implementation

The Blackhole architecture builds upon the Wormhole implementation but includes several key differences and enhancements.

### Key Elements of Blackhole Implementation

1.   **Hardware Constants**: Similar structure to Wormhole but with Blackhole-specific values.
2.   **Arithmetic Shift Support**: Blackhole adds support for arithmetic right shifts which were not available in Wormhole.
3.   **Implementation Differences**: Certain operations are implemented differently to leverage Blackhole-specific features.

For example, the subtraction operation in Blackhole uses a different implementation approach:

```
vFloat vFloat::operator-=(const vFloat a)
{
  operator+= (-a);
  return v;
}
```

Additionally, Blackhole adds arithmetic shift support:

```
vInt vInt::operator>>(unsigned amt) const {
  return __builtin_rvtt_sfpshft_i(get(), -amt, SFPSHFT_MOD1_ARITHMETIC);
}
vInt vInt::operator>>(vInt amt) const {
  return __builtin_rvtt_sfpshft_v(get(), (-amt).get(), SFPSHFT_MOD1_ARITHMETIC);
}
```

Sources: [include/blackhole/sfpi_imp.h 90-94](https://github.com/tenstorrent/sfpi/blob/ed989b9d/include/blackhole/sfpi_imp.h#L90-L94)[include/blackhole/sfpi_imp.h 167-172](https://github.com/tenstorrent/sfpi/blob/ed989b9d/include/blackhole/sfpi_imp.h#L167-L172)

## Architecture Implementation Differences

The table below highlights some of the key differences between the Wormhole and Blackhole implementations:

| Feature | Wormhole | Blackhole |
| --- | --- | --- |
| Arithmetic Shifts | Not supported | Supported via dedicated operators |
| Subtraction Implementation | Uses multiply-add with negative constant | Uses addition with negated value |
| Header Structure | Similar layout | Similar layout with additional features |
| Common Constants | Basic set of constants | Same set of basic constants |

This architecture-specific approach allows the SFPI to provide a consistent interface to developers while taking advantage of hardware-specific features and optimizations under the hood.

Sources: [include/wormhole/sfpi_imp.h 90-95](https://github.com/tenstorrent/sfpi/blob/ed989b9d/include/wormhole/sfpi_imp.h#L90-L95)[include/blackhole/sfpi_imp.h 90-94](https://github.com/tenstorrent/sfpi/blob/ed989b9d/include/blackhole/sfpi_imp.h#L90-L94)[include/blackhole/sfpi_imp.h 161-184](https://github.com/tenstorrent/sfpi/blob/ed989b9d/include/blackhole/sfpi_imp.h#L161-L184)

## Adding Support for New Architectures

To add support for a new architecture (e.g., a future Tenstorrent chip), the following steps would be required:

1.   Create architecture detection logic in `sfpi.h`
2.   Create the architecture-specific directories and files: 
    *   `include/<new_arch>/sfpi_hw.h`
    *   `include/<new_arch>/sfpi_imp.h`

3.   Implement all required constants, builtin mappings, and operation implementations
4.   Update any shared files like `sfpi_fp16.h` to include the new architecture headers

The modular design of the SFPI architecture implementation allows for relatively straightforward extension to new hardware platforms while maintaining backward compatibility with existing code.

Sources: [include/sfpi.h 122-141](https://github.com/tenstorrent/sfpi/blob/ed989b9d/include/sfpi.h#L122-L141)[include/sfpi_fp16.h 15-19](https://github.com/tenstorrent/sfpi/blob/ed989b9d/include/sfpi_fp16.h#L15-L19)

Dismiss
Refresh this wiki

Enter email to refresh
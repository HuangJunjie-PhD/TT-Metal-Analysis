---
project: sfpi-gcc
github: https://github.com/tenstorrent/sfpi-gcc
deepwiki: https://deepwiki.com/tenstorrent/sfpi-gcc/2-sfpu-extensions
---

# SFPU Extensions

Relevant source files
*   [gcc/common/config/riscv/riscv-common.cc](https://github.com/tenstorrent/sfpi-gcc/blob/763d6834/gcc/common/config/riscv/riscv-common.cc)
*   [gcc/config/riscv/constraints.md](https://github.com/tenstorrent/sfpi-gcc/blob/763d6834/gcc/config/riscv/constraints.md?plain=1)
*   [gcc/config/riscv/riscv-builtins.cc](https://github.com/tenstorrent/sfpi-gcc/blob/763d6834/gcc/config/riscv/riscv-builtins.cc)
*   [gcc/config/riscv/riscv-c.cc](https://github.com/tenstorrent/sfpi-gcc/blob/763d6834/gcc/config/riscv/riscv-c.cc)
*   [gcc/config/riscv/riscv-ftypes.def](https://github.com/tenstorrent/sfpi-gcc/blob/763d6834/gcc/config/riscv/riscv-ftypes.def)
*   [gcc/config/riscv/riscv-opts.h](https://github.com/tenstorrent/sfpi-gcc/blob/763d6834/gcc/config/riscv/riscv-opts.h)
*   [gcc/config/riscv/riscv.cc](https://github.com/tenstorrent/sfpi-gcc/blob/763d6834/gcc/config/riscv/riscv.cc)
*   [gcc/config/riscv/riscv.h](https://github.com/tenstorrent/sfpi-gcc/blob/763d6834/gcc/config/riscv/riscv.h)
*   [gcc/config/riscv/riscv.opt](https://github.com/tenstorrent/sfpi-gcc/blob/763d6834/gcc/config/riscv/riscv.opt)
*   [gcc/config/riscv/tt/gimple-rvtt-attrib.cc](https://github.com/tenstorrent/sfpi-gcc/blob/763d6834/gcc/config/riscv/tt/gimple-rvtt-attrib.cc)
*   [gcc/config/riscv/tt/gimple-rvtt-cc.cc](https://github.com/tenstorrent/sfpi-gcc/blob/763d6834/gcc/config/riscv/tt/gimple-rvtt-cc.cc)
*   [gcc/config/riscv/tt/gimple-rvtt-combine.cc](https://github.com/tenstorrent/sfpi-gcc/blob/763d6834/gcc/config/riscv/tt/gimple-rvtt-combine.cc)
*   [gcc/config/riscv/tt/gimple-rvtt-expand.cc](https://github.com/tenstorrent/sfpi-gcc/blob/763d6834/gcc/config/riscv/tt/gimple-rvtt-expand.cc)
*   [gcc/config/riscv/tt/gimple-rvtt-live.cc](https://github.com/tenstorrent/sfpi-gcc/blob/763d6834/gcc/config/riscv/tt/gimple-rvtt-live.cc)
*   [gcc/config/riscv/tt/gimple-rvtt-move.cc](https://github.com/tenstorrent/sfpi-gcc/blob/763d6834/gcc/config/riscv/tt/gimple-rvtt-move.cc)
*   [gcc/config/riscv/tt/gimple-rvtt-warn.cc](https://github.com/tenstorrent/sfpi-gcc/blob/763d6834/gcc/config/riscv/tt/gimple-rvtt-warn.cc)
*   [gcc/config/riscv/tt/rtl-rvtt-hll.cc](https://github.com/tenstorrent/sfpi-gcc/blob/763d6834/gcc/config/riscv/tt/rtl-rvtt-hll.cc)
*   [gcc/config/riscv/tt/rtl-rvtt-replay.cc](https://github.com/tenstorrent/sfpi-gcc/blob/763d6834/gcc/config/riscv/tt/rtl-rvtt-replay.cc)
*   [gcc/config/riscv/tt/rtl-rvtt-rmext.cc](https://github.com/tenstorrent/sfpi-gcc/blob/763d6834/gcc/config/riscv/tt/rtl-rvtt-rmext.cc)
*   [gcc/config/riscv/tt/rtl-rvtt-schedule.cc](https://github.com/tenstorrent/sfpi-gcc/blob/763d6834/gcc/config/riscv/tt/rtl-rvtt-schedule.cc)
*   [gcc/config/riscv/tt/rvtt-bh.cc](https://github.com/tenstorrent/sfpi-gcc/blob/763d6834/gcc/config/riscv/tt/rvtt-bh.cc)
*   [gcc/config/riscv/tt/rvtt-bh.md](https://github.com/tenstorrent/sfpi-gcc/blob/763d6834/gcc/config/riscv/tt/rvtt-bh.md?plain=1)
*   [gcc/config/riscv/tt/rvtt-constraints.md](https://github.com/tenstorrent/sfpi-gcc/blob/763d6834/gcc/config/riscv/tt/rvtt-constraints.md?plain=1)
*   [gcc/config/riscv/tt/rvtt-insn.h](https://github.com/tenstorrent/sfpi-gcc/blob/763d6834/gcc/config/riscv/tt/rvtt-insn.h)
*   [gcc/config/riscv/tt/rvtt-peephole-bh.md](https://github.com/tenstorrent/sfpi-gcc/blob/763d6834/gcc/config/riscv/tt/rvtt-peephole-bh.md?plain=1)
*   [gcc/config/riscv/tt/rvtt-peephole-wh.md](https://github.com/tenstorrent/sfpi-gcc/blob/763d6834/gcc/config/riscv/tt/rvtt-peephole-wh.md?plain=1)
*   [gcc/config/riscv/tt/rvtt-predicates.md](https://github.com/tenstorrent/sfpi-gcc/blob/763d6834/gcc/config/riscv/tt/rvtt-predicates.md?plain=1)
*   [gcc/config/riscv/tt/rvtt-protos.h](https://github.com/tenstorrent/sfpi-gcc/blob/763d6834/gcc/config/riscv/tt/rvtt-protos.h)
*   [gcc/config/riscv/tt/rvtt-wh.cc](https://github.com/tenstorrent/sfpi-gcc/blob/763d6834/gcc/config/riscv/tt/rvtt-wh.cc)
*   [gcc/config/riscv/tt/rvtt-wh.md](https://github.com/tenstorrent/sfpi-gcc/blob/763d6834/gcc/config/riscv/tt/rvtt-wh.md?plain=1)
*   [gcc/config/riscv/tt/rvtt.cc](https://github.com/tenstorrent/sfpi-gcc/blob/763d6834/gcc/config/riscv/tt/rvtt.cc)
*   [gcc/config/riscv/tt/rvtt.h](https://github.com/tenstorrent/sfpi-gcc/blob/763d6834/gcc/config/riscv/tt/rvtt.h)
*   [gcc/config/riscv/tt/rvtt.md](https://github.com/tenstorrent/sfpi-gcc/blob/763d6834/gcc/config/riscv/tt/rvtt.md?plain=1)
*   [gcc/testsuite/g++.target/tt/sfpi/fpsub-14604-bh.C](https://github.com/tenstorrent/sfpi-gcc/blob/763d6834/gcc/testsuite/g++.target/tt/sfpi/fpsub-14604-bh.C)
*   [gcc/testsuite/g++.target/tt/sfpi/fpsub-14604-wh.C](https://github.com/tenstorrent/sfpi-gcc/blob/763d6834/gcc/testsuite/g++.target/tt/sfpi/fpsub-14604-wh.C)
*   [gcc/testsuite/g++.target/tt/sfpi/muladd-14604-bh.C](https://github.com/tenstorrent/sfpi-gcc/blob/763d6834/gcc/testsuite/g++.target/tt/sfpi/muladd-14604-bh.C)
*   [gcc/testsuite/g++.target/tt/sfpi/muladd-14604-wh.C](https://github.com/tenstorrent/sfpi-gcc/blob/763d6834/gcc/testsuite/g++.target/tt/sfpi/muladd-14604-wh.C)
*   [gcc/testsuite/g++.target/tt/sfpi/velse-26365-bh.C](https://github.com/tenstorrent/sfpi-gcc/blob/763d6834/gcc/testsuite/g++.target/tt/sfpi/velse-26365-bh.C)

This document provides an overview of the Special Function Processing Unit (SFPU) extensions in the Tenstorrent GCC fork. These extensions enable the compiler to generate code for specialized mathematical acceleration hardware on RISC-V-based AI accelerator chips.

For detailed information about the base RISC-V SFPU implementation, see [RISC-V SFPU Core](https://deepwiki.com/tenstorrent/sfpi-gcc/2.1-risc-v-sfpu-core). For Tenstorrent-specific extensions including custom instructions and compiler passes, see [Tenstorrent SFPU Extensions](https://deepwiki.com/tenstorrent/sfpi-gcc/2.2-tenstorrent-sfpu-extensions).

## What is SFPU?

The Special Function Processing Unit (SFPU) is a custom hardware extension in Tenstorrent's AI accelerator architectures that provides specialized instructions for mathematical operations commonly used in neural network computation. The SFPU operates on vector registers (`V64SF` - 64-element single-precision floating-point vectors) and provides operations like:

*   Mathematical functions (exponential, logarithm, reciprocal)
*   Bitwise operations
*   Conditional execution
*   Load/store operations with special addressing modes
*   Stochastic rounding for quantization

The compiler support consists of:

*   1000+ intrinsic functions exposed as `__builtin_rvtt_*` functions
*   16 SFPU registers (L0-L15) separate from standard RISC-V registers
*   Custom GIMPLE and RTL optimization passes
*   Two hardware generations: Wormhole (WH) and Blackhole (BH)

Sources: [gcc/config/riscv/riscv-builtins.cc 252-273](https://github.com/tenstorrent/sfpi-gcc/blob/763d6834/gcc/config/riscv/riscv-builtins.cc#L252-L273)[gcc/config/riscv/riscv.h 385-389](https://github.com/tenstorrent/sfpi-gcc/blob/763d6834/gcc/config/riscv/riscv.h#L385-L389)[gcc/config/riscv/tt/rvtt.h 1-28](https://github.com/tenstorrent/sfpi-gcc/blob/763d6834/gcc/config/riscv/tt/rvtt.h#L1-L28)

## SFPU Extension Architecture

Sources: [gcc/config/riscv/riscv-builtins.cc 252-380](https://github.com/tenstorrent/sfpi-gcc/blob/763d6834/gcc/config/riscv/riscv-builtins.cc#L252-L380)[gcc/config/riscv/tt/rvtt.cc 86-291](https://github.com/tenstorrent/sfpi-gcc/blob/763d6834/gcc/config/riscv/tt/rvtt.cc#L86-L291)[gcc/config/riscv/tt/rvtt-insn.h 1-50](https://github.com/tenstorrent/sfpi-gcc/blob/763d6834/gcc/config/riscv/tt/rvtt-insn.h#L1-L50)

## Instruction Definition System

The SFPU instruction system uses a macro-based approach to define instructions that work across both hardware architectures while allowing architecture-specific variations.

### Instruction Metadata Structure

Each SFPU instruction is described by an `rvtt_insn_data` structure:

| Field | Type | Description |
| --- | --- | --- |
| `id` | `insn_id` | Enumeration identifier for the instruction |
| `name` | `const char*` | Instruction name (e.g., "sfpmul") |
| `decl` | `tree` | GCC tree node for the builtin function |
| `flags` | `unsigned` | Instruction properties (see below) |
| `dst_arg_pos` | `int` | Which argument is the destination (-1 if none) |
| `mod_pos` | `int` | Which argument contains modifier bits |
| `sched` | `unsigned` | Scheduling requirements |
| `nonimm_pos` | `int` | Position of non-immediate runtime value |
| `nonimm_mask` | `unsigned` | Bit mask for extracting non-immediate value |
| `nonimm_shft` | `int` | Bit shift for non-immediate value |

**Instruction Flags** (defined in [gcc/config/riscv/tt/rvtt.h 165-174](https://github.com/tenstorrent/sfpi-gcc/blob/763d6834/gcc/config/riscv/tt/rvtt.h#L165-L174)):

| Flag | Value | Meaning |
| --- | --- | --- |
| `INSN_FLAGS_SETCC` | 0x01 | Can set condition codes |
| `INSN_FLAGS_DSTISSRC` | 0x02 | Destination is also a source (for liveness) |
| `INSN_FLAGS_RISCV` | 0x04 | Standard RISC-V instruction |
| `INSN_FLAGS_EMPTY` | 0x08 | Internal/empty instruction |
| `INSN_FLAGS_CONST` | 0x20 | Constant (pure) function |
| `INSN_FLAGS_LREG` | 0x40 | Uses LREG (local register file) |

Sources: [gcc/config/riscv/tt/rvtt.h 76-174](https://github.com/tenstorrent/sfpi-gcc/blob/763d6834/gcc/config/riscv/tt/rvtt.h#L76-L174)[gcc/config/riscv/tt/rvtt.cc 90-117](https://github.com/tenstorrent/sfpi-gcc/blob/763d6834/gcc/config/riscv/tt/rvtt.cc#L90-L117)

### Instruction Definition Macros

Instructions are defined using macros in [gcc/config/riscv/tt/rvtt-insn.h](https://github.com/tenstorrent/sfpi-gcc/blob/763d6834/gcc/config/riscv/tt/rvtt-insn.h):

**Example instruction definitions** from [gcc/config/riscv/tt/rvtt-insn.h 120-127](https://github.com/tenstorrent/sfpi-gcc/blob/763d6834/gcc/config/riscv/tt/rvtt-insn.h#L120-L127):

```
RVTT_BUILTIN (synth_opcode, RISCV_USI_FTYPE_USI_USI, 0x20, -1, -1, 0x00, -1, 0, 0)
RVTT_WH_BUILTIN (sfpmul, RISCV_V64SF_FTYPE_V64SF_V64SF_USI, 0x00, -1, 2, 0x21, -1, 0, 0)
RVTT_BH_BUILTIN (sfpmul, RISCV_V64SF_FTYPE_V64SF_V64SF_USI, 0x00, -1, 3, 0x21, -1, 0, 0)
```

The macro arguments are: `(id, fmt, flags, dst_arg_pos, mod_pos, sched, nonimm_pos, nonimm_mask, nonimm_shft)`

Sources: [gcc/config/riscv/tt/rvtt-insn.h 28-90](https://github.com/tenstorrent/sfpi-gcc/blob/763d6834/gcc/config/riscv/tt/rvtt-insn.h#L28-L90)[gcc/config/riscv/tt/rvtt-insn.h 120-230](https://github.com/tenstorrent/sfpi-gcc/blob/763d6834/gcc/config/riscv/tt/rvtt-insn.h#L120-L230)

## Two-Architecture System

The compiler supports two distinct hardware generations with different instruction encodings but similar functionality:

### Wormhole (Generation 1)

*   Target flag: `TARGET_XTT_TENSIX_WH`
*   Architecture index: 0
*   Builtin name prefix: `__builtin_rvtt_wh_`
*   Machine description: [gcc/config/riscv/tt/rvtt-wh.md](https://github.com/tenstorrent/sfpi-gcc/blob/763d6834/gcc/config/riscv/tt/rvtt-wh.md?plain=1)
*   UNSPEC prefix: `UNSPECV_WH_*`

### Blackhole (Generation 2)

*   Target flag: `TARGET_XTT_TENSIX_BH`
*   Architecture index: 1
*   Builtin name prefix: `__builtin_rvtt_bh_`
*   Machine description: [gcc/config/riscv/tt/rvtt-bh.md](https://github.com/tenstorrent/sfpi-gcc/blob/763d6834/gcc/config/riscv/tt/rvtt-bh.md?plain=1)
*   UNSPEC prefix: `UNSPECV_BH_*`

The instruction tables are synchronized to ensure the same instruction IDs map to equivalent functionality across architectures (verified at initialization in [gcc/config/riscv/tt/rvtt.cc 268-276](https://github.com/tenstorrent/sfpi-gcc/blob/763d6834/gcc/config/riscv/tt/rvtt.cc#L268-L276)).

Sources: [gcc/config/riscv/tt/rvtt.cc 136-253](https://github.com/tenstorrent/sfpi-gcc/blob/763d6834/gcc/config/riscv/tt/rvtt.cc#L136-L253)[gcc/config/riscv/tt/rvtt-wh.md 1-50](https://github.com/tenstorrent/sfpi-gcc/blob/763d6834/gcc/config/riscv/tt/rvtt-wh.md?plain=1#L1-L50)[gcc/config/riscv/tt/rvtt-bh.md 1-50](https://github.com/tenstorrent/sfpi-gcc/blob/763d6834/gcc/config/riscv/tt/rvtt-bh.md?plain=1#L1-L50)

## Builtin Registration and Initialization

### Registration Process

**Key constants** from [gcc/config/riscv/riscv-builtins.cc 252](https://github.com/tenstorrent/sfpi-gcc/blob/763d6834/gcc/config/riscv/riscv-builtins.cc#L252-L252):

*   `first_sfpu_builtin = 343` (CMO=16 + crypto=50 + corev=187 + rocc=87 + 3)

Sources: [gcc/config/riscv/riscv-builtins.cc 356-380](https://github.com/tenstorrent/sfpi-gcc/blob/763d6834/gcc/config/riscv/riscv-builtins.cc#L356-L380)[gcc/config/riscv/tt/rvtt.cc 214-291](https://github.com/tenstorrent/sfpi-gcc/blob/763d6834/gcc/config/riscv/tt/rvtt.cc#L214-L291)

### Builtin Lookup System

The system provides multiple ways to look up instruction data:

| Function | Input | Output | Use Case |
| --- | --- | --- | --- |
| `rvtt_get_insn_data(insn_id)` | Enum ID | `rvtt_insn_data*` | Direct lookup by ID |
| `rvtt_get_insn_data(const char*)` | Name string | `rvtt_insn_data*` | Lookup by builtin name |
| `rvtt_get_insn_data(const gcall*)` | GIMPLE call | `rvtt_insn_data*` | Get from call statement |
| `rvtt_p(const rtx_insn*)` | RTL insn | `rvtt_insn_data*` | RTL instruction identification |

The lookup system uses an `std::unordered_map` (`insn_map`) for efficient name-based lookup ([gcc/config/riscv/tt/rvtt.cc 86](https://github.com/tenstorrent/sfpi-gcc/blob/763d6834/gcc/config/riscv/tt/rvtt.cc#L86-L86)).

Sources: [gcc/config/riscv/tt/rvtt.cc 299-367](https://github.com/tenstorrent/sfpi-gcc/blob/763d6834/gcc/config/riscv/tt/rvtt.cc#L299-L367)[gcc/config/riscv/tt/rvtt-protos.h 97-120](https://github.com/tenstorrent/sfpi-gcc/blob/763d6834/gcc/config/riscv/tt/rvtt-protos.h#L97-L120)

## Register Classes

The SFPU uses dedicated register classes separate from standard RISC-V registers:

| Register Class | Registers | Description |
| --- | --- | --- |
| `SFPU_REGS` | L0-L15 | All SFPU vector registers |
| `SFPU_REGS_L0` through `SFPU_REGS_L7` | Individual | Individual register classes for L0-L7 |
| `SFPU_STORE_REGS` | L0-L7 | Subset usable for store operations |
| `SFPU_USER_REG_NUM` | 8 | Number of user-accessible registers (L0-L7) |

**Register numbering** from [gcc/config/riscv/riscv.h 385-389](https://github.com/tenstorrent/sfpi-gcc/blob/763d6834/gcc/config/riscv/riscv.h#L385-L389):

*   `SFPU_REG_FIRST = 80`
*   `SFPU_REG_LAST = 95`
*   `SFPU_REG_NUM = 16`

The register classes are defined in [gcc/config/riscv/riscv.h 347-382](https://github.com/tenstorrent/sfpi-gcc/blob/763d6834/gcc/config/riscv/riscv.h#L347-L382) within the `riscv_regno_to_class[]` array.

**Register constraints** from [gcc/config/riscv/tt/rvtt-constraints.md 15-65](https://github.com/tenstorrent/sfpi-gcc/blob/763d6834/gcc/config/riscv/tt/rvtt-constraints.md?plain=1#L15-L65):

| Constraint | Description |
| --- | --- |
| `xr` | Any SFPU register |
| `xw` | Writable SFPU register (destination) |
| `xs` | Store-capable SFPU register (L0-L7) |
| `xn` | Special vec0 constant register |
| `x0`-`x7` | Specific SFPU registers L0-L7 |

Sources: [gcc/config/riscv/riscv.h 369-382](https://github.com/tenstorrent/sfpi-gcc/blob/763d6834/gcc/config/riscv/riscv.h#L369-L382)[gcc/config/riscv/riscv.h 385-416](https://github.com/tenstorrent/sfpi-gcc/blob/763d6834/gcc/config/riscv/riscv.h#L385-L416)[gcc/config/riscv/tt/rvtt-constraints.md 1-80](https://github.com/tenstorrent/sfpi-gcc/blob/763d6834/gcc/config/riscv/tt/rvtt-constraints.md?plain=1#L1-L80)

## Instruction Synthesis System

For instructions that require runtime-computed immediate values, the compiler uses a special synthesis mechanism to inject instruction encodings into the instruction stream.

**Key functions**:

*   `rvtt_sfpsynth_insn_dst()` - Synthesize instruction with destination ([gcc/config/riscv/tt/rvtt.cc 760-766](https://github.com/tenstorrent/sfpi-gcc/blob/763d6834/gcc/config/riscv/tt/rvtt.cc#L760-L766))
*   `rvtt_sfpsynth_insn()` - Synthesize instruction without destination ([gcc/config/riscv/tt/rvtt.cc 768-775](https://github.com/tenstorrent/sfpi-gcc/blob/763d6834/gcc/config/riscv/tt/rvtt.cc#L768-L775))
*   `rvtt_synth_insn_pattern()` - Generate assembly pattern with register fixups ([gcc/config/riscv/tt/rvtt.cc 530-593](https://github.com/tenstorrent/sfpi-gcc/blob/763d6834/gcc/config/riscv/tt/rvtt.cc#L530-L593))

The synthesis system handles cases where register allocation may assign different registers than initially encoded, adjusting the instruction encoding with XOR fixups at code emission time.

Sources: [gcc/config/riscv/tt/rvtt.cc 530-593](https://github.com/tenstorrent/sfpi-gcc/blob/763d6834/gcc/config/riscv/tt/rvtt.cc#L530-L593)[gcc/config/riscv/tt/rvtt.cc 760-784](https://github.com/tenstorrent/sfpi-gcc/blob/763d6834/gcc/config/riscv/tt/rvtt.cc#L760-L784)[gcc/config/riscv/tt/rvtt.md 69-154](https://github.com/tenstorrent/sfpi-gcc/blob/763d6834/gcc/config/riscv/tt/rvtt.md?plain=1#L69-L154)

## Function Type System

SFPU builtins use custom function type definitions that support vector and pointer types:

| Type Code | C Type | Description |
| --- | --- | --- |
| `V64SF` | `__attribute__((vector_size(256))) float` | 64-element vector |
| `USI` | `unsigned int` | 32-bit unsigned integer |
| `IPTR` | `volatile void*` | Instruction buffer pointer |
| `VOID_PTR` | `void*` | Generic pointer |

Function types are defined in [gcc/config/riscv/riscv-ftypes.def 67-158](https://github.com/tenstorrent/sfpi-gcc/blob/763d6834/gcc/config/riscv/riscv-ftypes.def#L67-L158) using the `DEF_RISCV_FTYPE` macro with argument count and type list. Examples:

*   `DEF_RISCV_FTYPE(2, (V64SF, V64SF, USI))` - Takes two vectors and an unsigned int, returns vector
*   `DEF_RISCV_FTYPE(6, (V64SF, IPTR, V64SF, USI, USI, USI, USI))` - Complex instruction with many arguments

The `v64SF_type_node` is constructed as a 64-element `V64SFmode` vector in [gcc/config/riscv/riscv-builtins.cc 345](https://github.com/tenstorrent/sfpi-gcc/blob/763d6834/gcc/config/riscv/riscv-builtins.cc#L345-L345)

Sources: [gcc/config/riscv/riscv-ftypes.def 67-158](https://github.com/tenstorrent/sfpi-gcc/blob/763d6834/gcc/config/riscv/riscv-ftypes.def#L67-L158)[gcc/config/riscv/riscv-builtins.cc 208-352](https://github.com/tenstorrent/sfpi-gcc/blob/763d6834/gcc/config/riscv/riscv-builtins.cc#L208-L352)

## Related Documentation

For implementation details of specific subsystems:

*   **Base RISC-V integration**: See [RISC-V SFPU Core](https://deepwiki.com/tenstorrent/sfpi-gcc/2.1-risc-v-sfpu-core) for standard RISC-V builtin system integration
*   **Tenstorrent extensions**: See [Tenstorrent SFPU Extensions](https://deepwiki.com/tenstorrent/sfpi-gcc/2.2-tenstorrent-sfpu-extensions) for custom instructions, GIMPLE/RTL passes, and architecture-specific optimizations
*   **Backend integration**: See [RISC-V Base Backend](https://deepwiki.com/tenstorrent/sfpi-gcc/6.4-risc-v-base-backend) for how SFPU integrates with the standard RISC-V target

Dismiss
Refresh this wiki

Enter email to refresh
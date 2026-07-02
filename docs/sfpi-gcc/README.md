---
project: sfpi-gcc
github: https://github.com/tenstorrent/sfpi-gcc
deepwiki: https://deepwiki.com/tenstorrent/sfpi-gcc
---

# Overview

 Relevant source files

* [ChangeLog](https://github.com/tenstorrent/sfpi-gcc/blob/763d6834/ChangeLog)
* [gcc/ChangeLog](https://github.com/tenstorrent/sfpi-gcc/blob/763d6834/gcc/ChangeLog)
* [gcc/DATESTAMP](https://github.com/tenstorrent/sfpi-gcc/blob/763d6834/gcc/DATESTAMP)
* [gcc/ada/ChangeLog](https://github.com/tenstorrent/sfpi-gcc/blob/763d6834/gcc/ada/ChangeLog)
* [gcc/analyzer/ChangeLog](https://github.com/tenstorrent/sfpi-gcc/blob/763d6834/gcc/analyzer/ChangeLog)
* [gcc/c-family/ChangeLog](https://github.com/tenstorrent/sfpi-gcc/blob/763d6834/gcc/c-family/ChangeLog)
* [gcc/c/ChangeLog](https://github.com/tenstorrent/sfpi-gcc/blob/763d6834/gcc/c/ChangeLog)
* [gcc/cp/ChangeLog](https://github.com/tenstorrent/sfpi-gcc/blob/763d6834/gcc/cp/ChangeLog)
* [gcc/d/ChangeLog](https://github.com/tenstorrent/sfpi-gcc/blob/763d6834/gcc/d/ChangeLog)
* [gcc/fortran/ChangeLog](https://github.com/tenstorrent/sfpi-gcc/blob/763d6834/gcc/fortran/ChangeLog)
* [gcc/testsuite/ChangeLog](https://github.com/tenstorrent/sfpi-gcc/blob/763d6834/gcc/testsuite/ChangeLog)
* [libgomp/ChangeLog](https://github.com/tenstorrent/sfpi-gcc/blob/763d6834/libgomp/ChangeLog)
* [libphobos/ChangeLog](https://github.com/tenstorrent/sfpi-gcc/blob/763d6834/libphobos/ChangeLog)
* [libstdc++-v3/ChangeLog](https://github.com/tenstorrent/sfpi-gcc/blob/763d6834/libstdc++-v3/ChangeLog)

## Purpose and Scope

This repository contains a fork of GCC (GNU Compiler Collection) created by Tenstorrent to add Special Function Processing Unit (SFPU) support for their AI accelerator hardware. The fork extends the RISC-V backend with over 1000 specialized instructions for mathematical operations on Tenstorrent's Wormhole and Blackhole architectures.

The primary additions to standard GCC are:

* RISC-V SFPU builtin functions and instruction patterns ([gcc/config/riscv/riscv-builtins.cc](https://github.com/tenstorrent/sfpi-gcc/blob/763d6834/gcc/config/riscv/riscv-builtins.cc) [gcc/config/riscv/riscv-ftypes.def](https://github.com/tenstorrent/sfpi-gcc/blob/763d6834/gcc/config/riscv/riscv-ftypes.def))
* Tenstorrent-specific SFPU extensions ([gcc/config/riscv/tt/](https://github.com/tenstorrent/sfpi-gcc/blob/763d6834/gcc/config/riscv/tt/))
* 8+ custom compiler passes for SFPU optimization
* Machine descriptions for Wormhole (`rvtt-wh.md`) and Blackhole (`rvtt-bh.md`) architectures

For detailed information about the SFPU extensions, see page 2. For standard GCC infrastructure components, see page 3.

Sources: [gcc/config/riscv/tt/rvtt.cc](https://github.com/tenstorrent/sfpi-gcc/blob/763d6834/gcc/config/riscv/tt/rvtt.cc) [gcc/config/riscv/tt/rvtt-bh.md](https://github.com/tenstorrent/sfpi-gcc/blob/763d6834/gcc/config/riscv/tt/rvtt-bh.md?plain=1) [gcc/config/riscv/tt/rvtt-wh.md](https://github.com/tenstorrent/sfpi-gcc/blob/763d6834/gcc/config/riscv/tt/rvtt-wh.md?plain=1)

## Compilation Pipeline

This GCC fork maintains the standard compilation pipeline with SFPU-specific extensions at multiple stages.

### Overall Compilation Flow

The following diagram shows how source code flows through the compiler, highlighting where SFPU extensions integrate:

Sources: [gcc/gimplify.c](https://github.com/tenstorrent/sfpi-gcc/blob/763d6834/gcc/gimplify.c) [gcc/expand.c](https://github.com/tenstorrent/sfpi-gcc/blob/763d6834/gcc/expand.c) [gcc/combine.c](https://github.com/tenstorrent/sfpi-gcc/blob/763d6834/gcc/combine.c) [gcc/config/riscv/riscv.cc](https://github.com/tenstorrent/sfpi-gcc/blob/763d6834/gcc/config/riscv/riscv.cc) [gcc/config/riscv/tt/rvtt.cc](https://github.com/tenstorrent/sfpi-gcc/blob/763d6834/gcc/config/riscv/tt/rvtt.cc)

### SFPU Extensions Architecture

The SFPU extensions are implemented through a layered architecture with RISC-V base support and Tenstorrent-specific additions:

Sources: [gcc/config/riscv/riscv-builtins.cc](https://github.com/tenstorrent/sfpi-gcc/blob/763d6834/gcc/config/riscv/riscv-builtins.cc) [gcc/config/riscv/riscv-ftypes.def](https://github.com/tenstorrent/sfpi-gcc/blob/763d6834/gcc/config/riscv/riscv-ftypes.def) [gcc/config/riscv/tt/rvtt.cc](https://github.com/tenstorrent/sfpi-gcc/blob/763d6834/gcc/config/riscv/tt/rvtt.cc) [gcc/config/riscv/tt/gimple-rvtt-attrib.cc](https://github.com/tenstorrent/sfpi-gcc/blob/763d6834/gcc/config/riscv/tt/gimple-rvtt-attrib.cc) [gcc/config/riscv/tt/rtl-rvtt-synth-opcode.cc](https://github.com/tenstorrent/sfpi-gcc/blob/763d6834/gcc/config/riscv/tt/rtl-rvtt-synth-opcode.cc) [gcc/config/riscv/tt/rvtt-wh.md](https://github.com/tenstorrent/sfpi-gcc/blob/763d6834/gcc/config/riscv/tt/rvtt-wh.md?plain=1) [gcc/config/riscv/tt/rvtt-bh.md](https://github.com/tenstorrent/sfpi-gcc/blob/763d6834/gcc/config/riscv/tt/rvtt-bh.md?plain=1)

## Major Components

### SFPU Extensions (Page 2)

The SFPU extensions are the primary addition to standard GCC, consisting of:

**RISC-V SFPU Core** ([gcc/config/riscv/](https://github.com/tenstorrent/sfpi-gcc/blob/763d6834/gcc/config/riscv/))

* Builtin function registration in `riscv-builtins.cc`
* Function type definitions in `riscv-ftypes.def`
* Base instruction patterns in `riscv.md`

**Tenstorrent SFPU Extensions** ([gcc/config/riscv/tt/](https://github.com/tenstorrent/sfpi-gcc/blob/763d6834/gcc/config/riscv/tt/))

* Over 1000 instruction definitions in `rvtt-insn.h`
* Builtin expansion in `rvtt.cc`
* Wormhole patterns in `rvtt-wh.md` (TARGET\_XTT\_TENSIX\_WH)
* Blackhole patterns in `rvtt-bh.md` (TARGET\_XTT\_TENSIX\_BH)

**SFPU Compiler Passes**

* GIMPLE level: `gimple-rvtt-attrib.cc`, `gimple-rvtt-expand.cc`, `gimple-rvtt-combine.cc`, `gimple-rvtt-cc.cc`, `gimple-rvtt-live.cc`, `gimple-rvtt-warn.cc`
* RTL level: `rtl-rvtt-synth-opcode.cc`, `rtl-rvtt-schedule.cc`, `rtl-rvtt-hll.cc`, `rtl-rvtt-replay.cc`

### GCC Infrastructure (Page 3)

Standard GCC components maintained in this fork:

| Component | Key Files | Description |
| --- | --- | --- |
| Build System | `Makefile.in`, `configure.ac` | Autoconf/automake configuration |
| Pass Manager | `passes.def`, `tree-pass.h` | Optimization pass sequencing |
| Options | `common.opt`, `opts.cc` | Command-line option processing |

### Language Support (Page 4)

This fork supports multiple language frontends:

| Language | Frontend Directory | Key Components |
| --- | --- | --- |
| C++ | `gcc/cp/` | `parser.cc`, `pt.cc` (templates), `constraint.cc` (concepts) |
| Ada | `gcc/ada/` | `sem_*.adb` (semantic analysis) |
| C | `gcc/c/` | `c-parser.c`, `c-decl.cc` |
| D | `gcc/d/` | `d-lang.cc` |
| Fortran | `gcc/fortran/` | `parse.cc` |

### Target Backends (Page 6)

While RISC-V is the primary target, other backends are maintained:

| Backend | Directory | SFPU Support |
| --- | --- | --- |
| RISC-V | `gcc/config/riscv/` | Full SFPU integration via `tt/` subdirectory |
| x86/x86\_64 | `gcc/config/i386/` | Standard GCC (SSE/AVX patterns in `sse.md`) |
| AArch64 | `gcc/config/aarch64/` | Standard GCC (NEON in `aarch64-simd.md`) |
| PowerPC | `gcc/config/rs6000/` | Standard GCC (AltiVec in `altivec.md`) |

## Build and Configuration

The SFPU extensions integrate into the standard GCC build system through:

* **Target Configuration**: SFPU availability controlled by `TARGET_RVTT_WH` and `TARGET_RVTT_BH` macros
* **Builtin Registration**: SFPU builtins registered through `riscv_builtin_avail_sfpu()` and related availability predicates
* **Machine Description**: SFPU patterns included via the standard `.md` file inclusion mechanism

## Integration with Standard GCC

sfpi-gcc maintains full backward compatibility with standard GCC while adding SFPU capabilities:

* **Command Line Interface**: Standard GCC options with additional SFPU-specific flags
* **Optimization Passes**: SFPU instructions participate in standard optimization passes including vectorization and instruction scheduling
* **Debug Support**: SFPU instructions supported by standard GCC debugging infrastructure

Sources: [gcc/common.opt](https://github.com/tenstorrent/sfpi-gcc/blob/763d6834/gcc/common.opt) [gcc/tree-pass.h](https://github.com/tenstorrent/sfpi-gcc/blob/763d6834/gcc/tree-pass.h) [gcc/config/riscv/constraints.md](https://github.com/tenstorrent/sfpi-gcc/blob/763d6834/gcc/config/riscv/constraints.md?plain=1)

Refresh this wiki
---
project: sfpi-gcc
github: https://github.com/tenstorrent/sfpi-gcc
deepwiki: https://deepwiki.com/tenstorrent/sfpi-gcc/3-gcc-infrastructure
---

# GCC Infrastructure

Relevant source files
*   [ChangeLog](https://github.com/tenstorrent/sfpi-gcc/blob/763d6834/ChangeLog)
*   [gcc/ChangeLog](https://github.com/tenstorrent/sfpi-gcc/blob/763d6834/gcc/ChangeLog)
*   [gcc/DATESTAMP](https://github.com/tenstorrent/sfpi-gcc/blob/763d6834/gcc/DATESTAMP)
*   [gcc/Makefile.in](https://github.com/tenstorrent/sfpi-gcc/blob/763d6834/gcc/Makefile.in)
*   [gcc/ada/ChangeLog](https://github.com/tenstorrent/sfpi-gcc/blob/763d6834/gcc/ada/ChangeLog)
*   [gcc/analyzer/ChangeLog](https://github.com/tenstorrent/sfpi-gcc/blob/763d6834/gcc/analyzer/ChangeLog)
*   [gcc/c-family/ChangeLog](https://github.com/tenstorrent/sfpi-gcc/blob/763d6834/gcc/c-family/ChangeLog)
*   [gcc/c/ChangeLog](https://github.com/tenstorrent/sfpi-gcc/blob/763d6834/gcc/c/ChangeLog)
*   [gcc/common.opt](https://github.com/tenstorrent/sfpi-gcc/blob/763d6834/gcc/common.opt)
*   [gcc/config/aarch64/aarch64-cores.def](https://github.com/tenstorrent/sfpi-gcc/blob/763d6834/gcc/config/aarch64/aarch64-cores.def)
*   [gcc/config/aarch64/aarch64-cost-tables.h](https://github.com/tenstorrent/sfpi-gcc/blob/763d6834/gcc/config/aarch64/aarch64-cost-tables.h)
*   [gcc/config/aarch64/aarch64-tune.md](https://github.com/tenstorrent/sfpi-gcc/blob/763d6834/gcc/config/aarch64/aarch64-tune.md?plain=1)
*   [gcc/config/aarch64/t-aarch64](https://github.com/tenstorrent/sfpi-gcc/blob/763d6834/gcc/config/aarch64/t-aarch64)
*   [gcc/config/arm/aarch-cost-tables.h](https://github.com/tenstorrent/sfpi-gcc/blob/763d6834/gcc/config/arm/aarch-cost-tables.h)
*   [gcc/config/riscv/tt/gimple-rvtt-synth-expand.cc](https://github.com/tenstorrent/sfpi-gcc/blob/763d6834/gcc/config/riscv/tt/gimple-rvtt-synth-expand.cc)
*   [gcc/config/riscv/tt/gimple-rvtt-synth-nullify.cc](https://github.com/tenstorrent/sfpi-gcc/blob/763d6834/gcc/config/riscv/tt/gimple-rvtt-synth-nullify.cc)
*   [gcc/config/riscv/tt/gimple-rvtt-synth-renumber.cc](https://github.com/tenstorrent/sfpi-gcc/blob/763d6834/gcc/config/riscv/tt/gimple-rvtt-synth-renumber.cc)
*   [gcc/config/riscv/tt/gimple-rvtt-synth-split.cc](https://github.com/tenstorrent/sfpi-gcc/blob/763d6834/gcc/config/riscv/tt/gimple-rvtt-synth-split.cc)
*   [gcc/config/riscv/tt/rtl-rvtt-synth-opcode.cc](https://github.com/tenstorrent/sfpi-gcc/blob/763d6834/gcc/config/riscv/tt/rtl-rvtt-synth-opcode.cc)
*   [gcc/config/riscv/tt/t-riscv-tt](https://github.com/tenstorrent/sfpi-gcc/blob/763d6834/gcc/config/riscv/tt/t-riscv-tt)
*   [gcc/cp/ChangeLog](https://github.com/tenstorrent/sfpi-gcc/blob/763d6834/gcc/cp/ChangeLog)
*   [gcc/d/ChangeLog](https://github.com/tenstorrent/sfpi-gcc/blob/763d6834/gcc/d/ChangeLog)
*   [gcc/doc/invoke.texi](https://github.com/tenstorrent/sfpi-gcc/blob/763d6834/gcc/doc/invoke.texi)
*   [gcc/flag-types.h](https://github.com/tenstorrent/sfpi-gcc/blob/763d6834/gcc/flag-types.h)
*   [gcc/flags.h](https://github.com/tenstorrent/sfpi-gcc/blob/763d6834/gcc/flags.h)
*   [gcc/fortran/ChangeLog](https://github.com/tenstorrent/sfpi-gcc/blob/763d6834/gcc/fortran/ChangeLog)
*   [gcc/params.opt](https://github.com/tenstorrent/sfpi-gcc/blob/763d6834/gcc/params.opt)
*   [gcc/passes.def](https://github.com/tenstorrent/sfpi-gcc/blob/763d6834/gcc/passes.def)
*   [gcc/testsuite/ChangeLog](https://github.com/tenstorrent/sfpi-gcc/blob/763d6834/gcc/testsuite/ChangeLog)
*   [gcc/testsuite/g++.dg/pr102955.C](https://github.com/tenstorrent/sfpi-gcc/blob/763d6834/gcc/testsuite/g++.dg/pr102955.C)
*   [gcc/testsuite/gcc.dg/pr102585.c](https://github.com/tenstorrent/sfpi-gcc/blob/763d6834/gcc/testsuite/gcc.dg/pr102585.c)
*   [gcc/testsuite/gcc.target/aarch64/vect-cse-codegen.c](https://github.com/tenstorrent/sfpi-gcc/blob/763d6834/gcc/testsuite/gcc.target/aarch64/vect-cse-codegen.c)
*   [gcc/testsuite/gcc.target/i386/attr-optimize.c](https://github.com/tenstorrent/sfpi-gcc/blob/763d6834/gcc/testsuite/gcc.target/i386/attr-optimize.c)
*   [gcc/toplev.h](https://github.com/tenstorrent/sfpi-gcc/blob/763d6834/gcc/toplev.h)
*   [gcc/tree-pass.h](https://github.com/tenstorrent/sfpi-gcc/blob/763d6834/gcc/tree-pass.h)
*   [libgomp/ChangeLog](https://github.com/tenstorrent/sfpi-gcc/blob/763d6834/libgomp/ChangeLog)
*   [libphobos/ChangeLog](https://github.com/tenstorrent/sfpi-gcc/blob/763d6834/libphobos/ChangeLog)
*   [libstdc++-v3/ChangeLog](https://github.com/tenstorrent/sfpi-gcc/blob/763d6834/libstdc++-v3/ChangeLog)

This document covers the core GCC infrastructure components that form the foundation of the sfpi-gcc compiler. This includes the build system, configuration mechanisms, command-line option handling, and the overall compilation pipeline that orchestrates the transformation from source code to executable output.

For information about SFPU-specific extensions, see [SFPU Extensions](https://deepwiki.com/tenstorrent/sfpi-gcc/2-sfpu-extensions). For details about language frontend implementations, see [Language Support](https://deepwiki.com/tenstorrent/sfpi-gcc/4-language-support). For target-specific backend implementations, see [Target Backends](https://deepwiki.com/tenstorrent/sfpi-gcc/6-target-backends).

## Overview

The GCC infrastructure provides the essential framework that enables the compiler to function as a cohesive system. It consists of three primary subsystems: the build system that configures and compiles the compiler itself, the command-line interface that handles user options and parameters, and the optimization pipeline that manages the sequence of compilation passes.

## Infrastructure Architecture

Sources: [gcc/Makefile.in](https://github.com/tenstorrent/sfpi-gcc/blob/763d6834/gcc/Makefile.in)[gcc/common.opt](https://github.com/tenstorrent/sfpi-gcc/blob/763d6834/gcc/common.opt)[gcc/c-family/c.opt](https://github.com/tenstorrent/sfpi-gcc/blob/763d6834/gcc/c-family/c.opt)[gcc/params.opt](https://github.com/tenstorrent/sfpi-gcc/blob/763d6834/gcc/params.opt)[gcc/flags.h](https://github.com/tenstorrent/sfpi-gcc/blob/763d6834/gcc/flags.h)[gcc/flag-types.h](https://github.com/tenstorrent/sfpi-gcc/blob/763d6834/gcc/flag-types.h)[gcc/passes.def](https://github.com/tenstorrent/sfpi-gcc/blob/763d6834/gcc/passes.def)[gcc/tree-pass.h](https://github.com/tenstorrent/sfpi-gcc/blob/763d6834/gcc/tree-pass.h)[gcc/doc/invoke.texi](https://github.com/tenstorrent/sfpi-gcc/blob/763d6834/gcc/doc/invoke.texi)

## Build System and Configuration

The GCC build system is centered around the autotools-generated configuration system and GNU Make. The `Makefile.in` template defines build targets, dependency relationships, and compilation rules for the entire compiler suite.

Key configuration components include:

*   **configure**: Detects system capabilities and generates build configuration
*   **Makefile.in**: Defines build rules and target dependencies
*   **DATESTAMP**: Tracks build version information for reproducible builds

For detailed information about build system mechanics and configuration options, see [Build System and Configuration](https://deepwiki.com/tenstorrent/sfpi-gcc/3.1-build-system-and-configuration).

## Command Line Interface

GCC's command-line interface is implemented through a hierarchical option definition system that supports both general compiler options and language-specific flags.

The option system supports complex features including:

*   Multi-level optimization flags (`-O0`, `-O1`, `-O2`, `-O3`, `-Ofast`)
*   Warning control with fine-grained enablement (`-Wall`, `-Wextra`, `-Wpedantic`)
*   Target-specific options and parameter tuning
*   Debug information control and profiling options

For comprehensive coverage of command-line option handling and flag management, see [Command Line Interface](https://deepwiki.com/tenstorrent/sfpi-gcc/3.2-command-line-interface).

Sources: [gcc/common.opt 1-4000](https://github.com/tenstorrent/sfpi-gcc/blob/763d6834/gcc/common.opt#L1-L4000)[gcc/c-family/c.opt 1-2000](https://github.com/tenstorrent/sfpi-gcc/blob/763d6834/gcc/c-family/c.opt#L1-L2000)[gcc/params.opt 1-1000](https://github.com/tenstorrent/sfpi-gcc/blob/763d6834/gcc/params.opt#L1-L1000)[gcc/flags.h 1-100](https://github.com/tenstorrent/sfpi-gcc/blob/763d6834/gcc/flags.h#L1-L100)[gcc/flag-types.h 1-500](https://github.com/tenstorrent/sfpi-gcc/blob/763d6834/gcc/flag-types.h#L1-L500)

## Optimization Pipeline

The GCC optimization pipeline is defined through a declarative pass specification system that manages the execution order and dependencies between compilation phases.

The pipeline includes several major phases:

*   **Early optimization**: Basic cleanup and canonicalization
*   **Interprocedural analysis**: Cross-function optimization opportunities
*   **Tree-level optimization**: High-level transformations on GIMPLE
*   **RTL optimization**: Low-level machine-specific improvements

For detailed information about pass implementation and pipeline configuration, see [Optimization Pipeline](https://deepwiki.com/tenstorrent/sfpi-gcc/3.3-pass-management-and-optimization-pipeline).

Sources: [gcc/passes.def 1-400](https://github.com/tenstorrent/sfpi-gcc/blob/763d6834/gcc/passes.def#L1-L400)[gcc/tree-pass.h 1-800](https://github.com/tenstorrent/sfpi-gcc/blob/763d6834/gcc/tree-pass.h#L1-L800)

## Infrastructure Integration Points

The GCC infrastructure provides several key integration points where SFPU extensions and other specialized components interface with the core compiler:

| Component | Integration Point | Purpose |
| --- | --- | --- |
| Build System | `Makefile.in` targets | Compile SFPU-specific source files |
| Option System | `common.opt` entries | Define SFPU-specific command-line flags |
| Pass Pipeline | `passes.def` insertions | Add SFPU optimization passes |
| Documentation | `invoke.texi` sections | Document SFPU options and behavior |

## Version and Change Management

GCC infrastructure includes comprehensive change tracking and version management:

*   **DATESTAMP**: Build timestamp for version identification
*   **ChangeLog files**: Detailed development history across all components
*   **Release management**: Coordinated versioning across compiler components

The ChangeLog system maintains detailed records of modifications across different subsystems, with separate logs for major components like the C++ frontend ([gcc/cp/ChangeLog](https://github.com/tenstorrent/sfpi-gcc/blob/763d6834/gcc/cp/ChangeLog)), the C frontend ([gcc/c/ChangeLog](https://github.com/tenstorrent/sfpi-gcc/blob/763d6834/gcc/c/ChangeLog)), language-independent parts ([gcc/ChangeLog](https://github.com/tenstorrent/sfpi-gcc/blob/763d6834/gcc/ChangeLog)), and libraries ([libstdc++-v3/ChangeLog](https://github.com/tenstorrent/sfpi-gcc/blob/763d6834/libstdc++-v3/ChangeLog)).

Sources: [gcc/DATESTAMP 1](https://github.com/tenstorrent/sfpi-gcc/blob/763d6834/gcc/DATESTAMP#L1-L1)[gcc/ChangeLog 1-1000](https://github.com/tenstorrent/sfpi-gcc/blob/763d6834/gcc/ChangeLog#L1-L1000)[gcc/cp/ChangeLog 1-1000](https://github.com/tenstorrent/sfpi-gcc/blob/763d6834/gcc/cp/ChangeLog#L1-L1000)[gcc/testsuite/ChangeLog 1-1000](https://github.com/tenstorrent/sfpi-gcc/blob/763d6834/gcc/testsuite/ChangeLog#L1-L1000)

## Documentation System

The infrastructure includes a comprehensive documentation generation system based on Texinfo format:

*   **invoke.texi**: Primary user documentation for command-line options
*   **Generated manuals**: Automatic generation of formatted documentation
*   **Option cross-references**: Automatic linking between option definitions and documentation

The documentation system automatically extracts option information from the `.opt` files to ensure consistency between implementation and user-facing documentation.

Sources: [gcc/doc/invoke.texi 1-50000](https://github.com/tenstorrent/sfpi-gcc/blob/763d6834/gcc/doc/invoke.texi#L1-L50000)

Dismiss
Refresh this wiki

Enter email to refresh
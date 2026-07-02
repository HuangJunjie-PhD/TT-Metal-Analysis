---
project: sfpi-gcc
github: https://github.com/tenstorrent/sfpi-gcc
deepwiki: https://deepwiki.com/tenstorrent/sfpi-gcc/5-analysis-and-optimization
---

# Analysis and Optimization

Relevant source files
*   [gcc/Makefile.in](https://github.com/tenstorrent/sfpi-gcc/blob/763d6834/gcc/Makefile.in)
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
*   [gcc/doc/invoke.texi](https://github.com/tenstorrent/sfpi-gcc/blob/763d6834/gcc/doc/invoke.texi)
*   [gcc/flag-types.h](https://github.com/tenstorrent/sfpi-gcc/blob/763d6834/gcc/flag-types.h)
*   [gcc/flags.h](https://github.com/tenstorrent/sfpi-gcc/blob/763d6834/gcc/flags.h)
*   [gcc/optabs-query.cc](https://github.com/tenstorrent/sfpi-gcc/blob/763d6834/gcc/optabs-query.cc)
*   [gcc/optabs-query.h](https://github.com/tenstorrent/sfpi-gcc/blob/763d6834/gcc/optabs-query.h)
*   [gcc/params.opt](https://github.com/tenstorrent/sfpi-gcc/blob/763d6834/gcc/params.opt)
*   [gcc/passes.def](https://github.com/tenstorrent/sfpi-gcc/blob/763d6834/gcc/passes.def)
*   [gcc/testsuite/g++.dg/pr102955.C](https://github.com/tenstorrent/sfpi-gcc/blob/763d6834/gcc/testsuite/g++.dg/pr102955.C)
*   [gcc/testsuite/g++.dg/vect/pr102788.cc](https://github.com/tenstorrent/sfpi-gcc/blob/763d6834/gcc/testsuite/g++.dg/vect/pr102788.cc)
*   [gcc/testsuite/g++.dg/vect/slp-pr87105.cc](https://github.com/tenstorrent/sfpi-gcc/blob/763d6834/gcc/testsuite/g++.dg/vect/slp-pr87105.cc)
*   [gcc/testsuite/gcc.dg/pr102585.c](https://github.com/tenstorrent/sfpi-gcc/blob/763d6834/gcc/testsuite/gcc.dg/pr102585.c)
*   [gcc/testsuite/gcc.dg/pr102798.c](https://github.com/tenstorrent/sfpi-gcc/blob/763d6834/gcc/testsuite/gcc.dg/pr102798.c)
*   [gcc/testsuite/gcc.dg/torture/pr102124.c](https://github.com/tenstorrent/sfpi-gcc/blob/763d6834/gcc/testsuite/gcc.dg/torture/pr102124.c)
*   [gcc/testsuite/gcc.dg/torture/pr103816.c](https://github.com/tenstorrent/sfpi-gcc/blob/763d6834/gcc/testsuite/gcc.dg/torture/pr103816.c)
*   [gcc/testsuite/gcc.dg/tree-ssa/ssa-dom-thread-2b.c](https://github.com/tenstorrent/sfpi-gcc/blob/763d6834/gcc/testsuite/gcc.dg/tree-ssa/ssa-dom-thread-2b.c)
*   [gcc/testsuite/gcc.dg/vect/bb-slp-71.c](https://github.com/tenstorrent/sfpi-gcc/blob/763d6834/gcc/testsuite/gcc.dg/vect/bb-slp-71.c)
*   [gcc/testsuite/gcc.dg/vect/bb-slp-pr101242.c](https://github.com/tenstorrent/sfpi-gcc/blob/763d6834/gcc/testsuite/gcc.dg/vect/bb-slp-pr101242.c)
*   [gcc/testsuite/gcc.dg/vect/bb-slp-pr54400.c](https://github.com/tenstorrent/sfpi-gcc/blob/763d6834/gcc/testsuite/gcc.dg/vect/bb-slp-pr54400.c)
*   [gcc/testsuite/gcc.dg/vect/pr102832.c](https://github.com/tenstorrent/sfpi-gcc/blob/763d6834/gcc/testsuite/gcc.dg/vect/pr102832.c)
*   [gcc/testsuite/gcc.dg/vect/pr103744-1.c](https://github.com/tenstorrent/sfpi-gcc/blob/763d6834/gcc/testsuite/gcc.dg/vect/pr103744-1.c)
*   [gcc/testsuite/gcc.dg/vect/pr103744-2.c](https://github.com/tenstorrent/sfpi-gcc/blob/763d6834/gcc/testsuite/gcc.dg/vect/pr103744-2.c)
*   [gcc/testsuite/gcc.dg/vect/pr104445.c](https://github.com/tenstorrent/sfpi-gcc/blob/763d6834/gcc/testsuite/gcc.dg/vect/pr104445.c)
*   [gcc/testsuite/gcc.dg/vect/pr67790.c](https://github.com/tenstorrent/sfpi-gcc/blob/763d6834/gcc/testsuite/gcc.dg/vect/pr67790.c)
*   [gcc/testsuite/gcc.target/aarch64/masked_epilogue.c](https://github.com/tenstorrent/sfpi-gcc/blob/763d6834/gcc/testsuite/gcc.target/aarch64/masked_epilogue.c)
*   [gcc/testsuite/gcc.target/aarch64/sve/cond_arith_6.c](https://github.com/tenstorrent/sfpi-gcc/blob/763d6834/gcc/testsuite/gcc.target/aarch64/sve/cond_arith_6.c)
*   [gcc/testsuite/gcc.target/aarch64/sve/gather_load_10.c](https://github.com/tenstorrent/sfpi-gcc/blob/763d6834/gcc/testsuite/gcc.target/aarch64/sve/gather_load_10.c)
*   [gcc/testsuite/gcc.target/aarch64/vect-cse-codegen.c](https://github.com/tenstorrent/sfpi-gcc/blob/763d6834/gcc/testsuite/gcc.target/aarch64/vect-cse-codegen.c)
*   [gcc/testsuite/gcc.target/i386/attr-optimize.c](https://github.com/tenstorrent/sfpi-gcc/blob/763d6834/gcc/testsuite/gcc.target/i386/attr-optimize.c)
*   [gcc/testsuite/gcc.target/i386/vect-alignment-peeling-1.c](https://github.com/tenstorrent/sfpi-gcc/blob/763d6834/gcc/testsuite/gcc.target/i386/vect-alignment-peeling-1.c)
*   [gcc/testsuite/gcc.target/i386/vect-alignment-peeling-2.c](https://github.com/tenstorrent/sfpi-gcc/blob/763d6834/gcc/testsuite/gcc.target/i386/vect-alignment-peeling-2.c)
*   [gcc/testsuite/gcc.target/i386/vect-gather-1.c](https://github.com/tenstorrent/sfpi-gcc/blob/763d6834/gcc/testsuite/gcc.target/i386/vect-gather-1.c)
*   [gcc/testsuite/gcc.target/powerpc/pr101596-1.c](https://github.com/tenstorrent/sfpi-gcc/blob/763d6834/gcc/testsuite/gcc.target/powerpc/pr101596-1.c)
*   [gcc/testsuite/gcc.target/powerpc/pr101596-2.c](https://github.com/tenstorrent/sfpi-gcc/blob/763d6834/gcc/testsuite/gcc.target/powerpc/pr101596-2.c)
*   [gcc/testsuite/gcc.target/powerpc/pr101596-3.c](https://github.com/tenstorrent/sfpi-gcc/blob/763d6834/gcc/testsuite/gcc.target/powerpc/pr101596-3.c)
*   [gcc/testsuite/gfortran.dg/vect/vect-8.f90](https://github.com/tenstorrent/sfpi-gcc/blob/763d6834/gcc/testsuite/gfortran.dg/vect/vect-8.f90)
*   [gcc/toplev.h](https://github.com/tenstorrent/sfpi-gcc/blob/763d6834/gcc/toplev.h)
*   [gcc/tree-pass.h](https://github.com/tenstorrent/sfpi-gcc/blob/763d6834/gcc/tree-pass.h)
*   [gcc/tree-vectorizer.h](https://github.com/tenstorrent/sfpi-gcc/blob/763d6834/gcc/tree-vectorizer.h)

This document covers the analysis and optimization infrastructure in sfpi-gcc, including range analysis, pattern matching, expression folding, vectorization, and interprocedural analysis systems. These components work together to analyze program properties and perform optimizations based on that analysis.

For information about SFPU-specific optimizations, see [SFPU Extensions](https://deepwiki.com/tenstorrent/sfpi-gcc/2-sfpu-extensions). For backend-specific optimizations, see [Target Backends](https://deepwiki.com/tenstorrent/sfpi-gcc/6-target-backends).

## Overview

The analysis and optimization system in sfpi-gcc consists of several interconnected subsystems that analyze program properties and apply transformations. The main components are:

*   **Range Analysis**: Tracks value ranges of SSA names across the program
*   **Pattern Matching and Folding**: Simplifies expressions using pattern-based rules
*   **Vectorization**: Transforms scalar operations into vector operations
*   **Interprocedural Analysis**: Analyzes function calls and memory effects

## Range Analysis System

The range analysis system is a comprehensive framework for tracking the possible values of SSA names throughout the program. It consists of multiple layers working together to provide precise range information.

### Core Architecture

Sources: [gcc/gimple-range.h 46-72](https://github.com/tenstorrent/sfpi-gcc/blob/763d6834/gcc/gimple-range.h#L46-L72)[gcc/gimple-range-cache.h 121-158](https://github.com/tenstorrent/sfpi-gcc/blob/763d6834/gcc/gimple-range-cache.h#L121-L158)[gcc/gimple-range-gori.h 85-158](https://github.com/tenstorrent/sfpi-gcc/blob/763d6834/gcc/gimple-range-gori.h#L85-L158)

### Range Query Interface

The `range_query` base class provides the fundamental interface for querying ranges:

Sources: [gcc/value-query.h 57-136](https://github.com/tenstorrent/sfpi-gcc/blob/763d6834/gcc/value-query.h#L57-L136)[gcc/gimple-range.cc 74-133](https://github.com/tenstorrent/sfpi-gcc/blob/763d6834/gcc/gimple-range.cc#L74-L133)

### GORI (Generate Outgoing Range Information)

The GORI system computes outgoing ranges on edges based on conditions:

Sources: [gcc/gimple-range-gori.cc 36-106](https://github.com/tenstorrent/sfpi-gcc/blob/763d6834/gcc/gimple-range-gori.cc#L36-L106)[gcc/gimple-range-gori.h 29-158](https://github.com/tenstorrent/sfpi-gcc/blob/763d6834/gcc/gimple-range-gori.h#L29-L158)

## Pattern Matching and Expression Folding

The pattern matching system uses rule-based transformations to simplify expressions and eliminate redundant computations.

### Match.pd Pattern System

The `match.pd` file contains pattern-based transformation rules:

Sources: [gcc/match.pd 1-50](https://github.com/tenstorrent/sfpi-gcc/blob/763d6834/gcc/match.pd#L1-L50)[gcc/match.pd 84-143](https://github.com/tenstorrent/sfpi-gcc/blob/763d6834/gcc/match.pd#L84-L143)

### Constant Folding Infrastructure

Sources: [gcc/fold-const.cc 28-41](https://github.com/tenstorrent/sfpi-gcc/blob/763d6834/gcc/fold-const.cc#L28-L41)[gcc/gimple-range-fold.cc 537-583](https://github.com/tenstorrent/sfpi-gcc/blob/763d6834/gcc/gimple-range-fold.cc#L537-L583)

## Vectorization System

The vectorization system transforms scalar operations into vector operations using SLP (Superword-Level Parallelism) and loop vectorization.

### Vectorizer Architecture

Sources: [gcc/tree-vectorizer.h 415-471](https://github.com/tenstorrent/sfpi-gcc/blob/763d6834/gcc/tree-vectorizer.h#L415-L471)[gcc/tree-vectorizer.h 160-222](https://github.com/tenstorrent/sfpi-gcc/blob/763d6834/gcc/tree-vectorizer.h#L160-L222)[gcc/tree-vectorizer.h 99-110](https://github.com/tenstorrent/sfpi-gcc/blob/763d6834/gcc/tree-vectorizer.h#L99-L110)

### SLP Tree Structure

Sources: [gcc/tree-vectorizer.h 161-222](https://github.com/tenstorrent/sfpi-gcc/blob/763d6834/gcc/tree-vectorizer.h#L161-L222)[gcc/tree-vectorizer.h 227-266](https://github.com/tenstorrent/sfpi-gcc/blob/763d6834/gcc/tree-vectorizer.h#L227-L266)

## Interprocedural Analysis

The IPA modref system analyzes memory references and side effects across function boundaries.

### Modref Tree Structure

Sources: [gcc/ipa-modref-tree.h 44-142](https://github.com/tenstorrent/sfpi-gcc/blob/763d6834/gcc/ipa-modref-tree.h#L44-L142)[gcc/ipa-modref-tree.h 47-57](https://github.com/tenstorrent/sfpi-gcc/blob/763d6834/gcc/ipa-modref-tree.h#L47-L57)

### Function Summaries

Sources: [gcc/ipa-modref.h 28-72](https://github.com/tenstorrent/sfpi-gcc/blob/763d6834/gcc/ipa-modref.h#L28-L72)[gcc/ipa-modref.h 79-109](https://github.com/tenstorrent/sfpi-gcc/blob/763d6834/gcc/ipa-modref.h#L79-L109)

## Integration and Data Flow

The analysis systems work together through well-defined interfaces:

Sources: [gcc/gimple-range.cc 40-71](https://github.com/tenstorrent/sfpi-gcc/blob/763d6834/gcc/gimple-range.cc#L40-L71)[gcc/tree-vectorizer.h 391-471](https://github.com/tenstorrent/sfpi-gcc/blob/763d6834/gcc/tree-vectorizer.h#L391-L471)[gcc/ipa-modref-tree.h 306-458](https://github.com/tenstorrent/sfpi-gcc/blob/763d6834/gcc/ipa-modref-tree.h#L306-L458)

The analysis and optimization system provides the foundation for most optimizations in sfpi-gcc, with the range analysis system being particularly important for enabling precise optimizations while maintaining correctness.

Dismiss
Refresh this wiki

Enter email to refresh
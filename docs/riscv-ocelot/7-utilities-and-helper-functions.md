---
project: riscv-ocelot
github: https://github.com/tenstorrent/riscv-ocelot
deepwiki: https://deepwiki.com/tenstorrent/riscv-ocelot/7-utilities-and-helper-functions
---

# Utilities and Helper Functions

Relevant source files
*   [src/main/scala/util/elastic-reg.scala](https://github.com/tenstorrent/riscv-ocelot/blob/920fd904/src/main/scala/util/elastic-reg.scala)
*   [src/main/scala/util/elastic-sram.scala](https://github.com/tenstorrent/riscv-ocelot/blob/920fd904/src/main/scala/util/elastic-sram.scala)
*   [src/main/scala/util/seqmem-transformable.scala](https://github.com/tenstorrent/riscv-ocelot/blob/920fd904/src/main/scala/util/seqmem-transformable.scala)
*   [src/main/scala/util/util.scala](https://github.com/tenstorrent/riscv-ocelot/blob/920fd904/src/main/scala/util/util.scala)

This page documents the various utility functions and helper classes used throughout the RISC-V Ocelot codebase. These utilities provide common functionality for bit manipulation, branch handling, register operations, memory components, and queue management that support the core processor implementation.

## Utility Overview

The RISC-V Ocelot codebase contains a collection of utility functions that can be grouped into several categories:

Sources: [src/main/scala/util/util.scala 1-665](https://github.com/tenstorrent/riscv-ocelot/blob/920fd904/src/main/scala/util/util.scala#L1-L665)

## Bit Manipulation Utilities

The RISC-V Ocelot codebase provides a variety of bit manipulation utilities that simplify common operations on bits and bit vectors.

### Fold

The `Fold` object XOR-folds an input register of `fullLength` into a compressed output of `compressedLength`. This is useful for creating compact hashes or signatures of larger values.

`Fold.apply(input: UInt, compressedLength: Int, fullLength: Int): UInt`
### Masking Operations

Two complementary masking operations are provided:

*   `MaskLower`: Sets all bits at or below the highest order '1'
*   `MaskUpper`: Sets all bits at or above the lowest order '1'

### Transposition and Selection

*   `Transpose`: Transposes a matrix of Chisel `Vec`s
*   `SelectFirstN`: N-wide one-hot priority encoder that selects the first N valid bits from an input

Sources: [src/main/scala/util/util.scala 29-47](https://github.com/tenstorrent/riscv-ocelot/blob/920fd904/src/main/scala/util/util.scala#L29-L47)[src/main/scala/util/util.scala 376-422](https://github.com/tenstorrent/riscv-ocelot/blob/920fd904/src/main/scala/util/util.scala#L376-L422)

## Branch Handling Utilities

Branch handling is a critical part of the BOOM out-of-order processor pipeline. Several utilities manage branch masks and updates:

Notable branch utilities include:

*   `IsKilledByBranch`: Checks if a microop was killed due to a branch mispredict
*   `GetNewUopAndBrMask`: Creates a new microop with an updated branch mask
*   `GetNewBrMask`: Returns a new branch mask given a microop mask and old branch mask
*   `UpdateBrMask`: Updates the branch mask of a microop or bundle
*   `maskMatch`: Checks if at least 1 bit matches in two masks
*   `clearMaskBit`: Clears one bit in a mask at the specified index

Sources: [src/main/scala/util/util.scala 54-127](https://github.com/tenstorrent/riscv-ocelot/blob/920fd904/src/main/scala/util/util.scala#L54-L127)

## Register and Counter Utilities

The codebase provides several utilities for register operations:

### Register Shift Operations

*   `PerformShiftRegister`: Shifts a register over by one bit and concatenates a new one
*   `PerformCircularShiftRegister`: Circular shift register that wraps the top bit around to the bottom

### Counter Operations

These utilities handle wrapped counting, which is useful for circular buffers and other wrapping data structures:

*   `WrapAdd`/`WrapSub`: Increment/decrement a value with wrapping
*   `WrapInc`/`WrapDec`: Convenience functions for incrementing/decrementing by 1 with wrapping
*   `AlignPCToBoundary`: Masks off lower bits of a PC to align to a specified byte boundary
*   `RotateL1`: Rotates a signal left by one bit
*   `Sext`: Sign-extends a value to a particular length

Sources: [src/main/scala/util/util.scala 132-262](https://github.com/tenstorrent/riscv-ocelot/blob/920fd904/src/main/scala/util/util.scala#L132-L262)

## Memory Utilities

The RISC-V Ocelot provides specialized memory components that extend the basic Chisel `Mem` and `SyncReadMem` primitives.

### ElasticSeqMem

`ElasticSeqMem` implements a decoupled SRAM which can be back-pressured on the read response side, which in turn back-pressures the read request side.

### ElasticReg

`ElasticReg` implements the same interface as Chisel's `Queue` but is specifically designed as a two-entry queue. It's optimized for short, elastic buffering in a pipeline.

### SeqMem1rwTransformable

`SeqMem1rwTransformable` implements a realizable `SeqMem` that transforms a tall, skinny aspect ratio memory into a more rectangular shape. This can be useful for improving memory utilization and layout.

`// Transforms logical dimensions to physical dimensionsval pDepth = 1 << log2Ceil(scala.math.sqrt(lDepth*lWidth).toInt)val pWidth = lDepth*lWidth/pDepth`
Sources: [src/main/scala/util/elastic-sram.scala 1-82](https://github.com/tenstorrent/riscv-ocelot/blob/920fd904/src/main/scala/util/elastic-sram.scala#L1-L82)[src/main/scala/util/elastic-reg.scala 1-57](https://github.com/tenstorrent/riscv-ocelot/blob/920fd904/src/main/scala/util/elastic-reg.scala#L1-L57)[src/main/scala/util/seqmem-transformable.scala 1-79](https://github.com/tenstorrent/riscv-ocelot/blob/920fd904/src/main/scala/util/seqmem-transformable.scala#L1-L79)

## Queue Utilities

### BranchKillableQueue

The `BranchKillableQueue` provides a queue implementation that can be flushed or invalidated when a branch misprediction occurs. This is vital in an out-of-order processor where speculative instructions need to be squashed on a branch mispredict.

Key features:

*   Tracks branch masks for each entry
*   Automatically invalidates entries affected by branch misprediction
*   Can be flushed entirely on demand
*   Provides flow-through behavior (like a Chisel `Queue`)

### Compactor

The `Compactor` is used to connect the first k of n valid input interfaces to k output interfaces. It's useful for selecting and compacting a sparse set of valid signals.

Sources: [src/main/scala/util/util.scala 427-542](https://github.com/tenstorrent/riscv-ocelot/blob/920fd904/src/main/scala/util/util.scala#L427-L542)

## Debug Utilities

Debug utilities help with processor debugging and diagnostics:

*   `DebugIsJALR`: Determines if an instruction is a JALR (Jump And Link Register)
*   `DebugGetBJImm`: Extracts the branch or jump immediate value from an instruction
*   `ImmGen`, `ImmGenRm`, `ImmGenTyp`: Utilities for immediate value generation and extraction

Sources: [src/main/scala/util/util.scala 307-346](https://github.com/tenstorrent/riscv-ocelot/blob/920fd904/src/main/scala/util/util.scala#L307-L346)

## Print Formatting Utilities

A set of helper functions make it easier to format data for debug printing:

These utilities convert various processor state elements into human-readable strings for debugging:

*   `BoolToChar`: Converts a Bool to a character for printing
*   `CfiTypeToChars`: Converts control flow instruction type to a string
*   `BpdTypeToChars`: Converts branch predictor type to a string
*   `RobTypeToChars`: Converts reorder buffer type to a string
*   `XRegToChars`/`FPRegToChars`: Converts register numbers to human-readable names
*   `BoomCoreStringPrefix`: Adds a prefix to BOOM strings (for multi-hart systems)

Sources: [src/main/scala/util/util.scala 548-665](https://github.com/tenstorrent/riscv-ocelot/blob/920fd904/src/main/scala/util/util.scala#L548-L665)

## Integration with Core Components

The utility functions and helpers described on this page are used extensively throughout the core components of the RISC-V Ocelot processor. The diagram below shows how these utilities integrate with key processor components:

The utilities provide essential functionality that's used by multiple components across the processor, promoting code reuse and ensuring consistent behavior throughout the design.

Dismiss
Refresh this wiki

Enter email to refresh
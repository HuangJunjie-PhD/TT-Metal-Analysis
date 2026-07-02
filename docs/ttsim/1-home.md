---
project: ttsim
github: https://github.com/tenstorrent/ttsim
deepwiki: https://deepwiki.com/tenstorrent/ttsim/1-home
---

# Home

Relevant source files
*   [README.md](https://github.com/tenstorrent/ttsim/blob/67b1733e/README.md?plain=1)

## Purpose and Scope

This page provides a high-level introduction to ttsim, the Tenstorrent hardware simulator. It explains what ttsim is, its role in the Tenstorrent development ecosystem, and when developers should use it instead of physical hardware. For detailed setup instructions, see [Getting Started](https://deepwiki.com/tenstorrent/ttsim/2-getting-started). For comprehensive architectural details, see [Architecture](https://deepwiki.com/tenstorrent/ttsim/3-architecture). For contribution guidelines, see [Contributing](https://deepwiki.com/tenstorrent/ttsim/5-contributing).

* * *

## What is ttsim?

ttsim is a fast full-system simulator for Tenstorrent hardware architectures. It provides a virtual Wormhole or Blackhole device that executes on Linux/x86_64 systems without requiring physical Tenstorrent silicon.

The simulator is distributed as architecture-specific shared libraries (`libttsim_wh.so` for Wormhole, `libttsim_bh.so` for Blackhole) that export a simple API for integration with the TT-Metalium software stack. Each simulator binary is designed to produce bit-exact numerical results matching physical hardware for most operations.

**Sources:**[README.md 1-10](https://github.com/tenstorrent/ttsim/blob/67b1733e/README.md?plain=1#L1-L10)

* * *

## Core Capabilities

| Capability | Description |
| --- | --- |
| **Platform Support** | Linux/x86_64 host systems |
| **Chip Architectures** | Wormhole (mature), Blackhole (in development) |
| **Numerical Accuracy** | Bit-exact results for most operations; FPU operations partially accurate |
| **Integration** | Direct TT-Metalium integration via environment variables |
| **Dispatch Mode** | Slow dispatch only (fast dispatch not yet supported) |
| **Distribution Format** | Precompiled shared libraries (`.so` files) |

**Sources:**[README.md 1-10](https://github.com/tenstorrent/ttsim/blob/67b1733e/README.md?plain=1#L1-L10)[README.md 18-19](https://github.com/tenstorrent/ttsim/blob/67b1733e/README.md?plain=1#L18-L19)[README.md 79-102](https://github.com/tenstorrent/ttsim/blob/67b1733e/README.md?plain=1#L79-L102)

* * *

## When to Use ttsim

### Use ttsim When:

*   **Exploring Tenstorrent hardware** before purchasing physical silicon
*   **Developing and debugging** TT-Metalium workloads without hardware access
*   **Running CI/CD pipelines** that require reproducible test environments
*   **Verifying numerical correctness** of algorithms with bit-exact results
*   **Testing workloads** on architectures not yet in production

### Use Physical Silicon When:

*   **Measuring actual performance** and timing characteristics
*   **Validating final deployments** before production
*   **Debugging timing-dependent** or hardware-specific issues
*   **Developing fast dispatch** workloads (not yet supported in ttsim)
*   **Maximizing execution speed** (silicon is faster than simulation)

**Sources:**[README.md 3-6](https://github.com/tenstorrent/ttsim/blob/67b1733e/README.md?plain=1#L3-L6)[README.md 64](https://github.com/tenstorrent/ttsim/blob/67b1733e/README.md?plain=1#L64-L64)

* * *

## System Architecture Overview

The following diagram shows how ttsim integrates with the TT-Metalium stack and relates to physical hardware:

### ttsim System Context

**Sources:**[README.md 8-10](https://github.com/tenstorrent/ttsim/blob/67b1733e/README.md?plain=1#L8-L10)[README.md 23-25](https://github.com/tenstorrent/ttsim/blob/67b1733e/README.md?plain=1#L23-L25)[README.md 41-61](https://github.com/tenstorrent/ttsim/blob/67b1733e/README.md?plain=1#L41-L61)

* * *

## Key Components

### Simulator Binaries

ttsim is distributed as architecture-specific shared libraries:

| File | Target Architecture | Status |
| --- | --- | --- |
| `libttsim_wh.so` | Wormhole B0 (80 cores) | Mature |
| `libttsim_bh.so` | Blackhole (140 cores) | In development |

Each binary must be co-located with a corresponding SOC descriptor file named `soc_descriptor.yaml`.

**Sources:**[README.md 8-10](https://github.com/tenstorrent/ttsim/blob/67b1733e/README.md?plain=1#L8-L10)[README.md 28-38](https://github.com/tenstorrent/ttsim/blob/67b1733e/README.md?plain=1#L28-L38)

### SOC Descriptor Files

SOC descriptors define the chip architecture topology and must be copied from TT-Metalium:

*   **Wormhole**: `tt_metal/soc_descriptors/wormhole_b0_80_arch.yaml`
*   **Blackhole**: `tt_metal/soc_descriptors/blackhole_140_arch.yaml`

The descriptor file must reside in the same directory as the simulator `.so` file and be named exactly `soc_descriptor.yaml`.

**Sources:**[README.md 42-48](https://github.com/tenstorrent/ttsim/blob/67b1733e/README.md?plain=1#L42-L48)[README.md 55-61](https://github.com/tenstorrent/ttsim/blob/67b1733e/README.md?plain=1#L55-L61)

### Environment Variables

Three environment variables control ttsim operation:

| Variable | Purpose | Example Value |
| --- | --- | --- |
| `TT_METAL_HOME` | Points to TT-Metalium installation | `~/tt-metal` |
| `TT_METAL_SIMULATOR` | Path to simulator `.so` file | `~/sim/libttsim_wh.so` |
| `TT_METAL_SLOW_DISPATCH_MODE` | Enables slow dispatch (required) | `1` |

**Sources:**[README.md 23-25](https://github.com/tenstorrent/ttsim/blob/67b1733e/README.md?plain=1#L23-L25)[README.md 41-61](https://github.com/tenstorrent/ttsim/blob/67b1733e/README.md?plain=1#L41-L61)[README.md 64](https://github.com/tenstorrent/ttsim/blob/67b1733e/README.md?plain=1#L64-L64)

* * *

## Component Relationships

The following diagram shows how the concrete code entities interact at runtime:

### Runtime Component Interactions

**Sources:**[README.md 8-10](https://github.com/tenstorrent/ttsim/blob/67b1733e/README.md?plain=1#L8-L10)[README.md 41-43](https://github.com/tenstorrent/ttsim/blob/67b1733e/README.md?plain=1#L41-L43)

* * *

## Execution Flow

When a TT-Metalium workload runs with ttsim:

1.   **Configuration**: User sets `TT_METAL_HOME`, `TT_METAL_SIMULATOR`, and `TT_METAL_SLOW_DISPATCH_MODE=1`
2.   **Library Loading**: TT-Metalium dynamically loads the `.so` file specified by `TT_METAL_SIMULATOR`
3.   **Descriptor Loading**: Simulator reads `soc_descriptor.yaml` from its directory to configure chip topology
4.   **Workload Execution**: User's tt-metal code runs on simulated Tensix cores
5.   **Result Validation**: Bit-exact results are produced for most operations

**Example Command:**

`export TT_METAL_SIMULATOR=~/sim/libttsim_wh.soTT_METAL_SLOW_DISPATCH_MODE=1 ./build/programming_examples/metal_example_add_2_integers_in_riscv`
**Sources:**[README.md 41-61](https://github.com/tenstorrent/ttsim/blob/67b1733e/README.md?plain=1#L41-L61)

* * *

## Simulated Hardware

ttsim simulates key components of Tenstorrent architecture:

| Component | Simulation Status | Bit-Exact? |
| --- | --- | --- |
| **Tensix Cores** | Fully simulated | Partial |
| **Unpacker** | Complete | Yes |
| **SFPU** | Complete | Yes |
| **Packer** | Complete | Yes |
| **FPU MOV*/GMPOOL** | Complete | Yes |
| **FPU ELW*/MVMUL/GAPOOL** | Complete | Not yet |
| **Memory Subsystem** | Complete | Yes (with caveats) |

Timing-dependent operations may produce different execution orders than silicon, which can affect results for non-deterministic algorithms (e.g., unordered floating-point reductions).

**Sources:**[README.md 79-102](https://github.com/tenstorrent/ttsim/blob/67b1733e/README.md?plain=1#L79-L102)

* * *

## Error Categories

ttsim provides structured error reporting with the following categories:

*   **UndefinedBehavior**: ISA contract violations
*   **UnimplementedFunctionality**: Features planned but not yet implemented
*   **UntestedFunctionality**: Implemented but insufficient test coverage
*   **UnsupportedFunctionality**: Features unlikely to be implemented
*   **ConfigurationError**: Environment variable or setup issues
*   **AssertionFailure**: Internal simulator bugs

For detailed error reference, see [Error Reference](https://deepwiki.com/tenstorrent/ttsim/4.4-error-reference).

**Sources:**[README.md 66-76](https://github.com/tenstorrent/ttsim/blob/67b1733e/README.md?plain=1#L66-L76)

* * *

## Current Limitations

*   **Fast dispatch mode not supported**: Must use `TT_METAL_SLOW_DISPATCH_MODE=1`
*   **FPU accuracy**: Some FPU opcodes not yet bit-exact with silicon
*   **Architecture maturity**: Wormhole is more mature than Blackhole
*   **Binary-only distribution**: Source code release planned for future

**Sources:**[README.md 64](https://github.com/tenstorrent/ttsim/blob/67b1733e/README.md?plain=1#L64-L64)[README.md 99-102](https://github.com/tenstorrent/ttsim/blob/67b1733e/README.md?plain=1#L99-L102)[README.md 13-15](https://github.com/tenstorrent/ttsim/blob/67b1733e/README.md?plain=1#L13-L15)

* * *

## Development Workflow

**Sources:**[README.md 3-6](https://github.com/tenstorrent/ttsim/blob/67b1733e/README.md?plain=1#L3-L6)

* * *

## Related Pages

*   **[Getting Started](https://deepwiki.com/tenstorrent/ttsim/2-getting-started)**: Installation and first-run instructions
*   **[Architecture](https://deepwiki.com/tenstorrent/ttsim/3-architecture)**: Detailed technical architecture documentation
*   **[Configuration](https://deepwiki.com/tenstorrent/ttsim/4.1-configuration)**: Complete environment variable reference
*   **[Running Workloads](https://deepwiki.com/tenstorrent/ttsim/4.2-running-workloads)**: How to execute tt-metal examples
*   **[Numerical Accuracy](https://deepwiki.com/tenstorrent/ttsim/4.3-numerical-accuracy)**: Bit-exactness guarantees and limitations
*   **[Contributing](https://deepwiki.com/tenstorrent/ttsim/5-contributing)**: How to report issues and request features
*   **[Licensing](https://deepwiki.com/tenstorrent/ttsim/6.2-licensing)**: Apache 2.0 license terms and hardware/IP clarifications

**Sources:**[README.md 1-121](https://github.com/tenstorrent/ttsim/blob/67b1733e/README.md?plain=1#L1-L121)

* * *

## Quick Start Reference

For users familiar with TT-Metalium who want to get started immediately:

`# 1. Download simulatorwget https://github.com/tenstorrent/ttsim/releases/download/vX.Y/libttsim_wh.so -P ~/sim # 2. Copy SOC descriptorcp $TT_METAL_HOME/tt_metal/soc_descriptors/wormhole_b0_80_arch.yaml ~/sim/soc_descriptor.yaml # 3. Configure and runexport TT_METAL_SIMULATOR=~/sim/libttsim_wh.socd $TT_METAL_HOMETT_METAL_SLOW_DISPATCH_MODE=1 ./build/programming_examples/metal_example_add_2_integers_in_riscv`
**Sources:**[README.md 28-52](https://github.com/tenstorrent/ttsim/blob/67b1733e/README.md?plain=1#L28-L52)

Dismiss
Refresh this wiki

Enter email to refresh
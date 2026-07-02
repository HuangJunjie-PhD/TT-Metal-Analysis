---
project: ttsim
github: https://github.com/tenstorrent/ttsim
deepwiki: https://deepwiki.com/tenstorrent/ttsim/7-reference
---

# Reference

Relevant source files
*   [README.md](https://github.com/tenstorrent/ttsim/blob/67b1733e/README.md?plain=1)

This section provides detailed reference information for advanced ttsim users and developers. It consolidates technical specifications, constants, file naming conventions, error categories, and implementation status details that are frequently referenced during development and debugging.

For practical usage guidance, see [User Guide](https://deepwiki.com/tenstorrent/ttsim/4-user-guide). For architectural concepts, see [Architecture](https://deepwiki.com/tenstorrent/ttsim/3-architecture).

* * *

## Overview

The ttsim reference documentation covers three main areas:

1.   **Supported Architectures** ([7.1](https://deepwiki.com/tenstorrent/ttsim/7.1-supported-architectures)): Details on Wormhole and Blackhole chip support, including SOC descriptor files and architecture-specific considerations
2.   **Environment Variables** ([7.2](https://deepwiki.com/tenstorrent/ttsim/7.2-environment-variables)): Complete reference of all environment variables used by ttsim
3.   **Feature Status** ([7.3](https://deepwiki.com/tenstorrent/ttsim/7.3-feature-status)): Implementation status and bit-accuracy guarantees for various hardware components

This page provides quick-reference summaries and cross-references to help locate specific technical details.

Sources: [README.md 1-121](https://github.com/tenstorrent/ttsim/blob/67b1733e/README.md?plain=1#L1-L121)

* * *

## Component Reference Map

This diagram illustrates the relationships between simulator binaries, configuration files, environment variables, simulated hardware components, and error categories. Each component is detailed in the subsections below and in child pages.

Sources: [README.md 8-103](https://github.com/tenstorrent/ttsim/blob/67b1733e/README.md?plain=1#L8-L103)

* * *

## Simulator Binary Reference

ttsim provides architecture-specific simulator libraries that integrate with TT-Metalium.

| Binary File | Architecture | SOC Descriptor | Status |
| --- | --- | --- | --- |
| `libttsim_wh.so` | Wormhole B0 | `wormhole_b0_80_arch.yaml` | More mature |
| `libttsim_bh.so` | Blackhole | `blackhole_140_arch.yaml` | Less mature |

### Binary Location Requirements

Each `libttsim.so` file must be placed in a directory that also contains a file named `soc_descriptor.yaml`. This co-location requirement is enforced by the simulator initialization logic.

**Example directory structure:**

```
~/sim/
├── libttsim_wh.so
└── soc_descriptor.yaml  (copy of wormhole_b0_80_arch.yaml)
```

The `TT_METAL_SIMULATOR` environment variable must point to the full path of the `.so` file, not just the directory.

Sources: [README.md 8-9](https://github.com/tenstorrent/ttsim/blob/67b1733e/README.md?plain=1#L8-L9)[README.md 42-61](https://github.com/tenstorrent/ttsim/blob/67b1733e/README.md?plain=1#L42-L61)

* * *

## SOC Descriptor Mapping

SOC (System-on-Chip) descriptor files define the architectural characteristics of each chip. These YAML files are located in TT-Metalium and must be copied to the simulator directory.

The SOC descriptor must match the simulator architecture. Using `wormhole_b0_80_arch.yaml` with `libttsim_bh.so` will result in a `ConfigurationError`.

Sources: [README.md 42-61](https://github.com/tenstorrent/ttsim/blob/67b1733e/README.md?plain=1#L42-L61)

* * *

## Error Category Reference

ttsim reports errors using standardized categories that indicate the nature and severity of the issue.

| Category | Description | Typical Cause | User Action |
| --- | --- | --- | --- |
| `UndefinedBehavior` | ISA violation producing unpredictable results | Invalid instruction usage, violating contracts | Fix code to comply with ISA specification |
| `UnpredictableValueUsed` | Using data from unpredictable sources | Reading uninitialized memory, race conditions | Add proper initialization or synchronization |
| `NonContractualBehavior` | Relying on behavior outside ISA guarantees | Assuming specific behavior not in specification | Modify code to use only contractual behavior |
| `UntestedFunctionality` | Feature implemented but not tested | Using newly implemented features | Report issues if encountered; feature may have bugs |
| `UnimplementedFunctionality` | Feature not yet available | Using planned but not yet implemented features | Wait for future release or use alternative approach |
| `UnsupportedFunctionality` | Feature unlikely to be implemented | Using features outside simulator scope | Redesign workload or run on silicon |
| `MissingSpecification` | Feature blocked on specification work | Using features requiring internal clarification | Feature will be implemented after specification complete |
| `SystemError` | Operating system error | File I/O failure, memory allocation failure | Check system resources and permissions |
| `ConfigurationError` | Invalid configuration | Wrong environment variables, missing files | Review configuration requirements |
| `AssertionFailure` | Internal simulator bug | Simulator implementation defect | Report as bug with reproduction steps |

### Error Category Hierarchy

For detailed troubleshooting guidance, see [Error Reference](https://deepwiki.com/tenstorrent/ttsim/4.4-error-reference).

For ISA specification terminology, consult the [tt-isa-documentation glossary](https://github.com/tenstorrent/ttsim/blob/67b1733e/tt-isa-documentation%20glossary)

Sources: [README.md 66-76](https://github.com/tenstorrent/ttsim/blob/67b1733e/README.md?plain=1#L66-L76)

* * *

## Implementation Status Summary

This table provides a quick reference for the bit-accuracy status of major functional units within the simulated Tensix core.

| Component | Implementation Status | Bit-Accuracy | Notes |
| --- | --- | --- | --- |
| Unpacker | Complete | ✓ Bit-exact | Fully bit-accurate |
| SFPU | Complete | ✓ Bit-exact | Fully bit-accurate |
| Packer | Complete | ✓ Bit-exact | Fully bit-accurate |
| FPU: MOV* opcodes | Complete | ✓ Bit-exact | Move operations bit-accurate |
| FPU: GMPOOL opcode | Complete | ✓ Bit-exact | Global max pooling bit-accurate |
| FPU: ELW* opcodes | Functional | ✗ Approximate | Element-wise operations not bit-accurate |
| FPU: MVMUL opcode | Functional | ✗ Approximate | Matrix-vector multiply not bit-accurate |
| FPU: GAPOOL opcode | Functional | ✗ Approximate | Global average pooling not bit-accurate |
| Fast Dispatch | Not implemented | N/A | Must use `TT_METAL_SLOW_DISPATCH_MODE=1` |

### Tensix Core Pipeline Status

For complete implementation details and planned improvements, see [Feature Status](https://deepwiki.com/tenstorrent/ttsim/7.3-feature-status).

For numerical accuracy expectations and divergence scenarios, see [Numerical Accuracy](https://deepwiki.com/tenstorrent/ttsim/4.3-numerical-accuracy).

Sources: [README.md 99-102](https://github.com/tenstorrent/ttsim/blob/67b1733e/README.md?plain=1#L99-L102)

* * *

## Dispatch Mode Reference

ttsim currently supports only slow dispatch mode. Fast dispatch mode is not implemented.

| Dispatch Mode | Environment Variable | Status | Required? |
| --- | --- | --- | --- |
| Slow Dispatch | `TT_METAL_SLOW_DISPATCH_MODE=1` | Supported | ✓ Yes |
| Fast Dispatch | `TT_METAL_SLOW_DISPATCH_MODE=0` | Not implemented | - |

**Configuration Example:**

`# Correct (required)TT_METAL_SLOW_DISPATCH_MODE=1 ./build/programming_examples/metal_example_add_2_integers_in_riscv # Incorrect (will fail)./build/programming_examples/metal_example_add_2_integers_in_riscv`
Omitting `TT_METAL_SLOW_DISPATCH_MODE=1` will result in a `ConfigurationError` or `UnimplementedFunctionality` error.

Sources: [README.md 64](https://github.com/tenstorrent/ttsim/blob/67b1733e/README.md?plain=1#L64-L64)[README.md 51-52](https://github.com/tenstorrent/ttsim/blob/67b1733e/README.md?plain=1#L51-L52)[README.md 60-61](https://github.com/tenstorrent/ttsim/blob/67b1733e/README.md?plain=1#L60-L61)

* * *

## Divergence Scenarios

ttsim aims for bit-exact results but certain scenarios can produce divergent behavior compared to silicon.

| Scenario | Cause | Deterministic? | Mitigation |
| --- | --- | --- | --- |
| Timing-dependent operand order | Concurrent operations with insufficient synchronization | No | Add explicit serialization |
| Hardware RNG reads | Entropy sources differ from silicon | No | Use software PRNG with fixed seed |
| Performance counter reads | Simulator timing model differs | No | Avoid timing-dependent logic |
| Cycle counter reads | Simulator timing model differs | No | Avoid timing-dependent logic |
| Missing memory fences | Weaker memory ordering in simulator | No | Add proper synchronization primitives |
| UndefinedBehavior execution | ISA contract violation | No | Fix ISA violations |
| Uninitialized memory reads | UnpredictableValue usage | No | Initialize all memory before use |

### Determinism in Floating-Point Operations

For algorithms where operation order affects results (e.g., floating-point reductions), ttsim may evaluate operations in any order permitted by software synchronization. This can differ from silicon's evaluation order.

**Example non-deterministic pattern:**

```
// Multiple threads adding to accumulator without serialization
accumulator += partial_result;  // Order undefined
```

**Example deterministic pattern:**

```
// Explicit serialization ensures deterministic order
for (int i = 0; i < N; i++) {
    accumulator += partial_result[i];  // Order explicit
}
```

Sources: [README.md 78-97](https://github.com/tenstorrent/ttsim/blob/67b1733e/README.md?plain=1#L78-L97)

* * *

## Cross-Reference Index

For detailed information on specific topics:

*   **Architecture selection**: [Supported Architectures](https://deepwiki.com/tenstorrent/ttsim/7.1-supported-architectures)
*   **Environment variable details**: [Environment Variables](https://deepwiki.com/tenstorrent/ttsim/7.2-environment-variables)
*   **Bit-accuracy guarantees**: [Feature Status](https://deepwiki.com/tenstorrent/ttsim/7.3-feature-status)
*   **Configuration procedures**: [Configuration](https://deepwiki.com/tenstorrent/ttsim/4.1-configuration)
*   **Error troubleshooting**: [Error Reference](https://deepwiki.com/tenstorrent/ttsim/4.4-error-reference)
*   **Numerical accuracy expectations**: [Numerical Accuracy](https://deepwiki.com/tenstorrent/ttsim/4.3-numerical-accuracy)

* * *

## Related Resources

*   **ISA Specification**: [tt-isa-documentation glossary](https://github.com/tenstorrent/ttsim/blob/67b1733e/tt-isa-documentation%20glossary)
*   **TT-Metalium Integration**: [TT-Metalium Integration](https://deepwiki.com/tenstorrent/ttsim/3.3-tt-metalium-integration)
*   **SOC Descriptor Location**: `$TT_METAL_HOME/tt_metal/soc_descriptors/`

Sources: [README.md 1-121](https://github.com/tenstorrent/ttsim/blob/67b1733e/README.md?plain=1#L1-L121)

Dismiss
Refresh this wiki

Enter email to refresh
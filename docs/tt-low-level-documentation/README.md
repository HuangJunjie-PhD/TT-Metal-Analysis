---
project: tt-low-level-documentation
github: https://github.com/tenstorrent/tt-low-level-documentation
deepwiki: https://deepwiki.com/tenstorrent/tt-low-level-documentation
---

# Overview

 Relevant source files

* [README.md](https://github.com/tenstorrent/tt-low-level-documentation/blob/ae7661c8/README.md?plain=1)

## Purpose and Scope

This repository provides low-level documentation for the TT-Metal software stack, focusing on two primary domains:

1. **Data Movement APIs**: Comprehensive coverage of Network-on-Chip (NOC) operations, overlay infrastructure, and data transfer patterns for Wormhole B0 and Blackhole P100 devices
2. **Compute Low-level Kernel APIs**: Documentation of Tensix hardware capabilities and Low-Level Kernel (LLK) API usage patterns

The documentation synthesizes information from multiple source repositories (`tt-metal`, `tt-llk`) and provides practical guidance for operation and model writers. This includes API usage patterns, performance analysis, and best practices derived from empirical testing.

**Out of Scope**: This repository does NOT document hardware instructions or ISA-level features. For detailed hardware instruction documentation, refer to the [tt-isa-documentation](https://github.com/tenstorrent/tt-low-level-documentation/blob/ae7661c8/tt-isa-documentation) repository.

For detailed data movement system architecture, see [Data Movement System](/tenstorrent/tt-low-level-documentation/2-data-movement-system). For API usage patterns and best practices, see [API Usage Guide](/tenstorrent/tt-low-level-documentation/3-api-usage-guide). For performance benchmarks and evaluations, see [Performance Analysis](/tenstorrent/tt-low-level-documentation/4-performance-analysis).

Sources: [README.md1-10](https://github.com/tenstorrent/tt-low-level-documentation/blob/ae7661c8/README.md?plain=1#L1-L10)

## Repository Architecture

The following diagram illustrates how this documentation repository integrates with the broader TT-Metal ecosystem:

**Repository Integration Points**

This documentation repository aggregates and synthesizes information from:

* **tt-metal repository**: Source of NOC API implementations and data movement test suites located at `tests/tt_metal/tt_metal/data_movement/`
* **tt-llk repository**: Source of Low-Level Kernel API implementations for Tensix compute operations
* **tt-isa-documentation**: Reference for hardware instruction set architecture details (not duplicated here)

Sources: [README.md1-10](https://github.com/tenstorrent/tt-low-level-documentation/blob/ae7661c8/README.md?plain=1#L1-L10)

## Documentation Coverage Map

The following diagram maps documentation topics to their corresponding sections and source code locations:

**Coverage by Section**

| Section | Topics Covered | Source Code Reference |
| --- | --- | --- |
| [Data Movement System](/tenstorrent/tt-low-level-documentation/2-data-movement-system) | NOC architecture, 2D torus topology, dual NOC instances, virtual channels | `tt-metal/hw/inc/` |
| [API Usage Guide](/tenstorrent/tt-low-level-documentation/3-api-usage-guide) | Asynchronous operations, circular buffers, batching patterns, posted writes | `tt-metal` NOC API headers |
| [Performance Analysis](/tenstorrent/tt-low-level-documentation/4-performance-analysis) | Multicast schemes, virtual channel evaluation, bandwidth measurements | `tests/tt_metal/tt_metal/data_movement/` |
| [Technical Reference](/tenstorrent/tt-low-level-documentation/5-technical-reference) | Register state preservation, platform differences, specifications | `tt-metal/hw/inc/` |

Sources: [README.md1-10](https://github.com/tenstorrent/tt-low-level-documentation/blob/ae7661c8/README.md?plain=1#L1-L10)

## Target Audience

This documentation is designed for **operation and model writers** working with the TT-Metal software stack. The primary audience includes:

* Software engineers implementing custom operations using NOC data movement primitives
* ML framework developers optimizing data transfer patterns for model execution
* Performance engineers analyzing and debugging data movement bottlenecks
* System architects designing efficient communication topologies

**Prerequisites**: Readers should have:

* Familiarity with asynchronous programming concepts
* Basic understanding of network-on-chip architectures
* Experience with C/C++ programming
* Knowledge of memory hierarchies (DRAM, L1, registers)

**Not Intended For**: Hardware engineers seeking ISA-level instruction documentation should refer to `tt-isa-documentation` instead.

Sources: [README.md1-10](https://github.com/tenstorrent/tt-low-level-documentation/blob/ae7661c8/README.md?plain=1#L1-L10)

## Relationship to Other Repositories

**Repository Boundaries**

| Repository | Responsibility | Example Content |
| --- | --- | --- |
| `tt-low-level-documentation` (this) | API usage, performance analysis, best practices | "How to use `noc_async_read()` efficiently", "Multicast scheme performance comparison" |
| `tt-metal` | Runtime implementation, kernel APIs, test suites | `noc_async_read()` implementation, `tests/tt_metal/tt_metal/data_movement/` |
| `tt-llk` | Low-level compute kernel implementations | LLK API implementations for Tensix operations |
| `tt-isa-documentation` | Hardware instruction specifications | "DRAM read instruction encoding", "NOC packet format" |

The key distinction: this repository provides **usage documentation and performance guidance** for APIs implemented in `tt-metal` and `tt-llk`, while `tt-isa-documentation` provides **hardware-level specifications**.

Sources: [README.md1-10](https://github.com/tenstorrent/tt-low-level-documentation/blob/ae7661c8/README.md?plain=1#L1-L10)

## How to Use This Documentation

### Navigation Structure

The documentation follows a layered approach:

1. **Foundational Concepts** ([Data Movement System](/tenstorrent/tt-low-level-documentation/2-data-movement-system)): Start here to understand NOC architecture, hardware platforms, and system topology
2. **Practical Usage** ([API Usage Guide](/tenstorrent/tt-low-level-documentation/3-api-usage-guide)): Learn correct API usage patterns, synchronization requirements, and common pitfalls
3. **Performance Optimization** ([Performance Analysis](/tenstorrent/tt-low-level-documentation/4-performance-analysis)): Study empirical performance evaluations to make informed design decisions
4. **Technical Details** ([Technical Reference](/tenstorrent/tt-low-level-documentation/5-technical-reference)): Reference platform-specific specifications and register behaviors

### Reading Paths by Use Case

| Use Case | Recommended Reading Path |
| --- | --- |
| New to TT-Metal data movement | [2.1 Introduction](/tenstorrent/tt-low-level-documentation/2.1-introduction-to-data-movement) → [3.1 Core API Concepts](/tenstorrent/tt-low-level-documentation/3.1-core-api-concepts) → [3.2 Async Operations](/tenstorrent/tt-low-level-documentation/3.2-asynchronous-operations-and-batching) |
| Implementing multicast operations | [4.2 Multicast Schemes Study](/tenstorrent/tt-low-level-documentation/4.2-multicast-schemes-performance-study) → [3.1 Core API Concepts](/tenstorrent/tt-low-level-documentation/3.1-core-api-concepts) → [2.2 NOC Architecture](/tenstorrent/tt-low-level-documentation/2.2-noc-architecture) |
| Debugging performance issues | [4.1 Baseline Performance](/tenstorrent/tt-low-level-documentation/4.1-baseline-performance-metrics) → [4.2 Multicast Schemes](/tenstorrent/tt-low-level-documentation/4.2-multicast-schemes-performance-study) → [4.3 Virtual Channels](/tenstorrent/tt-low-level-documentation/4.3-virtual-channels-performance-study) |
| Optimizing circular buffer usage | [3.3 Circular Buffers](/tenstorrent/tt-low-level-documentation/3.3-circular-buffers) → [3.2 Async Operations](/tenstorrent/tt-low-level-documentation/3.2-asynchronous-operations-and-batching) → [3.6 Posted Writes](/tenstorrent/tt-low-level-documentation/3.6-posted-writes-optimization) |
| Understanding platform differences | [2.3 Hardware Platforms](/tenstorrent/tt-low-level-documentation/2.3-hardware-platforms) → [5.2 Platform Comparison](/tenstorrent/tt-low-level-documentation/5.2-platform-comparison) → [5.1 Register State](/tenstorrent/tt-low-level-documentation/5.1-register-state-preservation) |

### Key Performance Findings

The documentation emphasizes empirical results from the `tests/tt_metal/tt_metal/data_movement/` test suite. Critical findings include:

* **Multicast self-interference mechanism**: Performance degrades sharply when write and acknowledgement traffic share physical links ([Section 4.2](/tenstorrent/tt-low-level-documentation/4.2-multicast-schemes-performance-study))
* **Virtual channel limitations**: Multiple VCs do NOT improve same-class traffic throughput due to arbitration and buffer sharing ([Section 4.3](/tenstorrent/tt-low-level-documentation/4.3-virtual-channels-performance-study))
* **Posted write optimization**: Up to 75% bandwidth improvement for NOC-saturating operations when using posted writes ([Section 3.6](/tenstorrent/tt-low-level-documentation/3.6-posted-writes-optimization))

These findings inform the best practices documented throughout [Section 3](/tenstorrent/tt-low-level-documentation/3-api-usage-guide).

Sources: [README.md1-10](https://github.com/tenstorrent/tt-low-level-documentation/blob/ae7661c8/README.md?plain=1#L1-L10)

## Current Documentation Status

### Data Movement Documentation

**Complete**:

* NOC architecture and topology fundamentals
* API usage patterns and best practices
* Comprehensive multicast scheme performance evaluation
* Virtual channel usage guidelines
* Platform comparison (Wormhole B0 vs Blackhole P100)

**In Progress**:

* Additional data movement patterns and case studies
* Extended performance analysis for specialized topologies

### Compute Documentation

**Planned**:

* Tensix hardware architecture
* LLK API usage patterns
* Compute kernel optimization techniques
* Performance analysis of compute primitives

The compute documentation will follow the same structure as data movement: foundational concepts, API usage, performance analysis, and technical reference.

Sources: [README.md1-10](https://github.com/tenstorrent/tt-low-level-documentation/blob/ae7661c8/README.md?plain=1#L1-L10)

## License

This documentation is licensed under the Creative Commons Attribution 4.0 International License. For details, see [License](/tenstorrent/tt-low-level-documentation/5.3-license).

Sources: [README.md1-10](https://github.com/tenstorrent/tt-low-level-documentation/blob/ae7661c8/README.md?plain=1#L1-L10)

Refresh this wiki
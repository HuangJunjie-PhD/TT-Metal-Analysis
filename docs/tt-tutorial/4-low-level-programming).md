---
project: tt-tutorial
github: https://github.com/tenstorrent/tt-tutorial
deepwiki: https://deepwiki.com/tenstorrent/tt-tutorial/4-low-level-programming)
---

# Low-Level Programming

Relevant source files
*   [operations/README.md](https://github.com/tenstorrent/tt-tutorial/blob/389c542c/operations/README.md?plain=1)
*   [tt-metalium/README.md](https://github.com/tenstorrent/tt-tutorial/blob/389c542c/tt-metalium/README.md?plain=1)

## Purpose and Scope

This section covers the foundational programming models and primitives for developing kernels and operations on Tenstorrent hardware. It encompasses the TT-Metalium programming model, core operations framework, and hands-on programming examples that form the building blocks for higher-level neural network operations.

For high-level neural network operations built on these primitives, see [TT-NN Neural Network Library](https://deepwiki.com/tenstorrent/tt-tutorial/3.2-tt-nn-neural-network-library). For model development using these low-level components, see [Model Development](https://deepwiki.com/tenstorrent/tt-tutorial/5-model-development).

## TT-Metalium Programming Model

TT-Metalium serves as Tenstorrent's core low-level programming model, enabling direct kernel development for Tenstorrent hardware architectures. The programming model provides abstractions for compute kernels, data movement, and hardware resource management.

### Architecture and Core Concepts

**TT-Metalium Programming Model Architecture**

The programming model abstracts hardware complexities while providing direct access to specialized compute units and interconnect resources.

Sources: [tt-metalium/README.md 11-16](https://github.com/tenstorrent/tt-tutorial/blob/389c542c/tt-metalium/README.md?plain=1#L11-L16)

### Technical Documentation and Resources

TT-Metalium provides comprehensive technical documentation covering architecture-specific implementations and optimization strategies:

| Resource Category | Documentation | Purpose |
| --- | --- | --- |
| Beginner Guide | TT-Metalium For Beginners | Foundational concepts and programming model introduction |
| Profiling | Metal Profiler | Performance analysis and optimization tools |
| Architecture | Galaxy Wormhole 6U Software Guide | Platform-specific programming guidelines |
| Networking | Basic Ethernet Multichip, CCL Developer Guide | Multi-device and distributed programming |
| Compute | FlashAttention, FlashDecode | Advanced compute kernel implementations |
| Memory | Tensor and Memory Layouts, Allocator | Memory management and data organization |
| Data Formats | Data Formats, Matrix Engine | Numerical precision and compute engine usage |

Sources: [tt-metalium/README.md 17-39](https://github.com/tenstorrent/tt-tutorial/blob/389c542c/tt-metalium/README.md?plain=1#L17-L39)

## Operations and Primitives Framework

The operations framework provides fundamental building blocks for data manipulation, computation, and testing within the TT-Metal ecosystem.

### Core Operation Categories

**Operations Framework to Hardware Mapping**

The operations framework abstracts hardware-specific implementations while maintaining performance-critical optimizations.

Sources: [operations/README.md 12-15](https://github.com/tenstorrent/tt-tutorial/blob/389c542c/operations/README.md?plain=1#L12-L15)

### Sharding and Data Distribution

Sharding operations enable efficient data distribution across multiple compute units and memory hierarchies:

*   **VecAdd Sharding**: Demonstrates basic vector addition with data sharding across cores
*   **Memory Layout Optimization**: Efficient tensor distribution for parallel computation
*   **Inter-core Communication**: Coordination mechanisms for distributed operations

The `vecadd_sharding.cpp` implementation showcases fundamental sharding patterns used throughout the operations framework.

Sources: [operations/README.md 14-15](https://github.com/tenstorrent/tt-tutorial/blob/389c542c/operations/README.md?plain=1#L14-L15)

## Programming Examples and Learning Path

TT-Metalium provides hands-on programming examples that demonstrate kernel development progression from basic arithmetic to complex multi-core operations.

### Kernel Development Progression

**Programming Examples Learning Progression**

Examples progress from simple arithmetic operations to complex distributed computing patterns.

Sources: [tt-metalium/README.md 20-22](https://github.com/tenstorrent/tt-tutorial/blob/389c542c/tt-metalium/README.md?plain=1#L20-L22)

### Core Programming Patterns

The programming examples demonstrate essential patterns for TT-Metalium development:

1.   **Compute Kernel Programming**: Basic integer addition showcases compute kernel structure and execution model
2.   **RISC-V Programming**: Low-level processor programming for specialized control and coordination tasks
3.   **Data Sharding**: Vector operations with distributed data placement and parallel execution

Each example builds foundational understanding for developing production kernels and operations.

Sources: [tt-metalium/README.md 21-22](https://github.com/tenstorrent/tt-tutorial/blob/389c542c/tt-metalium/README.md?plain=1#L21-L22)[operations/README.md 15](https://github.com/tenstorrent/tt-tutorial/blob/389c542c/operations/README.md?plain=1#L15-L15)

## Technical Specifications and Advanced Topics

TT-Metalium supports advanced programming concepts through comprehensive technical documentation covering specialized hardware features and optimization strategies.

### Architecture-Specific Programming

| Technology Area | Technical Reports | Implementation Focus |
| --- | --- | --- |
| Memory Systems | Saturating DRAM bandwidth, Allocator | Memory hierarchy optimization |
| Compute Engines | Matrix Engine, Data Formats | Numerical computation specialization |
| Networking | Ethernet Multichip, TT-Distributed | Multi-device coordination |
| Hardware Features | Sub-Devices, TT-Fabric Architecture | Advanced hardware utilization |
| Numerical Precision | Handling Special Values | Infinity, NaN, denormal handling |

### Performance Optimization Framework

**Performance Optimization and Technical Architecture**

Advanced optimization requires understanding memory systems, compute engines, and distributed coordination mechanisms.

Sources: [tt-metalium/README.md 24-39](https://github.com/tenstorrent/tt-tutorial/blob/389c542c/tt-metalium/README.md?plain=1#L24-L39)

The technical reports provide in-depth coverage of specialized programming techniques, from basic memory allocation to complex multi-device distributed computing architectures. These resources enable developers to implement high-performance kernels optimized for specific Tenstorrent hardware configurations.

Dismiss
Refresh this wiki

Enter email to refresh
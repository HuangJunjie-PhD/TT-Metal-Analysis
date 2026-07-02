---
project: tensix-isa-simulator
github: https://github.com/tenstorrent/tensix-isa-simulator
deepwiki: https://deepwiki.com/tenstorrent/tensix-isa-simulator/3-llk-system
---

# LLK System

Relevant source files
*   [tensix/whb0/prj/build_llk_api.sh](https://github.com/tenstorrent/tensix-isa-simulator/blob/afb83529/tensix/whb0/prj/build_llk_api.sh)
*   [tensix/whb0/prj/build_llk_ref.sh](https://github.com/tenstorrent/tensix-isa-simulator/blob/afb83529/tensix/whb0/prj/build_llk_ref.sh)

## Purpose and Scope

The LLK (Low-Level Kernel) System is a core component of the Tensix ISA Simulator. It provides the foundational kernel functionality necessary for simulating the Tensix instruction set architecture. This document provides an overview of the LLK System, its architecture, components, and how it relates to other parts of the simulator.

For information about the r37 architecture, see [r37 Architecture](https://deepwiki.com/tenstorrent/tensix-isa-simulator/4-r37-architecture). For details about supporting libraries, see [Supporting Libraries](https://deepwiki.com/tenstorrent/tensix-isa-simulator/5-supporting-libraries).

## System Architecture

The LLK System consists of several components that work together to provide Low-Level Kernel functionality:

1.   **LLK API**: The interface layer that defines the operations and data structures for LLK
2.   **LLK Implementations**: Different implementations of the LLK API, each with different characteristics: 
    *   **Basic Implementation**: A simple implementation focused on clarity
    *   **Reference Implementation**: A standard implementation serving as a baseline
    *   **Compute Implementation**: An optimized implementation for compute operations

### LLK System Component Diagram

Sources:

*   [tensix/whb0/prj/build_llk_api.sh](https://github.com/tenstorrent/tensix-isa-simulator/blob/afb83529/tensix/whb0/prj/build_llk_api.sh)
*   [tensix/whb0/prj/build_llk_ref.sh](https://github.com/tenstorrent/tensix-isa-simulator/blob/afb83529/tensix/whb0/prj/build_llk_ref.sh)

## LLK Components

### LLK API

The LLK API defines the interface for the Low-Level Kernel operations. All LLK implementations must adhere to this API, ensuring consistency and interchangeability. The API is compiled into the `llk_api.a` library.

**Build Process:**

*   Source: `../src/llk/api/*.cpp`
*   Include directories: `../src`, `../src/llk/api`
*   Output: `../lib/llk_api.a`

Sources:

*   [tensix/whb0/prj/build_llk_api.sh](https://github.com/tenstorrent/tensix-isa-simulator/blob/afb83529/tensix/whb0/prj/build_llk_api.sh)

### LLK Basic Implementation

The LLK Basic Implementation provides a simple implementation of the LLK API. It is designed for clarity and educational purposes, making it ideal for understanding the core functionality of the LLK System without the complexity of optimizations.

### LLK Reference Implementation

The LLK Reference Implementation serves as a baseline for other implementations. It provides a standard implementation of the LLK API that can be used to validate other implementations. The reference implementation is compiled into the `llk_ref.a` library.

**Build Process:**

*   Source: `../src/llk/api/*.cpp`
*   Include directories: `../src`, `../src/system`, `../src/llk/api`, `../src/llk/ref`
*   Output: `../lib/llk_ref.a`

Sources:

*   [tensix/whb0/prj/build_llk_ref.sh](https://github.com/tenstorrent/tensix-isa-simulator/blob/afb83529/tensix/whb0/prj/build_llk_ref.sh)

### LLK Compute Implementation

The LLK Compute Implementation is optimized for compute-intensive operations. It provides optimized implementations of the LLK API functions that are commonly used in compute workloads.

## Integration with Other Systems

The LLK System integrates with other components of the Tensix ISA Simulator, particularly the r37 System, which has its own LLK implementation specific to the r37 architecture.

### System Integration Diagram

Sources: Based on the system architecture diagrams provided.

## Library Dependencies

The LLK System has dependencies on other libraries in the Tensix ISA Simulator, primarily the Core library. The different LLK implementations may have additional dependencies based on their specific requirements.

| LLK Component | Dependencies |
| --- | --- |
| LLK API | Core |
| LLK Basic | Core |
| LLK Reference | Core |
| LLK Compute | Core |

## Build System

The LLK components are built using separate build scripts. The build process creates static libraries (`*.a` files) that can be linked with applications that require LLK functionality.

### Build Process Diagram

Sources:

*   [tensix/whb0/prj/build_llk_api.sh](https://github.com/tenstorrent/tensix-isa-simulator/blob/afb83529/tensix/whb0/prj/build_llk_api.sh)
*   [tensix/whb0/prj/build_llk_ref.sh](https://github.com/tenstorrent/tensix-isa-simulator/blob/afb83529/tensix/whb0/prj/build_llk_ref.sh)

## Demo Applications

There are several demo applications that showcase the functionality of the LLK System:

| Demo Application | Description |
| --- | --- |
| demo_llk_basic | Demonstrates the Basic LLK Implementation |
| demo_llk_ref | Demonstrates the Reference LLK Implementation |
| demo_llk_r37 | Demonstrates the r37 LLK Implementation |

For more information about these demo applications, see [LLK Demo Applications](https://deepwiki.com/tenstorrent/tensix-isa-simulator/6.1-llk-demo-applications).

Dismiss
Refresh this wiki

Enter email to refresh
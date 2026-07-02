---
project: tt-buda
github: https://github.com/tenstorrent/tt-buda
deepwiki: https://deepwiki.com/tenstorrent/tt-buda/2-core-architecture
---

# Core Architecture

Relevant source files
*   [pybuda/csrc/backend_api/backend_api.cpp](https://github.com/tenstorrent/tt-buda/blob/74755d1f/pybuda/csrc/backend_api/backend_api.cpp)
*   [pybuda/csrc/backend_api/device_config.hpp](https://github.com/tenstorrent/tt-buda/blob/74755d1f/pybuda/csrc/backend_api/device_config.hpp)
*   [pybuda/csrc/balancer/balancer.cpp](https://github.com/tenstorrent/tt-buda/blob/74755d1f/pybuda/csrc/balancer/balancer.cpp)
*   [pybuda/csrc/balancer/balancer_config.hpp](https://github.com/tenstorrent/tt-buda/blob/74755d1f/pybuda/csrc/balancer/balancer_config.hpp)
*   [pybuda/csrc/balancer/legalizer/graph_solver_types.hpp](https://github.com/tenstorrent/tt-buda/blob/74755d1f/pybuda/csrc/balancer/legalizer/graph_solver_types.hpp)
*   [pybuda/csrc/balancer/legalizer/legalizer.hpp](https://github.com/tenstorrent/tt-buda/blob/74755d1f/pybuda/csrc/balancer/legalizer/legalizer.hpp)
*   [pybuda/csrc/balancer/policies/policy_manager.cpp](https://github.com/tenstorrent/tt-buda/blob/74755d1f/pybuda/csrc/balancer/policies/policy_manager.cpp)
*   [pybuda/csrc/balancer/policies/policy_manager.hpp](https://github.com/tenstorrent/tt-buda/blob/74755d1f/pybuda/csrc/balancer/policies/policy_manager.hpp)
*   [pybuda/csrc/balancer/policies/policy_ribbon.cpp](https://github.com/tenstorrent/tt-buda/blob/74755d1f/pybuda/csrc/balancer/policies/policy_ribbon.cpp)
*   [pybuda/csrc/balancer/policies/policy_ribbon.hpp](https://github.com/tenstorrent/tt-buda/blob/74755d1f/pybuda/csrc/balancer/policies/policy_ribbon.hpp)
*   [pybuda/csrc/balancer/policies/policy_ribbon2.cpp](https://github.com/tenstorrent/tt-buda/blob/74755d1f/pybuda/csrc/balancer/policies/policy_ribbon2.cpp)
*   [pybuda/csrc/balancer/policies/policy_utils.cpp](https://github.com/tenstorrent/tt-buda/blob/74755d1f/pybuda/csrc/balancer/policies/policy_utils.cpp)
*   [pybuda/csrc/balancer/policies/policy_utils.hpp](https://github.com/tenstorrent/tt-buda/blob/74755d1f/pybuda/csrc/balancer/policies/policy_utils.hpp)
*   [pybuda/csrc/balancer/python_interface.hpp](https://github.com/tenstorrent/tt-buda/blob/74755d1f/pybuda/csrc/balancer/python_interface.hpp)
*   [pybuda/csrc/graph_lib/utils.cpp](https://github.com/tenstorrent/tt-buda/blob/74755d1f/pybuda/csrc/graph_lib/utils.cpp)
*   [pybuda/csrc/graph_lib/utils.hpp](https://github.com/tenstorrent/tt-buda/blob/74755d1f/pybuda/csrc/graph_lib/utils.hpp)
*   [pybuda/csrc/passes/fork_join.cpp](https://github.com/tenstorrent/tt-buda/blob/74755d1f/pybuda/csrc/passes/fork_join.cpp)
*   [pybuda/csrc/passes/fork_join.hpp](https://github.com/tenstorrent/tt-buda/blob/74755d1f/pybuda/csrc/passes/fork_join.hpp)
*   [pybuda/csrc/passes/tests/test_fork_join.cpp](https://github.com/tenstorrent/tt-buda/blob/74755d1f/pybuda/csrc/passes/tests/test_fork_join.cpp)
*   [pybuda/csrc/pybuda_bindings.cpp](https://github.com/tenstorrent/tt-buda/blob/74755d1f/pybuda/csrc/pybuda_bindings.cpp)
*   [pybuda/csrc/shared_utils/sparse_matmul_utils.cpp](https://github.com/tenstorrent/tt-buda/blob/74755d1f/pybuda/csrc/shared_utils/sparse_matmul_utils.cpp)
*   [pybuda/pybuda/_C/__init__.pyi](https://github.com/tenstorrent/tt-buda/blob/74755d1f/pybuda/pybuda/_C/__init__.pyi)
*   [pybuda/pybuda/_C/backend_api.pyi](https://github.com/tenstorrent/tt-buda/blob/74755d1f/pybuda/pybuda/_C/backend_api.pyi)
*   [pybuda/pybuda/backend.py](https://github.com/tenstorrent/tt-buda/blob/74755d1f/pybuda/pybuda/backend.py)
*   [pybuda/pybuda/compile.py](https://github.com/tenstorrent/tt-buda/blob/74755d1f/pybuda/pybuda/compile.py)
*   [pybuda/pybuda/tensor.py](https://github.com/tenstorrent/tt-buda/blob/74755d1f/pybuda/pybuda/tensor.py)
*   [pybuda/pybuda/ttdevice.py](https://github.com/tenstorrent/tt-buda/blob/74755d1f/pybuda/pybuda/ttdevice.py)

PyBuda's Core Architecture defines the fundamental components and mechanisms that transform neural network models into executable code for Tenstorrent hardware. This document explains the main architectural components, their relationships, and the compilation pipeline that converts user-defined models into efficient hardware configurations.

For information about specific subsystems like the TTDevice implementation or Balancer system, see their respective dedicated pages: [TTDevice and Compilation Pipeline](https://deepwiki.com/tenstorrent/tt-buda/2.1-ttdevice-and-compilation-pipeline) and [Balancer and Placement System](https://deepwiki.com/tenstorrent/tt-buda/2.2-balancer-and-placement-system).

## System Overview

The PyBuda framework follows a straightforward flow: users create PyBuda modules that are placed on TTDevices, which then transform these modules through a compilation pipeline into executable code for Tenstorrent hardware.

Sources:

*   [pybuda/pybuda/ttdevice.py 44-168](https://github.com/tenstorrent/tt-buda/blob/74755d1f/pybuda/pybuda/ttdevice.py#L44-L168) - TTDevice class definition
*   [pybuda/pybuda/compile.py 205-276](https://github.com/tenstorrent/tt-buda/blob/74755d1f/pybuda/pybuda/compile.py#L205-L276) - Compilation pipeline implementation
*   [pybuda/csrc/backend_api/backend_api.cpp 17-224](https://github.com/tenstorrent/tt-buda/blob/74755d1f/pybuda/csrc/backend_api/backend_api.cpp#L17-L224) - Backend API for hardware interaction

## Key Components

### TTDevice

The `TTDevice` class is the primary interface between user code and Tenstorrent hardware, responsible for placing modules, generating computational graphs, and orchestrating the compilation process.

Sources:

*   [pybuda/pybuda/ttdevice.py 44-168](https://github.com/tenstorrent/tt-buda/blob/74755d1f/pybuda/pybuda/ttdevice.py#L44-L168) - TTDevice class definition
*   [pybuda/pybuda/ttdevice.py 170-250](https://github.com/tenstorrent/tt-buda/blob/74755d1f/pybuda/pybuda/ttdevice.py#L170-L250) - Device configuration methods
*   [pybuda/pybuda/ttdevice.py 253-261](https://github.com/tenstorrent/tt-buda/blob/74755d1f/pybuda/pybuda/ttdevice.py#L253-L261) - Module placement implementation
*   [pybuda/pybuda/ttdevice.py 368-481](https://github.com/tenstorrent/tt-buda/blob/74755d1f/pybuda/pybuda/ttdevice.py#L368-L481) - Graph generation implementation

### Compilation Pipeline

The compilation pipeline transforms PyBuda modules into optimized executable code through a series of stages implemented in the `pybuda_compile_from_context` function:

Sources:

*   [pybuda/pybuda/compile.py 205-276](https://github.com/tenstorrent/tt-buda/blob/74755d1f/pybuda/pybuda/compile.py#L205-L276) - `pybuda_compile_from_context` function
*   [pybuda/pybuda/compile.py 489-508](https://github.com/tenstorrent/tt-buda/blob/74755d1f/pybuda/pybuda/compile.py#L489-L508) - `init_compile` function
*   [pybuda/pybuda/compile.py 222-236](https://github.com/tenstorrent/tt-buda/blob/74755d1f/pybuda/pybuda/compile.py#L222-L236) - Stage-to-function mapping

The compilation stages include:

1.   **Initial Graph Generation**: Creates a graph from PyBuda modules
2.   **Post-Initial Graph Passes**: Applies initial optimizations
3.   **Constant Evaluation**: Evaluates constant expressions
4.   **Pattern Matching**: Identifies and transforms patterns
5.   **Optimization**: Applies various optimizations
6.   **Autograd**: Adds gradient operations for training
7.   **Pre-Lowering**: Prepares the graph for lowering to Buda operations
8.   **Balancer and Placer**: Determines operation placement on hardware
9.   **Netlist Generation**: Creates a Buda netlist
10.   **Backend Compilation**: Compiles the netlist into a device image

### Balancer and Placement System

The Balancer and Placer systems are critical components that optimize operation placement on hardware resources, ensuring efficient execution of the computational graph.

Sources:

*   [pybuda/csrc/balancer/policies/policy_manager.cpp 16-69](https://github.com/tenstorrent/tt-buda/blob/74755d1f/pybuda/csrc/balancer/policies/policy_manager.cpp#L16-L69) - PolicyManager constructor
*   [pybuda/csrc/balancer/policies/policy_manager.cpp 76-156](https://github.com/tenstorrent/tt-buda/blob/74755d1f/pybuda/csrc/balancer/policies/policy_manager.cpp#L76-L156) - `commit_op` function
*   [pybuda/csrc/balancer/policies/policy_utils.cpp 50-133](https://github.com/tenstorrent/tt-buda/blob/74755d1f/pybuda/csrc/balancer/policies/policy_utils.cpp#L50-L133) - `run_placer` function
*   [pybuda/csrc/balancer/policies/policy_utils.cpp 134-168](https://github.com/tenstorrent/tt-buda/blob/74755d1f/pybuda/csrc/balancer/policies/policy_utils.cpp#L134-L168) - Epoch cost calculation

The Balancer follows these steps:

1.   **Graph Legalization**: Identifies legal operation models based on hardware constraints
2.   **Policy Selection**: Selects the appropriate policy based on graph characteristics
3.   **Operation Placement**: Determines grid shapes and placement for each operation
4.   **Epoch Planning**: Organizes operations into epochs for sequential execution
5.   **Fork-Join Buffering**: Handles fork-join patterns in the graph to ensure efficient execution

The policy type selection is a critical step that determines how operations will be placed on the device. Different policies optimize for different types of workloads (CNN, NLP, etc.).

### Operation Models and Placement

Operation Models (`OpModel`) represent the hardware configuration for executing an operation, including grid shape, block shape, and data format. The Balancer selects optimal operation models for each operation.

Sources:

*   [pybuda/csrc/balancer/policies/policy_utils.hpp 39-145](https://github.com/tenstorrent/tt-buda/blob/74755d1f/pybuda/csrc/balancer/policies/policy_utils.hpp#L39-L145) - EpochSolution class definition
*   [pybuda/csrc/balancer/policies/policy_utils.cpp 169-318](https://github.com/tenstorrent/tt-buda/blob/74755d1f/pybuda/csrc/balancer/policies/policy_utils.cpp#L169-L318) - Placer solution dump utilities

The placement system is responsible for mapping operations to physical locations on the hardware and organizing them into epochs. Key concepts include:

*   **Grid Shape**: The 2D arrangement of cores used by an operation
*   **Block Shape**: The microarchitectural blocking scheme used for data processing
*   **T-Streaming**: A technique to process tensors in a streaming fashion
*   **Epochs**: Sets of operations that execute sequentially

### Backend Integration

The Backend API provides an interface between the compiled representation and the Tenstorrent hardware:

Sources:

*   [pybuda/csrc/backend_api/backend_api.cpp 17-224](https://github.com/tenstorrent/tt-buda/blob/74755d1f/pybuda/csrc/backend_api/backend_api.cpp#L17-L224) - Backend API implementation
*   [pybuda/csrc/backend_api/device_config.hpp 20-283](https://github.com/tenstorrent/tt-buda/blob/74755d1f/pybuda/csrc/backend_api/device_config.hpp#L20-L283) - Device configuration definition
*   [pybuda/pybuda/backend.py](https://github.com/tenstorrent/tt-buda/blob/74755d1f/pybuda/pybuda/backend.py) - Python interface to Backend API

The Backend API manages:

*   Device configuration and parameter retrieval
*   DRAM allocation and memory transfers
*   Input/output queue management
*   Program execution control
*   Device state monitoring

### Fork-Join Buffering

The PyBuda framework includes a mechanism for handling fork-join patterns in the computational graph. The fork-join buffering system ensures efficient execution by adding appropriate buffering operations at fork and join points.

Sources:

*   [pybuda/csrc/passes/fork_join.cpp 61-255](https://github.com/tenstorrent/tt-buda/blob/74755d1f/pybuda/csrc/passes/fork_join.cpp#L61-L255) - Fork-join detection
*   [pybuda/csrc/passes/fork_join.cpp 538-826](https://github.com/tenstorrent/tt-buda/blob/74755d1f/pybuda/csrc/passes/fork_join.cpp#L538-L826) - Buffering calculation and insertion
*   [pybuda/csrc/balancer/policies/policy_ribbon2.cpp 434-456](https://github.com/tenstorrent/tt-buda/blob/74755d1f/pybuda/csrc/balancer/policies/policy_ribbon2.cpp#L434-L456) - Handle fork-join overflow

Fork-join buffering is critical for ensuring that operations in parallel paths are properly synchronized, preventing stalls and optimizing throughput.

## Compilation Stages in Detail

The compilation pipeline transforms a high-level PyBuda module representation into a hardware-optimized netlist through several key stages:

| Stage | Purpose | Key Components |
| --- | --- | --- |
| Initial Graph Generation | Create computational graph from PyBuda modules | Node creation, edge connection, tensor shapes |
| Constant Evaluation | Evaluate constant expressions | Constant folding, shape inference |
| Pattern Matching | Identify and transform patterns | Operation fusion, decomposition |
| Optimization | Apply graph optimizations | Dead code elimination, common subexpr elimination |
| Autograd | Add gradient operations for training | Forward/backward pass linking |
| Pre-Lowering | Prepare for Buda operations | Operation decomposition, format conversion |
| Balancer & Placer | Determine operation placement | Grid shape selection, epoch planning |
| Netlist Generation | Create Buda netlist | Backend-specific operation encoding |

Sources:

*   [pybuda/pybuda/compile.py 205-276](https://github.com/tenstorrent/tt-buda/blob/74755d1f/pybuda/pybuda/compile.py#L205-L276) - Compilation pipeline orchestration
*   [pybuda/pybuda/compile.py 430-467](https://github.com/tenstorrent/tt-buda/blob/74755d1f/pybuda/pybuda/compile.py#L430-L467) - Generate compile results

## Summary

The Core Architecture of PyBuda provides a framework for compiling, optimizing, and executing neural network models on Tenstorrent hardware. The key components include:

1.   **TTDevice**: Interface between user code and Tenstorrent hardware
2.   **Compilation Pipeline**: Transforms PyBuda modules into optimized executable code
3.   **Balancer and Placer**: Optimize operation placement on hardware resources
4.   **Backend API**: Interfaces with the hardware for execution control
5.   **Fork-Join Buffering**: Ensures efficient execution of parallel paths

Understanding these components and their interactions is essential for effectively using PyBuda to deploy neural networks on Tenstorrent hardware.

Dismiss
Refresh this wiki

Enter email to refresh
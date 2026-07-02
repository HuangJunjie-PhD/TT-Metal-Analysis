---
project: tt-buda
github: https://github.com/tenstorrent/tt-buda
deepwiki: https://deepwiki.com/tenstorrent/tt-buda/1-overview
---

# Overview

Relevant source files
*   [pybuda/csrc/backend_api/backend_api.cpp](https://github.com/tenstorrent/tt-buda/blob/74755d1f/pybuda/csrc/backend_api/backend_api.cpp)
*   [pybuda/csrc/backend_api/device_config.hpp](https://github.com/tenstorrent/tt-buda/blob/74755d1f/pybuda/csrc/backend_api/device_config.hpp)
*   [pybuda/csrc/balancer/balancer_config.hpp](https://github.com/tenstorrent/tt-buda/blob/74755d1f/pybuda/csrc/balancer/balancer_config.hpp)
*   [pybuda/csrc/balancer/legalizer/graph_solver_types.hpp](https://github.com/tenstorrent/tt-buda/blob/74755d1f/pybuda/csrc/balancer/legalizer/graph_solver_types.hpp)
*   [pybuda/csrc/balancer/policies/policy_manager.cpp](https://github.com/tenstorrent/tt-buda/blob/74755d1f/pybuda/csrc/balancer/policies/policy_manager.cpp)
*   [pybuda/csrc/balancer/policies/policy_manager.hpp](https://github.com/tenstorrent/tt-buda/blob/74755d1f/pybuda/csrc/balancer/policies/policy_manager.hpp)
*   [pybuda/csrc/balancer/policies/policy_ribbon.cpp](https://github.com/tenstorrent/tt-buda/blob/74755d1f/pybuda/csrc/balancer/policies/policy_ribbon.cpp)
*   [pybuda/csrc/balancer/policies/policy_ribbon.hpp](https://github.com/tenstorrent/tt-buda/blob/74755d1f/pybuda/csrc/balancer/policies/policy_ribbon.hpp)
*   [pybuda/csrc/balancer/policies/policy_ribbon2.cpp](https://github.com/tenstorrent/tt-buda/blob/74755d1f/pybuda/csrc/balancer/policies/policy_ribbon2.cpp)
*   [pybuda/csrc/balancer/policies/policy_utils.cpp](https://github.com/tenstorrent/tt-buda/blob/74755d1f/pybuda/csrc/balancer/policies/policy_utils.cpp)
*   [pybuda/csrc/balancer/policies/policy_utils.hpp](https://github.com/tenstorrent/tt-buda/blob/74755d1f/pybuda/csrc/balancer/policies/policy_utils.hpp)
*   [pybuda/csrc/buda_passes.cpp](https://github.com/tenstorrent/tt-buda/blob/74755d1f/pybuda/csrc/buda_passes.cpp)
*   [pybuda/csrc/passes/commute_utils.cpp](https://github.com/tenstorrent/tt-buda/blob/74755d1f/pybuda/csrc/passes/commute_utils.cpp)
*   [pybuda/csrc/passes/erase_inverse_ops.cpp](https://github.com/tenstorrent/tt-buda/blob/74755d1f/pybuda/csrc/passes/erase_inverse_ops.cpp)
*   [pybuda/csrc/passes/explicate_unsqueeze.cpp](https://github.com/tenstorrent/tt-buda/blob/74755d1f/pybuda/csrc/passes/explicate_unsqueeze.cpp)
*   [pybuda/csrc/passes/fuse_redundant_tm_sequence.cpp](https://github.com/tenstorrent/tt-buda/blob/74755d1f/pybuda/csrc/passes/fuse_redundant_tm_sequence.cpp)
*   [pybuda/csrc/passes/fuse_redundant_tm_sequence.hpp](https://github.com/tenstorrent/tt-buda/blob/74755d1f/pybuda/csrc/passes/fuse_redundant_tm_sequence.hpp)
*   [pybuda/csrc/passes/insert_inverse_outside_quantized_region.cpp](https://github.com/tenstorrent/tt-buda/blob/74755d1f/pybuda/csrc/passes/insert_inverse_outside_quantized_region.cpp)
*   [pybuda/csrc/passes/insert_qdq_on_biases.cpp](https://github.com/tenstorrent/tt-buda/blob/74755d1f/pybuda/csrc/passes/insert_qdq_on_biases.cpp)
*   [pybuda/csrc/passes/make_quantized_ops.cpp](https://github.com/tenstorrent/tt-buda/blob/74755d1f/pybuda/csrc/passes/make_quantized_ops.cpp)
*   [pybuda/csrc/passes/make_quantized_ops.hpp](https://github.com/tenstorrent/tt-buda/blob/74755d1f/pybuda/csrc/passes/make_quantized_ops.hpp)
*   [pybuda/csrc/passes/pre_lowering_passes.cpp](https://github.com/tenstorrent/tt-buda/blob/74755d1f/pybuda/csrc/passes/pre_lowering_passes.cpp)
*   [pybuda/csrc/passes/pre_lowering_passes.hpp](https://github.com/tenstorrent/tt-buda/blob/74755d1f/pybuda/csrc/passes/pre_lowering_passes.hpp)
*   [pybuda/csrc/passes/remove_quant_dequant.cpp](https://github.com/tenstorrent/tt-buda/blob/74755d1f/pybuda/csrc/passes/remove_quant_dequant.cpp)
*   [pybuda/csrc/tt_torch_device/python_bindings.cpp](https://github.com/tenstorrent/tt-buda/blob/74755d1f/pybuda/csrc/tt_torch_device/python_bindings.cpp)
*   [pybuda/csrc/tt_torch_device/torch_device_impl.cpp](https://github.com/tenstorrent/tt-buda/blob/74755d1f/pybuda/csrc/tt_torch_device/torch_device_impl.cpp)
*   [pybuda/csrc/tt_torch_device/tt_device.cpp](https://github.com/tenstorrent/tt-buda/blob/74755d1f/pybuda/csrc/tt_torch_device/tt_device.cpp)
*   [pybuda/csrc/tt_torch_device/tt_device.hpp](https://github.com/tenstorrent/tt-buda/blob/74755d1f/pybuda/csrc/tt_torch_device/tt_device.hpp)
*   [pybuda/pybuda/_C/__init__.pyi](https://github.com/tenstorrent/tt-buda/blob/74755d1f/pybuda/pybuda/_C/__init__.pyi)
*   [pybuda/pybuda/_C/backend_api.pyi](https://github.com/tenstorrent/tt-buda/blob/74755d1f/pybuda/pybuda/_C/backend_api.pyi)
*   [pybuda/pybuda/backend.py](https://github.com/tenstorrent/tt-buda/blob/74755d1f/pybuda/pybuda/backend.py)
*   [pybuda/pybuda/compile.py](https://github.com/tenstorrent/tt-buda/blob/74755d1f/pybuda/pybuda/compile.py)
*   [pybuda/pybuda/module.py](https://github.com/tenstorrent/tt-buda/blob/74755d1f/pybuda/pybuda/module.py)
*   [pybuda/pybuda/op/eval/pybuda/quantize.py](https://github.com/tenstorrent/tt-buda/blob/74755d1f/pybuda/pybuda/op/eval/pybuda/quantize.py)
*   [pybuda/pybuda/tensor.py](https://github.com/tenstorrent/tt-buda/blob/74755d1f/pybuda/pybuda/tensor.py)
*   [pybuda/pybuda/torch_compile.py](https://github.com/tenstorrent/tt-buda/blob/74755d1f/pybuda/pybuda/torch_compile.py)
*   [pybuda/pybuda/ttdevice.py](https://github.com/tenstorrent/tt-buda/blob/74755d1f/pybuda/pybuda/ttdevice.py)

The PyBuda framework is a compilation and execution system for deploying machine learning models on Tenstorrent hardware accelerators. It provides a seamless interface for translating models from popular frameworks like PyTorch, TensorFlow, and ONNX to optimized executables for Tenstorrent's unique dataflow architecture. This document provides a high-level overview of the framework's architecture, components, and compilation flow.

For detailed information about specific components, please refer to the corresponding sections in the wiki. For more information about the Core Architecture, see [Core Architecture](https://deepwiki.com/tenstorrent/tt-buda/2-core-architecture), and for the Balancer and Placement system, see [Balancer and Placement System](https://deepwiki.com/tenstorrent/tt-buda/2.2-balancer-and-placement-system).

## System Architecture

PyBuda follows a multi-stage compilation pipeline that transforms user models into efficient hardware executables. The system architecture involves several key components working together to optimize model execution.

Sources:

*   [pybuda/pybuda/ttdevice.py 45-166](https://github.com/tenstorrent/tt-buda/blob/74755d1f/pybuda/pybuda/ttdevice.py#L45-L166)
*   [pybuda/pybuda/torch_compile.py 432-446](https://github.com/tenstorrent/tt-buda/blob/74755d1f/pybuda/pybuda/torch_compile.py#L432-L446)
*   [pybuda/pybuda/compile.py 204-342](https://github.com/tenstorrent/tt-buda/blob/74755d1f/pybuda/pybuda/compile.py#L204-L342)

## Compilation Flow

The PyBuda compilation pipeline transforms high-level model descriptions into optimized hardware executables through a series of well-defined stages. This process handles both the logical structure of operations and their physical placement on the target hardware.

Sources:

*   [pybuda/pybuda/compile.py 219-236](https://github.com/tenstorrent/tt-buda/blob/74755d1f/pybuda/pybuda/compile.py#L219-L236)
*   [pybuda/csrc/buda_passes.cpp 95-127](https://github.com/tenstorrent/tt-buda/blob/74755d1f/pybuda/csrc/buda_passes.cpp#L95-L127)
*   [pybuda/csrc/buda_passes.cpp 225-250](https://github.com/tenstorrent/tt-buda/blob/74755d1f/pybuda/csrc/buda_passes.cpp#L225-L250)

The compilation process involves several key stages:

1.   **Frontend Compilation**: Translates models from frameworks like PyTorch/TensorFlow into a PyBuda module
2.   **Graph Generation**: Creates the initial computational graph
3.   **Graph Optimization**: Applies passes like pattern matching, constant evaluation, and operation fusion
4.   **Balancing**: Selects optimal operation models and grid shapes
5.   **Placement**: Assigns operations to specific locations on the hardware grid
6.   **Netlist Generation**: Creates a Buda netlist (hardware-specific representation)
7.   **Backend Compilation**: Compiles the netlist into a device executable image

Sources:

*   [pybuda/pybuda/compile.py 277-318](https://github.com/tenstorrent/tt-buda/blob/74755d1f/pybuda/pybuda/compile.py#L277-L318)
*   [pybuda/pybuda/torch_compile.py 184-237](https://github.com/tenstorrent/tt-buda/blob/74755d1f/pybuda/pybuda/torch_compile.py#L184-L237)

## Key Components

### TTDevice

The `TTDevice` class is the central user-facing component that represents one or more Tenstorrent hardware devices. It manages the placement of PyBuda modules, handles compilation, and controls execution.

`# Example usage of TTDevicett0 = pybuda.TTDevice("tt0", arch=pybuda.BackendDevice.Wormhole_B0)tt0.place_module(pybuda.module.PyTorchModule("my_model", model))`
Key responsibilities include:

*   Managing chip configurations and allocations
*   Providing an interface for module placement
*   Orchestrating the compilation pipeline
*   Managing device execution and queues

Sources:

*   [pybuda/pybuda/ttdevice.py 45-166](https://github.com/tenstorrent/tt-buda/blob/74755d1f/pybuda/pybuda/ttdevice.py#L45-L166)
*   [pybuda/pybuda/ttdevice.py 253-261](https://github.com/tenstorrent/tt-buda/blob/74755d1f/pybuda/pybuda/ttdevice.py#L253-L261)

### Graph and Compilation

The compilation system transforms user models through several graph representations:

| Stage | Representation | Purpose |
| --- | --- | --- |
| Initial | PyBuda Graph | High-level representation of the user model |
| Optimized | Optimized Graph | After pattern matching and optimization passes |
| Lowered | Buda Graph | Hardware-specific representation with placement info |
| Final | Buda Netlist | Executable representation for hardware |

Key components of the graph compilation:

1.   **Graph Generation**: Creates a computational graph from PyBuda modules
2.   **Optimization Passes**: Apply transformations like operator fusion and pattern matching
3.   **Autograd**: Automatically adds backward ops for training (if enabled)
4.   **Lowering**: Transforms the graph into hardware-specific operations

Sources:

*   [pybuda/pybuda/compile.py 204-246](https://github.com/tenstorrent/tt-buda/blob/74755d1f/pybuda/pybuda/compile.py#L204-L246)
*   [pybuda/csrc/buda_passes.cpp 95-127](https://github.com/tenstorrent/tt-buda/blob/74755d1f/pybuda/csrc/buda_passes.cpp#L95-L127)
*   [pybuda/csrc/buda_passes.cpp 129-211](https://github.com/tenstorrent/tt-buda/blob/74755d1f/pybuda/csrc/buda_passes.cpp#L129-L211)

### Balancer and Placer

The Balancer and Placer systems are crucial for efficient hardware utilization. They determine how operations are mapped to physical hardware resources.

Sources:

*   [pybuda/csrc/balancer/policies/policy_manager.cpp 16-58](https://github.com/tenstorrent/tt-buda/blob/74755d1f/pybuda/csrc/balancer/policies/policy_manager.cpp#L16-L58)
*   [pybuda/csrc/balancer/policies/policy_utils.cpp 50-133](https://github.com/tenstorrent/tt-buda/blob/74755d1f/pybuda/csrc/balancer/policies/policy_utils.cpp#L50-L133)
*   [pybuda/csrc/balancer/policies/policy_ribbon2.cpp 31-93](https://github.com/tenstorrent/tt-buda/blob/74755d1f/pybuda/csrc/balancer/policies/policy_ribbon2.cpp#L31-L93)

The Balancer works through these key steps:

1.   **Policy Selection**: Chooses a policy (Ribbon, CNN, NLP, etc.) based on the graph structure
2.   **Operation Models**: Selects optimal configurations for each operation
3.   **Interactive Placement**: Progressively places operations on the device grid
4.   **Epoch Planning**: Groups operations into execution epochs
5.   **Fork-Join Buffering**: Adds buffering for data dependencies between operations

Sources:

*   [pybuda/csrc/balancer/policies/policy_utils.hpp 138-440](https://github.com/tenstorrent/tt-buda/blob/74755d1f/pybuda/csrc/balancer/policies/policy_utils.hpp#L138-L440)
*   [pybuda/csrc/balancer/policies/policy_ribbon2.cpp 99-132](https://github.com/tenstorrent/tt-buda/blob/74755d1f/pybuda/csrc/balancer/policies/policy_ribbon2.cpp#L99-L132)

### Backend and Hardware Execution

The backend system handles the final translation from Buda netlist to hardware execution:

1.   **Backend Compilation**: Converts the Buda netlist into hardware-specific code
2.   **Device Configuration**: Sets up chip configurations and memory allocations
3.   **Execution**: Loads and runs the compiled program on Tenstorrent hardware
4.   **I/O Management**: Handles data transfers between host and device

Sources:

*   [pybuda/csrc/tt_torch_device/tt_device.cpp 40-77](https://github.com/tenstorrent/tt-buda/blob/74755d1f/pybuda/csrc/tt_torch_device/tt_device.cpp#L40-L77)
*   [pybuda/csrc/tt_torch_device/tt_device.cpp 282-304](https://github.com/tenstorrent/tt-buda/blob/74755d1f/pybuda/csrc/tt_torch_device/tt_device.cpp#L282-L304)
*   [pybuda/pybuda/backend.py 19-83](https://github.com/tenstorrent/tt-buda/blob/74755d1f/pybuda/pybuda/backend.py#L19-L83)

## Framework Integration and Model Support

PyBuda provides integration with popular ML frameworks:

| Framework | Integration Method | Key Function |
| --- | --- | --- |
| PyTorch | torch_compile | Converts PyTorch models to PyBudaModules |
| TensorFlow | tf_compile | Converts TensorFlow models to PyBudaModules |
| ONNX | onnx_compile | Converts ONNX models to PyBudaModules |

Example of PyTorch integration:

`# Convert PyTorch model to PyBudafrom pybuda import PyBudaModule, TTDeviceimport torch # Create PyTorch modelmodel = MyTorchModel() # Create PyBuda device and modulett0 = TTDevice("tt0")pybuda_module = PyBudaModule("my_model", model)tt0.place_module(pybuda_module) # Compile and runoutput = pybuda.run_inference(tt0, inputs)`
Sources:

*   [pybuda/pybuda/torch_compile.py 432-446](https://github.com/tenstorrent/tt-buda/blob/74755d1f/pybuda/pybuda/torch_compile.py#L432-L446)
*   [pybuda/pybuda/torch_compile.py 448-502](https://github.com/tenstorrent/tt-buda/blob/74755d1f/pybuda/pybuda/torch_compile.py#L448-L502)

## Conclusion

The PyBuda framework provides a comprehensive solution for deploying ML models on Tenstorrent hardware. It handles the complex process of transforming high-level model descriptions into optimized hardware executables through a multi-stage compilation pipeline. The framework's key components - TTDevice, Graph Compilation, Balancer, Placer, and Backend - work together to optimize performance while abstracting hardware complexities from the user.

By supporting multiple input frameworks and providing sophisticated optimization techniques, PyBuda enables efficient execution of a wide range of ML models on Tenstorrent's unique dataflow architecture.

Sources:

*   [pybuda/pybuda/ttdevice.py 45-50](https://github.com/tenstorrent/tt-buda/blob/74755d1f/pybuda/pybuda/ttdevice.py#L45-L50)
*   [pybuda/pybuda/compile.py 277-288](https://github.com/tenstorrent/tt-buda/blob/74755d1f/pybuda/pybuda/compile.py#L277-L288)

Dismiss
Refresh this wiki

Enter email to refresh
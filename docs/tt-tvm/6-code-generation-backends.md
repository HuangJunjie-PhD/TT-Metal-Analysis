---
project: tt-tvm
github: https://github.com/tenstorrent/tt-tvm
deepwiki: https://deepwiki.com/tenstorrent/tt-tvm/6-code-generation-backends
---

# Code Generation Backends

Relevant source files
*   [include/tvm/runtime/profiling.h](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/include/tvm/runtime/profiling.h)
*   [include/tvm/runtime/vm/executable.h](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/include/tvm/runtime/vm/executable.h)
*   [include/tvm/runtime/vm/vm.h](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/include/tvm/runtime/vm/vm.h)
*   [python/tvm/contrib/debugger/debug_executor.py](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/python/tvm/contrib/debugger/debug_executor.py)
*   [python/tvm/relay/backend/vm.py](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/python/tvm/relay/backend/vm.py)
*   [python/tvm/runtime/packed_func.py](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/python/tvm/runtime/packed_func.py)
*   [python/tvm/runtime/profiler_vm.py](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/python/tvm/runtime/profiler_vm.py)
*   [python/tvm/runtime/profiling/__init__.py](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/python/tvm/runtime/profiling/__init__.py)
*   [python/tvm/runtime/vm.py](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/python/tvm/runtime/vm.py)
*   [src/relay/backend/vm/compiler.cc](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/src/relay/backend/vm/compiler.cc)
*   [src/relay/backend/vm/compiler.h](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/src/relay/backend/vm/compiler.h)
*   [src/runtime/graph_executor/debug/graph_executor_debug.cc](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/src/runtime/graph_executor/debug/graph_executor_debug.cc)
*   [src/runtime/profiling.cc](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/src/runtime/profiling.cc)
*   [src/runtime/vm/executable.cc](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/src/runtime/vm/executable.cc)
*   [src/runtime/vm/profiler/vm.cc](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/src/runtime/vm/profiler/vm.cc)
*   [src/runtime/vm/profiler/vm.h](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/src/runtime/vm/profiler/vm.h)
*   [src/runtime/vm/vm.cc](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/src/runtime/vm/vm.cc)
*   [src/target/llvm/codegen_amdgpu.cc](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/src/target/llvm/codegen_amdgpu.cc)
*   [src/target/llvm/codegen_arm.cc](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/src/target/llvm/codegen_arm.cc)
*   [src/target/llvm/codegen_blob.cc](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/src/target/llvm/codegen_blob.cc)
*   [src/target/llvm/codegen_blob.h](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/src/target/llvm/codegen_blob.h)
*   [src/target/llvm/codegen_cpu.cc](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/src/target/llvm/codegen_cpu.cc)
*   [src/target/llvm/codegen_cpu.h](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/src/target/llvm/codegen_cpu.h)
*   [src/target/llvm/codegen_hexagon.cc](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/src/target/llvm/codegen_hexagon.cc)
*   [src/target/llvm/codegen_llvm.cc](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/src/target/llvm/codegen_llvm.cc)
*   [src/target/llvm/codegen_llvm.h](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/src/target/llvm/codegen_llvm.h)
*   [src/target/llvm/codegen_nvptx.cc](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/src/target/llvm/codegen_nvptx.cc)
*   [src/target/llvm/codegen_x86_64.cc](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/src/target/llvm/codegen_x86_64.cc)
*   [src/target/llvm/llvm_module.cc](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/src/target/llvm/llvm_module.cc)
*   [tests/python/all-platform-minimal-test/README.md](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/tests/python/all-platform-minimal-test/README.md?plain=1)
*   [tests/python/all-platform-minimal-test/test_minimal_target_codegen_llvm.py](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/tests/python/all-platform-minimal-test/test_minimal_target_codegen_llvm.py)
*   [tests/python/relay/test_vm.py](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/tests/python/relay/test_vm.py)
*   [tests/python/relay/test_vm_serialization.py](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/tests/python/relay/test_vm_serialization.py)
*   [tests/python/unittest/test_runtime_profiling.py](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/tests/python/unittest/test_runtime_profiling.py)
*   [tests/python/unittest/test_runtime_vm_profiler.py](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/tests/python/unittest/test_runtime_vm_profiler.py)
*   [tests/python/unittest/test_target_codegen_llvm.py](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/tests/python/unittest/test_target_codegen_llvm.py)

## Purpose and Scope

This page provides an overview of TVM's code generation backends that transform optimized TensorIR (TIR) programs into executable code for various hardware targets. Code generation is the final stage of TVM's compilation pipeline, where low-level IR is converted into machine code or bytecode.

**Related Pages:**

*   For TensorIR concepts and scheduling, see [TensorIR (TIR) System](https://deepwiki.com/tenstorrent/tt-tvm/5-tensorir-(tir)-system)
*   For runtime execution of generated code, see [Runtime and Execution](https://deepwiki.com/tenstorrent/tt-tvm/7-runtime-and-execution)
*   For LLVM backend details, see [LLVM Backend Architecture](https://deepwiki.com/tenstorrent/tt-tvm/6.1-llvm-backend-architecture)
*   For VM bytecode compilation, see [Virtual Machine Backend](https://deepwiki.com/tenstorrent/tt-tvm/6.7-virtual-machine-backend)

**Scope:** This page covers the backend layer responsible for:

*   LLVM-based code generation for CPUs, GPUs, and specialized processors
*   Virtual Machine bytecode compilation for dynamic execution
*   Target-specific optimizations and intrinsics
*   Memory management and buffer allocation in generated code

## Backend Architecture Overview

TVM's code generation system supports two primary backend categories:

**Sources:**

*   [src/target/llvm/codegen_llvm.cc 106-142](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/src/target/llvm/codegen_llvm.cc#L106-L142)
*   [src/relay/backend/vm/compiler.cc 240-292](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/src/relay/backend/vm/compiler.cc#L240-L292)

### Code Generation Workflow

The general workflow for code generation follows these stages:

1.   **Initialization**: Backend is configured with target specifications
2.   **Function Declaration**: Generate function signatures and interfaces
3.   **Code Emission**: Traverse TIR statements and expressions, emitting target-specific code
4.   **Optimization**: Apply target-specific optimization passes
5.   **Finalization**: Package code into executable module format

**Sources:**

*   [src/target/llvm/codegen_llvm.cc 144-373](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/src/target/llvm/codegen_llvm.cc#L144-L373)
*   [src/relay/backend/vm/compiler.cc 249-292](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/src/relay/backend/vm/compiler.cc#L249-L292)

## LLVM-Based Code Generation

The LLVM backend hierarchy provides a unified framework for generating native code across multiple hardware platforms. The `CodeGenLLVM` base class implements common functionality, with platform-specific subclasses adding target-specific features.

### Class Hierarchy and Specialization

**Key Classes:**

*   `CodeGenLLVM`: Base class in [src/target/llvm/codegen_llvm.h 106](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/src/target/llvm/codegen_llvm.h#L106-L106)
*   `CodeGenCPU`: CPU backend in [src/target/llvm/codegen_cpu.h 38](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/src/target/llvm/codegen_cpu.h#L38-L38)
*   `CodeGenNVPTX`: NVIDIA GPU backend in [src/target/llvm/codegen_nvptx.cc 70](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/src/target/llvm/codegen_nvptx.cc#L70-L70)
*   `CodeGenAMDGPU`: AMD GPU backend in [src/target/llvm/codegen_amdgpu.cc 48](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/src/target/llvm/codegen_amdgpu.cc#L48-L48)
*   `CodeGenHexagon`: Hexagon DSP backend in [src/target/llvm/codegen_hexagon.cc 73](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/src/target/llvm/codegen_hexagon.cc#L73-L73)

**Sources:**

*   [src/target/llvm/codegen_llvm.h 1-750](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/src/target/llvm/codegen_llvm.h#L1-L750)
*   [src/target/llvm/codegen_cpu.cc 1-1200](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/src/target/llvm/codegen_cpu.cc#L1-L1200)
*   [src/target/llvm/codegen_nvptx.cc 1-450](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/src/target/llvm/codegen_nvptx.cc#L1-L450)
*   [src/target/llvm/codegen_amdgpu.cc 1-350](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/src/target/llvm/codegen_amdgpu.cc#L1-L350)
*   [src/target/llvm/codegen_hexagon.cc 1-500](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/src/target/llvm/codegen_hexagon.cc#L1-L500)

### Factory Method and Target Selection

Backend instantiation uses a factory pattern that selects the appropriate code generator based on the target triple:

**Sources:**

*   [src/target/llvm/codegen_llvm.cc 126-142](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/src/target/llvm/codegen_llvm.cc#L126-L142)

### LLVM Module Compilation Pipeline

The `LLVMModuleNode` class manages the complete compilation pipeline from TIR to executable code:

| Stage | Method | Description |
| --- | --- | --- |
| **Initialization** | `Init(IRModule, Target)` | Creates `CodeGenLLVM` instance, configures target |
| **Code Generation** | `AddFunctionsOrdered()` | Generates LLVM IR for all functions |
| **Verification** | `Verify()` | Validates LLVM IR correctness |
| **Optimization** | `Optimize()` | Applies LLVM optimization passes |
| **Output** | `SaveToFile()` / `GetSource()` | Emits object files, assembly, or IR |

**Sources:**

*   [src/target/llvm/llvm_module.cc 91-361](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/src/target/llvm/llvm_module.cc#L91-L361)

For detailed information on:

*   LLVM backend architecture and internal structure → see [LLVM Backend Architecture](https://deepwiki.com/tenstorrent/tt-tvm/6.1-llvm-backend-architecture)
*   CPU-specific code generation and optimizations → see [CPU Code Generation](https://deepwiki.com/tenstorrent/tt-tvm/6.2-cpu-code-generation)
*   GPU code generation (CUDA/ROCm) → see [GPU Code Generation](https://deepwiki.com/tenstorrent/tt-tvm/6.3-gpu-code-generation)
*   Hexagon and other specialized targets → see [Specialized Target Code Generation](https://deepwiki.com/tenstorrent/tt-tvm/6.4-specialized-target-code-generation)
*   LLVM optimization passes → see [LLVM Optimization Pipeline](https://deepwiki.com/tenstorrent/tt-tvm/6.5-llvm-optimization-pipeline)
*   Memory allocation and buffer management → see [Memory Management in Codegen](https://deepwiki.com/tenstorrent/tt-tvm/6.6-memory-management-in-codegen)

## Virtual Machine Backend

The VM backend compiles Relay IR into bytecode for dynamic execution, supporting control flow and dynamic shapes that may be difficult to optimize statically in LLVM.

### VM Compilation Architecture

**Sources:**

*   [src/relay/backend/vm/compiler.cc 240-800](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/src/relay/backend/vm/compiler.cc#L240-L800)
*   [src/runtime/vm/vm.cc 1-900](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/src/runtime/vm/vm.cc#L1-L900)

### VM Code Generation Process

The `VMFunctionCompiler` traverses Relay expressions and emits VM instructions:

| Relay Expression | VM Instructions | Purpose |
| --- | --- | --- |
| `ConstantNode` | `LoadConst` | Load constant from constant pool |
| `TupleNode` | `AllocADT` | Allocate algebraic data type |
| `TupleGetItemNode` | `GetField` | Extract tuple field |
| `IfNode` | `If`, `Goto` | Conditional branching |
| `CallNode` (global) | `Invoke` / `AllocClosure` | Function call or closure allocation |
| `CallNode` (op) | `InvokePacked` | Call packed primitive function |
| Memory ops | `AllocStorage`, `AllocTensor` | Dynamic memory allocation |

**Key Methods:**

*   `VMCompiler::Compile()`: Entry point in [src/relay/backend/vm/compiler.cc 871-951](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/src/relay/backend/vm/compiler.cc#L871-L951)
*   `VMFunctionCompiler::Compile()`: Function compilation in [src/relay/backend/vm/compiler.cc 249-292](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/src/relay/backend/vm/compiler.cc#L249-L292)
*   `VMFunctionCompiler::VisitExpr_()`: Expression visitors in [src/relay/backend/vm/compiler.cc 368-708](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/src/relay/backend/vm/compiler.cc#L368-L708)

**Sources:**

*   [src/relay/backend/vm/compiler.cc 240-800](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/src/relay/backend/vm/compiler.cc#L240-L800)

### VM Instruction Set

The VM defines 50+ instruction types for computation, control flow, and memory management:

**Sources:**

*   [include/tvm/runtime/vm/executable.h 157-217](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/include/tvm/runtime/vm/executable.h#L157-L217)

### VM Executable Format

The `Executable` class encapsulates the compiled VM program:

| Component | Type | Description |
| --- | --- | --- |
| **functions** | `std::vector<VMFunction>` | Compiled functions with bytecode |
| **global_map** | `std::unordered_map<std::string, Index>` | Function name → index mapping |
| **constants** | `std::vector<NDArray>` | Constant data pool |
| **primitive_map** | `std::unordered_map<std::string, Index>` | Primitive name → index |
| **virtual_devices** | `std::vector<Device>` | Device specifications |
| **lib** | `runtime::Module` | Compiled primitive kernels (LLVM module) |

**Serialization:**

*   Binary format: [src/runtime/vm/executable.cc 347-565](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/src/runtime/vm/executable.cc#L347-L565)
*   Deserialization: [src/runtime/vm/executable.cc 567-790](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/src/runtime/vm/executable.cc#L567-L790)

**Sources:**

*   [include/tvm/runtime/vm/executable.h 1-300](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/include/tvm/runtime/vm/executable.h#L1-L300)
*   [src/runtime/vm/executable.cc 1-900](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/src/runtime/vm/executable.cc#L1-L900)

For detailed information on:

*   VM bytecode compilation details → see [Virtual Machine Backend](https://deepwiki.com/tenstorrent/tt-tvm/6.7-virtual-machine-backend)
*   Complete instruction set reference → see [VM Instruction Set](https://deepwiki.com/tenstorrent/tt-tvm/6.8-vm-instruction-set)
*   Executable format and serialization → see [VM Executable Format](https://deepwiki.com/tenstorrent/tt-tvm/6.9-vm-executable-format)

## Backend Selection and Integration

### Target Configuration

Backends are selected based on the `Target` specification provided during compilation:

**Example Usage:**

**Sources:**

*   [python/tvm/relay/backend/vm.py 37-60](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/python/tvm/relay/backend/vm.py#L37-L60)

### Primitive Function Compilation

Both LLVM and VM backends compile primitive operations (kernels) into efficient code:

**LLVM Backend:**

1.   TIR functions are lowered to LLVM IR
2.   Target-specific intrinsics are emitted
3.   LLVM optimization passes are applied
4.   Native code is generated (.so, .ptx, etc.)

**VM Backend:**

1.   Primitives are compiled via LLVM backend
2.   VM stores primitive functions in `lib` module
3.   `InvokePacked` instruction calls compiled primitives at runtime
4.   Primitive index is stored in `primitive_map`

**Sources:**

*   [src/relay/backend/vm/compiler.cc 479-527](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/src/relay/backend/vm/compiler.cc#L479-L527)
*   [src/target/llvm/codegen_llvm.cc 232-298](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/src/target/llvm/codegen_llvm.cc#L232-L298)

### Profiling Integration

Both backends integrate with TVM's profiling infrastructure:

**LLVM Backend:**

*   Uses `Timer::Start()` for per-operation timing
*   Hardware counters via PAPI when available
*   Debug information for source-level profiling

**VM Backend:**

*   `VirtualMachineDebug` variant for profiled execution
*   Per-instruction timing in [src/runtime/vm/profiler/vm.cc 1-300](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/src/runtime/vm/profiler/vm.cc#L1-L300)
*   Instruction-level metrics collection

**Sources:**

*   [src/runtime/profiling.cc 46-167](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/src/runtime/profiling.cc#L46-L167)
*   [src/runtime/vm/profiler/vm.cc 1-300](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/src/runtime/vm/profiler/vm.cc#L1-L300)

Dismiss
Refresh this wiki

Enter email to refresh
---
project: tt-forge-onnx
github: https://github.com/tenstorrent/tt-forge-onnx
deepwiki: https://deepwiki.com/tenstorrent/tt-forge-onnx
---

# Overview

Relevant source files
*   [forge/csrc/forge_bindings.cpp](https://github.com/tenstorrent/tt-forge-onnx/blob/e519819c/forge/csrc/forge_bindings.cpp)
*   [forge/csrc/passes/lower_to_mlir.cpp](https://github.com/tenstorrent/tt-forge-onnx/blob/e519819c/forge/csrc/passes/lower_to_mlir.cpp)
*   [forge/csrc/passes/mlir_compiler.cpp](https://github.com/tenstorrent/tt-forge-onnx/blob/e519819c/forge/csrc/passes/mlir_compiler.cpp)
*   [forge/csrc/passes/mlir_compiler.hpp](https://github.com/tenstorrent/tt-forge-onnx/blob/e519819c/forge/csrc/passes/mlir_compiler.hpp)
*   [forge/csrc/passes/mlir_passes.cpp](https://github.com/tenstorrent/tt-forge-onnx/blob/e519819c/forge/csrc/passes/mlir_passes.cpp)
*   [forge/csrc/passes/mlir_passes.hpp](https://github.com/tenstorrent/tt-forge-onnx/blob/e519819c/forge/csrc/passes/mlir_passes.hpp)
*   [forge/forge/compile.py](https://github.com/tenstorrent/tt-forge-onnx/blob/e519819c/forge/forge/compile.py)
*   [forge/forge/compiled_graph_state.py](https://github.com/tenstorrent/tt-forge-onnx/blob/e519819c/forge/forge/compiled_graph_state.py)
*   [forge/forge/forge_property_utils.py](https://github.com/tenstorrent/tt-forge-onnx/blob/e519819c/forge/forge/forge_property_utils.py)
*   [forge/forge/module.py](https://github.com/tenstorrent/tt-forge-onnx/blob/e519819c/forge/forge/module.py)
*   [forge/forge/python_codegen.py](https://github.com/tenstorrent/tt-forge-onnx/blob/e519819c/forge/forge/python_codegen.py)
*   [forge/forge/tensor.py](https://github.com/tenstorrent/tt-forge-onnx/blob/e519819c/forge/forge/tensor.py)
*   [forge/forge/tvm_calls/forge_compile.py](https://github.com/tenstorrent/tt-forge-onnx/blob/e519819c/forge/forge/tvm_calls/forge_compile.py)
*   [forge/forge/tvm_calls/forge_utils.py](https://github.com/tenstorrent/tt-forge-onnx/blob/e519819c/forge/forge/tvm_calls/forge_utils.py)
*   [forge/forge/tvm_utils.py](https://github.com/tenstorrent/tt-forge-onnx/blob/e519819c/forge/forge/tvm_utils.py)
*   [forge/forge/verify/config.py](https://github.com/tenstorrent/tt-forge-onnx/blob/e519819c/forge/forge/verify/config.py)
*   [forge/forge/verify/verify.py](https://github.com/tenstorrent/tt-forge-onnx/blob/e519819c/forge/forge/verify/verify.py)

This page introduces tt-forge-onnx: its purpose, the overall compilation architecture, and the major code components involved. It is the starting point for understanding how models move from a user-facing framework to execution on Tenstorrent hardware.

For setup and installation instructions, see [Getting Started](https://deepwiki.com/tenstorrent/tt-forge-onnx/2-getting-started). For a detailed walkthrough of each compilation stage, see [Core Compilation System](https://deepwiki.com/tenstorrent/tt-forge-onnx/3-core-compilation-system).

* * *

## What is tt-forge-onnx

tt-forge-onnx is a **framework-agnostic graph compiler frontend** for Tenstorrent hardware. Its primary job is to accept a trained model expressed in any supported ML framework (PyTorch, TensorFlow, ONNX, TFLite, Jax, PaddlePaddle), compile it through a multi-stage lowering pipeline, and produce a binary that runs on Tenstorrent silicon.

The project is designed for single-chip systems only, and it is a component of the broader [TT-Forge](https://docs.tenstorrent.com/tt-forge/) ecosystem, sitting on top of the [TT-MLIR](https://docs.tenstorrent.com/tt-mlir/) backend.

Key design goals:

*   Accept models from any major ML framework without framework-specific user code at compile time
*   Use Apache TVM Relay as a common intermediate representation between framework frontends
*   Lower the compiled graph to MLIR using the TTIR and TTNN dialects from `tt-mlir`
*   Produce a Flatbuffer binary executed directly on Tenstorrent devices via the `tt-metal` runtime

* * *

## Architecture Overview

**Diagram: End-to-End Compilation Architecture with Code Entities**

Sources: [forge/forge/compile.py 182-260](https://github.com/tenstorrent/tt-forge-onnx/blob/e519819c/forge/forge/compile.py#L182-L260)[forge/forge/module.py 79-146](https://github.com/tenstorrent/tt-forge-onnx/blob/e519819c/forge/forge/module.py#L79-L146)[forge/forge/tvm_calls/forge_compile.py 137-249](https://github.com/tenstorrent/tt-forge-onnx/blob/e519819c/forge/forge/tvm_calls/forge_compile.py#L137-L249)[forge/csrc/passes/lower_to_mlir.cpp 178-259](https://github.com/tenstorrent/tt-forge-onnx/blob/e519819c/forge/csrc/passes/lower_to_mlir.cpp#L178-L259)[forge/csrc/passes/mlir_compiler.cpp 70-228](https://github.com/tenstorrent/tt-forge-onnx/blob/e519819c/forge/csrc/passes/mlir_compiler.cpp#L70-L228)[forge/forge/compiled_graph_state.py 165-221](https://github.com/tenstorrent/tt-forge-onnx/blob/e519819c/forge/forge/compiled_graph_state.py#L165-L221)

* * *

## Key Components

The table below maps the conceptual role of each component to its primary code entity.

| Role | Primary Code Entity | Location |
| --- | --- | --- |
| Python package entry point | `forge.compile`, `forge.verify` | `forge/forge/__init__.py` |
| Main compilation orchestration | `compile_main()`, `forge_compile_from_context()`, `CompileContext` | `forge/forge/compile.py` |
| Framework module wrappers | `PyTorchModule`, `OnnxModule`, `TFModule`, `JaxModule`, `PaddleModule` | `forge/forge/module.py` |
| TVM Relay compilation | `compile_tvm_graph()`, `compile_for_forge()`, `partition_for_forge()` | `forge/forge/tvm_calls/forge_compile.py` |
| Relay graph transforms | `ConvertLayout`, `DecomposeStack`, `ResolveConvChannels`, `DFPatternCallback` | `forge/forge/tvm_calls/relay/op/forge_passes.py` |
| TVM → Forge op translation | `tvm_to_pytorch_op_map`, `pytorch_ops_needing_arguments`, `ForgeWriter` | `forge/forge/tvm_to_python.py``forge/forge/python_codegen.py` |
| C++ computation graph | `ForgeGraphModule`, `graphlib::Graph`, `graphlib::OpNode` | `forge/csrc/forge_graph_module.hpp` |
| C++ graph optimization passes | `run_post_initial_graph_passes()`, `run_optimization_graph_passes()`, `run_consteval_graph_pass()` | `forge/csrc/forge_passes.cpp` |
| Forge → TTIR lowering | `MLIRGenerator::emit_mlir()`, `emit_mlir_tt_forge_operation()` | `forge/csrc/passes/lower_to_mlir.cpp` |
| Operation handler registry | `lowering_handler_map`, `init_lowering_handler_map()` | `forge/csrc/passes/lower_to_mlir.cpp:783-828` |
| Attribute type conversion | `AttributeMapper`, `convert_to_mlir_attribute()` | `forge/csrc/passes/lower_to_mlir.cpp:84-382` |
| MLIR pass configuration | `MLIRConfig`, `config_to_pipeline_options()` | `forge/csrc/passes/mlir_compiler.hpp``forge/csrc/passes/mlir_passes.cpp` |
| MLIR compilation pipeline | `run_mlir_compiler()`, `run_mlir_passes()`, `PassManager` | `forge/csrc/passes/mlir_compiler.cpp``forge/csrc/passes/mlir_passes.cpp` |
| Compiled model state | `CompiledModel`, `CompiledGraphState`, `ModelState` | `forge/forge/compiled_graph_state.py` |
| Execution stage tracking | `ExecutionStage`, `ForgePropertyHandler`, `record_execution()` | `forge/forge/forge_property_utils.py:279-347` |
| Verification system | `verify()`, `VerifyConfig`, `AutomaticValueChecker` | `forge/forge/verify/verify.py``forge/forge/verify/config.py` |
| Tensor abstractions | `Tensor`, `TensorFromPytorch`, `TensorFromTrace`, `TensorShape` | `forge/forge/tensor.py:34-495` |
| Hardware device management | `TTDevice`, `TTSystem`, `tt_device.cpp` | `forge/csrc/runtime/tt_device.hpp` |
| Python-C++ bindings | `PYBIND11_MODULE(_C)`, `OpsModule()`, `GraphModule()`, `RuntimeModule()` | `forge/csrc/forge_bindings.cpp:42-248` |

Sources: [forge/csrc/forge_bindings.cpp 42-248](https://github.com/tenstorrent/tt-forge-onnx/blob/e519819c/forge/csrc/forge_bindings.cpp#L42-L248)[forge/forge/compile.py 112-154](https://github.com/tenstorrent/tt-forge-onnx/blob/e519819c/forge/forge/compile.py#L112-L154)[forge/csrc/passes/mlir_compiler.hpp 26-76](https://github.com/tenstorrent/tt-forge-onnx/blob/e519819c/forge/csrc/passes/mlir_compiler.hpp#L26-L76)[forge/csrc/passes/lower_to_mlir.cpp 84-828](https://github.com/tenstorrent/tt-forge-onnx/blob/e519819c/forge/csrc/passes/lower_to_mlir.cpp#L84-L828)[forge/forge/forge_property_utils.py 279-347](https://github.com/tenstorrent/tt-forge-onnx/blob/e519819c/forge/forge/forge_property_utils.py#L279-L347)[forge/forge/compiled_graph_state.py 46-157](https://github.com/tenstorrent/tt-forge-onnx/blob/e519819c/forge/forge/compiled_graph_state.py#L46-L157)

* * *

## Compilation Pipeline in Detail

**Diagram: Compilation Stages with Execution Stage Tracking**

Sources: [forge/forge/compile.py 262-340](https://github.com/tenstorrent/tt-forge-onnx/blob/e519819c/forge/forge/compile.py#L262-L340)[forge/forge/forge_property_utils.py 279-308](https://github.com/tenstorrent/tt-forge-onnx/blob/e519819c/forge/forge/forge_property_utils.py#L279-L308)[forge/csrc/passes/mlir_compiler.cpp 70-228](https://github.com/tenstorrent/tt-forge-onnx/blob/e519819c/forge/csrc/passes/mlir_compiler.cpp#L70-L228)[forge/csrc/passes/lower_to_mlir.cpp 178-259](https://github.com/tenstorrent/tt-forge-onnx/blob/e519819c/forge/csrc/passes/lower_to_mlir.cpp#L178-L259)[forge/csrc/passes/mlir_passes.cpp 80-136](https://github.com/tenstorrent/tt-forge-onnx/blob/e519819c/forge/csrc/passes/mlir_passes.cpp#L80-L136)[forge/forge/verify/verify.py 420-560](https://github.com/tenstorrent/tt-forge-onnx/blob/e519819c/forge/forge/verify/verify.py#L420-L560)

### Stage 1 — Framework Ingestion

The user calls `forge.compile(model, sample_inputs=inputs)`. The `model` argument can be a native PyTorch `nn.Module`, an ONNX `ModelProto`, a TFLite flatbuffer, or several other framework types. `compile_main()` in [forge/forge/compile.py 182-260](https://github.com/tenstorrent/tt-forge-onnx/blob/e519819c/forge/forge/compile.py#L182-L260) wraps the model in a corresponding `Module` subclass (`PyTorchModule`, `OnnxModule`, `TFLiteModule`, `JaxModule`, `PaddleModule`) via `wrap_module()`[forge/forge/module.py 586-624](https://github.com/tenstorrent/tt-forge-onnx/blob/e519819c/forge/forge/module.py#L586-L624) A `CompileContext` dataclass [forge/forge/compile.py 112-148](https://github.com/tenstorrent/tt-forge-onnx/blob/e519819c/forge/forge/compile.py#L112-L148) is created to track all compilation state. The `ExecutionStage` is set to `FAILED_TVM_RELAY_IRMODULE_GENERATION` via `record_execution()`[forge/forge/forge_property_utils.py 718-726](https://github.com/tenstorrent/tt-forge-onnx/blob/e519819c/forge/forge/forge_property_utils.py#L718-L726) to track progress through the compilation pipeline.

Sources: [forge/forge/compile.py 182-260](https://github.com/tenstorrent/tt-forge-onnx/blob/e519819c/forge/forge/compile.py#L182-L260)[forge/forge/module.py 586-624](https://github.com/tenstorrent/tt-forge-onnx/blob/e519819c/forge/forge/module.py#L586-L624)[forge/forge/forge_property_utils.py 279-308](https://github.com/tenstorrent/tt-forge-onnx/blob/e519819c/forge/forge/forge_property_utils.py#L279-L308)

### Stage 2 — TVM Relay Lowering

`compile_tvm_graph()`[forge/forge/tvm_calls/forge_compile.py 137-249](https://github.com/tenstorrent/tt-forge-onnx/blob/e519819c/forge/forge/tvm_calls/forge_compile.py#L137-L249) converts the framework model into an Apache TVM Relay computational graph. Framework-specific frontends (`compile_pytorch_for_forge()`, `compile_onnx_for_forge()`, `compile_tf_for_forge()`, etc.) use TVM's `from_pytorch()`, `from_onnx()`, or `from_tensorflow()` importers.

A sequence of `DFPatternCallback` subclasses transform the Relay graph, including `ConvertLayout`, `DecomposeStack`, `ResolveConvChannels`, `ConvertPyTorchWeightBiasLayout`, and many others. These callbacks are applied via `compile_for_forge()` and `partition_for_forge()` in the TVM compilation pipeline.

The TVM Relay graph is then translated to executable Python code via `tvm_to_python.py` and `python_codegen.py`. The `ForgeWriter` class [forge/forge/python_codegen.py 119-292](https://github.com/tenstorrent/tt-forge-onnx/blob/e519819c/forge/forge/python_codegen.py#L119-L292) generates a Python module defining a `ForgeModule` subclass with parameters and a `forward()` method. `tvm_to_pytorch_op_map` maps TVM Relay op names to Forge operations, and `pytorch_ops_needing_arguments` specifies argument conversion logic for each operation.

Sources: [forge/forge/tvm_calls/forge_compile.py 137-249](https://github.com/tenstorrent/tt-forge-onnx/blob/e519819c/forge/forge/tvm_calls/forge_compile.py#L137-L249)[forge/forge/python_codegen.py 119-292](https://github.com/tenstorrent/tt-forge-onnx/blob/e519819c/forge/forge/python_codegen.py#L119-L292)[forge/forge/tvm_to_python.py 1-300](https://github.com/tenstorrent/tt-forge-onnx/blob/e519819c/forge/forge/tvm_to_python.py#L1-L300)

### Stage 3 — C++ Graph Passes

The generated Python code is executed to create a `ForgeGraphModule`[forge/csrc/forge_graph_module.hpp 1-50](https://github.com/tenstorrent/tt-forge-onnx/blob/e519819c/forge/csrc/forge_graph_module.hpp#L1-L50) holding one or more `graphlib::Graph` objects [forge/csrc/graph_lib/graph.hpp](https://github.com/tenstorrent/tt-forge-onnx/blob/e519819c/forge/csrc/graph_lib/graph.hpp) Each graph contains `graphlib::Node` subclasses (`OpNode`, `InputNode`, `OutputNode`, `ConstantNode`, `ParameterNode`).

A sequence of C++ optimization passes operates on the graph:

*   `run_post_initial_graph_passes()` — Initial graph cleanup and normalization
*   `run_consteval_graph_pass()` — Constant folding and evaluation [forge/csrc/passes/consteval.cpp](https://github.com/tenstorrent/tt-forge-onnx/blob/e519819c/forge/csrc/passes/consteval.cpp)
*   `run_optimization_graph_passes()` — High-level optimizations
*   `run_post_autograd_graph_passes()` — Post-differentiation cleanup (training only)
*   `run_pre_lowering_passes()` — Pre-MLIR preparation including data format overrides

These passes are exposed to Python via pybind11 bindings in [forge/csrc/forge_bindings.cpp 208-217](https://github.com/tenstorrent/tt-forge-onnx/blob/e519819c/forge/csrc/forge_bindings.cpp#L208-L217) The `ExecutionStage` tracking records which stage each compilation reaches before potential failure.

Sources: [forge/csrc/forge_bindings.cpp 208-217](https://github.com/tenstorrent/tt-forge-onnx/blob/e519819c/forge/csrc/forge_bindings.cpp#L208-L217)[forge/forge/compile.py 278-340](https://github.com/tenstorrent/tt-forge-onnx/blob/e519819c/forge/forge/compile.py#L278-L340)[forge/forge/forge_property_utils.py 279-308](https://github.com/tenstorrent/tt-forge-onnx/blob/e519819c/forge/forge/forge_property_utils.py#L279-L308)

### Stage 4 — TTIR MLIR Emission

`lower_to_mlir()` constructs an `MLIRGenerator` instance [forge/csrc/passes/lower_to_mlir.cpp 178-183](https://github.com/tenstorrent/tt-forge-onnx/blob/e519819c/forge/csrc/passes/lower_to_mlir.cpp#L178-L183) and calls `emit_mlir()`[forge/csrc/passes/lower_to_mlir.cpp 185-259](https://github.com/tenstorrent/tt-forge-onnx/blob/e519819c/forge/csrc/passes/lower_to_mlir.cpp#L185-L259) The generator creates an `mlir::ModuleOp` with a `SystemDescAttr`[forge/csrc/passes/lower_to_mlir.cpp 639-658](https://github.com/tenstorrent/tt-forge-onnx/blob/e519819c/forge/csrc/passes/lower_to_mlir.cpp#L639-L658) describing the target hardware configuration.

For each graph in the `ForgeGraphModule`, `emit_mlir_function()`[forge/csrc/passes/lower_to_mlir.cpp 388-509](https://github.com/tenstorrent/tt-forge-onnx/blob/e519819c/forge/csrc/passes/lower_to_mlir.cpp#L388-L509) creates an `mlir::func::FuncOp` with input arguments (activations, constants, parameters) and return values. The function body is populated by walking the graph in topological order and calling `emit_mlir_tt_forge_operation()`[forge/csrc/passes/lower_to_mlir.cpp 512-530](https://github.com/tenstorrent/tt-forge-onnx/blob/e519819c/forge/csrc/passes/lower_to_mlir.cpp#L512-L530) for each node.

Each Forge operation is dispatched via `lowering_handler_map`[forge/csrc/passes/lower_to_mlir.cpp 783-828](https://github.com/tenstorrent/tt-forge-onnx/blob/e519819c/forge/csrc/passes/lower_to_mlir.cpp#L783-L828) — a `std::map<std::string, HandlerType>` mapping Forge op names (e.g., `"add"`, `"matmul"`, `"conv2d"`) to handler functions that emit the corresponding TTIR operations (e.g., `mlir::tt::ttir::AddOp`, `mlir::tt::ttir::MatmulOp`).

Forge op attributes are converted to MLIR attribute types via `AttributeMapper`[forge/csrc/passes/lower_to_mlir.cpp 84-176](https://github.com/tenstorrent/tt-forge-onnx/blob/e519819c/forge/csrc/passes/lower_to_mlir.cpp#L84-L176) which defines per-op remapping rules. For example:

*   `softmax` op: rename attribute `dim` to `dimension`
*   `index` op: rename `start` to `begin`, `stop` to `end`, `stride` to `step`, and cast all to `I32Attr`
*   `cumsum` op: cast `dim` to `I64Attr`
*   Convolution ops: cast stride, padding, dilation to `DenseI32ArrayAttr`

The `symbolTable_`[forge/csrc/passes/lower_to_mlir.cpp 274](https://github.com/tenstorrent/tt-forge-onnx/blob/e519819c/forge/csrc/passes/lower_to_mlir.cpp#L274-L274) maps Forge node names to `mlir::Value` objects for operand lookup during graph traversal.

Sources: [forge/csrc/passes/lower_to_mlir.cpp 178-828](https://github.com/tenstorrent/tt-forge-onnx/blob/e519819c/forge/csrc/passes/lower_to_mlir.cpp#L178-L828)

### Stage 5 — MLIR Pass Pipeline

`run_mlir_passes()`[forge/csrc/passes/mlir_passes.cpp 80-152](https://github.com/tenstorrent/tt-forge-onnx/blob/e519819c/forge/csrc/passes/mlir_passes.cpp#L80-L152) applies the TTNN pipeline to lower TTIR operations to device-specific TTNN operations. The function:

1.   Calls `register_mlir_passes()`[forge/csrc/passes/mlir_passes.cpp 24-41](https://github.com/tenstorrent/tt-forge-onnx/blob/e519819c/forge/csrc/passes/mlir_passes.cpp#L24-L41) to register TTIR and TTNN passes and pipelines
2.   Creates an `mlir::PassManager`[forge/csrc/passes/mlir_passes.cpp 87](https://github.com/tenstorrent/tt-forge-onnx/blob/e519819c/forge/csrc/passes/mlir_passes.cpp#L87-L87)
3.   Looks up the `"ttir-to-ttnn-backend-pipeline"` from the MLIR pipeline registry [forge/csrc/passes/mlir_passes.cpp 96-108](https://github.com/tenstorrent/tt-forge-onnx/blob/e519819c/forge/csrc/passes/mlir_passes.cpp#L96-L108)
4.   Converts `MLIRConfig` to pipeline options string via `config_to_pipeline_options()`[forge/csrc/passes/mlir_passes.cpp 43-78](https://github.com/tenstorrent/tt-forge-onnx/blob/e519819c/forge/csrc/passes/mlir_passes.cpp#L43-L78)
5.   Adds the pipeline to the pass manager with options [forge/csrc/passes/mlir_passes.cpp 126](https://github.com/tenstorrent/tt-forge-onnx/blob/e519819c/forge/csrc/passes/mlir_passes.cpp#L126-L126)
6.   Runs the pass manager on the MLIR module [forge/csrc/passes/mlir_passes.cpp 133](https://github.com/tenstorrent/tt-forge-onnx/blob/e519819c/forge/csrc/passes/mlir_passes.cpp#L133-L133)

`MLIRConfig`[forge/csrc/passes/mlir_compiler.hpp 27-76](https://github.com/tenstorrent/tt-forge-onnx/blob/e519819c/forge/csrc/passes/mlir_compiler.hpp#L27-L76) controls optional compilation features:

*   `enable_consteval` — Constant evaluation optimizations
*   `enable_optimizer` — General optimization passes
*   `enable_memory_layout_analysis` — Memory layout analysis
*   `enable_fusing` — Operation fusion
*   `enable_fusing_conv2d_with_multiply_pattern` — Conv2d+multiply pattern fusion
*   `custom_config` — String for additional pass options

Sources: [forge/csrc/passes/mlir_passes.cpp 24-152](https://github.com/tenstorrent/tt-forge-onnx/blob/e519819c/forge/csrc/passes/mlir_passes.cpp#L24-L152)[forge/csrc/passes/mlir_compiler.hpp 27-76](https://github.com/tenstorrent/tt-forge-onnx/blob/e519819c/forge/csrc/passes/mlir_compiler.hpp#L27-L76)

### Stage 6 — Binary Generation and Runtime

After the MLIR pass pipeline completes, one of three output generators is invoked:

| Function | Output | Description |
| --- | --- | --- |
| `run_mlir_compiler()` | `runtime::Binary` | Flatbuffer binary via `ttnnToFlatbuffer()` |
| `run_mlir_compiler_to_cpp()` | C++ source string | C++ code via `emitc::translateToCpp()` |
| `run_mlir_compiler_to_shared_object()` | `.so` file path | Compiled shared object via `compileCppToSo()` |

The default path uses `ttnnToFlatbuffer()`[forge/csrc/passes/mlir_compiler.cpp 142](https://github.com/tenstorrent/tt-forge-onnx/blob/e519819c/forge/csrc/passes/mlir_compiler.cpp#L142-L142) to serialize the TTNN MLIR module to a `runtime::Binary` Flatbuffer.

The binary is wrapped in a `CompiledModel` object [forge/forge/compiled_graph_state.py 165-221](https://github.com/tenstorrent/tt-forge-onnx/blob/e519819c/forge/forge/compiled_graph_state.py#L165-L221) which contains:

*   `fwd_compiled_graph_state` — `CompiledGraphState` with input/output names and consteval traces [forge/forge/compiled_graph_state.py 46-140](https://github.com/tenstorrent/tt-forge-onnx/blob/e519819c/forge/forge/compiled_graph_state.py#L46-L140)
*   `bwd_compiled_graph_state` — Backward pass state (training only)
*   `opt_compiled_graph_state` — Optimizer pass state (training only)
*   `compiled_binary` — The `runtime::Binary` Flatbuffer
*   `runtime_model_state` — `ModelState` object for device execution [forge/csrc/runtime/runtime_types.hpp](https://github.com/tenstorrent/tt-forge-onnx/blob/e519819c/forge/csrc/runtime/runtime_types.hpp)
*   `tensor_pool` — `TensorPool` for tensor memory management

At inference time, `CompiledModel.__call__()`[forge/forge/compiled_graph_state.py 267-300](https://github.com/tenstorrent/tt-forge-onnx/blob/e519819c/forge/forge/compiled_graph_state.py#L267-L300) converts inputs to `CTensor` objects and dispatches them to the device via `TTDevice`[forge/csrc/runtime/tt_device.hpp](https://github.com/tenstorrent/tt-forge-onnx/blob/e519819c/forge/csrc/runtime/tt_device.hpp) The `forward()` method [forge/forge/compiled_graph_state.py 302-324](https://github.com/tenstorrent/tt-forge-onnx/blob/e519819c/forge/forge/compiled_graph_state.py#L302-L324) executes the compiled binary on hardware and returns results.

The `verify()` function [forge/forge/verify/verify.py 420-560](https://github.com/tenstorrent/tt-forge-onnx/blob/e519819c/forge/forge/verify/verify.py#L420-L560) can be called after compilation to compare `CompiledModel` outputs against the original framework model, checking dtype, shape, and numerical accuracy via `VerifyConfig`[forge/forge/verify/config.py 92-246](https://github.com/tenstorrent/tt-forge-onnx/blob/e519819c/forge/forge/verify/config.py#L92-L246)

Sources: [forge/csrc/passes/mlir_compiler.cpp 70-239](https://github.com/tenstorrent/tt-forge-onnx/blob/e519819c/forge/csrc/passes/mlir_compiler.cpp#L70-L239)[forge/csrc/forge_bindings.cpp 218-228](https://github.com/tenstorrent/tt-forge-onnx/blob/e519819c/forge/csrc/forge_bindings.cpp#L218-L228)[forge/forge/compiled_graph_state.py 165-385](https://github.com/tenstorrent/tt-forge-onnx/blob/e519819c/forge/forge/compiled_graph_state.py#L165-L385)[forge/forge/verify/verify.py 420-560](https://github.com/tenstorrent/tt-forge-onnx/blob/e519819c/forge/forge/verify/verify.py#L420-L560)

* * *

## Framework Support

The following source frameworks are accepted by `forge.compile`:

| Framework | Wrapper Class | Input Type |
| --- | --- | --- |
| PyTorch | `PyTorchModule` | `torch.nn.Module` |
| ONNX | `OnnxModule` | `onnx.ModelProto` |
| TensorFlow | `TFModule` | TF SavedModel / graph |
| TFLite | `TFLiteModule` | TFLite flatbuffer |
| TF GraphDef | `TFGraphDefModule` | `tf.GraphDef` |
| Jax | `JaxModule` | Jax function |
| PaddlePaddle | `PaddleModule` | Paddle model |

All framework paths converge at the TVM Relay stage; downstream stages (MLIR lowering, device execution) are identical regardless of the original framework.

Sources: [forge/forge/tvm_to_python.py 416-469](https://github.com/tenstorrent/tt-forge-onnx/blob/e519819c/forge/forge/tvm_to_python.py#L416-L469)[docs/src/getting_started.md 1-25](https://github.com/tenstorrent/tt-forge-onnx/blob/e519819c/docs/src/getting_started.md?plain=1#L1-L25)[docs/src/introduction.md 1-15](https://github.com/tenstorrent/tt-forge-onnx/blob/e519819c/docs/src/introduction.md?plain=1#L1-L15)

* * *

## Third-Party Dependencies

**Diagram: External Library Integration**

| Dependency | Role |
| --- | --- |
| Apache TVM | Relay IR frontend; framework model ingestion and graph transformation |
| `tt-mlir` | TTIR / TTNN MLIR dialects; compiler passes; Flatbuffer serialization |
| `tt-metal` | Hardware runtime; `TTDevice`, `TTSystem`, kernel execution |
| PyTorch / libtorch | Tensor types for op evaluation; Python bindings via pybind11 |
| LLVM / MLIR | Base IR infrastructure; dialect infrastructure; LLVM translation |

Sources: [third_party/CMakeLists.txt 1-46](https://github.com/tenstorrent/tt-forge-onnx/blob/e519819c/third_party/CMakeLists.txt#L1-L46)[forge/csrc/CMakeLists.txt 10-32](https://github.com/tenstorrent/tt-forge-onnx/blob/e519819c/forge/csrc/CMakeLists.txt#L10-L32)[forge/csrc/passes/mlir_compiler.cpp 52-63](https://github.com/tenstorrent/tt-forge-onnx/blob/e519819c/forge/csrc/passes/mlir_compiler.cpp#L52-L63)

* * *

## Minimal Usage Example

The canonical usage pattern is:

`forge.compile` returns a `CompiledModel`. Calling it runs the Flatbuffer binary on the attached Tenstorrent device. The `forge.verify` function (see [Verification System](https://deepwiki.com/tenstorrent/tt-forge-onnx/3.4-verification-system)) can be used alongside `forge.compile` to confirm numerical accuracy against the original framework model.

Sources: [docs/src/getting_started_docker.md 94-115](https://github.com/tenstorrent/tt-forge-onnx/blob/e519819c/docs/src/getting_started_docker.md?plain=1#L94-L115)[docs/src/getting_started.md 53-100](https://github.com/tenstorrent/tt-forge-onnx/blob/e519819c/docs/src/getting_started.md?plain=1#L53-L100)

Dismiss
Refresh this wiki

Enter email to refresh
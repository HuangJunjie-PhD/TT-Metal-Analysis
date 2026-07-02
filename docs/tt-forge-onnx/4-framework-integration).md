---
project: tt-forge-onnx
github: https://github.com/tenstorrent/tt-forge-onnx
deepwiki: https://deepwiki.com/tenstorrent/tt-forge-onnx/4-framework-integration)
---

# Framework Integration

Relevant source files
*   [forge/csrc/ops/CMakeLists.txt](https://github.com/tenstorrent/tt-forge-onnx/blob/e519819c/forge/csrc/ops/CMakeLists.txt)
*   [forge/csrc/ops/op.cpp](https://github.com/tenstorrent/tt-forge-onnx/blob/e519819c/forge/csrc/ops/op.cpp)
*   [forge/csrc/ops/op.hpp](https://github.com/tenstorrent/tt-forge-onnx/blob/e519819c/forge/csrc/ops/op.hpp)
*   [forge/csrc/ops/op_interface.hpp](https://github.com/tenstorrent/tt-forge-onnx/blob/e519819c/forge/csrc/ops/op_interface.hpp)
*   [forge/csrc/ops/python_bindings.cpp](https://github.com/tenstorrent/tt-forge-onnx/blob/e519819c/forge/csrc/ops/python_bindings.cpp)
*   [forge/forge/op/__init__.py](https://github.com/tenstorrent/tt-forge-onnx/blob/e519819c/forge/forge/op/__init__.py)
*   [forge/forge/op/eltwise_binary.py](https://github.com/tenstorrent/tt-forge-onnx/blob/e519819c/forge/forge/op/eltwise_binary.py)
*   [forge/forge/op/eltwise_unary.py](https://github.com/tenstorrent/tt-forge-onnx/blob/e519819c/forge/forge/op/eltwise_unary.py)
*   [forge/forge/tvm_calls/relay/op/forge.py](https://github.com/tenstorrent/tt-forge-onnx/blob/e519819c/forge/forge/tvm_calls/relay/op/forge.py)
*   [forge/forge/tvm_calls/relay/op/forge_passes.py](https://github.com/tenstorrent/tt-forge-onnx/blob/e519819c/forge/forge/tvm_calls/relay/op/forge_passes.py)
*   [forge/forge/tvm_calls/relay/op/utils.py](https://github.com/tenstorrent/tt-forge-onnx/blob/e519819c/forge/forge/tvm_calls/relay/op/utils.py)
*   [forge/forge/tvm_to_python.py](https://github.com/tenstorrent/tt-forge-onnx/blob/e519819c/forge/forge/tvm_to_python.py)
*   [forge/test/mlir/operators/eltwise_binary/test_eltwise_binary.py](https://github.com/tenstorrent/tt-forge-onnx/blob/e519819c/forge/test/mlir/operators/eltwise_binary/test_eltwise_binary.py)
*   [forge/test/mlir/operators/eltwise_unary/test_eltwise_unary.py](https://github.com/tenstorrent/tt-forge-onnx/blob/e519819c/forge/test/mlir/operators/eltwise_unary/test_eltwise_unary.py)
*   [forge/test/models/onnx/vision/ssdlite320_mobilenetv3/test_ssdlite320_mobilenetv3_onnx.py](https://github.com/tenstorrent/tt-forge-onnx/blob/e519819c/forge/test/models/onnx/vision/ssdlite320_mobilenetv3/test_ssdlite320_mobilenetv3_onnx.py)
*   [forge/test/models/test_model_parts.py](https://github.com/tenstorrent/tt-forge-onnx/blob/e519819c/forge/test/models/test_model_parts.py)

This page describes how tt-forge-onnx accepts models from multiple ML frameworks and the abstraction layers that normalize them into a common internal representation. It covers the `Module` wrapper hierarchy, how Apache TVM Relay serves as the universal intermediate representation, and the high-level flow from a framework model to a `ForgeGraphModule`.

For details on specific aspects:

*   TVM Relay compilation pipeline and graph transformation passes: see [TVM Integration](https://deepwiki.com/tenstorrent/tt-forge-onnx/4.1-tvm-integration-and-relay-ir)
*   The complete `Module` class hierarchy and framework-specific tensor handling: see [Framework Support](https://deepwiki.com/tenstorrent/tt-forge-onnx/4.2-multi-framework-support)
*   How TVM graph nodes are translated into Forge operations: see [Operation Translation](https://deepwiki.com/tenstorrent/tt-forge-onnx/4.3-operation-translation-(tvm-to-python))

* * *

## Supported Frameworks

tt-forge-onnx accepts models from six ML frameworks. Each has a corresponding `Module` wrapper and a dedicated TVM compilation path.

| Framework | Module Class | Framework ID String | TVM Compile Function |
| --- | --- | --- | --- |
| PyTorch | `PyTorchModule` | `"pytorch"` | `compile_pytorch_for_forge` |
| TensorFlow | `TFModule` | `"tensorflow"` | `compile_tensorflow_for_forge` |
| TF GraphDef | `TFGraphDefModule` | `"tensorflow"` | `compile_tensorflow_for_forge` |
| TF Lite | `TFLiteModule` | `"tflite"` | `compile_tflite_for_forge` |
| ONNX | `OnnxModule` | `"onnx"` | `compile_onnx_for_forge` |
| PaddlePaddle | `PaddleModule` | `"paddle"` | `compile_paddle_for_forge` |
| JAX | `JaxModule` | `"jax"` | `compile_jax_for_forge` |

The `Framework` enum in `forge/forge/forge_property_utils.py` tracks which framework a model originated from for metadata/reporting purposes. This is separate from the string identifiers used at runtime in `compile_tvm_graph`.

Sources: [forge/forge/tvm_calls/forge_compile.py 137-200](https://github.com/tenstorrent/tt-forge-onnx/blob/e519819c/forge/forge/tvm_calls/forge_compile.py#L137-L200)[forge/forge/forge_property_utils.py 38-44](https://github.com/tenstorrent/tt-forge-onnx/blob/e519819c/forge/forge/forge_property_utils.py#L38-L44)

* * *

## Module Abstraction Layer

All framework models are wrapped in a subclass of the abstract `Module` base class before entering the compilation pipeline. This provides a uniform interface regardless of source framework.

**Module Class Hierarchy**

Sources: [forge/forge/module.py 38-150](https://github.com/tenstorrent/tt-forge-onnx/blob/e519819c/forge/forge/module.py#L38-L150)[forge/forge/verify/verify.py 12-13](https://github.com/tenstorrent/tt-forge-onnx/blob/e519819c/forge/forge/verify/verify.py#L12-L13)[forge/forge/tvm_to_python.py 15-16](https://github.com/tenstorrent/tt-forge-onnx/blob/e519819c/forge/forge/tvm_to_python.py#L15-L16)

The `wrap_module` utility function, imported in `forge/forge/compile.py`, converts a raw framework model into the appropriate `FrameworkModule` subclass automatically when `forge.compile` is called directly with a non-`Module` object.

Sources: [forge/forge/compile.py 34](https://github.com/tenstorrent/tt-forge-onnx/blob/e519819c/forge/forge/compile.py#L34-L34)

* * *

## Framework Ingestion Flow

The following diagram shows the end-to-end path from a raw framework model to the `ForgeGraphModule` that the rest of the compilation pipeline operates on.

**Framework Model to ForgeGraphModule Pipeline**

Sources: [forge/forge/tvm_calls/forge_compile.py 79-200](https://github.com/tenstorrent/tt-forge-onnx/blob/e519819c/forge/forge/tvm_calls/forge_compile.py#L79-L200)[forge/forge/tvm_calls/relay/op/forge_passes.py 1-215](https://github.com/tenstorrent/tt-forge-onnx/blob/e519819c/forge/forge/tvm_calls/relay/op/forge_passes.py#L1-L215)

* * *

## TVM Relay as Universal Intermediate Representation

All six framework paths converge on Apache TVM's `relay.IRModule` as a common graph representation. Each framework-specific compile function is responsible for:

1.   Tracing or scripting the model to produce a `relay.IRModule`
2.   Providing a parameter dictionary (weights) as TVM `NDArray` objects
3.   Flattening any structured inputs/outputs into flat tensor lists

TVM then applies the `forge_passes` (described in [TVM Integration](https://deepwiki.com/tenstorrent/tt-forge-onnx/4.1-tvm-integration-and-relay-ir)) to this relay module before handing it off to `partition_for_forge` and `compile_for_forge`.

The resulting JSON graph representations are stored in two global dicts:

| Dict | Purpose |
| --- | --- |
| `dev_json_graph` | Operations assigned to Tenstorrent hardware |
| `cpu_json_graph` | Operations run on CPU (e.g., `forge_cpudevice.*` ops) |

Both are populated via TVM-registered callbacks `retrieve_forge_json_graph` and `retrieve_forge_cpudevice_json_graph`.

Sources: [forge/forge/tvm_calls/forge_compile.py 52-77](https://github.com/tenstorrent/tt-forge-onnx/blob/e519819c/forge/forge/tvm_calls/forge_compile.py#L52-L77)

* * *

## Relay Graph Transformation Passes

Before partitioning, the Relay IR is transformed by a set of `DFPatternCallback` subclasses defined in `forge/forge/tvm_calls/relay/op/forge_passes.py`. These operate as rewrite rules on subgraph patterns.

| Class | Purpose |
| --- | --- |
| `ConvertLayout` | Rewrites `NHWC` conv/pool ops with `HWIO`/`HWOI` kernels to use `OIHW` kernel layout |
| `ResolveConvChannels` | Eliminates double-transpose wrapping around conv/pool ops, switching data layout instead |
| `FuseConvAndPoolPadding` | Folds `nn.pad` ops immediately preceding `nn.conv2d` or `nn.max_pool2d` into the op's padding attribute |
| `DecomposeRoll` | Converts `gather` ops that implement a roll/shift into `strided_slice` + `concatenate` |
| `DecomposeReverse` | Converts `reverse` ops into `adv_index` (for axis 0) or transpose + `adv_index` + transpose sequences |
| `DecomposeDynamicResize2d` | Converts `dyn.image.resize2d` to a static `image.resize2d` where the output size can be inferred |

Sources: [forge/forge/tvm_calls/relay/op/forge_passes.py 19-400](https://github.com/tenstorrent/tt-forge-onnx/blob/e519819c/forge/forge/tvm_calls/relay/op/forge_passes.py#L19-L400)

* * *

## Operation Translation Layer

After TVM graph partitioning, the JSON graph representation is walked node-by-node to produce a `ForgeModule` in Python. Three lookup tables in `forge/forge/tvm_to_python.py` drive this translation:

**Operation Translation Tables in `tvm_to_python.py`**

Sources: [forge/forge/tvm_to_python.py 416-549](https://github.com/tenstorrent/tt-forge-onnx/blob/e519819c/forge/forge/tvm_to_python.py#L416-L549)

### Key Translation Entries

| TVM Relay Op | Forge Op Name | PyTorch Function |
| --- | --- | --- |
| `nn.softmax` | `softmax` | `torch.nn.functional.softmax` |
| `nn.dense` | `linear` | `torch.nn.functional.linear` |
| `nn.batch_matmul` | `matmul` | `torch.matmul` |
| `strided_slice` | `index_select` | `torch.index_select` |
| `scatter_elements` | `scatter_add` | `torch.scatter_add` |
| `nn.log_softmax` | `log_softmax` | `torch.nn.functional.log_softmax` |
| `reshape` | `reshape` | `torch.reshape` |
| `transpose` | `transpose` | `torch.transpose` |
| `take` | `embedding` | `torch.embedding` |

Ops that carry TVM attributes (e.g., axis, keepdims, dtype) use a dedicated `populate_torch_*_args` function to translate those attributes into the corresponding Python arguments. For example, `populate_torch_softmax_args` extracts the `axis` attribute and maps it to `dim`.

Sources: [forge/forge/tvm_to_python.py 416-549](https://github.com/tenstorrent/tt-forge-onnx/blob/e519819c/forge/forge/tvm_to_python.py#L416-L549)[forge/forge/tvm_to_python.py 160-170](https://github.com/tenstorrent/tt-forge-onnx/blob/e519819c/forge/forge/tvm_to_python.py#L160-L170)

* * *

## Tensor Handling Across Frameworks

The `forge/forge/tensor.py` module provides conversion utilities that normalize framework-native tensor types into PyTorch `torch.Tensor` for internal processing.

| Utility Function | Converts From |
| --- | --- |
| `to_pt_tensors` | Any framework tensor → `torch.Tensor` list |
| `to_tf_tensors` | `torch.Tensor` → `tf.Tensor` list |
| `to_pd_tensors` | Any tensor → `paddle.Tensor` list |
| `pytorch_dtype_to_forge_dataformat` | `torch.dtype` → Forge `DataFormat` |
| `forge_dataformat_to_pytorch_dtype` | Forge `DataFormat` → `torch.dtype` |

The `Tensor` base class and its subclasses (`TensorFromPytorch`, `TensorFromTrace`) wrap these conversions so that framework tensors can be passed through the compilation pipeline uniformly.

Sources: [forge/forge/tensor.py 1-35](https://github.com/tenstorrent/tt-forge-onnx/blob/e519819c/forge/forge/tensor.py#L1-L35)[forge/forge/tensor.py 155-260](https://github.com/tenstorrent/tt-forge-onnx/blob/e519819c/forge/forge/tensor.py#L155-L260)

The `clone_framework_tensors` function in `forge/forge/verify/verify.py` handles framework-aware deep copies during verification, supporting `torch.Tensor`, `tf.Tensor`, `tf.Variable`, `paddle.Tensor`, `jax.Array`, and `keras` variables.

Sources: [forge/forge/verify/verify.py 50-88](https://github.com/tenstorrent/tt-forge-onnx/blob/e519819c/forge/forge/verify/verify.py#L50-L88)

* * *

## Entry Point: `forge.compile`

The `forge.compile` function in `forge/forge/compile.py` is the single public entry point for all framework models. It accepts either a `Module` subclass or a raw framework model object, wraps it via `wrap_module` if needed, and begins the `CompileContext`-driven pipeline.

The `compile_tvm_graph` dispatch in `forge_compile.py` uses the `framework` string field to route to the correct framework-specific function. This string is determined from the `Module` subclass type or passed explicitly.

Sources: [forge/forge/compile.py 1-50](https://github.com/tenstorrent/tt-forge-onnx/blob/e519819c/forge/forge/compile.py#L1-L50)[forge/forge/tvm_calls/forge_compile.py 137-200](https://github.com/tenstorrent/tt-forge-onnx/blob/e519819c/forge/forge/tvm_calls/forge_compile.py#L137-L200)

This wiki is featured in the [repository](https://github.com/tenstorrent/tt-forge-onnx/blob/main/README.md)

Dismiss
Refresh this wiki

Enter email to refresh
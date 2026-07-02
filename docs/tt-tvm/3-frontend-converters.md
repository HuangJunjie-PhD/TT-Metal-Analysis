---
project: tt-tvm
github: https://github.com/tenstorrent/tt-tvm
deepwiki: https://deepwiki.com/tenstorrent/tt-tvm/3-frontend-converters
---

# Frontend Converters

Relevant source files
*   [enable.sh](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/enable.sh)
*   [install.sh](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/install.sh)
*   [python/tvm/relay/frontend/common.py](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/python/tvm/relay/frontend/common.py)
*   [python/tvm/relay/frontend/mxnet.py](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/python/tvm/relay/frontend/mxnet.py)
*   [python/tvm/relay/frontend/onnx.py](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/python/tvm/relay/frontend/onnx.py)
*   [python/tvm/relay/frontend/pytorch.py](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/python/tvm/relay/frontend/pytorch.py)
*   [python/tvm/relay/frontend/pytorch_utils.py](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/python/tvm/relay/frontend/pytorch_utils.py)
*   [python/tvm/relay/frontend/qnn_torch.py](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/python/tvm/relay/frontend/qnn_torch.py)
*   [python/tvm/relay/frontend/tensorflow.py](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/python/tvm/relay/frontend/tensorflow.py)
*   [python/tvm/relay/frontend/tensorflow2.py](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/python/tvm/relay/frontend/tensorflow2.py)
*   [python/tvm/relay/frontend/tensorflow2_ops.py](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/python/tvm/relay/frontend/tensorflow2_ops.py)
*   [python/tvm/relay/frontend/tensorflow_ops.py](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/python/tvm/relay/frontend/tensorflow_ops.py)
*   [python/tvm/relay/frontend/tflite.py](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/python/tvm/relay/frontend/tflite.py)
*   [python/tvm/relay/frontend/tflite_flexbuffer.py](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/python/tvm/relay/frontend/tflite_flexbuffer.py)
*   [python/tvm/relay/testing/tf.py](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/python/tvm/relay/testing/tf.py)
*   [src/relay/printer/relay_text_printer.cc](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/src/relay/printer/relay_text_printer.cc)
*   [src/relay/printer/text_printer.h](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/src/relay/printer/text_printer.h)
*   [tests/python/frontend/mxnet/test_forward.py](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/tests/python/frontend/mxnet/test_forward.py)
*   [tests/python/frontend/onnx/test_forward.py](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/tests/python/frontend/onnx/test_forward.py)
*   [tests/python/frontend/pytorch/qnn_test.py](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/tests/python/frontend/pytorch/qnn_test.py)
*   [tests/python/frontend/pytorch/test_forward.py](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/tests/python/frontend/pytorch/test_forward.py)
*   [tests/python/frontend/pytorch/test_fx_quant.py](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/tests/python/frontend/pytorch/test_fx_quant.py)
*   [tests/python/frontend/pytorch/test_object_detection.py](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/tests/python/frontend/pytorch/test_object_detection.py)
*   [tests/python/frontend/pytorch/test_rnns.py](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/tests/python/frontend/pytorch/test_rnns.py)
*   [tests/python/frontend/tensorflow/test_forward.py](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/tests/python/frontend/tensorflow/test_forward.py)
*   [tests/python/frontend/tensorflow2/common.py](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/tests/python/frontend/tensorflow2/common.py)
*   [tests/python/frontend/tensorflow2/test_functional_models.py](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/tests/python/frontend/tensorflow2/test_functional_models.py)
*   [tests/python/frontend/tensorflow2/test_sequential_models.py](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/tests/python/frontend/tensorflow2/test_sequential_models.py)
*   [tests/python/frontend/tflite/test_forward.py](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/tests/python/frontend/tflite/test_forward.py)

## Purpose and Scope

Frontend converters are the entry point to TVM's compilation pipeline, responsible for ingesting trained models from various deep learning frameworks and converting them into TVM's Relay IR representation. This document describes the converter architecture, common infrastructure, and the conversion process.

For detailed information about individual converters, see:

*   PyTorch converter: [PyTorch Frontend](https://deepwiki.com/tenstorrent/tt-tvm/3.2-pytorch-frontend)
*   ONNX converter: [ONNX Frontend](https://deepwiki.com/tenstorrent/tt-tvm/3.3-onnx-frontend)
*   TensorFlow converter: [TensorFlow Frontend](https://deepwiki.com/tenstorrent/tt-tvm/3.4-tensorflow-frontend)
*   Keras converter: [Keras Frontend](https://deepwiki.com/tenstorrent/tt-tvm/3.5-keras-frontend)
*   Other converters: [TFLite and Other Frontends](https://deepwiki.com/tenstorrent/tt-tvm/3.6-tflite-and-other-frontends)

For common utilities shared across converters, see [Common Frontend Infrastructure](https://deepwiki.com/tenstorrent/tt-tvm/3.1-common-frontend-infrastructure).

For information about the Relay IR produced by converters, see [Relay IR System](https://deepwiki.com/tenstorrent/tt-tvm/4-relay-ir-system).

## Conversion Pipeline Overview

Frontend converters transform framework-specific model representations into TVM's unified Relay IR. All converters follow a common pattern: they parse the source model's computational graph, map operations to Relay operators, preserve type and shape information, and output an `IRModule` with associated parameters.

**Conversion Pipeline Architecture**

Sources: [python/tvm/relay/frontend/pytorch.py 24-59](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/python/tvm/relay/frontend/pytorch.py#L24-L59)[python/tvm/relay/frontend/onnx.py 22-67](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/python/tvm/relay/frontend/onnx.py#L22-L67)[python/tvm/relay/frontend/tensorflow.py 22-50](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/python/tvm/relay/frontend/tensorflow.py#L22-L50)[python/tvm/relay/frontend/tflite.py 22-46](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/python/tvm/relay/frontend/tflite.py#L22-L46)[python/tvm/relay/frontend/common.py 1-40](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/python/tvm/relay/frontend/common.py#L1-L40)

## Input and Output Format

### Input Requirements

Each converter accepts framework-specific model formats:

| Framework | Input Type | Key Attributes |
| --- | --- | --- |
| **PyTorch** | `torch.jit.ScriptModule` | Traced or scripted model |
| **ONNX** | `onnx.ModelProto` | ONNX protobuf format |
| **TensorFlow** | `GraphDef` | Frozen graph definition |
| **TFLite** | `tflite.Model` | FlatBuffer format |
| **Keras** | `keras.Model` | Sequential/Functional API |
| **MXNet** | `Symbol` or `Gluon` | Symbol graph or imperative |

Converters also require:

*   **Shape dictionary**: Maps input names to tensor shapes, e.g., `{"input0": (1, 3, 224, 224)}`
*   **Optional parameters**: Pre-trained weights, configuration options

Sources: [python/tvm/relay/frontend/pytorch.py 3781-3855](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/python/tvm/relay/frontend/pytorch.py#L3781-L3855)[python/tvm/relay/frontend/onnx.py 2815-2903](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/python/tvm/relay/frontend/onnx.py#L2815-L2903)[python/tvm/relay/frontend/tensorflow.py 4267-4354](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/python/tvm/relay/frontend/tensorflow.py#L4267-L4354)

### Output Format

All converters produce a standardized output:

The `IRModule` contains:

*   **Main function**: Entry point with input parameters
*   **Type annotations**: Inferred types for all expressions
*   **Span information**: Source location metadata for debugging

Sources: [python/tvm/relay/frontend/pytorch.py 3781-3855](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/python/tvm/relay/frontend/pytorch.py#L3781-L3855)[python/tvm/relay/frontend/common.py 619-664](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/python/tvm/relay/frontend/common.py#L619-L664)

## Converter Architecture

### Class Hierarchy

Converters follow a consistent architecture pattern with operator converter classes:

**Converter Class Architecture**

Sources: [python/tvm/relay/frontend/pytorch.py 156-168](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/python/tvm/relay/frontend/pytorch.py#L156-L168)[python/tvm/relay/frontend/onnx.py 562-585](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/python/tvm/relay/frontend/onnx.py#L562-L585)[python/tvm/relay/frontend/tflite.py 59-189](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/python/tvm/relay/frontend/tflite.py#L59-L189)

### Operator Mapping Pattern

Each converter maintains a dictionary mapping framework operators to conversion methods:

**PyTorch converter map**[python/tvm/relay/frontend/pytorch.py 1589-1970](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/python/tvm/relay/frontend/pytorch.py#L1589-L1970):

**ONNX converter pattern**[python/tvm/relay/frontend/onnx.py 587-597](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/python/tvm/relay/frontend/onnx.py#L587-L597):

**TFLite converter map**[python/tvm/relay/frontend/tflite.py 81-188](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/python/tvm/relay/frontend/tflite.py#L81-L188):

Sources: [python/tvm/relay/frontend/pytorch.py 1589-1970](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/python/tvm/relay/frontend/pytorch.py#L1589-L1970)[python/tvm/relay/frontend/onnx.py 587-617](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/python/tvm/relay/frontend/onnx.py#L587-L617)[python/tvm/relay/frontend/tflite.py 81-188](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/python/tvm/relay/frontend/tflite.py#L81-L188)

## Conversion Workflow

The conversion process follows these stages:

### Stage 1: Model Parsing

Each converter parses the framework-specific model format:

*   **PyTorch**: Traverses TorchScript IR graph nodes [python/tvm/relay/frontend/pytorch.py 3604-3755](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/python/tvm/relay/frontend/pytorch.py#L3604-L3755)
*   **ONNX**: Iterates over protobuf node definitions [python/tvm/relay/frontend/onnx.py 2815-2903](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/python/tvm/relay/frontend/onnx.py#L2815-L2903)
*   **TensorFlow**: Walks GraphDef nodes and builds control flow [python/tvm/relay/frontend/tensorflow.py 4267-4354](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/python/tvm/relay/frontend/tensorflow.py#L4267-L4354)
*   **TFLite**: Processes FlatBuffer operators [python/tvm/relay/frontend/tflite.py 267-309](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/python/tvm/relay/frontend/tflite.py#L267-L309)

### Stage 2: Operator Conversion

For each operation in the source model:

1.   **Lookup converter**: Find the conversion method in `convert_map`
2.   **Extract attributes**: Parse operator-specific attributes (padding, strides, etc.)
3.   **Convert inputs**: Recursively convert input expressions
4.   **Type inference**: Determine output types and shapes using `infer_type()`
5.   **Create Relay expression**: Call corresponding Relay operator
6.   **Attach span**: Add source location metadata with `set_span()`

**Operator Conversion Workflow**

Sources: [python/tvm/relay/frontend/pytorch.py 3604-3755](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/python/tvm/relay/frontend/pytorch.py#L3604-L3755)[python/tvm/relay/frontend/onnx.py 2588-2677](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/python/tvm/relay/frontend/onnx.py#L2588-L2677)[python/tvm/relay/frontend/common.py 619-664](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/python/tvm/relay/frontend/common.py#L619-L664)

### Stage 3: Type and Shape Inference

Type inference is critical for correctness. Common utilities provide:

*   **`infer_type(expr, mod)`**: Computes checked types for expressions [python/tvm/relay/frontend/common.py 170-188](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/python/tvm/relay/frontend/common.py#L170-L188)
*   **`infer_shape(expr, mod)`**: Extracts tensor shape tuples [python/tvm/relay/frontend/common.py 190-210](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/python/tvm/relay/frontend/common.py#L190-L210)
*   **`infer_value(expr, params, mod)`**: Evaluates constant expressions [python/tvm/relay/frontend/common.py 212-239](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/python/tvm/relay/frontend/common.py#L212-L239)

Example from PyTorch converter [python/tvm/relay/frontend/pytorch.py 171-191](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/python/tvm/relay/frontend/pytorch.py#L171-L191):

Sources: [python/tvm/relay/frontend/pytorch.py 171-191](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/python/tvm/relay/frontend/pytorch.py#L171-L191)[python/tvm/relay/frontend/common.py 170-239](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/python/tvm/relay/frontend/common.py#L170-L239)

### Stage 4: Parameter Extraction

Converters extract learned weights and store them in the parameters dictionary:

*   **PyTorch**: Extracts from ScriptModule's state dict [python/tvm/relay/frontend/pytorch.py 3708-3746](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/python/tvm/relay/frontend/pytorch.py#L3708-L3746)
*   **ONNX**: Reads initializer tensors [python/tvm/relay/frontend/onnx.py 2660-2705](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/python/tvm/relay/frontend/onnx.py#L2660-L2705)
*   **TensorFlow**: Processes variable nodes [python/tvm/relay/frontend/tensorflow.py 4091-4143](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/python/tvm/relay/frontend/tensorflow.py#L4091-L4143)

Parameters are stored as `tvm.nd.NDArray` objects with proper data types.

Sources: [python/tvm/relay/frontend/pytorch.py 3708-3746](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/python/tvm/relay/frontend/pytorch.py#L3708-L3746)[python/tvm/relay/frontend/onnx.py 2660-2705](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/python/tvm/relay/frontend/onnx.py#L2660-L2705)

### Stage 5: Module Construction

The final stage builds the `IRModule`:

Sources: [python/tvm/relay/frontend/pytorch.py 3781-3855](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/python/tvm/relay/frontend/pytorch.py#L3781-L3855)[python/tvm/relay/frontend/common.py 41-53](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/python/tvm/relay/frontend/common.py#L41-L53)

## Common Converter Utilities

### AttrCvt - Attribute Conversion Helper

The `AttrCvt` class provides declarative attribute mapping [python/tvm/relay/frontend/common.py 275-422](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/python/tvm/relay/frontend/common.py#L275-L422):

**Key features**:

*   Renames attributes between frameworks
*   Provides default values
*   Validates constraints
*   Excludes unsupported attributes

Sources: [python/tvm/relay/frontend/common.py 275-422](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/python/tvm/relay/frontend/common.py#L275-L422)

### Span Tracking

Span information preserves source location for debugging. The `set_span()` utility attaches metadata [python/tvm/relay/frontend/common.py 664-682](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/python/tvm/relay/frontend/common.py#L664-L682):

This enables error messages that reference the original model:

```
Error in operator conv2d.weight at location: Conv_0
```

Sources: [python/tvm/relay/frontend/common.py 664-682](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/python/tvm/relay/frontend/common.py#L664-L682)[python/tvm/relay/frontend/pytorch.py 55](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/python/tvm/relay/frontend/pytorch.py#L55-L55)

### Shape and Type Utilities

Common shape manipulation utilities [python/tvm/relay/frontend/common.py 424-544](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/python/tvm/relay/frontend/common.py#L424-L544):

| Function | Purpose | Usage |
| --- | --- | --- |
| `infer_channels()` | Extract channel dimension | For layout transformations |
| `infer_shape()` | Get concrete shapes | Shape-dependent logic |
| `infer_type()` | Get checked types | Type validation |
| `infer_value()` | Constant evaluation | Compile-time computation |
| `try_infer_value()` | Safe value inference | Handle dynamic shapes |
| `fold_constant()` | Constant folding pass | Optimize constants |

Sources: [python/tvm/relay/frontend/common.py 424-544](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/python/tvm/relay/frontend/common.py#L424-L544)

## Quantization Support

### PyTorch Quantization

The PyTorch frontend includes comprehensive quantization support via `qnn_torch.py`[python/tvm/relay/frontend/qnn_torch.py 1-246](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/python/tvm/relay/frontend/qnn_torch.py#L1-L246):

**Quantized operators**:

*   `quantized::conv2d`: Per-channel quantized convolution
*   `quantized::linear`: Quantized fully connected
*   `quantized::add`: Quantized element-wise addition
*   `quantized::mul`: Quantized multiplication

**Quantization parameter extraction**[python/tvm/relay/frontend/qnn_torch.py 63-82](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/python/tvm/relay/frontend/qnn_torch.py#L63-L82):

**Per-tensor vs per-channel**[python/tvm/relay/frontend/qnn_torch.py 63-77](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/python/tvm/relay/frontend/qnn_torch.py#L63-L77):

*   Per-tensor: Single scale and zero point for entire tensor
*   Per-channel: Vector of scales, zero points must be all zeros (QNN restriction)

Sources: [python/tvm/relay/frontend/qnn_torch.py 1-246](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/python/tvm/relay/frontend/qnn_torch.py#L1-L246)[python/tvm/relay/frontend/pytorch.py 48](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/python/tvm/relay/frontend/pytorch.py#L48-L48)

### ONNX Quantization

ONNX quantized operators use separate scale/zero-point inputs [python/tvm/relay/frontend/onnx.py 2303-2379](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/python/tvm/relay/frontend/onnx.py#L2303-L2379):

Quantized operations in ONNX:

*   `QLinearConv`
*   `QLinearMatMul`
*   `QuantizeLinear` / `DequantizeLinear`

Sources: [python/tvm/relay/frontend/onnx.py 2303-2379](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/python/tvm/relay/frontend/onnx.py#L2303-L2379)

## Error Handling and Validation

Converters implement validation at multiple stages:

### Unsupported Operator Detection

Each converter checks for unsupported operations:

**TFLite example**[python/tvm/relay/frontend/tflite.py 190-231](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/python/tvm/relay/frontend/tflite.py#L190-L231):

Sources: [python/tvm/relay/frontend/tflite.py 190-231](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/python/tvm/relay/frontend/tflite.py#L190-L231)

### Dynamic Shape Handling

Converters support dynamic shapes using `relay.Any()`:

*   **Input specification**: `shape_dict = {"input": (1, 3, Any(), Any())}`
*   **Runtime shapes**: Use `shape_of()` operator for dynamic computations
*   **Validation**: Some operations have restrictions on dynamic dimensions

Sources: [python/tvm/relay/frontend/onnx.py 129-149](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/python/tvm/relay/frontend/onnx.py#L129-L149)[python/tvm/relay/frontend/common.py 546-569](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/python/tvm/relay/frontend/common.py#L546-L569)

## Converter Performance Considerations

### Graph Traversal Efficiency

Converters use memoization to avoid redundant conversions:

**PyTorch expression table**[python/tvm/relay/frontend/pytorch.py 3604-3637](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/python/tvm/relay/frontend/pytorch.py#L3604-L3637):

Sources: [python/tvm/relay/frontend/pytorch.py 3604-3637](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/python/tvm/relay/frontend/pytorch.py#L3604-L3637)

### Constant Folding

Early constant folding reduces IR size:

This eliminates redundant shape computations and constant operations.

Sources: [python/tvm/relay/frontend/common.py 241-273](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/python/tvm/relay/frontend/common.py#L241-L273)

## Testing Infrastructure

Each converter includes comprehensive test suites:

| Converter | Test File | Coverage |
| --- | --- | --- |
| PyTorch | `tests/python/frontend/pytorch/test_forward.py` | 190+ test cases |
| ONNX | `tests/python/frontend/onnx/test_forward.py` | 250+ test cases |
| TensorFlow | `tests/python/frontend/tensorflow/test_forward.py` | 135+ test cases |
| TFLite | `tests/python/frontend/tflite/test_forward.py` | 175+ test cases |
| MXNet | `tests/python/frontend/mxnet/test_forward.py` | 55+ test cases |

**Test verification pattern**:

1.   Create framework model
2.   Generate test inputs
3.   Run inference in source framework
4.   Convert to Relay and compile
5.   Compare outputs with tolerance checks

Sources: [tests/python/frontend/pytorch/test_forward.py 133-235](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/tests/python/frontend/pytorch/test_forward.py#L133-L235)[tests/python/frontend/onnx/test_forward.py 194-278](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/tests/python/frontend/onnx/test_forward.py#L194-L278)[tests/python/frontend/tflite/test_forward.py 190-297](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/tests/python/frontend/tflite/test_forward.py#L190-L297)

Dismiss
Refresh this wiki

Enter email to refresh
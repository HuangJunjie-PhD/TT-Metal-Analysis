---
project: tt-forge-onnx
github: https://github.com/tenstorrent/tt-forge-onnx
deepwiki: https://deepwiki.com/tenstorrent/tt-forge-onnx/5-operation-system)
---

# Operation System

Relevant source files
*   [forge/csrc/ops/CMakeLists.txt](https://github.com/tenstorrent/tt-forge-onnx/blob/e519819c/forge/csrc/ops/CMakeLists.txt)
*   [forge/csrc/ops/op.cpp](https://github.com/tenstorrent/tt-forge-onnx/blob/e519819c/forge/csrc/ops/op.cpp)
*   [forge/csrc/ops/op.hpp](https://github.com/tenstorrent/tt-forge-onnx/blob/e519819c/forge/csrc/ops/op.hpp)
*   [forge/csrc/ops/op_interface.hpp](https://github.com/tenstorrent/tt-forge-onnx/blob/e519819c/forge/csrc/ops/op_interface.hpp)
*   [forge/csrc/ops/python_bindings.cpp](https://github.com/tenstorrent/tt-forge-onnx/blob/e519819c/forge/csrc/ops/python_bindings.cpp)
*   [forge/forge/op/__init__.py](https://github.com/tenstorrent/tt-forge-onnx/blob/e519819c/forge/forge/op/__init__.py)
*   [forge/forge/op/eltwise_nary.py](https://github.com/tenstorrent/tt-forge-onnx/blob/e519819c/forge/forge/op/eltwise_nary.py)
*   [forge/forge/op/eltwise_unary.py](https://github.com/tenstorrent/tt-forge-onnx/blob/e519819c/forge/forge/op/eltwise_unary.py)
*   [forge/forge/op/tm.py](https://github.com/tenstorrent/tt-forge-onnx/blob/e519819c/forge/forge/op/tm.py)
*   [forge/forge/tvm_to_python.py](https://github.com/tenstorrent/tt-forge-onnx/blob/e519819c/forge/forge/tvm_to_python.py)

This page describes the architecture of the operation system in tt-forge-onnx: how Python-level op functions in `forge/forge/op/` are defined, how they map to C++ implementations in `forge/csrc/ops/`, and how control flows between the two layers. For details on individual operation categories, see the sub-pages: [Operation Architecture](https://deepwiki.com/tenstorrent/tt-forge-onnx/5.1-operation-architecture), [Operation Implementation](https://deepwiki.com/tenstorrent/tt-forge-onnx/5.2-operation-implementation-guide), [Tensor Manipulation Operations](https://deepwiki.com/tenstorrent/tt-forge-onnx/5.3-operation-categories-and-apis), [Convolution Operations](https://deepwiki.com/tenstorrent/tt-forge-onnx/5-operation-system)#5.4), and [Element-wise Operations](https://deepwiki.com/tenstorrent/tt-forge-onnx/5-operation-system)#5.5). For how operations are lowered to MLIR/TTIR, see [MLIR Lowering](https://deepwiki.com/tenstorrent/tt-forge-onnx/3.3-mlir-backend-and-code-generation).

* * *

## Architecture Overview

The operation system is split into two distinct layers connected by pybind11 bindings:

*   **Python frontend** (`forge/forge/op/`): Thin wrapper functions that validate arguments, canonicalize parameters, construct a C++ `Op` object, and call `get_tensor()` to insert a node into the computation graph.
*   **C++ backend** (`forge/csrc/ops/`): The `Op` class holds the `OpType` enum and `Attrs` map. A central dispatch in `op.cpp` routes calls to per-op namespaces that implement `eval`, `shape`, `backward`, and `decompose_*` methods.

**Dual-Layer Operation System Architecture**

Sources: [forge/forge/op/__init__.py 1-72](https://github.com/tenstorrent/tt-forge-onnx/blob/e519819c/forge/forge/op/__init__.py#L1-L72)[forge/csrc/ops/op.hpp 1-253](https://github.com/tenstorrent/tt-forge-onnx/blob/e519819c/forge/csrc/ops/op.hpp#L1-L253)[forge/csrc/ops/op.cpp 1-100](https://github.com/tenstorrent/tt-forge-onnx/blob/e519819c/forge/csrc/ops/op.cpp#L1-L100)[forge/csrc/ops/python_bindings.cpp 1-127](https://github.com/tenstorrent/tt-forge-onnx/blob/e519819c/forge/csrc/ops/python_bindings.cpp#L1-L127)

* * *

## Python Frontend Layer

All Python-facing op functions live under `forge/forge/op/` and are re-exported from `forge/forge/op/__init__.py`. Each function follows a uniform pattern:

1.   Accept a `name: str`, one or more `Tensor` operands, and op-specific keyword arguments.
2.   Perform input validation and parameter normalization (e.g., `conv2d_padding_to_canonical` in `convolution.py`).
3.   Call `ForgeOp(OpType.<Name>, name, *operands, **attrs).get_tensor()` to register the op in the graph and return the output tensor.

`ForgeOp` is imported in every module as:

`from .common import ForgeOp as op`
A typical call looks like (from `forge/forge/op/eltwise_unary.py`):

`return op(OpType.Relu, name, operandA).get_tensor()`
Or with attributes (from `forge/forge/op/tm.py`):

`return op(OpType.Transpose, name, operandA, dim0=dim0, dim1=dim1).get_tensor()`
### Python Op Modules

| Module file | Exported ops |
| --- | --- |
| `forge/forge/op/eltwise_unary.py` | `Abs`, `Exp`, `Log`, `Pow`, `Relu`, `Gelu`, `Sigmoid`, `Clip`, `Sqrt`, `Tanh`, `Sine`, `Cosine`, `Atan`, `LeakyRelu`, `Reciprocal`, `LogicalNot`, `Erf`, `Cast`, `Identity` |
| `forge/forge/op/eltwise_binary.py` | `Add`, `Subtract`, `Multiply`, `Divide`, `Max`, `Min`, `Heaviside`, `Power`, `Greater`, `GreaterEqual`, `Less`, `LessEqual`, `Equal`, `NotEqual`, `LogicalAnd`, `BitwiseAnd`, `Remainder` |
| `forge/forge/op/eltwise_nary.py` | `Concatenate`, `Where`, `IndexCopy`, `Stack` |
| `forge/forge/op/tm.py` | `Transpose`, `Reshape`, `Index`, `AdvIndex`, `Select`, `Pad`, `ConstantPad`, `Broadcast`, `Repeat`, `RepeatInterleave`, `Unsqueeze`, `Squeeze`, `PixelShuffle` |
| `forge/forge/op/convolution.py` | `Conv2d`, `Conv2dTranspose` |
| `forge/forge/op/pooling.py` | `MaxPool1d`, `MaxPool2d`, `AvgPool1d`, `AvgPool2d` |
| `forge/forge/op/reduce.py` | `ReduceSum`, `ReduceAvg`, `ReduceMax`, `Argmax` |
| `forge/forge/op/nn.py` | `Softmax`, `LogSoftmax`, `Layernorm`, `Batchnorm`, `Dropout` |
| `forge/forge/op/matmul.py` | `Matmul` |
| `forge/forge/op/resize.py` | `Resize1d`, `Resize2d`, `Upsample2d`, `Downsample2d` |
| `forge/forge/op/embedding.py` | `Embedding` |
| `forge/forge/op/kv_cache.py` | `FillCache`, `UpdateCache` |
| `forge/forge/op/constant.py` | `Constant` |
| `forge/forge/op/misc.py` | `CumSum` |

Sources: [forge/forge/op/__init__.py 1-72](https://github.com/tenstorrent/tt-forge-onnx/blob/e519819c/forge/forge/op/__init__.py#L1-L72)

* * *

## C++ Backend Layer

### `Op` Class and `OpType` Enum

The central type in the C++ backend is the `Op` class defined in [forge/csrc/ops/op.hpp 43-253](https://github.com/tenstorrent/tt-forge-onnx/blob/e519819c/forge/csrc/ops/op.hpp#L43-L253) It holds:

*   `OpType type_` — an enum value identifying which operation this is.
*   `Attrs attrs_` — a `std::map<std::string, Attr>` where `Attr` is a `std::variant` over `std::string`, `bool`, `int`, `float`, `std::vector<int>`, and two tuple-vector types.

`OpType` is declared as a `uint32_t` enum with one enumerator per supported operation. The complete list spans from `Abs` through `Where` and includes approximately 80 entries.

The `Attrs` map allows ops to carry named parameters (e.g., `dim`, `shape`, `stride`, `padding`, `groups`) without requiring dedicated C++ struct fields per op.

### pybind11 Bridge

`forge/csrc/ops/python_bindings.cpp` binds the `Op` class and `OpType` enum to Python under the `forge._C.ops` module:

*   `Op` is bound with `py::init` accepting either `(op_name: str, attrs: dict)` or `(type: OpType, attrs: dict)`.
*   `__getattr__` and `__setattr__` proxy into `attrs_` so Python code can read/write named attributes directly.
*   The `OpType` enum is exposed as `forge._C.ops.OpType` with one `.value(...)` entry per enumerator.

Python wrappers import `OpType` from there:

`from forge._C.ops import OpType`
Sources: [forge/csrc/ops/python_bindings.cpp 1-127](https://github.com/tenstorrent/tt-forge-onnx/blob/e519819c/forge/csrc/ops/python_bindings.cpp#L1-L127)[forge/csrc/ops/op.hpp 133-151](https://github.com/tenstorrent/tt-forge-onnx/blob/e519819c/forge/csrc/ops/op.hpp#L133-L151)

### Per-Op Interface: `DECLARE_OP_INTERFACE`

Each operation is implemented in its own `.cpp` file inside `forge/csrc/ops/` (e.g., `op_relu.cpp`, `op_conv_2d.cpp`). The interface for every per-op namespace is declared in [forge/csrc/ops/op_interface.hpp 39-65](https://github.com/tenstorrent/tt-forge-onnx/blob/e519819c/forge/csrc/ops/op_interface.hpp#L39-L65) using the `DECLARE_OP_INTERFACE(ns)` macro, which expands to:

```
namespace ns {
  at::Tensor eval(const Op &op, const std::vector<at::Tensor> &tensors);
  std::tuple<Shape, std::vector<DimBroadcastTrampoline>> shape(const Op &op, const std::vector<std::vector<uint32_t>> &inputs);
  NodeContext backward(const Op &op, autograd_context &context, int operand, ...);
  void decompose_initial(const Op &op, DecomposingContext &dc, ...);
  void decompose_post_optimize(const Op &op, DecomposingContext &dc, ...);
  void decompose_post_autograd(const Op &op, DecomposingContext &dc, ...);
  long initial_flops_estimate(const Op &op, ...);
}
```

Every namespace registered in `op_interface.hpp` corresponds to one source file in `CMakeLists.txt`. The complete list of compiled sources is in [forge/csrc/ops/CMakeLists.txt 1-96](https://github.com/tenstorrent/tt-forge-onnx/blob/e519819c/forge/csrc/ops/CMakeLists.txt#L1-L96)

### Central Dispatch in `op.cpp`

`forge/csrc/ops/op.cpp` implements `Op::eval()`, `Op::shape()`, `Op::backward()`, `Op::decompose_initial()`, `Op::decompose_post_optimize()`, and `Op::decompose_post_autograd()` as large `switch (type_)` blocks. Each case delegates to the corresponding per-op namespace function:

```
case OpType::Relu: return relu::eval(*this, tensors);
case OpType::Conv2d: return conv_2d::eval(*this, tensors);
// ...
```

This makes `Op` itself an extremely thin dispatcher — all actual logic lives in the namespace-scoped files.

**C++ Op Dispatch Flow**

Sources: [forge/csrc/ops/op.cpp 243-332](https://github.com/tenstorrent/tt-forge-onnx/blob/e519819c/forge/csrc/ops/op.cpp#L243-L332)[forge/csrc/ops/op_interface.hpp 39-65](https://github.com/tenstorrent/tt-forge-onnx/blob/e519819c/forge/csrc/ops/op_interface.hpp#L39-L65)

* * *

## Op Attributes

Attributes are passed from Python as keyword arguments to `ForgeOp`, which converts them into the `Attrs` map. The `Attr` variant type allows each attribute to be one of:

| Type | Example attributes |
| --- | --- |
| `int` | `dim`, `stride`, `groups` |
| `float` | `alpha`, `value`, `min`, `max` |
| `bool` | `channel_last` |
| `std::string` | `approximate` (for Gelu) |
| `std::vector<int>` | `padding`, `shape`, `repeats` |

C++ op implementations retrieve attributes via `op.attr_as<T>("name")` or `op.get_attr("name")`, defined in [forge/csrc/ops/op.hpp 166-183](https://github.com/tenstorrent/tt-forge-onnx/blob/e519819c/forge/csrc/ops/op.hpp#L166-L183)

* * *

## Connection to the Compilation Pipeline

The `Op::as_string()` method (implemented via `OpTypeToString` in `op.cpp`) converts an `OpType` to its canonical string name (e.g., `OpType::Conv2d` → `"conv2d"`). This string is the key used by `MLIRGenerator::lowering_handler_map` (in `lower_to_mlir.cpp`) to look up the TTIR emission function for each op when lowering the Forge graph to MLIR. See [MLIR Lowering](https://deepwiki.com/tenstorrent/tt-forge-onnx/3.3-mlir-backend-and-code-generation) for details on that stage.

Decomposition methods (`decompose_initial`, `decompose_post_optimize`, `decompose_post_autograd`) are called during graph optimization passes described in [Graph Optimization Passes](https://deepwiki.com/tenstorrent/tt-forge-onnx/3.2-tvm-frontend-and-relay-ir). Some ops (e.g., `conv_2d`, `pixel_shuffle`, `resize_2d`) perform non-trivial decompositions into sequences of lower-level ops at these stages.

**Op Lifecycle in the Compilation Pipeline**

Sources: [forge/csrc/ops/op.cpp 37-131](https://github.com/tenstorrent/tt-forge-onnx/blob/e519819c/forge/csrc/ops/op.cpp#L37-L131)[forge/csrc/ops/op.hpp 204-243](https://github.com/tenstorrent/tt-forge-onnx/blob/e519819c/forge/csrc/ops/op.hpp#L204-L243)

* * *

## Summary of Key Files

| File | Role |
| --- | --- |
| `forge/forge/op/__init__.py` | Re-exports all Python op functions |
| `forge/forge/op/common.py` | Defines `ForgeOp` — the Python-to-C++ bridge wrapper |
| `forge/forge/op/tm.py` | Tensor manipulation op wrappers |
| `forge/forge/op/eltwise_unary.py` | Unary elementwise op wrappers |
| `forge/forge/op/eltwise_binary.py` | Binary elementwise op wrappers |
| `forge/forge/op/eltwise_nary.py` | N-ary op wrappers |
| `forge/forge/op/convolution.py` | `Conv2d`, `Conv2dTranspose` wrappers with padding canonicalization |
| `forge/csrc/ops/op.hpp` | `Op` class and `OpType` enum definition |
| `forge/csrc/ops/op.cpp` | `OpTypeToString`, `StringToOpType`, and central dispatch switch blocks |
| `forge/csrc/ops/op_interface.hpp` | `DECLARE_OP_INTERFACE` macro declaring per-namespace function signatures |
| `forge/csrc/ops/op_*.cpp` | Individual op implementations (`eval`, `shape`, `backward`, `decompose_*`) |
| `forge/csrc/ops/python_bindings.cpp` | pybind11 bindings for `Op` and `OpType` |
| `forge/csrc/ops/CMakeLists.txt` | Build list of all op source files compiled into the `ops` static library |

Dismiss
Refresh this wiki

Enter email to refresh
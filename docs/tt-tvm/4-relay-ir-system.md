---
project: tt-tvm
github: https://github.com/tenstorrent/tt-tvm
deepwiki: https://deepwiki.com/tenstorrent/tt-tvm/4-relay-ir-system
---

# Relay IR System

Relevant source files
*   [include/tvm/relay/attrs/nn.h](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/include/tvm/relay/attrs/nn.h)
*   [include/tvm/relay/attrs/transform.h](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/include/tvm/relay/attrs/transform.h)
*   [include/tvm/topi/transform.h](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/include/tvm/topi/transform.h)
*   [python/tvm/relay/op/_reduce.py](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/python/tvm/relay/op/_reduce.py)
*   [python/tvm/relay/op/_transform.py](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/python/tvm/relay/op/_transform.py)
*   [python/tvm/relay/op/nn/_nn.py](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/python/tvm/relay/op/nn/_nn.py)
*   [python/tvm/relay/op/nn/nn.py](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/python/tvm/relay/op/nn/nn.py)
*   [python/tvm/relay/op/op_attrs.py](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/python/tvm/relay/op/op_attrs.py)
*   [python/tvm/relay/op/reduce.py](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/python/tvm/relay/op/reduce.py)
*   [python/tvm/relay/op/strategy/cuda.py](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/python/tvm/relay/op/strategy/cuda.py)
*   [python/tvm/relay/op/strategy/generic.py](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/python/tvm/relay/op/strategy/generic.py)
*   [python/tvm/relay/op/strategy/hls.py](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/python/tvm/relay/op/strategy/hls.py)
*   [python/tvm/relay/op/strategy/intel_graphics.py](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/python/tvm/relay/op/strategy/intel_graphics.py)
*   [python/tvm/relay/op/strategy/x86.py](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/python/tvm/relay/op/strategy/x86.py)
*   [python/tvm/relay/op/transform.py](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/python/tvm/relay/op/transform.py)
*   [python/tvm/relay/qnn/op/layout_conversions.py](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/python/tvm/relay/qnn/op/layout_conversions.py)
*   [python/tvm/relay/transform/infer_layout_utils.py](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/python/tvm/relay/transform/infer_layout_utils.py)
*   [python/tvm/topi/__init__.py](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/python/tvm/topi/__init__.py)
*   [python/tvm/topi/cuda/__init__.py](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/python/tvm/topi/cuda/__init__.py)
*   [python/tvm/topi/scatter.py](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/python/tvm/topi/scatter.py)
*   [python/tvm/topi/transform.py](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/python/tvm/topi/transform.py)
*   [python/tvm/topi/x86/__init__.py](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/python/tvm/topi/x86/__init__.py)
*   [src/relay/op/nn/convolution.cc](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/src/relay/op/nn/convolution.cc)
*   [src/relay/op/nn/convolution.h](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/src/relay/op/nn/convolution.h)
*   [src/relay/op/nn/nn.cc](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/src/relay/op/nn/nn.cc)
*   [src/relay/op/nn/nn.h](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/src/relay/op/nn/nn.h)
*   [src/relay/op/nn/pad.cc](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/src/relay/op/nn/pad.cc)
*   [src/relay/op/nn/pooling.cc](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/src/relay/op/nn/pooling.cc)
*   [src/relay/op/tensor/reduce.cc](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/src/relay/op/tensor/reduce.cc)
*   [src/relay/op/tensor/transform.cc](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/src/relay/op/tensor/transform.cc)
*   [src/relay/op/tensor/transform.h](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/src/relay/op/tensor/transform.h)
*   [src/relay/transforms/alter_op_layout.cc](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/src/relay/transforms/alter_op_layout.cc)
*   [src/relay/transforms/canonicalize_cast.cc](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/src/relay/transforms/canonicalize_cast.cc)
*   [src/relay/transforms/convert_layout.cc](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/src/relay/transforms/convert_layout.cc)
*   [src/relay/transforms/forward_rewrite.cc](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/src/relay/transforms/forward_rewrite.cc)
*   [src/relay/transforms/infer_layout_utils.cc](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/src/relay/transforms/infer_layout_utils.cc)
*   [src/relay/transforms/infer_layout_utils.h](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/src/relay/transforms/infer_layout_utils.h)
*   [src/relay/transforms/legalize.cc](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/src/relay/transforms/legalize.cc)
*   [src/relay/transforms/transform_layout.h](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/src/relay/transforms/transform_layout.h)
*   [src/runtime/contrib/cudnn/softmax.cc](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/src/runtime/contrib/cudnn/softmax.cc)
*   [src/topi/transform.cc](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/src/topi/transform.cc)
*   [tests/python/relay/test_any.py](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/tests/python/relay/test_any.py)
*   [tests/python/relay/test_op_level1.py](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/tests/python/relay/test_op_level1.py)
*   [tests/python/relay/test_op_level10.py](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/tests/python/relay/test_op_level10.py)
*   [tests/python/relay/test_op_level2.py](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/tests/python/relay/test_op_level2.py)
*   [tests/python/relay/test_op_level3.py](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/tests/python/relay/test_op_level3.py)
*   [tests/python/relay/test_op_level4.py](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/tests/python/relay/test_op_level4.py)
*   [tests/python/relay/test_pass_alter_op_layout.py](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/tests/python/relay/test_pass_alter_op_layout.py)
*   [tests/python/relay/test_pass_convert_op_layout.py](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/tests/python/relay/test_pass_convert_op_layout.py)
*   [tests/python/topi/python/test_topi_scatter.py](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/tests/python/topi/python/test_topi_scatter.py)
*   [tests/python/topi/python/test_topi_transform.py](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/tests/python/topi/python/test_topi_transform.py)

## Purpose and Scope

The Relay IR System is TVM's high-level, graph-based intermediate representation for neural network models. Relay serves as the central abstraction layer that receives models from frontend converters and performs graph-level optimizations before lowering to TensorIR for kernel-level optimization.

This document covers:

*   Relay's operator system (NN and tensor transformation operators)
*   Type system and shape inference
*   Layout transformation infrastructure
*   Optimization passes at the graph level
*   Target-dependent strategy system

For information about:

*   Frontend model ingestion, see [Frontend Converters](https://deepwiki.com/tenstorrent/tt-tvm/3-frontend-converters)
*   Low-level kernel optimization, see [TensorIR (TIR) System](https://deepwiki.com/tenstorrent/tt-tvm/5-tensorir-(tir)-system)
*   Graph-level optimization passes in detail, see [Optimization Passes](https://deepwiki.com/tenstorrent/tt-tvm/4.5-optimization-passes)
*   Backend code generation, see [Code Generation Backends](https://deepwiki.com/tenstorrent/tt-tvm/6-code-generation-backends)

## Relay IR Core Concepts

### IRModule and Program Structure

Relay programs are represented as `IRModule` objects containing one or more `relay.Function` definitions. Each function consists of typed parameters and an expression body.

**Sources:**[python/tvm/relay/expr.py](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/python/tvm/relay/expr.py)[src/relay/ir/expr.cc](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/src/relay/ir/expr.cc)

### Expression Types

Relay supports multiple expression node types:

| Expression Type | Description | Example Usage |
| --- | --- | --- |
| `relay.Var` | Named variable with optional type annotation | Function parameters, let-bindings |
| `relay.Constant` | Compile-time constant tensor | Model weights, scalar values |
| `relay.Call` | Function/operator application | `relay.nn.conv2d(data, weight)` |
| `relay.Tuple` | Multiple expressions grouped together | Multi-output operators |
| `relay.TupleGetItem` | Extract item from tuple | Access individual outputs |
| `relay.Let` | Local binding (let x = value in body) | Intermediate computations |
| `relay.If` | Conditional expression | Dynamic control flow |

**Sources:**[python/tvm/relay/expr.py](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/python/tvm/relay/expr.py)[include/tvm/relay/expr.h](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/include/tvm/relay/expr.h)

## Operator System

### Operator Categories

Relay provides over 100 operators organized into two main categories:

**Sources:**[src/relay/op/nn/](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/src/relay/op/nn/)[src/relay/op/tensor/transform.cc](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/src/relay/op/tensor/transform.cc)[python/tvm/relay/op/nn/nn.py](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/python/tvm/relay/op/nn/nn.py)[python/tvm/relay/op/transform.py](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/python/tvm/relay/op/transform.py)

### Operator Registration and Attributes

Each operator is registered with:

*   **Type relation function**: Infers output types from input types
*   **Compute function** (`FTVMCompute`): Generates TE compute definition
*   **Schedule function**: Provides scheduling strategy
*   **Pattern annotation** (`TOpPattern`): Classifies operator for fusion
*   **Attributes structure**: Operator-specific parameters

#### Example: Conv2D Registration

The conv2d operator demonstrates the registration pattern:

**Sources:**[src/relay/op/nn/convolution.cc 290-350](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/src/relay/op/nn/convolution.cc#L290-L350)[include/tvm/relay/attrs/nn.h 75-150](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/include/tvm/relay/attrs/nn.h#L75-L150)

### Neural Network Operators

#### Convolution Operators

Relay supports 1D, 2D, and 3D convolutions with extensive layout support:

| Operator | Supported Layouts | Attributes |
| --- | --- | --- |
| `conv1d` | NCW, NWC | strides, padding, dilation, groups |
| `conv2d` | NCHW, NHWC, NCHW4c, NCHW16c | strides, padding, dilation, groups, kernel_size |
| `conv3d` | NCDHW, NDHWC | strides, padding, dilation, groups |
| `conv2d_transpose` | NCHW, NHWC | output_padding, strides, padding |

**Sources:**[src/relay/op/nn/convolution.cc](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/src/relay/op/nn/convolution.cc)[python/tvm/relay/op/nn/nn.py 30-350](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/python/tvm/relay/op/nn/nn.py#L30-L350)[include/tvm/relay/attrs/nn.h 53-180](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/include/tvm/relay/attrs/nn.h#L53-L180)

#### Pooling Operators

| Operator | Description | Type Relation |
| --- | --- | --- |
| `max_pool2d` | Max pooling with pool_size and strides | [src/relay/op/nn/pooling.cc 150-200](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/src/relay/op/nn/pooling.cc#L150-L200) |
| `avg_pool2d` | Average pooling with count_include_pad option | [src/relay/op/nn/pooling.cc 250-300](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/src/relay/op/nn/pooling.cc#L250-L300) |
| `global_avg_pool2d` | Global average across spatial dimensions | [src/relay/op/nn/pooling.cc 400-450](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/src/relay/op/nn/pooling.cc#L400-L450) |
| `adaptive_avg_pool2d` | Adaptive pooling to target output size | [src/relay/op/nn/pooling.cc 500-550](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/src/relay/op/nn/pooling.cc#L500-L550) |

**Sources:**[src/relay/op/nn/pooling.cc](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/src/relay/op/nn/pooling.cc)[python/tvm/relay/op/nn/nn.py 1800-2200](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/python/tvm/relay/op/nn/nn.py#L1800-L2200)

### Tensor Transform Operators

#### Shape Manipulation

The transform operators handle tensor shape changes:

**Sources:**[src/relay/op/tensor/transform.cc 663-855](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/src/relay/op/tensor/transform.cc#L663-L855)[python/tvm/relay/op/transform.py 246-333](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/python/tvm/relay/op/transform.py#L246-L333)[include/tvm/relay/attrs/transform.h 72-180](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/include/tvm/relay/attrs/transform.h#L72-L180)

#### Special Reshape Values

The `reshape` operator supports special dimension values:

| Value | Meaning | Example |
| --- | --- | --- |
| `0` | Copy dimension from input | `(2,3,4)` → `(0,-1)` → `(2,12)` |
| `-1` | Infer dimension from total size | `(2,3,4)` → `(-1,)` → `(24,)` |
| `-2` | Copy all remaining dimensions | `(2,3,4)` → `(-2,)` → `(2,3,4)` |
| `-3` | Merge two consecutive dimensions | `(2,3,4)` → `(-3,4)` → `(6,4)` |
| `-4` | Split one dimension into two | `(2,3,4)` → `(-4,1,2,-2)` → `(1,2,3,4)` |

**Sources:**[src/relay/op/tensor/transform.cc 663-797](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/src/relay/op/tensor/transform.cc#L663-L797)[python/tvm/relay/op/transform.py 246-298](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/python/tvm/relay/op/transform.py#L246-L298)

#### Slicing and Indexing

| Operator | Purpose | Attributes |
| --- | --- | --- |
| `strided_slice` | Extract sub-tensor with strides | begin, end, strides, axes, slice_mode |
| `take` | Gather elements by indices | axis, mode (clip/wrap/fast) |
| `gather` | Gather along axis | axis |
| `gather_nd` | Multi-dimensional gather | None (indices specify full coordinates) |
| `scatter_elements` | Update elements by indices | axis, reduction (update/add/mul/mean/min/max) |
| `scatter_nd` | Multi-dimensional scatter | mode (update/add/mul) |

**Sources:**[src/relay/op/tensor/transform.cc 1100-1500](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/src/relay/op/tensor/transform.cc#L1100-L1500)[python/tvm/relay/op/transform.py 650-950](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/python/tvm/relay/op/transform.py#L650-L950)

## Type System and Type Relations

### Type Hierarchy

**Sources:**[include/tvm/relay/type.h](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/include/tvm/relay/type.h)[python/tvm/relay/ty.py](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/python/tvm/relay/ty.py)

### Type Relations and Inference

Each operator defines a type relation function that infers output types from input types:

#### Example: Conv2D Type Relation

The Conv2DRel function performs:

1.   Extract input data shape and kernel shape
2.   Compute output channels, height, width based on padding, strides, dilation
3.   Validate input/output dimensions match layout
4.   Assign output type via `reporter->Assign(types[2], output_type)`

**Sources:**[src/relay/op/nn/convolution.cc 50-150](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/src/relay/op/nn/convolution.cc#L50-L150)[src/relay/op/tensor/transform.cc 133-195](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/src/relay/op/tensor/transform.cc#L133-L195)

### Dynamic Shape Support

Relay supports dynamic shapes using `relay.Any()`:

**Sources:**[tests/python/relay/test_any.py](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/tests/python/relay/test_any.py)[src/relay/op/tensor/transform.cc 773-790](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/src/relay/op/tensor/transform.cc#L773-L790)

## Layout Transformation System

### Layout Representation

Layouts are represented using `tir::Layout` objects with named axes:

**Sources:**[include/tvm/tir/data_layout.h](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/include/tvm/tir/data_layout.h)[src/tir/ir/data_layout.cc](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/src/tir/ir/data_layout.cc)

### Layout Transformation Passes

Relay provides two main layout transformation passes:

#### AlterOpLayout

Suggests better layouts for operators based on target capabilities:

**Registration Example:**

*   CPU targets may suggest NCHW16c for vectorized execution
*   GPU targets may suggest NHWC for better memory coalescing

**Sources:**[python/tvm/relay/op/nn/_nn.py 106-110](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/python/tvm/relay/op/nn/_nn.py#L106-L110)[python/tvm/relay/transform.py 400-450](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/python/tvm/relay/transform.py#L400-L450)

#### ConvertLayout

Forces specific layouts for operators:

**Sources:**[python/tvm/relay/op/nn/_nn.py 242-316](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/python/tvm/relay/op/nn/_nn.py#L242-L316)[tests/python/relay/test_pass_convert_op_layout.py](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/tests/python/relay/test_pass_convert_op_layout.py)

### Layout Inference

The `FInferCorrectLayout` attribute enables layout inference:

**For transpose operator:**

**Sources:**[src/relay/op/tensor/transform.cc 551-624](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/src/relay/op/tensor/transform.cc#L551-L624)

## Optimization Passes

### Pass Infrastructure

Relay optimization passes inherit from `relay::transform::Pass`:

| Pass Type | Description | Examples |
| --- | --- | --- |
| `FunctionPass` | Transforms individual functions | FuseOps, FoldConstant |
| `ModulePass` | Transforms entire modules | InferType, Legalize |
| `Sequential` | Composes multiple passes | Standard optimization pipeline |

**Sources:**[include/tvm/relay/transform.h](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/include/tvm/relay/transform.h)[python/tvm/relay/transform.py](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/python/tvm/relay/transform.py)

### Major Optimization Passes

#### FuseOps

Fuses multiple operators into a single kernel to reduce memory traffic:

**Fusion patterns:**

*   Injective ops (reshape, transpose) fuse with any compatible op
*   Element-wise ops (relu, add) fuse with preceding ops
*   Broadcast ops fuse as consumers

**Sources:**[src/relay/transforms/fuse_ops.cc](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/src/relay/transforms/fuse_ops.cc)[include/tvm/relay/op_attr_types.h 50-80](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/include/tvm/relay/op_attr_types.h#L50-L80)

#### FoldConstant

Evaluates constant sub-expressions at compile time:

**Optimizations:**

*   Constant folding for shape computations
*   Pre-computation of static transformations
*   Reduction of runtime overhead

**Sources:**[src/relay/transforms/fold_constant.cc](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/src/relay/transforms/fold_constant.cc)

#### Legalize

Lowers high-level operators to target-specific implementations:

**Example legalization:**

*   Conv2d with int8 data → specialized int8 conv2d implementation
*   Dense with specific shapes → packed matrix multiplication
*   Batch matmul → series of matrix multiplications

**Sources:**[python/tvm/relay/op/nn/_nn.py 56-100](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/python/tvm/relay/op/nn/_nn.py#L56-L100)[src/relay/transforms/legalize.cc](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/src/relay/transforms/legalize.cc)

## Strategy System

### OpStrategy Architecture

The strategy system enables target-dependent operator implementations:

**Sources:**[python/tvm/relay/op/strategy/generic.py](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/python/tvm/relay/op/strategy/generic.py)[python/tvm/relay/op/strategy/cuda.py](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/python/tvm/relay/op/strategy/cuda.py)[python/tvm/relay/op/strategy/x86.py](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/python/tvm/relay/op/strategy/x86.py)

### Strategy Selection

Each strategy implementation includes:

*   **Compute function**: Generates TE compute definition
*   **Schedule function**: Provides scheduling strategy
*   **Priority level** (`plevel`): Higher values preferred
*   **Condition checks**: Validates applicability (data types, shapes, target features)

#### CUDA Conv2D Strategy Example

**Key decision factors:**

1.   Target features (Tensor Cores available, cuDNN enabled)
2.   Data layout and types (NHWC for Tensor Cores, int8 for specialized kernels)
3.   Shape constraints (batch size divisibility, kernel size for Winograd)
4.   Priority levels when multiple implementations applicable

**Sources:**[python/tvm/relay/op/strategy/cuda.py 138-461](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/python/tvm/relay/op/strategy/cuda.py#L138-L461)

### Strategy and Tuning Integration

Auto-tuning systems measure actual performance of each strategy implementation and select the fastest.

**Sources:**[python/tvm/relay/op/strategy/generic.py 34-46](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/python/tvm/relay/op/strategy/generic.py#L34-L46)[python/tvm/auto_scheduler/](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/python/tvm/auto_scheduler/)

## Integration with Compilation Pipeline

### Relay's Position in TVM

### Typical Compilation Workflow

1.   **Frontend Conversion**: Model → Relay IRModule
2.   **Type Inference**: `InferType()` pass resolves all types
3.   **Graph Optimization**: Sequential passes optimize the graph 
    *   `FoldConstant`: Evaluate constant expressions
    *   `FuseOps`: Merge operators for efficiency
    *   `AlterOpLayout`: Suggest better layouts
    *   `ConvertLayout`: Apply layout transformations
    *   `Legalize`: Lower to target-specific ops

4.   **Operator Lowering**: Relay ops → TIR PrimFuncs via strategy system
5.   **Kernel Optimization**: TIR scheduling and auto-tuning
6.   **Code Generation**: TIR → target code (LLVM IR, CUDA, etc.)

**Sources:**[python/tvm/relay/build_module.py](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/python/tvm/relay/build_module.py)[python/tvm/driver/build_module.py](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/python/tvm/driver/build_module.py)

## Key File Locations

### Core Implementation

| Component | C++ Implementation | Python Interface |
| --- | --- | --- |
| Transform operators | [src/relay/op/tensor/transform.cc](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/src/relay/op/tensor/transform.cc) | [python/tvm/relay/op/transform.py](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/python/tvm/relay/op/transform.py) |
| NN operators | [src/relay/op/nn/nn.cc](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/src/relay/op/nn/nn.cc)[src/relay/op/nn/convolution.cc](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/src/relay/op/nn/convolution.cc) | [python/tvm/relay/op/nn/nn.py](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/python/tvm/relay/op/nn/nn.py) |
| Type relations | [src/relay/op/type_relations.h](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/src/relay/op/type_relations.h) | [python/tvm/relay/ty.py](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/python/tvm/relay/ty.py) |
| Operator attributes | [include/tvm/relay/attrs/transform.h](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/include/tvm/relay/attrs/transform.h)[include/tvm/relay/attrs/nn.h](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/include/tvm/relay/attrs/nn.h) | [python/tvm/relay/op/op_attrs.py](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/python/tvm/relay/op/op_attrs.py) |
| Strategy system | [src/relay/op/strategy/](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/src/relay/op/strategy/) | [python/tvm/relay/op/strategy/](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/python/tvm/relay/op/strategy/) |
| Optimization passes | [src/relay/transforms/](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/src/relay/transforms/) | [python/tvm/relay/transform.py](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/python/tvm/relay/transform.py) |

### Testing

| Test Category | Location |
| --- | --- |
| Operator tests | [tests/python/relay/test_op_level1.py](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/tests/python/relay/test_op_level1.py)[tests/python/relay/test_op_level2.py](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/tests/python/relay/test_op_level2.py)[tests/python/relay/test_op_level3.py](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/tests/python/relay/test_op_level3.py) |
| Dynamic shape tests | [tests/python/relay/test_any.py](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/tests/python/relay/test_any.py) |
| Pass tests | [tests/python/relay/test_pass_alter_op_layout.py](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/tests/python/relay/test_pass_alter_op_layout.py)[tests/python/relay/test_pass_convert_op_layout.py](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/tests/python/relay/test_pass_convert_op_layout.py) |

**Sources:** All files listed in table

Dismiss
Refresh this wiki

Enter email to refresh
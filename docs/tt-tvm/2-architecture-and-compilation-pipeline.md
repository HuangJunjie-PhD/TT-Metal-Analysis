---
project: tt-tvm
github: https://github.com/tenstorrent/tt-tvm
deepwiki: https://deepwiki.com/tenstorrent/tt-tvm/2-architecture-and-compilation-pipeline
---

# Architecture and Compilation Pipeline

Relevant source files
*   [enable.sh](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/enable.sh)
*   [include/tvm/relay/attrs/nn.h](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/include/tvm/relay/attrs/nn.h)
*   [include/tvm/relay/attrs/transform.h](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/include/tvm/relay/attrs/transform.h)
*   [include/tvm/runtime/profiling.h](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/include/tvm/runtime/profiling.h)
*   [include/tvm/runtime/vm/executable.h](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/include/tvm/runtime/vm/executable.h)
*   [include/tvm/runtime/vm/vm.h](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/include/tvm/runtime/vm/vm.h)
*   [include/tvm/tir/schedule/schedule.h](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/include/tvm/tir/schedule/schedule.h)
*   [include/tvm/topi/transform.h](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/include/tvm/topi/transform.h)
*   [install.sh](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/install.sh)
*   [python/tvm/contrib/debugger/debug_executor.py](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/python/tvm/contrib/debugger/debug_executor.py)
*   [python/tvm/relay/backend/vm.py](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/python/tvm/relay/backend/vm.py)
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
*   [python/tvm/relay/testing/tf.py](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/python/tvm/relay/testing/tf.py)
*   [python/tvm/relay/transform/infer_layout_utils.py](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/python/tvm/relay/transform/infer_layout_utils.py)
*   [python/tvm/runtime/packed_func.py](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/python/tvm/runtime/packed_func.py)
*   [python/tvm/runtime/profiler_vm.py](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/python/tvm/runtime/profiler_vm.py)
*   [python/tvm/runtime/profiling/__init__.py](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/python/tvm/runtime/profiling/__init__.py)
*   [python/tvm/runtime/vm.py](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/python/tvm/runtime/vm.py)
*   [python/tvm/tir/schedule/schedule.py](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/python/tvm/tir/schedule/schedule.py)
*   [python/tvm/topi/__init__.py](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/python/tvm/topi/__init__.py)
*   [python/tvm/topi/cuda/__init__.py](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/python/tvm/topi/cuda/__init__.py)
*   [python/tvm/topi/scatter.py](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/python/tvm/topi/scatter.py)
*   [python/tvm/topi/transform.py](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/python/tvm/topi/transform.py)
*   [python/tvm/topi/x86/__init__.py](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/python/tvm/topi/x86/__init__.py)
*   [src/relay/backend/vm/compiler.cc](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/src/relay/backend/vm/compiler.cc)
*   [src/relay/backend/vm/compiler.h](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/src/relay/backend/vm/compiler.h)
*   [src/relay/op/nn/convolution.cc](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/src/relay/op/nn/convolution.cc)
*   [src/relay/op/nn/convolution.h](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/src/relay/op/nn/convolution.h)
*   [src/relay/op/nn/nn.cc](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/src/relay/op/nn/nn.cc)
*   [src/relay/op/nn/nn.h](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/src/relay/op/nn/nn.h)
*   [src/relay/op/nn/pad.cc](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/src/relay/op/nn/pad.cc)
*   [src/relay/op/nn/pooling.cc](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/src/relay/op/nn/pooling.cc)
*   [src/relay/op/tensor/reduce.cc](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/src/relay/op/tensor/reduce.cc)
*   [src/relay/op/tensor/transform.cc](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/src/relay/op/tensor/transform.cc)
*   [src/relay/op/tensor/transform.h](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/src/relay/op/tensor/transform.h)
*   [src/relay/printer/relay_text_printer.cc](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/src/relay/printer/relay_text_printer.cc)
*   [src/relay/printer/text_printer.h](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/src/relay/printer/text_printer.h)
*   [src/relay/transforms/alter_op_layout.cc](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/src/relay/transforms/alter_op_layout.cc)
*   [src/relay/transforms/canonicalize_cast.cc](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/src/relay/transforms/canonicalize_cast.cc)
*   [src/relay/transforms/convert_layout.cc](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/src/relay/transforms/convert_layout.cc)
*   [src/relay/transforms/forward_rewrite.cc](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/src/relay/transforms/forward_rewrite.cc)
*   [src/relay/transforms/infer_layout_utils.cc](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/src/relay/transforms/infer_layout_utils.cc)
*   [src/relay/transforms/infer_layout_utils.h](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/src/relay/transforms/infer_layout_utils.h)
*   [src/relay/transforms/legalize.cc](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/src/relay/transforms/legalize.cc)
*   [src/relay/transforms/transform_layout.h](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/src/relay/transforms/transform_layout.h)
*   [src/runtime/contrib/cudnn/softmax.cc](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/src/runtime/contrib/cudnn/softmax.cc)
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
*   [src/tir/schedule/analysis.h](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/src/tir/schedule/analysis.h)
*   [src/tir/schedule/analysis/analysis.cc](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/src/tir/schedule/analysis/analysis.cc)
*   [src/tir/schedule/concrete_schedule.cc](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/src/tir/schedule/concrete_schedule.cc)
*   [src/tir/schedule/concrete_schedule.h](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/src/tir/schedule/concrete_schedule.h)
*   [src/tir/schedule/primitive.h](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/src/tir/schedule/primitive.h)
*   [src/tir/schedule/primitive/cache_read_write.cc](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/src/tir/schedule/primitive/cache_read_write.cc)
*   [src/tir/schedule/primitive/get_block_loop.cc](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/src/tir/schedule/primitive/get_block_loop.cc)
*   [src/tir/schedule/schedule.cc](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/src/tir/schedule/schedule.cc)
*   [src/tir/schedule/traced_schedule.cc](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/src/tir/schedule/traced_schedule.cc)
*   [src/tir/schedule/traced_schedule.h](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/src/tir/schedule/traced_schedule.h)
*   [src/tir/schedule/transform.cc](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/src/tir/schedule/transform.cc)
*   [src/tir/schedule/transform.h](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/src/tir/schedule/transform.h)
*   [src/tir/schedule/utils.h](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/src/tir/schedule/utils.h)
*   [src/topi/transform.cc](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/src/topi/transform.cc)
*   [tests/python/all-platform-minimal-test/README.md](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/tests/python/all-platform-minimal-test/README.md?plain=1)
*   [tests/python/all-platform-minimal-test/test_minimal_target_codegen_llvm.py](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/tests/python/all-platform-minimal-test/test_minimal_target_codegen_llvm.py)
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
*   [tests/python/relay/test_any.py](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/tests/python/relay/test_any.py)
*   [tests/python/relay/test_op_level1.py](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/tests/python/relay/test_op_level1.py)
*   [tests/python/relay/test_op_level10.py](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/tests/python/relay/test_op_level10.py)
*   [tests/python/relay/test_op_level2.py](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/tests/python/relay/test_op_level2.py)
*   [tests/python/relay/test_op_level3.py](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/tests/python/relay/test_op_level3.py)
*   [tests/python/relay/test_op_level4.py](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/tests/python/relay/test_op_level4.py)
*   [tests/python/relay/test_pass_alter_op_layout.py](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/tests/python/relay/test_pass_alter_op_layout.py)
*   [tests/python/relay/test_pass_convert_op_layout.py](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/tests/python/relay/test_pass_convert_op_layout.py)
*   [tests/python/relay/test_vm.py](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/tests/python/relay/test_vm.py)
*   [tests/python/relay/test_vm_serialization.py](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/tests/python/relay/test_vm_serialization.py)
*   [tests/python/topi/python/test_topi_scatter.py](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/tests/python/topi/python/test_topi_scatter.py)
*   [tests/python/topi/python/test_topi_transform.py](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/tests/python/topi/python/test_topi_transform.py)
*   [tests/python/unittest/test_runtime_profiling.py](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/tests/python/unittest/test_runtime_profiling.py)
*   [tests/python/unittest/test_runtime_vm_profiler.py](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/tests/python/unittest/test_runtime_vm_profiler.py)
*   [tests/python/unittest/test_target_codegen_llvm.py](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/tests/python/unittest/test_target_codegen_llvm.py)
*   [tests/python/unittest/test_tir_schedule_cache_read_write.py](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/tests/python/unittest/test_tir_schedule_cache_read_write.py)
*   [tests/python/unittest/test_tir_schedule_reindex.py](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/tests/python/unittest/test_tir_schedule_reindex.py)
*   [tests/python/unittest/test_tir_schedule_utilities.py](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/tests/python/unittest/test_tir_schedule_utilities.py)

## Purpose and Scope

This document describes the overall architecture of tt-tvm and the compilation pipeline that transforms machine learning models from framework-specific representations into optimized executable code. It provides a high-level view of how the major system components interact during compilation and execution.

For detailed information about specific components, see:

*   Frontend model ingestion: [Frontend Converters](https://deepwiki.com/tenstorrent/tt-tvm/3-frontend-converters)
*   Graph-level IR and optimization: [Relay IR System](https://deepwiki.com/tenstorrent/tt-tvm/4-relay-ir-system)
*   Kernel-level optimization: [TensorIR (TIR) System](https://deepwiki.com/tenstorrent/tt-tvm/5-tensorir-(tir)-system)
*   Code generation: [Code Generation Backends](https://deepwiki.com/tenstorrent/tt-tvm/6-code-generation-backends)
*   Model execution: [Runtime and Execution](https://deepwiki.com/tenstorrent/tt-tvm/7-runtime-and-execution)
*   Performance tuning: [Auto-Tuning System](https://deepwiki.com/tenstorrent/tt-tvm/8-auto-tuning-system)

## High-Level Architecture

tt-tvm is structured as a multi-layered compilation system that progressively lowers machine learning models from high-level framework representations to optimized machine code.

**Architectural Layers**

Sources: [python/tvm/relay/frontend/pytorch.py 59](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/python/tvm/relay/frontend/pytorch.py#L59-L59)[python/tvm/relay/frontend/onnx.py 67](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/python/tvm/relay/frontend/onnx.py#L67-L67)[python/tvm/relay/frontend/tensorflow.py 50](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/python/tvm/relay/frontend/tensorflow.py#L50-L50)[python/tvm/relay/frontend/tflite.py 46](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/python/tvm/relay/frontend/tflite.py#L46-L46)[python/tvm/relay/frontend/mxnet.py 70](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/python/tvm/relay/frontend/mxnet.py#L70-L70)[python/tvm/relay/expr.py](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/python/tvm/relay/expr.py)[python/tvm/tir/schedule/schedule.py](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/python/tvm/tir/schedule/schedule.py)[src/target/llvm/codegen_llvm.cc](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/src/target/llvm/codegen_llvm.cc)[src/relay/backend/vm/compiler.cc](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/src/relay/backend/vm/compiler.cc)

## Compilation Pipeline Flow

The compilation pipeline consists of five major stages, each transforming the program representation to enable different optimizations.

**Stage 1: Frontend Conversion**

Frontend converters transform framework-specific model formats into Relay IR:

| Framework | Entry Point | Output |
| --- | --- | --- |
| PyTorch | `relay.frontend.from_pytorch()` | `IRModule` + parameters |
| ONNX | `relay.frontend.from_onnx()` | `IRModule` + parameters |
| TensorFlow | `relay.frontend.from_tensorflow()` | `IRModule` + parameters |
| TFLite | `relay.frontend.from_tflite()` | `IRModule` + parameters |
| MXNet | `relay.frontend.from_mxnet()` | `IRModule` + parameters |

Each converter produces an `IRModule` containing `relay.Function` objects that represent the computational graph using Relay operators. Model weights are returned separately as a parameter dictionary.

Sources: [python/tvm/relay/frontend/pytorch.py 59](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/python/tvm/relay/frontend/pytorch.py#L59-L59)[python/tvm/relay/frontend/onnx.py 562-584](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/python/tvm/relay/frontend/onnx.py#L562-L584)[python/tvm/relay/frontend/tensorflow.py 50](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/python/tvm/relay/frontend/tensorflow.py#L50-L50)[python/tvm/relay/frontend/tflite.py 59-79](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/python/tvm/relay/frontend/tflite.py#L59-L79)[python/tvm/relay/frontend/mxnet.py 70](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/python/tvm/relay/frontend/mxnet.py#L70-L70)

**Stage 2: Relay IR Optimization**

Relay IR undergoes graph-level optimizations through a series of transformation passes:

Key passes execute in a coordinated sequence:

*   **InferType**: Propagates type information throughout the graph
*   **FuseOps**: Merges adjacent operators to reduce kernel launches
*   **AlterOpLayout**: Transforms data layouts for target-specific efficiency
*   **FoldConstant**: Evaluates constant expressions at compile time
*   **Legalize**: Converts operators to target-specific implementations

Sources: [python/tvm/relay/transform/__init__.py](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/python/tvm/relay/transform/__init__.py)[src/relay/transforms/](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/src/relay/transforms/)

**Stage 3: TensorIR Lowering and Scheduling**

Relay operators are lowered to TensorIR `PrimFunc` representations, which are then optimized through scheduling primitives:

The `Schedule` API provides transformation primitives that manipulate loop structures and memory access patterns to optimize for specific hardware targets.

Sources: [python/tvm/relay/op/strategy/generic.py](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/python/tvm/relay/op/strategy/generic.py)[python/tvm/tir/schedule/schedule.py](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/python/tvm/tir/schedule/schedule.py)[python/tvm/te/](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/python/tvm/te/)

**Stage 4: Backend Code Generation**

Optimized TensorIR is compiled to executable code through target-specific backends:

| Backend | Target | Code Generator | Output Format |
| --- | --- | --- | --- |
| LLVM | CPU (x86, ARM, etc.) | `CodeGenLLVM` | LLVM IR → Machine code |
| CUDA | NVIDIA GPUs | `CodeGenCUDA` | PTX/CUDA C |
| VM | Dynamic execution | `VMCompiler` | Bytecode |
| AOT | Embedded systems | `AOTExecutorCodegen` | C code |

Each backend implements target-specific optimizations and generates the appropriate executable format.

Sources: [src/target/llvm/codegen_llvm.cc 28](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/src/target/llvm/codegen_llvm.cc#L28-L28)[src/relay/backend/vm/compiler.cc 28](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/src/relay/backend/vm/compiler.cc#L28-L28)[src/target/source/codegen_cuda.cc](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/src/target/source/codegen_cuda.cc)

**Stage 5: Runtime Execution**

Compiled code is executed through one of three runtime systems:

*   **GraphExecutor**: Static graph execution with fixed memory allocation
*   **VirtualMachine**: Dynamic execution supporting control flow
*   **AOTExecutor**: Minimal runtime for embedded deployment

Sources: [src/runtime/graph_executor/graph_executor.cc](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/src/runtime/graph_executor/graph_executor.cc)[src/runtime/vm/executable.cc](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/src/runtime/vm/executable.cc)[src/runtime/crt/](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/src/runtime/crt/)

## Detailed Component Interactions

**Frontend to Relay Conversion**

The conversion from framework models to Relay IR involves operator-by-operator translation:

The `PyTorchOpConverter` class maps PyTorch operator names to conversion functions. Each conversion function uses common utilities like `AttrCvt` for attribute translation and `infer_type` for shape and type inference.

Sources: [python/tvm/relay/frontend/pytorch.py 156-168](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/python/tvm/relay/frontend/pytorch.py#L156-L168)[python/tvm/relay/frontend/common.py](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/python/tvm/relay/frontend/common.py)

**Relay to TIR Lowering**

Relay operators are lowered to TensorIR through the strategy system:

The strategy system allows multiple implementations per operator, selecting the best one based on target characteristics and input shapes.

Sources: [python/tvm/relay/op/strategy/generic.py](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/python/tvm/relay/op/strategy/generic.py)[python/tvm/relay/op/nn/_nn.py 102-103](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/python/tvm/relay/op/nn/_nn.py#L102-L103)[python/tvm/te/__init__.py](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/python/tvm/te/__init__.py)

**TIR to Machine Code**

TensorIR is compiled to machine code through LLVM or direct code generation:

The LLVM backend generates LLVM IR which is then optimized and compiled to native machine code. The VM backend generates bytecode for dynamic execution.

Sources: [src/target/llvm/codegen_llvm.cc](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/src/target/llvm/codegen_llvm.cc)[src/relay/backend/vm/compiler.cc](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/src/relay/backend/vm/compiler.cc)

## End-to-End Compilation Example

Consider a simple PyTorch model being compiled for CPU execution:

**Step 1: Model Definition and Tracing**

**Step 2: Frontend Conversion**

The `from_pytorch` function produces an `IRModule` containing a Relay function:

```
def @main(%input0: Tensor[(1, 3, 224, 224), float32]) {
  %0 = nn.conv2d(%input0, %weight0, ...);
  %1 = nn.relu(%0);
  %2 = nn.max_pool2d(%1, ...);
  %2
}
```

Sources: [python/tvm/relay/frontend/pytorch.py 4000-4100](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/python/tvm/relay/frontend/pytorch.py#L4000-L4100)

**Step 3: Relay Optimization**

After optimization, operators may be fused and layouts transformed:

```
def @main(%input0: Tensor[(1, 3, 224, 224), float32]) {
  %0 = fn(%x, %w) {  // Fused conv2d + relu
    %1 = nn.conv2d(%x, %w, ...);
    nn.relu(%1)
  };
  %2 = %0(%input0, %weight0);
  %3 = nn.max_pool2d(%2, ...);
  %3
}
```

Sources: [python/tvm/relay/transform/transform.py](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/python/tvm/relay/transform/transform.py)

**Step 4: Lowering to TIR**

During `relay.build()`, operators are lowered to TensorIR:

Each Relay operator becomes one or more `PrimFunc` objects with loop structures that can be optimized through scheduling.

Sources: [python/tvm/relay/build_module.py](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/python/tvm/relay/build_module.py)

**Step 5: Code Generation**

The `CodeGenLLVM` backend converts TIR to LLVM IR, then to native machine code:

```
PrimFunc → LLVM IR → x86-64 Assembly → Shared Library (.so)
```

Sources: [src/target/llvm/codegen_llvm.cc 370-450](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/src/target/llvm/codegen_llvm.cc#L370-L450)

**Step 6: Execution**

The `GraphExecutor` allocates memory based on the static graph and executes compiled kernels in sequence.

Sources: [python/tvm/contrib/graph_executor.py](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/python/tvm/contrib/graph_executor.py)[src/runtime/graph_executor/graph_executor.cc](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/src/runtime/graph_executor/graph_executor.cc)

## Data Structures and Representations

**IRModule Structure**

An `IRModule` is the top-level container for Relay programs:

Sources: [python/tvm/relay/expr.py](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/python/tvm/relay/expr.py)[python/tvm/ir/module.py](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/python/tvm/ir/module.py)

**PrimFunc Structure**

A `PrimFunc` represents a single computational kernel in TensorIR:

| Component | Type | Purpose |
| --- | --- | --- |
| `params` | `List[Var]` | Input buffer parameters |
| `body` | `Stmt` | Loop nest and computation |
| `buffer_map` | `Map[Var, Buffer]` | Parameter to buffer mapping |
| `ret_type` | `Type` | Return type annotation |
| `attrs` | `DictAttrs` | Metadata and annotations |

The `body` contains the loop structure with statements like `For`, `BufferStore`, and `IfThenElse`.

Sources: [python/tvm/tir/function.py](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/python/tvm/tir/function.py)[include/tvm/tir/function.h](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/include/tvm/tir/function.h)

## Compilation Modes and Execution Paths

tt-tvm supports multiple compilation and execution modes optimized for different use cases:

| Mode | Use Case | Memory | Control Flow | Entry Point |
| --- | --- | --- | --- | --- |
| **Graph Executor** | Production inference | Static, pre-allocated | None | `relay.build()` + `GraphExecutor` |
| **VM Executor** | Dynamic shapes, control flow | Dynamic allocation | Full support | `relay.vm.compile()` + `VirtualMachine` |
| **AOT Executor** | Embedded, microcontrollers | Static, minimal runtime | None | `relay.build()` with AOT target |
| **Interpreter** | Debugging, testing | Dynamic | Full support | `relay.create_executor("debug")` |

**Graph Executor Path**

Sources: [python/tvm/relay/build_module.py 390-450](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/python/tvm/relay/build_module.py#L390-L450)[src/runtime/graph_executor/graph_executor.cc](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/src/runtime/graph_executor/graph_executor.cc)

**VM Executor Path**

Sources: [python/tvm/relay/backend/vm.py](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/python/tvm/relay/backend/vm.py)[src/relay/backend/vm/compiler.cc](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/src/relay/backend/vm/compiler.cc)[src/runtime/vm/executable.cc](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/src/runtime/vm/executable.cc)

## Target-Specific Compilation

The compilation pipeline adapts to different hardware targets through target-specific strategies and code generators:

**Target Configuration**

**Strategy Dispatch**

Each target can provide multiple implementations with priority levels. The highest priority valid implementation is selected during compilation.

Sources: [python/tvm/relay/op/strategy/generic.py](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/python/tvm/relay/op/strategy/generic.py)[python/tvm/relay/op/strategy/cuda.py](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/python/tvm/relay/op/strategy/cuda.py)[python/tvm/relay/op/strategy/arm_cpu.py](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/python/tvm/relay/op/strategy/arm_cpu.py)

## Performance Optimization Features

**Auto-Tuning Integration**

The compilation pipeline can leverage auto-tuning to find optimal schedules:

The tuning results are stored and automatically applied during subsequent compilations.

Sources: [python/tvm/autotvm/task/task.py](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/python/tvm/autotvm/task/task.py)[python/tvm/autotvm/tuner/](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/python/tvm/autotvm/tuner/)

**Memory Planning**

The compilation pipeline includes memory planning to minimize memory footprint:

*   **Storage allocation**: Reuses memory across non-overlapping tensors
*   **Workspace planning**: Temporary buffers for operators
*   **Constant folding**: Pre-computes constant expressions to reduce runtime memory

Sources: [src/relay/backend/graph_executor_codegen.cc](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/src/relay/backend/graph_executor_codegen.cc)

## Summary

The tt-tvm compilation pipeline transforms high-level ML models through five distinct stages:

1.   **Frontend Conversion**: Framework models → Relay IR (`IRModule`)
2.   **Relay Optimization**: Graph-level passes → Optimized Relay IR
3.   **TIR Lowering**: Relay operators → TensorIR kernels (`PrimFunc`)
4.   **Code Generation**: TensorIR → Executable code (LLVM/VM/AOT)
5.   **Runtime Execution**: Execute compiled model (Graph/VM/AOT Executor)

Each stage operates on well-defined intermediate representations, enabling modular optimization and supporting diverse hardware targets. The architecture allows developers to extend the system at any level, from adding new frontends to implementing custom code generators.

For implementation details of each component, see the corresponding sections: [Frontend Converters](https://deepwiki.com/tenstorrent/tt-tvm/3-frontend-converters), [Relay IR System](https://deepwiki.com/tenstorrent/tt-tvm/4-relay-ir-system), [TensorIR System](https://deepwiki.com/tenstorrent/tt-tvm/5-tensorir-(tir)-system), [Code Generation Backends](https://deepwiki.com/tenstorrent/tt-tvm/6-code-generation-backends), and [Runtime and Execution](https://deepwiki.com/tenstorrent/tt-tvm/7-runtime-and-execution).

Dismiss
Refresh this wiki

Enter email to refresh
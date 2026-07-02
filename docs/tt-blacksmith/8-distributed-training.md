---
project: tt-blacksmith
github: https://github.com/tenstorrent/tt-blacksmith
deepwiki: https://deepwiki.com/tenstorrent/tt-blacksmith/8-distributed-training
---

# Distributed Training

Relevant source files
*   [blacksmith/datasets/jax/mnist/dataloader.py](https://github.com/tenstorrent/tt-blacksmith/blob/fe25eba0/blacksmith/datasets/jax/mnist/dataloader.py)
*   [blacksmith/experiments/jax/mnist/configs.py](https://github.com/tenstorrent/tt-blacksmith/blob/fe25eba0/blacksmith/experiments/jax/mnist/configs.py)
*   [blacksmith/experiments/jax/mnist/logging/wandb_utils.py](https://github.com/tenstorrent/tt-blacksmith/blob/fe25eba0/blacksmith/experiments/jax/mnist/logging/wandb_utils.py)
*   [blacksmith/experiments/jax/mnist/multi_chip/data_parallel/test_pure_jax_mnist.py](https://github.com/tenstorrent/tt-blacksmith/blob/fe25eba0/blacksmith/experiments/jax/mnist/multi_chip/data_parallel/test_pure_jax_mnist.py)
*   [blacksmith/experiments/jax/mnist/multi_chip/tensor_parallel/test_pure_jax_mnist.py](https://github.com/tenstorrent/tt-blacksmith/blob/fe25eba0/blacksmith/experiments/jax/mnist/multi_chip/tensor_parallel/test_pure_jax_mnist.py)
*   [blacksmith/experiments/jax/mnist/single_chip/test_flax_mnist.py](https://github.com/tenstorrent/tt-blacksmith/blob/fe25eba0/blacksmith/experiments/jax/mnist/single_chip/test_flax_mnist.py)
*   [blacksmith/experiments/jax/mnist/single_chip/test_nnx_mnist.py](https://github.com/tenstorrent/tt-blacksmith/blob/fe25eba0/blacksmith/experiments/jax/mnist/single_chip/test_nnx_mnist.py)
*   [blacksmith/experiments/jax/mnist/single_chip/test_pure_jax_mnist.py](https://github.com/tenstorrent/tt-blacksmith/blob/fe25eba0/blacksmith/experiments/jax/mnist/single_chip/test_pure_jax_mnist.py)
*   [blacksmith/experiments/torch/mnist/README.md](https://github.com/tenstorrent/tt-blacksmith/blob/fe25eba0/blacksmith/experiments/torch/mnist/README.md?plain=1)
*   [blacksmith/experiments/torch/mnist/cnn/test_mnist_cnn_training.py](https://github.com/tenstorrent/tt-blacksmith/blob/fe25eba0/blacksmith/experiments/torch/mnist/cnn/test_mnist_cnn_training.py)
*   [blacksmith/experiments/torch/mnist/cnn/test_mnist_cnn_training.yaml](https://github.com/tenstorrent/tt-blacksmith/blob/fe25eba0/blacksmith/experiments/torch/mnist/cnn/test_mnist_cnn_training.yaml)
*   [blacksmith/experiments/torch/mnist/configs.py](https://github.com/tenstorrent/tt-blacksmith/blob/fe25eba0/blacksmith/experiments/torch/mnist/configs.py)
*   [blacksmith/experiments/torch/mnist/data_parallel/test_mnist_training.py](https://github.com/tenstorrent/tt-blacksmith/blob/fe25eba0/blacksmith/experiments/torch/mnist/data_parallel/test_mnist_training.py)
*   [blacksmith/experiments/torch/mnist/data_parallel/test_mnist_training_dp.yaml](https://github.com/tenstorrent/tt-blacksmith/blob/fe25eba0/blacksmith/experiments/torch/mnist/data_parallel/test_mnist_training_dp.yaml)
*   [blacksmith/experiments/torch/mnist/data_parallel/utils.py](https://github.com/tenstorrent/tt-blacksmith/blob/fe25eba0/blacksmith/experiments/torch/mnist/data_parallel/utils.py)
*   [blacksmith/experiments/torch/mnist/tensor_parallel/test_mnist_training.py](https://github.com/tenstorrent/tt-blacksmith/blob/fe25eba0/blacksmith/experiments/torch/mnist/tensor_parallel/test_mnist_training.py)
*   [blacksmith/experiments/torch/mnist/tensor_parallel/test_mnist_training_tp.yaml](https://github.com/tenstorrent/tt-blacksmith/blob/fe25eba0/blacksmith/experiments/torch/mnist/tensor_parallel/test_mnist_training_tp.yaml)
*   [blacksmith/experiments/torch/mnist/tensor_parallel/utils.py](https://github.com/tenstorrent/tt-blacksmith/blob/fe25eba0/blacksmith/experiments/torch/mnist/tensor_parallel/utils.py)
*   [blacksmith/experiments/torch/mnist/test_mnist_training.py](https://github.com/tenstorrent/tt-blacksmith/blob/fe25eba0/blacksmith/experiments/torch/mnist/test_mnist_training.py)
*   [blacksmith/experiments/torch/mnist/test_mnist_training.yaml](https://github.com/tenstorrent/tt-blacksmith/blob/fe25eba0/blacksmith/experiments/torch/mnist/test_mnist_training.yaml)
*   [blacksmith/models/torch/mnist/mnist_cnn.py](https://github.com/tenstorrent/tt-blacksmith/blob/fe25eba0/blacksmith/models/torch/mnist/mnist_cnn.py)
*   [blacksmith/tools/device_manager.py](https://github.com/tenstorrent/tt-blacksmith/blob/fe25eba0/blacksmith/tools/device_manager.py)

This document provides an overview of distributed training capabilities in TT-Blacksmith for multi-chip Tenstorrent hardware. It covers the mesh-based parallelization architecture, the role of `DeviceManager`, and configuration patterns for scaling training across multiple devices.

For implementation details of specific strategies, see [Data Parallelism](https://deepwiki.com/tenstorrent/tt-blacksmith/8.1-data-parallelism), [Tensor Parallelism](https://deepwiki.com/tenstorrent/tt-blacksmith/8.2-tensor-parallelism), and [Hybrid Parallelism Strategies](https://deepwiki.com/tenstorrent/tt-blacksmith/8.3-hybrid-parallelism-strategies). For hardware abstraction concepts, see [Device Management and Hardware Abstraction](https://deepwiki.com/tenstorrent/tt-blacksmith/5.2-device-management-and-hardware-abstraction).

## Architecture Overview

TT-Blacksmith uses a **mesh-based parallelization model** built on top of `torch_xla` SPMD (Single Program Multiple Data) for PyTorch experiments and JAX's native sharding API for JAX experiments. The mesh abstraction allows users to define a logical grid of devices and specify how data and model parameters should be distributed across them.

The core abstraction is the **mesh**, which has:

*   **Shape**: A tuple defining the device grid dimensions, e.g., `[2, 4]` for 8 devices arranged in 2 rows and 4 columns
*   **Axis Names**: Semantic labels for each dimension, typically `["data", "model"]` to indicate data parallelism and model parallelism axes

### Distributed Training Architecture

**Sources:**[blacksmith/tools/device_manager.py 19-137](https://github.com/tenstorrent/tt-blacksmith/blob/fe25eba0/blacksmith/tools/device_manager.py#L19-L137)[blacksmith/experiments/torch/mnist/configs.py 77-84](https://github.com/tenstorrent/tt-blacksmith/blob/fe25eba0/blacksmith/experiments/torch/mnist/configs.py#L77-L84)

## DeviceManager: Core Orchestration

The `DeviceManager` class is responsible for:

1.   Detecting and configuring the target hardware environment
2.   Creating the device mesh based on configuration
3.   Determining which parallelization strategies are active
4.   Applying sharding to batches and models
5.   Orchestrating optimizer synchronization

### DeviceManager Code Entity Map

**Sources:**[blacksmith/tools/device_manager.py 19-137](https://github.com/tenstorrent/tt-blacksmith/blob/fe25eba0/blacksmith/tools/device_manager.py#L19-L137)

### Key Methods

| Method | Purpose | Returns |
| --- | --- | --- |
| `is_data_parallel()` | Checks if "data" axis size > 1 in mesh | `bool` |
| `is_tensor_parallel()` | Checks if "model" axis size > 1 in mesh | `bool` |
| `prepare_batch(batch)` | Shards batch along data axis if DP enabled | `Dict[str, Tensor]` |
| `shard_model(model)` | Applies tensor parallelism patterns to model | `nn.Module` |
| `optimizer_step(optimizer)` | Synchronizes gradients and updates parameters | `None` |

**Sources:**[blacksmith/tools/device_manager.py 71-137](https://github.com/tenstorrent/tt-blacksmith/blob/fe25eba0/blacksmith/tools/device_manager.py#L71-L137)

## Configuration-Driven Parallelization

Parallelization strategies are entirely configuration-driven. Users specify their desired strategy through YAML configuration files without modifying code.

### Configuration Patterns

**Sources:**[blacksmith/experiments/torch/mnist/test_mnist_training.yaml 53-55](https://github.com/tenstorrent/tt-blacksmith/blob/fe25eba0/blacksmith/experiments/torch/mnist/test_mnist_training.yaml#L53-L55)[blacksmith/experiments/torch/mnist/data_parallel/test_mnist_training_dp.yaml 53-55](https://github.com/tenstorrent/tt-blacksmith/blob/fe25eba0/blacksmith/experiments/torch/mnist/data_parallel/test_mnist_training_dp.yaml#L53-L55)[blacksmith/experiments/torch/mnist/tensor_parallel/test_mnist_training_tp.yaml 53-61](https://github.com/tenstorrent/tt-blacksmith/blob/fe25eba0/blacksmith/experiments/torch/mnist/tensor_parallel/test_mnist_training_tp.yaml#L53-L61)

### Configuration Field Reference

| Field | Type | Purpose | Example |
| --- | --- | --- | --- |
| `mesh_shape` | `list[int]` or `null` | Defines device grid dimensions | `[2, 4]` for 8 devices in 2×4 grid |
| `mesh_axis_names` | `list[str]` or `null` | Semantic labels for mesh axes | `["data", "model"]` |
| `model_sharding_patterns` | `list[tuple]` or `null` | Regex patterns for tensor parallelism | `[["layer.*", [null, "model"]]]` |

**Sources:**[blacksmith/experiments/torch/mnist/configs.py 77-84](https://github.com/tenstorrent/tt-blacksmith/blob/fe25eba0/blacksmith/experiments/torch/mnist/configs.py#L77-L84)

## Distributed Training Execution Flow

The following diagram shows how a training step executes differently based on parallelization strategy:

**Sources:**[blacksmith/experiments/torch/mnist/data_parallel/test_mnist_training.py 105-124](https://github.com/tenstorrent/tt-blacksmith/blob/fe25eba0/blacksmith/experiments/torch/mnist/data_parallel/test_mnist_training.py#L105-L124)[blacksmith/experiments/torch/mnist/tensor_parallel/test_mnist_training.py 112-132](https://github.com/tenstorrent/tt-blacksmith/blob/fe25eba0/blacksmith/experiments/torch/mnist/tensor_parallel/test_mnist_training.py#L112-L132)[blacksmith/tools/device_manager.py 115-136](https://github.com/tenstorrent/tt-blacksmith/blob/fe25eba0/blacksmith/tools/device_manager.py#L115-L136)

## PyTorch vs JAX Implementations

Both PyTorch and JAX experiments support distributed training, but with different APIs:

### PyTorch (torch_xla)

PyTorch experiments use `torch_xla.distributed.spmd` for SPMD parallelization:

```
DeviceManager creates xs.Mesh
├── xs.mark_sharding(tensor, mesh, partition_spec) for sharding
└── xm.optimizer_step(optimizer, barrier=True) for synchronization
```

**Example:**[blacksmith/experiments/torch/mnist/data_parallel/test_mnist_training.py 105-124](https://github.com/tenstorrent/tt-blacksmith/blob/fe25eba0/blacksmith/experiments/torch/mnist/data_parallel/test_mnist_training.py#L105-L124)

### JAX (Native Sharding)

JAX experiments use `jax.sharding` API with `shard_map`:

```
Mesh created with jax.sharding.Mesh
├── NamedSharding defines partition specs
├── shard_map.shard_map() for SPMD functions
└── lax.all_gather / lax.psum for collectives
```

**Example:**[blacksmith/experiments/jax/mnist/multi_chip/data_parallel/test_pure_jax_mnist.py 162-174](https://github.com/tenstorrent/tt-blacksmith/blob/fe25eba0/blacksmith/experiments/jax/mnist/multi_chip/data_parallel/test_pure_jax_mnist.py#L162-L174)

**Sources:**[blacksmith/tools/device_manager.py 1-137](https://github.com/tenstorrent/tt-blacksmith/blob/fe25eba0/blacksmith/tools/device_manager.py#L1-L137)[blacksmith/experiments/jax/mnist/multi_chip/data_parallel/test_pure_jax_mnist.py 1-326](https://github.com/tenstorrent/tt-blacksmith/blob/fe25eba0/blacksmith/experiments/jax/mnist/multi_chip/data_parallel/test_pure_jax_mnist.py#L1-L326)

## Environment Configuration

Multi-chip training requires specific environment variables to be set, which `DeviceManager._setup_tt_environment()` handles automatically:

| Environment Variable | Purpose | Value |
| --- | --- | --- |
| `PJRT_DEVICE` | Specify target device | `"TT"` |
| `XLA_STABLEHLO_COMPILE` | Enable StableHLO compilation | `"1"` |
| `XLA_ALWAYS_ALLREDUCE` | Force all-reduce operations | `"1"` (multi-chip only) |
| `CONVERT_SHLO_TO_SHARDY` | Enable Shardy partitioner | `"1"` (multi-chip only) |
| `DISABLE_NUMERIC_CC_TOKEN` | Disable numeric tokens | `"1"` (multi-chip only) |

**Sources:**[blacksmith/tools/device_manager.py 38-49](https://github.com/tenstorrent/tt-blacksmith/blob/fe25eba0/blacksmith/tools/device_manager.py#L38-L49)

## Example: MNIST Distributed Training

TT-Blacksmith includes MNIST examples demonstrating each parallelization strategy:

### Single Chip

```
blacksmith/experiments/torch/mnist/test_mnist_training.py
config: mesh_shape: null
behavior: Standard single-device training
```

### Data Parallel (2 Chips)

```
blacksmith/experiments/torch/mnist/data_parallel/test_mnist_training.py
config: mesh_shape: [1, 2], mesh_axis_names: ["model", "data"]
behavior: Batch split across 2 devices, model replicated
```

### Tensor Parallel (2 Chips)

```
blacksmith/experiments/torch/mnist/tensor_parallel/test_mnist_training.py
config: mesh_shape: [1, 2], mesh_axis_names: ["data", "model"]
       model_sharding_patterns: [...regex patterns...]
behavior: Model weights split across 2 devices, batch replicated
```

**Sources:**[blacksmith/experiments/torch/mnist/README.md 1-116](https://github.com/tenstorrent/tt-blacksmith/blob/fe25eba0/blacksmith/experiments/torch/mnist/README.md?plain=1#L1-L116)[blacksmith/experiments/torch/mnist/test_mnist_training.py 1-197](https://github.com/tenstorrent/tt-blacksmith/blob/fe25eba0/blacksmith/experiments/torch/mnist/test_mnist_training.py#L1-L197)[blacksmith/experiments/torch/mnist/data_parallel/test_mnist_training.py 1-195](https://github.com/tenstorrent/tt-blacksmith/blob/fe25eba0/blacksmith/experiments/torch/mnist/data_parallel/test_mnist_training.py#L1-L195)[blacksmith/experiments/torch/mnist/tensor_parallel/test_mnist_training.py 1-205](https://github.com/tenstorrent/tt-blacksmith/blob/fe25eba0/blacksmith/experiments/torch/mnist/tensor_parallel/test_mnist_training.py#L1-L205)

## Gradient Synchronization

The `optimizer_step` method in `DeviceManager` handles gradient synchronization differently for single vs. multi-chip scenarios:

**Single Chip:**

`optimizer.step()if self.config.use_tt:    torch_xla.sync(wait=True)`
**Multi-Chip:**

`xm.optimizer_step(optimizer, barrier=True)# Forces graph execution and ensures correct all-reduce operations`
The multi-chip path uses `xm.optimizer_step` with `barrier=True`, which:

1.   Forces execution of the computation graph
2.   Applies all-reduce to gradients across the mesh
3.   Updates parameters with synchronized gradients
4.   Ensures all devices finish before proceeding

**Sources:**[blacksmith/tools/device_manager.py 127-136](https://github.com/tenstorrent/tt-blacksmith/blob/fe25eba0/blacksmith/tools/device_manager.py#L127-L136)

## Loss Function Considerations

Multi-chip training requires special handling of loss functions to ensure correct shapes for sharding operations. Custom loss wrappers are used:

**Data Parallel MSE Loss:**

`# Returns shape [1, 1] instead of scalar for correct shardingdef mse_loss(outputs, targets):    loss = (outputs - targets).pow(2)    loss = loss.mean(dim=1, keepdim=True)    loss = loss.mean(dim=0, keepdim=True)    return loss`
**Sources:**[blacksmith/experiments/torch/mnist/data_parallel/utils.py 7-14](https://github.com/tenstorrent/tt-blacksmith/blob/fe25eba0/blacksmith/experiments/torch/mnist/data_parallel/utils.py#L7-L14)

**Tensor Parallel Cross-Entropy Loss:**

`# Maintains shape for tensor parallel operationsdef cross_entropy_loss(outputs, targets):    log_probs = F.log_softmax(outputs, dim=1)    per_sample = -(log_probs * targets).sum(dim=1, keepdim=True)    return per_sample.mean(dim=0, keepdim=True)`
**Sources:**[blacksmith/experiments/torch/mnist/tensor_parallel/utils.py 8-17](https://github.com/tenstorrent/tt-blacksmith/blob/fe25eba0/blacksmith/experiments/torch/mnist/tensor_parallel/utils.py#L8-L17)

These workarounds address constraints in the current XLA implementation where scalar losses cause issues with sharded operations (see [tt-xla issue #1993](https://github.com/tenstorrent/tt-blacksmith/blob/fe25eba0/tt-xla%20issue%20#1993)).

## Next Steps

For detailed information on implementing specific parallelization strategies:

*   [Data Parallelism](https://deepwiki.com/tenstorrent/tt-blacksmith/8.1-data-parallelism) - Batch sharding and gradient averaging
*   [Tensor Parallelism](https://deepwiki.com/tenstorrent/tt-blacksmith/8.2-tensor-parallelism) - Model sharding patterns and communication strategies
*   [Hybrid Parallelism Strategies](https://deepwiki.com/tenstorrent/tt-blacksmith/8.3-hybrid-parallelism-strategies) - Combining both approaches for large-scale training

For related topics:

*   [Device Management and Hardware Abstraction](https://deepwiki.com/tenstorrent/tt-blacksmith/5.2-device-management-and-hardware-abstraction) - Lower-level device management details
*   [Training Loop Patterns](https://deepwiki.com/tenstorrent/tt-blacksmith/5.3-training-loop-patterns) - How distributed training integrates with training loops
*   [Configuration System](https://deepwiki.com/tenstorrent/tt-blacksmith/4-configuration-system) - Complete configuration schema reference

This wiki is featured in the [repository](https://github.com/tenstorrent/tt-blacksmith/blob/main/README.md)

Dismiss
Refresh this wiki

Enter email to refresh
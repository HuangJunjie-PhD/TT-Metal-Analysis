---
project: tt-blacksmith
github: https://github.com/tenstorrent/tt-blacksmith
deepwiki: https://deepwiki.com/tenstorrent/tt-blacksmith/5-training-framework
---

# Training Framework

Relevant source files
*   [blacksmith/experiments/torch/albert/test_albert_finetuning.py](https://github.com/tenstorrent/tt-blacksmith/blob/fe25eba0/blacksmith/experiments/torch/albert/test_albert_finetuning.py)
*   [blacksmith/experiments/torch/gemma/test_gemma_finetuning.py](https://github.com/tenstorrent/tt-blacksmith/blob/fe25eba0/blacksmith/experiments/torch/gemma/test_gemma_finetuning.py)
*   [blacksmith/experiments/torch/gemma11/test_gemma11_finetuning.py](https://github.com/tenstorrent/tt-blacksmith/blob/fe25eba0/blacksmith/experiments/torch/gemma11/test_gemma11_finetuning.py)
*   [blacksmith/experiments/torch/llama/configs.py](https://github.com/tenstorrent/tt-blacksmith/blob/fe25eba0/blacksmith/experiments/torch/llama/configs.py)
*   [blacksmith/experiments/torch/llama/xla/lora/README.md](https://github.com/tenstorrent/tt-blacksmith/blob/fe25eba0/blacksmith/experiments/torch/llama/xla/lora/README.md?plain=1)
*   [blacksmith/experiments/torch/llama/xla/test_llama_fine_tuning_pure_torch.py](https://github.com/tenstorrent/tt-blacksmith/blob/fe25eba0/blacksmith/experiments/torch/llama/xla/test_llama_fine_tuning_pure_torch.py)
*   [blacksmith/experiments/torch/phi/test_phi_finetuning.py](https://github.com/tenstorrent/tt-blacksmith/blob/fe25eba0/blacksmith/experiments/torch/phi/test_phi_finetuning.py)
*   [blacksmith/experiments/torch/qwen/test_qwen_finetuning.py](https://github.com/tenstorrent/tt-blacksmith/blob/fe25eba0/blacksmith/experiments/torch/qwen/test_qwen_finetuning.py)
*   [blacksmith/tools/templates/test_model_template.py](https://github.com/tenstorrent/tt-blacksmith/blob/fe25eba0/blacksmith/tools/templates/test_model_template.py)
*   [docs/src/experiments.md](https://github.com/tenstorrent/tt-blacksmith/blob/fe25eba0/docs/src/experiments.md?plain=1)

## Purpose and Scope

This page provides an overview of the core training framework in TT-Blacksmith. The framework consists of four manager classes that handle orthogonal concerns: reproducibility, logging, checkpointing, and device management. These managers abstract common training concerns and work consistently across different experiments, frameworks (PyTorch/JAX), and hardware targets (CPU/GPU/Tenstorrent).

The training framework follows a configuration-driven design where all components are initialized from a `TrainingConfig` Pydantic model that provides type safety, validation, and default values.

**Related Pages:**

*   [Manager Classes](https://deepwiki.com/tenstorrent/tt-blacksmith/5.1-manager-classes) - Detailed implementation of each manager class
*   [Device Management and Hardware Abstraction](https://deepwiki.com/tenstorrent/tt-blacksmith/5.2-device-management-and-hardware-abstraction) - DeviceManager architecture and parallelism strategies
*   [Training Loop Patterns](https://deepwiki.com/tenstorrent/tt-blacksmith/5.3-training-loop-patterns) - Standard training loop structure across frameworks
*   [Configuration System](https://deepwiki.com/tenstorrent/tt-blacksmith/4-configuration-system) - TrainingConfig models and YAML configuration

* * *

## Framework Architecture

The training framework is built around four manager classes that are instantiated from a unified configuration object and coordinate to handle all cross-cutting training concerns.

**Sources:**[blacksmith/experiments/torch/llama/xla/test_llama_fine_tuning_pure_torch.py 243-270](https://github.com/tenstorrent/tt-blacksmith/blob/fe25eba0/blacksmith/experiments/torch/llama/xla/test_llama_fine_tuning_pure_torch.py#L243-L270)[blacksmith/experiments/torch/qwen/test_qwen_finetuning.py 205-226](https://github.com/tenstorrent/tt-blacksmith/blob/fe25eba0/blacksmith/experiments/torch/qwen/test_qwen_finetuning.py#L205-L226)

* * *

## Core Components

The training infrastructure consists of four manager classes that handle orthogonal concerns:

| Manager Class | Responsibility | Configuration Source |
| --- | --- | --- |
| `ReproducibilityManager` | Sets random seeds, enables deterministic algorithms | `seed`, `deterministic` fields |
| `TrainingLogger` | Logs metrics, artifacts, integrates with WandB | `use_wandb`, `wandb_*`, `log_level` fields |
| `CheckpointManager` | Saves/loads checkpoints, implements retention policies | `checkpoint_*`, `save_*`, `keep_*` fields |
| `DeviceManager` | Abstracts CPU/GPU/TT devices, manages mesh and sharding | `use_tt`, `device`, `mesh_shape`, `mesh_axis_names` fields |

Each manager is instantiated from a `TrainingConfig` Pydantic model that provides type safety and validation. The managers operate independently but coordinate through the configuration object.

**Sources:**[blacksmith/experiments/torch/llama/xla/test_llama_fine_tuning_pure_torch.py 243-270](https://github.com/tenstorrent/tt-blacksmith/blob/fe25eba0/blacksmith/experiments/torch/llama/xla/test_llama_fine_tuning_pure_torch.py#L243-L270)[blacksmith/experiments/torch/qwen/test_qwen_finetuning.py 205-226](https://github.com/tenstorrent/tt-blacksmith/blob/fe25eba0/blacksmith/experiments/torch/qwen/test_qwen_finetuning.py#L205-L226)[blacksmith/experiments/torch/llama/configs.py 11-93](https://github.com/tenstorrent/tt-blacksmith/blob/fe25eba0/blacksmith/experiments/torch/llama/configs.py#L11-L93)

* * *

## Standard Initialization Pattern

All training scripts follow a consistent initialization pattern that instantiates the four manager classes in a specific order:

**Initialization Order:**

1.   **CLI Parsing** - `parse_cli_options()` extracts `--config` and `--test-config` paths
2.   **Config Generation** - `generate_config()` merges YAML files and validates into `TrainingConfig`
3.   **Reproducibility Setup** - `ReproducibilityManager(config).setup()` sets all random seeds
4.   **Logger Initialization** - `TrainingLogger(config)` enables metrics tracking
5.   **Checkpoint Manager** - `CheckpointManager(config, logger)` prepares checkpoint directories
6.   **Device Manager** - `DeviceManager(config)` configures hardware and mesh
7.   **Training Execution** - `train(config, device_manager, logger, checkpoint_manager)` runs the training loop

**Sources:**[blacksmith/experiments/torch/llama/xla/test_llama_fine_tuning_pure_torch.py 243-270](https://github.com/tenstorrent/tt-blacksmith/blob/fe25eba0/blacksmith/experiments/torch/llama/xla/test_llama_fine_tuning_pure_torch.py#L243-L270)[blacksmith/experiments/torch/gemma/test_gemma_finetuning.py 185-207](https://github.com/tenstorrent/tt-blacksmith/blob/fe25eba0/blacksmith/experiments/torch/gemma/test_gemma_finetuning.py#L185-L207)[blacksmith/experiments/torch/qwen/test_qwen_finetuning.py 205-226](https://github.com/tenstorrent/tt-blacksmith/blob/fe25eba0/blacksmith/experiments/torch/qwen/test_qwen_finetuning.py#L205-L226)[blacksmith/experiments/torch/gemma11/test_gemma11_finetuning.py 186-207](https://github.com/tenstorrent/tt-blacksmith/blob/fe25eba0/blacksmith/experiments/torch/gemma11/test_gemma11_finetuning.py#L186-L207)

* * *

## Training Loop Integration

The manager classes integrate seamlessly into the training loop, each handling specific concerns at appropriate points in the execution flow.

**Key Integration Points:**

1.   **Batch Preparation** - `device_manager.prepare_batch()` moves tensors to device and applies data parallel sharding
2.   **Model Sharding** - `device_manager.shard_model()` applies tensor parallel patterns defined in configuration
3.   **Optimizer Step** - `device_manager.optimizer_step()` handles gradient synchronization for distributed training
4.   **Metric Logging** - `logger.log_metrics()` tracks training and validation metrics at configured frequencies
5.   **Checkpoint Saving** - `checkpoint_manager` saves model state based on configured strategy (epoch/step)

See [Training Loop Patterns](https://deepwiki.com/tenstorrent/tt-blacksmith/5.3-training-loop-patterns) for framework-specific implementations.

**Sources:**[blacksmith/experiments/torch/llama/xla/test_llama_fine_tuning_pure_torch.py 155-228](https://github.com/tenstorrent/tt-blacksmith/blob/fe25eba0/blacksmith/experiments/torch/llama/xla/test_llama_fine_tuning_pure_torch.py#L155-L228)[blacksmith/experiments/torch/qwen/test_qwen_finetuning.py 137-189](https://github.com/tenstorrent/tt-blacksmith/blob/fe25eba0/blacksmith/experiments/torch/qwen/test_qwen_finetuning.py#L137-L189)[blacksmith/experiments/torch/gemma/test_gemma_finetuning.py 121-172](https://github.com/tenstorrent/tt-blacksmith/blob/fe25eba0/blacksmith/experiments/torch/gemma/test_gemma_finetuning.py#L121-L172)

* * *

## Manager Interaction Patterns

The four managers interact with each other and the training script through well-defined interfaces. The configuration object serves as the single source of truth, and dependencies between managers are explicitly passed as constructor arguments.

**Manager Dependencies:**

| Manager | Constructor Arguments | Runtime Dependencies |
| --- | --- | --- |
| `ReproducibilityManager` | `config: TrainingConfig` | None (sets global state) |
| `TrainingLogger` | `config: TrainingConfig, prefix: str` | None (standalone) |
| `CheckpointManager` | `config: TrainingConfig, logger: TrainingLogger` | Uses logger for artifact logging |
| `DeviceManager` | `config: TrainingConfig` | None (hardware abstraction) |

**Interaction Patterns:**

1.   **Configuration-Driven** - All managers read their settings from the same `TrainingConfig` object
2.   **Logger Sharing** - `CheckpointManager` receives logger reference to upload checkpoint artifacts
3.   **Device Abstraction** - Model and data loading use `device_manager.device` for hardware-agnostic code
4.   **Independent Operation** - Managers don't directly call each other; training script orchestrates all interactions

**Sources:**[blacksmith/experiments/torch/llama/xla/test_llama_fine_tuning_pure_torch.py 243-270](https://github.com/tenstorrent/tt-blacksmith/blob/fe25eba0/blacksmith/experiments/torch/llama/xla/test_llama_fine_tuning_pure_torch.py#L243-L270)[blacksmith/experiments/torch/qwen/test_qwen_finetuning.py 205-226](https://github.com/tenstorrent/tt-blacksmith/blob/fe25eba0/blacksmith/experiments/torch/qwen/test_qwen_finetuning.py#L205-L226)[blacksmith/tools/templates/test_model_template.py 145-170](https://github.com/tenstorrent/tt-blacksmith/blob/fe25eba0/blacksmith/tools/templates/test_model_template.py#L145-L170)

* * *

## Manager Responsibilities Summary

### ReproducibilityManager

**Purpose:** Ensures reproducible training runs by controlling all sources of randomness.

**Key Methods:**

*   `setup()` - Sets seeds for Python `random`, NumPy, PyTorch, and PyTorch XLA

**Configuration Fields:**

*   `seed: int` - Random seed value (default: 23)
*   `deterministic: bool` - Enable deterministic algorithms (default: False)

**Usage:** Instantiated once at script startup and `setup()` called before any model or data operations.

See [Manager Classes](https://deepwiki.com/tenstorrent/tt-blacksmith/5.1-manager-classes) for implementation details.

**Sources:**[blacksmith/experiments/torch/llama/xla/test_llama_fine_tuning_pure_torch.py 249-251](https://github.com/tenstorrent/tt-blacksmith/blob/fe25eba0/blacksmith/experiments/torch/llama/xla/test_llama_fine_tuning_pure_torch.py#L249-L251)[blacksmith/experiments/torch/llama/configs.py 60-62](https://github.com/tenstorrent/tt-blacksmith/blob/fe25eba0/blacksmith/experiments/torch/llama/configs.py#L60-L62)

* * *

### TrainingLogger

**Purpose:** Handles metrics logging, artifact tracking, and Weights & Biases integration.

**Key Methods:**

*   `log_metrics(metrics_dict, step, commit)` - Logs training/validation metrics
*   `log_artifact(path, artifact_type, name)` - Uploads files to WandB
*   `info()`, `error()` - Standard logging interface
*   `finish()` - Closes WandB run and performs cleanup

**Configuration Fields:**

*   `use_wandb: bool` - Enable WandB integration (default: True)
*   `wandb_project: str` - WandB project name
*   `wandb_run_name: str` - Run identifier
*   `log_level: str` - Logging verbosity (default: "INFO")

**Usage:** Created once, passed to other managers, called throughout training for metrics tracking.

See [Manager Classes](https://deepwiki.com/tenstorrent/tt-blacksmith/5.1-manager-classes) for detailed logging patterns.

**Sources:**[blacksmith/experiments/torch/llama/xla/test_llama_fine_tuning_pure_torch.py 254](https://github.com/tenstorrent/tt-blacksmith/blob/fe25eba0/blacksmith/experiments/torch/llama/xla/test_llama_fine_tuning_pure_torch.py#L254-L254)[blacksmith/experiments/torch/llama/configs.py 31-42](https://github.com/tenstorrent/tt-blacksmith/blob/fe25eba0/blacksmith/experiments/torch/llama/configs.py#L31-L42)

* * *

### CheckpointManager

**Purpose:** Manages model checkpoint lifecycle including saving, loading, and retention policies.

**Key Methods:**

*   `save_checkpoint(model, step, epoch, optimizer, checkpoint_name)` - Persists model state
*   `load_checkpoint(model, optimizer)` - Restores from checkpoint
*   `should_save_checkpoint(step, epoch)` - Evaluates save strategy

**Configuration Fields:**

*   `resume_from_checkpoint: bool` - Enable checkpoint resuming
*   `save_strategy: str` - "epoch" or "step" based saving
*   `keep_last_n: int` - Number of recent checkpoints to retain (default: 3)
*   `keep_best_n: int` - Number of best checkpoints to retain (default: 3)
*   `checkpoint_metric: str` - Metric for best checkpoint selection

**Usage:** Called in training loop at configured intervals and after training completion.

See [Manager Classes](https://deepwiki.com/tenstorrent/tt-blacksmith/5.1-manager-classes) for checkpoint strategies and storage backends.

**Sources:**[blacksmith/experiments/torch/llama/xla/test_llama_fine_tuning_pure_torch.py 257](https://github.com/tenstorrent/tt-blacksmith/blob/fe25eba0/blacksmith/experiments/torch/llama/xla/test_llama_fine_tuning_pure_torch.py#L257-L257)[blacksmith/experiments/torch/llama/configs.py 44-58](https://github.com/tenstorrent/tt-blacksmith/blob/fe25eba0/blacksmith/experiments/torch/llama/configs.py#L44-L58)

* * *

### DeviceManager

**Purpose:** Abstracts hardware-specific operations and manages distributed training strategies.

**Key Methods:**

*   `prepare_batch(batch)` - Moves data to device, applies data parallel sharding
*   `shard_model(model)` - Applies tensor parallel sharding patterns
*   `optimizer_step(optimizer)` - Synchronized gradient update
*   `is_data_parallel()` - Check if data parallelism is active
*   `is_tensor_parallel()` - Check if tensor parallelism is active

**Configuration Fields:**

*   `use_tt: bool` - Target Tenstorrent hardware vs CPU/GPU
*   `mesh_shape: List[int]` - Device mesh dimensions, e.g., `[2, 4]`
*   `mesh_axis_names: List[str]` - Axis semantic names: `["data", "model"]`
*   `model_sharding_patterns` - Regex patterns for layer-wise sharding

**Usage:** Used throughout training loop for batch preparation, model sharding, and optimizer synchronization.

See [Device Management and Hardware Abstraction](https://deepwiki.com/tenstorrent/tt-blacksmith/5.2-device-management-and-hardware-abstraction) for detailed parallelism strategies.

**Sources:**[blacksmith/experiments/torch/llama/xla/test_llama_fine_tuning_pure_torch.py 260-261](https://github.com/tenstorrent/tt-blacksmith/blob/fe25eba0/blacksmith/experiments/torch/llama/xla/test_llama_fine_tuning_pure_torch.py#L260-L261)[blacksmith/experiments/torch/llama/configs.py 75-83](https://github.com/tenstorrent/tt-blacksmith/blob/fe25eba0/blacksmith/experiments/torch/llama/configs.py#L75-L83)

* * *

## Device Manager Parallelism Detection

The `DeviceManager` determines parallelism strategy from mesh configuration:

**Configuration Examples:**

| Parallelism Mode | mesh_shape | mesh_axis_names | Example |
| --- | --- | --- | --- |
| Single Device | `null` | `null` | N150 single chip |
| Data Parallel | `[1, 2]` | `["model", "data"]` | 2 devices, batch split |
| Tensor Parallel | `[1, 2]` | `["data", "model"]` | 2 devices, model split |
| Hybrid | `[2, 4]` | `["data", "model"]` | 8 devices, both splits |

**Sources:**[blacksmith/tools/device_manager.py 71-77](https://github.com/tenstorrent/tt-blacksmith/blob/fe25eba0/blacksmith/tools/device_manager.py#L71-L77)[blacksmith/experiments/torch/mnist/data_parallel/test_mnist_training_dp.yaml 54-56](https://github.com/tenstorrent/tt-blacksmith/blob/fe25eba0/blacksmith/experiments/torch/mnist/data_parallel/test_mnist_training_dp.yaml#L54-L56)[blacksmith/experiments/torch/mnist/tensor_parallel/test_mnist_training_tp.yaml 54-62](https://github.com/tenstorrent/tt-blacksmith/blob/fe25eba0/blacksmith/experiments/torch/mnist/tensor_parallel/test_mnist_training_tp.yaml#L54-L62)

* * *

## Configuration-Driven Design

All infrastructure components are driven by the `TrainingConfig` Pydantic model, which provides:

**Type Safety** - All fields have explicit types and validation constraints (e.g., `batch_size: int = Field(gt=0)`)

**Default Values** - Sensible defaults for all parameters

**Validation** - Pydantic validates configuration at load time, catching errors early

**Documentation** - Field descriptions serve as inline documentation

**Example from MNIST Config:**

This configuration is parsed into a `TrainingConfig` object and passed to all managers, ensuring consistency across components.

**Sources:**[blacksmith/experiments/torch/mnist/configs.py 12-94](https://github.com/tenstorrent/tt-blacksmith/blob/fe25eba0/blacksmith/experiments/torch/mnist/configs.py#L12-L94)[blacksmith/experiments/torch/mnist/test_mnist_training.yaml 1-65](https://github.com/tenstorrent/tt-blacksmith/blob/fe25eba0/blacksmith/experiments/torch/mnist/test_mnist_training.yaml#L1-L65)

* * *

## Error Handling and Cleanup

All training scripts follow a standard error handling pattern:

**Pattern Implementation:**

1.   **Try Block** - Contains entire training loop
2.   **Exception Handler** - Catches all exceptions, logs full traceback via `logger.error()`
3.   **Finally Block** - Always calls `logger.finish()` to close WandB run and clean up resources
4.   **Re-raise** - After logging, exception is re-raised to propagate error

This ensures that even if training crashes, metrics are properly finalized and uploaded to WandB.

**Sources:**[blacksmith/experiments/torch/llama/xla/test_llama_fine_tuning_pure_torch.py 140-216](https://github.com/tenstorrent/tt-blacksmith/blob/fe25eba0/blacksmith/experiments/torch/llama/xla/test_llama_fine_tuning_pure_torch.py#L140-L216)[blacksmith/experiments/torch/qwen/test_qwen_finetuning.py 121-187](https://github.com/tenstorrent/tt-blacksmith/blob/fe25eba0/blacksmith/experiments/torch/qwen/test_qwen_finetuning.py#L121-L187)

* * *

## Template Script

A template training script is provided at [blacksmith/tools/templates/test_model_template.py](https://github.com/tenstorrent/tt-blacksmith/blob/fe25eba0/blacksmith/tools/templates/test_model_template.py) that demonstrates the standard pattern. New experiments should follow this structure:

**Template Structure:**

1.   Imports (lines 1-20)
2.   `validate()` function (lines 23-49)
3.   `train()` function (lines 52-133)
4.   Main block with manager initialization (lines 136-161)

The template includes placeholders for model loading, dataset preparation, and training loop while maintaining the standard infrastructure pattern.

**Sources:**[blacksmith/tools/templates/test_model_template.py 1-162](https://github.com/tenstorrent/tt-blacksmith/blob/fe25eba0/blacksmith/tools/templates/test_model_template.py#L1-L162)

* * *

## See Also

*   [Manager Classes](https://deepwiki.com/tenstorrent/tt-blacksmith/5-training-framework#3.5.1) - Detailed implementation of each manager class
*   [Logging and Monitoring](https://deepwiki.com/tenstorrent/tt-blacksmith/5-training-framework#3.5.2) - WandB integration and metric tracking
*   [Checkpoint Management](https://deepwiki.com/tenstorrent/tt-blacksmith/5-training-framework#3.5.3) - Checkpoint strategies and storage backends
*   [Device Management and Sharding](https://deepwiki.com/tenstorrent/tt-blacksmith/5-training-framework#3.5.4) - Device abstraction and parallelism implementation
*   [Training Loop Patterns](https://deepwiki.com/tenstorrent/tt-blacksmith/5-training-framework#3.5.5) - PyTorch and JAX training loop structures
*   [Configuration System](https://deepwiki.com/tenstorrent/tt-blacksmith/5-training-framework#3.3) - TrainingConfig Pydantic models and YAML files

Dismiss
Refresh this wiki

Enter email to refresh
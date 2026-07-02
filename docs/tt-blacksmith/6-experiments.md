---
project: tt-blacksmith
github: https://github.com/tenstorrent/tt-blacksmith
deepwiki: https://deepwiki.com/tenstorrent/tt-blacksmith/6-experiments
---

# Experiments

Relevant source files
*   [blacksmith/experiments/torch/llama/configs.py](https://github.com/tenstorrent/tt-blacksmith/blob/fe25eba0/blacksmith/experiments/torch/llama/configs.py)
*   [blacksmith/experiments/torch/llama/xla/lora/README.md](https://github.com/tenstorrent/tt-blacksmith/blob/fe25eba0/blacksmith/experiments/torch/llama/xla/lora/README.md?plain=1)
*   [blacksmith/experiments/torch/llama/xla/test_llama_fine_tuning_pure_torch.py](https://github.com/tenstorrent/tt-blacksmith/blob/fe25eba0/blacksmith/experiments/torch/llama/xla/test_llama_fine_tuning_pure_torch.py)
*   [docs/src/experiments.md](https://github.com/tenstorrent/tt-blacksmith/blob/fe25eba0/docs/src/experiments.md?plain=1)

## Purpose and Scope

This document provides an overview of the experiments available in TT-Blacksmith, their organizational structure, and general patterns for running them. Experiments are complete end-to-end training scripts that demonstrate how to train specific models on specific datasets using the training infrastructure.

For detailed information about specific experiment implementations, see:

*   PyTorch-based experiments: [PyTorch Experiments](https://deepwiki.com/tenstorrent/tt-blacksmith/6.1-pytorch-experiments)
*   JAX-based experiments: [JAX Experiments](https://deepwiki.com/tenstorrent/tt-blacksmith/6.2-jax-experiments)

For information about the underlying training infrastructure used by experiments, see [Training Framework](https://deepwiki.com/tenstorrent/tt-blacksmith/5-training-framework).

**Sources:**[docs/src/experiments.md 1-3](https://github.com/tenstorrent/tt-blacksmith/blob/fe25eba0/docs/src/experiments.md?plain=1#L1-L3)

* * *

## Experiment Architecture

Experiments are the top-level user-facing components that tie together the core training infrastructure, model implementations, and dataset loaders. Each experiment is a self-contained demonstration of training a specific model on a specific dataset.

**Diagram: Experiment Component Architecture**

Experiments orchestrate the training infrastructure managers and call factory functions to load models and datasets. The `DeviceManager` handles hardware abstraction, allowing the same experiment to run on different backends.

**Sources:**[blacksmith/experiments/torch/llama/xla/test_llama_fine_tuning_pure_torch.py 243-270](https://github.com/tenstorrent/tt-blacksmith/blob/fe25eba0/blacksmith/experiments/torch/llama/xla/test_llama_fine_tuning_pure_torch.py#L243-L270)[docs/src/experiments.md 45-58](https://github.com/tenstorrent/tt-blacksmith/blob/fe25eba0/docs/src/experiments.md?plain=1#L45-L58)

* * *

## Experiment Organization

Experiments are organized hierarchically by framework and then by model or task:

```
blacksmith/
├── experiments/
│   ├── torch/               # PyTorch experiments
│   │   ├── mnist/           # MNIST experiments
│   │   ├── llama/           # Llama fine-tuning experiments
│   │   ├── qwen/            # Qwen fine-tuning experiments
│   │   ├── gemma/           # Gemma experiments
│   │   └── ...
│   ├── jax/                 # JAX experiments
│   │   ├── mnist/           # JAX MNIST implementations
│   │   ├── nerf/            # NeRF experiments
│   │   └── ...
│   └── lightning/           # PyTorch Lightning experiments
├── models/                  # Model implementations/loaders
│   ├── torch/
│   └── jax/
└── datasets/                # Dataset loaders
    ├── torch/
    └── jax/
```

Each experiment directory typically contains:

*   **Config Definition** (`configs.py`): Pydantic model defining all configuration parameters
*   **YAML Files**: Specific configurations for different experiment variants (e.g., single chip, multi-chip)
*   **Training Scripts**: Python scripts that execute the training loop
*   **README.md**: Documentation for the experiment
*   **Subdirectories** (optional): Organization by hardware configuration or parallelization strategy

**Sources:**[docs/src/experiments.md 45-58](https://github.com/tenstorrent/tt-blacksmith/blob/fe25eba0/docs/src/experiments.md?plain=1#L45-L58)

* * *

## Standard Experiment Structure

Every experiment follows a standardized structure with three main components:

### 1. Configuration Definition

Each experiment defines a Pydantic configuration model that specifies all parameters:

`# Example: blacksmith/experiments/torch/llama/configs.pyclass TrainingConfig(BaseModel):    # Dataset settings    dataset_id: str = Field(default="sst2")        # Model settings    model_name: str = Field(default="meta-llama/Llama-3.2-1B")    max_length: int = Field(default=128, gt=0)    dtype: str = Field(default="torch.bfloat16")        # Training hyperparameters    learning_rate: float = Field(default=2e-5, gt=0)    batch_size: int = Field(default=32, gt=0)    num_epochs: int = Field(default=1, gt=0)        # LoRA settings    lora_r: int = Field(default=4, ge=0)    lora_alpha: int = Field(default=8, gt=0)        # Device settings    mesh_shape: Optional[list[int]] = Field(default=None)    use_tt: bool = Field(default=True)    # ... additional fields`
**Sources:**[blacksmith/experiments/torch/llama/configs.py 11-93](https://github.com/tenstorrent/tt-blacksmith/blob/fe25eba0/blacksmith/experiments/torch/llama/configs.py#L11-L93)

### 2. YAML Configuration Files

YAML files provide concrete parameter values for specific experiment runs:

`# Example: test_llama_3_2_1b_sst2.yamlmodel_name: "meta-llama/Llama-3.2-1B"dataset_id: "sst2"batch_size: 32learning_rate: 2e-5num_epochs: 3lora_r: 4lora_alpha: 8use_tt: true`
**Sources:**[blacksmith/experiments/torch/llama/xla/lora/README.md 119-123](https://github.com/tenstorrent/tt-blacksmith/blob/fe25eba0/blacksmith/experiments/torch/llama/xla/lora/README.md?plain=1#L119-L123)

### 3. Training Script

The training script ties everything together following a standard pattern:

**Diagram: Standard Experiment Execution Flow**

**Sources:**[blacksmith/experiments/torch/llama/xla/test_llama_fine_tuning_pure_torch.py 243-271](https://github.com/tenstorrent/tt-blacksmith/blob/fe25eba0/blacksmith/experiments/torch/llama/xla/test_llama_fine_tuning_pure_torch.py#L243-L271)[blacksmith/experiments/torch/llama/xla/test_llama_fine_tuning_pure_torch.py 106-241](https://github.com/tenstorrent/tt-blacksmith/blob/fe25eba0/blacksmith/experiments/torch/llama/xla/test_llama_fine_tuning_pure_torch.py#L106-L241)

* * *

## Typical Experiment Entry Point

A typical experiment script follows this pattern:

`if __name__ == "__main__":    # 1. Config setup    default_config = Path(__file__).parent / "lora" / "single_chip" / "test_llama_3_2_1b_sst2.yaml"    args = parse_cli_options(default_config=default_config)    config: TrainingConfig = generate_config(TrainingConfig, args.config, args.test_config)        # 2. Reproducibility setup    repro_manager = ReproducibilityManager(config)    repro_manager.setup()        # 3. Logger setup    logger = TrainingLogger(config, args.test_log_filename_prefix)        # 4. Checkpoint manager setup    checkpoint_manager = CheckpointManager(config, logger)        # 5. Device setup    device_manager = DeviceManager(config)    logger.info(f"Using device: {device_manager.device}")        # 6. Start training    train(config, device_manager, logger, checkpoint_manager)`
**Sources:**[blacksmith/experiments/torch/llama/xla/test_llama_fine_tuning_pure_torch.py 243-270](https://github.com/tenstorrent/tt-blacksmith/blob/fe25eba0/blacksmith/experiments/torch/llama/xla/test_llama_fine_tuning_pure_torch.py#L243-L270)

* * *

## Running an Experiment

### Basic Execution

Run an experiment by executing its training script with a config file:

`python3 blacksmith/experiments/torch/llama/xla/test_llama_fine_tuning_pure_torch.py \    --config blacksmith/experiments/torch/llama/xla/lora/single_chip/test_llama_3_2_1b_sst2.yaml`
### Overriding Configuration

Override specific parameters using a test config file:

`python3 blacksmith/experiments/torch/llama/xla/test_llama_fine_tuning_pure_torch.py \    --config blacksmith/experiments/torch/llama/xla/lora/single_chip/test_llama_3_2_1b_sst2.yaml \    --test-config path/to/test_overrides.yaml`
### Running on Different Hardware

Switch between hardware backends by modifying the `use_tt` flag in the YAML configuration:

`# Run on Tenstorrent hardwareuse_tt: true # Run on GPU/CPUuse_tt: false`
For multi-chip distributed training, specify mesh configuration:

`mesh_shape: [8, 4]  # 8 data parallel, 4 model parallelmesh_axis_names: ["data", "model"]model_sharding_patterns:  - ['\.self_attn\.q_proj\.base_layer$', ["model", null]]  - ['\.self_attn\.o_proj$', [null, "model"]]  # ... additional patterns`
**Sources:**[blacksmith/experiments/torch/llama/xla/lora/README.md 42-67](https://github.com/tenstorrent/tt-blacksmith/blob/fe25eba0/blacksmith/experiments/torch/llama/xla/lora/README.md?plain=1#L42-L67)

* * *

## Available Experiments

The following table summarizes all experiments available in TT-Blacksmith:

| Framework | Model | Dataset | Method | Devices | Section |
| --- | --- | --- | --- | --- | --- |
| PyTorch | MLP | MNIST | Full-model | N150 | [6.1.1](https://deepwiki.com/tenstorrent/tt-blacksmith/6.1.1-mnist-training-examples) |
| PyTorch | CNN | MNIST | Full-model | N150 | [6.1.1](https://deepwiki.com/tenstorrent/tt-blacksmith/6.1.1-mnist-training-examples) |
| PyTorch | MLP | MNIST | Data parallel | N300 | [6.1.1](https://deepwiki.com/tenstorrent/tt-blacksmith/6.1.1-mnist-training-examples) |
| PyTorch | MLP | MNIST | Tensor parallel | N300 | [6.1.1](https://deepwiki.com/tenstorrent/tt-blacksmith/6.1.1-mnist-training-examples) |
| PyTorch | Llama 3.2 1B | SST-2 | LoRA | N150 | [6.1.2](https://deepwiki.com/tenstorrent/tt-blacksmith/6.1.2-llm-fine-tuning-with-lora) |
| PyTorch | Llama 3.2 1B | SST-2 | LoRA, DP+TP | T3K, Galaxy | [6.1.2](https://deepwiki.com/tenstorrent/tt-blacksmith/6.1.2-llm-fine-tuning-with-lora) |
| PyTorch | Llama 3.2 1B | SST-2 | Adapters | N150 | [6.1.2](https://deepwiki.com/tenstorrent/tt-blacksmith/6.1.2-llm-fine-tuning-with-lora) |
| PyTorch | Llama 3.2 3B | SST-2 | LoRA, TP | QuietBox | [6.1.2](https://deepwiki.com/tenstorrent/tt-blacksmith/6.1.2-llm-fine-tuning-with-lora) |
| PyTorch | Llama 3.1 8B | SST-2 | LoRA, DP+TP | T3K, Galaxy | [6.1.2](https://deepwiki.com/tenstorrent/tt-blacksmith/6.1.2-llm-fine-tuning-with-lora) |
| PyTorch | Qwen 2.5 0.5B | Text-to-SQL | LoRA | N150 | [6.1.2](https://deepwiki.com/tenstorrent/tt-blacksmith/6.1.2-llm-fine-tuning-with-lora) |
| PyTorch | Qwen 2.5 1.5B | Text-to-SQL | LoRA | N150 | [6.1.2](https://deepwiki.com/tenstorrent/tt-blacksmith/6.1.2-llm-fine-tuning-with-lora) |
| PyTorch | Qwen 3 4B | SST-2 | LoRA | N150, QuietBox | [6.1.2](https://deepwiki.com/tenstorrent/tt-blacksmith/6.1.2-llm-fine-tuning-with-lora) |
| PyTorch | Gemma 3 1B | SST-2 | LoRA | N150 | [6.1.2](https://deepwiki.com/tenstorrent/tt-blacksmith/6.1.2-llm-fine-tuning-with-lora) |
| PyTorch | Gemma 1.1 2B | SST-2, Squad-V2 | LoRA | N150 | [6.1.2](https://deepwiki.com/tenstorrent/tt-blacksmith/6.1.2-llm-fine-tuning-with-lora) |
| PyTorch | ALBERT | Banking77 | Adapters | N150 | [6.1.3](https://deepwiki.com/tenstorrent/tt-blacksmith/6.1.3-falcon3-and-other-models) |
| PyTorch | Phi-1, Phi-1.5 | SST-2, Squad-V2 | LoRA | N150 | [6.1.3](https://deepwiki.com/tenstorrent/tt-blacksmith/6.1.3-falcon3-and-other-models) |
| JAX | MLP | MNIST | Full-model | N150 | [6.2.1](https://deepwiki.com/tenstorrent/tt-blacksmith/6.2.1-jax-mnist-implementations) |
| JAX | MLP | MNIST | Data/Tensor parallel | N300 | [6.2.1](https://deepwiki.com/tenstorrent/tt-blacksmith/6.2.1-jax-mnist-implementations) |
| JAX | NeRF | Blender | Full-model | N150 | [6.2.2](https://deepwiki.com/tenstorrent/tt-blacksmith/6.2.2-jax-nerf-and-advanced-examples) |
| JAX | Llama 3.2 1B | SST-2 | LoRA, DoRA | N150 | [6.2](https://deepwiki.com/tenstorrent/tt-blacksmith/6.2-jax-experiments) |
| JAX | DistilBERT | SST-2 | Distillation | N150, N300 | [6.2](https://deepwiki.com/tenstorrent/tt-blacksmith/6.2-jax-experiments) |
| Lightning | NeRF | Blender | Full-model | N150 | [6.2.2](https://deepwiki.com/tenstorrent/tt-blacksmith/6.2.2-jax-nerf-and-advanced-examples) |

**Sources:**[docs/src/experiments.md 9-42](https://github.com/tenstorrent/tt-blacksmith/blob/fe25eba0/docs/src/experiments.md?plain=1#L9-L42)

* * *

## Experiment Components and Dependencies

**Diagram: Experiment File Structure and Dependencies**

**Sources:**[docs/src/experiments.md 45-58](https://github.com/tenstorrent/tt-blacksmith/blob/fe25eba0/docs/src/experiments.md?plain=1#L45-L58)[blacksmith/experiments/torch/llama/xla/test_llama_fine_tuning_pure_torch.py 1-26](https://github.com/tenstorrent/tt-blacksmith/blob/fe25eba0/blacksmith/experiments/torch/llama/xla/test_llama_fine_tuning_pure_torch.py#L1-L26)

* * *

## Training and Validation Pattern

Experiments implement training and validation functions that utilize the infrastructure managers:

**Diagram: Training and Validation Function Flow**

**Sources:**[blacksmith/experiments/torch/llama/xla/test_llama_fine_tuning_pure_torch.py 106-241](https://github.com/tenstorrent/tt-blacksmith/blob/fe25eba0/blacksmith/experiments/torch/llama/xla/test_llama_fine_tuning_pure_torch.py#L106-L241)[blacksmith/experiments/torch/llama/xla/test_llama_fine_tuning_pure_torch.py 28-88](https://github.com/tenstorrent/tt-blacksmith/blob/fe25eba0/blacksmith/experiments/torch/llama/xla/test_llama_fine_tuning_pure_torch.py#L28-L88)

* * *

## Configuration-Driven Experiments

Experiments are fully configuration-driven, meaning all behavior is controlled through the `TrainingConfig` object:

| Configuration Category | Examples | Usage |
| --- | --- | --- |
| **Model Settings** | `model_name`, `dtype`, `max_length` | Control which model to load and its precision |
| **Training Hyperparameters** | `learning_rate`, `batch_size`, `num_epochs`, `gradient_accumulation_steps` | Define training behavior |
| **LoRA/Adapter Settings** | `lora_r`, `lora_alpha`, `adapter_bottleneck_dim` | Configure parameter-efficient fine-tuning |
| **Device Settings** | `use_tt`, `mesh_shape`, `mesh_axis_names`, `model_sharding_patterns` | Control hardware and parallelization |
| **Logging Settings** | `use_wandb`, `wandb_project`, `steps_freq`, `val_steps_freq` | Configure logging behavior |
| **Checkpoint Settings** | `save_strategy`, `keep_last_n`, `keep_best_n` | Control checkpoint saving |
| **Reproducibility Settings** | `seed`, `deterministic` | Ensure reproducible results |

**Sources:**[blacksmith/experiments/torch/llama/configs.py 11-93](https://github.com/tenstorrent/tt-blacksmith/blob/fe25eba0/blacksmith/experiments/torch/llama/configs.py#L11-L93)[blacksmith/experiments/torch/llama/xla/lora/README.md 124-169](https://github.com/tenstorrent/tt-blacksmith/blob/fe25eba0/blacksmith/experiments/torch/llama/xla/lora/README.md?plain=1#L124-L169)

* * *

## Experiment Lifecycle

**Diagram: Experiment Execution Sequence**

**Sources:**[blacksmith/experiments/torch/llama/xla/test_llama_fine_tuning_pure_torch.py 243-270](https://github.com/tenstorrent/tt-blacksmith/blob/fe25eba0/blacksmith/experiments/torch/llama/xla/test_llama_fine_tuning_pure_torch.py#L243-L270)

* * *

## Framework-Specific Considerations

### PyTorch Experiments

PyTorch experiments use:

*   **torch_xla** for Tenstorrent hardware support via XLA compilation
*   **Hugging Face Transformers** for model loading
*   **PEFT library** for LoRA/adapter implementations
*   Standard PyTorch optimizers and data loaders

Key PyTorch-specific operations:

*   [blacksmith/experiments/torch/llama/xla/test_llama_fine_tuning_pure_torch.py 182](https://github.com/tenstorrent/tt-blacksmith/blob/fe25eba0/blacksmith/experiments/torch/llama/xla/test_llama_fine_tuning_pure_torch.py#L182-L182): `torch_xla.sync(wait=True)` for synchronization
*   [blacksmith/experiments/torch/llama/xla/test_llama_fine_tuning_pure_torch.py 219](https://github.com/tenstorrent/tt-blacksmith/blob/fe25eba0/blacksmith/experiments/torch/llama/xla/test_llama_fine_tuning_pure_torch.py#L219-L219): `xr.clear_computation_cache()` for memory management

**Sources:**[blacksmith/experiments/torch/llama/xla/test_llama_fine_tuning_pure_torch.py 1-271](https://github.com/tenstorrent/tt-blacksmith/blob/fe25eba0/blacksmith/experiments/torch/llama/xla/test_llama_fine_tuning_pure_torch.py#L1-L271)

### JAX Experiments

JAX experiments use:

*   **jax.numpy** for array operations
*   **Flax** or **NNX** for neural network abstractions
*   JAX's functional programming paradigm with pure functions
*   Different sharding APIs compared to PyTorch

See [JAX Experiments](https://deepwiki.com/tenstorrent/tt-blacksmith/6.2-jax-experiments) for detailed information.

**Sources:**[docs/src/experiments.md 34-41](https://github.com/tenstorrent/tt-blacksmith/blob/fe25eba0/docs/src/experiments.md?plain=1#L34-L41)

* * *

## Next Steps

For detailed information about specific experiment types:

*   **PyTorch Experiments**: See [PyTorch Experiments](https://deepwiki.com/tenstorrent/tt-blacksmith/6.1-pytorch-experiments) for MNIST, LLM fine-tuning, and other PyTorch-based experiments
*   **JAX Experiments**: See [JAX Experiments](https://deepwiki.com/tenstorrent/tt-blacksmith/6.2-jax-experiments) for JAX-based MNIST, NeRF, and other JAX implementations
*   **Model Loading**: See [Model Loading and Adaptation](https://deepwiki.com/tenstorrent/tt-blacksmith/7.1-model-loading-and-adaptation) for details on `get_model()` and model adaptation
*   **Dataset Loading**: See [Dataset Implementations](https://deepwiki.com/tenstorrent/tt-blacksmith/7.2-dataset-implementations) for details on `get_dataset()` and dataset patterns
*   **Distributed Training**: See [Distributed Training](https://deepwiki.com/tenstorrent/tt-blacksmith/8-distributed-training) for information on parallelization strategies

This wiki is featured in the [repository](https://github.com/tenstorrent/tt-blacksmith/blob/main/README.md)

Dismiss
Refresh this wiki

Enter email to refresh
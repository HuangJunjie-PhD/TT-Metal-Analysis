---
project: tt-blacksmith
github: https://github.com/tenstorrent/tt-blacksmith
deepwiki: https://deepwiki.com/tenstorrent/tt-blacksmith/4-configuration-system
---

# Configuration System

Relevant source files
*   [blacksmith/experiments/torch/llama/configs.py](https://github.com/tenstorrent/tt-blacksmith/blob/fe25eba0/blacksmith/experiments/torch/llama/configs.py)
*   [blacksmith/experiments/torch/llama/xla/lora/README.md](https://github.com/tenstorrent/tt-blacksmith/blob/fe25eba0/blacksmith/experiments/torch/llama/xla/lora/README.md?plain=1)
*   [blacksmith/experiments/torch/llama/xla/test_llama_fine_tuning_pure_torch.py](https://github.com/tenstorrent/tt-blacksmith/blob/fe25eba0/blacksmith/experiments/torch/llama/xla/test_llama_fine_tuning_pure_torch.py)
*   [blacksmith/experiments/torch/mnist/README.md](https://github.com/tenstorrent/tt-blacksmith/blob/fe25eba0/blacksmith/experiments/torch/mnist/README.md?plain=1)
*   [blacksmith/experiments/torch/mnist/configs.py](https://github.com/tenstorrent/tt-blacksmith/blob/fe25eba0/blacksmith/experiments/torch/mnist/configs.py)
*   [blacksmith/experiments/torch/mnist/data_parallel/test_mnist_training_dp.yaml](https://github.com/tenstorrent/tt-blacksmith/blob/fe25eba0/blacksmith/experiments/torch/mnist/data_parallel/test_mnist_training_dp.yaml)
*   [blacksmith/experiments/torch/mnist/data_parallel/utils.py](https://github.com/tenstorrent/tt-blacksmith/blob/fe25eba0/blacksmith/experiments/torch/mnist/data_parallel/utils.py)
*   [blacksmith/experiments/torch/mnist/tensor_parallel/test_mnist_training_tp.yaml](https://github.com/tenstorrent/tt-blacksmith/blob/fe25eba0/blacksmith/experiments/torch/mnist/tensor_parallel/test_mnist_training_tp.yaml)
*   [blacksmith/experiments/torch/mnist/tensor_parallel/utils.py](https://github.com/tenstorrent/tt-blacksmith/blob/fe25eba0/blacksmith/experiments/torch/mnist/tensor_parallel/utils.py)
*   [blacksmith/experiments/torch/mnist/test_mnist_training.yaml](https://github.com/tenstorrent/tt-blacksmith/blob/fe25eba0/blacksmith/experiments/torch/mnist/test_mnist_training.yaml)
*   [blacksmith/tools/device_manager.py](https://github.com/tenstorrent/tt-blacksmith/blob/fe25eba0/blacksmith/tools/device_manager.py)
*   [docs/src/experiments.md](https://github.com/tenstorrent/tt-blacksmith/blob/fe25eba0/docs/src/experiments.md?plain=1)

This page documents the configuration architecture in TT-Blacksmith. All experiments use Pydantic `BaseModel` classes named `TrainingConfig` to define, validate, and distribute parameters. YAML files provide concrete values, and two CLI utilities—`parse_cli_options` and `generate_config`—handle loading and validation.

The configuration system is the central integration point: every manager class (`DeviceManager`, `CheckpointManager`, `TrainingLogger`, `ReproducibilityManager`) and the main training loop receive the same validated config object.

Related pages:

*   For manager class consumption patterns, see page 5.1 (Manager Classes)
*   For complete field reference, see page 10.1 (Configuration Schema Reference)
*   For device/mesh configuration details, see page 5.2 (Device Management and Hardware Abstraction)

* * *

## Overview

TT-Blacksmith enforces a strict configuration pattern across all experiments:

1.   **Declaration**: A `TrainingConfig` class (subclass of `pydantic.BaseModel`) declares all parameters with types, defaults, and validation rules (`Field(gt=0)`, etc.)
2.   **Storage**: YAML files on disk provide values for specific hardware configurations or test scenarios
3.   **Loading**: At experiment startup, `parse_cli_options` parses command-line arguments (`--config`, `--test-config`) and `generate_config` loads, merges, and validates YAML into a `TrainingConfig` instance
4.   **Distribution**: The validated config object is passed once to each manager and the training function; no component modifies it

Each experiment defines its own `TrainingConfig` class (e.g., [blacksmith/experiments/torch/llama/configs.py 11-93](https://github.com/tenstorrent/tt-blacksmith/blob/fe25eba0/blacksmith/experiments/torch/llama/configs.py#L11-L93)[blacksmith/experiments/torch/mnist/configs.py 12-93](https://github.com/tenstorrent/tt-blacksmith/blob/fe25eba0/blacksmith/experiments/torch/mnist/configs.py#L12-L93)) that duplicates and extends a canonical template at [blacksmith/tools/templates/configs.py](https://github.com/tenstorrent/tt-blacksmith/blob/fe25eba0/blacksmith/tools/templates/configs.py)

Sources: [blacksmith/tools/cli.py 12-43](https://github.com/tenstorrent/tt-blacksmith/blob/fe25eba0/blacksmith/tools/cli.py#L12-L43)[blacksmith/tools/templates/configs.py 1-60](https://github.com/tenstorrent/tt-blacksmith/blob/fe25eba0/blacksmith/tools/templates/configs.py#L1-L60)[blacksmith/experiments/torch/llama/configs.py 1-93](https://github.com/tenstorrent/tt-blacksmith/blob/fe25eba0/blacksmith/experiments/torch/llama/configs.py#L1-L93)

* * *

## Config Loading Flow

**Configuration instantiation and distribution at experiment startup**

This diagram shows the exact function call sequence from [blacksmith/experiments/torch/llama/xla/test_llama_fine_tuning_pure_torch.py 243-270](https://github.com/tenstorrent/tt-blacksmith/blob/fe25eba0/blacksmith/experiments/torch/llama/xla/test_llama_fine_tuning_pure_torch.py#L243-L270) The pattern is identical across all experiments (PyTorch MNIST, JAX MNIST, Llama, Qwen, etc.), differing only in which `TrainingConfig` class is imported.

Sources: [blacksmith/tools/cli.py 12-43](https://github.com/tenstorrent/tt-blacksmith/blob/fe25eba0/blacksmith/tools/cli.py#L12-L43)[blacksmith/experiments/torch/llama/xla/test_llama_fine_tuning_pure_torch.py 243-270](https://github.com/tenstorrent/tt-blacksmith/blob/fe25eba0/blacksmith/experiments/torch/llama/xla/test_llama_fine_tuning_pure_torch.py#L243-L270)[blacksmith/tools/templates/configs.py 1-60](https://github.com/tenstorrent/tt-blacksmith/blob/fe25eba0/blacksmith/tools/templates/configs.py#L1-L60)

* * *

## Key Code Entities

| Code Entity | File | Role |
| --- | --- | --- |
| `TrainingConfig` (template) | `blacksmith/tools/templates/configs.py` | Base field set; used as a type hint for shared utilities |
| `TrainingConfig` (per-experiment) | `blacksmith/experiments/torch/*/configs.py` | Extends base with model-specific fields |
| `parse_cli_options` | `blacksmith/tools/cli.py` | Parses `--config`, `--test-config`, `--test-log-filename-prefix` |
| `generate_config` | `blacksmith/tools/cli.py` | Loads YAML(s), merges, validates via `model_validate` |
| `TestConfig` | `blacksmith/tools/test_config` | Optional sub-object embedded in `TrainingConfig` for CI overrides |

* * *

## `TrainingConfig`: The Pydantic BaseModel

Every experiment defines a class named `TrainingConfig` that inherits from `pydantic.BaseModel`. Fields are declared using `pydantic.Field` with `default` values and optional validators (`gt=0` for "greater than zero", `ge=0` for "greater than or equal to zero", etc.).

**TrainingConfig class structure across experiments**

**Key architectural note:** Each experiment's `TrainingConfig` is an independent `BaseModel` subclass. They do **not** inherit from the template class. Instead, they duplicate the common field pattern and add experiment-specific fields. The template at [blacksmith/tools/templates/configs.py 1-60](https://github.com/tenstorrent/tt-blacksmith/blob/fe25eba0/blacksmith/tools/templates/configs.py#L1-L60) serves two purposes:

1.   Reference implementation for authors creating new experiments
2.   Type hint used in shared utilities (`get_dataset`, `get_model`, manager classes) to indicate expected minimum field set

This design avoids circular imports between the utilities package and experiment modules, and allows each experiment to customize defaults (e.g., Llama uses `batch_size=32`, MNIST uses `batch_size=256`).

Sources: [blacksmith/tools/templates/configs.py 1-60](https://github.com/tenstorrent/tt-blacksmith/blob/fe25eba0/blacksmith/tools/templates/configs.py#L1-L60)[blacksmith/experiments/torch/llama/configs.py 11-93](https://github.com/tenstorrent/tt-blacksmith/blob/fe25eba0/blacksmith/experiments/torch/llama/configs.py#L11-L93)[blacksmith/experiments/torch/mnist/configs.py 12-93](https://github.com/tenstorrent/tt-blacksmith/blob/fe25eba0/blacksmith/experiments/torch/mnist/configs.py#L12-L93)

* * *

## CLI Loading Functions

Both functions live in [blacksmith/tools/cli.py](https://github.com/tenstorrent/tt-blacksmith/blob/fe25eba0/blacksmith/tools/cli.py) and are called in sequence by every experiment's `__main__` block.

### `parse_cli_options`

[blacksmith/tools/cli.py 26-43](https://github.com/tenstorrent/tt-blacksmith/blob/fe25eba0/blacksmith/tools/cli.py#L26-L43)

`def parse_cli_options(default_config: Path) -> argparse.Namespace`
Returns an `argparse.Namespace` with three attributes:

| Attribute | CLI Flag | Required | Purpose |
| --- | --- | --- | --- |
| `args.config` | `--config PATH` | No (defaults to `default_config` parameter) | Main experiment YAML file |
| `args.test_config` | `--test-config PATH` | No | CI-only: overrides for testing (e.g., `num_epochs: 1`) |
| `args.test_log_filename_prefix` | `--test-log-filename-prefix STR` | No | CI-only: golden log filename for pytest validation |

**Usage pattern in experiments:**

`default_config = Path(__file__).parent / "lora" / "single_chip" / "test_llama_3_2_1b_sst2.yaml"args = parse_cli_options(default_config=default_config)`
The `--test-config` mechanism enables the pytest framework to inject test-specific parameter overrides (see page 9.1, Test Framework) without modifying the main experiment YAML.

### `generate_config`

[blacksmith/tools/cli.py 12-23](https://github.com/tenstorrent/tt-blacksmith/blob/fe25eba0/blacksmith/tools/cli.py#L12-L23)

`def generate_config(config: BaseModel, yaml_path: Path, test_yaml_path: Optional[Path] = None) -> BaseModel`
Loads and validates configuration in three steps:

1.   **Load main config:**`config_data = yaml.safe_load(yaml_path)`
2.   **Optional merge:** If `test_yaml_path` is provided, `config_data |= yaml.safe_load(test_yaml_path)`. Dictionary merge overwrites main config keys with test config values and adds new keys (e.g., `test_config` subfield).
3.   **Validate:**`config.model_validate(config_data)` instantiates the `TrainingConfig` object, applying Pydantic validators and default values.

**Validation behavior:**

*   YAML keys not declared in `TrainingConfig`: Pydantic raises `ValidationError`
*   Fields declared but missing from YAML: Use `Field(default=...)` value
*   Fields with validators (e.g., `Field(gt=0)`): Pydantic checks constraint, raises error if violated

**Usage pattern in experiments:**

`config: TrainingConfig = generate_config(TrainingConfig, args.config, args.test_config)`
Sources: [blacksmith/tools/cli.py 12-43](https://github.com/tenstorrent/tt-blacksmith/blob/fe25eba0/blacksmith/tools/cli.py#L12-L43)[blacksmith/experiments/torch/llama/xla/test_llama_fine_tuning_pure_torch.py 245-247](https://github.com/tenstorrent/tt-blacksmith/blob/fe25eba0/blacksmith/experiments/torch/llama/xla/test_llama_fine_tuning_pure_torch.py#L245-L247)

* * *

## Logical Sections of a `TrainingConfig`

Every per-experiment `TrainingConfig` contains the following groups of fields, mirrored identically in the YAML files by comment header:

### Dataset Settings

| Field | Type | Description |
| --- | --- | --- |
| `dataset_id` | `str` | Key passed to `get_dataset()` factory (e.g. `"sst2"`, `"text2sql"`, `"mnist"`) |
| `train_ratio` | `float` | Train/validation split ratio (MNIST only) |
| `max_length` | `int` | Token truncation length for text models |
| `dtype` | `str` | Tensor dtype string, e.g. `"torch.bfloat16"` (evaluated at runtime) |

### Model Settings

| Field | Type | Description |
| --- | --- | --- |
| `model_name` | `str` | HuggingFace model ID or local path |
| `training_type` | `str` | `"lora"` or `"adapters"` |

### Training Hyperparameters

| Field | Type | Description |
| --- | --- | --- |
| `learning_rate` | `float` | Optimizer learning rate |
| `batch_size` | `int` | Per-device batch size |
| `gradient_accumulation_steps` | `int` | Steps before optimizer update |
| `gradient_checkpointing` | `bool` | Enable activation checkpointing |
| `num_epochs` | `int` | Training epochs |
| `optim` | `str` | Optimizer identifier (e.g. `"adamw_torch"`, `"sgd"`) |
| `ignored_index` | `int` | Label index to ignore in cross-entropy loss (default `-100`) |

### Logging Settings

| Field | Type | Description |
| --- | --- | --- |
| `log_level` | `str` | Python logging level |
| `use_wandb` | `bool` | Enable Weights & Biases |
| `wandb_project` | `str` | W&B project name |
| `wandb_run_name` | `str` | W&B run name |
| `wandb_tags` | `list[str]` | W&B run tags |
| `wandb_watch_mode` | `str` | W&B model watch mode |
| `wandb_log_freq` | `int` | W&B gradient/parameter log frequency (steps) |
| `model_to_wandb` | `bool` | Upload model artifact to W&B |
| `steps_freq` | `int` | Console/metric log frequency (steps) |
| `val_steps_freq` | `int` | Validation log frequency (steps) |
| `epoch_freq` | `int` | Log frequency (epochs) |

### Checkpoint Settings

| Field | Type | Description |
| --- | --- | --- |
| `resume_from_checkpoint` | `bool` | Resume training from a saved checkpoint |
| `resume_option` | `str` | `"last"`, `"best"`, or `"path"` |
| `checkpoint_path` | `str` | Explicit checkpoint path when `resume_option="path"` |
| `checkpoint_metric` | `str` | Metric key for best-checkpoint selection (e.g. `"eval/loss"`) |
| `checkpoint_metric_mode` | `str` | `"min"` or `"max"` |
| `keep_last_n` | `int` | Number of most-recent checkpoints to retain |
| `keep_best_n` | `int` | Number of best checkpoints to retain |
| `save_strategy` | `str` | `"epoch"` or `"step"` |
| `project_dir` | `str` | Directory for checkpoint output |
| `save_optim` | `bool` | Include optimizer state in checkpoint |
| `storage_backend` | `str` | `"local"` or remote backend identifier |
| `sync_to_storage` | `bool` | Sync checkpoints to remote storage |
| `load_from_storage` | `bool` | Load checkpoint from remote storage |
| `remote_path` | `str` | Remote storage path |

### Reproducibility Settings

| Field | Type | Description |
| --- | --- | --- |
| `seed` | `int` | Global RNG seed (default `23`) |
| `deterministic` | `bool` | Enable deterministic ops |

### Device / Mesh Settings

| Field | Type | Description |
| --- | --- | --- |
| `mesh_shape` | `Optional[list[int]]` | `None` for single-chip; `[x, y]` for 2-D mesh |
| `mesh_axis_names` | `Optional[list[str]]` | Axis names, e.g. `["data", "model"]` |
| `model_sharding_patterns` | `Optional[List[Tuple[str, Tuple[Optional[str], ...]]]]` | Regex-to-sharding-spec mapping for tensor parallelism |

When `mesh_shape` is `None`, `DeviceManager` operates in single-chip mode. When set, the `DeviceManager` calls `xr.use_spmd()` and constructs an `xs.Mesh`. See [Device Management and Sharding](https://deepwiki.com/tenstorrent/tt-blacksmith/4-configuration-system#3.5.4) for how these fields are consumed.

### LoRA Settings (LLM experiments)

| Field | Type | Description |
| --- | --- | --- |
| `lora_r` | `int` | LoRA rank |
| `lora_alpha` | `int` | LoRA scaling factor |
| `lora_target_modules` | `list[str]` | Module names to apply LoRA to |
| `lora_task_type` | `str` | PEFT task type (e.g. `"CAUSAL_LM"`) |

### Adapter Settings (Llama experiment)

| Field | Type | Description |
| --- | --- | --- |
| `adapter_bottleneck_dim` | `int` | Hidden dimension of residual adapter |
| `adapter_non_linearity` | `str` | Activation class string (evaluated at runtime) |
| `adapter_layers` | `list[int]` | Which transformer blocks to insert adapters into |

### Other / Miscellaneous

| Field | Type | Description |
| --- | --- | --- |
| `framework` | `str` | `"pytorch"` or `"jax"` |
| `use_tt` | `bool` | Target TT device; if `False`, falls back to GPU/CPU |
| `output_dir` | `str` | Experiment results directory |
| `test_config` | `Optional[TestConfig]` | CI-only override block injected via `--test-config` |

Sources: [blacksmith/tools/templates/configs.py 1-60](https://github.com/tenstorrent/tt-blacksmith/blob/fe25eba0/blacksmith/tools/templates/configs.py#L1-L60)[blacksmith/experiments/torch/llama/configs.py 11-93](https://github.com/tenstorrent/tt-blacksmith/blob/fe25eba0/blacksmith/experiments/torch/llama/configs.py#L11-L93)[blacksmith/experiments/torch/mnist/configs.py 12-93](https://github.com/tenstorrent/tt-blacksmith/blob/fe25eba0/blacksmith/experiments/torch/mnist/configs.py#L12-L93)[blacksmith/experiments/torch/qwen/configs.py 11-82](https://github.com/tenstorrent/tt-blacksmith/blob/fe25eba0/blacksmith/experiments/torch/qwen/configs.py#L11-L82)

* * *

## YAML Configuration Files

Each hardware configuration has its own YAML file. Fields omitted from the YAML fall back to the Pydantic `Field(default=...)` values. The YAML mirrors the section comments in the Python class.

**Anatomy of a single-chip YAML** ([blacksmith/experiments/torch/mnist/test_mnist_training.yaml](https://github.com/tenstorrent/tt-blacksmith/blob/fe25eba0/blacksmith/experiments/torch/mnist/test_mnist_training.yaml))

`# Dataset settingsdataset_id: "mnist"train_ratio: 0.8dtype: "torch.bfloat16" # Model settingsmodel_name: "MNISTLinear" # Training hyperparameterslearning_rate: 0.01batch_size: 256num_epochs: 16 # Device settings - Single device (no parallelism)mesh_shape: nullmesh_axis_names: null`
**Tensor-parallel YAML** ([blacksmith/experiments/torch/mnist/tensor_parallel/test_mnist_training_tp.yaml 53-69](https://github.com/tenstorrent/tt-blacksmith/blob/fe25eba0/blacksmith/experiments/torch/mnist/tensor_parallel/test_mnist_training_tp.yaml#L53-L69))

`# Device settings - Tensor Parallel with 2 devicesmesh_shape: [1, 2]  # [data, model]mesh_axis_names: ["data", "model"] model_sharding_patterns:    - ["linear_relu_stack\\.0$", [null, "model"]]    - ["linear_relu_stack\\.2$", ["model", null]]    - ["linear_relu_stack\\.4$", [null, "model"]]`
**Multi-chip (QuietBox) YAML for Llama** ([blacksmith/experiments/torch/llama/xla/lora/quietbox/test_llama_3_2_1b.yaml 22-38](https://github.com/tenstorrent/tt-blacksmith/blob/fe25eba0/blacksmith/experiments/torch/llama/xla/lora/quietbox/test_llama_3_2_1b.yaml#L22-L38))

`mesh_shape: [2, 4]  # Wormhole QuietBox mesh.mesh_axis_names: ["data", "model"] model_sharding_patterns:  - ['\.self_attn\.q_proj\.base_layer$',  ["model", null]]  - ['\.self_attn\.o_proj$',              [null, "model"]]  - ['\.mlp\.gate_proj$',                 ["model", null]]  - ['\.mlp\.down_proj$',                 [null, "model"]]`
The `model_sharding_patterns` field is a list of `[regex_pattern, partition_spec]` pairs consumed by `DeviceManager._apply_tensor_parallelism`. The method iterates `model.named_modules()`, matches each module name against regex patterns (first match wins), and calls `xs.mark_sharding(module.weight, mesh, partition_spec)`. See [blacksmith/tools/device_manager.py 89-110](https://github.com/tenstorrent/tt-blacksmith/blob/fe25eba0/blacksmith/tools/device_manager.py#L89-L110) and page 5.2 (Device Management and Hardware Abstraction) for detailed sharding logic.

Sources: [blacksmith/experiments/torch/mnist/test_mnist_training.yaml 1-64](https://github.com/tenstorrent/tt-blacksmith/blob/fe25eba0/blacksmith/experiments/torch/mnist/test_mnist_training.yaml#L1-L64)[blacksmith/experiments/torch/mnist/tensor_parallel/test_mnist_training_tp.yaml 53-62](https://github.com/tenstorrent/tt-blacksmith/blob/fe25eba0/blacksmith/experiments/torch/mnist/tensor_parallel/test_mnist_training_tp.yaml#L53-L62)[blacksmith/experiments/torch/llama/xla/lora/README.md 18-34](https://github.com/tenstorrent/tt-blacksmith/blob/fe25eba0/blacksmith/experiments/torch/llama/xla/lora/README.md?plain=1#L18-L34)[blacksmith/tools/device_manager.py 89-110](https://github.com/tenstorrent/tt-blacksmith/blob/fe25eba0/blacksmith/tools/device_manager.py#L89-L110)

* * *

## Template for New Experiments

The repository ships a reference template at two locations:

*   **Python class:**[blacksmith/tools/templates/configs.py](https://github.com/tenstorrent/tt-blacksmith/blob/fe25eba0/blacksmith/tools/templates/configs.py) — defines the minimum `TrainingConfig` with all common sections.
*   **YAML file:**[blacksmith/tools/templates/test_model_template.yaml](https://github.com/tenstorrent/tt-blacksmith/blob/fe25eba0/blacksmith/tools/templates/test_model_template.yaml) — mirrors the Python defaults.

New experiment authors copy these files, rename them, and add model-specific fields (LoRA rank, adapter dims, etc.) following the same `Field(default=...)` pattern.

* * *

## Config Merge: Main + Test Override

The `generate_config` function performs a dictionary merge when both `yaml_path` and `test_yaml_path` are provided:

**Merge semantics:**

*   Shared keys: Test config value overwrites main config value
*   New keys: Added to merged dict (e.g., `test_config` subfield typically only exists in test YAML)
*   Nested dicts: Python `|=` operator performs shallow merge (no recursive deep merge)

**Typical test YAML structure:**

`# Override hyperparameters for fast CI executionnum_epochs: 1batch_size: 2 # Add test-specific metadatatest_config:  expected_train_csv: "golden_train.csv"  expected_val_csv: "golden_val.csv"  loss_tolerance: 0.01`
The `test_config` subfield (defined in [blacksmith/tools/test_config.py](https://github.com/tenstorrent/tt-blacksmith/blob/fe25eba0/blacksmith/tools/test_config.py)) provides metadata for golden file validation in the pytest framework. See page 9.1 (Test Framework) for usage.

Sources: [blacksmith/tools/cli.py 12-23](https://github.com/tenstorrent/tt-blacksmith/blob/fe25eba0/blacksmith/tools/cli.py#L12-L23)[blacksmith/tools/test_config.py](https://github.com/tenstorrent/tt-blacksmith/blob/fe25eba0/blacksmith/tools/test_config.py)

* * *

## Config Consumption by Manager Classes

Once instantiated, a single `TrainingConfig` object is passed to every component in the experiment's `main()` block. No component modifies the config after construction.

Sources: [blacksmith/tools/device_manager.py 19-27](https://github.com/tenstorrent/tt-blacksmith/blob/fe25eba0/blacksmith/tools/device_manager.py#L19-L27)[blacksmith/tools/templates/configs.py 1-60](https://github.com/tenstorrent/tt-blacksmith/blob/fe25eba0/blacksmith/tools/templates/configs.py#L1-L60)

This wiki is featured in the [repository](https://github.com/tenstorrent/tt-blacksmith/blob/main/README.md)

Dismiss
Refresh this wiki

Enter email to refresh
---
project: tt-blacksmith
github: https://github.com/tenstorrent/tt-blacksmith
deepwiki: https://deepwiki.com/tenstorrent/tt-blacksmith/10-reference
---

# Reference

Relevant source files
*   [.gitignore](https://github.com/tenstorrent/tt-blacksmith/blob/fe25eba0/.gitignore)
*   [blacksmith/experiments/torch/mnist/README.md](https://github.com/tenstorrent/tt-blacksmith/blob/fe25eba0/blacksmith/experiments/torch/mnist/README.md?plain=1)
*   [blacksmith/experiments/torch/mnist/configs.py](https://github.com/tenstorrent/tt-blacksmith/blob/fe25eba0/blacksmith/experiments/torch/mnist/configs.py)
*   [blacksmith/experiments/torch/mnist/data_parallel/test_mnist_training_dp.yaml](https://github.com/tenstorrent/tt-blacksmith/blob/fe25eba0/blacksmith/experiments/torch/mnist/data_parallel/test_mnist_training_dp.yaml)
*   [blacksmith/experiments/torch/mnist/data_parallel/utils.py](https://github.com/tenstorrent/tt-blacksmith/blob/fe25eba0/blacksmith/experiments/torch/mnist/data_parallel/utils.py)
*   [blacksmith/experiments/torch/mnist/tensor_parallel/test_mnist_training_tp.yaml](https://github.com/tenstorrent/tt-blacksmith/blob/fe25eba0/blacksmith/experiments/torch/mnist/tensor_parallel/test_mnist_training_tp.yaml)
*   [blacksmith/experiments/torch/mnist/tensor_parallel/utils.py](https://github.com/tenstorrent/tt-blacksmith/blob/fe25eba0/blacksmith/experiments/torch/mnist/tensor_parallel/utils.py)
*   [blacksmith/experiments/torch/mnist/test_mnist_training.yaml](https://github.com/tenstorrent/tt-blacksmith/blob/fe25eba0/blacksmith/experiments/torch/mnist/test_mnist_training.yaml)
*   [blacksmith/tools/device_manager.py](https://github.com/tenstorrent/tt-blacksmith/blob/fe25eba0/blacksmith/tools/device_manager.py)
*   [setup.py](https://github.com/tenstorrent/tt-blacksmith/blob/fe25eba0/setup.py)

This section provides quick-reference material for TT-Blacksmith: a complete experiment inventory, environment dependency details, and pointers to detailed sub-reference pages. For conceptual background and architecture decisions, see the [Architecture](https://deepwiki.com/tenstorrent/tt-blacksmith/3-environment-management) section. For installation and first-run instructions, see [Getting Started](https://deepwiki.com/tenstorrent/tt-blacksmith/2-getting-started).

**Sub-pages in this section:**

| Page | Contents |
| --- | --- |
| [7.1 Hardware and Framework Support Matrix](https://deepwiki.com/tenstorrent/tt-blacksmith/7.1-model-loading-and-adaptation) | Which experiments run on which hardware (N150, N300, QuietBox, Galaxy) and framework (xla, ffe, gpu) |
| [7.2 Test Matrix and CI Configuration](https://deepwiki.com/tenstorrent/tt-blacksmith/7.2-dataset-implementations) | `pytest` markers, CI workflow files, test config format, and golden-log comparison |
| [7.3 Configuration Reference](https://deepwiki.com/tenstorrent/tt-blacksmith/10-reference#7.3) | All `TrainingConfig` fields across all experiment types, organized by logical group |
| [7.4 Contributing and Code Guidelines](https://deepwiki.com/tenstorrent/tt-blacksmith/10-reference#7.4) | PR process, issue templates, and pre-commit hooks |

* * *

## Experiment Inventory

Every experiment currently in the repository is listed below. Experiment scripts live under `blacksmith/experiments/<framework>/<model>/`, organized by hardware variant (`single_chip/`, `quietbox/`, `galaxy/`).

| Framework | Model | Dataset | Method | Devices |
| --- | --- | --- | --- | --- |
| PyTorch | MLP | MNIST | Full-model | N150 |
| PyTorch | CNN | MNIST | Full-model | N150 |
| PyTorch | MLP | MNIST | Full-model, Data parallel | N300 |
| PyTorch | MLP | MNIST | Full-model, Tensor parallel | N300 |
| PyTorch | Llama 3.2 1B | SST-2 | LoRA | N150 |
| PyTorch | Llama 3.2 1B | SST-2 | LoRA, Data + Tensor parallel | T3K |
| PyTorch | Llama 3.2 1B | SST-2 | LoRA, Data + Tensor parallel | WH/BH Galaxy |
| PyTorch | Llama 3.2 1B | SST-2 | Adapters | N150 |
| PyTorch | Llama 3.2 3B | SST-2 | LoRA, Tensor parallel | BH QuietBox |
| PyTorch | Llama 3.1 8B | SST-2 | LoRA, Data + Tensor parallel | T3K |
| PyTorch | Llama 3.1 8B | SST-2 | LoRA, Data + Tensor parallel | BH Galaxy |
| PyTorch | Qwen 2.5 0.5B | Text-to-SQL | LoRA | N150 |
| PyTorch | Qwen 2.5 1.5B | Text-to-SQL | LoRA | N150 |
| PyTorch | Qwen 3 4B Instruct | SST-2 | LoRA | N150 |
| PyTorch | Qwen 3 4B Instruct | SST-2 | LoRA, Tensor parallel | BH QuietBox |
| PyTorch | Gemma 3 1B | SST-2 | LoRA | N150 |
| PyTorch | Gemma 1.1 2B | SST-2 | LoRA | N150 |
| PyTorch | Gemma 1.1 2B | SQuAD V2 | LoRA | N150 |
| PyTorch | ALBERT | Banking77 | Adapters | N150 |
| PyTorch | Phi-1 | SST-2 | LoRA | N150 |
| PyTorch | Phi-1 | SQuAD V2 | LoRA | N150 |
| PyTorch | Phi-1.5 | SST-2 | LoRA | N150 |
| PyTorch | Phi-1.5 | SQuAD V2 | LoRA | N150 |
| JAX | MLP | MNIST | Full-model | N150 |
| JAX | MLP | MNIST | Full-model, Data parallel | N300 |
| JAX | MLP | MNIST | Full-model, Tensor parallel | N300 |
| JAX | NeRF | Blender | Full-model | N150 |
| JAX | Llama 3.2 1B | SST-2 | LoRA | N150 |
| JAX | Llama 3.2 1B | SST-2 | DoRA | N150 |
| JAX | DistilBERT | SST-2 | Distillation | N150 |
| JAX | DistilBERT | SST-2 | Distillation, Data parallel | N300 |
| Lightning | NeRF | Blender | Full-model | N150 |

Sources: [docs/src/experiments.md 9-42](https://github.com/tenstorrent/tt-blacksmith/blob/fe25eba0/docs/src/experiments.md?plain=1#L9-L42)

* * *

## Environment and Dependency Reference

The `env/activate` script selects a virtual environment and installs the appropriate dependency set based on the `--xla`, `--ffe`, or `--gpu` flag.

**Environment Selection and Dependency Diagram**

Sources: [env/activate 1-133](https://github.com/tenstorrent/tt-blacksmith/blob/fe25eba0/env/activate#L1-L133)[env/xla_requirements.txt 1-4](https://github.com/tenstorrent/tt-blacksmith/blob/fe25eba0/env/xla_requirements.txt#L1-L4)[env/requirements.txt 1-13](https://github.com/tenstorrent/tt-blacksmith/blob/fe25eba0/env/requirements.txt#L1-L13)[env/ffe_requirements.txt 1-11](https://github.com/tenstorrent/tt-blacksmith/blob/fe25eba0/env/ffe_requirements.txt#L1-L11)[env/gpu_requirements.txt 1-4](https://github.com/tenstorrent/tt-blacksmith/blob/fe25eba0/env/gpu_requirements.txt#L1-L4)

### Key Package Versions

| Package | Version / Source | Environment |
| --- | --- | --- |
| `pjrt-plugin-tt` | `0.9.0.dev20260228001009` (Tenstorrent PyPI) | `xla` |
| `torchvision` | `0.24.1+cpu` | `xla` |
| `torch` | `2.7.0` (cu126) | `gpu` |
| `torch-xla` | `2.7.0` (CUDA 12.6 wheel) | `gpu` |
| `tt-forge-fe` | `0.4.0.dev20250923` (Tenstorrent PyPI) | `ffe` |
| `tt-tvm` | `0.4.0.dev20250923` (Tenstorrent PyPI) | `ffe` |
| `transformers` | `4.57.1` | `xla`, `gpu` |
| `peft` | `>=latest` | `xla`, `ffe`, `gpu` |
| `wandb` | `>=0.12.10` | `xla`, `ffe`, `gpu` |
| `pydantic` | `>2.0` | `xla`, `ffe`, `gpu` |
| `flax` | latest | `xla`, `gpu` |

Sources: [env/xla_requirements.txt 1-4](https://github.com/tenstorrent/tt-blacksmith/blob/fe25eba0/env/xla_requirements.txt#L1-L4)[env/requirements.txt 1-13](https://github.com/tenstorrent/tt-blacksmith/blob/fe25eba0/env/requirements.txt#L1-L13)[env/ffe_requirements.txt 1-11](https://github.com/tenstorrent/tt-blacksmith/blob/fe25eba0/env/ffe_requirements.txt#L1-L11)[env/gpu_requirements.txt 1-4](https://github.com/tenstorrent/tt-blacksmith/blob/fe25eba0/env/gpu_requirements.txt#L1-L4)

* * *

## Experiment Path Structure

**Experiment Directory to Environment Mapping**

Sources: [docs/src/experiments.md 45-57](https://github.com/tenstorrent/tt-blacksmith/blob/fe25eba0/docs/src/experiments.md?plain=1#L45-L57)

* * *

## Version-Check Behavior for `pjrt-plugin-tt`

When the `xla_env` already exists and `env/activate` is sourced again, the script compares the installed version of `pjrt-plugin-tt` against `env/xla_requirements.txt` and force-reinstalls if there is a mismatch. This ensures the plugin stays aligned with the pinned version after nightly CI uplift.

Relevant logic: [env/activate 71-108](https://github.com/tenstorrent/tt-blacksmith/blob/fe25eba0/env/activate#L71-L108)

*   `get_expected_version` — parses `<pkg>==<version>` from a requirements file
*   `get_installed_version` — uses `pip show` inside the venv
*   `check_package_version` — calls `pip install --force-reinstall` on mismatch

Sources: [env/activate 43-108](https://github.com/tenstorrent/tt-blacksmith/blob/fe25eba0/env/activate#L43-L108)[env/xla_requirements.txt 1-4](https://github.com/tenstorrent/tt-blacksmith/blob/fe25eba0/env/xla_requirements.txt#L1-L4)

This wiki is featured in the [repository](https://github.com/tenstorrent/tt-blacksmith/blob/main/README.md)

Dismiss
Refresh this wiki

Enter email to refresh
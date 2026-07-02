---
project: tt-blacksmith
github: https://github.com/tenstorrent/tt-blacksmith
deepwiki: https://deepwiki.com/tenstorrent/tt-blacksmith
---

# Overview

 Relevant source files

* [.gitignore](https://github.com/tenstorrent/tt-blacksmith/blob/fe25eba0/.gitignore)
* [README.md](https://github.com/tenstorrent/tt-blacksmith/blob/fe25eba0/README.md?plain=1)
* [docs/shared/images/nerf\_demo.gif](https://github.com/tenstorrent/tt-blacksmith/blob/fe25eba0/docs/shared/images/nerf_demo.gif)
* [docs/shared/images/tt-blacksmith-logo.png](https://github.com/tenstorrent/tt-blacksmith/blob/fe25eba0/docs/shared/images/tt-blacksmith-logo.png)
* [docs/src/getting-started.md](https://github.com/tenstorrent/tt-blacksmith/blob/fe25eba0/docs/src/getting-started.md?plain=1)
* [setup.py](https://github.com/tenstorrent/tt-blacksmith/blob/fe25eba0/setup.py)

TT-Blacksmith is a repository of optimized training recipes and infrastructure for machine learning models running on Tenstorrent hardware. This page provides a high-level introduction to the repository's purpose, architecture, and major systems.

For setup instructions, see [Getting Started](/tenstorrent/tt-blacksmith/2-getting-started). For experiment details, see [Experiments](/tenstorrent/tt-blacksmith/6-experiments).

## What is TT-Blacksmith?

TT-Blacksmith is a collection of optimized ML training recipes for [Tenstorrent](https://tenstorrent.com/) accelerator hardware. The repository demonstrates how to train models using PyTorch and JAX frameworks on Tenstorrent devices through the TT-Forge compiler stack.

**Primary Goals:**

* **Training Demonstrations**: End-to-end training scripts for models including Llama, Qwen, Gemma, MNIST, and NeRF
* **Reusable Infrastructure**: Configuration-driven training framework with device management, checkpointing, logging, and reproducibility managers
* **Multi-Chip Support**: Data parallelism, tensor parallelism, and hybrid strategies for scaling across N150, N300, and Galaxy hardware

The Python package is named `blacksmith` ([setup.py1-11](https://github.com/tenstorrent/tt-blacksmith/blob/fe25eba0/setup.py#L1-L11)) and provides the core training infrastructure used across all experiments.

Sources: [README.md18-33](https://github.com/tenstorrent/tt-blacksmith/blob/fe25eba0/README.md?plain=1#L18-L33) [setup.py1-11](https://github.com/tenstorrent/tt-blacksmith/blob/fe25eba0/setup.py#L1-L11)

## Position in the Tenstorrent Software Stack

TT-Blacksmith sits at the application layer of the Tenstorrent software stack. Experiments written in PyTorch or JAX use framework front-ends (`pjrt-plugin-tt` for XLA, `tt-forge-fe` for PyTorch) that compile through TT-MLIR to the TT-Metalium hardware runtime.

**Diagram: TT-Blacksmith in the Software Stack**

Sources: [README.md20-46](https://github.com/tenstorrent/tt-blacksmith/blob/fe25eba0/README.md?plain=1#L20-L46) [docs/src/getting-started.md22-28](https://github.com/tenstorrent/tt-blacksmith/blob/fe25eba0/docs/src/getting-started.md?plain=1#L22-L28)

## Repository Structure

The repository is organized into four top-level directories with the `blacksmith/` Python package at its core:

**Diagram: Repository Structure and Key Files**

**Key Directories:**

* `blacksmith/experiments/`: Training scripts organized by framework (PyTorch/JAX)
* `blacksmith/models/`: Model loading via HuggingFace with LoRA/Adapter support
* `blacksmith/datasets/`: Dataset implementations following `BaseDataset` pattern
* `blacksmith/utils/`: Core infrastructure (config, device management, logging, checkpointing)
* `env/`: Environment activation script and dependency specifications
* `tests/`: pytest-based test suite with golden file validation

Sources: [setup.py1-11](https://github.com/tenstorrent/tt-blacksmith/blob/fe25eba0/setup.py#L1-L11) [.gitignore1-21](https://github.com/tenstorrent/tt-blacksmith/blob/fe25eba0/.gitignore#L1-L21)

## Environment Management

TT-Blacksmith provides three virtual environments, each configured for specific hardware and framework combinations. The environment activation script (`env/activate`) handles installation and dependency management automatically.

| Flag | Environment Directory | Primary Dependencies | Use Case |
| --- | --- | --- | --- |
| `--xla` | `env/xla_env/` | `pjrt-plugin-tt`, `torch-xla` (CPU build), JAX | JAX and PyTorch/XLA on Tenstorrent hardware |
| `--ffe` | `env/ffe_env/` | `tt-forge-fe`, `tt-tvm` | PyTorch via TT-Forge frontend on Tenstorrent |
| `--gpu` | `env/gpu_env/` | `torch`, `torch-xla` (CUDA build) | PyTorch on NVIDIA GPUs for comparison |

**Activation:**

The script performs several operations on first activation:

1. Creates a Python virtual environment in `env/{xla,ffe,gpu}_env/`
2. Installs framework-specific dependencies from `env/{xla,ffe}_requirements.txt`
3. Installs common dependencies from `env/requirements.txt`
4. For `--xla`, verifies and reinstalls `pjrt-plugin-tt` if version mismatch detected

A nightly CI workflow ([.github/workflows/schedule-uplift.yml](https://github.com/tenstorrent/tt-blacksmith/blob/fe25eba0/.github/workflows/schedule-uplift.yml)) automatically updates `xla_requirements.txt` with the latest `pjrt-plugin-tt` version from Tenstorrent's PyPI server.

Sources: [docs/src/getting-started.md22-28](https://github.com/tenstorrent/tt-blacksmith/blob/fe25eba0/docs/src/getting-started.md?plain=1#L22-L28) [.gitignore15-21](https://github.com/tenstorrent/tt-blacksmith/blob/fe25eba0/.gitignore#L15-L21)

## Available Experiments

TT-Blacksmith provides training recipes for models ranging from MNIST classifiers to multi-billion parameter LLMs. Experiments demonstrate single-chip training, data parallelism, tensor parallelism, and hybrid strategies across Tenstorrent hardware.

**Experiment Categories:**

* **PyTorch**: MNIST (MLP/CNN), Llama (1B-8B), Qwen (0.5B-4B), Gemma (1B-2B), Falcon3, Phi, ALBERT
* **JAX**: MNIST, NeRF, Llama with LoRA/DoRA, DistilBERT
* **Hardware Scaling**: Single-chip (N150), dual-chip (N300), multi-chip (Galaxy with up to 32 devices)
* **Training Methods**: Full fine-tuning, LoRA, Adapters, DoRA, knowledge distillation

For detailed experiment instructions, configurations, and expected results, see [Experiments](/tenstorrent/tt-blacksmith/6-experiments), [PyTorch Experiments](/tenstorrent/tt-blacksmith/6.1-pytorch-experiments), and [JAX Experiments](/tenstorrent/tt-blacksmith/6.2-jax-experiments).

Sources: [docs/src/getting-started.md30-37](https://github.com/tenstorrent/tt-blacksmith/blob/fe25eba0/docs/src/getting-started.md?plain=1#L30-L37)

---

## Key Design Principles

**Diagram: Experiment Script Dependencies**

Every experiment script follows the same pattern:

1. Load a `TrainingConfig` from a YAML file (with optional CLI overrides via `parse_cli_options` / `generate_config`).
2. Instantiate `DeviceManager`, `TrainingLogger`, `CheckpointManager`, and `ReproducibilityManager` with that config.
3. Call `get_model()` and `get_dataset()` to obtain a PEFT-adapted model and a `BaseDataset` subclass.
4. Enter the canonical `train()` loop.

For the full details of each subsystem, see:

* [Configuration System](#3.3)
* [Training Infrastructure](#3.5)
* [Multi-Chip Parallelism Architecture](#3.6)
* [Models and Datasets](/tenstorrent/tt-blacksmith/5-training-framework)

Sources: [docs/src/experiments.md46-57](https://github.com/tenstorrent/tt-blacksmith/blob/fe25eba0/docs/src/experiments.md?plain=1#L46-L57) [blacksmith/experiments/torch/llama/xla/lora/README.md1-170](https://github.com/tenstorrent/tt-blacksmith/blob/fe25eba0/blacksmith/experiments/torch/llama/xla/lora/README.md?plain=1#L1-L170) [README.md19-48](https://github.com/tenstorrent/tt-blacksmith/blob/fe25eba0/README.md?plain=1#L19-L48)

Refresh this wiki
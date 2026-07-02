---
project: tt-blacksmith
github: https://github.com/tenstorrent/tt-blacksmith
deepwiki: https://deepwiki.com/tenstorrent/tt-blacksmith/2-getting-started
---

# Getting Started

Relevant source files
*   [README.md](https://github.com/tenstorrent/tt-blacksmith/blob/fe25eba0/README.md?plain=1)
*   [docs/shared/images/nerf_demo.gif](https://github.com/tenstorrent/tt-blacksmith/blob/fe25eba0/docs/shared/images/nerf_demo.gif)
*   [docs/shared/images/tt-blacksmith-logo.png](https://github.com/tenstorrent/tt-blacksmith/blob/fe25eba0/docs/shared/images/tt-blacksmith-logo.png)
*   [docs/src/getting-started.md](https://github.com/tenstorrent/tt-blacksmith/blob/fe25eba0/docs/src/getting-started.md?plain=1)
*   [env/activate](https://github.com/tenstorrent/tt-blacksmith/blob/fe25eba0/env/activate)
*   [env/ffe_requirements.txt](https://github.com/tenstorrent/tt-blacksmith/blob/fe25eba0/env/ffe_requirements.txt)
*   [env/gpu_requirements.txt](https://github.com/tenstorrent/tt-blacksmith/blob/fe25eba0/env/gpu_requirements.txt)
*   [env/requirements.txt](https://github.com/tenstorrent/tt-blacksmith/blob/fe25eba0/env/requirements.txt)
*   [env/xla_requirements.txt](https://github.com/tenstorrent/tt-blacksmith/blob/fe25eba0/env/xla_requirements.txt)

This page covers cloning the repository, selecting and activating the correct Python environment, running a first experiment, and recovering from a broken environment state. For information about how experiments are organized and configured in detail, see [Experiment Framework Organization](https://deepwiki.com/tenstorrent/tt-blacksmith/2-getting-started#3.2). For the full list of available experiments, see [Experiments](https://deepwiki.com/tenstorrent/tt-blacksmith/4-configuration-system).

* * *

## Prerequisites

*   Python 3.12 available as `python3.12` on your `PATH`
*   Access to Tenstorrent hardware (for `--xla` and `--ffe` environments) or a CUDA-capable GPU (for `--gpu`)
*   Network access to `https://pypi.eng.aws.tenstorrent.com` for Tenstorrent-specific packages

* * *

## Step 1: Clone the Repository

* * *

## Step 2: Activate the Environment

All environment setup is handled by the `env/activate` shell script. It must be **sourced** (not executed) so that the virtual environment becomes active in your current shell session.

If no flag is provided, the script defaults to `--xla` and prints a warning.

Sources: [env/activate 1-21](https://github.com/tenstorrent/tt-blacksmith/blob/fe25eba0/env/activate#L1-L21)

* * *

### Environment Types

The flag you pass determines which virtual environment directory is created and which dependency set is installed.

| Flag | `EXPERIMENT_TYPE` | Virtual Env Directory | Hardware Target |
| --- | --- | --- | --- |
| `--ffe` | `0` | `env/ffe_env` | Tenstorrent (TT-Forge-FE) |
| `--xla` | `1` | `env/xla_env` | Tenstorrent (TT-XLA / PJRT) |
| `--gpu` | `2` | `env/gpu_env` | CUDA GPU |

For `--gpu`, the script additionally exports `PJRT_DEVICE=CUDA` into the shell environment.

Sources: [env/activate 27-41](https://github.com/tenstorrent/tt-blacksmith/blob/fe25eba0/env/activate#L27-L41)

* * *

### Requirements Files Per Environment

**Environment-to-requirements mapping:**

| Requirements File | Key Packages |
| --- | --- |
| `env/xla_requirements.txt` | `pjrt-plugin-tt`, `torchvision` (CPU build) |
| `env/gpu_requirements.txt` | `torch` (CUDA 12.6), `torch-xla` (CUDA wheel) |
| `env/ffe_requirements.txt` | `tt-tvm`, `tt-forge-fe`, `torchvision`, `peft`, `transformers` |
| `env/requirements.txt` | `wandb`, `pydantic`, `peft`, `transformers`, `flax`, `datasets`, `pytest`, `pandas` |

`env/requirements.txt` is shared by the `--xla` and `--gpu` environments. The `--ffe` environment bundles its own copies of common packages directly in `env/ffe_requirements.txt`.

Sources: [env/xla_requirements.txt 1-4](https://github.com/tenstorrent/tt-blacksmith/blob/fe25eba0/env/xla_requirements.txt#L1-L4)[env/gpu_requirements.txt 1-4](https://github.com/tenstorrent/tt-blacksmith/blob/fe25eba0/env/gpu_requirements.txt#L1-L4)[env/ffe_requirements.txt 1-11](https://github.com/tenstorrent/tt-blacksmith/blob/fe25eba0/env/ffe_requirements.txt#L1-L11)[env/requirements.txt 1-13](https://github.com/tenstorrent/tt-blacksmith/blob/fe25eba0/env/requirements.txt#L1-L13)[env/activate 117-130](https://github.com/tenstorrent/tt-blacksmith/blob/fe25eba0/env/activate#L117-L130)

* * *

### First-Time Activation

When `env/*_env` does not yet exist, `env/activate` runs the full setup:

1.   Creates a virtual environment with `python3.12 -m venv`
2.   Upgrades `pip`
3.   Installs the appropriate requirements files
4.   Installs the `blacksmith` package itself in editable mode (`pip install -e .`)

**First-time setup flow:**

Sources: [env/activate 92-133](https://github.com/tenstorrent/tt-blacksmith/blob/fe25eba0/env/activate#L92-L133)

* * *

### Automatic Version Checking (XLA Only)

On every subsequent activation of the `--xla` environment, the `check_package_version` function compares the installed version of `pjrt-plugin-tt` against the version pinned in `env/xla_requirements.txt`. If they differ, it force-reinstalls the expected version using the custom Tenstorrent PyPI index.

The helper functions involved are:

*   `get_expected_version` — greps the pinned version from the requirements file
*   `get_installed_version` — queries `pip show` against the venv's pip
*   `get_index_urls` — extracts `--index-url` / `--extra-index-url` lines from the requirements file
*   `check_package_version` — orchestrates the comparison and reinstall

Sources: [env/activate 43-88](https://github.com/tenstorrent/tt-blacksmith/blob/fe25eba0/env/activate#L43-L88)[env/activate 103-105](https://github.com/tenstorrent/tt-blacksmith/blob/fe25eba0/env/activate#L103-L105)

* * *

## Step 3: Run Your First Experiment

Once the environment is active, experiments are run directly as Python scripts. The general pattern is:

**To find an experiment to run:**

1.   Browse `docs/src/experiments.md` for the full experiment table, or see [Experiments](https://deepwiki.com/tenstorrent/tt-blacksmith/4-configuration-system) in this wiki.
2.   Read the `README.md` inside the experiment's directory for hardware requirements, supported YAML configs, and any model-specific prerequisites (e.g. HuggingFace token for gated models).
3.   Pick a YAML config matching your hardware (e.g. `single_chip.yaml`, `quietbox.yaml`).

**Example — MNIST on a single TT chip (XLA environment):**

For a JAX experiment, the same pattern applies under `blacksmith/experiments/jax/`.

Sources: [docs/src/getting-started.md 31-37](https://github.com/tenstorrent/tt-blacksmith/blob/fe25eba0/docs/src/getting-started.md?plain=1#L31-L37)

* * *

## Recovery and Reset

If the environment installation was interrupted, or you need to update to a newer version of the environment's dependencies, the recommended recovery path is:

This removes all generated virtual environment directories while preserving the tracked `env/` script and requirements files, then re-runs the full first-time setup on next activation.

> **Note:** Do not manually edit files inside `env/xla_env`, `env/ffe_env`, or `env/gpu_env`. These directories are generated and are not tracked by git. Only `env/activate`, `env/requirements.txt`, `env/xla_requirements.txt`, `env/gpu_requirements.txt`, and `env/ffe_requirements.txt` are tracked.

Sources: [docs/src/getting-started.md 12-21](https://github.com/tenstorrent/tt-blacksmith/blob/fe25eba0/docs/src/getting-started.md?plain=1#L12-L21)

* * *

## Next Steps

| Topic | Wiki Page |
| --- | --- |
| Full list of available experiments | [Experiments](https://deepwiki.com/tenstorrent/tt-blacksmith/4-configuration-system) |
| How experiments are organized (YAML, test_*.py, README) | [Experiment Framework Organization](https://deepwiki.com/tenstorrent/tt-blacksmith/2-getting-started#3.2) |
| TrainingConfig fields and YAML schema | [Configuration System](https://deepwiki.com/tenstorrent/tt-blacksmith/2-getting-started#3.3) |
| Environment management internals | [Environment Management](https://deepwiki.com/tenstorrent/tt-blacksmith/2-getting-started#3.4) |
| Multi-chip parallelism setup | [Multi-Chip Parallelism Architecture](https://deepwiki.com/tenstorrent/tt-blacksmith/2-getting-started#3.6) |
| Hardware and framework support matrix | [Hardware and Framework Support Matrix](https://deepwiki.com/tenstorrent/tt-blacksmith/7.1-model-loading-and-adaptation) |

Dismiss
Refresh this wiki

Enter email to refresh
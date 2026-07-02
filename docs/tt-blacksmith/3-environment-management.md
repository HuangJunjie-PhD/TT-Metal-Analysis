---
project: tt-blacksmith
github: https://github.com/tenstorrent/tt-blacksmith
deepwiki: https://deepwiki.com/tenstorrent/tt-blacksmith/3-environment-management
---

# Environment Management

Relevant source files
*   [env/activate](https://github.com/tenstorrent/tt-blacksmith/blob/fe25eba0/env/activate)
*   [env/ffe_requirements.txt](https://github.com/tenstorrent/tt-blacksmith/blob/fe25eba0/env/ffe_requirements.txt)
*   [env/gpu_requirements.txt](https://github.com/tenstorrent/tt-blacksmith/blob/fe25eba0/env/gpu_requirements.txt)
*   [env/requirements.txt](https://github.com/tenstorrent/tt-blacksmith/blob/fe25eba0/env/requirements.txt)
*   [env/xla_requirements.txt](https://github.com/tenstorrent/tt-blacksmith/blob/fe25eba0/env/xla_requirements.txt)

## Purpose and Scope

This document details the environment management system in TT-Blacksmith, which provides isolated Python environments for different training backends. The system enables users to switch between TT-XLA (default), TT-Forge Frontend (FFE), and GPU backends through a single activation script. For information about the specific compilation pipelines these environments support, see [XLA and Forge Compilation](https://deepwiki.com/tenstorrent/tt-blacksmith/3-environment-management#3.4). For hardware configuration and device management within a running environment, see [Device Management and Sharding](https://deepwiki.com/tenstorrent/tt-blacksmith/3-environment-management#3.5.4).

**Sources:**[env/activate 1-81](https://github.com/tenstorrent/tt-blacksmith/blob/fe25eba0/env/activate#L1-L81)

## Environment Types

TT-Blacksmith supports three distinct environment types, each with its own dependency requirements and configuration:

| Environment Type | Flag | Virtual Environment Path | Primary Backend |
| --- | --- | --- | --- |
| TT-XLA | `--xla` | `env/xla_env` | PyTorch-XLA with PJRT plugin for TT hardware |
| TT-Forge Frontend | `--ffe` | `env/ffe_env` | TT-TVM and TT-Forge-FE compiler stack |
| GPU | `--gpu` | `env/gpu_env` | PyTorch-XLA with CUDA backend |

**Sources:**[env/activate 10-21](https://github.com/tenstorrent/tt-blacksmith/blob/fe25eba0/env/activate#L10-L21)[env/activate 28-43](https://github.com/tenstorrent/tt-blacksmith/blob/fe25eba0/env/activate#L28-L43)

## Activation Script Workflow

The activation script `env/activate` orchestrates environment selection, virtual environment creation, and dependency installation. It must be sourced (not executed) to modify the current shell's environment.

**Sources:**[env/activate 1-81](https://github.com/tenstorrent/tt-blacksmith/blob/fe25eba0/env/activate#L1-L81)

### Frontend Selection Logic

The activation script uses a case statement to map command-line flags to internal experiment types:

**Sources:**[env/activate 10-21](https://github.com/tenstorrent/tt-blacksmith/blob/fe25eba0/env/activate#L10-L21)[env/activate 28-43](https://github.com/tenstorrent/tt-blacksmith/blob/fe25eba0/env/activate#L28-L43)

## Dependency Structure

Each environment type has a dedicated requirements file that specifies its unique dependencies. All environments also install common dependencies from `env/requirements.txt`.

### XLA Environment Dependencies

The XLA environment requires the PJRT plugin for Tenstorrent hardware, which provides the low-level device interface for PyTorch-XLA:

**Key Dependency:** The `pjrt-plugin-tt` package is hosted on Tenstorrent's internal PyPI index (`https://pypi.eng.aws.tenstorrent.com`) and provides the PJRT (Portable Just-In-Time Runtime) plugin that enables TT hardware as an XLA device.

**Sources:**[env/xla_requirements.txt 1-7](https://github.com/tenstorrent/tt-blacksmith/blob/fe25eba0/env/xla_requirements.txt#L1-L7)[env/requirements.txt 1-8](https://github.com/tenstorrent/tt-blacksmith/blob/fe25eba0/env/requirements.txt#L1-L8)[env/activate 68-70](https://github.com/tenstorrent/tt-blacksmith/blob/fe25eba0/env/activate#L68-L70)

### FFE Environment Dependencies

The Forge Frontend environment uses TT-specific compiler packages for direct model compilation:

Both `tt-tvm` and `tt-forge-fe` are installed from specific wheel URLs with SHA256 checksums for version integrity verification.

**Sources:**[env/ffe_requirements.txt 1-4](https://github.com/tenstorrent/tt-blacksmith/blob/fe25eba0/env/ffe_requirements.txt#L1-L4)[env/activate 66-67](https://github.com/tenstorrent/tt-blacksmith/blob/fe25eba0/env/activate#L66-L67)

### GPU Environment Dependencies

The GPU environment provides a reference implementation using CUDA-enabled PyTorch-XLA:

The GPU environment is unique in setting `PJRT_DEVICE=CUDA` to direct the PJRT runtime to use CUDA devices instead of TT hardware.

**Sources:**[env/gpu_requirements.txt 1-5](https://github.com/tenstorrent/tt-blacksmith/blob/fe25eba0/env/gpu_requirements.txt#L1-L5)[env/activate 37-39](https://github.com/tenstorrent/tt-blacksmith/blob/fe25eba0/env/activate#L37-L39)

## Environment Variables and Path Configuration

The activation script configures several environment variables to ensure proper package discovery and device selection:

| Variable | Value | Purpose |
| --- | --- | --- |
| `TT_BLACKSMITH_HOME` | `$(pwd)` | Root directory of the repository |
| `PYTHONPATH` | `$(pwd):$(pwd)/tests:$SYSTEM_DIST_PACKAGES` | Python module search path |
| `TT_BLACKSMITH_ENV_DIR` | `$TT_BLACKSMITH_HOME/env/{xla,ffe,gpu}_env` | Virtual environment location |
| `PJRT_DEVICE` | `CUDA` (GPU only) | PJRT device selection |
| `SYSTEM_DIST_PACKAGES` | `/usr/local/lib/python3.11/dist-packages` | System packages path |

**Sources:**[env/activate 1](https://github.com/tenstorrent/tt-blacksmith/blob/fe25eba0/env/activate#L1-L1)[env/activate 23-24](https://github.com/tenstorrent/tt-blacksmith/blob/fe25eba0/env/activate#L23-L24)[env/activate 31-39](https://github.com/tenstorrent/tt-blacksmith/blob/fe25eba0/env/activate#L31-L39)

### Python Path Structure

The `PYTHONPATH` configuration enables import resolution for the blacksmith package, test modules, and system packages:

**Sources:**[env/activate 23-24](https://github.com/tenstorrent/tt-blacksmith/blob/fe25eba0/env/activate#L23-L24)

## Virtual Environment Creation

When a virtual environment does not exist, the activation script creates it using Python 3.11's `venv` module:

**Sources:**[env/activate 45-81](https://github.com/tenstorrent/tt-blacksmith/blob/fe25eba0/env/activate#L45-L81)

### Editable Installation

After installing dependencies, the script installs the blacksmith package in editable mode using `pip install -e .`, which references the `setup.py` configuration:

The `setup.py` file defines a minimal package configuration that includes all `blacksmith*` packages:

```
Package: blacksmith
Version: 0.1
Description: Tenstorrent Python Blacksmith
Packages: blacksmith* (discovered via find_packages)
```

This editable installation allows code changes in the repository to be immediately available without reinstallation.

**Sources:**[setup.py 1-12](https://github.com/tenstorrent/tt-blacksmith/blob/fe25eba0/setup.py#L1-L12)[env/activate 80](https://github.com/tenstorrent/tt-blacksmith/blob/fe25eba0/env/activate#L80-L80)

## Dependency Update Automation

The XLA environment's `pjrt-plugin-tt` dependency is automatically updated through the nightly uplift workflow (see [CI/CD and Automation](https://deepwiki.com/tenstorrent/tt-blacksmith/3-environment-management#3.7)). The dependency specification includes:

*   **Package source:** Custom PyPI index at `--extra-index-url https://pypi.eng.aws.tenstorrent.com`
*   **Version format:**`0.8.0.dev<YYYYMMDD>` (development releases with date stamps)
*   **Update mechanism:** Automated PR creation by GitHub Actions that modifies `xla_requirements.txt`

**Sources:**[env/xla_requirements.txt 1-2](https://github.com/tenstorrent/tt-blacksmith/blob/fe25eba0/env/xla_requirements.txt#L1-L2)

## Environment Isolation

Each environment type maintains complete isolation:

This isolation prevents dependency conflicts between incompatible backend implementations and allows switching environments without deactivation by simply sourcing the activate script with a different flag.

**Sources:**[env/activate 28-43](https://github.com/tenstorrent/tt-blacksmith/blob/fe25eba0/env/activate#L28-L43)[env/activate 63-77](https://github.com/tenstorrent/tt-blacksmith/blob/fe25eba0/env/activate#L63-L77)

This wiki is featured in the [repository](https://github.com/tenstorrent/tt-blacksmith/blob/main/README.md)

Dismiss
Refresh this wiki

Enter email to refresh
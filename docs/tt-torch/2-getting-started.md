---
project: tt-torch
github: https://github.com/tenstorrent/tt-torch
deepwiki: https://deepwiki.com/tenstorrent/tt-torch/2-getting-started
---

# Getting Started

Relevant source files
*   [.github/Dockerfile.base](https://github.com/tenstorrent/tt-torch/blob/f5e3955f/.github/Dockerfile.base)
*   [.github/Dockerfile.ci](https://github.com/tenstorrent/tt-torch/blob/f5e3955f/.github/Dockerfile.ci)
*   [README.md](https://github.com/tenstorrent/tt-torch/blob/f5e3955f/README.md?plain=1)
*   [docs/src/adding_models.md](https://github.com/tenstorrent/tt-torch/blob/f5e3955f/docs/src/adding_models.md?plain=1)
*   [docs/src/controlling.md](https://github.com/tenstorrent/tt-torch/blob/f5e3955f/docs/src/controlling.md?plain=1)
*   [docs/src/overview.md](https://github.com/tenstorrent/tt-torch/blob/f5e3955f/docs/src/overview.md?plain=1)
*   [docs/src/pre_commit.md](https://github.com/tenstorrent/tt-torch/blob/f5e3955f/docs/src/pre_commit.md?plain=1)
*   [docs/src/test.md](https://github.com/tenstorrent/tt-torch/blob/f5e3955f/docs/src/test.md?plain=1)
*   [env/activate](https://github.com/tenstorrent/tt-torch/blob/f5e3955f/env/activate)
*   [requirements.txt](https://github.com/tenstorrent/tt-torch/blob/f5e3955f/requirements.txt)
*   [setup.py](https://github.com/tenstorrent/tt-torch/blob/f5e3955f/setup.py)
*   [tt_torch/__init__.py](https://github.com/tenstorrent/tt-torch/blob/f5e3955f/tt_torch/__init__.py)
*   [tt_torch/csrc/CMakeLists.txt](https://github.com/tenstorrent/tt-torch/blob/f5e3955f/tt_torch/csrc/CMakeLists.txt)
*   [tt_torch/tools/profile_util.py](https://github.com/tenstorrent/tt-torch/blob/f5e3955f/tt_torch/tools/profile_util.py)

This page provides an overview of the prerequisites and setup workflow for tt-torch. It guides you through selecting the appropriate installation method and verifying your setup before moving on to compiling and running models.

For detailed installation instructions, see [Installation and Setup](https://deepwiki.com/tenstorrent/tt-torch/2.1-installation-and-setup). For usage examples and your first model compilation, see [Basic Usage Examples](https://deepwiki.com/tenstorrent/tt-torch/2.2-basic-usage-examples). For architecture details about the compilation pipeline, see [Architecture](https://deepwiki.com/tenstorrent/tt-torch/3-architecture).

* * *

## Overview

tt-torch is a PyTorch compiler frontend that enables running PyTorch models on Tenstorrent AI accelerators. The system integrates with PyTorch's `torch.compile` interface and transforms models through multiple MLIR dialects before executing on Tenstorrent hardware.

**Diagram: tt-torch System Integration**

Sources: [README.md 19-34](https://github.com/tenstorrent/tt-torch/blob/f5e3955f/README.md?plain=1#L19-L34)[docs/src/getting_started.md 1-25](https://github.com/tenstorrent/tt-torch/blob/f5e3955f/docs/src/getting_started.md?plain=1#L1-L25)

* * *

## Prerequisites

### Hardware Requirements

tt-torch requires Tenstorrent AI accelerator hardware. Supported architectures include:

| Architecture | Device Type | Notes |
| --- | --- | --- |
| Wormhole B0 | Single chip or multi-chip | Primary development target |
| Blackhole | Single chip or multi-chip | Newer architecture |
| N300 | Multi-chip system | Requires mesh configuration |

**Hardware Configuration Steps:**

1.   Install hardware drivers using TT-Installer
2.   Enable hugepages for memory management
3.   Verify device visibility using `tt-smi`

**Diagram: Hardware Configuration Workflow**

Sources: [docs/src/getting_started.md 26-50](https://github.com/tenstorrent/tt-torch/blob/f5e3955f/docs/src/getting_started.md?plain=1#L26-L50)[docs/src/getting_started_build_from_source.md 19-36](https://github.com/tenstorrent/tt-torch/blob/f5e3955f/docs/src/getting_started_build_from_source.md?plain=1#L19-L36)

### Software Requirements

The following system dependencies are required:

| Component | Version | Purpose |
| --- | --- | --- |
| Operating System | Ubuntu 22.04 | Base platform |
| Python | 3.11+ | Runtime environment |
| CMake | 4.0.3+ | Build system (source builds only) |
| LLVM/Clang | 17 | Compiler toolchain (source builds only) |
| GCC | 11 | C++ compilation (source builds only) |

Additional runtime dependencies include OpenMPI for multi-device execution and various system libraries detailed in [Installation and Setup](https://deepwiki.com/tenstorrent/tt-torch/2.1-installation-and-setup).

Sources: [docs/src/getting_started_build_from_source.md 38-48](https://github.com/tenstorrent/tt-torch/blob/f5e3955f/docs/src/getting_started_build_from_source.md?plain=1#L38-L48)[docs/src/getting_started_build_from_source.md 122-147](https://github.com/tenstorrent/tt-torch/blob/f5e3955f/docs/src/getting_started_build_from_source.md?plain=1#L122-L147)

* * *

## Installation Methods

tt-torch supports three installation approaches, each suited to different use cases:

**Diagram: Installation Method Decision Tree**

### Method 1: Wheel Installation (Recommended for Users)

Best for running models without modifying tt-torch itself. Installs pre-built binaries.

**Quick Start:**

`# Activate virtual environmentsource ~/.tenstorrent-venv/bin/activate # Install tt-torchpip install tt-torch --pre --extra-index-url https://pypi.eng.aws.tenstorrent.com/ # Install MPI (required for multi-device)wget -q https://github.com/dmakoviichuk-tt/mpi-ulfm/releases/download/v5.0.7-ulfm/openmpi-ulfm_5.0.7-1_amd64.deb -O /tmp/openmpi-ulfm.debsudo apt install -y /tmp/openmpi-ulfm.deb`
For complete wheel installation instructions, see [Installation and Setup](https://deepwiki.com/tenstorrent/tt-torch/2.1-installation-and-setup).

Sources: [docs/src/getting_started.md 52-98](https://github.com/tenstorrent/tt-torch/blob/f5e3955f/docs/src/getting_started.md?plain=1#L52-L98)

### Method 2: Docker Container

Best for keeping tt-torch isolated from your host environment. Provides a pre-configured container with all dependencies.

**Quick Start:**

`docker run -it --rm \  --device /dev/tenstorrent \  -v /dev/hugepages-1G:/dev/hugepages-1G \  ghcr.io/tenstorrent/tt-torch-slim:latest`
**Important:** Pass `--device /dev/tenstorrent` to expose all Tenstorrent devices. Do not try to isolate individual devices (e.g., `/dev/tenstorrent/1`) as this causes runtime errors.

For complete Docker setup instructions, see [Installation and Setup](https://deepwiki.com/tenstorrent/tt-torch/2.1-installation-and-setup).

Sources: [docs/src/getting_started_docker.md 35-79](https://github.com/tenstorrent/tt-torch/blob/f5e3955f/docs/src/getting_started_docker.md?plain=1#L35-L79)

### Method 3: Build from Source

Required for developing tt-torch or modifying the compiler pipeline. This method builds the entire toolchain including LLVM/MLIR components.

**Key Build Artifacts:**

| Component | Output | Location |
| --- | --- | --- |
| MLIR Toolchain | libTTMLIRCompiler.so | /opt/ttmlir-toolchain |
| MLIR Runtime | libTTMLIRRuntime.so | build/ |
| Python Bindings | tt_mlir.so | build/ |
| tt-torch Package | tt-torch wheel | build/ |

**Build Steps:**

`# Clone repositorygit clone https://github.com/tenstorrent/tt-torch.gitcd tt-torch # Build toolchain (one-time, takes ~1 hour)sudo mkdir -p /opt/ttmlir-toolchainsudo chown -R $USER /opt/ttmlir-toolchaincd third_partycmake -B toolchain -DBUILD_TOOLCHAIN=ONcd .. # Build tt-torchsource env/activatecmake -G Ninja -B buildcmake --build buildcmake --install build`
For complete build instructions and dependency details, see [Installation and Setup](https://deepwiki.com/tenstorrent/tt-torch/2.1-installation-and-setup).

Sources: [docs/src/getting_started_build_from_source.md 153-192](https://github.com/tenstorrent/tt-torch/blob/f5e3955f/docs/src/getting_started_build_from_source.md?plain=1#L153-L192)

* * *

## Compilation and Execution Workflow

Once installed, tt-torch integrates with PyTorch's compilation API:

**Diagram: Compilation and Execution Flow**

The compilation process is controlled by `CompilerConfig` from [tt_torch/tools/utils.py](https://github.com/tenstorrent/tt-torch/blob/f5e3955f/tt_torch/tools/utils.py) and executed by the backend in [tt_torch/dynamo/backend.py](https://github.com/tenstorrent/tt-torch/blob/f5e3955f/tt_torch/dynamo/backend.py) The executor classes in [tt_torch/dynamo/executor.py](https://github.com/tenstorrent/tt-torch/blob/f5e3955f/tt_torch/dynamo/executor.py) manage runtime execution.

Sources: [docs/src/overview.md 16-28](https://github.com/tenstorrent/tt-torch/blob/f5e3955f/docs/src/overview.md?plain=1#L16-L28)[docs/src/adding_models.md 33-66](https://github.com/tenstorrent/tt-torch/blob/f5e3955f/docs/src/adding_models.md?plain=1#L33-L66)

* * *

## Basic Usage Pattern

A minimal example demonstrating the core workflow:

`import torchimport tt_torch # Define a PyTorch modelclass SimpleModel(torch.nn.Module):    def forward(self, x, y):        return x + y # Compile for Tenstorrent hardwaremodel = SimpleModel()compiled_model = torch.compile(model, backend="tt-legacy") # Execute on devicex = torch.ones(5, 5)y = torch.ones(5, 5)result = compiled_model(x, y)`
This example uses the default `CompilerConfig` settings. For advanced configuration (op-by-op execution, intermediate verification, etc.), see [Basic Usage Examples](https://deepwiki.com/tenstorrent/tt-torch/2.2-basic-usage-examples) and [Configuration and Control](https://deepwiki.com/tenstorrent/tt-torch/3.1.3-configuration-and-control).

Sources: [docs/src/test.md 74-93](https://github.com/tenstorrent/tt-torch/blob/f5e3955f/docs/src/test.md?plain=1#L74-L93)[README.md 32-34](https://github.com/tenstorrent/tt-torch/blob/f5e3955f/README.md?plain=1#L32-L34)

* * *

## Verification and Next Steps

### Verifying Your Installation

After installation, verify the setup by running a test model:

**Option 1: Run the ResNet50 Demo**

`# Clone demo repositorygit clone https://github.com/tenstorrent/tt-forge.gitcd tt-forge/demos/tt-torch # Install demo dependenciespip install pillow tabulate requests # Run demopython resnet50_demo.py`
Expected output: Top-5 predictions with "cat" as the highest probability class.

**Option 2: Run Unit Tests**

`# For source buildspytest -svv tests/torch/test_basic.py # For wheel installationspip install pytestcurl -s https://raw.githubusercontent.com/tenstorrent/tt-torch/main/tests/torch/test_basic.py -o test_basic.pypytest -svv test_basic.py`
Sources: [docs/src/getting_started.md 92-98](https://github.com/tenstorrent/tt-torch/blob/f5e3955f/docs/src/getting_started.md?plain=1#L92-L98)[docs/src/test.md 22-32](https://github.com/tenstorrent/tt-torch/blob/f5e3955f/docs/src/test.md?plain=1#L22-L32)[docs/src/getting_started_build_from_source.md 193-200](https://github.com/tenstorrent/tt-torch/blob/f5e3955f/docs/src/getting_started_build_from_source.md?plain=1#L193-L200)

### Multi-Device Verification

For multi-chip systems, verify device mesh functionality:

`pytest -svv tests/torch/test_basic_async.pypytest -svv tests/torch/test_basic_multichip.py`
These tests exercise the `DeviceManager` and mesh configuration features described in [Device Management and Parallelism](https://deepwiki.com/tenstorrent/tt-torch/3.5-device-management-and-parallelism).

Sources: [docs/src/test.md 34-36](https://github.com/tenstorrent/tt-torch/blob/f5e3955f/docs/src/test.md?plain=1#L34-L36)

### Next Steps

After successful verification:

1.   **Learn basic usage patterns**: See [Basic Usage Examples](https://deepwiki.com/tenstorrent/tt-torch/2.2-basic-usage-examples) for `CompilerConfig` usage, `torch.compile` integration, and the ResNet50 demo walkthrough

2.   **Understand the architecture**: Read [Architecture](https://deepwiki.com/tenstorrent/tt-torch/3-architecture) to learn about the compilation pipeline, executor framework, and MLIR transformations

3.   **Explore model support**: Check [Model Support](https://deepwiki.com/tenstorrent/tt-torch/5-model-support) to see tested models and learn how to add new models

4.   **Configure compilation behavior**: See [Configuration and Control](https://deepwiki.com/tenstorrent/tt-torch/3.1.3-configuration-and-control) for environment variables and programmatic configuration options

5.   **Debug issues**: Use [Profiling and Debugging](https://deepwiki.com/tenstorrent/tt-torch/6.3-profiling-and-debugging) for troubleshooting techniques including op-by-op execution and intermediate verification

Sources: [docs/src/getting_started.md 106-108](https://github.com/tenstorrent/tt-torch/blob/f5e3955f/docs/src/getting_started.md?plain=1#L106-L108)[docs/src/getting_started_docker.md 118-121](https://github.com/tenstorrent/tt-torch/blob/f5e3955f/docs/src/getting_started_docker.md?plain=1#L118-L121)

* * *

## Common Installation Issues

| Issue | Symptom | Solution |
| --- | --- | --- |
| Device not visible | `tt-smi` shows no devices | Check driver installation and hugepages configuration |
| Permission denied | Cannot access `/dev/tenstorrent` | Add user to appropriate group or run with sudo |
| Import error | `ModuleNotFoundError: No module named 'tt_torch'` | Ensure virtual environment is activated |
| MPI not found | Runtime error during multi-device execution | Install OpenMPI using instructions in [Installation and Setup](https://deepwiki.com/tenstorrent/tt-torch/2.1-installation-and-setup) |
| Build toolchain timeout | CMake hangs during toolchain build | Allocate at least 32GB RAM or use pre-built wheel |

For additional troubleshooting, see the [TT-Torch Issues](https://github.com/tenstorrent/tt-torch/blob/f5e3955f/TT-Torch%20Issues) page.

Sources: [docs/src/getting_started.md 17-18](https://github.com/tenstorrent/tt-torch/blob/f5e3955f/docs/src/getting_started.md?plain=1#L17-L18)[docs/src/getting_started_build_from_source.md 16-17](https://github.com/tenstorrent/tt-torch/blob/f5e3955f/docs/src/getting_started_build_from_source.md?plain=1#L16-L17)

* * *

## Deprecation Notice

**Note:** tt-torch is deprecated in favor of [TT-XLA](https://github.com/tenstorrent/tt-torch/blob/f5e3955f/TT-XLA) For new projects, consider using TT-XLA, which provides similar PyTorch integration with enhanced features. Existing tt-torch users can continue using this project, but future development efforts are focused on TT-XLA.

Sources: [README.md 21](https://github.com/tenstorrent/tt-torch/blob/f5e3955f/README.md?plain=1#L21-L21)[docs/src/getting_started.md 3](https://github.com/tenstorrent/tt-torch/blob/f5e3955f/docs/src/getting_started.md?plain=1#L3-L3)[docs/src/overview.md 3](https://github.com/tenstorrent/tt-torch/blob/f5e3955f/docs/src/overview.md?plain=1#L3-L3)

This wiki is featured in the [repository](https://github.com/tenstorrent/tt-torch/blob/main/README.md)

Dismiss
Refresh this wiki

Enter email to refresh
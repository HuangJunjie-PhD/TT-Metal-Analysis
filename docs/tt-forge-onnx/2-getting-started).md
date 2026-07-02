---
project: tt-forge-onnx
github: https://github.com/tenstorrent/tt-forge-onnx
deepwiki: https://deepwiki.com/tenstorrent/tt-forge-onnx/2-getting-started)
---

# Getting Started

Relevant source files
*   [env/core_requirements.txt](https://github.com/tenstorrent/tt-forge-onnx/blob/e519819c/env/core_requirements.txt)
*   [env/dist_requirements.txt](https://github.com/tenstorrent/tt-forge-onnx/blob/e519819c/env/dist_requirements.txt)
*   [env/linux_requirements.txt](https://github.com/tenstorrent/tt-forge-onnx/blob/e519819c/env/linux_requirements.txt)

## Purpose and Scope

This document provides an introduction to the Forge compiler system and outlines the basic workflow for compiling and running models on Tenstorrent hardware. It covers fundamental concepts and prerequisites needed to begin using Forge.

For detailed installation instructions and environment configuration, see [Environment Setup](https://deepwiki.com/tenstorrent/tt-forge-onnx/2.1-environment-setup). For hands-on examples of compiling your first models, see [Quick Start Guide](https://deepwiki.com/tenstorrent/tt-forge-onnx/2.2-quick-start-guide). For comprehensive information about the compilation pipeline stages, see [Core Compilation System](https://deepwiki.com/tenstorrent/tt-forge-onnx/3-core-compilation-system).

## What is Forge?

Forge is a multi-framework deep learning compiler that translates models from PyTorch, ONNX, TensorFlow, JAX, and PaddlePaddle into optimized binaries for Tenstorrent AI accelerators. The system uses a multi-stage compilation pipeline that converts high-level framework operations into hardware-specific instructions.

### High-Level Architecture

**Sources:** Diagram 1 from high-level architecture overview

## Basic Workflow

The typical Forge workflow consists of three steps:

### Step 1: Load Model

Load your model in its native framework format. Forge supports models from:

*   **PyTorch**: `torch.nn.Module` instances
*   **ONNX**: `onnx.ModelProto` loaded via `onnx.load()`
*   **TensorFlow**: `tf.keras.Model` or concrete functions
*   **JAX**: Functions operating on `jax.Array`
*   **PaddlePaddle**: `paddle.nn.Layer` instances

### Step 2: Compile

The `forge.compile()` function transforms your model into an optimized binary through a multi-stage pipeline:

| Stage | Purpose | Key Components |
| --- | --- | --- |
| Frontend | Framework → TVM Relay IR | `tvm.relay.frontend.from_pytorch` |
| Translation | Relay IR → Python code | `tvm_to_python.py` |
| Graph Optimization | Build Forge graph, apply passes | `ForgeGraphModule` |
| MLIR Backend | TTIR → TTNN → Binary | `MLIRGenerator`, `PassManager` |

### Step 3: Verify

The `forge.verify()` function executes the compiled model and compares outputs against the original framework implementation using numerical similarity metrics (PCC, ATOL, RTOL).

**Sources:** Diagram 2 from compilation pipeline overview

## Prerequisites

### Python Environment

Forge requires **Python 3.12** and the following core dependencies:

| Category | Packages | Version Requirements |
| --- | --- | --- |
| Deep Learning Frameworks | `torch`, `tensorflow`, `jax` | torch 2.7.0, tensorflow 2.19.0, jax 0.7.1 |
| ONNX Support | `onnx`, `onnxruntime`, `tf2onnx` | onnx ≥1.15.0, onnxruntime ≥1.16.3 |
| Scientific Computing | `numpy`, `scipy`, `pandas` | numpy 1.26.4, scipy ≥1.8.0 |
| Model Utilities | `transformers`, `timm`, `diffusers` | transformers 4.52.4, timm 1.0.9 |
| Testing | `pytest`, `pytest-xdist` | pytest 6.2.4 |

**Sources:**[env/core_requirements.txt 1-50](https://github.com/tenstorrent/tt-forge-onnx/blob/e519819c/env/core_requirements.txt#L1-L50)[env/linux_requirements.txt 1-35](https://github.com/tenstorrent/tt-forge-onnx/blob/e519819c/env/linux_requirements.txt#L1-L35)

### System Requirements

**Sources:**[env/core_requirements.txt 1-3](https://github.com/tenstorrent/tt-forge-onnx/blob/e519819c/env/core_requirements.txt#L1-L3)[env/linux_requirements.txt 1-2](https://github.com/tenstorrent/tt-forge-onnx/blob/e519819c/env/linux_requirements.txt#L1-L2)

### Hardware Targets

Forge supports three Tenstorrent hardware targets:

| Device | Description | Use Case |
| --- | --- | --- |
| **n150** | Single-chip development board | Development, small models |
| **n300** | Dual-chip configuration | Medium-scale models |
| **p150** | Production-grade device | Large models, inference |

## Core Concepts

### Module Abstraction

Forge provides framework-agnostic module wrappers:

**Sources:** Diagram 4 from multi-framework support system

### Compilation Output

The `forge.compile()` function returns a `CompiledModel` object containing:

| Component | Description |
| --- | --- |
| **Binary** | Flatbuffer-encoded instructions for device |
| **ModelState** | Weight tensors and parameter mappings |
| **Metadata** | Input/output specifications, graph structure |
| **Runtime Interface** | Methods for forward/backward passes |

### Verification Metrics

Forge uses multiple metrics to validate numerical correctness:

| Metric | Full Name | Typical Threshold | Purpose |
| --- | --- | --- | --- |
| **PCC** | Pearson Correlation Coefficient | > 0.99 | Overall correlation |
| **ATOL** | Absolute Tolerance | < 1e-4 | Maximum absolute error |
| **RTOL** | Relative Tolerance | < 1e-3 | Maximum relative error |

**Sources:** Diagram 2 verification checkpoints, [env/linux_requirements.txt 6-8](https://github.com/tenstorrent/tt-forge-onnx/blob/e519819c/env/linux_requirements.txt#L6-L8) for pytest dependencies

## Installation Methods

Forge can be installed through two primary methods:

### Method 1: Development Installation

For contributors and developers who need to modify Forge source code:

### Method 2: Distribution Installation

For end-users who want to use Forge without modifying source:

**Sources:**[env/dist_requirements.txt 1-5](https://github.com/tenstorrent/tt-forge-onnx/blob/e519819c/env/dist_requirements.txt#L1-L5)[env/linux_requirements.txt 1-35](https://github.com/tenstorrent/tt-forge-onnx/blob/e519819c/env/linux_requirements.txt#L1-L35)

## Supported Operations

Forge provides extensive operation coverage across six categories:

| Category | Example Operations | Count |
| --- | --- | --- |
| **Eltwise Unary** | `abs`, `exp`, `relu`, `sigmoid` | 30+ |
| **Eltwise Binary** | `add`, `mul`, `div`, `sub` | 20+ |
| **Eltwise N-ary** | `concat`, `where`, `stack` | 10+ |
| **Tensor Manipulation** | `transpose`, `reshape`, `slice` | 25+ |
| **Neural Network** | `conv2d`, `matmul`, `batchnorm` | 20+ |
| **Reduction** | `sum`, `mean`, `max`, `min` | 10+ |

For details on implementing new operations, see [Adding New Operations](https://deepwiki.com/tenstorrent/tt-forge-onnx/8.3-adding-new-operations).

**Sources:** Diagram 6 operation categories

## Model Support

Forge has been validated against 100+ models across three domains:

### Vision Models (60+)

*   **Classification**: ResNet, MobileNet, EfficientNet, ViT
*   **Detection**: YOLO (v3/v5/v6/v8), RetinaNet
*   **Segmentation**: UNet, SegFormer, DeepLabV3

### Text/LLM Models (30+)

*   **Base Models**: Falcon, Llama3, Mistral, Phi, Gemma, Qwen
*   **Encoder Models**: BERT, RoBERTa, DistilBERT
*   **Training**: LoRA fine-tuning support

### Multimodal Models (10+)

*   **Vision-Language**: LLaVA, Phi3.5 Vision
*   **Image Generation**: Stable Diffusion
*   **CLIP**: Image-text matching

For comprehensive model documentation, see [Model Support](https://deepwiki.com/tenstorrent/tt-forge-onnx/7-model-support).

**Sources:** Diagram 1 testing infrastructure, Diagrams 3-4 model categories

## Compilation Configuration

Key configuration objects control compilation behavior:

For detailed configuration options, see [Compilation Configuration](https://deepwiki.com/tenstorrent/tt-forge-onnx/8.2-compilation-configuration).

**Sources:** Diagram 7 MLIR compilation pipeline

## Testing Your Setup

After installation, verify your environment:

For comprehensive testing procedures, see [Running Tests](https://deepwiki.com/tenstorrent/tt-forge-onnx/8.1-running-tests).

**Sources:**[env/linux_requirements.txt 6-11](https://github.com/tenstorrent/tt-forge-onnx/blob/e519819c/env/linux_requirements.txt#L6-L11) for pytest configuration

## Next Steps

Now that you understand the basics:

1.   **[Environment Setup](https://deepwiki.com/tenstorrent/tt-forge-onnx/2.1-environment-setup)** - Follow detailed installation instructions including dependencies, system configuration, and hardware setup
2.   **[Quick Start Guide](https://deepwiki.com/tenstorrent/tt-forge-onnx/2.2-quick-start-guide)** - Walk through complete examples of compiling and verifying models from PyTorch and ONNX
3.   **[Compilation Pipeline Overview](https://deepwiki.com/tenstorrent/tt-forge-onnx/3.1-compilation-pipeline-overview)** - Learn about the five-stage compilation process in depth
4.   **[Framework Integration](https://deepwiki.com/tenstorrent/tt-forge-onnx/4-framework-integration)** - Understand how your preferred framework integrates with Forge

## Key Files Reference

| File | Purpose |
| --- | --- |
| `env/core_requirements.txt` | Core Python dependencies for all platforms |
| `env/linux_requirements.txt` | Linux-specific dependencies including testing tools |
| `env/dist_requirements.txt` | Minimal dependencies for distribution builds |
| `forge/compile.py` | Main compilation entry point |
| `forge/verify.py` | Numerical verification utilities |

**Sources:**[env/core_requirements.txt 1-50](https://github.com/tenstorrent/tt-forge-onnx/blob/e519819c/env/core_requirements.txt#L1-L50)[env/linux_requirements.txt 1-35](https://github.com/tenstorrent/tt-forge-onnx/blob/e519819c/env/linux_requirements.txt#L1-L35)[env/dist_requirements.txt 1-5](https://github.com/tenstorrent/tt-forge-onnx/blob/e519819c/env/dist_requirements.txt#L1-L5)

This wiki is featured in the [repository](https://github.com/tenstorrent/tt-forge-onnx/blob/main/README.md)

Dismiss
Refresh this wiki

Enter email to refresh
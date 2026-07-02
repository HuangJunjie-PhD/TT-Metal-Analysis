---
project: tt-tutorial
github: https://github.com/tenstorrent/tt-tutorial
deepwiki: https://deepwiki.com/tenstorrent/tt-tutorial/3-high-level-frameworks)
---

# High-Level Frameworks

Relevant source files
*   [tt-forge/tt-torch/codegen_hf_tutorial.md](https://github.com/tenstorrent/tt-tutorial/blob/389c542c/tt-forge/tt-torch/codegen_hf_tutorial.md?plain=1)
*   [tt-forge/tt-torch/stable_diffusion_tutorial.md](https://github.com/tenstorrent/tt-tutorial/blob/389c542c/tt-forge/tt-torch/stable_diffusion_tutorial.md?plain=1)
*   [tt-nn/README.md](https://github.com/tenstorrent/tt-tutorial/blob/389c542c/tt-nn/README.md?plain=1)

This document covers the application-facing frameworks that provide high-level interfaces for running models on Tenstorrent hardware. These frameworks abstract the underlying hardware complexity and enable developers to deploy neural networks using familiar Python APIs and PyTorch integration patterns.

For information about low-level hardware programming, see [Low-Level Programming](https://deepwiki.com/tenstorrent/tt-tutorial/4-low-level-programming). For model development and optimization techniques, see [Model Development](https://deepwiki.com/tenstorrent/tt-tutorial/5-model-development).

## Overview

The high-level frameworks in the Tenstorrent ecosystem provide two primary interfaces for neural network development:

1.   **TT-Forge/TT-Torch** - PyTorch integration framework that enables compilation of PyTorch models through the `torch.compile` backend system
2.   **TT-NN** - Native neural network operations library providing both Python and C++ APIs for direct neural network programming

These frameworks sit above the TT-Metalium programming model and provide abstracted interfaces that handle device management, memory allocation, and operation scheduling automatically.

## Framework Architecture

The following diagram illustrates how the high-level frameworks integrate with the broader Tenstorrent software stack:

**High-Level Framework Integration Architecture**

Sources: [tt-nn/README.md 1-35](https://github.com/tenstorrent/tt-tutorial/blob/389c542c/tt-nn/README.md?plain=1#L1-L35)[tt-forge/tt-torch/stable_diffusion_tutorial.md 1-186](https://github.com/tenstorrent/tt-tutorial/blob/389c542c/tt-forge/tt-torch/stable_diffusion_tutorial.md?plain=1#L1-L186)[tt-forge/tt-torch/codegen_hf_tutorial.md 1-130](https://github.com/tenstorrent/tt-tutorial/blob/389c542c/tt-forge/tt-torch/codegen_hf_tutorial.md?plain=1#L1-L130)

## TT-Forge/TT-Torch Integration Components

The TT-Torch framework provides seamless integration with existing PyTorch workflows through several key components:

**TT-Torch Component Interface Map**

The primary integration points include:

| Component | Purpose | Code Location |
| --- | --- | --- |
| `CompilerConfig` | Configure TT-Torch compilation options | `tt_torch.tools.utils` |
| `BackendOptions` | Set backend execution parameters | `tt_torch.dynamo.backend` |
| `torch.compile` | PyTorch compilation entry point | PyTorch dynamo system |
| `torch._dynamo.reset()` | Reset compiler state between runs | PyTorch dynamo system |

Sources: [tt-forge/tt-torch/stable_diffusion_tutorial.md 73-94](https://github.com/tenstorrent/tt-tutorial/blob/389c542c/tt-forge/tt-torch/stable_diffusion_tutorial.md?plain=1#L73-L94)[tt-forge/tt-torch/codegen_hf_tutorial.md 49-92](https://github.com/tenstorrent/tt-tutorial/blob/389c542c/tt-forge/tt-torch/codegen_hf_tutorial.md?plain=1#L49-L92)

## TT-NN Neural Network Library Components

TT-NN provides a comprehensive neural network operations library with both tutorial materials and production APIs:

**TT-NN Library Structure**

The TT-NN framework provides structured learning materials organized into:

| Category | Content | Purpose |
| --- | --- | --- |
| White Paper | Core TT-NN architecture documentation | Fundamental concepts and design |
| Tech Reports | Architecture-specific implementation guides | Deep-dive technical analysis |
| Tutorial Notebooks | Interactive Python examples | Hands-on learning progression |

Sources: [tt-nn/README.md 14-34](https://github.com/tenstorrent/tt-tutorial/blob/389c542c/tt-nn/README.md?plain=1#L14-L34)

## Framework Integration Patterns

The high-level frameworks support several common integration patterns for deploying models on Tenstorrent hardware:

**Framework Integration Workflow**

### PyTorch Model Integration Pattern

For PyTorch models, the typical integration follows this pattern:

1.   **Model Preparation**: Convert models to `torch.bfloat16` for optimal performance
2.   **Configuration**: Set up `CompilerConfig` and `BackendOptions` for TT-Torch
3.   **Compilation**: Use `torch.compile(model, backend="tt", options=options)`
4.   **Execution**: Run inference with compiled model on Tenstorrent hardware

### TT-NN Direct Programming Pattern

For direct neural network programming:

1.   **API Selection**: Choose between Python or C++ TT-NN APIs
2.   **Operation Composition**: Build models using TT-NN operations library
3.   **Tutorial Progression**: Follow structured tutorials from basic tensors to complex models
4.   **Performance Optimization**: Apply techniques from tech reports for specific architectures

Sources: [tt-forge/tt-torch/stable_diffusion_tutorial.md 87-125](https://github.com/tenstorrent/tt-tutorial/blob/389c542c/tt-forge/tt-torch/stable_diffusion_tutorial.md?plain=1#L87-L125)[tt-forge/tt-torch/codegen_hf_tutorial.md 82-98](https://github.com/tenstorrent/tt-tutorial/blob/389c542c/tt-forge/tt-torch/codegen_hf_tutorial.md?plain=1#L82-L98)[tt-nn/README.md 27-34](https://github.com/tenstorrent/tt-tutorial/blob/389c542c/tt-nn/README.md?plain=1#L27-L34)

Dismiss
Refresh this wiki

Enter email to refresh
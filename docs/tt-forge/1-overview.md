---
project: tt-forge
github: https://github.com/tenstorrent/tt-forge
deepwiki: https://deepwiki.com/tenstorrent/tt-forge/1-overview
---

# Overview

Relevant source files
*   [.github/workflows/claude-code-review.yml](https://github.com/tenstorrent/tt-forge/blob/6f2d9645/.github/workflows/claude-code-review.yml)
*   [.github/workflows/claude.yml](https://github.com/tenstorrent/tt-forge/blob/6f2d9645/.github/workflows/claude.yml)
*   [CLAUDE.md](https://github.com/tenstorrent/tt-forge/blob/6f2d9645/CLAUDE.md?plain=1)
*   [CONTRIBUTING.md](https://github.com/tenstorrent/tt-forge/blob/6f2d9645/CONTRIBUTING.md?plain=1)
*   [README.md](https://github.com/tenstorrent/tt-forge/blob/6f2d9645/README.md?plain=1)
*   [demos/README.md](https://github.com/tenstorrent/tt-forge/blob/6f2d9645/demos/README.md?plain=1)
*   [docs/src/getting_started.md](https://github.com/tenstorrent/tt-forge/blob/6f2d9645/docs/src/getting_started.md?plain=1)

## Purpose and Scope

TT-Forge is Tenstorrent's open-source AI compiler stack, built on [TT-Metalium](https://github.com/tenstorrent/tt-forge/blob/6f2d9645/TT-Metalium) It serves as the central hub for integrating various AI/ML frameworks (PyTorch, JAX, ONNX) with Tenstorrent hardware. The repository coordinates multiple sub-projects including frontends (TT-XLA, TT-Forge-ONNX), the TT-MLIR compiler, a kernel DSL (TT-Lang), and a model library (TT-Forge-Models) to enable seamless execution of AI workloads on Tenstorrent hardware. [README.md 15-32](https://github.com/tenstorrent/tt-forge/blob/6f2d9645/README.md?plain=1#L15-L32)

This document provides a high-level overview of the TT-Forge repository structure, its major components, and how they interact.

**Sources:**[README.md 15-42](https://github.com/tenstorrent/tt-forge/blob/6f2d9645/README.md?plain=1#L15-L42)[CONTRIBUTING.md 1-12](https://github.com/tenstorrent/tt-forge/blob/6f2d9645/CONTRIBUTING.md?plain=1#L1-L12)

## Repository Role and Architecture

### Central Hub Repository

TT-Forge functions as a meta-repository that orchestrates the development, testing, and release of multiple interconnected projects. It integrates various compiler technologies to enable both model execution and custom kernel generation. [CONTRIBUTING.md 3-11](https://github.com/tenstorrent/tt-forge/blob/6f2d9645/CONTRIBUTING.md?plain=1#L3-L11)

| Sub-Project | Purpose | Key Links |
| --- | --- | --- |
| **TT-XLA** | Primary frontend for PyTorch and JAX using PJRT interface. | [README.md 27](https://github.com/tenstorrent/tt-forge/blob/6f2d9645/README.md?plain=1#L27-L27) |
| **TT-Forge-ONNX** | TVM-based frontend for ONNX, TensorFlow, and PaddlePaddle. | [README.md 28](https://github.com/tenstorrent/tt-forge/blob/6f2d9645/README.md?plain=1#L28-L28) |
| **TT-MLIR** | Core MLIR-based compiler defining TTIR, TTNN, and TTKernel dialects. | [README.md 29](https://github.com/tenstorrent/tt-forge/blob/6f2d9645/README.md?plain=1#L29-L29) |
| **TT-Lang** | Python DSL for high-performance custom kernels. | [README.md 30](https://github.com/tenstorrent/tt-forge/blob/6f2d9645/README.md?plain=1#L30-L30) |
| **TT-Blacksmith** | Optimized training recipes and experiments. | [README.md 31](https://github.com/tenstorrent/tt-forge/blob/6f2d9645/README.md?plain=1#L31-L31) |
| **TT-Forge-Models** | Collection of 800+ validated model variants. | [README.md 32](https://github.com/tenstorrent/tt-forge/blob/6f2d9645/README.md?plain=1#L32-L32) |

**Sources:**[README.md 23-32](https://github.com/tenstorrent/tt-forge/blob/6f2d9645/README.md?plain=1#L23-L32)[CONTRIBUTING.md 3-11](https://github.com/tenstorrent/tt-forge/blob/6f2d9645/CONTRIBUTING.md?plain=1#L3-L11)

### Code Entity Space Mapping

The following diagram maps high-level system components to their specific code entities and directory structures within the TT-Forge ecosystem.

**Sources:**[README.md 23-32](https://github.com/tenstorrent/tt-forge/blob/6f2d9645/README.md?plain=1#L23-L32)[CLAUDE.md 15-55](https://github.com/tenstorrent/tt-forge/blob/6f2d9645/CLAUDE.md?plain=1#L15-L55)[demos/README.md 1-21](https://github.com/tenstorrent/tt-forge/blob/6f2d9645/demos/README.md?plain=1#L1-L21)

## Software Stack Overview

### Multi-Layer Architecture

TT-Forge implements a compiler stack architecture that lowers high-level framework code into hardware-specific instructions:

**Sources:**[CLAUDE.md 64-84](https://github.com/tenstorrent/tt-forge/blob/6f2d9645/CLAUDE.md?plain=1#L64-L84)[README.md 27-30](https://github.com/tenstorrent/tt-forge/blob/6f2d9645/README.md?plain=1#L27-L30)[demos/README.md 7-17](https://github.com/tenstorrent/tt-forge/blob/6f2d9645/demos/README.md?plain=1#L7-L17)

### Component Roles

*   **Frontend Layer**: Ingests models. TT-XLA uses PJRT to compile models into StableHLO graphs for JAX and PyTorch. TT-Forge-ONNX uses TVM to optimize graphs for ONNX and TensorFlow. [CLAUDE.md 70-72](https://github.com/tenstorrent/tt-forge/blob/6f2d9645/CLAUDE.md?plain=1#L70-L72)
*   **TT-MLIR Compiler**: Applies optimization passes such as op fusion, sharding, and layout transformation. It lowers code through various dialects: TTIR (Common IR), TTNN (entry to TTNN library), and TTMetal (direct kernel access). [CLAUDE.md 74-79](https://github.com/tenstorrent/tt-forge/blob/6f2d9645/CLAUDE.md?plain=1#L74-L79)
*   **TT-Metalium Layer**: Consists of the TTNN library for neural network operations and the TTMetal SDK for low-level programming. [CLAUDE.md 81](https://github.com/tenstorrent/tt-forge/blob/6f2d9645/CLAUDE.md?plain=1#L81-L81)
*   **Hardware Layer**: Supports Wormhole (N150, N300) and Blackhole (P150B) architectures. [CLAUDE.md 83](https://github.com/tenstorrent/tt-forge/blob/6f2d9645/CLAUDE.md?plain=1#L83-L83)

## AI-Assisted Development

TT-Forge integrates advanced AI automation via Claude Code to assist in model onboarding and code review.

*   **Claude Code Action**: A generic action (`.github/workflows/claude.yml`) triggered by manual prompts or GitHub events (issues, PR comments) to perform tasks using the `anthropic/claude-code-action`. [.github/workflows/claude.yml 1-34](https://github.com/tenstorrent/tt-forge/blob/6f2d9645/.github/workflows/claude.yml#L1-L34)
*   **Authorization**: Access is restricted to organization members, owners, or collaborators. [.github/workflows/claude.yml 64-71](https://github.com/tenstorrent/tt-forge/blob/6f2d9645/.github/workflows/claude.yml#L64-L71)
*   **PR Review**: Automated analysis of PRs for bugs, correctness, and performance using `claude-review`. [.github/workflows/claude-code-review.yml 1-41](https://github.com/tenstorrent/tt-forge/blob/6f2d9645/.github/workflows/claude-code-review.yml#L1-L41)
*   **Tooling**: The agent has access to the GitHub CLI (`gh`) to create issues, view PRs, and monitor CI runs. [.github/workflows/claude.yml 109-123](https://github.com/tenstorrent/tt-forge/blob/6f2d9645/.github/workflows/claude.yml#L109-L123)

**Sources:**[.github/workflows/claude.yml 1-128](https://github.com/tenstorrent/tt-forge/blob/6f2d9645/.github/workflows/claude.yml#L1-L128)[.github/workflows/claude-code-review.yml 1-48](https://github.com/tenstorrent/tt-forge/blob/6f2d9645/.github/workflows/claude-code-review.yml#L1-L48)

## Testing and Validation Infrastructure

### Execution Model

The repository supports multiple testing tiers to ensure stability and performance:

1.   **Basic Tests**: Quick validation for frontends (e.g., `python basic_tests/tt-xla/demo_test.py`). [CLAUDE.md 19-23](https://github.com/tenstorrent/tt-forge/blob/6f2d9645/CLAUDE.md?plain=1#L19-L23)
2.   **Demos**: Real-world examples like ResNet-50 showing end-to-end execution. [README.md 37-71](https://github.com/tenstorrent/tt-forge/blob/6f2d9645/README.md?plain=1#L37-L71)[docs/src/getting_started.md 60-70](https://github.com/tenstorrent/tt-forge/blob/6f2d9645/docs/src/getting_started.md?plain=1#L60-L70)
3.   **Benchmarks**: Comprehensive performance measurement for LLMs and Vision models using `pytest`. [CLAUDE.md 25-46](https://github.com/tenstorrent/tt-forge/blob/6f2d9645/CLAUDE.md?plain=1#L25-L46)
4.   **Performance Workflow**: CI-based performance tracking triggered via `gh workflow run "Performance benchmark"`. [CLAUDE.md 50-55](https://github.com/tenstorrent/tt-forge/blob/6f2d9645/CLAUDE.md?plain=1#L50-L55)

### Hardware Management

For developers working locally, hardware state can be managed via the `tt-smi` tool. If a device hangs (e.g., "Timeout waiting for Ethernet"), it can be reset using `tt-smi --reset 0`. [CLAUDE.md 57-61](https://github.com/tenstorrent/tt-forge/blob/6f2d9645/CLAUDE.md?plain=1#L57-L61)

**Sources:**[CLAUDE.md 15-61](https://github.com/tenstorrent/tt-forge/blob/6f2d9645/CLAUDE.md?plain=1#L15-L61)[docs/src/getting_started.md 40-70](https://github.com/tenstorrent/tt-forge/blob/6f2d9645/docs/src/getting_started.md?plain=1#L40-L70)

Dismiss
Refresh this wiki

Enter email to refresh
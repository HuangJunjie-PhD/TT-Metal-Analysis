---
project: tt-tutorial
github: https://github.com/tenstorrent/tt-tutorial
deepwiki: https://deepwiki.com/tenstorrent/tt-tutorial/2-tenstorrent-software-stack)
---

# Tenstorrent Software Stack

Relevant source files
*   [README.md](https://github.com/tenstorrent/tt-tutorial/blob/389c542c/README.md?plain=1)
*   [tt-metalium/README.md](https://github.com/tenstorrent/tt-tutorial/blob/389c542c/tt-metalium/README.md?plain=1)
*   [tt-nn/README.md](https://github.com/tenstorrent/tt-tutorial/blob/389c542c/tt-nn/README.md?plain=1)

## Purpose and Scope

This document provides a high-level overview of the complete Tenstorrent software ecosystem and how the tutorial materials in this repository map to different layers of the stack. It covers the architectural relationships between components from low-level hardware programming through high-level machine learning frameworks.

For detailed information about specific components, see [High-Level Frameworks](https://deepwiki.com/tenstorrent/tt-tutorial/3-high-level-frameworks), [Low-Level Programming](https://deepwiki.com/tenstorrent/tt-tutorial/4-low-level-programming), [Model Development](https://deepwiki.com/tenstorrent/tt-tutorial/5-model-development), and [Development Tools](https://deepwiki.com/tenstorrent/tt-tutorial/6-development-tools).

## Software Stack Architecture

The Tenstorrent software stack consists of multiple layers that provide different levels of abstraction for developing and deploying machine learning models on Tenstorrent hardware.

### Stack Layers Diagram

Sources: [README.md 13-23](https://github.com/tenstorrent/tt-tutorial/blob/389c542c/README.md?plain=1#L13-L23)[tt-metalium/README.md 11-12](https://github.com/tenstorrent/tt-tutorial/blob/389c542c/tt-metalium/README.md?plain=1#L11-L12)[tt-nn/README.md 12](https://github.com/tenstorrent/tt-tutorial/blob/389c542c/tt-nn/README.md?plain=1#L12-L12)

## Component Relationships and Dependencies

### Framework Integration Flow

Sources: [tt-nn/README.md 12](https://github.com/tenstorrent/tt-tutorial/blob/389c542c/tt-nn/README.md?plain=1#L12-L12)[tt-metalium/README.md 11-12](https://github.com/tenstorrent/tt-tutorial/blob/389c542c/tt-metalium/README.md?plain=1#L11-L12)

## Tutorial Material Mapping

The tutorial materials in this repository correspond to different layers of the software stack, providing learning paths from beginner to advanced topics.

### Tutorial Coverage by Stack Layer

| Stack Layer | Tutorial Materials | Key Components |
| --- | --- | --- |
| **Application Layer** | [Models](https://deepwiki.com/tenstorrent/tt-tutorial/5.1-model-tutorials-and-bring-up) | Model bring-up, optimization guides |
| **High-Level Frameworks** | [TT-Forge/TT-Torch](https://deepwiki.com/tenstorrent/tt-tutorial/3.1-tt-forge-and-tt-torch-(pytorch-integration)), [TT-NN](https://deepwiki.com/tenstorrent/tt-tutorial/3.2-tt-nn-neural-network-library) | `torch.compile` backend, `ttnn` operations |
| **Low-Level Programming** | [TT-Metalium](https://deepwiki.com/tenstorrent/tt-tutorial/4.1-tt-metalium-programming-model), [Operations](https://deepwiki.com/tenstorrent/tt-tutorial/4.2-operations-and-primitives) | Kernel development, primitive operations |
| **Development Tools** | [Model Explorer](https://deepwiki.com/tenstorrent/tt-tutorial/6.1-model-explorer), [TT-NN Visualizer](https://deepwiki.com/tenstorrent/tt-tutorial/6.2-tt-nn-visualizer) | Graph visualization, execution analysis |

Sources: [README.md 15-22](https://github.com/tenstorrent/tt-tutorial/blob/389c542c/README.md?plain=1#L15-L22)

### Learning Progression Path

Sources: [tt-metalium/README.md 15-22](https://github.com/tenstorrent/tt-tutorial/blob/389c542c/tt-metalium/README.md?plain=1#L15-L22)[tt-nn/README.md 14-34](https://github.com/tenstorrent/tt-tutorial/blob/389c542c/tt-nn/README.md?plain=1#L14-L34)[README.md 17-22](https://github.com/tenstorrent/tt-tutorial/blob/389c542c/README.md?plain=1#L17-L22)

## Key Programming Interfaces

### Primary APIs by Layer

| Layer | Primary Interface | Description |
| --- | --- | --- |
| **Application** | `torch.compile(backend="tt")` | PyTorch model compilation |
| **High-Level** | `ttnn.*` operations | Neural network operations library |
| **Low-Level** | `tt_metal` runtime | Hardware programming model |
| **Hardware** | Device APIs | Direct hardware access |

### File Structure Mapping

Sources: [README.md 17-22](https://github.com/tenstorrent/tt-tutorial/blob/389c542c/README.md?plain=1#L17-L22)

The software stack architecture enables developers to work at their preferred level of abstraction while maintaining access to lower-level optimizations when needed. The tutorial materials provide comprehensive coverage across all layers, supporting both learning progression and practical implementation.

Dismiss
Refresh this wiki

Enter email to refresh
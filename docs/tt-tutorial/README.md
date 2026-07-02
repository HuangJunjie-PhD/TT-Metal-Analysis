---
project: tt-tutorial
github: https://github.com/tenstorrent/tt-tutorial
deepwiki: https://deepwiki.com/tenstorrent/tt-tutorial
---

# Overview

Relevant source files
*   [README.md](https://github.com/tenstorrent/tt-tutorial/blob/389c542c/README.md?plain=1)

## Purpose and Scope

The TT-Tutorial repository serves as Tenstorrent's comprehensive educational hub, providing introductory and advanced material covering the entire software stack from low-level hardware programming to high-level PyTorch integration. This documentation covers the repository structure, learning pathways, and how tutorial materials map to the complete Tenstorrent software ecosystem.

For detailed information about specific components, see [Tenstorrent Software Stack](https://deepwiki.com/tenstorrent/tt-tutorial/2-tenstorrent-software-stack), [High-Level Frameworks](https://deepwiki.com/tenstorrent/tt-tutorial/3-high-level-frameworks), [Low-Level Programming](https://deepwiki.com/tenstorrent/tt-tutorial/4-low-level-programming), [Model Development](https://deepwiki.com/tenstorrent/tt-tutorial/5-model-development), and [Development Tools](https://deepwiki.com/tenstorrent/tt-tutorial/6-development-tools).

## Repository Architecture

**TT-Tutorial Repository Structure**

The repository follows a modular structure where each directory contains self-contained tutorial materials targeting different aspects of the Tenstorrent development stack. The central `README.md` provides navigation links to six main tutorial modules, each addressing specific development needs and skill levels.

Sources: [README.md 17-22](https://github.com/tenstorrent/tt-tutorial/blob/389c542c/README.md?plain=1#L17-L22)

## Software Stack Coverage

**Tutorial Material Mapping to Tenstorrent Stack**

This diagram illustrates how the six tutorial modules in the repository correspond to different layers of the Tenstorrent software stack. Each tutorial directory targets specific development scenarios, from high-level model deployment to low-level kernel optimization.

Sources: [README.md 17-22](https://github.com/tenstorrent/tt-tutorial/blob/389c542c/README.md?plain=1#L17-L22)

## Learning Pathways

The tutorial materials support three primary learning pathways based on developer background and objectives:

| Pathway | Starting Point | Core Modules | Target Outcome |
| --- | --- | --- | --- |
| **Hardware Programming** | `tt-metalium/` | `operations/` → `tt-nn/` | Kernel development and optimization |
| **Model Development** | `models/` | `tt-nn/` → `model-explorer/` | Model bring-up and deployment |
| **Analysis & Debugging** | `tt-nn visualizer/` | `model-explorer/` → `operations/` | Performance optimization and troubleshooting |

### Recommended Learning Sequence

**Progressive Tutorial Flow**

The learning sequence progresses from foundational concepts in hardware programming through neural network operations to advanced model development and analysis tools. Cross-connections enable developers to access visualization and debugging tools at appropriate points in their learning journey.

Sources: [README.md 13-22](https://github.com/tenstorrent/tt-tutorial/blob/389c542c/README.md?plain=1#L13-L22)

## Getting Started

### Repository Navigation

The central `README.md` file serves as the primary navigation hub, providing direct links to all tutorial modules. Each linked directory contains comprehensive tutorial materials, code examples, and documentation specific to that component of the Tenstorrent software stack.

### Tutorial Module Access

| Module | Repository Link | Primary Focus |
| --- | --- | --- |
| `model-explorer/` | [Model-Explorer](https://github.com/tenstorrent/tt-tutorial/blob/389c542c/Model-Explorer) | Graph visualization and analysis |
| `models/` | [Models](https://github.com/tenstorrent/tt-tutorial/blob/389c542c/Models) | Model bring-up and optimization |
| `operations/` | [Operations](https://github.com/tenstorrent/tt-tutorial/blob/389c542c/Operations) | Fundamental operations and testing |
| `tt-metalium/` | [TT-Metalium](https://github.com/tenstorrent/tt-tutorial/blob/389c542c/TT-Metalium) | Low-level hardware programming |
| `tt-nn visualizer/` | [TT-NN Visualizer](https://github.com/tenstorrent/tt-tutorial/blob/389c542c/TT-NN%20Visualizer) | Execution analysis and debugging |
| `tt-nn/` | [TT-NN](https://github.com/tenstorrent/tt-tutorial/blob/389c542c/TT-NN) | Neural network library tutorials |

Each tutorial module is designed to be self-contained while providing clear integration points with other components of the Tenstorrent development ecosystem.

Sources: [README.md 17-22](https://github.com/tenstorrent/tt-tutorial/blob/389c542c/README.md?plain=1#L17-L22)

Dismiss
Refresh this wiki

Enter email to refresh
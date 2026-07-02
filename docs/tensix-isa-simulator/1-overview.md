---
project: tensix-isa-simulator
github: https://github.com/tenstorrent/tensix-isa-simulator
deepwiki: https://deepwiki.com/tenstorrent/tensix-isa-simulator/1-overview
---

# Overview

Relevant source files
*   [README.md](https://github.com/tenstorrent/tensix-isa-simulator/blob/afb83529/README.md?plain=1)

The Tensix ISA Simulator is a software tool that simulates the Tensix Instruction Set Architecture. This document provides a technical introduction to the simulator, explaining its purpose, components, and architecture.

For detailed information on specific aspects of the system, please refer to the following sections:

*   [System Architecture](https://deepwiki.com/tenstorrent/tensix-isa-simulator/2-system-architecture)
*   [LLK System](https://deepwiki.com/tenstorrent/tensix-isa-simulator/3-llk-system)
*   [r37 Architecture](https://deepwiki.com/tenstorrent/tensix-isa-simulator/4-r37-architecture)
*   [Supporting Libraries](https://deepwiki.com/tenstorrent/tensix-isa-simulator/5-supporting-libraries)
*   [Demo Applications](https://deepwiki.com/tenstorrent/tensix-isa-simulator/6-demo-applications)

Sources: [README.md 1-14](https://github.com/tenstorrent/tensix-isa-simulator/blob/afb83529/README.md?plain=1#L1-L14)

## Purpose and Goals

The Tensix ISA Simulator serves two primary technical objectives:

1.   **Development Platform**: Provides a foundation for designing, debugging, and testing Tensix low-level kernels (LLK) on conventional CPUs without requiring Tenstorrent hardware.

2.   **Formal Specification**: Serves as a verifiable formal functional specification of the Tensix ISA, which can be used as a basis for detailed architecture documentation.

The simulator is currently a work in progress and supports the Wormhole B0 architecture.

Sources: [README.md 5-14](https://github.com/tenstorrent/tensix-isa-simulator/blob/afb83529/README.md?plain=1#L5-L14)

## System Components

The following diagram illustrates the main components of the Tensix ISA Simulator:

Title: Tensix ISA Simulator Component Architecture

Sources: From diagrams provided in the prompt

## Component Dependencies

The following diagram shows the dependencies between the various library components of the system:

Title: Library Dependencies

Sources: From diagrams provided in the prompt

## Main Subsystems

### Low-Level Kernel (LLK) System

The LLK System provides the foundation for simulating Tensix low-level kernels. It consists of:

| Component | Purpose |
| --- | --- |
| llk_api.a | API interfaces for the Low-Level Kernel system |
| llk_basic.a | Basic implementation of the Low-Level Kernel |
| llk_ref.a | Reference implementation for verification |
| llk_compute.a | Compute-focused implementation with optimizations |

For detailed documentation, see [LLK System](https://deepwiki.com/tenstorrent/tensix-isa-simulator/3-llk-system).

Sources: From diagrams provided in the prompt

### r37 System

The r37 System focuses on simulating the r37 architecture:

| Component | Purpose |
| --- | --- |
| r37_iss.a | Instruction Set Simulator for r37 |
| r37_llk.a | r37-specific Low-Level Kernel implementation |
| r37_ckernel.a | Compute kernel for r37 architecture |
| r37_compute.a | Compute library for r37 architecture |

For detailed documentation, see [r37 Architecture](https://deepwiki.com/tenstorrent/tensix-isa-simulator/4-r37-architecture).

Sources: From diagrams provided in the prompt

### Supporting Libraries

Core libraries providing fundamental functionality:

| Library | Purpose |
| --- | --- |
| core.a | Core functionality used across components |
| schedule.a | Task scheduling functionality |
| sfpi.a | Serial Flash Programming Interface |
| minicoro.h | Stackful coroutines implementation |

For detailed documentation, see [Supporting Libraries](https://deepwiki.com/tenstorrent/tensix-isa-simulator/5-supporting-libraries).

Sources: From diagrams provided in the prompt

### Demo Applications

Example applications demonstrating the simulator functionality:

| Application | Purpose |
| --- | --- |
| demo_llk_basic | Demonstrates basic LLK implementation |
| demo_llk_r37 | Demonstrates r37-specific implementation |
| demo_llk_ref | Demonstrates reference LLK implementation |
| demo_sfpi_gen | Demonstrates SFPI functionality |

For detailed documentation, see [Demo Applications](https://deepwiki.com/tenstorrent/tensix-isa-simulator/6-demo-applications).

Sources: From diagrams provided in the prompt

## Build System Organization

The build system organizes the compilation of the various components of the Tensix ISA Simulator:

Title: Build System Organization

Sources: From diagrams provided in the prompt

## System Requirements

The Tensix ISA Simulator has the following technical requirements:

*   Operating System: Linux (tested on Ubuntu 20.04)
*   Currently supports the Wormhole B0 architecture

Sources: [README.md 16-19](https://github.com/tenstorrent/tensix-isa-simulator/blob/afb83529/README.md?plain=1#L16-L19)

Dismiss
Refresh this wiki

Enter email to refresh
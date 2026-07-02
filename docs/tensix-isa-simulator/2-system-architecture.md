---
project: tensix-isa-simulator
github: https://github.com/tenstorrent/tensix-isa-simulator
deepwiki: https://deepwiki.com/tenstorrent/tensix-isa-simulator/2-system-architecture
---

# System Architecture

Relevant source files
*   [README.md](https://github.com/tenstorrent/tensix-isa-simulator/blob/afb83529/README.md?plain=1)
*   [tensix/whb0/prj/build_llk_api.sh](https://github.com/tenstorrent/tensix-isa-simulator/blob/afb83529/tensix/whb0/prj/build_llk_api.sh)
*   [tensix/whb0/prj/build_r37_iss.sh](https://github.com/tenstorrent/tensix-isa-simulator/blob/afb83529/tensix/whb0/prj/build_r37_iss.sh)

## Purpose and Scope

This document provides a high-level overview of the Tensix ISA Simulator architecture, explaining the major system components and how they relate to each other. It serves as a guide to understanding the overall structure of the codebase without delving into the implementation details of individual components.

For specific details about the LLK (Low-Level Kernel) system, see [LLK System](https://deepwiki.com/tenstorrent/tensix-isa-simulator/3-llk-system). Information about the r37 architecture implementation can be found in [r37 Architecture](https://deepwiki.com/tenstorrent/tensix-isa-simulator/4-r37-architecture). For information on supporting libraries, see [Supporting Libraries](https://deepwiki.com/tenstorrent/tensix-isa-simulator/5-supporting-libraries).

Sources: [README.md 1-20](https://github.com/tenstorrent/tensix-isa-simulator/blob/afb83529/README.md?plain=1#L1-L20)

## High-Level System Overview

The Tensix ISA Simulator is organized into four major subsystems:

1.   **LLK (Low-Level Kernel) System** - Provides infrastructure for designing, debugging, and testing Tensix low-level kernels
2.   **r37 System** - Implements the r37 architecture components including the Instruction Set Simulator
3.   **Supporting Libraries** - Core functionality shared across the system
4.   **Demo Applications** - Showcases the capabilities of the various components

### System Component Hierarchy

Sources: [README.md 1-20](https://github.com/tenstorrent/tensix-isa-simulator/blob/afb83529/README.md?plain=1#L1-L20)

## Component Dependencies

The following diagram illustrates the dependencies between the major components of the system:

Sources: [tensix/whb0/prj/build_llk_api.sh 1-18](https://github.com/tenstorrent/tensix-isa-simulator/blob/afb83529/tensix/whb0/prj/build_llk_api.sh#L1-L18)[tensix/whb0/prj/build_r37_iss.sh 1-25](https://github.com/tenstorrent/tensix-isa-simulator/blob/afb83529/tensix/whb0/prj/build_r37_iss.sh#L1-L25)

## Build System Organization

The build system is organized with separate build scripts for each component. Each script compiles the corresponding component and packages it into a static library (.a file).

Sources: [tensix/whb0/prj/build_llk_api.sh 1-18](https://github.com/tenstorrent/tensix-isa-simulator/blob/afb83529/tensix/whb0/prj/build_llk_api.sh#L1-L18)[tensix/whb0/prj/build_r37_iss.sh 1-25](https://github.com/tenstorrent/tensix-isa-simulator/blob/afb83529/tensix/whb0/prj/build_r37_iss.sh#L1-L25)

### Build Script Structure

The build scripts follow a common pattern:

1.   Define source and library directories
2.   Compile source files with appropriate include paths
3.   Package compiled objects into a static library
4.   Clean up temporary object files

For example, the LLK API build script (`build_llk_api.sh`) compiles all source files in the `llk/api` directory and packages them into `llk_api.a`:

```
g++ -c -std=c++17 -O3 -I $SRC -I $SRC/llk/api $SRC/llk/api/*.cpp
ar rsc $LIB/llk_api.a *.o
```

Similarly, the r37 ISS build script (`build_r37_iss.sh`) compiles all source files in the `r37/iss` directory, with additional include paths for related components:

```
g++ -c -std=c++17 -O3 \
    -I $SRC \
    -I $SRC/r37/iss \
    -I $SRC/r37/common/inc \
    -I $SRC/r37/llk_lib \
    -I $SRC/r37/etc \
    -I $SRC/r37/etc/defines \
    $SRC/r37/iss/*.cpp
ar rsc $LIB/r37_iss.a *.o
```

Sources: [tensix/whb0/prj/build_llk_api.sh 1-18](https://github.com/tenstorrent/tensix-isa-simulator/blob/afb83529/tensix/whb0/prj/build_llk_api.sh#L1-L18)[tensix/whb0/prj/build_r37_iss.sh 1-25](https://github.com/tenstorrent/tensix-isa-simulator/blob/afb83529/tensix/whb0/prj/build_r37_iss.sh#L1-L25)

## Key System Components

### LLK System

The LLK (Low-Level Kernel) system provides infrastructure for designing, debugging, and testing Tensix low-level kernels. It consists of four main components:

1.   **LLK API**: Defines the interfaces for interacting with LLK implementations
2.   **LLK Basic**: Provides a simple implementation of the LLK API
3.   **LLK Reference**: Provides a reference implementation of the LLK API
4.   **LLK Compute**: Provides an optimized implementation focused on compute operations

For more details on the LLK system, see [LLK System](https://deepwiki.com/tenstorrent/tensix-isa-simulator/3-llk-system).

### r37 System

The r37 system implements the architecture components specific to the r37 architecture. It consists of:

1.   **r37 ISS**: The Instruction Set Simulator for the r37 architecture
2.   **r37 LLK**: An LLK implementation specific to the r37 architecture
3.   **r37 CKernel**: The Compute Kernel implementation for r37
4.   **r37 Compute**: A compute library for r37

For more details on the r37 system, see [r37 Architecture](https://deepwiki.com/tenstorrent/tensix-isa-simulator/4-r37-architecture).

### Supporting Libraries

The supporting libraries provide core functionality used across the system:

1.   **Core**: Fundamental functionality used by multiple components
2.   **Schedule**: Task scheduling capabilities
3.   **SFPI**: Serial Flash Programming Interface for flash memory interactions
4.   **Minicoro**: Coroutine library for concurrent programming

For more details on these libraries, see [Supporting Libraries](https://deepwiki.com/tenstorrent/tensix-isa-simulator/5-supporting-libraries).

### Demo Applications

The demo applications showcase the capabilities of the various system components:

1.   **demo_llk_basic**: Demonstrates the basic LLK implementation
2.   **demo_llk_r37**: Demonstrates LLK functionality on the r37 architecture
3.   **demo_llk_ref**: Demonstrates the reference LLK implementation
4.   **demo_sfpi_gen**: Demonstrates the SFPI library's capabilities

For more details on the demo applications, see [Demo Applications](https://deepwiki.com/tenstorrent/tensix-isa-simulator/6-demo-applications).

## System Interaction Patterns

The following diagram illustrates how the major components interact at runtime:

## Architecture Implementation Details

The Tensix ISA Simulator currently supports the Wormhole B0 architecture, as noted in the README. All the components described are implemented with this architecture in mind. The simulator serves both as a development tool for low-level kernels and as a formal functional specification of the Tensix ISA.

Sources: [README.md 1-20](https://github.com/tenstorrent/tensix-isa-simulator/blob/afb83529/README.md?plain=1#L1-L20)

## Current Limitations

As stated in the README, the simulator should be considered a work in progress. It has been tested on Ubuntu 20.04 and requires a Linux environment to run.

Sources: [README.md 1-20](https://github.com/tenstorrent/tensix-isa-simulator/blob/afb83529/README.md?plain=1#L1-L20)

Dismiss
Refresh this wiki

Enter email to refresh
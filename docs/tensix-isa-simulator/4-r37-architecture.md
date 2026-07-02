---
project: tensix-isa-simulator
github: https://github.com/tenstorrent/tensix-isa-simulator
deepwiki: https://deepwiki.com/tenstorrent/tensix-isa-simulator/4-r37-architecture
---

# r37 Architecture

Relevant source files
*   [tensix/whb0/prj/build_r37_iss.sh](https://github.com/tenstorrent/tensix-isa-simulator/blob/afb83529/tensix/whb0/prj/build_r37_iss.sh)
*   [tensix/whb0/prj/build_r37_llk.sh](https://github.com/tenstorrent/tensix-isa-simulator/blob/afb83529/tensix/whb0/prj/build_r37_llk.sh)

## Introduction

The r37 architecture is a key component within the Tensix ISA Simulator. This page provides a technical overview of the r37 architecture, its components, and how they integrate with the broader system.

For details on specific implementations, see [r37 Instruction Set Simulator](https://deepwiki.com/tenstorrent/tensix-isa-simulator/4.1-r37-instruction-set-simulator), [r37 LLK Implementation](https://deepwiki.com/tenstorrent/tensix-isa-simulator/4.2-r37-llk-implementation), and [r37 Compute Components](https://deepwiki.com/tenstorrent/tensix-isa-simulator/4.3-r37-compute-components).

## Architecture Components

The r37 architecture consists of several primary components that work together to provide a complete simulation environment:

1.   **Instruction Set Simulator (ISS)**: Core component that simulates the execution of r37 instructions
2.   **LLK Implementation**: r37-specific implementation of the Low-Level Kernel interface
3.   **Compute Components**: 
    *   **Compute Kernel**: Provides specialized computational functionality
    *   **Compute Library**: Higher-level compute abstractions and utilities

### Component Hierarchy

The r37 Architecture Components Diagram

Sources: [tensix/whb0/prj/build_r37_iss.sh](https://github.com/tenstorrent/tensix-isa-simulator/blob/afb83529/tensix/whb0/prj/build_r37_iss.sh)[tensix/whb0/prj/build_r37_llk.sh](https://github.com/tenstorrent/tensix-isa-simulator/blob/afb83529/tensix/whb0/prj/build_r37_llk.sh)

## Source Code Organization

The r37 architecture implementation is organized across several directories in the codebase:

| Directory | Purpose |
| --- | --- |
| `src/r37/iss` | Instruction Set Simulator implementation |
| `src/r37/llk_lib` | r37-specific LLK implementation |
| `src/r37/common/inc` | Common include files shared across r37 components |
| `src/r37/etc` | Configuration files |
| `src/r37/etc/defines` | Architecture-specific constants and definitions |

Sources: [tensix/whb0/prj/build_r37_iss.sh 9-15](https://github.com/tenstorrent/tensix-isa-simulator/blob/afb83529/tensix/whb0/prj/build_r37_iss.sh#L9-L15)[tensix/whb0/prj/build_r37_llk.sh 9-15](https://github.com/tenstorrent/tensix-isa-simulator/blob/afb83529/tensix/whb0/prj/build_r37_llk.sh#L9-L15)

## Runtime Flow

The r37 Runtime Flow Diagram

Sources: [tensix/whb0/prj/build_r37_iss.sh](https://github.com/tenstorrent/tensix-isa-simulator/blob/afb83529/tensix/whb0/prj/build_r37_iss.sh)[tensix/whb0/prj/build_r37_llk.sh](https://github.com/tenstorrent/tensix-isa-simulator/blob/afb83529/tensix/whb0/prj/build_r37_llk.sh)

## Build System

The r37 components are compiled into static libraries using dedicated build scripts.

### Instruction Set Simulator Build

The r37 ISS is built using `build_r37_iss.sh`, which compiles the source files in `src/r37/iss/*.cpp` and creates the static library `lib/r37_iss.a`:

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

Source: [tensix/whb0/prj/build_r37_iss.sh 9-20](https://github.com/tenstorrent/tensix-isa-simulator/blob/afb83529/tensix/whb0/prj/build_r37_iss.sh#L9-L20)

### LLK Implementation Build

The r37 LLK implementation is built using `build_r37_llk.sh`, which compiles the source files in `src/r37/llk_lib/*.cpp` and creates the static library `lib/r37_llk.a`:

```
g++ -c -std=c++17 -O3 \
    -I $SRC \
    -I $SRC/r37/iss \
    -I $SRC/r37/common/inc \
    -I $SRC/r37/llk_lib \
    -I $SRC/r37/etc \
    -I $SRC/r37/etc/defines \
    $SRC/r37/llk_lib/*.cpp

ar rsc $LIB/r37_llk.a *.o
```

Source: [tensix/whb0/prj/build_r37_llk.sh 9-20](https://github.com/tenstorrent/tensix-isa-simulator/blob/afb83529/tensix/whb0/prj/build_r37_llk.sh#L9-L20)

## System Integration

The r37 System Integration Diagram

Sources: System Architecture Diagram from Overall System Architecture

## Dependencies and Relationships

The r37 architecture has several key dependencies:

1.   **Core Library**: Provides fundamental functionality used by r37 components

2.   **ISS Dependencies**:

    *   The r37 ISS forms the foundation for other r37 components
    *   It relies on common definitions and configuration files

3.   **Layered Structure**:

    *   LLK Implementation depends on the ISS
    *   Compute components depend on both ISS and LLK implementations

This layered approach allows for separation of concerns while maintaining a coherent simulation environment.

## References

*   [r37 Instruction Set Simulator](https://deepwiki.com/tenstorrent/tensix-isa-simulator/4.1-r37-instruction-set-simulator)
*   [r37 LLK Implementation](https://deepwiki.com/tenstorrent/tensix-isa-simulator/4.2-r37-llk-implementation)
*   [r37 Compute Components](https://deepwiki.com/tenstorrent/tensix-isa-simulator/4.3-r37-compute-components)
*   [Core Library](https://deepwiki.com/tenstorrent/tensix-isa-simulator/5.1-core-library)
*   [Schedule Library](https://deepwiki.com/tenstorrent/tensix-isa-simulator/5.2-schedule-library)
*   [Serial Flash Programming Interface](https://deepwiki.com/tenstorrent/tensix-isa-simulator/5.3-serial-flash-programming-interface)

Dismiss
Refresh this wiki

Enter email to refresh
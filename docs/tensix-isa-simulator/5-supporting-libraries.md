---
project: tensix-isa-simulator
github: https://github.com/tenstorrent/tensix-isa-simulator
deepwiki: https://deepwiki.com/tenstorrent/tensix-isa-simulator/5-supporting-libraries
---

# Supporting Libraries

Relevant source files
*   [tensix/whb0/prj/build_core.sh](https://github.com/tenstorrent/tensix-isa-simulator/blob/afb83529/tensix/whb0/prj/build_core.sh)
*   [tensix/whb0/prj/build_schedule.sh](https://github.com/tenstorrent/tensix-isa-simulator/blob/afb83529/tensix/whb0/prj/build_schedule.sh)

## Purpose and Scope

This document provides an overview of the key supporting libraries that form the foundation of the Tensix ISA Simulator. These libraries offer essential functionality used across both the LLK System (see [LLK System](https://deepwiki.com/tenstorrent/tensix-isa-simulator/3-llk-system)) and r37 Architecture (see [r37 Architecture](https://deepwiki.com/tenstorrent/tensix-isa-simulator/4-r37-architecture)) components. The supporting libraries provide fundamental services such as memory management, task scheduling, coroutine functionality, and flash programming interfaces that enable the higher-level components to function effectively.

## Library Overview

The Tensix ISA Simulator relies on four primary supporting libraries that provide critical infrastructure for the system:

Sources:

*   Based on diagram information in prompt

### Library Dependencies

The following diagram illustrates the dependencies between the supporting libraries and other components of the system:

Sources:

*   Based on diagram information in prompt

## Core Library

The Core Library provides fundamental functionality required by all other components in the Tensix ISA Simulator. It serves as the base layer of the library stack, handling essential operations such as:

*   Memory management
*   Basic data structures
*   Utility functions
*   Common interfaces used throughout the system

All LLK implementations (Basic, Reference, and Compute) and the r37 ISS directly depend on the Core Library, highlighting its critical role in the system architecture.

### Build Process

The Core Library is built using a dedicated build script:

```
build_core.sh
```

The build script compiles all C++ source files in the `src/core` directory and packages them into a static library (`core.a`) that can be linked by other components.

Sources:

*   [tensix/whb0/prj/build_core.sh 1-20](https://github.com/tenstorrent/tensix-isa-simulator/blob/afb83529/tensix/whb0/prj/build_core.sh#L1-L20)

## Schedule Library

The Schedule Library provides task scheduling functionality for the Tensix ISA Simulator. This library is responsible for:

*   Task creation and management
*   Task scheduling and prioritization
*   Execution flow control
*   Inter-task communication mechanisms

The Schedule Library is used by both the LLK system components and r37 architecture implementations to coordinate execution of operations.

### Build Process

Similar to the Core Library, the Schedule Library has its own build script:

```
build_schedule.sh
```

This script compiles all source files in the `src/schedule` directory and creates a static library (`schedule.a`) for use by dependent components.

Sources:

*   [tensix/whb0/prj/build_schedule.sh 1-20](https://github.com/tenstorrent/tensix-isa-simulator/blob/afb83529/tensix/whb0/prj/build_schedule.sh#L1-L20)

## Serial Flash Programming Interface (SFPI)

The Serial Flash Programming Interface (SFPI) library provides functionality for interacting with serial flash memory. This library is particularly important for:

*   Flash memory programming
*   Device configuration
*   Firmware updates
*   Persistent storage operations

The SFPI library is used by both the LLK and r37 systems, as well as having a dedicated demo application (`demo_sfpi_gen`) that showcases its capabilities. For detailed information about the SFPI demo generator, see [SFPI Demo Generator](https://deepwiki.com/tenstorrent/tensix-isa-simulator/6.3-sfpi-demo-generator).

### Key Features

The SFPI library provides a standardized interface for:

*   Reading from flash memory
*   Writing to flash memory
*   Erasing flash sectors
*   Managing flash configuration
*   Performing error checking and validation

## Minicoro Coroutine Library

The Minicoro library provides stackful coroutine functionality used within the Tensix ISA Simulator. This library enables:

*   Cooperative multitasking
*   Asynchronous execution flows
*   Suspension and resumption of operations
*   Context switching between tasks

The Minicoro library has an indirect dependency relationship with the Core Library, providing fundamental coroutine capabilities that can be utilized by other components in the system.

### Integration Model

Minicoro is integrated as a header-only library (`minicoro.h`), making it easy to include in other components without requiring separate linking steps. This lightweight approach allows for efficient coroutine support throughout the system.

## Library Integration

The supporting libraries form the foundation of the Tensix ISA Simulator architecture. The following table summarizes how each library is integrated into the larger system:

| Library | Build Output | Used By | Primary Functions |
| --- | --- | --- | --- |
| Core | core.a | LLK API, LLK Basic, LLK Ref, LLK Compute, r37 ISS | Memory management, data structures, utilities |
| Schedule | schedule.a | demo_llk_basic, demo_llk_r37, demo_llk_ref | Task scheduling, execution flow control |
| SFPI | sfpi.a | demo_llk_r37, demo_llk_ref, demo_sfpi_gen | Flash memory interaction, device configuration |
| Minicoro | minicoro.h | Core Library | Coroutine functionality, cooperative multitasking |

## Build System Organization

The supporting libraries are built using dedicated build scripts that compile the source files and create static libraries. These libraries are then linked into the higher-level components that depend on them.

Sources:

*   [tensix/whb0/prj/build_core.sh 1-20](https://github.com/tenstorrent/tensix-isa-simulator/blob/afb83529/tensix/whb0/prj/build_core.sh#L1-L20)
*   [tensix/whb0/prj/build_schedule.sh 1-20](https://github.com/tenstorrent/tensix-isa-simulator/blob/afb83529/tensix/whb0/prj/build_schedule.sh#L1-L20)

## Summary

The supporting libraries (Core, Schedule, SFPI, and Minicoro) provide essential functionality that underpins the entire Tensix ISA Simulator architecture. These libraries are designed to be reusable, modular components that can be integrated into various parts of the system. The Core Library, in particular, serves as a foundation for all other components, while the Schedule, SFPI, and Minicoro libraries provide specialized functionality for specific aspects of the system.

For more detailed information about each library, refer to their dedicated documentation pages:

*   [Core Library](https://deepwiki.com/tenstorrent/tensix-isa-simulator/5.1-core-library)
*   [Schedule Library](https://deepwiki.com/tenstorrent/tensix-isa-simulator/5.2-schedule-library)
*   [Serial Flash Programming Interface](https://deepwiki.com/tenstorrent/tensix-isa-simulator/5.3-serial-flash-programming-interface)
*   [Minicoro Coroutine Library](https://deepwiki.com/tenstorrent/tensix-isa-simulator/5.4-minicoro-coroutine-library)

Dismiss
Refresh this wiki

Enter email to refresh
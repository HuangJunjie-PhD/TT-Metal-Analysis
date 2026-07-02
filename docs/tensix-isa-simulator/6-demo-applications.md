---
project: tensix-isa-simulator
github: https://github.com/tenstorrent/tensix-isa-simulator
deepwiki: https://deepwiki.com/tenstorrent/tensix-isa-simulator/6-demo-applications
---

# Demo Applications

Relevant source files
*   [tensix/whb0/prj/build_demo_llk_basic.sh](https://github.com/tenstorrent/tensix-isa-simulator/blob/afb83529/tensix/whb0/prj/build_demo_llk_basic.sh)
*   [tensix/whb0/prj/build_demo_llk_r37.sh](https://github.com/tenstorrent/tensix-isa-simulator/blob/afb83529/tensix/whb0/prj/build_demo_llk_r37.sh)
*   [tensix/whb0/prj/build_demo_llk_ref.sh](https://github.com/tenstorrent/tensix-isa-simulator/blob/afb83529/tensix/whb0/prj/build_demo_llk_ref.sh)
*   [tensix/whb0/prj/build_demo_sfpi_gen.sh](https://github.com/tenstorrent/tensix-isa-simulator/blob/afb83529/tensix/whb0/prj/build_demo_sfpi_gen.sh)

## Purpose and Scope

This document provides comprehensive information about the demo applications included in the Tensix ISA Simulator repository. These demo applications serve as practical examples that demonstrate the functionality of various components within the system. They are designed to help developers understand how to use the different libraries and interfaces in real-world scenarios.

The demo applications covered in this document include demonstrations of the Low-Level Kernel (LLK) implementations, r37 architecture components, and the Serial Flash Programming Interface (SFPI). For information about the underlying libraries these demos showcase, see [System Architecture](https://deepwiki.com/tenstorrent/tensix-isa-simulator/2-system-architecture), [LLK System](https://deepwiki.com/tenstorrent/tensix-isa-simulator/3-llk-system), [r37 Architecture](https://deepwiki.com/tenstorrent/tensix-isa-simulator/4-r37-architecture), and [Supporting Libraries](https://deepwiki.com/tenstorrent/tensix-isa-simulator/5-supporting-libraries).

## Overview of Demo Applications

The Tensix ISA Simulator includes four primary demo applications that showcase different aspects of the system:

The demo applications are organized into three categories:

1.   **LLK Demo Applications** - Demonstrate the basic and reference implementations of the Low-Level Kernel
2.   **r37 Demo Applications** - Showcase the r37 architecture components and related functionality
3.   **SFPI Demo Generator** - Illustrates the capabilities of the Serial Flash Programming Interface

Sources: [tensix/whb0/prj/build_demo_llk_basic.sh](https://github.com/tenstorrent/tensix-isa-simulator/blob/afb83529/tensix/whb0/prj/build_demo_llk_basic.sh)[tensix/whb0/prj/build_demo_llk_r37.sh](https://github.com/tenstorrent/tensix-isa-simulator/blob/afb83529/tensix/whb0/prj/build_demo_llk_r37.sh)[tensix/whb0/prj/build_demo_llk_ref.sh](https://github.com/tenstorrent/tensix-isa-simulator/blob/afb83529/tensix/whb0/prj/build_demo_llk_ref.sh)[tensix/whb0/prj/build_demo_sfpi_gen.sh](https://github.com/tenstorrent/tensix-isa-simulator/blob/afb83529/tensix/whb0/prj/build_demo_sfpi_gen.sh)

## Demo Application Dependencies

Each demo application depends on specific libraries within the Tensix ISA Simulator architecture. The following diagram illustrates these dependencies:

Sources: [tensix/whb0/prj/build_demo_llk_basic.sh 12-17](https://github.com/tenstorrent/tensix-isa-simulator/blob/afb83529/tensix/whb0/prj/build_demo_llk_basic.sh#L12-L17)[tensix/whb0/prj/build_demo_llk_r37.sh 12-24](https://github.com/tenstorrent/tensix-isa-simulator/blob/afb83529/tensix/whb0/prj/build_demo_llk_r37.sh#L12-L24)[tensix/whb0/prj/build_demo_llk_ref.sh 12-22](https://github.com/tenstorrent/tensix-isa-simulator/blob/afb83529/tensix/whb0/prj/build_demo_llk_ref.sh#L12-L22)[tensix/whb0/prj/build_demo_sfpi_gen.sh 12-15](https://github.com/tenstorrent/tensix-isa-simulator/blob/afb83529/tensix/whb0/prj/build_demo_sfpi_gen.sh#L12-L15)

## LLK Demo Applications

The LLK (Low-Level Kernel) demo applications demonstrate how to use different implementations of the LLK system.

### demo_llk_basic

The `demo_llk_basic` application showcases the basic implementation of the LLK system. This demo is designed to illustrate the fundamental functionality of LLK without complex optimizations or features.

**Build Dependencies:**

*   llk_basic.a - Basic LLK implementation
*   core.a - Core library providing essential functionality
*   schedule.a - Schedule library for task management

**Build Command:**

`g++ -o demo_llk_basic -std=c++17 -O3 \    -I ../src \    ../src/demo/llk_basic/*.cpp \    ../lib/llk_basic.a \    ../lib/core.a \    ../lib/schedule.a`
This demo serves as an entry point for developers who want to understand the basics of LLK without diving into the more complex implementations.

Sources: [tensix/whb0/prj/build_demo_llk_basic.sh 12-17](https://github.com/tenstorrent/tensix-isa-simulator/blob/afb83529/tensix/whb0/prj/build_demo_llk_basic.sh#L12-L17)

### demo_llk_ref

The `demo_llk_ref` application demonstrates the reference implementation of the LLK system. The reference implementation provides a complete set of LLK features and serves as a baseline for other implementations.

**Build Dependencies:**

*   llk_api.a - LLK API library
*   core.a - Core library
*   schedule.a - Schedule library
*   sfpi.a - Serial Flash Programming Interface library

**Build Command:**

`g++ -o demo_llk_ref -std=c++17 -O3 \    -I ../src \    -I ../src/system \    -I ../src/llk \    -I ../src/llk/api \    -I ../src/llk/ref \    ../src/demo/llk_ref/*.cpp \    ../lib/llk_api.a \    ../lib/core.a \    ../lib/schedule.a \    ../lib/sfpi.a`
This demo is useful for developers who want to see the standard implementations of LLK features and understand how the API is intended to be used.

Sources: [tensix/whb0/prj/build_demo_llk_ref.sh 12-22](https://github.com/tenstorrent/tensix-isa-simulator/blob/afb83529/tensix/whb0/prj/build_demo_llk_ref.sh#L12-L22)

## r37 Demo Applications

### demo_llk_r37

The `demo_llk_r37` application demonstrates the LLK implementation specific to the r37 architecture. This demo showcases how the r37 Instruction Set Simulator interacts with the LLK system.

**Build Dependencies:**

*   r37_iss.a - r37 Instruction Set Simulator
*   core.a - Core library
*   schedule.a - Schedule library
*   sfpi.a - Serial Flash Programming Interface library

**Build Command:**

`g++ -o demo_llk_r37 -std=c++17 -O3 \    -I ../src \    -I ../src/r37 \    -I ../src/r37/iss \    -I ../src/r37/common/inc \    -I ../src/r37/llk_lib \    -I ../src/r37/etc \    -I ../src/r37/etc/defines \    ../src/demo/llk_r37/*.cpp \    ../lib/r37_iss.a \    ../lib/core.a \    ../lib/schedule.a \    ../lib/sfpi.a`
This demo is particularly valuable for developers working with the r37 architecture, as it illustrates how the architecture's specific features are utilized within the LLK framework.

Sources: [tensix/whb0/prj/build_demo_llk_r37.sh 12-24](https://github.com/tenstorrent/tensix-isa-simulator/blob/afb83529/tensix/whb0/prj/build_demo_llk_r37.sh#L12-L24)

## SFPI Demo Generator

### demo_sfpi_gen

The `demo_sfpi_gen` application demonstrates the capabilities of the Serial Flash Programming Interface (SFPI) library. This demo shows how to use SFPI to interact with serial flash memory.

**Build Dependencies:**

*   sfpi.a - Serial Flash Programming Interface library

**Build Command:**

`g++ -o demo_sfpi_gen -std=c++17 -O3 \    -I ../src \    ../src/demo/sfpi_gen/*.cpp \    ../lib/sfpi.a`
This demo is simpler than the others in terms of dependencies, focusing specifically on showing the functionality of the SFPI library. It's useful for developers who need to understand how to work with serial flash memory in the context of the Tensix ISA.

Sources: [tensix/whb0/prj/build_demo_sfpi_gen.sh 12-15](https://github.com/tenstorrent/tensix-isa-simulator/blob/afb83529/tensix/whb0/prj/build_demo_sfpi_gen.sh#L12-L15)

## Building and Running Demo Applications

All demo applications can be built using their respective build scripts in the `tensix/whb0/prj/` directory. These scripts compile the demo applications and place the resulting executables in the `../bin/` directory.

**General Build Process:**

1.   Navigate to the `tensix/whb0/prj/` directory
2.   Run the appropriate build script for the demo you want to build
3.   The executable will be created in the `../bin/` directory

For example, to build and run the demo_llk_basic application:

`cd tensix/whb0/prj/./build_demo_llk_basic.sh../bin/demo_llk_basic`
Sources: [tensix/whb0/prj/build_demo_llk_basic.sh 6-10](https://github.com/tenstorrent/tensix-isa-simulator/blob/afb83529/tensix/whb0/prj/build_demo_llk_basic.sh#L6-L10)[tensix/whb0/prj/build_demo_llk_r37.sh 6-10](https://github.com/tenstorrent/tensix-isa-simulator/blob/afb83529/tensix/whb0/prj/build_demo_llk_r37.sh#L6-L10)[tensix/whb0/prj/build_demo_llk_ref.sh 6-10](https://github.com/tenstorrent/tensix-isa-simulator/blob/afb83529/tensix/whb0/prj/build_demo_llk_ref.sh#L6-L10)[tensix/whb0/prj/build_demo_sfpi_gen.sh 6-10](https://github.com/tenstorrent/tensix-isa-simulator/blob/afb83529/tensix/whb0/prj/build_demo_sfpi_gen.sh#L6-L10)

## Use Cases and Practical Applications

The demo applications included in the Tensix ISA Simulator repository serve several important purposes:

1.   **Educational Resources** - The demos provide practical examples of how to use the different components of the system, making them valuable educational resources for new developers.

2.   **Testing and Verification** - The demos can be used to verify that the different components of the system are functioning correctly. They provide a way to test the behavior of libraries in isolation.

3.   **Reference Implementations** - The demos serve as reference implementations that developers can use as a basis for their own applications.

4.   **Feature Demonstration** - Each demo highlights specific features of its respective component, making it easier for developers to understand the capabilities of each part of the system.

The following table summarizes the primary use cases for each demo application:

| Demo Application | Primary Use Case |
| --- | --- |
| demo_llk_basic | Understanding basic LLK functionality without advanced features |
| demo_llk_ref | Learning the reference implementation of LLK features |
| demo_llk_r37 | Understanding how r37 architecture components interact with LLK |
| demo_sfpi_gen | Learning how to use SFPI for serial flash memory operations |

## Conclusion

The demo applications provided in the Tensix ISA Simulator repository offer valuable insights into the functionality and usage patterns of various system components. By studying and experimenting with these demos, developers can gain a better understanding of how to use the Tensix ISA Simulator effectively for their own projects.

For more detailed information about the underlying components that these demos showcase, refer to the documentation on [LLK System](https://deepwiki.com/tenstorrent/tensix-isa-simulator/3-llk-system), [r37 Architecture](https://deepwiki.com/tenstorrent/tensix-isa-simulator/4-r37-architecture), and [Supporting Libraries](https://deepwiki.com/tenstorrent/tensix-isa-simulator/5-supporting-libraries).

Dismiss
Refresh this wiki

Enter email to refresh
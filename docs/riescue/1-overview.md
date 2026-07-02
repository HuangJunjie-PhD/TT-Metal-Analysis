---
project: riescue
github: https://github.com/tenstorrent/riescue
deepwiki: https://deepwiki.com/tenstorrent/riescue/1-overview
---

# Overview

 Relevant source files

* [LICENSE](https://github.com/tenstorrent/riescue/blob/07cefde3/LICENSE)
* [README.md](https://github.com/tenstorrent/riescue/blob/07cefde3/README.md?plain=1)
* [infra/container/singularity-id](https://github.com/tenstorrent/riescue/blob/07cefde3/infra/container/singularity-id)
* [pyproject.toml](https://github.com/tenstorrent/riescue/blob/07cefde3/pyproject.toml)
* [riescue/dtest\_framework/\_\_init\_\_.py](https://github.com/tenstorrent/riescue/blob/07cefde3/riescue/dtest_framework/__init__.py)
* [riescue/dtest\_framework/lib/\_\_init\_\_.py](https://github.com/tenstorrent/riescue/blob/07cefde3/riescue/dtest_framework/lib/__init__.py)
* [riescue/riescued.py](https://github.com/tenstorrent/riescue/blob/07cefde3/riescue/riescued.py)
* [tests/cli\_tests/riescued/hypervisor\_test.py](https://github.com/tenstorrent/riescue/blob/07cefde3/tests/cli_tests/riescued/hypervisor_test.py)

This document provides a high-level introduction to the RiESCUE repository, describing its purpose as a RISC-V test generation framework and the architecture of its three main tools: RiescueD, RiescueC, and CTK. This overview covers the system's design philosophy, core components, external dependencies, and how they integrate to form a comprehensive RISC-V verification platform.

For detailed installation instructions, see [Installation and Quick Start](/tenstorrent/riescue/1.1-installation-and-quick-start). For in-depth documentation of individual tools, see [Core Testing Framework](/tenstorrent/riescue/2-core-testing-framework). For development environment setup, see [Development Environment](/tenstorrent/riescue/3-development-environment).

## What is RiESCUE?

RiESCUE (pronounced *"rescue"*) is a Python library and suite of command-line tools for generating, building, and simulating RISC-V test ELF files. The framework is designed to support RISC-V processor verification across multiple privilege modes, paging modes, virtualization scenarios, and ISA extensions.

The framework consists of three primary tools that build upon each other in a layered architecture:

| Tool | Purpose | Entry Point | Primary Use Case |
| --- | --- | --- | --- |
| **RiescueD** | Directed test framework | `riescued` | Writing custom directed tests with preprocessor directives |
| **RiescueC** | Compliance test generator | `riescuec` | Generating datapath compliance tests and test-plan-based scenarios |
| **CTK** | Compliance Test Kit | `ctk` | High-level ISA-string-based test suite generation |

Sources: [README.md11-73](https://github.com/tenstorrent/riescue/blob/07cefde3/README.md?plain=1#L11-L73) [pyproject.toml11-49](https://github.com/tenstorrent/riescue/blob/07cefde3/pyproject.toml#L11-L49)

## Core Components

### RiescueD: Directed Test Framework

RiescueD is the foundational layer of the testing framework. It provides a Python library and command-line tool for creating directed test cases by compiling assembly tests with preprocessor directives into ELF executables.

**Key capabilities:**

* OS code simulation with privilege mode support (Machine, Supervisor, User, Virtual Supervisor)
* Random address generation and memory management
* Page table generation for Sv39, Sv48, Sv57 modes
* Hypervisor and virtualization support
* Multi-processor support

The main class is `RiescueD` in [riescue/riescued.py31-433](https://github.com/tenstorrent/riescue/blob/07cefde3/riescue/riescued.py#L31-L433) Tests are written as `.rasm` files containing assembly code with special directives that control test generation behavior.

Sources: [README.md24-34](https://github.com/tenstorrent/riescue/blob/07cefde3/README.md?plain=1#L24-L34) [riescue/riescued.py31-86](https://github.com/tenstorrent/riescue/blob/07cefde3/riescue/riescued.py#L31-L86)

### RiescueC: Compliance Test Generator

RiescueC builds on top of RiescueD to provide automated compliance test generation. It operates in two modes:

**Bringup Mode** (`--mode bringup`): Generates datapath instruction compliance tests for RISC-V extensions including I, M, A, F, D, C, V, and various bit manipulation and cryptography extensions. Tests are self-checking with configurable constraints.

**Test Plan Mode** (`--mode tp`): Consumes `TestPlan` and `TestScenario` definitions from the `riscv-coretp` library to generate directed tests for architectural and privilege scenarios. This mode uses RiescueD's underlying framework to generate test ELFs from scenario descriptions.

Sources: [README.md37-64](https://github.com/tenstorrent/riescue/blob/07cefde3/README.md?plain=1#L37-L64) [README.md162-246](https://github.com/tenstorrent/riescue/blob/07cefde3/README.md?plain=1#L162-L246)

### CTK: Compliance Test Kit

CTK is a high-level wrapper around RiescueC that generates complete test suites from ISA strings. Given a specification like `"rv64imfv"`, it generates a directory structure containing tests for all specified extensions.

CTK simplifies the test generation workflow by automatically determining which RiescueC modes and configurations to use based on the ISA string, making it the easiest entry point for users who need comprehensive test coverage.

Sources: [README.md68-73](https://github.com/tenstorrent/riescue/blob/07cefde3/README.md?plain=1#L68-L73)

## System Architecture

The following diagram shows how the three core tools relate to each other and to external RISC-V ecosystem components:

**Architecture layers:**

1. **User Interface Layer**: CTK provides the simplest interface, RiescueC provides mode-based generation, RiescueD provides maximum control
2. **Framework Layer**: Parser, Pool, Generator, and FeatMgr implement the core test generation logic
3. **Integration Layer**: Toolchain class abstracts external tools (compiler, simulators)
4. **External Tools**: RISC-V GNU toolchain, instruction set simulators, test plan library

Sources: [riescue/riescued.py31-86](https://github.com/tenstorrent/riescue/blob/07cefde3/riescue/riescued.py#L31-L86) [pyproject.toml46-49](https://github.com/tenstorrent/riescue/blob/07cefde3/pyproject.toml#L46-L49)

## Test Generation Workflow

The following diagram illustrates the data flow from user input through test generation, compilation, and simulation:

**Pipeline stages:**

1. **Configuration** (`configure()`): Consolidates test header directives, CPU configuration JSON, and CLI arguments into a `FeatMgr` object at [riescue/riescued.py222-237](https://github.com/tenstorrent/riescue/blob/07cefde3/riescue/riescued.py#L222-L237)
2. **Generation** (`generate()`): Parser reads the `.rasm` file and populates the Pool with test data. Generator processes this data along with FeatMgr configuration to produce assembly `.S` file and linker script `.ld` at [riescue/riescued.py239-267](https://github.com/tenstorrent/riescue/blob/07cefde3/riescue/riescued.py#L239-L267)
3. **Build** (`build()`): Invokes `riscv64-unknown-elf-gcc` via the Compiler tool to create ELF executable and generates disassembly via objdump at [riescue/riescued.py269-309](https://github.com/tenstorrent/riescue/blob/07cefde3/riescue/riescued.py#L269-L309)
4. **Simulation** (`simulate()`): Runs the ELF on Whisper or Spike ISS and captures execution logs at [riescue/riescued.py311-411](https://github.com/tenstorrent/riescue/blob/07cefde3/riescue/riescued.py#L311-L411)

Sources: [riescue/riescued.py188-220](https://github.com/tenstorrent/riescue/blob/07cefde3/riescue/riescued.py#L188-L220) [riescue/dtest\_framework/artifacts.py](https://github.com/tenstorrent/riescue/blob/07cefde3/riescue/dtest_framework/artifacts.py)

## External Dependencies

RiESCUE integrates with multiple external RISC-V ecosystem tools. The following diagram shows these dependencies and their roles:

**Dependency categories:**

| Category | Tool | Purpose | Installation Method |
| --- | --- | --- | --- |
| Compiler | riscv-gnu-toolchain | Compile assembly to ELF | Built from source or downloaded archive |
| Simulator | Whisper (Tenstorrent) | Fast ISS with TT-specific features | Built from source via `build_whisper.sh` |
| Simulator | Spike (reference) | RISC-V reference ISS | Built from source via `build_spike.sh` |
| Simulator | tt\_spike (TT fork) | TT-enhanced Spike variant | Built from source via `build_spike.sh` |
| Test Plans | riscv-coretp | Architectural test scenarios | Installed via pip from GitHub |

The `Toolchain` class at [riescue/lib/toolchain.py](https://github.com/tenstorrent/riescue/blob/07cefde3/riescue/lib/toolchain.py) provides a unified abstraction over these tools, handling executable discovery, subprocess invocation, and error handling.

Sources: [pyproject.toml17-29](https://github.com/tenstorrent/riescue/blob/07cefde3/pyproject.toml#L17-L29) [README.md88-92](https://github.com/tenstorrent/riescue/blob/07cefde3/README.md?plain=1#L88-L92)

## Package Structure

The RiESCUE Python package is organized into three main modules:

**Module purposes:**

* **`riescue.dtest_framework`**: Core directed testing framework implementation, including the Parser that processes `.rasm` files, Pool that stores test data, Generator that produces output files, and FeatMgr that manages configuration
* **`riescue.compliance`**: Compliance test generation logic for bringup mode (datapath testing) and TP mode (test plan scenarios)
* **`riescue.lib`**: Shared utilities including toolchain abstractions, logging facilities, random number generation, and base CLI classes

Entry points are defined in [pyproject.toml46-49](https://github.com/tenstorrent/riescue/blob/07cefde3/pyproject.toml#L46-L49) as console scripts: `riescued`, `riescuec`, and `ctk`.

Sources: [pyproject.toml31-42](https://github.com/tenstorrent/riescue/blob/07cefde3/pyproject.toml#L31-L42) [pyproject.toml46-49](https://github.com/tenstorrent/riescue/blob/07cefde3/pyproject.toml#L46-L49) [riescue/riescued.py1-22](https://github.com/tenstorrent/riescue/blob/07cefde3/riescue/riescued.py#L1-L22)

## Development Environment

RiESCUE uses a containerized development environment to ensure reproducibility across different host systems. The container encapsulates all system-level dependencies including the RISC-V toolchain and simulators.

**Key infrastructure components:**

| Component | Purpose | Location |
| --- | --- | --- |
| Container definition | Rocky Linux 9.1 base with dependencies | `infra/container/Container.def` |
| Container image | Pre-built Singularity/Apptainer image | `riescue.sif` |
| Build scripts | Install RISC-V tools in container | `infra/container/build_*.sh` |
| Management tool | Python CLI for container operations | `infra/container/container.py` |

The container system is documented in detail in [Container System](/tenstorrent/riescue/3.1-container-system). For contributors, see [Developing and Contributing](/tenstorrent/riescue/5-development-workflow).

The container identifier `30-2d59cd239c2bfcc64dec4b00ba85f7ed2cb65ae6f1ee99158c76a09f07aac629` at [infra/container/singularity-id1](https://github.com/tenstorrent/riescue/blob/07cefde3/infra/container/singularity-id#L1-L1) tracks the current container version for reproducibility.

Sources: [README.md152-156](https://github.com/tenstorrent/riescue/blob/07cefde3/README.md?plain=1#L152-L156) [infra/container/singularity-id1](https://github.com/tenstorrent/riescue/blob/07cefde3/infra/container/singularity-id#L1-L1)

## License

RiESCUE is distributed under the Apache License 2.0. The full license text is available at [LICENSE1-203](https://github.com/tenstorrent/riescue/blob/07cefde3/LICENSE#L1-L203)

Copyright (c) 2025 Tenstorrent AI ULC

Sources: [LICENSE1-203](https://github.com/tenstorrent/riescue/blob/07cefde3/LICENSE#L1-L203) [LICENSE202](https://github.com/tenstorrent/riescue/blob/07cefde3/LICENSE#L202-L202)

Refresh this wiki
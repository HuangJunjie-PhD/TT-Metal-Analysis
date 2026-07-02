---
project: riescue
github: https://github.com/tenstorrent/riescue
deepwiki: https://deepwiki.com/tenstorrent/riescue/2-core-testing-framework
---

# Core Testing Framework

 Relevant source files

* [README.md](https://github.com/tenstorrent/riescue/blob/07cefde3/README.md?plain=1)
* [infra/container/singularity-id](https://github.com/tenstorrent/riescue/blob/07cefde3/infra/container/singularity-id)
* [riescue/dtest\_framework/README.md](https://github.com/tenstorrent/riescue/blob/07cefde3/riescue/dtest_framework/README.md?plain=1)
* [riescue/riescued.py](https://github.com/tenstorrent/riescue/blob/07cefde3/riescue/riescued.py)
* [tests/cli\_tests/riescued/hypervisor\_test.py](https://github.com/tenstorrent/riescue/blob/07cefde3/tests/cli_tests/riescued/hypervisor_test.py)

## Purpose and Scope

The Core Testing Framework is the central system in RiESCUE for generating, compiling, and executing RISC-V assembly tests. It consists of three layers that build upon each other: **RiescueD** (directed test framework), **RiescueC** (compliance test generator), and **CTK** (compliance test kit). This page provides an architectural overview of how these three layers interact and the shared components they use.

For detailed information about using each tool, see:

* [RiescueD: Directed Test Framework](/tenstorrent/riescue/2.1-riescued:-directed-test-framework) - Writing custom directed tests
* [RiescueC: Compliance Test Generator](/tenstorrent/riescue/2.2-riescuec:-compliance-test-generator) - Generating compliance tests
* [CTK: Compliance Test Kit](/tenstorrent/riescue/2.3-ctk:-compliance-test-kit) - High-level ISA-based test generation

For information about the external dependencies and simulators, see [Toolchain and External Dependencies](/tenstorrent/riescue/4-toolchain-and-external-dependencies).

**Sources:** [README.md1-98](https://github.com/tenstorrent/riescue/blob/07cefde3/README.md?plain=1#L1-L98) [riescue/dtest\_framework/README.md1-148](https://github.com/tenstorrent/riescue/blob/07cefde3/riescue/dtest_framework/README.md?plain=1#L1-L148)

## Three-Layer Architecture

The Core Testing Framework is organized as a three-layer stack, where each layer provides progressively higher-level abstractions:

**Layer 1 (RiescueD):** The foundational layer that provides the core test generation capabilities. It accepts assembly files with special directives (`.rasm` files), processes them through the `Parser`, manages test data in the `Pool`, configures features via `FeatMgr`, and generates final assembly and linker scripts via `Generator`. This layer is implemented primarily in [riescue/riescued.py](https://github.com/tenstorrent/riescue/blob/07cefde3/riescue/riescued.py)

**Layer 2 (RiescueC):** The compliance test generation layer that uses RiescueD's capabilities to generate tests according to RISC-V compliance requirements. It operates in two modes:

* `BringupMode`: Generates datapath instruction compliance tests for basic extension coverage
* `TPMode`: Consumes test plans from the [riscv-coretp](https://github.com/tenstorrent/riescue/blob/07cefde3/riscv-coretp) library to generate architectural compliance tests

**Layer 3 (CTK):** The highest-level interface that accepts an ISA string (e.g., "rv64imfv") and generates entire test suites by orchestrating calls to RiescueC.

**Sources:** [README.md14-73](https://github.com/tenstorrent/riescue/blob/07cefde3/README.md?plain=1#L14-L73) [riescue/riescued.py31-71](https://github.com/tenstorrent/riescue/blob/07cefde3/riescue/riescued.py#L31-L71) [riescue/dtest\_framework/README.md1-48](https://github.com/tenstorrent/riescue/blob/07cefde3/riescue/dtest_framework/README.md?plain=1#L1-L48)

## Core Components and Data Flow

The framework's internal architecture consists of several key components that collaborate to transform test definitions into executable binaries:

### Component Responsibilities

| Component | File Path | Responsibility |
| --- | --- | --- |
| `Parser` | [riescue/dtest\_framework/parser.py](https://github.com/tenstorrent/riescue/blob/07cefde3/riescue/dtest_framework/parser.py) | Parses test files to extract directives, test headers, and test sections |
| `Pool` | [riescue/dtest\_framework/pool.py](https://github.com/tenstorrent/riescue/blob/07cefde3/riescue/dtest_framework/pool.py) | Stores and manages parsed test data, discrete tests, and generated values |
| `FeatMgr` | [riescue/dtest\_framework/config.py](https://github.com/tenstorrent/riescue/blob/07cefde3/riescue/dtest_framework/config.py) | Consolidates configuration from test headers, CPU config, and CLI arguments |
| `FeatMgrBuilder` | [riescue/dtest\_framework/config.py](https://github.com/tenstorrent/riescue/blob/07cefde3/riescue/dtest_framework/config.py) | Builder pattern class for constructing `FeatMgr` instances |
| `Generator` | [riescue/dtest\_framework/generator.py](https://github.com/tenstorrent/riescue/blob/07cefde3/riescue/dtest_framework/generator.py) | Generates final assembly code, linker scripts, and page tables |
| `GeneratedFiles` | [riescue/dtest\_framework/artifacts.py](https://github.com/tenstorrent/riescue/blob/07cefde3/riescue/dtest_framework/artifacts.py) | Dataclass tracking paths to all generated artifacts |

**Sources:** [riescue/riescued.py72-86](https://github.com/tenstorrent/riescue/blob/07cefde3/riescue/riescued.py#L72-L86) [riescue/riescued.py222-267](https://github.com/tenstorrent/riescue/blob/07cefde3/riescue/riescued.py#L222-L267)

## Test Generation and Execution Workflow

The complete workflow from test definition to simulation follows a standardized pipeline:

### Workflow Methods

The `RiescueD` class implements the workflow through distinct methods:

1. **`__init__`** [riescue/riescued.py47-86](https://github.com/tenstorrent/riescue/blob/07cefde3/riescue/riescued.py#L47-L86): Initializes paths, random seed, toolchain, and invokes `Parser.parse()` to populate the `Pool`
2. **`configure`** [riescue/riescued.py222-237](https://github.com/tenstorrent/riescue/blob/07cefde3/riescue/riescued.py#L222-L237): Builds the `FeatMgr` by consolidating test headers, CPU configuration, and command-line arguments
3. **`generate`** [riescue/riescued.py239-267](https://github.com/tenstorrent/riescue/blob/07cefde3/riescue/riescued.py#L239-L267): Produces assembly files and linker scripts by invoking `Generator.generate()`
4. **`build`** [riescue/riescued.py269-309](https://github.com/tenstorrent/riescue/blob/07cefde3/riescue/riescued.py#L269-L309): Compiles generated assembly into ELF executable and creates disassembly
5. **`simulate`** [riescue/riescued.py311-411](https://github.com/tenstorrent/riescue/blob/07cefde3/riescue/riescued.py#L311-L411): Runs the ELF on an ISS (Whisper or Spike) and validates results

**Sources:** [riescue/riescued.py188-220](https://github.com/tenstorrent/riescue/blob/07cefde3/riescue/riescued.py#L188-L220) [riescue/riescued.py31-86](https://github.com/tenstorrent/riescue/blob/07cefde3/riescue/riescued.py#L31-L86)

## Command-Line Entry Points

Each layer of the framework provides a command-line interface:

### Entry Point Locations

| Command | Entry Point Function | Installation |
| --- | --- | --- |
| `riescued` | `riescue.riescued:main` | Defined in `pyproject.toml` `[project.scripts]` |
| `riescuec` | `riescue.riescuec:main` | Defined in `pyproject.toml` `[project.scripts]` |
| `ctk` | `riescue.ctk:main` | Defined in `pyproject.toml` `[project.scripts]` |

The entry points follow a common pattern:

1. Define arguments with `add_arguments()` method
2. Parse command-line arguments using `argparse`
3. Construct the main class from arguments using `from_clargs()`
4. Execute the workflow with `run()` method

**Sources:** [riescue/riescued.py89-158](https://github.com/tenstorrent/riescue/blob/07cefde3/riescue/riescued.py#L89-L158) [riescue/riescued.py435-440](https://github.com/tenstorrent/riescue/blob/07cefde3/riescue/riescued.py#L435-L440)

## Toolchain Integration

The framework integrates with external RISC-V tools through an abstraction layer:

### Toolchain Configuration

The `Toolchain` class provides a unified interface to external tools:

| Tool Class | Purpose | Used In |
| --- | --- | --- |
| `Compiler` | Compiles assembly to ELF | [riescue/riescued.py281-298](https://github.com/tenstorrent/riescue/blob/07cefde3/riescue/riescued.py#L281-L298) in `build()` |
| `Disassembler` | Generates disassembly from ELF | [riescue/riescued.py301-307](https://github.com/tenstorrent/riescue/blob/07cefde3/riescue/riescued.py#L301-L307) in `build()` |
| `Objcopy` | Binary manipulation | Used by generator for memory initialization |
| `Whisper` | Tenstorrent ISS | [riescue/riescued.py365-386](https://github.com/tenstorrent/riescue/blob/07cefde3/riescue/riescued.py#L365-L386) in `simulate()` |
| `Spike` | Reference RISC-V ISS | [riescue/riescued.py346-363](https://github.com/tenstorrent/riescue/blob/07cefde3/riescue/riescued.py#L346-L363) in `simulate()` |

The `RiescueD` class accepts an optional `Toolchain` instance in its constructor [riescue/riescued.py67-70](https://github.com/tenstorrent/riescue/blob/07cefde3/riescue/riescued.py#L67-L70) If not provided, a default `Toolchain()` is created with environment-based tool discovery.

**Sources:** [riescue/riescued.py67-70](https://github.com/tenstorrent/riescue/blob/07cefde3/riescue/riescued.py#L67-L70) [riescue/riescued.py269-309](https://github.com/tenstorrent/riescue/blob/07cefde3/riescue/riescued.py#L269-L309) [riescue/riescued.py311-411](https://github.com/tenstorrent/riescue/blob/07cefde3/riescue/riescued.py#L311-L411)

## Configuration Management

The framework uses a sophisticated configuration system that merges multiple sources:

### Configuration Priority

The `FeatMgrBuilder` applies configuration sources in order, with later sources overriding earlier ones:

1. **Default values**: Built into `FeatMgr` class
2. **CPU Config JSON**: System-level defaults from [riescue/dtest\_framework/lib/config.json](https://github.com/tenstorrent/riescue/blob/07cefde3/riescue/dtest_framework/lib/config.json)
3. **Test Header**: Test-specific directives parsed from `.rasm` file
4. **CLI Arguments**: User overrides from command line (highest priority)

This allows tests to specify reasonable defaults while giving users the ability to override any setting for experimentation or debugging.

**Sources:** [riescue/riescued.py222-237](https://github.com/tenstorrent/riescue/blob/07cefde3/riescue/riescued.py#L222-L237) [riescue/dtest\_framework/README.md40-68](https://github.com/tenstorrent/riescue/blob/07cefde3/riescue/dtest_framework/README.md?plain=1#L40-L68)

## Feature Support Matrix

The framework supports various RISC-V architectural features configurable through `FeatMgr`:

| Feature Category | Configuration Options | CLI Arguments |
| --- | --- | --- |
| **Privilege Modes** | `machine`, `super`, `user`, `any` | `--privilege_mode` |
| **Paging Modes** | `sv39`, `sv48`, `sv57`, `disable`, `any` | `--paging_mode` |
| **Environment** | `bare_metal`, `virtualized` | `--test_env` |
| **Multi-hart** | 1-N CPUs | `--num_cpus` |
| **Extensions** | RVA23 standard extensions | Feature flags in CPU config |
| **Memory Modes** | Big/little endian | `--big_endian` |

The "any" option for privilege and paging modes allows the framework to randomly select a valid configuration at runtime, enabling randomized testing across multiple architectural configurations.

**Sources:** [riescue/dtest\_framework/README.md78-84](https://github.com/tenstorrent/riescue/blob/07cefde3/riescue/dtest_framework/README.md?plain=1#L78-L84) [README.md28-33](https://github.com/tenstorrent/riescue/blob/07cefde3/README.md?plain=1#L28-L33)

## Test Types and Generation Modes

The framework supports different test generation approaches depending on the layer used:

| Test Type | Generated By | Input Format | Use Case |
| --- | --- | --- | --- |
| **Directed Tests** | RiescueD | `.rasm` assembly with directives | Custom test scenarios, corner cases |
| **Datapath Compliance** | RiescueC (bringup mode) | ISA string (e.g., "rv64imfv") | Basic extension instruction coverage |
| **Privilege Compliance** | RiescueC (tp mode) | Test plan name (e.g., "zicond") | Architectural compliance testing |
| **Test Suites** | CTK | ISA string | Complete test suite generation |

### Self-Checking Mechanism

All generated tests include self-checking mechanisms to automatically determine pass/fail status:

* **Standard Mode**: Tests write to the `tohost` CSR or memory location to signal completion
* **WYSIWYG Mode**: Tests write specific values to register `x31` (0xc001c0de for pass) for simulator-agnostic checking

**Sources:** [README.md38-73](https://github.com/tenstorrent/riescue/blob/07cefde3/README.md?plain=1#L38-L73) [riescue/riescued.py326-409](https://github.com/tenstorrent/riescue/blob/07cefde3/riescue/riescued.py#L326-L409)

Refresh this wiki
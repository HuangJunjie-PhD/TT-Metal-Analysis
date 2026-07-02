---
project: riscv_arch_tests
github: https://github.com/tenstorrent/riscv_arch_tests
deepwiki: https://deepwiki.com/tenstorrent/riscv_arch_tests/2-test-architecture)
---

# Test Architecture

Relevant source files
*   [README.md](https://github.com/tenstorrent/riscv_arch_tests/blob/e54223b0/README.md?plain=1)

## Purpose and Scope

This document describes the overall architecture and organization of the RISC-V test suite contained in this repository. The test architecture implements a hierarchical structure that systematically validates RISC-V implementations across different privilege modes, virtualization settings, paging configurations, and ISA extensions. For detailed information about test organization patterns, see [Test Organization](https://deepwiki.com/tenstorrent/riscv_arch_tests/2.1-test-organization). For test file internals, see [Test File Structure](https://deepwiki.com/tenstorrent/riscv_arch_tests/2.2-test-file-structure). For specific atomic operation test implementations, see [Atomic Memory Operations Tests](https://deepwiki.com/tenstorrent/riscv_arch_tests/2.3-atomic-memory-operations-tests).

The architecture enables comprehensive validation through self-checking assembly tests that execute on both RISC-V hardware designs and instruction set simulators like Whisper and Spike.

Sources: [README.md 4-9](https://github.com/tenstorrent/riscv_arch_tests/blob/e54223b0/README.md?plain=1#L4-L9)

The test architecture follows a structured approach where each test contains both the validation logic and a mini operating system to manage execution, enabling autonomous test execution across different RISC-V configurations.

Sources: [README.md 4-9](https://github.com/tenstorrent/riscv_arch_tests/blob/e54223b0/README.md?plain=1#L4-L9)[README.md 52-63](https://github.com/tenstorrent/riscv_arch_tests/blob/e54223b0/README.md?plain=1#L52-L63)

## Architectural Overview

The test architecture implements a multi-layered system built around the `riscv_tests/` directory structure. The architecture consists of hierarchical test organization, self-contained test files, and integration infrastructure that connects to external RISC-V simulators.

**Test Architecture Components**

The architecture separates concerns between test content organization (`riscv_tests/`), execution infrastructure (`infra/`), and simulator integration (submodules). Each test file is self-contained with embedded mini-OS functionality and test-specific validation logic.

Sources: [README.md 134-185](https://github.com/tenstorrent/riscv_arch_tests/blob/e54223b0/README.md?plain=1#L134-L185)[README.md 52-63](https://github.com/tenstorrent/riscv_arch_tests/blob/e54223b0/README.md?plain=1#L52-L63)

## Directory Structure and Test Classification

The `riscv_tests/` directory implements a taxonomic classification system that mirrors RISC-V architectural specifications. This structure enables systematic testing across all combinations of RISC-V operating modes and extensions.

**Directory Hierarchy Mapping**

Each directory level constrains the test environment according to RISC-V specifications:

| Directory Level | Purpose | Constraints |
| --- | --- | --- |
| `bare_metal/` vs `h_ext/` | Virtualization mode | Non-hypervisor vs hypervisor extension tests |
| `machine/`, `supervisor/`, `user/` | Privilege level | M-mode only, MS-mode, or MSU-mode support |
| `paging_bare/`, `paging_sv*` | Memory management | Virtual memory configuration |
| `rv_*` directories | ISA extensions | Specific instruction set extensions |

The leaf directories contain the actual test files: assembly source (`.S`), linker scripts (`.ld`), and test manifest lists (`.list`).

Sources: [README.md 134-185](https://github.com/tenstorrent/riscv_arch_tests/blob/e54223b0/README.md?plain=1#L134-L185)[README.md 34-48](https://github.com/tenstorrent/riscv_arch_tests/blob/e54223b0/README.md?plain=1#L34-L48)

## Test File Architecture

Each test implements a dual-layer architecture consisting of an embedded mini-OS (`.text` section) and test-specific validation code (`.code` section). This structure enables autonomous test execution with comprehensive error handling and result validation.

**Test File Internal Structure**

**Memory Section Organization**

| Section | Base Address | Purpose | Key Symbols |
| --- | --- | --- | --- |
| `.text` | `0x80000000` | Mini-OS code | `os_init`, `os_main`, trap handlers |
| `.code` | `0x80010000` | Test cases | `test1`, `test2`, ..., `testN` |
| `.data` | `0xb3cb4000` | Test data | Variable storage |
| `.os_data` | `0x94ad0000` | OS variables | `os_test_sequence`, state variables |
| `.os_stack` | `0x90000000` | OS stack | Stack for OS operations |
| `.io_htif` | `0x70000000` | Host interface | End-of-test signaling |

**Test Case Structure Pattern**

Each test follows a standardized pattern within the `.code` section:

1.   **Label Definition**: `testN:` where N is the test number
2.   **Operand Setup**: Register initialization using `lui`, `add`, `sll` instructions
3.   **Instruction Execution**: The target RISC-V instruction being validated
4.   **Result Verification**: Compare actual vs expected results
5.   **Branch Decision**: Jump to `passed` or `failed` based on verification

Sources: [README.md 52-85](https://github.com/tenstorrent/riscv_arch_tests/blob/e54223b0/README.md?plain=1#L52-L85)

## Memory Architecture and Linker Script Organization

The test architecture implements a standardized memory layout defined through linker scripts (`.ld` files) that ensure consistent memory organization across all test variants. This layout supports both the mini-OS functionality and test-specific memory requirements.

**Standard Memory Layout**

**Extension-Specific Memory Regions**

Different ISA extensions require specialized memory layouts defined in their respective linker scripts:

| Extension | Specialized Sections | Purpose |
| --- | --- | --- |
| RV-A (Atomic) | `.amo_*`, `.lr_*`, `.sc_*` | Atomic memory operation test regions |
| RV-V (Vector) | Vector-specific data sections | Vector register test data |
| RV-F/D | Floating-point data sections | FP register and rounding mode tests |

**Key Memory Management Features**

1.   **Predictable Addressing**: Fixed base addresses enable consistent debugging and analysis
2.   **Isolation**: Separate regions prevent interference between OS and test code
3.   **Extension Support**: Configurable sections accommodate different ISA requirements
4.   **Host Interface**: Dedicated `.io_htif` region for simulator communication

The linker scripts use `PROVIDE()` statements to export key symbols like section boundaries and entry points, enabling the mini-OS to dynamically locate test code and manage memory allocation.

Sources: [README.md 52-85](https://github.com/tenstorrent/riscv_arch_tests/blob/e54223b0/README.md?plain=1#L52-L85)

## Test Execution Model

The test execution follows a well-defined flow managed by the mini OS included in each test file. This execution model handles test scheduling, exception management, and result reporting.

The execution model supports several key mechanisms:

1.   **Test Scheduling**: Tests are selected in sequence from the `os_test_sequence` array defined in each test file
2.   **Exception Handling**: A trap handler manages exceptions, checking if they were expected or unexpected
3.   **Self-Checking**: Tests include verification code that validates results against expected values
4.   **End-of-Test Signaling**: Tests signal completion via a host interface (HTIF)

Sources: [riscv_tests/bare_metal/machine/paging_bare/rv_a/rv64a_0.S 992-1192](https://github.com/tenstorrent/riscv_arch_tests/blob/e54223b0/riscv_tests/bare_metal/machine/paging_bare/rv_a/rv64a_0.S#L992-L1192)[riscv_tests/bare_metal/machine/paging_bare/rv_a/rv64a_0.S 1197-1433](https://github.com/tenstorrent/riscv_arch_tests/blob/e54223b0/riscv_tests/bare_metal/machine/paging_bare/rv_a/rv64a_0.S#L1197-L1433)[riscv_tests/bare_metal/machine/paging_bare/rv_a/rv64a_0.S 2733-2932](https://github.com/tenstorrent/riscv_arch_tests/blob/e54223b0/riscv_tests/bare_metal/machine/paging_bare/rv_a/rv64a_0.S#L2733-L2932)

## Self-Validation and Result Verification

The test architecture implements autonomous validation through embedded self-checking logic that compares instruction results against pre-computed expected values. This eliminates the need for external test oracles and enables tests to run independently on any RISC-V implementation.

**Self-Checking Flow Implementation**

**Validation Mechanism Components**

1.   **Expected Value Computation**: Tests include hardcoded expected results calculated during test generation
2.   **Inline Comparison**: Each test uses `bne` (branch not equal) or `beq` (branch equal) to compare actual vs expected
3.   **Immediate Branching**: Failed comparisons immediately jump to `failed` label, successful ones continue to `passed`
4.   **Exception Validation**: Trap handlers verify expected exception cause codes and program counter values

**Test Result Propagation**

Tests signal results through standardized control flow:

*   **Success Path**: `passed` label increments test counter and continues to next test
*   **Failure Path**: `failed` label sets failure flag and jumps to end-of-test mechanism
*   **End-of-Test**: Uses Host-Target Interface (HTIF) to signal final test status to simulator

This architecture enables tests to execute completely autonomously while providing detailed failure diagnostics through instruction traces and register dumps.

Sources: [README.md 67-85](https://github.com/tenstorrent/riscv_arch_tests/blob/e54223b0/README.md?plain=1#L67-L85)

## Exception Handling

The test architecture includes a sophisticated exception handling mechanism to manage both expected and unexpected exceptions during test execution.

The exception handling mechanism provides:

1.   **Trap Vector Setup**: Configures trap handlers for different exception types
2.   **Exception Checking**: Validates if exceptions match expected conditions (cause, PC, etc.)
3.   **Exception Recovery**: Allows tests to continue after expected exceptions
4.   **Error Handling**: Signals test failure for unexpected exceptions

This robust exception management ensures that tests properly validate both normal operation and exception conditions in RISC-V implementations.

Sources: [riscv_tests/bare_metal/machine/paging_bare/rv_a/rv64a_0.S 1197-1433](https://github.com/tenstorrent/riscv_arch_tests/blob/e54223b0/riscv_tests/bare_metal/machine/paging_bare/rv_a/rv64a_0.S#L1197-L1433)[riscv_tests/bare_metal/machine/paging_bare/rv_a/rv64a_0.S 496-523](https://github.com/tenstorrent/riscv_arch_tests/blob/e54223b0/riscv_tests/bare_metal/machine/paging_bare/rv_a/rv64a_0.S#L496-L523)

## Supported Test Categories

The test architecture supports multiple categories of RISC-V tests across different parts of the specification:

| Test Category | Description | Example Test Areas |
| --- | --- | --- |
| RV64-I | Base integer instruction set | Integer computation, branching, memory operations |
| RV-M | Integer multiplication and division | Multiply, divide, remainder operations |
| RV-F | Single-precision floating-point | Float operations, conversions |
| RV-D | Double-precision floating-point | Double float operations, conversions |
| RV-C | Compressed instructions | 16-bit instruction encoding |
| RV-V | Vector extensions | Vector operations with different configurations |
| RV-A | Atomic memory operations | Atomic load/store, compare-and-swap |
| Zfh | Half-precision floating-point | Half float operations |
| Zba, Zbb, Zbc, Zbs | Bit manipulation extensions | Bit counting, manipulation operations |

The architecture supports testing each of these categories in various combinations of privilege modes, virtualization settings, and paging configurations, providing comprehensive coverage of the RISC-V specification.

Sources: [README.md 19-29](https://github.com/tenstorrent/riscv_arch_tests/blob/e54223b0/README.md?plain=1#L19-L29)

Dismiss
Refresh this wiki

Enter email to refresh
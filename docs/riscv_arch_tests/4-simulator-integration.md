---
project: riscv_arch_tests
github: https://github.com/tenstorrent/riscv_arch_tests
deepwiki: https://deepwiki.com/tenstorrent/riscv_arch_tests/4-simulator-integration
---

# Simulator Integration

Relevant source files
*   [.gitmodules](https://github.com/tenstorrent/riscv_arch_tests/blob/e54223b0/.gitmodules)

This document describes how RISC-V instruction set simulators (ISS) are integrated into the RISC-V architectural testing framework. The framework currently supports two simulators: Whisper and Spike, which are used to execute and validate the architectural tests.

For specific details about each simulator's configuration, see [Whisper Configuration](https://deepwiki.com/tenstorrent/riscv_arch_tests/4.1-whisper-configuration) and [Spike Integration](https://deepwiki.com/tenstorrent/riscv_arch_tests/4.2-spike-integration). For information about running tests using these simulators, see [Test Execution](https://deepwiki.com/tenstorrent/riscv_arch_tests/3.1-test-execution).

## Simulator Overview

The RISC-V architectural testing framework incorporates two instruction set simulators as Git submodules:

1.   **Whisper**: A cycle-accurate RISC-V simulator developed by Tenstorrent
2.   **Spike**: The official RISC-V ISA reference simulator

These simulators provide the execution environment for running the architectural tests, allowing for verification of RISC-V implementations against the ISA specification without requiring physical hardware.

Sources: [.gitmodules 1-6](https://github.com/tenstorrent/riscv_arch_tests/blob/e54223b0/.gitmodules#L1-L6)[README.md 6-7](https://github.com/tenstorrent/riscv_arch_tests/blob/e54223b0/README.md?plain=1#L6-L7)[README.md 92-105](https://github.com/tenstorrent/riscv_arch_tests/blob/e54223b0/README.md?plain=1#L92-L105)

## Integration Architecture

The simulators are integrated into the repository as Git submodules, which allows the framework to reference specific versions of the simulators while keeping them as separate repositories.

The test infrastructure, primarily through the `quals.py` script, provides a unified interface for running tests on either simulator. This allows users to validate tests against both simulators, ensuring the correctness of RISC-V implementations from multiple reference points.

Sources: [.gitmodules 1-6](https://github.com/tenstorrent/riscv_arch_tests/blob/e54223b0/.gitmodules#L1-L6)[README.md 91-105](https://github.com/tenstorrent/riscv_arch_tests/blob/e54223b0/README.md?plain=1#L91-L105)

## Test Execution Flow

When executing a test on one of the integrated simulators, the following flow occurs:

The `quals.py` script handles:

1.   Compiling the test if needed (when `--pre-compiled` is not specified)
2.   Invoking the selected simulator with the appropriate parameters
3.   Capturing and interpreting the simulator output to determine test success or failure

Sources: [README.md 92-105](https://github.com/tenstorrent/riscv_arch_tests/blob/e54223b0/README.md?plain=1#L92-L105)

## Simulator Selection and Configuration

The framework provides several ways to specify which simulator to use and how to configure it:

1.   **Command-line selection**: Using the `--iss` parameter with `quals.py`

```
./infra/quals.py --quals_file <quals_file> --iss <whisper/spike>
```
2.   **Vector length configuration**: For vector extension tests, the `--vlen` parameter configures the vector length

```
./infra/quals.py --quals_file <quals_file> --iss <whisper/spike> --vlen <128/256>
```
3.   **Configuration in quals file**: The simulator can also be specified within the qualification file itself

The command-line specification takes precedence over the quals file configuration, allowing for easy switching between simulators when debugging issues.

Sources: [README.md 97-102](https://github.com/tenstorrent/riscv_arch_tests/blob/e54223b0/README.md?plain=1#L97-L102)

## Test Result Interpretation

The simulators execute the test binaries and produce output that indicates whether the test passed or failed. Test failures can occur in two primary ways:

1.   **Self-checking mismatch**: The test includes self-checking code that verifies the expected results. If the actual result doesn't match the expected result, the test jumps to a failure routine.

2.   **Exception handling**: If an unexpected exception occurs during test execution, the trap handler processes it and indicates a test failure.

The end-of-test mechanism, common to both simulators, follows a semi-standard approach established by Spike for signaling test completion and status.

Sources: [README.md 107-110](https://github.com/tenstorrent/riscv_arch_tests/blob/e54223b0/README.md?plain=1#L107-L110)

## Debugging Test Failures

When a test fails on a simulator, the framework provides mechanisms to debug the issue:

1.   **Disassembly**: Disassemble the test binary to understand its structure

```
riscv64-unknown-linux-gnu-objdump <test_elf> -D > test.dis
```
2.   **Instruction tracing**: Compare the simulator's instruction trace with the disassembly to identify where the test failed

3.   **Failure analysis**: Determine if the failure was due to a self-checking mismatch or an exception

    *   For self-checking mismatches, trace back from the `<test_failed>` subroutine
    *   For exceptions, examine the `<ecall_from_machine>` subroutine

The simulators offer different levels of detail in their traces:

*   Spike includes code labels in the trace, making it easier to correlate with the disassembly
*   Whisper's traces require manual correlation with the disassembly

Sources: [README.md 107-110](https://github.com/tenstorrent/riscv_arch_tests/blob/e54223b0/README.md?plain=1#L107-L110)

## Building the Simulators

Before running tests, the simulators must be built from their respective submodules:

1.   **Clone and initialize submodules**:

```
git submodule update --init --recursive
```
2.   **Build Whisper**: Follow the build instructions in the Whisper repository

3.   **Build Spike**: Follow the build instructions in the Spike repository

The test infrastructure expects these simulators to be properly built and available in their respective directories.

Sources: [README.md 91-96](https://github.com/tenstorrent/riscv_arch_tests/blob/e54223b0/README.md?plain=1#L91-L96)

## Common Use Cases

### Running Tests on Both Simulators

The framework provides a convenient script to run all tests on both simulators:

```
./test_all.bash
```

This helps ensure that tests pass consistently across different simulator implementations, increasing confidence in the correctness of the RISC-V implementation being tested.

### Switching Between Simulators for Debugging

When a test fails on one simulator but passes on another, it can be useful to compare the behavior across simulators:

```
./infra/quals.py --pre-compiled --quals_file <quals_file> --iss whisper
./infra/quals.py --pre-compiled --quals_file <quals_file> --iss spike
```

Comparing the execution traces can help identify differences in interpretation of the RISC-V specification.

Sources: [README.md 103-105](https://github.com/tenstorrent/riscv_arch_tests/blob/e54223b0/README.md?plain=1#L103-L105)

Dismiss
Refresh this wiki

Enter email to refresh
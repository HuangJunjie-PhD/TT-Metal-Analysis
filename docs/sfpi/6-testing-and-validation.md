---
project: sfpi
github: https://github.com/tenstorrent/sfpi
deepwiki: https://deepwiki.com/tenstorrent/sfpi/6-testing-and-validation
---

# Testing and Validation

Relevant source files
*   [tests/wormhole/sfpi/ckernel.cc](https://github.com/tenstorrent/sfpi/blob/ed989b9d/tests/wormhole/sfpi/ckernel.cc)
*   [tests/wormhole/sfpi/gold/ckernel.S](https://github.com/tenstorrent/sfpi/blob/ed989b9d/tests/wormhole/sfpi/gold/ckernel.S)
*   [tests/wormhole/sfpi/gold/coverage.S](https://github.com/tenstorrent/sfpi/blob/ed989b9d/tests/wormhole/sfpi/gold/coverage.S)
*   [xfails/binutils.xfail](https://github.com/tenstorrent/sfpi/blob/ed989b9d/xfails/binutils.xfail)
*   [xfails/g++.xfail](https://github.com/tenstorrent/sfpi/blob/ed989b9d/xfails/g++.xfail)
*   [xfails/gas.xfail](https://github.com/tenstorrent/sfpi/blob/ed989b9d/xfails/gas.xfail)
*   [xfails/gcc.xfail](https://github.com/tenstorrent/sfpi/blob/ed989b9d/xfails/gcc.xfail)
*   [xfails/ld.xfail](https://github.com/tenstorrent/sfpi/blob/ed989b9d/xfails/ld.xfail)

**Purpose:** This page documents the comprehensive testing infrastructure that validates the SFPI toolchain, including instruction-level correctness tests, kernel function tests, compiler optimization tests, and upstream GCC/Binutils test suite integration. This page focuses on the test organization, execution flow, and validation mechanisms.

For information about building the toolchain with test support, see [Building the SFPI Toolchain](https://deepwiki.com/tenstorrent/sfpi/5-building-the-sfpi-toolchain). For information about integration with downstream projects, see [Integration and Usage](https://deepwiki.com/tenstorrent/sfpi/7-integration-and-usage).

* * *

## Testing Infrastructure Architecture

The SFPI testing system employs a multi-layered validation strategy that combines custom SFPU-specific tests with comprehensive upstream compiler test suites. All tests execute through the DejaGnu test framework using QEMU or alternative simulators.

**Test Infrastructure Components**

**Sources:**[tests/wormhole/sfpi/coverage.cc](https://github.com/tenstorrent/sfpi/blob/ed989b9d/tests/wormhole/sfpi/coverage.cc)[tests/wormhole/sfpi/ckernel.cc](https://github.com/tenstorrent/sfpi/blob/ed989b9d/tests/wormhole/sfpi/ckernel.cc)[tests/wormhole/sfpi/gold/coverage.S 1-30](https://github.com/tenstorrent/sfpi/blob/ed989b9d/tests/wormhole/sfpi/gold/coverage.S#L1-L30)[tests/wormhole/sfpi/gold/ckernel.S 1-40](https://github.com/tenstorrent/sfpi/blob/ed989b9d/tests/wormhole/sfpi/gold/ckernel.S#L1-L40)

* * *

## Test Organization by Architecture

Tests are organized by target architecture, with each architecture maintaining its own test suite and gold standards. The directory structure ensures platform-specific validation.

**Test Directory Structure**

| Architecture | Test Files | Gold Standards | Purpose |
| --- | --- | --- | --- |
| Wormhole | coverage.cc, ckernel.cc, regmov.cc, kernels.cc | coverage.S, ckernel.S, regmov.S, kernels_int, kernels_float | Full validation suite with 51 hardware operations |
| Blackhole | coverage.cc, ckernel.cc | coverage.S, ckernel.S | Validation for 54 operations including sfparecip, sfpmul24 |
| Grayskull | coverage.cc, regmov.cc | regmov.S | Basic validation and optimization tests |

**Sources:**[tests/wormhole/sfpi/](https://github.com/tenstorrent/sfpi/blob/ed989b9d/tests/wormhole/sfpi/)[tests/blackhole/sfpi/](https://github.com/tenstorrent/sfpi/blob/ed989b9d/tests/blackhole/sfpi/)[tests/grayskull/sfpi/](https://github.com/tenstorrent/sfpi/blob/ed989b9d/tests/grayskull/sfpi/)

* * *

## SFPU Instruction Coverage Tests

The `coverage.cc` file provides exhaustive validation of SFPU hardware intrinsics by testing each instruction type with various operand combinations. Each test function validates a specific operation category.

**Coverage Test Function Mapping**

**Key Test Categories in coverage.cc:**

| Test Function | Instructions Tested | Line Range | Purpose |
| --- | --- | --- | --- |
| `test_load_store()` | SFPLOAD, SFPSTORE | coverage.cc:1-30 | Memory operations with different addressing modes |
| `test_add()` | SFPADD, SFPADDI | coverage.cc:31-80 | Addition with registers and immediates |
| `test_sub()` | SFPMAD (L11 mode) | coverage.cc:81-130 | Subtraction via multiply-add with -1 |
| `test_mul()` | SFPMUL, SFPMULI | coverage.cc:131-180 | Multiplication operations |
| `test_mad()` | SFPMAD | coverage.cc:181-220 | Fused multiply-accumulate |
| `test_dreg_conditional_const()` | SFPSETCC, SFPPUSHC, SFPPOPC, SFPCOMPC | coverage.cc:574-650 | Predicated execution with condition stack |
| `test_bitwise()` | SFPOR, SFPAND, SFPNOT, SFPXOR | coverage.cc:1030-1094 | Bitwise logical operations |
| `test_lut()` | SFPLUT | coverage.cc:1512-1522 | Single lookup table operations |
| `test_stochrnd()` | SFPSTOCHRND | coverage.cc:1608-1656 | Stochastic rounding for quantization |

**Sources:**[tests/wormhole/sfpi/coverage.cc](https://github.com/tenstorrent/sfpi/blob/ed989b9d/tests/wormhole/sfpi/coverage.cc)[tests/wormhole/sfpi/gold/coverage.S 1-1700](https://github.com/tenstorrent/sfpi/blob/ed989b9d/tests/wormhole/sfpi/gold/coverage.S#L1-L1700)

* * *

## Kernel Function Tests

The `ckernel.cc` file validates high-level mathematical kernels that implement complex operations like exponential, logarithm, GELU, and sigmoid. These tests ensure correct composition of multiple SFPU instructions.

**Kernel Function Test Structure**

**Key Mathematical Kernels:**

| Function | Template Parameters | Implementation Strategy | Gold Standard Lines |
| --- | --- | --- | --- |
| `calculate_log` | APPROXIMATION_MODE | Extract exponent, normalize mantissa, polynomial fit | ckernel.S:8-39 |
| `calculate_exponential` | APPROXIMATION_MODE, ZERO_NEGATIVE, SCALE_EN | Fast: shift-based; Exact: series + reciprocal | ckernel.S:42-508 |
| `calculate_gelu` | APPROXIMATION_MODE | Polynomial approximation + LUT | ckernel.S:928-1040 |
| `calculate_sigmoid` | APPROXIMATION_MODE | LUT-based with 0.5 offset | ckernel.S:738-754 |
| `calculate_tanh` | APPROXIMATION_MODE | Direct LUT lookup | ckernel.S:760-774 |
| `calculate_reciprocal` | APPROXIMATION_MODE, ITERATIONS | Newton-Raphson iterations (2-3) | ckernel.cc:470-488 |
| `calculate_sqrt` | APPROXIMATION_MODE, ITERATIONS, RECIPROCAL_ITERATIONS | Reciprocal root method or magic constant | ckernel.cc:491-533 |

**Example: Exponential Function Assembly Validation**

The approximate exponential mode [ckernel.cc 236-259](https://github.com/tenstorrent/sfpi/blob/ed989b9d/ckernel.cc#L236-L259) generates the following expected assembly [ckernel.S 42-62](https://github.com/tenstorrent/sfpi/blob/ed989b9d/ckernel.S#L42-L62):

```
_Z21calculate_exponentialILb1ELb1ELb0EEvt.isra.0:
    TTREPLAY    0, 10, 1, 1
    SFPLOAD     L0, 0, 0, 3
    SFPMAD      L0, L0, L12, L13, 0
    SFPNOP
    SFPIADD     L0, L14, 0, 4        # Extract integer bits
    SFPSHFT     L0, L0, 8, 1         # Shift to exponent position
    SFPSTORE    0, L0, 0, 3
```

**Sources:**[tests/wormhole/sfpi/ckernel.cc 1-1200](https://github.com/tenstorrent/sfpi/blob/ed989b9d/tests/wormhole/sfpi/ckernel.cc#L1-L1200)[tests/wormhole/sfpi/gold/ckernel.S 1-1500](https://github.com/tenstorrent/sfpi/blob/ed989b9d/tests/wormhole/sfpi/gold/ckernel.S#L1-L1500)

* * *

## Test Execution Flow

Tests execute through the DejaGnu framework, which orchestrates compilation, execution, and validation. The framework supports multiple simulator backends and handles test result aggregation.

**DejaGnu Test Execution Pipeline**

**Simulator Backend Selection:**

The test framework supports multiple RISC-V simulators for test execution:

| Simulator | Configuration | Use Case | Speed |
| --- | --- | --- | --- |
| QEMU | `qemu-riscv32 -cpu rv32` | Default simulator, best compatibility | Fast |
| Spike | `spike --isa=rv32imxttwh` | Reference ISA simulator | Medium |
| GDB Simulator | `riscv32-unknown-elf-run` | Debugging and step-through | Slow |

**Test Execution Example:**

`# Run SFPI-specific tests with QEMUruntest --tool=gcc \        --target=riscv32-unknown-elf \        --target_board=riscv-sim \        tt.exp # Run with Spike simulatorruntest --tool=gcc \        --target=riscv32-unknown-elf \        --target_board=riscv-sim-spike \        tt.exp # Run GCC torture testsruntest --tool=gcc \        --target=riscv32-unknown-elf \        --target_board=riscv-sim \        dg.exp=torture/execute`
**Sources:** Build system documentation in [scripts/build.sh](https://github.com/tenstorrent/sfpi/blob/ed989b9d/scripts/build.sh)

* * *

## Known Failure Management

The SFPI toolchain maintains `.xfail` files that document known test failures from upstream GCC, G++, Binutils, and linker test suites. These files are used by the `local-xfails.py` script to filter expected failures from test results.

**Failure Filtering Process**

**xfail File Format:**

The `.xfail` files use a simple format to specify known failures:

```
multilibs: riscv-sim/*
FAIL: gcc.c-torture/execute/20000822-1.c   -O0  execution test
FAIL: gcc.c-torture/execute/20000822-1.c   -O1  execution test

multilibs: riscv-sim/cpu=tt-gs riscv-sim/cpu=tt-wh
FAIL: gcc.dg/loop-unswitch-6.c (test for excess errors)
```

| Component | Meaning | Example |
| --- | --- | --- |
| `multilibs:` | Specifies which multilib configurations this failure applies to | `riscv-sim/*` (all), `riscv-sim/cpu=tt-wh` (Wormhole only) |
| `FAIL:` | Test name and result type | `gcc.c-torture/execute/20000822-1.c -O0 execution test` |
| Comments | Lines starting with `#` | `# these fails occur with stock gcc 12.4.0` |

**Known Failure Categories:**

| File | Failure Count | Primary Issues |
| --- | --- | --- |
| gcc.xfail | ~130 failures | Nested functions, printf variations, floating-point edge cases, trampolines |
| g++.xfail | ~60 failures | Pure virtual functions, patchable function entry, spec barriers, ABI tests |
| binutils.xfail | 0 failures | No additional failures beyond stock binutils 2.39 |
| ld.xfail | ~30 failures | LTO plugin issues, garbage collection with KEEP, symbol management |

**Example Known Failures from gcc.xfail [xfails/gcc.xfail 1-134](https://github.com/tenstorrent/sfpi/blob/ed989b9d/xfails/gcc.xfail#L1-L134):**

*   Nested function execution tests (stock GCC 12.4.0 issues)
*   Floating-point conversion tests (`fp-double-convert-float-1.c`, `fp-uint64-convert-double-1.c`)
*   Printf family functions (`fprintf-2.c`, `printf-2.c`, `user-printf.c`)
*   Stack alignment with nested functions (`nested-5.c`, `nested-6.c`)
*   Trampoline execution (`trampoline-1.c`)

**Example Known Failures from g++.xfail [xfails/g++.xfail 1-61](https://github.com/tenstorrent/sfpi/blob/ed989b9d/xfails/g++.xfail#L1-L61):**

*   Patchable function entry tests (nop/NOP/SWYM counting)
*   Spec barrier tests
*   Pure virtual function ABI tests
*   Decomposition tests (`decomp2.C`)
*   Timeout issues in checking builds (`pr31863.C`)

**Sources:**[xfails/gcc.xfail 1-134](https://github.com/tenstorrent/sfpi/blob/ed989b9d/xfails/gcc.xfail#L1-L134)[xfails/g++.xfail 1-61](https://github.com/tenstorrent/sfpi/blob/ed989b9d/xfails/g++.xfail#L1-L61)[xfails/binutils.xfail 1-2](https://github.com/tenstorrent/sfpi/blob/ed989b9d/xfails/binutils.xfail#L1-L2)[xfails/ld.xfail 1-30](https://github.com/tenstorrent/sfpi/blob/ed989b9d/xfails/ld.xfail#L1-L30)

* * *

## Upstream Test Suite Integration

The SFPI toolchain integrates the complete upstream GCC, G++, and Binutils test suites to validate compatibility with standard compiler behavior. These tests ensure that the SFPU extensions do not break existing RISC-V functionality.

**Test Suite Coverage**

| Test Suite | Test Count | Categories | Validation Focus |
| --- | --- | --- | --- |
| GCC (gcc.exp) | ~15,000 tests | c-torture, gcc.dg, gcc.dg/torture | C language correctness, optimization passes |
| G++ (g++.exp) | ~25,000 tests | g++.dg, g++.target, g++.old-deja | C++ language features, templates, exceptions |
| Binutils (gas) | ~2,000 tests | gas/riscv, gas/all | Assembler correctness, RISC-V encoding |
| Binutils (ld) | ~1,500 tests | ld-riscv, ld-plugin, ld-elf | Linker behavior, LTO, relocation |

**Test Invocation in Build System:**

The build script [scripts/build.sh](https://github.com/tenstorrent/sfpi/blob/ed989b9d/scripts/build.sh) provides options to run different test suites:

`# Run all tests (GCC + G++ + Binutils + TT-specific)./scripts/build.sh --test # Run only GCC tests./scripts/build.sh --test-gcc # Run only Binutils tests  ./scripts/build.sh --test-binutils # Run only TT-specific SFPI tests./scripts/build.sh --test-tt # Run tests with DejaGnu and simulator setup./scripts/build.sh --dejagnu --sim=qemu`
**TT-Specific Test Integration:**

Custom SFPI tests are integrated through DejaGnu `.exp` files:

*   `tt.exp`: Main TT test orchestration
*   `sfpi.exp`: SFPU instruction validation

These test files compile the coverage, ckernel, and regmov tests, execute them, and compare outputs against gold standards.

**Sources:**[scripts/build.sh](https://github.com/tenstorrent/sfpi/blob/ed989b9d/scripts/build.sh) build script test invocation

* * *

## Assembly Validation Process

Assembly validation ensures that the SFPI compiler generates correct SFPU instruction sequences. The process compares generated assembly against gold standard files with exact instruction matching.

**Assembly Comparison Flow**

**Example Assembly Validation:**

For the `test_load_store()` function [coverage.cc test function](https://github.com/tenstorrent/sfpi/blob/ed989b9d/coverage.cc%20test%20function) the expected assembly [tests/wormhole/sfpi/gold/coverage.S 8-30](https://github.com/tenstorrent/sfpi/blob/ed989b9d/tests/wormhole/sfpi/gold/coverage.S#L8-L30) is:

`_Z15test_load_storev:    SFPLOAD  L2, 0, 0, 3      # Load from dreg[0] to L2, mode 3    SFPLOAD  L0, 2, 0, 3      # Load from dreg[2] to L0, mode 3    SFPLOAD  L1, 4, 0, 3      # Load from dreg[4] to L1, mode 3    SFPSTORE 6, L2, 0, 3      # Store L2 to dreg[6], mode 3    SFPSTORE 8, L0, 0, 3      # Store L0 to dreg[8], mode 3    SFPSTORE 10, L1, 0, 3     # Store L1 to dreg[10], mode 3`
The validation checks:

*   Correct instruction mnemonics (SFPLOAD, SFPSTORE)
*   Correct register operands (L0, L1, L2)
*   Correct destination/source register numbers (0, 2, 4, 6, 8, 10)
*   Correct addressing mode (3 = ADDR_MODE_NOINC for Wormhole)

**Critical Assembly Patterns:**

| Pattern | Description | Example | Gold Standard Lines |
| --- | --- | --- | --- |
| Load-Store Sequence | Memory access patterns | `SFPLOAD L0, 0, 0, 3` → `SFPSTORE 2, L0, 0, 3` | coverage.S:11-28 |
| Predicated Execution | Conditional code | `SFPSETCC` → `SFPPUSHC` → `SFPCOMPC` → `SFPPOPC` | coverage.S:580-650 |
| TTREPLAY | Instruction replay | `TTREPLAY 0, 2, 1, 1` → loop body | coverage.S:37-39 |
| SFPNOP | Pipeline delays | `SFPMUL` → `SFPNOP` → `SFPNOP` → use result | coverage.S:208-210 |

**Sources:**[tests/wormhole/sfpi/gold/coverage.S 1-1700](https://github.com/tenstorrent/sfpi/blob/ed989b9d/tests/wormhole/sfpi/gold/coverage.S#L1-L1700)

* * *

## Runtime Validation

Runtime validation executes compiled tests in a simulator and verifies that outputs match expected values. This validates correct instruction semantics and floating-point behavior.

**kernels_int and kernels_float Format:**

The `kernels_int` and `kernels_float` files contain expected output values in hexadecimal and decimal formats:

```
# kernels_int format (32 hex values per test)
0x00000000 0x00000001 0x00000002 ... (32 values)

# kernels_float format (32 decimal values per test)  
0.0 1.0 2.0 3.14159 ... (32 values)
```

Each test produces 32 values (one for each element in an SFPU tile row), and the test harness compares actual output against these gold standards.

**Sources:** Test infrastructure in build system

* * *

## Test Result Aggregation

Test results are aggregated into `.sum` (summary) and `.log` (detailed) files. The summary files provide counts of passed, failed, and unresolved tests, while log files contain detailed output for debugging.

**Summary File Format:**

```
Test Run By user on Wed Dec 18 10:00:00 2024
Target is riscv32-unknown-elf
Host   is x86_64-pc-linux-gnu

                === gcc Summary ===

# of expected passes            12543
# of unexpected failures        45
# of expected failures          130
# of unresolved testcases       12
# of unsupported tests          234
```

**Interpreting Results:**

| Result Type | Meaning | Action Required |
| --- | --- | --- |
| Expected passes | Tests that passed as expected | None |
| Unexpected failures | New test failures not in .xfail files | Investigation required |
| Expected failures | Failures listed in .xfail files | Already documented |
| Unresolved testcases | Tests that couldn't complete (crash, timeout) | Investigation required |
| Unsupported tests | Tests not applicable to this target | None |

The `local-xfails.py` script processes these summary files to identify only the unexpected failures, filtering out known issues documented in the `.xfail` files.

**Sources:** Test execution in [scripts/build.sh](https://github.com/tenstorrent/sfpi/blob/ed989b9d/scripts/build.sh)

Dismiss
Refresh this wiki

Enter email to refresh
---
project: tt-llk
github: https://github.com/tenstorrent/tt-llk
deepwiki: https://deepwiki.com/tenstorrent/tt-llk/8-testing-framework
---

# Testing Framework

Relevant source files
*   [.github/workflows/build-quasar.yml](https://github.com/tenstorrent/tt-llk/blob/366f58e2/.github/workflows/build-quasar.yml)
*   [.github/workflows/on-pr.yml](https://github.com/tenstorrent/tt-llk/blob/366f58e2/.github/workflows/on-pr.yml)
*   [.github/workflows/setup-and-test.yml](https://github.com/tenstorrent/tt-llk/blob/366f58e2/.github/workflows/setup-and-test.yml)
*   [.gitignore](https://github.com/tenstorrent/tt-llk/blob/366f58e2/.gitignore)
*   [tests/helpers/include/params.h](https://github.com/tenstorrent/tt-llk/blob/366f58e2/tests/helpers/include/params.h)
*   [tests/helpers/ld/memory.blackhole.debug.ld](https://github.com/tenstorrent/tt-llk/blob/366f58e2/tests/helpers/ld/memory.blackhole.debug.ld)
*   [tests/helpers/ld/memory.wormhole.debug.ld](https://github.com/tenstorrent/tt-llk/blob/366f58e2/tests/helpers/ld/memory.wormhole.debug.ld)
*   [tests/python_tests/conftest.py](https://github.com/tenstorrent/tt-llk/blob/366f58e2/tests/python_tests/conftest.py)
*   [tests/python_tests/helpers/golden_generators.py](https://github.com/tenstorrent/tt-llk/blob/366f58e2/tests/python_tests/helpers/golden_generators.py)
*   [tests/python_tests/helpers/test_config.py](https://github.com/tenstorrent/tt-llk/blob/366f58e2/tests/python_tests/helpers/test_config.py)
*   [tests/python_tests/test_eltwise_unary_sfpu.py](https://github.com/tenstorrent/tt-llk/blob/366f58e2/tests/python_tests/test_eltwise_unary_sfpu.py)
*   [tests/requirements.txt](https://github.com/tenstorrent/tt-llk/blob/366f58e2/tests/requirements.txt)
*   [tests/sources/eltwise_unary_sfpu_test.cpp](https://github.com/tenstorrent/tt-llk/blob/366f58e2/tests/sources/eltwise_unary_sfpu_test.cpp)
*   [tests/sources/matmul_and_unary_sfpu_test.cpp](https://github.com/tenstorrent/tt-llk/blob/366f58e2/tests/sources/matmul_and_unary_sfpu_test.cpp)

## Purpose and Scope

The testing framework is a comprehensive Python-based infrastructure for validating Low-Level Kernel (LLK) operations on Tenstorrent hardware. It orchestrates compilation of C++ kernels, execution on Tensix cores, and validation against golden reference models. The framework supports parameterized testing across data formats, operations, and hardware configurations (Wormhole B0, Blackhole, Quasar).

This page covers the overall testing infrastructure and its core components. For specific topics:

*   For compilation and build processes, see [Build System and Toolchain](https://deepwiki.com/tenstorrent/tt-llk/9-build-system-and-toolchain)
*   For CI/CD automation, see [CI/CD Pipeline](https://deepwiki.com/tenstorrent/tt-llk/10-cicd-pipeline)
*   For hardware-specific details, see [Hardware Architecture and Memory Hierarchy](https://deepwiki.com/tenstorrent/tt-llk/1-overview)

Sources: [tests/python_tests/conftest.py 1-421](https://github.com/tenstorrent/tt-llk/blob/366f58e2/tests/python_tests/conftest.py#L1-L421)[tests/python_tests/helpers/test_config.py 1-100](https://github.com/tenstorrent/tt-llk/blob/366f58e2/tests/python_tests/helpers/test_config.py#L1-L100)

* * *

## Test Architecture Overview

The testing framework follows a **producer-consumer** pattern where compilation and execution are separated to enable parallel test execution across multiple hardware runners. Tests are defined in pytest, compiled to ELF binaries, executed on hardware, and validated against golden models.

### High-Level Component Diagram

Sources: [tests/python_tests/helpers/test_config.py 105-434](https://github.com/tenstorrent/tt-llk/blob/366f58e2/tests/python_tests/helpers/test_config.py#L105-L434)[tests/python_tests/conftest.py 98-153](https://github.com/tenstorrent/tt-llk/blob/366f58e2/tests/python_tests/conftest.py#L98-L153)

### Producer-Consumer Test Execution Pattern

The framework implements a two-phase execution model to enable efficient parallel testing:

**Produce Phase**: Compiles all test variants in parallel without requiring hardware access. Each variant is identified by a SHA256 hash of its configuration parameters.

**Consume Phase**: Loads pre-compiled ELFs and executes them on hardware sequentially or with limited parallelism.

Sources: [tests/python_tests/helpers/test_config.py 78-82](https://github.com/tenstorrent/tt-llk/blob/366f58e2/tests/python_tests/helpers/test_config.py#L78-L82)[.github/workflows/setup-and-test.yml 136-150](https://github.com/tenstorrent/tt-llk/blob/366f58e2/.github/workflows/setup-and-test.yml#L136-L150)

* * *

## Core Components

### TestConfig Class

`TestConfig` is the central orchestrator that manages the entire test lifecycle from compilation through validation.

**Key Static Configuration**:

*   `ARCH`, `CHIP_ARCH`: Architecture selection (Wormhole/Blackhole/Quasar)
*   `ARTEFACTS_DIR`: Build output directory (`/tmp/tt-llk-build/`)
*   `SHARED_ELF_DIR`, `SHARED_OBJ_DIR`: Shared compilation artifacts
*   `GXX`, `OBJDUMP`, `GCOV`: RISC-V toolchain paths

**Instance Configuration**:

Sources: [tests/python_tests/helpers/test_config.py 105-434](https://github.com/tenstorrent/tt-llk/blob/366f58e2/tests/python_tests/helpers/test_config.py#L105-L434)

### Test Variant Hash System

Each unique test configuration is identified by a SHA256 hash computed from its parameters:

This enables **incremental compilation** where previously compiled variants are reused across test runs.

Sources: [tests/python_tests/helpers/test_config.py 576-593](https://github.com/tenstorrent/tt-llk/blob/366f58e2/tests/python_tests/helpers/test_config.py#L576-L593)

### Compilation Process

The `build_elfs()` method orchestrates C++ kernel compilation:

**Shared Artifacts** (compiled once per architecture):

*   `tmu-crt0.o`: Startup code
*   `brisc.o`: BRISC control core (Wormhole/Blackhole only)
*   `main_unpack.o`, `main_math.o`, `main_pack.o`: TRISC kernels
*   `coverage.o`: Coverage instrumentation (if enabled)

**Variant-Specific Artifacts**:

*   `build.h`: Auto-generated header with compile-time configuration
*   `test_variant.o`: Test-specific C++ compiled with `build.h`
*   `unpack.elf`, `math.elf`, `pack.elf`: Linked ELF binaries

Sources: [tests/python_tests/helpers/test_config.py 636-736](https://github.com/tenstorrent/tt-llk/blob/366f58e2/tests/python_tests/helpers/test_config.py#L636-L736)[tests/python_tests/helpers/test_config.py 872-1048](https://github.com/tenstorrent/tt-llk/blob/366f58e2/tests/python_tests/helpers/test_config.py#L872-L1048)

### Runtime Parameter Serialization

Runtime parameters are written to L1 memory at address `0x20000` (non-coverage) or `0x6E000` (coverage):

The struct layout is generated dynamically based on test configuration:

*   Tile sizes (pack, unpack_A, unpack_B)
*   Format configuration (7 uint32 values per L1-to-L1 iteration)
*   Stimuli configuration (buffer addresses)
*   Test-specific runtime parameters

Sources: [tests/python_tests/helpers/test_config.py 443-478](https://github.com/tenstorrent/tt-llk/blob/366f58e2/tests/python_tests/helpers/test_config.py#L443-L478)[tests/python_tests/helpers/test_config.py 479-561](https://github.com/tenstorrent/tt-llk/blob/366f58e2/tests/python_tests/helpers/test_config.py#L479-L561)[tests/helpers/include/params.h 1-16](https://github.com/tenstorrent/tt-llk/blob/366f58e2/tests/helpers/include/params.h#L1-L16)

* * *

## Test Execution Flow

### Complete Test Lifecycle

Sources: [tests/python_tests/helpers/test_config.py 1135-1236](https://github.com/tenstorrent/tt-llk/blob/366f58e2/tests/python_tests/helpers/test_config.py#L1135-L1236)

### Hardware Synchronization

Test execution uses **mailbox polling** to detect completion:

1.   **Initialization**: Mailboxes 0-3 set to `0xA3`
2.   **Execution**: TRISCs execute kernels, update mailboxes
3.   **Completion**: All mailboxes reach `0xFF`
4.   **Timeout**: 60 seconds (configurable via pytest `--timeout`)

Sources: [tests/python_tests/helpers/device.py](https://github.com/tenstorrent/tt-llk/blob/366f58e2/tests/python_tests/helpers/device.py) (referenced in test_config.py)

* * *

## Test Parameterization System

### Parameterization Architecture

Tests use a sophisticated parameterization system via `param_config.py`:

**Example Usage**:

This generates all valid combinations, automatically handling constraints like:

*   Float16 input requires `dest_acc=Yes` on Blackhole
*   MX formats require specific `num_faces` values

Sources: [tests/python_tests/helpers/param_config.py](https://github.com/tenstorrent/tt-llk/blob/366f58e2/tests/python_tests/helpers/param_config.py) (referenced), [tests/python_tests/test_eltwise_unary_sfpu.py 114-198](https://github.com/tenstorrent/tt-llk/blob/366f58e2/tests/python_tests/test_eltwise_unary_sfpu.py#L114-L198)

### Template vs Runtime Parameters

**Template Parameters** (compile-time, part of variant hash):

*   `APPROX_MODE`: Approximation mode for SFPU operations
*   `FAST_MODE`: Fast/accurate SFPU mode
*   `MATH_OP`: Mathematical operation enum
*   `IN_TILE_DIMS`: Input tile dimensions
*   `NUM_FACES`: Face count (1, 2, or 4)

**Runtime Parameters** (execution-time, not part of variant hash):

*   `TILE_COUNT`: Number of tiles to process
*   `NUM_BLOCKS`: Blocking for destination register capacity
*   `NUM_TILES_IN_BLOCK`: Tiles per block

This separation enables one compiled ELF to be tested with multiple runtime configurations.

Sources: [tests/python_tests/helpers/test_variant_parameters.py](https://github.com/tenstorrent/tt-llk/blob/366f58e2/tests/python_tests/helpers/test_variant_parameters.py) (referenced in test files)

* * *

## Golden Model System

Golden models compute reference outputs using PyTorch for validation:

**Key Golden Models**:

*   `UnarySFPUGolden`: Implements 50+ SFPU operations (exp, log, sqrt, tanh, etc.)
*   `MatmulGolden`: Matrix multiplication with fidelity masking
*   `ReduceGolden`: Sum/AVG/MAX reduction across dimensions
*   `BroadcastGolden`: Scalar/row/column broadcast operations
*   `TilizeGolden`/`UntilizeGolden`: Data layout transformations

**Fidelity Masking**: Models hardware precision by masking mantissa bits based on `MathFidelity` level (LoFi, HiFi2, HiFi3, HiFi4).

Sources: [tests/python_tests/helpers/golden_generators.py 38-120](https://github.com/tenstorrent/tt-llk/blob/366f58e2/tests/python_tests/helpers/golden_generators.py#L38-L120)[tests/python_tests/helpers/golden_generators.py 417-456](https://github.com/tenstorrent/tt-llk/blob/366f58e2/tests/python_tests/helpers/golden_generators.py#L417-L456)[tests/python_tests/helpers/golden_generators.py 742-859](https://github.com/tenstorrent/tt-llk/blob/366f58e2/tests/python_tests/helpers/golden_generators.py#L742-L859)

### MX Format Quantization

For MX formats (MxFp8R, MxFp8P), golden models must use **quantized stimuli** to match hardware precision:

This ensures the golden model operates on the same quantized values that hardware processes.

Sources: [tests/python_tests/helpers/golden_generators.py 122-231](https://github.com/tenstorrent/tt-llk/blob/366f58e2/tests/python_tests/helpers/golden_generators.py#L122-L231)

* * *

## Stimuli Generation and Memory Layout

### StimuliConfig

`StimuliConfig` manages L1 memory layout for test data:

**Memory Layout Example**:

```
L1 Memory:
0x10000: buffer_A (input operand A)
0x30000: buffer_B (input operand B)
0x50000: buffer_Res (output)
0x20000: RuntimeParams struct
```

Sources: [tests/python_tests/helpers/stimuli_config.py](https://github.com/tenstorrent/tt-llk/blob/366f58e2/tests/python_tests/helpers/stimuli_config.py) (referenced in test_config.py)

### Pack/Unpack Pipeline

Data format conversion between Python and hardware:

**Supported Formats**:

*   Standard: Float16, Float16_b, Float32
*   Block FP: Bfp8_b (block floating point)
*   Microscaling: MxFp8R, MxFp8P
*   Integer: Int32, Int16, Int8

Sources: [tests/python_tests/helpers/pack.py](https://github.com/tenstorrent/tt-llk/blob/366f58e2/tests/python_tests/helpers/pack.py)[tests/python_tests/helpers/unpack.py](https://github.com/tenstorrent/tt-llk/blob/366f58e2/tests/python_tests/helpers/unpack.py) (referenced)

* * *

## Test Example Walkthrough

### Complete SFPU Test Example

[tests/python_tests/test_eltwise_unary_sfpu.py 236-321](https://github.com/tenstorrent/tt-llk/blob/366f58e2/tests/python_tests/test_eltwise_unary_sfpu.py#L236-L321) demonstrates a complete test flow:

Sources: [tests/python_tests/test_eltwise_unary_sfpu.py 236-321](https://github.com/tenstorrent/tt-llk/blob/366f58e2/tests/python_tests/test_eltwise_unary_sfpu.py#L236-L321)

### Corresponding C++ Kernel

The C++ kernel [tests/sources/eltwise_unary_sfpu_test.cpp](https://github.com/tenstorrent/tt-llk/blob/366f58e2/tests/sources/eltwise_unary_sfpu_test.cpp) reads runtime parameters and executes the pipeline:

Sources: [tests/sources/eltwise_unary_sfpu_test.cpp 21-139](https://github.com/tenstorrent/tt-llk/blob/366f58e2/tests/sources/eltwise_unary_sfpu_test.cpp#L21-L139)

* * *

## CI/CD Integration

### GitHub Actions Workflow

The testing framework integrates with CI/CD via [.github/workflows/setup-and-test.yml](https://github.com/tenstorrent/tt-llk/blob/366f58e2/.github/workflows/setup-and-test.yml):

**Job Matrix Generation**:

**Compilation Phase**:

**Execution Phase**:

This enables **parallel compilation** (10 workers) followed by **architecture-specific execution** on dedicated hardware runners.

Sources: [.github/workflows/setup-and-test.yml 54-165](https://github.com/tenstorrent/tt-llk/blob/366f58e2/.github/workflows/setup-and-test.yml#L54-L165)

### Test Splitting Strategy

Tests are split across multiple workers using **duration-based balancing**:

This ensures roughly equal execution time across all workers despite varying test durations.

Sources: [.github/workflows/setup-and-test.yml 89-134](https://github.com/tenstorrent/tt-llk/blob/366f58e2/.github/workflows/setup-and-test.yml#L89-L134)

* * *

## Coverage and Performance Analysis

### Coverage Collection

When `--coverage` is enabled:

1.   **Compilation**: GCC flags `-fprofile-arcs -ftest-coverage` instrument code
2.   **Execution**: `.gcda` files written to L1 memory during test runs
3.   **Collection**: `read_from_device()` retrieves coverage data
4.   **Processing**: `gcov` and `lcov` generate `.info` files
5.   **Merging**: `process_coverage_run_artefacts()` combines per-test coverage
6.   **Upload**: Merged coverage uploaded to Codecov

Coverage data is stored in dedicated L1 regions defined by linker scripts:

*   Wormhole: [tests/helpers/ld/memory.wormhole.debug.ld 38-50](https://github.com/tenstorrent/tt-llk/blob/366f58e2/tests/helpers/ld/memory.wormhole.debug.ld#L38-L50)
*   Blackhole: [tests/helpers/ld/memory.blackhole.debug.ld 36-51](https://github.com/tenstorrent/tt-llk/blob/366f58e2/tests/helpers/ld/memory.blackhole.debug.ld#L36-L51)

Sources: [tests/python_tests/helpers/test_config.py 622-630](https://github.com/tenstorrent/tt-llk/blob/366f58e2/tests/python_tests/helpers/test_config.py#L622-L630)[.github/workflows/setup-and-test.yml 217-275](https://github.com/tenstorrent/tt-llk/blob/366f58e2/.github/workflows/setup-and-test.yml#L217-L275)

### Performance Profiling

Performance tests use the `PerfReport` system:

**Performance Metrics Collected**:

*   Cycle counts per operation
*   Throughput (tiles/second)
*   L1 memory bandwidth
*   Hardware utilization

Sources: [tests/python_tests/conftest.py 284-314](https://github.com/tenstorrent/tt-llk/blob/366f58e2/tests/python_tests/conftest.py#L284-L314)

* * *

## Pytest Configuration and Fixtures

### Core Fixtures

**Tensix Coordinate Assignment**[tests/python_tests/conftest.py 85-90](https://github.com/tenstorrent/tt-llk/blob/366f58e2/tests/python_tests/conftest.py#L85-L90):

Each pytest-xdist worker gets a unique Tensix core location to avoid hardware conflicts.

**Performance Report Collection**[tests/python_tests/conftest.py 284-314](https://github.com/tenstorrent/tt-llk/blob/366f58e2/tests/python_tests/conftest.py#L284-L314):

**Session Management**[tests/python_tests/conftest.py 275-282](https://github.com/tenstorrent/tt-llk/blob/366f58e2/tests/python_tests/conftest.py#L275-L282)[tests/python_tests/conftest.py 316-328](https://github.com/tenstorrent/tt-llk/blob/366f58e2/tests/python_tests/conftest.py#L316-L328):

*   `pytest_sessionstart`: Sends "GO_BUSY" to ARC firmware
*   `pytest_sessionfinish`: Sends "GO_IDLE", merges performance reports, processes coverage

### Command-Line Options

[tests/python_tests/conftest.py 331-395](https://github.com/tenstorrent/tt-llk/blob/366f58e2/tests/python_tests/conftest.py#L331-L395) defines custom pytest options:

| Option | Purpose |
| --- | --- |
| `--coverage` | Enable coverage data collection |
| `--compile-producer` | Only compile ELFs, skip execution |
| `--compile-consumer` | Use pre-compiled ELFs, execute on hardware |
| `--run-simulator` | Execute tests on simulator instead of hardware |
| `--detailed-artefacts` | Generate additional compilation artifacts |
| `--no-debug-symbols` | Compile without `-g` to save disk space |
| `--logging-level` | Set loguru log level (DEBUG, INFO, WARNING, etc.) |
| `--test-order-file` | Load test execution order from file |

Sources: [tests/python_tests/conftest.py 85-395](https://github.com/tenstorrent/tt-llk/blob/366f58e2/tests/python_tests/conftest.py#L85-L395)

* * *

## Architecture-Specific Considerations

### Quasar Architecture

Quasar requires special handling:

1.   **No BRISC**: Uses TRISC-mode boot [tests/python_tests/helpers/test_config.py 615-619](https://github.com/tenstorrent/tt-llk/blob/366f58e2/tests/python_tests/helpers/test_config.py#L615-L619)
2.   **Separate Headers**: Different hw_specific headers [tests/python_tests/conftest.py 42-55](https://github.com/tenstorrent/tt-llk/blob/366f58e2/tests/python_tests/conftest.py#L42-L55)
3.   **Compilation-Only Tests**: CI runs compilation validation without execution [.github/workflows/build-quasar.yml 69-86](https://github.com/tenstorrent/tt-llk/blob/366f58e2/.github/workflows/build-quasar.yml#L69-L86)

### Multi-Architecture Support

`TestConfig.setup_arch()` configures architecture-specific settings:

Sources: [tests/python_tests/helpers/test_config.py 206-235](https://github.com/tenstorrent/tt-llk/blob/366f58e2/tests/python_tests/helpers/test_config.py#L206-L235)

* * *

## Key Infrastructure Files

| File | Purpose |
| --- | --- |
| `test_config.py` | Central orchestrator, compilation, execution |
| `conftest.py` | Pytest configuration, fixtures, session hooks |
| `param_config.py` | Test parameterization and dependency resolution |
| `golden_generators.py` | Reference output generators |
| `device.py` | Hardware interface via ttexalens |
| `stimuli_config.py` | L1 memory layout configuration |
| `stimuli_generator.py` | Test input data generation |
| `pack.py` / `unpack.py` | Format conversion utilities |
| `perf.py` | Performance profiling infrastructure |

Sources: [tests/python_tests/helpers/test_config.py](https://github.com/tenstorrent/tt-llk/blob/366f58e2/tests/python_tests/helpers/test_config.py)[tests/python_tests/conftest.py](https://github.com/tenstorrent/tt-llk/blob/366f58e2/tests/python_tests/conftest.py)[tests/python_tests/helpers/golden_generators.py](https://github.com/tenstorrent/tt-llk/blob/366f58e2/tests/python_tests/helpers/golden_generators.py)

Dismiss
Refresh this wiki

Enter email to refresh
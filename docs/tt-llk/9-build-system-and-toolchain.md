---
project: tt-llk
github: https://github.com/tenstorrent/tt-llk
deepwiki: https://deepwiki.com/tenstorrent/tt-llk/9-build-system-and-toolchain
---

# Build System and Toolchain

Relevant source files
*   [.github/workflows/build-quasar.yml](https://github.com/tenstorrent/tt-llk/blob/366f58e2/.github/workflows/build-quasar.yml)
*   [.github/workflows/on-pr.yml](https://github.com/tenstorrent/tt-llk/blob/366f58e2/.github/workflows/on-pr.yml)
*   [.github/workflows/setup-and-test.yml](https://github.com/tenstorrent/tt-llk/blob/366f58e2/.github/workflows/setup-and-test.yml)
*   [.gitignore](https://github.com/tenstorrent/tt-llk/blob/366f58e2/.gitignore)
*   [tests/helpers/ld/memory.blackhole.debug.ld](https://github.com/tenstorrent/tt-llk/blob/366f58e2/tests/helpers/ld/memory.blackhole.debug.ld)
*   [tests/helpers/ld/memory.wormhole.debug.ld](https://github.com/tenstorrent/tt-llk/blob/366f58e2/tests/helpers/ld/memory.wormhole.debug.ld)
*   [tests/python_tests/conftest.py](https://github.com/tenstorrent/tt-llk/blob/366f58e2/tests/python_tests/conftest.py)
*   [tests/python_tests/helpers/test_config.py](https://github.com/tenstorrent/tt-llk/blob/366f58e2/tests/python_tests/helpers/test_config.py)
*   [tests/requirements.txt](https://github.com/tenstorrent/tt-llk/blob/366f58e2/tests/requirements.txt)
*   [tests/setup_testing_env.sh](https://github.com/tenstorrent/tt-llk/blob/366f58e2/tests/setup_testing_env.sh)
*   [tests/sfpi-info.sh](https://github.com/tenstorrent/tt-llk/blob/366f58e2/tests/sfpi-info.sh)
*   [tests/sfpi-version](https://github.com/tenstorrent/tt-llk/blob/366f58e2/tests/sfpi-version)

The tt-llk build system compiles C++ kernel code into RISC-V ELF binaries that execute on Tensix cores. This page documents the compilation architecture, toolchain setup, kernel compilation process, and Docker-based development environment.

## Overview

The build system transforms C++ kernel code into architecture-specific ELF binaries through:

*   **Two-tier artifact system**: Shared artifacts compiled once per architecture, variant-specific artifacts per test configuration
*   **RISC-V toolchain**: `riscv-tt-elf-g++` cross-compiler with Tensix extensions, installed via SFPI package
*   **Parallel compilation**: `ThreadPoolExecutor` with file locking (`FileLock`) for concurrent test builds
*   **Python orchestration**: `TestConfig` class manages compilation workflow
*   **Docker containers**: Consistent build environments with content-addressed image tagging

The system supports three architectures (Wormhole B0, Blackhole, Quasar) with architecture-specific flags and linker scripts.

Sources: [tests/python_tests/helpers/test_config.py 74-127](https://github.com/tenstorrent/tt-llk/blob/366f58e2/tests/python_tests/helpers/test_config.py#L74-L127)[tests/python_tests/conftest.py 99-123](https://github.com/tenstorrent/tt-llk/blob/366f58e2/tests/python_tests/conftest.py#L99-L123)

* * *

## 9.1 Compilation Architecture and Artifacts

The build system uses a two-tier artifact structure to minimize redundant compilation:

**Two-Tier Artifact System with File Locking**

Shared artifacts (built once) contain architecture-independent code compiled with common flags. Variant artifacts (built per test) contain test-specific kernels compiled with configuration-specific parameters. `FileLock` prevents race conditions during parallel test execution.

Sources: [tests/python_tests/helpers/test_config.py 560-657](https://github.com/tenstorrent/tt-llk/blob/366f58e2/tests/python_tests/helpers/test_config.py#L560-L657)[tests/python_tests/helpers/test_config.py 868-931](https://github.com/tenstorrent/tt-llk/blob/366f58e2/tests/python_tests/helpers/test_config.py#L868-L931)

### Shared Artifact Build Process

Shared artifacts are built by `TestConfig.build_shared_artefacts()`:

1.   **Lock acquisition**: Acquire `/tmp/tt-llk-build-shared.lock` using `FileLock`
2.   **Marker check**: Check for `.shared_complete` marker (fast path if exists)
3.   **Assembly compilation**: Compile `tmu-crt0.S` startup code
4.   **BRISC compilation**: Compile `brisc.cpp` (Wormhole/Blackhole only)
5.   **TRISC main compilation**: Compile `main_unpack.o`, `main_math.o`, `main_pack.o` in parallel via `ThreadPoolExecutor`
6.   **BRISC linking**: Link `brisc.elf` from tmu-crt0.o + brisc.o + coverage deps
7.   **Marker write**: Write `.shared_complete` to signal completion

**Profiler Variant**: Profiler builds maintain separate shared artifacts at `shared-profiler/` because `trisc.cpp` compiles differently with `-DLLK_PROFILER`. Uses separate lock `/tmp/tt-llk-build-shared-profiler.lock`.

Sources: [tests/python_tests/helpers/test_config.py 560-657](https://github.com/tenstorrent/tt-llk/blob/366f58e2/tests/python_tests/helpers/test_config.py#L560-L657)

### Variant Artifact Build Process

Variant artifacts are built by `TestConfig.build_elfs()`:

1.   **Hash generation**: `generate_variant_hash()` produces SHA256 hash from compilation parameters
2.   **Lock acquisition**: Acquire `sync_primitives/{variant_id}.lock` using `FileLock`
3.   **Marker check**: Check for `.build_complete` marker
4.   **Header generation**: `generate_build_header()` creates `build.h` with format inference and config constants
5.   **Kernel compilation**: Compile test-specific `kernel_*.o` files in parallel (3 workers)
6.   **ELF linking**: Link `unpack.elf`, `math.elf`, `pack.elf` from main + kernel objects
7.   **Metadata extraction**: Extract profiler metadata if profiler build enabled
8.   **Marker write**: Write `.build_complete`

Sources: [tests/python_tests/helpers/test_config.py 868-931](https://github.com/tenstorrent/tt-llk/blob/366f58e2/tests/python_tests/helpers/test_config.py#L868-L931)[tests/python_tests/helpers/test_config.py 488-517](https://github.com/tenstorrent/tt-llk/blob/366f58e2/tests/python_tests/helpers/test_config.py#L488-L517)[tests/python_tests/helpers/test_config.py 792-866](https://github.com/tenstorrent/tt-llk/blob/366f58e2/tests/python_tests/helpers/test_config.py#L792-L866)

### Variant Hash Calculation

Each test variant is identified by a SHA256 hash of compilation-relevant parameters:

**Included in Hash:**

*   `test_name`: C++ source file name
*   `formats`: `InputOutputFormat` (input_format, output_format, input_format_B)
*   `templates`: List of `TemplateParameter` (compile-time config)
*   `boot_mode`: BRISC vs TRISC boot mode
*   `profiler_build`: ProfilerBuild.Yes/No
*   `L1_to_L1_iterations`: Number of pipeline stages
*   `unpack_to_dest`: Direct unpack-to-dest mode
*   `dest_acc`: FP32 destination accumulation
*   `l1_acc`: L1 accumulation enable
*   Stimuli buffer addresses and tile counts

**Excluded from Hash:**

*   `variant_stimuli`: Actual data content (not compilation parameter)
*   `runtimes`: Runtime parameters passed via L1
*   `run_configs`: Execution-time configuration

The hash ensures that identical compilation configurations reuse cached artifacts, while different configurations trigger recompilation.

Sources: [tests/python_tests/helpers/test_config.py 488-517](https://github.com/tenstorrent/tt-llk/blob/366f58e2/tests/python_tests/helpers/test_config.py#L488-L517)

### Directory Structure

```
/tmp/tt-llk-build/
├── shared/
│   ├── obj/
│   │   ├── tmu-crt0.o
│   │   ├── brisc.o
│   │   ├── main_unpack.o
│   │   ├── main_math.o
│   │   ├── main_pack.o
│   │   └── .shared_complete
│   └── elf/
│       └── brisc.elf
├── shared-profiler/              # Separate for profiler builds
│   ├── obj/
│   └── elf/
├── {test_name}/
│   └── {variant_id}/             # SHA256 hash of compilation params
│       ├── build.h               # Generated configuration header
│       ├── obj/
│       │   ├── kernel_unpack.o
│       │   ├── kernel_math.o
│       │   └── kernel_pack.o
│       ├── elf/
│       │   ├── unpack.elf
│       │   ├── math.elf
│       │   └── pack.elf
│       └── .build_complete
├── sync_primitives/              # File locks for parallel builds
├── coverage_info/                # Merged coverage data
└── profiler_meta/                # Extracted profiler metadata
```

Sources: [tests/python_tests/helpers/test_config.py 86-96](https://github.com/tenstorrent/tt-llk/blob/366f58e2/tests/python_tests/helpers/test_config.py#L86-L96)[tests/python_tests/helpers/test_config.py 236-246](https://github.com/tenstorrent/tt-llk/blob/366f58e2/tests/python_tests/helpers/test_config.py#L236-L246)

### Parallel Compilation with ThreadPoolExecutor

The build system uses `ThreadPoolExecutor` with 3 workers for parallel compilation of TRISC cores:

`with ThreadPoolExecutor(max_workers=3) as executor:    futures = [        executor.submit(build_kernel_part_main, name)        for name in TestConfig.KERNEL_COMPONENTS  # ["unpack", "math", "pack"]    ]    for fut in futures:        fut.result()`
Each kernel component compiles with core-specific flags:

*   `kernel_unpack.o`: `-DCOMPILE_FOR_TRISC=0 -DLLK_TRISC_UNPACK`
*   `kernel_math.o`: `-DCOMPILE_FOR_TRISC=1 -DLLK_TRISC_MATH`
*   `kernel_pack.o`: `-DCOMPILE_FOR_TRISC=2 -DLLK_TRISC_PACK`

Sources: [tests/python_tests/helpers/test_config.py 637-643](https://github.com/tenstorrent/tt-llk/blob/366f58e2/tests/python_tests/helpers/test_config.py#L637-L643)[tests/python_tests/helpers/test_config.py 903-917](https://github.com/tenstorrent/tt-llk/blob/366f58e2/tests/python_tests/helpers/test_config.py#L903-L917)

* * *

## 9.2 Environment Setup and SFPI Toolchain

The build system requires environment setup before compilation can begin. This involves installing the SFPI toolchain, configuring architecture-specific paths, and verifying hardware headers.

**Environment Setup and Toolchain Configuration Flow**

The setup process installs the SFPI toolchain, configures architecture-specific variables via `TestConfig.setup_arch()`, sets toolchain paths via `TestConfig.setup_paths()`, and verifies hardware headers via `check_hardware_headers()`. All three architectures (Wormhole, Blackhole, Quasar) follow this flow with architecture-specific values.

Sources: [tests/python_tests/conftest.py 99-126](https://github.com/tenstorrent/tt-llk/blob/366f58e2/tests/python_tests/conftest.py#L99-L126)[tests/python_tests/helpers/test_config.py 183-234](https://github.com/tenstorrent/tt-llk/blob/366f58e2/tests/python_tests/helpers/test_config.py#L183-L234)

### CHIP_ARCH Environment Variable

The `CHIP_ARCH` environment variable determines target architecture:

| Value | Architecture | CPU Flags | Define |
| --- | --- | --- | --- |
| `wormhole` | Wormhole B0 | `-mcpu=tt-wh` / `-mcpu=tt-wh-tensix` | `-DARCH_WORMHOLE` |
| `blackhole` | Blackhole | `-mcpu=tt-bh` / `-mcpu=tt-bh-tensix` | `-DARCH_BLACKHOLE` |
| `quasar` | Quasar | `-mcpu=tt-bh` / `-mcpu=tt-bh-tensix` (fallback) | `-DARCH_QUASAR` |

**CPU Model Selection:**

*   `ARCH_NON_COMPUTE`: Used for BRISC (non-compute cores)
*   `ARCH_COMPUTE`: Used for TRISC (Tensix compute cores)

Quasar uses Blackhole CPU models as fallback until official SFPI support is available.

Sources: [tests/python_tests/helpers/test_config.py 183-208](https://github.com/tenstorrent/tt-llk/blob/366f58e2/tests/python_tests/helpers/test_config.py#L183-L208)

### SFPI Toolchain Installation

The `setup_testing_env.sh` script downloads and installs the SFPI package:

**Directory Structure After Installation:**

```
tests/sfpi/
├── compiler/
│   └── bin/
│       ├── riscv-tt-elf-g++
│       ├── riscv-tt-elf-objdump
│       ├── riscv-tt-elf-objcopy
│       ├── riscv-tt-elf-gcov
│       └── riscv-tt-elf-gcov-tool
├── include/
└── examples/
```

**Toolchain Components:**

| Tool | Purpose |
| --- | --- |
| `riscv-tt-elf-g++` | C++ compiler with Tensix extensions |
| `riscv-tt-elf-objdump` | ELF disassembler for debugging |
| `riscv-tt-elf-objcopy` | Binary format conversion, section extraction |
| `riscv-tt-elf-gcov` | Coverage data extraction |
| `riscv-tt-elf-gcov-tool` | Coverage data merging |

The script is idempotent and can be safely re-run. CI workflows call it automatically before running tests.

Sources: [.github/workflows/setup-and-test.yml 82-87](https://github.com/tenstorrent/tt-llk/blob/366f58e2/.github/workflows/setup-and-test.yml#L82-L87)[tests/python_tests/helpers/test_config.py 224-234](https://github.com/tenstorrent/tt-llk/blob/366f58e2/tests/python_tests/helpers/test_config.py#L224-L234)

### Path Configuration

`TestConfig.setup_paths()` configures all file system paths:

**Source Directories:**

*   `LLK_ROOT`: Repository root (from `LLK_HOME` environment variable)
*   `TESTS_WORKING_DIR`: `tests/` directory
*   `TOOL_PATH`: `tests/sfpi/compiler/bin/`
*   `HEADER_DIR`: `tests/hw_specific/{arch}/inc/`
*   `HELPERS`: `tests/helpers/`
*   `RISCV_SOURCES`: `tests/helpers/src/`
*   `LINKER_SCRIPTS`: `tests/helpers/ld/`

**Artifact Directories:**

*   `ARTEFACTS_DIR`: `/tmp/tt-llk-build/`
*   `SHARED_DIR`: `/tmp/tt-llk-build/shared/`
*   `SHARED_OBJ_DIR`: `/tmp/tt-llk-build/shared/obj/`
*   `SHARED_ELF_DIR`: `/tmp/tt-llk-build/shared/elf/`
*   `PROFILER_SHARED_DIR`: `/tmp/tt-llk-build/shared-profiler/`
*   `SYNC_DIR`: `/tmp/tt-llk-build/sync_primitives/`

Sources: [tests/python_tests/helpers/test_config.py 210-246](https://github.com/tenstorrent/tt-llk/blob/366f58e2/tests/python_tests/helpers/test_config.py#L210-L246)

### Hardware-Specific Headers

Each architecture requires hardware-specific headers defining register layouts and memory maps:

**Required Headers (Wormhole/Blackhole):**

*   `core_config.h`: Core configuration
*   `cfg_defines.h`: Register offsets
*   `dev_mem_map.h`: Memory map
*   `tensix.h`: Tensix registers
*   `tensix_types.h`: Type definitions

**Additional Headers (Quasar):**

*   `t6_debug_map.h`
*   `t6_mop_config_map.h`
*   `tt_t6_trisc_map.h`

Headers are not included in the repository and must be downloaded via `setup_testing_env.sh`. The `check_hardware_headers()` function validates presence before compilation, exiting with setup instructions if missing.

Sources: [tests/python_tests/conftest.py 30-83](https://github.com/tenstorrent/tt-llk/blob/366f58e2/tests/python_tests/conftest.py#L30-L83)

### Include Path Configuration

`TestConfig.INCLUDES` configures compiler include paths:

`[    "-Isfpi/include",                                    # SFPI compiler headers    f"-I../{TestConfig.ARCH_LLK_ROOT}/llk_lib",         # LLK library    f"-I../{TestConfig.ARCH_LLK_ROOT}/common/inc",      # Common includes    f"-I../{TestConfig.ARCH_LLK_ROOT}/common/inc/sfpu", # SFPU operations    f"-I{TestConfig.HEADER_DIR}",                       # Hardware headers    f"-Ihw_specific/{TestConfig.ARCH.value}",           # Architecture-specific    f"-Ihw_specific/{TestConfig.ARCH.value}/metal_sfpu",# Metal SFPU    "-Ifirmware/riscv/common",                          # Firmware common    "-Ihelpers/include",                                # Test helpers]`
Sources: [tests/python_tests/helpers/test_config.py 287-298](https://github.com/tenstorrent/tt-llk/blob/366f58e2/tests/python_tests/helpers/test_config.py#L287-L298)

* * *

## 9.3 Kernel Compilation and Linking

Kernel compilation transforms C++ source files into architecture-specific ELF binaries through a multi-stage process:

**Kernel Compilation and Linking Pipeline**

Each TRISC core requires two object files: `main_{core}.o` (shared across variants) and `kernel_{core}.o` (test-specific). The compilation uses architecture-specific CPU flags and core-specific defines. Linking combines these with startup code and linker scripts to produce executable ELFs.

Sources: [tests/python_tests/helpers/test_config.py 605-650](https://github.com/tenstorrent/tt-llk/blob/366f58e2/tests/python_tests/helpers/test_config.py#L605-L650)[tests/python_tests/helpers/test_config.py 920-968](https://github.com/tenstorrent/tt-llk/blob/366f58e2/tests/python_tests/helpers/test_config.py#L920-L968)

### trisc.cpp Compilation

The `trisc.cpp` file contains the main entry point for TRISC cores. It is compiled three times with different defines:

**Compilation for TRISC0 (Unpacker):**

`riscv-tt-elf-g++ -mcpu=tt-wh-tensix -O3 -std=c++17 -ffast-math -g \    -nostdlib -fno-exceptions -fno-rtti \    -DTENSIX_FIRMWARE -DENV_LLK_INFRA -DARCH_WORMHOLE \    -DCOMPILE_FOR_TRISC=0 -DLLK_TRISC_UNPACK \    -c -o main_unpack.o trisc.cpp`
**Compilation for TRISC1 (Math):**

`riscv-tt-elf-g++ -mcpu=tt-wh-tensix -O3 -std=c++17 -ffast-math -g \    -DCOMPILE_FOR_TRISC=1 -DLLK_TRISC_MATH \    -c -o main_math.o trisc.cpp`
**Compilation for TRISC2 (Packer):**

`riscv-tt-elf-g++ -mcpu=tt-wh-tensix -O3 -std=c++17 -ffast-math -g \    -DCOMPILE_FOR_TRISC=2 -DLLK_TRISC_PACK \    -c -o main_pack.o trisc.cpp`
The `COMPILE_FOR_TRISC` macro selects which core-specific code to compile, while `LLK_TRISC_{UNPACK,MATH,PACK}` macros enable conditional compilation of core-specific LLK functions.

Sources: [tests/python_tests/helpers/test_config.py 627-635](https://github.com/tenstorrent/tt-llk/blob/366f58e2/tests/python_tests/helpers/test_config.py#L627-L635)

### tmu-crt0.S Startup Assembly

The `tmu-crt0.S` file contains startup code for RISC cores:

`riscv-tt-elf-g++ -mcpu=tt-wh -O3 -std=c++17 -ffast-math -g \    -nostdlib -DTENSIX_FIRMWARE -DENV_LLK_INFRA -DARCH_WORMHOLE \    -c -o tmu-crt0.o tmu-crt0.S`
This startup code initializes the stack, sets up the C runtime environment, and jumps to the `main()` function. It is compiled with `ARCH_NON_COMPUTE` flags (non-Tensix CPU model).

Sources: [tests/python_tests/helpers/test_config.py 605-608](https://github.com/tenstorrent/tt-llk/blob/366f58e2/tests/python_tests/helpers/test_config.py#L605-L608)

### brisc.cpp Compilation

For Wormhole and Blackhole architectures, `brisc.cpp` provides the BRISC core main function:

`riscv-tt-elf-g++ -mcpu=tt-wh -O3 -std=c++17 -ffast-math -g \    -nostdlib -fno-exceptions -fno-rtti \    -DTENSIX_FIRMWARE -DENV_LLK_INFRA -DARCH_WORMHOLE \    -c -o brisc.o brisc.cpp`
Quasar does not have a BRISC core, so this compilation is skipped when `CHIP_ARCH == ChipArchitecture.QUASAR`.

Sources: [tests/python_tests/helpers/test_config.py 611-616](https://github.com/tenstorrent/tt-llk/blob/366f58e2/tests/python_tests/helpers/test_config.py#L611-L616)

### Kernel-Specific Compilation

Test-specific kernel files (e.g., `test_matmul.cpp`) are compiled for each TRISC core:

`# Unpack kernelriscv-tt-elf-g++ -mcpu=tt-wh-tensix -O3 -std=c++17 -ffast-math -g \    -I/tmp/tt-llk-build/test_matmul/{variant_id} \    -DCOMPILE_FOR_TRISC=0 -DLLK_TRISC_UNPACK \    -c -o kernel_unpack.o test_matmul.cpp # Math kernelriscv-tt-elf-g++ -mcpu=tt-wh-tensix \    -DCOMPILE_FOR_TRISC=1 -DLLK_TRISC_MATH \    -c -o kernel_math.o test_matmul.cpp # Pack kernelriscv-tt-elf-g++ -mcpu=tt-wh-tensix \    -DCOMPILE_FOR_TRISC=2 -DLLK_TRISC_PACK \    -c -o kernel_pack.o test_matmul.cpp`
The `-I{variant_dir}` flag includes the variant-specific `build.h` header containing generated configuration constants.

Sources: [tests/python_tests/helpers/test_config.py 920-931](https://github.com/tenstorrent/tt-llk/blob/366f58e2/tests/python_tests/helpers/test_config.py#L920-L931)

### Linking Process

Each ELF is linked from object files using core-specific linker scripts:

**BRISC ELF Linking:**

`riscv-tt-elf-g++ -mcpu=tt-wh -O3 -fexceptions \    -Wl,-z,max-page-size=16 -Wl,-z,common-page-size=16 \    -nostartfiles -Wl,--trace \    tmu-crt0.o brisc.o {coverage.o} {-lgcov} \    -T memory.wormhole.ld -T brisc.ld -T sections.ld \    -o brisc.elf`
**TRISC ELF Linking:**

`riscv-tt-elf-g++ -mcpu=tt-wh-tensix -O3 -fexceptions \    -nostartfiles \    main_unpack.o kernel_unpack.o tmu-crt0.o {-lgcov} \    -T memory.wormhole.ld -T unpack.ld -T sections.ld \    -o unpack.elf`
The linker scripts define memory layout, entry points, and section placement. Coverage builds include `coverage.o` and link against `-lgcov`.

Sources: [tests/python_tests/helpers/test_config.py 646-650](https://github.com/tenstorrent/tt-llk/blob/366f58e2/tests/python_tests/helpers/test_config.py#L646-L650)[tests/python_tests/helpers/test_config.py 933-968](https://github.com/tenstorrent/tt-llk/blob/366f58e2/tests/python_tests/helpers/test_config.py#L933-L968)

### Build Flags

**Base Compiler Flags (`OPTIONS_ALL`):**

*   `-O3`: Maximum optimization
*   `-std=c++17`: C++17 standard
*   `-ffast-math`: Fast math operations
*   `-g`: Debug symbols (unless `--no-debug-symbols`)

**Core Compiler Flags (`INITIAL_OPTIONS_COMPILE`):**

*   `-nostdlib`: No standard library
*   `-fno-use-cxa-atexit`: Disable C++ exit handling
*   `-Wall`: All warnings
*   `-fno-exceptions`: Disable exception handling
*   `-fno-rtti`: Disable run-time type information
*   `-DTENSIX_FIRMWARE`: Tensix firmware build
*   `-DENV_LLK_INFRA`: LLK infrastructure environment

**Linker Flags (`OPTIONS_LINK`):**

*   `-fexceptions`: Exception unwinding support (for callstacks)
*   `-Wl,-z,max-page-size=16 -Wl,-z,common-page-size=16`: 16-byte page alignment
*   `-nostartfiles`: No standard startup files
*   `-Wl,--trace`: Verbose linker output

**Coverage-Specific Flags:**

*   `-fprofile-arcs`: Arc profiling instrumentation
*   `-ftest-coverage`: Test coverage instrumentation
*   `-fprofile-info-section`: Store profile data in dedicated section
*   `-DCOVERAGE`: Coverage macro

**Profiler-Specific Flags:**

*   `-DLLK_PROFILER`: Profiler instrumentation

Sources: [tests/python_tests/helpers/test_config.py 276-286](https://github.com/tenstorrent/tt-llk/blob/366f58e2/tests/python_tests/helpers/test_config.py#L276-L286)[tests/python_tests/helpers/test_config.py 546-556](https://github.com/tenstorrent/tt-llk/blob/366f58e2/tests/python_tests/helpers/test_config.py#L546-L556)

### Linker Scripts and Memory Layout

The build system uses architecture-specific linker scripts:

**Memory Layout Scripts:**

*   `memory.wormhole.ld`: Standard memory layout for Wormhole
*   `memory.wormhole.debug.ld`: Debug layout with coverage instrumentation space
*   `memory.blackhole.ld`: Standard memory layout for Blackhole
*   `memory.blackhole.debug.ld`: Debug layout for Blackhole

**Core-Specific Scripts:**

*   `brisc.ld`: BRISC entry point and section placement
*   `unpack.ld`: TRISC0 (Unpacker) layout
*   `math.ld`: TRISC1 (Math) layout
*   `pack.ld`: TRISC2 (Packer) layout
*   `sections.ld`: Common section definitions

Coverage builds use debug memory layouts (`memory.{arch}.debug.ld`) to accommodate additional instrumentation data. Runtime parameters are placed at different addresses depending on coverage mode:

*   Non-coverage: `0x20000` (128KB)
*   Coverage: `0x64000` (400KB)

Sources: [tests/python_tests/helpers/test_config.py 532-553](https://github.com/tenstorrent/tt-llk/blob/366f58e2/tests/python_tests/helpers/test_config.py#L532-L553)[tests/python_tests/helpers/test_config.py 139-142](https://github.com/tenstorrent/tt-llk/blob/366f58e2/tests/python_tests/helpers/test_config.py#L139-L142)

* * *

## 9.4 Docker Images and Container Development

* * *

## SFPI Toolchain Installation

The SFPI (Special Function Processing Interface) toolchain provides the RISC-V compiler and SFPU operation support. It is downloaded and installed by `setup_testing_env.sh`.

**SFPI Installation Flow**

The installation script detects the target architecture, downloads the appropriate SFPI release package, and extracts it to the `tests/sfpi/` directory. The script is idempotent and can be safely re-run.

Sources: [tests/setup_external_testing_env.sh 99-100](https://github.com/tenstorrent/tt-llk/blob/366f58e2/tests/setup_external_testing_env.sh#L99-L100)[.github/workflows/setup-and-test.yml 76-82](https://github.com/tenstorrent/tt-llk/blob/366f58e2/.github/workflows/setup-and-test.yml#L76-L82)

### Environment Setup Script

The `setup_external_testing_env.sh` script provides a complete environment setup including:

**Features:**

*   Python 3.8 or 3.10 version checking
*   Virtual environment creation in `.venv/`
*   Python dependency installation via `uv` package manager
*   SFPI toolchain installation delegation
*   `--reuse` flag to skip setup if environment exists
*   `--clean` flag to remove all generated artifacts

**System Dependencies:**

`curl cmake software-properties-common build-essentiallibyaml-cpp-dev libhwloc-dev libzmq3-dev git-lfs xxd wget`
Sources: [tests/setup_external_testing_env.sh 1-108](https://github.com/tenstorrent/tt-llk/blob/366f58e2/tests/setup_external_testing_env.sh#L1-L108)

* * *

## Hardware-Specific Headers

Each architecture requires hardware-specific header files that define register layouts, memory maps, and hardware configurations. These headers are not included in the repository and must be downloaded separately.

**Header Organization and Validation**

Hardware headers are organized by architecture under `tests/hw_specific/{wormhole,blackhole,quasar}/inc/`. The `check_hardware_headers()` function validates that all required headers exist before compilation begins, providing clear error messages with setup instructions if headers are missing.

Sources: [tests/python_tests/conftest.py 29-82](https://github.com/tenstorrent/tt-llk/blob/366f58e2/tests/python_tests/conftest.py#L29-L82)[tests/python_tests/helpers/test_config.py 184-187](https://github.com/tenstorrent/tt-llk/blob/366f58e2/tests/python_tests/helpers/test_config.py#L184-L187)

### Include Path Configuration

The compiler is configured with multiple include paths via `TestConfig.INCLUDES`:

`[    "-Isfpi/include",                                    # SFPI compiler headers    f"-I../{TestConfig.ARCH_LLK_ROOT}/llk_lib",         # LLK library    f"-I../{TestConfig.ARCH_LLK_ROOT}/common/inc",      # Common includes    f"-I../{TestConfig.ARCH_LLK_ROOT}/common/inc/sfpu", # SFPU operations    f"-I{TestConfig.HEADER_DIR}",                       # Hardware headers    f"-Ihw_specific/{TestConfig.ARCH.value}",           # Architecture-specific    f"-Ihw_specific/{TestConfig.ARCH.value}/metal_sfpu",# Metal SFPU    "-Ifirmware/riscv/common",                          # Firmware common    "-Ihelpers/include",                                # Test helpers]`
Sources: [tests/python_tests/helpers/test_config.py 256-266](https://github.com/tenstorrent/tt-llk/blob/366f58e2/tests/python_tests/helpers/test_config.py#L256-L266)

* * *

## Compilation Architecture and Artifacts

The build system uses a two-tier artifact structure: shared artifacts compiled once per architecture, and variant-specific artifacts compiled for each test configuration.

**Artifact Build Hierarchy**

Shared artifacts are built once per architecture and reused across all test variants. File locks prevent concurrent builds of the same artifacts. Profiler builds maintain separate shared artifacts because `trisc.cpp` compiles differently with `-DLLK_PROFILER`.

Sources: [tests/python_tests/helpers/test_config.py 485-583](https://github.com/tenstorrent/tt-llk/blob/366f58e2/tests/python_tests/helpers/test_config.py#L485-L583)[tests/python_tests/helpers/test_config.py 784-874](https://github.com/tenstorrent/tt-llk/blob/366f58e2/tests/python_tests/helpers/test_config.py#L784-L874)

### Build.h Generation

Each test variant generates a `build.h` header containing:

**Configuration Constants:**

*   `TILE_SIZE_CNT`: 0x1000 (4096 bytes)
*   `TILE_SIZE_PACK`, `TILE_SIZE_UNPACK_A`, `TILE_SIZE_UNPACK_B`: Format-dependent tile sizes
*   `is_fp32_dest_acc_en`: Destination accumulation mode
*   `l1_acc_en`: L1 accumulation mode
*   `unpack_to_dest`: Unpack-to-dest optimization flag

**Data Format Inference:**

*   `UNPACK_A_IN`, `UNPACK_A_OUT`: Unpacker formats
*   `MATH_FORMAT`: Math unit format
*   `PACK_IN`, `PACK_OUT`: Packer formats
*   For multi-iteration L1-to-L1 pipelines: arrays of formats (`UNPACK_A_IN_LIST`, etc.)

**Stimuli Addresses:**

*   `BUF_A_ADDR`, `BUF_B_ADDR`, `BUF_RES_ADDR`: L1 memory addresses
*   `BUF_A_TILES`, `BUF_B_TILES`, `BUF_RES_TILES`: Tile counts

**Template Parameters:**

*   Test-specific compile-time parameters

**Runtime Parameters:**

*   `RuntimeParams` struct with layout matching Python serialization format

Sources: [tests/python_tests/helpers/test_config.py 703-782](https://github.com/tenstorrent/tt-llk/blob/366f58e2/tests/python_tests/helpers/test_config.py#L703-L782)

### Variant Hash Calculation

Each test variant is uniquely identified by a SHA256 hash of its compilation-relevant parameters:

**Variant Hash Calculation**

The variant hash includes all parameters that affect compiled code but excludes runtime data like stimuli content and runtime parameters. Stimuli buffer addresses are included because they are compiled as `constexpr` values in `build.h`.

Sources: [tests/python_tests/helpers/test_config.py 413-442](https://github.com/tenstorrent/tt-llk/blob/366f58e2/tests/python_tests/helpers/test_config.py#L413-L442)

### Artifact Directory Structure

```
/tmp/tt-llk-build/
├── shared/                          # Standard shared artifacts
│   ├── obj/
│   │   ├── tmu-crt0.o
│   │   ├── brisc.o
│   │   ├── main_unpack.o
│   │   ├── main_math.o
│   │   ├── main_pack.o
│   │   └── .shared_complete
│   └── elf/
│       └── brisc.elf
├── shared-profiler/                 # Profiler shared artifacts
│   ├── obj/
│   └── elf/
├── {test_name}/
│   └── {variant_id}/                # Variant-specific
│       ├── build.h
│       ├── obj/
│       │   ├── kernel_unpack.o
│       │   ├── kernel_math.o
│       │   └── kernel_pack.o
│       ├── elf/
│       │   ├── unpack.elf
│       │   ├── math.elf
│       │   └── pack.elf
│       ├── {stream_name}.stream     # Coverage data (if --coverage)
│       └── .build_complete
├── coverage_info/                   # Merged coverage data
├── profiler_meta/                   # Profiler metadata
├── sync_primitives/                 # File locks for parallel builds
└── temp_perf_data/                  # Performance profiling data
```

Sources: [tests/python_tests/helpers/test_config.py 89-96](https://github.com/tenstorrent/tt-llk/blob/366f58e2/tests/python_tests/helpers/test_config.py#L89-L96)[tests/python_tests/helpers/test_config.py 206-216](https://github.com/tenstorrent/tt-llk/blob/366f58e2/tests/python_tests/helpers/test_config.py#L206-L216)

### Parallel Compilation and File Locking

The build system supports parallel test execution via `pytest-xdist`. File locks prevent race conditions:

**Shared Artifact Lock:**

*   Lock file: `/tmp/tt-llk-build-shared.lock`
*   Protects: `shared/obj/` and `shared/elf/`
*   Marker: `shared/obj/.shared_complete`

**Profiler Shared Artifact Lock:**

*   Lock file: `/tmp/tt-llk-build-shared-profiler.lock`
*   Protects: `shared-profiler/obj/` and `shared-profiler/elf/`
*   Marker: `shared-profiler/obj/.shared_complete`

**Variant-Specific Lock:**

*   Lock file: `sync_primitives/{variant_id}.lock`
*   Protects: Test variant build directory
*   Marker: `{test_name}/{variant_id}/.build_complete`

The locking pattern uses `filelock.FileLock` with double-checked locking: check for completion marker, acquire lock, check again, build if needed, write marker, release lock.

Sources: [tests/python_tests/helpers/test_config.py 513-523](https://github.com/tenstorrent/tt-llk/blob/366f58e2/tests/python_tests/helpers/test_config.py#L513-L523)[tests/python_tests/helpers/test_config.py 800-807](https://github.com/tenstorrent/tt-llk/blob/366f58e2/tests/python_tests/helpers/test_config.py#L800-L807)

* * *

## Linker Scripts and Memory Layout

The build system uses architecture-specific linker scripts to define memory layout and section placement.

**Linker Script Organization**

Coverage builds use debug memory layouts with additional space for coverage instrumentation data. Each core type (BRISC, TRISC0-2) has a dedicated linker script defining entry points and section placement. The common `sections.ld` defines shared section layout rules.

Sources: [tests/python_tests/helpers/test_config.py 456-478](https://github.com/tenstorrent/tt-llk/blob/366f58e2/tests/python_tests/helpers/test_config.py#L456-L478)[tests/python_tests/helpers/test_config.py 140-150](https://github.com/tenstorrent/tt-llk/blob/366f58e2/tests/python_tests/helpers/test_config.py#L140-L150)

### Runtime Parameter Addresses

Runtime parameters are passed to kernels via L1 memory at fixed addresses:

*   **Non-coverage builds:**`0x20000` (128KB)
*   **Coverage builds:**`0x61000` (388KB) - offset to avoid collision with coverage instrumentation

The Python test framework serializes runtime parameters using `struct.pack()` with a format string derived from parameter types, then writes the packed data to the appropriate address before ELF execution.

Sources: [tests/python_tests/helpers/test_config.py 141-142](https://github.com/tenstorrent/tt-llk/blob/366f58e2/tests/python_tests/helpers/test_config.py#L141-L142)[tests/python_tests/helpers/test_config.py 371-398](https://github.com/tenstorrent/tt-llk/blob/366f58e2/tests/python_tests/helpers/test_config.py#L371-L398)

* * *

## Docker Images and Container Infrastructure

The CI/CD pipeline uses Docker images for consistent build and test environments across different machines. Images are built hierarchically with content-addressed tagging for cache efficiency.

**Docker Image Build and Tagging Workflow**

The `build-images.yml` workflow builds Docker images with content-addressed tags. The build script computes a hash from Dockerfile and dependency files, builds the image with `docker buildx`, and pushes to the GitHub Container Registry. The CI image (Dockerfile.ci) uses layer optimization techniques for fast builds.

Sources: [.github/workflows/build-images.yml 1-39](https://github.com/tenstorrent/tt-llk/blob/366f58e2/.github/workflows/build-images.yml#L1-L39)[.github/scripts/build-docker-images.sh 1-30](https://github.com/tenstorrent/tt-llk/blob/366f58e2/.github/scripts/build-docker-images.sh#L1-L30)[.github/scripts/get-docker-tag.sh 1-17](https://github.com/tenstorrent/tt-llk/blob/366f58e2/.github/scripts/get-docker-tag.sh#L1-L17)

### Content-Addressed Docker Tagging

Docker images are tagged with `dt-{hash}` where the hash is calculated from:

`sha256sum .github/Dockerfile.base \          .github/Dockerfile.ci \          .github/Dockerfile.ird \          .github/Dockerfile.ird.slim \          tests/requirements.txt \          .github/scripts/build-docker-images.sh \          .github/scripts/install-tests-dependencies.sh | sha256sum`
This ensures:

*   **Automatic cache invalidation** when dependencies change
*   **Reproducible builds** across different CI runs
*   **Parallel development** with different dependency sets
*   **Minimal rebuilds** when unrelated files change

Sources: [.github/scripts/get-docker-tag.sh 8-16](https://github.com/tenstorrent/tt-llk/blob/366f58e2/.github/scripts/get-docker-tag.sh#L8-L16)

### Dockerfile.ci Optimization

The CI image is optimized for fast builds and small size:

**Build Strategy:**

*   Single `RUN` layer combining all operations
*   Install dependencies with `uv pip install` for speed
*   Aggressive cleanup of temporary files and caches
*   `.dockerignore` excludes large directories from build context

**Environment Variables:**

*   `GITHUB_ACTIONS="true"`: Signals CI environment
*   `PYTEST_RUN_PATH="tests/python_tests"`: Default test path

Sources: [.github/Dockerfile.ci 1-26](https://github.com/tenstorrent/tt-llk/blob/366f58e2/.github/Dockerfile.ci#L1-L26)[.github/.dockerignore 1-22](https://github.com/tenstorrent/tt-llk/blob/366f58e2/.github/.dockerignore#L1-L22)

### Python Dependency Management

Python dependencies are installed via `uv` package manager for faster resolution and installation:

`pip install --upgrade pippip install uvuv pip install --index-strategy unsafe-best-match \               --no-cache-dir \               -r requirements.txt`
**Key Dependencies:**

*   `tt-exalens==0.3.5`: Tensix debugging and device communication
*   `tt-umd==0.9.1`: Unified device management
*   `pytest==8.4.2`: Test framework
*   `pytest-xdist==3.8.0`: Parallel test execution
*   `pytest-split==0.10.0`: Duration-based test splitting
*   `torch==2.9.1`: Golden model computation
*   `coverage[toml]==7.10.5`: Code coverage

Sources: [tests/requirements.txt 1-48](https://github.com/tenstorrent/tt-llk/blob/366f58e2/tests/requirements.txt#L1-L48)[.github/scripts/install-tests-dependencies.sh 1-18](https://github.com/tenstorrent/tt-llk/blob/366f58e2/.github/scripts/install-tests-dependencies.sh#L1-L18)

* * *

## Build Orchestration in TestConfig

The `TestConfig` class orchestrates the entire build process through three key methods:

**Build Orchestration Flow**

The build process follows a strict order: hash generation identifies the variant, header generation creates `build.h`, shared artifacts are built once, then variant-specific kernels are compiled and linked. Parallel compilation uses `ThreadPoolExecutor` with 3 workers for the three TRISC cores.

Sources: [tests/python_tests/helpers/test_config.py 303-342](https://github.com/tenstorrent/tt-llk/blob/366f58e2/tests/python_tests/helpers/test_config.py#L303-L342)[tests/python_tests/helpers/test_config.py 485-582](https://github.com/tenstorrent/tt-llk/blob/366f58e2/tests/python_tests/helpers/test_config.py#L485-L582)[tests/python_tests/helpers/test_config.py 784-874](https://github.com/tenstorrent/tt-llk/blob/366f58e2/tests/python_tests/helpers/test_config.py#L784-L874)

### Compilation Commands

**Shared Artifact Compilation:**

`# tmu-crt0.o (startup assembly)riscv-tt-elf-g++ {ARCH_NON_COMPUTE} {OPTIONS_ALL} {OPTIONS_COMPILE} \    -c -o {SHARED_OBJ_DIR}/tmu-crt0.o {HELPERS}/tmu-crt0.S # brisc.o (BRISC main)riscv-tt-elf-g++ {ARCH_NON_COMPUTE} {OPTIONS_ALL} {NON_COVERAGE_OPTIONS} \    -c -o {SHARED_OBJ_DIR}/brisc.o {RISCV_SOURCES}/brisc.cpp # main_unpack.o (TRISC0 main)riscv-tt-elf-g++ {ARCH_COMPUTE} {OPTIONS_ALL} {OPTIONS_COMPILE} \    -DCOMPILE_FOR_TRISC=0 -DLLK_TRISC_UNPACK \    -c -o {SHARED_OBJ_DIR}/main_unpack.o {RISCV_SOURCES}/trisc.cpp`
**Variant-Specific Compilation:**

`# kernel_unpack.o (test-specific kernel)riscv-tt-elf-g++ {ARCH_COMPUTE} {OPTIONS_ALL} -I{VARIANT_DIR} {OPTIONS_COMPILE} \    -DCOMPILE_FOR_TRISC=0 -DLLK_TRISC_UNPACK \    -c -o {VARIANT_OBJ_DIR}/kernel_unpack.o {TESTS_WORKING_DIR}/{test_name} # unpack.elf (linked ELF)riscv-tt-elf-g++ {ARCH_COMPUTE} {OPTIONS_ALL} {OPTIONS_LINK} \    {SHARED_OBJ_DIR}/main_unpack.o \    {VARIANT_OBJ_DIR}/kernel_unpack.o \    {COVERAGE_DEPS} {SHARED_OBJ_DIR}/tmu-crt0.o {SFPI_DEPS} \    -T{MEMORY_LAYOUT_LD} -T{LINKER_SCRIPTS}/unpack.ld -T{LINKER_SCRIPTS}/sections.ld \    -o {VARIANT_ELF_DIR}/unpack.elf`
Sources: [tests/python_tests/helpers/test_config.py 530-575](https://github.com/tenstorrent/tt-llk/blob/366f58e2/tests/python_tests/helpers/test_config.py#L530-L575)[tests/python_tests/helpers/test_config.py 835-847](https://github.com/tenstorrent/tt-llk/blob/366f58e2/tests/python_tests/helpers/test_config.py#L835-L847)

### Coverage Build Differences

When `--coverage` is specified:

**Additional Compile Flags:**

*   `-fprofile-arcs`: Generate instrumentation for arc profiling
*   `-ftest-coverage`: Generate instrumentation for test coverage
*   `-fprofile-info-section`: Store profile data in dedicated section
*   `-DCOVERAGE`: Define coverage macro

**Additional Link Dependencies:**

*   `coverage.o`: Coverage runtime support (compiled from `coverage.cpp`)
*   `-lgcov`: GCC coverage library

**Memory Layout:**

*   Uses `memory.{arch}.debug.ld` instead of `memory.{arch}.ld`
*   Runtime parameters at `0x61000` instead of `0x20000`

Sources: [tests/python_tests/helpers/test_config.py 471-478](https://github.com/tenstorrent/tt-llk/blob/366f58e2/tests/python_tests/helpers/test_config.py#L471-L478)[tests/python_tests/helpers/test_config.py 543-550](https://github.com/tenstorrent/tt-llk/blob/366f58e2/tests/python_tests/helpers/test_config.py#L543-L550)

### Profiler Build Differences

When `profiler_build=ProfilerBuild.Yes`:

**Additional Compile Flags:**

*   `-DLLK_PROFILER`: Enable profiler instrumentation

**Separate Shared Artifacts:**

*   Uses `shared-profiler/obj/` and `shared-profiler/elf/`
*   Separate lock file: `/tmp/tt-llk-build-shared-profiler.lock`
*   Required because `trisc.cpp` compiles differently with profiler enabled

**Metadata Extraction:** After linking, profiler metadata is extracted from ELF files:

`riscv-tt-elf-objcopy -O binary -j .profiler_meta \    {VARIANT_ELF_DIR}/unpack.elf \    {PROFILER_META}/{test_name}/{variant_id}/unpack.meta.bin`
Sources: [tests/python_tests/helpers/test_config.py 480-481](https://github.com/tenstorrent/tt-llk/blob/366f58e2/tests/python_tests/helpers/test_config.py#L480-L481)[tests/python_tests/helpers/test_config.py 486-511](https://github.com/tenstorrent/tt-llk/blob/366f58e2/tests/python_tests/helpers/test_config.py#L486-L511)[tests/python_tests/helpers/test_config.py 857-871](https://github.com/tenstorrent/tt-llk/blob/366f58e2/tests/python_tests/helpers/test_config.py#L857-L871)

* * *

## CI Build Integration

The CI pipeline integrates build system components through GitHub Actions workflows.

**CI Build and Test Flow**

The workflow separates compilation (`--compile-producer`) from execution (`--compile-consumer`) to enable efficient caching and debugging. The compilation phase runs with 10 parallel workers, while execution runs sequentially with `-x` (stop on first failure) for faster feedback.

Sources: [.github/workflows/setup-and-test.yml 100-144](https://github.com/tenstorrent/tt-llk/blob/366f58e2/.github/workflows/setup-and-test.yml#L100-L144)

### Architecture Detection in CI

The CI workflow automatically detects the target architecture from the GitHub Actions runner label:

`CHIP_ARCH=unknownif [[ "${{ inputs.runs_on }}" =~ n150 ]]; then    CHIP_ARCH=wormholeelif [[ "${{ inputs.runs_on }}" =~ p150 ]]; then    CHIP_ARCH=blackholefiexport CHIP_ARCH`
*   `tt-ubuntu-2204-n150-stable` → `CHIP_ARCH=wormhole`
*   `tt-ubuntu-2204-p150b-stable` → `CHIP_ARCH=blackhole`

Sources: [.github/workflows/setup-and-test.yml 106-113](https://github.com/tenstorrent/tt-llk/blob/366f58e2/.github/workflows/setup-and-test.yml#L106-L113)

### Test Splitting and Duration-Based Balancing

The CI uses `pytest-split` to divide tests across multiple parallel workers:

`pytest --splits $SPLITS --group ${{ matrix.test_group }} \       --durations-path=.test_durations`
Duration data is collected from previous runs and used to balance test distribution, ensuring each worker has approximately equal execution time.

Sources: [.github/workflows/setup-and-test.yml 84-98](https://github.com/tenstorrent/tt-llk/blob/366f58e2/.github/workflows/setup-and-test.yml#L84-L98)[.github/workflows/setup-and-test.yml 122-128](https://github.com/tenstorrent/tt-llk/blob/366f58e2/.github/workflows/setup-and-test.yml#L122-L128)

* * *

## Build System Testing and Debugging

### Compilation-Only Mode

Tests can be compiled without execution using `--compile-producer`:

`cd tests/python_testspytest --compile-producer -n 10 -m "not perf and not nightly"`
This generates all ELF files and caches them in `/tmp/tt-llk-build/`. Useful for:

*   Validating compilation without hardware
*   Pre-building artifacts for later execution
*   Debugging compiler errors

Sources: [tests/python_tests/helpers/test_config.py 282-300](https://github.com/tenstorrent/tt-llk/blob/366f58e2/tests/python_tests/helpers/test_config.py#L282-L300)[.github/workflows/setup-and-test.yml 130-136](https://github.com/tenstorrent/tt-llk/blob/366f58e2/.github/workflows/setup-and-test.yml#L130-L136)

### Execution-Only Mode

Pre-compiled tests can be executed using `--compile-consumer`:

`cd tests/python_testspytest --compile-consumer -m "not perf and not nightly"`
This skips compilation and loads ELFs from `/tmp/tt-llk-build/`. Useful for:

*   Rapid test iteration after initial compilation
*   Debugging runtime issues without recompilation
*   Running tests across multiple devices with shared artifacts

Sources: [tests/python_tests/helpers/test_config.py 282-300](https://github.com/tenstorrent/tt-llk/blob/366f58e2/tests/python_tests/helpers/test_config.py#L282-L300)[.github/workflows/setup-and-test.yml 138-144](https://github.com/tenstorrent/tt-llk/blob/366f58e2/.github/workflows/setup-and-test.yml#L138-L144)

### Detailed Artifacts Mode

Enable detailed compiler artifacts for debugging:

`pytest --detailed-artefacts`
This adds compiler flags:

*   `-save-temps=obj`: Keep intermediate files (`.i`, `.s`, `.o`)
*   `-fdump-tree-all`: Dump all compiler tree passes
*   `-fdump-rtl-all`: Dump all RTL passes
*   `-v`: Verbose compiler output

Sources: [tests/python_tests/helpers/test_config.py 249-252](https://github.com/tenstorrent/tt-llk/blob/366f58e2/tests/python_tests/helpers/test_config.py#L249-L252)[tests/python_tests/conftest.py 334-337](https://github.com/tenstorrent/tt-llk/blob/366f58e2/tests/python_tests/conftest.py#L334-L337)

### No Debug Symbols Mode

Compile without debug symbols to save disk space:

`pytest --no-debug-symbols`
This removes the `-g` flag from compilation, significantly reducing ELF file size at the cost of losing symbol information for debugging.

Sources: [tests/python_tests/helpers/test_config.py 245](https://github.com/tenstorrent/tt-llk/blob/366f58e2/tests/python_tests/helpers/test_config.py#L245-L245)[tests/python_tests/conftest.py 347-351](https://github.com/tenstorrent/tt-llk/blob/366f58e2/tests/python_tests/conftest.py#L347-L351)

### Build Directory Cleanup

The build system automatically cleans artifacts when running in production mode:

`if TestConfig.MODE != TestMode.CONSUME:    shutil.rmtree(TestConfig.ARTEFACTS_DIR.absolute(), ignore_errors=True)`
Manual cleanup:

`rm -rf /tmp/tt-llk-build/`
Sources: [tests/python_tests/helpers/test_config.py 298-300](https://github.com/tenstorrent/tt-llk/blob/366f58e2/tests/python_tests/helpers/test_config.py#L298-L300)

This wiki is featured in the [repository](https://github.com/tenstorrent/tt-llk/blob/main/README.md)

Dismiss
Refresh this wiki

Enter email to refresh
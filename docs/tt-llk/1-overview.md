---
project: tt-llk
github: https://github.com/tenstorrent/tt-llk
deepwiki: https://deepwiki.com/tenstorrent/tt-llk/1-overview
---

# Overview

Relevant source files
*   [.github/workflows/build-quasar.yml](https://github.com/tenstorrent/tt-llk/blob/366f58e2/.github/workflows/build-quasar.yml)
*   [.github/workflows/on-pr.yml](https://github.com/tenstorrent/tt-llk/blob/366f58e2/.github/workflows/on-pr.yml)
*   [.github/workflows/setup-and-test.yml](https://github.com/tenstorrent/tt-llk/blob/366f58e2/.github/workflows/setup-and-test.yml)
*   [.gitignore](https://github.com/tenstorrent/tt-llk/blob/366f58e2/.gitignore)
*   [tests/helpers/include/ckernel_helper.h](https://github.com/tenstorrent/tt-llk/blob/366f58e2/tests/helpers/include/ckernel_helper.h)
*   [tests/helpers/ld/memory.blackhole.debug.ld](https://github.com/tenstorrent/tt-llk/blob/366f58e2/tests/helpers/ld/memory.blackhole.debug.ld)
*   [tests/helpers/ld/memory.blackhole.ld](https://github.com/tenstorrent/tt-llk/blob/366f58e2/tests/helpers/ld/memory.blackhole.ld)
*   [tests/helpers/ld/memory.wormhole.debug.ld](https://github.com/tenstorrent/tt-llk/blob/366f58e2/tests/helpers/ld/memory.wormhole.debug.ld)
*   [tests/helpers/ld/memory.wormhole.ld](https://github.com/tenstorrent/tt-llk/blob/366f58e2/tests/helpers/ld/memory.wormhole.ld)
*   [tests/python_tests/conftest.py](https://github.com/tenstorrent/tt-llk/blob/366f58e2/tests/python_tests/conftest.py)
*   [tests/python_tests/helpers/test_config.py](https://github.com/tenstorrent/tt-llk/blob/366f58e2/tests/python_tests/helpers/test_config.py)
*   [tests/requirements.txt](https://github.com/tenstorrent/tt-llk/blob/366f58e2/tests/requirements.txt)
*   [tt_llk_blackhole/common/inc/ckernel.h](https://github.com/tenstorrent/tt-llk/blob/366f58e2/tt_llk_blackhole/common/inc/ckernel.h)
*   [tt_llk_blackhole/common/inc/ckernel_mutex_guard.h](https://github.com/tenstorrent/tt-llk/blob/366f58e2/tt_llk_blackhole/common/inc/ckernel_mutex_guard.h)
*   [tt_llk_blackhole/common/inc/ckernel_structs.h](https://github.com/tenstorrent/tt-llk/blob/366f58e2/tt_llk_blackhole/common/inc/ckernel_structs.h)
*   [tt_llk_wormhole_b0/common/inc/ckernel.h](https://github.com/tenstorrent/tt-llk/blob/366f58e2/tt_llk_wormhole_b0/common/inc/ckernel.h)
*   [tt_llk_wormhole_b0/common/inc/ckernel_instr_params.h](https://github.com/tenstorrent/tt-llk/blob/366f58e2/tt_llk_wormhole_b0/common/inc/ckernel_instr_params.h)
*   [tt_llk_wormhole_b0/common/inc/ckernel_mutex_guard.h](https://github.com/tenstorrent/tt-llk/blob/366f58e2/tt_llk_wormhole_b0/common/inc/ckernel_mutex_guard.h)
*   [tt_llk_wormhole_b0/common/inc/ckernel_structs.h](https://github.com/tenstorrent/tt-llk/blob/366f58e2/tt_llk_wormhole_b0/common/inc/ckernel_structs.h)
*   [tt_llk_wormhole_b0/common/inc/cmath_common.h](https://github.com/tenstorrent/tt-llk/blob/366f58e2/tt_llk_wormhole_b0/common/inc/cmath_common.h)
*   [tt_llk_wormhole_b0/common/inc/cpack_common.h](https://github.com/tenstorrent/tt-llk/blob/366f58e2/tt_llk_wormhole_b0/common/inc/cpack_common.h)
*   [tt_llk_wormhole_b0/common/inc/cunpack_common.h](https://github.com/tenstorrent/tt-llk/blob/366f58e2/tt_llk_wormhole_b0/common/inc/cunpack_common.h)
*   [tt_llk_wormhole_b0/llk_lib/llk_math_eltwise_unary_datacopy.h](https://github.com/tenstorrent/tt-llk/blob/366f58e2/tt_llk_wormhole_b0/llk_lib/llk_math_eltwise_unary_datacopy.h)
*   [tt_llk_wormhole_b0/llk_lib/llk_math_matmul.h](https://github.com/tenstorrent/tt-llk/blob/366f58e2/tt_llk_wormhole_b0/llk_lib/llk_math_matmul.h)
*   [tt_llk_wormhole_b0/llk_lib/llk_pack.h](https://github.com/tenstorrent/tt-llk/blob/366f58e2/tt_llk_wormhole_b0/llk_lib/llk_pack.h)
*   [tt_llk_wormhole_b0/llk_lib/llk_unpack_A.h](https://github.com/tenstorrent/tt-llk/blob/366f58e2/tt_llk_wormhole_b0/llk_lib/llk_unpack_A.h)
*   [tt_llk_wormhole_b0/llk_lib/llk_unpack_AB.h](https://github.com/tenstorrent/tt-llk/blob/366f58e2/tt_llk_wormhole_b0/llk_lib/llk_unpack_AB.h)
*   [tt_llk_wormhole_b0/llk_lib/llk_unpack_AB_matmul.h](https://github.com/tenstorrent/tt-llk/blob/366f58e2/tt_llk_wormhole_b0/llk_lib/llk_unpack_AB_matmul.h)
*   [tt_llk_wormhole_b0/llk_lib/llk_unpack_reduce.h](https://github.com/tenstorrent/tt-llk/blob/366f58e2/tt_llk_wormhole_b0/llk_lib/llk_unpack_reduce.h)
*   [tt_llk_wormhole_b0/llk_lib/llk_unpack_untilize.h](https://github.com/tenstorrent/tt-llk/blob/366f58e2/tt_llk_wormhole_b0/llk_lib/llk_unpack_untilize.h)

## Purpose and Scope

The `tt-llk` (Tenstorrent Low-Level Kernel) repository provides a C++ hardware abstraction layer for programming Tensix cores on Tenstorrent AI accelerators. This library implements the fundamental compute operations that form the building blocks of neural network workloads, including matrix multiplication, element-wise operations, reductions, and data format conversions.

This document provides a high-level overview of the repository architecture, core concepts, and system organization. For detailed information on specific subsystems:

*   Three-stage pipeline architecture: see [Three-Stage Pipeline Architecture](https://deepwiki.com/tenstorrent/tt-llk/1.1-three-stage-pipeline-architecture)
*   Architecture-specific differences: see [Multi-Architecture Support](https://deepwiki.com/tenstorrent/tt-llk/1.2-multi-architecture-support)
*   Directory layout: see [Repository Structure and File Organization](https://deepwiki.com/tenstorrent/tt-llk/1.3-repository-structure-and-file-organization)
*   Core hardware abstractions: see [Core Kernel API (ckernel)](https://deepwiki.com/tenstorrent/tt-llk/2-core-kernel-api-(ckernel))
*   Testing infrastructure: see [Testing Framework](https://deepwiki.com/tenstorrent/tt-llk/8-testing-framework)

**Sources**: [README.md](https://github.com/tenstorrent/tt-llk/blob/366f58e2/README.md?plain=1)[tests/python_tests/helpers/test_config.py 1-150](https://github.com/tenstorrent/tt-llk/blob/366f58e2/tests/python_tests/helpers/test_config.py#L1-L150)[tt_llk_wormhole_b0/common/inc/ckernel.h 1-50](https://github.com/tenstorrent/tt-llk/blob/366f58e2/tt_llk_wormhole_b0/common/inc/ckernel.h#L1-L50)

* * *

## System Architecture

The tt-llk library operates at the intersection of hardware and software, providing C++ APIs that directly control Tensix core execution units. The system is organized into three primary layers:

**Sources**: [tt_llk_wormhole_b0/common/inc/ckernel.h 1-100](https://github.com/tenstorrent/tt-llk/blob/366f58e2/tt_llk_wormhole_b0/common/inc/ckernel.h#L1-L100)[tt_llk_wormhole_b0/common/inc/cunpack_common.h 1-50](https://github.com/tenstorrent/tt-llk/blob/366f58e2/tt_llk_wormhole_b0/common/inc/cunpack_common.h#L1-L50)[tt_llk_wormhole_b0/common/inc/cmath_common.h 1-50](https://github.com/tenstorrent/tt-llk/blob/366f58e2/tt_llk_wormhole_b0/common/inc/cmath_common.h#L1-L50)[tt_llk_wormhole_b0/common/inc/cpack_common.h 1-50](https://github.com/tenstorrent/tt-llk/blob/366f58e2/tt_llk_wormhole_b0/common/inc/cpack_common.h#L1-L50)

* * *

## Three-Stage Compute Pipeline

The fundamental execution model in tt-llk follows a three-stage pipeline that processes data through Tensix cores. Each stage operates on tiles (typically 32×32 elements) with hardware-managed synchronization between stages.

| Stage | Hardware Unit | Configuration Function | Execution Instruction | Data Path |
| --- | --- | --- | --- | --- |
| Unpack | Unpacker (TRISC0) | `configure_unpack_AB<>()` | `TTI_UNPACR` | L1 → SrcA/SrcB registers |
| Math | FPU/SFPU (TRISC1) | `configure_math()` | `TTI_ELWADD`, `TTI_MVMUL`, etc. | SrcA/SrcB → Dest registers |
| Pack | Packer (TRISC2) | `configure_pack<>()` | `TTI_PACR` | Dest registers → L1 |

**Key Synchronization Primitives**:

*   `semaphore::UNPACK_SYNC` - Controls unpacker → math data flow
*   `semaphore::MATH_PACK` - Controls math → packer data flow
*   `t6_semaphore_post()` / `t6_semaphore_get()` - Post/acquire tokens

**Sources**: [tt_llk_wormhole_b0/common/inc/cunpack_common.h 207-377](https://github.com/tenstorrent/tt-llk/blob/366f58e2/tt_llk_wormhole_b0/common/inc/cunpack_common.h#L207-L377)[tt_llk_wormhole_b0/common/inc/cmath_common.h 1-50](https://github.com/tenstorrent/tt-llk/blob/366f58e2/tt_llk_wormhole_b0/common/inc/cmath_common.h#L1-L50)[tt_llk_wormhole_b0/common/inc/cpack_common.h 224-352](https://github.com/tenstorrent/tt-llk/blob/366f58e2/tt_llk_wormhole_b0/common/inc/cpack_common.h#L224-L352)[tt_llk_wormhole_b0/common/inc/ckernel_structs.h 12-60](https://github.com/tenstorrent/tt-llk/blob/366f58e2/tt_llk_wormhole_b0/common/inc/ckernel_structs.h#L12-L60)

* * *

## Supported Hardware Architectures

The tt-llk library supports three Tenstorrent architecture families, with architecture-specific implementations in separate directory trees:

| Architecture | Directory | Chip Examples | Key Differences |
| --- | --- | --- | --- |
| Wormhole B0 | `tt_llk_wormhole_b0/` | N150 | First production architecture |
| Blackhole | `tt_llk_blackhole/` | P150B | Enhanced SFPU, instruction gathering, TRISC sync tracking |
| Quasar | `tt_llk_quasar/` | Future | TRISC-only boot mode, different memory layout |

**Architecture Selection**: The active architecture is determined at compile-time via the `CHIP_ARCH` environment variable and configured in `TestConfig.setup_arch()`:

**Sources**: [tests/python_tests/helpers/test_config.py 207-235](https://github.com/tenstorrent/tt-llk/blob/366f58e2/tests/python_tests/helpers/test_config.py#L207-L235)[tests/python_tests/conftest.py 98-123](https://github.com/tenstorrent/tt-llk/blob/366f58e2/tests/python_tests/conftest.py#L98-L123)

* * *

## Repository Structure

The repository follows a consistent organization pattern across architectures:

```
tt-llk/
├── tt_llk_wormhole_b0/           # Wormhole B0 implementation
│   ├── llk_lib/                  # High-level LLK APIs
│   │   ├── llk_unpack_*.h        # Unpacker operations
│   │   ├── llk_math_*.h          # Math operations  
│   │   └── llk_pack*.h           # Packer operations
│   └── common/inc/               # Hardware abstraction layer
│       ├── ckernel.h             # Core abstractions
│       ├── cunpack_common.h      # Unpacker hardware control
│       ├── cmath_common.h        # Math hardware control
│       └── cpack_common.h        # Packer hardware control
├── tt_llk_blackhole/             # Blackhole implementation (same structure)
├── tt_llk_quasar/                # Quasar implementation (same structure)
├── common/                       # Architecture-independent utilities
├── tests/
│   ├── python_tests/             # Python test infrastructure
│   │   └── helpers/              # Test orchestration
│   │       ├── test_config.py    # TestConfig class
│   │       ├── device.py         # Hardware control
│   │       └── golden_generators.py  # Reference implementations
│   ├── helpers/                  # C++ test utilities
│   │   ├── include/              # Test headers
│   │   └── src/                  # BRISC/TRISC implementations
│   └── sources/                  # Test kernel implementations
└── .github/workflows/            # CI/CD automation
```

**Key File Roles**:

| File Path | Purpose |
| --- | --- |
| `ckernel.h` | Hardware synchronization primitives, memory ordering, config state management |
| `llk_unpack_A.h` | Unpack single operand with broadcast support |
| `llk_unpack_AB.h` | Unpack two operands for binary operations |
| `llk_math_eltwise_*.h` | Element-wise operations (unary/binary) |
| `llk_math_matmul.h` | Matrix multiplication implementation |
| `llk_pack.h` | Standard packing to L1 memory |
| `test_config.py` | Test orchestration, compilation, execution |

**Sources**: [tt_llk_wormhole_b0/common/inc/ckernel.h 1-50](https://github.com/tenstorrent/tt-llk/blob/366f58e2/tt_llk_wormhole_b0/common/inc/ckernel.h#L1-L50)[tt_llk_wormhole_b0/llk_lib/llk_unpack_A.h 1-30](https://github.com/tenstorrent/tt-llk/blob/366f58e2/tt_llk_wormhole_b0/llk_lib/llk_unpack_A.h#L1-L30)[tests/python_tests/helpers/test_config.py 105-180](https://github.com/tenstorrent/tt-llk/blob/366f58e2/tests/python_tests/helpers/test_config.py#L105-L180)

* * *

## Core Concepts

### Tiles and Faces

Data processing in tt-llk operates on **tiles**, which are 2D blocks of data with a hierarchical structure:

**Constant Definitions**:

*   `TILE_R_DIM = 32` - Tile row dimension
*   `TILE_C_DIM = 32` - Tile column dimension
*   `FACE_R_DIM = 16` - Face row dimension
*   `FACE_C_DIM = 16` - Face column dimension
*   `TILE_NUM_FACES = 4` - Number of faces per tile

**Partial Tile Support**: Operations can be configured for smaller tiles (e.g., 16×32, 32×16) by adjusting `face_r_dim` and `num_faces` parameters.

**Sources**: [tt_llk_wormhole_b0/common/inc/llk_defs.h](https://github.com/tenstorrent/tt-llk/blob/366f58e2/tt_llk_wormhole_b0/common/inc/llk_defs.h)[tt_llk_wormhole_b0/common/inc/cunpack_common.h 350-370](https://github.com/tenstorrent/tt-llk/blob/366f58e2/tt_llk_wormhole_b0/common/inc/cunpack_common.h#L350-L370)

### Data Formats

The LLK supports multiple numeric formats with automatic conversion in the unpack/pack stages:

| Format | Type | Bits | Description |
| --- | --- | --- | --- |
| `Float32` | Standard float | 32 | IEEE 754 single precision |
| `Float16` / `Float16_b` | Half precision | 16 | IEEE 754 half precision |
| `Bfp8` / `Bfp8_b` | Block floating point | 8 | Shared exponent per 16 elements |
| `Bfp4` / `Bfp4_b` | Block floating point | 4 | Shared exponent per 16 elements |
| `Int32` / `UInt32` | Integer | 32 | Signed/unsigned integers |
| `Int8` / `UInt8` | Integer | 8 | Signed/unsigned integers |

**Format Configuration Structure**:

```
FormatConfig {
    unpack_A_src: DataFormat     // L1 input format
    unpack_A_dst: DataFormat     // SrcA register format
    math: DataFormat             // Computation format
    pack_src: DataFormat         // Dest register format
    pack_dst: DataFormat         // L1 output format
}
```

**Sources**: [tests/python_tests/helpers/format_config.py](https://github.com/tenstorrent/tt-llk/blob/366f58e2/tests/python_tests/helpers/format_config.py)[tt_llk_wormhole_b0/common/inc/ckernel_defs.h](https://github.com/tenstorrent/tt-llk/blob/366f58e2/tt_llk_wormhole_b0/common/inc/ckernel_defs.h)

### Synchronization Model

Hardware units operate asynchronously with explicit synchronization via **semaphores** and **mutexes**:

**Semaphore Operations**:

*   `semaphore_post(index)` - Release token (increment counter, max 15)
*   `semaphore_get(index)` - Acquire token (decrement counter, min 0)
*   `t6_semaphore_post<WaitRes>(index)` - Tensix thread post with optional stall
*   `t6_semaphore_get<WaitRes>(index)` - Tensix thread get with optional stall

**Mutex Operations** (for register access):

*   `t6_mutex_acquire(mutex::REG_RMW)` - Lock register read-modify-write
*   `t6_mutex_release(mutex::REG_RMW)` - Unlock register access

**Sources**: [tt_llk_wormhole_b0/common/inc/ckernel.h 202-280](https://github.com/tenstorrent/tt-llk/blob/366f58e2/tt_llk_wormhole_b0/common/inc/ckernel.h#L202-L280)[tt_llk_wormhole_b0/common/inc/ckernel_structs.h 12-80](https://github.com/tenstorrent/tt-llk/blob/366f58e2/tt_llk_wormhole_b0/common/inc/ckernel_structs.h#L12-L80)

* * *

## Configuration State Management

The LLK uses **ping-pong configuration contexts** to enable double-buffering of hardware configuration, allowing overlap of configuration programming with operation execution:

**Configuration Context Functions**:

*   `get_cfg_pointer()` - Returns pointer to current context's config registers
*   `flip_cfg_state_id()` - Switch between contexts 0 and 1
*   `reset_cfg_state_id()` - Reset to context 0

**Unpacker Context Management**:

*   2 contexts per unpacker (`unp_cfg_context`)
*   `switch_config_context(unp_cfg_context)` - Toggle unpacker context
*   `wait_for_next_context(2)` - Wait for free context before programming

**Sources**: [tt_llk_wormhole_b0/common/inc/ckernel.h 283-350](https://github.com/tenstorrent/tt-llk/blob/366f58e2/tt_llk_wormhole_b0/common/inc/ckernel.h#L283-L350)[tt_llk_wormhole_b0/common/inc/cunpack_common.h 124-183](https://github.com/tenstorrent/tt-llk/blob/366f58e2/tt_llk_wormhole_b0/common/inc/cunpack_common.h#L124-L183)

* * *

## Testing Infrastructure

The repository includes a comprehensive Python-based testing framework that handles compilation, execution, and validation:

**Key Testing Classes**:

| Class | Purpose | Location |
| --- | --- | --- |
| `TestConfig` | Orchestrates compilation, execution, validation | `tests/python_tests/helpers/test_config.py` |
| `StimuliConfig` | Defines L1 memory layout for inputs/outputs | `tests/python_tests/helpers/stimuli_config.py` |
| `FormatConfig` | Specifies data formats through pipeline | `tests/python_tests/helpers/format_config.py` |
| `Golden*` generators | Generate reference outputs (PyTorch/NumPy) | `tests/python_tests/helpers/golden_generators.py` |
| `device` module | Hardware control via ttexalens | `tests/python_tests/helpers/device.py` |

**Test Execution Modes**:

*   `TestMode.DEFAULT` - Compile and execute sequentially
*   `TestMode.PRODUCE` - Only compile ELF artifacts (parallel CI workers)
*   `TestMode.CONSUME` - Only execute pre-compiled ELFs (on hardware)

**Sources**: [tests/python_tests/helpers/test_config.py 78-168](https://github.com/tenstorrent/tt-llk/blob/366f58e2/tests/python_tests/helpers/test_config.py#L78-L168)[tests/python_tests/conftest.py 98-150](https://github.com/tenstorrent/tt-llk/blob/366f58e2/tests/python_tests/conftest.py#L98-L150)

* * *

## Development Workflow

### Local Development

1.   **Environment Setup**:

 
2.   **Running Tests**:

 
3.   **Compilation Artifacts**:

    *   Shared objects: `/tmp/tt-llk-build/shared/obj/`
    *   Test ELFs: `/tmp/tt-llk-build/<test_name>/<variant_hash>/elf/`

**Sources**: [tests/setup_testing_env.sh](https://github.com/tenstorrent/tt-llk/blob/366f58e2/tests/setup_testing_env.sh)[tests/python_tests/conftest.py 1-100](https://github.com/tenstorrent/tt-llk/blob/366f58e2/tests/python_tests/conftest.py#L1-L100)

### CI/CD Pipeline

The GitHub Actions pipeline implements a two-phase test strategy:

**Workflow Files**:

*   `.github/workflows/on-pr.yml` - Main PR orchestration
*   `.github/workflows/setup-and-test.yml` - Reusable compile/execute workflow
*   `.github/workflows/build-images.yml` - Docker image management

**Key Features**:

*   Change detection filters tests by modified directories
*   Split compilation across 10 workers (producer phase)
*   Execute on hardware with 2 test groups (consumer phase)
*   Automatic coverage merging and reporting

**Sources**: [.github/workflows/on-pr.yml 1-224](https://github.com/tenstorrent/tt-llk/blob/366f58e2/.github/workflows/on-pr.yml#L1-L224)[.github/workflows/setup-and-test.yml 1-220](https://github.com/tenstorrent/tt-llk/blob/366f58e2/.github/workflows/setup-and-test.yml#L1-L220)

* * *

## Memory Layout

Tensix cores have a hierarchical memory structure with specific address ranges for different purposes:

| Memory Region | Address Range | Purpose | Access |
| --- | --- | --- | --- |
| L1 Data | `0x0 - 0x100000+` | Input/output buffers, shared data | All cores |
| L1 Code | Per-TRISC 16K sections | Program text for BRISC/TRISCs | Core-local |
| Runtime Args | `0x20000` (no coverage) `0x6E000` (coverage) | Parameter passing to kernels | Read by TRISCs |
| Performance Buffers | `0x16B000 - 0x16D000` | Per-thread profiling data | TRISC write |
| L0 Memory | `0xFFB00000+` | Core-local stack/data (3-6K) | Core-local |

**Configuration Registers**:

*   `TENSIX_CFG_BASE` - Configuration register base address
*   Per-state config space: `CFG_STATE_SIZE * 16` bytes
*   Unpacker regs: `THCON_SEC0_*`, `THCON_SEC1_*`
*   Math regs: `ALU_FORMAT_SPEC_*`, `ALU_ACC_CTRL_*`
*   Packer regs: `PCK_*`, `PACK_COUNTERS_*`

**Sources**: [tests/python_tests/helpers/test_config.py 170-204](https://github.com/tenstorrent/tt-llk/blob/366f58e2/tests/python_tests/helpers/test_config.py#L170-L204)[tt_llk_wormhole_b0/common/inc/ckernel.h 283-311](https://github.com/tenstorrent/tt-llk/blob/366f58e2/tt_llk_wormhole_b0/common/inc/ckernel.h#L283-L311)[tests/helpers/ld/memory.wormhole.ld 1-100](https://github.com/tenstorrent/tt-llk/blob/366f58e2/tests/helpers/ld/memory.wormhole.ld#L1-L100)

* * *

## Related Documentation

For deeper dives into specific subsystems, refer to:

*   **[Three-Stage Pipeline Architecture](https://deepwiki.com/tenstorrent/tt-llk/1.1-three-stage-pipeline-architecture)** - Detailed unpack-math-pack flow
*   **[Core Kernel API (ckernel)](https://deepwiki.com/tenstorrent/tt-llk/2-core-kernel-api-(ckernel))** - Hardware abstraction primitives
*   **[Data Formats and Configuration](https://deepwiki.com/tenstorrent/tt-llk/3-data-formats-and-configuration)** - Format system and conversion
*   **[Unpacker System](https://deepwiki.com/tenstorrent/tt-llk/4-unpacker-system)** - Data loading operations
*   **[Math Unit Operations](https://deepwiki.com/tenstorrent/tt-llk/5-math-unit-operations)** - FPU/SFPU computations
*   **[Packer System](https://deepwiki.com/tenstorrent/tt-llk/6-packer-system)** - Data storing operations
*   **[Testing Framework](https://deepwiki.com/tenstorrent/tt-llk/8-testing-framework)** - Complete test infrastructure
*   **[Build System and Toolchain](https://deepwiki.com/tenstorrent/tt-llk/9-build-system-and-toolchain)** - Compilation details
*   **[CI/CD Pipeline](https://deepwiki.com/tenstorrent/tt-llk/10-cicd-pipeline)** - Automated testing workflows

**Sources**: [tests/python_tests/helpers/test_config.py 1-100](https://github.com/tenstorrent/tt-llk/blob/366f58e2/tests/python_tests/helpers/test_config.py#L1-L100)[tt_llk_wormhole_b0/common/inc/ckernel.h 1-100](https://github.com/tenstorrent/tt-llk/blob/366f58e2/tt_llk_wormhole_b0/common/inc/ckernel.h#L1-L100)

Dismiss
Refresh this wiki

Enter email to refresh
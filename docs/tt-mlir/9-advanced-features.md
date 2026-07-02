---
project: tt-mlir
github: https://github.com/tenstorrent/tt-mlir
deepwiki: https://deepwiki.com/tenstorrent/tt-mlir/9-advanced-features
---

# Advanced Features

Relevant source files
*   [docs/src/ttnn-jit.md](https://github.com/tenstorrent/tt-mlir/blob/c7d92e92/docs/src/ttnn-jit.md?plain=1)
*   [lib/Conversion/StableHLOToTTIR/ShardyToTTIRPatterns.cpp](https://github.com/tenstorrent/tt-mlir/blob/c7d92e92/lib/Conversion/StableHLOToTTIR/ShardyToTTIRPatterns.cpp)
*   [lib/Conversion/TTNNToTTIR/TTNNToTTIRPatterns.cpp](https://github.com/tenstorrent/tt-mlir/blob/c7d92e92/lib/Conversion/TTNNToTTIR/TTNNToTTIRPatterns.cpp)
*   [test/pykernel/add_2_integers_in_compute/add_2_tiles.py](https://github.com/tenstorrent/tt-mlir/blob/c7d92e92/test/pykernel/add_2_integers_in_compute/add_2_tiles.py)
*   [test/pykernel/add_2_integers_in_compute/reader_binary_1_tile.py](https://github.com/tenstorrent/tt-mlir/blob/c7d92e92/test/pykernel/add_2_integers_in_compute/reader_binary_1_tile.py)
*   [test/pykernel/add_2_integers_in_compute/writer_1_tile.py](https://github.com/tenstorrent/tt-mlir/blob/c7d92e92/test/pykernel/add_2_integers_in_compute/writer_1_tile.py)
*   [test/pykernel/eltwise_sfpu/eltwise_sfpu.py](https://github.com/tenstorrent/tt-mlir/blob/c7d92e92/test/pykernel/eltwise_sfpu/eltwise_sfpu.py)
*   [test/pykernel/eltwise_sfpu/reader_unary.py](https://github.com/tenstorrent/tt-mlir/blob/c7d92e92/test/pykernel/eltwise_sfpu/reader_unary.py)
*   [test/pykernel/eltwise_sfpu/writer_unary.py](https://github.com/tenstorrent/tt-mlir/blob/c7d92e92/test/pykernel/eltwise_sfpu/writer_unary.py)
*   [test/pykernel/matmul_common/reader_bmm_8bank.py](https://github.com/tenstorrent/tt-mlir/blob/c7d92e92/test/pykernel/matmul_common/reader_bmm_8bank.py)
*   [test/pykernel/matmul_common/reader_bmm_8bank_output_tiles_partitioned.py](https://github.com/tenstorrent/tt-mlir/blob/c7d92e92/test/pykernel/matmul_common/reader_bmm_8bank_output_tiles_partitioned.py)
*   [test/pykernel/matmul_common/writer_bmm_8bank.py](https://github.com/tenstorrent/tt-mlir/blob/c7d92e92/test/pykernel/matmul_common/writer_bmm_8bank.py)
*   [test/pykernel/unit_tests.py](https://github.com/tenstorrent/tt-mlir/blob/c7d92e92/test/pykernel/unit_tests.py)
*   [test/ttmlir/Conversion/StableHLOToTTIR/ccl/ccl_ops_gspmd.mlir](https://github.com/tenstorrent/tt-mlir/blob/c7d92e92/test/ttmlir/Conversion/StableHLOToTTIR/ccl/ccl_ops_gspmd.mlir)
*   [test/ttmlir/Conversion/StableHLOToTTIR/ccl/ccl_ops_shardy.mlir](https://github.com/tenstorrent/tt-mlir/blob/c7d92e92/test/ttmlir/Conversion/StableHLOToTTIR/ccl/ccl_ops_shardy.mlir)
*   [test/ttmlir/Conversion/StableHLOToTTIR/ccl/e2e_dp_gspmd.mlir](https://github.com/tenstorrent/tt-mlir/blob/c7d92e92/test/ttmlir/Conversion/StableHLOToTTIR/ccl/e2e_dp_gspmd.mlir)
*   [test/ttmlir/Conversion/StableHLOToTTIR/ccl/e2e_dp_shardy.mlir](https://github.com/tenstorrent/tt-mlir/blob/c7d92e92/test/ttmlir/Conversion/StableHLOToTTIR/ccl/e2e_dp_shardy.mlir)
*   [test/ttmlir/Conversion/StableHLOToTTIR/ccl/e2e_fsdp_gspmd.mlir](https://github.com/tenstorrent/tt-mlir/blob/c7d92e92/test/ttmlir/Conversion/StableHLOToTTIR/ccl/e2e_fsdp_gspmd.mlir)
*   [test/ttmlir/Conversion/StableHLOToTTIR/ccl/e2e_fsdp_shardy.mlir](https://github.com/tenstorrent/tt-mlir/blob/c7d92e92/test/ttmlir/Conversion/StableHLOToTTIR/ccl/e2e_fsdp_shardy.mlir)
*   [test/ttmlir/Conversion/StableHLOToTTIR/ccl/e2e_tp_shardy.mlir](https://github.com/tenstorrent/tt-mlir/blob/c7d92e92/test/ttmlir/Conversion/StableHLOToTTIR/ccl/e2e_tp_shardy.mlir)
*   [test/ttmlir/Dialect/TTNN/ccl/mesh_shard.mlir](https://github.com/tenstorrent/tt-mlir/blob/c7d92e92/test/ttmlir/Dialect/TTNN/ccl/mesh_shard.mlir)
*   [test/ttnn-jit/conftest.py](https://github.com/tenstorrent/tt-mlir/blob/c7d92e92/test/ttnn-jit/conftest.py)
*   [test/ttnn-jit/lit/test_tracing_ir.py](https://github.com/tenstorrent/tt-mlir/blob/c7d92e92/test/ttnn-jit/lit/test_tracing_ir.py)
*   [test/ttnn-jit/nightly/test_eltwise.py](https://github.com/tenstorrent/tt-mlir/blob/c7d92e92/test/ttnn-jit/nightly/test_eltwise.py)
*   [test/ttnn-jit/nightly/test_eltwise_composite.py](https://github.com/tenstorrent/tt-mlir/blob/c7d92e92/test/ttnn-jit/nightly/test_eltwise_composite.py)
*   [test/ttnn-jit/nightly/test_layouts.py](https://github.com/tenstorrent/tt-mlir/blob/c7d92e92/test/ttnn-jit/nightly/test_layouts.py)
*   [test/ttnn-jit/nightly/test_matmul.py](https://github.com/tenstorrent/tt-mlir/blob/c7d92e92/test/ttnn-jit/nightly/test_matmul.py)
*   [test/ttnn-jit/op_definitions.py](https://github.com/tenstorrent/tt-mlir/blob/c7d92e92/test/ttnn-jit/op_definitions.py)
*   [test/ttnn-jit/test_eltwise_smoketest.py](https://github.com/tenstorrent/tt-mlir/blob/c7d92e92/test/ttnn-jit/test_eltwise_smoketest.py)
*   [test/ttnn-jit/test_jit_type_hints.py](https://github.com/tenstorrent/tt-mlir/blob/c7d92e92/test/ttnn-jit/test_jit_type_hints.py)
*   [test/ttnn-jit/test_matmul_smoketest.py](https://github.com/tenstorrent/tt-mlir/blob/c7d92e92/test/ttnn-jit/test_matmul_smoketest.py)
*   [test/ttnn-jit/test_mesh_tensor_eltwise.py](https://github.com/tenstorrent/tt-mlir/blob/c7d92e92/test/ttnn-jit/test_mesh_tensor_eltwise.py)
*   [test/ttnn-jit/test_mixed_legacy_sharding_types.py](https://github.com/tenstorrent/tt-mlir/blob/c7d92e92/test/ttnn-jit/test_mixed_legacy_sharding_types.py)
*   [test/ttnn-jit/test_program_cache.py](https://github.com/tenstorrent/tt-mlir/blob/c7d92e92/test/ttnn-jit/test_program_cache.py)
*   [test/ttnn-jit/test_unsupported_tensor_layouts.py](https://github.com/tenstorrent/tt-mlir/blob/c7d92e92/test/ttnn-jit/test_unsupported_tensor_layouts.py)
*   [test/ttnn-jit/utils.py](https://github.com/tenstorrent/tt-mlir/blob/c7d92e92/test/ttnn-jit/utils.py)
*   [tools/ttnn-jit/_src/conversions.py](https://github.com/tenstorrent/tt-mlir/blob/c7d92e92/tools/ttnn-jit/_src/conversions.py)
*   [tools/ttnn-jit/_src/ir_generator.py](https://github.com/tenstorrent/tt-mlir/blob/c7d92e92/tools/ttnn-jit/_src/ir_generator.py)
*   [tools/ttnn-jit/_src/jit.py](https://github.com/tenstorrent/tt-mlir/blob/c7d92e92/tools/ttnn-jit/_src/jit.py)
*   [tools/ttnn-jit/_src/jit_functions.py](https://github.com/tenstorrent/tt-mlir/blob/c7d92e92/tools/ttnn-jit/_src/jit_functions.py)
*   [tools/ttnn-jit/_src/memory_analyzer.py](https://github.com/tenstorrent/tt-mlir/blob/c7d92e92/tools/ttnn-jit/_src/memory_analyzer.py)
*   [tools/ttnn-jit/_src/supported_ops.py](https://github.com/tenstorrent/tt-mlir/blob/c7d92e92/tools/ttnn-jit/_src/supported_ops.py)
*   [tools/ttnn-jit/_src/tensor_translator.py](https://github.com/tenstorrent/tt-mlir/blob/c7d92e92/tools/ttnn-jit/_src/tensor_translator.py)
*   [tools/ttnn-jit/_src/tracing_compiler.py](https://github.com/tenstorrent/tt-mlir/blob/c7d92e92/tools/ttnn-jit/_src/tracing_compiler.py)
*   [tools/ttnn-jit/api.py](https://github.com/tenstorrent/tt-mlir/blob/c7d92e92/tools/ttnn-jit/api.py)

This page provides an overview of the advanced compiler and runtime features in `tt-mlir`. These systems extend the core compilation pipeline to support dynamic execution, custom hardware kernel authoring, and distributed processing across multi-device meshes.

## TTNN-JIT: Just-In-Time Compilation

`ttnn.jit` is a tool that allows TTNN model developers to leverage the Direct-To-Metal (D2M) compiler to JIT compile select portions of their model into optimized hardware kernels [docs/src/ttnn-jit.md 3-4](https://github.com/tenstorrent/tt-mlir/blob/c7d92e92/docs/src/ttnn-jit.md?plain=1#L3-L4) It uses a Python decorator `@ttnn_jit.jit()` to wrap TTNN subgraphs, which are then traced and lowered through the `tt-mlir` pipeline [docs/src/ttnn-jit.md 61-68](https://github.com/tenstorrent/tt-mlir/blob/c7d92e92/docs/src/ttnn-jit.md?plain=1#L61-L68)

### JIT Pipeline Architecture

The JIT system operates in three primary stages:

1.   **Tracing**: The `JitFunction` class captures Python execution [tools/ttnn-jit/_src/jit.py 29-30](https://github.com/tenstorrent/tt-mlir/blob/c7d92e92/tools/ttnn-jit/_src/jit.py#L29-L30) It rewrites source code so `ttnn.*` calls are redirected to JIT-aware handlers [docs/src/ttnn-jit.md 140-145](https://github.com/tenstorrent/tt-mlir/blob/c7d92e92/docs/src/ttnn-jit.md?plain=1#L140-L145)
2.   **IR Generation**: The `generate_ir` function converts the traced operations into the `TTIR` dialect [tools/ttnn-jit/_src/jit.py 146-152](https://github.com/tenstorrent/tt-mlir/blob/c7d92e92/tools/ttnn-jit/_src/jit.py#L146-L152) It maps user-facing op names to `TTIR` dialect names using the `TTIR_NAME_MAP`[tools/ttnn-jit/_src/supported_ops.py 15-23](https://github.com/tenstorrent/tt-mlir/blob/c7d92e92/tools/ttnn-jit/_src/supported_ops.py#L15-L23)
3.   **D2M Lowering**: The `TTIR` is lowered to `TTMetal` via the `ttnn_to_ttmetal_pipeline`, which performs grid selection and memory allocation [tools/ttnn-jit/_src/jit.py 159-164](https://github.com/tenstorrent/tt-mlir/blob/c7d92e92/tools/ttnn-jit/_src/jit.py#L159-L164)

### JIT Tracing and Execution Flow

The following diagram illustrates how the Python `JitFunction` interacts with the MLIR generator and runtime dispatch.

**JIT Tracing to Binary Dispatch**

Sources: [tools/ttnn-jit/_src/jit.py 29-187](https://github.com/tenstorrent/tt-mlir/blob/c7d92e92/tools/ttnn-jit/_src/jit.py#L29-L187)[tools/ttnn-jit/api.py 11-56](https://github.com/tenstorrent/tt-mlir/blob/c7d92e92/tools/ttnn-jit/api.py#L11-L56)[docs/src/ttnn-jit.md 139-161](https://github.com/tenstorrent/tt-mlir/blob/c7d92e92/docs/src/ttnn-jit.md?plain=1#L139-L161)[tools/ttnn-jit/_src/supported_ops.py 15-23](https://github.com/tenstorrent/tt-mlir/blob/c7d92e92/tools/ttnn-jit/_src/supported_ops.py#L15-L23)

For details, see [TTNN-JIT: Just-In-Time Compilation](https://deepwiki.com/tenstorrent/tt-mlir/9.1-ttnn-jit:-just-in-time-compilation).

* * *

## PyKernel: Python Kernel DSL

PyKernel is a domain-specific language (DSL) that allows developers to write Tenstorrent hardware kernels directly in Python [test/pykernel/unit_tests.py 8-15](https://github.com/tenstorrent/tt-mlir/blob/c7d92e92/test/pykernel/unit_tests.py#L8-L15) It provides decorators like `@ttkernel_compile` to transform Python syntax into the `TTKernel` dialect and subsequently into C++ for the Tensix processors [tools/ttnn-jit/_src/jit.py 12-16](https://github.com/tenstorrent/tt-mlir/blob/c7d92e92/tools/ttnn-jit/_src/jit.py#L12-L16)

### Supported Operations

*   **Arithmetic**: Maps Python operators (e.g., `+`, `-`, `*`, `//`) to `arith` dialect operations [test/pykernel/unit_tests.py 142-182](https://github.com/tenstorrent/tt-mlir/blob/c7d92e92/test/pykernel/unit_tests.py#L142-L182)
*   **Control Flow**: Supports `if` statements and `for` loops, lowering them to `scf.if` and `scf.for`[test/pykernel/unit_tests.py 54-138](https://github.com/tenstorrent/tt-mlir/blob/c7d92e92/test/pykernel/unit_tests.py#L54-L138)
*   **Memory Access**: Handles array declarations and indexing via `memref`[test/pykernel/unit_tests.py 257-285](https://github.com/tenstorrent/tt-mlir/blob/c7d92e92/test/pykernel/unit_tests.py#L257-L285)
*   **Hardware Primitives**: Provides access to NOC (Network-on-Chip) and SFPU instructions via specialized decorators like `@ttkernel_noc_compile`[test/pykernel/unit_tests.py 8](https://github.com/tenstorrent/tt-mlir/blob/c7d92e92/test/pykernel/unit_tests.py#L8-L8)

Sources: [test/pykernel/unit_tests.py 1-285](https://github.com/tenstorrent/tt-mlir/blob/c7d92e92/test/pykernel/unit_tests.py#L1-L285)[tools/ttnn-jit/_src/jit.py 15-16](https://github.com/tenstorrent/tt-mlir/blob/c7d92e92/tools/ttnn-jit/_src/jit.py#L15-L16)

For details, see [PyKernel: Python Kernel DSL](https://deepwiki.com/tenstorrent/tt-mlir/9.2-pykernel:-python-kernel-dsl).

* * *

## Distributed Execution and Collective Operations

`tt-mlir` supports multi-device execution through collective communication operations (CCL) and mesh-aware sharding.

### Collective Operations

The compiler supports several standard CCL primitives, including `all_gather`, `all_reduce`, and `reduce_scatter`[docs/src/ttnn-jit.md 102](https://github.com/tenstorrent/tt-mlir/blob/c7d92e92/docs/src/ttnn-jit.md?plain=1#L102-L102) These are integrated into the JIT tracing and IR generation path via the `ccl_ops` registry [tools/ttnn-jit/_src/supported_ops.py 99-104](https://github.com/tenstorrent/tt-mlir/blob/c7d92e92/tools/ttnn-jit/_src/supported_ops.py#L99-L104) Conversion patterns such as `TTNNMatmulToTTIRMatmulConversionPattern` handle the lowering of these operations to hardware-specific implementations [lib/Conversion/TTNNToTTIR/TTNNToTTIRPatterns.cpp 159-183](https://github.com/tenstorrent/tt-mlir/blob/c7d92e92/lib/Conversion/TTNNToTTIR/TTNNToTTIRPatterns.cpp#L159-L183)

### Distributed Infrastructure

The system uses a `Controller` to orchestrate execution across multiple workers. It manages `SystemDesc` merging to describe the hardware topology across the mesh [tools/ttnn-jit/_src/jit.py 82-106](https://github.com/tenstorrent/tt-mlir/blob/c7d92e92/tools/ttnn-jit/_src/jit.py#L82-L106) Automatic parallelization is supported via sharding strategies, with utility functions like `get_maximal_block_sharding_grid` used to calculate optimal core grids for partitioned tensors [test/ttnn-jit/utils.py 85-95](https://github.com/tenstorrent/tt-mlir/blob/c7d92e92/test/ttnn-jit/utils.py#L85-L95) The compiler also handles `sdy.manual_computation` blocks from the Shardy dialect, converting them to `ttir.mesh_shard` operations [lib/Conversion/StableHLOToTTIR/ShardyToTTIRPatterns.cpp 152-180](https://github.com/tenstorrent/tt-mlir/blob/c7d92e92/lib/Conversion/StableHLOToTTIR/ShardyToTTIRPatterns.cpp#L152-L180)

**Distributed Entity Mapping**

Sources: [tools/ttnn-jit/_src/jit.py 82-120](https://github.com/tenstorrent/tt-mlir/blob/c7d92e92/tools/ttnn-jit/_src/jit.py#L82-L120)[test/ttnn-jit/utils.py 85-95](https://github.com/tenstorrent/tt-mlir/blob/c7d92e92/test/ttnn-jit/utils.py#L85-L95)[tools/ttnn-jit/_src/supported_ops.py 99-104](https://github.com/tenstorrent/tt-mlir/blob/c7d92e92/tools/ttnn-jit/_src/supported_ops.py#L99-L104)[lib/Conversion/StableHLOToTTIR/ShardyToTTIRPatterns.cpp 45-150](https://github.com/tenstorrent/tt-mlir/blob/c7d92e92/lib/Conversion/StableHLOToTTIR/ShardyToTTIRPatterns.cpp#L45-L150)

For details, see [Distributed Execution and Collective Operations](https://deepwiki.com/tenstorrent/tt-mlir/9.3-distributed-execution-and-collective-operations).

* * *

## Op-by-Op Execution Infrastructure

The op-by-op execution framework provides a robust environment for isolating, testing, and validating individual operations against reference implementations.

### Key Components

*   **Unit Test Runners**: The `run_op_test` utility automates input tensor creation, JIT compilation, and verification [test/ttnn-jit/utils.py 182-219](https://github.com/tenstorrent/tt-mlir/blob/c7d92e92/test/ttnn-jit/utils.py#L182-L219)
*   **Validation Metrics**: Uses PCC (Pearson Correlation Coefficient) via `pcc_check` and `all_close_check` to ensure hardware results match Torch golden values [test/ttnn-jit/utils.py 98-118](https://github.com/tenstorrent/tt-mlir/blob/c7d92e92/test/ttnn-jit/utils.py#L98-L118)
*   **Memory Analysis**: The `MemoryAnalyzer` class tracks available L1/DRAM ranges and validates that output tensor requirements fit within hardware limits [tools/ttnn-jit/_src/jit.py 155-159](https://github.com/tenstorrent/tt-mlir/blob/c7d92e92/tools/ttnn-jit/_src/jit.py#L155-L159)
*   **Program Caching**: The `JitCache` mechanism optimizes performance by caching compiled binaries indexed by runtime tensor metadata [tools/ttnn-jit/_src/jit.py 67-69](https://github.com/tenstorrent/tt-mlir/blob/c7d92e92/tools/ttnn-jit/_src/jit.py#L67-L69)

### Operation Coverage

The infrastructure facilitates systematic testing across categories defined in `supported_ops.py`:

| Category | Examples |
| --- | --- |
| **Unary** | `abs`, `exp`, `neg`, `relu`, `sigmoid`, `gelu`[tools/ttnn-jit/_src/supported_ops.py 27-53](https://github.com/tenstorrent/tt-mlir/blob/c7d92e92/tools/ttnn-jit/_src/supported_ops.py#L27-L53) |
| **Binary** | `add`, `multiply`, `subtract`, `divide`, `maximum`[tools/ttnn-jit/_src/supported_ops.py 56-73](https://github.com/tenstorrent/tt-mlir/blob/c7d92e92/tools/ttnn-jit/_src/supported_ops.py#L56-L73) |
| **Reduction** | `sum`, `mean`, `max`, `min`[tools/ttnn-jit/_src/supported_ops.py 76-81](https://github.com/tenstorrent/tt-mlir/blob/c7d92e92/tools/ttnn-jit/_src/supported_ops.py#L76-L81) |
| **TM/Data Movement** | `permute`, `transpose`, `reshape`, `concat`[tools/ttnn-jit/_src/supported_ops.py 85-97](https://github.com/tenstorrent/tt-mlir/blob/c7d92e92/tools/ttnn-jit/_src/supported_ops.py#L85-L97) |

**Op-by-Op Validation Flow**

Sources: [test/ttnn-jit/utils.py 182-219](https://github.com/tenstorrent/tt-mlir/blob/c7d92e92/test/ttnn-jit/utils.py#L182-L219)[tools/ttnn-jit/_src/jit.py 155-159](https://github.com/tenstorrent/tt-mlir/blob/c7d92e92/tools/ttnn-jit/_src/jit.py#L155-L159)[tools/ttnn-jit/_src/supported_ops.py 27-97](https://github.com/tenstorrent/tt-mlir/blob/c7d92e92/tools/ttnn-jit/_src/supported_ops.py#L27-L97)[tools/ttnn-jit/_src/jit.py 67-70](https://github.com/tenstorrent/tt-mlir/blob/c7d92e92/tools/ttnn-jit/_src/jit.py#L67-L70)[tools/ttnn-jit/_src/tensor_translator.py 1-20](https://github.com/tenstorrent/tt-mlir/blob/c7d92e92/tools/ttnn-jit/_src/tensor_translator.py#L1-L20)

For details, see [Op-by-Op Execution Infrastructure](https://deepwiki.com/tenstorrent/tt-mlir/9.4-op-by-op-execution-infrastructure).

This wiki is featured in the [repository](https://github.com/tenstorrent/tt-mlir/blob/main/README.md)

Dismiss
Refresh this wiki

Enter email to refresh
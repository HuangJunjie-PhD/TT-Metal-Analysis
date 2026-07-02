---
project: tt-metal
github: https://github.com/tenstorrent/tt-metal
deepwiki: https://deepwiki.com/tenstorrent/tt-metal/1-overview
---

# Overview

Relevant source files
*   [.github/CODEOWNERS](https://github.com/tenstorrent/tt-metal/blob/f30f8df0/.github/CODEOWNERS)
*   [.github/pull_request_template.md](https://github.com/tenstorrent/tt-metal/blob/f30f8df0/.github/pull_request_template.md?plain=1)
*   [.github/workflows/all-model-tests.yaml](https://github.com/tenstorrent/tt-metal/blob/f30f8df0/.github/workflows/all-model-tests.yaml)
*   [.github/workflows/fast-dispatch-full-regressions-and-models-impl.yaml](https://github.com/tenstorrent/tt-metal/blob/f30f8df0/.github/workflows/fast-dispatch-full-regressions-and-models-impl.yaml)
*   [.github/workflows/fast-dispatch-full-regressions-and-models.yaml](https://github.com/tenstorrent/tt-metal/blob/f30f8df0/.github/workflows/fast-dispatch-full-regressions-and-models.yaml)
*   [.github/workflows/galaxy-deepseek-tests-impl.yaml](https://github.com/tenstorrent/tt-metal/blob/f30f8df0/.github/workflows/galaxy-deepseek-tests-impl.yaml)
*   [.github/workflows/galaxy-deepseek-tests.yaml](https://github.com/tenstorrent/tt-metal/blob/f30f8df0/.github/workflows/galaxy-deepseek-tests.yaml)
*   [.github/workflows/galaxy-demo-tests-impl.yaml](https://github.com/tenstorrent/tt-metal/blob/f30f8df0/.github/workflows/galaxy-demo-tests-impl.yaml)
*   [.github/workflows/galaxy-demo-tests.yaml](https://github.com/tenstorrent/tt-metal/blob/f30f8df0/.github/workflows/galaxy-demo-tests.yaml)
*   [.github/workflows/galaxy-profiler-tests.yaml](https://github.com/tenstorrent/tt-metal/blob/f30f8df0/.github/workflows/galaxy-profiler-tests.yaml)
*   [.github/workflows/galaxy-stress-tests-impl.yaml](https://github.com/tenstorrent/tt-metal/blob/f30f8df0/.github/workflows/galaxy-stress-tests-impl.yaml)
*   [.github/workflows/galaxy-stress-tests.yaml](https://github.com/tenstorrent/tt-metal/blob/f30f8df0/.github/workflows/galaxy-stress-tests.yaml)
*   [.github/workflows/galaxy-unit-tests-impl.yaml](https://github.com/tenstorrent/tt-metal/blob/f30f8df0/.github/workflows/galaxy-unit-tests-impl.yaml)
*   [.github/workflows/galaxy-unit-tests.yaml](https://github.com/tenstorrent/tt-metal/blob/f30f8df0/.github/workflows/galaxy-unit-tests.yaml)
*   [.github/workflows/metal-run-microbenchmarks.yaml](https://github.com/tenstorrent/tt-metal/blob/f30f8df0/.github/workflows/metal-run-microbenchmarks.yaml)
*   [.github/workflows/perf-device-models-impl.yaml](https://github.com/tenstorrent/tt-metal/blob/f30f8df0/.github/workflows/perf-device-models-impl.yaml)
*   [.github/workflows/perf-device-models.yaml](https://github.com/tenstorrent/tt-metal/blob/f30f8df0/.github/workflows/perf-device-models.yaml)
*   [.github/workflows/perf-models-impl.yaml](https://github.com/tenstorrent/tt-metal/blob/f30f8df0/.github/workflows/perf-models-impl.yaml)
*   [.github/workflows/perf-models.yaml](https://github.com/tenstorrent/tt-metal/blob/f30f8df0/.github/workflows/perf-models.yaml)
*   [.github/workflows/pipeline-select-galaxy.yaml](https://github.com/tenstorrent/tt-metal/blob/f30f8df0/.github/workflows/pipeline-select-galaxy.yaml)
*   [.github/workflows/pipeline-select-t3k.yaml](https://github.com/tenstorrent/tt-metal/blob/f30f8df0/.github/workflows/pipeline-select-t3k.yaml)
*   [.github/workflows/pipeline-select.yaml](https://github.com/tenstorrent/tt-metal/blob/f30f8df0/.github/workflows/pipeline-select.yaml)
*   [.github/workflows/pr-description-inject-branch-name.yaml](https://github.com/tenstorrent/tt-metal/blob/f30f8df0/.github/workflows/pr-description-inject-branch-name.yaml)
*   [.github/workflows/single-card-demo-tests-impl.yaml](https://github.com/tenstorrent/tt-metal/blob/f30f8df0/.github/workflows/single-card-demo-tests-impl.yaml)
*   [.github/workflows/single-card-demo-tests.yaml](https://github.com/tenstorrent/tt-metal/blob/f30f8df0/.github/workflows/single-card-demo-tests.yaml)
*   [.github/workflows/t3000-demo-tests-impl.yaml](https://github.com/tenstorrent/tt-metal/blob/f30f8df0/.github/workflows/t3000-demo-tests-impl.yaml)
*   [.github/workflows/t3000-demo-tests.yaml](https://github.com/tenstorrent/tt-metal/blob/f30f8df0/.github/workflows/t3000-demo-tests.yaml)
*   [.github/workflows/t3000-e2e-tests.yaml](https://github.com/tenstorrent/tt-metal/blob/f30f8df0/.github/workflows/t3000-e2e-tests.yaml)
*   [.github/workflows/t3000-integration-tests.yaml](https://github.com/tenstorrent/tt-metal/blob/f30f8df0/.github/workflows/t3000-integration-tests.yaml)
*   [.github/workflows/t3000-perf-tests.yaml](https://github.com/tenstorrent/tt-metal/blob/f30f8df0/.github/workflows/t3000-perf-tests.yaml)
*   [.github/workflows/t3000-profiler-tests-impl.yaml](https://github.com/tenstorrent/tt-metal/blob/f30f8df0/.github/workflows/t3000-profiler-tests-impl.yaml)
*   [.github/workflows/t3000-profiler-tests.yaml](https://github.com/tenstorrent/tt-metal/blob/f30f8df0/.github/workflows/t3000-profiler-tests.yaml)
*   [.github/workflows/t3000-unit-tests-impl.yaml](https://github.com/tenstorrent/tt-metal/blob/f30f8df0/.github/workflows/t3000-unit-tests-impl.yaml)
*   [.github/workflows/t3000-unit-tests.yaml](https://github.com/tenstorrent/tt-metal/blob/f30f8df0/.github/workflows/t3000-unit-tests.yaml)
*   [.github/workflows/test-dispatch.yaml](https://github.com/tenstorrent/tt-metal/blob/f30f8df0/.github/workflows/test-dispatch.yaml)
*   [CONTRIBUTING.md](https://github.com/tenstorrent/tt-metal/blob/f30f8df0/CONTRIBUTING.md?plain=1)
*   [README.md](https://github.com/tenstorrent/tt-metal/blob/f30f8df0/README.md?plain=1)
*   [models/README.md](https://github.com/tenstorrent/tt-metal/blob/f30f8df0/models/README.md?plain=1)
*   [models/demos/deepseek_v3/README.md](https://github.com/tenstorrent/tt-metal/blob/f30f8df0/models/demos/deepseek_v3/README.md?plain=1)
*   [models/demos/deepseek_v3/tests/fused_op_unit_tests/mla/test_ds_mla.py](https://github.com/tenstorrent/tt-metal/blob/f30f8df0/models/demos/deepseek_v3/tests/fused_op_unit_tests/mla/test_ds_mla.py)
*   [models/demos/deepseek_v3/tests/fused_op_unit_tests/moe/test_ds_moe.py](https://github.com/tenstorrent/tt-metal/blob/f30f8df0/models/demos/deepseek_v3/tests/fused_op_unit_tests/moe/test_ds_moe.py)
*   [models/demos/deepseek_v3/tests/fused_op_unit_tests/run_ci_device_perf_tracy.sh](https://github.com/tenstorrent/tt-metal/blob/f30f8df0/models/demos/deepseek_v3/tests/fused_op_unit_tests/run_ci_device_perf_tracy.sh)
*   [models/demos/deepseek_v3/tests/test_compute_tg.py](https://github.com/tenstorrent/tt-metal/blob/f30f8df0/models/demos/deepseek_v3/tests/test_compute_tg.py)
*   [models/demos/deepseek_v3/tests/test_dispatch_tg.py](https://github.com/tenstorrent/tt-metal/blob/f30f8df0/models/demos/deepseek_v3/tests/test_dispatch_tg.py)
*   [models/demos/deepseek_v3/tests/test_optimized_moe_decode_block_tg.py](https://github.com/tenstorrent/tt-metal/blob/f30f8df0/models/demos/deepseek_v3/tests/test_optimized_moe_decode_block_tg.py)
*   [models/demos/llama3_70b_galaxy/PERF.md](https://github.com/tenstorrent/tt-metal/blob/f30f8df0/models/demos/llama3_70b_galaxy/PERF.md?plain=1)
*   [models/demos/llama3_70b_galaxy/README.md](https://github.com/tenstorrent/tt-metal/blob/f30f8df0/models/demos/llama3_70b_galaxy/README.md?plain=1)
*   [models/demos/multimodal/gemma3/README.md](https://github.com/tenstorrent/tt-metal/blob/f30f8df0/models/demos/multimodal/gemma3/README.md?plain=1)
*   [models/demos/t3000/llama3_70b/README.md](https://github.com/tenstorrent/tt-metal/blob/f30f8df0/models/demos/t3000/llama3_70b/README.md?plain=1)
*   [models/demos/t3000/llama3_70b/setup_llama.sh](https://github.com/tenstorrent/tt-metal/blob/f30f8df0/models/demos/t3000/llama3_70b/setup_llama.sh)
*   [models/demos/wormhole/qwen3_embedding_8b/demo/generator_vllm.py](https://github.com/tenstorrent/tt-metal/blob/f30f8df0/models/demos/wormhole/qwen3_embedding_8b/demo/generator_vllm.py)
*   [models/docs/MODEL_HYBRID_TP_DP.md](https://github.com/tenstorrent/tt-metal/blob/f30f8df0/models/docs/MODEL_HYBRID_TP_DP.md?plain=1)
*   [models/docs/MODEL_UPDATES.md](https://github.com/tenstorrent/tt-metal/blob/f30f8df0/models/docs/MODEL_UPDATES.md?plain=1)
*   [models/docs/model_bring_up.md](https://github.com/tenstorrent/tt-metal/blob/f30f8df0/models/docs/model_bring_up.md?plain=1)
*   [models/perf/merge_device_perf_results.py](https://github.com/tenstorrent/tt-metal/blob/f30f8df0/models/perf/merge_device_perf_results.py)
*   [releases/README.md](https://github.com/tenstorrent/tt-metal/blob/f30f8df0/releases/README.md?plain=1)
*   [scripts/tracing/.gitattributes](https://github.com/tenstorrent/tt-metal/blob/f30f8df0/scripts/tracing/.gitattributes)
*   [scripts/tracing/.gitignore](https://github.com/tenstorrent/tt-metal/blob/f30f8df0/scripts/tracing/.gitignore)
*   [scripts/tracing/README.md](https://github.com/tenstorrent/tt-metal/blob/f30f8df0/scripts/tracing/README.md?plain=1)
*   [scripts/tracing/context.txt](https://github.com/tenstorrent/tt-metal/blob/f30f8df0/scripts/tracing/context.txt)
*   [scripts/tracing/questions.txt](https://github.com/tenstorrent/tt-metal/blob/f30f8df0/scripts/tracing/questions.txt)
*   [scripts/tracing/run.py](https://github.com/tenstorrent/tt-metal/blob/f30f8df0/scripts/tracing/run.py)
*   [scripts/tracing/system-prompt.txt](https://github.com/tenstorrent/tt-metal/blob/f30f8df0/scripts/tracing/system-prompt.txt)
*   [tech_reports/Debugging/Kernel_Debugging_Tips.md](https://github.com/tenstorrent/tt-metal/blob/f30f8df0/tech_reports/Debugging/Kernel_Debugging_Tips.md?plain=1)
*   [tech_reports/LLMs/vLLM_integration.md](https://github.com/tenstorrent/tt-metal/blob/f30f8df0/tech_reports/LLMs/vLLM_integration.md?plain=1)
*   [tests/pipeline_reorg/t3k_demo_tests.yaml](https://github.com/tenstorrent/tt-metal/blob/f30f8df0/tests/pipeline_reorg/t3k_demo_tests.yaml)
*   [tests/pipeline_reorg/t3k_integration_tests.yaml](https://github.com/tenstorrent/tt-metal/blob/f30f8df0/tests/pipeline_reorg/t3k_integration_tests.yaml)
*   [tests/pipeline_reorg/t3k_perf_tests.yaml](https://github.com/tenstorrent/tt-metal/blob/f30f8df0/tests/pipeline_reorg/t3k_perf_tests.yaml)
*   [tests/scripts/run_python_model_tests.sh](https://github.com/tenstorrent/tt-metal/blob/f30f8df0/tests/scripts/run_python_model_tests.sh)
*   [tests/scripts/single_card/run_single_card_demo_tests.sh](https://github.com/tenstorrent/tt-metal/blob/f30f8df0/tests/scripts/single_card/run_single_card_demo_tests.sh)
*   [tests/scripts/t3000/run_t3000_demo_tests.sh](https://github.com/tenstorrent/tt-metal/blob/f30f8df0/tests/scripts/t3000/run_t3000_demo_tests.sh)
*   [tests/scripts/t3000/run_t3000_integration_tests.sh](https://github.com/tenstorrent/tt-metal/blob/f30f8df0/tests/scripts/t3000/run_t3000_integration_tests.sh)
*   [tests/scripts/t3000/run_t3000_perf_tests.sh](https://github.com/tenstorrent/tt-metal/blob/f30f8df0/tests/scripts/t3000/run_t3000_perf_tests.sh)
*   [tests/scripts/t3000/run_t3000_perplexity_tests.sh](https://github.com/tenstorrent/tt-metal/blob/f30f8df0/tests/scripts/t3000/run_t3000_perplexity_tests.sh)
*   [tests/scripts/t3000/run_t3000_unit_tests.sh](https://github.com/tenstorrent/tt-metal/blob/f30f8df0/tests/scripts/t3000/run_t3000_unit_tests.sh)
*   [tests/scripts/tg/run_tg_frequent_tests.sh](https://github.com/tenstorrent/tt-metal/blob/f30f8df0/tests/scripts/tg/run_tg_frequent_tests.sh)
*   [tests/scripts/wh_6u/run_wh_6u_profiler_tests.sh](https://github.com/tenstorrent/tt-metal/blob/f30f8df0/tests/scripts/wh_6u/run_wh_6u_profiler_tests.sh)

## Purpose and Scope

The `tt-metal` repository provides a complete software stack for Tenstorrent AI accelerators, consisting of two primary tiers:

*   **TT-NN**: A high-level neural network operation library providing a Python and C++ API for tensor operations, collective communications, and model primitives optimized for Tenstorrent hardware. [README.md 12-22](https://github.com/tenstorrent/tt-metal/blob/f30f8df0/README.md?plain=1#L12-L22)
*   **TT-Metalium**: A low-level programming model and runtime enabling kernel development for Tenstorrent hardware. It provides direct access to the architecture, including Tensix cores, Ethernet cores, and the Network-on-Chip (NoC). [README.md 95-102](https://github.com/tenstorrent/tt-metal/blob/f30f8df0/README.md?plain=1#L95-L102)

This page introduces the overall system architecture and component interactions. For detailed information on specific subsystems, refer to the following child pages:

 — Organization of major directories (`tt_metal`, `ttnn`, `models`, `tests`, `tt-train`, `tools`).
 — High-level interaction between TT-Metalium, TTNN, and the Fabric layer.
 — Guide to `build_metal.sh`, dependencies, and environment configuration using `install_dependencies.sh`.
 — Details on Wormhole (N150/N300) and Blackhole (P100/P150) architectures.

**Sources**: [README.md 1-102](https://github.com/tenstorrent/tt-metal/blob/f30f8df0/README.md?plain=1#L1-L102)

* * *

## Repository Components

### TT-Metalium: Low-Level Programming Framework

TT-Metalium manages hardware abstraction and execution through several core systems:

*   **Context and Device Management**: Managed via the `MetalContext` singleton [tt_metal/impl/context/metal_context.hpp 56-67](https://github.com/tenstorrent/tt-metal/blob/f30f8df0/tt_metal/impl/context/metal_context.hpp#L56-L67) and `MetalEnv`[tt_metal/impl/context/metal_context.hpp 16-17](https://github.com/tenstorrent/tt-metal/blob/f30f8df0/tt_metal/impl/context/metal_context.hpp#L16-L17) Hardware is abstracted through `IDevice`, with implementations for single chips (`Device`) and multi-chip clusters (`MeshDevice`). [tt_metal/impl/context/metal_context.hpp 110-117](https://github.com/tenstorrent/tt-metal/blob/f30f8df0/tt_metal/impl/context/metal_context.hpp#L110-L117)
*   **Program and Kernel System**: Orchestrates execution using `Program` objects that contain `Kernel` definitions for compute and data movement.
*   **JIT Build System**: Handles on-the-fly compilation of RISC-V kernels using `JitBuildEnv`[tt_metal/jit_build/build.hpp 99](https://github.com/tenstorrent/tt-metal/blob/f30f8df0/tt_metal/jit_build/build.hpp#L99-L99) and `BuildEnvManager`. [tt_metal/jit_build/build_env_manager.hpp 20](https://github.com/tenstorrent/tt-metal/blob/f30f8df0/tt_metal/jit_build/build_env_manager.hpp#L20-L20) It utilizes the `SFPI` toolchain for specialized RISC-V math operations. [tt_metal/jit_build/build.cpp 125-140](https://github.com/tenstorrent/tt-metal/blob/f30f8df0/tt_metal/jit_build/build.cpp#L125-L140)
*   **Fast Dispatch**: A hardware command queue system (`HWCommandQueue`) that manages low-latency command submission to dispatch cores. [tt_metal/impl/context/metal_context.hpp 118-123](https://github.com/tenstorrent/tt-metal/blob/f30f8df0/tt_metal/impl/context/metal_context.hpp#L118-L123)

**Sources**: [tt_metal/impl/context/metal_context.hpp 56-123](https://github.com/tenstorrent/tt-metal/blob/f30f8df0/tt_metal/impl/context/metal_context.hpp#L56-L123)[tt_metal/jit_build/build.hpp 99](https://github.com/tenstorrent/tt-metal/blob/f30f8df0/tt_metal/jit_build/build.hpp#L99-L99)[tt_metal/jit_build/build_env_manager.hpp 20](https://github.com/tenstorrent/tt-metal/blob/f30f8df0/tt_metal/jit_build/build_env_manager.hpp#L20-L20)[tt_metal/jit_build/build.cpp 125-140](https://github.com/tenstorrent/tt-metal/blob/f30f8df0/tt_metal/jit_build/build.cpp#L125-L140)

### TT-NN: Neural Network Library

TT-NN provides the high-level interface for model developers:

*   **Tensor Abstraction**: Manages multi-dimensional data layouts, memory configurations, and sharding strategies via `ttnn::Tensor`. [ttnn/ttnn/__init__.py 244-250](https://github.com/tenstorrent/tt-metal/blob/f30f8df0/ttnn/ttnn/__init__.py#L244-L250)
*   **Operation Library**: Optimized implementations of matmul, convolutions, and transformer-specific ops used in models like Llama 3.3, Qwen 2.5, and DeepSeek-R1. [README.md 40-53](https://github.com/tenstorrent/tt-metal/blob/f30f8df0/README.md?plain=1#L40-L53)[tests/scripts/single_card/run_single_card_demo_tests.sh 24-30](https://github.com/tenstorrent/tt-metal/blob/f30f8df0/tests/scripts/single_card/run_single_card_demo_tests.sh#L24-L30)
*   **Distributed Execution**: Support for Tensor Parallelism (TP) and Data Parallelism (DP) across `MeshDevice` topologies, critical for large models like Llama 3.3 70B (TP=32). [README.md 36-43](https://github.com/tenstorrent/tt-metal/blob/f30f8df0/README.md?plain=1#L36-L43)

**Sources**: [README.md 12-65](https://github.com/tenstorrent/tt-metal/blob/f30f8df0/README.md?plain=1#L12-L65)[ttnn/ttnn/__init__.py 244-250](https://github.com/tenstorrent/tt-metal/blob/f30f8df0/ttnn/ttnn/__init__.py#L244-L250)[tests/scripts/single_card/run_single_card_demo_tests.sh 24-30](https://github.com/tenstorrent/tt-metal/blob/f30f8df0/tests/scripts/single_card/run_single_card_demo_tests.sh#L24-L30)

* * *

## System Architecture Layers

### High-Level Stack Diagram

**Architecture Summary**

The stack is strictly layered to provide both high-level productivity and low-level performance:

| Layer | Purpose | Key Code Entities |
| --- | --- | --- |
| **User Space** | Model implementation | `models/demos/`[README.md 18](https://github.com/tenstorrent/tt-metal/blob/f30f8df0/README.md?plain=1#L18-L18) |
| **TT-NN Library** | High-level primitives | `ttnn`[README.md 14](https://github.com/tenstorrent/tt-metal/blob/f30f8df0/README.md?plain=1#L14-L14) |
| **TT-Metalium** | Programming model | `tt_metal` |
| **LLRT** | HW Abstraction | `llrt` |
| **Hardware** | Execution | `umd`, `Firmware` |

**Sources**: [README.md 12-102](https://github.com/tenstorrent/tt-metal/blob/f30f8df0/README.md?plain=1#L12-L102)[ttnn/ttnn/__init__.py 1-250](https://github.com/tenstorrent/tt-metal/blob/f30f8df0/ttnn/ttnn/__init__.py#L1-L250)[tt_metal/impl/context/metal_context.hpp 56-123](https://github.com/tenstorrent/tt-metal/blob/f30f8df0/tt_metal/impl/context/metal_context.hpp#L56-L123)[tt_metal/jit_build/build.hpp 99](https://github.com/tenstorrent/tt-metal/blob/f30f8df0/tt_metal/jit_build/build.hpp#L99-L99)[tt_metal/jit_build/build_env_manager.hpp 20](https://github.com/tenstorrent/tt-metal/blob/f30f8df0/tt_metal/jit_build/build_env_manager.hpp#L20-L20)[tt_metal/jit_build/build.cpp 125-140](https://github.com/tenstorrent/tt-metal/blob/f30f8df0/tt_metal/jit_build/build.cpp#L125-L140)[tt_metal/llrt/tt_cluster.hpp 61](https://github.com/tenstorrent/tt-metal/blob/f30f8df0/tt_metal/llrt/tt_cluster.hpp#L61-L61)[tt_metal/fabric/control_plane.cpp 33](https://github.com/tenstorrent/tt-metal/blob/f30f8df0/tt_metal/fabric/control_plane.cpp#L33-L33)[tt_metal/llrt/hal.hpp 1](https://github.com/tenstorrent/tt-metal/blob/f30f8df0/tt_metal/llrt/hal.hpp#L1-L1)[tt_metal/llrt/tt_cluster.hpp 26](https://github.com/tenstorrent/tt-metal/blob/f30f8df0/tt_metal/llrt/tt_cluster.hpp#L26-L26)

* * *

## Core Runtime Flow

### Component Initialization and Execution

**Component Responsibilities**

*   **MetalContext**: Manages global state, including `DeviceManager` and `Cluster` discovery. The `MetalContext::instance()` method provides access to the singleton. [tt_metal/impl/context/metal_context.hpp 65](https://github.com/tenstorrent/tt-metal/blob/f30f8df0/tt_metal/impl/context/metal_context.hpp#L65-L65)
*   **JIT Build System**: Configures the compiler toolchain, including the RISC-V SFPI compiler path (`riscv-tt-elf-g++`). This is handled by `JitBuildEnv::init`. [tt_metal/jit_build/build.cpp 125-140](https://github.com/tenstorrent/tt-metal/blob/f30f8df0/tt_metal/jit_build/build.cpp#L125-L140)
*   **HAL**: Provides architecture-specific memory maps and core configurations for Tensix and Ethernet cores. The `Hal` object is accessed via `MetalContext::hal()`. [tt_metal/impl/context/metal_context.hpp 88](https://github.com/tenstorrent/tt-metal/blob/f30f8df0/tt_metal/impl/context/metal_context.hpp#L88-L88)

**Sources**: [tt_metal/impl/context/metal_context.hpp 65](https://github.com/tenstorrent/tt-metal/blob/f30f8df0/tt_metal/impl/context/metal_context.hpp#L65-L65)[tt_metal/jit_build/build.cpp 125-140](https://github.com/tenstorrent/tt-metal/blob/f30f8df0/tt_metal/jit_build/build.cpp#L125-L140)[tt_metal/impl/context/metal_context.hpp 88](https://github.com/tenstorrent/tt-metal/blob/f30f8df0/tt_metal/impl/context/metal_context.hpp#L88-L88)

* * *

## Hardware Platform Support

The stack supports Tenstorrent's Wormhole and Blackhole architectures:

### Supported Architectures

| Architecture | Chip Family | Characteristics |
| --- | --- | --- |
| **Wormhole** | N150, N300, Galaxy | Tensix cores + Active/Idle Ethernet cores. [tt_metal/llrt/tt_cluster.cpp 65](https://github.com/tenstorrent/tt-metal/blob/f30f8df0/tt_metal/llrt/tt_cluster.cpp#L65-L65) |
| **Blackhole** | P100, P150 | Higher core counts and updated NoC/Memory configurations. [tt_metal/llrt/tt_cluster.cpp 99](https://github.com/tenstorrent/tt-metal/blob/f30f8df0/tt_metal/llrt/tt_cluster.cpp#L99-L99) |

### System Configurations

*   **Single Device**: Standard N150 or P150 cards. [tt_metal/llrt/tt_cluster.cpp 134](https://github.com/tenstorrent/tt-metal/blob/f30f8df0/tt_metal/llrt/tt_cluster.cpp#L134-L134)
*   **N300**: Dual-chip Wormhole configuration. [tt_metal/llrt/tt_cluster.cpp 134](https://github.com/tenstorrent/tt-metal/blob/f30f8df0/tt_metal/llrt/tt_cluster.cpp#L134-L134)
*   **T3000**: 8-chip Wormhole system. [tests/scripts/t3000/run_t3000_unit_tests.sh 12-47](https://github.com/tenstorrent/tt-metal/blob/f30f8df0/tests/scripts/t3000/run_t3000_unit_tests.sh#L12-L47)
*   **Galaxy**: 32-chip Wormhole cluster for large-scale model inference. [tt_metal/llrt/tt_cluster.cpp 116](https://github.com/tenstorrent/tt-metal/blob/f30f8df0/tt_metal/llrt/tt_cluster.cpp#L116-L116)[README.md 40-43](https://github.com/tenstorrent/tt-metal/blob/f30f8df0/README.md?plain=1#L40-L43)

**Sources**: [README.md 40-66](https://github.com/tenstorrent/tt-metal/blob/f30f8df0/README.md?plain=1#L40-L66)[tt_metal/llrt/tt_cluster.cpp 65-170](https://github.com/tenstorrent/tt-metal/blob/f30f8df0/tt_metal/llrt/tt_cluster.cpp#L65-L170)[tests/scripts/t3000/run_t3000_unit_tests.sh 12-47](https://github.com/tenstorrent/tt-metal/blob/f30f8df0/tests/scripts/t3000/run_t3000_unit_tests.sh#L12-L47)

* * *

## Build and Setup

The build system is based on CMake and supports various configurations:

*   **CMake Configuration**: Top-level project definition with options for shared libraries, Python bindings, and LTO. [cmake/](https://github.com/tenstorrent/tt-metal/blob/f30f8df0/cmake/)
*   **Toolchain**: Uses a custom RISC-V SFPI toolchain for device-side kernels. The `JitBuildEnv` class manages the SFPI toolchain path. [tt_metal/jit_build/build.cpp 125-140](https://github.com/tenstorrent/tt-metal/blob/f30f8df0/tt_metal/jit_build/build.cpp#L125-L140)
*   **Docker Environments**: Multi-stage builds for CI and development (`ci-build`, `ci-test`, `dev`). [.github/workflows/fast-dispatch-full-regressions-and-models-impl.yaml 166-170](https://github.com/tenstorrent/tt-metal/blob/f30f8df0/.github/workflows/fast-dispatch-full-regressions-and-models-impl.yaml#L166-L170)
*   **Dependencies**: Managed via `install_dependencies.sh` for various OS families (Ubuntu, RHEL).

**Sources**: [tt_metal/jit_build/build.cpp 125-140](https://github.com/tenstorrent/tt-metal/blob/f30f8df0/tt_metal/jit_build/build.cpp#L125-L140)[cmake/](https://github.com/tenstorrent/tt-metal/blob/f30f8df0/cmake/)

This wiki is featured in the [repository](https://github.com/tenstorrent/tt-metal/blob/main/README.md)

Dismiss
Refresh this wiki

Enter email to refresh
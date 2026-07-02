---
project: tt-inference-server
github: https://github.com/tenstorrent/tt-inference-server
deepwiki: https://deepwiki.com/tenstorrent/tt-inference-server/10-glossary
---

# Glossary

Relevant source files
*   [.github/workflows/test-gate.yml](https://github.com/tenstorrent/tt-inference-server/blob/8ab207f9/.github/workflows/test-gate.yml)
*   [.gitignore](https://github.com/tenstorrent/tt-inference-server/blob/8ab207f9/.gitignore)
*   [.gitmodules](https://github.com/tenstorrent/tt-inference-server/blob/8ab207f9/.gitmodules)
*   [benchmarking/benchmark_config.py](https://github.com/tenstorrent/tt-inference-server/blob/8ab207f9/benchmarking/benchmark_config.py)
*   [benchmarking/benchmark_targets/model_performance_reference.json](https://github.com/tenstorrent/tt-inference-server/blob/8ab207f9/benchmarking/benchmark_targets/model_performance_reference.json)
*   [benchmarking/run_benchmarks.py](https://github.com/tenstorrent/tt-inference-server/blob/8ab207f9/benchmarking/run_benchmarks.py)
*   [benchmarking/summary_report.py](https://github.com/tenstorrent/tt-inference-server/blob/8ab207f9/benchmarking/summary_report.py)
*   [evals/eval_config.py](https://github.com/tenstorrent/tt-inference-server/blob/8ab207f9/evals/eval_config.py)
*   [evals/run_evals.py](https://github.com/tenstorrent/tt-inference-server/blob/8ab207f9/evals/run_evals.py)
*   [tt-media-server/Dockerfile](https://github.com/tenstorrent/tt-inference-server/blob/8ab207f9/tt-media-server/Dockerfile)
*   [tt-media-server/README.md](https://github.com/tenstorrent/tt-inference-server/blob/8ab207f9/tt-media-server/README.md?plain=1)
*   [tt-media-server/config/constants.py](https://github.com/tenstorrent/tt-inference-server/blob/8ab207f9/tt-media-server/config/constants.py)
*   [tt-media-server/config/settings.py](https://github.com/tenstorrent/tt-inference-server/blob/8ab207f9/tt-media-server/config/settings.py)
*   [tt-media-server/cpp_server/CLAUDE.md](https://github.com/tenstorrent/tt-inference-server/blob/8ab207f9/tt-media-server/cpp_server/CLAUDE.md?plain=1)
*   [tt-media-server/cpp_server/CMakeLists.txt](https://github.com/tenstorrent/tt-inference-server/blob/8ab207f9/tt-media-server/cpp_server/CMakeLists.txt)
*   [tt-media-server/cpp_server/README.md](https://github.com/tenstorrent/tt-inference-server/blob/8ab207f9/tt-media-server/cpp_server/README.md?plain=1)
*   [tt-media-server/cpp_server/build.sh](https://github.com/tenstorrent/tt-inference-server/blob/8ab207f9/tt-media-server/cpp_server/build.sh)
*   [tt-media-server/cpp_server/include/api/llm_controller.hpp](https://github.com/tenstorrent/tt-inference-server/blob/8ab207f9/tt-media-server/cpp_server/include/api/llm_controller.hpp)
*   [tt-media-server/cpp_server/include/config/runner_config.hpp](https://github.com/tenstorrent/tt-inference-server/blob/8ab207f9/tt-media-server/cpp_server/include/config/runner_config.hpp)
*   [tt-media-server/cpp_server/include/config/types.hpp](https://github.com/tenstorrent/tt-inference-server/blob/8ab207f9/tt-media-server/cpp_server/include/config/types.hpp)
*   [tt-media-server/cpp_server/include/domain/models_response.hpp](https://github.com/tenstorrent/tt-inference-server/blob/8ab207f9/tt-media-server/cpp_server/include/domain/models_response.hpp)
*   [tt-media-server/cpp_server/include/runners/llm_runner/block_manager.hpp](https://github.com/tenstorrent/tt-inference-server/blob/8ab207f9/tt-media-server/cpp_server/include/runners/llm_runner/block_manager.hpp)
*   [tt-media-server/cpp_server/include/services/llm_service.hpp](https://github.com/tenstorrent/tt-inference-server/blob/8ab207f9/tt-media-server/cpp_server/include/services/llm_service.hpp)
*   [tt-media-server/cpp_server/src/api/llm_controller.cpp](https://github.com/tenstorrent/tt-inference-server/blob/8ab207f9/tt-media-server/cpp_server/src/api/llm_controller.cpp)
*   [tt-media-server/cpp_server/src/runners/llm_runner/block_manager.cpp](https://github.com/tenstorrent/tt-inference-server/blob/8ab207f9/tt-media-server/cpp_server/src/runners/llm_runner/block_manager.cpp)
*   [tt-media-server/cpp_server/src/runners/llm_runner/mock_model_runner.cpp](https://github.com/tenstorrent/tt-inference-server/blob/8ab207f9/tt-media-server/cpp_server/src/runners/llm_runner/mock_model_runner.cpp)
*   [tt-media-server/cpp_server/src/services/llm_service.cpp](https://github.com/tenstorrent/tt-inference-server/blob/8ab207f9/tt-media-server/cpp_server/src/services/llm_service.cpp)
*   [tt-media-server/cpp_server/tsan_suppressions.txt](https://github.com/tenstorrent/tt-inference-server/blob/8ab207f9/tt-media-server/cpp_server/tsan_suppressions.txt)
*   [tt-media-server/device_workers/worker_utils.py](https://github.com/tenstorrent/tt-inference-server/blob/8ab207f9/tt-media-server/device_workers/worker_utils.py)
*   [tt-media-server/fix_dependencies.sh](https://github.com/tenstorrent/tt-inference-server/blob/8ab207f9/tt-media-server/fix_dependencies.sh)
*   [tt-media-server/tests/conftest.py](https://github.com/tenstorrent/tt-inference-server/blob/8ab207f9/tt-media-server/tests/conftest.py)
*   [tt-media-server/tests/test_worker_utils.py](https://github.com/tenstorrent/tt-inference-server/blob/8ab207f9/tt-media-server/tests/test_worker_utils.py)
*   [tt-media-server/tt_model_runners/runner_fabric.py](https://github.com/tenstorrent/tt-inference-server/blob/8ab207f9/tt-media-server/tt_model_runners/runner_fabric.py)
*   [tt-media-server/tt_model_runners/yolov4_runner.py](https://github.com/tenstorrent/tt-inference-server/blob/8ab207f9/tt-media-server/tt_model_runners/yolov4_runner.py)
*   [tt-media-server/utils/runner_utils.py](https://github.com/tenstorrent/tt-inference-server/blob/8ab207f9/tt-media-server/utils/runner_utils.py)
*   [tt-media-server/uv-overrides.txt](https://github.com/tenstorrent/tt-inference-server/blob/8ab207f9/tt-media-server/uv-overrides.txt)
*   [workflows/model_spec.py](https://github.com/tenstorrent/tt-inference-server/blob/8ab207f9/workflows/model_spec.py)
*   [workflows/run_reports.py](https://github.com/tenstorrent/tt-inference-server/blob/8ab207f9/workflows/run_reports.py)
*   [workflows/workflow_types.py](https://github.com/tenstorrent/tt-inference-server/blob/8ab207f9/workflows/workflow_types.py)
*   [workflows/workflow_venvs.py](https://github.com/tenstorrent/tt-inference-server/blob/8ab207f9/workflows/workflow_venvs.py)

This page provides definitions for the technical terms, hardware names, and codebase-specific concepts used within the `tt-inference-server` repository. It serves as a reference for onboarding engineers to understand the mapping between domain language and implementation details.

## System Architecture Overview

The following diagram illustrates how high-level system components map to specific code entities across the Python and C++ layers.

### Code Entity Mapping

"System Concept to Code Entity Space"

**Sources:**[tt-media-server/README.md 20-31](https://github.com/tenstorrent/tt-inference-server/blob/8ab207f9/tt-media-server/README.md?plain=1#L20-L31)[tt-media-server/cpp_server/CMakeLists.txt 24-60](https://github.com/tenstorrent/tt-inference-server/blob/8ab207f9/tt-media-server/cpp_server/CMakeLists.txt#L24-L60)[tt-media-server/config/settings.py 127-145](https://github.com/tenstorrent/tt-inference-server/blob/8ab207f9/tt-media-server/config/settings.py#L127-L145)

* * *

## Hardware Terms

| Term | Definition | Code Pointer |
| --- | --- | --- |
| **N150** | Single Wormhole (WH) chip PCIe card. | [workflows/workflow_types.py 21](https://github.com/tenstorrent/tt-inference-server/blob/8ab207f9/workflows/workflow_types.py#L21-L21) |
| **N300** | Dual Wormhole (WH) chip PCIe card. | [workflows/workflow_types.py 21](https://github.com/tenstorrent/tt-inference-server/blob/8ab207f9/workflows/workflow_types.py#L21-L21) |
| **T3K** | A machine containing 8 Wormhole chips (e.g., 4x N300). | [benchmarking/benchmark_targets/model_performance_reference.json 3](https://github.com/tenstorrent/tt-inference-server/blob/8ab207f9/benchmarking/benchmark_targets/model_performance_reference.json#L3-L3) |
| **Galaxy** | A high-density system containing 32 Wormhole chips. | [benchmarking/benchmark_targets/model_performance_reference.json 174](https://github.com/tenstorrent/tt-inference-server/blob/8ab207f9/benchmarking/benchmark_targets/model_performance_reference.json#L174-L174) |
| **P-Series** | Blackhole (BH) based hardware configurations (e.g., P300). | [benchmarking/benchmark_targets/model_performance_reference.json 233](https://github.com/tenstorrent/tt-inference-server/blob/8ab207f9/benchmarking/benchmark_targets/model_performance_reference.json#L233-L233) |
| **QBGE** | A specialized Tenstorrent hardware configuration (e.g., for Wan2.2 or Flux). | [tt-media-server/README.md 149-155](https://github.com/tenstorrent/tt-inference-server/blob/8ab207f9/tt-media-server/README.md?plain=1#L149-L155) |

**Sources:**[tt-media-server/README.md 143-160](https://github.com/tenstorrent/tt-inference-server/blob/8ab207f9/tt-media-server/README.md?plain=1#L143-L160)[benchmarking/benchmark_targets/model_performance_reference.json 1-10](https://github.com/tenstorrent/tt-inference-server/blob/8ab207f9/benchmarking/benchmark_targets/model_performance_reference.json#L1-L10)[workflows/workflow_types.py 21-25](https://github.com/tenstorrent/tt-inference-server/blob/8ab207f9/workflows/workflow_types.py#L21-L25)

* * *

## Core Software Concepts

### ModelSpec

The central configuration object defining how a model is deployed. It includes the Hugging Face repo, hardware requirements, and inference engine parameters.

*   **Implementation:**`ModelSpec` dataclass in [workflows/model_spec.py 180-200](https://github.com/tenstorrent/tt-inference-server/blob/8ab207f9/workflows/model_spec.py#L180-L200)
*   **Registry:** The `MODEL_SPECS` dictionary contains all certified model configurations [evals/eval_config.py 13](https://github.com/tenstorrent/tt-inference-server/blob/8ab207f9/evals/eval_config.py#L13-L13)

### Runner Fabric

A factory pattern used in the Python media server to instantiate the correct model runner (e.g., SDXL, Whisper, or vLLM) based on the `ModelRunners` enum.

*   **Implementation:**`runner_fabric.py` in [tt-media-server/tt_model_runners/runner_fabric.py 1-50](https://github.com/tenstorrent/tt-inference-server/blob/8ab207f9/tt-media-server/tt_model_runners/runner_fabric.py#L1-L50)
*   **Enum Definitions:**[tt-media-server/config/constants.py 87-126](https://github.com/tenstorrent/tt-inference-server/blob/8ab207f9/tt-media-server/config/constants.py#L87-L126)

### LLM Service (C++)

The core logic for managing LLM requests, tokenization, and communication with workers via IPC.

*   **Implementation:**`LLMService` class in [tt-media-server/cpp_server/src/services/llm_service.cpp 30-45](https://github.com/tenstorrent/tt-inference-server/blob/8ab207f9/tt-media-server/cpp_server/src/services/llm_service.cpp#L30-L45)
*   **Worker Management:** Uses `WorkerManager` to handle sub-process lifecycles [tt-media-server/cpp_server/src/services/llm_service.cpp 38](https://github.com/tenstorrent/tt-inference-server/blob/8ab207f9/tt-media-server/cpp_server/src/services/llm_service.cpp#L38-L38)

### Workflow Venv

Isolated virtual environments managed by `uv` to ensure clean dependency separation between benchmarks, evals, and server runtimes.

*   **Implementation:**`VenvConfig` and `WorkflowVenvType` in [workflows/workflow_venvs.py 36-60](https://github.com/tenstorrent/tt-inference-server/blob/8ab207f9/workflows/workflow_venvs.py#L36-L60)
*   **Evals Integration:** Evals tasks specify which venv to use via `workflow_venv_type`[evals/eval_config.py 33](https://github.com/tenstorrent/tt-inference-server/blob/8ab207f9/evals/eval_config.py#L33-L33)

### Prefix Caching

A mechanism in the C++ server to reuse KV cache slots for recurring prompt prefixes (e.g., multi-turn conversations).

*   **Implementation:**`resolveSession` logic in [tt-media-server/cpp_server/src/api/llm_controller.cpp 59-120](https://github.com/tenstorrent/tt-inference-server/blob/8ab207f9/tt-media-server/cpp_server/src/api/llm_controller.cpp#L59-L120)
*   **Hashing:** Uses `computePrefixCachingInfo` to identify cache hits [tt-media-server/cpp_server/src/api/llm_controller.cpp 73-81](https://github.com/tenstorrent/tt-inference-server/blob/8ab207f9/tt-media-server/cpp_server/src/api/llm_controller.cpp#L73-L81)

* * *

## Data Flow Diagrams

### LLM Request Lifecycle (C++)

This diagram tracks a request from the network through the C++ service layer to the hardware workers using Shared Memory IPC.

**Sources:**[tt-media-server/cpp_server/src/services/llm_service.cpp 74-170](https://github.com/tenstorrent/tt-inference-server/blob/8ab207f9/tt-media-server/cpp_server/src/services/llm_service.cpp#L74-L170)[tt-media-server/cpp_server/src/api/llm_controller.cpp 59-182](https://github.com/tenstorrent/tt-inference-server/blob/8ab207f9/tt-media-server/cpp_server/src/api/llm_controller.cpp#L59-L182)

* * *

## Key Enums and Constants

### Model Runners

Defines the specific execution logic for different model architectures.

*   `TT_SDXL_TRACE`: Optimized Stable Diffusion XL.
*   `TT_WHISPER`: Speech-to-text runner.
*   `VLLMForge`: Integration for vLLM-based LLM serving.
*   `TT_WAN_2_2`: Video generation runner.
*   **File:**[tt-media-server/config/constants.py 87-126](https://github.com/tenstorrent/tt-inference-server/blob/8ab207f9/tt-media-server/config/constants.py#L87-L126)

### Model Services

High-level categories of model tasks used for routing and resource allocation.

*   `IMAGE`, `LLM`, `AUDIO`, `VIDEO`, `CNN`, `EMBEDDING`, `TRAINING`, `TEXT_TO_SPEECH`.
*   **File:**[tt-media-server/config/constants.py 128-137](https://github.com/tenstorrent/tt-inference-server/blob/8ab207f9/tt-media-server/config/constants.py#L128-L137)

### Device Types

Enum for Tenstorrent hardware generations and form factors used throughout the workflow system.

*   `N150`, `N300`, `T3K`, `GALAXY`, `P150`.
*   **File:**[workflows/workflow_types.py 21-25](https://github.com/tenstorrent/tt-inference-server/blob/8ab207f9/workflows/workflow_types.py#L21-L25)

* * *

## Performance and Evaluation Terms

| Term | Definition | Code Pointer |
| --- | --- | --- |
| **ISL** | Input Sequence Length (tokens). | [workflows/model_spec.py 116](https://github.com/tenstorrent/tt-inference-server/blob/8ab207f9/workflows/model_spec.py#L116-L116) |
| **OSL** | Output Sequence Length (tokens). | [workflows/model_spec.py 117](https://github.com/tenstorrent/tt-inference-server/blob/8ab207f9/workflows/model_spec.py#L117-L117) |
| **TTFT** | Time To First Token (latency in ms). | [workflows/model_spec.py 103-105](https://github.com/tenstorrent/tt-inference-server/blob/8ab207f9/workflows/model_spec.py#L103-L105) |
| **TPUT** | Throughput (tokens/second or images/second). | [workflows/model_spec.py 109-111](https://github.com/tenstorrent/tt-inference-server/blob/8ab207f9/workflows/model_spec.py#L109-L111) |
| **Acceptance Criteria** | Automated checks comparing benchmark results against theoretical targets. | [workflows/run_reports.py 33-36](https://github.com/tenstorrent/tt-inference-server/blob/8ab207f9/workflows/run_reports.py#L33-L36) |
| **LM-Eval** | Integration with the `lm-evaluation-harness` for accuracy testing. | [evals/eval_config.py 97-127](https://github.com/tenstorrent/tt-inference-server/blob/8ab207f9/evals/eval_config.py#L97-L127) |

**Sources:**[workflows/model_spec.py 82-129](https://github.com/tenstorrent/tt-inference-server/blob/8ab207f9/workflows/model_spec.py#L82-L129)[workflows/run_reports.py 33-45](https://github.com/tenstorrent/tt-inference-server/blob/8ab207f9/workflows/run_reports.py#L33-L45)[evals/eval_config.py 18-61](https://github.com/tenstorrent/tt-inference-server/blob/8ab207f9/evals/eval_config.py#L18-L61)

This wiki is featured in the [repository](https://github.com/tenstorrent/tt-inference-server/blob/main/README.md)

Dismiss
Refresh this wiki

Enter email to refresh
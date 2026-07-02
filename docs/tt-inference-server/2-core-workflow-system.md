---
project: tt-inference-server
github: https://github.com/tenstorrent/tt-inference-server
deepwiki: https://deepwiki.com/tenstorrent/tt-inference-server/2-core-workflow-system
---

# Core Workflow System

Relevant source files
*   [benchmarking/benchmark_config.py](https://github.com/tenstorrent/tt-inference-server/blob/8ab207f9/benchmarking/benchmark_config.py)
*   [benchmarking/benchmark_targets/model_performance_reference.json](https://github.com/tenstorrent/tt-inference-server/blob/8ab207f9/benchmarking/benchmark_targets/model_performance_reference.json)
*   [benchmarking/run_benchmarks.py](https://github.com/tenstorrent/tt-inference-server/blob/8ab207f9/benchmarking/run_benchmarks.py)
*   [benchmarking/summary_report.py](https://github.com/tenstorrent/tt-inference-server/blob/8ab207f9/benchmarking/summary_report.py)
*   [evals/eval_config.py](https://github.com/tenstorrent/tt-inference-server/blob/8ab207f9/evals/eval_config.py)
*   [evals/run_evals.py](https://github.com/tenstorrent/tt-inference-server/blob/8ab207f9/evals/run_evals.py)
*   [run.py](https://github.com/tenstorrent/tt-inference-server/blob/8ab207f9/run.py)
*   [tests/test_run_arguments.py](https://github.com/tenstorrent/tt-inference-server/blob/8ab207f9/tests/test_run_arguments.py)
*   [tests/test_workflows.py](https://github.com/tenstorrent/tt-inference-server/blob/8ab207f9/tests/test_workflows.py)
*   [workflows/model_spec.py](https://github.com/tenstorrent/tt-inference-server/blob/8ab207f9/workflows/model_spec.py)
*   [workflows/run_docker_server.py](https://github.com/tenstorrent/tt-inference-server/blob/8ab207f9/workflows/run_docker_server.py)
*   [workflows/run_reports.py](https://github.com/tenstorrent/tt-inference-server/blob/8ab207f9/workflows/run_reports.py)
*   [workflows/run_workflows.py](https://github.com/tenstorrent/tt-inference-server/blob/8ab207f9/workflows/run_workflows.py)
*   [workflows/setup_host.py](https://github.com/tenstorrent/tt-inference-server/blob/8ab207f9/workflows/setup_host.py)
*   [workflows/utils.py](https://github.com/tenstorrent/tt-inference-server/blob/8ab207f9/workflows/utils.py)
*   [workflows/workflow_config.py](https://github.com/tenstorrent/tt-inference-server/blob/8ab207f9/workflows/workflow_config.py)
*   [workflows/workflow_types.py](https://github.com/tenstorrent/tt-inference-server/blob/8ab207f9/workflows/workflow_types.py)
*   [workflows/workflow_venvs.py](https://github.com/tenstorrent/tt-inference-server/blob/8ab207f9/workflows/workflow_venvs.py)

The **Core Workflow System** is the host-side orchestration layer of the Tenstorrent Inference Server. It manages the entire lifecycle of an inference task—from parsing command-line arguments and resolving hardware-specific model configurations to managing Docker containers and coordinating multi-host deployments.

This system ensures that the complex requirements of Tenstorrent hardware (such as device memory management, hugepages, and mesh topologies) are abstracted away from the user, providing a unified interface via the `run.py` entrypoint.

## System Architecture

The workflow system acts as a bridge between the user's intent (e.g., "Run Llama-3.1-8B benchmarks on T3K") and the execution environment (Docker or local venv).

### Workflow Execution Flow

The following diagram illustrates how a request flows through the core system entities:

**Workflow Orchestration Logic**

Sources: [run.py 73-210](https://github.com/tenstorrent/tt-inference-server/blob/8ab207f9/run.py#L73-L210)[workflows/run_docker_server.py 124-140](https://github.com/tenstorrent/tt-inference-server/blob/8ab207f9/workflows/run_docker_server.py#L124-L140)[workflows/setup_host.py 39-68](https://github.com/tenstorrent/tt-inference-server/blob/8ab207f9/workflows/setup_host.py#L39-L68)[workflows/model_spec.py 23-24](https://github.com/tenstorrent/tt-inference-server/blob/8ab207f9/workflows/model_spec.py#L23-L24)

## Key Components

### 1. [run.py CLI and Docker Orchestration](https://deepwiki.com/tenstorrent/tt-inference-server/2.1-run.py-cli-and-docker-orchestration)

The primary entry point for all operations. It handles `RuntimeConfig` resolution, which determines which hardware is available and which implementation (vLLM, Forge, etc.) should be used. It orchestrates the Docker lifecycle, including automatic volume creation for model weights and mapping Tenstorrent device nodes (`/dev/tenstorrent/X`) into containers.

*   **Key Class:**`RuntimeConfig`[workflows/runtime_config.py 10-50](https://github.com/tenstorrent/tt-inference-server/blob/8ab207f9/workflows/runtime_config.py#L10-L50)
*   **Key Function:**`generate_docker_run_command`[workflows/run_docker_server.py 124-140](https://github.com/tenstorrent/tt-inference-server/blob/8ab207f9/workflows/run_docker_server.py#L124-L140)
*   **Key Function:**`ensure_docker_image`[workflows/run_docker_server.py 60-88](https://github.com/tenstorrent/tt-inference-server/blob/8ab207f9/workflows/run_docker_server.py#L60-L88)

### 2. [Model Specification Registry](https://deepwiki.com/tenstorrent/tt-inference-server/2.2-model-specification-registry)

A centralized registry (`MODEL_SPECS`) that defines the capabilities and requirements of every supported model. It maps high-level model names to specific implementation details, including hardware-specific overrides (e.g., `data_parallel` factors for Galaxy) and inference engine parameters.

*   **Key Class:**`ModelSpec`[workflows/model_spec.py 189-250](https://github.com/tenstorrent/tt-inference-server/blob/8ab207f9/workflows/model_spec.py#L189-L250) (approx)
*   **Registry:**`MODEL_SPECS`[workflows/model_spec.py 13](https://github.com/tenstorrent/tt-inference-server/blob/8ab207f9/workflows/model_spec.py#L13-L13)
*   **Performance Reference:**`model_performance_reference`[workflows/model_spec.py 79](https://github.com/tenstorrent/tt-inference-server/blob/8ab207f9/workflows/model_spec.py#L79-L79) loaded from [benchmarking/benchmark_targets/model_performance_reference.json 1-10](https://github.com/tenstorrent/tt-inference-server/blob/8ab207f9/benchmarking/benchmark_targets/model_performance_reference.json#L1-L10)

### 3. [Multi-Host Deployment](https://deepwiki.com/tenstorrent/tt-inference-server/2.3-multi-host-deployment)

For large-scale deployments like **Dual Galaxy** or **Quad Galaxy**, the system uses a `MultiHostOrchestrator`. This component manages SSH-based communication across nodes, coordinates distributed execution, and ensures that all hosts in a cluster are synchronized during the inference lifecycle.

*   **Key Class:**`MultiHostOrchestrator`[workflows/multihost_orchestrator.py 18-23](https://github.com/tenstorrent/tt-inference-server/blob/8ab207f9/workflows/multihost_orchestrator.py#L18-L23)
*   **Hardware Support:**`DUAL_GALAXY` (2 hosts), `QUAD_GALAXY` (4 hosts) [workflows/workflow_types.py 176-196](https://github.com/tenstorrent/tt-inference-server/blob/8ab207f9/workflows/workflow_types.py#L176-L196)

### 4. [Virtual Environments and Dependency Management](https://deepwiki.com/tenstorrent/tt-inference-server/2.4-virtual-environments-and-dependency-management)

The workflow system manages isolated Python environments using `uv`. This is critical for running evaluations (e.g., `lm-eval`) and benchmarks that require specific versions of libraries without polluting the host or server environment.

*   **Key Class:**`VenvConfig`[workflows/workflow_venvs.py 36-65](https://github.com/tenstorrent/tt-inference-server/blob/8ab207f9/workflows/workflow_venvs.py#L36-L65)
*   **Setup Functions:**`setup_evals_common`[workflows/workflow_venvs.py 88-102](https://github.com/tenstorrent/tt-inference-server/blob/8ab207f9/workflows/workflow_venvs.py#L88-L102) and `setup_evals_meta`[workflows/workflow_venvs.py 122-187](https://github.com/tenstorrent/tt-inference-server/blob/8ab207f9/workflows/workflow_venvs.py#L122-L187)
*   **Venv Types:**`EVALS_COMMON`, `EVALS_VISION`, `BENCHMARKS_VLLM`[workflows/workflow_types.py 26-50](https://github.com/tenstorrent/tt-inference-server/blob/8ab207f9/workflows/workflow_types.py#L26-L50)

## Core Data Entities

| Entity | Code Symbol | Purpose |
| --- | --- | --- |
| **Runtime Configuration** | `RuntimeConfig` | Captures CLI state (port, device_id, interactive mode). |
| **Model Definition** | `ModelSpec` | Static definition of a model's requirements and engine. |
| **Host Setup** | `SetupConfig` | Resolved paths for host volumes, HF cache, and weights. |
| **Workflow Type** | `WorkflowType` | Enum for `BENCHMARKS`, `EVALS`, `STRESS_TESTS`, etc. |
| **Device Type** | `DeviceTypes` | Enum for hardware like `N150`, `T3K`, `GALAXY`. |

Sources: [workflows/runtime_config.py 10-50](https://github.com/tenstorrent/tt-inference-server/blob/8ab207f9/workflows/runtime_config.py#L10-L50)[workflows/model_spec.py 189-250](https://github.com/tenstorrent/tt-inference-server/blob/8ab207f9/workflows/model_spec.py#L189-L250)[workflows/setup_host.py 39-68](https://github.com/tenstorrent/tt-inference-server/blob/8ab207f9/workflows/setup_host.py#L39-L68)[workflows/workflow_types.py 8-16](https://github.com/tenstorrent/tt-inference-server/blob/8ab207f9/workflows/workflow_types.py#L8-L16)[workflows/workflow_types.py 60-78](https://github.com/tenstorrent/tt-inference-server/blob/8ab207f9/workflows/workflow_types.py#L60-L78)

## Implementation Mapping

**Code Entity Association**

Sources: [run.py 73-210](https://github.com/tenstorrent/tt-inference-server/blob/8ab207f9/run.py#L73-L210)[workflows/model_spec.py 189-250](https://github.com/tenstorrent/tt-inference-server/blob/8ab207f9/workflows/model_spec.py#L189-L250)[workflows/runtime_config.py 10-50](https://github.com/tenstorrent/tt-inference-server/blob/8ab207f9/workflows/runtime_config.py#L10-L50)[workflows/setup_host.py 39-68](https://github.com/tenstorrent/tt-inference-server/blob/8ab207f9/workflows/setup_host.py#L39-L68)[workflows/workflow_venvs.py 36-65](https://github.com/tenstorrent/tt-inference-server/blob/8ab207f9/workflows/workflow_venvs.py#L36-L65)

* * *

For detailed information on specific subsystems, refer to the child pages:

*   [run.py CLI and Docker Orchestration](https://deepwiki.com/tenstorrent/tt-inference-server/2.1-run.py-cli-and-docker-orchestration)
*   [Model Specification Registry](https://deepwiki.com/tenstorrent/tt-inference-server/2.2-model-specification-registry)
*   [Multi-Host Deployment](https://deepwiki.com/tenstorrent/tt-inference-server/2.3-multi-host-deployment)
*   [Virtual Environments and Dependency Management](https://deepwiki.com/tenstorrent/tt-inference-server/2.4-virtual-environments-and-dependency-management)

This wiki is featured in the [repository](https://github.com/tenstorrent/tt-inference-server/blob/main/README.md)

Dismiss
Refresh this wiki

Enter email to refresh
---
project: tt-inference-server
github: https://github.com/tenstorrent/tt-inference-server
deepwiki: https://deepwiki.com/tenstorrent/tt-inference-server
---

# Overview

 Relevant source files

* [.github/workflows/spdx-checker.yml](https://github.com/tenstorrent/tt-inference-server/blob/8ab207f9/.github/workflows/spdx-checker.yml)
* [LICENSE](https://github.com/tenstorrent/tt-inference-server/blob/8ab207f9/LICENSE)
* [README.md](https://github.com/tenstorrent/tt-inference-server/blob/8ab207f9/README.md?plain=1)
* [VERSION](https://github.com/tenstorrent/tt-inference-server/blob/8ab207f9/VERSION)
* [docs/README.md](https://github.com/tenstorrent/tt-inference-server/blob/8ab207f9/docs/README.md?plain=1)
* [docs/multihost\_deployment.md](https://github.com/tenstorrent/tt-inference-server/blob/8ab207f9/docs/multihost_deployment.md?plain=1)
* [run.py](https://github.com/tenstorrent/tt-inference-server/blob/8ab207f9/run.py)
* [scripts/add\_spdx\_header.py](https://github.com/tenstorrent/tt-inference-server/blob/8ab207f9/scripts/add_spdx_header.py)
* [scripts/build\_docker\_images.py](https://github.com/tenstorrent/tt-inference-server/blob/8ab207f9/scripts/build_docker_images.py)
* [scripts/release/update\_model\_spec.py](https://github.com/tenstorrent/tt-inference-server/blob/8ab207f9/scripts/release/update_model_spec.py)
* [tests/test\_build\_docker\_images.py](https://github.com/tenstorrent/tt-inference-server/blob/8ab207f9/tests/test_build_docker_images.py)
* [tests/test\_run\_arguments.py](https://github.com/tenstorrent/tt-inference-server/blob/8ab207f9/tests/test_run_arguments.py)
* [tests/test\_workflows.py](https://github.com/tenstorrent/tt-inference-server/blob/8ab207f9/tests/test_workflows.py)
* [tt-media-server/README.md](https://github.com/tenstorrent/tt-inference-server/blob/8ab207f9/tt-media-server/README.md?plain=1)
* [tt-media-server/config/constants.py](https://github.com/tenstorrent/tt-inference-server/blob/8ab207f9/tt-media-server/config/constants.py)
* [tt-media-server/config/settings.py](https://github.com/tenstorrent/tt-inference-server/blob/8ab207f9/tt-media-server/config/settings.py)
* [tt-media-server/tests/conftest.py](https://github.com/tenstorrent/tt-inference-server/blob/8ab207f9/tt-media-server/tests/conftest.py)
* [tt-media-server/tt\_model\_runners/runner\_fabric.py](https://github.com/tenstorrent/tt-inference-server/blob/8ab207f9/tt-media-server/tt_model_runners/runner_fabric.py)
* [tt-media-server/tt\_model\_runners/yolov4\_runner.py](https://github.com/tenstorrent/tt-inference-server/blob/8ab207f9/tt-media-server/tt_model_runners/yolov4_runner.py)
* [vllm-tt-metal/multihost\_entrypoint.sh](https://github.com/tenstorrent/tt-inference-server/blob/8ab207f9/vllm-tt-metal/multihost_entrypoint.sh)
* [vllm-tt-metal/vllm.tt-metal.src.multihost.Dockerfile](https://github.com/tenstorrent/tt-inference-server/blob/8ab207f9/vllm-tt-metal/vllm.tt-metal.src.multihost.Dockerfile)
* [workflows/run\_docker\_server.py](https://github.com/tenstorrent/tt-inference-server/blob/8ab207f9/workflows/run_docker_server.py)
* [workflows/run\_workflows.py](https://github.com/tenstorrent/tt-inference-server/blob/8ab207f9/workflows/run_workflows.py)
* [workflows/setup\_host.py](https://github.com/tenstorrent/tt-inference-server/blob/8ab207f9/workflows/setup_host.py)
* [workflows/utils.py](https://github.com/tenstorrent/tt-inference-server/blob/8ab207f9/workflows/utils.py)
* [workflows/workflow\_config.py](https://github.com/tenstorrent/tt-inference-server/blob/8ab207f9/workflows/workflow_config.py)

`tt-inference-server` is the primary deployment and testing framework for serving model inference on Tenstorrent hardware [README.md3-6](https://github.com/tenstorrent/tt-inference-server/blob/8ab207f9/README.md?plain=1#L3-L6) It provides a unified interface for various model types, including Large Language Models (LLMs), Vision-Language Models (VLMs), and generative media models (Image, Video, Audio, CNN) [README.md14-26](https://github.com/tenstorrent/tt-inference-server/blob/8ab207f9/README.md?plain=1#L14-L26)

The project orchestrates the lifecycle of inference services—from hardware discovery and Docker container management to automated performance benchmarking and accuracy evaluations [README.md43-51](https://github.com/tenstorrent/tt-inference-server/blob/8ab207f9/README.md?plain=1#L43-L51)

## System Architecture

The server architecture is divided into a host-side orchestration layer (`workflows`) and several pluggable inference backends. The primary entry point for all operations is the `run.py` CLI [run.py1-12](https://github.com/tenstorrent/tt-inference-server/blob/8ab207f9/run.py#L1-L12)

### High-Level Workflow

**Sources:** [run.py31-39](https://github.com/tenstorrent/tt-inference-server/blob/8ab207f9/run.py#L31-L39) [workflows/run\_docker\_server.py124-145](https://github.com/tenstorrent/tt-inference-server/blob/8ab207f9/workflows/run_docker_server.py#L124-L145) [README.md43-51](https://github.com/tenstorrent/tt-inference-server/blob/8ab207f9/README.md?plain=1#L43-L51)

## Major Subsystems

### 1. Core Workflow System

The core system manages the "outer loop" of inference. It handles hardware detection via `infer_default_device` [run.py17](https://github.com/tenstorrent/tt-inference-server/blob/8ab207f9/run.py#L17-L17) resolves model requirements through a template-based registry `MODEL_SPECS` [run.py20](https://github.com/tenstorrent/tt-inference-server/blob/8ab207f9/run.py#L20-L20) and manages isolated Python environments using `bootstrap_uv` [run.py16](https://github.com/tenstorrent/tt-inference-server/blob/8ab207f9/run.py#L16-L16) It supports both local execution and containerized deployments [run.py126-133](https://github.com/tenstorrent/tt-inference-server/blob/8ab207f9/run.py#L126-L133)

* **For details, see [Core Workflow System](/tenstorrent/tt-inference-server/2-core-workflow-system)**.

### 2. tt-media-server (Python & C++)

For non-LLM modalities (Image, Video, Audio, CNN), the project uses `tt-media-server`. It provides a FastAPI-based frontend and a multi-process `Scheduler` to manage `device_worker` lifecycles [tt-media-server/README.md20-31](https://github.com/tenstorrent/tt-inference-server/blob/8ab207f9/tt-media-server/README.md?plain=1#L20-L31) The server configuration is managed via a Pydantic-based `Settings` class that handles environment overrides for hardware and model parameters [tt-media-server/config/settings.py32-114](https://github.com/tenstorrent/tt-inference-server/blob/8ab207f9/tt-media-server/config/settings.py#L32-L114) It uses a `runner_fabric` to instantiate specific model runners like `TTSDXLGenerateRunnerTrace` or `TTWhisperRunner` [tt-media-server/tt\_model\_runners/runner\_fabric.py10-51](https://github.com/tenstorrent/tt-inference-server/blob/8ab207f9/tt-media-server/tt_model_runners/runner_fabric.py#L10-L51)

* **For details, see [tt-media-server (Python)](/tenstorrent/tt-inference-server/3-tt-media-server-(python)) and [tt-media-server C++ Backend](/tenstorrent/tt-inference-server/4-tt-media-server-c++-backend)**.

### 3. vLLM tt-metal Integration

LLM serving is primarily handled through a specialized integration of vLLM optimized for Tenstorrent's `tt-metal` backend. This supports models like Llama 3.1 and Qwen, often deployed via `vllm-forge` runners [tt-media-server/README.md162-184](https://github.com/tenstorrent/tt-inference-server/blob/8ab207f9/tt-media-server/README.md?plain=1#L162-L184)

* **For details, see [Inference Engine Plugins](/tenstorrent/tt-inference-server/5-inference-engine-plugins)**.

### 4. Benchmarking and Evaluation

The framework includes built-in pipelines for performance profiling (`run_benchmarks.py`) and accuracy scoring (`run_evals.py`). These tools leverage the `RuntimeConfig` to ensure consistency between server deployment and testing [run.py37-38](https://github.com/tenstorrent/tt-inference-server/blob/8ab207f9/run.py#L37-L38) The system uses `WorkflowSetup` to manage dependencies and execution for these tasks [workflows/run\_workflows.py29-60](https://github.com/tenstorrent/tt-inference-server/blob/8ab207f9/workflows/run_workflows.py#L29-L60)

* **For details, see [Benchmarking and Evaluation](/tenstorrent/tt-inference-server/6-benchmarking-and-evaluation)**.

## Code Entity Map: From CLI to Execution

This diagram maps the natural language concepts of "starting a server" to the specific classes and functions in the codebase.

**Sources:** [run.py73-210](https://github.com/tenstorrent/tt-inference-server/blob/8ab207f9/run.py#L73-L210) [workflows/run\_docker\_server.py124-145](https://github.com/tenstorrent/tt-inference-server/blob/8ab207f9/workflows/run_docker_server.py#L124-L145) [workflows/setup\_host.py38-68](https://github.com/tenstorrent/tt-inference-server/blob/8ab207f9/workflows/setup_host.py#L38-L68) [tt-media-server/config/settings.py116-145](https://github.com/tenstorrent/tt-inference-server/blob/8ab207f9/tt-media-server/config/settings.py#L116-L145) [tt-media-server/tt\_model\_runners/runner\_fabric.py136-148](https://github.com/tenstorrent/tt-inference-server/blob/8ab207f9/tt-media-server/tt_model_runners/runner_fabric.py#L136-L148)

## Supported Hardware and Models

The server supports a range of Tenstorrent hardware configurations, from single-chip cards to multi-host Galaxy systems [README.md27-41](https://github.com/tenstorrent/tt-inference-server/blob/8ab207f9/README.md?plain=1#L27-L41)

| Hardware Class | Device Types | Common Models |
| --- | --- | --- |
| **Wormhole** | `n150`, `n300`, `t3k`, `galaxy` | Llama 3.x, SDXL, Whisper, Flux.1, Mistral |
| **Blackhole** | `p100`, `p150`, `loudbox` | Resnet (Forge), Wan2.2, Qwen |

The `SupportedModels` enum defines the HuggingFace paths for integrated models [tt-media-server/config/constants.py8-40](https://github.com/tenstorrent/tt-inference-server/blob/8ab207f9/tt-media-server/config/constants.py#L8-L40)

For a complete catalog of supported model-hardware pairs, see **[Supported Models and Hardware](/tenstorrent/tt-inference-server/1.2-supported-models-and-hardware)**.

## Getting Started

To run your first model (e.g., Llama 3.2-1B) with automated setup:

[run.py90-133](https://github.com/tenstorrent/tt-inference-server/blob/8ab207f9/run.py#L90-L133)

For step-by-step installation and prerequisites, see **[Getting Started](/tenstorrent/tt-inference-server/1.1-getting-started)**.

## Development and Release

The project uses a versioned release strategy (e.g., `0.13.0`) [VERSION1](https://github.com/tenstorrent/tt-inference-server/blob/8ab207f9/VERSION#L1-L1) Releases are driven by CI results where `update_model_spec.py` updates the `MODEL_SPECS` registry with validated `tt_metal_commit` and `vllm_commit` SHAs to ensure reproducibility [scripts/release/update\_model\_spec.py9-26](https://github.com/tenstorrent/tt-inference-server/blob/8ab207f9/scripts/release/update_model_spec.py#L9-L26)

Compliance is maintained via tools like `add_spdx_header.py` which enforces license headers across the repository [scripts/add\_spdx\_header.py1-10](https://github.com/tenstorrent/tt-inference-server/blob/8ab207f9/scripts/add_spdx_header.py#L1-L10)

* **For details, see [CI/CD and Release Management](/tenstorrent/tt-inference-server/8-cicd-and-release-management)**.
* **To add a new model, see [Adding a New Model](/tenstorrent/tt-inference-server/9-adding-a-new-model)**.

---

**Sources:** [README.md1-69](https://github.com/tenstorrent/tt-inference-server/blob/8ab207f9/README.md?plain=1#L1-L69) [run.py1-210](https://github.com/tenstorrent/tt-inference-server/blob/8ab207f9/run.py#L1-L210) [workflows/run\_docker\_server.py1-145](https://github.com/tenstorrent/tt-inference-server/blob/8ab207f9/workflows/run_docker_server.py#L1-L145) [tt-media-server/config/settings.py1-188](https://github.com/tenstorrent/tt-inference-server/blob/8ab207f9/tt-media-server/config/settings.py#L1-L188) [tt-media-server/config/constants.py8-193](https://github.com/tenstorrent/tt-inference-server/blob/8ab207f9/tt-media-server/config/constants.py#L8-L193) [tt-media-server/tt\_model\_runners/runner\_fabric.py1-133](https://github.com/tenstorrent/tt-inference-server/blob/8ab207f9/tt-media-server/tt_model_runners/runner_fabric.py#L1-L133) [workflows/run\_workflows.py1-160](https://github.com/tenstorrent/tt-inference-server/blob/8ab207f9/workflows/run_workflows.py#L1-L160) [VERSION1](https://github.com/tenstorrent/tt-inference-server/blob/8ab207f9/VERSION#L1-L1)

Refresh this wiki
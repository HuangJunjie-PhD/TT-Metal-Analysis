---
project: tt-inference-server
github: https://github.com/tenstorrent/tt-inference-server
deepwiki: https://deepwiki.com/tenstorrent/tt-inference-server/5-inference-engine-plugins
---

# Inference Engine Plugins

Relevant source files
*   [tt-sglang-plugin/.gitignore](https://github.com/tenstorrent/tt-inference-server/blob/8ab207f9/tt-sglang-plugin/.gitignore)
*   [tt-sglang-plugin/README.md](https://github.com/tenstorrent/tt-inference-server/blob/8ab207f9/tt-sglang-plugin/README.md?plain=1)
*   [tt-sglang-plugin/setup.py](https://github.com/tenstorrent/tt-inference-server/blob/8ab207f9/tt-sglang-plugin/setup.py)
*   [tt-sglang-plugin/setup_sglang_tt.sh](https://github.com/tenstorrent/tt-inference-server/blob/8ab207f9/tt-sglang-plugin/setup_sglang_tt.sh)
*   [tt-sglang-plugin/sglang_tt_plugin.pth](https://github.com/tenstorrent/tt-inference-server/blob/8ab207f9/tt-sglang-plugin/sglang_tt_plugin.pth)
*   [tt-sglang-plugin/sglang_tt_plugin/__init__.py](https://github.com/tenstorrent/tt-inference-server/blob/8ab207f9/tt-sglang-plugin/sglang_tt_plugin/__init__.py)
*   [tt-sglang-plugin/sglang_tt_plugin/models/tt_llm.py](https://github.com/tenstorrent/tt-inference-server/blob/8ab207f9/tt-sglang-plugin/sglang_tt_plugin/models/tt_llm.py)
*   [tt-sglang-plugin/sglang_tt_plugin/patching/patching_model_registry.py](https://github.com/tenstorrent/tt-inference-server/blob/8ab207f9/tt-sglang-plugin/sglang_tt_plugin/patching/patching_model_registry.py)
*   [tt-sglang-plugin/sglang_tt_plugin/scripts/launch_tt_server.py](https://github.com/tenstorrent/tt-inference-server/blob/8ab207f9/tt-sglang-plugin/sglang_tt_plugin/scripts/launch_tt_server.py)
*   [tt-sglang-plugin/sglang_tt_plugin/utils/tt_utils.py](https://github.com/tenstorrent/tt-inference-server/blob/8ab207f9/tt-sglang-plugin/sglang_tt_plugin/utils/tt_utils.py)
*   [tt-sglang-plugin/sglang_tt_plugin/worker_setup/worker_setup.py](https://github.com/tenstorrent/tt-inference-server/blob/8ab207f9/tt-sglang-plugin/sglang_tt_plugin/worker_setup/worker_setup.py)
*   [tt-vllm-plugin/.gitignore](https://github.com/tenstorrent/tt-inference-server/blob/8ab207f9/tt-vllm-plugin/.gitignore)
*   [tt-vllm-plugin/CHANGELOG.md](https://github.com/tenstorrent/tt-inference-server/blob/8ab207f9/tt-vllm-plugin/CHANGELOG.md?plain=1)
*   [tt-vllm-plugin/README.md](https://github.com/tenstorrent/tt-inference-server/blob/8ab207f9/tt-vllm-plugin/README.md?plain=1)
*   [tt-vllm-plugin/examples/bge_large_en_v1_5.py](https://github.com/tenstorrent/tt-inference-server/blob/8ab207f9/tt-vllm-plugin/examples/bge_large_en_v1_5.py)
*   [tt-vllm-plugin/examples/llama_3_1_8b_instruct.py](https://github.com/tenstorrent/tt-inference-server/blob/8ab207f9/tt-vllm-plugin/examples/llama_3_1_8b_instruct.py)
*   [tt-vllm-plugin/examples/ttnn_test.py](https://github.com/tenstorrent/tt-inference-server/blob/8ab207f9/tt-vllm-plugin/examples/ttnn_test.py)
*   [tt-vllm-plugin/online_benchmark.sh](https://github.com/tenstorrent/tt-inference-server/blob/8ab207f9/tt-vllm-plugin/online_benchmark.sh)
*   [tt-vllm-plugin/pyproject.toml](https://github.com/tenstorrent/tt-inference-server/blob/8ab207f9/tt-vllm-plugin/pyproject.toml)
*   [tt-vllm-plugin/serve.sh](https://github.com/tenstorrent/tt-inference-server/blob/8ab207f9/tt-vllm-plugin/serve.sh)
*   [tt-vllm-plugin/setup.py](https://github.com/tenstorrent/tt-inference-server/blob/8ab207f9/tt-vllm-plugin/setup.py)

The `tt-inference-server` codebase supports multiple inference engine integrations through a pluggable architecture. These plugins allow popular LLM serving frameworks like **vLLM** and **SGLang** to utilize Tenstorrent hardware acceleration via `tt-metal`. This section provides an overview of the available plugins and their high-level integration strategies.

## Plugin Ecosystem Overview

The inference engine plugins bridge the gap between high-level Python serving frameworks and the low-level Tenstorrent hardware stack. They typically involve:

1.   **Model Registration**: Patching the framework's model registry to point HuggingFace architectures to TT-specific implementations [tt-vllm-plugin/pyproject.toml 47-48](https://github.com/tenstorrent/tt-inference-server/blob/8ab207f9/tt-vllm-plugin/pyproject.toml#L47-L48)[tt-sglang-plugin/sglang_tt_plugin/patching/patching_model_registry.py 15-38](https://github.com/tenstorrent/tt-inference-server/blob/8ab207f9/tt-sglang-plugin/sglang_tt_plugin/patching/patching_model_registry.py#L15-L38)
2.   **Platform Integration**: Implementing custom "Platform" or "Device" classes to handle hardware initialization and memory management [tt-vllm-plugin/pyproject.toml 44-45](https://github.com/tenstorrent/tt-inference-server/blob/8ab207f9/tt-vllm-plugin/pyproject.toml#L44-L45)[tt-sglang-plugin/sglang_tt_plugin/utils/tt_utils.py 14-47](https://github.com/tenstorrent/tt-inference-server/blob/8ab207f9/tt-sglang-plugin/sglang_tt_plugin/utils/tt_utils.py#L14-L47)
3.   **Worker Management**: Handling multi-device and multi-process coordination, specifically for Data Parallel (DP) and Tensor Parallel (TP) configurations [tt-sglang-plugin/sglang_tt_plugin/worker_setup/worker_setup.py 11-15](https://github.com/tenstorrent/tt-inference-server/blob/8ab207f9/tt-sglang-plugin/sglang_tt_plugin/worker_setup/worker_setup.py#L11-L15)

### Plugin Architecture Relationship

The following diagram illustrates how these plugins interface with the external frameworks and the internal Tenstorrent stack.

**Inference Plugin Integration Map**

Sources: [tt-vllm-plugin/pyproject.toml 44-49](https://github.com/tenstorrent/tt-inference-server/blob/8ab207f9/tt-vllm-plugin/pyproject.toml#L44-L49)[tt-sglang-plugin/sglang_tt_plugin/patching/patching_model_registry.py 15-38](https://github.com/tenstorrent/tt-inference-server/blob/8ab207f9/tt-sglang-plugin/sglang_tt_plugin/patching/patching_model_registry.py#L15-L38)[tt-sglang-plugin/sglang_tt_plugin/models/tt_llm.py 80-81](https://github.com/tenstorrent/tt-inference-server/blob/8ab207f9/tt-sglang-plugin/sglang_tt_plugin/models/tt_llm.py#L80-L81)[tt-sglang-plugin/sglang_tt_plugin/utils/tt_utils.py 37-47](https://github.com/tenstorrent/tt-inference-server/blob/8ab207f9/tt-sglang-plugin/sglang_tt_plugin/utils/tt_utils.py#L37-L47)

* * *

## vLLM tt-metal Integration

The `tt-vllm-plugin` is a specialized integration for the vLLM project. It leverages vLLM's out-of-tree (OOT) plugin system to register Tenstorrent as a supported hardware platform.

### Key Components

*   **Platform Registration**: Registers the `tt` platform via `vllm.platform_plugins` entry point to allow vLLM to interact with Tenstorrent hardware [tt-vllm-plugin/pyproject.toml 44-45](https://github.com/tenstorrent/tt-inference-server/blob/8ab207f9/tt-vllm-plugin/pyproject.toml#L44-L45)
*   **Model Registry**: Maps standard architectures to their respective TT-optimized implementations via the `tt_model_registry` entry point [tt-vllm-plugin/pyproject.toml 47-48](https://github.com/tenstorrent/tt-inference-server/blob/8ab207f9/tt-vllm-plugin/pyproject.toml#L47-L48)
*   **Package Distribution**: Uses a custom `BdistWheel` class in `setup.py` to ensure the resulting wheel is marked as non-pure and platform-specific for proper native binary handling [tt-vllm-plugin/setup.py 15-43](https://github.com/tenstorrent/tt-inference-server/blob/8ab207f9/tt-vllm-plugin/setup.py#L15-L43)
*   **Dependency Management**: Strictly pins versions of `vllm`, `torch`, and `transformers` to ensure compatibility with specific `tt-metal` releases [tt-vllm-plugin/pyproject.toml 27-35](https://github.com/tenstorrent/tt-inference-server/blob/8ab207f9/tt-vllm-plugin/pyproject.toml#L27-L35)

For details, see [vLLM tt-metal Integration](https://deepwiki.com/tenstorrent/tt-inference-server/5.1-vllm-tt-metal-integration).

* * *

## SGLang TT-Metal Plugin

The `tt-sglang-plugin` integrates Tenstorrent hardware into the SGLang runtime. This plugin uses runtime patching of the SGLang `ModelRegistry` and provides a specialized setup environment.

### Key Components

*   **Runtime Patching**: The `register_tt_models` function (called upon package import) updates SGLang's internal registry with TT-specific model classes like `TTLlamaForCausalLM` and `TTQwenForCausalLM`[tt-sglang-plugin/sglang_tt_plugin/patching/patching_model_registry.py 15-38](https://github.com/tenstorrent/tt-inference-server/blob/8ab207f9/tt-sglang-plugin/sglang_tt_plugin/patching/patching_model_registry.py#L15-L38)[tt-sglang-plugin/sglang_tt_plugin/__init__.py 10](https://github.com/tenstorrent/tt-inference-server/blob/8ab207f9/tt-sglang-plugin/sglang_tt_plugin/__init__.py#L10-L10)
*   **Worker Isolation**: Uses `setup_worker_from_process_title` to extract Data Parallel (DP) ranks from SGLang's process titles, ensuring each worker process targets the correct hardware devices via `TT_VISIBLE_DEVICES` and maintains isolated caches in `~/.cache/tt-metal-cache-worker{rank}`[tt-sglang-plugin/sglang_tt_plugin/worker_setup/worker_setup.py 11-76](https://github.com/tenstorrent/tt-inference-server/blob/8ab207f9/tt-sglang-plugin/sglang_tt_plugin/worker_setup/worker_setup.py#L11-L76)
*   **Forward Dispatch**: The `TTModels` class (inheriting from `nn.Module`) manages the transition between SGLang's `ForwardBatch` and the TT-Metal `prefill_forward` and `decode_forward` methods, including handling of padded tokens for decode batches [tt-sglang-plugin/sglang_tt_plugin/models/tt_llm.py 83-150](https://github.com/tenstorrent/tt-inference-server/blob/8ab207f9/tt-sglang-plugin/sglang_tt_plugin/models/tt_llm.py#L83-L150)
*   **Device Orchestration**: `BaseMetalDeviceRunner` handles the lifecycle of the `mesh_device`, including configuration of `FabricConfig` for multi-device setups and `DispatchCoreConfig` for architecture-specific (Blackhole vs Wormhole) optimizations [tt-sglang-plugin/sglang_tt_plugin/utils/tt_utils.py 103-172](https://github.com/tenstorrent/tt-inference-server/blob/8ab207f9/tt-sglang-plugin/sglang_tt_plugin/utils/tt_utils.py#L103-L172)[tt-sglang-plugin/sglang_tt_plugin/utils/tt_utils.py 69-101](https://github.com/tenstorrent/tt-inference-server/blob/8ab207f9/tt-sglang-plugin/sglang_tt_plugin/utils/tt_utils.py#L69-L101)

For details, see [SGLang TT-Metal Plugin](https://deepwiki.com/tenstorrent/tt-inference-server/5.2-sglang-tt-metal-plugin).

* * *

## Plugin Implementation Details

The following table summarizes the technical differences between the two primary plugins:

| Feature | vLLM Plugin | SGLang Plugin |
| --- | --- | --- |
| **Integration Method** | Entry Points / OOT Plugin [tt-vllm-plugin/pyproject.toml 44-49](https://github.com/tenstorrent/tt-inference-server/blob/8ab207f9/tt-vllm-plugin/pyproject.toml#L44-L49) | Runtime Patching (`ModelRegistry.update`) [tt-sglang-plugin/sglang_tt_plugin/patching/patching_model_registry.py 38](https://github.com/tenstorrent/tt-inference-server/blob/8ab207f9/tt-sglang-plugin/sglang_tt_plugin/patching/patching_model_registry.py#L38-L38) |
| **Primary Device Class** | `TTPlatform` | `BaseMetalDeviceRunner`[tt-sglang-plugin/sglang_tt_plugin/utils/tt_utils.py 14](https://github.com/tenstorrent/tt-inference-server/blob/8ab207f9/tt-sglang-plugin/sglang_tt_plugin/utils/tt_utils.py#L14-L14) |
| **Worker Setup** | vLLM native worker lifecycle | `setup_worker_from_process_title`[tt-sglang-plugin/sglang_tt_plugin/worker_setup/worker_setup.py 11](https://github.com/tenstorrent/tt-inference-server/blob/8ab207f9/tt-sglang-plugin/sglang_tt_plugin/worker_setup/worker_setup.py#L11-L11) |
| **Launch Command** | `vllm serve` | `sglang-tt-server`[tt-sglang-plugin/setup.py 35](https://github.com/tenstorrent/tt-inference-server/blob/8ab207f9/tt-sglang-plugin/setup.py#L35-L35) |
| **Environment Setup** | Docker-based | `setup_sglang_tt.sh` script [tt-sglang-plugin/setup_sglang_tt.sh 5-175](https://github.com/tenstorrent/tt-inference-server/blob/8ab207f9/tt-sglang-plugin/setup_sglang_tt.sh#L5-L175) |
| **KV Cache Logic** | vLLM Block Manager | `allocate_on_device`[tt-sglang-plugin/sglang_tt_plugin/models/tt_llm.py 152](https://github.com/tenstorrent/tt-inference-server/blob/8ab207f9/tt-sglang-plugin/sglang_tt_plugin/models/tt_llm.py#L152-L152) |

### Code Entity Space Bridge

The following diagram maps high-level plugin concepts to the specific code entities that implement them.

**Plugin Code Entity Mapping**

Sources: [tt-vllm-plugin/pyproject.toml 44-49](https://github.com/tenstorrent/tt-inference-server/blob/8ab207f9/tt-vllm-plugin/pyproject.toml#L44-L49)[tt-sglang-plugin/sglang_tt_plugin/patching/patching_model_registry.py 15-38](https://github.com/tenstorrent/tt-inference-server/blob/8ab207f9/tt-sglang-plugin/sglang_tt_plugin/patching/patching_model_registry.py#L15-L38)[tt-sglang-plugin/sglang_tt_plugin/models/tt_llm.py 21-32](https://github.com/tenstorrent/tt-inference-server/blob/8ab207f9/tt-sglang-plugin/sglang_tt_plugin/models/tt_llm.py#L21-L32)[tt-sglang-plugin/setup.py 33-43](https://github.com/tenstorrent/tt-inference-server/blob/8ab207f9/tt-sglang-plugin/setup.py#L33-L43)

Dismiss
Refresh this wiki

Enter email to refresh
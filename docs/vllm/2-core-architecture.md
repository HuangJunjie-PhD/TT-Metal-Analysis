---
project: vllm
github: https://github.com/tenstorrent/vllm
deepwiki: https://deepwiki.com/tenstorrent/vllm/2-core-architecture
---

# Core Architecture

Relevant source files
*   [.buildkite/test-pipeline.yaml](https://github.com/tenstorrent/vllm/blob/cd9e5b83/.buildkite/test-pipeline.yaml)
*   [docs/usage/metrics.md](https://github.com/tenstorrent/vllm/blob/cd9e5b83/docs/usage/metrics.md?plain=1)
*   [examples/online_serving/prometheus_grafana/grafana.json](https://github.com/tenstorrent/vllm/blob/cd9e5b83/examples/online_serving/prometheus_grafana/grafana.json)
*   [tests/config/test_config.yaml](https://github.com/tenstorrent/vllm/blob/cd9e5b83/tests/config/test_config.yaml)
*   [tests/config/test_config_with_model.yaml](https://github.com/tenstorrent/vllm/blob/cd9e5b83/tests/config/test_config_with_model.yaml)
*   [tests/conftest.py](https://github.com/tenstorrent/vllm/blob/cd9e5b83/tests/conftest.py)
*   [tests/entrypoints/openai/test_metrics.py](https://github.com/tenstorrent/vllm/blob/cd9e5b83/tests/entrypoints/openai/test_metrics.py)
*   [tests/v1/core/test_async_scheduler.py](https://github.com/tenstorrent/vllm/blob/cd9e5b83/tests/v1/core/test_async_scheduler.py)
*   [tests/v1/core/test_kv_cache_utils.py](https://github.com/tenstorrent/vllm/blob/cd9e5b83/tests/v1/core/test_kv_cache_utils.py)
*   [tests/v1/core/test_prefix_caching.py](https://github.com/tenstorrent/vllm/blob/cd9e5b83/tests/v1/core/test_prefix_caching.py)
*   [tests/v1/core/test_scheduler.py](https://github.com/tenstorrent/vllm/blob/cd9e5b83/tests/v1/core/test_scheduler.py)
*   [tests/v1/core/test_single_type_kv_cache_manager.py](https://github.com/tenstorrent/vllm/blob/cd9e5b83/tests/v1/core/test_single_type_kv_cache_manager.py)
*   [tests/v1/engine/test_engine_core.py](https://github.com/tenstorrent/vllm/blob/cd9e5b83/tests/v1/engine/test_engine_core.py)
*   [tests/v1/engine/test_engine_core_client.py](https://github.com/tenstorrent/vllm/blob/cd9e5b83/tests/v1/engine/test_engine_core_client.py)
*   [vllm/engine/arg_utils.py](https://github.com/tenstorrent/vllm/blob/cd9e5b83/vllm/engine/arg_utils.py)
*   [vllm/engine/async_llm_engine.py](https://github.com/tenstorrent/vllm/blob/cd9e5b83/vllm/engine/async_llm_engine.py)
*   [vllm/engine/llm_engine.py](https://github.com/tenstorrent/vllm/blob/cd9e5b83/vllm/engine/llm_engine.py)
*   [vllm/engine/metrics.py](https://github.com/tenstorrent/vllm/blob/cd9e5b83/vllm/engine/metrics.py)
*   [vllm/engine/metrics_types.py](https://github.com/tenstorrent/vllm/blob/cd9e5b83/vllm/engine/metrics_types.py)
*   [vllm/entrypoints/llm.py](https://github.com/tenstorrent/vllm/blob/cd9e5b83/vllm/entrypoints/llm.py)
*   [vllm/envs.py](https://github.com/tenstorrent/vllm/blob/cd9e5b83/vllm/envs.py)
*   [vllm/executor/uniproc_executor.py](https://github.com/tenstorrent/vllm/blob/cd9e5b83/vllm/executor/uniproc_executor.py)
*   [vllm/outputs.py](https://github.com/tenstorrent/vllm/blob/cd9e5b83/vllm/outputs.py)
*   [vllm/sampling_params.py](https://github.com/tenstorrent/vllm/blob/cd9e5b83/vllm/sampling_params.py)
*   [vllm/sequence.py](https://github.com/tenstorrent/vllm/blob/cd9e5b83/vllm/sequence.py)
*   [vllm/v1/core/block_pool.py](https://github.com/tenstorrent/vllm/blob/cd9e5b83/vllm/v1/core/block_pool.py)
*   [vllm/v1/core/kv_cache_coordinator.py](https://github.com/tenstorrent/vllm/blob/cd9e5b83/vllm/v1/core/kv_cache_coordinator.py)
*   [vllm/v1/core/kv_cache_manager.py](https://github.com/tenstorrent/vllm/blob/cd9e5b83/vllm/v1/core/kv_cache_manager.py)
*   [vllm/v1/core/kv_cache_utils.py](https://github.com/tenstorrent/vllm/blob/cd9e5b83/vllm/v1/core/kv_cache_utils.py)
*   [vllm/v1/core/sched/scheduler.py](https://github.com/tenstorrent/vllm/blob/cd9e5b83/vllm/v1/core/sched/scheduler.py)
*   [vllm/v1/core/single_type_kv_cache_manager.py](https://github.com/tenstorrent/vllm/blob/cd9e5b83/vllm/v1/core/single_type_kv_cache_manager.py)
*   [vllm/v1/engine/__init__.py](https://github.com/tenstorrent/vllm/blob/cd9e5b83/vllm/v1/engine/__init__.py)
*   [vllm/v1/engine/async_llm.py](https://github.com/tenstorrent/vllm/blob/cd9e5b83/vllm/v1/engine/async_llm.py)
*   [vllm/v1/engine/core.py](https://github.com/tenstorrent/vllm/blob/cd9e5b83/vllm/v1/engine/core.py)
*   [vllm/v1/engine/core_client.py](https://github.com/tenstorrent/vllm/blob/cd9e5b83/vllm/v1/engine/core_client.py)
*   [vllm/v1/engine/llm_engine.py](https://github.com/tenstorrent/vllm/blob/cd9e5b83/vllm/v1/engine/llm_engine.py)
*   [vllm/v1/engine/processor.py](https://github.com/tenstorrent/vllm/blob/cd9e5b83/vllm/v1/engine/processor.py)
*   [vllm/v1/executor/multiproc_executor.py](https://github.com/tenstorrent/vllm/blob/cd9e5b83/vllm/v1/executor/multiproc_executor.py)
*   [vllm/v1/kv_cache_interface.py](https://github.com/tenstorrent/vllm/blob/cd9e5b83/vllm/v1/kv_cache_interface.py)
*   [vllm/v1/metrics/loggers.py](https://github.com/tenstorrent/vllm/blob/cd9e5b83/vllm/v1/metrics/loggers.py)
*   [vllm/v1/metrics/stats.py](https://github.com/tenstorrent/vllm/blob/cd9e5b83/vllm/v1/metrics/stats.py)
*   [vllm/v1/request.py](https://github.com/tenstorrent/vllm/blob/cd9e5b83/vllm/v1/request.py)
*   [vllm/v1/utils.py](https://github.com/tenstorrent/vllm/blob/cd9e5b83/vllm/v1/utils.py)

This document provides a technical overview of vLLM's core architecture, explaining the main components, their interactions, and the execution flow. This covers the internal design of vLLM's inference engine that powers both offline batch processing and online serving. For information on model support and registry, see [Model Support](https://deepwiki.com/tenstorrent/vllm/3-model-support), and for details on the API server, see [API Server](https://deepwiki.com/tenstorrent/vllm/4-api-interfaces).

## System Overview

vLLM is a high-performance inference and serving engine for Large Language Models (LLMs) with two architectural versions:

*   **V0 Architecture**: The original design (default when `VLLM_USE_V1=0`)
*   **V1 Architecture**: A newer design with improved architecture (default when `VLLM_USE_V1=1`)

At a high level, vLLM follows a manager-worker architecture pattern where an engine orchestrates scheduling and dispatching work to one or more workers that execute the model.

Sources:

*   [vllm/engine/llm_engine.py 124-177](https://github.com/tenstorrent/vllm/blob/cd9e5b83/vllm/engine/llm_engine.py#L124-L177)
*   [vllm/worker/worker.py 38-90](https://github.com/tenstorrent/vllm/blob/cd9e5b83/vllm/worker/worker.py#L38-L90)
*   [vllm/core/scheduler.py 47-187](https://github.com/tenstorrent/vllm/blob/cd9e5b83/vllm/core/scheduler.py#L47-L187)
*   [vllm/worker/model_runner.py 77-183](https://github.com/tenstorrent/vllm/blob/cd9e5b83/vllm/worker/model_runner.py#L77-L183)

## Engine Architecture

The engine is the central component in vLLM that manages requests, sequences, and coordinates the work across components.

### V0 Engine Architecture

In V0:

*   `LLMEngine` initializes and manages model execution, scheduling, and KV cache
*   `AsyncLLMEngine` wraps `LLMEngine` to provide asynchronous API for online serving
*   `RequestTracker` manages request streams for asynchronous processing

Sources:

*   [vllm/engine/llm_engine.py 125-426](https://github.com/tenstorrent/vllm/blob/cd9e5b83/vllm/engine/llm_engine.py#L125-L426)
*   [vllm/engine/async_llm_engine.py 77-264](https://github.com/tenstorrent/vllm/blob/cd9e5b83/vllm/engine/async_llm_engine.py#L77-L264)

### V1 Engine Architecture

In V1:

*   `EngineCore` is the central component that runs the inner loop
*   `EngineCoreClient` abstracts the communication with the core (in-process or multiprocess)
*   `AsyncLLM` and `LLM` are client interfaces for different use cases
*   `OutputProcessor` handles output conversion and postprocessing

Sources:

*   [vllm/v1/engine/core.py 50-102](https://github.com/tenstorrent/vllm/blob/cd9e5b83/vllm/v1/engine/core.py#L50-L102)
*   [vllm/v1/engine/llm_engine.py 38-57](https://github.com/tenstorrent/vllm/blob/cd9e5b83/vllm/v1/engine/llm_engine.py#L38-L57)
*   [vllm/v1/engine/async_llm.py 44-70](https://github.com/tenstorrent/vllm/blob/cd9e5b83/vllm/v1/engine/async_llm.py#L44-L70)
*   [vllm/v1/engine/core_client.py 41-76](https://github.com/tenstorrent/vllm/blob/cd9e5b83/vllm/v1/engine/core_client.py#L41-L76)

## Request Processing Flow

The request processing flow illustrates how a request moves through the system.

Sources:

*   [vllm/engine/llm_engine.py 651-841](https://github.com/tenstorrent/vllm/blob/cd9e5b83/vllm/engine/llm_engine.py#L651-L841)
*   [vllm/worker/worker.py 227-260](https://github.com/tenstorrent/vllm/blob/cd9e5b83/vllm/worker/worker.py#L227-L260)
*   [vllm/worker/model_runner.py 940-1140](https://github.com/tenstorrent/vllm/blob/cd9e5b83/vllm/worker/model_runner.py#L940-L1140)

## Scheduler and Memory Management

The scheduler is responsible for deciding which sequence groups to process in each iteration, while the Block Space Manager handles memory allocation for KV cache.

### Scheduler Components

The scheduler manages two key operations:

1.   **Prefill Phase**: Initial processing of prompt tokens that generates the KV cache
2.   **Decode Phase**: Iterative token generation using the cached KV states

Sources:

*   [vllm/core/scheduler.py 125-200](https://github.com/tenstorrent/vllm/blob/cd9e5b83/vllm/core/scheduler.py#L125-L200)
*   [vllm/core/scheduler.py 680-740](https://github.com/tenstorrent/vllm/blob/cd9e5b83/vllm/core/scheduler.py#L680-L740)

### Memory Management with PagedAttention

PagedAttention enables efficient memory management by:

*   Splitting KV cache into fixed-size blocks
*   Efficiently reusing memory blocks
*   Supporting CPU offloading with block swapping
*   Enabling continuous batching with non-contiguous memory access

Sources:

*   [vllm/worker/worker.py 426-449](https://github.com/tenstorrent/vllm/blob/cd9e5b83/vllm/worker/worker.py#L426-L449)
*   [vllm/core/scheduler.py 680-740](https://github.com/tenstorrent/vllm/blob/cd9e5b83/vllm/core/scheduler.py#L680-L740)

## Worker Architecture

Workers are responsible for executing the model and managing the model state.

The worker architecture includes:

*   `Worker` class that handles model execution and cache management
*   `ModelRunner` that handles the actual model execution
*   `CacheEngine` that manages KV cache blocks

Sources:

*   [vllm/worker/worker_base.py 32-113](https://github.com/tenstorrent/vllm/blob/cd9e5b83/vllm/worker/worker_base.py#L32-L113)
*   [vllm/worker/worker.py 38-63](https://github.com/tenstorrent/vllm/blob/cd9e5b83/vllm/worker/worker.py#L38-L63)
*   [vllm/worker/model_runner.py 77-143](https://github.com/tenstorrent/vllm/blob/cd9e5b83/vllm/worker/model_runner.py#L77-L143)

## Distributed Execution

vLLM supports multiple forms of parallelism for distributed inference:

The distributed execution system supports:

*   **Tensor Parallelism**: Splits model layers across GPUs
*   **Pipeline Parallelism**: Distributes model stages across GPUs
*   **Data Parallelism**: Processes different batches on different GPUs
*   **Expert Parallelism**: Distributes experts in Mixture of Experts models

Sources:

*   [vllm/engine/llm_engine.py 453-488](https://github.com/tenstorrent/vllm/blob/cd9e5b83/vllm/engine/llm_engine.py#L453-L488)
*   [vllm/envs.py 132-204](https://github.com/tenstorrent/vllm/blob/cd9e5b83/vllm/envs.py#L132-L204)
*   [vllm/config.py 866-915](https://github.com/tenstorrent/vllm/blob/cd9e5b83/vllm/config.py#L866-L915)

## Configuration System

vLLM uses a configuration system to define and manage all aspects of the engine:

The configuration system provides:

*   Comprehensive control over all engine aspects
*   Support for different deployment scenarios
*   Extensibility for new features and capabilities

Sources:

*   [vllm/config.py 185-532](https://github.com/tenstorrent/vllm/blob/cd9e5b83/vllm/config.py#L185-L532)
*   [vllm/engine/arg_utils.py 120-243](https://github.com/tenstorrent/vllm/blob/cd9e5b83/vllm/engine/arg_utils.py#L120-L243)

## V0 vs V1 Architecture Differences

The V1 architecture represents an evolution of the original V0 design:

| Aspect | V0 Architecture | V1 Architecture |
| --- | --- | --- |
| Engine Structure | Integrated LLMEngine | Split into EngineCore and clients |
| Process Model | Single process or MP executor | More flexible process model with ZMQ communication |
| Scheduler | Per virtual engine | Central scheduler design |
| Output Processing | Integrated in engine | Dedicated OutputProcessor |
| Extensibility | More coupled design | More modular, easier to extend |
| Maturity | More stable, fully featured | Newer, under active development |

You can select the architecture by setting:

*   `VLLM_USE_V1=0` for V0 architecture (default when unset)
*   `VLLM_USE_V1=1` for V1 architecture

Sources:

*   [vllm/engine/llm_engine.py 221-226](https://github.com/tenstorrent/vllm/blob/cd9e5b83/vllm/engine/llm_engine.py#L221-L226)
*   [vllm/v1/engine/llm_engine.py 38-57](https://github.com/tenstorrent/vllm/blob/cd9e5b83/vllm/v1/engine/llm_engine.py#L38-L57)
*   [vllm/v1/engine/async_llm.py 57-70](https://github.com/tenstorrent/vllm/blob/cd9e5b83/vllm/v1/engine/async_llm.py#L57-L70)
*   [vllm/envs.py 76-78](https://github.com/tenstorrent/vllm/blob/cd9e5b83/vllm/envs.py#L76-L78)

## Execution Flow

To better understand the core architecture, here's the complete execution flow of a request in vLLM:

Sources:

*   [vllm/engine/llm_engine.py 651-841](https://github.com/tenstorrent/vllm/blob/cd9e5b83/vllm/engine/llm_engine.py#L651-L841)
*   [vllm/worker/worker.py 227-260](https://github.com/tenstorrent/vllm/blob/cd9e5b83/vllm/worker/worker.py#L227-L260)
*   [vllm/worker/model_runner.py 940-1140](https://github.com/tenstorrent/vllm/blob/cd9e5b83/vllm/worker/model_runner.py#L940-L1140)
*   [vllm/sequence.py 149-385](https://github.com/tenstorrent/vllm/blob/cd9e5b83/vllm/sequence.py#L149-L385)

## Summary

The vLLM core architecture is designed for high-performance LLM inference through several key innovations:

1.   **Efficient Memory Management**: PagedAttention for non-contiguous KV cache management
2.   **Continuous Batching**: Dynamically manages sequences of varying lengths
3.   **Distributed Execution**: Multiple parallelism strategies for larger models
4.   **Flexible Deployment**: Supports both offline batch inference and online serving
5.   **Dual Architectures**: V0 (original) and V1 (evolving) architecture options

This modular and efficient design enables vLLM to achieve high throughput, low latency, and efficient resource utilization for LLM inference at scale.

Dismiss
Refresh this wiki

Enter email to refresh
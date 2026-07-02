---
project: vllm
github: https://github.com/tenstorrent/vllm
deepwiki: https://deepwiki.com/tenstorrent/vllm
---

# Overview

 Relevant source files

* [.buildkite/test-pipeline.yaml](https://github.com/tenstorrent/vllm/blob/cd9e5b83/.buildkite/test-pipeline.yaml)
* [.gitignore](https://github.com/tenstorrent/vllm/blob/cd9e5b83/.gitignore)
* [README.md](https://github.com/tenstorrent/vllm/blob/cd9e5b83/README.md?plain=1)
* [docs/usage/metrics.md](https://github.com/tenstorrent/vllm/blob/cd9e5b83/docs/usage/metrics.md?plain=1)
* [examples/online\_serving/prometheus\_grafana/grafana.json](https://github.com/tenstorrent/vllm/blob/cd9e5b83/examples/online_serving/prometheus_grafana/grafana.json)
* [tests/config/test\_config.yaml](https://github.com/tenstorrent/vllm/blob/cd9e5b83/tests/config/test_config.yaml)
* [tests/config/test\_config\_with\_model.yaml](https://github.com/tenstorrent/vllm/blob/cd9e5b83/tests/config/test_config_with_model.yaml)
* [tests/conftest.py](https://github.com/tenstorrent/vllm/blob/cd9e5b83/tests/conftest.py)
* [tests/entrypoints/openai/test\_metrics.py](https://github.com/tenstorrent/vllm/blob/cd9e5b83/tests/entrypoints/openai/test_metrics.py)
* [tests/v1/engine/test\_engine\_core.py](https://github.com/tenstorrent/vllm/blob/cd9e5b83/tests/v1/engine/test_engine_core.py)
* [tests/v1/engine/test\_engine\_core\_client.py](https://github.com/tenstorrent/vllm/blob/cd9e5b83/tests/v1/engine/test_engine_core_client.py)
* [vllm/benchmarks/\_\_init\_\_.py](https://github.com/tenstorrent/vllm/blob/cd9e5b83/vllm/benchmarks/__init__.py)
* [vllm/engine/arg\_utils.py](https://github.com/tenstorrent/vllm/blob/cd9e5b83/vllm/engine/arg_utils.py)
* [vllm/engine/async\_llm\_engine.py](https://github.com/tenstorrent/vllm/blob/cd9e5b83/vllm/engine/async_llm_engine.py)
* [vllm/engine/llm\_engine.py](https://github.com/tenstorrent/vllm/blob/cd9e5b83/vllm/engine/llm_engine.py)
* [vllm/engine/metrics.py](https://github.com/tenstorrent/vllm/blob/cd9e5b83/vllm/engine/metrics.py)
* [vllm/engine/metrics\_types.py](https://github.com/tenstorrent/vllm/blob/cd9e5b83/vllm/engine/metrics_types.py)
* [vllm/entrypoints/llm.py](https://github.com/tenstorrent/vllm/blob/cd9e5b83/vllm/entrypoints/llm.py)
* [vllm/envs.py](https://github.com/tenstorrent/vllm/blob/cd9e5b83/vllm/envs.py)
* [vllm/executor/uniproc\_executor.py](https://github.com/tenstorrent/vllm/blob/cd9e5b83/vllm/executor/uniproc_executor.py)
* [vllm/model\_executor/models/\_\_init\_\_.py](https://github.com/tenstorrent/vllm/blob/cd9e5b83/vllm/model_executor/models/__init__.py)
* [vllm/outputs.py](https://github.com/tenstorrent/vllm/blob/cd9e5b83/vllm/outputs.py)
* [vllm/sampling\_params.py](https://github.com/tenstorrent/vllm/blob/cd9e5b83/vllm/sampling_params.py)
* [vllm/sequence.py](https://github.com/tenstorrent/vllm/blob/cd9e5b83/vllm/sequence.py)
* [vllm/transformers\_utils/config.py](https://github.com/tenstorrent/vllm/blob/cd9e5b83/vllm/transformers_utils/config.py)
* [vllm/transformers\_utils/configs/\_\_init\_\_.py](https://github.com/tenstorrent/vllm/blob/cd9e5b83/vllm/transformers_utils/configs/__init__.py)
* [vllm/v1/engine/\_\_init\_\_.py](https://github.com/tenstorrent/vllm/blob/cd9e5b83/vllm/v1/engine/__init__.py)
* [vllm/v1/engine/async\_llm.py](https://github.com/tenstorrent/vllm/blob/cd9e5b83/vllm/v1/engine/async_llm.py)
* [vllm/v1/engine/core.py](https://github.com/tenstorrent/vllm/blob/cd9e5b83/vllm/v1/engine/core.py)
* [vllm/v1/engine/core\_client.py](https://github.com/tenstorrent/vllm/blob/cd9e5b83/vllm/v1/engine/core_client.py)
* [vllm/v1/engine/llm\_engine.py](https://github.com/tenstorrent/vllm/blob/cd9e5b83/vllm/v1/engine/llm_engine.py)
* [vllm/v1/engine/processor.py](https://github.com/tenstorrent/vllm/blob/cd9e5b83/vllm/v1/engine/processor.py)
* [vllm/v1/executor/multiproc\_executor.py](https://github.com/tenstorrent/vllm/blob/cd9e5b83/vllm/v1/executor/multiproc_executor.py)
* [vllm/v1/metrics/loggers.py](https://github.com/tenstorrent/vllm/blob/cd9e5b83/vllm/v1/metrics/loggers.py)
* [vllm/v1/metrics/stats.py](https://github.com/tenstorrent/vllm/blob/cd9e5b83/vllm/v1/metrics/stats.py)
* [vllm/v1/utils.py](https://github.com/tenstorrent/vllm/blob/cd9e5b83/vllm/v1/utils.py)

## Purpose and Scope

This page provides a high-level introduction to vLLM's architecture, its core components, and how they interact. It serves as an entry point for understanding the codebase and navigating to more detailed documentation.

**Related pages:**

* For installation instructions, see [Installation Guide](/tenstorrent/vllm/1.1-installation-guide)
* For specific model support details, see [Supported Models](/tenstorrent/vllm/1.2-supported-models)
* For getting started with the API, see [Quick Start Guide](/tenstorrent/vllm/1.3-quick-start-guide)
* For detailed architecture documentation, see [Core Architecture](/tenstorrent/vllm/2-core-architecture)

## What is vLLM?

vLLM is a high-performance library for Large Language Model (LLM) inference and serving. Originally developed at UC Berkeley's Sky Computing Lab, it is now a PyTorch Foundation hosted project focused on making LLM serving fast, easy, and accessible.

**Key features:**

* **High throughput**: State-of-the-art serving performance via PagedAttention and continuous batching
* **Memory efficiency**: Intelligent KV cache management using block-based allocation
* **Hardware flexibility**: Support for NVIDIA GPUs, AMD GPUs, Intel CPUs/GPUs, TPU, and more
* **API compatibility**: OpenAI-compatible API server for easy integration
* **Model diversity**: Support for text generation, embedding, multimodal, and speculative decoding

Sources: [README.md60-96](https://github.com/tenstorrent/vllm/blob/cd9e5b83/README.md?plain=1#L60-L96)

## System Architecture Overview

vLLM is organized into distinct layers that separate concerns and enable flexible deployment:

**Architecture diagram: vLLM system layers with code entity mappings**

Sources: [vllm/entrypoints/llm.py70-204](https://github.com/tenstorrent/vllm/blob/cd9e5b83/vllm/entrypoints/llm.py#L70-L204) [vllm/v1/engine/async\_llm.py52-88](https://github.com/tenstorrent/vllm/blob/cd9e5b83/vllm/v1/engine/async_llm.py#L52-L88) [vllm/v1/engine/llm\_engine.py46-59](https://github.com/tenstorrent/vllm/blob/cd9e5b83/vllm/v1/engine/llm_engine.py#L46-L59) [vllm/v1/engine/core.py64-71](https://github.com/tenstorrent/vllm/blob/cd9e5b83/vllm/v1/engine/core.py#L64-L71) [vllm/v1/engine/processor.py36-64](https://github.com/tenstorrent/vllm/blob/cd9e5b83/vllm/v1/engine/processor.py#L36-L64)

## Core Components

### Component Responsibility Table

| Layer | Component | Module Path | Primary Responsibility |
| --- | --- | --- | --- |
| **Interface** | `LLM` | `vllm.entrypoints.llm` | Synchronous Python API for offline inference |
| `AsyncLLM` | `vllm.v1.engine.async_llm` | Asynchronous engine for online serving |
| OpenAI Server | `vllm.entrypoints.openai` | FastAPI-based OpenAI-compatible API |
| **Engine** | `Processor` | `vllm.v1.engine.processor` | Input validation, tokenization, multimodal processing |
| `EngineCoreClient` | `vllm.v1.engine.core_client` | IPC abstraction between client and engine process |
| `EngineCore` | `vllm.v1.engine.core` | Core orchestration loop, manages scheduler and executor |
| **Scheduling** | `Scheduler` | `vllm.v1.core.sched.scheduler` | Request scheduling, resource allocation, preemption |
| `KVCacheManager` | `vllm.v1.core.kv_cache_manager` | KV cache block allocation and prefix caching |
| **Execution** | `Executor` | `vllm.v1.executor.abstract` | Worker process management and model execution |
| `Worker` | `vllm.v1.worker.worker_base` | Per-GPU worker for model execution |
| `ModelRunner` | `vllm.v1.worker.model_runner` | Batched model forward pass and sampling |
| **Config** | `EngineArgs` | `vllm.engine.arg_utils` | CLI/API argument parsing and validation |
| `VllmConfig` | `vllm.config` | Unified configuration container |

Sources: [vllm/entrypoints/llm.py70-166](https://github.com/tenstorrent/vllm/blob/cd9e5b83/vllm/entrypoints/llm.py#L70-L166) [vllm/v1/engine/async\_llm.py52-141](https://github.com/tenstorrent/vllm/blob/cd9e5b83/vllm/v1/engine/async_llm.py#L52-L141) [vllm/v1/engine/core.py64-132](https://github.com/tenstorrent/vllm/blob/cd9e5b83/vllm/v1/engine/core.py#L64-L132) [vllm/v1/engine/processor.py36-64](https://github.com/tenstorrent/vllm/blob/cd9e5b83/vllm/v1/engine/processor.py#L36-L64)

## Request Processing Flow

The following diagram shows how a user request flows through vLLM's architecture, from input to output generation:

**Request flow diagram: From user input to token generation**

### Key Request Processing Steps

1. **Input Processing** ([vllm/v1/engine/processor.py150-250](https://github.com/tenstorrent/vllm/blob/cd9e5b83/vllm/v1/engine/processor.py#L150-L250))

   * `Processor.process_inputs()` validates and tokenizes input
   * Multimodal data is processed and cached via `mm_processor_cache`
   * Creates `EngineCoreRequest` with parsed tokens and parameters
2. **Scheduling** ([vllm/v1/engine/core.py218-244](https://github.com/tenstorrent/vllm/blob/cd9e5b83/vllm/v1/engine/core.py#L218-L244))

   * `EngineCore.add_request()` adds request to scheduler
   * `Scheduler.schedule()` decides which requests to execute
   * `KVCacheManager.allocate_slots()` allocates memory blocks
3. **Execution** ([vllm/v1/engine/core.py327-392](https://github.com/tenstorrent/vllm/blob/cd9e5b83/vllm/v1/engine/core.py#L327-L392))

   * `EngineCore.step()` orchestrates model execution
   * `Executor.execute_model()` dispatches to worker processes
   * `ModelRunner.forward()` performs batched inference
4. **Output Processing** ([vllm/v1/engine/output\_processor.py](https://github.com/tenstorrent/vllm/blob/cd9e5b83/vllm/v1/engine/output_processor.py))

   * `OutputProcessor` converts model outputs to `RequestOutput`
   * Detokenization and streaming handled incrementally

Sources: [vllm/v1/engine/core.py218-392](https://github.com/tenstorrent/vllm/blob/cd9e5b83/vllm/v1/engine/core.py#L218-L392) [vllm/v1/engine/processor.py150-250](https://github.com/tenstorrent/vllm/blob/cd9e5b83/vllm/v1/engine/processor.py#L150-L250)

## Configuration System

vLLM uses a hierarchical configuration system that consolidates settings from multiple sources:

**Configuration flow diagram: From user input to validated VllmConfig**

### Configuration Classes

| Config Class | File Location | Purpose |
| --- | --- | --- |
| `EngineArgs` | [vllm/engine/arg\_utils.py285-487](https://github.com/tenstorrent/vllm/blob/cd9e5b83/vllm/engine/arg_utils.py#L285-L487) | Parses CLI/API arguments, entry point for all configuration |
| `VllmConfig` | [vllm/config/\_\_init\_\_.py](https://github.com/tenstorrent/vllm/blob/cd9e5b83/vllm/config/__init__.py) | Unified container for all configuration objects |
| `ModelConfig` | [vllm/config/model.py](https://github.com/tenstorrent/vllm/blob/cd9e5b83/vllm/config/model.py) | Model architecture, dtype, quantization settings |
| `CacheConfig` | [vllm/config/cache.py](https://github.com/tenstorrent/vllm/blob/cd9e5b83/vllm/config/cache.py) | KV cache configuration (block size, memory limits) |
| `ParallelConfig` | [vllm/config/parallel.py](https://github.com/tenstorrent/vllm/blob/cd9e5b83/vllm/config/parallel.py) | Distributed execution settings (tensor/pipeline/data parallelism) |
| `SchedulerConfig` | [vllm/config/scheduler.py](https://github.com/tenstorrent/vllm/blob/cd9e5b83/vllm/config/scheduler.py) | Scheduling policies, batch sizes, chunked prefill |
| `LoadConfig` | [vllm/config/load.py](https://github.com/tenstorrent/vllm/blob/cd9e5b83/vllm/config/load.py) | Model loading and weight download settings |

Sources: [vllm/engine/arg\_utils.py285-507](https://github.com/tenstorrent/vllm/blob/cd9e5b83/vllm/engine/arg_utils.py#L285-L507) [vllm/config/\_\_init\_\_.py](https://github.com/tenstorrent/vllm/blob/cd9e5b83/vllm/config/__init__.py)

## Deployment Modes

vLLM supports multiple deployment modes based on the use case:

### Mode Comparison

| Mode | Entry Point | Use Case | Multiprocessing | Code Path |
| --- | --- | --- | --- | --- |
| **Offline Inference** | `LLM` class | Batch processing, development | Optional | [vllm/entrypoints/llm.py70](https://github.com/tenstorrent/vllm/blob/cd9e5b83/vllm/entrypoints/llm.py#L70-L70) |
| **Online Serving (Sync)** | `LLMEngine` | Legacy sync serving | Optional | [vllm/v1/engine/llm\_engine.py46](https://github.com/tenstorrent/vllm/blob/cd9e5b83/vllm/v1/engine/llm_engine.py#L46-L46) |
| **Online Serving (Async)** | `AsyncLLM` | Production serving, high concurrency | Yes (V1) | [vllm/v1/engine/async\_llm.py52](https://github.com/tenstorrent/vllm/blob/cd9e5b83/vllm/v1/engine/async_llm.py#L52-L52) |
| **OpenAI API Server** | `vllm serve` CLI | OpenAI-compatible REST API | Yes | [vllm/entrypoints/openai/api\_server.py](https://github.com/tenstorrent/vllm/blob/cd9e5b83/vllm/entrypoints/openai/api_server.py) |

### Multiprocessing Architecture (V1)

When `VLLM_ENABLE_V1_MULTIPROCESSING=1`, vLLM uses a three-process architecture:

**Multiprocess architecture diagram: V1 engine with IPC mechanisms**

**IPC mechanisms:**

* **Client ↔ Engine**: ZeroMQ sockets (`ROUTER`/`DEALER`/`PUSH`/`PULL`)
* **Engine ↔ Workers**: Shared memory message queues via `MessageQueue` class
* **Broadcast Queue**: Distributes work to all workers
* **Response Queues**: Collect outputs from each worker

Sources: [vllm/v1/engine/core\_client.py49-102](https://github.com/tenstorrent/vllm/blob/cd9e5b83/vllm/v1/engine/core_client.py#L49-L102) [vllm/v1/engine/core.py64-97](https://github.com/tenstorrent/vllm/blob/cd9e5b83/vllm/v1/engine/core.py#L64-L97) [vllm/v1/executor/multiproc\_executor.py49-118](https://github.com/tenstorrent/vllm/blob/cd9e5b83/vllm/v1/executor/multiproc_executor.py#L49-L118)

## Environment Variables

vLLM behavior is controlled by numerous environment variables. Key examples:

| Variable | Default | Purpose | File Reference |
| --- | --- | --- | --- |
| `VLLM_USE_V1` | `True` | Enable V1 engine architecture | [vllm/envs.py103](https://github.com/tenstorrent/vllm/blob/cd9e5b83/vllm/envs.py#L103-L103) |
| `VLLM_ENABLE_V1_MULTIPROCESSING` | `True` | Use multiprocess mode in V1 | [vllm/envs.py118](https://github.com/tenstorrent/vllm/blob/cd9e5b83/vllm/envs.py#L118-L118) |
| `VLLM_TARGET_DEVICE` | `cuda` | Target hardware (cuda/rocm/cpu/tpu/xpu) | [vllm/envs.py373-374](https://github.com/tenstorrent/vllm/blob/cd9e5b83/vllm/envs.py#L373-L374) |
| `VLLM_ATTENTION_BACKEND` | `None` | Force specific attention backend | [vllm/envs.py620-624](https://github.com/tenstorrent/vllm/blob/cd9e5b83/vllm/envs.py#L620-L624) |
| `VLLM_WORKER_MULTIPROC_METHOD` | `fork` | Multiprocessing method (fork/spawn) | [vllm/envs.py65](https://github.com/tenstorrent/vllm/blob/cd9e5b83/vllm/envs.py#L65-L65) |

See [vllm/envs.py365-210](https://github.com/tenstorrent/vllm/blob/cd9e5b83/vllm/envs.py#L365-L210) for the complete list of environment variables.

Sources: [vllm/envs.py1-211](https://github.com/tenstorrent/vllm/blob/cd9e5b83/vllm/envs.py#L1-L211)

## Key Abstractions

### Request

The `Request` class ([vllm/v1/request.py](https://github.com/tenstorrent/vllm/blob/cd9e5b83/vllm/v1/request.py)) represents a single inference request and tracks:

* `request_id`: Unique identifier
* `prompt_token_ids`: Tokenized input
* `sampling_params` or `pooling_params`: Generation configuration
* `mm_inputs`: Multimodal inputs (images, audio, video)
* `status`: Current state (waiting/running/finished)

### SchedulerOutput

The `SchedulerOutput` class ([vllm/v1/core/sched/output.py](https://github.com/tenstorrent/vllm/blob/cd9e5b83/vllm/v1/core/sched/output.py)) contains the scheduling decision:

* `scheduled_new_reqs`: New requests to start processing
* `scheduled_resumed_reqs`: Preempted requests to resume
* `scheduled_running_reqs`: Continuing requests
* `num_scheduled_tokens`: Total tokens to process

### ModelRunnerOutput

The `ModelRunnerOutput` class ([vllm/v1/outputs.py](https://github.com/tenstorrent/vllm/blob/cd9e5b83/vllm/v1/outputs.py)) contains model execution results:

* `sampled_token_ids`: Generated tokens
* `logprobs`: Log probabilities (if requested)
* `hidden_states`: Intermediate activations (for multi-stage models)

Sources: [vllm/v1/request.py](https://github.com/tenstorrent/vllm/blob/cd9e5b83/vllm/v1/request.py) [vllm/v1/core/sched/output.py](https://github.com/tenstorrent/vllm/blob/cd9e5b83/vllm/v1/core/sched/output.py) [vllm/v1/outputs.py](https://github.com/tenstorrent/vllm/blob/cd9e5b83/vllm/v1/outputs.py)

## Next Steps

For deeper understanding of specific subsystems:

* **Configuration details**: See [Configuration System](/tenstorrent/vllm/2.1-configuration-system)
* **Engine internals**: See [Engine Components](/tenstorrent/vllm/2.2-engine-components)
* **Scheduling algorithms**: See [Scheduler and Request Lifecycle](/tenstorrent/vllm/2.3-scheduler-and-request-lifecycle)
* **Memory management**: See [Memory Management](/tenstorrent/vllm/2.4-memory-management)
* **Worker execution**: See [Worker Architecture](/tenstorrent/vllm/2.5-worker-architecture)
* **Hardware backends**: See [Platform Abstraction Layer](/tenstorrent/vllm/5.1-platform-abstraction-layer)

Sources: [README.md1-183](https://github.com/tenstorrent/vllm/blob/cd9e5b83/README.md?plain=1#L1-L183) [vllm/v1/engine/core.py1-600](https://github.com/tenstorrent/vllm/blob/cd9e5b83/vllm/v1/engine/core.py#L1-L600)

Refresh this wiki
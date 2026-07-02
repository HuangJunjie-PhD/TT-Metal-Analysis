---
project: vllm
github: https://github.com/tenstorrent/vllm
deepwiki: https://deepwiki.com/tenstorrent/vllm/6-advanced-topics
---

# Advanced Topics

Relevant source files
*   [.buildkite/test-pipeline.yaml](https://github.com/tenstorrent/vllm/blob/cd9e5b83/.buildkite/test-pipeline.yaml)
*   [tests/config/test_config.yaml](https://github.com/tenstorrent/vllm/blob/cd9e5b83/tests/config/test_config.yaml)
*   [tests/config/test_config_with_model.yaml](https://github.com/tenstorrent/vllm/blob/cd9e5b83/tests/config/test_config_with_model.yaml)
*   [tests/conftest.py](https://github.com/tenstorrent/vllm/blob/cd9e5b83/tests/conftest.py)
*   [tests/v1/core/test_async_scheduler.py](https://github.com/tenstorrent/vllm/blob/cd9e5b83/tests/v1/core/test_async_scheduler.py)
*   [tests/v1/core/test_kv_cache_utils.py](https://github.com/tenstorrent/vllm/blob/cd9e5b83/tests/v1/core/test_kv_cache_utils.py)
*   [tests/v1/core/test_prefix_caching.py](https://github.com/tenstorrent/vllm/blob/cd9e5b83/tests/v1/core/test_prefix_caching.py)
*   [tests/v1/core/test_scheduler.py](https://github.com/tenstorrent/vllm/blob/cd9e5b83/tests/v1/core/test_scheduler.py)
*   [tests/v1/core/test_single_type_kv_cache_manager.py](https://github.com/tenstorrent/vllm/blob/cd9e5b83/tests/v1/core/test_single_type_kv_cache_manager.py)
*   [vllm/engine/arg_utils.py](https://github.com/tenstorrent/vllm/blob/cd9e5b83/vllm/engine/arg_utils.py)
*   [vllm/engine/async_llm_engine.py](https://github.com/tenstorrent/vllm/blob/cd9e5b83/vllm/engine/async_llm_engine.py)
*   [vllm/engine/llm_engine.py](https://github.com/tenstorrent/vllm/blob/cd9e5b83/vllm/engine/llm_engine.py)
*   [vllm/entrypoints/llm.py](https://github.com/tenstorrent/vllm/blob/cd9e5b83/vllm/entrypoints/llm.py)
*   [vllm/envs.py](https://github.com/tenstorrent/vllm/blob/cd9e5b83/vllm/envs.py)
*   [vllm/outputs.py](https://github.com/tenstorrent/vllm/blob/cd9e5b83/vllm/outputs.py)
*   [vllm/sampling_params.py](https://github.com/tenstorrent/vllm/blob/cd9e5b83/vllm/sampling_params.py)
*   [vllm/sequence.py](https://github.com/tenstorrent/vllm/blob/cd9e5b83/vllm/sequence.py)
*   [vllm/v1/core/block_pool.py](https://github.com/tenstorrent/vllm/blob/cd9e5b83/vllm/v1/core/block_pool.py)
*   [vllm/v1/core/kv_cache_coordinator.py](https://github.com/tenstorrent/vllm/blob/cd9e5b83/vllm/v1/core/kv_cache_coordinator.py)
*   [vllm/v1/core/kv_cache_manager.py](https://github.com/tenstorrent/vllm/blob/cd9e5b83/vllm/v1/core/kv_cache_manager.py)
*   [vllm/v1/core/kv_cache_utils.py](https://github.com/tenstorrent/vllm/blob/cd9e5b83/vllm/v1/core/kv_cache_utils.py)
*   [vllm/v1/core/sched/scheduler.py](https://github.com/tenstorrent/vllm/blob/cd9e5b83/vllm/v1/core/sched/scheduler.py)
*   [vllm/v1/core/single_type_kv_cache_manager.py](https://github.com/tenstorrent/vllm/blob/cd9e5b83/vllm/v1/core/single_type_kv_cache_manager.py)
*   [vllm/v1/kv_cache_interface.py](https://github.com/tenstorrent/vllm/blob/cd9e5b83/vllm/v1/kv_cache_interface.py)
*   [vllm/v1/request.py](https://github.com/tenstorrent/vllm/blob/cd9e5b83/vllm/v1/request.py)

This page covers advanced features and optimization techniques for power users of vLLM. These topics are designed for users who need fine-grained control over model inference behavior, performance optimization, or distributed execution.

**Scope**: This page provides an overview of advanced vLLM capabilities including sampling control, prefix caching, distributed execution, and performance tuning. For basic usage patterns, see [Quick Start Guide](https://deepwiki.com/tenstorrent/vllm/1.3-quick-start-guide). For core architecture concepts, see [Core Architecture](https://deepwiki.com/tenstorrent/vllm/2-core-architecture).

* * *

## Overview of Advanced Features

vLLM provides several advanced capabilities that enable sophisticated use cases beyond basic text generation:

| Feature Category | Key Capabilities | Primary Use Cases |
| --- | --- | --- |
| **Sampling Control** ([6.1](https://deepwiki.com/tenstorrent/vllm/6.1-sampling-and-generation-control)) | Structured outputs, custom logits processors, generation constraints | JSON/grammar-constrained generation, tool calling, reasoning |
| **Prefix Caching** ([6.2](https://deepwiki.com/tenstorrent/vllm/6.2-prefix-caching)) | Automatic KV cache reuse, block-level hashing | Multi-turn conversations, shared prompts, RAG applications |
| **Distributed Execution** ([6.3](https://deepwiki.com/tenstorrent/vllm/6.3-distributed-execution)) | Tensor/pipeline/data parallelism, KV transfer | Large model serving, multi-node deployment, disaggregation |
| **Performance Optimization** ([6.4](https://deepwiki.com/tenstorrent/vllm/6.4-performance-optimization)) | CUDA graphs, speculative decoding, chunked prefill | Low latency, high throughput, memory efficiency |

* * *

## Configuration System

Advanced features are primarily configured through `EngineArgs` and environment variables. The configuration system provides multiple entry points:

**Sources**: [vllm/engine/arg_utils.py 284-472](https://github.com/tenstorrent/vllm/blob/cd9e5b83/vllm/engine/arg_utils.py#L284-L472)[vllm/envs.py 367-1200](https://github.com/tenstorrent/vllm/blob/cd9e5b83/vllm/envs.py#L367-L1200)

* * *

## Sampling Parameters and Request Control

The `SamplingParams` class controls generation behavior at the request level:

### Key Sampling Features

**Sources**: [vllm/sampling_params.py 85-400](https://github.com/tenstorrent/vllm/blob/cd9e5b83/vllm/sampling_params.py#L85-L400)[vllm/engine/arg_utils.py 425-481](https://github.com/tenstorrent/vllm/blob/cd9e5b83/vllm/engine/arg_utils.py#L425-L481)

* * *

## Prefix Caching Architecture

Prefix caching automatically reuses KV cache blocks across requests with shared prompt prefixes. This is transparent to the user when enabled:

### Block-Level Caching Mechanism

**Key Concepts**:

*   **Block Hashing**: Each complete block (16 tokens by default) is hashed based on its token IDs and parent block hash
*   **Block Granularity**: Caching operates at block boundaries; partial blocks are not cached
*   **Reference Counting**: Cached blocks track usage via `ref_cnt` to enable safe eviction
*   **Cache Hit Detection**: `get_computed_blocks()` finds longest matching prefix before scheduling

**Sources**: [vllm/v1/core/kv_cache_manager.py 154-199](https://github.com/tenstorrent/vllm/blob/cd9e5b83/vllm/v1/core/kv_cache_manager.py#L154-L199)[vllm/v1/core/kv_cache_utils.py 40-96](https://github.com/tenstorrent/vllm/blob/cd9e5b83/vllm/v1/core/kv_cache_utils.py#L40-L96)[tests/v1/core/test_prefix_caching.py 117-183](https://github.com/tenstorrent/vllm/blob/cd9e5b83/tests/v1/core/test_prefix_caching.py#L117-L183)

* * *

## Hash Function and Cache Salt

vLLM supports configurable hash functions for block hashing:

The `cache_salt` parameter enables cache isolation between different logical contexts (e.g., users, sessions) while still allowing sharing within the same context.

**Sources**: [vllm/v1/core/kv_cache_utils.py 81-96](https://github.com/tenstorrent/vllm/blob/cd9e5b83/vllm/v1/core/kv_cache_utils.py#L81-L96)[vllm/v1/request.py 41-72](https://github.com/tenstorrent/vllm/blob/cd9e5b83/vllm/v1/request.py#L41-L72)[tests/v1/core/test_kv_cache_utils.py 103-124](https://github.com/tenstorrent/vllm/blob/cd9e5b83/tests/v1/core/test_kv_cache_utils.py#L103-L124)

* * *

## Distributed Execution Configuration

vLLM supports multiple parallelism strategies that can be combined:

### Parallelism Strategy Selection

**Sources**: [vllm/engine/arg_utils.py 315-378](https://github.com/tenstorrent/vllm/blob/cd9e5b83/vllm/engine/arg_utils.py#L315-L378)[vllm/config/__init__.py](https://github.com/tenstorrent/vllm/blob/cd9e5b83/vllm/config/__init__.py)

* * *

## Performance Optimization Patterns

### CUDA Graphs

CUDA graphs reduce kernel launch overhead for decode steps:

Environment variables for tuning:

*   `VLLM_FLASH_ATTN_MAX_NUM_SPLITS_FOR_CUDA_GRAPH`: Controls attention kernel splitting in CUDA graphs
*   `VLLM_DISABLE_PAD_FOR_CUDAGRAPH`: Disables padding for CUDA graphs
*   `VLLM_ENABLE_CUDAGRAPH_GC`: Enables garbage collection for CUDA graphs

### Speculative Decoding

Speculative decoding uses a smaller draft model to accelerate generation:

Supported methods:

*   `eagle`: EAGLE speculative decoding
*   `eagle3`: EAGLE3 with 3-level speculation
*   `mlp_speculator`: MLP-based draft model

### Chunked Prefill

Chunked prefill breaks long prompts into smaller chunks to maintain fairness:

**Sources**: [vllm/engine/arg_utils.py 310-451](https://github.com/tenstorrent/vllm/blob/cd9e5b83/vllm/engine/arg_utils.py#L310-L451)[vllm/envs.py 127-194](https://github.com/tenstorrent/vllm/blob/cd9e5b83/vllm/envs.py#L127-L194)

* * *

## Performance Tuning Checklist

### Memory Optimization

### Throughput Optimization

### Latency Optimization

* * *

## Advanced Environment Variables

Key environment variables for advanced features:

| Variable | Purpose | Default |
| --- | --- | --- |
| `VLLM_ATTENTION_BACKEND` | Override attention backend selection | Auto-detect |
| `VLLM_USE_FLASHINFER_SAMPLER` | Use FlashInfer sampler for faster sampling | Auto-detect |
| `VLLM_USE_V1` | Enable V1 engine (default in latest versions) | `True` |
| `VLLM_ENABLE_V1_MULTIPROCESSING` | Enable multiprocessing for V1 engine | `True` |
| `VLLM_LOGGING_LEVEL` | Logging verbosity | `INFO` |
| `VLLM_TRACE_FUNCTION` | Enable function tracing for debugging | `0` |
| `VLLM_ALLOW_LONG_MAX_MODEL_LEN` | Allow longer sequences than default | `False` |

### Compilation and Optimization Flags

Environment variables:

*   `VLLM_USE_STANDALONE_COMPILE`: Use standalone compilation mode
*   `VLLM_ENABLE_INDUCTOR_MAX_AUTOTUNE`: Enable maximum autotuning
*   `VLLM_DISABLE_COMPILE_CACHE`: Disable compilation cache

**Sources**: [vllm/envs.py 367-1200](https://github.com/tenstorrent/vllm/blob/cd9e5b83/vllm/envs.py#L367-L1200)[vllm/engine/arg_utils.py 451-472](https://github.com/tenstorrent/vllm/blob/cd9e5b83/vllm/engine/arg_utils.py#L451-L472)

* * *

## Request Lifecycle with Advanced Features

**Sources**: [vllm/v1/core/sched/scheduler.py 180-450](https://github.com/tenstorrent/vllm/blob/cd9e5b83/vllm/v1/core/sched/scheduler.py#L180-L450)[vllm/v1/core/kv_cache_manager.py 154-260](https://github.com/tenstorrent/vllm/blob/cd9e5b83/vllm/v1/core/kv_cache_manager.py#L154-L260)

* * *

## Monitoring and Metrics

### Prefix Cache Metrics

The `PrefixCacheStats` class tracks:

*   `requests`: Number of requests processed
*   `queries`: Total tokens queried
*   `hits`: Number of cached tokens found
*   `hit_rate`: Computed as `hits / queries`

### Request State Metrics

**Sources**: [vllm/v1/metrics/stats.py](https://github.com/tenstorrent/vllm/blob/cd9e5b83/vllm/v1/metrics/stats.py)[vllm/v1/core/kv_cache_utils.py 98-167](https://github.com/tenstorrent/vllm/blob/cd9e5b83/vllm/v1/core/kv_cache_utils.py#L98-L167)

* * *

## Best Practices Summary

### When to Use Each Feature

1.   **Enable Prefix Caching** when:

    *   Multiple requests share common prompt prefixes
    *   Running multi-turn conversations
    *   Using retrieval-augmented generation (RAG)
    *   Serving agentic workflows with tool calls

2.   **Use Structured Outputs** when:

    *   Generating JSON responses for APIs
    *   Enforcing grammar constraints
    *   Implementing tool calling
    *   Requiring specific output formats

3.   **Apply Distributed Execution** when:

    *   Model doesn't fit on single GPU (use TP)
    *   Need to scale throughput (use DP)
    *   Have very long sequences (use PP)
    *   Want to separate prefill/decode (use KV transfer)

4.   **Optimize Performance** by:

    *   Starting with default CUDA graphs enabled
    *   Tuning `max_num_batched_tokens` based on workload
    *   Using chunked prefill for mixed prompt lengths
    *   Considering speculative decoding for high-latency scenarios

* * *

## Advanced Configuration Example

Complete example combining multiple advanced features:

**Sources**: [vllm/entrypoints/llm.py 167-303](https://github.com/tenstorrent/vllm/blob/cd9e5b83/vllm/entrypoints/llm.py#L167-L303)[vllm/sampling_params.py 85-400](https://github.com/tenstorrent/vllm/blob/cd9e5b83/vllm/sampling_params.py#L85-L400)

* * *

For detailed documentation on each advanced topic, see the subsections:

*   **[Sampling and Generation Control](https://deepwiki.com/tenstorrent/vllm/6.1-sampling-and-generation-control)**: Deep dive into sampling parameters and structured outputs
*   **[Prefix Caching](https://deepwiki.com/tenstorrent/vllm/6.2-prefix-caching)**: Implementation details and optimization strategies
*   **[Distributed Execution](https://deepwiki.com/tenstorrent/vllm/6.3-distributed-execution)**: Multi-GPU and multi-node deployment patterns
*   **[Performance Optimization](https://deepwiki.com/tenstorrent/vllm/6.4-performance-optimization)**: CUDA graphs, speculative decoding, and tuning guidelines

Dismiss
Refresh this wiki

Enter email to refresh
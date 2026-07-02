---
project: vllm
github: https://github.com/tenstorrent/vllm
deepwiki: https://deepwiki.com/tenstorrent/vllm/5-hardware-and-platform-support
---

# Hardware and Platform Support

Relevant source files
*   [vllm/platforms/__init__.py](https://github.com/tenstorrent/vllm/blob/cd9e5b83/vllm/platforms/__init__.py)
*   [vllm/platforms/cpu.py](https://github.com/tenstorrent/vllm/blob/cd9e5b83/vllm/platforms/cpu.py)
*   [vllm/platforms/cuda.py](https://github.com/tenstorrent/vllm/blob/cd9e5b83/vllm/platforms/cuda.py)
*   [vllm/platforms/interface.py](https://github.com/tenstorrent/vllm/blob/cd9e5b83/vllm/platforms/interface.py)
*   [vllm/platforms/rocm.py](https://github.com/tenstorrent/vllm/blob/cd9e5b83/vllm/platforms/rocm.py)
*   [vllm/platforms/tpu.py](https://github.com/tenstorrent/vllm/blob/cd9e5b83/vllm/platforms/tpu.py)
*   [vllm/platforms/xpu.py](https://github.com/tenstorrent/vllm/blob/cd9e5b83/vllm/platforms/xpu.py)

## Purpose and Scope

This document describes vLLM's platform abstraction layer and multi-hardware support strategy. It covers how vLLM detects and adapts to different hardware platforms (CUDA, ROCm, TPU, CPU, XPU), how it queries device capabilities, and how platform-specific configurations and optimizations are applied.

For information about specific attention backend implementations, see [Attention Mechanisms](https://deepwiki.com/tenstorrent/vllm/2.6-attention-mechanisms). For build system configuration and compilation targets, see [Build System](https://deepwiki.com/tenstorrent/vllm/7.1-build-system).

* * *

## Platform Architecture Overview

vLLM uses a plugin-based platform abstraction system that allows it to support multiple hardware backends through a unified interface. The platform layer is responsible for:

*   Hardware detection and initialization
*   Device capability querying (compute capability, memory, device names)
*   Backend selection (attention, quantization, distributed communication)
*   Configuration validation and platform-specific adjustments
*   Hardware-specific operation implementations

### Platform Detection Flow

**Platform Detection Algorithm**

The platform detection mechanism in [vllm/platforms/\\_\\_init\\_\\_.py 180-221](https://github.com/tenstorrent/vllm/blob/cd9e5b83/vllm/platforms///_//_init//_//_.py#L180-L221) follows this logic:

1.   **Plugin Loading**: Load both builtin and out-of-tree platform plugins
2.   **Priority**: Out-of-tree plugins take precedence over builtin plugins
3.   **Uniqueness**: Only one platform can be activated; multiple activations raise an error
4.   **Detection Order**: Plugins are evaluated in registration order, but typically: TPU → CUDA → ROCm → XPU → CPU
5.   **Fallback**: If no platform is detected, `UnspecifiedPlatform` is used

Sources: [vllm/platforms/\\_\\_init\\_\\_.py 180-221](https://github.com/tenstorrent/vllm/blob/cd9e5b83/vllm/platforms///_//_init//_//_.py#L180-L221)[vllm/platforms/\\_\\_init\\_\\_.py 55-169](https://github.com/tenstorrent/vllm/blob/cd9e5b83/vllm/platforms///_//_init//_//_.py#L55-L169)

* * *

## Platform Interface

The `Platform` base class defines the contract that all platform implementations must satisfy. It provides default implementations for common operations and requires subclasses to implement hardware-specific methods.

### Core Platform Attributes

### Key Platform Responsibilities

| Responsibility | Method(s) | Description |
| --- | --- | --- |
| Device Detection | `get_device_name()`, `get_device_capability()`, `device_count()` | Query hardware properties |
| Memory Management | `get_device_total_memory()`, `get_current_memory_usage()` | Query and track memory usage |
| Backend Selection | `get_attn_backend_cls()`, `get_vit_attn_backend()` | Select optimal attention backend |
| Configuration | `check_and_update_config()` | Validate and adjust `VllmConfig` for platform |
| Capability Queries | `supports_fp8()`, `support_hybrid_kv_cache()`, `support_static_graph_mode()` | Report platform capabilities |
| Distributed Comm | `get_device_communicator_cls()`, `use_custom_allreduce()` | Configure distributed execution |
| Validation | `verify_quantization()`, `verify_model_arch()`, `check_if_supports_dtype()` | Verify compatibility |

Sources: [vllm/platforms/interface.py 80-600](https://github.com/tenstorrent/vllm/blob/cd9e5b83/vllm/platforms/interface.py#L80-L600)

* * *

## CUDA Platform (NVIDIA GPUs)

The CUDA platform supports NVIDIA GPUs and is the most feature-complete platform in vLLM. It uses NVML (NVIDIA Management Library) for hardware queries when available, falling back to PyTorch's CUDA APIs.

### CUDA Platform Detection

**NVML vs Non-NVML Implementations**

vLLM provides two CUDA platform implementations:

*   **`NvmlCudaPlatform`**[vllm/platforms/cuda.py 548-644](https://github.com/tenstorrent/vllm/blob/cd9e5b83/vllm/platforms/cuda.py#L548-L644): Uses NVML for stateless device queries without initializing CUDA context. Supports NVLink detection and accurate device information.

*   **`NonNvmlCudaPlatform`**[vllm/platforms/cuda.py 646-669](https://github.com/tenstorrent/vllm/blob/cd9e5b83/vllm/platforms/cuda.py#L646-L669): Falls back to `torch.cuda` APIs. Used on Jetson devices where NVML is not available.

The system automatically selects between these at import time [vllm/platforms/cuda.py 673-686](https://github.com/tenstorrent/vllm/blob/cd9e5b83/vllm/platforms/cuda.py#L673-L686)

Sources: [vllm/platforms/\\_\\_init\\_\\_.py 55-100](https://github.com/tenstorrent/vllm/blob/cd9e5b83/vllm/platforms///_//_init//_//_.py#L55-L100)[vllm/platforms/cuda.py 548-688](https://github.com/tenstorrent/vllm/blob/cd9e5b83/vllm/platforms/cuda.py#L548-L688)

### Device Capability Detection

CUDA platforms report device compute capability (SM version), which is critical for backend selection:

| Compute Capability | Architecture | vLLM Support |
| --- | --- | --- |
| SM 7.5 | Turing (T4) | Triton/Flex attention |
| SM 8.0, 8.6, 8.9 | Ampere (A100, A10, H100) | FlashAttention, bfloat16 |
| SM 9.0 | Hopper (H100) | FP8, advanced quantization |
| SM 10.0 | Blackwell (B100) | FlashInfer preferred, CUTLASS MLA |
| SM 12.0 | Future | Experimental support |

**Device Capability Query Methods**:

*   `get_device_capability(device_id: int)` → `DeviceCapability(major, minor)`[vllm/platforms/cuda.py 553-562](https://github.com/tenstorrent/vllm/blob/cd9e5b83/vllm/platforms/cuda.py#L553-L562)
*   `has_device_capability(capability, device_id)` → `bool` - Check if device meets minimum capability [vllm/platforms/interface.py 189-210](https://github.com/tenstorrent/vllm/blob/cd9e5b83/vllm/platforms/interface.py#L189-L210)
*   `is_device_capability(capability, device_id)` → `bool` - Check for exact capability match [vllm/platforms/interface.py 213-234](https://github.com/tenstorrent/vllm/blob/cd9e5b83/vllm/platforms/interface.py#L213-L234)

Sources: [vllm/platforms/cuda.py 89-92](https://github.com/tenstorrent/vllm/blob/cd9e5b83/vllm/platforms/cuda.py#L89-L92)[vllm/platforms/cuda.py 553-562](https://github.com/tenstorrent/vllm/blob/cd9e5b83/vllm/platforms/cuda.py#L553-L562)[vllm/platforms/interface.py 63-78](https://github.com/tenstorrent/vllm/blob/cd9e5b83/vllm/platforms/interface.py#L63-L78)

### CUDA Configuration and MLA Support

The CUDA platform's `check_and_update_config()` method [vllm/platforms/cuda.py 111-196](https://github.com/tenstorrent/vllm/blob/cd9e5b83/vllm/platforms/cuda.py#L111-L196) performs critical configuration adjustments:

1.   **Worker Class Selection**: Sets default worker to `"vllm.v1.worker.gpu_worker.Worker"`[vllm/platforms/cuda.py 115-116](https://github.com/tenstorrent/vllm/blob/cd9e5b83/vllm/platforms/cuda.py#L115-L116)

2.   **Block Size Defaults**: Sets KV cache block size to 16 by default [vllm/platforms/cuda.py 118-120](https://github.com/tenstorrent/vllm/blob/cd9e5b83/vllm/platforms/cuda.py#L118-L120)

3.   **MLA Backend Selection**: For Multi-Latent Attention models [vllm/platforms/cuda.py 122-178](https://github.com/tenstorrent/vllm/blob/cd9e5b83/vllm/platforms/cuda.py#L122-L178):

    *   **Blackwell GPUs (SM 10.0)**: Defaults to CUTLASS_MLA with block_size=128
    *   **Non-Blackwell**: Defaults to FlashMLA with block_size=64
    *   **FlashInfer MLA**: Requires block_size in [32, 64]
    *   **Sparse MLA**: Requires block_size=64

4.   **CUDA Graph Disabling**: Disables CUDA graphs when using DeepEP high-throughput kernels [vllm/platforms/cuda.py 183-196](https://github.com/tenstorrent/vllm/blob/cd9e5b83/vllm/platforms/cuda.py#L183-L196)

Sources: [vllm/platforms/cuda.py 111-196](https://github.com/tenstorrent/vllm/blob/cd9e5b83/vllm/platforms/cuda.py#L111-L196)

### CUDA Attention Backend Selection

**Attention Backend Priority (Standard Models, V1 Engine)**:

1.   **Blackwell (SM 10.0)**: FlashInfer with HND layout [vllm/platforms/cuda.py 333-344](https://github.com/tenstorrent/vllm/blob/cd9e5b83/vllm/platforms/cuda.py#L333-L344)
2.   **Ampere/Hopper (SM 8.0+)**: 
    *   FlashAttention if no FP8 KV cache or sink tokens [vllm/platforms/cuda.py 353-363](https://github.com/tenstorrent/vllm/blob/cd9e5b83/vllm/platforms/cuda.py#L353-L363)
    *   Triton if FP8 KV cache or sink tokens on non-Hopper [vllm/platforms/cuda.py 354-357](https://github.com/tenstorrent/vllm/blob/cd9e5b83/vllm/platforms/cuda.py#L354-L357)

3.   **Turing/Older (SM < 8.0)**: FlexAttention [vllm/platforms/cuda.py 366-368](https://github.com/tenstorrent/vllm/blob/cd9e5b83/vllm/platforms/cuda.py#L366-L368)

Sources: [vllm/platforms/cuda.py 233-387](https://github.com/tenstorrent/vllm/blob/cd9e5b83/vllm/platforms/cuda.py#L233-L387)

### CUDA Memory and Device Operations

**Memory Management**:

*   `get_current_memory_usage()`: Calls `torch.cuda.empty_cache()` and `torch.cuda.reset_peak_memory_stats()`[vllm/platforms/cuda.py 199-204](https://github.com/tenstorrent/vllm/blob/cd9e5b83/vllm/platforms/cuda.py#L199-L204)
*   `get_device_total_memory()`: Uses NVML to query device memory [vllm/platforms/cuda.py 591-594](https://github.com/tenstorrent/vllm/blob/cd9e5b83/vllm/platforms/cuda.py#L591-L594)

**KV Cache Transfer Operations**:

*   `insert_blocks_to_device()`: GPU-to-GPU block copy [vllm/platforms/cuda.py 512-521](https://github.com/tenstorrent/vllm/blob/cd9e5b83/vllm/platforms/cuda.py#L512-L521)
*   `swap_out_blocks_to_host()`: GPU-to-CPU block copy [vllm/platforms/cuda.py 524-533](https://github.com/tenstorrent/vllm/blob/cd9e5b83/vllm/platforms/cuda.py#L524-L533)

**Device Capabilities**:

*   `supports_fp8()`: Returns `True` for SM 8.9+ (Ada, Hopper, Blackwell) [vllm/platforms/cuda.py 398-399](https://github.com/tenstorrent/vllm/blob/cd9e5b83/vllm/platforms/cuda.py#L398-L399)
*   `support_hybrid_kv_cache()`: Always `True`[vllm/platforms/cuda.py 536-537](https://github.com/tenstorrent/vllm/blob/cd9e5b83/vllm/platforms/cuda.py#L536-L537)
*   `support_static_graph_mode()`: Always `True` (CUDA graphs) [vllm/platforms/cuda.py 540-541](https://github.com/tenstorrent/vllm/blob/cd9e5b83/vllm/platforms/cuda.py#L540-L541)
*   `use_custom_allreduce()`: Always `True`[vllm/platforms/cuda.py 402-403](https://github.com/tenstorrent/vllm/blob/cd9e5b83/vllm/platforms/cuda.py#L402-L403)

Sources: [vllm/platforms/cuda.py 199-541](https://github.com/tenstorrent/vllm/blob/cd9e5b83/vllm/platforms/cuda.py#L199-L541)

### CUDA Distributed Communication

*   **Backend**: NCCL [vllm/platforms/cuda.py 62](https://github.com/tenstorrent/vllm/blob/cd9e5b83/vllm/platforms/cuda.py#L62-L62)
*   **Device Communicator**: `CudaCommunicator`[vllm/platforms/cuda.py 394-395](https://github.com/tenstorrent/vllm/blob/cd9e5b83/vllm/platforms/cuda.py#L394-L395)
*   **Process Group Initialization**: `stateless_init_device_torch_dist_pg()` uses NCCL with custom timeout [vllm/platforms/cuda.py 414-441](https://github.com/tenstorrent/vllm/blob/cd9e5b83/vllm/platforms/cuda.py#L414-L441)

Sources: [vllm/platforms/cuda.py 62](https://github.com/tenstorrent/vllm/blob/cd9e5b83/vllm/platforms/cuda.py#L62-L62)[vllm/platforms/cuda.py 394-395](https://github.com/tenstorrent/vllm/blob/cd9e5b83/vllm/platforms/cuda.py#L394-L395)[vllm/platforms/cuda.py 414-441](https://github.com/tenstorrent/vllm/blob/cd9e5b83/vllm/platforms/cuda.py#L414-L441)

* * *

## ROCm Platform (AMD GPUs)

The ROCm platform supports AMD GPUs including MI300 series. It uses AMDSMI (AMD System Management Interface) for hardware queries and provides ROCm-specific optimizations.

### ROCm Platform Characteristics

**Key Differences from CUDA**:

1.   **Device Type**: Uses `device_type: "cuda"` for compatibility, but `device_name: "rocm"`[vllm/platforms/rocm.py 173-174](https://github.com/tenstorrent/vllm/blob/cd9e5b83/vllm/platforms/rocm.py#L173-L174)
2.   **FP8 Format**: MI300 series uses FNUZ format; others use OCP format [vllm/platforms/rocm.py 390-399](https://github.com/tenstorrent/vllm/blob/cd9e5b83/vllm/platforms/rocm.py#L390-L399)
3.   **Custom Attention**: Uses `use_rocm_custom_paged_attention()` for GFX9 GPUs [vllm/platforms/rocm.py 131-169](https://github.com/tenstorrent/vllm/blob/cd9e5b83/vllm/platforms/rocm.py#L131-L169)

Sources: [vllm/platforms/rocm.py 171-185](https://github.com/tenstorrent/vllm/blob/cd9e5b83/vllm/platforms/rocm.py#L171-L185)

### ROCm Device Detection and Architecture

**Supported Architectures**:

**Device ID Mapping**[vllm/platforms/rocm.py 69-77](https://github.com/tenstorrent/vllm/blob/cd9e5b83/vllm/platforms/rocm.py#L69-L77):

**Device Capability**: ROCm uses GCN architecture name instead of compute capability [vllm/platforms/rocm.py 272-276](https://github.com/tenstorrent/vllm/blob/cd9e5b83/vllm/platforms/rocm.py#L272-L276):

*   Major version = architecture number (8, 9, 10, etc.)
*   Minor version = sub-architecture

Sources: [vllm/platforms/rocm.py 106-128](https://github.com/tenstorrent/vllm/blob/cd9e5b83/vllm/platforms/rocm.py#L106-L128)[vllm/platforms/rocm.py 69-77](https://github.com/tenstorrent/vllm/blob/cd9e5b83/vllm/platforms/rocm.py#L69-L77)[vllm/platforms/rocm.py 272-276](https://github.com/tenstorrent/vllm/blob/cd9e5b83/vllm/platforms/rocm.py#L272-L276)

### ROCm Attention Backend Selection

**ROCm Attention Backend Logic**[vllm/platforms/rocm.py 198-261](https://github.com/tenstorrent/vllm/blob/cd9e5b83/vllm/platforms/rocm.py#L198-L261):

1.   **MLA Models**:

    *   AIter MLA backend for block_size=1 [vllm/platforms/rocm.py 227-234](https://github.com/tenstorrent/vllm/blob/cd9e5b83/vllm/platforms/rocm.py#L227-L234)
    *   Triton MLA backend for block_size≠1 [vllm/platforms/rocm.py 219-226](https://github.com/tenstorrent/vllm/blob/cd9e5b83/vllm/platforms/rocm.py#L219-L226)

2.   **Standard Models (V1)**:

    *   **AIter FlashAttention**: If `VLLM_ROCM_USE_AITER` and `VLLM_ROCM_USE_AITER_MHA` on GFX9 [vllm/platforms/rocm.py 240-244](https://github.com/tenstorrent/vllm/blob/cd9e5b83/vllm/platforms/rocm.py#L240-L244)
    *   **RocmAttentionBackend**: If `VLLM_USE_AITER_UNIFIED_ATTENTION` or `VLLM_V1_USE_PREFILL_DECODE_ATTENTION`[vllm/platforms/rocm.py 245-253](https://github.com/tenstorrent/vllm/blob/cd9e5b83/vllm/platforms/rocm.py#L245-L253)
    *   **TritonAttentionBackend**: Default fallback [vllm/platforms/rocm.py 255-258](https://github.com/tenstorrent/vllm/blob/cd9e5b83/vllm/platforms/rocm.py#L255-L258)

Sources: [vllm/platforms/rocm.py 198-261](https://github.com/tenstorrent/vllm/blob/cd9e5b83/vllm/platforms/rocm.py#L198-L261)

### ROCm Configuration and Optimizations

**Configuration Adjustments**[vllm/platforms/rocm.py 320-340](https://github.com/tenstorrent/vllm/blob/cd9e5b83/vllm/platforms/rocm.py#L320-L340):

1.   **Block Size**: Default to 16 [vllm/platforms/rocm.py 332-333](https://github.com/tenstorrent/vllm/blob/cd9e5b83/vllm/platforms/rocm.py#L332-L333)
2.   **Worker Class**: `"vllm.v1.worker.gpu_worker.Worker"`[vllm/platforms/rocm.py 335-336](https://github.com/tenstorrent/vllm/blob/cd9e5b83/vllm/platforms/rocm.py#L335-L336)
3.   **AIter RMS Norm**: Enabled with CUDA graphs for best performance [vllm/platforms/rocm.py 337-340](https://github.com/tenstorrent/vllm/blob/cd9e5b83/vllm/platforms/rocm.py#L337-L340)

**Model Compatibility**:

*   Unsupported models: Currently none [vllm/platforms/rocm.py 46](https://github.com/tenstorrent/vllm/blob/cd9e5b83/vllm/platforms/rocm.py#L46-L46)
*   Partially supported models [vllm/platforms/rocm.py 54-68](https://github.com/tenstorrent/vllm/blob/cd9e5b83/vllm/platforms/rocm.py#L54-L68): 
    *   Qwen2, Mistral, Mixtral: SWA requires CK flash attention
    *   PaliGemma: FP32 precision issues with ROCm flash attention
    *   Phi3V: Triton flash attention may OOM on shared memory

**ROCm-Specific Features**:

*   **Custom Paged Attention**: `use_rocm_custom_paged_attention()` for GFX9/GFX11 [vllm/platforms/rocm.py 131-169](https://github.com/tenstorrent/vllm/blob/cd9e5b83/vllm/platforms/rocm.py#L131-L169)
*   **Custom AllReduce**: Enabled for MI300 series (gfx94, gfx95) [vllm/platforms/rocm.py 402-406](https://github.com/tenstorrent/vllm/blob/cd9e5b83/vllm/platforms/rocm.py#L402-L406)
*   **MX Types**: Supported on gfx95 (MI325X) [vllm/platforms/rocm.py 380-382](https://github.com/tenstorrent/vllm/blob/cd9e5b83/vllm/platforms/rocm.py#L380-L382)

Sources: [vllm/platforms/rocm.py 320-340](https://github.com/tenstorrent/vllm/blob/cd9e5b83/vllm/platforms/rocm.py#L320-L340)[vllm/platforms/rocm.py 46-68](https://github.com/tenstorrent/vllm/blob/cd9e5b83/vllm/platforms/rocm.py#L46-L68)[vllm/platforms/rocm.py 131-169](https://github.com/tenstorrent/vllm/blob/cd9e5b83/vllm/platforms/rocm.py#L131-L169)[vllm/platforms/rocm.py 380-406](https://github.com/tenstorrent/vllm/blob/cd9e5b83/vllm/platforms/rocm.py#L380-L406)

### ROCm Topology Detection

ROCm uses XGMI (AMD's high-speed interconnect) for multi-GPU communication. The `is_fully_connected()` method [vllm/platforms/rocm.py 280-300](https://github.com/tenstorrent/vllm/blob/cd9e5b83/vllm/platforms/rocm.py#L280-L300) checks if GPUs are connected via 1-hop XGMI:

Sources: [vllm/platforms/rocm.py 280-300](https://github.com/tenstorrent/vllm/blob/cd9e5b83/vllm/platforms/rocm.py#L280-L300)

* * *

## CPU Platform

The CPU platform enables vLLM to run on CPUs, primarily for development and inference on non-GPU hardware. It supports x86, ARM, PowerPC, and other architectures.

### CPU Architecture Detection

**Architecture-Specific dtype Support**[vllm/platforms/cpu.py 76-88](https://github.com/tenstorrent/vllm/blob/cd9e5b83/vllm/platforms/cpu.py#L76-L88):

| Architecture | Supported dtypes | Notes |
| --- | --- | --- |
| x86 | bfloat16, float16, float32 | Default support |
| ARM (Linux) | bfloat16, float16, float32 | Default support |
| ARM (macOS) | Conditional bfloat16 | Checks `hw.optional.arm.FEAT_BF16` |
| PowerPC | bfloat16, float32 | No float16 support |

Sources: [vllm/platforms/cpu.py 76-88](https://github.com/tenstorrent/vllm/blob/cd9e5b83/vllm/platforms/cpu.py#L76-L88)[vllm/platforms/interface.py 334-352](https://github.com/tenstorrent/vllm/blob/cd9e5b83/vllm/platforms/interface.py#L334-L352)

### CPU Platform Configuration

**Key Configuration Changes**[vllm/platforms/cpu.py 140-277](https://github.com/tenstorrent/vllm/blob/cd9e5b83/vllm/platforms/cpu.py#L140-L277):

1.   **Block Size**: 128 if IPEX available, else 16 [vllm/platforms/cpu.py 148-156](https://github.com/tenstorrent/vllm/blob/cd9e5b83/vllm/platforms/cpu.py#L148-L156)
2.   **KV Cache Restrictions**: 
    *   FP8 e4m3 → cast to e5m2 [vllm/platforms/cpu.py 165-169](https://github.com/tenstorrent/vllm/blob/cd9e5b83/vllm/platforms/cpu.py#L165-L169)
    *   FP8 KV + FP16 model → cast model to bfloat16 [vllm/platforms/cpu.py 171-175](https://github.com/tenstorrent/vllm/blob/cd9e5b83/vllm/platforms/cpu.py#L171-L175)
    *   Incompatible with chunked prefill and prefix caching [vllm/platforms/cpu.py 159-163](https://github.com/tenstorrent/vllm/blob/cd9e5b83/vllm/platforms/cpu.py#L159-L163)

3.   **Distributed Execution**: Force `mp` backend, disable DBO [vllm/platforms/cpu.py 181-194](https://github.com/tenstorrent/vllm/blob/cd9e5b83/vllm/platforms/cpu.py#L181-L194)
4.   **Compilation**: DYNAMO_ONCE level, no CUDA graphs [vllm/platforms/cpu.py 196-229](https://github.com/tenstorrent/vllm/blob/cd9e5b83/vllm/platforms/cpu.py#L196-L229)
5.   **Environment Variables**: Configure OpenMP, NUMA, thread counts [vllm/platforms/cpu.py 238-266](https://github.com/tenstorrent/vllm/blob/cd9e5b83/vllm/platforms/cpu.py#L238-L266)

Sources: [vllm/platforms/cpu.py 140-277](https://github.com/tenstorrent/vllm/blob/cd9e5b83/vllm/platforms/cpu.py#L140-L277)

### CPU Attention Backend

The CPU platform uses PyTorch's SDPA (Scaled Dot-Product Attention) as its only attention backend [vllm/platforms/cpu.py 95-110](https://github.com/tenstorrent/vllm/blob/cd9e5b83/vllm/platforms/cpu.py#L95-L110):

**CPU-Specific Limitations**:

*   No MLA support
*   No sparse attention
*   Only V1 engine supported
*   No pin memory support [vllm/platforms/cpu.py 318-320](https://github.com/tenstorrent/vllm/blob/cd9e5b83/vllm/platforms/cpu.py#L318-L320)

Sources: [vllm/platforms/cpu.py 95-110](https://github.com/tenstorrent/vllm/blob/cd9e5b83/vllm/platforms/cpu.py#L95-L110)[vllm/platforms/cpu.py 318-320](https://github.com/tenstorrent/vllm/blob/cd9e5b83/vllm/platforms/cpu.py#L318-L320)

### CPU NUMA and Thread Management

**NUMA Node Detection**[vllm/platforms/cpu.py 279-315](https://github.com/tenstorrent/vllm/blob/cd9e5b83/vllm/platforms/cpu.py#L279-L315):

**Thread Configuration**:

*   `OMP_NUM_THREADS`: Set to `torch.get_num_threads()`[vllm/platforms/cpu.py 246](https://github.com/tenstorrent/vllm/blob/cd9e5b83/vllm/platforms/cpu.py#L246-L246)
*   `NUMEXPR_MAX_THREADS`: Set to max available threads [vllm/platforms/cpu.py 243](https://github.com/tenstorrent/vllm/blob/cd9e5b83/vllm/platforms/cpu.py#L243-L243)
*   Intel OpenMP settings: `KMP_BLOCKTIME`, `KMP_TPAUSE`, barrier patterns [vllm/platforms/cpu.py 252-262](https://github.com/tenstorrent/vllm/blob/cd9e5b83/vllm/platforms/cpu.py#L252-L262)

Sources: [vllm/platforms/cpu.py 239-266](https://github.com/tenstorrent/vllm/blob/cd9e5b83/vllm/platforms/cpu.py#L239-L266)[vllm/platforms/cpu.py 279-315](https://github.com/tenstorrent/vllm/blob/cd9e5b83/vllm/platforms/cpu.py#L279-L315)

* * *

## TPU Platform (Google Cloud TPU)

The TPU platform enables vLLM to run on Google Cloud TPUs using XLA (Accelerated Linear Algebra) compiler and Pallas attention kernels.

### TPU Platform Characteristics

**Key TPU Characteristics**:

*   **Device Type**: `"tpu"` with XLA dispatch [vllm/platforms/tpu.py 34-40](https://github.com/tenstorrent/vllm/blob/cd9e5b83/vllm/platforms/tpu.py#L34-L40)
*   **Compilation**: Forces DYNAMO_ONCE, OpenXLA backend [vllm/platforms/tpu.py 114-127](https://github.com/tenstorrent/vllm/blob/cd9e5b83/vllm/platforms/tpu.py#L114-L127)
*   **Dtype**: Only bfloat16 supported; fp16/fp32 cast to bfloat16 [vllm/platforms/tpu.py 133-138](https://github.com/tenstorrent/vllm/blob/cd9e5b83/vllm/platforms/tpu.py#L133-L138)
*   **No CUDA Graphs**: TPU doesn't support CUDA-style static graphs [vllm/platforms/tpu.py 119-124](https://github.com/tenstorrent/vllm/blob/cd9e5b83/vllm/platforms/tpu.py#L119-L124)

Sources: [vllm/platforms/tpu.py 32-44](https://github.com/tenstorrent/vllm/blob/cd9e5b83/vllm/platforms/tpu.py#L32-L44)

### TPU Attention Backend

TPU uses Pallas (XLA's kernel language) for attention [vllm/platforms/tpu.py 51-65](https://github.com/tenstorrent/vllm/blob/cd9e5b83/vllm/platforms/tpu.py#L51-L65):

The Pallas backend computes its own optimal page size based on model config [vllm/platforms/tpu.py 140-142](https://github.com/tenstorrent/vllm/blob/cd9e5b83/vllm/platforms/tpu.py#L140-L142)

Sources: [vllm/platforms/tpu.py 51-65](https://github.com/tenstorrent/vllm/blob/cd9e5b83/vllm/platforms/tpu.py#L51-L65)[vllm/platforms/tpu.py 140-142](https://github.com/tenstorrent/vllm/blob/cd9e5b83/vllm/platforms/tpu.py#L140-L142)

### TPU Configuration

**TPU Configuration Details**[vllm/platforms/tpu.py 104-167](https://github.com/tenstorrent/vllm/blob/cd9e5b83/vllm/platforms/tpu.py#L104-L167):

1.   **Compilation**: DYNAMO_ONCE with OpenXLA backend [vllm/platforms/tpu.py 114-127](https://github.com/tenstorrent/vllm/blob/cd9e5b83/vllm/platforms/tpu.py#L114-L127)
2.   **Block Size**: Computed by PallasAttentionBackend [vllm/platforms/tpu.py 140-142](https://github.com/tenstorrent/vllm/blob/cd9e5b83/vllm/platforms/tpu.py#L140-L142)
3.   **Worker Class**: `"vllm.v1.worker.tpu_worker.TPUWorker"`[vllm/platforms/tpu.py 146-147](https://github.com/tenstorrent/vllm/blob/cd9e5b83/vllm/platforms/tpu.py#L146-L147)
4.   **Multimodal**: Requires `--disable_chunked_mm_input`[vllm/platforms/tpu.py 152-157](https://github.com/tenstorrent/vllm/blob/cd9e5b83/vllm/platforms/tpu.py#L152-L157)
5.   **MLA**: Disables chunked prefill and prefix caching [vllm/platforms/tpu.py 159-167](https://github.com/tenstorrent/vllm/blob/cd9e5b83/vllm/platforms/tpu.py#L159-L167)

Sources: [vllm/platforms/tpu.py 104-167](https://github.com/tenstorrent/vllm/blob/cd9e5b83/vllm/platforms/tpu.py#L104-L167)

### TPU-Specific Methods

**Memory Operations**: TPU implements compiled memory transfer ops [vllm/platforms/tpu.py 200-223](https://github.com/tenstorrent/vllm/blob/cd9e5b83/vllm/platforms/tpu.py#L200-L223):

**Limitations**:

*   `inference_mode()`: Returns `torch.no_grad()` instead (XLA incompatibility) [vllm/platforms/tpu.py 100-101](https://github.com/tenstorrent/vllm/blob/cd9e5b83/vllm/platforms/tpu.py#L100-L101)
*   `can_update_inplace()`: Returns `False` (XLA requires out-of-place updates) [vllm/platforms/tpu.py 92-93](https://github.com/tenstorrent/vllm/blob/cd9e5b83/vllm/platforms/tpu.py#L92-L93)
*   `use_sync_weight_loader()`: Returns `True`[vllm/platforms/tpu.py 226-227](https://github.com/tenstorrent/vllm/blob/cd9e5b83/vllm/platforms/tpu.py#L226-L227)

Sources: [vllm/platforms/tpu.py 88-227](https://github.com/tenstorrent/vllm/blob/cd9e5b83/vllm/platforms/tpu.py#L88-L227)

* * *

## Intel XPU Platform

The XPU platform supports Intel Data Center GPUs (Ponte Vecchio, Arctic Sound) using Intel Extension for PyTorch (IPEX).

### XPU Platform Configuration

**XPU Characteristics**[vllm/platforms/xpu.py 26-36](https://github.com/tenstorrent/vllm/blob/cd9e5b83/vllm/platforms/xpu.py#L26-L36):

*   **Device Type**: `"xpu"` with XPU dispatch
*   **Distributed Backend**: CCL (Collective Communications Library) or XCCL [vllm/platforms/xpu.py 34](https://github.com/tenstorrent/vllm/blob/cd9e5b83/vllm/platforms/xpu.py#L34-L34)
*   **Ray Integration**: Uses "GPU" as device key for Ray [vllm/platforms/xpu.py 33](https://github.com/tenstorrent/vllm/blob/cd9e5b83/vllm/platforms/xpu.py#L33-L33)
*   **Device Control**: `ZE_AFFINITY_MASK` environment variable [vllm/platforms/xpu.py 35](https://github.com/tenstorrent/vllm/blob/cd9e5b83/vllm/platforms/xpu.py#L35-L35)

Sources: [vllm/platforms/xpu.py 26-36](https://github.com/tenstorrent/vllm/blob/cd9e5b83/vllm/platforms/xpu.py#L26-L36)

### XPU Attention Backend Selection

**XPU Attention Backends**[vllm/platforms/xpu.py 38-63](https://github.com/tenstorrent/vllm/blob/cd9e5b83/vllm/platforms/xpu.py#L38-L63):

1.   **FlashAttention**: Default backend [vllm/platforms/xpu.py 62-63](https://github.com/tenstorrent/vllm/blob/cd9e5b83/vllm/platforms/xpu.py#L62-L63)
2.   **TritonAttention**: Alternative backend [vllm/platforms/xpu.py 49-53](https://github.com/tenstorrent/vllm/blob/cd9e5b83/vllm/platforms/xpu.py#L49-L53)
3.   **FP8 KV Cache**: Only supported with Triton backend [vllm/platforms/xpu.py 66-76](https://github.com/tenstorrent/vllm/blob/cd9e5b83/vllm/platforms/xpu.py#L66-L76)

Sources: [vllm/platforms/xpu.py 38-76](https://github.com/tenstorrent/vllm/blob/cd9e5b83/vllm/platforms/xpu.py#L38-L76)

### XPU Configuration and Limitations

**Configuration Updates**[vllm/platforms/xpu.py 112-172](https://github.com/tenstorrent/vllm/blob/cd9e5b83/vllm/platforms/xpu.py#L112-L172):

**XPU-Specific Limitations**:

*   No CUDA graph support [vllm/platforms/xpu.py 125-126](https://github.com/tenstorrent/vllm/blob/cd9e5b83/vllm/platforms/xpu.py#L125-L126)
*   Data Center GPU recommended for production [vllm/platforms/xpu.py 197-199](https://github.com/tenstorrent/vllm/blob/cd9e5b83/vllm/platforms/xpu.py#L197-L199)
*   Arc A770 has bfloat16 accuracy issues [vllm/platforms/xpu.py 210-218](https://github.com/tenstorrent/vllm/blob/cd9e5b83/vllm/platforms/xpu.py#L210-L218)
*   No static graph mode [vllm/platforms/xpu.py 178-179](https://github.com/tenstorrent/vllm/blob/cd9e5b83/vllm/platforms/xpu.py#L178-L179)

Sources: [vllm/platforms/xpu.py 112-172](https://github.com/tenstorrent/vllm/blob/cd9e5b83/vllm/platforms/xpu.py#L112-L172)[vllm/platforms/xpu.py 178-179](https://github.com/tenstorrent/vllm/blob/cd9e5b83/vllm/platforms/xpu.py#L178-L179)[vllm/platforms/xpu.py 197-218](https://github.com/tenstorrent/vllm/blob/cd9e5b83/vllm/platforms/xpu.py#L197-L218)

### XPU Memory Operations

**Memory Management**[vllm/platforms/xpu.py 186-191](https://github.com/tenstorrent/vllm/blob/cd9e5b83/vllm/platforms/xpu.py#L186-L191):

**Block Transfer Operations**[vllm/platforms/xpu.py 225-246](https://github.com/tenstorrent/vllm/blob/cd9e5b83/vllm/platforms/xpu.py#L225-L246):

Sources: [vllm/platforms/xpu.py 186-246](https://github.com/tenstorrent/vllm/blob/cd9e5b83/vllm/platforms/xpu.py#L186-L246)

* * *

## Platform Comparison Table

| Feature | CUDA | ROCm | CPU | TPU | XPU |
| --- | --- | --- | --- | --- | --- |
| **Device Detection** | NVML / torch.cuda | AMDSMI | N/A | libtpu | torch.xpu |
| **Distributed Backend** | NCCL | NCCL | Gloo / MP | Gloo | CCL / XCCL |
| **Default Attention** | FlashAttn / FlashInfer | AIter / Triton | Torch SDPA | Pallas | FlashAttn |
| **FP8 Support** | Yes (SM 8.9+) | Yes (gfx94+) | No | Yes | Yes (Triton only) |
| **FP8 Format** | OCP | FNUZ (MI300) / OCP | N/A | OCP | OCP |
| **MLA Support** | Yes (multiple backends) | Yes (AIter, Triton) | No | No | No |
| **Custom AllReduce** | Yes | Yes (MI300+) | No | No | No |
| **CUDA Graphs** | Yes | Yes | No | No | No |
| **Hybrid KV Cache** | Yes | Yes | Yes | No | Yes |
| **Default Block Size** | 16 | 16 | 16 (128 w/ IPEX) | Computed | 64 |
| **KV Cache Layout** | NHD / HND (Blackwell) | NHD | NHD | NHD | NHD (forced) |
| **Worker Class** | gpu_worker.Worker | gpu_worker.Worker | cpu_worker.CPUWorker | tpu_worker.TPUWorker | xpu_worker.XPUWorker |
| **Pin Memory** | Yes | Yes | No | No | Yes |
| **Compilation Backend** | Inductor | Inductor | Inductor / Eager | OpenXLA (forced) | Inductor |

Sources: All platform files [vllm/platforms/cuda.py](https://github.com/tenstorrent/vllm/blob/cd9e5b83/vllm/platforms/cuda.py)[vllm/platforms/rocm.py](https://github.com/tenstorrent/vllm/blob/cd9e5b83/vllm/platforms/rocm.py)[vllm/platforms/cpu.py](https://github.com/tenstorrent/vllm/blob/cd9e5b83/vllm/platforms/cpu.py)[vllm/platforms/tpu.py](https://github.com/tenstorrent/vllm/blob/cd9e5b83/vllm/platforms/tpu.py)[vllm/platforms/xpu.py](https://github.com/tenstorrent/vllm/blob/cd9e5b83/vllm/platforms/xpu.py)

* * *

## Platform-Specific Environment Variables

| Platform | Environment Variable | Purpose | Default |
| --- | --- | --- | --- |
| **CUDA** | `CUDA_VISIBLE_DEVICES` | Device visibility | All devices |
|  | `VLLM_ATTENTION_BACKEND` | Force attention backend | Auto-select |
|  | `CUDA_DEVICE_ORDER` | Device ordering | PCI_BUS_ID recommended |
| **ROCm** | `HIP_VISIBLE_DEVICES` / `CUDA_VISIBLE_DEVICES` | Device visibility | All devices |
|  | `VLLM_ROCM_USE_AITER` | Enable AIter backend | `0` |
|  | `VLLM_ROCM_USE_AITER_MHA` | Enable AIter MHA | `0` |
|  | `VLLM_ROCM_CUSTOM_PAGED_ATTN` | Enable custom paged attention | `1` |
|  | `VLLM_USE_TRITON_FLASH_ATTN` | Use Triton flash attention | `1` |
|  | `VLLM_USE_TRITON_AWQ` | Force Triton AWQ | Auto-enabled |
| **CPU** | `CPU_VISIBLE_MEMORY_NODES` | NUMA node visibility | All nodes |
|  | `VLLM_CPU_KVCACHE_SPACE` | KV cache space (GiB) | `4` |
|  | `VLLM_WORKER_MULTIPROC_METHOD` | Multiprocessing method | `spawn` |
|  | `OMP_NUM_THREADS` | OpenMP threads | `torch.get_num_threads()` |
| **TPU** | `TPU_VISIBLE_CHIPS` | Chip visibility | All chips |
|  | `TPU_CHIPS_PER_HOST_BOUNDS` | Chip topology | Auto |
|  | `VLLM_TPU_USING_PATHWAYS` | Use Pathways proxy | `0` |
| **XPU** | `ZE_AFFINITY_MASK` | Device affinity | All devices |
|  | `VLLM_WORKER_MULTIPROC_METHOD` | Multiprocessing method | `spawn` |

Sources: [vllm/platforms/cuda.py 63](https://github.com/tenstorrent/vllm/blob/cd9e5b83/vllm/platforms/cuda.py#L63-L63)[vllm/platforms/rocm.py 179](https://github.com/tenstorrent/vllm/blob/cd9e5b83/vllm/platforms/rocm.py#L179-L179)[vllm/platforms/cpu.py 74](https://github.com/tenstorrent/vllm/blob/cd9e5b83/vllm/platforms/cpu.py#L74-L74)[vllm/platforms/tpu.py 39](https://github.com/tenstorrent/vllm/blob/cd9e5b83/vllm/platforms/tpu.py#L39-L39)[vllm/platforms/xpu.py 35](https://github.com/tenstorrent/vllm/blob/cd9e5b83/vllm/platforms/xpu.py#L35-L35)

* * *

## Extending Platform Support

vLLM supports out-of-tree platform plugins through the plugin system. To add a new platform:

### Plugin Registration

### Platform Implementation

**Plugin Priority**: Out-of-tree plugins take precedence over builtin plugins [vllm/platforms/\\_\\_init\\_\\_.py 200-206](https://github.com/tenstorrent/vllm/blob/cd9e5b83/vllm/platforms///_//_init//_//_.py#L200-L206)

Sources: [vllm/platforms/\\_\\_init\\_\\_.py 180-221](https://github.com/tenstorrent/vllm/blob/cd9e5b83/vllm/platforms///_//_init//_//_.py#L180-L221)[vllm/platforms/interface.py 80-600](https://github.com/tenstorrent/vllm/blob/cd9e5b83/vllm/platforms/interface.py#L80-L600)

* * *

## Summary

vLLM's platform abstraction layer provides a unified interface for supporting diverse hardware backends. Key design principles:

1.   **Lazy Initialization**: Platforms are detected lazily on first access to `current_platform`[vllm/platforms/\\_\\_init\\_\\_.py 231-251](https://github.com/tenstorrent/vllm/blob/cd9e5b83/vllm/platforms///_//_init//_//_.py#L231-L251)
2.   **Hardware-Specific Optimization**: Each platform selects optimal attention backends and configurations
3.   **Extensibility**: Plugin system allows out-of-tree platform implementations
4.   **Configuration Validation**: Platforms validate and adjust `VllmConfig` for hardware compatibility
5.   **Device Capability Abstraction**: Unified interface for querying compute capabilities across hardware

For attention backend implementation details, see [Attention Mechanisms](https://deepwiki.com/tenstorrent/vllm/2.6-attention-mechanisms). For distributed execution specifics, see [Distributed Execution](https://deepwiki.com/tenstorrent/vllm/6.3-distributed-execution).

Sources: [vllm/platforms/\\_\\_init\\_\\_.py](https://github.com/tenstorrent/vllm/blob/cd9e5b83/vllm/platforms///_//_init//_//_.py)[vllm/platforms/interface.py](https://github.com/tenstorrent/vllm/blob/cd9e5b83/vllm/platforms/interface.py)[vllm/platforms/cuda.py](https://github.com/tenstorrent/vllm/blob/cd9e5b83/vllm/platforms/cuda.py)[vllm/platforms/rocm.py](https://github.com/tenstorrent/vllm/blob/cd9e5b83/vllm/platforms/rocm.py)[vllm/platforms/cpu.py](https://github.com/tenstorrent/vllm/blob/cd9e5b83/vllm/platforms/cpu.py)[vllm/platforms/tpu.py](https://github.com/tenstorrent/vllm/blob/cd9e5b83/vllm/platforms/tpu.py)[vllm/platforms/xpu.py](https://github.com/tenstorrent/vllm/blob/cd9e5b83/vllm/platforms/xpu.py)

Dismiss
Refresh this wiki

Enter email to refresh
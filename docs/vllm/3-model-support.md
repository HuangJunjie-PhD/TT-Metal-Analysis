---
project: vllm
github: https://github.com/tenstorrent/vllm
deepwiki: https://deepwiki.com/tenstorrent/vllm/3-model-support
---

# Model Support

Relevant source files
*   [.gitignore](https://github.com/tenstorrent/vllm/blob/cd9e5b83/.gitignore)
*   [README.md](https://github.com/tenstorrent/vllm/blob/cd9e5b83/README.md?plain=1)
*   [docs/features/multimodal_inputs.md](https://github.com/tenstorrent/vllm/blob/cd9e5b83/docs/features/multimodal_inputs.md?plain=1)
*   [docs/models/supported_models.md](https://github.com/tenstorrent/vllm/blob/cd9e5b83/docs/models/supported_models.md?plain=1)
*   [docs/usage/security.md](https://github.com/tenstorrent/vllm/blob/cd9e5b83/docs/usage/security.md?plain=1)
*   [examples/offline_inference/audio_language.py](https://github.com/tenstorrent/vllm/blob/cd9e5b83/examples/offline_inference/audio_language.py)
*   [examples/offline_inference/vision_language.py](https://github.com/tenstorrent/vllm/blob/cd9e5b83/examples/offline_inference/vision_language.py)
*   [examples/offline_inference/vision_language_multi_image.py](https://github.com/tenstorrent/vllm/blob/cd9e5b83/examples/offline_inference/vision_language_multi_image.py)
*   [tests/entrypoints/test_chat_utils.py](https://github.com/tenstorrent/vllm/blob/cd9e5b83/tests/entrypoints/test_chat_utils.py)
*   [tests/models/multimodal/generation/test_common.py](https://github.com/tenstorrent/vllm/blob/cd9e5b83/tests/models/multimodal/generation/test_common.py)
*   [tests/models/multimodal/generation/vlm_utils/model_utils.py](https://github.com/tenstorrent/vllm/blob/cd9e5b83/tests/models/multimodal/generation/vlm_utils/model_utils.py)
*   [tests/models/multimodal/processing/test_common.py](https://github.com/tenstorrent/vllm/blob/cd9e5b83/tests/models/multimodal/processing/test_common.py)
*   [tests/models/registry.py](https://github.com/tenstorrent/vllm/blob/cd9e5b83/tests/models/registry.py)
*   [tests/models/test_initialization.py](https://github.com/tenstorrent/vllm/blob/cd9e5b83/tests/models/test_initialization.py)
*   [vllm/benchmarks/__init__.py](https://github.com/tenstorrent/vllm/blob/cd9e5b83/vllm/benchmarks/__init__.py)
*   [vllm/connections.py](https://github.com/tenstorrent/vllm/blob/cd9e5b83/vllm/connections.py)
*   [vllm/entrypoints/chat_utils.py](https://github.com/tenstorrent/vllm/blob/cd9e5b83/vllm/entrypoints/chat_utils.py)
*   [vllm/model_executor/models/__init__.py](https://github.com/tenstorrent/vllm/blob/cd9e5b83/vllm/model_executor/models/__init__.py)
*   [vllm/model_executor/models/registry.py](https://github.com/tenstorrent/vllm/blob/cd9e5b83/vllm/model_executor/models/registry.py)
*   [vllm/transformers_utils/config.py](https://github.com/tenstorrent/vllm/blob/cd9e5b83/vllm/transformers_utils/config.py)
*   [vllm/transformers_utils/configs/__init__.py](https://github.com/tenstorrent/vllm/blob/cd9e5b83/vllm/transformers_utils/configs/__init__.py)

vLLM supports a diverse range of language model architectures for various tasks. This page provides an overview of vLLM's model support capabilities and serves as a navigation hub to detailed documentation for specific model types.

For a complete list of supported models with their specific configurations, see page [1.2](https://deepwiki.com/tenstorrent/vllm/1.2-supported-models) (Supported Models).

## Model Architecture in vLLM

### High-Level Model Integration

Title: Model Integration in vLLM System Architecture

vLLM's model support is built on a modular architecture where models are registered in a central registry and dynamically loaded based on their architecture type. The `_ModelRegistry` class in [vllm/model_executor/models/registry.py 560-655](https://github.com/tenstorrent/vllm/blob/cd9e5b83/vllm/model_executor/models/registry.py#L560-L655) manages model architecture mappings and provides methods to resolve and instantiate model implementations.

Sources: [vllm/model_executor/models/registry.py 560-655](https://github.com/tenstorrent/vllm/blob/cd9e5b83/vllm/model_executor/models/registry.py#L560-L655)[vllm/engine/llm_engine.py](https://github.com/tenstorrent/vllm/blob/cd9e5b83/vllm/engine/llm_engine.py)[README.md 61-96](https://github.com/tenstorrent/vllm/blob/cd9e5b83/README.md?plain=1#L61-L96)

### Model Categories

Title: Model Category Mappings in _ModelRegistry

The registry organizes models into distinct categories, each mapping architecture names to their implementation modules. For detailed information about each category:

*   **Text Generation Models**: See page [3.2](https://deepwiki.com/tenstorrent/vllm/3.2-text-generation-models)
*   **Multimodal Models**: See page [3.3](https://deepwiki.com/tenstorrent/vllm/3.3-multimodal-models)
*   **Quantization Support**: See page [3.4](https://deepwiki.com/tenstorrent/vllm/3.4-quantization-strategies)
*   **Mixture of Experts**: See page [3.5](https://deepwiki.com/tenstorrent/vllm/3.5-mixture-of-experts)

For registry implementation details and model loading mechanisms, see page [3.1](https://deepwiki.com/tenstorrent/vllm/3.1-model-registry-and-loading).

Sources: [vllm/model_executor/models/registry.py 44-329](https://github.com/tenstorrent/vllm/blob/cd9e5b83/vllm/model_executor/models/registry.py#L44-L329)[vllm/model_executor/models/__init__.py 1-34](https://github.com/tenstorrent/vllm/blob/cd9e5b83/vllm/model_executor/models/__init__.py#L1-L34)

## Model Support Mechanisms

vLLM provides three mechanisms for supporting language models, allowing it to run a wide variety of architectures:

Title: Model Support Decision Flow

### 1. Native vLLM Implementations

Models with native implementations in [vllm/model_executor/models/](https://github.com/tenstorrent/vllm/blob/cd9e5b83/vllm/model_executor/models/) are optimized for high-throughput serving with features like:

*   Custom CUDA/HIP kernels for attention
*   Optimized weight loading and sharding
*   Native support for tensor/pipeline parallelism
*   Integration with vLLM's memory management

Example architectures with native support include `LlamaForCausalLM`, `MixtralForCausalLM`, `Qwen2ForCausalLM`, and many others. See [vllm/model_executor/models/registry.py 44-156](https://github.com/tenstorrent/vllm/blob/cd9e5b83/vllm/model_executor/models/registry.py#L44-L156) for the complete list.

### 2. Transformers Backend

For models without native implementations, vLLM uses the Transformers backend via `TransformersForCausalLM`, `TransformersForMultimodalLM`, and related wrapper classes in [vllm/model_executor/models/transformers.py](https://github.com/tenstorrent/vllm/blob/cd9e5b83/vllm/model_executor/models/transformers.py) This enables:

*   Running models using their original HuggingFace Transformers code
*   Support for quantization, LoRA, and other vLLM features
*   Performance within 5% of native implementations

Models using the Transformers backend are listed in [vllm/model_executor/models/registry.py 302-318](https://github.com/tenstorrent/vllm/blob/cd9e5b83/vllm/model_executor/models/registry.py#L302-L318)

### 3. Remote Code Support

Models can use custom remote code from HuggingFace Hub when `trust_remote_code=True` is set. This allows:

*   Running models before they're officially supported by Transformers or vLLM
*   Using model-specific custom implementations
*   Supporting proprietary or experimental architectures

For Transformers backend compatibility with remote code, the model must set `_supports_attention_backend = True` in its implementation.

Sources: [docs/models/supported_models.md 16-126](https://github.com/tenstorrent/vllm/blob/cd9e5b83/docs/models/supported_models.md?plain=1#L16-L126)[vllm/model_executor/models/registry.py 302-329](https://github.com/tenstorrent/vllm/blob/cd9e5b83/vllm/model_executor/models/registry.py#L302-L329)[vllm/transformers_utils/config.py 103-161](https://github.com/tenstorrent/vllm/blob/cd9e5b83/vllm/transformers_utils/config.py#L103-L161)

## Model Categories and Capabilities

### Text Generation Models

Decoder-only causal language models for text completion, chat, and instruction following. These include architectures like:

*   `LlamaForCausalLM` - LLaMA family, Mistral, Yi, and derivatives
*   `Qwen2ForCausalLM` - Qwen family models
*   `MixtralForCausalLM` - Mixture of Experts models
*   `Gemma2ForCausalLM`, `Phi3ForCausalLM`, `GPT2LMHeadModel`, and many others

**Supported Features:**

*   LoRA adapters for efficient fine-tuning
*   Pipeline parallelism (PP) for large models
*   Quantization (FP8, INT8, INT4, AWQ, GPTQ)
*   Speculative decoding for faster inference

For comprehensive details on text generation models, see page [3.2](https://deepwiki.com/tenstorrent/vllm/3.2-text-generation-models).

Sources: [vllm/model_executor/models/registry.py 44-156](https://github.com/tenstorrent/vllm/blob/cd9e5b83/vllm/model_executor/models/registry.py#L44-L156)[docs/models/supported_models.md 320-425](https://github.com/tenstorrent/vllm/blob/cd9e5b83/docs/models/supported_models.md?plain=1#L320-L425)[README.md 89-92](https://github.com/tenstorrent/vllm/blob/cd9e5b83/README.md?plain=1#L89-L92)

### Multimodal Models

Models that process combinations of text, images, video, and audio inputs:

Title: Multimodal Model Examples and Modalities

**Key Capabilities:**

*   Multi-image support (up to model-specific limits)
*   Video frame sampling and processing
*   Audio feature extraction
*   Interleaved multi-modal inputs
*   UUID-based caching for efficient reuse

For detailed information on multimodal support, see page [3.3](https://deepwiki.com/tenstorrent/vllm/3.3-multimodal-models).

Sources: [vllm/model_executor/models/registry.py 215-280](https://github.com/tenstorrent/vllm/blob/cd9e5b83/vllm/model_executor/models/registry.py#L215-L280)[examples/offline_inference/vision_language.py 1-1200](https://github.com/tenstorrent/vllm/blob/cd9e5b83/examples/offline_inference/vision_language.py#L1-L1200)[vllm/entrypoints/chat_utils.py 49-66](https://github.com/tenstorrent/vllm/blob/cd9e5b83/vllm/entrypoints/chat_utils.py#L49-L66)

### Embedding and Classification Models

Models for generating embeddings and performing classification tasks:

*   **Embedding**: `BertModel`, `RobertaModel`, `GteNewModel`, `NomicBertModel`
*   **Sequence Classification**: `BertForSequenceClassification`, `RobertaForSequenceClassification`
*   **Reward Modeling**: `InternLM2ForRewardModel`, `Qwen2ForRewardModel`

These models use the `runner="pooling"` mode instead of the default `runner="generate"`.

Sources: [vllm/model_executor/models/registry.py 158-198](https://github.com/tenstorrent/vllm/blob/cd9e5b83/vllm/model_executor/models/registry.py#L158-L198)[docs/models/supported_models.md 587-750](https://github.com/tenstorrent/vllm/blob/cd9e5b83/docs/models/supported_models.md?plain=1#L587-L750)

### Specialized Model Types

**Quantization**: vLLM supports multiple quantization methods to reduce memory usage and improve throughput. See page [3.4](https://deepwiki.com/tenstorrent/vllm/3.4-quantization-strategies) for details on:

*   FP8 quantization (W8A8, W8A16)
*   INT8/INT4 quantization
*   AWQ, GPTQ, Marlin formats
*   Per-layer and per-channel quantization

**Mixture of Experts (MoE)**: Sparse models with expert routing for efficient scaling. See page [3.5](https://deepwiki.com/tenstorrent/vllm/3.5-mixture-of-experts) for:

*   `MixtralForCausalLM`, `Qwen2MoeForCausalLM`, `DeepseekV2ForCausalLM`
*   Expert parallelism and load balancing
*   FusedMoE kernel optimizations

**Speculative Decoding**: Models designed for draft-target speculative decoding:

*   `MedusaModel`, `EagleLlamaForCausalLM`, `DeepSeekMTPModel`

Sources: [vllm/model_executor/models/registry.py 282-300](https://github.com/tenstorrent/vllm/blob/cd9e5b83/vllm/model_executor/models/registry.py#L282-L300)[README.md 73-76](https://github.com/tenstorrent/vllm/blob/cd9e5b83/README.md?plain=1#L73-L76)

## Model Loading Process

When loading a model in vLLM, the system follows a process to determine the correct implementation to use:

The model registry normalizes the list of architectures to try, prioritizing native implementations over the Transformers backend.

Sources: [vllm/model_executor/models/registry.py 418-473](https://github.com/tenstorrent/vllm/blob/cd9e5b83/vllm/model_executor/models/registry.py#L418-L473)

### Weight Loading

Each model implementation includes a `load_weights` method that handles loading weights from the pre-trained model files. This method:

1.   Maps weight names from the source format to the vLLM format
2.   Handles tensor parallel sharding of weights
3.   Applies quantization if needed

For example, LLaMA's weight loading handles merged QKV projection weights, gate and up projection weights, and other model-specific weight patterns.

Sources: [vllm/model_executor/models/llama.py 371-435](https://github.com/tenstorrent/vllm/blob/cd9e5b83/vllm/model_executor/models/llama.py#L371-L435)

## Adding New Models

vLLM supports adding new model architectures through its model registry system:

To add a new model, you need to:

1.   Create a new model implementation file in `vllm/model_executor/models/`
2.   Implement the model-specific layers and main model class
3.   Register the model in the model registry
4.   Add test examples to verify the model works correctly

The model implementation should implement any necessary interfaces like `SupportsLoRA` or `SupportsPP` if the model should support those features.

Sources: [vllm/model_executor/models/registry.py 363-404](https://github.com/tenstorrent/vllm/blob/cd9e5b83/vllm/model_executor/models/registry.py#L363-L404)[docs/source/models/supported_models.md 1179-1200](https://github.com/tenstorrent/vllm/blob/cd9e5b83/docs/source/models/supported_models.md?plain=1#L1179-L1200)

## Model Support Policy

vLLM follows a community-driven approach to model support with these principles:

1.   **Community Contributions**: Support for new models primarily comes from community PRs
2.   **Best-Effort Consistency**: The focus is on producing sensible results rather than exact consistency with transformers
3.   **Issue Resolution**: Users report bugs and propose fixes through PRs
4.   **Selective Focus**: More attention is given to commonly used models

There are different levels of testing for models:

1.   **Strict Consistency**: Comparing output with HuggingFace Transformers under greedy decoding
2.   **Output Comparison**: Checking if the model produces reasonable outputs
3.   **Basic Functionality**: Ensuring the model loads and runs without errors

Sources: [docs/source/models/supported_models.md 1179-1200](https://github.com/tenstorrent/vllm/blob/cd9e5b83/docs/source/models/supported_models.md?plain=1#L1179-L1200)

## Conclusion

vLLM's model support system provides a flexible and extensible framework for supporting a wide range of language models. The model registry serves as the central component that manages model architectures and their implementations. vLLM supports text generation models, embedding models, and multimodal models, with native implementations for popular architectures and fallback mechanisms for other models. This design allows vLLM to continuously expand its support for new models while maintaining high performance.

Dismiss
Refresh this wiki

Enter email to refresh
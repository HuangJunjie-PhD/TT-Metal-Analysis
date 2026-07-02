---
project: tt-forge-onnx
github: https://github.com/tenstorrent/tt-forge-onnx
deepwiki: https://deepwiki.com/tenstorrent/tt-forge-onnx/7-model-support
---

# Model Support

Relevant source files
*   [forge/test/models/onnx/multimodal/clip/test_clip_onnx.py](https://github.com/tenstorrent/tt-forge-onnx/blob/e519819c/forge/test/models/onnx/multimodal/clip/test_clip_onnx.py)
*   [forge/test/models/onnx/multimodal/llava/test_llava_onnx.py](https://github.com/tenstorrent/tt-forge-onnx/blob/e519819c/forge/test/models/onnx/multimodal/llava/test_llava_onnx.py)
*   [forge/test/models/onnx/text/nanogpt/test_nanogpt_onnx.py](https://github.com/tenstorrent/tt-forge-onnx/blob/e519819c/forge/test/models/onnx/text/nanogpt/test_nanogpt_onnx.py)
*   [forge/test/models/onnx/text/t5/test_t5_onnx.py](https://github.com/tenstorrent/tt-forge-onnx/blob/e519819c/forge/test/models/onnx/text/t5/test_t5_onnx.py)
*   [forge/test/models/onnx/timeseries/test_nbeats_onnx.py](https://github.com/tenstorrent/tt-forge-onnx/blob/e519819c/forge/test/models/onnx/timeseries/test_nbeats_onnx.py)
*   [forge/test/models/pytorch/multimodal/llava/test_llava.py](https://github.com/tenstorrent/tt-forge-onnx/blob/e519819c/forge/test/models/pytorch/multimodal/llava/test_llava.py)
*   [forge/test/models/pytorch/multimodal/phi3/test_phi3_5_vision.py](https://github.com/tenstorrent/tt-forge-onnx/blob/e519819c/forge/test/models/pytorch/multimodal/phi3/test_phi3_5_vision.py)
*   [forge/test/models/pytorch/multimodal/stable_diffusion/test_stable_diffusion_v35.py](https://github.com/tenstorrent/tt-forge-onnx/blob/e519819c/forge/test/models/pytorch/multimodal/stable_diffusion/test_stable_diffusion_v35.py)
*   [forge/test/models/pytorch/text/falcon/test_falcon.py](https://github.com/tenstorrent/tt-forge-onnx/blob/e519819c/forge/test/models/pytorch/text/falcon/test_falcon.py)
*   [forge/test/models/pytorch/text/gemma/test_gemma_2b.py](https://github.com/tenstorrent/tt-forge-onnx/blob/e519819c/forge/test/models/pytorch/text/gemma/test_gemma_2b.py)
*   [forge/test/models/pytorch/text/gemma/test_gemma_v1.py](https://github.com/tenstorrent/tt-forge-onnx/blob/e519819c/forge/test/models/pytorch/text/gemma/test_gemma_v1.py)
*   [forge/test/models/pytorch/text/llama/test_llama3.py](https://github.com/tenstorrent/tt-forge-onnx/blob/e519819c/forge/test/models/pytorch/text/llama/test_llama3.py)
*   [forge/test/models/pytorch/text/mistral/test_mistral.py](https://github.com/tenstorrent/tt-forge-onnx/blob/e519819c/forge/test/models/pytorch/text/mistral/test_mistral.py)
*   [forge/test/models/pytorch/text/phi2/test_phi2.py](https://github.com/tenstorrent/tt-forge-onnx/blob/e519819c/forge/test/models/pytorch/text/phi2/test_phi2.py)
*   [forge/test/models/pytorch/text/phi3/test_phi3.py](https://github.com/tenstorrent/tt-forge-onnx/blob/e519819c/forge/test/models/pytorch/text/phi3/test_phi3.py)
*   [forge/test/models/pytorch/text/phi3/test_phi3_5.py](https://github.com/tenstorrent/tt-forge-onnx/blob/e519819c/forge/test/models/pytorch/text/phi3/test_phi3_5.py)
*   [forge/test/models/pytorch/text/phi4/test_phi4.py](https://github.com/tenstorrent/tt-forge-onnx/blob/e519819c/forge/test/models/pytorch/text/phi4/test_phi4.py)
*   [forge/test/models/pytorch/text/qwen/test_qwen_v2.py](https://github.com/tenstorrent/tt-forge-onnx/blob/e519819c/forge/test/models/pytorch/text/qwen/test_qwen_v2.py)
*   [forge/test/models/pytorch/timeseries/nbeats/test_nbeats.py](https://github.com/tenstorrent/tt-forge-onnx/blob/e519819c/forge/test/models/pytorch/timeseries/nbeats/test_nbeats.py)
*   [forge/test/models/pytorch/vision/resnet/test_resnet.py](https://github.com/tenstorrent/tt-forge-onnx/blob/e519819c/forge/test/models/pytorch/vision/resnet/test_resnet.py)
*   [forge/test/models/pytorch/vision/yolo/test_yolo_v5.py](https://github.com/tenstorrent/tt-forge-onnx/blob/e519819c/forge/test/models/pytorch/vision/yolo/test_yolo_v5.py)

This page documents the 100+ model architectures supported by tt-forge-onnx, organized by domain. It describes the `ModelLoader` abstraction used consistently across all models, the `third_party/tt_forge_models` submodule containing model definitions, and a comprehensive catalog of models with their testing status.

The tt-forge-onnx system supports models from multiple frameworks (PyTorch, ONNX, TensorFlow, JAX, PaddlePaddle) across three primary domains: vision, text/LLM, and multimodal. Each model follows a standardized testing pattern using `ModelLoader`, model wrappers, and the `forge.compile` → `verify` workflow.

See also:

*   [Vision Models](https://deepwiki.com/tenstorrent/tt-forge-onnx/7.1-vision-models) - CNN architectures, object detection, segmentation
*   [Text and LLM Models](https://deepwiki.com/tenstorrent/tt-forge-onnx/7.2-text-and-llm-models) - Transformer language models, LLMs, sequence tasks
*   [Multimodal Models](https://deepwiki.com/tenstorrent/tt-forge-onnx/7.3-multimodal-models) - Vision-language models, image generation
*   [Llama Inference and Training](https://deepwiki.com/tenstorrent/tt-forge-onnx/7.4-llama-inference-and-training) - Detailed Llama implementation patterns
*   [Core Compilation System](https://deepwiki.com/tenstorrent/tt-forge-onnx/3-core-compilation-system) - Compilation mechanics
*   [Verification System](https://deepwiki.com/tenstorrent/tt-forge-onnx/3.4-verification-system) - Numerical validation workflow

* * *

## Model Repository Structure

Model definitions, weight loading, and preprocessing logic are separated from test code:

*   **Model implementations**: `third_party/tt_forge_models` (Git submodule)
*   **Test files**: `forge/test/models/` (imports from submodule, orchestrates compilation and verification)

This separation allows model code to be maintained independently while test files focus on integration with the Forge compilation pipeline.

**Test directory structure (maps to physical file paths):**

**Model implementation structure (third_party submodule):**

Each model directory exports a `ModelLoader` class and (usually) a `ModelVariant` enum. Test files import these and instantiate loaders with specific variants.

Sources: [forge/test/models/pytorch/vision/resnet/test_resnet.py 26](https://github.com/tenstorrent/tt-forge-onnx/blob/e519819c/forge/test/models/pytorch/vision/resnet/test_resnet.py#L26-L26)[forge/test/models/pytorch/text/falcon/test_falcon.py 6](https://github.com/tenstorrent/tt-forge-onnx/blob/e519819c/forge/test/models/pytorch/text/falcon/test_falcon.py#L6-L6)[forge/test/models/pytorch/text/llama/test_llama3.py 7-18](https://github.com/tenstorrent/tt-forge-onnx/blob/e519819c/forge/test/models/pytorch/text/llama/test_llama3.py#L7-L18)[forge/test/models/pytorch/multimodal/stable_diffusion/test_stable_diffusion_v35.py 7-10](https://github.com/tenstorrent/tt-forge-onnx/blob/e519819c/forge/test/models/pytorch/multimodal/stable_diffusion/test_stable_diffusion_v35.py#L7-L10)

* * *

## The ModelLoader Pattern

Every supported model exposes the same interface through its `ModelLoader` class, ensuring uniform test structure across all domains and frameworks.

**Standard model test workflow (code entities in execution order):**

Sources: [forge/test/models/pytorch/vision/resnet/test_resnet.py 30-76](https://github.com/tenstorrent/tt-forge-onnx/blob/e519819c/forge/test/models/pytorch/vision/resnet/test_resnet.py#L30-L76)[forge/test/models/pytorch/text/llama/test_llama3.py 74-123](https://github.com/tenstorrent/tt-forge-onnx/blob/e519819c/forge/test/models/pytorch/text/llama/test_llama3.py#L74-L123)[forge/test/models/pytorch/vision/yolo/test_yolo_v5.py 33-82](https://github.com/tenstorrent/tt-forge-onnx/blob/e519819c/forge/test/models/pytorch/vision/yolo/test_yolo_v5.py#L33-L82)

### ModelLoader Interface

All `ModelLoader` classes implement three core methods:

| Method | Return Type | Purpose | Common Arguments | Example Usage |
| --- | --- | --- | --- | --- |
| `load_model(dtype_override=...)` | `torch.nn.Module` or model object | Downloads weights (if needed) and instantiates model | `dtype_override=torch.bfloat16` | `model = loader.load_model(dtype_override=torch.bfloat16)` |
| `load_inputs(dtype_override=..., **kwargs)` | `list` of tensors or `dict` | Generates or loads sample inputs for model | `dtype_override`, `input_size` (vision), `max_new_tokens` (text), `prompt` (text) | `inputs = loader.load_inputs(dtype_override=torch.bfloat16, input_size=640)` |
| `post_process(...)` | None (side effect: print/display) | Decodes model outputs and displays results | Model-specific (raw outputs, processor, etc.) | `loader.post_process(co_out)` |

**ModelVariant Enum Pattern:**

Each model exports a `ModelVariant` enum listing available checkpoints:

Import pattern (maps code symbols to file locations):

Sources: [forge/test/models/pytorch/vision/yolo/test_yolo_v5.py 20-62](https://github.com/tenstorrent/tt-forge-onnx/blob/e519819c/forge/test/models/pytorch/vision/yolo/test_yolo_v5.py#L20-L62)[forge/test/models/pytorch/text/llama/test_llama3.py 7-113](https://github.com/tenstorrent/tt-forge-onnx/blob/e519819c/forge/test/models/pytorch/text/llama/test_llama3.py#L7-L113)[forge/test/models/pytorch/text/phi2/test_phi2.py 5-23](https://github.com/tenstorrent/tt-forge-onnx/blob/e519819c/forge/test/models/pytorch/text/phi2/test_phi2.py#L5-L23)[forge/test/models/pytorch/text/falcon/test_falcon.py 6-42](https://github.com/tenstorrent/tt-forge-onnx/blob/e519819c/forge/test/models/pytorch/text/falcon/test_falcon.py#L6-L42)

* * *

## Metadata Registration with `record_model_properties`

Every test function calls `record_model_properties` before model loading to register structured metadata for CI tracking, failure analysis, and performance dashboards. This function is imported from `forge.forge_property_utils`.

**Metadata field definitions:**

| Field | Type | Required | Purpose | Example Values |
| --- | --- | --- | --- | --- |
| `framework` | `Framework` enum | Yes | Source framework of the model | `Framework.PYTORCH`, `Framework.ONNX`, `Framework.TENSORFLOW` |
| `model` | `ModelArch` enum | Yes | High-level model architecture family | `ModelArch.RESNET`, `ModelArch.LLAMA3`, `ModelArch.YOLOV5` |
| `variant` | `ModelVariant` enum or `str` | Yes | Specific model variant/checkpoint | `ModelVariant.YOLOV5N`, `"meta-llama/Llama-3.2-1B"`, `"Qwen/Qwen2-7B"` |
| `task` | `Task` enum | Yes | ML task category | `Task.CV_IMAGE_CLASSIFICATION`, `Task.NLP_CAUSAL_LM`, `Task.CONDITIONAL_GENERATION` |
| `source` | `Source` enum | Yes | Model weight source | `Source.HUGGINGFACE`, `Source.TORCHVISION`, `Source.TORCH_HUB`, `Source.TIMM`, `Source.GITHUB` |
| `group` | `ModelGroup` enum | No | Test grouping (RED = high priority) | `ModelGroup.RED`, `ModelGroup.GENERALITY` |
| `priority` | `ModelPriority` enum | No | Execution priority | `ModelPriority.P1`, `ModelPriority.P2` |
| `suffix` | `str` | No | Additional identifier | `"320x320"`, `"text"`, `"640x640"` |

**Code example mapping:**

The returned `module_name` (a formatted string) is passed to `forge.compile` for identification in compiled artifacts and logs.

**Property flow (from test file to compilation metadata):**

Sources: [forge/test/models/pytorch/vision/resnet/test_resnet.py 30-45](https://github.com/tenstorrent/tt-forge-onnx/blob/e519819c/forge/test/models/pytorch/vision/resnet/test_resnet.py#L30-L45)[forge/test/models/pytorch/text/falcon/test_falcon.py 26-36](https://github.com/tenstorrent/tt-forge-onnx/blob/e519819c/forge/test/models/pytorch/text/falcon/test_falcon.py#L26-L36)[forge/test/models/pytorch/text/llama/test_llama3.py 90-99](https://github.com/tenstorrent/tt-forge-onnx/blob/e519819c/forge/test/models/pytorch/text/llama/test_llama3.py#L90-L99)[forge/test/models/pytorch/vision/yolo/test_yolo_v5.py 39-47](https://github.com/tenstorrent/tt-forge-onnx/blob/e519819c/forge/test/models/pytorch/vision/yolo/test_yolo_v5.py#L39-L47)

* * *

## Model Catalog

The following sections catalog all models present in the test suite, organized by domain. Each entry maps the model family to its `ModelArch` enum constant and test file location.

**High-level model distribution across domains:**

**ModelArch enum to test file mapping:**

Sources: [forge/test/models/pytorch/vision/resnet/test_resnet.py 1-30](https://github.com/tenstorrent/tt-forge-onnx/blob/e519819c/forge/test/models/pytorch/vision/resnet/test_resnet.py#L1-L30)[forge/test/models/pytorch/text/falcon/test_falcon.py 1-25](https://github.com/tenstorrent/tt-forge-onnx/blob/e519819c/forge/test/models/pytorch/text/falcon/test_falcon.py#L1-L25)[forge/test/models/pytorch/multimodal/stable_diffusion/test_stable_diffusion_v35.py 1-30](https://github.com/tenstorrent/tt-forge-onnx/blob/e519819c/forge/test/models/pytorch/multimodal/stable_diffusion/test_stable_diffusion_v35.py#L1-L30)[forge/test/models/pytorch/text/llama/test_llama3.py 1-30](https://github.com/tenstorrent/tt-forge-onnx/blob/e519819c/forge/test/models/pytorch/text/llama/test_llama3.py#L1-L30)

### Vision Models Summary

Vision models cover image classification, object detection, and segmentation tasks. Most use `DataFormat.Float16_b` for compilation.

| Model | `ModelArch` Constant | Variants | Input Resolutions | Source | Framework | Test Status |
| --- | --- | --- | --- | --- | --- | --- |
| ResNet | `RESNET` | 18, 34, 50, 101, 152 | 224×224 | `TORCHVISION`, `HUGGINGFACE`, `TIMM` | PyTorch | ✓ Pass (all variants) |
| YOLOv5 | `YOLOV5` | N, S, M, L, X | 320×320, 480×480, 640×640, 1280×1280 | `TORCH_HUB` | PyTorch | ✓ Pass (all variants/resolutions) |

**YOLOv5 resolution testing pattern:**

See [Vision Models](https://deepwiki.com/tenstorrent/tt-forge-onnx/7.1-vision-models) for complete coverage including EfficientNet, MobileNet, ViT, DETR, SegFormer, and ONNX vision models.

Sources: [forge/test/models/pytorch/vision/resnet/test_resnet.py 119-170](https://github.com/tenstorrent/tt-forge-onnx/blob/e519819c/forge/test/models/pytorch/vision/resnet/test_resnet.py#L119-L170)[forge/test/models/pytorch/vision/yolo/test_yolo_v5.py 22-194](https://github.com/tenstorrent/tt-forge-onnx/blob/e519819c/forge/test/models/pytorch/vision/yolo/test_yolo_v5.py#L22-L194)

### Text and LLM Models Summary

Text models primarily perform causal language modeling, sequence classification, and token classification. Most large models (7B+ parameters) require multi-chip support.

| Model Family | `ModelArch` Constants | Notable Variants | Supported Tasks | Test Status | Multi-Chip Required |
| --- | --- | --- | --- | --- | --- |
| Falcon | `FALCON`, `FALCON3` | 1B ✓, 3B, 7B, 10B, Mamba-7B | `NLP_CAUSAL_LM` | 1B passes; others xfail | Yes (3B+) |
| Llama 3 | `LLAMA3` | 3.2-1B ✓, 3.2-1B-Instruct ✓, 3.8B, 3.1-8B, 3.2-3B | `NLP_CAUSAL_LM`, `NLP_SEQUENCE_CLASSIFICATION` | 1B variants pass; others xfail | Yes (3B+) |
| Gemma | `GEMMA` | 2B, 2-2B-IT, 2-9B-IT, 2-27B-IT, 1.1-2B-IT, 1.1-7B-IT | `NLP_CAUSAL_LM`, `NLP_QA` | All xfail | Yes (all) |
| Mistral | `MISTRAL` | 7B, 7B-Instruct-v0.3, Nemo, Small-24B, Large | `NLP_CAUSAL_LM` | All xfail | Yes (all) |
| Qwen2 | `QWENV2` | 7B | `NLP_TOKEN_CLASSIFICATION` | xfail | Yes |
| Phi2 | `PHI2` | phi2, phi2-pytdml | `NLP_CAUSAL_LM`, `NLP_TOKEN_CLASSIFICATION`, `NLP_SEQUENCE_CLASSIFICATION` | All xfail | Yes |
| Phi3 | `PHI3`, `PHI3_5`, `PHI3_5_MOE` | Mini-4K, Mini-128K, Mini-Instruct, MoE-Instruct | `NLP_CAUSAL_LM`, `NLP_TOKEN_CLASSIFICATION`, `NLP_SEQUENCE_CLASSIFICATION` | All xfail | Yes |
| Phi4 | `PHI4` | microsoft/phi-4 | `NLP_CAUSAL_LM`, `NLP_TOKEN_CLASSIFICATION`, `NLP_SEQUENCE_CLASSIFICATION` | All xfail | Yes |
| T5 | `T5` | t5-small | `NLP_CAUSAL_LM` | ✓ Pass (ONNX) | No |
| NanoGPT | `NANOGPT` | FinancialSupport/NanoGPT | `NLP_CAUSAL_LM` | xfail (ONNX) | No |
| NBEATS | `NBEATS` | SeasonalityBasis ✓, GenericBasis, TrendBasis | `TIME_SERIES_FORECASTING` | SeasonalityBasis passes (ONNX); others xfail | No |

**Task distribution for Llama3 variants:**

**TextModelWrapper usage pattern for LLM models:**

See [Text and LLM Models](https://deepwiki.com/tenstorrent/tt-forge-onnx/7.2-text-and-llm-models) for detailed implementation notes and [Llama Inference and Training](https://deepwiki.com/tenstorrent/tt-forge-onnx/7.4-llama-inference-and-training) for Llama-specific patterns.

Sources: [forge/test/models/pytorch/text/falcon/test_falcon.py 63-140](https://github.com/tenstorrent/tt-forge-onnx/blob/e519819c/forge/test/models/pytorch/text/falcon/test_falcon.py#L63-L140)[forge/test/models/pytorch/text/llama/test_llama3.py 34-157](https://github.com/tenstorrent/tt-forge-onnx/blob/e519819c/forge/test/models/pytorch/text/llama/test_llama3.py#L34-L157)[forge/test/models/pytorch/text/phi2/test_phi2.py 38-84](https://github.com/tenstorrent/tt-forge-onnx/blob/e519819c/forge/test/models/pytorch/text/phi2/test_phi2.py#L38-L84)[forge/test/models/pytorch/text/phi3/test_phi3.py 36-131](https://github.com/tenstorrent/tt-forge-onnx/blob/e519819c/forge/test/models/pytorch/text/phi3/test_phi3.py#L36-L131)[forge/test/models/pytorch/text/phi4/test_phi4.py 29-134](https://github.com/tenstorrent/tt-forge-onnx/blob/e519819c/forge/test/models/pytorch/text/phi4/test_phi4.py#L29-L134)[forge/test/models/pytorch/timeseries/nbeats/test_nbeats.py 1-89](https://github.com/tenstorrent/tt-forge-onnx/blob/e519819c/forge/test/models/pytorch/timeseries/nbeats/test_nbeats.py#L1-L89)[forge/test/models/onnx/text/t5/test_t5_onnx.py 31-74](https://github.com/tenstorrent/tt-forge-onnx/blob/e519819c/forge/test/models/onnx/text/t5/test_t5_onnx.py#L31-L74)

### Multimodal Models Summary

Multimodal models combine vision and language components. Most require custom wrappers to handle multi-input signatures.

| Model | `ModelArch` Constant | Variants | Primary Task | Inputs | Test Status | Multi-Chip Required |
| --- | --- | --- | --- | --- | --- | --- |
| LLaVA | `LLAVA` | 1.5-7B | `CONDITIONAL_GENERATION` | input_ids, attention_mask, pixel_values | xfail | Yes |
| Stable Diffusion 3.5 | `STABLEDIFFUSION` | Medium, Large, Large-Turbo | `CONDITIONAL_GENERATION` | latent_model_input, timestep, prompt_embeds, pooled_prompt_embeds | xfail | Yes |
| CLIP | `CLIP` | openai/clip-vit-base-patch32 (text encoder) | `NLP_CAUSAL_LM` | input_ids, attention_mask | xfail (ONNX) | No |
| Phi3.5 Vision | `PHI35VISION` | Instruct | `MM_CAUSAL_LM` | input_ids, attention_mask, pixel_values, image_sizes | xfail | Yes |

**Multimodal wrapper pattern (normalizing input signatures):**

See [Multimodal Models](https://deepwiki.com/tenstorrent/tt-forge-onnx/7.3-multimodal-models) for additional models including ViLT, OFT, and PaddleOCR.

Sources: [forge/test/models/pytorch/multimodal/llava/test_llava.py 26-73](https://github.com/tenstorrent/tt-forge-onnx/blob/e519819c/forge/test/models/pytorch/multimodal/llava/test_llava.py#L26-L73)[forge/test/models/pytorch/multimodal/stable_diffusion/test_stable_diffusion_v35.py 27-98](https://github.com/tenstorrent/tt-forge-onnx/blob/e519819c/forge/test/models/pytorch/multimodal/stable_diffusion/test_stable_diffusion_v35.py#L27-L98)[forge/test/models/onnx/multimodal/clip/test_clip_onnx.py 1-67](https://github.com/tenstorrent/tt-forge-onnx/blob/e519819c/forge/test/models/onnx/multimodal/clip/test_clip_onnx.py#L1-L67)[forge/test/models/pytorch/multimodal/phi3/test_phi3_5_vision.py 25-75](https://github.com/tenstorrent/tt-forge-onnx/blob/e519819c/forge/test/models/pytorch/multimodal/phi3/test_phi3_5_vision.py#L25-L75)

* * *

* * *

## PyTorch vs ONNX Testing Paths

Many models are tested in both PyTorch and ONNX frameworks. The ONNX path adds an intermediate export step using `torch.onnx.export` and wraps the model with `forge.OnnxModule`.

**Workflow comparison (code entities at each stage):**

**Key differences:**

| Aspect | PyTorch Path | ONNX Path |
| --- | --- | --- |
| Model loading | Direct PyTorch `nn.Module` | PyTorch → ONNX → `forge.OnnxModule` |
| Export step | None | `torch.onnx.export(model, inputs, path, opset_version=17)` |
| Model wrapper | `forge.compile` handles directly | `forge.OnnxModule(module_name, onnx_model)` |
| Framework argument | `framework=Framework.PYTORCH` | `framework=Framework.ONNX` |
| Typical use case | Primary testing path | Cross-framework validation, debugging |

**ONNX export example code:**

**Models tested in both PyTorch and ONNX:**

| Model | PyTorch Test | ONNX Test |
| --- | --- | --- |
| NBEATS | `pytorch/timeseries/nbeats/test_nbeats.py` | `onnx/timeseries/nbeats/test_nbeats_onnx.py` ✓ |
| T5 | Not directly tested | `onnx/text/t5/test_t5_onnx.py` ✓ |
| NanoGPT | Not directly tested | `onnx/text/nanogpt/test_nanogpt_onnx.py` (xfail) |
| CLIP | `pytorch/multimodal/clip/` | `onnx/multimodal/clip/test_clip_onnx.py` (xfail) |
| LLaVA | `pytorch/multimodal/llava/test_llava.py` (xfail) | `onnx/multimodal/llava/test_llava_onnx.py` (xfail) |

Sources: [forge/test/models/onnx/timeseries/test_nbeats_onnx.py 30-73](https://github.com/tenstorrent/tt-forge-onnx/blob/e519819c/forge/test/models/onnx/timeseries/test_nbeats_onnx.py#L30-L73)[forge/test/models/onnx/text/t5/test_t5_onnx.py 36-74](https://github.com/tenstorrent/tt-forge-onnx/blob/e519819c/forge/test/models/onnx/text/t5/test_t5_onnx.py#L36-L74)[forge/test/models/onnx/multimodal/clip/test_clip_onnx.py 32-67](https://github.com/tenstorrent/tt-forge-onnx/blob/e519819c/forge/test/models/onnx/multimodal/clip/test_clip_onnx.py#L32-L67)[forge/test/models/onnx/text/nanogpt/test_nanogpt_onnx.py 30-67](https://github.com/tenstorrent/tt-forge-onnx/blob/e519819c/forge/test/models/onnx/text/nanogpt/test_nanogpt_onnx.py#L30-L67)

* * *

## Model Wrapper Pattern

Many models require a thin `torch.nn.Module` wrapper before compilation. Wrappers serve three purposes:

1.   **Normalize input signatures**: Convert dict-based or keyword-only inputs to positional tensor arguments.
2.   **Filter outputs**: Return only required tensors (e.g., `logits`), not full `ModelOutput` objects.
3.   **Disable unsupported features**: Turn off KV caching, return_dict, or other tracing-incompatible options.

**Common wrapper types and their roles:**

| Wrapper Class | File Location | Models Using It | Primary Purpose |
| --- | --- | --- | --- |
| `TextModelWrapper` | `test/models/models_utils.py` | Llama, Gemma, Phi, Mistral, Qwen | Embeds `input_ids` via embedding layer; hides KV cache |
| `Wrapper` (LLaVA) | [forge/test/models/pytorch/multimodal/llava/test_llava.py 26-34](https://github.com/tenstorrent/tt-forge-onnx/blob/e519819c/forge/test/models/pytorch/multimodal/llava/test_llava.py#L26-L34) | LLaVA-1.5-7B | Converts dict inputs to positional; extracts `logits` |
| `StableDiffusionWrapper` | [forge/test/models/pytorch/multimodal/stable_diffusion/test_stable_diffusion_v35.py 27-43](https://github.com/tenstorrent/tt-forge-onnx/blob/e519819c/forge/test/models/pytorch/multimodal/stable_diffusion/test_stable_diffusion_v35.py#L27-L43) | Stable Diffusion 3.5 | Flattens 4 input tensors; passes kwargs to transformer |
| `Wrapper` (Phi3.5 Vision) | [forge/test/models/pytorch/multimodal/phi3/test_phi3_5_vision.py 25-31](https://github.com/tenstorrent/tt-forge-onnx/blob/e519819c/forge/test/models/pytorch/multimodal/phi3/test_phi3_5_vision.py#L25-L31) | Phi3.5-Vision-Instruct | Passes only used forward args (drops unused ones) |
| `Wrapper` (Falcon) | [forge/test/models/pytorch/text/falcon/test_falcon.py 44-50](https://github.com/tenstorrent/tt-forge-onnx/blob/e519819c/forge/test/models/pytorch/text/falcon/test_falcon.py#L44-L50) | Falcon-7B-Instruct | Drops `past_key_values` from signature |
| `Wrapper` (T5 ONNX) | [forge/test/models/onnx/text/t5/test_t5_onnx.py 20-28](https://github.com/tenstorrent/tt-forge-onnx/blob/e519819c/forge/test/models/onnx/text/t5/test_t5_onnx.py#L20-L28) | T5-small | Converts dict inputs to positional for ONNX export |
| `Wrapper` (CLIP ONNX) | Not shown | CLIP text encoder | Wraps text encoder; converts dict inputs |

**TextModelWrapper usage (most common for LLMs):**

**Example wrapper implementations:**

**When to use a wrapper:**

Sources: [forge/test/models/pytorch/multimodal/stable_diffusion/test_stable_diffusion_v35.py 27-43](https://github.com/tenstorrent/tt-forge-onnx/blob/e519819c/forge/test/models/pytorch/multimodal/stable_diffusion/test_stable_diffusion_v35.py#L27-L43)[forge/test/models/pytorch/multimodal/llava/test_llava.py 26-34](https://github.com/tenstorrent/tt-forge-onnx/blob/e519819c/forge/test/models/pytorch/multimodal/llava/test_llava.py#L26-L34)[forge/test/models/pytorch/multimodal/phi3/test_phi3_5_vision.py 25-31](https://github.com/tenstorrent/tt-forge-onnx/blob/e519819c/forge/test/models/pytorch/multimodal/phi3/test_phi3_5_vision.py#L25-L31)[forge/test/models/pytorch/text/falcon/test_falcon.py 44-53](https://github.com/tenstorrent/tt-forge-onnx/blob/e519819c/forge/test/models/pytorch/text/falcon/test_falcon.py#L44-L53)[forge/test/models/onnx/text/t5/test_t5_onnx.py 20-28](https://github.com/tenstorrent/tt-forge-onnx/blob/e519819c/forge/test/models/onnx/text/t5/test_t5_onnx.py#L20-L28)[forge/test/models/pytorch/text/llama/test_llama3.py 110](https://github.com/tenstorrent/tt-forge-onnx/blob/e519819c/forge/test/models/pytorch/text/llama/test_llama3.py#L110-L110)

* * *

## Known Limitations

Several models are marked `xfail` or `out_of_memory` in the test suite. The most common reason is that the model requires multiple Tenstorrent chips to fit in device memory.

| Limitation | Pytest mark | Affected models |
| --- | --- | --- |
| Model too large for single chip | `pytest.xfail(reason="Requires multi-chip support")` | Falcon-7B+, Llama-3.8B+, Gemma-2B, Mistral-7B, Qwen2-7B, Phi2, Phi3, Phi4, LLaVA-1.5-7B, SD 3.5 Medium/Large |
| Host OOM during weight load | `@pytest.mark.out_of_memory` | Many 7B+ models |
| Compilation bug | `pytest.xfail(reason="...")` with issue URL | Gemma-2-2B-IT, NBEATS variants, NanoGPT ONNX, CLIP ONNX |
| Crashes at consteval stage | `pytest.xfail(reason="Test is killed at consteval")` | Phi4 token/sequence classification |

Models that pass single-chip compilation: ResNet (all variants), YOLOv5 (all variants/resolutions), NBEATS seasonality (ONNX), T5-small (ONNX), Llama-3.2-1B/1B-Instruct, Falcon-1B, Falcon-3 (1B).

Sources: [forge/test/models/pytorch/text/falcon/test_falcon.py 24-37](https://github.com/tenstorrent/tt-forge-onnx/blob/e519819c/forge/test/models/pytorch/text/falcon/test_falcon.py#L24-L37)[forge/test/models/pytorch/text/llama/test_llama3.py 101-105](https://github.com/tenstorrent/tt-forge-onnx/blob/e519819c/forge/test/models/pytorch/text/llama/test_llama3.py#L101-L105)[forge/test/models/pytorch/text/phi4/test_phi4.py 82-83](https://github.com/tenstorrent/tt-forge-onnx/blob/e519819c/forge/test/models/pytorch/text/phi4/test_phi4.py#L82-L83)[forge/test/models/onnx/timeseries/test_nbeats_onnx.py 28-30](https://github.com/tenstorrent/tt-forge-onnx/blob/e519819c/forge/test/models/onnx/timeseries/test_nbeats_onnx.py#L28-L30)

Dismiss
Refresh this wiki

Enter email to refresh
---
project: tt-forge-models
github: https://github.com/tenstorrent/tt-forge-models
deepwiki: https://deepwiki.com/tenstorrent/tt-forge-models/1-overview)
---

# Overview

 Relevant source files

* [README.md](https://github.com/tenstorrent/tt-forge-models/blob/0a7d2042/README.md?plain=1)
* [albert/masked\_lm/pytorch/\_\_init\_\_.py](https://github.com/tenstorrent/tt-forge-models/blob/0a7d2042/albert/masked_lm/pytorch/__init__.py)
* [albert/masked\_lm/pytorch/loader.py](https://github.com/tenstorrent/tt-forge-models/blob/0a7d2042/albert/masked_lm/pytorch/loader.py)
* [base.py](https://github.com/tenstorrent/tt-forge-models/blob/0a7d2042/base.py)
* [bert/pytorch/loader.py](https://github.com/tenstorrent/tt-forge-models/blob/0a7d2042/bert/pytorch/loader.py)
* [config.py](https://github.com/tenstorrent/tt-forge-models/blob/0a7d2042/config.py)
* [efficientnet/pytorch/loader.py](https://github.com/tenstorrent/tt-forge-models/blob/0a7d2042/efficientnet/pytorch/loader.py)
* [segformer/pytorch/loader.py](https://github.com/tenstorrent/tt-forge-models/blob/0a7d2042/segformer/pytorch/loader.py)
* [vit/pytorch/loader.py](https://github.com/tenstorrent/tt-forge-models/blob/0a7d2042/vit/pytorch/loader.py)
* [yolov3/pytorch/loader.py](https://github.com/tenstorrent/tt-forge-models/blob/0a7d2042/yolov3/pytorch/loader.py)
* [yolov4/pytorch/loader.py](https://github.com/tenstorrent/tt-forge-models/blob/0a7d2042/yolov4/pytorch/loader.py)
* [yolov4/pytorch/src/post\_processing.py](https://github.com/tenstorrent/tt-forge-models/blob/0a7d2042/yolov4/pytorch/src/post_processing.py)

This document provides a comprehensive overview of the `tt-forge-models` repository, which serves as a centralized collection of AI model implementations for Tenstorrent projects. The repository implements a standardized `ForgeModel` abstraction system that provides consistent interfaces across diverse model types including computer vision, natural language processing, and multimodal models.

For detailed information about specific model categories, see [Computer Vision Models](/tenstorrent/tt-forge-models/3-computer-vision-models), [Natural Language Processing Models](/tenstorrent/tt-forge-models/4-natural-language-processing-models), and [Multimodal Models](/tenstorrent/tt-forge-models/5-multimodal-models). For implementation details of the core architecture, see [Core Architecture](/tenstorrent/tt-forge-models/2-core-architecture).

## Purpose and Centralized Model Collection

The `tt-forge-models` repository eliminates code duplication across TT-Forge frontend repositories by providing a single source of truth for model implementations. Rather than each project maintaining separate model loaders, this repository offers over 100 model variants spanning object detection (YOLOv3-v10), image classification (ViT, EfficientNet, ResNet), large language models (Mistral, Llama, BERT), and specialized models.

**Centralized Model Collection Architecture**

Sources: [README.md1-8](https://github.com/tenstorrent/tt-forge-models/blob/0a7d2042/README.md?plain=1#L1-L8) [base.py16-24](https://github.com/tenstorrent/tt-forge-models/blob/0a7d2042/base.py#L16-L24)

## Standardized ForgeModel Interface System

Every model implementation inherits from the `ForgeModel` abstract base class, ensuring consistent APIs across all model types. The system uses a variant-based configuration approach where each model defines available variants through `ModelVariant` enums and `_VARIANTS` dictionaries mapping to `ModelConfig` objects.

**ForgeModel Inheritance Hierarchy with Concrete Implementations**

All model loaders implement three core abstract methods: `load_model()` returns the PyTorch model instance, `load_inputs()` provides sample input tensors, and `_get_model_info()` returns metadata for dashboard reporting. The `dtype_override` parameter enables hardware-specific optimizations like `torch.bfloat16` for TT hardware.

Sources: [base.py16-177](https://github.com/tenstorrent/tt-forge-models/blob/0a7d2042/base.py#L16-L177) [yolov3/pytorch/loader.py34-102](https://github.com/tenstorrent/tt-forge-models/blob/0a7d2042/yolov3/pytorch/loader.py#L34-L102) [vit/pytorch/loader.py32-89](https://github.com/tenstorrent/tt-forge-models/blob/0a7d2042/vit/pytorch/loader.py#L32-L89) [bert/pytorch/loader.py30-129](https://github.com/tenstorrent/tt-forge-models/blob/0a7d2042/bert/pytorch/loader.py#L30-L129)

## Repository Organization and Package Structure

The repository follows a consistent hierarchical structure: `{model_name}/{framework}/` with standardized file organization. Each model directory contains framework-specific subdirectories (primarily `pytorch/` with some `onnx/` support), and each framework directory exports `ModelLoader` and `ModelVariant` classes through `__init__.py` files.

**Repository Directory Structure and Import Patterns**

Each `loader.py` file defines a `ModelVariant` enum and `ModelLoader` class that inherits from `ForgeModel`. The top-level `__init__.py` files provide convenient imports from the default framework (typically PyTorch), while framework-specific `__init__.py` files export the core classes for external consumption.

Sources: [README.md9-36](https://github.com/tenstorrent/tt-forge-models/blob/0a7d2042/README.md?plain=1#L9-L36) [yolov3/pytorch/loader.py1-8](https://github.com/tenstorrent/tt-forge-models/blob/0a7d2042/yolov3/pytorch/loader.py#L1-L8) [albert/masked\_lm/pytorch/\_\_init\_\_.py5](https://github.com/tenstorrent/tt-forge-models/blob/0a7d2042/albert/masked_lm/pytorch/__init__.py#L5-L5) [vit/pytorch/loader.py1-6](https://github.com/tenstorrent/tt-forge-models/blob/0a7d2042/vit/pytorch/loader.py#L1-L6)

## Model Ecosystem and External Integration

The repository integrates with multiple external model sources through standardized `ModelSource` enum values. HuggingFace Hub dominates for modern transformer models, while TorchVision serves classic computer vision architectures, TIMM provides efficient image models, and custom sources handle specialized implementations like YOLO variants.

**External Model Source Integration Architecture**

The `get_file()` utility function handles file caching for custom models, downloading from URLs or loading from local paths. HuggingFace models use `from_pretrained()` methods directly, while TorchVision models leverage the weights system for pretrained parameters.

Sources: [vit/pytorch/loader.py77-89](https://github.com/tenstorrent/tt-forge-models/blob/0a7d2042/vit/pytorch/loader.py#L77-L89) [efficientnet/pytorch/loader.py148-172](https://github.com/tenstorrent/tt-forge-models/blob/0a7d2042/efficientnet/pytorch/loader.py#L148-L172) [yolov3/pytorch/loader.py76-102](https://github.com/tenstorrent/tt-forge-models/blob/0a7d2042/yolov3/pytorch/loader.py#L76-L102) [albert/masked\_lm/pytorch/loader.py113-137](https://github.com/tenstorrent/tt-forge-models/blob/0a7d2042/albert/masked_lm/pytorch/loader.py#L113-L137)

## Configuration and Metadata System

The configuration system uses dataclass-based `ModelConfig` objects to define variant-specific parameters, with specialized subclasses like `LLMModelConfig` for language models. The `ModelInfo` class provides structured metadata for dashboard reporting and metrics tracking across model groups, tasks, and sources.

**Configuration System Class Hierarchy and Enums**

The `_VARIANTS` dictionary maps `ModelVariant` enum values to configuration objects, enabling systematic variant management. Each model's `_get_model_info()` method returns standardized metadata using the enum values, supporting automated dashboard generation and metrics collection across the model ecosystem.

Sources: [config.py129-154](https://github.com/tenstorrent/tt-forge-models/blob/0a7d2042/config.py#L129-L154) [config.py92-127](https://github.com/tenstorrent/tt-forge-models/blob/0a7d2042/config.py#L92-L127) [config.py19-82](https://github.com/tenstorrent/tt-forge-models/blob/0a7d2042/config.py#L19-L82) [albert/masked\_lm/pytorch/loader.py36-53](https://github.com/tenstorrent/tt-forge-models/blob/0a7d2042/albert/masked_lm/pytorch/loader.py#L36-L53) [efficientnet/pytorch/loader.py29-35](https://github.com/tenstorrent/tt-forge-models/blob/0a7d2042/efficientnet/pytorch/loader.py#L29-L35) [bert/pytorch/loader.py34-43](https://github.com/tenstorrent/tt-forge-models/blob/0a7d2042/bert/pytorch/loader.py#L34-L43)

## Usage Patterns and External Consumption

External projects consume models through standardized import paths like `from third_party.tt_forge_models.{model}.pytorch import ModelLoader`. The consistent `ModelLoader` class name across all models enables generic programming patterns, while variant support allows fine-grained model selection within each family.

| Usage Pattern | Code Example | Description |
| --- | --- | --- |
| Basic Model Loading | `loader = ModelLoader()` | Uses `DEFAULT_VARIANT` |
| Variant-Specific Loading | `loader = ModelLoader(ModelVariant.LARGE)` | Explicit variant selection |
| Hardware Optimization | `model = loader.load_model(dtype_override=torch.bfloat16)` | TT hardware compatibility |
| Batch Processing | `inputs = loader.load_inputs(batch_size=8)` | Multi-sample inference |
| Multi-Model Usage | `YOLOLoader, BERTLoader` | Import aliases for multiple models |

The `dtype_override` parameter enables hardware-specific optimizations across all model types, supporting TT hardware requirements while maintaining consistent interfaces. The variant system allows selecting between model sizes (base/large), architectures (EfficientNet B0-B7), or specialized configurations within each model family.

Sources: [README.md38-60](https://github.com/tenstorrent/tt-forge-models/blob/0a7d2042/README.md?plain=1#L38-L60) [yolov3/pytorch/loader.py67-74](https://github.com/tenstorrent/tt-forge-models/blob/0a7d2042/yolov3/pytorch/loader.py#L67-L74) [vit/pytorch/loader.py65-72](https://github.com/tenstorrent/tt-forge-models/blob/0a7d2042/vit/pytorch/loader.py#L65-L72) [segformer/pytorch/loader.py68-76](https://github.com/tenstorrent/tt-forge-models/blob/0a7d2042/segformer/pytorch/loader.py#L68-L76) [efficientnet/pytorch/loader.py117-124](https://github.com/tenstorrent/tt-forge-models/blob/0a7d2042/efficientnet/pytorch/loader.py#L117-L124)

Refresh this wiki
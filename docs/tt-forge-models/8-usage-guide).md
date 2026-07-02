---
project: tt-forge-models
github: https://github.com/tenstorrent/tt-forge-models
deepwiki: https://deepwiki.com/tenstorrent/tt-forge-models/8-usage-guide)
---

# Usage Guide

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

This guide provides practical instructions for using the ModelLoader classes and ForgeModel system to load, configure, and run models from the tt-forge-models repository. It covers the standardized interface patterns, variant management, data type handling, and integration workflows.

For information about the underlying architecture and design principles, see [Core Architecture](/tenstorrent/tt-forge-models/2-core-architecture). For details about specific model families and their implementations, see [Computer Vision Models](/tenstorrent/tt-forge-models/3-computer-vision-models), [Natural Language Processing Models](/tenstorrent/tt-forge-models/4-natural-language-processing-models), and [Multimodal Models](/tenstorrent/tt-forge-models/5-multimodal-models).

## Basic Usage Pattern

All models in tt-forge-models follow a consistent usage pattern through the `ModelLoader` class and `ForgeModel` base interface. The standard workflow involves importing a model loader, instantiating it with an optional variant, and using the `load_model()` and `load_inputs()` methods.

### Basic Model Loading

The simplest usage pattern requires only importing and calling the load methods:

Sources: [yolov3/pytorch/loader.py34-102](https://github.com/tenstorrent/tt-forge-models/blob/0a7d2042/yolov3/pytorch/loader.py#L34-L102) [base.py16-177](https://github.com/tenstorrent/tt-forge-models/blob/0a7d2042/base.py#L16-L177)

### Working with Multiple Models

When using multiple models in the same code, use import aliases to avoid naming conflicts:

Sources: [README.md313-316](https://github.com/tenstorrent/tt-forge-models/blob/0a7d2042/README.md?plain=1#L313-L316)

## Working with Model Variants

Most models support multiple variants through the `ModelVariant` enum system. Each model defines its available variants in a `_VARIANTS` dictionary that maps variant enums to `ModelConfig` objects.

### Querying Available Variants

Before instantiating a model, you can check which variants are available:

Sources: [base.py39-51](https://github.com/tenstorrent/tt-forge-models/blob/0a7d2042/base.py#L39-L51) [albert/masked\_lm/pytorch/loader.py36-53](https://github.com/tenstorrent/tt-forge-models/blob/0a7d2042/albert/masked_lm/pytorch/loader.py#L36-L53)

### Using Specific Variants

Specify a variant when creating the loader instance:

Sources: [vit/pytorch/loader.py25-44](https://github.com/tenstorrent/tt-forge-models/blob/0a7d2042/vit/pytorch/loader.py#L25-L44) [base.py92-108](https://github.com/tenstorrent/tt-forge-models/blob/0a7d2042/base.py#L92-L108)

### Variant Validation

The system validates variants at instantiation time and provides helpful error messages:

Sources: [base.py54-90](https://github.com/tenstorrent/tt-forge-models/blob/0a7d2042/base.py#L54-L90)

## Data Type Management

The tt-forge-models system supports flexible data type management through the `dtype_override` parameter, enabling compatibility with different hardware targets and precision requirements.

### Default Behavior

Without dtype override, models use their default precision (typically `float32`):

Sources: [yolov3/pytorch/loader.py76-102](https://github.com/tenstorrent/tt-forge-models/blob/0a7d2042/yolov3/pytorch/loader.py#L76-L102) [yolov4/pytorch/loader.py81-107](https://github.com/tenstorrent/tt-forge-models/blob/0a7d2042/yolov4/pytorch/loader.py#L81-L107)

### Hardware-Specific Precision

For TT hardware or other specialized targets, override to appropriate precision:

Sources: [README.md56-59](https://github.com/tenstorrent/tt-forge-models/blob/0a7d2042/README.md?plain=1#L56-L59) [yolov3/pytorch/loader.py98-100](https://github.com/tenstorrent/tt-forge-models/blob/0a7d2042/yolov3/pytorch/loader.py#L98-L100)

### Tokenizer and Processor Integration

For NLP models, dtype overrides are passed through to tokenizers and processors when supported:

Sources: [albert/masked\_lm/pytorch/loader.py131-135](https://github.com/tenstorrent/tt-forge-models/blob/0a7d2042/albert/masked_lm/pytorch/loader.py#L131-L135) [bert/pytorch/loader.py122-125](https://github.com/tenstorrent/tt-forge-models/blob/0a7d2042/bert/pytorch/loader.py#L122-L125)

## Input and Output Handling

The system provides standardized methods for loading inputs and processing outputs, with model-specific implementations for preprocessing and post-processing.

### Input Loading with Batch Size

Most models support batch size configuration:

Sources: [vit/pytorch/loader.py91-111](https://github.com/tenstorrent/tt-forge-models/blob/0a7d2042/vit/pytorch/loader.py#L91-L111) [segformer/pytorch/loader.py135-167](https://github.com/tenstorrent/tt-forge-models/blob/0a7d2042/segformer/pytorch/loader.py#L135-L167)

### Computer Vision Input Processing

CV models typically download sample images and apply preprocessing transforms:

Sources: [efficientnet/pytorch/loader.py174-211](https://github.com/tenstorrent/tt-forge-models/blob/0a7d2042/efficientnet/pytorch/loader.py#L174-L211)

### NLP Input Processing

NLP models use tokenizers to convert text into model inputs:

Sources: [bert/pytorch/loader.py131-158](https://github.com/tenstorrent/tt-forge-models/blob/0a7d2042/bert/pytorch/loader.py#L131-L158)

### Output Decoding

Models that generate text or require output interpretation provide `decode_output()` methods:

Sources: [bert/pytorch/loader.py160-181](https://github.com/tenstorrent/tt-forge-models/blob/0a7d2042/bert/pytorch/loader.py#L160-L181) [albert/masked\_lm/pytorch/loader.py156-181](https://github.com/tenstorrent/tt-forge-models/blob/0a7d2042/albert/masked_lm/pytorch/loader.py#L156-L181)

### Post-Processing for Object Detection

Object detection models often provide specialized post-processing methods:

Sources: [yolov4/pytorch/loader.py135-164](https://github.com/tenstorrent/tt-forge-models/blob/0a7d2042/yolov4/pytorch/loader.py#L135-L164)

## Integration Patterns

The tt-forge-models repository is designed to be integrated into other projects as a git submodule or dependency, with consistent import patterns and interface contracts.

### Standard Import Pattern

All models follow the same import structure with framework-specific paths:

Sources: [README.md308-311](https://github.com/tenstorrent/tt-forge-models/blob/0a7d2042/README.md?plain=1#L308-L311) [README.md317-318](https://github.com/tenstorrent/tt-forge-models/blob/0a7d2042/README.md?plain=1#L317-L318)

### Generic Model Loading

Write generic code that works with any model by using the consistent interface:

Sources: [README.md40-60](https://github.com/tenstorrent/tt-forge-models/blob/0a7d2042/README.md?plain=1#L40-L60)

### Model Information Retrieval

Access model metadata for dashboards and reporting:

Sources: [base.py110-123](https://github.com/tenstorrent/tt-forge-models/blob/0a7d2042/base.py#L110-L123) [config.py118-126](https://github.com/tenstorrent/tt-forge-models/blob/0a7d2042/config.py#L118-L126)

## Advanced Usage Patterns

### Custom Configuration Extensions

Extend the base `ModelConfig` class for model-specific configuration:

Sources: [efficientnet/pytorch/loader.py29-35](https://github.com/tenstorrent/tt-forge-models/blob/0a7d2042/efficientnet/pytorch/loader.py#L29-L35)

### Batch Processing Utilities

Implement batch processing for multiple inputs:

### Hardware-Specific Optimizations

Configure models for specific hardware targets:

Sources: [README.md56-59](https://github.com/tenstorrent/tt-forge-models/blob/0a7d2042/README.md?plain=1#L56-L59)

Sources: [base.py16-177](https://github.com/tenstorrent/tt-forge-models/blob/0a7d2042/base.py#L16-L177) [yolov3/pytorch/loader.py1-138](https://github.com/tenstorrent/tt-forge-models/blob/0a7d2042/yolov3/pytorch/loader.py#L1-L138) [yolov4/pytorch/loader.py1-165](https://github.com/tenstorrent/tt-forge-models/blob/0a7d2042/yolov4/pytorch/loader.py#L1-L165) [vit/pytorch/loader.py1-127](https://github.com/tenstorrent/tt-forge-models/blob/0a7d2042/vit/pytorch/loader.py#L1-L127) [bert/pytorch/loader.py1-182](https://github.com/tenstorrent/tt-forge-models/blob/0a7d2042/bert/pytorch/loader.py#L1-L182) [albert/masked\_lm/pytorch/loader.py1-182](https://github.com/tenstorrent/tt-forge-models/blob/0a7d2042/albert/masked_lm/pytorch/loader.py#L1-L182) [efficientnet/pytorch/loader.py1-220](https://github.com/tenstorrent/tt-forge-models/blob/0a7d2042/efficientnet/pytorch/loader.py#L1-L220) [segformer/pytorch/loader.py1-182](https://github.com/tenstorrent/tt-forge-models/blob/0a7d2042/segformer/pytorch/loader.py#L1-L182) [config.py1-154](https://github.com/tenstorrent/tt-forge-models/blob/0a7d2042/config.py#L1-L154) [README.md1-319](https://github.com/tenstorrent/tt-forge-models/blob/0a7d2042/README.md?plain=1#L1-L319)

Refresh this wiki
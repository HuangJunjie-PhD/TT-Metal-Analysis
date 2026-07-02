---
project: tt-forge-models
github: https://github.com/tenstorrent/tt-forge-models
deepwiki: https://deepwiki.com/tenstorrent/tt-forge-models/2-core-architecture
---

# Core Architecture

Relevant source files
*   [albert/masked_lm/pytorch/__init__.py](https://github.com/tenstorrent/tt-forge-models/blob/0a7d2042/albert/masked_lm/pytorch/__init__.py)
*   [albert/masked_lm/pytorch/loader.py](https://github.com/tenstorrent/tt-forge-models/blob/0a7d2042/albert/masked_lm/pytorch/loader.py)
*   [base.py](https://github.com/tenstorrent/tt-forge-models/blob/0a7d2042/base.py)
*   [bert/pytorch/loader.py](https://github.com/tenstorrent/tt-forge-models/blob/0a7d2042/bert/pytorch/loader.py)
*   [config.py](https://github.com/tenstorrent/tt-forge-models/blob/0a7d2042/config.py)

This document covers the foundational architecture of the tt-forge-models repository, focusing on the `ForgeModel` base class, configuration system, variant management, and the standardized ModelLoader pattern that all model implementations follow. This architecture provides a unified interface for loading, configuring, and using AI models across different domains including computer vision, NLP, and multimodal tasks.

For information about specific model implementations, see [Computer Vision Models](https://deepwiki.com/tenstorrent/tt-forge-models/3-computer-vision-models), [Natural Language Processing Models](https://deepwiki.com/tenstorrent/tt-forge-models/4-natural-language-processing-models), and [Multimodal Models](https://deepwiki.com/tenstorrent/tt-forge-models/5-multimodal-models). For usage patterns and integration details, see [Usage Guide](https://deepwiki.com/tenstorrent/tt-forge-models/8-usage-guide).

## ForgeModel Base Class

The `ForgeModel` class serves as the abstract foundation for all model loaders in the repository. It defines a standardized interface that every model implementation must follow, ensuring consistency across diverse model types.

### Core Interface Definition

**ForgeModel Base Class Interface Diagram**

The `ForgeModel` class enforces three core abstract methods that subclasses must implement:

*   `load_model()`: Returns a PyTorch model instance configured for the selected variant
*   `load_inputs()`: Provides sample inputs appropriate for the model and variant
*   `_get_model_info()`: Returns metadata about the model for reporting and categorization

Sources: [base.py 16-177](https://github.com/tenstorrent/tt-forge-models/blob/0a7d2042/base.py#L16-L177)

### Variant Validation and Management

The base class provides robust variant validation through the `_validate_variant()` method, which ensures type safety and validates that requested variants exist in the model's `_VARIANTS` dictionary.

**Variant Validation Flow**

Sources: [base.py 54-91](https://github.com/tenstorrent/tt-forge-models/blob/0a7d2042/base.py#L54-L91)[base.py 26-38](https://github.com/tenstorrent/tt-forge-models/blob/0a7d2042/base.py#L26-L38)

## Configuration System

The configuration system provides a hierarchical structure for managing model parameters, with specialized configurations for different model types.

### Configuration Class Hierarchy

**Configuration System Class Hierarchy**

Sources: [config.py 129-154](https://github.com/tenstorrent/tt-forge-models/blob/0a7d2042/config.py#L129-L154)

### Enumeration System

The configuration system relies on several key enumerations that provide standardized categorization across all models:

| Enum Class | Purpose | Key Values |
| --- | --- | --- |
| `ModelGroup` | Model categorization | GENERALITY, RED, PRIORITY |
| `ModelTask` | Task classification | NLP_CAUSAL_LM, CV_IMAGE_CLS, NLP_QA |
| `ModelSource` | Origin of model | HUGGING_FACE, TORCH_HUB, TIMM |
| `Framework` | Implementation framework | TORCH, JAX, ONNX |

Sources: [config.py 19-82](https://github.com/tenstorrent/tt-forge-models/blob/0a7d2042/config.py#L19-L82)

## Standardized ModelLoader Pattern

All model implementations follow a consistent pattern that extends `ForgeModel` and implements the required interface methods.

### Implementation Pattern Structure

**Standard ModelLoader Implementation Pattern**

### Variant Configuration Pattern

Each ModelLoader defines its available variants through a structured `_VARIANTS` dictionary that maps variant enums to configuration objects:

**Variant Configuration Mapping**

Sources: [albert/masked_lm/pytorch/loader.py 23-53](https://github.com/tenstorrent/tt-forge-models/blob/0a7d2042/albert/masked_lm/pytorch/loader.py#L23-L53)[bert/pytorch/loader.py 23-43](https://github.com/tenstorrent/tt-forge-models/blob/0a7d2042/bert/pytorch/loader.py#L23-L43)

### Instance Lifecycle and Method Implementation

**ModelLoader Instance Lifecycle**

Sources: [albert/masked_lm/pytorch/loader.py 61-70](https://github.com/tenstorrent/tt-forge-models/blob/0a7d2042/albert/masked_lm/pytorch/loader.py#L61-L70)[albert/masked_lm/pytorch/loader.py 113-137](https://github.com/tenstorrent/tt-forge-models/blob/0a7d2042/albert/masked_lm/pytorch/loader.py#L113-L137)[bert/pytorch/loader.py 104-129](https://github.com/tenstorrent/tt-forge-models/blob/0a7d2042/bert/pytorch/loader.py#L104-L129)

## Metadata and Reporting System

The architecture includes a comprehensive metadata system through the `ModelInfo` class, which provides standardized information for dashboard reporting and model categorization.

### ModelInfo Structure and Usage

**ModelInfo and Reporting Integration**

The `ModelInfo.name` property generates standardized identifiers following the pattern: `{framework}_{model}_{variant}_{task}_{source}`, while `to_report_dict()` provides structured data for integration with testing and reporting pipelines.

Sources: [config.py 92-127](https://github.com/tenstorrent/tt-forge-models/blob/0a7d2042/config.py#L92-L127)[albert/masked_lm/pytorch/loader.py 72-89](https://github.com/tenstorrent/tt-forge-models/blob/0a7d2042/albert/masked_lm/pytorch/loader.py#L72-L89)[bert/pytorch/loader.py 62-80](https://github.com/tenstorrent/tt-forge-models/blob/0a7d2042/bert/pytorch/loader.py#L62-L80)

## Type Safety and Validation

The architecture emphasizes type safety through the `StrEnum` base class and comprehensive validation in the `ForgeModel` base class.

### Type Safety Mechanisms

**Type Safety and Validation Flow**

The `StrEnum` class ensures that enum values have proper string representations, while the validation system provides clear error messages when invalid variants are requested.

Sources: [config.py 12-17](https://github.com/tenstorrent/tt-forge-models/blob/0a7d2042/config.py#L12-L17)[base.py 77-88](https://github.com/tenstorrent/tt-forge-models/blob/0a7d2042/base.py#L77-L88)

Dismiss
Refresh this wiki

Enter email to refresh
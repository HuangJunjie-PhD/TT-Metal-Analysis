---
project: tt-forge-models
github: https://github.com/tenstorrent/tt-forge-models
deepwiki: https://deepwiki.com/tenstorrent/tt-forge-models/7-package-structure-and-organization)
---

# Package Structure and Organization

 Relevant source files

* [beit/\_\_init\_\_.py](https://github.com/tenstorrent/tt-forge-models/blob/0a7d2042/beit/__init__.py)
* [beit/pytorch/\_\_init\_\_.py](https://github.com/tenstorrent/tt-forge-models/blob/0a7d2042/beit/pytorch/__init__.py)
* [beit/pytorch/loader.py](https://github.com/tenstorrent/tt-forge-models/blob/0a7d2042/beit/pytorch/loader.py)
* [bi\_rnn\_crf/\_\_init\_\_.py](https://github.com/tenstorrent/tt-forge-models/blob/0a7d2042/bi_rnn_crf/__init__.py)
* [bi\_rnn\_crf/pytorch/\_\_init\_\_.py](https://github.com/tenstorrent/tt-forge-models/blob/0a7d2042/bi_rnn_crf/pytorch/__init__.py)
* [d\_fine/\_\_init\_\_.py](https://github.com/tenstorrent/tt-forge-models/blob/0a7d2042/d_fine/__init__.py)
* [d\_fine/pytorch/\_\_init\_\_.py](https://github.com/tenstorrent/tt-forge-models/blob/0a7d2042/d_fine/pytorch/__init__.py)
* [d\_fine/pytorch/loader.py](https://github.com/tenstorrent/tt-forge-models/blob/0a7d2042/d_fine/pytorch/loader.py)
* [deit/pytorch/\_\_init\_\_.py](https://github.com/tenstorrent/tt-forge-models/blob/0a7d2042/deit/pytorch/__init__.py)
* [densenet/pytorch/\_\_init\_\_.py](https://github.com/tenstorrent/tt-forge-models/blob/0a7d2042/densenet/pytorch/__init__.py)
* [efficientnet/pytorch/\_\_init\_\_.py](https://github.com/tenstorrent/tt-forge-models/blob/0a7d2042/efficientnet/pytorch/__init__.py)
* [flux/\_\_init\_\_.py](https://github.com/tenstorrent/tt-forge-models/blob/0a7d2042/flux/__init__.py)
* [flux/pytorch/\_\_init\_\_.py](https://github.com/tenstorrent/tt-forge-models/blob/0a7d2042/flux/pytorch/__init__.py)
* [ghostnet/pytorch/\_\_init\_\_.py](https://github.com/tenstorrent/tt-forge-models/blob/0a7d2042/ghostnet/pytorch/__init__.py)
* [googlenet/pytorch/\_\_init\_\_.py](https://github.com/tenstorrent/tt-forge-models/blob/0a7d2042/googlenet/pytorch/__init__.py)
* [hrnet/pytorch/\_\_init\_\_.py](https://github.com/tenstorrent/tt-forge-models/blob/0a7d2042/hrnet/pytorch/__init__.py)
* [inception/pytorch/\_\_init\_\_.py](https://github.com/tenstorrent/tt-forge-models/blob/0a7d2042/inception/pytorch/__init__.py)
* [mlp\_mixer/pytorch/\_\_init\_\_.py](https://github.com/tenstorrent/tt-forge-models/blob/0a7d2042/mlp_mixer/pytorch/__init__.py)
* [phi1/causal\_lm/\_\_init\_\_.py](https://github.com/tenstorrent/tt-forge-models/blob/0a7d2042/phi1/causal_lm/__init__.py)
* [regnet/pytorch/\_\_init\_\_.py](https://github.com/tenstorrent/tt-forge-models/blob/0a7d2042/regnet/pytorch/__init__.py)
* [resnext/pytorch/\_\_init\_\_.py](https://github.com/tenstorrent/tt-forge-models/blob/0a7d2042/resnext/pytorch/__init__.py)
* [segformer/pytorch/\_\_init\_\_.py](https://github.com/tenstorrent/tt-forge-models/blob/0a7d2042/segformer/pytorch/__init__.py)
* [vgg/pytorch/\_\_init\_\_.py](https://github.com/tenstorrent/tt-forge-models/blob/0a7d2042/vgg/pytorch/__init__.py)
* [xception/pytorch/\_\_init\_\_.py](https://github.com/tenstorrent/tt-forge-models/blob/0a7d2042/xception/pytorch/__init__.py)

This document explains the organizational structure of the tt-forge-models repository, covering directory layouts, naming conventions, and import patterns that enable consistent access to model implementations across different frameworks and model families.

For information about the core ForgeModel interface and configuration system, see [Core Architecture](/tenstorrent/tt-forge-models/2-core-architecture). For guidance on using these packages in external projects, see [Usage Guide](/tenstorrent/tt-forge-models/8-usage-guide).

## Repository Organization

The tt-forge-models repository follows a hierarchical structure designed to provide consistent access patterns across model families while supporting multiple frameworks and variants.

### Top-Level Structure

**Directory Structure Pattern**

Every model implementation follows this standardized pattern:

* `{model_name}/` - Top-level model directory
* `{model_name}/__init__.py` - Default framework import
* `{model_name}/pytorch/` - PyTorch implementation directory
* `{model_name}/pytorch/__init__.py` - Framework-specific exports
* `{model_name}/pytorch/loader.py` - ModelLoader implementation
* `{model_name}/pytorch/src/` - Optional model-specific source code

Sources: [d\_fine/\_\_init\_\_.py1-9](https://github.com/tenstorrent/tt-forge-models/blob/0a7d2042/d_fine/__init__.py#L1-L9) [d\_fine/pytorch/\_\_init\_\_.py1-8](https://github.com/tenstorrent/tt-forge-models/blob/0a7d2042/d_fine/pytorch/__init__.py#L1-L8) [d\_fine/pytorch/loader.py1-210](https://github.com/tenstorrent/tt-forge-models/blob/0a7d2042/d_fine/pytorch/loader.py#L1-L210) [beit/\_\_init\_\_.py1-9](https://github.com/tenstorrent/tt-forge-models/blob/0a7d2042/beit/__init__.py#L1-L9) [beit/pytorch/\_\_init\_\_.py1-8](https://github.com/tenstorrent/tt-forge-models/blob/0a7d2042/beit/pytorch/__init__.py#L1-L8)

## Model Package Import Pattern

The repository implements a two-tier import system that provides both convenience and flexibility for accessing model implementations.

**Import Hierarchy**

1. **Top-level model import**: `from model_name import ModelLoader`
2. **Framework-specific import**: `from model_name.pytorch import ModelLoader`
3. **Direct loader import**: `from model_name.pytorch.loader import ModelLoader, ModelVariant`

This design allows external projects to import models using the simplest syntax while maintaining the ability to access framework-specific implementations when needed.

Sources: [d\_fine/\_\_init\_\_.py8](https://github.com/tenstorrent/tt-forge-models/blob/0a7d2042/d_fine/__init__.py#L8-L8) [d\_fine/pytorch/\_\_init\_\_.py7](https://github.com/tenstorrent/tt-forge-models/blob/0a7d2042/d_fine/pytorch/__init__.py#L7-L7) [flux/\_\_init\_\_.py8](https://github.com/tenstorrent/tt-forge-models/blob/0a7d2042/flux/__init__.py#L8-L8) [flux/pytorch/\_\_init\_\_.py7](https://github.com/tenstorrent/tt-forge-models/blob/0a7d2042/flux/pytorch/__init__.py#L7-L7)

## Standardized File Patterns

### Top-Level Model `__init__.py`

Every model's top-level `__init__.py` follows an identical structure:

This pattern provides:

* Consistent licensing headers
* Standardized documentation format
* Default framework selection (PyTorch)
* Simple import interface for external projects

Sources: [d\_fine/\_\_init\_\_.py1-9](https://github.com/tenstorrent/tt-forge-models/blob/0a7d2042/d_fine/__init__.py#L1-L9) [flux/\_\_init\_\_.py1-9](https://github.com/tenstorrent/tt-forge-models/blob/0a7d2042/flux/__init__.py#L1-L9) [beit/\_\_init\_\_.py1-9](https://github.com/tenstorrent/tt-forge-models/blob/0a7d2042/beit/__init__.py#L1-L9) [bi\_rnn\_crf/\_\_init\_\_.py1-9](https://github.com/tenstorrent/tt-forge-models/blob/0a7d2042/bi_rnn_crf/__init__.py#L1-L9)

### Framework-Specific `__init__.py`

Each framework subdirectory contains an `__init__.py` that exports the key classes:

This ensures that both `ModelLoader` and `ModelVariant` are available at the framework level, enabling type hints and variant discovery.

Sources: [d\_fine/pytorch/\_\_init\_\_.py1-8](https://github.com/tenstorrent/tt-forge-models/blob/0a7d2042/d_fine/pytorch/__init__.py#L1-L8) [beit/pytorch/\_\_init\_\_.py1-8](https://github.com/tenstorrent/tt-forge-models/blob/0a7d2042/beit/pytorch/__init__.py#L1-L8) [deit/pytorch/\_\_init\_\_.py1-8](https://github.com/tenstorrent/tt-forge-models/blob/0a7d2042/deit/pytorch/__init__.py#L1-L8) [segformer/pytorch/\_\_init\_\_.py1-8](https://github.com/tenstorrent/tt-forge-models/blob/0a7d2042/segformer/pytorch/__init__.py#L1-L8)

## ModelLoader Implementation Structure

### Required Components

Every `loader.py` file implements a standardized structure with these required components:

### Standard Import Pattern

Each `loader.py` imports from consistent locations:

| Import Source | Purpose | Example |
| --- | --- | --- |
| `...base` | ForgeModel base class | `from ...base import ForgeModel` |
| `...config` | Configuration classes and enums | `from ...config import ModelConfig, ModelInfo` |
| `...tools.utils` | Utility functions | `from ...tools.utils import get_file` |
| External libraries | Framework-specific dependencies | `from transformers import BeitForImageClassification` |

Sources: [d\_fine/pytorch/loader.py13-22](https://github.com/tenstorrent/tt-forge-models/blob/0a7d2042/d_fine/pytorch/loader.py#L13-L22) [beit/pytorch/loader.py13-22](https://github.com/tenstorrent/tt-forge-models/blob/0a7d2042/beit/pytorch/loader.py#L13-L22)

### Variant Configuration Pattern

The `_VARIANTS` dictionary maps `ModelVariant` enum values to `ModelConfig` objects:

This pattern enables:

* Consistent variant discovery across models
* Centralized configuration management
* Easy addition of new variants
* Type-safe variant selection

Sources: [d\_fine/pytorch/loader.py39-55](https://github.com/tenstorrent/tt-forge-models/blob/0a7d2042/d_fine/pytorch/loader.py#L39-L55) [beit/pytorch/loader.py36-43](https://github.com/tenstorrent/tt-forge-models/blob/0a7d2042/beit/pytorch/loader.py#L36-L43)

## Framework Support Architecture

### Multi-Framework Design

The package structure is designed to support multiple ML frameworks while maintaining a consistent interface:

### Default Framework Selection

The top-level `__init__.py` always imports from the PyTorch implementation by default:

This provides:

* Consistent default behavior across all models
* Easy migration path if default framework changes
* Clear indication of primary framework support

Sources: [d\_fine/\_\_init\_\_.py7-8](https://github.com/tenstorrent/tt-forge-models/blob/0a7d2042/d_fine/__init__.py#L7-L8) [flux/\_\_init\_\_.py7-8](https://github.com/tenstorrent/tt-forge-models/blob/0a7d2042/flux/__init__.py#L7-L8) [bi\_rnn\_crf/\_\_init\_\_.py7-8](https://github.com/tenstorrent/tt-forge-models/blob/0a7d2042/bi_rnn_crf/__init__.py#L7-L8)

## Package Discovery and Navigation

### Consistent Naming Conventions

The repository follows these naming conventions:

| Element | Convention | Example |
| --- | --- | --- |
| Model directories | `snake_case` | `d_fine/`, `bi_rnn_crf/` |
| Framework directories | `lowercase` | `pytorch/`, `onnx/` |
| Class names | `PascalCase` | `ModelLoader`, `ModelVariant` |
| Enum values | `UPPER_CASE` | `ModelVariant.SMALL` |
| Configuration keys | `snake_case` | `pretrained_model_name` |

### Import Path Predictability

The standardized structure enables predictable import paths:

This predictability simplifies:

* Automated tooling and scripts
* Documentation generation
* IDE auto-completion and navigation
* External project integration

Sources: All `__init__.py` files demonstrate this pattern consistently across [d\_fine/\_\_init\_\_.py1-9](https://github.com/tenstorrent/tt-forge-models/blob/0a7d2042/d_fine/__init__.py#L1-L9) [beit/\_\_init\_\_.py1-9](https://github.com/tenstorrent/tt-forge-models/blob/0a7d2042/beit/__init__.py#L1-L9) [efficientnet/pytorch/\_\_init\_\_.py1-8](https://github.com/tenstorrent/tt-forge-models/blob/0a7d2042/efficientnet/pytorch/__init__.py#L1-L8) [vgg/pytorch/\_\_init\_\_.py1-8](https://github.com/tenstorrent/tt-forge-models/blob/0a7d2042/vgg/pytorch/__init__.py#L1-L8)

Refresh this wiki
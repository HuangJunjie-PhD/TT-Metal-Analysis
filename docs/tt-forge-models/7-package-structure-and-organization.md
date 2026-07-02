---
project: tt-forge-models
github: https://github.com/tenstorrent/tt-forge-models
deepwiki: https://deepwiki.com/tenstorrent/tt-forge-models/7-package-structure-and-organization
---

# Package Structure and Organization

Relevant source files
*   [beit/__init__.py](https://github.com/tenstorrent/tt-forge-models/blob/0a7d2042/beit/__init__.py)
*   [beit/pytorch/__init__.py](https://github.com/tenstorrent/tt-forge-models/blob/0a7d2042/beit/pytorch/__init__.py)
*   [beit/pytorch/loader.py](https://github.com/tenstorrent/tt-forge-models/blob/0a7d2042/beit/pytorch/loader.py)
*   [bi_rnn_crf/__init__.py](https://github.com/tenstorrent/tt-forge-models/blob/0a7d2042/bi_rnn_crf/__init__.py)
*   [bi_rnn_crf/pytorch/__init__.py](https://github.com/tenstorrent/tt-forge-models/blob/0a7d2042/bi_rnn_crf/pytorch/__init__.py)
*   [d_fine/__init__.py](https://github.com/tenstorrent/tt-forge-models/blob/0a7d2042/d_fine/__init__.py)
*   [d_fine/pytorch/__init__.py](https://github.com/tenstorrent/tt-forge-models/blob/0a7d2042/d_fine/pytorch/__init__.py)
*   [d_fine/pytorch/loader.py](https://github.com/tenstorrent/tt-forge-models/blob/0a7d2042/d_fine/pytorch/loader.py)
*   [deit/pytorch/__init__.py](https://github.com/tenstorrent/tt-forge-models/blob/0a7d2042/deit/pytorch/__init__.py)
*   [densenet/pytorch/__init__.py](https://github.com/tenstorrent/tt-forge-models/blob/0a7d2042/densenet/pytorch/__init__.py)
*   [efficientnet/pytorch/__init__.py](https://github.com/tenstorrent/tt-forge-models/blob/0a7d2042/efficientnet/pytorch/__init__.py)
*   [flux/__init__.py](https://github.com/tenstorrent/tt-forge-models/blob/0a7d2042/flux/__init__.py)
*   [flux/pytorch/__init__.py](https://github.com/tenstorrent/tt-forge-models/blob/0a7d2042/flux/pytorch/__init__.py)
*   [ghostnet/pytorch/__init__.py](https://github.com/tenstorrent/tt-forge-models/blob/0a7d2042/ghostnet/pytorch/__init__.py)
*   [googlenet/pytorch/__init__.py](https://github.com/tenstorrent/tt-forge-models/blob/0a7d2042/googlenet/pytorch/__init__.py)
*   [hrnet/pytorch/__init__.py](https://github.com/tenstorrent/tt-forge-models/blob/0a7d2042/hrnet/pytorch/__init__.py)
*   [inception/pytorch/__init__.py](https://github.com/tenstorrent/tt-forge-models/blob/0a7d2042/inception/pytorch/__init__.py)
*   [mlp_mixer/pytorch/__init__.py](https://github.com/tenstorrent/tt-forge-models/blob/0a7d2042/mlp_mixer/pytorch/__init__.py)
*   [phi1/causal_lm/__init__.py](https://github.com/tenstorrent/tt-forge-models/blob/0a7d2042/phi1/causal_lm/__init__.py)
*   [regnet/pytorch/__init__.py](https://github.com/tenstorrent/tt-forge-models/blob/0a7d2042/regnet/pytorch/__init__.py)
*   [resnext/pytorch/__init__.py](https://github.com/tenstorrent/tt-forge-models/blob/0a7d2042/resnext/pytorch/__init__.py)
*   [segformer/pytorch/__init__.py](https://github.com/tenstorrent/tt-forge-models/blob/0a7d2042/segformer/pytorch/__init__.py)
*   [vgg/pytorch/__init__.py](https://github.com/tenstorrent/tt-forge-models/blob/0a7d2042/vgg/pytorch/__init__.py)
*   [xception/pytorch/__init__.py](https://github.com/tenstorrent/tt-forge-models/blob/0a7d2042/xception/pytorch/__init__.py)

This document explains the organizational structure of the tt-forge-models repository, covering directory layouts, naming conventions, and import patterns that enable consistent access to model implementations across different frameworks and model families.

For information about the core ForgeModel interface and configuration system, see [Core Architecture](https://deepwiki.com/tenstorrent/tt-forge-models/2-core-architecture). For guidance on using these packages in external projects, see [Usage Guide](https://deepwiki.com/tenstorrent/tt-forge-models/8-usage-guide).

## Repository Organization

The tt-forge-models repository follows a hierarchical structure designed to provide consistent access patterns across model families while supporting multiple frameworks and variants.

### Top-Level Structure

**Directory Structure Pattern**

Every model implementation follows this standardized pattern:

*   `{model_name}/` - Top-level model directory
*   `{model_name}/__init__.py` - Default framework import
*   `{model_name}/pytorch/` - PyTorch implementation directory
*   `{model_name}/pytorch/__init__.py` - Framework-specific exports
*   `{model_name}/pytorch/loader.py` - ModelLoader implementation
*   `{model_name}/pytorch/src/` - Optional model-specific source code

Sources: [d_fine/__init__.py 1-9](https://github.com/tenstorrent/tt-forge-models/blob/0a7d2042/d_fine/__init__.py#L1-L9)[d_fine/pytorch/__init__.py 1-8](https://github.com/tenstorrent/tt-forge-models/blob/0a7d2042/d_fine/pytorch/__init__.py#L1-L8)[d_fine/pytorch/loader.py 1-210](https://github.com/tenstorrent/tt-forge-models/blob/0a7d2042/d_fine/pytorch/loader.py#L1-L210)[beit/__init__.py 1-9](https://github.com/tenstorrent/tt-forge-models/blob/0a7d2042/beit/__init__.py#L1-L9)[beit/pytorch/__init__.py 1-8](https://github.com/tenstorrent/tt-forge-models/blob/0a7d2042/beit/pytorch/__init__.py#L1-L8)

## Model Package Import Pattern

The repository implements a two-tier import system that provides both convenience and flexibility for accessing model implementations.

**Import Hierarchy**

1.   **Top-level model import**: `from model_name import ModelLoader`
2.   **Framework-specific import**: `from model_name.pytorch import ModelLoader`
3.   **Direct loader import**: `from model_name.pytorch.loader import ModelLoader, ModelVariant`

This design allows external projects to import models using the simplest syntax while maintaining the ability to access framework-specific implementations when needed.

Sources: [d_fine/__init__.py 8](https://github.com/tenstorrent/tt-forge-models/blob/0a7d2042/d_fine/__init__.py#L8-L8)[d_fine/pytorch/__init__.py 7](https://github.com/tenstorrent/tt-forge-models/blob/0a7d2042/d_fine/pytorch/__init__.py#L7-L7)[flux/__init__.py 8](https://github.com/tenstorrent/tt-forge-models/blob/0a7d2042/flux/__init__.py#L8-L8)[flux/pytorch/__init__.py 7](https://github.com/tenstorrent/tt-forge-models/blob/0a7d2042/flux/pytorch/__init__.py#L7-L7)

## Standardized File Patterns

### Top-Level Model `__init__.py`

Every model's top-level `__init__.py` follows an identical structure:

This pattern provides:

*   Consistent licensing headers
*   Standardized documentation format
*   Default framework selection (PyTorch)
*   Simple import interface for external projects

Sources: [d_fine/__init__.py 1-9](https://github.com/tenstorrent/tt-forge-models/blob/0a7d2042/d_fine/__init__.py#L1-L9)[flux/__init__.py 1-9](https://github.com/tenstorrent/tt-forge-models/blob/0a7d2042/flux/__init__.py#L1-L9)[beit/__init__.py 1-9](https://github.com/tenstorrent/tt-forge-models/blob/0a7d2042/beit/__init__.py#L1-L9)[bi_rnn_crf/__init__.py 1-9](https://github.com/tenstorrent/tt-forge-models/blob/0a7d2042/bi_rnn_crf/__init__.py#L1-L9)

### Framework-Specific `__init__.py`

Each framework subdirectory contains an `__init__.py` that exports the key classes:

This ensures that both `ModelLoader` and `ModelVariant` are available at the framework level, enabling type hints and variant discovery.

Sources: [d_fine/pytorch/__init__.py 1-8](https://github.com/tenstorrent/tt-forge-models/blob/0a7d2042/d_fine/pytorch/__init__.py#L1-L8)[beit/pytorch/__init__.py 1-8](https://github.com/tenstorrent/tt-forge-models/blob/0a7d2042/beit/pytorch/__init__.py#L1-L8)[deit/pytorch/__init__.py 1-8](https://github.com/tenstorrent/tt-forge-models/blob/0a7d2042/deit/pytorch/__init__.py#L1-L8)[segformer/pytorch/__init__.py 1-8](https://github.com/tenstorrent/tt-forge-models/blob/0a7d2042/segformer/pytorch/__init__.py#L1-L8)

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

Sources: [d_fine/pytorch/loader.py 13-22](https://github.com/tenstorrent/tt-forge-models/blob/0a7d2042/d_fine/pytorch/loader.py#L13-L22)[beit/pytorch/loader.py 13-22](https://github.com/tenstorrent/tt-forge-models/blob/0a7d2042/beit/pytorch/loader.py#L13-L22)

### Variant Configuration Pattern

The `_VARIANTS` dictionary maps `ModelVariant` enum values to `ModelConfig` objects:

This pattern enables:

*   Consistent variant discovery across models
*   Centralized configuration management
*   Easy addition of new variants
*   Type-safe variant selection

Sources: [d_fine/pytorch/loader.py 39-55](https://github.com/tenstorrent/tt-forge-models/blob/0a7d2042/d_fine/pytorch/loader.py#L39-L55)[beit/pytorch/loader.py 36-43](https://github.com/tenstorrent/tt-forge-models/blob/0a7d2042/beit/pytorch/loader.py#L36-L43)

## Framework Support Architecture

### Multi-Framework Design

The package structure is designed to support multiple ML frameworks while maintaining a consistent interface:

### Default Framework Selection

The top-level `__init__.py` always imports from the PyTorch implementation by default:

This provides:

*   Consistent default behavior across all models
*   Easy migration path if default framework changes
*   Clear indication of primary framework support

Sources: [d_fine/__init__.py 7-8](https://github.com/tenstorrent/tt-forge-models/blob/0a7d2042/d_fine/__init__.py#L7-L8)[flux/__init__.py 7-8](https://github.com/tenstorrent/tt-forge-models/blob/0a7d2042/flux/__init__.py#L7-L8)[bi_rnn_crf/__init__.py 7-8](https://github.com/tenstorrent/tt-forge-models/blob/0a7d2042/bi_rnn_crf/__init__.py#L7-L8)

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

*   Automated tooling and scripts
*   Documentation generation
*   IDE auto-completion and navigation
*   External project integration

Sources: All `__init__.py` files demonstrate this pattern consistently across [d_fine/__init__.py 1-9](https://github.com/tenstorrent/tt-forge-models/blob/0a7d2042/d_fine/__init__.py#L1-L9)[beit/__init__.py 1-9](https://github.com/tenstorrent/tt-forge-models/blob/0a7d2042/beit/__init__.py#L1-L9)[efficientnet/pytorch/__init__.py 1-8](https://github.com/tenstorrent/tt-forge-models/blob/0a7d2042/efficientnet/pytorch/__init__.py#L1-L8)[vgg/pytorch/__init__.py 1-8](https://github.com/tenstorrent/tt-forge-models/blob/0a7d2042/vgg/pytorch/__init__.py#L1-L8)

Dismiss
Refresh this wiki

Enter email to refresh
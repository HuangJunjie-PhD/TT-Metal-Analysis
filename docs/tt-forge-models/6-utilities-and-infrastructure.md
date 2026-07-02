---
project: tt-forge-models
github: https://github.com/tenstorrent/tt-forge-models
deepwiki: https://deepwiki.com/tenstorrent/tt-forge-models/6-utilities-and-infrastructure
---

# Utilities and Infrastructure

Relevant source files
*   [densenet/pytorch/loader.py](https://github.com/tenstorrent/tt-forge-models/blob/0a7d2042/densenet/pytorch/loader.py)
*   [dla/pytorch/loader.py](https://github.com/tenstorrent/tt-forge-models/blob/0a7d2042/dla/pytorch/loader.py)
*   [ghostnet/pytorch/loader.py](https://github.com/tenstorrent/tt-forge-models/blob/0a7d2042/ghostnet/pytorch/loader.py)
*   [hrnet/pytorch/loader.py](https://github.com/tenstorrent/tt-forge-models/blob/0a7d2042/hrnet/pytorch/loader.py)
*   [mlp_mixer/pytorch/loader.py](https://github.com/tenstorrent/tt-forge-models/blob/0a7d2042/mlp_mixer/pytorch/loader.py)
*   [regnet/pytorch/loader.py](https://github.com/tenstorrent/tt-forge-models/blob/0a7d2042/regnet/pytorch/loader.py)
*   [resnext/pytorch/loader.py](https://github.com/tenstorrent/tt-forge-models/blob/0a7d2042/resnext/pytorch/loader.py)
*   [tools/utils.py](https://github.com/tenstorrent/tt-forge-models/blob/0a7d2042/tools/utils.py)

This page documents the shared utility functions, file caching system, and infrastructure components that support all model implementations in the tt-forge-models repository. These utilities provide consistent file management, result processing, and integration capabilities across all ModelLoader implementations.

For information about the core ModelLoader pattern and ForgeModel abstraction, see [Core Architecture](https://deepwiki.com/tenstorrent/tt-forge-models/2-core-architecture). For specific model implementations that use these utilities, see [Computer Vision Models](https://deepwiki.com/tenstorrent/tt-forge-models/3-computer-vision-models) and [Natural Language Processing Models](https://deepwiki.com/tenstorrent/tt-forge-models/4-natural-language-processing-models).

## File Caching System

The file caching system is the primary infrastructure component, providing a multi-tier caching strategy that handles both local files and remote URLs. The system is implemented in the `get_file()` function and supports various cache locations based on environment configuration.

### Caching Architecture

Sources: [tools/utils.py 15-112](https://github.com/tenstorrent/tt-forge-models/blob/0a7d2042/tools/utils.py#L15-L112)

### Cache Location Logic

The caching system uses a hierarchical approach to determine the optimal cache location:

| Priority | Environment Variable | Cache Path | Use Case |
| --- | --- | --- | --- |
| 1 | `DOCKER_CACHE_ROOT` | `$DOCKER_CACHE_ROOT/url_cache` | Docker environments |
| 2 | `LOCAL_LF_CACHE` | `$LOCAL_LF_CACHE/url_cache` | Local development |
| 3 | Default | `~/.cache/url_cache` | Fallback for all users |

The system also handles read-only cache scenarios where files can be read from a shared cache but new downloads fall back to the user's home directory.

Sources: [tools/utils.py 49-72](https://github.com/tenstorrent/tt-forge-models/blob/0a7d2042/tools/utils.py#L49-L72)

### URL Handling and Collision Prevention

For URL-based downloads, the system implements collision prevention through hash-based filename generation:

Sources: [tools/utils.py 28-44](https://github.com/tenstorrent/tt-forge-models/blob/0a7d2042/tools/utils.py#L28-L44)[tools/utils.py 76-104](https://github.com/tenstorrent/tt-forge-models/blob/0a7d2042/tools/utils.py#L76-L104)

## Result Processing Utilities

The result processing system provides standardized output formatting for model predictions, particularly for image classification tasks. The primary function `print_compiled_model_results()` handles both ImageNet-1K and ImageNet-21K label sets.

### Classification Result Processing

Sources: [tools/utils.py 126-158](https://github.com/tenstorrent/tt-forge-models/blob/0a7d2042/tools/utils.py#L126-L158)

### Label Management

The `load_class_labels()` function supports multiple label file formats:

| Format | File Extension | Processing Method |
| --- | --- | --- |
| JSON | `.json` | Parse as dict, extract ordered labels |
| Text | `.txt` | Read lines, strip whitespace |

The function automatically detects the format based on file extension and processes accordingly.

Sources: [tools/utils.py 115-124](https://github.com/tenstorrent/tt-forge-models/blob/0a7d2042/tools/utils.py#L115-L124)

## Integration Patterns

ModelLoader implementations consistently integrate with the utilities infrastructure through standardized patterns:

### Common Import Pattern

All model loaders import utilities using the relative import pattern:

Sources: [dla/pytorch/loader.py 22](https://github.com/tenstorrent/tt-forge-models/blob/0a7d2042/dla/pytorch/loader.py#L22-L22)[resnext/pytorch/loader.py 23](https://github.com/tenstorrent/tt-forge-models/blob/0a7d2042/resnext/pytorch/loader.py#L23-L23)[regnet/pytorch/loader.py 21](https://github.com/tenstorrent/tt-forge-models/blob/0a7d2042/regnet/pytorch/loader.py#L21-L21)

### Sample Input Loading Pattern

Model loaders use `get_file()` to obtain sample images for testing:

Sources: [densenet/pytorch/loader.py 123-126](https://github.com/tenstorrent/tt-forge-models/blob/0a7d2042/densenet/pytorch/loader.py#L123-L126)[regnet/pytorch/loader.py 139](https://github.com/tenstorrent/tt-forge-models/blob/0a7d2042/regnet/pytorch/loader.py#L139-L139)[mlp_mixer/pytorch/loader.py 191-194](https://github.com/tenstorrent/tt-forge-models/blob/0a7d2042/mlp_mixer/pytorch/loader.py#L191-L194)

### Result Processing Integration

Models integrate result processing through method calls to utility functions:

| Model Type | Integration Method | Function Called |
| --- | --- | --- |
| Image Classification | `print_cls_results()` | `print_compiled_model_results()` |
| Custom Processing | `post_processing()` | Custom logic + utilities |
| Direct Integration | Direct call | `print_compiled_model_results()` |

Sources: [dla/pytorch/loader.py 175-181](https://github.com/tenstorrent/tt-forge-models/blob/0a7d2042/dla/pytorch/loader.py#L175-L181)[ghostnet/pytorch/loader.py 149-168](https://github.com/tenstorrent/tt-forge-models/blob/0a7d2042/ghostnet/pytorch/loader.py#L149-L168)[regnet/pytorch/loader.py 158-170](https://github.com/tenstorrent/tt-forge-models/blob/0a7d2042/regnet/pytorch/loader.py#L158-L170)

## Environment Configuration

The utilities system supports multiple deployment environments through environment variable configuration:

### Environment Variables

| Variable | Purpose | Default Behavior |
| --- | --- | --- |
| `DOCKER_CACHE_ROOT` | Docker container cache location | Auto-sync with S3 |
| `LOCAL_LF_CACHE` | Local development cache | Manual management |
| `IRD_LF_CACHE` | IRD cache server URL | Required for server downloads |

### Error Handling

The system provides specific error messages for different failure scenarios:

Sources: [tools/utils.py 87-104](https://github.com/tenstorrent/tt-forge-models/blob/0a7d2042/tools/utils.py#L87-L104)

## Advanced Features

### State Dictionary Loading

The utilities include a wrapper function for PyTorch's state dictionary loading that removes hash checking:

This function enables loading of model weights without hash verification, useful for custom or modified model weights.

Sources: [tools/utils.py 161-163](https://github.com/tenstorrent/tt-forge-models/blob/0a7d2042/tools/utils.py#L161-L163)

### Read-Only Cache Support

The system handles scenarios where the primary cache is read-only (common in CI environments with shared caches):

1.   Attempts to write to primary cache
2.   Detects write permission failure
3.   Falls back to user's home directory cache
4.   Prints warning message for transparency

This design enables shared cache reading while maintaining individual user write capabilities.

Sources: [tools/utils.py 64-71](https://github.com/tenstorrent/tt-forge-models/blob/0a7d2042/tools/utils.py#L64-L71)

Dismiss
Refresh this wiki

Enter email to refresh
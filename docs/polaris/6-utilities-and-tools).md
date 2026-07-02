---
project: polaris
github: https://github.com/tenstorrent/polaris
deepwiki: https://deepwiki.com/tenstorrent/polaris/6-utilities-and-tools)
---

# Utilities and Tools

Relevant source files
*   [tests/test_common/test_cache.py](https://github.com/tenstorrent/polaris/blob/ac6ed4a3/tests/test_common/test_cache.py)
*   [ttsim/utils/cache.py](https://github.com/tenstorrent/polaris/blob/ac6ed4a3/ttsim/utils/cache.py)
*   [ttsim/utils/common.py](https://github.com/tenstorrent/polaris/blob/ac6ed4a3/ttsim/utils/common.py)
*   [ttsim/utils/readfromurl.py](https://github.com/tenstorrent/polaris/blob/ac6ed4a3/ttsim/utils/readfromurl.py)

This page documents the utility functions and supporting tools that provide common functionality across the Polaris simulation framework. These utilities handle data parsing, file I/O, type conversions, URL content fetching, and caching operations that are used throughout the system.

For performance analysis and benchmarking tools, see [Performance Analysis and Benchmarking](https://deepwiki.com/tenstorrent/polaris/3-performance-analysis-and-benchmarking). For development and testing infrastructure, see [Development and Testing](https://deepwiki.com/tenstorrent/polaris/5-development-and-testing).

## Common Utilities

The `ttsim.utils.common` module provides a comprehensive set of utility functions for data manipulation, file parsing, type conversion, and dynamic class loading that are used throughout the simulation framework.

### Data Structure Utilities

The `dict2obj` class provides a convenient way to convert dictionaries to objects with attribute access:

`class dict2obj:    def __init__(self, d):        for k,v in d.items():            setattr(self, k, dict2obj(v) if isinstance(v, dict) else v)`
This utility is used when configuration data needs to be accessed with dot notation instead of dictionary keys.

### File Parsing and Data I/O

The module provides comprehensive support for reading and writing various file formats:

| Function | Purpose | Supported Formats |
| --- | --- | --- |
| `parse_csv` | Parse CSV files with comment filtering | CSV |
| `parse_xlsx` | Parse Excel worksheets | XLS, XLSX |
| `parse_worksheet` | Unified worksheet parser | CSV, XLS, XLSX |
| `parse_yaml` | Parse single-document YAML files | YAML |
| `parse_multidoc_yaml` | Parse multi-document YAML files | YAML |
| `print_csv` | Write CSV files | CSV |
| `print_json` | Write JSON files | JSON |
| `print_yaml` | Write YAML files | YAML |

#### File Parsing Flow

### Dynamic Class Loading

The framework provides utilities for dynamically loading Python classes from module paths:

*   `get_ttsim_python_class(python_module_path)` - Loads a class from a module path using `importlib`
*   `get_ttsim_functional_instance(python_module_path, python_instance_name, python_instance_cfg)` - Creates an instance of a dynamically loaded class

These functions support the `name@file` syntax for importing specific classes from modules and use `@lru_cache(128)` for performance optimization.

### Type Conversion and Validation

The module provides several type conversion utilities:

| Function | Purpose | Example |
| --- | --- | --- |
| `str_to_bool` | Convert string to boolean | `"true"` → `True` |
| `convert_units` | Convert between units | `convert_units(1, "GB", "MB")` → `1000` |
| `make_tuple` | Create tuples from scalars | `make_tuple(5, 3)` → `(5, 5, 5)` |

The `convert_units` function supports various unit prefixes (T, G, M, K) and types (HZ, B, FLOPS, OPS) with proper conversion factors.

### Argument Validation

Two key functions provide argument validation for operations:

*   `check_known_args(opname, args, default_args)` - Validates that only known arguments are provided
*   `get_kwargs_with_defaults(opname, args, default_args)` - Merges provided arguments with defaults

These utilities ensure type safety and provide clear error messages for invalid operation arguments.

**Sources:**[ttsim/utils/common.py 1-239](https://github.com/tenstorrent/polaris/blob/ac6ed4a3/ttsim/utils/common.py#L1-L239)

## URL Reading and Caching System

The framework includes a sophisticated system for reading content from URLs and local files with built-in caching capabilities.

### FileLocator Architecture

The `FileLocator` class handles both local files and remote URLs transparently:

*   **Local files**: Direct path handling without additional processing
*   **Remote URLs**: HTTP/HTTPS support with automatic caching
*   **Caching**: Automatic content caching to avoid repeated downloads

### Cache Management

The `CacheManager` class provides a simple but effective caching system:

#### Cache Operations

| Method | Purpose | Return Type |
| --- | --- | --- |
| `get_cached_filepath(key)` | Get path to cached file | `Path | None` |
| `get_content(key)` | Read cached content | `str | None` |
| `set_content(key, content)` | Store content in cache | `Path` |

The cache system stores files in `~/.cache/urlcontent/` with keys derived from URLs by replacing special characters.

### URL Content Reading

The `read_from_url` function provides a simple interface for reading content from URLs:

`def read_from_url(url: str, use_cache: bool = True) -> str:    with open(FileLocator(url, use_cache=use_cache).path) as fin:        return fin.read()`
This function supports:

*   **Caching control**: Optional cache bypass with `use_cache=False`
*   **Error handling**: Automatic HTTP error handling via `requests.Response.raise_for_status()`
*   **Transparent access**: Same interface for local files and URLs

**Sources:**[ttsim/utils/readfromurl.py 1-97](https://github.com/tenstorrent/polaris/blob/ac6ed4a3/ttsim/utils/readfromurl.py#L1-L97)[ttsim/utils/cache.py 1-69](https://github.com/tenstorrent/polaris/blob/ac6ed4a3/ttsim/utils/cache.py#L1-L69)

## Utility Integration in Simulation Framework

The utilities are integrated throughout the simulation framework to provide consistent data handling and processing capabilities:

### Error Handling and Logging

The utilities implement comprehensive error handling:

*   **Type validation**: Functions like `str_to_bool` provide clear error messages for invalid inputs
*   **Argument validation**: `check_known_args` ensures only supported arguments are used
*   **HTTP error handling**: `requests.Response.raise_for_status()` for network operations
*   **Warning caching**: `warnonce` function prevents duplicate warnings using `@lru_cache(128)`

### Performance Optimizations

Several performance optimizations are implemented:

*   **Function caching**: `@lru_cache(128)` on `get_ttsim_python_class` and `warnonce`
*   **Content caching**: URL content cached to filesystem to avoid repeated downloads
*   **Lazy loading**: Optional imports (e.g., `openpyxl`) to reduce startup time
*   **Efficient parsing**: CSV comment filtering and Excel row processing optimizations

**Sources:**[ttsim/utils/common.py 134-158](https://github.com/tenstorrent/polaris/blob/ac6ed4a3/ttsim/utils/common.py#L134-L158)[ttsim/utils/readfromurl.py 56-72](https://github.com/tenstorrent/polaris/blob/ac6ed4a3/ttsim/utils/readfromurl.py#L56-L72)[tests/test_common/test_cache.py 1-113](https://github.com/tenstorrent/polaris/blob/ac6ed4a3/tests/test_common/test_cache.py#L1-L113)

Dismiss
Refresh this wiki

Enter email to refresh
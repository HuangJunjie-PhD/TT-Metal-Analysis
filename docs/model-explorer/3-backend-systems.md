---
project: model-explorer
github: https://github.com/tenstorrent/model-explorer
deepwiki: https://deepwiki.com/tenstorrent/model-explorer/3-backend-systems
---

# Backend Systems

Relevant source files
*   [src/builtin-adapter/BUILD](https://github.com/tenstorrent/model-explorer/blob/70d3e3e4/src/builtin-adapter/BUILD)
*   [src/builtin-adapter/model_json_graph_convert.cc](https://github.com/tenstorrent/model-explorer/blob/70d3e3e4/src/builtin-adapter/model_json_graph_convert.cc)
*   [src/builtin-adapter/models_to_json_main.cc](https://github.com/tenstorrent/model-explorer/blob/70d3e3e4/src/builtin-adapter/models_to_json_main.cc)
*   [src/builtin-adapter/translate_helpers.cc](https://github.com/tenstorrent/model-explorer/blob/70d3e3e4/src/builtin-adapter/translate_helpers.cc)
*   [src/builtin-adapter/translate_helpers.h](https://github.com/tenstorrent/model-explorer/blob/70d3e3e4/src/builtin-adapter/translate_helpers.h)
*   [src/builtin-adapter/translations.h](https://github.com/tenstorrent/model-explorer/blob/70d3e3e4/src/builtin-adapter/translations.h)
*   [src/server/package/src/model_explorer/__main__.py](https://github.com/tenstorrent/model-explorer/blob/70d3e3e4/src/server/package/src/model_explorer/__main__.py)
*   [src/server/package/src/model_explorer/adapter.py](https://github.com/tenstorrent/model-explorer/blob/70d3e3e4/src/server/package/src/model_explorer/adapter.py)
*   [src/server/package/src/model_explorer/apis.py](https://github.com/tenstorrent/model-explorer/blob/70d3e3e4/src/server/package/src/model_explorer/apis.py)
*   [src/server/package/src/model_explorer/builtin_pytorch_exportedprogram_adapter.py](https://github.com/tenstorrent/model-explorer/blob/70d3e3e4/src/server/package/src/model_explorer/builtin_pytorch_exportedprogram_adapter.py)
*   [src/server/package/src/model_explorer/config.py](https://github.com/tenstorrent/model-explorer/blob/70d3e3e4/src/server/package/src/model_explorer/config.py)
*   [src/server/package/src/model_explorer/consts.py](https://github.com/tenstorrent/model-explorer/blob/70d3e3e4/src/server/package/src/model_explorer/consts.py)
*   [src/server/package/src/model_explorer/extension_manager.py](https://github.com/tenstorrent/model-explorer/blob/70d3e3e4/src/server/package/src/model_explorer/extension_manager.py)
*   [src/server/package/src/model_explorer/pytorch_exported_program_adater_impl.py](https://github.com/tenstorrent/model-explorer/blob/70d3e3e4/src/server/package/src/model_explorer/pytorch_exported_program_adater_impl.py)

This document covers the backend components of Model Explorer that handle model conversion, server infrastructure, and adapter management. The backend systems transform various ML model formats into standardized JSON representations for visualization and provide REST APIs for the frontend application.

For information about the frontend visualization components, see [Frontend Systems](https://deepwiki.com/tenstorrent/model-explorer/4-frontend-systems). For details about development workflows and testing, see [Development and Testing](https://deepwiki.com/tenstorrent/model-explorer/5-development-and-testing).

## Architecture Overview

The backend systems consist of three main layers: model conversion adapters (C++ components), Python server infrastructure, and extension management. These components work together to provide a unified interface for converting and serving various ML model formats.

**Backend Component Interaction Flow**

Sources: [src/builtin-adapter/BUILD 1-233](https://github.com/tenstorrent/model-explorer/blob/70d3e3e4/src/builtin-adapter/BUILD#L1-L233)[src/server/package/src/model_explorer/extension_manager.py 31-152](https://github.com/tenstorrent/model-explorer/blob/70d3e3e4/src/server/package/src/model_explorer/extension_manager.py#L31-L152)[src/server/package/src/model_explorer/config.py 51-270](https://github.com/tenstorrent/model-explorer/blob/70d3e3e4/src/server/package/src/model_explorer/config.py#L51-L270)

## Model Conversion Adapters

The C++ builtin adapters handle the core model conversion logic, transforming various ML frameworks' formats into standardized JSON graph representations. These adapters are built using Bazel and distributed as a Python wheel package.

### Core Conversion Architecture

**Model Conversion Pipeline**

The system provides two main conversion approaches:

1.   **Direct Conversion**: For TFLite flatbuffers and TensorFlow SavedModels, bypassing MLIR
2.   **MLIR-based Conversion**: For complex models requiring dialect translation and optimization

Key classes and functions:

*   `VisualizeConfig`: Configuration for conversion parameters like `const_element_count_limit`[src/builtin-adapter/visualize_config.h 109-111](https://github.com/tenstorrent/model-explorer/blob/70d3e3e4/src/builtin-adapter/visualize_config.h#L109-L111)
*   `GraphNodeBuilder`: Constructs individual graph nodes with attributes and metadata [src/builtin-adapter/graphnode_builder.h 114-122](https://github.com/tenstorrent/model-explorer/blob/70d3e3e4/src/builtin-adapter/graphnode_builder.h#L114-L122)
*   `FuncOpToSubgraph()`: Converts MLIR function operations to graph subgraphs [src/builtin-adapter/translate_helpers.h 28-30](https://github.com/tenstorrent/model-explorer/blob/70d3e3e4/src/builtin-adapter/translate_helpers.h#L28-L30)
*   `MlirToGraph()`: Main entry point for MLIR module to graph conversion [src/builtin-adapter/translate_helpers.h 32-34](https://github.com/tenstorrent/model-explorer/blob/70d3e3e4/src/builtin-adapter/translate_helpers.h#L32-L34)

Sources: [src/builtin-adapter/translate_helpers.cc 70-1640](https://github.com/tenstorrent/model-explorer/blob/70d3e3e4/src/builtin-adapter/translate_helpers.cc#L70-L1640)[src/builtin-adapter/model_json_graph_convert.cc 81-368](https://github.com/tenstorrent/model-explorer/blob/70d3e3e4/src/builtin-adapter/model_json_graph_convert.cc#L81-L368)[src/builtin-adapter/BUILD 42-56](https://github.com/tenstorrent/model-explorer/blob/70d3e3e4/src/builtin-adapter/BUILD#L42-L56)

### CLI Binary Interface

The `models_to_json` binary provides command-line access to the conversion functionality:

**CLI Conversion Flow**

The binary accepts these key flags:

*   `-i`: Input file path
*   `-o`: Output JSON file path
*   `--const_element_count_limit`: Maximum tensor elements to serialize
*   `--disable_mlir`: Force direct conversion bypass

Sources: [src/builtin-adapter/models_to_json_main.cc 28-100](https://github.com/tenstorrent/model-explorer/blob/70d3e3e4/src/builtin-adapter/models_to_json_main.cc#L28-L100)[src/builtin-adapter/models_to_json_lib.h 134-149](https://github.com/tenstorrent/model-explorer/blob/70d3e3e4/src/builtin-adapter/models_to_json_lib.h#L134-L149)

## Python Server Layer

The Python server provides REST APIs, configuration management, and adapter orchestration. It's built on Flask and manages both builtin C++ adapters and custom Python adapters.

### Configuration Management

The `ModelExplorerConfig` class centralizes model and node data configuration:

**Configuration Architecture**

Key methods and data structures:

*   `model_sources`: List of `ModelSource` objects with URL and optional adapter ID [src/server/package/src/model_explorer/config.py 55-65](https://github.com/tenstorrent/model-explorer/blob/70d3e3e4/src/server/package/src/model_explorer/config.py#L55-L65)
*   `graphs_list`: Cached converted graphs for PyTorch models [src/server/package/src/model_explorer/config.py 57](https://github.com/tenstorrent/model-explorer/blob/70d3e3e4/src/server/package/src/model_explorer/config.py#L57-L57)
*   `add_model_from_pytorch()`: Converts PyTorch `ExportedProgram` to graphs [src/server/package/src/model_explorer/config.py 87-121](https://github.com/tenstorrent/model-explorer/blob/70d3e3e4/src/server/package/src/model_explorer/config.py#L87-L121)

Sources: [src/server/package/src/model_explorer/config.py 51-270](https://github.com/tenstorrent/model-explorer/blob/70d3e3e4/src/server/package/src/model_explorer/config.py#L51-L270)

### API Layer

The main API functions in `apis.py` provide high-level interfaces for visualization:

| Function | Purpose | Key Parameters |
| --- | --- | --- |
| `visualize()` | Visualize models from file paths | `model_paths`, `extensions`, `node_data` |
| `visualize_pytorch()` | Visualize PyTorch ExportedProgram | `exported_program`, `settings` |
| `visualize_from_config()` | Visualize from configuration object | `config`, `cors_host` |

All functions ultimately call `server.start()` with the constructed configuration.

Sources: [src/server/package/src/model_explorer/apis.py 60-220](https://github.com/tenstorrent/model-explorer/blob/70d3e3e4/src/server/package/src/model_explorer/apis.py#L60-L220)

## Extension and Adapter Management

The `ExtensionManager` singleton manages adapter registration, discovery, and execution. It handles both builtin C++ adapters and custom Python adapters.

### Extension Registration System

**Extension Management Architecture**

The system automatically loads builtin adapter modules and caches their registrations:

*   `BUILTIN_ADAPTER_MODULES`: Predefined list of adapter module names [src/server/package/src/model_explorer/extension_manager.py 32-40](https://github.com/tenstorrent/model-explorer/blob/70d3e3e4/src/server/package/src/model_explorer/extension_manager.py#L32-L40)
*   `load_extensions()`: Imports modules and registers extension classes [src/server/package/src/model_explorer/extension_manager.py 62-66](https://github.com/tenstorrent/model-explorer/blob/70d3e3e4/src/server/package/src/model_explorer/extension_manager.py#L62-L66)
*   `run_cmd()`: Dispatches commands to appropriate adapters [src/server/package/src/model_explorer/extension_manager.py 81-96](https://github.com/tenstorrent/model-explorer/blob/70d3e3e4/src/server/package/src/model_explorer/extension_manager.py#L81-L96)

### PyTorch Adapter Implementation

The PyTorch adapter demonstrates the Python adapter pattern:

**PyTorch Conversion Pipeline**

Key implementation details:

*   `get_inputs_map()`: Maps FX graph inputs to tensor values [src/server/package/src/model_explorer/pytorch_exported_program_adater_impl.py 79-101](https://github.com/tenstorrent/model-explorer/blob/70d3e3e4/src/server/package/src/model_explorer/pytorch_exported_program_adater_impl.py#L79-L101)
*   `create_node()`: Converts `torch.fx.Node` to `GraphNode`[src/server/package/src/model_explorer/pytorch_exported_program_adater_impl.py 231-240](https://github.com/tenstorrent/model-explorer/blob/70d3e3e4/src/server/package/src/model_explorer/pytorch_exported_program_adater_impl.py#L231-L240)
*   `get_hierachy()`: Extracts namespace from `nn_module_stack` metadata [src/server/package/src/model_explorer/pytorch_exported_program_adater_impl.py 122-136](https://github.com/tenstorrent/model-explorer/blob/70d3e3e4/src/server/package/src/model_explorer/pytorch_exported_program_adater_impl.py#L122-L136)

Sources: [src/server/package/src/model_explorer/extension_manager.py 31-152](https://github.com/tenstorrent/model-explorer/blob/70d3e3e4/src/server/package/src/model_explorer/extension_manager.py#L31-L152)[src/server/package/src/model_explorer/pytorch_exported_program_adater_impl.py 28-250](https://github.com/tenstorrent/model-explorer/blob/70d3e3e4/src/server/package/src/model_explorer/pytorch_exported_program_adater_impl.py#L28-L250)[src/server/package/src/model_explorer/builtin_pytorch_exportedprogram_adapter.py 30-54](https://github.com/tenstorrent/model-explorer/blob/70d3e3e4/src/server/package/src/model_explorer/builtin_pytorch_exportedprogram_adapter.py#L30-L54)

## Build and Packaging Systems

The backend uses a hybrid build system: Bazel for C++ components and Python setuptools for server packaging.

### Bazel Workspace Configuration

The C++ adapters are built using Bazel with external dependencies:

| Dependency | Purpose |
| --- | --- |
| `@llvm-project//mlir` | MLIR infrastructure and dialects |
| `@org_tensorflow//tensorflow/compiler/mlir` | TensorFlow MLIR components |
| `@stablehlo` | StableHLO dialect support |
| `@shardy//shardy/dialect/sdy` | Shardy sharding dialect |
| `@flatbuffers` | FlatBuffer serialization |

Key build targets:

*   `models_to_json`: CLI binary [src/builtin-adapter/BUILD 151-166](https://github.com/tenstorrent/model-explorer/blob/70d3e3e4/src/builtin-adapter/BUILD#L151-L166)
*   `models_to_json_lib`: Core conversion library [src/builtin-adapter/BUILD 129-149](https://github.com/tenstorrent/model-explorer/blob/70d3e3e4/src/builtin-adapter/BUILD#L129-L149)
*   `translate_helpers`: MLIR translation utilities [src/builtin-adapter/BUILD 6-40](https://github.com/tenstorrent/model-explorer/blob/70d3e3e4/src/builtin-adapter/BUILD#L6-L40)

### Python Package Structure

The server is packaged as `ai-edge-model-explorer` with these key modules:

*   `model_explorer.apis`: Main user-facing API [src/server/package/src/model_explorer/apis.py](https://github.com/tenstorrent/model-explorer/blob/70d3e3e4/src/server/package/src/model_explorer/apis.py)
*   `model_explorer.extension_manager`: Adapter management [src/server/package/src/model_explorer/extension_manager.py](https://github.com/tenstorrent/model-explorer/blob/70d3e3e4/src/server/package/src/model_explorer/extension_manager.py)
*   `model_explorer.config`: Configuration classes [src/server/package/src/model_explorer/config.py](https://github.com/tenstorrent/model-explorer/blob/70d3e3e4/src/server/package/src/model_explorer/config.py)

The C++ adapters are distributed separately as `ai-edge-model-explorer-adapter` wheel.

Sources: [src/builtin-adapter/BUILD 1-233](https://github.com/tenstorrent/model-explorer/blob/70d3e3e4/src/builtin-adapter/BUILD#L1-L233)[src/server/package/src/model_explorer/consts.py 16-23](https://github.com/tenstorrent/model-explorer/blob/70d3e3e4/src/server/package/src/model_explorer/consts.py#L16-L23)

Dismiss
Refresh this wiki

Enter email to refresh
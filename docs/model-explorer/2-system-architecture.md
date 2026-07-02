---
project: model-explorer
github: https://github.com/tenstorrent/model-explorer
deepwiki: https://deepwiki.com/tenstorrent/model-explorer/2-system-architecture
---

# System Architecture

Relevant source files
*   [src/builtin-adapter/BUILD](https://github.com/tenstorrent/model-explorer/blob/70d3e3e4/src/builtin-adapter/BUILD)
*   [src/builtin-adapter/model_json_graph_convert.cc](https://github.com/tenstorrent/model-explorer/blob/70d3e3e4/src/builtin-adapter/model_json_graph_convert.cc)
*   [src/builtin-adapter/models_to_json_main.cc](https://github.com/tenstorrent/model-explorer/blob/70d3e3e4/src/builtin-adapter/models_to_json_main.cc)
*   [src/builtin-adapter/translate_helpers.cc](https://github.com/tenstorrent/model-explorer/blob/70d3e3e4/src/builtin-adapter/translate_helpers.cc)
*   [src/builtin-adapter/translate_helpers.h](https://github.com/tenstorrent/model-explorer/blob/70d3e3e4/src/builtin-adapter/translate_helpers.h)
*   [src/builtin-adapter/translations.h](https://github.com/tenstorrent/model-explorer/blob/70d3e3e4/src/builtin-adapter/translations.h)
*   [src/server/package/pyproject.toml](https://github.com/tenstorrent/model-explorer/blob/70d3e3e4/src/server/package/pyproject.toml)
*   [src/server/package/src/model_explorer/web_app/index.html](https://github.com/tenstorrent/model-explorer/blob/70d3e3e4/src/server/package/src/model_explorer/web_app/index.html)
*   [src/ui/src/components/visualizer/app_service.ts](https://github.com/tenstorrent/model-explorer/blob/70d3e3e4/src/ui/src/components/visualizer/app_service.ts)
*   [src/ui/src/components/visualizer/common/types.ts](https://github.com/tenstorrent/model-explorer/blob/70d3e3e4/src/ui/src/components/visualizer/common/types.ts)
*   [src/ui/src/components/visualizer/common/worker_events.ts](https://github.com/tenstorrent/model-explorer/blob/70d3e3e4/src/ui/src/components/visualizer/common/worker_events.ts)
*   [src/ui/src/components/visualizer/model_graph_visualizer.ts](https://github.com/tenstorrent/model-explorer/blob/70d3e3e4/src/ui/src/components/visualizer/model_graph_visualizer.ts)
*   [src/ui/src/components/visualizer/webgl_renderer.ts](https://github.com/tenstorrent/model-explorer/blob/70d3e3e4/src/ui/src/components/visualizer/webgl_renderer.ts)
*   [src/ui/src/components/visualizer/webgl_renderer_threejs_service.ts](https://github.com/tenstorrent/model-explorer/blob/70d3e3e4/src/ui/src/components/visualizer/webgl_renderer_threejs_service.ts)
*   [src/ui/src/components/visualizer/worker/graph_expander.ts](https://github.com/tenstorrent/model-explorer/blob/70d3e3e4/src/ui/src/components/visualizer/worker/graph_expander.ts)
*   [src/ui/src/components/visualizer/worker/worker.ts](https://github.com/tenstorrent/model-explorer/blob/70d3e3e4/src/ui/src/components/visualizer/worker/worker.ts)

This document provides a comprehensive overview of the Model Explorer system architecture, explaining how the backend model conversion, Python server infrastructure, and frontend visualization components work together to create an interactive model graph visualizer and debugger.

For detailed information about specific backend components, see [Backend Systems](https://deepwiki.com/tenstorrent/model-explorer/3-backend-systems). For frontend implementation details, see [Frontend Systems](https://deepwiki.com/tenstorrent/model-explorer/4-frontend-systems). For development and testing procedures, see [Development and Testing](https://deepwiki.com/tenstorrent/model-explorer/5-development-and-testing).

## Architecture Overview

Model Explorer is a multi-layered system that converts machine learning models from various formats into interactive visualizations. The system consists of four main architectural layers that process models from input to visualization.

### High-Level System Components

**System Architecture Components Diagram**

Sources: [src/builtin-adapter/BUILD 1-233](https://github.com/tenstorrent/model-explorer/blob/70d3e3e4/src/builtin-adapter/BUILD#L1-L233)[src/server/package/pyproject.toml 1-40](https://github.com/tenstorrent/model-explorer/blob/70d3e3e4/src/server/package/pyproject.toml#L1-L40)[src/ui/src/components/visualizer/model_graph_visualizer.ts 1-85](https://github.com/tenstorrent/model-explorer/blob/70d3e3e4/src/ui/src/components/visualizer/model_graph_visualizer.ts#L1-L85)[src/ui/src/components/visualizer/app_service.ts 1-80](https://github.com/tenstorrent/model-explorer/blob/70d3e3e4/src/ui/src/components/visualizer/app_service.ts#L1-L80)[src/ui/src/components/visualizer/webgl_renderer.ts 1-234](https://github.com/tenstorrent/model-explorer/blob/70d3e3e4/src/ui/src/components/visualizer/webgl_renderer.ts#L1-L234)[src/ui/src/components/visualizer/worker/worker.ts 1-50](https://github.com/tenstorrent/model-explorer/blob/70d3e3e4/src/ui/src/components/visualizer/worker/worker.ts#L1-L50)

## Backend Model Processing Subsystem

The backend processing layer handles conversion of various machine learning model formats into standardized JSON representations. This subsystem is implemented primarily in C++ for performance.

### Model Conversion Pipeline

**Backend Model Conversion Pipeline**

The `models_to_json` binary serves as the main entry point for model conversion, accepting various flags for configuration:

| Component | Purpose | Key Files |
| --- | --- | --- |
| `translate_helpers` | Core MLIR-to-graph conversion logic | [src/builtin-adapter/translate_helpers.cc 1-40](https://github.com/tenstorrent/model-explorer/blob/70d3e3e4/src/builtin-adapter/translate_helpers.cc#L1-L40) |
| `model_json_graph_convert` | MLIR module processing and conversion | [src/builtin-adapter/model_json_graph_convert.cc 1-80](https://github.com/tenstorrent/model-explorer/blob/70d3e3e4/src/builtin-adapter/model_json_graph_convert.cc#L1-L80) |
| `models_to_json` | CLI entry point with flag parsing | [src/builtin-adapter/models_to_json_main.cc 39-100](https://github.com/tenstorrent/model-explorer/blob/70d3e3e4/src/builtin-adapter/models_to_json_main.cc#L39-L100) |
| `direct_*_to_json_graph_convert` | Direct conversion without MLIR | [src/builtin-adapter/BUILD 168-232](https://github.com/tenstorrent/model-explorer/blob/70d3e3e4/src/builtin-adapter/BUILD#L168-L232) |

Sources: [src/builtin-adapter/BUILD 41-106](https://github.com/tenstorrent/model-explorer/blob/70d3e3e4/src/builtin-adapter/BUILD#L41-L106)[src/builtin-adapter/translate_helpers.cc 70-125](https://github.com/tenstorrent/model-explorer/blob/70d3e3e4/src/builtin-adapter/translate_helpers.cc#L70-L125)[src/builtin-adapter/model_json_graph_convert.cc 81-243](https://github.com/tenstorrent/model-explorer/blob/70d3e3e4/src/builtin-adapter/model_json_graph_convert.cc#L81-L243)[src/builtin-adapter/models_to_json_main.cc 28-65](https://github.com/tenstorrent/model-explorer/blob/70d3e3e4/src/builtin-adapter/models_to_json_main.cc#L28-L65)

## Python Server Subsystem

The Python server layer provides REST APIs and manages the web application serving. It acts as the bridge between the backend conversion utilities and the frontend visualization.

### Server Architecture Components

**Python Server Architecture**

The server configuration is defined in `pyproject.toml`:

*   **Package Name**: `ai-edge-model-explorer`
*   **Entry Point**: `model-explorer` command maps to `model_explorer.cmdline:main`
*   **Web Assets**: All files in `web_app` directory are included via `[tool.setuptools.package-data]`
*   **Platform Dependencies**: The adapter is excluded on Windows via `sys_platform != 'win32'`

Sources: [src/server/package/pyproject.toml 1-40](https://github.com/tenstorrent/model-explorer/blob/70d3e3e4/src/server/package/pyproject.toml#L1-L40)[src/server/package/src/model_explorer/web_app/index.html 1-32](https://github.com/tenstorrent/model-explorer/blob/70d3e3e4/src/server/package/src/model_explorer/web_app/index.html#L1-L32)

## Frontend Application Subsystem

The frontend is an Angular-based single-page application that provides interactive model graph visualization using WebGL for high-performance rendering.

### Frontend Component Hierarchy

**Frontend Component Architecture**

The `AppService` acts as the central coordination point, managing:

*   **Graph Collections**: Input model data via `curGraphCollections` signal
*   **Model Graphs**: Processed graph data via `modelGraphs` signal
*   **Pane Management**: Split-pane layout via `panes` signal
*   **Node Selection**: User interaction state via `selectedNode` signal
*   **Command Processing**: Inter-component communication via `command` Subject

Sources: [src/ui/src/components/visualizer/model_graph_visualizer.ts 67-84](https://github.com/tenstorrent/model-explorer/blob/70d3e3e4/src/ui/src/components/visualizer/model_graph_visualizer.ts#L67-L84)[src/ui/src/components/visualizer/app_service.ts 79-128](https://github.com/tenstorrent/model-explorer/blob/70d3e3e4/src/ui/src/components/visualizer/app_service.ts#L79-L128)[src/ui/src/components/visualizer/webgl_renderer.ts 207-234](https://github.com/tenstorrent/model-explorer/blob/70d3e3e4/src/ui/src/components/visualizer/webgl_renderer.ts#L207-L234)[src/ui/src/components/visualizer/webgl_renderer_threejs_service.ts 37-61](https://github.com/tenstorrent/model-explorer/blob/70d3e3e4/src/ui/src/components/visualizer/webgl_renderer_threejs_service.ts#L37-L61)

## Graph Processing Pipeline

The graph processing pipeline handles the computationally intensive tasks of graph layout, expansion/collapse operations, and node positioning using Web Workers for non-blocking performance.

### Worker-Based Processing Architecture

**Worker-Based Graph Processing Pipeline**

The worker system handles several types of processing requests:

| Event Type | Purpose | Handler |
| --- | --- | --- |
| `PROCESS_GRAPH_REQ` | Convert input graph to model graph | `handleProcessGraph()` |
| `EXPAND_OR_COLLAPSE_GROUP_NODE_REQ` | Handle node expansion/collapse | `handleExpandGroupNode()` / `handleCollapseGroupNode()` |
| `RELAYOUT_GRAPH_REQ` | Re-layout graph maintaining states | `handleReLayoutGraph()` |
| `LOCATE_NODE_REQ` | Find and reveal specific nodes | `handleLocateNode()` |

The worker maintains a cache of model graphs indexed by `<rendererId + ModelGraphId>` to enable efficient processing without data transfer overhead.

Sources: [src/ui/src/components/visualizer/worker/worker.ts 50-220](https://github.com/tenstorrent/model-explorer/blob/70d3e3e4/src/ui/src/components/visualizer/worker/worker.ts#L50-L220)[src/ui/src/components/visualizer/worker/graph_expander.ts 44-62](https://github.com/tenstorrent/model-explorer/blob/70d3e3e4/src/ui/src/components/visualizer/worker/graph_expander.ts#L44-L62)[src/ui/src/components/visualizer/common/worker_events.ts 31-46](https://github.com/tenstorrent/model-explorer/blob/70d3e3e4/src/ui/src/components/visualizer/common/worker_events.ts#L31-L46)

## Data Flow and Communication

The system uses a combination of signals, observables, and worker message passing to coordinate between components while maintaining responsive user interactions.

### Inter-Component Communication Patterns

**Data Flow Sequence Diagram**

The communication patterns follow these principles:

*   **Reactive State Management**: Uses Angular signals for automatic UI updates
*   **Worker Isolation**: Heavy processing isolated to prevent UI blocking
*   **Event-Driven Architecture**: Component communication via Subject observables
*   **Caching Strategy**: Worker maintains model graph cache to minimize data transfer

Sources: [src/ui/src/components/visualizer/model_graph_visualizer.ts 144-226](https://github.com/tenstorrent/model-explorer/blob/70d3e3e4/src/ui/src/components/visualizer/model_graph_visualizer.ts#L144-L226)[src/ui/src/components/visualizer/app_service.ts 139-146](https://github.com/tenstorrent/model-explorer/blob/70d3e3e4/src/ui/src/components/visualizer/app_service.ts#L139-L146)[src/ui/src/components/visualizer/worker/worker.ts 55-80](https://github.com/tenstorrent/model-explorer/blob/70d3e3e4/src/ui/src/components/visualizer/worker/worker.ts#L55-L80)[src/ui/src/components/visualizer/webgl_renderer.ts 415-474](https://github.com/tenstorrent/model-explorer/blob/70d3e3e4/src/ui/src/components/visualizer/webgl_renderer.ts#L415-L474)

Dismiss
Refresh this wiki

Enter email to refresh
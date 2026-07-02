---
project: model-explorer
github: https://github.com/tenstorrent/model-explorer
deepwiki: https://deepwiki.com/tenstorrent/model-explorer/4-frontend-systems
---

# Frontend Systems

Relevant source files
*   [src/ui/src/common/extension_command.ts](https://github.com/tenstorrent/model-explorer/blob/70d3e3e4/src/ui/src/common/extension_command.ts)
*   [src/ui/src/common/model_loader_service_interface.ts](https://github.com/tenstorrent/model-explorer/blob/70d3e3e4/src/ui/src/common/model_loader_service_interface.ts)
*   [src/ui/src/components/visualizer/app_service.ts](https://github.com/tenstorrent/model-explorer/blob/70d3e3e4/src/ui/src/components/visualizer/app_service.ts)
*   [src/ui/src/components/visualizer/common/types.ts](https://github.com/tenstorrent/model-explorer/blob/70d3e3e4/src/ui/src/components/visualizer/common/types.ts)
*   [src/ui/src/components/visualizer/common/worker_events.ts](https://github.com/tenstorrent/model-explorer/blob/70d3e3e4/src/ui/src/components/visualizer/common/worker_events.ts)
*   [src/ui/src/components/visualizer/graph_selector.ng.html](https://github.com/tenstorrent/model-explorer/blob/70d3e3e4/src/ui/src/components/visualizer/graph_selector.ng.html)
*   [src/ui/src/components/visualizer/graph_selector.ts](https://github.com/tenstorrent/model-explorer/blob/70d3e3e4/src/ui/src/components/visualizer/graph_selector.ts)
*   [src/ui/src/components/visualizer/model_graph_visualizer.ts](https://github.com/tenstorrent/model-explorer/blob/70d3e3e4/src/ui/src/components/visualizer/model_graph_visualizer.ts)
*   [src/ui/src/components/visualizer/webgl_renderer.ts](https://github.com/tenstorrent/model-explorer/blob/70d3e3e4/src/ui/src/components/visualizer/webgl_renderer.ts)
*   [src/ui/src/components/visualizer/webgl_renderer_threejs_service.ts](https://github.com/tenstorrent/model-explorer/blob/70d3e3e4/src/ui/src/components/visualizer/webgl_renderer_threejs_service.ts)
*   [src/ui/src/components/visualizer/worker/graph_expander.ts](https://github.com/tenstorrent/model-explorer/blob/70d3e3e4/src/ui/src/components/visualizer/worker/graph_expander.ts)
*   [src/ui/src/components/visualizer/worker/worker.ts](https://github.com/tenstorrent/model-explorer/blob/70d3e3e4/src/ui/src/components/visualizer/worker/worker.ts)
*   [src/ui/src/services/model_loader_service.ts](https://github.com/tenstorrent/model-explorer/blob/70d3e3e4/src/ui/src/services/model_loader_service.ts)

This document covers the Angular-based frontend application that provides the interactive user interface for Model Explorer. The frontend is responsible for loading models, rendering graph visualizations using WebGL, and managing user interactions. For information about backend model conversion and server APIs, see [Backend Systems](https://deepwiki.com/tenstorrent/model-explorer/3-backend-systems). For build systems and packaging, see [Frontend Build and Configuration](https://deepwiki.com/tenstorrent/model-explorer/4.4-frontend-build-and-configuration).

## System Architecture

The frontend follows a layered architecture with clear separation between services, visualization, and UI components:

**Sources:**[src/ui/src/components/visualizer/model_graph_visualizer.ts 68-84](https://github.com/tenstorrent/model-explorer/blob/70d3e3e4/src/ui/src/components/visualizer/model_graph_visualizer.ts#L68-L84)[src/ui/src/components/visualizer/app_service.ts 79-80](https://github.com/tenstorrent/model-explorer/blob/70d3e3e4/src/ui/src/components/visualizer/app_service.ts#L79-L80)[src/ui/src/components/visualizer/webgl_renderer.ts 208-234](https://github.com/tenstorrent/model-explorer/blob/70d3e3e4/src/ui/src/components/visualizer/webgl_renderer.ts#L208-L234)[src/ui/src/components/visualizer/worker/worker.ts 55-220](https://github.com/tenstorrent/model-explorer/blob/70d3e3e4/src/ui/src/components/visualizer/worker/worker.ts#L55-L220)

## Core Services Layer

The core services layer manages application state, model loading, and configuration through a set of injectable Angular services:

**Sources:**[src/ui/src/components/visualizer/app_service.ts 79-80](https://github.com/tenstorrent/model-explorer/blob/70d3e3e4/src/ui/src/components/visualizer/app_service.ts#L79-L80)[src/ui/src/services/model_loader_service.ts 72-75](https://github.com/tenstorrent/model-explorer/blob/70d3e3e4/src/ui/src/services/model_loader_service.ts#L72-L75)[src/ui/src/common/model_loader_service_interface.ts 34-49](https://github.com/tenstorrent/model-explorer/blob/70d3e3e4/src/ui/src/common/model_loader_service_interface.ts#L34-L49)

### AppService

The `AppService` acts as the central state coordinator using Angular signals for reactive state management:

| Signal | Type | Purpose |
| --- | --- | --- |
| `curGraphCollections` | `GraphCollection[]` | Loaded graph collections |
| `modelGraphs` | `ModelGraph[]` | Processed model graphs |
| `panes` | `Pane[]` | Split pane configurations |
| `selectedPaneId` | `string` | Currently active pane |
| `config` | `VisualizerConfig` | Application configuration |

Key methods include `selectGraphInCurrentPane()`, `openGraphInSplitPane()`, and `selectNode()` for managing graph navigation and selection.

**Sources:**[src/ui/src/components/visualizer/app_service.ts 81-128](https://github.com/tenstorrent/model-explorer/blob/70d3e3e4/src/ui/src/components/visualizer/app_service.ts#L81-L128)

### ModelLoaderService

The `ModelLoaderService` handles model loading from various sources and communicates with backend adapters:

The service supports multiple model types: `LOCAL`, `FILE_PATH`, and `GRAPH_JSONS_FROM_SERVER`, with different processing paths for each.

**Sources:**[src/ui/src/services/model_loader_service.ts 72-75](https://github.com/tenstorrent/model-explorer/blob/70d3e3e4/src/ui/src/services/model_loader_service.ts#L72-L75)[src/ui/src/common/model_loader_service_interface.ts 35-49](https://github.com/tenstorrent/model-explorer/blob/70d3e3e4/src/ui/src/common/model_loader_service_interface.ts#L35-L49)[src/ui/src/services/model_loader_service.ts 199-338](https://github.com/tenstorrent/model-explorer/blob/70d3e3e4/src/ui/src/services/model_loader_service.ts#L199-L338)

## Visualization Engine

The visualization engine provides high-performance WebGL-based rendering using Three.js:

**Sources:**[src/ui/src/components/visualizer/webgl_renderer.ts 218-230](https://github.com/tenstorrent/model-explorer/blob/70d3e3e4/src/ui/src/components/visualizer/webgl_renderer.ts#L218-L230)[src/ui/src/components/visualizer/webgl_renderer_threejs_service.ts 37-38](https://github.com/tenstorrent/model-explorer/blob/70d3e3e4/src/ui/src/components/visualizer/webgl_renderer_threejs_service.ts#L37-L38)

### WebglRenderer Component

The `WebglRenderer` is the main rendering component that orchestrates WebGL rendering:

| Input Property | Type | Purpose |
| --- | --- | --- |
| `modelGraph` | `ModelGraph` | Graph data to render |
| `rendererId` | `string` | Unique renderer identifier |
| `paneId` | `string` | Associated pane ID |
| `rootNodeId` | `string?` | Root node for subgraph rendering |

The component manages rendering elements through `RenderElement[]` arrays and handles user interactions like zooming, panning, and node selection.

**Sources:**[src/ui/src/components/visualizer/webgl_renderer.ts 235-254](https://github.com/tenstorrent/model-explorer/blob/70d3e3e4/src/ui/src/components/visualizer/webgl_renderer.ts#L235-L254)[src/ui/src/components/visualizer/webgl_renderer.ts 359-390](https://github.com/tenstorrent/model-explorer/blob/70d3e3e4/src/ui/src/components/visualizer/webgl_renderer.ts#L359-L390)

### Three.js Integration

The `WebglRendererThreejsService` manages Three.js scene setup and camera controls:

The service uses orthographic projection for 2D graph rendering and integrates with D3.js for zoom and pan behaviors.

**Sources:**[src/ui/src/components/visualizer/webgl_renderer_threejs_service.ts 38-60](https://github.com/tenstorrent/model-explorer/blob/70d3e3e4/src/ui/src/components/visualizer/webgl_renderer_threejs_service.ts#L38-L60)[src/ui/src/components/visualizer/webgl_renderer_threejs_service.ts 199-283](https://github.com/tenstorrent/model-explorer/blob/70d3e3e4/src/ui/src/components/visualizer/webgl_renderer_threejs_service.ts#L199-L283)

## User Interface Components

The UI layer consists of Angular components that provide the interactive interface:

**Sources:**[src/ui/src/components/visualizer/model_graph_visualizer.ts 68-83](https://github.com/tenstorrent/model-explorer/blob/70d3e3e4/src/ui/src/components/visualizer/model_graph_visualizer.ts#L68-L83)[src/ui/src/components/visualizer/graph_selector.ts 71-86](https://github.com/tenstorrent/model-explorer/blob/70d3e3e4/src/ui/src/components/visualizer/graph_selector.ts#L71-L86)

### GraphSelector Component

The `GraphSelector` provides graph selection functionality with search and filtering:

The component displays a dropdown with graph collections, node counts, and actions for opening graphs in split panes.

**Sources:**[src/ui/src/components/visualizer/graph_selector.ts 87-89](https://github.com/tenstorrent/model-explorer/blob/70d3e3e4/src/ui/src/components/visualizer/graph_selector.ts#L87-L89)[src/ui/src/components/visualizer/graph_selector.ts 97-150](https://github.com/tenstorrent/model-explorer/blob/70d3e3e4/src/ui/src/components/visualizer/graph_selector.ts#L97-L150)[src/ui/src/components/visualizer/graph_selector.ng.html 19-99](https://github.com/tenstorrent/model-explorer/blob/70d3e3e4/src/ui/src/components/visualizer/graph_selector.ng.html#L19-L99)

### ModelGraphVisualizer Main Component

The `ModelGraphVisualizer` serves as the root component coordinating all frontend systems:

| Input | Type | Purpose |
| --- | --- | --- |
| `graphCollections` | `GraphCollection[]` | Model data to visualize |
| `config` | `VisualizerConfig?` | Visualization configuration |
| `initialUiState` | `VisualizerUiState?` | Restore previous state |
| `nodeDataSources` | `string[]` | Additional data sources |

The component handles initialization, keyboard shortcuts, drag-and-drop file handling, and coordinates between services.

**Sources:**[src/ui/src/components/visualizer/model_graph_visualizer.ts 85-134](https://github.com/tenstorrent/model-explorer/blob/70d3e3e4/src/ui/src/components/visualizer/model_graph_visualizer.ts#L85-L134)[src/ui/src/components/visualizer/model_graph_visualizer.ts 228-259](https://github.com/tenstorrent/model-explorer/blob/70d3e3e4/src/ui/src/components/visualizer/model_graph_visualizer.ts#L228-L259)

## Background Processing System

Heavy graph processing operations are offloaded to web workers to maintain UI responsiveness:

**Sources:**[src/ui/src/components/visualizer/worker/worker.ts 50-220](https://github.com/tenstorrent/model-explorer/blob/70d3e3e4/src/ui/src/components/visualizer/worker/worker.ts#L50-L220)[src/ui/src/components/visualizer/worker/graph_expander.ts 46-61](https://github.com/tenstorrent/model-explorer/blob/70d3e3e4/src/ui/src/components/visualizer/worker/graph_expander.ts#L46-L61)

### Worker Event System

Communication between main thread and worker uses typed message events:

Each request includes a `rendererId` for proper response routing and `ModelGraph` caching.

**Sources:**[src/ui/src/components/visualizer/common/worker_events.ts 32-46](https://github.com/tenstorrent/model-explorer/blob/70d3e3e4/src/ui/src/components/visualizer/common/worker_events.ts#L32-L46)[src/ui/src/components/visualizer/worker/worker.ts 55-80](https://github.com/tenstorrent/model-explorer/blob/70d3e3e4/src/ui/src/components/visualizer/worker/worker.ts#L55-L80)

### Graph Processing Pipeline

The worker handles several key operations:

1.   **Graph Processing** - Converts input `Graph` to `ModelGraph` with layout data
2.   **Node Expansion** - Expands/collapses group nodes and recalculates layouts
3.   **Node Location** - Finds and reveals specific nodes in the graph
4.   **Layout Calculation** - Uses Dagre algorithm for automatic graph layout

**Sources:**[src/ui/src/components/visualizer/worker/worker.ts 222-282](https://github.com/tenstorrent/model-explorer/blob/70d3e3e4/src/ui/src/components/visualizer/worker/worker.ts#L222-L282)[src/ui/src/components/visualizer/worker/graph_expander.ts 63-132](https://github.com/tenstorrent/model-explorer/blob/70d3e3e4/src/ui/src/components/visualizer/worker/graph_expander.ts#L63-L132)

Dismiss
Refresh this wiki

Enter email to refresh
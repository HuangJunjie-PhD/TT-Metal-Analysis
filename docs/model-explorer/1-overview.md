---
project: model-explorer
github: https://github.com/tenstorrent/model-explorer
deepwiki: https://deepwiki.com/tenstorrent/model-explorer/1-overview
---

# Overview

Relevant source files
*   [README.md](https://github.com/tenstorrent/model-explorer/blob/70d3e3e4/README.md?plain=1)
*   [screenshots/main_ui.png](https://github.com/tenstorrent/model-explorer/blob/70d3e3e4/screenshots/main_ui.png)
*   [src/server/package/README.md](https://github.com/tenstorrent/model-explorer/blob/70d3e3e4/src/server/package/README.md?plain=1)

## Purpose and Scope

This document provides a high-level overview of the Model Explorer repository, covering its purpose as a machine learning model graph visualizer and debugger, its core architectural components, and how the major systems work together. For detailed information about specific subsystems, see [System Architecture](https://deepwiki.com/tenstorrent/model-explorer/2-system-architecture) for architectural details, [Backend Systems](https://deepwiki.com/tenstorrent/model-explorer/3-backend-systems) for server-side components, [Frontend Systems](https://deepwiki.com/tenstorrent/model-explorer/4-frontend-systems) for user interface components, and [Development and Testing](https://deepwiki.com/tenstorrent/model-explorer/5-development-and-testing) for development workflows.

## System Purpose and Capabilities

Model Explorer is a comprehensive tool for visualizing and debugging machine learning model graphs across multiple frameworks. It provides an intuitive hierarchical visualization that organizes model operations into nested layers, allowing users to dynamically expand or collapse these layers for exploration and analysis.

The system supports multiple model formats including TFLite, TensorFlow, TFJS, MLIR, and PyTorch Exported Programs. It offers an extension framework that enables developers to add support for additional model formats through custom adapters.

**Key Capabilities:**

*   Hierarchical graph visualization with expandable/collapsible layers
*   Input/output operation highlighting
*   Metadata overlay on graph nodes
*   Interactive layer display in pop-ups
*   Search functionality for finding specific operations
*   Identical layer detection and visualization
*   GPU-accelerated graph rendering using WebGL
*   Command-line interface and Python API access
*   Web-based interface accessible through browser

**Distribution Packages:**

*   `ai-edge-model-explorer` - Python package with CLI and server
*   `ai-edge-model-explorer-visualizer` - NPM package for embedding visualization

Sources: [README.md 1-58](https://github.com/tenstorrent/model-explorer/blob/70d3e3e4/README.md?plain=1#L1-L58)[src/server/package/README.md 1-27](https://github.com/tenstorrent/model-explorer/blob/70d3e3e4/src/server/package/README.md?plain=1#L1-L27)

## High-Level System Architecture

Model Explorer follows a three-tier architecture consisting of backend model processing, Python server infrastructure, and frontend visualization components.

### System Overview Diagram

The architecture separates concerns into distinct layers:

1.   **Backend Processing Layer** - Handles conversion of various model formats into standardized JSON representations
2.   **Python Server Layer** - Provides REST APIs, manages extensions, and serves the web application
3.   **Frontend Application Layer** - Delivers the interactive user interface built with Angular and TypeScript
4.   **Graph Processing Pipeline** - Performs computational work for graph layout and rendering using web workers

Sources: [README.md 1-58](https://github.com/tenstorrent/model-explorer/blob/70d3e3e4/README.md?plain=1#L1-L58)[src/server/package/README.md 1-27](https://github.com/tenstorrent/model-explorer/blob/70d3e3e4/src/server/package/README.md?plain=1#L1-L27)

## Core Component Integration

The following diagram shows how the main code entities work together to process models from input to visualization.

### Component Integration Flow

This diagram illustrates the flow from model input through the various adapters, server processing, and frontend services to the final WebGL visualization. The `ExtensionManager` serves as the central registry for model format adapters, while the Flask application provides the HTTP interface. On the frontend, the `AppService` coordinates between model loading and visualization components.

Sources: [README.md 1-58](https://github.com/tenstorrent/model-explorer/blob/70d3e3e4/README.md?plain=1#L1-L58)[src/server/package/README.md 1-27](https://github.com/tenstorrent/model-explorer/blob/70d3e3e4/src/server/package/README.md?plain=1#L1-L27)

## Supported Model Formats and Adapters

Model Explorer supports multiple machine learning model formats through a flexible adapter system:

| Format | Adapter Type | Primary Use Case |
| --- | --- | --- |
| TFLite | Builtin Adapter | Mobile/edge inference models |
| TensorFlow | Builtin Adapter | TensorFlow SavedModel and frozen graphs |
| TFJS | Builtin Adapter | JavaScript/web deployment models |
| MLIR | Builtin Adapter | Compiler intermediate representations |
| PyTorch | Extension Adapter | PyTorch Exported Program format |
| HLO/XLA | Specialized Adapter | Google XLA compiler representations |

The system provides an extension framework that allows developers to create custom adapters for additional model formats. Community-contributed adapters are available, such as the ONNX adapter maintained by external contributors.

Sources: [README.md 9-11](https://github.com/tenstorrent/model-explorer/blob/70d3e3e4/README.md?plain=1#L9-L11)[README.md 46-48](https://github.com/tenstorrent/model-explorer/blob/70d3e3e4/README.md?plain=1#L46-L48)

## Key Features and Functionality

Model Explorer provides comprehensive model analysis capabilities:

**Graph Visualization:**

*   Hierarchical layer organization with expand/collapse functionality
*   GPU-accelerated rendering using WebGL for smooth interaction with large models
*   Interactive node selection and metadata display
*   Input/output operation highlighting for data flow analysis

**Analysis Tools:**

*   Search functionality to locate specific operations or layers
*   Identical layer detection to identify repeated patterns
*   Metadata overlay system for displaying operation attributes
*   Interactive pop-ups with detailed layer information

**Integration Options:**

*   Command-line interface via `model-explorer` CLI command
*   Python API for programmatic access and Jupyter notebook integration
*   Web-based interface accessible through browser
*   NPM package for embedding visualization in custom applications

**Extensibility:**

*   Extension framework for adding new model format support
*   Plugin architecture for custom analysis tools
*   REST API for external tool integration

Sources: [README.md 3-11](https://github.com/tenstorrent/model-explorer/blob/70d3e3e4/README.md?plain=1#L3-L11)[README.md 17-22](https://github.com/tenstorrent/model-explorer/blob/70d3e3e4/README.md?plain=1#L17-L22)[README.md 39-44](https://github.com/tenstorrent/model-explorer/blob/70d3e3e4/README.md?plain=1#L39-L44)

Dismiss
Refresh this wiki

Enter email to refresh
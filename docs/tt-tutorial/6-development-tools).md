---
project: tt-tutorial
github: https://github.com/tenstorrent/tt-tutorial
deepwiki: https://deepwiki.com/tenstorrent/tt-tutorial/6-development-tools)
---

# Development Tools

Relevant source files
*   [model-explorer/README.md](https://github.com/tenstorrent/tt-tutorial/blob/389c542c/model-explorer/README.md?plain=1)
*   [tt-nn visualizer/README.md](https://github.com/tenstorrent/tt-tutorial/blob/389c542c/tt-nn%20visualizer/README.md?plain=1)

## Purpose and Scope

This document provides an overview of the development tools available in the Tenstorrent ecosystem for visualization, debugging, and analysis of models and operations. These tools support the entire development lifecycle from model exploration and debugging to performance optimization and system analysis.

For detailed guides on specific tools, see [Model Explorer](https://deepwiki.com/tenstorrent/tt-tutorial/6.1-model-explorer) and [TT-NN Visualizer](https://deepwiki.com/tenstorrent/tt-tutorial/6.2-tt-nn-visualizer). For performance analysis and system optimization techniques, see [Performance and Optimization](https://deepwiki.com/tenstorrent/tt-tutorial/7-performance-and-optimization).

## Development Tools Ecosystem

The Tenstorrent development ecosystem includes comprehensive visualization and analysis tools that integrate across different layers of the software stack. These tools provide visibility into model structure, execution flow, memory usage, and performance characteristics.

### Development Tools Architecture

Sources: [model-explorer/README.md](https://github.com/tenstorrent/tt-tutorial/blob/389c542c/model-explorer/README.md?plain=1)[tt-nn visualizer/README.md](https://github.com/tenstorrent/tt-tutorial/blob/389c542c/tt-nn%20visualizer/README.md?plain=1)

## Tool Categories and Functions

The development tools are organized into specialized categories based on their primary analysis focus and target audience.

### Visualization Tools Feature Matrix

| Tool | Graph Visualization | Memory Analysis | Performance Metrics | Interactive Exploration | Remote Access |
| --- | --- | --- | --- | --- | --- |
| Model Explorer | ✓ Hierarchical | - | - | ✓ Expand/Collapse | - |
| TT-NN Visualizer | ✓ Operation Flow | ✓ Buffer Overview | ✓ Execution Analysis | ✓ Multi-instance | ✓ SSH/File Loading |

### Tool Capabilities Overview

Sources: [model-explorer/README.md 12](https://github.com/tenstorrent/tt-tutorial/blob/389c542c/model-explorer/README.md?plain=1#L12-L12)[tt-nn visualizer/README.md 12](https://github.com/tenstorrent/tt-tutorial/blob/389c542c/tt-nn%20visualizer/README.md?plain=1#L12-L12)

## Development Workflow Integration

The development tools integrate at different stages of the model development and optimization workflow, providing targeted analysis capabilities for specific development phases.

### Tool Integration in Development Lifecycle

Sources: [model-explorer/README.md](https://github.com/tenstorrent/tt-tutorial/blob/389c542c/model-explorer/README.md?plain=1)[tt-nn visualizer/README.md](https://github.com/tenstorrent/tt-tutorial/blob/389c542c/tt-nn%20visualizer/README.md?plain=1)

## Tool Selection Guide

### Model Explorer Use Cases

Model Explorer is optimized for structural analysis and hierarchical exploration of model graphs. Key use cases include:

*   **Model Architecture Exploration**: Visualizing nested layer structures with dynamic expand/collapse functionality
*   **Operation Relationship Analysis**: Understanding input/output connections between model components
*   **Development Phase Integration**: Supporting model design and initial implementation phases
*   **Interactive Debugging**: Using search, filtering, and metadata overlay for targeted analysis

**File Path**: [model-explorer/README.md](https://github.com/tenstorrent/tt-tutorial/blob/389c542c/model-explorer/README.md?plain=1)

### TT-NN Visualizer Use Cases

TT-NN Visualizer provides comprehensive execution analysis and performance monitoring capabilities:

*   **Execution Flow Analysis**: Tracking operation flow graphs and execution patterns
*   **Memory Profiling**: Analyzing buffer usage, tensor details, and memory allocation patterns
*   **Performance Optimization**: Identifying bottlenecks through execution analysis and performance metrics
*   **Remote Analysis**: Supporting SSH-based and file-based report loading for distributed environments
*   **Multi-instance Support**: Analyzing multiple execution instances for comparative analysis

**File Path**: [tt-nn visualizer/README.md](https://github.com/tenstorrent/tt-tutorial/blob/389c542c/tt-nn%20visualizer/README.md?plain=1)

## Getting Started Resources

### Model Explorer Quick Start

The Model Explorer provides Jupyter notebook tutorials for immediate hands-on experience:

*   **Quick Start Tutorial**: [model-explorer/example_colabs/quick_start.ipynb](https://github.com/tenstorrent/tt-tutorial/blob/389c542c/model-explorer/example_colabs/quick_start.ipynb)
*   **Custom Data Overlay Demo**: [model-explorer/example_colabs/custom_data_overlay_demo.ipynb](https://github.com/tenstorrent/tt-tutorial/blob/389c542c/model-explorer/example_colabs/custom_data_overlay_demo.ipynb)

### TT-NN Visualizer Documentation

Comprehensive guides for TT-NN Visualizer setup and advanced usage:

*   **Getting Started Guide**: Available in the ttnn-visualizer repository
*   **Remote Querying Tutorial**: Detailed instructions for SSH-based analysis workflows

Sources: [model-explorer/README.md 15-16](https://github.com/tenstorrent/tt-tutorial/blob/389c542c/model-explorer/README.md?plain=1#L15-L16)[tt-nn visualizer/README.md 15-16](https://github.com/tenstorrent/tt-tutorial/blob/389c542c/tt-nn%20visualizer/README.md?plain=1#L15-L16)

Dismiss
Refresh this wiki

Enter email to refresh
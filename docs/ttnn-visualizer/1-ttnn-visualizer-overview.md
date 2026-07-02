---
project: ttnn-visualizer
github: https://github.com/tenstorrent/ttnn-visualizer
deepwiki: https://deepwiki.com/tenstorrent/ttnn-visualizer/1-ttnn-visualizer-overview
---

# TTNN Visualizer Overview

Relevant source files
*   [README.md](https://github.com/tenstorrent/ttnn-visualizer/blob/b191aae4/README.md?plain=1)
*   [docs/getting-started.md](https://github.com/tenstorrent/ttnn-visualizer/blob/b191aae4/docs/getting-started.md?plain=1)

## Purpose and Scope

The TT-NN Visualizer is a specialized tool for analyzing and visualizing Tenstorrent Neural Network (TT-NN) models. This document provides an introduction to the visualizer, its key features, architecture, and main components. It serves as a starting point for understanding the system before diving into more specific aspects covered in other wiki pages.

For installation and setup instructions, see [Getting Started](https://deepwiki.com/tenstorrent/ttnn-visualizer/1.1-getting-started). For detailed architecture information, see [System Architecture](https://deepwiki.com/tenstorrent/ttnn-visualizer/2-system-architecture).

Sources: [README.md 1-115](https://github.com/tenstorrent/ttnn-visualizer/blob/b191aae4/README.md?plain=1#L1-L115)

## What is TT-NN Visualizer?

TT-NN Visualizer provides a comprehensive suite of visualization and analysis tools for TT-NN model execution data. It enables users to examine model operations, memory usage, tensor allocations, and performance metrics through an interactive web interface. The tool helps developers optimize their models by providing detailed insights into how models execute on Tenstorrent hardware.

Diagram Title: TT-NN Visualizer Functionality Overview

Sources: [README.md 12-15](https://github.com/tenstorrent/ttnn-visualizer/blob/b191aae4/README.md?plain=1#L12-L15)[README.md 40-54](https://github.com/tenstorrent/ttnn-visualizer/blob/b191aae4/README.md?plain=1#L40-L54)

## Key Features

TT-NN Visualizer offers a rich set of features for model analysis:

*   **Operation Analysis**

    *   Comprehensive list and graph visualization of all operations in the model
    *   Detailed view of operation inputs, outputs, and execution flow
    *   Operation flow graph for a holistic view of model execution

*   **Memory Analysis**

    *   Interactive L1, DRAM, and circular buffer memory plots
    *   Detailed insights into L1 peak memory consumption
    *   Interactive graph of allocation over time

*   **Tensor Analysis**

    *   Visualization of input and output tensors with core tiling and sharding details
    *   Core-specific tensor allocation views
    *   Filterable list of tensor details

*   **Performance Analysis**

    *   Performance reports with detailed metrics
    *   Interactive performance charts
    *   Execution time analysis

*   **Network Analysis**

    *   Network-on-chip performance estimator (NPE) for Tenstorrent Tensix-based devices
    *   Visualization of network traffic and bottlenecks

*   **Data Management**

    *   Load reports via local file system or SSH connection
    *   Support for multiple concurrent application instances

Sources: [README.md 40-54](https://github.com/tenstorrent/ttnn-visualizer/blob/b191aae4/README.md?plain=1#L40-L54)[docs/getting-started.md 92-112](https://github.com/tenstorrent/ttnn-visualizer/blob/b191aae4/docs/getting-started.md?plain=1#L92-L112)

## System Architecture

The TT-NN Visualizer follows a client-server architecture with a Flask backend and React frontend.

Diagram Title: TT-NN Visualizer System Architecture

Sources: [README.md 24-34](https://github.com/tenstorrent/ttnn-visualizer/blob/b191aae4/README.md?plain=1#L24-L34)[docs/getting-started.md 86-128](https://github.com/tenstorrent/ttnn-visualizer/blob/b191aae4/docs/getting-started.md?plain=1#L86-L128)

## Data Flow and Processing

The data flow in the TT-NN Visualizer starts with the data generated from TT-NN model execution and ends with interactive visualizations.

Diagram Title: TT-NN Visualizer Data Flow

Sources: [docs/getting-started.md 3-48](https://github.com/tenstorrent/ttnn-visualizer/blob/b191aae4/docs/getting-started.md?plain=1#L3-L48)[docs/getting-started.md 86-128](https://github.com/tenstorrent/ttnn-visualizer/blob/b191aae4/docs/getting-started.md?plain=1#L86-L128)

## Main Components

The TT-NN Visualizer consists of several key components, each focused on a specific aspect of model analysis:

### 1. Operation Analysis

The operation analysis components provide a comprehensive view of model operations, including:

*   Operation list with filtering and sorting
*   Operation details showing inputs, outputs, and execution flow
*   Graph visualization of operation dependencies

### 2. Memory Analysis

Memory analysis components visualize memory usage during model execution:

*   L1 and DRAM memory allocation plots
*   Peak memory consumption graphs
*   Tensor memory allocation details

### 3. Performance Analysis

Performance analysis components help identify performance bottlenecks:

*   Performance reports with execution time metrics
*   Performance charts visualizing operation performance
*   Comparative performance analysis

### 4. Network Analysis (NPE)

The Network-on-chip Performance Estimator (NPE) components visualize network traffic:

*   Network traffic heatmaps
*   Communication patterns visualization
*   Bottleneck identification

### 5. Data Source Management

Data source management components handle data loading from different sources:

*   Local file system access
*   Remote SSH connection and file synchronization
*   Session management for multiple concurrent instances

Sources: [README.md 40-54](https://github.com/tenstorrent/ttnn-visualizer/blob/b191aae4/README.md?plain=1#L40-L54)[docs/getting-started.md 92-112](https://github.com/tenstorrent/ttnn-visualizer/blob/b191aae4/docs/getting-started.md?plain=1#L92-L112)

## Data Requirements

TT-NN Visualizer requires specific data files generated during TT-NN model execution:

### Memory Reports

*   `config.json`: Configuration information
*   `db.sqlite`: Main database with operations and memory data

### Performance Reports

*   `profile_log_device.csv`: Device profiling data
*   Performance results CSV (e.g., `ops_perf_results_YYYY_MM_DD_HH_MM_SS.csv`)
*   `tracy_profile_log_host.tracy`: Tracy profiling data

### NPE Data

*   Network-on-chip performance estimator data files

These files must be organized in their own directories for the visualizer to load them correctly.

Sources: [docs/getting-started.md 3-48](https://github.com/tenstorrent/ttnn-visualizer/blob/b191aae4/docs/getting-started.md?plain=1#L3-L48)[docs/getting-started.md 92-112](https://github.com/tenstorrent/ttnn-visualizer/blob/b191aae4/docs/getting-started.md?plain=1#L92-L112)

## Getting Started

To start using TT-NN Visualizer:

1.   Install from PyPI:

```
pip install ttnn-visualizer
```
2.   Run the application:

```
ttnn-visualizer
```
3.   Load data via:

    *   Local folder selection
    *   Remote SSH connection
    *   Command line arguments: 
```
ttnn-visualizer --report-path PATH_TO_REPORT --performance-path PATH_TO_PERFORMANCE_DATA
```

4.   Navigate the interface to analyze operations, memory usage, performance, and network data.

For detailed installation and usage instructions, see [Getting Started](https://deepwiki.com/tenstorrent/ttnn-visualizer/1.1-getting-started).

Sources: [README.md 24-34](https://github.com/tenstorrent/ttnn-visualizer/blob/b191aae4/README.md?plain=1#L24-L34)[docs/getting-started.md 49-85](https://github.com/tenstorrent/ttnn-visualizer/blob/b191aae4/docs/getting-started.md?plain=1#L49-L85)

## Summary

TT-NN Visualizer is a powerful tool for analyzing and optimizing TT-NN models. It provides comprehensive visualization capabilities for operations, memory usage, performance, and network behavior. With its client-server architecture and flexible data loading options, it offers an efficient workflow for model developers working with Tenstorrent hardware.

Sources: [README.md 1-115](https://github.com/tenstorrent/ttnn-visualizer/blob/b191aae4/README.md?plain=1#L1-L115)[docs/getting-started.md 1-128](https://github.com/tenstorrent/ttnn-visualizer/blob/b191aae4/docs/getting-started.md?plain=1#L1-L128)

Dismiss
Refresh this wiki

Enter email to refresh
---
project: ttnn-visualizer
github: https://github.com/tenstorrent/ttnn-visualizer
deepwiki: https://deepwiki.com/tenstorrent/ttnn-visualizer/6-buffer-summary
---

# Buffer Summary

Relevant source files
*   [src/components/buffer-summary/BufferSummaryPlotRenderer.tsx](https://github.com/tenstorrent/ttnn-visualizer/blob/b191aae4/src/components/buffer-summary/BufferSummaryPlotRenderer.tsx)
*   [src/components/buffer-summary/BufferSummaryPlotRendererDRAM.tsx](https://github.com/tenstorrent/ttnn-visualizer/blob/b191aae4/src/components/buffer-summary/BufferSummaryPlotRendererDRAM.tsx)
*   [src/components/buffer-summary/BufferSummaryTab.tsx](https://github.com/tenstorrent/ttnn-visualizer/blob/b191aae4/src/components/buffer-summary/BufferSummaryTab.tsx)
*   [src/definitions/BufferSummary.ts](https://github.com/tenstorrent/ttnn-visualizer/blob/b191aae4/src/definitions/BufferSummary.ts)
*   [src/hooks/useAPI.tsx](https://github.com/tenstorrent/ttnn-visualizer/blob/b191aae4/src/hooks/useAPI.tsx)
*   [src/model/APIData.ts](https://github.com/tenstorrent/ttnn-visualizer/blob/b191aae4/src/model/APIData.ts)
*   [src/routes/BufferSummary.tsx](https://github.com/tenstorrent/ttnn-visualizer/blob/b191aae4/src/routes/BufferSummary.tsx)

The Buffer Summary view provides a comprehensive visualization of memory buffer allocations across operations in the TT-NN neural network models. This page enables engineers to analyze how memory is allocated and utilized throughout the model execution, both in L1 (on-chip) memory and DRAM.

For detailed information about individual operation memory usage, see [Operation Details](https://deepwiki.com/tenstorrent/ttnn-visualizer/4.2-operation-details) or for performance analysis, see [Performance Analysis](https://deepwiki.com/tenstorrent/ttnn-visualizer/5-performance-analysis).

## Overview

The Buffer Summary feature allows users to:

1.   Visualize buffer allocations across all operations in a model
2.   Compare memory usage patterns between different operations
3.   Identify potential memory bottlenecks or inefficiencies
4.   Analyze both L1 and DRAM memory allocations
5.   View data in both graphical plot format and tabular format

Sources: [src/routes/BufferSummary.tsx 1-192](https://github.com/tenstorrent/ttnn-visualizer/blob/b191aae4/src/routes/BufferSummary.tsx#L1-L192)[src/components/buffer-summary/BufferSummaryTab.tsx 1-83](https://github.com/tenstorrent/ttnn-visualizer/blob/b191aae4/src/components/buffer-summary/BufferSummaryTab.tsx#L1-L83)

## Memory Buffer Concepts

In the TT-NN architecture, memory buffers are allocated for various operations and tensors. The system primarily works with two types of memory:

1.   **L1 Memory** - On-chip memory that is fast but limited in size

    *   Regular L1 buffers
    *   L1 Small buffers (specialized allocations)
    *   Circular buffers (CB)

2.   **DRAM Memory** - Off-chip memory with larger capacity but slower access

Each buffer has properties such as:

*   Address (memory location)
*   Size (bytes)
*   Buffer type (L1, DRAM)
*   Device ID (which device the buffer is allocated on)
*   Association with tensors and operations

Sources: [src/model/APIData.ts 62-67](https://github.com/tenstorrent/ttnn-visualizer/blob/b191aae4/src/model/APIData.ts#L62-L67)[src/model/APIData.ts 53-60](https://github.com/tenstorrent/ttnn-visualizer/blob/b191aae4/src/model/APIData.ts#L53-L60)[src/hooks/useAPI.ts 152-156](https://github.com/tenstorrent/ttnn-visualizer/blob/b191aae4/src/hooks/useAPI.ts#L152-L156)

## User Interface

The Buffer Summary view consists of two main tabs (L1 and DRAM) and two view modes (Plot and Table) for each memory type.

### Navigation Controls

At the top of the page, users can toggle between:

*   **Plot view**: Graphical representation of buffer allocations
*   **Table view**: Detailed tabular data of buffers

Users can also switch between L1 and DRAM memory types using the tabs.

Sources: [src/routes/BufferSummary.tsx 63-141](https://github.com/tenstorrent/ttnn-visualizer/blob/b191aae4/src/routes/BufferSummary.tsx#L63-L141)[src/components/buffer-summary/BufferSummaryPlotRenderer.tsx 118-152](https://github.com/tenstorrent/ttnn-visualizer/blob/b191aae4/src/components/buffer-summary/BufferSummaryPlotRenderer.tsx#L118-L152)

### Plot View Features

The plot view visualizes buffer allocations across operations with the following features:

1.   **Memory Address Axis (X-axis)**: Shows the memory address space
2.   **Operations Axis (Y-axis)**: Lists operations by ID and name
3.   **Buffer Blocks**: Rectangular blocks representing buffer allocations
4.   **Memory Region Markers**: Visual indicators for different memory regions (L1 Small, L1 Start)

Control options include:

*   **Buffer zoom**: Zoom in on the active buffer addresses
*   **Hex axis labels**: Display memory addresses in hexadecimal format
*   **Tensor memory layout overlay**: Show tensor memory layout information
*   **Memory regions**: Show markers for memory region boundaries

Sources: [src/components/buffer-summary/BufferSummaryPlotRenderer.tsx 118-152](https://github.com/tenstorrent/ttnn-visualizer/blob/b191aae4/src/components/buffer-summary/BufferSummaryPlotRenderer.tsx#L118-L152)[src/components/buffer-summary/BufferSummaryPlotRendererDRAM.tsx 106-134](https://github.com/tenstorrent/ttnn-visualizer/blob/b191aae4/src/components/buffer-summary/BufferSummaryPlotRendererDRAM.tsx#L106-L134)

### Table View Features

The table view provides detailed information about buffer allocations in a tabular format, including:

*   Operation ID and name
*   Buffer address
*   Buffer size
*   Tensor information (shape, type)
*   Device ID

Sources: [src/components/buffer-summary/BufferSummaryTab.tsx 68-77](https://github.com/tenstorrent/ttnn-visualizer/blob/b191aae4/src/components/buffer-summary/BufferSummaryTab.tsx#L68-L77)

## Data Flow

The Buffer Summary component fetches and processes data through several steps:

Sources: [src/routes/BufferSummary.tsx 27-31](https://github.com/tenstorrent/ttnn-visualizer/blob/b191aae4/src/routes/BufferSummary.tsx#L27-L31)[src/hooks/useAPI.ts 196-205](https://github.com/tenstorrent/ttnn-visualizer/blob/b191aae4/src/hooks/useAPI.ts#L196-L205)[src/hooks/useAPI.ts 107-150](https://github.com/tenstorrent/ttnn-visualizer/blob/b191aae4/src/hooks/useAPI.ts#L107-L150)[src/components/buffer-summary/BufferSummaryTab.tsx 29-46](https://github.com/tenstorrent/ttnn-visualizer/blob/b191aae4/src/components/buffer-summary/BufferSummaryTab.tsx#L29-L46)

### Data Fetching

1.   The component uses several React Query hooks to fetch data:

    *   `useBuffers`: Retrieves buffer data for a specific buffer type (L1 or DRAM)
    *   `useOperationsList`: Gets the list of operations
    *   `useDevices`: Retrieves device information for memory size calculations

2.   Buffer data is fetched from the API endpoint `/api/operation-buffers` with optional buffer type filtering.

### Data Processing

1.   `createTensorsByOperationByIdList`: Associates tensors with buffer addresses for each operation
2.   `uniqueBuffersByOperationList`: Filters out duplicate buffers at the same address, keeping only the largest one

### Data Visualization

1.   Memory plot rendering: Uses `MemoryPlotRenderer` to create the visual buffer plots
2.   Virtualized rendering: Uses `useVirtualizer` from `@tanstack/react-virtual` for efficient rendering of large operation lists

## Memory Regions and Visualization

The Buffer Summary visualizes memory allocations with attention to different memory regions:

Sources: [src/components/buffer-summary/BufferSummaryPlotRenderer.tsx 111-116](https://github.com/tenstorrent/ttnn-visualizer/blob/b191aae4/src/components/buffer-summary/BufferSummaryPlotRenderer.tsx#L111-L116)[src/components/buffer-summary/BufferSummaryPlotRenderer.tsx 157-181](https://github.com/tenstorrent/ttnn-visualizer/blob/b191aae4/src/components/buffer-summary/BufferSummaryPlotRenderer.tsx#L157-L181)

### Memory Size Calculation

1.   For L1 memory: Uses device information to determine available memory size (typically from device configuration)
2.   For DRAM: Uses a constant value defined in `DRAM_MEMORY_SIZE`

### Buffer Zoom Feature

The buffer zoom feature focuses the visualization on the active memory range:

1.   Calculates the minimum and maximum addresses of all buffers
2.   Adds padding for better visualization
3.   Updates the plot range to focus on the active memory area

This is particularly useful for DRAM visualization where the address space can be very large and sparse.

Sources: [src/components/buffer-summary/BufferSummaryPlotRenderer.tsx 76-94](https://github.com/tenstorrent/ttnn-visualizer/blob/b191aae4/src/components/buffer-summary/BufferSummaryPlotRenderer.tsx#L76-L94)[src/components/buffer-summary/BufferSummaryPlotRendererDRAM.tsx 67-86](https://github.com/tenstorrent/ttnn-visualizer/blob/b191aae4/src/components/buffer-summary/BufferSummaryPlotRendererDRAM.tsx#L67-L86)

## State Management

The Buffer Summary component uses several state atoms from Jotai for global state management:

*   `selectedBufferSummaryTabAtom`: Tracks the active tab (L1 or DRAM)
*   `showBufferSummaryZoomedAtom`: Controls zoom state for buffer visualization
*   `showHexAtom`: Toggle for hexadecimal address display
*   `renderMemoryLayoutAtom`: Toggle for memory layout overlay
*   `showMemoryRegionsAtom`: Toggle for memory region markers
*   `selectedDeviceAtom`: Tracks the selected device (for multi-device systems)

Sources: [src/routes/BufferSummary.tsx 26](https://github.com/tenstorrent/ttnn-visualizer/blob/b191aae4/src/routes/BufferSummary.tsx#L26-L26)[src/components/buffer-summary/BufferSummaryPlotRenderer.tsx 51-60](https://github.com/tenstorrent/ttnn-visualizer/blob/b191aae4/src/components/buffer-summary/BufferSummaryPlotRenderer.tsx#L51-L60)

## Implementation Details

### Table-Plot Navigation

The Buffer Summary page provides navigation between Plot and Table views through anchor links:

1.   The page monitors scroll position to highlight the active section
2.   "Plot view" and "Table view" buttons in the sticky navigation allow switching between views
3.   Section IDs (`plot` and `table`) are used as anchors in the URL

Sources: [src/routes/BufferSummary.tsx 35-56](https://github.com/tenstorrent/ttnn-visualizer/blob/b191aae4/src/routes/BufferSummary.tsx#L35-L56)[src/routes/BufferSummary.tsx 69-86](https://github.com/tenstorrent/ttnn-visualizer/blob/b191aae4/src/routes/BufferSummary.tsx#L69-L86)

### Virtualized Rendering

For efficient rendering of potentially hundreds of operations, the component uses virtualized rendering:

1.   `useVirtualizer` hook from `@tanstack/react-virtual` calculates which operations to render
2.   Only visible operations are rendered in the DOM
3.   Scroll tracking adjusts which operations are displayed as the user scrolls

Sources: [src/components/buffer-summary/BufferSummaryPlotRenderer.tsx 96-102](https://github.com/tenstorrent/ttnn-visualizer/blob/b191aae4/src/components/buffer-summary/BufferSummaryPlotRenderer.tsx#L96-L102)[src/components/buffer-summary/BufferSummaryPlotRendererDRAM.tsx 91-97](https://github.com/tenstorrent/ttnn-visualizer/blob/b191aae4/src/components/buffer-summary/BufferSummaryPlotRendererDRAM.tsx#L91-L97)

### Memory Plot Rendering

Buffer visualization uses the `MemoryPlotRenderer` component which:

1.   Creates a Plotly-based visualization of memory address space
2.   Renders buffer blocks at appropriate addresses and sizes
3.   Adds memory region markers when enabled
4.   Supports zooming to focus on active memory regions

Sources: [src/components/buffer-summary/BufferSummaryPlotRenderer.tsx 157-181](https://github.com/tenstorrent/ttnn-visualizer/blob/b191aae4/src/components/buffer-summary/BufferSummaryPlotRenderer.tsx#L157-L181)[src/components/buffer-summary/BufferSummaryPlotRendererDRAM.tsx 139-153](https://github.com/tenstorrent/ttnn-visualizer/blob/b191aae4/src/components/buffer-summary/BufferSummaryPlotRendererDRAM.tsx#L139-L153)

## Use Cases

The Buffer Summary view addresses several use cases for TT-NN model developers:

1.   **Memory Usage Analysis**: Understand how memory is allocated across operations
2.   **Memory Optimization**: Identify operations with excessive memory usage
3.   **Buffer Reuse Patterns**: See how buffers are reused across operations
4.   **Memory Fragmentation**: Locate potential memory fragmentation issues
5.   **Compare L1 vs DRAM Usage**: Analyze the balance between L1 and DRAM memory allocations

By providing both visual and tabular representations of buffer allocations, the Buffer Summary offers comprehensive insight into a model's memory usage patterns, which is crucial for optimizing performance on Tenstorrent hardware.

Dismiss
Refresh this wiki

Enter email to refresh
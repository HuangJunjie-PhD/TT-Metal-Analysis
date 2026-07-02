---
project: ttnn-visualizer
github: https://github.com/tenstorrent/ttnn-visualizer
deepwiki: https://deepwiki.com/tenstorrent/ttnn-visualizer/5-performance-analysis
---

# Performance Analysis

Relevant source files
*   [src/components/performance/PerfCharts.tsx](https://github.com/tenstorrent/ttnn-visualizer/blob/b191aae4/src/components/performance/PerfCharts.tsx)
*   [src/components/performance/PerfReport.tsx](https://github.com/tenstorrent/ttnn-visualizer/blob/b191aae4/src/components/performance/PerfReport.tsx)
*   [src/components/report-selection/LocalFolderPicker.tsx](https://github.com/tenstorrent/ttnn-visualizer/blob/b191aae4/src/components/report-selection/LocalFolderPicker.tsx)
*   [src/definitions/PerfTable.ts](https://github.com/tenstorrent/ttnn-visualizer/blob/b191aae4/src/definitions/PerfTable.ts)
*   [src/functions/perfFunctions.tsx](https://github.com/tenstorrent/ttnn-visualizer/blob/b191aae4/src/functions/perfFunctions.tsx)
*   [src/routes/Performance.tsx](https://github.com/tenstorrent/ttnn-visualizer/blob/b191aae4/src/routes/Performance.tsx)
*   [src/scss/_common.scss](https://github.com/tenstorrent/ttnn-visualizer/blob/b191aae4/src/scss/_common.scss)
*   [src/scss/components/PerfCharts.scss](https://github.com/tenstorrent/ttnn-visualizer/blob/b191aae4/src/scss/components/PerfCharts.scss)
*   [src/scss/components/PerfReport.scss](https://github.com/tenstorrent/ttnn-visualizer/blob/b191aae4/src/scss/components/PerfReport.scss)

The Performance Analysis module in the TT-NN Visualizer provides comprehensive tooling for analyzing and understanding the performance characteristics of TT-NN models. This page covers the performance analysis capabilities, metrics, visualizations, and comparison features available in the system.

For operation-specific analysis and memory visualization, see [Operation Analysis](https://deepwiki.com/tenstorrent/ttnn-visualizer/4-operation-analysis).

## Overview

The Performance Analysis system enables users to:

1.   Analyze operation-level performance metrics
2.   Visualize performance characteristics through tables and charts
3.   Compare performance data between different runs
4.   Identify performance bottlenecks and optimization opportunities
5.   Filter and sort data to focus on specific operations

Sources: [src/routes/Performance.tsx 23-213](https://github.com/tenstorrent/ttnn-visualizer/blob/b191aae4/src/routes/Performance.tsx#L23-L213)[src/components/performance/PerfReport.tsx 83-363](https://github.com/tenstorrent/ttnn-visualizer/blob/b191aae4/src/components/performance/PerfReport.tsx#L83-L363)[src/components/performance/PerfCharts.tsx 19-39](https://github.com/tenstorrent/ttnn-visualizer/blob/b191aae4/src/components/performance/PerfCharts.tsx#L19-L39)

## Performance Metrics and Data Model

The Performance Analysis system tracks and displays numerous metrics for each operation in a model. These metrics help identify bottlenecks and optimization opportunities.

### Key Performance Metrics

| Metric | Description | Unit |
| --- | --- | --- |
| ID | Operation identifier | - |
| Total % | Percentage of total execution time | % |
| Bound | Performance bound type (DRAM, FLOP, SLOW) | - |
| Op Code | Operation type | - |
| Device Time | Execution time on device | μs |
| Op-to-Op Gap | Latency between operations | μs |
| Cores | Number of cores used | count |
| DRAM | DRAM bandwidth utilization | GB/s |
| DRAM % | Percentage of peak DRAM bandwidth | % |
| FLOPs | Floating-point operations performance | TFLOPs |
| FLOPs % | Percentage of peak FLOPs performance | % |
| Math Fidelity | Precision level used (HiFi4, HiFi2, LoFi) | - |

The performance data model is defined in the `PerfTableRow` interface, which contains these metrics along with additional data like input/output data types and memory usage.

Sources: [src/definitions/PerfTable.ts 1-114](https://github.com/tenstorrent/ttnn-visualizer/blob/b191aae4/src/definitions/PerfTable.ts#L1-L114)[src/components/performance/PerfReport.tsx 65-78](https://github.com/tenstorrent/ttnn-visualizer/blob/b191aae4/src/components/performance/PerfReport.tsx#L65-L78)

## Performance Report View

The Performance Report view displays a detailed tabular representation of operation performance data. This view allows for in-depth analysis of individual operations.

Sources: [src/components/performance/PerfReport.tsx 83-363](https://github.com/tenstorrent/ttnn-visualizer/blob/b191aae4/src/components/performance/PerfReport.tsx#L83-L363)[src/scss/components/PerfReport.scss 1-102](https://github.com/tenstorrent/ttnn-visualizer/blob/b191aae4/src/scss/components/PerfReport.scss#L1-L102)

### Features of the Performance Report

1.   **Sortable Columns**: Click on column headers to sort data
2.   **Filtering**: Filter operations by Op Code or Math Fidelity
3.   **Color Coding**: Visual indicators of performance characteristics 
    *   Green: Optimal performance
    *   Yellow: Potential bottleneck
    *   Red: Performance issue
    *   Gray: Very small operations (< 0.5% of total time)

4.   **High Dispatch Analysis**: Identifies operations with high dispatch latency (> 6μs)
5.   **Matmul Optimization Advice**: Specialized advice for optimizing matrix multiplication operations

### Color Coding Logic

The system uses color coding to highlight important performance characteristics:

*   **Core Utilization**: 
    *   Red: <10 cores (underutilization)
    *   Green: 64 cores (full utilization)

*   **Performance Bound**: 
    *   Green: DRAM or FLOP bound (expected)
    *   Yellow: SLOW (unexpected delay)

*   **Math Fidelity**: 
    *   Green: Sufficient precision
    *   Red: Excessive precision (performance opportunity)
    *   Cyan: Insufficient precision (accuracy concern)

*   **Op-to-Op Gap**: 
    *   Red: >6.5μs (high dispatch latency)

Sources: [src/functions/perfFunctions.tsx 125-235](https://github.com/tenstorrent/ttnn-visualizer/blob/b191aae4/src/functions/perfFunctions.tsx#L125-L235)

## Performance Charts

The Performance Charts tab provides visual representations of performance data to help identify patterns and bottlenecks.

Sources: [src/routes/Performance.tsx 146-204](https://github.com/tenstorrent/ttnn-visualizer/blob/b191aae4/src/routes/Performance.tsx#L146-L204)[src/components/performance/PerfCharts.tsx 19-39](https://github.com/tenstorrent/ttnn-visualizer/blob/b191aae4/src/components/performance/PerfCharts.tsx#L19-L39)[src/scss/components/PerfCharts.scss 1-25](https://github.com/tenstorrent/ttnn-visualizer/blob/b191aae4/src/scss/components/PerfCharts.scss#L1-L25)

### Available Charts

1.   **Op Count vs Runtime Chart**: Shows the relationship between operation count and execution time for different operation types
2.   **Device Kernel Runtime Chart**: Visualizes the runtime distribution across different kernels
3.   **Device Kernel Duration Chart**: Shows the duration of each kernel operation
4.   **Non-Filterable Charts**: Additional charts that show aggregated performance metrics that apply to the entire model

The charts provide different perspectives on performance data to help users understand where time is being spent in the model and identify potential optimizations.

## Performance Comparison

The Performance Analysis system includes a powerful comparison feature that allows users to compare performance between different runs of the same model or different models.

Sources: [src/routes/Performance.tsx 101-127](https://github.com/tenstorrent/ttnn-visualizer/blob/b191aae4/src/routes/Performance.tsx#L101-L127)[src/routes/Performance.tsx 169-203](https://github.com/tenstorrent/ttnn-visualizer/blob/b191aae4/src/routes/Performance.tsx#L169-L203)

### How to Use Comparison

1.   Select an active performance report
2.   Use the comparison picker to select a different report to compare against
3.   The system will display charts for both reports side by side
4.   Operation filters apply to both reports simultaneously
5.   The clear selection button removes the comparison report

This feature is particularly useful for:

*   Comparing performance before and after optimizations
*   Analyzing differences between hardware configurations
*   Understanding the impact of different model parameters on performance

## Advanced Analysis Features

The Performance Analysis system includes several advanced features to help identify specific performance issues.

### High Dispatch Operation Analysis

When enabled, the system identifies operations with high dispatch latency (>6.5μs), which can indicate scheduling or kernel launch overhead issues. The system:

1.   Highlights operations with high dispatch time
2.   Calculates the total overhead from high dispatch operations
3.   Provides an estimate of potential performance improvement by optimizing these operations
4.   Suggests optimization strategies (e.g., moving runtime args to compile-time args)

Sources: [src/components/performance/PerfReport.tsx 198-203](https://github.com/tenstorrent/ttnn-visualizer/blob/b191aae4/src/components/performance/PerfReport.tsx#L198-L203)[src/functions/perfFunctions.tsx 237-282](https://github.com/tenstorrent/ttnn-visualizer/blob/b191aae4/src/functions/perfFunctions.tsx#L237-L282)

### Math Fidelity Analysis

The system analyzes the precision settings (math fidelity) used for operations against the data types of inputs and outputs. It can identify:

1.   **Excessive Precision**: Using higher precision than needed (performance opportunity)
2.   **Insufficient Precision**: Using lower precision than recommended (accuracy concern)
3.   **Optimal Precision**: Using appropriate precision for the data types

This analysis helps users make informed decisions about precision vs. performance tradeoffs in their models.

Sources: [src/functions/perfFunctions.tsx 284-357](https://github.com/tenstorrent/ttnn-visualizer/blob/b191aae4/src/functions/perfFunctions.tsx#L284-L357)

## Implementation Details

The Performance Analysis system is implemented using several key components:

### Main Components

1.   **Performance Route**: Main container component that sets up the analysis environment

    *   Defined in `src/routes/Performance.tsx`
    *   Manages state and data fetching

2.   **Performance Report**: Tabular display of performance data

    *   Defined in `src/components/performance/PerfReport.tsx`
    *   Handles filtering, sorting, and rendering performance data

3.   **Performance Charts**: Visualization of performance metrics

    *   Defined in `src/components/performance/PerfCharts.tsx`
    *   Renders multiple chart types for different aspects of performance

### Key Technical Hooks

1.   **usePerformanceReport**: Fetches performance report data
2.   **usePerformanceComparisonReport**: Fetches comparison report data
3.   **useDeviceLog**: Fetches device metadata
4.   **useSortTable**: Manages sorting state and logic
5.   **useTableFilter**: Manages filtering state and logic

Sources: [src/routes/Performance.tsx 10-30](https://github.com/tenstorrent/ttnn-visualizer/blob/b191aae4/src/routes/Performance.tsx#L10-L30)[src/components/performance/PerfReport.tsx 84-89](https://github.com/tenstorrent/ttnn-visualizer/blob/b191aae4/src/components/performance/PerfReport.tsx#L84-L89)

## Usage Guidelines

To effectively use the Performance Analysis system:

1.   **Start with the table view** to get an overview of operation performance
2.   **Sort by "Total %"** to identify the most time-consuming operations
3.   **Look for color-coded indicators** of performance issues
4.   **Switch to the charts view** to visualize patterns and distribution
5.   **Use the comparison feature** to analyze the impact of optimizations
6.   **Enable high dispatch analysis** to identify scheduling overhead
7.   **Check math fidelity recommendations** for precision optimization opportunities

By following these guidelines, users can effectively identify and address performance bottlenecks in their TT-NN models.

Dismiss
Refresh this wiki

Enter email to refresh
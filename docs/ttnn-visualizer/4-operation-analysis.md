---
project: ttnn-visualizer
github: https://github.com/tenstorrent/ttnn-visualizer
deepwiki: https://deepwiki.com/tenstorrent/ttnn-visualizer/4-operation-analysis
---

# Operation Analysis

Relevant source files
*   [src/components/OperationArguments.tsx](https://github.com/tenstorrent/ttnn-visualizer/blob/b191aae4/src/components/OperationArguments.tsx)
*   [src/components/OperationList.tsx](https://github.com/tenstorrent/ttnn-visualizer/blob/b191aae4/src/components/OperationList.tsx)
*   [src/components/operation-details/OperationDetailsComponent.tsx](https://github.com/tenstorrent/ttnn-visualizer/blob/b191aae4/src/components/operation-details/OperationDetailsComponent.tsx)
*   [src/components/operation-details/TensorDetailsComponent.tsx](https://github.com/tenstorrent/ttnn-visualizer/blob/b191aae4/src/components/operation-details/TensorDetailsComponent.tsx)
*   [src/scss/components/OperationArguments.scss](https://github.com/tenstorrent/ttnn-visualizer/blob/b191aae4/src/scss/components/OperationArguments.scss)
*   [src/scss/components/OperationDetailsComponent.scss](https://github.com/tenstorrent/ttnn-visualizer/blob/b191aae4/src/scss/components/OperationDetailsComponent.scss)

The Operation Analysis section of the TT-NN Visualizer provides detailed insights into the operations performed during the execution of Tenstorrent Neural Network models. This system allows users to examine a comprehensive list of operations, analyze memory usage patterns, visualize tensor allocations, and inspect device-level operations.

For higher-level system architecture information, see [System Architecture](https://deepwiki.com/tenstorrent/ttnn-visualizer/2-system-architecture). For performance-specific analysis features, see [Performance Analysis](https://deepwiki.com/tenstorrent/ttnn-visualizer/5-performance-analysis).

## Operation Analysis Overview

The operation analysis system in the TT-NN Visualizer consists of two main components:

1.   **Operation List** - A comprehensive view of all operations in a model with filtering and sorting capabilities
2.   **Operation Details** - An in-depth analysis of a single operation, including memory usage, tensor details, and device operations

Sources: [src/components/OperationList.tsx](https://github.com/tenstorrent/ttnn-visualizer/blob/b191aae4/src/components/OperationList.tsx)[src/components/operation-details/OperationDetailsComponent.tsx](https://github.com/tenstorrent/ttnn-visualizer/blob/b191aae4/src/components/operation-details/OperationDetailsComponent.tsx)

## Operation List

The Operation List provides users with a comprehensive view of all operations in the model, allowing for filtering, sorting, and navigation.

### Key Features

*   **Filtering**: Filter operations by name or ID
*   **Sorting**: Sort operations by ID or duration
*   **Virtualized List**: Efficiently renders large lists of operations
*   **Expandable Views**: Expand operations to view arguments and details
*   **Navigation**: Quickly navigate to operation details

Sources: [src/components/OperationList.tsx 40-374](https://github.com/tenstorrent/ttnn-visualizer/blob/b191aae4/src/components/OperationList.tsx#L40-L374)

### Operation List Workflow

Sources: [src/components/OperationList.tsx 181-364](https://github.com/tenstorrent/ttnn-visualizer/blob/b191aae4/src/components/OperationList.tsx#L181-L364)

## Operation Details

The Operation Details component provides a comprehensive analysis of a single operation, including memory usage, tensor details, and device operations.

### Main Components

*   **Memory Plots**: Visualizations of L1 and DRAM memory usage
*   **Tensor Details**: Information about input, output, and intermediate tensors
*   **Device Operations**: Details of operations performed at the device level
*   **Stack Trace**: Python stack trace for debugging

Sources: [src/components/operation-details/OperationDetailsComponent.tsx 45-397](https://github.com/tenstorrent/ttnn-visualizer/blob/b191aae4/src/components/operation-details/OperationDetailsComponent.tsx#L45-L397)

### Memory Analysis

The Memory Analysis section displays memory allocation patterns in both L1 and DRAM memory spaces. Users can toggle between different views to analyze:

*   Current operation memory allocations
*   Delta from previous operation
*   Memory layout patterns
*   Circular buffer details

Sources: [src/components/operation-details/OperationDetailsComponent.tsx 264-346](https://github.com/tenstorrent/ttnn-visualizer/blob/b191aae4/src/components/operation-details/OperationDetailsComponent.tsx#L264-L346)

### Tensor Analysis

The Tensor Analysis section provides detailed information about tensors, including:

*   Shape, data type, and layout
*   Memory address and buffer type
*   Sharding specification
*   Visualization of tensor allocations across cores

Sources: [src/components/operation-details/TensorDetailsComponent.tsx 33-179](https://github.com/tenstorrent/ttnn-visualizer/blob/b191aae4/src/components/operation-details/TensorDetailsComponent.tsx#L33-L179)[src/components/operation-details/OperationDetailsComponent.tsx 351-356](https://github.com/tenstorrent/ttnn-visualizer/blob/b191aae4/src/components/operation-details/OperationDetailsComponent.tsx#L351-L356)

### Device Operations

The Device Operations section displays information about low-level operations performed on the device, including:

*   Operation names and types
*   Dependencies between operations
*   Graphical representation of operation flow

This view helps users understand the execution flow at the device level.

Sources: [src/components/operation-details/OperationDetailsComponent.tsx 358-391](https://github.com/tenstorrent/ttnn-visualizer/blob/b191aae4/src/components/operation-details/OperationDetailsComponent.tsx#L358-L391)

## Key User Interactions

Sources: [src/components/operation-details/OperationDetailsComponent.tsx 127-154](https://github.com/tenstorrent/ttnn-visualizer/blob/b191aae4/src/components/operation-details/OperationDetailsComponent.tsx#L127-L154)[src/components/operation-details/OperationDetailsComponent.tsx 174-247](https://github.com/tenstorrent/ttnn-visualizer/blob/b191aae4/src/components/operation-details/OperationDetailsComponent.tsx#L174-L247)

## Data Model

The Operation Details system relies on several key data models:

| Data Model | Purpose | Key Properties |
| --- | --- | --- |
| OperationDetails | Processes and organizes operation data | memoryData(), getTensorForAddress() |
| Tensor | Stores tensor information | id, address, shape, dtype, layout, buffer_type |
| BufferType | Enumerates memory buffer types | L1, DRAM, L1_SMALL |
| DeviceOperation | Describes device-level operations | id, name, type, dependencies |

Sources: [src/components/operation-details/OperationDetailsComponent.tsx 92-104](https://github.com/tenstorrent/ttnn-visualizer/blob/b191aae4/src/components/operation-details/OperationDetailsComponent.tsx#L92-L104)[src/components/operation-details/TensorDetailsComponent.tsx 25-32](https://github.com/tenstorrent/ttnn-visualizer/blob/b191aae4/src/components/operation-details/TensorDetailsComponent.tsx#L25-L32)

## Memory Visualization

The memory visualization system provides multiple views of memory usage:

1.   **Buffer view**: Shows allocations as horizontal bars with addresses
2.   **Delta view**: Shows changes in memory usage from the previous operation
3.   **Per-core view**: Shows how tensors are distributed across processor cores

### Key Controls for Memory Visualization

*   Buffer zoom: Focus on the relevant memory range
*   Memory layout overlay: Show tensor memory layout patterns
*   L1/DRAM toggle: Switch between memory types
*   Hex axis labels: Display addresses in hexadecimal format

Sources: [src/components/operation-details/OperationDetailsComponent.tsx 196-246](https://github.com/tenstorrent/ttnn-visualizer/blob/b191aae4/src/components/operation-details/OperationDetailsComponent.tsx#L196-L246)[src/scss/components/OperationDetailsComponent.scss 14-30](https://github.com/tenstorrent/ttnn-visualizer/blob/b191aae4/src/scss/components/OperationDetailsComponent.scss#L14-L30)

## Conclusion

The Operation Analysis system in the TT-NN Visualizer provides powerful tools for understanding and debugging neural network operations. By combining high-level operation listings with detailed memory, tensor, and device operation analysis, users can gain comprehensive insights into model execution behavior.

For more detailed information about specific aspects of operation analysis, see:

*   [Operation List](https://deepwiki.com/tenstorrent/ttnn-visualizer/4.1-operation-list) for details on the operation list view
*   [Operation Details](https://deepwiki.com/tenstorrent/ttnn-visualizer/4.2-operation-details) for in-depth explanation of the operation details view
*   [Memory Visualization](https://deepwiki.com/tenstorrent/ttnn-visualizer/4.3-memory-visualization) for more information about memory plotting features
*   [Tensor Analysis](https://deepwiki.com/tenstorrent/ttnn-visualizer/4.4-tensor-analysis) for tensor visualization and analysis capabilities

Dismiss
Refresh this wiki

Enter email to refresh
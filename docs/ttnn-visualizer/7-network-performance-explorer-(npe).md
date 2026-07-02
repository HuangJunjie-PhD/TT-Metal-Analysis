---
project: ttnn-visualizer
github: https://github.com/tenstorrent/ttnn-visualizer
deepwiki: https://deepwiki.com/tenstorrent/ttnn-visualizer/7-network-performance-explorer-(npe)
---

# Network Performance Explorer (NPE)

Relevant source files
*   [README.md](https://github.com/tenstorrent/ttnn-visualizer/blob/b191aae4/README.md?plain=1)
*   [docs/getting-started.md](https://github.com/tenstorrent/ttnn-visualizer/blob/b191aae4/docs/getting-started.md?plain=1)
*   [src/components/npe/ActiveTransferDetails.tsx](https://github.com/tenstorrent/ttnn-visualizer/blob/b191aae4/src/components/npe/ActiveTransferDetails.tsx)
*   [src/components/npe/NPECongestionHeatMap.tsx](https://github.com/tenstorrent/ttnn-visualizer/blob/b191aae4/src/components/npe/NPECongestionHeatMap.tsx)
*   [src/components/npe/NPEMetadata.tsx](https://github.com/tenstorrent/ttnn-visualizer/blob/b191aae4/src/components/npe/NPEMetadata.tsx)
*   [src/components/npe/NPEViewComponent.tsx](https://github.com/tenstorrent/ttnn-visualizer/blob/b191aae4/src/components/npe/NPEViewComponent.tsx)
*   [src/components/npe/TensixTransferRenderer.tsx](https://github.com/tenstorrent/ttnn-visualizer/blob/b191aae4/src/components/npe/TensixTransferRenderer.tsx)
*   [src/components/npe/drawingApi.ts](https://github.com/tenstorrent/ttnn-visualizer/blob/b191aae4/src/components/npe/drawingApi.ts)
*   [src/model/NPEModel.ts](https://github.com/tenstorrent/ttnn-visualizer/blob/b191aae4/src/model/NPEModel.ts)
*   [src/scss/components/NPEComponent.scss](https://github.com/tenstorrent/ttnn-visualizer/blob/b191aae4/src/scss/components/NPEComponent.scss)

The Network Performance Explorer (NPE) is a specialized visualization tool within the TT-NN Visualizer that enables users to analyze and optimize network-on-chip (NoC) performance for Tenstorrent Tensix-based devices. NPE provides detailed insights into data transfers, link congestion, and network utilization across the chip architecture.

For general performance analysis of operations and memory, see [Performance Analysis](https://deepwiki.com/tenstorrent/ttnn-visualizer/5-performance-analysis) or [Operation Analysis](https://deepwiki.com/tenstorrent/ttnn-visualizer/4-operation-analysis).

## Overview

NPE allows engineers to visualize and analyze how data moves between cores on Tenstorrent hardware. It renders a timestep-by-timestep view of data transfers across the mesh network, helping identify bottlenecks, congestion points, and inefficient data movement patterns.

Sources: [src/components/npe/NPEViewComponent.tsx](https://github.com/tenstorrent/ttnn-visualizer/blob/b191aae4/src/components/npe/NPEViewComponent.tsx)[src/model/NPEModel.ts](https://github.com/tenstorrent/ttnn-visualizer/blob/b191aae4/src/model/NPEModel.ts)[README.md](https://github.com/tenstorrent/ttnn-visualizer/blob/b191aae4/README.md?plain=1)

## Key Features

*   **Chip Visualization**: Visual representation of the Tensix core grid with indicators for different node types (Tensix cores, DRAM, Ethernet, PCIe)
*   **Data Transfer Animation**: Timestep-by-timestep playback of data transfers across the network
*   **Congestion Analysis**: Heat map visualization of link congestion and utilization
*   **Transfer Details**: Detailed information about individual transfers including source, destination, size, and type
*   **Interactive Selection**: Ability to select specific cores and view all transfers passing through them

## Data Model

The NPE visualization is built on a comprehensive data model that represents the network topology and data transfers:

Sources: [src/model/NPEModel.ts 1-127](https://github.com/tenstorrent/ttnn-visualizer/blob/b191aae4/src/model/NPEModel.ts#L1-L127)

### Key Data Structures

*   **CommonInfo**: Contains metadata about the device configuration and overall performance metrics
*   **NoCTransfer**: Represents a single data transfer, including its source, destination(s), and route through the network
*   **TimestepData**: Contains data for a specific simulation timestep, including active transfers and link utilization
*   **LinkUtilization**: Represents the utilization and demand of a specific network link at a given timestep

## User Interface Components

NPE utilizes several React components to create its interactive visualization:

Sources: [src/components/npe/NPEViewComponent.tsx 1-511](https://github.com/tenstorrent/ttnn-visualizer/blob/b191aae4/src/components/npe/NPEViewComponent.tsx#L1-L511)[src/components/npe/NPECongestionHeatMap.tsx 1-178](https://github.com/tenstorrent/ttnn-visualizer/blob/b191aae4/src/components/npe/NPECongestionHeatMap.tsx#L1-L178)[src/components/npe/ActiveTransferDetails.tsx 1-102](https://github.com/tenstorrent/ttnn-visualizer/blob/b191aae4/src/components/npe/ActiveTransferDetails.tsx#L1-L102)[src/components/npe/TensixTransferRenderer.tsx 1-99](https://github.com/tenstorrent/ttnn-visualizer/blob/b191aae4/src/components/npe/TensixTransferRenderer.tsx#L1-L99)

### Main Components

1.   **NPEViewComponent**: The primary React component that orchestrates the entire NPE visualization
2.   **NPECongestionHeatMap**: Renders a heat map showing congestion levels across all timesteps
3.   **ActiveTransferDetails**: Displays detailed information about selected transfers
4.   **TensixTransferRenderer**: Renders the visual representation of data transfers between cores
5.   **NPEMetadata**: Shows summary information about the device and overall metrics

## Visualization Details

### Tensix Grid Visualization

The main visualization in NPE is a grid representing the Tensix cores on the chip. Each cell in the grid represents a node (core, DRAM, Ethernet, or PCIe interface) and shows:

*   Node type indicators (T for Tensix core, d for DRAM, e for Ethernet, p for PCIe)
*   Arrows representing data transfers through NoC channels
*   Color coding to indicate congestion levels

Sources: [src/components/npe/NPEViewComponent.tsx 322-492](https://github.com/tenstorrent/ttnn-visualizer/blob/b191aae4/src/components/npe/NPEViewComponent.tsx#L322-L492)[src/scss/components/NPEComponent.scss 128-277](https://github.com/tenstorrent/ttnn-visualizer/blob/b191aae4/src/scss/components/NPEComponent.scss#L128-L277)

### Link Visualization

Links between nodes are visualized as arrows with different directions based on the NoC channel type:

| NoC Channel ID | Direction | Description |
| --- | --- | --- |
| NOC1_NORTH | North | Outgoing from NoC1 to north |
| NOC0_SOUTH | South | Outgoing from NoC0 to south |
| NOC0_EAST | East | Outgoing from NoC0 to east |
| NOC1_WEST | West | Outgoing from NoC1 to west |
| NOC0_IN | Core-in | From core to NoC0 |
| NOC0_OUT | Core-out | From NoC0 to core |
| NOC1_IN | Core-in | From core to NoC1 |
| NOC1_OUT | Core-out | From NoC1 to core |

Sources: [src/components/npe/drawingApi.ts 70-219](https://github.com/tenstorrent/ttnn-visualizer/blob/b191aae4/src/components/npe/drawingApi.ts#L70-L219)[src/model/NPEModel.ts 77-86](https://github.com/tenstorrent/ttnn-visualizer/blob/b191aae4/src/model/NPEModel.ts#L77-L86)

### Congestion Visualization

Link congestion is visualized using color intensity, calculated based on the link demand percentage:

*   Higher demand values result in more intense colors
*   The `calculateLinkCongestionColor` function maps demand values to RGB colors
*   A heatmap shows congestion levels across all timesteps

Sources: [src/components/npe/drawingApi.ts 220-233](https://github.com/tenstorrent/ttnn-visualizer/blob/b191aae4/src/components/npe/drawingApi.ts#L220-L233)[src/components/npe/NPECongestionHeatMap.tsx 11-176](https://github.com/tenstorrent/ttnn-visualizer/blob/b191aae4/src/components/npe/NPECongestionHeatMap.tsx#L11-L176)

## Interactive Features

### Playback Controls

NPE provides VCR-like controls for navigating through timesteps:

*   **Play**: Automatically advance through timesteps at normal speed
*   **Fast Forward**: Play at 2x speed
*   **Stop**: Pause the animation
*   **Step Forward/Backward**: Move one timestep forward or backward
*   **Timestep Slider**: Directly navigate to a specific timestep

Sources: [src/components/npe/NPEViewComponent.tsx 270-315](https://github.com/tenstorrent/ttnn-visualizer/blob/b191aae4/src/components/npe/NPEViewComponent.tsx#L270-L315)

### Selection and Highlighting

Users can interact with the visualization in several ways:

*   **Node Selection**: Click on a node to see transfers passing through it
*   **Transfer Selection**: Hover over a transfer in the details panel to highlight its route
*   **"Show all active transfers" Toggle**: Switch between showing only selected transfers or all active transfers

Sources: [src/components/npe/NPEViewComponent.tsx 189-238](https://github.com/tenstorrent/ttnn-visualizer/blob/b191aae4/src/components/npe/NPEViewComponent.tsx#L189-L238)[src/components/npe/ActiveTransferDetails.tsx 7-100](https://github.com/tenstorrent/ttnn-visualizer/blob/b191aae4/src/components/npe/ActiveTransferDetails.tsx#L7-L100)

## Data Flow

The following diagram illustrates the data flow within the NPE system:

Sources: [src/components/npe/NPEViewComponent.tsx 139-223](https://github.com/tenstorrent/ttnn-visualizer/blob/b191aae4/src/components/npe/NPEViewComponent.tsx#L139-L223)[src/components/npe/NPEViewComponent.tsx 398-504](https://github.com/tenstorrent/ttnn-visualizer/blob/b191aae4/src/components/npe/NPEViewComponent.tsx#L398-L504)

## Loading and Using NPE Data

### Sample Data

Sample NPE data is provided in the repository for testing:

```
Llama decode: llama-decode-3B.zip
```

Sources: [README.md 106-109](https://github.com/tenstorrent/ttnn-visualizer/blob/b191aae4/README.md?plain=1#L106-L109)

### Loading Data

NPE data can be loaded via the `/npe` route, separate from the main profiler and performance data:

1.   Navigate to the `/npe` route in the TT-NN Visualizer
2.   Upload or select the NPE data file
3.   The visualization will load and display the network data

Sources: [docs/getting-started.md 43-47](https://github.com/tenstorrent/ttnn-visualizer/blob/b191aae4/docs/getting-started.md?plain=1#L43-L47)[docs/getting-started.md 100-101](https://github.com/tenstorrent/ttnn-visualizer/blob/b191aae4/docs/getting-started.md?plain=1#L100-L101)

### Generating NPE Data

To generate NPE data for your own models, refer to the [tt-npe documentation](https://github.com/tenstorrent/ttnn-visualizer/blob/b191aae4/tt-npe%20documentation)

Sources: [docs/getting-started.md 47](https://github.com/tenstorrent/ttnn-visualizer/blob/b191aae4/docs/getting-started.md?plain=1#L47-L47)

## Summary Metrics

NPE provides several key performance indicators (KPIs) to help evaluate network performance:

| Metric | Description |
| --- | --- |
| Congestion Model | Model used in simulation to infer congestion |
| Cycles Per Timestep | How many cycles each simulation timestep spans |
| Device Name | The simulated device (e.g., wormhole_b0, blackhole) |
| DRAM Bandwidth Utilization | Percentage of DRAM RW bandwidth used over runtime |
| NoC Link Demand | Average demand for NoC links over runtime |
| NoC Link Utilization | Average utilization of NoC links over runtime |
| Max NoC Link Demand | Maximum observed link demand over all timesteps |

Sources: [src/model/NPEModel.ts 13-70](https://github.com/tenstorrent/ttnn-visualizer/blob/b191aae4/src/model/NPEModel.ts#L13-L70)[src/components/npe/NPEMetadata.tsx 1-43](https://github.com/tenstorrent/ttnn-visualizer/blob/b191aae4/src/components/npe/NPEMetadata.tsx#L1-L43)

## Integration with TT-NN Visualizer

NPE is integrated with the TT-NN Visualizer but operates somewhat independently:

1.   It has its own dedicated route (`/npe`)
2.   Data is loaded separately from the main profiler and performance data
3.   It shares the overall visual style and interaction patterns of the TT-NN Visualizer

When working with complete model analyses, users can use both the main TT-NN Visualizer features and the NPE feature to understand both computational and communication performance aspects of their models.

Sources: [docs/getting-started.md 43-47](https://github.com/tenstorrent/ttnn-visualizer/blob/b191aae4/docs/getting-started.md?plain=1#L43-L47)[README.md 53-54](https://github.com/tenstorrent/ttnn-visualizer/blob/b191aae4/README.md?plain=1#L53-L54)

Dismiss
Refresh this wiki

Enter email to refresh
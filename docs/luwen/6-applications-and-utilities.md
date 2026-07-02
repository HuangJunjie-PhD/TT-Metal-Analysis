---
project: luwen
github: https://github.com/tenstorrent/luwen
deepwiki: https://deepwiki.com/tenstorrent/luwen/6-applications-and-utilities
---

# Applications and Utilities

Relevant source files
*   [app/create-ethernet-map/src/lib.rs](https://github.com/tenstorrent/luwen/blob/55e35616/app/create-ethernet-map/src/lib.rs)
*   [crates/luwen-core/src/lib.rs](https://github.com/tenstorrent/luwen/blob/55e35616/crates/luwen-core/src/lib.rs)
*   [crates/luwen-if/src/chip/remote.rs](https://github.com/tenstorrent/luwen/blob/55e35616/crates/luwen-if/src/chip/remote.rs)
*   [crates/luwen-if/src/lib.rs](https://github.com/tenstorrent/luwen/blob/55e35616/crates/luwen-if/src/lib.rs)
*   [src/bin/luwen-demo.rs](https://github.com/tenstorrent/luwen/blob/55e35616/src/bin/luwen-demo.rs)

## Purpose and Scope

This document provides an overview of the applications and utilities built on top of the Luwen framework. These tools leverage the Luwen core functionality to deliver specific capabilities for working with Tenstorrent AI accelerator chips, including Ethernet mapping between chips, telemetry monitoring, and performance testing.

For information about the core architecture that these applications are built upon, see [Architecture](https://deepwiki.com/tenstorrent/luwen/2-architecture). For details about language bindings that can be used to build additional applications, see [Language Bindings](https://deepwiki.com/tenstorrent/luwen/5-language-bindings).

## Overview of Applications and Utilities

Luwen includes several applications and utilities that build upon its core functionality:

Sources: [app/create-ethernet-map/src/lib.rs 1-299](https://github.com/tenstorrent/luwen/blob/55e35616/app/create-ethernet-map/src/lib.rs#L1-L299)[src/bin/luwen-demo.rs 1-134](https://github.com/tenstorrent/luwen/blob/55e35616/src/bin/luwen-demo.rs#L1-L134)[crates/luwen-if/src/lib.rs 1-24](https://github.com/tenstorrent/luwen/blob/55e35616/crates/luwen-if/src/lib.rs#L1-L24)

## Create Ethernet Map

The Create Ethernet Map utility discovers and maps Ethernet connections between Tenstorrent AI accelerator chips. It generates a structured representation of the chip network topology, including architectures, board IDs, coordinates, and connections.

### Architecture and Components

Sources: [app/create-ethernet-map/src/lib.rs 13-26](https://github.com/tenstorrent/luwen/blob/55e35616/app/create-ethernet-map/src/lib.rs#L13-L26)[app/create-ethernet-map/src/lib.rs 28-276](https://github.com/tenstorrent/luwen/blob/55e35616/app/create-ethernet-map/src/lib.rs#L28-L276)

### Workflow

The Ethernet map generation follows this process:

Sources: [app/create-ethernet-map/src/lib.rs 28-276](https://github.com/tenstorrent/luwen/blob/55e35616/app/create-ethernet-map/src/lib.rs#L28-L276)

### Output Format

The utility generates a structured output file with these sections:

| Section | Description |
| --- | --- |
| `arch` | Maps chip IDs to architectures (Wormhole, Grayskull, Blackhole) |
| `chips` | Maps chip IDs to their coordinates in the network |
| `ethernet_connections` | Lists all connections between chips with routing information |
| `chips_with_mmio` | Lists chips with Memory-Mapped I/O access |
| `harvesting` | Contains chip harvesting information and NOC translation status |
| `boardtype` | Identifies the board type for each chip |

Example output:

```
arch: {
   0: Wormhole,
   1: Wormhole,
   2: Grayskull,
}

chips: {
   0: [0,0,0,0],
   1: [0,0,0,1],
}

ethernet_connections: [
   [{chip: 0, chan: 0}, {chip: 1, chan: 2}, {routing_enabled: true}],
]

chips_with_mmio: [
   0: 0,
   2: 1,
]

harvesting: {
   0: {noc_translation: true, harvest_mask: 0},
   1: {noc_translation: true, harvest_mask: 0},
   2: {noc_translation: false, harvest_mask: 3},
}

boardtype: {
   0: E300,
   1: E300,
   2: B100,
}
```

Sources: [app/create-ethernet-map/src/lib.rs 208-268](https://github.com/tenstorrent/luwen/blob/55e35616/app/create-ethernet-map/src/lib.rs#L208-L268)

### Usage

The utility provides both Rust and C-compatible interfaces:

Sources: [app/create-ethernet-map/src/lib.rs 28-276](https://github.com/tenstorrent/luwen/blob/55e35616/app/create-ethernet-map/src/lib.rs#L28-L276)[app/create-ethernet-map/src/lib.rs 278-298](https://github.com/tenstorrent/luwen/blob/55e35616/app/create-ethernet-map/src/lib.rs#L278-L298)

## Luwen Demo

The Luwen Demo application demonstrates how to use the Luwen framework to interact with Tenstorrent AI accelerator chips. It shows key capabilities including chip detection, DMA transfers, and ARC messaging.

The demo showcases:

1.   **Device Discovery**: Scanning for and detecting available PCI devices
2.   **Chip Initialization**: Opening and initializing detected chips
3.   **DMA Transfers**: Configuring and executing DMA transfers between host and device
4.   **Data Verification**: Ensuring data integrity during transfers
5.   **ARC Messaging**: Sending command messages to the ARC processor on the chip
6.   **Remote Chip Access**: Interacting with remote chips via Ethernet

Sources: [src/bin/luwen-demo.rs 13-134](https://github.com/tenstorrent/luwen/blob/55e35616/src/bin/luwen-demo.rs#L13-L134)

## Prometheus Exporter

The Prometheus Exporter collects telemetry data from Tenstorrent AI accelerator chips and exports it for monitoring with Prometheus. This enables real-time monitoring of chip status, temperature, power consumption, and other critical metrics.

### Exported Metrics

The exporter provides various metrics including:

*   Chip temperature readings
*   Power consumption metrics
*   Memory utilization
*   Ethernet link status
*   Error counts and status flags
*   Harvesting information
*   Board information

### Configuration

The Prometheus Exporter can be configured to:

*   Specify which chips to monitor
*   Set the polling interval for metric collection
*   Define the HTTP endpoint for Prometheus scraping
*   Filter which metrics to expose

## Performance Testing

The Performance Testing utilities provide tools for benchmarking and measuring the performance of Tenstorrent AI accelerator chips. These tools help users evaluate throughput, latency, and power efficiency across different workloads.

### Benchmark Capabilities

The performance testing framework provides:

1.   **DMA Transfer Benchmarks**: Measuring data transfer rates between host and device
2.   **NoC Performance**: Evaluating on-chip Network-on-Chip bandwidth
3.   **Memory Access Tests**: Measuring memory read/write performance
4.   **ARC Processing Tests**: Evaluating command processing efficiency
5.   **Ethernet Tests**: Measuring chip-to-chip communication performance
6.   **Power Efficiency**: Monitoring power consumption during different workloads

### Results Analysis

The framework includes tools for:

*   Collecting and aggregating benchmark results
*   Generating performance reports
*   Comparing results across different configurations
*   Visualizing performance metrics

## Summary

The applications and utilities in the Luwen project provide a comprehensive set of tools for working with Tenstorrent AI accelerator chips:

*   **Create Ethernet Map** provides network topology discovery and mapping
*   **Luwen Demo** demonstrates key functionality of the framework
*   **Prometheus Exporter** enables real-time monitoring and alerting
*   **Performance Testing** tools help evaluate and optimize system performance

These tools build upon the core Luwen framework to provide practical solutions for common tasks when working with Tenstorrent hardware.

Sources: [app/create-ethernet-map/src/lib.rs 1-299](https://github.com/tenstorrent/luwen/blob/55e35616/app/create-ethernet-map/src/lib.rs#L1-L299)[src/bin/luwen-demo.rs 1-134](https://github.com/tenstorrent/luwen/blob/55e35616/src/bin/luwen-demo.rs#L1-L134)[crates/luwen-if/src/lib.rs 1-24](https://github.com/tenstorrent/luwen/blob/55e35616/crates/luwen-if/src/lib.rs#L1-L24)[crates/luwen-if/src/chip/remote.rs 1-264](https://github.com/tenstorrent/luwen/blob/55e35616/crates/luwen-if/src/chip/remote.rs#L1-L264)

Dismiss
Refresh this wiki

Enter email to refresh
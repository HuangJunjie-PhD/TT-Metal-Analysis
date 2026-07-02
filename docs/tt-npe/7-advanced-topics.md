---
project: tt-npe
github: https://github.com/tenstorrent/tt-npe
deepwiki: https://deepwiki.com/tenstorrent/tt-npe/7-advanced-topics
---

# Advanced Topics

Relevant source files
*   [tt_npe/cpp/src/npeEngine.cpp](https://github.com/tenstorrent/tt-npe/blob/d1c8b91a/tt_npe/cpp/src/npeEngine.cpp)
*   [tt_npe/cpp/src/npeStats.cpp](https://github.com/tenstorrent/tt-npe/blob/d1c8b91a/tt_npe/cpp/src/npeStats.cpp)
*   [tt_npe/py/util/npe_analyze_noc_trace_dir.py](https://github.com/tenstorrent/tt-npe/blob/d1c8b91a/tt_npe/py/util/npe_analyze_noc_trace_dir.py)

This page covers advanced features and specialized use cases in tt-npe designed for power users and developers. These topics assume familiarity with basic tt-npe concepts covered in [TT-NPE Overview](https://deepwiki.com/tenstorrent/tt-npe/1-tt-npe-overview), [NoC Trace Analysis Pipeline](https://deepwiki.com/tenstorrent/tt-npe/2-noc-trace-analysis-pipeline), [Workload System](https://deepwiki.com/tenstorrent/tt-npe/3-workload-system), [Simulation Engine](https://deepwiki.com/tenstorrent/tt-npe/4-simulation-engine), and [Device Models](https://deepwiki.com/tenstorrent/tt-npe/5-device-models).

The advanced features documented here enable:

*   Large-scale batch analysis of workloads with custom grouping strategies
*   Visualization of complex multi-chip fabric transfers with timeline splitting
*   Automatic inference and manual override of hardware parameters
*   Schedule manipulation for what-if analysis

For basic CLI usage and Python API fundamentals, see [Python API and CLI](https://deepwiki.com/tenstorrent/tt-npe/6-python-api-and-cli). For contributing to tt-npe development, see [Development Guide](https://deepwiki.com/tenstorrent/tt-npe/8-development-guide).

## Overview of Advanced Features

The following diagram shows how advanced features integrate with the core tt-npe pipeline:

**Sources:**[tt_npe/cpp/src/npeEngine.cpp 67-170](https://github.com/tenstorrent/tt-npe/blob/d1c8b91a/tt_npe/cpp/src/npeEngine.cpp#L67-L170)[tt_npe/cpp/src/npeStats.cpp 203-876](https://github.com/tenstorrent/tt-npe/blob/d1c8b91a/tt_npe/cpp/src/npeStats.cpp#L203-L876)[tt_npe/py/util/npe_analyze_noc_trace_dir.py 364-448](https://github.com/tenstorrent/tt-npe/blob/d1c8b91a/tt_npe/py/util/npe_analyze_noc_trace_dir.py#L364-L448)

## Advanced Architecture Components

### Transfer Groups and Fabric Routing

Multi-hop fabric transfers are represented using transfer groups, where each hop across an ethernet link becomes a separate transfer with dependency relationships:

**Sources:**[tt_npe/cpp/src/npeEngine.cpp 112-153](https://github.com/tenstorrent/tt-npe/blob/d1c8b91a/tt_npe/cpp/src/npeEngine.cpp#L112-L153)

The transfer group mechanism works as follows:

*   Each transfer in a multi-chip route has the same `transfer_group_id`
*   The `transfer_group_index` orders transfers within the group (0, 1, 2, ...)
*   The `transfer_group_parent` field points to the previous hop's index
*   Checkpoints enforce serialization with ethernet hop delays (base delay + per-byte delay)

### Checkpoint-Based Dependency System

Dependencies between transfers use a checkpoint mechanism that supports both NIU serialization and transfer group ordering:

**Sources:**[tt_npe/cpp/src/npeEngine.cpp 67-170](https://github.com/tenstorrent/tt-npe/blob/d1c8b91a/tt_npe/cpp/src/npeEngine.cpp#L67-L170)

The dependency tracker maintains checkpoint state including:

*   Reference count (number of transfers that must complete)
*   Cycle delay (additional latency before dependent transfer can start)
*   Completion cycle (when all dependencies satisfied)

### Congestion Impact Estimation

The `estimate_cong_impact` configuration option runs two simulations to quantify congestion effects:

**Sources:**[tt_npe/cpp/src/npeEngine.cpp 172-200](https://github.com/tenstorrent/tt-npe/blob/d1c8b91a/tt_npe/cpp/src/npeEngine.cpp#L172-L200)

This feature is useful for:

*   Identifying bottlenecks caused by contention
*   Evaluating the effectiveness of routing strategies
*   Quantifying the performance gap between ideal and realistic scenarios

### Timeline Format Evolution

tt-npe supports two timeline formats for different use cases:

| Feature | v0 Format | v1 Format |
| --- | --- | --- |
| Schema version | Legacy | Current (recommended) |
| Multi-chip support | Limited | Full topology information |
| Transfer groups | Individual transfers | Logical grouped transfers |
| Zone tracking | Not supported | Nested zone hierarchy |
| Split files | Not supported | Automatic for large workloads |
| Compression | Optional | Optional (zstd) |
| Manifest generation | Not supported | Automatic with op mapping |

**Sources:**[tt_npe/cpp/src/npeStats.cpp 203-876](https://github.com/tenstorrent/tt-npe/blob/d1c8b91a/tt_npe/cpp/src/npeStats.cpp#L203-L876)

The v1 format is designed for scalability and includes:

*   Device coordinate mapping from `topology.json` for multi-chip visualization
*   Transfer group aggregation to reduce timeline file size
*   Hierarchical zone structure for temporal analysis
*   Automatic splitting when timestep count exceeds threshold

### Batch Analysis Architecture

The `npe_analyze_noc_trace_dir.py` tool provides parallel batch processing:

**Sources:**[tt_npe/py/util/npe_analyze_noc_trace_dir.py 1-456](https://github.com/tenstorrent/tt-npe/blob/d1c8b91a/tt_npe/py/util/npe_analyze_noc_trace_dir.py#L1-L456)

Key batch analysis features:

*   **Grouping strategies**: TTNN mode groups by operation ID, Metal mode groups by execution order
*   **Parallel execution**: Utilizes all CPU cores for simultaneous trace processing
*   **Aggregate metrics**: Cycle-weighted utilization statistics across all operations
*   **Manifest generation**: Maps operation IDs to timeline files for navigation in ttnn-visualizer

## Subsection Overview

The following subsections provide detailed documentation of specific advanced features:

*   **[Timeline Visualization Format](https://deepwiki.com/tenstorrent/tt-npe/7.1-timeline-visualization-format)**: Complete schema specification for v0 and v1 timeline JSON, split file format, compression, and metadata fields required by ttnn-visualizer

*   **[Multi-chip Simulation Details](https://deepwiki.com/tenstorrent/tt-npe/7.2-multi-chip-simulation-details)**: Deep dive into topology graphs, fabric path elaboration, cross-device routing algorithms, and ethernet hop modeling

*   **[Injection Rate Inference](https://deepwiki.com/tenstorrent/tt-npe/7.3-injection-rate-inference)**: Automatic inference from NoC traces, device model integration, when to use manual override, and accuracy considerations

*   **[Workload Schedule Scaling](https://deepwiki.com/tenstorrent/tt-npe/7.4-workload-schedule-scaling)**: Use cases for schedule manipulation, scale factor application, validation, and interaction with dependency tracking

## Configuration Options for Advanced Features

The following `npeConfig` fields control advanced features:

`// Timeline output configurationcfg.emit_timeline_file = true;                    // Enable timeline generationcfg.timeline_filepath = "output.npeviz";          // Override default pathcfg.use_legacy_timeline_format = false;           // Use v0 (true) or v1 (false)cfg.compress_timeline_output_file = true;         // Apply zstd compressioncfg.timeline_split_threshold_timesteps = 10000;   // Split threshold // Multi-chip configurationcfg.topology_json = "topology.json";              // Fabric topology file // Congestion analysiscfg.estimate_cong_impact = true;                  // Run dual simulation // Workload manipulationcfg.schedule_scale_factor = 1.5;                  // Scale phase timingscfg.infer_injection_rates = true;                 // Auto-infer from device`
**Sources:**[tt_npe/cpp/include/npeConfig.hpp](https://github.com/tenstorrent/tt-npe/blob/d1c8b91a/tt_npe/cpp/include/npeConfig.hpp)[tt_npe/py/util/npe_analyze_noc_trace_dir.py 205-220](https://github.com/tenstorrent/tt-npe/blob/d1c8b91a/tt_npe/py/util/npe_analyze_noc_trace_dir.py#L205-L220)

## Advanced Usage Patterns

### Pattern 1: Analyzing Large Multi-Chip Workloads

For workloads with thousands of timesteps across multiple chips:

1.   Enable timeline splitting: `cfg.timeline_split_threshold_timesteps = 5000`
2.   Enable compression: `cfg.compress_timeline_output_file = true`
3.   Use batch analysis: `npe_analyze_noc_trace_dir.py -e --compress_timeline_files`
4.   Result: Full timeline + split files for incremental loading in visualizer

**Sources:**[tt_npe/cpp/src/npeStats.cpp 827-876](https://github.com/tenstorrent/tt-npe/blob/d1c8b91a/tt_npe/cpp/src/npeStats.cpp#L827-L876)

### Pattern 2: Quantifying Congestion Impact

To understand how much performance is lost to contention:

1.   Set `cfg.estimate_cong_impact = true`
2.   Run simulation once
3.   Result: `npeStats` includes both `estimated_cycles` and `estimated_cong_free_cycles`
4.   Congestion impact percentage automatically calculated

**Sources:**[tt_npe/cpp/src/npeEngine.cpp 172-200](https://github.com/tenstorrent/tt-npe/blob/d1c8b91a/tt_npe/cpp/src/npeEngine.cpp#L172-L200)[tt_npe/cpp/src/npeStats.cpp 878-885](https://github.com/tenstorrent/tt-npe/blob/d1c8b91a/tt_npe/cpp/src/npeStats.cpp#L878-L885)

### Pattern 3: Custom Fabric Transfer Modeling

For synthetic workloads simulating multi-hop transfers:

1.   Assign consistent `transfer_group_id` to all hops
2.   Set `transfer_group_index` in execution order (0, 1, 2, ...)
3.   Set `transfer_group_parent` to previous hop's index
4.   Engine automatically creates checkpoint dependencies with ethernet delays

**Sources:**[tt_npe/cpp/src/npeEngine.cpp 112-153](https://github.com/tenstorrent/tt-npe/blob/d1c8b91a/tt_npe/cpp/src/npeEngine.cpp#L112-L153)

### Pattern 4: Batch Analysis with Custom Grouping

For Metal programs without TTNN operation IDs:

`npe_analyze_noc_trace_dir.py trace_dir/ \  --group_as_metal_traces \  -e \  --max_rows_in_summary_table 100`
This groups traces by aligned execution order across devices instead of TTNN operation IDs.

**Sources:**[tt_npe/py/util/npe_analyze_noc_trace_dir.py 276-319](https://github.com/tenstorrent/tt-npe/blob/d1c8b91a/tt_npe/py/util/npe_analyze_noc_trace_dir.py#L276-L319)

## Performance Considerations

### Timeline File Size Management

Timeline file sizes scale with workload complexity:

| Workload Characteristic | Impact on File Size | Mitigation Strategy |
| --- | --- | --- |
| Number of transfers | Linear | Use transfer groups to aggregate |
| Number of timesteps | Linear | Enable timeline splitting |
| Multi-chip topology | Moderate | Compression reduces overhead |
| Zone depth | Moderate | Zone filtering during serialization |

For workloads exceeding 10,000 timesteps, split files enable:

*   Incremental loading in visualizer (load only needed time ranges)
*   Reduced memory footprint during serialization
*   Faster initial visualization startup

**Sources:**[tt_npe/cpp/src/npeStats.cpp 827-876](https://github.com/tenstorrent/tt-npe/blob/d1c8b91a/tt_npe/cpp/src/npeStats.cpp#L827-L876)

### Batch Analysis Scalability

The `npe_analyze_noc_trace_dir.py` tool achieves near-linear speedup with CPU core count:

**Sources:**[tt_npe/py/util/npe_analyze_noc_trace_dir.py 419-435](https://github.com/tenstorrent/tt-npe/blob/d1c8b91a/tt_npe/py/util/npe_analyze_noc_trace_dir.py#L419-L435)

Bottlenecks:

*   **Fabric post-processing**: Sequential per operation (merges traces from all devices)
*   **Stats aggregation**: Sequential collection of results
*   **Timeline writing**: Sequential JSON serialization and compression

Optimizations:

*   Pre-merge traces before parallel NPE execution
*   Use `imap_unordered` for out-of-order completion
*   Skip timeline emission (`-e` flag) for faster analysis-only runs

## Debugging Advanced Features

### Transfer Group Consistency Checks

The v1 timeline serialization performs consistency checks:

`// Verify all transfers belong to exactly one groupfor (const auto& [group_id, transfers] : transfer_groups) {    for (const auto& transfer_id : transfers) {        TT_ASSERT(all_transfers.insert(transfer_id).second,                  "Transfer ID {} exists in multiple transfer groups!",                  transfer_id);    }}`
**Sources:**[tt_npe/cpp/src/npeStats.cpp 472-502](https://github.com/tenstorrent/tt-npe/blob/d1c8b91a/tt_npe/cpp/src/npeStats.cpp#L472-L502)

Errors indicate:

*   Duplicate `transfer_group_id` assignment
*   Missing parent transfer in group
*   Inconsistent group index ordering

### Dependency Sanity Checks

The dependency tracker validates completeness:

`if (!dep_tracker.sanityCheck() || !dep_tracker.allComplete()) {    log_error("Some dependencies not satisfied!");}`
**Sources:**[tt_npe/cpp/src/npeEngine.cpp 161-164](https://github.com/tenstorrent/tt-npe/blob/d1c8b91a/tt_npe/cpp/src/npeEngine.cpp#L161-L164)[tt_npe/cpp/src/npeEngine.cpp 334-336](https://github.com/tenstorrent/tt-npe/blob/d1c8b91a/tt_npe/cpp/src/npeEngine.cpp#L334-L336)

This catches:

*   Checkpoint reference count mismatches
*   Unreachable transfers due to circular dependencies
*   Missing dependency endpoints

### Timeline Consistency Validation

The v1 format validates transfer group references:

`// Check that all active transfer groups are definedfor (const auto& defined_group : defined_groups) {    if (active_groups.find(defined_group) == active_groups.end()) {        log_error("Transfer group ID {} is defined but never active", defined_group);    }}`
**Sources:**[tt_npe/cpp/src/npeStats.cpp 741-772](https://github.com/tenstorrent/tt-npe/blob/d1c8b91a/tt_npe/cpp/src/npeStats.cpp#L741-L772)

This ensures timeline integrity for visualization tools.

## Next Steps

For detailed documentation of each advanced feature:

*   Timeline format specification and schema: [Timeline Visualization Format](https://deepwiki.com/tenstorrent/tt-npe/7.1-timeline-visualization-format)
*   Multi-chip routing and topology: [Multi-chip Simulation Details](https://deepwiki.com/tenstorrent/tt-npe/7.2-multi-chip-simulation-details)
*   Injection rate configuration: [Injection Rate Inference](https://deepwiki.com/tenstorrent/tt-npe/7.3-injection-rate-inference)
*   Schedule manipulation: [Workload Schedule Scaling](https://deepwiki.com/tenstorrent/tt-npe/7.4-workload-schedule-scaling)

For development and extension of advanced features, see [Development Guide](https://deepwiki.com/tenstorrent/tt-npe/8-development-guide).

This wiki is featured in the [repository](https://github.com/tenstorrent/tt-npe/blob/main/README.md)

Dismiss
Refresh this wiki

Enter email to refresh
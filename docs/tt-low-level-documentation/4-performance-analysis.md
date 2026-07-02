---
project: tt-low-level-documentation
github: https://github.com/tenstorrent/tt-low-level-documentation
deepwiki: https://deepwiki.com/tenstorrent/tt-low-level-documentation/4-performance-analysis
---

# Performance Analysis

Relevant source files
*   [data_movement_doc/general/ideal_performance.md](https://github.com/tenstorrent/tt-low-level-documentation/blob/ae7661c8/data_movement_doc/general/ideal_performance.md?plain=1)
*   [data_movement_doc/multicast_schemes/Multicast Schemes.md](https://github.com/tenstorrent/tt-low-level-documentation/blob/ae7661c8/data_movement_doc/multicast_schemes/Multicast%20Schemes.md?plain=1)

## Purpose and Scope

This section documents empirical performance studies of data movement operations on Tenstorrent hardware platforms. It provides measured throughput and bandwidth characteristics for various data movement patterns, along with detailed analysis of configuration parameters that impact performance.

The performance analysis covers:

*   **Baseline performance metrics** for fundamental data movement primitives (DRAM read/write, core-to-core transfers, multicast operations)
*   **Multicast scheme performance** across different sender placements, NoC selections, and grid configurations
*   **Virtual channel behavior** under different traffic patterns and workload characteristics

For API usage guidance based on these performance findings, see [Best Practices](https://deepwiki.com/tenstorrent/tt-low-level-documentation/3-api-usage-guide). For platform-specific architectural details that explain performance characteristics, see [Hardware Platforms](https://deepwiki.com/tenstorrent/tt-low-level-documentation/2.3-hardware-platforms).

**Sources:**[data_movement_doc/multicast_schemes/Multicast Schemes.md 1-77](https://github.com/tenstorrent/tt-low-level-documentation/blob/ae7661c8/data_movement_doc/multicast_schemes/Multicast%20Schemes.md?plain=1#L1-L77)[data_movement_doc/general/ideal_performance.md 1-18](https://github.com/tenstorrent/tt-low-level-documentation/blob/ae7661c8/data_movement_doc/general/ideal_performance.md?plain=1#L1-L18)

* * *

## Performance Testing Framework

All performance data is collected using a comprehensive test suite that runs on both Wormhole B0 and Blackhole P100 platforms. The testing framework follows a systematic methodology:

The framework executes tests with varying parameters, collects raw cycle counts and byte transfer measurements, and processes them into throughput metrics expressed as **bytes per cycle**. The `test_data_movement.py` script automates data processing, visualization, and regression detection.

**Sources:**[data_movement_doc/multicast_schemes/Multicast Schemes.md 12-19](https://github.com/tenstorrent/tt-low-level-documentation/blob/ae7661c8/data_movement_doc/multicast_schemes/Multicast%20Schemes.md?plain=1#L12-L19)[data_movement_doc/general/ideal_performance.md 1-5](https://github.com/tenstorrent/tt-low-level-documentation/blob/ae7661c8/data_movement_doc/general/ideal_performance.md?plain=1#L1-L5)

* * *

## Test Coverage and Primitives

The performance analysis covers eight fundamental data movement primitives, each representing a distinct communication pattern on the Network-on-Chip:

| Primitive Category | Operation | Description |
| --- | --- | --- |
| **DRAM Operations** | DRAM Read | Tensix core reading from DRAM banks |
|  | DRAM Write | Tensix core writing to DRAM banks |
| **Point-to-Point** | One To One | Single source to single destination |
|  | One From One | Single destination reading from single source |
| **Broadcast/Collect** | One To All (Unicast) | Single source to multiple destinations via separate unicasts |
|  | One To All (Multicast) | Single source to rectangular grid via multicast write |
|  | One To All (Multicast + Linked) | Multicast with linked list packet mode |
|  | One From All | Multiple sources to single destination |

**Sources:**[data_movement_doc/general/ideal_performance.md 8-17](https://github.com/tenstorrent/tt-low-level-documentation/blob/ae7661c8/data_movement_doc/general/ideal_performance.md?plain=1#L8-L17)

* * *

## Performance Metrics and Units

All performance measurements use **bytes per cycle** as the primary throughput metric. This normalized metric enables direct comparison across:

*   Different hardware platforms (Wormhole B0 vs Blackhole P100)
*   Different NoC instances (NoC 0 vs NoC 1)
*   Different grid configurations (2×2 up to 9×9 on Blackhole)

For Wormhole B0, the theoretical maximum single-link bandwidth is 32 bytes/cycle (256 bits / 8 bits per byte). For Blackhole P100, it is 64 bytes/cycle (512 bits / 8 bits per byte). Measured throughput values are compared against these theoretical limits to assess efficiency.

**Sources:**[data_movement_doc/general/ideal_performance.md 4-5](https://github.com/tenstorrent/tt-low-level-documentation/blob/ae7661c8/data_movement_doc/general/ideal_performance.md?plain=1#L4-L5)[data_movement_doc/multicast_schemes/Multicast Schemes.md 14](https://github.com/tenstorrent/tt-low-level-documentation/blob/ae7661c8/data_movement_doc/multicast_schemes/Multicast%20Schemes.md?plain=1#L14-L14)

* * *

## Test Parameter Space

Performance studies systematically vary multiple configuration parameters to characterize their impact on throughput:

The multicast schemes study evaluates **10 distinct sender placements** relative to the destination grid:

**Schemes 1-4:** Sender positioned inside the grid (at corners) **Schemes 5-7:** Sender positioned outside grid, near bottom-left corner **Schemes 8-10:** Sender positioned outside grid, near top-right corner

For each scheme, tests vary NoC selection (0 or 1), loopback setting (enabled or disabled), and grid size (2×2 up to 7×7 on Wormhole, 2×2 up to 9×9 on Blackhole).

**Sources:**[data_movement_doc/multicast_schemes/Multicast Schemes.md 14-27](https://github.com/tenstorrent/tt-low-level-documentation/blob/ae7661c8/data_movement_doc/multicast_schemes/Multicast%20Schemes.md?plain=1#L14-L27)

* * *

## Performance Study Organization

The detailed performance analysis is organized into three sub-sections:

### 4.1 Baseline Performance Metrics

Documents ideal throughput characteristics for each primitive under optimal conditions. These baseline measurements establish performance targets and identify theoretical limits. See [Baseline Performance Metrics](https://deepwiki.com/tenstorrent/tt-low-level-documentation/4.1-baseline-performance-metrics).

Key findings:

*   DRAM operations achieve 21-22 bytes/cycle on Wormhole, 33-34 bytes/cycle on Blackhole
*   Point-to-point transfers (One To One) achieve 28-29 bytes/cycle on Wormhole, 60 bytes/cycle on Blackhole
*   Basic multicast achieves 15 bytes/cycle on Wormhole, 24 bytes/cycle on Blackhole
*   Multicast with linked mode improves to 22 bytes/cycle on Wormhole, 41 bytes/cycle on Blackhole

**Sources:**[data_movement_doc/general/ideal_performance.md 8-17](https://github.com/tenstorrent/tt-low-level-documentation/blob/ae7661c8/data_movement_doc/general/ideal_performance.md?plain=1#L8-L17)

### 4.2 Multicast Schemes Performance Study

**Critical analysis** of how multicast write parameters impact throughput. This is the highest-importance performance study (importance score 7.13), revealing fundamental architectural behaviors including:

*   **Self-interference mechanism:** When write traffic and acknowledge traffic share the same congested physical links, throughput degrades sharply with increasing grid size
*   **NoC-dependent routing:** NoC 0 emphasizes column sharing; NoC 1 emphasizes row sharing, creating opposite performance characteristics for different sender placements
*   **Loopback impact:** Disabling loopback slightly improves performance for inside-grid sender placements

The study identifies specific scheme/NoC combinations to avoid and provides actionable recommendations. See [Multicast Schemes Performance Study](https://deepwiki.com/tenstorrent/tt-low-level-documentation/4.2-multicast-schemes-performance-study).

**Sources:**[data_movement_doc/multicast_schemes/Multicast Schemes.md 1-77](https://github.com/tenstorrent/tt-low-level-documentation/blob/ae7661c8/data_movement_doc/multicast_schemes/Multicast%20Schemes.md?plain=1#L1-L77)

### 4.3 Virtual Channels Performance Study

Empirical evaluation demonstrating that multiple virtual channels (VCs) do **not** improve throughput for same-class traffic. Key findings:

*   **Router arbitration behavior:** VC selection occurs before port arbitration, causing idle cycles when blocked VCs are selected
*   **Shared input buffers:** Only 3 VCs can achieve full bandwidth simultaneously due to buffer sharing
*   **Correct usage pattern:** VCs should separate traffic classes with different characteristics (DRAM, L1, Ethernet, credits), not parallelize identical traffic

See [Virtual Channels Performance Study](https://deepwiki.com/tenstorrent/tt-low-level-documentation/4.3-virtual-channels-performance-study).

**Sources:** High-level system diagrams (Diagram 5: Virtual Channel System and Trade-offs)

* * *

## Platform Comparison Summary

Performance characteristics differ significantly between platforms due to architectural improvements:

| Metric | Wormhole B0 | Blackhole P100 | Improvement |
| --- | --- | --- | --- |
| Interface width | 256 bits/cycle | 512 bits/cycle | 2× |
| DRAM read throughput | 22 bytes/cycle | 33 bytes/cycle | 1.5× |
| DRAM write throughput | 21 bytes/cycle | 34 bytes/cycle | 1.6× |
| Core-to-core (One To One) | 29 bytes/cycle | 60 bytes/cycle | 2.1× |
| Multicast throughput | 15 bytes/cycle | 24 bytes/cycle | 1.6× |
| Multicast + Linked | 22 bytes/cycle | 41 bytes/cycle | 1.9× |
| Tensix core count | 64 cores | 110 cores | 1.7× |

Blackhole's doubled interface width (512 vs 256 bits/cycle) translates to approximately 2× improvement in core-to-core transfers and 1.5-2× improvement in DRAM operations. The larger core count (110 vs 64) enables testing of larger grid configurations (up to 9×9 vs 7×7).

**Sources:**[data_movement_doc/general/ideal_performance.md 6-17](https://github.com/tenstorrent/tt-low-level-documentation/blob/ae7661c8/data_movement_doc/general/ideal_performance.md?plain=1#L6-L17)

* * *

## Testing Infrastructure Components

The performance testing system comprises several key components that work together to collect, process, and visualize data:

The test harness invokes NOC APIs with specific configurations, hardware devices execute and measure cycles, the Python script processes raw data into throughput metrics, and visualization/documentation artifacts are generated automatically. This automated pipeline enables continuous performance monitoring and regression detection as the codebase evolves.

**Sources:**[data_movement_doc/multicast_schemes/Multicast Schemes.md 12-19](https://github.com/tenstorrent/tt-low-level-documentation/blob/ae7661c8/data_movement_doc/multicast_schemes/Multicast%20Schemes.md?plain=1#L12-L19) High-level system diagrams (Diagram 7: Platform Evolution and Testing Framework)

* * *

## Key Performance Insights

Across all performance studies, several critical insights emerge:

1.   **Physical link contention dominates performance:** Virtual channels provide logical separation but do not add physical bandwidth. When write traffic and acknowledge traffic converge on the same congested links, throughput degrades regardless of VC assignment.

2.   **NoC routing asymmetry creates complementary behaviors:** NoC 0's column-emphasis and NoC 1's row-emphasis mean that optimal sender placement depends on NoC selection. A placement that performs well on NoC 0 may perform poorly on NoC 1, and vice versa.

3.   **Grid size amplifies congestion effects:** As multicast grid dimensions increase, more traffic converges on shared links. Configurations that exhibit self-interference show sharp, non-linear degradation with grid size.

4.   **Loopback adds minimal overhead:** Disabling loopback for inside-grid senders provides slight improvements (5-10%) but does not fundamentally change performance characteristics.

5.   **Linked multicast mode improves throughput:** The multicast + linked primitive achieves 47-71% higher throughput than basic multicast by reducing packet overhead.

These insights inform the best practices and usage patterns documented in [API Usage Guide](https://deepwiki.com/tenstorrent/tt-low-level-documentation/3-api-usage-guide), particularly in [Asynchronous Operations and Batching](https://deepwiki.com/tenstorrent/tt-low-level-documentation/3.2-asynchronous-operations-and-batching) and [Posted Writes Optimization](https://deepwiki.com/tenstorrent/tt-low-level-documentation/3.6-posted-writes-optimization).

**Sources:**[data_movement_doc/multicast_schemes/Multicast Schemes.md 48-77](https://github.com/tenstorrent/tt-low-level-documentation/blob/ae7661c8/data_movement_doc/multicast_schemes/Multicast%20Schemes.md?plain=1#L48-L77)[data_movement_doc/general/ideal_performance.md 8-17](https://github.com/tenstorrent/tt-low-level-documentation/blob/ae7661c8/data_movement_doc/general/ideal_performance.md?plain=1#L8-L17)

Dismiss
Refresh this wiki

Enter email to refresh
---
project: tt-perf-report
github: https://github.com/tenstorrent/tt-perf-report
deepwiki: https://deepwiki.com/tenstorrent/tt-perf-report/5-performance-classification-system
---

# Performance Classification System

Relevant source files
*   [README.md](https://github.com/tenstorrent/tt-perf-report/blob/076a1b39/README.md?plain=1)
*   [src/tt_perf_report/perf_report.py](https://github.com/tenstorrent/tt-perf-report/blob/076a1b39/src/tt_perf_report/perf_report.py)

## Purpose and Scope

This document explains the performance classification system used in the tt-perf-report tool to categorize operations based on their performance characteristics. It covers how operations are classified, what each classification means, and how this information can be used to guide optimization efforts. For information about implementing the suggested optimizations, see [Optimization Advice System](https://deepwiki.com/tenstorrent/tt-perf-report/6-optimization-advice-system).

Sources: [src/tt_perf_report/perf_report.py 515-540](https://github.com/tenstorrent/tt-perf-report/blob/076a1b39/src/tt_perf_report/perf_report.py#L515-L540)[README.md 97-102](https://github.com/tenstorrent/tt-perf-report/blob/076a1b39/README.md?plain=1#L97-L102)

## Classification Categories Overview

The tt-perf-report tool classifies each operation into one of the following categories:

| Classification | Description | Criteria | Visual Indicator |
| --- | --- | --- | --- |
| **DRAM** | Memory bandwidth bound | ≥65% of peak DRAM bandwidth | Green DRAM metrics |
| **FLOP** | Compute bound | ≥65% of peak FLOPs for the given math fidelity | Green FLOPs metrics |
| **BOTH** | Both memory and compute bound | ≥65% of peak DRAM bandwidth AND ≥65% of peak FLOPs | Green for both metrics |
| **SLOW** | Neither memory nor compute bound | <65% of both peak DRAM and FLOPs | Yellow metrics |
| **HOST** | Operation running on host CPU | Contains "(torch)" in op code | Red bound indicator |

Sources: [src/tt_perf_report/perf_report.py 529-539](https://github.com/tenstorrent/tt-perf-report/blob/076a1b39/src/tt_perf_report/perf_report.py#L529-L539)[README.md 97-102](https://github.com/tenstorrent/tt-perf-report/blob/076a1b39/README.md?plain=1#L97-L102)

## Classification Algorithm

The performance classification system works by calculating resource utilization percentages and applying threshold-based rules to determine bottlenecks.

### Classification Flow Diagram

Sources: [src/tt_perf_report/perf_report.py 515-540](https://github.com/tenstorrent/tt-perf-report/blob/076a1b39/src/tt_perf_report/perf_report.py#L515-L540)

### Resource Utilization Calculation

#### DRAM Utilization

For Matmul operations, DRAM bandwidth utilization is calculated as:

1.   Compute total data size from inputs and outputs that use DRAM memory
2.   Calculate achieved bandwidth: `dram_speed_gb_s = (total_data_size_bytes / duration_s) / 1e9`
3.   Calculate percentage: `dram_percentage = (dram_speed_gb_s / 288) * 100`
    *   288 GB/s is the theoretical peak DRAM bandwidth on Wormhole devices

#### FLOPs Utilization

For compute-intensive operations (Matmul, Conv), FLOPs utilization is calculated as:

1.   Determine peak FLOPs based on math fidelity and core count 
    *   HiFi4: 74 TFLOPs/core for high precision
    *   HiFi2: 148 TFLOPs/core for medium precision
    *   LoFi: 262 TFLOPs/core for low precision

2.   Calculate achieved FLOPs from operation dimensions and duration
3.   Calculate percentage: `flops_percentage = (flops / peak_flops_value) * 100`

Sources: [src/tt_perf_report/perf_report.py 52-60](https://github.com/tenstorrent/tt-perf-report/blob/076a1b39/src/tt_perf_report/perf_report.py#L52-L60)[src/tt_perf_report/perf_report.py 207-277](https://github.com/tenstorrent/tt-perf-report/blob/076a1b39/src/tt_perf_report/perf_report.py#L207-L277)[src/tt_perf_report/perf_report.py 315-373](https://github.com/tenstorrent/tt-perf-report/blob/076a1b39/src/tt_perf_report/perf_report.py#L315-L373)

## Classification Implementation

The classification is implemented in the `add_derived_columns` function of the performance report tool. This function takes a list of operation data rows and adds the classification as a new "Bound" column:

Sources: [src/tt_perf_report/perf_report.py 515-540](https://github.com/tenstorrent/tt-perf-report/blob/076a1b39/src/tt_perf_report/perf_report.py#L515-L540)

## Visual Representation in the Performance Report

The classification is visually represented in the performance report through color coding to help quickly identify bottlenecks:

Sources: [src/tt_perf_report/perf_report.py 551-630](https://github.com/tenstorrent/tt-perf-report/blob/076a1b39/src/tt_perf_report/perf_report.py#L551-L630)

## Interpretation and Optimization Recommendations

Each classification provides insights into the operation's performance characteristics and guides optimization strategies:

### DRAM-bound Operations

Operations classified as DRAM-bound are limited by memory bandwidth.

**Indicators:**

*   High DRAM bandwidth utilization (≥65% of peak)
*   DRAM metrics highlighted in green

**Optimization Strategies:**

*   Use DRAM-sharded program configuration (for Matmul operations)
*   Reduce memory transfers
*   Optimize memory access patterns
*   Consider lower precision datatypes if accuracy allows

Sources: [src/tt_perf_report/perf_report.py 585-588](https://github.com/tenstorrent/tt-perf-report/blob/076a1b39/src/tt_perf_report/perf_report.py#L585-L588)[src/tt_perf_report/perf_report.py 745-749](https://github.com/tenstorrent/tt-perf-report/blob/076a1b39/src/tt_perf_report/perf_report.py#L745-L749)

### FLOP-bound Operations

Operations classified as FLOP-bound are limited by compute capabilities.

**Indicators:**

*   High FLOPs utilization (≥65% of peak)
*   FLOPs metrics highlighted in green

**Optimization Strategies:**

*   Increase grid size/core count if not using all 64 cores
*   Consider using lower precision math fidelity if accuracy allows
*   Optimize compute algorithm

Sources: [src/tt_perf_report/perf_report.py 589-592](https://github.com/tenstorrent/tt-perf-report/blob/076a1b39/src/tt_perf_report/perf_report.py#L589-L592)[src/tt_perf_report/perf_report.py 755-758](https://github.com/tenstorrent/tt-perf-report/blob/076a1b39/src/tt_perf_report/perf_report.py#L755-L758)

### BOTH-bound Operations

Operations classified as BOTH are well-optimized and balanced.

**Indicators:**

*   High DRAM bandwidth utilization (≥65% of peak)
*   High FLOPs utilization (≥65% of peak)
*   Both metrics highlighted in green

**Interpretation:**

*   These operations are well-balanced and efficiently using resources
*   Further optimization may require architectural changes or algorithm redesign

Sources: [src/tt_perf_report/perf_report.py 585-592](https://github.com/tenstorrent/tt-perf-report/blob/076a1b39/src/tt_perf_report/perf_report.py#L585-L592)

### SLOW Operations

Operations classified as SLOW are inefficiently using resources.

**Indicators:**

*   Low resource utilization (<65% of both DRAM and FLOPs)
*   Metrics highlighted in yellow

**Optimization Strategies:**

*   Check memory layout (prefer L1 memory if possible)
*   Optimize block sizes: 
    *   Increase inner dimension block size (in0_block_w ≥ 2)
    *   Increase output subblock dimensions (out_subblock_h * out_subblock_w ≥ 2)

*   Consider adjusting program configuration

Sources: [src/tt_perf_report/perf_report.py 593-603](https://github.com/tenstorrent/tt-perf-report/blob/076a1b39/src/tt_perf_report/perf_report.py#L593-L603)[src/tt_perf_report/perf_report.py 759-796](https://github.com/tenstorrent/tt-perf-report/blob/076a1b39/src/tt_perf_report/perf_report.py#L759-L796)

### HOST Operations

Operations classified as HOST run on the CPU rather than the device.

**Indicators:**

*   Contains "(torch)" in the operation code
*   Bound indicator highlighted in red

**Optimization Strategies:**

*   Move operations to run on the device when possible
*   Investigate why operations are falling back to host execution

Sources: [src/tt_perf_report/perf_report.py 604-605](https://github.com/tenstorrent/tt-perf-report/blob/076a1b39/src/tt_perf_report/perf_report.py#L604-L605)[src/tt_perf_report/perf_report.py 674-681](https://github.com/tenstorrent/tt-perf-report/blob/076a1b39/src/tt_perf_report/perf_report.py#L674-L681)

## Classification and Decision Making Process

The following diagram illustrates how the classification system informs the optimization advice generation:

Sources: [src/tt_perf_report/perf_report.py 731-799](https://github.com/tenstorrent/tt-perf-report/blob/076a1b39/src/tt_perf_report/perf_report.py#L731-L799)

## Performance Classification in Action

When the performance report runs, it follows this process to classify each operation:

1.   Parse the CSV trace file
2.   Analyze each operation to calculate resource utilization
3.   Apply classification rules based on utilization thresholds
4.   Apply color coding based on classification
5.   Generate optimization advice based on classification

This classification system is the core of the performance analysis capability in tt-perf-report and forms the basis for all optimization recommendations provided by the tool.

Sources: [src/tt_perf_report/perf_report.py 900-906](https://github.com/tenstorrent/tt-perf-report/blob/076a1b39/src/tt_perf_report/perf_report.py#L900-L906)[src/tt_perf_report/perf_report.py 943-1045](https://github.com/tenstorrent/tt-perf-report/blob/076a1b39/src/tt_perf_report/perf_report.py#L943-L1045)

## Conclusion

The Performance Classification System is a critical component of the tt-perf-report tool that enables developers to quickly identify bottlenecks and optimization opportunities in their Tenstorrent workloads. By categorizing operations as DRAM-bound, FLOP-bound, BOTH, SLOW, or HOST, the system provides clear indications of which resources are constraining performance and suggests targeted optimization strategies.

For detailed information about implementing the optimization strategies suggested by these classifications, refer to the [Optimization Advice System](https://deepwiki.com/tenstorrent/tt-perf-report/6-optimization-advice-system) page.

Dismiss
Refresh this wiki

Enter email to refresh
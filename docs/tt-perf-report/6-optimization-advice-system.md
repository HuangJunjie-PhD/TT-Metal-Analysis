---
project: tt-perf-report
github: https://github.com/tenstorrent/tt-perf-report
deepwiki: https://deepwiki.com/tenstorrent/tt-perf-report/6-optimization-advice-system
---

# Optimization Advice System

Relevant source files
*   [src/tt_perf_report/perf_report.py](https://github.com/tenstorrent/tt-perf-report/blob/076a1b39/src/tt_perf_report/perf_report.py)

## Purpose and Scope

The Optimization Advice System in tt-perf-report is responsible for analyzing performance data and generating specific optimization recommendations for TT-Metal operations. This system identifies bottlenecks, inefficiencies, and potential improvements in neural network workloads running on Tenstorrent hardware. The document explains how the advice system works, the types of optimizations it suggests, and how to implement these suggestions in your code.

For information about how operations are classified (DRAM-bound, FLOP-bound, etc.), please refer to the [Performance Classification System](https://deepwiki.com/tenstorrent/tt-perf-report/5-performance-classification-system).

## 1. Advice Generation Pipeline

The Optimization Advice System analyzes performance data and generates tailored recommendations based on operation type and detected bottlenecks. The advice is displayed after the performance report table in the command line output.

Sources: [src/tt_perf_report/perf_report.py 666-672](https://github.com/tenstorrent/tt-perf-report/blob/076a1b39/src/tt_perf_report/perf_report.py#L666-L672)[src/tt_perf_report/perf_report.py 731-799](https://github.com/tenstorrent/tt-perf-report/blob/076a1b39/src/tt_perf_report/perf_report.py#L731-L799)

## 2. Advice Categories

The system provides four main categories of optimization advice:

### 2.1 Host Operation Advice

Operations running on the host CPU (`(torch)` operations) are identified and flagged for potential migration to the device.

Sources: [src/tt_perf_report/perf_report.py 674-681](https://github.com/tenstorrent/tt-perf-report/blob/076a1b39/src/tt_perf_report/perf_report.py#L674-L681)

### 2.2 Op-to-Op Gap Advice

Operations with high gaps (>6.5 μs) between them indicate potential host-device synchronization issues.

Sources: [src/tt_perf_report/perf_report.py 683-706](https://github.com/tenstorrent/tt-perf-report/blob/076a1b39/src/tt_perf_report/perf_report.py#L683-L706)

### 2.3 Matmul Optimization Advice

The most sophisticated advice is for matrix multiplication operations, which are analyzed based on their performance classification.

Sources: [src/tt_perf_report/perf_report.py 712-728](https://github.com/tenstorrent/tt-perf-report/blob/076a1b39/src/tt_perf_report/perf_report.py#L712-L728)[src/tt_perf_report/perf_report.py 731-799](https://github.com/tenstorrent/tt-perf-report/blob/076a1b39/src/tt_perf_report/perf_report.py#L731-L799)

### 2.4 Math Fidelity Advice

For all matmul operations, the system analyzes whether the current math fidelity setting (HiFi4, HiFi2, LoFi) is appropriate for the datatypes used.

Sources: [src/tt_perf_report/perf_report.py 135-205](https://github.com/tenstorrent/tt-perf-report/blob/076a1b39/src/tt_perf_report/perf_report.py#L135-L205)

## 3. Advice Generation Implementation

### 3.1 Main Advice Generation Function

The main advice generation function (`print_advice_section`) orchestrates the analysis of different operation types:

Sources: [src/tt_perf_report/perf_report.py 666-672](https://github.com/tenstorrent/tt-perf-report/blob/076a1b39/src/tt_perf_report/perf_report.py#L666-L672)

### 3.2 Matmul Advice Generation

The `generate_matmul_advice` function is the core of the optimization advice system, analyzing multiple factors to generate tailored recommendations:

Sources: [src/tt_perf_report/perf_report.py 731-799](https://github.com/tenstorrent/tt-perf-report/blob/076a1b39/src/tt_perf_report/perf_report.py#L731-L799)

## 4. Implementing the Suggested Optimizations

This section explains how to implement the optimizations suggested by the advice system.

### 4.1 Moving Host Operations to Device

When the advice system identifies host operations (`(torch)` operations), it suggests moving them to the device.

**Implementation:**

*   Identify the PyTorch operation that's running on the host
*   Find the equivalent TT-Metal operation or implement a custom device kernel
*   Replace the PyTorch operation with the TT-Metal equivalent in your code

### 4.2 Reducing Op-to-Op Gaps

High op-to-op gaps indicate excessive host-device synchronization overhead.

**Implementation:**

*   Enable asynchronous execution with `device.enable_async(True)`
*   Run with tracing mode to reduce overhead
*   For advanced users: Convert runtime arguments to compile-time arguments in kernels

### 4.3 Optimizing DRAM-bound Matmuls

For matmuls classified as DRAM-bound, the system recommends using a DRAM-sharded program configuration.

**Implementation:**

Sources: [src/tt_perf_report/perf_report.py 746-748](https://github.com/tenstorrent/tt-perf-report/blob/076a1b39/src/tt_perf_report/perf_report.py#L746-L748)

### 4.4 Optimizing FLOP-bound Matmuls

For FLOP-bound matmuls, the advice system suggests increasing the number of cores.

**Implementation:**

Sources: [src/tt_perf_report/perf_report.py 755-756](https://github.com/tenstorrent/tt-perf-report/blob/076a1b39/src/tt_perf_report/perf_report.py#L755-L756)

### 4.5 Optimizing SLOW Matmuls

For matmuls classified as SLOW, the system provides advice on memory placement and block sizes.

**Implementation:**

Sources: [src/tt_perf_report/perf_report.py 760-796](https://github.com/tenstorrent/tt-perf-report/blob/076a1b39/src/tt_perf_report/perf_report.py#L760-L796)

### 4.6 Optimizing Math Fidelity

The advice system suggests appropriate math fidelity settings based on input and output datatypes.

**Implementation:**

Sources: [src/tt_perf_report/perf_report.py 135-205](https://github.com/tenstorrent/tt-perf-report/blob/076a1b39/src/tt_perf_report/perf_report.py#L135-L205)[src/tt_perf_report/perf_report.py 741-757](https://github.com/tenstorrent/tt-perf-report/blob/076a1b39/src/tt_perf_report/perf_report.py#L741-L757)

## 5. Advice Output Format

The optimization advice is displayed in the console output after the performance table:

```
💡 Advice 💡
============

Fallback
--------
[Host operations displayed here]

These ops should be moved to run on device.

High Op-to-Op Gap
----------------
[High gap operations displayed here]

These ops have a >6us gap since the previous operation. Running with tracing could save X us (Y% of overall time)
Alternatively ensure device is not waiting for the host and use device.enable_async(True). Experts can try moving runtime args in the kernels to compile-time args.

Matmul Optimization
-------------------
[Matmul operation displayed here]
- [Specific advice for this matmul]
- [Additional advice if applicable]
```

If using CSV output with the `--csv` option, the advice is included as an additional column:

| ID | OP Code | ... | Advice |
| --- | --- | --- | --- |
| 1 | Matmul 1024 x 1024 x 1024 | ... | Try a DRAM-sharded program config • Increase grid size (currently using 12) |
| 2 | Matmul 2048 x 1024 x 1024 | ... | If possible place input 0 in L1 (currently in DRAM) • in0_block_w=1 is small, try in0_block_w=2 or above |

Sources: [src/tt_perf_report/perf_report.py 634-663](https://github.com/tenstorrent/tt-perf-report/blob/076a1b39/src/tt_perf_report/perf_report.py#L634-L663)[src/tt_perf_report/perf_report.py 666-728](https://github.com/tenstorrent/tt-perf-report/blob/076a1b39/src/tt_perf_report/perf_report.py#L666-L728)

## 6. Conclusion

The Optimization Advice System in tt-perf-report provides actionable recommendations to improve the performance of your TT-Metal workloads. By following these suggestions, you can optimize memory usage, computational efficiency, and reduce host-device synchronization overhead.

The system is most effective for matrix multiplication operations, providing detailed advice based on performance classification (DRAM-bound, FLOP-bound, or SLOW) and analyzing multiple factors including math fidelity, memory placement, and block sizes.

To get the most out of the advice system, run tt-perf-report on your workload, implement the suggested optimizations incrementally, and re-run the analysis to measure the impact of your changes.

Dismiss
Refresh this wiki

Enter email to refresh
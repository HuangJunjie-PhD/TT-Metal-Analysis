---
project: tt-perf-report
github: https://github.com/tenstorrent/tt-perf-report
deepwiki: https://deepwiki.com/tenstorrent/tt-perf-report/3-usage-guide
---

# Usage Guide

Relevant source files
*   [README.md](https://github.com/tenstorrent/tt-perf-report/blob/076a1b39/README.md?plain=1)
*   [src/tt_perf_report/perf_report.py](https://github.com/tenstorrent/tt-perf-report/blob/076a1b39/src/tt_perf_report/perf_report.py)

This guide provides comprehensive instructions for using the `tt-perf-report` tool to analyze performance traces from Tenstorrent Metal operations. You'll learn how to generate trace files, run the analysis tool, interpret results, and implement the suggested optimizations to improve your workloads on Tenstorrent hardware.

For detailed information on command line options, see [Command Line Options](https://deepwiki.com/tenstorrent/tt-perf-report/3.1-command-line-options). For information on trace generation, see [Generating Traces](https://deepwiki.com/tenstorrent/tt-perf-report/3.2-generating-traces).

## Basic Usage

The primary workflow for performance analysis with `tt-perf-report` follows these steps:

Sources: [README.md 9-41](https://github.com/tenstorrent/tt-perf-report/blob/076a1b39/README.md?plain=1#L9-L41)[perf_report.py 900-904](https://github.com/tenstorrent/tt-perf-report/blob/076a1b39/perf_report.py#L900-L904)

The simplest way to use the tool is:

This command analyzes the trace file and displays a performance report in the terminal, showing key metrics for each operation and providing optimization advice.

## Generating Performance Traces

To generate traces for analysis, you need to:

1.   Build Metal with performance tracing enabled:

1.   Run your workload with the tracy module:

This creates a CSV file containing timing data for all operations.

### Using Tracy Signposts

Tracy signposts mark specific sections of code for focused analysis. Add signposts to your Python code:

By default, `tt-perf-report` analyzes operations after the last signpost in the trace. This is typically the most relevant section for performance analysis (e.g., after warmup iterations).

You can control signpost behavior with these options:

*   `--signpost name`: Analyze operations after the specified signpost
*   `--ignore-signposts`: Analyze the entire trace, ignoring all signposts

Sources: [README.md 30-50](https://github.com/tenstorrent/tt-perf-report/blob/076a1b39/README.md?plain=1#L30-L50)[perf_report.py 97-117](https://github.com/tenstorrent/tt-perf-report/blob/076a1b39/perf_report.py#L97-L117)

## Filtering Operations

Each operation in the performance report is assigned a unique ID corresponding to its row in the CSV file. You can filter operations using the `--id-range` option:

This filtering is particularly useful for:

*   Isolating the decode phase in prefill+decode LLM inference
*   Analyzing a single transformer layer without embeddings/projections
*   Focusing on specific model components

Sources: [README.md 52-71](https://github.com/tenstorrent/tt-perf-report/blob/076a1b39/README.md?plain=1#L52-L71)[perf_report.py 861-893](https://github.com/tenstorrent/tt-perf-report/blob/076a1b39/perf_report.py#L861-L893)

## Output Options

The tool provides several options to control the output format:

*   `--min-percentage value`: Hide operations below the specified percentage of total time (default: 0.5%)
*   `--color/--no-color`: Force colored output or plain text output
*   `--csv FILENAME`: Output the report to a CSV file for further analysis
*   `--no-advice`: Show only the performance table, without the optimization advice section
*   `--no-host-ops`: Exclude host operations (torch CPU ops) from the report
*   `--raw-op-codes`: Include raw operation codes in the output

Sources: [README.md 73-79](https://github.com/tenstorrent/tt-perf-report/blob/076a1b39/README.md?plain=1#L73-L79)[perf_report.py 906-926](https://github.com/tenstorrent/tt-perf-report/blob/076a1b39/perf_report.py#L906-L926)

## Understanding the Performance Report

The performance report consists of two main sections:

1.   A table showing metrics for each operation
2.   An advice section with optimization suggestions

Sources: [perf_report.py 632-647](https://github.com/tenstorrent/tt-perf-report/blob/076a1b39/perf_report.py#L632-L647)[perf_report.py 666-672](https://github.com/tenstorrent/tt-perf-report/blob/076a1b39/perf_report.py#L666-L672)

### Performance Table Columns

| Column | Description |
| --- | --- |
| ID | Row number from the original CSV file |
| Total % | Percentage of total execution time |
| Bound | Performance classification (DRAM, FLOP, BOTH, SLOW, HOST) |
| OP Code | Operation name and parameters |
| Device Time | Time spent executing the operation on device (μs) |
| Op-to-Op Gap | Time between operations, including host overhead (μs) |
| Cores | Number of cores used by the operation (max 64 on Wormhole) |
| DRAM | Memory bandwidth achieved (GB/s) |
| DRAM % | Percentage of theoretical peak DRAM bandwidth (288 GB/s) |
| FLOPs | Compute throughput achieved (TFLOPs) |
| FLOPs % | Percentage of theoretical peak compute |
| Math Fidelity | Precision configuration used for matrix operations |

Sources: [README.md 82-110](https://github.com/tenstorrent/tt-perf-report/blob/076a1b39/README.md?plain=1#L82-L110)[perf_report.py 998-1011](https://github.com/tenstorrent/tt-perf-report/blob/076a1b39/perf_report.py#L998-L1011)

### Performance Classifications

Operations are classified based on resource utilization:

Sources: [perf_report.py 514-539](https://github.com/tenstorrent/tt-perf-report/blob/076a1b39/perf_report.py#L514-L539)[perf_report.py 584-605](https://github.com/tenstorrent/tt-perf-report/blob/076a1b39/perf_report.py#L584-L605)

*   **DRAM**: Operation is memory bandwidth bound (>65% of peak DRAM)
*   **FLOP**: Operation is compute bound (>65% of peak FLOPs)
*   **BOTH**: Operation is both memory and compute bound
*   **SLOW**: Operation is neither memory nor compute bound (needs optimization)
*   **HOST**: Operation running on host CPU

### Color Coding in Terminal Output

The tool uses color highlighting to identify important information:

*   **Green**: Good utilization of available resources (DRAM, FLOP, or BOTH bound)
*   **Yellow**: Operations that could be optimized (SLOW)
*   **Red**: Problematic areas (host ops, high op-to-op gaps, underutilized cores)
*   **Grey**: Operations that contribute very little to the total time (below `--min-percentage` threshold)

Sources: [perf_report.py 551-628](https://github.com/tenstorrent/tt-perf-report/blob/076a1b39/perf_report.py#L551-L628)

### Advice Section

The advice section provides optimization suggestions for:

1.   **Fallback Operations**: Host operations that should be moved to run on device
2.   **High Op-to-Op Gap**: Operations with excessive gaps (>6.5μs) that suggest host overhead
3.   **Matmul Optimization**: Tailored advice for matrix multiplications based on their performance classification

For matrix multiplications, the tool provides specific advice:

*   For **DRAM-bound** operations: Suggests using DRAM-sharded configurations
*   For **FLOP-bound** operations: Suggests increasing the number of cores
*   For **SLOW** operations: Suggests optimizing memory layouts and block sizes

Sources: [perf_report.py 673-799](https://github.com/tenstorrent/tt-perf-report/blob/076a1b39/perf_report.py#L673-L799)

#### Math Fidelity Analysis

For matrix operations, the tool evaluates the math fidelity based on input and output datatypes:

| Math Fidelity | Peak TFLOPs/core | Use Case |
| --- | --- | --- |
| HiFi4 | 74 | Highest precision (BF16) |
| HiFi2 | 148 | Medium precision (BF8) |
| LoFi | 262 | Lowest precision (BF4) |

The tool may recommend adjusting the fidelity for better performance or precision.

Sources: [perf_report.py 51-60](https://github.com/tenstorrent/tt-perf-report/blob/076a1b39/perf_report.py#L51-L60)[perf_report.py 134-205](https://github.com/tenstorrent/tt-perf-report/blob/076a1b39/perf_report.py#L134-L205)

## Example Use Cases

### Basic Analysis

Run the tool with default settings to get a comprehensive performance report:

### Analyzing Specific Model Sections

View operations 100-200 (e.g., a specific transformer layer):

### Exporting for Further Analysis

Export the report to CSV for detailed analysis or sharing:

### Focused Analysis on Device Operations

Exclude host operations to focus on device performance:

### Performance Reporting

Generate just the performance table without advice for reporting:

Sources: [README.md 118-141](https://github.com/tenstorrent/tt-perf-report/blob/076a1b39/README.md?plain=1#L118-L141)

## Troubleshooting

### Multiple Devices

If your trace contains data from multiple devices, the tool will automatically detect and merge the device data:

Sources: [perf_report.py 803-858](https://github.com/tenstorrent/tt-perf-report/blob/076a1b39/perf_report.py#L803-L858)

### Handling Large Traces

For large trace files, use the filtering options to focus on specific sections:

Sources: [perf_report.py 906-926](https://github.com/tenstorrent/tt-perf-report/blob/076a1b39/perf_report.py#L906-L926)

Dismiss
Refresh this wiki

Enter email to refresh
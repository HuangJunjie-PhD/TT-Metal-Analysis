---
project: tt-toplike
github: https://github.com/tenstorrent/tt-toplike
deepwiki: https://deepwiki.com/tenstorrent/tt-toplike/7-workload-detection-and-process-monitoring
---

# Workload Detection and Process Monitoring

Relevant source files
*   [docs/PROCESS_MONITORING.md](https://github.com/tenstorrent/tt-toplike/blob/487bfe5a/docs/PROCESS_MONITORING.md?plain=1)
*   [src/workload/mod.rs](https://github.com/tenstorrent/tt-toplike/blob/487bfe5a/src/workload/mod.rs)

The `workload` module provides the intelligence layer of `tt-toplike`, moving beyond raw telemetry to identify **who** is using the hardware and **how** they are using it. It combines low-level Linux process tracking with high-level heuristic analysis to classify the operational state of Tenstorrent devices.

### Architectural Overview

The workload subsystem is divided into three primary functional areas:

1.   **Process Monitoring**: Identifying specific Linux PIDs interacting with `/dev/tenstorrent` character devices and hugepage memory mappings.
2.   **Inference Classification**: Analyzing telemetry trends (power, current, temperature) over time to determine if a device is idling, loading a model, or actively generating tokens.
3.   **Serving Detection**: Specialized probing for known inference servers (like vLLM) to correlate system-level metrics with high-level serving states.

### Relationship to Code Entities

The following diagram illustrates how the natural language concepts of "monitoring" and "classification" map to specific structs and modules within the `src/workload/` directory.

**Workload Module Structure**

**Sources:**[src/workload/mod.rs 1-20](https://github.com/tenstorrent/tt-toplike/blob/487bfe5a/src/workload/mod.rs#L1-L20)

* * *

### Process Monitor

The `ProcessMonitor` is responsible for identifying active workloads by scanning the Linux `procfs` filesystem. It detects hardware usage by looking for open file descriptors pointing to Tenstorrent devices and inspecting memory maps for hugepage allocations (1GB or 2MB).

*   **Cadence**: Scans are performed every 2 seconds to minimize CPU overhead [docs/PROCESS_MONITORING.md 43-44](https://github.com/tenstorrent/tt-toplike/blob/487bfe5a/docs/PROCESS_MONITORING.md?plain=1#L43-L44)
*   **Detection Logic**: It specifically targets `/dev/tenstorrent/*` descriptors and `hugepages-1G` or `pagesize-1GB` entries in `/proc/[pid]/maps`[docs/PROCESS_MONITORING.md 25-27](https://github.com/tenstorrent/tt-toplike/blob/487bfe5a/docs/PROCESS_MONITORING.md?plain=1#L25-L27)
*   **Permissions**: While the monitor runs for all users, non-root users may have limited visibility into processes owned by other users [docs/PROCESS_MONITORING.md 49](https://github.com/tenstorrent/tt-toplike/blob/487bfe5a/docs/PROCESS_MONITORING.md?plain=1#L49-L49)

For details, see [Process Monitor](https://deepwiki.com/tenstorrent/tt-toplike/7.1-process-monitor).

**Sources:**[src/workload/mod.rs 6-7](https://github.com/tenstorrent/tt-toplike/blob/487bfe5a/src/workload/mod.rs#L6-L7)[src/workload/mod.rs 12-13](https://github.com/tenstorrent/tt-toplike/blob/487bfe5a/src/workload/mod.rs#L12-L13)[docs/PROCESS_MONITORING.md 1-66](https://github.com/tenstorrent/tt-toplike/blob/487bfe5a/docs/PROCESS_MONITORING.md?plain=1#L1-L66)

* * *

### Inference State Classification

The `InferenceEngine` provides a heuristic-based classification of what the hardware is currently doing. Rather than relying on a single data point, it uses a rolling history of telemetry samples to calculate trends.

*   **Device States**: Categorizes device activity into states such as `Stalled`, `Throttling`, `Thinking`, `Generating`, and `LoadingModel`[src/workload/mod.rs 14-17](https://github.com/tenstorrent/tt-toplike/blob/487bfe5a/src/workload/mod.rs#L14-L17)
*   **Heuristics**: Uses the `PowerTrend` (slope and variance of power consumption) and `Confidence` scores to differentiate between steady-state inference and transient spikes [src/workload/mod.rs 14-17](https://github.com/tenstorrent/tt-toplike/blob/487bfe5a/src/workload/mod.rs#L14-L17)
*   **Server Integration**: The `InferenceServerProbe` can identify if the workload is a known "flavour" of server, such as vLLM, by checking process command lines and environment [src/workload/mod.rs 18-19](https://github.com/tenstorrent/tt-toplike/blob/487bfe5a/src/workload/mod.rs#L18-L19)

For details, see [Inference State Classification](https://deepwiki.com/tenstorrent/tt-toplike/7.2-inference-state-classification).

**Sources:**[src/workload/mod.rs 8-10](https://github.com/tenstorrent/tt-toplike/blob/487bfe5a/src/workload/mod.rs#L8-L10)[src/workload/mod.rs 14-17](https://github.com/tenstorrent/tt-toplike/blob/487bfe5a/src/workload/mod.rs#L14-L17)

* * *

### Feature Gating

Most workload detection features depend on Linux-specific interfaces. These are gated behind the `linux-procfs` feature flag, which is enabled by default in the TUI and GUI builds.

| Feature Flag | Module | Dependency |
| --- | --- | --- |
| `linux-procfs` | `process_monitor` | `/proc` filesystem access |
| `linux-procfs` | `serving` | `InferenceServerProbe` logic |
| (Always) | `inference` | Core `InferenceEngine` and `PowerTrend` |

**Sources:**[src/workload/mod.rs 6-19](https://github.com/tenstorrent/tt-toplike/blob/487bfe5a/src/workload/mod.rs#L6-L19)[docs/PROCESS_MONITORING.md 31-39](https://github.com/tenstorrent/tt-toplike/blob/487bfe5a/docs/PROCESS_MONITORING.md?plain=1#L31-L39)

Dismiss
Refresh this wiki

Enter email to refresh
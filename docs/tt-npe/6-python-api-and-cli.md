---
project: tt-npe
github: https://github.com/tenstorrent/tt-npe
deepwiki: https://deepwiki.com/tenstorrent/tt-npe/6-python-api-and-cli
---

# Python API and CLI

Relevant source files
*   [tt_npe/cpp/include/npeConfig.hpp](https://github.com/tenstorrent/tt-npe/blob/d1c8b91a/tt_npe/cpp/include/npeConfig.hpp)
*   [tt_npe/cpp/pybind/bindings.cpp](https://github.com/tenstorrent/tt-npe/blob/d1c8b91a/tt_npe/cpp/pybind/bindings.cpp)
*   [tt_npe/doc/tt_npe_pybind.html](https://github.com/tenstorrent/tt-npe/blob/d1c8b91a/tt_npe/doc/tt_npe_pybind.html)
*   [tt_npe/py/pycli/tt_npe.py](https://github.com/tenstorrent/tt-npe/blob/d1c8b91a/tt_npe/py/pycli/tt_npe.py)

## Purpose and Scope

This page provides an overview of tt-npe's Python interface, which consists of two primary entry points: a command-line tool (`tt_npe.py`) and a Python API (`tt_npe_pybind` module). Both interfaces provide access to the C++ simulation engine through Python bindings created with pybind11.

For detailed information about specific topics:

*   Command-line usage and options: see [Command Line Interface](https://deepwiki.com/tenstorrent/tt-npe/6.1-command-line-interface)
*   Complete Python API reference: see [Python API Reference](https://deepwiki.com/tenstorrent/tt-npe/6.2-python-api-reference)
*   Configuration parameters explained: see [Configuration Options](https://deepwiki.com/tenstorrent/tt-npe/6.3-configuration-options)
*   Creating workloads: see [Workload System](https://deepwiki.com/tenstorrent/tt-npe/3-workload-system)
*   Understanding simulation results: see [Statistics and Visualization Output](https://deepwiki.com/tenstorrent/tt-npe/2.4-statistics-and-visualization-output)

**Sources:**[README.md 109-182](https://github.com/tenstorrent/tt-npe/blob/d1c8b91a/README.md?plain=1#L109-L182)[docs/src/getting_started.md 1-111](https://github.com/tenstorrent/tt-npe/blob/d1c8b91a/docs/src/getting_started.md?plain=1#L1-L111)[docs/src/api.md 1-88](https://github.com/tenstorrent/tt-npe/blob/d1c8b91a/docs/src/api.md?plain=1#L1-L88)

## System Architecture

The Python interface serves as a bridge between user-facing tools and the C++ simulation core. The following diagram illustrates how components are organized:

Both the CLI and programmatic Python usage converge on the same `tt_npe_pybind` module, which exposes C++ classes and functions to Python. The CLI script ([tt_npe/py/pycli/tt_npe.py](https://github.com/tenstorrent/tt-npe/blob/d1c8b91a/tt_npe/py/pycli/tt_npe.py)) is essentially a convenience wrapper that translates command-line arguments into API calls.

**Sources:**[tt_npe/py/pycli/tt_npe.py 1-169](https://github.com/tenstorrent/tt-npe/blob/d1c8b91a/tt_npe/py/pycli/tt_npe.py#L1-L169)[tt_npe/cpp/pybind/bindings.cpp 1-318](https://github.com/tenstorrent/tt-npe/blob/d1c8b91a/tt_npe/cpp/pybind/bindings.cpp#L1-L318)

## Core Python Types and Objects

The `tt_npe_pybind` module exposes the following primary types:

**Sources:**[tt_npe/cpp/pybind/bindings.cpp 12-317](https://github.com/tenstorrent/tt-npe/blob/d1c8b91a/tt_npe/cpp/pybind/bindings.cpp#L12-L317)[tt_npe/doc/tt_npe_pybind.html 1-536](https://github.com/tenstorrent/tt-npe/blob/d1c8b91a/tt_npe/doc/tt_npe_pybind.html#L1-L536)

### Type Summary Table

| Type | Purpose | Key Methods/Fields |
| --- | --- | --- |
| `Config` | Holds all simulation parameters (device, timestep size, congestion model, etc.) | `device_name`, `cycles_per_timestep`, `congestion_model_name`, `emit_timeline_file` |
| `API` | Handle for running simulations | `runNPE(workload)` |
| `Workload` | Container for all transfers to simulate | `addPhase(phase)` |
| `Phase` | Logical group of transfers with no mutual dependencies | `addTransfer(transfer)` |
| `Transfer` | Represents packet series from source to destination(s) | Constructor takes packet_size, num_packets, src, dst, injection_rate, etc. |
| `Coord` | 3D coordinate: (device_id, row, col) | `device_id`, `row`, `col` |
| `Stats` | Simulation results on success | `per_device_stats`, `estimated_cycles`, `dram_bw_util`, `getCongestionImpact()` |
| `DeviceStats` | Per-device statistics | `estimated_cycles`, `golden_cycles`, `overall_avg_link_util`, `dram_bw_util` |
| `Exception` | Error information on failure | `err()` returns error message |

**Sources:**[tt_npe/cpp/pybind/bindings.cpp 18-317](https://github.com/tenstorrent/tt-npe/blob/d1c8b91a/tt_npe/cpp/pybind/bindings.cpp#L18-L317)[tt_npe/doc/tt_npe_pybind.html 64-427](https://github.com/tenstorrent/tt-npe/blob/d1c8b91a/tt_npe/doc/tt_npe_pybind.html#L64-L427)

## CLI to Python API Mapping

The CLI script demonstrates the canonical way to use the Python API. The following diagram shows how CLI arguments map to Config fields and API calls:

**Sources:**[tt_npe/py/pycli/tt_npe.py 13-169](https://github.com/tenstorrent/tt-npe/blob/d1c8b91a/tt_npe/py/pycli/tt_npe.py#L13-L169)[tt_npe/cpp/include/npeConfig.hpp 16-69](https://github.com/tenstorrent/tt-npe/blob/d1c8b91a/tt_npe/cpp/include/npeConfig.hpp#L16-L69)

## Usage Patterns

### CLI Usage Pattern

The typical CLI workflow is:

**Command Example:**

`tt_npe.py -w workload.json -d wormhole_b0 -c 128 -e --timeline-json-filepath output.json`
**Sources:**[tt_npe/py/pycli/tt_npe.py 117-169](https://github.com/tenstorrent/tt-npe/blob/d1c8b91a/tt_npe/py/pycli/tt_npe.py#L117-L169)[README.md 126-154](https://github.com/tenstorrent/tt-npe/blob/d1c8b91a/README.md?plain=1#L126-L154)

### Programmatic API Usage Pattern

For programmatic use, users directly import and use the `tt_npe_pybind` module:

**Code Example Structure:**

`import tt_npe_pybind as npe # Option 1: Load from JSONworkload = npe.createWorkloadFromJSON("trace.json", "wormhole_b0", is_noc_trace_format=True) # Option 2: Build programmaticallyworkload = npe.Workload()phase = npe.Phase()transfer = npe.Transfer(    packet_size=1024,    num_packets=10,    src=npe.Coord(0, 1, 1),    dst=npe.Coord(0, 2, 2),    injection_rate=0.0,  # inferred    phase_cycle_offset=0,    noc_type=npe.NocType.NOC_0)phase.addTransfer(transfer)workload.addPhase(phase) # Configure and runconfig = npe.Config()config.device_name = "wormhole_b0"config.cycles_per_timestep = 128config.emit_timeline_file = True api = npe.InitAPI(config)if api:    result = api.runNPE(workload)    if isinstance(result, npe.Stats):        print(f"Estimated cycles: {result.estimated_cycles}")    else:  # npe.Exception        print(f"Error: {result.err()}")`
**Sources:**[tt_npe/cpp/pybind/bindings.cpp 243-317](https://github.com/tenstorrent/tt-npe/blob/d1c8b91a/tt_npe/cpp/pybind/bindings.cpp#L243-L317)[README.md 156-178](https://github.com/tenstorrent/tt-npe/blob/d1c8b91a/README.md?plain=1#L156-L178)[docs/src/api.md 52-74](https://github.com/tenstorrent/tt-npe/blob/d1c8b91a/docs/src/api.md?plain=1#L52-L74)

## Function Reference Overview

### Module-Level Functions

| Function | Signature | Returns | Purpose |
| --- | --- | --- | --- |
| `InitAPI` | `InitAPI(config: Config)` | `Optional[API]` | Creates simulation handle from configuration. Returns `None` on error. |
| `createWorkloadFromJSON` | `createWorkloadFromJSON(json_wl_filename: str, device_name: str, is_noc_trace_format: bool, verbose: bool)` | `Optional[Workload]` | Parses JSON file into Workload object. Handles both simplified workload format and tt-metal NoC trace format. |

**Sources:**[tt_npe/cpp/pybind/bindings.cpp 27-37](https://github.com/tenstorrent/tt-npe/blob/d1c8b91a/tt_npe/cpp/pybind/bindings.cpp#L27-L37)[tt_npe/cpp/pybind/bindings.cpp 308-316](https://github.com/tenstorrent/tt-npe/blob/d1c8b91a/tt_npe/cpp/pybind/bindings.cpp#L308-L316)

### Key Class Methods

| Class | Method | Purpose |
| --- | --- | --- |
| `API` | `runNPE(workload: Workload)` | Runs simulation and returns `Stats` on success or `Exception` on failure |
| `Config` | `set_verbosity_level(level: int)` | Sets verbosity (0-3) |
| `Workload` | `addPhase(phase: Phase)` | Adds a phase to the workload |
| `Workload` | `setGoldenResultCycles(dict)` | Sets ground-truth cycle counts for accuracy comparison |
| `Phase` | `addTransfer(transfer: Transfer)` | Adds a transfer to the phase |
| `Stats` | `getCongestionImpact()` | Calculates congestion impact percentage |
| `DeviceStats` | `getCongestionImpact()` | Calculates congestion impact for specific device |
| `DeviceStats` | `getAggregateEthBwUtil()` | Returns average Ethernet bandwidth utilization |
| `MulticastCoordSet` | `grid_size()` | Returns total number of coordinates in all grids |

**Sources:**[tt_npe/cpp/pybind/bindings.cpp 18-235](https://github.com/tenstorrent/tt-npe/blob/d1c8b91a/tt_npe/cpp/pybind/bindings.cpp#L18-L235)[tt_npe/doc/tt_npe_pybind.html 76-85](https://github.com/tenstorrent/tt-npe/blob/d1c8b91a/tt_npe/doc/tt_npe_pybind.html#L76-L85)

## Result Handling

After calling `api.runNPE(workload)`, the result must be type-checked to distinguish success from failure:

**Example result handling:**

`result = api.runNPE(workload) match type(result):    case npe.Stats:        print(f"Success! Runtime: {result.wallclock_runtime_us} us")        for device_id, dev_stats in result.per_device_stats.items():            print(f"Device {device_id}:")            print(f"  Estimated cycles: {dev_stats.estimated_cycles}")            print(f"  Congestion impact: {dev_stats.getCongestionImpact():.1f}%")            print(f"  DRAM BW util: {dev_stats.dram_bw_util:.2f}")    case npe.Exception:        print(f"Simulation failed: {result}")`
**Sources:**[tt_npe/py/pycli/tt_npe.py 158-166](https://github.com/tenstorrent/tt-npe/blob/d1c8b91a/tt_npe/py/pycli/tt_npe.py#L158-L166)[tt_npe/cpp/pybind/bindings.cpp 39-84](https://github.com/tenstorrent/tt-npe/blob/d1c8b91a/tt_npe/cpp/pybind/bindings.cpp#L39-L84)[tt_npe/cpp/pybind/bindings.cpp 154-181](https://github.com/tenstorrent/tt-npe/blob/d1c8b91a/tt_npe/cpp/pybind/bindings.cpp#L154-L181)

## Import and Installation

The `tt_npe_pybind` module is installed to `install/lib/` by the build system. The `ENV_SETUP` script configures the Python path to find it:

**Setup Commands:**

`cd tt-npe/./build-npe.sh              # Builds and installs to install/source ENV_SETUP            # Adds install/bin/ to PATH and install/lib/ to PYTHONPATHpython -c "import tt_npe_pybind as npe; print(npe.__doc__)"  # Verify import`
**Sources:**[README.md 53-74](https://github.com/tenstorrent/tt-npe/blob/d1c8b91a/README.md?plain=1#L53-L74)[docs/src/getting_started.md 17-24](https://github.com/tenstorrent/tt-npe/blob/d1c8b91a/docs/src/getting_started.md?plain=1#L17-L24)[README.md 112-116](https://github.com/tenstorrent/tt-npe/blob/d1c8b91a/README.md?plain=1#L112-L116)

## Environment Variables and Configuration

Several aspects of tt-npe behavior can be controlled through `Config` fields, which map to CLI arguments:

| Config Field | CLI Argument | Type | Default | Description |
| --- | --- | --- | --- | --- |
| `device_name` | `-d, --device` | str | `"wormhole_b0"` | Device architecture to simulate |
| `cycles_per_timestep` | `-c, --cycles-per-timestep` | int | 128 | Cycles per simulation timestep |
| `congestion_model_name` | `--cong-model` | str | `"fast"` | Congestion model: "fast" or "none" |
| `workload_json_filepath` | `-w, --workload` | str | `""` | Path to workload JSON file |
| `workload_is_noc_trace` | `-t, --workload-is-noc-trace` | bool | `False` | Parse as tt-metal NoC trace format |
| `emit_timeline_file` | `-e, --emit-timeline-file` | bool | `False` | Generate visualization timeline |
| `timeline_filepath` | `--timeline-json-filepath` | str | `"npe_timeline.json"` | Output path for timeline |
| `compress_timeline_output_file` | `--compress-timeline-output-file` | bool | `False` | Compress timeline with zstd |
| `infer_injection_rate_from_src` | `--no-injection-rate-inference` | bool | `True` | Infer injection rates from source core type |
| `topology_json` | `--topology-json` | str | `""` | Path to topology JSON for multi-chip |
| `use_legacy_timeline_format` | `--use-legacy-timeline-format` | bool | `False` | Use v0 timeline format instead of v1 |
| `scale_workload_schedule` | `--scale-workload-schedule` | float | `0.0` | Experimental: scale workload timing |

**Sources:**[tt_npe/py/pycli/tt_npe.py 13-109](https://github.com/tenstorrent/tt-npe/blob/d1c8b91a/tt_npe/py/pycli/tt_npe.py#L13-L109)[tt_npe/cpp/include/npeConfig.hpp 16-69](https://github.com/tenstorrent/tt-npe/blob/d1c8b91a/tt_npe/cpp/include/npeConfig.hpp#L16-L69)[tt_npe/cpp/pybind/bindings.cpp 193-218](https://github.com/tenstorrent/tt-npe/blob/d1c8b91a/tt_npe/cpp/pybind/bindings.cpp#L193-L218)

## Error Handling

The Python API uses two error handling mechanisms:

1.   **Optional return values**: `InitAPI()` and `createWorkloadFromJSON()` return `None` on failure
2.   **Exception as result type**: `api.runNPE()` returns `npe.Exception` object (not a Python exception) on simulation failure

**CLI error handling example:**[tt_npe/py/pycli/tt_npe.py 111-116](https://github.com/tenstorrent/tt-npe/blob/d1c8b91a/tt_npe/py/pycli/tt_npe.py#L111-L116)[tt_npe/py/pycli/tt_npe.py 142-150](https://github.com/tenstorrent/tt-npe/blob/d1c8b91a/tt_npe/py/pycli/tt_npe.py#L142-L150)[tt_npe/py/pycli/tt_npe.py 152-166](https://github.com/tenstorrent/tt-npe/blob/d1c8b91a/tt_npe/py/pycli/tt_npe.py#L152-L166)

**Sources:**[tt_npe/cpp/pybind/bindings.cpp 27-38](https://github.com/tenstorrent/tt-npe/blob/d1c8b91a/tt_npe/cpp/pybind/bindings.cpp#L27-L38)[tt_npe/cpp/pybind/bindings.cpp 183-190](https://github.com/tenstorrent/tt-npe/blob/d1c8b91a/tt_npe/cpp/pybind/bindings.cpp#L183-L190)

## Serialization Support

The `Stats` and `DeviceStats` classes support Python pickle serialization, enabling result caching and parallel processing:

`import pickleimport tt_npe_pybind as npe # Run simulation and cache resultresult = api.runNPE(workload)if isinstance(result, npe.Stats):    with open('cached_stats.pkl', 'wb') as f:        pickle.dump(result, f)        # Later: load cached result    with open('cached_stats.pkl', 'rb') as f:        cached_stats = pickle.load(f)        print(cached_stats.per_device_stats[0].dram_bw_util)`
This is particularly useful in the batch trace analysis workflow ([npe_analyze_noc_trace_dir.py](https://github.com/tenstorrent/tt-npe/blob/d1c8b91a/npe_analyze_noc_trace_dir.py)) where results from multiple operations are collected and aggregated.

**Sources:**[tt_npe/cpp/pybind/bindings.cpp 85-152](https://github.com/tenstorrent/tt-npe/blob/d1c8b91a/tt_npe/cpp/pybind/bindings.cpp#L85-L152)[tt_npe/cpp/pybind/bindings.cpp 168-181](https://github.com/tenstorrent/tt-npe/blob/d1c8b91a/tt_npe/cpp/pybind/bindings.cpp#L168-L181)

This wiki is featured in the [repository](https://github.com/tenstorrent/tt-npe/blob/main/README.md)

Dismiss
Refresh this wiki

Enter email to refresh
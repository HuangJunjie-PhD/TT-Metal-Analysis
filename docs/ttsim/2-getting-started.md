---
project: ttsim
github: https://github.com/tenstorrent/ttsim
deepwiki: https://deepwiki.com/tenstorrent/ttsim/2-getting-started
---

# Getting Started

Relevant source files
*   [README.md](https://github.com/tenstorrent/ttsim/blob/67b1733e/README.md?plain=1)

This page guides you through the initial setup and configuration of ttsim, from installation through running your first workload. By the end of this guide, you will have a working ttsim installation and will have successfully executed a TT-Metalium example on the simulator.

For detailed architecture information, see [Architecture](https://deepwiki.com/tenstorrent/ttsim/3-architecture). For advanced configuration options, see [Configuration](https://deepwiki.com/tenstorrent/ttsim/4.1-configuration). For comprehensive workload execution guidance, see [Running Workloads](https://deepwiki.com/tenstorrent/ttsim/4.2-running-workloads).

* * *

## Prerequisites

Before installing ttsim, you must have the following in place:

| Requirement | Description | Verification |
| --- | --- | --- |
| **Operating System** | Linux/x86_64 | `uname -m` should return `x86_64` |
| **TT-Metalium** | Tenstorrent software stack installed and built | Directory must exist with compiled binaries |
| **TT_METAL_HOME** | Environment variable pointing to TT-Metalium installation | `echo $TT_METAL_HOME` should return valid path |

The simulator requires TT-Metalium because it acts as a drop-in replacement for physical hardware within the TT-Metalium execution environment. The simulator exports an API that TT-Metalium communicates with, and TT-Metalium provides the SOC descriptor files that configure the simulator.

**Sources:**[README.md 23-25](https://github.com/tenstorrent/ttsim/blob/67b1733e/README.md?plain=1#L23-L25)

* * *

## Installation Workflow

The installation process consists of downloading architecture-specific simulator binaries and placing them in a dedicated directory.

**Installation Workflow Diagram**

**Sources:**[README.md 27-38](https://github.com/tenstorrent/ttsim/blob/67b1733e/README.md?plain=1#L27-L38)

### Step 1: Create Installation Directory

Create a dedicated directory to hold the simulator binaries. While `~/sim` is used in this guide, you may choose any location.

`mkdir -p ~/simcd ~/sim`
### Step 2: Download Simulator Binaries

Download the architecture-specific simulator binaries from the GitHub releases page. Replace `vX.Y` with the desired version number (e.g., `v1.0`).

For **Wormhole**:

`wget https://github.com/tenstorrent/ttsim/releases/download/vX.Y/libttsim_wh.so`
For **Blackhole**:

`wget https://github.com/tenstorrent/ttsim/releases/download/vX.Y/libttsim_bh.so`
Each simulator is a single shared library file (`libttsim_wh.so` for Wormhole, `libttsim_bh.so` for Blackhole) containing the complete simulation implementation for that architecture.

**Note:** Wormhole is currently in more mature shape than Blackhole. If you are just getting started and do not have a specific architecture requirement, choose Wormhole.

**Sources:**[README.md 27-38](https://github.com/tenstorrent/ttsim/blob/67b1733e/README.md?plain=1#L27-L38)[README.md 18-19](https://github.com/tenstorrent/ttsim/blob/67b1733e/README.md?plain=1#L18-L19)

* * *

## Configuration

The simulator requires three configuration elements to function correctly: environment variable setup, SOC descriptor file placement, and dispatch mode configuration.

**Configuration Dependency Diagram**

**Sources:**[README.md 40-61](https://github.com/tenstorrent/ttsim/blob/67b1733e/README.md?plain=1#L40-L61)

### Environment Variables

Set the `TT_METAL_SIMULATOR` environment variable to point to the simulator binary. This variable tells TT-Metalium to use the simulator instead of physical hardware.

For **Wormhole**:

`export TT_METAL_SIMULATOR=~/sim/libttsim_wh.so`
For **Blackhole**:

`export TT_METAL_SIMULATOR=~/sim/libttsim_bh.so`
Verify that `TT_METAL_HOME` is still set:

`echo $TT_METAL_HOME# Should output the path to your TT-Metalium installation`
**Sources:**[README.md 47](https://github.com/tenstorrent/ttsim/blob/67b1733e/README.md?plain=1#L47-L47)[README.md 56](https://github.com/tenstorrent/ttsim/blob/67b1733e/README.md?plain=1#L56-L56)

### SOC Descriptor Placement

The simulator requires an SOC descriptor YAML file named `soc_descriptor.yaml` to exist in the same directory as the `libttsim_*.so` file. This file describes the chip's architectural configuration, including core counts, memory layouts, and interconnect topology.

For **Wormhole**, copy `wormhole_b0_80_arch.yaml`:

`cp $TT_METAL_HOME/tt_metal/soc_descriptors/wormhole_b0_80_arch.yaml ~/sim/soc_descriptor.yaml`
For **Blackhole**, copy `blackhole_140_arch.yaml`:

`cp $TT_METAL_HOME/tt_metal/soc_descriptors/blackhole_140_arch.yaml ~/sim/soc_descriptor.yaml`
The file **must** be named `soc_descriptor.yaml` exactly. The simulator will fail to initialize if this file is missing or has a different name.

**Sources:**[README.md 48](https://github.com/tenstorrent/ttsim/blob/67b1733e/README.md?plain=1#L48-L48)[README.md 57](https://github.com/tenstorrent/ttsim/blob/67b1733e/README.md?plain=1#L57-L57)

### Dispatch Mode Requirement

ttsim currently requires slow dispatch mode. Fast dispatch is not yet implemented. You must set `TT_METAL_SLOW_DISPATCH_MODE=1` when running any command.

This is a **mandatory requirement**, not an optional configuration. Attempting to run without this setting will result in errors.

**Sources:**[README.md 64](https://github.com/tenstorrent/ttsim/blob/67b1733e/README.md?plain=1#L64-L64)

* * *

## Running Your First Example

With configuration complete, you can now run a TT-Metalium example on the simulator.

**Execution Flow Diagram**

**Sources:**[README.md 40-61](https://github.com/tenstorrent/ttsim/blob/67b1733e/README.md?plain=1#L40-L61)

### For Wormhole

Navigate to your TT-Metalium installation and run an example:

`cd $TT_METAL_HOMETT_METAL_SLOW_DISPATCH_MODE=1 ./build/programming_examples/metal_example_add_2_integers_in_riscv`
The command structure is:

*   `TT_METAL_SLOW_DISPATCH_MODE=1`: Enables slow dispatch mode (required)
*   `./build/programming_examples/metal_example_add_2_integers_in_riscv`: The example binary to execute

**Sources:**[README.md 50-52](https://github.com/tenstorrent/ttsim/blob/67b1733e/README.md?plain=1#L50-L52)

### For Blackhole

`cd $TT_METAL_HOMETT_METAL_SLOW_DISPATCH_MODE=1 ./build/programming_examples/metal_example_add_2_integers_in_riscv`
The command is identical to Wormhole. The difference is determined by which `libttsim_*.so` file `TT_METAL_SIMULATOR` points to.

**Sources:**[README.md 59-61](https://github.com/tenstorrent/ttsim/blob/67b1733e/README.md?plain=1#L59-L61)

* * *

## Verifying Success

A successful execution will complete without errors and produce output. The exact output depends on the example, but you should see:

1.   **No error messages** containing categories like `UndefinedBehavior`, `UnimplementedFunctionality`, `ConfigurationError`, etc.
2.   **Expected output** from the example program (e.g., computation results, timing information)
3.   **Clean exit** with return code 0

If you encounter errors, consult the [Error Reference](https://deepwiki.com/tenstorrent/ttsim/4.4-error-reference) page for detailed explanations of error categories.

**Common First-Run Issues:**

| Error Symptom | Likely Cause | Solution |
| --- | --- | --- |
| `soc_descriptor.yaml not found` | SOC descriptor not in same directory as `.so` | Copy SOC descriptor to `~/sim/` |
| `TT_METAL_SIMULATOR not set` | Environment variable not exported | Run `export TT_METAL_SIMULATOR=...` |
| Fast dispatch error | `TT_METAL_SLOW_DISPATCH_MODE` not set | Prefix command with `TT_METAL_SLOW_DISPATCH_MODE=1` |
| `UnimplementedFunctionality` | Example uses unimplemented simulator features | Try a different example or report issue |

**Sources:**[README.md 63-77](https://github.com/tenstorrent/ttsim/blob/67b1733e/README.md?plain=1#L63-L77)

* * *

## Configuration Summary Table

For quick reference, here is a complete configuration checklist:

| Item | Wormhole Value | Blackhole Value | Notes |
| --- | --- | --- | --- |
| **Simulator Binary** | `libttsim_wh.so` | `libttsim_bh.so` | Download from releases page |
| **TT_METAL_SIMULATOR** | `~/sim/libttsim_wh.so` | `~/sim/libttsim_bh.so` | Must point to `.so` file |
| **SOC Descriptor Source** | `wormhole_b0_80_arch.yaml` | `blackhole_140_arch.yaml` | From `$TT_METAL_HOME/tt_metal/soc_descriptors/` |
| **SOC Descriptor Target** | `~/sim/soc_descriptor.yaml` | `~/sim/soc_descriptor.yaml` | Must be named exactly `soc_descriptor.yaml` |
| **TT_METAL_SLOW_DISPATCH_MODE** | `1` | `1` | Mandatory, prefix every command |
| **TT_METAL_HOME** | Path to tt-metal | Path to tt-metal | Must be set before installation |

**Sources:**[README.md 40-61](https://github.com/tenstorrent/ttsim/blob/67b1733e/README.md?plain=1#L40-L61)

* * *

## Next Steps

Once you have successfully run your first example, you can:

1.   **Explore more examples**: Try other programs in `$TT_METAL_HOME/build/programming_examples/` and `$TT_METAL_HOME/build/test/`
2.   **Understand the architecture**: Read [Architecture](https://deepwiki.com/tenstorrent/ttsim/3-architecture) to learn how ttsim works internally
3.   **Learn about numerical accuracy**: See [Numerical Accuracy](https://deepwiki.com/tenstorrent/ttsim/4.3-numerical-accuracy) to understand bit-exactness guarantees and potential divergence sources
4.   **Configure advanced options**: See [Configuration](https://deepwiki.com/tenstorrent/ttsim/4.1-configuration) for additional environment variables and options
5.   **Run test suites**: See [Running Workloads](https://deepwiki.com/tenstorrent/ttsim/4.2-running-workloads) for guidance on running tt-metal and ttnn test suites
6.   **Report issues**: If you encounter problems, see [Reporting Issues](https://deepwiki.com/tenstorrent/ttsim/5.2-reporting-issues) for how to file bug reports

**Sources:**[README.md 1-121](https://github.com/tenstorrent/ttsim/blob/67b1733e/README.md?plain=1#L1-L121)

Dismiss
Refresh this wiki

Enter email to refresh
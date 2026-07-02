---
project: cosim-arch-checker
github: https://github.com/tenstorrent/cosim-arch-checker
deepwiki: https://deepwiki.com/tenstorrent/cosim-arch-checker/4-configuration
---

# Configuration

Relevant source files
*   [README.md](https://github.com/tenstorrent/cosim-arch-checker/blob/157c6637/README.md?plain=1)
*   [bridge/whisper/config/boom.json](https://github.com/tenstorrent/cosim-arch-checker/blob/157c6637/bridge/whisper/config/boom.json)

This document covers the configuration system for the cosim-arch-checker framework, including DUT parameters, Whisper ISS configuration, build-time options, and runtime flags. Configuration enables the framework to adapt to different RISC-V processor implementations and verification scenarios.

For specific Whisper configuration details, see [Whisper Configuration](https://deepwiki.com/tenstorrent/cosim-arch-checker/4.1-whisper-configuration). For DUT-specific parameter configuration, see [DUT Parameters](https://deepwiki.com/tenstorrent/cosim-arch-checker/4.2-dut-parameters). For build system configuration, see [Build System](https://deepwiki.com/tenstorrent/cosim-arch-checker/5-build-system).

## Configuration Architecture Overview

The cosim-arch-checker uses a multi-layered configuration approach that spans compile-time parameters, configuration files, build options, and runtime flags.

Sources: [README.md 47-60](https://github.com/tenstorrent/cosim-arch-checker/blob/157c6637/README.md?plain=1#L47-L60)[README.md 107-122](https://github.com/tenstorrent/cosim-arch-checker/blob/157c6637/README.md?plain=1#L107-L122)

## DUT Parameter Configuration

DUT-specific parameters are configured through compile-time constants in the `env/params.h` file. These parameters define the architectural characteristics of the processor being verified.

### Core Parameters

The primary DUT configuration parameters include:

| Parameter | Description | Example Value |
| --- | --- | --- |
| `k_NumHarts` | Number of hardware threads | `1` |
| `k_XLen` | Integer register width in bits | `64` |
| `k_VLen` | Vector register width in bits | `256` |

### Configuration Macros

The framework uses preprocessor macros to select predefined configuration sets:

Sources: [README.md 61-67](https://github.com/tenstorrent/cosim-arch-checker/blob/157c6637/README.md?plain=1#L61-L67)[README.md 109-112](https://github.com/tenstorrent/cosim-arch-checker/blob/157c6637/README.md?plain=1#L109-L112)

## Whisper Configuration Files

Whisper ISS configuration is managed through JSON configuration files that specify processor parameters, CSR initialization values, and architectural features.

### Configuration File Structure

Whisper configuration files define the reference model's architectural state:

| Section | Purpose | Key Parameters |
| --- | --- | --- |
| `xlen` | Register width | `64` |
| `isa` | ISA string | `"rv64gc"` |
| `reset_vec` | Reset vector address | `"0x10040"` |
| `vector` | Vector extension config | `bytes_per_vec`, `min_bytes_per_elem` |
| `csr` | CSR initialization | `misa`, `mip`, `mstatus` |

### Sample Configuration Mapping

Sources: [bridge/whisper/config/boom.json 1-33](https://github.com/tenstorrent/cosim-arch-checker/blob/157c6637/bridge/whisper/config/boom.json#L1-L33)[README.md 78-80](https://github.com/tenstorrent/cosim-arch-checker/blob/157c6637/README.md?plain=1#L78-L80)

## Build-Time Configuration

Build-time configuration is managed through Bazel build options and environment variables that specify paths to external tools and test artifacts.

### Required Build Options

| Option | Purpose | Example |
| --- | --- | --- |
| `WHISPER_PATH` | Path to Whisper executable | `/path/to/whisper` |
| `WHISPER_JSON` | Path to Whisper config file | `/path/to/ocelot.json` |
| `TESTFILE` | Path to test ELF binary | `/path/to/test.elf` |
| `BOOTCODE` | Path to boot ROM binary | `/path/to/bootrom` |

### Build Command Structure

The typical build command integrates these options:

```
bazel build :dpi --copt=-DCONFIG=MediumBoomVecConfig
```

Sources: [README.md 54-58](https://github.com/tenstorrent/cosim-arch-checker/blob/157c6637/README.md?plain=1#L54-L58)[README.md 107-111](https://github.com/tenstorrent/cosim-arch-checker/blob/157c6637/README.md?plain=1#L107-L111)

## Runtime Configuration

Runtime configuration is controlled through SystemVerilog plusargs that enable or disable specific functionality during simulation execution.

### Runtime Flags

| Flag | Default | Purpose |
| --- | --- | --- |
| `+cosim` | OFF | Enable cosimulation framework |
| `+harness_tracer` | OFF | Enable testbench tracing |
| `+dutmon_tracer` | OFF | Enable DUT monitor tracing |
| `+bridge_tracer` | ON | Enable bridge operation tracing |

### Runtime Execution Flow

Sources: [README.md 115-122](https://github.com/tenstorrent/cosim-arch-checker/blob/157c6637/README.md?plain=1#L115-L122)

## Configuration Dependencies and Validation

The configuration system has specific dependencies and validation requirements that ensure proper framework operation.

Sources: [README.md 47-60](https://github.com/tenstorrent/cosim-arch-checker/blob/157c6637/README.md?plain=1#L47-L60)[bridge/whisper/config/boom.json 1-33](https://github.com/tenstorrent/cosim-arch-checker/blob/157c6637/bridge/whisper/config/boom.json#L1-L33)

Dismiss
Refresh this wiki

Enter email to refresh
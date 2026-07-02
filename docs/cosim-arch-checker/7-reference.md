---
project: cosim-arch-checker
github: https://github.com/tenstorrent/cosim-arch-checker
deepwiki: https://deepwiki.com/tenstorrent/cosim-arch-checker/7-reference
---

# Reference

Relevant source files
*   [README.md](https://github.com/tenstorrent/cosim-arch-checker/blob/157c6637/README.md?plain=1)
*   [bridge/bridge.h](https://github.com/tenstorrent/cosim-arch-checker/blob/157c6637/bridge/bridge.h)

This page provides comprehensive technical reference material for the cosim-arch-checker framework, including API specifications, configuration parameters, command-line options, and key data structures. For integration guidance, see [Integration Guide](https://deepwiki.com/tenstorrent/cosim-arch-checker/6-integration-guide). For configuration details, see [Configuration](https://deepwiki.com/tenstorrent/cosim-arch-checker/4-configuration). For build system specifics, see [Build System](https://deepwiki.com/tenstorrent/cosim-arch-checker/5-build-system).

## API Reference

### DPI Interface Functions

The SystemVerilog DPI interface provides the primary mechanism for extracting architectural state from the DUT. These functions are called from the SystemVerilog testbench on each instruction retirement.

**Sources:**[README.md 69-76](https://github.com/tenstorrent/cosim-arch-checker/blob/157c6637/README.md?plain=1#L69-L76)

#### Core Monitoring Functions

| Function | Purpose | Parameters |
| --- | --- | --- |
| `monitor_gpr` | Integer register state | `name`, `hart`, `cycle`, `addr`, `data` |
| `monitor_fpr` | Floating-point register state | `name`, `hart`, `cycle`, `addr`, `data` |
| `monitor_vr` | Vector register state | `name`, `hart`, `cycle`, `addr`, `*data` |
| `monitor_csr` | Control/Status register state | `name`, `hart`, `cycle`, `addr`, `data` |
| `monitor_retire` | Instruction retirement | `name`, `hart`, `cycle`, `tag`, `pc`, `opcode`, `trap` |

**Sources:**[README.md 69-76](https://github.com/tenstorrent/cosim-arch-checker/blob/157c6637/README.md?plain=1#L69-L76)

### Bridge Class Interface

The `cBridge` class serves as the central orchestrator between DUT monitoring and Whisper ISS communication.

**Sources:**[bridge/bridge.h 12-93](https://github.com/tenstorrent/cosim-arch-checker/blob/157c6637/bridge/bridge.h#L12-L93)

## Configuration Parameters

### DUT Configuration

Core DUT parameters are defined in `env/params.h` and control the architectural features of the target processor.

| Parameter | Description | Example Value |
| --- | --- | --- |
| `k_NumHarts` | Number of hardware threads | `1` |
| `k_XLen` | RISC-V base integer register width | `64` |
| `k_VLen` | Vector register width in bits | `256` |

**Sources:**[README.md 61-67](https://github.com/tenstorrent/cosim-arch-checker/blob/157c6637/README.md?plain=1#L61-L67)

### Build-Time Options

**Sources:**[README.md 54-58](https://github.com/tenstorrent/cosim-arch-checker/blob/157c6637/README.md?plain=1#L54-L58)[README.md 108-112](https://github.com/tenstorrent/cosim-arch-checker/blob/157c6637/README.md?plain=1#L108-L112)

| Option | Description | Example |
| --- | --- | --- |
| `WHISPER_PATH` | Path to Whisper executable | `/opt/whisper/bin/whisper` |
| `WHISPER_JSON` | Path to Whisper configuration JSON | `bridge/whisper/config/ocelot.json` |
| `TESTFILE` | Path to test ELF binary | `/tests/basic.elf` |
| `BOOTCODE` | Path to boot ROM binary | `bootrom/bootrom` |
| `--copt=-DCONFIG` | Processor configuration | `MediumBoomVecConfig` |

## Runtime Options

### Simulation Flags

| Flag | Description | Default |
| --- | --- | --- |
| `+cosim` | Enable co-simulation checking | Required |
| `+harness_tracer` | Enable harness trace logging | OFF |
| `+dutmon_tracer` | Enable DUT monitor trace logging | OFF |
| `+bridge_tracer` | Enable bridge trace logging | ON |

**Sources:**[README.md 119-122](https://github.com/tenstorrent/cosim-arch-checker/blob/157c6637/README.md?plain=1#L119-L122)

### Whisper Command Generation

The framework automatically generates Whisper ISS commands with the following structure:

```
whisper <testfile> <bootcode> --harts <numHarts> --raw --configfile <json> --logfile iss_cosim.log --traceload --server whisper_connect
```

**Sources:**[README.md 85](https://github.com/tenstorrent/cosim-arch-checker/blob/157c6637/README.md?plain=1#L85-L85)

## Data Structures

### sWhisperState Structure

**Sources:**[bridge/bridge.h 32-50](https://github.com/tenstorrent/cosim-arch-checker/blob/157c6637/bridge/bridge.h#L32-L50)[bridge/bridge.h 52-61](https://github.com/tenstorrent/cosim-arch-checker/blob/157c6637/bridge/bridge.h#L52-L61)

### Resource and Source Enumerations

| Enum | Values | Purpose |
| --- | --- | --- |
| `eResource` | `k_IntReg=0`, `k_FpReg=1`, `k_VecReg=4` | Identify register type in state changes |
| `eSrc` | `k_Dut`, `k_Whisper` | Distinguish data source for CAC updates |

**Sources:**[bridge/bridge.h 52-61](https://github.com/tenstorrent/cosim-arch-checker/blob/157c6637/bridge/bridge.h#L52-L61)

## Integration Points

### Core Arch Checker Interface

The `CacCore` class provides the architectural comparison functionality:

**Sources:**[bridge/bridge.h 10](https://github.com/tenstorrent/cosim-arch-checker/blob/157c6637/bridge/bridge.h#L10-L10)[bridge/bridge.h 81](https://github.com/tenstorrent/cosim-arch-checker/blob/157c6637/bridge/bridge.h#L81-L81)

### Directory Structure Reference

```
cosim-arch-checker/
├── bin/                    # Build artifacts
├── env/                    # Environment configuration
│   └── params.h           # DUT parameters
├── mon/                   # Monitoring interfaces
│   └── mon_instr/
│       └── mon_instr_dpi.cc  # DPI implementation
├── bridge/                # Bridge orchestrator
│   └── whisper/
│       ├── config/        # Whisper configurations
│       │   └── ocelot.json
│       ├── whisper_client.cpp
│       └── whisper_client.h
└── cac/                   # Core Arch Checker
```

**Sources:**[README.md 29-45](https://github.com/tenstorrent/cosim-arch-checker/blob/157c6637/README.md?plain=1#L29-L45)

This reference provides the essential technical details needed for integrating, configuring, and troubleshooting the cosim-arch-checker framework in RISC-V verification environments.

Dismiss
Refresh this wiki

Enter email to refresh
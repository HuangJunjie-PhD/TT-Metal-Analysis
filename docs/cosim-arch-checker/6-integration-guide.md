---
project: cosim-arch-checker
github: https://github.com/tenstorrent/cosim-arch-checker
deepwiki: https://deepwiki.com/tenstorrent/cosim-arch-checker/6-integration-guide
---

# Integration Guide

Relevant source files
*   [README.md](https://github.com/tenstorrent/cosim-arch-checker/blob/157c6637/README.md?plain=1)
*   [docs/images/boom_cosim.png](https://github.com/tenstorrent/cosim-arch-checker/blob/157c6637/docs/images/boom_cosim.png)

This document provides comprehensive instructions for integrating the cosim-arch-checker framework with hardware simulation environments. It covers the essential steps needed to set up lockstep architectural verification between a RISC-V DUT and the Whisper ISS.

For specific integration with Boom core and Ocelot vector unit in Chipyard, see [Chipyard Integration](https://deepwiki.com/tenstorrent/cosim-arch-checker/6.1-chipyard-integration). For boot ROM setup details, see [Boot ROM](https://deepwiki.com/tenstorrent/cosim-arch-checker/6.2-boot-rom). For build system configuration, see [Build System](https://deepwiki.com/tenstorrent/cosim-arch-checker/5-build-system).

## Integration Overview

The integration process involves configuring four main components: DUT parameters, DPI interface connections, Whisper ISS configuration, and the build system. The framework operates by monitoring DUT architectural state through SystemVerilog DPI calls and comparing it against Whisper's execution in lockstep.

### Integration Architecture

**Sources:**[README.md 47-76](https://github.com/tenstorrent/cosim-arch-checker/blob/157c6637/README.md?plain=1#L47-L76)[README.md 103-122](https://github.com/tenstorrent/cosim-arch-checker/blob/157c6637/README.md?plain=1#L103-L122)

## Step 1: DUT Parameter Configuration

Configure DUT-specific parameters in `env/params.h` to match your processor implementation. These parameters define the architectural characteristics that the framework will monitor and verify.

### Required Parameters

| Parameter | Description | Example Values |
| --- | --- | --- |
| `k_NumHarts` | Number of hardware threads | `1`, `2`, `4` |
| `k_XLen` | Register width (32 or 64-bit) | `32`, `64` |
| `k_VLen` | Vector register width | `128`, `256`, `512` |

### Configuration Example

`// env/params.h configuration for 64-bit single-hart processor with 256-bit vectorsconst int k_NumHarts = 1;const int k_XLen = 64;const int k_VLen = 256;`
The framework uses these parameters during compilation to generate appropriate data structures and interface definitions. The `CONFIG` build flag automatically sets these parameters for known processor configurations.

**Sources:**[README.md 61-67](https://github.com/tenstorrent/cosim-arch-checker/blob/157c6637/README.md?plain=1#L61-L67)[README.md 111-112](https://github.com/tenstorrent/cosim-arch-checker/blob/157c6637/README.md?plain=1#L111-L112)

## Step 2: DPI Interface Integration

Integrate the monitoring DPI functions into your SystemVerilog testbench to extract architectural state from the DUT. These functions must be called whenever the corresponding architectural state changes in your DUT.

### DPI Function Interface

### Required DPI Function Calls

Call these functions from your testbench harness or top-level module:

`// Call when general-purpose register changesmonitor_gpr("instance_name", hart_id, cycle, register_addr, register_data);// Call when floating-point register changes  monitor_fpr("instance_name", hart_id, cycle, register_addr, register_data);// Call when vector register changesmonitor_vr("instance_name", hart_id, cycle, register_addr, vector_data_array);// Call when CSR changesmonitor_csr("instance_name", hart_id, cycle, csr_addr, csr_data);// Call when instruction retiresmonitor_retire("instance_name", hart_id, cycle, instruction_tag, pc, opcode, trap_flag);`
The `monitor_retire()` call triggers the comparison process and must be called for every retired instruction.

**Sources:**[README.md 69-76](https://github.com/tenstorrent/cosim-arch-checker/blob/157c6637/README.md?plain=1#L69-L76)[mon/mon_instr/mon_instr_dpi.cc 1-50](https://github.com/tenstorrent/cosim-arch-checker/blob/157c6637/mon/mon_instr/mon_instr_dpi.cc#L1-L50)

## Step 3: Whisper Configuration

Create a JSON configuration file for the Whisper ISS that matches your DUT's architectural configuration. This file defines processor features, CSR initialization values, and other ISS parameters.

### Whisper Configuration Structure

Place your configuration file in `bridge/whisper/config/` directory and reference it during the build process.

**Sources:**[README.md 78-80](https://github.com/tenstorrent/cosim-arch-checker/blob/157c6637/README.md?plain=1#L78-L80)[bridge/whisper/config/ocelot.json 1-50](https://github.com/tenstorrent/cosim-arch-checker/blob/157c6637/bridge/whisper/config/ocelot.json#L1-L50)

## Step 4: Build System Integration

Integrate the cosim-arch-checker build into your testbench build system using the provided Bazel target with required build-time options.

### Build Command Structure

`bazel build :dpi \  --copt=-DCONFIG=YourProcessorConfig \  --define WHISPER_PATH=/path/to/whisper/executable \  --define WHISPER_JSON=/path/to/your/config.json \  --define TESTFILE=/path/to/test/binary.elf \  --define BOOTCODE=/path/to/bootrom/binary`
### Build-Time Options

| Option | Purpose | Example |
| --- | --- | --- |
| `CONFIG` | Processor configuration macro | `MediumBoomVecConfig` |
| `WHISPER_PATH` | Path to Whisper ISS executable | `/tools/whisper/bin/whisper` |
| `WHISPER_JSON` | Path to Whisper config file | `bridge/whisper/config/boom.json` |
| `TESTFILE` | Path to test ELF binary | `/tests/riscv-tests/isa/rv64ui-p-add` |
| `BOOTCODE` | Path to boot ROM binary | `bootrom/bootrom.img` |

The build produces either `libcosim.so` (Makefile) or `dpi` binary (Bazel) that can be linked with your simulation.

**Sources:**[README.md 54-59](https://github.com/tenstorrent/cosim-arch-checker/blob/157c6637/README.md?plain=1#L54-L59)[README.md 108-112](https://github.com/tenstorrent/cosim-arch-checker/blob/157c6637/README.md?plain=1#L108-L112)

## Step 5: Runtime Integration

Run your simulation with the `+cosim` flag to enable architectural checking. The framework will automatically spawn the Whisper ISS process and begin lockstep verification.

### Runtime Flow

### Runtime Command Example

`# VCS simulation with cosim enabledmake -C sims/vcs/ run-binary-debug-hex \  CONFIG=MediumBoomVecConfig \  BINARY=/path/to/test.elf \  SIM_FLAGS="+cosim +bridge_tracer"`
### Runtime Options

| Flag | Description | Default |
| --- | --- | --- |
| `+cosim` | Enable architectural checking | OFF |
| `+harness_tracer` | Enable testbench tracing | OFF |
| `+dutmon_tracer` | Enable DUT monitor tracing | OFF |
| `+bridge_tracer` | Enable bridge debug output | ON |

**Sources:**[README.md 115-122](https://github.com/tenstorrent/cosim-arch-checker/blob/157c6637/README.md?plain=1#L115-L122)

## Integration Verification

When successfully integrated, the framework will output comparison results showing DUT and Whisper state for each retired instruction:

```
Cosim whisper command: /path/to/whisper /path/to/elf /path/to/bootrom --harts 1 --raw --configfile /path/to/json --logfile iss_cosim.log --traceload --server whisper_connect &
Whisper connect succeeded in 60 ms
<27> Whisper Step #0: [Hart=0, InstrTag=0x0, ChangeCount=1, PC=0x10040, Opcode=0x517, auipc x10, 0x0]
Step: 0
                 X10                     DUT:[Data:0000000000010040]
                                         SIM:[Data:0000000000010040]
                  PC                     DUT:[Data:0000000000010040]
                                         SIM:[Data:0000000000010040]
```

Mismatches will cause simulation termination with detailed error information identifying the failing architectural state.

**Sources:**[README.md 82-101](https://github.com/tenstorrent/cosim-arch-checker/blob/157c6637/README.md?plain=1#L82-L101)

Dismiss
Refresh this wiki

Enter email to refresh
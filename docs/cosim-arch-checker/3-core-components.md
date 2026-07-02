---
project: cosim-arch-checker
github: https://github.com/tenstorrent/cosim-arch-checker
deepwiki: https://deepwiki.com/tenstorrent/cosim-arch-checker/3-core-components
---

# Core Components

Relevant source files
*   [bridge/bridge.cc](https://github.com/tenstorrent/cosim-arch-checker/blob/157c6637/bridge/bridge.cc)
*   [bridge/bridge.h](https://github.com/tenstorrent/cosim-arch-checker/blob/157c6637/bridge/bridge.h)

This document provides a detailed examination of the four primary components that comprise the cosim-arch-checker framework: the Bridge System, DUT Monitoring Interface, Whisper Integration, and Core Arch Checker (CAC). These components work together to implement lockstep architectural verification between a RISC-V DUT and the Whisper ISS reference model.

For build system configuration of these components, see [Build System](https://deepwiki.com/tenstorrent/cosim-arch-checker/5-build-system). For integration with specific hardware environments, see [Integration Guide](https://deepwiki.com/tenstorrent/cosim-arch-checker/6-integration-guide).

## Component Architecture Overview

The cosim-arch-checker framework consists of four core components that orchestrate the comparison between DUT execution and reference model behavior:

Sources: [bridge/bridge.h 1-94](https://github.com/tenstorrent/cosim-arch-checker/blob/157c6637/bridge/bridge.h#L1-L94)[bridge/bridge.cc 1-274](https://github.com/tenstorrent/cosim-arch-checker/blob/157c6637/bridge/bridge.cc#L1-L274)

## Component Responsibilities

| Component | Primary Responsibility | Key Interfaces |
| --- | --- | --- |
| **DUT Monitoring Interface** | Extract architectural state from SystemVerilog testbench | `monitor_gpr()`, `monitor_fpr()`, `monitor_retire()` |
| **Bridge System** | Orchestrate DUT-Whisper synchronization and comparison | `processDutInstrRetire()`, `stepWhisper()`, `updateCac()` |
| **Whisper Integration** | Manage TCP communication with Whisper ISS | `whisperStep()`, `whisperChange()`, `whisperConnect()` |
| **Core Arch Checker** | Perform architectural state comparisons | `updateRegister()`, `step()`, `getStatus()` |

Sources: [bridge/bridge.h 18-28](https://github.com/tenstorrent/cosim-arch-checker/blob/157c6637/bridge/bridge.h#L18-L28)[bridge/bridge.cc 95-115](https://github.com/tenstorrent/cosim-arch-checker/blob/157c6637/bridge/bridge.cc#L95-L115)

## Data Flow Architecture

The instruction processing flow demonstrates how these components collaborate during each instruction retirement:

Sources: [bridge/bridge.cc 95-115](https://github.com/tenstorrent/cosim-arch-checker/blob/157c6637/bridge/bridge.cc#L95-L115)[bridge/bridge.cc 118-146](https://github.com/tenstorrent/cosim-arch-checker/blob/157c6637/bridge/bridge.cc#L118-L146)[bridge/bridge.cc 173-217](https://github.com/tenstorrent/cosim-arch-checker/blob/157c6637/bridge/bridge.cc#L173-L217)

## Bridge System Core Logic

The `cBridge` class serves as the central orchestrator with the following key responsibilities:

### State Management Structure

Sources: [bridge/bridge.h 12-93](https://github.com/tenstorrent/cosim-arch-checker/blob/157c6637/bridge/bridge.h#L12-L93)[bridge/bridge.h 32-50](https://github.com/tenstorrent/cosim-arch-checker/blob/157c6637/bridge/bridge.h#L32-L50)

### Resource Type Mapping

The bridge maps between DUT architectural resources and CAC resource identifiers:

| DUT Resource | Bridge Enum | CAC Resource | Purpose |
| --- | --- | --- | --- |
| Program Counter | N/A | `CAC_STATE_PC_ID` | Instruction pointer tracking |
| General Purpose Registers | `k_IntReg` | `eResource::k_IntReg` | Integer register file |
| Floating Point Registers | `k_FpReg` | `eResource::k_FpReg` | FP register file |
| Vector Registers | `k_VecReg` | `eResource::k_VecReg` | Vector register file |

Sources: [bridge/bridge.h 52-56](https://github.com/tenstorrent/cosim-arch-checker/blob/157c6637/bridge/bridge.h#L52-L56)[bridge/bridge.cc 149-170](https://github.com/tenstorrent/cosim-arch-checker/blob/157c6637/bridge/bridge.cc#L149-L170)[bridge/bridge.cc 220-237](https://github.com/tenstorrent/cosim-arch-checker/blob/157c6637/bridge/bridge.cc#L220-L237)

## Integration Points

### Command Line Configuration

The bridge system accepts runtime configuration through SystemVerilog `+args`:

| Argument | Purpose | Default |
| --- | --- | --- |
| `+testfile=<path>` | ELF binary to execute | Required |
| `+bootcode=<path>` | Boot ROM binary | Required |
| `+whisper_path=<path>` | Whisper ISS executable | Required |
| `+whisper_json_path=<path>` | Whisper configuration | Required |
| `+arch_checks_exclude=<list>` | Skip checks on instruction types | None |
| `+bridge_tracer` | Enable debug tracing | Disabled |

Sources: [bridge/bridge.cc 29-52](https://github.com/tenstorrent/cosim-arch-checker/blob/157c6637/bridge/bridge.cc#L29-L52)

### Whisper Process Management

The bridge automatically manages the Whisper ISS server process:

Sources: [bridge/bridge.cc 54-91](https://github.com/tenstorrent/cosim-arch-checker/blob/157c6637/bridge/bridge.cc#L54-L91)

For detailed implementation of each component, see:

*   Bridge System implementation: [Bridge System](https://deepwiki.com/tenstorrent/cosim-arch-checker/3.1-bridge-system)
*   DUT monitoring interface: [DUT Monitoring Interface](https://deepwiki.com/tenstorrent/cosim-arch-checker/3.2-dut-monitoring-interface)
*   Whisper communication protocol: [Whisper Integration](https://deepwiki.com/tenstorrent/cosim-arch-checker/3.3-whisper-integration)
*   SystemVerilog DPI bindings: [SystemVerilog DPI](https://deepwiki.com/tenstorrent/cosim-arch-checker/3.4-systemverilog-dpi)

Dismiss
Refresh this wiki

Enter email to refresh
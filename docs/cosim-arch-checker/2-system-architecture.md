---
project: cosim-arch-checker
github: https://github.com/tenstorrent/cosim-arch-checker
deepwiki: https://deepwiki.com/tenstorrent/cosim-arch-checker/2-system-architecture
---

# System Architecture

Relevant source files
*   [README.md](https://github.com/tenstorrent/cosim-arch-checker/blob/157c6637/README.md?plain=1)
*   [bridge/bridge.h](https://github.com/tenstorrent/cosim-arch-checker/blob/157c6637/bridge/bridge.h)
*   [docs/images/boom_cosim.png](https://github.com/tenstorrent/cosim-arch-checker/blob/157c6637/docs/images/boom_cosim.png)

This document provides a detailed explanation of the cosim-arch-checker system architecture, focusing on the four-component lockstep verification framework and the data flow between components. The system performs instruction-level architectural verification by comparing RISC-V DUT execution against the Whisper ISS reference model.

For information about specific component implementations, see [Core Components](https://deepwiki.com/tenstorrent/cosim-arch-checker/3-core-components). For configuration details, see [Configuration](https://deepwiki.com/tenstorrent/cosim-arch-checker/4-configuration). For build system details, see [Build System](https://deepwiki.com/tenstorrent/cosim-arch-checker/5-build-system).

## Overview

The cosim-arch-checker implements a lockstep architectural verification system with four primary components that work together to verify RISC-V processor correctness:

**System Architecture Components**

Sources: [README.md 17-28](https://github.com/tenstorrent/cosim-arch-checker/blob/157c6637/README.md?plain=1#L17-L28)[bridge/bridge.h 12-94](https://github.com/tenstorrent/cosim-arch-checker/blob/157c6637/bridge/bridge.h#L12-L94)

## Component Architecture

The system consists of four main architectural components that form a complete verification pipeline:

### DUT DPI Interface

The DUT monitoring interface extracts architectural state from the SystemVerilog testbench through SystemVerilog DPI (Direct Programming Interface) calls. The interface provides the following monitoring functions:

| Function | Purpose |
| --- | --- |
| `monitor_gpr` | General-purpose register monitoring |
| `monitor_fpr` | Floating-point register monitoring |
| `monitor_vr` | Vector register monitoring |
| `monitor_csr` | Control and status register monitoring |
| `monitor_retire` | Instruction retirement notification |

Sources: [README.md 69-76](https://github.com/tenstorrent/cosim-arch-checker/blob/157c6637/README.md?plain=1#L69-L76)[mon/mon_instr/mon_instr_dpi.cc](https://github.com/tenstorrent/cosim-arch-checker/blob/157c6637/mon/mon_instr/mon_instr_dpi.cc)

### Whisper API Interface

The Whisper integration provides communication with the Whisper RISC-V ISS through a TCP socket connection. The `whisper_client.cpp` manages the protocol for stepping the reference model and retrieving architectural state changes.

Sources: [bridge/whisper/whisper_client.cpp](https://github.com/tenstorrent/cosim-arch-checker/blob/157c6637/bridge/whisper/whisper_client.cpp)[bridge/whisper/whisper_client.h](https://github.com/tenstorrent/cosim-arch-checker/blob/157c6637/bridge/whisper/whisper_client.h)[bridge/whisper/config/ocelot.json](https://github.com/tenstorrent/cosim-arch-checker/blob/157c6637/bridge/whisper/config/ocelot.json)

### Bridge Orchestrator

The `cBridge` class serves as the central orchestrator, inheriting from `cBridgeBase` and coordinating between the DUT interface, Whisper ISS, and Core Arch Checker. It maintains the `sWhisperState` structure and manages the verification flow.

Sources: [bridge/bridge.h 12-94](https://github.com/tenstorrent/cosim-arch-checker/blob/157c6637/bridge/bridge.h#L12-L94)[bridge/bridge.cc](https://github.com/tenstorrent/cosim-arch-checker/blob/157c6637/bridge/bridge.cc)

### Core Arch Checker

The `CacCore` component performs the actual architectural state comparison between DUT and Whisper ISS. It receives state updates from the bridge and determines pass/fail status for each instruction.

Sources: [bridge/bridge.h 81](https://github.com/tenstorrent/cosim-arch-checker/blob/157c6637/bridge/bridge.h#L81-L81)[cac/src](https://github.com/tenstorrent/cosim-arch-checker/blob/157c6637/cac/src#LNaN-LNaN)

## Data Flow Architecture

The instruction verification process follows a precise sequence of operations coordinated by the bridge:

Sources: [bridge/bridge.h 28](https://github.com/tenstorrent/cosim-arch-checker/blob/157c6637/bridge/bridge.h#L28-L28)[bridge/bridge.h 70-72](https://github.com/tenstorrent/cosim-arch-checker/blob/157c6637/bridge/bridge.h#L70-L72)

## Interface Definitions

### Bridge State Management

The bridge manages architectural state through several key data structures:

Sources: [bridge/bridge.h 32-50](https://github.com/tenstorrent/cosim-arch-checker/blob/157c6637/bridge/bridge.h#L32-L50)[bridge/bridge.h 52-61](https://github.com/tenstorrent/cosim-arch-checker/blob/157c6637/bridge/bridge.h#L52-L61)[bridge/bridge.h 78-93](https://github.com/tenstorrent/cosim-arch-checker/blob/157c6637/bridge/bridge.h#L78-L93)

## System Integration Points

The architecture provides several key integration points for different verification environments:

### Configuration Interface

*   DUT parameters via `env/params.h` (hart count, register widths)
*   Whisper configuration via JSON files in `bridge/whisper/config/`
*   Build-time configuration through Bazel and Makefile systems

### Runtime Control

*   Trace control via runtime flags (`+bridge_tracer`, `+dutmon_tracer`)
*   Dynamic whisper command generation via `getWhisperCmd()`
*   Timeout management through `k_whisperTimeoutInMilliSeconds`

### Error Handling

*   Mismatch detection triggers simulation termination via `vpi_control(vpiFinish)`
*   Configurable architectural checks exclusion via `archChecksExclude_`
*   Instruction-level exclusion via `archChecksInstrExclude_`

Sources: [bridge/bridge.h 83-92](https://github.com/tenstorrent/cosim-arch-checker/blob/157c6637/bridge/bridge.h#L83-L92)[README.md 54-59](https://github.com/tenstorrent/cosim-arch-checker/blob/157c6637/README.md?plain=1#L54-L59)[README.md 118-122](https://github.com/tenstorrent/cosim-arch-checker/blob/157c6637/README.md?plain=1#L118-L122)

Dismiss
Refresh this wiki

Enter email to refresh
---
project: cosim-arch-checker
github: https://github.com/tenstorrent/cosim-arch-checker
deepwiki: https://deepwiki.com/tenstorrent/cosim-arch-checker/1-overview
---

# Overview

Relevant source files
*   [README.md](https://github.com/tenstorrent/cosim-arch-checker/blob/157c6637/README.md?plain=1)
*   [docs/images/boom_cosim.png](https://github.com/tenstorrent/cosim-arch-checker/blob/157c6637/docs/images/boom_cosim.png)

## Purpose and Scope

The cosim-arch-checker framework provides lockstep architectural verification capabilities for RISC-V processors by comparing Design Under Test (DUT) execution against the Whisper RISC-V Instruction Set Simulator (ISS). This document covers the framework's architecture, core components, and integration interfaces.

For specific integration instructions with Chipyard environments, see [Chipyard Integration](https://deepwiki.com/tenstorrent/cosim-arch-checker/6.1-chipyard-integration). For detailed build system configuration, see [Build System](https://deepwiki.com/tenstorrent/cosim-arch-checker/5-build-system). For Whisper ISS configuration parameters, see [Whisper Configuration](https://deepwiki.com/tenstorrent/cosim-arch-checker/4.1-whisper-configuration).

## System Architecture

The framework implements a four-component architecture that enables cycle-accurate comparison between DUT and reference model execution:

The framework orchestrates instruction-level verification through four primary subsystems: the DUT monitoring interface extracts architectural state via SystemVerilog DPI, the Bridge component synchronizes execution between DUT and ISS, the Whisper client manages TCP communication with the reference model, and the Core Arch Checker performs state comparisons.

**Sources:**[README.md 1-28](https://github.com/tenstorrent/cosim-arch-checker/blob/157c6637/README.md?plain=1#L1-L28)[README.md 32-45](https://github.com/tenstorrent/cosim-arch-checker/blob/157c6637/README.md?plain=1#L32-L45)

## Component Interaction Model

The following diagram illustrates the specific code entities and their interaction patterns during instruction retirement processing:

The `cBridge` class serves as the central orchestrator, receiving DUT state from `mon_instr_dpi.cc` functions and coordinating with `whisper_client.cpp` for ISS synchronization. State comparison occurs through the external `CacCore` dependency, with results flowing back through the bridge to control simulation continuation.

**Sources:**[README.md 36-42](https://github.com/tenstorrent/cosim-arch-checker/blob/157c6637/README.md?plain=1#L36-L42)[README.md 69-76](https://github.com/tenstorrent/cosim-arch-checker/blob/157c6637/README.md?plain=1#L69-L76)

## Instruction Processing Data Flow

Each instruction retirement triggers a coordinated sequence where DUT architectural state is captured through DPI monitor functions, Whisper ISS is stepped to the corresponding instruction, and both states are fed to the Core Arch Checker for comparison. Mismatches result in immediate simulation termination via VPI control.

**Sources:**[README.md 69-76](https://github.com/tenstorrent/cosim-arch-checker/blob/157c6637/README.md?plain=1#L69-L76)[README.md 84-101](https://github.com/tenstorrent/cosim-arch-checker/blob/157c6637/README.md?plain=1#L84-L101)

## Key Interfaces and Configuration

The framework exposes several critical interfaces for integration:

| Interface Type | Code Location | Key Functions/Parameters |
| --- | --- | --- |
| DUT DPI Interface | [mon/mon_instr/mon_instr_dpi.cc](https://github.com/tenstorrent/cosim-arch-checker/blob/157c6637/mon/mon_instr/mon_instr_dpi.cc) | `monitor_gpr`, `monitor_fpr`, `monitor_vr`, `monitor_csr`, `monitor_retire` |
| DUT Configuration | [env/params.h](https://github.com/tenstorrent/cosim-arch-checker/blob/157c6637/env/params.h) | `k_NumHarts`, `k_XLen`, `k_VLen` |
| Whisper Configuration | [bridge/whisper/config/ocelot.json](https://github.com/tenstorrent/cosim-arch-checker/blob/157c6637/bridge/whisper/config/ocelot.json) | Processor parameters, CSR initialization |
| Build-time Options | Build system | `WHISPER_PATH`, `WHISPER_JSON`, `TESTFILE`, `BOOTCODE` |
| Runtime Options | Simulation | `+cosim`, `+harness_tracer`, `+dutmon_tracer`, `+bridge_tracer` |

The framework supports dual build approaches via Bazel and Makefile, with configuration managed through header files for DUT parameters and JSON files for Whisper ISS setup. Runtime behavior is controlled through SystemVerilog simulation flags.

**Sources:**[README.md 47-67](https://github.com/tenstorrent/cosim-arch-checker/blob/157c6637/README.md?plain=1#L47-L67)[README.md 78-81](https://github.com/tenstorrent/cosim-arch-checker/blob/157c6637/README.md?plain=1#L78-L81)[README.md 108-122](https://github.com/tenstorrent/cosim-arch-checker/blob/157c6637/README.md?plain=1#L108-L122)

Dismiss
Refresh this wiki

Enter email to refresh
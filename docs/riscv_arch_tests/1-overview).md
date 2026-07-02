---
project: riscv_arch_tests
github: https://github.com/tenstorrent/riscv_arch_tests
deepwiki: https://deepwiki.com/tenstorrent/riscv_arch_tests/1-overview)
---

# Overview

 Relevant source files

* [LICENSE](https://github.com/tenstorrent/riscv_arch_tests/blob/e54223b0/LICENSE)
* [LICENSE\_understanding.txt](https://github.com/tenstorrent/riscv_arch_tests/blob/e54223b0/LICENSE_understanding.txt)
* [README.md](https://github.com/tenstorrent/riscv_arch_tests/blob/e54223b0/README.md?plain=1)

## Purpose and Scope

This document provides an overview of the RISC-V architectural test repository, a comprehensive testing framework designed to validate RISC-V processor designs and instruction set simulators. The repository contains self-checking assembly tests that exercise various RISC-V extensions and privilege modes, along with the infrastructure needed to execute these tests on instruction set simulators like Whisper and Spike.

For detailed information about test architecture and organization, see [Test Architecture](/tenstorrent/riscv_arch_tests/2-test-architecture). For infrastructure components and execution details, see [Test Infrastructure](/tenstorrent/riscv_arch_tests/3-test-infrastructure). For simulator-specific configurations, see [Simulator Integration](/tenstorrent/riscv_arch_tests/4-simulator-integration).

## Repository Architecture

The repository is structured as a multi-layered system with test generation, execution infrastructure, and simulator integration components:

**Sources:** [README.md1-186](https://github.com/tenstorrent/riscv_arch_tests/blob/e54223b0/README.md?plain=1#L1-L186) [LICENSE1-201](https://github.com/tenstorrent/riscv_arch_tests/blob/e54223b0/LICENSE#L1-L201) [.gitmodules](https://github.com/tenstorrent/riscv_arch_tests/blob/e54223b0/.gitmodules) [infra/quals.py](https://github.com/tenstorrent/riscv_arch_tests/blob/e54223b0/infra/quals.py) [infra/smoke.py](https://github.com/tenstorrent/riscv_arch_tests/blob/e54223b0/infra/smoke.py) [infra/gen\_testlist.py](https://github.com/tenstorrent/riscv_arch_tests/blob/e54223b0/infra/gen_testlist.py) [infra/unpack.py](https://github.com/tenstorrent/riscv_arch_tests/blob/e54223b0/infra/unpack.py)

## Test Execution Flow

The system implements a comprehensive test execution pipeline that orchestrates test compilation, execution, and validation:

**Sources:** [infra/quals.py](https://github.com/tenstorrent/riscv_arch_tests/blob/e54223b0/infra/quals.py) [infra/smoke.py](https://github.com/tenstorrent/riscv_arch_tests/blob/e54223b0/infra/smoke.py) [infra/gen\_testlist.py](https://github.com/tenstorrent/riscv_arch_tests/blob/e54223b0/infra/gen_testlist.py) [whisper\_config.json](https://github.com/tenstorrent/riscv_arch_tests/blob/e54223b0/whisper_config.json) [whisper\_config\_vlen\_128.json](https://github.com/tenstorrent/riscv_arch_tests/blob/e54223b0/whisper_config_vlen_128.json) [whisper\_config\_vlen\_256.json](https://github.com/tenstorrent/riscv_arch_tests/blob/e54223b0/whisper_config_vlen_256.json) [Containerfile](https://github.com/tenstorrent/riscv_arch_tests/blob/e54223b0/Containerfile)

## Key System Components

### Test Generation and Organization

The repository contains tests organized by privilege modes, paging modes, and ISA extensions. Tests are generated using an internal Tenstorrent tool that parses the ISA specification from `riscv-opcodes` and compiles using `riscv-gnu-toolchain` release 2023.12.12.

**Test Directory Structure:**

* **Privilege Modes:** `machine`, `supervisor`, `user` [README.md38-41](https://github.com/tenstorrent/riscv_arch_tests/blob/e54223b0/README.md?plain=1#L38-L41)
* **Paging Modes:** `sv39`, `sv48`, `sv57`, `bare` [README.md44-48](https://github.com/tenstorrent/riscv_arch_tests/blob/e54223b0/README.md?plain=1#L44-L48)
* **Virtualization:** `bare_metal`, `h_ext` [README.md35-36](https://github.com/tenstorrent/riscv_arch_tests/blob/e54223b0/README.md?plain=1#L35-L36)
* **Extensions:** RV64-I, RV-M, RV-F, RV-D, RV-C, RV-V, Zfh, Zba/Zbb/Zbc/Zbs [README.md22-29](https://github.com/tenstorrent/riscv_arch_tests/blob/e54223b0/README.md?plain=1#L22-L29)

### Test Structure

Each test implements a two-section architecture:

| Section | Purpose | Components |
| --- | --- | --- |
| `.text` | Mini OS | Loader, Scheduler, Trap Handler, Hypervisor, End-of-test mechanism |
| `.code` | Test Logic | Operand setup, Instruction under test, Self-checking code |

**Sources:** [README.md52-68](https://github.com/tenstorrent/riscv_arch_tests/blob/e54223b0/README.md?plain=1#L52-L68)

### Infrastructure Components

The test infrastructure provides automated execution and management capabilities:

| Component | File | Purpose |
| --- | --- | --- |
| Test Runner | `infra/quals.py` | Execute individual test lists on ISS |
| Smoke Tests | `infra/smoke.py` | Comprehensive test suite execution |
| List Generator | `infra/gen_testlist.py` | Automated test list creation |
| Archive Handler | `infra/unpack.py` | Test archive management |

### Simulator Integration

The system integrates with two primary instruction set simulators:

* **Whisper:** Tenstorrent's RISC-V ISS with configurable vector lengths (128/256-bit)
* **Spike:** RISC-V ISS from riscv-software-src with custom build options

Build configuration for Spike includes custom options: `--enable-tt-stop-if-tohost-nonzero`, `--enable-tt-table-walk-debug`, `--enable-tt-expanded-dram-address-range`, `--enable-dual-endian` [README.md100](https://github.com/tenstorrent/riscv_arch_tests/blob/e54223b0/README.md?plain=1#L100-L100)

**Sources:** [README.md92-103](https://github.com/tenstorrent/riscv_arch_tests/blob/e54223b0/README.md?plain=1#L92-L103) [whisper\_config.json](https://github.com/tenstorrent/riscv_arch_tests/blob/e54223b0/whisper_config.json) [whisper\_config\_vlen\_128.json](https://github.com/tenstorrent/riscv_arch_tests/blob/e54223b0/whisper_config_vlen_128.json) [whisper\_config\_vlen\_256.json](https://github.com/tenstorrent/riscv_arch_tests/blob/e54223b0/whisper_config_vlen_256.json)

## Licensing and Dependencies

The repository is licensed under Apache License 2.0 with copyright held by Tenstorrent AI ULC [LICENSE178](https://github.com/tenstorrent/riscv_arch_tests/blob/e54223b0/LICENSE#L178-L178) The system includes third-party dependencies:

* **riscv-isa-sim** (Spike simulator)
* **riscv-opcodes** (ISA specification source)

Additional licensing considerations for hardware, models, or IP may require separate patent rights licensing [LICENSE\_understanding.txt1-4](https://github.com/tenstorrent/riscv_arch_tests/blob/e54223b0/LICENSE_understanding.txt#L1-L4)

## Usage Integration Points

The system provides multiple entry points for test execution:

1. **Single Test Execution:** `./infra/quals.py --quals_file  --iss  --vlen <128/256>`
2. **Comprehensive Testing:** `./test_all.bash`
3. **Container-based Execution:** Through GitLab CI pipeline with Podman containers

**Sources:** [README.md105-112](https://github.com/tenstorrent/riscv_arch_tests/blob/e54223b0/README.md?plain=1#L105-L112) [LICENSE1-201](https://github.com/tenstorrent/riscv_arch_tests/blob/e54223b0/LICENSE#L1-L201) [LICENSE\_understanding.txt1-4](https://github.com/tenstorrent/riscv_arch_tests/blob/e54223b0/LICENSE_understanding.txt#L1-L4)

Refresh this wiki
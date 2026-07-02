---
project: tt-system-firmware
github: https://github.com/tenstorrent/tt-system-firmware
deepwiki: https://deepwiki.com/tenstorrent/tt-system-firmware/8-cicd-infrastructure
---

# CI/CD Infrastructure

Relevant source files
*   [.github/Dockerfile](https://github.com/tenstorrent/tt-system-firmware/blob/ab59b09c/.github/Dockerfile)
*   [.github/ci_boards.json](https://github.com/tenstorrent/tt-system-firmware/blob/ab59b09c/.github/ci_boards.json)
*   [.github/workflows/build-fw.yml](https://github.com/tenstorrent/tt-system-firmware/blob/ab59b09c/.github/workflows/build-fw.yml)
*   [.github/workflows/build-native.yml](https://github.com/tenstorrent/tt-system-firmware/blob/ab59b09c/.github/workflows/build-native.yml)
*   [.github/workflows/cleanup-branches.yml](https://github.com/tenstorrent/tt-system-firmware/blob/ab59b09c/.github/workflows/cleanup-branches.yml)
*   [.github/workflows/compliance.yml](https://github.com/tenstorrent/tt-system-firmware/blob/ab59b09c/.github/workflows/compliance.yml)
*   [.github/workflows/copyright-check.yml](https://github.com/tenstorrent/tt-system-firmware/blob/ab59b09c/.github/workflows/copyright-check.yml)
*   [.github/workflows/hardware-long.yml](https://github.com/tenstorrent/tt-system-firmware/blob/ab59b09c/.github/workflows/hardware-long.yml)
*   [.github/workflows/hardware-smoke.yml](https://github.com/tenstorrent/tt-system-firmware/blob/ab59b09c/.github/workflows/hardware-smoke.yml)
*   [.github/workflows/license-check.yml](https://github.com/tenstorrent/tt-system-firmware/blob/ab59b09c/.github/workflows/license-check.yml)
*   [.github/workflows/metal.yml](https://github.com/tenstorrent/tt-system-firmware/blob/ab59b09c/.github/workflows/metal.yml)
*   [.github/workflows/prepare-container/action.yml](https://github.com/tenstorrent/tt-system-firmware/blob/ab59b09c/.github/workflows/prepare-container/action.yml)
*   [.github/workflows/prepare-zephyr/action.yml](https://github.com/tenstorrent/tt-system-firmware/blob/ab59b09c/.github/workflows/prepare-zephyr/action.yml)
*   [.github/workflows/run-unit-tests.yml](https://github.com/tenstorrent/tt-system-firmware/blob/ab59b09c/.github/workflows/run-unit-tests.yml)
*   [.github/workflows/verify-blob.yml](https://github.com/tenstorrent/tt-system-firmware/blob/ab59b09c/.github/workflows/verify-blob.yml)
*   [scripts/requirements.txt](https://github.com/tenstorrent/tt-system-firmware/blob/ab59b09c/scripts/requirements.txt)
*   [scripts/verify_blob.py](https://github.com/tenstorrent/tt-system-firmware/blob/ab59b09c/scripts/verify_blob.py)

## Purpose and Scope

This document describes the Continuous Integration and Continuous Deployment (CI/CD) infrastructure for the tt-system-firmware repository. It covers the GitHub Actions workflows, physical hardware test infrastructure, board configuration management, and quality assurance automation.

For information about the build system itself (Zephyr integration, firmware packaging), see [Build System](https://deepwiki.com/tenstorrent/tt-system-firmware/6-build-system). For details on testing methodologies and validation strategies, see [Testing and Validation](https://deepwiki.com/tenstorrent/tt-system-firmware/7-testing-and-validation).

* * *

## Overview

The CI/CD infrastructure provides automated validation across multiple dimensions:

*   **Build Pipelines**: Compile firmware for all supported board variants with signature verification
*   **Unit Testing**: Execute Zephyr Twister tests on simulated platforms
*   **Hardware Testing**: Validate on physical devices with JTAG access across multiple board types
*   **Quality Assurance**: Enforce code standards, copyright compliance, and license verification
*   **Integration Testing**: Validate firmware with TT-Metal runtime and ML workloads

All workflows are defined in the [.github/workflows](https://github.com/tenstorrent/tt-system-firmware/blob/ab59b09c/.github/workflows) directory and execute automatically on push, pull request, merge queue, or scheduled triggers.

**Sources**: [.github/workflows/hardware-smoke.yml](https://github.com/tenstorrent/tt-system-firmware/blob/ab59b09c/.github/workflows/hardware-smoke.yml)[.github/workflows/build-fw.yml](https://github.com/tenstorrent/tt-system-firmware/blob/ab59b09c/.github/workflows/build-fw.yml)[.github/workflows/run-unit-tests.yml](https://github.com/tenstorrent/tt-system-firmware/blob/ab59b09c/.github/workflows/run-unit-tests.yml)

* * *

## Workflow Trigger Matrix

The following table shows when each workflow executes:

| Workflow | Push (main) | Pull Request | Merge Group | Schedule | Manual |
| --- | --- | --- | --- | --- | --- |
| `build-fw.yml` | ✓ | ✓ | ✓ |  | ✓ |
| `hardware-smoke.yml` | ✓ | ✓ | ✓ |  | ✓ |
| `hardware-long.yml` |  | PR labeled |  | Daily 05:21 UTC | ✓ |
| `metal.yml` |  | PR labeled |  | Daily 00:00 UTC | ✓ |
| `run-unit-tests.yml` | ✓ | ✓ | ✓ |  | ✓ |
| `build-native.yml` | ✓ | ✓ | ✓ |  | ✓ |
| `compliance.yml` |  | ✓ |  |  | ✓ |
| `copyright-check.yml` | ✓ | ✓ | ✓ |  | ✓ |
| `license-check.yml` | ✓ | ✓ | ✓ |  | ✓ |
| `verify-blob.yml` |  | ✓ |  |  | ✓ |
| `cleanup-branches.yml` |  |  |  | Daily 05:00 UTC |  |

**Sources**: [.github/workflows/hardware-smoke.yml 3-22](https://github.com/tenstorrent/tt-system-firmware/blob/ab59b09c/.github/workflows/hardware-smoke.yml#L3-L22)[.github/workflows/hardware-long.yml 4-10](https://github.com/tenstorrent/tt-system-firmware/blob/ab59b09c/.github/workflows/hardware-long.yml#L4-L10)[.github/workflows/metal.yml 4-10](https://github.com/tenstorrent/tt-system-firmware/blob/ab59b09c/.github/workflows/metal.yml#L4-L10)

* * *

## Workflow Architecture

**Workflow Orchestration**: The `build-fw.yml` workflow is reusable and called by both `hardware-long.yml` and `metal.yml` to generate firmware artifacts before testing.

**Sources**: [.github/workflows/build-fw.yml 1-11](https://github.com/tenstorrent/tt-system-firmware/blob/ab59b09c/.github/workflows/build-fw.yml#L1-L11)[.github/workflows/hardware-long.yml 25-29](https://github.com/tenstorrent/tt-system-firmware/blob/ab59b09c/.github/workflows/hardware-long.yml#L25-L29)[.github/workflows/metal.yml 25-29](https://github.com/tenstorrent/tt-system-firmware/blob/ab59b09c/.github/workflows/metal.yml#L25-L29)

* * *

## Build Pipeline Architecture

**Parallelization Strategy**: The build matrix executes all board variants in parallel with `fail-fast: false`, allowing all builds to complete even if one fails.

**Sources**: [.github/workflows/build-fw.yml 36-52](https://github.com/tenstorrent/tt-system-firmware/blob/ab59b09c/.github/workflows/build-fw.yml#L36-L52)[.github/workflows/build-fw.yml 86-122](https://github.com/tenstorrent/tt-system-firmware/blob/ab59b09c/.github/workflows/build-fw.yml#L86-L122)[.github/workflows/build-fw.yml 349-380](https://github.com/tenstorrent/tt-system-firmware/blob/ab59b09c/.github/workflows/build-fw.yml#L349-L380)

* * *

## Container Infrastructure

### Base Container Image

The CI system uses a custom container image `ghcr.io/tenstorrent/tt-zephyr-platforms/ci-image:v18.12.0-rc1` that includes:

*   Zephyr SDK 0.17.2 with toolchains (ARM, RISC-V, x86_64, ARC)
*   Python 3.12 with required packages
*   OpenOCD with RISC-V and ARC RTT support
*   tt-kmd kernel module builds at `/opt/tenstorrent/kmd-builds/`
*   West meta-tool and Zephyr dependencies

**Container Build Process**:

**Sources**: [.github/Dockerfile 1-50](https://github.com/tenstorrent/tt-system-firmware/blob/ab59b09c/.github/Dockerfile#L1-L50)

### Container Setup Composite Actions

Two composite actions standardize environment preparation:

#### prepare-zephyr

Used for build-only jobs running on Ubuntu runners:

*   Installs Rust toolchain
*   Initializes West workspace from `west.yml` manifest
*   Downloads Zephyr SDK and sets up CMake packages
*   Verifies binary blobs with `west blobs fetch`

**Sources**: [.github/workflows/prepare-zephyr/action.yml 1-87](https://github.com/tenstorrent/tt-system-firmware/blob/ab59b09c/.github/workflows/prepare-zephyr/action.yml#L1-L87)

#### prepare-container

Used for hardware test jobs running in containers on self-hosted runners:

*   Applies git safe.directory workaround for UID mismatches
*   Cleans stale West manifests and shallow.lock files
*   Loads specified `tt-kmd` kernel module version
*   Updates Python packages (uninstalls PyPI versions, installs from VCS)
*   Verifies binary blobs with SHA256 checksums

**Sources**: [.github/workflows/prepare-container/action.yml 1-127](https://github.com/tenstorrent/tt-system-firmware/blob/ab59b09c/.github/workflows/prepare-container/action.yml#L1-L127)

* * *

## Hardware Test Execution Flow

**Test Isolation**: Each test job begins with `rm -rf *` to ensure clean state, critical for self-hosted runners that reuse workspace directories.

**Sources**: [.github/workflows/hardware-smoke.yml 65-139](https://github.com/tenstorrent/tt-system-firmware/blob/ab59b09c/.github/workflows/hardware-smoke.yml#L65-L139)[.github/workflows/hardware-long.yml 78-111](https://github.com/tenstorrent/tt-system-firmware/blob/ab59b09c/.github/workflows/hardware-long.yml#L78-L111)[.github/workflows/metal.yml 106-134](https://github.com/tenstorrent/tt-system-firmware/blob/ab59b09c/.github/workflows/metal.yml#L106-L134)

* * *

## Board Configuration System

### ci_boards.json Structure

The [.github/ci_boards.json](https://github.com/tenstorrent/tt-system-firmware/blob/ab59b09c/.github/ci_boards.json) file defines all hardware test platforms:

**Supported Configurations**:

| Board | Product | Runner | Metal Target | KMD Version |
| --- | --- | --- | --- | --- |
| `p100a` | Blackhole | `p100a-jtag` | `blackhole` | 2.5.0 |
| `p150a` | Blackhole | `p150a-jtag` | `blackhole` | 2.5.0 |
| `p300a` | Blackhole | `p300a-jtag` | `blackhole_p300` | 2.5.0 |
| `galaxy` | Blackhole | `tt-galaxy-bh` | `blackhole_glx` | 2.5.0 |
| `n150` | Wormhole | `n150-jtag` | N/A | 2.5.0 |
| `n300` | Wormhole | `n300-jtag` | N/A | 2.5.0 |

**Sources**: [.github/ci_boards.json 1-46](https://github.com/tenstorrent/tt-system-firmware/blob/ab59b09c/.github/ci_boards.json#L1-L46)

### Matrix Generation

Workflows use `jq` to filter configurations dynamically:

**Matrix Strategy Application**:

**Sources**: [.github/workflows/hardware-smoke.yml 29-46](https://github.com/tenstorrent/tt-system-firmware/blob/ab59b09c/.github/workflows/hardware-smoke.yml#L29-L46)[.github/workflows/hardware-smoke.yml 54-57](https://github.com/tenstorrent/tt-system-firmware/blob/ab59b09c/.github/workflows/hardware-smoke.yml#L54-L57)

### KMD Version Selection

Hardware tests dynamically load the appropriate kernel module:

**Sources**: [.github/workflows/prepare-container/action.yml 64-69](https://github.com/tenstorrent/tt-system-firmware/blob/ab59b09c/.github/workflows/prepare-container/action.yml#L64-L69)[.github/workflows/metal.yml 68-75](https://github.com/tenstorrent/tt-system-firmware/blob/ab59b09c/.github/workflows/metal.yml#L68-L75)

* * *

## Quality Assurance Workflows

### Compliance Checks

The `compliance.yml` workflow enforces Zephyr coding standards:

**Check Types**:

| Check | Description | Treatment |
| --- | --- | --- |
| `ClangFormat` | Code formatting | Warning only |
| `GitLint` | Commit message format | Error |
| `Checkpatch` | Kernel style checks | Error |
| `Kconfig` | Kconfig syntax | Error |
| `DevicetreeLint` | DTS formatting | Error |
| `Identity` | Signed-off-by required | Skipped for dependabot |

**Execution Flow**:

**Exclusions**: The workflow explicitly excludes certain checks that don't apply:

**Sources**: [.github/workflows/compliance.yml 1-157](https://github.com/tenstorrent/tt-system-firmware/blob/ab59b09c/.github/workflows/compliance.yml#L1-L157)

### Copyright Verification

The `copyright-check.yml` workflow validates file headers:

This script scans all source files for proper copyright headers with the format:

**Sources**: [.github/workflows/copyright-check.yml 1-39](https://github.com/tenstorrent/tt-system-firmware/blob/ab59b09c/.github/workflows/copyright-check.yml#L1-L39)

### License Scanning

The `license-check.yml` workflow uses `scancode` to verify licenses:

The workflow fails if `artifacts/report.txt` contains any licensing issues.

**Sources**: [.github/workflows/license-check.yml 1-57](https://github.com/tenstorrent/tt-system-firmware/blob/ab59b09c/.github/workflows/license-check.yml#L1-L57)

### Binary Blob Verification

The `verify-blob.yml` workflow ensures blob integrity:

**Verification Process**:

1.   Parse [zephyr/module.yml](https://github.com/tenstorrent/tt-system-firmware/blob/ab59b09c/zephyr/module.yml) to extract blob definitions
2.   For each blob, compute SHA256 of file in [zephyr/blobs/](https://github.com/tenstorrent/tt-system-firmware/blob/ab59b09c/zephyr/blobs/)
3.   Compare computed hash against declared hash in `module.yml`
4.   Fail if any mismatch detected

**Key Functions**:

**Sources**: [.github/workflows/verify-blob.yml 1-48](https://github.com/tenstorrent/tt-system-firmware/blob/ab59b09c/.github/workflows/verify-blob.yml#L1-L48)[scripts/verify_blob.py 34-56](https://github.com/tenstorrent/tt-system-firmware/blob/ab59b09c/scripts/verify_blob.py#L34-L56)

* * *

## Test Script Integration

### Smoke Test Execution

The `run-smoke.sh` script orchestrates Twister execution on hardware:

**Output Artifacts**:

*   `zephyr/twister-dmc-smoke/twister.json` - Test results
*   `zephyr/twister-dmc-smoke/**/device.log` - Device output
*   `zephyr/twister-dmc-smoke/**/handler.log` - Harness logs

**Sources**: [.github/workflows/hardware-smoke.yml 84-104](https://github.com/tenstorrent/tt-system-firmware/blob/ab59b09c/.github/workflows/hardware-smoke.yml#L84-L104)

### E2E Test Execution

The `run-e2e.sh` script performs firmware update testing:

This script:

1.   Builds firmware with test signature key
2.   Flashes firmware to device
3.   Validates telemetry and boot status
4.   Tests firmware update mechanisms
5.   Verifies MCUBoot recovery

**Sources**: [.github/workflows/hardware-smoke.yml 190-213](https://github.com/tenstorrent/tt-system-firmware/blob/ab59b09c/.github/workflows/hardware-smoke.yml#L190-L213)

### Stress Test Execution

The `run-stress.sh` script performs reliability testing:

Executes tests with 1000+ iterations to validate:

*   Reset mechanisms (SMI, dirty, watchdog)
*   Firmware update cycles
*   Power cycling
*   GDDR training consistency

**Sources**: [.github/workflows/hardware-long.yml 78-100](https://github.com/tenstorrent/tt-system-firmware/blob/ab59b09c/.github/workflows/hardware-long.yml#L78-L100)

* * *

## Artifact Management

### Build Artifacts

Each board build produces:

| Artifact Type | Path Pattern | Description |
| --- | --- | --- |
| Firmware Bundle | `build-*/update.fwbundle` | Complete firmware package |
| Signed Binaries | `build-**/zephyr.signed.bin` | MCUBoot signed images |
| ELF Files | `build-**/zephyr.elf` | Debug symbols |
| Map Files | `build-**/zephyr.map` | Memory layout |
| Device Trees | `build-**/zephyr.dts` | Compiled DTS |
| Configuration | `build-**/.config` | Kconfig settings |

**Upload Configuration**:

**Sources**: [.github/workflows/build-fw.yml 123-138](https://github.com/tenstorrent/tt-system-firmware/blob/ab59b09c/.github/workflows/build-fw.yml#L123-L138)

### Combined Firmware Bundle

The `build-combined-fwbundle` job aggregates all board artifacts:

Produces: `fw_pack-blackhole-<version>.fwbundle`

**Sources**: [.github/workflows/build-fw.yml 349-380](https://github.com/tenstorrent/tt-system-firmware/blob/ab59b09c/.github/workflows/build-fw.yml#L349-L380)

### Test Result Artifacts

Test workflows upload comprehensive results:

**Sources**: [.github/workflows/hardware-smoke.yml 88-127](https://github.com/tenstorrent/tt-system-firmware/blob/ab59b09c/.github/workflows/hardware-smoke.yml#L88-L127)

* * *

## Concurrency Control

All workflows implement concurrency groups to prevent resource conflicts:

**Behavior**:

*   For PRs: Only latest commit in the PR runs; previous runs are cancelled
*   For pushes: Each commit gets its own concurrent run
*   For merge queue: Separate group per merge queue entry

This prevents multiple test runs from conflicting on the same physical hardware.

**Sources**: [.github/workflows/hardware-smoke.yml 24-26](https://github.com/tenstorrent/tt-system-firmware/blob/ab59b09c/.github/workflows/hardware-smoke.yml#L24-L26)[.github/workflows/build-fw.yml 31-33](https://github.com/tenstorrent/tt-system-firmware/blob/ab59b09c/.github/workflows/build-fw.yml#L31-L33)

* * *

## Scheduled Workflows

### Daily Soak Tests

Executes stress tests and tensix column tests on all Blackhole platforms.

**Sources**: [.github/workflows/hardware-long.yml 8-9](https://github.com/tenstorrent/tt-system-firmware/blob/ab59b09c/.github/workflows/hardware-long.yml#L8-L9)

### Nightly Metal Tests

Validates firmware with TT-Metal runtime and ML workloads (Llama models).

**Sources**: [.github/workflows/metal.yml 8-9](https://github.com/tenstorrent/tt-system-firmware/blob/ab59b09c/.github/workflows/metal.yml#L8-L9)

### Branch Cleanup

Automatically deletes branches not matching:

*   `main`
*   `v*-branch` (release branches)
*   `collab*` (collaboration branches)

**Sources**: [.github/workflows/cleanup-branches.yml 4-5](https://github.com/tenstorrent/tt-system-firmware/blob/ab59b09c/.github/workflows/cleanup-branches.yml#L4-L5)[.github/workflows/cleanup-branches.yml 20-36](https://github.com/tenstorrent/tt-system-firmware/blob/ab59b09c/.github/workflows/cleanup-branches.yml#L20-L36)

* * *

## Runner Infrastructure

### Self-Hosted Runners

Physical hardware runners provide:

**Runner Labels and Capabilities**:

| Runner Label | Hardware | JTAG | IPMI | Hugepages | KMD Builds |
| --- | --- | --- | --- | --- | --- |
| `p100a-jtag` | P100A board | ✓ | ✓ | 1G | ✓ |
| `p150a-jtag` | P150A board | ✓ | ✓ | 1G | ✓ |
| `p300a-jtag` | P300A board | ✓ | ✓ | 1G | ✓ |
| `tt-galaxy-bh` | 32-chip Galaxy | ✓ | ✓ | 1G | ✓ |
| `n150-jtag` | N150 Wormhole | ✓ |  | 1G | ✓ |
| `n300-jtag` | N300 Wormhole | ✓ |  | 1G | ✓ |

### Container Configuration

Hardware test jobs mount host resources:

**Device Access**:

*   `/dev/tenstorrent` - PCIe device access via tt-kmd
*   `/dev/bus/usb` - JTAG debugger (OpenOCD)
*   `/dev/ipmi0` - Power control and sensors
*   `/dev/hugepages-1G` - DMA buffer allocation

**Sources**: [.github/workflows/hardware-smoke.yml 58-63](https://github.com/tenstorrent/tt-system-firmware/blob/ab59b09c/.github/workflows/hardware-smoke.yml#L58-L63)[.github/workflows/metal.yml 52-59](https://github.com/tenstorrent/tt-system-firmware/blob/ab59b09c/.github/workflows/metal.yml#L52-L59)

* * *

## Python Dependencies

Test infrastructure requires specific versions:

| Package | Version | Purpose |
| --- | --- | --- |
| `pyluwen` | >=0.8.1 | Hardware abstraction and ARC messages |
| `tt-flash` | ==3.6.2 | Firmware flashing |
| `tt-smi` | >=3.0.23, <4.0.0 | System management |
| `tt-tools-common` | >=1.4.31 | Common utilities |
| `tt-burnin` | >=0.3.0 | Stress testing |
| `west` | Latest | Zephyr meta-tool |
| `pytest-repeat` | Latest | Test iteration |

**Version Restrictions**:

*   `tt-smi` is restricted to `<4.0.0` to avoid long reset times due to Ethernet training waits (see tt-smi#197)

**Sources**: [scripts/requirements.txt 1-21](https://github.com/tenstorrent/tt-system-firmware/blob/ab59b09c/scripts/requirements.txt#L1-L21)

* * *

## Failure Diagnostics

When hardware tests fail, workflows automatically collect diagnostic information:

**Diagnostic Tools**:

*   `dmc_rtt.py` - Read DMC Real-Time Transfer buffer
*   `smc_console.py --rtt` - Read SMC RTT buffer
*   `dump_smc_state.py` - Dump SMC telemetry and message queue state

**Sources**: [.github/workflows/hardware-smoke.yml 129-138](https://github.com/tenstorrent/tt-system-firmware/blob/ab59b09c/.github/workflows/hardware-smoke.yml#L129-L138)[.github/workflows/hardware-long.yml 102-111](https://github.com/tenstorrent/tt-system-firmware/blob/ab59b09c/.github/workflows/hardware-long.yml#L102-L111)

* * *

## Summary

The CI/CD infrastructure provides:

*   **Comprehensive Coverage**: 11 workflows covering build, test, and quality assurance
*   **Hardware Validation**: Tests on 6 physical platforms (4 Blackhole, 2 Wormhole)
*   **Parallel Execution**: Per-board matrix builds with fail-fast disabled
*   **Quality Gates**: Compliance, copyright, license, and blob verification
*   **Artifact Traceability**: All build and test outputs uploaded with version metadata
*   **Automated Scheduling**: Daily stress tests, Metal integration, and branch cleanup
*   **Diagnostic Support**: Automatic RTT log collection on test failures

All workflows follow best practices with concurrency control, artifact retention, and clear failure reporting.

**Sources**: [.github/workflows/](https://github.com/tenstorrent/tt-system-firmware/blob/ab59b09c/.github/workflows/)

Dismiss
Refresh this wiki

Enter email to refresh

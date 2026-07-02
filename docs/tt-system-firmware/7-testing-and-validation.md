---
project: tt-system-firmware
github: https://github.com/tenstorrent/tt-system-firmware
deepwiki: https://deepwiki.com/tenstorrent/tt-system-firmware/7-testing-and-validation
---

# Testing and Validation

Relevant source files
*   [.github/Dockerfile](https://github.com/tenstorrent/tt-system-firmware/blob/ab59b09c/.github/Dockerfile)
*   [.github/ci_boards.json](https://github.com/tenstorrent/tt-system-firmware/blob/ab59b09c/.github/ci_boards.json)
*   [.github/workflows/cleanup-branches.yml](https://github.com/tenstorrent/tt-system-firmware/blob/ab59b09c/.github/workflows/cleanup-branches.yml)
*   [.github/workflows/hardware-long.yml](https://github.com/tenstorrent/tt-system-firmware/blob/ab59b09c/.github/workflows/hardware-long.yml)
*   [.github/workflows/hardware-smoke.yml](https://github.com/tenstorrent/tt-system-firmware/blob/ab59b09c/.github/workflows/hardware-smoke.yml)
*   [.github/workflows/metal.yml](https://github.com/tenstorrent/tt-system-firmware/blob/ab59b09c/.github/workflows/metal.yml)
*   [.github/workflows/prepare-container/action.yml](https://github.com/tenstorrent/tt-system-firmware/blob/ab59b09c/.github/workflows/prepare-container/action.yml)
*   [.github/workflows/prepare-zephyr/action.yml](https://github.com/tenstorrent/tt-system-firmware/blob/ab59b09c/.github/workflows/prepare-zephyr/action.yml)
*   [app/dmc/boards/qemu_riscv32.overlay](https://github.com/tenstorrent/tt-system-firmware/blob/ab59b09c/app/dmc/boards/qemu_riscv32.overlay)
*   [app/smc/pytest/e2e_smoke.py](https://github.com/tenstorrent/tt-system-firmware/blob/ab59b09c/app/smc/pytest/e2e_smoke.py)
*   [app/smc/pytest/e2e_stress.py](https://github.com/tenstorrent/tt-system-firmware/blob/ab59b09c/app/smc/pytest/e2e_stress.py)
*   [app/smc/pytest/vuart.py](https://github.com/tenstorrent/tt-system-firmware/blob/ab59b09c/app/smc/pytest/vuart.py)
*   [app/smc/sample.yaml](https://github.com/tenstorrent/tt-system-firmware/blob/ab59b09c/app/smc/sample.yaml)
*   [boards/tenstorrent/tt_blackhole/tt_blackhole_tt_blackhole_smc_galaxy.yaml](https://github.com/tenstorrent/tt-system-firmware/blob/ab59b09c/boards/tenstorrent/tt_blackhole/tt_blackhole_tt_blackhole_smc_galaxy.yaml)
*   [scripts/requirements.txt](https://github.com/tenstorrent/tt-system-firmware/blob/ab59b09c/scripts/requirements.txt)
*   [scripts/tooling/attrs.x](https://github.com/tenstorrent/tt-system-firmware/blob/ab59b09c/scripts/tooling/attrs.x)
*   [tests/lib/tenstorrent/bh_arc/testcase.yaml](https://github.com/tenstorrent/tt-system-firmware/blob/ab59b09c/tests/lib/tenstorrent/bh_arc/testcase.yaml)

## Overview

This document describes the comprehensive testing and validation strategy for tt-system-firmware. The testing infrastructure ensures firmware reliability through multiple validation layers, from unit tests running on simulated platforms to stress tests executing thousands of iterations on physical hardware. The system validates the complete firmware stack (SMC, DMC, and CMFW) along with their interactions with hardware subsystems.

For information about the build process that produces artifacts consumed by tests, see [Build System](https://deepwiki.com/tenstorrent/tt-system-firmware/6-build-system). For details on CI/CD workflows that orchestrate test execution, see [CI/CD Infrastructure](https://deepwiki.com/tenstorrent/tt-system-firmware/8-cicd-infrastructure).

**Testing Layers:**

*   **Unit Tests**: Fast feedback on code changes using Zephyr Twister with `native_sim` platform
*   **Smoke Tests**: Basic functionality validation on physical hardware across multiple board types
*   **End-to-End Tests**: Complete firmware stack validation using pyluwen for host-to-hardware interaction
*   **Stress Tests**: Reliability validation through 1000+ iteration cycles of critical operations
*   **Metal Tests**: Integration validation with TT-Metal ML runtime using actual workloads

Sources: [.github/workflows/hardware-smoke.yml 1-273](https://github.com/tenstorrent/tt-system-firmware/blob/ab59b09c/.github/workflows/hardware-smoke.yml#L1-L273)[.github/workflows/hardware-long.yml 1-166](https://github.com/tenstorrent/tt-system-firmware/blob/ab59b09c/.github/workflows/hardware-long.yml#L1-L166)[.github/workflows/metal.yml 1-135](https://github.com/tenstorrent/tt-system-firmware/blob/ab59b09c/.github/workflows/metal.yml#L1-L135)

* * *

## Test Type Hierarchy

The testing pyramid follows a multi-tier strategy where each layer provides different coverage and execution characteristics. Tests are organized by scope and execution environment.

**Diagram: Test execution hierarchy showing test types, triggers, and artifacts**

Sources: [.github/workflows/hardware-smoke.yml 3-22](https://github.com/tenstorrent/tt-system-firmware/blob/ab59b09c/.github/workflows/hardware-smoke.yml#L3-L22)[.github/workflows/hardware-long.yml 3-14](https://github.com/tenstorrent/tt-system-firmware/blob/ab59b09c/.github/workflows/hardware-long.yml#L3-L14)[.github/workflows/metal.yml 3-14](https://github.com/tenstorrent/tt-system-firmware/blob/ab59b09c/.github/workflows/metal.yml#L3-L14)

* * *

## Test Infrastructure Components

The test infrastructure relies on specialized Docker containers, physical hardware runners with JTAG access, and Python libraries for hardware interaction. Configuration is centralized in `ci_boards.json`.

**Diagram: Test infrastructure showing containers, Python libraries, and hardware access paths**

The container environment includes device mounts for hardware access:

*   `--device /dev/tenstorrent`: PCIe device access for firmware operations
*   `--device /dev/bus/usb`: JTAG access for direct chip programming
*   `--device /dev/ipmi0`: IPMI access for board management

Volume mounts provide shared resources:

*   `/dev/hugepages-1G`: Large page support for performance
*   `/opt/tenstorrent/kmd-builds/`: Kernel module builds for different versions

Sources: [.github/workflows/hardware-smoke.yml 58-63](https://github.com/tenstorrent/tt-system-firmware/blob/ab59b09c/.github/workflows/hardware-smoke.yml#L58-L63)[scripts/requirements.txt 1-21](https://github.com/tenstorrent/tt-system-firmware/blob/ab59b09c/scripts/requirements.txt#L1-L21)[.github/workflows/prepare-container/action.yml 64-74](https://github.com/tenstorrent/tt-system-firmware/blob/ab59b09c/.github/workflows/prepare-container/action.yml#L64-L74)[.github/Dockerfile 1-50](https://github.com/tenstorrent/tt-system-firmware/blob/ab59b09c/.github/Dockerfile#L1-L50)

* * *

## Unit Testing with Twister

Unit tests execute on the `native_sim` platform using Zephyr's Twister framework. Tests validate individual firmware components without requiring physical hardware.

**Test Configuration Structure:**

| Configuration File | Purpose | Platform |
| --- | --- | --- |
| `tests/lib/tenstorrent/bh_arc/testcase.yaml` | BH ARC library tests | `native_sim` |
| `app/smc/sample.yaml` | SMC application tests | Hardware platforms |
| `app/dmc/boards/qemu_riscv32.overlay` | DMC emulation overlay | QEMU RISC-V |

**Diagram: Twister test execution flow from configuration to output artifacts**

The `native_sim` platform allows rapid iteration during development since tests execute as native Linux binaries without hardware dependencies. The Twister framework automatically discovers tests based on YAML configuration files.

Sources: [tests/lib/tenstorrent/bh_arc/testcase.yaml 1-6](https://github.com/tenstorrent/tt-system-firmware/blob/ab59b09c/tests/lib/tenstorrent/bh_arc/testcase.yaml#L1-L6)[app/smc/sample.yaml 1-93](https://github.com/tenstorrent/tt-system-firmware/blob/ab59b09c/app/smc/sample.yaml#L1-L93)[app/dmc/boards/qemu_riscv32.overlay 1-62](https://github.com/tenstorrent/tt-system-firmware/blob/ab59b09c/app/dmc/boards/qemu_riscv32.overlay#L1-L62)

* * *

## Hardware Smoke Tests

Hardware smoke tests validate basic firmware functionality on physical devices. Tests execute on multiple board configurations using the Zephyr Twister framework with hardware harnesses.

**Test Execution Flow:**

**Diagram: Hardware smoke test workflow showing parallel DMC, SMC, and E2E test execution**

**Smoke Test Scripts:**

The smoke tests are orchestrated by shell scripts that invoke Twister with specific configurations:

*   `scripts/ci/run-smoke.sh`: Executes DMC or SMC smoke tests using Twister
*   `scripts/ci/run-e2e.sh`: Executes end-to-end tests with firmware signing
*   `scripts/dmc_rtt.py`: Extracts RTT (Real-Time Transfer) logs from DMC firmware
*   `scripts/smc_console.py`: Extracts RTT logs from SMC firmware
*   `scripts/dump_smc_state.py`: Dumps SMC state for debugging

**Board Exclusions:**

Galaxy boards are excluded from smoke tests because they lack a recovery solution:

```
# Exclude galaxy board from smoke tests since we do not have a recovery solution for it
MATRIX_CONFIG_BH="$(jq -c '[.[] | select((.product == "bh") and (.board != "galaxy")) | del(.product)]' .github/ci_boards.json)"
```

Sources: [.github/workflows/hardware-smoke.yml 29-128](https://github.com/tenstorrent/tt-system-firmware/blob/ab59b09c/.github/workflows/hardware-smoke.yml#L29-L128)[.github/workflows/hardware-smoke.yml 140-224](https://github.com/tenstorrent/tt-system-firmware/blob/ab59b09c/.github/workflows/hardware-smoke.yml#L140-L224)

* * *

## End-to-End Testing with pyluwen

End-to-end tests validate the complete firmware stack using pyluwen to interact with hardware. Tests cover firmware updates, ARC message handling, reset mechanisms, and hardware subsystem validation.

**Core Test Functions and Memory Addresses:**

**Diagram: E2E test structure showing fixtures, memory addresses, ARC messages, and test categories**

**Key Test Functions:**

| Test Function | Purpose | ARC Message |
| --- | --- | --- |
| `test_arc_msg()` | Validate ARC message handling | `TT_SMC_MSG_TEST (0x90)` |
| `test_dmc_msg()` | Validate DMC ping from SMC | `TT_SMC_MSG_PING_DM (0xC0)` |
| `test_boot_status()` | Check hardware boot status | N/A (AXI read) |
| `test_arc_watchdog()` | Verify watchdog reset | `TT_SMC_MSG_SET_WDT (0xC1)` |
| `test_temperature_sensors()` | Read temperature sensors | `TT_SMC_MSG_READ_TS (0x1B)` |
| `test_voltage_monitors()` | Read voltage monitors | `TT_SMC_MSG_READ_VM (0x1D)` |
| `test_flash_write()` | Validate SPI flash R/W | N/A (SPI operations) |
| `test_upgrade_from_19_00()` | Test firmware migration | N/A (tt-flash) |

**Boot Validation Example:**

The `wait_arc_boot()` function validates the ARC firmware boot sequence:

`# Check ARC status magic valuestatus = chip.axi_read32(ARC_STATUS)assert (status & 0xFFFF0000) == 0xC0DE0000, "SMC firmware postcode is invalid"# Check boot completion (postcode >= 0x1D)assert (status & 0xFFFF) >= 0x1D, "SMC firmware boot failed"`
**Chip Count Validation:**

Tests verify the expected number of enumerated chips based on board type:

*   Galaxy: 32 chips expected
*   P300A: 2 chips expected
*   Other boards: 1 chip expected

Sources: [app/smc/pytest/e2e_smoke.py 76-105](https://github.com/tenstorrent/tt-system-firmware/blob/ab59b09c/app/smc/pytest/e2e_smoke.py#L76-L105)[app/smc/pytest/e2e_smoke.py 426-476](https://github.com/tenstorrent/tt-system-firmware/blob/ab59b09c/app/smc/pytest/e2e_smoke.py#L426-L476)[app/smc/pytest/e2e_smoke.py 200-209](https://github.com/tenstorrent/tt-system-firmware/blob/ab59b09c/app/smc/pytest/e2e_smoke.py#L200-L209)

* * *

## Stress and Long-Duration Tests

Stress tests execute critical operations thousands of times to validate firmware reliability under sustained load. Tests run on a scheduled basis (daily at 05:21 UTC) or when manually triggered via PR labels.

**Stress Test Configuration:**

**Diagram: Stress test categories with iteration counts and thresholds**

**DMC Ping Latency Tracking:**

The DMC ping stress test tracks response latency statistics:

`# From e2e_stress.pydmfw_ping_avg = 0dmfw_ping_max = 0for i in range(total_tries):    response = arc_chip.arc_msg(TT_SMC_MSG_PING_DM, True, False, 0, 0, 1000)    duration = arc_chip.axi_read32(PING_DMFW_DURATION_REG_ADDR)  # 0x80030448    dmfw_ping_avg += duration / total_tries    dmfw_ping_max = max(dmfw_ping_max, duration)`
**Power Virus Test:**

The power virus test validates thermal management under sustained ML workload:

1.   Sample temperature sensors 20 times before starting workload
2.   Launch `tt-burnin --no-reset` subprocess
3.   Continuously read temperature sensors every 1 second for 180 seconds
4.   Verify `tt-burnin` process remains alive throughout duration
5.   Gracefully terminate workload by sending enter key

**Tensix Column Test:**

A specialized long-duration test validates Tensix column re-enabling:

`# From hardware-long.ymltt-update-tensix-disable-count --input *.fwbundle --output patched.fwbundlett-flash patched.fwbundle --forceTENSIX_COUNT=$(python3 -c "import pyluwen; print(pyluwen.detect_chips()[0].get_telemetry().tensix_enabled_col.bit_count() * 10)")# Expected: 140 (14 columns × 10 rows)`
Sources: [app/smc/pytest/e2e_stress.py 47-253](https://github.com/tenstorrent/tt-system-firmware/blob/ab59b09c/app/smc/pytest/e2e_stress.py#L47-L253)[app/smc/pytest/e2e_stress.py 344-437](https://github.com/tenstorrent/tt-system-firmware/blob/ab59b09c/app/smc/pytest/e2e_stress.py#L344-L437)[.github/workflows/hardware-long.yml 113-166](https://github.com/tenstorrent/tt-system-firmware/blob/ab59b09c/.github/workflows/hardware-long.yml#L113-L166)

* * *

## TT-Metal Integration Tests

Metal tests validate firmware integration with the TT-Metal ML runtime by executing actual workloads on physical hardware. Tests run nightly or when triggered via the `metal-tests` PR label.

**Metal Test Workflow:**

**Diagram: Metal test workflow from container selection to test execution**

**Test Environment Setup:**

Metal tests require specific environment variables and volume mounts:

| Volume Mount | Purpose |
| --- | --- |
| `/dev/hugepages-1G` | 1GB hugepage support for DMA |
| `/dev/hugepages` | Standard hugepage support |
| `/opt/tenstorrent/hf-models/` | Pre-downloaded Hugging Face models |
| `/opt/tenstorrent/kmd-builds/` | Kernel module builds |

**Test Patches Applied:**

The workflow applies runtime patches to accommodate CI environment differences:

1.   **Disable Performance Checks**: Whisper demo tests skip performance validation on CI runners

`sed -i 's/pytest\(.*\)test_demo_for_conditional_generation/CI=false pytest\1test_demo_for_conditional_generation/g'`
2.   **Add Determinism Warmup**: ResNet tests run once without determinism checks to work around cold-boot output variations (SYS-2705)

`pytest tests/didt/test_resnet_conv.py::test_resnet_conv -k "all" --didt-workload-iterations 100 --determinism-check-interval 0`

**Metal Test Scripts:**

The actual test execution is delegated to Metal's container entrypoint:

*   `dockerfile/upstream_test_images/run_upstream_tests_vanilla.sh`: Runs upstream validation tests
*   Metal target is passed as argument: `blackhole`, `blackhole_p300`, or `blackhole_glx`

Sources: [.github/workflows/metal.yml 45-135](https://github.com/tenstorrent/tt-system-firmware/blob/ab59b09c/.github/workflows/metal.yml#L45-L135)[.github/ci_boards.json 1-46](https://github.com/tenstorrent/tt-system-firmware/blob/ab59b09c/.github/ci_boards.json#L1-L46)

* * *

## Board Configuration Matrix

The `ci_boards.json` file defines the test matrix for all supported hardware platforms. Each board entry specifies runner labels, kernel module versions, and Metal container images.

**Board Configuration Schema:**

**Diagram: Board configuration structure showing fields and board types**

**Example Board Configuration (P300A):**

`{  "board": "p300a",  "runs-on": "p300a-jtag",  "product": "bh",  "metal-image": "ghcr.io/tenstorrent/tt-metal/upstream-tests-bh-p300:v0.66.0-dev20251218-4-g255c9332f1",  "metal-target": "blackhole_p300",  "kmd_build": "2.5.0"}`
**Board-Specific Test Behavior:**

Certain tests adapt behavior based on the board type:

| Board Type | Test Adaptations |
| --- | --- |
| `galaxy` | DMC tests skipped (no DMC), SMBUS tests skipped, recovery tests skipped |
| `p300a` | 2-chip enumeration expected, P300-specific Metal container |
| `n150`, `n300` | Wormhole-specific tests, requires `wh-tests` label or schedule trigger |

**Configuration Filtering:**

Workflows use `jq` to filter boards by product:

`# Filter Blackhole boards (excluding galaxy for smoke tests)MATRIX_CONFIG_BH="$(jq -c '[.[] | select((.product == "bh") and (.board != "galaxy")) | del(.product)]' .github/ci_boards.json)" # Filter Wormhole boardsMATRIX_CONFIG_WH="$(jq -c '[.[] | select(.product == "wh") | del(.product)]' .github/ci_boards.json)"`
Sources: [.github/ci_boards.json 1-46](https://github.com/tenstorrent/tt-system-firmware/blob/ab59b09c/.github/ci_boards.json#L1-L46)[.github/workflows/hardware-smoke.yml 38-46](https://github.com/tenstorrent/tt-system-firmware/blob/ab59b09c/.github/workflows/hardware-smoke.yml#L38-L46)

* * *

## Test Execution and Artifact Management

Test execution produces structured artifacts for debugging and result tracking. Artifacts are uploaded to GitHub Actions for each test run, preserving build outputs, logs, and test results.

**Artifact Categories:**

**Diagram: Test artifact structure showing outputs and upload destinations**

**Artifact Upload Configuration:**

Each test job uploads artifacts with conditional logic:

`# Upload artifacts always, even on failure- name: Upload SMC Smoke Tests  if: ${{ always() }}  uses: actions/upload-artifact@v4  with:    name: SMC Smoke test results (${{ matrix.config.board }})    include-hidden-files: true    path: |      zephyr/twister-smc-smoke/**/handler.log      zephyr/twister-smc-smoke/**/device.log      zephyr/twister-smc-smoke/twister.json`
**RTT Log Extraction on Failure:**

When tests fail, RTT (Real-Time Transfer) logs are extracted for debugging:

`# Print RTT logs on failureecho "DMC RTT logs:"python3 ./scripts/dmc_rtt.py -n echo "SMC RTT logs:"python3 ./scripts/smc_console.py --rtt -n echo "SMC state dump:"python3 ./scripts/dump_smc_state.py --asic-id $ASIC_ID`
RTT provides a non-intrusive mechanism to retrieve firmware debug logs without requiring a UART connection.

**Stress Test Result Recording:**

Stress tests use regex patterns to extract summary statistics:

`harness_config:  record:    regex:      - >-        INFO: (?P<test_name>.*) completed.        Failed (?P<fail_count>\d+)/(?P<total_tries>\d+) times.`
This allows Twister to parse structured test results from log output and generate JUnit XML reports.

Sources: [.github/workflows/hardware-smoke.yml 88-127](https://github.com/tenstorrent/tt-system-firmware/blob/ab59b09c/.github/workflows/hardware-smoke.yml#L88-L127)[.github/workflows/hardware-smoke.yml 129-139](https://github.com/tenstorrent/tt-system-firmware/blob/ab59b09c/.github/workflows/hardware-smoke.yml#L129-L139)[app/smc/sample.yaml 75-79](https://github.com/tenstorrent/tt-system-firmware/blob/ab59b09c/app/smc/sample.yaml#L75-L79)

* * *

## Test Harness Integration

The test infrastructure uses Zephyr Twister's pytest harness to execute Python-based hardware tests. The harness provides fixtures for device flashing, boot validation, and test parameterization.

**Pytest Harness Configuration:**

**Diagram: Pytest harness integration showing configuration, fixtures, and test functions**

**Fixture Scopes:**

| Fixture | Scope | Purpose | Invocations |
| --- | --- | --- | --- |
| `unlaunched_dut` | `session` | Provides firmware bundle path | Once per test session |
| `launched_arc_dut` | `session` | Flashes firmware once | Once per test session |
| `arc_chip_dut` | `function` | Validates boot before each test | Before each test function |

**Session-Scoped Flashing:**

The `launched_arc_dut` fixture flashes firmware only once per test session, allowing multiple tests to execute against the same firmware image without redundant flashing:

`@pytest.fixture(scope="session")def launched_arc_dut(unlaunched_dut: DeviceAdapter):    logger.info("Flashing ARC DUT")    try:        unlaunched_dut.launch()    except TwisterHarnessException:        pytest.exit("Failed to flash DUT")    time.sleep(1)  # Wait for ARC to start    return unlaunched_dut`
**Test Parameterization:**

Tests use pytest parameterization for board-specific behavior:

`@pytest.mark.skipif(os.getenv("BOARD") == "galaxy", reason="Galaxy lacks a DMC")def test_arc_watchdog(arc_chip_dut, asic_id):    # Test implementation`
**Virtual UART Testing:**

VUART tests validate console output:

`def test_boot_banner(arc_chip_dut):    output = arc_chip_dut.readlines_until(        "Booting tt_blackhole with Zephyr OS", timeout=3000    )    assert "Booting tt_blackhole with Zephyr OS" in "\n".join(output)`
Sources: [app/smc/sample.yaml 11-74](https://github.com/tenstorrent/tt-system-firmware/blob/ab59b09c/app/smc/sample.yaml#L11-L74)[app/smc/pytest/e2e_smoke.py 128-198](https://github.com/tenstorrent/tt-system-firmware/blob/ab59b09c/app/smc/pytest/e2e_smoke.py#L128-L198)[app/smc/pytest/vuart.py 1-21](https://github.com/tenstorrent/tt-system-firmware/blob/ab59b09c/app/smc/pytest/vuart.py#L1-L21)

Dismiss
Refresh this wiki

Enter email to refresh
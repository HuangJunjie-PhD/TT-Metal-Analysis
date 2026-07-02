---
project: tt-system-firmware
github: https://github.com/tenstorrent/tt-system-firmware
deepwiki: https://deepwiki.com/tenstorrent/tt-system-firmware/6-build-system
---

# Build System

Relevant source files
*   [.github/workflows/build-fw.yml](https://github.com/tenstorrent/tt-system-firmware/blob/ab59b09c/.github/workflows/build-fw.yml)
*   [.github/workflows/build-native.yml](https://github.com/tenstorrent/tt-system-firmware/blob/ab59b09c/.github/workflows/build-native.yml)
*   [.github/workflows/compliance.yml](https://github.com/tenstorrent/tt-system-firmware/blob/ab59b09c/.github/workflows/compliance.yml)
*   [.github/workflows/copyright-check.yml](https://github.com/tenstorrent/tt-system-firmware/blob/ab59b09c/.github/workflows/copyright-check.yml)
*   [.github/workflows/license-check.yml](https://github.com/tenstorrent/tt-system-firmware/blob/ab59b09c/.github/workflows/license-check.yml)
*   [.github/workflows/run-unit-tests.yml](https://github.com/tenstorrent/tt-system-firmware/blob/ab59b09c/.github/workflows/run-unit-tests.yml)
*   [.github/workflows/verify-blob.yml](https://github.com/tenstorrent/tt-system-firmware/blob/ab59b09c/.github/workflows/verify-blob.yml)
*   [scripts/requirements-actions.in](https://github.com/tenstorrent/tt-system-firmware/blob/ab59b09c/scripts/requirements-actions.in)
*   [scripts/requirements-actions.txt](https://github.com/tenstorrent/tt-system-firmware/blob/ab59b09c/scripts/requirements-actions.txt)
*   [scripts/verify_blob.py](https://github.com/tenstorrent/tt-system-firmware/blob/ab59b09c/scripts/verify_blob.py)
*   [test-conf/tests/drivers/pwm/pwm_api/boards/tt_blackhole_tt_blackhole_dmc.conf](https://github.com/tenstorrent/tt-system-firmware/blob/ab59b09c/test-conf/tests/drivers/pwm/pwm_api/boards/tt_blackhole_tt_blackhole_dmc.conf)
*   [test-conf/tests/drivers/pwm/pwm_api/boards/tt_blackhole_tt_blackhole_dmc.overlay](https://github.com/tenstorrent/tt-system-firmware/blob/ab59b09c/test-conf/tests/drivers/pwm/pwm_api/boards/tt_blackhole_tt_blackhole_dmc.overlay)
*   [test-conf/tests/drivers/pwm/pwm_api/testcase.yaml](https://github.com/tenstorrent/tt-system-firmware/blob/ab59b09c/test-conf/tests/drivers/pwm/pwm_api/testcase.yaml)
*   [west.yml](https://github.com/tenstorrent/tt-system-firmware/blob/ab59b09c/west.yml)
*   [zephyr/blobs/fw_pack-grayskull.tar.gz](https://github.com/tenstorrent/tt-system-firmware/blob/ab59b09c/zephyr/blobs/fw_pack-grayskull.tar.gz)
*   [zephyr/blobs/fw_pack-wormhole.tar.gz](https://github.com/tenstorrent/tt-system-firmware/blob/ab59b09c/zephyr/blobs/fw_pack-wormhole.tar.gz)
*   [zephyr/blobs/tt_blackhole_erisc.bin](https://github.com/tenstorrent/tt-system-firmware/blob/ab59b09c/zephyr/blobs/tt_blackhole_erisc.bin)
*   [zephyr/blobs/tt_blackhole_erisc_params.bin](https://github.com/tenstorrent/tt-system-firmware/blob/ab59b09c/zephyr/blobs/tt_blackhole_erisc_params.bin)
*   [zephyr/blobs/tt_blackhole_gddr_params_GALAXY_REVC.bin](https://github.com/tenstorrent/tt-system-firmware/blob/ab59b09c/zephyr/blobs/tt_blackhole_gddr_params_GALAXY_REVC.bin)
*   [zephyr/blobs/tt_blackhole_libpciesd.a](https://github.com/tenstorrent/tt-system-firmware/blob/ab59b09c/zephyr/blobs/tt_blackhole_libpciesd.a)
*   [zephyr/blobs/tt_blackhole_serdes_eth_fw.bin](https://github.com/tenstorrent/tt-system-firmware/blob/ab59b09c/zephyr/blobs/tt_blackhole_serdes_eth_fw.bin)
*   [zephyr/blobs/tt_blackhole_serdes_eth_fwreg.bin](https://github.com/tenstorrent/tt-system-firmware/blob/ab59b09c/zephyr/blobs/tt_blackhole_serdes_eth_fwreg.bin)
*   [zephyr/module.yml](https://github.com/tenstorrent/tt-system-firmware/blob/ab59b09c/zephyr/module.yml)

The tt-system-firmware build system is based on Zephyr RTOS and uses the `west` meta-tool for project management and CMake for compilation. The build system supports multiple board configurations, produces signed firmware images through MCUBoot integration, and packages all firmware components into combined bundles for deployment. This page provides an overview of the build architecture, tooling, and processes. For detailed information about specific aspects, see [Zephyr Integration](https://deepwiki.com/tenstorrent/tt-system-firmware/6.1-zephyr-integration), [Build Pipeline and Artifacts](https://deepwiki.com/tenstorrent/tt-system-firmware/6.2-build-pipeline-and-artifacts), [Firmware Signing and MCUBoot](https://deepwiki.com/tenstorrent/tt-system-firmware/6.3-firmware-signing-and-mcuboot), and [Combined Firmware Bundles](https://deepwiki.com/tenstorrent/tt-system-firmware/6.4-combined-firmware-bundles).

* * *

## Build System Architecture

The build system follows a hierarchical structure where the tt-system-firmware repository integrates as a Zephyr module, pulling in Zephyr and its dependencies through the west manifest system.

**Sources:**[west.yml 1-33](https://github.com/tenstorrent/tt-system-firmware/blob/ab59b09c/west.yml#L1-L33)[zephyr/module.yml 1-235](https://github.com/tenstorrent/tt-system-firmware/blob/ab59b09c/zephyr/module.yml#L1-L235)

* * *

## West Workspace Initialization

The build system uses `west` as the meta-tool to manage the multi-repository workspace. The manifest defines two remotes and imports specific Zephyr dependencies.

### West Manifest Structure

| Component | Remote | Path | Purpose |
| --- | --- | --- | --- |
| zephyr | tenstorrent/zephyr-fork | . | Custom Zephyr fork with board support |
| librpmi | tenstorrent-forks/librpmi | modules/lib/librpmi | RISC-V Power Management Interface |
| cmsis_6 | (via Zephyr import) | modules/hal/cmsis | Cortex Microcontroller Software Interface |
| hal_stm32 | (via Zephyr import) | modules/hal/stm32 | STM32 Hardware Abstraction Layer |
| mbedtls | (via Zephyr import) | modules/crypto/mbedtls | Cryptography library |
| mcuboot | (via Zephyr import) | bootloader/mcuboot | Secure bootloader |
| nanopb | (via Zephyr import) | modules/lib/nanopb | Protocol buffers for embedded |
| segger | (via Zephyr import) | modules/debug/segger | RTT console support |

The workspace is initialized with:

**Sources:**[west.yml 4-33](https://github.com/tenstorrent/tt-system-firmware/blob/ab59b09c/west.yml#L4-L33)

* * *

## Zephyr Module Configuration

The `zephyr/module.yml` file declares tt-system-firmware as a Zephyr module, specifying custom boards, binary blobs, and build-time runners.

**Sources:**[zephyr/module.yml 1-235](https://github.com/tenstorrent/tt-system-firmware/blob/ab59b09c/zephyr/module.yml#L1-L235)

### Binary Blob Declarations

The module declares 20 binary firmware blobs with SHA256 checksums for integrity verification:

| Blob Type | Count | Purpose |
| --- | --- | --- |
| MRISC Firmware | 1 base + 13 parameter files | GDDR memory controller initialization |
| ERISC Firmware | 1 base + 1 parameter file | Ethernet controller firmware |
| Ethernet SerDes | 2 files (firmware + registers) | Ethernet PHY configuration |
| PCIe SerDes | 1 static library | PCIe SerDes initialization |
| Bootrom | 1 file | Modified boot code for Blackhole |
| Legacy Packs | 2 tar.gz files | Wormhole and Grayskull firmware |

Each blob entry specifies:

*   `path`: Relative path to the blob file
*   `sha256`: Integrity checksum
*   `type`: `img` (image) or `lib` (library)
*   `version`: Blob version string
*   `license-path`: License file location
*   `url`: Download URL for missing blobs
*   `description`: Human-readable description

**Sources:**[zephyr/module.yml 36-235](https://github.com/tenstorrent/tt-system-firmware/blob/ab59b09c/zephyr/module.yml#L36-L235)[scripts/verify_blob.py 1-55](https://github.com/tenstorrent/tt-system-firmware/blob/ab59b09c/scripts/verify_blob.py#L1-L55)

* * *

## Build Process Flow

The build process orchestrates multiple applications and combines them into firmware bundles through the sysbuild mechanism.

**Sources:**[.github/workflows/build-fw.yml 86-121](https://github.com/tenstorrent/tt-system-firmware/blob/ab59b09c/.github/workflows/build-fw.yml#L86-L121)

### Build Command Structure

The standard build command for a board configuration is:

Where:

*   `-d build-BOARD_REV`: Build directory for specific board revision
*   `--sysbuild`: Enable sysbuild for multi-image builds
*   `-p`: Pristine build (clean before building)
*   `-b BOARD`: Target board identifier
*   `app/smc`: Primary application to build (triggers DMC and recovery builds)
*   `--`: Separator for CMake arguments
*   `-DCONFIG_COMPILER_WARNINGS_AS_ERRORS=y`: Treat warnings as errors
*   `-DSB_CONFIG_BOOT_SIGNATURE_KEY_FILE`: Path to signing key

**Sources:**[.github/workflows/build-fw.yml 100-106](https://github.com/tenstorrent/tt-system-firmware/blob/ab59b09c/.github/workflows/build-fw.yml#L100-L106)

* * *

## GitHub Actions Build Matrix

The CI/CD system builds firmware for multiple board configurations in parallel using a matrix strategy.

**Sources:**[.github/workflows/build-fw.yml 1-523](https://github.com/tenstorrent/tt-system-firmware/blob/ab59b09c/.github/workflows/build-fw.yml#L1-L523)

### Build Workflow Jobs

| Job Name | Purpose | Outputs |
| --- | --- | --- |
| `genboards` | Parse `.github/boards.json` and generate matrix | Board list JSON array |
| `build` | Build firmware for each board in matrix | Per-board `.fwbundle` and artifacts |
| `build-recovery` | Build standalone recovery firmware | `recovery.signed.bin` |
| `build-assembly-test` | Build assembly test firmware for P150/P300 | Assembly test images |
| `build-preflash` | Build preflash firmware | `preflash*.ihex` |
| `build-combined-fwbundle` | Combine all board bundles | `fw_pack-*.fwbundle` |
| `build-bh-flm` | Build Blackhole Flash Loader Module | FLM binaries |
| `smc-build-only-tests` | Build SMC test suite (no execution) | Test build artifacts |
| `grendel-build-test` | Build tests for Grendel boards | Grendel test artifacts |

**Sources:**[.github/workflows/build-fw.yml 35-523](https://github.com/tenstorrent/tt-system-firmware/blob/ab59b09c/.github/workflows/build-fw.yml#L35-L523)

* * *

## Build Environment Setup

The GitHub Actions workflows use a composite action to prepare the Zephyr build environment consistently across all jobs.

### prepare-zephyr Action

The `.github/workflows/prepare-zephyr` composite action:

1.   Sets up Python 3.12 with pip caching
2.   Installs Python dependencies from `scripts/requirements.txt`
3.   Initializes the west workspace: `west init -l tt-system-firmware`
4.   Updates dependencies: `west update -o=--depth=1 -n`
5.   Fetches repository tags: `west forall -c 'git fetch --unshallow || true'`

This action is used by all build workflows to ensure consistent tooling versions.

**Sources:**[.github/workflows/build-fw.yml 57-68](https://github.com/tenstorrent/tt-system-firmware/blob/ab59b09c/.github/workflows/build-fw.yml#L57-L68)

* * *

## Build Artifacts Organization

Each build job produces a structured set of artifacts organized by build directory and application.

### Artifact Structure

```
build-BOARD_REV/
├── mcuboot/
│   └── zephyr/
│       ├── .config
│       ├── devicetree_generated.h
│       ├── zephyr.bin
│       ├── zephyr.dts
│       ├── zephyr.elf
│       ├── zephyr.hex
│       ├── zephyr.map
│       └── zephyr.stat
├── dmc/
│   └── zephyr/
│       ├── .config
│       ├── devicetree_generated.h
│       ├── zephyr.bin
│       ├── zephyr.dts
│       ├── zephyr.elf
│       ├── zephyr.hex
│       ├── zephyr.map
│       ├── zephyr.signed.bin
│       ├── zephyr.signed.hex
│       └── zephyr.stat
├── smc/
│   └── zephyr/
│       └── (same as dmc/)
├── recovery/
│   └── zephyr/
│       └── (same as dmc/)
└── update.fwbundle
```

**Sources:**[.github/workflows/build-fw.yml 123-138](https://github.com/tenstorrent/tt-system-firmware/blob/ab59b09c/.github/workflows/build-fw.yml#L123-L138)

### Artifact Upload

GitHub Actions uploads the following artifacts for each build:

**Sources:**[.github/workflows/build-fw.yml 123-138](https://github.com/tenstorrent/tt-system-firmware/blob/ab59b09c/.github/workflows/build-fw.yml#L123-L138)

* * *

## Firmware Signature Verification

All firmware images are signed using MCUBoot and verified before deployment to ensure integrity and authenticity.

### Signing Process

**Sources:**[.github/workflows/build-fw.yml 77-121](https://github.com/tenstorrent/tt-system-firmware/blob/ab59b09c/.github/workflows/build-fw.yml#L77-L121)

### Verification Command

After each build, signed images are verified:

The `imgtool` command returns a non-zero exit code if verification fails, causing the build to fail.

**Sources:**[.github/workflows/build-fw.yml 108-121](https://github.com/tenstorrent/tt-system-firmware/blob/ab59b09c/.github/workflows/build-fw.yml#L108-L121)

* * *

## Board-to-Target Mapping

The build system uses a `rev2board.sh` script to map board revision identifiers to Zephyr board targets.

### Board Mapping Examples

| Input Revision | Zephyr Board Target | Notes |
| --- | --- | --- |
| `p100a` | `tt_blackhole@p100a/tt_blackhole/smc` | P100A SMC build |
| `p150a` | `tt_blackhole@p150a/tt_blackhole/smc` | P150A SMC build |
| `p300a` | `tt_blackhole@p300a/tt_blackhole/smc` | P300A SMC build |
| `galaxy` | `tt_blackhole@galaxy/tt_blackhole/smc` | Galaxy SMC build (no DMC) |

The script is invoked in build workflows:

**Sources:**[.github/workflows/build-fw.yml 70-75](https://github.com/tenstorrent/tt-system-firmware/blob/ab59b09c/.github/workflows/build-fw.yml#L70-L75)

* * *

## Combined Firmware Bundle Creation

After all individual board builds complete, the `build-combined-fwbundle` job packages all firmware images into a single deployable bundle.

### Bundle Creation Process

**Sources:**[.github/workflows/build-fw.yml 349-380](https://github.com/tenstorrent/tt-system-firmware/blob/ab59b09c/.github/workflows/build-fw.yml#L349-L380)

### Bundle Creation Steps

1.   **Download Artifacts**: Download all `firmware-*` artifacts from parallel build jobs
2.   **Install Dependencies**: Install `pykwalify`, `intelhex`, and `imgtool` Python packages
3.   **Set Environment Variable**: `BUNDLE_TEMP_PREFIX="$PWD/tt-system-firmware/firmware-"`
4.   **Execute Script**: Run `scripts/build-combined-fwbundle.sh`
5.   **Upload Bundle**: Upload `fw_pack*.fwbundle` as `combined-fwbundle` artifact

The combined bundle contains all board-specific firmware images and is used by downstream flash and deployment tools.

**Sources:**[.github/workflows/build-fw.yml 365-380](https://github.com/tenstorrent/tt-system-firmware/blob/ab59b09c/.github/workflows/build-fw.yml#L365-L380)

* * *

## Unit Testing Integration

The build system includes unit test execution using Zephyr's Twister framework.

### Unit Test Workflow

**Sources:**[.github/workflows/run-unit-tests.yml 26-66](https://github.com/tenstorrent/tt-system-firmware/blob/ab59b09c/.github/workflows/run-unit-tests.yml#L26-L66)

### Test Platforms

| Platform | Purpose | Tests |
| --- | --- | --- |
| `unit_testing` | Unit test platform | Firmware logic tests |
| `native_sim` | Native simulation | Host-based execution |
| `native_sim/native/64` | 64-bit native | TT simulation tests |

Test configuration files are located in:

*   `tests/`: Unit tests
*   `samples/`: Sample applications
*   `test-conf/`: Test-specific configuration overlays

**Sources:**[.github/workflows/run-unit-tests.yml 26-105](https://github.com/tenstorrent/tt-system-firmware/blob/ab59b09c/.github/workflows/run-unit-tests.yml#L26-L105)[test-conf/tests/drivers/pwm/pwm_api/testcase.yaml 1-18](https://github.com/tenstorrent/tt-system-firmware/blob/ab59b09c/test-conf/tests/drivers/pwm/pwm_api/testcase.yaml#L1-L18)

* * *

## Quality Assurance Workflows

The build system includes several quality assurance workflows that run on every pull request.

### QA Workflow Summary

| Workflow | File | Purpose |
| --- | --- | --- |
| Compliance Checks | `compliance.yml` | Code style, commit format, Kconfig validation |
| Copyright Check | `copyright-check.yml` | Verify copyright headers |
| License Check | `license-check.yml` | Scan code for license compliance using scancode |
| Blob Verification | `verify-blob.yml` | Verify SHA256 checksums of binary blobs |

**Sources:**[.github/workflows/compliance.yml 1-157](https://github.com/tenstorrent/tt-system-firmware/blob/ab59b09c/.github/workflows/compliance.yml#L1-L157)[.github/workflows/copyright-check.yml 1-39](https://github.com/tenstorrent/tt-system-firmware/blob/ab59b09c/.github/workflows/copyright-check.yml#L1-L39)[.github/workflows/license-check.yml 1-57](https://github.com/tenstorrent/tt-system-firmware/blob/ab59b09c/.github/workflows/license-check.yml#L1-L57)[.github/workflows/verify-blob.yml 1-48](https://github.com/tenstorrent/tt-system-firmware/blob/ab59b09c/.github/workflows/verify-blob.yml#L1-L48)

### Blob Verification Process

The `verify-blob.yml` workflow ensures all binary blobs have correct SHA256 checksums:

1.   Parse `zephyr/module.yml` to extract blob declarations
2.   Compute SHA256 checksum for each blob file
3.   Compare computed checksum against declared checksum
4.   Fail if any mismatch is detected

This is implemented in `scripts/verify_blob.py`.

**Sources:**[scripts/verify_blob.py 1-55](https://github.com/tenstorrent/tt-system-firmware/blob/ab59b09c/scripts/verify_blob.py#L1-L55)

* * *

## Native Tooling Build

In addition to firmware builds, the system builds native host tools for debugging and tracing.

### Native Build Workflow

**Sources:**[.github/workflows/build-native.yml 25-44](https://github.com/tenstorrent/tt-system-firmware/blob/ab59b09c/.github/workflows/build-native.yml#L25-L44)

The native tools are built separately from firmware and uploaded as the `tt-tooling` artifact.

* * *

## Build Configuration Management

The build system uses Kconfig for configuration management, allowing board-specific and feature-specific settings.

### Configuration Layers

1.   **Base Configuration**: Defined in `Kconfig` files throughout the source tree
2.   **Board Configuration**: Board-specific defaults in `boards/tenstorrent/*/Kconfig`
3.   **Application Configuration**: Application-specific settings in `app/*/prj.conf`
4.   **Build-Time Overrides**: CMake arguments passed via `--` separator
5.   **Test Overlays**: Test-specific configurations in `test-conf/`

### Example Configuration Override

**Sources:**[.github/workflows/build-fw.yml 86-106](https://github.com/tenstorrent/tt-system-firmware/blob/ab59b09c/.github/workflows/build-fw.yml#L86-L106)[test-conf/tests/drivers/pwm/pwm_api/boards/tt_blackhole_tt_blackhole_dmc.conf 1-22](https://github.com/tenstorrent/tt-system-firmware/blob/ab59b09c/test-conf/tests/drivers/pwm/pwm_api/boards/tt_blackhole_tt_blackhole_dmc.conf#L1-L22)

* * *

## Special Build Targets

The build system supports several specialized build targets beyond the standard firmware builds.

### Assembly Test Firmware

The `build-assembly-test` job builds firmware for factory assembly testing:

*   **Boards**: P150, P300
*   **Target**: `app/dmc` with `CONFIG_TT_ASSEMBLY_TEST=y`
*   **Features**: RTT console enabled for debugging
*   **Outputs**: 
    *   Bootloader: `BOARD-bootloader-BRANCH.elf`
    *   Application: `BOARD-app-BRANCH.signed.hex`

**Sources:**[.github/workflows/build-fw.yml 204-326](https://github.com/tenstorrent/tt-system-firmware/blob/ab59b09c/.github/workflows/build-fw.yml#L204-L326)

### Recovery Firmware

The `build-recovery` job builds a standalone recovery firmware:

*   **Board**: `tt_blackhole@p100a/tt_blackhole/smc`
*   **Config**: `sysbuild/recovery.conf`
*   **Purpose**: Minimal firmware for system recovery
*   **Output**: `recovery.signed.bin`

**Sources:**[.github/workflows/build-fw.yml 140-203](https://github.com/tenstorrent/tt-system-firmware/blob/ab59b09c/.github/workflows/build-fw.yml#L140-L203)

### Preflash Firmware

The `build-preflash` job creates firmware for factory flashing:

*   **Script**: `scripts/build-preflash.sh`
*   **Output**: `preflash*.ihex` files
*   **Purpose**: Factory programming of flash memory

**Sources:**[.github/workflows/build-fw.yml 327-348](https://github.com/tenstorrent/tt-system-firmware/blob/ab59b09c/.github/workflows/build-fw.yml#L327-L348)

* * *

## Build System Summary

The tt-system-firmware build system leverages Zephyr's infrastructure to provide:

1.   **Multi-repository management** via west manifest
2.   **Parallel builds** for multiple board configurations
3.   **Secure firmware** through MCUBoot signing and verification
4.   **Combined bundles** packaging all firmware components
5.   **Comprehensive testing** including unit tests and compliance checks
6.   **Artifact organization** for easy deployment and debugging
7.   **Quality assurance** through automated checks

For detailed information about specific build system aspects, refer to:

*   [Zephyr Integration](https://deepwiki.com/tenstorrent/tt-system-firmware/6.1-zephyr-integration) - Module configuration and dependencies
*   [Build Pipeline and Artifacts](https://deepwiki.com/tenstorrent/tt-system-firmware/6.2-build-pipeline-and-artifacts) - CI/CD workflows and outputs
*   [Firmware Signing and MCUBoot](https://deepwiki.com/tenstorrent/tt-system-firmware/6.3-firmware-signing-and-mcuboot) - Security and bootloader integration
*   [Combined Firmware Bundles](https://deepwiki.com/tenstorrent/tt-system-firmware/6.4-combined-firmware-bundles) - Package format and deployment

Dismiss
Refresh this wiki

Enter email to refresh
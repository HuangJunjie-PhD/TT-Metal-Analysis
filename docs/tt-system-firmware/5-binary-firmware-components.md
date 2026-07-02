---
project: tt-system-firmware
github: https://github.com/tenstorrent/tt-system-firmware
deepwiki: https://deepwiki.com/tenstorrent/tt-system-firmware/5-binary-firmware-components
---

# Binary Firmware Components

Relevant source files
*   [include/tenstorrent/sys_init_defines.h](https://github.com/tenstorrent/tt-system-firmware/blob/ab59b09c/include/tenstorrent/sys_init_defines.h)
*   [lib/tenstorrent/bh_arc/CMakeLists.txt](https://github.com/tenstorrent/tt-system-firmware/blob/ab59b09c/lib/tenstorrent/bh_arc/CMakeLists.txt)
*   [lib/tenstorrent/bh_arc/eth.c](https://github.com/tenstorrent/tt-system-firmware/blob/ab59b09c/lib/tenstorrent/bh_arc/eth.c)
*   [lib/tenstorrent/bh_arc/gddr.c](https://github.com/tenstorrent/tt-system-firmware/blob/ab59b09c/lib/tenstorrent/bh_arc/gddr.c)
*   [lib/tenstorrent/bh_arc/gddr.h](https://github.com/tenstorrent/tt-system-firmware/blob/ab59b09c/lib/tenstorrent/bh_arc/gddr.h)
*   [lib/tenstorrent/bh_arc/pcie.c](https://github.com/tenstorrent/tt-system-firmware/blob/ab59b09c/lib/tenstorrent/bh_arc/pcie.c)
*   [lib/tenstorrent/bh_arc/pcie.h](https://github.com/tenstorrent/tt-system-firmware/blob/ab59b09c/lib/tenstorrent/bh_arc/pcie.h)
*   [lib/tenstorrent/bh_arc/pciesd.h](https://github.com/tenstorrent/tt-system-firmware/blob/ab59b09c/lib/tenstorrent/bh_arc/pciesd.h)
*   [lib/tenstorrent/bh_arc/tensix_init.c](https://github.com/tenstorrent/tt-system-firmware/blob/ab59b09c/lib/tenstorrent/bh_arc/tensix_init.c)
*   [lib/tenstorrent/bh_arc/tensix_init.h](https://github.com/tenstorrent/tt-system-firmware/blob/ab59b09c/lib/tenstorrent/bh_arc/tensix_init.h)
*   [zephyr/blobs/fw_pack-grayskull.tar.gz](https://github.com/tenstorrent/tt-system-firmware/blob/ab59b09c/zephyr/blobs/fw_pack-grayskull.tar.gz)
*   [zephyr/blobs/fw_pack-wormhole.tar.gz](https://github.com/tenstorrent/tt-system-firmware/blob/ab59b09c/zephyr/blobs/fw_pack-wormhole.tar.gz)
*   [zephyr/blobs/tt_blackhole_erisc.bin](https://github.com/tenstorrent/tt-system-firmware/blob/ab59b09c/zephyr/blobs/tt_blackhole_erisc.bin)
*   [zephyr/blobs/tt_blackhole_erisc_params.bin](https://github.com/tenstorrent/tt-system-firmware/blob/ab59b09c/zephyr/blobs/tt_blackhole_erisc_params.bin)
*   [zephyr/blobs/tt_blackhole_gddr_params_GALAXY_REVC.bin](https://github.com/tenstorrent/tt-system-firmware/blob/ab59b09c/zephyr/blobs/tt_blackhole_gddr_params_GALAXY_REVC.bin)
*   [zephyr/blobs/tt_blackhole_libpciesd.a](https://github.com/tenstorrent/tt-system-firmware/blob/ab59b09c/zephyr/blobs/tt_blackhole_libpciesd.a)
*   [zephyr/blobs/tt_blackhole_serdes_eth_fw.bin](https://github.com/tenstorrent/tt-system-firmware/blob/ab59b09c/zephyr/blobs/tt_blackhole_serdes_eth_fw.bin)
*   [zephyr/blobs/tt_blackhole_serdes_eth_fwreg.bin](https://github.com/tenstorrent/tt-system-firmware/blob/ab59b09c/zephyr/blobs/tt_blackhole_serdes_eth_fwreg.bin)
*   [zephyr/module.yml](https://github.com/tenstorrent/tt-system-firmware/blob/ab59b09c/zephyr/module.yml)

This page documents the pre-compiled binary firmware components that are bundled with tt-system-firmware. These blobs provide specialized firmware for hardware subsystems that run on dedicated RISC controllers within the Blackhole ASIC, as well as firmware packs for legacy hardware platforms. For information about the SMC and DMC firmware applications that manage these components, see [Firmware Applications](https://deepwiki.com/tenstorrent/tt-system-firmware/2-firmware-applications). For details on the hardware subsystems themselves, see [Hardware Subsystems](https://deepwiki.com/tenstorrent/tt-system-firmware/3-hardware-subsystems).

## Overview

The tt-system-firmware repository includes several categories of pre-compiled binary firmware that cannot be built from source within the Zephyr build system. These binaries are managed as Zephyr module blobs with SHA256 verification and are loaded by the chip management firmware (CMFW) during hardware initialization. The blobs fall into four main categories:

1.   **MRISC Firmware**: Runs on GDDR memory controller RISC processors (8 instances)
2.   **ERISC Firmware**: Runs on Ethernet controller RISC processors (14 instances)
3.   **SerDes Firmware**: Initializes PCIe and Ethernet SerDes PHY layers
4.   **Auxiliary Firmware**: Boot ROM, TRISC utilities, and legacy platform support

**Firmware Declaration and Integrity**

All binary firmware components are declared in [zephyr/module.yml 36-234](https://github.com/tenstorrent/tt-system-firmware/blob/ab59b09c/zephyr/module.yml#L36-L234) under the `blobs` section. Each blob entry includes:

The SHA256 checksums provide cryptographic verification of blob integrity, and the Zephyr build system validates these checksums before allowing the blobs to be used. The `verify-blob.yml` workflow in CI enforces this verification.

Sources: [zephyr/module.yml 36-234](https://github.com/tenstorrent/tt-system-firmware/blob/ab59b09c/zephyr/module.yml#L36-L234)

## MRISC Firmware for GDDR Controllers

**Purpose and Architecture**

MRISC (Memory RISC) firmware executes on dedicated RISC-V processors embedded in each of the 8 GDDR memory controller instances. This firmware is responsible for GDDR6 PHY initialization, memory training, and Built-In Self Test (BIST). The MRISC firmware operates autonomously after being loaded by CMFW during the hardware initialization sequence.

**Firmware Components**

The MRISC firmware consists of two parts:

1.   **Core Firmware**: `tt_blackhole_gddr_init.bin` (version 2.13)

    *   Executable code loaded to MRISC L1 memory at address 0x0
    *   Performs GDDR PHY initialization and training sequences
    *   Provides telemetry interface at L1 address 0x8000

2.   **Parameter Files**: Board-specific configuration loaded at offset 0x3C00

    *   Configure memory timings, voltage levels, and training parameters
    *   Different variants for each board type (P100A, P150A, P300A, etc.)

**Parameter File Variants**

The following table shows the board-specific MRISC parameter files declared in module.yml:

| Board Type | Parameter File | SHA256 (first 16 chars) | Use Case |
| --- | --- | --- | --- |
| ORION | `tt_blackhole_gddr_params_ORION.bin` | a6a60bd9ea8dd517 | Orion reference platform |
| P100A | `tt_blackhole_gddr_params_P100A.bin` | 23b0df46d5a669a4 | 100W single-chip card |
| P100B | `tt_blackhole_gddr_params_P100B.bin` | 041260a83fbf30c9 | 100W single-chip (rev B) |
| P100 | `tt_blackhole_gddr_params_P100.bin` | a2b442560bc0b8ac | 100W single-chip (base) |
| P150A | `tt_blackhole_gddr_params_P150A.bin` | 1a4aab3c58b39bba | 150W single-chip card |
| P150B | `tt_blackhole_gddr_params_P150B.bin` | 93cb6c3ff981cd0f | 150W single-chip (rev B) |
| P150C | `tt_blackhole_gddr_params_P150C.bin` | 0e4ed9986e18e7a9 | 150W single-chip (rev C) |
| P300A | `tt_blackhole_gddr_params_P300A.bin` | a627ee5baced8b1b | 300W dual-chip card |
| P300B | `tt_blackhole_gddr_params_P300B.bin` | 82de78316889dbaf | 300W dual-chip (rev B) |
| P300C | `tt_blackhole_gddr_params_P300C.bin` | b44f26c6626504210 | 300W dual-chip (rev C) |
| GALAXY | `tt_blackhole_gddr_params_GALAXY.bin` | 1438d62af2ee2740 | 32-chip Galaxy system |
| GALAXY_REVC | `tt_blackhole_gddr_params_GALAXY_REVC.bin` | 06169418f022f30d | Galaxy system (rev C) |

The parameter selection is performed by the firmware using board type detection from the FwTable. The GDDR speed configuration is extracted from the parameter file at DWORD offset 1 (see [lib/tenstorrent/bh_arc/gddr.c 65-70](https://github.com/tenstorrent/tt-system-firmware/blob/ab59b09c/lib/tenstorrent/bh_arc/gddr.c#L65-L70)).

**Loading Process**

The MRISC firmware loading sequence is orchestrated by `InitMrisc()` at SYS_INIT priority 105:

1.   **L1 Memory Wipe**: All MRISC L1 memories are cleared using NOC DMA transfers ([gddr.c 303-349](https://github.com/tenstorrent/tt-system-firmware/blob/ab59b09c/gddr.c#L303-L349))
2.   **AXI Enable**: NOC-to-AXI bridges are enabled for all three NOC2AXI ports per GDDR instance ([gddr.c 364-367](https://github.com/tenstorrent/tt-system-firmware/blob/ab59b09c/gddr.c#L364-L367))
3.   **Firmware Load**: Core firmware is DMA'd from SPI flash to L1 address 0x0 using `spi_arc_dma_transfer_to_tile()` ([gddr.c 181-189](https://github.com/tenstorrent/tt-system-firmware/blob/ab59b09c/gddr.c#L181-L189))
4.   **Config Load**: Board-specific parameters are loaded to L1 offset 0x3C00 ([gddr.c 190-198](https://github.com/tenstorrent/tt-system-firmware/blob/ab59b09c/gddr.c#L190-L198))
5.   **Clock Configuration**: GDDR memory clock (PLL3) is set based on speed parameter ([gddr.c 421-426](https://github.com/tenstorrent/tt-system-firmware/blob/ab59b09c/gddr.c#L421-L426))
6.   **Reset Release**: MRISC soft reset is de-asserted, allowing firmware execution ([gddr.c 145-155](https://github.com/tenstorrent/tt-system-firmware/blob/ab59b09c/gddr.c#L145-L155))

**Runtime Communication**

After initialization, CMFW communicates with MRISC firmware through a message-based protocol using scratch registers:

*   `MRISC_MSG_REGISTER` (0xFFB14018): Command/status register
*   `GDDR_MSG_STRUCT_ADDR` (0x6000): L1 memory mailbox for message arguments
*   `GDDR_TELEMETRY_TABLE_ADDR` (0x8000): Telemetry data structure

Supported MRISC message types ([gddr.h 40-49](https://github.com/tenstorrent/tt-system-firmware/blob/ab59b09c/gddr.h#L40-L49)):

*   `MRISC_MSG_TYPE_NONE` (0): No active message
*   `MRISC_MSG_TYPE_PHY_POWERDOWN` (1): Enter low-power state
*   `MRISC_MSG_TYPE_PHY_WAKEUP` (2): Exit low-power state
*   `MRISC_MSG_TYPE_RUN_MEMTEST` (8): Execute memory BIST

Sources: [zephyr/module.yml 82-189](https://github.com/tenstorrent/tt-system-firmware/blob/ab59b09c/zephyr/module.yml#L82-L189)[lib/tenstorrent/bh_arc/gddr.c 1-683](https://github.com/tenstorrent/tt-system-firmware/blob/ab59b09c/lib/tenstorrent/bh_arc/gddr.c#L1-L683)[lib/tenstorrent/bh_arc/gddr.h 1-75](https://github.com/tenstorrent/tt-system-firmware/blob/ab59b09c/lib/tenstorrent/bh_arc/gddr.h#L1-L75)

## ERISC Firmware for Ethernet Controllers

**Purpose and Architecture**

ERISC (Ethernet RISC) firmware runs on dedicated RISC-V processors within the 14 Ethernet controller instances in Blackhole. This firmware implements the Ethernet protocol stack, link management, and SerDes lane configuration. The firmware supports 40G, 100G, 200G, and 400G Ethernet speeds with dynamic speed negotiation.

**Firmware Components**

The ERISC firmware bundle consists of three binary components:

1.   **Core Firmware**: `tt_blackhole_erisc.bin` (version 1.9.0)

    *   Main executable loaded to ERISC L1 at address 0x70000
    *   Implements Ethernet MAC, link training, and protocol handling
    *   Size: approximately 48KB (from binary inspection)

2.   **Parameter Table**: `tt_blackhole_erisc_params.bin` (version 1.9.0)

    *   Configuration data loaded to L1 address 0x7C000
    *   Contains speed settings, MAC address base, board metadata
    *   Dynamically populated by CMFW before ERISC execution

3.   **SerDes Firmware Bundle**:

    *   `tt_blackhole_serdes_eth_fw.bin`: SerDes microcode (version 0.9.16)
    *   `tt_blackhole_serdes_eth_fwreg.bin`: SerDes register configuration (version 0.9.16)

**SerDes Mux Configuration**

Ethernet instances 4-6 and 7-9 share SerDes lanes with PCIe controllers through a multiplexer. The mux configuration is determined by PCIe property tables in FwTable and is encoded in the `RESET_UNIT_PCIE_MISC_CNTL3` registers ([eth.c 87-114](https://github.com/tenstorrent/tt-system-firmware/blob/ab59b09c/eth.c#L87-L114)). The `GetEthSel()` function calculates which Ethernet instances should be enabled based on:

*   PCIe mode (enabled/disabled) for PCI0 and PCI1
*   Number of PCIe SerDes lanes in use
*   Mux select bits (0b00, 0b10, 0b11)
*   Ethernet disable mask from FwTable

**Parameter Initialization**

The ERISC parameter table is dynamically populated by CMFW in `LoadEthFwCfg()` ([eth.c 260-317](https://github.com/tenstorrent/tt-system-firmware/blob/ab59b09c/eth.c#L260-L317)):

| Offset | Field | Source | Description |
| --- | --- | --- | --- |
| 0x00 | `eth_sel` | `GetEthSel()` | Bitmask of enabled instances + mux config |
| 0x04 | `speed_override` | FwTable | Optional speed setting (40/100/200/400) |
| 0x80 | `pcb_type` | FwTable | Board type identifier |
| 0x84 | `asic_location` | FwTable | Chip position in multi-chip system |
| 0x88-0x8F | `board_id` | FwTable | Unique board serial number |
| 0x90-0x94 | `mac_addr_base` | Efuse | Base MAC address (48-bit) |
| 0x98-0x9F | `asic_id` | Efuse | Chip unique identifier |
| 0xA0 | `eth_enabled` | Harvesting | Physical Ethernet availability |

The MAC address base is computed from the ASIC_ID efuse with an organizational prefix of 0x208C47 ([eth.c 186-198](https://github.com/tenstorrent/tt-system-firmware/blob/ab59b09c/eth.c#L186-L198)).

**Loading Sequence**

The Ethernet initialization sequence is split across two SYS_INIT functions at priority 106:

1.   **SerDes Initialization** (`SerdesEthInit`):

    *   Configure mux selects based on PCIe configuration
    *   Load SerDes register configuration to all active SerDes instances (0-5)
    *   Load SerDes microcode to all active SerDes instances
    *   Selection based on PCIe mode (6 total SerDes, some shared with PCIe)

2.   **ERISC Firmware Load** (`EthInit`):

    *   Wipe all ERISC L1 memories using NOC DMA
    *   Load core firmware to each enabled Ethernet instance at 0x70000
    *   Populate and load parameter table to each instance at 0x7C000
    *   Set RESET_PC_0 to 0x70000 and END_PC_0 to 0x7BFFC
    *   Release ERISC soft reset (bit 11 of SOFT_RESET_0)

**Runtime Access**

After initialization, CMFW can query ERISC firmware version by reading L1 address `ETH_FW_BASE_ADDR + ETH_FW_VERSION_ADDR_OFFSET` (0x70000 + 0x188) from any enabled Ethernet instance using `GetEthFwVersion()` ([eth.c 200-217](https://github.com/tenstorrent/tt-system-firmware/blob/ab59b09c/eth.c#L200-L217)).

Sources: [zephyr/module.yml 37-81](https://github.com/tenstorrent/tt-system-firmware/blob/ab59b09c/zephyr/module.yml#L37-L81)[lib/tenstorrent/bh_arc/eth.c 1-483](https://github.com/tenstorrent/tt-system-firmware/blob/ab59b09c/lib/tenstorrent/bh_arc/eth.c#L1-L483)

## PCIe SerDes Library

**Purpose and Architecture**

The PCIe SerDes library (`tt_blackhole_libpciesd.a`) is a pre-compiled static library that contains initialization routines and control functions for the PCIe PHY layer. Unlike the ERISC and MRISC firmware which run on remote RISC processors, this library is linked directly into the CMFW application and executes on the ARC processor.

**Library Contents**

The static library exports five primary functions:

1.   `SerdesInit()`: Initialize PCIe SerDes hardware
2.   `EnterLoopback()`: Configure SerDes loopback mode for testing
3.   `ExitLoopback()`: Exit SerDes loopback mode
4.   `CntlInitV2()`: Modern PCIe controller initialization with extended parameters
5.   `CntlInit()`: Legacy PCIe controller initialization

The library is built for the ARCompact ISA and linked during the Zephyr build process ([lib/tenstorrent/bh_arc/CMakeLists.txt 69-70](https://github.com/tenstorrent/tt-system-firmware/blob/ab59b09c/lib/tenstorrent/bh_arc/CMakeLists.txt#L69-L70)):

**Interface Definition**

The library interface is defined in [lib/tenstorrent/bh_arc/pciesd.h 1-46](https://github.com/tenstorrent/tt-system-firmware/blob/ab59b09c/lib/tenstorrent/bh_arc/pciesd.h#L1-L46):

**Usage in PCIe Initialization**

The PCIe SerDes library is invoked during the `pcie_init()` sequence at SYS_INIT priority 103. The initialization flow in [lib/tenstorrent/bh_arc/pcie.c 1-545](https://github.com/tenstorrent/tt-system-firmware/blob/ab59b09c/lib/tenstorrent/bh_arc/pcie.c#L1-L545) is:

The library requires access to ARC DMA functionality, and the interface is verified at compile time using a function pointer assignment ([pciesd.h 35](https://github.com/tenstorrent/tt-system-firmware/blob/ab59b09c/pciesd.h#L35-L35)):

**Device Configuration Parameters**

When invoking `CntlInitV2()`, the CMFW passes configuration derived from FwTable and efuses:

*   **board_id**: Unique board serial number from read-only FwTable
*   **vendor_id**: PCI vendor ID (0x1E52 for Tenstorrent)
*   **pcie_inst**: 0 or 1 (two PCIe controllers in Blackhole)
*   **serdes_inst**: SerDes instance number (0-5)
*   **max_pcie_speed**: Maximum link speed (1=Gen1, 2=Gen2, 3=Gen3, 4=Gen4)
*   **device_type**: 0=Endpoint, 1=Root Complex
*   **region masks**: BAR size configuration (default: 512MB, 1MB, 32GB)

The SerDes library handles low-level PHY calibration, lane configuration, and link training state machine transitions. It returns status codes indicating success or timeout conditions:

*   `PCIeInitOk` (0): Successful initialization
*   `PCIeSerdesFWLoadTimeout` (1): SerDes firmware load timed out
*   `PCIeLinkTrainTimeout` (2): Link training failed to reach L0 state

Sources: [zephyr/module.yml 55-63](https://github.com/tenstorrent/tt-system-firmware/blob/ab59b09c/zephyr/module.yml#L55-L63)[lib/tenstorrent/bh_arc/pciesd.h 1-46](https://github.com/tenstorrent/tt-system-firmware/blob/ab59b09c/lib/tenstorrent/bh_arc/pciesd.h#L1-L46)[lib/tenstorrent/bh_arc/pcie.c 1-545](https://github.com/tenstorrent/tt-system-firmware/blob/ab59b09c/lib/tenstorrent/bh_arc/pcie.c#L1-L545)[lib/tenstorrent/bh_arc/CMakeLists.txt 68-70](https://github.com/tenstorrent/tt-system-firmware/blob/ab59b09c/lib/tenstorrent/bh_arc/CMakeLists.txt#L68-L70)

## Auxiliary Firmware Components

**Boot ROM**

The `tt_blackhole_nano_bootcode.bin` (version 0.1) is a modified bootrom used during chip reset and recovery operations. This bootrom is loaded by the DMC via JTAG during chip resets to provide a minimal execution environment that allows the main CMFW to be loaded.

The bootrom is particularly important during dirty reset scenarios where the chip state must be restored to a known-good configuration. The DMC loads this bootrom through the JTAG interface before releasing the ARC processor from reset ([lib/tenstorrent/bh_chip/chip_reset.c](https://github.com/tenstorrent/tt-system-firmware/blob/ab59b09c/lib/tenstorrent/bh_chip/chip_reset.c)).

**TRISC Dest Wipe Firmware**

The `tt_blackhole_trisc_dest_wipe.bin` (version 0.2) is a small utility firmware loaded onto Tensix TRISC0 processors to clear the DEST register array. This is part of the Tensix initialization sequence and ensures clean state before workload execution.

The dest wipe firmware is loaded during `tensix_init()` at SYS_INIT priority 104. The process involves:

1.   Loading the firmware to TRISC0_CODE region at L1 address 0x6000
2.   Setting up a counter coordination mechanism at L1 addresses: 
    *   0x100000: Counter coordinate mailbox (stores Tensix X/Y)
    *   0x110000: Atomic counter for synchronization

3.   Broadcasting the firmware to all non-harvested Tensix cores
4.   Releasing TRISC0 reset and waiting for completion with 10ms timeout

The firmware uses atomic operations to coordinate across all Tensix cores, ensuring orderly DEST register clearing across the compute fabric ([lib/tenstorrent/bh_arc/tensix_init.c 39-287](https://github.com/tenstorrent/tt-system-firmware/blob/ab59b09c/lib/tenstorrent/bh_arc/tensix_init.c#L39-L287)).

**Legacy Platform Firmware Packs**

The repository includes complete firmware bundles for legacy Tenstorrent platforms:

1.   **Wormhole**: `fw_pack-wormhole.tar.gz` (version 19.7.0-rc1)

    *   Complete SMC/DMC firmware for Wormhole architecture
    *   Used by test infrastructure for Wormhole boards (n150, n300)
    *   Extracted and used by hardware smoke tests

2.   **Grayskull**: `fw_pack-grayskull.tar.gz` (version 80.18.0.0)

    *   Complete firmware for Grayskull architecture
    *   Legacy support for first-generation Tenstorrent AI accelerators

These tarball archives are declared as blobs to ensure consistent firmware versions across test environments. The CI infrastructure extracts these packs during hardware testing to flash firmware to Wormhole boards ([.github/workflows/hardware-smoke.yml](https://github.com/tenstorrent/tt-system-firmware/blob/ab59b09c/.github/workflows/hardware-smoke.yml)).

Sources: [zephyr/module.yml 199-234](https://github.com/tenstorrent/tt-system-firmware/blob/ab59b09c/zephyr/module.yml#L199-L234)[lib/tenstorrent/bh_arc/tensix_init.c 46-287](https://github.com/tenstorrent/tt-system-firmware/blob/ab59b09c/lib/tenstorrent/bh_arc/tensix_init.c#L46-L287)

## Firmware Loading Architecture

**SPI Flash Filesystem**

All firmware blobs (except the legacy tar archives) are stored in a custom SPI flash filesystem format called tt-boot-fs. This filesystem provides:

*   Tag-based lookup (e.g., "memfw", "ethfw", "ethsdfw")
*   Metadata including image size and flash offset
*   Support for sparse reading to minimize SPI bandwidth

The filesystem is accessed through the `tt_boot_fs_find_fd_by_tag()` API ([include/tenstorrent/tt_boot_fs.h](https://github.com/tenstorrent/tt-system-firmware/blob/ab59b09c/include/tenstorrent/tt_boot_fs.h)), which returns a file descriptor structure containing the SPI address and size for subsequent DMA operations.

**DMA Transfer Pipeline**

Firmware loading uses a two-stage DMA pipeline to optimize transfer performance:

The key transfer function is `spi_arc_dma_transfer_to_tile()` defined in [lib/tenstorrent/bh_arc/spi_flash_buf.c](https://github.com/tenstorrent/tt-system-firmware/blob/ab59b09c/lib/tenstorrent/bh_arc/spi_flash_buf.c) This function:

1.   Reads firmware from SPI flash to ARC SRAM scratchpad buffer (4KB-16KB chunks)
2.   Transfers each chunk via NOC DMA to the target tile's L1 memory
3.   Repeats until entire firmware image is transferred
4.   Uses hardware DMA controllers to minimize CPU overhead

The scratchpad buffer size is configurable via `CONFIG_TT_BH_ARC_SCRATCHPAD_SIZE` Kconfig option.

**Initialization Priority Order**

The firmware loading occurs at specific SYS_INIT priorities to ensure correct dependency ordering:

| Priority | Function | Firmware Loaded | Dependencies |
| --- | --- | --- | --- |
| 92 | `InitSpiFS_PRIO` | - | SPI flash driver ready |
| 104 | `tensix_init_PRIO` | TRISC dest wipe | NOC initialized, Tensix resets released |
| 105 | `InitMrisc_PRIO` | MRISC core + params | PLL3 configured, GDDR clocks ready |
| 106 | `eth_init_PRIO` | ERISC + SerDes | MRISC complete, Ethernet clocks ready |

This ordering ensures that:

*   Clock sources are stable before firmware execution
*   NOC routing is configured before DMA transfers
*   Hardware resets are released in proper sequence
*   Dependent subsystems initialize after prerequisites

Sources: [include/tenstorrent/sys_init_defines.h 1-39](https://github.com/tenstorrent/tt-system-firmware/blob/ab59b09c/include/tenstorrent/sys_init_defines.h#L1-L39)[lib/tenstorrent/bh_arc/gddr.c 351-442](https://github.com/tenstorrent/tt-system-firmware/blob/ab59b09c/lib/tenstorrent/bh_arc/gddr.c#L351-L442)[lib/tenstorrent/bh_arc/eth.c 319-483](https://github.com/tenstorrent/tt-system-firmware/blob/ab59b09c/lib/tenstorrent/bh_arc/eth.c#L319-L483)[lib/tenstorrent/bh_arc/tensix_init.c 1-296](https://github.com/tenstorrent/tt-system-firmware/blob/ab59b09c/lib/tenstorrent/bh_arc/tensix_init.c#L1-L296)

## Firmware Versioning and Updates

**Version Identification**

Each binary blob has an embedded version number accessible through runtime queries:

*   **MRISC**: Version stored in telemetry table at L1 offset 0x8000, fields `mrisc_fw_version_major` and `mrisc_fw_version_minor` ([lib/tenstorrent/bh_arc/gddr_telemetry_table.h](https://github.com/tenstorrent/tt-system-firmware/blob/ab59b09c/lib/tenstorrent/bh_arc/gddr_telemetry_table.h))
*   **ERISC**: Version stored at L1 offset 0x70188 (ETH_FW_BASE + 0x188), readable via `GetEthFwVersion()` ([eth.c 200-217](https://github.com/tenstorrent/tt-system-firmware/blob/ab59b09c/eth.c#L200-L217))
*   **Module Manifest**: All versions declared in [zephyr/module.yml 36-234](https://github.com/tenstorrent/tt-system-firmware/blob/ab59b09c/zephyr/module.yml#L36-L234) with semantic versioning

The CMFW can query firmware versions at runtime to verify compatibility and report telemetry. Version checks are performed before certain operations, such as MRISC memory test which requires firmware >= 2.7 ([gddr.c 246-254](https://github.com/tenstorrent/tt-system-firmware/blob/ab59b09c/gddr.c#L246-L254)).

**Blob Integrity Verification**

The CI pipeline includes a dedicated workflow `.github/workflows/verify-blob.yml` that:

1.   Downloads all declared blob files from GitHub
2.   Computes SHA256 checksums
3.   Verifies checksums match module.yml declarations
4.   Fails the build if any mismatch is detected

This ensures that:

*   Blobs have not been corrupted or tampered with
*   Local checkouts have the correct firmware versions
*   Updates to blobs are intentional and tracked

**Firmware Update Process**

Updating firmware blobs requires:

1.   Replacing the binary file in `zephyr/blobs/`
2.   Computing new SHA256 checksum: `sha256sum <filename>`
3.   Updating the checksum and version in [zephyr/module.yml](https://github.com/tenstorrent/tt-system-firmware/blob/ab59b09c/zephyr/module.yml)
4.   Updating the GitHub raw URL if necessary
5.   Committing both the blob and manifest changes
6.   CI verification of new checksums

The firmware bundles (`.fwbundle` files) are generated by the build system and include all necessary blobs plus the SMC/DMC application firmware for complete system updates via `tt-flash` tool.

Sources: [zephyr/module.yml 36-234](https://github.com/tenstorrent/tt-system-firmware/blob/ab59b09c/zephyr/module.yml#L36-L234)[.github/workflows/verify-blob.yml](https://github.com/tenstorrent/tt-system-firmware/blob/ab59b09c/.github/workflows/verify-blob.yml)[lib/tenstorrent/bh_arc/gddr.c 240-254](https://github.com/tenstorrent/tt-system-firmware/blob/ab59b09c/lib/tenstorrent/bh_arc/gddr.c#L240-L254)[lib/tenstorrent/bh_arc/eth.c 200-217](https://github.com/tenstorrent/tt-system-firmware/blob/ab59b09c/lib/tenstorrent/bh_arc/eth.c#L200-L217)

Dismiss
Refresh this wiki

Enter email to refresh
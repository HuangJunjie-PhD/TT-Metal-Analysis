---
project: tt-system-firmware
github: https://github.com/tenstorrent/tt-system-firmware
deepwiki: https://deepwiki.com/tenstorrent/tt-system-firmware/3-hardware-subsystems
---

# Hardware Subsystems

Relevant source files
*   [app/dmc/src/main.c](https://github.com/tenstorrent/tt-system-firmware/blob/ab59b09c/app/dmc/src/main.c)
*   [include/tenstorrent/bh_arc.h](https://github.com/tenstorrent/tt-system-firmware/blob/ab59b09c/include/tenstorrent/bh_arc.h)
*   [include/tenstorrent/bh_chip.h](https://github.com/tenstorrent/tt-system-firmware/blob/ab59b09c/include/tenstorrent/bh_chip.h)
*   [include/tenstorrent/jtag_bootrom.h](https://github.com/tenstorrent/tt-system-firmware/blob/ab59b09c/include/tenstorrent/jtag_bootrom.h)
*   [include/tenstorrent/sys_init_defines.h](https://github.com/tenstorrent/tt-system-firmware/blob/ab59b09c/include/tenstorrent/sys_init_defines.h)
*   [include/tenstorrent/tt_smbus_regs.h](https://github.com/tenstorrent/tt-system-firmware/blob/ab59b09c/include/tenstorrent/tt_smbus_regs.h)
*   [lib/tenstorrent/bh_arc/CMakeLists.txt](https://github.com/tenstorrent/tt-system-firmware/blob/ab59b09c/lib/tenstorrent/bh_arc/CMakeLists.txt)
*   [lib/tenstorrent/bh_arc/cm2dm_msg.c](https://github.com/tenstorrent/tt-system-firmware/blob/ab59b09c/lib/tenstorrent/bh_arc/cm2dm_msg.c)
*   [lib/tenstorrent/bh_arc/cm2dm_msg.h](https://github.com/tenstorrent/tt-system-firmware/blob/ab59b09c/lib/tenstorrent/bh_arc/cm2dm_msg.h)
*   [lib/tenstorrent/bh_arc/eth.c](https://github.com/tenstorrent/tt-system-firmware/blob/ab59b09c/lib/tenstorrent/bh_arc/eth.c)
*   [lib/tenstorrent/bh_arc/gddr.c](https://github.com/tenstorrent/tt-system-firmware/blob/ab59b09c/lib/tenstorrent/bh_arc/gddr.c)
*   [lib/tenstorrent/bh_arc/gddr.h](https://github.com/tenstorrent/tt-system-firmware/blob/ab59b09c/lib/tenstorrent/bh_arc/gddr.h)
*   [lib/tenstorrent/bh_arc/pcie.c](https://github.com/tenstorrent/tt-system-firmware/blob/ab59b09c/lib/tenstorrent/bh_arc/pcie.c)
*   [lib/tenstorrent/bh_arc/pcie.h](https://github.com/tenstorrent/tt-system-firmware/blob/ab59b09c/lib/tenstorrent/bh_arc/pcie.h)
*   [lib/tenstorrent/bh_arc/pciesd.h](https://github.com/tenstorrent/tt-system-firmware/blob/ab59b09c/lib/tenstorrent/bh_arc/pciesd.h)
*   [lib/tenstorrent/bh_arc/smbus_target.c](https://github.com/tenstorrent/tt-system-firmware/blob/ab59b09c/lib/tenstorrent/bh_arc/smbus_target.c)
*   [lib/tenstorrent/bh_arc/tensix_init.c](https://github.com/tenstorrent/tt-system-firmware/blob/ab59b09c/lib/tenstorrent/bh_arc/tensix_init.c)
*   [lib/tenstorrent/bh_arc/tensix_init.h](https://github.com/tenstorrent/tt-system-firmware/blob/ab59b09c/lib/tenstorrent/bh_arc/tensix_init.h)
*   [lib/tenstorrent/bh_chip/CMakeLists.txt](https://github.com/tenstorrent/tt-system-firmware/blob/ab59b09c/lib/tenstorrent/bh_chip/CMakeLists.txt)
*   [lib/tenstorrent/bh_chip/bh_arc.c](https://github.com/tenstorrent/tt-system-firmware/blob/ab59b09c/lib/tenstorrent/bh_chip/bh_arc.c)
*   [lib/tenstorrent/bh_chip/bh_chip.c](https://github.com/tenstorrent/tt-system-firmware/blob/ab59b09c/lib/tenstorrent/bh_chip/bh_chip.c)
*   [lib/tenstorrent/bh_chip/strapping.c](https://github.com/tenstorrent/tt-system-firmware/blob/ab59b09c/lib/tenstorrent/bh_chip/strapping.c)
*   [lib/tenstorrent/jtag_bootrom/jtag_bootrom.c](https://github.com/tenstorrent/tt-system-firmware/blob/ab59b09c/lib/tenstorrent/jtag_bootrom/jtag_bootrom.c)
*   [lib/tenstorrent/jtag_bootrom/reset.c](https://github.com/tenstorrent/tt-system-firmware/blob/ab59b09c/lib/tenstorrent/jtag_bootrom/reset.c)

This page provides an overview of the major hardware subsystems in Blackhole ASICs that are managed by the chip management firmware (CMFW). The Blackhole chip contains four primary hardware subsystems: Tensix compute cores, GDDR memory controllers, Ethernet interfaces, and PCIe host interfaces. Each subsystem has dedicated firmware and initialization sequences coordinated by the CMFW running on the ARC processor.

For detailed information about the boot and initialization sequence, see [Boot and Initialization Sequence](https://deepwiki.com/tenstorrent/tt-system-firmware/3.1-boot-and-initialization-sequence). For subsystem-specific details, see [PCIe Subsystem](https://deepwiki.com/tenstorrent/tt-system-firmware/3.2-pcie-subsystem), [Tensix Compute Cores](https://deepwiki.com/tenstorrent/tt-system-firmware/3.3-tensix-compute-cores), [GDDR Memory Controllers](https://deepwiki.com/tenstorrent/tt-system-firmware/3.4-gddr-memory-controllers), and [Ethernet Subsystem](https://deepwiki.com/tenstorrent/tt-system-firmware/3.5-ethernet-subsystem). For information about the NOC interconnect and translation layer, see [Network-on-Chip (NOC)](https://deepwiki.com/tenstorrent/tt-system-firmware/4-network-on-chip-(noc)).

* * *

## Hardware Subsystem Architecture

The Blackhole ASIC integrates four categories of hardware blocks, each serving distinct purposes in the AI accelerator pipeline. The firmware coordinates initialization, configuration, and runtime management of these subsystems through the Network-on-Chip (NOC) fabric.

**Sources:**[lib/tenstorrent/bh_arc/CMakeLists.txt 1-71](https://github.com/tenstorrent/tt-system-firmware/blob/ab59b09c/lib/tenstorrent/bh_arc/CMakeLists.txt#L1-L71)[include/tenstorrent/sys_init_defines.h 1-40](https://github.com/tenstorrent/tt-system-firmware/blob/ab59b09c/include/tenstorrent/sys_init_defines.h#L1-L40)

* * *

## Tensix Compute Cores

The Tensix subsystem comprises a 14x10 grid of compute cores forming the primary AI acceleration fabric. Each Tensix core contains three RISC processors (TRISCs), local L1 memory, and specialized compute units for tensor operations.

### Key Characteristics

| Parameter | Value | Location |
| --- | --- | --- |
| Grid Dimensions | 14 columns × 10 rows | [lib/tenstorrent/bh_arc/tensix_init.c 33-36](https://github.com/tenstorrent/tt-system-firmware/blob/ab59b09c/lib/tenstorrent/bh_arc/tensix_init.c#L33-L36) |
| Total Cores | 140 (before harvesting) | Calculated from grid |
| L1 Memory per Core | 1.5 MB (1536 KB) | [lib/tenstorrent/bh_arc/tensix_init.c 37](https://github.com/tenstorrent/tt-system-firmware/blob/ab59b09c/lib/tenstorrent/bh_arc/tensix_init.c#L37-L37) |
| TRISCs per Core | 3 | Multiple references |
| Initialization Function | `tensix_init()` | [lib/tenstorrent/bh_arc/tensix_init.c 326-346](https://github.com/tenstorrent/tt-system-firmware/blob/ab59b09c/lib/tenstorrent/bh_arc/tensix_init.c#L326-L346) |
| Init Priority | 104 | [include/tenstorrent/sys_init_defines.h 27](https://github.com/tenstorrent/tt-system-firmware/blob/ab59b09c/include/tenstorrent/sys_init_defines.h#L27-L27) |

### Initialization Operations

The CMFW performs the following operations during Tensix initialization:

1.   **Clock Gating Enable** (`EnableTensixCG()`): Configures clock gating for power management with hysteresis value of 2 for all functional blocks [lib/tenstorrent/bh_arc/tensix_init.c 84-108](https://github.com/tenstorrent/tt-system-firmware/blob/ab59b09c/lib/tenstorrent/bh_arc/tensix_init.c#L84-L108)

2.   **L1 Memory Wipe** (`wipe_l1()`): Zeros all L1 memory in non-harvested cores using NOC DMA broadcasts for efficiency [lib/tenstorrent/bh_arc/tensix_init.c 117-178](https://github.com/tenstorrent/tt-system-firmware/blob/ab59b09c/lib/tenstorrent/bh_arc/tensix_init.c#L117-L178)

3.   **DEST Register Clear** (`wipe_dest()`): Loads temporary firmware to each TRISC 0 to clear DEST registers that cannot be written via NOC [lib/tenstorrent/bh_arc/tensix_init.c 241-315](https://github.com/tenstorrent/tt-system-firmware/blob/ab59b09c/lib/tenstorrent/bh_arc/tensix_init.c#L241-L315)

The initialization uses multicast NOC transactions to configure all unharvested cores simultaneously, coordinated through a global synchronization counter at L1 address `0x110000`[lib/tenstorrent/bh_arc/tensix_init.c 54](https://github.com/tenstorrent/tt-system-firmware/blob/ab59b09c/lib/tenstorrent/bh_arc/tensix_init.c#L54-L54)

**Sources:**[lib/tenstorrent/bh_arc/tensix_init.c 1-347](https://github.com/tenstorrent/tt-system-firmware/blob/ab59b09c/lib/tenstorrent/bh_arc/tensix_init.c#L1-L347)[lib/tenstorrent/bh_arc/tensix_init.h 1-6](https://github.com/tenstorrent/tt-system-firmware/blob/ab59b09c/lib/tenstorrent/bh_arc/tensix_init.h#L1-L6)

* * *

## GDDR Memory Controllers

The GDDR subsystem provides high-bandwidth memory access through eight independent memory controllers. Each controller includes a dedicated MRISC (Memory RISC) processor that executes firmware for memory training and runtime management.

### Key Characteristics

| Parameter | Value | Location |
| --- | --- | --- |
| Number of Controllers | 8 | [lib/tenstorrent/bh_arc/gddr.h 16](https://github.com/tenstorrent/tt-system-firmware/blob/ab59b09c/lib/tenstorrent/bh_arc/gddr.h#L16-L16) |
| MRISC L1 Memory | 128 KB per controller | [lib/tenstorrent/bh_arc/gddr.c 49](https://github.com/tenstorrent/tt-system-firmware/blob/ab59b09c/lib/tenstorrent/bh_arc/gddr.c#L49-L49) |
| NOC2AXI Ports | 3 per controller | [lib/tenstorrent/bh_arc/gddr.h 17](https://github.com/tenstorrent/tt-system-firmware/blob/ab59b09c/lib/tenstorrent/bh_arc/gddr.h#L17-L17) |
| Memory Speed Range | 12000-20000 MHz | [lib/tenstorrent/bh_arc/gddr.h 13-14](https://github.com/tenstorrent/tt-system-firmware/blob/ab59b09c/lib/tenstorrent/bh_arc/gddr.h#L13-L14) |
| Training Timeout | 1000 ms | [lib/tenstorrent/bh_arc/gddr.h 34](https://github.com/tenstorrent/tt-system-firmware/blob/ab59b09c/lib/tenstorrent/bh_arc/gddr.h#L34-L34) |
| BIST Timeout | 1000 ms | [lib/tenstorrent/bh_arc/gddr.h 35](https://github.com/tenstorrent/tt-system-firmware/blob/ab59b09c/lib/tenstorrent/bh_arc/gddr.h#L35-L35) |

### MRISC Firmware Management

The MRISC firmware is loaded from SPI flash during initialization:

*   **Firmware Binary**: Tagged as `"memfw"` in boot filesystem [lib/tenstorrent/bh_arc/gddr.c 51](https://github.com/tenstorrent/tt-system-firmware/blob/ab59b09c/lib/tenstorrent/bh_arc/gddr.c#L51-L51)
*   **Configuration Data**: Tagged as `"memfwcfg"` in boot filesystem [lib/tenstorrent/bh_arc/gddr.c 52](https://github.com/tenstorrent/tt-system-firmware/blob/ab59b09c/lib/tenstorrent/bh_arc/gddr.c#L52-L52)
*   **Load Address**: `0x0` in MRISC L1 memory [lib/tenstorrent/bh_arc/gddr.c 183-189](https://github.com/tenstorrent/tt-system-firmware/blob/ab59b09c/lib/tenstorrent/bh_arc/gddr.c#L183-L189)
*   **Config Offset**: `0x3C00` in MRISC L1 memory [lib/tenstorrent/bh_arc/gddr.c 46](https://github.com/tenstorrent/tt-system-firmware/blob/ab59b09c/lib/tenstorrent/bh_arc/gddr.c#L46-L46)

### Initialization Sequence

**Sources:**[lib/tenstorrent/bh_arc/gddr.c 351-544](https://github.com/tenstorrent/tt-system-firmware/blob/ab59b09c/lib/tenstorrent/bh_arc/gddr.c#L351-L544)[lib/tenstorrent/bh_arc/gddr.h 1-75](https://github.com/tenstorrent/tt-system-firmware/blob/ab59b09c/lib/tenstorrent/bh_arc/gddr.h#L1-L75)

### Memory Training and BIST

After firmware loading, each MRISC autonomously performs GDDR training through the following states tracked in scratch register `RISC_CTRL_A_SCRATCH_0` (0xFFB14010):

*   `MRISC_INIT_BEFORE` (0x11111111): Pre-initialization state set by CMFW
*   `MRISC_INIT_STARTED` (0x0): Training in progress
*   `MRISC_INIT_FINISHED` (0xdeadbeef): Training successful
*   `MRISC_INIT_FAILED` (0xfa11): Training failed

Upon successful training, CMFW triggers hardware BIST using message type `MRISC_MSG_TYPE_RUN_MEMTEST` (8) with test parameters written to L1 address `0x6000`. The BIST validates memory integrity across all address bits [lib/tenstorrent/bh_arc/gddr.c 236-300](https://github.com/tenstorrent/tt-system-firmware/blob/ab59b09c/lib/tenstorrent/bh_arc/gddr.c#L236-L300)

**Sources:**[lib/tenstorrent/bh_arc/gddr.c 444-507](https://github.com/tenstorrent/tt-system-firmware/blob/ab59b09c/lib/tenstorrent/bh_arc/gddr.c#L444-L507)[lib/tenstorrent/bh_arc/gddr.h 19-49](https://github.com/tenstorrent/tt-system-firmware/blob/ab59b09c/lib/tenstorrent/bh_arc/gddr.h#L19-L49)

* * *

## Ethernet Subsystem

The Ethernet subsystem provides chip-to-chip networking through 14 Ethernet channels. Each channel includes an ERISC (Ethernet RISC) processor with dedicated L1 memory and SerDes interfaces for physical connectivity.

### Key Characteristics

| Parameter | Value | Location |
| --- | --- | --- |
| Total Channels | 14 | Inferred from code |
| ERISC L1 Memory | 512 KB per channel | [lib/tenstorrent/bh_arc/eth.c 36](https://github.com/tenstorrent/tt-system-firmware/blob/ab59b09c/lib/tenstorrent/bh_arc/eth.c#L36-L36) |
| Firmware Load Address | `0x70000` | [lib/tenstorrent/bh_arc/eth.c 32](https://github.com/tenstorrent/tt-system-firmware/blob/ab59b09c/lib/tenstorrent/bh_arc/eth.c#L32-L32) |
| Parameter Table Address | `0x7c000` | [lib/tenstorrent/bh_arc/eth.c 33](https://github.com/tenstorrent/tt-system-firmware/blob/ab59b09c/lib/tenstorrent/bh_arc/eth.c#L33-L33) |
| SerDes Instances | 6 (Serdes 0-5) | [lib/tenstorrent/bh_arc/eth.c 356](https://github.com/tenstorrent/tt-system-firmware/blob/ab59b09c/lib/tenstorrent/bh_arc/eth.c#L356-L356) |
| Initialization Function | `eth_init()` | [lib/tenstorrent/bh_arc/eth.c 479-492](https://github.com/tenstorrent/tt-system-firmware/blob/ab59b09c/lib/tenstorrent/bh_arc/eth.c#L479-L492) |
| Init Priority | 106 | [include/tenstorrent/sys_init_defines.h 29](https://github.com/tenstorrent/tt-system-firmware/blob/ab59b09c/include/tenstorrent/sys_init_defines.h#L29-L29) |

### ERISC Firmware Components

The Ethernet initialization loads multiple firmware components from SPI flash:

| Component | Flash Tag | Purpose | Code Reference |
| --- | --- | --- | --- |
| ERISC Firmware | `"ethfw"` | Main Ethernet processor code | [lib/tenstorrent/bh_arc/eth.c 47](https://github.com/tenstorrent/tt-system-firmware/blob/ab59b09c/lib/tenstorrent/bh_arc/eth.c#L47-L47) |
| ERISC Config | `"ethfwcfg"` | Channel configuration table | [lib/tenstorrent/bh_arc/eth.c 46](https://github.com/tenstorrent/tt-system-firmware/blob/ab59b09c/lib/tenstorrent/bh_arc/eth.c#L46-L46) |
| SerDes Registers | `"ethsdreg"` | SerDes register initialization | [lib/tenstorrent/bh_arc/eth.c 48](https://github.com/tenstorrent/tt-system-firmware/blob/ab59b09c/lib/tenstorrent/bh_arc/eth.c#L48-L48) |
| SerDes Firmware | `"ethsdfw"` | SerDes processor firmware | [lib/tenstorrent/bh_arc/eth.c 49](https://github.com/tenstorrent/tt-system-firmware/blob/ab59b09c/lib/tenstorrent/bh_arc/eth.c#L49-L49) |

### SerDes Mux Configuration

Ethernet channels 4-6 and 7-9 share SerDes resources with PCIe through hardware multiplexers controlled by registers:

*   `RESET_UNIT_PCIE_MISC_CNTL_3` (0x8003009C) for channels 4-6
*   `RESET_UNIT_PCIE1_MISC_CNTL_3` (0x8003050C) for channels 7-9

The mux selection determines which Ethernet channels have physical connectivity based on PCIe configuration [lib/tenstorrent/bh_arc/eth.c 87-184](https://github.com/tenstorrent/tt-system-firmware/blob/ab59b09c/lib/tenstorrent/bh_arc/eth.c#L87-L184)

**Sources:**[lib/tenstorrent/bh_arc/eth.c 1-493](https://github.com/tenstorrent/tt-system-firmware/blob/ab59b09c/lib/tenstorrent/bh_arc/eth.c#L1-L493)[lib/tenstorrent/bh_arc/eth.h 1-12](https://github.com/tenstorrent/tt-system-firmware/blob/ab59b09c/lib/tenstorrent/bh_arc/eth.h#L1-L12)

### Channel Selection and Configuration

The `GetEthSel()` function computes which Ethernet channels to enable based on:

1.   **PCIe Configuration**: Channels 0-3 and 10-13 are disabled if corresponding PCIe instances are enabled
2.   **SerDes Mux Settings**: Channels 4-6 and 7-9 availability depends on mux configuration
3.   **Harvesting Mask**: Channels may be disabled due to manufacturing defects
4.   **Firmware Table Overrides**: Additional channels can be force-disabled via `eth_disable_mask`

The computed channel mask is passed to ERISC firmware in the configuration table along with board ID, ASIC ID, and MAC address base [lib/tenstorrent/bh_arc/eth.c 260-317](https://github.com/tenstorrent/tt-system-firmware/blob/ab59b09c/lib/tenstorrent/bh_arc/eth.c#L260-L317)

**Sources:**[lib/tenstorrent/bh_arc/eth.c 116-184](https://github.com/tenstorrent/tt-system-firmware/blob/ab59b09c/lib/tenstorrent/bh_arc/eth.c#L116-L184)[lib/tenstorrent/bh_arc/eth.c 260-317](https://github.com/tenstorrent/tt-system-firmware/blob/ab59b09c/lib/tenstorrent/bh_arc/eth.c#L260-L317)

* * *

## PCIe Subsystem

The PCIe subsystem provides host communication through two independent PCIe controllers, each supporting Gen1-4 operation and configurable as either endpoint or root complex. SerDes firmware handles physical layer initialization and link training.

### Key Characteristics

| Parameter | Value | Location |
| --- | --- | --- |
| Number of Instances | 2 (PCIe0, PCIe1) | Throughout pcie.c |
| PCIe Generations | Gen1-4 | [lib/tenstorrent/bh_arc/pcie.c 1-492](https://github.com/tenstorrent/tt-system-firmware/blob/ab59b09c/lib/tenstorrent/bh_arc/pcie.c#L1-L492) |
| Logical Coordinates | (2,0) for PCIe0, (11,0) for PCIe1 | [lib/tenstorrent/bh_arc/pcie.h 24-26](https://github.com/tenstorrent/tt-system-firmware/blob/ab59b09c/lib/tenstorrent/bh_arc/pcie.h#L24-L26) |
| Default BAR Sizes | BAR0: 512MB, BAR2: 1MB, BAR4: 32GB | [lib/tenstorrent/bh_arc/pcie.c 30-32](https://github.com/tenstorrent/tt-system-firmware/blob/ab59b09c/lib/tenstorrent/bh_arc/pcie.c#L30-L32) |
| SerDes Library | `tt_blackhole_libpciesd.a` | [lib/tenstorrent/bh_arc/CMakeLists.txt 69](https://github.com/tenstorrent/tt-system-firmware/blob/ab59b09c/lib/tenstorrent/bh_arc/CMakeLists.txt#L69-L69) |
| Initialization Function | `pcie_init()` | [lib/tenstorrent/bh_arc/pcie.c 367-492](https://github.com/tenstorrent/tt-system-firmware/blob/ab59b09c/lib/tenstorrent/bh_arc/pcie.c#L367-L492) |
| Init Priority | 103 | [include/tenstorrent/sys_init_defines.h 26](https://github.com/tenstorrent/tt-system-firmware/blob/ab59b09c/include/tenstorrent/sys_init_defines.h#L26-L26) |

### TLB Configuration for PCIe Access

The CMFW configures multiple NOC TLBs to access PCIe registers and SerDes blocks:

| TLB Index | Purpose | Base Address | Code Reference |
| --- | --- | --- | --- |
| 0 | SerDes 0 AlphaCore Registers | `0xFFFFFFFFE1000000` | [lib/tenstorrent/bh_arc/pcie.c 34](https://github.com/tenstorrent/tt-system-firmware/blob/ab59b09c/lib/tenstorrent/bh_arc/pcie.c#L34-L34) |
| 1 | SerDes 1 AlphaCore Registers | `0xFFFFFFFFE1000000 + 0x04000000` | [lib/tenstorrent/bh_arc/pcie.c 35](https://github.com/tenstorrent/tt-system-firmware/blob/ab59b09c/lib/tenstorrent/bh_arc/pcie.c#L35-L35) |
| 2 | SerDes 0 Control Registers | `0xFFFFFFFFE0000000 + 0x03000000` | [lib/tenstorrent/bh_arc/pcie.c 36](https://github.com/tenstorrent/tt-system-firmware/blob/ab59b09c/lib/tenstorrent/bh_arc/pcie.c#L36-L36) |
| 3 | SerDes 1 Control Registers | Similar with instance offset | [lib/tenstorrent/bh_arc/pcie.c 37](https://github.com/tenstorrent/tt-system-firmware/blob/ab59b09c/lib/tenstorrent/bh_arc/pcie.c#L37-L37) |
| 4 | PCIe SII Registers | `0xFFFFFFFFF0000000` | [lib/tenstorrent/bh_arc/pcie.c 38](https://github.com/tenstorrent/tt-system-firmware/blob/ab59b09c/lib/tenstorrent/bh_arc/pcie.c#L38-L38) |
| 5 | PCIe TLB Configuration | `0x1FC00000` | [lib/tenstorrent/bh_arc/pcie.c 39](https://github.com/tenstorrent/tt-system-firmware/blob/ab59b09c/lib/tenstorrent/bh_arc/pcie.c#L39-L39) |
| 14 | DBI (Design-Based Interface) | TLB 62 mapping with DBI flag | [lib/tenstorrent/bh_arc/pcie.h 27](https://github.com/tenstorrent/tt-system-firmware/blob/ab59b09c/lib/tenstorrent/bh_arc/pcie.h#L27-L27) |

The `ConfigurePCIeTlbs()` function sets up these mappings during initialization [lib/tenstorrent/bh_arc/pcie.c 319-339](https://github.com/tenstorrent/tt-system-firmware/blob/ab59b09c/lib/tenstorrent/bh_arc/pcie.c#L319-L339)

**Sources:**[lib/tenstorrent/bh_arc/pcie.c 1-492](https://github.com/tenstorrent/tt-system-firmware/blob/ab59b09c/lib/tenstorrent/bh_arc/pcie.c#L1-L492)[lib/tenstorrent/bh_arc/pcie.h 1-43](https://github.com/tenstorrent/tt-system-firmware/blob/ab59b09c/lib/tenstorrent/bh_arc/pcie.h#L1-L43)

### Initialization Flow

**Sources:**[lib/tenstorrent/bh_arc/pcie.c 367-492](https://github.com/tenstorrent/tt-system-firmware/blob/ab59b09c/lib/tenstorrent/bh_arc/pcie.c#L367-L492)[lib/tenstorrent/bh_arc/pciesd.h 1-52](https://github.com/tenstorrent/tt-system-firmware/blob/ab59b09c/lib/tenstorrent/bh_arc/pciesd.h#L1-L52)

### BAR (Base Address Register) Configuration

PCIe BAR sizes are configured via firmware table overrides with validation:

*   **BAR0**: Fixed at 512 MB for NOC access, overrides are rejected [lib/tenstorrent/bh_arc/pcie.c 206-216](https://github.com/tenstorrent/tt-system-firmware/blob/ab59b09c/lib/tenstorrent/bh_arc/pcie.c#L206-L216)
*   **BAR2**: Fixed at 1 MB for configuration space [lib/tenstorrent/bh_arc/pcie.c 218-228](https://github.com/tenstorrent/tt-system-firmware/blob/ab59b09c/lib/tenstorrent/bh_arc/pcie.c#L218-L228)
*   **BAR4**: Configurable, rounded up to nearest power of two if needed [lib/tenstorrent/bh_arc/pcie.c 230-246](https://github.com/tenstorrent/tt-system-firmware/blob/ab59b09c/lib/tenstorrent/bh_arc/pcie.c#L230-L246)

The `CntlInitV2Param` structure packages these sizes along with board ID, vendor ID, and device type for the SerDes initialization library [lib/tenstorrent/bh_arc/pcie.c 195-260](https://github.com/tenstorrent/tt-system-firmware/blob/ab59b09c/lib/tenstorrent/bh_arc/pcie.c#L195-L260)

**Sources:**[lib/tenstorrent/bh_arc/pcie.c 195-260](https://github.com/tenstorrent/tt-system-firmware/blob/ab59b09c/lib/tenstorrent/bh_arc/pcie.c#L195-L260)[lib/tenstorrent/bh_arc/pciesd.h 22-32](https://github.com/tenstorrent/tt-system-firmware/blob/ab59b09c/lib/tenstorrent/bh_arc/pciesd.h#L22-L32)

* * *

## Communication Infrastructure

All hardware subsystems are interconnected and managed through the Network-on-Chip (NOC) fabric, which provides a unified communication mechanism for the CMFW running on the ARC processor.

### NOC Architecture

The Blackhole ASIC includes two independent NOC rings (NOC0 and NOC1) that provide redundant paths for communication. Each NOC supports:

*   **Logical Coordinate Mapping**: Tiles are addressed using (x, y) coordinates that can be translated to physical coordinates to handle harvesting
*   **TLB-Based Translation**: 16 TLBs per NOC provide memory-mapped windows into remote tile address spaces
*   **Multicast and Broadcast**: Efficient bulk operations to multiple tiles
*   **Ordering Guarantees**: Configurable ordering modes for memory consistency

### NOC2AXI Interface

The NOC2AXI translation layer allows the ARC processor to access registers and memories in remote tiles:

Key NOC2AXI functions used throughout subsystem initialization:

| Function | Purpose | Usage Examples |
| --- | --- | --- |
| `NOC2AXITlbSetup()` | Configure TLB mapping to (x,y,addr) | [lib/tenstorrent/bh_arc/noc2axi.c](https://github.com/tenstorrent/tt-system-firmware/blob/ab59b09c/lib/tenstorrent/bh_arc/noc2axi.c) |
| `NOC2AXIWrite32()` | Write 32-bit value via TLB | [lib/tenstorrent/bh_arc/tensix_init.c 103-107](https://github.com/tenstorrent/tt-system-firmware/blob/ab59b09c/lib/tenstorrent/bh_arc/tensix_init.c#L103-L107) |
| `NOC2AXIRead32()` | Read 32-bit value via TLB | [lib/tenstorrent/bh_arc/gddr.c 99-106](https://github.com/tenstorrent/tt-system-firmware/blob/ab59b09c/lib/tenstorrent/bh_arc/gddr.c#L99-L106) |
| `NOC2AXIMulticastTlbSetup()` | Configure TLB for multicast region | [lib/tenstorrent/bh_arc/tensix_init.c 228-231](https://github.com/tenstorrent/tt-system-firmware/blob/ab59b09c/lib/tenstorrent/bh_arc/tensix_init.c#L228-L231) |
| `NOC2AXITensixBroadcastTlbSetup()` | Configure TLB for broadcast to all Tensix | [lib/tenstorrent/bh_arc/tensix_init.c 101](https://github.com/tenstorrent/tt-system-firmware/blob/ab59b09c/lib/tenstorrent/bh_arc/tensix_init.c#L101-L101) |

**Sources:**[lib/tenstorrent/bh_arc/tensix_init.c 101-107](https://github.com/tenstorrent/tt-system-firmware/blob/ab59b09c/lib/tenstorrent/bh_arc/tensix_init.c#L101-L107)[lib/tenstorrent/bh_arc/gddr.c 72-115](https://github.com/tenstorrent/tt-system-firmware/blob/ab59b09c/lib/tenstorrent/bh_arc/gddr.c#L72-L115)[lib/tenstorrent/bh_arc/eth.c 77-85](https://github.com/tenstorrent/tt-system-firmware/blob/ab59b09c/lib/tenstorrent/bh_arc/eth.c#L77-L85)

### Firmware Loading via SPI Flash

All subsystem firmware binaries are stored in SPI flash and loaded during initialization. The boot filesystem provides tagged access to firmware images:

The `tt_boot_fs_find_fd_by_tag()` function locates firmware by string tag and returns the SPI address and size. Firmware is then either DMA'd directly to tile L1 memory or loaded into an intermediate buffer for processing [lib/tenstorrent/bh_arc/gddr.c 378-398](https://github.com/tenstorrent/tt-system-firmware/blob/ab59b09c/lib/tenstorrent/bh_arc/gddr.c#L378-L398)[lib/tenstorrent/bh_arc/eth.c 441-453](https://github.com/tenstorrent/tt-system-firmware/blob/ab59b09c/lib/tenstorrent/bh_arc/eth.c#L441-L453)

**Sources:**[lib/tenstorrent/bh_arc/gddr.c 378-412](https://github.com/tenstorrent/tt-system-firmware/blob/ab59b09c/lib/tenstorrent/bh_arc/gddr.c#L378-L412)[lib/tenstorrent/bh_arc/eth.c 320-378](https://github.com/tenstorrent/tt-system-firmware/blob/ab59b09c/lib/tenstorrent/bh_arc/eth.c#L320-L378)[lib/tenstorrent/bh_arc/tensix_init.c 256-264](https://github.com/tenstorrent/tt-system-firmware/blob/ab59b09c/lib/tenstorrent/bh_arc/tensix_init.c#L256-L264)

* * *

## Initialization Dependencies and Ordering

Hardware subsystem initialization follows a strictly ordered sequence managed by Zephyr's `SYS_INIT` framework with priority levels defined in [include/tenstorrent/sys_init_defines.h 1-40](https://github.com/tenstorrent/tt-system-firmware/blob/ab59b09c/include/tenstorrent/sys_init_defines.h#L1-L40) The ordering ensures dependencies are satisfied before each subsystem initializes.

### Initialization Priority Sequence

| Priority | Function | Subsystem | Key Dependencies |
| --- | --- | --- | --- |
| 94 | `CATEarlyInit` | Core initialization | None |
| 95 | `CalculateHarvesting` | Tile enable masks | CAT efuses |
| 99 | `NocInit` | NOC fabric | Harvesting data |
| 103 | `pcie_init` | PCIe subsystem | NOC, TLBs |
| 104 | `tensix_init` | Tensix cores | NOC, TLBs |
| 105 | `InitMrisc` | GDDR controllers | NOC, TLBs |
| 106 | `eth_init` | Ethernet channels | NOC, TLBs |
| 111 | `gddr_training` | GDDR training | MRISC firmware loaded |

Each initialization function is registered using the `SYS_INIT_APP()` macro which expands to `SYS_INIT(func, POST_KERNEL, func##_PRIO)`[include/tenstorrent/sys_init_defines.h 38](https://github.com/tenstorrent/tt-system-firmware/blob/ab59b09c/include/tenstorrent/sys_init_defines.h#L38-L38)

**Sources:**[include/tenstorrent/sys_init_defines.h 12-37](https://github.com/tenstorrent/tt-system-firmware/blob/ab59b09c/include/tenstorrent/sys_init_defines.h#L12-L37)[lib/tenstorrent/bh_arc/gddr.c 442-584](https://github.com/tenstorrent/tt-system-firmware/blob/ab59b09c/lib/tenstorrent/bh_arc/gddr.c#L442-L584)[lib/tenstorrent/bh_arc/pcie.c 367](https://github.com/tenstorrent/tt-system-firmware/blob/ab59b09c/lib/tenstorrent/bh_arc/pcie.c#L367-L367)

### Cross-Subsystem Dependencies

The initialization sequence manages several critical dependencies:

1.   **NOC before all subsystems**: The NOC fabric and TLB configuration must complete before any subsystem can be accessed [include/tenstorrent/sys_init_defines.h 22](https://github.com/tenstorrent/tt-system-firmware/blob/ab59b09c/include/tenstorrent/sys_init_defines.h#L22-L22)

2.   **Tensix L1 wipe before GDDR/Ethernet wipe**: Tensix initialization clears its L1 first, which is then used as a source for zeroing GDDR and Ethernet L1 via NOC broadcasts [lib/tenstorrent/bh_arc/gddr.c 302-349](https://github.com/tenstorrent/tt-system-firmware/blob/ab59b09c/lib/tenstorrent/bh_arc/gddr.c#L302-L349)[lib/tenstorrent/bh_arc/eth.c 381-422](https://github.com/tenstorrent/tt-system-firmware/blob/ab59b09c/lib/tenstorrent/bh_arc/eth.c#L381-L422)

3.   **MRISC firmware load before training**: GDDR training cannot begin until MRISC firmware is loaded and released from reset [lib/tenstorrent/bh_arc/gddr.c 351-441](https://github.com/tenstorrent/tt-system-firmware/blob/ab59b09c/lib/tenstorrent/bh_arc/gddr.c#L351-L441)[lib/tenstorrent/bh_arc/gddr.c 509-544](https://github.com/tenstorrent/tt-system-firmware/blob/ab59b09c/lib/tenstorrent/bh_arc/gddr.c#L509-L544)

4.   **PCIe before Ethernet SerDes mux**: Ethernet channels 4-9 require PCIe configuration to determine SerDes mux settings [lib/tenstorrent/bh_arc/eth.c 87-114](https://github.com/tenstorrent/tt-system-firmware/blob/ab59b09c/lib/tenstorrent/bh_arc/eth.c#L87-L114)

**Sources:**[lib/tenstorrent/bh_arc/gddr.c 302-349](https://github.com/tenstorrent/tt-system-firmware/blob/ab59b09c/lib/tenstorrent/bh_arc/gddr.c#L302-L349)[lib/tenstorrent/bh_arc/eth.c 87-114](https://github.com/tenstorrent/tt-system-firmware/blob/ab59b09c/lib/tenstorrent/bh_arc/eth.c#L87-L114)[include/tenstorrent/sys_init_defines.h 1-40](https://github.com/tenstorrent/tt-system-firmware/blob/ab59b09c/include/tenstorrent/sys_init_defines.h#L1-L40)

* * *

## Harvesting Support

All hardware subsystems support harvesting (disabling defective tiles) through coordinate translation in the NOC layer. The firmware reads harvesting information from efuses and applies it during initialization.

The `tile_enable` global structure tracks which tiles are enabled:

`struct {    uint16_t tensix_col_enabled;  // Bitmask of enabled Tensix columns    uint8_t gddr_enabled;          // Bitmask of enabled GDDR controllers    uint16_t eth_enabled;          // Bitmask of enabled Ethernet channels} tile_enable;`
Subsystem initialization functions check these masks before attempting to configure each tile:

*   **Tensix**: Uses `POPCOUNT(tile_enable.tensix_col_enabled)` to determine enabled columns [lib/tenstorrent/bh_arc/tensix_init.c 300](https://github.com/tenstorrent/tt-system-firmware/blob/ab59b09c/lib/tenstorrent/bh_arc/tensix_init.c#L300-L300)
*   **GDDR**: Checks `IS_BIT_SET(tile_enable.gddr_enabled, gddr_inst)` for each controller [lib/tenstorrent/bh_arc/gddr.c 387](https://github.com/tenstorrent/tt-system-firmware/blob/ab59b09c/lib/tenstorrent/bh_arc/gddr.c#L387-L387)
*   **Ethernet**: Checks `IS_BIT_SET(tile_enable.eth_enabled, eth_inst)` for each channel [lib/tenstorrent/bh_arc/eth.c 451](https://github.com/tenstorrent/tt-system-firmware/blob/ab59b09c/lib/tenstorrent/bh_arc/eth.c#L451-L451)

NOC coordinate translation automatically routes around harvested tiles, allowing firmware to use logical coordinates while the NOC hardware maps to physical coordinates. See [NOC Translation and Harvesting](https://deepwiki.com/tenstorrent/tt-system-firmware/4.1-noc-translation-and-harvesting) for details on the translation mechanism.

**Sources:**[lib/tenstorrent/bh_arc/harvesting.h](https://github.com/tenstorrent/tt-system-firmware/blob/ab59b09c/lib/tenstorrent/bh_arc/harvesting.h)[lib/tenstorrent/bh_arc/tensix_init.c 300](https://github.com/tenstorrent/tt-system-firmware/blob/ab59b09c/lib/tenstorrent/bh_arc/tensix_init.c#L300-L300)[lib/tenstorrent/bh_arc/gddr.c 387-393](https://github.com/tenstorrent/tt-system-firmware/blob/ab59b09c/lib/tenstorrent/bh_arc/gddr.c#L387-L393)[lib/tenstorrent/bh_arc/eth.c 450-454](https://github.com/tenstorrent/tt-system-firmware/blob/ab59b09c/lib/tenstorrent/bh_arc/eth.c#L450-L454)

Dismiss
Refresh this wiki

Enter email to refresh
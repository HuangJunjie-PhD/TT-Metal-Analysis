---
project: tt-system-firmware
github: https://github.com/tenstorrent/tt-system-firmware
deepwiki: https://deepwiki.com/tenstorrent/tt-system-firmware/2-firmware-applications
---

# Firmware Applications

Relevant source files
*   [VERSION](https://github.com/tenstorrent/tt-system-firmware/blob/ab59b09c/VERSION)
*   [app/dmc/CMakeLists.txt](https://github.com/tenstorrent/tt-system-firmware/blob/ab59b09c/app/dmc/CMakeLists.txt)
*   [app/dmc/VERSION](https://github.com/tenstorrent/tt-system-firmware/blob/ab59b09c/app/dmc/VERSION)
*   [app/dmc/boards/qemu_riscv32.conf](https://github.com/tenstorrent/tt-system-firmware/blob/ab59b09c/app/dmc/boards/qemu_riscv32.conf)
*   [app/dmc/boards/qemu_riscv32.overlay](https://github.com/tenstorrent/tt-system-firmware/blob/ab59b09c/app/dmc/boards/qemu_riscv32.overlay)
*   [app/dmc/boards/tt_blackhole_tt_blackhole_dmc.overlay](https://github.com/tenstorrent/tt-system-firmware/blob/ab59b09c/app/dmc/boards/tt_blackhole_tt_blackhole_dmc.overlay)
*   [app/dmc/src/main.c](https://github.com/tenstorrent/tt-system-firmware/blob/ab59b09c/app/dmc/src/main.c)
*   [app/smc/VERSION](https://github.com/tenstorrent/tt-system-firmware/blob/ab59b09c/app/smc/VERSION)
*   [app/smc/pytest/e2e_smoke.py](https://github.com/tenstorrent/tt-system-firmware/blob/ab59b09c/app/smc/pytest/e2e_smoke.py)
*   [app/smc/pytest/e2e_stress.py](https://github.com/tenstorrent/tt-system-firmware/blob/ab59b09c/app/smc/pytest/e2e_stress.py)
*   [app/smc/pytest/vuart.py](https://github.com/tenstorrent/tt-system-firmware/blob/ab59b09c/app/smc/pytest/vuart.py)
*   [app/smc/sample.yaml](https://github.com/tenstorrent/tt-system-firmware/blob/ab59b09c/app/smc/sample.yaml)
*   [boards/tenstorrent/tt_blackhole/tt_blackhole_tt_blackhole_smc_galaxy.yaml](https://github.com/tenstorrent/tt-system-firmware/blob/ab59b09c/boards/tenstorrent/tt_blackhole/tt_blackhole_tt_blackhole_smc_galaxy.yaml)
*   [include/tenstorrent/bh_arc.h](https://github.com/tenstorrent/tt-system-firmware/blob/ab59b09c/include/tenstorrent/bh_arc.h)
*   [include/tenstorrent/bh_chip.h](https://github.com/tenstorrent/tt-system-firmware/blob/ab59b09c/include/tenstorrent/bh_chip.h)
*   [include/tenstorrent/jtag_bootrom.h](https://github.com/tenstorrent/tt-system-firmware/blob/ab59b09c/include/tenstorrent/jtag_bootrom.h)
*   [include/tenstorrent/tt_smbus_regs.h](https://github.com/tenstorrent/tt-system-firmware/blob/ab59b09c/include/tenstorrent/tt_smbus_regs.h)
*   [lib/tenstorrent/bh_arc/cm2dm_msg.c](https://github.com/tenstorrent/tt-system-firmware/blob/ab59b09c/lib/tenstorrent/bh_arc/cm2dm_msg.c)
*   [lib/tenstorrent/bh_arc/cm2dm_msg.h](https://github.com/tenstorrent/tt-system-firmware/blob/ab59b09c/lib/tenstorrent/bh_arc/cm2dm_msg.h)
*   [lib/tenstorrent/bh_arc/smbus_target.c](https://github.com/tenstorrent/tt-system-firmware/blob/ab59b09c/lib/tenstorrent/bh_arc/smbus_target.c)
*   [lib/tenstorrent/bh_chip/CMakeLists.txt](https://github.com/tenstorrent/tt-system-firmware/blob/ab59b09c/lib/tenstorrent/bh_chip/CMakeLists.txt)
*   [lib/tenstorrent/bh_chip/bh_arc.c](https://github.com/tenstorrent/tt-system-firmware/blob/ab59b09c/lib/tenstorrent/bh_chip/bh_arc.c)
*   [lib/tenstorrent/bh_chip/bh_chip.c](https://github.com/tenstorrent/tt-system-firmware/blob/ab59b09c/lib/tenstorrent/bh_chip/bh_chip.c)
*   [lib/tenstorrent/bh_chip/strapping.c](https://github.com/tenstorrent/tt-system-firmware/blob/ab59b09c/lib/tenstorrent/bh_chip/strapping.c)
*   [lib/tenstorrent/jtag_bootrom/jtag_bootrom.c](https://github.com/tenstorrent/tt-system-firmware/blob/ab59b09c/lib/tenstorrent/jtag_bootrom/jtag_bootrom.c)
*   [lib/tenstorrent/jtag_bootrom/reset.c](https://github.com/tenstorrent/tt-system-firmware/blob/ab59b09c/lib/tenstorrent/jtag_bootrom/reset.c)
*   [scripts/tooling/attrs.x](https://github.com/tenstorrent/tt-system-firmware/blob/ab59b09c/scripts/tooling/attrs.x)
*   [tests/lib/tenstorrent/bh_arc/testcase.yaml](https://github.com/tenstorrent/tt-system-firmware/blob/ab59b09c/tests/lib/tenstorrent/bh_arc/testcase.yaml)

This page provides an overview of the two primary firmware applications in the tt-system-firmware repository: the System Management Controller (SMC) and the Device Management Controller (DMC). These applications run on separate processors and coordinate to manage Tenstorrent AI accelerator hardware.

For detailed information on individual applications, see [System Management Controller (SMC)](https://deepwiki.com/tenstorrent/tt-system-firmware/2.1-system-management-controller-(smc)) and [Device Management Controller (DMC)](https://deepwiki.com/tenstorrent/tt-system-firmware/2.2-device-management-controller-(dmc)). For information on the underlying hardware subsystems they manage, see [Hardware Subsystems](https://deepwiki.com/tenstorrent/tt-system-firmware/3-hardware-subsystems). For build and deployment details, see [Build System](https://deepwiki.com/tenstorrent/tt-system-firmware/6-build-system).

* * *

## Firmware Application Architecture

The firmware architecture consists of two independently versioned applications running on different processors, coordinating through SMBus-based communication protocols.

**System-Level Firmware Application Relationships**

**Sources:**

*   [app/dmc/src/main.c 1-713](https://github.com/tenstorrent/tt-system-firmware/blob/ab59b09c/app/dmc/src/main.c#L1-L713)
*   [lib/tenstorrent/bh_arc/cm2dm_msg.c 1-442](https://github.com/tenstorrent/tt-system-firmware/blob/ab59b09c/lib/tenstorrent/bh_arc/cm2dm_msg.c#L1-L442)
*   [lib/tenstorrent/bh_arc/smbus_target.c 1-271](https://github.com/tenstorrent/tt-system-firmware/blob/ab59b09c/lib/tenstorrent/bh_arc/smbus_target.c#L1-L271)
*   [app/smc/VERSION 1-6](https://github.com/tenstorrent/tt-system-firmware/blob/ab59b09c/app/smc/VERSION#L1-L6)
*   [app/dmc/VERSION 1-6](https://github.com/tenstorrent/tt-system-firmware/blob/ab59b09c/app/dmc/VERSION#L1-L6)

* * *

## Application Responsibilities

The SMC and DMC applications have distinct but complementary responsibilities in managing the AI accelerator system.

| Responsibility | SMC (ARC Processor) | DMC (RISC-V Processor) |
| --- | --- | --- |
| **Processor** | ARC cores in Blackhole ASIC | RISC-V microcontroller on board |
| **RTOS** | Zephyr RTOS (ARC architecture) | Zephyr RTOS (RISC-V architecture) |
| **Version** | 0.29.99.0 | 0.23.99.0 |
| **Primary Interface** | Host driver via PCIe/SMBus | Board-level peripherals |
| **Message Handling** | 256 command types via message queue | Event-driven architecture |
| **Telemetry** | TAG system with 100ms updates | Reports DMC status to SMC |
| **Power Management** | DVFS, throttling, AICLK arbitration | Board power monitoring via INA228 |
| **Thermal Management** | Temperature monitoring, throttle limits | Fan control (MAX6639), therm trip handling |
| **Reset Control** | Requests resets via CM2DM messages | Executes resets via JTAG and GPIO |
| **Hardware Init** | Manages ASIC subsystems | Loads bootrom, resets chip |
| **Communication** | SMBus target, issues CM2DM requests | SMBus controller, responds to CM2DM |

**Sources:**

*   [app/smc/VERSION 1-6](https://github.com/tenstorrent/tt-system-firmware/blob/ab59b09c/app/smc/VERSION#L1-L6)
*   [app/dmc/VERSION 1-6](https://github.com/tenstorrent/tt-system-firmware/blob/ab59b09c/app/dmc/VERSION#L1-L6)
*   [app/dmc/src/main.c 555-713](https://github.com/tenstorrent/tt-system-firmware/blob/ab59b09c/app/dmc/src/main.c#L555-L713)
*   [lib/tenstorrent/bh_arc/cm2dm_msg.c 54-173](https://github.com/tenstorrent/tt-system-firmware/blob/ab59b09c/lib/tenstorrent/bh_arc/cm2dm_msg.c#L54-L173)

* * *

## Communication Protocol Overview

The SMC and DMC applications communicate bidirectionally through SMBus using the CM2DM (CMFW to DMC) and DM2CM (DMC to CMFW) protocols.

**CM2DM Message Flow**

**CM2DM Message Types**

The CM2DM protocol defines 9 message types for CMFW to request operations from DMC:

**DM2CM SMBus Registers**

DMC communicates back to SMC using specific SMBus registers defined in the CMFW SMBus address space:

| Register | Direction | Purpose |
| --- | --- | --- |
| `CMFW_SMBUS_DM_STATIC_INFO` (0x20) | DMC→SMC | Send DMC version, ARC start time, init duration |
| `CMFW_SMBUS_PING` (0x21) | DMC→SMC | Respond to ping with 0xA5A5 |
| `CMFW_SMBUS_FAN_SPEED` (0x22) | DMC→SMC | Broadcast forced fan speed to all chips |
| `CMFW_SMBUS_FAN_RPM` (0x23) | DMC→SMC | Report actual fan RPM feedback |
| `CMFW_SMBUS_POWER_INSTANT` (0x25) | DMC→SMC | Report current board power |
| `CMFW_SMBUS_THERM_TRIP_COUNT` (0x28) | DMC→SMC | Report thermal trip event count |
| `CMFW_SMBUS_DMC_LOG` (0x29) | DMC→SMC | Stream DMC log data to SMC |

**Sources:**

*   [lib/tenstorrent/bh_arc/cm2dm_msg.c 54-124](https://github.com/tenstorrent/tt-system-firmware/blob/ab59b09c/lib/tenstorrent/bh_arc/cm2dm_msg.c#L54-L124)
*   [lib/tenstorrent/bh_arc/cm2dm_msg.h 1-41](https://github.com/tenstorrent/tt-system-firmware/blob/ab59b09c/lib/tenstorrent/bh_arc/cm2dm_msg.h#L1-L41)
*   [include/tenstorrent/bh_arc.h 15-74](https://github.com/tenstorrent/tt-system-firmware/blob/ab59b09c/include/tenstorrent/bh_arc.h#L15-L74)
*   [include/tenstorrent/tt_smbus_regs.h 16-67](https://github.com/tenstorrent/tt-system-firmware/blob/ab59b09c/include/tenstorrent/tt_smbus_regs.h#L16-L67)
*   [lib/tenstorrent/bh_arc/smbus_target.c 125-263](https://github.com/tenstorrent/tt-system-firmware/blob/ab59b09c/lib/tenstorrent/bh_arc/smbus_target.c#L125-L263)

* * *

## Version Management and Hierarchy

The firmware follows a hierarchical versioning scheme where the product version encompasses independently versioned applications.

**Version Hierarchy**

Each application maintains its own `VERSION` file with the format:

```
VERSION_MAJOR = X
VERSION_MINOR = Y
PATCHLEVEL = Z
VERSION_TWEAK = W
EXTRAVERSION =
```

The version is encoded as a 32-bit integer: `(MAJOR << 24) | (MINOR << 16) | (PATCHLEVEL << 8) | TWEAK`

**Version Verification**

The test suite validates version consistency across firmware updates:

**Sources:**

*   [VERSION 1-6](https://github.com/tenstorrent/tt-system-firmware/blob/ab59b09c/VERSION#L1-L6)
*   [app/smc/VERSION 1-6](https://github.com/tenstorrent/tt-system-firmware/blob/ab59b09c/app/smc/VERSION#L1-L6)
*   [app/dmc/VERSION 1-6](https://github.com/tenstorrent/tt-system-firmware/blob/ab59b09c/app/dmc/VERSION#L1-L6)
*   [app/smc/pytest/e2e_smoke.py 693-704](https://github.com/tenstorrent/tt-system-firmware/blob/ab59b09c/app/smc/pytest/e2e_smoke.py#L693-L704)

* * *

## Firmware Application Lifecycle

Both applications follow a structured boot sequence that coordinates initialization, communication establishment, and runtime operation.

**DMC Application Lifecycle**

The DMC main loop is event-driven, processing events posted by timers, GPIO interrupts, and CM2DM message handlers:

**SMC Application Lifecycle**

The SMC application initialization and runtime is documented in detail in [System Management Controller (SMC)](https://deepwiki.com/tenstorrent/tt-system-firmware/2.1-system-management-controller-(smc)). Key aspects include:

*   Message queue initialization with 256 command handlers
*   Telemetry system setup with TAG registers
*   DVFS and throttling subsystem initialization
*   SMBus target mode configuration for DM2CM communication
*   100ms telemetry update timer

**Sources:**

*   [app/dmc/src/main.c 581-713](https://github.com/tenstorrent/tt-system-firmware/blob/ab59b09c/app/dmc/src/main.c#L581-L713)
*   [app/dmc/src/main.c 555-579](https://github.com/tenstorrent/tt-system-firmware/blob/ab59b09c/app/dmc/src/main.c#L555-L579)
*   [lib/tenstorrent/bh_chip/bh_chip.c 172-190](https://github.com/tenstorrent/tt-system-firmware/blob/ab59b09c/lib/tenstorrent/bh_chip/bh_chip.c#L172-L190)

* * *

## Reset and Recovery Mechanisms

Both applications implement multiple reset mechanisms to handle fault conditions and maintain system availability.

**Reset Types and Handlers**

| Reset Type | Initiator | Handler | Implementation |
| --- | --- | --- | --- |
| **ASIC Reset** | SMC → DMC | `kCm2DmResetLevelAsic` | `bh_chip_reset_chip()` resets chip via JTAG |
| **DMC Reset** | SMC → DMC | `kCm2DmResetLevelDmc` | `sys_reboot(SYS_REBOOT_COLD)` reboots DMC |
| **Watchdog Reset** | DMC timer | Auto-reset timeout expires | DMC reads ARC PC, resets chip |
| **Therm Trip Reset** | GPIO interrupt | THERM_TRIP pin active | DMC asserts reset, sets fan to 100% |
| **PERST (PCIe Reset)** | GPIO interrupt | PRESET_TRIGGER edge | DMC reloads bootrom, resets chip |
| **PGood Recovery** | GPIO interrupt | PGOOD falling edge | DMC waits for rise, then resets chip |

**Watchdog Reset Sequence**

The DMC implements a configurable watchdog that monitors SMC telemetry heartbeat:

The watchdog is configured by SMC via CM2DM message:

**Sources:**

*   [app/dmc/src/main.c 101-120](https://github.com/tenstorrent/tt-system-firmware/blob/ab59b09c/app/dmc/src/main.c#L101-L120)
*   [app/dmc/src/main.c 419-450](https://github.com/tenstorrent/tt-system-firmware/blob/ab59b09c/app/dmc/src/main.c#L419-L450)
*   [app/dmc/src/main.c 452-482](https://github.com/tenstorrent/tt-system-firmware/blob/ab59b09c/app/dmc/src/main.c#L452-L482)
*   [lib/tenstorrent/bh_arc/cm2dm_msg.c 126-133](https://github.com/tenstorrent/tt-system-firmware/blob/ab59b09c/lib/tenstorrent/bh_arc/cm2dm_msg.c#L126-L133)
*   [lib/tenstorrent/bh_arc/cm2dm_msg.c 238-265](https://github.com/tenstorrent/tt-system-firmware/blob/ab59b09c/lib/tenstorrent/bh_arc/cm2dm_msg.c#L238-L265)
*   [lib/tenstorrent/bh_chip/bh_chip.c 128-136](https://github.com/tenstorrent/tt-system-firmware/blob/ab59b09c/lib/tenstorrent/bh_chip/bh_chip.c#L128-L136)

* * *

## Multi-Chip Coordination

The DMC application supports managing multiple Blackhole chips simultaneously, particularly on multi-chip boards like Galaxy (32 chips) and P300 (2 chips).

**Multi-Chip Configuration**

The chip array is initialized from device tree:

**Fan Speed Arbitration**

When managing multiple chips, the DMC selects the highest requested fan speed:

**Sources:**

*   [app/dmc/src/main.c 47](https://github.com/tenstorrent/tt-system-firmware/blob/ab59b09c/app/dmc/src/main.c#L47-L47)
*   [app/dmc/src/main.c 70-99](https://github.com/tenstorrent/tt-system-firmware/blob/ab59b09c/app/dmc/src/main.c#L70-L99)
*   [include/tenstorrent/bh_chip.h 92-147](https://github.com/tenstorrent/tt-system-firmware/blob/ab59b09c/include/tenstorrent/bh_chip.h#L92-L147)

Dismiss
Refresh this wiki

Enter email to refresh
---
project: tt-kmd
github: https://github.com/tenstorrent/tt-kmd
deepwiki: https://deepwiki.com/tenstorrent/tt-kmd/8-hardware-monitoring-and-telemetry
---

# Hardware Monitoring and Telemetry

Relevant source files
*   [blackhole.c](https://github.com/tenstorrent/tt-kmd/blob/0ab170d7/blackhole.c)
*   [blackhole.h](https://github.com/tenstorrent/tt-kmd/blob/0ab170d7/blackhole.h)
*   [docs/sysfs-attributes.md](https://github.com/tenstorrent/tt-kmd/blob/0ab170d7/docs/sysfs-attributes.md?plain=1)
*   [telemetry.c](https://github.com/tenstorrent/tt-kmd/blob/0ab170d7/telemetry.c)
*   [telemetry.h](https://github.com/tenstorrent/tt-kmd/blob/0ab170d7/telemetry.h)
*   [test/hwmon.cpp](https://github.com/tenstorrent/tt-kmd/blob/0ab170d7/test/hwmon.cpp)
*   [wormhole.c](https://github.com/tenstorrent/tt-kmd/blob/0ab170d7/wormhole.c)

This document describes the hardware monitoring subsystem within the Tenstorrent kernel driver, which exposes device telemetry, sensor data, and performance counters through the Linux `hwmon` framework and `sysfs` interfaces. The monitoring system provides real-time access to temperature, power, voltage, current, and PCIe performance metrics from Tenstorrent AI hardware.

## System Architecture

The hardware monitoring system integrates with the Linux `hwmon` subsystem to expose device sensors and performance counters. The architecture is built around a tag-based discovery system where the ARC firmware publishes available telemetry data, and the driver maps these tags to standard kernel interfaces.

### Hardware Monitoring Data Flow

Sources: [telemetry.c 9-33](https://github.com/tenstorrent/tt-kmd/blob/0ab170d7/telemetry.c#L9-L33)[telemetry.c 149-168](https://github.com/tenstorrent/tt-kmd/blob/0ab170d7/telemetry.c#L149-L168)[telemetry.h 71](https://github.com/tenstorrent/tt-kmd/blob/0ab170d7/telemetry.h#L71-L71)[telemetry.h 11-13](https://github.com/tenstorrent/tt-kmd/blob/0ab170d7/telemetry.h#L11-L13)

### Telemetry to Code Entity Mapping

Sources: [telemetry.h 15-41](https://github.com/tenstorrent/tt-kmd/blob/0ab170d7/telemetry.h#L15-L41)[telemetry.c 170-210](https://github.com/tenstorrent/tt-kmd/blob/0ab170d7/telemetry.c#L170-L210)[telemetry.c 35-47](https://github.com/tenstorrent/tt-kmd/blob/0ab170d7/telemetry.c#L35-L47)[telemetry.c 67-93](https://github.com/tenstorrent/tt-kmd/blob/0ab170d7/telemetry.c#L67-L93)

## Telemetry Discovery and Cache

The driver utilizes a discovery protocol to determine which telemetry metrics are supported by the currently running ARC firmware.

### Discovery Process

1.   The driver reads the `ARC_TELEMETRY_PTR` register to find the location of the telemetry metadata in device memory. [wormhole.c 83](https://github.com/tenstorrent/tt-kmd/blob/0ab170d7/wormhole.c#L83-L83)[blackhole.c 60](https://github.com/tenstorrent/tt-kmd/blob/0ab170d7/blackhole.c#L60-L60)
2.   It iterates through the firmware-provided list of tag IDs and their corresponding memory offsets.
3.   The driver populates the `telemetry_tag_cache` (an array of `TELEM_TAG_CACHE_SIZE`). [telemetry.h 11-13](https://github.com/tenstorrent/tt-kmd/blob/0ab170d7/telemetry.h#L11-L13)
4.   If a tag ID is non-zero in the cache, the corresponding `sysfs` or `hwmon` attribute is made visible. [telemetry.c 130-143](https://github.com/tenstorrent/tt-kmd/blob/0ab170d7/telemetry.c#L130-L143)

### Common Telemetry Tags

| Tag Name | Tag ID | Description |
| --- | --- | --- |
| `TELEMETRY_BOARD_ID` | 1 | Unique board identifier |
| `TELEMETRY_VCORE` | 6 | ASIC core voltage |
| `TELEMETRY_POWER` | 7 | Total board power |
| `TELEMETRY_CURRENT` | 8 | Core current |
| `TELEMETRY_ASIC_TEMP` | 11 | ASIC temperature (16.16 fixed-point) |
| `TELEMETRY_AICLK` | 14 | AI clock frequency |
| `TELEMETRY_FAN_RPM` | 41 | Cooling fan speed |

Sources: [telemetry.h 15-41](https://github.com/tenstorrent/tt-kmd/blob/0ab170d7/telemetry.h#L15-L41)

## Sysfs Interface

Telemetry attributes are exposed under `/sys/class/tenstorrent/tenstorrent!<N>/`. The driver uses common show callbacks to format raw telemetry data for user-space.

### Formatting Callbacks

*   `tt_sysfs_show_u32_dec`: Standard decimal output for counters and IDs. [telemetry.c 35-47](https://github.com/tenstorrent/tt-kmd/blob/0ab170d7/telemetry.c#L35-L47)
*   `tt_sysfs_show_u32_ver`: Formats version tags (e.g., `major.minor.patch.ver`). It handles the specific bit-packing used by Ethernet firmware vs. standard ARC firmware. [telemetry.c 67-93](https://github.com/tenstorrent/tt-kmd/blob/0ab170d7/telemetry.c#L67-L93)
*   `tt_sysfs_show_card_type`: Maps numeric card type IDs to human-readable strings like "n150", "p150a", or "galaxy-blackhole". [telemetry.c 95-128](https://github.com/tenstorrent/tt-kmd/blob/0ab170d7/telemetry.c#L95-L128)
*   `tt_sysfs_show_u64_hex`: Combines two 32-bit telemetry tags into a single 64-bit hex string (used for serial numbers). [telemetry.c 49-65](https://github.com/tenstorrent/tt-kmd/blob/0ab170d7/telemetry.c#L49-L65)

### Attribute Visibility

The `tt_sysfs_telemetry_is_visible` function ensures that only telemetry attributes supported by the device's current firmware version appear in the filesystem. [telemetry.c 130-143](https://github.com/tenstorrent/tt-kmd/blob/0ab170d7/telemetry.c#L130-L143)

Sources: [docs/sysfs-attributes.md 33-70](https://github.com/tenstorrent/tt-kmd/blob/0ab170d7/docs/sysfs-attributes.md?plain=1#L33-L70)[telemetry.c 130-143](https://github.com/tenstorrent/tt-kmd/blob/0ab170d7/telemetry.c#L130-L143)

## HWMON Integration

The driver registers with the Linux hardware monitoring (`hwmon`) subsystem, allowing standard tools like `sensors` to monitor the device.

### Data Scaling

The `tt_hwmon_read` function performs the necessary unit conversions from raw firmware values to standard `hwmon` units:

*   **Temperature**: ASIC temperature is stored as 16.16 fixed-point. The driver converts this to millidegrees Celsius. [telemetry.c 184-193](https://github.com/tenstorrent/tt-kmd/blob/0ab170d7/telemetry.c#L184-L193)
*   **Current**: Amperes to milliamperes (x1000). [telemetry.c 194-195](https://github.com/tenstorrent/tt-kmd/blob/0ab170d7/telemetry.c#L194-L195)
*   **Power**: Watts to microwatts (x1000000). [telemetry.c 196-197](https://github.com/tenstorrent/tt-kmd/blob/0ab170d7/telemetry.c#L196-L197)
*   **Voltage**: Reported in millivolts. [telemetry.c 198-202](https://github.com/tenstorrent/tt-kmd/blob/0ab170d7/telemetry.c#L198-L202)

### Labels

Standard labels are provided for sensors via `tt_hwmon_read_string`, mapping sensors to names like `vcore`, `asic_temp`, and `power`. [telemetry.c 212-226](https://github.com/tenstorrent/tt-kmd/blob/0ab170d7/telemetry.c#L212-L226)[test/hwmon.cpp 15-20](https://github.com/tenstorrent/tt-kmd/blob/0ab170d7/test/hwmon.cpp#L15-L20)

Sources: [telemetry.c 170-210](https://github.com/tenstorrent/tt-kmd/blob/0ab170d7/telemetry.c#L170-L210)[telemetry.h 59-71](https://github.com/tenstorrent/tt-kmd/blob/0ab170d7/telemetry.h#L59-L71)

## PCIe Performance Counters

Both Wormhole and Blackhole expose PCIe and NOC (Network on Chip) performance counters in the `pcie_perf_counters` sysfs subdirectory. [docs/sysfs-attributes.md 87-101](https://github.com/tenstorrent/tt-kmd/blob/0ab170d7/docs/sysfs-attributes.md?plain=1#L87-L101)

### Implementation Details

*   **Counters**: These are 32-bit cumulative counters that count events in units of 32-byte flits. [docs/sysfs-attributes.md 113-127](https://github.com/tenstorrent/tt-kmd/blob/0ab170d7/docs/sysfs-attributes.md?plain=1#L113-L127)
*   **Access**: 
    *   **Wormhole**: Counters are read from BAR4 via the Reset Unit/NOC2AXI registers. [wormhole.c 78-81](https://github.com/tenstorrent/tt-kmd/blob/0ab170d7/wormhole.c#L78-L81)
    *   **Blackhole**: Counters are read from the `noc2axi_cfg` mapping at `NOC_STATUS_OFFSET`. [blackhole.c 46-47](https://github.com/tenstorrent/tt-kmd/blob/0ab170d7/blackhole.c#L46-L47)[blackhole.h 16](https://github.com/tenstorrent/tt-kmd/blob/0ab170d7/blackhole.h#L16-L16)

*   **NOC Instances**: Counters are duplicated for both NOC0 and NOC1 (e.g., `mst_rd_data_word_received0` and `mst_rd_data_word_received1`). [docs/sysfs-attributes.md 131-142](https://github.com/tenstorrent/tt-kmd/blob/0ab170d7/docs/sysfs-attributes.md?plain=1#L131-L142)

Sources: [docs/sysfs-attributes.md 87-142](https://github.com/tenstorrent/tt-kmd/blob/0ab170d7/docs/sysfs-attributes.md?plain=1#L87-L142)[blackhole.c 44-49](https://github.com/tenstorrent/tt-kmd/blob/0ab170d7/blackhole.c#L44-L49)[wormhole.c 78-81](https://github.com/tenstorrent/tt-kmd/blob/0ab170d7/wormhole.c#L78-L81)

Dismiss
Refresh this wiki

Enter email to refresh
---
project: tt-flash
github: https://github.com/tenstorrent/tt-flash
deepwiki: https://deepwiki.com/tenstorrent/tt-flash/6-hardware-abstraction-layer
---

# Hardware Abstraction Layer

Relevant source files
*   [tt_flash/chip.py](https://github.com/tenstorrent/tt-flash/blob/60c06032/tt_flash/chip.py)
*   [tt_flash/utility.py](https://github.com/tenstorrent/tt-flash/blob/60c06032/tt_flash/utility.py)

## Purpose and Scope

The Hardware Abstraction Layer (HAL) provides a unified interface for interacting with different Tenstorrent chip architectures. This layer abstracts the differences between Wormhole and Blackhole chips, enabling the flashing engine to operate on both platforms through a common API. The HAL handles chip detection, hardware communication primitives (AXI, SPI, ARC messaging), firmware version retrieval, and board identification.

For details on how the HAL is used during firmware flashing operations, see [Flash Stages](https://deepwiki.com/tenstorrent/tt-flash/5.2-flash-stages). For board-specific reset procedures and platform-specific features, see [Wormhole Platform](https://deepwiki.com/tenstorrent/tt-flash/4.3-wormhole-platform) and [Blackhole Platform](https://deepwiki.com/tenstorrent/tt-flash/4.4-blackhole-platform).

* * *

## Class Hierarchy

The HAL is organized around an abstract base class with concrete implementations for each supported chip architecture:

**Sources:**[tt_flash/chip.py 113-388](https://github.com/tenstorrent/tt-flash/blob/60c06032/tt_flash/chip.py#L113-L388)

### TTChip Abstract Base Class

`TTChip` is the abstract base class that defines the common interface for all chip types. It wraps a `pyluwen.PciChip` object and provides higher-level abstractions.

| Component | Purpose |
| --- | --- |
| `luwen_chip: PciChip` | The underlying pyluwen device driver interface |
| `interface_id: int` | PCI interface identifier for device enumeration |
| `fw_defines: dict` | Firmware message type definitions loaded from `fw_defines.yaml` |
| `telmetry_cache: Telemetry` | Cached telemetry data to avoid redundant hardware queries |

Key responsibilities:

*   Wrapping `pyluwen.PciChip` operations
*   Managing telemetry caching
*   Providing version extraction utilities
*   Defining abstract methods for chip-specific behavior

**Sources:**[tt_flash/chip.py 113-239](https://github.com/tenstorrent/tt-flash/blob/60c06032/tt_flash/chip.py#L113-L239)

### WhChip Implementation

`WhChip` provides Wormhole-specific implementations of abstract methods.

**Minimum Firmware Version:**`0x2170000` - Earlier firmware versions lack bundled firmware support.

**Bundle Version Retrieval:** Uses `get_bundle_version_v1()` which queries firmware via ARC messages with different `arg0` values:

*   `arg0=0`: Base firmware version
*   `arg0=1`: Running bundle version
*   `arg0=2`: SPI-stored bundle version

**Sources:**[tt_flash/chip.py 297-306](https://github.com/tenstorrent/tt-flash/blob/60c06032/tt_flash/chip.py#L297-L306)[tt_flash/chip.py 29-92](https://github.com/tenstorrent/tt-flash/blob/60c06032/tt_flash/chip.py#L29-L92)

### BhChip Implementation

`BhChip` provides Blackhole-specific implementations with enhanced capabilities.

**Minimum Firmware Version:**`0x0` - All Blackhole firmware versions support required features.

**Bundle Version Retrieval:** Uses telemetry for running version and `decode_boot_fs_table("cmfwcfg")` for SPI version, providing more reliable access than ARC messages.

**ASIC Location Detection:** For P300 boards with dual ASICs, `get_asic_location()` determines whether the chip is the left (0) or right (1) ASIC. Primary method reads telemetry; fallback method reads GPIO strap register at `0x80030D20`.

**Sources:**[tt_flash/chip.py 241-295](https://github.com/tenstorrent/tt-flash/blob/60c06032/tt_flash/chip.py#L241-L295)

* * *

## Chip Detection and Initialization

**Sources:**[tt_flash/chip.py 308-374](https://github.com/tenstorrent/tt-flash/blob/60c06032/tt_flash/chip.py#L308-L374)

### detect_local_chips()

The primary chip detection function used during flashing operations.

**Function Signature:**

`detect_local_chips(ignore_ethernet: bool = False) -> list[Union[WhChip, BhChip]]`
**Parameters:**

| Parameter | Type | Purpose |
| --- | --- | --- |
| `ignore_ethernet` | `bool` | When `True`, disables NOC safety checks for faster detection on systems with broken Ethernet links |

**Process:**

1.   Calls `pyluwen.detect_chips_fallible()` with `local_only=True` to enumerate PCI devices
2.   Validates communication with each device using `device.have_comms()`
3.   Calls `device.force_upgrade()` to obtain full device capabilities
4.   Identifies chip type using `device.as_wh()` or `device.as_bh()`
5.   Wraps each device in the appropriate `WhChip` or `BhChip` object
6.   Provides real-time progress updates via `chip_detect_callback`

**Progress Callback:** Updates terminal with detected chip count and status messages when running in TTY mode, controlled by `sys.stdout.isatty()`.

**Sources:**[tt_flash/chip.py 308-374](https://github.com/tenstorrent/tt-flash/blob/60c06032/tt_flash/chip.py#L308-L374)

### detect_chips()

Simplified detection function for verification operations that don't require interactive progress reporting.

**Function Signature:**

`detect_chips(local_only: bool = False) -> list[Union[WhChip, BhChip]]`
Uses `pyluwen.detect_chips()` (without `_fallible` suffix) for simpler enumeration without progress callbacks.

**Sources:**[tt_flash/chip.py 377-387](https://github.com/tenstorrent/tt-flash/blob/60c06032/tt_flash/chip.py#L377-L387)

### Chip Re-initialization

After firmware updates or reset operations, chips must be re-initialized to refresh communication channels and cached state.

**Method:**`TTChip.reinit(callback=None)`

**Process:**

1.   Creates new `PciChip` object using stored `interface_id`
2.   Clears telemetry cache
3.   Calls `luwen_chip.init()` with optional progress callback
4.   Handles chip re-enumeration and Ethernet link establishment

**Sources:**[tt_flash/chip.py 122-161](https://github.com/tenstorrent/tt-flash/blob/60c06032/tt_flash/chip.py#L122-L161)

* * *

## Hardware Communication Interfaces

The HAL provides four primary communication mechanisms with the hardware:

**Sources:**[tt_flash/chip.py 205-230](https://github.com/tenstorrent/tt-flash/blob/60c06032/tt_flash/chip.py#L205-L230)

### AXI Bus Operations

AXI (Advanced eXtensible Interface) provides direct access to chip memory-mapped registers and memory regions.

**Write Operations:**

`def axi_write32(self, addr: int, value: int)def axi_write(self, addr: int, data: bytes)`
| Method | Purpose | Usage |
| --- | --- | --- |
| `axi_write32` | Write single 32-bit value | Register configuration, control flags |
| `axi_write` | Write arbitrary byte array | Memory region initialization, bulk data |

**Read Operations:**

`def axi_read32(self, addr: int) -> intdef axi_read(self, addr: int, size: int) -> bytes`
| Method | Purpose | Usage |
| --- | --- | --- |
| `axi_read32` | Read single 32-bit value | Status register polling, configuration readback |
| `axi_read` | Read arbitrary byte range | Memory dumps, verification |

**Example Usage:** Blackhole ASIC location detection reads GPIO strap register at `0x80030D20` using `axi_read32()`.

**Sources:**[tt_flash/chip.py 205-218](https://github.com/tenstorrent/tt-flash/blob/60c06032/tt_flash/chip.py#L205-L218)

### SPI Flash Operations

SPI (Serial Peripheral Interface) operations access the external flash memory where firmware images are stored.

**Write Operation:**

`def spi_write(self, addr: int, data: bytes)`
Used during firmware flashing to write image data to specific flash offsets. Writes are typically followed by read-back verification.

**Read Operation:**

`def spi_read(self, addr: int, size: int) -> bytes`
Used for:

*   Verification after write operations
*   Reading existing firmware for version checks
*   Extracting flash contents during verification commands

**Sources:**[tt_flash/chip.py 220-227](https://github.com/tenstorrent/tt-flash/blob/60c06032/tt_flash/chip.py#L220-L227)

### ARC Messaging

ARC (Argon RISC Core) messaging provides a request-response interface with the on-chip ARC processor.

**Method:**

`def arc_msg(self, *args, **kwargs)`
This is a thin wrapper around `pyluwen.PciChip.arc_msg()`. Messages are identified by type codes defined in `fw_defines.yaml`.

**Common Message Types:**

| Message Type | Purpose | Arguments |
| --- | --- | --- |
| `MSG_TYPE_FW_VERSION` | Query firmware versions | `arg0`: version selector (0=FW, 1=running bundle, 2=SPI bundle) |
| `MSG_TYPE_ARC_STATE` | Query ARC processor state | Used for boot monitoring |
| `MSG_TRIGGER_SPI_COPY_LtoR` | Copy SPI from local to remote | NEBULA_X2 dual-chip synchronization |
| `MSG_CONFIRM_FLASHED_SPI` | Cryptographic flash validation | Blackhole firmware v19+ post-flash validation |

**Wormhole Bundle Version Query Example:**[tt_flash/chip.py 42-82](https://github.com/tenstorrent/tt-flash/blob/60c06032/tt_flash/chip.py#L42-L82) demonstrates multi-phase version queries using different `arg0` values.

**Sources:**[tt_flash/chip.py 229-230](https://github.com/tenstorrent/tt-flash/blob/60c06032/tt_flash/chip.py#L229-L230)[tt_flash/chip.py 42-82](https://github.com/tenstorrent/tt-flash/blob/60c06032/tt_flash/chip.py#L42-L82)

### Telemetry Interface

Telemetry provides structured access to hardware sensors, version information, and operational state.

**Methods:**

`def get_telemetry(self) -> Telemetrydef get_telemetry_unchanged(self) -> Telemetry`
| Method | Behavior | Use Case |
| --- | --- | --- |
| `get_telemetry()` | Refreshes from hardware, updates cache | When fresh data is required |
| `get_telemetry_unchanged()` | Returns cached data, queries if cache empty | Multiple reads of same snapshot |

**Telemetry-Derived Properties:**

`def m3_fw_app_version(self) -> tuple[int, int, int, int]def smbus_fw_version(self) -> tuple[int, int, int, int]def arc_l2_fw_version(self) -> tuple[int, int, int, int]def get_asic_location(self) -> int`
These methods extract and decode specific fields from the `Telemetry` object, converting raw version numbers to semantic version tuples `(component, major, minor, patch)`.

**Blackhole Bundle Version Extraction:**[tt_flash/chip.py 254-260](https://github.com/tenstorrent/tt-flash/blob/60c06032/tt_flash/chip.py#L254-L260) reads `telem.fw_bundle_version` and decodes it into component version tuple.

**Sources:**[tt_flash/chip.py 163-201](https://github.com/tenstorrent/tt-flash/blob/60c06032/tt_flash/chip.py#L163-L201)

* * *

## Firmware Version Retrieval

**Sources:**[tt_flash/chip.py 29-92](https://github.com/tenstorrent/tt-flash/blob/60c06032/tt_flash/chip.py#L29-L92)[tt_flash/chip.py 248-275](https://github.com/tenstorrent/tt-flash/blob/60c06032/tt_flash/chip.py#L248-L275)

### FwVersion Dataclass

The `FwVersion` dataclass encapsulates firmware version information with error handling:

`@dataclassclass FwVersion:    allow_exception: bool    exception: Optional[Exception]    running: Optional[tuple[int, int, int, int]]    spi: Optional[tuple[int, int, int, int]]`
| Field | Type | Purpose |
| --- | --- | --- |
| `allow_exception` | `bool` | Indicates whether version retrieval failures should be tolerated |
| `exception` | `Optional[Exception]` | Captured exception if version retrieval failed |
| `running` | `Optional[tuple]` | Currently executing firmware version as `(component, major, minor, patch)` |
| `spi` | `Optional[tuple]` | Firmware version stored in SPI flash as `(component, major, minor, patch)` |

**Version Tuple Format:** Each version is a 4-element tuple where:

*   `component`: Component identifier (typically 0 for main firmware)
*   `major`: Major version number
*   `minor`: Minor version number
*   `patch`: Patch version number

**Sources:**[tt_flash/chip.py 21-27](https://github.com/tenstorrent/tt-flash/blob/60c06032/tt_flash/chip.py#L21-L27)

### Wormhole Version Retrieval (get_bundle_version_v1)

Wormhole chips use ARC messaging with a multi-stage query process implemented in `get_bundle_version_v1()`:

**Stage 1: Base Firmware Check**

*   Query: `arc_msg(MSG_TYPE_FW_VERSION, arg0=0)`
*   Returns: Base firmware version
*   Validates: Must be >= `min_fw_version()` (0x2170000) for bundle support

**Stage 2: Running Bundle Version**

*   Query: `arc_msg(MSG_TYPE_FW_VERSION, arg0=1)`
*   Returns: 32-bit packed version or `0xFFFFFFFF`/`0xDEAD` on error
*   Decoding: `(temp >> 24) & 0xFF` = component, `(temp >> 16) & 0xFF` = major, etc.

**Stage 3: SPI Bundle Version**

*   Query: `arc_msg(MSG_TYPE_FW_VERSION, arg0=2)`
*   Returns: Version stored in flash
*   Validation: Skipped if running version equals firmware version (detects buggy firmware)

**Sources:**[tt_flash/chip.py 29-92](https://github.com/tenstorrent/tt-flash/blob/60c06032/tt_flash/chip.py#L29-L92)[tt_flash/chip.py 304-305](https://github.com/tenstorrent/tt-flash/blob/60c06032/tt_flash/chip.py#L304-L305)

### Blackhole Version Retrieval

Blackhole chips use more reliable telemetry-based version retrieval:

**Running Version:**

*   Source: `get_telemetry_unchanged().fw_bundle_version`
*   Decoding: Same bit-packing as Wormhole: `(temp >> 24) & 0xFF` for component, etc.

**SPI Version:**

*   Source: `luwen_chip.decode_boot_fs_table("cmfwcfg")`
*   Accesses: Boot filesystem table configuration section
*   Field: `cmfwcfg["fw_bundle_version"]`

This approach is more reliable than ARC messaging because:

*   Telemetry is refreshed automatically during chip communication
*   Boot filesystem decode provides direct access to structured flash data
*   No dependency on ARC message protocol versioning

**Sources:**[tt_flash/chip.py 248-275](https://github.com/tenstorrent/tt-flash/blob/60c06032/tt_flash/chip.py#L248-L275)

* * *

## Board Identification System

**Sources:**[tt_flash/utility.py 39-131](https://github.com/tenstorrent/tt-flash/blob/60c06032/tt_flash/utility.py#L39-L131)

### Board ID Structure

Board IDs are 64-bit values with the following structure:

| Bit Range | Field | Purpose |
| --- | --- | --- |
| 56-63 | AA | Reserved |
| 36-55 | BBBBB (UPI) | Unique Part Identifier - primary board type selector |
| 32-35 | C (Revision) | Board revision number |
| 28-31 | D | Reserved |
| 16-27 | EE | Reserved |
| 8-15 | FF | Reserved |
| 0-7 | XXX | Reserved |

The UPI field is the primary identifier for board type determination.

**Sources:**[tt_flash/utility.py 39-59](https://github.com/tenstorrent/tt-flash/blob/60c06032/tt_flash/utility.py#L39-L59)

### get_board_type() - Codename Resolution

The `get_board_type()` function maps Board IDs to internal codenames:

`def get_board_type(board_id: int, from_type: bool = False) -> Optional[str]`
**Parameters:**

| Parameter | Type | Purpose |
| --- | --- | --- |
| `board_id` | `int` | 64-bit Board ID or UPI value |
| `from_type` | `bool` | If `True`, treats `board_id` as raw UPI; if `False`, extracts UPI from full Board ID |

**UPI-to-Codename Mapping:**

| UPI (hex) | Codename | Notes |
| --- | --- | --- |
| 0x1 | E300_R2 / E300_R3 | Requires revision check |
| 0x3 | E300_105 |  |
| 0x7 | E75 |  |
| 0x8 | NEBULA_CB |  |
| 0xA | E300_X2 |  |
| 0xB | GALAXY |  |
| 0x14 | NEBULA_X2 |  |
| 0x18 | NEBULA_X1 |  |
| 0x35 | WH_UBB |  |
| 0x36 | P100-1 |  |
| 0x40 | P150A-1 |  |
| 0x41 | P150B-1 |  |
| 0x42 | P150C-1 |  |
| 0x43 | P100A-1 |  |
| 0x44 | P300B-1 |  |
| 0x45 | P300A-1 |  |
| 0x46 | P300C-1 |  |
| 0x47 | GALAXY-1 |  |

**E300 Special Case:** UPI `0x1` requires checking the revision field:

*   Revision `0x2` → E300_R2
*   Revision `0x3` or `0x4` → E300_R3

**Sources:**[tt_flash/utility.py 39-106](https://github.com/tenstorrent/tt-flash/blob/60c06032/tt_flash/utility.py#L39-L106)

### change_to_public_name() - Display Name Mapping

The `change_to_public_name()` function translates internal codenames to user-friendly display names:

`def change_to_public_name(codename: str) -> str`
**Codename-to-Public Name Mapping:**

| Internal Codename | Public Display Name |
| --- | --- |
| E300_105 | e150 |
| E300_X2 | e300 |
| E75 | e75 |
| NEBULA_X1 | n150 |
| NEBULA_X2 | n300 |
| WH_UBB | Galaxy Wormhole |
| P100-1 | p100 |
| P150A-1 | p150a |
| P150B-1 | p150b |
| P150C-1 | p150c |
| P300A/B/C | p300 |
| GALAXY-1 | Galaxy Blackhole |

**Fallback Behavior:** If no mapping exists, returns the original codename unchanged.

**Sources:**[tt_flash/utility.py 108-131](https://github.com/tenstorrent/tt-flash/blob/60c06032/tt_flash/utility.py#L108-L131)

### Version String Utilities

The HAL includes utilities for converting between version representations:

**Semantic Version to Hex:**

`def semver_to_hex(semver: str) -> str`
Converts "10.15.1" → "0x0A0F0100" for parameter handling.

**Hex to Semantic Version:**

`def hex_to_semver(hexsemver: int) -> str`
Converts `0x0A0F0100` → "10.15.1" for display.

**Date Encoding:**

`def date_to_hex(date: int) -> str`
Converts "YYYYMMDDHHMM" → "0xYMDDHHMM" where Y is year offset from 2020.

**Date Decoding:**

`def hex_to_date(hexdate: int) -> str`
Converts "0xYMDDHHMM" → "YYYY-MM-DD HH:MM" formatted string.

**Sources:**[tt_flash/utility.py 133-168](https://github.com/tenstorrent/tt-flash/blob/60c06032/tt_flash/utility.py#L133-L168)

* * *

## Integration with Pyluwen

All hardware operations ultimately flow through the `pyluwen` library (~0.8.0), which provides the low-level device driver interface:

**Key Pyluwen Components Used:**

| Component | Purpose in HAL |
| --- | --- |
| `pyluwen.PciChip` | Wrapped by `TTChip.luwen_chip` for all hardware operations |
| `pyluwen.detect_chips_fallible()` | Used by `detect_local_chips()` with progress reporting |
| `pyluwen.detect_chips()` | Used by `detect_chips()` for simple enumeration |
| `pyluwen.Telemetry` | Returned by `get_telemetry()` methods |

**Version Requirement:** The HAL requires `pyluwen ~0.8.0` as specified in `pyproject.toml`. Version compatibility is critical because pyluwen evolves alongside firmware protocol changes.

**Sources:**[tt_flash/chip.py 13-15](https://github.com/tenstorrent/tt-flash/blob/60c06032/tt_flash/chip.py#L13-L15) High-Level Diagram 5

Dismiss
Refresh this wiki

Enter email to refresh
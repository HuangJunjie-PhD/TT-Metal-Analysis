---
project: tt-flash
github: https://github.com/tenstorrent/tt-flash
deepwiki: https://deepwiki.com/tenstorrent/tt-flash/5-flashing-operations
---

# Flashing Operations

Relevant source files
*   [README.md](https://github.com/tenstorrent/tt-flash/blob/60c06032/README.md?plain=1)
*   [tt_flash/flash.py](https://github.com/tenstorrent/tt-flash/blob/60c06032/tt_flash/flash.py)

This document provides a deep dive into the firmware flashing process in tt-flash. It covers the internal mechanisms, data structures, and algorithms used to safely update firmware on Tenstorrent devices. For information about supported hardware platforms, see [Supported Hardware](https://deepwiki.com/tenstorrent/tt-flash/4-supported-hardware). For command-line usage, see [Command Reference](https://deepwiki.com/tenstorrent/tt-flash/3-command-reference).

## Overview

Flashing operations in tt-flash follow a multi-stage pipeline implemented primarily in [tt_flash/flash.py](https://github.com/tenstorrent/tt-flash/blob/60c06032/tt_flash/flash.py) The main entry point is the `flash_chips()` function at [tt_flash/flash.py 792-958](https://github.com/tenstorrent/tt-flash/blob/60c06032/tt_flash/flash.py#L792-L958) which orchestrates the entire process across multiple devices. The system is designed with safety as a priority, implementing version checking, write verification with retries, and platform-specific reset procedures.

The flashing process consists of four distinct stages:

| Stage | Purpose | Key Functions |
| --- | --- | --- |
| SETUP | Load and validate firmware bundle | `verify_package()`[tt_flash/flash.py 663-697](https://github.com/tenstorrent/tt-flash/blob/60c06032/tt_flash/flash.py#L663-L697) |
| DETECT | Enumerate PCI devices | `detect_chips()` from `chip.py` |
| FLASH | Version check, write, and verify | `flash_chip_stage1()`[tt_flash/flash.py 194-492](https://github.com/tenstorrent/tt-flash/blob/60c06032/tt_flash/flash.py#L194-L492)`flash_chip_stage2()`[tt_flash/flash.py 495-650](https://github.com/tenstorrent/tt-flash/blob/60c06032/tt_flash/flash.py#L495-L650) |
| RESET | Apply platform-specific resets | `WHChipReset.full_lds_reset()`, `BHChipReset.full_lds_reset()`, `glx_6u_trays_reset()`[tt_flash/flash.py 738-789](https://github.com/tenstorrent/tt-flash/blob/60c06032/tt_flash/flash.py#L738-L789) |

**Sources:**[tt_flash/flash.py 792-958](https://github.com/tenstorrent/tt-flash/blob/60c06032/tt_flash/flash.py#L792-L958)

* * *

## Firmware Bundle Format

Firmware bundles use the `.fwbundle` extension and are standard tar archives containing board-specific firmware images and metadata. The structure is validated by `verify_package()` at [tt_flash/flash.py 663-697](https://github.com/tenstorrent/tt-flash/blob/60c06032/tt_flash/flash.py#L663-L697)

### Bundle Structure

### manifest.json Structure

The manifest contains bundle-level metadata parsed in [tt_flash/flash.py 672-681](https://github.com/tenstorrent/tt-flash/blob/60c06032/tt_flash/flash.py#L672-L681):

`{  "bundle_version": {    "fwId": 19,    "releaseId": 2,    "patch": 0,    "debug": 0  },  "format_version": [2, 0, 0],  "supported_boards": ["NEBULA_X1", "NEBULA_X2", "WH_UBB", ...]}`
The `bundle_version` tuple is extracted as `(fwId, releaseId, patch, debug)` and stored in the `Manifest` dataclass at [tt_flash/flash.py 654-656](https://github.com/tenstorrent/tt-flash/blob/60c06032/tt_flash/flash.py#L654-L656)

### image.bin Format

For Blackhole chips, `image.bin` uses Intel HEX-like format parsed at [tt_flash/flash.py 378-389](https://github.com/tenstorrent/tt-flash/blob/60c06032/tt_flash/flash.py#L378-L389):

```
@00000000
AABBCCDDEEFF...
@00001000
1122334455...
```

Lines starting with `@` specify the address offset, followed by lines of hex-encoded data. For Wormhole chips, the same format is parsed at [tt_flash/flash.py 428-454](https://github.com/tenstorrent/tt-flash/blob/60c06032/tt_flash/flash.py#L428-L454)

### mask.json Format

The mask file defines parameter regions that require special handling. Structure validated at [tt_flash/flash.py 398-424](https://github.com/tenstorrent/tt-flash/blob/60c06032/tt_flash/flash.py#L398-L424):

`[  {    "start": 4096,    "end": 4100,    "tag": "rmw"  },  {    "start": 8192,    "end": 8196,    "tag": "incr"  }]`
Each entry specifies a byte range `<FileRef file-url="https://github.com/tenstorrent/tt-flash/blob/60c06032/start, end)` and a handler tag from `TAG_HANDLERS`.\n\n**Sources#LNaN-LNaN" NaN file-path="start, end)`and a handler tag from`TAG_HANDLERS`.\n\n**Sources">Hii [tt_flash/flash.py 336-366](https://github.com/tenstorrent/tt-flash/blob/60c06032/tt_flash/flash.py#L336-L366)[tt_flash/flash.py 369-393](https://github.com/tenstorrent/tt-flash/blob/60c06032/tt_flash/flash.py#L369-L393)

* * *

## Flash Stages

The flashing process is divided into two primary stages executed per-chip, coordinated by `flash_chips()`.

### Stage 1: Validation and Preparation

The function `flash_chip_stage1()` at [tt_flash/flash.py 194-492](https://github.com/tenstorrent/tt-flash/blob/60c06032/tt_flash/flash.py#L194-L492) performs these operations:

1.   **ARC State Query**: Sends `MSG_TYPE_ARC_STATE3` message to wake up the ARC processor [tt_flash/flash.py 216-221](https://github.com/tenstorrent/tt-flash/blob/60c06032/tt_flash/flash.py#L216-L221)
2.   **Version Retrieval**: Calls `chip.get_bundle_version()` to get both SPI and running versions [tt_flash/flash.py 223](https://github.com/tenstorrent/tt-flash/blob/60c06032/tt_flash/flash.py#L223-L223)
3.   **Version Normalization**: Converts legacy `(80, major, minor, patch)` format to `(major, minor, patch, 0)` using `normalize_fw_version()`[tt_flash/flash.py 127-139](https://github.com/tenstorrent/tt-flash/blob/60c06032/tt_flash/flash.py#L127-L139)
4.   **Exception Handling**: Checks if version retrieval raised exceptions; on Wormhole this is allowed with `--force`, on Blackhole it's fatal [tt_flash/flash.py 230-246](https://github.com/tenstorrent/tt-flash/blob/60c06032/tt_flash/flash.py#L230-L246)
5.   **Version Comparison**: Compares normalized versions; permits upgrades across one major version boundary [tt_flash/flash.py 263-289](https://github.com/tenstorrent/tt-flash/blob/60c06032/tt_flash/flash.py#L263-L289)
6.   **Image Extraction**: Extracts `{boardname}/image.bin` and `{boardname}/mask.json` from tarfile [tt_flash/flash.py 336-366](https://github.com/tenstorrent/tt-flash/blob/60c06032/tt_flash/flash.py#L336-L366)
7.   **Parameter Application**: Applies handlers from `TAG_HANDLERS` dict to modify firmware parameters [tt_flash/flash.py 437-444](https://github.com/tenstorrent/tt-flash/blob/60c06032/tt_flash/flash.py#L437-L444)

**Sources:**[tt_flash/flash.py 194-492](https://github.com/tenstorrent/tt-flash/blob/60c06032/tt_flash/flash.py#L194-L492)

### Stage 2: Write and Verify

The function `flash_chip_stage2()` at [tt_flash/flash.py 495-650](https://github.com/tenstorrent/tt-flash/blob/60c06032/tt_flash/flash.py#L495-L650) implements:

1.   **SIGINT Protection**: Installs a signal handler to prevent interruption during critical writes [tt_flash/flash.py 499-501](https://github.com/tenstorrent/tt-flash/blob/60c06032/tt_flash/flash.py#L499-L501)
2.   **Write Phase**: Calls `chip.spi_write()` for each `FlashWrite` object [tt_flash/flash.py 508-509](https://github.com/tenstorrent/tt-flash/blob/60c06032/tt_flash/flash.py#L508-L509)
3.   **Verify Phase**: Reads back data with `chip.spi_read()` and compares byte-by-byte [tt_flash/flash.py 518-529](https://github.com/tenstorrent/tt-flash/blob/60c06032/tt_flash/flash.py#L518-L529)
4.   **Retry Logic**: On mismatch, reports first error location and count, then retries write+verify once [tt_flash/flash.py 561-611](https://github.com/tenstorrent/tt-flash/blob/60c06032/tt_flash/flash.py#L561-L611)
5.   **NEBULA_X2 Special Case**: Triggers `MSG_TRIGGER_SPI_COPY_LtoR` to copy from local to remote SPI [tt_flash/flash.py 620-648](https://github.com/tenstorrent/tt-flash/blob/60c06032/tt_flash/flash.py#L620-L648)
6.   **Boot Loop Mitigation**: For M3 version 5.8.0.1, disables auto-reset before triggering copy [tt_flash/flash.py 625-639](https://github.com/tenstorrent/tt-flash/blob/60c06032/tt_flash/flash.py#L625-L639)

**Sources:**[tt_flash/flash.py 495-650](https://github.com/tenstorrent/tt-flash/blob/60c06032/tt_flash/flash.py#L495-L650)

* * *

## Version Checking and Compatibility

Version checking is critical to prevent unsafe downgrades and ensure firmware compatibility.

### Version Normalization

The `normalize_fw_version()` function at [tt_flash/flash.py 127-139](https://github.com/tenstorrent/tt-flash/blob/60c06032/tt_flash/flash.py#L127-L139) handles legacy format:

`def normalize_fw_version(version: Optional[tuple[int, int, int, int]]) -> Optional[tuple[int, int, int, int]]:    """    Old FW bundles used format 80.major.minor.patch.    FW version switched at major version 18 from 80.18.X.X -> 18.X.X.        If version[0] == 80, return (major, minor, patch, 0).    Otherwise, just return the version.    """    if version is None:        return None    if version[0] == 80:        return (version[1], version[2], version[3], 0)    return version`
This transformation is applied to:

*   `fw_bundle_version.spi` - version in SPI flash
*   `fw_bundle_version.running` - currently running version
*   `manifest.bundle_version` - version in new bundle

All at [tt_flash/flash.py 226-228](https://github.com/tenstorrent/tt-flash/blob/60c06032/tt_flash/flash.py#L226-L228)

### Version Comparison Logic

Key version checks at [tt_flash/flash.py 263-333](https://github.com/tenstorrent/tt-flash/blob/60c06032/tt_flash/flash.py#L263-L333):

1.   **Major Downgrade**: Requires `--allow-major-downgrades` flag [tt_flash/flash.py 263-273](https://github.com/tenstorrent/tt-flash/blob/60c06032/tt_flash/flash.py#L263-L273)
2.   **Major Upgrade**: Warns if upgrading across one major version (e.g., 18.x → 19.x) [tt_flash/flash.py 274-279](https://github.com/tenstorrent/tt-flash/blob/60c06032/tt_flash/flash.py#L274-L279)
3.   **Multi-Major Jump**: Requires `--force` flag for upgrades/downgrades across >1 major version [tt_flash/flash.py 280-289](https://github.com/tenstorrent/tt-flash/blob/60c06032/tt_flash/flash.py#L280-L289)
4.   **SPI Check**: If SPI version ≥ manifest version, skip flash (already up-to-date) [tt_flash/flash.py 301-318](https://github.com/tenstorrent/tt-flash/blob/60c06032/tt_flash/flash.py#L301-L318)
5.   **Running Check**: If no SPI version available, use running version [tt_flash/flash.py 320-330](https://github.com/tenstorrent/tt-flash/blob/60c06032/tt_flash/flash.py#L320-L330)

**Sources:**[tt_flash/flash.py 127-139](https://github.com/tenstorrent/tt-flash/blob/60c06032/tt_flash/flash.py#L127-L139)[tt_flash/flash.py 226-333](https://github.com/tenstorrent/tt-flash/blob/60c06032/tt_flash/flash.py#L226-L333)

* * *

## Parameter Handling and Masks

Parameter handling allows dynamic modification of firmware data before writing to SPI. The system uses a tag-based handler architecture defined in `TAG_HANDLERS` at [tt_flash/flash.py 142-148](https://github.com/tenstorrent/tt-flash/blob/60c06032/tt_flash/flash.py#L142-L148)

### TAG_HANDLERS Dictionary

`TAG_HANDLERS: dict[str, Callable[[TTChip, bytearray, int, int, int], bytearray]] = {    "rmw": rmw_param,    "incr": incr_param,    "date": date_param,    "flash_version": flash_version,    "bundle_version": bundle_version,}`
Each handler has signature: `(chip, data, spi_addr, data_addr, len) -> bytearray`

### Handler Implementations

### Handler Details

| Tag | Function | Purpose | Line Range |
| --- | --- | --- | --- |
| `rmw` | `rmw_param()` | Read-Modify-Write: preserve existing SPI data | [tt_flash/flash.py 33-42](https://github.com/tenstorrent/tt-flash/blob/60c06032/tt_flash/flash.py#L33-L42) |
| `incr` | `incr_param()` | Increment existing value by 1, used for counters | [tt_flash/flash.py 45-62](https://github.com/tenstorrent/tt-flash/blob/60c06032/tt_flash/flash.py#L45-L62) |
| `date` | `date_param()` | Write current date in hex format 0xYYYYMMDD | [tt_flash/flash.py 65-74](https://github.com/tenstorrent/tt-flash/blob/60c06032/tt_flash/flash.py#L65-L74) |
| `flash_version` | `flash_version()` | Embed tt-flash tool version | [tt_flash/flash.py 77-97](https://github.com/tenstorrent/tt-flash/blob/60c06032/tt_flash/flash.py#L77-L97) |
| `bundle_version` | `bundle_version()` | Embed firmware bundle version | [tt_flash/flash.py 105-124](https://github.com/tenstorrent/tt-flash/blob/60c06032/tt_flash/flash.py#L105-L124) |

### Parameter Application Flow

For Wormhole chips, parameters are applied during image parsing at [tt_flash/flash.py 437-448](https://github.com/tenstorrent/tt-flash/blob/60c06032/tt_flash/flash.py#L437-L448):

`for (start, end), handler in param_handlers:    if start < curr_stop and end > curr_addr:        # chip, data, spi_addr, data_addr, len        if not isinstance(data, bytearray):            data = bytearray(data)        data = handler(            chip, data, start, start - curr_addr, end - start        )`
The handler modifies the `data` bytearray in-place for the region `<FileRef file-url="https://github.com/tenstorrent/tt-flash/blob/60c06032/start, end)` before creating the `FlashWrite` object.\n\nFor Blackhole chips, parameters are handled by `boot_fs_write()` in [tt_flash/blackhole.py" undefined file-path="start, end)`before creating the`FlashWrite`object.\n\nFor Blackhole chips, parameters are handled by`boot_fs_write()` in [tt_flash/blackhole.py">Hii before being passed to stage 2.

**Sources:**[tt_flash/flash.py 33-124](https://github.com/tenstorrent/tt-flash/blob/60c06032/tt_flash/flash.py#L33-L124)[tt_flash/flash.py 142-148](https://github.com/tenstorrent/tt-flash/blob/60c06032/tt_flash/flash.py#L142-L148)[tt_flash/flash.py 437-448](https://github.com/tenstorrent/tt-flash/blob/60c06032/tt_flash/flash.py#L437-L448)

* * *

## Write and Verify Cycle

The write-verify cycle implements a robust two-attempt strategy with detailed error reporting.

### Cycle Implementation

### Write Function

The `perform_write()` local function at [tt_flash/flash.py 503-511](https://github.com/tenstorrent/tt-flash/blob/60c06032/tt_flash/flash.py#L503-L511):

1.   **Save Signal Handler**: Store original SIGINT handler [tt_flash/flash.py 504](https://github.com/tenstorrent/tt-flash/blob/60c06032/tt_flash/flash.py#L504-L504)
2.   **Install Protection**: Replace with handler that prints warning and blocks [tt_flash/flash.py 505](https://github.com/tenstorrent/tt-flash/blob/60c06032/tt_flash/flash.py#L505-L505)
3.   **Execute Writes**: Loop through `FlashWrite` list calling `chip.spi_write()`[tt_flash/flash.py 508-509](https://github.com/tenstorrent/tt-flash/blob/60c06032/tt_flash/flash.py#L508-L509)
4.   **Restore Handler**: Always restore original handler in finally block [tt_flash/flash.py 511](https://github.com/tenstorrent/tt-flash/blob/60c06032/tt_flash/flash.py#L511-L511)

### Verify Function

The `perform_verify()` local function at [tt_flash/flash.py 513-533](https://github.com/tenstorrent/tt-flash/blob/60c06032/tt_flash/flash.py#L513-L533):

1.   **Read Back Data**: For each write region, call `chip.spi_read()`[tt_flash/flash.py 519](https://github.com/tenstorrent/tt-flash/blob/60c06032/tt_flash/flash.py#L519-L519)
2.   **Byte Comparison**: Compare read data with expected write data [tt_flash/flash.py 521](https://github.com/tenstorrent/tt-flash/blob/60c06032/tt_flash/flash.py#L521-L521)
3.   **Error Analysis**: On mismatch, find first error offset and count total mismatches [tt_flash/flash.py 522-529](https://github.com/tenstorrent/tt-flash/blob/60c06032/tt_flash/flash.py#L522-L529)
4.   **Return Status**: Return `(first_mismatch, mismatch_count)` tuple or `None` for success [tt_flash/flash.py 529](https://github.com/tenstorrent/tt-flash/blob/60c06032/tt_flash/flash.py#L529-L529)

### Retry Logic

On verification failure at [tt_flash/flash.py 561-611](https://github.com/tenstorrent/tt-flash/blob/60c06032/tt_flash/flash.py#L561-L611):

1.   **Report First Failure**: Print colored "failed" message with mismatch details [tt_flash/flash.py 567-570](https://github.com/tenstorrent/tt-flash/blob/60c06032/tt_flash/flash.py#L567-L570)
2.   **Second Write Attempt**: Call `perform_write()` again [tt_flash/flash.py 583](https://github.com/tenstorrent/tt-flash/blob/60c06032/tt_flash/flash.py#L583-L583)
3.   **Second Verify Attempt**: Call `perform_verify()` again [tt_flash/flash.py 599](https://github.com/tenstorrent/tt-flash/blob/60c06032/tt_flash/flash.py#L599-L599)
4.   **Fatal on Second Failure**: Print error message instructing user not to reset [tt_flash/flash.py 606-610](https://github.com/tenstorrent/tt-flash/blob/60c06032/tt_flash/flash.py#L606-L610)

### TTY-Aware Output

The code uses `CConfig.is_tty()` to provide enhanced output in interactive terminals:

`if CConfig.is_tty():    print("\t\t\tWriting new firmware... ", end="", flush=True)else:    print("\t\t\tWriting new firmware... ")`
This allows overwriting progress messages with `\r\033<FileRef file-url="https://github.com/tenstorrent/tt-flash/blob/60c06032/K` (carriage return + clear line) in TTY mode.\n\n**Sources#LNaN-LNaN" NaN file-path="K` (carriage return + clear line) in TTY mode.\n\n**Sources">Hii

* * *

## Reset Procedures

Reset procedures are platform-specific and handle post-flash device initialization. The logic is in `flash_chips()` at [tt_flash/flash.py 890-951](https://github.com/tenstorrent/tt-flash/blob/60c06032/tt_flash/flash.py#L890-L951)

### Reset Decision Tree

### Reset Eligibility

Chips are added to `needs_reset_wh` or `needs_reset_bh` lists based on:

| Board Type | Can Auto-Reset | Conditions | Line Range |
| --- | --- | --- | --- |
| NEBULA_X1, NEBULA_X2 | Maybe | M3 ≥ 5.5.0.0 AND ARC L2 ≥ 2.12.0.0 AND SMBus ≥ 2.12.0.0 | [tt_flash/flash.py 459-479](https://github.com/tenstorrent/tt-flash/blob/60c06032/tt_flash/flash.py#L459-L479) |
| BhChip (any) | Yes | Always | [tt_flash/flash.py 480-481](https://github.com/tenstorrent/tt-flash/blob/60c06032/tt_flash/flash.py#L480-L481) |
| WH_UBB | Yes | Always | [tt_flash/flash.py 482-483](https://github.com/tenstorrent/tt-flash/blob/60c06032/tt_flash/flash.py#L482-L483) |
| Other WH | No | Cannot auto-reset | [tt_flash/flash.py 484-485](https://github.com/tenstorrent/tt-flash/blob/60c06032/tt_flash/flash.py#L484-L485) |

### Galaxy Reset Procedure

The `glx_6u_trays_reset()` function at [tt_flash/flash.py 738-789](https://github.com/tenstorrent/tt-flash/blob/60c06032/tt_flash/flash.py#L738-L789) handles Galaxy system resets:

1.   **IPMI Command**: Call `run_wh_ubb_ipmi_reset()` with bitmap parameters [tt_flash/flash.py 760](https://github.com/tenstorrent/tt-flash/blob/60c06032/tt_flash/flash.py#L760-L760)
2.   **Wait for Reset**: 30 second countdown with live progress [tt_flash/flash.py 761](https://github.com/tenstorrent/tt-flash/blob/60c06032/tt_flash/flash.py#L761-L761)
3.   **Driver Wait**: Call `run_ubb_wait_for_driver_load()`[tt_flash/flash.py 762](https://github.com/tenstorrent/tt-flash/blob/60c06032/tt_flash/flash.py#L762-L762)
4.   **Re-detect**: Enumerate chips with `detect_chips_with_callback()`[tt_flash/flash.py 777](https://github.com/tenstorrent/tt-flash/blob/60c06032/tt_flash/flash.py#L777-L777)
5.   **ETH Link Check**: For full reset (ubb_num=0xF), validate Ethernet links [tt_flash/flash.py 782-783](https://github.com/tenstorrent/tt-flash/blob/60c06032/tt_flash/flash.py#L782-L783)

The function accepts bitmap parameters:

*   `ubb_num`: UBB slots to reset (0x0-0xF bitmap)
*   `dev_num`: Devices within UBB (0x0-0xFF bitmap)
*   `op_mode`: 0x0=toggle, 0x1=assert, 0x2=deassert
*   `reset_time`: Duration in 10ms units (0xF = 150ms)

### Standard Chip Reset

For non-Galaxy systems at [tt_flash/flash.py 927-936](https://github.com/tenstorrent/tt-flash/blob/60c06032/tt_flash/flash.py#L927-L936):

1.   **Wormhole Reset**: `WHChipReset().full_lds_reset(pci_interfaces=needs_reset_wh, reset_m3=True)`[tt_flash/flash.py 928-930](https://github.com/tenstorrent/tt-flash/blob/60c06032/tt_flash/flash.py#L928-L930)
2.   **Blackhole Reset**: `BHChipReset().full_lds_reset(pci_interfaces=needs_reset_bh, reset_m3=True, m3_delay=m3_delay)`[tt_flash/flash.py 932-936](https://github.com/tenstorrent/tt-flash/blob/60c06032/tt_flash/flash.py#L932-L936)

The `m3_delay` parameter is adaptive [tt_flash/flash.py 893-900](https://github.com/tenstorrent/tt-flash/blob/60c06032/tt_flash/flash.py#L893-L900):

*   **20 seconds**: Normal case (same major version)
*   **60 seconds**: Major version upgrade detected (allows longer M3 boot time)

### Post-Flash Validation

For Blackhole chips with firmware v19+ at [tt_flash/flash.py 942-951](https://github.com/tenstorrent/tt-flash/blob/60c06032/tt_flash/flash.py#L942-L951):

1.   **Generate Challenge**: Random 16-bit value [tt_flash/flash.py 944](https://github.com/tenstorrent/tt-flash/blob/60c06032/tt_flash/flash.py#L944-L944)
2.   **Send Message**: `arc_msg(MSG_CONFIRM_FLASHED_SPI, arg0=check_val)`[tt_flash/flash.py 946](https://github.com/tenstorrent/tt-flash/blob/60c06032/tt_flash/flash.py#L946-L946)
3.   **Verify Response**: Check if response matches challenge value [tt_flash/flash.py 949](https://github.com/tenstorrent/tt-flash/blob/60c06032/tt_flash/flash.py#L949-L949)
4.   **Warn on Failure**: Print yellow warning suggesting manual reset [tt_flash/flash.py 950-951](https://github.com/tenstorrent/tt-flash/blob/60c06032/tt_flash/flash.py#L950-L951)

This cryptographic challenge-response confirms the new firmware is actually running from SPI.

**Sources:**[tt_flash/flash.py 738-789](https://github.com/tenstorrent/tt-flash/blob/60c06032/tt_flash/flash.py#L738-L789)[tt_flash/flash.py 890-951](https://github.com/tenstorrent/tt-flash/blob/60c06032/tt_flash/flash.py#L890-L951)

* * *

## Data Structures

### FlashWrite

Defined at [tt_flash/flash.py 20-21](https://github.com/tenstorrent/tt-flash/blob/60c06032/tt_flash/flash.py#L20-L21) and imported from `tt_flash.blackhole`:

`@dataclassclass FlashWrite:    offset: int    write: bytes`
Represents a single contiguous write operation to SPI flash.

### FlashData

Defined at [tt_flash/flash.py 173-177](https://github.com/tenstorrent/tt-flash/blob/60c06032/tt_flash/flash.py#L173-L177):

`@dataclassclass FlashData:    write: list[FlashWrite]    name: str       # Public display name    idname: str     # Internal codename`
Contains all write operations for a single chip, along with board identification.

### FlashStageResult

Defined at [tt_flash/flash.py 180-191](https://github.com/tenstorrent/tt-flash/blob/60c06032/tt_flash/flash.py#L180-L191):

`class FlashStageResultState(Enum):    Ok = auto()    NoFlash = auto()    Err = auto() @dataclassclass FlashStageResult:    state: FlashStageResultState    can_reset: bool    msg: str    data: Optional[FlashData]`
Return value from `flash_chip_stage1()` indicating whether flash should proceed.

### Manifest

Defined at [tt_flash/flash.py 653-656](https://github.com/tenstorrent/tt-flash/blob/60c06032/tt_flash/flash.py#L653-L656):

`@dataclassclass Manifest:    data: dict    bundle_version: tuple[int, int, int, int]`
Contains parsed manifest.json data and extracted version tuple.

**Sources:**[tt_flash/flash.py 173-191](https://github.com/tenstorrent/tt-flash/blob/60c06032/tt_flash/flash.py#L173-L191)[tt_flash/flash.py 653-656](https://github.com/tenstorrent/tt-flash/blob/60c06032/tt_flash/flash.py#L653-L656)[tt_flash/blackhole.py](https://github.com/tenstorrent/tt-flash/blob/60c06032/tt_flash/blackhole.py)

* * *

## Error Handling

### TTError Usage

The system raises `TTError` (from `tt_flash.error`) for recoverable errors:

| Error Condition | Location | Message Template |
| --- | --- | --- |
| Version check failure | [tt_flash/flash.py 239-241](https://github.com/tenstorrent/tt-flash/blob/60c06032/tt_flash/flash.py#L239-L241) | "Hit error X while trying to determine running firmware..." |
| Major downgrade without flag | [tt_flash/flash.py 270-273](https://github.com/tenstorrent/tt-flash/blob/60c06032/tt_flash/flash.py#L270-L273) | "Detected major version downgrade from X to Y..." |
| Major version mismatch | [tt_flash/flash.py 286-289](https://github.com/tenstorrent/tt-flash/blob/60c06032/tt_flash/flash.py#L286-L289) | "Bundle fwId does not match expected fwId..." |
| Missing board firmware | [tt_flash/flash.py 356-358](https://github.com/tenstorrent/tt-flash/blob/60c06032/tt_flash/flash.py#L356-L358) | "Could not find flash data for X in tarfile" |
| Invalid mask format | [tt_flash/flash.py 408-410](https://github.com/tenstorrent/tt-flash/blob/60c06032/tt_flash/flash.py#L408-L410) | "Invalid mask format for X..." |
| Unknown mask tag | [tt_flash/flash.py 418-424](https://github.com/tenstorrent/tt-flash/blob/60c06032/tt_flash/flash.py#L418-L424) | "Invalid tag X for Y..." |
| Manifest not found | [tt_flash/flash.py 669-671](https://github.com/tenstorrent/tt-flash/blob/60c06032/tt_flash/flash.py#L669-L671) | "Could not find manifest in fw package..." |
| Unsupported manifest version | [tt_flash/flash.py 686-688](https://github.com/tenstorrent/tt-flash/blob/60c06032/tt_flash/flash.py#L686-L688) | "Unsupported manifest version X..." |
| Galaxy link errors | [tt_flash/flash.py 733-735](https://github.com/tenstorrent/tt-flash/blob/60c06032/tt_flash/flash.py#L733-L735) | "Galaxy Ethernet link errors detected" |

### Non-Fatal Warnings

The system prints warnings but continues operation for:

*   Post-flash validation failure (BH v19+): Yellow warning at [tt_flash/flash.py 950-951](https://github.com/tenstorrent/tt-flash/blob/60c06032/tt_flash/flash.py#L950-L951)
*   Failed automatic reset on NEBULA boards: Blue note at [tt_flash/flash.py 476-478](https://github.com/tenstorrent/tt-flash/blob/60c06032/tt_flash/flash.py#L476-L478)
*   MSG_TRIGGER_SPI_COPY_LtoR failure: Blue note at [tt_flash/flash.py 645-648](https://github.com/tenstorrent/tt-flash/blob/60c06032/tt_flash/flash.py#L645-L648)

**Sources:**[tt_flash/flash.py 239-735](https://github.com/tenstorrent/tt-flash/blob/60c06032/tt_flash/flash.py#L239-L735)

Dismiss
Refresh this wiki

Enter email to refresh
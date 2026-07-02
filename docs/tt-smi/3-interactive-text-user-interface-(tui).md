---
project: tt-smi
github: https://github.com/tenstorrent/tt-smi
deepwiki: https://deepwiki.com/tenstorrent/tt-smi/3-interactive-text-user-interface-(tui)
---

# Interactive Text User Interface (TUI)

Relevant source files
*   [tt_smi/constants.py](https://github.com/tenstorrent/tt-smi/blob/a2791bfe/tt_smi/constants.py)
*   [tt_smi/tt_smi.py](https://github.com/tenstorrent/tt-smi/blob/a2791bfe/tt_smi/tt_smi.py)
*   [tt_smi/tt_smi_style.css](https://github.com/tenstorrent/tt-smi/blob/a2791bfe/tt_smi/tt_smi_style.css)

## Purpose and Scope

This document describes the Interactive Text User Interface (TUI) component of TT-SMI, which provides a real-time, terminal-based monitoring interface for Tenstorrent devices. The TUI is built using the Textual framework and displays device information, telemetry, and firmware versions in a tabbed interface with live updates.

For command-line interface operations, see [Command-Line Interface](https://deepwiki.com/tenstorrent/tt-smi/2-command-line-interface). For details on the backend data collection system that feeds the TUI, see [Backend System (TTSMIBackend)](https://deepwiki.com/tenstorrent/tt-smi/6.2-backend-system-(ttsmibackend)). For information about the data models used in logging, see [Data Models and Logging](https://deepwiki.com/tenstorrent/tt-smi/6.4-data-models-and-logging).

* * *

## TUI Architecture Overview

The TUI is implemented as a single Textual application class (`TTSMI`) that orchestrates multiple widgets, manages a background telemetry update thread, and handles user interactions through keyboard shortcuts. The architecture follows a three-layer pattern: presentation (widgets), application logic (TTSMI class), and data management (TTSMIBackend).

**Sources:**[tt_smi/tt_smi.py 67-575](https://github.com/tenstorrent/tt-smi/blob/a2791bfe/tt_smi/tt_smi.py#L67-L575)[tt_smi/constants.py 163-227](https://github.com/tenstorrent/tt-smi/blob/a2791bfe/tt_smi/constants.py#L163-L227)

* * *

## Application Structure (TTSMI Class)

The `TTSMI` class extends `textual.app.App` and serves as the main application controller. It is initialized with a `TTSMIBackend` instance and configuration options, then manages the lifecycle of the TUI.

### Class Initialization

| Parameter | Type | Default | Description |
| --- | --- | --- | --- |
| `result_filename` | `str` | `None` | Path for snapshot output when saving logs |
| `app_name` | `str` | `"TT-SMI"` | Application name displayed in header |
| `app_version` | `str` | From package metadata | Version string displayed in header |
| `key_bindings` | `TextualKeyBindings` | `[]` | Additional key bindings to register |
| `backend` | `TTSMIBackend` | Required | Backend instance providing device data |
| `snapshot` | `bool` | `False` | Whether to save logs on exit |
| `show_sidebar` | `bool` | `True` | Whether to display the left sidebar |

### Key Bindings

The application defines seven primary key bindings through the `BINDINGS` class attribute:

| Key(s) | Action | Description |
| --- | --- | --- |
| `q, Q` | `action_quit()` | Exit the application and save logs if snapshot mode enabled |
| `h, H` | `action_help()` | Display the help menu overlay |
| `d, D` | `app.toggle_dark` | Toggle dark mode (built-in Textual action) |
| `c, C` | `action_toggle_compact()` | Toggle visibility of left sidebar |
| `1` | `action_tab_one()` | Switch to Device Information tab |
| `2` | `action_tab_two()` | Switch to Telemetry tab |
| `3` | `action_tab_three()` | Switch to Firmware Version tab |

**Sources:**[tt_smi/tt_smi.py 67-79](https://github.com/tenstorrent/tt-smi/blob/a2791bfe/tt_smi/tt_smi.py#L67-L79)[tt_smi/tt_smi.py 509-548](https://github.com/tenstorrent/tt-smi/blob/a2791bfe/tt_smi/tt_smi.py#L509-L548)

* * *

## Layout and Widget Composition

The TUI uses a grid-based layout with three primary regions: header (full width), left sidebar (optional), and tabbed content area. The layout is defined in the `compose()` method and styled via CSS files.

### Widget Hierarchy

### Table Headers

Each tab displays a `TTDataTable` with specific column headers defined in `constants.py`:

#### Device Information Tab Headers

`INFO_TABLE_HEADER = [    "#", "Bus ID", "Board Type", "Board ID", "Coords",    "DRAM Trained", "DRAM Speed", "Link Speed", "Link Width"]`
#### Telemetry Tab Headers

`TELEMETRY_TABLE_HEADER = [    "#", "Core Voltage (V)", "Core Current (A)", "AICLK (MHz)",    "Core Power (W)", "Core Temp (°C)", "Fan Speed (%)", "Heartbeat"]`
#### Firmware Version Tab Headers

`FIRMWARES_TABLE_HEADER = [    "#", "FW Bundle Version", "TT-Flash Version", "CM FW Version",    "CM FW Date", "ETH FW Version", "BM BL Version", "BM App Version"]`
### CSS Styling

The layout is styled using two CSS files:

*   `common_style.css` from `tt_tools_common.ui_common` (shared across TT tools)
*   `tt_smi_style.css` for TT-SMI-specific styles

Key style rules:

| Selector | Properties | Purpose |
| --- | --- | --- |
| `#app_grid` | `layout: grid`, `grid-gutter: 1 1` | Grid-based main container |
| `#left_col` | `width: 40`, `dock: left` | Fixed-width left sidebar |
| `#tab_container` | `overflow: auto auto` | Scrollable tab content |
| `TabPane` | `width: 100%`, `overflow: auto hidden` | Full-width tabs with horizontal scroll |
| `DataTable` | `text-align: center` | Center-aligned table cells |

**Sources:**[tt_smi/tt_smi.py 114-146](https://github.com/tenstorrent/tt-smi/blob/a2791bfe/tt_smi/tt_smi.py#L114-L146)[tt_smi/constants.py 166-198](https://github.com/tenstorrent/tt-smi/blob/a2791bfe/tt_smi/constants.py#L166-L198)[tt_smi/tt_smi_style.css 1-113](https://github.com/tenstorrent/tt-smi/blob/a2791bfe/tt_smi/tt_smi_style.css#L1-L113)

* * *

## Telemetry Threading Model

The TUI implements a background thread to continuously update telemetry data when the Telemetry tab is active. This provides near-real-time monitoring without blocking the UI.

### Thread Lifecycle

### Thread Implementation Details

The telemetry thread is managed through three global variables:

| Variable | Type | Purpose |
| --- | --- | --- |
| `INTERRUPT_RECEIVED` | `bool` | Signal flag to terminate all threads |
| `TELEM_THREADS` | `List[Thread]` | List of active telemetry threads |
| `RUNNING_TELEM_THREAD` | `bool` | Prevents creating multiple telemetry threads |

The thread executes an infinite loop with the following logic:

1.   **Check interrupt flag**: If `INTERRUPT_RECEIVED` is `True`, exit loop
2.   **Update telemetry**: Call `update_telem_table()` which fetches fresh data from backend
3.   **Format data**: Convert raw telemetry to styled `Text` objects with color coding
4.   **Update display**: Refresh the data table widget
5.   **Sleep**: Wait `GUI_INTERVAL_TIME` (0.1 seconds) before next iteration

`# Simplified thread logic from tt_smi.py:556-560def update_telem():    global INTERRUPT_RECEIVED    while not INTERRUPT_RECEIVED:        self.update_telem_table()        time.sleep(constants.GUI_INTERVAL_TIME)`
**Sources:**[tt_smi/tt_smi.py 54-58](https://github.com/tenstorrent/tt-smi/blob/a2791bfe/tt_smi/tt_smi.py#L54-L58)[tt_smi/tt_smi.py 550-575](https://github.com/tenstorrent/tt-smi/blob/a2791bfe/tt_smi/tt_smi.py#L550-L575)[tt_smi/constants.py 159](https://github.com/tenstorrent/tt-smi/blob/a2791bfe/tt_smi/constants.py#L159-L159)

* * *

## Data Formatting and Display

The TUI transforms raw backend data into styled, human-readable table rows using three formatting methods. Each method applies conditional styling based on data values and limits.

### Device Information Formatting (`format_device_info_rows`)

This method formats static device information from `backend.device_infos` and `backend.pci_properties`. Key formatting logic:

**Color Scheme:**

*   **Green (`text_green`)**: Normal/optimal values
*   **Yellow (`yellow_bold`)**: Index numbers, limit denominators
*   **Red (`attention`)**: Values below expected (PCIe width/speed), DRAM not trained
*   **Gray**: N/A or unavailable data

**Sources:**[tt_smi/tt_smi.py 318-507](https://github.com/tenstorrent/tt-smi/blob/a2791bfe/tt_smi/tt_smi.py#L318-L507)

### Telemetry Formatting (`format_telemetry_rows`)

This method formats real-time telemetry data and compares values against chip limits. The telemetry update frequency is 0.1 seconds (`GUI_INTERVAL_TIME`).

**Conditional Formatting Rules:**

| Field | Comparison Logic | Display Format |
| --- | --- | --- |
| `voltage` | `val < chip_limits[board_num]['vdd_max']` | `{val} / {vdd_max}` (green if under, red if over) |
| `current` | `val < chip_limits[board_num]['tdc_limit']` | `{val} / {tdc_limit}` (green if under, red if over) |
| `power` | `val < chip_limits[board_num]['tdp_limit']` | `{val} / {tdp_limit}` (green if under, red if over) |
| `aiclk` | `val < chip_limits[board_num]['asic_fmax']` | `{val} / {asic_fmax}` (green if under, red if over) |
| `asic_temperature` | `val < chip_limits[board_num]['thm_limit']` | `{val} / {thm_limit}` (green if under, red if over) |
| `fan_speed` | `0 < val <= 100` | `{val} / 100` (green if valid, gray "N/A" if invalid) |
| `heartbeat` | Always valid | Animated spinner: `●∙∙ → ∙●∙ → ∙∙● → ∙●∙` |

**Heartbeat Spinner Logic:**

The heartbeat field displays a rotating spinner to indicate device liveness:

`# From tt_smi.py:304-316def get_heartbeat_spinner(self, input_secs: Union[int, str]) -> str:    symbols = ["●∙∙", "∙●∙", "∙∙●", "∙●∙"]    cur_symbol = int(input_secs) % len(symbols)    return symbols[cur_symbol]`
The input is a monotonically increasing seconds counter, providing visual confirmation that telemetry is updating.

**Sources:**[tt_smi/tt_smi.py 196-302](https://github.com/tenstorrent/tt-smi/blob/a2791bfe/tt_smi/tt_smi.py#L196-L302)[tt_smi/tt_smi.py 304-316](https://github.com/tenstorrent/tt-smi/blob/a2791bfe/tt_smi/tt_smi.py#L304-L316)

### Firmware Version Formatting (`format_firmware_rows`)

This method formats firmware version information from `backend.firmware_infos`. The logic is simpler than other formatters:

*   If firmware version is `"N/A"`: Display in gray
*   Otherwise: Display in green

The firmware versions are sourced from different components depending on board type:

| Firmware Type | Wormhole Source | Blackhole Source |
| --- | --- | --- |
| `fw_bundle_version` | SMBus: `FW_BUNDLE_VERSION` | TAG: `TAG_FLASH_BUNDLE_VERSION` |
| `tt_flash_version` | SMBus: `TT_FLASH_VERSION` | N/A |
| `cm_fw` | SMBus: `ARC0_FW_VERSION` | TAG: `TAG_CM_FW_VERSION` |
| `eth_fw` | SMBus: `ETH_FW_VERSION` | TAG: `TAG_ETH_FW_VERSION` |
| `bm_bl_fw` | SMBus: `M3_BL_FW_VERSION` | TAG: `TAG_BM_BL_FW_VERSION` |
| `bm_app_fw` | SMBus: `M3_APP_FW_VERSION` | TAG: `TAG_BM_APP_FW_VERSION` |

**Sources:**[tt_smi/tt_smi.py 178-194](https://github.com/tenstorrent/tt-smi/blob/a2791bfe/tt_smi/tt_smi.py#L178-L194)[tt_smi/constants.py 123-131](https://github.com/tenstorrent/tt-smi/blob/a2791bfe/tt_smi/constants.py#L123-L131)

* * *

## Widget Initialization and Mounting

The TUI initializes widgets in two phases: composition (`compose()`) and mounting (`on_mount()`). The mounting phase populates data tables and configures widget properties.

### Mount Sequence

**Key Configuration:**

*   **Cursor type**: Set to `"none"` for all data tables to prevent row selection highlighting
*   **Sidebar display**: Controlled by `show_sidebar` parameter (can be toggled with `c` key or `--compact` CLI flag)
*   **Initial data**: All three tables are pre-populated before the UI renders

**Sources:**[tt_smi/tt_smi.py 148-164](https://github.com/tenstorrent/tt-smi/blob/a2791bfe/tt_smi/tt_smi.py#L148-L164)

* * *

## Help Menu System

The TUI provides a contextual help menu accessible via the `h` or `H` key. The help content is displayed as a modal overlay using the `TTHelperMenuBox` widget.

### Help Menu Content

The help text is defined as markdown in `constants.HELP_MENU_MARKDOWN` and includes:

1.   **Application description**: Brief explanation of TT-SMI's purpose
2.   **Keyboard shortcuts table**: All available key bindings and their functions

`# TT-SMI HELP MENU TT-SMI is a command-line utility that allows users to look at the telemetry and device information of Tenstorrent devices. ## KEYBOARD SHORTCUTS |            Action            |    Key           |                     Detailed Description                     || :--------------------------: | :--------------: | :----------------------------------------------------------: ||             Quit             |   q  |        Exit the program       ||             Help             |   h   |                   Opens up this help menu                   ||   Go to device(s) info tab  |        1        |          Switch to tab with device info         ||   Go to device(s) telemetry tab     |        2        |          Switch to tab with telemetry info that is updated every 100ms           ||   Go to device(s) firmware tab     |        3        |          Switch to tab with all the fw versions on the board(s)          |`
The help menu is rendered as a modal screen that:

*   Pauses telemetry updates (gracefully handles `NoMatches` exception in update thread)
*   Displays formatted markdown with proper styling
*   Closes when user presses any key or clicks outside the box

**Sources:**[tt_smi/constants.py 209-226](https://github.com/tenstorrent/tt-smi/blob/a2791bfe/tt_smi/constants.py#L209-L226)[tt_smi/tt_smi.py 543-548](https://github.com/tenstorrent/tt-smi/blob/a2791bfe/tt_smi/tt_smi.py#L543-L548)

* * *

## Compact Mode

The TUI supports a "compact mode" that hides the left sidebar (`TTHostCompatibilityMenu`) to maximize space for data tables. This is useful on smaller terminal windows or when focusing on device data.

### Toggle Mechanism

Compact mode can be activated in three ways:

1.   **Command-line flag**: `tt-smi --compact` or `tt-smi -c`
2.   **Keyboard shortcut**: Press `c` or `C` while the TUI is running
3.   **Programmatic**: Pass `show_sidebar=False` to `TTSMI` constructor

**Implementation:**

`# Initialization - tt_smi.py:99, 163self.show_sidebar = show_sidebarleft_sidebar.display = self.show_sidebar # Toggle action - tt_smi.py:509-512def action_toggle_compact(self) -> None:    left_sidebar = self.query_one("#left_col")    left_sidebar.display = not left_sidebar.display`
**Sources:**[tt_smi/tt_smi.py 99](https://github.com/tenstorrent/tt-smi/blob/a2791bfe/tt_smi/tt_smi.py#L99-L99)[tt_smi/tt_smi.py 162-163](https://github.com/tenstorrent/tt-smi/blob/a2791bfe/tt_smi/tt_smi.py#L162-L163)[tt_smi/tt_smi.py 509-512](https://github.com/tenstorrent/tt-smi/blob/a2791bfe/tt_smi/tt_smi.py#L509-L512)[tt_smi/tt_smi.py 618-623](https://github.com/tenstorrent/tt-smi/blob/a2791bfe/tt_smi/tt_smi.py#L618-L623)

* * *

## Snapshot Mode Integration

When the TUI is launched in snapshot mode (`--snapshot` or `-f` flags), it integrates with the logging system to save device state on exit. The snapshot mode is transparent to the user during normal operation but triggers additional behavior during shutdown.

### Snapshot Workflow

**Exit Message Examples:**

Without snapshot mode:

```
Exiting TT-SMI.
```

With snapshot mode:

```
Exiting TT-SMI.
Saved tt-smi log to: /home/user/tt_smi/2024-01-15_14-30-22_snapshot.json
```

**Sources:**[tt_smi/tt_smi.py 514-526](https://github.com/tenstorrent/tt-smi/blob/a2791bfe/tt_smi/tt_smi.py#L514-L526)[tt_smi/tt_smi.py 594-599](https://github.com/tenstorrent/tt-smi/blob/a2791bfe/tt_smi/tt_smi.py#L594-L599)[tt_smi/tt_smi.py 725-731](https://github.com/tenstorrent/tt-smi/blob/a2791bfe/tt_smi/tt_smi.py#L725-L731)

* * *

## Styling and Theming

The TUI uses a combination of CSS styling and programmatic color themes to achieve a consistent visual appearance across the interface.

### CSS Architecture

The application loads two CSS files through the `CSS_PATH` class attribute:

`# tt_smi.py:80-89CSS_PATH = [    f"{common_style_file_path}",  # From tt_tools_common.ui_common    "tt_smi_style.css"            # TT-SMI specific styles]`
### Key CSS Selectors and Rules

| Selector | Key Properties | Purpose |
| --- | --- | --- |
| `#app_grid` | `layout: grid` `grid-gutter: 1 1` | Main container with grid layout and spacing |
| `#left_col` | `width: 40` `column-span: 1` `dock: left` | Fixed-width left sidebar docked to left edge |
| `#tab_container` | `overflow: auto auto` | Enable scrolling for tab content |
| `TabPane` | `width: 100%` `overflow: auto hidden` | Full-width tabs with horizontal scrolling only |
| `DataTable` | `text-align: center` | Center-align all table cell content |
| `DataTable > .datatable--header-hover` | `background: 0%` `color: white 0%` | Disable header hover effects |
| `#help_menu_box` | `width: 110` `height: 33` `border: solid $green` | Help menu modal dimensions and styling |

### Programmatic Color Theme

The TUI uses the `create_tt_tools_theme()` function from `tt_tools_common` to generate a color scheme dictionary:

`# tt_smi.py:109self.text_theme = create_tt_tools_theme()`
**Color Keys Used in TT-SMI:**

| Theme Key | Usage | Context |
| --- | --- | --- |
| `yellow_bold` | Index numbers, limit denominators | Device indices, max values in comparisons |
| `text_green` | Normal/optimal values | Telemetry within limits, available firmware |
| `attention` | Warning/suboptimal values | Telemetry exceeding limits, reduced PCIe performance |
| `gray` | N/A or unavailable data | Missing values, uninitialized hardware |
| `red_bold` | Critical errors | Failed PCIe links, critical failures |
| `purple` | Success messages | Log file save confirmations |

**Example Usage:**

`# From tt_smi.py:216-221Text(    f"{val}",    style=self.text_theme["text_green"] if float(val) < float(vdd_max)           else self.text_theme["attention"],    justify="center",)`
**Sources:**[tt_smi/tt_smi.py 80-89](https://github.com/tenstorrent/tt-smi/blob/a2791bfe/tt_smi/tt_smi.py#L80-L89)[tt_smi/tt_smi.py 109](https://github.com/tenstorrent/tt-smi/blob/a2791bfe/tt_smi/tt_smi.py#L109-L109)[tt_smi/tt_smi_style.css 1-113](https://github.com/tenstorrent/tt-smi/blob/a2791bfe/tt_smi/tt_smi_style.css#L1-L113)

* * *

## Exception Handling in Update Loop

The telemetry update thread includes graceful exception handling to prevent crashes when widgets are temporarily unavailable, such as when the help menu overlay is displayed.

### NoMatches Exception Pattern

`# From tt_smi.py:166-176def update_telem_table(self) -> None:    try:        telem_table = self.get_widget_by_id(id="tt_smi_telem")        self.backend.update_telem()        rows = self.format_telemetry_rows()        telem_table.update_data(rows)    # When we bring up the help menu, the telem table is no longer visible,    # but the thread keeps running, so we need to ignore that exception.    except NoMatches as e:        pass`
**Why This is Necessary:**

1.   The telemetry thread runs continuously in the background
2.   When the help menu (`TTHelperMenuBox`) is pushed as an overlay screen, the data table widgets are no longer in the active widget tree
3.   `get_widget_by_id()` raises `NoMatches` exception when the widget isn't found
4.   The thread silently catches this exception and retries on the next iteration
5.   When the help menu closes, the data table becomes available again and updates resume

This pattern ensures the telemetry thread doesn't crash when users interact with modal overlays or switch between screens.

**Sources:**[tt_smi/tt_smi.py 166-176](https://github.com/tenstorrent/tt-smi/blob/a2791bfe/tt_smi/tt_smi.py#L166-L176)

Dismiss
Refresh this wiki

Enter email to refresh
---
project: tt-smi
github: https://github.com/tenstorrent/tt-smi
deepwiki: https://deepwiki.com/tenstorrent/tt-smi/2-command-line-interface
---

# Command-Line Interface

Relevant source files
*   [README.md](https://github.com/tenstorrent/tt-smi/blob/a2791bfe/README.md?plain=1)
*   [tt_smi/tt_smi.py](https://github.com/tenstorrent/tt-smi/blob/a2791bfe/tt_smi/tt_smi.py)

The Tenstorrent System Management Interface (TT-SMI) provides a comprehensive command-line interface (CLI) for interacting with Tenstorrent hardware devices. The CLI serves as the primary entry point for all TT-SMI operations and can be invoked via the `tt-smi` executable.

## Overview

The TT-SMI CLI operates in two modes:

1.   **Command Mode**: Execute a specific operation (reset, list, snapshot) and exit
2.   **Interactive Mode**: Launch the Textual-based TUI when no operation flags are provided

The CLI is implemented in the `parse_args()` function [tt_smi/tt_smi.py 576-682](https://github.com/tenstorrent/tt-smi/blob/a2791bfe/tt_smi/tt_smi.py#L576-L682) and processes arguments in the `main()` function [tt_smi/tt_smi.py 768-897](https://github.com/tenstorrent/tt-smi/blob/a2791bfe/tt_smi/tt_smi.py#L768-L897) All CLI operations ultimately interact with the `TTSMIBackend` class for hardware communication.

CLI capabilities:

*   List available Tenstorrent devices on the host
*   Reset Tenstorrent hardware (standard PCI reset and Galaxy IPMI-based reset)
*   Generate JSON snapshots for diagnostics and logging
*   Launch the interactive TUI with configurable display options
*   Control device re-initialization behavior after resets

Sources: [tt_smi/tt_smi.py 4-12](https://github.com/tenstorrent/tt-smi/blob/a2791bfe/tt_smi/tt_smi.py#L4-L12)[tt_smi/tt_smi.py 576-682](https://github.com/tenstorrent/tt-smi/blob/a2791bfe/tt_smi/tt_smi.py#L576-L682)[README.md 1-9](https://github.com/tenstorrent/tt-smi/blob/a2791bfe/README.md?plain=1#L1-L9)

## Available CLI Options

TT-SMI supports the following command-line arguments, defined in `parse_args()`[tt_smi/tt_smi.py 576-682](https://github.com/tenstorrent/tt-smi/blob/a2791bfe/tt_smi/tt_smi.py#L576-L682):

| Argument | Type | Description | Default | Defined At |
| --- | --- | --- | --- | --- |
| `-h, --help` | Flag | Display help message and exit | N/A | Built-in |
| `-v, --version` | Flag | Display TT-SMI version and exit | N/A | [tt_smi/tt_smi.py 588-592](https://github.com/tenstorrent/tt-smi/blob/a2791bfe/tt_smi/tt_smi.py#L588-L592) |
| `-l, --local` | Flag | Run on local chips only (Wormhole only) | `False` | [tt_smi/tt_smi.py 580-586](https://github.com/tenstorrent/tt-smi/blob/a2791bfe/tt_smi/tt_smi.py#L580-L586) |
| `-c, --compact` | Flag | Run TUI in compact mode (hide sidebar) | `False` | [tt_smi/tt_smi.py 617-623](https://github.com/tenstorrent/tt-smi/blob/a2791bfe/tt_smi/tt_smi.py#L617-L623) |
| `-s, --snapshot` | Flag | Dump snapshot to STDOUT | `False` | [tt_smi/tt_smi.py 593-599](https://github.com/tenstorrent/tt-smi/blob/a2791bfe/tt_smi/tt_smi.py#L593-L599) |
| `-ls, --list` | Flag | List boards and exit | `False` | [tt_smi/tt_smi.py 600-606](https://github.com/tenstorrent/tt-smi/blob/a2791bfe/tt_smi/tt_smi.py#L600-L606) |
| `-f, --filename` | Optional String | Write snapshot to file (default path if no filename given) | `False` | [tt_smi/tt_smi.py 607-616](https://github.com/tenstorrent/tt-smi/blob/a2791bfe/tt_smi/tt_smi.py#L607-L616) |
| `--snapshot_no_tty` | Flag | Force no-tty behavior in snapshot output | `False` | [tt_smi/tt_smi.py 637-642](https://github.com/tenstorrent/tt-smi/blob/a2791bfe/tt_smi/tt_smi.py#L637-L642) |
| `-r, --reset` | List of Strings | PCI indices to reset (empty list = reset all) | `None` | [tt_smi/tt_smi.py 624-636](https://github.com/tenstorrent/tt-smi/blob/a2791bfe/tt_smi/tt_smi.py#L624-L636) |
| `--no_reinit` | Flag | Don't detect devices post reset | `False` | [tt_smi/tt_smi.py 675-680](https://github.com/tenstorrent/tt-smi/blob/a2791bfe/tt_smi/tt_smi.py#L675-L680) |
| `-glx_reset` | Flag | Reset all ASICs on Galaxy host | `False` | [tt_smi/tt_smi.py 643-649](https://github.com/tenstorrent/tt-smi/blob/a2791bfe/tt_smi/tt_smi.py#L643-L649) |
| `-glx_reset_auto` | Flag | Auto-retry Galaxy reset up to 3 times | `False` | [tt_smi/tt_smi.py 650-658](https://github.com/tenstorrent/tt-smi/blob/a2791bfe/tt_smi/tt_smi.py#L650-L658) |
| `-glx_reset_tray` | Choice (1-4) | Reset specific Galaxy tray | `None` | [tt_smi/tt_smi.py 659-666](https://github.com/tenstorrent/tt-smi/blob/a2791bfe/tt_smi/tt_smi.py#L659-L666) |
| `-glx_list_tray_to_device` | Flag | List Galaxy tray-to-device mapping | `False` | [tt_smi/tt_smi.py 667-674](https://github.com/tenstorrent/tt-smi/blob/a2791bfe/tt_smi/tt_smi.py#L667-L674) |

All arguments are optional. When no arguments are provided, TT-SMI launches the interactive TUI with the default sidebar enabled.

Sources: [tt_smi/tt_smi.py 576-682](https://github.com/tenstorrent/tt-smi/blob/a2791bfe/tt_smi/tt_smi.py#L576-L682)[README.md 90-127](https://github.com/tenstorrent/tt-smi/blob/a2791bfe/README.md?plain=1#L90-L127)

## CLI Entry Point and Command Processing Flow

The `main()` function serves as the primary entry point for TT-SMI [tt_smi/tt_smi.py 768-897](https://github.com/tenstorrent/tt-smi/blob/a2791bfe/tt_smi/tt_smi.py#L768-L897) It orchestrates argument parsing, driver validation, device detection, and command execution.

**Key Processing Order:**

1.   Driver validation happens before any device operations
2.   Reset operations are processed early and exit immediately (no backend initialization required for PCI resets)
3.   Galaxy reset operations also exit early
4.   Device detection occurs after early-exit operations
5.   Backend initialization happens only when devices are needed
6.   Listing and snapshot operations execute and exit
7.   TUI launch is the default fallback when no operation flags are set

Sources: [tt_smi/tt_smi.py 768-897](https://github.com/tenstorrent/tt-smi/blob/a2791bfe/tt_smi/tt_smi.py#L768-L897)[tt_smi/tt_smi.py 685-731](https://github.com/tenstorrent/tt-smi/blob/a2791bfe/tt_smi/tt_smi.py#L685-L731)

## Command Categories

The CLI operations are organized into three main categories:

### 1. Information Display Commands

These commands display device and system information and then exit:

*   **`-ls, --list`**: Lists all available boards on the host [tt_smi/tt_smi.py 700-702](https://github.com/tenstorrent/tt-smi/blob/a2791bfe/tt_smi/tt_smi.py#L700-L702)

    *   Calls `backend.print_all_available_devices()`
    *   Shows PCI Device ID, Board Type, Device Series, Board Number
    *   Separates all boards from boards that can be reset
    *   See page 2.3 for details

*   **`-glx_list_tray_to_device`**: Lists Galaxy tray-to-device mapping [tt_smi/tt_smi.py 703-713](https://github.com/tenstorrent/tt-smi/blob/a2791bfe/tt_smi/tt_smi.py#L703-L713)

    *   Galaxy-specific command
    *   Requires `check_is_galaxy()` validation
    *   Cannot be run in VM environment
    *   Displays mapping table: Tray Number, Tray Bus ID, PCI Device IDs
    *   See page 2.3 for details

### 2. Reset Commands

These commands perform hardware resets and then exit:

*   **`-r, --reset [indices]`**: Standard PCI device reset [tt_smi/tt_smi.py 790-803](https://github.com/tenstorrent/tt-smi/blob/a2791bfe/tt_smi/tt_smi.py#L790-L803)

    *   Accepts comma-separated PCI indices or empty for all devices
    *   Uses `parse_reset_input()` from `tt_tools_common.reset_common.reset_utils`
    *   Calls `pci_board_reset()` function
    *   See page 2.2 for complete reset documentation

*   **`-glx_reset`**: Galaxy full system reset [tt_smi/tt_smi.py 805-821](https://github.com/tenstorrent/tt-smi/blob/a2791bfe/tt_smi/tt_smi.py#L805-L821)

*   **`-glx_reset_auto`**: Galaxy reset with auto-retry [tt_smi/tt_smi.py 822-859](https://github.com/tenstorrent/tt-smi/blob/a2791bfe/tt_smi/tt_smi.py#L822-L859)

*   **`-glx_reset_tray N`**: Galaxy single tray reset [tt_smi/tt_smi.py 860-871](https://github.com/tenstorrent/tt-smi/blob/a2791bfe/tt_smi/tt_smi.py#L860-L871)

*   **`--no_reinit`**: Modifier flag to skip device re-detection [tt_smi/tt_smi.py 675-680](https://github.com/tenstorrent/tt-smi/blob/a2791bfe/tt_smi/tt_smi.py#L675-L680)

    *   See page 2.2 for complete reset documentation

### 3. Snapshot Commands

These commands generate JSON snapshots of system state:

*   **`-s, --snapshot`**: Output snapshot to STDOUT [tt_smi/tt_smi.py 714-716](https://github.com/tenstorrent/tt-smi/blob/a2791bfe/tt_smi/tt_smi.py#L714-L716)
*   **`-f, --filename [path]`**: Save snapshot to file [tt_smi/tt_smi.py 717-724](https://github.com/tenstorrent/tt-smi/blob/a2791bfe/tt_smi/tt_smi.py#L717-L724)
*   **`-f -`**: Alternative syntax for STDOUT output [tt_smi/tt_smi.py 714](https://github.com/tenstorrent/tt-smi/blob/a2791bfe/tt_smi/tt_smi.py#L714-L714)
    *   See page 2.3 for complete snapshot documentation

### 4. Display Modifiers

These flags modify the TUI display but don't change core functionality:

*   **`-l, --local`**: Run on local chips only (Wormhole) [tt_smi/tt_smi.py 874-876](https://github.com/tenstorrent/tt-smi/blob/a2791bfe/tt_smi/tt_smi.py#L874-L876)

    *   Sets `local_only=True` in `detect_chips_with_callback()`
    *   Also sets `ignore_ethernet=True`

*   **`-c, --compact`**: Hide sidebar in TUI [tt_smi/tt_smi.py 729](https://github.com/tenstorrent/tt-smi/blob/a2791bfe/tt_smi/tt_smi.py#L729-L729)

    *   Sets `show_sidebar=False` in TTSMI app constructor

*   **`--snapshot_no_tty`**: Force non-TTY output formatting [tt_smi/tt_smi.py 787](https://github.com/tenstorrent/tt-smi/blob/a2791bfe/tt_smi/tt_smi.py#L787-L787)

    *   Overrides `sys.stdout.isatty()` detection

Sources: [tt_smi/tt_smi.py 576-682](https://github.com/tenstorrent/tt-smi/blob/a2791bfe/tt_smi/tt_smi.py#L576-L682)[tt_smi/tt_smi.py 685-731](https://github.com/tenstorrent/tt-smi/blob/a2791bfe/tt_smi/tt_smi.py#L685-L731)[tt_smi/tt_smi.py 790-871](https://github.com/tenstorrent/tt-smi/blob/a2791bfe/tt_smi/tt_smi.py#L790-L871)

## Argument Parsing Implementation

The `parse_args()` function [tt_smi/tt_smi.py 576-682](https://github.com/tenstorrent/tt-smi/blob/a2791bfe/tt_smi/tt_smi.py#L576-L682) uses Python's `argparse` module to define and parse command-line arguments:

**Argument Storage Conventions:**

*   Boolean flags use `action="store_true"` and default to `False`
*   The `-r, --reset` argument uses `nargs="*"` to accept variable-length comma-separated input
*   The `-f, --filename` argument uses `nargs="?"` with `const=None` and `default=False` to distinguish three states: not set, set without value, set with value
*   The `-glx_reset_tray` argument uses `choices` to restrict input to valid tray numbers

Sources: [tt_smi/tt_smi.py 576-682](https://github.com/tenstorrent/tt-smi/blob/a2791bfe/tt_smi/tt_smi.py#L576-L682)

## Backend Integration

The CLI interfaces with the `TTSMIBackend` class to perform hardware operations. Backend initialization occurs after device detection:

**Backend Construction:**

*   `devices` parameter: List of detected chip objects from `detect_chips_with_callback()`
*   `pretty_output` parameter: Set based on `sys.stdout.isatty()` and `args.snapshot_no_tty`
*   Backend is NOT created for early-exit operations (reset commands that don't require device enumeration)

**Key Integration Points:**

*   Device detection uses `detect_chips_with_callback()` from `tt_tools_common.utils_common.tools_utils`
*   Reset operations call `pci_board_reset()` and `glx_6u_trays_reset()` from `tt_smi.tt_smi_backend`
*   The `tt_smi_main()` function [tt_smi/tt_smi.py 685-731](https://github.com/tenstorrent/tt-smi/blob/a2791bfe/tt_smi/tt_smi.py#L685-L731) handles the transition from CLI to either snapshot output or TUI launch

Sources: [tt_smi/tt_smi.py 873-893](https://github.com/tenstorrent/tt-smi/blob/a2791bfe/tt_smi/tt_smi.py#L873-L893)[tt_smi/tt_smi.py 685-731](https://github.com/tenstorrent/tt-smi/blob/a2791bfe/tt_smi/tt_smi.py#L685-L731)[tt_smi/tt_smi.py 34-38](https://github.com/tenstorrent/tt-smi/blob/a2791bfe/tt_smi/tt_smi.py#L34-L38)

## Driver Validation

Before any device operations, TT-SMI validates that the Tenstorrent kernel driver is installed:

The `get_driver_version()` function [tt_smi/tt_smi.py 43-45](https://github.com/tenstorrent/tt-smi/blob/a2791bfe/tt_smi/tt_smi.py#L43-L45) is imported from `tt_tools_common.utils_common.system_utils` and checks for the presence of the tt-kmd driver. This validation occurs at line [tt_smi/tt_smi.py 777-784](https://github.com/tenstorrent/tt-smi/blob/a2791bfe/tt_smi/tt_smi.py#L777-L784)

Error message directs users to install the driver from: [https://github.com/tenstorrent/tt-kmd](https://github.com/tenstorrent/tt-kmd)

Sources: [tt_smi/tt_smi.py 777-784](https://github.com/tenstorrent/tt-smi/blob/a2791bfe/tt_smi/tt_smi.py#L777-L784)[tt_smi/tt_smi.py 43-45](https://github.com/tenstorrent/tt-smi/blob/a2791bfe/tt_smi/tt_smi.py#L43-L45)

## TTY Detection and Output Formatting

TT-SMI adjusts its output formatting based on whether it's running in a TTY (terminal) or being piped/redirected:

**TTY Impact:**

*   When `is_tty=True`: Progress bars, colored output, and status messages are displayed
*   When `is_tty=False`: Minimal output suitable for log files and scripts
*   The `--snapshot_no_tty` flag forces `is_tty=False` even when running in a terminal

This is particularly useful for:

*   Automated scripts that capture output
*   CI/CD pipelines
*   Log file generation
*   JSON output piping to other tools

Sources: [tt_smi/tt_smi.py 787](https://github.com/tenstorrent/tt-smi/blob/a2791bfe/tt_smi/tt_smi.py#L787-L787)[tt_smi/tt_smi.py 891](https://github.com/tenstorrent/tt-smi/blob/a2791bfe/tt_smi/tt_smi.py#L891-L891)[tt_smi/tt_smi.py 796](https://github.com/tenstorrent/tt-smi/blob/a2791bfe/tt_smi/tt_smi.py#L796-L796)[tt_smi/tt_smi.py 800](https://github.com/tenstorrent/tt-smi/blob/a2791bfe/tt_smi/tt_smi.py#L800-L800)[tt_smi/tt_smi.py 814](https://github.com/tenstorrent/tt-smi/blob/a2791bfe/tt_smi/tt_smi.py#L814-L814)

## Error Handling

The CLI implements multiple error checking mechanisms with colored terminal output:

### 1. Driver Validation

Checks that tt-kmd is installed [tt_smi/tt_smi.py 777-784](https://github.com/tenstorrent/tt-smi/blob/a2791bfe/tt_smi/tt_smi.py#L777-L784):

### 2. Device Detection

Validates devices are present [tt_smi/tt_smi.py 873-890](https://github.com/tenstorrent/tt-smi/blob/a2791bfe/tt_smi/tt_smi.py#L873-L890):

### 3. Galaxy-Specific Validation

The `check_is_galaxy()` helper function [tt_smi/tt_smi.py 733-750](https://github.com/tenstorrent/tt-smi/blob/a2791bfe/tt_smi/tt_smi.py#L733-L750) validates Galaxy commands:

*   Checks if any devices are detected
*   Verifies the board type is in `constants.GLX_BOARD_TYPES`
*   Used by `-glx_list_tray_to_device` command

### 4. VM Detection for Galaxy Commands

The `is_vm()` function [tt_smi/tt_smi.py 753-765](https://github.com/tenstorrent/tt-smi/blob/a2791bfe/tt_smi/tt_smi.py#L753-L765) checks `/proc/cpuinfo` for hypervisor presence:

*   Prevents Galaxy hardware operations in virtualized environments
*   Returns `True` if running in VM, `False` otherwise
*   Used to block `-glx_list_tray_to_device` in VMs [tt_smi/tt_smi.py 705-711](https://github.com/tenstorrent/tt-smi/blob/a2791bfe/tt_smi/tt_smi.py#L705-L711)

### 5. Reset Exception Handling

Galaxy reset operations are wrapped in try-except blocks [tt_smi/tt_smi.py 807-821](https://github.com/tenstorrent/tt-smi/blob/a2791bfe/tt_smi/tt_smi.py#L807-L821)[tt_smi/tt_smi.py 862-870](https://github.com/tenstorrent/tt-smi/blob/a2791bfe/tt_smi/tt_smi.py#L862-L870):

*   Catch exceptions during IPMI reset commands
*   Display error messages with color coding
*   Exit with code 1 on failure

**Color Coding:**

*   `CMD_LINE_COLOR.RED`: Error messages
*   `CMD_LINE_COLOR.YELLOW`: Warnings and hints
*   `CMD_LINE_COLOR.PURPLE`: Success messages
*   `CMD_LINE_COLOR.ENDC`: Reset color

Sources: [tt_smi/tt_smi.py 777-784](https://github.com/tenstorrent/tt-smi/blob/a2791bfe/tt_smi/tt_smi.py#L777-L784)[tt_smi/tt_smi.py 873-890](https://github.com/tenstorrent/tt-smi/blob/a2791bfe/tt_smi/tt_smi.py#L873-L890)[tt_smi/tt_smi.py 733-765](https://github.com/tenstorrent/tt-smi/blob/a2791bfe/tt_smi/tt_smi.py#L733-L765)[tt_smi/tt_smi.py 807-870](https://github.com/tenstorrent/tt-smi/blob/a2791bfe/tt_smi/tt_smi.py#L807-L870)[tt_smi/tt_smi.py 29](https://github.com/tenstorrent/tt-smi/blob/a2791bfe/tt_smi/tt_smi.py#L29-L29)

## Local-Only Mode

The `-l, --local` option limits TT-SMI to operate only on locally connected devices, ignoring remote devices available via Ethernet:

```
tt-smi -l
```

This is particularly useful for:

*   Systems with mixed local and remote devices
*   Troubleshooting connectivity issues
*   Operations that should only affect local hardware

The local-only mode is primarily designed for Wormhole devices.

Sources: [tt_smi/tt_smi.py 632-638](https://github.com/tenstorrent/tt-smi/blob/a2791bfe/tt_smi/tt_smi.py#L632-L638)[README.md 83](https://github.com/tenstorrent/tt-smi/blob/a2791bfe/README.md?plain=1#L83-L83)

## Command Flow Implementation

The command flow is implemented in the `main()` function [tt_smi/tt_smi.py 785-906](https://github.com/tenstorrent/tt-smi/blob/a2791bfe/tt_smi/tt_smi.py#L785-L906) which:

1.   Parses command-line arguments using `parse_args()`[tt_smi/tt_smi.py 628-716](https://github.com/tenstorrent/tt-smi/blob/a2791bfe/tt_smi/tt_smi.py#L628-L716)
2.   Checks for driver installation
3.   Processes special commands (reset, list, snapshot, etc.)
4.   Detects available devices
5.   Creates a backend instance
6.   Checks firmware compatibility
7.   Launches the appropriate interface based on the arguments

This structured approach ensures that commands are processed in the correct order and with appropriate validation.

Dismiss
Refresh this wiki

Enter email to refresh
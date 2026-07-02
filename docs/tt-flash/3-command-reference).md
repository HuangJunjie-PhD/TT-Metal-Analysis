---
project: tt-flash
github: https://github.com/tenstorrent/tt-flash
deepwiki: https://deepwiki.com/tenstorrent/tt-flash/3-command-reference)
---

# Command Reference

Relevant source files
*   [README.md](https://github.com/tenstorrent/tt-flash/blob/60c06032/README.md?plain=1)
*   [tt_flash/main.py](https://github.com/tenstorrent/tt-flash/blob/60c06032/tt_flash/main.py)

This page provides a comprehensive reference for the `tt-flash` command-line interface. It covers the command structure, global options, and argument parsing behavior. For detailed documentation of individual subcommands, see:

*   [flash Command](https://deepwiki.com/tenstorrent/tt-flash/3.1-flash-command) - Firmware flashing operations
*   [verify Command](https://deepwiki.com/tenstorrent/tt-flash/3.2-verify-command) - SPI flash verification operations

## Command Structure

The `tt-flash` utility uses a subcommand-based interface where all operations are invoked through explicit subcommands. The general command structure is:

```
tt-flash [GLOBAL_OPTIONS] <subcommand> [SUBCOMMAND_OPTIONS] [ARGUMENTS]
```

### Supported Subcommands

| Subcommand | Purpose | Detailed Documentation |
| --- | --- | --- |
| `flash` | Flash firmware bundles to Tenstorrent devices | [Section 3.1](https://deepwiki.com/tenstorrent/tt-flash/3.1-flash-command) |
| `verify` | Verify SPI flash contents and firmware versions | [Section 3.2](https://deepwiki.com/tenstorrent/tt-flash/3.2-verify-command) |

**Sources:**[tt_flash/main.py 82-131](https://github.com/tenstorrent/tt-flash/blob/60c06032/tt_flash/main.py#L82-L131)

## Global Options

Global options must be specified before the subcommand and apply to all operations.

### Version Information

```
-v, --version
```

Displays the tt-flash version string and exits. The version information is sourced from either `.ignored/version.txt` (for development builds) or from the package metadata (`tt_flash.__version__`).

**Implementation:**[tt_flash/main.py 63-68](https://github.com/tenstorrent/tt-flash/blob/60c06032/tt_flash/main.py#L63-L68)

### Output Control

#### Color Output

```
--no-color
```

Disables colorized terminal output. By default, tt-flash uses ANSI color codes for status messages, stage indicators, and error highlighting. This option forces plain text output, useful for log files or terminals that don't support color.

**Implementation:**[tt_flash/main.py 70-74](https://github.com/tenstorrent/tt-flash/blob/60c06032/tt_flash/main.py#L70-L74)[tt_flash/main.py 208](https://github.com/tenstorrent/tt-flash/blob/60c06032/tt_flash/main.py#L208-L208)

#### TTY Mode

```
--no-tty
```

Forces disable of TTY-specific command output features. This affects progress indicators and interactive display elements that assume a terminal environment.

**Implementation:**[tt_flash/main.py 76-80](https://github.com/tenstorrent/tt-flash/blob/60c06032/tt_flash/main.py#L76-L80)[tt_flash/main.py 207](https://github.com/tenstorrent/tt-flash/blob/60c06032/tt_flash/main.py#L207-L207)

**Sources:**[tt_flash/main.py 63-80](https://github.com/tenstorrent/tt-flash/blob/60c06032/tt_flash/main.py#L63-L80)

## Argument Parsing Architecture

The argument parsing system uses a custom `ArgumentParser` subclass to provide backward compatibility and improved error handling.

### Custom ArgumentParser Class

The `NoExitArgumentParser` class [tt_flash/main.py 49-57](https://github.com/tenstorrent/tt-flash/blob/60c06032/tt_flash/main.py#L49-L57) overrides the default `error()` method to raise `ArgumentParseError` exceptions instead of calling `sys.exit()` when `EXIT_ON_ERROR` is False. This enables programmatic error handling during backward compatibility processing.

**Sources:**[tt_flash/main.py 40-58](https://github.com/tenstorrent/tt-flash/blob/60c06032/tt_flash/main.py#L40-L58)

### Backward Compatibility Mechanism

The backward compatibility logic [tt_flash/main.py 132-177](https://github.com/tenstorrent/tt-flash/blob/60c06032/tt_flash/main.py#L132-L177) allows users to omit the subcommand name when using the `flash` command. This maintains compatibility with versions prior to the introduction of explicit subcommands.

**Process Flow:**

1.   Disable exit-on-error behavior (`EXIT_ON_ERROR = False`)
2.   Attempt to parse arguments as-is
3.   If parsing fails, insert `"flash"` as the first argument
4.   Attempt to parse again with inserted subcommand
5.   If still failing, remove inserted `"flash"` to show original error
6.   Re-enable exit-on-error behavior
7.   Perform final parse with standard error handling

**Examples:**

| User Input | Interpreted As | Valid |
| --- | --- | --- |
| `tt-flash bundle.fwbundle` | `tt-flash flash bundle.fwbundle` | Yes (backward compat) |
| `tt-flash flash bundle.fwbundle` | `tt-flash flash bundle.fwbundle` | Yes (explicit) |
| `tt-flash verify bundle.fwbundle` | `tt-flash verify bundle.fwbundle` | Yes (explicit) |
| `tt-flash -h` | `tt-flash -h` | Yes (exits during first parse) |
| `tt-flash --version` | `tt-flash --version` | Yes (exits during first parse) |

**Sources:**[tt_flash/main.py 132-177](https://github.com/tenstorrent/tt-flash/blob/60c06032/tt_flash/main.py#L132-L177)

## Firmware Bundle Specification

All subcommands that require firmware data accept a bundle file specification through one of two mutually exclusive arguments:

### Positional Argument (Recommended)

```
<fwbundle>
```

Path to a firmware bundle file (`.fwbundle` extension). This is the recommended way to specify firmware bundles.

**Example:**`tt-flash flash /path/to/firmware.fwbundle`

### Legacy Option (Deprecated)

```
--fw-tar <path>
```

Path to a firmware tarball. This option is deprecated and will emit a warning message when used. The warning directs users to use the positional argument syntax instead.

**Implementation:**[tt_flash/main.py 174-175](https://github.com/tenstorrent/tt-flash/blob/60c06032/tt_flash/main.py#L174-L175)

### Validation Logic

The argument parser enforces exactly one bundle specification:

*   If both positional and `--fw-tar` are provided: error
*   If neither is provided: error
*   If only one is provided: accepted

**Implementation:**[tt_flash/main.py 168-171](https://github.com/tenstorrent/tt-flash/blob/60c06032/tt_flash/main.py#L168-L171)

**Sources:**[tt_flash/main.py 85-91](https://github.com/tenstorrent/tt-flash/blob/60c06032/tt_flash/main.py#L85-L91)[tt_flash/main.py 114-120](https://github.com/tenstorrent/tt-flash/blob/60c06032/tt_flash/main.py#L114-L120)[tt_flash/main.py 168-175](https://github.com/tenstorrent/tt-flash/blob/60c06032/tt_flash/main.py#L168-L175)

## Command Execution Flow

**Sources:**[tt_flash/main.py 204-242](https://github.com/tenstorrent/tt-flash/blob/60c06032/tt_flash/main.py#L204-L242)

## Manifest Loading

The `load_manifest()` function [tt_flash/main.py 179-201](https://github.com/tenstorrent/tt-flash/blob/60c06032/tt_flash/main.py#L179-L201) extracts and validates metadata from firmware bundles:

### Process

1.   Opens bundle as tarfile
2.   Extracts `manifest.json` from bundle root
3.   Parses version field from manifest
4.   Validates version format as three-part semantic version (major.minor.patch)
5.   Returns tarfile handle and parsed version tuple

### Error Conditions

| Condition | Error Message |
| --- | --- |
| Manifest not found | `Could not find manifest in {path}` |
| Version field missing | `Could not find version in {path}/manifest.json` |
| Invalid version format | `Invalid version ({version}) in {path}/manifest.json` |

**Sources:**[tt_flash/main.py 179-201](https://github.com/tenstorrent/tt-flash/blob/60c06032/tt_flash/main.py#L179-L201)

## Version Display Format

Version information follows this format structure:

### Development Builds

*   Source: `.ignored/version.txt`[tt_flash/main.py 26-30](https://github.com/tenstorrent/tt-flash/blob/60c06032/tt_flash/main.py#L26-L30)
*   Format: `YYYY-MM-DD-<16-digit-hex-hash>`
*   Parsed into: `VERSION_STR`, `VERSION_DATE`, `VERSION_HASH`

### Release Builds

*   Source: `tt_flash.__version__` package metadata [tt_flash/main.py 32](https://github.com/tenstorrent/tt-flash/blob/60c06032/tt_flash/main.py#L32-L32)
*   Format: Semantic version string

The version is displayed in the program's docstring [tt_flash/main.py 34-37](https://github.com/tenstorrent/tt-flash/blob/60c06032/tt_flash/main.py#L34-L37) and is accessible via `--version`.

**Sources:**[tt_flash/main.py 25-37](https://github.com/tenstorrent/tt-flash/blob/60c06032/tt_flash/main.py#L25-L37)

## Error Handling

All command execution is wrapped in exception handling [tt_flash/main.py 211-238](https://github.com/tenstorrent/tt-flash/blob/60c06032/tt_flash/main.py#L211-L238) that:

1.   Catches any exception during command execution
2.   Prints error message with red color formatting (if colors enabled)
3.   Returns appropriate exit code

For manifest loading failures, the error handling also prints the parser help text to guide users on correct usage [tt_flash/main.py 217-219](https://github.com/tenstorrent/tt-flash/blob/60c06032/tt_flash/main.py#L217-L219)

**Sources:**[tt_flash/main.py 211-239](https://github.com/tenstorrent/tt-flash/blob/60c06032/tt_flash/main.py#L211-L239)

Dismiss
Refresh this wiki

Enter email to refresh
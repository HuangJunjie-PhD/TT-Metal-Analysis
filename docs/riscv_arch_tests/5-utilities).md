---
project: riscv_arch_tests
github: https://github.com/tenstorrent/riscv_arch_tests
deepwiki: https://deepwiki.com/tenstorrent/riscv_arch_tests/5-utilities)
---

# Utilities

Relevant source files
*   [.gitignore](https://github.com/tenstorrent/riscv_arch_tests/blob/e54223b0/.gitignore)
*   [infra/unpack.py](https://github.com/tenstorrent/riscv_arch_tests/blob/e54223b0/infra/unpack.py)

This document covers the utility scripts and supporting tools that facilitate test management, file handling, and infrastructure operations within the RISC-V architectural test repository. These utilities provide essential functionality for archive processing, file manipulation, and automated workflows.

For test execution utilities like `quals.py` and `smoke.py`, see [Test Execution](https://deepwiki.com/tenstorrent/riscv_arch_tests/3.1-test-execution). For test list generation utilities, see [Test List Generation](https://deepwiki.com/tenstorrent/riscv_arch_tests/3.2-test-list-generation). For container infrastructure utilities, see [Container Infrastructure](https://deepwiki.com/tenstorrent/riscv_arch_tests/3.3-container-infrastructure).

## Archive Processing Utilities

The repository includes a comprehensive archive processing system centered around the `Unpack` class, which handles extraction of compressed test archives and manages file organization for the test suite.

### Unpack Class Architecture

The `Unpack` class provides both programmatic and command-line interfaces for extracting ZIP and TAR archives, with specialized handling for RISC-V test file structures.

**Sources:**[infra/unpack.py 20-88](https://github.com/tenstorrent/riscv_arch_tests/blob/e54223b0/infra/unpack.py#L20-L88)

### Archive Processing Workflow

The archive processing system follows a structured workflow for handling different types of compressed files, with special consideration for RISC-V test archive formats.

**Sources:**[infra/unpack.py 42-88](https://github.com/tenstorrent/riscv_arch_tests/blob/e54223b0/infra/unpack.py#L42-L88)[infra/unpack.py 91-103](https://github.com/tenstorrent/riscv_arch_tests/blob/e54223b0/infra/unpack.py#L91-L103)

## Command-Line Interface

The `unpack.py` utility provides a flexible command-line interface with multiple options for controlling extraction behavior.

### Command-Line Arguments

| Argument | Type | Default | Description |
| --- | --- | --- | --- |
| `target` | Path | Current directory | Target paths to unzip (0 or more) |
| `-o, --output_directory` | Path | None | Output directory for extracted files |
| `-r, --recursive` | Flag | False | Recursively unpack all ZIP files in target directories |
| `-rm, --remove_original` | Flag | False | Remove original ZIP file after extraction |
| `-tz, --tar_zip` | Flag | False | Handle tar-zip format with special RISC-V test processing |

**Sources:**[infra/unpack.py 115-121](https://github.com/tenstorrent/riscv_arch_tests/blob/e54223b0/infra/unpack.py#L115-L121)

### Usage Examples

The utility supports various usage patterns for different archive processing scenarios:

*   **Basic extraction**: `./unpack.py archive.zip`
*   **Recursive processing**: `./unpack.py -r -rm` (processes all ZIPs in current directory)
*   **Tar-zip processing**: `./unpack.py -tz archive.zip` (extracts and processes embedded tar files)

**Sources:**[infra/unpack.py 14-17](https://github.com/tenstorrent/riscv_arch_tests/blob/e54223b0/infra/unpack.py#L14-L17)

## Specialized Archive Handling

The `Unpack` class includes specialized logic for handling RISC-V test archives that contain embedded TAR files with specific directory structures.

### Tar-Zip Processing

When the `tar_zip` flag is enabled, the utility performs additional processing to extract ELF files from embedded TAR archives while filtering out disassembly files:

**Sources:**[infra/unpack.py 63-74](https://github.com/tenstorrent/riscv_arch_tests/blob/e54223b0/infra/unpack.py#L63-L74)

## Utility Function Interface

The module provides a simplified function interface for single-file extraction scenarios:

This function wraps the `Unpack` class and includes validation to ensure only single-file archives are processed, raising a `ValueError` if multiple files are found.

**Sources:**[infra/unpack.py 124-128](https://github.com/tenstorrent/riscv_arch_tests/blob/e54223b0/infra/unpack.py#L124-L128)

## Integration with Build System

The archive processing utilities integrate with the broader test infrastructure through the `.gitignore` configuration, which excludes generated log files and coverage data from version control:

*   `riscv_tests/log/*` - Test execution logs
*   `riscv_tests/rv**/**/*_coverage` - Coverage analysis files
*   `__pycache__` - Python bytecode cache

**Sources:**[.gitignore 1-5](https://github.com/tenstorrent/riscv_arch_tests/blob/e54223b0/.gitignore#L1-L5)

Dismiss
Refresh this wiki

Enter email to refresh
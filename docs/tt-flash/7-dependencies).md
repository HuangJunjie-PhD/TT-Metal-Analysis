---
project: tt-flash
github: https://github.com/tenstorrent/tt-flash
deepwiki: https://deepwiki.com/tenstorrent/tt-flash/7-dependencies)
---

# Dependencies

Relevant source files
*   [CHANGELOG.md](https://github.com/tenstorrent/tt-flash/blob/60c06032/CHANGELOG.md?plain=1)
*   [pyproject.toml](https://github.com/tenstorrent/tt-flash/blob/60c06032/pyproject.toml)

## Purpose and Scope

This document catalogs all external dependencies required by tt-flash, including Python packages, Tenstorrent ecosystem libraries, and build tools. It explains the role of each dependency, version constraints, and update patterns observed in the codebase's evolution.

For information about the build system that consumes these dependencies, see [Building from Source](https://deepwiki.com/tenstorrent/tt-flash/8.1-building-from-source). For distribution packaging requirements, see [Debian Packaging](https://deepwiki.com/tenstorrent/tt-flash/9.2-debian-packaging) and [PyPI Distribution](https://deepwiki.com/tenstorrent/tt-flash/9.3-pypi-distribution).

* * *

## Dependency Categories

tt-flash dependencies fall into four distinct categories based on their role in the system:

| Category | Purpose | Scope |
| --- | --- | --- |
| **Tenstorrent Ecosystem** | Hardware communication and shared tooling infrastructure | Runtime, critical for device interaction |
| **Python Standard** | Configuration parsing, data formatting, compatibility layers | Runtime, required for all operations |
| **Development** | Code quality enforcement and static analysis | Development-time only |
| **Build System** | Package creation and distribution | Build-time only |

**Sources:**[pyproject.toml 1-84](https://github.com/tenstorrent/tt-flash/blob/60c06032/pyproject.toml#L1-L84)

* * *

## Runtime Dependencies Overview

**Diagram: Runtime Dependency Graph**

This diagram maps tt-flash modules to their direct dependencies, showing that `pyluwen` is the most heavily used library (referenced by `main`, `flash`, and `chip` modules), while configuration parsing libraries like `pyyaml` and `tomli` are used selectively.

**Sources:**[pyproject.toml 23-35](https://github.com/tenstorrent/tt-flash/blob/60c06032/pyproject.toml#L23-L35)

* * *

## Tenstorrent Ecosystem Dependencies

### pyluwen (~0.8.0)

`pyluwen` provides the critical hardware abstraction layer for all device communication. It is the single most important dependency in tt-flash.

**Role:**

*   SPI flash read/write operations
*   AXI bus memory access
*   ARC processor messaging
*   PCI device enumeration
*   Chip reset operations

**Version Constraint:**`~= 0.8.0` (compatible release, allows 0.8.x patches)

**Update History:**

```
3.3.5 (03/07/25):  0.7.3 → 0.7.5
3.3.4 (02/07/25):  0.6.4 → 0.7.3
3.2.0 (12/03/25):  Stability fixes for tt-smi alignment
3.1.3 (06/03/25):  Added BH ARC init checks
3.1.1 (06/01/25):  Maturin build system updates
3.1.0 (29/10/24):  0.4.6 - Reset support for inaccessible chips
3.0.1 (16/10/24):  0.4.5 - Fixed false positive chip detection
2.2.0 (19/07/24):  Alternative SPI flash configuration support
2.0.8 (14/05/24):  0.3.8 release
```

The frequent version bumps indicate tight coupling with hardware support evolution, particularly for Blackhole platform features.

**Sources:**[pyproject.toml 26](https://github.com/tenstorrent/tt-flash/blob/60c06032/pyproject.toml#L26-L26)[CHANGELOG.md 10-96](https://github.com/tenstorrent/tt-flash/blob/60c06032/CHANGELOG.md?plain=1#L10-L96)

* * *

### tt_tools_common (~1.5)

Provides shared utilities across the Tenstorrent tool ecosystem.

**Role:**

*   Driver version compatibility checks (`tt-kmd` 2.0.0+)
*   Color-coded CLI output (`CMD_LINE_COLOR`)
*   Common error handling patterns
*   Shared configuration parsing

**Version Constraint:**`~= 1.5` (compatible release)

**Update History:**

```
3.3.4 (02/07/25):  1.4.16 → 1.4.17
3.3.3 (05/06/25):  Driver version check fix for tt-kmd 2.0.0
3.3.2 (14/05/25):  Updated to latest version
2.0.8 (14/05/24):  1.4.3 release
```

**Sources:**[pyproject.toml 25](https://github.com/tenstorrent/tt-flash/blob/60c06032/pyproject.toml#L25-L25)[CHANGELOG.md 19-30](https://github.com/tenstorrent/tt-flash/blob/60c06032/CHANGELOG.md?plain=1#L19-L30)

* * *

### Ecosystem Integration Points

**Diagram: Tenstorrent Ecosystem Integration**

The `pyluwen` library requires `tt-kmd` kernel driver to be installed and loaded, while `tt_tools_common` validates driver compatibility. The system maintains deliberate alignment with `tt-smi` versions to ensure stability.

**Sources:**[CHANGELOG.md 25-35](https://github.com/tenstorrent/tt-flash/blob/60c06032/CHANGELOG.md?plain=1#L25-L35)

* * *

## Python Standard Dependencies

### pyyaml (6.0.2)

YAML parsing library used exclusively for firmware metadata.

**Role:**

*   Parse `fw_defines.yaml` files embedded in firmware bundles
*   Extract firmware message type definitions (`MSG_TYPE_ARC_STATE`, `MSG_TYPE_FW_VERSION`, etc.)
*   Load parameter configuration from bundle metadata

**Version Constraint:**`== 6.0.2` (exact pin)

**Update History:**

```
3.4.0 (30/07/25):  6.0.1 → 6.0.2
```

The exact version pin ensures deterministic YAML parsing behavior across all deployments.

**Sources:**[pyproject.toml 24](https://github.com/tenstorrent/tt-flash/blob/60c06032/pyproject.toml#L24-L24)[CHANGELOG.md 10](https://github.com/tenstorrent/tt-flash/blob/60c06032/CHANGELOG.md?plain=1#L10-L10)

* * *

### tabulate (0.9.0)

Table formatting library for CLI output.

**Role:**

*   Format device enumeration tables
*   Display firmware version comparison matrices
*   Present verification results

**Version Constraint:**`== 0.9.0` (exact pin)

**Usage Example:** Device listing output uses `tabulate` to display chip ID, board type, firmware version, and flash status in aligned columns.

**Sources:**[pyproject.toml 27](https://github.com/tenstorrent/tt-flash/blob/60c06032/pyproject.toml#L27-L27)

* * *

### tomli (2.0.1)

TOML parser backport for Python versions < 3.11.

**Role:**

*   Parse `pyproject.toml` for version information
*   Enable PEP 517/518 compatibility on older Python versions

**Version Constraint:**`== 2.0.1; python_version < '3.11'`

**Conditional Dependency:** Only installed when `python_version < '3.11'`. Python 3.11+ uses the built-in `tomllib` module.

**Sources:**[pyproject.toml 28](https://github.com/tenstorrent/tt-flash/blob/60c06032/pyproject.toml#L28-L28)

* * *

### Python Version Compatibility Layers

Two additional dependencies provide backports for older Python versions:

| Dependency | Version | Condition | Purpose |
| --- | --- | --- | --- |
| `importlib-metadata` | >= 1.4.0 | Python < 3.8 | Backport metadata access APIs |
| `importlib-resources` | >= 1.3.0 | Python < 3.9 | Backport `files()` function for package resources |

These ensure tt-flash can access embedded resources (`fw_defines.yaml`, `version.txt`) consistently across Python 3.7-3.11.

**Sources:**[pyproject.toml 30-34](https://github.com/tenstorrent/tt-flash/blob/60c06032/pyproject.toml#L30-L34)

* * *

## Development Dependencies

Development dependencies enforce code quality but are not required for running tt-flash.

### black (Version-Dependent)

Code formatter with Python version-specific installations.

**Version Matrix:**

| Python Version | black Version |
| --- | --- |
| >= 3.9 | 24.10.0 |
| 3.8 | 24.8.0 |
| 3.7 | 23.3.0 |

**Configuration:** Line length and other formatting rules are defined in `pyproject.toml` (not shown in provided files).

**Installation:**

`pip install tt-flash[dev]`
**Sources:**[pyproject.toml 38-42](https://github.com/tenstorrent/tt-flash/blob/60c06032/pyproject.toml#L38-L42)

* * *

### basedpyright (Type Checker)

Static type checker configured for Python 3.7 compatibility.

**Configuration:**

*   **Python Version Target:** 3.7
*   **Type Checking Mode:** "standard"

**Location:**[pyproject.toml 67-69](https://github.com/tenstorrent/tt-flash/blob/60c06032/pyproject.toml#L67-L69)

This ensures type safety across all supported Python versions, using the minimum version (3.7) as the baseline for compatibility checking.

**Sources:**[pyproject.toml 67-69](https://github.com/tenstorrent/tt-flash/blob/60c06032/pyproject.toml#L67-L69)

* * *

## Build System Dependencies

### Build-Time Requirements

The build system requires two packages specified in PEP 517/518 format:

`[build-system]requires = [  "setuptools>=43.0.0",  "wheel"]build-backend = "setuptools.build_meta"`
**setuptools (>= 43.0.0):**

*   Minimum version ensures PEP 517/518 compliance
*   Handles package metadata extraction from `pyproject.toml`
*   Builds source distributions and wheels

**wheel:**

*   Creates binary wheel distributions
*   Required for `pip install` compatibility

**Sources:**[pyproject.toml 74-81](https://github.com/tenstorrent/tt-flash/blob/60c06032/pyproject.toml#L74-L81)

* * *

### Debian Build Tools

For `.deb` package creation, additional tools are required but not declared in `pyproject.toml`:

**Diagram: Debian Build Tool Chain**

These tools are environment dependencies for CI/CD workflows, not Python package dependencies. See [Debian Packaging](https://deepwiki.com/tenstorrent/tt-flash/9.2-debian-packaging) for detailed build procedures.

**Sources:** Inferred from build workflow context; not explicitly declared in provided files.

* * *

## Version Pinning Strategy

tt-flash uses a mixed pinning strategy based on dependency stability requirements:

### Exact Pins (`==`)

Used for dependencies where behavior changes could affect output or correctness:

*   `pyyaml == 6.0.2` - YAML parsing must be deterministic
*   `tabulate == 0.9.0` - Output formatting consistency
*   `tomli == 2.0.1` - TOML parsing behavior
*   `black == X.Y.Z` - Formatter output must be reproducible

### Compatible Release (`~=`)

Used for Tenstorrent ecosystem libraries that follow semantic versioning:

*   `pyluwen ~= 0.8.0` - Allows patch updates (0.8.x) but not minor version changes
*   `tt_tools_common ~= 1.5` - Allows 1.5.x patches, blocks 1.6.0+

This strategy permits bug fixes while preventing breaking changes from ecosystem libraries.

### Minimum Version (`>=`)

Used for compatibility backports where any newer version is acceptable:

*   `importlib-metadata >= 1.4.0` - Any version providing required APIs
*   `importlib-resources >= 1.3.0` - Any version with `files()` function
*   `setuptools >= 43.0.0` - Minimum for PEP 517/518 support

**Sources:**[pyproject.toml 23-35](https://github.com/tenstorrent/tt-flash/blob/60c06032/pyproject.toml#L23-L35)

* * *

## Dependency Update Patterns

### High-Frequency Updates

`pyluwen` and `tt_tools_common` show frequent version bumps, indicating active co-evolution with hardware support:

```
Timeline of pyluwen Updates (last 6 months):
├── 2024-05-14: 0.3.8
├── 2024-07-19: Alternative SPI config
├── 2024-10-16: 0.4.5 (chip detection fixes)
├── 2024-10-29: 0.4.6 (reset improvements)
├── 2025-01-06: Maturin updates
├── 2025-03-06: BH ARC init checks
├── 2025-07-02: 0.6.4 → 0.7.3
└── 2025-07-03: 0.7.3 → 0.7.5
```

These rapid iterations suggest tight integration with firmware development cycles.

**Sources:**[CHANGELOG.md 10-96](https://github.com/tenstorrent/tt-flash/blob/60c06032/CHANGELOG.md?plain=1#L10-L96)

* * *

### Stability-Driven Updates

Some updates explicitly target stability alignment with other tools:

*   **v3.2.0 (12/03/2025):** "luwen version bump to bring inline with tt-smi; provides stability fixes"
*   **v3.3.3 (05/06/2025):** "Bumped tt-tools-common version to fix driver version check for compatibility with tt-kmd 2.0.0"

This indicates coordinated releases across the Tenstorrent tool ecosystem to maintain system-wide compatibility.

**Sources:**[CHANGELOG.md 25-35](https://github.com/tenstorrent/tt-flash/blob/60c06032/CHANGELOG.md?plain=1#L25-L35)

* * *

## Package Data and Resources

tt-flash includes non-Python resources in its distribution:

`[tool.setuptools.package-data]"*" = [    ".ignored/version.txt",    "data/*/*.yaml"]`
**Version File:**`.ignored/version.txt` stores the package version for runtime access.

**Firmware Definitions:**`data/*/*.yaml` contains `fw_defines.yaml` files with firmware message type definitions, parsed using `pyyaml`.

**Sources:**[pyproject.toml 55-59](https://github.com/tenstorrent/tt-flash/blob/60c06032/pyproject.toml#L55-L59)

* * *

## Dependency Installation

### Standard Installation

Install core dependencies only:

`pip install tt-flash`
### Development Installation

Install with development tools:

`pip install tt-flash[dev]`
### From Source with Editable Install

Install dependencies from source tree:

`git clone https://github.com/tenstorrent/tt-flash.gitcd tt-flashpip install -e .pip install -e .[dev]  # Include dev dependencies`
**Sources:**[pyproject.toml 37-42](https://github.com/tenstorrent/tt-flash/blob/60c06032/pyproject.toml#L37-L42)

* * *

## Summary Table

| Dependency | Version | Category | Update Frequency | Purpose |
| --- | --- | --- | --- | --- |
| `pyluwen` | ~0.8.0 | Tenstorrent | High | Hardware I/O layer |
| `tt_tools_common` | ~1.5 | Tenstorrent | Medium | Driver checks, CLI utils |
| `pyyaml` | 6.0.2 | Python | Low | Firmware metadata parsing |
| `tabulate` | 0.9.0 | Python | Low | CLI table formatting |
| `tomli` | 2.0.1 | Python | Low | TOML parsing (Python < 3.11) |
| `black` | Version-dependent | Dev | Low | Code formatting |
| `basedpyright` | Not versioned | Dev | N/A | Static type checking |
| `setuptools` | >= 43.0.0 | Build | N/A | Package building |
| `wheel` | Not versioned | Build | N/A | Wheel creation |

**Sources:**[pyproject.toml 23-81](https://github.com/tenstorrent/tt-flash/blob/60c06032/pyproject.toml#L23-L81)[CHANGELOG.md 1-96](https://github.com/tenstorrent/tt-flash/blob/60c06032/CHANGELOG.md?plain=1#L1-L96)

Dismiss
Refresh this wiki

Enter email to refresh
---
project: tt-system-firmware
github: https://github.com/tenstorrent/tt-system-firmware
deepwiki: https://deepwiki.com/tenstorrent/tt-system-firmware/9-development-guide
---

# Development Guide

Relevant source files
*   [.github/workflows/docs.yml](https://github.com/tenstorrent/tt-system-firmware/blob/ab59b09c/.github/workflows/docs.yml)
*   [README.md](https://github.com/tenstorrent/tt-system-firmware/blob/ab59b09c/README.md?plain=1)
*   [doc/Doxyfile](https://github.com/tenstorrent/tt-system-firmware/blob/ab59b09c/doc/Doxyfile)
*   [doc/Makefile](https://github.com/tenstorrent/tt-system-firmware/blob/ab59b09c/doc/Makefile)
*   [doc/_doxygen/doxygen-awesome-tt.css](https://github.com/tenstorrent/tt-system-firmware/blob/ab59b09c/doc/_doxygen/doxygen-awesome-tt.css)
*   [doc/_doxygen/doxygen-awesome-tt.js](https://github.com/tenstorrent/tt-system-firmware/blob/ab59b09c/doc/_doxygen/doxygen-awesome-tt.js)
*   [doc/_doxygen/main.md](https://github.com/tenstorrent/tt-system-firmware/blob/ab59b09c/doc/_doxygen/main.md?plain=1)
*   [doc/api.rst](https://github.com/tenstorrent/tt-system-firmware/blob/ab59b09c/doc/api.rst)
*   [doc/conf.py](https://github.com/tenstorrent/tt-system-firmware/blob/ab59b09c/doc/conf.py)
*   [doc/contribute/coding_guidelines/index.rst](https://github.com/tenstorrent/tt-system-firmware/blob/ab59b09c/doc/contribute/coding_guidelines/index.rst)
*   [doc/contribute/index.rst](https://github.com/tenstorrent/tt-system-firmware/blob/ab59b09c/doc/contribute/index.rst)
*   [doc/images/tt_logo_white.png](https://github.com/tenstorrent/tt-system-firmware/blob/ab59b09c/doc/images/tt_logo_white.png)
*   [doc/index.rst](https://github.com/tenstorrent/tt-system-firmware/blob/ab59b09c/doc/index.rst)
*   [doc/requirements.txt](https://github.com/tenstorrent/tt-system-firmware/blob/ab59b09c/doc/requirements.txt)
*   [doc/zephyr.rst](https://github.com/tenstorrent/tt-system-firmware/blob/ab59b09c/doc/zephyr.rst)
*   [scripts/get_ttzp_version.py](https://github.com/tenstorrent/tt-system-firmware/blob/ab59b09c/scripts/get_ttzp_version.py)
*   [scripts/requirements-actions.in](https://github.com/tenstorrent/tt-system-firmware/blob/ab59b09c/scripts/requirements-actions.in)
*   [scripts/requirements-actions.txt](https://github.com/tenstorrent/tt-system-firmware/blob/ab59b09c/scripts/requirements-actions.txt)
*   [test-conf/tests/drivers/pwm/pwm_api/boards/tt_blackhole_tt_blackhole_dmc.conf](https://github.com/tenstorrent/tt-system-firmware/blob/ab59b09c/test-conf/tests/drivers/pwm/pwm_api/boards/tt_blackhole_tt_blackhole_dmc.conf)
*   [test-conf/tests/drivers/pwm/pwm_api/boards/tt_blackhole_tt_blackhole_dmc.overlay](https://github.com/tenstorrent/tt-system-firmware/blob/ab59b09c/test-conf/tests/drivers/pwm/pwm_api/boards/tt_blackhole_tt_blackhole_dmc.overlay)
*   [test-conf/tests/drivers/pwm/pwm_api/testcase.yaml](https://github.com/tenstorrent/tt-system-firmware/blob/ab59b09c/test-conf/tests/drivers/pwm/pwm_api/testcase.yaml)
*   [west.yml](https://github.com/tenstorrent/tt-system-firmware/blob/ab59b09c/west.yml)

This guide provides essential information for developers contributing to tt-system-firmware. It covers the development environment setup, build processes, testing strategies, and documentation workflows. This page provides an overview of the development process; detailed instructions for specific tasks can be found in the subsections.

For information about code style and contribution requirements, see [Coding Guidelines and Standards](https://deepwiki.com/tenstorrent/tt-system-firmware/9.3-coding-guidelines-and-standards). For detailed setup instructions, see [Getting Started](https://deepwiki.com/tenstorrent/tt-system-firmware/9.1-getting-started). For documentation generation, see [Documentation System](https://deepwiki.com/tenstorrent/tt-system-firmware/9.2-documentation-system).

* * *

## Development Environment Overview

The tt-system-firmware repository is structured as a Zephyr RTOS module that integrates with the Zephyr build system through `west`, the Zephyr meta-tool. Development requires setting up a Zephyr workspace with the appropriate dependencies and toolchains.

**Development Workflow Diagram**

Sources: [README.md 19-32](https://github.com/tenstorrent/tt-system-firmware/blob/ab59b09c/README.md?plain=1#L19-L32)[west.yml 1-33](https://github.com/tenstorrent/tt-system-firmware/blob/ab59b09c/west.yml#L1-L33)[doc/Makefile 1-32](https://github.com/tenstorrent/tt-system-firmware/blob/ab59b09c/doc/Makefile#L1-L32)

* * *

## Project Dependencies and Manifest

The project uses `west` to manage dependencies and external modules. The manifest defines all upstream dependencies, including the Zephyr RTOS fork and supporting libraries.

| Component | Repository | Purpose |
| --- | --- | --- |
| `zephyr` | tenstorrent/zephyr-fork | Customized Zephyr RTOS with board support |
| `librpmi` | tenstorrent-forks/librpmi | Runtime Power Management Interface library |
| `cmsis_6` | (via Zephyr) | ARM CMSIS headers |
| `hal_stm32` | (via Zephyr) | STM32 HAL drivers |
| `mcuboot` | (via Zephyr) | Secure bootloader framework |
| `mbedtls` | (via Zephyr) | Cryptography library |

The manifest is defined in [west.yml 1-33](https://github.com/tenstorrent/tt-system-firmware/blob/ab59b09c/west.yml#L1-L33) and specifies exact commit revisions for reproducible builds. The Zephyr fork at revision `01cee78c8305` includes board definitions and custom patches specific to Tenstorrent hardware.

Sources: [west.yml 11-29](https://github.com/tenstorrent/tt-system-firmware/blob/ab59b09c/west.yml#L11-L29)

* * *

## Version Management

The project uses a hierarchical versioning scheme where the product version contains independently versioned sub-applications.

**Version Management Architecture**

The [VERSION 1-5](https://github.com/tenstorrent/tt-system-firmware/blob/ab59b09c/VERSION#L1-L5) file contains four numeric components:

*   `VERSION_MAJOR` - Major version
*   `VERSION_MINOR` - Minor version
*   `PATCHLEVEL` - Patch level
*   `VERSION_TWEAK` - Tweak version

These are parsed by [scripts/get_ttzp_version.py 11-18](https://github.com/tenstorrent/tt-system-firmware/blob/ab59b09c/scripts/get_ttzp_version.py#L11-L18) for documentation and [scripts/get_ttzp_version.py 20-54](https://github.com/tenstorrent/tt-system-firmware/blob/ab59b09c/scripts/get_ttzp_version.py#L20-L54) for firmware binary encoding.

Sources: [scripts/get_ttzp_version.py 1-59](https://github.com/tenstorrent/tt-system-firmware/blob/ab59b09c/scripts/get_ttzp_version.py#L1-L59)[doc/conf.py 16-23](https://github.com/tenstorrent/tt-system-firmware/blob/ab59b09c/doc/conf.py#L16-L23)

* * *

## Building and Testing Workflow

The development workflow follows a build-test-document cycle managed by GitHub Actions but reproducible locally.

**Build and Test Workflow**

Sources: [README.md 1-49](https://github.com/tenstorrent/tt-system-firmware/blob/ab59b09c/README.md?plain=1#L1-L49)[.github/workflows/docs.yml 1-271](https://github.com/tenstorrent/tt-system-firmware/blob/ab59b09c/.github/workflows/docs.yml#L1-L271)

### Local Build Commands

**Build SMC firmware:**

`west build -b tt_blackhole@p150a/tt_blackhole/smc app/smc`
**Build DMC firmware:**

`west build -b tt_blackhole@p150a/tt_blackhole/dmc app/dmc`
**Run unit tests:**

`west twister -T test-conf/tests --platform native_sim`
**Build documentation:**

`cd docmake html`
Sources: [README.md 19-32](https://github.com/tenstorrent/tt-system-firmware/blob/ab59b09c/README.md?plain=1#L19-L32)

* * *

## Documentation System Architecture

Documentation is generated using a combination of Sphinx (for narrative docs) and Doxygen (for API reference), following a similar pattern to upstream Zephyr.

**Documentation Build Pipeline**

The documentation build is orchestrated by [doc/Makefile 1-32](https://github.com/tenstorrent/tt-system-firmware/blob/ab59b09c/doc/Makefile#L1-L32) which handles both Sphinx and Doxygen builds. The Sphinx configuration at [doc/conf.py 1-77](https://github.com/tenstorrent/tt-system-firmware/blob/ab59b09c/doc/conf.py#L1-L77) sets up the project name, version, theme, and extensions.

Key configuration:

*   **Theme**: `sphinx_rtd_theme` (Read the Docs theme)
*   **Extensions**: `myst_parser`, `sphinx.ext.intersphinx`, `sphinx_tabs.tabs`, `sphinx.ext.graphviz`
*   **Doxygen version**: 1.14.0 (specified in [.github/workflows/docs.yml 19](https://github.com/tenstorrent/tt-system-firmware/blob/ab59b09c/.github/workflows/docs.yml#L19-L19))

Sources: [doc/conf.py 1-77](https://github.com/tenstorrent/tt-system-firmware/blob/ab59b09c/doc/conf.py#L1-L77)[doc/Doxyfile 1-318](https://github.com/tenstorrent/tt-system-firmware/blob/ab59b09c/doc/Doxyfile#L1-L318)[doc/Makefile 1-32](https://github.com/tenstorrent/tt-system-firmware/blob/ab59b09c/doc/Makefile#L1-L32)[.github/workflows/docs.yml 4-271](https://github.com/tenstorrent/tt-system-firmware/blob/ab59b09c/.github/workflows/docs.yml#L4-L271)

### Documentation Build Process

The GitHub Actions workflow [.github/workflows/docs.yml 168-256](https://github.com/tenstorrent/tt-system-firmware/blob/ab59b09c/.github/workflows/docs.yml#L168-L256) demonstrates the complete documentation build:

1.   **Install dependencies** - Doxygen binary and Python packages from [doc/requirements.txt 1-30](https://github.com/tenstorrent/tt-system-firmware/blob/ab59b09c/doc/requirements.txt#L1-L30)
2.   **Clone Zephyr** - Required for intersphinx linking and Zephyr extensions
3.   **Build Doxygen** - Generate API reference from source code
4.   **Build Sphinx** - Generate narrative documentation with Doxygen integration
5.   **Aggregate CI stats** - Collect test results from stress tests
6.   **Deploy to GitHub Pages** - Publish to `docs.tenstorrent.com`

Sources: [.github/workflows/docs.yml 168-256](https://github.com/tenstorrent/tt-system-firmware/blob/ab59b09c/.github/workflows/docs.yml#L168-L256)[doc/requirements.txt 1-30](https://github.com/tenstorrent/tt-system-firmware/blob/ab59b09c/doc/requirements.txt#L1-L30)

* * *

## Testing Infrastructure

The testing strategy employs multiple levels of validation, from unit tests on simulated platforms to hardware smoke tests on physical boards.

### Test Configuration Matrix

Test configurations are organized under [test-conf/tests/](https://github.com/tenstorrent/tt-system-firmware/blob/ab59b09c/test-conf/tests/) with board-specific overlays and configuration files:

| Test Type | Configuration File | Board Target |
| --- | --- | --- |
| PWM Driver Test | [test-conf/tests/drivers/pwm/pwm_api/testcase.yaml 1-18](https://github.com/tenstorrent/tt-system-firmware/blob/ab59b09c/test-conf/tests/drivers/pwm/pwm_api/testcase.yaml#L1-L18) | `tt_blackhole@p150a/tt_blackhole/dmc` |
| PWM Overlay | [test-conf/tests/drivers/pwm/pwm_api/boards/tt_blackhole_tt_blackhole_dmc.overlay 1-11](https://github.com/tenstorrent/tt-system-firmware/blob/ab59b09c/test-conf/tests/drivers/pwm/pwm_api/boards/tt_blackhole_tt_blackhole_dmc.overlay#L1-L11) | DMC board specific |
| PWM Config | [test-conf/tests/drivers/pwm/pwm_api/boards/tt_blackhole_tt_blackhole_dmc.conf 1-22](https://github.com/tenstorrent/tt-system-firmware/blob/ab59b09c/test-conf/tests/drivers/pwm/pwm_api/boards/tt_blackhole_tt_blackhole_dmc.conf#L1-L22) | MAX6639 fan controller |

**Test Configuration Architecture**

Sources: [test-conf/tests/drivers/pwm/pwm_api/testcase.yaml 1-18](https://github.com/tenstorrent/tt-system-firmware/blob/ab59b09c/test-conf/tests/drivers/pwm/pwm_api/testcase.yaml#L1-L18)[test-conf/tests/drivers/pwm/pwm_api/boards/tt_blackhole_tt_blackhole_dmc.overlay 1-11](https://github.com/tenstorrent/tt-system-firmware/blob/ab59b09c/test-conf/tests/drivers/pwm/pwm_api/boards/tt_blackhole_tt_blackhole_dmc.overlay#L1-L11)[test-conf/tests/drivers/pwm/pwm_api/boards/tt_blackhole_tt_blackhole_dmc.conf 1-22](https://github.com/tenstorrent/tt-system-firmware/blob/ab59b09c/test-conf/tests/drivers/pwm/pwm_api/boards/tt_blackhole_tt_blackhole_dmc.conf#L1-L22)

### Running Tests Locally

**Run all unit tests:**

`west twister -T test-conf/tests --platform native_sim`
**Run specific test suite:**

`west twister -T test-conf/tests/drivers/pwm/pwm_api --platform native_sim`
**Run with coverage:**

`west twister -T test-conf/tests --platform native_sim --coverage`
For hardware testing details, see the [Hardware Test Infrastructure](https://deepwiki.com/tenstorrent/tt-system-firmware/8.2-hardware-test-infrastructure) documentation.

Sources: [README.md 28-32](https://github.com/tenstorrent/tt-system-firmware/blob/ab59b09c/README.md?plain=1#L28-L32)

* * *

## Contribution Workflow

The project follows Zephyr's contribution model with emphasis on small, focused changes and proper commit messages.

### Pull Request Guidelines

From [doc/contribute/index.rst 24-42](https://github.com/tenstorrent/tt-system-firmware/blob/ab59b09c/doc/contribute/index.rst#L24-L42):

1.   **Keep PRs small** - One logical change per PR
2.   **Split into multiple commits** - Firmware must build at each commit
3.   **Sign-off required** - Use `git commit -s` for DCO compliance
4.   **Maintain bisectability** - Each commit should build without errors
5.   **Pass style checks** - Run `clang-format` before committing

### Code Style Requirements

The project follows Zephyr's coding style as documented in [doc/contribute/coding_guidelines/index.rst 11-17](https://github.com/tenstorrent/tt-system-firmware/blob/ab59b09c/doc/contribute/coding_guidelines/index.rst#L11-L17) Key points:

*   **Formatting tool**: `clang-format` ([scripts/requirements-actions.in 6](https://github.com/tenstorrent/tt-system-firmware/blob/ab59b09c/scripts/requirements-actions.in#L6-L6) specifies version >=15.0.0)
*   **Naming convention**: snake_case for variables, functions, structs
*   **No typedef structs** - Use `struct foo` not `typedef struct { ... } foo_t`
*   **Function pointer typedefs allowed** - For readability

**Example - Formatting a file:**

`clang-format -i path/to/file.c`
Sources: [doc/contribute/index.rst 1-60](https://github.com/tenstorrent/tt-system-firmware/blob/ab59b09c/doc/contribute/index.rst#L1-L60)[doc/contribute/coding_guidelines/index.rst 1-107](https://github.com/tenstorrent/tt-system-firmware/blob/ab59b09c/doc/contribute/coding_guidelines/index.rst#L1-L107)[scripts/requirements-actions.in 6](https://github.com/tenstorrent/tt-system-firmware/blob/ab59b09c/scripts/requirements-actions.in#L6-L6)

* * *

## CI/CD Quality Gates

All pull requests must pass multiple quality gates before merging:

### Automated Checks

| Workflow | Purpose | Key Validations |
| --- | --- | --- |
| `build-fw.yml` | Firmware builds | Multi-board matrix, all configurations |
| `run-unit-tests.yml` | Unit tests | Twister tests on `native_sim` |
| `compliance.yml` | Code standards | Style, licensing, headers |
| `copyright-check.yml` | Copyright headers | Header verification |
| `license-check.yml` | License compliance | `scancode` analysis |
| `verify-blob.yml` | Binary integrity | SHA256 checksums |
| `hardware-smoke.yml` | Hardware validation | DMC/SMC smoke tests on physical boards |

The compliance checks use tools from [scripts/requirements-actions.txt 1-45](https://github.com/tenstorrent/tt-system-firmware/blob/ab59b09c/scripts/requirements-actions.txt#L1-L45) including:

*   `clang-format>=15.0.0` for code formatting
*   `pylint>=3` for Python code quality
*   `gitlint>=0.19.1` for commit message validation
*   `reuse` for SPDX license compliance

Sources: [.github/workflows/docs.yml 1-13](https://github.com/tenstorrent/tt-system-firmware/blob/ab59b09c/.github/workflows/docs.yml#L1-L13)[scripts/requirements-actions.txt 1-45](https://github.com/tenstorrent/tt-system-firmware/blob/ab59b09c/scripts/requirements-actions.txt#L1-L45)

* * *

## Development Tool Requirements

### Python Dependencies

Core development requires the packages in [doc/requirements.txt 1-30](https://github.com/tenstorrent/tt-system-firmware/blob/ab59b09c/doc/requirements.txt#L1-L30):

**Documentation tools:**

*   `sphinx<8.2.0` - Documentation generator
*   `sphinx_rtd_theme~=3.0` - Read the Docs theme
*   `doxygen` - API documentation (binary install)

**Testing tools:**

*   `pytest` - Test framework
*   `pyserial` - Serial communication for hardware tests

**YAML validation:**

*   `PyYAML>=6.0` - YAML parsing
*   `pykwalify` - YAML schema validation

### CI/CD Dependencies

Additional tools for CI workflows from [scripts/requirements-actions.in 1-46](https://github.com/tenstorrent/tt-system-firmware/blob/ab59b09c/scripts/requirements-actions.in#L1-L46):

*   `west>=0.14.0` - Zephyr meta-tool
*   `gcovr==6.0` - Coverage reporting
*   `junit2html`, `junitparser>=2` - Test result processing
*   `gitpython>=3.1.41` - Git operations
*   `tox` - Test environment management

Sources: [doc/requirements.txt 1-30](https://github.com/tenstorrent/tt-system-firmware/blob/ab59b09c/doc/requirements.txt#L1-L30)[scripts/requirements-actions.in 1-46](https://github.com/tenstorrent/tt-system-firmware/blob/ab59b09c/scripts/requirements-actions.in#L1-L46)

* * *

## Common Development Tasks

### Building for Different Board Variants

The project supports multiple Blackhole board variants:

`# P100A variantwest build -b tt_blackhole@p100a/tt_blackhole/smc app/smc # P150A variantwest build -b tt_blackhole@p150a/tt_blackhole/smc app/smc # P300A variantwest build -b tt_blackhole@p300a/tt_blackhole/smc app/smc`
Board definitions are located in [boards/tenstorrent/](https://github.com/tenstorrent/tt-system-firmware/blob/ab59b09c/boards/tenstorrent/) as referenced in [doc/conf.py 65](https://github.com/tenstorrent/tt-system-firmware/blob/ab59b09c/doc/conf.py#L65-L65)

### Generating Documentation Locally

From [doc/Makefile 16-32](https://github.com/tenstorrent/tt-system-firmware/blob/ab59b09c/doc/Makefile#L16-L32):

**Build HTML documentation:**

`cd docmake html`
**Build with Doxygen integration:**

`cd docdoxygen Doxyfilemake html`
**Live preview with auto-rebuild:**

`cd docmake watch  # Serves on http://localhost:3000`
The `watch` target uses `sphinx-autobuild` from [doc/requirements.txt 12](https://github.com/tenstorrent/tt-system-firmware/blob/ab59b09c/doc/requirements.txt#L12-L12) for live reloading during development.

Sources: [doc/Makefile 16-32](https://github.com/tenstorrent/tt-system-firmware/blob/ab59b09c/doc/Makefile#L16-L32)[doc/requirements.txt 12](https://github.com/tenstorrent/tt-system-firmware/blob/ab59b09c/doc/requirements.txt#L12-L12)

### Version Management

To update the product version:

1.   Edit [VERSION](https://github.com/tenstorrent/tt-system-firmware/blob/ab59b09c/VERSION) file with new version numbers
2.   Run [scripts/get_ttzp_version.py 57-59](https://github.com/tenstorrent/tt-system-firmware/blob/ab59b09c/scripts/get_ttzp_version.py#L57-L59) to verify parsing
3.   The version automatically propagates to: 
    *   Documentation: [doc/conf.py 23](https://github.com/tenstorrent/tt-system-firmware/blob/ab59b09c/doc/conf.py#L23-L23)
    *   Firmware binaries: via `get_ttzp_version_u32()`
    *   Build artifacts: firmware bundle filenames

Sources: [scripts/get_ttzp_version.py 11-59](https://github.com/tenstorrent/tt-system-firmware/blob/ab59b09c/scripts/get_ttzp_version.py#L11-L59)[doc/conf.py 16-23](https://github.com/tenstorrent/tt-system-firmware/blob/ab59b09c/doc/conf.py#L16-L23)

* * *

## Key File Locations Reference

| Component | Path | Description |
| --- | --- | --- |
| Project manifest | [west.yml](https://github.com/tenstorrent/tt-system-firmware/blob/ab59b09c/west.yml) | West workspace configuration |
| Version file | [VERSION](https://github.com/tenstorrent/tt-system-firmware/blob/ab59b09c/VERSION) | Product and sub-app versions |
| SMC application | [app/smc/](https://github.com/tenstorrent/tt-system-firmware/blob/ab59b09c/app/smc/) | System Management Controller firmware |
| DMC application | [app/dmc/](https://github.com/tenstorrent/tt-system-firmware/blob/ab59b09c/app/dmc/) | Device Management Controller firmware |
| Libraries | [lib/tenstorrent/](https://github.com/tenstorrent/tt-system-firmware/blob/ab59b09c/lib/tenstorrent/) | Shared firmware libraries |
| Board definitions | [boards/tenstorrent/](https://github.com/tenstorrent/tt-system-firmware/blob/ab59b09c/boards/tenstorrent/) | Hardware board configurations |
| Test configurations | [test-conf/tests/](https://github.com/tenstorrent/tt-system-firmware/blob/ab59b09c/test-conf/tests/) | Twister test definitions |
| Documentation source | [doc/](https://github.com/tenstorrent/tt-system-firmware/blob/ab59b09c/doc/) | Sphinx/Doxygen documentation |
| Build scripts | [scripts/](https://github.com/tenstorrent/tt-system-firmware/blob/ab59b09c/scripts/) | Build and versioning scripts |
| CI workflows | [.github/workflows/](https://github.com/tenstorrent/tt-system-firmware/blob/ab59b09c/.github/workflows/) | GitHub Actions definitions |

* * *

## Next Steps

For detailed information on specific development topics:

*   **Environment Setup**: See [Getting Started](https://deepwiki.com/tenstorrent/tt-system-firmware/9.1-getting-started) for complete setup instructions including Zephyr SDK installation
*   **Documentation Authoring**: See [Documentation System](https://deepwiki.com/tenstorrent/tt-system-firmware/9.2-documentation-system) for details on Sphinx/Doxygen integration and styling
*   **Code Standards**: See [Coding Guidelines and Standards](https://deepwiki.com/tenstorrent/tt-system-firmware/9.3-coding-guidelines-and-standards) for style rules, naming conventions, and compliance requirements
*   **Contributing Process**: See [Contributing Guidelines](https://github.com/tenstorrent/tt-system-firmware/blob/ab59b09c/Contributing%20Guidelines) for PR workflow and commit message format
*   **Testing Strategy**: See [Testing and Validation](https://deepwiki.com/tenstorrent/tt-system-firmware/7-testing-and-validation) for comprehensive test coverage information
*   **CI/CD Details**: See [CI/CD Infrastructure](https://deepwiki.com/tenstorrent/tt-system-firmware/8-cicd-infrastructure) for automated pipeline details

Sources: [README.md 1-49](https://github.com/tenstorrent/tt-system-firmware/blob/ab59b09c/README.md?plain=1#L1-L49)[doc/index.rst 1-34](https://github.com/tenstorrent/tt-system-firmware/blob/ab59b09c/doc/index.rst#L1-L34)[doc/contribute/index.rst 1-60](https://github.com/tenstorrent/tt-system-firmware/blob/ab59b09c/doc/contribute/index.rst#L1-L60)

Dismiss
Refresh this wiki

Enter email to refresh
---
project: tt-burnin
github: https://github.com/tenstorrent/tt-burnin
deepwiki: https://deepwiki.com/tenstorrent/tt-burnin
---

# Overview

 Relevant source files

* [CHANGELOG.md](https://github.com/tenstorrent/tt-burnin/blob/809f293d/CHANGELOG.md?plain=1)
* [LICENSE](https://github.com/tenstorrent/tt-burnin/blob/809f293d/LICENSE)
* [README.md](https://github.com/tenstorrent/tt-burnin/blob/809f293d/README.md?plain=1)
* [pyproject.toml](https://github.com/tenstorrent/tt-burnin/blob/809f293d/pyproject.toml)

This document provides a comprehensive overview of the tt-burnin repository, explaining its purpose as a burn-in testing utility for Tenstorrent AI hardware and detailing its high-level architecture and capabilities.

For detailed installation and usage instructions, see [Getting Started](/tenstorrent/tt-burnin/1.1-getting-started). For comprehensive hardware support information, see [Hardware Support](/tenstorrent/tt-burnin/4-hardware-support). For build and release processes, see [Build and Release](/tenstorrent/tt-burnin/5-build-and-release).

## Purpose and Functionality

The tt-burnin project is a command-line utility designed to run high power consumption workloads on Tenstorrent AI accelerator chips for burn-in testing purposes. The utility automatically detects connected Tenstorrent devices, loads appropriate power virus workloads, and provides real-time telemetry monitoring to validate hardware operation under stress conditions.

The primary entry point is the `tt-burnin` command, which is defined in [pyproject.toml49](https://github.com/tenstorrent/tt-burnin/blob/809f293d/pyproject.toml#L49-L49) as mapping to the `tt_burnin.main:main` function. The application description states it will "Run high power workload on all connected chips" [pyproject.toml4](https://github.com/tenstorrent/tt-burnin/blob/809f293d/pyproject.toml#L4-L4)

Sources: [pyproject.toml1-72](https://github.com/tenstorrent/tt-burnin/blob/809f293d/pyproject.toml#L1-L72) [README.md1-108](https://github.com/tenstorrent/tt-burnin/blob/809f293d/README.md?plain=1#L1-L108)

## Supported Hardware Platforms

The utility supports three generations of Tenstorrent AI accelerator architectures:

| Architecture | Chip Series | TTX Workload | First Supported |
| --- | --- | --- | --- |
| Grayskull | e75, e150, e300 | gspv.ttx | v0.1.0 |
| Wormhole | n150, n300, Galaxy | whpv.ttx | v0.1.0 |
| Blackhole | p100a, p150a/b | bhpv.ttx | v0.2.0 |

The workload files are included as package data through the setuptools configuration [pyproject.toml54-57](https://github.com/tenstorrent/tt-burnin/blob/809f293d/pyproject.toml#L54-L57) specifically including all `*.ttx` files from the `ttx/` directory.

Sources: [pyproject.toml54-57](https://github.com/tenstorrent/tt-burnin/blob/809f293d/pyproject.toml#L54-L57) [CHANGELOG.md26-27](https://github.com/tenstorrent/tt-burnin/blob/809f293d/CHANGELOG.md?plain=1#L26-L27) [CHANGELOG.md38-39](https://github.com/tenstorrent/tt-burnin/blob/809f293d/CHANGELOG.md?plain=1#L38-L39)

## System Architecture Overview

**System Architecture Overview**

This diagram shows how the CLI interface connects to the core application modules, which abstract hardware through chip-specific implementations and utilize external dependencies for hardware communication and workload management.

Sources: [pyproject.toml48-49](https://github.com/tenstorrent/tt-burnin/blob/809f293d/pyproject.toml#L48-L49) [pyproject.toml26-35](https://github.com/tenstorrent/tt-burnin/blob/809f293d/pyproject.toml#L26-L35) [pyproject.toml54-57](https://github.com/tenstorrent/tt-burnin/blob/809f293d/pyproject.toml#L54-L57)

## Operational Workflow

**Burn-in Execution Workflow**

This diagram illustrates the complete operational sequence that tt-burnin follows, from hardware detection through workload execution to cleanup and exit.

Sources: [README.md64-68](https://github.com/tenstorrent/tt-burnin/blob/809f293d/README.md?plain=1#L64-L68) [README.md89-95](https://github.com/tenstorrent/tt-burnin/blob/809f293d/README.md?plain=1#L89-L95)

## Key Dependencies and Integration

The application relies on several critical external libraries:

* **pyluwen** [pyproject.toml28](https://github.com/tenstorrent/tt-burnin/blob/809f293d/pyproject.toml#L28-L28): Provides low-level hardware interface for communicating with Tenstorrent chips
* **tt\_tools\_common** [pyproject.toml27](https://github.com/tenstorrent/tt-burnin/blob/809f293d/pyproject.toml#L27-L27): Shared utilities and common functionality across Tenstorrent tools
* **jsons** [pyproject.toml30](https://github.com/tenstorrent/tt-burnin/blob/809f293d/pyproject.toml#L30-L30): Handles serialization and deserialization of TTX workload files
* **tomli** [pyproject.toml29](https://github.com/tenstorrent/tt-burnin/blob/809f293d/pyproject.toml#L29-L29): TOML parsing for configuration files (Python < 3.11)

The utility supports Python versions 3.7 through 3.11 [pyproject.toml19-23](https://github.com/tenstorrent/tt-burnin/blob/809f293d/pyproject.toml#L19-L23) and follows semantic versioning principles [CHANGELOG.md6](https://github.com/tenstorrent/tt-burnin/blob/809f293d/CHANGELOG.md?plain=1#L6-L6)

Sources: [pyproject.toml26-35](https://github.com/tenstorrent/tt-burnin/blob/809f293d/pyproject.toml#L26-L35) [pyproject.toml19-25](https://github.com/tenstorrent/tt-burnin/blob/809f293d/pyproject.toml#L19-L25)

## Development Status and Licensing

The project is currently in Beta development status [pyproject.toml15](https://github.com/tenstorrent/tt-burnin/blob/809f293d/pyproject.toml#L15-L15) and is distributed under the Apache 2.0 license [pyproject.toml17](https://github.com/tenstorrent/tt-burnin/blob/809f293d/pyproject.toml#L17-L17) The codebase is actively maintained with regular feature additions and bug fixes as documented in the changelog.

Recent major milestones include:

* **v0.1.0** (2024-04-04): Initial open source release with Grayskull and Wormhole support
* **v0.2.0** (2024-10-29): Added Blackhole burn-in support
* **v0.2.2** (2024-07-31): Added threaded execution, GLX reset support, and Blackhole harvesting

Sources: [pyproject.toml14-17](https://github.com/tenstorrent/tt-burnin/blob/809f293d/pyproject.toml#L14-L17) [CHANGELOG.md34-39](https://github.com/tenstorrent/tt-burnin/blob/809f293d/CHANGELOG.md?plain=1#L34-L39) [CHANGELOG.md23-27](https://github.com/tenstorrent/tt-burnin/blob/809f293d/CHANGELOG.md?plain=1#L23-L27) [CHANGELOG.md8-14](https://github.com/tenstorrent/tt-burnin/blob/809f293d/CHANGELOG.md?plain=1#L8-L14)

Refresh this wiki
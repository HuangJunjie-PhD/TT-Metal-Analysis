---
project: tt-burnin
github: https://github.com/tenstorrent/tt-burnin
deepwiki: https://deepwiki.com/tenstorrent/tt-burnin/2-core-application
---

# Core Application

Relevant source files
*   [tt_burnin/__init__.py](https://github.com/tenstorrent/tt-burnin/blob/809f293d/tt_burnin/__init__.py)
*   [tt_burnin/main.py](https://github.com/tenstorrent/tt-burnin/blob/809f293d/tt_burnin/main.py)

The Core Application encompasses the main execution logic, hardware abstraction, workload management, and utility functions that form the foundation of the tt-burnin system. This page provides an overview of the primary components and their interactions within the burn-in testing utility for Tenstorrent AI hardware.

For detailed information about specific aspects: command-line interface and execution flow, see [Main Application Logic](https://deepwiki.com/tenstorrent/tt-burnin/2.1-main-application-logic); chip detection and hardware abstraction, see [Hardware Abstraction Layer](https://deepwiki.com/tenstorrent/tt-burnin/2.2-hardware-abstraction-layer); TTX file handling and workload loading, see [TTX Workload System](https://deepwiki.com/tenstorrent/tt-burnin/2.3-ttx-workload-system); device management utilities, see [Hardware Management Utilities](https://deepwiki.com/tenstorrent/tt-burnin/2.4-hardware-management-utilities).

## Core Component Architecture

The core application is structured around several key components that work together to provide burn-in testing functionality across different Tenstorrent chip architectures.

**Core Application Component Structure**

Sources: [tt_burnin/main.py 1-390](https://github.com/tenstorrent/tt-burnin/blob/809f293d/tt_burnin/main.py#L1-L390)

## Execution Flow Overview

The main execution follows a structured lifecycle from initialization through monitoring to cleanup, with parallel operations for performance optimization.

**Main Execution Flow**

Sources: [tt_burnin/main.py 286-390](https://github.com/tenstorrent/tt-burnin/blob/809f293d/tt_burnin/main.py#L286-L390)

## Architecture-Specific Burn-in Functions

The application provides specialized burn-in functions for each supported Tenstorrent architecture, following a consistent pattern of reset sequences, workload loading, and core control.

**Burn-in Function Architecture**

Sources: [tt_burnin/main.py 77-241](https://github.com/tenstorrent/tt-burnin/blob/809f293d/tt_burnin/main.py#L77-L241)

## Package Structure and Versioning

The core application includes dynamic version resolution that works both in development and distribution environments.

| Component | Purpose | Implementation |
| --- | --- | --- |
| `__get_package_version()` | Dynamic version resolution | Tries `importlib_metadata.version()` first, falls back to `pyproject.toml` parsing |
| `__getattr__()` | Dynamic attribute access | Provides `__version__` attribute via function call |
| Version Sources | Dual-mode operation | Distribution packages vs development environment |

**Version Resolution Flow**

Sources: [tt_burnin/__init__.py 1-60](https://github.com/tenstorrent/tt-burnin/blob/809f293d/tt_burnin/__init__.py#L1-L60)

## Threading and Performance Optimization

The application uses parallel execution for both start and stop operations to minimize latency when managing multiple devices simultaneously.

| Operation | Threading Strategy | Implementation |
| --- | --- | --- |
| Burn-in Start | Parallel thread per device | `threading.Thread(target=start_burnin, args=(device, i, len(devs)))` |
| Burn-in Stop | Parallel thread per device | `threading.Thread(target=stop_burnin, args=(device,))` |
| Thread Management | Join all threads | `for t in threads: t.join()` |
| Performance Benefit | Reduced total execution time | N devices processed in parallel instead of sequentially |

Sources: [tt_burnin/main.py 334-385](https://github.com/tenstorrent/tt-burnin/blob/809f293d/tt_burnin/main.py#L334-L385)

Dismiss
Refresh this wiki

Enter email to refresh
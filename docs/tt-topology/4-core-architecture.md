---
project: tt-topology
github: https://github.com/tenstorrent/tt-topology
deepwiki: https://deepwiki.com/tenstorrent/tt-topology/4-core-architecture
---

# Core Architecture

Relevant source files
*   [pyproject.toml](https://github.com/tenstorrent/tt-topology/blob/f86e46c6/pyproject.toml)
*   [tt_topology/backend.py](https://github.com/tenstorrent/tt-topology/blob/f86e46c6/tt_topology/backend.py)
*   [tt_topology/tt_topology.py](https://github.com/tenstorrent/tt-topology/blob/f86e46c6/tt_topology/tt_topology.py)

This document provides a comprehensive overview of the tt-topology system's internal architecture, focusing on the core components, data flow, and design patterns that enable Tenstorrent hardware topology configuration. This covers the main processing pipeline, backend systems, and hardware interface layer.

For information about CLI usage patterns and command-line options, see [CLI Reference](https://deepwiki.com/tenstorrent/tt-topology/3-cli-reference). For hardware-specific details and memory layouts, see [Hardware Support](https://deepwiki.com/tenstorrent/tt-topology/5-hardware-support). For logging system implementation, see [Logging and Diagnostics](https://deepwiki.com/tenstorrent/tt-topology/6-logging-and-diagnostics).

## System Overview

The tt-topology system follows a layered architecture with clear separation between user interface, core processing logic, and hardware interface layers. The system orchestrates hardware detection, topology generation, and coordinate flashing through a pipeline-based approach.

**Sources**: [tt_topology/tt_topology.py 1-539](https://github.com/tenstorrent/tt-topology/blob/f86e46c6/tt_topology/tt_topology.py#L1-L539)[tt_topology/backend.py 1-1082](https://github.com/tenstorrent/tt-topology/blob/f86e46c6/tt_topology/backend.py#L1-L1082)

## Core Processing Pipeline

The system implements a multi-stage processing pipeline that transforms user input into hardware configuration. Each stage has specific responsibilities and error handling mechanisms.

**Sources**: [tt_topology/tt_topology.py 119-286](https://github.com/tenstorrent/tt-topology/blob/f86e46c6/tt_topology/tt_topology.py#L119-L286)[tt_topology/backend.py 86-971](https://github.com/tenstorrent/tt-topology/blob/f86e46c6/tt_topology/backend.py#L86-L971)

## Backend System Architecture

The backend system is built around two primary classes that handle different hardware configurations and provide specialized functionality for topology management.

### TopoBackend Class Structure

**Sources**: [tt_topology/backend.py 86-971](https://github.com/tenstorrent/tt-topology/blob/f86e46c6/tt_topology/backend.py#L86-L971)

### TopoBackend_Octopus Class Structure

**Sources**: [tt_topology/backend.py 973-1082](https://github.com/tenstorrent/tt-topology/blob/f86e46c6/tt_topology/backend.py#L973-L1082)

## Hardware Interface Layer

The system interfaces with Tenstorrent hardware through multiple abstraction layers, each providing specific functionality for different aspects of hardware management.

**Sources**: [tt_topology/backend.py 142-217](https://github.com/tenstorrent/tt-topology/blob/f86e46c6/tt_topology/backend.py#L142-L217)[tt_topology/backend.py 314-432](https://github.com/tenstorrent/tt-topology/blob/f86e46c6/tt_topology/backend.py#L314-L432)[tt_topology/constants.py](https://github.com/tenstorrent/tt-topology/blob/f86e46c6/tt_topology/constants.py)

## Coordinate Generation Algorithms

The system implements multiple coordinate generation algorithms optimized for different topology types. Each algorithm uses graph theory principles to generate valid coordinate mappings.

### Algorithm Selection Logic

**Sources**: [tt_topology/backend.py 513-576](https://github.com/tenstorrent/tt-topology/blob/f86e46c6/tt_topology/backend.py#L513-L576)[tt_topology/backend.py 654-709](https://github.com/tenstorrent/tt-topology/blob/f86e46c6/tt_topology/backend.py#L654-L709)[tt_topology/backend.py 711-756](https://github.com/tenstorrent/tt-topology/blob/f86e46c6/tt_topology/backend.py#L711-L756)

## Error Handling and Validation

The architecture implements comprehensive error handling and validation mechanisms throughout the processing pipeline.

| Validation Stage | Method | Error Conditions |
| --- | --- | --- |
| Device Detection | `detect_chips_with_callback()` | No devices found, driver issues |
| Board Compatibility | `main()` | Unsupported board types |
| Connection Validation | `check_num_available_connections()` | Missing physical connections |
| Coordinate Generation | `generate_mesh_connection_independent()` | Non-compliant coordinate assignments |
| Hardware Flashing | `flash_to_specified_state()` | SPI write failures, ARC message timeouts |

**Sources**: [tt_topology/tt_topology.py 430-470](https://github.com/tenstorrent/tt-topology/blob/f86e46c6/tt_topology/tt_topology.py#L430-L470)[tt_topology/backend.py 434-481](https://github.com/tenstorrent/tt-topology/blob/f86e46c6/tt_topology/backend.py#L434-L481)[tt_topology/backend.py 513-575](https://github.com/tenstorrent/tt-topology/blob/f86e46c6/tt_topology/backend.py#L513-L575)

## Logging and State Management

The system maintains comprehensive state tracking through the `TTToplogyLog` class, capturing configuration states at multiple pipeline stages.

**Sources**: [tt_topology/backend.py 100-111](https://github.com/tenstorrent/tt-topology/blob/f86e46c6/tt_topology/backend.py#L100-L111)[tt_topology/backend.py 142-216](https://github.com/tenstorrent/tt-topology/blob/f86e46c6/tt_topology/backend.py#L142-L216)[tt_topology/log.py](https://github.com/tenstorrent/tt-topology/blob/f86e46c6/tt_topology/log.py)

Dismiss
Refresh this wiki

Enter email to refresh
---
project: tt-topology
github: https://github.com/tenstorrent/tt-topology
deepwiki: https://deepwiki.com/tenstorrent/tt-topology/6-logging-and-diagnostics
---

# Logging and Diagnostics

Relevant source files
*   [README.md](https://github.com/tenstorrent/tt-topology/blob/f86e46c6/README.md?plain=1)
*   [tt_topology/log.py](https://github.com/tenstorrent/tt-topology/blob/f86e46c6/tt_topology/log.py)

This document covers tt-topology's structured logging system and diagnostic capabilities. The logging system captures comprehensive information about topology operations, hardware configurations, and system state for debugging and record-keeping purposes. The system is designed to be compatible with Elasticsearch for enterprise-scale log analysis and storage.

For detailed information about the specific log data structure and schemas, see [Log Format](https://deepwiki.com/tenstorrent/tt-topology/6.1-log-format). For setting up Elasticsearch integration for log storage and analysis, see [Elasticsearch Integration](https://deepwiki.com/tenstorrent/tt-topology/6.2-elasticsearch-integration).

## Overview

The tt-topology logging system provides structured, machine-readable logs that capture every aspect of a topology configuration operation. The system uses Pydantic data models to ensure consistent schema validation and automatic generation of Elasticsearch-compatible mappings. All logs are stored as JSON files with timestamps and can be optionally ingested into Elasticsearch for centralized analysis.

The logging system captures:

*   Pre and post-flash SPI register states
*   Hardware connection mappings
*   Coordinate assignments
*   Host system information
*   Error conditions and diagnostics
*   Operation timestamps and metadata

**Sources:**[README.md 233-239](https://github.com/tenstorrent/tt-topology/blob/f86e46c6/README.md?plain=1#L233-L239)[tt_topology/log.py 4-6](https://github.com/tenstorrent/tt-topology/blob/f86e46c6/tt_topology/log.py#L4-L6)

## Log Data Flow Architecture

**Log Data Flow and Aggregation**

This diagram shows how data flows from topology operations through structured models into the final log output. Each data model captures specific aspects of the operation, which are then aggregated into the main `TTToplogyLog` structure.

**Sources:**[tt_topology/log.py 183-194](https://github.com/tenstorrent/tt-topology/blob/f86e46c6/tt_topology/log.py#L183-L194)[tt_topology/log.py 195-199](https://github.com/tenstorrent/tt-topology/blob/f86e46c6/tt_topology/log.py#L195-L199)

## Core Data Models

**Core Logging Data Models and Relationships**

This class diagram illustrates the inheritance hierarchy and composition relationships between the main logging data models. All models inherit from `ElasticModel` to ensure Elasticsearch compatibility.

**Sources:**[tt_topology/log.py 122-135](https://github.com/tenstorrent/tt-topology/blob/f86e46c6/tt_topology/log.py#L122-L135)[tt_topology/log.py 143-194](https://github.com/tenstorrent/tt-topology/blob/f86e46c6/tt_topology/log.py#L143-L194)

## Elasticsearch Compatibility

The logging system is designed from the ground up to be compatible with Elasticsearch. The `ElasticModel` base class provides automatic mapping generation for Elasticsearch field types through the `get_mapping()` method. This ensures that all log data can be properly indexed and searched in Elasticsearch without manual schema configuration.

### Type Mapping System

The system includes a comprehensive type mapping function that converts Python types to Elasticsearch field mappings:

| Python Type | Elasticsearch Type | Notes |
| --- | --- | --- |
| `float` | `float` | Numeric floating point |
| `bool` | `boolean` | Boolean values |
| `Long` | `long` | Large integers |
| `int` | `integer` | Standard integers |
| `bytes` | `binary` | Binary data (base64 encoded) |
| `Keyword` | `keyword` | Exact match strings |
| `Text` | `text` | Full-text searchable |
| `str` | `text` with `keyword` field | Dual indexing for both search and exact match |
| `Date` | `date` | Datetime objects with custom formats |

**Sources:**[tt_topology/log.py 67-91](https://github.com/tenstorrent/tt-topology/blob/f86e46c6/tt_topology/log.py#L67-L91)[tt_topology/log.py 94-112](https://github.com/tenstorrent/tt-topology/blob/f86e46c6/tt_topology/log.py#L94-L112)

## Log File Management

### Default Location and Naming

By default, topology logs are stored in the user's home directory under `~/tt_topology_logs/` with a timestamp-based filename pattern: `<timestamp>_log.json`. This ensures unique filenames for each operation and chronological organization.

### Custom Log File Configuration

Users can specify custom log filenames using the `--log` command-line option:

`tt-topology --log custom_log.json -l mesh`
The log file location can be either a relative or absolute path. If only a filename is provided, it will be saved in the current working directory.

**Sources:**[README.md 236-239](https://github.com/tenstorrent/tt-topology/blob/f86e46c6/README.md?plain=1#L236-L239)

## Diagnostic Information Captured

### Hardware State Tracking

The logging system captures hardware configuration at three critical points:

1.   **Starting Configurations** (`starting_configs`): Initial state of all chips before any modifications
2.   **Post-Default Flashing** (`post_default_flashing_configs`): State after resetting all chips to default coordinates
3.   **Final Coordinates Flash** (`final_coords_flash_config`): Final state after applying new topology coordinates

Each configuration snapshot includes:

*   Board identification
*   Firmware version
*   Chip coordinates (local and remote)
*   Port disable states
*   Rack and shelf assignments

### Connection Discovery

The `ConnectionMap` model captures the physical connectivity between chips, including:

*   Unique connection identifiers
*   Board types and IDs
*   Ethernet board information
*   List of connections with port mappings

### Host Environment

The `HostInfo` model records comprehensive system information:

*   Operating system and distribution
*   Kernel version
*   Hostname and platform details
*   Python version
*   Memory configuration
*   Driver information

**Sources:**[tt_topology/log.py 154-180](https://github.com/tenstorrent/tt-topology/blob/f86e46c6/tt_topology/log.py#L154-L180)[tt_topology/log.py 143-152](https://github.com/tenstorrent/tt-topology/blob/f86e46c6/tt_topology/log.py#L143-L152)

## Integration with Topology Operations

The logging system is tightly integrated with the main topology workflow documented in the README. For each step of the 7-step topology procedure, relevant data is captured:

1.   **Step 1-2**: Default flashing captured in `post_default_flashing_configs`
2.   **Step 3**: Connection mapping captured in `connection_map`
3.   **Step 4**: Coordinate generation captured in `coordinate_map`
4.   **Step 5**: Final configuration captured in `final_coords_flash_config`
5.   **Step 7**: PNG visualization filename recorded in `png_filename`

Any errors encountered during the process are captured in the `errors` field for debugging purposes.

**Sources:**[README.md 95-104](https://github.com/tenstorrent/tt-topology/blob/f86e46c6/README.md?plain=1#L95-L104)[tt_topology/log.py 183-194](https://github.com/tenstorrent/tt-topology/blob/f86e46c6/tt_topology/log.py#L183-L194)

Dismiss
Refresh this wiki

Enter email to refresh
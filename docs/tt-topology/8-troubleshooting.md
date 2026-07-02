---
project: tt-topology
github: https://github.com/tenstorrent/tt-topology
deepwiki: https://deepwiki.com/tenstorrent/tt-topology/8-troubleshooting
---

# Troubleshooting

Relevant source files
*   [CHANGELOG.md](https://github.com/tenstorrent/tt-topology/blob/f86e46c6/CHANGELOG.md?plain=1)
*   [README.md](https://github.com/tenstorrent/tt-topology/blob/f86e46c6/README.md?plain=1)
*   [tt_topology/tt_topology.py](https://github.com/tenstorrent/tt-topology/blob/f86e46c6/tt_topology/tt_topology.py)

This page provides diagnostic guidance and solutions for common issues encountered when using tt-topology. It covers error identification, debugging techniques, and recovery procedures for hardware configuration failures.

For information about system architecture and normal operation flow, see [Core Architecture](https://deepwiki.com/tenstorrent/tt-topology/4-core-architecture). For hardware-specific configuration details, see [Hardware Support](https://deepwiki.com/tenstorrent/tt-topology/5-hardware-support).

## Error Classification and Flow

The tt-topology system has multiple failure points that can be categorized by the stage where they occur:

**Error Classification by Stage**

| Stage | Error Type | Exit Code | Recovery Action |
| --- | --- | --- | --- |
| Driver Check | Missing tt-kmd | 1 | Install driver |
| Device Detection | No hardware found | 1 | Check connections |
| Board Validation | Unsupported boards | Warning | Use supported hardware |
| Connection Mapping | Missing physical links | 1 (mesh only) | Check cables |
| Reset Verification | Device enumeration | 1 | Retry operation |
| Octopus Mode | Missing config | 1 | Provide reset JSON |

Sources: [tt_topology/tt_topology.py 404-538](https://github.com/tenstorrent/tt-topology/blob/f86e46c6/tt_topology/tt_topology.py#L404-L538)[tt_topology/tt_topology.py 288-402](https://github.com/tenstorrent/tt-topology/blob/f86e46c6/tt_topology/tt_topology.py#L288-L402)

## Common Error Messages and Solutions

### Driver and System Errors

**Error**: `No Tenstorrent driver detected! Please install driver using tt-kmd`

**Root Cause**: The system cannot detect a valid Tenstorrent kernel module driver.

**Solution**:

1.   Install tt-kmd version 2.0.0 or higher
2.   Verify driver loading: `lsmod | grep tenstorrent`
3.   Check driver version: `dmesg | grep -i tenstorrent`

**Code Reference**: [tt_topology/tt_topology.py 411-418](https://github.com/tenstorrent/tt-topology/blob/f86e46c6/tt_topology/tt_topology.py#L411-L418)

* * *

**Error**: `No Tenstorrent devices detected! Please check your hardware and try again`

**Root Cause**: Device enumeration failed during `detect_chips_with_callback()`.

**Diagnostic Steps**:

`# Check PCI device visibilitylspci | grep -i tenstorrent # Verify device permissionsls -la /dev/tenstorrent/ # Check system logsdmesg | tail -20`
**Solution**:

1.   Verify PCI card seating
2.   Check power connections
3.   Restart the system if devices were recently installed
4.   Ensure user has appropriate permissions

**Code Reference**: [tt_topology/tt_topology.py 437-450](https://github.com/tenstorrent/tt-topology/blob/f86e46c6/tt_topology/tt_topology.py#L437-L450)

### Layout-Specific Errors

**Error**: `Detected X missing physical connection(s) for mesh layout! It's possible cables are loose or missing.`

**Root Cause**: Mesh topology requires specific connection patterns that are not satisfied by the current hardware configuration.

**Solution**:

1.   Physically inspect all ethernet cables
2.   Verify cable seating in both ends
3.   Test cables with known good hardware
4.   Use `tt-topology --list` to see current connection state
5.   Consider using `linear` or `torus` layout instead

**Code Reference**: [tt_topology/tt_topology.py 199-217](https://github.com/tenstorrent/tt-topology/blob/f86e46c6/tt_topology/tt_topology.py#L199-L217)

* * *

**Error**: `Invalid layout type!`

**Root Cause**: The coordinate generation logic received an unexpected layout parameter.

**Solution**: Use only supported layout types: `linear`, `torus`, `mesh`, `isolated`

**Code Reference**: [tt_topology/tt_topology.py 239-244](https://github.com/tenstorrent/tt-topology/blob/f86e46c6/tt_topology/tt_topology.py#L239-L244)

### Octopus/Galaxy Configuration Errors

**Error**: `No reset json file provided for octopus` or `Please provide a reset json file for octopus`

**Root Cause**: Octopus mode requires a reset configuration file but none was provided.

**Solution**:

1.   Generate default configuration: `tt-topology -g`
2.   Edit the generated file at `~/.config/tenstorrent/reset_config.json`
3.   Run with configuration: `tt-topology -o -r ~/.config/tenstorrent/reset_config.json`

**Code Reference**: [tt_topology/tt_topology.py 314-321](https://github.com/tenstorrent/tt-topology/blob/f86e46c6/tt_topology/tt_topology.py#L314-L321)[tt_topology/tt_topology.py 502-509](https://github.com/tenstorrent/tt-topology/blob/f86e46c6/tt_topology/tt_topology.py#L502-L509)

* * *

**Error**: `NOT ALL LOCAL/REMOTE BOARDS DETECTED!`

**Root Cause**: Post-reset device enumeration shows fewer devices than expected.

**Solution**:

1.   Wait 30 seconds and retry the operation
2.   Check all Galaxy QSFP connections
3.   Verify power to Galaxy systems
4.   Check reset configuration parameters

**Code Reference**: [tt_topology/tt_topology.py 377-393](https://github.com/tenstorrent/tt-topology/blob/f86e46c6/tt_topology/tt_topology.py#L377-L393)

## Hardware-Specific Troubleshooting

### Board Support Validation

The system automatically filters unsupported boards:

**Supported Boards**: N300, N150, Galaxy (WH 4U only)

**Unsupported Boards**: BH PCIe cards, WH 6U Galaxy, BH 6U Galaxy

**Warning Message**: `TT-Topology will only run on n300/n150/GALAXY(WH 4U only) boards. Ignoring these devices: [board_types]`

**Solution**: Use only supported hardware or contact support for additional board support.

**Code Reference**: [tt_topology/tt_topology.py 452-470](https://github.com/tenstorrent/tt-topology/blob/f86e46c6/tt_topology/tt_topology.py#L452-L470)

### Connection and Cable Issues

**Timing-Related Failures**:

*   Ethernet training loops (fixed in v1.2.6)
*   Device enumeration delays after reset
*   M3 L2R copy instability during SPI operations

**Diagnostic Command**: `tt-topology --list`

This command provides current topology state without making changes:

*   Board coordinates
*   Connection status
*   Layout detection

**Code Reference**: [tt_topology/tt_topology.py 473-475](https://github.com/tenstorrent/tt-topology/blob/f86e46c6/tt_topology/tt_topology.py#L473-L475)

## Debugging Tools and Techniques

### Log Analysis

tt-topology generates detailed JSON logs for troubleshooting:

**Default Location**: `~/tt_topology_logs/<timestamp>_log.json`

**Log Contents**:

*   Pre/post flash SPI register states
*   Connection mapping data
*   Coordinate generation results
*   Error traces and exception details

**Custom Log Location**: `tt-topology --log custom_log.json`

**Code Reference**: [tt_topology/tt_topology.py 530](https://github.com/tenstorrent/tt-topology/blob/f86e46c6/tt_topology/tt_topology.py#L530-L530)

### Exception Handling and Recovery

**Exception Recovery**: The system always attempts to save logs even when operations fail, providing diagnostic information for troubleshooting.

**Code Reference**: [tt_topology/tt_topology.py 518-534](https://github.com/tenstorrent/tt-topology/blob/f86e46c6/tt_topology/tt_topology.py#L518-L534)

### Hardware State Inspection

**Pre-operation State Capture**:

`# Captured before any modificationstopo_backend.get_eth_config_state()`
**Post-operation Verification**:

`# Captured after coordinate flashingtopo_backend.get_eth_config_state()`
**Visual Verification**: The system generates PNG plots showing the actual layout achieved.

**Code Reference**: [tt_topology/tt_topology.py 131](https://github.com/tenstorrent/tt-topology/blob/f86e46c6/tt_topology/tt_topology.py#L131-L131)[tt_topology/tt_topology.py 282](https://github.com/tenstorrent/tt-topology/blob/f86e46c6/tt_topology/tt_topology.py#L282-L282)

## Recovery Procedures

### Complete System Reset

When tt-topology fails partially through execution:

1.   **Isolated Mode Recovery**: `tt-topology -l isolated`

    *   Flashes all cards to default non-connected state
    *   Disables ethernet ports to prevent conflicts

2.   **Manual Device Reset**: Use tt-tools-common reset utilities

3.   **Hardware Power Cycle**: For persistent enumeration issues

### Incremental Debugging

1.   **List Current State**: `tt-topology --list`
2.   **Generate Reset Config**: `tt-topology -g`
3.   **Test with Isolated Mode**: `tt-topology -l isolated`
4.   **Attempt Desired Layout**: `tt-topology -l [layout]`

### PCI Interface Issues

**Problem**: Tool fails when PCI interfaces don't start from ID 0 (fixed in v1.2.8)

**Solution**: The tool now uses actual PCI interface IDs from device objects rather than assuming sequential numbering.

**Code Reference**: Based on changelog entry for v1.2.8

Sources: [tt_topology/tt_topology.py 404-538](https://github.com/tenstorrent/tt-topology/blob/f86e46c6/tt_topology/tt_topology.py#L404-L538)[tt_topology/backend.py](https://github.com/tenstorrent/tt-topology/blob/f86e46c6/tt_topology/backend.py)[CHANGELOG.md 1-137](https://github.com/tenstorrent/tt-topology/blob/f86e46c6/CHANGELOG.md?plain=1#L1-L137)[README.md 1-244](https://github.com/tenstorrent/tt-topology/blob/f86e46c6/README.md?plain=1#L1-L244)

Dismiss
Refresh this wiki

Enter email to refresh
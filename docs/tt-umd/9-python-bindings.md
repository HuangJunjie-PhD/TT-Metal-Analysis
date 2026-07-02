---
project: tt-umd
github: https://github.com/tenstorrent/tt-umd
deepwiki: https://deepwiki.com/tenstorrent/tt-umd/9-python-bindings
---

# Python Bindings

Relevant source files
*   [.gitignore](https://github.com/tenstorrent/tt-umd/blob/9bd70c7a/.gitignore)
*   [.pre-commit-config.yaml](https://github.com/tenstorrent/tt-umd/blob/9bd70c7a/.pre-commit-config.yaml)
*   [.pre-commit-hooks/check_cpp_comments.py](https://github.com/tenstorrent/tt-umd/blob/9bd70c7a/.pre-commit-hooks/check_cpp_comments.py)
*   [cmake/packaging.cmake](https://github.com/tenstorrent/tt-umd/blob/9bd70c7a/cmake/packaging.cmake)
*   [device/api/umd/device/logging/config.hpp](https://github.com/tenstorrent/tt-umd/blob/9bd70c7a/device/api/umd/device/logging/config.hpp)
*   [device/api/umd/device/topology/topology_discovery.hpp](https://github.com/tenstorrent/tt-umd/blob/9bd70c7a/device/api/umd/device/topology/topology_discovery.hpp)
*   [device/api/umd/device/topology/topology_discovery_blackhole.hpp](https://github.com/tenstorrent/tt-umd/blob/9bd70c7a/device/api/umd/device/topology/topology_discovery_blackhole.hpp)
*   [device/api/umd/device/topology/topology_discovery_options.hpp](https://github.com/tenstorrent/tt-umd/blob/9bd70c7a/device/api/umd/device/topology/topology_discovery_options.hpp)
*   [device/api/umd/device/topology/topology_discovery_wormhole.hpp](https://github.com/tenstorrent/tt-umd/blob/9bd70c7a/device/api/umd/device/topology/topology_discovery_wormhole.hpp)
*   [device/api/umd/device/tt_device/rtl_simulation_tt_device.hpp](https://github.com/tenstorrent/tt-umd/blob/9bd70c7a/device/api/umd/device/tt_device/rtl_simulation_tt_device.hpp)
*   [device/api/umd/device/tt_device/tt_sim_tt_device.hpp](https://github.com/tenstorrent/tt-umd/blob/9bd70c7a/device/api/umd/device/tt_device/tt_sim_tt_device.hpp)
*   [device/api/umd/device/utils/error.hpp](https://github.com/tenstorrent/tt-umd/blob/9bd70c7a/device/api/umd/device/utils/error.hpp)
*   [device/api/umd/device/utils/error_detail.hpp](https://github.com/tenstorrent/tt-umd/blob/9bd70c7a/device/api/umd/device/utils/error_detail.hpp)
*   [device/api/umd/device/warm_reset_with_recovery.hpp](https://github.com/tenstorrent/tt-umd/blob/9bd70c7a/device/api/umd/device/warm_reset_with_recovery.hpp)
*   [device/arc/wormhole_arc_messenger.cpp](https://github.com/tenstorrent/tt-umd/blob/9bd70c7a/device/arc/wormhole_arc_messenger.cpp)
*   [device/logging/config.cpp](https://github.com/tenstorrent/tt-umd/blob/9bd70c7a/device/logging/config.cpp)
*   [device/topology/topology_discovery.cpp](https://github.com/tenstorrent/tt-umd/blob/9bd70c7a/device/topology/topology_discovery.cpp)
*   [device/topology/topology_discovery_blackhole.cpp](https://github.com/tenstorrent/tt-umd/blob/9bd70c7a/device/topology/topology_discovery_blackhole.cpp)
*   [device/topology/topology_discovery_wormhole.cpp](https://github.com/tenstorrent/tt-umd/blob/9bd70c7a/device/topology/topology_discovery_wormhole.cpp)
*   [device/tt_device/rtl_simulation_tt_device.cpp](https://github.com/tenstorrent/tt-umd/blob/9bd70c7a/device/tt_device/rtl_simulation_tt_device.cpp)
*   [device/tt_device/tt_sim_tt_device.cpp](https://github.com/tenstorrent/tt-umd/blob/9bd70c7a/device/tt_device/tt_sim_tt_device.cpp)
*   [device/warm_reset_with_recovery.cpp](https://github.com/tenstorrent/tt-umd/blob/9bd70c7a/device/warm_reset_with_recovery.cpp)
*   [nanobind/CMakeLists.txt](https://github.com/tenstorrent/tt-umd/blob/9bd70c7a/nanobind/CMakeLists.txt)
*   [nanobind/generate_init.py](https://github.com/tenstorrent/tt-umd/blob/9bd70c7a/nanobind/generate_init.py)
*   [nanobind/py_api_basic_types.cpp](https://github.com/tenstorrent/tt-umd/blob/9bd70c7a/nanobind/py_api_basic_types.cpp)
*   [nanobind/py_api_cluster.cpp](https://github.com/tenstorrent/tt-umd/blob/9bd70c7a/nanobind/py_api_cluster.cpp)
*   [nanobind/py_api_error.cpp](https://github.com/tenstorrent/tt-umd/blob/9bd70c7a/nanobind/py_api_error.cpp)
*   [nanobind/py_api_logging.cpp](https://github.com/tenstorrent/tt-umd/blob/9bd70c7a/nanobind/py_api_logging.cpp)
*   [nanobind/py_api_module.cpp](https://github.com/tenstorrent/tt-umd/blob/9bd70c7a/nanobind/py_api_module.cpp)
*   [nanobind/py_api_topology_discovery.cpp](https://github.com/tenstorrent/tt-umd/blob/9bd70c7a/nanobind/py_api_topology_discovery.cpp)
*   [nanobind/py_api_tt_device.cpp](https://github.com/tenstorrent/tt-umd/blob/9bd70c7a/nanobind/py_api_tt_device.cpp)
*   [nanobind/tests/test_py_basic_types.py](https://github.com/tenstorrent/tt-umd/blob/9bd70c7a/nanobind/tests/test_py_basic_types.py)
*   [nanobind/tests/test_py_cluster.py](https://github.com/tenstorrent/tt-umd/blob/9bd70c7a/nanobind/tests/test_py_cluster.py)
*   [nanobind/tests/test_py_logging.py](https://github.com/tenstorrent/tt-umd/blob/9bd70c7a/nanobind/tests/test_py_logging.py)
*   [nanobind/tests/test_py_no_eth_map_reset.py](https://github.com/tenstorrent/tt-umd/blob/9bd70c7a/nanobind/tests/test_py_no_eth_map_reset.py)
*   [nanobind/tests/test_py_soc_descriptor.py](https://github.com/tenstorrent/tt-umd/blob/9bd70c7a/nanobind/tests/test_py_soc_descriptor.py)
*   [nanobind/tests/test_py_telemetry.py](https://github.com/tenstorrent/tt-umd/blob/9bd70c7a/nanobind/tests/test_py_telemetry.py)
*   [nanobind/tests/test_py_topology_discovery.py](https://github.com/tenstorrent/tt-umd/blob/9bd70c7a/nanobind/tests/test_py_topology_discovery.py)
*   [nanobind/tests/test_py_tt_device.py](https://github.com/tenstorrent/tt-umd/blob/9bd70c7a/nanobind/tests/test_py_tt_device.py)
*   [nanobind/tests/test_py_warm_reset.py](https://github.com/tenstorrent/tt-umd/blob/9bd70c7a/nanobind/tests/test_py_warm_reset.py)
*   [pyproject.toml](https://github.com/tenstorrent/tt-umd/blob/9bd70c7a/pyproject.toml)
*   [tests/misc/CMakeLists.txt](https://github.com/tenstorrent/tt-umd/blob/9bd70c7a/tests/misc/CMakeLists.txt)
*   [tests/misc/test_errors.cpp](https://github.com/tenstorrent/tt-umd/blob/9bd70c7a/tests/misc/test_errors.cpp)
*   [tools/warm_reset.cpp](https://github.com/tenstorrent/tt-umd/blob/9bd70c7a/tools/warm_reset.cpp)

## Purpose and Scope

The Python bindings provide a Python interface to the `tt-umd` C++ library, enabling applications to discover hardware, manage multi-chip clusters, and communicate with Tenstorrent devices. The bindings expose core functionality through the `tt_umd` Python package, including device enumeration, topology discovery, cluster management, and telemetry access.

This page documents the Python binding layer specifically, covering its integration with **nanobind**, the type stub generation pipeline, and the structure of the exposed Python API.

**Sources:**[nanobind/CMakeLists.txt 1-72](https://github.com/tenstorrent/tt-umd/blob/9bd70c7a/nanobind/CMakeLists.txt#L1-L72)[nanobind/py_api_module.cpp 1-50](https://github.com/tenstorrent/tt-umd/blob/9bd70c7a/nanobind/py_api_module.cpp#L1-L50)

## Build System and Integration

### Nanobind Integration

The repository uses **nanobind** for C++/Python interoperability. Nanobind provides a high-performance bridge with automatic type conversion for STL containers and smart pointers.

**Sources:**[nanobind/py_api_tt_device.cpp 5-65](https://github.com/tenstorrent/tt-umd/blob/9bd70c7a/nanobind/py_api_tt_device.cpp#L5-L65)[nanobind/py_api_topology_discovery.cpp 5-31](https://github.com/tenstorrent/tt-umd/blob/9bd70c7a/nanobind/py_api_topology_discovery.cpp#L5-L31)

### PyBufferView and Zero-Copy I/O

To support efficient device I/O, the bindings implement `PyBufferView`[nanobind/py_api_tt_device.cpp 65-95](https://github.com/tenstorrent/tt-umd/blob/9bd70c7a/nanobind/py_api_tt_device.cpp#L65-L95) This RAII wrapper allows Python objects supporting the buffer protocol (e.g., `bytes`, `bytearray`, `memoryview`) to be passed directly to C++ device read/write functions without intermediate copies.

*   **Read-only (Device Writes):** Requests `PyBUF_SIMPLE`.
*   **Writable (Device Reads):** Requests `PyBUF_WRITABLE`. Rejects read-only exporters like `bytes`.
*   **Contiguity:** Enforces C-contiguous buffers to ensure data integrity during hardware DMA/MMIO transfers.

**Sources:**[nanobind/py_api_tt_device.cpp 65-95](https://github.com/tenstorrent/tt-umd/blob/9bd70c7a/nanobind/py_api_tt_device.cpp#L65-L95)

### Type Stubs Generation

The build system automatically generates PEP 561 type stubs (`.pyi` files) to provide IDE autocomplete and static type checking.

1.   **nanobind_add_stub**: Introspects the compiled extension to generate initial `.pyi` files [nanobind/CMakeLists.txt 30-40](https://github.com/tenstorrent/tt-umd/blob/9bd70c7a/nanobind/CMakeLists.txt#L30-L40)
2.   **generate_init.py**: A post-processing script that scans the generated stubs, creates the `__init__.py` entry point, and sets up submodule re-exports [nanobind/generate_init.py 12-87](https://github.com/tenstorrent/tt-umd/blob/9bd70c7a/nanobind/generate_init.py#L12-L87)

**Sources:**[nanobind/CMakeLists.txt 30-60](https://github.com/tenstorrent/tt-umd/blob/9bd70c7a/nanobind/CMakeLists.txt#L30-L60)[nanobind/generate_init.py 1-87](https://github.com/tenstorrent/tt-umd/blob/9bd70c7a/nanobind/generate_init.py#L1-L87)

## Python API Reference

### Topology Discovery (`py_api_topology_discovery`)

Exposes the `TopologyDiscovery` and `ClusterDescriptor` classes.

| Class | Key Methods | Description |
| --- | --- | --- |
| `ClusterDescriptor` | `get_all_chips()`, `is_chip_remote()`, `get_active_eth_channels()` | Represents the physical and logical layout of a cluster [nanobind/py_api_topology_discovery.cpp 41-124](https://github.com/tenstorrent/tt-umd/blob/9bd70c7a/nanobind/py_api_topology_discovery.cpp#L41-L124) |
| `TopologyDiscoveryOptions` | `discover_remote_devices`, `low_power`, `use_safe_api` | Configuration for the discovery process [nanobind/py_api_topology_discovery.cpp 126-145](https://github.com/tenstorrent/tt-umd/blob/9bd70c7a/nanobind/py_api_topology_discovery.cpp#L126-L145) |
| `TopologyDiscovery` | `discover()` | Entry point for scanning the system and generating a `ClusterDescriptor`[nanobind/py_api_topology_discovery.cpp 147-156](https://github.com/tenstorrent/tt-umd/blob/9bd70c7a/nanobind/py_api_topology_discovery.cpp#L147-L156) |

**Sources:**[nanobind/py_api_topology_discovery.cpp 41-156](https://github.com/tenstorrent/tt-umd/blob/9bd70c7a/nanobind/py_api_topology_discovery.cpp#L41-L156)[device/api/umd/device/topology/topology_discovery.hpp 31-36](https://github.com/tenstorrent/tt-umd/blob/9bd70c7a/device/api/umd/device/topology/topology_discovery.hpp#L31-L36)

### Device Management (`py_api_tt_device`)

Exposes `TTDevice`, `PCIDevice`, and hardware-specific creation helpers.

*   **`PCIDevice`**: Provides `enumerate_devices()` and `enumerate_devices_info()` to list physical Tenstorrent boards [nanobind/py_api_tt_device.cpp 147-164](https://github.com/tenstorrent/tt-umd/blob/9bd70c7a/nanobind/py_api_tt_device.cpp#L147-L164)
*   **`TTDevice`**: The primary interface for I/O. Supports `read_from_device`, `write_to_device`, and `noc_multicast_write`[nanobind/py_api_tt_device.cpp 180-260](https://github.com/tenstorrent/tt-umd/blob/9bd70c7a/nanobind/py_api_tt_device.cpp#L180-L260)
*   **Remote Device Helpers**: 
    *   `create_remote_tt_device`: Creates a `TTDevice` for a remote chip using an existing local gateway chip [nanobind/py_api_tt_device.cpp 101-117](https://github.com/tenstorrent/tt-umd/blob/9bd70c7a/nanobind/py_api_tt_device.cpp#L101-L117)
    *   `create_remote_tt_device_from_coord`: Creates a remote device using explicit `EthCoord` (rack, shelf, x, y) [nanobind/py_api_tt_device.cpp 121-131](https://github.com/tenstorrent/tt-umd/blob/9bd70c7a/nanobind/py_api_tt_device.cpp#L121-L131)

**Sources:**[nanobind/py_api_tt_device.cpp 101-260](https://github.com/tenstorrent/tt-umd/blob/9bd70c7a/nanobind/py_api_tt_device.cpp#L101-L260)[device/tt_device/tt_sim_tt_device.cpp 53-71](https://github.com/tenstorrent/tt-umd/blob/9bd70c7a/device/tt_device/tt_sim_tt_device.cpp#L53-L71)

### Telemetry and System Health (`py_api_telemetry`)

Provides access to ARC (Argonaut RISC Core) telemetry data.

*   **`ArcTelemetryReader`**: Reads metrics such as `board_id`, `smbus_address`, and architecture-specific telemetry tags [nanobind/py_api_telemetry.cpp 20-50](https://github.com/tenstorrent/tt-umd/blob/9bd70c7a/nanobind/py_api_telemetry.cpp#L20-L50)
*   **`ArcTelemetry`**: A structure containing the actual telemetry values read from the device [nanobind/py_api_telemetry.cpp 52-65](https://github.com/tenstorrent/tt-umd/blob/9bd70c7a/nanobind/py_api_telemetry.cpp#L52-L65)

**Sources:**[nanobind/py_api_telemetry.cpp 20-65](https://github.com/tenstorrent/tt-umd/blob/9bd70c7a/nanobind/py_api_telemetry.cpp#L20-L65)

### Logging Configuration (`py_api_logging`)

Exposes the `tt-logger` configuration to Python.

*   **`set_logging_dir`**: Sets the directory for UMD logs [nanobind/py_api_logging.cpp 18-20](https://github.com/tenstorrent/tt-umd/blob/9bd70c7a/nanobind/py_api_logging.cpp#L18-L20)
*   **`set_level`**: Dynamically adjusts the logging verbosity (e.g., Info, Debug, Warning) [nanobind/py_api_logging.cpp 21-23](https://github.com/tenstorrent/tt-umd/blob/9bd70c7a/nanobind/py_api_logging.cpp#L21-L23)

**Sources:**[nanobind/py_api_logging.cpp 15-30](https://github.com/tenstorrent/tt-umd/blob/9bd70c7a/nanobind/py_api_logging.cpp#L15-L30)

### Warm Reset (`py_api_warm_reset`)

Exposes the system-level warm reset functionality.

*   **`warm_reset_with_recovery`**: Performs a hardware reset on a specified PCI device and handles the recovery sequence to bring the chip back to a functional state [nanobind/py_api_warm_reset.cpp 15-25](https://github.com/tenstorrent/tt-umd/blob/9bd70c7a/nanobind/py_api_warm_reset.cpp#L15-L25)

**Sources:**[nanobind/py_api_warm_reset.cpp 10-30](https://github.com/tenstorrent/tt-umd/blob/9bd70c7a/nanobind/py_api_warm_reset.cpp#L10-L30)

## Python Test Suite

The Python bindings are verified using a comprehensive test suite located in `nanobind/tests/`.

| Test File | Coverage |
| --- | --- |
| `test_py_tt_device.py` | Basic I/O, MMIO, and device initialization. |
| `test_py_cluster.py` | Multi-chip cluster creation and `ClusterDescriptor` validation. |
| `test_py_topology_discovery.py` | Hardware enumeration and Ethernet link detection. |
| `test_py_telemetry.py` | ARC telemetry reading and tag verification. |
| `test_py_warm_reset.py` | Verifies reset lifecycle and recovery. |
| `test_py_logging.py` | Verifies log level adjustment and file output. |

**Sources:**[nanobind/tests/test_py_tt_device.py 1-50](https://github.com/tenstorrent/tt-umd/blob/9bd70c7a/nanobind/tests/test_py_tt_device.py#L1-L50)[nanobind/tests/test_py_topology_discovery.py 1-50](https://github.com/tenstorrent/tt-umd/blob/9bd70c7a/nanobind/tests/test_py_topology_discovery.py#L1-L50)[nanobind/tests/test_py_telemetry.py 1-50](https://github.com/tenstorrent/tt-umd/blob/9bd70c7a/nanobind/tests/test_py_telemetry.py#L1-L50)

## Implementation Details

### GIL Management

To prevent Python's Global Interpreter Lock (GIL) from bottlenecking hardware I/O, most device-access methods use `nb::call_guard<nb::gil_scoped_release>()`. This allows C++ code to execute device transfers (which may block on hardware response) while other Python threads continue running.

**Sources:**[nanobind/py_api_tt_device.cpp 32-40](https://github.com/tenstorrent/tt-umd/blob/9bd70c7a/nanobind/py_api_tt_device.cpp#L32-L40)[nanobind/py_api_topology_discovery.cpp 25-31](https://github.com/tenstorrent/tt-umd/blob/9bd70c7a/nanobind/py_api_topology_discovery.cpp#L25-L31)

### Data Flow: Python Write to Device

**Sources:**[nanobind/py_api_tt_device.cpp 65-95](https://github.com/tenstorrent/tt-umd/blob/9bd70c7a/nanobind/py_api_tt_device.cpp#L65-L95)[nanobind/py_api_tt_device.cpp 200-220](https://github.com/tenstorrent/tt-umd/blob/9bd70c7a/nanobind/py_api_tt_device.cpp#L200-L220)

Dismiss
Refresh this wiki

Enter email to refresh
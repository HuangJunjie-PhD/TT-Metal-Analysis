---
project: tt-kmd
github: https://github.com/tenstorrent/tt-kmd
deepwiki: https://deepwiki.com/tenstorrent/tt-kmd/10-testing-and-development
---

# Testing and Development

Relevant source files
*   [test/Makefile](https://github.com/tenstorrent/tt-kmd/blob/0ab170d7/test/Makefile)
*   [test/config_space.cpp](https://github.com/tenstorrent/tt-kmd/blob/0ab170d7/test/config_space.cpp)
*   [test/get_driver_info.cpp](https://github.com/tenstorrent/tt-kmd/blob/0ab170d7/test/get_driver_info.cpp)
*   [test/ioctl.h](https://github.com/tenstorrent/tt-kmd/blob/0ab170d7/test/ioctl.h)
*   [test/main.cpp](https://github.com/tenstorrent/tt-kmd/blob/0ab170d7/test/main.cpp)
*   [test/mappings_debugfs.cpp](https://github.com/tenstorrent/tt-kmd/blob/0ab170d7/test/mappings_debugfs.cpp)
*   [test/release.cpp](https://github.com/tenstorrent/tt-kmd/blob/0ab170d7/test/release.cpp)

This page provides an overview of the testing infrastructure and development workflow for the Tenstorrent kernel driver. The `tt-kmd` repository includes a comprehensive C++ test suite (`ttkmd_test`) that validates driver functionality through automated testing, along with a CI/CD pipeline that ensures code quality across multiple Linux distributions.

For detailed information about test suite architecture and individual test components, see [User-Space Test Suite](https://deepwiki.com/tenstorrent/tt-kmd/10.1-user-space-test-suite). For implementation details of specific tests like DMA, TLB, and safety checks, see [Test Implementation Details](https://deepwiki.com/tenstorrent/tt-kmd/10.2-test-implementation-details).

## Testing Philosophy

The `tt-kmd` testing strategy focuses on:

*   **Interface validation**: Verifying correct IOCTL parameter handling and versioning [test/get_driver_info.cpp 55-56](https://github.com/tenstorrent/tt-kmd/blob/0ab170d7/test/get_driver_info.cpp#L55-L56)
*   **Memory safety**: Detecting buffer overruns and validating proper output zeroing [test/main.cpp 51-52](https://github.com/tenstorrent/tt-kmd/blob/0ab170d7/test/main.cpp#L51-L52)
*   **Resource management**: Ensuring proper cleanup of DMA buffers, pinned pages, and TLBs [test/main.cpp 46-53](https://github.com/tenstorrent/tt-kmd/blob/0ab170d7/test/main.cpp#L46-L53)
*   **Multi-device scenarios**: Testing peer-to-peer operations between multiple cards [test/main.cpp 61-67](https://github.com/tenstorrent/tt-kmd/blob/0ab170d7/test/main.cpp#L61-L67)
*   **Hardware verification**: Validating PCIe configuration, MSI setup, and AER status [test/config_space.cpp 88-138](https://github.com/tenstorrent/tt-kmd/blob/0ab170d7/test/config_space.cpp#L88-L138)

The test suite runs in user space and exercises the kernel driver through the character device interface at `/dev/tenstorrent/*`, making it independent of the kernel module build system.

Sources: [test/main.cpp 1-73](https://github.com/tenstorrent/tt-kmd/blob/0ab170d7/test/main.cpp#L1-L73)[test/Makefile 1-38](https://github.com/tenstorrent/tt-kmd/blob/0ab170d7/test/Makefile#L1-L38)[test/get_driver_info.cpp 55-56](https://github.com/tenstorrent/tt-kmd/blob/0ab170d7/test/get_driver_info.cpp#L55-L56)[test/config_space.cpp 88-138](https://github.com/tenstorrent/tt-kmd/blob/0ab170d7/test/config_space.cpp#L88-L138)

## Test Suite Structure

The `ttkmd_test` executable is built from modular C++ components, each responsible for testing a specific driver subsystem. The test runner in `main()` orchestrates test execution across all discovered devices using the `EnumeratedDevice` abstraction [test/main.cpp 38-59](https://github.com/tenstorrent/tt-kmd/blob/0ab170d7/test/main.cpp#L38-L59)

**Test Execution Flow (main.cpp)**

Sources: [test/main.cpp 28-73](https://github.com/tenstorrent/tt-kmd/blob/0ab170d7/test/main.cpp#L28-L73)

**Test Component Organization**

The test suite consists of:

| Category | Source Files | Purpose |
| --- | --- | --- |
| **Core Infrastructure** | `main.cpp`, `enumeration.cpp`, `devfd.cpp`, `util.cpp`, `test_failure.cpp` | Test runner, device discovery, file descriptor management [test/Makefile 13](https://github.com/tenstorrent/tt-kmd/blob/0ab170d7/test/Makefile#L13-L13) |
| **IOCTL Tests** | `get_driver_info.cpp`, `get_device_info.cpp`, `query_mappings.cpp`, `lock.cpp` | Validate basic IOCTL commands and driver metadata [test/Makefile 8-9](https://github.com/tenstorrent/tt-kmd/blob/0ab170d7/test/Makefile#L8-L9) |
| **Memory Tests** | `dma_buf.cpp`, `pin_pages.cpp`, `map_peer_bar.cpp`, `tlbs.cpp` | Test DMA, page pinning, peer BAR mapping, TLB management [test/Makefile 9-10](https://github.com/tenstorrent/tt-kmd/blob/0ab170d7/test/Makefile#L9-L10) |
| **Safety Tests** | `ioctl_overrun.cpp`, `ioctl_zeroing.cpp`, `release.cpp` | Buffer overrun detection, output validation, resource cleanup [test/Makefile 10-11](https://github.com/tenstorrent/tt-kmd/blob/0ab170d7/test/Makefile#L10-L11) |
| **Hardware Tests** | `config_space.cpp`, `hwmon.cpp` | PCIe configuration validation, hardware monitoring [test/Makefile 9](https://github.com/tenstorrent/tt-kmd/blob/0ab170d7/test/Makefile#L9-L9) |
| **Debug Interface Tests** | `mappings_debugfs.cpp`, `procfs_pids.cpp` | Validate debugfs and procfs diagnostic interfaces [test/Makefile 10-11](https://github.com/tenstorrent/tt-kmd/blob/0ab170d7/test/Makefile#L10-L11) |

Sources: [test/Makefile 8-14](https://github.com/tenstorrent/tt-kmd/blob/0ab170d7/test/Makefile#L8-L14)

## Test Categories

The test suite validates driver functionality across several categories:

### Driver Information and Device Discovery

Tests validate device enumeration and driver metadata queries via `TENSTORRENT_IOCTL_GET_DRIVER_INFO`[test/ioctl.h 19](https://github.com/tenstorrent/tt-kmd/blob/0ab170d7/test/ioctl.h#L19-L19) and `TENSTORRENT_IOCTL_GET_DEVICE_INFO`[test/ioctl.h 14](https://github.com/tenstorrent/tt-kmd/blob/0ab170d7/test/ioctl.h#L14-L14)`TestGetDriverInfo` specifically compares the version reported by IOCTL against the version in `/sys/module/tenstorrent/version`[test/get_driver_info.cpp 58-65](https://github.com/tenstorrent/tt-kmd/blob/0ab170d7/test/get_driver_info.cpp#L58-L65)

### Memory Management Operations

Tests for DMA, page pinning, and TLB allocation. `TestDmaBuf` uses `TENSTORRENT_IOCTL_ALLOCATE_DMA_BUF`[test/ioctl.h 17](https://github.com/tenstorrent/tt-kmd/blob/0ab170d7/test/ioctl.h#L17-L17) while `TestTlbs` validates the allocation and configuration of inbound TLB windows [test/ioctl.h 25-27](https://github.com/tenstorrent/tt-kmd/blob/0ab170d7/test/ioctl.h#L25-L27)

### Interface Safety and Correctness

*   `TestIoctlOverrun()`: Detects buffer overrun by placing IOCTL structures at page boundaries.
*   `TestIoctlZeroing()`: Validates that kernel properly zeros unused portions of output buffers.
*   `TestDeviceRelease()`: Verifies the `TENSTORRENT_IOCTL_SET_NOC_CLEANUP` mechanism which triggers a NOC write when a file descriptor is closed [test/release.cpp 18-42](https://github.com/tenstorrent/tt-kmd/blob/0ab170d7/test/release.cpp#L18-L42)

### Hardware Validation

*   `TestConfigSpace()`: Reads PCIe configuration space via sysfs to verify the Command register [test/config_space.cpp 88-98](https://github.com/tenstorrent/tt-kmd/blob/0ab170d7/test/config_space.cpp#L88-L98) MSI status [test/config_space.cpp 100-118](https://github.com/tenstorrent/tt-kmd/blob/0ab170d7/test/config_space.cpp#L100-L118) and Advanced Error Reporting (AER) [test/config_space.cpp 120-138](https://github.com/tenstorrent/tt-kmd/blob/0ab170d7/test/config_space.cpp#L120-L138)
*   `TestHwmon()`: Validates the hardware monitoring interface.

### Debug Interfaces

*   `TestMappingsDebugfs()`: Ensures the `/sys/kernel/debug/tenstorrent/<ordinal>/mappings` file contains mandatory headers and correctly reflects active `OPEN_FD`, `PIN_PAGES`, and `DMA_BUF` entries [test/mappings_debugfs.cpp 60-185](https://github.com/tenstorrent/tt-kmd/blob/0ab170d7/test/mappings_debugfs.cpp#L60-L185)

Sources: [test/main.cpp 11-26](https://github.com/tenstorrent/tt-kmd/blob/0ab170d7/test/main.cpp#L11-L26)[test/ioctl.h 14-30](https://github.com/tenstorrent/tt-kmd/blob/0ab170d7/test/ioctl.h#L14-L30)[test/get_driver_info.cpp 58-65](https://github.com/tenstorrent/tt-kmd/blob/0ab170d7/test/get_driver_info.cpp#L58-L65)[test/release.cpp 18-42](https://github.com/tenstorrent/tt-kmd/blob/0ab170d7/test/release.cpp#L18-L42)[test/mappings_debugfs.cpp 43-185](https://github.com/tenstorrent/tt-kmd/blob/0ab170d7/test/mappings_debugfs.cpp#L43-L185)

## Running Tests

The test suite can be built and executed as follows:

**Build**

`cd test/make -j $(nproc)`
This produces the `ttkmd_test` executable using `CXXFLAGS` including `-std=c++17`[test/Makefile 20-26](https://github.com/tenstorrent/tt-kmd/blob/0ab170d7/test/Makefile#L20-L26)

**Execution**

`sudo ./ttkmd_test`
The test runner requires root privileges to access `/dev/tenstorrent/*` devices. It automatically enumerates all available devices [test/main.cpp 37](https://github.com/tenstorrent/tt-kmd/blob/0ab170d7/test/main.cpp#L37-L37)

**Command-Line Options**

*   `--skip-aer`: Skip Advanced Error Reporting (AER) validation, useful in VMs where AER is often disabled [test/main.cpp 32-35](https://github.com/tenstorrent/tt-kmd/blob/0ab170d7/test/main.cpp#L32-L35)

Sources: [test/main.cpp 28-37](https://github.com/tenstorrent/tt-kmd/blob/0ab170d7/test/main.cpp#L28-L37)[test/Makefile 6-26](https://github.com/tenstorrent/tt-kmd/blob/0ab170d7/test/Makefile#L6-L26)

## Development Workflow

The development workflow emphasizes comprehensive testing before deployment, with special attention to memory safety and proper resource cleanup.

**Natural Language to Code Mapping**

| Concept | Code Entity |
| --- | --- |
| Device Enumeration | `EnumerateDevices()`[test/main.cpp 37](https://github.com/tenstorrent/tt-kmd/blob/0ab170d7/test/main.cpp#L37-L37) |
| Device Handle | `DevFd`[test/devfd.h](https://github.com/tenstorrent/tt-kmd/blob/0ab170d7/test/devfd.h) |
| Test Failure | `THROW_TEST_FAILURE`[test/test_failure.h](https://github.com/tenstorrent/tt-kmd/blob/0ab170d7/test/test_failure.h) |
| IOCTL Magic | `TENSTORRENT_IOCTL_MAGIC` (0xFA) [test/ioctl.h 12](https://github.com/tenstorrent/tt-kmd/blob/0ab170d7/test/ioctl.h#L12-L12) |
| Resource Tracking | `/sys/kernel/debug/tenstorrent/<id>/mappings`[test/mappings_debugfs.cpp 43](https://github.com/tenstorrent/tt-kmd/blob/0ab170d7/test/mappings_debugfs.cpp#L43-L43) |

Sources: [test/main.cpp 37](https://github.com/tenstorrent/tt-kmd/blob/0ab170d7/test/main.cpp#L37-L37)[test/ioctl.h 12](https://github.com/tenstorrent/tt-kmd/blob/0ab170d7/test/ioctl.h#L12-L12)[test/mappings_debugfs.cpp 43](https://github.com/tenstorrent/tt-kmd/blob/0ab170d7/test/mappings_debugfs.cpp#L43-L43)

Dismiss
Refresh this wiki

Enter email to refresh
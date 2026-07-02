---
project: tt-umd
github: https://github.com/tenstorrent/tt-umd
deepwiki: https://deepwiki.com/tenstorrent/tt-umd/8-device-simulation
---

# Device Simulation

Relevant source files
*   [device/api/umd/device/chip/chip.hpp](https://github.com/tenstorrent/tt-umd/blob/9bd70c7a/device/api/umd/device/chip/chip.hpp)
*   [device/api/umd/device/chip/local_chip.hpp](https://github.com/tenstorrent/tt-umd/blob/9bd70c7a/device/api/umd/device/chip/local_chip.hpp)
*   [device/api/umd/device/chip/mock_chip.hpp](https://github.com/tenstorrent/tt-umd/blob/9bd70c7a/device/api/umd/device/chip/mock_chip.hpp)
*   [device/api/umd/device/chip/remote_chip.hpp](https://github.com/tenstorrent/tt-umd/blob/9bd70c7a/device/api/umd/device/chip/remote_chip.hpp)
*   [device/api/umd/device/cluster.hpp](https://github.com/tenstorrent/tt-umd/blob/9bd70c7a/device/api/umd/device/cluster.hpp)
*   [device/api/umd/device/simulation/simulation_chip.hpp](https://github.com/tenstorrent/tt-umd/blob/9bd70c7a/device/api/umd/device/simulation/simulation_chip.hpp)
*   [device/api/umd/device/tt_device/rtl_simulation_tt_device.hpp](https://github.com/tenstorrent/tt-umd/blob/9bd70c7a/device/api/umd/device/tt_device/rtl_simulation_tt_device.hpp)
*   [device/api/umd/device/tt_device/tt_sim_tt_device.hpp](https://github.com/tenstorrent/tt-umd/blob/9bd70c7a/device/api/umd/device/tt_device/tt_sim_tt_device.hpp)
*   [device/chip/mock_chip.cpp](https://github.com/tenstorrent/tt-umd/blob/9bd70c7a/device/chip/mock_chip.cpp)
*   [device/simulation/simulation_chip.cpp](https://github.com/tenstorrent/tt-umd/blob/9bd70c7a/device/simulation/simulation_chip.cpp)
*   [device/tt_device/rtl_simulation_tt_device.cpp](https://github.com/tenstorrent/tt-umd/blob/9bd70c7a/device/tt_device/rtl_simulation_tt_device.cpp)
*   [device/tt_device/tt_sim_tt_device.cpp](https://github.com/tenstorrent/tt-umd/blob/9bd70c7a/device/tt_device/tt_sim_tt_device.cpp)
*   [nanobind/py_api_tt_device.cpp](https://github.com/tenstorrent/tt-umd/blob/9bd70c7a/nanobind/py_api_tt_device.cpp)

## Purpose and Scope

Device Simulation provides the capability to test UMD functionality without physical Tenstorrent hardware. The simulation system implements multiple chip abstractions that conform to the standard `Chip` interface: `MockChip` for lightweight no-op testing, `SWEmuleChip` for software emulation, and `SimulationChip` for RTL-level or high-level functional simulation. Both allow the `Cluster` API to operate transparently against simulated devices.

The simulation infrastructure serves several primary use cases:

1.   **Unit Testing** - `MockChip` provides a zero-overhead stub for testing `Cluster` API logic without hardware interaction.
2.   **RTL Verification** - `SimulationChip` connects to Verilator/VCS simulators or high-level C++ simulators for hardware-accurate functional testing.
3.   **Software Emulation** - `SWEmuleChip` uses memory-backed I/O for software development without physical hardware.
4.   **Pre-Silicon Development** - Enables firmware and driver development before silicon availability.

For details on the simulation-specific components like `TTSimCommunicator` and memory management, see [Simulation Architecture](https://deepwiki.com/tenstorrent/tt-umd/8.1-simulation-architecture). For details on the RTL-specific communication protocols, see [RTL Simulation Device](https://deepwiki.com/tenstorrent/tt-umd/8.2-rtl-simulation-device).

## Simulation Chip Types

The UMD provides several simulation chip implementations, each serving different testing needs:

**Diagram: Chip Type Hierarchy Including Simulation**

### MockChip

`MockChip` is a lightweight no-op implementation of the `Chip` interface [device/api/umd/device/chip/mock_chip.hpp 21](https://github.com/tenstorrent/tt-umd/blob/9bd70c7a/device/api/umd/device/chip/mock_chip.hpp#L21-L21) All device operations (read, write, DMA, reset) are no-ops that return immediately without error. This chip type is used for API contract testing and cluster topology validation.

**Key Characteristics:**

*   `is_mmio_capable()` returns `false`[device/chip/mock_chip.cpp 20](https://github.com/tenstorrent/tt-umd/blob/9bd70c7a/device/chip/mock_chip.cpp#L20-L20)
*   `arc_msg()` returns success with `*return_3 = 1`[device/chip/mock_chip.cpp 57-67](https://github.com/tenstorrent/tt-umd/blob/9bd70c7a/device/chip/mock_chip.cpp#L57-L67)
*   `write_to_device()` and `read_from_device()` are empty stubs [device/chip/mock_chip.cpp 40-42](https://github.com/tenstorrent/tt-umd/blob/9bd70c7a/device/chip/mock_chip.cpp#L40-L42)

Sources: [device/api/umd/device/chip/mock_chip.hpp 21-75](https://github.com/tenstorrent/tt-umd/blob/9bd70c7a/device/api/umd/device/chip/mock_chip.hpp#L21-L75)[device/chip/mock_chip.cpp 15-98](https://github.com/tenstorrent/tt-umd/blob/9bd70c7a/device/chip/mock_chip.cpp#L15-L98)

### SimulationChip

`SimulationChip` provides simulation by communicating with external simulator backends [device/api/umd/device/simulation/simulation_chip.hpp 38](https://github.com/tenstorrent/tt-umd/blob/9bd70c7a/device/api/umd/device/simulation/simulation_chip.hpp#L38-L38) The specific backend (high-level TTSim `.so` or RTL subprocess) is abstracted by the underlying `TTDevice` implementation.

**Key Components:**

*   **TTSimTTDevice**: Interfaces with a high-level simulator via a shared library (`.so`) [device/api/umd/device/tt_device/tt_sim_tt_device.hpp 31](https://github.com/tenstorrent/tt-umd/blob/9bd70c7a/device/api/umd/device/tt_device/tt_sim_tt_device.hpp#L31-L31)
*   **RtlSimulationTTDevice**: Interfaces with RTL simulators (Verilator/VCS) using a FlatBuffer-based protocol [device/api/umd/device/tt_device/rtl_simulation_tt_device.hpp 30](https://github.com/tenstorrent/tt-umd/blob/9bd70c7a/device/api/umd/device/tt_device/rtl_simulation_tt_device.hpp#L30-L30)

Sources: [device/api/umd/device/simulation/simulation_chip.hpp 38-124](https://github.com/tenstorrent/tt-umd/blob/9bd70c7a/device/api/umd/device/simulation/simulation_chip.hpp#L38-L124)[device/simulation/simulation_chip.cpp 39-125](https://github.com/tenstorrent/tt-umd/blob/9bd70c7a/device/simulation/simulation_chip.cpp#L39-L125)

### Comparison Table

| Feature | MockChip | SimulationChip | SWEmuleChip |
| --- | --- | --- | --- |
| **ChipType Enum** | `MOCK` | `SIMULATION` | `SWEMULE` |
| **I/O Accuracy** | None (No-op) | High (RTL or C++ Sim) | Functional (Memory-backed) |
| **Latency** | Minimal | High (Sim overhead) | Moderate |
| **MMIO Support** | No | Simulated via TLBs | Simulated |
| **Use Case** | Unit testing | RTL verification | Software dev |

Sources: [device/api/umd/device/cluster.hpp 64-69](https://github.com/tenstorrent/tt-umd/blob/9bd70c7a/device/api/umd/device/cluster.hpp#L64-L69)

## Simulation Device Architecture

The simulation infrastructure bridges the gap between the standard `Chip` interface and simulation backends. It uses specialized managers to mimic hardware features like System Memory (hugepages) and TLBs.

**Diagram: Simulation Architecture and Code Entities**

### Key Simulation Components

*   **SimulationSysmemManager**: Manages simulated host memory channels (hugepages) [device/api/umd/device/tt_device/tt_sim_tt_device.hpp 28](https://github.com/tenstorrent/tt-umd/blob/9bd70c7a/device/api/umd/device/tt_device/tt_sim_tt_device.hpp#L28-L28) It provides `read_from_sysmem` and `write_to_sysmem` functionality without requiring physical kernel hugepages [device/tt_device/tt_sim_tt_device.cpp 87](https://github.com/tenstorrent/tt-umd/blob/9bd70c7a/device/tt_device/tt_sim_tt_device.cpp#L87-L87)
*   **SimulationTlbAllocator**: Handles the allocation of simulated TLB indices for architectures that use them (Wormhole, Blackhole) [device/api/umd/device/tt_device/tt_sim_tt_device.hpp 93](https://github.com/tenstorrent/tt-umd/blob/9bd70c7a/device/api/umd/device/tt_device/tt_sim_tt_device.hpp#L93-L93)
*   **TTSimCommunicator**: A low-level wrapper that manages the lifecycle of the simulator shared library and provides the ABI for device I/O [device/api/umd/device/tt_device/tt_sim_tt_device.hpp 27](https://github.com/tenstorrent/tt-umd/blob/9bd70c7a/device/api/umd/device/tt_device/tt_sim_tt_device.hpp#L27-L27)
*   **RtlSimCommunicator**: Manages communication with an RTL simulation subprocess using a FlatBuffer protocol [device/api/umd/device/tt_device/rtl_simulation_tt_device.hpp 25](https://github.com/tenstorrent/tt-umd/blob/9bd70c7a/device/api/umd/device/tt_device/rtl_simulation_tt_device.hpp#L25-L25)

Sources: [device/tt_device/tt_sim_tt_device.cpp 83-131](https://github.com/tenstorrent/tt-umd/blob/9bd70c7a/device/tt_device/tt_sim_tt_device.cpp#L83-L131)[device/tt_device/rtl_simulation_tt_device.cpp 70-107](https://github.com/tenstorrent/tt-umd/blob/9bd70c7a/device/tt_device/rtl_simulation_tt_device.cpp#L70-L107)

## Cluster Configuration for Simulation

When constructing a `Cluster`, the `ClusterOptions` struct is used to specify simulation parameters:

`ClusterOptions options;options.chip_type = ChipType::SIMULATION;options.simulator_directory = "/path/to/simulator/";options.num_host_mem_ch_per_mmio_device = 1;Cluster cluster(options);`
The `Cluster` constructor uses these options to determine which concrete `Chip` and `TTDevice` types to instantiate. For `ChipType::SIMULATION`, it defaults to creating a `SimulationChip` which then creates the appropriate `TTDevice` based on the files found in the `simulator_directory`[device/api/umd/device/cluster.hpp 76-123](https://github.com/tenstorrent/tt-umd/blob/9bd70c7a/device/api/umd/device/cluster.hpp#L76-L123)

### DRAM Teleportation

A specialized optimization called "DRAM Teleportation" can be enabled via the environment variable `TT_SIMULATOR_DRAM_TELEPORT`[device/tt_device/tt_sim_tt_device.cpp 38-49](https://github.com/tenstorrent/tt-umd/blob/9bd70c7a/device/tt_device/tt_sim_tt_device.cpp#L38-L49) This allows the simulation driver to bypass the standard NoC-based DRAM access path for faster data transfers during simulation.

Sources: [device/api/umd/device/cluster.hpp 76-123](https://github.com/tenstorrent/tt-umd/blob/9bd70c7a/device/api/umd/device/cluster.hpp#L76-L123)[device/tt_device/tt_sim_tt_device.cpp 38-49](https://github.com/tenstorrent/tt-umd/blob/9bd70c7a/device/tt_device/tt_sim_tt_device.cpp#L38-L49)

## Child Pages

For more in-depth technical documentation, refer to the following child pages:

*   [Simulation Architecture](https://deepwiki.com/tenstorrent/tt-umd/8.1-simulation-architecture) — Detailed look at `SimulationChip` variants, `TTSimCommunicator`, and memory/TLB management in simulation.
*   [RTL Simulation Device](https://deepwiki.com/tenstorrent/tt-umd/8.2-rtl-simulation-device) — Technical details on `RtlSimulationTTDevice`, the FlatBuffer communication protocol, and RTL-specific reset sequences.

Dismiss
Refresh this wiki

Enter email to refresh
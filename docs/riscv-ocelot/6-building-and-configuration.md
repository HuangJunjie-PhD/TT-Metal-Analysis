---
project: riscv-ocelot
github: https://github.com/tenstorrent/riscv-ocelot
deepwiki: https://deepwiki.com/tenstorrent/riscv-ocelot/6-building-and-configuration
---

# Building and Configuration

Relevant source files
*   [.circleci/README.md](https://github.com/tenstorrent/riscv-ocelot/blob/920fd904/.circleci/README.md?plain=1)
*   [.circleci/build-run-csmith-tests.sh](https://github.com/tenstorrent/riscv-ocelot/blob/920fd904/.circleci/build-run-csmith-tests.sh)
*   [.circleci/build-toolchains.sh](https://github.com/tenstorrent/riscv-ocelot/blob/920fd904/.circleci/build-toolchains.sh)
*   [.circleci/config.yml](https://github.com/tenstorrent/riscv-ocelot/blob/920fd904/.circleci/config.yml)
*   [.circleci/create-hash.sh](https://github.com/tenstorrent/riscv-ocelot/blob/920fd904/.circleci/create-hash.sh)
*   [.circleci/defaults.sh](https://github.com/tenstorrent/riscv-ocelot/blob/920fd904/.circleci/defaults.sh)
*   [.circleci/do-rtl-build.sh](https://github.com/tenstorrent/riscv-ocelot/blob/920fd904/.circleci/do-rtl-build.sh)
*   [.circleci/prepare-for-rtl-build.sh](https://github.com/tenstorrent/riscv-ocelot/blob/920fd904/.circleci/prepare-for-rtl-build.sh)
*   [.circleci/run-tests.sh](https://github.com/tenstorrent/riscv-ocelot/blob/920fd904/.circleci/run-tests.sh)
*   [.gitignore](https://github.com/tenstorrent/riscv-ocelot/blob/920fd904/.gitignore)
*   [CHIPYARD.hash](https://github.com/tenstorrent/riscv-ocelot/blob/920fd904/CHIPYARD.hash)
*   [build.sbt](https://github.com/tenstorrent/riscv-ocelot/blob/920fd904/build.sbt)
*   [project/build.properties](https://github.com/tenstorrent/riscv-ocelot/blob/920fd904/project/build.properties)
*   [project/plugins.sbt](https://github.com/tenstorrent/riscv-ocelot/blob/920fd904/project/plugins.sbt)
*   [src/main/scala/common/types.scala](https://github.com/tenstorrent/riscv-ocelot/blob/920fd904/src/main/scala/common/types.scala)

This page documents the build process and configuration options for the RISC-V Ocelot processor. It explains how to set up the build environment, compile the processor RTL, and configure different processor implementations. For detailed information about the parameterization system, see [Parameterization System](https://deepwiki.com/tenstorrent/riscv-ocelot/6.1-parameterization-system). For information on the continuous integration system, see [Continuous Integration](https://deepwiki.com/tenstorrent/riscv-ocelot/6.2-continuous-integration).

## Prerequisites

Before building the RISC-V Ocelot processor, you need to install the following dependencies:

*   Scala (2.13.10)
*   SBT (1.3.13)
*   RISC-V GNU Toolchain
*   Verilator (v4.034 recommended)
*   Chipyard repository

Sources: [build.sbt](https://github.com/tenstorrent/riscv-ocelot/blob/920fd904/build.sbt)[project/build.properties](https://github.com/tenstorrent/riscv-ocelot/blob/920fd904/project/build.properties)[.circleci/defaults.sh 103-104](https://github.com/tenstorrent/riscv-ocelot/blob/920fd904/.circleci/defaults.sh#L103-L104)

## Development Environment Setup

The RISC-V Ocelot processor is built within the Chipyard framework. The repository structure assumes the code will be integrated as a generator in Chipyard.

Steps to set up the development environment:

1.   Clone Chipyard repository at the specific commit referenced in the Ocelot repository
2.   Install the RISC-V toolchain
3.   Install Verilator
4.   Place the Ocelot repository in the `generators/boom` directory of the Chipyard repository

Sources: [.circleci/prepare-for-rtl-build.sh](https://github.com/tenstorrent/riscv-ocelot/blob/920fd904/.circleci/prepare-for-rtl-build.sh)[.circleci/defaults.sh](https://github.com/tenstorrent/riscv-ocelot/blob/920fd904/.circleci/defaults.sh)[CHIPYARD.hash](https://github.com/tenstorrent/riscv-ocelot/blob/920fd904/CHIPYARD.hash)

## Supported Configurations

RISC-V Ocelot supports several predefined configurations with varying features and performance characteristics:

| Configuration | Description | RV32/RV64 | Vector Extension |
| --- | --- | --- | --- |
| SmallBoomConfig | Minimal configuration, good for testing | RV64 | No |
| MediumBoomConfig | Medium-sized core | RV64 | No |
| LargeBoomConfig | Large out-of-order core | RV64 | No |
| MegaBoomConfig | Maximum performance configuration | RV64 | No |
| SmallRV32BoomConfig | 32-bit variant of SmallBoomConfig | RV32 | No |
| HwachaLargeBoomConfig | Large configuration with Hwacha vector extension | RV64 | Hwacha |

Sources: [.circleci/defaults.sh 141-148](https://github.com/tenstorrent/riscv-ocelot/blob/920fd904/.circleci/defaults.sh#L141-L148)

## Building Process

The build process for RISC-V Ocelot involves several steps, which can be executed manually or through the provided scripts.

### Basic Build Commands

To build a specific configuration, use the following make command in the Chipyard Verilator simulation directory:

```
make CONFIG=<ConfigName>
```

For example, to build the LargeBoomConfig:

```
make CONFIG=LargeBoomConfig
```

This will generate the RTL, compile it with Verilator, and build the simulator.

Sources: [.circleci/do-rtl-build.sh 50-56](https://github.com/tenstorrent/riscv-ocelot/blob/920fd904/.circleci/do-rtl-build.sh#L50-L56)

### Build Outputs

The build process generates several important outputs:

1.   Verilog RTL files
2.   Verilator C++ model
3.   Simulator executable (`simulator-chipyard-<ConfigName>`)

The simulator executable can be used to run various tests and benchmarks.

Sources: [.circleci/build-run-csmith-tests.sh 19-22](https://github.com/tenstorrent/riscv-ocelot/blob/920fd904/.circleci/build-run-csmith-tests.sh#L19-L22)

## Testing

RISC-V Ocelot includes several test suites to verify functionality. Different configurations may use different test sets.

### RISC-V ISA Tests

These tests verify basic ISA compliance:

```
make run-asm-tests-fast
```

### RISC-V Benchmark Tests

These tests run more complex workloads to verify performance:

```
make run-bmark-tests-fast
```

### Randomized Testing

Csmith is used for randomized testing to catch edge cases:

```
./run-csmith.sh --sim <simulator_path> --run <number_of_runs> --nodebug
```

Sources: [.circleci/run-tests.sh](https://github.com/tenstorrent/riscv-ocelot/blob/920fd904/.circleci/run-tests.sh)[.circleci/build-run-csmith-tests.sh](https://github.com/tenstorrent/riscv-ocelot/blob/920fd904/.circleci/build-run-csmith-tests.sh)

## Common Build Scenarios

The following sections provide step-by-step instructions for common build scenarios.

### Building a Small Configuration for Quick Testing

1.   Set up the environment:

```
export RISCV=/path/to/riscv/tools
export PATH=$RISCV/bin:$PATH
export LD_LIBRARY_PATH=$RISCV/lib:$LD_LIBRARY_PATH
```
2.   Build the SmallBoomConfig:

```
cd /path/to/chipyard/sims/verilator
make CONFIG=SmallBoomConfig
```
3.   Run basic tests:

```
make run-asm-tests-fast CONFIG=SmallBoomConfig
```

### Building a Large Configuration for Performance Testing

1.   Set up the environment as above

2.   Build the LargeBoomConfig:

```
cd /path/to/chipyard/sims/verilator
make CONFIG=LargeBoomConfig
```
3.   Run benchmark tests:

```
make run-bmark-tests-fast CONFIG=LargeBoomConfig
```

### Building with Vector Extensions

To build with vector extensions (Hwacha):

1.   Install the ESP toolchain instead of the standard RISC-V toolchain

2.   Set up the environment:

```
export RISCV=/path/to/esp/tools
export PATH=$RISCV/bin:$PATH
export LD_LIBRARY_PATH=$RISCV/lib:$LD_LIBRARY_PATH
```
3.   Build the HwachaLargeBoomConfig:

```
cd /path/to/chipyard/sims/verilator
make CONFIG=HwachaLargeBoomConfig
```
4.   Run vector tests:

```
make run-rv64uv-p-asm-tests CONFIG=HwachaLargeBoomConfig
```

Sources: [.circleci/do-rtl-build.sh 37-47](https://github.com/tenstorrent/riscv-ocelot/blob/920fd904/.circleci/do-rtl-build.sh#L37-L47)[.circleci/run-tests.sh 52-55](https://github.com/tenstorrent/riscv-ocelot/blob/920fd904/.circleci/run-tests.sh#L52-L55)

## Configuration Integration Diagram

The following diagram illustrates how configurations integrate with the build system and relate to the processor architecture:

Sources: [.circleci/defaults.sh 141-148](https://github.com/tenstorrent/riscv-ocelot/blob/920fd904/.circleci/defaults.sh#L141-L148)[.circleci/config.yml 215-244](https://github.com/tenstorrent/riscv-ocelot/blob/920fd904/.circleci/config.yml#L215-L244)[src/main/scala/common/types.scala](https://github.com/tenstorrent/riscv-ocelot/blob/920fd904/src/main/scala/common/types.scala)

## FireSim Integration

For FPGA-accelerated simulation using FireSim, additional configurations are available:

| FireSim Configuration | Base Configuration | Description |
| --- | --- | --- |
| largefireboom | LargeBoomConfig | LargeBoom configuration adapted for FireSim |

To build a FireSim configuration, first set up the FireSim environment according to the FireSim documentation, then use the following commands:

```
cd /path/to/chipyard/sims/firesim
./build-afi.sh largefireboom
```

Various workloads can be run on the FireSim configurations:

*   Buildroot
*   Fedora
*   CoreMark
*   SPEC17

Sources: [.circleci/config.yml 360-383](https://github.com/tenstorrent/riscv-ocelot/blob/920fd904/.circleci/config.yml#L360-L383)[.circleci/defaults.sh 150-151](https://github.com/tenstorrent/riscv-ocelot/blob/920fd904/.circleci/defaults.sh#L150-L151)

## Troubleshooting

Here are some common issues and their solutions:

1.   **Missing toolchain**: Ensure the RISCV environment variable is set correctly to point to your installed RISC-V tools
2.   **Build failures**: Check that you have the correct version of Chipyard as specified in CHIPYARD.hash
3.   **Test failures**: Different configurations support different test types - refer to the configuration table

For more detailed CI/CD information and automated testing, see [Continuous Integration](https://deepwiki.com/tenstorrent/riscv-ocelot/6.2-continuous-integration).

Dismiss
Refresh this wiki

Enter email to refresh
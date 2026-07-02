---
project: tenstorrent.github.io
github: https://github.com/tenstorrent/tenstorrent.github.io
deepwiki: https://deepwiki.com/tenstorrent/tenstorrent.github.io/6-glossary
---

# Glossary

Relevant source files
*   [core/_static/assets/home/discord.svg](https://github.com/tenstorrent/tenstorrent.github.io/blob/eda54884/core/_static/assets/home/discord.svg)
*   [core/aibs/README.md](https://github.com/tenstorrent/tenstorrent.github.io/blob/eda54884/core/aibs/README.md?plain=1)
*   [core/aibs/blackhole/index.rst](https://github.com/tenstorrent/tenstorrent.github.io/blob/eda54884/core/aibs/blackhole/index.rst)
*   [core/aibs/blackhole/installation.md](https://github.com/tenstorrent/tenstorrent.github.io/blob/eda54884/core/aibs/blackhole/installation.md?plain=1)
*   [core/aibs/blackhole/p300.md](https://github.com/tenstorrent/tenstorrent.github.io/blob/eda54884/core/aibs/blackhole/p300.md?plain=1)
*   [core/aibs/blackhole/specifications.md](https://github.com/tenstorrent/tenstorrent.github.io/blob/eda54884/core/aibs/blackhole/specifications.md?plain=1)
*   [core/aibs/blackhole/support.md](https://github.com/tenstorrent/tenstorrent.github.io/blob/eda54884/core/aibs/blackhole/support.md?plain=1)
*   [core/aibs/grayskull/specifications.md](https://github.com/tenstorrent/tenstorrent.github.io/blob/eda54884/core/aibs/grayskull/specifications.md?plain=1)
*   [core/aibs/wormhole/ack.md](https://github.com/tenstorrent/tenstorrent.github.io/blob/eda54884/core/aibs/wormhole/ack.md?plain=1)
*   [core/aibs/wormhole/specifications.md](https://github.com/tenstorrent/tenstorrent.github.io/blob/eda54884/core/aibs/wormhole/specifications.md?plain=1)
*   [core/forge/forge-backend-compiler/index.rst](https://github.com/tenstorrent/tenstorrent.github.io/blob/eda54884/core/forge/forge-backend-compiler/index.rst)
*   [core/forge/forge-frontend-compiler/index.rst](https://github.com/tenstorrent/tenstorrent.github.io/blob/eda54884/core/forge/forge-frontend-compiler/index.rst)
*   [core/forge/index.rst](https://github.com/tenstorrent/tenstorrent.github.io/blob/eda54884/core/forge/index.rst)
*   [core/forge/tools/index.rst](https://github.com/tenstorrent/tenstorrent.github.io/blob/eda54884/core/forge/tools/index.rst)
*   [core/getting-started/README.md](https://github.com/tenstorrent/tenstorrent.github.io/blob/eda54884/core/getting-started/README.md?plain=1)
*   [core/getting-started/model-demos.md](https://github.com/tenstorrent/tenstorrent.github.io/blob/eda54884/core/getting-started/model-demos.md?plain=1)
*   [core/getting-started/vLLM-servers.md](https://github.com/tenstorrent/tenstorrent.github.io/blob/eda54884/core/getting-started/vLLM-servers.md?plain=1)
*   [core/systems/quietbox/quietbox-bh-2/index.rst](https://github.com/tenstorrent/tenstorrent.github.io/blob/eda54884/core/systems/quietbox/quietbox-bh-2/index.rst)
*   [core/systems/quietbox/quietbox-bh-2/qb2-setup-hero2.jpg](https://github.com/tenstorrent/tenstorrent.github.io/blob/eda54884/core/systems/quietbox/quietbox-bh-2/qb2-setup-hero2.jpg)
*   [core/systems/quietbox/quietbox-bh-2/setup.md](https://github.com/tenstorrent/tenstorrent.github.io/blob/eda54884/core/systems/quietbox/quietbox-bh-2/setup.md?plain=1)
*   [core/systems/quietbox/quietbox-bh-2/specifications.md](https://github.com/tenstorrent/tenstorrent.github.io/blob/eda54884/core/systems/quietbox/quietbox-bh-2/specifications.md?plain=1)
*   [core/systems/quietbox/quietbox-bh-2/support-bh-2.md](https://github.com/tenstorrent/tenstorrent.github.io/blob/eda54884/core/systems/quietbox/quietbox-bh-2/support-bh-2.md?plain=1)
*   [core/systems/quietbox/quietbox-bh-2/welcome.md](https://github.com/tenstorrent/tenstorrent.github.io/blob/eda54884/core/systems/quietbox/quietbox-bh-2/welcome.md?plain=1)

This page provides definitions for codebase-specific terms, hardware architecture components, software stack layers, and domain-specific jargon used across the Tenstorrent ecosystem. It serves as a technical reference for onboarding engineers and developers to ensure consistent terminology.

## Hardware Architecture Terms

| Term | Definition | Code/Hardware Context |
| --- | --- | --- |
| **Tensix Core** | The fundamental computational unit of Tenstorrent ASICs, containing specialized hardware for tensor operations and local SRAM. | [core/aibs/blackhole/specifications.md 16-18](https://github.com/tenstorrent/tenstorrent.github.io/blob/eda54884/core/aibs/blackhole/specifications.md?plain=1#L16-L18) |
| **Blackhole** | The latest generation Tenstorrent Networked AI Processor, featuring PCIe Gen 5.0 and integrated RISC-V cores. | [core/systems/quietbox/quietbox-bh-2/specifications.md 35](https://github.com/tenstorrent/tenstorrent.github.io/blob/eda54884/core/systems/quietbox/quietbox-bh-2/specifications.md?plain=1#L35-L35) |
| **Wormhole** | A Tenstorrent AI Processor generation optimized for networking and scaling via QSFP-DD. | [core/aibs/wormhole/specifications.md 3-5](https://github.com/tenstorrent/tenstorrent.github.io/blob/eda54884/core/aibs/wormhole/specifications.md?plain=1#L3-L5) |
| **Grayskull** | A legacy Tenstorrent processor generation (e75/e150). Support is frozen at TT-Metalium v0.55. | [core/aibs/grayskull/specifications.md 15-20](https://github.com/tenstorrent/tenstorrent.github.io/blob/eda54884/core/aibs/grayskull/specifications.md?plain=1#L15-L20) |
| **NoC (Network on Chip)** | The internal communication fabric that connects Tensix cores, RISC-V cores, and memory controllers. | Domain Concept |
| **SRAM** | Fast, local on-chip memory available to each Tensix core (typically 1.5 MB per core on Blackhole). | [core/aibs/blackhole/specifications.md 18](https://github.com/tenstorrent/tenstorrent.github.io/blob/eda54884/core/aibs/blackhole/specifications.md?plain=1#L18-L18) |
| **GDDR6** | Graphics Double Data Rate 6 memory used as the primary off-chip DRAM for Blackhole and Wormhole cards. | [core/aibs/blackhole/p300.md 34](https://github.com/tenstorrent/tenstorrent.github.io/blob/eda54884/core/aibs/blackhole/p300.md?plain=1#L34-L34) |
| **QSFP-DD** | High-speed networking interface (800G on Blackhole, 200G on Wormhole) used for inter-chip and inter-system scaling. | [core/aibs/blackhole/specifications.md 68](https://github.com/tenstorrent/tenstorrent.github.io/blob/eda54884/core/aibs/blackhole/specifications.md?plain=1#L68-L68) |

**Sources:**[core/aibs/blackhole/specifications.md 16-19](https://github.com/tenstorrent/tenstorrent.github.io/blob/eda54884/core/aibs/blackhole/specifications.md?plain=1#L16-L19)[core/aibs/wormhole/specifications.md 3-10](https://github.com/tenstorrent/tenstorrent.github.io/blob/eda54884/core/aibs/wormhole/specifications.md?plain=1#L3-L10)[core/aibs/grayskull/specifications.md 15-20](https://github.com/tenstorrent/tenstorrent.github.io/blob/eda54884/core/aibs/grayskull/specifications.md?plain=1#L15-L20)[core/aibs/blackhole/p300.md 23](https://github.com/tenstorrent/tenstorrent.github.io/blob/eda54884/core/aibs/blackhole/p300.md?plain=1#L23-L23)

## Software Stack Components

### Core Frameworks

*   **TT-Metalium**: The low-level programming SDK providing bare-metal access to Tensix cores and RISC-V processors.
*   **TT-NN**: A high-level neural network library built on top of TT-Metalium for executing optimized kernels.
*   **TT-Forge**: The end-to-end compiler stack that ingests models from PyTorch, JAX, or ONNX. [core/forge/index.rst 1-6](https://github.com/tenstorrent/tenstorrent.github.io/blob/eda54884/core/forge/index.rst#L1-L6)
*   **TT-MLIR**: The middle and backend compiler component within the TT-Forge stack. [core/forge/index.rst 5](https://github.com/tenstorrent/tenstorrent.github.io/blob/eda54884/core/forge/index.rst#L5-L5)

### System Utilities

*   **tt-smi**: The System Management Interface utility for monitoring device telemetry (temperature, power, utilization). [core/getting-started/README.md 140](https://github.com/tenstorrent/tenstorrent.github.io/blob/eda54884/core/getting-started/README.md?plain=1#L140-L140)
*   **tt-flash**: Utility used to flash firmware blobs to Tenstorrent devices. [core/getting-started/README.md 137](https://github.com/tenstorrent/tenstorrent.github.io/blob/eda54884/core/getting-started/README.md?plain=1#L137-L137)
*   **TT-KMD**: The Kernel-Mode Driver required for the host OS to communicate with Tenstorrent hardware. [core/getting-started/README.md 136](https://github.com/tenstorrent/tenstorrent.github.io/blob/eda54884/core/getting-started/README.md?plain=1#L136-L136)
*   **tt-topology**: A tool used to configure mesh connectivity between networked processors. [core/getting-started/vLLM-servers.md 30-33](https://github.com/tenstorrent/tenstorrent.github.io/blob/eda54884/core/getting-started/vLLM-servers.md?plain=1#L30-L33)

### Logical Mapping: Hardware to Software Entities

This diagram illustrates how physical hardware components are represented and accessed through software entities in the Tenstorrent stack.

**Hardware to Software Mapping**

**Sources:**[core/getting-started/README.md 135-141](https://github.com/tenstorrent/tenstorrent.github.io/blob/eda54884/core/getting-started/README.md?plain=1#L135-L141)[core/forge/index.rst 4-6](https://github.com/tenstorrent/tenstorrent.github.io/blob/eda54884/core/forge/index.rst#L4-L6)[core/aibs/blackhole/p300.md 21-24](https://github.com/tenstorrent/tenstorrent.github.io/blob/eda54884/core/aibs/blackhole/p300.md?plain=1#L21-L24)

## System Form Factors

| System | Description | Chip Configuration |
| --- | --- | --- |
| **TT-QuietBox 2** | A liquid-cooled workstation designed for quiet office operation. | 2x Blackhole p300c (4 chips total) [core/systems/quietbox/quietbox-bh-2/specifications.md 35](https://github.com/tenstorrent/tenstorrent.github.io/blob/eda54884/core/systems/quietbox/quietbox-bh-2/specifications.md?plain=1#L35-L35) |
| **TT-LoudBox** | High-density rack-mount or server configurations for data centers. | Variable (e.g., T3000 Wormhole mesh) [core/getting-started/vLLM-servers.md 97](https://github.com/tenstorrent/tenstorrent.github.io/blob/eda54884/core/getting-started/vLLM-servers.md?plain=1#L97-L97) |
| **n150 / n300** | Wormhole-based PCIe add-in cards. | n150 (1 chip), n300 (2 chips) [core/aibs/wormhole/specifications.md 15-18](https://github.com/tenstorrent/tenstorrent.github.io/blob/eda54884/core/aibs/wormhole/specifications.md?plain=1#L15-L18) |
| **p100 / p150** | Blackhole-based PCIe add-in cards. | p100 (1 chip), p150 (1 chip, higher specs) [core/aibs/blackhole/specifications.md 27-30](https://github.com/tenstorrent/tenstorrent.github.io/blob/eda54884/core/aibs/blackhole/specifications.md?plain=1#L27-L30) |

**Sources:**[core/systems/quietbox/quietbox-bh-2/specifications.md 35-47](https://github.com/tenstorrent/tenstorrent.github.io/blob/eda54884/core/systems/quietbox/quietbox-bh-2/specifications.md?plain=1#L35-L47)[core/aibs/wormhole/specifications.md 15-18](https://github.com/tenstorrent/tenstorrent.github.io/blob/eda54884/core/aibs/wormhole/specifications.md?plain=1#L15-L18)[core/aibs/blackhole/specifications.md 27-30](https://github.com/tenstorrent/tenstorrent.github.io/blob/eda54884/core/aibs/blackhole/specifications.md?plain=1#L27-L30)

## Connectivity and Data Standards

### Interconnects

*   **Warp 100 / Warp 400**: Internal bridge connectors for chip-to-chip communication within a system. [core/aibs/wormhole/specifications.md 49](https://github.com/tenstorrent/tenstorrent.github.io/blob/eda54884/core/aibs/wormhole/specifications.md?plain=1#L49-L49)
*   **Samtec ARP6**: High-performance internal cabling used in QuietBox 2 to connect p300c cards. [core/systems/quietbox/quietbox-bh-2/specifications.md 65](https://github.com/tenstorrent/tenstorrent.github.io/blob/eda54884/core/systems/quietbox/quietbox-bh-2/specifications.md?plain=1#L65-L65)

### Data Formats

Tenstorrent hardware supports a wide range of precisions to optimize AI workloads:

*   **BFLOAT16**: Brain Floating Point 16-bit.
*   **BLOCKFP8/4/2**: Block floating-point formats that share an exponent across a block of values for efficiency.
*   **TF32**: TensorFloat-32.

**Sources:**[core/aibs/blackhole/specifications.md 72-81](https://github.com/tenstorrent/tenstorrent.github.io/blob/eda54884/core/aibs/blackhole/specifications.md?plain=1#L72-L81)[core/aibs/wormhole/specifications.md 55-65](https://github.com/tenstorrent/tenstorrent.github.io/blob/eda54884/core/aibs/wormhole/specifications.md?plain=1#L55-L65)

## Software Deployment Concepts

### Deployment Flow: Model to Hardware

The following diagram traces the data and command flow from a high-level model definition down to the execution on Tenstorrent hardware.

**Model Execution Data Flow**

**Sources:**[core/forge/index.rst 1-10](https://github.com/tenstorrent/tenstorrent.github.io/blob/eda54884/core/forge/index.rst#L1-L10)[core/getting-started/model-demos.md 46-52](https://github.com/tenstorrent/tenstorrent.github.io/blob/eda54884/core/getting-started/model-demos.md?plain=1#L46-L52)[core/getting-started/README.md 94-102](https://github.com/tenstorrent/tenstorrent.github.io/blob/eda54884/core/getting-started/README.md?plain=1#L94-L102)

### Environment Terms

*   **tt-installer**: An orchestration script that automates the installation of drivers, firmware, and containers. [core/getting-started/README.md 43-46](https://github.com/tenstorrent/tenstorrent.github.io/blob/eda54884/core/getting-started/README.md?plain=1#L43-L46)
*   **HugePages**: A Linux kernel feature configured by `tt-system-tools` to reserve large memory blocks for high-performance DMA. [core/getting-started/README.md 139](https://github.com/tenstorrent/tenstorrent.github.io/blob/eda54884/core/getting-started/README.md?plain=1#L139-L139)
*   **tt-metalium-models**: A containerized environment containing pre-built model demos and their dependencies. [core/getting-started/model-demos.md 34-38](https://github.com/tenstorrent/tenstorrent.github.io/blob/eda54884/core/getting-started/model-demos.md?plain=1#L34-L38)
*   **vLLM**: A high-throughput LLM serving engine integrated with Tenstorrent hardware via `tt-inference-server`. [core/getting-started/vLLM-servers.md 11](https://github.com/tenstorrent/tenstorrent.github.io/blob/eda54884/core/getting-started/vLLM-servers.md?plain=1#L11-L11)

**Sources:**[core/getting-started/README.md 43-141](https://github.com/tenstorrent/tenstorrent.github.io/blob/eda54884/core/getting-started/README.md?plain=1#L43-L141)[core/getting-started/model-demos.md 34-38](https://github.com/tenstorrent/tenstorrent.github.io/blob/eda54884/core/getting-started/model-demos.md?plain=1#L34-L38)[core/getting-started/vLLM-servers.md 9-11](https://github.com/tenstorrent/tenstorrent.github.io/blob/eda54884/core/getting-started/vLLM-servers.md?plain=1#L9-L11)

Dismiss
Refresh this wiki

Enter email to refresh
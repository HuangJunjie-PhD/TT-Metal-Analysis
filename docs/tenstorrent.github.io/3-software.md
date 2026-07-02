---
project: tenstorrent.github.io
github: https://github.com/tenstorrent/tenstorrent.github.io
deepwiki: https://deepwiki.com/tenstorrent/tenstorrent.github.io/3-software
---

# Software

Relevant source files
*   [core/_static/assets/home/tt-lang.png](https://github.com/tenstorrent/tenstorrent.github.io/blob/eda54884/core/_static/assets/home/tt-lang.png)
*   [core/_static/assets/home/tt-metalium.png](https://github.com/tenstorrent/tenstorrent.github.io/blob/eda54884/core/_static/assets/home/tt-metalium.png)
*   [core/_static/assets/home/tt-mlir.png](https://github.com/tenstorrent/tenstorrent.github.io/blob/eda54884/core/_static/assets/home/tt-mlir.png)
*   [core/_static/assets/home/tt-nn.png](https://github.com/tenstorrent/tenstorrent.github.io/blob/eda54884/core/_static/assets/home/tt-nn.png)
*   [core/_templates/home.html](https://github.com/tenstorrent/tenstorrent.github.io/blob/eda54884/core/_templates/home.html)
*   [core/getting-started/README.md](https://github.com/tenstorrent/tenstorrent.github.io/blob/eda54884/core/getting-started/README.md?plain=1)
*   [core/getting-started/model-demos.md](https://github.com/tenstorrent/tenstorrent.github.io/blob/eda54884/core/getting-started/model-demos.md?plain=1)
*   [core/getting-started/vLLM-servers.md](https://github.com/tenstorrent/tenstorrent.github.io/blob/eda54884/core/getting-started/vLLM-servers.md?plain=1)

This page provides an overview of the Tenstorrent software stack, including installation processes, software frameworks, and system utilities. The software stack enables users to develop, deploy, and monitor machine learning workloads on Tenstorrent hardware.

## Software Stack Architecture

### Tenstorrent Software Stack Overview

The Tenstorrent software stack is organized in layers:

1.   **System Drivers**: The foundation layer that communicates directly with hardware. 
    *   **TT-KMD**: Kernel Mode Driver for device access [core/getting-started/README.md 136](https://github.com/tenstorrent/tenstorrent.github.io/blob/eda54884/core/getting-started/README.md?plain=1#L136-L136)
    *   **TT-Flash**: Firmware update utility [core/getting-started/README.md 137](https://github.com/tenstorrent/tenstorrent.github.io/blob/eda54884/core/getting-started/README.md?plain=1#L137-L137)
    *   **HugePages**: System tool for improving memory performance [core/getting-started/README.md 139](https://github.com/tenstorrent/tenstorrent.github.io/blob/eda54884/core/getting-started/README.md?plain=1#L139-L139)

2.   **Low-Level Software Stack**: Hardware abstraction and optimization. 
    *   **TT-Metalium**: Low-level hardware abstraction SDK [core/getting-started/README.md 94](https://github.com/tenstorrent/tenstorrent.github.io/blob/eda54884/core/getting-started/README.md?plain=1#L94-L94)
    *   **TT-MLIR**: Compiler infrastructure for hardware optimization.

3.   **High-Level Software Stack**: User-facing APIs for ML application development. 
    *   **TT-NN**: Neural network operation library for running models [core/getting-started/README.md 95](https://github.com/tenstorrent/tenstorrent.github.io/blob/eda54884/core/getting-started/README.md?plain=1#L95-L95)
    *   **TT-Forge**: MLIR-based compiler stack supporting PyTorch, JAX, and TensorFlow.

4.   **Management & Deployment Tools**: Utilities for monitoring and serving. 
    *   **TT-SMI**: System Management Interface for telemetry [core/getting-started/README.md 140](https://github.com/tenstorrent/tenstorrent.github.io/blob/eda54884/core/getting-started/README.md?plain=1#L140-L140)
    *   **TT-Inference-Server**: Project for deploying LLMs using vLLM [core/getting-started/vLLM-servers.md 11](https://github.com/tenstorrent/tenstorrent.github.io/blob/eda54884/core/getting-started/vLLM-servers.md?plain=1#L11-L11)
    *   **tt-installer**: Streamlined automated installation utility [core/getting-started/README.md 45](https://github.com/tenstorrent/tenstorrent.github.io/blob/eda54884/core/getting-started/README.md?plain=1#L45-L45)

Sources: [core/getting-started/README.md 94-141](https://github.com/tenstorrent/tenstorrent.github.io/blob/eda54884/core/getting-started/README.md?plain=1#L94-L141)[core/getting-started/vLLM-servers.md 11-12](https://github.com/tenstorrent/tenstorrent.github.io/blob/eda54884/core/getting-started/vLLM-servers.md?plain=1#L11-L12)

## Installation & Setup

### Software Installation Flow

Tenstorrent recommends using the `tt-installer` script to automate the setup process. It handles dependencies (`curl`, `jq`), drivers, and can install containerized development environments via Podman [core/getting-started/README.md 45-58](https://github.com/tenstorrent/tenstorrent.github.io/blob/eda54884/core/getting-started/README.md?plain=1#L45-L58)

For detailed step-by-step instructions, see [Installation & Setup](https://deepwiki.com/tenstorrent/tenstorrent.github.io/3.1-installation-and-setup).

Sources: [core/getting-started/README.md 43-102](https://github.com/tenstorrent/tenstorrent.github.io/blob/eda54884/core/getting-started/README.md?plain=1#L43-L102)

## Software Frameworks and SDKs

Tenstorrent provides multiple entry points depending on the level of optimization required:

*   **TT-NN**: A high-level library of neural network operations. It is the recommended path for most AI model developers [core/getting-started/README.md 95](https://github.com/tenstorrent/tenstorrent.github.io/blob/eda54884/core/getting-started/README.md?plain=1#L95-L95)
*   **TT-Metalium**: The bare-metal programming SDK. It allows for writing custom C++ kernels that run directly on Tensix cores [core/getting-started/README.md 118](https://github.com/tenstorrent/tenstorrent.github.io/blob/eda54884/core/getting-started/README.md?plain=1#L118-L118)
*   **TT-Forge**: A compiler-driven approach that ingests models from PyTorch or ONNX and compiles them for Tenstorrent hardware.

For a deeper dive into these layers, see [Software Stack](https://deepwiki.com/tenstorrent/tenstorrent.github.io/3.2-software-stack).

Sources: [core/getting-started/README.md 94-119](https://github.com/tenstorrent/tenstorrent.github.io/blob/eda54884/core/getting-started/README.md?plain=1#L94-L119)[core/_templates/home.html 274-290](https://github.com/tenstorrent/tenstorrent.github.io/blob/eda54884/core/_templates/home.html#L274-L290)

## Model Deployment & Inference

Tenstorrent supports production-grade model serving through integration with **vLLM** and the **TT-Inference-Server**[core/getting-started/vLLM-servers.md 11](https://github.com/tenstorrent/tenstorrent.github.io/blob/eda54884/core/getting-started/vLLM-servers.md?plain=1#L11-L11)

### Deployment Workflow

1.   **Hardware Selection**: Identifying the target system (e.g., TT-QuietBox, n150s, p150b) [core/getting-started/vLLM-servers.md 97](https://github.com/tenstorrent/tenstorrent.github.io/blob/eda54884/core/getting-started/vLLM-servers.md?plain=1#L97-L97)
2.   **Model Access**: Authenticating with Hugging Face to download gated models like Llama-3.3-70B [core/getting-started/vLLM-servers.md 66-78](https://github.com/tenstorrent/tenstorrent.github.io/blob/eda54884/core/getting-started/vLLM-servers.md?plain=1#L66-L78)
3.   **Containerized Execution**: Running models within pre-configured Docker environments for consistent performance [core/getting-started/vLLM-servers.md 37-43](https://github.com/tenstorrent/tenstorrent.github.io/blob/eda54884/core/getting-started/vLLM-servers.md?plain=1#L37-L43)

For more information, see [Model Deployment & Inference](https://deepwiki.com/tenstorrent/tenstorrent.github.io/3.4-model-deployment-and-inference).

Sources: [core/getting-started/vLLM-servers.md 11-98](https://github.com/tenstorrent/tenstorrent.github.io/blob/eda54884/core/getting-started/vLLM-servers.md?plain=1#L11-L98)

## System Utilities & Tools

A suite of tools is provided to manage Tenstorrent hardware:

*   **tt-smi**: Displays real-time telemetry such as power, temperature, and clock speeds [core/getting-started/README.md 33](https://github.com/tenstorrent/tenstorrent.github.io/blob/eda54884/core/getting-started/README.md?plain=1#L33-L33)
*   **tt-topology**: Configures the system-level mesh topology for multi-card Wormhole systems [core/getting-started/vLLM-servers.md 30-33](https://github.com/tenstorrent/tenstorrent.github.io/blob/eda54884/core/getting-started/vLLM-servers.md?plain=1#L30-L33)
*   **tt-flash**: Used to update the on-device firmware [core/getting-started/README.md 137](https://github.com/tenstorrent/tenstorrent.github.io/blob/eda54884/core/getting-started/README.md?plain=1#L137-L137)
*   **TT-NN Visualizer**: A tool for debugging and visualizing tensor operations.

For details, see [System Utilities & Tools](https://deepwiki.com/tenstorrent/tenstorrent.github.io/3.3-system-utilities-and-tools).

Sources: [core/getting-started/README.md 33-140](https://github.com/tenstorrent/tenstorrent.github.io/blob/eda54884/core/getting-started/README.md?plain=1#L33-L140)[core/getting-started/vLLM-servers.md 30-33](https://github.com/tenstorrent/tenstorrent.github.io/blob/eda54884/core/getting-started/vLLM-servers.md?plain=1#L30-L33)

## Support & Resources

*   **Model Demos**: Pre-built examples for LLMs, Speech-to-Text, and Vision models are available in the `tt-metalium-models` container [core/getting-started/model-demos.md 37-68](https://github.com/tenstorrent/tenstorrent.github.io/blob/eda54884/core/getting-started/model-demos.md?plain=1#L37-L68)
*   **Support Portal**: Technical issues can be raised at the [Tenstorrent Support Portal](https://tenstorrent.atlassian.net/servicedesk/customer/portal/1)[core/getting-started/model-demos.md 80](https://github.com/tenstorrent/tenstorrent.github.io/blob/eda54884/core/getting-started/model-demos.md?plain=1#L80-L80)

Sources: [core/getting-started/model-demos.md 37-80](https://github.com/tenstorrent/tenstorrent.github.io/blob/eda54884/core/getting-started/model-demos.md?plain=1#L37-L80)

Dismiss
Refresh this wiki

Enter email to refresh
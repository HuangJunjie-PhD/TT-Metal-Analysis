---
project: tt-studio
github: https://github.com/tenstorrent/tt-studio
deepwiki: https://deepwiki.com/tenstorrent/tt-studio/2-getting-started
---

# Getting Started

Relevant source files
*   [README.md](https://github.com/tenstorrent/tt-studio/blob/a6d347af/README.md?plain=1)
*   [app/.env.default](https://github.com/tenstorrent/tt-studio/blob/a6d347af/app/.env.default)
*   [app/README.md](https://github.com/tenstorrent/tt-studio/blob/a6d347af/app/README.md?plain=1)
*   [app/frontend/index.html](https://github.com/tenstorrent/tt-studio/blob/a6d347af/app/frontend/index.html)
*   [app/frontend/src/components/chatui/StreamingMessage.tsx](https://github.com/tenstorrent/tt-studio/blob/a6d347af/app/frontend/src/components/chatui/StreamingMessage.tsx)
*   [run.py](https://github.com/tenstorrent/tt-studio/blob/a6d347af/run.py)
*   [startup.sh](https://github.com/tenstorrent/tt-studio/blob/a6d347af/startup.sh)

This guide provides comprehensive instructions for setting up and running TT-Studio, a platform for deploying and interacting with machine learning models with optimized support for Tenstorrent hardware. This page covers the installation process, basic configuration, and how to start the application.

## System Requirements

Before installing TT-Studio, ensure your system meets the following requirements:

*   **Python 3.8+**: Required for running the `run.py` orchestration script [README.md 28](https://github.com/tenstorrent/tt-studio/blob/a6d347af/README.md?plain=1#L28-L28)
*   **Docker**: All services run as containerized applications. You must be able to run Docker commands without `sudo` by adding your user to the `docker` group [README.md 29-37](https://github.com/tenstorrent/tt-studio/blob/a6d347af/README.md?plain=1#L29-L37)
*   **Storage**: Sufficient disk space for model weights and persistent data, stored in `HOST_PERSISTENT_STORAGE_VOLUME`[app/.env.default 6](https://github.com/tenstorrent/tt-studio/blob/a6d347af/app/.env.default#L6-L6)
*   **Hardware**: Tenstorrent AI accelerator hardware is automatically detected when available. Alternatively, you can connect to [remote endpoints](https://github.com/tenstorrent/tt-studio/blob/a6d347af/remote%20endpoints)[README.md 159-160](https://github.com/tenstorrent/tt-studio/blob/a6d347af/README.md?plain=1#L159-L160)

Sources: [README.md 28-46](https://github.com/tenstorrent/tt-studio/blob/a6d347af/README.md?plain=1#L28-L46)[README.md 159-160](https://github.com/tenstorrent/tt-studio/blob/a6d347af/README.md?plain=1#L159-L160)[app/.env.default 5-7](https://github.com/tenstorrent/tt-studio/blob/a6d347af/app/.env.default#L5-L7)

## Installation and Setup

TT-Studio uses `run.py` as the main entry point for setup and deployment [run.py 5-12](https://github.com/tenstorrent/tt-studio/blob/a6d347af/run.py#L5-L12) This script replaces the deprecated `startup.sh`[startup.sh 2-7](https://github.com/tenstorrent/tt-studio/blob/a6d347af/startup.sh#L2-L7) It automates environment configuration, frontend dependency installation, and Docker service orchestration.

### Quick Setup (Easy Mode)

For users who want to start immediately with default settings:

`git clone https://github.com/tenstorrent/tt-studio.git && cd tt-studio && python3 run.py --easy`
**What happens during setup:**

1.   **Environment Configuration**: Generates `.env` from `.env.default`[run.py 84-85](https://github.com/tenstorrent/tt-studio/blob/a6d347af/run.py#L84-L85)
2.   **Dependency Management**: Installs node modules and initializes artifacts [run.py 9-12](https://github.com/tenstorrent/tt-studio/blob/a6d347af/run.py#L9-L12)
3.   **Docker Orchestration**: Launches the core services using `docker-compose.yml`[run.py 80](https://github.com/tenstorrent/tt-studio/blob/a6d347af/run.py#L80-L80)
4.   **Service Health Checks**: Monitors services until they are ready for use [run.py 101-109](https://github.com/tenstorrent/tt-studio/blob/a6d347af/run.py#L101-L109)

For details, see [Installation and Setup](https://deepwiki.com/tenstorrent/tt-studio/2.1-installation-and-setup).

Sources: [README.md 54-71](https://github.com/tenstorrent/tt-studio/blob/a6d347af/README.md?plain=1#L54-L71)[run.py 5-26](https://github.com/tenstorrent/tt-studio/blob/a6d347af/run.py#L5-L26)[run.py 80-99](https://github.com/tenstorrent/tt-studio/blob/a6d347af/run.py#L80-L99)

## System Architecture Overview

TT-Studio orchestrates multiple Docker services. The architecture includes a React frontend, a Django backend, an AI Agent, and specialized services for inference and data storage.

### Code-to-System Mapping

The following diagram bridges the high-level services to their respective code entities and ports.

Sources: [README.md 75-77](https://github.com/tenstorrent/tt-studio/blob/a6d347af/README.md?plain=1#L75-L77)[README.md 161-168](https://github.com/tenstorrent/tt-studio/blob/a6d347af/README.md?plain=1#L161-L168)[run.py 80-109](https://github.com/tenstorrent/tt-studio/blob/a6d347af/run.py#L80-L109)[app/.env.default 26](https://github.com/tenstorrent/tt-studio/blob/a6d347af/app/.env.default#L26-L26)

## Tenstorrent Hardware Integration

TT-Studio is designed to detect and utilize Tenstorrent hardware automatically. If `/dev/tenstorrent` is present, the system applies hardware-specific configurations via `docker-compose.tt-hardware.yml`[run.py 83](https://github.com/tenstorrent/tt-studio/blob/a6d347af/run.py#L83-L83)

*   **Device Detection**: The system checks for local AI accelerators during startup.
*   **Resource Allocation**: Models are deployed to specific chips (e.g., `Device ID 0`).
*   **Remote Access**: If local hardware is unavailable, the system can be configured to use remote Tenstorrent endpoints [README.md 104](https://github.com/tenstorrent/tt-studio/blob/a6d347af/README.md?plain=1#L104-L104)

For details, see [Tenstorrent Hardware Integration](https://deepwiki.com/tenstorrent/tt-studio/2.2-tenstorrent-hardware-integration).

Sources: [README.md 159-161](https://github.com/tenstorrent/tt-studio/blob/a6d347af/README.md?plain=1#L159-L161)[run.py 83](https://github.com/tenstorrent/tt-studio/blob/a6d347af/run.py#L83-L83)[app/README.md 48-51](https://github.com/tenstorrent/tt-studio/blob/a6d347af/app/README.md?plain=1#L48-L51)

## Environment Configuration

Configuration is managed via the `app/.env` file. A template is provided in `app/.env.default`[app/.env.default 1-5](https://github.com/tenstorrent/tt-studio/blob/a6d347af/app/.env.default#L1-L5)

### Key Configuration Variables

| Variable | Description |
| --- | --- |
| `JWT_SECRET` | Secret key for JWT authentication [app/.env.default 18](https://github.com/tenstorrent/tt-studio/blob/a6d347af/app/.env.default#L18-L18) |
| `HF_TOKEN` | Hugging Face token for downloading model weights [app/.env.default 24](https://github.com/tenstorrent/tt-studio/blob/a6d347af/app/.env.default#L24-L24) |
| `DOCKER_CONTROL_SERVICE_URL` | URL for the secure Docker management API [app/.env.default 26](https://github.com/tenstorrent/tt-studio/blob/a6d347af/app/.env.default#L26-L26) |
| `TT_INFERENCE_ARTIFACT_BRANCH` | Specifies the branch/version of inference tools to use [app/.env.default 15](https://github.com/tenstorrent/tt-studio/blob/a6d347af/app/.env.default#L15-L15) |

For a comprehensive guide to all variables, see [Environment Configuration](https://deepwiki.com/tenstorrent/tt-studio/2.3-environment-configuration).

Sources: [app/.env.default 1-63](https://github.com/tenstorrent/tt-studio/blob/a6d347af/app/.env.default#L1-L63)[run.py 84-85](https://github.com/tenstorrent/tt-studio/blob/a6d347af/run.py#L84-L85)

## Operations and Maintenance

### Common Commands

| Task | Command |
| --- | --- |
| **Start (Normal)** | `python3 run.py` |
| **Start (Dev Mode)** | `python3 run.py --dev`[run.py 18](https://github.com/tenstorrent/tt-studio/blob/a6d347af/run.py#L18-L18) |
| **Stop & Cleanup** | `python3 run.py --cleanup`[run.py 19](https://github.com/tenstorrent/tt-studio/blob/a6d347af/run.py#L19-L19) |
| **Full Reset** | `python3 run.py --cleanup-all`[run.py 20](https://github.com/tenstorrent/tt-studio/blob/a6d347af/run.py#L20-L20) |

### Troubleshooting

Common issues include Docker permission errors, missing Hugging Face tokens, or port conflicts (especially ports 3000, 8000, and 8001).

For detailed solutions, see [Troubleshooting](https://deepwiki.com/tenstorrent/tt-studio/2.4-troubleshooting).

Sources: [README.md 79-85](https://github.com/tenstorrent/tt-studio/blob/a6d347af/README.md?plain=1#L79-L85)[run.py 14-26](https://github.com/tenstorrent/tt-studio/blob/a6d347af/run.py#L14-L26)[app/README.md 27-36](https://github.com/tenstorrent/tt-studio/blob/a6d347af/app/README.md?plain=1#L27-L36)

This wiki is featured in the [repository](https://github.com/tenstorrent/tt-studio/blob/main/README.md)

Dismiss
Refresh this wiki

Enter email to refresh
---
project: tt-studio
github: https://github.com/tenstorrent/tt-studio
deepwiki: https://deepwiki.com/tenstorrent/tt-studio
---

# Overview

 Relevant source files

* [README.md](https://github.com/tenstorrent/tt-studio/blob/a6d347af/README.md?plain=1)
* [app/README.md](https://github.com/tenstorrent/tt-studio/blob/a6d347af/app/README.md?plain=1)
* [app/docker-compose.yml](https://github.com/tenstorrent/tt-studio/blob/a6d347af/app/docker-compose.yml)
* [app/frontend/index.html](https://github.com/tenstorrent/tt-studio/blob/a6d347af/app/frontend/index.html)
* [startup.sh](https://github.com/tenstorrent/tt-studio/blob/a6d347af/startup.sh)

TT-Studio is a comprehensive platform designed for the rapid deployment and management of AI inference models, specifically optimized for **Tenstorrent hardware**. It provides a containerized environment that integrates the [TT Inference Server](https://github.com/tenstorrent/tt-studio/blob/a6d347af/TT Inference Server) and [TT-Metal](https://github.com/tenstorrent/tt-studio/blob/a6d347af/TT-Metal) frameworks into a user-friendly web interface [README.md10-14](https://github.com/tenstorrent/tt-studio/blob/a6d347af/README.md?plain=1#L10-L14) The platform supports diverse AI tasks including conversational LLMs, object detection (YOLO), image generation (Stable Diffusion), and speech-to-text (Whisper) [README.md90-98](https://github.com/tenstorrent/tt-studio/blob/a6d347af/README.md?plain=1#L90-L98)

## System Architecture

TT-Studio implements a microservices-based architecture orchestrated via Docker. It bridges the gap between high-level user interactions and low-level hardware acceleration.

Sources:

* [app/docker-compose.yml5-155](https://github.com/tenstorrent/tt-studio/blob/a6d347af/app/docker-compose.yml#L5-L155)
* [README.md157-170](https://github.com/tenstorrent/tt-studio/blob/a6d347af/README.md?plain=1#L157-L170)

## Key Components

### 1. Frontend Application (`tt_studio_frontend`)

A React-based SPA (Single Page Application) that serves as the primary control plane. It includes a stepper-based deployment wizard, a real-time chat interface with streaming support, and management dashboards for RAG (Retrieval-Augmented Generation) [app/docker-compose.yml78-87](https://github.com/tenstorrent/tt-studio/blob/a6d347af/app/docker-compose.yml#L78-L87) [app/frontend/index.html1-20](https://github.com/tenstorrent/tt-studio/blob/a6d347af/app/frontend/index.html#L1-L20)

### 2. Backend API (`tt_studio_backend`)

A Django-based service running via `uvicorn` [app/docker-compose.yml23-24](https://github.com/tenstorrent/tt-studio/blob/a6d347af/app/docker-compose.yml#L23-L24) It manages:

* **Model Lifecycle**: Tracking deployed models and their health.
* **RAG Pipeline**: Ingesting documents into the vector database.
* **Hardware Mapping**: Identifying available Tenstorrent devices for model placement.
* **Authentication**: Handling `JWT_SECRET` for secure inter-service communication [app/docker-compose.yml38](https://github.com/tenstorrent/tt-studio/blob/a6d347af/app/docker-compose.yml#L38-L38)

### 3. Docker Control Service (DCS)

A security-hardened FastAPI service that acts as a proxy for the Docker socket. Instead of mounting `/var/run/docker.sock` directly into the backend (which poses security risks), the backend communicates with the DCS to pull images and manage model containers [app/docker-compose.yml55-57](https://github.com/tenstorrent/tt-studio/blob/a6d347af/app/docker-compose.yml#L55-L57)

### 4. Agent Service (`tt_studio_agent`)

A dedicated service for multi-LLM orchestration and autonomous tasks. It supports auto-discovery of model containers and integrates with external tools like Tavily for search-augmented capabilities [app/docker-compose.yml89-119](https://github.com/tenstorrent/tt-studio/blob/a6d347af/app/docker-compose.yml#L89-L119)

### 5. Vector Database (`tt_studio_chroma`)

Uses `chromadb/chroma` to store document embeddings for semantic search, enabling the Chat UI to perform Retrieval-Augmented Generation [app/docker-compose.yml121-143](https://github.com/tenstorrent/tt-studio/blob/a6d347af/app/docker-compose.yml#L121-L143)

## Hardware and Data Flow

TT-Studio is designed to operate in various modes, from local development on a laptop to high-performance inference on Tenstorrent servers.

### Code Entity to Infrastructure Mapping

The following diagram illustrates how specific code components interact with the system's infrastructure.

Sources:

* [startup.sh1-13](https://github.com/tenstorrent/tt-studio/blob/a6d347af/startup.sh#L1-L13)
* [app/docker-compose.yml15-17](https://github.com/tenstorrent/tt-studio/blob/a6d347af/app/docker-compose.yml#L15-L17) [app/docker-compose.yml59-63](https://github.com/tenstorrent/tt-studio/blob/a6d347af/app/docker-compose.yml#L59-L63) [app/docker-compose.yml149-155](https://github.com/tenstorrent/tt-studio/blob/a6d347af/app/docker-compose.yml#L149-L155)
* [app/README.md13-23](https://github.com/tenstorrent/tt-studio/blob/a6d347af/app/README.md?plain=1#L13-L23)

### Inference Port Mapping

Individual AI models are isolated into separate containers. By default, these models are exposed on host ports starting from **7000+** [README.md77](https://github.com/tenstorrent/tt-studio/blob/a6d347af/README.md?plain=1#L77-L77) [README.md148-151](https://github.com/tenstorrent/tt-studio/blob/a6d347af/README.md?plain=1#L148-L151) This allows for fine-grained resource management and avoids port collisions between different model architectures (e.g., Llama 3.1 vs. YOLOv4).

## Deployment Modes

| Mode | Entry Point | Description |
| --- | --- | --- |
| **Easy Mode** | `python3 run.py --easy` | Automated setup using defaults and prompting only for essential tokens (e.g., Hugging Face) [README.md54-58](https://github.com/tenstorrent/tt-studio/blob/a6d347af/README.md?plain=1#L54-L58) |
| **Dev Mode** | `python3 run.py --dev` | Mounts local directories (e.g., `./backend`) for live code reloading and hot-module replacement in the frontend [app/README.md40-63](https://github.com/tenstorrent/tt-studio/blob/a6d347af/app/README.md?plain=1#L40-L63) [app/docker-compose.yml60-61](https://github.com/tenstorrent/tt-studio/blob/a6d347af/app/docker-compose.yml#L60-L61) |
| **Hardware Mode** | `python3 run.py` | Automatically detects `/dev/tenstorrent` and configures the environment to use Tenstorrent AI accelerators [README.md159-161](https://github.com/tenstorrent/tt-studio/blob/a6d347af/README.md?plain=1#L159-L161) |

## Getting Started

To begin using TT-Studio, ensure you have **Python 3.8+** and **Docker** installed, and that your user is part of the `docker` group [README.md28-39](https://github.com/tenstorrent/tt-studio/blob/a6d347af/README.md?plain=1#L28-L39)

Sources:

* [README.md48-72](https://github.com/tenstorrent/tt-studio/blob/a6d347af/README.md?plain=1#L48-L72)
* [startup.sh70-91](https://github.com/tenstorrent/tt-studio/blob/a6d347af/startup.sh#L70-L91)

Refresh this wiki
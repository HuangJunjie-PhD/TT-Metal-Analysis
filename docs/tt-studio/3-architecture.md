---
project: tt-studio
github: https://github.com/tenstorrent/tt-studio
deepwiki: https://deepwiki.com/tenstorrent/tt-studio/3-architecture
---

# Architecture

Relevant source files
*   [README.md](https://github.com/tenstorrent/tt-studio/blob/a6d347af/README.md?plain=1)
*   [app/README.md](https://github.com/tenstorrent/tt-studio/blob/a6d347af/app/README.md?plain=1)
*   [app/docker-compose.yml](https://github.com/tenstorrent/tt-studio/blob/a6d347af/app/docker-compose.yml)
*   [app/frontend/index.html](https://github.com/tenstorrent/tt-studio/blob/a6d347af/app/frontend/index.html)
*   [startup.sh](https://github.com/tenstorrent/tt-studio/blob/a6d347af/startup.sh)

This document provides a comprehensive high-level architectural overview of TT-Studio's system components, their interactions, and deployment structure. It covers the containerized service architecture, frontend-backend integration patterns, and inter-service communication flows.

For detailed information about individual Docker services and orchestration, see [Docker Container Architecture](https://deepwiki.com/tenstorrent/tt-studio/3.1-docker-container-architecture). For frontend component details, see [Frontend Architecture](https://deepwiki.com/tenstorrent/tt-studio/3.2-frontend-architecture). For backend service implementation, see [Backend Architecture](https://deepwiki.com/tenstorrent/tt-studio/3.3-backend-architecture).

## System Overview

TT-Studio is architected as a multi-service containerized application with core services orchestrated through Docker Compose. The system provides a web-based interface for AI model management and interaction, with specialized services handling different aspects of the platform.

### Core Services Architecture

**Sources:**[app/docker-compose.yml 5-149](https://github.com/tenstorrent/tt-studio/blob/a6d347af/app/docker-compose.yml#L5-L149)[app/docker-compose.yml 23-24](https://github.com/tenstorrent/tt-studio/blob/a6d347af/app/docker-compose.yml#L23-L24)[app/docker-compose.yml 55-57](https://github.com/tenstorrent/tt-studio/blob/a6d347af/app/docker-compose.yml#L55-L57)[app/frontend/index.html 15-18](https://github.com/tenstorrent/tt-studio/blob/a6d347af/app/frontend/index.html#L15-L18)

### Service Responsibilities

| Service | Container Name | Purpose | Key Technologies |
| --- | --- | --- | --- |
| Frontend | `tt_studio_frontend` | React web application serving the user interface | React, Vite, TypeScript |
| Backend API | `tt_studio_backend` | Main Django API server handling requests and orchestration | Django, Uvicorn, Python |
| Agent | `tt_studio_agent` | Autonomous agent service for multi-LLM tasks | FastAPI, Python |
| Vector DB | `tt_studio_chroma` | Document storage and retrieval for RAG functionality | ChromaDB |
| Docker Control | `docker-control-service` | Secure layer for managing model containers and hardware | FastAPI, Docker SDK |

**Sources:**[app/docker-compose.yml 6-143](https://github.com/tenstorrent/tt-studio/blob/a6d347af/app/docker-compose.yml#L6-L143)[README.md 161-168](https://github.com/tenstorrent/tt-studio/blob/a6d347af/README.md?plain=1#L161-L168)[app/docker-compose.yml 55-57](https://github.com/tenstorrent/tt-studio/blob/a6d347af/app/docker-compose.yml#L55-L57)

## Container Orchestration and Networking

TT-Studio uses Docker Compose to orchestrate multiple services within a custom bridge network named `tt_studio_network`. The architecture supports both development and production deployment modes with optional Tenstorrent hardware integration.

### Docker Network Architecture

**Sources:**[app/docker-compose.yml 149-154](https://github.com/tenstorrent/tt-studio/blob/a6d347af/app/docker-compose.yml#L149-L154)[app/docker-compose.yml 55-57](https://github.com/tenstorrent/tt-studio/blob/a6d347af/app/docker-compose.yml#L55-L57)[app/docker-compose.yml 74-76](https://github.com/tenstorrent/tt-studio/blob/a6d347af/app/docker-compose.yml#L74-L76)[app/docker-compose.yml 58-59](https://github.com/tenstorrent/tt-studio/blob/a6d347af/app/docker-compose.yml#L58-L59)

### Service Dependencies and Health Checks

The services have a defined startup order managed through Docker Compose dependencies and health checks to ensure the system initializes correctly:

1.   `tt_studio_chroma` starts first with health check on `/api/v1/heartbeat`. [app/docker-compose.yml 137-142](https://github.com/tenstorrent/tt-studio/blob/a6d347af/app/docker-compose.yml#L137-L142)
2.   `tt_studio_backend` waits for Chroma to be healthy, then performs its own health check on `/up/`. [app/docker-compose.yml 25-27](https://github.com/tenstorrent/tt-studio/blob/a6d347af/app/docker-compose.yml#L25-L27)[app/docker-compose.yml 65-73](https://github.com/tenstorrent/tt-studio/blob/a6d347af/app/docker-compose.yml#L65-L73)
3.   `tt_studio_frontend` and `tt_studio_agent` wait for the backend health check to pass before starting. [app/docker-compose.yml 82-84](https://github.com/tenstorrent/tt-studio/blob/a6d347af/app/docker-compose.yml#L82-L84)[app/docker-compose.yml 93-95](https://github.com/tenstorrent/tt-studio/blob/a6d347af/app/docker-compose.yml#L93-L95)

## Frontend Application Structure

The frontend is a React application built with Vite, featuring a modular component architecture. For a deep dive into component hierarchy and state management, see [Frontend Architecture](https://deepwiki.com/tenstorrent/tt-studio/3.2-frontend-architecture).

### Frontend-Backend Communication

The frontend communicates with backend services through a Vite development proxy (in dev mode) or direct container routing. Key backend API categories include:

| Category | Endpoint Prefix | Purpose |
| --- | --- | --- |
| Docker | `/docker/` | Container and image management via Docker Control Service |
| Models | `/models/` | Model deployment, lifecycle, and configuration |
| RAG | `/collections/` | Document ingestion and vector search management |
| System | `/board/` | Hardware status and device detection |

**Sources:**[app/docker-compose.yml 37](https://github.com/tenstorrent/tt-studio/blob/a6d347af/app/docker-compose.yml#L37-L37)[app/docker-compose.yml 56](https://github.com/tenstorrent/tt-studio/blob/a6d347af/app/docker-compose.yml#L56-L56)

## Backend Services Integration

The backend consists of multiple specialized services that handle different aspects of model management, inference, and data processing. For implementation details, see [Backend Architecture](https://deepwiki.com/tenstorrent/tt-studio/3.3-backend-architecture).

### Backend Service Communication Flow

**Sources:**[app/docker-compose.yml 23-24](https://github.com/tenstorrent/tt-studio/blob/a6d347af/app/docker-compose.yml#L23-L24)[app/docker-compose.yml 55-57](https://github.com/tenstorrent/tt-studio/blob/a6d347af/app/docker-compose.yml#L55-L57)[app/docker-compose.yml 32-33](https://github.com/tenstorrent/tt-studio/blob/a6d347af/app/docker-compose.yml#L32-L33)[README.md 167](https://github.com/tenstorrent/tt-studio/blob/a6d347af/README.md?plain=1#L167-L167)

### Environment Configuration

Services are configured through environment variables defined in `.env` (templated from `.env.default`). The backend acts as the central orchestrator, passing configuration to agents and model containers.

| Environment Variable | Purpose |
| --- | --- |
| `JWT_SECRET` | Authentication between backend, agent, and inference services |
| `DOCKER_CONTROL_SERVICE_URL` | Endpoint for the secure Docker management layer |
| `HOST_PERSISTENT_STORAGE_VOLUME` | Path for persistent data (Chroma, logs, models) |
| `VITE_ENABLE_DEPLOYED` | Feature flag for enabling model deployment features |

**Sources:**[app/docker-compose.yml 30-57](https://github.com/tenstorrent/tt-studio/blob/a6d347af/app/docker-compose.yml#L30-L57)[app/README.md 15-23](https://github.com/tenstorrent/tt-studio/blob/a6d347af/app/README.md?plain=1#L15-L23)

## Deployment and Initialization

TT-Studio deployment is managed via `run.py`, which automates environment setup, hardware detection, and service orchestration.

### Deployment Workflow

1.   **Environment Detection**: Checks for OS, Docker, and Tenstorrent drivers. [startup.sh 66-91](https://github.com/tenstorrent/tt-studio/blob/a6d347af/startup.sh#L66-L91)
2.   **Configuration**: Generates `.env` and sets `TT_STUDIO_ROOT`. [startup.sh 117-119](https://github.com/tenstorrent/tt-studio/blob/a6d347af/startup.sh#L117-L119)
3.   **Infrastructure**: Creates the `tt_studio_network` and persistent storage directories. [app/docker-compose.yml 149-154](https://github.com/tenstorrent/tt-studio/blob/a6d347af/app/docker-compose.yml#L149-L154)
4.   **Service Launch**: Executes `docker compose up` with mode-specific overrides (e.g., `--dev`, `--tt-hardware`). [app/README.md 43-57](https://github.com/tenstorrent/tt-studio/blob/a6d347af/app/README.md?plain=1#L43-L57)

For detailed deployment procedures, see [Docker Container Architecture](https://deepwiki.com/tenstorrent/tt-studio/3.1-docker-container-architecture).

This wiki is featured in the [repository](https://github.com/tenstorrent/tt-studio/blob/main/README.md)

Dismiss
Refresh this wiki

Enter email to refresh
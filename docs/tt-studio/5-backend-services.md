---
project: tt-studio
github: https://github.com/tenstorrent/tt-studio
deepwiki: https://deepwiki.com/tenstorrent/tt-studio/5-backend-services
---

# Backend Services

Relevant source files
*   [app/backend/docker_control/views.py](https://github.com/tenstorrent/tt-studio/blob/a6d347af/app/backend/docker_control/views.py)
*   [app/backend/model_control/views.py](https://github.com/tenstorrent/tt-studio/blob/a6d347af/app/backend/model_control/views.py)

## Purpose and Scope

This document covers the backend service architecture of TT-Studio, including the core API services, inference servers, agent systems, and data storage components. The backend services provide the foundational infrastructure for model management, inference execution, and data processing that power the web-based AI platform.

For information about frontend components and user interfaces, see [Frontend Components](https://deepwiki.com/tenstorrent/tt-studio/4-frontend-components). For details about Docker container architecture, see [Docker Container Architecture](https://deepwiki.com/tenstorrent/tt-studio/3.1-docker-container-architecture).

## Service Architecture Overview

The backend services consist of several primary components that work together to provide AI model management and inference capabilities. The Django backend serves as the central orchestrator, communicating with specialized services for inference, agentic behavior, and vector storage.

**Sources:**[app/backend/docker_control/views.py 41-48](https://github.com/tenstorrent/tt-studio/blob/a6d347af/app/backend/docker_control/views.py#L41-L48)[app/backend/model_control/views.py 102-104](https://github.com/tenstorrent/tt-studio/blob/a6d347af/app/backend/model_control/views.py#L102-L104)[app/backend/model_control/views.py 161-162](https://github.com/tenstorrent/tt-studio/blob/a6d347af/app/backend/model_control/views.py#L161-L162)

## Core Backend Components

The backend is partitioned into several specialized sub-pages for detailed documentation:

### [API Endpoints](https://deepwiki.com/tenstorrent/tt-studio/5.1-api-endpoints)

The Django backend exposes a comprehensive REST API for model management, hardware control, and application state. Key views include `ContainersView` for hardware-aware model listing [app/backend/docker_control/views.py 151-170](https://github.com/tenstorrent/tt-studio/blob/a6d347af/app/backend/docker_control/views.py#L151-L170) and `StopView` for graceful container termination and hardware reset [app/backend/docker_control/views.py 75-109](https://github.com/tenstorrent/tt-studio/blob/a6d347af/app/backend/docker_control/views.py#L75-L109)

### [Model Management](https://deepwiki.com/tenstorrent/tt-studio/5.2-model-management)

Handles the lifecycle of AI models on Tenstorrent hardware. This includes tracking deployments in a local cache [app/backend/docker_control/views.py 37-38](https://github.com/tenstorrent/tt-studio/blob/a6d347af/app/backend/docker_control/views.py#L37-L38) mapping board types to device configurations [app/backend/docker_control/views.py 158-167](https://github.com/tenstorrent/tt-studio/blob/a6d347af/app/backend/docker_control/views.py#L158-L167) and managing the deployment state machine.

### [Vector Database](https://deepwiki.com/tenstorrent/tt-studio/5.3-vector-database)

Integrates ChromaDB for Retrieval-Augmented Generation (RAG). The backend manages document ingestion, embedding generation, and context retrieval to augment LLM prompts during inference.

### [Docker Control Service](https://deepwiki.com/tenstorrent/tt-studio/5.4-docker-control-service)

A security-hardened FastAPI service that acts as a proxy for the Docker socket. It provides authenticated endpoints for starting, stopping, and monitoring model containers [app/backend/docker_control/views.py 41](https://github.com/tenstorrent/tt-studio/blob/a6d347af/app/backend/docker_control/views.py#L41-L41) ensuring that the main Django application does not require root-level Docker socket access.

## Inference and Agent Systems

The backend implements sophisticated routing for inference requests, supporting local Tenstorrent-accelerated models, cloud-based fallbacks, and agentic workflows.

### Inference Routing Logic

The `InferenceView` in the Django backend manages the flow of data to deployed model containers. It retrieves the `internal_url` from the deployment cache [app/backend/model_control/views.py 103-104](https://github.com/tenstorrent/tt-studio/blob/a6d347af/app/backend/model_control/views.py#L103-L104) and streams responses back to the client using `StreamingHttpResponse`[app/backend/model_control/views.py 135-138](https://github.com/tenstorrent/tt-studio/blob/a6d347af/app/backend/model_control/views.py#L135-L138)

**Sources:**[app/backend/model_control/views.py 94-138](https://github.com/tenstorrent/tt-studio/blob/a6d347af/app/backend/model_control/views.py#L94-L138)

### Agent Service Integration

The `AgentView` handles complex tasks requiring tool use or multi-step reasoning. It can operate in a specific deployment mode or an "auto-discovery" mode where it identifies available model containers to fulfill requests [app/backend/model_control/views.py 141-167](https://github.com/tenstorrent/tt-studio/blob/a6d347af/app/backend/model_control/views.py#L141-L167)

| Feature | Implementation | Purpose |
| --- | --- | --- |
| Agent Discovery | `use_agent_discovery: True` | Automatically find active LLM containers [app/backend/model_control/views.py 165-166](https://github.com/tenstorrent/tt-studio/blob/a6d347af/app/backend/model_control/views.py#L165-L166) |
| Cloud Fallback | `InferenceCloudView` | Routes requests to remote APIs when local hardware is unavailable [app/backend/model_control/views.py 79-91](https://github.com/tenstorrent/tt-studio/blob/a6d347af/app/backend/model_control/views.py#L79-L91) |
| Token Clamping | `max_tokens_limit` | Ensures requests fit within the model's context window (75% headroom) [app/backend/model_control/views.py 111-117](https://github.com/tenstorrent/tt-studio/blob/a6d347af/app/backend/model_control/views.py#L111-L117) |

**Sources:**[app/backend/model_control/views.py 111-175](https://github.com/tenstorrent/tt-studio/blob/a6d347af/app/backend/model_control/views.py#L111-L175)

## Hardware Control and Reset

A critical function of the backend is managing the physical state of Tenstorrent hardware. When a model container is stopped via `StopView`, the system performs a hardware reset [app/backend/docker_control/views.py 106-108](https://github.com/tenstorrent/tt-studio/blob/a6d347af/app/backend/docker_control/views.py#L106-L108) and flushes the `tt-smi` cache [app/backend/docker_control/views.py 121-124](https://github.com/tenstorrent/tt-studio/blob/a6d347af/app/backend/docker_control/views.py#L121-L124) to ensure the next deployment starts from a clean state.

**Sources:**[app/backend/docker_control/views.py 75-146](https://github.com/tenstorrent/tt-studio/blob/a6d347af/app/backend/docker_control/views.py#L75-L146)

This wiki is featured in the [repository](https://github.com/tenstorrent/tt-studio/blob/main/README.md)

Dismiss
Refresh this wiki

Enter email to refresh
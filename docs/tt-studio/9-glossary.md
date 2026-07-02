---
project: tt-studio
github: https://github.com/tenstorrent/tt-studio
deepwiki: https://deepwiki.com/tenstorrent/tt-studio/9-glossary
---

# Glossary

Relevant source files
*   [README.md](https://github.com/tenstorrent/tt-studio/blob/a6d347af/README.md?plain=1)
*   [app/.env.default](https://github.com/tenstorrent/tt-studio/blob/a6d347af/app/.env.default)
*   [app/README.md](https://github.com/tenstorrent/tt-studio/blob/a6d347af/app/README.md?plain=1)
*   [app/backend/docker_control/tt_inference_client.py](https://github.com/tenstorrent/tt-studio/blob/a6d347af/app/backend/docker_control/tt_inference_client.py)
*   [app/docker-compose.yml](https://github.com/tenstorrent/tt-studio/blob/a6d347af/app/docker-compose.yml)
*   [app/frontend/index.html](https://github.com/tenstorrent/tt-studio/blob/a6d347af/app/frontend/index.html)
*   [app/frontend/src/api/modelsDeployedApis.ts](https://github.com/tenstorrent/tt-studio/blob/a6d347af/app/frontend/src/api/modelsDeployedApis.ts)
*   [app/frontend/src/components/ModelsDeployedTable.tsx](https://github.com/tenstorrent/tt-studio/blob/a6d347af/app/frontend/src/components/ModelsDeployedTable.tsx)
*   [app/frontend/src/components/chatui/ChatHistory.tsx](https://github.com/tenstorrent/tt-studio/blob/a6d347af/app/frontend/src/components/chatui/ChatHistory.tsx)
*   [app/frontend/src/components/chatui/Header.tsx](https://github.com/tenstorrent/tt-studio/blob/a6d347af/app/frontend/src/components/chatui/Header.tsx)
*   [app/frontend/src/components/chatui/InputArea.tsx](https://github.com/tenstorrent/tt-studio/blob/a6d347af/app/frontend/src/components/chatui/InputArea.tsx)
*   [app/frontend/src/components/chatui/StreamingMessage.tsx](https://github.com/tenstorrent/tt-studio/blob/a6d347af/app/frontend/src/components/chatui/StreamingMessage.tsx)
*   [app/frontend/src/components/object_detection/ObjectDetectionComponent.tsx](https://github.com/tenstorrent/tt-studio/blob/a6d347af/app/frontend/src/components/object_detection/ObjectDetectionComponent.tsx)
*   [docker-control-service/README.md](https://github.com/tenstorrent/tt-studio/blob/a6d347af/docker-control-service/README.md?plain=1)
*   [inference-api/api.py](https://github.com/tenstorrent/tt-studio/blob/a6d347af/inference-api/api.py)
*   [startup.sh](https://github.com/tenstorrent/tt-studio/blob/a6d347af/startup.sh)

This glossary defines technical terms, domain-specific jargon, and architectural concepts used throughout the TT-Studio codebase.

## Core System Terms

### TT Inference Server

The underlying engine responsible for model packaging, containerization, and hardware-optimized execution. TT-Studio acts as a graphical orchestration layer over this server [README.md 14-15](https://github.com/tenstorrent/tt-studio/blob/a6d347af/README.md?plain=1#L14-L15) It provides the `run.py` entry point and the `workflows` logic used to execute models on Tenstorrent hardware [inference-api/api.py 24-41](https://github.com/tenstorrent/tt-studio/blob/a6d347af/inference-api/api.py#L24-L41)

### Docker Control Service (DCS)

A secure FastAPI-based service (typically running on port 8002) that acts as a proxy for the Docker socket [app/.env.default 25-27](https://github.com/tenstorrent/tt-studio/blob/a6d347af/app/.env.default#L25-L27) It prevents the backend containers from needing direct `/var/run/docker.sock` access, providing an API for container lifecycle management, image whitelisting, and resource monitoring [app/docker-compose.yml 55-57](https://github.com/tenstorrent/tt-studio/blob/a6d347af/app/docker-compose.yml#L55-L57)

### Persistent Storage Volume

A shared directory between the host and containers (defined by `HOST_PERSISTENT_STORAGE_VOLUME`) used to store model weights, ChromaDB data, and application logs [app/docker-compose.yml 58-59](https://github.com/tenstorrent/tt-studio/blob/a6d347af/app/docker-compose.yml#L58-L59)

*   **Code Pointer:**`INTERNAL_PERSISTENT_STORAGE_VOLUME` is mapped to `/tt_studio_persistent_volume` inside containers [app/.env.default 6-7](https://github.com/tenstorrent/tt-studio/blob/a6d347af/app/.env.default#L6-L7)

* * *

## Architecture Entities

The following diagram maps natural language concepts to their specific implementation entities in the codebase.

**System to Code Entity Mapping**

Sources: [app/docker-compose.yml 5-147](https://github.com/tenstorrent/tt-studio/blob/a6d347af/app/docker-compose.yml#L5-L147)[inference-api/api.py 104-112](https://github.com/tenstorrent/tt-studio/blob/a6d347af/inference-api/api.py#L104-L112)

* * *

## Domain Concepts

### RAG (Retrieval-Augmented Generation)

A technique used in the Chat UI to provide LLMs with external context. Documents are processed and stored in **ChromaDB**[app/docker-compose.yml 121-122](https://github.com/tenstorrent/tt-studio/blob/a6d347af/app/docker-compose.yml#L121-L122)

*   **Implementation:** The frontend uses `RagPill` to display active data sources [app/frontend/src/components/chatui/ChatHistory.tsx 18-38](https://github.com/tenstorrent/tt-studio/blob/a6d347af/app/frontend/src/components/chatui/ChatHistory.tsx#L18-L38) and `ForwardedSelect` in the header to switch between collections [app/frontend/src/components/chatui/Header.tsx 119-142](https://github.com/tenstorrent/tt-studio/blob/a6d347af/app/frontend/src/components/chatui/Header.tsx#L119-L142)

### Thinking Blocks

Specialized UI segments in the chat interface that display the intermediate reasoning steps of a model (often seen in Llama 3.1 or reasoning-heavy models).

*   **Code Pointer:** Managed via `messageThinkingState` in [app/frontend/src/components/chatui/ChatHistory.tsx 139-142](https://github.com/tenstorrent/tt-studio/blob/a6d347af/app/frontend/src/components/chatui/ChatHistory.tsx#L139-L142)

### Model Deployment Stepper

The multi-step UI process for deploying a model, involving hardware detection, configuration, and asynchronous container startup.

*   **Code Pointer:** Status is tracked via the `models-api/deployed/` endpoint [app/frontend/src/api/modelsDeployedApis.ts 13](https://github.com/tenstorrent/tt-studio/blob/a6d347af/app/frontend/src/api/modelsDeployedApis.ts#L13-L13)

* * *

## Data Flow & Lifecycle

**Inference Request Flow** This diagram traces a request from the UI through the backend to the hardware-specific inference API.

Sources: [app/frontend/src/components/chatui/InputArea.tsx 121-133](https://github.com/tenstorrent/tt-studio/blob/a6d347af/app/frontend/src/components/chatui/InputArea.tsx#L121-L133)[inference-api/api.py 104-112](https://github.com/tenstorrent/tt-studio/blob/a6d347af/inference-api/api.py#L104-L112)[README.md 75-77](https://github.com/tenstorrent/tt-studio/blob/a6d347af/README.md?plain=1#L75-L77)

* * *

## Abbreviations & Jargon

| Term | Definition | Code Reference |
| --- | --- | --- |
| **VLM** | Vision Language Model (e.g., Llama-3.2-11B-Vision). | [app/frontend/src/api/modelsDeployedApis.ts 68](https://github.com/tenstorrent/tt-studio/blob/a6d347af/app/frontend/src/api/modelsDeployedApis.ts#L68-L68) |
| **SSE** | Server-Sent Events; used for streaming inference responses. | [inference-api/api.py 5](https://github.com/tenstorrent/tt-studio/blob/a6d347af/inference-api/api.py#L5-L5) |
| **DCS** | Docker Control Service. | [app/docker-compose.yml 55](https://github.com/tenstorrent/tt-studio/blob/a6d347af/app/docker-compose.yml#L55-L55) |
| **Artifact** | Pre-built tt-inference-server binaries and workflows. | [app/.env.default 10-15](https://github.com/tenstorrent/tt-studio/blob/a6d347af/app/.env.default#L10-L15) |
| **Chip Allocation** | Assigning specific Tenstorrent `device_id` to a container. | [app/frontend/src/api/modelsDeployedApis.ts 163](https://github.com/tenstorrent/tt-studio/blob/a6d347af/app/frontend/src/api/modelsDeployedApis.ts#L163-L163) |

## Hardware Terms

### Device ID

The integer identifier for a specific Tenstorrent PCIe card in a multi-card system (e.g., Grayskull or Wormhole).

*   **Code Pointer:** Passed through the `device_id` field in container data [app/frontend/src/api/modelsDeployedApis.ts 33](https://github.com/tenstorrent/tt-studio/blob/a6d347af/app/frontend/src/api/modelsDeployedApis.ts#L33-L33)

### tt-metal

The low-level programming model and hardware abstraction layer used by Tenstorrent to execute operations on the Tensix cores [README.md 14-15](https://github.com/tenstorrent/tt-studio/blob/a6d347af/README.md?plain=1#L14-L15)

* * *

**Sources:**

*   [app/docker-compose.yml 5-155](https://github.com/tenstorrent/tt-studio/blob/a6d347af/app/docker-compose.yml#L5-L155)
*   [README.md 1-168](https://github.com/tenstorrent/tt-studio/blob/a6d347af/README.md?plain=1#L1-L168)
*   [app/.env.default 1-63](https://github.com/tenstorrent/tt-studio/blob/a6d347af/app/.env.default#L1-L63)
*   [inference-api/api.py 1-112](https://github.com/tenstorrent/tt-studio/blob/a6d347af/inference-api/api.py#L1-L112)
*   [app/frontend/src/api/modelsDeployedApis.ts 1-168](https://github.com/tenstorrent/tt-studio/blob/a6d347af/app/frontend/src/api/modelsDeployedApis.ts#L1-L168)
*   [app/frontend/src/components/chatui/ChatHistory.tsx 18-150](https://github.com/tenstorrent/tt-studio/blob/a6d347af/app/frontend/src/components/chatui/ChatHistory.tsx#L18-L150)

Dismiss
Refresh this wiki

Enter email to refresh
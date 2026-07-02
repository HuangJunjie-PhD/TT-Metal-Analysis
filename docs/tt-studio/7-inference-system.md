---
project: tt-studio
github: https://github.com/tenstorrent/tt-studio
deepwiki: https://deepwiki.com/tenstorrent/tt-studio/7-inference-system
---

# Inference System

Relevant source files
*   [app/backend/docker_control/tt_inference_client.py](https://github.com/tenstorrent/tt-studio/blob/a6d347af/app/backend/docker_control/tt_inference_client.py)
*   [inference-api/api.py](https://github.com/tenstorrent/tt-studio/blob/a6d347af/inference-api/api.py)

This document covers the inference execution pipeline in TT-Studio, detailing how user requests are processed from the frontend through to model execution and streaming response handling. The inference system bridges the gap between high-level user intent and low-level Tenstorrent hardware execution.

For information about the specific API endpoints used by the inference system, see [Inference API](https://deepwiki.com/tenstorrent/tt-studio/7.1-inference-api). For details about RAG context processing, see [Request Processing](https://deepwiki.com/tenstorrent/tt-studio/7.2-request-processing). For the agent service architecture, see [Agent System](https://deepwiki.com/tenstorrent/tt-studio/7.3-agent-system).

## Inference Request Flow

The inference system follows a structured pipeline from user input to model response, involving the frontend chat interface, the Django backend, and the specialized inference servers.

**Sources:**[app/frontend/src/components/chatui/runInference.ts 16-354](https://github.com/tenstorrent/tt-studio/blob/a6d347af/app/frontend/src/components/chatui/runInference.ts#L16-L354)[app/frontend/src/components/chatui/ChatComponent.tsx 487-703](https://github.com/tenstorrent/tt-studio/blob/a6d347af/app/frontend/src/components/chatui/ChatComponent.tsx#L487-L703)

## Core Inference Components

The inference system consists of several key components that work together to process requests across the stack:

### Frontend Interaction Layer

The frontend manages the user session, file uploads, and the rendering of streaming responses. It uses `runInference()` to orchestrate the asynchronous lifecycle of an AI request. **Sources:**[app/frontend/src/components/chatui/runInference.ts 1-367](https://github.com/tenstorrent/tt-studio/blob/a6d347af/app/frontend/src/components/chatui/runInference.ts#L1-L367)[app/frontend/src/components/chatui/ChatComponent.tsx 1-800](https://github.com/tenstorrent/tt-studio/blob/a6d347af/app/frontend/src/components/chatui/ChatComponent.tsx#L1-L800)

### Backend Orchestration

The Django backend acts as a proxy and coordinator. It routes requests to the appropriate model container or agent service. It also handles the integration of the `TTInferenceClient` to communicate with the `tt-inference-server` for deployment-related tasks. **Sources:**[app/backend/docker_control/tt_inference_client.py 24-100](https://github.com/tenstorrent/tt-studio/blob/a6d347af/app/backend/docker_control/tt_inference_client.py#L24-L100)

### Inference API (FastAPI)

The `inference-api/api.py` serves as the primary entry point for executing workflows on Tenstorrent hardware. It wraps the `tt-inference-server` logic, providing endpoints for running models (`/run`), checking progress (`/run/progress/{job_id}`), and retrieving logs. **Sources:**[inference-api/api.py 105-112](https://github.com/tenstorrent/tt-studio/blob/a6d347af/inference-api/api.py#L105-L112)[inference-api/api.py 122-168](https://github.com/tenstorrent/tt-studio/blob/a6d347af/inference-api/api.py#L122-L168)

**Sources:**[app/backend/docker_control/tt_inference_client.py 57](https://github.com/tenstorrent/tt-studio/blob/a6d347af/app/backend/docker_control/tt_inference_client.py#L57-L57)[inference-api/api.py 27-48](https://github.com/tenstorrent/tt-studio/blob/a6d347af/inference-api/api.py#L27-L48)

## Request Processing Pipeline

### Input Processing

The inference system processes various input types through a unified pipeline. The `InferenceRequest` interface defines the structure for all inference requests, including optional parameters like `temperature` and `max_tokens`. **Sources:**[app/frontend/src/components/chatui/types.ts 71-83](https://github.com/tenstorrent/tt-studio/blob/a6d347af/app/frontend/src/components/chatui/types.ts#L71-L83)[app/frontend/src/components/chatui/runInference.ts 42-134](https://github.com/tenstorrent/tt-studio/blob/a6d347af/app/frontend/src/components/chatui/runInference.ts#L42-L134)

### RAG Context Integration

When RAG (Retrieval-Augmented Generation) datasources are selected, the system enhances requests with retrieved context before sending them to the model. This allows the model to "see" private documents or specific datasets. **Sources:**[app/frontend/src/components/chatui/runInference.ts 34-73](https://github.com/tenstorrent/tt-studio/blob/a6d347af/app/frontend/src/components/chatui/runInference.ts#L34-L73)

## Response Streaming

The inference system implements real-time streaming using Server-Sent Events (SSE) to provide immediate feedback to the user.

1.   **Connection**: Uses `fetch` with `AbortController` for cancellation support.
2.   **Parsing**: Processes the `data:` stream from the FastAPI or Agent backend.
3.   **Statistics**: Extracts performance metrics (TTFT, TPOT) directly from the stream.

**Sources:**[app/frontend/src/components/chatui/runInference.ts 215-331](https://github.com/tenstorrent/tt-studio/blob/a6d347af/app/frontend/src/components/chatui/runInference.ts#L215-L331)[app/frontend/src/components/chatui/types.ts 85-99](https://github.com/tenstorrent/tt-studio/blob/a6d347af/app/frontend/src/components/chatui/types.ts#L85-L99)

## Deployment and Hardware Execution

The system utilizes a specialized client, `TTInferenceClient`, to manage the lifecycle of chat deployments on Tenstorrent hardware.

*   **`start_chat_deployment`**: Sends a POST request to the inference server's `/run` endpoint with a `workflow: "server"` payload.
*   **Job Polling**: Returns a `job_id` allowing the UI to poll for weights download and initialization progress.
*   **Hardware Selection**: Supports passing `device` and `device_id` to target specific Tenstorrent chips.

**Sources:**[app/backend/docker_control/tt_inference_client.py 24-55](https://github.com/tenstorrent/tt-studio/blob/a6d347af/app/backend/docker_control/tt_inference_client.py#L24-L55)[app/backend/docker_control/tt_inference_client.py 83-100](https://github.com/tenstorrent/tt-studio/blob/a6d347af/app/backend/docker_control/tt_inference_client.py#L83-L100)

## Performance Statistics

The system captures and displays comprehensive performance metrics to help developers understand how models perform on Tenstorrent hardware:

| Metric | Code Symbol | Description |
| --- | --- | --- |
| Time to First Token | `user_ttft_s` | Latency before the first character appears. |
| Time Per Output Token | `user_tpot` | Average time to generate each subsequent token. |
| Tokens Decoded | `tokens_decoded` | Total number of tokens in the response. |
| Context Length | `context_length` | Size of the input context window. |

**Sources:**[app/frontend/src/components/chatui/types.ts 85-99](https://github.com/tenstorrent/tt-studio/blob/a6d347af/app/frontend/src/components/chatui/types.ts#L85-L99)[app/frontend/src/components/chatui/MessageActions.tsx 148-181](https://github.com/tenstorrent/tt-studio/blob/a6d347af/app/frontend/src/components/chatui/MessageActions.tsx#L148-L181)

This wiki is featured in the [repository](https://github.com/tenstorrent/tt-studio/blob/main/README.md)

Dismiss
Refresh this wiki

Enter email to refresh
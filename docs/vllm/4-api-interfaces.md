---
project: vllm
github: https://github.com/tenstorrent/vllm
deepwiki: https://deepwiki.com/tenstorrent/vllm/4-api-interfaces
---

# API Interfaces

Relevant source files
*   [.buildkite/test-pipeline.yaml](https://github.com/tenstorrent/vllm/blob/cd9e5b83/.buildkite/test-pipeline.yaml)
*   [tests/config/test_config.yaml](https://github.com/tenstorrent/vllm/blob/cd9e5b83/tests/config/test_config.yaml)
*   [tests/config/test_config_with_model.yaml](https://github.com/tenstorrent/vllm/blob/cd9e5b83/tests/config/test_config_with_model.yaml)
*   [tests/conftest.py](https://github.com/tenstorrent/vllm/blob/cd9e5b83/tests/conftest.py)
*   [tests/distributed/test_pipeline_parallel.py](https://github.com/tenstorrent/vllm/blob/cd9e5b83/tests/distributed/test_pipeline_parallel.py)
*   [tests/entrypoints/openai/test_lora_resolvers.py](https://github.com/tenstorrent/vllm/blob/cd9e5b83/tests/entrypoints/openai/test_lora_resolvers.py)
*   [tests/entrypoints/openai/test_serving_chat.py](https://github.com/tenstorrent/vllm/blob/cd9e5b83/tests/entrypoints/openai/test_serving_chat.py)
*   [tests/utils.py](https://github.com/tenstorrent/vllm/blob/cd9e5b83/tests/utils.py)
*   [vllm/engine/arg_utils.py](https://github.com/tenstorrent/vllm/blob/cd9e5b83/vllm/engine/arg_utils.py)
*   [vllm/engine/async_llm_engine.py](https://github.com/tenstorrent/vllm/blob/cd9e5b83/vllm/engine/async_llm_engine.py)
*   [vllm/engine/llm_engine.py](https://github.com/tenstorrent/vllm/blob/cd9e5b83/vllm/engine/llm_engine.py)
*   [vllm/entrypoints/llm.py](https://github.com/tenstorrent/vllm/blob/cd9e5b83/vllm/entrypoints/llm.py)
*   [vllm/entrypoints/openai/api_server.py](https://github.com/tenstorrent/vllm/blob/cd9e5b83/vllm/entrypoints/openai/api_server.py)
*   [vllm/entrypoints/openai/cli_args.py](https://github.com/tenstorrent/vllm/blob/cd9e5b83/vllm/entrypoints/openai/cli_args.py)
*   [vllm/entrypoints/openai/protocol.py](https://github.com/tenstorrent/vllm/blob/cd9e5b83/vllm/entrypoints/openai/protocol.py)
*   [vllm/entrypoints/openai/serving_chat.py](https://github.com/tenstorrent/vllm/blob/cd9e5b83/vllm/entrypoints/openai/serving_chat.py)
*   [vllm/entrypoints/openai/serving_classification.py](https://github.com/tenstorrent/vllm/blob/cd9e5b83/vllm/entrypoints/openai/serving_classification.py)
*   [vllm/entrypoints/openai/serving_completion.py](https://github.com/tenstorrent/vllm/blob/cd9e5b83/vllm/entrypoints/openai/serving_completion.py)
*   [vllm/entrypoints/openai/serving_embedding.py](https://github.com/tenstorrent/vllm/blob/cd9e5b83/vllm/entrypoints/openai/serving_embedding.py)
*   [vllm/entrypoints/openai/serving_engine.py](https://github.com/tenstorrent/vllm/blob/cd9e5b83/vllm/entrypoints/openai/serving_engine.py)
*   [vllm/entrypoints/openai/serving_pooling.py](https://github.com/tenstorrent/vllm/blob/cd9e5b83/vllm/entrypoints/openai/serving_pooling.py)
*   [vllm/entrypoints/openai/serving_score.py](https://github.com/tenstorrent/vllm/blob/cd9e5b83/vllm/entrypoints/openai/serving_score.py)
*   [vllm/entrypoints/openai/serving_tokenization.py](https://github.com/tenstorrent/vllm/blob/cd9e5b83/vllm/entrypoints/openai/serving_tokenization.py)
*   [vllm/envs.py](https://github.com/tenstorrent/vllm/blob/cd9e5b83/vllm/envs.py)
*   [vllm/outputs.py](https://github.com/tenstorrent/vllm/blob/cd9e5b83/vllm/outputs.py)
*   [vllm/sampling_params.py](https://github.com/tenstorrent/vllm/blob/cd9e5b83/vllm/sampling_params.py)
*   [vllm/sequence.py](https://github.com/tenstorrent/vllm/blob/cd9e5b83/vllm/sequence.py)

vLLM provides multiple interfaces for interacting with language models, each designed for different use cases. This page provides an overview of the available interfaces and their relationships to the core engine. For detailed information about specific interfaces, see the child pages.

The main interfaces are:

1.   **Python API** ([LLM class](https://deepwiki.com/tenstorrent/vllm/4.1-python-api)): Direct Python API for offline inference
2.   **OpenAI-Compatible Server** ([HTTP API](https://deepwiki.com/tenstorrent/vllm/4.2-openai-compatible-server)): HTTP server compatible with OpenAI's API specification
3.   **Protocol Objects** ([Data Types](https://deepwiki.com/tenstorrent/vllm/4.3-protocol-and-data-types)): Request/response models used across interfaces
4.   **Streaming and Tool Calling** ([Advanced Features](https://deepwiki.com/tenstorrent/vllm/4.4-streaming-and-tool-calling)): Streaming responses and function calling

## Architecture Overview

All vLLM interfaces ultimately communicate with the same core engine infrastructure. The diagram below shows how different user-facing interfaces connect to the underlying engine components.

**Interface Architecture**

Sources: [vllm/entrypoints/llm.py 70-165](https://github.com/tenstorrent/vllm/blob/cd9e5b83/vllm/entrypoints/llm.py#L70-L165)[vllm/entrypoints/openai/api_server.py 120-241](https://github.com/tenstorrent/vllm/blob/cd9e5b83/vllm/entrypoints/openai/api_server.py#L120-L241)[vllm/v1/engine/async_llm.py 1-7](https://github.com/tenstorrent/vllm/blob/cd9e5b83/vllm/v1/engine/async_llm.py#L1-L7)[vllm/v1/engine/llm_engine.py 1-7](https://github.com/tenstorrent/vllm/blob/cd9e5b83/vllm/v1/engine/llm_engine.py#L1-L7)

The key components in this architecture are:

| Component | File Path | Purpose |
| --- | --- | --- |
| `LLM` | [vllm/entrypoints/llm.py 70-165](https://github.com/tenstorrent/vllm/blob/cd9e5b83/vllm/entrypoints/llm.py#L70-L165) | Python class for offline inference |
| FastAPI Server | [vllm/entrypoints/openai/api_server.py 120-150](https://github.com/tenstorrent/vllm/blob/cd9e5b83/vllm/entrypoints/openai/api_server.py#L120-L150) | HTTP server with OpenAI-compatible endpoints |
| `OpenAIServingChat` | [vllm/entrypoints/openai/serving_chat.py 59-81](https://github.com/tenstorrent/vllm/blob/cd9e5b83/vllm/entrypoints/openai/serving_chat.py#L59-L81) | Handles `/v1/chat/completions` requests |
| `OpenAIServingCompletion` | [vllm/entrypoints/openai/serving_completion.py 45-68](https://github.com/tenstorrent/vllm/blob/cd9e5b83/vllm/entrypoints/openai/serving_completion.py#L45-L68) | Handles `/v1/completions` requests |
| `AsyncLLM` | [vllm/v1/engine/async_llm.py 1-7](https://github.com/tenstorrent/vllm/blob/cd9e5b83/vllm/v1/engine/async_llm.py#L1-L7) | Async engine interface |
| `LLMEngine` | [vllm/v1/engine/llm_engine.py 1-7](https://github.com/tenstorrent/vllm/blob/cd9e5b83/vllm/v1/engine/llm_engine.py#L1-L7) | Core engine implementation |
| `Processor` | [vllm/v1/engine/processor.py 1-22](https://github.com/tenstorrent/vllm/blob/cd9e5b83/vllm/v1/engine/processor.py#L1-L22) | Request preprocessing and validation |

## Python API (LLM Class)

The `LLM` class in [vllm/entrypoints/llm.py 70-165](https://github.com/tenstorrent/vllm/blob/cd9e5b83/vllm/entrypoints/llm.py#L70-L165) provides a simple Python interface for offline inference. It is designed for batch processing scenarios where you want to generate completions for a set of prompts.

**Basic Usage Pattern**

`from vllm import LLM, SamplingParams # Initialize the LLMllm = LLM(model="meta-llama/Llama-2-7b-chat-hf") # Define prompts and sampling parametersprompts = ["Hello, my name is", "The capital of France is"]sampling_params = SamplingParams(temperature=0.8, top_p=0.95) # Generate completionsoutputs = llm.generate(prompts, sampling_params)`
The `LLM` class supports various tasks:

*   **Text Generation**: `llm.generate()` for standard completions
*   **Chat**: `llm.chat()` for conversational interactions
*   **Embeddings**: `llm.encode()` for generating text embeddings

For detailed documentation on the Python API, including parameters and advanced usage, see [Python API](https://deepwiki.com/tenstorrent/vllm/4.1-python-api).

Sources: [vllm/entrypoints/llm.py 70-165](https://github.com/tenstorrent/vllm/blob/cd9e5b83/vllm/entrypoints/llm.py#L70-L165)[vllm/entrypoints/llm.py 300-400](https://github.com/tenstorrent/vllm/blob/cd9e5b83/vllm/entrypoints/llm.py#L300-L400)

## OpenAI-Compatible Server

The OpenAI-compatible server provides an HTTP interface that mirrors OpenAI's API specification. It is implemented using FastAPI in [vllm/entrypoints/openai/api_server.py 120-241](https://github.com/tenstorrent/vllm/blob/cd9e5b83/vllm/entrypoints/openai/api_server.py#L120-L241)

**Starting the Server**

`vllm serve meta-llama/Llama-2-7b-chat-hf \  --port 8000 \  --tensor-parallel-size 2`
**Making Requests**

`curl http://localhost:8000/v1/chat/completions \  -H "Content-Type: application/json" \  -d '{    "model": "meta-llama/Llama-2-7b-chat-hf",    "messages": [{"role": "user", "content": "Hello!"}]  }'`
The server implements these main endpoints:

| Endpoint | Handler | Purpose |
| --- | --- | --- |
| `/v1/chat/completions` | `OpenAIServingChat` | Chat-style completions |
| `/v1/completions` | `OpenAIServingCompletion` | Text completions |
| `/v1/embeddings` | `OpenAIServingEmbedding` | Text embeddings |
| `/v1/models` | `OpenAIServingModels` | List available models |
| `/tokenize` | `OpenAIServingTokenization` | Tokenization utilities |
| `/health` | Direct handler | Health check |

For detailed information about the server architecture, endpoints, and configuration, see [OpenAI-Compatible Server](https://deepwiki.com/tenstorrent/vllm/4.2-openai-compatible-server).

Sources: [vllm/entrypoints/openai/api_server.py 252-253](https://github.com/tenstorrent/vllm/blob/cd9e5b83/vllm/entrypoints/openai/api_server.py#L252-L253)[vllm/entrypoints/openai/api_server.py 345-380](https://github.com/tenstorrent/vllm/blob/cd9e5b83/vllm/entrypoints/openai/api_server.py#L345-L380)

## Request and Response Protocol

Both the Python API and HTTP server use common protocol objects defined in [vllm/entrypoints/openai/protocol.py](https://github.com/tenstorrent/vllm/blob/cd9e5b83/vllm/entrypoints/openai/protocol.py) and related modules. These objects ensure consistent data structures across interfaces.

**Key Protocol Classes**

Sources: [vllm/entrypoints/openai/protocol.py 437-456](https://github.com/tenstorrent/vllm/blob/cd9e5b83/vllm/entrypoints/openai/protocol.py#L437-L456)[vllm/sampling_params.py 85-104](https://github.com/tenstorrent/vllm/blob/cd9e5b83/vllm/sampling_params.py#L85-L104)[vllm/pooling_params.py 1-50](https://github.com/tenstorrent/vllm/blob/cd9e5b83/vllm/pooling_params.py#L1-L50)

The main protocol types include:

| Type | File | Purpose |
| --- | --- | --- |
| `ChatCompletionRequest` | [vllm/entrypoints/openai/protocol.py 437-456](https://github.com/tenstorrent/vllm/blob/cd9e5b83/vllm/entrypoints/openai/protocol.py#L437-L456) | Chat completion requests |
| `CompletionRequest` | [vllm/entrypoints/openai/protocol.py 699-793](https://github.com/tenstorrent/vllm/blob/cd9e5b83/vllm/entrypoints/openai/protocol.py#L699-L793) | Text completion requests |
| `SamplingParams` | [vllm/sampling_params.py 85-104](https://github.com/tenstorrent/vllm/blob/cd9e5b83/vllm/sampling_params.py#L85-L104) | Controls text generation behavior |
| `PoolingParams` | [vllm/pooling_params.py 1-50](https://github.com/tenstorrent/vllm/blob/cd9e5b83/vllm/pooling_params.py#L1-L50) | Controls embedding generation |
| `RequestOutput` | [vllm/outputs.py 80-200](https://github.com/tenstorrent/vllm/blob/cd9e5b83/vllm/outputs.py#L80-L200) | Unified output format |

For detailed information about protocol objects, validation, and parameters, see [Protocol and Data Types](https://deepwiki.com/tenstorrent/vllm/4.3-protocol-and-data-types).

Sources: [vllm/entrypoints/openai/protocol.py 80-122](https://github.com/tenstorrent/vllm/blob/cd9e5b83/vllm/entrypoints/openai/protocol.py#L80-L122)[vllm/sampling_params.py 1-50](https://github.com/tenstorrent/vllm/blob/cd9e5b83/vllm/sampling_params.py#L1-L50)[vllm/outputs.py 22-61](https://github.com/tenstorrent/vllm/blob/cd9e5b83/vllm/outputs.py#L22-L61)

## Common Configuration

Both the Python API and HTTP server share a common configuration system based on `EngineArgs`. This class consolidates all engine configuration parameters into a single object.

**Configuration Flow**

Sources: [vllm/engine/arg_utils.py 284-487](https://github.com/tenstorrent/vllm/blob/cd9e5b83/vllm/engine/arg_utils.py#L284-L487)[vllm/config/__init__.py 1-50](https://github.com/tenstorrent/vllm/blob/cd9e5b83/vllm/config/__init__.py#L1-L50)

**Key Configuration Parameters**

| Parameter | Type | Purpose | Used By |
| --- | --- | --- | --- |
| `model` | `str` | Model name or path | Both |
| `tensor_parallel_size` | `int` | Number of GPUs for tensor parallelism | Both |
| `dtype` | `str` | Model precision (auto/float16/bfloat16) | Both |
| `max_model_len` | `int` | Maximum sequence length | Both |
| `gpu_memory_utilization` | `float` | GPU memory fraction for KV cache | Both |
| `trust_remote_code` | `bool` | Allow remote code execution | Both |

**Python API Example**

`from vllm import LLM llm = LLM(    model="meta-llama/Llama-2-7b-chat-hf",    tensor_parallel_size=2,    dtype="bfloat16",    max_model_len=4096,)`
**HTTP Server Example**

`vllm serve meta-llama/Llama-2-7b-chat-hf \  --tensor-parallel-size 2 \  --dtype bfloat16 \  --max-model-len 4096`
Sources: [vllm/engine/arg_utils.py 284-487](https://github.com/tenstorrent/vllm/blob/cd9e5b83/vllm/engine/arg_utils.py#L284-L487)[vllm/entrypoints/llm.py 167-203](https://github.com/tenstorrent/vllm/blob/cd9e5b83/vllm/entrypoints/llm.py#L167-L203)

## Configuration

The API server can be configured with various command-line arguments:

### Server Options

| Option | Description | Default |
| --- | --- | --- |
| `--host` | Host name to bind to | `None` (all interfaces) |
| `--port` | Port number | `8000` |
| `--api-key` | API key for authentication | `None` |
| `--disable-frontend-multiprocessing` | Run API server in same process as engine | `False` |
| `--allowed-origins` | CORS allowed origins | `["*"]` |
| `--root-path` | FastAPI root path for proxy setups | `None` |

### Model Settings

| Option | Description | Default |
| --- | --- | --- |
| `--model` | Model to load | Required |
| `--served-model-name` | Override model name in responses | `None` |
| `--chat-template` | Chat template file or string | `None` |
| `--response-role` | Role name for chat responses | `"assistant"` |
| `--lora-modules` | LoRA adapters to load | `None` |

### Security Options

| Option | Description | Default |
| --- | --- | --- |
| `--ssl-keyfile` | SSL key file | `None` |
| `--ssl-certfile` | SSL certificate file | `None` |
| `--ssl-ca-certs` | CA certificates | `None` |
| `--enable-ssl-refresh` | Refresh SSL when files change | `False` |

Sources: [vllm/entrypoints/openai/cli_args.py 80-270](https://github.com/tenstorrent/vllm/blob/cd9e5b83/vllm/entrypoints/openai/cli_args.py#L80-L270)

## Advanced Features

### Authentication

When `--api-key` is provided, the server requires clients to include the key in the Authorization header:

```
Authorization: Bearer <api-key>
```

Authentication is implemented as a middleware that checks all requests to `/v1/*` endpoints:

`@app.middleware("http")async def authentication(request: Request, call_next):    if request.url.path.startswith("/v1"):        if request.headers.get("Authorization") != "Bearer " + token:            return JSONResponse(content={"error": "Unauthorized"}, status_code=401)    return await call_next(request)`
Sources: [vllm/entrypoints/openai/api_server.py 845-857](https://github.com/tenstorrent/vllm/blob/cd9e5b83/vllm/entrypoints/openai/api_server.py#L845-L857)

### Multiprocessing

By default, the API server uses multiprocessing to separate the frontend (FastAPI) from the backend (LLM engine). This provides better performance by allowing the API server to handle a large number of concurrent requests without blocking the engine.

The multiprocessing flow is:

1.   Start engine process with `run_mp_engine()`
2.   Create `MQLLMEngineClient` in API server process
3.   Connect to engine process via ZeroMQ IPC socket
4.   Forward requests to engine and return responses

To disable multiprocessing, use the `--disable-frontend-multiprocessing` flag.

Sources: [vllm/entrypoints/openai/api_server.py 188-293](https://github.com/tenstorrent/vllm/blob/cd9e5b83/vllm/entrypoints/openai/api_server.py#L188-L293)

### Streaming

Both the chat and completion endpoints support streaming responses. When `stream=True` is set in the request, the server uses Server-Sent Events (SSE) to stream tokens as they're generated:

`return StreamingResponse(content=generator, media_type="text/event-stream")`
Each streamed response is formatted as an SSE event:

```
data: {"id":"...", "object":"chat.completion.chunk", "choices":[...]}

data: [DONE]
```

Sources: [vllm/entrypoints/openai/api_server.py 486](https://github.com/tenstorrent/vllm/blob/cd9e5b83/vllm/entrypoints/openai/api_server.py#L486-L486)[vllm/entrypoints/openai/serving_chat.py 395-530](https://github.com/tenstorrent/vllm/blob/cd9e5b83/vllm/entrypoints/openai/serving_chat.py#L395-L530)

### Specialized Endpoints

The server includes several specialized endpoints for development and monitoring:

*   `/health` - Health check endpoint
*   `/metrics` - Prometheus metrics endpoint
*   `/version` - Server version information
*   `/server_info` - Debug information (in dev mode)
*   `/reset_prefix_cache` - Reset KV cache (in dev mode)
*   `/sleep` and `/wake_up` - Control engine state (in dev mode)

Sources: [vllm/entrypoints/openai/api_server.py 391-396](https://github.com/tenstorrent/vllm/blob/cd9e5b83/vllm/entrypoints/openai/api_server.py#L391-L396)[vllm/entrypoints/openai/api_server.py 691-736](https://github.com/tenstorrent/vllm/blob/cd9e5b83/vllm/entrypoints/openai/api_server.py#L691-L736)

## Performance Optimizations

The API server includes several optimizations for high-performance serving:

1.   **Prefix Caching**: Reuse previous KV cache for similar requests
2.   **Async Processing**: Non-blocking request handling with async/await
3.   **Multiprocessing**: Separate API server and engine processes
4.   **Batching**: Process multiple tokens efficiently
5.   **Streaming**: Start sending responses before generation completes

For high-throughput deployments, consider:

*   Using multiprocessing mode (default)
*   Configuring batch size and scheduler parameters
*   Monitoring performance with the `/metrics` endpoint
*   Disabling access logs with `--disable-uvicorn-access-log`

Sources: [vllm/entrypoints/openai/api_server.py 188-293](https://github.com/tenstorrent/vllm/blob/cd9e5b83/vllm/entrypoints/openai/api_server.py#L188-L293)[vllm/entrypoints/openai/serving_chat.py 395-530](https://github.com/tenstorrent/vllm/blob/cd9e5b83/vllm/entrypoints/openai/serving_chat.py#L395-L530)

## Example Usage

### Starting the Server

`python -m vllm.entrypoints.openai.api_server \  --model llama2-7b-chat \  --port 8000 \  --tensor-parallel-size 2`
### Making a Request

`curl http://localhost:8000/v1/chat/completions \  -H "Content-Type: application/json" \  -d '{    "model": "llama2-7b-chat",    "messages": [      {"role": "system", "content": "You are a helpful assistant."},      {"role": "user", "content": "Hello, how are you?"}    ],    "stream": true  }'`
## Integration with vLLM Components

The API Server integrates with other vLLM components:

The API Server is the entry point for client applications but relies on the core vLLM engine components to perform the actual model inference. It creates a clean separation between the HTTP interface and the model execution pipeline, allowing each layer to be optimized independently.

Sources: [vllm/entrypoints/openai/api_server.py 138-202](https://github.com/tenstorrent/vllm/blob/cd9e5b83/vllm/entrypoints/openai/api_server.py#L138-L202)[vllm/entrypoints/openai/api_server.py 305-384](https://github.com/tenstorrent/vllm/blob/cd9e5b83/vllm/entrypoints/openai/api_server.py#L305-L384)

Dismiss
Refresh this wiki

Enter email to refresh
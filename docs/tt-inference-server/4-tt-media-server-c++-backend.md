---
project: tt-inference-server
github: https://github.com/tenstorrent/tt-inference-server
deepwiki: https://deepwiki.com/tenstorrent/tt-inference-server/4-tt-media-server-c++-backend
---

# tt-media-server C++ Backend

Relevant source files
*   [.github/workflows/test-gate.yml](https://github.com/tenstorrent/tt-inference-server/blob/8ab207f9/.github/workflows/test-gate.yml)
*   [.gitmodules](https://github.com/tenstorrent/tt-inference-server/blob/8ab207f9/.gitmodules)
*   [tt-media-server/cpp_server/CLAUDE.md](https://github.com/tenstorrent/tt-inference-server/blob/8ab207f9/tt-media-server/cpp_server/CLAUDE.md?plain=1)
*   [tt-media-server/cpp_server/CMakeLists.txt](https://github.com/tenstorrent/tt-inference-server/blob/8ab207f9/tt-media-server/cpp_server/CMakeLists.txt)
*   [tt-media-server/cpp_server/README.md](https://github.com/tenstorrent/tt-inference-server/blob/8ab207f9/tt-media-server/cpp_server/README.md?plain=1)
*   [tt-media-server/cpp_server/build.sh](https://github.com/tenstorrent/tt-inference-server/blob/8ab207f9/tt-media-server/cpp_server/build.sh)
*   [tt-media-server/cpp_server/include/api/llm_controller.hpp](https://github.com/tenstorrent/tt-inference-server/blob/8ab207f9/tt-media-server/cpp_server/include/api/llm_controller.hpp)
*   [tt-media-server/cpp_server/include/config/runner_config.hpp](https://github.com/tenstorrent/tt-inference-server/blob/8ab207f9/tt-media-server/cpp_server/include/config/runner_config.hpp)
*   [tt-media-server/cpp_server/include/config/types.hpp](https://github.com/tenstorrent/tt-inference-server/blob/8ab207f9/tt-media-server/cpp_server/include/config/types.hpp)
*   [tt-media-server/cpp_server/include/domain/models_response.hpp](https://github.com/tenstorrent/tt-inference-server/blob/8ab207f9/tt-media-server/cpp_server/include/domain/models_response.hpp)
*   [tt-media-server/cpp_server/include/runners/llm_runner/block_manager.hpp](https://github.com/tenstorrent/tt-inference-server/blob/8ab207f9/tt-media-server/cpp_server/include/runners/llm_runner/block_manager.hpp)
*   [tt-media-server/cpp_server/include/services/llm_service.hpp](https://github.com/tenstorrent/tt-inference-server/blob/8ab207f9/tt-media-server/cpp_server/include/services/llm_service.hpp)
*   [tt-media-server/cpp_server/src/api/llm_controller.cpp](https://github.com/tenstorrent/tt-inference-server/blob/8ab207f9/tt-media-server/cpp_server/src/api/llm_controller.cpp)
*   [tt-media-server/cpp_server/src/runners/llm_runner/block_manager.cpp](https://github.com/tenstorrent/tt-inference-server/blob/8ab207f9/tt-media-server/cpp_server/src/runners/llm_runner/block_manager.cpp)
*   [tt-media-server/cpp_server/src/runners/llm_runner/mock_model_runner.cpp](https://github.com/tenstorrent/tt-inference-server/blob/8ab207f9/tt-media-server/cpp_server/src/runners/llm_runner/mock_model_runner.cpp)
*   [tt-media-server/cpp_server/src/services/llm_service.cpp](https://github.com/tenstorrent/tt-inference-server/blob/8ab207f9/tt-media-server/cpp_server/src/services/llm_service.cpp)

The **tt-media-server C++ Backend** (`tt_media_server_cpp`) is a production-grade, high-performance inference server designed for Tenstorrent hardware. It provides a zero-overhead C++ implementation of the OpenAI-compatible API, serving as a high-throughput alternative to the Python-based server for LLM and Embedding workloads [tt-media-server/cpp_server/README.md 1-7](https://github.com/tenstorrent/tt-inference-server/blob/8ab207f9/tt-media-server/cpp_server/README.md?plain=1#L1-L7)

The backend is built on the **Drogon** web framework and utilizes a multi-process architecture to ensure fault isolation and hardware-level performance [tt-media-server/cpp_server/src/main.cpp 167-184](https://github.com/tenstorrent/tt-inference-server/blob/8ab207f9/tt-media-server/cpp_server/src/main.cpp#L167-L184)

### High-Level Architecture

The C++ backend follows a layered architecture that separates the network interface from the hardware-specific inference logic.

**Sources:**[tt-media-server/cpp_server/CLAUDE.md 67-79](https://github.com/tenstorrent/tt-inference-server/blob/8ab207f9/tt-media-server/cpp_server/CLAUDE.md?plain=1#L67-L79)[tt-media-server/cpp_server/src/main.cpp 37-61](https://github.com/tenstorrent/tt-inference-server/blob/8ab207f9/tt-media-server/cpp_server/src/main.cpp#L37-L61)

* * *

### Build System and Prerequisites

The server uses a CMake-based build system that manages several high-performance dependencies, including **pybind11** for Python integration, **xgrammar** for guided decoding, and **mlc-ai/tokenizers-cpp** for fast tokenization [tt-media-server/cpp_server/CMakeLists.txt 49-95](https://github.com/tenstorrent/tt-inference-server/blob/8ab207f9/tt-media-server/cpp_server/CMakeLists.txt#L49-L95)[tt-media-server/cpp_server/CMakeLists.txt 182-190](https://github.com/tenstorrent/tt-inference-server/blob/8ab207f9/tt-media-server/cpp_server/CMakeLists.txt#L182-L190)

*   **Build Script**: `build.sh` automates dependency checks (Drogon, Rust/Cargo) and pre-fetches tokenizer files for supported models like DeepSeek and Llama [tt-media-server/cpp_server/build.sh 120-162](https://github.com/tenstorrent/tt-inference-server/blob/8ab207f9/tt-media-server/cpp_server/build.sh#L120-L162)[tt-media-server/cpp_server/build.sh 174-208](https://github.com/tenstorrent/tt-inference-server/blob/8ab207f9/tt-media-server/cpp_server/build.sh#L174-L208)
*   **Debug/Profiling**: Supports AddressSanitizer (`--asan`), ThreadSanitizer (`--tsan`), and Tracy profiling (`--tracy`) for performance optimization [tt-media-server/cpp_server/build.sh 26-39](https://github.com/tenstorrent/tt-inference-server/blob/8ab207f9/tt-media-server/cpp_server/build.sh#L26-L39)

**Sources:**[tt-media-server/cpp_server/CMakeLists.txt 4-130](https://github.com/tenstorrent/tt-inference-server/blob/8ab207f9/tt-media-server/cpp_server/CMakeLists.txt#L4-L130)[tt-media-server/cpp_server/build.sh 1-101](https://github.com/tenstorrent/tt-inference-server/blob/8ab207f9/tt-media-server/cpp_server/build.sh#L1-L101)

* * *

### Key Subsystems

#### C++ API Layer

The API layer uses Drogon controllers to expose OpenAI-compatible endpoints. It handles JSON parsing, request validation, and maps C++ exceptions to appropriate HTTP error codes [tt-media-server/cpp_server/src/api/llm_controller.cpp 184-197](https://github.com/tenstorrent/tt-inference-server/blob/8ab207f9/tt-media-server/cpp_server/src/api/llm_controller.cpp#L184-L197)

*   **Chat**: `POST /v1/chat/completions`[tt-media-server/cpp_server/include/api/llm_controller.hpp 24-25](https://github.com/tenstorrent/tt-inference-server/blob/8ab207f9/tt-media-server/cpp_server/include/api/llm_controller.hpp#L24-L25)
*   **Embeddings**: `POST /v1/embeddings`
*   **Details**: For more information on the request lifecycle and SSE streaming, see [C++ API Layer](https://deepwiki.com/tenstorrent/tt-inference-server/4.1-c++-api-layer).

**Sources:**[tt-media-server/cpp_server/src/main.cpp 188-197](https://github.com/tenstorrent/tt-inference-server/blob/8ab207f9/tt-media-server/cpp_server/src/main.cpp#L188-L197)[tt-media-server/cpp_server/include/api/llm_controller.hpp 21-32](https://github.com/tenstorrent/tt-inference-server/blob/8ab207f9/tt-media-server/cpp_server/include/api/llm_controller.hpp#L21-L32)

#### LLM Engine: Scheduler and Memory

The core inference engine features a sophisticated scheduler supporting **Paged Attention** and **Prefix Caching**. It manages KV cache in fixed-size blocks to maximize memory utilization and supports preemption of sequences when hardware resources are exhausted [tt-media-server/cpp_server/README.md 93-101](https://github.com/tenstorrent/tt-inference-server/blob/8ab207f9/tt-media-server/cpp_server/README.md?plain=1#L93-L101)

*   **Scheduling Policies**: Supports `Prefill First` (latency-optimized) and `Max Occupancy` (throughput-optimized) [tt-media-server/cpp_server/README.md 103-135](https://github.com/tenstorrent/tt-inference-server/blob/8ab207f9/tt-media-server/cpp_server/README.md?plain=1#L103-L135)
*   **Details**: For details on the `BlockManager` and sequence lifecycle, see [LLM Engine: Scheduler, Memory, and Sequences](https://deepwiki.com/tenstorrent/tt-inference-server/4.2-llm-engine:-scheduler-memory-and-sequences).

**Sources:**[tt-media-server/cpp_server/README.md 85-101](https://github.com/tenstorrent/tt-inference-server/blob/8ab207f9/tt-media-server/cpp_server/README.md?plain=1#L85-L101)[tt-media-server/cpp_server/CLAUDE.md 80-88](https://github.com/tenstorrent/tt-inference-server/blob/8ab207f9/tt-media-server/cpp_server/CLAUDE.md?plain=1#L80-L88)

#### Session Management and Disaggregation

The server supports advanced distributed inference scenarios, including splitting workloads between Prefill and Decode nodes and offloading session states using Kafka [tt-media-server/cpp_server/src/api/llm_controller.cpp 59-100](https://github.com/tenstorrent/tt-inference-server/blob/8ab207f9/tt-media-server/cpp_server/src/api/llm_controller.cpp#L59-L100)

*   **Details**: For details on inter-server communication and KV cache slot allocation, see [Session Management and Disaggregation](https://deepwiki.com/tenstorrent/tt-inference-server/4.3-session-management-and-disaggregation).

**Sources:**[tt-media-server/cpp_server/CMakeLists.txt 148-150](https://github.com/tenstorrent/tt-inference-server/blob/8ab207f9/tt-media-server/cpp_server/CMakeLists.txt#L148-L150)[tt-media-server/cpp_server/build.sh 48-51](https://github.com/tenstorrent/tt-inference-server/blob/8ab207f9/tt-media-server/cpp_server/build.sh#L48-L51)[tt-media-server/cpp_server/src/api/llm_controller.cpp 48-56](https://github.com/tenstorrent/tt-inference-server/blob/8ab207f9/tt-media-server/cpp_server/src/api/llm_controller.cpp#L48-L56)

#### Runners and Workers

To ensure **Fault Tolerance**, the server employs a multi-process worker model. The main process acts as a supervisor, while workers run in isolated processes via `fork()`/`exec()`[tt-media-server/cpp_server/README.md 23-32](https://github.com/tenstorrent/tt-inference-server/blob/8ab207f9/tt-media-server/cpp_server/README.md?plain=1#L23-L32)

*   **Worker Management**: `WorkerManager` handles the lifecycle and health monitoring of workers [tt-media-server/cpp_server/src/services/llm_service.cpp 37-44](https://github.com/tenstorrent/tt-inference-server/blob/8ab207f9/tt-media-server/cpp_server/src/services/llm_service.cpp#L37-L44)
*   **Details**: For details on the hardware backends and the `LlamaModelRunner` Python bridge, see [Runners, Workers, and Hardware Backends](https://deepwiki.com/tenstorrent/tt-inference-server/4.4-runners-workers-and-hardware-backends).

**Sources:**[tt-media-server/cpp_server/src/main.cpp 37-61](https://github.com/tenstorrent/tt-inference-server/blob/8ab207f9/tt-media-server/cpp_server/src/main.cpp#L37-L61)[tt-media-server/cpp_server/src/services/llm_service.cpp 37-44](https://github.com/tenstorrent/tt-inference-server/blob/8ab207f9/tt-media-server/cpp_server/src/services/llm_service.cpp#L37-L44)

#### Guided Decoding and Utilities

The backend includes specialized utilities for production observability and structured output.

*   **Guided Decoding**: Integration with `xgrammar` for JSON-schema constrained generation [tt-media-server/cpp_server/CMakeLists.txt 75-95](https://github.com/tenstorrent/tt-inference-server/blob/8ab207f9/tt-media-server/cpp_server/CMakeLists.txt#L75-L95)
*   **Observability**: A `ZeroOverheadLogger` and Prometheus metrics integration for monitoring throughput and latency [tt-media-server/cpp_server/README.md 54-67](https://github.com/tenstorrent/tt-inference-server/blob/8ab207f9/tt-media-server/cpp_server/README.md?plain=1#L54-L67)[tt-media-server/cpp_server/CMakeLists.txt 168-180](https://github.com/tenstorrent/tt-inference-server/blob/8ab207f9/tt-media-server/cpp_server/CMakeLists.txt#L168-L180)
*   **Details**: For details on tokenizers and decoding logic, see [Guided Decoding, Tokenizers, and Utilities](https://deepwiki.com/tenstorrent/tt-inference-server/4.5-guided-decoding-tokenizers-and-utilities).

**Sources:**[tt-media-server/cpp_server/CMakeLists.txt 116-126](https://github.com/tenstorrent/tt-inference-server/blob/8ab207f9/tt-media-server/cpp_server/CMakeLists.txt#L116-L126)[tt-media-server/cpp_server/src/main.cpp 93-95](https://github.com/tenstorrent/tt-inference-server/blob/8ab207f9/tt-media-server/cpp_server/src/main.cpp#L93-L95)

* * *

### Code Entity Map

The following diagrams bridge the high-level system concepts to the specific C++ classes and files implementing them.

**Request Flow Mapping**

**Sources:**[tt-media-server/cpp_server/src/main.cpp 167-184](https://github.com/tenstorrent/tt-inference-server/blob/8ab207f9/tt-media-server/cpp_server/src/main.cpp#L167-L184)[tt-media-server/cpp_server/src/services/llm_service.cpp 37-44](https://github.com/tenstorrent/tt-inference-server/blob/8ab207f9/tt-media-server/cpp_server/src/services/llm_service.cpp#L37-L44)[tt-media-server/cpp_server/src/main.cpp 61-89](https://github.com/tenstorrent/tt-inference-server/blob/8ab207f9/tt-media-server/cpp_server/src/main.cpp#L61-L89)

**Worker Management Structure**

**Sources:**[tt-media-server/cpp_server/include/services/llm_service.hpp 86-98](https://github.com/tenstorrent/tt-inference-server/blob/8ab207f9/tt-media-server/cpp_server/include/services/llm_service.hpp#L86-L98)[tt-media-server/cpp_server/src/services/llm_service.cpp 29-44](https://github.com/tenstorrent/tt-inference-server/blob/8ab207f9/tt-media-server/cpp_server/src/services/llm_service.cpp#L29-L44)

* * *

### Non-Functional Requirements

| Priority | Requirement | Implementation Strategy |
| --- | --- | --- |
| 1 | **Performance** | Zero-copy shared memory IPC, minimal serialization, C++20 [tt-media-server/cpp_server/README.md 12-21](https://github.com/tenstorrent/tt-inference-server/blob/8ab207f9/tt-media-server/cpp_server/README.md?plain=1#L12-L21) |
| 2 | **Fault Tolerance** | Multi-process worker isolation, watchdog monitoring, automatic restarts [tt-media-server/cpp_server/README.md 23-32](https://github.com/tenstorrent/tt-inference-server/blob/8ab207f9/tt-media-server/cpp_server/README.md?plain=1#L23-L32) |
| 3 | **Observability** | Structured logging, Prometheus metrics, Tracy instrumentation [tt-media-server/cpp_server/README.md 34-42](https://github.com/tenstorrent/tt-inference-server/blob/8ab207f9/tt-media-server/cpp_server/README.md?plain=1#L34-L42) |
| 4 | **Extensibility** | Pluggable `Runner` interface and isolated adapter layers [tt-media-server/cpp_server/README.md 44-52](https://github.com/tenstorrent/tt-inference-server/blob/8ab207f9/tt-media-server/cpp_server/README.md?plain=1#L44-L52) |

**Sources:**[tt-media-server/cpp_server/README.md 7-52](https://github.com/tenstorrent/tt-inference-server/blob/8ab207f9/tt-media-server/cpp_server/README.md?plain=1#L7-L52)

Dismiss
Refresh this wiki

Enter email to refresh
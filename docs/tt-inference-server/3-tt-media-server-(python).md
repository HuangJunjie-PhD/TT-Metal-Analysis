---
project: tt-inference-server
github: https://github.com/tenstorrent/tt-inference-server
deepwiki: https://deepwiki.com/tenstorrent/tt-inference-server/3-tt-media-server-(python)
---

# tt-media-server (Python)

Relevant source files
*   [.gitignore](https://github.com/tenstorrent/tt-inference-server/blob/8ab207f9/.gitignore)
*   [tt-media-server/Dockerfile](https://github.com/tenstorrent/tt-inference-server/blob/8ab207f9/tt-media-server/Dockerfile)
*   [tt-media-server/README.md](https://github.com/tenstorrent/tt-inference-server/blob/8ab207f9/tt-media-server/README.md?plain=1)
*   [tt-media-server/config/constants.py](https://github.com/tenstorrent/tt-inference-server/blob/8ab207f9/tt-media-server/config/constants.py)
*   [tt-media-server/config/settings.py](https://github.com/tenstorrent/tt-inference-server/blob/8ab207f9/tt-media-server/config/settings.py)
*   [tt-media-server/cpp_server/tsan_suppressions.txt](https://github.com/tenstorrent/tt-inference-server/blob/8ab207f9/tt-media-server/cpp_server/tsan_suppressions.txt)
*   [tt-media-server/device_workers/worker_utils.py](https://github.com/tenstorrent/tt-inference-server/blob/8ab207f9/tt-media-server/device_workers/worker_utils.py)
*   [tt-media-server/fix_dependencies.sh](https://github.com/tenstorrent/tt-inference-server/blob/8ab207f9/tt-media-server/fix_dependencies.sh)
*   [tt-media-server/tests/conftest.py](https://github.com/tenstorrent/tt-inference-server/blob/8ab207f9/tt-media-server/tests/conftest.py)
*   [tt-media-server/tests/test_worker_utils.py](https://github.com/tenstorrent/tt-inference-server/blob/8ab207f9/tt-media-server/tests/test_worker_utils.py)
*   [tt-media-server/tt_model_runners/runner_fabric.py](https://github.com/tenstorrent/tt-inference-server/blob/8ab207f9/tt-media-server/tt_model_runners/runner_fabric.py)
*   [tt-media-server/tt_model_runners/yolov4_runner.py](https://github.com/tenstorrent/tt-inference-server/blob/8ab207f9/tt-media-server/tt_model_runners/yolov4_runner.py)
*   [tt-media-server/utils/runner_utils.py](https://github.com/tenstorrent/tt-inference-server/blob/8ab207f9/tt-media-server/utils/runner_utils.py)
*   [tt-media-server/uv-overrides.txt](https://github.com/tenstorrent/tt-inference-server/blob/8ab207f9/tt-media-server/uv-overrides.txt)

The `tt-media-server` is a Python-based inference application built on **FastAPI**. It serves as the orchestration layer for Tenstorrent hardware, providing OpenAI-compatible endpoints for a diverse range of modalities including Image Generation (SDXL, FLUX, SD3.5), Audio (Whisper, SpeechT5), Video (Mochi, Wan2.2), and Computer Vision (YOLOv4, ResNet). It manages the lifecycle of model runners, handles multi-process scheduling, and abstracts hardware interactions through a unified service architecture.

### System Architecture

The server follows a layered architecture where API controllers delegate work to specialized **Model Services**, which in turn use a **Scheduler** to dispatch tasks to **Device Workers** running in isolated processes.

#### Code-to-System Mapping

The following diagram illustrates how high-level system components map to specific code entities within the `tt-media-server` directory.

**Media Server Component Map**

**Sources:**[tt-media-server/README.md 20-30](https://github.com/tenstorrent/tt-inference-server/blob/8ab207f9/tt-media-server/README.md?plain=1#L20-L30)[tt-media-server/tt_model_runners/runner_fabric.py 136-148](https://github.com/tenstorrent/tt-inference-server/blob/8ab207f9/tt-media-server/tt_model_runners/runner_fabric.py#L136-L148)[tt-media-server/config/settings.py 32-91](https://github.com/tenstorrent/tt-inference-server/blob/8ab207f9/tt-media-server/config/settings.py#L32-L91)

* * *

### API Layer and Service Routing

The API layer is built with FastAPI and adheres to the OpenAI API standard for interoperability. It supports versioned paths (e.g., `/v1/images/generations`) and provides legacy support for unversioned paths via deprecation headers [tt-media-server/README.md 33-72](https://github.com/tenstorrent/tt-inference-server/blob/8ab207f9/tt-media-server/README.md?plain=1#L33-L72)

*   **Service Resolver:** A singleton pattern used to instantiate the correct `BaseService` implementation based on the `MODEL_RUNNER` and `MODEL` environment variables [tt-media-server/config/settings.py 127-158](https://github.com/tenstorrent/tt-inference-server/blob/8ab207f9/tt-media-server/config/settings.py#L127-L158)
*   **Routing:** The `MODEL_SERVICE_RUNNER_MAP` directs incoming requests to the appropriate model logic such as `ModelServices.IMAGE`, `ModelServices.AUDIO`, or `ModelServices.VIDEO`[tt-media-server/config/constants.py 139-193](https://github.com/tenstorrent/tt-inference-server/blob/8ab207f9/tt-media-server/config/constants.py#L139-L193)

For details, see [API Layer and Service Routing](https://deepwiki.com/tenstorrent/tt-inference-server/3.1-api-layer-and-service-routing).

**Sources:**[tt-media-server/README.md 33-72](https://github.com/tenstorrent/tt-inference-server/blob/8ab207f9/tt-media-server/README.md?plain=1#L33-L72)[tt-media-server/config/settings.py 149-159](https://github.com/tenstorrent/tt-inference-server/blob/8ab207f9/tt-media-server/config/settings.py#L149-L159)[tt-media-server/config/constants.py 139-193](https://github.com/tenstorrent/tt-inference-server/blob/8ab207f9/tt-media-server/config/constants.py#L139-L193)

* * *

### Model Services and Scheduler

Model services manage high-level task logic, such as pre-processing and result post-processing. The server utilizes a multi-process architecture to isolate hardware-specific execution from the API front-end.

*   **Multi-process Scheduler:** The `Scheduler` manages a pool of workers. It uses queues to distribute work and return results to the async API handlers [tt-media-server/config/settings.py 61-67](https://github.com/tenstorrent/tt-inference-server/blob/8ab207f9/tt-media-server/config/settings.py#L61-L67)
*   **Device Workers:** Each worker manages the lifecycle of a single `BaseDeviceRunner` on a specific Tenstorrent device, handling model loading, warmup, and inference execution [tt-media-server/device_workers/worker_utils.py 12-34](https://github.com/tenstorrent/tt-inference-server/blob/8ab207f9/tt-media-server/device_workers/worker_utils.py#L12-L34)
*   **Health & Reset:** Supports "Deep Resets" and per-device restarts to recover from hardware hangs via `reset_device_command`[tt-media-server/config/settings.py 41-43](https://github.com/tenstorrent/tt-inference-server/blob/8ab207f9/tt-media-server/config/settings.py#L41-L43)

For details, see [Model Services and Scheduler](https://deepwiki.com/tenstorrent/tt-inference-server/3.2-model-services-and-scheduler).

**Sources:**[tt-media-server/device_workers/worker_utils.py 12-41](https://github.com/tenstorrent/tt-inference-server/blob/8ab207f9/tt-media-server/device_workers/worker_utils.py#L12-L41)[tt-media-server/config/settings.py 41-43](https://github.com/tenstorrent/tt-inference-server/blob/8ab207f9/tt-media-server/config/settings.py#L41-L43)[tt-media-server/config/settings.py 61-67](https://github.com/tenstorrent/tt-inference-server/blob/8ab207f9/tt-media-server/config/settings.py#L61-L67)

* * *

### Model Runners

Runners are the concrete implementations that execute models on Tenstorrent hardware using `ttnn`, `forge`, or `torch_xla`.

*   **Runner Fabric:** A factory module `runner_fabric.py` that dynamically imports and instantiates runners based on `ModelRunners` enums [tt-media-server/tt_model_runners/runner_fabric.py 10-133](https://github.com/tenstorrent/tt-inference-server/blob/8ab207f9/tt-media-server/tt_model_runners/runner_fabric.py#L10-L133)
*   **Supported Modalities:**
    *   **Diffusion (Image/Video):** SDXL, SD3.5, FLUX.1, Mochi-1, Wan2.2 [tt-media-server/tt_model_runners/runner_fabric.py 11-48](https://github.com/tenstorrent/tt-inference-server/blob/8ab207f9/tt-media-server/tt_model_runners/runner_fabric.py#L11-L48)
    *   **Audio:**`TTWhisperRunner` for speech-to-text and `TTSpeechT5Runner` for TTS [tt-media-server/tt_model_runners/runner_fabric.py 49-51](https://github.com/tenstorrent/tt-inference-server/blob/8ab207f9/tt-media-server/tt_model_runners/runner_fabric.py#L49-L51)[tt-media-server/tt_model_runners/runner_fabric.py 122-124](https://github.com/tenstorrent/tt-inference-server/blob/8ab207f9/tt-media-server/tt_model_runners/runner_fabric.py#L122-L124)
    *   **Vision:**`TTYolov4Runner` for object detection and various Forge-based CNN runners (ResNet, MobileNet) [tt-media-server/tt_model_runners/yolov4_runner.py 37-48](https://github.com/tenstorrent/tt-inference-server/blob/8ab207f9/tt-media-server/tt_model_runners/yolov4_runner.py#L37-L48)[tt-media-server/tt_model_runners/runner_fabric.py 83-103](https://github.com/tenstorrent/tt-inference-server/blob/8ab207f9/tt-media-server/tt_model_runners/runner_fabric.py#L83-L103)
    *   **LLM/Embedding:** Integrations for `VLLMForgeRunner` and BGE/Qwen embeddings [tt-media-server/tt_model_runners/runner_fabric.py 52-82](https://github.com/tenstorrent/tt-inference-server/blob/8ab207f9/tt-media-server/tt_model_runners/runner_fabric.py#L52-L82)

For details, see [Model Runners](https://deepwiki.com/tenstorrent/tt-inference-server/3.3-model-runners).

**Sources:**[tt-media-server/tt_model_runners/runner_fabric.py 10-133](https://github.com/tenstorrent/tt-inference-server/blob/8ab207f9/tt-media-server/tt_model_runners/runner_fabric.py#L10-L133)[tt-media-server/tt_model_runners/yolov4_runner.py 37-48](https://github.com/tenstorrent/tt-inference-server/blob/8ab207f9/tt-media-server/tt_model_runners/yolov4_runner.py#L37-L48)[tt-media-server/config/constants.py 87-126](https://github.com/tenstorrent/tt-inference-server/blob/8ab207f9/tt-media-server/config/constants.py#L87-L126)

* * *

### Supported Models and Configuration

The server's behavior is heavily driven by the `Settings` class which resolves hardware configurations (N150, Galaxy, T3K) and model parameters [tt-media-server/config/settings.py 32-66](https://github.com/tenstorrent/tt-inference-server/blob/8ab207f9/tt-media-server/config/settings.py#L32-L66)

| Category | Supported Models (Examples) |
| --- | --- |
| **Image** | SDXL, SD3.5, FLUX.1-dev, Motif-Image-6B, Z-Image-Turbo [tt-media-server/config/constants.py 140-152](https://github.com/tenstorrent/tt-inference-server/blob/8ab207f9/tt-media-server/config/constants.py#L140-L152) |
| **Video** | Mochi-1, Wan2.2, Wan2.2-I2V [tt-media-server/config/constants.py 180-185](https://github.com/tenstorrent/tt-inference-server/blob/8ab207f9/tt-media-server/config/constants.py#L180-L185) |
| **Audio** | Whisper Large v3, SpeechT5 (TTS) [tt-media-server/config/constants.py 177-179](https://github.com/tenstorrent/tt-inference-server/blob/8ab207f9/tt-media-server/config/constants.py#L177-L179)[tt-media-server/config/constants.py 190-192](https://github.com/tenstorrent/tt-inference-server/blob/8ab207f9/tt-media-server/config/constants.py#L190-L192) |
| **CNN/Vision** | YOLOv4, ResNet-50, MobileNetV2, Segformer, ViT [tt-media-server/config/constants.py 167-176](https://github.com/tenstorrent/tt-inference-server/blob/8ab207f9/tt-media-server/config/constants.py#L167-L176) |
| **LLM/Embed** | Llama-3.1/3.2, Qwen3, BGE-M3 [tt-media-server/config/constants.py 153-166](https://github.com/tenstorrent/tt-inference-server/blob/8ab207f9/tt-media-server/config/constants.py#L153-L166) |

**Model Configuration Flow**

For details on Fine-Tuning, see [Fine-Tuning and Training](https://deepwiki.com/tenstorrent/tt-inference-server/3.4-fine-tuning-and-training). For details on IPC and Utilities, see [Configuration, IPC, and Utilities](https://deepwiki.com/tenstorrent/tt-inference-server/3.5-configuration-ipc-and-utilities).

**Sources:**[tt-media-server/config/constants.py 8-40](https://github.com/tenstorrent/tt-inference-server/blob/8ab207f9/tt-media-server/config/constants.py#L8-L40)[tt-media-server/config/constants.py 139-193](https://github.com/tenstorrent/tt-inference-server/blob/8ab207f9/tt-media-server/config/constants.py#L139-L193)[tt-media-server/config/settings.py 117-191](https://github.com/tenstorrent/tt-inference-server/blob/8ab207f9/tt-media-server/config/settings.py#L117-L191)

Dismiss
Refresh this wiki

Enter email to refresh
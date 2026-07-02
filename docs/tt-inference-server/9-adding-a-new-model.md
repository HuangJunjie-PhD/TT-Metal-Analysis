---
project: tt-inference-server
github: https://github.com/tenstorrent/tt-inference-server
deepwiki: https://deepwiki.com/tenstorrent/tt-inference-server/9-adding-a-new-model
---

# Adding a New Model

Relevant source files
*   [.github/ISSUE_TEMPLATE/model-readiness.yml](https://github.com/tenstorrent/tt-inference-server/blob/8ab207f9/.github/ISSUE_TEMPLATE/model-readiness.yml)
*   [benchmarking/benchmark_config.py](https://github.com/tenstorrent/tt-inference-server/blob/8ab207f9/benchmarking/benchmark_config.py)
*   [benchmarking/benchmark_targets/model_performance_reference.json](https://github.com/tenstorrent/tt-inference-server/blob/8ab207f9/benchmarking/benchmark_targets/model_performance_reference.json)
*   [benchmarking/run_benchmarks.py](https://github.com/tenstorrent/tt-inference-server/blob/8ab207f9/benchmarking/run_benchmarks.py)
*   [benchmarking/summary_report.py](https://github.com/tenstorrent/tt-inference-server/blob/8ab207f9/benchmarking/summary_report.py)
*   [docs/add_support_for_new_model.md](https://github.com/tenstorrent/tt-inference-server/blob/8ab207f9/docs/add_support_for_new_model.md?plain=1)
*   [evals/eval_config.py](https://github.com/tenstorrent/tt-inference-server/blob/8ab207f9/evals/eval_config.py)
*   [evals/run_evals.py](https://github.com/tenstorrent/tt-inference-server/blob/8ab207f9/evals/run_evals.py)
*   [server_tests/test_categorization_system/__init__.py](https://github.com/tenstorrent/tt-inference-server/blob/8ab207f9/server_tests/test_categorization_system/__init__.py)
*   [server_tests/test_categorization_system/suite_loader.py](https://github.com/tenstorrent/tt-inference-server/blob/8ab207f9/server_tests/test_categorization_system/suite_loader.py)
*   [server_tests/test_categorization_system/test_filter.py](https://github.com/tenstorrent/tt-inference-server/blob/8ab207f9/server_tests/test_categorization_system/test_filter.py)
*   [server_tests/test_suites/audio.json](https://github.com/tenstorrent/tt-inference-server/blob/8ab207f9/server_tests/test_suites/audio.json)
*   [server_tests/test_suites/cnn.json](https://github.com/tenstorrent/tt-inference-server/blob/8ab207f9/server_tests/test_suites/cnn.json)
*   [server_tests/test_suites/embedding.json](https://github.com/tenstorrent/tt-inference-server/blob/8ab207f9/server_tests/test_suites/embedding.json)
*   [server_tests/test_suites/image.json](https://github.com/tenstorrent/tt-inference-server/blob/8ab207f9/server_tests/test_suites/image.json)
*   [server_tests/test_suites/tts.json](https://github.com/tenstorrent/tt-inference-server/blob/8ab207f9/server_tests/test_suites/tts.json)
*   [server_tests/test_suites/video.json](https://github.com/tenstorrent/tt-inference-server/blob/8ab207f9/server_tests/test_suites/video.json)
*   [tests/test_benchmark_concurrency_sweeps.py](https://github.com/tenstorrent/tt-inference-server/blob/8ab207f9/tests/test_benchmark_concurrency_sweeps.py)
*   [tests/test_benchmark_config.py](https://github.com/tenstorrent/tt-inference-server/blob/8ab207f9/tests/test_benchmark_config.py)
*   [tests/test_run_evals.py](https://github.com/tenstorrent/tt-inference-server/blob/8ab207f9/tests/test_run_evals.py)
*   [workflows/model_spec.py](https://github.com/tenstorrent/tt-inference-server/blob/8ab207f9/workflows/model_spec.py)
*   [workflows/run_reports.py](https://github.com/tenstorrent/tt-inference-server/blob/8ab207f9/workflows/run_reports.py)
*   [workflows/workflow_types.py](https://github.com/tenstorrent/tt-inference-server/blob/8ab207f9/workflows/workflow_types.py)
*   [workflows/workflow_venvs.py](https://github.com/tenstorrent/tt-inference-server/blob/8ab207f9/workflows/workflow_venvs.py)

This guide provides an end-to-end technical workflow for onboarding a new model to the `tt-inference-server`. The process involves defining the model's hardware requirements, integrating it into the benchmarking and evaluation pipelines, and certifying it for release through CI/CD.

## 1. Onboarding Initialization

The process begins with a **Model Readiness** GitHub issue. This serves as a checklist for the engineering team to track implementation, performance targets, and accuracy verification.

*   **GitHub Issue Template:**`.github/ISSUE_TEMPLATE/model-readiness.yml` provides the structure for reporting model status, including the target `InferenceEngine` (vLLM, Forge, or Media Server) and supported `DeviceTypes`.
*   **Tracking:** Step 1 of the onboarding process requires creating this issue to track the model's progress through the certification gates [docs/add_support_for_new_model.md 12-21](https://github.com/tenstorrent/tt-inference-server/blob/8ab207f9/docs/add_support_for_new_model.md?plain=1#L12-L21)

## 2. Model Specification Registry

The core of model onboarding is defining a `ModelSpecTemplate` in `workflows/model_spec.py`. This template is expanded into specific `DeviceModelSpec` entries for each hardware target (N150, N300, T3K, Galaxy, etc.).

### Key Components of a Model Spec

*   **Model ID & Repo:** Unique identifier and the HuggingFace repository path [workflows/model_spec.py 180-188](https://github.com/tenstorrent/tt-inference-server/blob/8ab207f9/workflows/model_spec.py#L180-L188)
*   **Inference Engine:** Specifies if the model runs via `InferenceEngine.VLLM`, `InferenceEngine.FORGE`, or `InferenceEngine.MEDIA`[workflows/workflow_types.py 22](https://github.com/tenstorrent/tt-inference-server/blob/8ab207f9/workflows/workflow_types.py#L22-L22)
*   **Implementation Specs (`ImplSpec`):** Defines the Docker image, environment variables, and entrypoint commands [workflows/model_spec.py 236-248](https://github.com/tenstorrent/tt-inference-server/blob/8ab207f9/workflows/model_spec.py#L236-L248)
*   **Hardware Overrides:** The `DeviceModelSpec` allows setting `max_concurrency`, `max_context`, and `override_tt_config` (e.g., `dispatch_core_axis`, `trace_region_size`) specific to Tenstorrent hardware [docs/add_support_for_new_model.md 37-51](https://github.com/tenstorrent/tt-inference-server/blob/8ab207f9/docs/add_support_for_new_model.md?plain=1#L37-L51)

### Model Registration Data Flow

The following diagram illustrates how a natural language model request becomes a concrete code entity in the registry.

**Model Registration Flow**

Sources: [workflows/model_spec.py 236-248](https://github.com/tenstorrent/tt-inference-server/blob/8ab207f9/workflows/model_spec.py#L236-L248)[docs/add_support_for_new_model.md 28-65](https://github.com/tenstorrent/tt-inference-server/blob/8ab207f9/docs/add_support_for_new_model.md?plain=1#L28-L65)

## 3. Benchmarking Configuration

Once registered, a model must be added to `BENCHMARK_CONFIGS` in `benchmarking/benchmark_config.py`.

*   **Task Type Selection:** Map the model to a `BenchmarkTaskType` (e.g., `HTTP_CLIENT_VLLM_API` for text/VLM, `HTTP_CLIENT_CNN_API` for media) [benchmarking/benchmark_config.py 22-55](https://github.com/tenstorrent/tt-inference-server/blob/8ab207f9/benchmarking/benchmark_config.py#L22-L55)
*   **Parameter Sweeps:** Define standard Input Sequence Length (ISL) and Output Sequence Length (OSL) pairs. For VLMs, this includes image resolutions and `images_per_prompt`[benchmarking/benchmark_config.py 64-85](https://github.com/tenstorrent/tt-inference-server/blob/8ab207f9/benchmarking/benchmark_config.py#L64-L85)
*   **Performance Targets:** Theoretical targets (TTFT and Throughput) must be added to `benchmarking/benchmark_targets/model_performance_reference.json`[benchmarking/benchmark_targets/model_performance_reference.json 1-20](https://github.com/tenstorrent/tt-inference-server/blob/8ab207f9/benchmarking/benchmark_targets/model_performance_reference.json#L1-L20)

### Performance Scaling

For multi-device deployments (e.g., T3K or Galaxy), the system automatically scales throughput targets based on the `data_parallel` factor defined in the spec [workflows/model_spec.py 132-160](https://github.com/tenstorrent/tt-inference-server/blob/8ab207f9/workflows/model_spec.py#L132-L160) The `get_perf_reference` function handles the resolution of these scaled targets [workflows/model_spec.py 163-177](https://github.com/tenstorrent/tt-inference-server/blob/8ab207f9/workflows/model_spec.py#L163-L177)

Sources: [benchmarking/benchmark_config.py 22-55](https://github.com/tenstorrent/tt-inference-server/blob/8ab207f9/benchmarking/benchmark_config.py#L22-L55)[workflows/model_spec.py 132-177](https://github.com/tenstorrent/tt-inference-server/blob/8ab207f9/workflows/model_spec.py#L132-L177)[benchmarking/run_benchmarks.py 134-195](https://github.com/tenstorrent/tt-inference-server/blob/8ab207f9/benchmarking/run_benchmarks.py#L134-L195)

## 4. Accuracy Evaluation Selection

Models are validated using `run_evals.py` which leverages `lm-evaluation-harness` or `lmms-eval` via specialized virtual environments.

*   **EvalConfig Definition:** Add an entry to `_eval_config_list` in `evals/eval_config.py`[evals/eval_config.py 93-130](https://github.com/tenstorrent/tt-inference-server/blob/8ab207f9/evals/eval_config.py#L93-L130)
*   **EvalTask:** Define the specific task (e.g., `ifeval`, `mmlu_pro`, `gsm8k`) and the expected `published_score` or `gpu_reference_score`[evals/eval_config.py 30-61](https://github.com/tenstorrent/tt-inference-server/blob/8ab207f9/evals/eval_config.py#L30-L61)
*   **Venv Management:** The system uses `uv` to bootstrap isolated environments like `WorkflowVenvType.EVALS_COMMON` (for text) or `EVALS_AUDIO` to avoid dependency conflicts [workflows/workflow_venvs.py 37-43](https://github.com/tenstorrent/tt-inference-server/blob/8ab207f9/workflows/workflow_venvs.py#L37-L43)

### Media Model Accuracy

For Image models like SDXL or FLUX, accuracy is measured using FID and CLIP scores. Valid ranges are defined in `evals/eval_targets/model_accuracy_reference.json`.

**Evaluation Execution Flow**

Sources: [evals/run_evals.py 203-215](https://github.com/tenstorrent/tt-inference-server/blob/8ab207f9/evals/run_evals.py#L203-L215)[workflows/workflow_venvs.py 67-85](https://github.com/tenstorrent/tt-inference-server/blob/8ab207f9/workflows/workflow_venvs.py#L67-L85)[evals/eval_config.py 30-61](https://github.com/tenstorrent/tt-inference-server/blob/8ab207f9/evals/eval_config.py#L30-L61)

## 5. Server Integration Tests

Before a model is considered "Ready," it must pass the integration suite. This involves adding the model to specific test suites based on its modality.

*   **Suite Configuration:** Integration tests are defined in JSON files (e.g., `server_tests/test_suites/image.json`, `server_tests/test_suites/video.json`) [server_tests/test_suites/video.json 1-99](https://github.com/tenstorrent/tt-inference-server/blob/8ab207f9/server_tests/test_suites/video.json#L1-L99)
*   **Test Types:** Models undergo `load` tests for concurrency stability and `param` tests for API parameter validation [server_tests/test_suites/video.json 19-48](https://github.com/tenstorrent/tt-inference-server/blob/8ab207f9/server_tests/test_suites/video.json#L19-L48)
*   **Health Checks:** For media models, `_check_media_server_health` is called to ensure the runner is active before starting tests [evals/run_evals.py 100-138](https://github.com/tenstorrent/tt-inference-server/blob/8ab207f9/evals/run_evals.py#L100-L138)

Sources: [evals/run_evals.py 100-138](https://github.com/tenstorrent/tt-inference-server/blob/8ab207f9/evals/run_evals.py#L100-L138)[server_tests/test_suites/video.json 1-99](https://github.com/tenstorrent/tt-inference-server/blob/8ab207f9/server_tests/test_suites/video.json#L1-L99)

## 6. Release Certification Checklist

To promote a model to a release-ready state, it must satisfy the requirements in the reporting and acceptance system.

1.   **CI Gates:** The model must pass the `test-gate.yml` workflow which includes unit tests and C++ server benchmarks.
2.   **Acceptance Criteria:**`workflows/run_reports.py` uses `acceptance_criteria_check` to compare the `benchmark_*.json` results against the `theoretical` targets in `model_performance_reference.json`[workflows/run_reports.py 33-36](https://github.com/tenstorrent/tt-inference-server/blob/8ab207f9/workflows/run_reports.py#L33-L36)
3.   **Accuracy Certification:** Evaluation results must fall within the `tolerance` defined in the `EvalTaskScore`[evals/eval_config.py 19-26](https://github.com/tenstorrent/tt-inference-server/blob/8ab207f9/evals/eval_config.py#L19-L26)
4.   **Documentation:** The model's `ModelStatusTypes` in `model_spec.py` is updated to `COMPLETE` or `SELLABLE`[workflows/model_spec.py 23](https://github.com/tenstorrent/tt-inference-server/blob/8ab207f9/workflows/model_spec.py#L23-L23)

**Release Promotion Diagram**

Sources: [workflows/run_reports.py 33-36](https://github.com/tenstorrent/tt-inference-server/blob/8ab207f9/workflows/run_reports.py#L33-L36)[evals/eval_config.py 19-26](https://github.com/tenstorrent/tt-inference-server/blob/8ab207f9/evals/eval_config.py#L19-L26)[workflows/model_spec.py 23](https://github.com/tenstorrent/tt-inference-server/blob/8ab207f9/workflows/model_spec.py#L23-L23)

Dismiss
Refresh this wiki

Enter email to refresh
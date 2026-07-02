---
project: tt-inference-server
github: https://github.com/tenstorrent/tt-inference-server
deepwiki: https://deepwiki.com/tenstorrent/tt-inference-server/6-benchmarking-and-evaluation
---

# Benchmarking and Evaluation

Relevant source files
*   [benchmarking/README.md](https://github.com/tenstorrent/tt-inference-server/blob/8ab207f9/benchmarking/README.md?plain=1)
*   [benchmarking/benchmark_config.py](https://github.com/tenstorrent/tt-inference-server/blob/8ab207f9/benchmarking/benchmark_config.py)
*   [benchmarking/benchmark_targets/model_performance_reference.json](https://github.com/tenstorrent/tt-inference-server/blob/8ab207f9/benchmarking/benchmark_targets/model_performance_reference.json)
*   [benchmarking/run_benchmarks.py](https://github.com/tenstorrent/tt-inference-server/blob/8ab207f9/benchmarking/run_benchmarks.py)
*   [benchmarking/summary_report.py](https://github.com/tenstorrent/tt-inference-server/blob/8ab207f9/benchmarking/summary_report.py)
*   [evals/README.md](https://github.com/tenstorrent/tt-inference-server/blob/8ab207f9/evals/README.md?plain=1)
*   [evals/eval_config.py](https://github.com/tenstorrent/tt-inference-server/blob/8ab207f9/evals/eval_config.py)
*   [evals/run_evals.py](https://github.com/tenstorrent/tt-inference-server/blob/8ab207f9/evals/run_evals.py)
*   [utils/README.md](https://github.com/tenstorrent/tt-inference-server/blob/8ab207f9/utils/README.md?plain=1)
*   [utils/batch_processor.py](https://github.com/tenstorrent/tt-inference-server/blob/8ab207f9/utils/batch_processor.py)
*   [utils/prompt_client.py](https://github.com/tenstorrent/tt-inference-server/blob/8ab207f9/utils/prompt_client.py)
*   [utils/prompt_client_cli.py](https://github.com/tenstorrent/tt-inference-server/blob/8ab207f9/utils/prompt_client_cli.py)
*   [utils/prompt_configs.py](https://github.com/tenstorrent/tt-inference-server/blob/8ab207f9/utils/prompt_configs.py)
*   [utils/prompt_generation.py](https://github.com/tenstorrent/tt-inference-server/blob/8ab207f9/utils/prompt_generation.py)
*   [workflows/model_spec.py](https://github.com/tenstorrent/tt-inference-server/blob/8ab207f9/workflows/model_spec.py)
*   [workflows/run_reports.py](https://github.com/tenstorrent/tt-inference-server/blob/8ab207f9/workflows/run_reports.py)
*   [workflows/workflow_types.py](https://github.com/tenstorrent/tt-inference-server/blob/8ab207f9/workflows/workflow_types.py)
*   [workflows/workflow_venvs.py](https://github.com/tenstorrent/tt-inference-server/blob/8ab207f9/workflows/workflow_venvs.py)

The `tt-inference-server` provides a comprehensive suite for measuring both the performance (latency/throughput) and accuracy of supported models. These systems are integrated into the core `run.py` workflow, allowing for automated regression testing and performance validation across different Tenstorrent hardware generations.

## Performance Benchmarking

Performance benchmarking is orchestrated by `run_benchmarks.py`, which supports multiple backend tools including a custom high-concurrency HTTP client, `genai-perf`, and `aiperf`. The system evaluates models across various dimensions such as Input Sequence Length (ISL), Output Sequence Length (OSL), and request concurrency.

*   **Concurrency Sweeps**: The system can automatically expand benchmark parameters to test powers-of-two concurrency levels up to the model's maximum capacity using `expand_concurrency_sweep_params`[benchmarking/benchmark_config.py 31-34](https://github.com/tenstorrent/tt-inference-server/blob/8ab207f9/benchmarking/benchmark_config.py#L31-L34)
*   **Trace Capture & Warmup**: To ensure accurate measurements on Tenstorrent hardware, the benchmarker manages trace capture and warmup. If a model has `has_builtin_warmup` enabled in its spec, client-side trace capture is automatically disabled to avoid redundant captures [benchmarking/run_benchmarks.py 215-223](https://github.com/tenstorrent/tt-inference-server/blob/8ab207f9/benchmarking/run_benchmarks.py#L215-L223)
*   **Media Task Routing**: For non-LLM models (Image, Audio, CNN, etc.), the `MediaClientFactory` routes tasks to specialized strategies defined in `BENCHMARKS_TASK_TYPES`[benchmarking/run_benchmarks.py 64-71](https://github.com/tenstorrent/tt-inference-server/blob/8ab207f9/benchmarking/run_benchmarks.py#L64-L71)
*   **VLM Benchmarking**: Specialized support for Vision-Language Models includes parameters for `image_height`, `image_width`, and `images_per_prompt` to accurately calculate vision tokens based on model-specific architectures (e.g., Qwen2.5-VL vs Gemma-3) [benchmarking/benchmark_config.py 184-225](https://github.com/tenstorrent/tt-inference-server/blob/8ab207f9/benchmarking/benchmark_config.py#L184-L225)

For details, see [Performance Benchmarking](https://deepwiki.com/tenstorrent/tt-inference-server/6.1-performance-benchmarking).

### Performance Logic Flow

The diagram below illustrates how benchmark parameters are resolved and executed.

**Sources:**[benchmarking/run_benchmarks.py 29-51](https://github.com/tenstorrent/tt-inference-server/blob/8ab207f9/benchmarking/run_benchmarks.py#L29-L51)[benchmarking/benchmark_config.py 31-34](https://github.com/tenstorrent/tt-inference-server/blob/8ab207f9/benchmarking/benchmark_config.py#L31-L34)[benchmarking/run_genai_benchmarks.py 35](https://github.com/tenstorrent/tt-inference-server/blob/8ab207f9/benchmarking/run_genai_benchmarks.py#L35-L35)

## Accuracy Evaluations

Accuracy evaluations ensure that model implementations produce correct outputs compared to reference scores. The system integrates with industry-standard harnesses like `lm-evaluation-harness` and `lmms-eval`.

*   **Task Selection**: Evaluations are defined as `EvalTask` objects within `EVAL_CONFIGS`, specifying the dataset (e.g., `ifeval`, `mmlu_pro`), few-shot settings, and scoring functions like `score_task_single_key`[evals/eval_config.py 30-61](https://github.com/tenstorrent/tt-inference-server/blob/8ab207f9/evals/eval_config.py#L30-L61)[evals/eval_config.py 99-111](https://github.com/tenstorrent/tt-inference-server/blob/8ab207f9/evals/eval_config.py#L99-L111)
*   **Environment Isolation**: Each evaluation runs in a dedicated virtual environment (e.g., `WorkflowVenvType.EVALS_COMMON` or `EVALS_AUDIO`) managed by `uv` to prevent dependency conflicts between different evaluation harnesses [evals/run_evals.py 217-222](https://github.com/tenstorrent/tt-inference-server/blob/8ab207f9/evals/run_evals.py#L217-L222)[workflows/workflow_venvs.py 88-102](https://github.com/tenstorrent/tt-inference-server/blob/8ab207f9/workflows/workflow_venvs.py#L88-L102)
*   **Reference Comparison**: Results are compared against published or GPU-reference scores stored in the `EvalTaskScore` dataclass, with configurable tolerances [evals/eval_config.py 19-27](https://github.com/tenstorrent/tt-inference-server/blob/8ab207f9/evals/eval_config.py#L19-L27)

For details, see [Accuracy Evaluations](https://deepwiki.com/tenstorrent/tt-inference-server/6.2-accuracy-evaluations).

**Sources:**[evals/run_evals.py 28-44](https://github.com/tenstorrent/tt-inference-server/blob/8ab207f9/evals/run_evals.py#L28-L44)[evals/eval_config.py 18-61](https://github.com/tenstorrent/tt-inference-server/blob/8ab207f9/evals/eval_config.py#L18-L61)[workflows/workflow_venvs.py 35-43](https://github.com/tenstorrent/tt-inference-server/blob/8ab207f9/workflows/workflow_venvs.py#L35-L43)

## Reporting and Acceptance Criteria

The reporting system aggregates raw JSON results from benchmarks and evaluations into human-readable formats and validates them against performance targets.

*   **Performance Targets**: Theoretical targets (TTFT, Throughput) are stored in `model_performance_reference.json`[workflows/model_spec.py 65-76](https://github.com/tenstorrent/tt-inference-server/blob/8ab207f9/workflows/model_spec.py#L65-L76) These are scaled based on `data_parallel` configurations for multi-chip deployments via `scale_llm_perf_targets`[workflows/model_spec.py 132-160](https://github.com/tenstorrent/tt-inference-server/blob/8ab207f9/workflows/model_spec.py#L132-L160)
*   **Acceptance Checks**: The `acceptance_criteria_check` function compares measured P50/P99 latencies and throughput against defined thresholds [workflows/run_reports.py 33-36](https://github.com/tenstorrent/tt-inference-server/blob/8ab207f9/workflows/run_reports.py#L33-L36)
*   **Multi-Modality Reporting**: Specialized reporting logic exists for `audio`, `cnn`, `image`, `video`, `tts`, and `embedding` tasks to handle modality-specific metrics like FID, CLIP, or Word Error Rate (WER) patterns [workflows/run_reports.py 60-146](https://github.com/tenstorrent/tt-inference-server/blob/8ab207f9/workflows/run_reports.py#L60-L146)

For details, see [Reporting and Acceptance Criteria](https://deepwiki.com/tenstorrent/tt-inference-server/6.3-reporting-and-acceptance-criteria).

| Metric | Code Reference | Purpose |
| --- | --- | --- |
| **TTFT** | `PerformanceTarget.ttft_ms` | Time to First Token latency target [workflows/model_spec.py 103-105](https://github.com/tenstorrent/tt-inference-server/blob/8ab207f9/workflows/model_spec.py#L103-L105) |
| **TPUT** | `PerformanceTarget.tput` | Total system throughput (tokens/sec) [workflows/model_spec.py 109-111](https://github.com/tenstorrent/tt-inference-server/blob/8ab207f9/workflows/model_spec.py#L109-L111) |
| **ISL/OSL** | `BenchmarkTaskParams` | Input/Output sequence length definitions [workflows/model_spec.py 115-125](https://github.com/tenstorrent/tt-inference-server/blob/8ab207f9/workflows/model_spec.py#L115-L125) |

**Sources:**[workflows/run_reports.py 21-48](https://github.com/tenstorrent/tt-inference-server/blob/8ab207f9/workflows/run_reports.py#L21-L48)[workflows/model_spec.py 61-125](https://github.com/tenstorrent/tt-inference-server/blob/8ab207f9/workflows/model_spec.py#L61-L125)[benchmarking/summary_report.py 95-137](https://github.com/tenstorrent/tt-inference-server/blob/8ab207f9/benchmarking/summary_report.py#L95-L137)

## Prompt Client and Test Utilities

The `PromptClient` serves as the unified interface for interacting with the inference server during benchmarks and evals.

*   **Media Clients**: The `MediaClientFactory` provides specialized clients for different `MediaTaskType` values (e.g., `AUDIO`, `IMAGE`, `VIDEO`) to abstract away modality-specific API calls [utils/media_clients/media_client_factory.py 20-21](https://github.com/tenstorrent/tt-inference-server/blob/8ab207f9/utils/media_clients/media_client_factory.py#L20-L21)
*   **Environment Config**: `EnvironmentConfig` manages the base URL and API keys (including JWT-based auth) required for secure communication with the server [utils/prompt_configs.py 16](https://github.com/tenstorrent/tt-inference-server/blob/8ab207f9/utils/prompt_configs.py#L16-L16)
*   **VLM Utilities**: Functions like `calculate_vision_tokens` help estimate the impact of image inputs on total context window usage based on hardware-specific tiling logic [benchmarking/benchmark_config.py 184-203](https://github.com/tenstorrent/tt-inference-server/blob/8ab207f9/benchmarking/benchmark_config.py#L184-L203)

For details, see [Prompt Client and Test Utilities](https://deepwiki.com/tenstorrent/tt-inference-server/6.4-prompt-client-and-test-utilities).

### Evaluation and Client Architecture

The following diagram bridges the high-level evaluation concepts to the client-side code entities used to execute them.

**Sources:**[evals/eval_config.py 30-61](https://github.com/tenstorrent/tt-inference-server/blob/8ab207f9/evals/eval_config.py#L30-L61)[workflows/workflow_types.py 26-50](https://github.com/tenstorrent/tt-inference-server/blob/8ab207f9/workflows/workflow_types.py#L26-L50)[utils/prompt_client.py 83-103](https://github.com/tenstorrent/tt-inference-server/blob/8ab207f9/utils/prompt_client.py#L83-L103)[utils/media_clients/media_client_factory.py 20-21](https://github.com/tenstorrent/tt-inference-server/blob/8ab207f9/utils/media_clients/media_client_factory.py#L20-L21)

This wiki is featured in the [repository](https://github.com/tenstorrent/tt-inference-server/blob/main/README.md)

Dismiss
Refresh this wiki

Enter email to refresh
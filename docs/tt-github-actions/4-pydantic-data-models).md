---
project: tt-github-actions
github: https://github.com/tenstorrent/tt-github-actions
deepwiki: https://deepwiki.com/tenstorrent/tt-github-actions/4-pydantic-data-models)
---

# Pydantic Data Models

Relevant source files
*   [.github/actions/collect_data/src/benchmark.py](https://github.com/tenstorrent/tt-github-actions/blob/3ecf8e49/.github/actions/collect_data/src/benchmark.py)
*   [.github/actions/collect_data/src/optests.py](https://github.com/tenstorrent/tt-github-actions/blob/3ecf8e49/.github/actions/collect_data/src/optests.py)
*   [.github/actions/collect_data/src/pydantic_models.py](https://github.com/tenstorrent/tt-github-actions/blob/3ecf8e49/.github/actions/collect_data/src/pydantic_models.py)
*   [.github/actions/collect_data/src/utils.py](https://github.com/tenstorrent/tt-github-actions/blob/3ecf8e49/.github/actions/collect_data/src/utils.py)
*   [.github/actions/collect_data/test/data/14468030535/expected/benchmark.jsonl](https://github.com/tenstorrent/tt-github-actions/blob/3ecf8e49/.github/actions/collect_data/test/data/14468030535/expected/benchmark.jsonl)
*   [.github/actions/collect_data/test/data/17415392798/artifacts/test-reports-builder-n150-tracy-silicon-49443670279/report_49443670279.xml](https://github.com/tenstorrent/tt-github-actions/blob/3ecf8e49/.github/actions/collect_data/test/data/17415392798/artifacts/test-reports-builder-n150-tracy-silicon-49443670279/report_49443670279.xml)
*   [.github/actions/collect_data/test/test_optests.py](https://github.com/tenstorrent/tt-github-actions/blob/3ecf8e49/.github/actions/collect_data/test/test_optests.py)

This page provides reference documentation for the Pydantic data models used to structure CI/CD telemetry, performance benchmarks, and operation test (OpTest) results. These models are defined in [.github/actions/collect_data/src/pydantic_models.py](https://github.com/tenstorrent/tt-github-actions/blob/3ecf8e49/.github/actions/collect_data/src/pydantic_models.py) They ensure data integrity and type safety throughout the collection, transformation, and upload phases of the data pipeline.

## Model Hierarchy and Relationships

The data models are structured hierarchically to represent the nested nature of GitHub Actions (Pipelines contain Jobs, which contain Steps and Tests).

### CI/CD Entity Relationship

The following diagram illustrates how the core CI/CD models relate to one another in the "Code Entity Space."

Title: CI/CD Model Relationship

Sources: [.github/actions/collect_data/src/pydantic_models.py 15-162](https://github.com/tenstorrent/tt-github-actions/blob/3ecf8e49/.github/actions/collect_data/src/pydantic_models.py#L15-L162)

* * *

## Core CI/CD Models

### Pipeline

Represents a single execution of a workflow (e.g., a "Workflow Run"). It is the root container for all telemetry collected during a CI cycle [[.github/actions/collect_data/src/pydantic_models.py:127-133]](https://deepwiki.com/tenstorrent/tt-github-actions/4-pydantic-data-models)).

| Field | Type | Description |
| --- | --- | --- |
| `github_pipeline_id` | `Optional[int]` | Unique GitHub identifier for the workflow run [[.github/actions/collect_data/src/pydantic_models.py:135-139]](https://deepwiki.com/tenstorrent/tt-github-actions/4-pydantic-data-models)). |
| `pipeline_start_ts` | `datetime` | Time when the first job in the pipeline actually started [[.github/actions/collect_data/src/pydantic_models.py:147]](https://deepwiki.com/tenstorrent/tt-github-actions/4-pydantic-data-models)). |
| `pipeline_status` | `PipelineStatus` | Enum: `success`, `failure`, `skipped`, `cancelled`, etc. [[.github/actions/collect_data/src/pydantic_models.py:115-125]](https://deepwiki.com/tenstorrent/tt-github-actions/4-pydantic-data-models)). |
| `jobs` | `List[Job]` | Collection of all jobs executed within this pipeline [[.github/actions/collect_data/src/pydantic_models.py:171]](https://deepwiki.com/tenstorrent/tt-github-actions/4-pydantic-data-models)). |

### Job

Represents an individual job within a workflow run, typically executed on a specific runner [[.github/actions/collect_data/src/pydantic_models.py:64-71]](https://deepwiki.com/tenstorrent/tt-github-actions/4-pydantic-data-models)).

| Field | Type | Description |
| --- | --- | --- |
| `github_job_id` | `Optional[int]` | Unique GitHub identifier for the job [[.github/actions/collect_data/src/pydantic_models.py:73-76]](https://deepwiki.com/tenstorrent/tt-github-actions/4-pydantic-data-models)). |
| `job_success` | `bool` | Boolean flag indicating if the job passed [[.github/actions/collect_data/src/pydantic_models.py:85-89]](https://deepwiki.com/tenstorrent/tt-github-actions/4-pydantic-data-models)). |
| `card_type` | `Optional[str]` | Hardware card type (e.g., N150, N300) detected on the runner [[.github/actions/collect_data/src/pydantic_models.py:106]](https://deepwiki.com/tenstorrent/tt-github-actions/4-pydantic-data-models)). |
| `failure_signature` | `Optional[str]` | Extracted string identifying the cause of failure (e.g., a specific failed step) [[.github/actions/collect_data/src/pydantic_models.py:109]](https://deepwiki.com/tenstorrent/tt-github-actions/4-pydantic-data-models)). |

### Test

Contains information about individual test cases (usually Pytest) executed within a job [[.github/actions/collect_data/src/pydantic_models.py:15-21]](https://deepwiki.com/tenstorrent/tt-github-actions/4-pydantic-data-models)).

| Field | Type | Description |
| --- | --- | --- |
| `full_test_name` | `str` | The test name including parameters/config [[.github/actions/collect_data/src/pydantic_models.py:33]](https://deepwiki.com/tenstorrent/tt-github-actions/4-pydantic-data-models)). |
| `test_start_ts` | `datetime` | Timestamp when the specific test case started [[.github/actions/collect_data/src/pydantic_models.py:23]](https://deepwiki.com/tenstorrent/tt-github-actions/4-pydantic-data-models)). |
| `success` | `bool` | Whether the test case passed [[.github/actions/collect_data/src/pydantic_models.py:31]](https://deepwiki.com/tenstorrent/tt-github-actions/4-pydantic-data-models)). |

Sources: [.github/actions/collect_data/src/pydantic_models.py 15-171](https://github.com/tenstorrent/tt-github-actions/blob/3ecf8e49/.github/actions/collect_data/src/pydantic_models.py#L15-L171)

* * *

## Benchmark Models

Benchmark models are used to store performance metrics from specialized test suites like `tt-shield` or `vllm`.

Title: Benchmark Data Mapping

Sources: [.github/actions/collect_data/src/benchmark.py 20-56](https://github.com/tenstorrent/tt-github-actions/blob/3ecf8e49/.github/actions/collect_data/src/benchmark.py#L20-L56)[.github/actions/collect_data/src/pydantic_models.py 174-325](https://github.com/tenstorrent/tt-github-actions/blob/3ecf8e49/.github/actions/collect_data/src/pydantic_models.py#L174-L325)

### CompleteBenchmarkRun

The top-level model for a performance test run of a specific ML model [[.github/actions/collect_data/src/pydantic_models.py:214-220]](https://deepwiki.com/tenstorrent/tt-github-actions/4-pydantic-data-models)).

*   **Key Fields**: `ml_model_name`, `batch_size`, `input_sequence_length`, and `output_sequence_length`[[.github/actions/collect_data/src/pydantic_models.py:238-251]](https://deepwiki.com/tenstorrent/tt-github-actions/4-pydantic-data-models)).
*   **Measurements**: Contains a list of `BenchmarkMeasurement` objects [[.github/actions/collect_data/src/pydantic_models.py:261]](https://deepwiki.com/tenstorrent/tt-github-actions/4-pydantic-data-models)).

### BenchmarkMeasurement

Captures a single metric (e.g., TTFT, TPOT) for a specific iteration [[.github/actions/collect_data/src/pydantic_models.py:174-179]](https://deepwiki.com/tenstorrent/tt-github-actions/4-pydantic-data-models)).

*   **Fields**: `name` (e.g., "mean_tps"), `value` (float), and `step_name`[[.github/actions/collect_data/src/pydantic_models.py:186-192]](https://deepwiki.com/tenstorrent/tt-github-actions/4-pydantic-data-models)).

Sources: [.github/actions/collect_data/src/pydantic_models.py 174-261](https://github.com/tenstorrent/tt-github-actions/blob/3ecf8e49/.github/actions/collect_data/src/pydantic_models.py#L174-L261)[.github/actions/collect_data/src/benchmark.py 106-139](https://github.com/tenstorrent/tt-github-actions/blob/3ecf8e49/.github/actions/collect_data/src/benchmark.py#L106-L139)

* * *

## OpTest Models

These models represent low-level kernel operation tests, including tensor descriptions and hardware-specific execution details.

### OpTest

Captures the result of a single operation test, including frontend/backend details [[.github/actions/collect_data/src/pydantic_models.py:336-342]](https://deepwiki.com/tenstorrent/tt-github-actions/4-pydantic-data-models)).

*   **Hardware Metadata**: `card_type`, `arch`, and `backend` (e.g., `ttnn`, `ttmetal`) [[.github/actions/collect_data/src/pydantic_models.py:355-360]](https://deepwiki.com/tenstorrent/tt-github-actions/4-pydantic-data-models)).
*   **Failure Analysis**: `failure_stage` and `failure_description`[[.github/actions/collect_data/src/pydantic_models.py:364-365]](https://deepwiki.com/tenstorrent/tt-github-actions/4-pydantic-data-models)).

### TensorDesc

Describes the input/output tensors used in an operation test [[.github/actions/collect_data/src/pydantic_models.py:328-333]](https://deepwiki.com/tenstorrent/tt-github-actions/4-pydantic-data-models)).

*   **Fields**: `shape`, `dtype`, and `layout`[[.github/actions/collect_data/src/pydantic_models.py:331-333]](https://deepwiki.com/tenstorrent/tt-github-actions/4-pydantic-data-models)).

Sources: [.github/actions/collect_data/src/pydantic_models.py 328-368](https://github.com/tenstorrent/tt-github-actions/blob/3ecf8e49/.github/actions/collect_data/src/pydantic_models.py#L328-L368)

* * *

## Data Validation & Coercion

The models use Pydantic validators to handle inconsistencies in raw data from GitHub APIs or log files.

### coerce_optional_int

A reusable validator used in `CompleteBenchmarkRun` to ensure that string values from reports (like sequence lengths) are correctly converted to integers or set to `None` if invalid [[.github/actions/collect_data/src/pydantic_models.py:263-273]](https://deepwiki.com/tenstorrent/tt-github-actions/4-pydantic-data-models)).

`@field_validator("input_sequence_length", "output_sequence_length", mode="before")@classmethoddef coerce_optional_int(cls, v: Any) -> Optional[int]:    if v is None or v == "":        return None    try:        return int(v)    except (ValueError, TypeError):        return None`
Sources: [.github/actions/collect_data/src/pydantic_models.py 263-273](https://github.com/tenstorrent/tt-github-actions/blob/3ecf8e49/.github/actions/collect_data/src/pydantic_models.py#L263-L273)

### Status Enums

Strict enums are used for status fields to ensure downstream dashboards receive consistent categories.

*   **JobStatus**: `success`, `failure`, `skipped`, `cancelled`, `timed_out`, etc. [[.github/actions/collect_data/src/pydantic_models.py:52-62]](https://deepwiki.com/tenstorrent/tt-github-actions/4-pydantic-data-models)).
*   **PipelineStatus**: Mirrors `JobStatus` for workflow-level results [[.github/actions/collect_data/src/pydantic_models.py:115-125]](https://deepwiki.com/tenstorrent/tt-github-actions/4-pydantic-data-models)).

Sources: [.github/actions/collect_data/src/pydantic_models.py 52-125](https://github.com/tenstorrent/tt-github-actions/blob/3ecf8e49/.github/actions/collect_data/src/pydantic_models.py#L52-L125)

Dismiss
Refresh this wiki

Enter email to refresh
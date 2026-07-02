---
project: tt-github-actions
github: https://github.com/tenstorrent/tt-github-actions
deepwiki: https://deepwiki.com/tenstorrent/tt-github-actions/8-glossary
---

# Glossary

Relevant source files
*   [.github/actions/collect_data/download_workflow_data.sh](https://github.com/tenstorrent/tt-github-actions/blob/3ecf8e49/.github/actions/collect_data/download_workflow_data.sh)
*   [.github/actions/collect_data/sftp-optest.txt](https://github.com/tenstorrent/tt-github-actions/blob/3ecf8e49/.github/actions/collect_data/sftp-optest.txt)
*   [.github/actions/collect_data/src/benchmark.py](https://github.com/tenstorrent/tt-github-actions/blob/3ecf8e49/.github/actions/collect_data/src/benchmark.py)
*   [.github/actions/collect_data/src/cicd.py](https://github.com/tenstorrent/tt-github-actions/blob/3ecf8e49/.github/actions/collect_data/src/cicd.py)
*   [.github/actions/collect_data/src/optests.py](https://github.com/tenstorrent/tt-github-actions/blob/3ecf8e49/.github/actions/collect_data/src/optests.py)
*   [.github/actions/collect_data/src/parsers/python_pytest_parser.py](https://github.com/tenstorrent/tt-github-actions/blob/3ecf8e49/.github/actions/collect_data/src/parsers/python_pytest_parser.py)
*   [.github/actions/collect_data/src/parsers/python_unittest_parser.py](https://github.com/tenstorrent/tt-github-actions/blob/3ecf8e49/.github/actions/collect_data/src/parsers/python_unittest_parser.py)
*   [.github/actions/collect_data/src/parsers/tt_torch_model_tests_parser.py](https://github.com/tenstorrent/tt-github-actions/blob/3ecf8e49/.github/actions/collect_data/src/parsers/tt_torch_model_tests_parser.py)
*   [.github/actions/collect_data/src/pydantic_models.py](https://github.com/tenstorrent/tt-github-actions/blob/3ecf8e49/.github/actions/collect_data/src/pydantic_models.py)
*   [.github/actions/collect_data/src/utils.py](https://github.com/tenstorrent/tt-github-actions/blob/3ecf8e49/.github/actions/collect_data/src/utils.py)
*   [.github/actions/collect_data/test/data/14468030535/expected/benchmark.jsonl](https://github.com/tenstorrent/tt-github-actions/blob/3ecf8e49/.github/actions/collect_data/test/data/14468030535/expected/benchmark.jsonl)
*   [.github/actions/collect_data/test/data/17415392798/artifacts/test-reports-builder-n150-tracy-silicon-49443670279/report_49443670279.xml](https://github.com/tenstorrent/tt-github-actions/blob/3ecf8e49/.github/actions/collect_data/test/data/17415392798/artifacts/test-reports-builder-n150-tracy-silicon-49443670279/report_49443670279.xml)
*   [.github/actions/collect_data/test/test_optests.py](https://github.com/tenstorrent/tt-github-actions/blob/3ecf8e49/.github/actions/collect_data/test/test_optests.py)
*   [.github/actions/collect_data/test/test_tt_torch_model_tests_parser.py](https://github.com/tenstorrent/tt-github-actions/blob/3ecf8e49/.github/actions/collect_data/test/test_tt_torch_model_tests_parser.py)
*   [.github/actions/job_id/Readme.md](https://github.com/tenstorrent/tt-github-actions/blob/3ecf8e49/.github/actions/job_id/Readme.md?plain=1)
*   [.github/actions/job_id/action.yml](https://github.com/tenstorrent/tt-github-actions/blob/3ecf8e49/.github/actions/job_id/action.yml)
*   [.github/actions/spdx-checker/README.md](https://github.com/tenstorrent/tt-github-actions/blob/3ecf8e49/.github/actions/spdx-checker/README.md?plain=1)
*   [.github/actions/spdx-checker/action.yml](https://github.com/tenstorrent/tt-github-actions/blob/3ecf8e49/.github/actions/spdx-checker/action.yml)
*   [.github/actions/spdx-checker/check_copyright_config.yaml](https://github.com/tenstorrent/tt-github-actions/blob/3ecf8e49/.github/actions/spdx-checker/check_copyright_config.yaml)
*   [.github/actions/spdx-checker/example-workflow.yml](https://github.com/tenstorrent/tt-github-actions/blob/3ecf8e49/.github/actions/spdx-checker/example-workflow.yml)
*   [.github/actions/spdx-checker/merge_config.py](https://github.com/tenstorrent/tt-github-actions/blob/3ecf8e49/.github/actions/spdx-checker/merge_config.py)
*   [.github/workflows/bounty-pr.yml](https://github.com/tenstorrent/tt-github-actions/blob/3ecf8e49/.github/workflows/bounty-pr.yml)
*   [.github/workflows/on-community-issue.yml](https://github.com/tenstorrent/tt-github-actions/blob/3ecf8e49/.github/workflows/on-community-issue.yml)
*   [.github/workflows/test_job_id.yml](https://github.com/tenstorrent/tt-github-actions/blob/3ecf8e49/.github/workflows/test_job_id.yml)

This glossary defines codebase-specific terms, abbreviations, and domain concepts used throughout the `tt-github-actions` repository. It serves as a technical reference for the data models, processing pipelines, and automation logic.

## 1. Core Concepts & Pipeline Terms

### Pipeline

In the context of this codebase, a **Pipeline** refers to a single execution of a GitHub Actions workflow. It is the top-level entity in the data model and contains multiple **Jobs**.

*   **Implementation**: Represented by the `Pipeline` Pydantic model [.github/actions/collect_data/src/pydantic_models.py 127-162](https://github.com/tenstorrent/tt-github-actions/blob/3ecf8e49/%20.github/actions/collect_data/src/pydantic_models.py#L127-L162)
*   **Data Flow**: Raw JSON from the GitHub API is transformed into a `Pipeline` object by `get_pipeline_row_from_github_info`[.github/actions/collect_data/src/utils.py 94-155](https://github.com/tenstorrent/tt-github-actions/blob/3ecf8e49/%20.github/actions/collect_data/src/utils.py#L94-L155)

### Job

A **Job** is a set of steps executed on a specific runner (host) within a Pipeline.

*   **Implementation**: Represented by the `Job` Pydantic model [.github/actions/collect_data/src/pydantic_models.py 64-113](https://github.com/tenstorrent/tt-github-actions/blob/3ecf8e49/%20.github/actions/collect_data/src/pydantic_models.py#L64-L113)
*   **Correlation**: Jobs are mapped to test reports using the `github_job_id` extracted from filenames [.github/actions/collect_data/src/cicd.py 116-122](https://github.com/tenstorrent/tt-github-actions/blob/3ecf8e49/%20.github/actions/collect_data/src/cicd.py#L116-L122)

### Test

A **Test** represents an individual unit of execution (e.g., a pytest function) within a Job.

*   **Implementation**: Represented by the `Test` Pydantic model [.github/actions/collect_data/src/pydantic_models.py 15-36](https://github.com/tenstorrent/tt-github-actions/blob/3ecf8e49/%20.github/actions/collect_data/src/pydantic_models.py#L15-L36)
*   **Parsing**: Parsed from XML or JSON reports by specialized classes inheriting from the `Parser` base class [.github/actions/collect_data/src/parsers/python_pytest_parser.py 19-37](https://github.com/tenstorrent/tt-github-actions/blob/3ecf8e49/%20.github/actions/collect_data/src/parsers/python_pytest_parser.py#L19-L37)

### Failure Signature

A concise string used to categorize the reason for a Job's failure. It often includes a "triage tag" if specific patterns (like device timeouts) are found in logs.

*   **Logic**: Extracted in `get_job_failure_signature`[.github/actions/collect_data/src/utils.py 158-176](https://github.com/tenstorrent/tt-github-actions/blob/3ecf8e49/%20.github/actions/collect_data/src/utils.py#L158-L176)
*   **Triage Tag**: `[tt-triage]` is appended if "device timeout" or "potential hang" is detected [.github/actions/collect_data/src/utils.py 166-174](https://github.com/tenstorrent/tt-github-actions/blob/3ecf8e49/%20.github/actions/collect_data/src/utils.py#L166-L174)

* * *

## 2. Telemetry & Benchmarking Terms

### Benchmark Measurement

A specific metric (e.g., TTFT, TPOT) recorded during a performance run.

*   **Implementation**: `BenchmarkMeasurement` model [.github/actions/collect_data/src/pydantic_models.py 10](https://github.com/tenstorrent/tt-github-actions/blob/3ecf8e49/%20.github/actions/collect_data/src/pydantic_models.py#L10-L10)
*   **Standard Metrics**: Common keys include `mean_ttft_ms` (Time to First Token) and `mean_tps` (Tokens Per Second) [.github/actions/collect_data/test/data/14468030535/expected/benchmark.jsonl 1](https://github.com/tenstorrent/tt-github-actions/blob/3ecf8e49/%20.github/actions/collect_data/test/data/14468030535/expected/benchmark.jsonl#L1-L1)

### Model Spec

A JSON file containing metadata about the machine learning model being benchmarked (e.g., number of layers, configuration parameters).

*   **Usage**: Loaded by `_load_model_spec_json` to enrich benchmark reports [.github/actions/collect_data/src/benchmark.py 20-32](https://github.com/tenstorrent/tt-github-actions/blob/3ecf8e49/%20.github/actions/collect_data/src/benchmark.py#L20-L32)

* * *

## 3. System Architecture Diagrams

### Data Entity Mapping

This diagram bridges the natural language concepts to the specific Pydantic models and utility functions used to instantiate them.

**Sources**: [.github/actions/collect_data/src/cicd.py 49-84](https://github.com/tenstorrent/tt-github-actions/blob/3ecf8e49/%20.github/actions/collect_data/src/cicd.py#L49-L84)[.github/actions/collect_data/src/pydantic_models.py 15-162](https://github.com/tenstorrent/tt-github-actions/blob/3ecf8e49/%20.github/actions/collect_data/src/pydantic_models.py#L15-L162)

### Parser Selection Logic

The `optests.py` module uses specific logic to route test reports to the correct parser based on file extensions and branch names.

**Sources**: [.github/actions/collect_data/src/optests.py 14-49](https://github.com/tenstorrent/tt-github-actions/blob/3ecf8e49/%20.github/actions/collect_data/src/optests.py#L14-L49)[.github/actions/collect_data/src/optests.py 76-84](https://github.com/tenstorrent/tt-github-actions/blob/3ecf8e49/%20.github/actions/collect_data/src/optests.py#L76-L84)

* * *

## 4. Technical Abbreviations

| Abbreviation | Full Term | Context | Code Reference |
| --- | --- | --- | --- |
| **TTFT** | Time to First Token | Benchmark metric for LLMs. | [.github/actions/collect_data/test/data/14468030535/expected/benchmark.jsonl 1](https://github.com/tenstorrent/tt-github-actions/blob/3ecf8e49/%20.github/actions/collect_data/test/data/14468030535/expected/benchmark.jsonl#L1-L1) |
| **TPOT** | Time Per Output Token | Benchmark metric for LLMs. | [.github/actions/collect_data/test/data/14468030535/expected/benchmark.jsonl 1](https://github.com/tenstorrent/tt-github-actions/blob/3ecf8e49/%20.github/actions/collect_data/test/data/14468030535/expected/benchmark.jsonl#L1-L1) |
| **TPS** | Tokens Per Second | Throughput metric. | [.github/actions/collect_data/test/data/14468030535/expected/benchmark.jsonl 1](https://github.com/tenstorrent/tt-github-actions/blob/3ecf8e49/%20.github/actions/collect_data/test/data/14468030535/expected/benchmark.jsonl#L1-L1) |
| **E2EL** | End-to-End Latency | Total execution time. | [.github/actions/collect_data/test/data/14468030535/expected/benchmark.jsonl 1](https://github.com/tenstorrent/tt-github-actions/blob/3ecf8e49/%20.github/actions/collect_data/test/data/14468030535/expected/benchmark.jsonl#L1-L1) |
| **SPDX** | Software Package Data Exchange | License header standard. | [.github/actions/spdx-checker/action.yml 1](https://github.com/tenstorrent/tt-github-actions/blob/3ecf8e49/%20.github/actions/spdx-checker/action.yml#L1-L1) |
| **PAT** | Personal Access Token | GitHub authentication. | [.github/workflows/on-community-issue.yml 43](https://github.com/tenstorrent/tt-github-actions/blob/3ecf8e49/%20.github/workflows/on-community-issue.yml#L43-L43) |

* * *

## 5. Domain Concepts

### Builder Jobs

Specific CI jobs (identified by the string `builder` in the job name) that execute comprehensive operation tests (OpTests) and generate detailed XML reports containing hardware-specific metadata like `card_type` and `backend`.

*   **Gating**: `BuilderPytestParser` is only active on the `main` branch [.github/actions/collect_data/src/optests.py 33-35](https://github.com/tenstorrent/tt-github-actions/blob/3ecf8e49/%20.github/actions/collect_data/src/optests.py#L33-L35)

### Community Member Detection

The process of distinguishing between Tenstorrent organization members and external contributors.

*   **Mechanism**: A `curl` request to the GitHub Org Members API. An HTTP `204` status indicates membership [.github/workflows/on-community-issue.yml 45-54](https://github.com/tenstorrent/tt-github-actions/blob/3ecf8e49/%20.github/workflows/on-community-issue.yml#L45-L54)

### Job ID Retrieval

The process of fetching the internal numeric GitHub Job ID using the human-readable job name. This is necessary because the `$GITHUB_JOB` environment variable only provides the name, not the ID required for API calls.

*   **Implementation**: A bash loop in `action.yml` that paginates through the `/jobs` endpoint and filters using `jq`[.github/actions/job_id/action.yml 30-52](https://github.com/tenstorrent/tt-github-actions/blob/3ecf8e49/%20.github/actions/job_id/action.yml#L30-L52)

**Sources**:

*   Pipeline/Job/Test models: [.github/actions/collect_data/src/pydantic_models.py 1-162](https://github.com/tenstorrent/tt-github-actions/blob/3ecf8e49/%20.github/actions/collect_data/src/pydantic_models.py#L1-L162)
*   Failure Signatures: [.github/actions/collect_data/src/utils.py 158-211](https://github.com/tenstorrent/tt-github-actions/blob/3ecf8e49/%20.github/actions/collect_data/src/utils.py#L158-L211)
*   Benchmark metrics: [.github/actions/collect_data/test/data/14468030535/expected/benchmark.jsonl 1-2](https://github.com/tenstorrent/tt-github-actions/blob/3ecf8e49/%20.github/actions/collect_data/test/data/14468030535/expected/benchmark.jsonl#L1-L2)
*   Parser selection: [.github/actions/collect_data/src/optests.py 1-91](https://github.com/tenstorrent/tt-github-actions/blob/3ecf8e49/%20.github/actions/collect_data/src/optests.py#L1-L91)
*   Community logic: [.github/workflows/on-community-issue.yml 1-82](https://github.com/tenstorrent/tt-github-actions/blob/3ecf8e49/%20.github/workflows/on-community-issue.yml#L1-L82)
*   Job ID fetching: [.github/actions/job_id/action.yml 1-60](https://github.com/tenstorrent/tt-github-actions/blob/3ecf8e49/%20.github/actions/job_id/action.yml#L1-L60)

Dismiss
Refresh this wiki

Enter email to refresh
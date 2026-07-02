---
project: tt-github-actions
github: https://github.com/tenstorrent/tt-github-actions
deepwiki: https://deepwiki.com/tenstorrent/tt-github-actions/2-collect_data-action
---

# collect_data Action

Relevant source files
*   [.github/actions/collect_data/action.yml](https://github.com/tenstorrent/tt-github-actions/blob/3ecf8e49/.github/actions/collect_data/action.yml)
*   [.github/actions/collect_data/download_workflow_data.sh](https://github.com/tenstorrent/tt-github-actions/blob/3ecf8e49/.github/actions/collect_data/download_workflow_data.sh)
*   [.github/actions/collect_data/src/generate_data.py](https://github.com/tenstorrent/tt-github-actions/blob/3ecf8e49/.github/actions/collect_data/src/generate_data.py)
*   [.github/workflows/on-community-issue.yml](https://github.com/tenstorrent/tt-github-actions/blob/3ecf8e49/.github/workflows/on-community-issue.yml)

The `collect_data` action is a central composite action designed to aggregate telemetry and test results from GitHub workflow runs. It serves as the primary data ingestion engine for Tenstorrent's CI/CD analytics, performance benchmarking, and operation testing (OpTest) pipelines. By processing raw GitHub API metadata and workflow artifacts, it produces structured JSON/JSONL reports that are subsequently uploaded to an SFTP server for long-term storage and analysis.

### Purpose and Scope

The action automates the transition from raw execution logs and XML test reports to validated Pydantic data models. It handles three distinct data streams:

1.   **CI/CD Telemetry**: Metadata about workflow runs, job statuses, durations, and runner environments.
2.   **Performance Benchmarks**: Throughput and latency metrics (TTFT, TPOT, etc.) from various model inference engines.
3.   **OpTests**: Detailed results of ML kernel operation tests, including hardware-specific failure stages.

### High-Level Workflow Architecture

The following diagram illustrates how the `collect_data` action orchestrates data acquisition, transformation, and delivery.

**Data Collection and Upload Flow**

**Sources:**[.github/actions/collect_data/action.yml 37-95](https://github.com/tenstorrent/tt-github-actions/blob/3ecf8e49/.github/actions/collect_data/action.yml#L37-L95)[.github/actions/collect_data/src/generate_data.py 16-65](https://github.com/tenstorrent/tt-github-actions/blob/3ecf8e49/.github/actions/collect_data/src/generate_data.py#L16-L65)

* * *

### Data Pipelines

The `collect_data` action delegates specialized processing to three sub-pipelines. Each pipeline is responsible for parsing specific artifact types and mapping them to the system's [Pydantic Data Models](https://deepwiki.com/tenstorrent/tt-github-actions/4-pydantic-data-models).

#### 1. CI/CD Pipeline

This pipeline captures the "heartbeat" of the CI system. It uses `cicd.py` to transform raw JSON from the GitHub REST API into a structured `Pipeline` model. It extracts information such as the runner environment, job dependencies, and execution timestamps.

*   **Key Logic:**`create_cicd_json_for_data_analysis`
*   **For details, see [Action Interface & Data Acquisition](https://deepwiki.com/tenstorrent/tt-github-actions/2.1-action-interface-and-data-acquisition) and [CICD Pipeline Data Processing](https://deepwiki.com/tenstorrent/tt-github-actions/2.2-cicd-pipeline-data-processing).**

#### 2. Benchmark Pipeline

The benchmark pipeline processes performance reports from engines like `tt-forge`, `tt-shield`, and `vllm`. It normalizes disparate metric formats into a unified `CompleteBenchmarkRun` format using a mapper pattern.

*   **Key Logic:**`create_json_from_report`
*   **For details, see [Benchmark Data Processing](https://deepwiki.com/tenstorrent/tt-github-actions/2.3-benchmark-data-processing).**

#### 3. OpTest Pipeline

This pipeline focuses on low-level kernel validation. It parses JUnit XML results from operation tests, often involving complex hardware metadata (e.g., Grayskull vs. Wormhole) and failure stage categorization.

*   **Key Logic:**`create_optest_reports`
*   **For details, see [OpTest Data Processing](https://deepwiki.com/tenstorrent/tt-github-actions/2.4-optest-data-processing).**

* * *

### System Entity Mapping

The following diagram bridges the high-level functional stages to the specific code entities and scripts that implement them.

**Code Entity Association Map**

**Sources:**[.github/actions/collect_data/download_workflow_data.sh 25-61](https://github.com/tenstorrent/tt-github-actions/blob/3ecf8e49/.github/actions/collect_data/download_workflow_data.sh#L25-L61)[.github/actions/collect_data/src/generate_data.py 1-13](https://github.com/tenstorrent/tt-github-actions/blob/3ecf8e49/.github/actions/collect_data/src/generate_data.py#L1-L13)

### Action Interface Summary

The action is invoked as a composite step within a workflow. It requires access to the repository name and the run ID of the pipeline being analyzed.

| Input | Description | Required |
| --- | --- | --- |
| `repository` | The owner/repo string for API calls. | Yes |
| `run_id` | The GitHub Workflow Run ID to process. | Yes |
| `run_attempt` | The specific attempt number (for retries). | Yes |
| `sftp_host` | Target for CI/CD telemetry. | Optional |
| `sftp_perf_host` | Target for benchmark results. | Optional |
| `sftp_optest_host` | Target for operation test results. | Optional |

**Sources:**[.github/actions/collect_data/action.yml 5-35](https://github.com/tenstorrent/tt-github-actions/blob/3ecf8e49/.github/actions/collect_data/action.yml#L5-L35)

Dismiss
Refresh this wiki

Enter email to refresh
---
project: tt-github-actions
github: https://github.com/tenstorrent/tt-github-actions
deepwiki: https://deepwiki.com/tenstorrent/tt-github-actions/5-utility-actions)
---

# Utility Actions

Relevant source files
*   [.github/actions/job_id/Readme.md](https://github.com/tenstorrent/tt-github-actions/blob/3ecf8e49/.github/actions/job_id/Readme.md?plain=1)
*   [.github/actions/job_id/action.yml](https://github.com/tenstorrent/tt-github-actions/blob/3ecf8e49/.github/actions/job_id/action.yml)
*   [.github/actions/maximize_space/Readme.md](https://github.com/tenstorrent/tt-github-actions/blob/3ecf8e49/.github/actions/maximize_space/Readme.md?plain=1)
*   [.github/actions/maximize_space/action.yml](https://github.com/tenstorrent/tt-github-actions/blob/3ecf8e49/.github/actions/maximize_space/action.yml)
*   [.github/actions/show_telemtery/action.yml](https://github.com/tenstorrent/tt-github-actions/blob/3ecf8e49/.github/actions/show_telemtery/action.yml)
*   [.github/actions/show_telemtery/process_telemetry.py](https://github.com/tenstorrent/tt-github-actions/blob/3ecf8e49/.github/actions/show_telemtery/process_telemetry.py)
*   [.github/actions/spdx-checker/README.md](https://github.com/tenstorrent/tt-github-actions/blob/3ecf8e49/.github/actions/spdx-checker/README.md?plain=1)
*   [.github/actions/spdx-checker/action.yml](https://github.com/tenstorrent/tt-github-actions/blob/3ecf8e49/.github/actions/spdx-checker/action.yml)
*   [.github/actions/spdx-checker/check_copyright_config.yaml](https://github.com/tenstorrent/tt-github-actions/blob/3ecf8e49/.github/actions/spdx-checker/check_copyright_config.yaml)
*   [.github/actions/spdx-checker/example-workflow.yml](https://github.com/tenstorrent/tt-github-actions/blob/3ecf8e49/.github/actions/spdx-checker/example-workflow.yml)
*   [.github/actions/spdx-checker/merge_config.py](https://github.com/tenstorrent/tt-github-actions/blob/3ecf8e49/.github/actions/spdx-checker/merge_config.py)
*   [.github/workflows/test_job_id.yml](https://github.com/tenstorrent/tt-github-actions/blob/3ecf8e49/.github/workflows/test_job_id.yml)
*   [.github/workflows/test_maximize_space.yml](https://github.com/tenstorrent/tt-github-actions/blob/3ecf8e49/.github/workflows/test_maximize_space.yml)

This section provides an overview of the standalone utility actions available in the `tt-github-actions` repository. Unlike the primary data collection pipeline, these actions perform specific, localized tasks such as retrieving metadata, optimizing runner resources, monitoring system health, or enforcing licensing standards.

### Overview of Utility Actions

The utility suite consists of four primary composite actions designed to be integrated into various stages of a GitHub CI/CD workflow.

| Action | Purpose | Key Files |
| --- | --- | --- |
| `job_id` | Retrieves the numeric GitHub Job ID via REST API. | `.github/actions/job_id/action.yml` |
| `maximize_space` | Reclaims ~6-7GB of disk space on GitHub-hosted runners. | `.github/actions/maximize_space/action.yml` |
| `show_telemetry` | Collects and visualizes system resource metrics (`/proc`). | `.github/actions/show_telemetry/action.yml` |
| `spdx-checker` | Validates SPDX license headers using `check-copyright`. | `.github/actions/spdx-checker/action.yml` |

* * *

### job_id Action

The `job_id` action solves the limitation where the GitHub Actions context (`github` object) does not natively provide the numeric ID of the currently executing job. This ID is often required for linking telemetry data or artifacts back to a specific job instance in external databases.

It uses a `bash` loop to query the GitHub REST API, implementing pagination to handle workflows with a large number of jobs [.github/actions/job_id/action.yml 34-52](https://github.com/tenstorrent/tt-github-actions/blob/3ecf8e49/.github/actions/job_id/action.yml#L34-L52) It filters the API response using `jq` to match the provided `job_name` input [.github/actions/job_id/action.yml 38](https://github.com/tenstorrent/tt-github-actions/blob/3ecf8e49/.github/actions/job_id/action.yml#L38-L38)

For details, see [job_id Action](https://deepwiki.com/tenstorrent/tt-github-actions/5.1-job_id-action).

**Sources:**

*   [.github/actions/job_id/action.yml 1-60](https://github.com/tenstorrent/tt-github-actions/blob/3ecf8e49/.github/actions/job_id/action.yml#L1-L60)
*   [.github/actions/job_id/Readme.md 1-21](https://github.com/tenstorrent/tt-github-actions/blob/3ecf8e49/.github/actions/job_id/Readme.md?plain=1#L1-L21)

* * *

### maximize_space Action

The `maximize_space` action is a resource optimization utility specifically targeted at GitHub-hosted runners. It reclaims disk space by removing pre-installed software packages that are typically unnecessary for Tenstorrent build environments (e.g., Android SDKs, CodeQL, Go, and various language runtimes).

The action targets specific directories such as `/__t` and `/opt/hostedtoolcache`[.github/actions/maximize_space/action.yml 21-37](https://github.com/tenstorrent/tt-github-actions/blob/3ecf8e49/.github/actions/maximize_space/action.yml#L21-L37) By purging these locations, the action can increase available space from approximately 8.7GB to 16GB [.github/actions/maximize_space/Readme.md 8-31](https://github.com/tenstorrent/tt-github-actions/blob/3ecf8e49/.github/actions/maximize_space/Readme.md?plain=1#L8-L31)

For details, see [maximize_space Action](https://deepwiki.com/tenstorrent/tt-github-actions/5.2-maximize_space-action).

**Sources:**

*   [.github/actions/maximize_space/action.yml 1-41](https://github.com/tenstorrent/tt-github-actions/blob/3ecf8e49/.github/actions/maximize_space/action.yml#L1-L41)
*   [.github/actions/maximize_space/Readme.md 1-39](https://github.com/tenstorrent/tt-github-actions/blob/3ecf8e49/.github/actions/maximize_space/Readme.md?plain=1#L1-L39)
*   [.github/workflows/test_maximize_space.yml 1-37](https://github.com/tenstorrent/tt-github-actions/blob/3ecf8e49/.github/workflows/test_maximize_space.yml#L1-L37)

* * *

### show_telemetry Action

The `show_telemetry` action provides visibility into the hardware utilization of a runner during a job's execution. It operates using a pre/post hook pattern:

1.   **Start**: Launches `collect_telemetry.py` as a background process (`nohup`) [.github/actions/show_telemetry/action.yml 46-49](https://github.com/tenstorrent/tt-github-actions/blob/3ecf8e49/.github/actions/show_telemetry/action.yml#L46-L49)
2.   **Collect**: Periodically samples `/proc` for CPU load, memory usage, and process-level statistics.
3.   **Finish**: Terminates the collector and runs `process_telemetry.py` to generate Markdown charts for the `$GITHUB_STEP_SUMMARY`[.github/actions/show_telemetry/action.yml 67-73](https://github.com/tenstorrent/tt-github-actions/blob/3ecf8e49/.github/actions/show_telemetry/action.yml#L67-L73)

#### Telemetry Component Architecture

For details, see [show_telemtery Action](https://deepwiki.com/tenstorrent/tt-github-actions/5.3-show_telemtery-action).

**Sources:**

*   [.github/actions/show_telemetry/action.yml 1-74](https://github.com/tenstorrent/tt-github-actions/blob/3ecf8e49/.github/actions/show_telemetry/action.yml#L1-L74)
*   [.github/actions/show_telemetry/process_telemetry.py 20-186](https://github.com/tenstorrent/tt-github-actions/blob/3ecf8e49/.github/actions/show_telemetry/process_telemetry.py#L20-L186)

* * *

### spdx-checker Action

The `spdx-checker` action ensures that all source files in a repository contain valid SPDX license headers. This is critical for maintaining open-source compliance across Tenstorrent projects. It leverages the Espressif `check-copyright` tool [.github/actions/spdx-checker/action.yml 39](https://github.com/tenstorrent/tt-github-actions/blob/3ecf8e49/.github/actions/spdx-checker/action.yml#L39-L39)

The action uses a Python script, `merge_config.py`, to combine a mandatory `DEFAULT_CONFIG` (containing approved licenses like Apache-2.0 and MIT) with repository-specific `ignore` patterns [.github/actions/spdx-checker/merge_config.py 18-37](https://github.com/tenstorrent/tt-github-actions/blob/3ecf8e49/.github/actions/spdx-checker/merge_config.py#L18-L37) This prevents individual repositories from overriding the company-wide license standards while allowing them to skip third-party or generated directories [.github/actions/spdx-checker/README.md 37-38](https://github.com/tenstorrent/tt-github-actions/blob/3ecf8e49/.github/actions/spdx-checker/README.md?plain=1#L37-L38)

#### Configuration Merging Logic

For details, see [spdx-checker Action](https://deepwiki.com/tenstorrent/tt-github-actions/5.4-spdx-checker-action).

**Sources:**

*   [.github/actions/spdx-checker/action.yml 1-74](https://github.com/tenstorrent/tt-github-actions/blob/3ecf8e49/.github/actions/spdx-checker/action.yml#L1-L74)
*   [.github/actions/spdx-checker/merge_config.py 1-78](https://github.com/tenstorrent/tt-github-actions/blob/3ecf8e49/.github/actions/spdx-checker/merge_config.py#L1-L78)
*   [.github/actions/spdx-checker/README.md 1-134](https://github.com/tenstorrent/tt-github-actions/blob/3ecf8e49/.github/actions/spdx-checker/README.md?plain=1#L1-L134)

Dismiss
Refresh this wiki

Enter email to refresh
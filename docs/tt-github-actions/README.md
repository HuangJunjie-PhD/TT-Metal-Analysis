---
project: tt-github-actions
github: https://github.com/tenstorrent/tt-github-actions
deepwiki: https://deepwiki.com/tenstorrent/tt-github-actions
---

# Overview

Relevant source files
*   [.github/CODEOWNERS](https://github.com/tenstorrent/tt-github-actions/blob/3ecf8e49/.github/CODEOWNERS)
*   [.github/actions/collect_data/Readme.md](https://github.com/tenstorrent/tt-github-actions/blob/3ecf8e49/.github/actions/collect_data/Readme.md?plain=1)
*   [.github/actions/collect_data/pytest.ini](https://github.com/tenstorrent/tt-github-actions/blob/3ecf8e49/.github/actions/collect_data/pytest.ini)
*   [.gitignore](https://github.com/tenstorrent/tt-github-actions/blob/3ecf8e49/.gitignore)
*   [LICENSE](https://github.com/tenstorrent/tt-github-actions/blob/3ecf8e49/LICENSE)
*   [Readme.md](https://github.com/tenstorrent/tt-github-actions/blob/3ecf8e49/Readme.md?plain=1)

The `tt-github-actions` repository serves as a centralized library of shared GitHub Actions and workflows designed for use across Tenstorrent projects. Its primary goal is to standardize CI/CD telemetry collection, optimize runner environments, and automate community management tasks.

By consolidating these tools, Tenstorrent ensures consistent data reporting for analytics and reduces maintenance overhead for individual repositories.

## Core Capabilities

The repository is structured around several key functional areas:

*   **Telemetry & Data Analytics**: Collecting detailed logs, test reports, and performance benchmarks from CI/CD pipelines to upload them to a central SFTP server for analysis.
*   **Runner Optimization**: Tools to maximize available disk space and monitor system resource usage (CPU, Memory, Disk) during workflow execution.
*   **Workflow Utilities**: Small, reusable actions for retrieving metadata like numeric Job IDs or enforcing license compliance via SPDX headers.
*   **Community Automation**: Automated labeling and management of issues and pull requests from external contributors.

For a detailed look at the project layout and how to integrate these actions into your own repository, see **[Getting Started & Repository Structure](https://deepwiki.com/tenstorrent/tt-github-actions/1.1-getting-started-and-repository-structure)**.

### System Architecture: Data Flow

The following diagram illustrates how the primary `collect_data` action interacts with GitHub's infrastructure and Tenstorrent's internal analytics storage.

**CI/CD Telemetry Collection Pipeline**

Sources: [.github/actions/collect_data/Readme.md 41-51](https://github.com/tenstorrent/tt-github-actions/blob/3ecf8e49/.github/actions/collect_data/Readme.md?plain=1#L41-L51)[.github/actions/collect_data/Readme.md 11-37](https://github.com/tenstorrent/tt-github-actions/blob/3ecf8e49/.github/actions/collect_data/Readme.md?plain=1#L11-L37)

## Primary Actions

| Action | Purpose | Key Files |
| --- | --- | --- |
| **`collect_data`** | Aggregates CI/CD results, benchmarks, and OpTests. | [.github/actions/collect_data/action.yml](https://github.com/tenstorrent/tt-github-actions/blob/3ecf8e49/.github/actions/collect_data/action.yml)[.github/actions/collect_data/src/generate_data.py](https://github.com/tenstorrent/tt-github-actions/blob/3ecf8e49/.github/actions/collect_data/src/generate_data.py) |
| **`job_id`** | Retrieves the numeric ID of the current job for API interactions. | [.github/actions/job_id/action.yml](https://github.com/tenstorrent/tt-github-actions/blob/3ecf8e49/.github/actions/job_id/action.yml) |
| **`maximize_space`** | Cleans up runner environments to reclaim ~6-7GB of disk space. | [.github/actions/maximize_space/action.yml](https://github.com/tenstorrent/tt-github-actions/blob/3ecf8e49/.github/actions/maximize_space/action.yml) |
| **`show_telemetry`** | Captures and visualizes runner resource usage (CPU/RAM). | [.github/actions/show_telemetry/action.yml](https://github.com/tenstorrent/tt-github-actions/blob/3ecf8e49/.github/actions/show_telemetry/action.yml) |
| **`spdx-checker`** | Validates Apache 2.0/SPDX license headers in source files. | [.github/actions/spdx-checker/action.yml](https://github.com/tenstorrent/tt-github-actions/blob/3ecf8e49/.github/actions/spdx-checker/action.yml) |

For a complete list of top-level workflows and their triggers, see the **[CI/CD Workflow Catalog](https://deepwiki.com/tenstorrent/tt-github-actions/1.2-cicd-workflow-catalog)**.

## Code Entity Map

This map associates high-level system functions with the specific code entities that implement them.

**Telemetry Implementation Map**

Sources: [.github/actions/collect_data/Readme.md 41-51](https://github.com/tenstorrent/tt-github-actions/blob/3ecf8e49/.github/actions/collect_data/Readme.md?plain=1#L41-L51)[.github/actions/collect_data/pytest.ini 1-3](https://github.com/tenstorrent/tt-github-actions/blob/3ecf8e49/.github/actions/collect_data/pytest.ini#L1-L3)

## Integration Summary

Most actions in this repository are designed to be used as steps within a job. For example, the `collect_data` action is typically triggered by a `workflow_run` event to process data after a primary CI pipeline completes.

`# Example usage snippet- name: Collect CI/CD data  uses: tenstorrent/tt-github-actions/.github/actions/collect_data@main  with:    repository: ${{ github.repository }}    run_id: ${{ github.event.workflow_run.id }}`
Sources: [.github/actions/collect_data/Readme.md 28-33](https://github.com/tenstorrent/tt-github-actions/blob/3ecf8e49/.github/actions/collect_data/Readme.md?plain=1#L28-L33)

## Maintainers

The repository is maintained by the `@tenstorrent/shared-common-maintainer` team. Sources: [.github/CODEOWNERS 1-2](https://github.com/tenstorrent/tt-github-actions/blob/3ecf8e49/.github/CODEOWNERS#L1-L2)

Dismiss
Refresh this wiki

Enter email to refresh
---
project: pytorch2.0_ttnn
github: https://github.com/tenstorrent/pytorch2.0_ttnn
deepwiki: https://deepwiki.com/tenstorrent/pytorch2.0_ttnn/9-cicd-and-release-management)
---

# CI/CD and Release Management

Relevant source files
*   [.github/actions/common_repo_setup/action.yaml](https://github.com/tenstorrent/pytorch2.0_ttnn/blob/7ba2867e/.github/actions/common_repo_setup/action.yaml)
*   [.github/actions/common_repo_setup/dependencies.json](https://github.com/tenstorrent/pytorch2.0_ttnn/blob/7ba2867e/.github/actions/common_repo_setup/dependencies.json)
*   [.github/workflows/before_merge.yaml](https://github.com/tenstorrent/pytorch2.0_ttnn/blob/7ba2867e/.github/workflows/before_merge.yaml)
*   [.github/workflows/community-issue-tagging.yml](https://github.com/tenstorrent/pytorch2.0_ttnn/blob/7ba2867e/.github/workflows/community-issue-tagging.yml)
*   [.github/workflows/run-accuracy-tests.yaml](https://github.com/tenstorrent/pytorch2.0_ttnn/blob/7ba2867e/.github/workflows/run-accuracy-tests.yaml)
*   [.github/workflows/run-tests.yaml](https://github.com/tenstorrent/pytorch2.0_ttnn/blob/7ba2867e/.github/workflows/run-tests.yaml)
*   [.github/workflows/update-docker-container.yaml](https://github.com/tenstorrent/pytorch2.0_ttnn/blob/7ba2867e/.github/workflows/update-docker-container.yaml)
*   [.github/workflows/update-readme.yaml](https://github.com/tenstorrent/pytorch2.0_ttnn/blob/7ba2867e/.github/workflows/update-readme.yaml)
*   [.github/workflows/update-ttnn-wheel.yaml](https://github.com/tenstorrent/pytorch2.0_ttnn/blob/7ba2867e/.github/workflows/update-ttnn-wheel.yaml)
*   [dockerfile/Dockerfile](https://github.com/tenstorrent/pytorch2.0_ttnn/blob/7ba2867e/dockerfile/Dockerfile)
*   [requirements.txt](https://github.com/tenstorrent/pytorch2.0_ttnn/blob/7ba2867e/requirements.txt)
*   [torch_ttnn/passes/analysis/multi_device_shard_analysis_pass.py](https://github.com/tenstorrent/pytorch2.0_ttnn/blob/7ba2867e/torch_ttnn/passes/analysis/multi_device_shard_analysis_pass.py)

This document describes the CI/CD infrastructure that automates testing, dependency updates, and release processes for the pytorch2.0_ttnn repository. It covers GitHub Actions workflows, Docker container management, test parallelization strategies, and automated dependency updates.

For information about the testing framework itself, see [Testing Framework and Infrastructure](https://deepwiki.com/tenstorrent/pytorch2.0_ttnn/4-testing-framework-and-infrastructure). For details on metrics collection and reporting, see [Metrics, Reporting, and Feedback Loops](https://deepwiki.com/tenstorrent/pytorch2.0_ttnn/7-metrics-reporting-and-feedback-loops).

* * *

## Workflow Architecture Overview

The CI/CD system is built around several interconnected GitHub Actions workflows that handle different aspects of the development lifecycle. The primary workflow is `run-tests.yaml`, which serves as the main test orchestrator and is invoked by other workflows.

### Workflow Relationships

**Sources:**[.github/workflows/before_merge.yaml 1-27](https://github.com/tenstorrent/pytorch2.0_ttnn/blob/7ba2867e/.github/workflows/before_merge.yaml#L1-L27)[.github/workflows/run-tests.yaml 1-469](https://github.com/tenstorrent/pytorch2.0_ttnn/blob/7ba2867e/.github/workflows/run-tests.yaml#L1-L469)[.github/workflows/update-readme.yaml 1-45](https://github.com/tenstorrent/pytorch2.0_ttnn/blob/7ba2867e/.github/workflows/update-readme.yaml#L1-L45)[.github/workflows/update-docker-container.yaml 1-132](https://github.com/tenstorrent/pytorch2.0_ttnn/blob/7ba2867e/.github/workflows/update-docker-container.yaml#L1-L132)[.github/workflows/update-ttnn-wheel.yaml 1-121](https://github.com/tenstorrent/pytorch2.0_ttnn/blob/7ba2867e/.github/workflows/update-ttnn-wheel.yaml#L1-L121)[.github/workflows/run-accuracy-tests.yaml 1-180](https://github.com/tenstorrent/pytorch2.0_ttnn/blob/7ba2867e/.github/workflows/run-accuracy-tests.yaml#L1-L180)[.github/workflows/community-issue-tagging.yml 1-15](https://github.com/tenstorrent/pytorch2.0_ttnn/blob/7ba2867e/.github/workflows/community-issue-tagging.yml#L1-L15)

### Workflow Trigger Conditions

| Workflow | Trigger Type | Trigger Condition | Purpose |
| --- | --- | --- | --- |
| `before_merge.yaml` | Automatic | `merge_group` event | Gate before merge to main |
| `run-tests.yaml` | Reusable | `workflow_call` | Execute test suite |
| `update-readme.yaml` | Manual | `workflow_dispatch` | Update documentation |
| `update-docker-container.yaml` | Manual | `workflow_dispatch` | Rebuild and test container |
| `update-ttnn-wheel.yaml` | Manual | `workflow_dispatch` | Update TTNN dependency |
| `run-accuracy-tests.yaml` | Manual | `workflow_dispatch` | Run detailed accuracy tests |
| `community-issue-tagging.yml` | Automatic | Issue/PR opened | Tag community contributions |

**Sources:**[.github/workflows/before_merge.yaml 3-4](https://github.com/tenstorrent/pytorch2.0_ttnn/blob/7ba2867e/.github/workflows/before_merge.yaml#L3-L4)[.github/workflows/run-tests.yaml 2-45](https://github.com/tenstorrent/pytorch2.0_ttnn/blob/7ba2867e/.github/workflows/run-tests.yaml#L2-L45)[.github/workflows/update-readme.yaml 3-4](https://github.com/tenstorrent/pytorch2.0_ttnn/blob/7ba2867e/.github/workflows/update-readme.yaml#L3-L4)[.github/workflows/update-docker-container.yaml 3-9](https://github.com/tenstorrent/pytorch2.0_ttnn/blob/7ba2867e/.github/workflows/update-docker-container.yaml#L3-L9)[.github/workflows/update-ttnn-wheel.yaml 3-4](https://github.com/tenstorrent/pytorch2.0_ttnn/blob/7ba2867e/.github/workflows/update-ttnn-wheel.yaml#L3-L4)[.github/workflows/run-accuracy-tests.yaml 3-21](https://github.com/tenstorrent/pytorch2.0_ttnn/blob/7ba2867e/.github/workflows/run-accuracy-tests.yaml#L3-L21)[.github/workflows/community-issue-tagging.yml 3-7](https://github.com/tenstorrent/pytorch2.0_ttnn/blob/7ba2867e/.github/workflows/community-issue-tagging.yml#L3-L7)

* * *

## Main Test Workflow: run-tests.yaml

The `run-tests.yaml` workflow is the central test orchestrator that executes all test suites, collects metrics, and optionally generates documentation. It uses a reusable workflow pattern (`workflow_call`) to be invoked by other workflows.

### Workflow Inputs and Outputs

The workflow accepts three input parameters:

*   `commit_report`: Controls what artifacts to commit (`'None'`, `'Docs'`, `'All'`, `'Tests'`)
*   `docker_tag`: Specifies which Docker container to use (default: `'ghcr.io/tenstorrent/pytorch2.0_ttnn/ubuntu-22.04-amd64:latest'`)
*   `mock_run`: Array of test groups to run (empty means all tests)

It outputs:

*   `tests_passed`: Exit code indicating test success (0 = pass, 1 = fail)
*   `pull_request_number`: PR number if documentation was updated

**Sources:**[.github/workflows/run-tests.yaml 4-26](https://github.com/tenstorrent/pytorch2.0_ttnn/blob/7ba2867e/.github/workflows/run-tests.yaml#L4-L26)

### Job Execution Flow

**Sources:**[.github/workflows/run-tests.yaml 47-469](https://github.com/tenstorrent/pytorch2.0_ttnn/blob/7ba2867e/.github/workflows/run-tests.yaml#L47-L469)

### Test Job Types

The workflow executes several categories of tests in sequence:

1.   **tools-tests**: Validates testing utilities and helper functions ([.github/workflows/run-tests.yaml 48-68](https://github.com/tenstorrent/pytorch2.0_ttnn/blob/7ba2867e/.github/workflows/run-tests.yaml#L48-L68))
2.   **lowering-tests**: Tests ATen to TTNN operation lowering with 2-group parallelization ([.github/workflows/run-tests.yaml 70-92](https://github.com/tenstorrent/pytorch2.0_ttnn/blob/7ba2867e/.github/workflows/run-tests.yaml#L70-L92))
3.   **model-tests**: Executes full model tests with dynamic parallelization based on test count ([.github/workflows/run-tests.yaml 110-153](https://github.com/tenstorrent/pytorch2.0_ttnn/blob/7ba2867e/.github/workflows/run-tests.yaml#L110-L153))
4.   **multi-device-tests**: Runs multi-device data parallel tests on N300 hardware ([.github/workflows/run-tests.yaml 165-204](https://github.com/tenstorrent/pytorch2.0_ttnn/blob/7ba2867e/.github/workflows/run-tests.yaml#L165-L204))
5.   **model-autogen-op-tests**: Runs autogenerated operation tests from observed model input variations ([.github/workflows/run-tests.yaml 266-306](https://github.com/tenstorrent/pytorch2.0_ttnn/blob/7ba2867e/.github/workflows/run-tests.yaml#L266-L306))

**Sources:**[.github/workflows/run-tests.yaml 48-306](https://github.com/tenstorrent/pytorch2.0_ttnn/blob/7ba2867e/.github/workflows/run-tests.yaml#L48-L306)

* * *

## Test Parallelization and Matrix Jobs

The workflow uses GitHub Actions matrix strategy to parallelize test execution across multiple jobs. The number of parallel jobs is dynamically determined by counting test files.

### Dynamic Matrix Generation

The `count-test-files` job generates a matrix array that determines how many parallel jobs to spawn. Each job in the matrix runs a subset of tests using pytest's `--splits` and `--group` options.

**Sources:**[.github/workflows/run-tests.yaml 93-109](https://github.com/tenstorrent/pytorch2.0_ttnn/blob/7ba2867e/.github/workflows/run-tests.yaml#L93-L109)

### Model Test Parallelization

Model tests are split across multiple runners using a matrix strategy:

`strategy:  matrix:     group: ${{ fromJson(needs.count-test-files.outputs.matrix) }}`
Each matrix job:

1.   Checks out the repository with LFS support
2.   Sets up the Python environment via `common_repo_setup` action
3.   Runs tests for its assigned group: `pytest --splits N --group M`
4.   Uploads metrics artifacts for its group

**Sources:**[.github/workflows/run-tests.yaml 129-153](https://github.com/tenstorrent/pytorch2.0_ttnn/blob/7ba2867e/.github/workflows/run-tests.yaml#L129-L153)

### Runner Configuration

Different test types use different runner configurations:

| Test Type | Runner Labels | Container Options |
| --- | --- | --- |
| tools-tests | `["in-service"]` | Standard Tenstorrent device mount |
| lowering-tests | `["in-service"]` | Standard Tenstorrent device mount |
| model-tests | `["in-service", "nfs"]` | Device mount + NFS cache volumes |
| multi-device-tests | `["n300", "in-service", "nfs"]` | N300 hardware + NFS cache |

All jobs use the same Docker container image specified by the `docker_tag` input parameter and mount:

*   `/dev/hugepages-1G` for huge pages support
*   `/dev/tenstorrent` for hardware access
*   `/mnt/tt-metal-pytorch-cache/.cache:/root/.cache` for model and TTNN cache (model-tests and multi-device-tests only)

**Sources:**[.github/workflows/run-tests.yaml 54-192](https://github.com/tenstorrent/pytorch2.0_ttnn/blob/7ba2867e/.github/workflows/run-tests.yaml#L54-L192)

* * *

## Artifact Collection and Aggregation

Test jobs generate metrics in pickle files which are collected as GitHub Actions artifacts, then aggregated for documentation generation.

### Artifact Flow

**Sources:**[.github/workflows/run-tests.yaml 147-153](https://github.com/tenstorrent/pytorch2.0_ttnn/blob/7ba2867e/.github/workflows/run-tests.yaml#L147-L153)[.github/workflows/run-tests.yaml 228-233](https://github.com/tenstorrent/pytorch2.0_ttnn/blob/7ba2867e/.github/workflows/run-tests.yaml#L228-L233)[.github/workflows/run-tests.yaml 308-329](https://github.com/tenstorrent/pytorch2.0_ttnn/blob/7ba2867e/.github/workflows/run-tests.yaml#L308-L329)[.github/workflows/run-tests.yaml 372-384](https://github.com/tenstorrent/pytorch2.0_ttnn/blob/7ba2867e/.github/workflows/run-tests.yaml#L372-L384)

### Artifact Types

The workflow collects three types of artifacts:

1.   **Model Test Metrics**: Runtime performance, accuracy, and schema information from model tests

    *   Artifact pattern: `model-tests-metrics-group-*`
    *   Contents: Pickle files in `metrics/` directory
    *   Used by: `push-autogen-op-tests` and `collect-metrics` jobs

2.   **Autogen Op Test Metrics**: Results from running autogenerated operation tests

    *   Artifact pattern: `model-autogen-op-tests-metrics-group-*`
    *   Contents: Pickle files in `metrics-autogen-op/` directory
    *   Used by: `collect-metrics` job

3.   **Multi-Device Test Metrics**: Results from N300 multi-device tests

    *   Artifact name: `multi-device-tests-metrics`
    *   Contents: Pickle files in `metrics/` directory
    *   Used by: `collect-metrics` job

**Sources:**[.github/workflows/run-tests.yaml 147-153](https://github.com/tenstorrent/pytorch2.0_ttnn/blob/7ba2867e/.github/workflows/run-tests.yaml#L147-L153)[.github/workflows/run-tests.yaml 199-204](https://github.com/tenstorrent/pytorch2.0_ttnn/blob/7ba2867e/.github/workflows/run-tests.yaml#L199-L204)[.github/workflows/run-tests.yaml 301-306](https://github.com/tenstorrent/pytorch2.0_ttnn/blob/7ba2867e/.github/workflows/run-tests.yaml#L301-L306)

* * *

## Documentation Generation and PR Creation

When `commit_report` is set to `'Docs'` or `'All'`, the workflow automatically generates documentation from test metrics and creates a pull request.

### Documentation Generation Process

**Sources:**[.github/workflows/run-tests.yaml 351-428](https://github.com/tenstorrent/pytorch2.0_ttnn/blob/7ba2867e/.github/workflows/run-tests.yaml#L351-L428)

The `collect-metrics` job:

1.   Downloads all metrics artifacts from matrix jobs
2.   Executes `python3 tools/collect_metrics.py` to aggregate data
3.   Determines which files to commit based on `commit_report` input
4.   Uses `peter-evans/create-pull-request@v7` action to create PR
5.   Commits to a timestamped branch: `update-docs-{timestamp}`

**Sources:**[.github/workflows/run-tests.yaml 399-428](https://github.com/tenstorrent/pytorch2.0_ttnn/blob/7ba2867e/.github/workflows/run-tests.yaml#L399-L428)

### Autogenerated Test Generation

The `push-autogen-op-tests` job generates operation-specific tests before documentation generation:

1.   Downloads model test metrics artifacts
2.   Runs `generate_input_variation_test_from_models.py` to create test files in `tests/autogen_op/`
3.   Runs the script again with `--merge=True` to consolidate variations
4.   Creates a PR with the generated tests if `commit_report` is `'All'` or `'Tests'`

**Sources:**[.github/workflows/run-tests.yaml 206-264](https://github.com/tenstorrent/pytorch2.0_ttnn/blob/7ba2867e/.github/workflows/run-tests.yaml#L206-L264)

* * *

## TTNN Dependency Management

The `update-ttnn-wheel.yaml` workflow automates the process of updating the TTNN library dependency to the latest pre-release version.

### Dependency Update Process

**Sources:**[.github/workflows/update-ttnn-wheel.yaml 1-121](https://github.com/tenstorrent/pytorch2.0_ttnn/blob/7ba2867e/.github/workflows/update-ttnn-wheel.yaml#L1-L121)

### Version Extraction and Update

The workflow fetches the latest TTNN wheel from the internal PyPI server:

`latest_pre_release=$(curl -s $repo_url | sed -n 's/.*href="\([^"]*\).*/\1/p' | grep cp310 | sort -V | tail -n 1)`
It then updates three files:

1.   **requirements.txt**: Direct wheel URL with SHA256 hash
2.   **setup.py**: GitHub release URL for public installation
3.   **pyproject.toml**: Same format as setup.py for modern Python packaging

**Sources:**[.github/workflows/update-ttnn-wheel.yaml 20-50](https://github.com/tenstorrent/pytorch2.0_ttnn/blob/7ba2867e/.github/workflows/update-ttnn-wheel.yaml#L20-L50)

### C++ Extension Cache Building

The `build_cache_cpp_extension` job runs in parallel to pre-build C++ extensions:

1.   Updates `.gitmodules` to point to the latest tt-metal version
2.   Runs the `build_cpp_extension_artifacts` action
3.   Caches the built extensions in `/root/.cache/cpp-extension-cache`

This ensures that subsequent test runs can use pre-built extensions without recompilation.

**Sources:**[.github/workflows/update-ttnn-wheel.yaml 81-121](https://github.com/tenstorrent/pytorch2.0_ttnn/blob/7ba2867e/.github/workflows/update-ttnn-wheel.yaml#L81-L121)

* * *

## Docker Container Infrastructure

The Docker container infrastructure provides a consistent execution environment for all CI/CD jobs. Containers are versioned using content-based hashing.

### Container Build and Versioning

**Sources:**[.github/workflows/update-docker-container.yaml 14-132](https://github.com/tenstorrent/pytorch2.0_ttnn/blob/7ba2867e/.github/workflows/update-docker-container.yaml#L14-L132)

### Docker Image Naming

Container images follow this naming pattern:

*   **SHA1-tagged**: `ghcr.io/tenstorrent/pytorch2.0_ttnn/ubuntu22.04:{SHA1_HASH}`
*   **Latest**: `ghcr.io/tenstorrent/pytorch2.0_ttnn/ubuntu-22.04-amd64:latest`

The SHA1 hash is computed from the concatenation of:

`cat requirements.txt dockerfile/Dockerfile requirements-dev.txt | sha1sum`
This ensures that any change to dependencies or the Dockerfile triggers a new container build.

**Sources:**[.github/workflows/update-docker-container.yaml 26-34](https://github.com/tenstorrent/pytorch2.0_ttnn/blob/7ba2867e/.github/workflows/update-docker-container.yaml#L26-L34)

### Base Image and Dockerfile

The Dockerfile is minimal and builds on top of the tt-metalium base image:

`FROM ghcr.io/tenstorrent/tt-metal/tt-metalium/ubuntu-22.04-dev-amd64:latest AS release`
It installs:

*   System dependencies: `sudo`, `libgl1-mesa-glx`, `git-lfs`, `libsndfile1`, `docker.io`
*   Initializes git-lfs for large file support

**Sources:**[dockerfile/Dockerfile 1-15](https://github.com/tenstorrent/pytorch2.0_ttnn/blob/7ba2867e/dockerfile/Dockerfile#L1-L15)

* * *

## Development Environment Setup

The `common_repo_setup` composite action standardizes the Python environment setup across all CI jobs.

### Environment Setup Flow

**Sources:**[.github/actions/common_repo_setup/action.yaml 1-34](https://github.com/tenstorrent/pytorch2.0_ttnn/blob/7ba2867e/.github/actions/common_repo_setup/action.yaml#L1-L34)

### Python Dependencies

The primary dependencies are specified in `requirements.txt`:

| Package | Version | Purpose |
| --- | --- | --- |
| `torch` | 2.7.1+cpu | PyTorch framework (CPU-only for CI) |
| `torchvision` | 0.22.1+cpu | Vision models and utilities |
| `ttnn` | 0.62.0rc36.dev247+g627c4eed5b | Tenstorrent TTNN library |
| `tabulate` | 0.9.0 | Table formatting for reports |
| `networkx` | 3.1 | Graph utilities |
| `graphviz` | - | Graph visualization |
| `matplotlib` | 3.7.1 | Plotting |
| `paramiko` | 3.5.1 | SSH/SFTP for data transfer |

The TTNN wheel is installed from an internal PyPI server with a SHA256 hash for verification and is Python 3.10 specific.

**Sources:**[requirements.txt 1-10](https://github.com/tenstorrent/pytorch2.0_ttnn/blob/7ba2867e/requirements.txt#L1-L10)

### OS-Specific Dependencies

The `common_repo_setup` action can install OS-specific packages defined in `dependencies.json`:

| Ubuntu Version | Key Dependencies |
| --- | --- |
| 20.04 | `build-essential`, `python3.8-venv`, `libhwloc-dev`, `graphviz`, `patchelf` |
| 22.04 | `build-essential`, `python3.10-venv`, `libhwloc-dev`, `graphviz`, `patchelf` |
| 24.04 | `build-essential`, `python3.10-venv`, `libhwloc-dev`, `graphviz`, `patchelf` |

**Sources:**[.github/actions/common_repo_setup/dependencies.json 1-27](https://github.com/tenstorrent/pytorch2.0_ttnn/blob/7ba2867e/.github/actions/common_repo_setup/dependencies.json#L1-L27)

### Cache Configuration

The environment setup configures caching for:

*   **Pip packages**: `PIP_CACHE_DIR` environment variable (usually `/root/pip_cache/.pip_cache` or `/root/.cache`)
*   **PyTorch models**: `TORCH_HOME=/root/.cache/torch`
*   **HuggingFace models**: `HF_HOME=/root/.cache/huggingface`

These caches are mounted as Docker volumes from the NFS-backed storage at `/mnt/tt-metal-pytorch-cache/.cache` on runners with the `nfs` label.

**Sources:**[.github/workflows/run-tests.yaml 121-128](https://github.com/tenstorrent/pytorch2.0_ttnn/blob/7ba2867e/.github/workflows/run-tests.yaml#L121-L128)[.github/workflows/run-tests.yaml 190-192](https://github.com/tenstorrent/pytorch2.0_ttnn/blob/7ba2867e/.github/workflows/run-tests.yaml#L190-L192)

* * *

## Merge Gate and Release Process

The `before_merge.yaml` workflow serves as the merge gate, ensuring all tests pass before code is merged into the main branch.

### Before Merge Workflow

The workflow:

1.   Triggers on `merge_group` events (GitHub merge queue)
2.   Invokes `run-tests.yaml` with `commit_report: 'None'` (no documentation updates)
3.   Uses the `latest` Docker container tag
4.   Validates that all test jobs passed
5.   Exits with the appropriate code to allow or block merge

**Sources:**[.github/workflows/before_merge.yaml 1-27](https://github.com/tenstorrent/pytorch2.0_ttnn/blob/7ba2867e/.github/workflows/before_merge.yaml#L1-L27)

### Release Process

While there is no automated release workflow, releases are managed through:

1.   **Docker Container Updates**: Manual trigger of `update-docker-container.yaml` after dependency changes
2.   **TTNN Dependency Updates**: Manual trigger of `update-ttnn-wheel.yaml` when new TTNN versions are available
3.   **Documentation Updates**: Manual trigger of `update-readme.yaml` to refresh metrics and operation reports

All PRs created by these workflows include auto-approval and auto-merge configuration, allowing them to merge automatically once tests pass.

**Sources:**[.github/workflows/update-readme.yaml 1-45](https://github.com/tenstorrent/pytorch2.0_ttnn/blob/7ba2867e/.github/workflows/update-readme.yaml#L1-L45)[.github/workflows/update-ttnn-wheel.yaml 67-80](https://github.com/tenstorrent/pytorch2.0_ttnn/blob/7ba2867e/.github/workflows/update-ttnn-wheel.yaml#L67-L80)

* * *

## Community Workflows

The `community-issue-tagging.yml` workflow provides automated labeling for community contributions.

### Community Issue Tagging

This workflow delegates to a centralized workflow maintained by the Tenstorrent organization to ensure consistent labeling across repositories.

**Sources:**[.github/workflows/community-issue-tagging.yml 1-15](https://github.com/tenstorrent/pytorch2.0_ttnn/blob/7ba2867e/.github/workflows/community-issue-tagging.yml#L1-L15)

* * *

## Test Result Validation

All test workflows include a final validation job that aggregates results from matrix jobs and determines overall pass/fail status.

### Validation Logic

The `tests-passed` job in `run-tests.yaml` checks the result of all prerequisite jobs:

`- id: check  run: |    if [[ ($tools_result == "success" || $tools_result == "skipped") &&           ($multi_device_result == "success" || $multi_device_result == "skipped") &&           ($lowering_result == "success" || $lowering_result == "skipped") &&           ($model_result == "success" || $model_result == "skipped") ]] ; then      echo "didpass=0" >> $GITHUB_OUTPUT      exit 0    else      echo "didpass=1" >> $GITHUB_OUTPUT      exit 1    fi`
This validation:

*   Treats both `success` and `skipped` results as passing
*   Requires all test categories to pass
*   Outputs `didpass=0` for success or `didpass=1` for failure
*   This output is used by `before_merge.yaml` to gate merges

**Sources:**[.github/workflows/run-tests.yaml 446-468](https://github.com/tenstorrent/pytorch2.0_ttnn/blob/7ba2867e/.github/workflows/run-tests.yaml#L446-L468)

* * *

## Summary

The CI/CD infrastructure for pytorch2.0_ttnn provides:

1.   **Automated Testing**: Comprehensive test suite with parallelization across multiple runners
2.   **Dependency Management**: Automated TTNN wheel updates with PR creation and auto-merge
3.   **Container Management**: Content-addressed Docker images with automatic rebuilds
4.   **Documentation Generation**: Metrics-driven documentation updates via automated PRs
5.   **Merge Protection**: Before-merge validation ensuring all tests pass
6.   **Artifact Management**: Structured collection and aggregation of test metrics

The system uses GitHub Actions extensively with reusable workflows, composite actions, and matrix strategies to achieve efficient and scalable CI/CD operations.

Dismiss
Refresh this wiki

Enter email to refresh
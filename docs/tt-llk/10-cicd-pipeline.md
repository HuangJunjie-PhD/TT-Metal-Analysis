---
project: tt-llk
github: https://github.com/tenstorrent/tt-llk
deepwiki: https://deepwiki.com/tenstorrent/tt-llk/10-cicd-pipeline
---

# CI/CD Pipeline

 Relevant source files

* [.github/workflows/build-quasar.yml](https://github.com/tenstorrent/tt-llk/blob/366f58e2/.github/workflows/build-quasar.yml)
* [.github/workflows/on-pr.yml](https://github.com/tenstorrent/tt-llk/blob/366f58e2/.github/workflows/on-pr.yml)
* [.github/workflows/setup-and-test.yml](https://github.com/tenstorrent/tt-llk/blob/366f58e2/.github/workflows/setup-and-test.yml)
* [.gitignore](https://github.com/tenstorrent/tt-llk/blob/366f58e2/.gitignore)
* [tests/helpers/ld/memory.blackhole.debug.ld](https://github.com/tenstorrent/tt-llk/blob/366f58e2/tests/helpers/ld/memory.blackhole.debug.ld)
* [tests/helpers/ld/memory.wormhole.debug.ld](https://github.com/tenstorrent/tt-llk/blob/366f58e2/tests/helpers/ld/memory.wormhole.debug.ld)
* [tests/python\_tests/conftest.py](https://github.com/tenstorrent/tt-llk/blob/366f58e2/tests/python_tests/conftest.py)
* [tests/python\_tests/helpers/test\_config.py](https://github.com/tenstorrent/tt-llk/blob/366f58e2/tests/python_tests/helpers/test_config.py)
* [tests/requirements.txt](https://github.com/tenstorrent/tt-llk/blob/366f58e2/tests/requirements.txt)

This document describes the GitHub Actions-based continuous integration and continuous deployment (CI/CD) system that automates testing, building, and quality assurance for the tt-llk repository. The pipeline orchestrates static analysis, Docker image construction, change detection, test execution across multiple hardware architectures, and result reporting.

For information about the testing framework that the CI/CD pipeline executes, see [Testing Framework](/tenstorrent/tt-llk/8-testing-framework). For details about the build system and toolchain, see [Build System and Toolchain](/tenstorrent/tt-llk/9-build-system-and-toolchain).

---

## Pipeline Architecture Overview

The CI/CD pipeline is implemented through GitHub Actions workflows that coordinate multiple stages of validation. The primary workflow [`on-pr.yml`](https://github.com/tenstorrent/tt-llk/blob/366f58e2/`on-pr.yml`)() orchestrates job dependencies, while reusable workflows handle specialized tasks.

### Workflow Dependency Graph

**Sources:** [.github/workflows/on-pr.yml1-224](https://github.com/tenstorrent/tt-llk/blob/366f58e2/.github/workflows/on-pr.yml#L1-L224)

---

## Workflow Triggers and Concurrency Control

### Trigger Configuration

The pipeline activates on four event types defined in [.github/workflows/on-pr.yml2-9](https://github.com/tenstorrent/tt-llk/blob/366f58e2/.github/workflows/on-pr.yml#L2-L9):

| Trigger | Branches | Types | Purpose |
| --- | --- | --- | --- |
| `push` | `main` | N/A | Validate commits to protected branch |
| `pull_request` | `main` | `opened`, `synchronize`, `reopened`, `ready_for_review` | Validate PR changes |
| `workflow_dispatch` | N/A | N/A | Manual trigger for debugging |
| `merge_group` | N/A | N/A | Validate merge queue entries |

### Concurrency Strategy

The concurrency control mechanism in [.github/workflows/on-pr.yml17-25](https://github.com/tenstorrent/tt-llk/blob/366f58e2/.github/workflows/on-pr.yml#L17-L25) prevents redundant pipeline executions:

**Logic:**

* **Protected branches (main):** Use `github.run_id` to ensure no runs are cancelled
* **Pull requests:** Use `github.event.pull_request.number` to cancel obsolete runs when new commits are pushed
* **Other branches:** Use `github.ref` for branch-specific grouping

**Sources:** [.github/workflows/on-pr.yml17-25](https://github.com/tenstorrent/tt-llk/blob/366f58e2/.github/workflows/on-pr.yml#L17-L25)

---

## Change Detection and Conditional Execution

### Change Detection Job

The `detect-changes` job ([.github/workflows/on-pr.yml32-76](https://github.com/tenstorrent/tt-llk/blob/366f58e2/.github/workflows/on-pr.yml#L32-L76)) uses `tj-actions/changed-files@v47` to analyze which directories have modifications:

### Conditional Job Execution

Jobs use the `if` condition to execute only when relevant changes are detected. For example, the Wormhole test job in [.github/workflows/on-pr.yml148-151](https://github.com/tenstorrent/tt-llk/blob/366f58e2/.github/workflows/on-pr.yml#L148-L151):

| Job | Triggers When Changes In |
| --- | --- |
| `setup-and-test-wormhole` | `tt_llk_wormhole_b0/`, `tests/`, `.github/` |
| `setup-and-test-blackhole` | `tt_llk_blackhole/`, `tests/`, `.github/` |
| `build-quasar` | `tt_llk_quasar/`, `tests/sources/quasar/`, `tests/python_tests/quasar/` |
| `perf-tests` | `**/*perf**`, `**/*profiler**` |

**Sources:** [.github/workflows/on-pr.yml32-76](https://github.com/tenstorrent/tt-llk/blob/366f58e2/.github/workflows/on-pr.yml#L32-L76) [.github/workflows/on-pr.yml148-151](https://github.com/tenstorrent/tt-llk/blob/366f58e2/.github/workflows/on-pr.yml#L148-L151) [.github/workflows/on-pr.yml163-166](https://github.com/tenstorrent/tt-llk/blob/366f58e2/.github/workflows/on-pr.yml#L163-L166) [.github/workflows/on-pr.yml178-179](https://github.com/tenstorrent/tt-llk/blob/366f58e2/.github/workflows/on-pr.yml#L178-L179) [.github/workflows/on-pr.yml190](https://github.com/tenstorrent/tt-llk/blob/366f58e2/.github/workflows/on-pr.yml#L190-L190)

---

## Automated PR Labeling

The `label-pr` job ([.github/workflows/on-pr.yml77-136](https://github.com/tenstorrent/tt-llk/blob/366f58e2/.github/workflows/on-pr.yml#L77-L136)) automatically applies and removes labels based on detected changes:

### Label Synchronization Logic

**Managed Labels:** `blackhole`, `ci`, `documentation`, `quasar`, `performance`, `test-infra`, `wormhole`

The job uses `actions/github-script@v7` to execute GitHub API operations. Label removal operations run in parallel using `Promise.all` for efficiency ([.github/workflows/on-pr.yml128-135](https://github.com/tenstorrent/tt-llk/blob/366f58e2/.github/workflows/on-pr.yml#L128-L135)).

**Sources:** [.github/workflows/on-pr.yml77-136](https://github.com/tenstorrent/tt-llk/blob/366f58e2/.github/workflows/on-pr.yml#L77-L136)

---

## Docker Image Construction

### Build Images Workflow

The `build-images` workflow ([.github/workflows/build-images.yml1-39](https://github.com/tenstorrent/tt-llk/blob/366f58e2/.github/workflows/build-images.yml#L1-L39)) constructs Docker images with the SFPI toolchain:

The workflow executes [.github/scripts/build-docker-images.sh](https://github.com/tenstorrent/tt-llk/blob/366f58e2/.github/scripts/build-docker-images.sh) which:

1. Builds the Docker image with layer caching
2. Pushes to GitHub Container Registry (`ghcr.io`)
3. Outputs the image tag for use by downstream jobs

The `build-images` job in [.github/workflows/on-pr.yml138-142](https://github.com/tenstorrent/tt-llk/blob/366f58e2/.github/workflows/on-pr.yml#L138-L142) calls this reusable workflow and provides its output to test jobs via `needs.build-images.outputs.docker-image`.

**Sources:** [.github/workflows/build-images.yml1-39](https://github.com/tenstorrent/tt-llk/blob/366f58e2/.github/workflows/build-images.yml#L1-L39) [.github/workflows/on-pr.yml138-142](https://github.com/tenstorrent/tt-llk/blob/366f58e2/.github/workflows/on-pr.yml#L138-L142)

---

## Test Execution Architecture

### Self-Hosted Runners

The pipeline uses self-hosted runners with actual hardware for test execution:

| Runner | Hardware | Usage |
| --- | --- | --- |
| `tt-ubuntu-2204-n150-stable` | Wormhole B0 (n150) | Wormhole tests, performance tests |
| `tt-ubuntu-2204-p150b-stable` | Blackhole (p150b) | Blackhole tests, performance tests |
| `tt-ubuntu-2204-xlarge-stable` | High-CPU machine | Quasar compilation (no hardware) |

### Producer-Consumer Pattern

The `setup-and-test.yml` reusable workflow (referenced but not provided in files) implements a producer-consumer pattern for efficient test execution:

**Test Splits:** Tests are divided into N groups (`test_splits` parameter) for parallel execution. The Wormhole and Blackhole jobs use 2 splits ([.github/workflows/on-pr.yml155](https://github.com/tenstorrent/tt-llk/blob/366f58e2/.github/workflows/on-pr.yml#L155-L155) [.github/workflows/on-pr.yml170](https://github.com/tenstorrent/tt-llk/blob/366f58e2/.github/workflows/on-pr.yml#L170-L170)), while performance tests use 5 splits ([.github/workflows/on-pr.yml202](https://github.com/tenstorrent/tt-llk/blob/366f58e2/.github/workflows/on-pr.yml#L202-L202)).

**Sources:** [.github/workflows/on-pr.yml144-172](https://github.com/tenstorrent/tt-llk/blob/366f58e2/.github/workflows/on-pr.yml#L144-L172)

---

## Quasar Build Pipeline

Quasar follows a compilation-only workflow since it lacks physical hardware. The `build-quasar.yml` workflow ([.github/workflows/build-quasar.yml1-107](https://github.com/tenstorrent/tt-llk/blob/366f58e2/.github/workflows/build-quasar.yml#L1-L107)) compiles tests without execution:

### Quasar Compilation Steps

**Key Parameters:**

* `-n 40`: 40 parallel pytest workers for compilation
* `--compile-producer`: Producer mode (compile only, no execution)
* `--timeout=60`: 60-second timeout per test
* Marker: `quasar` ([.github/workflows/build-quasar.yml21](https://github.com/tenstorrent/tt-llk/blob/366f58e2/.github/workflows/build-quasar.yml#L21-L21))

The job runs on `tt-ubuntu-2204-xlarge-stable` ([.github/workflows/on-pr.yml182](https://github.com/tenstorrent/tt-llk/blob/366f58e2/.github/workflows/on-pr.yml#L182-L182)), a high-CPU runner optimized for compilation throughput.

**Sources:** [.github/workflows/build-quasar.yml1-107](https://github.com/tenstorrent/tt-llk/blob/366f58e2/.github/workflows/build-quasar.yml#L1-L107) [.github/workflows/on-pr.yml174-185](https://github.com/tenstorrent/tt-llk/blob/366f58e2/.github/workflows/on-pr.yml#L174-L185)

---

## Performance Testing

Performance tests execute separately with extended timeouts and more test splits:

### Performance Test Configuration

The `perf-tests` job ([.github/workflows/on-pr.yml186-205](https://github.com/tenstorrent/tt-llk/blob/366f58e2/.github/workflows/on-pr.yml#L186-L205)) uses a matrix strategy to test both architectures:

**Configuration:**

* **Test Splits:** 5 (vs. 2 for functional tests)
* **Timeout:** 240 minutes (vs. default)
* **Pytest Markers:** `perf` ([.github/workflows/on-pr.yml203](https://github.com/tenstorrent/tt-llk/blob/366f58e2/.github/workflows/on-pr.yml#L203-L203))
* **Duration-based Balancing:** `use_durations: true` ([.github/workflows/on-pr.yml205](https://github.com/tenstorrent/tt-llk/blob/366f58e2/.github/workflows/on-pr.yml#L205-L205))

Performance tests only run when changes affect performance-related files ([.github/workflows/on-pr.yml190](https://github.com/tenstorrent/tt-llk/blob/366f58e2/.github/workflows/on-pr.yml#L190-L190)):

Performance test results are uploaded with a `perf-` prefix in artifact names for easy identification.

**Sources:** [.github/workflows/on-pr.yml186-205](https://github.com/tenstorrent/tt-llk/blob/366f58e2/.github/workflows/on-pr.yml#L186-L205)

---

## Static Analysis and Pre-commit Hooks

### Pre-commit Workflow

The `pre-commit.yml` workflow ([.github/workflows/pre-commit.yml1-68](https://github.com/tenstorrent/tt-llk/blob/366f58e2/.github/workflows/pre-commit.yml#L1-L68)) runs static analysis checks:

The workflow checks only the diff between `origin/main` and `HEAD` ([.github/workflows/pre-commit.yml30](https://github.com/tenstorrent/tt-llk/blob/366f58e2/.github/workflows/pre-commit.yml#L30-L30)) to minimize check time.

The PR comment ([.github/workflows/pre-commit.yml56-67](https://github.com/tenstorrent/tt-llk/blob/366f58e2/.github/workflows/pre-commit.yml#L56-L67)) informs contributors about:

* Adding `metal-post-commit-tests` label for integration testing
* Link to `CONTRIBUTING.md` guide

**Sources:** [.github/workflows/pre-commit.yml1-68](https://github.com/tenstorrent/tt-llk/blob/366f58e2/.github/workflows/pre-commit.yml#L1-L68)

---

## Final Validation

### Check All Green Job

The `check-all-green` job ([.github/workflows/on-pr.yml207-223](https://github.com/tenstorrent/tt-llk/blob/366f58e2/.github/workflows/on-pr.yml#L207-L223)) provides final pipeline validation:

**Allowed Skips:** The `allowed-skips` parameter ([.github/workflows/on-pr.yml223](https://github.com/tenstorrent/tt-llk/blob/366f58e2/.github/workflows/on-pr.yml#L223-L223)) permits conditional jobs to be skipped without failing the pipeline. This accommodates the change detection system where jobs only run for relevant changes.

**Always Run:** The `if: always()` condition ([.github/workflows/on-pr.yml209](https://github.com/tenstorrent/tt-llk/blob/366f58e2/.github/workflows/on-pr.yml#L209-L209)) ensures this check runs even if upstream jobs fail, providing a single source of truth for pipeline status.

**Sources:** [.github/workflows/on-pr.yml207-223](https://github.com/tenstorrent/tt-llk/blob/366f58e2/.github/workflows/on-pr.yml#L207-L223)

---

## JUnit Reporting and Test Results

### Test Report Publishing

Each test job publishes JUnit XML reports using `mikepenz/action-junit-report@v5`:

**Report Structure:**

* **Compilation reports:** `pytest-report-{arch}-compile.xml`
* **Execution reports:** `pytest-report-{arch}-execute.xml`
* **Performance reports:** Prefixed with `perf-` in artifact names

Reports are uploaded as artifacts for post-run analysis and debugging.

**Sources:** [.github/workflows/build-quasar.yml98-106](https://github.com/tenstorrent/tt-llk/blob/366f58e2/.github/workflows/build-quasar.yml#L98-L106)

---

## Coverage Collection

Coverage data is collected during test execution when the `WITH_COVERAGE` flag is enabled (see [Testing Framework](/tenstorrent/tt-llk/8.10-performance-testing-and-profiling) for details). The CI/CD pipeline:

1. **Execution:** Tests run with coverage instrumentation
2. **Collection:** `read_coverage_data_from_device` extracts `.gcda` files
3. **Merging:** `lcov` combines coverage from all test splits
4. **Filtering:** Test files are excluded from coverage reports
5. **Upload:** Merged `.info` file is uploaded to Codecov

The setup-and-test workflow handles coverage merging across test splits to produce a unified coverage report.

**Sources:** Referenced from high-level diagrams

---

## Environment Variables and Configuration

### Key Environment Variables

| Variable | Purpose | Set In |
| --- | --- | --- |
| `CHIP_ARCH` | Target architecture (`wormhole_b0`, `blackhole`, `quasar`) | Test workflows |
| `TEST_SPLITS` | Number of test groups | Workflow inputs |
| `GITHUB_TOKEN` | Authentication for GitHub API | Automatic by Actions |
| `DOCKER_CI_IMAGE` | Built Docker image tag | `build-images` output |

### Pytest Configuration

Tests are invoked with specific markers and flags:

**Common Flags:**

* `-q`: Quiet mode
* `-n 40`: 40 parallel workers (Quasar only)
* `--compile-producer`: Producer mode (compile)
* `--compile-consumer`: Consumer mode (execute pre-compiled)
* `--splits N --group M`: Split tests into N groups, run group M
* `--use-durations`: Duration-based balancing
* `--timeout=60`: Per-test timeout
* `--junitxml`: Generate JUnit XML report

**Sources:** [.github/workflows/build-quasar.yml81-85](https://github.com/tenstorrent/tt-llk/blob/366f58e2/.github/workflows/build-quasar.yml#L81-L85) [.github/workflows/on-pr.yml156](https://github.com/tenstorrent/tt-llk/blob/366f58e2/.github/workflows/on-pr.yml#L156-L156) [.github/workflows/on-pr.yml171](https://github.com/tenstorrent/tt-llk/blob/366f58e2/.github/workflows/on-pr.yml#L171-L171) [.github/workflows/on-pr.yml203](https://github.com/tenstorrent/tt-llk/blob/366f58e2/.github/workflows/on-pr.yml#L203-L203)

---

## Pipeline Permissions

The workflow requires specific GitHub permissions ([.github/workflows/on-pr.yml11-15](https://github.com/tenstorrent/tt-llk/blob/366f58e2/.github/workflows/on-pr.yml#L11-L15)):

| Permission | Access | Purpose |
| --- | --- | --- |
| `checks` | `write` | Create and update check runs |
| `contents` | `read` | Read repository contents |
| `packages` | `write` | Push Docker images to GHCR |
| `pull-requests` | `write` | Add labels and comments to PRs |

These permissions enable the pipeline to perform automated quality control operations without manual intervention.

**Sources:** [.github/workflows/on-pr.yml11-15](https://github.com/tenstorrent/tt-llk/blob/366f58e2/.github/workflows/on-pr.yml#L11-L15)

Refresh this wiki
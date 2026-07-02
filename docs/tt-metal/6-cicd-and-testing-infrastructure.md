---
project: tt-metal
github: https://github.com/tenstorrent/tt-metal
deepwiki: https://deepwiki.com/tenstorrent/tt-metal/6-cicd-and-testing-infrastructure
---

# CI/CD and Testing Infrastructure

Relevant source files
*   [.github/CODEOWNERS](https://github.com/tenstorrent/tt-metal/blob/f30f8df0/.github/CODEOWNERS)
*   [.github/actions/find-changed-files/action.yml](https://github.com/tenstorrent/tt-metal/blob/f30f8df0/.github/actions/find-changed-files/action.yml)
*   [.github/actions/manual-docker-bake/action.yml](https://github.com/tenstorrent/tt-metal/blob/f30f8df0/.github/actions/manual-docker-bake/action.yml)
*   [.github/actions/report-failure/action.yml](https://github.com/tenstorrent/tt-metal/blob/f30f8df0/.github/actions/report-failure/action.yml)
*   [.github/scripts/compute-platform-data.sh](https://github.com/tenstorrent/tt-metal/blob/f30f8df0/.github/scripts/compute-platform-data.sh)
*   [.github/scripts/utils/find-changed-files.sh](https://github.com/tenstorrent/tt-metal/blob/f30f8df0/.github/scripts/utils/find-changed-files.sh)
*   [.github/scripts/utils/model-charts-sync.py](https://github.com/tenstorrent/tt-metal/blob/f30f8df0/.github/scripts/utils/model-charts-sync.py)
*   [.github/workflows/all-model-tests.yaml](https://github.com/tenstorrent/tt-metal/blob/f30f8df0/.github/workflows/all-model-tests.yaml)
*   [.github/workflows/basic.yaml](https://github.com/tenstorrent/tt-metal/blob/f30f8df0/.github/workflows/basic.yaml)
*   [.github/workflows/build-artifact.yaml](https://github.com/tenstorrent/tt-metal/blob/f30f8df0/.github/workflows/build-artifact.yaml)
*   [.github/workflows/build-docker-artifact.yaml](https://github.com/tenstorrent/tt-metal/blob/f30f8df0/.github/workflows/build-docker-artifact.yaml)
*   [.github/workflows/build-docker-tools.yaml](https://github.com/tenstorrent/tt-metal/blob/f30f8df0/.github/workflows/build-docker-tools.yaml)
*   [.github/workflows/build-evaluation-image.yaml](https://github.com/tenstorrent/tt-metal/blob/f30f8df0/.github/workflows/build-evaluation-image.yaml)
*   [.github/workflows/build-wrapper.yaml](https://github.com/tenstorrent/tt-metal/blob/f30f8df0/.github/workflows/build-wrapper.yaml)
*   [.github/workflows/clang-tidy-reusable.yaml](https://github.com/tenstorrent/tt-metal/blob/f30f8df0/.github/workflows/clang-tidy-reusable.yaml)
*   [.github/workflows/code-analysis.yaml](https://github.com/tenstorrent/tt-metal/blob/f30f8df0/.github/workflows/code-analysis.yaml)
*   [.github/workflows/fast-dispatch-full-regressions-and-models-impl.yaml](https://github.com/tenstorrent/tt-metal/blob/f30f8df0/.github/workflows/fast-dispatch-full-regressions-and-models-impl.yaml)
*   [.github/workflows/fast-dispatch-full-regressions-and-models.yaml](https://github.com/tenstorrent/tt-metal/blob/f30f8df0/.github/workflows/fast-dispatch-full-regressions-and-models.yaml)
*   [.github/workflows/galaxy-deepseek-tests-impl.yaml](https://github.com/tenstorrent/tt-metal/blob/f30f8df0/.github/workflows/galaxy-deepseek-tests-impl.yaml)
*   [.github/workflows/galaxy-deepseek-tests.yaml](https://github.com/tenstorrent/tt-metal/blob/f30f8df0/.github/workflows/galaxy-deepseek-tests.yaml)
*   [.github/workflows/galaxy-demo-tests-impl.yaml](https://github.com/tenstorrent/tt-metal/blob/f30f8df0/.github/workflows/galaxy-demo-tests-impl.yaml)
*   [.github/workflows/galaxy-demo-tests.yaml](https://github.com/tenstorrent/tt-metal/blob/f30f8df0/.github/workflows/galaxy-demo-tests.yaml)
*   [.github/workflows/galaxy-profiler-tests.yaml](https://github.com/tenstorrent/tt-metal/blob/f30f8df0/.github/workflows/galaxy-profiler-tests.yaml)
*   [.github/workflows/galaxy-stress-tests-impl.yaml](https://github.com/tenstorrent/tt-metal/blob/f30f8df0/.github/workflows/galaxy-stress-tests-impl.yaml)
*   [.github/workflows/galaxy-stress-tests.yaml](https://github.com/tenstorrent/tt-metal/blob/f30f8df0/.github/workflows/galaxy-stress-tests.yaml)
*   [.github/workflows/galaxy-unit-tests-impl.yaml](https://github.com/tenstorrent/tt-metal/blob/f30f8df0/.github/workflows/galaxy-unit-tests-impl.yaml)
*   [.github/workflows/galaxy-unit-tests.yaml](https://github.com/tenstorrent/tt-metal/blob/f30f8df0/.github/workflows/galaxy-unit-tests.yaml)
*   [.github/workflows/merge-gate.yaml](https://github.com/tenstorrent/tt-metal/blob/f30f8df0/.github/workflows/merge-gate.yaml)
*   [.github/workflows/metal-run-microbenchmarks.yaml](https://github.com/tenstorrent/tt-metal/blob/f30f8df0/.github/workflows/metal-run-microbenchmarks.yaml)
*   [.github/workflows/perf-device-models-impl.yaml](https://github.com/tenstorrent/tt-metal/blob/f30f8df0/.github/workflows/perf-device-models-impl.yaml)
*   [.github/workflows/perf-device-models.yaml](https://github.com/tenstorrent/tt-metal/blob/f30f8df0/.github/workflows/perf-device-models.yaml)
*   [.github/workflows/perf-models-impl.yaml](https://github.com/tenstorrent/tt-metal/blob/f30f8df0/.github/workflows/perf-models-impl.yaml)
*   [.github/workflows/perf-models.yaml](https://github.com/tenstorrent/tt-metal/blob/f30f8df0/.github/workflows/perf-models.yaml)
*   [.github/workflows/pipeline-select-galaxy.yaml](https://github.com/tenstorrent/tt-metal/blob/f30f8df0/.github/workflows/pipeline-select-galaxy.yaml)
*   [.github/workflows/pipeline-select-t3k.yaml](https://github.com/tenstorrent/tt-metal/blob/f30f8df0/.github/workflows/pipeline-select-t3k.yaml)
*   [.github/workflows/pipeline-select.yaml](https://github.com/tenstorrent/tt-metal/blob/f30f8df0/.github/workflows/pipeline-select.yaml)
*   [.github/workflows/pr-gate.yaml](https://github.com/tenstorrent/tt-metal/blob/f30f8df0/.github/workflows/pr-gate.yaml)
*   [.github/workflows/sdk-examples.yaml](https://github.com/tenstorrent/tt-metal/blob/f30f8df0/.github/workflows/sdk-examples.yaml)
*   [.github/workflows/single-card-demo-tests-impl.yaml](https://github.com/tenstorrent/tt-metal/blob/f30f8df0/.github/workflows/single-card-demo-tests-impl.yaml)
*   [.github/workflows/single-card-demo-tests.yaml](https://github.com/tenstorrent/tt-metal/blob/f30f8df0/.github/workflows/single-card-demo-tests.yaml)
*   [.github/workflows/smoke.yaml](https://github.com/tenstorrent/tt-metal/blob/f30f8df0/.github/workflows/smoke.yaml)
*   [.github/workflows/t3000-demo-tests-impl.yaml](https://github.com/tenstorrent/tt-metal/blob/f30f8df0/.github/workflows/t3000-demo-tests-impl.yaml)
*   [.github/workflows/t3000-demo-tests.yaml](https://github.com/tenstorrent/tt-metal/blob/f30f8df0/.github/workflows/t3000-demo-tests.yaml)
*   [.github/workflows/t3000-e2e-tests.yaml](https://github.com/tenstorrent/tt-metal/blob/f30f8df0/.github/workflows/t3000-e2e-tests.yaml)
*   [.github/workflows/t3000-integration-tests.yaml](https://github.com/tenstorrent/tt-metal/blob/f30f8df0/.github/workflows/t3000-integration-tests.yaml)
*   [.github/workflows/t3000-perf-tests.yaml](https://github.com/tenstorrent/tt-metal/blob/f30f8df0/.github/workflows/t3000-perf-tests.yaml)
*   [.github/workflows/t3000-profiler-tests-impl.yaml](https://github.com/tenstorrent/tt-metal/blob/f30f8df0/.github/workflows/t3000-profiler-tests-impl.yaml)
*   [.github/workflows/t3000-profiler-tests.yaml](https://github.com/tenstorrent/tt-metal/blob/f30f8df0/.github/workflows/t3000-profiler-tests.yaml)
*   [.github/workflows/t3000-unit-tests-impl.yaml](https://github.com/tenstorrent/tt-metal/blob/f30f8df0/.github/workflows/t3000-unit-tests-impl.yaml)
*   [.github/workflows/t3000-unit-tests.yaml](https://github.com/tenstorrent/tt-metal/blob/f30f8df0/.github/workflows/t3000-unit-tests.yaml)
*   [.github/workflows/test-dispatch.yaml](https://github.com/tenstorrent/tt-metal/blob/f30f8df0/.github/workflows/test-dispatch.yaml)
*   [.github/workflows/ttsim.yaml](https://github.com/tenstorrent/tt-metal/blob/f30f8df0/.github/workflows/ttsim.yaml)
*   [CMakeLists.txt](https://github.com/tenstorrent/tt-metal/blob/f30f8df0/CMakeLists.txt)
*   [INSTALLING.md](https://github.com/tenstorrent/tt-metal/blob/f30f8df0/INSTALLING.md?plain=1)
*   [MANIFEST.in](https://github.com/tenstorrent/tt-metal/blob/f30f8df0/MANIFEST.in)
*   [build_metal.sh](https://github.com/tenstorrent/tt-metal/blob/f30f8df0/build_metal.sh)
*   [cmake/linking.cmake](https://github.com/tenstorrent/tt-metal/blob/f30f8df0/cmake/linking.cmake)
*   [cmake/project_options.cmake](https://github.com/tenstorrent/tt-metal/blob/f30f8df0/cmake/project_options.cmake)
*   [create_venv.sh](https://github.com/tenstorrent/tt-metal/blob/f30f8df0/create_venv.sh)
*   [dockerfile/Dockerfile](https://github.com/tenstorrent/tt-metal/blob/f30f8df0/dockerfile/Dockerfile)
*   [dockerfile/Dockerfile.basic-dev](https://github.com/tenstorrent/tt-metal/blob/f30f8df0/dockerfile/Dockerfile.basic-dev)
*   [dockerfile/Dockerfile.evaluation](https://github.com/tenstorrent/tt-metal/blob/f30f8df0/dockerfile/Dockerfile.evaluation)
*   [dockerfile/Dockerfile.tools](https://github.com/tenstorrent/tt-metal/blob/f30f8df0/dockerfile/Dockerfile.tools)
*   [dockerfile/scripts/install-ccache.sh](https://github.com/tenstorrent/tt-metal/blob/f30f8df0/dockerfile/scripts/install-ccache.sh)
*   [docs/source/tt-metalium/tools/triage.rst](https://github.com/tenstorrent/tt-metal/blob/f30f8df0/docs/source/tt-metalium/tools/triage.rst)
*   [install_dependencies.sh](https://github.com/tenstorrent/tt-metal/blob/f30f8df0/install_dependencies.sh)
*   [models/demos/deepseek_v3/tests/fused_op_unit_tests/mla/test_ds_mla.py](https://github.com/tenstorrent/tt-metal/blob/f30f8df0/models/demos/deepseek_v3/tests/fused_op_unit_tests/mla/test_ds_mla.py)
*   [models/demos/deepseek_v3/tests/fused_op_unit_tests/moe/test_ds_moe.py](https://github.com/tenstorrent/tt-metal/blob/f30f8df0/models/demos/deepseek_v3/tests/fused_op_unit_tests/moe/test_ds_moe.py)
*   [models/demos/deepseek_v3/tests/fused_op_unit_tests/run_ci_device_perf_tracy.sh](https://github.com/tenstorrent/tt-metal/blob/f30f8df0/models/demos/deepseek_v3/tests/fused_op_unit_tests/run_ci_device_perf_tracy.sh)
*   [models/demos/deepseek_v3/tests/test_compute_tg.py](https://github.com/tenstorrent/tt-metal/blob/f30f8df0/models/demos/deepseek_v3/tests/test_compute_tg.py)
*   [models/demos/deepseek_v3/tests/test_dispatch_tg.py](https://github.com/tenstorrent/tt-metal/blob/f30f8df0/models/demos/deepseek_v3/tests/test_dispatch_tg.py)
*   [models/demos/deepseek_v3/tests/test_optimized_moe_decode_block_tg.py](https://github.com/tenstorrent/tt-metal/blob/f30f8df0/models/demos/deepseek_v3/tests/test_optimized_moe_decode_block_tg.py)
*   [models/perf/merge_device_perf_results.py](https://github.com/tenstorrent/tt-metal/blob/f30f8df0/models/perf/merge_device_perf_results.py)
*   [pyproject.toml](https://github.com/tenstorrent/tt-metal/blob/f30f8df0/pyproject.toml)
*   [scripts/install-uv.sh](https://github.com/tenstorrent/tt-metal/blob/f30f8df0/scripts/install-uv.sh)
*   [scripts/install_debugger.sh](https://github.com/tenstorrent/tt-metal/blob/f30f8df0/scripts/install_debugger.sh)
*   [setup.py](https://github.com/tenstorrent/tt-metal/blob/f30f8df0/setup.py)
*   [tests/pipeline_reorg/t3k_demo_tests.yaml](https://github.com/tenstorrent/tt-metal/blob/f30f8df0/tests/pipeline_reorg/t3k_demo_tests.yaml)
*   [tests/pipeline_reorg/t3k_integration_tests.yaml](https://github.com/tenstorrent/tt-metal/blob/f30f8df0/tests/pipeline_reorg/t3k_integration_tests.yaml)
*   [tests/pipeline_reorg/t3k_perf_tests.yaml](https://github.com/tenstorrent/tt-metal/blob/f30f8df0/tests/pipeline_reorg/t3k_perf_tests.yaml)
*   [tests/pipeline_reorg/ttnn-tests.yaml](https://github.com/tenstorrent/tt-metal/blob/f30f8df0/tests/pipeline_reorg/ttnn-tests.yaml)
*   [tests/pipeline_reorg/ttsim-skip-list.yaml](https://github.com/tenstorrent/tt-metal/blob/f30f8df0/tests/pipeline_reorg/ttsim-skip-list.yaml)
*   [tests/scripts/run_python_model_tests.sh](https://github.com/tenstorrent/tt-metal/blob/f30f8df0/tests/scripts/run_python_model_tests.sh)
*   [tests/scripts/single_card/run_single_card_demo_tests.sh](https://github.com/tenstorrent/tt-metal/blob/f30f8df0/tests/scripts/single_card/run_single_card_demo_tests.sh)
*   [tests/scripts/t3000/run_t3000_demo_tests.sh](https://github.com/tenstorrent/tt-metal/blob/f30f8df0/tests/scripts/t3000/run_t3000_demo_tests.sh)
*   [tests/scripts/t3000/run_t3000_integration_tests.sh](https://github.com/tenstorrent/tt-metal/blob/f30f8df0/tests/scripts/t3000/run_t3000_integration_tests.sh)
*   [tests/scripts/t3000/run_t3000_perf_tests.sh](https://github.com/tenstorrent/tt-metal/blob/f30f8df0/tests/scripts/t3000/run_t3000_perf_tests.sh)
*   [tests/scripts/t3000/run_t3000_perplexity_tests.sh](https://github.com/tenstorrent/tt-metal/blob/f30f8df0/tests/scripts/t3000/run_t3000_perplexity_tests.sh)
*   [tests/scripts/t3000/run_t3000_unit_tests.sh](https://github.com/tenstorrent/tt-metal/blob/f30f8df0/tests/scripts/t3000/run_t3000_unit_tests.sh)
*   [tests/scripts/tg/run_tg_frequent_tests.sh](https://github.com/tenstorrent/tt-metal/blob/f30f8df0/tests/scripts/tg/run_tg_frequent_tests.sh)
*   [tests/scripts/wh_6u/run_wh_6u_profiler_tests.sh](https://github.com/tenstorrent/tt-metal/blob/f30f8df0/tests/scripts/wh_6u/run_wh_6u_profiler_tests.sh)
*   [tests/ttnn/nightly/unit_tests/operations/experimental/deepseek_prefill/test_deepseek_moe_post_combine_reduce.py](https://github.com/tenstorrent/tt-metal/blob/f30f8df0/tests/ttnn/nightly/unit_tests/operations/experimental/deepseek_prefill/test_deepseek_moe_post_combine_reduce.py)
*   [tests/ttnn/nightly/unit_tests/operations/experimental/deepseek_prefill/test_deepseek_prefill_extract.py](https://github.com/tenstorrent/tt-metal/blob/f30f8df0/tests/ttnn/nightly/unit_tests/operations/experimental/deepseek_prefill/test_deepseek_prefill_extract.py)
*   [tests/ttnn/nightly/unit_tests/operations/experimental/deepseek_prefill/test_deepseek_prefill_insert.py](https://github.com/tenstorrent/tt-metal/blob/f30f8df0/tests/ttnn/nightly/unit_tests/operations/experimental/deepseek_prefill/test_deepseek_prefill_insert.py)
*   [tests/ttnn/nightly/unit_tests/operations/experimental/deepseek_prefill/test_moe_grouped_topk.py](https://github.com/tenstorrent/tt-metal/blob/f30f8df0/tests/ttnn/nightly/unit_tests/operations/experimental/deepseek_prefill/test_moe_grouped_topk.py)
*   [tests/ttnn/nightly/unit_tests/operations/experimental/deepseek_prefill/test_single_routed_expert.py](https://github.com/tenstorrent/tt-metal/blob/f30f8df0/tests/ttnn/nightly/unit_tests/operations/experimental/deepseek_prefill/test_single_routed_expert.py)
*   [tt_metal/python_env/requirements-dev.txt](https://github.com/tenstorrent/tt-metal/blob/f30f8df0/tt_metal/python_env/requirements-dev.txt)
*   [ttnn/ttnn/download_sfpi.py](https://github.com/tenstorrent/tt-metal/blob/f30f8df0/ttnn/ttnn/download_sfpi.py)

## Purpose and Scope

This document describes the Continuous Integration/Continuous Deployment (CI/CD) and testing infrastructure for `tt-metal`. The system implements a progressive validation strategy that ensures code quality through automated builds, multi-tier testing, and hardware validation across all supported SKUs (N150, N300, P150, T3000, Galaxy, Blackhole). For information about the build system itself (CMake, Docker images, packaging), see [Build and Packaging System](https://deepwiki.com/tenstorrent/tt-metal/5-build-and-packaging-system).

The CI/CD infrastructure is built entirely on GitHub Actions and orchestrates:

*   Multi-platform builds with `ccache` and Docker layer caching.
*   Progressive quality gates (PR Gate → Merge Gate → Post-Commit).
*   Hardware test execution across diverse SKUs (Wormhole, Blackhole, T3000, Galaxy).
*   Static code analysis (`clang-tidy`, Clang Static Analyzer).
*   Performance regression detection and model accuracy validation.
*   Automated releases with version management and artifact publishing.

Sources: [.github/workflows/pr-gate.yaml 1-12](https://github.com/tenstorrent/tt-metal/blob/f30f8df0/.github/workflows/pr-gate.yaml#L1-L12)[.github/workflows/merge-gate.yaml 1-10](https://github.com/tenstorrent/tt-metal/blob/f30f8df0/.github/workflows/merge-gate.yaml#L1-L10)[.github/workflows/ttsim.yaml 1-37](https://github.com/tenstorrent/tt-metal/blob/f30f8df0/.github/workflows/ttsim.yaml#L1-L37)

* * *

## CI/CD Architecture Overview

The CI/CD system uses a hub-and-spoke architecture where orchestrator workflows call reusable implementation workflows. This design enables artifact sharing across multiple test jobs (build once, test many) and consistent test execution patterns.

### CI/CD Workflow Hierarchy

Title: CI/CD Workflow Orchestration

**Key architectural patterns:**

1.   **Build Once, Test Many**: Docker images are built once in `build-docker-images` and passed to multiple build/test jobs via tags [.github/workflows/pr-gate.yaml 89-105](https://github.com/tenstorrent/tt-metal/blob/f30f8df0/.github/workflows/pr-gate.yaml#L89-L105)
2.   **Progressive Validation**: The `PR Gate` provides fast feedback (<5 mins) [.github/workflows/pr-gate.yaml 9](https://github.com/tenstorrent/tt-metal/blob/f30f8df0/.github/workflows/pr-gate.yaml#L9-L9) while the `Merge Gate` runs more intensive checks like `TSan`[.github/workflows/merge-gate.yaml 137-153](https://github.com/tenstorrent/tt-metal/blob/f30f8df0/.github/workflows/merge-gate.yaml#L137-L153)
3.   **Hardware-Specific Runners**: Self-hosted runners are selected based on SKU requirements, such as `tt-ubuntu-2204-n300-stable` or `tt-ubuntu-2204-p150b-stable`[.github/workflows/perf-device-models-impl.yaml 144-146](https://github.com/tenstorrent/tt-metal/blob/f30f8df0/.github/workflows/perf-device-models-impl.yaml#L144-L146)

Sources: [.github/workflows/pr-gate.yaml 89-125](https://github.com/tenstorrent/tt-metal/blob/f30f8df0/.github/workflows/pr-gate.yaml#L89-L125)[.github/workflows/merge-gate.yaml 137-160](https://github.com/tenstorrent/tt-metal/blob/f30f8df0/.github/workflows/merge-gate.yaml#L137-L160)[.github/workflows/perf-device-models-impl.yaml 142-147](https://github.com/tenstorrent/tt-metal/blob/f30f8df0/.github/workflows/perf-device-models-impl.yaml#L142-L147)

For details, see [CI/CD Architecture Overview](https://deepwiki.com/tenstorrent/tt-metal/6.1-cicd-architecture-overview).

* * *

## Workflow Orchestration and Triggers

Workflows are triggered through multiple mechanisms including `workflow_call`, `workflow_dispatch`, and `merge_group`. Concurrency is managed to avoid wasting resources on obsolete runs while protecting `main` branch builds.

**Trigger mechanisms:**

*   **Merge Group**: Used to validate code after it has passed PR reviews but before it lands on `main`[.github/workflows/merge-gate.yaml 27-30](https://github.com/tenstorrent/tt-metal/blob/f30f8df0/.github/workflows/merge-gate.yaml#L27-L30)
*   **Workflow Call**: Reusable logic for complex test setups, such as the `ttsim-integration` job [.github/workflows/ttsim.yaml 4-37](https://github.com/tenstorrent/tt-metal/blob/f30f8df0/.github/workflows/ttsim.yaml#L4-L37)
*   **Dynamic Matrix Generation**: Workflows compute the set of models or tests to run based on input parameters or changed files [.github/workflows/perf-device-models-impl.yaml 92-178](https://github.com/tenstorrent/tt-metal/blob/f30f8df0/.github/workflows/perf-device-models-impl.yaml#L92-L178)

Sources: [.github/workflows/merge-gate.yaml 27-47](https://github.com/tenstorrent/tt-metal/blob/f30f8df0/.github/workflows/merge-gate.yaml#L27-L47)[.github/workflows/ttsim.yaml 4-37](https://github.com/tenstorrent/tt-metal/blob/f30f8df0/.github/workflows/ttsim.yaml#L4-L37)[.github/workflows/perf-device-models-impl.yaml 92-178](https://github.com/tenstorrent/tt-metal/blob/f30f8df0/.github/workflows/perf-device-models-impl.yaml#L92-L178)

For details, see [Workflow Orchestration and Triggers](https://deepwiki.com/tenstorrent/tt-metal/6.2-workflow-orchestration-and-triggers).

* * *

## Build Artifact Pipeline

The build pipeline creates Docker images and binary artifacts with aggressive caching to minimize build times.

*   **Docker Build**: Deduplicates container builds across `ASan`, standard builds, and `clang-tidy`[.github/workflows/pr-gate.yaml 89-105](https://github.com/tenstorrent/tt-metal/blob/f30f8df0/.github/workflows/pr-gate.yaml#L89-L105)
*   **Artifact Distribution**: Artifacts like `.deb` packages and Python wheels are downloaded with retry logic in test jobs [.github/workflows/ttsim.yaml 80-85](https://github.com/tenstorrent/tt-metal/blob/f30f8df0/.github/workflows/ttsim.yaml#L80-L85)
*   **Sanitizer Builds**: Supports `ASan` for memory error detection in PRs [.github/workflows/pr-gate.yaml 121-135](https://github.com/tenstorrent/tt-metal/blob/f30f8df0/.github/workflows/pr-gate.yaml#L121-L135) and `TSan` for thread safety in merge gates [.github/workflows/merge-gate.yaml 137-153](https://github.com/tenstorrent/tt-metal/blob/f30f8df0/.github/workflows/merge-gate.yaml#L137-L153)

Sources: [.github/workflows/pr-gate.yaml 89-146](https://github.com/tenstorrent/tt-metal/blob/f30f8df0/.github/workflows/pr-gate.yaml#L89-L146)[.github/workflows/merge-gate.yaml 137-153](https://github.com/tenstorrent/tt-metal/blob/f30f8df0/.github/workflows/merge-gate.yaml#L137-L153)[.github/workflows/ttsim.yaml 80-85](https://github.com/tenstorrent/tt-metal/blob/f30f8df0/.github/workflows/ttsim.yaml#L80-L85)

For details, see [Build Artifact Pipeline](https://deepwiki.com/tenstorrent/tt-metal/6.3-build-artifact-pipeline).

* * *

## Test Suite Organization and Execution

Tests are organized into categories with dedicated execution scripts and patterns:

*   **Smoke Tests**: Minimum bar validation for `tt-metalium` and `tt-nn`[.github/workflows/smoke.yaml 1-50](https://github.com/tenstorrent/tt-metal/blob/f30f8df0/.github/workflows/smoke.yaml#L1-L50)
*   **Unit Tests**: C++ and Python tests for core functionality, such as `distributed_unit_tests` and `unit_tests_ttnn`[tests/scripts/t3000/run_t3000_unit_tests.sh 18-101](https://github.com/tenstorrent/tt-metal/blob/f30f8df0/tests/scripts/t3000/run_t3000_unit_tests.sh#L18-L101)
*   **Demo Tests**: Model-level validation for vision and NLP models [.github/workflows/single-card-demo-tests-impl.yaml 40-89](https://github.com/tenstorrent/tt-metal/blob/f30f8df0/.github/workflows/single-card-demo-tests-impl.yaml#L40-L89)
*   **Simulator Integration**: Validates software changes against `TTSim` to ensure compatibility without requiring physical hardware [.github/workflows/ttsim.yaml 39-146](https://github.com/tenstorrent/tt-metal/blob/f30f8df0/.github/workflows/ttsim.yaml#L39-L146)

### Test Entity Mapping

Title: Mapping Test Scripts to Code Entities

Sources: [.github/workflows/smoke.yaml 1-50](https://github.com/tenstorrent/tt-metal/blob/f30f8df0/.github/workflows/smoke.yaml#L1-L50)[tests/scripts/t3000/run_t3000_unit_tests.sh 12-117](https://github.com/tenstorrent/tt-metal/blob/f30f8df0/tests/scripts/t3000/run_t3000_unit_tests.sh#L12-L117)[.github/workflows/single-card-demo-tests-impl.yaml 40-89](https://github.com/tenstorrent/tt-metal/blob/f30f8df0/.github/workflows/single-card-demo-tests-impl.yaml#L40-L89)[tests/scripts/single_card/run_single_card_demo_tests.sh 110-114](https://github.com/tenstorrent/tt-metal/blob/f30f8df0/tests/scripts/single_card/run_single_card_demo_tests.sh#L110-L114)

For details, see [Test Suite Organization and Execution](https://deepwiki.com/tenstorrent/tt-metal/6.4-test-suite-organization-and-execution).

* * *

## Hardware Test Matrix and Runners

The CI system uses self-hosted runners mapped to specific hardware SKUs.

*   **SKU Mapping**: Runners are categorized by labels like `N150`, `N300`, `P150`, and `T3000`[.github/workflows/single-card-demo-tests-impl.yaml 48-69](https://github.com/tenstorrent/tt-metal/blob/f30f8df0/.github/workflows/single-card-demo-tests-impl.yaml#L48-L69)
*   **Environment Management**: Docker containers are configured with `OMPI_VERSION` and `SFPI` toolchains to match hardware requirements [dockerfile/Dockerfile 90-130](https://github.com/tenstorrent/tt-metal/blob/f30f8df0/dockerfile/Dockerfile#L90-L130)
*   **Simulated Hardware**: `ttsim.yaml` allows testing `wormhole_b0` and `blackhole` architectures on standard `ubuntu-latest` runners [.github/workflows/ttsim.yaml 41-53](https://github.com/tenstorrent/tt-metal/blob/f30f8df0/.github/workflows/ttsim.yaml#L41-L53)

Sources: [.github/workflows/single-card-demo-tests-impl.yaml 48-69](https://github.com/tenstorrent/tt-metal/blob/f30f8df0/.github/workflows/single-card-demo-tests-impl.yaml#L48-L69)[dockerfile/Dockerfile 90-130](https://github.com/tenstorrent/tt-metal/blob/f30f8df0/dockerfile/Dockerfile#L90-L130)[.github/workflows/ttsim.yaml 41-53](https://github.com/tenstorrent/tt-metal/blob/f30f8df0/.github/workflows/ttsim.yaml#L41-L53)

For details, see [Hardware Test Matrix and Runners](https://deepwiki.com/tenstorrent/tt-metal/6.5-hardware-test-matrix-and-runners).

* * *

## Quality Gates and Validation Strategies

The CI implements a tiered validation strategy to balance developer velocity and system stability:

*   **PR Gate**: Focuses on speed (<5 mins) and lightweight feedback [.github/workflows/pr-gate.yaml 9](https://github.com/tenstorrent/tt-metal/blob/f30f8df0/.github/workflows/pr-gate.yaml#L9-L9)
*   **Merge Gate**: Higher bar for landing on `main`, including `TSan` and static checks [.github/workflows/merge-gate.yaml 1-10](https://github.com/tenstorrent/tt-metal/blob/f30f8df0/.github/workflows/merge-gate.yaml#L1-L10)
*   **Fail-Fast**: Strategy to stop execution on first failure; typically disabled for nightly or complex matrices to ensure all tests complete [.github/workflows/ttsim.yaml 45](https://github.com/tenstorrent/tt-metal/blob/f30f8df0/.github/workflows/ttsim.yaml#L45-L45)

Sources: [.github/workflows/pr-gate.yaml 1-12](https://github.com/tenstorrent/tt-metal/blob/f30f8df0/.github/workflows/pr-gate.yaml#L1-L12)[.github/workflows/merge-gate.yaml 1-10](https://github.com/tenstorrent/tt-metal/blob/f30f8df0/.github/workflows/merge-gate.yaml#L1-L10)[.github/workflows/ttsim.yaml 45](https://github.com/tenstorrent/tt-metal/blob/f30f8df0/.github/workflows/ttsim.yaml#L45-L45)

For details, see [Quality Gates and Validation Strategies](https://deepwiki.com/tenstorrent/tt-metal/6.6-quality-gates-and-validation-strategies).

* * *

## Code Analysis and Static Checks

Static analysis ensures code quality and detects regressions early.

*   **Clang Tidy**: Integrated into the merge gate via `all-static-checks.yaml`[.github/workflows/merge-gate.yaml 56-62](https://github.com/tenstorrent/tt-metal/blob/f30f8df0/.github/workflows/merge-gate.yaml#L56-L62)
*   **Incremental vs Full Scan**: Workflows determine whether to perform a full scan (on `main` or config change) or an incremental scan of changed directories like `tt_metal` or `ttnn`[.github/workflows/code-analysis.yaml 103-200](https://github.com/tenstorrent/tt-metal/blob/f30f8df0/.github/workflows/code-analysis.yaml#L103-L200)
*   **Clang-20 Toolchain**: Default toolchain for builds to ensure modern C++ standard compliance [.github/workflows/build-artifact.yaml 39](https://github.com/tenstorrent/tt-metal/blob/f30f8df0/.github/workflows/build-artifact.yaml#L39-L39)

Sources: [.github/workflows/merge-gate.yaml 56-62](https://github.com/tenstorrent/tt-metal/blob/f30f8df0/.github/workflows/merge-gate.yaml#L56-L62)[.github/workflows/code-analysis.yaml 103-200](https://github.com/tenstorrent/tt-metal/blob/f30f8df0/.github/workflows/code-analysis.yaml#L103-L200)[.github/workflows/build-artifact.yaml 36-40](https://github.com/tenstorrent/tt-metal/blob/f30f8df0/.github/workflows/build-artifact.yaml#L36-L40)

For details, see [Code Analysis and Static Checks](https://deepwiki.com/tenstorrent/tt-metal/6.7-code-analysis-and-static-checks).

* * *

## Performance and Profiler Testing

Performance testing validates throughput and latency across different hardware generations.

*   **Device Perf Regressions**: Tracking performance across different models using `perf-device-models-impl.yaml`[.github/workflows/perf-device-models-impl.yaml 1-31](https://github.com/tenstorrent/tt-metal/blob/f30f8df0/.github/workflows/perf-device-models-impl.yaml#L1-L31)
*   **Tracy Integration**: Allows wrapping `pytest` with the Tracy profiler for operations recording via `PYTEST_CMD`[tests/scripts/single_card/run_single_card_demo_tests.sh 9-22](https://github.com/tenstorrent/tt-metal/blob/f30f8df0/tests/scripts/single_card/run_single_card_demo_tests.sh#L9-L22)
*   **Microbenchmarks**: Specific tests for fabric and routing performance [tests/scripts/t3000/run_t3000_unit_tests.sh 78-83](https://github.com/tenstorrent/tt-metal/blob/f30f8df0/tests/scripts/t3000/run_t3000_unit_tests.sh#L78-L83)

Sources: [.github/workflows/perf-device-models-impl.yaml 1-31](https://github.com/tenstorrent/tt-metal/blob/f30f8df0/.github/workflows/perf-device-models-impl.yaml#L1-L31)[tests/scripts/single_card/run_single_card_demo_tests.sh 9-22](https://github.com/tenstorrent/tt-metal/blob/f30f8df0/tests/scripts/single_card/run_single_card_demo_tests.sh#L9-L22)[tests/scripts/t3000/run_t3000_unit_tests.sh 78-83](https://github.com/tenstorrent/tt-metal/blob/f30f8df0/tests/scripts/t3000/run_t3000_unit_tests.sh#L78-L83)

For details, see [Performance and Profiler Testing](https://deepwiki.com/tenstorrent/tt-metal/6.8-performance-and-profiler-testing).

* * *

## Release Process and Publishing

The release pipeline automates the distribution of the tt-metal stack.

*   **Packaging**: Creates `.deb` packages for `tt-metalium`, `tt-metalium-jit`, `tt-metalium-dev`, and `tt-metalium-examples`[.github/workflows/ttsim.yaml 91-92](https://github.com/tenstorrent/tt-metal/blob/f30f8df0/.github/workflows/ttsim.yaml#L91-L92)
*   **Docker Multi-stage**: Optimizes release images by separating build tools from runtime dependencies [dockerfile/Dockerfile 1-22](https://github.com/tenstorrent/tt-metal/blob/f30f8df0/dockerfile/Dockerfile#L1-L22)

Sources: [.github/workflows/ttsim.yaml 86-92](https://github.com/tenstorrent/tt-metal/blob/f30f8df0/.github/workflows/ttsim.yaml#L86-L92)[dockerfile/Dockerfile 1-22](https://github.com/tenstorrent/tt-metal/blob/f30f8df0/dockerfile/Dockerfile#L1-L22)

For details, see [Release Process and Publishing](https://deepwiki.com/tenstorrent/tt-metal/6.9-release-process-and-publishing).

* * *

## Sweep Test Framework

Sweep tests provide exhaustive parameter coverage for operations.

*   **Model Trace Sweeps**: Validates models using traces, managed by dedicated workflows [.github/CODEOWNERS 23-24](https://github.com/tenstorrent/tt-metal/blob/f30f8df0/.github/CODEOWNERS#L23-L24)
*   **TTSim Sweeps**: Integration of TTNN sweeps with the simulator for architectural validation [.github/workflows/ttsim.yaml 150-188](https://github.com/tenstorrent/tt-metal/blob/f30f8df0/.github/workflows/ttsim.yaml#L150-L188)

Sources: [.github/CODEOWNERS 22-24](https://github.com/tenstorrent/tt-metal/blob/f30f8df0/.github/CODEOWNERS#L22-L24)[.github/workflows/ttsim.yaml 150-188](https://github.com/tenstorrent/tt-metal/blob/f30f8df0/.github/workflows/ttsim.yaml#L150-L188)

For details, see [Sweep Test Framework](https://deepwiki.com/tenstorrent/tt-metal/6.10-sweep-test-framework).

* * *

## Result Collection and Reporting

Test results are collected and analyzed to maintain high reliability.

*   **JUnit Reports**: Test results are stored in XML format for integration with GitHub's UI.
*   **Data Collection**: The CI pipeline includes analysis steps for sweep results [.github/CODEOWNERS 9](https://github.com/tenstorrent/tt-metal/blob/f30f8df0/.github/CODEOWNERS#L9-L9)
*   **Bug Checker**: Automated detection of known issues and bug tracking [.github/CODEOWNERS 21](https://github.com/tenstorrent/tt-metal/blob/f30f8df0/.github/CODEOWNERS#L21-L21)

Sources: [.github/CODEOWNERS 9-21](https://github.com/tenstorrent/tt-metal/blob/f30f8df0/.github/CODEOWNERS#L9-L21)

For details, see [Result Collection and Reporting](https://deepwiki.com/tenstorrent/tt-metal/6.11-result-collection-and-reporting).

* * *

## CODEOWNERS and Review Process

The `CODEOWNERS` file defines the review hierarchy for the repository.

*   **Infra Team**: `@tenstorrent/metalium-developers-infra` owns the CI/CD pipelines and `.github/` directory [.github/CODEOWNERS 8-11](https://github.com/tenstorrent/tt-metal/blob/f30f8df0/.github/CODEOWNERS#L8-L11)
*   **Core Runtime**: Subsystems like `tt_metal` and `tt_stl` have designated owners for API changes [.github/CODEOWNERS 62-77](https://github.com/tenstorrent/tt-metal/blob/f30f8df0/.github/CODEOWNERS#L62-L77)
*   **Distributed/Fabric**: Managed by specific owners for fabric and multi-device communication [.github/CODEOWNERS 71-79](https://github.com/tenstorrent/tt-metal/blob/f30f8df0/.github/CODEOWNERS#L71-L79)

Sources: [.github/CODEOWNERS 1-80](https://github.com/tenstorrent/tt-metal/blob/f30f8df0/.github/CODEOWNERS#L1-L80)

For details, see [CODEOWNERS and Review Process](https://deepwiki.com/tenstorrent/tt-metal/6.12-codeowners-and-review-process).

Dismiss
Refresh this wiki

Enter email to refresh
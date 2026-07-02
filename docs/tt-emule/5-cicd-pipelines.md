---
project: tt-emule
github: https://github.com/tenstorrent/tt-emule
deepwiki: https://deepwiki.com/tenstorrent/tt-emule/5-cicd-pipelines
---

# CI/CD Pipelines

Relevant source files
*   [.github/CODEOWNERS](https://github.com/tenstorrent/tt-emule/blob/a57cf072/.github/CODEOWNERS)
*   [.github/scripts/ci-build-mlir.sh](https://github.com/tenstorrent/tt-emule/blob/a57cf072/.github/scripts/ci-build-mlir.sh)
*   [.github/scripts/ci-build.sh](https://github.com/tenstorrent/tt-emule/blob/a57cf072/.github/scripts/ci-build.sh)
*   [.github/workflows/nightly-d2m-upstream.yml](https://github.com/tenstorrent/tt-emule/blob/a57cf072/.github/workflows/nightly-d2m-upstream.yml)
*   [.github/workflows/nightly-metal-upstream.yml](https://github.com/tenstorrent/tt-emule/blob/a57cf072/.github/workflows/nightly-metal-upstream.yml)
*   [.github/workflows/pr-d2m-regression.yml](https://github.com/tenstorrent/tt-emule/blob/a57cf072/.github/workflows/pr-d2m-regression.yml)
*   [.github/workflows/pr-metal-regression.yml](https://github.com/tenstorrent/tt-emule/blob/a57cf072/.github/workflows/pr-metal-regression.yml)
*   [include/jit_hw/api/compile_time_args.h](https://github.com/tenstorrent/tt-emule/blob/a57cf072/include/jit_hw/api/compile_time_args.h)
*   [run_d2m_regression.sh](https://github.com/tenstorrent/tt-emule/blob/a57cf072/run_d2m_regression.sh)

The `tt-emule` CI/CD infrastructure provides automated validation of the emulation layer against two primary upstream projects: `tt-metal` and `tt-mlir`. The system is built on GitHub Actions and is designed to catch regressions in both the emulation logic and the integration with Tenstorrent's software stack.

## Overview of CI Systems

The repository maintains two distinct CI tracks, each with Pull Request (PR) gates and Nightly "Upstream" runs.

1.   **Metal Regression Pipeline**: Validates `tt-emule` against the `tt-metal` repository. It runs C++ GTests (Tiers 1–6) and Python `ttnn` pytests.
2.   **D2M (Direct-to-Metal) Pipeline**: Validates the full stack from `tt-mlir` through `tt-emule`. It ensures that MLIR-generated kernels execute correctly on the emulated hardware.

### Workflow Hierarchy

The following diagram illustrates the relationship between the workflows and the external dependencies they manage.

**CI Workflow & Dependency Map**

Sources: [.github/workflows/pr-metal-regression.yml 109-113](https://github.com/tenstorrent/tt-emule/blob/a57cf072/.github/workflows/pr-metal-regression.yml#L109-L113)[.github/workflows/pr-d2m-regression.yml 48-62](https://github.com/tenstorrent/tt-emule/blob/a57cf072/.github/workflows/pr-d2m-regression.yml#L48-L62)[.github/workflows/nightly-metal-upstream.yml 44-54](https://github.com/tenstorrent/tt-emule/blob/a57cf072/.github/workflows/nightly-metal-upstream.yml#L44-L54)[.github/workflows/nightly-d2m-upstream.yml 39-59](https://github.com/tenstorrent/tt-emule/blob/a57cf072/.github/workflows/nightly-d2m-upstream.yml#L39-L59)

* * *

## Build and Test Strategy

### Build/Test Separation

To optimize runner utilization, all workflows separate the **Build** job from the **Test** jobs.

*   The Build job produces a self-contained artifact containing the compiled `tt-metal` or `tt-mlir` binaries with `tt-emule` integrated via `-DTT_METAL_USE_EMULE=ON`[.github/scripts/ci-build.sh 62](https://github.com/tenstorrent/tt-emule/blob/a57cf072/.github/scripts/ci-build.sh#L62-L62)
*   The Test jobs run in parallel across a matrix of architectures (Wormhole, Blackhole, Quasar) [.github/workflows/pr-metal-regression.yml 179-180](https://github.com/tenstorrent/tt-emule/blob/a57cf072/.github/workflows/pr-metal-regression.yml#L179-L180)

### Artifact Management

Because `actions/upload-artifact` strips Unix executable bits, the test jobs include a restoration step using `chmod +x` on the unit test binaries before execution [.github/workflows/nightly-metal-upstream.yml 139-145](https://github.com/tenstorrent/tt-emule/blob/a57cf072/.github/workflows/nightly-metal-upstream.yml#L139-L145) Artifacts also include cluster descriptors staged from the UMD submodule to ensure the environment is reproducible [.github/scripts/ci-build.sh 89-99](https://github.com/tenstorrent/tt-emule/blob/a57cf072/.github/scripts/ci-build.sh#L89-L99)

### Change Detection

The `pr-metal-regression.yml` workflow uses a `changes` job to detect if a PR only contains documentation or configuration changes. If `code == 'false'`, the heavy build and test jobs are skipped, but an `all-passed` aggregator still reports success to satisfy GitHub branch protection rules [.github/workflows/pr-metal-regression.yml 35-44](https://github.com/tenstorrent/tt-emule/blob/a57cf072/.github/workflows/pr-metal-regression.yml#L35-L44)

**Build Logic Entity Mapping**

Sources: [.github/scripts/ci-build.sh 1-20](https://github.com/tenstorrent/tt-emule/blob/a57cf072/.github/scripts/ci-build.sh#L1-L20)[.github/scripts/ci-build-mlir.sh 1-22](https://github.com/tenstorrent/tt-emule/blob/a57cf072/.github/scripts/ci-build-mlir.sh#L1-L22)[.github/workflows/nightly-metal-upstream.yml 171-187](https://github.com/tenstorrent/tt-emule/blob/a57cf072/.github/workflows/nightly-metal-upstream.yml#L171-L187)

* * *

## Metal Regression & Nightly Upstream

The Metal Regression suite is the primary gate for `tt-emule` development. It ensures that changes to the emulator do not break existing `tt-metal` functionality.

*   **PR Gate**: Runs against the SHA pinned in `tt-metal-pin.txt`. It enforces zero-tolerance for failures on Wormhole and Blackhole, while allowing a specific set of known failures on Quasar [.github/workflows/pr-metal-regression.yml 5-8](https://github.com/tenstorrent/tt-emule/blob/a57cf072/.github/workflows/pr-metal-regression.yml#L5-L8)
*   **Nightly Upstream**: Runs against the `main` branch tip of `tt-metal`. This serves as an early warning system for breaking changes in the upstream repository and warms the `ccache` for future pin updates [.github/workflows/nightly-metal-upstream.yml 5-10](https://github.com/tenstorrent/tt-emule/blob/a57cf072/.github/workflows/nightly-metal-upstream.yml#L5-L10)

For details, see [PR Metal Regression & Nightly Upstream Workflows](https://deepwiki.com/tenstorrent/tt-emule/5.1-pr-metal-regression-and-nightly-upstream-workflows).

* * *

## D2M Regression & Nightly Upstream

The D2M (Direct-to-Metal) suite validates the integration with the `tt-mlir` compiler stack.

*   **Dependency Resolution**: This pipeline uses a cascading resolution strategy. It reads `tt-mlir-pin.txt`, then curls the `CMakeLists.txt` from that specific `tt-mlir` SHA to find the corresponding `TT_METAL_VERSION` it requires [.github/workflows/pr-d2m-regression.yml 48-67](https://github.com/tenstorrent/tt-emule/blob/a57cf072/.github/workflows/pr-d2m-regression.yml#L48-L67)
*   **Environment Preparation**: Uses `ci-d2m-prepare.sh` to generate system descriptors (`.ttsys`) required by the `ttrt` (Tenstorrent Runtime) tool [.github/workflows/nightly-d2m-upstream.yml 180-199](https://github.com/tenstorrent/tt-emule/blob/a57cf072/.github/workflows/nightly-d2m-upstream.yml#L180-L199)

For details, see [PR D2M Regression & Nightly D2M Upstream Workflows](https://deepwiki.com/tenstorrent/tt-emule/5.2-pr-d2m-regression-and-nightly-d2m-upstream-workflows).

* * *

## Common CI Utilities

| Script | Purpose |
| --- | --- |
| `ci-build.sh` | Configures and builds `tt-metal` with `-DTT_METAL_USE_EMULE=ON`. [.github/scripts/ci-build.sh 57-70](https://github.com/tenstorrent/tt-emule/blob/a57cf072/.github/scripts/ci-build.sh#L57-L70) |
| `ci-build-mlir.sh` | Builds `tt-metal` then builds `tt-mlir` against that pre-built tree. [.github/scripts/ci-build-mlir.sh 119-131](https://github.com/tenstorrent/tt-emule/blob/a57cf072/.github/scripts/ci-build-mlir.sh#L119-L131) |
| `classify-results.py` | Parses GTest XML and Python test logs to determine pass/fail against allowlists. [.github/workflows/nightly-metal-upstream.yml 184-187](https://github.com/tenstorrent/tt-emule/blob/a57cf072/.github/workflows/nightly-metal-upstream.yml#L184-L187) |
| `run_d2m_regression.sh` | Orchestrates parallel pytest execution for D2M with JIT cache isolation. [run_d2m_regression.sh 119-158](https://github.com/tenstorrent/tt-emule/blob/a57cf072/run_d2m_regression.sh#L119-L158) |

Sources: [.github/scripts/ci-build.sh 1-104](https://github.com/tenstorrent/tt-emule/blob/a57cf072/.github/scripts/ci-build.sh#L1-L104)[.github/scripts/ci-build-mlir.sh 1-146](https://github.com/tenstorrent/tt-emule/blob/a57cf072/.github/scripts/ci-build-mlir.sh#L1-L146)[run_d2m_regression.sh 1-191](https://github.com/tenstorrent/tt-emule/blob/a57cf072/run_d2m_regression.sh#L1-L191)

Dismiss
Refresh this wiki

Enter email to refresh
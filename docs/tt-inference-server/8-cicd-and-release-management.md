---
project: tt-inference-server
github: https://github.com/tenstorrent/tt-inference-server
deepwiki: https://deepwiki.com/tenstorrent/tt-inference-server/8-cicd-and-release-management
---

# CI/CD and Release Management

Relevant source files
*   [.github/workflows/spdx-checker.yml](https://github.com/tenstorrent/tt-inference-server/blob/8ab207f9/.github/workflows/spdx-checker.yml)
*   [.github/workflows/test-gate.yml](https://github.com/tenstorrent/tt-inference-server/blob/8ab207f9/.github/workflows/test-gate.yml)
*   [LICENSE](https://github.com/tenstorrent/tt-inference-server/blob/8ab207f9/LICENSE)
*   [README.md](https://github.com/tenstorrent/tt-inference-server/blob/8ab207f9/README.md?plain=1)
*   [VERSION](https://github.com/tenstorrent/tt-inference-server/blob/8ab207f9/VERSION)
*   [docs/README.md](https://github.com/tenstorrent/tt-inference-server/blob/8ab207f9/docs/README.md?plain=1)
*   [docs/multihost_deployment.md](https://github.com/tenstorrent/tt-inference-server/blob/8ab207f9/docs/multihost_deployment.md?plain=1)
*   [scripts/add_spdx_header.py](https://github.com/tenstorrent/tt-inference-server/blob/8ab207f9/scripts/add_spdx_header.py)
*   [scripts/build_docker_images.py](https://github.com/tenstorrent/tt-inference-server/blob/8ab207f9/scripts/build_docker_images.py)
*   [scripts/release/update_model_spec.py](https://github.com/tenstorrent/tt-inference-server/blob/8ab207f9/scripts/release/update_model_spec.py)
*   [tests/test_build_docker_images.py](https://github.com/tenstorrent/tt-inference-server/blob/8ab207f9/tests/test_build_docker_images.py)
*   [tt-media-server/cpp_server/CLAUDE.md](https://github.com/tenstorrent/tt-inference-server/blob/8ab207f9/tt-media-server/cpp_server/CLAUDE.md?plain=1)
*   [tt-media-server/cpp_server/README.md](https://github.com/tenstorrent/tt-inference-server/blob/8ab207f9/tt-media-server/cpp_server/README.md?plain=1)
*   [tt-media-server/cpp_server/build.sh](https://github.com/tenstorrent/tt-inference-server/blob/8ab207f9/tt-media-server/cpp_server/build.sh)
*   [tt-media-server/cpp_server/include/config/runner_config.hpp](https://github.com/tenstorrent/tt-inference-server/blob/8ab207f9/tt-media-server/cpp_server/include/config/runner_config.hpp)
*   [tt-media-server/cpp_server/include/runners/llm_runner/block_manager.hpp](https://github.com/tenstorrent/tt-inference-server/blob/8ab207f9/tt-media-server/cpp_server/include/runners/llm_runner/block_manager.hpp)
*   [tt-media-server/cpp_server/src/runners/llm_runner/block_manager.cpp](https://github.com/tenstorrent/tt-inference-server/blob/8ab207f9/tt-media-server/cpp_server/src/runners/llm_runner/block_manager.cpp)
*   [tt-media-server/cpp_server/src/runners/llm_runner/mock_model_runner.cpp](https://github.com/tenstorrent/tt-inference-server/blob/8ab207f9/tt-media-server/cpp_server/src/runners/llm_runner/mock_model_runner.cpp)
*   [vllm-tt-metal/multihost_entrypoint.sh](https://github.com/tenstorrent/tt-inference-server/blob/8ab207f9/vllm-tt-metal/multihost_entrypoint.sh)
*   [vllm-tt-metal/vllm.tt-metal.src.multihost.Dockerfile](https://github.com/tenstorrent/tt-inference-server/blob/8ab207f9/vllm-tt-metal/vllm.tt-metal.src.multihost.Dockerfile)

This section provides a high-level overview of the automation frameworks that ensure code quality, maintain license compliance, and manage the lifecycle of production-ready Docker images for the `tt-inference-server`.

## Overview

The `tt-inference-server` utilizes a multi-stage CI/CD strategy designed to handle both standard software testing and hardware-specific validation. The pipeline is primarily driven by GitHub Actions and specialized Python scripts that interface with internal Tenstorrent infrastructure.

### CI/CD Workflow Architecture

**Sources:**[.github/workflows/test-gate.yml 1-181](https://github.com/tenstorrent/tt-inference-server/blob/8ab207f9/.github/workflows/test-gate.yml#L1-L181)[scripts/release/update_model_spec.py 6-29](https://github.com/tenstorrent/tt-inference-server/blob/8ab207f9/scripts/release/update_model_spec.py#L6-L29)[scripts/build_docker_images.py 1-40](https://github.com/tenstorrent/tt-inference-server/blob/8ab207f9/scripts/build_docker_images.py#L1-L40)

## CI Pipeline (test-gate.yml)

The CI pipeline is triggered on pull requests to the `main` branch and on merge groups [.github/workflows/test-gate.yml 4-7](https://github.com/tenstorrent/tt-inference-server/blob/8ab207f9/.github/workflows/test-gate.yml#L4-L7) It serves as the primary quality gate for all code contributions.

*   **Change Detection**: The `detect-changes` job uses `dorny/paths-filter` to determine if Python code, C++ server code, or dependencies have changed, optimizing resource usage by skipping irrelevant tests [.github/workflows/test-gate.yml 10-29](https://github.com/tenstorrent/tt-inference-server/blob/8ab207f9/.github/workflows/test-gate.yml#L10-L29)
*   **Static Analysis**: Includes linting and formatting checks using `ruff` (version 0.15.0) [.github/workflows/test-gate.yml 43-50](https://github.com/tenstorrent/tt-inference-server/blob/8ab207f9/.github/workflows/test-gate.yml#L43-L50)
*   **Unit Testing**: Runs Python tests via `pytest` for both the root `tests/` and the `tt-media-server/tests/` directories [.github/workflows/test-gate.yml 83-100](https://github.com/tenstorrent/tt-inference-server/blob/8ab207f9/.github/workflows/test-gate.yml#L83-L100) It generates a detailed `Test Results Summary` in the GitHub Step Summary [.github/workflows/test-gate.yml 101-181](https://github.com/tenstorrent/tt-inference-server/blob/8ab207f9/.github/workflows/test-gate.yml#L101-L181)
*   **C++ Validation**: Builds the `tt_media_server_cpp` using `build.sh`[.github/workflows/test-gate.yml 27-28](https://github.com/tenstorrent/tt-inference-server/blob/8ab207f9/.github/workflows/test-gate.yml#L27-L28) The build script manages dependencies like `Drogon` and pre-fetches tokenizers for supported models [tt-media-server/cpp_server/build.sh 121-200](https://github.com/tenstorrent/tt-inference-server/blob/8ab207f9/tt-media-server/cpp_server/build.sh#L121-L200)

For details on test coverage, C++ build gates, and specific test suites, see [CI Pipeline (test-gate.yml)](https://deepwiki.com/tenstorrent/tt-inference-server/8.1-ci-pipeline-(test-gate.yml)).

## Release Automation

The release process is a structured workflow that transitions validated models from internal nightly runs to public release artifacts. It relies on the `VERSION` file to manage the project's semantic versioning [VERSION 1](https://github.com/tenstorrent/tt-inference-server/blob/8ab207f9/VERSION#L1-L1)

### Release Workflow Entities

| Component | Role | Code Entity |
| --- | --- | --- |
| **Models CI Reader** | Parses nightly test data to find "last good" runs | `models_ci_reader.py` |
| **Spec Updater** | Syncs `model_spec.py` with validated SHAs | `update_model_spec.py` |
| **Artifact Generator** | Promotes Docker images using `crane` | `make_release_image_artifacts.py` |
| **Build Manager** | Concurrent Docker image building with resource checks | `scripts/build_docker_images.py` |

The `update_model_spec.py` script maps `perf_status` (experimental, functional, complete, top_perf) from CI results to `ModelStatusTypes` in the codebase [scripts/release/update_model_spec.py 47-55](https://github.com/tenstorrent/tt-inference-server/blob/8ab207f9/scripts/release/update_model_spec.py#L47-L55) The `build_docker_images.py` script manages concurrency by checking host RAM, disk, and CPU limits via `get_max_concurrent_builds` to prevent system exhaustion during heavy image builds [scripts/build_docker_images.py 100-155](https://github.com/tenstorrent/tt-inference-server/blob/8ab207f9/scripts/build_docker_images.py#L100-L155)

For details on image promotion and the model spec update cycle, see [Release Automation](https://deepwiki.com/tenstorrent/tt-inference-server/8.2-release-automation).

## License Compliance and Tooling

To maintain legal standards, the project enforces automated license header checks and provides developer tooling for consistent code style.

*   **SPDX Enforcement**: The `spdx-checker.yml` workflow automatically runs a script to ensure every `.py`, `.sh`, and `Dockerfile` contains the required Apache-2.0 license headers [.github/workflows/spdx-checker.yml 1-10](https://github.com/tenstorrent/tt-inference-server/blob/8ab207f9/.github/workflows/spdx-checker.yml#L1-L10)
*   **Automated Headers**: The `add_spdx_header.py` script targets specific directories including `tt-media-server`, `workflows`, and `benchmarking` to inject missing headers [scripts/add_spdx_header.py 28-49](https://github.com/tenstorrent/tt-inference-server/blob/8ab207f9/scripts/add_spdx_header.py#L28-L49)
*   **C++ Quality**: The C++ build system optionally supports `CLANG_TIDY` for static analysis during compilation, which can be enabled via the `--clang-tidy` flag in `build.sh`[tt-media-server/cpp_server/build.sh 45-48](https://github.com/tenstorrent/tt-inference-server/blob/8ab207f9/tt-media-server/cpp_server/build.sh#L45-L48)

For details on compliance scripts and local developer setup, see [License Compliance and Developer Tooling](https://deepwiki.com/tenstorrent/tt-inference-server/8.3-license-compliance-and-developer-tooling).

**Sources:**[.github/workflows/test-gate.yml 1-181](https://github.com/tenstorrent/tt-inference-server/blob/8ab207f9/.github/workflows/test-gate.yml#L1-L181)[scripts/build_docker_images.py 100-155](https://github.com/tenstorrent/tt-inference-server/blob/8ab207f9/scripts/build_docker_images.py#L100-L155)[scripts/release/update_model_spec.py 47-55](https://github.com/tenstorrent/tt-inference-server/blob/8ab207f9/scripts/release/update_model_spec.py#L47-L55)[tt-media-server/cpp_server/build.sh 45-48](https://github.com/tenstorrent/tt-inference-server/blob/8ab207f9/tt-media-server/cpp_server/build.sh#L45-L48)[scripts/add_spdx_header.py 28-49](https://github.com/tenstorrent/tt-inference-server/blob/8ab207f9/scripts/add_spdx_header.py#L28-L49)[VERSION 1](https://github.com/tenstorrent/tt-inference-server/blob/8ab207f9/VERSION#L1-L1)

Dismiss
Refresh this wiki

Enter email to refresh
---
project: tt-inference-server
github: https://github.com/tenstorrent/tt-inference-server
deepwiki: https://deepwiki.com/tenstorrent/tt-inference-server/7-testing-infrastructure
---

# Testing Infrastructure

Relevant source files
*   [.gitignore](https://github.com/tenstorrent/tt-inference-server/blob/8ab207f9/.gitignore)
*   [.pre-commit-config.yaml](https://github.com/tenstorrent/tt-inference-server/blob/8ab207f9/.pre-commit-config.yaml)
*   [docs/development.md](https://github.com/tenstorrent/tt-inference-server/blob/8ab207f9/docs/development.md?plain=1)
*   [docs/workflows_user_guide.md](https://github.com/tenstorrent/tt-inference-server/blob/8ab207f9/docs/workflows_user_guide.md?plain=1)
*   [pyproject.toml](https://github.com/tenstorrent/tt-inference-server/blob/8ab207f9/pyproject.toml)
*   [requirements-dev.txt](https://github.com/tenstorrent/tt-inference-server/blob/8ab207f9/requirements-dev.txt)
*   [scripts/hooks/pre-commit](https://github.com/tenstorrent/tt-inference-server/blob/8ab207f9/scripts/hooks/pre-commit)
*   [scripts/setup-hooks.sh](https://github.com/tenstorrent/tt-inference-server/blob/8ab207f9/scripts/setup-hooks.sh)
*   [tests/README.md](https://github.com/tenstorrent/tt-inference-server/blob/8ab207f9/tests/README.md?plain=1)
*   [tt-media-server/Dockerfile](https://github.com/tenstorrent/tt-inference-server/blob/8ab207f9/tt-media-server/Dockerfile)
*   [tt-media-server/cpp_server/tsan_suppressions.txt](https://github.com/tenstorrent/tt-inference-server/blob/8ab207f9/tt-media-server/cpp_server/tsan_suppressions.txt)
*   [tt-media-server/device_workers/worker_utils.py](https://github.com/tenstorrent/tt-inference-server/blob/8ab207f9/tt-media-server/device_workers/worker_utils.py)
*   [tt-media-server/fix_dependencies.sh](https://github.com/tenstorrent/tt-inference-server/blob/8ab207f9/tt-media-server/fix_dependencies.sh)
*   [tt-media-server/tests/test_worker_utils.py](https://github.com/tenstorrent/tt-inference-server/blob/8ab207f9/tt-media-server/tests/test_worker_utils.py)
*   [tt-media-server/utils/runner_utils.py](https://github.com/tenstorrent/tt-inference-server/blob/8ab207f9/tt-media-server/utils/runner_utils.py)
*   [tt-media-server/uv-overrides.txt](https://github.com/tenstorrent/tt-inference-server/blob/8ab207f9/tt-media-server/uv-overrides.txt)

The `tt-inference-server` utilizes a multi-layered testing strategy to ensure reliability across host-side orchestration, Python-based media services, and the high-performance C++ backend. This infrastructure spans from fast, isolated unit tests to long-running endurance stress tests on actual Tenstorrent hardware.

### Testing Layers Overview

The repository organizes tests into several distinct categories, each targeting a specific layer of the stack:

| Layer | Tooling | Focus |
| --- | --- | --- |
| **Unit & Integration** | `pytest` | Python logic, `run.py` arguments, `ModelSpec` validation, and mocked hardware flows. |
| **Server Integration** | `server_tests` | E2E API validation, multi-model concurrency, and parameter sweeps. |
| **Stress Tests** | Custom Framework | 24-hour endurance, performance target matching, and resource leak detection. |
| **C++ Tests** | `CTest` / `gtest` | LLM scheduler logic, sequence management, and C++ runner performance. |

The testing lifecycle is integrated into the developer workflow via `pre-commit` hooks and `pytest` configurations [`.pre-commit-config.yaml 1-25](https://github.com/tenstorrent/tt-inference-server/blob/8ab207f9/%60.pre-commit-config.yaml#L1-L25)

* * *

### System Testing Architecture

The following diagram illustrates how different testing components interact with the codebase entities:

**Testing Component to Code Entity Mapping**

Sources: [tests/README.md 1-51](https://github.com/tenstorrent/tt-inference-server/blob/8ab207f9/tests/README.md?plain=1#L1-L51)[tt-media-server/device_workers/worker_utils.py 12-41](https://github.com/tenstorrent/tt-inference-server/blob/8ab207f9/tt-media-server/device_workers/worker_utils.py#L12-L41)[tt-media-server/utils/runner_utils.py 24-73](https://github.com/tenstorrent/tt-inference-server/blob/8ab207f9/tt-media-server/utils/runner_utils.py#L24-L73)

* * *

### 7.1 Unit and Integration Tests

Python-based tests reside in the `/tests` and `/tt-media-server/tests` directories. These tests use `pytest` and focus on the logic of the host orchestration layer and the Python media services.

*   **Argument Parsing**: Validates `run.py` CLI logic and `RuntimeConfig` resolution in `test_run_arguments.py`[tests/README.md 54](https://github.com/tenstorrent/tt-inference-server/blob/8ab207f9/tests/README.md?plain=1#L54-L54)
*   **Worker Utilities**: Tests for `initialize_device_worker` and `setup_runner_environment` ensure environment variables like `TT_VISIBLE_DEVICES` and `TT_METAL_CACHE` are correctly set for isolated worker processes [tt-media-server/tests/test_worker_utils.py 109-140](https://github.com/tenstorrent/tt-inference-server/blob/8ab207f9/tt-media-server/tests/test_worker_utils.py#L109-L140)
*   **Mocking Strategy**: To allow testing without physical Tenstorrent hardware, the suite utilizes a comprehensive mocking strategy for `ttnn`, `config.settings`, and `telemetry`[tt-media-server/tests/test_worker_utils.py 11-45](https://github.com/tenstorrent/tt-inference-server/blob/8ab207f9/tt-media-server/tests/test_worker_utils.py#L11-L45)

For details, see [Unit and Integration Tests](https://deepwiki.com/tenstorrent/tt-inference-server/7.1-unit-and-integration-tests).

* * *

### 7.2 Server Integration Tests (server_tests)

The `server_tests` framework is designed for end-to-end validation of the running server. It uses a JSON-based configuration to define test matrices across different models and hardware targets.

*   **Test Markers**: Tests are categorized using `pytest` markers such as `load`, `param`, `eval`, and `smoke` defined in `pyproject.toml`[`pyproject.toml 26-38](https://github.com/tenstorrent/tt-inference-server/blob/8ab207f9/%60pyproject.toml#L26-L38)
*   **Hardware Targeting**: Markers like `n150`, `n300`, and `galaxy` allow the suite to target specific Tenstorrent silicon configurations [`pyproject.toml 45-49](https://github.com/tenstorrent/tt-inference-server/blob/8ab207f9/%60pyproject.toml#L45-L49)
*   **Model Coverage**: Includes specific markers for `sdxl`, `whisper`, `llm`, and `cnn` models to filter execution by modality [`pyproject.toml 51-64](https://github.com/tenstorrent/tt-inference-server/blob/8ab207f9/%60pyproject.toml#L51-L64)

For details, see [Server Integration Tests (server_tests)](https://deepwiki.com/tenstorrent/tt-inference-server/7.2-server-integration-tests-(server_tests)).

* * *

### 7.3 Stress Tests

The stress testing framework evaluates the system's stability and performance under heavy load over extended periods.

*   **Workflow**: Triggered via `python3 run.py --workflow stress_tests`[docs/workflows_user_guide.md 29-31](https://github.com/tenstorrent/tt-inference-server/blob/8ab207f9/docs/workflows_user_guide.md?plain=1#L29-L31)
*   **Performance Sweeps**: Iterates through Input Sequence Length (ISL) and Output Sequence Length (OSL) combinations to find performance bottlenecks.
*   **Stability**: Includes 24-hour "soak tests" to identify memory leaks or hardware hangs during continuous operation.

For details, see [Stress Tests](https://deepwiki.com/tenstorrent/tt-inference-server/7.3-stress-tests).

* * *

### 7.4 C++ Tests and Benchmarks

The C++ backend (`tt-media-server/cpp_server`) includes a dedicated suite of tests managed by CTest to ensure the performance and correctness of the high-speed LLM engine.

*   **Scheduler & Sequences**: Validates the core LLM engine logic including block management and request scheduling.
*   **Hardware Simulation**: Utilizes mock implementations to simulate hardware responses for rapid logic iteration.
*   **Build Integration**: C++ tests are built as part of the `builder` stage in the Dockerfile, ensuring that binary artifacts are verified before deployment [tt-media-server/Dockerfile 96-97](https://github.com/tenstorrent/tt-inference-server/blob/8ab207f9/tt-media-server/Dockerfile#L96-L97)

For details, see [C++ Tests and Benchmarks](https://deepwiki.com/tenstorrent/tt-inference-server/7.4-c++-tests-and-benchmarks).

* * *

### Developer Tooling & Quality Gates

To maintain code quality before reaching CI, the repository enforces several local checks:

*   **Pre-commit Hooks**: Automatically runs `ruff` for linting and formatting, and `pytest` for basic sanity checks [`scripts/hooks/pre-commit 68-106](https://github.com/tenstorrent/tt-inference-server/blob/8ab207f9/%60scripts/hooks/pre-commit#L68-L106)
*   **Schema Validation**: The pre-commit hook validates `models-ci-config.json` against its schema to prevent configuration errors [`scripts/hooks/pre-commit 46-61](https://github.com/tenstorrent/tt-inference-server/blob/8ab207f9/%60scripts/hooks/pre-commit#L46-L61)
*   **Environment Setup**: Developers can use `scripts/setup-hooks.sh` to install the local git hooks that leverage tools found in `.pre-commit/bin` or `python_env/bin`[`scripts/hooks/pre-commit 6-19](https://github.com/tenstorrent/tt-inference-server/blob/8ab207f9/%60scripts/hooks/pre-commit#L6-L19)

**Pre-commit Workflow**

Sources: [scripts/hooks/pre-commit 1-107](https://github.com/tenstorrent/tt-inference-server/blob/8ab207f9/scripts/hooks/pre-commit#L1-L107)[.pre-commit-config.yaml 1-25](https://github.com/tenstorrent/tt-inference-server/blob/8ab207f9/.pre-commit-config.yaml#L1-L25)[pyproject.toml 1-20](https://github.com/tenstorrent/tt-inference-server/blob/8ab207f9/pyproject.toml#L1-L20)

This wiki is featured in the [repository](https://github.com/tenstorrent/tt-inference-server/blob/main/README.md)

Dismiss
Refresh this wiki

Enter email to refresh
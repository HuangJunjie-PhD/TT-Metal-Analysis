---
project: tt-llk
github: https://github.com/tenstorrent/tt-llk
deepwiki: https://deepwiki.com/tenstorrent/tt-llk/11-contributing-guidelines
---

# Contributing Guidelines

Relevant source files
*   [.github/workflows/build-quasar.yml](https://github.com/tenstorrent/tt-llk/blob/366f58e2/.github/workflows/build-quasar.yml)
*   [.github/workflows/on-pr.yml](https://github.com/tenstorrent/tt-llk/blob/366f58e2/.github/workflows/on-pr.yml)
*   [.github/workflows/setup-and-test.yml](https://github.com/tenstorrent/tt-llk/blob/366f58e2/.github/workflows/setup-and-test.yml)
*   [.gitignore](https://github.com/tenstorrent/tt-llk/blob/366f58e2/.gitignore)
*   [tests/helpers/ld/memory.blackhole.debug.ld](https://github.com/tenstorrent/tt-llk/blob/366f58e2/tests/helpers/ld/memory.blackhole.debug.ld)
*   [tests/helpers/ld/memory.wormhole.debug.ld](https://github.com/tenstorrent/tt-llk/blob/366f58e2/tests/helpers/ld/memory.wormhole.debug.ld)
*   [tests/python_tests/conftest.py](https://github.com/tenstorrent/tt-llk/blob/366f58e2/tests/python_tests/conftest.py)
*   [tests/python_tests/helpers/test_config.py](https://github.com/tenstorrent/tt-llk/blob/366f58e2/tests/python_tests/helpers/test_config.py)
*   [tests/requirements.txt](https://github.com/tenstorrent/tt-llk/blob/366f58e2/tests/requirements.txt)
*   [tests/setup_testing_env.sh](https://github.com/tenstorrent/tt-llk/blob/366f58e2/tests/setup_testing_env.sh)
*   [tests/sfpi-info.sh](https://github.com/tenstorrent/tt-llk/blob/366f58e2/tests/sfpi-info.sh)
*   [tests/sfpi-version](https://github.com/tenstorrent/tt-llk/blob/366f58e2/tests/sfpi-version)

This page documents the contribution workflow for the tt-llk repository, including code quality standards, automated validation through pre-commit hooks and CI/CD pipelines, and coordination with the tt-metal repository where tt-llk serves as a git submodule. Contributors must follow these guidelines to ensure changes pass validation and integrate correctly with dependent systems.

For development environment setup and local test execution, see [Testing Framework](https://deepwiki.com/tenstorrent/tt-llk/8-testing-framework). For CI/CD infrastructure details, see [CI/CD Pipeline](https://deepwiki.com/tenstorrent/tt-llk/10-cicd-pipeline).

## CONTRIBUTING.md File

The repository includes a `CONTRIBUTING.md` file that provides quick-start guidance for contributors. This markdown file is automatically referenced in PR comments via [.github/workflows/pre-commit.yml 67](https://github.com/tenstorrent/tt-llk/blob/366f58e2/.github/workflows/pre-commit.yml#L67-L67):

```
📖 For more information, please refer to our
<FileRef file-url="https://github.com/tenstorrent/tt-llk/blob/366f58e2/CONTRIBUTING" undefined  file-path="CONTRIBUTING">Hii</FileRef> guide.
```

The `CONTRIBUTING.md` file should contain:

*   Repository structure overview
*   Local setup instructions referencing `setup_external_testing_env.sh`
*   Pre-commit hook installation steps
*   Test execution examples
*   Label application guidelines for `metal-post-commit-tests`
*   Common contribution patterns

This wiki page provides comprehensive technical details beyond the quick-start guide.

**Sources:**[.github/workflows/pre-commit.yml 60-67](https://github.com/tenstorrent/tt-llk/blob/366f58e2/.github/workflows/pre-commit.yml#L60-L67)

* * *

## Contribution Workflow Overview

The tt-llk repository implements a multi-stage validation pipeline that ensures code quality, correctness, and compatibility with the tt-metal repository. The workflow progresses through local validation, automated CI checks, and cross-repository integration testing.

**Diagram: Complete Contribution Lifecycle with Code Entity Flow**

The workflow enforces quality gates at each stage:

*   **Pre-commit Hooks:** Execute `.pre-commit-config.yaml` rules via `pre-commit/action@v3.0.1`
*   **Change Detection:**`tj-actions/changed-files@v47` analyzes file paths
*   **Test Execution:**`setup-and-test.yml` reusable workflow orchestrates compilation and execution
*   **Metal Integration:**`trigger-tt-metal-post-commit.yml` dispatches to `test-llk-metal-integration.yaml`

**Sources:**[.github/workflows/on-pr.yml 1-224](https://github.com/tenstorrent/tt-llk/blob/366f58e2/.github/workflows/on-pr.yml#L1-L224)[tests/setup_testing_env.sh 1-252](https://github.com/tenstorrent/tt-llk/blob/366f58e2/tests/setup_testing_env.sh#L1-L252)

* * *

## Pre-commit Hooks and Code Quality

The repository enforces code quality through automated pre-commit hooks that run locally and in CI. These hooks prevent malformed commits from entering the codebase and maintain consistent code formatting across contributors.

### Pre-commit Configuration

The `.pre-commit-config.yaml` file defines validation rules executed on every commit. The configuration is enforced both locally via git hooks and in CI via [.github/workflows/pre-commit.yml](https://github.com/tenstorrent/tt-llk/blob/366f58e2/.github/workflows/pre-commit.yml)

### Setup Script Execution

The repository provides two setup scripts for different use cases:

**Primary Setup Script** (`setup_external_testing_env.sh`):

`cd tests./setup_external_testing_env.sh`
This script orchestrates the complete development environment setup:

| Operation | Implementation | Purpose |
| --- | --- | --- |
| Dependency Check | `check_deps()` | Verifies python3, git, wget, sudo availability |
| Python Version Check | `check_python_version()` | Ensures Python 3.8 or 3.10 |
| Virtual Environment | `python3 -m venv .venv` | Isolated Python environment at [tests/setup_external_testing_env.sh 84-88](https://github.com/tenstorrent/tt-llk/blob/366f58e2/tests/setup_external_testing_env.sh#L84-L88) |
| Package Manager | `pip install uv` | Fast Python package installer at [tests/setup_external_testing_env.sh 91-93](https://github.com/tenstorrent/tt-llk/blob/366f58e2/tests/setup_external_testing_env.sh#L91-L93) |
| Dependencies | `uv pip install -r requirements.txt` | Installs test dependencies at [tests/setup_external_testing_env.sh 96-97](https://github.com/tenstorrent/tt-llk/blob/366f58e2/tests/setup_external_testing_env.sh#L96-L97) |
| SFPI & Headers | `./setup_testing_env.sh` | Delegates to internal setup at [tests/setup_external_testing_env.sh 100](https://github.com/tenstorrent/tt-llk/blob/366f58e2/tests/setup_external_testing_env.sh#L100-L100) |

**Architecture-Specific Setup** (`setup_testing_env.sh`):

The internal script (called automatically) performs:

| Operation | Function | Purpose |
| --- | --- | --- |
| Architecture Detection | `get_chip_architecture()` | Queries `tt-smi` or uses `CHIP_ARCH` environment variable |
| Header Download | `download_headers()` | Fetches architecture-specific headers from tt-metal repository |
| SFPU Files | `download_sfpu_files()` | Clones SFPU headers using sparse checkout |
| Pre-commit Setup | `setup_precommit()` | Installs pre-commit hooks for automated validation |
| SFPI Installation | Version check and download | Installs RISC-V toolchain if not present or outdated |

**Script Arguments:**

*   `--reuse`: Skip setup if virtual environment exists at [tests/setup_external_testing_env.sh 44-54](https://github.com/tenstorrent/tt-llk/blob/366f58e2/tests/setup_external_testing_env.sh#L44-L54)
*   `--clean`: Remove all setup artifacts including `.venv`, `sfpi/`, `arch.dump` at [tests/setup_external_testing_env.sh 57-62](https://github.com/tenstorrent/tt-llk/blob/366f58e2/tests/setup_external_testing_env.sh#L57-L62)

**Sources:**[tests/setup_external_testing_env.sh 1-108](https://github.com/tenstorrent/tt-llk/blob/366f58e2/tests/setup_external_testing_env.sh#L1-L108)

### Static Check Workflow

The `pre-commit.yml` workflow runs on every pull request via [.github/workflows/on-pr.yml 28-30](https://github.com/tenstorrent/tt-llk/blob/366f58e2/.github/workflows/on-pr.yml#L28-L30):

**Diagram: Pre-commit Hook Execution in Local and CI Contexts**

The pre-commit system operates in two contexts:

**Local Hook Installation:**

*   **Setup Function:**`setup_precommit()` at [tests/setup_testing_env.sh 145-170](https://github.com/tenstorrent/tt-llk/blob/366f58e2/tests/setup_testing_env.sh#L145-L170)
*   **Command:**`pre-commit install` executed in repository root
*   **Configuration:**`.pre-commit-config.yaml` defines all validation rules
*   **Execution:** Runs automatically on `git commit`

**CI Workflow:**

*   **Workflow Definition:**[.github/workflows/pre-commit.yml 1-68](https://github.com/tenstorrent/tt-llk/blob/366f58e2/.github/workflows/pre-commit.yml#L1-L68)
*   **Trigger:** Called by `on-pr.yml` at [.github/workflows/on-pr.yml 28-30](https://github.com/tenstorrent/tt-llk/blob/366f58e2/.github/workflows/on-pr.yml#L28-L30)
*   **Action:**`pre-commit/action@v3.0.1` at [.github/workflows/pre-commit.yml 27-30](https://github.com/tenstorrent/tt-llk/blob/366f58e2/.github/workflows/pre-commit.yml#L27-L30)
*   **Scope:**`--from-ref origin/main --to-ref HEAD` validates changed files only
*   **Comment Job:** Posts guidance to PRs at [.github/workflows/pre-commit.yml 32-68](https://github.com/tenstorrent/tt-llk/blob/366f58e2/.github/workflows/pre-commit.yml#L32-L68)

### Environment Setup Requirements

Contributors must configure their local environment before committing changes:

**Setup Scripts:**

The repository provides `setup_external_testing_env.sh` which orchestrates complete environment setup:

`cd tests./setup_external_testing_env.sh`
This script performs:

| Operation | Implementation | Location |
| --- | --- | --- |
| Dependency Check | `check_deps()` | [tests/setup_external_testing_env.sh 20-29](https://github.com/tenstorrent/tt-llk/blob/366f58e2/tests/setup_external_testing_env.sh#L20-L29) |
| Python Version Check | `check_python_version()` | [tests/setup_external_testing_env.sh 31-42](https://github.com/tenstorrent/tt-llk/blob/366f58e2/tests/setup_external_testing_env.sh#L31-L42) |
| Virtual Environment | `python3 -m venv .venv` | [tests/setup_external_testing_env.sh 84-88](https://github.com/tenstorrent/tt-llk/blob/366f58e2/tests/setup_external_testing_env.sh#L84-L88) |
| Package Manager | `pip install uv` | [tests/setup_external_testing_env.sh 91-93](https://github.com/tenstorrent/tt-llk/blob/366f58e2/tests/setup_external_testing_env.sh#L91-L93) |
| Dependencies | `uv pip install -r requirements.txt` | [tests/setup_external_testing_env.sh 96-97](https://github.com/tenstorrent/tt-llk/blob/366f58e2/tests/setup_external_testing_env.sh#L96-L97) |
| SFPI & Headers | `./setup_testing_env.sh` | [tests/setup_external_testing_env.sh 100](https://github.com/tenstorrent/tt-llk/blob/366f58e2/tests/setup_external_testing_env.sh#L100-L100) |

The internal `setup_testing_env.sh` (called automatically) installs:

*   Architecture-specific headers via `download_headers()`
*   SFPU files via `download_sfpu_files()`
*   Pre-commit hooks via `setup_precommit()`
*   SFPI toolchain if not present

**Script Arguments:**

*   `--reuse`: Skip setup if `.venv` exists at [tests/setup_external_testing_env.sh 44-54](https://github.com/tenstorrent/tt-llk/blob/366f58e2/tests/setup_external_testing_env.sh#L44-L54)
*   `--clean`: Remove all artifacts including `.venv`, `sfpi/`, `arch.dump` at [tests/setup_external_testing_env.sh 57-62](https://github.com/tenstorrent/tt-llk/blob/366f58e2/tests/setup_external_testing_env.sh#L57-L62)

**Sources:**[tests/setup_external_testing_env.sh 1-108](https://github.com/tenstorrent/tt-llk/blob/366f58e2/tests/setup_external_testing_env.sh#L1-L108)[tests/setup_testing_env.sh 1-252](https://github.com/tenstorrent/tt-llk/blob/366f58e2/tests/setup_testing_env.sh#L1-L252)

### Compilation Quality Standards

The test infrastructure enforces code quality through strict compilation flags configured in `TestConfig.setup_compilation_options()`:

**Warning Flags:**

The following flags are configured at [tests/python_tests/helpers/test_config.py 255](https://github.com/tenstorrent/tt-llk/blob/366f58e2/tests/python_tests/helpers/test_config.py#L255-L255):

```
-Wall -Wunused-parameter -Wfloat-equal -Wpointer-arith 
-Wnull-dereference -Wredundant-decls -Wuninitialized -Wmaybe-uninitialized
```

These flags enforce:

*   All standard warnings enabled (`-Wall`)
*   Unused parameter detection
*   Float equality comparison warnings
*   Null dereference prevention
*   Uninitialized variable detection

**Optimization and Debugging:**

*   Optimization level: `-O3` for production builds at [tests/python_tests/helpers/test_config.py 246](https://github.com/tenstorrent/tt-llk/blob/366f58e2/tests/python_tests/helpers/test_config.py#L246-L246)
*   Debug symbols: `-g` flag (default) at [tests/python_tests/helpers/test_config.py 245](https://github.com/tenstorrent/tt-llk/blob/366f58e2/tests/python_tests/helpers/test_config.py#L245-L245)
*   Fast math: `-ffast-math` enabled at [tests/python_tests/helpers/test_config.py 246](https://github.com/tenstorrent/tt-llk/blob/366f58e2/tests/python_tests/helpers/test_config.py#L246-L246)
*   No debug symbols: `--no-debug-symbols` flag available at [tests/python_tests/conftest.py 347-351](https://github.com/tenstorrent/tt-llk/blob/366f58e2/tests/python_tests/conftest.py#L347-L351)

**Coverage Builds:**

When `--coverage` flag is used at [tests/python_tests/conftest.py 316-319](https://github.com/tenstorrent/tt-llk/blob/366f58e2/tests/python_tests/conftest.py#L316-L319):

*   Additional flags: `-fprofile-arcs -ftest-coverage -fprofile-info-section` at [tests/python_tests/helpers/test_config.py 473-475](https://github.com/tenstorrent/tt-llk/blob/366f58e2/tests/python_tests/helpers/test_config.py#L473-L475)
*   Alternative memory layout: `memory.{arch}.debug.ld` at [tests/python_tests/helpers/test_config.py 476-478](https://github.com/tenstorrent/tt-llk/blob/366f58e2/tests/python_tests/helpers/test_config.py#L476-L478)
*   Coverage data extraction: `read_coverage_data_from_device()` at [tests/python_tests/helpers/test_config.py 876-901](https://github.com/tenstorrent/tt-llk/blob/366f58e2/tests/python_tests/helpers/test_config.py#L876-L901)

**Profiler Builds:**

Performance profiling via `-DLLK_PROFILER` flag at [tests/python_tests/helpers/test_config.py 481](https://github.com/tenstorrent/tt-llk/blob/366f58e2/tests/python_tests/helpers/test_config.py#L481-L481) enables:

*   Separate shared artifacts: `PROFILER_SHARED_OBJ_DIR` at [tests/python_tests/helpers/test_config.py 211-212](https://github.com/tenstorrent/tt-llk/blob/366f58e2/tests/python_tests/helpers/test_config.py#L211-L212)
*   Metadata extraction: `.profiler_meta` section at [tests/python_tests/helpers/test_config.py 857-871](https://github.com/tenstorrent/tt-llk/blob/366f58e2/tests/python_tests/helpers/test_config.py#L857-L871)
*   Cannot combine with coverage builds at [tests/python_tests/helpers/test_config.py 344-350](https://github.com/tenstorrent/tt-llk/blob/366f58e2/tests/python_tests/helpers/test_config.py#L344-L350)

**Build Mode Flags:**

The `TestConfig` class supports multiple build modes through command-line flags:

| Mode | Flag | Additional Flags | Location |
| --- | --- | --- | --- |
| Coverage | `--coverage` | `-fprofile-arcs -ftest-coverage -fprofile-info-section` | [tests/python_tests/helpers/test_config.py 473-475](https://github.com/tenstorrent/tt-llk/blob/366f58e2/tests/python_tests/helpers/test_config.py#L473-L475) |
| Profiler | `-DLLK_PROFILER` | Separate shared artifacts directory | [tests/python_tests/helpers/test_config.py 481](https://github.com/tenstorrent/tt-llk/blob/366f58e2/tests/python_tests/helpers/test_config.py#L481-L481) |
| Debug Symbols | Default | `-g` flag included | [tests/python_tests/helpers/test_config.py 245](https://github.com/tenstorrent/tt-llk/blob/366f58e2/tests/python_tests/helpers/test_config.py#L245-L245) |
| No Debug Symbols | `--no-debug-symbols` | Omits `-g` flag | [tests/python_tests/conftest.py 347-351](https://github.com/tenstorrent/tt-llk/blob/366f58e2/tests/python_tests/conftest.py#L347-L351) |
| Detailed Artifacts | `--detailed-artefacts` | `-save-temps` for intermediates | [tests/python_tests/conftest.py 333-337](https://github.com/tenstorrent/tt-llk/blob/366f58e2/tests/python_tests/conftest.py#L333-L337) |

**Note:** Coverage and profiler builds are mutually exclusive and checked at [tests/python_tests/helpers/test_config.py 344-350](https://github.com/tenstorrent/tt-llk/blob/366f58e2/tests/python_tests/helpers/test_config.py#L344-L350)

**Sources:**[tests/python_tests/helpers/test_config.py 240-481](https://github.com/tenstorrent/tt-llk/blob/366f58e2/tests/python_tests/helpers/test_config.py#L240-L481)[tests/python_tests/conftest.py 316-351](https://github.com/tenstorrent/tt-llk/blob/366f58e2/tests/python_tests/conftest.py#L316-L351)

### Docker Development Environment

The repository provides Docker images for containerized development:

**CI Image (`Dockerfile.ci`):**

*   **Base:**`tt-llk-base-ubuntu-22-04:${FROM_TAG}` at [.github/Dockerfile.ci 2](https://github.com/tenstorrent/tt-llk/blob/366f58e2/.github/Dockerfile.ci#L2-L2)
*   **Purpose:** Minimal image for automated testing in CI/CD
*   **Dependencies:** Test dependencies via `install-tests-dependencies.sh`
*   **Optimization:** Aggressive cleanup at [.github/Dockerfile.ci 13-25](https://github.com/tenstorrent/tt-llk/blob/366f58e2/.github/Dockerfile.ci#L13-L25)

**IRD Image (`Dockerfile.ird`):**

*   **Base:**`tt-metalium/ubuntu-22.04-dev-amd64:latest` at [.github/Dockerfile.ird 1](https://github.com/tenstorrent/tt-llk/blob/366f58e2/.github/Dockerfile.ird#L1-L1)
*   **Purpose:** Interactive development with debugging tools
*   **Tools:** clangd, lcov, tmux, vim, nano at [.github/Dockerfile.ird 8-23](https://github.com/tenstorrent/tt-llk/blob/366f58e2/.github/Dockerfile.ird#L8-L23)
*   **Use Case:** Remote development environments

**Image Tagging Strategy:**

Docker images use content-addressed tags via `get-docker-tag.sh`:

*   **Tag Format:**`dt-{sha256}` where hash includes Dockerfiles, requirements.txt, build scripts
*   **Hash Calculation:**[.github/scripts/get-docker-tag.sh 8-14](https://github.com/tenstorrent/tt-llk/blob/366f58e2/.github/scripts/get-docker-tag.sh#L8-L14)
*   **Purpose:** Cache invalidation only when dependencies actually change
*   **Build Workflow:**[.github/workflows/build-images.yml 1-39](https://github.com/tenstorrent/tt-llk/blob/366f58e2/.github/workflows/build-images.yml#L1-L39) builds and pushes images

**Sources:**[.github/Dockerfile.ci 1-26](https://github.com/tenstorrent/tt-llk/blob/366f58e2/.github/Dockerfile.ci#L1-L26)[.github/Dockerfile.ird 1-32](https://github.com/tenstorrent/tt-llk/blob/366f58e2/.github/Dockerfile.ird#L1-L32)[.github/scripts/get-docker-tag.sh 6-16](https://github.com/tenstorrent/tt-llk/blob/366f58e2/.github/scripts/get-docker-tag.sh#L6-L16)[.github/workflows/build-images.yml 29-38](https://github.com/tenstorrent/tt-llk/blob/366f58e2/.github/workflows/build-images.yml#L29-L38)

* * *

## Breaking Change Workflow

Changes to tt-llk that modify public interfaces may break downstream consumers in the tt-metal repository. The breaking change workflow ensures compatibility through coordinated integration testing before merge.

### When to Apply metal-post-commit-tests Label

Contributors must apply the `metal-post-commit-tests` label to pull requests that:

1.   **Modify LLK API Surfaces:**

    *   Function signatures in `llk_*.h` headers
    *   Configuration struct layouts (`pack_config_t`, `unpack_tile_descriptor_t`, `alu_config_t`)
    *   Macro definitions or constants in `ckernel_defs.h`
    *   SFPU operation interfaces in `ckernel_sfpu.h`

2.   **Change Behavioral Contracts:**

    *   Semaphore synchronization patterns
    *   Register access sequences
    *   Memory layout expectations
    *   Data format handling

3.   **Architecture-Specific Updates:**

    *   Wormhole B0 changes in `tt_llk_wormhole_b0/`
    *   Blackhole changes in `tt_llk_blackhole/`
    *   Cross-architecture interface changes

### Architecture-Specific Header Management

Headers are downloaded during setup based on detected architecture:

**Diagram: Architecture-Specific Header Download**

The download process uses stamp files to avoid redundant fetches:

*   **Wormhole:** Dual-path fallback (primary and `wormhole_b0_defines`) at [tests/setup_testing_env.sh 73-92](https://github.com/tenstorrent/tt-llk/blob/366f58e2/tests/setup_testing_env.sh#L73-L92)
*   **Blackhole:** Standard path download at [tests/setup_testing_env.sh 45-103](https://github.com/tenstorrent/tt-llk/blob/366f58e2/tests/setup_testing_env.sh#L45-L103)
*   **Quasar:** Minimal header set at [tests/setup_testing_env.sh 48-50](https://github.com/tenstorrent/tt-llk/blob/366f58e2/tests/setup_testing_env.sh#L48-L50)
*   **SFPU Files:** Sparse git clone at [tests/setup_testing_env.sh 129-139](https://github.com/tenstorrent/tt-llk/blob/366f58e2/tests/setup_testing_env.sh#L129-L139)
*   **Stamp Files:**`.headers_downloaded` and `.sfpu_downloaded` prevent re-download

**Docker-Based Development:**

The repository provides two Docker images for different use cases:

**CI Image** (`Dockerfile.ci`):

*   Base: `tt-llk-base-ubuntu-22-04:${FROM_TAG}` at [.github/Dockerfile.ci 2](https://github.com/tenstorrent/tt-llk/blob/366f58e2/.github/Dockerfile.ci#L2-L2)
*   Purpose: Minimal image for automated testing
*   Includes: Test dependencies only via [.github/scripts/install-tests-dependencies.sh 1-17](https://github.com/tenstorrent/tt-llk/blob/366f58e2/.github/scripts/install-tests-dependencies.sh#L1-L17)
*   Size optimization: Aggressive cleanup of caches and documentation at [.github/Dockerfile.ci 13-25](https://github.com/tenstorrent/tt-llk/blob/366f58e2/.github/Dockerfile.ci#L13-L25)

**IRD Image** (`Dockerfile.ird`):

*   Base: `tt-metalium/ubuntu-22.04-dev-amd64:latest` at [.github/Dockerfile.ird 1](https://github.com/tenstorrent/tt-llk/blob/366f58e2/.github/Dockerfile.ird#L1-L1)
*   Purpose: Interactive development with debugging tools
*   Includes: clangd, lcov, tmux, vim, nano at [.github/Dockerfile.ird 8-23](https://github.com/tenstorrent/tt-llk/blob/366f58e2/.github/Dockerfile.ird#L8-L23)
*   Use case: Remote development and debugging

**Image Tagging:**

Docker images use content-addressed tags via [.github/scripts/get-docker-tag.sh 1-16](https://github.com/tenstorrent/tt-llk/blob/366f58e2/.github/scripts/get-docker-tag.sh#L1-L16):

*   Tag format: `dt-{sha256}`
*   Hash includes: Dockerfiles, requirements.txt, build scripts at [.github/scripts/get-docker-tag.sh 8-14](https://github.com/tenstorrent/tt-llk/blob/366f58e2/.github/scripts/get-docker-tag.sh#L8-L14)
*   Purpose: Cache invalidation only when dependencies change

**Sources:**[tests/setup_testing_env.sh 45-139](https://github.com/tenstorrent/tt-llk/blob/366f58e2/tests/setup_testing_env.sh#L45-L139)

* * *

## Submodule Coordination with tt-metal

The tt-llk repository serves as a git submodule within the tt-metal repository. This relationship requires careful coordination when making changes to ensure compatibility with metal's build system and test infrastructure.

### Submodule Update Workflow

The typical submodule update flow in tt-metal:

**Diagram: Submodule Update and Integration Flow**

**Integration Testing Before Merge:**

To prevent breaking changes from reaching tt-llk main, the `metal-post-commit-tests` label triggers pre-merge validation:

1.   **Label Application:** Developer adds label to tt-llk PR at [.github/workflows/trigger-tt-metal-post-commit.yml 28-30](https://github.com/tenstorrent/tt-llk/blob/366f58e2/.github/workflows/trigger-tt-metal-post-commit.yml#L28-L30)
2.   **Fork Handling:** Workflow detects if PR is from fork at [.github/workflows/trigger-tt-metal-post-commit.yml 59-68](https://github.com/tenstorrent/tt-llk/blob/366f58e2/.github/workflows/trigger-tt-metal-post-commit.yml#L59-L68)
3.   **Branch Mirroring:** Fork branches cloned to internal repo at [.github/workflows/trigger-tt-metal-post-commit.yml 70-91](https://github.com/tenstorrent/tt-llk/blob/366f58e2/.github/workflows/trigger-tt-metal-post-commit.yml#L70-L91)
4.   **Change Detection:** Determines which Metal tests to run at [.github/workflows/trigger-tt-metal-post-commit.yml 163-216](https://github.com/tenstorrent/tt-llk/blob/366f58e2/.github/workflows/trigger-tt-metal-post-commit.yml#L163-L216)
5.   **Metal Dispatch:** Triggers `test-llk-metal-integration.yaml` in tt-metal at [.github/workflows/trigger-tt-metal-post-commit.yml 292-298](https://github.com/tenstorrent/tt-llk/blob/366f58e2/.github/workflows/trigger-tt-metal-post-commit.yml#L292-L298)
6.   **Result Reporting:** Posts test results as PR comment at [.github/workflows/trigger-tt-metal-post-commit.yml 528-555](https://github.com/tenstorrent/tt-llk/blob/366f58e2/.github/workflows/trigger-tt-metal-post-commit.yml#L528-L555)

**Sources:**[.github/workflows/trigger-tt-metal-post-commit.yml 1-555](https://github.com/tenstorrent/tt-llk/blob/366f58e2/.github/workflows/trigger-tt-metal-post-commit.yml#L1-L555)

### Metal Test Suite Mapping

The Metal integration tests validate different aspects based on changed files:

| Change Location | Metal Test Suites Triggered | Purpose |
| --- | --- | --- |
| `tt_llk_wormhole_b0/**` | All Post-Commit Tests C++ Post-Commit Device Perf Regressions | Full Wormhole validation |
| `tt_llk_blackhole/**` | Blackhole Post-Commit C++ Post-Commit Device Perf Regressions | Full Blackhole validation |
| Both architectures | All suites | Cross-architecture validation |

**Test Suite Execution:**

The `trigger-tt-metal-post-commit.yml` workflow dispatches to Metal's `test-llk-metal-integration.yaml` with inputs:

*   `mirrored_branch`: Source branch to test (either head branch or mirrored fork branch)
*   `run_all_post_commit`: Boolean, true if Wormhole changes detected
*   `run_blackhole_post_commit`: Boolean, true if Blackhole changes detected
*   `workflow_timeout`: 720 minutes for complete test execution

**Result Format:**

Test results are parsed at [.github/workflows/trigger-tt-metal-post-commit.yml 322-370](https://github.com/tenstorrent/tt-llk/blob/366f58e2/.github/workflows/trigger-tt-metal-post-commit.yml#L322-L370) and formatted as:

*   `success:URL` - Test passed with link to workflow run
*   `failure:URL` - Test failed with link to workflow run
*   `timeout` - Test exceeded time limit

**Sources:**[.github/workflows/trigger-tt-metal-post-commit.yml 283-555](https://github.com/tenstorrent/tt-llk/blob/366f58e2/.github/workflows/trigger-tt-metal-post-commit.yml#L283-L555)

### Coordinating Breaking Changes

For breaking changes requiring simultaneous updates:

1.   **Create tt-llk PR:** Implement LLK interface changes
2.   **Apply Label:** Add `metal-post-commit-tests` to trigger pre-merge validation
3.   **Prepare Metal PR:** Create corresponding Metal PR pointing to tt-llk branch (not yet merged)
4.   **Validate Integration:** Ensure Metal tests pass with tt-llk changes
5.   **Merge Sequence:**
    *   Merge tt-llk PR to main
    *   Update Metal PR to point to new tt-llk commit SHA
    *   Merge Metal PR to main

**Communication:**

Breaking changes require coordination via:

*   PR descriptions documenting the interface change
*   Comments on both tt-llk and Metal PRs linking them together
*   Slack channels for urgent coordination
*   Issue tracking for planned breaking changes

**Sources:**[.github/workflows/trigger-tt-metal-post-commit.yml 1-555](https://github.com/tenstorrent/tt-llk/blob/366f58e2/.github/workflows/trigger-tt-metal-post-commit.yml#L1-L555)

* * *

## Pull Request Validation Pipeline

When a pull request is opened or updated, the `on-pr.yml` workflow orchestrates comprehensive validation. The pipeline uses change detection to run only necessary tests, optimizing CI resource usage while maintaining correctness guarantees.

### Change Detection and Auto-Labeling

The `detect-changes` job analyzes modified files to determine which architecture-specific tests must run:

**Diagram: Change Detection and Auto-Labeling**

The implementation uses GitHub Actions' `changed-files` action:

*   **File Path Mapping:**[.github/workflows/on-pr.yml 54-75](https://github.com/tenstorrent/tt-llk/blob/366f58e2/.github/workflows/on-pr.yml#L54-L75) defines YAML paths for each architecture
*   **Output Variables:**`wormhole_any_changed`, `blackhole_any_changed`, etc. control downstream jobs
*   **Label Management:**[.github/workflows/on-pr.yml 86-136](https://github.com/tenstorrent/tt-llk/blob/366f58e2/.github/workflows/on-pr.yml#L86-L136) maintains consistent labeling
*   **Managed Set:** Seven labels automatically applied/removed based on changes

**Sources:**[.github/workflows/on-pr.yml 32-136](https://github.com/tenstorrent/tt-llk/blob/366f58e2/.github/workflows/on-pr.yml#L32-L136)

### Conditional Test Execution

Test jobs use change detection outputs to conditionally execute:

**Diagram: Conditional Test Execution Based on Changes**

The conditional logic appears in job definitions:

*   **Wormhole Tests:**[.github/workflows/on-pr.yml 148-157](https://github.com/tenstorrent/tt-llk/blob/366f58e2/.github/workflows/on-pr.yml#L148-L157) runs if `wormhole`, `tests`, or `github` changed
*   **Blackhole Tests:**[.github/workflows/on-pr.yml 163-172](https://github.com/tenstorrent/tt-llk/blob/366f58e2/.github/workflows/on-pr.yml#L163-L172) uses same pattern for BH architecture
*   **Performance Tests:**[.github/workflows/on-pr.yml 190-205](https://github.com/tenstorrent/tt-llk/blob/366f58e2/.github/workflows/on-pr.yml#L190-L205) requires `performance=true`
*   **Quasar Build:**[.github/workflows/on-pr.yml 178-184](https://github.com/tenstorrent/tt-llk/blob/366f58e2/.github/workflows/on-pr.yml#L178-L184) requires `quasar=true`
*   **Skip Mechanism:**`allowed-skips` parameter at [.github/workflows/on-pr.yml 223](https://github.com/tenstorrent/tt-llk/blob/366f58e2/.github/workflows/on-pr.yml#L223-L223) permits skipped jobs

**Sources:**[.github/workflows/on-pr.yml 144-223](https://github.com/tenstorrent/tt-llk/blob/366f58e2/.github/workflows/on-pr.yml#L144-L223)

### Test Splitting and Parallel Execution

The `setup-and-test.yml` reusable workflow implements test parallelization with duration-based balancing:

**Test Configuration Matrix:**

| Parameter | Wormhole PR | Blackhole PR | Nightly | Performance |
| --- | --- | --- | --- | --- |
| `test_splits` | 2 | 2 | 10 | 5 |
| `pytest_markers` | `not perf and not nightly and not quasar` | `not perf and not nightly and not quasar` | `not perf` | `perf` |
| `timeout_minutes` | 80 | 80 | 360 | 240 |
| `use_durations` | true | true | true | true |
| `coverage` | false | false | true | false |

**Splitting Mechanism:**

Duration-based balancing uses historical test timings to distribute work evenly:

*   **Matrix Generation:**[.github/workflows/setup-and-test.yml 49-57](https://github.com/tenstorrent/tt-llk/blob/366f58e2/.github/workflows/setup-and-test.yml#L49-L57) creates test group array
*   **Duration Download:**[.github/workflows/setup-and-test.yml 86-98](https://github.com/tenstorrent/tt-llk/blob/366f58e2/.github/workflows/setup-and-test.yml#L86-L98) fetches `.test_durations` file from main branch
*   **SFPI Installation:**[.github/workflows/setup-and-test.yml 77-82](https://github.com/tenstorrent/tt-llk/blob/366f58e2/.github/workflows/setup-and-test.yml#L77-L82) runs `./setup_testing_env.sh`
*   **Compilation Phase:**[.github/workflows/setup-and-test.yml 131-136](https://github.com/tenstorrent/tt-llk/blob/366f58e2/.github/workflows/setup-and-test.yml#L131-L136) runs with `--compile-producer -n 10`
*   **Execution Phase:**[.github/workflows/setup-and-test.yml 139-144](https://github.com/tenstorrent/tt-llk/blob/366f58e2/.github/workflows/setup-and-test.yml#L139-L144) runs with `--compile-consumer -x`
*   **Duration Path:**`--durations-path=.test_durations` enables balanced splitting at [.github/workflows/setup-and-test.yml 124](https://github.com/tenstorrent/tt-llk/blob/366f58e2/.github/workflows/setup-and-test.yml#L124-L124)
*   **Report Merging:**[.github/workflows/setup-and-test.yml 146-148](https://github.com/tenstorrent/tt-llk/blob/366f58e2/.github/workflows/setup-and-test.yml#L146-L148) combines compile and run reports via junitparser

**Quasar Build-Only Workflow:**

Quasar architecture has a separate compile-only workflow at [.github/workflows/build-quasar.yml 1-107](https://github.com/tenstorrent/tt-llk/blob/366f58e2/.github/workflows/build-quasar.yml#L1-L107):

*   No execution: Only validates compilation succeeds
*   Marker: `pytest_markers: "quasar"` at [.github/workflows/build-quasar.yml 84](https://github.com/tenstorrent/tt-llk/blob/366f58e2/.github/workflows/build-quasar.yml#L84-L84)
*   Higher parallelism: `-n 40` workers at [.github/workflows/build-quasar.yml 81](https://github.com/tenstorrent/tt-llk/blob/366f58e2/.github/workflows/build-quasar.yml#L81-L81)
*   Purpose: Validate Quasar code compiles without hardware execution

**Quasar Build-Only Workflow:**

Quasar architecture uses a separate compile-only workflow at [.github/workflows/build-quasar.yml 1-107](https://github.com/tenstorrent/tt-llk/blob/366f58e2/.github/workflows/build-quasar.yml#L1-L107):

*   **No Execution:** Only validates compilation succeeds
*   **Marker:**`pytest_markers: "quasar"` at [.github/workflows/build-quasar.yml 84](https://github.com/tenstorrent/tt-llk/blob/366f58e2/.github/workflows/build-quasar.yml#L84-L84)
*   **High Parallelism:**`-n 40` pytest workers at [.github/workflows/build-quasar.yml 81](https://github.com/tenstorrent/tt-llk/blob/366f58e2/.github/workflows/build-quasar.yml#L81-L81)
*   **Purpose:** Fast validation that Quasar code compiles without requiring hardware

**Sources:**[.github/workflows/setup-and-test.yml 1-260](https://github.com/tenstorrent/tt-llk/blob/366f58e2/.github/workflows/setup-and-test.yml#L1-L260)[.github/workflows/on-pr.yml 152-172](https://github.com/tenstorrent/tt-llk/blob/366f58e2/.github/workflows/on-pr.yml#L152-L172)[.github/workflows/build-quasar.yml 44-107](https://github.com/tenstorrent/tt-llk/blob/366f58e2/.github/workflows/build-quasar.yml#L44-L107)

* * *

## Nightly and Scheduled Workflows

The repository runs comprehensive validation on scheduled intervals to collect metrics and catch regressions:

### Nightly Test Execution

The `nightly.yml` workflow runs at 1 AM UTC via cron trigger:

**Diagram: Nightly Workflow Execution and Reporting**

The nightly workflow differs from PR tests:

*   **Coverage Enabled:**[.github/workflows/nightly.yml 37-38](https://github.com/tenstorrent/tt-llk/blob/366f58e2/.github/workflows/nightly.yml#L37-L38) sets `coverage: true` flag
*   **Higher Splits:** 10 parallel groups vs. 2 for PRs at [.github/workflows/nightly.yml 33](https://github.com/tenstorrent/tt-llk/blob/366f58e2/.github/workflows/nightly.yml#L33-L33)
*   **Longer Timeout:** 360 minutes vs. 80 for PRs at [.github/workflows/nightly.yml 36](https://github.com/tenstorrent/tt-llk/blob/366f58e2/.github/workflows/nightly.yml#L36-L36)
*   **Merge Job:**[.github/workflows/setup-and-test.yml 202-260](https://github.com/tenstorrent/tt-llk/blob/366f58e2/.github/workflows/setup-and-test.yml#L202-L260) combines coverage files
*   **Slack Notification:**[.github/actions/report-failure/action.yml 1-71](https://github.com/tenstorrent/tt-llk/blob/366f58e2/.github/actions/report-failure/action.yml#L1-L71) sends status updates

**Nightly Schedule:**[.github/workflows/nightly.yml 6](https://github.com/tenstorrent/tt-llk/blob/366f58e2/.github/workflows/nightly.yml#L6-L6) defines cron trigger `0 1 * * *`

**Sources:**[.github/workflows/nightly.yml 1-85](https://github.com/tenstorrent/tt-llk/blob/366f58e2/.github/workflows/nightly.yml#L1-L85)[.github/workflows/setup-and-test.yml 202-260](https://github.com/tenstorrent/tt-llk/blob/366f58e2/.github/workflows/setup-and-test.yml#L202-L260)

### Performance and Duration Collection

**Performance Tests:**

*   **Schedule:** 3 AM UTC via [.github/workflows/nightly-perf-tests.yml 5-6](https://github.com/tenstorrent/tt-llk/blob/366f58e2/.github/workflows/nightly-perf-tests.yml#L5-L6)
*   **Workflow:**`run-perf-tests.yml` reusable workflow
*   **Markers:**`pytest_markers: "perf"` selects benchmarks
*   **Splits:** 5 parallel groups per architecture
*   **Timeout:** 240 minutes

**Test Duration Collection:**

*   **Schedule:** Sunday 2 AM UTC via [.github/workflows/collect-test-durations.yml 6](https://github.com/tenstorrent/tt-llk/blob/366f58e2/.github/workflows/collect-test-durations.yml#L6-L6)
*   **Purpose:** Update `.test_durations` file for balanced splitting

**Diagram: Test Duration Collection and Usage**

The collection workflow optimizes test splitting:

*   **Workflow:**[.github/workflows/collect-test-durations.yml 1-136](https://github.com/tenstorrent/tt-llk/blob/366f58e2/.github/workflows/collect-test-durations.yml#L1-L136) runs weekly
*   **Compilation:**[.github/workflows/collect-test-durations.yml 46-52](https://github.com/tenstorrent/tt-llk/blob/366f58e2/.github/workflows/collect-test-durations.yml#L46-L52) compiles all tests with duration tracking
*   **Artifact Upload:**[.github/workflows/collect-test-durations.yml 54-61](https://github.com/tenstorrent/tt-llk/blob/366f58e2/.github/workflows/collect-test-durations.yml#L54-L61) stores `.test_durations` file
*   **PR Download:**[.github/workflows/setup-and-test.yml 86-98](https://github.com/tenstorrent/tt-llk/blob/366f58e2/.github/workflows/setup-and-test.yml#L86-L98) fetches latest durations
*   **Usage:**[.github/workflows/setup-and-test.yml 122-128](https://github.com/tenstorrent/tt-llk/blob/366f58e2/.github/workflows/setup-and-test.yml#L122-L128) passes `--durations-path` to pytest

**Sources:**[.github/workflows/collect-test-durations.yml 1-136](https://github.com/tenstorrent/tt-llk/blob/366f58e2/.github/workflows/collect-test-durations.yml#L1-L136)[.github/workflows/nightly-perf-tests.yml 1-38](https://github.com/tenstorrent/tt-llk/blob/366f58e2/.github/workflows/nightly-perf-tests.yml#L1-L38)[.github/workflows/setup-and-test.yml 86-98](https://github.com/tenstorrent/tt-llk/blob/366f58e2/.github/workflows/setup-and-test.yml#L86-L98)

* * *

## Contribution Checklist

Before submitting a pull request, ensure the following requirements are met:

### Pre-Submission Checklist

*   - [x] **Environment Setup:** Run `./tests/setup_external_testing_env.sh` to create virtual environment and install SFPI
*   - [x] **Pre-commit Passing:** All local pre-commit hooks pass without errors
*   - [x] **Tests Added:** New functionality includes corresponding test cases
*   - [x] **Architecture Support:** Changes work on all affected architectures (Wormhole, Blackhole, Quasar if applicable)
*   - [x] **Breaking Changes:** If modifying LLK API, apply `metal-post-commit-tests` label
*   - [x] **Test Markers:** Apply appropriate pytest markers (`@pytest.mark.nightly`, `@pytest.mark.perf`, `@skip_for_*`)
*   - [x] **Coverage Impact:** Run `pytest --coverage` locally to verify test coverage

### Post-Submission Validation

*   - [x] **Static Checks:**`pre-commit` job passes in CI at [.github/workflows/on-pr.yml 28-30](https://github.com/tenstorrent/tt-llk/blob/366f58e2/.github/workflows/on-pr.yml#L28-L30)
*   - [x] **Architecture Tests:**`setup-and-test-wormhole` and `setup-and-test-blackhole` jobs pass at [.github/workflows/on-pr.yml 144-172](https://github.com/tenstorrent/tt-llk/blob/366f58e2/.github/workflows/on-pr.yml#L144-L172)
*   - [x] **Quasar Build:** If Quasar changes made, `build-quasar` job passes at [.github/workflows/on-pr.yml 174-184](https://github.com/tenstorrent/tt-llk/blob/366f58e2/.github/workflows/on-pr.yml#L174-L184)
*   - [x] **Performance Tests:** If `performance` label applied, `perf-tests` job passes at [.github/workflows/on-pr.yml 186-205](https://github.com/tenstorrent/tt-llk/blob/366f58e2/.github/workflows/on-pr.yml#L186-L205)
*   - [x] **Metal Integration:** If `metal-post-commit-tests` label applied, all Metal test suites pass
*   - [x] **All Green:**`check-all-green` aggregation job succeeds at [.github/workflows/on-pr.yml 207-223](https://github.com/tenstorrent/tt-llk/blob/366f58e2/.github/workflows/on-pr.yml#L207-L223)

### Test Execution Options

Contributors can use these pytest flags for local development:

**Test Mode Flags:**

*   `--compile-producer`: Generate test artifacts without execution at [tests/python_tests/conftest.py 322-325](https://github.com/tenstorrent/tt-llk/blob/366f58e2/tests/python_tests/conftest.py#L322-L325)
*   `--compile-consumer`: Execute pre-compiled test artifacts at [tests/python_tests/conftest.py 327-331](https://github.com/tenstorrent/tt-llk/blob/366f58e2/tests/python_tests/conftest.py#L327-L331)
*   Default (no flag): Compile and execute sequentially defined at [tests/python_tests/helpers/test_config.py 71-73](https://github.com/tenstorrent/tt-llk/blob/366f58e2/tests/python_tests/helpers/test_config.py#L71-L73)

**Build Configuration:**

*   `--coverage`: Enable code coverage collection at [tests/python_tests/conftest.py 316-319](https://github.com/tenstorrent/tt-llk/blob/366f58e2/tests/python_tests/conftest.py#L316-L319)
*   `--detailed-artefacts`: Generate debug artifacts with `-save-temps` at [tests/python_tests/conftest.py 333-337](https://github.com/tenstorrent/tt-llk/blob/366f58e2/tests/python_tests/conftest.py#L333-L337)
*   `--no-debug-symbols`: Omit `-g` flag to save disk space at [tests/python_tests/conftest.py 347-351](https://github.com/tenstorrent/tt-llk/blob/366f58e2/tests/python_tests/conftest.py#L347-L351)

**Test Selection:**

*   `--run_simulator`: Execute tests on simulator instead of hardware at [tests/python_tests/conftest.py 305-306](https://github.com/tenstorrent/tt-llk/blob/366f58e2/tests/python_tests/conftest.py#L305-L306)
*   `-m "not perf"`: Skip performance tests using markers
*   `--skip-codegen`: Reuse existing C++ for fused tests at [tests/python_tests/conftest.py 340-344](https://github.com/tenstorrent/tt-llk/blob/366f58e2/tests/python_tests/conftest.py#L340-L344)

**Architecture Skip Decorators:**

*   `@skip_for_wormhole`: Skip test on Wormhole at [tests/python_tests/conftest.py 359-362](https://github.com/tenstorrent/tt-llk/blob/366f58e2/tests/python_tests/conftest.py#L359-L362)
*   `@skip_for_blackhole`: Skip test on Blackhole at [tests/python_tests/conftest.py 364-367](https://github.com/tenstorrent/tt-llk/blob/366f58e2/tests/python_tests/conftest.py#L364-L367)
*   `@skip_for_quasar`: Skip test on Quasar at [tests/python_tests/conftest.py 369-372](https://github.com/tenstorrent/tt-llk/blob/366f58e2/tests/python_tests/conftest.py#L369-L372)
*   `@skip_for_coverage`: Skip during coverage runs at [tests/python_tests/conftest.py 374-377](https://github.com/tenstorrent/tt-llk/blob/366f58e2/tests/python_tests/conftest.py#L374-L377)

### Common Issues and Solutions

| Issue | Cause | Solution |
| --- | --- | --- |
| Pre-commit fails in CI | Local hooks not installed | Run `./tests/setup_testing_env.sh` and commit again |
| Metal tests timeout | Large API changes | Coordinate with Metal team; may need staged rollout |
| Architecture tests skip | Change detection false negative | Manually trigger workflow or add architecture label |
| Coverage upload fails | Test failures prevent artifact generation | Fix test failures first; coverage collected only on success |
| Fork PR cannot trigger Metal tests | Security restriction | Maintainer will mirror branch and add label |

**Sources:**[tests/setup_testing_env.sh 144-170](https://github.com/tenstorrent/tt-llk/blob/366f58e2/tests/setup_testing_env.sh#L144-L170)[.github/workflows/on-pr.yml 1-224](https://github.com/tenstorrent/tt-llk/blob/366f58e2/.github/workflows/on-pr.yml#L1-L224)[.github/workflows/trigger-tt-metal-post-commit.yml 1-555](https://github.com/tenstorrent/tt-llk/blob/366f58e2/.github/workflows/trigger-tt-metal-post-commit.yml#L1-L555)

Dismiss
Refresh this wiki

Enter email to refresh
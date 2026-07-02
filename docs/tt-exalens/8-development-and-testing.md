---
project: tt-exalens
github: https://github.com/tenstorrent/tt-exalens
deepwiki: https://deepwiki.com/tenstorrent/tt-exalens/8-development-and-testing
---

# Development and Testing

Relevant source files
*   [.github/Dockerfile.ci](https://github.com/tenstorrent/tt-exalens/blob/046c35eb/.github/Dockerfile.ci)
*   [.gitignore](https://github.com/tenstorrent/tt-exalens/blob/046c35eb/.gitignore)
*   [CMakeLists.txt](https://github.com/tenstorrent/tt-exalens/blob/046c35eb/CMakeLists.txt)
*   [Makefile](https://github.com/tenstorrent/tt-exalens/blob/046c35eb/Makefile)
*   [README.md](https://github.com/tenstorrent/tt-exalens/blob/046c35eb/README.md?plain=1)
*   [cmake/sfpi_release.cmake](https://github.com/tenstorrent/tt-exalens/blob/046c35eb/cmake/sfpi_release.cmake)
*   [docs/gdb.md](https://github.com/tenstorrent/tt-exalens/blob/046c35eb/docs/gdb.md?plain=1)
*   [riscv-src/CMakeLists.txt](https://github.com/tenstorrent/tt-exalens/blob/046c35eb/riscv-src/CMakeLists.txt)
*   [scripts/create-venv.sh](https://github.com/tenstorrent/tt-exalens/blob/046c35eb/scripts/create-venv.sh)
*   [scripts/install-deps.sh](https://github.com/tenstorrent/tt-exalens/blob/046c35eb/scripts/install-deps.sh)
*   [scripts/setup-dev-env.sh](https://github.com/tenstorrent/tt-exalens/blob/046c35eb/scripts/setup-dev-env.sh)

This page provides an overview of the development environment, build system, testing infrastructure, and CI/CD pipeline for the TTExaLens project. It covers the tooling and workflows needed to build, test, and contribute to the codebase.

For detailed coverage of individual areas, see:

*   [Build System Architecture](https://deepwiki.com/tenstorrent/tt-exalens/8.1-build-system-architecture) — CMake, Makefile targets, SFPI toolchain, wheel packaging
*   [Testing Framework](https://deepwiki.com/tenstorrent/tt-exalens/8.2-testing-framework) — pytest configuration, simulator utilities, test structure
*   [Test Categories and Coverage](https://deepwiki.com/tenstorrent/tt-exalens/8.3-test-categories-and-coverage) — per-suite breakdown, hardware requirements
*   [CI/CD Pipeline](https://deepwiki.com/tenstorrent/tt-exalens/8.4-cicd-pipeline) — GitHub Actions workflows, Docker images, release automation
*   [Contributing Guidelines](https://deepwiki.com/tenstorrent/tt-exalens/8.5-contributing-guidelines) — pre-commit hooks, mypy, pull request process

* * *

## Development Environment

TTExaLens development requires Python 3 and a set of native build tools. The recommended approach is a virtual environment managed by the provided scripts.

### System Prerequisites

| Dependency | Purpose |
| --- | --- |
| `python3`, `pip` / `uv` | Python runtime and package management |
| `cmake` | C/C++ build configuration |
| `ninja-build` | Fast build backend used by CMake |
| `make` | Makefile task runner |
| `git` | Source control and submodule management |
| `rsync`, `gdb` | Required for remote access and RISC-V debugging |

The CI Docker image ([`.github/Dockerfile.ci`](https://github.com/tenstorrent/tt-exalens/blob/046c35eb/%60.github/Dockerfile.ci%60)) documents the full system dependency set. It installs `git`, `python3`, `python3-pip`, `ninja-build`, and `cmake` as the base system packages.

### Python Dependency Files

| File | Contents |
| --- | --- |
| `ttexalens/requirements.txt` | Runtime library dependencies |
| `ttexalens/dev-requirements.txt` | Development-only tools (mypy, pre-commit, etc.) |
| `test/test_requirements.txt` | Test runner and reporting libraries |

[`test/test_requirements.txt`](https://github.com/tenstorrent/tt-exalens/blob/046c35eb/%60test/test_requirements.txt%60) includes `pytest`, `coverage`, `parameterized`, and `unittest-xml-reporting`.

### Setting Up a Virtual Environment

The [`scripts/create-venv.sh`](https://github.com/tenstorrent/tt-exalens/blob/046c35eb/%60scripts/create-venv.sh%60) script creates a fresh `.venv` environment. The [`scripts/install-deps.sh`](https://github.com/tenstorrent/tt-exalens/blob/046c35eb/%60scripts/install-deps.sh%60) script installs all three dependency groups (runtime, dev, and test) into the current environment, preferring `uv` if available and falling back to `pip`.

```
./scripts/create-venv.sh        # create .venv
source .venv/bin/activate       # activate
./scripts/install-deps.sh       # install all deps
```

Sources: [`README.md 55-75](https://github.com/tenstorrent/tt-exalens/blob/046c35eb/%60README.md?plain=1#L55-L75)[`scripts/install-deps.sh`](https://github.com/tenstorrent/tt-exalens/blob/046c35eb/%60scripts/install-deps.sh%60)[`scripts/setup-dev-env.sh`](https://github.com/tenstorrent/tt-exalens/blob/046c35eb/%60scripts/setup-dev-env.sh%60)

* * *

## Build System Overview

The top-level [`Makefile`](https://github.com/tenstorrent/tt-exalens/blob/046c35eb/%60Makefile%60) wraps a CMake + Ninja build. The primary targets are:

| Make target | Action |
| --- | --- |
| `make build` (default) | Configures and builds with CMake + Ninja. Uses `ccache` if available. |
| `make wheel` | Builds the Python wheel via `pip wheel`. Output goes to `build/ttexalens_wheel/`. |
| `make clean` | Removes the `build/` directory. |
| `make test` | Installs dependencies and runs the full test suite via `test/run_all.sh`. |
| `make mypy` | Runs `python3 -m mypy` for static type checking. |
| `make docs` | Generates library documentation from docstrings (defined in `docs/module.mk`). |
| `make dump_elfs` | Re-runs `build` with `DUMP_ELFS=ON`. |

The default `BUILD_DIR` is `build/`, overridable via the environment variable `BUILD_DIR`.

**CMake and the SFPI Toolchain**

The CMake configuration downloads the SFPI (RISC-V Tenstorrent ELF) toolchain, which is required to compile firmware test kernels. See [Build System Architecture](https://deepwiki.com/tenstorrent/tt-exalens/8.1-build-system-architecture) for details on the `BUILD_PYTHON_WHEEL`, `STRIP_SYMBOLS`, and `DUMP_ELFS` CMake options.

**Diagram: Build Pipeline**

Sources: [`Makefile`](https://github.com/tenstorrent/tt-exalens/blob/046c35eb/%60Makefile%60)[`README.md 87-112](https://github.com/tenstorrent/tt-exalens/blob/046c35eb/%60README.md?plain=1#L87-L112)

* * *

## Testing Infrastructure Overview

Tests are organized into two top-level directories:

| Directory | Purpose |
| --- | --- |
| `test/ttexalens/` | Library unit and integration tests |
| `test/app/` | CLI application tests |
| `test/wheel/` | Smoke tests run against the installed wheel |

Tests use Python's `unittest` framework, discovered and run with `pytest` or `python3 -m unittest`. The `xmlrunner` integration is used in CI to produce JUnit-style XML reports.

**Running tests locally:**

`# Build test kernels firstmake # Install all dependencies./scripts/install-deps.sh # Run library testspytest test/ttexalens # Run CLI app testspytest test/app`
**NOC1 tests** can be activated by setting the environment variable:

`export TTEXALENS_TESTS_USE_NOC1=1pytest test/ttexalens`
NOC1 tests are disabled on `n300` boards due to a known issue ([`.github/workflows/build-and-test.yml 147-158](https://github.com/tenstorrent/tt-exalens/blob/046c35eb/%60.github/workflows/build-and-test.yml#L147-L158)).

For detailed information about test utilities such as `init_default_test_context`, `RiscvCoreSimulator`, and per-suite hardware requirements, see [Testing Framework](https://deepwiki.com/tenstorrent/tt-exalens/8.2-testing-framework) and [Test Categories and Coverage](https://deepwiki.com/tenstorrent/tt-exalens/8.3-test-categories-and-coverage).

Sources: [`README.md 75-112](https://github.com/tenstorrent/tt-exalens/blob/046c35eb/%60README.md?plain=1#L75-L112)[`.github/workflows/build-and-test.yml 131-167](https://github.com/tenstorrent/tt-exalens/blob/046c35eb/%60.github/workflows/build-and-test.yml#L131-L167)[`test/test_requirements.txt`](https://github.com/tenstorrent/tt-exalens/blob/046c35eb/%60test/test_requirements.txt%60)

* * *

## CI/CD Pipeline Overview

The CI system is built on GitHub Actions. The workflows are organized as follows:

**Diagram: GitHub Actions Workflow Dependencies**

**Hardware test matrix:**

| Architecture | Board | Runner label |
| --- | --- | --- |
| `blackhole` | `p150b` | `tt-ubuntu-2204-p150b-stable` |
| `wormhole_b0` | `n150` | `tt-ubuntu-2204-n150-stable` |
| `wormhole_b0` | `n300` | `tt-ubuntu-2204-n300-stable` |

**Docker Image Management**

The CI Docker image is built from [`.github/Dockerfile.ci`](https://github.com/tenstorrent/tt-exalens/blob/046c35eb/%60.github/Dockerfile.ci%60) The image tag is a `sha256` hash of the Dockerfile and all three dependency files, computed by [`.github/get-docker-tag.sh`](https://github.com/tenstorrent/tt-exalens/blob/046c35eb/%60.github/get-docker-tag.sh%60):

```
(cat .github/Dockerfile.ci requirements.txt dev-requirements.txt test_requirements.txt) | sha256sum
```

This ensures the image is rebuilt only when dependencies change. On `main` branch pushes, the image is also tagged as `latest` via the `set-latest` job.

The `build-ttexalens.yml` reusable workflow ([`.github/workflows/build-ttexalens.yml`](https://github.com/tenstorrent/tt-exalens/blob/046c35eb/%60.github/workflows/build-ttexalens.yml%60)) performs the following steps in order: `pre-commit`, `make mypy`, `make` (test kernels), `make wheel`, then strips the SFPI compiler from artifacts before uploading `build/` as a workflow artifact. The RISC-V GDB binary (`riscv-tt-elf-gdb`) is preserved in the stripped artifact.

For release pipeline details (PyPI publishing), see [CI/CD Pipeline](https://deepwiki.com/tenstorrent/tt-exalens/8.4-cicd-pipeline).

Sources: [`.github/workflows/build-and-test.yml`](https://github.com/tenstorrent/tt-exalens/blob/046c35eb/%60.github/workflows/build-and-test.yml%60)[`.github/workflows/build-ttexalens.yml`](https://github.com/tenstorrent/tt-exalens/blob/046c35eb/%60.github/workflows/build-ttexalens.yml%60)[`.github/workflows/on-pr.yml`](https://github.com/tenstorrent/tt-exalens/blob/046c35eb/%60.github/workflows/on-pr.yml%60)[`.github/workflows/on-push.yml`](https://github.com/tenstorrent/tt-exalens/blob/046c35eb/%60.github/workflows/on-push.yml%60)[`.github/build-docker-images.sh`](https://github.com/tenstorrent/tt-exalens/blob/046c35eb/%60.github/build-docker-images.sh%60)[`.github/get-docker-tag.sh`](https://github.com/tenstorrent/tt-exalens/blob/046c35eb/%60.github/get-docker-tag.sh%60)

* * *

## Static Analysis and Code Quality

Two static analysis tools are enforced on every pull request:

### pre-commit

Pre-commit hooks check code for formatting, license headers, and other style issues. After installing `pre-commit`, hooks run automatically at `git commit` time.

`pip install pre-commitpre-commit install          # install hooks into .git/hooks/commit-msgpre-commit run --all-files  # retroactively check all files`
The `build-ttexalens.yml` CI workflow runs `pre-commit run --all-files --show-diff-on-failure` as its first step, blocking the build if any check fails.

### mypy

mypy performs static type checking across the Python source. The `mypy` make target runs `python3 -m mypy`. Configuration is read from the project's `mypy.ini` or `pyproject.toml`. This step also runs in CI as part of `build-ttexalens.yml`.

`make mypy# or directly:python3 -m mypy`
### Documentation Generation

Library documentation is generated from docstrings using scripts in `docs/bin/`. Running `make docs` regenerates the markdown documentation files.

**Diagram: Code Quality Gates (file → tool mapping)**

Sources: [`README.md 127-163](https://github.com/tenstorrent/tt-exalens/blob/046c35eb/%60README.md?plain=1#L127-L163)[`.github/workflows/build-ttexalens.yml 41-46](https://github.com/tenstorrent/tt-exalens/blob/046c35eb/%60.github/workflows/build-ttexalens.yml#L41-L46)

Dismiss
Refresh this wiki

Enter email to refresh

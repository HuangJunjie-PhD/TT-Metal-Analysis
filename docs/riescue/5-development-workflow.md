---
project: riescue
github: https://github.com/tenstorrent/riescue
deepwiki: https://deepwiki.com/tenstorrent/riescue/5-development-workflow
---

# Development Workflow

Relevant source files
*   [.github/CODE_OF_CONDUCT.md](https://github.com/tenstorrent/riescue/blob/07cefde3/.github/CODE_OF_CONDUCT.md?plain=1)
*   [.github/CONTRIBUTING.md](https://github.com/tenstorrent/riescue/blob/07cefde3/.github/CONTRIBUTING.md?plain=1)
*   [.github/ISSUE_TEMPLATE/pull_request_template.md](https://github.com/tenstorrent/riescue/blob/07cefde3/.github/ISSUE_TEMPLATE/pull_request_template.md?plain=1)
*   [.github/workflows/smoke.yml](https://github.com/tenstorrent/riescue/blob/07cefde3/.github/workflows/smoke.yml)
*   [docs/images/Roadmap.png](https://github.com/tenstorrent/riescue/blob/07cefde3/docs/images/Roadmap.png)
*   [infra/build_base_deps.sh](https://github.com/tenstorrent/riescue/blob/07cefde3/infra/build_base_deps.sh)

## Purpose and Scope

This page documents the development workflow for contributing to RiESCUE, including code quality standards, testing requirements, and the CI/CD pipeline. It covers the processes from initial development through code review to deployment.

For information about the container-based development environment and build system, see [Development Environment](https://deepwiki.com/tenstorrent/riescue/3-development-environment). For details on testing framework internals, see [Test Generation Internals](https://deepwiki.com/tenstorrent/riescue/7.1-test-generation-internals).

* * *

## Contributing to RiESCUE

### Overview

All contributions to RiESCUE follow a standardized workflow that ensures code quality, consistency, and proper review. The contribution process is enforced through GitHub workflows and requires adherence to coding standards.

**Key Requirements:**

*   All contributions require an associated GitHub issue
*   Pull requests must link to the issue they address
*   Code must pass all automated checks (linting, type checking, tests)
*   Changes must include appropriate tests and documentation
*   Code must follow Python 3.9 standards with type hints

**Development Flow Diagram:**

Sources: [.github/CONTRIBUTING.md 1-72](https://github.com/tenstorrent/riescue/blob/07cefde3/.github/CONTRIBUTING.md?plain=1#L1-L72)

### Issue and Pull Request Templates

#### Issue Creation

Issues must be filed before creating pull requests. The repository provides templates for:

*   **Bug Reports**: Structured format for reporting defects
*   **Feature Requests**: Proposals for new functionality
*   **Support Requests**: Help with using RiESCUE

When opening an issue, select the appropriate template and fill in all required fields.

#### Pull Request Template Structure

Pull requests must use the provided template and include:

| Section | Required Content |
| --- | --- |
| **Description** | Clear explanation of changes |
| **Related Issues** | Link using `#issue_number` syntax |
| **Type of Change** | Bug fix, Feature, Documentation, Refactoring, Performance, Tests |
| **What does this PR do?** | Detailed description of functionality |
| **Why are we doing this?** | Motivation and context |
| **How was this tested?** | List of unit tests added/modified |
| **How were these changes documented?** | Documentation updates made |

Sources: [.github/ISSUE_TEMPLATE/pull_request_template.md 1-19](https://github.com/tenstorrent/riescue/blob/07cefde3/.github/ISSUE_TEMPLATE/pull_request_template.md?plain=1#L1-L19)[.github/CONTRIBUTING.md 38-46](https://github.com/tenstorrent/riescue/blob/07cefde3/.github/CONTRIBUTING.md?plain=1#L38-L46)

### Code Standards

**Python Code Requirements:**

*   Python 3.9 compatibility
*   Type hints on function signatures where possible
*   Absolute imports in `riescue/` source code (e.g., `from riescue.lib import ...`)
*   No global variables
*   Graceful error handling with appropriate exceptions
*   Sphinx-compatible docstrings on all public APIs

**Code Organization:**

*   Keep functions focused on single responsibilities
*   Avoid deep nesting (prefer early returns)
*   Use descriptive variable and function names
*   Comment complex logic, not obvious code

Sources: [.github/CONTRIBUTING.md 54-58](https://github.com/tenstorrent/riescue/blob/07cefde3/.github/CONTRIBUTING.md?plain=1#L54-L58)

### Code of Conduct

The RiESCUE project follows the Contributor Covenant Code of Conduct version 2.0. Key principles:

**Community Standards:**

*   Demonstrate empathy and kindness
*   Respect differing opinions and experiences
*   Accept and provide constructive feedback
*   Focus on what benefits the overall community

**Unacceptable Behavior:**

*   Harassment of any kind
*   Trolling or insulting comments
*   Publishing private information without permission
*   Other unprofessional conduct

**Enforcement:** Violations should be reported to community leaders at `jvasiljevic@tenstorrent.com` or `rkim@tenstorrent.com`. All reports are investigated promptly and fairly.

Sources: [.github/CODE_OF_CONDUCT.md 1-128](https://github.com/tenstorrent/riescue/blob/07cefde3/.github/CODE_OF_CONDUCT.md?plain=1#L1-L128)[.github/CONTRIBUTING.md 6](https://github.com/tenstorrent/riescue/blob/07cefde3/.github/CONTRIBUTING.md?plain=1#L6-L6)

* * *

## Code Quality and Linting

RiESCUE enforces code quality through three primary tools: `black` (formatting), `flake8` (linting), and `pyright` (type checking). These tools run both locally during development and automatically in CI.

**Quality Tools Integration:**

Sources: [.github/workflows/smoke.yml 16-33](https://github.com/tenstorrent/riescue/blob/07cefde3/.github/workflows/smoke.yml#L16-L33)[.github/CONTRIBUTING.md 60-63](https://github.com/tenstorrent/riescue/blob/07cefde3/.github/CONTRIBUTING.md?plain=1#L60-L63)

### Black Code Formatter

Black is an opinionated code formatter that ensures consistent code style across the project.

**Configuration:** Black is configured through `pyproject.toml` with project-wide settings. No per-file configuration is needed.

**Usage:**

`# Format all Python filesblack . # Check formatting without making changes (CI mode)black --check .`
**Integration Points:**

*   Pre-commit hooks (if configured)
*   CI pipeline at [.github/workflows/smoke.yml 31](https://github.com/tenstorrent/riescue/blob/07cefde3/.github/workflows/smoke.yml#L31-L31)
*   IDE integration (VS Code, PyCharm, etc.)

**Why Black?**

*   Eliminates formatting debates in code reviews
*   Automatic formatting reduces manual work
*   Consistent style improves readability
*   Industry-standard tool with wide adoption

Sources: [.github/workflows/smoke.yml 31](https://github.com/tenstorrent/riescue/blob/07cefde3/.github/workflows/smoke.yml#L31-L31)[.github/CONTRIBUTING.md 60-61](https://github.com/tenstorrent/riescue/blob/07cefde3/.github/CONTRIBUTING.md?plain=1#L60-L61)

### Flake8 Linter

Flake8 checks Python code for style violations, potential bugs, and complexity issues.

**Configuration Location:** The `.flake8` configuration file in the repository root defines all linting rules.

**Common Configuration Elements:**

*   Maximum line length
*   Excluded directories (e.g., `__pycache__`, `.git`, `venv`)
*   Ignored error codes for specific patterns
*   Complexity thresholds

**Usage:**

`# Lint all Python filesflake8 . --config=.flake8 # Lint specific directoryflake8 riescue/ --config=.flake8 # Show statisticsflake8 . --statistics --config=.flake8`
**Common Error Categories:**

*   `E***`: PEP 8 style errors (indentation, whitespace, line length)
*   `W***`: PEP 8 style warnings
*   `F***`: PyFlakes errors (undefined names, unused imports)
*   `C***`: McCabe complexity warnings

Sources: [.github/workflows/smoke.yml 30](https://github.com/tenstorrent/riescue/blob/07cefde3/.github/workflows/smoke.yml#L30-L30)[.github/CONTRIBUTING.md 60-63](https://github.com/tenstorrent/riescue/blob/07cefde3/.github/CONTRIBUTING.md?plain=1#L60-L63)

### Pyright Type Checker

Pyright is a static type checker for Python that validates type hints and catches type-related errors.

**Configuration:** Pyright is configured through `pyproject.toml` with settings for:

*   Type checking mode (basic, standard, strict)
*   Included/excluded paths
*   Python version target
*   Specific type checking rules

**Usage:**

`# Type check all codepyright # Type check specific modulepyright riescue/dtest_framework/ # Show detailed informationpyright --verbose`
**Type Hints Requirements:**

*   All public function signatures should include type hints
*   Use `typing` module for complex types (`List`, `Dict`, `Optional`, etc.)
*   Prefer specific types over `Any` when possible
*   Document return types, especially for functions returning complex objects

**IDE Integration:**

*   **VS Code**: Install Pylance extension (built on Pyright)
*   **PyCharm**: Built-in type checker with Pyright-compatible rules
*   **Vim/Neovim**: Language server protocol support via `pyright-langserver`

Sources: [.github/CONTRIBUTING.md 54-58](https://github.com/tenstorrent/riescue/blob/07cefde3/.github/CONTRIBUTING.md?plain=1#L54-L58)[.github/CONTRIBUTING.md 60-63](https://github.com/tenstorrent/riescue/blob/07cefde3/.github/CONTRIBUTING.md?plain=1#L60-L63)

### Pre-commit Hooks

Pre-commit hooks automatically run quality checks before each commit, catching issues early in the development cycle.

**Setup:**

`# Install pre-commit (if not already installed)pip install pre-commit # Install the git hookspre-commit install # Manually run hooks on all filespre-commit run --all-files`
**Hook Execution Flow:**

**What Hooks Check:**

1.   Code formatting (Black compatibility)
2.   Style compliance (Flake8 rules)
3.   Type correctness (Pyright validation)
4.   SPDX headers (if configured)

**Bypassing Hooks:** While not recommended, hooks can be bypassed in exceptional cases:

`git commit --no-verify`
**Note:** CI will still enforce all checks, so bypassing locally only defers the validation.

Sources: [.github/CONTRIBUTING.md 60-63](https://github.com/tenstorrent/riescue/blob/07cefde3/.github/CONTRIBUTING.md?plain=1#L60-L63)

* * *

## CI/CD Pipeline

The CI/CD pipeline is implemented through GitHub Actions in the `smoke` workflow. It enforces quality gates and automates deployment.

**Pipeline Architecture:**

Sources: [.github/workflows/smoke.yml 1-100](https://github.com/tenstorrent/riescue/blob/07cefde3/.github/workflows/smoke.yml#L1-L100)

### Workflow Jobs and Dependencies

**Job Dependency Chain:**

| Job | Depends On | Runs On | When |
| --- | --- | --- | --- |
| `check-spdx-headers` | None | `ubuntu-latest` | Every push |
| `lint` | `check-spdx-headers` | `ubuntu-latest` | After SPDX check passes |
| `test` | `lint` | `rockylinux:9.1` | Non-main branches only |
| `deploy-docs` | `lint` | `ubuntu-latest` | Main branch only |

Sources: [.github/workflows/smoke.yml 7-100](https://github.com/tenstorrent/riescue/blob/07cefde3/.github/workflows/smoke.yml#L7-L100)

### Phase 1: SPDX Header Check

The first quality gate ensures all source files contain proper Apache-2.0 license headers.

**Implementation:**

`check-spdx-headers:  runs-on: ubuntu-latest  steps:  - name: checkout    uses: actions/checkout@v4  - uses: enarx/spdx@master    with:      licenses: Apache-2.0`
**What It Checks:**

*   All source files have SPDX copyright headers
*   Headers specify Apache-2.0 license
*   Headers include proper attribution

**Example Valid Header:**

`# SPDX-FileCopyrightText: © 2025 Tenstorrent AI ULC# SPDX-License-Identifier: Apache-2.0`
Sources: [.github/workflows/smoke.yml 7-14](https://github.com/tenstorrent/riescue/blob/07cefde3/.github/workflows/smoke.yml#L7-L14)

### Phase 2: Lint

The lint phase validates code style, formatting, and documentation build.

**Steps:**

1.   **Setup Environment**

    *   Checkout code
    *   Install Python 3.9
    *   Install dependencies: `pip install -e .`

2.   **Run Linters**

`flake8 . --config=.flake8black --check .`
3.   **Check Documentation**

`./docs/build.py --build_dir=public --source_dir=docs/source --check`

**Exit Criteria:**

*   All Flake8 rules pass (no style violations)
*   Black reports no formatting changes needed (`--check` mode)
*   Documentation builds without errors or warnings

Sources: [.github/workflows/smoke.yml 16-33](https://github.com/tenstorrent/riescue/blob/07cefde3/.github/workflows/smoke.yml#L16-L33)

### Phase 3: Automated Testing

The test phase builds a complete development environment and runs the test suite. This phase only executes for non-main branches.

**Test Environment Setup:**

**Detailed Steps:**

1.   **Base Dependencies**[.github/workflows/smoke.yml 43-45](https://github.com/tenstorrent/riescue/blob/07cefde3/.github/workflows/smoke.yml#L43-L45)

`./infra/build_base_deps.sh`
Installs system packages required for building tools (see [Base Dependencies](https://deepwiki.com/tenstorrent/riescue/3.2.1-base-dependencies)).

2.   **RISC-V Toolchain**[.github/workflows/smoke.yml 48-54](https://github.com/tenstorrent/riescue/blob/07cefde3/.github/workflows/smoke.yml#L48-L54)

`wget https://github.com/riscv-collab/riscv-gnu-toolchain/releases/download/2025.05.30/riscv64-elf-ubuntu-22.04-gcc-nightly-2025.05.30-nightly.tar.xztar -xJf riscv64-elf-ubuntu-22.04-gcc-nightly-2025.05.30-nightly.tar.xz -C $HOME/RV_TOOLCHAINexport RV_TOOLCHAIN=$HOME/RV_TOOLCHAIN/riscv/bin`
3.   **Python Dependencies**[.github/workflows/smoke.yml 56-59](https://github.com/tenstorrent/riescue/blob/07cefde3/.github/workflows/smoke.yml#L56-L59)

`pip install wheelpip install -e .`
4.   **Whisper Installation**[.github/workflows/smoke.yml 61-66](https://github.com/tenstorrent/riescue/blob/07cefde3/.github/workflows/smoke.yml#L61-L66)

`NO_SINGULARITY=1 ./infra/build_whisper.shexport WHISPER_PATH=/usr/bin/whisper`
5.   **Spike Installation**[.github/workflows/smoke.yml 68-70](https://github.com/tenstorrent/riescue/blob/07cefde3/.github/workflows/smoke.yml#L68-L70)

`NO_SINGULARITY=1 ./infra/build_spike.sh`
6.   **Test Execution**[.github/workflows/smoke.yml 72-73](https://github.com/tenstorrent/riescue/blob/07cefde3/.github/workflows/smoke.yml#L72-L73)

`python -m unittest discover -s tests/ -p "*_test.py"`

**Environment Variables Set:**

*   `RV_TOOLCHAIN`: Path to RISC-V compiler binaries
*   `WHISPER_PATH`: Path to Whisper executable
*   `NO_SINGULARITY=1`: Disables container-specific logic in build scripts

Sources: [.github/workflows/smoke.yml 35-73](https://github.com/tenstorrent/riescue/blob/07cefde3/.github/workflows/smoke.yml#L35-L73)[infra/build_base_deps.sh 1-39](https://github.com/tenstorrent/riescue/blob/07cefde3/infra/build_base_deps.sh#L1-L39)

### Phase 4: Documentation Deployment

For commits to the `main` branch, the pipeline builds and deploys documentation to GitHub Pages.

**Deployment Flow:**

**Deployment Steps:**

1.   **Build Documentation**

`./docs/build.py --build_dir=public --source_dir=docs/source`
Executes Sphinx build and generates HTML documentation.

2.   **Upload Artifact** Uses `actions/upload-pages-artifact@v3` to package the `./public` directory.

3.   **Deploy** Uses `actions/deploy-pages@v4` to publish to GitHub Pages.

**Permissions Required:**

`permissions:  contents: read  pages: write  id-token: write`
**Conditional Execution:**

`if: github.ref == 'refs/heads/main'`
Sources: [.github/workflows/smoke.yml 75-100](https://github.com/tenstorrent/riescue/blob/07cefde3/.github/workflows/smoke.yml#L75-L100)

### Branch-Specific Behavior

The workflow implements different behavior based on the branch:

| Branch | SPDX Check | Lint | Test | Deploy Docs |
| --- | --- | --- | --- | --- |
| `main` | ✓ | ✓ | ✗ | ✓ |
| Feature branches | ✓ | ✓ | ✓ | ✗ |
| PR branches | ✓ | ✓ | ✓ | ✗ |

**Rationale:**

*   **Main branch skips tests**: Tests run on feature branches before merge, so redundant testing on main is unnecessary. This saves CI time and resources.
*   **Only main deploys docs**: Ensures the public documentation site reflects the canonical state of the repository.

**Implementation:**

`# Test job conditionif: github.ref != 'refs/heads/main' # Deploy docs job conditionif: github.ref == 'refs/heads/main'`
Sources: [.github/workflows/smoke.yml 38](https://github.com/tenstorrent/riescue/blob/07cefde3/.github/workflows/smoke.yml#L38-L38)[.github/workflows/smoke.yml 78](https://github.com/tenstorrent/riescue/blob/07cefde3/.github/workflows/smoke.yml#L78-L78)

### Running Tests Locally

While CI handles automated testing, developers can run tests locally for faster iteration:

**Basic Test Execution:**

`# Run all testspython -m unittest discover -v -s tests -p "*_test.py" # Run specific test filepython -m unittest tests/my_test.py # Run specific test classpython -m unittest tests.my_test.MyTestClass # Run specific test methodpython -m unittest tests.my_test.MyTestClass.test_specific_feature`
**Test Discovery:** The test suite uses Python's `unittest` discovery mechanism:

*   Tests are located in the `tests/` directory
*   Test files must match the pattern `*_test.py`
*   Test classes should inherit from `unittest.TestCase`
*   Test methods must start with `test_`

For detailed information about test structure and writing tests, see `tests/README.md`.

Sources: [.github/CONTRIBUTING.md 65-71](https://github.com/tenstorrent/riescue/blob/07cefde3/.github/CONTRIBUTING.md?plain=1#L65-L71)[.github/workflows/smoke.yml 72-73](https://github.com/tenstorrent/riescue/blob/07cefde3/.github/workflows/smoke.yml#L72-L73)

* * *

## Summary

The RiESCUE development workflow enforces quality through multiple automated checkpoints:

1.   **Local Development**: Pre-commit hooks catch issues before they reach CI
2.   **Code Quality**: Black, Flake8, and Pyright ensure consistent, maintainable code
3.   **SPDX Compliance**: License headers verified on every push
4.   **Automated Testing**: Full integration tests run in containerized environment
5.   **Documentation**: Automatic deployment keeps docs synchronized with code

**Key Points for Contributors:**

*   Always create an issue before starting work
*   Use the PR template when submitting changes
*   Run linters locally before pushing
*   Include tests for new features
*   Update documentation for user-facing changes
*   Ensure all CI checks pass before requesting review

Sources: [.github/workflows/smoke.yml 1-100](https://github.com/tenstorrent/riescue/blob/07cefde3/.github/workflows/smoke.yml#L1-L100)[.github/CONTRIBUTING.md 1-72](https://github.com/tenstorrent/riescue/blob/07cefde3/.github/CONTRIBUTING.md?plain=1#L1-L72)

Dismiss
Refresh this wiki

Enter email to refresh
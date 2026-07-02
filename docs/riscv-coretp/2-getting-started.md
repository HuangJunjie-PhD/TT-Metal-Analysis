---
project: riscv-coretp
github: https://github.com/tenstorrent/riscv-coretp
deepwiki: https://deepwiki.com/tenstorrent/riscv-coretp/2-getting-started
---

# Getting Started

Relevant source files
*   [BUILD.bazel](https://github.com/tenstorrent/riscv-coretp/blob/e4fb9eea/BUILD.bazel)
*   [coretp/plans/__init__.py](https://github.com/tenstorrent/riscv-coretp/blob/e4fb9eea/coretp/plans/__init__.py)
*   [coretp/plans/svadu/__init__.py](https://github.com/tenstorrent/riscv-coretp/blob/e4fb9eea/coretp/plans/svadu/__init__.py)
*   [pyproject.toml](https://github.com/tenstorrent/riscv-coretp/blob/e4fb9eea/pyproject.toml)

This document guides you through installing the RISC-V Core Test Plan (coretp) framework, verifying your setup, and running your first test plan queries and documentation exports. By the end of this guide, you will understand how to explore the test plan registry and generate PDF or Excel reports for RISC-V extension test coverage.

For detailed information about the framework architecture, see [Core Architecture](https://deepwiki.com/tenstorrent/riscv-coretp/3-core-architecture). For creating your own test plans, see [Development Guide](https://deepwiki.com/tenstorrent/riscv-coretp/6-development-guide).

## Prerequisites

The coretp framework requires:

*   **Python 3.9 or higher**: The framework uses modern Python features including type hints and dataclasses
*   **pip**: Python package installer for dependency management
*   **Git**: For cloning the repository

**Sources:**[pyproject.toml 16](https://github.com/tenstorrent/riscv-coretp/blob/e4fb9eea/pyproject.toml#L16-L16)

## Installation

### Installing from Source

Clone the repository and install dependencies:

`# Clone the repositorygit clone https://github.com/tenstorrent/riscv-coretp.gitcd riscv-coretp # Install the package in development modepip install -e .`
This installs the `coretp` Python package and registers the `coretp-export` CLI tool. The `-e` flag creates an editable installation, allowing you to modify the source code without reinstalling.

**Key Dependencies:**

*   `reportlab`: PDF generation for test plan documentation
*   `openpyxl`: Excel export for test coverage matrices
*   `flake8`, `black`, `pyright`: Development and linting tools

**Sources:**[pyproject.toml 17-29](https://github.com/tenstorrent/riscv-coretp/blob/e4fb9eea/pyproject.toml#L17-L29)[pyproject.toml 34-35](https://github.com/tenstorrent/riscv-coretp/blob/e4fb9eea/pyproject.toml#L34-L35)

### Verifying Installation

Confirm the installation by checking the package and CLI availability:

`# Verify Python package importpython3 -c "import coretp; print('coretp imported successfully')" # Verify CLI toolcoretp-export --help`
You can also verify that the test plan registry is functioning:

`from coretp.plans import list_plans # List all registered test plansplans = list_plans()print(f"Found {len(plans)} test plans")for plan in plans:    print(f"  - {plan.name}: {plan.description}")`
**Sources:**[coretp/plans/__init__.py 1-21](https://github.com/tenstorrent/riscv-coretp/blob/e4fb9eea/coretp/plans/__init__.py#L1-L21)

## Understanding the Project Structure

The coretp repository follows a modular structure organized by functional layer:

**Key Modules:**

| Directory/Module | Purpose | Key Files |
| --- | --- | --- |
| `coretp/plans/` | Test plan definitions and registry | `__init__.py`, `test_plan_registry.py` |
| `coretp/plans/svadu/` | SVADU extension test scenarios | `svadu_scenarios.py`, `__init__.py` |
| `coretp/plans/sstc/` | SSTC timer counter test scenarios | `sstc_scenarios.py` |
| `coretp/plans/zicbom_zicboz_zicbop/` | Cache management test scenarios | `zicbom_zicboz_zicbop_scenarios.py` |
| `coretp/env/` | Test environment configuration | `cfg.py`, `env.py`, `solver.py` |
| `coretp/step/` | Test step primitives | `memory.py`, `csr.py`, `__init__.py` |
| `coretp/isa/` | Instruction catalog system | `catalog.py`, `instructions/` |
| `coretp/coretp_export.py` | CLI tool for PDF/Excel export | (main CLI entry point) |

**Sources:**[coretp/plans/__init__.py 5-18](https://github.com/tenstorrent/riscv-coretp/blob/e4fb9eea/coretp/plans/__init__.py#L5-L18)[pyproject.toml 31-35](https://github.com/tenstorrent/riscv-coretp/blob/e4fb9eea/pyproject.toml#L31-L35)

## Exploring Test Plans

The test plan registry provides programmatic access to all defined test plans. The registry automatically discovers plans when the `coretp.plans` module is imported.

### Listing Available Test Plans

Use the `list_plans()` function to retrieve all registered test plans:

`from coretp.plans import list_plans # Get all test plansall_plans = list_plans() # Display basic informationfor plan in all_plans:    print(f"Plan: {plan.name}")    print(f"  Description: {plan.description}")    print(f"  Tags: {', '.join(plan.tags)}")    print(f"  Scenarios: {len(plan.scenarios)}")    print()`
**Example Output:**

```
Plan: zicbom_zicboz_zicbop
  Description: Cache Management Operations
  Tags: cache, cmo, zicbom, zicboz, zicbop
  Scenarios: 47

Plan: svadu
  Description: Covers SVADU (Supervisor-mode Virtual Address Update) behavior including hardware A/D bit updates
  Tags: svadu, paging, hardware_ad_update
  Scenarios: 4

Plan: sstc
  Description: Supervisor-mode Timer Extension
  Tags: sstc, timer, supervisor
  Scenarios: 6
```

### Querying Specific Test Plans

Use `get_plan()` to retrieve a specific test plan by name, or `query_plans()` for filtered searches:

`from coretp.plans import get_plan, query_plans # Get a specific plan by namesvadu_plan = get_plan("svadu")print(f"SVADU plan has {len(svadu_plan.scenarios)} scenarios") # Query plans by tagspaging_plans = query_plans(tags=["paging"])print(f"Found {len(paging_plans)} plans related to paging") # Query plans by name patterncache_plans = query_plans(name_contains="zicbo")print(f"Found {len(cache_plans)} cache-related plans")`
**Test Plan Registry System Diagram:**

**Sources:**[coretp/plans/__init__.py 5](https://github.com/tenstorrent/riscv-coretp/blob/e4fb9eea/coretp/plans/__init__.py#L5-L5)[coretp/plans/svadu/__init__.py 8-12](https://github.com/tenstorrent/riscv-coretp/blob/e4fb9eea/coretp/plans/svadu/__init__.py#L8-L12)

## Generating Test Documentation

The `coretp-export` CLI tool generates documentation from the test plan registry in PDF or Excel formats. This is useful for generating compliance reports, test coverage matrices, and detailed test plan documentation.

### CLI Command Structure

### Exporting to PDF

Generate PDF documentation for test plans:

`# Export all test plans to PDFcoretp-export --pdf all_plans.pdf # Export a specific test plancoretp-export --plan svadu --pdf svadu_test_plan.pdf # Export cache management test plancoretp-export --plan zicbom_zicboz_zicbop --pdf cache_tests.pdf`
The generated PDF includes:

*   Test plan metadata (name, description, tags)
*   Complete scenario listings with IDs and descriptions
*   Environment configurations (privilege modes, paging modes)
*   Test step sequences for each scenario

### Exporting to Excel

Generate Excel spreadsheets for test coverage tracking:

`# Export all test plans to Excelcoretp-export --excel all_plans.xlsx # Export a specific test plan with scenarioscoretp-export --plan sstc --excel sstc_coverage.xlsx`
The generated Excel file contains:

*   Summary worksheet with test plan overview
*   Scenario worksheets with detailed test steps
*   Environment configuration matrices
*   Coverage tracking columns (status, notes, results)

### Listing Available Plans via CLI

Use the `--list` flag to see all registered test plans without generating exports:

`# List all available test planscoretp-export --list`
**Example Output:**

```
Available Test Plans:
  - zicbom_zicboz_zicbop (47 scenarios)
  - svadu (4 scenarios)
  - sstc (6 scenarios)
  - paging (12 scenarios)
  - sscofpmf (Multiple scenarios)
  - zicntr_zihpm_sscounterenw (Multiple scenarios)
```

**Sources:**[pyproject.toml 34-35](https://github.com/tenstorrent/riscv-coretp/blob/e4fb9eea/pyproject.toml#L34-L35)

## First Steps with Test Scenarios

Each test plan contains multiple test scenarios. A scenario is a complete test case with:

*   Unique ID and descriptive name
*   Test step sequence (memory allocation, CSR operations, assertions)
*   Environment configuration (privilege modes, paging modes, register width)

**Example: Exploring SVADU Scenarios**

`from coretp.plans import get_plan # Get the SVADU test plansvadu_plan = get_plan("svadu") # Examine scenariosfor scenario in svadu_plan.scenarios:    print(f"Scenario ID: {scenario.id}")    print(f"  Name: {scenario.name}")    print(f"  Description: {scenario.description}")    print(f"  Steps: {len(scenario.steps)}")    print(f"  Environment: {scenario.env}")    print()`
**Scenario Composition Pattern:**

Test scenarios follow a standard composition pattern where test functions use decorators for registration and return structured test steps:

```
@svadu_scenario  ← Decorator registers function with plan
def SID_SVADU_01():
    ↓
    1. Define TestEnvCfg (privilege modes, paging modes)
    2. Create Memory regions
    3. Add CSR operations (CsrRead, CsrWrite)
    4. Add memory operations (Load, Store, MemAccess)
    5. Add assertions (AssertException, AssertEqual)
    6. Return TestScenario.from_steps(...)
```

For detailed information about writing scenarios, see [Writing Test Scenarios](https://deepwiki.com/tenstorrent/riscv-coretp/6.2-writing-test-scenarios). For the complete test step reference, see [Test Steps Reference](https://deepwiki.com/tenstorrent/riscv-coretp/5-test-steps-reference).

**Sources:**[coretp/plans/svadu/__init__.py 8-12](https://github.com/tenstorrent/riscv-coretp/blob/e4fb9eea/coretp/plans/svadu/__init__.py#L8-L12)

## Project Build System

The coretp framework supports both pip-based installation and Bazel builds:

### Bazel Build (Optional)

For projects using Bazel, coretp provides a `py_library` target:

`# In your BUILD fileload("@rules_python//python:defs.bzl", "py_library") py_binary(    name = "my_test_runner",    deps = ["@coretp//:coretp"],)`
The Bazel target exposes all Python modules under `coretp/**/*.py` with public visibility.

**Sources:**[BUILD.bazel 1-9](https://github.com/tenstorrent/riscv-coretp/blob/e4fb9eea/BUILD.bazel#L1-L9)

## Next Steps

Now that you have coretp installed and understand the basic commands, explore these topics:

| Topic | Wiki Page | What You'll Learn |
| --- | --- | --- |
| Framework Architecture | [Core Architecture](https://deepwiki.com/tenstorrent/riscv-coretp/3-core-architecture) | Layered design, subsystem interactions |
| Test Plan System | [Test Plan Registry System](https://deepwiki.com/tenstorrent/riscv-coretp/3.1-test-plan-registry-system) | How plans are registered and discovered |
| Environment Configuration | [Test Environment Configuration](https://deepwiki.com/tenstorrent/riscv-coretp/3.2-test-environment-configuration) | TestEnvCfg, TestEnv, TestEnvSolver |
| Writing Tests | [Writing Test Scenarios](https://deepwiki.com/tenstorrent/riscv-coretp/6.2-writing-test-scenarios) | Creating your own test scenarios |
| Test Steps | [Test Steps Reference](https://deepwiki.com/tenstorrent/riscv-coretp/5-test-steps-reference) | Complete reference for all step types |
| Cache Extension Tests | [Cache Management Extensions](https://deepwiki.com/tenstorrent/riscv-coretp/4.1-cache-management-extensions-(zicbomzicbozzicbop)) | 47+ scenarios for cache operations |
| Virtual Memory Tests | [Virtual Memory Extensions](https://deepwiki.com/tenstorrent/riscv-coretp/4.2-virtual-memory-extensions) | SVADU and page table walk tests |
| Supervisor Extensions | [Supervisor Mode Extensions](https://deepwiki.com/tenstorrent/riscv-coretp/4.3-supervisor-mode-extensions) | SSTC, counters, PMU overflow tests |

**Common Workflows:**

1.   **Exploring existing tests**: Use `list_plans()` and `query_plans()` to discover test coverage
2.   **Generating documentation**: Use `coretp-export` to create PDF/Excel reports for compliance
3.   **Understanding test structure**: Examine specific scenarios with `get_plan().scenarios`
4.   **Extending the framework**: See [Creating New Test Plans](https://deepwiki.com/tenstorrent/riscv-coretp/6.1-creating-new-test-plans) to add custom test plans

**Sources:**[coretp/plans/__init__.py 8-18](https://github.com/tenstorrent/riscv-coretp/blob/e4fb9eea/coretp/plans/__init__.py#L8-L18)

Dismiss
Refresh this wiki

Enter email to refresh
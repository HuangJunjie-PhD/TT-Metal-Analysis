---
project: riscv-coretp
github: https://github.com/tenstorrent/riscv-coretp
deepwiki: https://deepwiki.com/tenstorrent/riscv-coretp
---

# Overview

Relevant source files
*   [BUILD.bazel](https://github.com/tenstorrent/riscv-coretp/blob/e4fb9eea/BUILD.bazel)
*   [coretp/plans/__init__.py](https://github.com/tenstorrent/riscv-coretp/blob/e4fb9eea/coretp/plans/__init__.py)
*   [coretp/plans/svadu/__init__.py](https://github.com/tenstorrent/riscv-coretp/blob/e4fb9eea/coretp/plans/svadu/__init__.py)
*   [pyproject.toml](https://github.com/tenstorrent/riscv-coretp/blob/e4fb9eea/pyproject.toml)

## Purpose and Scope

This document provides an introduction to the RISC-V Core Test Plan (coretp) repository. It explains the purpose of the framework, its high-level architecture, and the organization of test plans for RISC-V processor verification. For detailed information about specific subsystems, see:

*   Test plan registration and discovery: [Test Plan Registry System](https://deepwiki.com/tenstorrent/riscv-coretp/3.1-test-plan-registry-system)
*   Environment configuration: [Test Environment Configuration](https://deepwiki.com/tenstorrent/riscv-coretp/3.2-test-environment-configuration)
*   Building test scenarios: [Test Steps Reference](https://deepwiki.com/tenstorrent/riscv-coretp/5-test-steps-reference)
*   Extending the framework: [Development Guide](https://deepwiki.com/tenstorrent/riscv-coretp/6-development-guide)

**Sources:**[pyproject.toml 1-44](https://github.com/tenstorrent/riscv-coretp/blob/e4fb9eea/pyproject.toml#L1-L44)

## What is coretp?

The `coretp` (RISC-V Core Test Plan) repository is a comprehensive test framework for RISC-V processor verification and compliance testing. It provides a declarative Python-based system for defining, organizing, and documenting test scenarios that validate RISC-V architectural extensions.

The framework supports:

*   **Extension Coverage**: Test plans for RISC-V extensions including ZICBOM/ZICBOZ/ZICBOP (cache management), Svadu (hardware A/D bits), SSTC (supervisor timers), and more
*   **Test Documentation**: Export test plans to PDF and Excel formats via the `coretp-export` CLI tool
*   **Composable Test Steps**: Building blocks for memory operations, CSR access, assertions, and multi-processor coordination
*   **ISA Abstraction**: Instruction catalog system for working with RISC-V instruction definitions
*   **Environment Variability**: Automatic generation of test variants across privilege modes, paging modes, and architectural parameters

**Sources:**[pyproject.toml 12-35](https://github.com/tenstorrent/riscv-coretp/blob/e4fb9eea/pyproject.toml#L12-L35)[coretp/plans/__init__.py 1-21](https://github.com/tenstorrent/riscv-coretp/blob/e4fb9eea/coretp/plans/__init__.py#L1-L21)

## High-Level Architecture

The coretp framework follows a layered architecture with five primary layers:

**Diagram: coretp Layered Architecture**

**Layer Descriptions:**

| Layer | Purpose | Key Components |
| --- | --- | --- |
| **Test Definition** | Register and organize test plans | `new_test_plan()`, scenario decorator functions, test plan modules |
| **Test Configuration** | Resolve test environments | `TestEnvCfg`, `TestEnvSolver`, `TestEnv` |
| **Test Execution** | Composable test primitives | `TestStep`, `Memory`, `Load/Store`, `CsrRead/Write`, assertions |
| **ISA Support** | RISC-V instruction definitions | `InstructionCatalog`, `InstructionDef`, `ALL_INSTRS` |
| **Export** | Documentation generation | `coretp-export` CLI, PDF/Excel exporters |

**Sources:**[coretp/plans/__init__.py 1-21](https://github.com/tenstorrent/riscv-coretp/blob/e4fb9eea/coretp/plans/__init__.py#L1-L21)[pyproject.toml 34-35](https://github.com/tenstorrent/riscv-coretp/blob/e4fb9eea/pyproject.toml#L34-L35)

## Key Component Flow

The following diagram illustrates how test scenarios are created and registered in the coretp system:

**Diagram: Test Scenario Registration and Discovery Flow**

**Workflow Steps:**

1.   **Definition**: Test plans are created using `new_test_plan()` which returns a decorator function. This decorator is applied to test scenario functions.

2.   **Registration**: Decorated functions are automatically registered in `_PLAN_REGISTRY`. Discovery is performed via `get_plan()`, `list_plans()`, and `query_plans()` functions.

3.   **Test Steps**: Scenario functions instantiate `TestStep` objects (Memory, Load, CsrWrite, AssertException, etc.) that define the test behavior.

4.   **Export**: The `coretp-export` CLI tool reads the registry and generates PDF and Excel documentation.

**Sources:**[coretp/plans/__init__.py 5-20](https://github.com/tenstorrent/riscv-coretp/blob/e4fb9eea/coretp/plans/__init__.py#L5-L20)[pyproject.toml 34-35](https://github.com/tenstorrent/riscv-coretp/blob/e4fb9eea/pyproject.toml#L34-L35)

## Repository Structure

The coretp repository is organized into the following directory structure:

```
coretp/
├── plans/                    # Test plan definitions
│   ├── __init__.py          # Registry exports: new_test_plan, get_plan, list_plans, query_plans
│   ├── test_plan_registry.py
│   ├── zicbom_zicboz_zicbop/
│   ├── svadu/
│   ├── sstc/
│   ├── paging/
│   ├── sscofpmf/
│   ├── zicntr_zihpm_sscounterenw/
│   ├── zicond/
│   ├── zkt/
│   ├── zimop_zcmop/
│   └── zifencei/
├── step/                     # TestStep implementations
├── env/                      # TestEnv, TestEnvCfg, TestEnvSolver
├── isa/                      # InstructionCatalog, InstructionDef
└── coretp_export.py          # CLI tool entry point
```

**Key Modules:**

| Module | Purpose | Entry Points |
| --- | --- | --- |
| `coretp/plans/` | Test plan organization | `new_test_plan()`, `get_plan()`, `list_plans()`, `query_plans()` |
| `coretp/step/` | Test step primitives | `TestStep`, `Memory`, `Load`, `Store`, `CsrRead`, `CsrWrite`, assertions |
| `coretp/env/` | Environment configuration | `TestEnvCfg`, `TestEnvSolver`, `TestEnv` |
| `coretp/isa/` | Instruction support | `InstructionCatalog`, `InstructionDef`, `ALL_INSTRS` |
| `coretp_export.py` | Documentation export | `main()` function |

**Sources:**[coretp/plans/__init__.py 1-21](https://github.com/tenstorrent/riscv-coretp/blob/e4fb9eea/coretp/plans/__init__.py#L1-L21)[BUILD.bazel 1-9](https://github.com/tenstorrent/riscv-coretp/blob/e4fb9eea/BUILD.bazel#L1-L9)

## Test Plan Coverage

The framework includes test plans for the following RISC-V extensions:

**Diagram: Test Plan Module Organization**

**Test Plan Summary:**

| Extension | Module | Category | Scenario Count |
| --- | --- | --- | --- |
| ZICBOM/ZICBOZ/ZICBOP | `zicbom_zicboz_zicbop_scenarios` | Cache Management | 47+ |
| Svadu | `svadu_scenarios` | Virtual Memory | 4 |
| Paging | `page_table_walks` | Virtual Memory | 12 |
| SSTC | `sstc_scenarios` | Supervisor Extensions | 6 |
| Zicntr/Zihpm/Sscounterenw | `zicntr_zihpm_sscounterenw_scenarios` | Supervisor Extensions | Multiple |
| Sscofpmf | `sscofpmf_scenarios` | Supervisor Extensions | Multiple |
| Zicond | `zicond_scenarios` | Base Extensions | Multiple |
| Zkt | `zkt_scenarios` | Cryptography | Multiple |
| Zimop/Zcmop | `zimop_zcmop_scenarios` | Base Extensions | Multiple |
| Zifencei | `zifencei_scenarios` | Base Extensions | Multiple |

For detailed documentation of each test plan, see [Test Plans Overview](https://deepwiki.com/tenstorrent/riscv-coretp/4-test-plans-overview).

**Sources:**[coretp/plans/__init__.py 8-18](https://github.com/tenstorrent/riscv-coretp/blob/e4fb9eea/coretp/plans/__init__.py#L8-L18)

## Command-Line Interface

The `coretp-export` command provides test plan documentation export:

`coretp-export [options]`
The CLI tool reads registered test plans from the `_PLAN_REGISTRY` and generates:

*   **PDF documents**: Detailed test plan specifications using ReportLab
*   **Excel spreadsheets**: Tabular test scenario summaries using openpyxl

**Dependencies:**

| Package | Purpose |
| --- | --- |
| `reportlab` | PDF generation |
| `openpyxl` | Excel file generation |

**Sources:**[pyproject.toml 17-20](https://github.com/tenstorrent/riscv-coretp/blob/e4fb9eea/pyproject.toml#L17-L20)[pyproject.toml 34-35](https://github.com/tenstorrent/riscv-coretp/blob/e4fb9eea/pyproject.toml#L34-L35)

## Next Steps

*   To learn how to install and run coretp, see [Getting Started](https://deepwiki.com/tenstorrent/riscv-coretp/2-getting-started)
*   To understand the architecture in detail, see [Core Architecture](https://deepwiki.com/tenstorrent/riscv-coretp/3-core-architecture)
*   To explore specific test plans, see [Test Plans Overview](https://deepwiki.com/tenstorrent/riscv-coretp/4-test-plans-overview)
*   To contribute new test plans, see [Development Guide](https://deepwiki.com/tenstorrent/riscv-coretp/6-development-guide)

**Sources:**[coretp/plans/__init__.py 1-21](https://github.com/tenstorrent/riscv-coretp/blob/e4fb9eea/coretp/plans/__init__.py#L1-L21)[pyproject.toml 1-44](https://github.com/tenstorrent/riscv-coretp/blob/e4fb9eea/pyproject.toml#L1-L44)

Dismiss
Refresh this wiki

Enter email to refresh
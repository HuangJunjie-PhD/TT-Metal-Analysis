---
project: riscv-coretp
github: https://github.com/tenstorrent/riscv-coretp
deepwiki: https://deepwiki.com/tenstorrent/riscv-coretp/6-development-guide
---

# Development Guide

Relevant source files
*   [coretp/plans/svadu/svadu_scenarios.py](https://github.com/tenstorrent/riscv-coretp/blob/e4fb9eea/coretp/plans/svadu/svadu_scenarios.py)
*   [coretp/plans/test_plan_registry.py](https://github.com/tenstorrent/riscv-coretp/blob/e4fb9eea/coretp/plans/test_plan_registry.py)
*   [coretp/plans/zicntr_zihpm_sscounterenw/zicntr_zihpm_sscounterenw_scenarios.py](https://github.com/tenstorrent/riscv-coretp/blob/e4fb9eea/coretp/plans/zicntr_zihpm_sscounterenw/zicntr_zihpm_sscounterenw_scenarios.py)
*   [pyproject.toml](https://github.com/tenstorrent/riscv-coretp/blob/e4fb9eea/pyproject.toml)

## Purpose and Scope

This document provides guidance for developers who want to extend the coretp framework by adding new test plans, creating test scenarios, or extending the instruction catalog. It covers the development workflow, core patterns, and best practices for contributing to the repository.

For detailed step-by-step instructions on specific development tasks, see:

*   [Creating New Test Plans](https://deepwiki.com/tenstorrent/riscv-coretp/6.1-creating-new-test-plans) - Guide for adding new extension test coverage
*   [Writing Test Scenarios](https://deepwiki.com/tenstorrent/riscv-coretp/6.2-writing-test-scenarios) - Detailed patterns for composing test scenarios
*   [Extending the Instruction Catalog](https://deepwiki.com/tenstorrent/riscv-coretp/6.3-extending-the-instruction-catalog) - Adding new RISC-V instructions

For understanding the underlying architecture before development, see:

*   [Test Plan Registry System](https://deepwiki.com/tenstorrent/riscv-coretp/3.1-test-plan-registry-system) - Registration and discovery mechanisms
*   [Test Environment Configuration](https://deepwiki.com/tenstorrent/riscv-coretp/3.2-test-environment-configuration) - Environment configuration system
*   [Test Step System](https://deepwiki.com/tenstorrent/riscv-coretp/3.3-test-step-system) - Available test primitives

## Development Environment Setup

### Installation

The project uses standard Python packaging tools. Install the development environment with:

`pip install -e .`
This installs the `coretp` package in editable mode along with all dependencies specified in [pyproject.toml 17-29](https://github.com/tenstorrent/riscv-coretp/blob/e4fb9eea/pyproject.toml#L17-L29)

### Required Dependencies

| Dependency | Purpose |
| --- | --- |
| `reportlab` | PDF export functionality |
| `openpyxl` | Excel export functionality |
| `flake8` | Code linting |
| `black` | Code formatting |
| `pyright` | Type checking |
| `coverage` | Test coverage analysis |

### Project Structure

```
coretp/
├── plans/                          # Test plan definitions
│   ├── __init__.py                # Registry exports
│   ├── test_plan_registry.py     # Registration system
│   ├── svadu/                     # SVADU extension tests
│   ├── sstc/                      # SSTC extension tests
│   ├── zicbom_zicboz_zicbop/     # Cache management tests
│   └── ...                        # Other extension tests
├── step/                          # TestStep implementations
├── isa/                           # Instruction catalog
├── env/                           # Environment configuration
└── rv_enums.py                    # RISC-V enumerations
```

**Sources:**[pyproject.toml 1-44](https://github.com/tenstorrent/riscv-coretp/blob/e4fb9eea/pyproject.toml#L1-L44)

## Development Workflow Overview

The following diagram illustrates the complete development workflow from defining a new test plan to exporting documentation:

**Sources:**[coretp/plans/test_plan_registry.py 1-192](https://github.com/tenstorrent/riscv-coretp/blob/e4fb9eea/coretp/plans/test_plan_registry.py#L1-L192)[coretp/plans/svadu/svadu_scenarios.py 1-210](https://github.com/tenstorrent/riscv-coretp/blob/e4fb9eea/coretp/plans/svadu/svadu_scenarios.py#L1-L210)

## Core Development Patterns

### Decorator-Based Registration

The framework uses a decorator pattern to register test scenarios with test plans. This pattern enables automatic discovery and collection of scenarios without manual registration.

#### Key Components

| Component | Location | Purpose |
| --- | --- | --- |
| `new_test_plan()` | [test_plan_registry.py 144-164](https://github.com/tenstorrent/riscv-coretp/blob/e4fb9eea/test_plan_registry.py#L144-L164) | Creates decorator for test plan |
| `@my_extension_scenario` | Extension `__init__.py` | Decorator returned by `new_test_plan()` |
| `_TestPlanInfo` | [test_plan_registry.py 44-79](https://github.com/tenstorrent/riscv-coretp/blob/e4fb9eea/test_plan_registry.py#L44-L79) | Internal registry entry |
| `get_plan()` | [test_plan_registry.py 167-173](https://github.com/tenstorrent/riscv-coretp/blob/e4fb9eea/test_plan_registry.py#L167-L173) | Retrieves built `TestPlan` |

**Sources:**[coretp/plans/test_plan_registry.py 44-192](https://github.com/tenstorrent/riscv-coretp/blob/e4fb9eea/coretp/plans/test_plan_registry.py#L44-L192)[coretp/plans/svadu/svadu_scenarios.py 29](https://github.com/tenstorrent/riscv-coretp/blob/e4fb9eea/coretp/plans/svadu/svadu_scenarios.py#L29-L29)

### Scenario Composition Pattern

Test scenarios follow a consistent composition pattern that separates configuration, step definition, and metadata:

#### Example: SVADU Scenario

The following code pattern appears consistently across test scenarios:

`@svadu_scenariodef SID_SVADU_01_fault_on_a_bit_cleared():    """Docstring explaining scenario purpose"""        # 1. Define memory regions    mem = Memory(size=0x1000, page_size=PageSize.SIZE_4K, ...)        # 2. Configure CSRs    disable_svadu = CsrWrite(csr_name="menvcfg", clear_mask=1 << 61)        # 3. Perform operations    load_op = Load(memory=mem)        # 4. Assert expected behavior    assert_fault = AssertException(cause=ExceptionCause.LOAD_PAGE_FAULT, code=[load_op])        # 5. Assemble scenario    return TestScenario.from_steps(        id="1",        name="SID_SVADU_01_fault_on_a_bit_cleared",        description="When SVADU disabled, memory access faults when pte.a=0",        env=TestEnvCfg(paging_modes=[PagingMode.SV39, PagingMode.SV48, PagingMode.SV57]),        steps=[mem, disable_svadu, assert_fault],    )`
**Sources:**[coretp/plans/svadu/svadu_scenarios.py 32-62](https://github.com/tenstorrent/riscv-coretp/blob/e4fb9eea/coretp/plans/svadu/svadu_scenarios.py#L32-L62)

### TestEnvCfg Configuration Patterns

`TestEnvCfg` defines the parameter space for test scenarios. The solver generates concrete test environments for each valid combination.

#### Common Configuration Patterns

| Pattern | Example | Use Case |
| --- | --- | --- |
| Single privilege mode | `TestEnvCfg(priv_modes=[PrivilegeMode.U])` | Testing U-mode specific behavior |
| All paging modes | `TestEnvCfg(paging_modes=[PagingMode.SV39, SV48, SV57])` | Virtual memory features |
| Multi-hart tests | `TestEnvCfg(min_num_harts=2)` | Multi-processor synchronization |
| Privilege hierarchy | `TestEnvCfg(priv_modes=[U, S, M])` | Testing access control at all levels |

**Sources:**[coretp/plans/svadu/svadu_scenarios.py 56](https://github.com/tenstorrent/riscv-coretp/blob/e4fb9eea/coretp/plans/svadu/svadu_scenarios.py#L56-L56)[coretp/plans/zicntr_zihpm_sscounterenw/zicntr_zihpm_sscounterenw_scenarios.py 50](https://github.com/tenstorrent/riscv-coretp/blob/e4fb9eea/coretp/plans/zicntr_zihpm_sscounterenw/zicntr_zihpm_sscounterenw_scenarios.py#L50-L50)

## Common TestStep Usage Patterns

### Memory Operations Pattern

Most test scenarios follow this memory allocation and access pattern:

**Sources:**[coretp/plans/svadu/svadu_scenarios.py 37-50](https://github.com/tenstorrent/riscv-coretp/blob/e4fb9eea/coretp/plans/svadu/svadu_scenarios.py#L37-L50)[coretp/plans/svadu/svadu_scenarios.py 106-131](https://github.com/tenstorrent/riscv-coretp/blob/e4fb9eea/coretp/plans/svadu/svadu_scenarios.py#L106-L131)

### CSR Configuration Pattern

CSR operations use set/clear masks for precise bit manipulation:

`# Enable a feature by setting bit 61enable_svadu = CsrWrite(csr_name="menvcfg", set_mask=1 << 61) # Disable a feature by clearing bit 61disable_svadu = CsrWrite(csr_name="menvcfg", clear_mask=1 << 61) # Set all bits in a maskall_fields_mask = sum(1 << bit for bit, _ in COUNTER_FIELDS.values())enable_all = CsrWrite(csr_name="mcounteren", set_mask=all_fields_mask) # Write absolute valuewrite_value = CsrWrite(csr_name="mcounteren", value=0x00000001)`
**Sources:**[coretp/plans/svadu/svadu_scenarios.py 46](https://github.com/tenstorrent/riscv-coretp/blob/e4fb9eea/coretp/plans/svadu/svadu_scenarios.py#L46-L46)[coretp/plans/svadu/svadu_scenarios.py 79](https://github.com/tenstorrent/riscv-coretp/blob/e4fb9eea/coretp/plans/svadu/svadu_scenarios.py#L79-L79)[coretp/plans/zicntr_zihpm_sscounterenw/zicntr_zihpm_sscounterenw_scenarios.py 37-40](https://github.com/tenstorrent/riscv-coretp/blob/e4fb9eea/coretp/plans/zicntr_zihpm_sscounterenw/zicntr_zihpm_sscounterenw_scenarios.py#L37-L40)

### Assertion Pattern

Assertions verify expected behavior and exceptions:

`# Assert exception occursload_op = Load(memory=mem)assert_fault = AssertException(    cause=ExceptionCause.LOAD_PAGE_FAULT,     code=[load_op]) # Assert values are equalread_pte = ReadLeafPTE(memory=mem)mask = LoadImmediateStep(imm=1 << 6)and_result = Arithmetic(op="and", src1=read_pte, src2=mask)assert_equal = AssertEqual(src1=and_result, src2=mask) # Assert values are not equalassert_not_equal = AssertNotEqual(src1=result, src2=zero)`
**Sources:**[coretp/plans/svadu/svadu_scenarios.py 48-50](https://github.com/tenstorrent/riscv-coretp/blob/e4fb9eea/coretp/plans/svadu/svadu_scenarios.py#L48-L50)[coretp/plans/svadu/svadu_scenarios.py 118-131](https://github.com/tenstorrent/riscv-coretp/blob/e4fb9eea/coretp/plans/svadu/svadu_scenarios.py#L118-L131)

## Naming Conventions

### Scenario Function Names

Scenario functions follow the pattern: `SID_<EXTENSION>_<NUMBER>_<description>`

Examples:

*   `SID_SVADU_01_fault_on_a_bit_cleared` - SVADU extension, scenario 1
*   `SID_XCOUNTEREN_02_U` - Counter enable extension, scenario 2, U-mode variant
*   `SID_SVADU_MP_3_hart_update_observe` - Multi-processor (MP) scenario 3

### Module Organization

Each test plan resides in its own directory under `coretp/plans/`:

```
plans/
├── svadu/
│   ├── __init__.py              # Defines svadu_scenario decorator
│   └── svadu_scenarios.py       # Contains @svadu_scenario functions
├── sstc/
│   ├── __init__.py              # Defines sstc_scenario decorator
│   └── sstc_scenarios.py        # Contains @sstc_scenario functions
└── zicntr_zihpm_sscounterenw/
    ├── __init__.py              # Defines zicntr_zihpm_sscounterenw_scenario
    └── zicntr_zihpm_sscounterenw_scenarios.py
```

**Sources:**[coretp/plans/svadu/svadu_scenarios.py 29](https://github.com/tenstorrent/riscv-coretp/blob/e4fb9eea/coretp/plans/svadu/svadu_scenarios.py#L29-L29)[coretp/plans/zicntr_zihpm_sscounterenw/zicntr_zihpm_sscounterenw_scenarios.py 8](https://github.com/tenstorrent/riscv-coretp/blob/e4fb9eea/coretp/plans/zicntr_zihpm_sscounterenw/zicntr_zihpm_sscounterenw_scenarios.py#L8-L8)

## Development Checklist

When adding a new test plan or scenario, verify:

### Test Plan Creation

*   - [x]  Created directory under `coretp/plans/<extension_name>/`
*   - [x]  Created `__init__.py` with decorator definition
*   - [x]  Registered plan with `new_test_plan(name, description, tags, features)`
*   - [x]  Imported decorator in scenario module

### Scenario Implementation

*   - [x]  Function name follows `SID_<EXTENSION>_<NUMBER>_<description>` pattern
*   - [x]  Applied decorator: `@my_extension_scenario`
*   - [x]  Included docstring describing scenario purpose
*   - [x]  Composed TestSteps in logical order
*   - [x]  Configured appropriate `TestEnvCfg` for test requirements
*   - [x]  Returned `TestScenario.from_steps()` with all required fields

### Code Quality

*   - [x]  Ran `black` formatter: `black coretp/`
*   - [x]  Passed `flake8` linting: `flake8 coretp/`
*   - [x]  Verified type checking: `pyright coretp/`
*   - [x]  Tested scenario builds without errors

### Documentation

*   - [x]  Scenario docstring clearly explains test purpose
*   - [x]  Added inline comments for complex logic
*   - [x]  Updated relevant wiki pages if introducing new patterns

**Sources:**[pyproject.toml 24-29](https://github.com/tenstorrent/riscv-coretp/blob/e4fb9eea/pyproject.toml#L24-L29)[coretp/plans/test_plan_registry.py 144-164](https://github.com/tenstorrent/riscv-coretp/blob/e4fb9eea/coretp/plans/test_plan_registry.py#L144-L164)[coretp/plans/svadu/svadu_scenarios.py 32-62](https://github.com/tenstorrent/riscv-coretp/blob/e4fb9eea/coretp/plans/svadu/svadu_scenarios.py#L32-L62)

## Testing Your Changes

### Building Test Plans

Verify that your test plan builds correctly:

`from coretp.plans import get_plan # Get your test planplan = get_plan("my_extension") # Check scenarios were registeredprint(f"Plan: {plan.name}")print(f"Scenarios: {len(plan.scenarios)}")for scenario in plan.scenarios:    print(f"  - {scenario.name}")`
### Querying Plans

Use the query interface to verify metadata:

`from coretp.plans import list_plans, query_plans # List all plansall_plans = list_plans() # Query by tagsmemory_plans = query_plans(tags=["memory"]) # Query by featurestimer_plans = query_plans(features=["timer"])`
### Exporting Documentation

Generate documentation to verify formatting:

`# Export to PDFcoretp-export --pdf output.pdf # Export to Excelcoretp-export --excel output.xlsx`
**Sources:**[coretp/plans/test_plan_registry.py 167-192](https://github.com/tenstorrent/riscv-coretp/blob/e4fb9eea/coretp/plans/test_plan_registry.py#L167-L192)[pyproject.toml 34-35](https://github.com/tenstorrent/riscv-coretp/blob/e4fb9eea/pyproject.toml#L34-L35)

## Best Practices

### Memory Region Configuration

When defining memory regions with specific page table entry characteristics:

`# Exclude specific flags to test fault conditionsmem = Memory(    size=0x1000,    page_size=PageSize.SIZE_4K,    flags=PageFlags.VALID | PageFlags.READ | PageFlags.WRITE,    exclude_flags=PageFlags.ACCESSED | PageFlags.DIRTY,    modify=True,  # Allow PTE modification)`
### CSR Bit Manipulation

Use explicit bit positions for clarity:

`# Good: Clear intentadue_bit_position = 61enable_svadu = CsrWrite(csr_name="menvcfg", set_mask=1 << adue_bit_position) # Better: Document bit meaningMENVCFG_ADUE_BIT = 61  # Hardware A/D bit update enableenable_svadu = CsrWrite(csr_name="menvcfg", set_mask=1 << MENVCFG_ADUE_BIT)`
### Test Scenario Documentation

Each scenario should have a clear docstring:

`@my_extension_scenariodef SID_XXX_01_descriptive_name():    """    Brief one-line summary of what is being tested.        Additional details about:    - Preconditions    - Expected behavior    - Postconditions    """    # Implementation`
### Avoiding Common Pitfalls

| Issue | Problem | Solution |
| --- | --- | --- |
| Late registration | Adding scenarios after `get_plan()` called | Ensure all scenario imports happen before plan retrieval |
| Missing steps list | Steps defined but not added to list | Always append steps to list or pass directly to `from_steps()` |
| Incorrect step order | Memory operations before allocation | Define Memory first, then operations that reference it |
| Missing environment | No `TestEnvCfg` specified | Always provide env parameter to `from_steps()` |

**Sources:**[coretp/plans/svadu/svadu_scenarios.py 37-43](https://github.com/tenstorrent/riscv-coretp/blob/e4fb9eea/coretp/plans/svadu/svadu_scenarios.py#L37-L43)[coretp/plans/svadu/svadu_scenarios.py 115](https://github.com/tenstorrent/riscv-coretp/blob/e4fb9eea/coretp/plans/svadu/svadu_scenarios.py#L115-L115)[coretp/plans/test_plan_registry.py 65-69](https://github.com/tenstorrent/riscv-coretp/blob/e4fb9eea/coretp/plans/test_plan_registry.py#L65-L69)

## Next Steps

Now that you understand the development fundamentals, proceed to:

1.   **[Creating New Test Plans](https://deepwiki.com/tenstorrent/riscv-coretp/6.1-creating-new-test-plans)** - Step-by-step guide for adding a new extension test plan
2.   **[Writing Test Scenarios](https://deepwiki.com/tenstorrent/riscv-coretp/6.2-writing-test-scenarios)** - Detailed patterns for composing effective test scenarios
3.   **[Extending the Instruction Catalog](https://deepwiki.com/tenstorrent/riscv-coretp/6.3-extending-the-instruction-catalog)** - Adding new RISC-V instructions to the framework

For understanding the systems you'll be working with:

*   **[Test Plan Registry System](https://deepwiki.com/tenstorrent/riscv-coretp/3.1-test-plan-registry-system)** - Deep dive into registration mechanics
*   **[Test Step System](https://deepwiki.com/tenstorrent/riscv-coretp/3.3-test-step-system)** - Complete reference of available test primitives
*   **[Test Steps Reference](https://deepwiki.com/tenstorrent/riscv-coretp/5-test-steps-reference)** - API documentation for all TestStep types

**Sources:**[coretp/plans/test_plan_registry.py 1-192](https://github.com/tenstorrent/riscv-coretp/blob/e4fb9eea/coretp/plans/test_plan_registry.py#L1-L192)[coretp/plans/svadu/svadu_scenarios.py 1-210](https://github.com/tenstorrent/riscv-coretp/blob/e4fb9eea/coretp/plans/svadu/svadu_scenarios.py#L1-L210)[coretp/plans/zicntr_zihpm_sscounterenw/zicntr_zihpm_sscounterenw_scenarios.py 1-567](https://github.com/tenstorrent/riscv-coretp/blob/e4fb9eea/coretp/plans/zicntr_zihpm_sscounterenw/zicntr_zihpm_sscounterenw_scenarios.py#L1-L567)

Dismiss
Refresh this wiki

Enter email to refresh
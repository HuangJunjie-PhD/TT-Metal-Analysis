---
project: riscv-coretp
github: https://github.com/tenstorrent/riscv-coretp
deepwiki: https://deepwiki.com/tenstorrent/riscv-coretp/5-test-steps-reference
---

# Test Steps Reference

Relevant source files
*   [coretp/plans/sstc/__init__.py](https://github.com/tenstorrent/riscv-coretp/blob/e4fb9eea/coretp/plans/sstc/__init__.py)
*   [coretp/plans/sstc/sstc_scenarios.py](https://github.com/tenstorrent/riscv-coretp/blob/e4fb9eea/coretp/plans/sstc/sstc_scenarios.py)
*   [coretp/step/__init__.py](https://github.com/tenstorrent/riscv-coretp/blob/e4fb9eea/coretp/step/__init__.py)
*   [coretp/step/csr.py](https://github.com/tenstorrent/riscv-coretp/blob/e4fb9eea/coretp/step/csr.py)
*   [coretp/step/memory.py](https://github.com/tenstorrent/riscv-coretp/blob/e4fb9eea/coretp/step/memory.py)

## Purpose and Scope

This document provides a comprehensive reference for all TestStep types available in the coretp framework. TestSteps are the fundamental building blocks for constructing test scenarios - they represent atomic operations like memory allocation, CSR access, load/store operations, and assertions that compose together to form complete test cases.

For information about creating test scenarios using these steps, see [Writing Test Scenarios](https://deepwiki.com/tenstorrent/riscv-coretp/6.2-writing-test-scenarios). For details on how test scenarios fit into test plans, see [Test Plan Registry System](https://deepwiki.com/tenstorrent/riscv-coretp/3.1-test-plan-registry-system).

**Covered in this section:**

*   TestStep base class and hierarchy
*   How TestSteps compose into scenarios
*   Data dependencies between steps
*   Categories of available TestSteps

**Detailed references for each category:**

*   [Memory Operations](https://deepwiki.com/tenstorrent/riscv-coretp/5.1-memory-operations) - Memory, CodePage, Load, Store, MemAccess, ModifyPte, ReadLeafPTE
*   [CSR Operations](https://deepwiki.com/tenstorrent/riscv-coretp/5.2-csr-operations) - CsrRead, CsrWrite
*   [Assertions and Verification](https://deepwiki.com/tenstorrent/riscv-coretp/5.3-assertions-and-verification) - AssertEqual, AssertNotEqual, AssertException
*   [Control Flow and Multi-Hart Operations](https://deepwiki.com/tenstorrent/riscv-coretp/5.4-control-flow-and-multi-hart-operations) - Hart, HartExit, Call, Arithmetic

**Sources:**[coretp/step/__init__.py 1-40](https://github.com/tenstorrent/riscv-coretp/blob/e4fb9eea/coretp/step/__init__.py#L1-L40)

* * *

## TestStep Hierarchy and Categories

The TestStep ecosystem is organized into functional categories, all inheriting from the abstract `TestStep` base class. Each concrete step type serves a specific purpose in test scenario construction.

**TestStep Categories:**

| Category | Base Class | Purpose | Concrete Types |
| --- | --- | --- | --- |
| Memory Management | `TestStep` | Define memory regions, page tables | `Memory`, `CodePage`, `ModifyPte`, `ReadLeafPTE` |
| Memory Access | `MemoryOp` | Load/store operations | `Load`, `Store`, `MemAccess` |
| Control & Status | `TestStep` | CSR manipulation | `CsrRead`, `CsrWrite` |
| Computation | `TestStep` | Arithmetic and immediate loading | `Arithmetic`, `LoadImmediateStep`, `LoadAddressStep` |
| Verification | `TestStep` | Runtime assertions | `AssertEqual`, `AssertNotEqual`, `AssertException` |
| Control Flow | `TestStep` | Multi-hart coordination | `Hart`, `HartExit`, `Call` |
| Metadata | `TestStep` | Test configuration | `Directive` |

**Sources:**[coretp/step/__init__.py 1-40](https://github.com/tenstorrent/riscv-coretp/blob/e4fb9eea/coretp/step/__init__.py#L1-L40)[coretp/step/memory.py 1-102](https://github.com/tenstorrent/riscv-coretp/blob/e4fb9eea/coretp/step/memory.py#L1-L102)[coretp/step/csr.py 1-61](https://github.com/tenstorrent/riscv-coretp/blob/e4fb9eea/coretp/step/csr.py#L1-L61)

* * *

## TestStep Composition and Data Dependencies

TestSteps form directed acyclic graphs (DAGs) through data dependencies. Steps can reference the output of previous steps, creating a chain of operations that the test framework resolves at execution time.

**Dependency Resolution:**

*   Steps execute in declaration order within a scenario
*   Steps can reference previous steps as dependencies using the step object itself
*   The framework tracks data flow and resolves dependencies at execution time
*   Circular dependencies are not permitted (enforced by DAG structure)

**Example from SSTC Test:**

The following pattern from [coretp/plans/sstc/sstc_scenarios.py 456-461](https://github.com/tenstorrent/riscv-coretp/blob/e4fb9eea/coretp/plans/sstc/sstc_scenarios.py#L456-L461) demonstrates data dependencies:

`henvcfg_read = CsrRead(csr_name="henvcfg")           # Step 1: Read CSRtop = LoadImmediateStep(imm=(1 << 63))               # Step 2: Load maskhenvcfg_masked = Arithmetic(op="and",                # Step 3: Mask operation                           src1=henvcfg_read,        #   References Step 1                           src2=top)                 #   References Step 2zero = LoadImmediateStep(imm=0)                      # Step 4: Load zeroassert_equal = AssertEqual(src1=henvcfg_masked,      # Step 5: Assert                          src2=zero)                 #   References Step 3, 4`
**Sources:**[coretp/plans/sstc/sstc_scenarios.py 456-487](https://github.com/tenstorrent/riscv-coretp/blob/e4fb9eea/coretp/plans/sstc/sstc_scenarios.py#L456-L487)

* * *

## TestStep in Test Scenario Construction

TestSteps are instantiated within test scenario functions and passed to `TestScenario.from_steps()`. The framework combines steps with test metadata and environment configuration to create executable test scenarios.

**Typical Test Scenario Structure:**

`@test_plan_decoratordef test_scenario_function():    """Test description"""    # 1. Configure state with CSR writes    setup_step = CsrWrite(csr_name="mcounteren", clear_mask=0x2)        # 2. Perform operations    operation_step = CsrRead(csr_name="time", direct_read=True)        # 3. Verify behavior with assertions    verification_step = AssertException(        cause=ExceptionCause.ILLEGAL_INSTRUCTION,        code=[operation_step]    )        # 4. Return composed scenario    return TestScenario.from_steps(        id="1",        name="test_name",        description="Test description",        env=TestEnvCfg(priv_modes=[PrivilegeMode.S]),        steps=[setup_step, operation_step, verification_step]    )`
**Sources:**[coretp/plans/sstc/sstc_scenarios.py 11-101](https://github.com/tenstorrent/riscv-coretp/blob/e4fb9eea/coretp/plans/sstc/sstc_scenarios.py#L11-L101)

* * *

## Common TestStep Patterns

### Pattern 1: CSR Access Control Testing

Tests verify that CSR access is properly gated by privilege mode and control bits. This pattern uses `CsrWrite` to configure control registers, `CsrRead` to attempt access, and `AssertException` to verify proper exception behavior.

**Example:**[coretp/plans/sstc/sstc_scenarios.py 18-31](https://github.com/tenstorrent/riscv-coretp/blob/e4fb9eea/coretp/plans/sstc/sstc_scenarios.py#L18-L31)

**Sources:**[coretp/plans/sstc/sstc_scenarios.py 11-101](https://github.com/tenstorrent/riscv-coretp/blob/e4fb9eea/coretp/plans/sstc/sstc_scenarios.py#L11-L101)

### Pattern 2: WARL Field Verification

Write-Any-Read-Legal (WARL) fields require verification that writes outside legal ranges don't take effect. This pattern writes illegal values, reads back the CSR, and asserts the value remains legal.

**Example:**[coretp/plans/sstc/sstc_scenarios.py 450-487](https://github.com/tenstorrent/riscv-coretp/blob/e4fb9eea/coretp/plans/sstc/sstc_scenarios.py#L450-L487)

**Sources:**[coretp/plans/sstc/sstc_scenarios.py 445-488](https://github.com/tenstorrent/riscv-coretp/blob/e4fb9eea/coretp/plans/sstc/sstc_scenarios.py#L445-L488)

### Pattern 3: Memory Region Operations

Memory operations require allocating regions with `Memory`, optionally populating with code using `CodePage`, and accessing with `Load`/`Store` or `MemAccess`.

**Sources:**[coretp/step/memory.py 11-73](https://github.com/tenstorrent/riscv-coretp/blob/e4fb9eea/coretp/step/memory.py#L11-L73)

### Pattern 4: Multi-Step Arithmetic and Assertion

Complex verification requires computing values through multiple arithmetic steps before asserting equality or inequality.

**Example:**[coretp/plans/sstc/sstc_scenarios.py 509-550](https://github.com/tenstorrent/riscv-coretp/blob/e4fb9eea/coretp/plans/sstc/sstc_scenarios.py#L509-L550) - Verifies `mip.stip` is read-only when `menvcfg.STCE=1`

**Sources:**[coretp/plans/sstc/sstc_scenarios.py 497-551](https://github.com/tenstorrent/riscv-coretp/blob/e4fb9eea/coretp/plans/sstc/sstc_scenarios.py#L497-L551)

* * *

## TestStep Parameter Types

TestSteps use various parameter types to support flexible test construction:

### Data Dependency Parameters

Many TestSteps accept parameters that can be either concrete values or references to previous steps:

`StepOrInt = Optional[Union[TestStep, int]]`
**Example usage:**

`# Concrete valuecsr_write = CsrWrite(csr_name="mcounteren", set_mask=0x2) # Step dependencyprevious_read = CsrRead(csr_name="mip")masked_value = Arithmetic(op="and", src1=previous_read, src2=(1 << 5))`
**Sources:**[coretp/step/csr.py 9-42](https://github.com/tenstorrent/riscv-coretp/blob/e4fb9eea/coretp/step/csr.py#L9-L42)

### Memory Configuration Parameters

Memory-related steps use specialized types from `coretp.rv_enums`:

| Parameter | Type | Purpose | Example Values |
| --- | --- | --- | --- |
| `size` | `int` | Region size in bytes | `0x1000`, `0x10000` |
| `page_size` | `PageSize` | Page granularity | `SIZE_4K`, `SIZE_2M`, `SIZE_1G` |
| `flags` | `PageFlags` | Memory protection | `VALID | READ | WRITE | EXECUTE` |
| `base_pa` | `Optional[int]` | Physical address | `0x80000000` |
| `base_va` | `Optional[int]` | Virtual address | `0x40000000` |

**Sources:**[coretp/step/memory.py 11-50](https://github.com/tenstorrent/riscv-coretp/blob/e4fb9eea/coretp/step/memory.py#L11-L50)

### CSR Operation Parameters

CSR steps support three mutually exclusive write modes:

| Mode | Parameter | Behavior |
| --- | --- | --- |
| Set bits | `set_mask` | OR with existing value |
| Clear bits | `clear_mask` | AND with ~mask |
| Full write | `value` | Replace entire value |

**Validation:**[coretp/step/csr.py 39-41](https://github.com/tenstorrent/riscv-coretp/blob/e4fb9eea/coretp/step/csr.py#L39-L41) enforces mutual exclusivity.

**Sources:**[coretp/step/csr.py 12-42](https://github.com/tenstorrent/riscv-coretp/blob/e4fb9eea/coretp/step/csr.py#L12-L42)

* * *

## Categories Reference

The following subsections provide detailed reference documentation for each category of TestSteps:

*   **[Memory Operations](https://deepwiki.com/tenstorrent/riscv-coretp/5.1-memory-operations)** - Complete reference for `Memory`, `CodePage`, `Load`, `Store`, `MemAccess`, `ModifyPte`, and `ReadLeafPTE` including parameter details, usage examples, and page table manipulation

*   **[CSR Operations](https://deepwiki.com/tenstorrent/riscv-coretp/5.2-csr-operations)** - Detailed documentation of `CsrRead` and `CsrWrite` with privilege mode handling, direct vs. indirect access, and examples from supervisor-mode extensions

*   **[Assertions and Verification](https://deepwiki.com/tenstorrent/riscv-coretp/5.3-assertions-and-verification)** - Reference for `AssertEqual`, `AssertNotEqual`, and `AssertException` including exception cause handling and verification patterns

*   **[Control Flow and Multi-Hart Operations](https://deepwiki.com/tenstorrent/riscv-coretp/5.4-control-flow-and-multi-hart-operations)** - Documentation for `Hart`, `HartExit`, `Call`, and `Arithmetic` steps used for multi-processor coordination, control flow, and computational operations

**Sources:**[coretp/step/__init__.py 1-40](https://github.com/tenstorrent/riscv-coretp/blob/e4fb9eea/coretp/step/__init__.py#L1-L40)

* * *

## TestStep Immutability and Frozen Dataclasses

All TestStep types are implemented as frozen dataclasses, making them immutable after instantiation. This design ensures:

1.   **Reproducibility** - Test scenarios produce consistent results across executions
2.   **Safety** - Steps cannot be accidentally modified after creation
3.   **Shareability** - Steps can be safely referenced by multiple consumers

**Implementation pattern:**

`@dataclass(frozen=True)class CsrWrite(TestStep):    csr_name: str = ""    set_mask: StepOrInt = None    clear_mask: StepOrInt = None    # ...`
**Sources:**[coretp/step/csr.py 12-42](https://github.com/tenstorrent/riscv-coretp/blob/e4fb9eea/coretp/step/csr.py#L12-L42)[coretp/step/memory.py 11-50](https://github.com/tenstorrent/riscv-coretp/blob/e4fb9eea/coretp/step/memory.py#L11-L50)

Dismiss
Refresh this wiki

Enter email to refresh
---
project: riscv-coretp
github: https://github.com/tenstorrent/riscv-coretp
deepwiki: https://deepwiki.com/tenstorrent/riscv-coretp/4-test-plans-overview
---

# Test Plans Overview

Relevant source files
*   [coretp/isa/instructions/misc.py](https://github.com/tenstorrent/riscv-coretp/blob/e4fb9eea/coretp/isa/instructions/misc.py)
*   [coretp/plans/__init__.py](https://github.com/tenstorrent/riscv-coretp/blob/e4fb9eea/coretp/plans/__init__.py)
*   [coretp/plans/sstc/__init__.py](https://github.com/tenstorrent/riscv-coretp/blob/e4fb9eea/coretp/plans/sstc/__init__.py)
*   [coretp/plans/sstc/sstc_scenarios.py](https://github.com/tenstorrent/riscv-coretp/blob/e4fb9eea/coretp/plans/sstc/sstc_scenarios.py)
*   [coretp/plans/svadu/__init__.py](https://github.com/tenstorrent/riscv-coretp/blob/e4fb9eea/coretp/plans/svadu/__init__.py)
*   [coretp/plans/svadu/svadu_scenarios.py](https://github.com/tenstorrent/riscv-coretp/blob/e4fb9eea/coretp/plans/svadu/svadu_scenarios.py)
*   [coretp/plans/zicbom_zicboz_zicbop/zicbom_zicboz_zicbop_scenarios.py](https://github.com/tenstorrent/riscv-coretp/blob/e4fb9eea/coretp/plans/zicbom_zicboz_zicbop/zicbom_zicboz_zicbop_scenarios.py)
*   [coretp/step/csr.py](https://github.com/tenstorrent/riscv-coretp/blob/e4fb9eea/coretp/step/csr.py)

## Purpose and Scope

This document provides an overview of all test plans implemented in the coretp framework. Test plans organize test scenarios by RISC-V extension or functional area, providing comprehensive verification coverage for processor implementations. Each test plan consists of multiple scenarios that validate specific behaviors, edge cases, and compliance requirements.

For detailed information about individual test plan implementations, see:

*   Cache Management Extensions: [Section 4.1](https://deepwiki.com/tenstorrent/riscv-coretp/4.1-cache-management-extensions-(zicbomzicbozzicbop))
*   Virtual Memory Extensions: [Section 4.2](https://deepwiki.com/tenstorrent/riscv-coretp/4.2-virtual-memory-extensions)
*   Supervisor Mode Extensions: [Section 4.3](https://deepwiki.com/tenstorrent/riscv-coretp/4.3-supervisor-mode-extensions)

For information on creating new test plans, see [Creating New Test Plans](https://deepwiki.com/tenstorrent/riscv-coretp/6.1-creating-new-test-plans).

## Test Plan Organization

Test plans in the coretp framework are organized into three major functional areas based on the RISC-V architectural domains they verify:

### Cache Management Extensions

The cache management test plan covers the ZICBOM (Cache Block Management), ZICBOZ (Cache Block Zero), and ZICBOP (Cache Block Prefetch) extensions. This is the most extensive test plan with 47+ scenarios testing cache block operations (`cbo.clean`, `cbo.flush`, `cbo.inval`, `cbo.zero`) and prefetch variants (`prefetch.i`, `prefetch.r`, `prefetch.w`). Tests verify CSR-based control via `menvcfg`, `senvcfg`, and `henvcfg`, privilege mode access control, multi-processor synchronization, and interactions with atomic LR/SC operations.

### Virtual Memory Extensions

Virtual memory test plans verify paging behavior and hardware-managed page table entry updates:

*   **SVADU (Supervisor Virtual Address Update)**: Tests hardware-managed Accessed and Dirty bit updates in page table entries, fault behavior when bits are cleared, and WARL (Write Any, Read Legal) behavior across SV39/48/57 paging modes
*   **Paging**: Comprehensive page table walk tests covering boundary conditions, alignment faults, permission encodings, and global bit handling

### Supervisor Mode Extensions

Supervisor mode test plans verify privileged CSR access and interrupt mechanisms:

*   **SSTC (Supervisor Timer Counter)**: Tests `stimecmp`/`vstimecmp` CSRs, counter enable hierarchies via `mcounteren`/`hcounteren`/`scounteren`, and privilege mode access control
*   **ZICNTR/ZIHPM/SSCOUNTERENW**: Tests counter enable CSR access control across privilege modes
*   **SSCOFPMF (Supervisor Counter Overflow and Privilege Mode Filtering)**: Tests PMU counter overflow detection, LCOFIP interrupts, and `scountovf` shadow copies

**Sources:**[coretp/plans/__init__.py 1-21](https://github.com/tenstorrent/riscv-coretp/blob/e4fb9eea/coretp/plans/__init__.py#L1-L21) Diagram 2 and Diagram 6 from high-level architecture

## Test Plan Registration System

### Registration Mechanism

Test plans are registered using the `new_test_plan()` function, which returns a decorator for marking test scenario functions. This decorator pattern enables automatic discovery and organization of test scenarios.

**Diagram: Test Plan Registration Flow**

The registration system provides three key functions for test plan discovery:

*   `get_plan(name)`: Retrieve a specific test plan by name
*   `list_plans()`: List all registered test plan names
*   `query_plans(tags=[], features=[])`: Query test plans by tags or features

**Sources:**[coretp/plans/test_plan_registry.py](https://github.com/tenstorrent/riscv-coretp/blob/e4fb9eea/coretp/plans/test_plan_registry.py)[coretp/plans/__init__.py 5](https://github.com/tenstorrent/riscv-coretp/blob/e4fb9eea/coretp/plans/__init__.py#L5-L5)

### Example Test Plan Definitions

**ZICBOM/ZICBOZ/ZICBOP Test Plan:**

`zicbom_zicboz_zicbop_scenario = new_test_plan(    name="zicbom_zicboz_zicbop",    description="Cache Management Operations",    tags=["cache", "cmo", "zicbom", "zicboz", "zicbop"])`
**SVADU Test Plan:**

`svadu_scenario = new_test_plan(    name="svadu",    description="Covers SVADU (Supervisor-mode Virtual Address Update) behavior including hardware A/D bit updates",    tags=["svadu", "paging", "hardware_ad_update"])`
**SSTC Test Plan:**

`sstc_scenario = new_test_plan(    name="sstc",    description="Covers SSTC (Supervisor-mode Timer Counter) behavior",    tags=["sstc", "timer", "supervisor", "interrupt"])`
**Sources:**[coretp/plans/zicbom_zicboz_zicbop/__init__.py](https://github.com/tenstorrent/riscv-coretp/blob/e4fb9eea/coretp/plans/zicbom_zicboz_zicbop/__init__.py)[coretp/plans/svadu/__init__.py 1-15](https://github.com/tenstorrent/riscv-coretp/blob/e4fb9eea/coretp/plans/svadu/__init__.py#L1-L15)[coretp/plans/sstc/__init__.py 1-15](https://github.com/tenstorrent/riscv-coretp/blob/e4fb9eea/coretp/plans/sstc/__init__.py#L1-L15)

## Test Plan Coverage Summary

The following table summarizes all test plans currently implemented in the coretp framework:

| Test Plan | Extension(s) | Scenario Count | Primary Focus | Importance Score |
| --- | --- | --- | --- | --- |
| zicbom_zicboz_zicbop | ZICBOM, ZICBOZ, ZICBOP | 47+ | Cache block operations, CMO instructions | 35.33 |
| svadu | Svadu | 4 | Hardware A/D bit management | 22.40 |
| sstc | Sstc | 6 | Timer CSRs, counter enable | 13.33 |
| paging | Base Paging | 12 | Page table walks, fault conditions | 3.38 |
| zicntr_zihpm_sscounterenw | Zicntr, Zihpm, Sscounterenw | Multiple | Counter enable, HPM access | 9.56 |
| sscofpmf | Sscofpmf | Multiple | PMU overflow, interrupts | 7.50 |
| zicond | Zicond | Multiple | Conditional zeroing | - |
| zkt | Zkt | Multiple | Timing side-channel resistance | - |
| zimop_zcmop | Zimop, Zcmop | Multiple | May-be-operations | - |
| zifencei | Zifencei | Multiple | Instruction fetch fence | - |

**Sources:**[coretp/plans/__init__.py 8-18](https://github.com/tenstorrent/riscv-coretp/blob/e4fb9eea/coretp/plans/__init__.py#L8-L18) Diagram 6 from high-level architecture

## Test Scenario Structure

### Scenario Definition Pattern

Test scenarios follow a consistent structure using the decorator pattern and the `TestScenario.from_steps()` factory method:

**Diagram: Test Scenario Construction Pattern**

**Sources:**[coretp/plans/zicbom_zicboz_zicbop/zicbom_zicboz_zicbop_scenarios.py 12-43](https://github.com/tenstorrent/riscv-coretp/blob/e4fb9eea/coretp/plans/zicbom_zicboz_zicbop/zicbom_zicboz_zicbop_scenarios.py#L12-L43)

### Example Scenario: ZICBOM CSR Control

The following example demonstrates a typical test scenario structure:

`@zicbom_zicboz_zicbop_scenariodef SID_ZICBO_001():    """    Cover cbo.inval when menvcfg.CBIE = 00 from all lower privilege modes    """    # Set up memory region    mem = Memory(        size=0x1000,        page_size=PageSize.SIZE_4K,        flags=PageFlags.VALID | PageFlags.READ | PageFlags.WRITE | PageFlags.EXECUTE,    )     # Configure menvcfg.CBIE = 00    menvcfg_write = CsrWrite(csr_name="menvcfg", clear_mask=0x30)     # Execute cbo.inval instruction    cbo_inval = MemAccess(op="cbo.inval", memory=mem)     # Check for illegal instruction exception    assert_exception = AssertException(        cause=ExceptionCause.ILLEGAL_INSTRUCTION,         code=[cbo_inval]    )     return TestScenario.from_steps(        id="1",        name="SID_ZICBO_001",        description="Cover cbo.inval when menvcfg.CBIE = 00 from all lower privilege modes",        env=TestEnvCfg(priv_modes=[PrivilegeMode.U, PrivilegeMode.S]),        steps=[mem, menvcfg_write, assert_exception],    )`
This scenario tests that `cbo.inval` triggers an illegal instruction exception when the cache block invalidate enable bits (`CBIE`) in `menvcfg` are cleared, verifying proper privilege mode access control.

**Sources:**[coretp/plans/zicbom_zicboz_zicbop/zicbom_zicboz_zicbop_scenarios.py 12-43](https://github.com/tenstorrent/riscv-coretp/blob/e4fb9eea/coretp/plans/zicbom_zicboz_zicbop/zicbom_zicboz_zicbop_scenarios.py#L12-L43)

### Example Scenario: SVADU Hardware Update

The following example demonstrates SVADU hardware A/D bit testing:

`@svadu_scenariodef SID_SVADU_02_hardware_update_a_bit():    """    When SVADU enabled (menvcfg.adue=1) and pte pa mem_type=cacheable,    memory access should update pte.a bit if pte.a=0    """    mem = Memory(        size=0x1000,        page_size=PageSize.SIZE_4K,        flags=PageFlags.VALID | PageFlags.READ | PageFlags.WRITE,        exclude_flags=PageFlags.ACCESSED | PageFlags.DIRTY,        modify=True,    )     # Enable SVADU    enable_svadu = CsrWrite(csr_name="menvcfg", set_mask=1 << 61)     # Verify A bit is initially 0    first_read_leaf_pte = ReadLeafPTE(memory=mem)    load_first_immediate_mask_check = LoadImmediateStep(imm=1 << 6)    and_first_op = Arithmetic(op="and", src1=first_read_leaf_pte,                               src2=load_first_immediate_mask_check)    zero = LoadImmediateStep(imm=0)    assert_equal_first = AssertEqual(src1=and_first_op, src2=zero)     # Perform load - should update A bit    load_op = Load(memory=mem)     # Verify A bit is now set    read_leaf_pte = ReadLeafPTE(memory=mem)    load_immediate_mask_check = LoadImmediateStep(imm=1 << 6)    and_op = Arithmetic(op="and", src1=read_leaf_pte,                         src2=load_immediate_mask_check)    assert_equal = AssertEqual(src1=and_op, src2=load_immediate_mask_check)     return TestScenario.from_steps(        id="3",        name="SID_SVADU_02_hardware_update_a_bit",        description="SVADU enabled: hardware updates pte.a bit on memory access",        env=TestEnvCfg(paging_modes=[PagingMode.SV39, PagingMode.SV48, PagingMode.SV57]),        steps=[mem, enable_svadu, first_read_leaf_pte, load_first_immediate_mask_check,               and_first_op, zero, assert_equal_first, load_op, read_leaf_pte,               load_immediate_mask_check, and_op, assert_equal],    )`
This scenario verifies that when SVADU is enabled, hardware automatically sets the Accessed bit (bit 6) in the page table entry on memory access.

**Sources:**[coretp/plans/svadu/svadu_scenarios.py 100-152](https://github.com/tenstorrent/riscv-coretp/blob/e4fb9eea/coretp/plans/svadu/svadu_scenarios.py#L100-L152)

### Example Scenario: SSTC Counter Enable

The following example demonstrates SSTC CSR access control testing:

`@sstc_scenariodef SID_SSTC_01():    """    Test mcounteren.tm=0, hcounteren.tm = 0/1, scounteren.tm = 0/1    Access to stimecmp, vstimecmp, & time csr is blocked in modes below M     when mcounteren.tm=0.    """    # Set mcounteren.tm=0    mcounteren_clear = CsrWrite(csr_name="mcounteren", clear_mask=0x2)     # Try accessing time CSR - should cause illegal instruction exception    time_read_1 = CsrRead(csr_name="time", direct_read=True)    assert_time_exception_1 = AssertException(        cause=ExceptionCause.ILLEGAL_INSTRUCTION,         code=[time_read_1]    )     # Try accessing stimecmp CSR - should cause illegal instruction exception    stimecmp_read_1 = CsrRead(csr_name="stimecmp", direct_read=True)    assert_stimecmp_exception_1 = AssertException(        cause=ExceptionCause.ILLEGAL_INSTRUCTION,         code=[stimecmp_read_1]    )     return TestScenario.from_steps(        id="1",        name="SID_SSTC_01",        description="Access to stimecmp, vstimecmp, & time blocked when mcounteren.tm=0",        env=TestEnvCfg(            priv_modes=[PrivilegeMode.S, PrivilegeMode.U],             hypervisor=[True, False],             virtualized=[True, False]        ),        steps=[mcounteren_clear, assert_time_exception_1,                assert_stimecmp_exception_1, ...],    )`
This scenario verifies hierarchical counter enable control, ensuring that clearing `mcounteren.tm` blocks access to timer-related CSRs in all lower privilege modes regardless of `hcounteren` and `scounteren` settings.

**Sources:**[coretp/plans/sstc/sstc_scenarios.py 11-101](https://github.com/tenstorrent/riscv-coretp/blob/e4fb9eea/coretp/plans/sstc/sstc_scenarios.py#L11-L101)

## Test Plan Coverage by Functional Domain

The following diagram illustrates the test coverage organization across RISC-V architectural domains:

**Diagram: Test Coverage by Functional Domain**

**Sources:** Diagram 6 from high-level architecture, [coretp/plans/__init__.py 8-18](https://github.com/tenstorrent/riscv-coretp/blob/e4fb9eea/coretp/plans/__init__.py#L8-L18)

## Test Step Composition

Test scenarios are composed from primitive `TestStep` objects that define individual operations. The following diagram shows the common test step types used across all test plans:

**Diagram: Test Step Composition in Scenarios**

**Sources:**[coretp/step/__init__.py](https://github.com/tenstorrent/riscv-coretp/blob/e4fb9eea/coretp/step/__init__.py)[coretp/plans/zicbom_zicboz_zicbop/zicbom_zicboz_zicbop_scenarios.py 6-7](https://github.com/tenstorrent/riscv-coretp/blob/e4fb9eea/coretp/plans/zicbom_zicboz_zicbop/zicbom_zicboz_zicbop_scenarios.py#L6-L7)[coretp/plans/svadu/svadu_scenarios.py 6-27](https://github.com/tenstorrent/riscv-coretp/blob/e4fb9eea/coretp/plans/svadu/svadu_scenarios.py#L6-L27)[coretp/plans/sstc/sstc_scenarios.py 6](https://github.com/tenstorrent/riscv-coretp/blob/e4fb9eea/coretp/plans/sstc/sstc_scenarios.py#L6-L6)

## Common Test Patterns

### CSR Access Control Pattern

Many test scenarios follow a pattern of testing hierarchical CSR access control across privilege modes:

1.   Configure control CSR (e.g., `menvcfg`, `mcounteren`)
2.   Attempt access to controlled CSR (e.g., `stimecmp`, `time`)
3.   Verify expected exception or success

This pattern appears in:

*   ZICBOM/ZICBOZ scenarios testing `menvcfg.CBIE`, `menvcfg.CBCFE`, `menvcfg.CBZE`
*   SSTC scenarios testing `mcounteren.tm`, `hcounteren.tm`, `scounteren.tm`
*   Counter enable scenarios testing `mcounteren`, `scounteren` for HPM counters

**Sources:**[coretp/plans/zicbom_zicboz_zicbop/zicbom_zicboz_zicbop_scenarios.py 12-77](https://github.com/tenstorrent/riscv-coretp/blob/e4fb9eea/coretp/plans/zicbom_zicboz_zicbop/zicbom_zicboz_zicbop_scenarios.py#L12-L77)[coretp/plans/sstc/sstc_scenarios.py 11-101](https://github.com/tenstorrent/riscv-coretp/blob/e4fb9eea/coretp/plans/sstc/sstc_scenarios.py#L11-L101)

### Hardware Update Verification Pattern

SVADU scenarios follow a pattern for verifying hardware-managed updates:

1.   Allocate memory with specific PTE flags
2.   Enable hardware update mechanism (e.g., `menvcfg.adue`)
3.   Verify initial PTE state (e.g., A bit cleared)
4.   Perform triggering operation (e.g., load/store)
5.   Verify PTE state change (e.g., A bit set)

This pattern uses `ReadLeafPTE`, `Arithmetic` for bit masking, and `AssertEqual` for verification.

**Sources:**[coretp/plans/svadu/svadu_scenarios.py 100-209](https://github.com/tenstorrent/riscv-coretp/blob/e4fb9eea/coretp/plans/svadu/svadu_scenarios.py#L100-L209)

### Multi-Processor Synchronization Pattern

Cache management scenarios test multi-processor behavior using:

1.   `Hart(hart_index=0)` to specify execution on hart 0
2.   Perform operation (e.g., store data)
3.   `HartExit(sync=True)` for synchronization barrier
4.   `Hart(hart_index=1)` to switch to hart 1
5.   Perform verification operation

This pattern ensures proper ordering and visibility of cache operations across harts.

**Sources:**[coretp/plans/zicbom_zicboz_zicbop/zicbom_zicboz_zicbop_scenarios.py 748-797](https://github.com/tenstorrent/riscv-coretp/blob/e4fb9eea/coretp/plans/zicbom_zicboz_zicbop/zicbom_zicboz_zicbop_scenarios.py#L748-L797)

## Environment Configuration

Test scenarios specify execution environment requirements through `TestEnvCfg`, which defines:

*   **Privilege Modes**: `priv_modes=[PrivilegeMode.U, PrivilegeMode.S, PrivilegeMode.M]`
*   **Paging Modes**: `paging_modes=[PagingMode.SV39, PagingMode.SV48, PagingMode.SV57]`
*   **Hypervisor Support**: `hypervisor=[True, False]`
*   **Virtualization**: `virtualized=[True, False]`
*   **Hart Count**: `min_num_harts=2` for multi-processor tests

The `TestEnvSolver` processes these configurations to generate concrete test environments that satisfy the specified constraints. For more details on environment configuration, see [Test Environment Configuration](https://deepwiki.com/tenstorrent/riscv-coretp/3.2-test-environment-configuration).

**Sources:**[coretp/plans/zicbom_zicboz_zicbop/zicbom_zicboz_zicbop_scenarios.py 37](https://github.com/tenstorrent/riscv-coretp/blob/e4fb9eea/coretp/plans/zicbom_zicboz_zicbop/zicbom_zicboz_zicbop_scenarios.py#L37-L37)[coretp/plans/svadu/svadu_scenarios.py 56](https://github.com/tenstorrent/riscv-coretp/blob/e4fb9eea/coretp/plans/svadu/svadu_scenarios.py#L56-L56)[coretp/plans/sstc/sstc_scenarios.py 82](https://github.com/tenstorrent/riscv-coretp/blob/e4fb9eea/coretp/plans/sstc/sstc_scenarios.py#L82-L82)

## Scenario Naming Conventions

Test scenarios follow consistent naming conventions:

*   **Function Names**: `SID_<EXTENSION>_<NUMBER>` (e.g., `SID_ZICBO_001`, `SID_SVADU_02_hardware_update_a_bit`)
*   **Scenario IDs**: Sequential numeric strings (e.g., `"1"`, `"2"`, `"3"`)
*   **Scenario Names**: Match function names for traceability
*   **Descriptions**: Brief natural language description of the test objective

These conventions enable easy identification and cross-referencing of test scenarios in documentation, test reports, and code.

**Sources:**[coretp/plans/zicbom_zicboz_zicbop/zicbom_zicboz_zicbop_scenarios.py 34-36](https://github.com/tenstorrent/riscv-coretp/blob/e4fb9eea/coretp/plans/zicbom_zicboz_zicbop/zicbom_zicboz_zicbop_scenarios.py#L34-L36)[coretp/plans/svadu/svadu_scenarios.py 52-55](https://github.com/tenstorrent/riscv-coretp/blob/e4fb9eea/coretp/plans/svadu/svadu_scenarios.py#L52-L55)[coretp/plans/sstc/sstc_scenarios.py 78-81](https://github.com/tenstorrent/riscv-coretp/blob/e4fb9eea/coretp/plans/sstc/sstc_scenarios.py#L78-L81)

## Additional Test Plans

Beyond the major test plans described above, the coretp framework includes test plans for additional RISC-V extensions:

*   **ZICOND**: Conditional zeroing instructions (`czero.eqz`, `czero.nez`)
*   **ZKT**: Cryptographic timing side-channel resistance
*   **ZIMOP/ZCMOP**: May-be-operation extensions for instruction encoding space reservation
*   **ZIFENCEI**: Instruction fetch fence operations

These test plans follow the same structural patterns and registration mechanisms as the major test plans.

**Sources:**[coretp/plans/__init__.py 12-16](https://github.com/tenstorrent/riscv-coretp/blob/e4fb9eea/coretp/plans/__init__.py#L12-L16)

Dismiss
Refresh this wiki

Enter email to refresh
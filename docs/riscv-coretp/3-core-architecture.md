---
project: riscv-coretp
github: https://github.com/tenstorrent/riscv-coretp
deepwiki: https://deepwiki.com/tenstorrent/riscv-coretp/3-core-architecture
---

# Core Architecture

Relevant source files
*   [coretp/env/cfg.py](https://github.com/tenstorrent/riscv-coretp/blob/e4fb9eea/coretp/env/cfg.py)
*   [coretp/plans/__init__.py](https://github.com/tenstorrent/riscv-coretp/blob/e4fb9eea/coretp/plans/__init__.py)
*   [coretp/plans/svadu/__init__.py](https://github.com/tenstorrent/riscv-coretp/blob/e4fb9eea/coretp/plans/svadu/__init__.py)
*   [coretp/plans/test_plan_registry.py](https://github.com/tenstorrent/riscv-coretp/blob/e4fb9eea/coretp/plans/test_plan_registry.py)
*   [coretp/plans/zicntr_zihpm_sscounterenw/zicntr_zihpm_sscounterenw_scenarios.py](https://github.com/tenstorrent/riscv-coretp/blob/e4fb9eea/coretp/plans/zicntr_zihpm_sscounterenw/zicntr_zihpm_sscounterenw_scenarios.py)
*   [coretp/step/__init__.py](https://github.com/tenstorrent/riscv-coretp/blob/e4fb9eea/coretp/step/__init__.py)

## Purpose and Scope

This document explains the fundamental architecture of the coretp framework, including its layered design and main subsystems. The framework provides a declarative approach to defining RISC-V processor verification tests through composable primitives organized into distinct layers.

For detailed information about specific subsystems, see:

*   Test plan registration mechanics: [Test Plan Registry System](https://deepwiki.com/tenstorrent/riscv-coretp/3.1-test-plan-registry-system)
*   Environment configuration and resolution: [Test Environment Configuration](https://deepwiki.com/tenstorrent/riscv-coretp/3.2-test-environment-configuration)
*   Test step primitives and composition: [Test Step System](https://deepwiki.com/tenstorrent/riscv-coretp/3.3-test-step-system)
*   Instruction definitions and filtering: [Instruction Catalog System](https://deepwiki.com/tenstorrent/riscv-coretp/3.4-instruction-catalog-system)

## Architectural Overview

The coretp framework is organized into five distinct layers that separate concerns and enable modular test development:

**Diagram: Layered Architecture and Code Entities**

**Sources:**[coretp/plans/test_plan_registry.py 1-192](https://github.com/tenstorrent/riscv-coretp/blob/e4fb9eea/coretp/plans/test_plan_registry.py#L1-L192)[coretp/env/cfg.py 1-39](https://github.com/tenstorrent/riscv-coretp/blob/e4fb9eea/coretp/env/cfg.py#L1-L39)[coretp/step/__init__.py 1-40](https://github.com/tenstorrent/riscv-coretp/blob/e4fb9eea/coretp/step/__init__.py#L1-L40)

## Layer Descriptions

### Test Definition Layer

The Test Definition Layer provides the organizational structure for test plans through a central registry system. Test plans are collections of related test scenarios targeting specific RISC-V extensions or features.

**Key Components:**

| Component | Location | Purpose |
| --- | --- | --- |
| `_TestPlanRegistry` | [coretp/plans/test_plan_registry.py 82-138](https://github.com/tenstorrent/riscv-coretp/blob/e4fb9eea/coretp/plans/test_plan_registry.py#L82-L138) | Global singleton registry storing all test plans |
| `_TestPlanInfo` | [coretp/plans/test_plan_registry.py 44-80](https://github.com/tenstorrent/riscv-coretp/blob/e4fb9eea/coretp/plans/test_plan_registry.py#L44-L80) | Internal metadata and scenario storage for each plan |
| `new_test_plan()` | [coretp/plans/test_plan_registry.py 144-164](https://github.com/tenstorrent/riscv-coretp/blob/e4fb9eea/coretp/plans/test_plan_registry.py#L144-L164) | Decorator function for registering test scenarios |
| `get_plan()` | [coretp/plans/test_plan_registry.py 167-173](https://github.com/tenstorrent/riscv-coretp/blob/e4fb9eea/coretp/plans/test_plan_registry.py#L167-L173) | Retrieves built TestPlan by name |
| `query_plans()` | [coretp/plans/test_plan_registry.py 181-191](https://github.com/tenstorrent/riscv-coretp/blob/e4fb9eea/coretp/plans/test_plan_registry.py#L181-L191) | Filters plans by tags and features |

Test plans use lazy construction - scenarios are accumulated via decorators until the plan is first requested, after which it becomes immutable. All test plan modules are imported in [coretp/plans/__init__.py 8-18](https://github.com/tenstorrent/riscv-coretp/blob/e4fb9eea/coretp/plans/__init__.py#L8-L18) to ensure registration at module load time.

**Diagram: Test Plan Registration Flow**

**Sources:**[coretp/plans/test_plan_registry.py 1-192](https://github.com/tenstorrent/riscv-coretp/blob/e4fb9eea/coretp/plans/test_plan_registry.py#L1-L192)[coretp/plans/__init__.py 1-21](https://github.com/tenstorrent/riscv-coretp/blob/e4fb9eea/coretp/plans/__init__.py#L1-L21)

### Test Configuration Layer

The Test Configuration Layer defines and resolves test environments. A `TestEnvCfg` specifies the parameter space (privilege modes, paging modes, register widths), while `TestEnvSolver` applies validation predicates to generate valid `TestEnv` instances.

**Key Components:**

| Component | Location | Purpose |
| --- | --- | --- |
| `TestEnvCfg` | [coretp/env/cfg.py 11-39](https://github.com/tenstorrent/riscv-coretp/blob/e4fb9eea/coretp/env/cfg.py#L11-L39) | Template defining test parameter space |
| `TestEnv` | [coretp/env/env.py](https://github.com/tenstorrent/riscv-coretp/blob/e4fb9eea/coretp/env/env.py) | Concrete environment with specific parameters |
| `TestEnvSolver` | [coretp/env/solver.py](https://github.com/tenstorrent/riscv-coretp/blob/e4fb9eea/coretp/env/solver.py) | Validates and filters environment combinations |

The `TestEnvCfg.generate_all_cfgs()` method at [coretp/env/cfg.py 28-38](https://github.com/tenstorrent/riscv-coretp/blob/e4fb9eea/coretp/env/cfg.py#L28-L38) produces all combinations of parameters through cartesian product, which can then be filtered by `TestEnvSolver` based on architectural constraints (e.g., hypervisor requires supervisor mode).

**Configuration Parameters:**

`# From TestEnvCfg dataclassreg_widths: list[int]                    # 32 or 64 bitpriv_modes: list[PrivilegeMode]         # M, S, Uhypervisor: list[bool]                   # H extension supportpaging_modes: list[PagingMode]          # DISABLED, SV39, SV48, SV57page_sizes: list[PageSize]              # 4K, 2M, 1Gmin_num_harts: int                       # Multi-processor supportvirtualized: list[bool]                  # Virtual machine contextdeleg_excp_to: list[PrivilegeMode]      # Exception delegation target`
**Sources:**[coretp/env/cfg.py 1-39](https://github.com/tenstorrent/riscv-coretp/blob/e4fb9eea/coretp/env/cfg.py#L1-L39)

### Test Execution Layer

The Test Execution Layer provides primitive operations (TestSteps) that compose into test scenarios. All steps inherit from the abstract `TestStep` base class.

**Diagram: TestStep Hierarchy and Module Organization**

**Sources:**[coretp/step/__init__.py 1-40](https://github.com/tenstorrent/riscv-coretp/blob/e4fb9eea/coretp/step/__init__.py#L1-L40)

**Step Categories:**

| Category | Steps | Purpose |
| --- | --- | --- |
| Memory Management | `Memory`, `CodePage`, `ModifyPte`, `ReadLeafPTE` | Define regions, allocate pages, manipulate page tables |
| Memory Operations | `Load`, `Store`, `MemAccess` | Read/write data from memory addresses |
| CSR Operations | `CsrRead`, `CsrWrite` | Access Control and Status Registers |
| Computation | `Arithmetic`, `LoadImmediateStep`, `LoadAddressStep` | ALU operations and constant loading |
| Verification | `AssertEqual`, `AssertNotEqual`, `AssertException` | Check values and exception behavior |
| Control Flow | `Hart`, `HartExit`, `Call` | Multi-processor synchronization and function calls |

Test scenarios use these steps declaratively. For example, from [coretp/plans/zicntr_zihpm_sscounterenw/zicntr_zihpm_sscounterenw_scenarios.py 39-44](https://github.com/tenstorrent/riscv-coretp/blob/e4fb9eea/coretp/plans/zicntr_zihpm_sscounterenw/zicntr_zihpm_sscounterenw_scenarios.py#L39-L44):

`steps.append(CsrWrite(csr_name="mcounteren", set_mask=all_fields_mask))steps.append(CsrWrite(csr_name="scounteren", set_mask=all_fields_mask))for _, (_, csr_name) in COUNTER_FIELDS.items():    steps.append(CsrRead(csr_name=csr_name, direct_read=True))`
**Sources:**[coretp/step/__init__.py 1-40](https://github.com/tenstorrent/riscv-coretp/blob/e4fb9eea/coretp/step/__init__.py#L1-L40)[coretp/plans/zicntr_zihpm_sscounterenw/zicntr_zihpm_sscounterenw_scenarios.py 1-567](https://github.com/tenstorrent/riscv-coretp/blob/e4fb9eea/coretp/plans/zicntr_zihpm_sscounterenw/zicntr_zihpm_sscounterenw_scenarios.py#L1-L567)

### ISA Support Layer

The ISA Support Layer manages RISC-V instruction definitions and provides filtering capabilities based on extension, operand type, and architectural parameters.

**Key Components:**

| Component | Purpose |
| --- | --- |
| `InstructionCatalog` | Provides filtered views of instruction set based on ISA configuration |
| `InstructionDef` | Frozen template defining instruction format, operands, and extension |
| `Instruction` | Mutable runtime instance for assembly generation |
| `ALL_INSTRS` | Global collection of all defined instructions |
| `IMPLIED_EXTENSIONS` | Maps meta-extensions (G, ZK*) to their constituent extensions |

The catalog system supports extensibility - new RISC-V extensions can be added by defining `InstructionDef` templates in the appropriate module under `coretp/isa/instructions/`.

**Sources:**[coretp/isa/catalog.py](https://github.com/tenstorrent/riscv-coretp/blob/e4fb9eea/coretp/isa/catalog.py)

### Export Layer

The Export Layer provides command-line tools for generating documentation from test plans. The `coretp-export` CLI tool can produce PDF and Excel format reports containing test scenarios, requirements, and coverage metrics.

**Sources:**[coretp/plans/__init__.py 5](https://github.com/tenstorrent/riscv-coretp/blob/e4fb9eea/coretp/plans/__init__.py#L5-L5)

## Data Flow

**Diagram: Test Scenario Creation and Execution Flow**

**Sources:**[coretp/plans/test_plan_registry.py 1-192](https://github.com/tenstorrent/riscv-coretp/blob/e4fb9eea/coretp/plans/test_plan_registry.py#L1-L192)[coretp/env/cfg.py 1-39](https://github.com/tenstorrent/riscv-coretp/blob/e4fb9eea/coretp/env/cfg.py#L1-L39)

## Test Scenario Composition Pattern

Test scenarios follow a consistent composition pattern demonstrated across all test plans in the repository:

1.   **Decorator Application**: Use `@new_test_plan()` to register scenario function
2.   **Step Construction**: Build list of `TestStep` instances defining test operations
3.   **Environment Definition**: Create `TestEnvCfg` specifying parameter space
4.   **Scenario Assembly**: Call `TestScenario.from_steps()` with metadata, environment, and steps

Example from [coretp/plans/svadu/__init__.py 8-12](https://github.com/tenstorrent/riscv-coretp/blob/e4fb9eea/coretp/plans/svadu/__init__.py#L8-L12) showing registration:

`svadu_scenario = new_test_plan(    name="svadu",    description="Covers SVADU (Supervisor-mode Virtual Address Update) behavior...",    tags=["svadu", "paging", "hardware_ad_update"],)`
Example from [coretp/plans/zicntr_zihpm_sscounterenw/zicntr_zihpm_sscounterenw_scenarios.py 30-52](https://github.com/tenstorrent/riscv-coretp/blob/e4fb9eea/coretp/plans/zicntr_zihpm_sscounterenw/zicntr_zihpm_sscounterenw_scenarios.py#L30-L52) showing scenario construction:

`@zicntr_zihpm_sscounterenw_scenariodef SID_XCOUNTEREN_01_U():    description = "..."    steps = []        # Configure CSRs    steps.append(CsrWrite(csr_name="mcounteren", set_mask=all_fields_mask))    steps.append(CsrWrite(csr_name="scounteren", set_mask=all_fields_mask))        # Test counter access    for _, (_, csr_name) in COUNTER_FIELDS.items():        steps.append(CsrRead(csr_name=csr_name, direct_read=True))        # Assemble scenario    return TestScenario.from_steps(        id="1",        name="SID_XCOUNTEREN_01_U",        description=description,        env=TestEnvCfg(priv_modes=[PrivilegeMode.U]),        steps=steps,    )`
**Sources:**[coretp/plans/svadu/__init__.py 1-15](https://github.com/tenstorrent/riscv-coretp/blob/e4fb9eea/coretp/plans/svadu/__init__.py#L1-L15)[coretp/plans/zicntr_zihpm_sscounterenw/zicntr_zihpm_sscounterenw_scenarios.py 30-52](https://github.com/tenstorrent/riscv-coretp/blob/e4fb9eea/coretp/plans/zicntr_zihpm_sscounterenw/zicntr_zihpm_sscounterenw_scenarios.py#L30-L52)

## Extension Coverage Organization

Test plans are organized by functional domain and RISC-V extension. The registry in [coretp/plans/__init__.py 9-18](https://github.com/tenstorrent/riscv-coretp/blob/e4fb9eea/coretp/plans/__init__.py#L9-L18) imports all extension modules:

`from .paging import page_table_walksfrom .sstc import sstc_scenariosfrom .svadu import svadu_scenariosfrom .zicond import zicond_scenariosfrom .zkt import zkt_scenariosfrom .zimop_zcmop import zimop_zcmop_scenariosfrom .zifencei import zifencei_scenariosfrom .zicbom_zicboz_zicbop import zicbom_zicboz_zicbop_scenariosfrom .zicntr_zihpm_sscounterenw import zicntr_zihpm_sscounterenw_scenariosfrom .sscofpmf import sscofpmf_scenarios`
Each extension module follows the same pattern: define a test plan decorator, create scenario functions, and register them through decoration. This modular organization allows independent development and testing of each extension's verification scenarios.

**Sources:**[coretp/plans/__init__.py 1-21](https://github.com/tenstorrent/riscv-coretp/blob/e4fb9eea/coretp/plans/__init__.py#L1-L21)

## Immutability and Caching

The architecture enforces immutability at key points to ensure consistency:

*   **TestPlan objects**: Built once and cached in `_TestPlanInfo._built_plan` at [coretp/plans/test_plan_registry.py 63](https://github.com/tenstorrent/riscv-coretp/blob/e4fb9eea/coretp/plans/test_plan_registry.py#L63-L63)
*   **Scenario registration**: Blocked after first plan retrieval at [coretp/plans/test_plan_registry.py 67-69](https://github.com/tenstorrent/riscv-coretp/blob/e4fb9eea/coretp/plans/test_plan_registry.py#L67-L69)
*   **TestEnvCfg**: Frozen dataclass at [coretp/env/cfg.py 11](https://github.com/tenstorrent/riscv-coretp/blob/e4fb9eea/coretp/env/cfg.py#L11-L11)

This ensures the same `TestPlan` object is returned for repeated `get_plan()` calls, making plans safe as dictionary keys and preventing accidental modification of test definitions after registration.

**Sources:**[coretp/plans/test_plan_registry.py 44-80](https://github.com/tenstorrent/riscv-coretp/blob/e4fb9eea/coretp/plans/test_plan_registry.py#L44-L80)[coretp/env/cfg.py 11-39](https://github.com/tenstorrent/riscv-coretp/blob/e4fb9eea/coretp/env/cfg.py#L11-L39)

Dismiss
Refresh this wiki

Enter email to refresh
---
project: riescue
github: https://github.com/tenstorrent/riescue
deepwiki: https://deepwiki.com/tenstorrent/riescue/7-advanced-topics
---

# Advanced Topics

Relevant source files
*   [README.md](https://github.com/tenstorrent/riescue/blob/07cefde3/README.md?plain=1)
*   [infra/container/singularity-id](https://github.com/tenstorrent/riescue/blob/07cefde3/infra/container/singularity-id)
*   [riescue/compliance/tp.py](https://github.com/tenstorrent/riescue/blob/07cefde3/riescue/compliance/tp.py)
*   [riescue/dtest_framework/parser.py](https://github.com/tenstorrent/riescue/blob/07cefde3/riescue/dtest_framework/parser.py)
*   [riescue/riescued.py](https://github.com/tenstorrent/riescue/blob/07cefde3/riescue/riescued.py)
*   [tests/cli_tests/riescued/hypervisor_test.py](https://github.com/tenstorrent/riescue/blob/07cefde3/tests/cli_tests/riescued/hypervisor_test.py)

This page covers in-depth technical implementation details for advanced users and contributors who need to understand the internal architecture and mechanisms of RiESCUE. Topics include test generation internals, configuration file formats, and toolchain abstraction patterns.

For basic usage of RiescueD directives, see [Test Directives and Syntax](https://deepwiki.com/tenstorrent/riescue/2.1.2-test-directives-and-syntax). For information on external tool integration, see [Toolchain and External Dependencies](https://deepwiki.com/tenstorrent/riescue/4-toolchain-and-external-dependencies). For development workflow and code quality standards, see [Development Workflow](https://deepwiki.com/tenstorrent/riescue/5-development-workflow).

* * *

## Test Generation Internals

The test generation pipeline in RiescueD consists of three primary components that work together to transform assembly files with directives into executable tests: `Parser`, `Pool`, and `Generator`. Understanding their interactions is essential for extending RiESCUE or debugging test generation issues.

### Architecture Overview

**Sources:**[riescue/riescued.py 31-268](https://github.com/tenstorrent/riescue/blob/07cefde3/riescue/riescued.py#L31-L268)[riescue/dtest_framework/parser.py 20-699](https://github.com/tenstorrent/riescue/blob/07cefde3/riescue/dtest_framework/parser.py#L20-L699)

### Data Flow Through Components

The following diagram illustrates how data flows from input files through parsing, configuration, and generation stages:

**Sources:**[riescue/riescued.py 46-267](https://github.com/tenstorrent/riescue/blob/07cefde3/riescue/riescued.py#L46-L267)

* * *

## Parser Implementation

The `Parser` class in `riescue/dtest_framework/parser.py` is responsible for extracting RiESCUE-specific directives from assembly files and converting them into structured Python dataclasses.

### Directive Parsing Patterns

The Parser uses regular expressions to match specific directive patterns:

| Directive | Pattern | Parsed Into |
| --- | --- | --- |
| `;#random_data(...)` | `r"^;#(random_data)\((.+)\)"` | `ParsedRandomData` |
| `;#random_addr(...)` | `r"^;#(random_addr)\((.+)\)"` | `ParsedRandomAddress` |
| `;#page_mapping(...)` | `r"^;#(page_mapping)\((.+)\)"` | `ParsedPageMapping` |
| `;#page_map(...)` | `r"^;#(page_map)\((.+)\)"` | `ParsedPageMap` |
| `;#test.<header>` | `r";#test.(\w*)(.*)"` | `ParsedTestHeader` |
| `;#discrete_test(...)` | `r"^;#(discrete_test)\((.+)\)"` | Stored in Pool |
| `;#reserve_memory(...)` | `r"^;#(reserve_memory)\((.+)\)"` | `ParsedReserveMemory` |
| `;#csr_rw(...)` | `r"^;#csr_rw\((?P<csr_name>\w*),\s*(?P<read_write_set_clear>\w*),\s*(?P<direct_rw>\w*)\)"` | `ParsedCsrAccess` |
| `;#read_leaf_pte(...)` | `r"^;#read_leaf_pte\((?P<lin_name>\w+),\s*(?P<paging_mode>\w+)\)"` | `ParsedLeafPte` |

**Sources:**[riescue/dtest_framework/parser.py 52-693](https://github.com/tenstorrent/riescue/blob/07cefde3/riescue/dtest_framework/parser.py#L52-L693)

### Parser to Pool Integration

**Sources:**[riescue/dtest_framework/parser.py 116-693](https://github.com/tenstorrent/riescue/blob/07cefde3/riescue/dtest_framework/parser.py#L116-L693)

### Key Dataclass Structures

The parsed directives are stored in dataclasses that preserve all directive parameters:

**Sources:**[riescue/dtest_framework/parser.py 701-845](https://github.com/tenstorrent/riescue/blob/07cefde3/riescue/dtest_framework/parser.py#L701-L845)

### Parser Execution Flow

When `Parser.parse()` is called, it:

1.   Reads the entire `.rasm` file line-by-line ([parser.py 52-55](https://github.com/tenstorrent/riescue/blob/07cefde3/parser.py#L52-L55))
2.   For each line, checks if it matches any directive pattern ([parser.py 56-97](https://github.com/tenstorrent/riescue/blob/07cefde3/parser.py#L56-L97))
3.   Extracts parameters using regex groups ([parser.py 100-114](https://github.com/tenstorrent/riescue/blob/07cefde3/parser.py#L100-L114))
4.   Creates a dataclass instance with extracted values ([parser.py 116-131](https://github.com/tenstorrent/riescue/blob/07cefde3/parser.py#L116-L131))
5.   Adds the instance to the `Pool` ([parser.py 130](https://github.com/tenstorrent/riescue/blob/07cefde3/parser.py#L130-L130))

The `Pool` object serves as a central repository that other components can query during generation.

**Sources:**[riescue/dtest_framework/parser.py 52-699](https://github.com/tenstorrent/riescue/blob/07cefde3/riescue/dtest_framework/parser.py#L52-L699)

* * *

## Feature Manager (FeatMgr)

The `FeatMgr` class consolidates test configuration from multiple sources with a defined precedence hierarchy. It is constructed using the Builder pattern via `FeatMgrBuilder`.

### Configuration Priority

Configuration sources are applied in order of increasing priority:

**Sources:**[riescue/riescued.py 222-237](https://github.com/tenstorrent/riescue/blob/07cefde3/riescue/riescued.py#L222-L237)

### FeatMgr Construction Pattern

The Builder pattern allows incremental configuration:

This pattern allows:

*   Partial configuration (e.g., test header only)
*   Overriding specific values (e.g., CLI args override test header)
*   Validation at build time
*   Reusable builder instances

**Sources:**[riescue/riescued.py 222-237](https://github.com/tenstorrent/riescue/blob/07cefde3/riescue/riescued.py#L222-L237)

### FeatMgr Key Attributes

The `FeatMgr` object contains consolidated configuration used throughout generation:

| Attribute | Type | Description | Example Values |
| --- | --- | --- | --- |
| `priv_mode` | `RiscvPrivileges` | Privilege level | `MACHINE`, `SUPER`, `USER` |
| `env` | `RiscvTestEnv` | Test environment | `NORMAL`, `VIRTUALIZED` |
| `paging_mode` | `RiscvPagingModes` | Virtual memory mode | `SV39`, `SV48`, `SV57`, `DISABLE` |
| `paging_g_mode` | `RiscvPagingModes` | Guest paging mode (hypervisor) | Same as `paging_mode` |
| `secure_mode` | `bool` | Secure/non-secure world | `True`, `False` |
| `reset_pc` | `int` | Initial program counter | `0x80000000` |
| `big_endian` | `bool` | Endianness | `True`, `False` |
| `num_cpus` | `int` | Number of harts | `1`, `2`, `4` |
| `wysiwyg` | `bool` | Pass/fail via x31 register | `True`, `False` |
| `cfiles` | `list[str]` | Additional C files to compile | `["helper.c"]` |

**Sources:**[riescue/riescued.py 222-267](https://github.com/tenstorrent/riescue/blob/07cefde3/riescue/riescued.py#L222-L267)

### Configuration Usage in Generation

The `FeatMgr` instance is passed to:

1.   **Generator** - Controls page table generation, memory layout, and OS code inclusion ([riescued.py 265](https://github.com/tenstorrent/riescue/blob/07cefde3/riescued.py#L265-L265))
2.   **Compiler** - Determines endianness flags ([riescued.py 291-292](https://github.com/tenstorrent/riescue/blob/07cefde3/riescued.py#L291-L292))
3.   **Simulator** - Configures privilege modes, multicore settings, and termination conditions ([riescued.py 311-396](https://github.com/tenstorrent/riescue/blob/07cefde3/riescued.py#L311-L396))

**Sources:**[riescue/riescued.py 239-396](https://github.com/tenstorrent/riescue/blob/07cefde3/riescue/riescued.py#L239-L396)

* * *

## Memory Management

Memory management in RiESCUE involves random address generation, page table construction, and memory region allocation. This is handled primarily by the `Generator` class working with data from `Pool`.

### Random Address Generation

The `ParsedRandomAddress` dataclass supports multiple address generation modes:

**Sources:**[riescue/dtest_framework/parser.py 719-734](https://github.com/tenstorrent/riescue/blob/07cefde3/riescue/dtest_framework/parser.py#L719-L734)

### Address Resolution Process

When the Generator encounters a `random_addr` directive:

1.   **Parse Phase**: Parser creates `ParsedRandomAddress` with constraints ([parser.py 132-166](https://github.com/tenstorrent/riescue/blob/07cefde3/parser.py#L132-L166))
2.   **Pool Storage**: Address stored in `pool.parsed_addrs` dictionary ([parser.py 166](https://github.com/tenstorrent/riescue/blob/07cefde3/parser.py#L166-L166))
3.   **Generation Phase**: Generator retrieves address and generates random value satisfying constraints
4.   **Validation**: Checks alignment, PMA requirements, and conflicts with other addresses
5.   **Substitution**: Assembly file references are replaced with actual address values

### Page Table Generation

For tests with `paging_mode != DISABLE`, the Generator creates page tables:

**Sources:**[riescue/dtest_framework/parser.py 168-572](https://github.com/tenstorrent/riescue/blob/07cefde3/riescue/dtest_framework/parser.py#L168-L572)[riescue/dtest_framework/parser.py 737-845](https://github.com/tenstorrent/riescue/blob/07cefde3/riescue/dtest_framework/parser.py#L737-L845)

### Memory Region Types

The Parser and Generator support several memory region types:

| Region Type | Directive | Purpose |
| --- | --- | --- |
| Random Data | `;#random_data(name=...)` | Generate random immediate values |
| Random Address | `;#random_addr(name=...)` | Generate random virtual/physical addresses |
| Reserve Memory | `;#reserve_memory(addr=...)` | Reserve memory region (no randomization) |
| Page Mapping | `;#page_mapping(lin_name=..., phys_name=...)` | Create virtual-to-physical mapping |
| Init Memory | `;#init_memory(...)` | Initialize memory with specific values |

**Sources:**[riescue/dtest_framework/parser.py 99-693](https://github.com/tenstorrent/riescue/blob/07cefde3/riescue/dtest_framework/parser.py#L99-L693)

### Hypervisor Memory Management

For virtualized environments (`test_env=virtualized`), the page mapping system supports two-stage address translation:

*   **VS-stage** (Virtual Supervisor): Guest virtual → Guest physical
*   **G-stage** (Guest): Guest physical → Host physical

The `ParsedPageMapping` dataclass includes fields for both stages:

*   `v_leaf_gleaf`, `v_leaf_gnonleaf`, `v_nonleaf_gleaf`, `v_nonleaf_gnonleaf`
*   Similar patterns for `a`, `d`, `r`, `w`, `x`, `u`, `g` bits
*   `gstage_vs_leaf_pagesizes` and `gstage_vs_nonleaf_pagesizes` arrays

**Sources:**[riescue/dtest_framework/parser.py 737-845](https://github.com/tenstorrent/riescue/blob/07cefde3/riescue/dtest_framework/parser.py#L737-L845)[tests/cli_tests/riescued/hypervisor_test.py 11-24](https://github.com/tenstorrent/riescue/blob/07cefde3/tests/cli_tests/riescued/hypervisor_test.py#L11-L24)

* * *

## Configuration Files

RiESCUE uses several JSON configuration files to specify system characteristics, simulator settings, and container configurations.

### CPU Configuration Format

The CPU configuration file (typically `dtest_framework/lib/config.json`) defines the memory map and system features:

**Key Sections:**

| Section | Purpose | Key Fields |
| --- | --- | --- |
| `memory_map` | Defines memory regions | `name`, `base_addr`, `size`, `permissions` |
| `features` | System capabilities | `reset_pc`, `privilege_modes`, `paging_modes` |
| `pma` | Physical Memory Attributes | `memory_type`, `amo_type`, `cacheability` |

**Sources:**[riescue/riescued.py 50-60](https://github.com/tenstorrent/riescue/blob/07cefde3/riescue/riescued.py#L50-L60)

### Whisper Configuration Files

Whisper ISS uses JSON configuration files to specify processor features. RiESCUE includes several pre-configured files:

#### whisper_config.json (Standard Configuration)

Used for non-secure mode tests. Contains ISA extensions, memory regions, and CSR configurations.

**Sources:**[riescue/riescued.py 372](https://github.com/tenstorrent/riescue/blob/07cefde3/riescue/riescued.py#L372-L372)

#### whisper_secure_config.json (Secure Mode)

Used when `secure_mode=True` in test header or CLI. Includes secure world configurations.

**Sources:**[riescue/riescued.py 370](https://github.com/tenstorrent/riescue/blob/07cefde3/riescue/riescued.py#L370-L370)

#### whisper_basic_config.json (Minimal Configuration)

Minimal configuration for basic tests without advanced features.

### Whisper Configuration in Simulation

**Sources:**[riescue/riescued.py 366-373](https://github.com/tenstorrent/riescue/blob/07cefde3/riescue/riescued.py#L366-L373)

### Container Configuration

The `.container_config` file specifies Singularity container registry and bind mount settings:

**Key Fields:**

| Field | Purpose | Example |
| --- | --- | --- |
| `registry` | Container image registry URL | `docker://ghcr.io/tenstorrent/riescue` |
| `bind_mounts` | Host paths to mount in container | `["/workspace:/workspace"]` |
| `environment` | Environment variables | `{"RV_TOOLCHAIN": "/opt/riscv"}` |

The container system uses this configuration in `container.py` for building, running, and managing containers.

**Sources:** Not directly visible in provided files, but referenced in architecture diagrams

* * *

## Toolchain Abstraction Layer

The `Toolchain` class and its component `Tool` subclasses provide a unified interface to external executables (compilers, simulators, disassemblers).

### Tool Base Class Architecture

**Sources:** Architecture based on typical Tool class pattern in riescue.lib.toolchain

### Toolchain Class Composition

The `Toolchain` class aggregates multiple `Tool` instances:

**Sources:**[riescue/riescued.py 21](https://github.com/tenstorrent/riescue/blob/07cefde3/riescue/riescued.py#L21-L21)[riescue/riescued.py 67-70](https://github.com/tenstorrent/riescue/blob/07cefde3/riescue/riescued.py#L67-L70)[riescue/riescued.py 179](https://github.com/tenstorrent/riescue/blob/07cefde3/riescue/riescued.py#L179-L179)

### Compiler Tool Usage

The `Compiler` class handles RISC-V cross-compilation:

**RiescueD Invocation:**

**Sources:**[riescue/riescued.py 281-298](https://github.com/tenstorrent/riescue/blob/07cefde3/riescue/riescued.py#L281-L298)

### Disassembler Tool Usage

The `Disassembler` class generates human-readable disassembly:

The disassembler:

*   Uses `riscv64-unknown-elf-objdump`
*   Disassembles all sections with `-D`
*   Uses numeric register names with `-M numeric`
*   Outputs to `.dis` file for debugging

**Sources:**[riescue/riescued.py 300-307](https://github.com/tenstorrent/riescue/blob/07cefde3/riescue/riescued.py#L300-L307)

### Simulator Tool Classes

#### Whisper Simulator

**Whisper-Specific Arguments:**

*   `--configfile`: JSON configuration file
*   `--logfile`: Simulation log output
*   `--quitany`: Exit when any hart finishes (multicore)
*   `--harts N`: Number of harts
*   `--deterministic N`: Deterministic scheduling
*   `--seed N`: Random seed for scheduling
*   `--endpc 0xABCD`: End simulation at specific PC

**Sources:**[riescue/riescued.py 365-386](https://github.com/tenstorrent/riescue/blob/07cefde3/riescue/riescued.py#L365-L386)

#### Spike Simulator

**Spike-Specific Arguments:**

*   `--isa=<string>`: ISA specification
*   `--priv=msu`: Privilege modes
*   `--pc=0x80000000`: Initial program counter
*   `-p<N>`: Number of cores
*   `--misaligned`: Allow misaligned access
*   `--big-endian`: Big-endian mode
*   `--tt-tohost-nonzero-terminate`: TT-specific termination
*   `--end-pc 0xABCD`: End simulation at PC

**Sources:**[riescue/riescued.py 346-364](https://github.com/tenstorrent/riescue/blob/07cefde3/riescue/riescued.py#L346-L364)

### Tool Executable Discovery

The Tool base class searches for executables in this order:

1.   **Explicit Path**: If full path provided
2.   **Environment Variable**: `$RV_TOOLCHAIN/bin/<executable>`
3.   **System PATH**: Standard PATH search
4.   **Error**: Raises exception if not found

This allows flexibility for:

*   CI environments with specific toolchain paths
*   Developer machines with custom installations
*   Containerized builds with mounted toolchains

**Sources:** Inferred from typical Tool implementation patterns

### Error Handling in Tool Execution

Tools handle subprocess errors consistently:

1.   **Command Construction**: Build argument list
2.   **Subprocess Launch**: Use `subprocess.Popen()` with captured stdout/stderr
3.   **Output Logging**: Write output to log file if specified
4.   **Return Code Check**: Raise exception on non-zero exit
5.   **Error Context**: Include command, working directory, and output in exception

This provides detailed debugging information when external tools fail.

**Sources:**[riescue/riescued.py 281-396](https://github.com/tenstorrent/riescue/blob/07cefde3/riescue/riescued.py#L281-L396)

* * *

## Test Plan Mode Internals

The Test Plan (TP) mode in RiescueC (`riescue/compliance/tp.py`) generates tests from structured test plans in the `riscv-coretp` library.

### TP Mode Architecture

**Sources:**[riescue/compliance/tp.py 29-114](https://github.com/tenstorrent/riescue/blob/07cefde3/riescue/compliance/tp.py#L29-L114)

### Predicate-Based Environment Solving

The TP mode uses predicates to filter valid test environments:

**Predicates filter environments to match:**

*   Privilege mode constraints
*   Paging mode requirements
*   Virtualization settings
*   Exception delegation configuration

**Sources:**[riescue/compliance/tp.py 139-182](https://github.com/tenstorrent/riescue/blob/07cefde3/riescue/compliance/tp.py#L139-L182)

### TestPlanGenerator Workflow

**Sources:**[riescue/compliance/tp.py 72-114](https://github.com/tenstorrent/riescue/blob/07cefde3/riescue/compliance/tp.py#L72-L114)

### TP Mode vs Bringup Mode

| Aspect | TP Mode | Bringup Mode |
| --- | --- | --- |
| **Input** | Test plan name from coretp | ISA string |
| **Test Structure** | Structured scenarios with requirements | Random instruction sequences |
| **Environment** | Solved from scenario constraints | Specified by user or default |
| **Use Case** | Architecture compliance validation | Datapath testing, bringup |
| **Complexity** | Higher (environment solving) | Lower (direct generation) |

**Sources:**[riescue/compliance/tp.py 29-114](https://github.com/tenstorrent/riescue/blob/07cefde3/riescue/compliance/tp.py#L29-L114)[README.md 38-65](https://github.com/tenstorrent/riescue/blob/07cefde3/README.md?plain=1#L38-L65)

* * *

This completes the Advanced Topics documentation covering test generation internals, configuration systems, and toolchain abstraction patterns in RiESCUE.

Dismiss
Refresh this wiki

Enter email to refresh
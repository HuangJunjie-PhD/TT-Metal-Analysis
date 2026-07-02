---
project: riescue
github: https://github.com/tenstorrent/riescue
deepwiki: https://deepwiki.com/tenstorrent/riescue/4-toolchain-and-external-dependencies
---

# Toolchain and External Dependencies

 Relevant source files

* [README.md](https://github.com/tenstorrent/riescue/blob/07cefde3/README.md?plain=1)
* [infra/build\_spike.sh](https://github.com/tenstorrent/riescue/blob/07cefde3/infra/build_spike.sh)
* [infra/build\_whisper.sh](https://github.com/tenstorrent/riescue/blob/07cefde3/infra/build_whisper.sh)
* [infra/container/singularity-id](https://github.com/tenstorrent/riescue/blob/07cefde3/infra/container/singularity-id)
* [riescue/lib/toolchain/tool.py](https://github.com/tenstorrent/riescue/blob/07cefde3/riescue/lib/toolchain/tool.py)
* [riescue/riescued.py](https://github.com/tenstorrent/riescue/blob/07cefde3/riescue/riescued.py)
* [tests/cli\_tests/riescued/hypervisor\_test.py](https://github.com/tenstorrent/riescue/blob/07cefde3/tests/cli_tests/riescued/hypervisor_test.py)

This page documents RiESCUE's integration with external RISC-V toolchain components and dependencies. It covers the mechanism for discovering and executing external tools, the specific versions and configurations used, and how these dependencies are built and managed.

For specific usage of individual tools in the testing workflow, see [RiescueD: Directed Test Framework](/tenstorrent/riescue/2.1-riescued:-directed-test-framework). For container-based dependency management, see [Container System](/tenstorrent/riescue/3.1-container-system). For build script details, see [Build Scripts and Dependencies](/tenstorrent/riescue/3.2-build-scripts-and-dependencies).

## Overview

RiESCUE depends on three categories of external tools:

| Category | Tools | Purpose |
| --- | --- | --- |
| **RISC-V GNU Toolchain** | `riscv64-unknown-elf-gcc`, `riscv64-unknown-elf-objdump`, `riscv64-unknown-elf-objcopy` | Compiling assembly tests to ELF, disassembling executables, binary manipulation |
| **Instruction Set Simulators** | `whisper`, `spike`, `tt_spike` | Simulating RISC-V test execution |
| **Python Libraries** | `coretp` | Test plan definitions for compliance testing |

All external tools are accessed through abstraction classes that handle discovery, configuration, and execution. This abstraction layer isolates RiESCUE's core logic from the specifics of tool installation and invocation.

Sources: [README.md88-92](https://github.com/tenstorrent/riescue/blob/07cefde3/README.md?plain=1#L88-L92) [riescue/riescued.py21](https://github.com/tenstorrent/riescue/blob/07cefde3/riescue/riescued.py#L21-L21)

## Tool Discovery and Abstraction Layer

### Tool Base Class Architecture

The `Tool` base class at [riescue/lib/toolchain/tool.py18-126](https://github.com/tenstorrent/riescue/blob/07cefde3/riescue/lib/toolchain/tool.py#L18-L126) implements a three-tier discovery mechanism:

1. **Explicit path**: If provided via constructor or command-line argument (e.g., `--compiler_path`)
2. **Environment variable**: Tool-specific environment variable (e.g., `RV_GCC`, `SPIKE_PATH`)
3. **System PATH**: Uses `shutil.which()` to search system PATH

This approach allows flexibility for different installation scenarios while maintaining a consistent interface.

Sources: [riescue/lib/toolchain/tool.py18-56](https://github.com/tenstorrent/riescue/blob/07cefde3/riescue/lib/toolchain/tool.py#L18-L56)

### Discovery Flow

Sources: [riescue/lib/toolchain/tool.py36-55](https://github.com/tenstorrent/riescue/blob/07cefde3/riescue/lib/toolchain/tool.py#L36-L55)

## RISC-V GNU Toolchain Components

### Compiler

The `Compiler` class wraps `riscv64-unknown-elf-gcc` with RiESCUE-specific configuration:

**Default Configuration** [riescue/lib/toolchain/tool.py128-166](https://github.com/tenstorrent/riescue/blob/07cefde3/riescue/lib/toolchain/tool.py#L128-L166):

* **March**: `rv64imafdcvh_svinval_zfh_zba_zbb_zbc_zbs_zifencei_zicsr_zvkned_zicbom_zicbop_zicboz_zawrs_zihintpause_zvbb1_zicond_zvkg_zvkn_zvbc_zfa_zk`
* **Standard flags**: `-static -mcmodel=medany -fvisibility=hidden -nostdlib -nostartfiles`
* **Optional**: `-mabi` for ABI specification, `-D` for test equates

**Environment Variables**:

* `RV_GCC`: Path to GCC executable

**Command-line Arguments**:

* `--compiler_path`: Override executable path
* `--compiler_opts`: Additional compiler options
* `--compiler_march`: Override march string
* `--compiler_mabi`: Override ABI
* `--test_equates`: Define preprocessor symbols

Sources: [riescue/lib/toolchain/tool.py128-203](https://github.com/tenstorrent/riescue/blob/07cefde3/riescue/lib/toolchain/tool.py#L128-L203)

### Disassembler

The `Disassembler` class wraps `riscv64-unknown-elf-objdump` for generating human-readable disassembly from ELF files.

**Environment Variables**:

* `RV_OBJDUMP`: Path to objdump executable

**Command-line Arguments**:

* `--disassembler_path`: Override executable path
* `--disassembler_opts`: Additional disassembler options (currently ignored due to legacy compatibility)

**Usage in RiescueD** [riescue/riescued.py300-308](https://github.com/tenstorrent/riescue/blob/07cefde3/riescue/riescued.py#L300-L308):

```
disassembler.run( output_file=self.generated_files.dis, cwd=self.run_dir, args=["-D", str(self.generated_files.elf), "-M", "numeric"] ) 
```

Sources: [riescue/lib/toolchain/tool.py206-242](https://github.com/tenstorrent/riescue/blob/07cefde3/riescue/lib/toolchain/tool.py#L206-L242) [riescue/riescued.py300-308](https://github.com/tenstorrent/riescue/blob/07cefde3/riescue/riescued.py#L300-L308)

### Objcopy

The `Objcopy` class wraps `riscv64-unknown-elf-objcopy` for binary manipulation tasks.

**Environment Variables**:

* `RV_OBJCOPY`: Path to objcopy executable

**Command-line Arguments**:

* `--objcopy_path`: Override executable path
* `--objcopy_opts`: Additional objcopy options

Sources: [riescue/lib/toolchain/tool.py245-270](https://github.com/tenstorrent/riescue/blob/07cefde3/riescue/lib/toolchain/tool.py#L245-L270)

## Instruction Set Simulators

### Whisper

Whisper is Tenstorrent's RISC-V instruction set simulator, used as the default ISS for RiESCUE.

**Version and Source**:

* Repository: `https://github.com/tenstorrent/whisper.git`
* Pinned SHA: `b24e30f238d462d3930744cb084d74129d0873a8`

**Build Configuration** [infra/build\_whisper.sh10](https://github.com/tenstorrent/riescue/blob/07cefde3/infra/build_whisper.sh#L10-L10):

**Installation**:

* Development mode: Built in `whisper/` submodule
* Container/public mode: Cloned to `/usr/local/whisper`, installed to `/usr/bin/whisper`

**Configuration Files**:

* Default: `dtest_framework/lib/whisper_config.json`
* Secure mode: `dtest_framework/lib/whisper_secure_config.json`
* Override: `--whisper_config_json` command-line argument

**Usage in RiescueD** [riescue/riescued.py365-396](https://github.com/tenstorrent/riescue/blob/07cefde3/riescue/riescued.py#L365-L396):

* Supports multi-hart simulation (`--harts`, `--quitany`, `--deterministic`)
* WYSIWYG mode support with `--endpc`
* Configurable via JSON configuration files

Sources: [infra/build\_whisper.sh1-73](https://github.com/tenstorrent/riescue/blob/07cefde3/infra/build_whisper.sh#L1-L73) [riescue/riescued.py365-396](https://github.com/tenstorrent/riescue/blob/07cefde3/riescue/riescued.py#L365-L396)

### Spike and tt\_spike

Spike is the reference RISC-V ISS. RiESCUE supports both the standard version and Tenstorrent's fork (`tt_spike`) with custom features.

**Versions and Sources** [infra/build\_spike.sh9-14](https://github.com/tenstorrent/riescue/blob/07cefde3/infra/build_spike.sh#L9-L14):

| Variant | Repository | SHA | Features |
| --- | --- | --- | --- |
| **spike** | `https://github.com/riscv-software-src/riscv-isa-sim.git` | `4703ad98bf4c247a0841a6d7254357b14a97ff29` | Standard reference implementation |
| **tt\_spike** | `https://github.com/tenstorrent/spike.git` | `344b9ef3951fc9318caae4ce41da8ede5d6085fc` | Custom tohost handling, table walk debug, expanded DRAM range |

**Build Configuration** [infra/build\_spike.sh43-49](https://github.com/tenstorrent/riescue/blob/07cefde3/infra/build_spike.sh#L43-L49):

Standard spike:

tt\_spike:

**Installation**:

* `spike`: Installed to system PATH via `make install`
* `tt_spike`: Installed to `/usr/local/bin/tt_spike`

**Default ISA Strings** [riescue/lib/toolchain/tool.py288-294](https://github.com/tenstorrent/riescue/blob/07cefde3/riescue/lib/toolchain/tool.py#L288-L294):

* `spike`: `RV64IMAFDCVH_zba_zbb_zbc_zfh_zbs_zfbfmin_zvfh_zvbb_zvbc_zvfbfmin_zvfbfwma_zvkg_zvkned_zvl256b_zve64d_svpbmt`
* `tt_spike`: `RV64IMAFDCVH_zba_zbb_zbc_zfh_zbs_zfbfmin_zvfh_zvbb_zvbc_zvfbfmin_zvfbfwma_zvkg_zvkned_zvknhb_svpbmt_sstc_zicntr`

**Command-line Arguments**:

* `--spike_path`: Override executable path
* `--spike_args`: Additional spike arguments
* `--spike_isa`: Override ISA string
* `--third_party_spike`: Use standard spike instead of tt\_spike
* `--spike_max_instr`: Maximum instruction count (default: 2,000,000)

**Usage in RiescueD** [riescue/riescued.py346-364](https://github.com/tenstorrent/riescue/blob/07cefde3/riescue/riescued.py#L346-L364):

```
iss_args = ["--priv=msu", f"--pc=0x{featmgr.reset_pc:x}"] if not featmgr.force_alignment: iss_args.append("--misaligned") if featmgr.mp_mode_on(): iss_args.append(f"-p{featmgr.num_cpus}") 
```

Sources: [infra/build\_spike.sh1-69](https://github.com/tenstorrent/riescue/blob/07cefde3/infra/build_spike.sh#L1-L69) [riescue/lib/toolchain/tool.py273-334](https://github.com/tenstorrent/riescue/blob/07cefde3/riescue/lib/toolchain/tool.py#L273-L334) [riescue/riescued.py346-364](https://github.com/tenstorrent/riescue/blob/07cefde3/riescue/riescued.py#L346-L364)

### Spike Class Implementation

Sources: [riescue/lib/toolchain/tool.py273-334](https://github.com/tenstorrent/riescue/blob/07cefde3/riescue/lib/toolchain/tool.py#L273-L334)

## External Python Dependencies

### riscv-coretp

The `coretp` package provides test plan definitions consumed by RiescueC's TP mode.

**Repository**: `https://github.com/tenstorrent/riscv-coretp`

**Version Pinning**: Specified in `pyproject.toml` as a git dependency with pinned commit:

**Usage**: RiescueC imports test scenarios from coretp:

The test plan structure defines instruction sequences, environmental constraints, and predicates that RiescueC uses to generate directed tests.

Sources: [README.md21](https://github.com/tenstorrent/riescue/blob/07cefde3/README.md?plain=1#L21-L21)

## Dependency Version Management

### Version Pinning Strategy

All external dependencies use explicit version pinning for reproducibility:

| Dependency | Pinning Method | Version/SHA |
| --- | --- | --- |
| **Whisper** | Git SHA in build script | `b24e30f238d462d3930744cb084d74129d0873a8` |
| **Spike (public)** | Git SHA in build script | `4703ad98bf4c247a0841a6d7254357b14a97ff29` |
| **tt\_spike** | Git SHA in build script | `344b9ef3951fc9318caae4ce41da8ede5d6085fc` |
| **coretp** | Git SHA in pyproject.toml | `5da80d20` |
| **RISC-V Toolchain** | CI downloads prebuilt archive | Version varies by CI environment |

This pinning ensures that tests produce consistent results across different environments and time periods.

Sources: [infra/build\_whisper.sh12-13](https://github.com/tenstorrent/riescue/blob/07cefde3/infra/build_whisper.sh#L12-L13) [infra/build\_spike.sh9-14](https://github.com/tenstorrent/riescue/blob/07cefde3/infra/build_spike.sh#L9-L14)

### Container Version Tracking

The container image version is tracked via a hash-based identifier stored in [infra/container/singularity-id1](https://github.com/tenstorrent/riescue/blob/07cefde3/infra/container/singularity-id#L1-L1):

```
30-2d59cd239c2bfcc64dec4b00ba85f7ed2cb65ae6f1ee99158c76a09f07aac629 
```

This identifier combines:

* Build iteration number (30)
* SHA256 hash of container definition and build scripts

Sources: [infra/container/singularity-id1](https://github.com/tenstorrent/riescue/blob/07cefde3/infra/container/singularity-id#L1-L1)

## Build Process Architecture

### Build Script Features

**Whisper Build** [infra/build\_whisper.sh1-73](https://github.com/tenstorrent/riescue/blob/07cefde3/infra/build_whisper.sh#L1-L73):

* Detects development environment (submodule) vs. public environment (clone)
* Supports container-less builds via `NO_SINGULARITY` environment variable
* Shallow clone with specific SHA for minimal download
* Installs to `/usr/bin/whisper` for global access

**Spike Build** [infra/build\_spike.sh1-69](https://github.com/tenstorrent/riescue/blob/07cefde3/infra/build_spike.sh#L1-L69):

* Builds two variants: standard `spike` and `tt_spike`
* Uses reusable `build_spike()` function for both variants
* Different configure options for each variant
* Cleans up build directories after installation

**Common Patterns**:

Sources: [infra/build\_whisper.sh1-73](https://github.com/tenstorrent/riescue/blob/07cefde3/infra/build_whisper.sh#L1-L73) [infra/build\_spike.sh1-69](https://github.com/tenstorrent/riescue/blob/07cefde3/infra/build_spike.sh#L1-L69)

## Integration with RiescueD

### Toolchain Class

The `Toolchain` class aggregates all tool instances and provides unified configuration:

**Initialization in RiescueD** [riescue/riescued.py67-70](https://github.com/tenstorrent/riescue/blob/07cefde3/riescue/riescued.py#L67-L70):

**Command-line Integration** [riescue/riescued.py136-138](https://github.com/tenstorrent/riescue/blob/07cefde3/riescue/riescued.py#L136-L138):

Sources: [riescue/riescued.py67-70](https://github.com/tenstorrent/riescue/blob/07cefde3/riescue/riescued.py#L67-L70) [riescue/riescued.py136-138](https://github.com/tenstorrent/riescue/blob/07cefde3/riescue/riescued.py#L136-L138)

### Tool Execution Flow

Sources: [riescue/riescued.py269-310](https://github.com/tenstorrent/riescue/blob/07cefde3/riescue/riescued.py#L269-L310) [riescue/riescued.py311-411](https://github.com/tenstorrent/riescue/blob/07cefde3/riescue/riescued.py#L311-L411)

## Error Handling and Diagnostics

### ToolchainError Classification

The `Tool` base class implements error classification via the `_classify()` abstract method, which subclasses override to provide tool-specific error handling:

**Error Types** (from `ToolFailureType` enum):

* `COMPILE_FAILURE`: Compilation or disassembly failed
* `SEGFAULT`: Tool segmentation fault (return code 139)
* `NONZERO_EXIT`: Unexpected non-zero exit code
* `TIMEOUT`: Process exceeded timeout limit

**Compiler Error Handling** [riescue/lib/toolchain/tool.py192-203](https://github.com/tenstorrent/riescue/blob/07cefde3/riescue/lib/toolchain/tool.py#L192-L203):

**ISS Error Handling** [riescue/lib/toolchain/tool.py317-319](https://github.com/tenstorrent/riescue/blob/07cefde3/riescue/lib/toolchain/tool.py#L317-L319):

Sources: [riescue/lib/toolchain/tool.py100-125](https://github.com/tenstorrent/riescue/blob/07cefde3/riescue/lib/toolchain/tool.py#L100-L125) [riescue/lib/toolchain/tool.py192-203](https://github.com/tenstorrent/riescue/blob/07cefde3/riescue/lib/toolchain/tool.py#L192-L203)

### WYSIWYG Mode Special Handling

In WYSIWYG (What You See Is What You Get) mode, tests use a non-standard termination mechanism that requires special ISS configuration:

**Problem**: Tests write `0xc001c0de` to `x31` to indicate success, but Whisper doesn't support this pattern natively.

**Solution** [riescue/riescued.py329-342](https://github.com/tenstorrent/riescue/blob/07cefde3/riescue/riescued.py#L329-L342):

1. Parse disassembly to find  label PC
2. Add 16 bytes (4 instructions) to reach end-of-test
3. Pass to Whisper/Spike as `--endpc`/`--end-pc`
4. Parse simulation log to verify `x31 == 0xc001c0de`

Sources: [riescue/riescued.py329-408](https://github.com/tenstorrent/riescue/blob/07cefde3/riescue/riescued.py#L329-L408)

## Environment Variable Reference

| Variable | Tool | Default Behavior | Example |
| --- | --- | --- | --- |
| `RV_GCC` | Compiler | Search PATH for `riscv64-unknown-elf-gcc` | `/opt/riscv/bin/riscv64-unknown-elf-gcc` |
| `RV_OBJDUMP` | Disassembler | Search PATH for `riscv64-unknown-elf-objdump` | `/opt/riscv/bin/riscv64-unknown-elf-objdump` |
| `RV_OBJCOPY` | Objcopy | Search PATH for `riscv64-unknown-elf-objcopy` | `/opt/riscv/bin/riscv64-unknown-elf-objcopy` |
| `SPIKE_PATH` | Spike | Search PATH for `spike` or `tt_spike` | `/usr/local/bin/tt_spike` |
| `RV_TOOLCHAIN` | All | Toolchain installation directory | `/opt/riscv` |
| `NO_SINGULARITY` | Build scripts | Disable container use in build scripts | `1` or `true` |

Sources: [riescue/lib/toolchain/tool.py36-55](https://github.com/tenstorrent/riescue/blob/07cefde3/riescue/lib/toolchain/tool.py#L36-L55) [infra/build\_whisper.sh26](https://github.com/tenstorrent/riescue/blob/07cefde3/infra/build_whisper.sh#L26-L26)

## Summary

RiESCUE's external dependency integration provides:

1. **Flexible discovery**: Three-tier search (explicit path → env var → system PATH)
2. **Version pinning**: All external tools pinned to specific SHAs for reproducibility
3. **Unified abstraction**: Common `Tool` base class for all external executables
4. **Error classification**: Tool-specific error handling with meaningful diagnostics
5. **Container integration**: Build scripts work both in containers and bare-metal
6. **Configuration management**: JSON configs for ISS, march strings for compiler

This architecture allows RiESCUE to work across diverse installation scenarios while maintaining consistent behavior and reproducible results.

Sources: [riescue/lib/toolchain/tool.py18-334](https://github.com/tenstorrent/riescue/blob/07cefde3/riescue/lib/toolchain/tool.py#L18-L334) [infra/build\_whisper.sh1-73](https://github.com/tenstorrent/riescue/blob/07cefde3/infra/build_whisper.sh#L1-L73) [infra/build\_spike.sh1-69](https://github.com/tenstorrent/riescue/blob/07cefde3/infra/build_spike.sh#L1-L69)

Refresh this wiki
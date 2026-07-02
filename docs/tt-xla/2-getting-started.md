---
project: tt-xla
github: https://github.com/tenstorrent/tt-xla
deepwiki: https://deepwiki.com/tenstorrent/tt-xla/2-getting-started
---

# Getting Started

Relevant source files
*   [.gitignore](https://github.com/tenstorrent/tt-xla/blob/c77995f6/.gitignore)
*   [README.md](https://github.com/tenstorrent/tt-xla/blob/c77995f6/README.md?plain=1)
*   [docs/src/getting_started.md](https://github.com/tenstorrent/tt-xla/blob/c77995f6/docs/src/getting_started.md?plain=1)
*   [docs/src/getting_started_build_from_source.md](https://github.com/tenstorrent/tt-xla/blob/c77995f6/docs/src/getting_started_build_from_source.md?plain=1)
*   [docs/src/getting_started_docker.md](https://github.com/tenstorrent/tt-xla/blob/c77995f6/docs/src/getting_started_docker.md?plain=1)
*   [docs/src/imgs/test_infra.png](https://github.com/tenstorrent/tt-xla/blob/c77995f6/docs/src/imgs/test_infra.png)
*   [docs/src/imgs/tt_smi.png](https://github.com/tenstorrent/tt-xla/blob/c77995f6/docs/src/imgs/tt_smi.png)
*   [docs/src/imgs/tt_xla_logo.png](https://github.com/tenstorrent/tt-xla/blob/c77995f6/docs/src/imgs/tt_xla_logo.png)
*   [docs/src/test_infra.md](https://github.com/tenstorrent/tt-xla/blob/c77995f6/docs/src/test_infra.md?plain=1)
*   [tests/filecheck/add.ttnn.mlir](https://github.com/tenstorrent/tt-xla/blob/c77995f6/tests/filecheck/add.ttnn.mlir)
*   [tests/filecheck/rms_norm.ttir.mlir](https://github.com/tenstorrent/tt-xla/blob/c77995f6/tests/filecheck/rms_norm.ttir.mlir)

This page introduces the setup options for tt-xla and provides the context needed to choose the right path. It does not cover the detailed steps for each installation method — those are in the sub-pages linked throughout. For deeper background on how the PJRT plugin works internally, see [Core Architecture](https://deepwiki.com/tenstorrent/tt-xla/4-system-architecture). For information on the build system, see [Build System](https://deepwiki.com/tenstorrent/tt-xla/3-build-system).

* * *

## What You Are Setting Up

tt-xla exposes Tenstorrent AI hardware to JAX, PyTorch/XLA, and vLLM via a [PJRT](https://github.com/tenstorrent/tt-xla/blob/c77995f6/PJRT) plugin. When installed, it provides:

*   `pjrt_plugin_tt.so` — the core PJRT plugin binary implementing the PJRT C API
*   `jax_plugin_tt` — Python package that registers the plugin with JAX
*   `torch_plugin_tt` — Python package that registers the plugin with PyTorch/XLA
*   `vllm_tt` — integration for vLLM-based LLM serving (optional)

Once the plugin is loaded, JAX sees Tenstorrent devices alongside CPU/GPU, and models compiled with `jax.jit` or `torch.compile(backend='tt')` are dispatched through the TT-MLIR compiler to Tenstorrent hardware. The plugin handles:

1.   Device enumeration and management via PJRT API
2.   Compilation requests to TT-MLIR (StableHLO → TTIR → TTNN → device code)
3.   Execution scheduling and memory management on TT devices

**Wheel package layout:**

```
pjrt_plugin_tt/
    __init__.py
    pjrt_plugin_tt.so          # PJRT plugin binary (C API implementation)
    tt-metal/                  # Runtime kernels and RISC-V toolchain
    lib/                       # Shared library dependencies (libTTMLIRCompiler.so, etc.)
jax_plugin_tt/
    __init__.py                # Imports pjrt_plugin_tt and calls xla_client.register_plugin
torch_plugin_tt/
    __init__.py                # Imports pjrt_plugin_tt and registers with torch_xla
```

Sources: [docs/src/getting_started_build_from_source.md 174-187](https://github.com/tenstorrent/tt-xla/blob/c77995f6/docs/src/getting_started_build_from_source.md?plain=1#L174-L187)[README.md 19](https://github.com/tenstorrent/tt-xla/blob/c77995f6/README.md?plain=1#L19-L19)

* * *

## How the Plugin Fits Into the Stack

```mermaid
graph TD
    USER["User Code<br/>(model.py)"]
    
    subgraph "Framework Layer"
        JAX["JAX<br/>jax.jit"]
        PT["PyTorch/XLA<br/>torch.compile(backend='tt')"]
        VLLM["vLLM<br/>LLM.generate()"]
    end
    
    subgraph "Plugin Registration"
        JAXPKG["jax_plugin_tt/__init__.py<br/>xla_client.register_plugin()"]
        PTPKG["torch_plugin_tt/__init__.py<br/>torch_xla plugin registration"]
        VLLMPKG["vllm_tt.TTWorker"]
    end
    
    subgraph "Core PJRT Plugin"
        PJRTSO["pjrt_plugin_tt.so<br/>PJRT_Plugin_Initialize<br/>PJRT_Client_Create"]
    end
    
    subgraph "Compilation"
        TTMLIR["TT-MLIR Compiler<br/>libTTMLIRCompiler.so<br/>StableHLO → TTIR → TTNN"]
    end
    
    subgraph "Runtime"
        TTMETAL["tt-metal libraries<br/>Device kernels<br/>Memory management"]
    end
    
    HW["Tenstorrent Hardware<br/>n150 / p150 / n300 / llmbox"]

    USER --> JAX --> JAXPKG --> PJRTSO
    USER --> PT --> PTPKG --> PJRTSO
    USER --> VLLM --> VLLMPKG --> PJRTSO
    PJRTSO --> TTMLIR --> TTMETAL --> HW
```

Sources: [README.md:19-19](), [docs/src/getting_started_build_from_source.md:174-187]()

---
```


**Diagram: Component interaction from user code to hardware**

Sources: [README.md 19](https://github.com/tenstorrent/tt-xla/blob/c77995f6/README.md?plain=1#L19-L19)[docs/src/getting_started_build_from_source.md 174-187](https://github.com/tenstorrent/tt-xla/blob/c77995f6/docs/src/getting_started_build_from_source.md?plain=1#L174-L187)

* * *

## Choosing a Setup Path

There are three ways to install tt-xla. The table below summarizes when to use each.

| Path | Package / Entry Point | Best For |
| --- | --- | --- |
| **pip wheel** | `pip install pjrt-plugin-tt` | Running models; fastest setup |
| **Docker container** | `ghcr.io/tenstorrent/tt-xla-slim:latest` | Isolated environment; no system changes |
| **Build from source** | `source venv/activate && cmake ...` | Developing or modifying tt-xla |

Detailed instructions for each path are in the sub-pages:

 — pip wheel, Docker, and build-from-source details
 — TT-Installer, hugepages, and `tt-smi`
 — a concrete end-to-end example

* * *

## Setup Decision Flow

**Diagram: Choosing a setup path**

Sources: [docs/src/getting_started.md 19-23](https://github.com/tenstorrent/tt-xla/blob/c77995f6/docs/src/getting_started.md?plain=1#L19-L23)

* * *

## Hardware Prerequisites

Before any software installation, the host machine must have Tenstorrent hardware configured. This requires:

1.   Running the [TT-Installer](https://docs.tenstorrent.com/getting-started/README.html) to install device drivers and firmware.
2.   Rebooting the machine.
3.   Enabling hugepages: `sudo systemctl enable --now 'dev-hugepages\x2d1G.mount'sudo systemctl enable --now tenstorrent-hugepages.service`
4.   Activating the virtual environment created by TT-Installer: `source ~/.tenstorrent-venv/bin/activate`
5.   Verifying device visibility with `tt-smi`, which displays real-time device stats and health.

See [Hardware Configuration](https://deepwiki.com/tenstorrent/tt-xla/2.2-hardware-configuration) for the full walkthrough.

Sources: [docs/src/getting_started.md 25-47](https://github.com/tenstorrent/tt-xla/blob/c77995f6/docs/src/getting_started.md?plain=1#L25-L47)

* * *

## Quick Install (Wheel Path)

Once hardware is configured, install the wheel in your active virtual environment:

`pip install pjrt-plugin-tt --extra-index-url https://pypi.eng.aws.tenstorrent.com/`
**Install options:**

*   Stable releases: use the command above
*   Pre-release builds: add `--pre` flag
*   Nightly builds: download from [GitHub Releases](https://github.com/tenstorrent/tt-xla/blob/c77995f6/GitHub%20Releases)

After installation, verify the plugin is loaded correctly:

`python -c "import jax; print(jax.devices('tt'))"`
Expected output (example):

```
[TTDevice(id=0, arch=Wormhole_b0)]
```

This confirms that:

1.   `jax_plugin_tt/__init__.py` imported successfully
2.   The PJRT plugin (`pjrt_plugin_tt.so`) loaded without errors
3.   The plugin successfully enumerated Tenstorrent devices via the PJRT `PJRT_Client_Devices` API
4.   JAX can now dispatch computations to `TTDevice` instances

If you see an error or an empty list, check [Hardware Configuration](https://deepwiki.com/tenstorrent/tt-xla/2.2-hardware-configuration) to ensure drivers and hugepages are configured.

Sources: [docs/src/getting_started.md 51-88](https://github.com/tenstorrent/tt-xla/blob/c77995f6/docs/src/getting_started.md?plain=1#L51-L88)[docs/src/getting_started_build_from_source.md 154-159](https://github.com/tenstorrent/tt-xla/blob/c77995f6/docs/src/getting_started_build_from_source.md?plain=1#L154-L159)

* * *

## Key Files and Entry Points

```mermaid
graph TB
    subgraph "Python Package (installed via pip)"
        WHL["pjrt-plugin-tt<br/>(Python wheel)"]
        
        subgraph "JAX Integration"
            JAXPKG["jax_plugin_tt/__init__.py"]
            JAXREG["Calls xla_client.register_plugin<br/>with 'tt' as platform name"]
        end
        
        subgraph "PyTorch Integration"
            PTPKG["torch_plugin_tt/__init__.py"]
            PTREG["Registers with torch_xla<br/>plugin system"]
        end
        
        subgraph "Core Plugin Binary"
            PJRTDIR["pjrt_plugin_tt/"]
            PJRTSO["pjrt_plugin_tt.so<br/>(PJRT C API implementation)"]
            PJRTINIT["Exports PJRT_Plugin_Initialize"]
        end
        
        subgraph "Runtime Dependencies"
            TTMETAL["pjrt_plugin_tt/tt-metal/<br/>(kernels, RISC-V compiler)"]
            LIBS["pjrt_plugin_tt/lib/<br/>(libTTMLIRCompiler.so,<br/>libTTMLIRRuntime.so)"]
        end
    end
    
    subgraph "Framework Discovery"
        JAXLOAD["JAX imports jax_plugin_tt<br/>at startup (entry point)"]
        PTLOAD["PyTorch/XLA imports torch_plugin_tt<br/>at startup (entry point)"]
    end
    
    WHL --> JAXPKG
    WHL --> PTPKG
    WHL --> PJRTDIR
    WHL --> TTMETAL
    WHL --> LIBS
    
    JAXLOAD --> JAXPKG --> JAXREG --> PJRTSO
    PTLOAD --> PTPKG --> PTREG --> PJRTSO
    
    PJRTSO --> PJRTINIT
    PJRTSO --> LIBS
    PJRTSO --> TTMETAL
```

The plugin uses Python's [entry point mechanism](https://packaging.python.org/en/latest/specifications/entry-points/) to automatically register with JAX and PyTorch/XLA when the packages are imported. This means no manual configuration is needed — just import JAX or PyTorch/XLA and the `tt` backend is available.

Sources: [docs/src/getting_started_build_from_source.md:174-187]()

---
```


**Diagram: Package structure and plugin loading mechanism**

The plugin uses Python's [entry point mechanism](https://packaging.python.org/en/latest/specifications/entry-points/) to automatically register with JAX and PyTorch/XLA when the packages are imported. This means no manual configuration is needed — just import JAX or PyTorch/XLA and the `tt` backend is available.

Sources: [docs/src/getting_started_build_from_source.md 174-187](https://github.com/tenstorrent/tt-xla/blob/c77995f6/docs/src/getting_started_build_from_source.md?plain=1#L174-L187)

* * *

## System Dependencies (Build from Source Only)

If building from source, the following are required on Ubuntu 22.04:

| Dependency | Version |
| --- | --- |
| Python | 3.12 |
| Clang | 17 |
| GCC | 12 |
| CMake | 4.0.3 |
| Ninja | any recent |
| OpenMPI | 5.0.7-ulfm |

Additional libraries: `protobuf-compiler`, `libprotobuf-dev`, `ccache`, `libnuma-dev`, `libhwloc-dev`, `libboost-all-dev`.

The build also depends on the TT-MLIR toolchain (built separately from the [tt-mlir](https://github.com/tenstorrent/tt-xla/blob/c77995f6/tt-mlir) repository). The environment variable `TTMLIR_TOOLCHAIN_DIR` must point to the toolchain directory before running CMake.

See [Installation Options](https://deepwiki.com/tenstorrent/tt-xla/2.1-installation-options) and [Development Environment Setup](https://deepwiki.com/tenstorrent/tt-xla/8.1-development-environment-setup) for the complete procedures.

Sources: [docs/src/getting_started_build_from_source.md 22-113](https://github.com/tenstorrent/tt-xla/blob/c77995f6/docs/src/getting_started_build_from_source.md?plain=1#L22-L113)

* * *

## Where to Go Next

| Goal | Page |
| --- | --- |
| Detailed install instructions (wheel, Docker, source) | [Installation Options](https://deepwiki.com/tenstorrent/tt-xla/2.1-installation-options) |
| Configure hardware (TT-Installer, hugepages, tt-smi) | [Hardware Configuration](https://deepwiki.com/tenstorrent/tt-xla/2.2-hardware-configuration) |
| Run a concrete JAX or PyTorch model | [Running Your First Model](https://deepwiki.com/tenstorrent/tt-xla/2.3-running-your-first-model) |
| Understand how the PJRT plugin works | [PJRT Plugin System](https://deepwiki.com/tenstorrent/tt-xla/4.1-compilation-pipeline) |
| Understand the compilation pipeline | [MLIR Compilation Pipeline](https://deepwiki.com/tenstorrent/tt-xla/4.2-pjrt-plugin-system) |
| Set up a development environment | [Development Environment Setup](https://deepwiki.com/tenstorrent/tt-xla/8.1-development-environment-setup) |
| Explore the test suite | [Testing Infrastructure](https://deepwiki.com/tenstorrent/tt-xla/6-testing-infrastructure) |

Sources: [docs/src/getting_started.md 91-99](https://github.com/tenstorrent/tt-xla/blob/c77995f6/docs/src/getting_started.md?plain=1#L91-L99)[README.md 22-24](https://github.com/tenstorrent/tt-xla/blob/c77995f6/README.md?plain=1#L22-L24)

This wiki is featured in the [repository](https://github.com/tenstorrent/tt-xla/blob/main/README.md)

Dismiss
Refresh this wiki

Enter email to refresh

## Additional Diagrams


#### Installed Wheel Structure


```mermaid
graph TD
    Root["pjrt-plugin-tt wheel"] --> PjrtPkg["pjrt_plugin_tt/"]
    Root --> JaxPkg["jax_plugin_tt/"]
    Root --> TorchPkg["torch_plugin_tt/"]
    
    PjrtPkg --> Init1["__init__.py"]
    PjrtPkg --> So["pjrt_plugin_tt.so"]
    PjrtPkg --> Metal["tt-metal/"]
    PjrtPkg --> Lib["lib/"]
    
    Metal --> Kernels["Device kernels"]
    Metal --> Riscv["RISC-V compiler/linker"]
    
    Lib --> TtmlirLib["tt-mlir libraries"]
    Lib --> MetalLib["tt-metal libraries"]
    
    JaxPkg --> Init2["__init__.py"]
    Init2 --> Setup1["Import and setup PJRT plugin for JAX"]
    
    TorchPkg --> Init3["__init__.py"]
    Init3 --> Setup2["Import and setup PJRT plugin for PyTorch/XLA"]
```


#### Container Architecture


```mermaid
graph TB
    subgraph Host["Host System"]
        Drivers["/dev/tenstorrent<br/>Device drivers"]
        Hugepages["/dev/hugepages-1G<br/>1GB hugepages"]
        DockerDaemon["Docker daemon"]
    end
    
    subgraph Container["TT-XLA Container<br/>(ghcr.io/tenstorrent/tt-xla-slim)"]
        Image["Base image<br/>Ubuntu 22.04"]
        Python["Python 3.12"]
        Wheel["pjrt-plugin-tt wheel"]
        TtForge["TT-Forge demos"]
        
        Image --> Python
        Python --> Wheel
        Wheel --> Models["Ready to run models"]
    end
    
    Drivers -.Mount via --device.-> Container
    Hugepages -.Mount via -v.-> Container
    DockerDaemon --> Container
```


### Installation Approach Decision Guide


```mermaid
graph TD
    Start["Need to install TT-XLA"] --> Question1{"Modifying TT-XLA<br/>source code?"}
    
    Question1 -->|Yes| BuildSource["Build from Source"]
    Question1 -->|No| Question2{"Need environment<br/>isolation?"}
    
    Question2 -->|Yes| Question3{"Docker already<br/>installed?"}
    Question2 -->|No| Question4{"Complex Python<br/>environment?"}
    
    Question3 -->|Yes| Docker["Docker Deployment"]
    Question3 -->|No| Question5{"Can install<br/>Docker?"}
    
    Question5 -->|Yes| Docker
    Question5 -->|No| Wheel["Wheel Installation"]
    
    Question4 -->|Yes| Docker
    Question4 -->|No| Wheel
    
    BuildSource --> DevTools["Install dev tools:<br/>CMake, Clang 17, GCC 12"]
    BuildSource --> TtmlirStep["Build TT-MLIR toolchain"]
    BuildSource --> CloneBuild["Clone and build TT-XLA"]
    
    Docker --> DockerInstall["Install Docker"]
    Docker --> DockerRun["docker run with device mounts"]
    
    Wheel --> VenvCreate["Create/activate venv"]
    Wheel --> PipInstall["pip install pjrt-plugin-tt"]
    
    CloneBuild --> Verify["Verify with jax.devices()"]
    DockerRun --> Verify
    PipInstall --> Verify
    
    Verify --> Done["Ready to run models"]
```


#### Hugepages Configuration


```mermaid
graph LR
    subgraph "System Memory"
        A["Standard Pages<br/>4KB"] 
        B["Hugepages<br/>1GB"]
    end
    
    subgraph "Tenstorrent Device"
        C["Device Memory"]
        D["DMA Engine"]
    end
    
    B --> D
    D --> C
    
    A -.->|"Not Used for<br/>Device I/O"| D
```

Hugepages reduce TLB (Translation Lookaside Buffer) misses during DMA operations, improving data transfer performance between host and device memory.
```


### Example Test Patterns


```mermaid
graph LR
    OpTest["run_op_test()"]
    GraphTest["run_graph_test()"]
    ModelTest["ModelTester.test()"]
    
    subgraph "Test Utilities"
        Framework["Framework.TORCH / Framework.JAX"]
        CompConfig["ComparisonConfig(pcc=0.99)"]
        Evaluator["TorchComparisonEvaluator"]
    end
    
    OpTest --> Framework
    GraphTest --> CompConfig
    ModelTest --> Evaluator
```

The test infrastructure provides reusable patterns for validating model compilation and output correctness.
```


### Wheel Layout


```mermaid
graph TD
    W["pjrt-plugin-tt wheel (.whl)"]
    W --> PKG["pjrt_plugin_tt/"]
    W --> JAX["jax_plugin_tt/"]
    W --> TCH["torch_plugin_tt/"]
    W --> TRACY["tracy/"]
    W --> TTNN["ttnn/"]
    W --> TOOLS["ttxla_tools/"]

    PKG --> SO["pjrt_plugin_tt.so (CMake install)"]
    PKG --> METAL["tt-metal/ (runtime kernels, riscv toolchain)"]
    PKG --> LIB["lib/ (tt-mlir, tt-metal shared libs)"]

    JAX --> JAX_INIT["__init__.py (XLA plugin init)"]
    TCH --> TCH_INIT["__init__.py (TTPlugin registration)"]
    TRACY --> TRACY_ORIG["_original/ (wrapped tracy from tt-metal)"]
    TTNN --> TTNN_ORIG["_original/ (wrapped ttnn from tt-metal)"]
    TOOLS --> SFPI["install_sfpi.py (tt-forge-install entrypoint)"]
```

Sources: [python_package/setup.py:22-43]()

---
```


#### Package Layout


```mermaid
graph TB
    subgraph "Wheel Package: pjrt_plugin_tt-*.whl"
        subgraph "pjrt_plugin_tt/"
            Init1["__init__.py"]
            PluginSO["pjrt_plugin_tt.so<br/>PJRT C API"]
            TTMetal["tt-metal/<br/>Runtime dependencies"]
            Libs["lib/<br/>Shared libraries<br/>libTTMLIR*.so"]
        end
        
        subgraph "jax_plugin_tt/"
            Init2["__init__.py<br/>JAX registration"]
        end
        
        subgraph "torch_plugin_tt/"
            Init3["__init__.py<br/>PyTorch registration"]
        end
    end
    
    subgraph "JAX Framework"
        JAXRuntime["JAX Runtime"]
        PluginRegistry["XLA Plugin Registry"]
    end
    
    Init2 --> |"imports and<br/>configures"| Init1
    Init2 --> |"registers with"| PluginRegistry
    PluginRegistry --> |"loads"| PluginSO
    JAXRuntime --> |"uses"| PluginRegistry
```

**Wheel Package Structure and Loading Flow**
```


#### Core Execution Steps


```mermaid
graph TB
    subgraph "Initialization"
        A["RequirementsManager.for_loader()"]
        B["ModelLoader.load_model()"]
        C["ModelLoader.load_inputs()"]
        D["_initialize_workload()"]
    end
    
    subgraph "Configuration"
        E["ModelTestConfig.to_comparison_config()"]
        F["CompilerConfig setup"]
        G["test_metadata resolution"]
    end
    
    subgraph "CPU Baseline"
        H["_compile_for_cpu()"]
        I["_run_on_cpu()"]
        J["Store CPU result"]
    end
    
    subgraph "TT Device Execution"
        K["_compile_for_tt_device()"]
        L["_run_on_tt_device()"]
        M["Store TT result"]
    end
    
    subgraph "Validation"
        N["Evaluator.compare()"]
        O["ComparisonResult creation"]
        P["Evaluator._assert_on_results()"]
    end
    
    subgraph "Reporting"
        Q["record_model_test_properties()"]
        R["update_test_metadata_for_exception()"]
        S["pytest.skip() or pytest.xfail()"]
    end
    
    A --> B
    B --> C
    C --> D
    E --> D
    F --> D
    G --> E
    
    D --> H
    H --> I
    I --> J
    
    D --> K
    K --> L
    L --> M
    
    J --> N
    M --> N
    N --> O
    O --> P
    
    P --> Q
    P --> R
    Q --> S
```

**Execution Steps**: After initialization and configuration, the model is executed on CPU to establish a baseline, then on TT device for comparison. Results are validated and recorded, with appropriate pytest markers applied based on status.

Sources: [tests/infra/testers/single_chip/model/model_tester.py:1-250](), [tests/runner/test_utils.py:483-628]()
```


#### ComponentChecker Enum


```mermaid
graph TB
    Exception["Exception Raised"]
    
    ComponentChecker{"ComponentChecker<br/>checks_xla.py:13-151"}
    
    METAL["METAL<br/>(libtt_metal.so)"]
    TTNN["TTNN<br/>(_ttnncpp.so, libTTMLIRCompiler.so)"]
    XLA["XLA<br/>(tt_torch/backend, torch_device_runner)"]
    TORCH["TORCH<br/>(torch/utils/_pytree.py)"]
    SWEEPS["SWEEPS<br/>(operators/utils/verify.py)"]
    
    Exception --> ComponentChecker
    ComponentChecker -->|"stack trace contains<br/>libtt_metal.so"| METAL
    ComponentChecker -->|"stack trace contains<br/>_ttnncpp.so but not libtt_metal"| TTNN
    ComponentChecker -->|"no compiler/runtime libs<br/>in stack trace"| XLA
    ComponentChecker -->|"torch utils in<br/>stack trace"| TORCH
    ComponentChecker -->|"verify.py in<br/>stack trace"| SWEEPS
    
    style ComponentChecker fill:#f9f9f9
```


#### Available Preset Files


```mermaid
graph TB
    subgraph "Push Tests"
        BasicTest["basic-test.json<br/>Quick validation on PR"]
    end
    
    subgraph "Nightly Tests"
        NightlyBasic["basic-test-nightly.json<br/>Comprehensive nightly suite"]
        NightlyExperimental["schedule-nightly-experimental.yml<br/>Experimental models"]
    end
    
    subgraph "Model Tests"
        ModelPassing["model-test-passing.json<br/>EXPECTED_PASSING tests"]
        ModelXFail["model-test-xfail.json<br/>KNOWN_FAILURE/SKIP tests"]
        ModelExperimental["model-test-experimental.json<br/>UNKNOWN/experimental tests"]
        ModelFull["model-test-full.json<br/>All model tests"]
    end
    
    subgraph "Specialized"
        VLLMTests["vllm-model-tests.json<br/>vLLM plugin tests"]
    end
```


#### End-to-End Flow


```mermaid
graph TB
    subgraph "Matrix Generation"
        Preset["Load Preset JSON<br/>model-test-passing.json"] --> ParseEntries["Parse Matrix Entries<br/>3 jobs for n150<br/>4 jobs for n300-llmbox"]
    end
    
    subgraph "Job Preparation"
        ParseEntries --> CreateJobs["Create Parallel Jobs<br/>strategy.matrix<br/>fail-fast: false"]
        CreateJobs --> DownloadWheel["Download Wheel<br/>gh run download"]
        DownloadWheel --> InstallWheel["Install Wheel<br/>uv pip install"]
    end
    
    subgraph "Test Collection"
        InstallWheel --> CheckRerun["Check Rerun Attempt<br/>.pytest_tests_to_run"]
        CheckRerun --> CollectTests["Collect Tests<br/>pytest --collect-only"]
        CollectTests --> CalculateTimeout["Calculate Timeout<br/>calculate_test_timeout.py"]
    end
    
    subgraph "Test Execution"
        CalculateTimeout --> SplitTests["Split Tests<br/>pytest-split<br/>least_duration"]
        SplitTests --> RunPytest["Run pytest<br/>--forked (torch)<br/>--splits N --group M"]
        RunPytest --> GenerateReport["Generate JUnit XML<br/>--junitxml=report.xml"]
    end
    
    subgraph "Result Upload"
        GenerateReport --> UploadLog["Upload Test Log<br/>pytest.log"]
        UploadLog --> UploadReport["Upload Test Report<br/>test-reports-hash-attempt.index-*"]
        UploadReport --> UploadPerf["Upload Perf Report<br/>perf-reports-*"]
    end
```


#### CMake Directory Structure


```mermaid
graph TB
    subgraph "Source Tree"
        ROOT["CMakeLists.txt<br/>(root)"]
        THIRD["third_party/<br/>CMakeLists.txt"]
        PJRT_API["third_party/pjrt_c_api/<br/>(submodule)"]
        SRC["src/common/<br/>CMakeLists.txt"]
        DOCS["docs/<br/>CMakeLists.txt"]
        PKG["python_package/<br/>CMakeLists.txt"]
    end
    
    subgraph "Configuration"
        ENV_CHECK["Check TTXLA_ENV_ACTIVATED"]
        COMPILER["Set clang/clang++"]
        OPTIONS["Build options<br/>(Explorer, Tests, etc.)"]
        PATHS["Set install paths<br/>TTMLIR_INSTALL_PREFIX<br/>LOGURU_INSTALL_PREFIX"]
    end
    
    subgraph "Subdirectories"
        SUB_SRC["src/common/"]
        SUB_THIRD["third_party/"]
        SUB_DOCS["docs/"]
        SUB_PKG["python_package/"]
    end
    
    ROOT --> ENV_CHECK
    ENV_CHECK --> COMPILER
    COMPILER --> OPTIONS
    OPTIONS --> PATHS
    PATHS --> SUB_SRC
    PATHS --> SUB_THIRD
    PATHS --> SUB_DOCS
    PATHS --> SUB_PKG
    
    SUB_SRC -.builds.-> SRC
    SUB_THIRD -.builds.-> THIRD
    SUB_THIRD -.references.-> PJRT_API
    SUB_DOCS -.builds.-> DOCS
    SUB_PKG -.builds.-> PKG
    
    style ROOT fill:#e3f2fd
    style ENV_CHECK fill:#fff3e0
    style SRC fill:#e8f5e9
    style THIRD fill:#e8f5e9
```


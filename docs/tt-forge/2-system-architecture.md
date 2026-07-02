---
project: tt-forge
github: https://github.com/tenstorrent/tt-forge
deepwiki: https://deepwiki.com/tenstorrent/tt-forge/2-system-architecture
---

# TT-Forge Software Stack

Relevant source files
*   [.github/workflows/pages.yml](https://github.com/tenstorrent/tt-forge/blob/6f2d9645/.github/workflows/pages.yml)
*   [README.md](https://github.com/tenstorrent/tt-forge/blob/6f2d9645/README.md?plain=1)
*   [demos/README.md](https://github.com/tenstorrent/tt-forge/blob/6f2d9645/demos/README.md?plain=1)
*   [docs/.gitignore](https://github.com/tenstorrent/tt-forge/blob/6f2d9645/docs/.gitignore)
*   [docs/book.toml](https://github.com/tenstorrent/tt-forge/blob/6f2d9645/docs/book.toml)
*   [docs/src/SUMMARY.md](https://github.com/tenstorrent/tt-forge/blob/6f2d9645/docs/src/SUMMARY.md?plain=1)
*   [docs/src/getting_started.md](https://github.com/tenstorrent/tt-forge/blob/6f2d9645/docs/src/getting_started.md?plain=1)
*   [docs/src/model-bring-up-guide.md](https://github.com/tenstorrent/tt-forge/blob/6f2d9645/docs/src/model-bring-up-guide.md?plain=1)

## Purpose and Scope

This document describes the complete software stack architecture of TT-Forge, Tenstorrent's open-source AI compiler system. It details the layered flow from high-level AI/ML frameworks (PyTorch, JAX, ONNX) through the MLIR compiler dialects, down to the low-level runtime and hardware execution. For detailed information about specific layers, see:

 — Detail the three frontend systems (TT-XLA, TT-Forge-FE, TT-Torch) that ingest models from various ML frameworks.
 — Explain the compiler architecture, intermediate representations (StableHLO, TTIR, TTNN-IR, TTMetal-IR, TTKernel-IR), and optimization passes.
 — Describe TT-Metalium runtime (TTNN library, TTMetal SDK, LLK), system software (UMD/KMD), and supported hardware (Wormhole, Blackhole).

**Sources:**[README.md 15-32](https://github.com/tenstorrent/tt-forge/blob/6f2d9645/README.md?plain=1#L15-L32)[docs/src/model-bring-up-guide.md 26-65](https://github.com/tenstorrent/tt-forge/blob/6f2d9645/docs/src/model-bring-up-guide.md?plain=1#L26-L65)

## Architectural Overview

The TT-Forge stack is designed to provide a seamless path from standard ML framework code to optimized Tenstorrent hardware execution. The architecture is modular, allowing different frontends to share a common compiler backend and runtime.

| Layer | Primary Components | Role |
| --- | --- | --- |
| **Framework** | PyTorch, JAX, ONNX, TensorFlow, PaddlePaddle | User-facing model definition and training/inference logic. |
| **Frontend** | `tt-xla`, `tt-forge-onnx` | Translates framework-specific graphs into MLIR (StableHLO or TTIR). |
| **Compiler** | `tt-mlir` | Performs graph optimizations, tiling (32x32), and lowering across dialects. |
| **Runtime** | `tt-metal` (TTNN + TTMetal) | Dispatches operations, manages SRAM/DRAM, and executes kernels. |
| **Hardware** | Wormhole, Blackhole | Physical silicon execution on Tensix cores. |

**Sources:**[README.md 25-32](https://github.com/tenstorrent/tt-forge/blob/6f2d9645/README.md?plain=1#L25-L32)[docs/src/model-bring-up-guide.md 30-65](https://github.com/tenstorrent/tt-forge/blob/6f2d9645/docs/src/model-bring-up-guide.md?plain=1#L30-L65)[docs/src/model-bring-up-guide.md 166-184](https://github.com/tenstorrent/tt-forge/blob/6f2d9645/docs/src/model-bring-up-guide.md?plain=1#L166-L184)

## Software Stack Flow

```mermaid
graph TD
    subgraph FrameworkSpace["Natural Language & Framework Space"]
        PT["PyTorch"]
        JX["JAX"]
        ONNX["ONNX / TF / Paddle"]
    end

    subgraph FrontendLayer["Frontend Layer (Code Entities)"]
        TTXLA["tt-xla (PJRT Plugin)"]
        TTFORGEONNX["tt-forge-onnx (TVM-based)"]
        TTTORCH["tt-torch (Deprecated)"]
    end

    subgraph CompilerLayer["TT-MLIR Compiler Layer"]
        SHLO["StableHLO"]
        TTIR["TTIR Dialect"]
        TTNNIR["TTNN Dialect"]
        TTMETALIR["TTMetal Dialect"]
    end

    subgraph RuntimeLayer["Runtime Layer (TT-Metalium)"]
        TTNNLIB["ttnn Library"]
        TTMETALSYS["tt-metal (Metal SDK)"]
        LLK["LLK (Low Level Kernels)"]
    end

    PT -->|torch.compile| TTXLA
    JX -->|jax.jit| TTXLA
    ONNX --> TTFORGEONNX
    
    TTXLA -->|Produces| SHLO
    TTFORGEONNX -->|Produces| TTIR
    
    SHLO --> TTIR
    TTIR -->|Passes: Fusing/Sharding| TTNNIR
    TTNNIR -->|Lowers to| TTMETALIR
    
    TTNNIR --> TTNNLIB
    TTMETALIR --> TTMETALSYS
    TTMETALSYS --> LLK
    
    LLK --> HW["Wormhole / Blackhole Hardware"]
```


The following diagram illustrates the end-to-end flow of a model through the TT-Forge stack, mapping framework entry points to the internal code entities and libraries.

**Sources:**[README.md 25-32](https://github.com/tenstorrent/tt-forge/blob/6f2d9645/README.md?plain=1#L25-L32)[docs/src/model-bring-up-guide.md 30-65](https://github.com/tenstorrent/tt-forge/blob/6f2d9645/docs/src/model-bring-up-guide.md?plain=1#L30-L65)[demos/README.md 5-18](https://github.com/tenstorrent/tt-forge/blob/6f2d9645/demos/README.md?plain=1#L5-L18)

## Frontend Layer

The Frontend Layer is the entry point for models. It abstracts the complexities of different ML frameworks and produces a standardized representation for the compiler.

*   **TT-XLA (Primary):** The recommended frontend for PyTorch and JAX. It uses the PJRT interface to produce StableHLO graphs. It supports both single-chip and multi-chip (Tensor Parallelism/SPMD) configurations.
*   **TT-Forge-ONNX:** A TVM-based frontend for ONNX, TensorFlow, and PaddlePaddle. It generates TTIR directly but is currently limited to single-chip projects.
*   **TT-Torch:** A legacy frontend for PyTorch; developers are encouraged to migrate to the `tt-xla``torch.compile` path.

For details, see [Frontend Layer](https://deepwiki.com/tenstorrent/tt-forge/2.1-frontend-layer).

**Sources:**[README.md 27-28](https://github.com/tenstorrent/tt-forge/blob/6f2d9645/README.md?plain=1#L27-L28)[docs/src/model-bring-up-guide.md 36-43](https://github.com/tenstorrent/tt-forge/blob/6f2d9645/docs/src/model-bring-up-guide.md?plain=1#L36-L43)[demos/README.md 7-13](https://github.com/tenstorrent/tt-forge/blob/6f2d9645/demos/README.md?plain=1#L7-L13)

## TT-MLIR Compiler Layer

The `tt-mlir` compiler is the heart of the stack. It takes high-level IR (StableHLO or TTIR) and applies hardware-specific transformations.

*   **Tiling:** Automatically handles the conversion of tensors to the hardware-native 32x32 tile format.
*   **Memory Management:** Determines data placement between interleaved DRAM and fast local SRAM (L1).
*   **Optimization Passes:** Includes operation fusing, layout transformations, and sharding for multi-device execution.
*   **Dialects:** Progressively lowers code through `TTIR` ->`TTNN-IR` ->`TTMetal-IR` ->`TTKernel-IR`.

For details, see [TT-MLIR Compiler Layer](https://deepwiki.com/tenstorrent/tt-forge/2.2-tt-mlir-compiler-layer).

**Sources:**[README.md 29](https://github.com/tenstorrent/tt-forge/blob/6f2d9645/README.md?plain=1#L29-L29)[docs/src/model-bring-up-guide.md 47-54](https://github.com/tenstorrent/tt-forge/blob/6f2d9645/docs/src/model-bring-up-guide.md?plain=1#L47-L54)[docs/src/model-bring-up-guide.md 166-184](https://github.com/tenstorrent/tt-forge/blob/6f2d9645/docs/src/model-bring-up-guide.md?plain=1#L166-L184)

## Runtime and Hardware Layers

The Runtime Layer, known as **TT-Metalium**, manages the actual execution of optimized kernels on Tenstorrent silicon.

*   **TTNN:** A high-level C++/Python library of optimized operations (convolutions, matmuls, etc.) that the compiler targets by default.
*   **TT-Metal:** The underlying SDK for dispatching kernels, managing device memory, and coordinating Tensix cores.
*   **System Drivers:** Includes the User Mode Driver (UMD) and Kernel Mode Driver (KMD) for PCIe communication and device initialization.
*   **Hardware Targets:** Supports `Wormhole_b0` and the next-generation `Blackhole` architectures.

For details, see [Runtime and Hardware Layers](https://deepwiki.com/tenstorrent/tt-forge/2.3-runtime-and-hardware-layers).

**Sources:**[README.md 15](https://github.com/tenstorrent/tt-forge/blob/6f2d9645/README.md?plain=1#L15-L15)[docs/src/model-bring-up-guide.md 57-64](https://github.com/tenstorrent/tt-forge/blob/6f2d9645/docs/src/model-bring-up-guide.md?plain=1#L57-L64)[docs/src/model-bring-up-guide.md 95-97](https://github.com/tenstorrent/tt-forge/blob/6f2d9645/docs/src/model-bring-up-guide.md?plain=1#L95-L97)

## Data and Execution Flow Architecture

```mermaid
graph TB
    subgraph UserSpace["User Python Space"]
        API["tt_torch / jax.jit"]
        COMP["torch.compile(backend='tt')"]
    end

    subgraph CompilerEntities["Compiler Logic (tt-mlir)"]
        OPT["optimization_level Pass"]
        SHARD["Sharding / SPMD Pass"]
        TILE["32x32 Tiling Engine"]
    end

    subgraph RuntimeEntities["Runtime & Drivers"]
        UMD["UMD (User Mode Driver)"]
        KMD["KMD (Kernel Mode Driver)"]
        DEVICE["tt_metal::Device"]
    end

    API --> COMP
    COMP -->|Calls| TTXLA["tt-xla / PJRT"]
    TTXLA --> OPT
    OPT --> SHARD
    SHARD --> TILE
    TILE -->|Generates| BIN["Device Binaries / Kernels"]
    
    BIN --> DEVICE
    DEVICE --> UMD
    UMD --> KMD
    KMD --> HW["Tenstorrent ASIC"]
```


This diagram bridges the gap between the high-level Python API calls and the low-level hardware drivers.

**Sources:**[README.md 48-71](https://github.com/tenstorrent/tt-forge/blob/6f2d9645/README.md?plain=1#L48-L71)[docs/src/model-bring-up-guide.md 99-130](https://github.com/tenstorrent/tt-forge/blob/6f2d9645/docs/src/model-bring-up-guide.md?plain=1#L99-L130)[docs/src/model-bring-up-guide.md 156-184](https://github.com/tenstorrent/tt-forge/blob/6f2d9645/docs/src/model-bring-up-guide.md?plain=1#L156-L184)

Dismiss
Refresh this wiki

Enter email to refresh

## Additional Diagrams


#### Data Flow


```mermaid
graph TD
    subgraph "Input Formats"
        ONNX_F[".onnx"]
        TF_F["TensorFlow Protobuf"]
        Paddle_F["Paddle Model"]
    end

    subgraph "TT-Forge-ONNX Pipeline"
        TVM_Ingest["TT-TVM Ingestion"]
        Relay["TVM Relay IR"]
        ForgeCompile["forge.compile()"]
    end

    subgraph "MLIR Layer"
        TTIR["TTIR Dialect"]
    end

    ONNX_F --> TVM_Ingest
    TF_F --> TVM_Ingest
    Paddle_F --> TVM_Ingest
    TVM_Ingest --> Relay
    Relay --> ForgeCompile
    ForgeCompile --> TTIR
```

Sources: [README.md:28](), [demos/README.md:11-13]()

---
```


#### Runtime Configuration


```mermaid
graph LR
    subgraph "Environment and Runtime"
        SetDevice["xr.set_device_type('TT')<br/>torch_xla.runtime"]
        GetDevice["xm.xla_device()<br/>torch_xla.core.xla_model"]
    end
    
    subgraph "Compile Options"
        EnableOpt["enable_optimizer=True"]
        MemLayout["enable_memory_layout_analysis=True"]
        FuseConv["enable_fusing_conv2d_with_multiply_pattern=True"]
    end
    
    subgraph "Execution"
        CompileModel["torch.compile(model, backend='tt')"]
        ToDevice["model.to(device)"]
    end
    
    SetDevice --> GetDevice
    GetDevice --> ToDevice
    EnableOpt --> CompileModel
    MemLayout --> CompileModel
    FuseConv --> CompileModel
    CompileModel --> ToDevice
```

Sources: [demos/tt-xla/cnn/resnet_demo.py:26-38](), [demos/tt-xla/cnn/arnold_demo.py:18-30](), [benchmark/tt-xla/resnet.py:150-159]()
```


#### System Data Flow: Configuration to Results


```mermaid
graph TD
    Entry["benchmark(config: dict)"]
    ParseConfig["Parse Configuration<br/>batch_size, loop_count,<br/>data_format, task"]
    LoadModel["Load Model via ModelLoader<br/>UNetLoader / SegformerLoader / ViTLoader"]
    PrepareInputs["Prepare Input Tensors<br/>load_benchmark_dataset()<br/>or random torch.randn()"]
    ConfigXLA["Configure torch-xla<br/>torch_xla.set_custom_compile_options()"]
    Compile["Compile Model<br/>torch.compile(backend='tt')"]
    Warmup["Warmup Phase<br/>torch_xla_warmup_model()"]
    Measure["Benchmark Measurement<br/>torch_xla_measure_fps()"]
    Validate["PCC Validation<br/>compute_pcc()"]
    Serialize["Serialize Modules<br/>serialize_modules()"]
    Results["Create Benchmark Result<br/>create_benchmark_result()"]
    
    Entry --> ParseConfig
    ParseConfig --> LoadModel
    LoadModel --> PrepareInputs
    PrepareInputs --> ConfigXLA
    ConfigXLA --> Compile
    Compile --> Warmup
    Warmup --> Measure
    Measure --> Validate
    Validate --> Serialize
    Serialize --> Results
```


#### Configuration Hierarchy


```mermaid
graph LR
    CLI["Command-line Args<br/>--batch-size 32"]
    JSON["perf-bench-matrix.json<br/>variant config"]
    Defaults["BATCH_SIZE = 32<br/>llms.py constants"]
    
    CLI -->|"Override"| Final["Final Configuration"]
    JSON -->|"Override if CLI None"| Final
    Defaults -->|"Fallback"| Final
    
    style Final fill:#f9f9f9
```


#### Superset API Integration


```mermaid
graph LR
    subgraph "GitHub Workflow"
        Step1["superset-api action"]
    end
    
    subgraph "Infrastructure"
        APIGateway["AWS API Gateway<br/>(SigV4 Auth)"]
        Lambda["Lambda/Compute"]
        Postgres["Postgres Database<br/>(Superset Data)"]
    end
    
    Step1 --> APIGateway
    APIGateway --> Lambda
    Lambda --> Postgres
```


#### Logic and Data Flow


```mermaid
graph TD
    subgraph "Trigger Sources"
        WorkflowDispatch["workflow_dispatch<br/>(Manual)"]
        WorkflowCall["workflow_call<br/>(From release.yml)"]
    end
    
    subgraph "Input Configuration"
        DockerImage["docker-image<br/>Default: tt-forge-slim:nightly-latest"]
        ParentRunID["parent_run_id<br/>Workflow tracking"]
        ProjectFilter["project-filter<br/>Choices: tt-forge-onnx, tt-torch, tt-xla, tt-forge"]
        RunnerFilter["runner-filter<br/>Choices: n150, p150, All"]
    end
    
    subgraph "Job: build_matrix"
        SetMatrix["set-matrix step"]
        FilterLogic["Filter Logic:<br/>Map project-filter to frontends<br/>Expand runner-filter"]
        BuildJSON["Build JSON Matrix<br/>[{frontend, runs_on}]"]
        
        SetMatrix --> FilterLogic
        FilterLogic --> BuildJSON
    end
    
    subgraph "Job: basic-test"
        MatrixStrategy["Matrix Strategy<br/>fail-fast: false"]
        ParallelJobs["Parallel Job Instances:<br/>basic-test frontend on runner"]
        
        MatrixStrategy --> ParallelJobs
    end
    
    subgraph "Test Execution"
        Checkout["Checkout repository"]
        FixHome["Fix HOME directory<br/>Container issue workaround"]
        RunTest["python basic_tests/FRONTEND/demo_test.py"]
        
        Checkout --> FixHome
        FixHome --> RunTest
    end
    
    WorkflowDispatch --> DockerImage
    WorkflowCall --> DockerImage
    DockerImage --> SetMatrix
    ProjectFilter --> FilterLogic
    RunnerFilter --> FilterLogic
    
    BuildJSON -->|json_matrix output| MatrixStrategy
    ParallelJobs --> Checkout
```


#### Configuration Propagation


```mermaid
graph TD
    DailyReleaser["daily-releaser.yml"]
    ReleaseWorkflow["release.yml Workflow"]
    UpdateWorkflow["update-releases.yml Workflow"]
    
    subgraph "Configuration Layer"
        SetFactsCall["set-release-facts Action<br/>Called by release.yml"]
        VersionFile[".version File<br/>MAJOR=X<br/>MINOR=Y<br/>VERSION=X.Y.Z"]
        RepoSpecifics["Repository-Specific Logic"]
    end
    
    subgraph "Configuration Outputs"
        VersionOutput["Version Information:<br/>new_version_tag<br/>gh_new_version_tag<br/>build_release_tag"]
        ArtifactOutput["Artifact Patterns:<br/>artifact_download_glob<br/>pip_wheel_names"]
        TestOutput["Test Configuration:<br/>test_demo_filter<br/>test_perf_filter<br/>basic_tests_runner_filter"]
        WorkflowOutput["Workflow Settings:<br/>workflow_allow_failed<br/>skip_docker_build"]
    end
    
    DailyReleaser --> ReleaseWorkflow
    DailyReleaser --> UpdateWorkflow
    
    ReleaseWorkflow --> SetFactsCall
    UpdateWorkflow --> SetFactsCall
    
    SetFactsCall --> VersionFile
    SetFactsCall --> RepoSpecifics
    
    VersionFile --> VersionOutput
    RepoSpecifics --> ArtifactOutput
    RepoSpecifics --> TestOutput
    RepoSpecifics --> WorkflowOutput
```


#### Version Management Workflows


```mermaid
graph TB
    subgraph "Manual Version Operations"
        create_branch["create-version-branches.yml<br/>────────<br/>Creates release-X.Y branch<br/>Triggers release.yml for initial RC"]
        
        bump_version["bump-version.yml<br/>────────<br/>Increments patch version<br/>Updates .version file<br/>Commits to release branch"]
        
        promote_stable["promote-stable.yml<br/>────────<br/>Removes rc suffix<br/>Creates stable release<br/>May trigger version bump"]
    end
    
    subgraph "Version File"
        version_file[".version<br/>────────<br/>MAJOR=0<br/>MINOR=6<br/>PATCH=0<br/>VERSION=$MAJOR.$MINOR.$PATCH"]
    end
    
    create_branch -->|"reads"| version_file
    bump_version -->|"modifies"| version_file
    promote_stable -->|"reads"| version_file
    
    create_branch -->|"invokes"| release_yml["release.yml<br/>type=rc"]
    bump_version -->|"invokes"| release_yml
    promote_stable -->|"invokes"| release_yml2["release.yml<br/>type=stable"]
```


#### Patch Version Flow


```mermaid
graph TB
    StableRelease["Stable release X.Y.0<br/>Published"]
    Commit1["New commit on release-X.Y"]
    UpdateReleases1["update-releases.yml<br/>Detects commit"]
    BumpVersion1["bump-version.yml<br/>X.Y.0 → X.Y.1"]
    Release1["release.yml<br/>type=stable<br/>new_version_tag=X.Y.1"]
    Publish1["Publish X.Y.1<br/>make_latest=true"]
    
    Commit2["Another commit"]
    BumpVersion2["bump-version.yml<br/>X.Y.1 → X.Y.2"]
    Release2["release.yml<br/>new_version_tag=X.Y.2"]
    Publish2["Publish X.Y.2<br/>make_latest=true"]
    
    StableRelease --> Commit1
    Commit1 --> UpdateReleases1
    UpdateReleases1 --> BumpVersion1
    BumpVersion1 --> Release1
    Release1 --> Publish1
    
    Publish1 --> Commit2
    Commit2 --> BumpVersion2
    BumpVersion2 --> Release2
    Release2 --> Publish2
```

The test lifecycle validates that a second commit after stable release bumps the version from `X.Y.0` to `X.Y.1` [.github/workflows/test-rc-stable-release-lifecycle.yml:455-470]().
```


#### Tag Logic Diagram


```mermaid
graph TB
    InputTag["Input: new_version_tag"]
    
    CheckRelType{"release_type?"}
    
    Stable["stable:<br/>new_version_tag unchanged<br/>gh_new_version_tag=new_version_tag<br/>build_release_tag=new_version_tag"]
    
    RC["rc:<br/>new_version_tag unchanged<br/>gh_new_version_tag=new_version_tag<br/>build_release_tag=new_version_tag"]
    
    Nightly["nightly:<br/>new_version_tag=VERSION.devYYYYMMDD<br/>gh_new_version_tag=new_version_tag<br/>build_release_tag=new_version_tag"]
    
    CheckDraftMode{"draft=true?"}
    
    DraftStable["draft + stable:<br/>new_version_tag=draft.repo.X.Y.Z<br/>build_release_tag=X.Y.Z<br/>gh_new_version_tag=draft.repo.X.Y.Z<br/>git_log_fail_on_error=false"]
    
    DraftRC["draft + rc:<br/>new_version_tag unchanged<br/>build_release_tag=X.Y.ZrcN<br/>gh_new_version_tag=draft.repo.X.Y.ZrcN<br/>git_log_fail_on_error=false"]
    
    DraftNightly["draft + nightly:<br/>new_version_tag unchanged<br/>build_release_tag=new_version_tag<br/>gh_new_version_tag=draft.repo.new_version_tag"]
    
    NoDraft["No draft modifications"]
    
    Output["Output:<br/>new_version_tag<br/>gh_new_version_tag<br/>build_release_tag"]
    
    InputTag --> CheckRelType
    
    CheckRelType -->|stable| Stable
    CheckRelType -->|rc| RC
    CheckRelType -->|nightly| Nightly
    
    Stable --> CheckDraftMode
    RC --> CheckDraftMode
    Nightly --> CheckDraftMode
    
    CheckDraftMode -->|true + stable| DraftStable
    CheckDraftMode -->|true + rc| DraftRC
    CheckDraftMode -->|true + nightly| DraftNightly
    CheckDraftMode -->|false| NoDraft
    
    DraftStable --> Output
    DraftRC --> Output
    DraftNightly --> Output
    NoDraft --> Output
```


#### System Architecture and Data Flow


```mermaid
graph TD
    subgraph "Frontend_Layer"
        A["HuggingFace / PyTorch / JAX"] -- "torch.compile(backend='tt')" --> B["TT-XLA (PJRT)"]
        C["ONNX / TensorFlow"] -- "TVM Ingestion" --> D["TT-Forge-ONNX"]
    end

    subgraph "TT-MLIR_Compiler"
        B -- "Produces" --> E["StableHLO Dialect"]
        D -- "Produces" --> F["TTIR Dialect"]
        E -- "Lowering" --> F
        F -- "Optimization Passes (Fusion/Layout)" --> G["TTNN-IR"]
        G -- "Custom Kernels" --> H["TTKernel-IR"]
    end

    subgraph "Hardware_Runtime"
        G -- "Dispatch" --> I["TT-Metalium (TTNN + TTMetal)"]
        H -- "Execution" --> I
        I -- "Firmware/LLK" --> J["Wormhole / Blackhole Card"]
    end

    style A stroke-width:2px
    style J stroke-width:2px
```
Sources: [docs/src/model-bring-up-guide.md:26-65](), [README.md:23-32]()
```


#### System Architecture: Natural Language to Code Entity Space


```mermaid
graph TD
    subgraph "Natural Language Space"
        UserPrompt["User Prompt / Issue / PR Comment"]
        WorkflowDispatch["workflow_dispatch (Prompt Input)"]
    end

    subgraph "Agent Orchestration (GitHub Actions)"
        ClaudeAction["anthropics/claude-code-action@v1"]
        ClaudeYAML[".github/workflows/claude.yml"]
        CallClaudeYAML[".github/workflows/call-claude.yml"]
    end

    subgraph "Code Entity Space"
        CLAUDE_MD["CLAUDE.md (Style & Context)"]
        SkillsDir[".claude/skills/ (Task Blueprints)"]
        ForgeModels["tt-forge-models (Target Repository)"]
        TTXLA["tt-xla (Frontend Environment)"]
    end

    UserPrompt --> ClaudeYAML
    WorkflowDispatch --> CallClaudeYAML
    ClaudeYAML --> ClaudeAction
    CallClaudeClaudeYAML --> ClaudeAction
    
    ClaudeAction -.-> CLAUDE_MD
    ClaudeAction -.-> SkillsDir
    ClaudeAction -->|Executes Bash| TTXLA
    ClaudeAction -->|Writes Code| ForgeModels
```


#### Mapping Natural Language to Code Entities


```mermaid
graph TD
    subgraph "Natural Language Space"
        A["'I want to fuse these ops'"]
        B["'I need to shard this tensor'"]
        C["'Run this on remote hardware'"]
    end

    subgraph "Code Entity Space"
        direction TB
        A -->|Uses| S1["ttl.kernel"]
        A -->|Uses| S2["ttl.compute"]
        B -->|Uses| S3["ttnn.ShardTensorToMesh"]
        B -->|Uses| S4["ttnn.MemoryConfig"]
        C -->|Uses| S5["scripts/run-test.sh"]
        C -->|Uses| S6["scripts/remote-run.sh"]
    end

    subgraph "Implementation Files"
        S1 & S2 -.-> F1["skills/tt-lang/SKILL.md"]
        S3 & S4 -.-> F2["skills/ttnn/SKILL.md"]
        S5 & S6 -.-> F3["skills/tt-connect-remote-device/SKILL.md"]
    end
```


### PipeNet: Inter-Core Communication


```mermaid
graph LR
    subgraph "Core (0, 0)"
        D1["dm_read()"] -- "ttl.copy(blk, pipe)" --> P1["Pipe Source"]
    end

    subgraph "Pipe Infrastructure"
        P1 --> P2["Pipe Destination"]
    end

    subgraph "Core (1, 0)"
        P2 -- "ttl.copy(pipe, blk)" --> D2["dm_read()"]
    end

    PN["ttl.PipeNet"] -. "Manages" .-> P1
    PN -. "Manages" .-> P2
```
Sources: [skills/tt-lang/examples.md:8-10](), [skills/tt-lang/examples.md:28-35]()
```


#### 2.2 Auto-Profiling and Signposts


```mermaid
graph TD
    subgraph "Natural Language Space"
        "Wall Time Ground Truth"
        "Core Utilization"
        "Hotspot Analysis"
    end

    subgraph "Code Entity Space"
        "TT_METAL_DEVICE_PROFILER"["TT_METAL_DEVICE_PROFILER=1"]
        "TT_METAL_DEVICE_PROFILER_NOC_EVENTS"["TT_METAL_DEVICE_PROFILER_NOC_EVENTS=1"]
        "TTLANG_PERF_DUMP"["TTLANG_PERF_DUMP=1"]
        "TTLANG_AUTO_PROFILE"["TTLANG_AUTO_PROFILE=1"]
        "ttl_signpost"["ttl.signpost()"]
        "perf_summary"["ttl._src.perf_summary"]
    end

    "Wall Time Ground Truth" --> "TTLANG_PERF_DUMP"
    "Core Utilization" --> "perf_summary"
    "Hotspot Analysis" --> "TTLANG_AUTO_PROFILE"
    "Hotspot Analysis" --> "ttl_signpost"
    "TT_METAL_DEVICE_PROFILER" --> "TTLANG_PERF_DUMP"
    "TT_METAL_DEVICE_PROFILER_NOC_EVENTS" --> "perf_summary"
```

Sources: [skills/tt-lang-profile-optimize/SKILL.md:21-117](), [skills/tt-lang-profile-optimize/performance-tools.md:1-144]()

---
```


#### Frontend to Hardware Data Flow


```mermaid
graph TD
    subgraph "Frontend Layer"
        A["PyTorch/JAX"] -- "torch_xla / PJRT" --> B["TT-XLA"]
        C["ONNX/TF"] -- "TVM" --> D["TT-Forge-ONNX"]
    end

    subgraph "TT-MLIR Compiler"
        B -- "StableHLO" --> E["TTIR Dialect"]
        D -- "TVM Relay/IR" --> E
        E -- "Optimization Passes" --> F["TTNN Dialect"]
        F -- "Lowering" --> G["TTKernel Dialect"]
    end

    subgraph "Runtime & Hardware"
        F -- "Dispatch" --> H["TT-Metalium (TTNN)"]
        G -- "Custom Kernels" --> I["TT-Metalium (TTMetal)"]
        H --> J["Wormhole/Blackhole Silicon"]
        I --> J
    end

    style B fill:none,stroke-width:2px
    style D fill:none,stroke-width:2px
    style E fill:none,stroke-width:2px
    style H fill:none,stroke-width:2px
```


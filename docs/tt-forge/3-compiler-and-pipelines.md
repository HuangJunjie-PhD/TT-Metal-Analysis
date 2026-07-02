---
project: tt-forge
github: https://github.com/tenstorrent/tt-forge
deepwiki: https://deepwiki.com/tenstorrent/tt-forge/3-compiler-and-pipelines
---

# Benchmarking System

Relevant source files
*   [.github/workflows/basic-tests.yml](https://github.com/tenstorrent/tt-forge/blob/6f2d9645/.github/workflows/basic-tests.yml)
*   [.github/workflows/demo-tests.yml](https://github.com/tenstorrent/tt-forge/blob/6f2d9645/.github/workflows/demo-tests.yml)
*   [.github/workflows/filter-test-matrix.py](https://github.com/tenstorrent/tt-forge/blob/6f2d9645/.github/workflows/filter-test-matrix.py)
*   [.github/workflows/models-matrix.json](https://github.com/tenstorrent/tt-forge/blob/6f2d9645/.github/workflows/models-matrix.json)
*   [demos/tt-forge-onnx/README.md](https://github.com/tenstorrent/tt-forge/blob/6f2d9645/demos/tt-forge-onnx/README.md?plain=1)

The benchmarking system provides comprehensive performance validation for the TT-Forge compiler stack across multiple hardware architectures (Wormhole n150, Blackhole p150) and model types. It validates two primary compilation paths: **torch-xla** benchmarks (page 3.3) test the JAX/PyTorch → XLA → TT-MLIR → TTNN path, while **forge.compile** benchmarks (page 3.4) test the TVM → TT-MLIR → TTNN path.

The system consists of:

*   **Benchmark Infrastructure and Workflows** ([Benchmark Infrastructure and Workflows](https://deepwiki.com/tenstorrent/tt-forge/3.1-benchmark-infrastructure-and-workflows)): `perf-benchmark.yml` orchestration, GitHub runners, and Docker containers.
*   **Test Matrix Configuration** ([Test Matrix Configuration](https://deepwiki.com/tenstorrent/tt-forge/3.2-test-matrix-configuration)): `perf-bench-matrix.json` structure and `filter-test-matrix.py` filtering logic.
*   **torch-xla Backend Benchmarks** ([torch-xla Backend Benchmarks](https://deepwiki.com/tenstorrent/tt-forge/3.3-torch-xla-backend-benchmarks)): ResNet, UNet, ViT, and LLMs using the torch-xla device.
*   **forge.compile Backend Benchmarks** ([forge.compile Backend Benchmarks](https://deepwiki.com/tenstorrent/tt-forge/3.4-forge.compile-backend-benchmarks)): MobileNetV2, Segformer, and UNet using the `forge.compile` API.
*   **Performance Metrics and Reporting** ([Performance Metrics and Reporting](https://deepwiki.com/tenstorrent/tt-forge/3.5-performance-metrics-and-reporting)): FPS measurement, PCC validation, and JSON result structures.

## System Architecture

The benchmarking system is built on a three-tier architecture: workflow orchestration, matrix-based configuration, and dynamic execution.

### Overall Architecture

```mermaid
graph TB
    subgraph "Workflow Orchestration"
        perfBench["perf-benchmark.yml<br/>Main orchestrator"]
        demoTests["demo-tests.yml<br/>Demo validation"]
        basicTests["basic-tests.yml<br/>Smoke tests"]
    end
    
    subgraph "Configuration Layer"
        perfMatrix["perf-bench-matrix.json<br/>Test configurations"]
        modelsMatrix["models-matrix.json<br/>Demo configurations"]
        filterScript["filter-test-matrix.py<br/>Dynamic test selection"]
    end
    
    subgraph "Execution Engine"
        benchmarkPy["benchmark.py<br/>Dynamic module loader"]
        basicDemoTest["basic_tests/.../demo_test.py<br/>Frontend smoke tests"]
        modelModules["Model Benchmark Modules<br/>tt-xla/resnet.py<br/>tt-forge-fe/unet.py"]
    end
    
    subgraph "Hardware Architecture"
        n150["n150 Wormhole Runners<br/>tt-ubuntu-2204-n150-stable"]
        p150["p150 Blackhole Runners<br/>tt-ubuntu-2204-p150-stable"]
    end
    
    subgraph "Reporting"
        slackNotify["Slack Notifications<br/>Failure alerts"]
        produceData["produce_data.yml<br/>Metrics collection"]
    end
    
    perfBench --> perfMatrix
    demoTests --> modelsMatrix
    
    perfMatrix --> filterScript
    modelsMatrix --> filterScript
    
    filterScript --> n150
    filterScript --> p150
    
    n150 --> benchmarkPy
    p150 --> benchmarkPy
    
    benchmarkPy --> modelModules
    basicTests --> basicDemoTest
    
    n150 --> produceData
    p150 --> produceData
```

Sources: [.github/workflows/demo-tests.yml:54-104](), [.github/workflows/basic-tests.yml:58-100](), [.github/workflows/filter-test-matrix.py:10-24]()
```


Sources: [.github/workflows/demo-tests.yml 54-104](https://github.com/tenstorrent/tt-forge/blob/6f2d9645/.github/workflows/demo-tests.yml#L54-L104)[.github/workflows/basic-tests.yml 58-100](https://github.com/tenstorrent/tt-forge/blob/6f2d9645/.github/workflows/basic-tests.yml#L58-L100)[.github/workflows/filter-test-matrix.py 10-24](https://github.com/tenstorrent/tt-forge/blob/6f2d9645/.github/workflows/filter-test-matrix.py#L10-L24)

## Workflow Orchestration

The system uses specialized workflows to handle different validation levels, from quick smoke tests to deep performance benchmarks.

### Workflow Types

| Workflow | Purpose | Key File |
| --- | --- | --- |
| **Basic Tests** | Rapid validation of frontend functionality (smoke tests). | [.github/workflows/basic-tests.yml 1-57](https://github.com/tenstorrent/tt-forge/blob/6f2d9645/.github/workflows/basic-tests.yml#L1-L57) |
| **Demo Tests** | Validates end-to-end model demos across different projects. | [.github/workflows/demo-tests.yml 1-47](https://github.com/tenstorrent/tt-forge/blob/6f2d9645/.github/workflows/demo-tests.yml#L1-L47) |
| **Perf Benchmarks** | Detailed performance measurement (FPS, Latency, PCC). | [.github/workflows/perf-benchmark.yml](https://github.com/tenstorrent/tt-forge/blob/6f2d9645/.github/workflows/perf-benchmark.yml) |

For details, see [Benchmark Infrastructure and Workflows](https://deepwiki.com/tenstorrent/tt-forge/3.1-benchmark-infrastructure-and-workflows).

## Matrix Configuration System

The benchmarking system uses JSON-based matrix files to define test configurations. The `filter-test-matrix.py` script parses these matrices and generates filtered test configurations based on project, test name, and hardware requirements.

### Matrix Filtering Logic

```mermaid
graph LR
    JSON["models-matrix.json"] --> Filter["filter-test-matrix.py"]
    ProjIn["project-filter"] --> Filter
    TestIn["test-filter"] --> Filter
    Filter --> Out["GitHub Actions Matrix"]
```

For details, see [Test Matrix Configuration](#3.2).
```


The script `filter-test-matrix.py` performs the following operations:

1.   **Flattening**: Expands tests with multiple `runs-on` targets into individual matrix entries [[.github/workflows/filter-test-matrix.py 10-24](https://github.com/tenstorrent/tt-forge/blob/6f2d9645/[.github/workflows/filter-test-matrix.py#L10-L24)].
2.   **Filtering**: Matches entries against `project-filter` and `test-filter` inputs [[.github/workflows/filter-test-matrix.py 27-48](https://github.com/tenstorrent/tt-forge/blob/6f2d9645/[.github/workflows/filter-test-matrix.py#L27-L48)].
3.   **Runner Mapping**: Maps logical runner names (e.g., `n150`) to specific CI labels (e.g., `n150-perf`) [[.github/workflows/filter-test-matrix.py 51-60](https://github.com/tenstorrent/tt-forge/blob/6f2d9645/[.github/workflows/filter-test-matrix.py#L51-L60)].

For details, see [Test Matrix Configuration](https://deepwiki.com/tenstorrent/tt-forge/3.2-test-matrix-configuration).

## Backend Benchmarks

The system validates performance across different frontend entry points.

### torch-xla Backend

This backend targets the JAX and PyTorch ecosystem. Benchmarks include:

*   **NLP**: ALBERT, GPT2, OPT, BGE-M3 [[.github/workflows/models-matrix.json 24-32](https://github.com/tenstorrent/tt-forge/blob/6f2d9645/[.github/workflows/models-matrix.json#L24-L32)].
*   **CNN**: ResNet50, ResNet50-HF [[.github/workflows/models-matrix.json 29-30](https://github.com/tenstorrent/tt-forge/blob/6f2d9645/[.github/workflows/models-matrix.json#L29-L30)].

For details, see [torch-xla Backend Benchmarks](https://deepwiki.com/tenstorrent/tt-forge/3.3-torch-xla-backend-benchmarks).

### forge.compile Backend

This backend targets models compiled via the TVM-based frontend. It covers a wide range of computer vision models including AlexNet, DenseNet, and EfficientNet [[demos/tt-forge-onnx/README.md 25-32](https://github.com/tenstorrent/tt-forge/blob/6f2d9645/[demos/tt-forge-onnx/README.md?plain=1#L25-L32)].

For details, see [forge.compile Backend Benchmarks](https://deepwiki.com/tenstorrent/tt-forge/3.4-forge.compile-backend-benchmarks).

## Performance Metrics and Reporting

The system tracks several critical metrics to ensure hardware efficiency and numerical correctness:

*   **FPS (Frames Per Second)**: Throughput measurement.
*   **PCC (Pearson Correlation Coefficient)**: Measures numerical accuracy against a CPU baseline.
*   **CPU FPS**: Baseline performance for comparison.

### Failure Notification

Failures on the `main` branch trigger Slack notifications to the `C088QN7E0R3` channel [[.github/workflows/demo-tests.yml 173-190](https://github.com/tenstorrent/tt-forge/blob/6f2d9645/[.github/workflows/demo-tests.yml#L173-L190)].

For details, see [Performance Metrics and Reporting](https://deepwiki.com/tenstorrent/tt-forge/3.5-performance-metrics-and-reporting).

Sources: [.github/workflows/demo-tests.yml 173-190](https://github.com/tenstorrent/tt-forge/blob/6f2d9645/.github/workflows/demo-tests.yml#L173-L190)[.github/workflows/models-matrix.json 1-45](https://github.com/tenstorrent/tt-forge/blob/6f2d9645/.github/workflows/models-matrix.json#L1-L45)[.github/workflows/filter-test-matrix.py 1-94](https://github.com/tenstorrent/tt-forge/blob/6f2d9645/.github/workflows/filter-test-matrix.py#L1-L94)

Dismiss
Refresh this wiki

Enter email to refresh

## Additional Diagrams


### Compilation Flow


```mermaid
graph TB
    subgraph "Frontend Layer"
        TTXLA["TT-XLA<br/>(PyTorch/JAX)"]
        TTForgeONNX["TT-Forge-ONNX<br/>(ONNX/TF/Paddle)"]
    end
    
    subgraph "TT-MLIR Layer"
        TTIR_Base["TTIR Representation"]
        Passes["Optimization Passes<br/>(Fusion, Sharding, Layout)"]
        Backend_IR["TTNN / TTMetal Dialects"]
    end
    
    subgraph "Runtime Layer"
        Metalium["TT-Metalium"]
        HW["Hardware<br/>(Wormhole/Blackhole)"]
    end
    
    TTXLA -- "StableHLO" --> TTIR_Base
    TTForgeONNX -- "TVM -> TTIR" --> TTIR_Base
    TTIR_Base --> Passes
    Passes --> Backend_IR
    Backend_IR --> Metalium
    Metalium --> HW
```

**Diagram: System-wide Compilation Flow**
```


#### Compiler Configuration Structure


```mermaid
graph TB
    subgraph "CompilerConfig"
        CC["CompilerConfig()"]
        DFOverride["default_df_override<br/>DataFormat.Float16_b<br/>or DataFormat.Float32"]
        MLIRCfg["mlir_config"]
    end
    
    subgraph "MLIRConfig"
        MLIR["MLIRConfig()"]
        Optimizer["set_enable_optimizer()<br/>OPTIMIZER_ENABLED=True"]
        MemLayout["set_enable_memory_layout_analysis()<br/>MEMORY_LAYOUT_ANALYSIS_ENABLED=True"]
        Fusing["set_enable_fusing(True)"]
    end
    
    subgraph "DeviceSettings"
        DS["DeviceSettings()"]
        ProgCache["enable_program_cache<br/>PROGRAM_CACHE_ENABLED=True"]
        ConfigDevices["configure_devices()"]
    end
    
    CC --> DFOverride
    CC --> MLIRCfg
    MLIRCfg --> MLIR
    MLIR --> Optimizer
    MLIR --> MemLayout
    MLIR --> Fusing
    
    DS --> ProgCache
    DS --> ConfigDevices
    
    CC -.->|"Used in"| ForgeCompile["forge.compile()"]
    DS -.->|"Applied after compile"| ConfigDevices
```


#### Input Data Flow


```mermaid
graph TB
    subgraph "Task Type Selection"
        TaskCheck{"task parameter"}
        Classification["task == 'classification'"]
        NA["task == 'na'"]
    end
    
    subgraph "Classification Path"
        LoadDataset["load_benchmark_dataset()<br/>from benchmark/utils.py"]
        DatasetParams["model_version: 'microsoft/resnet-50'<br/>dataset_name: 'imagenet-1k'<br/>split: 'validation'<br/>batch_size, loop_count"]
        Processor["AutoImageProcessor<br/>from HuggingFace"]
        HFDataset["load_dataset()<br/>streaming=True"]
        CreateBatches["create_input_classification()<br/>loop_count batches"]
        ReturnInputs["Return inputs, labels"]
    end
    
    subgraph "Random Data Path"
        ManualSeed["torch.manual_seed(1)"]
        RandomData["torch.randn()<br/>batch_size, channels,<br/>height, width"]
        ReturnRandom["Return inputs only"]
    end
    
    subgraph "Data Format Conversion"
        FormatCheck{"data_format"}
        BFloat16["inputs.to(torch.bfloat16)"]
        Float32["Keep as float32"]
    end
    
    TaskCheck --> Classification
    TaskCheck --> NA
    
    Classification --> LoadDataset
    LoadDataset --> DatasetParams
    DatasetParams --> Processor
    DatasetParams --> HFDataset
    HFDataset --> CreateBatches
    CreateBatches --> ReturnInputs
    
    NA --> ManualSeed
    ManualSeed --> RandomData
    RandomData --> ReturnRandom
    
    ReturnInputs --> FormatCheck
    ReturnRandom --> FormatCheck
    
    FormatCheck --> BFloat16
    FormatCheck --> Float32
```


#### Performance Metrics Collection


```mermaid
graph TB
    subgraph "CPU Baseline Measurement"
        MeasureCPU{"measure_cpu flag"}
        BatchOne["Extract batch_size=1<br/>cpu_input = inputs[0][0]"]
        CPUFunc["measure_cpu_fps()<br/>iterations=512"]
        CPULoop["for i in range(512):<br/>measure single inference"]
        BestTime["best_time = min(times)"]
        CPUFPS["cpu_fps = 1 / best_time"]
        SkipCPU["cpu_fps = -1.0"]
    end
    
    subgraph "Device Performance Measurement"
        Warmup["Warm-up:<br/>verify() or<br/>compiled_model(inputs[0])"]
        StartTime["start = time.time()"]
        DeviceLoop["for i in tqdm(range(loop_count)):<br/>co_out = compiled_model(inputs[i])"]
        EndTime["end = time.time()"]
        CalcTotal["total_time = end - start<br/>total_samples = batch_size * loop_count"]
        CalcSPS["samples_per_sec = total_samples / total_time"]
    end
    
    subgraph "Accuracy Measurement"
        TaskType{"task"}
        EvalClass["evaluate_classification()<br/>predictions, labels"]
        Accuracy["accuracy = 100.0 * correct / total"]
        NoAccuracy["evaluation_score = 0.0"]
    end
    
    MeasureCPU -->|True| BatchOne
    BatchOne --> CPUFunc
    CPUFunc --> CPULoop
    CPULoop --> BestTime
    BestTime --> CPUFPS
    
    MeasureCPU -->|False| SkipCPU
    
    CPUFPS --> Warmup
    SkipCPU --> Warmup
    
    Warmup --> StartTime
    StartTime --> DeviceLoop
    DeviceLoop --> EndTime
    EndTime --> CalcTotal
    CalcTotal --> CalcSPS
    
    CalcSPS --> TaskType
    TaskType -->|classification| EvalClass
    EvalClass --> Accuracy
    TaskType -->|na| NoAccuracy
```


#### Result Dictionary Schema


```mermaid
graph TB
    subgraph "Top-Level Fields"
        Model["model: str<br/>e.g. 'MobileNet V2 Basic'"]
        ModelType["model_type: str<br/>e.g. 'Classification, ImageNet-1K'"]
        RunType["run_type: str<br/>Unique identifier"]
        Config["config: dict"]
        NumLayers["num_layers: int"]
        BatchSize["batch_size: int"]
        Precision["precision: str<br/>e.g. 'bfloat16'"]
        DatasetName["dataset_name: str"]
        ProfileName["profile_name: str"]
        SeqLengths["input_sequence_length: int<br/>output_sequence_length: int"]
        ImageDim["image_dimension: str<br/>e.g. '3x224x224'"]
        PerfAnalysis["perf_analysis: bool"]
        Training["training: bool"]
        Measurements["measurements: List[dict]"]
        DeviceInfo["device_info: dict"]
        DeviceIP["device_ip: Optional[str]"]
    end
    
    subgraph "config dict"
        ModelSize["model_size: str<br/>e.g. 'small'"]
        OptEnabled["optimizer_enabled: bool"]
        ProgCache["program_cache_enabled: bool"]
        MemLayout["memory_layout_analysis_enabled: bool"]
        Trace["trace_enabled: bool"]
    end
    
    subgraph "measurements list"
        Meas1["Measurement 1:<br/>total_samples"]
        Meas2["Measurement 2:<br/>total_time"]
        Meas3["Measurement 3:<br/>evaluation_score"]
        Meas4["Measurement 4:<br/>cpu_fps"]
    end
    
    Config --> ModelSize
    Config --> OptEnabled
    Config --> ProgCache
    Config --> MemLayout
    Config --> Trace
    
    Measurements --> Meas1
    Measurements --> Meas2
    Measurements --> Meas3
    Measurements --> Meas4
    
    DeviceInfo --> DevName["device_name"]
    DeviceInfo --> Galaxy["galaxy"]
    DeviceInfo --> Arch["arch"]
    DeviceInfo --> Chips["chips"]
```


#### Pearson Correlation Coefficient


```mermaid
graph TB
    Inputs["Golden Output<br/>Device Output"]
    Flatten["Flatten tensors<br/>to torch.float32"]
    Center["Center by subtracting mean<br/>golden_centered<br/>device_centered"]
    Compute["PCC = (golden_centered @ device_centered)<br/>÷ (||golden_centered|| × ||device_centered||)"]
    Validate{"PCC >= required_pcc?"}
    Pass["Validation Passed"]
    Fail["AssertionError"]
    
    Inputs --> Flatten
    Flatten --> Center
    Center --> Compute
    Compute --> Validate
    Validate -->|Yes| Pass
    Validate -->|No| Fail
```


#### Benchmark Result Schema


```mermaid
graph TD
    BenchmarkFunc["Benchmark Function<br/>(e.g., test_mobilenetv2_basic)"]
    
    subgraph "Result Components"
        ModelMetadata["Model Metadata<br/>- model: str<br/>- model_type: str<br/>- dataset_name: str<br/>- num_layers: int<br/>- run_type: str"]
        
        ConfigInfo["Config Dict<br/>- model_size: str<br/>- optimizer_enabled: bool<br/>- program_cache_enabled: bool<br/>- memory_layout_analysis_enabled: bool<br/>- trace_enabled: bool"]
        
        InputParams["Input Parameters<br/>- batch_size: int<br/>- precision: str<br/>- image_dimension: str<br/>- input_sequence_length: int<br/>- output_sequence_length: int"]
        
        Measurements["Measurements List<br/>- total_samples<br/>- total_time<br/>- evaluation_score<br/>- cpu_fps<br/>- custom metrics"]
        
        DeviceInfo["Device Info Dict<br/>- device_name: str<br/>- galaxy: bool<br/>- arch: str<br/>- chips: int"]
    end
    
    BenchmarkFunc --> ModelMetadata
    BenchmarkFunc --> ConfigInfo
    BenchmarkFunc --> InputParams
    BenchmarkFunc --> Measurements
    BenchmarkFunc --> DeviceInfo
    
    ModelMetadata --> ResultDict["Complete Result Dictionary"]
    ConfigInfo --> ResultDict
    InputParams --> ResultDict
    Measurements --> ResultDict
    DeviceInfo --> ResultDict
```


#### Metrics Aggregation Pipeline


```mermaid
graph TB
    subgraph "Data Collection Workflow"
        Trigger["workflow_run: completed<br/>(Performance Benchmark)"]
        Collect["Collect CI/CD data action"]
    end
    
    subgraph "Storage"
        SFTP_CICD["SFTP CICD Host"]
        SFTP_PERF["SFTP Perf Host"]
    end
    
    Trigger --> Collect
    Collect --> SFTP_CICD
    Collect --> SFTP_PERF
```

**Implementation Details:**
- **Trigger**: Automatically runs when the "Performance Benchmark External Trigger" workflow completes [.github/workflows/produce_data.yml:28-32]().
- **Action**: Uses `tenstorrent/tt-github-actions/.github/actions/collect_data` [.github/workflows/produce_data.yml:46]().
- **Destinations**: Distinguishes between standard CI/CD data and specialized performance data via separate SFTP host secrets [.github/workflows/produce_data.yml:51-54]().
```


#### Matrix Filtering Flow


```mermaid
graph LR
    subgraph "Input Configuration"
        MatrixFile["Matrix JSON File<br/>─────────────<br/>models-matrix.json"]
        ProjectFilter["Project Filter<br/>─────────────<br/>tt-forge-onnx<br/>tt-torch<br/>tt-xla<br/>tt-forge"]
        TestFilter["Test Filter<br/>─────────────<br/>Optional name substring"]
        ShRunner["Shared Runner Flag<br/>─────────────<br/>--sh-runner"]
    end
    
    subgraph "Processing Functions"
        Flatten["flatten_matrix(data)<br/>─────────────<br/>Merge test-defaults<br/>Expand runs-on arrays<br/>Add project attribute"]
        Filter["filter_matrix(matrix)<br/>─────────────<br/>Filter by project<br/>Filter by name<br/>Return filtered list"]
        UpdateRunner["update_runners(matrix)<br/>─────────────<br/>Map: n150 → n150-perf<br/>Map: p150 → p150b<br/>Store original in runs-on-original"]
    end
    
    subgraph "Output"
        MatrixJSON["JSON Output<br/>─────────────<br/>{matrix: [...], matrix_skip: bool}"]
        GitHubOutput["GITHUB_OUTPUT<br/>─────────────<br/>matrix=$matrix<br/>matrix_skip=$matrix_skip"]
    end
    
    MatrixFile --> Flatten
    ProjectFilter --> Filter
    TestFilter --> Filter
    Flatten --> Filter
    Filter --> UpdateRunner
    ShRunner --> UpdateRunner
    UpdateRunner --> MatrixJSON
    MatrixJSON --> GitHubOutput
```


### Orchestration Architecture


```mermaid
graph TD
    subgraph "Trigger Layer"
        CronTrigger["Cron Schedule<br/>04:00 UTC Daily"]
        ManualTrigger["workflow_dispatch<br/>Manual Execution"]
    end
    
    subgraph "Discovery Phase"
        GetRepos["get-repos Job<br/>Enumerates target repositories"]
    end
    
    subgraph "Parallel Execution Paths"
        NightlyPath["release-nightly Job<br/>Matrix Strategy<br/>One job per repo"]
        RCStablePath["update-releases Job<br/>Conditionally executed<br/>Only when !draft"]
        
        subgraph "Nightly Release Details"
            NightlyMatrix["Matrix includes:<br/>tt-forge-fe<br/>tt-mlir<br/>tt-xla<br/>tt-forge"]
            NightlyRelease["release.yml Workflow<br/>release_type=nightly"]
        end
        
        subgraph "RC/Stable Release Details"
            GetBranches["get-release-branches Action<br/>Finds release-X.Y branches<br/>with new commits"]
            UpdateMatrix["Matrix includes:<br/>branch<br/>release_type<br/>new_version_tag<br/>latest_branch_commit"]
            UpdateRelease["release.yml Workflow<br/>release_type=rc or stable"]
        end
    end
    
    subgraph "Aggregation Layer"
        FailNotify["fail-notify Job<br/>Checks all job statuses"]
        BranchCheck["Branch Check<br/>IS_MAIN flag"]
        StatusCheck["alls-green Action<br/>Aggregate success/failure"]
    end
    
    subgraph "Notification Layer"
        FailSendMsg["fail-send-msg Job<br/>Conditional execution"]
        SlackNotification["Slack Webhook<br/>C088QN7E0R3 channel"]
    end
    
    CronTrigger --> GetRepos
    ManualTrigger --> GetRepos
    
    GetRepos --> NightlyPath
    GetRepos --> RCStablePath
    
    NightlyPath --> NightlyMatrix
    NightlyMatrix --> NightlyRelease
    
    RCStablePath --> GetBranches
    GetBranches --> UpdateMatrix
    UpdateMatrix --> UpdateRelease
    
    NightlyRelease --> FailNotify
    UpdateRelease --> FailNotify
    
    FailNotify --> BranchCheck
    FailNotify --> StatusCheck
    
    BranchCheck --> FailSendMsg
    StatusCheck --> FailSendMsg
    
    FailSendMsg --> SlackNotification
```

**Orchestration Flow:**
1. **Trigger Layer**: Workflow activated via cron schedule or manual dispatch [.github/workflows/daily-releaser.yml:4-9]().
2. **Discovery Phase**: `get-repos` job identifies all repositories to process [.github/workflows/daily-releaser.yml:56-66]().
3. **Parallel Execution**: Two independent paths execute simultaneously:
   - **Nightly Path**: Always runs for all repositories [.github/workflows/daily-releaser.yml:84-98]().
   - **RC/Stable Path**: Conditionally runs (skipped in draft mode) [.github/workflows/daily-releaser.yml:68-82]().
4. **Aggregation**: `fail-notify` job waits for all parallel jobs to complete [.github/workflows/daily-releaser.yml:101-118]().
5. **Notification**: Slack alert sent only for main branch scheduled failures [.github/workflows/daily-releaser.yml:119-136]().
```


#### Failure Aggregation


```mermaid
graph TD
    ReleaseNightly["release-nightly Jobs<br/>Multiple parallel executions"]
    UpdateReleases["update-releases Workflow<br/>Optional execution"]
    
    FailNotify["fail-notify Job<br/>always() condition"]
    
    subgraph "Status Checks"
        BranchCheck["Branch Check Step<br/>IS_MAIN output<br/>refs/heads/main"]
        AllsGreen["alls-green Action<br/>re-actors/alls-green@release/v1<br/>Inputs: toJSON(needs)"]
    end
    
    subgraph "Outputs"
        IsMainOutput["is-main: boolean"]
        FailedOutput["failed: boolean"]
    end
    
    ReleaseNightly --> FailNotify
    UpdateReleases --> FailNotify
    
    FailNotify --> BranchCheck
    FailNotify --> AllsGreen
    
    BranchCheck --> IsMainOutput
    AllsGreen --> FailedOutput
```

**Execution Characteristics:**
- **Trigger Condition:** `if: always()` - Runs regardless of job failures [.github/workflows/daily-releaser.yml:102]().
- **Dependency Analysis:** Waits for `release-nightly` [.github/workflows/daily-releaser.yml:101]().
- **Status Detection:** Uses `re-actors/alls-green@release/v1` action to check job outcomes [.github/workflows/daily-releaser.yml:115-117]().

**Branch Detection Logic:**
```bash
IS_MAIN=$(if [ '${{ github.ref }}' == 'refs/heads/main' ]; then echo true; else echo false; fi)
```
[.github/workflows/daily-releaser.yml:108-111]()
```


### Build Workflow Selection


```mermaid
graph LR
    subgraph "Repository Type"
        ForgeFE["tt-forge-fe"]
        MLIR["tt-mlir"]
        XLA["tt-xla"]
        Forge["tt-forge"]
    end
    
    subgraph "Nightly Build Workflow"
        OnPushFE["On push workflow<br/>Triggered by main push"]
        ScheduleNightly["schedule-nightly.yml<br/>Scheduled build"]
        OnPushXLA["On push workflow<br/>Triggered by main push"]
        DailyReleaser["Daily Releaser<br/>Orchestrator workflow"]
    end
    
    subgraph "Workflow Behavior"
        MainBranch["Searches main branch<br/>for workflow runs"]
        AllowFailed["workflow_allow_failed=true<br/>Continues if workflow failed"]
        CommitLookup["Artifact lookup by commit SHA<br/>uplift_artifacts_by_commit=true"]
    end
    
    ForgeFE --> OnPushFE
    MLIR --> ScheduleNightly
    XLA --> OnPushXLA
    Forge --> DailyReleaser
    
    OnPushFE --> MainBranch
    ScheduleNightly --> MainBranch
    OnPushXLA --> MainBranch
    DailyReleaser --> MainBranch
    
    MainBranch --> AllowFailed
    OnPushXLA --> CommitLookup
```


#### Logical Decision Flow


```mermaid
graph TB
    Start["Input Parameters"]
    
    subgraph "Stage 1: Universal Defaults"
        LoadVersion["Load .version file<br/>Lines 140"]
        SetDefaults["Set common defaults<br/>Lines 142-195<br/>────────────<br/>prerelease=true<br/>draft=input.draft<br/>make_latest=false<br/>workflow_allow_failed=false<br/>test_perf=false<br/>python_version=3.11"]
    end
    
    subgraph "Stage 2: Release Type Overrides"
        CheckType{"release_type?<br/>Lines 202-209"}
        Stable["stable:<br/>prerelease=false<br/>make_latest=true"]
        Nightly["nightly:<br/>new_version_tag=X.Y.Z.devYYYYMMDD<br/>workflow_allow_failed=true<br/>build_release_find_workflow_branch=main"]
        RC["rc:<br/>Use defaults"]
    end
    
    subgraph "Stage 3: Repository-Specific"
        CheckRepo{"repo?<br/>Lines 217-248"}
        TTForgeFE["tt-forge-fe:<br/>pip_wheel_names=tt_forge_fe tt_tvm<br/>artifact_download_glob=*wheel*test-reports*"]
        TTMLIR["tt-mlir:<br/>pip_wheel_names=ttmlir<br/>skip_model_compatible_table=true<br/>skip_docker_build=true"]
        TTXLA["tt-xla:<br/>pip_wheel_names=pjrt-plugin-tt<br/>test_perf=true<br/>uplift_artifacts_by_commit=true"]
        TTForge["tt-forge:<br/>ignore_artifacts=true<br/>pip_wheel_deps_names=pjrt-plugin-tt"]
    end
    
    subgraph "Stage 4: Testing Overrides"
        CheckDraft{"draft=true?<br/>Lines 256-295"}
        DraftOverrides["Draft mode overrides:<br/>test_demo_filter=bge_m3<br/>test_perf_filter=resnet<br/>test_demo_wait=true<br/>basic_tests_runner_filter=n150<br/>gh_new_version_tag=draft.repo.tag"]
        NoDraft["No overrides"]
    end
    
    Output["Write 36 outputs to<br/>GITHUB_OUTPUT<br/>Lines 335-370"]
    
    Start --> LoadVersion
    LoadVersion --> SetDefaults
    SetDefaults --> CheckType
    CheckType -->|stable| Stable
    CheckType -->|nightly| Nightly
    CheckType -->|rc| RC
    Stable --> CheckRepo
    Nightly --> CheckRepo
    RC --> CheckRepo
    
    CheckRepo -->|tt-forge-fe| TTForgeFE
    CheckRepo -->|tt-mlir| TTMLIR
    CheckRepo -->|tt-xla| TTXLA
    CheckRepo -->|tt-forge| TTForge
    
    TTForgeFE --> CheckDraft
    TTMLIR --> CheckDraft
    TTXLA --> CheckDraft
    TTForge --> CheckDraft
    
    CheckDraft -->|true| DraftOverrides
    CheckDraft -->|false| NoDraft
    
    DraftOverrides --> Output
    NoDraft --> Output
```


#### The Compilation Pipeline


```mermaid
graph LR
    subgraph "Frontend Space"
        PT["PyTorch Model"]
        JAX["JAX Model"]
        ONNX["ONNX/TF"]
    end

    subgraph "Code Entities (Compiler)"
        TTXLA["TT-XLA (PJRT)"]
        TTFORGE["TT-Forge-ONNX"]
        TTMLIR["TT-MLIR"]
    end

    subgraph "IR Space"
        SHLO["StableHLO"]
        TTIR["TTIR Dialect"]
        TTNN["TTNN-IR"]
    end

    PT --> TTXLA
    JAX --> TTXLA
    ONNX --> TTFORGE
    TTXLA --> SHLO
    SHLO --> TTMLIR
    TTFORGE --> TTIR
    TTMLIR --> TTIR
    TTIR --> TTNN
```


### Test Matrix Configuration System


```mermaid
graph LR
    subgraph "Matrix Definition Space"
        MatrixJSON["models-matrix.json"]
        ProjectEntry["Project Entry<br/>e.g., 'tt-xla'"]
        TestEntry["Test Entry<br/>e.g., 'resnet50'"]
    end
    
    subgraph "Code Execution Space"
        FilterPy["filter-test-matrix.py"]
        DemoTestJob["demo-test (GHA Job)"]
        RunStep["run: python $demo_file"]
    end
    
    MatrixJSON --> ProjectEntry
    ProjectEntry --> TestEntry
    TestEntry -.->|Parsed by| FilterPy
    FilterPy -->|Generates| DemoTestJob
    DemoTestJob -->|Executes| RunStep
```

**Matrix Structure**

The `models-matrix.json` file contains an array of project objects. Each project defines its tests and optional default settings like `runs-on` [.github/workflows/models-matrix.json:1-45]().

| Field | Description |
|-------|-------------|
| `project` | The frontend/project name (e.g., `tt-forge-onnx`, `tt-torch`, `tt-xla`) |
| `test-defaults` | Default settings applied to all tests in the project (e.g., `runs-on: ["n150", "p150"]`) |
| `tests` | Array of test objects containing `name`, `path`, and optional overrides |

**Example Matrix Entry (tt-torch):**
```json
{
  "project": "tt-torch",
  "tests": [
    { 
      "runs-on": "n150", 
      "name": "resnet50", 
      "path": "resnet50_demo.py", 
      "pyreq": "loguru requests transformers datasets==3.6.0 torch==2.7.0 torchvision pytest tabulate" 
    }
  ]
}
```
Sources: [.github/workflows/models-matrix.json:36-44]()
```


#### Matrix Definition Structure


```mermaid
graph LR
    MatrixFile["perf-bench-matrix.json<br/>───────────<br/>Projects array<br/>test-defaults object<br/>tests array"]
    
    FilterScript["filter-test-matrix.py<br/>───────────<br/>flatten_matrix()<br/>filter_matrix()<br/>update_runners()"]
    
    FlatMatrix["Flattened Matrix<br/>───────────<br/>Expanded runner entries<br/>Merged defaults<br/>Project metadata"]
    
    FilteredMatrix["Filtered Matrix<br/>───────────<br/>Project-specific<br/>Test-specific<br/>Runner-mapped"]
    
    MatrixFile --> FilterScript
    FilterScript --> FlatMatrix
    FlatMatrix --> FilteredMatrix
```


#### Matrix Flattening Process


```mermaid
graph TD
    Input["Input Matrix Entry<br/>───────────<br/>project: tt-forge-fe<br/>test-defaults: {runs-on: [n150, p150]}<br/>tests: [{name: basic-test}]"]
    
    MergeDefaults["Merge Defaults<br/>───────────<br/>merged_test = {**test_defaults, **test}"]
    
    CheckRunners{"runs-on<br/>is list?"}
    
    ExpandArray["Expand Array<br/>───────────<br/>matrix.extend({**merged_test, 'runs-on': runner})"]
    
    SingleEntry["Single Entry<br/>───────────<br/>matrix.append(merged_test)"]
    
    Output1["Output Entry 1<br/>───────────<br/>project: tt-forge-fe<br/>name: basic-test<br/>runs-on: n150"]
    
    Output2["Output Entry 2<br/>───────────<br/>project: tt-forge-fe<br/>name: basic-test<br/>runs-on: p150"]
    
    Input --> MergeDefaults
    MergeDefaults --> CheckRunners
    CheckRunners -->|Yes| ExpandArray
    CheckRunners -->|No| SingleEntry
    ExpandArray --> Output1
    ExpandArray --> Output2
```


#### Project Filtering Logic


```mermaid
graph TD
    Item["Matrix Item<br/>───────────<br/>project: ?<br/>name: ?"]
    
    ProjectCheck{"project_filter<br/>== 'tt-forge'?"}
    
    XLACheck{"item.project<br/>in ['tt-xla']?"}
    
    Exclude1["Exclude<br/>───────────<br/>tt-forge filter only<br/>includes tt-xla tests"]
    
    ProjectMatch{"item.project<br/>== project_filter?"}
    
    NameCheck{"name_filter<br/>provided?"}
    
    NameMatch{"filter in<br/>item.name?"}
    
    Include["Include in<br/>Filtered Matrix"]
    
    Exclude2["Exclude from<br/>Filtered Matrix"]
    
    Item --> ProjectCheck
    ProjectCheck -->|Yes| XLACheck
    ProjectCheck -->|No| ProjectMatch
    
    XLACheck -->|No| Exclude1
    XLACheck -->|Yes| NameCheck
    
    ProjectMatch -->|No| Exclude2
    ProjectMatch -->|Yes| NameCheck
    
    NameCheck -->|Yes| NameMatch
    NameCheck -->|No| Include
    
    NameMatch -->|Yes| Include
    NameMatch -->|No| Exclude2
```


### Data Flow and Reporting


```mermaid
graph LR
    PROMPT["Prompt (manual-ai-install.yml)"] -- "inputs.prompt" --> CLAUDE_ACTION["claude-code-action"]
    CLAUDE_ACTION -- "Bash/Read/Write" --> WORKSPACE["/github/workspace/"]
    WORKSPACE -- "tt-forge-ai-report.md" --> UPLOAD["actions/upload-artifact"]
    WORKSPACE -- "cat report" --> SUMMARY["GITHUB_STEP_SUMMARY"]
    
    subgraph "Entities in Code"
        PROMPT
        CLAUDE_ACTION
        WORKSPACE
    end
```


#### 5.2 Reporting Requirements


```mermaid
graph LR
    subgraph "Natural Language Space"
        "Reproducer Script"
        "Actual vs Expected"
        "Compiler Flags"
    end

    subgraph "Code Entity Space"
        "gh_issue_create"["gh issue create"]
        "ttlang_initial_mlir"["/tmp/ttlang_initial.mlir"]
        "ttlang_final_mlir"["/tmp/ttlang_final.mlir"]
        "ttlang_test_output"["/tmp/ttlang_test_output.log"]
    end

    "Reproducer Script" --> "gh_issue_create"
    "Actual vs Expected" --> "ttlang_test_output"
    "Compiler Flags" --> "ttlang_initial_mlir"
    "Compiler Flags" --> "ttlang_final_mlir"
```

Sources: [skills/tt-bug-report/SKILL.md:1-68]()
43:T1f36,
```


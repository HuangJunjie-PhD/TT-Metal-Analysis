---
project: tt-forge-onnx
github: https://github.com/tenstorrent/tt-forge-onnx
deepwiki: https://deepwiki.com/tenstorrent/tt-forge-onnx/3-core-compilation-system)
---

# Core Compilation System

Relevant source files
*   [forge/csrc/forge_bindings.cpp](https://github.com/tenstorrent/tt-forge-onnx/blob/e519819c/forge/csrc/forge_bindings.cpp)
*   [forge/csrc/passes/lower_to_mlir.cpp](https://github.com/tenstorrent/tt-forge-onnx/blob/e519819c/forge/csrc/passes/lower_to_mlir.cpp)
*   [forge/csrc/passes/mlir_compiler.cpp](https://github.com/tenstorrent/tt-forge-onnx/blob/e519819c/forge/csrc/passes/mlir_compiler.cpp)
*   [forge/csrc/passes/mlir_compiler.hpp](https://github.com/tenstorrent/tt-forge-onnx/blob/e519819c/forge/csrc/passes/mlir_compiler.hpp)
*   [forge/csrc/passes/mlir_passes.cpp](https://github.com/tenstorrent/tt-forge-onnx/blob/e519819c/forge/csrc/passes/mlir_passes.cpp)
*   [forge/csrc/passes/mlir_passes.hpp](https://github.com/tenstorrent/tt-forge-onnx/blob/e519819c/forge/csrc/passes/mlir_passes.hpp)
*   [forge/forge/compile.py](https://github.com/tenstorrent/tt-forge-onnx/blob/e519819c/forge/forge/compile.py)
*   [forge/forge/compiled_graph_state.py](https://github.com/tenstorrent/tt-forge-onnx/blob/e519819c/forge/forge/compiled_graph_state.py)
*   [forge/forge/forge_property_utils.py](https://github.com/tenstorrent/tt-forge-onnx/blob/e519819c/forge/forge/forge_property_utils.py)
*   [forge/forge/module.py](https://github.com/tenstorrent/tt-forge-onnx/blob/e519819c/forge/forge/module.py)
*   [forge/forge/python_codegen.py](https://github.com/tenstorrent/tt-forge-onnx/blob/e519819c/forge/forge/python_codegen.py)
*   [forge/forge/tensor.py](https://github.com/tenstorrent/tt-forge-onnx/blob/e519819c/forge/forge/tensor.py)
*   [forge/forge/tvm_calls/forge_compile.py](https://github.com/tenstorrent/tt-forge-onnx/blob/e519819c/forge/forge/tvm_calls/forge_compile.py)
*   [forge/forge/tvm_calls/forge_utils.py](https://github.com/tenstorrent/tt-forge-onnx/blob/e519819c/forge/forge/tvm_calls/forge_utils.py)
*   [forge/forge/tvm_utils.py](https://github.com/tenstorrent/tt-forge-onnx/blob/e519819c/forge/forge/tvm_utils.py)
*   [forge/forge/verify/config.py](https://github.com/tenstorrent/tt-forge-onnx/blob/e519819c/forge/forge/verify/config.py)
*   [forge/forge/verify/verify.py](https://github.com/tenstorrent/tt-forge-onnx/blob/e519819c/forge/forge/verify/verify.py)

The Core Compilation System is the multi-stage pipeline that transforms framework models (PyTorch, ONNX, TensorFlow, JAX, PaddlePaddle) into optimized binaries for Tenstorrent hardware. This document describes the compilation flow, major components, and key interfaces for extending and debugging the system.

For information about individual operations and their implementations, see [Operation System](https://deepwiki.com/tenstorrent/tt-forge-onnx/5-operation-system). For testing and validation workflows, see [Testing Infrastructure](https://deepwiki.com/tenstorrent/tt-forge-onnx/6-testing-infrastructure). For supported models and their configurations, see [Model Support](https://deepwiki.com/tenstorrent/tt-forge-onnx/7-model-support).

* * *

## System Overview

The compilation system consists of five major stages that progressively lower and optimize model representations:

**Compilation Flow Architecture**

Each stage transforms the model representation while preserving numerical accuracy (validated through verification points):

| Stage | Input | Output | Primary Purpose |
| --- | --- | --- | --- |
| **Frontend** | Framework model | TVM Relay IR | Unify multiple frameworks |
| **IR Translation** | Relay IR | Python/Forge ops | Convert to executable form |
| **Graph Optimization** | Forge graph | Optimized Forge graph | High-level optimizations |
| **MLIR Backend** | Forge graph | TTNN binary | Device-specific lowering |
| **Runtime** | Binary + State | Device execution | Execute on hardware |

Sources: [forge/forge/compile.py 262-341](https://github.com/tenstorrent/tt-forge-onnx/blob/e519819c/forge/forge/compile.py#L262-L341)[forge/csrc/passes/mlir_compiler.cpp 70-223](https://github.com/tenstorrent/tt-forge-onnx/blob/e519819c/forge/csrc/passes/mlir_compiler.cpp#L70-L223)

* * *

## Entry Points and Main Flow

### Primary Compilation Interface

The main entry point for compilation is `forge.compile()` which wraps `compile_main()`:

**Main Compilation Entry Points**

The `CompileContext` dataclass maintains all state during compilation:

`@dataclassclass CompileContext:    modules: List[Module]    graph_name: str    compiler_cfg: CompilerConfig    verify_cfg: DeprecatedVerifyConfig    inputs: List[torch.Tensor]    optimizer: Optional[Union[torch.optim.Optimizer, forge.optimizers.Optimizer]]    training: bool    graph: Optional[Graph]    # ... additional state fields    forge_module: Optional[ForgeGraphModule]    compiled_binary: Optional[Binary]`
Sources: [forge/forge/compile.py 182-260](https://github.com/tenstorrent/tt-forge-onnx/blob/e519819c/forge/forge/compile.py#L182-L260)[forge/forge/compile.py 112-148](https://github.com/tenstorrent/tt-forge-onnx/blob/e519819c/forge/forge/compile.py#L112-L148)[forge/forge/compile.py 262-341](https://github.com/tenstorrent/tt-forge-onnx/blob/e519819c/forge/forge/compile.py#L262-L341)

* * *

## Stage 1: Frontend and TVM Relay IR

### Framework to Relay IR Conversion

The frontend stage converts models from multiple frameworks into a unified TVM Relay IR representation:

**Framework Frontend Converters**

The `compile_tvm_graph()` function orchestrates framework-specific conversions:

`def compile_tvm_graph(inputs, module, compiler_cfg, graph_name,                       input_names=[], path=None, verify_cfg=None, framework=None):    if framework == "pytorch":        json_graphs, inputs = compile_pytorch_for_forge(...)    elif framework == "tensorflow":        tf_inputs = to_tf_tensors(inputs)        json_graphs, inputs = compile_tf_for_forge(...)    elif framework == "onnx":        onnx_inputs = [x.detach().numpy() for x in inputs]        json_graphs, _ = compile_onnx_for_forge(...)    # ... other frameworks        return json_graphs, inputs`
**Key Classes:**

*   **`relay.IRModule`** - Container for TVM Relay IR functions
*   **`relay.Function`** - Represents a callable computational graph
*   **`DFPatternCallback`** - Pattern matching for graph transformations

Sources: [forge/forge/tvm_calls/forge_compile.py 137-249](https://github.com/tenstorrent/tt-forge-onnx/blob/e519819c/forge/forge/tvm_calls/forge_compile.py#L137-L249)[forge/forge/tvm_calls/forge_compile.py 79-135](https://github.com/tenstorrent/tt-forge-onnx/blob/e519819c/forge/forge/tvm_calls/forge_compile.py#L79-L135)

### Graph Partitioning

The partitioning logic identifies operations supported on Tenstorrent devices versus CPU fallback:

**Graph Partitioning Flow**

The partitioning creates up to three function regions:

*   **CPU Pre-function**: Operations before device execution
*   **Device Function**: Main computation on Tenstorrent hardware
*   **CPU Post-function**: Operations after device execution

Sources: [forge/forge/tvm_calls/forge_compile.py 268-408](https://github.com/tenstorrent/tt-forge-onnx/blob/e519819c/forge/forge/tvm_calls/forge_compile.py#L268-L408)

* * *

## Stage 2: IR Translation to Python

### TVM to Python Code Generation

The IR translation stage converts TVM Relay JSON graphs into executable Python code using Forge operations:

**Python Code Generation Flow**

The `ForgeWriter` class generates executable Python modules:

`class ForgeWriter(PythonWriter):    def write_class_definition(self, params, constants, class_name=None):        # Generate ForgeModule subclass        self.wl(f"class {class_name}(ForgeModule):")        self.indent += 1                # Add parameters        for param in params.values():            name, shape, requires_grad, dtype = param            self.wl(f'self.add_parameter("{name}", forge.Parameter(*{shape}, ...))')                # Add constants        for const in constants.values():            self.wl(f'self.add_constant("{name}", shape={shape}, dtype={dtype})')        def write_forward(self, ops, inputs, outputs):        # Generate forward method with Forge operations        for key in sorted(ops):            op = ops[key]            self.wl(f'{op.output_name} = {op.function_name}(...)')`
**Attribute Mapping:**

TVM operation attributes are mapped to Forge operation parameters through attribute remapping tables defined in the generated code.

Sources: [forge/forge/python_codegen.py 119-289](https://github.com/tenstorrent/tt-forge-onnx/blob/e519819c/forge/forge/python_codegen.py#L119-L289)[forge/forge/tvm_calls/forge_compile.py 268-408](https://github.com/tenstorrent/tt-forge-onnx/blob/e519819c/forge/forge/tvm_calls/forge_compile.py#L268-L408)

* * *

## Stage 3: Graph Optimization

### Forge Graph Passes

The graph optimization stage applies multiple passes to transform and optimize the Forge graph:

**Graph Optimization Pipeline**

**Key Pass Functions:**

| Function | Purpose | File |
| --- | --- | --- |
| `run_post_initial_graph_passes()` | Shape/type inference, initial transforms | `forge/_C` binding |
| `run_consteval_graph_pass()` | Evaluate constants at compile time | `forge/csrc/passes/consteval.cpp` |
| `run_optimization_graph_passes()` | High-level optimizations | `forge/_C` binding |
| `run_autograd_pass()` | Generate backward pass for training | `forge/_C.autograd` |
| `run_pre_lowering_passes()` | Prepare for MLIR lowering | `forge/_C` binding |

**Graph Representation:**

The `Graph` class from `forge._C.graph` represents the computation graph with nodes and edges:

`class Graph:    def get_ordered_input_names() -> List[str]    def get_ordered_output_names() -> List[str]    def get_constant_nodes() -> List[Node]    def get_parameter_nodes() -> List[Node]    # ... additional graph query methods`
Sources: [forge/forge/compile.py 278-327](https://github.com/tenstorrent/tt-forge-onnx/blob/e519819c/forge/forge/compile.py#L278-L327)[forge/csrc/forge_bindings.cpp 208-229](https://github.com/tenstorrent/tt-forge-onnx/blob/e519819c/forge/csrc/forge_bindings.cpp#L208-L229)

* * *

## Stage 4: MLIR Backend (Critical Component)

### MLIR Generation and Lowering

The MLIR backend is the most critical component (importance: 443.92) that lowers Forge graphs to device-executable code:

**MLIR Generation Architecture**

### Key Classes and Data Structures

**`MLIRGenerator`** ([forge/csrc/passes/lower_to_mlir.cpp 178-840](https://github.com/tenstorrent/tt-forge-onnx/blob/e519819c/forge/csrc/passes/lower_to_mlir.cpp#L178-L840)):

The core class responsible for MLIR emission:

`class MLIRGenerator {private:    mlir::ModuleOp graphModule_;  // Output MLIR module    mlir::OpBuilder builder_;      // MLIR operation builder        // Symbol table: node name → (mlir::Value, graphlib::Node*)    std::map<std::string, std::pair<mlir::Value, graphlib::Node*>> symbolTable_;        // Operation lowering handlers    std::map<std::string, HandlerType> lowering_handler_map;        // Attribute conversion    static AttributeMapper attr_mapper_; public:    mlir::ModuleOp emit_mlir(tt::ForgeGraphModule& module);    private:    mlir::func::FuncOp emit_mlir_function(tt::graphlib::Graph* graph, std::string fn_name);    mlir::Value emit_mlir_tt_forge_operation(tt::graphlib::Graph* graph, tt::graphlib::OpNode* op_node);        template<typename TTIROp>    mlir::Value emit_mlir_ttforge_op(tt::graphlib::Graph* graph, tt::graphlib::OpNode* op_node);};`
**Lowering Handler Map:**

Operations are mapped to TTIR dialect operations through a handler map initialized in `init_lowering_handler_map()`:

`lowering_handler_map["abs"] = &MLIRGenerator::emit_mlir_ttforge_op<mlir::tt::ttir::AbsOp>;lowering_handler_map["add"] = &MLIRGenerator::emit_mlir_ttforge_op<mlir::tt::ttir::AddOp>;lowering_handler_map["matmul"] = &MLIRGenerator::emit_mlir_ttforge_op<mlir::tt::ttir::MatmulOp>;lowering_handler_map["conv2d"] = &MLIRGenerator::emit_mlir_ttforge_op<mlir::tt::ttir::Conv2dOp>;// ... 80+ operation mappings`
Sources: [forge/csrc/passes/lower_to_mlir.cpp 178-260](https://github.com/tenstorrent/tt-forge-onnx/blob/e519819c/forge/csrc/passes/lower_to_mlir.cpp#L178-L260)[forge/csrc/passes/lower_to_mlir.cpp 783-840](https://github.com/tenstorrent/tt-forge-onnx/blob/e519819c/forge/csrc/passes/lower_to_mlir.cpp#L783-L840)

### Attribute Mapping

The `AttributeMapper` handles attribute name and type conversions between Forge and MLIR:

**Attribute Mapping Flow**

Example mappings from [forge/csrc/passes/lower_to_mlir.cpp 116-176](https://github.com/tenstorrent/tt-forge-onnx/blob/e519819c/forge/csrc/passes/lower_to_mlir.cpp#L116-L176):

`// softmax: dim → dimensionadd_op_mapping("softmax", "dim", AttributeRemap("dimension")); // index: start → begin, stop → end, stride → stepadd_op_mapping("index", "start", AttributeRemap("begin", TargetType::I32Attr));add_op_mapping("index", "stop", AttributeRemap("end", TargetType::I32Attr));add_op_mapping("index", "stride", AttributeRemap("step", TargetType::I32Attr)); // conv2d: array attributesadd_op_mapping("conv2d", "stride", AttributeRemap(std::nullopt, TargetType::DenseI32ArrayAttr));add_op_mapping("conv2d", "padding", AttributeRemap(std::nullopt, TargetType::DenseI32ArrayAttr));`
Sources: [forge/csrc/passes/lower_to_mlir.cpp 84-176](https://github.com/tenstorrent/tt-forge-onnx/blob/e519819c/forge/csrc/passes/lower_to_mlir.cpp#L84-L176)

### MLIR Pass Pipeline

After MLIR module generation, a series of passes optimize and lower to the TTNN dialect:

**MLIR Pass Pipeline Architecture**

**`MLIRConfig`** controls pass behavior ([forge/csrc/passes/mlir_compiler.hpp 26-76](https://github.com/tenstorrent/tt-forge-onnx/blob/e519819c/forge/csrc/passes/mlir_compiler.hpp#L26-L76)):

`struct MLIRConfig {    std::optional<bool> enable_consteval;    std::optional<bool> enable_optimizer;    std::optional<bool> enable_memory_layout_analysis;    std::optional<bool> enable_fusing;    std::optional<bool> enable_fusing_conv2d_with_multiply_pattern;    std::string custom_config;        MLIRConfig& set_enable_consteval(bool enable);    MLIRConfig& set_enable_optimizer(bool enable);    // ... configuration methods};`
**Pipeline Execution:**

The pipeline is registered and executed through MLIR's pass infrastructure:

`void run_mlir_passes(mlir::OwningOpRef<mlir::ModuleOp>& mlir_module,                      const std::optional<MLIRConfig>& mlir_config) {    register_mlir_passes();  // Register TTIR and TTNN passes        mlir::PassManager pm(mlir_module.get()->getName());        // Lookup and configure pipeline    const auto pipelineInfo = mlir::PassPipelineInfo::lookup("ttir-to-ttnn-backend-pipeline");        std::string options = config_to_pipeline_options(mlir_config);    pipelineInfo->addToPipeline(pm, options, err_handler);        // Execute pipeline    pm.run(mlir_module.get());}`
Sources: [forge/csrc/passes/mlir_passes.cpp 80-152](https://github.com/tenstorrent/tt-forge-onnx/blob/e519819c/forge/csrc/passes/mlir_passes.cpp#L80-L152)[forge/csrc/passes/mlir_compiler.cpp 70-223](https://github.com/tenstorrent/tt-forge-onnx/blob/e519819c/forge/csrc/passes/mlir_compiler.cpp#L70-L223)

### Output Generation

The final stage generates executable binaries in multiple formats:

**MLIR Output Generation**

**API Functions:**

`// Generate flatbuffer binary (default)runtime::Binary run_mlir_compiler(    tt::ForgeGraphModule& module,     const std::optional<MLIRConfig>& mlir_config = std::nullopt); // Generate C++ source codestd::string run_mlir_compiler_to_cpp(    tt::ForgeGraphModule& module,    const std::optional<MLIRConfig>& mlir_config = std::nullopt); // Generate shared object (.so)std::string run_mlir_compiler_to_shared_object(    tt::ForgeGraphModule& module,    const std::optional<MLIRConfig>& mlir_config = std::nullopt);`
Sources: [forge/csrc/passes/mlir_compiler.cpp 225-239](https://github.com/tenstorrent/tt-forge-onnx/blob/e519819c/forge/csrc/passes/mlir_compiler.cpp#L225-L239)[forge/csrc/forge_bindings.cpp 218-228](https://github.com/tenstorrent/tt-forge-onnx/blob/e519819c/forge/csrc/forge_bindings.cpp#L218-L228)

* * *

## Stage 5: Runtime Execution

### Compiled Model Structure

The `CompiledModel` class provides the runtime execution interface:

**Compiled Model Architecture**

**Key Components:**

`class CompiledModel:    # Runtime state    runtime_model_state: ModelState    tensor_pool: TensorPool    compiled_binary: Binary        # Graph states for each program    fwd_compiled_graph_state: CompiledGraphState    bwd_compiled_graph_state: Optional[CompiledGraphState]    opt_compiled_graph_state: Optional[CompiledGraphState]        # Execution interface    inputs: List[CTensor]    outputs: Dict[str, CTensor]        # Framework and Forge representations    framework_module: AnyModule    forge_graph_module: ForgeGraphModule        def __call__(self, *args) -> List[torch.Tensor]:        """Execute forward pass"""        def backward(self) -> List[torch.Tensor]:        """Execute backward pass (training mode)"""        def update_parameters(self) -> List[torch.Tensor]:        """Execute optimizer step (training mode)"""`
Sources: [forge/forge/compiled_graph_state.py 165-221](https://github.com/tenstorrent/tt-forge-onnx/blob/e519819c/forge/forge/compiled_graph_state.py#L165-L221)

### Execution Flow

**Runtime Execution Sequence**

**CompiledGraphState:**

Maintains metadata for each compiled graph (forward/backward/optimizer):

`@dataclassclass CompiledGraphState:    graph: Graph    ordered_input_names: List[str]    ordered_output_names: List[str]    ordered_constant_node_names: List[str]    ordered_parameter_node_names: List[str]        # Constant evaluation results    consteval_trace: Dict[str, Any]    post_const_eval_constants: Dict[str, torch.Tensor]    post_const_eval_parameters: Dict[str, torch.Tensor]        # Output aliasing for memory optimization    aliased_outputs: Dict[str, str]        @staticmethod    def from_compiled_graph(module: Module, graph: Graph,                            optimizer_params: Optional[Dict[str, Tensor]]) -> CompiledGraphState`
Sources: [forge/forge/compiled_graph_state.py 46-140](https://github.com/tenstorrent/tt-forge-onnx/blob/e519819c/forge/forge/compiled_graph_state.py#L46-L140)[forge/forge/compiled_graph_state.py 224-362](https://github.com/tenstorrent/tt-forge-onnx/blob/e519819c/forge/forge/compiled_graph_state.py#L224-L362)

* * *

## Verification System

### Multi-Stage Verification

The verification system validates numerical accuracy at multiple compilation stages:

**Verification Architecture**

### Verification Configuration

**`VerifyConfig`** ([forge/forge/verify/config.py 90-152](https://github.com/tenstorrent/tt-forge-onnx/blob/e519819c/forge/forge/verify/config.py#L90-L152)):

`@dataclassclass VerifyConfig:    enabled: bool = True    verify_size: bool = True      # Check output count matches    verify_dtype: bool = True      # Check data types match    verify_shape: bool = True      # Check tensor shapes match    verify_values: bool = True     # Numerical comparison        # Value comparison configuration    value_checker: ValueChecker = AutomaticValueChecker()        # Framework and compiled model types    supported_tensor_types: tuple = (TensorFromPytorch, torch.Tensor, tf.Tensor, ...)    framework_model_types: tuple = (torch.nn.Module, ForgeModule, ...)    compiled_model_types: tuple = (CompiledModel,)`
**Deprecated `DeprecatedVerifyConfig`** for legacy graph-level verification:

`@dataclassclass DeprecatedVerifyConfig:    enabled: bool = True    intermediates: bool = True      # Verify intermediate tensors    rtol: Dict[Any, Optional[float]]  # Relative tolerance per data format    atol: Dict[Any, Optional[float]]  # Absolute tolerance per data format    pcc: Optional[float] = None     # Pearson Correlation threshold        verify_all: bool = False        # Verify after every stage    verify_last: bool = True        # Verify after final stage    stages_for_intermediate_verification: Set[CompileDepth]`
### Verification Functions

**Main Verification API** ([forge/forge/verify/verify.py 420-560](https://github.com/tenstorrent/tt-forge-onnx/blob/e519819c/forge/forge/verify/verify.py#L420-L560)):

`def verify(    inputs: List[FrameworkTensor],    framework_model: FrameworkModule,    compiled_model: CompiledModel,    verify_cfg: VerifyConfig = None,    with_backward: bool = False) -> tuple:    """    Performs verification by comparing framework and compiled model outputs.        Returns:        tuple: (framework_outputs, compiled_outputs)    """    # Execute framework model    fw_out = framework_model(*inputs)        # Execute compiled model    record_execution(ExecutionStage.FAILED_TTNN_BINARY_EXECUTION)    co_out = compiled_model(*inputs)    record_execution(ExecutionStage.FAILED_VERIFICATION)        # Perform checks    if verify_cfg.verify_size:        assert len(fw_out) == len(co_out)        for fw, co in zip(fw_out, co_out):        if verify_cfg.verify_dtype:            check_dtypes(fw.dtype, co.dtype)        if verify_cfg.verify_shape:            assert fw.shape == co.shape        if verify_cfg.verify_values:            verify_cfg.value_checker.check(fw, co)        # Backward verification for training    if with_backward:        _verify_backward(inputs, grad, fw_out[0], co_out[0], ...)        record_execution(ExecutionStage.PASSED)    return fw_out, co_out`
**Legacy Graph Verification** ([forge/forge/verify/verify.py 120-253](https://github.com/tenstorrent/tt-forge-onnx/blob/e519819c/forge/forge/verify/verify.py#L120-L253)):

`def do_verify(    stage_name: str,    graph: pygraph.Graph,    inputs: Tuple[Tensor, ...],    parameters: Dict[str, torch.Tensor],    golden_input_grads: Tuple[torch.Tensor, ...],    outputs: Tuple[Tensor, ...],    intermediate_golden_tensors: Dict,    verify_cfg: DeprecatedVerifyConfig,    losses=None,    targets: List[Tensor] = [],    optimizer=None):    """    Verify graph vs. pytorch golden at specific compilation stage.    """    trace_outputs, parameter_to_gradients, ... = pygraph.eval(        graph, inputs, parameters, verify_cfg.relative_atol,         verify_cfg.pcc, intermediate_golden_tensors, ...    )        # Compare outputs    for i, (golden, evaled) in enumerate(zip(outputs, trace_outputs)):        compare_tensor_to_golden(f"output {i}", golden.value(), evaled, verify_cfg)        # Compare gradients (training mode)    if training:        for param_name, param_tensor in parameters.items():            golden = param_tensor.grad            evaled = parameter_to_gradients[param_name]            compare_tensor_to_golden(f"gradient of {param_name}", golden, evaled, verify_cfg)`
Sources: [forge/forge/verify/verify.py 420-560](https://github.com/tenstorrent/tt-forge-onnx/blob/e519819c/forge/forge/verify/verify.py#L420-L560)[forge/forge/verify/verify.py 120-253](https://github.com/tenstorrent/tt-forge-onnx/blob/e519819c/forge/forge/verify/verify.py#L120-L253)

* * *

## Property and Metadata Management

### Execution Stage Tracking

The `ForgePropertyHandler` tracks execution progress and metadata throughout compilation:

**Property Management Architecture**

### Execution Stages

**`ExecutionStage`** enum ([forge/forge/forge_property_utils.py 279-308](https://github.com/tenstorrent/tt-forge-onnx/blob/e519819c/forge/forge/forge_property_utils.py#L279-L308)):

`class ExecutionStage(Enum):    FAILED_BEFORE_FORGE_COMPILATION_INITIATION = auto()    FAILED_TVM_RELAY_IRMODULE_GENERATION = auto()    FAILED_TVM_RELAY_IO_FLATTENING = auto()    FAILED_TVM_RELAY_IR_TRANSFORMATION = auto()    FAILED_TVM_PATTERN_CALLBACKS = auto()    FAILED_TVM_GRAPH_PARTITIONING = auto()    FAILED_FORGE_MODULE_GENERATION = auto()    FAILED_FORGE_INITIAL_GRAPH_PASS = auto()    FAILED_FORGE_POST_INITIAL_GRAPH_PASS = auto()    FAILED_FORGE_CONSTEVAL = auto()    FAILED_FORGE_OPTIMIZATION_GRAPH_PASS = auto()    FAILED_FORGE_POST_OPTIMIZATION_DECOMP = auto()    FAILED_FORGE_AUTOGRAD_PASS = auto()    FAILED_FORGE_POST_AUTOGRAD_DECOMP = auto()    FAILED_FORGE_PRE_LOWERING = auto()    FAILED_FORGE_GRAPH_SPLIT = auto()    FAILED_FORGE_MLIR_COMPILATION = auto()    FAILED_TTNN_BINARY_EXECUTION = auto()    FAILED_VERIFICATION = auto()    PASSED = auto()`
Stages map to execution depth for categorization:

`class ExecutionDepth(Enum):    CI_FAILURE = auto()    FAILED_FE_COMPILATION = auto()      # Frontend stages    FAILED_TTMLIR_COMPILATION = auto()  # MLIR stages    FAILED_RUNTIME = auto()             # Device execution    INCORRECT_RESULT = auto()           # Verification failure    PASSED = auto()        @staticmethod    def from_exec_stage(exec_stage: ExecutionStage):        # Maps ExecutionStage to ExecutionDepth`
### Property Recording

**API Functions** ([forge/forge/forge_property_utils.py 561-830](https://github.com/tenstorrent/tt-forge-onnx/blob/e519819c/forge/forge/forge_property_utils.py#L561-L830)):

`class ForgePropertyHandler:    def __init__(self, store: Optional[ForgePropertyStore] = None):        self.store = store if store else ForgePropertyStore()        self.record_execution(ExecutionStage.FAILED_BEFORE_FORGE_COMPILATION_INITIATION)        def add(self, key: str, value: Any):        """Add property using dot-separated key (e.g., 'tags.model_name')"""        def record_execution(self, execution_stage: ExecutionStage):        """Record current execution stage and depth"""        self.record_execution_stage(execution_stage)        self.record_execution_depth(ExecutionDepth.from_exec_stage(execution_stage))        def record_compiler_config(self, compiler_cfg: CompilerConfig):        """Record compiler configuration"""        def record_verify_config(self, verify_cfg: VerifyConfig):        """Record verification configuration"""        def clean_store(self) -> dict:        """Return cleaned dictionary removing empty values"""`
**Usage Pattern:**

`# Initialize property handlerrecord_execution(ExecutionStage.FAILED_TVM_RELAY_IRMODULE_GENERATION) # ... compilation progresses ... record_execution(ExecutionStage.FAILED_FORGE_MLIR_COMPILATION) # ... MLIR compilation ... record_execution(ExecutionStage.FAILED_TTNN_BINARY_EXECUTION) # ... device execution ... record_execution(ExecutionStage.PASSED)`
Sources: [forge/forge/forge_property_utils.py 279-348](https://github.com/tenstorrent/tt-forge-onnx/blob/e519819c/forge/forge/forge_property_utils.py#L279-L348)[forge/forge/forge_property_utils.py 561-830](https://github.com/tenstorrent/tt-forge-onnx/blob/e519819c/forge/forge/forge_property_utils.py#L561-L830)

### Flatbuffer Binary Metadata

After binary generation, the `FlatbufferDetailsExtractor` extracts tensor and program information:

`class FlatbufferDetailsExtractor:    def __init__(self, binary_json: Dict[str, Any]):        self.binary = binary_json        def extract_tensor_details(self, inputs_or_outputs) -> List[TensorDesc]:        """Extract tensor shape, dtype, buffer_type, layout from descriptors"""        tensor_desc_list = []        for io in inputs_or_outputs:            desc = io["desc"]            tensor_desc = TensorDesc(                shape=desc["shape"],                data_type=desc["layout"]["memory_desc"]["data_type"],                buffer_type=desc["layout"]["memory_desc"]["buffer_type"],                layout=desc["layout"]["memory_desc"]["tensor_memory_layout"],                grid_shape=[size["x"], size["y"]]            )            tensor_desc_list.append(tensor_desc)        return tensor_desc_list        def extract_program_io_details(self, program_filter: Optional[List[str]] = None):        """Extract input/output configurations for each program"""        program_inputs = {}        program_outputs = {}                for program in self.binary["programs"]:            if program_filter and program["name"] not in program_filter:                continue            program_inputs[program["name"]] = self.extract_tensor_details(program["inputs"])            program_outputs[program["name"]] = self.extract_tensor_details(program["outputs"])                return program_inputs, program_outputs`
Sources: [forge/forge/forge_property_utils.py 360-453](https://github.com/tenstorrent/tt-forge-onnx/blob/e519819c/forge/forge/forge_property_utils.py#L360-L453)

* * *

## Configuration and Control

### Compiler Configuration

The `CompilerConfig` class controls compilation behavior:

`class CompilerConfig:    # Compilation depth control    compile_depth: CompileDepth = CompileDepth.FULL        # MLIR configuration    mlir_config: Optional[MLIRConfig] = None        # Data format overrides    default_df_override: Optional[DataFormat] = None        # Optimization flags    enable_consteval: bool = True    enable_optimizer: bool = True    enable_fusing: bool = True        # TVM graph serialization    tvm_graph_store_path: str = ""    tvm_graph_load_path: str = ""`
**Compilation Depth:**

Controls early stopping at specific stages:

`class CompileDepth(Enum):    INIT_COMPILE = auto()    GENERATE_INITIAL_GRAPH = auto()    POST_INITIAL_GRAPH_PASS = auto()    CONSTEVAL_GRAPH = auto()    POST_PATTERN_MATCHER = auto()    OPTIMIZED_GRAPH = auto()    AUTOGRAD = auto()    POST_AUTOGRAD_PASS = auto()    PRE_LOWERING_PASS = auto()    SPLIT_GRAPH = auto()    RUN_MLIR_COMPILER = auto()    FINISH_COMPILE = auto()    FULL = auto()  # Complete compilation`
### MLIR Configuration

**Python Interface:**

`mlir_config = forge._C.MLIRConfig()mlir_config.set_enable_consteval(True)mlir_config.set_enable_optimizer(True)mlir_config.set_enable_memory_layout_analysis(True)mlir_config.set_enable_fusing(False)mlir_config.set_custom_config("memory-layout-analysis-enabled=true") # Use in compilationcompiled_model = forge.compile(    model,     sample_inputs,    compiler_cfg=CompilerConfig(mlir_config=mlir_config))`
**C++ Bindings** ([forge/csrc/forge_bindings.cpp 172-206](https://github.com/tenstorrent/tt-forge-onnx/blob/e519819c/forge/csrc/forge_bindings.cpp#L172-L206)):

`py::class_<tt::passes::MLIRConfig>(m, "MLIRConfig")    .def(py::init<>())    .def("set_enable_consteval", ...)    .def("set_enable_optimizer", ...)    .def("set_enable_memory_layout_analysis", ...)    .def("set_enable_fusing", ...)    .def("set_enable_fusing_conv2d_with_multiply_pattern", ...)    .def("set_custom_config", ...)    .def("to_json", ...)    .def("from_json", ...);`
Sources: [forge/csrc/forge_bindings.cpp 172-206](https://github.com/tenstorrent/tt-forge-onnx/blob/e519819c/forge/csrc/forge_bindings.cpp#L172-L206)[forge/csrc/passes/mlir_compiler.hpp 26-76](https://github.com/tenstorrent/tt-forge-onnx/blob/e519819c/forge/csrc/passes/mlir_compiler.hpp#L26-L76)

* * *

## Error Handling and Debugging

### Compilation Failure Points

Each stage records its execution state before attempting operations:

`def forge_compile_from_context(context: CompileContext) -> CompiledModel:    stage_to_func = {        CompileDepth.INIT_COMPILE: init_compile,        CompileDepth.GENERATE_INITIAL_GRAPH: generate_initial_graph,        # ... stage mappings        CompileDepth.RUN_MLIR_COMPILER: run_mlir_compiler,    }        while context.stage != CompileDepth.FULL:        current_stage = context.stage                # Execute stage        next_stage = stage_to_func<FileRef file-url="https://github.com/tenstorrent/tt-forge-onnx/blob/e519819c/current_stage" undefined  file-path="current_stage">Hii</FileRef>                # Early stop if configured        if check_for_compilation_early_stop(compiler_cfg.compile_depth, current_stage):            return generate_compile_results(...)                context.stage = next_stage`
### Debug Output

**Graph Dumping:**

`# Dump graph at specific stageforge._C.dump_graph(graph, test_name="debug_test", graph_name="forward_graph") # Dumps to: reportify/<test_name>/<graph_name>.json`
**MLIR Module Inspection:**

`// In lower_to_mlir.cpp, MLIR modules are logged and savedlog_trace(LogMLIRCompiler, "TTIR module after lowering ForgeGraphModule:\n{}", moduleStr); // Save to filereportify::dump_mlir("ttir", module_name, mlir_module.getOperation());`
**Environment Variables:**

| Variable | Effect |
| --- | --- |
| `FORGE_CONTINUE_ON_MISMATCH` | Continue compilation on verification failure |
| `FORGE_DUMP_CONFIG` | Dump compiler config to YAML |
| `FORGE_GENERATE_OVERRIDE_CONFIG` | Generate override config from balancer |
| `FORGE_VERIFY_GOLDEN` | Run backend golden verification |

Sources: [forge/forge/compile.py 262-341](https://github.com/tenstorrent/tt-forge-onnx/blob/e519819c/forge/forge/compile.py#L262-L341)[forge/csrc/passes/lower_to_mlir.cpp 230-256](https://github.com/tenstorrent/tt-forge-onnx/blob/e519819c/forge/csrc/passes/lower_to_mlir.cpp#L230-L256)

* * *

## Summary

The Core Compilation System transforms framework models into executable Tenstorrent binaries through five stages:

1.   **Frontend**: Unify multiple frameworks into TVM Relay IR
2.   **IR Translation**: Convert Relay IR to Python/Forge operations
3.   **Graph Optimization**: Apply high-level transformations and constant folding
4.   **MLIR Backend**: Lower to TTIR/TTNN dialects and generate device binary (most critical)
5.   **Runtime**: Execute on hardware through `CompiledModel` interface

Key architectural components:

*   **`MLIRGenerator`**: Core MLIR generation class with 80+ operation handlers
*   **`CompiledModel`**: Runtime execution wrapper with forward/backward/optimizer support
*   **`ForgePropertyHandler`**: Metadata tracking through compilation
*   **Verification**: Multi-stage numerical validation with PCC/ATOL/RTOL checks

For extending the system:

*   Add operations: Implement handler in `MLIRGenerator::lowering_handler_map`
*   Add passes: Register with MLIR pass infrastructure
*   Debug: Use graph dumping, MLIR inspection, and property tracking

Sources: [forge/forge/compile.py 182-341](https://github.com/tenstorrent/tt-forge-onnx/blob/e519819c/forge/forge/compile.py#L182-L341)[forge/csrc/passes/lower_to_mlir.cpp 178-840](https://github.com/tenstorrent/tt-forge-onnx/blob/e519819c/forge/csrc/passes/lower_to_mlir.cpp#L178-L840)[forge/csrc/passes/mlir_compiler.cpp 70-223](https://github.com/tenstorrent/tt-forge-onnx/blob/e519819c/forge/csrc/passes/mlir_compiler.cpp#L70-L223)[forge/forge/compiled_graph_state.py 165-362](https://github.com/tenstorrent/tt-forge-onnx/blob/e519819c/forge/forge/compiled_graph_state.py#L165-L362)[forge/forge/verify/verify.py 420-560](https://github.com/tenstorrent/tt-forge-onnx/blob/e519819c/forge/forge/verify/verify.py#L420-L560)[forge/forge/forge_property_utils.py 279-830](https://github.com/tenstorrent/tt-forge-onnx/blob/e519819c/forge/forge/forge_property_utils.py#L279-L830)

This wiki is featured in the [repository](https://github.com/tenstorrent/tt-forge-onnx/blob/main/README.md)

Dismiss
Refresh this wiki

Enter email to refresh
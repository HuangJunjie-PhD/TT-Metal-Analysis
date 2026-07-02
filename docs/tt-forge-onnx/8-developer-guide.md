---
project: tt-forge-onnx
github: https://github.com/tenstorrent/tt-forge-onnx
deepwiki: https://deepwiki.com/tenstorrent/tt-forge-onnx/8-developer-guide
---

# Developer Guide

Relevant source files
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
*   [forge/test/operators/pytorch/conftest.py](https://github.com/tenstorrent/tt-forge-onnx/blob/e519819c/forge/test/operators/pytorch/conftest.py)
*   [forge/test/operators/pytorch/eltwise_nary/test_concatenate.py](https://github.com/tenstorrent/tt-forge-onnx/blob/e519819c/forge/test/operators/pytorch/eltwise_nary/test_concatenate.py)
*   [forge/test/operators/pytorch/matmul/__init__.py](https://github.com/tenstorrent/tt-forge-onnx/blob/e519819c/forge/test/operators/pytorch/matmul/__init__.py)
*   [forge/test/operators/pytorch/test_all.py](https://github.com/tenstorrent/tt-forge-onnx/blob/e519819c/forge/test/operators/pytorch/test_all.py)
*   [forge/test/operators/utils/__init__.py](https://github.com/tenstorrent/tt-forge-onnx/blob/e519819c/forge/test/operators/utils/__init__.py)
*   [forge/test/operators/utils/compat.py](https://github.com/tenstorrent/tt-forge-onnx/blob/e519819c/forge/test/operators/utils/compat.py)
*   [forge/test/operators/utils/failing_reasons_validation.py](https://github.com/tenstorrent/tt-forge-onnx/blob/e519819c/forge/test/operators/utils/failing_reasons_validation.py)
*   [forge/test/operators/utils/features.py](https://github.com/tenstorrent/tt-forge-onnx/blob/e519819c/forge/test/operators/utils/features.py)
*   [forge/test/operators/utils/logger_utils.py](https://github.com/tenstorrent/tt-forge-onnx/blob/e519819c/forge/test/operators/utils/logger_utils.py)
*   [forge/test/operators/utils/plan.py](https://github.com/tenstorrent/tt-forge-onnx/blob/e519819c/forge/test/operators/utils/plan.py)
*   [forge/test/operators/utils/pytest.py](https://github.com/tenstorrent/tt-forge-onnx/blob/e519819c/forge/test/operators/utils/pytest.py)
*   [forge/test/operators/utils/test_data.py](https://github.com/tenstorrent/tt-forge-onnx/blob/e519819c/forge/test/operators/utils/test_data.py)
*   [forge/test/operators/utils/utils.py](https://github.com/tenstorrent/tt-forge-onnx/blob/e519819c/forge/test/operators/utils/utils.py)

This page provides practical guidance for developers working with the tt-forge-onnx codebase, including how to run tests, configure compilation, and extend the system with new operators. For information about the overall system architecture, see [Overview](https://deepwiki.com/tenstorrent/tt-forge-onnx/1-overview). For details about specific subsystems, see [Core Compilation System](https://deepwiki.com/tenstorrent/tt-forge-onnx/2-getting-started), [Framework Integration](https://deepwiki.com/tenstorrent/tt-forge-onnx/3-core-compilation-system), [Operation System](https://deepwiki.com/tenstorrent/tt-forge-onnx/4-framework-integration), [Testing Infrastructure](https://deepwiki.com/tenstorrent/tt-forge-onnx/5-operation-system), and [Model Support](https://deepwiki.com/tenstorrent/tt-forge-onnx/6-testing-infrastructure).

* * *

## Running Tests

The codebase provides comprehensive testing infrastructure for operator-level and model-level verification. Tests are managed using pytest with a sophisticated query system for filtering and parameterization.

### Test Execution Commands

Tests are executed using pytest with environment variable-based filtering. Load the helper commands:

`source forge/test/operators/pytorch/test_commands.sh`
This provides commands including `pytest`, `with-params`, `export_tests`, and `print_query_docs`.

**Sources:**[forge/test/operators/pytorch/test_commands.sh 1-184](https://github.com/tenstorrent/tt-forge-onnx/blob/e519819c/forge/test/operators/pytorch/test_commands.sh#L1-L184)

### Environment Variables for Test Filtering

The test framework supports extensive filtering through environment variables:

| Variable | Description | Example |
| --- | --- | --- |
| `OPERATORS` | Comma-separated list of operators to test | `add,div,matmul` |
| `FILTERS` | Lambda filters from `VectorLambdas` or `QueryLambdas` | `QUICK,HAS_DATA_FORMAT` |
| `INPUT_SOURCES` | Input source types | `FROM_HOST,FROM_DRAM_QUEUE` |
| `INPUT_SHAPES` | Python expression for shapes | `[(1,2,3,4),(2,3,4)]` |
| `DEV_DATA_FORMATS` | Data formats to test | `Float16_b,Int8` |
| `MATH_FIDELITIES` | Math fidelity levels | `HiFi4,HiFi3` |
| `KWARGS` | Operator-specific parameters | `[{'dim':0},{'dim':1}]` |
| `TEST_ID` | Single test ID to run | `add-FROM_HOST-None-(1,2,3,4)-Float16_b-HiFi4` |
| `ID_FILES` | Files containing test IDs | `ids/push.txt` |
| `ID_FILES_IGNORE` | Blacklist files | `ids/blacklist.txt` |
| `SAMPLE` | Percentage of tests to sample | `10` (for 10%) |
| `RANGE` | Index range to run | `0,100` |

**Sources:**[forge/test/operators/pytorch/test_all.py 60-141](https://github.com/tenstorrent/tt-forge-onnx/blob/e519819c/forge/test/operators/pytorch/test_all.py#L60-L141)[forge/test/operators/pytorch/test_commands.sh 28-48](https://github.com/tenstorrent/tt-forge-onnx/blob/e519819c/forge/test/operators/pytorch/test_commands.sh#L28-L48)

### Test Query Architecture

The test framework uses a declarative approach where `TestPlan` objects define test collections and failing rules. The `TestQuery` class provides fluent API for filtering:

`query = TestQuery(test_vectors)query = query.filter(VectorLambdas.QUICK)query = query.filter(lambda tv: tv.operator in ['add', 'mul'])query = query.sample(10, random_seed=42)params = query.to_params()`
**Sources:**[forge/test/operators/utils/plan.py 137-333](https://github.com/tenstorrent/tt-forge-onnx/blob/e519819c/forge/test/operators/utils/plan.py#L137-L333)[forge/test/operators/pytorch/test_all.py 258-366](https://github.com/tenstorrent/tt-forge-onnx/blob/e519819c/forge/test/operators/pytorch/test_all.py#L258-L366)

### Test Execution Flow

Test IDs uniquely identify test vectors with format: `{operator}-{input_source}-{kwargs}-{input_shape}-{dev_data_format}-{math_fidelity}`. Example: `add-FROM_HOST-None-(1,2,3,4)-Float16_b-HiFi4`

**Sources:**[forge/test/operators/utils/plan.py 457-489](https://github.com/tenstorrent/tt-forge-onnx/blob/e519819c/forge/test/operators/utils/plan.py#L457-L489)[forge/test/operators/pytorch/test_query.py 1-19](https://github.com/tenstorrent/tt-forge-onnx/blob/e519819c/forge/test/operators/pytorch/test_query.py#L1-L19)

### CI/CD Integration

The CI/CD system splits tests into groups for parallel execution. Test collection uses pytest-split with `--splitting-algorithm least_duration` based on historical test durations in `.test_durations`.

**Sources:**[.github/workflows/test-sub.yml 1-327](https://github.com/tenstorrent/tt-forge-onnx/blob/e519819c/.github/workflows/test-sub.yml#L1-L327)[.github/workflows/on-pr.yml 1-149](https://github.com/tenstorrent/tt-forge-onnx/blob/e519819c/.github/workflows/on-pr.yml#L1-L149)

### Test Runner Implementation

The custom test runner at `.github/workflows/test_runner.py` manages test execution with crash recovery:

`# test_runner.py executes tests from .pytest_tests_to_runwith open(".pytest_tests_to_run", "r") as fd:    test_list = [line.strip() for line in fd.readlines()]sys.exit(pytest.main(test_list + sys.argv[1:]))`
The runner supports `--continue-after-crash` flag to restart tests after crashes, tracking crashed tests in `crashed_pytest.log`.

**Sources:**[.github/workflows/test-sub.yml 253-288](https://github.com/tenstorrent/tt-forge-onnx/blob/e519819c/.github/workflows/test-sub.yml#L253-L288)[.github/workflows/run_tests.py 1-12](https://github.com/tenstorrent/tt-forge-onnx/blob/e519819c/.github/workflows/run_tests.py#L1-L12)

### Example Test Usage

`# Run all tests for specific operatorsexport OPERATORS=add,divwith-params pytest # Run quick subset with specific data formatsexport OPERATORS=matmulexport FILTERS=QUICK,HAS_DATA_FORMATexport DEV_DATA_FORMATS=Float16_b,Int8with-params pytest # Run single test by IDTEST_ID='add-FROM_HOST-None-(1,2,3,4)-Float16_b-HiFi4' pytest # Export test IDs to fileOPERATORS=add,mul FILTERS=UNIQUE_KWARGS export_tests my_tests.json # Collect without runningOPERATORS=add pytest --collect-only # Run with sampling for quick validationexport OPERATORS=add,mul,divexport SAMPLE=10  # Run 10% of testswith-params pytest # Run specific index rangeexport OPERATORS=matmulexport RANGE=0,50  # Run first 50 testswith-params pytest # Exclude operatorsexport OPERATORS=-conv2d,-relu  # Run all except conv2d and reluwith-params pytest`
**Sources:**[docs/src/test.md 103-150](https://github.com/tenstorrent/tt-forge-onnx/blob/e519819c/docs/src/test.md?plain=1#L103-L150)[forge/test/operators/pytorch/test_commands.sh 1-184](https://github.com/tenstorrent/tt-forge-onnx/blob/e519819c/forge/test/operators/pytorch/test_commands.sh#L1-L184)[forge/test/operators/pytorch/test_all.py 60-141](https://github.com/tenstorrent/tt-forge-onnx/blob/e519819c/forge/test/operators/pytorch/test_all.py#L60-L141)

### Interpreting Test Results

The test framework automatically tracks execution metadata using the `ForgePropertyHandler` system. This information is recorded in JUnit XML reports and can be used for debugging.

#### Property Tracking System

The property system records execution progress through stages:

| Stage | Description |
| --- | --- |
| `FAILED_BEFORE_FORGE_COMPILATION_INITIATION` | Error before compilation starts |
| `FAILED_TVM_RELAY_IRMODULE_GENERATION` | TVM frontend conversion failed |
| `FAILED_FORGE_MODULE_GENERATION` | Failed to create Forge module |
| `FAILED_FORGE_MLIR_COMPILATION` | MLIR compilation failed |
| `FAILED_TTNN_BINARY_EXECUTION` | Runtime execution failed |
| `FAILED_VERIFICATION` | Output verification failed |
| `PASSED` | Test passed all stages |

**Sources:**[forge/forge/forge_property_utils.py 1-1203](https://github.com/tenstorrent/tt-forge-onnx/blob/e519819c/forge/forge/forge_property_utils.py#L1-L1203)[forge/test/operators/pytorch/conftest.py 1-121](https://github.com/tenstorrent/tt-forge-onnx/blob/e519819c/forge/test/operators/pytorch/conftest.py#L1-L121)[forge/test/operators/utils/logger_utils.py 1-41](https://github.com/tenstorrent/tt-forge-onnx/blob/e519819c/forge/test/operators/utils/logger_utils.py#L1-L41)

#### Debugging Compilation Failures

When a test fails, check the execution stage to understand where the failure occurred:

`# Example: Reading test properties from pytest output# JUnit XML contains properties like:# <property name="tags" value='{"execution_stage": "FAILED_FORGE_MLIR_COMPILATION"}'/> # In test code, properties are recorded automatically:from forge.forge_property_utils import record_execution, ExecutionStage # Record compilation progressrecord_execution(ExecutionStage.FAILED_TVM_RELAY_IRMODULE_GENERATION)record_execution(ExecutionStage.FAILED_FORGE_MLIR_COMPILATION)record_execution(ExecutionStage.PASSED)`
Use `FORGE_DUMP_CONFIG=1` to save compiler configuration for reproduction:

`FORGE_DUMP_CONFIG=1 pytest test_name.py  # Creates {graph_name}_config.yamlFORGE_LOAD_CONFIG=graph_config.yaml pytest test_name.py  # Load saved config`
**Sources:**[forge/forge/compile.py 57-82](https://github.com/tenstorrent/tt-forge-onnx/blob/e519819c/forge/forge/compile.py#L57-L82)[forge/forge/forge_property_utils.py 561-833](https://github.com/tenstorrent/tt-forge-onnx/blob/e519819c/forge/forge/forge_property_utils.py#L561-L833)

* * *

## Compilation Configuration

The compilation system provides multiple configuration layers to control compilation behavior, MLIR optimization passes, and verification.

### CompilerConfig

The `CompilerConfig` class controls high-level compilation settings:

**Sources:**[forge/test/operators/utils/utils.py 110-128](https://github.com/tenstorrent/tt-forge-onnx/blob/e519819c/forge/test/operators/utils/utils.py#L110-L128)[forge/test/operators/utils/compat.py 289-303](https://github.com/tenstorrent/tt-forge-onnx/blob/e519819c/forge/test/operators/utils/compat.py#L289-L303)

### MLIRConfig

The `MLIRConfig` structure configures MLIR compilation passes:

`struct MLIRConfig {    std::optional<bool> enable_consteval = std::nullopt;    std::optional<bool> enable_optimizer = std::nullopt;    std::optional<bool> enable_memory_layout_analysis = std::nullopt;    std::optional<bool> enable_fusing = std::nullopt;    std::optional<bool> enable_fusing_conv2d_with_multiply_pattern = std::nullopt;    std::string custom_config = "";        MLIRConfig& set_enable_consteval(bool enable);    MLIRConfig& set_enable_optimizer(bool enable);    MLIRConfig& set_enable_memory_layout_analysis(bool enable);    MLIRConfig& set_enable_fusing(bool enable);    MLIRConfig& set_custom_config(const std::string& config);};`
The configuration is converted to MLIR pass pipeline options in `config_to_pipeline_options()`:

`// mlir_passes.cpp:43-78std::stringstream options{""};if (mlir_config->enable_consteval.has_value()) {    options << " enable-const-eval=" << *mlir_config->enable_consteval;}if (mlir_config->enable_optimizer.has_value()) {    options << " enable-optimizer=" << *mlir_config->enable_optimizer;}// ... more optionsoptions << " " << mlir_config->custom_config;`
**Sources:**[forge/csrc/passes/mlir_compiler.hpp 26-76](https://github.com/tenstorrent/tt-forge-onnx/blob/e519819c/forge/csrc/passes/mlir_compiler.hpp#L26-L76)[forge/csrc/passes/mlir_passes.cpp 43-78](https://github.com/tenstorrent/tt-forge-onnx/blob/e519819c/forge/csrc/passes/mlir_passes.cpp#L43-L78)

### Python Bindings for Configuration

The Python bindings expose configuration through `forge._C.MLIRConfig`:

`mlir_config = forge._C.MLIRConfig()mlir_config.set_enable_consteval(True)mlir_config.set_enable_optimizer(True)mlir_config.set_enable_memory_layout_analysis(False)mlir_config.set_enable_fusing(True)mlir_config.set_custom_config("memory-layout-analysis-enabled=false")`
**Sources:**[forge/csrc/forge_bindings.cpp 172-206](https://github.com/tenstorrent/tt-forge-onnx/blob/e519819c/forge/csrc/forge_bindings.cpp#L172-L206)[forge/csrc/passes/mlir_compiler.cpp 241-260](https://github.com/tenstorrent/tt-forge-onnx/blob/e519819c/forge/csrc/passes/mlir_compiler.cpp#L241-L260)

### VerifyConfig

The `VerifyConfig` class controls output verification:

`from forge.verify.config import VerifyConfigfrom forge.verify.value_checkers import AllCloseValueChecker, AutomaticValueChecker # Default verification with automatic checkerverify_config = VerifyConfig() # Custom tolerance for floating point comparisonverify_config = VerifyConfig(    value_checker=AllCloseValueChecker(atol=1e-2, rtol=1e-3)) # Automatic checker for integer data formatsverify_config = VerifyConfig(value_checker=AutomaticValueChecker())`
The verification system compares framework outputs with compiled model outputs using configurable checkers. The `AutomaticValueChecker` selects appropriate comparison based on data type (exact match for integers, PCC for floats).

**Sources:**[forge/test/operators/pytorch/eltwise_nary/test_concatenate.py 158-174](https://github.com/tenstorrent/tt-forge-onnx/blob/e519819c/forge/test/operators/pytorch/eltwise_nary/test_concatenate.py#L158-L174)[forge/test/operators/utils/compat.py 289-315](https://github.com/tenstorrent/tt-forge-onnx/blob/e519819c/forge/test/operators/utils/compat.py#L289-L315)

### Configuration Flow Diagram

**Sources:**[forge/test/operators/utils/utils.py 139-209](https://github.com/tenstorrent/tt-forge-onnx/blob/e519819c/forge/test/operators/utils/utils.py#L139-L209)[forge/test/operators/utils/compat.py 289-315](https://github.com/tenstorrent/tt-forge-onnx/blob/e519819c/forge/test/operators/utils/compat.py#L289-L315)

### Common Configuration Patterns

#### Example 1: Testing with Different Data Formats

`from forge import DataFormat, compilefrom forge.verify.config import VerifyConfigfrom forge.verify.value_checkers import AutomaticValueChecker # Override default data format for all tensorscompiler_cfg = CompilerConfig()compiler_cfg.default_df_override = DataFormat.Float16_b # Use automatic checker for mixed integer/float operationsverify_cfg = VerifyConfig(value_checker=AutomaticValueChecker()) compiled_model = compile(model, sample_inputs, compiler_cfg=compiler_cfg)verify(inputs, model, compiled_model, verify_cfg=verify_cfg)`
#### Example 2: Debugging Intermediate Stages

`from forge import CompileDepth # Stop compilation after optimization passes to inspect intermediate graphcompiler_cfg = CompilerConfig()compiler_cfg.compile_depth = CompileDepth.OPTIMIZED_GRAPH # Graph will be available but not compiled to binaryresults = compile(model, sample_inputs, compiler_cfg=compiler_cfg)# Access intermediate graph: results.final_graph`
#### Example 3: Custom MLIR Pass Configuration

`# Disable specific optimization passes for debuggingmlir_config = forge._C.MLIRConfig()mlir_config.set_enable_consteval(False)mlir_config.set_enable_optimizer(False)mlir_config.set_enable_fusing(True) # Custom pipeline optionsmlir_config.set_custom_config("memory-layout-analysis-enabled=false")`
**Sources:**[forge/forge/compile.py 182-260](https://github.com/tenstorrent/tt-forge-onnx/blob/e519819c/forge/forge/compile.py#L182-L260)[forge/csrc/passes/mlir_compiler.hpp 26-76](https://github.com/tenstorrent/tt-forge-onnx/blob/e519819c/forge/csrc/passes/mlir_compiler.hpp#L26-L76)[forge/forge/verify/config.py 1-261](https://github.com/tenstorrent/tt-forge-onnx/blob/e519819c/forge/forge/verify/config.py#L1-L261)

### CompileDepth and ExecutionStage

The compilation can be stopped at intermediate stages using `compile_depth`:

`from forge import CompileDepthfrom forge.config import CompilerConfig compiler_cfg = CompilerConfig() # Stop after generating initial graphcompiler_cfg.compile_depth = CompileDepth.GENERATE_INITIAL_GRAPH # Stop after optimization passescompiler_cfg.compile_depth = CompileDepth.OPTIMIZED_GRAPH # Stop after autograd pass (for training)compiler_cfg.compile_depth = CompileDepth.AUTOGRAD # Stop before MLIR compilercompiler_cfg.compile_depth = CompileDepth.PRE_LOWERING_PASS # Stop after MLIR compilationcompiler_cfg.compile_depth = CompileDepth.RUN_MLIR_COMPILER # Complete compilation (default)compiler_cfg.compile_depth = CompileDepth.FULL`
Available compilation stages (from [forge/forge/compile.py 278-291](https://github.com/tenstorrent/tt-forge-onnx/blob/e519819c/forge/forge/compile.py#L278-L291)):

| Stage | Description |
| --- | --- |
| `INIT_COMPILE` | Initialize compilation context |
| `GENERATE_INITIAL_GRAPH` | Create initial graph from framework model |
| `POST_INITIAL_GRAPH_PASS` | Run initial graph passes |
| `CONSTEVAL_GRAPH` | Constant evaluation pass |
| `POST_PATTERN_MATCHER` | Pattern matching optimizations |
| `OPTIMIZED_GRAPH` | Run optimization passes |
| `AUTOGRAD` | Generate backward pass (training only) |
| `POST_AUTOGRAD_PASS` | Post-autograd cleanup |
| `PRE_LOWERING_PASS` | Pre-lowering transformations |
| `SPLIT_GRAPH` | Split graph for multi-device |
| `RUN_MLIR_COMPILER` | Lower to MLIR and compile |
| `FINISH_COMPILE` | Finalize compilation |
| `FULL` | Complete pipeline |

This is useful for debugging intermediate representations and isolating compilation stages. Each stage corresponds to an `ExecutionStage` enum value used in property tracking.

**Sources:**[forge/forge/compile.py 278-291](https://github.com/tenstorrent/tt-forge-onnx/blob/e519819c/forge/forge/compile.py#L278-L291)[forge/forge/forge_property_utils.py 279-348](https://github.com/tenstorrent/tt-forge-onnx/blob/e519819c/forge/forge/forge_property_utils.py#L279-L348)

* * *

## Adding New Operators

Adding a new operator requires implementing the operation across multiple layers: OpType enumeration, C++ implementation, MLIR lowering, Python API, and tests.

### Operator Addition Workflow

**Sources:** Based on operation system architecture from Diagram 5 of high-level architecture and code analysis

### Step 1: Add OpType Enum

Add the new operator type to the OpType enumeration. Example for a new operation "my_new_op":

`// In forge/csrc/ops/op.hppenum class OpType {    // ... existing ops ...    Add,    Multiply,    MyNewOp,  // Add your new op here    // ... more ops ...};`
**Sources:**[forge/csrc/ops/op.hpp 40-200](https://github.com/tenstorrent/tt-forge-onnx/blob/e519819c/forge/csrc/ops/op.hpp#L40-L200) (referenced in architecture)

### Step 2: Implement C++ Operation

Create implementation file `op_my_new_op.cpp` with eval, shape, and backward functions:

`// forge/csrc/ops/op_my_new_op.cpp#include "op.hpp" namespace tt::ops { void eval_my_new_op(/* parameters */) {    // Implementation for forward evaluation} Shape shape_my_new_op(/* parameters */) {    // Return output shape given input shapes} Op backward_my_new_op(/* parameters */) {    // Return backward operation for gradient computation} } // namespace tt::ops`
Update dispatch in `op.cpp`:

`// forge/csrc/ops/op.cpp - in eval() methodcase OpType::MyNewOp:    eval_my_new_op(/* args */);    break; // in shape() methodcase OpType::MyNewOp:    return shape_my_new_op(/* args */); // in backward() methodcase OpType::MyNewOp:    return backward_my_new_op(/* args */);`
**Sources:**[forge/csrc/ops/op.cpp 1-500](https://github.com/tenstorrent/tt-forge-onnx/blob/e519819c/forge/csrc/ops/op.cpp#L1-L500) (referenced in architecture)

### Step 3: Add MLIR Lowering Handler

Register the operation in the MLIR lowering handler map:

`// forge/csrc/passes/lower_to_mlir.cpp - in init_lowering_handler_map()void MLIRGenerator::init_lowering_handler_map() {    // ... existing mappings ...    lowering_handler_map["my_new_op"] =         &MLIRGenerator::emit_mlir_ttforge_op<mlir::tt::ttir::MyNewOpOp>;}`
If attribute remapping is needed, add to `AttributeMapper::initialize_default_mappings()`:

`// forge/csrc/passes/lower_to_mlir.cpp:116-175void initialize_default_mappings() {    // Add mapping for your op's attributes    add_op_mapping("my_new_op", "my_param",                    AttributeRemap(std::nullopt, TargetType::I32Attr));    add_op_mapping("my_new_op", "my_array",                    AttributeRemap("new_name", TargetType::DenseI32ArrayAttr));}`
**Sources:**[forge/csrc/passes/lower_to_mlir.cpp 784-843](https://github.com/tenstorrent/tt-forge-onnx/blob/e519819c/forge/csrc/passes/lower_to_mlir.cpp#L784-L843)[forge/csrc/passes/lower_to_mlir.cpp 116-175](https://github.com/tenstorrent/tt-forge-onnx/blob/e519819c/forge/csrc/passes/lower_to_mlir.cpp#L116-L175)

### Step 4: Create Python API

Create Python wrapper in appropriate category file (e.g., `op/eltwise_binary.py`):

`# forge/forge/op/eltwise_binary.pyfrom forge._C.graph import OpTypefrom .common import ForgeOp def my_new_op(name: str, operand_a: Tensor, operand_b: Tensor,               my_param: int) -> Tensor:    """    My new operation documentation.        Args:        name: Operation name        operand_a: First input tensor        operand_b: Second input tensor          my_param: Operation parameter            Returns:        Output tensor    """    return ForgeOp(        OpType.my_new_op,        name,        operand_a,        operand_b,        attrs={"my_param": my_param}    )`
**Sources:**[forge/forge/op/eltwise_binary.py 1-438](https://github.com/tenstorrent/tt-forge-onnx/blob/e519819c/forge/forge/op/eltwise_binary.py#L1-L438) (referenced in architecture)

### Step 5: Register in Operator Repository

Add operator definition to the repository:

`# forge/forge/op_repo/pytorch_operators.pyOperatorDefinition(    name="my_new_op",    module="torch",    full_name="torch.my_new_op",    constructor_params=[        ConstructorParameter("my_param", int),    ],    forward_params=[        ForwardParameter("operand_a", torch.Tensor),        ForwardParameter("operand_b", torch.Tensor),    ],)`
**Sources:**[forge/forge/op_repo/pytorch_operators.py 14-185](https://github.com/tenstorrent/tt-forge-onnx/blob/e519819c/forge/forge/op_repo/pytorch_operators.py#L14-L185) (referenced in architecture)

### Step 6: Create Test Plan

Create comprehensive test file following the test plan template:

`# forge/test/operators/pytorch/eltwise_binary/test_my_new_op.pyimport torchfrom test.operators.utils import (    TestPlan, TestCollection, TestVector,     TestCollectionCommon, VerifyUtils) class TestVerification:    @classmethod    def verify(cls, test_device, test_vector: TestVector):        operator = torch.my_new_op        kwargs = test_vector.kwargs if test_vector.kwargs else {}                class Model(torch.nn.Module):            def forward(self, a, b):                return operator(a, b, **kwargs)                VerifyUtils.verify(            model=Model(),            test_device=test_device,            input_shapes=test_vector.input_shape,            dev_data_format=test_vector.dev_data_format,            math_fidelity=test_vector.math_fidelity,        ) test_plan = TestPlan(    verify=TestVerification.verify,    collections=[        TestCollection(            operators=["my_new_op"],            input_sources=TestCollectionCommon.all.input_sources,            input_shapes=TestCollectionCommon.all.input_shapes,            kwargs=[                {"my_param": 1},                {"my_param": 2},                {"my_param": 5},            ],        ),    ],    failing_rules=[        # Add known failures here    ],)`
**Sources:**[forge/test/operators/pytorch/eltwise_nary/test_concatenate.py 1-264](https://github.com/tenstorrent/tt-forge-onnx/blob/e519819c/forge/test/operators/pytorch/eltwise_nary/test_concatenate.py#L1-L264)[forge/test/operators/utils/plan.py 336-454](https://github.com/tenstorrent/tt-forge-onnx/blob/e519819c/forge/test/operators/utils/plan.py#L336-L454)

### Operator Testing Structure

Tests should cover:

*   All input sources (FROM_HOST, FROM_ANOTHER_OP, FROM_DRAM_QUEUE, CONST_EVAL_PASS)
*   Various input shapes (2D, 3D, 4D with different microbatch sizes)
*   Data formats (Float32, Float16_b, Int8, etc.)
*   Math fidelities (HiFi4, HiFi3, HiFi2, LoFi)
*   Different parameter combinations via kwargs

**Sources:**[forge/test/operators/pytorch/eltwise_nary/test_concatenate.py 108-175](https://github.com/tenstorrent/tt-forge-onnx/blob/e519819c/forge/test/operators/pytorch/eltwise_nary/test_concatenate.py#L108-L175)[forge/test/operators/utils/plan.py 336-454](https://github.com/tenstorrent/tt-forge-onnx/blob/e519819c/forge/test/operators/utils/plan.py#L336-L454)

### Testing Best Practices

1.   **Organize by Category**: Place tests in appropriate subdirectory (`eltwise_binary/`, `eltwise_unary/`, `matmul/`, etc.)

2.   **Use TestCollection**: Define comprehensive test collections covering all combinations

3.   **Define Failing Rules**: Document known issues with specific `FailingReasons`:

`failing_rules=[    TestCollection(        operators=["my_new_op"],        kwargs=[{"my_param": 999}],        failing_reason=FailingReasons.DATA_MISMATCH,    ),]`
4.   **Generate Parameters**: Use generator functions for combinatorial kwargs:

`@classmethoddef generate_kwargs(cls, test_vector: TestVector):    for param in [1, 2, 5, 10]:        yield {"my_param": param}`
5.   **Provide Multiple Models**: Implement model variants for different input sources

**Sources:**[forge/test/operators/pytorch/eltwise_nary/test_concatenate.py 177-264](https://github.com/tenstorrent/tt-forge-onnx/blob/e519819c/forge/test/operators/pytorch/eltwise_nary/test_concatenate.py#L177-L264)[forge/test/operators/utils/failing_reasons.py 16-270](https://github.com/tenstorrent/tt-forge-onnx/blob/e519819c/forge/test/operators/utils/failing_reasons.py#L16-L270) (referenced in architecture)

* * *

This wiki is featured in the [repository](https://github.com/tenstorrent/tt-forge-onnx/blob/main/README.md)

Dismiss
Refresh this wiki

Enter email to refresh
---
project: tt-buda
github: https://github.com/tenstorrent/tt-buda
deepwiki: https://deepwiki.com/tenstorrent/tt-buda/3-testing-framework
---

# Testing Framework

Relevant source files
*   [pybuda/pybuda/config.py](https://github.com/tenstorrent/tt-buda/blob/74755d1f/pybuda/pybuda/config.py)
*   [pybuda/pybuda/op_repo/datatypes.py](https://github.com/tenstorrent/tt-buda/blob/74755d1f/pybuda/pybuda/op_repo/datatypes.py)
*   [pybuda/pybuda/pybudaglobal.py](https://github.com/tenstorrent/tt-buda/blob/74755d1f/pybuda/pybuda/pybudaglobal.py)
*   [pybuda/pybuda/tti/runtime_param_yamls/bh_syslevel.yaml](https://github.com/tenstorrent/tt-buda/blob/74755d1f/pybuda/pybuda/tti/runtime_param_yamls/bh_syslevel.yaml)
*   [pybuda/pybuda/verify/config.py](https://github.com/tenstorrent/tt-buda/blob/74755d1f/pybuda/pybuda/verify/config.py)
*   [pybuda/test/README.debug.md](https://github.com/tenstorrent/tt-buda/blob/74755d1f/pybuda/test/README.debug.md?plain=1)
*   [pybuda/test/conftest.py](https://github.com/tenstorrent/tt-buda/blob/74755d1f/pybuda/test/conftest.py)
*   [pybuda/test/operators/eltwise_binary/test_eltwise_binary.py](https://github.com/tenstorrent/tt-buda/blob/74755d1f/pybuda/test/operators/eltwise_binary/test_eltwise_binary.py)
*   [pybuda/test/operators/eltwise_unary/test_eltwise_unary.py](https://github.com/tenstorrent/tt-buda/blob/74755d1f/pybuda/test/operators/eltwise_unary/test_eltwise_unary.py)
*   [pybuda/test/operators/matmul/test_matmul.py](https://github.com/tenstorrent/tt-buda/blob/74755d1f/pybuda/test/operators/matmul/test_matmul.py)
*   [pybuda/test/operators/matmul/test_matmul_pytorch.py](https://github.com/tenstorrent/tt-buda/blob/74755d1f/pybuda/test/operators/matmul/test_matmul_pytorch.py)
*   [pybuda/test/operators/matmul/test_sparse_matmul.py](https://github.com/tenstorrent/tt-buda/blob/74755d1f/pybuda/test/operators/matmul/test_sparse_matmul.py)
*   [pybuda/test/operators/nary/test_concatenate.py](https://github.com/tenstorrent/tt-buda/blob/74755d1f/pybuda/test/operators/nary/test_concatenate.py)
*   [pybuda/test/operators/nary/test_index_copy.py](https://github.com/tenstorrent/tt-buda/blob/74755d1f/pybuda/test/operators/nary/test_index_copy.py)
*   [pybuda/test/operators/nary/test_stack.py](https://github.com/tenstorrent/tt-buda/blob/74755d1f/pybuda/test/operators/nary/test_stack.py)
*   [pybuda/test/operators/nary/test_where.py](https://github.com/tenstorrent/tt-buda/blob/74755d1f/pybuda/test/operators/nary/test_where.py)
*   [pybuda/test/operators/utils/__init__.py](https://github.com/tenstorrent/tt-buda/blob/74755d1f/pybuda/test/operators/utils/__init__.py)
*   [pybuda/test/operators/utils/failing_reasons.py](https://github.com/tenstorrent/tt-buda/blob/74755d1f/pybuda/test/operators/utils/failing_reasons.py)
*   [pybuda/test/operators/utils/utils.py](https://github.com/tenstorrent/tt-buda/blob/74755d1f/pybuda/test/operators/utils/utils.py)
*   [pybuda/test/random/rgg/__init__.py](https://github.com/tenstorrent/tt-buda/blob/74755d1f/pybuda/test/random/rgg/__init__.py)
*   [pybuda/test/random/rgg/algorithms.py](https://github.com/tenstorrent/tt-buda/blob/74755d1f/pybuda/test/random/rgg/algorithms.py)
*   [pybuda/test/random/rgg/base.py](https://github.com/tenstorrent/tt-buda/blob/74755d1f/pybuda/test/random/rgg/base.py)
*   [pybuda/test/random/rgg/config.py](https://github.com/tenstorrent/tt-buda/blob/74755d1f/pybuda/test/random/rgg/config.py)
*   [pybuda/test/random/rgg/datatypes.py](https://github.com/tenstorrent/tt-buda/blob/74755d1f/pybuda/test/random/rgg/datatypes.py)
*   [pybuda/test/random/rgg/pybuda/generated_model.jinja2](https://github.com/tenstorrent/tt-buda/blob/74755d1f/pybuda/test/random/rgg/pybuda/generated_model.jinja2)
*   [pybuda/test/random/rgg/pytorch/generated_model.jinja2](https://github.com/tenstorrent/tt-buda/blob/74755d1f/pybuda/test/random/rgg/pytorch/generated_model.jinja2)
*   [pybuda/test/random/rgg/utils.py](https://github.com/tenstorrent/tt-buda/blob/74755d1f/pybuda/test/random/rgg/utils.py)
*   [pybuda/test/tti/test_tti.py](https://github.com/tenstorrent/tt-buda/blob/74755d1f/pybuda/test/tti/test_tti.py)
*   [pybuda/test/utils.py](https://github.com/tenstorrent/tt-buda/blob/74755d1f/pybuda/test/utils.py)

## Purpose and Scope

The PyBuda Testing Framework is a comprehensive set of tools and utilities designed to verify the correctness and functionality of the PyBuda system. The framework provides mechanisms for testing individual operators, complete models, and automatically generating random computational graphs for exhaustive testing. This page focuses on the overall testing infrastructure - how tests are written, configured, and executed to ensure PyBuda's reliability.

For specific information about testing individual operators, see [Operator Testing](https://deepwiki.com/tenstorrent/tt-buda/3.2-operator-testing). For details on model testing, see [Model Testing](https://deepwiki.com/tenstorrent/tt-buda/3.3-model-testing). For information about the Random Graph Generator, see [Random Graph Generator](https://deepwiki.com/tenstorrent/tt-buda/3.1-random-graph-generator).

## Overview of Testing Architecture

The testing framework in PyBuda consists of several interconnected components that work together to provide comprehensive testing capabilities.

Sources:

*   [pybuda/test/conftest.py 158-317](https://github.com/tenstorrent/tt-buda/blob/74755d1f/pybuda/test/conftest.py#L158-L317)
*   [pybuda/test/random/rgg/base.py 150-311](https://github.com/tenstorrent/tt-buda/blob/74755d1f/pybuda/test/random/rgg/base.py#L150-L311)
*   [pybuda/test/operators/utils/utils.py 71-118](https://github.com/tenstorrent/tt-buda/blob/74755d1f/pybuda/test/operators/utils/utils.py#L71-L118)

## Test Device Infrastructure

PyBuda tests can run on several types of devices, each representing a different execution mode or hardware platform. The `TestDevice` class encapsulates this functionality.

Sources:

*   [pybuda/test/conftest.py 180-269](https://github.com/tenstorrent/tt-buda/blob/74755d1f/pybuda/test/conftest.py#L180-L269)

### Available Test Devices

The testing framework supports several types of test devices:

| Device Type | Description | Backend Type | Use Case |
| --- | --- | --- | --- |
| Golden | Reference implementation | BackendType.Golden | Basic verification without hardware |
| Model | High-level simulation | BackendType.Model | Complex verifications without hardware |
| Versim | Cycle-accurate simulation | BackendType.Versim | Detailed behavior verification |
| Emulation | Hardware emulation | BackendType.Emulation | Hardware-like behavior without real hardware |
| Silicon | Real Tenstorrent hardware | BackendType.Silicon | Full end-to-end testing |

Each device type supports different architectures:

*   Grayskull
*   Wormhole_B0
*   Blackhole

Sources:

*   [pybuda/test/conftest.py 191-221](https://github.com/tenstorrent/tt-buda/blob/74755d1f/pybuda/test/conftest.py#L191-L221)
*   [pybuda/test/conftest.py 271-316](https://github.com/tenstorrent/tt-buda/blob/74755d1f/pybuda/test/conftest.py#L271-L316)

## Verification System

The verification system is responsible for comparing PyBuda's outputs with expected reference results.

Sources:

*   [pybuda/test/operators/utils/utils.py 71-118](https://github.com/tenstorrent/tt-buda/blob/74755d1f/pybuda/test/operators/utils/utils.py#L71-L118)
*   [pybuda/pybuda/verify/config.py 18-48](https://github.com/tenstorrent/tt-buda/blob/74755d1f/pybuda/pybuda/verify/config.py#L18-L48)
*   [pybuda/pybuda/verify/config.py 56-116](https://github.com/tenstorrent/tt-buda/blob/74755d1f/pybuda/pybuda/verify/config.py#L56-L116)

### Verification Configuration

The `VerifyConfig` class provides options for configuring verification:

| Option | Description |
| --- | --- |
| `test_kind` | Test kind: INFERENCE, TRAINING, TRAINING_RECOMPUTE |
| `devtype` | Device type for verification |
| `arch` | Device architecture |
| `pcc` | Pearson Correlation Coefficient threshold |
| `rtol` | Relative tolerance |
| `atol` | Absolute tolerance |
| `relative_atol` | Relative absolute tolerance |

Sources:

*   [pybuda/pybuda/verify/config.py 56-116](https://github.com/tenstorrent/tt-buda/blob/74755d1f/pybuda/pybuda/verify/config.py#L56-L116)

### Verification Utilities

The `VerifyUtils` class provides utilities for verification, including:

*   `verify`: Verifies a PyBuda module against expected outputs
*   `get_netlist_filename`: Gets the netlist filename of the last compiled model

Example verification:

`VerifyUtils.verify(    model=model,    test_device=test_device,    input_shapes=input_shapes,    input_params=input_params,    input_source_flag=input_source_flag,    dev_data_format=dev_data_format,    math_fidelity=math_fidelity,)`
Sources:

*   [pybuda/test/operators/utils/utils.py 71-118](https://github.com/tenstorrent/tt-buda/blob/74755d1f/pybuda/test/operators/utils/utils.py#L71-L118)

## Random Graph Generator (RGG)

The Random Graph Generator (RGG) is an advanced testing system that automatically generates random computational graphs to extensively test PyBuda's compilation and execution.

Sources:

*   [pybuda/test/random/rgg/algorithms.py 24-404](https://github.com/tenstorrent/tt-buda/blob/74755d1f/pybuda/test/random/rgg/algorithms.py#L24-L404)
*   [pybuda/test/random/rgg/utils.py 25-308](https://github.com/tenstorrent/tt-buda/blob/74755d1f/pybuda/test/random/rgg/utils.py#L25-L308)
*   [pybuda/test/random/rgg/datatypes.py 18-179](https://github.com/tenstorrent/tt-buda/blob/74755d1f/pybuda/test/random/rgg/datatypes.py#L18-L179)
*   [pybuda/test/random/rgg/base.py 26-339](https://github.com/tenstorrent/tt-buda/blob/74755d1f/pybuda/test/random/rgg/base.py#L26-L339)

### RGG Components

The key components of the Random Graph Generator include:

| Component | Description |
| --- | --- |
| `RandomizerConfig` | Configures RGG behavior, including graph size, dimensions, and other parameters |
| `RandomizerTestContext` | Context for a randomizer test, including configuration, parameters, and graph |
| `RandomGraphAlgorithm` | Algorithm for building random graphs |
| `GraphNodeSetup` | Class for setting up and validating graph nodes |
| `RandomizerRunner` | Runner for executing randomized tests |
| `RandomizerModelProviderFromSourceCode` | Provides models from generated source code |
| `RandomizerCodeGenerator` | Generates test code for randomized tests |

Sources:

*   [pybuda/test/random/rgg/algorithms.py 24-203](https://github.com/tenstorrent/tt-buda/blob/74755d1f/pybuda/test/random/rgg/algorithms.py#L24-L203)
*   [pybuda/test/random/rgg/algorithms.py 203-404](https://github.com/tenstorrent/tt-buda/blob/74755d1f/pybuda/test/random/rgg/algorithms.py#L203-L404)
*   [pybuda/test/random/rgg/datatypes.py 18-123](https://github.com/tenstorrent/tt-buda/blob/74755d1f/pybuda/test/random/rgg/datatypes.py#L18-L123)
*   [pybuda/test/random/rgg/datatypes.py 124-179](https://github.com/tenstorrent/tt-buda/blob/74755d1f/pybuda/test/random/rgg/datatypes.py#L124-L179)
*   [pybuda/test/random/rgg/base.py 26-139](https://github.com/tenstorrent/tt-buda/blob/74755d1f/pybuda/test/random/rgg/base.py#L26-L139)
*   [pybuda/test/random/rgg/base.py 150-311](https://github.com/tenstorrent/tt-buda/blob/74755d1f/pybuda/test/random/rgg/base.py#L150-L311)

### RGG Configuration

The Random Graph Generator can be configured through `RandomizerConfig`:

| Parameter | Description | Default |
| --- | --- | --- |
| `dim_min` | Minimum number of dimensions | 4 |
| `dim_max` | Maximum number of dimensions | 4 |
| `op_size_per_dim_min` | Minimum size per dimension | 16 |
| `op_size_per_dim_max` | Maximum size per dimension | 64 |
| `microbatch_size_min` | Minimum microbatch size | 1 |
| `microbatch_size_max` | Maximum microbatch size | 8 |
| `num_of_nodes_min` | Minimum number of nodes | 5 |
| `num_of_nodes_max` | Maximum number of nodes | 10 |
| `num_fork_joins_max` | Maximum number of fork joins | 50 |
| `constant_input_rate` | Rate of constant inputs | 20 |
| `same_inputs_percent_limit` | Limit of same inputs | 10 |
| `verification_timeout` | Timeout for verification | 60 |

Environment variables can override these defaults:

*   `MIN_DIM`, `MAX_DIM`
*   `MIN_OP_SIZE_PER_DIM`, `MAX_OP_SIZE_PER_DIM`
*   `MIN_MICROBATCH_SIZE`, `MAX_MICROBATCH_SIZE`
*   `NUM_OF_NODES_MIN`, `NUM_OF_NODES_MAX`
*   `NUM_OF_FORK_JOINS_MAX`
*   `CONSTANT_INPUT_RATE`
*   `SAME_INPUTS_PERCENT_LIMIT`
*   `VERIFICATION_TIMEOUT`

Sources:

*   [pybuda/test/random/rgg/config.py 12-41](https://github.com/tenstorrent/tt-buda/blob/74755d1f/pybuda/test/random/rgg/config.py#L12-L41)
*   [pybuda/test/README.debug.md 1-19](https://github.com/tenstorrent/tt-buda/blob/74755d1f/pybuda/test/README.debug.md?plain=1#L1-L19)

### RGG Test Execution

Random Graph Generator tests are executed through the `process_test` function:

`process_test(    test_name,    test_index,    random_seed,    test_device,    randomizer_config,    graph_builder_type,    framework)`
This function:

1.   Instantiates a graph builder
2.   Creates test parameters and context
3.   Instantiates a model builder and runner
4.   Runs the test

Sources:

*   [pybuda/test/random/rgg/base.py 313-339](https://github.com/tenstorrent/tt-buda/blob/74755d1f/pybuda/test/random/rgg/base.py#L313-L339)

## Operator Testing

Operator testing verifies the correctness of individual PyBuda operators under various conditions.

Sources:

*   [pybuda/test/operators/eltwise_binary/test_eltwise_binary.py 10-57](https://github.com/tenstorrent/tt-buda/blob/74755d1f/pybuda/test/operators/eltwise_binary/test_eltwise_binary.py#L10-L57)
*   [pybuda/test/operators/eltwise_unary/test_eltwise_unary.py 10-57](https://github.com/tenstorrent/tt-buda/blob/74755d1f/pybuda/test/operators/eltwise_unary/test_eltwise_unary.py#L10-L57)
*   [pybuda/test/operators/nary/test_stack.py 9-56](https://github.com/tenstorrent/tt-buda/blob/74755d1f/pybuda/test/operators/nary/test_stack.py#L9-L56)
*   [pybuda/test/operators/matmul/test_matmul.py 10-57](https://github.com/tenstorrent/tt-buda/blob/74755d1f/pybuda/test/operators/matmul/test_matmul.py#L10-L57)
*   [pybuda/test/operators/utils/utils.py 36-118](https://github.com/tenstorrent/tt-buda/blob/74755d1f/pybuda/test/operators/utils/utils.py#L36-L118)

### Operator Categories

PyBuda tests various categories of operators:

| Category | Examples | File |
| --- | --- | --- |
| Elementwise Unary | Abs, LeakyRelu, Exp, Identity, Relu | [pybuda/test/operators/eltwise_unary/test_eltwise_unary.py](https://github.com/tenstorrent/tt-buda/blob/74755d1f/pybuda/test/operators/eltwise_unary/test_eltwise_unary.py) |
| Elementwise Binary | Add, Max, Min, Power, Subtract, Multiply | [pybuda/test/operators/eltwise_binary/test_eltwise_binary.py](https://github.com/tenstorrent/tt-buda/blob/74755d1f/pybuda/test/operators/eltwise_binary/test_eltwise_binary.py) |
| Matrix Multiplication | Matmul, SparseMatmul | [pybuda/test/operators/matmul/test_matmul.py](https://github.com/tenstorrent/tt-buda/blob/74755d1f/pybuda/test/operators/matmul/test_matmul.py) |
| N-ary Operators | Stack, Concatenate, Where, IndexCopy | [pybuda/test/operators/nary/test_stack.py](https://github.com/tenstorrent/tt-buda/blob/74755d1f/pybuda/test/operators/nary/test_stack.py) |

Sources:

*   [pybuda/test/operators/eltwise_unary/test_eltwise_unary.py 142-164](https://github.com/tenstorrent/tt-buda/blob/74755d1f/pybuda/test/operators/eltwise_unary/test_eltwise_unary.py#L142-L164)
*   [pybuda/test/operators/eltwise_binary/test_eltwise_binary.py 280-297](https://github.com/tenstorrent/tt-buda/blob/74755d1f/pybuda/test/operators/eltwise_binary/test_eltwise_binary.py#L280-L297)
*   [pybuda/test/operators/matmul/test_matmul.py 131-141](https://github.com/tenstorrent/tt-buda/blob/74755d1f/pybuda/test/operators/matmul/test_matmul.py#L131-L141)
*   [pybuda/test/operators/nary/test_stack.py 101-106](https://github.com/tenstorrent/tt-buda/blob/74755d1f/pybuda/test/operators/nary/test_stack.py#L101-L106)

### Operator Test Parameters

Operator tests vary inputs based on:

1.   **Input Source**: Where the operator input comes from

    *   From another op (`model_op_src_from_another_op`)
    *   From host (`model_op_src_from_host`)
    *   From DRAM queue (`model_op_src_from_dram_queue`)
    *   From DRAM, prologued (`model_op_src_from_dram_queue_prologued`)
    *   From constant evaluation (`model_op_src_const_eval_pass`)
    *   From TM edge (`model_op_src_from_tm_edge1`, `model_op_src_from_tm_edge2`)

2.   **Input Shapes**: Varied shapes for comprehensive testing

    *   2D, 3D, and 4D tensors
    *   Various microbatch sizes
    *   Shapes divisible by 32
    *   Prime number dimensions
    *   Large dimensions (thousands)
    *   Extreme ratios between height/width

3.   **Data Format**: Various data formats

    *   Float32, Float16, etc.

4.   **Math Fidelity**: Various math fidelity options

    *   LoFi, HiFi2, HiFi3, HiFi4

Sources:

*   [pybuda/test/operators/eltwise_binary/test_eltwise_binary.py 85-236](https://github.com/tenstorrent/tt-buda/blob/74755d1f/pybuda/test/operators/eltwise_binary/test_eltwise_binary.py#L85-L236)
*   [pybuda/test/operators/eltwise_binary/test_eltwise_binary.py 269-315](https://github.com/tenstorrent/tt-buda/blob/74755d1f/pybuda/test/operators/eltwise_binary/test_eltwise_binary.py#L269-L315)
*   [pybuda/test/operators/utils/utils.py 36-63](https://github.com/tenstorrent/tt-buda/blob/74755d1f/pybuda/test/operators/utils/utils.py#L36-L63)

### Operator Testing Example

Operator tests are parameterized to test various combinations of operators, models, and shapes:

`@pytest.mark.parametrize("input_operator", get_eltwise_binary_ops())@pytest.mark.parametrize("model_type", MODEL_TYPES)@pytest.mark.parametrize("input_shape", get_input_shapes())def test_eltwise_binary_ops_per_test_plan(    input_operator,    model_type,    input_shape,    test_device,    dev_data_format=None,     input_math_fidelity=None):    # ...test implementation...    verify(        test_device=test_device,        model_type=model_type,        input_operator=input_operator,        input_shape=input_shape,        number_of_operands=2,        kwargs=kwargs,        input_source_flag=input_source_flag,        dev_data_format=dev_data_format,        math_fidelity=input_math_fidelity,    )`
Sources:

*   [pybuda/test/operators/eltwise_binary/test_eltwise_binary.py 406-465](https://github.com/tenstorrent/tt-buda/blob/74755d1f/pybuda/test/operators/eltwise_binary/test_eltwise_binary.py#L406-L465)

## Test Configuration System

The test configuration system allows customizing tests through various configuration options.

Sources:

*   [pybuda/test/conftest.py 98-123](https://github.com/tenstorrent/tt-buda/blob/74755d1f/pybuda/test/conftest.py#L98-L123)
*   [pybuda/test/conftest.py 271-316](https://github.com/tenstorrent/tt-buda/blob/74755d1f/pybuda/test/conftest.py#L271-L316)
*   [pybuda/pybuda/config.py 122-336](https://github.com/tenstorrent/tt-buda/blob/74755d1f/pybuda/pybuda/config.py#L122-L336)
*   [pybuda/pybuda/config.py 436-488](https://github.com/tenstorrent/tt-buda/blob/74755d1f/pybuda/pybuda/config.py#L436-L488)

### Compiler Configuration

The `CompilerConfig` class provides options for configuring the PyBuda compiler:

| Option | Description |
| --- | --- |
| `enable_training` | Enable training mode |
| `enable_recompute` | Enable training recompute |
| `balancer_policy` | Balancer policy (default, NLP, Ribbon, etc.) |
| `enable_t_streaming` | Enable t-streaming optimization |
| `enable_consteval` | Enable constant evaluation |
| `default_df_override` | Default data format |
| `default_math_fidelity` | Default math fidelity |
| `performance_trace` | Performance trace level |
| `backend_opt_level` | Backend optimization level |
| `chip_placement_policy` | Chip placement policy |

Sources:

*   [pybuda/pybuda/config.py 147-248](https://github.com/tenstorrent/tt-buda/blob/74755d1f/pybuda/pybuda/config.py#L147-L248)
*   [pybuda/pybuda/config.py 436-488](https://github.com/tenstorrent/tt-buda/blob/74755d1f/pybuda/pybuda/config.py#L436-L488)

### Command-Line Options

The test framework supports various command-line options:

| Option | Description |
| --- | --- |
| `--silicon-only` | Run silicon tests only, skip golden/model |
| `--no-silicon` | Skip silicon tests |
| `--compile-only` | Only compile the model and generate TTI |
| `--run-only` | Load the generated TTI and only run the model |
| `--tti-path` | Path for saving/loading TTI |
| `--device-config` | Device configuration |
| `--devtype` | Backend device type |

Sources:

*   [pybuda/test/conftest.py 98-123](https://github.com/tenstorrent/tt-buda/blob/74755d1f/pybuda/test/conftest.py#L98-L123)

### Environment Variables

The test framework can be configured through environment variables:

| Variable | Description |
| --- | --- |
| `PYBUDA_OVERRIDE_NUM_CHIPS` | Override number of chips |
| `PYBUDA_DISABLE_OP_FUSING` | Disable operation fusing |
| `PYBUDA_PERFORMANCE_TRACE` | Set performance trace level |
| `PYBUDA_COMPILE_DEPTH` | Set compilation depth |
| `PYBUDA_DEFAULT_DF` | Set default data format |
| `RANDOM_TEST_COUNT` | Number of random tests to generate |
| `MIN_DIM`, `MAX_DIM` | Min/max dimensions for RGG |
| `MIN_OP_SIZE_PER_DIM`, `MAX_OP_SIZE_PER_DIM` | Min/max operand size |
| `MIN_MICROBATCH_SIZE`, `MAX_MICROBATCH_SIZE` | Min/max microbatch size |

Sources:

*   [pybuda/pybuda/config.py 251-317](https://github.com/tenstorrent/tt-buda/blob/74755d1f/pybuda/pybuda/config.py#L251-L317)
*   [pybuda/test/README.debug.md 1-19](https://github.com/tenstorrent/tt-buda/blob/74755d1f/pybuda/test/README.debug.md?plain=1#L1-L19)

## Integration with PyBuda Architecture

The Testing Framework is tightly integrated with the PyBuda architecture, providing comprehensive testing capabilities for all aspects of PyBuda.

Sources:

*   [pybuda/test/operators/utils/utils.py 71-118](https://github.com/tenstorrent/tt-buda/blob/74755d1f/pybuda/test/operators/utils/utils.py#L71-L118)
*   [pybuda/test/random/rgg/base.py 150-311](https://github.com/tenstorrent/tt-buda/blob/74755d1f/pybuda/test/random/rgg/base.py#L150-L311)
*   [pybuda/test/conftest.py 180-269](https://github.com/tenstorrent/tt-buda/blob/74755d1f/pybuda/test/conftest.py#L180-L269)

## Conclusion

The PyBuda Testing Framework provides a comprehensive set of tools for testing and verifying PyBuda's functionality. Through operator testing, model testing, and automatic random graph generation, the framework ensures that PyBuda operates correctly across a wide range of scenarios and configurations. The verification system compares PyBuda's outputs with expected results, while the test configuration system allows customizing tests to target specific aspects of PyBuda.

Dismiss
Refresh this wiki

Enter email to refresh
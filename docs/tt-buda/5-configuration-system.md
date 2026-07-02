---
project: tt-buda
github: https://github.com/tenstorrent/tt-buda
deepwiki: https://deepwiki.com/tenstorrent/tt-buda/5-configuration-system
---

# Configuration System

Relevant source files
*   [README.debug.md](https://github.com/tenstorrent/tt-buda/blob/74755d1f/README.debug.md?plain=1)
*   [pybuda/csrc/balancer/balancer_utils.cpp](https://github.com/tenstorrent/tt-buda/blob/74755d1f/pybuda/csrc/balancer/balancer_utils.cpp)
*   [pybuda/csrc/balancer/balancer_utils.hpp](https://github.com/tenstorrent/tt-buda/blob/74755d1f/pybuda/csrc/balancer/balancer_utils.hpp)
*   [pybuda/csrc/balancer/legalizer/legalizer.cpp](https://github.com/tenstorrent/tt-buda/blob/74755d1f/pybuda/csrc/balancer/legalizer/legalizer.cpp)
*   [pybuda/csrc/balancer/python_bindings.cpp](https://github.com/tenstorrent/tt-buda/blob/74755d1f/pybuda/csrc/balancer/python_bindings.cpp)
*   [pybuda/csrc/balancer/types.cpp](https://github.com/tenstorrent/tt-buda/blob/74755d1f/pybuda/csrc/balancer/types.cpp)
*   [pybuda/csrc/balancer/types.hpp](https://github.com/tenstorrent/tt-buda/blob/74755d1f/pybuda/csrc/balancer/types.hpp)
*   [pybuda/csrc/shared_utils/sparse_matmul_utils.hpp](https://github.com/tenstorrent/tt-buda/blob/74755d1f/pybuda/csrc/shared_utils/sparse_matmul_utils.hpp)
*   [pybuda/pybuda/config.py](https://github.com/tenstorrent/tt-buda/blob/74755d1f/pybuda/pybuda/config.py)
*   [pybuda/pybuda/op/eval/buda/matmul.py](https://github.com/tenstorrent/tt-buda/blob/74755d1f/pybuda/pybuda/op/eval/buda/matmul.py)
*   [pybuda/pybuda/op/eval/common.py](https://github.com/tenstorrent/tt-buda/blob/74755d1f/pybuda/pybuda/op/eval/common.py)
*   [pybuda/pybuda/op/eval/pybuda/matmul.py](https://github.com/tenstorrent/tt-buda/blob/74755d1f/pybuda/pybuda/op/eval/pybuda/matmul.py)
*   [pybuda/pybuda/pybudaglobal.py](https://github.com/tenstorrent/tt-buda/blob/74755d1f/pybuda/pybuda/pybudaglobal.py)
*   [pybuda/pybuda/tti/runtime_param_yamls/bh_syslevel.yaml](https://github.com/tenstorrent/tt-buda/blob/74755d1f/pybuda/pybuda/tti/runtime_param_yamls/bh_syslevel.yaml)
*   [pybuda/pybuda/verify/config.py](https://github.com/tenstorrent/tt-buda/blob/74755d1f/pybuda/pybuda/verify/config.py)
*   [pybuda/test/conftest.py](https://github.com/tenstorrent/tt-buda/blob/74755d1f/pybuda/test/conftest.py)
*   [pybuda/test/tti/test_tti.py](https://github.com/tenstorrent/tt-buda/blob/74755d1f/pybuda/test/tti/test_tti.py)

The PyBuda Configuration System provides mechanisms to control and customize the behavior of the compiler, balancer, runtime execution, and verification components within the PyBuda framework. This page documents the core configuration components, their interactions, and the different ways to configure PyBuda during development and deployment.

For configuration specific to testing, see [Test Configuration](https://deepwiki.com/tenstorrent/tt-buda/5.2-test-configuration). For details about compiler-specific options, see [Compiler Configuration](https://deepwiki.com/tenstorrent/tt-buda/5.1-compiler-configuration).

## Configuration Architecture

The PyBuda Configuration System consists of multiple configuration classes that control different aspects of the framework. These configurations work together to determine how models are compiled, optimized, and executed on Tenstorrent hardware.

Sources: [pybuda/pybuda/config.py 147-247](https://github.com/tenstorrent/tt-buda/blob/74755d1f/pybuda/pybuda/config.py#L147-L247)[pybuda/pybuda/verify/config.py 54-106](https://github.com/tenstorrent/tt-buda/blob/74755d1f/pybuda/pybuda/verify/config.py#L54-L106)[pybuda/csrc/balancer/python_bindings.cpp 173-240](https://github.com/tenstorrent/tt-buda/blob/74755d1f/pybuda/csrc/balancer/python_bindings.cpp#L173-L240)

## Configuration Classes

### CompilerConfig

The `CompilerConfig` class is the primary configuration class controlling the compiler pipeline, including optimization passes, data formats, device settings, and runtime parameters.

A global instance of this class (`g_compiler_config`) stores the active configuration, which can be modified through the `set_configuration_options()` API.

Sources: [pybuda/pybuda/config.py 147-247](https://github.com/tenstorrent/tt-buda/blob/74755d1f/pybuda/pybuda/config.py#L147-L247)[pybuda/pybuda/config.py 430-565](https://github.com/tenstorrent/tt-buda/blob/74755d1f/pybuda/pybuda/config.py#L430-L565)

### BalancerConfig

The `BalancerConfig` class controls how operations are placed on hardware resources, including grid shapes, chip placement policies, and memory usage constraints.

The balancer configuration is derived from the main compiler configuration and controls resource allocation and operation placement strategies.

Sources: [pybuda/csrc/balancer/python_bindings.cpp 173-240](https://github.com/tenstorrent/tt-buda/blob/74755d1f/pybuda/csrc/balancer/python_bindings.cpp#L173-L240)

### VerifyConfig

The `VerifyConfig` class controls the verification behavior, specifying how tensor comparisons are performed, tolerance levels, and other testing parameters.

The verification configuration is used primarily during testing to validate correctness of the compiled models.

Sources: [pybuda/pybuda/verify/config.py 54-106](https://github.com/tenstorrent/tt-buda/blob/74755d1f/pybuda/pybuda/verify/config.py#L54-L106)

## Configuration Mechanisms

PyBuda provides three main ways to configure the framework:

### 1. Python API

The primary method to configure PyBuda is through the Python API. The main function for this is `set_configuration_options()`, which allows setting various compiler options:

For verification configuration, you can create a `VerifyConfig` object:

Sources: [pybuda/pybuda/config.py 430-565](https://github.com/tenstorrent/tt-buda/blob/74755d1f/pybuda/pybuda/config.py#L430-L565)[pybuda/pybuda/verify/config.py 54-106](https://github.com/tenstorrent/tt-buda/blob/74755d1f/pybuda/pybuda/verify/config.py#L54-L106)

### 2. Environment Variables

PyBuda recognizes numerous environment variables (prefixed with `PYBUDA_`) that can override configuration settings. These are useful for quick adjustments, debugging, and CI/CD pipelines.

Common environment variables include:

*   `PYBUDA_BALANCER_POLICY`: Override the balancer policy type
*   `PYBUDA_COMPILE_DEPTH`: Set how far to compile (partial or full)
*   `PYBUDA_DEFAULT_DF`: Set the default data format
*   `PYBUDA_AMP_LEVEL`: Configure automatic mixed precision level
*   `PYBUDA_FORCE_VERIFY_ALL`: Enable verification after every compile stage
*   `PYBUDA_ENABLE_T_STREAMING`: Enable/disable t-streaming optimization

Sources: [README.debug.md 4-143](https://github.com/tenstorrent/tt-buda/blob/74755d1f/README.debug.md?plain=1#L4-L143)[pybuda/pybuda/config.py 251-324](https://github.com/tenstorrent/tt-buda/blob/74755d1f/pybuda/pybuda/config.py#L251-L324)

### 3. Default Values

If a configuration option is not explicitly set, PyBuda uses sensible defaults that work for most cases. These defaults are defined in the configuration classes' initializers.

Sources: [pybuda/pybuda/config.py 147-247](https://github.com/tenstorrent/tt-buda/blob/74755d1f/pybuda/pybuda/config.py#L147-L247)[pybuda/pybuda/verify/config.py 54-106](https://github.com/tenstorrent/tt-buda/blob/74755d1f/pybuda/pybuda/verify/config.py#L54-L106)

## Core Configuration Categories

### Model Compilation

These options control how a model is compiled for Tenstorrent hardware:

| Option | Description | Default | Environment Variable |
| --- | --- | --- | --- |
| `enable_training` | Enable training mode | `False` | N/A |
| `enable_recompute` | Enable training recompute to reduce memory | `False` | N/A |
| `enable_consteval` | Enable constant evaluation | `True` | N/A |
| `enable_auto_fusing` | Fuse operations when possible | `True` | `PYBUDA_DISABLE_OP_FUSING` |
| `compile_depth` | How far to run the compilation | `CompileDepth.FULL` | `PYBUDA_COMPILE_DEPTH` |
| `default_df_override` | Default data format | `None` | `PYBUDA_DEFAULT_DF` |
| `default_math_fidelity` | Default math precision | `MathFidelity.HiFi3` | N/A |

Sources: [pybuda/pybuda/config.py 147-247](https://github.com/tenstorrent/tt-buda/blob/74755d1f/pybuda/pybuda/config.py#L147-L247)[README.debug.md 4-143](https://github.com/tenstorrent/tt-buda/blob/74755d1f/README.debug.md?plain=1#L4-L143)

### Balancer and Placement

These options control how operations are placed on hardware resources:

| Option | Description | Default | Environment Variable |
| --- | --- | --- | --- |
| `balancer_policy` | Policy for placement decisions | `"default"` | `PYBUDA_BALANCER_POLICY` |
| `scheduler_policy` | Policy for operation scheduling | `"ModuleInputsBFS"` | `PYBUDA_SCHEDULER_POLICY` |
| `chip_placement_policy` | How to order chip IDs | `"MMIO_LAST"` | N/A |
| `enable_t_streaming` | Enable t-streaming optimization | `True` | `PYBUDA_ENABLE_T_STREAMING` |
| `use_interactive_placer` | Use interactive placer | `True` | `PYBUDA_DISABLE_INTERACTIVE_PLACER` |
| `graph_solver_self_cut_type` | Self-cut algorithm for graph solver | `"FastCut"` | `PYBUDA_GRAPHSOLVER_SELF_CUT_TYPE` |

Sources: [pybuda/pybuda/config.py 147-247](https://github.com/tenstorrent/tt-buda/blob/74755d1f/pybuda/pybuda/config.py#L147-L247)[pybuda/csrc/balancer/python_bindings.cpp 173-240](https://github.com/tenstorrent/tt-buda/blob/74755d1f/pybuda/csrc/balancer/python_bindings.cpp#L173-L240)[README.debug.md 4-143](https://github.com/tenstorrent/tt-buda/blob/74755d1f/README.debug.md?plain=1#L4-L143)

### Performance Optimization

These options control performance optimizations:

| Option | Description | Default | Environment Variable |
| --- | --- | --- | --- |
| `amp_level` | Automatic mixed precision level | `None` | `PYBUDA_AMP_LEVEL` |
| `performance_trace` | Level of performance tracing | `PerfTraceLevel.NONE` | `PYBUDA_PERFORMANCE_TRACE` |
| `backend_opt_level` | Backend optimization level | `4` | N/A |
| `enable_enumerate_u_kt` | Search all possible matmul u_kts | `True` | `PYBUDA_DISABLE_ENUMERATE_U_KT` |
| `enable_conv_prestride` | Enable convolution prestriding | `True` | `PYBUDA_PRESTRIDE_DISABLE` |

Sources: [pybuda/pybuda/config.py 147-247](https://github.com/tenstorrent/tt-buda/blob/74755d1f/pybuda/pybuda/config.py#L147-L247)[README.debug.md 4-143](https://github.com/tenstorrent/tt-buda/blob/74755d1f/README.debug.md?plain=1#L4-L143)

### Device Configuration

These options control device-specific settings:

| Option | Description | Default | Environment Variable |
| --- | --- | --- | --- |
| `input_queues_on_host` | Place input queues on host | `True` | `PYBUDA_ENABLE_INPUT_QUEUES_ON_HOST` |
| `output_queues_on_host` | Place output queues on host | `True` | `PYBUDA_ENABLE_OUTPUT_QUEUES_ON_HOST` |
| `harvesting_mask` | List of harvested rows | `0` | N/A |
| `backend_device_descriptor_path` | Device descriptor YAML path | `""` | `PYBUDA_OVERRIDE_DEVICE_YAML` |
| `backend_runtime_params_path` | Backend runtime parameters path | `""` | N/A |

Sources: [pybuda/pybuda/config.py 147-247](https://github.com/tenstorrent/tt-buda/blob/74755d1f/pybuda/pybuda/config.py#L147-L247)[README.debug.md 4-143](https://github.com/tenstorrent/tt-buda/blob/74755d1f/README.debug.md?plain=1#L4-L143)

## Configuration Application Flow

The following diagram shows how configurations are applied throughout the PyBuda system:

Sources: [pybuda/pybuda/config.py 430-565](https://github.com/tenstorrent/tt-buda/blob/74755d1f/pybuda/pybuda/config.py#L430-L565)[pybuda/test/conftest.py 318-386](https://github.com/tenstorrent/tt-buda/blob/74755d1f/pybuda/test/conftest.py#L318-L386)

## Customizing Placement with Overrides

PyBuda provides mechanisms to override default placement decisions through the configuration system:

### Operation Override Example

You can override specific operations using the `balancer_op_override` method:

### Epoch and Chip Breaks

You can control operation placement across epochs and chips:

Sources: [pybuda/pybuda/config.py 347-371](https://github.com/tenstorrent/tt-buda/blob/74755d1f/pybuda/pybuda/config.py#L347-L371)[pybuda/csrc/balancer/legalizer/legalizer.cpp 33-105](https://github.com/tenstorrent/tt-buda/blob/74755d1f/pybuda/csrc/balancer/legalizer/legalizer.cpp#L33-L105)

## Configuration Impact on Tensor Formats

The configuration system significantly influences how tensors are represented and processed:

### Data Format Options

PyBuda supports multiple data formats that can be configured:

| Format | Description | Use Case |
| --- | --- | --- |
| `Float32` | 32-bit floating point | Highest precision, debugging |
| `Float16` | 16-bit floating point | Good balance of precision and performance |
| `Float16_b` | 16-bit floating point (block format) | Optimized memory layout |
| `Bfp8` | 8-bit brain floating point | Lower precision, higher performance |
| `Bfp8_b` | 8-bit brain floating point (block format) | Optimized memory layout |
| `Bfp4`, `Bfp2` | 4-bit/2-bit brain floating point | Very low precision, highest performance |
| `Int8`, `Int32` | Integer formats | Quantized models |

### Automatic Mixed Precision (AMP)

The `amp_level` configuration automatically selects appropriate data formats based on operation types:

| AMP Level | Configuration |
| --- | --- |
| 1 | Matmuls: BFP8_b, Other ops: FP16_b |
| 2 | Matmuls: BFP8, Other ops: FP16, GELU: BFP8 |

Sources: [pybuda/pybuda/config.py 216-222](https://github.com/tenstorrent/tt-buda/blob/74755d1f/pybuda/pybuda/config.py#L216-L222)[pybuda/pybuda/op/eval/common.py 301-333](https://github.com/tenstorrent/tt-buda/blob/74755d1f/pybuda/pybuda/op/eval/common.py#L301-L333)

## Verification Configuration

The `VerifyConfig` class controls how model verification is performed:

Example of configuring verification:

Sources: [pybuda/pybuda/verify/config.py 54-106](https://github.com/tenstorrent/tt-buda/blob/74755d1f/pybuda/pybuda/verify/config.py#L54-L106)[pybuda/pybuda/op/eval/common.py 154-299](https://github.com/tenstorrent/tt-buda/blob/74755d1f/pybuda/pybuda/op/eval/common.py#L154-L299)

## Environment Variable Reference

PyBuda supports numerous environment variables for configuration. Here's a sample of important ones:

| Variable | Description | Default |
| --- | --- | --- |
| `PYBUDA_COMPILE_DEPTH` | Compilation depth level | `full` |
| `PYBUDA_BALANCER_POLICY` | Balancer policy | `default` |
| `PYBUDA_AMP_LEVEL` | Automatic mixed precision level | `0` (disabled) |
| `PYBUDA_ENABLE_T_STREAMING` | Enable t-streaming optimization | `1` (enabled) |
| `PYBUDA_FORCE_VERIFY_ALL` | Verify after every compile stage | `0` (disabled) |
| `PYBUDA_VERIFY_POST_AUTOGRAD_PASSES` | Force verification at post-autograd | `0` (disabled) |
| `PYBUDA_DISABLE_AUTO_TRANSPOSE` | Disable auto-transpose in placement | `1` (disabled) |
| `PYBUDA_DEFAULT_DF` | Default data format | `None` |
| `PYBUDA_DEVMODE` | Enable development mode | `0` (disabled) |
| `PYBUDA_PERFORMANCE_TRACE` | Performance trace level | `none` |

Sources: [README.debug.md 4-143](https://github.com/tenstorrent/tt-buda/blob/74755d1f/README.debug.md?plain=1#L4-L143)[pybuda/pybuda/config.py 251-324](https://github.com/tenstorrent/tt-buda/blob/74755d1f/pybuda/pybuda/config.py#L251-L324)[pybuda/test/conftest.py 318-386](https://github.com/tenstorrent/tt-buda/blob/74755d1f/pybuda/test/conftest.py#L318-L386)

## Configuration Initialization

The configuration system is initialized when PyBuda is imported, and environment variables are processed early in the initialization sequence:

Sources: [pybuda/pybuda/config.py 326-328](https://github.com/tenstorrent/tt-buda/blob/74755d1f/pybuda/pybuda/config.py#L326-L328)[pybuda/test/conftest.py 33-49](https://github.com/tenstorrent/tt-buda/blob/74755d1f/pybuda/test/conftest.py#L33-L49)

## Best Practices

Here are some recommended practices for using the PyBuda Configuration System:

1.   **Use Python API for Persistent Settings**: Use `set_configuration_options()` for settings that should be consistent across runs.

2.   **Use Environment Variables for Development**: Use environment variables for quick iterations during development and debugging.

3.   **Profile First, Then Optimize**: Start with default settings, profile your model, then adjust configuration options based on the results.

4.   **Document Your Configuration**: Keep track of successful configurations for different model types, as they can be reused.

5.   **Balance Precision and Performance**: Use appropriate data formats and AMP levels to balance accuracy needs with performance goals.

6.   **Leverage Policy Types**: Choose the right balancer policy for your model type (NLP, CNN, Ribbon) instead of manual overrides.

7.   **Verify Incrementally**: Use the verification system to check results at different stages during development.

Sources: [pybuda/pybuda/config.py 430-565](https://github.com/tenstorrent/tt-buda/blob/74755d1f/pybuda/pybuda/config.py#L430-L565)

Dismiss
Refresh this wiki

Enter email to refresh
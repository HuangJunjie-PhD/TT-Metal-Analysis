---
project: polaris
github: https://github.com/tenstorrent/polaris
deepwiki: https://deepwiki.com/tenstorrent/polaris/4-workload-models-and-examples))
---

# Workload Models and Examples

Relevant source files
*   [.gitignore](https://github.com/tenstorrent/polaris/blob/ac6ed4a3/.gitignore)
*   [config/ip_workloads.yaml](https://github.com/tenstorrent/polaris/blob/ac6ed4a3/config/ip_workloads.yaml)
*   [tests/test_onnx2graph.py](https://github.com/tenstorrent/polaris/blob/ac6ed4a3/tests/test_onnx2graph.py)
*   [tests/test_simnn.py](https://github.com/tenstorrent/polaris/blob/ac6ed4a3/tests/test_simnn.py)
*   [tests/test_types.py](https://github.com/tenstorrent/polaris/blob/ac6ed4a3/tests/test_types.py)

This document explains the workload model system in Polaris, which defines the neural network architectures and computational workloads that can be simulated. The workload model system provides a standardized interface for defining both custom TTSIM-based neural networks and importing external ONNX models for performance simulation.

For information about how workloads are mapped to specific hardware architectures, see [Workload to Architecture Mapping](https://deepwiki.com/tenstorrent/polaris/2.3.1-workload-to-architecture-mapping). For details about the simulation orchestration process, see [Polaris Main Orchestrator](https://deepwiki.com/tenstorrent/polaris/2.1-polaris-main-orchestrator).

## Workload Model Architecture

The workload model system consists of three main components: configuration definitions, model implementations, and runtime instantiation. Workloads are defined in YAML configuration files and implemented as Python classes that generate computational graphs for simulation.

### Workload Configuration Structure

**Sources:**[config/ip_workloads.yaml 1-50](https://github.com/tenstorrent/polaris/blob/ac6ed4a3/config/ip_workloads.yaml#L1-L50)[tests/test_simnn.py 25-29](https://github.com/tenstorrent/polaris/blob/ac6ed4a3/tests/test_simnn.py#L25-L29)[tests/test_onnx2graph.py 9](https://github.com/tenstorrent/polaris/blob/ac6ed4a3/tests/test_onnx2graph.py#L9-L9)

### Workload Model Interface

All workload models implement a common interface that enables consistent simulation across different model types:

**Sources:**[tests/test_simnn.py 25-35](https://github.com/tenstorrent/polaris/blob/ac6ed4a3/tests/test_simnn.py#L25-L35)

## TTSIM API Workloads

TTSIM workloads are native Python implementations of neural network models that directly use the simulation framework's tensor operations. These workloads provide fine-grained control over the computational graph construction and are optimized for simulation performance.

### Neural Network Model Categories

| Model Category | Examples | Configuration Location |
| --- | --- | --- |
| Object Detection | YOLOv7, YOLOv8 | [config/ip_workloads.yaml 4-24](https://github.com/tenstorrent/polaris/blob/ac6ed4a3/config/ip_workloads.yaml#L4-L24) |
| Image Classification | RESNET50 | [config/ip_workloads.yaml 27-33](https://github.com/tenstorrent/polaris/blob/ac6ed4a3/config/ip_workloads.yaml#L27-L33) |
| Depth Estimation | BEVDepth | [config/ip_workloads.yaml 35-49](https://github.com/tenstorrent/polaris/blob/ac6ed4a3/config/ip_workloads.yaml#L35-L49) |
| Language Models | BasicLLM | [tests/test_simnn.py 11-23](https://github.com/tenstorrent/polaris/blob/ac6ed4a3/tests/test_simnn.py#L11-L23) |

### YOLO Model Configuration

YOLO models demonstrate the external configuration approach where model architectures are defined in remote YAML files:

**Sources:**[config/ip_workloads.yaml 4-24](https://github.com/tenstorrent/polaris/blob/ac6ed4a3/config/ip_workloads.yaml#L4-L24)

### BasicLLM Implementation Example

The `BasicLLM` workload demonstrates the complete implementation pattern for TTSIM models:

**Sources:**[tests/test_simnn.py 11-35](https://github.com/tenstorrent/polaris/blob/ac6ed4a3/tests/test_simnn.py#L11-L35)

## ONNX Integration

ONNX workloads enable simulation of externally trained models by converting ONNX format files into the internal graph representation. This provides compatibility with models from frameworks like PyTorch, TensorFlow, and other ML platforms.

### ONNX to Graph Conversion

**Sources:**[tests/test_onnx2graph.py 9](https://github.com/tenstorrent/polaris/blob/ac6ed4a3/tests/test_onnx2graph.py#L9-L9)

### ONNX Model Example

The test infrastructure demonstrates ONNX model loading with a GPT nano model:

| Component | File Path | Function |
| --- | --- | --- |
| ONNX Converter | `ttsim/front/onnx/onnx2nx.py` | `onnx2graph()` |
| Test Model | `tests/models/onnx/inference/gpt_nano.onnx` | Sample ONNX file |
| Test Function | `tests/test_onnx2graph.py:9` | Integration test |

**Sources:**[tests/test_onnx2graph.py 9](https://github.com/tenstorrent/polaris/blob/ac6ed4a3/tests/test_onnx2graph.py#L9-L9)

## Workload Configuration System

The workload configuration system uses a hierarchical YAML structure that defines workload metadata, module locations, and instance parameters.

### Configuration Schema

**Sources:**[config/ip_workloads.yaml 1-50](https://github.com/tenstorrent/polaris/blob/ac6ed4a3/config/ip_workloads.yaml#L1-L50)

### ResNet50 Configuration Example

ResNet50 demonstrates parameter inheritance and instance customization:

| Parameter | Default Value | Instance Override |
| --- | --- | --- |
| `layers` | `[3,4,6,3]` | - |
| `num_classes` | `1000` | - |
| `use_adaptive_pool` | `true` | - |
| `img_height` | - | `1024` (rn50_b1_hd) |
| `img_width` | - | `1024` (rn50_b1_hd) |
| `img_height` | - | `2828` (rn50_b1_uhd) |
| `img_width` | - | `2828` (rn50_b1_uhd) |

**Sources:**[config/ip_workloads.yaml 27-33](https://github.com/tenstorrent/polaris/blob/ac6ed4a3/config/ip_workloads.yaml#L27-L33)

### BEVDepth Configuration Structure

BEVDepth shows complex configuration with nested YAML files and multiple parameter sources:

**Sources:**[config/ip_workloads.yaml 35-49](https://github.com/tenstorrent/polaris/blob/ac6ed4a3/config/ip_workloads.yaml#L35-L49)

## Type System Integration

The workload model system integrates with the simulation type system to ensure consistent data format handling across different model implementations.

### Data Type Mapping

**Sources:**[tests/test_types.py 11-16](https://github.com/tenstorrent/polaris/blob/ac6ed4a3/tests/test_types.py#L11-L16)

## Model Implementation Guidelines

When implementing new workload models, follow these patterns established by existing models:

### Required Methods

1.   **Constructor**: Initialize model parameters and configuration
2.   **`create_input_tensors()`**: Define input tensor shapes and types
3.   **`__call__()`**: Execute forward pass and fix tensor shapes
4.   **`get_forward_graph()`**: Return computational graph for simulation

### Graph Statistics Validation

All workload models should provide graph statistics for validation:

| Metric | Description | Example (BasicLLM) |
| --- | --- | --- |
| `_ops` | Number of operations | 33 |
| `_tensors` | Total tensor count | 65 |
| `_input_tensors` | Input tensor count | 30 |
| `_input_nodes` | Input node count | 22 |
| `_output_nodes` | Output node count | 1 |
| `_output_tensors` | Output tensor count | 1 |

**Sources:**[tests/test_simnn.py 30-35](https://github.com/tenstorrent/polaris/blob/ac6ed4a3/tests/test_simnn.py#L30-L35)

Dismiss
Refresh this wiki

Enter email to refresh
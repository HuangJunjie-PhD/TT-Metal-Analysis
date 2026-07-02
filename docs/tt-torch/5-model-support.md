---
project: tt-torch
github: https://github.com/tenstorrent/tt-torch
deepwiki: https://deepwiki.com/tenstorrent/tt-torch/5-model-support
---

# Model Support

Relevant source files
*   [tests/models/bloom/test_bloom.py](https://github.com/tenstorrent/tt-torch/blob/f5e3955f/tests/models/bloom/test_bloom.py)
*   [tests/models/codegen/test_codegen.py](https://github.com/tenstorrent/tt-torch/blob/f5e3955f/tests/models/codegen/test_codegen.py)
*   [tests/models/hardnet/test_hardnet.py](https://github.com/tenstorrent/tt-torch/blob/f5e3955f/tests/models/hardnet/test_hardnet.py)
*   [tests/models/musicgen_small/test_musicgen_small.py](https://github.com/tenstorrent/tt-torch/blob/f5e3955f/tests/models/musicgen_small/test_musicgen_small.py)
*   [tests/models/opt/test_opt.py](https://github.com/tenstorrent/tt-torch/blob/f5e3955f/tests/models/opt/test_opt.py)
*   [tests/models/timm/test_timm_image_classification.py](https://github.com/tenstorrent/tt-torch/blob/f5e3955f/tests/models/timm/test_timm_image_classification.py)
*   [tests/models/torchvision/test_torchvision_image_classification.py](https://github.com/tenstorrent/tt-torch/blob/f5e3955f/tests/models/torchvision/test_torchvision_image_classification.py)
*   [tests/models/torchvision/test_torchvision_image_classification_n300.py](https://github.com/tenstorrent/tt-torch/blob/f5e3955f/tests/models/torchvision/test_torchvision_image_classification_n300.py)
*   [tests/models/torchvision/test_torchvision_object_detection.py](https://github.com/tenstorrent/tt-torch/blob/f5e3955f/tests/models/torchvision/test_torchvision_object_detection.py)
*   [tests/models/yolos/test_yolos.py](https://github.com/tenstorrent/tt-torch/blob/f5e3955f/tests/models/yolos/test_yolos.py)
*   [tests/models/yolov5/test_yolov5.py](https://github.com/tenstorrent/tt-torch/blob/f5e3955f/tests/models/yolov5/test_yolov5.py)

## Overview

This page documents the models tested and supported in tt-torch, organized by domain. tt-torch supports a wide range of pre-trained models from popular frameworks including `torchvision`, `timm` (PyTorch Image Models), HuggingFace Transformers, and custom implementations. Models are tested across multiple execution modes (full compilation, op-by-op) and parallelization strategies (single device, data parallel, N300 batch parallel).

Model support is organized into three primary domains:

*   **Computer Vision Models** (page 5.1): Image classification, object detection from `torchvision` and `timm`
*   **Natural Language Processing Models** (page 5.2): Text generation, classification from HuggingFace and custom implementations
*   **Specialized and Experimental Models** (page 5.3): Audio, pose estimation, OCR, and other domain-specific models

All models use the `ModelTester` framework (see page 4.2.1) for standardized testing, verification, and performance measurement. For information about test configuration and CI/CD infrastructure, see pages 4.2 and 4.3.

* * *

## Model Organization and Testing Infrastructure

All models in tt-torch are tested using the `ModelTester` framework, which provides standardized model loading, compilation, execution, and verification. Models are organized in the `tests/models/` directory, with each model or model family in its own subdirectory containing a test file.

### Model Test Structure

**Directory Structure:**

```
tests/models/
├── torchvision/
│   ├── test_torchvision_image_classification.py
│   ├── test_torchvision_object_detection.py
│   └── test_torchvision_image_classification_n300.py
├── timm/
│   └── test_timm_image_classification.py
├── codegen/
│   └── test_codegen.py
├── opt/
│   └── test_opt.py
├── bloom/
│   └── test_bloom.py
├── yolos/
│   └── test_yolos.py
├── musicgen_small/
│   └── test_musicgen_small.py
└── [other models...]
```

**Model Test Lifecycle:**

Each model test defines a `ThisTester` class that inherits from `ModelTester` and implements model-specific loading logic. The test function uses pytest parameterization to run the model across multiple configurations.

**Sources:**[tests/models/torchvision/test_torchvision_image_classification.py 13-34](https://github.com/tenstorrent/tt-torch/blob/f5e3955f/tests/models/torchvision/test_torchvision_image_classification.py#L13-L34)[tests/models/timm/test_timm_image_classification.py 17-41](https://github.com/tenstorrent/tt-torch/blob/f5e3955f/tests/models/timm/test_timm_image_classification.py#L17-L41)[tests/models/codegen/test_codegen.py 13-21](https://github.com/tenstorrent/tt-torch/blob/f5e3955f/tests/models/codegen/test_codegen.py#L13-L21)

* * *

## Supported Model Domains

tt-torch tests models across three primary domains, with comprehensive coverage of popular architectures:

### Domain Summary

| Domain | Model Count | Frameworks | Test Coverage |
| --- | --- | --- | --- |
| **Computer Vision** | 70+ models | torchvision, timm, HuggingFace | Image classification, object detection, segmentation |
| **Natural Language Processing** | 5+ models | HuggingFace Transformers | Text generation, causal LM |
| **Specialized** | 5+ models | Custom implementations | Audio generation, pose estimation, OCR |

### Model Categories by Framework

**Sources:**[tests/models/torchvision/test_torchvision_image_classification.py 37-90](https://github.com/tenstorrent/tt-torch/blob/f5e3955f/tests/models/torchvision/test_torchvision_image_classification.py#L37-L90)[tests/models/torchvision/test_torchvision_object_detection.py 39-44](https://github.com/tenstorrent/tt-torch/blob/f5e3955f/tests/models/torchvision/test_torchvision_object_detection.py#L39-L44)[tests/models/timm/test_timm_image_classification.py 45-60](https://github.com/tenstorrent/tt-torch/blob/f5e3955f/tests/models/timm/test_timm_image_classification.py#L45-L60)[tests/models/codegen/test_codegen.py](https://github.com/tenstorrent/tt-torch/blob/f5e3955f/tests/models/codegen/test_codegen.py)[tests/models/opt/test_opt.py](https://github.com/tenstorrent/tt-torch/blob/f5e3955f/tests/models/opt/test_opt.py)[tests/models/bloom/test_bloom.py](https://github.com/tenstorrent/tt-torch/blob/f5e3955f/tests/models/bloom/test_bloom.py)[tests/models/musicgen_small/test_musicgen_small.py](https://github.com/tenstorrent/tt-torch/blob/f5e3955f/tests/models/musicgen_small/test_musicgen_small.py)

* * *

## Test Execution Modes

Models are tested across multiple execution configurations through pytest parameterization:

### Execution Mode Matrix

| Parameter | Values | Description |
| --- | --- | --- |
| `mode` | `eval`, `train` | Model execution mode (train mostly skipped) |
| `op_by_op` | `None`, `STABLEHLO`, `TORCH` | Compilation depth (full, op-by-op StableHLO, op-by-op Torch) |
| `data_parallel_mode` | `False`, `True` | Single device vs multi-device data parallelism |

**Test Configuration Flow:**

**Example Parameterization:**

[tests/models/torchvision/test_torchvision_image_classification.py 93-109](https://github.com/tenstorrent/tt-torch/blob/f5e3955f/tests/models/torchvision/test_torchvision_image_classification.py#L93-L109)

The test function accepts all parameter combinations and configures `CompilerConfig` accordingly. Op-by-op mode is incompatible with data parallel and will skip the test.

**Sources:**[tests/models/torchvision/test_torchvision_image_classification.py 93-122](https://github.com/tenstorrent/tt-torch/blob/f5e3955f/tests/models/torchvision/test_torchvision_image_classification.py#L93-L122)[tests/models/timm/test_timm_image_classification.py 63-90](https://github.com/tenstorrent/tt-torch/blob/f5e3955f/tests/models/timm/test_timm_image_classification.py#L63-L90)[tests/models/hardnet/test_hardnet.py 36-48](https://github.com/tenstorrent/tt-torch/blob/f5e3955f/tests/models/hardnet/test_hardnet.py#L36-L48)

* * *

## Verification and Accuracy Metrics

All model tests verify correctness by comparing device outputs against PyTorch CPU golden references using two primary metrics:

### Verification Metrics

| Metric | Description | Typical Range | Usage |
| --- | --- | --- | --- |
| **PCC** (Pearson Correlation Coefficient) | Linear correlation between device and golden outputs | 0.95 - 0.99 | Primary correctness metric |
| **ATOL** (Absolute Tolerance) | Maximum absolute difference | 0.01 - 0.05 | Secondary metric, often disabled |

### PCC Requirements by Domain

| Domain | Typical PCC Threshold | Notes |
| --- | --- | --- |
| Image Classification (torchvision) | 0.96 - 0.99 | Most require ≥0.96 |
| Image Classification (timm) | 0.95 - 0.99 | Some models only 0.95 |
| Object Detection | 0.98 | Higher precision needed |
| NLP Text Generation | 0.98 - 0.99 | High accuracy for token prediction |
| Audio/Specialized | Varies | Often disabled during development |

**PCC Configuration Example:**

[tests/models/timm/test_timm_image_classification.py 111-128](https://github.com/tenstorrent/tt-torch/blob/f5e3955f/tests/models/timm/test_timm_image_classification.py#L111-L128)

Models specify different PCC thresholds based on their complexity and numeric stability. The `ModelTester` framework automatically compares outputs and fails the test if PCC falls below the threshold.

**ATOL Configuration:**

[tests/models/hardnet/test_hardnet.py 56-66](https://github.com/tenstorrent/tt-torch/blob/f5e3955f/tests/models/hardnet/test_hardnet.py#L56-L66)

ATOL verification is often disabled (`assert_atol=False`) for complex models due to expected bfloat16 precision differences.

**Sources:**[tests/models/timm/test_timm_image_classification.py 111-148](https://github.com/tenstorrent/tt-torch/blob/f5e3955f/tests/models/timm/test_timm_image_classification.py#L111-L148)[tests/models/torchvision/test_torchvision_image_classification.py 124-139](https://github.com/tenstorrent/tt-torch/blob/f5e3955f/tests/models/torchvision/test_torchvision_image_classification.py#L124-L139)[tests/models/hardnet/test_hardnet.py 53-68](https://github.com/tenstorrent/tt-torch/blob/f5e3955f/tests/models/hardnet/test_hardnet.py#L53-L68)

* * *

## Model Loading Patterns

Models in tt-torch follow one of two loading patterns depending on their source:

### Pattern 1: Direct Framework Loading

Models from `torchvision` and `timm` load directly from their respective frameworks:

**torchvision Example:**

[tests/models/torchvision/test_torchvision_image_classification.py 20-33](https://github.com/tenstorrent/tt-torch/blob/f5e3955f/tests/models/torchvision/test_torchvision_image_classification.py#L20-L33)

`def _load_model(self):    model_name, weights_name = self.model_info_tuple    self.weights = getattr(models, weights_name).DEFAULT    model = models.get_model(model_name, weights=self.weights).to(torch.bfloat16)    return model def _load_inputs(self):    preprocess = self.weights.transforms()    image_file = get_file("http://images.cocodataset.org/val2017/000000039769.jpg")    image = Image.open(str(image_file))    img_t = preprocess(image)    return torch.unsqueeze(img_t, 0).to(torch.bfloat16)`
### Pattern 2: ModelLoader Integration

Models from `third_party.tt_forge_models` use the `ModelLoader` abstraction:

**HardNet Example:**

[tests/models/hardnet/test_hardnet.py 16-22](https://github.com/tenstorrent/tt-torch/blob/f5e3955f/tests/models/hardnet/test_hardnet.py#L16-L22)

`class ThisTester(ModelTester):    def _load_model(self):        return self.loader.load_model(dtype_override=torch.bfloat16)        def _load_inputs(self):        return self.loader.load_inputs(dtype_override=torch.bfloat16)`
[tests/models/hardnet/test_hardnet.py 50-52](https://github.com/tenstorrent/tt-torch/blob/f5e3955f/tests/models/hardnet/test_hardnet.py#L50-L52)

`loader = ModelLoader(variant=None)model_info = loader.get_model_info(variant=None)`
The `ModelLoader` provides:

*   `load_model()`: Returns the model instance with optional dtype override
*   `load_inputs()`: Returns preprocessed inputs for testing
*   `get_model_info()`: Returns metadata (name, variant, etc.)

### Asset Management

Both patterns use `get_file()` from `third_party.tt_forge_models.tools.utils` for downloading and caching test assets (images, weights):

[tests/models/torchvision/test_torchvision_image_classification.py 29](https://github.com/tenstorrent/tt-torch/blob/f5e3955f/tests/models/torchvision/test_torchvision_image_classification.py#L29-L29)

`image_file = get_file("http://images.cocodataset.org/val2017/000000039769.jpg")`
**Sources:**[tests/models/torchvision/test_torchvision_image_classification.py 20-33](https://github.com/tenstorrent/tt-torch/blob/f5e3955f/tests/models/torchvision/test_torchvision_image_classification.py#L20-L33)[tests/models/timm/test_timm_image_classification.py 18-41](https://github.com/tenstorrent/tt-torch/blob/f5e3955f/tests/models/timm/test_timm_image_classification.py#L18-L41)[tests/models/hardnet/test_hardnet.py 16-22](https://github.com/tenstorrent/tt-torch/blob/f5e3955f/tests/models/hardnet/test_hardnet.py#L16-L22)[tests/models/codegen/test_codegen.py 13-20](https://github.com/tenstorrent/tt-torch/blob/f5e3955f/tests/models/codegen/test_codegen.py#L13-L20)

* * *

## Hardware-Specific Configurations

Different hardware platforms require specific test configurations:

### Single Device vs N300 Multi-Chip

**Single Device (Wormhole/Blackhole):**

[tests/models/torchvision/test_torchvision_image_classification.py 113-122](https://github.com/tenstorrent/tt-torch/blob/f5e3955f/tests/models/torchvision/test_torchvision_image_classification.py#L113-L122)

Standard configuration with optional data parallelism:

*   `data_parallel_mode=False`: Single device execution
*   `data_parallel_mode=True`: Data parallel across multiple devices

**N300 Multi-Chip Configuration:**

[tests/models/torchvision/test_torchvision_image_classification_n300.py 110-119](https://github.com/tenstorrent/tt-torch/blob/f5e3955f/tests/models/torchvision/test_torchvision_image_classification_n300.py#L110-L119)

`cc = CompilerConfig()cc.enable_consteval = Truecc.consteval_parameters = Truecc.automatic_parallelization = True  # Enable automatic graph splittingcc.mesh_shape = [1, 2]               # 1x2 mesh topologycc.dump_debug = True                 # Generate debug artifacts`
N300 tests use batch size 4 with 1x2 mesh for automatic batch parallelization across two chips.

### Backend Selection

Some models require specific backends due to compatibility issues:

[tests/models/timm/test_timm_image_classification.py 130-134](https://github.com/tenstorrent/tt-torch/blob/f5e3955f/tests/models/timm/test_timm_image_classification.py#L130-L134)

`# Most models use tt-experimental (XLA-based)backend = "tt-experimental" # Exception for known issuesif model_name == "inception_v4.tf_in1k":    backend = "tt-legacy"  # Fall back to legacy Dynamo backend`
[tests/models/torchvision/test_torchvision_image_classification_n300.py 126](https://github.com/tenstorrent/tt-torch/blob/f5e3955f/tests/models/torchvision/test_torchvision_image_classification_n300.py#L126-L126)

`backend = "tt" if model_name in ["regnet_x_32gf"] else "tt-experimental"`
**Sources:**[tests/models/torchvision/test_torchvision_image_classification.py 106-122](https://github.com/tenstorrent/tt-torch/blob/f5e3955f/tests/models/torchvision/test_torchvision_image_classification.py#L106-L122)[tests/models/torchvision/test_torchvision_image_classification_n300.py 110-126](https://github.com/tenstorrent/tt-torch/blob/f5e3955f/tests/models/torchvision/test_torchvision_image_classification_n300.py#L110-L126)[tests/models/timm/test_timm_image_classification.py 130-147](https://github.com/tenstorrent/tt-torch/blob/f5e3955f/tests/models/timm/test_timm_image_classification.py#L130-L147)

* * *

## Known Limitations and Workarounds

### Training Mode

Most model tests skip training mode execution:

[tests/models/torchvision/test_torchvision_image_classification.py 110-111](https://github.com/tenstorrent/tt-torch/blob/f5e3955f/tests/models/torchvision/test_torchvision_image_classification.py#L110-L111)

`if mode == "train":    pytest.skip()`
**Sources:**[tests/models/torchvision/test_torchvision_image_classification.py 110-111](https://github.com/tenstorrent/tt-torch/blob/f5e3955f/tests/models/torchvision/test_torchvision_image_classification.py#L110-L111)[tests/models/timm/test_timm_image_classification.py 79-80](https://github.com/tenstorrent/tt-torch/blob/f5e3955f/tests/models/timm/test_timm_image_classification.py#L79-L80)

### Op-by-Op and Data Parallel Mutual Exclusion

Op-by-op execution is not supported with data parallel mode:

[tests/models/torchvision/test_torchvision_image_classification.py 116-118](https://github.com/tenstorrent/tt-torch/blob/f5e3955f/tests/models/torchvision/test_torchvision_image_classification.py#L116-L118)

`if op_by_op:    if data_parallel_mode == "data_parallel":        pytest.skip("Op-by-op not supported in data parallel mode")`
**Sources:**[tests/models/torchvision/test_torchvision_image_classification.py 116-118](https://github.com/tenstorrent/tt-torch/blob/f5e3955f/tests/models/torchvision/test_torchvision_image_classification.py#L116-L118)[tests/models/timm/test_timm_image_classification.py 85-87](https://github.com/tenstorrent/tt-torch/blob/f5e3955f/tests/models/timm/test_timm_image_classification.py#L85-L87)

### BFloat16 Casting

All models are converted to bfloat16 for execution:

[tests/models/torchvision/test_torchvision_image_classification.py 23](https://github.com/tenstorrent/tt-torch/blob/f5e3955f/tests/models/torchvision/test_torchvision_image_classification.py#L23-L23)

`model = models.get_model(model_name, weights=self.weights).to(torch.bfloat16)`
This is required for device execution but may introduce precision differences compared to fp32 PyTorch execution.

**Sources:**[tests/models/torchvision/test_torchvision_image_classification.py 23](https://github.com/tenstorrent/tt-torch/blob/f5e3955f/tests/models/torchvision/test_torchvision_image_classification.py#L23-L23)[tests/models/timm/test_timm_image_classification.py 22](https://github.com/tenstorrent/tt-torch/blob/f5e3955f/tests/models/timm/test_timm_image_classification.py#L22-L22)[tests/models/hardnet/test_hardnet.py 18](https://github.com/tenstorrent/tt-torch/blob/f5e3955f/tests/models/hardnet/test_hardnet.py#L18-L18)

* * *

## Output Verification and Debugging

### Print Result Functions

Tests define custom `print_result()` functions to decode and display model outputs:

[tests/models/torchvision/test_torchvision_image_classification.py 142-148](https://github.com/tenstorrent/tt-torch/blob/f5e3955f/tests/models/torchvision/test_torchvision_image_classification.py#L142-L148)

`def print_result(result):    _, indices = torch.topk(result, 5)    print(f"Model: {model_name} | Top 5 predictions: {indices[0].tolist()}") if mode == "eval":    ModelTester.print_outputs(results, data_parallel_mode, print_result)`
**Sources:**[tests/models/torchvision/test_torchvision_image_classification.py 142-148](https://github.com/tenstorrent/tt-torch/blob/f5e3955f/tests/models/torchvision/test_torchvision_image_classification.py#L142-L148)[tests/models/yolos/test_yolos.py 94-104](https://github.com/tenstorrent/tt-torch/blob/f5e3955f/tests/models/yolos/test_yolos.py#L94-L104)

### Disabled Verification

Some models have verification temporarily disabled during bring-up:

[tests/models/mgp-str-base/test_mgp_str_base.py 51-52](https://github.com/tenstorrent/tt-torch/blob/f5e3955f/tests/models/mgp-str-base/test_mgp_str_base.py#L51-L52)

`# TODO Enable checking - https://github.com/tenstorrent/tt-torch/issues/552disable_checking = True`
**Sources:**[tests/models/mgp-str-base/test_mgp_str_base.py 51-52](https://github.com/tenstorrent/tt-torch/blob/f5e3955f/tests/models/mgp-str-base/test_mgp_str_base.py#L51-L52)[tests/models/torchvision/test_torchvision_object_detection.py 79-83](https://github.com/tenstorrent/tt-torch/blob/f5e3955f/tests/models/torchvision/test_torchvision_object_detection.py#L79-L83)

This wiki is featured in the [repository](https://github.com/tenstorrent/tt-torch/blob/main/README.md)

Dismiss
Refresh this wiki

Enter email to refresh
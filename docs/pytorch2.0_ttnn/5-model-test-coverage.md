---
project: pytorch2.0_ttnn
github: https://github.com/tenstorrent/pytorch2.0_ttnn
deepwiki: https://deepwiki.com/tenstorrent/pytorch2.0_ttnn/5-model-test-coverage
---

# Model Test Coverage

Relevant source files
*   [tests/lowering/creation/test_masked_fill.py](https://github.com/tenstorrent/pytorch2.0_ttnn/blob/7ba2867e/tests/lowering/creation/test_masked_fill.py)
*   [tests/lowering/eltwise/unary/test_tanh.py](https://github.com/tenstorrent/pytorch2.0_ttnn/blob/7ba2867e/tests/lowering/eltwise/unary/test_tanh.py)
*   [tests/lowering/tensor_manipulation/test_squeeze.py](https://github.com/tenstorrent/pytorch2.0_ttnn/blob/7ba2867e/tests/lowering/tensor_manipulation/test_squeeze.py)
*   [tests/models/autoencoder_linear/test_autoencoder_linear.py](https://github.com/tenstorrent/pytorch2.0_ttnn/blob/7ba2867e/tests/models/autoencoder_linear/test_autoencoder_linear.py)
*   [tests/models/beit/test_beit_image_classification.py](https://github.com/tenstorrent/pytorch2.0_ttnn/blob/7ba2867e/tests/models/beit/test_beit_image_classification.py)
*   [tests/models/bert/test_bert.py](https://github.com/tenstorrent/pytorch2.0_ttnn/blob/7ba2867e/tests/models/bert/test_bert.py)
*   [tests/models/bloom/test_bloom.py](https://github.com/tenstorrent/pytorch2.0_ttnn/blob/7ba2867e/tests/models/bloom/test_bloom.py)
*   [tests/models/deit/test_deit.py](https://github.com/tenstorrent/pytorch2.0_ttnn/blob/7ba2867e/tests/models/deit/test_deit.py)
*   [tests/models/detr/test_detr.py](https://github.com/tenstorrent/pytorch2.0_ttnn/blob/7ba2867e/tests/models/detr/test_detr.py)
*   [tests/models/falcon/test_falcon.py](https://github.com/tenstorrent/pytorch2.0_ttnn/blob/7ba2867e/tests/models/falcon/test_falcon.py)
*   [tests/models/gpt2/test_gpt2.py](https://github.com/tenstorrent/pytorch2.0_ttnn/blob/7ba2867e/tests/models/gpt2/test_gpt2.py)
*   [tests/models/llama/test_llama.py](https://github.com/tenstorrent/pytorch2.0_ttnn/blob/7ba2867e/tests/models/llama/test_llama.py)
*   [tests/models/mnist/test_mnist.py](https://github.com/tenstorrent/pytorch2.0_ttnn/blob/7ba2867e/tests/models/mnist/test_mnist.py)
*   [tests/models/openpose/test_openpose.py](https://github.com/tenstorrent/pytorch2.0_ttnn/blob/7ba2867e/tests/models/openpose/test_openpose.py)
*   [tests/models/openpose/test_openpose_v2.py](https://github.com/tenstorrent/pytorch2.0_ttnn/blob/7ba2867e/tests/models/openpose/test_openpose_v2.py)
*   [tests/models/perceiver_io/test_perceiver_io.py](https://github.com/tenstorrent/pytorch2.0_ttnn/blob/7ba2867e/tests/models/perceiver_io/test_perceiver_io.py)
*   [tests/models/resnet/test_resnet.py](https://github.com/tenstorrent/pytorch2.0_ttnn/blob/7ba2867e/tests/models/resnet/test_resnet.py)
*   [tests/models/resnet50/test_resnet50.py](https://github.com/tenstorrent/pytorch2.0_ttnn/blob/7ba2867e/tests/models/resnet50/test_resnet50.py)
*   [tests/models/speecht5_tts/test_speecht5_tts.py](https://github.com/tenstorrent/pytorch2.0_ttnn/blob/7ba2867e/tests/models/speecht5_tts/test_speecht5_tts.py)
*   [tests/models/torchvision/test_torchvision_image_classification.py](https://github.com/tenstorrent/pytorch2.0_ttnn/blob/7ba2867e/tests/models/torchvision/test_torchvision_image_classification.py)
*   [tests/models/vilt/test_vilt.py](https://github.com/tenstorrent/pytorch2.0_ttnn/blob/7ba2867e/tests/models/vilt/test_vilt.py)
*   [tests/models/xglm/test_xglm.py](https://github.com/tenstorrent/pytorch2.0_ttnn/blob/7ba2867e/tests/models/xglm/test_xglm.py)
*   [tests/models/yolos/test_yolos.py](https://github.com/tenstorrent/pytorch2.0_ttnn/blob/7ba2867e/tests/models/yolos/test_yolos.py)
*   [tests/models/yolov5/test_yolov5.py](https://github.com/tenstorrent/pytorch2.0_ttnn/blob/7ba2867e/tests/models/yolov5/test_yolov5.py)
*   [torch_ttnn/passes/analysis/graph_module_analysis_pass.py](https://github.com/tenstorrent/pytorch2.0_ttnn/blob/7ba2867e/torch_ttnn/passes/analysis/graph_module_analysis_pass.py)
*   [torch_ttnn/passes/lowering/load_once_pass.py](https://github.com/tenstorrent/pytorch2.0_ttnn/blob/7ba2867e/torch_ttnn/passes/lowering/load_once_pass.py)

## Purpose and Scope

This document provides an overview of the comprehensive model test suite in the pytorch2.0_ttnn repository. The test suite validates end-to-end model compilation and execution across computer vision, natural language processing, and multi-modal domains. Each model test verifies that the compilation pipeline can successfully transform a complete PyTorch model into TTNN operations and execute it on Tenstorrent hardware.

For detailed information about:

*   The testing framework infrastructure, see [Testing Framework and Infrastructure](https://deepwiki.com/tenstorrent/pytorch2.0_ttnn/4-testing-framework-and-infrastructure)
*   The ModelTester base class pattern, see [ModelTester Framework](https://deepwiki.com/tenstorrent/pytorch2.0_ttnn/4.2-modeltester-framework)
*   Computer vision specific tests, see [Computer Vision Model Tests](https://deepwiki.com/tenstorrent/pytorch2.0_ttnn/5.1-computer-vision-model-tests)
*   NLP specific tests, see [Natural Language Processing Model Tests](https://deepwiki.com/tenstorrent/pytorch2.0_ttnn/5.2-natural-language-processing-model-tests)
*   Multi-modal specific tests, see [Multi-Modal and Speech Model Tests](https://deepwiki.com/tenstorrent/pytorch2.0_ttnn/5.3-multi-modal-and-speech-model-tests)
*   Operation-level lowering tests, see [Operation Lowering Test Coverage](https://deepwiki.com/tenstorrent/pytorch2.0_ttnn/6-operation-lowering-test-coverage)

## Test Suite Organization

The model test suite is organized in the `tests/models/` directory, with each model residing in its own subdirectory. Tests are categorized by model architecture and domain.

**Test Directory Structure**

| Category | Example Models | Test Count |
| --- | --- | --- |
| Computer Vision | MNIST, ResNet, YOLOv5, DETR, torchvision models | 40+ |
| NLP | BERT, GPT-2, Llama, Falcon, Bloom | 10+ |
| Multi-Modal | ViLT, BEiT, DeiT | 5+ |
| Speech | SpeechT5 | 1+ |
| Specialized | Autoencoders, Perceiver IO | 5+ |

Sources: [tests/models/mnist/test_mnist.py](https://github.com/tenstorrent/pytorch2.0_ttnn/blob/7ba2867e/tests/models/mnist/test_mnist.py)[tests/models/resnet/test_resnet.py](https://github.com/tenstorrent/pytorch2.0_ttnn/blob/7ba2867e/tests/models/resnet/test_resnet.py)[tests/models/bert/test_bert.py](https://github.com/tenstorrent/pytorch2.0_ttnn/blob/7ba2867e/tests/models/bert/test_bert.py)[tests/models/falcon/test_falcon.py](https://github.com/tenstorrent/pytorch2.0_ttnn/blob/7ba2867e/tests/models/falcon/test_falcon.py)[tests/models/vilt/test_vilt.py](https://github.com/tenstorrent/pytorch2.0_ttnn/blob/7ba2867e/tests/models/vilt/test_vilt.py)

## Common Test Pattern Using ModelTester

All model tests follow a standardized pattern using the `ModelTester` base class. Each test implements a `ThisTester` subclass that defines model loading and input preparation logic.

**Standard Test Structure**

Each model test follows this pattern:

1.   **Test Module Structure**[tests/models/mnist/test_mnist.py 41-69](https://github.com/tenstorrent/pytorch2.0_ttnn/blob/7ba2867e/tests/models/mnist/test_mnist.py#L41-L69)

    *   `ThisTester` class inheriting from `ModelTester`
    *   `_load_model()` method: instantiates and returns the model
    *   `_load_inputs()` method: creates sample inputs for the specified batch size
    *   Test function with pytest parametrization

2.   **Model Loading**[tests/models/resnet/test_resnet.py 11-14](https://github.com/tenstorrent/pytorch2.0_ttnn/blob/7ba2867e/tests/models/resnet/test_resnet.py#L11-L14)

    *   Downloads pretrained weights or creates model instance
    *   Converts model to `torch.bfloat16` precision
    *   Returns the model instance

3.   **Input Preparation**[tests/models/bert/test_bert.py 23-49](https://github.com/tenstorrent/pytorch2.0_ttnn/blob/7ba2867e/tests/models/bert/test_bert.py#L23-L49)

    *   Downloads or generates sample data
    *   Applies model-specific preprocessing (tokenization, transforms)
    *   Handles batch size expansion using `repeat_inputs` utility
    *   Converts inputs to `torch.bfloat16` precision

4.   **Test Execution**[tests/models/mnist/test_mnist.py 56-68](https://github.com/tenstorrent/pytorch2.0_ttnn/blob/7ba2867e/tests/models/mnist/test_mnist.py#L56-L68)

    *   Parametrizes mode (train/eval) and other configurations
    *   Records test metadata via `record_property`
    *   Invokes `tester.test_model()` which triggers compilation
    *   Stores results in `record_property("torch_ttnn", (tester, results))`

Sources: [tests/models/mnist/test_mnist.py 41-69](https://github.com/tenstorrent/pytorch2.0_ttnn/blob/7ba2867e/tests/models/mnist/test_mnist.py#L41-L69)[tests/models/resnet/test_resnet.py 10-20](https://github.com/tenstorrent/pytorch2.0_ttnn/blob/7ba2867e/tests/models/resnet/test_resnet.py#L10-L20)[tests/models/bert/test_bert.py 15-49](https://github.com/tenstorrent/pytorch2.0_ttnn/blob/7ba2867e/tests/models/bert/test_bert.py#L15-L49)

## Model Test Implementation Patterns

### Pattern 1: Simple Model Tests

Basic tests for models without complex input preprocessing requirements.

**Example: MNIST**[tests/models/mnist/test_mnist.py](https://github.com/tenstorrent/pytorch2.0_ttnn/blob/7ba2867e/tests/models/mnist/test_mnist.py)

*   Custom model definition inline
*   Simple torchvision dataset loading
*   Single tensor input

**Example: ResNet**[tests/models/resnet/test_resnet.py](https://github.com/tenstorrent/pytorch2.0_ttnn/blob/7ba2867e/tests/models/resnet/test_resnet.py)

*   Direct torchvision model loading
*   Random tensor input generation
*   Parametrized train/eval modes

### Pattern 2: Transformer Model Tests

Tests for transformer-based models requiring tokenization.

**Example: BERT**[tests/models/bert/test_bert.py](https://github.com/tenstorrent/pytorch2.0_ttnn/blob/7ba2867e/tests/models/bert/test_bert.py)

*   Uses `AutoTokenizer` for text preprocessing
*   Loads custom input data from JSON files
*   Dictionary inputs with `input_ids` and `attention_mask`
*   Batch size handling with question-context pairs

**Example: Llama**[tests/models/llama/test_llama.py 11-18](https://github.com/tenstorrent/pytorch2.0_ttnn/blob/7ba2867e/tests/models/llama/test_llama.py#L11-L18)

*   Wraps model in custom `LlamaModelModule` to expose specific outputs
*   Uses `encode_plus` with padding and truncation
*   Multiple model variants via parametrization

### Pattern 3: Vision Transformer Tests

Tests for vision models with specialized preprocessors.

**Example: Torchvision Image Classification**[tests/models/torchvision/test_torchvision_image_classification.py](https://github.com/tenstorrent/pytorch2.0_ttnn/blob/7ba2867e/tests/models/torchvision/test_torchvision_image_classification.py)

*   Extensive parametrization for 40+ models
*   Uses weight-specific preprocessing transforms
*   Custom `ThisTester.__init__` to accept model_info tuple

**Example: DETR**[tests/models/detr/test_detr.py](https://github.com/tenstorrent/pytorch2.0_ttnn/blob/7ba2867e/tests/models/detr/test_detr.py)

*   Custom normalization based on image statistics
*   Object detection output structure
*   Hub model loading with `skip_validation=True`

### Pattern 4: Multi-Modal Tests

Tests for models accepting multiple input modalities.

**Example: ViLT**[tests/models/vilt/test_vilt.py 14-29](https://github.com/tenstorrent/pytorch2.0_ttnn/blob/7ba2867e/tests/models/vilt/test_vilt.py#L14-L29)

*   Uses `ViltProcessor` for joint image-text processing
*   Question-answering task setup
*   Both image and text inputs

**Example: SpeechT5**[tests/models/speecht5_tts/test_speecht5_tts.py 13-35](https://github.com/tenstorrent/pytorch2.0_ttnn/blob/7ba2867e/tests/models/speecht5_tts/test_speecht5_tts.py#L13-L35)

*   Tests model's `generate_speech` method directly
*   Loads speaker embeddings from external dataset
*   Requires vocoder model for synthesis
*   Custom `set_model_eval` override

### Pattern 5: Tests with External Dependencies

Tests requiring special dependency management or setup.

**Example: YOLOv5**[tests/models/yolov5/test_yolov5.py 36-99](https://github.com/tenstorrent/pytorch2.0_ttnn/blob/7ba2867e/tests/models/yolov5/test_yolov5.py#L36-L99)

*   `setup_module` function for dependency installation
*   OpenCV headless variant to avoid GPU requirements
*   `teardown_module` for cleanup of downloaded weights
*   Uses `@pytest.mark.usefixtures("manage_dependencies")`

**Example: OpenPose**[tests/models/openpose/test_openpose.py 12-44](https://github.com/tenstorrent/pytorch2.0_ttnn/blob/7ba2867e/tests/models/openpose/test_openpose.py#L12-L44)

*   Declares dependencies via module-level variable
*   Uses controlnet_aux package
*   Currently marked as skipped due to failures

Sources: [tests/models/mnist/test_mnist.py](https://github.com/tenstorrent/pytorch2.0_ttnn/blob/7ba2867e/tests/models/mnist/test_mnist.py)[tests/models/bert/test_bert.py](https://github.com/tenstorrent/pytorch2.0_ttnn/blob/7ba2867e/tests/models/bert/test_bert.py)[tests/models/llama/test_llama.py](https://github.com/tenstorrent/pytorch2.0_ttnn/blob/7ba2867e/tests/models/llama/test_llama.py)[tests/models/torchvision/test_torchvision_image_classification.py](https://github.com/tenstorrent/pytorch2.0_ttnn/blob/7ba2867e/tests/models/torchvision/test_torchvision_image_classification.py)[tests/models/vilt/test_vilt.py](https://github.com/tenstorrent/pytorch2.0_ttnn/blob/7ba2867e/tests/models/vilt/test_vilt.py)[tests/models/yolov5/test_yolov5.py](https://github.com/tenstorrent/pytorch2.0_ttnn/blob/7ba2867e/tests/models/yolov5/test_yolov5.py)

## Test Execution and Markers

Model tests use pytest markers to control execution and classify test status.

### Common Pytest Markers

| Marker | Purpose | Example |
| --- | --- | --- |
| `@pytest.mark.converted_end_to_end` | Marks tests that fully convert to TTNN | [tests/models/mnist/test_mnist.py 58](https://github.com/tenstorrent/pytorch2.0_ttnn/blob/7ba2867e/tests/models/mnist/test_mnist.py#L58-L58) |
| `@pytest.mark.e2e_with_native_integration` | Tests using native TTNN integration | [tests/models/bert/test_bert.py 61](https://github.com/tenstorrent/pytorch2.0_ttnn/blob/7ba2867e/tests/models/bert/test_bert.py#L61-L61) |
| `@pytest.mark.compilation_xfail` | Expected compilation failures with reason | [tests/models/yolov5/test_yolov5.py 106](https://github.com/tenstorrent/pytorch2.0_ttnn/blob/7ba2867e/tests/models/yolov5/test_yolov5.py#L106-L106) |
| `@pytest.mark.skip` | Skipped tests with reason | [tests/models/openpose/test_openpose.py 35](https://github.com/tenstorrent/pytorch2.0_ttnn/blob/7ba2867e/tests/models/openpose/test_openpose.py#L35-L35) |
| `@pytest.mark.parametrize` | Parametrized test variations | [tests/models/llama/test_llama.py 44-54](https://github.com/tenstorrent/pytorch2.0_ttnn/blob/7ba2867e/tests/models/llama/test_llama.py#L44-L54) |
| `@pytest.mark.usefixtures` | Declares fixture dependencies | [tests/models/yolov5/test_yolov5.py 107](https://github.com/tenstorrent/pytorch2.0_ttnn/blob/7ba2867e/tests/models/yolov5/test_yolov5.py#L107-L107) |

### Parametrization Patterns

**Mode Parametrization**

Most tests parametrize over training and evaluation modes:

Some mark only specific modes as converted:

**Batch Size Parametrization**[tests/models/llama/test_llama.py 48-50](https://github.com/tenstorrent/pytorch2.0_ttnn/blob/7ba2867e/tests/models/llama/test_llama.py#L48-L50)

*   Large language models often test with batch_size=32
*   Vision models vary between 1 and 16

**Model Variant Parametrization**[tests/models/llama/test_llama.py 52-54](https://github.com/tenstorrent/pytorch2.0_ttnn/blob/7ba2867e/tests/models/llama/test_llama.py#L52-L54)

*   Single test function covers multiple model sizes
*   Examples: Llama-7B, Llama-3.2-1B, Llama-3.2-3B, Llama-3.1-8B

**Multi-Model Tests**[tests/models/torchvision/test_torchvision_image_classification.py 40-103](https://github.com/tenstorrent/pytorch2.0_ttnn/blob/7ba2867e/tests/models/torchvision/test_torchvision_image_classification.py#L40-L103)

*   Parametrizes over 40+ torchvision models
*   Each model-mode combination is a separate test case

Sources: [tests/models/mnist/test_mnist.py 56-59](https://github.com/tenstorrent/pytorch2.0_ttnn/blob/7ba2867e/tests/models/mnist/test_mnist.py#L56-L59)[tests/models/llama/test_llama.py 44-54](https://github.com/tenstorrent/pytorch2.0_ttnn/blob/7ba2867e/tests/models/llama/test_llama.py#L44-L54)[tests/models/torchvision/test_torchvision_image_classification.py 40-103](https://github.com/tenstorrent/pytorch2.0_ttnn/blob/7ba2867e/tests/models/torchvision/test_torchvision_image_classification.py#L40-L103)[tests/models/yolov5/test_yolov5.py 102-116](https://github.com/tenstorrent/pytorch2.0_ttnn/blob/7ba2867e/tests/models/yolov5/test_yolov5.py#L102-L116)

## Input Handling and Data Loading

### Input Preprocessing Strategies

### Data Source Categories

**1. Downloaded Images**[tests/models/resnet50/test_resnet50.py 22-33](https://github.com/tenstorrent/pytorch2.0_ttnn/blob/7ba2867e/tests/models/resnet50/test_resnet50.py#L22-L33)

*   URL-based image loading via `requests`
*   Common datasets: COCO validation images
*   Preprocessing with model-specific transforms

**2. Downloaded Datasets**[tests/models/mnist/test_mnist.py 47-53](https://github.com/tenstorrent/pytorch2.0_ttnn/blob/7ba2867e/tests/models/mnist/test_mnist.py#L47-L53)

*   Torchvision datasets (MNIST, CIFAR, etc.)
*   HuggingFace datasets for embeddings
*   DataLoader integration for batch sampling

**3. Tokenized Text**[tests/models/falcon/test_falcon.py 20-25](https://github.com/tenstorrent/pytorch2.0_ttnn/blob/7ba2867e/tests/models/falcon/test_falcon.py#L20-L25)

*   `AutoTokenizer.from_pretrained()`
*   Padding strategies (left/right/max_length)
*   Return format: dictionaries with `input_ids`, `attention_mask`

**4. Random Tensors**[tests/models/resnet/test_resnet.py 16-19](https://github.com/tenstorrent/pytorch2.0_ttnn/blob/7ba2867e/tests/models/resnet/test_resnet.py#L16-L19)

*   `torch.rand()` for shape validation
*   Used when pretrained weights not required
*   Useful for architecture testing

**5. Multi-Modal Inputs**[tests/models/vilt/test_vilt.py 20-29](https://github.com/tenstorrent/pytorch2.0_ttnn/blob/7ba2867e/tests/models/vilt/test_vilt.py#L20-L29)

*   Image-text pairs
*   Processor handles joint encoding
*   Multiple input tensors in dictionary

### Batch Size Handling

The `repeat_inputs` utility function handles batch size expansion:

**Tensor Inputs**[tests/models/yolov5/test_yolov5.py 32](https://github.com/tenstorrent/pytorch2.0_ttnn/blob/7ba2867e/tests/models/yolov5/test_yolov5.py#L32-L32)

**Dictionary Inputs**[tests/models/bert/test_bert.py 41](https://github.com/tenstorrent/pytorch2.0_ttnn/blob/7ba2867e/tests/models/bert/test_bert.py#L41-L41)

*   Automatically replicates each dictionary value
*   Maintains structure for tokenizer outputs

**Manual Expansion**[tests/models/bert/test_bert.py 34-40](https://github.com/tenstorrent/pytorch2.0_ttnn/blob/7ba2867e/tests/models/bert/test_bert.py#L34-L40)

### Precision Handling

All models and inputs convert to `torch.bfloat16`:

**Model Conversion**[tests/models/mnist/test_mnist.py 44](https://github.com/tenstorrent/pytorch2.0_ttnn/blob/7ba2867e/tests/models/mnist/test_mnist.py#L44-L44)

**Input Conversion**[tests/models/resnet/test_resnet.py 18](https://github.com/tenstorrent/pytorch2.0_ttnn/blob/7ba2867e/tests/models/resnet/test_resnet.py#L18-L18)

**Conditional Conversion**[tests/models/beit/test_beit_image_classification.py 23](https://github.com/tenstorrent/pytorch2.0_ttnn/blob/7ba2867e/tests/models/beit/test_beit_image_classification.py#L23-L23)

Sources: [tests/models/resnet50/test_resnet50.py 22-33](https://github.com/tenstorrent/pytorch2.0_ttnn/blob/7ba2867e/tests/models/resnet50/test_resnet50.py#L22-L33)[tests/models/mnist/test_mnist.py 47-53](https://github.com/tenstorrent/pytorch2.0_ttnn/blob/7ba2867e/tests/models/mnist/test_mnist.py#L47-L53)[tests/models/falcon/test_falcon.py 20-25](https://github.com/tenstorrent/pytorch2.0_ttnn/blob/7ba2867e/tests/models/falcon/test_falcon.py#L20-L25)[tests/models/vilt/test_vilt.py 20-29](https://github.com/tenstorrent/pytorch2.0_ttnn/blob/7ba2867e/tests/models/vilt/test_vilt.py#L20-L29)[tests/models/bert/test_bert.py 34-41](https://github.com/tenstorrent/pytorch2.0_ttnn/blob/7ba2867e/tests/models/bert/test_bert.py#L34-L41)

## Special Test Configurations

### Training Mode Tests

Some tests include training-specific methods:

**Gradient Requirement**[tests/models/beit/test_beit_image_classification.py 27-29](https://github.com/tenstorrent/pytorch2.0_ttnn/blob/7ba2867e/tests/models/beit/test_beit_image_classification.py#L27-L29)

**Fake Loss Function**[tests/models/beit/test_beit_image_classification.py 31-32](https://github.com/tenstorrent/pytorch2.0_ttnn/blob/7ba2867e/tests/models/beit/test_beit_image_classification.py#L31-L32)

**Gradient Extraction**[tests/models/beit/test_beit_image_classification.py 34-35](https://github.com/tenstorrent/pytorch2.0_ttnn/blob/7ba2867e/tests/models/beit/test_beit_image_classification.py#L34-L35)

### Custom Model Wrappers

Some tests wrap models to control execution:

**Llama Wrapper**[tests/models/llama/test_llama.py 11-18](https://github.com/tenstorrent/pytorch2.0_ttnn/blob/7ba2867e/tests/models/llama/test_llama.py#L11-L18)

*   Wraps `AutoModelForCausalLM` to return only logits
*   Exposes explicit `input_ids` and `attention_mask` parameters
*   Simplifies forward call signature

**SpeechT5 Wrapper**[tests/models/speecht5_tts/test_speecht5_tts.py 18](https://github.com/tenstorrent/pytorch2.0_ttnn/blob/7ba2867e/tests/models/speecht5_tts/test_speecht5_tts.py#L18-L18)

*   Tests `generate_speech` method instead of full model
*   Requires vocoder as additional input
*   Custom `set_model_eval` to bypass `.eval()` call

### Fixture Interactions

**disable_load_params_once**[tests/models/falcon/test_falcon.py 36](https://github.com/tenstorrent/pytorch2.0_ttnn/blob/7ba2867e/tests/models/falcon/test_falcon.py#L36-L36)

*   Disables LoadOncePass for specific tests
*   Used with large models to avoid memory issues
*   Declared as function parameter

**manage_dependencies**[tests/models/yolov5/test_yolov5.py 107](https://github.com/tenstorrent/pytorch2.0_ttnn/blob/7ba2867e/tests/models/yolov5/test_yolov5.py#L107-L107)

*   Automatically installs test-specific dependencies
*   Reads from module-level `dependencies` variable
*   Cleans up after test completion

Sources: [tests/models/beit/test_beit_image_classification.py 27-35](https://github.com/tenstorrent/pytorch2.0_ttnn/blob/7ba2867e/tests/models/beit/test_beit_image_classification.py#L27-L35)[tests/models/llama/test_llama.py 11-18](https://github.com/tenstorrent/pytorch2.0_ttnn/blob/7ba2867e/tests/models/llama/test_llama.py#L11-L18)[tests/models/speecht5_tts/test_speecht5_tts.py 13-34](https://github.com/tenstorrent/pytorch2.0_ttnn/blob/7ba2867e/tests/models/speecht5_tts/test_speecht5_tts.py#L13-L34)[tests/models/falcon/test_falcon.py 36](https://github.com/tenstorrent/pytorch2.0_ttnn/blob/7ba2867e/tests/models/falcon/test_falcon.py#L36-L36)

## Metrics Collection and Result Recording

### Record Property Pattern

All tests use pytest's `record_property` to capture metadata and results:

The final `torch_ttnn` property stores:

*   `tester`: ModelTester instance with configuration
*   `results`: Output tensors from model execution

This data feeds into the metrics collection system documented in [Metrics Collection and Schema Tracking](https://deepwiki.com/tenstorrent/pytorch2.0_ttnn/7.1-metrics-collection-and-schema-tracking).

### Output Interpretation

Many tests include result interpretation logic:

**Classification Models**[tests/models/torchvision/test_torchvision_image_classification.py 116-119](https://github.com/tenstorrent/pytorch2.0_ttnn/blob/7ba2867e/tests/models/torchvision/test_torchvision_image_classification.py#L116-L119)

**Language Models**[tests/models/falcon/test_falcon.py 45-49](https://github.com/tenstorrent/pytorch2.0_ttnn/blob/7ba2867e/tests/models/falcon/test_falcon.py#L45-L49)

**Question Answering**[tests/models/bert/test_bert.py 72-76](https://github.com/tenstorrent/pytorch2.0_ttnn/blob/7ba2867e/tests/models/bert/test_bert.py#L72-L76)

Sources: [tests/models/torchvision/test_torchvision_image_classification.py 107-121](https://github.com/tenstorrent/pytorch2.0_ttnn/blob/7ba2867e/tests/models/torchvision/test_torchvision_image_classification.py#L107-L121)[tests/models/falcon/test_falcon.py 36-61](https://github.com/tenstorrent/pytorch2.0_ttnn/blob/7ba2867e/tests/models/falcon/test_falcon.py#L36-L61)[tests/models/bert/test_bert.py 62-90](https://github.com/tenstorrent/pytorch2.0_ttnn/blob/7ba2867e/tests/models/bert/test_bert.py#L62-L90)

## Test Coverage Summary

### Model Count by Category

| Category | Models Tested | Fully Converted (e2e) | Compilation XFail | Skipped |
| --- | --- | --- | --- | --- |
| Computer Vision | 40+ | 35+ | 2 | 1 |
| NLP | 10+ | 1 | 3 | 0 |
| Multi-Modal | 5+ | 1 | 1 | 1 |
| Speech | 1 | 0 | 1 | 0 |
| Specialized | 5+ | 3 | 0 | 0 |

### Architecture Coverage

The test suite covers major deep learning architectures:

**Convolutional Networks**

*   Classic CNNs: ResNet, VGG, DenseNet, MobileNet
*   Object Detection: YOLO, DETR, YOLOS
*   Pose Estimation: OpenPose variants

**Transformers**

*   Vision Transformers: ViT, Swin, BEiT, DeiT
*   Language Models: BERT, GPT-2, Llama, Falcon, Bloom, XGLM
*   Multi-Modal: ViLT, Perceiver IO
*   Speech: SpeechT5

**Specialized Architectures**

*   Autoencoders: Linear autoencoder
*   Hybrid: RegNet (ResNet + Group Normalization)

Sources: [tests/models/torchvision/test_torchvision_image_classification.py 40-103](https://github.com/tenstorrent/pytorch2.0_ttnn/blob/7ba2867e/tests/models/torchvision/test_torchvision_image_classification.py#L40-L103)[tests/models/bert/test_bert.py](https://github.com/tenstorrent/pytorch2.0_ttnn/blob/7ba2867e/tests/models/bert/test_bert.py)[tests/models/llama/test_llama.py](https://github.com/tenstorrent/pytorch2.0_ttnn/blob/7ba2867e/tests/models/llama/test_llama.py)

Dismiss
Refresh this wiki

Enter email to refresh
---
project: tt-buda
github: https://github.com/tenstorrent/tt-buda
deepwiki: https://deepwiki.com/tenstorrent/tt-buda/4-models-and-benchmarking
---

# Models and Benchmarking

Relevant source files
*   [pybuda/pybuda/python_codegen.py](https://github.com/tenstorrent/tt-buda/blob/74755d1f/pybuda/pybuda/python_codegen.py)
*   [pybuda/pybuda/tvm_to_python.py](https://github.com/tenstorrent/tt-buda/blob/74755d1f/pybuda/pybuda/tvm_to_python.py)
*   [pybuda/test/benchmark/benchmark/models/bert.py](https://github.com/tenstorrent/tt-buda/blob/74755d1f/pybuda/test/benchmark/benchmark/models/bert.py)
*   [pybuda/test/benchmark/benchmark/models/deit.py](https://github.com/tenstorrent/tt-buda/blob/74755d1f/pybuda/test/benchmark/benchmark/models/deit.py)
*   [pybuda/test/benchmark/benchmark/models/hrnet.py](https://github.com/tenstorrent/tt-buda/blob/74755d1f/pybuda/test/benchmark/benchmark/models/hrnet.py)
*   [pybuda/test/benchmark/benchmark/models/inception_v4.py](https://github.com/tenstorrent/tt-buda/blob/74755d1f/pybuda/test/benchmark/benchmark/models/inception_v4.py)
*   [pybuda/test/benchmark/benchmark/models/mobilenet_v1.py](https://github.com/tenstorrent/tt-buda/blob/74755d1f/pybuda/test/benchmark/benchmark/models/mobilenet_v1.py)
*   [pybuda/test/benchmark/benchmark/models/mobilenet_v2.py](https://github.com/tenstorrent/tt-buda/blob/74755d1f/pybuda/test/benchmark/benchmark/models/mobilenet_v2.py)
*   [pybuda/test/benchmark/benchmark/models/mobilenet_v3_timm.py](https://github.com/tenstorrent/tt-buda/blob/74755d1f/pybuda/test/benchmark/benchmark/models/mobilenet_v3_timm.py)
*   [pybuda/test/benchmark/benchmark/models/openpose_body.py](https://github.com/tenstorrent/tt-buda/blob/74755d1f/pybuda/test/benchmark/benchmark/models/openpose_body.py)
*   [pybuda/test/benchmark/benchmark/models/openpose_hand.py](https://github.com/tenstorrent/tt-buda/blob/74755d1f/pybuda/test/benchmark/benchmark/models/openpose_hand.py)
*   [pybuda/test/benchmark/benchmark/models/resnet.py](https://github.com/tenstorrent/tt-buda/blob/74755d1f/pybuda/test/benchmark/benchmark/models/resnet.py)
*   [pybuda/test/benchmark/benchmark/models/t5.py](https://github.com/tenstorrent/tt-buda/blob/74755d1f/pybuda/test/benchmark/benchmark/models/t5.py)
*   [pybuda/test/benchmark/benchmark/models/unet.py](https://github.com/tenstorrent/tt-buda/blob/74755d1f/pybuda/test/benchmark/benchmark/models/unet.py)
*   [pybuda/test/benchmark/benchmark/models/vit.py](https://github.com/tenstorrent/tt-buda/blob/74755d1f/pybuda/test/benchmark/benchmark/models/vit.py)
*   [pybuda/test/benchmark/benchmark/models/vovnet_v2.py](https://github.com/tenstorrent/tt-buda/blob/74755d1f/pybuda/test/benchmark/benchmark/models/vovnet_v2.py)
*   [pybuda/test/benchmark/benchmark/models/yolo_v3.py](https://github.com/tenstorrent/tt-buda/blob/74755d1f/pybuda/test/benchmark/benchmark/models/yolo_v3.py)
*   [pybuda/test/benchmark/benchmark/models/yolo_v5.py](https://github.com/tenstorrent/tt-buda/blob/74755d1f/pybuda/test/benchmark/benchmark/models/yolo_v5.py)
*   [pybuda/test/model_demos/high_prio/cnn/onnx/test_ddrnet.py](https://github.com/tenstorrent/tt-buda/blob/74755d1f/pybuda/test/model_demos/high_prio/cnn/onnx/test_ddrnet.py)
*   [pybuda/test/model_demos/high_prio/cnn/onnx/test_hardnet.py](https://github.com/tenstorrent/tt-buda/blob/74755d1f/pybuda/test/model_demos/high_prio/cnn/onnx/test_hardnet.py)
*   [pybuda/test/model_demos/high_prio/cnn/onnx/test_yolo_v5.py](https://github.com/tenstorrent/tt-buda/blob/74755d1f/pybuda/test/model_demos/high_prio/cnn/onnx/test_yolo_v5.py)
*   [pybuda/test/model_demos/high_prio/cnn/onnx/test_yolo_x.py](https://github.com/tenstorrent/tt-buda/blob/74755d1f/pybuda/test/model_demos/high_prio/cnn/onnx/test_yolo_x.py)
*   [pybuda/test/model_demos/high_prio/cnn/pytorch/test_ddrnet.py](https://github.com/tenstorrent/tt-buda/blob/74755d1f/pybuda/test/model_demos/high_prio/cnn/pytorch/test_ddrnet.py)
*   [pybuda/test/model_demos/high_prio/cnn/pytorch/test_hardnet.py](https://github.com/tenstorrent/tt-buda/blob/74755d1f/pybuda/test/model_demos/high_prio/cnn/pytorch/test_hardnet.py)
*   [pybuda/test/model_demos/high_prio/cnn/pytorch/test_monodle.py](https://github.com/tenstorrent/tt-buda/blob/74755d1f/pybuda/test/model_demos/high_prio/cnn/pytorch/test_monodle.py)
*   [pybuda/test/model_demos/high_prio/cnn/pytorch/test_pidnet.py](https://github.com/tenstorrent/tt-buda/blob/74755d1f/pybuda/test/model_demos/high_prio/cnn/pytorch/test_pidnet.py)
*   [pybuda/test/model_demos/high_prio/cnn/pytorch/test_ssd300_resnet50.py](https://github.com/tenstorrent/tt-buda/blob/74755d1f/pybuda/test/model_demos/high_prio/cnn/pytorch/test_ssd300_resnet50.py)
*   [pybuda/test/model_demos/high_prio/cnn/pytorch/test_tri_basic_2.py](https://github.com/tenstorrent/tt-buda/blob/74755d1f/pybuda/test/model_demos/high_prio/cnn/pytorch/test_tri_basic_2.py)
*   [pybuda/test/model_demos/high_prio/cnn/pytorch/test_yolo_v5.py](https://github.com/tenstorrent/tt-buda/blob/74755d1f/pybuda/test/model_demos/high_prio/cnn/pytorch/test_yolo_v5.py)
*   [pybuda/test/model_demos/high_prio/nlp/pytorch/test_gemma_2b.py](https://github.com/tenstorrent/tt-buda/blob/74755d1f/pybuda/test/model_demos/high_prio/nlp/pytorch/test_gemma_2b.py)
*   [pybuda/test/model_demos/models/monodle.py](https://github.com/tenstorrent/tt-buda/blob/74755d1f/pybuda/test/model_demos/models/monodle.py)
*   [pybuda/test/test_shapes.py](https://github.com/tenstorrent/tt-buda/blob/74755d1f/pybuda/test/test_shapes.py)

This page provides an overview of the various models supported by PyBuda and the benchmarking capabilities available to evaluate performance on Tenstorrent hardware. It covers the integration of both CNN and NLP models from various frameworks and the benchmarking system used to measure their performance.

For information on the testing framework used to verify these models, see [Testing Framework](https://deepwiki.com/tenstorrent/tt-buda/3-testing-framework).

## Model Support Architecture

PyBuda is designed to work with models from multiple frameworks, including PyTorch, ONNX, and TensorFlow. The system provides a unified approach to compile and run these models on Tenstorrent hardware.

Sources:

*   [pybuda/test/model_demos/high_prio/nlp/pytorch/test_gemma_2b.py 12-25](https://github.com/tenstorrent/tt-buda/blob/74755d1f/pybuda/test/model_demos/high_prio/nlp/pytorch/test_gemma_2b.py#L12-L25)
*   [pybuda/test/model_demos/high_prio/cnn/pytorch/test_ddrnet.py 4-22](https://github.com/tenstorrent/tt-buda/blob/74755d1f/pybuda/test/model_demos/high_prio/cnn/pytorch/test_ddrnet.py#L4-L22)
*   [pybuda/test/model_demos/high_prio/cnn/onnx/test_ddrnet.py 4-13](https://github.com/tenstorrent/tt-buda/blob/74755d1f/pybuda/test/model_demos/high_prio/cnn/onnx/test_ddrnet.py#L4-L13)

## Model Catalog and Support Status

PyBuda supports a wide range of models across different domains. The table below summarizes the key models and their support status on different Tenstorrent hardware architectures.

| Model Category | Model Name | PyTorch | ONNX | Grayskull | Wormhole_B0 | Blackhole |
| --- | --- | --- | --- | --- | --- | --- |
| CNN - Detection | YOLO v5 (n,s,m,l,x) | ✓ | ✓ | ✓ | ✓ | ✓ |
| CNN - Detection | YOLOx (nano to x) | ✓ | ✓ | ✓ | ✓ | ✓ |
| CNN - Segmentation | DDRNet (23s,23,39) | ✓ | ✓ | ✓ | ✓ | ✓ |
| CNN - Segmentation | PIDNet (s,m,l) | ✓ | - | ✓ | ✓ | ✓ |
| CNN - Classification | HardNet (39ds-85) | ✓ | ✓ | ✓ | ✓ | ✓ |
| CNN - Detection | SSD300 ResNet50 | ✓ | - | ✓ | ✓ | ✓ |
| CNN - Pose | OpenPose Hand | ✓ | - | ✓ | ✓ | - |
| CNN - Segmentation | MonoDLE | ✓ | - | ✓ | ✓ | ✓ |
| NLP - LLM | Gemma-2b | ✓ | - | ✓ | ✓ | ✓ |
| NLP - Transformer | BERT | ✓ | - | ✓ | ✓ | ✓ |
| NLP - Transformer | T5/FLAN-T5 | ✓ | - | ✓ | ✓ | ✓ |

Sources:

*   [pybuda/test/model_demos/high_prio/cnn/pytorch/test_yolo_v5.py 67-70](https://github.com/tenstorrent/tt-buda/blob/74755d1f/pybuda/test/model_demos/high_prio/cnn/pytorch/test_yolo_v5.py#L67-L70)
*   [pybuda/test/model_demos/high_prio/cnn/onnx/test_yolo_v5.py 63-64](https://github.com/tenstorrent/tt-buda/blob/74755d1f/pybuda/test/model_demos/high_prio/cnn/onnx/test_yolo_v5.py#L63-L64)
*   [pybuda/test/model_demos/high_prio/cnn/pytorch/test_ddrnet.py 25-26](https://github.com/tenstorrent/tt-buda/blob/74755d1f/pybuda/test/model_demos/high_prio/cnn/pytorch/test_ddrnet.py#L25-L26)
*   [pybuda/test/model_demos/high_prio/cnn/onnx/test_ddrnet.py 15-16](https://github.com/tenstorrent/tt-buda/blob/74755d1f/pybuda/test/model_demos/high_prio/cnn/onnx/test_ddrnet.py#L15-L16)
*   [pybuda/test/model_demos/high_prio/nlp/pytorch/test_gemma_2b.py 53-55](https://github.com/tenstorrent/tt-buda/blob/74755d1f/pybuda/test/model_demos/high_prio/nlp/pytorch/test_gemma_2b.py#L53-L55)
*   [pybuda/test/benchmark/benchmark/models/bert.py 41-42](https://github.com/tenstorrent/tt-buda/blob/74755d1f/pybuda/test/benchmark/benchmark/models/bert.py#L41-L42)
*   [pybuda/test/benchmark/benchmark/models/t5.py 13-14](https://github.com/tenstorrent/tt-buda/blob/74755d1f/pybuda/test/benchmark/benchmark/models/t5.py#L13-L14)

## CNN Model Integration

PyBuda provides comprehensive support for CNN-based models across various tasks including image classification, object detection, and semantic segmentation.

### CNN Model Integration Workflow

Sources:

*   [pybuda/test/model_demos/high_prio/cnn/pytorch/test_ddrnet.py 29-99](https://github.com/tenstorrent/tt-buda/blob/74755d1f/pybuda/test/model_demos/high_prio/cnn/pytorch/test_ddrnet.py#L29-L99)
*   [pybuda/test/model_demos/high_prio/cnn/pytorch/test_yolo_v5.py 24-67](https://github.com/tenstorrent/tt-buda/blob/74755d1f/pybuda/test/model_demos/high_prio/cnn/pytorch/test_yolo_v5.py#L24-L67)
*   [pybuda/test/model_demos/high_prio/cnn/onnx/test_ddrnet.py 19-71](https://github.com/tenstorrent/tt-buda/blob/74755d1f/pybuda/test/model_demos/high_prio/cnn/onnx/test_ddrnet.py#L19-L71)

### Example: DDRNet Integration

DDRNet (Dense Dual-Resolution Network) is supported in both PyTorch and ONNX formats with multiple variants:

DDRNet requires specific configurations for different hardware backends to optimize performance:

Sources:

*   [pybuda/test/model_demos/high_prio/cnn/pytorch/test_ddrnet.py 29-98](https://github.com/tenstorrent/tt-buda/blob/74755d1f/pybuda/test/model_demos/high_prio/cnn/pytorch/test_ddrnet.py#L29-L98)
*   [pybuda/test/model_demos/high_prio/cnn/onnx/test_ddrnet.py 21-71](https://github.com/tenstorrent/tt-buda/blob/74755d1f/pybuda/test/model_demos/high_prio/cnn/onnx/test_ddrnet.py#L21-L71)

### Example: YOLO v5 Integration

YOLO v5 is supported with multiple variants (n, s, m, l, x) and image sizes:

Hardware-specific optimizations are essential for best performance:

Sources:

*   [pybuda/test/model_demos/high_prio/cnn/pytorch/test_yolo_v5.py 24-67](https://github.com/tenstorrent/tt-buda/blob/74755d1f/pybuda/test/model_demos/high_prio/cnn/pytorch/test_yolo_v5.py#L24-L67)
*   [pybuda/test/model_demos/high_prio/cnn/onnx/test_yolo_v5.py 19-104](https://github.com/tenstorrent/tt-buda/blob/74755d1f/pybuda/test/model_demos/high_prio/cnn/onnx/test_yolo_v5.py#L19-L104)

## NLP Model Integration

PyBuda supports various NLP models, with special emphasis on transformer-based architectures like Gemma-2b, BERT, and T5/FLAN-T5.

### NLP Model Integration Workflow

Sources:

*   [pybuda/test/model_demos/high_prio/nlp/pytorch/test_gemma_2b.py 60-350](https://github.com/tenstorrent/tt-buda/blob/74755d1f/pybuda/test/model_demos/high_prio/nlp/pytorch/test_gemma_2b.py#L60-L350)
*   [pybuda/test/benchmark/benchmark/models/bert.py 41-157](https://github.com/tenstorrent/tt-buda/blob/74755d1f/pybuda/test/benchmark/benchmark/models/bert.py#L41-L157)
*   [pybuda/test/benchmark/benchmark/models/t5.py 13-57](https://github.com/tenstorrent/tt-buda/blob/74755d1f/pybuda/test/benchmark/benchmark/models/t5.py#L13-L57)

### Example: Gemma-2b Integration

Gemma-2b is a state-of-the-art language model that's supported in PyBuda with both inference and generation capabilities:

For text generation, PyBuda provides a pipeline API that simplifies the integration:

Sources:

*   [pybuda/test/model_demos/high_prio/nlp/pytorch/test_gemma_2b.py 414-490](https://github.com/tenstorrent/tt-buda/blob/74755d1f/pybuda/test/model_demos/high_prio/nlp/pytorch/test_gemma_2b.py#L414-L490)
*   [pybuda/test/model_demos/high_prio/nlp/pytorch/test_gemma_2b.py 295-353](https://github.com/tenstorrent/tt-buda/blob/74755d1f/pybuda/test/model_demos/high_prio/nlp/pytorch/test_gemma_2b.py#L295-L353)

## Benchmarking System

PyBuda provides a comprehensive benchmarking system to evaluate model performance across different hardware targets, data formats, and configurations.

### Benchmarking Architecture

Sources:

*   [pybuda/test/benchmark/benchmark/models/resnet.py 16-69](https://github.com/tenstorrent/tt-buda/blob/74755d1f/pybuda/test/benchmark/benchmark/models/resnet.py#L16-L69)
*   [pybuda/test/benchmark/benchmark/models/bert.py 41-157](https://github.com/tenstorrent/tt-buda/blob/74755d1f/pybuda/test/benchmark/benchmark/models/bert.py#L41-L157)
*   [pybuda/test/benchmark/benchmark/models/yolo_v5.py 13-85](https://github.com/tenstorrent/tt-buda/blob/74755d1f/pybuda/test/benchmark/benchmark/models/yolo_v5.py#L13-L85)

### Benchmarking API

The benchmarking system uses a decorator-based approach to register models for benchmarking:

This pattern is used across all benchmark models, allowing for consistent evaluation and comparison.

Sources:

*   [pybuda/test/benchmark/benchmark/models/resnet.py 16-68](https://github.com/tenstorrent/tt-buda/blob/74755d1f/pybuda/test/benchmark/benchmark/models/resnet.py#L16-L68)
*   [pybuda/test/benchmark/benchmark/models/mobilenet_v2.py 14-85](https://github.com/tenstorrent/tt-buda/blob/74755d1f/pybuda/test/benchmark/benchmark/models/mobilenet_v2.py#L14-L85)
*   [pybuda/test/benchmark/benchmark/models/bert.py 41-157](https://github.com/tenstorrent/tt-buda/blob/74755d1f/pybuda/test/benchmark/benchmark/models/bert.py#L41-L157)

### Example Benchmarks

The table below shows example model configurations available for benchmarking:

| Model Family | Available Configurations | Training Support | Mixed Precision |
| --- | --- | --- | --- |
| ResNet | resnet18, resnet50 | ✓ | ✓ |
| MobileNet | v1 (192, 224), v2 (96, 160, 224), v3 (small, large) | ✓ | ✓ |
| YOLO | v3 (default, tiny), v5 (s, m) | ✓ | ✓ |
| VIT/DeiT | base, small | ✓ | ✓ |
| BERT | tiny, base, large | ✓ | ✓ |
| T5/FLAN-T5 | base, large | ✓ | ✓ |
| HRNet | multiple width variants | ✓ | ✓ |
| VoVNet v2 | 19, 39, 99 | ✓ | ✓ |
| UNet | 256 | ✓ | ✓ |
| Inception | v4 (224) | ✓ | ✓ |
| OpenPose | body (2d, 3d), hand | ✓ | ✓ |

Sources:

*   [pybuda/test/benchmark/benchmark/models/resnet.py 16-17](https://github.com/tenstorrent/tt-buda/blob/74755d1f/pybuda/test/benchmark/benchmark/models/resnet.py#L16-L17)
*   [pybuda/test/benchmark/benchmark/models/mobilenet_v2.py 13-14](https://github.com/tenstorrent/tt-buda/blob/74755d1f/pybuda/test/benchmark/benchmark/models/mobilenet_v2.py#L13-L14)
*   [pybuda/test/benchmark/benchmark/models/vit.py 13-14](https://github.com/tenstorrent/tt-buda/blob/74755d1f/pybuda/test/benchmark/benchmark/models/vit.py#L13-L14)
*   [pybuda/test/benchmark/benchmark/models/bert.py 41-42](https://github.com/tenstorrent/tt-buda/blob/74755d1f/pybuda/test/benchmark/benchmark/models/bert.py#L41-L42)
*   [pybuda/test/benchmark/benchmark/models/t5.py 13-14](https://github.com/tenstorrent/tt-buda/blob/74755d1f/pybuda/test/benchmark/benchmark/models/t5.py#L13-L14)
*   [pybuda/test/benchmark/benchmark/models/yolo_v5.py 13-14](https://github.com/tenstorrent/tt-buda/blob/74755d1f/pybuda/test/benchmark/benchmark/models/yolo_v5.py#L13-L14)
*   [pybuda/test/benchmark/benchmark/models/hrnet.py 18-28](https://github.com/tenstorrent/tt-buda/blob/74755d1f/pybuda/test/benchmark/benchmark/models/hrnet.py#L18-L28)
*   [pybuda/test/benchmark/benchmark/models/vovnet_v2.py 13-14](https://github.com/tenstorrent/tt-buda/blob/74755d1f/pybuda/test/benchmark/benchmark/models/vovnet_v2.py#L13-L14)
*   [pybuda/test/benchmark/benchmark/models/unet.py 12-13](https://github.com/tenstorrent/tt-buda/blob/74755d1f/pybuda/test/benchmark/benchmark/models/unet.py#L12-L13)
*   [pybuda/test/benchmark/benchmark/models/openpose_body.py 13-14](https://github.com/tenstorrent/tt-buda/blob/74755d1f/pybuda/test/benchmark/benchmark/models/openpose_body.py#L13-L14)
*   [pybuda/test/benchmark/benchmark/models/openpose_hand.py 12-13](https://github.com/tenstorrent/tt-buda/blob/74755d1f/pybuda/test/benchmark/benchmark/models/openpose_hand.py#L12-L13)

## Model Optimization Techniques

To achieve optimal performance on Tenstorrent hardware, PyBuda employs several optimization strategies:

### Data Formats and Mixed Precision

### Hardware-Specific Optimizations

### Balancer Policies and Placement Strategies

Sources:

*   [pybuda/test/model_demos/high_prio/nlp/pytorch/test_gemma_2b.py 425-439](https://github.com/tenstorrent/tt-buda/blob/74755d1f/pybuda/test/model_demos/high_prio/nlp/pytorch/test_gemma_2b.py#L425-L439)
*   [pybuda/test/benchmark/benchmark/models/mobilenet_v2.py 41-48](https://github.com/tenstorrent/tt-buda/blob/74755d1f/pybuda/test/benchmark/benchmark/models/mobilenet_v2.py#L41-L48)
*   [pybuda/test/model_demos/high_prio/cnn/pytorch/test_yolo_v5.py 39-52](https://github.com/tenstorrent/tt-buda/blob/74755d1f/pybuda/test/model_demos/high_prio/cnn/pytorch/test_yolo_v5.py#L39-L52)
*   [pybuda/test/model_demos/high_prio/cnn/onnx/test_ddrnet.py 83-97](https://github.com/tenstorrent/tt-buda/blob/74755d1f/pybuda/test/model_demos/high_prio/cnn/onnx/test_ddrnet.py#L83-L97)

## Conclusion

PyBuda provides a comprehensive framework for deploying and benchmarking both CNN and NLP models on Tenstorrent hardware. The system offers:

1.   Support for multiple frameworks (PyTorch, ONNX, TensorFlow)
2.   Optimized implementations for popular CNN and NLP models
3.   Hardware-specific optimizations for Grayskull, Wormhole_B0, and Blackhole architectures
4.   Flexible benchmarking capabilities for performance evaluation
5.   Mixed precision support for optimal performance/accuracy trade-offs

When working with models on PyBuda, users should:

*   Configure the appropriate balancer policy (typically "Ribbon" for best results)
*   Set the right data format (Float16_b/BFloat16 or mixed precision with Bfp8_b)
*   Apply hardware-specific optimizations based on the target device
*   Use the benchmarking system to evaluate and optimize performance

Sources:

*   [pybuda/test/model_demos/high_prio/nlp/pytorch/test_gemma_2b.py 414-490](https://github.com/tenstorrent/tt-buda/blob/74755d1f/pybuda/test/model_demos/high_prio/nlp/pytorch/test_gemma_2b.py#L414-L490)
*   [pybuda/test/model_demos/high_prio/cnn/pytorch/test_ddrnet.py 29-100](https://github.com/tenstorrent/tt-buda/blob/74755d1f/pybuda/test/model_demos/high_prio/cnn/pytorch/test_ddrnet.py#L29-L100)
*   [pybuda/test/benchmark/benchmark/models/resnet.py 16-69](https://github.com/tenstorrent/tt-buda/blob/74755d1f/pybuda/test/benchmark/benchmark/models/resnet.py#L16-L69)
*   [pybuda/test/benchmark/benchmark/models/bert.py 41-157](https://github.com/tenstorrent/tt-buda/blob/74755d1f/pybuda/test/benchmark/benchmark/models/bert.py#L41-L157)

Dismiss
Refresh this wiki

Enter email to refresh
---
project: tt-forge-models
github: https://github.com/tenstorrent/tt-forge-models
deepwiki: https://deepwiki.com/tenstorrent/tt-forge-models/3-computer-vision-models)
---

# Computer Vision Models

 Relevant source files

* [README.md](https://github.com/tenstorrent/tt-forge-models/blob/0a7d2042/README.md?plain=1)
* [densenet/pytorch/loader.py](https://github.com/tenstorrent/tt-forge-models/blob/0a7d2042/densenet/pytorch/loader.py)
* [dla/pytorch/loader.py](https://github.com/tenstorrent/tt-forge-models/blob/0a7d2042/dla/pytorch/loader.py)
* [efficientnet/pytorch/loader.py](https://github.com/tenstorrent/tt-forge-models/blob/0a7d2042/efficientnet/pytorch/loader.py)
* [ghostnet/pytorch/loader.py](https://github.com/tenstorrent/tt-forge-models/blob/0a7d2042/ghostnet/pytorch/loader.py)
* [hrnet/pytorch/loader.py](https://github.com/tenstorrent/tt-forge-models/blob/0a7d2042/hrnet/pytorch/loader.py)
* [mlp\_mixer/pytorch/loader.py](https://github.com/tenstorrent/tt-forge-models/blob/0a7d2042/mlp_mixer/pytorch/loader.py)
* [regnet/pytorch/loader.py](https://github.com/tenstorrent/tt-forge-models/blob/0a7d2042/regnet/pytorch/loader.py)
* [resnext/pytorch/loader.py](https://github.com/tenstorrent/tt-forge-models/blob/0a7d2042/resnext/pytorch/loader.py)
* [segformer/pytorch/loader.py](https://github.com/tenstorrent/tt-forge-models/blob/0a7d2042/segformer/pytorch/loader.py)
* [tools/utils.py](https://github.com/tenstorrent/tt-forge-models/blob/0a7d2042/tools/utils.py)
* [vit/pytorch/loader.py](https://github.com/tenstorrent/tt-forge-models/blob/0a7d2042/vit/pytorch/loader.py)
* [yolov3/pytorch/loader.py](https://github.com/tenstorrent/tt-forge-models/blob/0a7d2042/yolov3/pytorch/loader.py)
* [yolov4/pytorch/loader.py](https://github.com/tenstorrent/tt-forge-models/blob/0a7d2042/yolov4/pytorch/loader.py)
* [yolov4/pytorch/src/post\_processing.py](https://github.com/tenstorrent/tt-forge-models/blob/0a7d2042/yolov4/pytorch/src/post_processing.py)

This document covers the computer vision model implementations available in the tt-forge-models repository. These models provide standardized interfaces for image classification, object detection, and other computer vision tasks through the `ForgeModel` abstraction system.

For information about the core architecture and `ForgeModel` base class, see [Core Architecture](/tenstorrent/tt-forge-models/2-core-architecture). For multimodal models that combine vision with other modalities, see [Multimodal Models](/tenstorrent/tt-forge-models/5-multimodal-models).

## Overview

The computer vision models in tt-forge-models are organized into several categories based on their primary tasks and architectures. All models follow the standardized `ModelLoader` pattern and inherit from the `ForgeModel` base class, providing consistent interfaces for model loading, input preprocessing, and output handling.

Sources: [base.py](https://github.com/tenstorrent/tt-forge-models/blob/0a7d2042/base.py) [config.py](https://github.com/tenstorrent/tt-forge-models/blob/0a7d2042/config.py) [yolov3/pytorch/loader.py23-24](https://github.com/tenstorrent/tt-forge-models/blob/0a7d2042/yolov3/pytorch/loader.py#L23-L24) [vit/pytorch/loader.py21-22](https://github.com/tenstorrent/tt-forge-models/blob/0a7d2042/vit/pytorch/loader.py#L21-L22) [efficientnet/pytorch/loader.py22-26](https://github.com/tenstorrent/tt-forge-models/blob/0a7d2042/efficientnet/pytorch/loader.py#L22-L26)

## Architecture and Common Patterns

All computer vision models follow a standardized architecture pattern that ensures consistency across different model types and sources. The pattern includes configuration management, variant handling, and standardized method signatures.

Sources: [base.py](https://github.com/tenstorrent/tt-forge-models/blob/0a7d2042/base.py) [config.py](https://github.com/tenstorrent/tt-forge-models/blob/0a7d2042/config.py) [yolov3/pytorch/loader.py28-32](https://github.com/tenstorrent/tt-forge-models/blob/0a7d2042/yolov3/pytorch/loader.py#L28-L32) [vit/pytorch/loader.py25-30](https://github.com/tenstorrent/tt-forge-models/blob/0a7d2042/vit/pytorch/loader.py#L25-L30) [efficientnet/pytorch/loader.py37-48](https://github.com/tenstorrent/tt-forge-models/blob/0a7d2042/efficientnet/pytorch/loader.py#L37-L48)

### Configuration Pattern

Each computer vision model implements a consistent configuration pattern using the `_VARIANTS` dictionary, `ModelVariant` enum, and `DEFAULT_VARIANT` class attribute:

| Component | Purpose | Example |
| --- | --- | --- |
| `ModelVariant` | Enumeration of available model variants | `BASE`, `LARGE`, `B0`, `B7` |
| `_VARIANTS` | Dictionary mapping variants to configurations | `{ModelVariant.BASE: ModelConfig(...)}` |
| `DEFAULT_VARIANT` | Default variant when none specified | `ModelVariant.BASE` |

Sources: [yolov3/pytorch/loader.py28-44](https://github.com/tenstorrent/tt-forge-models/blob/0a7d2042/yolov3/pytorch/loader.py#L28-L44) [vit/pytorch/loader.py25-44](https://github.com/tenstorrent/tt-forge-models/blob/0a7d2042/vit/pytorch/loader.py#L25-L44) [efficientnet/pytorch/loader.py37-115](https://github.com/tenstorrent/tt-forge-models/blob/0a7d2042/efficientnet/pytorch/loader.py#L37-L115)

## Object Detection Models

Object detection models identify and locate objects within images, typically outputting bounding boxes and class predictions. The repository includes YOLO family models with comprehensive post-processing capabilities.

Sources: [yolov3/pytorch/loader.py](https://github.com/tenstorrent/tt-forge-models/blob/0a7d2042/yolov3/pytorch/loader.py) [yolov4/pytorch/loader.py](https://github.com/tenstorrent/tt-forge-models/blob/0a7d2042/yolov4/pytorch/loader.py) [yolov4/pytorch/src/post\_processing.py](https://github.com/tenstorrent/tt-forge-models/blob/0a7d2042/yolov4/pytorch/src/post_processing.py)

### YOLOv3 Implementation

The YOLOv3 model loader provides object detection capabilities with COCO dataset support:

* **Model Configuration**: Uses custom weights from `test_files/pytorch/yolov3/yolov3_coco_01.h5`
* **Input Processing**: Resizes images to 512x512 and applies torchvision transforms
* **Model Source**: `ModelSource.CUSTOM` indicating custom implementation
* **Task Classification**: `ModelTask.CV_OBJECT_DET` for object detection

Sources: [yolov3/pytorch/loader.py34-102](https://github.com/tenstorrent/tt-forge-models/blob/0a7d2042/yolov3/pytorch/loader.py#L34-L102)

### YOLOv4 Implementation

The YOLOv4 model includes advanced post-processing capabilities with visualization support:

* **Post-Processing Pipeline**: Implements full YOLO post-processing with NMS
* **Visualization**: Includes `plot_boxes_cv2()` function for result visualization
* **Configuration**: Uses `.pth` weights with custom state dict alignment
* **Output Processing**: Generates detection results with confidence scores and class labels

Sources: [yolov4/pytorch/loader.py81-164](https://github.com/tenstorrent/tt-forge-models/blob/0a7d2042/yolov4/pytorch/loader.py#L81-L164) [yolov4/pytorch/src/post\_processing.py50-118](https://github.com/tenstorrent/tt-forge-models/blob/0a7d2042/yolov4/pytorch/src/post_processing.py#L50-L118)

## Image Classification Models

Image classification models predict class labels for input images. The repository includes both traditional CNN architectures and modern transformer-based models from various sources.

### Vision Transformers and Modern Architectures

Sources: [vit/pytorch/loader.py34-41](https://github.com/tenstorrent/tt-forge-models/blob/0a7d2042/vit/pytorch/loader.py#L34-L41) [segformer/pytorch/loader.py44-63](https://github.com/tenstorrent/tt-forge-models/blob/0a7d2042/segformer/pytorch/loader.py#L44-L63) [mlp\_mixer/pytorch/loader.py56-112](https://github.com/tenstorrent/tt-forge-models/blob/0a7d2042/mlp_mixer/pytorch/loader.py#L56-L112) [efficientnet/pytorch/loader.py103-112](https://github.com/tenstorrent/tt-forge-models/blob/0a7d2042/efficientnet/pytorch/loader.py#L103-L112)

### Model Source Integration Patterns

Different model sources require specific integration patterns:

| Source | Integration Pattern | Example Models |
| --- | --- | --- |
| `HUGGING_FACE` | `AutoImageProcessor`, `from_pretrained()` | ViT, Segformer, RegNet |
| `TIMM` | `timm.create_model()`, `resolve_data_config()` | MLP-Mixer, GhostNet, HRNet |
| `TORCH_HUB` | `torch.hub.load()` with repository specification | DenseNet, ResNeXt, DLA |
| `TORCHVISION` | `torchvision.models` with weights classes | EfficientNet |

Sources: [vit/pytorch/loader.py77-89](https://github.com/tenstorrent/tt-forge-models/blob/0a7d2042/vit/pytorch/loader.py#L77-L89) [mlp\_mixer/pytorch/loader.py148-173](https://github.com/tenstorrent/tt-forge-models/blob/0a7d2042/mlp_mixer/pytorch/loader.py#L148-L173) [densenet/pytorch/loader.py88-109](https://github.com/tenstorrent/tt-forge-models/blob/0a7d2042/densenet/pytorch/loader.py#L88-L109) [efficientnet/pytorch/loader.py148-172](https://github.com/tenstorrent/tt-forge-models/blob/0a7d2042/efficientnet/pytorch/loader.py#L148-L172)

### Preprocessing and Input Handling

Computer vision models implement consistent input preprocessing patterns while accommodating model-specific requirements:

Sources: [tools/utils.py15-112](https://github.com/tenstorrent/tt-forge-models/blob/0a7d2042/tools/utils.py#L15-L112) [vit/pytorch/loader.py91-111](https://github.com/tenstorrent/tt-forge-models/blob/0a7d2042/vit/pytorch/loader.py#L91-L111) [mlp\_mixer/pytorch/loader.py175-217](https://github.com/tenstorrent/tt-forge-models/blob/0a7d2042/mlp_mixer/pytorch/loader.py#L175-L217) [densenet/pytorch/loader.py111-148](https://github.com/tenstorrent/tt-forge-models/blob/0a7d2042/densenet/pytorch/loader.py#L111-L148)

## Specialized Computer Vision Models

Some models serve specialized computer vision tasks beyond standard classification and detection:

### HRNet for Keypoint Detection

HRNet (High-Resolution Network) is configured for keypoint detection tasks, using `ModelTask.CV_KEYPOINT_DET` instead of standard image classification:

Sources: [hrnet/pytorch/loader.py92-111](https://github.com/tenstorrent/tt-forge-models/blob/0a7d2042/hrnet/pytorch/loader.py#L92-L111)

## Usage Patterns and Integration

Computer vision models follow consistent usage patterns that enable easy integration across different frontend repositories:

Sources: [base.py](https://github.com/tenstorrent/tt-forge-models/blob/0a7d2042/base.py) [vit/pytorch/loader.py65-127](https://github.com/tenstorrent/tt-forge-models/blob/0a7d2042/vit/pytorch/loader.py#L65-L127) [yolov4/pytorch/loader.py135-164](https://github.com/tenstorrent/tt-forge-models/blob/0a7d2042/yolov4/pytorch/loader.py#L135-L164)

### File Caching and Resource Management

All computer vision models utilize the shared file caching system through `get_file()` for consistent resource management:

* **Multi-tier Caching**: Supports Docker cache, local LF cache, and home directory fallback
* **URL Handling**: Downloads from URLs with hash-based naming to prevent collisions
* **Environment Integration**: Respects `DOCKER_CACHE_ROOT`, `LOCAL_LF_CACHE`, and `IRD_LF_CACHE` variables

Sources: [tools/utils.py15-112](https://github.com/tenstorrent/tt-forge-models/blob/0a7d2042/tools/utils.py#L15-L112) [yolov3/pytorch/loader.py116-118](https://github.com/tenstorrent/tt-forge-models/blob/0a7d2042/yolov3/pytorch/loader.py#L116-L118) [vit/pytorch/loader.py94](https://github.com/tenstorrent/tt-forge-models/blob/0a7d2042/vit/pytorch/loader.py#L94-L94)

Refresh this wiki
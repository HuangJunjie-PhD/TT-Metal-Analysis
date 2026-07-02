---
project: tt-forge-models
github: https://github.com/tenstorrent/tt-forge-models
deepwiki: https://deepwiki.com/tenstorrent/tt-forge-models/5-multimodal-models
---

# Multimodal Models

Relevant source files
*   [autoencoder_linear/pytorch/loader.py](https://github.com/tenstorrent/tt-forge-models/blob/0a7d2042/autoencoder_linear/pytorch/loader.py)
*   [bloom/pytorch/loader.py](https://github.com/tenstorrent/tt-forge-models/blob/0a7d2042/bloom/pytorch/loader.py)
*   [clip/pytorch/loader.py](https://github.com/tenstorrent/tt-forge-models/blob/0a7d2042/clip/pytorch/loader.py)
*   [codegen/pytorch/loader.py](https://github.com/tenstorrent/tt-forge-models/blob/0a7d2042/codegen/pytorch/loader.py)
*   [deit/pytorch/loader.py](https://github.com/tenstorrent/tt-forge-models/blob/0a7d2042/deit/pytorch/loader.py)
*   [dpr/pytorch/loader.py](https://github.com/tenstorrent/tt-forge-models/blob/0a7d2042/dpr/pytorch/loader.py)
*   [mamba/pytorch/loader.py](https://github.com/tenstorrent/tt-forge-models/blob/0a7d2042/mamba/pytorch/loader.py)
*   [t5/pytorch/loader.py](https://github.com/tenstorrent/tt-forge-models/blob/0a7d2042/t5/pytorch/loader.py)
*   [unet/pytorch/loader.py](https://github.com/tenstorrent/tt-forge-models/blob/0a7d2042/unet/pytorch/loader.py)
*   [vilt/pytorch/loader.py](https://github.com/tenstorrent/tt-forge-models/blob/0a7d2042/vilt/pytorch/loader.py)
*   [whisper/pytorch/loader.py](https://github.com/tenstorrent/tt-forge-models/blob/0a7d2042/whisper/pytorch/loader.py)
*   [xglm/pytorch/loader.py](https://github.com/tenstorrent/tt-forge-models/blob/0a7d2042/xglm/pytorch/loader.py)

This document covers multimodal models in the tt-forge-models repository - models that process and integrate multiple types of input data such as audio, text, and images. These models represent the intersection of different AI domains and enable tasks like speech recognition, visual question answering, and cross-modal understanding.

For computer vision models that process only images, see [Computer Vision Models](https://deepwiki.com/tenstorrent/tt-forge-models/3-computer-vision-models). For natural language processing models that work with text only, see [Natural Language Processing Models](https://deepwiki.com/tenstorrent/tt-forge-models/4-natural-language-processing-models).

## Overview

The repository includes three primary categories of multimodal models, each handling different combinations of input modalities:

| Model Type | Input Modalities | Output | Primary Task | Key Models |
| --- | --- | --- | --- | --- |
| Audio-to-Text | Audio | Text | Speech Recognition | Whisper |
| Vision-Language | Image + Text | Embeddings/Scores | Cross-modal Understanding | CLIP |
| Visual Question Answering | Image + Text | Text/Answers | Visual Reasoning | ViLT |

### Multimodal Model Architecture

Sources: [whisper/pytorch/loader.py 1-100](https://github.com/tenstorrent/tt-forge-models/blob/0a7d2042/whisper/pytorch/loader.py#L1-L100)[clip/pytorch/loader.py 1-123](https://github.com/tenstorrent/tt-forge-models/blob/0a7d2042/clip/pytorch/loader.py#L1-L123)[vilt/pytorch/loader.py 1-98](https://github.com/tenstorrent/tt-forge-models/blob/0a7d2042/vilt/pytorch/loader.py#L1-L98)

## Audio-to-Text Models (Speech Recognition)

### Whisper Implementation

The Whisper model performs automatic speech recognition, converting audio input to text transcription. It follows the standard `ForgeModel` pattern with specialized audio processing capabilities.

**Key Implementation Details:**

*   **Model Configuration**: Uses `openai/whisper-tiny` as the default pretrained model [whisper/pytorch/loader.py 53](https://github.com/tenstorrent/tt-forge-models/blob/0a7d2042/whisper/pytorch/loader.py#L53-L53)
*   **Processor Initialization**: Creates `WhisperProcessor` for audio preprocessing [whisper/pytorch/loader.py 64-66](https://github.com/tenstorrent/tt-forge-models/blob/0a7d2042/whisper/pytorch/loader.py#L64-L66)
*   **Audio Data Source**: Loads sample audio from `hf-internal-testing/librispeech_asr_dummy` dataset [whisper/pytorch/loader.py 87-89](https://github.com/tenstorrent/tt-forge-models/blob/0a7d2042/whisper/pytorch/loader.py#L87-L89)
*   **Special Input Requirements**: Requires `decoder_input_ids` in addition to audio features [whisper/pytorch/loader.py 96-97](https://github.com/tenstorrent/tt-forge-models/blob/0a7d2042/whisper/pytorch/loader.py#L96-L97)
*   **Task Classification**: Categorized as `ModelTask.AUDIO_ASR`[whisper/pytorch/loader.py 38](https://github.com/tenstorrent/tt-forge-models/blob/0a7d2042/whisper/pytorch/loader.py#L38-L38)

Sources: [whisper/pytorch/loader.py 21-100](https://github.com/tenstorrent/tt-forge-models/blob/0a7d2042/whisper/pytorch/loader.py#L21-L100)

## Vision-Language Models

### CLIP Implementation

CLIP (Contrastive Language-Image Pre-training) processes both images and text to create aligned embeddings for cross-modal understanding tasks.

**Key Implementation Details:**

*   **Dual Input Processing**: Handles both text and image inputs simultaneously [clip/pytorch/loader.py 107-113](https://github.com/tenstorrent/tt-forge-models/blob/0a7d2042/clip/pytorch/loader.py#L107-L113)
*   **Model Source**: Uses `openai/clip-vit-base-patch32` from Hugging Face [clip/pytorch/loader.py 35](https://github.com/tenstorrent/tt-forge-models/blob/0a7d2042/clip/pytorch/loader.py#L35-L35)
*   **Sample Data**: Uses COCO dataset images for testing [clip/pytorch/loader.py 103](https://github.com/tenstorrent/tt-forge-models/blob/0a7d2042/clip/pytorch/loader.py#L103-L103)
*   **Processor Integration**: `CLIPProcessor` handles both modalities in a single call [clip/pytorch/loader.py 74-76](https://github.com/tenstorrent/tt-forge-models/blob/0a7d2042/clip/pytorch/loader.py#L74-L76)
*   **Task Classification**: Categorized as `ModelTask.MM_IMAGE_CAPT`[clip/pytorch/loader.py 54](https://github.com/tenstorrent/tt-forge-models/blob/0a7d2042/clip/pytorch/loader.py#L54-L54)

Sources: [clip/pytorch/loader.py 22-123](https://github.com/tenstorrent/tt-forge-models/blob/0a7d2042/clip/pytorch/loader.py#L22-L123)

## Visual Question Answering Models

### ViLT Implementation

ViLT (Vision-and-Language Transformer) performs visual question answering by processing images alongside natural language questions.

**Key Implementation Details:**

*   **Question-Answer Focus**: Pre-configured with sample question "How many cats are there?" [vilt/pytorch/loader.py 55](https://github.com/tenstorrent/tt-forge-models/blob/0a7d2042/vilt/pytorch/loader.py#L55-L55)
*   **Model Specialization**: Uses `dandelin/vilt-b32-finetuned-vqa` specifically tuned for VQA tasks [vilt/pytorch/loader.py 54](https://github.com/tenstorrent/tt-forge-models/blob/0a7d2042/vilt/pytorch/loader.py#L54-L54)
*   **Unified Processing**: `ViltProcessor` handles both image and text in a single call [vilt/pytorch/loader.py 91](https://github.com/tenstorrent/tt-forge-models/blob/0a7d2042/vilt/pytorch/loader.py#L91-L91)
*   **Tensor Batch Handling**: Explicitly checks for tensor types before batch replication [vilt/pytorch/loader.py 94-95](https://github.com/tenstorrent/tt-forge-models/blob/0a7d2042/vilt/pytorch/loader.py#L94-L95)
*   **Task Classification**: Categorized as `ModelTask.MM_VISUAL_QA`[vilt/pytorch/loader.py 39](https://github.com/tenstorrent/tt-forge-models/blob/0a7d2042/vilt/pytorch/loader.py#L39-L39)

Sources: [vilt/pytorch/loader.py 22-98](https://github.com/tenstorrent/tt-forge-models/blob/0a7d2042/vilt/pytorch/loader.py#L22-L98)

## Common Implementation Patterns

### Processor-Model Architecture

All multimodal models in the repository follow a consistent processor-model architecture pattern:

### Data Type and Batch Handling

All multimodal models implement consistent patterns for:

*   **Data Type Override**: Support for `dtype_override` parameter in both `load_model()` and `load_inputs()` methods
*   **Batch Size Handling**: Tensor replication using `repeat_interleave(batch_size, dim=0)` pattern
*   **Processor Initialization**: Lazy initialization of processors within model loading methods
*   **Return Format**: Consistent return of dictionaries containing processed tensor inputs

### Model Metadata Configuration

Each multimodal model implements the `_get_model_info()` method with specific task classifications:

| Model | Task Enum | Source | Framework |
| --- | --- | --- | --- |
| Whisper | `AUDIO_ASR` | `HUGGING_FACE` | `TORCH` |
| CLIP | `MM_IMAGE_CAPT` | `HUGGING_FACE` | `TORCH` |
| ViLT | `MM_VISUAL_QA` | `HUGGING_FACE` | `TORCH` |

Sources: [whisper/pytorch/loader.py 34-41](https://github.com/tenstorrent/tt-forge-models/blob/0a7d2042/whisper/pytorch/loader.py#L34-L41)[clip/pytorch/loader.py 49-57](https://github.com/tenstorrent/tt-forge-models/blob/0a7d2042/clip/pytorch/loader.py#L49-L57)[vilt/pytorch/loader.py 34-42](https://github.com/tenstorrent/tt-forge-models/blob/0a7d2042/vilt/pytorch/loader.py#L34-L42)

Dismiss
Refresh this wiki

Enter email to refresh
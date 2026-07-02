---
project: tt-forge-models
github: https://github.com/tenstorrent/tt-forge-models
deepwiki: https://deepwiki.com/tenstorrent/tt-forge-models/9-specialized-models
---

# Specialized Models

Relevant source files
*   [bi_rnn_crf/pytorch/loader.py](https://github.com/tenstorrent/tt-forge-models/blob/0a7d2042/bi_rnn_crf/pytorch/loader.py)
*   [llama/causal_lm/__init__.py](https://github.com/tenstorrent/tt-forge-models/blob/0a7d2042/llama/causal_lm/__init__.py)
*   [llama/causal_lm/pytorch/__init__.py](https://github.com/tenstorrent/tt-forge-models/blob/0a7d2042/llama/causal_lm/pytorch/__init__.py)
*   [llama/causal_lm/pytorch/loader.py](https://github.com/tenstorrent/tt-forge-models/blob/0a7d2042/llama/causal_lm/pytorch/loader.py)
*   [mamba/pytorch/__init__.py](https://github.com/tenstorrent/tt-forge-models/blob/0a7d2042/mamba/pytorch/__init__.py)
*   [mgp_str_base/pytorch/loader.py](https://github.com/tenstorrent/tt-forge-models/blob/0a7d2042/mgp_str_base/pytorch/loader.py)
*   [mnist/pytorch/loader.py](https://github.com/tenstorrent/tt-forge-models/blob/0a7d2042/mnist/pytorch/loader.py)
*   [mnist/pytorch/src/utils.py](https://github.com/tenstorrent/tt-forge-models/blob/0a7d2042/mnist/pytorch/src/utils.py)
*   [squeezebert/pytorch/loader.py](https://github.com/tenstorrent/tt-forge-models/blob/0a7d2042/squeezebert/pytorch/loader.py)
*   [vit/pytorch/__init__.py](https://github.com/tenstorrent/tt-forge-models/blob/0a7d2042/vit/pytorch/__init__.py)
*   [xglm/pytorch/__init__.py](https://github.com/tenstorrent/tt-forge-models/blob/0a7d2042/xglm/pytorch/__init__.py)

This document covers specialized models in the tt-forge-models repository that do not fit into the major categories of computer vision, natural language processing, or multimodal models. These include custom implementations, sequence labeling models, specialized text processing models, and compact variants designed for specific use cases or efficiency requirements.

For mainstream computer vision models, see [Computer Vision Models](https://deepwiki.com/tenstorrent/tt-forge-models/3-computer-vision-models). For standard NLP models, see [Natural Language Processing Models](https://deepwiki.com/tenstorrent/tt-forge-models/4-natural-language-processing-models). For models handling multiple modalities, see [Multimodal Models](https://deepwiki.com/tenstorrent/tt-forge-models/5-multimodal-models).

## Overview

Specialized models in this repository fall into several categories:

*   **Custom implementations** built from scratch for specific tasks
*   **Sequence labeling models** for token-level classification tasks
*   **Specialized text processing models** for tasks like optical character recognition
*   **Compact model variants** optimized for efficiency

These models often require custom loading logic, specialized input preprocessing, or task-specific output decoding that differs from standard model patterns.

## Model Categories and Implementation Patterns

Sources: [bi_rnn_crf/pytorch/loader.py 78-85](https://github.com/tenstorrent/tt-forge-models/blob/0a7d2042/bi_rnn_crf/pytorch/loader.py#L78-L85)[mnist/pytorch/loader.py 22-40](https://github.com/tenstorrent/tt-forge-models/blob/0a7d2042/mnist/pytorch/loader.py#L22-L40)[squeezebert/pytorch/loader.py 20-39](https://github.com/tenstorrent/tt-forge-models/blob/0a7d2042/squeezebert/pytorch/loader.py#L20-L39)[mgp_str_base/pytorch/loader.py 23-41](https://github.com/tenstorrent/tt-forge-models/blob/0a7d2042/mgp_str_base/pytorch/loader.py#L23-L41)

## Custom Model Implementations

### MNIST Digit Classification

The MNIST model provides a custom convolutional neural network implementation for digit classification, demonstrating how to integrate non-standard models into the ForgeModel framework.

The MNIST implementation follows a simplified pattern without the standard `_VARIANTS` dictionary, using a legacy variant parameter approach:

*   **Model loading**: Uses `load_model()` from [mnist/pytorch/src/utils.py 37-40](https://github.com/tenstorrent/tt-forge-models/blob/0a7d2042/mnist/pytorch/src/utils.py#L37-L40)
*   **Input generation**: Loads real MNIST test data via `load_input()` from [mnist/pytorch/src/utils.py 43-50](https://github.com/tenstorrent/tt-forge-models/blob/0a7d2042/mnist/pytorch/src/utils.py#L43-L50)
*   **Architecture**: Custom CNN with conv layers, dropout, and fully connected layers defined in [mnist/pytorch/src/utils.py 11-34](https://github.com/tenstorrent/tt-forge-models/blob/0a7d2042/mnist/pytorch/src/utils.py#L11-L34)

Sources: [mnist/pytorch/loader.py 1-77](https://github.com/tenstorrent/tt-forge-models/blob/0a7d2042/mnist/pytorch/loader.py#L1-L77)[mnist/pytorch/src/utils.py 1-51](https://github.com/tenstorrent/tt-forge-models/blob/0a7d2042/mnist/pytorch/src/utils.py#L1-L51)

### BiRNN-CRF Sequence Labeling

The BiRNN-CRF model implements bidirectional RNN with Conditional Random Field layers for sequence tagging tasks like Named Entity Recognition.

Key implementation details:

*   **Variant system**: Supports LSTM and GRU variants via [bi_rnn_crf/pytorch/loader.py 23-28](https://github.com/tenstorrent/tt-forge-models/blob/0a7d2042/bi_rnn_crf/pytorch/loader.py#L23-L28)
*   **Configuration**: Hardcoded model parameters in [bi_rnn_crf/pytorch/loader.py 56-63](https://github.com/tenstorrent/tt-forge-models/blob/0a7d2042/bi_rnn_crf/pytorch/loader.py#L56-L63)
*   **Input generation**: Random token IDs within vocabulary range [bi_rnn_crf/pytorch/loader.py 115-120](https://github.com/tenstorrent/tt-forge-models/blob/0a7d2042/bi_rnn_crf/pytorch/loader.py#L115-L120)
*   **Output decoding**: Returns emissions and tag sequences [bi_rnn_crf/pytorch/loader.py 123-139](https://github.com/tenstorrent/tt-forge-models/blob/0a7d2042/bi_rnn_crf/pytorch/loader.py#L123-L139)

Sources: [bi_rnn_crf/pytorch/loader.py 1-140](https://github.com/tenstorrent/tt-forge-models/blob/0a7d2042/bi_rnn_crf/pytorch/loader.py#L1-L140)

## Specialized Text Processing Models

### SqueezeBERT for Text Classification

SqueezeBERT provides an efficient BERT variant optimized for reduced memory usage while maintaining performance on text classification tasks.

The implementation follows standard Hugging Face patterns but with specific optimizations:

*   **Model**: Uses `AutoModelForSequenceClassification` from [squeezebert/pytorch/loader.py 73-75](https://github.com/tenstorrent/tt-forge-models/blob/0a7d2042/squeezebert/pytorch/loader.py#L73-L75)
*   **Tokenizer**: Standard `AutoTokenizer` with padding and truncation [squeezebert/pytorch/loader.py 87-94](https://github.com/tenstorrent/tt-forge-models/blob/0a7d2042/squeezebert/pytorch/loader.py#L87-L94)
*   **Output decoding**: Class prediction with label mapping [squeezebert/pytorch/loader.py 109-110](https://github.com/tenstorrent/tt-forge-models/blob/0a7d2042/squeezebert/pytorch/loader.py#L109-L110)

Sources: [squeezebert/pytorch/loader.py 1-113](https://github.com/tenstorrent/tt-forge-models/blob/0a7d2042/squeezebert/pytorch/loader.py#L1-L113)

### MGP-STR for Scene Text Recognition

MGP-STR (Multi-Granularity Prediction for Scene Text Recognition) handles optical character recognition tasks on natural scene images.

Key features:

*   **Image preprocessing**: Downloads and processes images via [mgp_str_base/pytorch/loader.py 74-83](https://github.com/tenstorrent/tt-forge-models/blob/0a7d2042/mgp_str_base/pytorch/loader.py#L74-L83)
*   **Model loading**: Uses Hugging Face transformers [mgp_str_base/pytorch/loader.py 60-62](https://github.com/tenstorrent/tt-forge-models/blob/0a7d2042/mgp_str_base/pytorch/loader.py#L60-L62)
*   **Output decoding**: Converts model outputs to readable text [mgp_str_base/pytorch/loader.py 104-106](https://github.com/tenstorrent/tt-forge-models/blob/0a7d2042/mgp_str_base/pytorch/loader.py#L104-L106)

Sources: [mgp_str_base/pytorch/loader.py 1-108](https://github.com/tenstorrent/tt-forge-models/blob/0a7d2042/mgp_str_base/pytorch/loader.py#L1-L108)

## Architecture Patterns for Specialized Models

Specialized models exhibit several common patterns that differ from mainstream implementations:

| Pattern | Description | Examples | Implementation Details |
| --- | --- | --- | --- |
| **Custom Architecture** | Models with non-standard architectures | MNIST, BiRNN-CRF | Custom model classes in `src/` directories |
| **Legacy Variant System** | Simplified variant handling | MNIST, SqueezeBERT | String-based variants instead of enum-based |
| **Specialized Input Processing** | Task-specific input preparation | MGP-STR (images), BiRNN-CRF (token generation) | Custom `load_inputs()` implementations |
| **Custom Output Decoding** | Task-specific output interpretation | All specialized models | Model-specific `decode_output()` methods |

Sources: [bi_rnn_crf/pytorch/loader.py 23-28](https://github.com/tenstorrent/tt-forge-models/blob/0a7d2042/bi_rnn_crf/pytorch/loader.py#L23-L28)[mnist/pytorch/loader.py 44-51](https://github.com/tenstorrent/tt-forge-models/blob/0a7d2042/mnist/pytorch/loader.py#L44-L51)[squeezebert/pytorch/loader.py 41-48](https://github.com/tenstorrent/tt-forge-models/blob/0a7d2042/squeezebert/pytorch/loader.py#L41-L48)[mgp_str_base/pytorch/loader.py 45-52](https://github.com/tenstorrent/tt-forge-models/blob/0a7d2042/mgp_str_base/pytorch/loader.py#L45-L52)

## Usage Patterns and Integration

Specialized models require careful attention to their unique loading and processing requirements:

### Model Instantiation

### Input Loading Considerations

*   **MNIST**: Loads real test data from torchvision datasets
*   **BiRNN-CRF**: Generates random token sequences within vocabulary
*   **MGP-STR**: Downloads and processes external images
*   **SqueezeBERT**: Uses standard text tokenization

### Output Processing

All specialized models provide `decode_output()` methods for interpreting results, but implementations vary significantly based on the task requirements.

Sources: [bi_rnn_crf/pytorch/loader.py 46-53](https://github.com/tenstorrent/tt-forge-models/blob/0a7d2042/bi_rnn_crf/pytorch/loader.py#L46-L53)[mnist/pytorch/loader.py 44-51](https://github.com/tenstorrent/tt-forge-models/blob/0a7d2042/mnist/pytorch/loader.py#L44-L51)[mgp_str_base/pytorch/loader.py 45-52](https://github.com/tenstorrent/tt-forge-models/blob/0a7d2042/mgp_str_base/pytorch/loader.py#L45-L52)[squeezebert/pytorch/loader.py 41-48](https://github.com/tenstorrent/tt-forge-models/blob/0a7d2042/squeezebert/pytorch/loader.py#L41-L48)

Dismiss
Refresh this wiki

Enter email to refresh
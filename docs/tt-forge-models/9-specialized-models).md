---
project: tt-forge-models
github: https://github.com/tenstorrent/tt-forge-models
deepwiki: https://deepwiki.com/tenstorrent/tt-forge-models/9-specialized-models)
---

# Specialized Models

 Relevant source files

* [bi\_rnn\_crf/pytorch/loader.py](https://github.com/tenstorrent/tt-forge-models/blob/0a7d2042/bi_rnn_crf/pytorch/loader.py)
* [llama/causal\_lm/\_\_init\_\_.py](https://github.com/tenstorrent/tt-forge-models/blob/0a7d2042/llama/causal_lm/__init__.py)
* [llama/causal\_lm/pytorch/\_\_init\_\_.py](https://github.com/tenstorrent/tt-forge-models/blob/0a7d2042/llama/causal_lm/pytorch/__init__.py)
* [llama/causal\_lm/pytorch/loader.py](https://github.com/tenstorrent/tt-forge-models/blob/0a7d2042/llama/causal_lm/pytorch/loader.py)
* [mamba/pytorch/\_\_init\_\_.py](https://github.com/tenstorrent/tt-forge-models/blob/0a7d2042/mamba/pytorch/__init__.py)
* [mgp\_str\_base/pytorch/loader.py](https://github.com/tenstorrent/tt-forge-models/blob/0a7d2042/mgp_str_base/pytorch/loader.py)
* [mnist/pytorch/loader.py](https://github.com/tenstorrent/tt-forge-models/blob/0a7d2042/mnist/pytorch/loader.py)
* [mnist/pytorch/src/utils.py](https://github.com/tenstorrent/tt-forge-models/blob/0a7d2042/mnist/pytorch/src/utils.py)
* [squeezebert/pytorch/loader.py](https://github.com/tenstorrent/tt-forge-models/blob/0a7d2042/squeezebert/pytorch/loader.py)
* [vit/pytorch/\_\_init\_\_.py](https://github.com/tenstorrent/tt-forge-models/blob/0a7d2042/vit/pytorch/__init__.py)
* [xglm/pytorch/\_\_init\_\_.py](https://github.com/tenstorrent/tt-forge-models/blob/0a7d2042/xglm/pytorch/__init__.py)

This document covers specialized models in the tt-forge-models repository that do not fit into the major categories of computer vision, natural language processing, or multimodal models. These include custom implementations, sequence labeling models, specialized text processing models, and compact variants designed for specific use cases or efficiency requirements.

For mainstream computer vision models, see [Computer Vision Models](/tenstorrent/tt-forge-models/3-computer-vision-models). For standard NLP models, see [Natural Language Processing Models](/tenstorrent/tt-forge-models/4-natural-language-processing-models). For models handling multiple modalities, see [Multimodal Models](/tenstorrent/tt-forge-models/5-multimodal-models).

## Overview

Specialized models in this repository fall into several categories:

* **Custom implementations** built from scratch for specific tasks
* **Sequence labeling models** for token-level classification tasks
* **Specialized text processing models** for tasks like optical character recognition
* **Compact model variants** optimized for efficiency

These models often require custom loading logic, specialized input preprocessing, or task-specific output decoding that differs from standard model patterns.

## Model Categories and Implementation Patterns

Sources: [bi\_rnn\_crf/pytorch/loader.py78-85](https://github.com/tenstorrent/tt-forge-models/blob/0a7d2042/bi_rnn_crf/pytorch/loader.py#L78-L85) [mnist/pytorch/loader.py22-40](https://github.com/tenstorrent/tt-forge-models/blob/0a7d2042/mnist/pytorch/loader.py#L22-L40) [squeezebert/pytorch/loader.py20-39](https://github.com/tenstorrent/tt-forge-models/blob/0a7d2042/squeezebert/pytorch/loader.py#L20-L39) [mgp\_str\_base/pytorch/loader.py23-41](https://github.com/tenstorrent/tt-forge-models/blob/0a7d2042/mgp_str_base/pytorch/loader.py#L23-L41)

## Custom Model Implementations

### MNIST Digit Classification

The MNIST model provides a custom convolutional neural network implementation for digit classification, demonstrating how to integrate non-standard models into the ForgeModel framework.

The MNIST implementation follows a simplified pattern without the standard `_VARIANTS` dictionary, using a legacy variant parameter approach:

* **Model loading**: Uses `load_model()` from [mnist/pytorch/src/utils.py37-40](https://github.com/tenstorrent/tt-forge-models/blob/0a7d2042/mnist/pytorch/src/utils.py#L37-L40)
* **Input generation**: Loads real MNIST test data via `load_input()` from [mnist/pytorch/src/utils.py43-50](https://github.com/tenstorrent/tt-forge-models/blob/0a7d2042/mnist/pytorch/src/utils.py#L43-L50)
* **Architecture**: Custom CNN with conv layers, dropout, and fully connected layers defined in [mnist/pytorch/src/utils.py11-34](https://github.com/tenstorrent/tt-forge-models/blob/0a7d2042/mnist/pytorch/src/utils.py#L11-L34)

Sources: [mnist/pytorch/loader.py1-77](https://github.com/tenstorrent/tt-forge-models/blob/0a7d2042/mnist/pytorch/loader.py#L1-L77) [mnist/pytorch/src/utils.py1-51](https://github.com/tenstorrent/tt-forge-models/blob/0a7d2042/mnist/pytorch/src/utils.py#L1-L51)

### BiRNN-CRF Sequence Labeling

The BiRNN-CRF model implements bidirectional RNN with Conditional Random Field layers for sequence tagging tasks like Named Entity Recognition.

Key implementation details:

* **Variant system**: Supports LSTM and GRU variants via [bi\_rnn\_crf/pytorch/loader.py23-28](https://github.com/tenstorrent/tt-forge-models/blob/0a7d2042/bi_rnn_crf/pytorch/loader.py#L23-L28)
* **Configuration**: Hardcoded model parameters in [bi\_rnn\_crf/pytorch/loader.py56-63](https://github.com/tenstorrent/tt-forge-models/blob/0a7d2042/bi_rnn_crf/pytorch/loader.py#L56-L63)
* **Input generation**: Random token IDs within vocabulary range [bi\_rnn\_crf/pytorch/loader.py115-120](https://github.com/tenstorrent/tt-forge-models/blob/0a7d2042/bi_rnn_crf/pytorch/loader.py#L115-L120)
* **Output decoding**: Returns emissions and tag sequences [bi\_rnn\_crf/pytorch/loader.py123-139](https://github.com/tenstorrent/tt-forge-models/blob/0a7d2042/bi_rnn_crf/pytorch/loader.py#L123-L139)

Sources: [bi\_rnn\_crf/pytorch/loader.py1-140](https://github.com/tenstorrent/tt-forge-models/blob/0a7d2042/bi_rnn_crf/pytorch/loader.py#L1-L140)

## Specialized Text Processing Models

### SqueezeBERT for Text Classification

SqueezeBERT provides an efficient BERT variant optimized for reduced memory usage while maintaining performance on text classification tasks.

The implementation follows standard Hugging Face patterns but with specific optimizations:

* **Model**: Uses `AutoModelForSequenceClassification` from [squeezebert/pytorch/loader.py73-75](https://github.com/tenstorrent/tt-forge-models/blob/0a7d2042/squeezebert/pytorch/loader.py#L73-L75)
* **Tokenizer**: Standard `AutoTokenizer` with padding and truncation [squeezebert/pytorch/loader.py87-94](https://github.com/tenstorrent/tt-forge-models/blob/0a7d2042/squeezebert/pytorch/loader.py#L87-L94)
* **Output decoding**: Class prediction with label mapping [squeezebert/pytorch/loader.py109-110](https://github.com/tenstorrent/tt-forge-models/blob/0a7d2042/squeezebert/pytorch/loader.py#L109-L110)

Sources: [squeezebert/pytorch/loader.py1-113](https://github.com/tenstorrent/tt-forge-models/blob/0a7d2042/squeezebert/pytorch/loader.py#L1-L113)

### MGP-STR for Scene Text Recognition

MGP-STR (Multi-Granularity Prediction for Scene Text Recognition) handles optical character recognition tasks on natural scene images.

Key features:

* **Image preprocessing**: Downloads and processes images via [mgp\_str\_base/pytorch/loader.py74-83](https://github.com/tenstorrent/tt-forge-models/blob/0a7d2042/mgp_str_base/pytorch/loader.py#L74-L83)
* **Model loading**: Uses Hugging Face transformers [mgp\_str\_base/pytorch/loader.py60-62](https://github.com/tenstorrent/tt-forge-models/blob/0a7d2042/mgp_str_base/pytorch/loader.py#L60-L62)
* **Output decoding**: Converts model outputs to readable text [mgp\_str\_base/pytorch/loader.py104-106](https://github.com/tenstorrent/tt-forge-models/blob/0a7d2042/mgp_str_base/pytorch/loader.py#L104-L106)

Sources: [mgp\_str\_base/pytorch/loader.py1-108](https://github.com/tenstorrent/tt-forge-models/blob/0a7d2042/mgp_str_base/pytorch/loader.py#L1-L108)

## Architecture Patterns for Specialized Models

Specialized models exhibit several common patterns that differ from mainstream implementations:

| Pattern | Description | Examples | Implementation Details |
| --- | --- | --- | --- |
| **Custom Architecture** | Models with non-standard architectures | MNIST, BiRNN-CRF | Custom model classes in `src/` directories |
| **Legacy Variant System** | Simplified variant handling | MNIST, SqueezeBERT | String-based variants instead of enum-based |
| **Specialized Input Processing** | Task-specific input preparation | MGP-STR (images), BiRNN-CRF (token generation) | Custom `load_inputs()` implementations |
| **Custom Output Decoding** | Task-specific output interpretation | All specialized models | Model-specific `decode_output()` methods |

Sources: [bi\_rnn\_crf/pytorch/loader.py23-28](https://github.com/tenstorrent/tt-forge-models/blob/0a7d2042/bi_rnn_crf/pytorch/loader.py#L23-L28) [mnist/pytorch/loader.py44-51](https://github.com/tenstorrent/tt-forge-models/blob/0a7d2042/mnist/pytorch/loader.py#L44-L51) [squeezebert/pytorch/loader.py41-48](https://github.com/tenstorrent/tt-forge-models/blob/0a7d2042/squeezebert/pytorch/loader.py#L41-L48) [mgp\_str\_base/pytorch/loader.py45-52](https://github.com/tenstorrent/tt-forge-models/blob/0a7d2042/mgp_str_base/pytorch/loader.py#L45-L52)

## Usage Patterns and Integration

Specialized models require careful attention to their unique loading and processing requirements:

### Model Instantiation

### Input Loading Considerations

* **MNIST**: Loads real test data from torchvision datasets
* **BiRNN-CRF**: Generates random token sequences within vocabulary
* **MGP-STR**: Downloads and processes external images
* **SqueezeBERT**: Uses standard text tokenization

### Output Processing

All specialized models provide `decode_output()` methods for interpreting results, but implementations vary significantly based on the task requirements.

Sources: [bi\_rnn\_crf/pytorch/loader.py46-53](https://github.com/tenstorrent/tt-forge-models/blob/0a7d2042/bi_rnn_crf/pytorch/loader.py#L46-L53) [mnist/pytorch/loader.py44-51](https://github.com/tenstorrent/tt-forge-models/blob/0a7d2042/mnist/pytorch/loader.py#L44-L51) [mgp\_str\_base/pytorch/loader.py45-52](https://github.com/tenstorrent/tt-forge-models/blob/0a7d2042/mgp_str_base/pytorch/loader.py#L45-L52) [squeezebert/pytorch/loader.py41-48](https://github.com/tenstorrent/tt-forge-models/blob/0a7d2042/squeezebert/pytorch/loader.py#L41-L48)

Refresh this wiki
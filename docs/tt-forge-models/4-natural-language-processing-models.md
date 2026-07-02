---
project: tt-forge-models
github: https://github.com/tenstorrent/tt-forge-models
deepwiki: https://deepwiki.com/tenstorrent/tt-forge-models/4-natural-language-processing-models
---

# Natural Language Processing Models

Relevant source files
*   [autoencoder_linear/pytorch/loader.py](https://github.com/tenstorrent/tt-forge-models/blob/0a7d2042/autoencoder_linear/pytorch/loader.py)
*   [bloom/pytorch/loader.py](https://github.com/tenstorrent/tt-forge-models/blob/0a7d2042/bloom/pytorch/loader.py)
*   [clip/pytorch/loader.py](https://github.com/tenstorrent/tt-forge-models/blob/0a7d2042/clip/pytorch/loader.py)
*   [codegen/pytorch/loader.py](https://github.com/tenstorrent/tt-forge-models/blob/0a7d2042/codegen/pytorch/loader.py)
*   [deit/pytorch/loader.py](https://github.com/tenstorrent/tt-forge-models/blob/0a7d2042/deit/pytorch/loader.py)
*   [dpr/pytorch/loader.py](https://github.com/tenstorrent/tt-forge-models/blob/0a7d2042/dpr/pytorch/loader.py)
*   [falcon/pytorch/loader.py](https://github.com/tenstorrent/tt-forge-models/blob/0a7d2042/falcon/pytorch/loader.py)
*   [gliner_model/pytorch/loader.py](https://github.com/tenstorrent/tt-forge-models/blob/0a7d2042/gliner_model/pytorch/loader.py)
*   [llama/pytorch/loader.py](https://github.com/tenstorrent/tt-forge-models/blob/0a7d2042/llama/pytorch/loader.py)
*   [mistral/pytorch/loader.py](https://github.com/tenstorrent/tt-forge-models/blob/0a7d2042/mistral/pytorch/loader.py)
*   [mlp_mixer/lucidrains/__init__.py](https://github.com/tenstorrent/tt-forge-models/blob/0a7d2042/mlp_mixer/lucidrains/__init__.py)
*   [mlp_mixer/lucidrains/pytorch/__init__.py](https://github.com/tenstorrent/tt-forge-models/blob/0a7d2042/mlp_mixer/lucidrains/pytorch/__init__.py)
*   [mlp_mixer/lucidrains/pytorch/loader.py](https://github.com/tenstorrent/tt-forge-models/blob/0a7d2042/mlp_mixer/lucidrains/pytorch/loader.py)
*   [roberta/masked_lm/__init__.py](https://github.com/tenstorrent/tt-forge-models/blob/0a7d2042/roberta/masked_lm/__init__.py)
*   [roberta/masked_lm/pytorch/__init__.py](https://github.com/tenstorrent/tt-forge-models/blob/0a7d2042/roberta/masked_lm/pytorch/__init__.py)
*   [roberta/masked_lm/pytorch/loader.py](https://github.com/tenstorrent/tt-forge-models/blob/0a7d2042/roberta/masked_lm/pytorch/loader.py)
*   [t5/pytorch/loader.py](https://github.com/tenstorrent/tt-forge-models/blob/0a7d2042/t5/pytorch/loader.py)
*   [unet/pytorch/loader.py](https://github.com/tenstorrent/tt-forge-models/blob/0a7d2042/unet/pytorch/loader.py)
*   [whisper/pytorch/loader.py](https://github.com/tenstorrent/tt-forge-models/blob/0a7d2042/whisper/pytorch/loader.py)

This document covers the natural language processing (NLP) models available in the tt-forge-models repository. These models handle various text-based tasks including language generation, text understanding, question answering, and token classification. All NLP models follow the standardized `ForgeModel` interface pattern described in [Core Architecture](https://deepwiki.com/tenstorrent/tt-forge-models/2-core-architecture).

For computer vision models, see [Computer Vision Models](https://deepwiki.com/tenstorrent/tt-forge-models/3-computer-vision-models). For models that combine text and other modalities, see [Multimodal Models](https://deepwiki.com/tenstorrent/tt-forge-models/5-multimodal-models).

## NLP Model Categories

The repository organizes NLP models by their primary task types, as defined in the `ModelTask` enumeration. The main categories implemented are:

| Task Category | ModelTask Enum | Description | Example Models |
| --- | --- | --- | --- |
| Causal Language Modeling | `NLP_CAUSAL_LM` | Auto-regressive text generation | Mistral, Llama, T5, Falcon |
| Masked Language Modeling | `NLP_MASKED_LM` | Bidirectional text understanding | RoBERTa, BERT variants |
| Question Answering | `NLP_QA` | Information retrieval and QA | DPR (Dense Passage Retrieval) |
| Token Classification | `NLP_TOKEN_CLS` | Named entity recognition, POS tagging | GLiNER |

Sources: [mistral/pytorch/loader.py 23-28](https://github.com/tenstorrent/tt-forge-models/blob/0a7d2042/mistral/pytorch/loader.py#L23-L28)[t5/pytorch/loader.py 37](https://github.com/tenstorrent/tt-forge-models/blob/0a7d2042/t5/pytorch/loader.py#L37-L37)[roberta/masked_lm/pytorch/loader.py 67](https://github.com/tenstorrent/tt-forge-models/blob/0a7d2042/roberta/masked_lm/pytorch/loader.py#L67-L67)[dpr/pytorch/loader.py 36](https://github.com/tenstorrent/tt-forge-models/blob/0a7d2042/dpr/pytorch/loader.py#L36-L36)[gliner_model/pytorch/loader.py 51](https://github.com/tenstorrent/tt-forge-models/blob/0a7d2042/gliner_model/pytorch/loader.py#L51-L51)

## Causal Language Models

Causal language models perform auto-regressive text generation, predicting the next token given previous context. These models are commonly used for text completion, dialogue, and code generation tasks.

### Model Implementations

The repository includes several major causal language model families:

Sources: [mistral/pytorch/loader.py 119-121](https://github.com/tenstorrent/tt-forge-models/blob/0a7d2042/mistral/pytorch/loader.py#L119-L121)[falcon/pytorch/loader.py 83-85](https://github.com/tenstorrent/tt-forge-models/blob/0a7d2042/falcon/pytorch/loader.py#L83-L85)[bloom/pytorch/loader.py 82](https://github.com/tenstorrent/tt-forge-models/blob/0a7d2042/bloom/pytorch/loader.py#L82-L82)[codegen/pytorch/loader.py 74](https://github.com/tenstorrent/tt-forge-models/blob/0a7d2042/codegen/pytorch/loader.py#L74-L74)[t5/pytorch/loader.py 73-75](https://github.com/tenstorrent/tt-forge-models/blob/0a7d2042/t5/pytorch/loader.py#L73-L75)

### Variant Configuration System

Several causal language models implement sophisticated variant systems using `ModelVariant` enums and `_VARIANTS` dictionaries:

**Mistral Model Variants:**

*   `MISTRAL_7B`: "mistralai/Mistral-7B-v0.1"
*   `MINISTRAL_3B`: "ministral/Ministral-3b-instruct"
*   `MINISTRAL_8B`: "mistralai/Ministral-8B-Instruct-2410"

The variant system allows switching between different model sizes and configurations:

Sources: [mistral/pytorch/loader.py 35-45](https://github.com/tenstorrent/tt-forge-models/blob/0a7d2042/mistral/pytorch/loader.py#L35-L45)[mistral/pytorch/loader.py 48](https://github.com/tenstorrent/tt-forge-models/blob/0a7d2042/mistral/pytorch/loader.py#L48-L48)

### Text Generation Pipeline

All causal language models follow a consistent pattern for text generation:

1.   **Tokenization**: Convert input text to token IDs using `AutoTokenizer`
2.   **Model Forward Pass**: Generate logits for next token prediction
3.   **Decoding**: Convert output logits back to human-readable text

Sources: [mistral/pytorch/loader.py 142-143](https://github.com/tenstorrent/tt-forge-models/blob/0a7d2042/mistral/pytorch/loader.py#L142-L143)[bloom/pytorch/loader.py 107-114](https://github.com/tenstorrent/tt-forge-models/blob/0a7d2042/bloom/pytorch/loader.py#L107-L114)[codegen/pytorch/loader.py 95-96](https://github.com/tenstorrent/tt-forge-models/blob/0a7d2042/codegen/pytorch/loader.py#L95-L96)

## Masked Language Models

Masked language models use bidirectional context to predict masked tokens, enabling deep text understanding tasks. These models follow the BERT architecture pattern.

### RoBERTa Implementation

The repository implements XLM-RoBERTa for masked language modeling:

**Model Configuration:**

*   Base model: `FacebookAI/xlm-roberta-base`
*   Task: `ModelTask.NLP_MASKED_LM`
*   Architecture: `XLMRobertaForMaskedLM`

**Key Implementation Details:**

The `decode_output()` method specifically handles mask token prediction:

1.   Locate `<mask>` token position in input
2.   Extract logits for that position
3.   Apply `argmax` to get predicted token ID
4.   Decode token ID back to text

Sources: [roberta/masked_lm/pytorch/loader.py 106-107](https://github.com/tenstorrent/tt-forge-models/blob/0a7d2042/roberta/masked_lm/pytorch/loader.py#L106-L107)[roberta/masked_lm/pytorch/loader.py 127](https://github.com/tenstorrent/tt-forge-models/blob/0a7d2042/roberta/masked_lm/pytorch/loader.py#L127-L127)[roberta/masked_lm/pytorch/loader.py 162-170](https://github.com/tenstorrent/tt-forge-models/blob/0a7d2042/roberta/masked_lm/pytorch/loader.py#L162-L170)

## Question Answering Models

Question answering models retrieve and process information to answer queries. The repository implements Dense Passage Retrieval (DPR) for this task.

### DPR Implementation

DPR uses dense vector representations for context encoding and retrieval:

**Model Configuration:**

*   Base model: `facebook/dpr-ctx_encoder-multiset-base`
*   Architecture: `DPRContextEncoder`
*   Task: `ModelTask.NLP_QA`

**Context Encoding Process:**

The model produces dense embeddings that can be used for similarity search and passage retrieval in question answering systems.

Sources: [dpr/pytorch/loader.py 64-65](https://github.com/tenstorrent/tt-forge-models/blob/0a7d2042/dpr/pytorch/loader.py#L64-L65)[dpr/pytorch/loader.py 73-75](https://github.com/tenstorrent/tt-forge-models/blob/0a7d2042/dpr/pytorch/loader.py#L73-L75)[dpr/pytorch/loader.py 87-93](https://github.com/tenstorrent/tt-forge-models/blob/0a7d2042/dpr/pytorch/loader.py#L87-L93)

## Token Classification Models

Token classification models assign labels to individual tokens for tasks like named entity recognition (NER) and part-of-speech tagging.

### GLiNER Implementation

GLiNER (Generalist and Lightweight Named Entity Recognition) provides flexible NER capabilities:

**Model Configuration:**

*   Base model: `urchade/gliner_largev2`
*   Task: `ModelTask.NLP_TOKEN_CLS`
*   Architecture: Custom GLiNER model

**Entity Recognition Pipeline:**

The model accepts both text and entity labels as input, allowing flexible entity recognition:

The `load_model()` method returns `model.batch_predict_entities`, indicating batch processing capabilities for entity extraction.

Sources: [gliner_model/pytorch/loader.py 33](https://github.com/tenstorrent/tt-forge-models/blob/0a7d2042/gliner_model/pytorch/loader.py#L33-L33)[gliner_model/pytorch/loader.py 62-63](https://github.com/tenstorrent/tt-forge-models/blob/0a7d2042/gliner_model/pytorch/loader.py#L62-L63)[gliner_model/pytorch/loader.py 74-82](https://github.com/tenstorrent/tt-forge-models/blob/0a7d2042/gliner_model/pytorch/loader.py#L74-L82)

## Common Implementation Patterns

All NLP models in the repository follow consistent patterns inherited from the `ForgeModel` base class:

### Standard ModelLoader Structure

### Tokenizer Initialization Pattern

Most NLP models follow this tokenizer initialization pattern:

1.   **Lazy Loading**: Tokenizers are initialized in `load_model()` or when first needed
2.   **Dtype Override Support**: Optional `dtype_override` parameter for precision control
3.   **Model-Specific Configuration**: Custom tokenizer parameters (e.g., padding_side)

### Batch Processing Support

All models implement batch processing through the `batch_size` parameter in `load_inputs()`:

Sources: [mistral/pytorch/loader.py 86-94](https://github.com/tenstorrent/tt-forge-models/blob/0a7d2042/mistral/pytorch/loader.py#L86-L94)[bloom/pytorch/loader.py 116-118](https://github.com/tenstorrent/tt-forge-models/blob/0a7d2042/bloom/pytorch/loader.py#L116-L118)[t5/pytorch/loader.py 64-66](https://github.com/tenstorrent/tt-forge-models/blob/0a7d2042/t5/pytorch/loader.py#L64-L66)

Dismiss
Refresh this wiki

Enter email to refresh
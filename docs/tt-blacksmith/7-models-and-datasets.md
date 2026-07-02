---
project: tt-blacksmith
github: https://github.com/tenstorrent/tt-blacksmith
deepwiki: https://deepwiki.com/tenstorrent/tt-blacksmith/7-models-and-datasets
---

# Models and Datasets

Relevant source files
*   [blacksmith/datasets/torch/BOUNTIES/__init__.py](https://github.com/tenstorrent/tt-blacksmith/blob/fe25eba0/blacksmith/datasets/torch/BOUNTIES/__init__.py)
*   [blacksmith/datasets/torch/BOUNTIES/wikitext/__init__.py](https://github.com/tenstorrent/tt-blacksmith/blob/fe25eba0/blacksmith/datasets/torch/BOUNTIES/wikitext/__init__.py)
*   [blacksmith/datasets/torch/BOUNTIES/wikitext/wikitext_dataset.py](https://github.com/tenstorrent/tt-blacksmith/blob/fe25eba0/blacksmith/datasets/torch/BOUNTIES/wikitext/wikitext_dataset.py)
*   [blacksmith/datasets/torch/banking77/banking77_dataset.py](https://github.com/tenstorrent/tt-blacksmith/blob/fe25eba0/blacksmith/datasets/torch/banking77/banking77_dataset.py)
*   [blacksmith/datasets/torch/dataset_utils.py](https://github.com/tenstorrent/tt-blacksmith/blob/fe25eba0/blacksmith/datasets/torch/dataset_utils.py)
*   [blacksmith/datasets/torch/mnist/mnist_dataset.py](https://github.com/tenstorrent/tt-blacksmith/blob/fe25eba0/blacksmith/datasets/torch/mnist/mnist_dataset.py)
*   [blacksmith/datasets/torch/nerf/blender.py](https://github.com/tenstorrent/tt-blacksmith/blob/fe25eba0/blacksmith/datasets/torch/nerf/blender.py)
*   [blacksmith/datasets/torch/squadV2/squadV2_dataset.py](https://github.com/tenstorrent/tt-blacksmith/blob/fe25eba0/blacksmith/datasets/torch/squadV2/squadV2_dataset.py)
*   [blacksmith/datasets/torch/sst2/sst2_dataset.py](https://github.com/tenstorrent/tt-blacksmith/blob/fe25eba0/blacksmith/datasets/torch/sst2/sst2_dataset.py)
*   [blacksmith/datasets/torch/text2sql/text2sql_dataset.py](https://github.com/tenstorrent/tt-blacksmith/blob/fe25eba0/blacksmith/datasets/torch/text2sql/text2sql_dataset.py)
*   [blacksmith/datasets/torch/torch_dataset.py](https://github.com/tenstorrent/tt-blacksmith/blob/fe25eba0/blacksmith/datasets/torch/torch_dataset.py)
*   [blacksmith/experiments/torch/BOUNTIES/__init__.py](https://github.com/tenstorrent/tt-blacksmith/blob/fe25eba0/blacksmith/experiments/torch/BOUNTIES/__init__.py)
*   [blacksmith/experiments/torch/BOUNTIES/falcon3_1b/README.md](https://github.com/tenstorrent/tt-blacksmith/blob/fe25eba0/blacksmith/experiments/torch/BOUNTIES/falcon3_1b/README.md?plain=1)
*   [blacksmith/experiments/torch/BOUNTIES/falcon3_1b/__init__.py](https://github.com/tenstorrent/tt-blacksmith/blob/fe25eba0/blacksmith/experiments/torch/BOUNTIES/falcon3_1b/__init__.py)
*   [blacksmith/experiments/torch/BOUNTIES/falcon3_1b/configs.py](https://github.com/tenstorrent/tt-blacksmith/blob/fe25eba0/blacksmith/experiments/torch/BOUNTIES/falcon3_1b/configs.py)
*   [blacksmith/experiments/torch/BOUNTIES/falcon3_1b/test_falcon3_finetuning.py](https://github.com/tenstorrent/tt-blacksmith/blob/fe25eba0/blacksmith/experiments/torch/BOUNTIES/falcon3_1b/test_falcon3_finetuning.py)
*   [blacksmith/experiments/torch/BOUNTIES/falcon3_1b/test_falcon3_finetuning.yaml](https://github.com/tenstorrent/tt-blacksmith/blob/fe25eba0/blacksmith/experiments/torch/BOUNTIES/falcon3_1b/test_falcon3_finetuning.yaml)
*   [blacksmith/models/torch/huggingface/hf_models.py](https://github.com/tenstorrent/tt-blacksmith/blob/fe25eba0/blacksmith/models/torch/huggingface/hf_models.py)
*   [blacksmith/tools/cli.py](https://github.com/tenstorrent/tt-blacksmith/blob/fe25eba0/blacksmith/tools/cli.py)
*   [blacksmith/tools/templates/configs.py](https://github.com/tenstorrent/tt-blacksmith/blob/fe25eba0/blacksmith/tools/templates/configs.py)

This document provides an overview of the model loading and dataset systems in TT-Blacksmith. The repository uses factory patterns to provide consistent interfaces for loading models from Hugging Face and preparing datasets for training. Both systems integrate seamlessly with the training infrastructure through the `TrainingConfig` configuration object.

For detailed information on model loading, LoRA integration, and adapter implementations, see page 7.1 Model Loading and Adaptation. For specific dataset implementations and the BaseDataset pattern, see page 7.2 Dataset Implementations.

For information about how datasets integrate with the training loop, see page 5 Training Framework. For details on data parallelism and batch sharding, see page 8.1 Data Parallelism.

## Dataset Architecture

All PyTorch dataset implementations in TT-Blacksmith inherit from the `BaseDataset` abstract class, which provides a consistent interface and automatic test mode support.

**Sources:**

*   [blacksmith/datasets/torch/torch_dataset.py 42-99](https://github.com/tenstorrent/tt-blacksmith/blob/fe25eba0/blacksmith/datasets/torch/torch_dataset.py#L42-L99)
*   [blacksmith/datasets/torch/squadV2/squadV2_dataset.py 24](https://github.com/tenstorrent/tt-blacksmith/blob/fe25eba0/blacksmith/datasets/torch/squadV2/squadV2_dataset.py#L24-L24)
*   [blacksmith/datasets/torch/text2sql/text2sql_dataset.py 25](https://github.com/tenstorrent/tt-blacksmith/blob/fe25eba0/blacksmith/datasets/torch/text2sql/text2sql_dataset.py#L25-L25)

### BaseDataset Abstract Class

The `BaseDataset` class [blacksmith/datasets/torch/torch_dataset.py 42-99](https://github.com/tenstorrent/tt-blacksmith/blob/fe25eba0/blacksmith/datasets/torch/torch_dataset.py#L42-L99) defines the contract for all dataset implementations:

| Method | Purpose | Required |
| --- | --- | --- |
| `_prepare_dataset()` | Load and prepare raw data | Abstract |
| `_get_dataloader()` | Create DataLoader with collation | Abstract |
| `__getitem__(idx)` | Return single example | Concrete |
| `__len__()` | Return dataset size | Concrete |
| `get_dataloader()` | Public API with test mode support | Concrete |

The `get_dataloader()` method [blacksmith/datasets/torch/torch_dataset.py 97-98](https://github.com/tenstorrent/tt-blacksmith/blob/fe25eba0/blacksmith/datasets/torch/torch_dataset.py#L97-L98) wraps the DataLoader with optional `TestDataLoaderWrapper` to limit batches during testing when `config.test_config.max_steps_per_epoch` is set.

### TestDataLoaderWrapper

The `TestDataLoaderWrapper`[blacksmith/datasets/torch/torch_dataset.py 13-40](https://github.com/tenstorrent/tt-blacksmith/blob/fe25eba0/blacksmith/datasets/torch/torch_dataset.py#L13-L40) uses `itertools.islice` to limit the number of batches processed per epoch for fast testing:

`class TestDataLoaderWrapper:    def __init__(self, dataloader: DataLoader, max_steps_per_epoch: int):        self.dataloader = dataloader        self.max_steps_per_epoch = max_steps_per_epoch        def __iter__(self):        return islice(iter(self.dataloader), self.max_steps_per_epoch)`
**Sources:**

*   [blacksmith/datasets/torch/torch_dataset.py 13-40](https://github.com/tenstorrent/tt-blacksmith/blob/fe25eba0/blacksmith/datasets/torch/torch_dataset.py#L13-L40)
*   [blacksmith/datasets/torch/torch_dataset.py 76-98](https://github.com/tenstorrent/tt-blacksmith/blob/fe25eba0/blacksmith/datasets/torch/torch_dataset.py#L76-L98)

## Dataset Implementations

### Question Answering: SQuAD V2

The `SquadV2Dataset`[blacksmith/datasets/torch/squadV2/squadV2_dataset.py 24-116](https://github.com/tenstorrent/tt-blacksmith/blob/fe25eba0/blacksmith/datasets/torch/squadV2/squadV2_dataset.py#L24-L116) implements question answering with context and handles unanswerable questions:

**Key features:**

*   **Prompt template**: Formats context and question [blacksmith/datasets/torch/squadV2/squadV2_dataset.py 14-20](https://github.com/tenstorrent/tt-blacksmith/blob/fe25eba0/blacksmith/datasets/torch/squadV2/squadV2_dataset.py#L14-L20)
*   **Label masking**: Masks prompt tokens with `-100` to exclude from loss [blacksmith/datasets/torch/squadV2/squadV2_dataset.py 60-64](https://github.com/tenstorrent/tt-blacksmith/blob/fe25eba0/blacksmith/datasets/torch/squadV2/squadV2_dataset.py#L60-L64)
*   **Unanswerable handling**: Assigns `"unanswerable"` for questions without answers [blacksmith/datasets/torch/squadV2/squadV2_dataset.py 46-51](https://github.com/tenstorrent/tt-blacksmith/blob/fe25eba0/blacksmith/datasets/torch/squadV2/squadV2_dataset.py#L46-L51)
*   **Validation reduction**: Uses 2% of validation split for efficiency [blacksmith/datasets/torch/squadV2/squadV2_dataset.py 78-79](https://github.com/tenstorrent/tt-blacksmith/blob/fe25eba0/blacksmith/datasets/torch/squadV2/squadV2_dataset.py#L78-L79)

**Sources:**

*   [blacksmith/datasets/torch/squadV2/squadV2_dataset.py 24-116](https://github.com/tenstorrent/tt-blacksmith/blob/fe25eba0/blacksmith/datasets/torch/squadV2/squadV2_dataset.py#L24-L116)

### Text-to-SQL Generation

The `TextToSQLDataset`[blacksmith/datasets/torch/text2sql/text2sql_dataset.py 25-107](https://github.com/tenstorrent/tt-blacksmith/blob/fe25eba0/blacksmith/datasets/torch/text2sql/text2sql_dataset.py#L25-L107) converts natural language questions to SQL queries:

**Prompt template**[blacksmith/datasets/torch/text2sql/text2sql_dataset.py 14-21](https://github.com/tenstorrent/tt-blacksmith/blob/fe25eba0/blacksmith/datasets/torch/text2sql/text2sql_dataset.py#L14-L21):

```
### Instruction:
Generate an SQL query for the given question and database schema.

### Input:
Question: $prompt
Schema: $context

### Output:
```

**Processing steps:**

1.   Load from `gretelai/synthetic_text_to_sql`[blacksmith/datasets/torch/text2sql/text2sql_dataset.py 22](https://github.com/tenstorrent/tt-blacksmith/blob/fe25eba0/blacksmith/datasets/torch/text2sql/text2sql_dataset.py#L22-L22)
2.   Filter to `"basic SQL"` complexity [blacksmith/datasets/torch/text2sql/text2sql_dataset.py 71](https://github.com/tenstorrent/tt-blacksmith/blob/fe25eba0/blacksmith/datasets/torch/text2sql/text2sql_dataset.py#L71-L71)
3.   Tokenize with prompt masking [blacksmith/datasets/torch/text2sql/text2sql_dataset.py 41-67](https://github.com/tenstorrent/tt-blacksmith/blob/fe25eba0/blacksmith/datasets/torch/text2sql/text2sql_dataset.py#L41-L67)
4.   Apply `DataCollatorForSeq2Seq`[blacksmith/datasets/torch/text2sql/text2sql_dataset.py 92-94](https://github.com/tenstorrent/tt-blacksmith/blob/fe25eba0/blacksmith/datasets/torch/text2sql/text2sql_dataset.py#L92-L94)

**Sources:**

*   [blacksmith/datasets/torch/text2sql/text2sql_dataset.py 25-107](https://github.com/tenstorrent/tt-blacksmith/blob/fe25eba0/blacksmith/datasets/torch/text2sql/text2sql_dataset.py#L25-L107)

### Sentiment Analysis: SST-2

The `SSTDataset`[blacksmith/datasets/torch/sst2/sst2_dataset.py 20-98](https://github.com/tenstorrent/tt-blacksmith/blob/fe25eba0/blacksmith/datasets/torch/sst2/sst2_dataset.py#L20-L98) performs binary sentiment classification:

| Component | Implementation |
| --- | --- |
| Dataset source | GLUE benchmark, SST-2 split |
| Label mapping | `LBL2VALUE` dict (0→negative, 1→positive) |
| Prompt template | `PROMPT_TEMPLATE.substitute(input=sentence)` |
| Response template | `RESPONSE_TEMPLATE.substitute(label=sentiment)` |
| Tokenizer padding | `pad_token = eos_token` |

The tokenization function [blacksmith/datasets/torch/sst2/sst2_dataset.py 37-59](https://github.com/tenstorrent/tt-blacksmith/blob/fe25eba0/blacksmith/datasets/torch/sst2/sst2_dataset.py#L37-L59) masks the prompt portion with `-100` labels, training only on the sentiment token.

**Sources:**

*   [blacksmith/datasets/torch/sst2/sst2_dataset.py 20-98](https://github.com/tenstorrent/tt-blacksmith/blob/fe25eba0/blacksmith/datasets/torch/sst2/sst2_dataset.py#L20-L98)
*   [blacksmith/datasets/torch/sst2/sst2_utils.py](https://github.com/tenstorrent/tt-blacksmith/blob/fe25eba0/blacksmith/datasets/torch/sst2/sst2_utils.py)

### Intent Classification: Banking77

The `Banking77Dataset`[blacksmith/datasets/torch/banking77/banking77_dataset.py 18-80](https://github.com/tenstorrent/tt-blacksmith/blob/fe25eba0/blacksmith/datasets/torch/banking77/banking77_dataset.py#L18-L80) classifies banking customer queries into 77 intents:

**Key differences from generative datasets:**

*   Uses `DataCollatorWithPadding` instead of `DataCollatorForSeq2Seq`[blacksmith/datasets/torch/banking77/banking77_dataset.py 61-63](https://github.com/tenstorrent/tt-blacksmith/blob/fe25eba0/blacksmith/datasets/torch/banking77/banking77_dataset.py#L61-L63)
*   Labels are class indices, not token sequences [blacksmith/datasets/torch/banking77/banking77_dataset.py 40](https://github.com/tenstorrent/tt-blacksmith/blob/fe25eba0/blacksmith/datasets/torch/banking77/banking77_dataset.py#L40-L40)
*   Provides `get_num_labels()` and `get_label_map()` methods [blacksmith/datasets/torch/banking77/banking77_dataset.py 75-79](https://github.com/tenstorrent/tt-blacksmith/blob/fe25eba0/blacksmith/datasets/torch/banking77/banking77_dataset.py#L75-L79)
*   Creates dynamic label mapping from unique labels [blacksmith/datasets/torch/banking77/banking77_dataset.py 47-50](https://github.com/tenstorrent/tt-blacksmith/blob/fe25eba0/blacksmith/datasets/torch/banking77/banking77_dataset.py#L47-L50)

**Sources:**

*   [blacksmith/datasets/torch/banking77/banking77_dataset.py 18-80](https://github.com/tenstorrent/tt-blacksmith/blob/fe25eba0/blacksmith/datasets/torch/banking77/banking77_dataset.py#L18-L80)

### Image Classification: MNIST

The `MNISTDataset`[blacksmith/datasets/torch/mnist/mnist_dataset.py 20-63](https://github.com/tenstorrent/tt-blacksmith/blob/fe25eba0/blacksmith/datasets/torch/mnist/mnist_dataset.py#L20-L63) loads handwritten digit classification data:

**Transform pipeline**[blacksmith/datasets/torch/mnist/mnist_dataset.py 30-43](https://github.com/tenstorrent/tt-blacksmith/blob/fe25eba0/blacksmith/datasets/torch/mnist/mnist_dataset.py#L30-L43):

`transform = transforms.Compose([    transforms.ToTensor(),    transforms.Normalize((MEAN,), (STD,)),  # 0.1307, 0.3081    transforms.Lambda(lambda x: x.view(-1)),  # Flatten to 784-dim    transforms.Lambda(lambda x: x.to(dtype)),]) target_transform = transforms.Compose([    transforms.Lambda(lambda y: torch.tensor(y, dtype=torch.long)),    transforms.Lambda(lambda y: F.one_hot(y, num_classes=10).to(dtype)),])`
Downloads automatically from `torchvision.datasets.MNIST` with `download=True`[blacksmith/datasets/torch/mnist/mnist_dataset.py 45-51](https://github.com/tenstorrent/tt-blacksmith/blob/fe25eba0/blacksmith/datasets/torch/mnist/mnist_dataset.py#L45-L51)

**Sources:**

*   [blacksmith/datasets/torch/mnist/mnist_dataset.py 20-63](https://github.com/tenstorrent/tt-blacksmith/blob/fe25eba0/blacksmith/datasets/torch/mnist/mnist_dataset.py#L20-L63)

### 3D Neural Rendering: Blender

The `BlenderDataset`[blacksmith/datasets/torch/nerf/blender.py 44-156](https://github.com/tenstorrent/tt-blacksmith/blob/fe25eba0/blacksmith/datasets/torch/nerf/blender.py#L44-L156) provides synthetic 3D scenes for Neural Radiance Field (NeRF) training:

**Key features:**

*   **Training mode**: Pre-computes all rays and RGB values in memory [blacksmith/datasets/torch/nerf/blender.py 75-98](https://github.com/tenstorrent/tt-blacksmith/blob/fe25eba0/blacksmith/datasets/torch/nerf/blender.py#L75-L98)
*   **Test mode**: Generates rays per image on-demand [blacksmith/datasets/torch/nerf/blender.py 99-137](https://github.com/tenstorrent/tt-blacksmith/blob/fe25eba0/blacksmith/datasets/torch/nerf/blender.py#L99-L137)
*   **Ray normalization**: Normalizes ray directions [blacksmith/datasets/torch/nerf/blender.py 73](https://github.com/tenstorrent/tt-blacksmith/blob/fe25eba0/blacksmith/datasets/torch/nerf/blender.py#L73-L73)
*   **RGBA blending**: Blends alpha channel to RGB [blacksmith/datasets/torch/nerf/blender.py 92](https://github.com/tenstorrent/tt-blacksmith/blob/fe25eba0/blacksmith/datasets/torch/nerf/blender.py#L92-L92)
*   **StatefulDataLoader**: Uses `torchdata.stateful_dataloader` for checkpointing [blacksmith/datasets/torch/nerf/blender.py 149-155](https://github.com/tenstorrent/tt-blacksmith/blob/fe25eba0/blacksmith/datasets/torch/nerf/blender.py#L149-L155)

**Sources:**

*   [blacksmith/datasets/torch/nerf/blender.py 44-156](https://github.com/tenstorrent/tt-blacksmith/blob/fe25eba0/blacksmith/datasets/torch/nerf/blender.py#L44-L156)
*   [blacksmith/datasets/torch/nerf/ray_utils.py](https://github.com/tenstorrent/tt-blacksmith/blob/fe25eba0/blacksmith/datasets/torch/nerf/ray_utils.py)

## Model Loading System

The model loading system provides a factory pattern for instantiating and adapting models from Hugging Face.

**Sources:**

*   [blacksmith/models/torch/huggingface/hf_models.py 12-30](https://github.com/tenstorrent/tt-blacksmith/blob/fe25eba0/blacksmith/models/torch/huggingface/hf_models.py#L12-L30)

### get_model Factory Function

The `get_model()` function [blacksmith/models/torch/huggingface/hf_models.py 12-30](https://github.com/tenstorrent/tt-blacksmith/blob/fe25eba0/blacksmith/models/torch/huggingface/hf_models.py#L12-L30) orchestrates model loading:

`def get_model(config: TrainingConfig, device: torch.device):    # Load pretrained model    model = AutoModelForCausalLM.from_pretrained(        config.model_name,         use_cache=config.gradient_checkpointing    )        # Apply training-specific modifications    if config.training_type == "lora":        model = _apply_lora(model, config)    elif config.training_type == "adapters":        _apply_adapters(model, config)        model.to(eval(config.dtype))    model.to(device)        return model`
**Configuration fields used:**

*   `model_name`: Hugging Face model identifier
*   `training_type`: `"lora"` or `"adapters"`
*   `gradient_checkpointing`: Disables cache when True
*   `dtype`: Model precision (e.g., `"torch.bfloat16"`)

**Sources:**

*   [blacksmith/models/torch/huggingface/hf_models.py 12-30](https://github.com/tenstorrent/tt-blacksmith/blob/fe25eba0/blacksmith/models/torch/huggingface/hf_models.py#L12-L30)

### LoRA via PEFT

The `_apply_lora()` function [blacksmith/models/torch/huggingface/hf_models.py 33-41](https://github.com/tenstorrent/tt-blacksmith/blob/fe25eba0/blacksmith/models/torch/huggingface/hf_models.py#L33-L41) applies Low-Rank Adaptation using the `peft` library:

| Config Field | Purpose | Example |
| --- | --- | --- |
| `lora_r` | Rank of adaptation matrices | 8, 16, 32 |
| `lora_alpha` | Scaling factor | 16, 32 |
| `lora_target_modules` | Layers to adapt | `["q_proj", "v_proj"]` |
| `lora_task_type` | Task-specific setup | `"CAUSAL_LM"` |

The function creates a `LoraConfig` and wraps the model with `get_peft_model()`, which inserts trainable low-rank matrices into specified layers while freezing the base model.

**Sources:**

*   [blacksmith/models/torch/huggingface/hf_models.py 33-41](https://github.com/tenstorrent/tt-blacksmith/blob/fe25eba0/blacksmith/models/torch/huggingface/hf_models.py#L33-L41)

### Custom ResidualAdapter

The `_apply_adapters()` function [blacksmith/models/torch/huggingface/hf_models.py 44-66](https://github.com/tenstorrent/tt-blacksmith/blob/fe25eba0/blacksmith/models/torch/huggingface/hf_models.py#L44-L66) inserts custom residual adapters:

**Architecture**[blacksmith/models/torch/huggingface/hf_models.py 70-90](https://github.com/tenstorrent/tt-blacksmith/blob/fe25eba0/blacksmith/models/torch/huggingface/hf_models.py#L70-L90):

`class ResidualAdapter(nn.Module):    def __init__(self, linear, bottleneck_dim):        super().__init__()        self.linear = linear  # Original frozen layer        d = linear.out_features                self.adapter = nn.Sequential(            nn.Linear(d, bottleneck_dim),            nn.GELU(),            nn.Linear(bottleneck_dim, d),        )                # Initialize as identity        nn.init.zeros_(self.adapter[-1].weight)        nn.init.zeros_(self.adapter[-1].bias)        def forward(self, x):        y = self.linear(x)        return y + self.adapter(y)  # Residual connection`
**Insertion points**[blacksmith/models/torch/huggingface/hf_models.py 56-64](https://github.com/tenstorrent/tt-blacksmith/blob/fe25eba0/blacksmith/models/torch/huggingface/hf_models.py#L56-L64):

*   After `self_attn.o_proj` (attention output projection)
*   After `mlp.down_proj` (MLP output projection)

The adapters start as identity transformations (zero-initialized), allowing gradual adaptation without disrupting pretrained representations.

**Sources:**

*   [blacksmith/models/torch/huggingface/hf_models.py 44-90](https://github.com/tenstorrent/tt-blacksmith/blob/fe25eba0/blacksmith/models/torch/huggingface/hf_models.py#L44-L90)

## Data Processing Pipeline

### Tokenization Pattern

All text datasets follow a consistent tokenization pattern:

**Common pattern across datasets:**

1.   **Tokenizer setup** (e.g., [blacksmith/datasets/torch/squadV2/squadV2_dataset.py 33-34](https://github.com/tenstorrent/tt-blacksmith/blob/fe25eba0/blacksmith/datasets/torch/squadV2/squadV2_dataset.py#L33-L34)):

`self.tokenizer = AutoTokenizer.from_pretrained(    config.model_name, padding_side="right", use_fast=True)self.tokenizer.pad_token = self.tokenizer.eos_token`
1.   **Full text tokenization** (e.g., [blacksmith/datasets/torch/squadV2/squadV2_dataset.py 55-58](https://github.com/tenstorrent/tt-blacksmith/blob/fe25eba0/blacksmith/datasets/torch/squadV2/squadV2_dataset.py#L55-L58)):

`encoding = self.tokenizer(full_text, truncation=False, padding=False,                           return_tensors="pt")input_ids = encoding["input_ids"].squeeze(0)attention_mask = encoding["attention_mask"].squeeze(0)`
1.   **Prompt masking** (e.g., [blacksmith/datasets/torch/squadV2/squadV2_dataset.py 60-64](https://github.com/tenstorrent/tt-blacksmith/blob/fe25eba0/blacksmith/datasets/torch/squadV2/squadV2_dataset.py#L60-L64)):

`labels = input_ids.clone()prompt_encoding = self.tokenizer(prompt, truncation=False, padding=False,                                  return_tensors="pt")prompt_len = prompt_encoding["input_ids"].squeeze(0).size(0)labels[:prompt_len] = -100  # Mask prompt tokens`
**Sources:**

*   [blacksmith/datasets/torch/squadV2/squadV2_dataset.py 33-72](https://github.com/tenstorrent/tt-blacksmith/blob/fe25eba0/blacksmith/datasets/torch/squadV2/squadV2_dataset.py#L33-L72)
*   [blacksmith/datasets/torch/text2sql/text2sql_dataset.py 33-67](https://github.com/tenstorrent/tt-blacksmith/blob/fe25eba0/blacksmith/datasets/torch/text2sql/text2sql_dataset.py#L33-L67)
*   [blacksmith/datasets/torch/sst2/sst2_dataset.py 29-59](https://github.com/tenstorrent/tt-blacksmith/blob/fe25eba0/blacksmith/datasets/torch/sst2/sst2_dataset.py#L29-L59)

### Collation Functions

Datasets use Hugging Face `DataCollator` classes for efficient batching:

| Dataset Type | Collator | Purpose |
| --- | --- | --- |
| Generative (SQuAD, Text2SQL, SST) | `DataCollatorForSeq2Seq` | Pads sequences to max_length, creates batch tensors |
| Classification (Banking77) | `DataCollatorWithPadding` | Dynamic padding to longest in batch |
| Vision (MNIST) | None (fixed size) | Images already uniform 28×28 |
| NeRF (Blender) | None (custom batching) | Rays and RGB pre-processed |

**Example: DataCollatorForSeq2Seq**[blacksmith/datasets/torch/squadV2/squadV2_dataset.py 100-102](https://github.com/tenstorrent/tt-blacksmith/blob/fe25eba0/blacksmith/datasets/torch/squadV2/squadV2_dataset.py#L100-L102):

`data_collator = DataCollatorForSeq2Seq(    tokenizer=self.tokenizer,     padding="max_length",     max_length=self.config.max_length)`
**Custom collate wrapper**[blacksmith/datasets/torch/squadV2/squadV2_dataset.py 104-107](https://github.com/tenstorrent/tt-blacksmith/blob/fe25eba0/blacksmith/datasets/torch/squadV2/squadV2_dataset.py#L104-L107):

`if self.collate_fn is not None:    total_collate_fn = lambda batch: self.collate_fn(data_collator(batch))else:    total_collate_fn = data_collator`
This allows experiments to inject custom batch transformations after standard collation.

**Sources:**

*   [blacksmith/datasets/torch/squadV2/squadV2_dataset.py 100-115](https://github.com/tenstorrent/tt-blacksmith/blob/fe25eba0/blacksmith/datasets/torch/squadV2/squadV2_dataset.py#L100-L115)
*   [blacksmith/datasets/torch/banking77/banking77_dataset.py 61-73](https://github.com/tenstorrent/tt-blacksmith/blob/fe25eba0/blacksmith/datasets/torch/banking77/banking77_dataset.py#L61-L73)

## Dataset-Model Integration

The integration between datasets and models flows through the training template:

**Key integration points:**

1.   **Unified configuration**: Both datasets and models read from the same `TrainingConfig`[blacksmith/tools/templates/test_model_template.py 140](https://github.com/tenstorrent/tt-blacksmith/blob/fe25eba0/blacksmith/tools/templates/test_model_template.py#L140-L140)

2.   **Factory pattern**: Decouples experiment scripts from specific implementations [blacksmith/tools/templates/test_model_template.py 56-70](https://github.com/tenstorrent/tt-blacksmith/blob/fe25eba0/blacksmith/tools/templates/test_model_template.py#L56-L70)

3.   **Batch format contract**: All datasets produce `{"input_ids", "attention_mask", "labels"}` dicts compatible with Hugging Face model signatures [blacksmith/datasets/torch/squadV2/squadV2_dataset.py 93-97](https://github.com/tenstorrent/tt-blacksmith/blob/fe25eba0/blacksmith/datasets/torch/squadV2/squadV2_dataset.py#L93-L97)

4.   **Device management**: `DeviceManager.prepare_batch()` moves tensors to correct device and handles sharding [blacksmith/experiments/torch/albert/test_albert_finetuning.py 99](https://github.com/tenstorrent/tt-blacksmith/blob/fe25eba0/blacksmith/experiments/torch/albert/test_albert_finetuning.py#L99-L99)

**Sources:**

*   [blacksmith/tools/templates/test_model_template.py 52-134](https://github.com/tenstorrent/tt-blacksmith/blob/fe25eba0/blacksmith/tools/templates/test_model_template.py#L52-L134)
*   [blacksmith/experiments/torch/albert/test_albert_finetuning.py 61-143](https://github.com/tenstorrent/tt-blacksmith/blob/fe25eba0/blacksmith/experiments/torch/albert/test_albert_finetuning.py#L61-L143)
*   [blacksmith/tools/cli.py 12-38](https://github.com/tenstorrent/tt-blacksmith/blob/fe25eba0/blacksmith/tools/cli.py#L12-L38)

Dismiss
Refresh this wiki

Enter email to refresh
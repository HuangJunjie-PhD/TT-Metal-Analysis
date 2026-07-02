---
project: tt-blacksmith
github: https://github.com/tenstorrent/tt-blacksmith
deepwiki: https://deepwiki.com/tenstorrent/tt-blacksmith/9-testing-and-cicd
---

# CI/CD and Test Automation

Relevant source files
*   [.github/workflows/call-generate-matrix.yml](https://github.com/tenstorrent/tt-blacksmith/blob/fe25eba0/.github/workflows/call-generate-matrix.yml)
*   [.github/workflows/call-test.yml](https://github.com/tenstorrent/tt-blacksmith/blob/fe25eba0/.github/workflows/call-test.yml)
*   [.github/workflows/manual-test.yml](https://github.com/tenstorrent/tt-blacksmith/blob/fe25eba0/.github/workflows/manual-test.yml)
*   [.github/workflows/pr-main.yml](https://github.com/tenstorrent/tt-blacksmith/blob/fe25eba0/.github/workflows/pr-main.yml)
*   [.github/workflows/push-main.yml](https://github.com/tenstorrent/tt-blacksmith/blob/fe25eba0/.github/workflows/push-main.yml)
*   [.github/workflows/schedule-uplift.yml](https://github.com/tenstorrent/tt-blacksmith/blob/fe25eba0/.github/workflows/schedule-uplift.yml)
*   [.github/workflows/test-matrix-presets/basic-test.json](https://github.com/tenstorrent/tt-blacksmith/blob/fe25eba0/.github/workflows/test-matrix-presets/basic-test.json)
*   [.github/workflows/test-matrix-presets/regressed-test.json](https://github.com/tenstorrent/tt-blacksmith/blob/fe25eba0/.github/workflows/test-matrix-presets/regressed-test.json)
*   [.github/workflows/test-matrix-presets/uplift-test.json](https://github.com/tenstorrent/tt-blacksmith/blob/fe25eba0/.github/workflows/test-matrix-presets/uplift-test.json)
*   [tests/configs/test_training_fast.yaml](https://github.com/tenstorrent/tt-blacksmith/blob/fe25eba0/tests/configs/test_training_fast.yaml)
*   [tests/golden_files/tt-llama_3_2_1b-sst2-n300-galaxy_train.csv](https://github.com/tenstorrent/tt-blacksmith/blob/fe25eba0/tests/golden_files/tt-llama_3_2_1b-sst2-n300-galaxy_train.csv)
*   [tests/golden_files/tt-llama_3_2_1b-sst2-n300-galaxy_val.csv](https://github.com/tenstorrent/tt-blacksmith/blob/fe25eba0/tests/golden_files/tt-llama_3_2_1b-sst2-n300-galaxy_val.csv)
*   [tests/pytest.ini](https://github.com/tenstorrent/tt-blacksmith/blob/fe25eba0/tests/pytest.ini)
*   [tests/test_all.py](https://github.com/tenstorrent/tt-blacksmith/blob/fe25eba0/tests/test_all.py)
*   [tests/training_test_cases.py](https://github.com/tenstorrent/tt-blacksmith/blob/fe25eba0/tests/training_test_cases.py)

This page covers the GitHub Actions pipeline used to validate TT-Blacksmith, the test matrix preset system, how test jobs are parameterized and dispatched, and the pytest-based test runner including its golden-log comparison mechanism. For the full list of test cases and their hardware/marker assignments, see [Test Matrix and CI Configuration](https://deepwiki.com/tenstorrent/tt-blacksmith/7.2-dataset-implementations). For `TrainingConfig` fields that affect test behavior (e.g., `max_steps_per_epoch`, `log_on_wandb`), see [Configuration Reference](https://deepwiki.com/tenstorrent/tt-blacksmith/9-testing-and-cicd#7.3).

* * *

## Pipeline Overview

The CI system is built around four GitHub Actions workflow files that share two reusable called workflows. All test execution is driven through `call-test.yml`, which itself delegates hardware matrix generation to `call-generate-matrix.yml`.

**Workflow Dependency Diagram**

Sources: [.github/workflows/pr-main.yml 1-47](https://github.com/tenstorrent/tt-blacksmith/blob/fe25eba0/.github/workflows/pr-main.yml#L1-L47)[.github/workflows/push-main.yml 1-24](https://github.com/tenstorrent/tt-blacksmith/blob/fe25eba0/.github/workflows/push-main.yml#L1-L24)[.github/workflows/manual-test.yml 1-32](https://github.com/tenstorrent/tt-blacksmith/blob/fe25eba0/.github/workflows/manual-test.yml#L1-L32)[.github/workflows/schedule-uplift.yml 1-84](https://github.com/tenstorrent/tt-blacksmith/blob/fe25eba0/.github/workflows/schedule-uplift.yml#L1-L84)[.github/workflows/call-test.yml 1-78](https://github.com/tenstorrent/tt-blacksmith/blob/fe25eba0/.github/workflows/call-test.yml#L1-L78)[.github/workflows/call-generate-matrix.yml 1-37](https://github.com/tenstorrent/tt-blacksmith/blob/fe25eba0/.github/workflows/call-generate-matrix.yml#L1-L37)

* * *

## Trigger Events

| Workflow file | Trigger(s) | Test suite |
| --- | --- | --- |
| `pr-main.yml` | `pull_request` → `main`, `workflow_dispatch` | `basic-test.json` (always); `uplift-test.json` (only if branch = `uplift`) |
| `push-main.yml` | `push` → `main` | `basic-test.json` |
| `manual-test.yml` | `workflow_dispatch` | User-chosen: `basic-test.json`, `uplift-test.json`, or `regressed-test.json` |
| `schedule-uplift.yml` | `schedule` (cron `0 6 * * *`), `workflow_dispatch` | Creates/merges an uplift PR; does not run tests directly |

`pr-main.yml` enforces a `check-all-green` gate job using `re-actors/alls-green`. The `uplift-test` job is listed under `allowed-skips` so that non-uplift PRs can still pass.

Sources: [.github/workflows/pr-main.yml 1-47](https://github.com/tenstorrent/tt-blacksmith/blob/fe25eba0/.github/workflows/pr-main.yml#L1-L47)[.github/workflows/push-main.yml 1-24](https://github.com/tenstorrent/tt-blacksmith/blob/fe25eba0/.github/workflows/push-main.yml#L1-L24)[.github/workflows/manual-test.yml 1-32](https://github.com/tenstorrent/tt-blacksmith/blob/fe25eba0/.github/workflows/manual-test.yml#L1-L32)

* * *

## Nightly Uplift Workflow

`schedule-uplift.yml` runs at 06:00 UTC every day and automates keeping `pjrt-plugin-tt` current.

Steps executed:

1.   Check out `main` with `fetch-depth: 0`.
2.   Query the Tenstorrent PyPI index for the latest `pjrt-plugin-tt` nightly build matching the pattern `^[0-9]+\.[0-9]+\.[0-9]+\.dev[0-9]{14}$`.
3.   `sed -i` replaces the pinned version in `env/xla_requirements.txt`.
4.   `peter-evans/create-pull-request` opens a branch named `uplift`, commits the change, and sets the `uplift` label.
5.   The PR is immediately approved via `gh pr review --approve` and set to auto-merge with `--squash`.

When the resulting PR targets `main`, `pr-main.yml` sees `github.head_ref == 'uplift'` and runs the `uplift-test` job in addition to `basic-test`.

Sources: [.github/workflows/schedule-uplift.yml 1-84](https://github.com/tenstorrent/tt-blacksmith/blob/fe25eba0/.github/workflows/schedule-uplift.yml#L1-L84)

* * *

## Test Matrix Presets

Preset files live in `.github/workflows/test-matrix-presets/`. Each is a JSON array where every element is a test job descriptor.

**JSON object fields:**

| Field | Type | Meaning |
| --- | --- | --- |
| `runs-on` | string | Hardware runner label (e.g., `n150`, `n300`, `n300-llmbox`) |
| `frontend` | string | Passed to `env/activate --<frontend>` (values: `xla`) |
| `marks` | string | `-m` expression forwarded to `pytest` |

### `basic-test.json`

[.github/workflows/test-matrix-presets/basic-test.json 1-6](https://github.com/tenstorrent/tt-blacksmith/blob/fe25eba0/.github/workflows/test-matrix-presets/basic-test.json#L1-L6)

```
n150  / xla / single_chip and push and n150 and torch
n300  / xla / single_chip and push and n300 and torch
n300  / xla / tensor_parallel and push and n300 and torch
n300  / xla / data_parallel and push and n300 and torch
```

These use the `push` marker and run on every PR and every push to `main`.

### `uplift-test.json`

[.github/workflows/test-matrix-presets/uplift-test.json 1-8](https://github.com/tenstorrent/tt-blacksmith/blob/fe25eba0/.github/workflows/test-matrix-presets/uplift-test.json#L1-L8)

```
n150             / xla / single_chip and uplift and n150 and jax
n300             / xla / data_parallel and uplift and n300 and jax
n300             / xla / tensor_parallel and uplift and n300 and jax
n150-high-memory / xla / single_chip and uplift and n150 and torch and split_0
n150-high-memory / xla / single_chip and uplift and n150 and torch and split_1
n300-llmbox      / xla / data_parallel and uplift and n300_llmbox and torch
```

`split_0` and `split_1` partition large single-chip torch tests across two parallel runners to reduce wall-clock time.

### `regressed-test.json`

[.github/workflows/test-matrix-presets/regressed-test.json 1-2](https://github.com/tenstorrent/tt-blacksmith/blob/fe25eba0/.github/workflows/test-matrix-presets/regressed-test.json#L1-L2)

Currently empty. Used as a parking area for temporarily regressed tests.

* * *

## `call-generate-matrix.yml` and `call-test.yml`

These two reusable workflows implement the core test dispatch logic.

**Matrix Generation and Test Execution Flow**

### `call-generate-matrix.yml` details

[.github/workflows/call-generate-matrix.yml 1-37](https://github.com/tenstorrent/tt-blacksmith/blob/fe25eba0/.github/workflows/call-generate-matrix.yml#L1-L37)

Reads the preset JSON file from `.github/workflows/test-matrix-presets/<test_suite>` using `cat` and pipes through `jq -c` to produce a compact JSON string that GitHub Actions can consume as a matrix.

### `call-test.yml` details

[.github/workflows/call-test.yml 1-78](https://github.com/tenstorrent/tt-blacksmith/blob/fe25eba0/.github/workflows/call-test.yml#L1-L78)

Key behaviors:

*   Sets `WANDB_MODE: disabled` at the environment level to suppress W&B uploads during CI.
*   Each matrix entry runs in a Docker container using `ghcr.io/tenstorrent/tt-xla-slim:nightly-latest`. The container has `/dev/tenstorrent` mounted via `--device`.
*   Huge-page volumes (`/dev/hugepages`, `/dev/hugepages-1G`) and `/mnt/dockercache` are mounted into each container.
*   Environment setup: `source env/activate --<frontend>` installs the correct virtual environment. (See [Environment Management](https://deepwiki.com/tenstorrent/tt-blacksmith/9-testing-and-cicd#3.4) for details on `env/activate`.)
*   Test execution: 
```
pytest -m '<marks>' tests/
```
 with `WANDB_MODE=offline`, `HF_HOME=/mnt/dockercache/huggingface`, and `HF_TOKEN` from secrets.
*   `timeout-minutes: 240` applies globally; per-test timeouts are enforced inside Python.

* * *

## Pytest Marker Taxonomy

Markers are declared in `tests/pytest.ini`:

[tests/pytest.ini 1-14](https://github.com/tenstorrent/tt-blacksmith/blob/fe25eba0/tests/pytest.ini#L1-L14)

| Marker | Meaning |
| --- | --- |
| `push` | Included in every PR and push-to-main run (`basic-test.json`) |
| `uplift` | Included only in uplift PR runs (`uplift-test.json`) |
| `n150` | Runs on a single n150 (Blackhole) card |
| `n300` | Runs on an n300 (Wormhole) card |
| `n300_llmbox` | Runs on a QuietBox (n300 LLMbox) multi-chip configuration |
| `torch` | Test uses the `torch`/`torch_xla` frontend |
| `jax` | Test uses the JAX/XLA frontend |
| `single_chip` | Single-chip test (no parallelism) |
| `data_parallel` | Data-parallel multi-chip test |
| `tensor_parallel` | Tensor-parallel multi-chip test |
| `split_0` | First partition of a large test group (run in parallel with `split_1`) |
| `split_1` | Second partition of a large test group |

Each `pytest.param` in `TRAINING_TEST_CASES` combines multiple markers. The matrix entry's `marks` string is an `and`-expression that selects exactly the matching subset.

**Marker-to-hardware mapping**

Sources: [tests/pytest.ini 1-14](https://github.com/tenstorrent/tt-blacksmith/blob/fe25eba0/tests/pytest.ini#L1-L14)[.github/workflows/test-matrix-presets/basic-test.json 1-6](https://github.com/tenstorrent/tt-blacksmith/blob/fe25eba0/.github/workflows/test-matrix-presets/basic-test.json#L1-L6)[.github/workflows/test-matrix-presets/uplift-test.json 1-8](https://github.com/tenstorrent/tt-blacksmith/blob/fe25eba0/.github/workflows/test-matrix-presets/uplift-test.json#L1-L8)

* * *

## `TRAINING_TEST_CASES` and `test_training_script`

All parameterized test cases are defined in `tests/training_test_cases.py` as the `TRAINING_TEST_CASES` list of `pytest.param` objects.

[tests/training_test_cases.py 1-248](https://github.com/tenstorrent/tt-blacksmith/blob/fe25eba0/tests/training_test_cases.py#L1-L248)

Each `pytest.param` contains a dictionary with these fields:

| Field | Required | Default | Description |
| --- | --- | --- | --- |
| `test_script` | yes | — | Path to the experiment Python script |
| `experiment_config` | yes | — | Path to the primary YAML config |
| `test_config` | no | `tests/configs/test_training_fast.yaml` | Override config applied on top of `experiment_config` |
| `tolerance` | no | `0.3` | Relative tolerance for golden-log comparison |
| `timeout` | no | `800.0` | Subprocess timeout in seconds |
| `skip_loss_checks` | no | `False` | Skip golden-log comparison (used for JAX tests without golden files yet) |

**Test case examples from `TRAINING_TEST_CASES`:**

| `id` | Script | Markers |
| --- | --- | --- |
| `tt-mlp-mnist-n150` | `torch/mnist/test_mnist_training.py` | `push, n150, n300, torch, single_chip` |
| `tt-mlp-mnist-n300-tp` | `torch/mnist/tensor_parallel/test_mnist_training.py` | `push, n300, torch, tensor_parallel` |
| `tt-llama_3_2_1b-sst2-n150` | `torch/llama/xla/test_llama_fine_tuning_pure_torch.py` | `uplift, n150, torch, single_chip, split_0` |
| `tt-qwen_1_5b-text2sql-n150` | `torch/qwen/test_qwen_finetuning.py` | `uplift, n150, torch, single_chip, split_0` |
| `tt-gemma11-squadv2-n150` | `torch/gemma11/test_gemma11_finetuning.py` | `uplift, n150, torch, single_chip, split_1` |
| `tt-albert_base_v2-banking77-n150` | `torch/albert/test_albert_finetuning.py` | `uplift, n150, torch, single_chip, split_1` |
| `tt-mlp-mnist-n150-jax` | `jax/mnist/single_chip/test_pure_jax_mnist.py` | `uplift, n150, jax, single_chip` |
| `tt-mlp-mnist-n300-dp-jax` | `jax/mnist/multi_chip/data_parallel/test_pure_jax_mnist.py` | `uplift, n300, jax, data_parallel` |

The single test function `test_training_script` in `tests/test_all.py` handles all cases:

[tests/test_all.py 34-126](https://github.com/tenstorrent/tt-blacksmith/blob/fe25eba0/tests/test_all.py#L34-L126)

### `test_training_script` Execution Flow

**`test_training_script` subprocess dispatch**

Sources: [tests/test_all.py 34-126](https://github.com/tenstorrent/tt-blacksmith/blob/fe25eba0/tests/test_all.py#L34-L126)[tests/training_test_cases.py 1-248](https://github.com/tenstorrent/tt-blacksmith/blob/fe25eba0/tests/training_test_cases.py#L1-L248)

* * *

## Test Configuration Override

The file `tests/configs/test_training_fast.yaml` is the default `test_config` applied during CI runs:

[tests/configs/test_training_fast.yaml 1-7](https://github.com/tenstorrent/tt-blacksmith/blob/fe25eba0/tests/configs/test_training_fast.yaml#L1-L7)

This config is merged on top of the experiment's primary YAML by `generate_config` in `blacksmith/tools/cli.py`:

[blacksmith/tools/cli.py 12-23](https://github.com/tenstorrent/tt-blacksmith/blob/fe25eba0/blacksmith/tools/cli.py#L12-L23)

The merge uses `config_data |= yaml.safe_load(test_yaml_path)`, so any field in the test config overwrites the corresponding field in the experiment config. Key effects:

*   `max_steps_per_epoch: 15` limits training to 15 steps per epoch, drastically reducing wall time.
*   `steps_freq: 5` and `val_steps_freq: 5` set logging frequency so that metrics are captured within those 15 steps.
*   `log_on_wandb: false` prevents W&B uploads even if the experiment YAML sets it to `true`.
*   `use_tt: true` ensures the TT hardware backend is used (not a CPU fallback).

Individual test cases can override `test_config` by specifying a different path in their `setup_dict`. For example, `tt-albert_base_v2-banking77-n150` uses `tests/configs/tt-albert_base_v2-banking77-n150.yaml` instead of the default.

Sources: [tests/configs/test_training_fast.yaml 1-7](https://github.com/tenstorrent/tt-blacksmith/blob/fe25eba0/tests/configs/test_training_fast.yaml#L1-L7)[blacksmith/tools/cli.py 12-23](https://github.com/tenstorrent/tt-blacksmith/blob/fe25eba0/blacksmith/tools/cli.py#L12-L23)[tests/training_test_cases.py 171-185](https://github.com/tenstorrent/tt-blacksmith/blob/fe25eba0/tests/training_test_cases.py#L171-L185)

* * *

## Golden-Log Comparison

When `skip_loss_checks` is `False` (default for PyTorch tests), `test_training_script` performs a numerical comparison between the test run's CSV logs and pre-committed golden reference files.

**File layout:**

| Directory constant | Role |
| --- | --- |
| `TEST_LOGS_DIR` (from `blacksmith.tools.logging_manager`) | Output location for the current test run's CSV files |
| `GOLDEN_LOGS_DIR` (from `blacksmith.tools.logging_manager`) | Pre-committed reference CSV files |

**Generated filenames** follow the pattern `<test_id>_train.csv` and `<test_id>_val.csv`, where `test_id` is the `pytest.param``id` string (e.g., `tt-mlp-mnist-n150`). The `--test-log-filename-prefix` CLI argument passes `test_id` into the training script.

**`assert_loss_with_tolerance`:**

[tests/test_all.py 20-23](https://github.com/tenstorrent/tt-blacksmith/blob/fe25eba0/tests/test_all.py#L20-L23)

Uses `pandas.testing.assert_frame_equal` with relative tolerance (`rtol`). Default `tolerance` is `0.3` (30% relative).

**Generating golden files:** Pass `--generate-golden-files` to pytest. The test moves the newly generated CSVs from `TEST_LOGS_DIR` to `GOLDEN_LOGS_DIR` instead of comparing.

JAX tests currently set `skip_loss_checks: True` because golden files are not yet committed for those experiments.

Sources: [tests/test_all.py 20-127](https://github.com/tenstorrent/tt-blacksmith/blob/fe25eba0/tests/test_all.py#L20-L127)

* * *

## Component-to-File Reference

**CI/CD entity to file mapping**

Sources: [.github/workflows/call-test.yml 1-78](https://github.com/tenstorrent/tt-blacksmith/blob/fe25eba0/.github/workflows/call-test.yml#L1-L78)[.github/workflows/call-generate-matrix.yml 1-37](https://github.com/tenstorrent/tt-blacksmith/blob/fe25eba0/.github/workflows/call-generate-matrix.yml#L1-L37)[tests/test_all.py 1-127](https://github.com/tenstorrent/tt-blacksmith/blob/fe25eba0/tests/test_all.py#L1-L127)[tests/training_test_cases.py 1-248](https://github.com/tenstorrent/tt-blacksmith/blob/fe25eba0/tests/training_test_cases.py#L1-L248)[blacksmith/tools/cli.py 1-43](https://github.com/tenstorrent/tt-blacksmith/blob/fe25eba0/blacksmith/tools/cli.py#L1-L43)

Dismiss
Refresh this wiki

Enter email to refresh
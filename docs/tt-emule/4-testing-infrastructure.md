---
project: tt-emule
github: https://github.com/tenstorrent/tt-emule
deepwiki: https://deepwiki.com/tenstorrent/tt-emule/4-testing-infrastructure
---

# Testing Infrastructure

Relevant source files
*   [.github/CODEOWNERS](https://github.com/tenstorrent/tt-emule/blob/a57cf072/.github/CODEOWNERS)
*   [.github/notify-stub.md](https://github.com/tenstorrent/tt-emule/blob/a57cf072/.github/notify-stub.md?plain=1)
*   [.github/scripts/.gitignore](https://github.com/tenstorrent/tt-emule/blob/a57cf072/.github/scripts/.gitignore)
*   [.github/scripts/ci-regression.sh](https://github.com/tenstorrent/tt-emule/blob/a57cf072/.github/scripts/ci-regression.sh)
*   [.github/scripts/classify-results.py](https://github.com/tenstorrent/tt-emule/blob/a57cf072/.github/scripts/classify-results.py)
*   [.github/workflows/nightly-d2m-upstream.yml](https://github.com/tenstorrent/tt-emule/blob/a57cf072/.github/workflows/nightly-d2m-upstream.yml)
*   [.github/workflows/pr-d2m-regression.yml](https://github.com/tenstorrent/tt-emule/blob/a57cf072/.github/workflows/pr-d2m-regression.yml)
*   [include/jit_hw/llk_defs.h](https://github.com/tenstorrent/tt-emule/blob/a57cf072/include/jit_hw/llk_defs.h)
*   [run_d2m_regression.sh](https://github.com/tenstorrent/tt-emule/blob/a57cf072/run_d2m_regression.sh)
*   [run_regression.sh](https://github.com/tenstorrent/tt-emule/blob/a57cf072/run_regression.sh)
*   [scripts/run_ttnn_pytests_blackhole.sh](https://github.com/tenstorrent/tt-emule/blob/a57cf072/scripts/run_ttnn_pytests_blackhole.sh)
*   [scripts/run_ttnn_pytests_wormhole.sh](https://github.com/tenstorrent/tt-emule/blob/a57cf072/scripts/run_ttnn_pytests_wormhole.sh)

The `tt-emule` testing infrastructure employs a multi-tier strategy designed to ensure functional parity with Tenstorrent hardware across different levels of abstraction. This ranges from low-level C++ unit tests to high-level Python operator tests and full compiler-stack integration via the Direct-to-Metal (D2M) suite.

### Testing Strategy Overview

The infrastructure is built on three primary pillars:

1.   **C++ GTests (Tiers 1–6):** Regression suites from `tt-metal` that verify low-level kernel APIs, memory management, and basic ops.
2.   **Python `ttnn` Pytests:** High-level operator verification using the `ttnn` framework, sharded for performance.
3.   **D2M Regression:** Integration testing with `tt-mlir`, ensuring that the compiler-generated kernels execute correctly on the emulated backend.

### System Architecture: Test Flow

The following diagram illustrates how test runners interact with the emulation core and the allowlist system to determine CI success.

**Test Execution and Classification Flow**

**Sources:**[run_regression.sh 8-15](https://github.com/tenstorrent/tt-emule/blob/a57cf072/run_regression.sh#L8-L15)[.github/scripts/classify-results.py 5-13](https://github.com/tenstorrent/tt-emule/blob/a57cf072/.github/scripts/classify-results.py#L5-L13)[run_d2m_regression.sh 128-158](https://github.com/tenstorrent/tt-emule/blob/a57cf072/run_d2m_regression.sh#L128-L158)

* * *

### C++ Regression Suite (Tier 1–6)

The C++ regression suite is the primary guard for low-level hardware abstraction layers. It executes `tt-metal` unit tests categorized into tiers, ranging from host-only buffer tests to complex matmul operations.

*   **Tiers 1-3:** Focus on basic L1/DRAM buffers and simple JIT kernels on Wormhole.
*   **Tiers 3b-3j:** Focus on Quasar-specific features like Dataflow Buffers (DFB) and semaphores.
*   **Tier 4-6:** Focus on Blackhole INT32 support and `ttnn` BF16 matmuls.

The results are processed by `classify-results.py`, which compares the observed failures against an architecture-specific allowlist.

For details, see [C++ Regression Suite (Tier 1–6)](https://deepwiki.com/tenstorrent/tt-emule/4.1-c++-regression-suite-(tier-1-6)).

**Sources:**[run_regression.sh 20-33](https://github.com/tenstorrent/tt-emule/blob/a57cf072/run_regression.sh#L20-L33)[.github/scripts/classify-results.py 30-69](https://github.com/tenstorrent/tt-emule/blob/a57cf072/.github/scripts/classify-results.py#L30-L69)

* * *

### Python ttnn Pytest Suite

The `ttnn` suite verifies the high-level Python API. Because `ttnn` imports are heavy and device initialization is time-consuming, the suite uses a curated list of passing tests and a round-robin sharding strategy to parallelize execution across CI runners.

**Pytest Sharding Logic**

Key features include:

*   **Environment Injection:** Sets `TT_METAL_EMULE_MODE=1` and `TT_METAL_SLOW_DISPATCH_MODE=1` to redirect `tt-metal` calls to the emulator.
*   **Targeted Filtering:** Uses `-k` and `--deselect` to run only the verified-pass subsets of the massive `tt-metal` test suite.

For details, see [Python ttnn Pytest Suite](https://deepwiki.com/tenstorrent/tt-emule/4.2-python-ttnn-pytest-suite).

**Sources:**[scripts/run_ttnn_pytests_wormhole.sh 42-54](https://github.com/tenstorrent/tt-emule/blob/a57cf072/scripts/run_ttnn_pytests_wormhole.sh#L42-L54)[scripts/run_ttnn_pytests_blackhole.sh 81-89](https://github.com/tenstorrent/tt-emule/blob/a57cf072/scripts/run_ttnn_pytests_blackhole.sh#L81-L89)

* * *

### D2M Regression (tt-mlir Integration)

The Direct-to-Metal (D2M) suite ensures that the `tt-mlir` compiler produces valid code for the emulator. This involves a complex "three-way pin" to ensure compatibility between `tt-emule`, `tt-mlir`, and `tt-metal`.

| Component | Dependency Source | Purpose |
| --- | --- | --- |
| `tt-emule` | Current PR / Main | The emulation logic under test. |
| `tt-mlir` | `tt-mlir-pin.txt` | The compiler version to test against. |
| `tt-metal` | `tt-mlir/third_party/CMakeLists.txt` | The hardware abstraction layer pinned by the compiler. |

The `run_d2m_regression.sh` script manages JIT cache isolation and per-file timeouts to prevent local hangs from blocking the entire suite.

For details, see [D2M Regression (tt-mlir Integration)](https://deepwiki.com/tenstorrent/tt-emule/4.3-d2m-regression-(tt-mlir-integration)).

**Sources:**[.github/workflows/pr-d2m-regression.yml 51-69](https://github.com/tenstorrent/tt-emule/blob/a57cf072/.github/workflows/pr-d2m-regression.yml#L51-L69)[run_d2m_regression.sh 95-103](https://github.com/tenstorrent/tt-emule/blob/a57cf072/run_d2m_regression.sh#L95-L103)

* * *

### Known-Failures Allowlist System

To maintain a "Green CI" while work is in progress, `tt-emule` uses an allowlist system.

*   **`classify-results.py`**: A utility that parses JUnit XML and compares it to `known-failures.txt`.
*   **Strict Enforcement**: CI fails not only on **new failures** but also on **newly-passing** tests (to encourage allowlist cleanup) and **stale entries** (patterns that no longer match any existing test).
*   **Flaky Markers**: Entries prefixed with `flaky:` are allowed to flip-flop between PASS and FAIL without breaking the build.

**Sources:**[.github/scripts/classify-results.py 94-114](https://github.com/tenstorrent/tt-emule/blob/a57cf072/.github/scripts/classify-results.py#L94-L114)[.github/scripts/classify-results.py 127-145](https://github.com/tenstorrent/tt-emule/blob/a57cf072/.github/scripts/classify-results.py#L127-L145)

Dismiss
Refresh this wiki

Enter email to refresh
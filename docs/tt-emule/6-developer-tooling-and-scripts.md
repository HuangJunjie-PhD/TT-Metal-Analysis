---
project: tt-emule
github: https://github.com/tenstorrent/tt-emule
deepwiki: https://deepwiki.com/tenstorrent/tt-emule/6-developer-tooling-and-scripts
---

# Developer Tooling & Scripts

Relevant source files
*   [scripts/classify_kernels.py](https://github.com/tenstorrent/tt-emule/blob/a57cf072/scripts/classify_kernels.py)
*   [scripts/emule_surface.py](https://github.com/tenstorrent/tt-emule/blob/a57cf072/scripts/emule_surface.py)
*   [scripts/find_symbol.py](https://github.com/tenstorrent/tt-emule/blob/a57cf072/scripts/find_symbol.py)
*   [scripts/gen_structure.py](https://github.com/tenstorrent/tt-emule/blob/a57cf072/scripts/gen_structure.py)

This section provides an overview of the developer-facing scripts and automated tooling used to maintain the `tt-emule` codebase, analyze the JIT kernel surface, and facilitate the bring-up of new kernels. These tools bridge the gap between the `tt-metal` source tree and the emulated environment by providing deterministic classification and structured indexing.

## Core Scripting & Maintenance

The `tt-emule` repository employs a set of Python-based tools to ensure the emulation surface remains in sync with the provided headers and to assist developers in identifying missing functionality.

### Structure Indexing & Discovery

The repository uses a structured index, `.claude/references/structure.yaml`, which serves as the source of truth for all files and symbols provided by the emulator. This index is maintained by `gen_structure.py`, which scans the source tree to extract namespace-scope declarations while preserving editorial summaries [scripts/gen_structure.py 5-24](https://github.com/tenstorrent/tt-emule/blob/a57cf072/scripts/gen_structure.py#L5-L24)

*   **`gen_structure.py`**: Automatically regenerates the index, ensuring that new `jit_hw` headers or symbols are captured for the classifier and search tools [scripts/gen_structure.py 27-31](https://github.com/tenstorrent/tt-emule/blob/a57cf072/scripts/gen_structure.py#L27-L31)
*   **`find_symbol.py`**: A CLI tool for querying the index. It supports exact name matches, kind-aware filtering (e.g., `class`, `function`), and early-detection probes via the `--supports` and `--shadows` flags [scripts/find_symbol.py 12-21](https://github.com/tenstorrent/tt-emule/blob/a57cf072/scripts/find_symbol.py#L12-L21)

### Surface Analysis

The "surface" refers to the boundary of what `tt-emule` provides to a JIT-compiled kernel. This is programmatically accessed via the `emule_surface.py` module, which answers whether a specific symbol or `#include` is shadowed by the emulator [scripts/emule_surface.py 5-21](https://github.com/tenstorrent/tt-emule/blob/a57cf072/scripts/emule_surface.py#L5-L21)

Sources: [scripts/find_symbol.py 107-122](https://github.com/tenstorrent/tt-emule/blob/a57cf072/scripts/find_symbol.py#L107-L122)[scripts/emule_surface.py 48-75](https://github.com/tenstorrent/tt-emule/blob/a57cf072/scripts/emule_surface.py#L48-L75)

* * *

## Kernel Classification & Surface Analysis

The classification pipeline allows developers to determine if a kernel from `tt-metal` is compatible with the emulator without attempting a full build.

### Classification Verdicts

The `classify_kernels.py` script labels kernels based on their API usage [scripts/classify_kernels.py 20-25](https://github.com/tenstorrent/tt-emule/blob/a57cf072/scripts/classify_kernels.py#L20-L25):

*   **`layer1`**: Fully modelable by `tt-emule` today.
*   **`needs_stub`**: Uses high-level API wrappers (e.g., `*_tile`) that exist in the architecture but are not yet implemented in `tt-emule`.
*   **`ruled_out`**: Uses lower-layer features that the emulator cannot model (e.g., raw hardware instructions, SFPI vector intrinsics).

### Ruleout Buckets

When a kernel is `ruled_out`, it is categorized into specific buckets to help developers understand the bring-up effort required [scripts/classify_kernels.py 101-104](https://github.com/tenstorrent/tt-emule/blob/a57cf072/scripts/classify_kernels.py#L101-L104):

1.   `sfpi_intrinsics`: Hand-written SFPU vector code.
2.   `dst_register`: Raw DST register indexing (bypassing tile abstractions).
3.   `hw_instructions`: Raw MOP or `TTI_*` macros.
4.   `llk_headers`: Direct access to the underlying LLK header tree.

For details, see [Kernel Classification & Surface Analysis](https://deepwiki.com/tenstorrent/tt-emule/6.1-kernel-classification-and-surface-analysis).

* * *

## Kernel Bring-Up Workflow & Claude Skills

To streamline the addition of new kernels, `tt-emule` utilizes a structured workflow supported by specialized LLM "skills" and agents.

### Structured Workflow

The bring-up process follows a specific sequence of operations:

1.   **`implement-mock`**: Create initial stubs for missing headers.
2.   **`verify-mock`**: Ensure the mocks satisfy the JIT compiler's requirements.
3.   **`compute-llk-bringup`**: Map missing high-level compute wrappers to their underlying LLK implementations.
4.   **`parallel-mock-implementation`**: Efficiently implement multiple related stubs.

### Architecture Lookup Agents

Developers use "Sage" agents (`sage-wormhole`, `sage-blackhole`, `sage-quasar`) to look up hardware-specific details and register maps during the implementation of new `jit_hw` shims. These agents ensure that the emulated behavior matches the target hardware's expectations.

For details, see [Kernel Bring-Up Workflow & Claude Skills](https://deepwiki.com/tenstorrent/tt-emule/6.2-kernel-bring-up-workflow-and-claude-skills).

* * *

## Summary of Tooling Interactions

The following diagram illustrates how the developer tools interact with the codebase to maintain the emulation surface.

Sources: [scripts/gen_structure.py 5-12](https://github.com/tenstorrent/tt-emule/blob/a57cf072/scripts/gen_structure.py#L5-L12)[scripts/classify_kernels.py 50-55](https://github.com/tenstorrent/tt-emule/blob/a57cf072/scripts/classify_kernels.py#L50-L55)[scripts/emule_surface.py 39-42](https://github.com/tenstorrent/tt-emule/blob/a57cf072/scripts/emule_surface.py#L39-L42)

Dismiss
Refresh this wiki

Enter email to refresh
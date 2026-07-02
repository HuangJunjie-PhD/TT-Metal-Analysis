---
project: tt-lang
github: https://github.com/tenstorrent/tt-lang
deepwiki: https://deepwiki.com/tenstorrent/tt-lang/12-development-guide
---

# Development Guide

Relevant source files
*   [.github/workflows/call-build-toolchain.yml](https://github.com/tenstorrent/tt-lang/blob/d76e6233/.github/workflows/call-build-toolchain.yml)
*   [.github/workflows/call-build.yml](https://github.com/tenstorrent/tt-lang/blob/d76e6233/.github/workflows/call-build.yml)
*   [.github/workflows/call-test-hardware.yml](https://github.com/tenstorrent/tt-lang/blob/d76e6233/.github/workflows/call-test-hardware.yml)
*   [.github/workflows/call-test-sim.yml](https://github.com/tenstorrent/tt-lang/blob/d76e6233/.github/workflows/call-test-sim.yml)
*   [AGENTS.md](https://github.com/tenstorrent/tt-lang/blob/d76e6233/AGENTS.md?plain=1)
*   [CMakeLists.txt](https://github.com/tenstorrent/tt-lang/blob/d76e6233/CMakeLists.txt)
*   [cmake/modules/GetVersionFromGit.cmake](https://github.com/tenstorrent/tt-lang/blob/d76e6233/cmake/modules/GetVersionFromGit.cmake)
*   [cmake/modules/TTLangUtils.cmake](https://github.com/tenstorrent/tt-lang/blob/d76e6233/cmake/modules/TTLangUtils.cmake)
*   [env/activate.in](https://github.com/tenstorrent/tt-lang/blob/d76e6233/env/activate.in)
*   [env/ttlang_prompt_utils.sh](https://github.com/tenstorrent/tt-lang/blob/d76e6233/env/ttlang_prompt_utils.sh)
*   [scripts/docker-test.sh](https://github.com/tenstorrent/tt-lang/blob/d76e6233/scripts/docker-test.sh)
*   [test/TESTING.md](https://github.com/tenstorrent/tt-lang/blob/d76e6233/test/TESTING.md?plain=1)
*   [test/lit.cfg.py](https://github.com/tenstorrent/tt-lang/blob/d76e6233/test/lit.cfg.py)

This page provides guidance for developers working on `tt-lang` itself—implementing new compiler passes, fixing bugs, extending the Python DSL, or improving the simulator. For users writing kernels in `tt-lang`, see the [Programming Guide](https://deepwiki.com/tenstorrent/tt-lang/4-programming-guide) instead.

This guide covers development environment setup, the build-test-debug cycle, MLIR pass development, and troubleshooting compiler issues.

## Overview

`tt-lang` is an MLIR-based compiler with a Python frontend and a functional simulator. Development typically involves one of four workflows:

1.   **Python DSL Extension**: Adding new operators or DSL features in `python/ttl/`.
2.   **MLIR Pass Development**: Implementing transformations in `lib/` and `include/` following LLVM/MLIR conventions [AGENTS.md 44-46](https://github.com/tenstorrent/tt-lang/blob/d76e6233/AGENTS.md?plain=1#L44-L46)
3.   **Simulator Enhancement**: Extending the execution model in `python/sim/` using cooperative scheduling with greenlets [AGENTS.md 16](https://github.com/tenstorrent/tt-lang/blob/d76e6233/AGENTS.md?plain=1#L16-L16)
4.   **Testing Infrastructure**: Adding validation in `test/` using lit or pytest [test/TESTING.md 16-27](https://github.com/tenstorrent/tt-lang/blob/d76e6233/test/TESTING.md?plain=1#L16-L27)

### Development Workflow: From Code to Validation

The following diagram maps the high-level development stages to the specific code entities and tools used in the `tt-lang` repository.

Title: tt-lang Development Flow

Sources: [AGENTS.md 4-16](https://github.com/tenstorrent/tt-lang/blob/d76e6233/AGENTS.md?plain=1#L4-L16)[test/TESTING.md 16-27](https://github.com/tenstorrent/tt-lang/blob/d76e6233/test/TESTING.md?plain=1#L16-L27)[python/ttl/ttl_api.py 1-50](https://github.com/tenstorrent/tt-lang/blob/d76e6233/python/ttl/ttl_api.py#L1-L50)

## Development Workflow

### Setting Up the Development Environment

The project builds LLVM, `tt-mlir`, and `tt-metal` from git submodules under `third-party/`[CMakeLists.txt 134-155](https://github.com/tenstorrent/tt-lang/blob/d76e6233/CMakeLists.txt#L134-L155) Developers can use a pre-built toolchain by setting `TTLANG_USE_TOOLCHAIN=ON`[CMakeLists.txt 42](https://github.com/tenstorrent/tt-lang/blob/d76e6233/CMakeLists.txt#L42-L42)

`# Clone and enter directorygit clone https://github.com/tenstorrent/tt-lang.gitcd tt-lang # Build the projectcmake -G Ninja -B build -DTTLANG_USE_TOOLCHAIN=ONcmake --build build # Activate the environment (Required for all subsequent commands)source build/env/activate`
The `source build/env/activate` script sets `PYTHONPATH`, `LD_LIBRARY_PATH`, and `PATH` to include built binaries and the Python virtual environment [env/activate.in 42-58](https://github.com/tenstorrent/tt-lang/blob/d76e6233/env/activate.in#L42-L58)

For details, see [Development Workflow](https://deepwiki.com/tenstorrent/tt-lang/12.1-development-workflow).

Sources: [CMakeLists.txt 1-155](https://github.com/tenstorrent/tt-lang/blob/d76e6233/CMakeLists.txt#L1-L155)[env/activate.in 1-70](https://github.com/tenstorrent/tt-lang/blob/d76e6233/env/activate.in#L1-L70)[AGENTS.md 4-10](https://github.com/tenstorrent/tt-lang/blob/d76e6233/AGENTS.md?plain=1#L4-L10)

### Running Tests

`tt-lang` uses `lit` (LLVM Integrated Tester) for MLIR and Python IR tests, and `pytest` for functional and simulator tests [test/TESTING.md 3-8](https://github.com/tenstorrent/tt-lang/blob/d76e6233/test/TESTING.md?plain=1#L3-L8)

| Target / Tool | Command | Description |
| --- | --- | --- |
| **All Tests** | `cmake --build build --target check-ttlang-all` | Runs all suites sequentially [.github/workflows/call-build.yml 81-99](https://github.com/tenstorrent/tt-lang/blob/d76e6233/.github/workflows/call-build.yml#L81-L99) |
| **MLIR Only** | `cmake --build build --target check-ttlang-mlir` | Dialect and pass tests via `ttlang-opt`[test/TESTING.md 20](https://github.com/tenstorrent/tt-lang/blob/d76e6233/test/TESTING.md?plain=1#L20-L20) |
| **Python Lit** | `cmake --build build --target check-ttlang-python-lit` | Verifies IR and C++ generation from Python [test/TESTING.md 21](https://github.com/tenstorrent/tt-lang/blob/d76e6233/test/TESTING.md?plain=1#L21-L21) |
| **Simulator** | `pytest test/sim` | Software simulation of runtime behavior [AGENTS.md 16](https://github.com/tenstorrent/tt-lang/blob/d76e6233/AGENTS.md?plain=1#L16-L16) |
| **ME2E Suite** | `pytest test/me2e` | Middle end-to-end tests requiring hardware [test/TESTING.md 25](https://github.com/tenstorrent/tt-lang/blob/d76e6233/test/TESTING.md?plain=1#L25-L25) |

For details, see [Development Workflow](https://deepwiki.com/tenstorrent/tt-lang/12.1-development-workflow).

Sources: [test/TESTING.md 1-200](https://github.com/tenstorrent/tt-lang/blob/d76e6233/test/TESTING.md?plain=1#L1-L200)[.github/workflows/call-build.yml 76-100](https://github.com/tenstorrent/tt-lang/blob/d76e6233/.github/workflows/call-build.yml#L76-L100)[AGENTS.md 11-16](https://github.com/tenstorrent/tt-lang/blob/d76e6233/AGENTS.md?plain=1#L11-L16)

## Debugging Techniques

### MLIR Pipeline Inspection

Developers use environment variables to inspect the IR at different stages. These are checked by the Python DSL during kernel compilation [test/TESTING.md 63-67](https://github.com/tenstorrent/tt-lang/blob/d76e6233/test/TESTING.md?plain=1#L63-L67)

*   `TTLANG_INITIAL_MLIR`: Captures the IR immediately after Python AST translation [test/TESTING.md 36-37](https://github.com/tenstorrent/tt-lang/blob/d76e6233/test/TESTING.md?plain=1#L36-L37)
*   `TTLANG_FINAL_MLIR`: Captures the IR after all backend passes [test/lit.cfg.py 107](https://github.com/tenstorrent/tt-lang/blob/d76e6233/test/lit.cfg.py#L107-L107)
*   `TTLANG_COMPILE_ONLY`: Skips hardware execution to test compiler output on machines without a Tenstorrent device [test/TESTING.md 10](https://github.com/tenstorrent/tt-lang/blob/d76e6233/test/TESTING.md?plain=1#L10-L10)

For details, see [Debugging Techniques](https://deepwiki.com/tenstorrent/tt-lang/12.2-debugging-techniques).

Sources: [test/TESTING.md 33-91](https://github.com/tenstorrent/tt-lang/blob/d76e6233/test/TESTING.md?plain=1#L33-L91)[test/lit.cfg.py 104-108](https://github.com/tenstorrent/tt-lang/blob/d76e6233/test/lit.cfg.py#L104-L108)

### Docker-based Testing

The `scripts/docker-test.sh` script allows running tests in a container that matches the CI environment, mounting the local source tree to ensure `build/env/activate` works correctly [scripts/docker-test.sh 5-7](https://github.com/tenstorrent/tt-lang/blob/d76e6233/scripts/docker-test.sh#L5-L7)

For details, see [Debugging Techniques](https://deepwiki.com/tenstorrent/tt-lang/12.2-debugging-techniques).

Sources: [scripts/docker-test.sh 1-143](https://github.com/tenstorrent/tt-lang/blob/d76e6233/scripts/docker-test.sh#L1-L143)

## MLIR Pass Development

The compiler lowers the `TTL` dialect to the `TTKernel` dialect, which maps to hardware-specific operations [test/TESTING.md 5-8](https://github.com/tenstorrent/tt-lang/blob/d76e6233/test/TESTING.md?plain=1#L5-L8)

### Compiler Pipeline Architecture

The following diagram bridges the logical compilation stages to the specific MLIR dialects and tools.

Title: Compilation Pipeline and Dialect Transitions

Sources: [test/TESTING.md 5-100](https://github.com/tenstorrent/tt-lang/blob/d76e6233/test/TESTING.md?plain=1#L5-L100)[AGENTS.md 35-50](https://github.com/tenstorrent/tt-lang/blob/d76e6233/AGENTS.md?plain=1#L35-L50)[test/lit.cfg.py 98-102](https://github.com/tenstorrent/tt-lang/blob/d76e6233/test/lit.cfg.py#L98-L102)

For details, see [MLIR Pass Development](https://deepwiki.com/tenstorrent/tt-lang/12.3-mlir-pass-development).

## Troubleshooting

Common development hurdles include:

*   **Environment Activation**: Forgetting to `source build/env/activate` leads to missing dependencies or incorrect Python paths [env/activate.in 14-16](https://github.com/tenstorrent/tt-lang/blob/d76e6233/env/activate.in#L14-L16)
*   **LLVM Version Mismatch**: The build system verifies the LLVM SHA to prevent binary compatibility issues [cmake/modules/TTLangUtils.cmake 148-153](https://github.com/tenstorrent/tt-lang/blob/d76e6233/cmake/modules/TTLangUtils.cmake#L148-L153)
*   **Pattern Rewriter Errors**: Using `emitOpError()` inside a pattern rewriter instead of `notifyMatchFailure()` can cause silent pass successes followed by downstream crashes [AGENTS.md 66-71](https://github.com/tenstorrent/tt-lang/blob/d76e6233/AGENTS.md?plain=1#L66-L71)

For details, see [Troubleshooting](https://deepwiki.com/tenstorrent/tt-lang/12.4-troubleshooting).

Sources: [AGENTS.md 66-77](https://github.com/tenstorrent/tt-lang/blob/d76e6233/AGENTS.md?plain=1#L66-L77)[cmake/modules/TTLangUtils.cmake 148-175](https://github.com/tenstorrent/tt-lang/blob/d76e6233/cmake/modules/TTLangUtils.cmake#L148-L175)[env/activate.in 1-26](https://github.com/tenstorrent/tt-lang/blob/d76e6233/env/activate.in#L1-L26)

## Claude Slash Commands for tt-lang

AI-assisted development is supported through specific Claude slash commands. These tools assist in importing code, optimizing kernels, and analyzing compilation failures.

*   **/ttl-bug**: Used for diagnosing compiler errors by analyzing MLIR dumps.
*   **/ttl-test**: Integrates with `run-test.sh` for remote hardware execution and validation.

For details, see [Claude Slash Commands for tt-lang](https://deepwiki.com/tenstorrent/tt-lang/12.5-claude-slash-commands-for-tt-lang).

Sources: [AGENTS.md 1-131](https://github.com/tenstorrent/tt-lang/blob/d76e6233/AGENTS.md?plain=1#L1-L131)

Dismiss
Refresh this wiki

Enter email to refresh

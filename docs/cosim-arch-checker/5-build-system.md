---
project: cosim-arch-checker
github: https://github.com/tenstorrent/cosim-arch-checker
deepwiki: https://deepwiki.com/tenstorrent/cosim-arch-checker/5-build-system
---

# Build System

Relevant source files
*   [BUILD.bazel](https://github.com/tenstorrent/cosim-arch-checker/blob/157c6637/BUILD.bazel)
*   [Makefile](https://github.com/tenstorrent/cosim-arch-checker/blob/157c6637/Makefile)
*   [WORKSPACE](https://github.com/tenstorrent/cosim-arch-checker/blob/157c6637/WORKSPACE)
*   [build/.gitignore](https://github.com/tenstorrent/cosim-arch-checker/blob/157c6637/build/.gitignore)

This document covers the build system architecture for the cosim-arch-checker framework. The build system provides two alternative approaches for compiling the project: a traditional Makefile-based build and a modern Bazel-based build. Both approaches produce shared libraries or binaries suitable for integration with SystemVerilog testbenches.

For information about configuring build parameters and processor-specific options, see [DUT Parameters](https://deepwiki.com/tenstorrent/cosim-arch-checker/4.2-dut-parameters). For details on integrating the built artifacts with simulation environments, see [Integration Guide](https://deepwiki.com/tenstorrent/cosim-arch-checker/6-integration-guide).

## Build System Architecture

The cosim-arch-checker implements a dual build system to support different development workflows and integration requirements:

### Build System Overview

**Sources:**[BUILD.bazel 1-23](https://github.com/tenstorrent/cosim-arch-checker/blob/157c6637/BUILD.bazel#L1-L23)[Makefile 1-45](https://github.com/tenstorrent/cosim-arch-checker/blob/157c6637/Makefile#L1-L45)[WORKSPACE 1-16](https://github.com/tenstorrent/cosim-arch-checker/blob/157c6637/WORKSPACE#L1-L16)

## Makefile Build System

The Makefile provides a traditional build approach that creates a shared library suitable for direct linking with simulation environments.

### Build Configuration

The Makefile uses the following key settings:

| Setting | Value | Purpose |
| --- | --- | --- |
| Compiler | `g++` | C++ compiler |
| Standard | `-std=c++17` | C++17 language standard |
| Position Independent Code | `-fpic` | Required for shared library |
| Warning Level | `-Wall -Werror` | Strict warning handling |

### Include Paths

The build system includes headers from multiple directories:

**Sources:**[Makefile 3](https://github.com/tenstorrent/cosim-arch-checker/blob/157c6637/Makefile#L3-L3)

### Build Process

The Makefile implements a multi-stage build process:

1.   **Source Discovery**: Automatically finds all `.cpp`, `.cxx`, and `.cc` files recursively
2.   **Object Compilation**: Compiles each source file to an object file with dependency tracking
3.   **Shared Library Creation**: Links all object files into `lib/libcosim.so`

Key build targets:

*   `all`: Default target that builds the shared library
*   `clean`: Removes all build artifacts from `build/` and `lib/` directories
*   `lib/libcosim.so`: The main shared library output

**Sources:**[Makefile 12-15](https://github.com/tenstorrent/cosim-arch-checker/blob/157c6637/Makefile#L12-L15)[Makefile 24](https://github.com/tenstorrent/cosim-arch-checker/blob/157c6637/Makefile#L24-L24)[Makefile 26-27](https://github.com/tenstorrent/cosim-arch-checker/blob/157c6637/Makefile#L26-L27)[Makefile 29-30](https://github.com/tenstorrent/cosim-arch-checker/blob/157c6637/Makefile#L29-L30)

## Bazel Build System

The Bazel build system provides a more modern approach with explicit dependency management and external repository integration.

### Build Target Configuration

The primary build target is defined as a `cc_binary` with shared library output:

**Sources:**[BUILD.bazel 1-4](https://github.com/tenstorrent/cosim-arch-checker/blob/157c6637/BUILD.bazel#L1-L4)[BUILD.bazel 15-16](https://github.com/tenstorrent/cosim-arch-checker/blob/157c6637/BUILD.bazel#L15-L16)[BUILD.bazel 18-20](https://github.com/tenstorrent/cosim-arch-checker/blob/157c6637/BUILD.bazel#L18-L20)

### External Dependencies

The WORKSPACE file defines external repositories:

| Repository | Source | Purpose |
| --- | --- | --- |
| `@whisper` | `github.com/tenstorrent/whisper.git` | RISC-V ISS reference model |
| `@googletest` | `github.com/google/googletest` | Unit testing framework |

The Whisper dependency is pinned to a specific commit hash for reproducible builds.

**Sources:**[WORKSPACE 3-8](https://github.com/tenstorrent/cosim-arch-checker/blob/157c6637/WORKSPACE#L3-L8)[WORKSPACE 10-15](https://github.com/tenstorrent/cosim-arch-checker/blob/157c6637/WORKSPACE#L10-L15)

### Compiler Options

Bazel uses the same include path structure as the Makefile but organizes them differently:

**Sources:**[BUILD.bazel 5-14](https://github.com/tenstorrent/cosim-arch-checker/blob/157c6637/BUILD.bazel#L5-L14)

## Build Artifacts and Integration

### Output Comparison

| Build System | Output | Location | Usage |
| --- | --- | --- | --- |
| Makefile | `libcosim.so` | `lib/` directory | Direct linking with simulation |
| Bazel | `dpi` binary | Bazel output tree | Bazel-managed integration |

### Build Directory Structure

**Sources:**[Makefile 7-9](https://github.com/tenstorrent/cosim-arch-checker/blob/157c6637/Makefile#L7-L9)[build/.gitignore 1-5](https://github.com/tenstorrent/cosim-arch-checker/blob/157c6637/build/.gitignore#L1-L5)

## Usage Patterns

### Makefile Build Commands

`# Build the shared librarymake all # Clean build artifactsmake clean # Build with verbose outputmake VERBOSE=1`
### Bazel Build Commands

`# Build the dpi targetbazel build :dpi # Build with specific configurationbazel build :dpi --copt=-DCONFIG=MediumBoomVecConfig # Clean Bazel artifactsbazel clean`
Both build systems support parallel compilation and incremental builds. The choice between them depends on the integration requirements and development workflow preferences.

**Sources:**[BUILD.bazel 1-2](https://github.com/tenstorrent/cosim-arch-checker/blob/157c6637/BUILD.bazel#L1-L2)[Makefile 22-24](https://github.com/tenstorrent/cosim-arch-checker/blob/157c6637/Makefile#L22-L24)

Dismiss
Refresh this wiki

Enter email to refresh
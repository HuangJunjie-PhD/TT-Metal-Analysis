---
project: riescue
github: https://github.com/tenstorrent/riescue
deepwiki: https://deepwiki.com/tenstorrent/riescue/3-development-environment
---

# Development Environment

 Relevant source files

* [.github/CODE\_OF\_CONDUCT.md](https://github.com/tenstorrent/riescue/blob/07cefde3/.github/CODE_OF_CONDUCT.md?plain=1)
* [.github/CONTRIBUTING.md](https://github.com/tenstorrent/riescue/blob/07cefde3/.github/CONTRIBUTING.md?plain=1)
* [.github/ISSUE\_TEMPLATE/pull\_request\_template.md](https://github.com/tenstorrent/riescue/blob/07cefde3/.github/ISSUE_TEMPLATE/pull_request_template.md?plain=1)
* [docs/container.md](https://github.com/tenstorrent/riescue/blob/07cefde3/docs/container.md?plain=1)
* [docs/images/Roadmap.png](https://github.com/tenstorrent/riescue/blob/07cefde3/docs/images/Roadmap.png)
* [infra/Container.def](https://github.com/tenstorrent/riescue/blob/07cefde3/infra/Container.def)
* [infra/singularity/container.py](https://github.com/tenstorrent/riescue/blob/07cefde3/infra/singularity/container.py)

This document provides a comprehensive overview of the RiESCUE development environment, including the containerized infrastructure, build system, and dependency management. The development environment is built around a Singularity (Apptainer) container that encapsulates all external dependencies, ensuring reproducibility across different host systems.

For information about contributing code and using the quality tools (linting, testing), see [Development Workflow](/tenstorrent/riescue/5-development-workflow). For details on building and deploying documentation, see [Documentation System](/tenstorrent/riescue/6-documentation-system).

## Overview

RiESCUE uses a containerized development approach to eliminate "works on my machine" problems. The container provides:

* **Rocky Linux 9.1 base environment** with all system dependencies
* **RISC-V toolchain** (GNU compiler and binutils)
* **Instruction Set Simulators** (Whisper and Spike)
* **Python development environment** with all required packages
* **Build scripts** for complex external dependencies

The container is managed through Python scripts that handle building, running, and distributing container images.

## Development Environment Architecture

**Development Environment Layers**

The development environment is organized in five layers, each building upon the previous one.

Sources: [infra/Container.def1-57](https://github.com/tenstorrent/riescue/blob/07cefde3/infra/Container.def#L1-L57) [infra/singularity/container.py1-336](https://github.com/tenstorrent/riescue/blob/07cefde3/infra/singularity/container.py#L1-L336)

## Container System

The container system uses Singularity (also known as Apptainer) to provide an isolated, reproducible development environment. Unlike Docker, Singularity does not require root privileges and integrates seamlessly with the host filesystem.

### Container Definition

The container is defined in [infra/Container.def1-57](https://github.com/tenstorrent/riescue/blob/07cefde3/infra/Container.def#L1-L57) The definition file uses a multi-stage build process:

| Section | Purpose | Key Operations |
| --- | --- | --- |
| `bootstrap` & `From` | Base image | Rocky Linux 9.1 from Docker Hub |
| `%environment` | Runtime environment | PATH and LD\_LIBRARY\_PATH setup |
| `%runscript` | Container entry | Set ulimit for stack size |
| `%labels` | Metadata | VERSION tracking (currently 1.30) |
| `%files` | Source files | Copy pyproject.toml, source code, build scripts |
| `%post` | Build steps | Install dependencies, build tools, Python packages |

The `%post` section executes three main build phases:

1. **Base dependencies**: [infra/Container.def30](https://github.com/tenstorrent/riescue/blob/07cefde3/infra/Container.def#L30-L30) calls `build_base_deps.sh`
2. **Python packages**: [infra/Container.def34-46](https://github.com/tenstorrent/riescue/blob/07cefde3/infra/Container.def#L34-L46) installs Python dependencies
3. **External tools**: [infra/Container.def52-56](https://github.com/tenstorrent/riescue/blob/07cefde3/infra/Container.def#L52-L56) builds Whisper and Spike

Sources: [infra/Container.def1-57](https://github.com/tenstorrent/riescue/blob/07cefde3/infra/Container.def#L1-L57)

### Container Management

The `Container` class in [infra/singularity/container.py40-336](https://github.com/tenstorrent/riescue/blob/07cefde3/infra/singularity/container.py#L40-L336) provides a Python interface for container lifecycle management:

**Container Class Key Methods**

The `Container` class implements version tracking and remote registry support:

* **Version management**: [infra/singularity/container.py204-225](https://github.com/tenstorrent/riescue/blob/07cefde3/infra/singularity/container.py#L204-L225) - Properties for `sif_version`, `def_version`, and `container_version`
* **Build**: [infra/singularity/container.py85-112](https://github.com/tenstorrent/riescue/blob/07cefde3/infra/singularity/container.py#L85-L112) - Increments version if needed, builds with `--fakeroot`, optionally pushes
* **Run**: [infra/singularity/container.py114-170](https://github.com/tenstorrent/riescue/blob/07cefde3/infra/singularity/container.py#L114-L170) - Handles argument parsing, checks version compatibility, executes with bind mounts
* **Push/Pull**: [infra/singularity/container.py172-187](https://github.com/tenstorrent/riescue/blob/07cefde3/infra/singularity/container.py#L172-L187) - Upload/download from remote registry

Sources: [infra/singularity/container.py40-336](https://github.com/tenstorrent/riescue/blob/07cefde3/infra/singularity/container.py#L40-L336)

### Container Configuration

The optional `.container_config` file provides additional configuration. The `Container` class searches for it in three locations (in order):

1. Current working directory: `./.container_config`
2. Repository root: `{repo_path}/.container_config`
3. Infrastructure directory: `{repo_path}/infra/.container_config`

See [infra/singularity/container.py273-289](https://github.com/tenstorrent/riescue/blob/07cefde3/infra/singularity/container.py#L273-L289) for the search logic.

**Configuration Schema**

| Key | Type | Purpose | Example |
| --- | --- | --- | --- |
| `registry_uri` | string | Container registry URI | `"oras://registry.example.com/riescue"` |
| `registry_remote` | string | Singularity remote name | `"example-registry"` |
| `binds` | string | Additional bind mounts (comma-separated) | `"/data:/mnt/data,/scratch:/scratch"` |
| `remote_token_path` | string | Path to authentication token | `"~/.secrets/registry_token"` |

The configuration is loaded in [infra/singularity/container.py52-67](https://github.com/tenstorrent/riescue/blob/07cefde3/infra/singularity/container.py#L52-L67) If `binds` are specified, they are appended to the default binds `/usr/lib64:/shared`.

Sources: [infra/singularity/container.py43-83](https://github.com/tenstorrent/riescue/blob/07cefde3/infra/singularity/container.py#L43-L83) [docs/container.md16-26](https://github.com/tenstorrent/riescue/blob/07cefde3/docs/container.md?plain=1#L16-L26)

### Container Entry Points

The repository provides wrapper scripts for container operations:

* `./infra/container-build` - Builds the container image
* `./infra/container-run` - Runs commands inside the container
* `./infra/container-push` - Pushes container to remote registry
* `./infra/container-pull` - Pulls container from remote registry

These scripts instantiate the `Container` class and call the corresponding methods. The `container-run` script supports two modes:

1. **Command mode**: `./infra/container-run  [args]` - Execute a specific command
2. **Interactive mode**: `./infra/container-run` - Launch bash shell

Sources: [infra/singularity/container.py114-170](https://github.com/tenstorrent/riescue/blob/07cefde3/infra/singularity/container.py#L114-L170) [docs/container.md9-14](https://github.com/tenstorrent/riescue/blob/07cefde3/docs/container.md?plain=1#L9-L14)

## Build Scripts and Dependencies

The container build process relies on three bash scripts that install system dependencies and compile external tools. These scripts are copied into the container during the build phase [infra/Container.def24-26](https://github.com/tenstorrent/riescue/blob/07cefde3/infra/Container.def#L24-L26) and executed in the `%post` section.

### Dependency Installation Flow

**Build Script Dependencies**

Each script builds upon the previous layer's outputs.

Sources: [infra/Container.def24-30](https://github.com/tenstorrent/riescue/blob/07cefde3/infra/Container.def#L24-L30) [infra/Container.def52-56](https://github.com/tenstorrent/riescue/blob/07cefde3/infra/Container.def#L52-L56)

### Base System Dependencies

The `build_base_deps.sh` script installs system-level packages required for building C/C++ projects and RISC-V tools. Key package categories include:

* **Build tools**: `gcc`, `gcc-c++`, `make`, `cmake`, `autoconf`, `automake`, `libtool`
* **Development libraries**: `boost-devel`, `device-mapper-devel`, `zlib-devel`
* **Version control**: `git`, `git-lfs`
* **Utilities**: `wget`, `vim`, `dtc` (device tree compiler)

The script uses Rocky Linux's `dnf` package manager. This establishes the foundation for compiling Whisper and Spike from source.

Sources: [infra/Container.def30](https://github.com/tenstorrent/riescue/blob/07cefde3/infra/Container.def#L30-L30)

### Building Whisper

The `build_whisper.sh` script compiles the Whisper instruction set simulator. Whisper is Tenstorrent's high-performance RISC-V simulator. The build process:

1. Clones the repository from `https://github.com/tenstorrent/whisper`
2. Checks out specific commit `b24e30f2` for reproducibility
3. Configures with `--prefix=/usr` to install system-wide
4. Compiles and installs to `/usr/bin/whisper`

The Whisper build requires the C++ compiler and Boost libraries installed by `build_base_deps.sh`.

Sources: [infra/Container.def52-53](https://github.com/tenstorrent/riescue/blob/07cefde3/infra/Container.def#L52-L53)

### Building Spike

The `build_spike.sh` script builds two variants of the Spike simulator:

1. **Standard Spike** - Reference RISC-V simulator from `riscv-isa-sim` repository (commit `4703ad98`)
2. **tt\_spike** - Tenstorrent's fork with custom extensions (commit `344b9ef3`)

Both simulators are configured with `--prefix=/usr/local` and installed to:

* `/usr/local/bin/spike`
* `/usr/local/bin/tt_spike`

The dual installation allows tests to run against both the reference implementation and Tenstorrent-specific features.

Sources: [infra/Container.def55-56](https://github.com/tenstorrent/riescue/blob/07cefde3/infra/Container.def#L55-L56)

## Python Package Configuration

The Python environment is configured through `pyproject.toml`, which defines the package metadata, dependencies, and entry points. The container installs Python packages in two stages:

1. **Core dependencies** [infra/Container.def34-45](https://github.com/tenstorrent/riescue/blob/07cefde3/infra/Container.def#L34-L45): Direct pip installation of common packages
2. **RiESCUE package**: Installed later by users with `pip install -e .` for development

### Package Metadata and Dependencies

**Python Package Structure**

The pyproject.toml defines three command-line entry points and manages dependencies.

Sources: [infra/Container.def34-46](https://github.com/tenstorrent/riescue/blob/07cefde3/infra/Container.def#L34-L46)

### Core Dependencies

The container pre-installs essential Python packages [infra/Container.def35-45](https://github.com/tenstorrent/riescue/blob/07cefde3/infra/Container.def#L35-L45):

| Package | Purpose |
| --- | --- |
| `sortedcontainers` | Efficient sorted data structures |
| `pyyaml` | YAML configuration parsing |
| `numpy` | Numerical operations |
| `sphinx`, `sphinx-rtd-theme` | Documentation generation |
| `flake8` | Code linting |
| `black` | Code formatting |
| `intervaltree` | Interval-based data structures |
| `coverage` | Test coverage analysis |
| `pyright[nodejs]` | Type checking with bundled Node.js |

The `pip install` commands are executed with the container as the current environment, ensuring all packages are available when the container runs.

Sources: [infra/Container.def34-46](https://github.com/tenstorrent/riescue/blob/07cefde3/infra/Container.def#L34-L46)

### External RISC-V Dependencies

In addition to PyPI packages, the Python environment depends on the `coretp` package from the `riscv-coretp` repository. This package provides test plan definitions for RISC-V compliance testing and is pinned to a specific commit (`5da80d20`) in the pyproject.toml.

The RISC-V GNU toolchain is not installed via pip but rather:

* **In CI**: Downloaded as a pre-built archive
* **In development**: Expected at `/opt/riscv` or the path specified by `$RV_TOOLCHAIN` environment variable

The `.env` file can be used to set `RV_TOOLCHAIN` for custom installations. The container's `_load_dotenv()` method [infra/singularity/container.py291-303](https://github.com/tenstorrent/riescue/blob/07cefde3/infra/singularity/container.py#L291-L303) reads this file and updates the environment.

Sources: [infra/singularity/container.py73-82](https://github.com/tenstorrent/riescue/blob/07cefde3/infra/singularity/container.py#L73-L82) [infra/singularity/container.py291-303](https://github.com/tenstorrent/riescue/blob/07cefde3/infra/singularity/container.py#L291-L303)

### Entry Points

The package defines multiple command-line entry points that are installed when running `pip install -e .`:

* **riescued**: Main entry point for directed test framework
* **riescuec**: Compliance test generator
* **ctk**: Compliance Test Kit high-level interface

These entry points map to Python functions in the respective modules. After installation, they become available as commands in the shell.

Sources: [infra/Container.def20-22](https://github.com/tenstorrent/riescue/blob/07cefde3/infra/Container.def#L20-L22)

## Development Workflow

The typical development workflow with the container system:

**Typical Development Cycle**

A standard development session involves building or pulling the container, entering it, and iterating on code.

1. **Initial Setup**: Clone repository and build/pull container
2. **Enter Container**: Use `./infra/container-run` for interactive shell
3. **Install Package**: Run `pip install -e .` for editable installation
4. **Development Loop**: Edit code, run tests, fix issues
5. **Quality Checks**: Run linters before committing

The container provides version tracking [infra/singularity/container.py132-146](https://github.com/tenstorrent/riescue/blob/07cefde3/infra/singularity/container.py#L132-L146) to ensure local and remote versions stay synchronized. If a mismatch is detected, the `run()` method automatically pulls the latest version from the registry (if configured) or rebuilds locally.

Sources: [infra/singularity/container.py114-170](https://github.com/tenstorrent/riescue/blob/07cefde3/infra/singularity/container.py#L114-L170) [.github/CONTRIBUTING.md17-34](https://github.com/tenstorrent/riescue/blob/07cefde3/.github/CONTRIBUTING.md?plain=1#L17-L34)

### Environment Variables

The container respects environment variables set in the `.env` file at the repository root. The `_load_dotenv()` method [infra/singularity/container.py291-303](https://github.com/tenstorrent/riescue/blob/07cefde3/infra/singularity/container.py#L291-L303) parses this file and sets environment variables for the current session.

Key environment variables:

* `RV_TOOLCHAIN`: Path to RISC-V GNU toolchain (e.g., `/opt/riscv`)
* `NO_SINGULARITY`: Set to `1` to skip container checks in build scripts
* `BUILD_TT_DOCS`: Controls whether to build internal documentation

The container also sets default environment variables in the `%environment` section [infra/Container.def5-7](https://github.com/tenstorrent/riescue/blob/07cefde3/infra/Container.def#L5-L7):

* `PATH`: Prepends `/usr/local/bin` for Spike binaries
* `LD_LIBRARY_PATH`: Includes `/usr/lib64` and `/shared` for shared libraries

Sources: [infra/Container.def5-7](https://github.com/tenstorrent/riescue/blob/07cefde3/infra/Container.def#L5-L7) [infra/singularity/container.py291-303](https://github.com/tenstorrent/riescue/blob/07cefde3/infra/singularity/container.py#L291-L303)

### Version Management

The container uses a three-component versioning system:

**Container Version Tracking**

The version system ensures reproducibility and prevents version drift.

* **def\_version**: [infra/Container.def14](https://github.com/tenstorrent/riescue/blob/07cefde3/infra/Container.def#L14-L14) - Semantic version in Container.def (currently 1.30)
* **sif\_version**: Extracted from built .sif file labels [infra/singularity/container.py229-236](https://github.com/tenstorrent/riescue/blob/07cefde3/infra/singularity/container.py#L229-L236)
* **container\_version**: Read from `infra/container/singularity-id` [infra/singularity/container.py217-220](https://github.com/tenstorrent/riescue/blob/07cefde3/infra/singularity/container.py#L217-L220)

The `build()` method [infra/singularity/container.py85-112](https://github.com/tenstorrent/riescue/blob/07cefde3/infra/singularity/container.py#L85-L112) increments `def_version` if it matches `container_version`, builds the new container, computes the SHA256 of the filesystem payload [infra/singularity/container.py189-202](https://github.com/tenstorrent/riescue/blob/07cefde3/infra/singularity/container.py#L189-L202) and writes the version string (format: `{version}-{sha}`) to the `singularity-id` file.

Sources: [infra/singularity/container.py85-112](https://github.com/tenstorrent/riescue/blob/07cefde3/infra/singularity/container.py#L85-L112) [infra/singularity/container.py189-202](https://github.com/tenstorrent/riescue/blob/07cefde3/infra/singularity/container.py#L189-L202) [infra/singularity/container.py217-236](https://github.com/tenstorrent/riescue/blob/07cefde3/infra/singularity/container.py#L217-L236)

### Bind Mounts

The container uses bind mounts to share directories between host and container. Default binds are set to `/usr/lib64:/shared` [infra/singularity/container.py41](https://github.com/tenstorrent/riescue/blob/07cefde3/infra/singularity/container.py#L41-L41) which maps the host's `/usr/lib64` to `/shared` inside the container.

Additional binds can be specified:

1. **Via `.container_config`**: [infra/singularity/container.py57-59](https://github.com/tenstorrent/riescue/blob/07cefde3/infra/singularity/container.py#L57-L59) - Persistent configuration
2. **Via command line**: `--singularity-binds=/host/path:/container/path` [infra/singularity/container.py127-128](https://github.com/tenstorrent/riescue/blob/07cefde3/infra/singularity/container.py#L127-L128)

The `run()` method [infra/singularity/container.py148-156](https://github.com/tenstorrent/riescue/blob/07cefde3/infra/singularity/container.py#L148-L156) checks if each bind source exists on the host before adding it to the bind list, preventing errors from missing directories.

Sources: [infra/singularity/container.py41](https://github.com/tenstorrent/riescue/blob/07cefde3/infra/singularity/container.py#L41-L41) [infra/singularity/container.py148-156](https://github.com/tenstorrent/riescue/blob/07cefde3/infra/singularity/container.py#L148-L156) [infra/singularity/container.py305-325](https://github.com/tenstorrent/riescue/blob/07cefde3/infra/singularity/container.py#L305-L325)

Refresh this wiki
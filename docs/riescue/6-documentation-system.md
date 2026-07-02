---
project: riescue
github: https://github.com/tenstorrent/riescue
deepwiki: https://deepwiki.com/tenstorrent/riescue/6-documentation-system
---

# Documentation System

Relevant source files
*   [docs/README.md](https://github.com/tenstorrent/riescue/blob/07cefde3/docs/README.md?plain=1)
*   [docs/build.py](https://github.com/tenstorrent/riescue/blob/07cefde3/docs/build.py)
*   [docs/source/conf.py](https://github.com/tenstorrent/riescue/blob/07cefde3/docs/source/conf.py)
*   [docs/source/index.rst](https://github.com/tenstorrent/riescue/blob/07cefde3/docs/source/index.rst)

## Purpose and Scope

The Documentation System provides a Sphinx-based framework for generating and maintaining comprehensive documentation for the RiESCUE repository. This page provides an overview of the documentation architecture, source organization, build system, and deployment workflow.

For detailed information on building documentation locally, see [Building Documentation](https://deepwiki.com/tenstorrent/riescue/6.1-building-documentation). For guidance on documentation structure and contributing content, see [Documentation Structure](https://deepwiki.com/tenstorrent/riescue/6.2-documentation-structure). For information on CI/CD deployment of documentation, see [Documentation Deployment](https://deepwiki.com/tenstorrent/riescue/5.3.2-documentation-deployment).

Sources: [docs/README.md 1-47](https://github.com/tenstorrent/riescue/blob/07cefde3/docs/README.md?plain=1#L1-L47)[docs/build.py 1-223](https://github.com/tenstorrent/riescue/blob/07cefde3/docs/build.py#L1-L223)

## Architecture Overview

The documentation system is built on [Sphinx](https://www.sphinx-doc.org/), a Python documentation generator that converts reStructuredText (`.rst`) files into HTML. The system supports multiple source trees, custom Sphinx directives, and flexible build modes for both local development and CI/CD deployment.

### Documentation System Components

Sources: [docs/build.py 21-223](https://github.com/tenstorrent/riescue/blob/07cefde3/docs/build.py#L21-L223)[docs/source/conf.py 1-132](https://github.com/tenstorrent/riescue/blob/07cefde3/docs/source/conf.py#L1-L132)[docs/README.md 1-47](https://github.com/tenstorrent/riescue/blob/07cefde3/docs/README.md?plain=1#L1-L47)

### Build and Deployment Flow

Sources: [docs/build.py 22-123](https://github.com/tenstorrent/riescue/blob/07cefde3/docs/build.py#L22-L123)[docs/build.py 179-219](https://github.com/tenstorrent/riescue/blob/07cefde3/docs/build.py#L179-L219)

## Source Organization

The documentation system maintains two separate source trees:

| Source Tree | Path | Purpose | Activation |
| --- | --- | --- | --- |
| Public | `docs/source/` | External-facing API documentation | Default |
| Internal | `docs/tt_source/` | Internal developer documentation | `BUILD_TT_DOCS` environment variable |

The source tree selection happens at initialization time in the `DocBuilder` class [docs/build.py 22-26](https://github.com/tenstorrent/riescue/blob/07cefde3/docs/build.py#L22-L26):

### Public Documentation Structure

The public documentation tree (`docs/source/`) contains:

*   **Tutorials** (`tutorials/index.rst`) - Step-by-step learning guides
*   **User Guides** (`user_guides/index.rst`) - Task-oriented documentation
*   **Reference** (`reference/index.rst`) - API reference documentation
*   **Main Index** (`index.rst`) - Landing page and navigation

The public API is committed to by the RiESCUE team with deprecation periods for breaking changes [docs/README.md 12-14](https://github.com/tenstorrent/riescue/blob/07cefde3/docs/README.md?plain=1#L12-L14)

Sources: [docs/source/index.rst 1-46](https://github.com/tenstorrent/riescue/blob/07cefde3/docs/source/index.rst#L1-L46)[docs/README.md 12-14](https://github.com/tenstorrent/riescue/blob/07cefde3/docs/README.md?plain=1#L12-L14)

## Build System: DocBuilder Class

The `DocBuilder` class [docs/build.py 21-222](https://github.com/tenstorrent/riescue/blob/07cefde3/docs/build.py#L21-L222) provides the core build functionality:

### Class Attributes and Initialization

| Attribute | Type | Description |
| --- | --- | --- |
| `docs_dir` | `Path` | Documentation directory (parent of script) |
| `defualt_src` | `Path` | Default source directory (public/internal) |
| `repo_dir` | `Path` | Repository root directory |
| `clean` | `bool` | Whether to clean build directory |
| `check` | `bool` | Whether to treat warnings as errors |
| `local_host` | `bool` | Whether to start local HTTP server |
| `interactive` | `bool` | Whether to enable interactive reload |

### Build Modes

The `DocBuilder` supports four primary build modes:

Sources: [docs/build.py 29-94](https://github.com/tenstorrent/riescue/blob/07cefde3/docs/build.py#L29-L94)[docs/build.py 102-123](https://github.com/tenstorrent/riescue/blob/07cefde3/docs/build.py#L102-L123)

### Key Methods

| Method | Purpose | Line Reference |
| --- | --- | --- |
| `add_args()` | Define CLI argument parser | [docs/build.py 55-73](https://github.com/tenstorrent/riescue/blob/07cefde3/docs/build.py#L55-L73) |
| `run_cli()` | Main CLI entry point | [docs/build.py 76-81](https://github.com/tenstorrent/riescue/blob/07cefde3/docs/build.py#L76-L81) |
| `run()` | Execute build workflow | [docs/build.py 83-93](https://github.com/tenstorrent/riescue/blob/07cefde3/docs/build.py#L83-L93) |
| `clean_build_dir()` | Remove and recreate build directory | [docs/build.py 95-100](https://github.com/tenstorrent/riescue/blob/07cefde3/docs/build.py#L95-L100) |
| `build_docs()` | Execute Sphinx build | [docs/build.py 102-122](https://github.com/tenstorrent/riescue/blob/07cefde3/docs/build.py#L102-L122) |
| `start_local_host()` | Start HTTP server for local viewing | [docs/build.py 179-218](https://github.com/tenstorrent/riescue/blob/07cefde3/docs/build.py#L179-L218) |
| `start_interactive_host()` | Interactive server with reload | [docs/build.py 124-177](https://github.com/tenstorrent/riescue/blob/07cefde3/docs/build.py#L124-L177) |

### Sphinx Build Command Construction

The `build_docs()` method constructs the Sphinx command [docs/build.py 110-122](https://github.com/tenstorrent/riescue/blob/07cefde3/docs/build.py#L110-L122):

```
sphinx-build [-W -E] <source_dir> <build_dir>
```

Where:

*   `-W` flag treats warnings as errors (enabled with `--check`)
*   `-E` flag forces rebuild of all files (enabled with `--check`)
*   Locale set to `C` to avoid locale errors in containers

Sources: [docs/build.py 102-122](https://github.com/tenstorrent/riescue/blob/07cefde3/docs/build.py#L102-L122)

## Sphinx Configuration

The Sphinx configuration file [docs/source/conf.py 1-132](https://github.com/tenstorrent/riescue/blob/07cefde3/docs/source/conf.py#L1-L132) defines:

### Project Metadata

Version extraction from `pyproject.toml`[docs/source/conf.py 72-83](https://github.com/tenstorrent/riescue/blob/07cefde3/docs/source/conf.py#L72-L83):

### Extensions and Configuration

| Extension | Purpose |
| --- | --- |
| `sphinx.ext.autodoc` | Automatic API documentation from docstrings |
| `sphinx.ext.autosummary` | Generate summary tables for modules |

Configuration settings [docs/source/conf.py 88-98](https://github.com/tenstorrent/riescue/blob/07cefde3/docs/source/conf.py#L88-L98):

| Setting | Value | Purpose |
| --- | --- | --- |
| `autodoc_default_options` | `{"show-inheritance": False}` | Hide inheritance info |
| `autodoc_member_order` | `"bysource"` | Order members by source order |
| `exclude_patterns` | `["public", "_build", ...]` | Exclude build artifacts |
| `templates_path` | `["_templates", "../common/_templates"]` | Template directories |

### Theme Configuration

The documentation uses the `sphinx_rtd_theme` (Read the Docs theme) with custom styling [docs/source/conf.py 103-119](https://github.com/tenstorrent/riescue/blob/07cefde3/docs/source/conf.py#L103-L119):

*   Custom CSS file: `tt_theme.css`
*   TT logo: `common/images/tt_logo.svg`
*   Navigation: 4-level depth, non-collapsing
*   Logo links to: `https://docs.tenstorrent.com/`

Sources: [docs/source/conf.py 1-132](https://github.com/tenstorrent/riescue/blob/07cefde3/docs/source/conf.py#L1-L132)

## Custom Sphinx Directives

### ArgparseDirective

The `ArgparseDirective`[docs/source/conf.py 21-56](https://github.com/tenstorrent/riescue/blob/07cefde3/docs/source/conf.py#L21-L56) automatically generates documentation for command-line interfaces:

Usage syntax:

This directive:

1.   Imports the specified class [docs/source/conf.py 26-28](https://github.com/tenstorrent/riescue/blob/07cefde3/docs/source/conf.py#L26-L28)
2.   Creates an `ArgumentParser` and calls the class's `add_arguments()` method [docs/source/conf.py 31-32](https://github.com/tenstorrent/riescue/blob/07cefde3/docs/source/conf.py#L31-L32)
3.   Extracts option strings and help text [docs/source/conf.py 37-54](https://github.com/tenstorrent/riescue/blob/07cefde3/docs/source/conf.py#L37-L54)
4.   Generates definition list nodes for rendering

### MockedClassDocumenter

The `MockedClassDocumenter`[docs/source/conf.py 59-66](https://github.com/tenstorrent/riescue/blob/07cefde3/docs/source/conf.py#L59-L66) customizes the `autodoc` extension to suppress `Bases: object` lines in class documentation, providing cleaner API documentation.

Sources: [docs/source/conf.py 21-66](https://github.com/tenstorrent/riescue/blob/07cefde3/docs/source/conf.py#L21-L66)

## Local Development Workflow

The documentation system provides two modes for local development:

### Standard Local Host Mode

Behavior [docs/build.py 89-93](https://github.com/tenstorrent/riescue/blob/07cefde3/docs/build.py#L89-L93):

1.   Cleans build directory
2.   Builds documentation
3.   Starts HTTP server on `http://localhost:8888`
4.   Requires `CTRL+C` to stop

### Interactive Mode

Features [docs/build.py 124-177](https://github.com/tenstorrent/riescue/blob/07cefde3/docs/build.py#L124-L177):

*   **Reload command** (`r` or `reload`): Rebuilds documentation and restarts server
*   **Stop command** (`s` or `stop`): Stops server and exits
*   **Threading**: Server runs in daemon thread while main thread handles user input
*   **Stop event**: Coordinates shutdown between threads

The interactive mode implements a reload loop that:

1.   Starts HTTP server in background thread [docs/build.py 140-141](https://github.com/tenstorrent/riescue/blob/07cefde3/docs/build.py#L140-L141)
2.   Monitors user input for commands [docs/build.py 144-160](https://github.com/tenstorrent/riescue/blob/07cefde3/docs/build.py#L144-L160)
3.   On reload: stops server, rebuilds docs, restarts server [docs/build.py 151-172](https://github.com/tenstorrent/riescue/blob/07cefde3/docs/build.py#L151-L172)

### ReusableTCPServer

Both modes use a custom `ReusableTCPServer` class [docs/build.py 193-202](https://github.com/tenstorrent/riescue/blob/07cefde3/docs/build.py#L193-L202) that:

*   Sets `allow_reuse_address = True` to prevent "Address already in use" errors
*   Overrides `serve_forever()` in interactive mode to check `stop_event`
*   Uses 0.5-second poll interval for responsive shutdown

Sources: [docs/build.py 89-218](https://github.com/tenstorrent/riescue/blob/07cefde3/docs/build.py#L89-L218)

## Integration with CI/CD

The documentation system integrates with GitHub Actions for automated deployment:

The `--check` flag [docs/build.py 111-112](https://github.com/tenstorrent/riescue/blob/07cefde3/docs/build.py#L111-L112) enables strict mode:

*   Treats warnings as errors (`-W`)
*   Forces full rebuild (`-E`)
*   Ensures documentation quality before deployment

For detailed CI/CD workflow, see [Documentation Deployment](https://deepwiki.com/tenstorrent/riescue/5.3.2-documentation-deployment).

Sources: [docs/build.py 111-112](https://github.com/tenstorrent/riescue/blob/07cefde3/docs/build.py#L111-L112)[.github/workflows/smoke.yaml](https://github.com/tenstorrent/riescue/blob/07cefde3/.github/workflows/smoke.yaml)

## Command-Line Reference

The `DocBuilder` class provides extensive CLI options [docs/build.py 55-73](https://github.com/tenstorrent/riescue/blob/07cefde3/docs/build.py#L55-L73):

| Option | Type | Default | Description |
| --- | --- | --- | --- |
| `--clean` | flag | `False` | Clean build directory before building |
| `--check` | flag | `False` | Check for sphinx warnings as errors |
| `--source_dir` | path | `docs/source/` or `docs/tt_source/` | Source directory |
| `--build_dir` | path | `docs/_build` | Build directory |
| `--local_host` | flag | `False` | Launch local host server |
| `--interactive` / `-i` | flag | `False` | Launch interactive server with reload |
| `--port` | int | `8888` | Port for local host server |
| `--host` | str | `0.0.0.0` | Host for local host server |

### Usage Examples

**Standard build:**

**Build for CI (strict mode):**

**Local development with server:**

**Interactive development:**

**Custom output directory:**

Sources: [docs/build.py 55-73](https://github.com/tenstorrent/riescue/blob/07cefde3/docs/build.py#L55-L73)[docs/README.md 20-46](https://github.com/tenstorrent/riescue/blob/07cefde3/docs/README.md?plain=1#L20-L46)

Dismiss
Refresh this wiki

Enter email to refresh
---
project: tenstorrent.github.io
github: https://github.com/tenstorrent/tenstorrent.github.io
deepwiki: https://deepwiki.com/tenstorrent/tenstorrent.github.io/4-documentation-system
---

# Documentation System

Relevant source files
*   [.github/workflows/pages_deploy.yml](https://github.com/tenstorrent/tenstorrent.github.io/blob/eda54884/.github/workflows/pages_deploy.yml)
*   [build_docs.py](https://github.com/tenstorrent/tenstorrent.github.io/blob/eda54884/build_docs.py)
*   [core/_static/js/custom.js](https://github.com/tenstorrent/tenstorrent.github.io/blob/eda54884/core/_static/js/custom.js)
*   [core/conf.py](https://github.com/tenstorrent/tenstorrent.github.io/blob/eda54884/core/conf.py)
*   [requirements.txt](https://github.com/tenstorrent/tenstorrent.github.io/blob/eda54884/requirements.txt)
*   [syseng/conf.py](https://github.com/tenstorrent/tenstorrent.github.io/blob/eda54884/syseng/conf.py)
*   [versions.yml](https://github.com/tenstorrent/tenstorrent.github.io/blob/eda54884/versions.yml)

This page provides an overview of the documentation system used by Tenstorrent to build, maintain, and deploy its technical documentation. The system is designed to support multiple projects and versions, with a centralized build and deployment process.

## Overview

The Tenstorrent documentation system is a Sphinx-based framework that automates the building and deployment of documentation across multiple projects (core, ttnn, tt-metalium, syseng) and versions. It uses GitHub Actions to automatically build and deploy the documentation to GitHub Pages whenever changes are pushed to the `main` branch.

### System Architecture

The following diagram maps the logical build components to their corresponding code entities.

Sources: [.github/workflows/pages_deploy.yml 1-11](https://github.com/tenstorrent/tenstorrent.github.io/blob/eda54884/.github/workflows/pages_deploy.yml#L1-L11)[build_docs.py 8-15](https://github.com/tenstorrent/tenstorrent.github.io/blob/eda54884/build_docs.py#L8-L15)[build_docs.py 25-46](https://github.com/tenstorrent/tenstorrent.github.io/blob/eda54884/build_docs.py#L25-L46)[versions.yml 1-13](https://github.com/tenstorrent/tenstorrent.github.io/blob/eda54884/versions.yml#L1-L13)

## System Components

The documentation system consists of several key components that work together to generate and deploy the documentation:

### Documentation Build Process

The build process is orchestrated by `build_docs.py`, which iterates through projects defined in `versions.yml`. It handles per-project Sphinx builds, moves output to a centralized `/output` directory, and manages PDF assets. The GitHub Actions workflow in `pages_deploy.yml` provides the execution environment, installing dependencies like `pandoc` and `graphviz` before running the build.

For details, see [Documentation Build Process](https://deepwiki.com/tenstorrent/tenstorrent.github.io/4.1-documentation-build-process).

Sources: [build_docs.py 8-15](https://github.com/tenstorrent/tenstorrent.github.io/blob/eda54884/build_docs.py#L8-L15)[build_docs.py 80-97](https://github.com/tenstorrent/tenstorrent.github.io/blob/eda54884/build_docs.py#L80-L97)[.github/workflows/pages_deploy.yml 23-26](https://github.com/tenstorrent/tenstorrent.github.io/blob/eda54884/.github/workflows/pages_deploy.yml#L23-L26)

### Documentation Structure

The documentation is organized into a multi-project layout. The `core` project serves as the root portal, while other projects like `ttnn`, `tt-metalium`, and `syseng` are nested under `/project/latest/` URL paths. The system uses a shared `_templates` and `_static` directory to ensure visual consistency across all sub-projects.

For details, see [Documentation Structure](https://deepwiki.com/tenstorrent/tenstorrent.github.io/4.2-documentation-structure).

Sources: [build_docs.py 86-94](https://github.com/tenstorrent/tenstorrent.github.io/blob/eda54884/build_docs.py#L86-L94)[core/conf.py 19-26](https://github.com/tenstorrent/tenstorrent.github.io/blob/eda54884/core/conf.py#L19-L26)[syseng/conf.py 9-11](https://github.com/tenstorrent/tenstorrent.github.io/blob/eda54884/syseng/conf.py#L9-L11)

### Sphinx Configuration

Each project contains a `conf.py` file that defines its behavior. The `core/conf.py` file configures the `myst_parser` for Markdown support, handles dynamic version substitutions (e.g., `ver_kmd`, `ver_fw`) by reading version files, and manages static assets like `custom.js` and `posthog.js`.

For details, see [Sphinx Configuration](https://deepwiki.com/tenstorrent/tenstorrent.github.io/4.3-sphinx-configuration).

Sources: [core/conf.py 21-26](https://github.com/tenstorrent/tenstorrent.github.io/blob/eda54884/core/conf.py#L21-L26)[core/conf.py 40-48](https://github.com/tenstorrent/tenstorrent.github.io/blob/eda54884/core/conf.py#L40-L48)[core/conf.py 55-60](https://github.com/tenstorrent/tenstorrent.github.io/blob/eda54884/core/conf.py#L55-L60)

## Code Entity Mapping

The following diagram bridges the natural language concepts of the documentation system to the specific files and functions that implement them.

Sources: [build_docs.py 8-50](https://github.com/tenstorrent/tenstorrent.github.io/blob/eda54884/build_docs.py#L8-L50)[versions.yml 1-13](https://github.com/tenstorrent/tenstorrent.github.io/blob/eda54884/versions.yml#L1-L13)[core/conf.py 19-26](https://github.com/tenstorrent/tenstorrent.github.io/blob/eda54884/core/conf.py#L19-L26)[.github/workflows/pages_deploy.yml 1-7](https://github.com/tenstorrent/tenstorrent.github.io/blob/eda54884/.github/workflows/pages_deploy.yml#L1-L7)

## Technical Dependencies

The documentation system relies on several key dependencies defined in `requirements.txt`:

| Dependency | Version | Purpose |
| --- | --- | --- |
| `sphinx` | 8.1.3 | Core documentation generator |
| `myst-parser` | - | Markdown (MyST) support for Sphinx |
| `sphinx-rtd-theme` | - | Read the Docs visual theme |
| `sphinx-sitemap` | - | XML sitemap generation for SEO |
| `Sphinx_Substitution_Extensions` | - | Dynamic text replacement (e.g. versions) |
| `sphinx-copybutton` | - | Adds "copy" buttons to code blocks |

Sources: [requirements.txt 1-12](https://github.com/tenstorrent/tenstorrent.github.io/blob/eda54884/requirements.txt#L1-L12)[core/conf.py 21-26](https://github.com/tenstorrent/tenstorrent.github.io/blob/eda54884/core/conf.py#L21-L26)

## Conclusion

The Tenstorrent documentation system provides a flexible and automated way to build and deploy documentation for multiple projects and versions. By centralizing the build and deployment process via `build_docs.py` and GitHub Actions, it ensures consistency across all documentation while allowing each project to maintain its own configuration.

For more details on specific aspects of the documentation system, see:

*   [Documentation Build Process](https://deepwiki.com/tenstorrent/tenstorrent.github.io/4.1-documentation-build-process)
*   [Documentation Structure](https://deepwiki.com/tenstorrent/tenstorrent.github.io/4.2-documentation-structure)
*   [Sphinx Configuration](https://deepwiki.com/tenstorrent/tenstorrent.github.io/4.3-sphinx-configuration)

Dismiss
Refresh this wiki

Enter email to refresh
---
project: tt-topology
github: https://github.com/tenstorrent/tt-topology
deepwiki: https://deepwiki.com/tenstorrent/tt-topology/7-development
---

# Development

Relevant source files
*   [.github/workflows/community-issue-tagging.yml](https://github.com/tenstorrent/tt-topology/blob/f86e46c6/.github/workflows/community-issue-tagging.yml)
*   [.pre-commit-config.yaml](https://github.com/tenstorrent/tt-topology/blob/f86e46c6/.pre-commit-config.yaml)
*   [CHANGELOG.md](https://github.com/tenstorrent/tt-topology/blob/f86e46c6/CHANGELOG.md?plain=1)

This document provides technical guidance for developers working on the tt-topology codebase. It covers the development environment, code quality tools, and workflow practices used in this project. For specific contribution guidelines, see [Contributing](https://deepwiki.com/tenstorrent/tt-topology/7.1-contributing). For testing procedures, see [Testing Framework](https://deepwiki.com/tenstorrent/tt-topology/7.2-testing-framework). For version history details, see [Release History](https://deepwiki.com/tenstorrent/tt-topology/7.3-release-history).

## Development Environment

The tt-topology project uses a standardized development environment with automated code quality enforcement and continuous integration workflows. The development setup integrates with the broader Tenstorrent ecosystem while maintaining compatibility with standard Python development practices.

### Code Quality and Formatting

The project enforces code quality through automated pre-commit hooks and formatting tools. The configuration is defined in [.pre-commit-config.yaml 1-15](https://github.com/tenstorrent/tt-topology/blob/f86e46c6/.pre-commit-config.yaml#L1-L15) and includes:

*   **Black code formatter** (version 23.11.0) for consistent Python code formatting
*   **Pre-commit hooks** for automated quality checks including: 
    *   YAML validation (`check-yaml`)
    *   File ending fixes (`end-of-file-fixer`)
    *   Trailing whitespace removal (`trailing-whitespace`)
    *   TOML validation (`check-toml`)

All tools are configured to use Python 3.8 as the base language version for compatibility across the development environment.

**Development Toolchain Architecture**

Sources: [.pre-commit-config.yaml 1-15](https://github.com/tenstorrent/tt-topology/blob/f86e46c6/.pre-commit-config.yaml#L1-L15)[.github/workflows/community-issue-tagging.yml 1-13](https://github.com/tenstorrent/tt-topology/blob/f86e46c6/.github/workflows/community-issue-tagging.yml#L1-L13)

### Continuous Integration

The project integrates with GitHub Actions through centralized Tenstorrent workflows. The [.github/workflows/community-issue-tagging.yml 1-13](https://github.com/tenstorrent/tt-topology/blob/f86e46c6/.github/workflows/community-issue-tagging.yml#L1-L13) file defines automated issue and pull request labeling that triggers on:

*   Issue creation (`issues.opened`)
*   Pull request creation (`pull_request.opened`)

The workflow delegates to the central Tenstorrent GitHub Actions repository at `tenstorrent/tt-github-actions/.github/workflows/on-community-issue.yml@main`, ensuring consistent community interaction across all Tenstorrent projects.

## Release Management

The project follows semantic versioning principles as documented in [CHANGELOG.md 1-137](https://github.com/tenstorrent/tt-topology/blob/f86e46c6/CHANGELOG.md?plain=1#L1-L137) The changelog tracks all notable changes using the [Keep a Changelog](https://keepachangelog.com/en/1.0.0/) format with categorized entries:

*   **Updated**: Feature enhancements and version bumps
*   **Fixed**: Bug fixes and stability improvements
*   **Added**: New functionality

Recent development trends visible in the changelog include:

*   Regular dependency updates for `tt-tools-common` and `pyluwen`
*   Ethernet firmware compatibility improvements
*   PCI interface detection enhancements
*   Hardware detection robustness improvements

**Release Development Workflow**

Sources: [CHANGELOG.md 1-137](https://github.com/tenstorrent/tt-topology/blob/f86e46c6/CHANGELOG.md?plain=1#L1-L137)

## Development Dependencies

The development environment relies on several key external dependencies that must be maintained for compatibility:

| Component | Version | Purpose |
| --- | --- | --- |
| `tt-tools-common` | 1.4.15+ | Hardware utilities and driver compatibility |
| `pyluwen` | 0.6.3+ | Low-level hardware interface |
| `tt-kmd` | 2.0.0+ | Kernel module driver |
| `black` | 23.11.0 | Code formatting |
| `pre-commit` | 4.5.0 | Git hook management |

The changelog shows active maintenance of these dependencies, with regular version bumps to address compatibility issues and incorporate upstream improvements. Dependency management focuses particularly on maintaining compatibility with evolving Tenstorrent hardware and firmware versions.

Sources: [CHANGELOG.md 8-137](https://github.com/tenstorrent/tt-topology/blob/f86e46c6/CHANGELOG.md?plain=1#L8-L137)[.pre-commit-config.yaml 10-14](https://github.com/tenstorrent/tt-topology/blob/f86e46c6/.pre-commit-config.yaml#L10-L14)

Dismiss
Refresh this wiki

Enter email to refresh
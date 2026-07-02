---
project: tt-burnin
github: https://github.com/tenstorrent/tt-burnin
deepwiki: https://deepwiki.com/tenstorrent/tt-burnin/5-build-and-release
---

# Build and Release

Relevant source files
*   [.github/workflows/build-all.yml](https://github.com/tenstorrent/tt-burnin/blob/809f293d/.github/workflows/build-all.yml)
*   [.github/workflows/build-debian.yml](https://github.com/tenstorrent/tt-burnin/blob/809f293d/.github/workflows/build-debian.yml)
*   [debian/changelog](https://github.com/tenstorrent/tt-burnin/blob/809f293d/debian/changelog)

This document covers the automated build and release system for the tt-burnin project, including CI/CD workflows, package generation, and distribution to multiple channels. The system orchestrates the creation of Debian packages and Python wheels from source code and manages their distribution to GitHub releases, PyPI, and other repositories.

For detailed information about Debian-specific packaging, see [Debian Packaging](https://deepwiki.com/tenstorrent/tt-burnin/5.1-debian-packaging). For CI/CD workflow implementation details, see [CI/CD Workflows](https://deepwiki.com/tenstorrent/tt-burnin/5.2-cicd-workflows). For release management and versioning processes, see [Release Management](https://deepwiki.com/tenstorrent/tt-burnin/5.3-release-management).

## Build System Overview

The tt-burnin project uses a multi-stage build system that produces packages for different distribution channels. The system is designed around GitHub Actions workflows that coordinate the building of Debian packages and Python wheels.

### Build Orchestration Workflow

**Build Orchestration Pipeline**

The `build-all.yml` workflow serves as the central orchestrator that coordinates all package builds. It accepts version parameters and git references, then triggers parallel builds for different package types.

Sources: [.github/workflows/build-all.yml 1-53](https://github.com/tenstorrent/tt-burnin/blob/809f293d/.github/workflows/build-all.yml#L1-L53)

### Package Generation Matrix

The build system generates multiple package variants to support different Ubuntu versions and distribution channels:

| Package Type | Target Platform | Workflow | Output Format |
| --- | --- | --- | --- |
| Debian Package | ubuntu-22.04 | `build-debian.yml` | `.deb` file |
| Debian Package | ubuntu-24.04 | `build-debian.yml` | `.deb` file |
| Debian Package | ubuntu-latest | `build-debian.yml` | `.deb` file |
| Python Wheel | Cross-platform | `build-pypi.yml` | `.whl` file |

## Debian Package Build Process

**Debian Package Build Pipeline**

The Debian build process uses `git-buildpackage` (`gbp`) tools to generate signed `.deb` packages. Each build updates the `debian/changelog` with version information before building.

Sources: [.github/workflows/build-debian.yml 52-166](https://github.com/tenstorrent/tt-burnin/blob/809f293d/.github/workflows/build-debian.yml#L52-L166)

### Version Management in Builds

The build system accepts version parameters through workflow inputs:

`inputs:  MAJOR:    required: false    type: string  MINOR:    required: false    type: string  PATCH:    required: false    type: string  NUMBER_OF_COMMITS_SINCE_TAG:    required: false    type: string`
Version information is propagated from the orchestrator workflow to individual build workflows and used to update package metadata.

Sources: [.github/workflows/build-all.yml 11-22](https://github.com/tenstorrent/tt-burnin/blob/809f293d/.github/workflows/build-all.yml#L11-L22)[.github/workflows/build-debian.yml 12-23](https://github.com/tenstorrent/tt-burnin/blob/809f293d/.github/workflows/build-debian.yml#L12-L23)

## Package Signing and Security

The Debian build process includes GPG package signing for security verification:

**Package Signing Workflow**

The signing process uses the `crazy-max/ghaction-import-gpg` action to import the signing key from GitHub secrets, then configures the build environment to sign packages during the `gbp buildpackage` step.

Sources: [.github/workflows/build-debian.yml 66-72](https://github.com/tenstorrent/tt-burnin/blob/809f293d/.github/workflows/build-debian.yml#L66-L72)[.github/workflows/build-debian.yml 128-132](https://github.com/tenstorrent/tt-burnin/blob/809f293d/.github/workflows/build-debian.yml#L128-L132)

## Artifact Management

Build outputs are systematically organized and uploaded as GitHub Actions artifacts:

| Artifact Type | Naming Pattern | Content |
| --- | --- | --- |
| Debian Package | `{package-name}_all-{distro-identifier}` | Single `.deb` file |
| Changelog | `debian-changelog-{distro-identifier}` | `debian/changelog` file |
| Complete Artifacts | `artifacts-{distro-identifier}.zip` | All build outputs |

The artifact naming includes distribution identifiers to distinguish between builds for different Ubuntu versions.

Sources: [.github/workflows/build-debian.yml 134-165](https://github.com/tenstorrent/tt-burnin/blob/809f293d/.github/workflows/build-debian.yml#L134-L165)

## Version History Tracking

The system maintains version history in the `debian/changelog` file, which records package versions, changes, and release information:

```
tt-burnin (0.2.4) noble; urgency=medium

  [ John 'Warthog9' Hawley ]
  * Fixing build process
  * Chasing down that signing problem
  * build-debian: Fixing number of commits counter

  [ Tenstorrent Releases ]
  * pyproject.toml- updating version to 0.2.4

 -- Tenstorrent Releases <releases@tenstorrent.com>  Mon, 18 Aug 2025 23:07:23 +0000
```

The changelog is automatically updated during builds using `gbp dch` commands with version information from workflow inputs.

Sources: [debian/changelog 1-39](https://github.com/tenstorrent/tt-burnin/blob/809f293d/debian/changelog#L1-L39)[.github/workflows/build-debian.yml 118-123](https://github.com/tenstorrent/tt-burnin/blob/809f293d/.github/workflows/build-debian.yml#L118-L123)

## Build Triggers and Workflow Dispatch

The build system supports both manual and automated triggering:

*   **Manual Dispatch**: `workflow_dispatch` events allow manual triggering with custom parameters
*   **Workflow Call**: `workflow_call` events enable orchestration from other workflows
*   **Parameter Passing**: Version and git reference parameters are passed between workflows

The `build-all.yml` orchestrator accepts parameters and distributes them to child workflows using the `workflow_call` mechanism.

Sources: [.github/workflows/build-all.yml 3-10](https://github.com/tenstorrent/tt-burnin/blob/809f293d/.github/workflows/build-all.yml#L3-L10)[.github/workflows/build-debian.yml 3-11](https://github.com/tenstorrent/tt-burnin/blob/809f293d/.github/workflows/build-debian.yml#L3-L11)

Dismiss
Refresh this wiki

Enter email to refresh
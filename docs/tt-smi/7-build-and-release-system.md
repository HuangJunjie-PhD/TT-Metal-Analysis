---
project: tt-smi
github: https://github.com/tenstorrent/tt-smi
deepwiki: https://deepwiki.com/tenstorrent/tt-smi/7-build-and-release-system
---

# Build and Release System

Relevant source files
*   [.github/workflows/build-all.yml](https://github.com/tenstorrent/tt-smi/blob/a2791bfe/.github/workflows/build-all.yml)
*   [.github/workflows/build-pypi.yml](https://github.com/tenstorrent/tt-smi/blob/a2791bfe/.github/workflows/build-pypi.yml)
*   [.github/workflows/release.yml](https://github.com/tenstorrent/tt-smi/blob/a2791bfe/.github/workflows/release.yml)
*   [pyproject.toml](https://github.com/tenstorrent/tt-smi/blob/a2791bfe/pyproject.toml)
*   [tests/test_reset.py](https://github.com/tenstorrent/tt-smi/blob/a2791bfe/tests/test_reset.py)

## Purpose and Scope

This document describes the build and release infrastructure for TT-SMI, including package configuration, automated workflows for building Python wheels and Debian packages, version management, changelog generation, and distribution to PyPI and GitHub Releases. For information about development environment setup and testing, see [Development Guide](https://deepwiki.com/tenstorrent/tt-smi/8-development-guide). For Debian-specific packaging details, see [Debian Package Building](https://deepwiki.com/tenstorrent/tt-smi/7.3-debian-package-building).

* * *

## Package Configuration Overview

TT-SMI's build system is defined by `pyproject.toml`, which serves as the single source of truth for project metadata, dependencies, and build configuration following PEP 517/518 standards.

### Project Metadata

The package is configured with the following core metadata:

| Property | Value | Location |
| --- | --- | --- |
| Package Name | `tt-smi` | [pyproject.toml 2](https://github.com/tenstorrent/tt-smi/blob/a2791bfe/pyproject.toml#L2-L2) |
| Current Version | `3.1.1` | [pyproject.toml 3](https://github.com/tenstorrent/tt-smi/blob/a2791bfe/pyproject.toml#L3-L3) |
| Python Requirement | `>=3.10` | [pyproject.toml 6](https://github.com/tenstorrent/tt-smi/blob/a2791bfe/pyproject.toml#L6-L6) |
| License | Apache License 2.0 | [pyproject.toml 7](https://github.com/tenstorrent/tt-smi/blob/a2791bfe/pyproject.toml#L7-L7) |
| Entry Point | `tt-smi = "tt_smi:main"` | [pyproject.toml 53-54](https://github.com/tenstorrent/tt-smi/blob/a2791bfe/pyproject.toml#L53-L54) |

**Sources:**[pyproject.toml 1-54](https://github.com/tenstorrent/tt-smi/blob/a2791bfe/pyproject.toml#L1-L54)

### Dependency Architecture

**Dependency Categories:**

**Runtime Dependencies**[pyproject.toml 26-36](https://github.com/tenstorrent/tt-smi/blob/a2791bfe/pyproject.toml#L26-L36):

*   **Hardware Interface**: `pyluwen ~0.8.0` provides low-level device access
*   **Shared Utilities**: `tt_tools_common ~1.6.0` provides chip detection and reset operations
*   **UI Framework**: `textual >=0.59.0` and `rich >=13.7.0` power the TUI
*   **Data Management**: `pydantic >=1.2` for data validation, `distro >=1.8.0` for OS detection, `tomli` for TOML parsing
*   **Optional Logging**: `elasticsearch >=8.11.0` for telemetry export

**Development Dependencies**[pyproject.toml 38-42](https://github.com/tenstorrent/tt-smi/blob/a2791bfe/pyproject.toml#L38-L42):

*   **Code Formatting**: `black` with version pinned per Python version (23.3.0 for 3.7, 24.8.0 for 3.8, 24.10.0 for 3.9+)
*   **Git Hooks**: `pre-commit >=3.5.0`

**Test Dependencies**[pyproject.toml 44-46](https://github.com/tenstorrent/tt-smi/blob/a2791bfe/pyproject.toml#L44-L46):

*   `pytest` for the test suite

**Sources:**[pyproject.toml 26-46](https://github.com/tenstorrent/tt-smi/blob/a2791bfe/pyproject.toml#L26-L46)

### Package Data Configuration

The build system includes specific data files in the distribution:

Configuration for package data inclusion/exclusion:

*   **Included**[pyproject.toml 59-65](https://github.com/tenstorrent/tt-smi/blob/a2791bfe/pyproject.toml#L59-L65): `data/version.txt`, `*.css`, YAML configuration files
*   **Excluded**[pyproject.toml 67-72](https://github.com/tenstorrent/tt-smi/blob/a2791bfe/pyproject.toml#L67-L72): `build`, `debian`, `images` directories

**Sources:**[pyproject.toml 56-75](https://github.com/tenstorrent/tt-smi/blob/a2791bfe/pyproject.toml#L56-L75)[pyproject.toml 77-84](https://github.com/tenstorrent/tt-smi/blob/a2791bfe/pyproject.toml#L77-L84)

### Build Backend Configuration

The build system uses setuptools as the PEP 517 build backend:

This configuration is located at [pyproject.toml 77-84](https://github.com/tenstorrent/tt-smi/blob/a2791bfe/pyproject.toml#L77-L84) and enables building wheels and source distributions via `python -m build`.

**Sources:**[pyproject.toml 77-84](https://github.com/tenstorrent/tt-smi/blob/a2791bfe/pyproject.toml#L77-L84)

* * *

## Build Workflow Architecture

### Workflow Orchestration

TT-SMI uses three GitHub Actions workflows to manage builds:

**Sources:**[.github/workflows/build-all.yml 1-53](https://github.com/tenstorrent/tt-smi/blob/a2791bfe/.github/workflows/build-all.yml#L1-L53)

### Build Orchestration Job

The `build-all.yml` workflow serves as the central orchestrator that triggers both Python and Debian builds:

**Workflow Inputs**[.github/workflows/build-all.yml 6-22](https://github.com/tenstorrent/tt-smi/blob/a2791bfe/.github/workflows/build-all.yml#L6-L22):

*   `ref`: Git reference to build from (default: `${{ github.ref }}`)
*   `MAJOR`, `MINOR`, `PATCH`: Version components
*   `NUMBER_OF_COMMITS_SINCE_TAG`: Commit count for build metadata

**Job Structure**[.github/workflows/build-all.yml 24-52](https://github.com/tenstorrent/tt-smi/blob/a2791bfe/.github/workflows/build-all.yml#L24-L52):

1.   `build_all_depends`: Initialization job that logs build parameters
2.   `builddebian`: Calls the Debian build workflow with version parameters
3.   `buildpypi`: Calls the PyPI build workflow with the specified git ref

**Sources:**[.github/workflows/build-all.yml 1-53](https://github.com/tenstorrent/tt-smi/blob/a2791bfe/.github/workflows/build-all.yml#L1-L53)

### Python Wheel Build Process

The `build-pypi.yml` workflow builds platform-independent Python wheels:

**Build Steps**[.github/workflows/build-pypi.yml 17-37](https://github.com/tenstorrent/tt-smi/blob/a2791bfe/.github/workflows/build-pypi.yml#L17-L37):

1.   **Checkout**: Fetches code at specified ref with full tag history
2.   **Python Setup**: Configures Python 3.10 environment
3.   **Build**: Installs `build` package and runs `python -m build`
4.   **Upload**: Stores wheel and sdist in `release-dists` artifact

The build process uses Python 3.10 as the build environment (compatible with the `>=3.10` requirement in pyproject.toml) and produces:

*   `.whl` file (binary wheel distribution)
*   `.tar.gz` file (source distribution)

**Sources:**[.github/workflows/build-pypi.yml 1-39](https://github.com/tenstorrent/tt-smi/blob/a2791bfe/.github/workflows/build-pypi.yml#L1-L39)

* * *

## Release Workflow

The `release.yml` workflow automates the complete release process from version bumping to PyPI publication.

### Release Workflow Job Dependencies

**Sources:**[.github/workflows/release.yml 1-454](https://github.com/tenstorrent/tt-smi/blob/a2791bfe/.github/workflows/release.yml#L1-L454)

### Temporary Branch Creation

The release process begins by creating a temporary branch to isolate release changes:

**Job: `create-temp-branch`**[.github/workflows/release.yml 41-62](https://github.com/tenstorrent/tt-smi/blob/a2791bfe/.github/workflows/release.yml#L41-L62)

*   Creates branch named `rc-temp-{SHORT_SHA}-{TIMESTAMP}`
*   Fetches full repository history with tags
*   Outputs `temp_branch_ref` for downstream jobs

This isolation prevents conflicts during the multi-stage release process.

**Sources:**[.github/workflows/release.yml 41-62](https://github.com/tenstorrent/tt-smi/blob/a2791bfe/.github/workflows/release.yml#L41-L62)

### Version Bumping Process

**Job: `versionchange`**[.github/workflows/release.yml 64-160](https://github.com/tenstorrent/tt-smi/blob/a2791bfe/.github/workflows/release.yml#L64-L160)

**Key Operations:**

1.   **Environment**: Runs on Ubuntu 22.04 (oldest supported)
2.   **Current Version**: Extracted using `toml-cli`[.github/workflows/release.yml 101-107](https://github.com/tenstorrent/tt-smi/blob/a2791bfe/.github/workflows/release.yml#L101-L107)
3.   **Version Bump**: Uses `bump2version` CLI [.github/workflows/release.yml 116-121](https://github.com/tenstorrent/tt-smi/blob/a2791bfe/.github/workflows/release.yml#L116-L121)
4.   **New Version**: Re-read from `pyproject.toml`[.github/workflows/release.yml 122-131](https://github.com/tenstorrent/tt-smi/blob/a2791bfe/.github/workflows/release.yml#L122-L131)
5.   **Version Parsing**: Extracts semver components using `release-kit/semver@v2`[.github/workflows/release.yml 132-136](https://github.com/tenstorrent/tt-smi/blob/a2791bfe/.github/workflows/release.yml#L132-L136)
6.   **Commit Count**: Calculates commits since last tag [.github/workflows/release.yml 137-142](https://github.com/tenstorrent/tt-smi/blob/a2791bfe/.github/workflows/release.yml#L137-L142)
7.   **Git Push**: Commits updated `pyproject.toml`[.github/workflows/release.yml 144-149](https://github.com/tenstorrent/tt-smi/blob/a2791bfe/.github/workflows/release.yml#L144-L149)

**Job Outputs**[.github/workflows/release.yml 73-84](https://github.com/tenstorrent/tt-smi/blob/a2791bfe/.github/workflows/release.yml#L73-L84):

*   `git_hash`: New commit SHA
*   `package_name`: From `project.name` in pyproject.toml
*   `package_version_new`: Updated version string
*   `version_major`, `version_minor`, `version_patch`: Semver components
*   `number_of_commits_since_tag`: For build metadata

**Sources:**[.github/workflows/release.yml 64-160](https://github.com/tenstorrent/tt-smi/blob/a2791bfe/.github/workflows/release.yml#L64-L160)

### Changelog Generation

**Job: `changelogs`**[.github/workflows/release.yml 165-236](https://github.com/tenstorrent/tt-smi/blob/a2791bfe/.github/workflows/release.yml#L165-L236)

The changelog generation uses `gbp dch` (git-buildpackage debian changelog tool):

**Process**[.github/workflows/release.yml 204-213](https://github.com/tenstorrent/tt-smi/blob/a2791bfe/.github/workflows/release.yml#L204-L213):

1.   Installs `git-buildpackage` package
2.   Runs `gbp dch` with version from versionchange job
3.   Generates Debian-format changelog
4.   Commits and pushes changes
5.   Outputs new `git_hash` for downstream jobs

**Configuration:**

*   Environment: `EMAIL: releases@tenstorrent.com`, `NAME: Tenstorrent Releases`
*   Debian branch: Points to temporary branch
*   Release flag: `-R` marks as released
*   New version: `-N {version}` sets version explicitly

**Sources:**[.github/workflows/release.yml 165-236](https://github.com/tenstorrent/tt-smi/blob/a2791bfe/.github/workflows/release.yml#L165-L236)

### Build Execution

**Job: `build_all_depends`**[.github/workflows/release.yml 241-253](https://github.com/tenstorrent/tt-smi/blob/a2791bfe/.github/workflows/release.yml#L241-L253)

Triggers the `build-all.yml` workflow with:

*   `ref`: Temporary branch reference
*   `MAJOR`, `MINOR`, `PATCH`: Version components
*   `NUMBER_OF_COMMITS_SINCE_TAG`: Commit count

This job coordinates building both Python wheels and Debian packages on the updated release branch.

**Sources:**[.github/workflows/release.yml 241-253](https://github.com/tenstorrent/tt-smi/blob/a2791bfe/.github/workflows/release.yml#L241-L253)

### Git Tag Creation

**Job: `tagrelease`**[.github/workflows/release.yml 257-283](https://github.com/tenstorrent/tt-smi/blob/a2791bfe/.github/workflows/release.yml#L257-L283)

Creates the version tag after successful builds:

The tag is created on the commit from the `changelogs` job (after all version and changelog updates are committed).

**Tag Format:**`v{MAJOR}.{MINOR}.{PATCH}` (e.g., `v3.1.1`)

**Sources:**[.github/workflows/release.yml 257-283](https://github.com/tenstorrent/tt-smi/blob/a2791bfe/.github/workflows/release.yml#L257-L283)

### GitHub Release Creation

**Job: `generate-release`**[.github/workflows/release.yml 288-357](https://github.com/tenstorrent/tt-smi/blob/a2791bfe/.github/workflows/release.yml#L288-L357)

Creates the GitHub Release with artifacts:

**Changelog Generation**[.github/workflows/release.yml 305-321](https://github.com/tenstorrent/tt-smi/blob/a2791bfe/.github/workflows/release.yml#L305-L321): Uses `mikepenz/release-changelog-builder-action@v5` in HYBRID mode to include both PRs and direct commits.

**Artifact Attachment**[.github/workflows/release.yml 323-357](https://github.com/tenstorrent/tt-smi/blob/a2791bfe/.github/workflows/release.yml#L323-L357):

1.   Downloads all build artifacts
2.   Renames Debian packages to prevent name collisions
3.   Attaches files matching patterns: 
    *   `${{ github.workspace }}/${{ env.project_name }}*/*.deb`
    *   `${{ github.workspace }}/[!artifacts-]*/*.deb`
    *   `${{ github.workspace }}/*/*.whl`

**Release Properties:**

*   Tag: `v{version}`
*   Draft: `false`
*   Prerelease: `false`

**Sources:**[.github/workflows/release.yml 288-357](https://github.com/tenstorrent/tt-smi/blob/a2791bfe/.github/workflows/release.yml#L288-L357)

### Branch Merge-back

**Job: `mergeback`**[.github/workflows/release.yml 362-386](https://github.com/tenstorrent/tt-smi/blob/a2791bfe/.github/workflows/release.yml#L362-L386)

After successful release:

1.   Checks out original branch
2.   Rebases from temporary branch
3.   Pushes to main repository
4.   Deletes temporary branch

This ensures all release changes (version bump, changelog) are merged back to the main branch.

**Sources:**[.github/workflows/release.yml 362-386](https://github.com/tenstorrent/tt-smi/blob/a2791bfe/.github/workflows/release.yml#L362-L386)

* * *

## Distribution Channels

### TestPyPI Publication

**Job: `publish-to-testpypi`**[.github/workflows/release.yml 391-419](https://github.com/tenstorrent/tt-smi/blob/a2791bfe/.github/workflows/release.yml#L391-L419)

Publishes to TestPyPI for validation before production release:

**Configuration:**

*   Environment: `testpypi` with appropriate credentials
*   URL: `https://test.pypi.org/p/tt-smi`
*   Uses OIDC trusted publishing (no manual token required)
*   Downloads wheel artifacts with pattern `tt-smi*.whl`

**Sources:**[.github/workflows/release.yml 391-419](https://github.com/tenstorrent/tt-smi/blob/a2791bfe/.github/workflows/release.yml#L391-L419)

### PyPI Publication

**Job: `publish-to-pypi`**[.github/workflows/release.yml 424-452](https://github.com/tenstorrent/tt-smi/blob/a2791bfe/.github/workflows/release.yml#L424-L452)

Publishes to production PyPI after TestPyPI validation:

**Dependencies:**

*   Requires successful `publish-to-testpypi` job
*   Requires successful `generate-release` job
*   Requires successful `build_all_depends` job

**Configuration:**

*   Environment: `pypi` with production credentials
*   URL: `https://pypi.org/p/tt-smi`
*   Uses OIDC trusted publishing
*   Verbose logging enabled

**Sources:**[.github/workflows/release.yml 424-452](https://github.com/tenstorrent/tt-smi/blob/a2791bfe/.github/workflows/release.yml#L424-L452)

### Distribution Flow Summary

| Stage | Repository | Purpose | Dependencies |
| --- | --- | --- | --- |
| 1. Build | GitHub Actions | Create artifacts | None |
| 2. TestPyPI | test.pypi.org | Validation | Build + Release |
| 3. PyPI | pypi.org | Production | TestPyPI |
| 4. GitHub Release | github.com/releases | Artifact hosting | Build + Tag |
| 5. Debian Repo | (External) | Debian packages | Build |

**Sources:**[.github/workflows/release.yml 391-452](https://github.com/tenstorrent/tt-smi/blob/a2791bfe/.github/workflows/release.yml#L391-L452)

* * *

## Version Management System

### Semantic Versioning

TT-SMI follows semantic versioning (MAJOR.MINOR.PATCH):

| Component | Trigger | Example |
| --- | --- | --- |
| MAJOR | Breaking changes | 2.x.x → 3.0.0 |
| MINOR | New features | 3.0.x → 3.1.0 |
| PATCH | Bug fixes | 3.1.0 → 3.1.1 |

**Current Version:**`3.1.1`[pyproject.toml 3](https://github.com/tenstorrent/tt-smi/blob/a2791bfe/pyproject.toml#L3-L3)

### bump2version Tool

The version bumping process uses the `bump2version` tool:

This tool:

1.   Parses the current version from `pyproject.toml`
2.   Increments the specified component
3.   Writes the new version back to `pyproject.toml`
4.   Does NOT automatically commit (handled separately in the workflow)

**Sources:**[.github/workflows/release.yml 116-121](https://github.com/tenstorrent/tt-smi/blob/a2791bfe/.github/workflows/release.yml#L116-L121)

### Version Tracking

The workflow tracks version information through multiple mechanisms:

**Version Sources:**

*   **Source of Truth**: `project.version` in `pyproject.toml`[pyproject.toml 3](https://github.com/tenstorrent/tt-smi/blob/a2791bfe/pyproject.toml#L3-L3)
*   **Git Tags**: Created as `v{version}`[.github/workflows/release.yml 281](https://github.com/tenstorrent/tt-smi/blob/a2791bfe/.github/workflows/release.yml#L281-L281)
*   **Package Data**: `data/version.txt` included in wheel [pyproject.toml 61](https://github.com/tenstorrent/tt-smi/blob/a2791bfe/pyproject.toml#L61-L61)
*   **Changelog**: Version recorded in Debian changelog format

**Sources:**[pyproject.toml 1-10](https://github.com/tenstorrent/tt-smi/blob/a2791bfe/pyproject.toml#L1-L10)[.github/workflows/release.yml 132-136](https://github.com/tenstorrent/tt-smi/blob/a2791bfe/.github/workflows/release.yml#L132-L136)

* * *

## Testing and Quality Assurance

### Reset Testing Framework

The test suite includes critical reset operation tests to validate hardware functionality:

**Test Configuration**[tests/test_reset.py 11](https://github.com/tenstorrent/tt-smi/blob/a2791bfe/tests/test_reset.py#L11-L11):

*   `NUM_RESETS_STRESS_TEST = 10`: Number of iterations for stress tests

**Fixtures**[tests/test_reset.py 13-31](https://github.com/tenstorrent/tt-smi/blob/a2791bfe/tests/test_reset.py#L13-L31):

1.   `devices`: Returns list of PCI indices via `pci_scan()`
2.   `is_galaxy_6u`: Detects Galaxy 6U systems (32 devices, all Galaxy board types)

**Test Cases:**

1.   **`test_pci_reset_all_devices`**[tests/test_reset.py 34-46](https://github.com/tenstorrent/tt-smi/blob/a2791bfe/tests/test_reset.py#L34-L46): Tests standard PCI reset, skipped on Galaxy
2.   **`test_pci_reset_all_devices_stress`**[tests/test_reset.py 49-62](https://github.com/tenstorrent/tt-smi/blob/a2791bfe/tests/test_reset.py#L49-L62): Stress test with 10 resets
3.   **`test_glx_reset`**[tests/test_reset.py 65-82](https://github.com/tenstorrent/tt-smi/blob/a2791bfe/tests/test_reset.py#L65-L82): Tests Galaxy reset, skipped on non-Galaxy
4.   **`test_glx_reset_stress`**[tests/test_reset.py 85-103](https://github.com/tenstorrent/tt-smi/blob/a2791bfe/tests/test_reset.py#L85-L103): Galaxy stress test with 10 resets

**Validation:** All tests verify that device count remains consistent before and after reset operations.

**Sources:**[tests/test_reset.py 1-104](https://github.com/tenstorrent/tt-smi/blob/a2791bfe/tests/test_reset.py#L1-L104)

* * *

## Build System Integration Points

### Entry Point Configuration

The package defines a console script entry point:

This creates the `tt-smi` executable that calls the `main()` function in the `tt_smi` module.

**Sources:**[pyproject.toml 53-54](https://github.com/tenstorrent/tt-smi/blob/a2791bfe/pyproject.toml#L53-L54)

### Dependency Coordination

The build system ensures version compatibility between related Tenstorrent packages:

The `~=` version specifier (compatible release) ensures:

*   `pyluwen ~0.8.0` accepts `>=0.8.0, <0.9.0`
*   `tt_tools_common ~1.6.0` accepts `>=1.6.0, <1.7.0`

This allows patch updates while preventing breaking changes.

**Sources:**[pyproject.toml 30-31](https://github.com/tenstorrent/tt-smi/blob/a2791bfe/pyproject.toml#L30-L31)

### Package URLs and Metadata

The package configuration includes project URLs for reference:

| URL Type | Location |
| --- | --- |
| Homepage | [http://tenstorrent.com](http://tenstorrent.com/) |
| Bug Reports | [https://github.com/tenstorrent/tt-smi/issues](https://github.com/tenstorrent/tt-smi/issues) |
| Source | [https://github.com/tenstorrent/tt-smi](https://github.com/tenstorrent/tt-smi) |

**Sources:**[pyproject.toml 48-51](https://github.com/tenstorrent/tt-smi/blob/a2791bfe/pyproject.toml#L48-L51)

Dismiss
Refresh this wiki

Enter email to refresh
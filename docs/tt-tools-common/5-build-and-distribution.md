---
project: tt-tools-common
github: https://github.com/tenstorrent/tt-tools-common
deepwiki: https://deepwiki.com/tenstorrent/tt-tools-common/5-build-and-distribution
---

# Build and Distribution

Relevant source files
*   [.github/workflows/build-all.yml](https://github.com/tenstorrent/tt-tools-common/blob/892885f4/.github/workflows/build-all.yml)
*   [.github/workflows/build-debian.yml](https://github.com/tenstorrent/tt-tools-common/blob/892885f4/.github/workflows/build-debian.yml)
*   [.github/workflows/release.yml](https://github.com/tenstorrent/tt-tools-common/blob/892885f4/.github/workflows/release.yml)
*   [debian/changelog](https://github.com/tenstorrent/tt-tools-common/blob/892885f4/debian/changelog)
*   [setup.py](https://github.com/tenstorrent/tt-tools-common/blob/892885f4/setup.py)

## Purpose and Scope

This page provides an overview of the build and distribution infrastructure for tt-tools-common. It explains the dual-format packaging strategy (Debian `.deb` and Python `.whl`), version management approach, and the automated release pipeline that orchestrates building, testing, and publishing to multiple distribution channels.

For detailed information about specific aspects of the build system, see:

*   [Debian Package Build System](https://deepwiki.com/tenstorrent/tt-tools-common/5.1-debian-package-build-system) - Multi-distro `.deb` builds, GPG signing, and packaging
*   [Python Package Build](https://deepwiki.com/tenstorrent/tt-tools-common/5.2-python-package-build) - Wheel creation and setuptools configuration
*   [Automated Release Workflow](https://deepwiki.com/tenstorrent/tt-tools-common/5.3-automated-release-workflow) - Version bumping, changelog generation, and release orchestration
*   [Publishing to PyPI and TestPyPI](https://deepwiki.com/tenstorrent/tt-tools-common/5.4-publishing-to-pypi-and-testpypi) - Package upload and OIDC trusted publishing

## Distribution Strategy

tt-tools-common provides two distribution formats to support different installation workflows:

| Format | Target Users | Installation Method | Build Output |
| --- | --- | --- | --- |
| **Debian Package** | System administrators, production deployments | `apt install tt-tools-common` | `.deb` files for Ubuntu 22.04, 24.04, latest |
| **Python Wheel** | Developers, pip users | `pip install tt-tools-common` | `.whl` files for PyPI |

Both formats are built from the same source tree and share a single version number defined in [`pyproject.toml`](https://github.com/tenstorrent/tt-tools-common/blob/892885f4/%60pyproject.toml%60)(). The package name `tt-tools-common` is used consistently across both distribution channels.

**Distribution Diagram: Package Flow**

Sources: [.github/workflows/release.yml](https://github.com/tenstorrent/tt-tools-common/blob/892885f4/.github/workflows/release.yml)(), [pyproject.toml](https://github.com/tenstorrent/tt-tools-common/blob/892885f4/pyproject.toml)()

## Version Management

Version information is centralized in [`pyproject.toml`](https://github.com/tenstorrent/tt-tools-common/blob/892885f4/%60pyproject.toml%60)() under the `project.version` key. The version follows semantic versioning (MAJOR.MINOR.PATCH) and serves as the single source of truth for both Debian and Python packages.

### Version Extraction Mechanism

The [`setup.py`](https://github.com/tenstorrent/tt-tools-common/blob/892885f4/%60setup.py%60)() file provides backward compatibility for older Ubuntu packaging tools that cannot parse `pyproject.toml` directly. It uses the `tomli` library to extract the version:

The release workflow uses `toml-cli` to read and `bump2version` to increment version numbers:

Sources: [setup.py](https://github.com/tenstorrent/tt-tools-common/blob/892885f4/setup.py)(), [.github/workflows/release.yml 95-121](https://github.com/tenstorrent/tt-tools-common/blob/892885f4/.github/workflows/release.yml#L95-L121)

### Changelog Synchronization

The Debian changelog at [`debian/changelog`](https://github.com/tenstorrent/tt-tools-common/blob/892885f4/%60debian/changelog%60)() is automatically synchronized with git history using `gbp dch` (git-buildpackage debian changelog):

This ensures version numbers remain consistent between `pyproject.toml` and `debian/changelog`.

Sources: [debian/changelog](https://github.com/tenstorrent/tt-tools-common/blob/892885f4/debian/changelog)(), [.github/workflows/release.yml 203-216](https://github.com/tenstorrent/tt-tools-common/blob/892885f4/.github/workflows/release.yml#L203-L216)[.github/workflows/build-debian.yml 119-127](https://github.com/tenstorrent/tt-tools-common/blob/892885f4/.github/workflows/build-debian.yml#L119-L127)

## Build System Architecture

**Build System Component Diagram**

Sources: [.github/workflows/release.yml](https://github.com/tenstorrent/tt-tools-common/blob/892885f4/.github/workflows/release.yml)(), [.github/workflows/build-all.yml](https://github.com/tenstorrent/tt-tools-common/blob/892885f4/.github/workflows/build-all.yml)()

### Temporary Branch Isolation Pattern

The release workflow uses a temporary branch strategy to isolate version modifications before merging to `main`. This prevents race conditions when multiple release steps modify files:

**Temporary Branch Lifecycle**

The temporary branch name format is: `rc-temp-{SHORT_SHA}-{YYYY.MM.DD-HH.MM.SS}`

Sources: [.github/workflows/release.yml 41-62](https://github.com/tenstorrent/tt-tools-common/blob/892885f4/.github/workflows/release.yml#L41-L62)

## GitHub Actions Workflows

### Core Workflows

| Workflow | File | Purpose | Trigger |
| --- | --- | --- | --- |
| **Create Release** | `release.yml` | Orchestrates entire release process | Manual `workflow_dispatch` |
| **Build All** | `build-all.yml` | Parallel trigger for Debian + Python builds | Called by `release.yml` |
| **Build Ubuntu Packages** | `build-debian.yml` | Matrix build for 3 Ubuntu versions | Called by `build-all.yml` |
| **Build Python Package** | `build-pypi.yml` (referenced) | Creates `.whl` for PyPI | Called by `build-all.yml` |

### Workflow Data Flow

Sources: [.github/workflows/release.yml](https://github.com/tenstorrent/tt-tools-common/blob/892885f4/.github/workflows/release.yml)()

### Build Matrix Strategy

The Debian build workflow uses a matrix strategy with `fail-fast: false` to build packages for multiple Ubuntu versions independently:

This ensures that a build failure on one Ubuntu version does not block builds for other versions. Each matrix job produces a uniquely-named artifact that includes the distro identifier.

Sources: [.github/workflows/build-debian.yml 26-34](https://github.com/tenstorrent/tt-tools-common/blob/892885f4/.github/workflows/build-debian.yml#L26-L34)

## Build Dependencies

### Debian Build Dependencies

The Debian build process requires the following system packages:

| Package | Purpose |
| --- | --- |
| `build-essential` | C/C++ compiler toolchain |
| `debhelper` | Debian package building helpers |
| `dh-python` | Python-specific Debian helpers |
| `dh-sequence-python3` | Python3 build sequence |
| `git-buildpackage` | `gbp` tools for changelog generation |
| `gnupg` | GPG signing for package authentication |
| `pybuild-plugin-pyproject` | PEP 517/518 build system support |
| `python3-tomli` | TOML parsing for `pyproject.toml` |

These are installed via `apt` at [.github/workflows/build-debian.yml 53-66](https://github.com/tenstorrent/tt-tools-common/blob/892885f4/.github/workflows/build-debian.yml#L53-L66)

### Python Build Dependencies

Python builds require:

*   `setuptools` - Package building (referenced in `pyproject.toml`)
*   `tomli` - TOML parsing (for `setup.py` compatibility)
*   `bump2version` - Version incrementing
*   `toml-cli` - Command-line TOML manipulation

Sources: [.github/workflows/build-debian.yml 53-66](https://github.com/tenstorrent/tt-tools-common/blob/892885f4/.github/workflows/build-debian.yml#L53-L66)[.github/workflows/release.yml 95-100](https://github.com/tenstorrent/tt-tools-common/blob/892885f4/.github/workflows/release.yml#L95-L100)[setup.py 1-21](https://github.com/tenstorrent/tt-tools-common/blob/892885f4/setup.py#L1-L21)

## Artifact Management

### Artifact Naming Conventions

Debian packages use distro-specific naming to prevent collisions:

This renaming occurs at [.github/workflows/release.yml 328-344](https://github.com/tenstorrent/tt-tools-common/blob/892885f4/.github/workflows/release.yml#L328-L344) before uploading to GitHub Releases.

### Artifact Collection

The release workflow collects artifacts from multiple sources:

This pattern captures:

*   `.deb` files from all distro-specific artifact directories
*   `.whl` files from Python build artifacts
*   Associated `.changes` files for Debian package signatures

Sources: [.github/workflows/release.yml 346-357](https://github.com/tenstorrent/tt-tools-common/blob/892885f4/.github/workflows/release.yml#L346-L357)

### Artifact Upload Destinations

| Artifact Type | Immediate Destination | Final Destination |
| --- | --- | --- |
| `.deb` files | GitHub Actions artifacts | GitHub Releases (tagged) |
| `.whl` files | GitHub Actions artifacts | TestPyPI → PyPI |
| `.changes` files | GitHub Actions artifacts | GitHub Releases |
| `debian/changelog` | GitHub Actions artifacts | Git repository (committed) |

## GPG Signing

Debian packages are signed using GPG keys stored in GitHub Secrets. The `crazy-max/ghaction-import-gpg` action imports the private key at [.github/workflows/build-debian.yml 67-71](https://github.com/tenstorrent/tt-tools-common/blob/892885f4/.github/workflows/build-debian.yml#L67-L71):

The key fingerprint is passed to `gbp buildpackage` via the `DEBSIGN_KEYID` environment variable at [.github/workflows/build-debian.yml 128-133](https://github.com/tenstorrent/tt-tools-common/blob/892885f4/.github/workflows/build-debian.yml#L128-L133)

Sources: [.github/workflows/build-debian.yml 67-71](https://github.com/tenstorrent/tt-tools-common/blob/892885f4/.github/workflows/build-debian.yml#L67-L71)[.github/workflows/build-debian.yml 128-133](https://github.com/tenstorrent/tt-tools-common/blob/892885f4/.github/workflows/build-debian.yml#L128-L133)

## Environment Variables and Outputs

### Release Workflow Outputs

The `versionchange` job produces outputs consumed by downstream jobs:

| Output Variable | Purpose | Example Value |
| --- | --- | --- |
| `git_hash` | Commit SHA after version bump | `abc123def456...` |
| `package_name` | Project name from pyproject.toml | `tt-tools-common` |
| `package_version_new` | New version after bump | `1.6.0` |
| `version_major` | Major version component | `1` |
| `version_minor` | Minor version component | `6` |
| `version_patch` | Patch version component | `0` |
| `number_of_commits_since_tag` | Commits since last tag | `5` |

These outputs enable data flow between independent jobs without file system sharing.

Sources: [.github/workflows/release.yml 73-84](https://github.com/tenstorrent/tt-tools-common/blob/892885f4/.github/workflows/release.yml#L73-L84)

### Environment Variable Propagation

Version components are propagated through nested workflow calls:

This pattern propagates from `release.yml` → `build-all.yml` → `build-debian.yml`.

Sources: [.github/workflows/release.yml 241-253](https://github.com/tenstorrent/tt-tools-common/blob/892885f4/.github/workflows/release.yml#L241-L253)[.github/workflows/build-all.yml 34-45](https://github.com/tenstorrent/tt-tools-common/blob/892885f4/.github/workflows/build-all.yml#L34-L45)

## Release Creation

GitHub Releases are created by the `generate-release` job using the `softprops/action-gh-release` action. The release includes:

1.   **Auto-generated changelog** from git history using `mikepenz/release-changelog-builder-action`
2.   **All build artifacts** (`.deb` and `.whl` files)
3.   **Git tag** in format `vX.Y.Z`

The changelog builder uses hybrid mode to capture both PRs and direct commits:

Sources: [.github/workflows/release.yml 288-357](https://github.com/tenstorrent/tt-tools-common/blob/892885f4/.github/workflows/release.yml#L288-L357)

## Compatibility Considerations

### Python Version Support

The minimum Python version is 3.10, enforced in:

*   [`pyproject.toml`](https://github.com/tenstorrent/tt-tools-common/blob/892885f4/%60pyproject.toml%60)() via `requires-python = ">=3.10"`
*   [`setup.py 19](https://github.com/tenstorrent/tt-tools-common/blob/892885f4/%60setup.py#L19-L19)() via `python_requires=">=3.10"`

This requirement was added at version 1.4.31 as noted in [`debian/changelog 60](https://github.com/tenstorrent/tt-tools-common/blob/892885f4/%60debian/changelog#L60-L60)().

### Ubuntu Version Support

The matrix build targets three Ubuntu versions:

*   **ubuntu-22.04** (Jammy Jellyfish) - LTS, older packaging tools
*   **ubuntu-24.04** (Noble Numbat) - LTS, current recommendation
*   **ubuntu-latest** - Rolling latest for future compatibility

The `setup.py` compatibility shim exists specifically for Ubuntu 22.04, which ships with older versions of setuptools that cannot parse modern `pyproject.toml` files directly.

Sources: [setup.py 1-21](https://github.com/tenstorrent/tt-tools-common/blob/892885f4/setup.py#L1-L21)[debian/changelog 60](https://github.com/tenstorrent/tt-tools-common/blob/892885f4/debian/changelog#L60-L60)[.github/workflows/build-debian.yml 31-34](https://github.com/tenstorrent/tt-tools-common/blob/892885f4/.github/workflows/build-debian.yml#L31-L34)

Dismiss
Refresh this wiki

Enter email to refresh
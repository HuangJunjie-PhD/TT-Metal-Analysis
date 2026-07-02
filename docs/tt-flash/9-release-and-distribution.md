---
project: tt-flash
github: https://github.com/tenstorrent/tt-flash
deepwiki: https://deepwiki.com/tenstorrent/tt-flash/9-release-and-distribution
---

# Release and Distribution

Relevant source files
*   [.github/workflows/build-pypi.yml](https://github.com/tenstorrent/tt-flash/blob/60c06032/.github/workflows/build-pypi.yml)
*   [.github/workflows/release.yml](https://github.com/tenstorrent/tt-flash/blob/60c06032/.github/workflows/release.yml)
*   [debian/changelog](https://github.com/tenstorrent/tt-flash/blob/60c06032/debian/changelog)

## Purpose and Scope

This document describes the release engineering process for tt-flash, including version management, automated build pipelines, and distribution to multiple channels (PyPI and Debian repositories). The release process is fully automated through GitHub Actions workflows that handle version bumping, changelog generation, artifact building, tagging, and publication.

For information about building from source during development, see [Building from Source](https://deepwiki.com/tenstorrent/tt-flash/8.1-building-from-source). For CI/CD automation workflows, see [CI/CD Automation](https://deepwiki.com/tenstorrent/tt-flash/10-cicd-automation).

* * *

## Release Overview

The tt-flash project uses a dual-track distribution strategy: **Python wheels** distributed via PyPI and **Debian packages** for Ubuntu systems. The release process is orchestrated by [.github/workflows/release.yml 1-454](https://github.com/tenstorrent/tt-flash/blob/60c06032/.github/workflows/release.yml#L1-L454) which coordinates version updates, changelog generation, parallel artifact builds, Git tagging, and sequential publication to distribution channels.

**Sources:**[.github/workflows/release.yml 1-454](https://github.com/tenstorrent/tt-flash/blob/60c06032/.github/workflows/release.yml#L1-L454)

* * *

## Version Management

### Version Storage

The authoritative version is stored in `pyproject.toml` under the `project.version` field. The version follows semantic versioning (MAJOR.MINOR.PATCH).

`[project]name = "tt-flash"version = "3.5.0"`
### Version Bumping

The `versionchange` job in [.github/workflows/release.yml 64-161](https://github.com/tenstorrent/tt-flash/blob/60c06032/.github/workflows/release.yml#L64-L161) performs automated version increments using the `bump2version` tool:

| Bump Type | Example Transition | Use Case |
| --- | --- | --- |
| `patch` | 3.5.0 → 3.5.1 | Bug fixes, minor updates |
| `minor` | 3.5.0 → 3.6.0 | New features, backward compatible |
| `major` | 3.5.0 → 4.0.0 | Breaking changes |

The workflow extracts the current version using `toml-cli`, applies the bump, and commits the change:

`# Extract current versiontoml get project.version --toml-path pyproject.toml # Bump versionbump2version --current-version $OLD_VERSION $BUMP_TYPE pyproject.toml # Extract new versiontoml get project.version --toml-path pyproject.toml`
**Sources:**[.github/workflows/release.yml 95-131](https://github.com/tenstorrent/tt-flash/blob/60c06032/.github/workflows/release.yml#L95-L131)

### Version Format Parsing

The workflow parses version components using the `release-kit/semver@v2` action [.github/workflows/release.yml 132-136](https://github.com/tenstorrent/tt-flash/blob/60c06032/.github/workflows/release.yml#L132-L136):

`outputs:  version_major: ${{ steps.version.outputs.major }}  version_minor: ${{ steps.version.outputs.minor }}  version_patch: ${{ steps.version.outputs.patch }}  version_prerelease: ${{ steps.version.outputs.prerelease }}  version_build: ${{ steps.version.outputs.build }}  version_full: ${{ steps.version.outputs.full }}`
These outputs are propagated to downstream jobs for changelog generation and artifact naming.

**Sources:**[.github/workflows/release.yml 64-161](https://github.com/tenstorrent/tt-flash/blob/60c06032/.github/workflows/release.yml#L64-L161)

* * *

## Automated Release Workflow

### Workflow Structure

The release workflow consists of eight sequential and parallel jobs:

**Sources:**[.github/workflows/release.yml 1-454](https://github.com/tenstorrent/tt-flash/blob/60c06032/.github/workflows/release.yml#L1-L454)

### Temporary Branch Strategy

The workflow creates a temporary release candidate branch using [.github/workflows/release.yml 41-62](https://github.com/tenstorrent/tt-flash/blob/60c06032/.github/workflows/release.yml#L41-L62):

`# Branch naming patternrc-temp-${GIT_SHORT_SHA}-${TIMESTAMP}# Example: rc-temp-a3f5c2b-2025.01.15-14.23.45`
This branch isolates release commits (version bumps, changelog updates) from the main branch until all builds complete successfully. The branch is automatically merged back and deleted in [.github/workflows/release.yml 362-386](https://github.com/tenstorrent/tt-flash/blob/60c06032/.github/workflows/release.yml#L362-L386)

**Sources:**[.github/workflows/release.yml 41-62](https://github.com/tenstorrent/tt-flash/blob/60c06032/.github/workflows/release.yml#L41-L62)[.github/workflows/release.yml 362-386](https://github.com/tenstorrent/tt-flash/blob/60c06032/.github/workflows/release.yml#L362-L386)

### Job Outputs and Data Flow

The workflow uses job outputs to propagate information between stages:

| Job | Key Outputs | Purpose |
| --- | --- | --- |
| `create-temp-branch` | `temp_branch_ref` | Branch name for downstream checkouts |
| `versionchange` | `package_version_new`, `git_hash` | New version string, commit SHA |
| `changelogs` | `git_hash` | Updated commit SHA after changelog |
| `build_all_depends` | Artifacts | Debian/Python packages |

**Sources:**[.github/workflows/release.yml 42-85](https://github.com/tenstorrent/tt-flash/blob/60c06032/.github/workflows/release.yml#L42-L85)[.github/workflows/release.yml 174-176](https://github.com/tenstorrent/tt-flash/blob/60c06032/.github/workflows/release.yml#L174-L176)

* * *

## Changelog Generation

### Debian Changelog Format

The workflow generates a Debian-standard changelog using `git-buildpackage` tools [.github/workflows/release.yml 165-237](https://github.com/tenstorrent/tt-flash/blob/60c06032/.github/workflows/release.yml#L165-L237):

`gbp dch \  --debian-branch $TEMP_BRANCH \  -R \  -N ${MAJOR}.${MINOR}.${PATCH} \  --spawn-editor=never`
The resulting [debian/changelog 1-171](https://github.com/tenstorrent/tt-flash/blob/60c06032/debian/changelog#L1-L171) follows Debian conventions:

```
tt-flash (3.5.0) noble; urgency=medium

  [ Zakhary Kaplan ]
  * build(deps): bump pyluwen to 0.8.0, tt-tools-common to 1.5
  * ci(release): specify version bump type

  [ Tenstorrent Releases ]
  * pyproject.toml- updating version to 3.5.0

 -- Tenstorrent Releases <releases@tenstorrent.com>  Fri, 19 Dec 2025 20:39:34 +0000
```

**Sources:**[.github/workflows/release.yml 203-213](https://github.com/tenstorrent/tt-flash/blob/60c06032/.github/workflows/release.yml#L203-L213)[debian/changelog 1-171](https://github.com/tenstorrent/tt-flash/blob/60c06032/debian/changelog#L1-L171)

### Changelog Entry Components

| Component | Description | Example |
| --- | --- | --- |
| Package name | Project identifier | `tt-flash` |
| Version | Release version | `(3.5.0)` |
| Distribution | Ubuntu codename | `noble` |
| Urgency | Update priority | `medium` |
| Changes | Git commit messages | `* build(deps): bump pyluwen` |
| Author | Release bot identity | `Tenstorrent Releases` |
| Timestamp | RFC 2822 date | `Fri, 19 Dec 2025 20:39:34 +0000` |

**Sources:**[debian/changelog 1-171](https://github.com/tenstorrent/tt-flash/blob/60c06032/debian/changelog#L1-L171)

### GitHub Release Changelog

The GitHub Release uses a different format generated by `mikepenz/release-changelog-builder-action@v5`[.github/workflows/release.yml 305-321](https://github.com/tenstorrent/tt-flash/blob/60c06032/.github/workflows/release.yml#L305-L321):

`{  "template": "#{{CHANGELOG}}\n\n## Contributors\n#{{CONTRIBUTORS}}",  "categories": [    {      "title": "## 🔄 Changes",      "labels": []    }  ],  "pr_template": "- #{{TITLE}} (#{{NUMBER}}) by @#{{AUTHOR}}",  "commit_template": "- #{{TITLE}} (#{{MERGE_SHA}}) by @#{{AUTHOR}}"}`
This generates a markdown changelog with both PR references and direct commits.

**Sources:**[.github/workflows/release.yml 305-321](https://github.com/tenstorrent/tt-flash/blob/60c06032/.github/workflows/release.yml#L305-L321)

* * *

## Debian Package Distribution

### Build Process

Debian packages are built by the `build-all.yml` workflow, which invokes `build-debian.yml` for each Ubuntu version. The build process:

1.   Checks out the repository at the specified ref
2.   Installs `git-buildpackage` tools
3.   Runs `gbp buildpackage` with GPG signing
4.   Generates `.deb` and `.changes` files
5.   Uploads artifacts to GitHub Actions

**Sources:** Referenced from [.github/workflows/release.yml 241-253](https://github.com/tenstorrent/tt-flash/blob/60c06032/.github/workflows/release.yml#L241-L253)

### Multi-Ubuntu Support

The Debian build workflow supports three Ubuntu versions:

| Ubuntu Version | Codename | Python Version | Use Case |
| --- | --- | --- | --- |
| 22.04 | Jammy | 3.10 | LTS, production systems |
| 24.04 | Noble | 3.12 | Latest LTS |
| latest | Rolling | Latest | Testing |

Each version produces a separate `.deb` package to ensure compatibility with different system library versions.

**Sources:**[debian/changelog 1-171](https://github.com/tenstorrent/tt-flash/blob/60c06032/debian/changelog#L1-L171) (shows `noble` target)

### Package Naming Convention

Debian packages are renamed during the release workflow [.github/workflows/release.yml 328-344](https://github.com/tenstorrent/tt-flash/blob/60c06032/.github/workflows/release.yml#L328-L344) to avoid filename collisions:

`# Pattern: tt-flash_VERSION_UBUNTU-VERSION.deb# Examples:#   artifacts-ubuntu-22.04.deb#   artifacts-ubuntu-24.04.deb#   artifacts-ubuntu-latest.deb`
**Sources:**[.github/workflows/release.yml 328-344](https://github.com/tenstorrent/tt-flash/blob/60c06032/.github/workflows/release.yml#L328-L344)

* * *

## Python Wheel Distribution

### Build Workflow

Python wheels are built by [.github/workflows/build-pypi.yml 1-39](https://github.com/tenstorrent/tt-flash/blob/60c06032/.github/workflows/build-pypi.yml#L1-L39):

**Sources:**[.github/workflows/build-pypi.yml 1-39](https://github.com/tenstorrent/tt-flash/blob/60c06032/.github/workflows/build-pypi.yml#L1-L39)

### Build Tool Configuration

The `build` package follows PEP 517/518 standards, reading build configuration from `pyproject.toml`. The build process generates:

*   **Source distribution** (`.tar.gz`): Complete source code archive
*   **Wheel** (`.whl`): Binary distribution for faster installation

**Sources:**[.github/workflows/build-pypi.yml 28-31](https://github.com/tenstorrent/tt-flash/blob/60c06032/.github/workflows/build-pypi.yml#L28-L31)

### Trusted Publishing

Both TestPyPI and PyPI publication use OpenID Connect (OIDC) trusted publishing, eliminating the need for API tokens:

`permissions:  id-token: write  # IMPORTANT: mandatory for trusted publishing`
The `pypa/gh-action-pypi-publish@release/v1` action automatically obtains short-lived credentials from the GitHub Actions OIDC provider.

**Sources:**[.github/workflows/release.yml 404-419](https://github.com/tenstorrent/tt-flash/blob/60c06032/.github/workflows/release.yml#L404-L419)[.github/workflows/release.yml 438-452](https://github.com/tenstorrent/tt-flash/blob/60c06032/.github/workflows/release.yml#L438-L452)

### Two-Stage Publication

The workflow publishes to TestPyPI before PyPI [.github/workflows/release.yml 391-453](https://github.com/tenstorrent/tt-flash/blob/60c06032/.github/workflows/release.yml#L391-L453):

| Stage | Repository | URL | Purpose |
| --- | --- | --- | --- |
| Test | TestPyPI | `https://test.pypi.org/p/tt-flash` | Pre-production validation |
| Production | PyPI | `https://pypi.org/p/tt-flash` | Public distribution |

**Sources:**[.github/workflows/release.yml 391-453](https://github.com/tenstorrent/tt-flash/blob/60c06032/.github/workflows/release.yml#L391-L453)

* * *

## Git Tagging

### Tag Creation

The `tagrelease` job creates an annotated Git tag after successful builds [.github/workflows/release.yml 256-283](https://github.com/tenstorrent/tt-flash/blob/60c06032/.github/workflows/release.yml#L256-L283):

`# Tag format: v{MAJOR}.{MINOR}.{PATCH}git tag v${PACKAGE_VERSION_NEW}git push --tags`
Examples: `v3.5.0`, `v3.4.12`, `v3.4.11`

**Sources:**[.github/workflows/release.yml 278-283](https://github.com/tenstorrent/tt-flash/blob/60c06032/.github/workflows/release.yml#L278-L283)

### Tag Usage

Git tags serve multiple purposes:

1.   **Version reference**: Permanent reference to release commit
2.   **Changelog boundaries**: `gbp dch` uses tags to identify commits since last release [.github/workflows/release.yml 137-142](https://github.com/tenstorrent/tt-flash/blob/60c06032/.github/workflows/release.yml#L137-L142)
3.   **GitHub Releases**: Tag name links GitHub Release to Git history
4.   **Version validation**: Prevents re-releasing the same version

**Sources:**[.github/workflows/release.yml 137-142](https://github.com/tenstorrent/tt-flash/blob/60c06032/.github/workflows/release.yml#L137-L142)[.github/workflows/release.yml 278-283](https://github.com/tenstorrent/tt-flash/blob/60c06032/.github/workflows/release.yml#L278-L283)

* * *

## GitHub Release Creation

### Release Artifact Collection

The `generate-release` job collects artifacts from all build jobs [.github/workflows/release.yml 288-358](https://github.com/tenstorrent/tt-flash/blob/60c06032/.github/workflows/release.yml#L288-L358):

`files: |  ${{ github.workspace }}/${{ env.project_name }}*/*.deb  ${{ github.workspace }}/[!artifacts-]*/*.deb  ${{ github.workspace }}/*/*.whl`
This glob pattern captures:

*   Debian packages from all Ubuntu versions
*   Python wheels (both source and binary distributions)

**Sources:**[.github/workflows/release.yml 350-354](https://github.com/tenstorrent/tt-flash/blob/60c06032/.github/workflows/release.yml#L350-L354)

### Release Metadata

Each GitHub Release includes:

| Field | Value | Source |
| --- | --- | --- |
| Tag | `v${VERSION}` | `versionchange` job output |
| Title | Auto-generated | GitHub default (tag name) |
| Body | Changelog markdown | `release-changelog-builder-action` |
| Assets | `.deb`, `.whl`, `.changes` | Build job artifacts |
| Draft | `false` | Published immediately |
| Prerelease | `false` | Production release |

**Sources:**[.github/workflows/release.yml 346-357](https://github.com/tenstorrent/tt-flash/blob/60c06032/.github/workflows/release.yml#L346-L357)

* * *

## Distribution Channels Summary

### Current Distribution Channels

| Channel | Content | Access Method | Update Frequency |
| --- | --- | --- | --- |
| GitHub Releases | `.deb`, `.whl`, changelog | Direct download | Per release |
| PyPI | `.whl`, `.tar.gz` | `pip install tt-flash` | Per release |
| TestPyPI | `.whl`, `.tar.gz` | `pip install -i https://test.pypi.org/simple/ tt-flash` | Per release (pre-validation) |

**Sources:**[.github/workflows/release.yml 1-454](https://github.com/tenstorrent/tt-flash/blob/60c06032/.github/workflows/release.yml#L1-L454)

* * *

## Release Trigger and Permissions

### Manual Trigger

Releases are triggered manually via GitHub Actions `workflow_dispatch`[.github/workflows/release.yml 4-16](https://github.com/tenstorrent/tt-flash/blob/60c06032/.github/workflows/release.yml#L4-L16):

`workflow_dispatch:  inputs:    version_bump_type:      description: 'Version bump type'      required: true      default: 'patch'      type: choice      options:        - patch        - minor        - major`
### Required Permissions

The release workflow requires elevated GitHub token permissions [.github/workflows/release.yml 42-44](https://github.com/tenstorrent/tt-flash/blob/60c06032/.github/workflows/release.yml#L42-L44):

`permissions:  contents: write  # For pushing commits and tags  packages: write  # For publishing packages  id-token: write  # For OIDC trusted publishing`
**Sources:**[.github/workflows/release.yml 4-16](https://github.com/tenstorrent/tt-flash/blob/60c06032/.github/workflows/release.yml#L4-L16)[.github/workflows/release.yml 42-44](https://github.com/tenstorrent/tt-flash/blob/60c06032/.github/workflows/release.yml#L42-L44)

* * *

## Version History Tracking

The [debian/changelog 1-171](https://github.com/tenstorrent/tt-flash/blob/60c06032/debian/changelog#L1-L171) file provides a comprehensive version history with 20+ releases documented. Notable version milestones:

| Version | Date | Key Changes |
| --- | --- | --- |
| 3.5.0 | 2025-12-19 | Bump pyluwen to 0.8.0, tt-tools-common to 1.5 |
| 3.4.8 | 2025-11-26 | Remove Grayskull support, FW version 80.X.Y.Z handling |
| 3.4.7 | 2025-10-17 | Manifest v2.0.0 support, major version downgrade prevention |
| 3.4.6 | 2025-09-23 | Blackhole Galaxy UBB support |
| 3.4.1 | 2025-08-05 | Initial PyPI release, adjust dependencies |

**Sources:**[debian/changelog 1-171](https://github.com/tenstorrent/tt-flash/blob/60c06032/debian/changelog#L1-L171)

Dismiss
Refresh this wiki

Enter email to refresh
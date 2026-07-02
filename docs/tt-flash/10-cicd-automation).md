---
project: tt-flash
github: https://github.com/tenstorrent/tt-flash
deepwiki: https://deepwiki.com/tenstorrent/tt-flash/10-cicd-automation)
---

# CI/CD Automation

Relevant source files
*   [.github/workflows/build-debian.yml](https://github.com/tenstorrent/tt-flash/blob/60c06032/.github/workflows/build-debian.yml)
*   [.github/workflows/build-pypi.yml](https://github.com/tenstorrent/tt-flash/blob/60c06032/.github/workflows/build-pypi.yml)
*   [.github/workflows/community-issue-tagging.yml](https://github.com/tenstorrent/tt-flash/blob/60c06032/.github/workflows/community-issue-tagging.yml)
*   [.github/workflows/release.yml](https://github.com/tenstorrent/tt-flash/blob/60c06032/.github/workflows/release.yml)

This document describes the GitHub Actions-based CI/CD infrastructure that automates building, testing, releasing, and distributing tt-flash across multiple platforms. The system manages dual-track distribution (Debian packages and PyPI wheels), automated versioning, changelog generation, and community issue management.

For information about the release process and packaging outputs, see [Release and Distribution](https://deepwiki.com/tenstorrent/tt-flash/9-release-and-distribution). For development build processes, see [Building from Source](https://deepwiki.com/tenstorrent/tt-flash/8.1-building-from-source).

* * *

## Workflow Architecture

The CI/CD system is organized around five primary GitHub Actions workflows that orchestrate the complete build-test-release pipeline:

**Sources:**[.github/workflows/release.yml 1-453](https://github.com/tenstorrent/tt-flash/blob/60c06032/.github/workflows/release.yml#L1-L453)[.github/workflows/build-debian.yml 1-179](https://github.com/tenstorrent/tt-flash/blob/60c06032/.github/workflows/build-debian.yml#L1-L179)[.github/workflows/build-pypi.yml 1-38](https://github.com/tenstorrent/tt-flash/blob/60c06032/.github/workflows/build-pypi.yml#L1-L38)[.github/workflows/community-issue-tagging.yml 1-19](https://github.com/tenstorrent/tt-flash/blob/60c06032/.github/workflows/community-issue-tagging.yml#L1-L19)

* * *

## Build Workflows

### build-all.yml Orchestration

The `build-all.yml` workflow (referenced but not included in the provided files) serves as the primary orchestrator for parallel build execution. It is invoked by `release.yml` with specific version parameters and Git ref:

`with:  ref: ${{ needs.create-temp-branch.outputs.temp_branch_ref }}  MAJOR: ${{ needs.versionchange.outputs.version_major }}  MINOR: ${{ needs.versionchange.outputs.version_minor }}  PATCH: ${{ needs.versionchange.outputs.version_patch }}  NUMBER_OF_COMMITS_SINCE_TAG: ${{ needs.versionchange.outputs.number_of_commits_since_tag }}`
This workflow calls both `build-debian.yml` and `build-pypi.yml` as sub-workflows using the `workflow_call` trigger mechanism.

**Sources:**[.github/workflows/release.yml 241-253](https://github.com/tenstorrent/tt-flash/blob/60c06032/.github/workflows/release.yml#L241-L253)

* * *

### build-debian.yml: Multi-Ubuntu Package Builder

The Debian build workflow produces signed `.deb` packages for multiple Ubuntu LTS versions using a matrix strategy:

#### Key Implementation Details

**Matrix Strategy**[.github/workflows/build-debian.yml 28-34](https://github.com/tenstorrent/tt-flash/blob/60c06032/.github/workflows/build-debian.yml#L28-L34):

*   Runs on three Ubuntu versions: `ubuntu-22.04`, `ubuntu-24.04`, `ubuntu-latest`
*   Uses `fail-fast: false` to ensure all OS builds complete even if one fails

**Dependency Installation**[.github/workflows/build-debian.yml 52-66](https://github.com/tenstorrent/tt-flash/blob/60c06032/.github/workflows/build-debian.yml#L52-L66):

`sudo apt install -y \  build-essential \  debhelper \  dh-python \  dh-sequence-python3 \  git-buildpackage \  gnupg \  libpython3-all-dev \  pybuild-plugin-pyproject \  python3-all \  python3-pip \  python3-tomli`
**GPG Signing**[.github/workflows/build-debian.yml 67-73](https://github.com/tenstorrent/tt-flash/blob/60c06032/.github/workflows/build-debian.yml#L67-L73):

*   Uses `crazy-max/ghaction-import-gpg@v6` to import signing key
*   Key stored in GitHub secret: `PKG_SIGNING_KEY_DEB`
*   Fingerprint passed to `gbp buildpackage` via `DEBSIGN_KEYID` environment variable

**Changelog Generation**[.github/workflows/build-debian.yml 119-127](https://github.com/tenstorrent/tt-flash/blob/60c06032/.github/workflows/build-debian.yml#L119-L127):

*   Uses `gbp dch` (git-buildpackage debian changelog) command
*   Automatically generates entries from Git commit history
*   Version format: `${MAJOR}.${MINOR}.${PATCH}`
*   Sets maintainer to `releases@tenstorrent.com` / `Tenstorrent Releases`

**Package Building**[.github/workflows/build-debian.yml 128-133](https://github.com/tenstorrent/tt-flash/blob/60c06032/.github/workflows/build-debian.yml#L128-L133):

`gbp buildpackage --git-ignore-new`
*   `--git-ignore-new` allows building with uncommitted changes
*   Creates signed `.deb`, `.changes`, and related files in parent directory

**Artifact Naming**[.github/workflows/build-debian.yml 143-158](https://github.com/tenstorrent/tt-flash/blob/60c06032/.github/workflows/build-debian.yml#L143-L158):

*   Original filename: `tt-flash_X.Y.Z_all.deb`
*   Renamed to: `tt-flash_X.Y.Z_all-ubuntu-22.04.deb` (distro-specific)
*   Ensures artifacts from different Ubuntu versions don't conflict

**Sources:**[.github/workflows/build-debian.yml 1-179](https://github.com/tenstorrent/tt-flash/blob/60c06032/.github/workflows/build-debian.yml#L1-L179)

* * *

### build-pypi.yml: Python Wheel Builder

The PyPI build workflow produces universal Python wheel packages using modern Python packaging standards:

#### Implementation Details

**Python Version**[.github/workflows/build-pypi.yml 24-27](https://github.com/tenstorrent/tt-flash/blob/60c06032/.github/workflows/build-pypi.yml#L24-L27):

*   Fixed to Python 3.10 for consistent builds
*   Uses `actions/setup-python@v5` action

**Build Tool**[.github/workflows/build-pypi.yml 28-31](https://github.com/tenstorrent/tt-flash/blob/60c06032/.github/workflows/build-pypi.yml#L28-L31):

*   Uses PEP 517/518 compliant `build` package
*   Command: `python -m build`
*   Reads configuration from `pyproject.toml`
*   Produces both wheel (`.whl`) and source distribution (`.tar.gz`)

**Git History**[.github/workflows/build-pypi.yml 18-23](https://github.com/tenstorrent/tt-flash/blob/60c06032/.github/workflows/build-pypi.yml#L18-L23):

*   Fetches full Git history (`fetch-depth: 0`) and tags (`fetch-tags: true`)
*   Required for version extraction during build

**Artifact Upload**[.github/workflows/build-pypi.yml 33-37](https://github.com/tenstorrent/tt-flash/blob/60c06032/.github/workflows/build-pypi.yml#L33-L37):

*   Artifact name: `release-dists`
*   Contains all files in `dist/` directory
*   Downloaded later by `release.yml` for publishing

**Sources:**[.github/workflows/build-pypi.yml 1-38](https://github.com/tenstorrent/tt-flash/blob/60c06032/.github/workflows/build-pypi.yml#L1-L38)

* * *

## Release Workflow

The `release.yml` workflow orchestrates the complete release process through nine sequential and parallel jobs:

**Sources:**[.github/workflows/release.yml 1-453](https://github.com/tenstorrent/tt-flash/blob/60c06032/.github/workflows/release.yml#L1-L453)

* * *

### Stage 1: Temporary Branch Creation

The `create-temp-branch` job [.github/workflows/release.yml 41-62](https://github.com/tenstorrent/tt-flash/blob/60c06032/.github/workflows/release.yml#L41-L62) isolates release work from the main branch:

**Branch Naming Pattern**[.github/workflows/release.yml 54-58](https://github.com/tenstorrent/tt-flash/blob/60c06032/.github/workflows/release.yml#L54-L58):

`rc-temp-$( git rev-parse --short HEAD )-$( date +%Y.%m.%d-%H.%M.%S )`
Example: `rc-temp-a1b2c3d-2024.03.15-14.30.45`

**Output**: Exports `temp_branch_ref` for downstream jobs

**Sources:**[.github/workflows/release.yml 41-62](https://github.com/tenstorrent/tt-flash/blob/60c06032/.github/workflows/release.yml#L41-L62)

* * *

### Stage 2: Version Management

The `versionchange` job [.github/workflows/release.yml 64-160](https://github.com/tenstorrent/tt-flash/blob/60c06032/.github/workflows/release.yml#L64-L160) manages semantic versioning:

**Version Bump Process**[.github/workflows/release.yml 95-121](https://github.com/tenstorrent/tt-flash/blob/60c06032/.github/workflows/release.yml#L95-L121):

1.   Install `toml-cli` for parsing `pyproject.toml`
2.   Read current version: `toml get project.version --toml-path pyproject.toml`
3.   Install `bump2version` package
4.   Execute bump: `bump2version ${{ github.event.inputs.version_bump_type }} pyproject.toml`
5.   Read new version and parse using `release-kit/semver@v2`

**Job Outputs**[.github/workflows/release.yml 73-84](https://github.com/tenstorrent/tt-flash/blob/60c06032/.github/workflows/release.yml#L73-L84):

*   `git_hash`: SHA of the commit after version bump
*   `package_name`: From `project.name` in `pyproject.toml`
*   `package_version` / `package_version_new`: Before/after versions
*   `version_major`, `version_minor`, `version_patch`: Parsed components
*   `number_of_commits_since_tag`: For developmental versioning

**Commit Details**[.github/workflows/release.yml 144-149](https://github.com/tenstorrent/tt-flash/blob/60c06032/.github/workflows/release.yml#L144-L149):

*   Committer: `releases@tenstorrent.com` / `Tenstorrent Releases`
*   Message: `pyproject.toml- updating version to X.Y.Z`

**Sources:**[.github/workflows/release.yml 64-160](https://github.com/tenstorrent/tt-flash/blob/60c06032/.github/workflows/release.yml#L64-L160)

* * *

### Stage 3: Changelog Generation

The `changelogs` job [.github/workflows/release.yml 165-236](https://github.com/tenstorrent/tt-flash/blob/60c06032/.github/workflows/release.yml#L165-L236) generates Debian-format changelogs:

**Git-Buildpackage Integration**[.github/workflows/release.yml 203-213](https://github.com/tenstorrent/tt-flash/blob/60c06032/.github/workflows/release.yml#L203-L213):

`gbp dch \  --debian-branch ${{ needs.create-temp-branch.outputs.temp_branch_ref }} \  -R \  -N ${MAJOR}.${MINOR}.${PATCH} \  --spawn-editor=never`
**Flags Explained**:

*   `--debian-branch`: Specifies the temporary release branch
*   `-R`: Mark as released (not `-UNRELEASED`)
*   `-N`: New version number
*   `--spawn-editor=never`: Non-interactive mode

**Output**: Updates `debian/changelog` with commit history since last tag

**Sources:**[.github/workflows/release.yml 165-236](https://github.com/tenstorrent/tt-flash/blob/60c06032/.github/workflows/release.yml#L165-L236)

* * *

### Stage 4: Build Orchestration

The `build_all_depends` job [.github/workflows/release.yml 241-253](https://github.com/tenstorrent/tt-flash/blob/60c06032/.github/workflows/release.yml#L241-L253) coordinates parallel builds using `workflow_call`:

**Parameters Passed**:

`with:  ref: ${{ needs.create-temp-branch.outputs.temp_branch_ref }}  MAJOR: ${{ needs.versionchange.outputs.version_major }}  MINOR: ${{ needs.versionchange.outputs.version_minor }}  PATCH: ${{ needs.versionchange.outputs.version_patch }}  NUMBER_OF_COMMITS_SINCE_TAG: ${{ needs.versionchange.outputs.number_of_commits_since_tag }}secrets: inherit`
This triggers both `build-debian.yml` (3 Ubuntu variants) and `build-pypi.yml` (Python wheel) in parallel.

**Sources:**[.github/workflows/release.yml 241-253](https://github.com/tenstorrent/tt-flash/blob/60c06032/.github/workflows/release.yml#L241-L253)

* * *

### Stage 5: Git Tagging

The `tagrelease` job [.github/workflows/release.yml 257-283](https://github.com/tenstorrent/tt-flash/blob/60c06032/.github/workflows/release.yml#L257-L283) creates annotated Git tags:

**Tagging Process**[.github/workflows/release.yml 278-283](https://github.com/tenstorrent/tt-flash/blob/60c06032/.github/workflows/release.yml#L278-L283):

`git tag v${{ needs.versionchange.outputs.package_version_new }}git taggit push --tags`
**Tag Format**: `vX.Y.Z` (e.g., `v3.5.0`)

**Sources:**[.github/workflows/release.yml 257-283](https://github.com/tenstorrent/tt-flash/blob/60c06032/.github/workflows/release.yml#L257-L283)

* * *

### Stage 6: GitHub Release Creation

The `generate-release` job [.github/workflows/release.yml 288-357](https://github.com/tenstorrent/tt-flash/blob/60c06032/.github/workflows/release.yml#L288-L357) assembles and publishes the GitHub release:

**Artifact Download**[.github/workflows/release.yml 302-303](https://github.com/tenstorrent/tt-flash/blob/60c06032/.github/workflows/release.yml#L302-L303):

*   Downloads all artifacts from previous jobs using `actions/download-artifact@v4`
*   No pattern filter = downloads everything

**Changelog Builder**[.github/workflows/release.yml 305-321](https://github.com/tenstorrent/tt-flash/blob/60c06032/.github/workflows/release.yml#L305-L321):

*   Uses `mikepenz/release-changelog-builder-action@v5`
*   Mode: `HYBRID` (includes PRs and direct commits)
*   Template includes changes and contributors sections

**Debian Package Renaming**[.github/workflows/release.yml 328-344](https://github.com/tenstorrent/tt-flash/blob/60c06032/.github/workflows/release.yml#L328-L344):

`for x in $(find ${GITHUB_WORKSPACE} -type f -iname *.deb | grep -v "artifacts-ubuntu")do  want="$(echo "${x}" | xargs dirname | tr "/" "\n" | tail -n 1)"  mv -v "${x}" "$(echo "${x}" | xargs dirname)/${want}"done`
*   Ensures `.deb` files from different Ubuntu versions have unique names
*   Uses parent directory name (artifact name) as filename

**Release Creation**[.github/workflows/release.yml 346-357](https://github.com/tenstorrent/tt-flash/blob/60c06032/.github/workflows/release.yml#L346-L357):

`uses: softprops/action-gh-release@v1with:  tag_name: v${{ needs.versionchange.outputs.package_version_new }}  files: |    ${{ github.workspace }}/${{ env.project_name }}*/*.deb    ${{ github.workspace }}/[!artifacts-]*/*.deb    ${{ github.workspace }}/*/*.whl  body: ${{ steps.build_changelog.outputs.changelog }}  draft: false  prerelease: false`
**Sources:**[.github/workflows/release.yml 288-357](https://github.com/tenstorrent/tt-flash/blob/60c06032/.github/workflows/release.yml#L288-L357)

* * *

### Stage 7: Branch Merge and Cleanup

The `mergeback` job [.github/workflows/release.yml 362-386](https://github.com/tenstorrent/tt-flash/blob/60c06032/.github/workflows/release.yml#L362-L386) integrates changes back to main:

**Merge Process**[.github/workflows/release.yml 379-386](https://github.com/tenstorrent/tt-flash/blob/60c06032/.github/workflows/release.yml#L379-L386):

`git log -3 --onelinegit rebase origin/${{ needs.create-temp-branch.outputs.temp_branch_ref }}git pull --rebasegit log -3 --onelinegit pushgit push origin --delete ${{ needs.create-temp-branch.outputs.temp_branch_ref }}`
*   Uses `git rebase` to apply temp branch commits to main
*   Deletes temporary branch after successful merge

**Sources:**[.github/workflows/release.yml 362-386](https://github.com/tenstorrent/tt-flash/blob/60c06032/.github/workflows/release.yml#L362-L386)

* * *

### Stage 8-9: PyPI Publishing

**TestPyPI Publication**[.github/workflows/release.yml 391-419](https://github.com/tenstorrent/tt-flash/blob/60c06032/.github/workflows/release.yml#L391-L419):

`uses: pypa/gh-action-pypi-publish@release/v1with:  repository-url: https://test.pypi.org/legacy/  verbose: true`
*   Environment: `testpypi`
*   Uses trusted publishing (no API tokens needed)
*   Requires `id-token: write` permission

**Production PyPI Publication**[.github/workflows/release.yml 424-452](https://github.com/tenstorrent/tt-flash/blob/60c06032/.github/workflows/release.yml#L424-L452):

`uses: pypa/gh-action-pypi-publish@release/v1with:  verbose: true`
*   Environment: `pypi`
*   URL: `https://pypi.org/p/tt-flash`
*   Depends on successful TestPyPI publication
*   Uses trusted publishing with OIDC

**Sources:**[.github/workflows/release.yml 391-452](https://github.com/tenstorrent/tt-flash/blob/60c06032/.github/workflows/release.yml#L391-L452)

* * *

## Community Automation

### Issue Tagging Workflow

The `community-issue-tagging.yml` workflow [.github/workflows/community-issue-tagging.yml 1-19](https://github.com/tenstorrent/tt-flash/blob/60c06032/.github/workflows/community-issue-tagging.yml#L1-L19) automatically labels issues and pull requests from community contributors:

**Trigger Events**[.github/workflows/community-issue-tagging.yml 3-7](https://github.com/tenstorrent/tt-flash/blob/60c06032/.github/workflows/community-issue-tagging.yml#L3-L7):

*   `issues.types: [opened]`: New issues
*   `pull_request.types: [opened]`: New pull requests

**Reusable Workflow Pattern**[.github/workflows/community-issue-tagging.yml 15-18](https://github.com/tenstorrent/tt-flash/blob/60c06032/.github/workflows/community-issue-tagging.yml#L15-L18):

`uses: tenstorrent/tt-github-actions/.github/workflows/issues-community-autotag.yml@mainsecrets:  AUTOLABEL_COMMUNITY_ISSUES: ${{ secrets.AUTOLABEL_COMMUNITY_ISSUES }}`
This leverages a centralized workflow maintained in the `tenstorrent/tt-github-actions` repository, allowing consistent labeling logic across multiple Tenstorrent projects.

**Permissions Model**[.github/workflows/community-issue-tagging.yml 9-12](https://github.com/tenstorrent/tt-flash/blob/60c06032/.github/workflows/community-issue-tagging.yml#L9-L12):

*   Minimal read access to repository contents
*   Write access to issues and pull requests for adding labels
*   Follows principle of least privilege

**Sources:**[.github/workflows/community-issue-tagging.yml 1-19](https://github.com/tenstorrent/tt-flash/blob/60c06032/.github/workflows/community-issue-tagging.yml#L1-L19)

* * *

## Artifact Management

### Artifact Naming Convention

The CI/CD system produces artifacts with specific naming patterns to prevent conflicts:

| Artifact Type | Original Name | Renamed/Suffixed | Example |
| --- | --- | --- | --- |
| Debian Package | `tt-flash_3.5.0_all.deb` | `tt-flash_3.5.0_all-ubuntu-22.04.deb` | Ubuntu version suffix |
| Changes File | `tt-flash_3.5.0_amd64.changes` | `tt-flash_3.5.0_amd64-ubuntu-22.04.changes` | Ubuntu version suffix |
| Python Wheel | `tt_flash-3.5.0-py3-none-any.whl` | (unchanged) | No suffix needed |
| Changelog | `debian/changelog` | `debian-changelog-ubuntu-22.04` | Artifact name includes distro |

**Sources:**[.github/workflows/build-debian.yml 143-178](https://github.com/tenstorrent/tt-flash/blob/60c06032/.github/workflows/build-debian.yml#L143-L178)

* * *

### Artifact Flow

**Sources:**[.github/workflows/build-debian.yml 159-178](https://github.com/tenstorrent/tt-flash/blob/60c06032/.github/workflows/build-debian.yml#L159-L178)[.github/workflows/build-pypi.yml 33-37](https://github.com/tenstorrent/tt-flash/blob/60c06032/.github/workflows/build-pypi.yml#L33-L37)[.github/workflows/release.yml 302-357](https://github.com/tenstorrent/tt-flash/blob/60c06032/.github/workflows/release.yml#L302-L357)

* * *

## Security and Secrets Management

### GitHub Secrets

The CI/CD system requires the following secrets:

| Secret Name | Purpose | Used In |
| --- | --- | --- |
| `PKG_SIGNING_KEY_DEB` | GPG private key for signing Debian packages | `build-debian.yml` |
| `AUTOLABEL_COMMUNITY_ISSUES` | Token for auto-labeling community contributions | `community-issue-tagging.yml` |

**GPG Key Import**[.github/workflows/build-debian.yml 67-73](https://github.com/tenstorrent/tt-flash/blob/60c06032/.github/workflows/build-debian.yml#L67-L73):

`- name: Import GPG key  id: gpg_key_import  uses: crazy-max/ghaction-import-gpg@v6  with:    gpg_private_key: ${{ secrets.PKG_SIGNING_KEY_DEB }}`
The imported key's fingerprint is captured as `${{ steps.gpg_key_import.outputs.fingerprint }}` and passed to `gbp buildpackage` via the `DEBSIGN_KEYID` environment variable [.github/workflows/build-debian.yml 133](https://github.com/tenstorrent/tt-flash/blob/60c06032/.github/workflows/build-debian.yml#L133-L133)

**Sources:**[.github/workflows/build-debian.yml 67-73](https://github.com/tenstorrent/tt-flash/blob/60c06032/.github/workflows/build-debian.yml#L67-L73)[.github/workflows/community-issue-tagging.yml 17-18](https://github.com/tenstorrent/tt-flash/blob/60c06032/.github/workflows/community-issue-tagging.yml#L17-L18)

* * *

### Trusted Publishing

PyPI publication uses OpenID Connect (OIDC) trusted publishing instead of API tokens:

**Permission Requirement**[.github/workflows/release.yml 404-405](https://github.com/tenstorrent/tt-flash/blob/60c06032/.github/workflows/release.yml#L404-L405):

`permissions:  id-token: write  # IMPORTANT: mandatory for trusted publishing`
This allows GitHub Actions to authenticate with PyPI using OIDC tokens, eliminating the need to store long-lived API tokens as secrets. The publishing action automatically handles token exchange.

**Sources:**[.github/workflows/release.yml 404-405](https://github.com/tenstorrent/tt-flash/blob/60c06032/.github/workflows/release.yml#L404-L405)[.github/workflows/release.yml 438-439](https://github.com/tenstorrent/tt-flash/blob/60c06032/.github/workflows/release.yml#L438-L439)

* * *

## Environment-Specific Configuration

### Environment Protection

The release workflow uses GitHub Environments for production deployments:

| Environment Name | URL | Protection Rules |
| --- | --- | --- |
| `testpypi` | [https://test.pypi.org/p/tt-flash](https://test.pypi.org/p/tt-flash) | Optional (not specified) |
| `pypi` | [https://pypi.org/p/tt-flash](https://pypi.org/p/tt-flash) | Requires `testpypi` success |

**TestPyPI Environment**[.github/workflows/release.yml 400-402](https://github.com/tenstorrent/tt-flash/blob/60c06032/.github/workflows/release.yml#L400-L402):

`environment:  name: testpypi  url: https://test.pypi.org/p/${{ env.project_name }}`
**Production PyPI Environment**[.github/workflows/release.yml 434-436](https://github.com/tenstorrent/tt-flash/blob/60c06032/.github/workflows/release.yml#L434-L436):

`environment:  name: pypi  url: https://pypi.org/p/${{ env.project_name }}`
The `needs` dependency [.github/workflows/release.yml 429-432](https://github.com/tenstorrent/tt-flash/blob/60c06032/.github/workflows/release.yml#L429-L432) ensures production deployment only occurs after successful test deployment:

`needs:  - build_all_depends  - generate-release  - publish-to-testpypi`
**Sources:**[.github/workflows/release.yml 400-402](https://github.com/tenstorrent/tt-flash/blob/60c06032/.github/workflows/release.yml#L400-L402)[.github/workflows/release.yml 429-436](https://github.com/tenstorrent/tt-flash/blob/60c06032/.github/workflows/release.yml#L429-L436)

* * *

## Workflow Dependencies

The following table summarizes inter-job dependencies in the `release.yml` workflow:

| Job | Depends On | Purpose |
| --- | --- | --- |
| `versionchange` | `create-temp-branch` | Needs temp branch ref |
| `changelogs` | `create-temp-branch`, `versionchange` | Needs version info and branch |
| `build_all_depends` | `create-temp-branch`, `versionchange`, `changelogs` | Needs all updates before building |
| `tagrelease` | `versionchange`, `changelogs`, `build_all_depends` | Needs build success and version info |
| `generate-release` | `create-temp-branch`, `versionchange`, `changelogs`, `build_all_depends`, `tagrelease` | Needs all artifacts and tag |
| `mergeback` | `create-temp-branch`, `generate-release` | Needs release success and branch name |
| `publish-to-testpypi` | `build_all_depends`, `generate-release` | Needs wheel artifacts |
| `publish-to-pypi` | `build_all_depends`, `generate-release`, `publish-to-testpypi` | Needs TestPyPI success |

**Sources:**[.github/workflows/release.yml 1-453](https://github.com/tenstorrent/tt-flash/blob/60c06032/.github/workflows/release.yml#L1-L453)

Dismiss
Refresh this wiki

Enter email to refresh
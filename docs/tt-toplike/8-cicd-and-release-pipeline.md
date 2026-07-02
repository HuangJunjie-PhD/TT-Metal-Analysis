---
project: tt-toplike
github: https://github.com/tenstorrent/tt-toplike
deepwiki: https://deepwiki.com/tenstorrent/tt-toplike/8-cicd-and-release-pipeline
---

# CI/CD and Release Pipeline

Relevant source files
*   [.github/workflows/ci.yml](https://github.com/tenstorrent/tt-toplike/blob/487bfe5a/.github/workflows/ci.yml)
*   [.github/workflows/release.yml](https://github.com/tenstorrent/tt-toplike/blob/487bfe5a/.github/workflows/release.yml)

The `tt-toplike` project utilizes GitHub Actions to maintain code quality through automated gating and to streamline the delivery of Debian packages. The pipeline is split into two primary workflows: **Continuous Integration (CI)**, which validates every pull request, and **Release**, which packages the software for multiple Ubuntu distributions and updates the project website.

### Workflow Orchestration

The following diagram illustrates how GitHub events trigger specific automation jobs and how those jobs interact with the codebase and external release assets.

**CI/CD Workflow Overview**

**Sources:**[.github/workflows/ci.yml 1-9](https://github.com/tenstorrent/tt-toplike/blob/487bfe5a/.github/workflows/ci.yml#L1-L9)[.github/workflows/release.yml 1-7](https://github.com/tenstorrent/tt-toplike/blob/487bfe5a/.github/workflows/release.yml#L1-L7)

* * *

### Continuous Integration

The CI workflow acts as a quality gate for all contributions. It is designed to be developer-friendly by ignoring pre-existing "formatting debt" in files that a contributor hasn't touched, while enforcing strict adherence to warnings and tests for new code.

*   **Differential Formatting:** The workflow identifies changed `.rs` files between the PR branch and `main` and only fails if those specific files fail `rustfmt` checks [.github/workflows/ci.yml 24-52](https://github.com/tenstorrent/tt-toplike/blob/487bfe5a/.github/workflows/ci.yml#L24-L52)
*   **Strict Linting:** Clippy is executed with the `-D warnings` flag to ensure no lint violations are introduced into the TUI or library crates [.github/workflows/ci.yml 54-55](https://github.com/tenstorrent/tt-toplike/blob/487bfe5a/.github/workflows/ci.yml#L54-L55)
*   **Verification Builds:** Both TUI and GUI binaries are compiled on `ubuntu-24.04` runners to ensure that feature-flagged dependencies (like `egui` or `iced`) remain compatible with the core library [.github/workflows/ci.yml 60-69](https://github.com/tenstorrent/tt-toplike/blob/487bfe5a/.github/workflows/ci.yml#L60-L69)

For details, see [Continuous Integration](https://deepwiki.com/tenstorrent/tt-toplike/8.1-continuous-integration).

**Sources:**[.github/workflows/ci.yml 10-69](https://github.com/tenstorrent/tt-toplike/blob/487bfe5a/.github/workflows/ci.yml#L10-L69)

* * *

### Release and Debian Packaging

The release pipeline automates the creation of `.deb` packages for supported Ubuntu distributions (`noble` and `jammy`). This process ensures that binaries are linked against the correct `glibc` versions for their respective target environments.

*   **Matrix Strategy:** The workflow uses a GitHub Actions matrix to run parallel builds on `ubuntu-24.04` and `ubuntu-22.04` runners [.github/workflows/release.yml 28-36](https://github.com/tenstorrent/tt-toplike/blob/487bfe5a/.github/workflows/release.yml#L28-L36)
*   **Changelog Patching:** For non-default suites (like `jammy`), the workflow dynamically patches `debian/changelog` to update the target distribution before calling the build script [.github/workflows/release.yml 52-54](https://github.com/tenstorrent/tt-toplike/blob/487bfe5a/.github/workflows/release.yml#L52-L54)
*   **Race-Condition Safety:** The workflow includes logic to check if a GitHub Release already exists before attempting to create it, allowing parallel matrix jobs to safely converge on the same release object [.github/workflows/release.yml 83-91](https://github.com/tenstorrent/tt-toplike/blob/487bfe5a/.github/workflows/release.yml#L83-L91)
*   **Site Automation:** Upon a successful release, the `gh-pages` branch is updated to reflect the new version number and download links on the project's landing page [.github/workflows/release.yml 117-143](https://github.com/tenstorrent/tt-toplike/blob/487bfe5a/.github/workflows/release.yml#L117-L143)

For details, see [Release and Debian Packaging](https://deepwiki.com/tenstorrent/tt-toplike/8.2-release-and-debian-packaging).

**Sources:**[.github/workflows/release.yml 12-144](https://github.com/tenstorrent/tt-toplike/blob/487bfe5a/.github/workflows/release.yml#L12-L144)

* * *

### Code Entity Mapping

The following diagram maps the logical CI/CD stages to the specific files and shell commands executed within the GitHub Actions environment.

**Workflow Implementation Map**

**Sources:**[.github/workflows/ci.yml 25-58](https://github.com/tenstorrent/tt-toplike/blob/487bfe5a/.github/workflows/ci.yml#L25-L58)[.github/workflows/release.yml 54-57](https://github.com/tenstorrent/tt-toplike/blob/487bfe5a/.github/workflows/release.yml#L54-L57)

Dismiss
Refresh this wiki

Enter email to refresh
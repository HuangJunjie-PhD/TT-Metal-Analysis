---
project: tt-studio
github: https://github.com/tenstorrent/tt-studio
deepwiki: https://deepwiki.com/tenstorrent/tt-studio/8-development-guide
---

# Development Guide

Relevant source files
*   [CODE_OF_CONDUCT.md](https://github.com/tenstorrent/tt-studio/blob/a6d347af/CODE_OF_CONDUCT.md?plain=1)
*   [CONTRIBUTING.md](https://github.com/tenstorrent/tt-studio/blob/a6d347af/CONTRIBUTING.md?plain=1)

This guide provides high-level information for developers contributing to or extending TT-Studio. It outlines the contribution standards, branching strategies, and quality assurance processes required to maintain the codebase.

For detailed technical instructions, please refer to the following child pages:

*   **[Development Environment](https://deepwiki.com/tenstorrent/tt-studio/8.1-development-environment)** — Setup for dev mode with live reloading, pre-commit hooks, and VSCode configuration.
*   **[Testing and CI](https://deepwiki.com/tenstorrent/tt-studio/8.2-testing-and-ci)** — Details on GitHub Actions, license checking, and automated linting.
*   **[Issue and Feature Request Management](https://deepwiki.com/tenstorrent/tt-studio/8.3-issue-and-feature-request-management)** — Procedures for bug reports and feature requests.

## Contribution Standards

All contributions must follow a structured process to ensure code quality and system stability. This includes mandatory issue tracking, code reviews by maintainers or codeowners, and passing all automated CI checks.

### Contribution Requirements

*   **Issue Tracking:** Every change must be preceded by a feature request or bug report in the Issues section [CONTRIBUTING.md 11-13](https://github.com/tenstorrent/tt-studio/blob/a6d347af/CONTRIBUTING.md?plain=1#L11-L13)
*   **Pull Requests (PRs):** Changes must be submitted via PR and require approval from the maintaining team and relevant codeowners [CONTRIBUTING.md 15-29](https://github.com/tenstorrent/tt-studio/blob/a6d347af/CONTRIBUTING.md?plain=1#L15-L29)
*   **Validation:** PRs must pass pre-commit hooks, automated GitHub Actions, and any specific testing requirements mandated by codeowners [CONTRIBUTING.md 30-33](https://github.com/tenstorrent/tt-studio/blob/a6d347af/CONTRIBUTING.md?plain=1#L30-L33)

### Code of Conduct

Contributors are expected to follow the Contributor Covenant to ensure a welcoming and harassment-free environment [CODE_OF_CONDUCT.md 1-13](https://github.com/tenstorrent/tt-studio/blob/a6d347af/CODE_OF_CONDUCT.md?plain=1#L1-L13)

Sources: [CONTRIBUTING.md 1-34](https://github.com/tenstorrent/tt-studio/blob/a6d347af/CONTRIBUTING.md?plain=1#L1-L34)[CODE_OF_CONDUCT.md 1-13](https://github.com/tenstorrent/tt-studio/blob/a6d347af/CODE_OF_CONDUCT.md?plain=1#L1-L13)

## Git Branching and Versioning

TT-Studio utilizes a structured branching strategy to manage features, releases, and production-ready code.

### Branching Strategy

*   **`main`**: Production-ready tagged code. Requires rebase/squash merge from release branches [CONTRIBUTING.md 41-45](https://github.com/tenstorrent/tt-studio/blob/a6d347af/CONTRIBUTING.md?plain=1#L41-L45)
*   **`dev`**: The central integration branch where feature branches are merged [CONTRIBUTING.md 47-50](https://github.com/tenstorrent/tt-studio/blob/a6d347af/CONTRIBUTING.md?plain=1#L47-L50)
*   **Feature Branches**: Created from `dev` using naming conventions like `dev-name/feature` or `dev/issue-number`[CONTRIBUTING.md 58-62](https://github.com/tenstorrent/tt-studio/blob/a6d347af/CONTRIBUTING.md?plain=1#L58-L62)
*   **Release Branches**: Named `rc-vx.x.x`, used for final testing and cherry-picking validated features from `dev`[CONTRIBUTING.md 73-85](https://github.com/tenstorrent/tt-studio/blob/a6d347af/CONTRIBUTING.md?plain=1#L73-L85)

### Semantic Versioning

The project follows semantic versioning (`MAJOR.MINOR.PATCH`):

*   **MAJOR**: Breaking changes to APIs or networking design [CONTRIBUTING.md 109-115](https://github.com/tenstorrent/tt-studio/blob/a6d347af/CONTRIBUTING.md?plain=1#L109-L115)
*   **MINOR**: Backward-compatible new features (e.g., new model support) [CONTRIBUTING.md 116-121](https://github.com/tenstorrent/tt-studio/blob/a6d347af/CONTRIBUTING.md?plain=1#L116-L121)
*   **PATCH**: Backward-compatible bug fixes [CONTRIBUTING.md 122-124](https://github.com/tenstorrent/tt-studio/blob/a6d347af/CONTRIBUTING.md?plain=1#L122-L124)

### Git Flow to Code Entities

This diagram maps the branching strategy to the specific files and workflows that govern it.

Sources: [CONTRIBUTING.md 37-124](https://github.com/tenstorrent/tt-studio/blob/a6d347af/CONTRIBUTING.md?plain=1#L37-L124)

## Quality Assurance and Licensing

Maintaining a high standard of code quality is enforced through automated workflows.

### License Compliance

TT-Studio is licensed under **Apache License 2.0**. All source files must contain the appropriate SPDX license identifier header [CONTRIBUTING.md 32](https://github.com/tenstorrent/tt-studio/blob/a6d347af/CONTRIBUTING.md?plain=1#L32-L32)

*   **Header:**`// SPDX-License-Identifier: Apache-2.0` or `# SPDX-License-Identifier: Apache-2.0`.
*   **Copyright:**`Copyright (c) 2024 Tenstorrent AI ULC`.

### CI Workflows

Automated checks are triggered on every PR to `main` or `dev`:

*   **Frontend Linting:** Runs ESLint and checks for missing license headers.
*   **Backend License Check:** Uses the `check-copyright` tool to validate SPDX identifiers.

### QA Workflow to Code Entities

This diagram maps the QA process to the specific GitHub Action configuration files.

For details on setting up these checks locally, see **[Testing and CI](https://deepwiki.com/tenstorrent/tt-studio/8.2-testing-and-ci)**.

Sources: [CONTRIBUTING.md 30-33](https://github.com/tenstorrent/tt-studio/blob/a6d347af/CONTRIBUTING.md?plain=1#L30-L33)[.github/workflows/frontend-lint-license-checker.yml 1-10](https://github.com/tenstorrent/tt-studio/blob/a6d347af/.github/workflows/frontend-lint-license-checker.yml#L1-L10)[.github/workflows/backend-license-checker.yml 1-10](https://github.com/tenstorrent/tt-studio/blob/a6d347af/.github/workflows/backend-license-checker.yml#L1-L10)[check_copyright_config.yaml 1-5](https://github.com/tenstorrent/tt-studio/blob/a6d347af/check_copyright_config.yaml#L1-L5)

This wiki is featured in the [repository](https://github.com/tenstorrent/tt-studio/blob/main/README.md)

Dismiss
Refresh this wiki

Enter email to refresh
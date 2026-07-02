---
project: ttsim
github: https://github.com/tenstorrent/ttsim
deepwiki: https://deepwiki.com/tenstorrent/ttsim/5-contributing
---

# Contributing

Relevant source files
*   [CONTRIBUTING.md](https://github.com/tenstorrent/ttsim/blob/67b1733e/CONTRIBUTING.md?plain=1)
*   [README.md](https://github.com/tenstorrent/ttsim/blob/67b1733e/README.md?plain=1)

This page explains how external developers can participate in the ttsim project. The ttsim project uses a **unidirectional contribution model**: the community provides feedback and reports issues, while code development occurs in an internal Tenstorrent repository. This differs from typical open-source projects that accept pull requests.

For detailed information about specific contribution activities, see:

*   **Contribution Process**: [5.1](https://deepwiki.com/tenstorrent/ttsim/5.1-contribution-process) - Understanding the issues-only model and internal development workflow
*   **Reporting Issues**: [5.2](https://deepwiki.com/tenstorrent/ttsim/5.2-reporting-issues) - How to file effective bug reports and feature requests
*   **Security Vulnerabilities**: [5.3](https://deepwiki.com/tenstorrent/ttsim/5.3-security-vulnerabilities) - Private reporting procedures for security issues

For community standards and governance policies, see [Community & Governance](https://deepwiki.com/tenstorrent/ttsim/6-community-and-governance).

* * *

## Overview of the Contribution Model

The ttsim project follows an **issues-only contribution model** where:

*   **Pull requests are not accepted** and will remain closed even after source code is released
*   **GitHub issues are the primary contribution channel** for bug reports, feature requests, and questions
*   **Development occurs internally** at Tenstorrent with filtered releases to the public repository
*   **Binary releases are provided** via GitHub Releases, with source code planned for future release

This model exists because the public repository at `github.com/tenstorrent/ttsim` is a **filtered export** from an internal development repository. The public view is a derived artifact, not the source of truth, which makes the standard pull request workflow incompatible with the project structure.

**Sources:**[CONTRIBUTING.md 4-5](https://github.com/tenstorrent/ttsim/blob/67b1733e/CONTRIBUTING.md?plain=1#L4-L5)[CONTRIBUTING.md 31-34](https://github.com/tenstorrent/ttsim/blob/67b1733e/CONTRIBUTING.md?plain=1#L31-L34)[README.md 107-108](https://github.com/tenstorrent/ttsim/blob/67b1733e/README.md?plain=1#L107-L108)

* * *

## Contribution Channels

The ttsim project provides three distinct channels for community participation, each designed for different types of contributions:

| Channel | Purpose | Visibility | Contact Method |
| --- | --- | --- | --- |
| **GitHub Issues** | Bug reports, feature requests, questions, feedback | Public | [github.com/tenstorrent/ttsim/issues](https://github.com/tenstorrent/ttsim/blob/67b1733e/github.com/tenstorrent/ttsim/issues) |
| **GitHub Security Advisory** | Security vulnerabilities, exploits | Private | GitHub's security advisory feature |
| **Email** | Code of Conduct violations | Private | [opensource@tenstorrent.com](mailto:opensource@tenstorrent.com) |

### GitHub Issues

Use GitHub issues for:

*   Reporting bugs in simulator behavior
*   Requesting new features or improvements
*   Asking questions about usage or configuration
*   Providing general feedback

Issues are **public** and searchable. Before filing a new issue, search existing issues to avoid duplicates.

### GitHub Security Advisory

Use GitHub's security advisory feature for:

*   Security vulnerabilities
*   Potential exploits
*   Privacy concerns

Security reports are **private** and handled by Tenstorrent's security team. See [Security Vulnerabilities](https://deepwiki.com/tenstorrent/ttsim/5.3-security-vulnerabilities) for detailed reporting procedures.

### Email Contact

Use `opensource@tenstorrent.com` for:

*   Reporting Code of Conduct violations
*   Other confidential matters

**Sources:**[CONTRIBUTING.md 13-29](https://github.com/tenstorrent/ttsim/blob/67b1733e/CONTRIBUTING.md?plain=1#L13-L29)[CONTRIBUTING.md 8](https://github.com/tenstorrent/ttsim/blob/67b1733e/CONTRIBUTING.md?plain=1#L8-L8)[CONTRIBUTING.md 10-11](https://github.com/tenstorrent/ttsim/blob/67b1733e/CONTRIBUTING.md?plain=1#L10-L11)

* * *

## Information Flow Architecture

The following diagram illustrates how information and code flow through the ttsim ecosystem:

**Contribution Flow: External Community to Internal Development**

**Key observations:**

*   **One-way code flow**: Code flows from internal repository → public releases only
*   **No reverse integration**: Community cannot submit code changes via pull requests
*   **Filtered release process**: Public repository receives exports from internal development
*   **Multiple feedback channels**: Three distinct paths for different types of contributions

**Sources:**[CONTRIBUTING.md 31-34](https://github.com/tenstorrent/ttsim/blob/67b1733e/CONTRIBUTING.md?plain=1#L31-L34)[README.md 12-15](https://github.com/tenstorrent/ttsim/blob/67b1733e/README.md?plain=1#L12-L15)[README.md 107-108](https://github.com/tenstorrent/ttsim/blob/67b1733e/README.md?plain=1#L107-L108)

* * *

## Repository Structure Comparison

Understanding the relationship between the public and internal repositories clarifies why pull requests cannot be accepted:

**Public vs Internal Repository Architecture**

**Why pull requests are not accepted:**

1.   **Public repository is read-only artifact**: The public repository is a release target, not a development workspace
2.   **Filtered content**: Only approved portions of the internal codebase are exported
3.   **No bi-directional sync**: Changes to public repository cannot be merged back to internal repository
4.   **Build process**: Binaries are compiled from internal source, not public repository

This architecture will remain even after source code is released, as the public source will be a filtered subset.

**Sources:**[CONTRIBUTING.md 31-34](https://github.com/tenstorrent/ttsim/blob/67b1733e/CONTRIBUTING.md?plain=1#L31-L34)[README.md 12-15](https://github.com/tenstorrent/ttsim/blob/67b1733e/README.md?plain=1#L12-L15)

* * *

## Contribution Lifecycle

When you contribute feedback through GitHub issues, it follows this lifecycle:

**Issue Handling Workflow**

**Timeline expectations:**

*   **Triage**: Issues are reviewed regularly by the Tenstorrent team
*   **Implementation**: Depends on priority, complexity, and team capacity
*   **Release**: Coordinated with internal release schedule
*   **Updates**: Team may request additional information or clarification during any phase

**Sources:**[CONTRIBUTING.md 15-29](https://github.com/tenstorrent/ttsim/blob/67b1733e/CONTRIBUTING.md?plain=1#L15-L29)[README.md 111-114](https://github.com/tenstorrent/ttsim/blob/67b1733e/README.md?plain=1#L111-L114)

* * *

## What to Include in Issues

When filing a GitHub issue, include the following information to help the team understand and reproduce your concern:

### For Bug Reports

| Field | Description |
| --- | --- |
| **Clear description** | What went wrong? What is the impact? |
| **Steps to reproduce** | Exact commands and configuration needed to trigger the bug |
| **Expected behavior** | What should have happened? |
| **Actual behavior** | What actually happened? |
| **System information** | OS version, CPU architecture, TT-Metalium version |
| **ttsim version** | Which release version are you using? |
| **Environment variables** | Values of `TT_METAL_HOME`, `TT_METAL_SIMULATOR`, `TT_METAL_SLOW_DISPATCH_MODE` |
| **SOC descriptor** | Which SOC descriptor file are you using? |
| **Error messages** | Complete error output, including error category (e.g., UnimplementedFunctionality) |

### For Feature Requests

| Field | Description |
| --- | --- |
| **Use case** | What problem are you trying to solve? |
| **Proposed solution** | How would you like ttsim to behave? |
| **Alternatives considered** | What workarounds have you tried? |
| **Impact** | How would this improvement help your workflow? |

### For Questions

| Field | Description |
| --- | --- |
| **Context** | What are you trying to accomplish? |
| **What you've tried** | What approaches have you already attempted? |
| **Specific question** | What specific information do you need? |

**Sources:**[CONTRIBUTING.md 22-26](https://github.com/tenstorrent/ttsim/blob/67b1733e/CONTRIBUTING.md?plain=1#L22-L26)

* * *

## Code of Conduct

All contributors must adhere to the Contributor Covenant [Code of Conduct](https://github.com/tenstorrent/ttsim/blob/67b1733e/Code%20of%20Conduct) This includes:

*   Using welcoming and inclusive language
*   Being respectful of differing viewpoints and experiences
*   Gracefully accepting constructive criticism
*   Focusing on what is best for the community
*   Showing empathy towards other community members

**Reporting violations:**

If you witness or experience unacceptable behavior, report it to `opensource@tenstorrent.com`. All reports will be handled confidentially by the Tenstorrent Code of Conduct enforcement team.

**Sources:**[CONTRIBUTING.md 7-8](https://github.com/tenstorrent/ttsim/blob/67b1733e/CONTRIBUTING.md?plain=1#L7-L8)

* * *

## Licensing of Contributions

By contributing to ttsim through GitHub issues or other channels, you agree that:

*   Your **feedback and suggestions** are provided voluntarily without expectation of compensation
*   Any ideas or suggestions you provide may be incorporated into the project under the **Apache License 2.0**
*   You retain no intellectual property rights to general ideas or feedback shared through public channels
*   The ttsim project remains under **Tenstorrent AI ULC copyright**

This is not a contributor license agreement (CLA) since no code contributions are accepted. It simply clarifies that public feedback can be used to improve the project.

**Sources:**[CONTRIBUTING.md 39-40](https://github.com/tenstorrent/ttsim/blob/67b1733e/CONTRIBUTING.md?plain=1#L39-L40)[LICENSE](https://github.com/tenstorrent/ttsim/blob/67b1733e/LICENSE)

* * *

## File References

The contribution model is documented in the following files:

| File | Purpose |
| --- | --- |
| [CONTRIBUTING.md 1-41](https://github.com/tenstorrent/ttsim/blob/67b1733e/CONTRIBUTING.md?plain=1#L1-L41) | Primary contribution guidelines |
| [README.md 104-116](https://github.com/tenstorrent/ttsim/blob/67b1733e/README.md?plain=1#L104-L116) | Contributing section with quick reference |
| [CODE_OF_CONDUCT.md](https://github.com/tenstorrent/ttsim/blob/67b1733e/CODE_OF_CONDUCT.md?plain=1) | Community behavior standards |
| [SECURITY.md](https://github.com/tenstorrent/ttsim/blob/67b1733e/SECURITY.md?plain=1) | Security vulnerability reporting procedures |
| [LICENSE](https://github.com/tenstorrent/ttsim/blob/67b1733e/LICENSE) | Apache License 2.0 terms |

**Sources:**[CONTRIBUTING.md](https://github.com/tenstorrent/ttsim/blob/67b1733e/CONTRIBUTING.md?plain=1)[README.md 104-116](https://github.com/tenstorrent/ttsim/blob/67b1733e/README.md?plain=1#L104-L116)

Dismiss
Refresh this wiki

Enter email to refresh
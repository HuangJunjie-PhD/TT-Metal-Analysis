---
project: tt-toplike
github: https://github.com/tenstorrent/tt-toplike
deepwiki: https://deepwiki.com/tenstorrent/tt-toplike/9-contributing-and-community
---

# Contributing and Community

Relevant source files
*   [CODE_OF_CONDUCT.md](https://github.com/tenstorrent/tt-toplike/blob/487bfe5a/CODE_OF_CONDUCT.md?plain=1)
*   [CONTRIBUTING.md](https://github.com/tenstorrent/tt-toplike/blob/487bfe5a/CONTRIBUTING.md?plain=1)
*   [LICENSE](https://github.com/tenstorrent/tt-toplike/blob/487bfe5a/LICENSE)
*   [LICENSE-DOCS](https://github.com/tenstorrent/tt-toplike/blob/487bfe5a/LICENSE-DOCS)
*   [NOTICE](https://github.com/tenstorrent/tt-toplike/blob/487bfe5a/NOTICE)
*   [SECURITY.md](https://github.com/tenstorrent/tt-toplike/blob/487bfe5a/SECURITY.md?plain=1)

This page outlines the standards and procedures for participating in the `tt-toplike` project. We welcome contributions that improve hardware telemetry visualization, whether through bug fixes, performance optimizations, or new visualization modes.

The project follows a structured contribution model centered on high-quality Rust code, comprehensive testing via mock backends, and strict adherence to the Contributor Covenant.

### Governance and Communication

The community is governed by a set of standards designed to ensure a welcoming and professional environment. Technical and social issues are managed through the following channels:

*   **Bug Reports & Features**: Managed via [GitHub Issues](https://github.com/tenstorrent/tt-toplike/blob/487bfe5a/GitHub%20Issues)[CONTRIBUTING.md 7-23](https://github.com/tenstorrent/tt-toplike/blob/487bfe5a/CONTRIBUTING.md?plain=1#L7-L23)
*   **Security Vulnerabilities**: Reported privately via the GitHub Security tab, not through public issues [SECURITY.md 3-12](https://github.com/tenstorrent/tt-toplike/blob/487bfe5a/SECURITY.md?plain=1#L3-L12)
*   **Governance Inquiries**: Directed to the Open Source Program Office at `ospo@tenstorrent.com`[CODE_OF_CONDUCT.md 39](https://github.com/tenstorrent/tt-toplike/blob/487bfe5a/CODE_OF_CONDUCT.md?plain=1#L39-L39)

## Contribution Workflow

Contributing to `tt-toplike` involves a standard fork-and-pull-request workflow. Because the project supports multiple hardware backends and UI frontends, contributors must be mindful of feature flags and environmental requirements during development.

### Technical Standards

*   **Coding Style**: All code must follow standard Rust formatting via `cargo fmt` and pass `cargo clippy` without warnings [CONTRIBUTING.md 70-71](https://github.com/tenstorrent/tt-toplike/blob/487bfe5a/CONTRIBUTING.md?plain=1#L70-L71)
*   **Licensing**: New source files must include SPDX headers for Apache-2.0 [CONTRIBUTING.md 72-76](https://github.com/tenstorrent/tt-toplike/blob/487bfe5a/CONTRIBUTING.md?plain=1#L72-L76)
*   **Testing**: Changes should be verified using the Mock backend (e.g., `cargo run -- --mock --mock-devices 4`) to simulate hardware without requiring physical Tenstorrent devices [CONTRIBUTING.md 79-83](https://github.com/tenstorrent/tt-toplike/blob/487bfe5a/CONTRIBUTING.md?plain=1#L79-L83)

### Build Configuration

The project uses mutually exclusive or environment-specific features. For example, the `luwen-backend` requires specific kernel modules and is not included in default builds [CONTRIBUTING.md 57-60](https://github.com/tenstorrent/tt-toplike/blob/487bfe5a/CONTRIBUTING.md?plain=1#L57-L60)

For details on managing these builds and using helper scripts, see **[Development Workflow](https://deepwiki.com/tenstorrent/tt-toplike/9.1-development-workflow)**.

### Community Structure and Entity Mapping

The following diagram maps the conceptual community processes to the specific files and contact points defined in the repository.

**Community Engagement Map**

Sources: [CONTRIBUTING.md 5-39](https://github.com/tenstorrent/tt-toplike/blob/487bfe5a/CONTRIBUTING.md?plain=1#L5-L39)[SECURITY.md 1-23](https://github.com/tenstorrent/tt-toplike/blob/487bfe5a/SECURITY.md?plain=1#L1-L23)[CODE_OF_CONDUCT.md 37-43](https://github.com/tenstorrent/tt-toplike/blob/487bfe5a/CODE_OF_CONDUCT.md?plain=1#L37-L43)

## Code of Conduct and Licensing

All participants are expected to uphold the **Contributor Covenant**, which is enforced by community leaders to prevent harassment and maintain professional standards [CODE_OF_CONDUCT.md 1-32](https://github.com/tenstorrent/tt-toplike/blob/487bfe5a/CODE_OF_CONDUCT.md?plain=1#L1-L32)

### Licensing Model

The project utilizes a dual-license approach depending on the content type:

*   **Software**: Licensed under the **Apache License 2.0**[LICENSE 2-5](https://github.com/tenstorrent/tt-toplike/blob/487bfe5a/LICENSE#L2-L5)
*   **Documentation**: Licensed under **Creative Commons Attribution 4.0 International**[LICENSE-DOCS 7](https://github.com/tenstorrent/tt-toplike/blob/487bfe5a/LICENSE-DOCS#L7-L7)

Contributors must ensure that their submissions do not violate third-party licenses and that any necessary attributions are added to the `NOTICE` file [LICENSE 107-114](https://github.com/tenstorrent/tt-toplike/blob/487bfe5a/LICENSE#L107-L114)

For details on enforcement tiers and specific license terms, see **[Code of Conduct and Licensing](https://deepwiki.com/tenstorrent/tt-toplike/9.2-code-of-conduct-and-licensing)**.

### Development Lifecycle Diagram

The following diagram illustrates the relationship between code standards, the build system, and the contribution gatekeeping process.

**Contribution Pipeline**

Sources: [CONTRIBUTING.md 25-39](https://github.com/tenstorrent/tt-toplike/blob/487bfe5a/CONTRIBUTING.md?plain=1#L25-L39)[CONTRIBUTING.md 68-83](https://github.com/tenstorrent/tt-toplike/blob/487bfe5a/CONTRIBUTING.md?plain=1#L68-L83)

## Sub-Pages

*   **[Development Workflow](https://deepwiki.com/tenstorrent/tt-toplike/9.1-development-workflow)**: Detailed guide on building with feature flags (TUI vs GUI), running tests, using the vendor directory for offline builds, and utilizing the `test-modes.sh` scripts.
*   **[Code of Conduct and Licensing](https://deepwiki.com/tenstorrent/tt-toplike/9.2-code-of-conduct-and-licensing)**: Deep dive into the enforcement guidelines (Correction, Warning, Temporary/Permanent Ban) and the full text of the Apache 2.0 and CC-BY-4.0 licenses.

Sources: [CONTRIBUTING.md 1-98](https://github.com/tenstorrent/tt-toplike/blob/487bfe5a/CONTRIBUTING.md?plain=1#L1-L98)[CODE_OF_CONDUCT.md 1-78](https://github.com/tenstorrent/tt-toplike/blob/487bfe5a/CODE_OF_CONDUCT.md?plain=1#L1-L78)[LICENSE 1-132](https://github.com/tenstorrent/tt-toplike/blob/487bfe5a/LICENSE#L1-L132)[SECURITY.md 1-33](https://github.com/tenstorrent/tt-toplike/blob/487bfe5a/SECURITY.md?plain=1#L1-L33)[NOTICE 1-20](https://github.com/tenstorrent/tt-toplike/blob/487bfe5a/NOTICE#L1-L20)

Dismiss
Refresh this wiki

Enter email to refresh
---
project: ttsim
github: https://github.com/tenstorrent/ttsim
deepwiki: https://deepwiki.com/tenstorrent/ttsim/6-community-and-governance
---

# Community & Governance

Relevant source files
*   [CODE_OF_CONDUCT.md](https://github.com/tenstorrent/ttsim/blob/67b1733e/CODE_OF_CONDUCT.md?plain=1)
*   [LICENSE](https://github.com/tenstorrent/ttsim/blob/67b1733e/LICENSE)

## Purpose and Scope

This document provides an overview of the policies, standards, and legal framework that govern the ttsim project and its community. It establishes the foundational rules for how the project operates, how community members should interact, and what legal rights and obligations exist for users and contributors.

For specific information about:

*   Contributing code feedback and bug reports, see [Contributing](https://deepwiki.com/tenstorrent/ttsim/5-contributing)
*   Reporting security vulnerabilities, see [Security Vulnerabilities](https://deepwiki.com/tenstorrent/ttsim/5.3-security-vulnerabilities)
*   Detailed code contribution processes, see [Contribution Process](https://deepwiki.com/tenstorrent/ttsim/5.1-contribution-process)

This page focuses on the governance structure, community standards, and legal framework. Detailed procedures for each policy area are covered in the subsections.

## Governance Structure

The ttsim project operates under a governance model that balances open-source principles with Tenstorrent's internal development processes. The governance structure consists of three primary policy domains, each with specific enforcement mechanisms and contact channels.

**Governance Structure Diagram**: Shows how policy documents connect to enforcement mechanisms and community actors.

The governance framework is intentionally lightweight, consisting of five policy documents that establish clear boundaries and procedures. Unlike traditional open-source projects with community maintainers and democratic decision-making, ttsim uses a corporate-maintained model where Tenstorrent retains control over the codebase while accepting community feedback through structured channels.

**Sources:**[CODE_OF_CONDUCT.md 1-134](https://github.com/tenstorrent/ttsim/blob/67b1733e/CODE_OF_CONDUCT.md?plain=1#L1-L134)[LICENSE 1-202](https://github.com/tenstorrent/ttsim/blob/67b1733e/LICENSE#L1-L202)

## Policy Domains

The ttsim project governance is organized into three distinct policy domains, each addressing different aspects of project operation and community interaction.

### Policy Domain Overview

| Domain | Primary Document | Scope | Enforcement Contact |
| --- | --- | --- | --- |
| Community Conduct | [CODE_OF_CONDUCT.md 1-134](https://github.com/tenstorrent/ttsim/blob/67b1733e/CODE_OF_CONDUCT.md?plain=1#L1-L134) | Behavioral standards for all community interactions | [opensource@tenstorrent.com](mailto:opensource@tenstorrent.com) |
| Software Licensing | [LICENSE 1-202](https://github.com/tenstorrent/ttsim/blob/67b1733e/LICENSE#L1-L202) | Legal rights to use, modify, and distribute ttsim | Apache 2.0 terms |
| Copyright & Attribution | NOTICE | Ownership and attribution requirements | Tenstorrent AI ULC |
| Security | SECURITY.md | Vulnerability disclosure and response | GitHub Security Advisory |
| Contributions | CONTRIBUTING.md | How community feedback is processed | GitHub Issues only |

**Sources:**[CODE_OF_CONDUCT.md 1-134](https://github.com/tenstorrent/ttsim/blob/67b1733e/CODE_OF_CONDUCT.md?plain=1#L1-L134)[LICENSE 1-202](https://github.com/tenstorrent/ttsim/blob/67b1733e/LICENSE#L1-L202)

### Code of Conduct

The project adopts the **Contributor Covenant version 2.1** as its Code of Conduct. This establishes behavioral expectations for all community spaces, including GitHub issues, discussions, and any official communication channels.

**Code of Conduct Enforcement Flow**: Shows the graduated response system for conduct violations.

The Code of Conduct applies in all community spaces and when officially representing the project. Community leaders at Tenstorrent are responsible for enforcement decisions.

For detailed information about behavioral expectations and enforcement procedures, see [Code of Conduct](https://deepwiki.com/tenstorrent/ttsim/6.1-code-of-conduct).

**Sources:**[CODE_OF_CONDUCT.md 1-134](https://github.com/tenstorrent/ttsim/blob/67b1733e/CODE_OF_CONDUCT.md?plain=1#L1-L134)

### Licensing Framework

The ttsim software is released under the **Apache License 2.0**, a permissive open-source license that grants broad rights to use, modify, and distribute the software. The licensing framework distinguishes between software rights and hardware/IP rights.

**Licensing Scope Diagram**: Illustrates the distinction between software and hardware/IP licensing.

The Apache 2.0 license grants copyright and patent licenses for the software but explicitly does not grant trademark rights [LICENSE 138-141](https://github.com/tenstorrent/ttsim/blob/67b1733e/LICENSE#L138-L141) Additionally, the license includes a patent retaliation clause that terminates patent rights if you sue over patent infringement [LICENSE 73-87](https://github.com/tenstorrent/ttsim/blob/67b1733e/LICENSE#L73-L87)

For complete licensing terms and the hardware/IP distinction, see [Licensing](https://deepwiki.com/tenstorrent/ttsim/6.2-licensing).

**Sources:**[LICENSE 1-202](https://github.com/tenstorrent/ttsim/blob/67b1733e/LICENSE#L1-L202)

### Copyright and Attribution

All copyright in ttsim is owned by **Tenstorrent AI ULC**. The NOTICE file contains required attribution notices that must be preserved in derivative works.

Key copyright principles:

*   Tenstorrent AI ULC holds all copyright to ttsim code
*   Apache 2.0 license grants users specific rights without transferring ownership
*   Derivative works must preserve copyright notices [LICENSE 100-104](https://github.com/tenstorrent/ttsim/blob/67b1733e/LICENSE#L100-L104)
*   Users may add their own copyright statements to their modifications [LICENSE 123-128](https://github.com/tenstorrent/ttsim/blob/67b1733e/LICENSE#L123-L128)

For detailed attribution requirements, see [Copyright & Attribution](https://deepwiki.com/tenstorrent/ttsim/6.3-copyright-and-attribution).

**Sources:**[LICENSE 1-202](https://github.com/tenstorrent/ttsim/blob/67b1733e/LICENSE#L1-L202)

## Enforcement Mechanisms

The ttsim project uses differentiated enforcement mechanisms based on the type of issue being reported. This ensures appropriate handling of technical issues, security vulnerabilities, and conduct violations.

### Contact Channels

**Issue Routing Diagram**: Shows how different issue types are routed to appropriate handlers.

### Enforcement Matrix

| Issue Type | Reporting Channel | Response Team | Expected Timeline | Public/Private |
| --- | --- | --- | --- | --- |
| Bug Reports | GitHub Issues | Development Team | Best effort | Public |
| Feature Requests | GitHub Issues | Development Team | Best effort | Public |
| Security Vulnerabilities | GitHub Security Advisory | Security Team | Coordinated disclosure | Private initially |
| Code of Conduct Violations | [opensource@tenstorrent.com](mailto:opensource@tenstorrent.com) | Community Leaders | Prompt and fair review | Private |
| License Questions | GitHub Issues | Development Team | Best effort | Public |

**Sources:**[CODE_OF_CONDUCT.md 59-68](https://github.com/tenstorrent/ttsim/blob/67b1733e/CODE_OF_CONDUCT.md?plain=1#L59-L68)

### Code of Conduct Enforcement

The Code of Conduct defines a four-level enforcement ladder based on the severity and pattern of violations:

1.   **Correction**[CODE_OF_CONDUCT.md 75-82](https://github.com/tenstorrent/ttsim/blob/67b1733e/CODE_OF_CONDUCT.md?plain=1#L75-L82): Private written warning for inappropriate language or unprofessional behavior
2.   **Warning**[CODE_OF_CONDUCT.md 84-94](https://github.com/tenstorrent/ttsim/blob/67b1733e/CODE_OF_CONDUCT.md?plain=1#L84-L94): Consequences for continued behavior, including interaction restrictions
3.   **Temporary Ban**[CODE_OF_CONDUCT.md 96-105](https://github.com/tenstorrent/ttsim/blob/67b1733e/CODE_OF_CONDUCT.md?plain=1#L96-L105): Time-limited exclusion from community interaction
4.   **Permanent Ban**[CODE_OF_CONDUCT.md 107-114](https://github.com/tenstorrent/ttsim/blob/67b1733e/CODE_OF_CONDUCT.md?plain=1#L107-L114): Complete removal from community for pattern violations or serious offenses

Community leaders are obligated to respect the privacy and security of anyone reporting an incident [CODE_OF_CONDUCT.md 67-68](https://github.com/tenstorrent/ttsim/blob/67b1733e/CODE_OF_CONDUCT.md?plain=1#L67-L68) All complaints are reviewed and investigated promptly and fairly [CODE_OF_CONDUCT.md 65](https://github.com/tenstorrent/ttsim/blob/67b1733e/CODE_OF_CONDUCT.md?plain=1#L65-L65)

**Sources:**[CODE_OF_CONDUCT.md 59-114](https://github.com/tenstorrent/ttsim/blob/67b1733e/CODE_OF_CONDUCT.md?plain=1#L59-L114)

## Relationship to Contribution Model

The ttsim governance structure reflects its unique contribution model. Unlike traditional open-source projects that accept pull requests, ttsim operates as a **read-only distribution point** for software developed internally at Tenstorrent.

**Contribution Flow Diagram**: Illustrates the one-way flow from internal development to public release.

This governance model is intentional and serves several purposes:

1.   **Quality Control**: All code is developed, reviewed, and tested using Tenstorrent's internal processes before public release
2.   **IP Management**: Maintains clear intellectual property boundaries and contribution provenance
3.   **Security**: Reduces attack surface by limiting who can inject code into the build pipeline
4.   **Simplicity**: No need for complex CLA (Contributor License Agreement) processes or external maintainer coordination

The governance documents support this model by:

*   Establishing clear behavioral expectations through the Code of Conduct
*   Granting permissive usage rights through Apache 2.0
*   Providing structured feedback channels through GitHub Issues
*   Maintaining Tenstorrent's copyright ownership

For details on how to provide feedback within this model, see [Contribution Process](https://deepwiki.com/tenstorrent/ttsim/5.1-contribution-process) and [Reporting Issues](https://deepwiki.com/tenstorrent/ttsim/5.2-reporting-issues).

**Sources:**[CODE_OF_CONDUCT.md 1-134](https://github.com/tenstorrent/ttsim/blob/67b1733e/CODE_OF_CONDUCT.md?plain=1#L1-L134)[LICENSE 1-202](https://github.com/tenstorrent/ttsim/blob/67b1733e/LICENSE#L1-L202)

## Community Scope

The Code of Conduct applies in all community spaces and when officially representing the project [CODE_OF_CONDUCT.md 51-57](https://github.com/tenstorrent/ttsim/blob/67b1733e/CODE_OF_CONDUCT.md?plain=1#L51-L57) This includes:

**Community Spaces:**

*   GitHub repository (issues, discussions, comments)
*   Official email communications ([opensource@tenstorrent.com](mailto:opensource@tenstorrent.com))
*   Any official social media accounts
*   Online or offline events where individuals officially represent ttsim

**Official Representation:**

*   Using an official Tenstorrent email address in project context
*   Posting via official social media accounts
*   Acting as an appointed representative at events

The scope ensures that community standards apply wherever the project's reputation is at stake, while respecting that individuals have separate personal online identities.

**Sources:**[CODE_OF_CONDUCT.md 51-57](https://github.com/tenstorrent/ttsim/blob/67b1733e/CODE_OF_CONDUCT.md?plain=1#L51-L57)

## Summary

The ttsim governance framework consists of:

1.   **Community Conduct**: Contributor Covenant 2.1 with four-level enforcement ladder
2.   **Software Licensing**: Apache License 2.0 granting broad software usage rights
3.   **Copyright**: Tenstorrent AI ULC ownership with attribution requirements
4.   **Contribution Model**: Issues-only feedback, no direct code contributions
5.   **Security**: Private vulnerability disclosure through GitHub Security Advisory

All governance elements support the project's goal of providing a high-quality simulator while maintaining clear legal boundaries and professional community standards. The governance structure is intentionally lightweight, focusing on essential policies that enable productive community interaction without imposing complex bureaucracy.

For implementation details of each governance area, see the subsections:

*   [Code of Conduct](https://deepwiki.com/tenstorrent/ttsim/6.1-code-of-conduct)
*   [Licensing](https://deepwiki.com/tenstorrent/ttsim/6.2-licensing)
*   [Copyright & Attribution](https://deepwiki.com/tenstorrent/ttsim/6.3-copyright-and-attribution)

**Sources:**[CODE_OF_CONDUCT.md 1-134](https://github.com/tenstorrent/ttsim/blob/67b1733e/CODE_OF_CONDUCT.md?plain=1#L1-L134)[LICENSE 1-202](https://github.com/tenstorrent/ttsim/blob/67b1733e/LICENSE#L1-L202)

Dismiss
Refresh this wiki

Enter email to refresh
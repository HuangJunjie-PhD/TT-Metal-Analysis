---
project: tt-firmware
github: https://github.com/tenstorrent/tt-firmware
deepwiki: https://deepwiki.com/tenstorrent/tt-firmware/5-development-and-contribution)
---

# Development and Contribution

Relevant source files
*   [.github/workflows/community-issue-tagging.yml](https://github.com/tenstorrent/tt-firmware/blob/c9a325bb/.github/workflows/community-issue-tagging.yml)
*   [README.md](https://github.com/tenstorrent/tt-firmware/blob/c9a325bb/README.md?plain=1)

This page provides information for developers and contributors to the tt-firmware repository. It covers contribution guidelines, experimental firmware bundles, GitHub workflows, and how to engage with the project community. For information about firmware bundle formats and content, see [Firmware Bundles](https://deepwiki.com/tenstorrent/tt-firmware/3-firmware-bundles).

## Contribution Model Overview

Due to the binary nature of the tt-firmware repository, the contribution model differs from typical open-source projects. The firmware contains third-party IP which prevents the distribution of source code, as explained in the README:

Sources: [README.md 15-19](https://github.com/tenstorrent/tt-firmware/blob/c9a325bb/README.md?plain=1#L15-L19)

## Binary Release Limitations

As stated in the README, Tenstorrent products incorporate third-party intellectual property, which restricts the distribution of source code. Contribution to the firmware source code itself is limited because:

1.   The firmware contains third-party IP with agreements preventing source code distribution
2.   Disentangling proprietary components would require significant rewriting
3.   Tenstorrent has chosen to focus on making portions of future firmware open-source rather than rewriting existing code

For users interested in how applications interact with the firmware, the Tenstorrent User Mode Driver (UMD) repository provides relevant information.

Sources: [README.md 15-21](https://github.com/tenstorrent/tt-firmware/blob/c9a325bb/README.md?plain=1#L15-L21)

## Working with Experimental Firmware Bundles

The repository contains an "experiments" directory with experimental firmware bundles. These are based on the latest available firmware with modifications to address specific issues. Contributors can work with these experimental bundles or propose new ones.

Diagram: Experimental Firmware Bundle Workflow

Sources: [README.md 62-65](https://github.com/tenstorrent/tt-firmware/blob/c9a325bb/README.md?plain=1#L62-L65)

## GitHub Workflows

The repository utilizes GitHub workflows to manage community contributions and engagement. One key workflow is the Community Issue/PR Labeling workflow, which automatically processes new issues and pull requests.

Diagram: GitHub Community Issue/PR Workflow

Sources: [.github/workflows/community-issue-tagging.yml 1-12](https://github.com/tenstorrent/tt-firmware/blob/c9a325bb/.github/workflows/community-issue-tagging.yml#L1-L12)

### Community Issue Tagging

When a community member opens an issue or pull request, the repository automatically processes it using a centralized workflow:

1.   The `community-issue-tagging.yml` workflow is triggered when issues are opened or pull requests are created
2.   This workflow calls the centralized workflow `on-community-issue.yml` from the `tenstorrent/tt-github-actions` repository
3.   The centralized workflow applies appropriate labels to categorize and track the issue/PR

This automated tagging helps ensure community contributions are properly categorized and addressed by the Tenstorrent team.

Sources: [.github/workflows/community-issue-tagging.yml 1-12](https://github.com/tenstorrent/tt-firmware/blob/c9a325bb/.github/workflows/community-issue-tagging.yml#L1-L12)

## Contribution Guidelines

While direct firmware source code contributions are limited due to the binary-only distribution model, community members can contribute in several ways:

1.   **Issue Reporting**: Report bugs, performance issues, or compatibility problems with specific hardware
2.   **Feature Requests**: Suggest new functionality or improvements to existing features
3.   **Experimental Bundles**: Work with and propose modifications to experimental firmware bundles
4.   **Documentation**: Contribute to improving documentation or providing usage examples
5.   **Testing**: Test firmware bundles across different configurations and report findings

Diagram: Contribution Pathways for tt-firmware

Sources: [README.md](https://github.com/tenstorrent/tt-firmware/blob/c9a325bb/README.md?plain=1)

## License and Redistribution

Contributions must comply with the license terms specified in the repository. As stated in the README, redistribution is permitted under certain conditions detailed in the LICENSE file. Contributors should review these terms before submitting any contributions.

Sources: [README.md 67-71](https://github.com/tenstorrent/tt-firmware/blob/c9a325bb/README.md?plain=1#L67-L71)

## Engaging with the Community

Contributors are encouraged to:

1.   Review existing issues before creating new ones
2.   Follow GitHub's best practices for issue reporting and PR submission
3.   Provide clear, concise descriptions of problems or proposed changes
4.   Include relevant system information when reporting issues
5.   Reference related issues or PRs when applicable

For firmware-specific technical questions, engaging through GitHub issues allows the community and Tenstorrent team to track and address concerns systematically.

Sources: [README.md](https://github.com/tenstorrent/tt-firmware/blob/c9a325bb/README.md?plain=1)

Dismiss
Refresh this wiki

Enter email to refresh
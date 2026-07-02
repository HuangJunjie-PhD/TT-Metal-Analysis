---
project: tt-github-actions
github: https://github.com/tenstorrent/tt-github-actions
deepwiki: https://deepwiki.com/tenstorrent/tt-github-actions/6-community-and-bounty-workflows
---

# Community & Bounty Workflows

Relevant source files
*   [.github/actions/collect_data/download_workflow_data.sh](https://github.com/tenstorrent/tt-github-actions/blob/3ecf8e49/.github/actions/collect_data/download_workflow_data.sh)
*   [.github/workflows/bounty-complete.yml](https://github.com/tenstorrent/tt-github-actions/blob/3ecf8e49/.github/workflows/bounty-complete.yml)
*   [.github/workflows/bounty-pr-review-reminder.yml](https://github.com/tenstorrent/tt-github-actions/blob/3ecf8e49/.github/workflows/bounty-pr-review-reminder.yml)
*   [.github/workflows/bounty-pr.yml](https://github.com/tenstorrent/tt-github-actions/blob/3ecf8e49/.github/workflows/bounty-pr.yml)
*   [.github/workflows/bounty-stale.yml](https://github.com/tenstorrent/tt-github-actions/blob/3ecf8e49/.github/workflows/bounty-stale.yml)
*   [.github/workflows/bounty-terms.yml](https://github.com/tenstorrent/tt-github-actions/blob/3ecf8e49/.github/workflows/bounty-terms.yml)
*   [.github/workflows/issues-community-autotag.yml](https://github.com/tenstorrent/tt-github-actions/blob/3ecf8e49/.github/workflows/issues-community-autotag.yml)
*   [.github/workflows/on-community-issue.yml](https://github.com/tenstorrent/tt-github-actions/blob/3ecf8e49/.github/workflows/on-community-issue.yml)

This section provides an overview of the reusable workflows designed to manage community engagement and the Tenstorrent Bounty Program. These workflows automate the lifecycle of external contributions, from initial membership detection and labeling to tracking bounty progress and facilitating final payments.

## Community Membership & Labeling

The community workflows are designed to distinguish between internal Tenstorrent organization members and external contributors. This distinction allows the maintainers to automatically apply a `community` label to issues and pull requests, ensuring that external feedback and contributions receive appropriate visibility and tracking.

The system utilizes two primary workflow files:

*   `on-community-issue.yml`: Performs membership detection by querying the GitHub API for organization membership status [[.github/workflows/on-community-issue.yml:39-54]](https://deepwiki.com/tenstorrent/tt-github-actions/6-community-and-bounty-workflows).
*   `issues-community-autotag.yml`: A comprehensive workflow that extracts metadata from the event (issue or PR), checks both organization membership and repository collaborator status, and ensures the `community` label exists before applying it [[.github/workflows/issues-community-autotag.yml:64-135]](https://deepwiki.com/tenstorrent/tt-github-actions/6-community-and-bounty-workflows).

For details, see [Community Membership Detection & Labeling](https://deepwiki.com/tenstorrent/tt-github-actions/6.1-community-membership-detection-and-labeling).

### Membership Detection Logic

The following diagram illustrates how the system determines if a user is a community member and how it interacts with the GitHub API.

**Community Detection Flow**

Sources: [[.github/workflows/on-community-issue.yml:39-54]](https://deepwiki.com/tenstorrent/tt-github-actions/6-community-and-bounty-workflows), [[.github/workflows/issues-community-autotag.yml:64-135]](https://deepwiki.com/tenstorrent/tt-github-actions/6-community-and-bounty-workflows)

* * *

## Bounty Program Workflows

The Bounty Program suite automates the administrative overhead of managing paid external contributions. These workflows monitor the status of issues and PRs tagged with the `bounty` label and trigger notifications or comments based on specific lifecycle events.

The suite includes:

*   **PR Acknowledgment**: `bounty-pr.yml` detects if a new PR is linked to a bounty issue via `closingIssuesReferences` and notifies the internal team on Slack [[.github/workflows/bounty-pr.yml:37-69]](https://deepwiki.com/tenstorrent/tt-github-actions/6-community-and-bounty-workflows).
*   **Terms Assignment**: `bounty-terms.yml` automatically comments on a bounty issue when a user is assigned, providing a link to the legal terms [[.github/workflows/bounty-terms.yml:14-33]](https://deepwiki.com/tenstorrent/tt-github-actions/6-community-and-bounty-workflows).
*   **Stale Tracking**: `bounty-stale.yml` uses the `actions/stale` action to warn contributors if a bounty issue has seen no activity for 14 days [[.github/workflows/bounty-stale.yml:16-29]](https://deepwiki.com/tenstorrent/tt-github-actions/6-community-and-bounty-workflows).
*   **Review Reminders**: `bounty-pr-review-reminder.yml` alerts the internal team if a bounty PR has been open for more than 24 hours without an assigned reviewer [[.github/workflows/bounty-pr-review-reminder.yml:66-107]](https://deepwiki.com/tenstorrent/tt-github-actions/6-community-and-bounty-workflows).
*   **Completion**: `bounty-complete.yml` provides instructions for payment processing once a bounty issue is closed [[.github/workflows/bounty-complete.yml:14-28]](https://deepwiki.com/tenstorrent/tt-github-actions/6-community-and-bounty-workflows).

For details, see [Bounty Program Workflows](https://deepwiki.com/tenstorrent/tt-github-actions/6.2-bounty-program-workflows).

### Bounty Lifecycle Automation

The following diagram maps the lifecycle stages to the specific workflow files responsible for automation.

**Bounty Workflow Entity Map**

Sources: [[.github/workflows/bounty-pr.yml:1-10]](https://deepwiki.com/tenstorrent/tt-github-actions/6-community-and-bounty-workflows), [[.github/workflows/bounty-complete.yml:1-10]](https://deepwiki.com/tenstorrent/tt-github-actions/6-community-and-bounty-workflows), [[.github/workflows/bounty-stale.yml:1-10]](https://deepwiki.com/tenstorrent/tt-github-actions/6-community-and-bounty-workflows), [[.github/workflows/bounty-terms.yml:1-10]](https://deepwiki.com/tenstorrent/tt-github-actions/6-community-and-bounty-workflows)

### Key Workflow Metadata

| Workflow | Trigger Event | Key Action |
| --- | --- | --- |
| `bounty-pr.yml` | `pull_request` | GraphQL query to check `closingIssuesReferences` for `bounty` label [[.github/workflows/bounty-pr.yml:37-69]](https://deepwiki.com/tenstorrent/tt-github-actions/6-community-and-bounty-workflows). |
| `bounty-terms.yml` | `issues` (assigned) | Posts `COMMENT` with link to `bounty_terms.html`[[.github/workflows/bounty-terms.yml:18-26]](https://deepwiki.com/tenstorrent/tt-github-actions/6-community-and-bounty-workflows). |
| `bounty-stale.yml` | `schedule` | Marks issues with `bounty` label as stale after 14 days [[.github/workflows/bounty-stale.yml:20-21]](https://deepwiki.com/tenstorrent/tt-github-actions/6-community-and-bounty-workflows). |
| `bounty-complete.yml` | `issues` (closed) | Directs user to email `cguerrero@tenstorrent.com` for payment [[.github/workflows/bounty-complete.yml:23-25]](https://deepwiki.com/tenstorrent/tt-github-actions/6-community-and-bounty-workflows). |

Sources: [[.github/workflows/bounty-pr.yml:37-69]](https://deepwiki.com/tenstorrent/tt-github-actions/6-community-and-bounty-workflows), [[.github/workflows/bounty-terms.yml:18-26]](https://deepwiki.com/tenstorrent/tt-github-actions/6-community-and-bounty-workflows), [[.github/workflows/bounty-stale.yml:20-21]](https://deepwiki.com/tenstorrent/tt-github-actions/6-community-and-bounty-workflows), [[.github/workflows/bounty-complete.yml:23-25]](https://deepwiki.com/tenstorrent/tt-github-actions/6-community-and-bounty-workflows)

Dismiss
Refresh this wiki

Enter email to refresh
---
project: tt-awesome
github: https://github.com/tenstorrent/tt-awesome
deepwiki: https://deepwiki.com/tenstorrent/tt-awesome/6-claude-code-plugin-and-add-project-skill
---

# Claude Code Plugin & Add-Project Skill

Relevant source files
*   [.claude-plugin/marketplace.json](https://github.com/tenstorrent/tt-awesome/blob/1d86dbe5/.claude-plugin/marketplace.json)
*   [.claude-plugin/plugin.json](https://github.com/tenstorrent/tt-awesome/blob/1d86dbe5/.claude-plugin/plugin.json)
*   [docs/superpowers/specs/2026-05-27-add-project-skill-design.md](https://github.com/tenstorrent/tt-awesome/blob/1d86dbe5/docs/superpowers/specs/2026-05-27-add-project-skill-design.md?plain=1)
*   [skills/add-project/SKILL.md](https://github.com/tenstorrent/tt-awesome/blob/1d86dbe5/skills/add-project/SKILL.md?plain=1)

The `tt-awesome` repository integrates with **Claude Code** to provide a conversational interface for contributors. This integration is defined via a plugin manifest and a specialized "skill" that automates the process of adding new projects to the ecosystem. By leveraging the `/tt-awesome:add-project` command, users can move from a project URL to a submitted entry (via GitHub Issue or Pull Request) through a guided, multi-phase dialogue.

### System Overview: Claude Code Integration

The integration consists of two main components: the plugin configuration which registers the extension with Claude Code, and the skill definition which contains the logic for the project submission workflow.

The plugin is registered via a manifest in the `.claude-plugin/` directory and is backed by a skill defined in Markdown format. This skill has access to local tools like `Bash`, `Read`, and `Write` to interact with the repository and the GitHub CLI (`gh`).

**Claude Code Skill Architecture**

**Sources:**

*   [.claude-plugin/plugin.json 1-7](https://github.com/tenstorrent/tt-awesome/blob/1d86dbe5/.claude-plugin/plugin.json#L1-L7)
*   [skills/add-project/SKILL.md 1-11](https://github.com/tenstorrent/tt-awesome/blob/1d86dbe5/skills/add-project/SKILL.md?plain=1#L1-L11)
*   [docs/superpowers/specs/2026-05-27-add-project-skill-design.md 33-43](https://github.com/tenstorrent/tt-awesome/blob/1d86dbe5/docs/superpowers/specs/2026-05-27-add-project-skill-design.md?plain=1#L33-L43)

* * *

### Plugin Configuration

The plugin is defined by a manifest that specifies the name, description, and author. It is bundled directly within the repository, allowing Claude Code to discover and load the `tt-awesome` specific capabilities when a user is working within the codebase.

For details, see [Plugin Configuration](https://deepwiki.com/tenstorrent/tt-awesome/6.1-plugin-configuration).

**Sources:**

*   [.claude-plugin/plugin.json 1-7](https://github.com/tenstorrent/tt-awesome/blob/1d86dbe5/.claude-plugin/plugin.json#L1-L7)
*   [.claude-plugin/marketplace.json 1-21](https://github.com/tenstorrent/tt-awesome/blob/1d86dbe5/.claude-plugin/marketplace.json#L1-L21)

* * *

### The /tt-awesome:add-project Skill

The core of the integration is the `add-project` skill. It implements a three-phase workflow designed to ensure data quality and provide multiple contribution paths depending on the user's permissions and environment.

#### Phase 1: Data Gathering & Auto-fetch

The skill gathers required fields (Name, URL, Description, Categories, Affiliation) and optional metadata. A key feature is the **GitHub meta auto-fetch**: once a repository URL is provided, the skill uses the GitHub API to automatically retrieve the project's description, primary language, and license.

#### Phase 2: Preview & Validation

The skill generates a JSON representation of the entry and presents it to the user for confirmation. This phase ensures that the `id` generation logic (slugifying the project name) matches the behavior of the `scripts/issue_to_entry.py` script used by the backend automation.

#### Phase 3: Contribution Paths

Users can choose from three exit paths:

1.   **Path A (GitHub Issue):** Formats the data into a GitHub Issue using the template expected by the automation pipeline.
2.   **Path B (Local JSON):** Writes the file to the local `entries/` directory and provides git commands for manual submission.
3.   **Path C (Auto PR):** Automates the entire process of branch creation, file writing, and PR submission.

For details, see [Add-Project Skill Walkthrough](https://deepwiki.com/tenstorrent/tt-awesome/6.2-add-project-skill-walkthrough).

**Skill Workflow Diagram**

**Sources:**

*   [skills/add-project/SKILL.md 22-151](https://github.com/tenstorrent/tt-awesome/blob/1d86dbe5/skills/add-project/SKILL.md?plain=1#L22-L151)
*   [docs/superpowers/specs/2026-05-27-add-project-skill-design.md 71-144](https://github.com/tenstorrent/tt-awesome/blob/1d86dbe5/docs/superpowers/specs/2026-05-27-add-project-skill-design.md?plain=1#L71-L144)
*   [scripts/issue_to_entry.py (referenced for slugify logic)](https://github.com/tenstorrent/tt-awesome/blob/1d86dbe5/scripts/issue_to_entry.py%20(referenced%20for%20slugify%20logic))

Dismiss
Refresh this wiki

Enter email to refresh
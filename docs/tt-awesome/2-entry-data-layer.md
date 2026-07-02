---
project: tt-awesome
github: https://github.com/tenstorrent/tt-awesome
deepwiki: https://deepwiki.com/tenstorrent/tt-awesome/2-entry-data-layer
---

# Entry Data Layer

Relevant source files
*   [entries/agents/ttvt-ai-agents.json](https://github.com/tenstorrent/tt-awesome/blob/1d86dbe5/entries/agents/ttvt-ai-agents.json)
*   [entries/ai-models/tt-blacksmith.json](https://github.com/tenstorrent/tt-awesome/blob/1d86dbe5/entries/ai-models/tt-blacksmith.json)
*   [entries/ai-models/tt-inference-server.json](https://github.com/tenstorrent/tt-awesome/blob/1d86dbe5/entries/ai-models/tt-inference-server.json)
*   [entries/hw-system/tt-installer.json](https://github.com/tenstorrent/tt-awesome/blob/1d86dbe5/entries/hw-system/tt-installer.json)
*   [scripts/validate.py](https://github.com/tenstorrent/tt-awesome/blob/1d86dbe5/scripts/validate.py)
*   [src/_data/categories.js](https://github.com/tenstorrent/tt-awesome/blob/1d86dbe5/src/_data/categories.js)
*   [src/_data/entries.js](https://github.com/tenstorrent/tt-awesome/blob/1d86dbe5/src/_data/entries.js)
*   [src/_data/planetFeeds.js](https://github.com/tenstorrent/tt-awesome/blob/1d86dbe5/src/_data/planetFeeds.js)
*   [tests/test_validate.py](https://github.com/tenstorrent/tt-awesome/blob/1d86dbe5/tests/test_validate.py)

The **Entry Data Layer** serves as the single source of truth for the Tenstorrent ecosystem directory. It manages how individual project entries are stored in the `entries/` directory, validated for schema compliance, and transformed into a unified data structure for the Eleventy static site generator.

### Data Flow Overview

The pipeline begins with raw JSON files authored by contributors and ends with a sorted, enriched JavaScript object used by Nunjucks templates.

#### Logic Pipeline: JSON to Frontend

1.   **Storage**: Individual project metadata is stored in `entries/{category}/{id}.json`.
2.   **Validation**: `scripts/validate.py` ensures all files meet strict schema requirements.
3.   **Enrichment**: `src/_data/entries.js` loads the JSON files and merges them with external metadata (like GitHub star counts) from `github_meta.json`.
4.   **Transformation**: The data layer pre-computes fields (e.g., `latestStableRelease`) and filters out `hidden` entries.
5.   **Sorting**: Entries are ranked based on `affiliation` (Community → Affiliated → Official), `featured` status, and `stars`.

**Sources:**[src/_data/entries.js 40-94](https://github.com/tenstorrent/tt-awesome/blob/1d86dbe5/src/_data/entries.js#L40-L94)[scripts/validate.py 104-154](https://github.com/tenstorrent/tt-awesome/blob/1d86dbe5/scripts/validate.py#L104-L154)[src/_data/categories.js 4-17](https://github.com/tenstorrent/tt-awesome/blob/1d86dbe5/src/_data/categories.js#L4-L17)

* * *

### Entry JSON Schema & Categories

Every project in the directory must adhere to a strict JSON schema. This ensures consistency across the website and the generated README. Key fields include:

*   **Identity**: `id` (must match filename), `name`, and `description`.
*   **Classification**: `affiliation` (official, affiliated, or community) and a list of `categories`.
*   **Resources**: A `links` array containing `repo`, `website`, `video`, etc.
*   **Hardware**: Compatibility tags for Tenstorrent generations (e.g., `wormhole`, `blackhole`).

The category system is defined by 12 distinct slugs (e.g., `ai-models`, `kernels`, `dev-tools`) which determine the project's primary location in the UI and its folder path in the repository.

For details, see [Entry JSON Schema & Categories](https://deepwiki.com/tenstorrent/tt-awesome/2.1-entry-json-schema-and-categories).

**Sources:**[scripts/validate.py 11-21](https://github.com/tenstorrent/tt-awesome/blob/1d86dbe5/scripts/validate.py#L11-L21)[scripts/validate.py 24-93](https://github.com/tenstorrent/tt-awesome/blob/1d86dbe5/scripts/validate.py#L24-L93)[src/_data/categories.js 4-17](https://github.com/tenstorrent/tt-awesome/blob/1d86dbe5/src/_data/categories.js#L4-L17)

* * *

### Entry Validation & CI Gating

To maintain data integrity, `scripts/validate.py` is executed on every Pull Request via GitHub Actions. The validator performs:

*   **Hard Constraints**: Rejects entries with missing required fields, invalid URL formats (HTTPS only), or unknown category slugs.
*   **Uniqueness Checks**: Ensures no two projects share the same `id`.
*   **Soft Warnings**: Flags missing metadata like `added_at` dates that should be backfilled.

This gating mechanism prevents malformed data from breaking the build or the search interface.

For details, see [Entry Validation & CI Gating](https://deepwiki.com/tenstorrent/tt-awesome/2.2-entry-validation-and-ci-gating).

**Sources:**[scripts/validate.py 24-93](https://github.com/tenstorrent/tt-awesome/blob/1d86dbe5/scripts/validate.py#L24-L93)[scripts/validate.py 113-146](https://github.com/tenstorrent/tt-awesome/blob/1d86dbe5/scripts/validate.py#L113-L146)[tests/test_validate.py 12-104](https://github.com/tenstorrent/tt-awesome/blob/1d86dbe5/tests/test_validate.py#L12-L104)

* * *

### Entries Directory Reference

The `entries/` directory is organized into subdirectories corresponding to the primary category of each project. This structure makes the repository easy to navigate for contributors and maintainers.

| Directory | Purpose | Example Entry |
| --- | --- | --- |
| `ai-models/` | Running and serving AI models | `tt-inference-server.json` |
| `hw-system/` | Drivers, firmware, and installers | `tt-installer.json` |
| `agents/` | On-device AI assistants | `ttvt-ai-agents.json` |
| `kernels/` | Low-level Metalium programming | — |

For a full tour of all 11 category subdirectories and notable projects, see [Entries Directory Reference](https://deepwiki.com/tenstorrent/tt-awesome/2.3-entries-directory-reference).

**Sources:**[scripts/validate.py 12-16](https://github.com/tenstorrent/tt-awesome/blob/1d86dbe5/scripts/validate.py#L12-L16)[entries/ai-models/tt-inference-server.json 1-37](https://github.com/tenstorrent/tt-awesome/blob/1d86dbe5/entries/ai-models/tt-inference-server.json#L1-L37)[entries/hw-system/tt-installer.json 1-35](https://github.com/tenstorrent/tt-awesome/blob/1d86dbe5/entries/hw-system/tt-installer.json#L1-L35)[entries/agents/ttvt-ai-agents.json 1-37](https://github.com/tenstorrent/tt-awesome/blob/1d86dbe5/entries/agents/ttvt-ai-agents.json#L1-L37)

* * *

### Enrichment and Sorting Logic

The `src/_data/entries.js` file is the engine that prepares the data for the frontend. It performs three critical tasks:

1.   **Metadata Merging**: It matches the `repo` URL of an entry to keys in `github_meta.json` to inject live star counts and release history [src/_data/entries.js 48-51](https://github.com/tenstorrent/tt-awesome/blob/1d86dbe5/src/_data/entries.js#L48-L51)
2.   **Safety Filtering**: It enforces `isSafeHttpsUrl` on all links and author URLs to prevent XSS or broken links [src/_data/entries.js 62-69](https://github.com/tenstorrent/tt-awesome/blob/1d86dbe5/src/_data/entries.js#L62-L69)
3.   **Tiered Sorting**: It implements the project's sorting philosophy, ranking "Community" projects first to encourage ecosystem growth, followed by "Affiliated" and "Official" tiers [src/_data/entries.js 84-93](https://github.com/tenstorrent/tt-awesome/blob/1d86dbe5/src/_data/entries.js#L84-L93)

**Sources:**[src/_data/entries.js 27-38](https://github.com/tenstorrent/tt-awesome/blob/1d86dbe5/src/_data/entries.js#L27-L38)[src/_data/entries.js 44-78](https://github.com/tenstorrent/tt-awesome/blob/1d86dbe5/src/_data/entries.js#L44-L78)[src/_data/entries.js 84-93](https://github.com/tenstorrent/tt-awesome/blob/1d86dbe5/src/_data/entries.js#L84-L93)

Dismiss
Refresh this wiki

Enter email to refresh
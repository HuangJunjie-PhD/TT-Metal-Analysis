---
project: tt-awesome
github: https://github.com/tenstorrent/tt-awesome
deepwiki: https://deepwiki.com/tenstorrent/tt-awesome/5-scripts-and-tooling-reference
---

# Scripts & Tooling Reference

Relevant source files
*   [README.md](https://github.com/tenstorrent/tt-awesome/blob/1d86dbe5/README.md?plain=1)
*   [data.json](https://github.com/tenstorrent/tt-awesome/blob/1d86dbe5/data.json)
*   [entries/ai-models/tt-studio.json](https://github.com/tenstorrent/tt-awesome/blob/1d86dbe5/entries/ai-models/tt-studio.json)
*   [entries/cloud-infra/tt-console.json](https://github.com/tenstorrent/tt-awesome/blob/1d86dbe5/entries/cloud-infra/tt-console.json)
*   [entries/compilers/tt-forge.json](https://github.com/tenstorrent/tt-awesome/blob/1d86dbe5/entries/compilers/tt-forge.json)
*   [entries/kernels/tt-metal.json](https://github.com/tenstorrent/tt-awesome/blob/1d86dbe5/entries/kernels/tt-metal.json)
*   [package.json](https://github.com/tenstorrent/tt-awesome/blob/1d86dbe5/package.json)
*   [scripts/add_entry.py](https://github.com/tenstorrent/tt-awesome/blob/1d86dbe5/scripts/add_entry.py)
*   [scripts/generate_data_json.py](https://github.com/tenstorrent/tt-awesome/blob/1d86dbe5/scripts/generate_data_json.py)
*   [scripts/generate_readme.py](https://github.com/tenstorrent/tt-awesome/blob/1d86dbe5/scripts/generate_readme.py)
*   [scripts/issue_to_entry.py](https://github.com/tenstorrent/tt-awesome/blob/1d86dbe5/scripts/issue_to_entry.py)
*   [tests/test_generate_readme.py](https://github.com/tenstorrent/tt-awesome/blob/1d86dbe5/tests/test_generate_readme.py)

The `scripts/` directory contains the Python-based automation layer of the `tt-awesome` project. These scripts handle the transformation of raw JSON entry data into human-readable documentation, machine-readable API exports, and the automated ingestion of new community contributions.

### Tooling Pipeline Overview

The pipeline is designed to be unidirectional: `entries/*.json` files serve as the single source of truth. Scripts consume these files to generate the `README.md` and `data.json` artifacts used by GitHub and the Eleventy frontend.

**System Data Flow**

Sources: [scripts/generate_readme.py 11-27](https://github.com/tenstorrent/tt-awesome/blob/1d86dbe5/scripts/generate_readme.py#L11-L27)[scripts/issue_to_entry.py 5-12](https://github.com/tenstorrent/tt-awesome/blob/1d86dbe5/scripts/issue_to_entry.py#L5-L12)[scripts/generate_data_json.py 2-15](https://github.com/tenstorrent/tt-awesome/blob/1d86dbe5/scripts/generate_data_json.py#L2-L15)

* * *

### Core Scripts

| Script | Role | Key Code Entities |
| --- | --- | --- |
| `generate_readme.py` | Transforms JSON entries into the project's root `README.md`. | `sort_entries()`, `render_entry()`, `AFFILIATION_ORDER` |
| `generate_data_json.py` | Produces a machine-readable API export for the frontend. | `generate()`, `CATEGORIES` |
| `issue_to_entry.py` | Automates PR creation from GitHub Issue form submissions. | `parse_sections()`, `slugify()`, `parse_checkboxes()` |
| `add_entry.py` | Interactive CLI for manual entry creation. | `ask()`, `pick()`, `collect_links()` |
| `validate.py` | Enforces schema and integrity rules (CI gate). | `VALID_CATEGORIES`, `validate_entry()` |

Sources: [scripts/generate_readme.py 80-91](https://github.com/tenstorrent/tt-awesome/blob/1d86dbe5/scripts/generate_readme.py#L80-L91)[scripts/issue_to_entry.py 27-58](https://github.com/tenstorrent/tt-awesome/blob/1d86dbe5/scripts/issue_to_entry.py#L27-L58)[scripts/add_entry.py 26-54](https://github.com/tenstorrent/tt-awesome/blob/1d86dbe5/scripts/add_entry.py#L26-L54)[package.json 5-10](https://github.com/tenstorrent/tt-awesome/blob/1d86dbe5/package.json#L5-L10)

* * *

### README Generation & Sorting

The `generate_readme.py` script implements the project's "community-first" philosophy. Unlike typical directories that prioritize official corporate content, this script uses a weighted sorting algorithm to ensure community contributions are highlighted.

*   **Sorting Logic**: Entries are sorted by a tuple key: `(affiliation_tier, featured_status, -stars)`.
*   **Affiliation Tiers**: Defined in `AFFILIATION_ORDER`, where `community` (0) <`affiliated` (1) <`official` (2) [scripts/generate_readme.py 31](https://github.com/tenstorrent/tt-awesome/blob/1d86dbe5/scripts/generate_readme.py#L31-L31)
*   **Formatting**: Uses `LINK_ICONS` and `SHIELDS` badges to provide visual metadata for each entry [scripts/generate_readme.py 34-67](https://github.com/tenstorrent/tt-awesome/blob/1d86dbe5/scripts/generate_readme.py#L34-L67)

For details, see [README Generator](https://deepwiki.com/tenstorrent/tt-awesome/5.1-readme-generator).

* * *

### Data Export & Contribution Automation

The project provides two primary ways to manage data beyond manual JSON editing:

1.   **API Export**: `generate_data_json.py` aggregates all entries into a single `data.json` file. This file includes metadata such as `total_entries` and a UTC `generated` timestamp [scripts/generate_data_json.py 75-84](https://github.com/tenstorrent/tt-awesome/blob/1d86dbe5/scripts/generate_data_json.py#L75-L84)
2.   **Issue Parsing**: `issue_to_entry.py` allows users to submit projects via a GitHub Issue template. It parses the Markdown headings (e.g., `### Name`) and checkboxes to generate a valid JSON file in the correct category subdirectory [scripts/issue_to_entry.py 27-70](https://github.com/tenstorrent/tt-awesome/blob/1d86dbe5/scripts/issue_to_entry.py#L27-L70)
3.   **Local CLI**: `add_entry.py` provides an interactive terminal interface (`ask()` and `pick()` helpers) to ensure local contributors follow the schema [scripts/add_entry.py 26-54](https://github.com/tenstorrent/tt-awesome/blob/1d86dbe5/scripts/add_entry.py#L26-L54)

For details, see [Data JSON Export & Issue-to-Entry Automation](https://deepwiki.com/tenstorrent/tt-awesome/5.2-data-json-export-and-issue-to-entry-automation).

* * *

### Integration with CI/CD

These scripts are orchestrated via `package.json` scripts and GitHub Actions. The `generate` command runs both the README and Data JSON generators in sequence to ensure all artifacts stay in sync with the `entries/` directory [package.json 9](https://github.com/tenstorrent/tt-awesome/blob/1d86dbe5/package.json#L9-L9)

**Code Entity Mapping**

Sources: [scripts/generate_readme.py 14-31](https://github.com/tenstorrent/tt-awesome/blob/1d86dbe5/scripts/generate_readme.py#L14-L31)[scripts/generate_data_json.py 25-41](https://github.com/tenstorrent/tt-awesome/blob/1d86dbe5/scripts/generate_data_json.py#L25-L41)[scripts/issue_to_entry.py 77-85](https://github.com/tenstorrent/tt-awesome/blob/1d86dbe5/scripts/issue_to_entry.py#L77-L85)[package.json 9](https://github.com/tenstorrent/tt-awesome/blob/1d86dbe5/package.json#L9-L9)

Dismiss
Refresh this wiki

Enter email to refresh
---
project: tt-awesome
github: https://github.com/tenstorrent/tt-awesome
deepwiki: https://deepwiki.com/tenstorrent/tt-awesome
---

# Overview

 Relevant source files

* [.github/ISSUE\_TEMPLATE/submit-entry.yml](https://github.com/tenstorrent/tt-awesome/blob/1d86dbe5/.github/ISSUE_TEMPLATE/submit-entry.yml)
* [.gitignore](https://github.com/tenstorrent/tt-awesome/blob/1d86dbe5/.gitignore)
* [CODE\_OF\_CONDUCT.md](https://github.com/tenstorrent/tt-awesome/blob/1d86dbe5/CODE_OF_CONDUCT.md?plain=1)
* [CONTRIBUTING.md](https://github.com/tenstorrent/tt-awesome/blob/1d86dbe5/CONTRIBUTING.md?plain=1)
* [LICENSE](https://github.com/tenstorrent/tt-awesome/blob/1d86dbe5/LICENSE)
* [LICENSE-DOCS](https://github.com/tenstorrent/tt-awesome/blob/1d86dbe5/LICENSE-DOCS)
* [LICENSE\_understanding.txt](https://github.com/tenstorrent/tt-awesome/blob/1d86dbe5/LICENSE_understanding.txt)
* [NOTICE](https://github.com/tenstorrent/tt-awesome/blob/1d86dbe5/NOTICE)
* [README.md](https://github.com/tenstorrent/tt-awesome/blob/1d86dbe5/README.md?plain=1)
* [SECURITY.md](https://github.com/tenstorrent/tt-awesome/blob/1d86dbe5/SECURITY.md?plain=1)
* [docs/superpowers/specs/2026-05-08-awesome-tenstorrent-design.md](https://github.com/tenstorrent/tt-awesome/blob/1d86dbe5/docs/superpowers/specs/2026-05-08-awesome-tenstorrent-design.md?plain=1)
* [entries/.gitkeep](https://github.com/tenstorrent/tt-awesome/blob/1d86dbe5/entries/.gitkeep)
* [entries/cloud-infra/tt-console.json](https://github.com/tenstorrent/tt-awesome/blob/1d86dbe5/entries/cloud-infra/tt-console.json)
* [package-lock.json](https://github.com/tenstorrent/tt-awesome/blob/1d86dbe5/package-lock.json)
* [scripts/generate\_readme.py](https://github.com/tenstorrent/tt-awesome/blob/1d86dbe5/scripts/generate_readme.py)
* [scripts/issue\_to\_entry.py](https://github.com/tenstorrent/tt-awesome/blob/1d86dbe5/scripts/issue_to_entry.py)
* [tests/test\_generate\_readme.py](https://github.com/tenstorrent/tt-awesome/blob/1d86dbe5/tests/test_generate_readme.py)

`tt-awesome` is a curated, community-first directory of projects, tools, models, and research specifically for the Tenstorrent hardware ecosystem [docs/superpowers/specs/2026-05-08-awesome-tenstorrent-design.md8-15](https://github.com/tenstorrent/tt-awesome/blob/1d86dbe5/docs/superpowers/specs/2026-05-08-awesome-tenstorrent-design.md?plain=1#L8-L15) It serves as a unified discovery layer for developers working with Tenstorrent accelerators, ranging from high-level AI model serving to bare-metal RISC-V programming [README.md24-48](https://github.com/tenstorrent/tt-awesome/blob/1d86dbe5/README.md?plain=1#L24-L48)

The project is designed as a "data-first" application where a collection of JSON files acts as the single source of truth for a generated Markdown README and a full-featured static website [docs/superpowers/specs/2026-05-08-awesome-tenstorrent-design.md12-16](https://github.com/tenstorrent/tt-awesome/blob/1d86dbe5/docs/superpowers/specs/2026-05-08-awesome-tenstorrent-design.md?plain=1#L12-L16)

### System Architecture

The architecture follows a unidirectional pipeline: modifications are made to JSON entry files, which then trigger automated scripts to update the documentation and the web frontend [docs/superpowers/specs/2026-05-08-awesome-tenstorrent-design.md16](https://github.com/tenstorrent/tt-awesome/blob/1d86dbe5/docs/superpowers/specs/2026-05-08-awesome-tenstorrent-design.md?plain=1#L16-L16)

#### High-Level Data Flow

This diagram illustrates how raw project data moves from the `entries/` directory through various processing scripts to reach the end-user interfaces.

**Sources:** [docs/superpowers/specs/2026-05-08-awesome-tenstorrent-design.md30-68](https://github.com/tenstorrent/tt-awesome/blob/1d86dbe5/docs/superpowers/specs/2026-05-08-awesome-tenstorrent-design.md?plain=1#L30-L68) [scripts/generate\_readme.py1-12](https://github.com/tenstorrent/tt-awesome/blob/1d86dbe5/scripts/generate_readme.py#L1-L12)

### Core Components

* **JSON Database**: Located in `entries/`, these files store structured metadata for every project, including affiliation, hardware compatibility, and links [docs/superpowers/specs/2026-05-08-awesome-tenstorrent-design.md34-37](https://github.com/tenstorrent/tt-awesome/blob/1d86dbe5/docs/superpowers/specs/2026-05-08-awesome-tenstorrent-design.md?plain=1#L34-L37)
* **README Generator**: The `scripts/generate_readme.py` script transforms JSON data into the project's root `README.md`. It implements a specific "community-first" sorting logic where community projects appear above official Tenstorrent repositories [scripts/generate\_readme.py80-91](https://github.com/tenstorrent/tt-awesome/blob/1d86dbe5/scripts/generate_readme.py#L80-L91)
* **Static Website**: A three-pane web interface built with **Eleventy**, providing search, filtering, and deep-linking capabilities [docs/superpowers/specs/2026-05-08-awesome-tenstorrent-design.md149-156](https://github.com/tenstorrent/tt-awesome/blob/1d86dbe5/docs/superpowers/specs/2026-05-08-awesome-tenstorrent-design.md?plain=1#L149-L156)
* **Automation Suite**: Python scripts handle metadata fetching (e.g., GitHub stars), release summarization via LLMs, and entry validation [docs/superpowers/specs/2026-05-08-awesome-tenstorrent-design.md38-42](https://github.com/tenstorrent/tt-awesome/blob/1d86dbe5/docs/superpowers/specs/2026-05-08-awesome-tenstorrent-design.md?plain=1#L38-L42)

### Community-First Philosophy

A defining feature of `tt-awesome` is its sorting algorithm. Projects are ranked by affiliation in the following order:

1. **Community**: Independent developers (shown first) [CONTRIBUTING.md60](https://github.com/tenstorrent/tt-awesome/blob/1d86dbe5/CONTRIBUTING.md?plain=1#L60-L60)
2. **Affiliated**: Tenstorrent employees contributing in a personal capacity [CONTRIBUTING.md57](https://github.com/tenstorrent/tt-awesome/blob/1d86dbe5/CONTRIBUTING.md?plain=1#L57-L57)
3. **Official**: Repositories under the `tenstorrent` GitHub organization [CONTRIBUTING.md58](https://github.com/tenstorrent/tt-awesome/blob/1d86dbe5/CONTRIBUTING.md?plain=1#L58-L58)

This ensures that community-driven innovation is highlighted rather than being buried by official corporate repositories [scripts/generate\_readme.py29-31](https://github.com/tenstorrent/tt-awesome/blob/1d86dbe5/scripts/generate_readme.py#L29-L31)

#### Code-to-System Mapping

This diagram maps specific code entities to their roles within the curation and display system.

**Sources:** [scripts/generate\_readme.py14-31](https://github.com/tenstorrent/tt-awesome/blob/1d86dbe5/scripts/generate_readme.py#L14-L31) [scripts/issue\_to\_entry.py5-12](https://github.com/tenstorrent/tt-awesome/blob/1d86dbe5/scripts/issue_to_entry.py#L5-L12) [CONTRIBUTING.md13-27](https://github.com/tenstorrent/tt-awesome/blob/1d86dbe5/CONTRIBUTING.md?plain=1#L13-L27)

### Detailed Sections

For in-depth information on specific parts of the ecosystem, refer to the following pages:

* **[Getting Started & Contributing](/tenstorrent/tt-awesome/1.1-getting-started-and-contributing)**: Detailed guide on how to add projects via GitHub Issues, the CLI tool, or direct JSON edits. Covers the entry schema and affiliation policies.
* **[Project Architecture & Design Decisions](/tenstorrent/tt-awesome/1.2-project-architecture-and-design-decisions)**: Deep dive into the tech stack (Eleventy, Python), the nightly automation workflows, and the rationale behind the single-source-of-truth design.

**Sources:** [README.md1-22](https://github.com/tenstorrent/tt-awesome/blob/1d86dbe5/README.md?plain=1#L1-L22) [CONTRIBUTING.md1-90](https://github.com/tenstorrent/tt-awesome/blob/1d86dbe5/CONTRIBUTING.md?plain=1#L1-L90) [docs/superpowers/specs/2026-05-08-awesome-tenstorrent-design.md1-147](https://github.com/tenstorrent/tt-awesome/blob/1d86dbe5/docs/superpowers/specs/2026-05-08-awesome-tenstorrent-design.md?plain=1#L1-L147)

Refresh this wiki
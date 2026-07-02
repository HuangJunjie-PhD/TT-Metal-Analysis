---
project: tt-awesome
github: https://github.com/tenstorrent/tt-awesome
deepwiki: https://deepwiki.com/tenstorrent/tt-awesome/9-glossary
---

# Glossary

Relevant source files
*   [.claude-plugin/marketplace.json](https://github.com/tenstorrent/tt-awesome/blob/1d86dbe5/.claude-plugin/marketplace.json)
*   [.claude-plugin/plugin.json](https://github.com/tenstorrent/tt-awesome/blob/1d86dbe5/.claude-plugin/plugin.json)
*   [.eleventy.js](https://github.com/tenstorrent/tt-awesome/blob/1d86dbe5/.eleventy.js)
*   [README.md](https://github.com/tenstorrent/tt-awesome/blob/1d86dbe5/README.md?plain=1)
*   [docs/superpowers/specs/2026-05-27-add-project-skill-design.md](https://github.com/tenstorrent/tt-awesome/blob/1d86dbe5/docs/superpowers/specs/2026-05-27-add-project-skill-design.md?plain=1)
*   [entries/cloud-infra/tt-console.json](https://github.com/tenstorrent/tt-awesome/blob/1d86dbe5/entries/cloud-infra/tt-console.json)
*   [entries/riscv-arch/ttsim.json](https://github.com/tenstorrent/tt-awesome/blob/1d86dbe5/entries/riscv-arch/ttsim.json)
*   [scripts/generate_readme.py](https://github.com/tenstorrent/tt-awesome/blob/1d86dbe5/scripts/generate_readme.py)
*   [scripts/issue_to_entry.py](https://github.com/tenstorrent/tt-awesome/blob/1d86dbe5/scripts/issue_to_entry.py)
*   [scripts/validate.py](https://github.com/tenstorrent/tt-awesome/blob/1d86dbe5/scripts/validate.py)
*   [skills/add-project/SKILL.md](https://github.com/tenstorrent/tt-awesome/blob/1d86dbe5/skills/add-project/SKILL.md?plain=1)
*   [src/_data/entries.js](https://github.com/tenstorrent/tt-awesome/blob/1d86dbe5/src/_data/entries.js)
*   [src/_data/github_meta.json](https://github.com/tenstorrent/tt-awesome/blob/1d86dbe5/src/_data/github_meta.json)
*   [src/_data/planet_feeds.json](https://github.com/tenstorrent/tt-awesome/blob/1d86dbe5/src/_data/planet_feeds.json)
*   [src/_includes/sidebar.njk](https://github.com/tenstorrent/tt-awesome/blob/1d86dbe5/src/_includes/sidebar.njk)
*   [src/assets/main.js](https://github.com/tenstorrent/tt-awesome/blob/1d86dbe5/src/assets/main.js)
*   [src/assets/style.css](https://github.com/tenstorrent/tt-awesome/blob/1d86dbe5/src/assets/style.css)
*   [src/index.njk](https://github.com/tenstorrent/tt-awesome/blob/1d86dbe5/src/index.njk)
*   [tests/test_generate_readme.py](https://github.com/tenstorrent/tt-awesome/blob/1d86dbe5/tests/test_generate_readme.py)
*   [tests/test_validate.py](https://github.com/tenstorrent/tt-awesome/blob/1d86dbe5/tests/test_validate.py)

This glossary defines codebase-specific terminology, domain concepts, and technical abbreviations used throughout the **tt-awesome** project and the broader Tenstorrent ecosystem.

## Core Project Terms

These terms refer to the internal architecture and data structures of the `tt-awesome` repository.

### Affiliation Tiers

The classification system for projects based on their relationship with Tenstorrent. This determines the badge displayed and the sorting priority in both the README and the website [scripts/generate_readme.py 31](https://github.com/tenstorrent/tt-awesome/blob/1d86dbe5/scripts/generate_readme.py#L31-L31)

*   **Official**: Projects maintained directly by Tenstorrent (e.g., `tt-metal`, `tt-forge`).
*   **Affiliated**: Projects by Tenstorrent employees or close partners that are not official product offerings.
*   **Community**: Projects created independently by the developer community.

### Entry

A single JSON file located within a subdirectory of `entries/`. It serves as the single source of truth for a project's metadata, including its name, description, links, and hardware compatibility [scripts/validate.py 24-31](https://github.com/tenstorrent/tt-awesome/blob/1d86dbe5/scripts/validate.py#L24-L31)

### Planet Tenstorrent

An automated aggregator sub-site (located at `/planet/`) that combines GitHub releases, external RSS/Atom feeds, and article-type links from entries into a unified chronological stream [src/.eleventy.js 132-144](https://github.com/tenstorrent/tt-awesome/blob/1d86dbe5/src/.eleventy.js#L132-L144)

### Community-First Sorting

The logic used to rank projects. To encourage ecosystem growth, the pipeline sorts by **Affiliation Tier** (Community → Affiliated → Official), then by **Featured** status, and finally by **GitHub Star Count**[scripts/generate_readme.py 80-91](https://github.com/tenstorrent/tt-awesome/blob/1d86dbe5/scripts/generate_readme.py#L80-L91)

### Data Flow Diagram: Natural Language to Code Entities

The following diagram illustrates how a user-submitted project (Natural Language Space) is transformed into a validated, sorted entry within the system (Code Entity Space).

Title: Entry Processing Pipeline

Sources: [scripts/issue_to_entry.py](https://github.com/tenstorrent/tt-awesome/blob/1d86dbe5/scripts/issue_to_entry.py)[scripts/validate.py 11-20](https://github.com/tenstorrent/tt-awesome/blob/1d86dbe5/scripts/validate.py#L11-L20)[src/_data/entries.js 40-78](https://github.com/tenstorrent/tt-awesome/blob/1d86dbe5/src/_data/entries.js#L40-L78)[scripts/generate_readme.py 80-91](https://github.com/tenstorrent/tt-awesome/blob/1d86dbe5/scripts/generate_readme.py#L80-L91)[src/.eleventy.js 48-87](https://github.com/tenstorrent/tt-awesome/blob/1d86dbe5/src/.eleventy.js#L48-L87)

* * *

## Tenstorrent Ecosystem Terms

These terms refer to the hardware and software stack components frequently referenced in entry data files.

### Hardware Architectures

*   **Grayskull**: Tenstorrent's first-generation processor (PCIe-based).
*   **Wormhole**: Second-generation processor featuring integrated Ethernet for scaling (N150/N300).
*   **Blackhole**: Third-generation processor designed for switch-free scaling (P150/P300).
*   **Galaxy**: A 32-chip multi-card system configuration.
*   **QuietBox (QB)**: A compact, liquid-cooled workstation configuration (e.g., QB2).

### Software Stack Components

*   **TT-Metalium**: The low-level programming model for writing kernels that run on Tensix cores [README.md 46-48](https://github.com/tenstorrent/tt-awesome/blob/1d86dbe5/README.md?plain=1#L46-L48)
*   **TT-NN**: A high-level tensor operator library built on top of TT-Metalium.
*   **TT-Forge**: An MLIR-based compiler frontend for running PyTorch/ONNX workloads [README.md 34-36](https://github.com/tenstorrent/tt-awesome/blob/1d86dbe5/README.md?plain=1#L34-L36)
*   **ttsim**: A fast, bit-exact full-system simulator for Wormhole and Blackhole hardware [entries/riscv-arch/ttsim.json 2-4](https://github.com/tenstorrent/tt-awesome/blob/1d86dbe5/entries/riscv-arch/ttsim.json#L2-L4)
*   **BUDA**: A legacy software stack; entries using BUDA are often deprioritized in the website showcase in favor of TT-Metalium [src/.eleventy.js 52-61](https://github.com/tenstorrent/tt-awesome/blob/1d86dbe5/src/.eleventy.js#L52-L61)

### Hardware-Software Interaction Diagram

This diagram bridges the conceptual hardware/software layers with the specific code entities and data fields used to track them.

Title: Ecosystem Mapping

Sources: [scripts/validate.py 18](https://github.com/tenstorrent/tt-awesome/blob/1d86dbe5/scripts/validate.py#L18-L18)[entries/riscv-arch/ttsim.json 27-29](https://github.com/tenstorrent/tt-awesome/blob/1d86dbe5/entries/riscv-arch/ttsim.json#L27-L29)[src/_data/entries.js 53-61](https://github.com/tenstorrent/tt-awesome/blob/1d86dbe5/src/_data/entries.js#L53-L61)

* * *

## Technical Abbreviations

| Abbreviation | Full Term | Context |
| --- | --- | --- |
| **ARC** | Argonaut RISC Core | The system controller RISC-V core on Tenstorrent chips. |
| **ERISC** | Ethernet RISC | RISC-V cores dedicated to Ethernet stream processing. |
| **KMD** | Kernel Mode Driver | The low-level Linux driver (`tt-kmd`) for hardware communication. |
| **NoC** | Network on Chip | The high-speed interconnect between cores on the silicon. |
| **PPA** | Personal Package Archive | Used in `packages` field for Ubuntu/Debian distributions [scripts/generate_readme.py 53-60](https://github.com/tenstorrent/tt-awesome/blob/1d86dbe5/scripts/generate_readme.py#L53-L60) |
| **SMI** | System Management Interface | Refers to `tt-smi`, the tool for monitoring hardware telemetry [src/_data/planet_feeds.json 48-50](https://github.com/tenstorrent/tt-awesome/blob/1d86dbe5/src/_data/planet_feeds.json#L48-L50) |
| **UMD** | User Mode Driver | The library (`tt-umd`) that provides a user-space API to the KMD. |

Sources: [scripts/generate_readme.py 44-60](https://github.com/tenstorrent/tt-awesome/blob/1d86dbe5/scripts/generate_readme.py#L44-L60)[src/_data/planet_feeds.json 48-57](https://github.com/tenstorrent/tt-awesome/blob/1d86dbe5/src/_data/planet_feeds.json#L48-L57)[entries/riscv-arch/ttsim.json 4-5](https://github.com/tenstorrent/tt-awesome/blob/1d86dbe5/entries/riscv-arch/ttsim.json#L4-L5)

Dismiss
Refresh this wiki

Enter email to refresh
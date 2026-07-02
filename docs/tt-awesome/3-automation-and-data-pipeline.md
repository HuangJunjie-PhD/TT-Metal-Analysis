---
project: tt-awesome
github: https://github.com/tenstorrent/tt-awesome
deepwiki: https://deepwiki.com/tenstorrent/tt-awesome/3-automation-and-data-pipeline
---

# Automation & Data Pipeline

Relevant source files
*   [.github/workflows/deploy.yml](https://github.com/tenstorrent/tt-awesome/blob/1d86dbe5/.github/workflows/deploy.yml)
*   [.github/workflows/nightly.yml](https://github.com/tenstorrent/tt-awesome/blob/1d86dbe5/.github/workflows/nightly.yml)
*   [.github/workflows/planet-feeds.yml](https://github.com/tenstorrent/tt-awesome/blob/1d86dbe5/.github/workflows/planet-feeds.yml)
*   [.github/workflows/validate.yml](https://github.com/tenstorrent/tt-awesome/blob/1d86dbe5/.github/workflows/validate.yml)
*   [_config.yml](https://github.com/tenstorrent/tt-awesome/blob/1d86dbe5/_config.yml)
*   [src/planet/index.njk](https://github.com/tenstorrent/tt-awesome/blob/1d86dbe5/src/planet/index.njk)

The `tt-awesome` backend is a collection of automated Python scripts and GitHub Actions workflows that maintain the directory's metadata, track the Tenstorrent ecosystem's pulse, and handle site deployment. This pipeline ensures that project statistics (like GitHub stars) remain current, new software releases are automatically summarized using LLMs, and community content from external feeds is aggregated into the "Planet Tenstorrent" sub-site.

## System Architecture Overview

The automation layer operates on a "pull-then-commit" model. Nightly workflows fetch data from external APIs (GitHub, RSS, Atom), process it, and commit the results back to the repository as JSON files in `src/_data/`. This triggers the build pipeline to refresh the static site.

### Data Flow & Code Entities

The following diagram maps the high-level automation processes to the specific scripts and data files they manage.

**Automation Data Flow**

Sources: [.github/workflows/nightly.yml 1-50](https://github.com/tenstorrent/tt-awesome/blob/1d86dbe5/.github/workflows/nightly.yml#L1-L50)[.github/workflows/planet-feeds.yml 1-30](https://github.com/tenstorrent/tt-awesome/blob/1d86dbe5/.github/workflows/planet-feeds.yml#L1-L30)[src/planet/index.njk 6-12](https://github.com/tenstorrent/tt-awesome/blob/1d86dbe5/src/planet/index.njk#L6-L12)

* * *

## 3.1 GitHub Metadata Fetcher

The `scripts/fetch_github_meta.py` script is responsible for keeping the directory's project statistics synchronized with reality. It iterates through all entries, identifies those with GitHub repositories, and queries the GitHub REST API for star counts, fork status, and latest release tags. The results are stored in `src/_data/github_meta.json`, which `src/_data/entries.js` then merges into the main entry objects at build time.

For details, see [GitHub Metadata Fetcher](https://deepwiki.com/tenstorrent/tt-awesome/3.1-github-metadata-fetcher).

**Sources:**[.github/workflows/nightly.yml 26-29](https://github.com/tenstorrent/tt-awesome/blob/1d86dbe5/.github/workflows/nightly.yml#L26-L29)[.github/workflows/nightly.yml 42-43](https://github.com/tenstorrent/tt-awesome/blob/1d86dbe5/.github/workflows/nightly.yml#L42-L43)

* * *

## 3.2 Release Summarization Pipeline

To provide users with meaningful updates without requiring manual writing, `scripts/summarize_releases.py` automates the tracking of new software versions. It identifies recent releases across all listed repositories, filters out noise (pre-releases or empty notes), and sends the release notes to the Anthropic Claude API. The LLM generates a concise, one-paragraph summary of the changes, which is then appended to `src/_data/planet_feeds.json` for display in the "Recent Releases" pane.

For details, see [Release Summarization Pipeline](https://deepwiki.com/tenstorrent/tt-awesome/3.2-release-summarization-pipeline).

**Sources:**[.github/workflows/nightly.yml 30-37](https://github.com/tenstorrent/tt-awesome/blob/1d86dbe5/.github/workflows/nightly.yml#L30-L37)[.github/workflows/nightly.yml 44](https://github.com/tenstorrent/tt-awesome/blob/1d86dbe5/.github/workflows/nightly.yml#L44-L44)

* * *

## 3.3 Planet Feeds Aggregator

The "Planet Tenstorrent" sub-site aggregates community content including YouTube videos, arXiv papers, and Reddit discussions. This is powered by `scripts/fetch_planet_feeds.py`, which polls a curated list of external RSS and Atom feeds. New items are added to `src/_data/planet_feeds.json` with an `approved: false` flag, requiring a maintainer to flip the bit to `true` via a Pull Request before they appear on the live site.

For details, see [Planet Feeds Aggregator](https://deepwiki.com/tenstorrent/tt-awesome/3.3-planet-feeds-aggregator).

**Sources:**[.github/workflows/planet-feeds.yml 16-17](https://github.com/tenstorrent/tt-awesome/blob/1d86dbe5/.github/workflows/planet-feeds.yml#L16-L17)[.github/workflows/planet-feeds.yml 21-29](https://github.com/tenstorrent/tt-awesome/blob/1d86dbe5/.github/workflows/planet-feeds.yml#L21-L29)[src/planet/index.njk 12-20](https://github.com/tenstorrent/tt-awesome/blob/1d86dbe5/src/planet/index.njk#L12-L20)

* * *

## 3.4 Build & Deploy Pipeline

The CI/CD lifecycle is managed primarily by `deploy.yml` and `validate.yml`. Every PR is gated by a validation script that ensures JSON integrity. Upon merging to `main`, a two-phase build process begins:

1.   **Python Phase**: `scripts/generate_readme.py` and `scripts/generate_data_json.py` update the repository's README and the frontend's API payload.
2.   **Eleventy Phase**: The Node.js-based Eleventy engine transforms templates and JSON data into a static site in the `_site/` directory, which is then deployed to GitHub Pages.

For details, see [Build & Deploy Pipeline](https://deepwiki.com/tenstorrent/tt-awesome/3.4-build-and-deploy-pipeline).

### CI/CD Pipeline Mapping

The following diagram illustrates how GitHub Actions workflows orchestrate the transition from raw code/data to the deployed environment.

**Workflow Orchestration**

Sources: [.github/workflows/validate.yml 1-13](https://github.com/tenstorrent/tt-awesome/blob/1d86dbe5/.github/workflows/validate.yml#L1-L13)[.github/workflows/deploy.yml 1-50](https://github.com/tenstorrent/tt-awesome/blob/1d86dbe5/.github/workflows/deploy.yml#L1-L50)[.github/workflows/nightly.yml 1-50](https://github.com/tenstorrent/tt-awesome/blob/1d86dbe5/.github/workflows/nightly.yml#L1-L50)[_config.yml 1-15](https://github.com/tenstorrent/tt-awesome/blob/1d86dbe5/_config.yml#L1-L15)

Dismiss
Refresh this wiki

Enter email to refresh
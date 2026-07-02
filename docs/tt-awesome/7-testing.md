---
project: tt-awesome
github: https://github.com/tenstorrent/tt-awesome
deepwiki: https://deepwiki.com/tenstorrent/tt-awesome/7-testing
---

# Testing

Relevant source files
*   [README.md](https://github.com/tenstorrent/tt-awesome/blob/1d86dbe5/README.md?plain=1)
*   [entries/cloud-infra/tt-console.json](https://github.com/tenstorrent/tt-awesome/blob/1d86dbe5/entries/cloud-infra/tt-console.json)
*   [scripts/generate_readme.py](https://github.com/tenstorrent/tt-awesome/blob/1d86dbe5/scripts/generate_readme.py)
*   [scripts/issue_to_entry.py](https://github.com/tenstorrent/tt-awesome/blob/1d86dbe5/scripts/issue_to_entry.py)
*   [scripts/summarize_releases.py](https://github.com/tenstorrent/tt-awesome/blob/1d86dbe5/scripts/summarize_releases.py)
*   [scripts/validate.py](https://github.com/tenstorrent/tt-awesome/blob/1d86dbe5/scripts/validate.py)
*   [src/_data/entries.js](https://github.com/tenstorrent/tt-awesome/blob/1d86dbe5/src/_data/entries.js)
*   [tests/test_generate_readme.py](https://github.com/tenstorrent/tt-awesome/blob/1d86dbe5/tests/test_generate_readme.py)
*   [tests/test_summarize_releases.py](https://github.com/tenstorrent/tt-awesome/blob/1d86dbe5/tests/test_summarize_releases.py)
*   [tests/test_validate.py](https://github.com/tenstorrent/tt-awesome/blob/1d86dbe5/tests/test_validate.py)

The `tt-awesome` project maintains a dual-language test suite to ensure the integrity of the curated data and the correctness of the transformation pipeline. The suite is divided into Python-based tests for backend scripts and JavaScript-based tests for the Eleventy frontend logic.

## High-Level Architecture

The testing strategy focuses on validating the "Single Source of Truth" (`entries/*.json`) and the scripts that consume this data to generate the README and the website.

### Test Distribution

| Area | Framework | Target |
| --- | --- | --- |
| **Python** | `pytest` | Schema validation, README generation, Release summarization, Metadata fetching. |
| **JavaScript** | `Node.js` | Eleventy custom filters, Data pipeline integration. |

Sources: [scripts/validate.py 104-154](https://github.com/tenstorrent/tt-awesome/blob/1d86dbe5/scripts/validate.py#L104-L154)[scripts/generate_readme.py 150-182](https://github.com/tenstorrent/tt-awesome/blob/1d86dbe5/scripts/generate_readme.py#L150-L182)[tests/test_summarize_releases.py 1-13](https://github.com/tenstorrent/tt-awesome/blob/1d86dbe5/tests/test_summarize_releases.py#L1-L13)

## Python Test Suite

The Python test suite is the primary gatekeeper for data integrity. It uses `pytest` to verify that all entries conform to the strict JSON schema and that the community-first sorting logic is applied correctly during README generation.

Key components include:

*   **Schema Validation**: Testing edge cases for `validate_entry` such as ID-filename matching and HTTPS enforcement [tests/test_validate.py 30-80](https://github.com/tenstorrent/tt-awesome/blob/1d86dbe5/tests/test_validate.py#L30-L80)
*   **README Logic**: Ensuring the `sort_entries` function maintains the `community` → `affiliated` → `official` hierarchy [tests/test_generate_readme.py 22-44](https://github.com/tenstorrent/tt-awesome/blob/1d86dbe5/tests/test_generate_readme.py#L22-L44)
*   **LLM Pipeline**: Using `unittest.mock` to simulate GitHub and Anthropic API responses for release summarization [tests/test_summarize_releases.py 148-173](https://github.com/tenstorrent/tt-awesome/blob/1d86dbe5/tests/test_summarize_releases.py#L148-L173)

For details, see [Python Test Suite](https://deepwiki.com/tenstorrent/tt-awesome/7.1-python-test-suite).

Sources: [tests/test_validate.py 12-23](https://github.com/tenstorrent/tt-awesome/blob/1d86dbe5/tests/test_validate.py#L12-L23)[tests/test_generate_readme.py 9-10](https://github.com/tenstorrent/tt-awesome/blob/1d86dbe5/tests/test_generate_readme.py#L9-L10)[tests/test_summarize_releases.py 77-95](https://github.com/tenstorrent/tt-awesome/blob/1d86dbe5/tests/test_summarize_releases.py#L77-L95)

## JavaScript Test Suite

The JavaScript tests ensure that the data processed by the Python scripts is correctly consumed by the Eleventy static site generator. These tests focus on the transformation layer between raw JSON and the Nunjucks templates.

Key components include:

*   **Filter Unit Tests**: Verifying the logic of custom Nunjucks filters like `planetItems` (which merges releases and articles) and `diversifiedFeatured` (which handles homepage showcase logic).
*   **Data Integrity**: Ensuring that `src/_data/entries.js` correctly merges GitHub metadata into the entry objects and handles missing fields gracefully [src/_data/entries.js 44-78](https://github.com/tenstorrent/tt-awesome/blob/1d86dbe5/src/_data/entries.js#L44-L78)

For details, see [JavaScript Test Suite](https://deepwiki.com/tenstorrent/tt-awesome/7.2-javascript-test-suite).

Sources: [src/_data/entries.js 8-24](https://github.com/tenstorrent/tt-awesome/blob/1d86dbe5/src/_data/entries.js#L8-L24)[src/_data/entries.js 84-93](https://github.com/tenstorrent/tt-awesome/blob/1d86dbe5/src/_data/entries.js#L84-L93)

## CI Integration

Testing is integrated into the GitHub Actions lifecycle to prevent regressions:

1.   **PR Gating**: `validate.yml` runs `scripts/validate.py` on every pull request. Failure to meet the schema blocks the merge [scripts/validate.py 144-146](https://github.com/tenstorrent/tt-awesome/blob/1d86dbe5/scripts/validate.py#L144-L146)
2.   **Nightly Checks**: The metadata fetchers and summarizers are tested via their respective suites before being allowed to update the `planet_feeds.json` or `github_meta.json` files.

Sources: [scripts/validate.py 104-113](https://github.com/tenstorrent/tt-awesome/blob/1d86dbe5/scripts/validate.py#L104-L113)[scripts/summarize_releases.py 150-165](https://github.com/tenstorrent/tt-awesome/blob/1d86dbe5/scripts/summarize_releases.py#L150-L165)

Dismiss
Refresh this wiki

Enter email to refresh
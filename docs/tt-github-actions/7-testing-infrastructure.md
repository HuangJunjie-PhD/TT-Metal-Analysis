---
project: tt-github-actions
github: https://github.com/tenstorrent/tt-github-actions
deepwiki: https://deepwiki.com/tenstorrent/tt-github-actions/7-testing-infrastructure
---

# Testing Infrastructure

Relevant source files
*   [.github/actions/collect_data/Readme.md](https://github.com/tenstorrent/tt-github-actions/blob/3ecf8e49/.github/actions/collect_data/Readme.md?plain=1)
*   [.github/actions/collect_data/pytest.ini](https://github.com/tenstorrent/tt-github-actions/blob/3ecf8e49/.github/actions/collect_data/pytest.ini)
*   [.github/actions/collect_data/requirements.txt](https://github.com/tenstorrent/tt-github-actions/blob/3ecf8e49/.github/actions/collect_data/requirements.txt)
*   [.github/actions/collect_data/src/parsers/junit_xml_utils.py](https://github.com/tenstorrent/tt-github-actions/blob/3ecf8e49/.github/actions/collect_data/src/parsers/junit_xml_utils.py)
*   [.github/actions/collect_data/test/test_generate_data.py](https://github.com/tenstorrent/tt-github-actions/blob/3ecf8e49/.github/actions/collect_data/test/test_generate_data.py)
*   [.github/workflows/test_collect_data_action.yml](https://github.com/tenstorrent/tt-github-actions/blob/3ecf8e49/.github/workflows/test_collect_data_action.yml)
*   [LICENSE](https://github.com/tenstorrent/tt-github-actions/blob/3ecf8e49/LICENSE)

The `collect_data` action utilizes a comprehensive test suite designed to ensure the integrity of CI/CD telemetry, performance benchmarks, and operation test (OpTest) results. The infrastructure is built around `pytest` and emphasizes end-to-end validation using "golden" data fixtures to prevent regressions in data transformation logic.

### Testing Strategy and Configuration

The test suite is configured to include the `src` directory in the Python path, allowing tests to interface directly with the action's core logic [pytest.ini 1-3](https://github.com/tenstorrent/tt-github-actions/blob/3ecf8e49/pytest.ini#L1-L3) Testing requirements include `pytest`, `pytest-cov` for coverage reporting, and `deepdiff` for deep object comparison [.github/actions/collect_data/requirements.txt 5-8](https://github.com/tenstorrent/tt-github-actions/blob/3ecf8e49/.github/actions/collect_data/requirements.txt#L5-L8)

The primary testing methodology involves:

1.   **Fixture-Based Execution**: Using historical GitHub API responses and artifacts stored in `test/data/` to simulate real-world workflow runs [.github/actions/collect_data/test/test_generate_data.py 30-32](https://github.com/tenstorrent/tt-github-actions/blob/3ecf8e49/.github/actions/collect_data/test/test_generate_data.py#L30-L32)
2.   **Golden File Comparison**: Comparing generated JSON/JSONL outputs against known-good "golden" files using `DeepDiff` to detect unauthorized schema or data changes [.github/actions/collect_data/test/test_generate_data.py 86-93](https://github.com/tenstorrent/tt-github-actions/blob/3ecf8e49/.github/actions/collect_data/test/test_generate_data.py#L86-L93)
3.   **Constraint Validation**: Enforcing data uniqueness constraints (e.g., ensuring no duplicate test entries exist for the same job and timestamp) [.github/actions/collect_data/test/test_generate_data.py 121-138](https://github.com/tenstorrent/tt-github-actions/blob/3ecf8e49/.github/actions/collect_data/test/test_generate_data.py#L121-L138)

### Test Execution & CI Integration

Tests are automatically executed via the `Test Collect Data` workflow on every pull request targeting `main` that modifies the `collect_data` action [.github/workflows/test_collect_data_action.yml 1-9](https://github.com/tenstorrent/tt-github-actions/blob/3ecf8e49/.github/workflows/test_collect_data_action.yml#L1-L9)

**Automated Test Pipeline**

| Step | Command / Action | Purpose |
| --- | --- | --- |
| **Environment Setup** | `actions/setup-python@v5` | Initializes Python 3.10 environment [.github/workflows/test_collect_data_action.yml 17-19](https://github.com/tenstorrent/tt-github-actions/blob/3ecf8e49/.github/workflows/test_collect_data_action.yml#L17-L19) |
| **Execution** | `pytest --cov=src test` | Runs suite and calculates coverage [.github/workflows/test_collect_data_action.yml 27-31](https://github.com/tenstorrent/tt-github-actions/blob/3ecf8e49/.github/workflows/test_collect_data_action.yml#L27-L31) |
| **Reporting** | `MishaKav/pytest-coverage-comment` | Posts coverage and test results to the PR [.github/workflows/test_collect_data_action.yml 33-37](https://github.com/tenstorrent/tt-github-actions/blob/3ecf8e49/.github/workflows/test_collect_data_action.yml#L33-L37) |

### Data Flow: Test to Code Entity Mapping

The following diagram illustrates how the testing infrastructure maps specific test files and functions to the production code entities they validate.

**Test Mapping Diagram**

Sources: [.github/actions/collect_data/test/test_generate_data.py 5-6](https://github.com/tenstorrent/tt-github-actions/blob/3ecf8e49/.github/actions/collect_data/test/test_generate_data.py#L5-L6)[.github/actions/collect_data/Readme.md 60-66](https://github.com/tenstorrent/tt-github-actions/blob/3ecf8e49/.github/actions/collect_data/Readme.md?plain=1#L60-L66)

### System Components and Test Data

The testing system bridges raw GitHub data (JSON) and parsed artifacts (XML/TAR) to verified Pydantic models.

**Infrastructure Component Interaction**

Sources: [.github/actions/collect_data/test/test_generate_data.py 13-22](https://github.com/tenstorrent/tt-github-actions/blob/3ecf8e49/.github/actions/collect_data/test/test_generate_data.py#L13-L22)[.github/actions/collect_data/test/test_generate_data.py 91-93](https://github.com/tenstorrent/tt-github-actions/blob/3ecf8e49/.github/actions/collect_data/test/test_generate_data.py#L91-L93)[.github/actions/collect_data/test/test_generate_data.py 121-138](https://github.com/tenstorrent/tt-github-actions/blob/3ecf8e49/.github/actions/collect_data/test/test_generate_data.py#L121-L138)

* * *

### Child Pages

Detailed documentation for specific test suites is available in the following sub-pages:

*   **[CICD & Benchmark Test Suite](https://deepwiki.com/tenstorrent/tt-github-actions/7.1-cicd-and-benchmark-test-suite)** Covers end-to-end validation of the pipeline and benchmark data generation. Details the use of `DeepDiff` for golden-file comparisons and the enforcement of uniqueness constraints on `github_job_id` and `test_start_ts`. For details, see [CICD & Benchmark Test Suite](https://deepwiki.com/tenstorrent/tt-github-actions/7.1-cicd-and-benchmark-test-suite).

*   **[Parser Test Suite](https://deepwiki.com/tenstorrent/tt-github-actions/7.2-parser-test-suite)** Covers unit and integration tests for the various report parsers (JUnit XML, TAR archives). Details how the system selects parsers based on builder types and branch gating logic. For details, see [Parser Test Suite](https://deepwiki.com/tenstorrent/tt-github-actions/7.2-parser-test-suite).

Dismiss
Refresh this wiki

Enter email to refresh
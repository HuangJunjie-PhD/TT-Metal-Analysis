---
project: tt-perf-report
github: https://github.com/tenstorrent/tt-perf-report
deepwiki: https://deepwiki.com/tenstorrent/tt-perf-report/8-development-guide
---

# Development Guide

Relevant source files
*   [.github/workflows/build-pypi.yml](https://github.com/tenstorrent/tt-perf-report/blob/076a1b39/.github/workflows/build-pypi.yml)
*   [pyproject.toml](https://github.com/tenstorrent/tt-perf-report/blob/076a1b39/pyproject.toml)
*   [src/tt_perf_report/__init__.py](https://github.com/tenstorrent/tt-perf-report/blob/076a1b39/src/tt_perf_report/__init__.py)

## Purpose

This document provides guidance for developers who want to contribute to or modify the tt-perf-report tool. It covers development environment setup, codebase structure, contribution workflow, and instructions for extending the tool's functionality.

For information on using the tool, see the [Usage Guide](https://deepwiki.com/tenstorrent/tt-perf-report/3-usage-guide), and for details on the tool's internal architecture, see the [Architecture](https://deepwiki.com/tenstorrent/tt-perf-report/7-architecture) section.

## Development Environment Setup

### Prerequisites

Based on the project configuration, you will need:

*   Python 3.x
*   Dependencies: pandas

### Local Development Setup

1.   Clone the repository:

`git clone https://github.com/tenstorrent/tt-perf-report.gitcd tt-perf-report`
1.   Install the package in development mode:

`pip install -e .`
This installs the package in editable mode, allowing you to make changes to the code and see them reflected immediately when you run the `tt-perf-report` command.

Sources: [pyproject.toml 1-23](https://github.com/tenstorrent/tt-perf-report/blob/076a1b39/pyproject.toml#L1-L23)

## Repository Structure

The following diagram shows the organization of the tt-perf-report repository:

### Repository Layout Diagram

The main code is in the `src/tt_perf_report` directory, with the main entry point being `perf_report.py`.

Sources: [pyproject.toml 14-15](https://github.com/tenstorrent/tt-perf-report/blob/076a1b39/pyproject.toml#L14-L15)[pyproject.toml 20-22](https://github.com/tenstorrent/tt-perf-report/blob/076a1b39/pyproject.toml#L20-L22)

## Code Architecture

The tt-perf-report tool consists of several core components that work together to analyze performance traces. Understanding these components is crucial for making modifications or extending the tool.

### Code Component Diagram

### Key Components

1.   **CLI Entry Point**: The `main()` function in `perf_report.py` that parses command-line arguments and orchestrates the workflow.
2.   **Trace Parser**: Responsible for reading CSV performance trace files and converting them to a pandas DataFrame.
3.   **Performance Analyzer**: Analyzes different operation types (Matmul, Conv, Halo) to calculate metrics like DRAM bandwidth, FLOPs, and utilization.
4.   **Performance Classifier**: Categorizes operations as DRAM-bound, FLOP-bound, both, or slow, to identify bottlenecks.
5.   **Advice Generator**: Provides tailored optimization suggestions based on the bottleneck classifications.
6.   **Report Generator**: Formats and displays the analysis results with performance indicators.

Sources: Inferred from system architecture diagrams

## Data Processing Pipeline

The following diagram illustrates the data flow through the tt-perf-report system:

### Data Pipeline Diagram

Understanding this pipeline is essential when modifying the tool or adding support for new operation types.

Sources: Inferred from data processing pipeline diagram

## Contribution Workflow

### Version Control Workflow

1.   Fork the repository on GitHub.
2.   Clone your fork locally.
3.   Create a new branch for your feature or bugfix.
4.   Make your changes, following the code structure and style.
5.   Test your changes thoroughly.
6.   Commit your changes with clear, descriptive commit messages.
7.   Push your branch to your fork.
8.   Create a pull request against the main repository.

### Building the Package

To build the package locally:

`pip install buildpython -m build`
This will create distribution files in the `dist/` directory.

### CI/CD Pipeline

The repository uses GitHub Actions for continuous integration and deployment:

### CI/CD Workflow Diagram

Sources: [.github/workflows/build-pypi.yml 1-53](https://github.com/tenstorrent/tt-perf-report/blob/076a1b39/.github/workflows/build-pypi.yml#L1-L53)

## Extending the Tool

### Adding New Operation Types

To add support for a new operation type:

1.   Create a new analyzer class in the appropriate module.
2.   Implement the required metrics calculation methods.
3.   Register the new operation type with the main analysis pipeline.
4.   Update the classification system to handle the new metrics.
5.   Add appropriate optimization advice for the new operation type.

### New Operation Type Implementation Diagram

### Modifying the Classification System

The classification system categorizes operations based on their performance characteristics:

### Classification System Diagram

To modify this system:

1.   Update the classification thresholds or criteria.
2.   Adjust the visualization/reporting of the classifications.
3.   Update the advice generation to reflect your changes.

Sources: Inferred from key classification system diagram

### Enhancing the Advice System

To improve or extend the optimization advice:

1.   Identify new patterns or bottlenecks that warrant specific advice.
2.   Add new advice logic based on performance metrics and classifications.
3.   Update the report generation to include the new advice.
4.   Consider adding new visualization or highlighting for critical issues.

## Testing Your Changes

### Manual Testing

1.   Run the tool on sample CSV trace files.
2.   Verify that the output matches expectations.
3.   Check that performance metrics are calculated correctly.
4.   Ensure that any new features work as intended.

### Automated Testing

While we don't have explicit information about test suites, it's recommended to:

1.   Write unit tests for any new functionality.
2.   Run existing tests to ensure your changes don't break anything.
3.   Consider adding integration tests for complex features.

## Documentation

When adding new features or making significant changes:

1.   Update the README.md with any new command-line options or features.
2.   Add comments to your code explaining complex logic.
3.   Consider updating or adding to the wiki documentation.

## Building and Publishing

The package uses setuptools for building and is published to PyPI through GitHub Actions.

### Version Update Process

To update the package version:

1.   Update the version number in `pyproject.toml`.
2.   Commit the change.
3.   Tag the commit with the same version number.
4.   Push the tag to trigger the publishing workflow.

| Action | Command/Location |
| --- | --- |
| Update Version | Edit `pyproject.toml`, line 7 |
| Tag Version | `git tag v1.0.x` |
| Push Tag | `git push origin v1.0.x` |
| Check Workflow | GitHub Actions tab |

Sources: [pyproject.toml 5-7](https://github.com/tenstorrent/tt-perf-report/blob/076a1b39/pyproject.toml#L5-L7)[.github/workflows/build-pypi.yml 33-52](https://github.com/tenstorrent/tt-perf-report/blob/076a1b39/.github/workflows/build-pypi.yml#L33-L52)

## Troubleshooting Common Development Issues

### Package Installation Issues

If you encounter issues with the development installation:

1.   Ensure you have the correct Python version.
2.   Try installing dependencies manually: `pip install pandas`.
3.   Check for any error messages during installation.

### GitHub Actions Failures

If the GitHub Actions workflow fails:

1.   Check the build logs for specific error messages.
2.   Ensure your package builds locally before pushing.
3.   Verify that all dependencies are properly specified in `pyproject.toml`.

Dismiss
Refresh this wiki

Enter email to refresh
---
project: luwen
github: https://github.com/tenstorrent/luwen
deepwiki: https://deepwiki.com/tenstorrent/luwen/7-development-guide
---

# Development Guide

Relevant source files
*   [.gitlab-ci.yml](https://github.com/tenstorrent/luwen/blob/55e35616/.gitlab-ci.yml)
*   [.pre-commit-config.yaml](https://github.com/tenstorrent/luwen/blob/55e35616/.pre-commit-config.yaml)
*   [.tito/packages/.readme](https://github.com/tenstorrent/luwen/blob/55e35616/.tito/packages/.readme)
*   [.tito/tito.props](https://github.com/tenstorrent/luwen/blob/55e35616/.tito/tito.props)
*   [Cargo.toml](https://github.com/tenstorrent/luwen/blob/55e35616/Cargo.toml)
*   [README.md](https://github.com/tenstorrent/luwen/blob/55e35616/README.md?plain=1)
*   [crates/pyluwen/Cargo.toml](https://github.com/tenstorrent/luwen/blob/55e35616/crates/pyluwen/Cargo.toml)

This document provides comprehensive guidance for developers who want to work with or contribute to the Luwen project. It covers development environment setup, building instructions, testing procedures, CI/CD workflows, and contribution guidelines. For information about the overall architecture and functionality of Luwen, see [Architecture](https://deepwiki.com/tenstorrent/luwen/2-architecture), and for detailed API usage, refer to the [Language Bindings](https://deepwiki.com/tenstorrent/luwen/5-language-bindings) documentation.

## Development Environment Setup

Setting up a proper development environment is the first step for working with Luwen.

### Prerequisites

The following tools and dependencies are required:

*   Rust toolchain (stable channel recommended)
*   Cargo (Rust package manager)
*   Protobuf compiler (`protobuf-compiler` package)
*   Git (version control)
*   Docker (optional, for container-based builds)
*   Pre-commit (for code quality checks)

### Recommended Development Tools

*   Visual Studio Code with Rust-Analyzer extension
*   LLDB for debugging
*   Clippy for Rust linting
*   Rustfmt for code formatting

### Installing Pre-commit Hooks

Luwen uses pre-commit hooks to ensure code quality. To set these up:

`pip install pre-commitpre-commit install`
The hooks perform the following checks:

*   Trailing whitespace and end-of-file fixing
*   YAML validation
*   Rust formatting (rustfmt)
*   Cargo check for compilation errors
*   Clippy for code quality issues

Sources: [.pre-commit-config.yaml 1-20](https://github.com/tenstorrent/luwen/blob/55e35616/.pre-commit-config.yaml#L1-L20)

## Project Structure

Luwen is organized as a Rust workspace with multiple crates.

The workspace is defined in the main Cargo.toml file and includes all crates in the `crates/` and `app/` directories.

Sources: [Cargo.toml 25-32](https://github.com/tenstorrent/luwen/blob/55e35616/Cargo.toml#L25-L32)

## Building Luwen

Luwen can be built using Cargo, with various configurations available.

### Basic Build Commands

`# Debug buildcargo build # Release buildcargo build --release # Build a specific targetcargo build --target x86_64-unknown-linux-gnu --release`
### Build Profiles

Luwen supports different build profiles:

| Profile | Purpose | Command |
| --- | --- | --- |
| Debug | Development and debugging | `cargo build` |
| Release | Optimized production code | `cargo build --release` |

### Building Python Bindings

The `pyluwen` crate provides Python bindings for Luwen:

`cd crates/pyluwencargo build --release`
This will generate a Python module that can be imported in Python code.

Sources: [Cargo.toml 11-24](https://github.com/tenstorrent/luwen/blob/55e35616/Cargo.toml#L11-L24)[crates/pyluwen/Cargo.toml 1-19](https://github.com/tenstorrent/luwen/blob/55e35616/crates/pyluwen/Cargo.toml#L1-L19)

## Testing

Luwen has a comprehensive testing strategy that includes unit tests and hardware-specific tests.

### Running Tests

Basic test commands:

`# Run all testscargo test --workspace # Run tests for a specific cratecargo test -p luwen-core # Run tests with release profilecargo test --release`
### Hardware-Specific Tests

Luwen includes tests that require specific hardware:

*   GraySkull tests (requires gs-1-card)
*   Wormhole tests (requires wh-nebula_x2-card)

These tests are typically run in the CI/CD pipeline with the appropriate hardware tags.

Sources: [.gitlab-ci.yml 80-98](https://github.com/tenstorrent/luwen/blob/55e35616/.gitlab-ci.yml#L80-L98)

## Continuous Integration Pipeline

Luwen uses GitLab CI/CD for continuous integration and deployment.

### Pipeline Stages

1.   **Build Stage**: Compiles the code in different configurations (release/debug, stable/nightly)
2.   **Test Stage**: Runs tests on different hardware platforms
3.   **Deploy Stage**: Creates deployment packages and artifacts

### CI Variables

The pipeline uses several predefined variables:

| Variable | Purpose | Value |
| --- | --- | --- |
| RUST_BACKTRACE | Enables detailed backtraces | 1 |
| CHANNEL | Rust channel | stable, nightly |
| PROFILE | Build profile | release, debug |
| TARGET | Target architecture | x86_64-unknown-linux-gnu |

Sources: [.gitlab-ci.yml 1-136](https://github.com/tenstorrent/luwen/blob/55e35616/.gitlab-ci.yml#L1-L136)

## Versioning and Releases

Luwen uses semantic versioning for releases. The current version is defined in the Cargo.toml file.

```
version = "0.6.4"
```

### Release Tools

Tito is used for managing releases:

`# Tag a new releasetito tag # Build a package from the latest tagtito build`
Sources: [Cargo.toml 1-8](https://github.com/tenstorrent/luwen/blob/55e35616/Cargo.toml#L1-L8)[.tito/tito.props 1-6](https://github.com/tenstorrent/luwen/blob/55e35616/.tito/tito.props#L1-L6)

## Debugging and Troubleshooting

When developing with Luwen, you may encounter issues that require debugging.

### Rust Backtraces

Enable detailed Rust backtraces by setting:

`export RUST_BACKTRACE=1`
This is automatically set in the CI/CD pipeline.

### Common Issues

1.   **Missing Protobuf Compiler**: Install with `apt install -y protobuf-compiler`
2.   **Hardware Not Detected**: Ensure appropriate kernel modules are loaded
3.   **Build Failures on Nightly**: The nightly Rust channel may have incompatibilities, try using stable

Sources: [.gitlab-ci.yml 41-43](https://github.com/tenstorrent/luwen/blob/55e35616/.gitlab-ci.yml#L41-L43)

## Contributing Guidelines

When contributing to Luwen, please follow these guidelines:

### Code Style

*   Follow Rust's official style guide
*   Use rustfmt to format your code
*   Run clippy to catch common mistakes
*   Address all warnings before submitting code

### Pull Request Process

1.   Create a branch from `main`
2.   Make your changes
3.   Ensure all tests pass
4.   Submit a pull request with a clear description of changes

### Commit Message Guidelines

*   Use descriptive commit messages
*   Reference issue numbers when applicable
*   Keep changes focused and atomic

Sources: [.pre-commit-config.yaml 1-20](https://github.com/tenstorrent/luwen/blob/55e35616/.pre-commit-config.yaml#L1-L20)

## Conclusion

This development guide provides the essential information needed to work with and contribute to the Luwen project. By following these guidelines, you can ensure that your development process aligns with the project's standards and practices.

For further assistance, consult the [Overview](https://deepwiki.com/tenstorrent/luwen/1-overview) and [Architecture](https://deepwiki.com/tenstorrent/luwen/2-architecture) documentation or reach out to the project maintainers.

Dismiss
Refresh this wiki

Enter email to refresh
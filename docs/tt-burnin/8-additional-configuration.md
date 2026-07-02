---
project: tt-burnin
github: https://github.com/tenstorrent/tt-burnin
deepwiki: https://deepwiki.com/tenstorrent/tt-burnin/8-additional-configuration
---

# Additional Configuration

Relevant source files
*   [.gitlab-ci.yml](https://github.com/tenstorrent/tt-burnin/blob/809f293d/.gitlab-ci.yml)
*   [debian/py3dist-overrides](https://github.com/tenstorrent/tt-burnin/blob/809f293d/debian/py3dist-overrides)

This document covers miscellaneous configuration files and development tooling that support the build, packaging, and security processes of the tt-burnin project. These configurations complement the main build and release infrastructure documented in other sections.

For comprehensive information about the main build system, see [Build System](https://deepwiki.com/tenstorrent/tt-burnin/6.1-build-system). For details about CI/CD workflows, see [CI/CD Workflows](https://deepwiki.com/tenstorrent/tt-burnin/5.2-cicd-workflows). For Debian packaging processes, see [Debian Packaging](https://deepwiki.com/tenstorrent/tt-burnin/5.1-debian-packaging).

## Configuration Overview

The tt-burnin project includes several auxiliary configuration files that handle specific aspects of package management, security scanning, and build tooling. These files work together to ensure proper dependency mapping, security compliance, and development workflow automation.

**Configuration Integration Flow**

Sources: [debian/py3dist-overrides 1-3](https://github.com/tenstorrent/tt-burnin/blob/809f293d/debian/py3dist-overrides#L1-L3)[.gitlab-ci.yml 1-14](https://github.com/tenstorrent/tt-burnin/blob/809f293d/.gitlab-ci.yml#L1-L14)

## Debian Package Dependency Mapping

The `py3dist-overrides` file provides explicit mapping between Python package names and their corresponding Debian package names. This configuration ensures correct dependency resolution during Debian package creation.

| Python Package | Debian Package |
| --- | --- |
| `pyluwen` | `python3-pyluwen` |
| `tt_tools_common` | `python3-tt-tools-common` |

### Package Mapping Configuration

The [debian/py3dist-overrides 1-2](https://github.com/tenstorrent/tt-burnin/blob/809f293d/debian/py3dist-overrides#L1-L2) file contains two key dependency mappings:

*   **pyluwen mapping**: Maps the `pyluwen` Python package to the `python3-pyluwen` Debian package, ensuring the hardware interface library is correctly referenced in Debian dependencies
*   **tt_tools_common mapping**: Maps the `tt_tools_common` Python package to the `python3-tt-tools-common` Debian package, maintaining proper linkage to shared Tenstorrent utilities

This mapping is essential for the Debian packaging system to correctly resolve and install dependencies when the tt-burnin package is installed via `.deb` files.

Sources: [debian/py3dist-overrides 1-3](https://github.com/tenstorrent/tt-burnin/blob/809f293d/debian/py3dist-overrides#L1-L3)

## Security and CI Configuration

The project includes GitLab CI configuration for automated security scanning and testing processes. The [.gitlab-ci.yml 8-13](https://github.com/tenstorrent/tt-burnin/blob/809f293d/.gitlab-ci.yml#L8-L13) file defines a minimal CI pipeline focused on security analysis.

### Security Scanning Pipeline

**GitLab CI Security Pipeline Structure**

### CI Configuration Components

The GitLab CI configuration provides several key security features:

*   **SAST Integration**: The [.gitlab-ci.yml 13](https://github.com/tenstorrent/tt-burnin/blob/809f293d/.gitlab-ci.yml#L13-L13) includes the `Security/SAST.gitlab-ci.yml` template for static application security testing
*   **Test Stage**: A dedicated [.gitlab-ci.yml 9](https://github.com/tenstorrent/tt-burnin/blob/809f293d/.gitlab-ci.yml#L9-L9) test stage that runs the security analysis job
*   **Customization Support**: Comments at [.gitlab-ci.yml 2-7](https://github.com/tenstorrent/tt-burnin/blob/809f293d/.gitlab-ci.yml#L2-L7) provide guidance for customizing security scanning settings

### Security Scanning Capabilities

The included SAST template enables multiple security analysis features as referenced in the configuration comments:

*   **Static Application Security Testing (SAST)**: Analyzes source code for security vulnerabilities
*   **Secret Detection**: Scans for accidentally committed secrets and credentials
*   **Dependency Scanning**: Checks project dependencies for known vulnerabilities
*   **Container Scanning**: Analyzes container images for security issues

Sources: [.gitlab-ci.yml 1-14](https://github.com/tenstorrent/tt-burnin/blob/809f293d/.gitlab-ci.yml#L1-L14)

## Configuration File Relationships

The auxiliary configuration files integrate with the main build and release systems to provide comprehensive project management capabilities.

**Configuration Integration Architecture**

These configuration files work together to ensure that:

1.   **Dependency Resolution**: Python package names are correctly mapped to Debian packages during build processes
2.   **Security Compliance**: Automated security scanning validates code quality and dependency safety
3.   **Build Integration**: Configuration settings support both local development and automated CI/CD workflows

Sources: [debian/py3dist-overrides 1-3](https://github.com/tenstorrent/tt-burnin/blob/809f293d/debian/py3dist-overrides#L1-L3)[.gitlab-ci.yml 8-14](https://github.com/tenstorrent/tt-burnin/blob/809f293d/.gitlab-ci.yml#L8-L14)

Dismiss
Refresh this wiki

Enter email to refresh
---
project: tt-installer
github: https://github.com/tenstorrent/tt-installer
deepwiki: https://deepwiki.com/tenstorrent/tt-installer/6-operating-system-compatibility
---

# Operating System Compatibility

Relevant source files
*   [README.md](https://github.com/tenstorrent/tt-installer/blob/19fdd6a2/README.md?plain=1)
*   [install.sh](https://github.com/tenstorrent/tt-installer/blob/19fdd6a2/install.sh)

This page documents the operating system compatibility for the Tenstorrent Software Stack Installer (`tt-installer`). It covers which Linux distributions are supported, their support status, any distribution-specific considerations, and how the installer handles different distributions. For information about customizing your installation with environment variables, see [Configuration Options](https://deepwiki.com/tenstorrent/tt-installer/4-configuration-options).

## Support Status Overview

The Tenstorrent Software Stack has varying levels of compatibility with different Linux distributions. Ubuntu 22.04 LTS is the preferred operating system with full support, while other distributions have different levels of support.

Sources: [README.md 60-72](https://github.com/tenstorrent/tt-installer/blob/19fdd6a2/README.md?plain=1#L60-L72)[install.sh 465-468](https://github.com/tenstorrent/tt-installer/blob/19fdd6a2/install.sh#L465-L468)

## Compatibility Matrix

The following table provides detailed information about compatibility status and notes for each supported distribution:

| OS | Version | Support Status | Notes |
| --- | --- | --- | --- |
| Ubuntu | 24.04.2 LTS | Full | Fully supported with all features |
| Ubuntu | 22.04.5 LTS | Full | Preferred OS with all features |
| Ubuntu | 20.04.6 LTS | Limited | - Deprecated; support will be removed in future - Cannot install Metalium - No pipx support |
| Debian | 12.10.0 | Limited | - Curl not installed by default - Requires manual rustc/cargo installation - Use rustup for modern versions |
| Fedora | 41/42 | Limited | May require restart after base package installation |
| Other DEB-based | Various | Unofficial | May work but not officially supported |
| Other RPM-based | Various | Unofficial | May work but not officially supported |

Sources: [README.md 60-72](https://github.com/tenstorrent/tt-installer/blob/19fdd6a2/README.md?plain=1#L60-L72)[install.sh 465-468](https://github.com/tenstorrent/tt-installer/blob/19fdd6a2/install.sh#L465-L468)[install.sh 470-473](https://github.com/tenstorrent/tt-installer/blob/19fdd6a2/install.sh#L470-L473)

## Distribution Detection Process

The installer automatically detects your Linux distribution and applies the appropriate installation process for your system.

Sources: [install.sh 176-186](https://github.com/tenstorrent/tt-installer/blob/19fdd6a2/install.sh#L176-L186)[install.sh 188-195](https://github.com/tenstorrent/tt-installer/blob/19fdd6a2/install.sh#L188-L195)[install.sh 436-463](https://github.com/tenstorrent/tt-installer/blob/19fdd6a2/install.sh#L436-L463)[install.sh 465-468](https://github.com/tenstorrent/tt-installer/blob/19fdd6a2/install.sh#L465-L468)[install.sh 470-473](https://github.com/tenstorrent/tt-installer/blob/19fdd6a2/install.sh#L470-L473)

## Distribution-Specific Installation Paths

The installer uses different package management commands and package lists depending on the detected distribution.

Sources: [install.sh 436-463](https://github.com/tenstorrent/tt-installer/blob/19fdd6a2/install.sh#L436-L463)[install.sh 580-602](https://github.com/tenstorrent/tt-installer/blob/19fdd6a2/install.sh#L580-L602)

## Distribution-Specific Considerations

### Ubuntu

#### Ubuntu 22.04 LTS and 24.04 LTS

*   **Support Status**: Full support
*   **Installation**: All components can be installed
*   **Recommendation**: Ubuntu 22.04 LTS is the preferred OS for Tenstorrent hardware

#### Ubuntu 20.04 LTS

*   **Support Status**: Limited support, deprecated
*   **Limitations**: 
    *   Metalium container cannot be installed
    *   pipx installation is not supported
    *   Support will be removed in a future release

*   **Installation**: The installer automatically detects Ubuntu 20 and applies the appropriate limitations

Sources: [install.sh 465-468](https://github.com/tenstorrent/tt-installer/blob/19fdd6a2/install.sh#L465-L468)[install.sh 483-486](https://github.com/tenstorrent/tt-installer/blob/19fdd6a2/install.sh#L483-L486)[README.md 66](https://github.com/tenstorrent/tt-installer/blob/19fdd6a2/README.md?plain=1#L66-L66)

### Debian

#### Debian 12

*   **Support Status**: Limited support with notes
*   **Prerequisites**: 
    *   curl is not installed by default (needed if using curl to download the installer)
    *   The packaged rustc/cargo versions are too old for installation

*   **Special Steps**: 
    *   Install rustc/cargo using rustup: [https://rustup.rs/](https://rustup.rs/)
    *   The installer will warn about this requirement but won't install it automatically

Sources: [install.sh 470-473](https://github.com/tenstorrent/tt-installer/blob/19fdd6a2/install.sh#L470-L473)[README.md 67](https://github.com/tenstorrent/tt-installer/blob/19fdd6a2/README.md?plain=1#L67-L67)

### Fedora/RHEL

#### Fedora 41/42

*   **Support Status**: Limited support with notes
*   **Considerations**: 
    *   May require a restart after base package installation
    *   Uses dnf package manager for installations

Sources: [install.sh 452-453](https://github.com/tenstorrent/tt-installer/blob/19fdd6a2/install.sh#L452-L453)[README.md 68-69](https://github.com/tenstorrent/tt-installer/blob/19fdd6a2/README.md?plain=1#L68-L69)

#### RHEL/CentOS

*   **Support Status**: Unofficial
*   **Installation Process**: 
    *   Installs EPEL repository first
    *   Then installs required packages

Sources: [install.sh 455-457](https://github.com/tenstorrent/tt-installer/blob/19fdd6a2/install.sh#L455-L457)

## How the Installer Handles Distribution Detection

The installer uses the following process to detect and handle different distributions:

1.   Reads `/etc/os-release` to determine distribution ID and version
2.   Performs special handling for Ubuntu 20.04 (flagged as deprecated)
3.   Applies distribution-specific package installation commands
4.   Adapts installation options based on distribution (e.g., disabling Metalium on Ubuntu 20.04)
5.   Uses appropriate package formats (.deb or .rpm) for HugePages setup

Key code sections handling this process:

*   Distribution detection: [install.sh 176-186](https://github.com/tenstorrent/tt-installer/blob/19fdd6a2/install.sh#L176-L186)
*   Ubuntu 20 check: [install.sh 188-195](https://github.com/tenstorrent/tt-installer/blob/19fdd6a2/install.sh#L188-L195)
*   Distribution-specific package installation: [install.sh 436-463](https://github.com/tenstorrent/tt-installer/blob/19fdd6a2/install.sh#L436-L463)
*   Distribution-specific warnings: [install.sh 465-473](https://github.com/tenstorrent/tt-installer/blob/19fdd6a2/install.sh#L465-L473)
*   Distribution-specific HugePages setup: [install.sh 580-602](https://github.com/tenstorrent/tt-installer/blob/19fdd6a2/install.sh#L580-L602)

## Recommendations

1.   **Preferred OS**: Ubuntu 22.04 LTS is the recommended operating system for the most complete and well-tested experience
2.   **Ubuntu 20.04 users**: Consider upgrading to a newer Ubuntu version to maintain full support and access all features
3.   **Debian users**: Install rustc/cargo using rustup before running the installer
4.   **Fedora users**: Be prepared to restart your system after the base package installation if required

## Troubleshooting OS-Specific Issues

For troubleshooting installation issues related to specific operating systems, refer to the [Troubleshooting](https://deepwiki.com/tenstorrent/tt-installer/7-troubleshooting) wiki page.

Dismiss
Refresh this wiki

Enter email to refresh
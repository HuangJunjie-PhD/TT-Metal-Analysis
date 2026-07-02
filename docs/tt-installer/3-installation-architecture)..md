---
project: tt-installer
github: https://github.com/tenstorrent/tt-installer
deepwiki: https://deepwiki.com/tenstorrent/tt-installer/3-installation-architecture).
---

# Installation Architecture

Relevant source files
*   [README.md](https://github.com/tenstorrent/tt-installer/blob/19fdd6a2/README.md?plain=1)
*   [install.sh](https://github.com/tenstorrent/tt-installer/blob/19fdd6a2/install.sh)

## Purpose and Scope

This document explains the overall architecture of the Tenstorrent installation system implemented in the `tt-installer` repository. It covers the high-level components, installation process flow, component dependencies, and configuration system. This document focuses on the architectural design rather than step-by-step usage instructions. For usage instructions, see [Installation Guide](https://deepwiki.com/tenstorrent/tt-installer/2-installation-guide).

Sources: [README.md 1-9](https://github.com/tenstorrent/tt-installer/blob/19fdd6a2/README.md?plain=1#L1-L9)

## Overall Architecture

The tt-installer architecture centers around a main shell script (`install.sh`) that orchestrates the installation of multiple components required for using Tenstorrent hardware. The system is designed to be flexible, supporting various operating systems, installation modes, and configuration options.

The architecture consists of three primary layers:

1.   **Core Installer System** - The main script, configuration, logging, and mode systems
2.   **External Dependencies** - Systems and repositories the installer interacts with
3.   **Installed Components** - The actual Tenstorrent software stack components that get installed

Sources: [install.sh 1-20](https://github.com/tenstorrent/tt-installer/blob/19fdd6a2/install.sh#L1-L20)[install.sh 390-658](https://github.com/tenstorrent/tt-installer/blob/19fdd6a2/install.sh#L390-L658)

## Installation Process Flow

The installation process follows a sequential flow with optional steps that can be skipped based on user preferences or environment variables. The process is designed to be resilient, with appropriate error handling at each stage.

The installation process begins with initialization and environment setup, followed by a series of steps that install and configure various components. Each step is conditional and can be skipped based on configuration parameters.

Sources: [install.sh 390-656](https://github.com/tenstorrent/tt-installer/blob/19fdd6a2/install.sh#L390-L656)[install.sh 529-627](https://github.com/tenstorrent/tt-installer/blob/19fdd6a2/install.sh#L529-L627)

## Component Architecture

The tt-installer installs and configures several components that form the Tenstorrent software stack. These components have specific dependencies and interact with each other to provide a complete environment for using Tenstorrent hardware.

Component interactions:

*   **Kernel-Mode Driver (KMD)**: Provides kernel-level access to Tenstorrent hardware
*   **HugePages**: Configured for optimal memory performance with Tenstorrent hardware
*   **tt-flash**: Uses KMD to access and update card firmware
*   **tt-smi**: Uses KMD to monitor and manage Tenstorrent hardware
*   **Podman**: Container runtime used to host the tt-metalium development environment
*   **tt-metalium**: Provides a complete development environment for Tenstorrent's TTNN framework

Sources: [install.sh 55-67](https://github.com/tenstorrent/tt-installer/blob/19fdd6a2/install.sh#L55-L67)[README.md 23-32](https://github.com/tenstorrent/tt-installer/blob/19fdd6a2/README.md?plain=1#L23-L32)

## Configuration System

The tt-installer is highly configurable through environment variables. These variables allow users to customize the installation process to suit their specific needs.

The configuration system uses environment variables with the `TT_` prefix to control various aspects of the installation process. For example, `TT_MODE_NON_INTERACTIVE=0` enables non-interactive mode, and `TT_SKIP_INSTALL_KMD=0` skips the KMD installation step.

Sources: [install.sh 45-91](https://github.com/tenstorrent/tt-installer/blob/19fdd6a2/install.sh#L45-L91)[README.md 36-55](https://github.com/tenstorrent/tt-installer/blob/19fdd6a2/README.md?plain=1#L36-L55)

## Installation Modes

The installer supports three main operational modes that affect how it behaves during installation:

| Mode | Environment Variable | Description | Key Effects |
| --- | --- | --- | --- |
| Interactive | Default | Prompts user for choices | Asks about Python environment, component installation, reboot |
| Non-Interactive | `TT_MODE_NON_INTERACTIVE=0` | No user prompts | Uses defaults for all choices, never reboots unless specified |
| Container | `TT_MODE_CONTAINER=0` | For running in containers | Skips KMD, HugePages, Podman; never reboots |

Mode detection happens early in the installation process and affects many downstream decisions, such as which components to install and whether to prompt the user.

Sources: [install.sh 94-113](https://github.com/tenstorrent/tt-installer/blob/19fdd6a2/install.sh#L94-L113)[install.sh 398-426](https://github.com/tenstorrent/tt-installer/blob/19fdd6a2/install.sh#L398-L426)

## Python Environment Options

The installer provides several options for configuring the Python environment used to install Python-based tools like tt-flash and tt-smi:

The Python environment choice determines how Python packages are installed and managed. Options include using an existing virtual environment, creating a new virtual environment, using the system Python installation, or using pipx for isolated package installation.

Sources: [install.sh 76-84](https://github.com/tenstorrent/tt-installer/blob/19fdd6a2/install.sh#L76-L84)[install.sh 222-251](https://github.com/tenstorrent/tt-installer/blob/19fdd6a2/install.sh#L222-L251)[install.sh 489-525](https://github.com/tenstorrent/tt-installer/blob/19fdd6a2/install.sh#L489-L525)

## Logging System

The installer implements a logging system that captures all installation steps and errors for troubleshooting:

The logging system uses a combination of colored output to the terminal and plain text output to a log file. All stdout and stderr output is captured, stripped of color codes, timestamped, and written to the log file.

Sources: [install.sh 121-167](https://github.com/tenstorrent/tt-installer/blob/19fdd6a2/install.sh#L121-L167)[install.sh 629-631](https://github.com/tenstorrent/tt-installer/blob/19fdd6a2/install.sh#L629-L631)

## Error Handling

The installer includes error handling mechanisms to ensure the installation process can recover from failures or exit gracefully when needed:

Error handling is implemented through functions like `error()`, `error_exit()`, and through shell command error checking with constructs like `set -euo pipefail`. Critical errors like sudo permission failures will terminate the installation, while non-critical errors may allow the installation to continue with warnings.

Sources: [install.sh 5](https://github.com/tenstorrent/tt-installer/blob/19fdd6a2/install.sh#L5-L5)[install.sh 146-161](https://github.com/tenstorrent/tt-installer/blob/19fdd6a2/install.sh#L146-L161)[install.sh 169-174](https://github.com/tenstorrent/tt-installer/blob/19fdd6a2/install.sh#L169-L174)

## Operating System Compatibility

The installer supports multiple Linux distributions with varying levels of compatibility:

| OS | Version | Support Level | Notes |
| --- | --- | --- | --- |
| Ubuntu | 22.04.5 LTS | Full | Preferred OS |
| Ubuntu | 24.04.2 LTS | Full |  |
| Ubuntu | 20.04.6 LTS | Limited | Deprecated, no Metalium support |
| Debian | 12.10.0 | Limited | Needs modern rustc from rustup |
| Fedora | 41/42 | Limited | May require restart after package install |

The installer adapts its behavior based on the detected operating system, installing different packages and using different commands as needed.

Sources: [install.sh 176-195](https://github.com/tenstorrent/tt-installer/blob/19fdd6a2/install.sh#L176-L195)[install.sh 434-468](https://github.com/tenstorrent/tt-installer/blob/19fdd6a2/install.sh#L434-L468)[README.md 59-72](https://github.com/tenstorrent/tt-installer/blob/19fdd6a2/README.md?plain=1#L59-L72)

## Conclusion

The tt-installer architecture provides a flexible, customizable system for installing the Tenstorrent software stack across different operating systems. Its modular design allows individual components to be installed or skipped, while its configuration system offers extensive customization options. The installation process is designed to be user-friendly, with interactive prompts, comprehensive logging, and robust error handling.

For detailed information about configuration options, see [Configuration Options](https://deepwiki.com/tenstorrent/tt-installer/4-configuration-options). For information about specific Tenstorrent components, see [Tenstorrent Components](https://deepwiki.com/tenstorrent/tt-installer/5-tenstorrent-components).

Sources: [install.sh 1-663](https://github.com/tenstorrent/tt-installer/blob/19fdd6a2/install.sh#L1-L663)[README.md 1-72](https://github.com/tenstorrent/tt-installer/blob/19fdd6a2/README.md?plain=1#L1-L72)

Dismiss
Refresh this wiki

Enter email to refresh
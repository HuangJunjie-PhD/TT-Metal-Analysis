---
project: tt-installer
github: https://github.com/tenstorrent/tt-installer
deepwiki: https://deepwiki.com/tenstorrent/tt-installer/7-troubleshooting
---

# Troubleshooting

Relevant source files
*   [README.md](https://github.com/tenstorrent/tt-installer/blob/19fdd6a2/README.md?plain=1)
*   [install.sh](https://github.com/tenstorrent/tt-installer/blob/19fdd6a2/install.sh)

This document provides guidance for diagnosing and resolving common issues encountered during the installation and operation of the Tenstorrent software stack using the tt-installer. For specific information about component architecture and installation process, see [Installation Architecture](https://deepwiki.com/tenstorrent/tt-installer/3-installation-architecture). For details on configuration options, see [Configuration Options](https://deepwiki.com/tenstorrent/tt-installer/4-configuration-options).

## 1. Installation Process Diagnostics

When experiencing installation problems, the diagnostic process should follow a systematic approach. The tt-installer creates detailed logs during execution that serve as the primary diagnostic resource.

Sources: [install.sh 126-136](https://github.com/tenstorrent/tt-installer/blob/19fdd6a2/install.sh#L126-L136)

### 1.1 Locating the Installation Log

The installer creates a detailed log in a temporary directory. The log path is displayed at the beginning of the installation:

```
[INFO] Log is at /tmp/tenstorrent_install_XXXXXX/install.log
```

This log contains timestamped entries of all operations performed during installation, along with errors and warnings.

Sources: [install.sh 126-136](https://github.com/tenstorrent/tt-installer/blob/19fdd6a2/install.sh#L126-L136)[install.sh 146-167](https://github.com/tenstorrent/tt-installer/blob/19fdd6a2/install.sh#L146-L167)

### 1.2 Understanding Log Messages

The log contains several types of messages:

| Message Type | Prefix | Description |
| --- | --- | --- |
| Information | `[INFO]` | General status updates |
| Warning | `[WARNING]` | Non-critical issues that may need attention |
| Error | `[ERROR]` | Critical issues that caused a component to fail |

Sources: [install.sh 146-167](https://github.com/tenstorrent/tt-installer/blob/19fdd6a2/install.sh#L146-L167)

## 2. General Installation Issues

### 2.1 Sudo Permission Problems

If the installation fails with sudo permission errors:

```
[ERROR] Cannot use sudo, exiting...
```

**Solution**:

1.   Ensure your user has sudo privileges
2.   Run the installer with a user that has sudo privileges
3.   If in a container, ensure the container has the necessary capabilities

Sources: [install.sh 169-174](https://github.com/tenstorrent/tt-installer/blob/19fdd6a2/install.sh#L169-L174)

### 2.2 Distribution Compatibility Issues

The installer supports specific Linux distributions. If running on an unsupported distribution, you may encounter various compatibility issues.

**Solution**:

1.   Check the supported distributions in the README
2.   If on Ubuntu 20.04, note the limitations (no tt-metalium container)
3.   On Debian, ensure rustc and cargo are installed via rustup
4.   For other distributions, consider manual component installation

Sources: [install.sh 176-186](https://github.com/tenstorrent/tt-installer/blob/19fdd6a2/install.sh#L176-L186)[install.sh 437-473](https://github.com/tenstorrent/tt-installer/blob/19fdd6a2/install.sh#L437-L473)[README.md 59-72](https://github.com/tenstorrent/tt-installer/blob/19fdd6a2/README.md?plain=1#L59-L72)

### 2.3 Python Environment Issues

Python environment configuration issues are common and can affect tool installation:

**Solutions**:

1.   For virtual environment issues: 
    *   Ensure venv is activated before running installer
    *   Check for Python version compatibility
    *   Try a different Python installation method

2.   For package installation issues: 
    *   Check internet connectivity
    *   Try manual installation of the affected package

3.   For path issues: 
    *   Ensure `~/.local/bin` is in your PATH
    *   For venv, always activate before using tools

Sources: [install.sh 226-254](https://github.com/tenstorrent/tt-installer/blob/19fdd6a2/install.sh#L226-L254)[install.sh 489-525](https://github.com/tenstorrent/tt-installer/blob/19fdd6a2/install.sh#L489-L525)

## 3. Component-Specific Issues

### 3.1 Kernel-Mode Driver (KMD) Issues

**Common Issues and Solutions**:

1.   **Module Not Loading**

    *   Error: `modprobe: FATAL: Module tenstorrent not found`
    *   Solution: Reinstall the KMD with `sudo dkms install "tenstorrent/[version]"`

2.   **Version Mismatch**

    *   Check installed version: `modinfo -F version tenstorrent`
    *   Solution: Force KMD reinstall during installation

3.   **Build Failures**

    *   Check DKMS logs: `cat /var/lib/dkms/tenstorrent/[version]/build/make.log`
    *   Solution: Install missing kernel development packages or update kernel

Sources: [install.sh 529-553](https://github.com/tenstorrent/tt-installer/blob/19fdd6a2/install.sh#L529-L553)

### 3.2 Firmware and tt-flash Issues

**Common Issues and Solutions**:

1.   **tt-flash Not Found**

    *   Error: `tt-flash: command not found`
    *   Solution: Ensure Python tools are installed and PATH is configured correctly

2.   **Firmware Update Failure**

    *   Error: `Failed to update firmware`
    *   Solution: Try force update with `tt-flash --fw-tar [firmware] --force`

3.   **Firmware Download Issues**

    *   Error: `Download failed: [firmware] not found`
    *   Solution: Manually download firmware from the repository and specify its path

Sources: [install.sh 555-571](https://github.com/tenstorrent/tt-installer/blob/19fdd6a2/install.sh#L555-L571)

### 3.3 HugePages Configuration Issues

**Common Issues and Solutions**:

1.   **Mount Point Missing**

    *   Error: Unable to find `/dev/hugepages-1G`
    *   Solution: Restart the mount service with `sudo systemctl restart 'dev-hugepages\x2d1G.mount'`

2.   **Service Not Starting**

    *   Check service errors with `systemctl status tenstorrent-hugepages.service`
    *   Solution: Ensure the tenstorrent-tools package is installed correctly

3.   **Permission Issues**

    *   Ensure your user has access to the HugePages mount point
    *   Solution: Add your user to the appropriate group or adjust permissions

Sources: [install.sh 573-603](https://github.com/tenstorrent/tt-installer/blob/19fdd6a2/install.sh#L573-L603)

### 3.4 System Management Interface (tt-smi) Issues

**Common Issues and Solutions**:

1.   **tt-smi Not Found**

    *   Error: `tt-smi: command not found`
    *   Solution: Reinstall tt-smi or check PATH configuration

2.   **No Devices Found**

    *   Error: `No devices found` in tt-smi output
    *   Check that KMD is loaded and `/dev/tenstorrent` exists
    *   Solution: Reinstall KMD and ensure hardware is properly connected

3.   **Permission Denied**

    *   Error: `Permission denied` when accessing the device
    *   Solution: Ensure your user has read/write access to `/dev/tenstorrent`

Sources: [install.sh 605-607](https://github.com/tenstorrent/tt-installer/blob/19fdd6a2/install.sh#L605-L607)

### 3.5 Podman and tt-metalium Issues

**Common Issues and Solutions**:

1.   **Podman Installation Issues**

    *   Check distribution-specific package manager errors
    *   Solution: Follow manual installation instructions for your distribution

2.   **tt-metalium Script Missing**

    *   Error: `tt-metalium: command not found`
    *   Solution: Recreate the script at `~/.local/bin/tt-metalium` with correct contents

3.   **Container Access to Hardware**

    *   Error: Unable to access hardware inside container
    *   Solution: Ensure proper device permissions and mounting of `/dev/tenstorrent`

4.   **HugePages Not Mounted in Container**

    *   Error: HugePages not accessible in container
    *   Solution: Verify container script mounts `/dev/hugepages-1G` correctly

Sources: [install.sh 267-354](https://github.com/tenstorrent/tt-installer/blob/19fdd6a2/install.sh#L267-L354)[README.md 10-13](https://github.com/tenstorrent/tt-installer/blob/19fdd6a2/README.md?plain=1#L10-L13)

## 4. Advanced Troubleshooting Techniques

### 4.1 Reinstallation with Specific Components

When troubleshooting specific components, you can use environment variables to skip or target particular installation steps:

| Environment Variable | Purpose | Example |
| --- | --- | --- |
| `TT_SKIP_INSTALL_KMD` | Skip KMD installation | `TT_SKIP_INSTALL_KMD=0 ./install.sh` |
| `TT_SKIP_INSTALL_HUGEPAGES` | Skip HugePages setup | `TT_SKIP_INSTALL_HUGEPAGES=0 ./install.sh` |
| `TT_SKIP_UPDATE_FIRMWARE` | Skip firmware update | `TT_SKIP_UPDATE_FIRMWARE=0 ./install.sh` |
| `TT_SKIP_INSTALL_PODMAN` | Skip Podman installation | `TT_SKIP_INSTALL_PODMAN=0 ./install.sh` |
| `TT_SKIP_INSTALL_METALIUM_CONTAINER` | Skip tt-metalium container setup | `TT_SKIP_INSTALL_METALIUM_CONTAINER=0 ./install.sh` |

Sources: [install.sh 54-67](https://github.com/tenstorrent/tt-installer/blob/19fdd6a2/install.sh#L54-L67)[README.md 35-55](https://github.com/tenstorrent/tt-installer/blob/19fdd6a2/README.md?plain=1#L35-L55)

### 4.2 Manual Component Installation

For persistent issues, manual component installation may help diagnose problems:

Sources: [install.sh 529-553](https://github.com/tenstorrent/tt-installer/blob/19fdd6a2/install.sh#L529-L553)[install.sh 555-571](https://github.com/tenstorrent/tt-installer/blob/19fdd6a2/install.sh#L555-L571)[install.sh 573-603](https://github.com/tenstorrent/tt-installer/blob/19fdd6a2/install.sh#L573-L603)[install.sh 605-607](https://github.com/tenstorrent/tt-installer/blob/19fdd6a2/install.sh#L605-L607)[install.sh 608-627](https://github.com/tenstorrent/tt-installer/blob/19fdd6a2/install.sh#L608-L627)

## 5. Distribution-Specific Troubleshooting

### 5.1 Ubuntu 20.04 (Deprecated)

1.   **tt-metalium Not Available**

    *   Ubuntu 20.04 does not support tt-metalium container installation
    *   Solution: Upgrade to Ubuntu 22.04+ for full functionality

2.   **Python Environment Limitations**

    *   `pipx` installation not supported
    *   Solution: Use virtual environment (option 2) for Python tools

Sources: [install.sh 465-468](https://github.com/tenstorrent/tt-installer/blob/19fdd6a2/install.sh#L465-L468)[install.sh 483-486](https://github.com/tenstorrent/tt-installer/blob/19fdd6a2/install.sh#L483-L486)[README.md 66](https://github.com/tenstorrent/tt-installer/blob/19fdd6a2/README.md?plain=1#L66-L66)

### 5.2 Debian Issues

1.   **Rust/Cargo Issues**

    *   Debian's packaged Rust/Cargo versions are too old
    *   Solution: Install using rustup ([https://rustup.rs/](https://rustup.rs/))

2.   **curl Not Installed**

    *   Default Debian installation may not include curl
    *   Solution: Install curl manually before running the installer

Sources: [install.sh 447-473](https://github.com/tenstorrent/tt-installer/blob/19fdd6a2/install.sh#L447-L473)[README.md 67](https://github.com/tenstorrent/tt-installer/blob/19fdd6a2/README.md?plain=1#L67-L67)

### 5.3 Fedora Issues

Fedora may require a system restart after base package installation due to package conflicts or updates.

Sources: [README.md 68-69](https://github.com/tenstorrent/tt-installer/blob/19fdd6a2/README.md?plain=1#L68-L69)

## 6. Verifying Installation Success

After troubleshooting, verify installation success with these commands:

1.   Check KMD version and status:

```
modinfo tenstorrent
```
2.   Verify hardware is detected:

```
tt-smi
```
3.   Test tt-metalium container (if installed):

```
tt-metalium echo "Container is working"
```
4.   Check HugePages configuration:

```
ls -la /dev/hugepages-1G
```

Sources: [install.sh 629-643](https://github.com/tenstorrent/tt-installer/blob/19fdd6a2/install.sh#L629-L643)

Dismiss
Refresh this wiki

Enter email to refresh
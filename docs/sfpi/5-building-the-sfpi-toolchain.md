---
project: sfpi
github: https://github.com/tenstorrent/sfpi
deepwiki: https://deepwiki.com/tenstorrent/sfpi/5-building-the-sfpi-toolchain
---

# Building the SFPI Toolchain

Relevant source files
*   [.github/workflows/build-sfpi.yaml](https://github.com/tenstorrent/sfpi/blob/ed989b9d/.github/workflows/build-sfpi.yaml)
*   [Makefile.in](https://github.com/tenstorrent/sfpi/blob/ed989b9d/Makefile.in)
*   [configure](https://github.com/tenstorrent/sfpi/blob/ed989b9d/configure)
*   [configure.ac](https://github.com/tenstorrent/sfpi/blob/ed989b9d/configure.ac)
*   [scripts/build.sh](https://github.com/tenstorrent/sfpi/blob/ed989b9d/scripts/build.sh)
*   [scripts/git-hash.sh](https://github.com/tenstorrent/sfpi/blob/ed989b9d/scripts/git-hash.sh)
*   [scripts/release.sh](https://github.com/tenstorrent/sfpi/blob/ed989b9d/scripts/release.sh)
*   [scripts/sfpi-info.sh](https://github.com/tenstorrent/sfpi/blob/ed989b9d/scripts/sfpi-info.sh)

This document provides a comprehensive guide to building, testing, and packaging the GCC-based RISC-V toolchain with SFPU (Single Floating-Point Unit) extensions. The SFPI toolchain is built using a traditional Autoconf/Automake-based build system that orchestrates compilation of multiple components: Binutils, GCC (two stages), Newlib, and optionally GDB. The toolchain targets the `riscv32-tt-elf` triple and supports multiple Tenstorrent architectures through multilib configurations.

## Build System Overview

The SFPI build system follows a multi-stage compilation process typical of cross-compilation toolchains. The build is orchestrated through three main components:

1.   **Autoconf Configuration** ([configure.ac 1-302](https://github.com/tenstorrent/sfpi/blob/ed989b9d/configure.ac#L1-L302)[Makefile.in 1-620](https://github.com/tenstorrent/sfpi/blob/ed989b9d/Makefile.in#L1-L620)) - Generates platform-specific build files
2.   **Build Script** ([scripts/build.sh 1-236](https://github.com/tenstorrent/sfpi/blob/ed989b9d/scripts/build.sh#L1-L236)) - Manages the compilation pipeline and testing
3.   **Release Script** ([scripts/release.sh 1-160](https://github.com/tenstorrent/sfpi/blob/ed989b9d/scripts/release.sh#L1-L160)) - Packages the toolchain for distribution

### Prerequisites

The build requires a Linux-based development environment with the following dependencies:

**Build Tools:**

*   `autoconf`, `automake` - Build system generators
*   `bison`, `flex` - Parser generators
*   `gawk` - Text processing
*   `python3` - Build scripts
*   `texinfo` - Documentation generation
*   `patchutils` - Patch management

**Development Libraries:**

*   `libgmp-dev`, `libmpfr-dev`, `libmpc-dev` - GCC mathematical libraries
*   `libexpat1-dev` - XML parsing for GDB
*   `gcc`, `g++` - Host compiler

**Optional:**

*   `ruby`, `fpm` - For generating `.deb` and `.rpm` packages
*   `expect` - For DejaGnu test framework

Sources: [.github/workflows/build-sfpi.yaml 62-73](https://github.com/tenstorrent/sfpi/blob/ed989b9d/.github/workflows/build-sfpi.yaml#L62-L73)[scripts/sfpi-info.sh 165-169](https://github.com/tenstorrent/sfpi/blob/ed989b9d/scripts/sfpi-info.sh#L165-L169)

## 5.1 Build System Architecture

The SFPI build system uses GNU Autoconf and Automake to manage cross-platform builds. The configuration system generates Makefiles that orchestrate compilation of multiple interdependent components.

### Configuration System

The build configuration is defined in [configure.ac 1-302](https://github.com/tenstorrent/sfpi/blob/ed989b9d/configure.ac#L1-L302) and processed by Autoconf to generate the `configure` script. Key configuration parameters include:

**Architecture Settings:**

*   `--with-arch` - Target ISA (default: `rv32i` per [Makefile.in 31](https://github.com/tenstorrent/sfpi/blob/ed989b9d/Makefile.in#L31-L31))
*   `--with-abi` - Target ABI (default: `ilp32` per [Makefile.in 32](https://github.com/tenstorrent/sfpi/blob/ed989b9d/Makefile.in#L32-L32))
*   `--with-mfc` - Manufacturer field in target triple (default: `tt` per [Makefile.in 15](https://github.com/tenstorrent/sfpi/blob/ed989b9d/Makefile.in#L15-L15))

**Build Options:**

*   `--enable-multilib` - Enable multiple library variants ([configure.ac 106-111](https://github.com/tenstorrent/sfpi/blob/ed989b9d/configure.ac#L106-L111))
*   `--with-multilib-generator` - Specify multilib configurations ([configure.ac 113-122](https://github.com/tenstorrent/sfpi/blob/ed989b9d/configure.ac#L113-L122))
*   `--enable-gcc-checking` - GCC internal consistency checks ([configure.ac 141-149](https://github.com/tenstorrent/sfpi/blob/ed989b9d/configure.ac#L141-L149))
*   `--enable-gdb` - Include GDB debugger ([configure.ac 235-244](https://github.com/tenstorrent/sfpi/blob/ed989b9d/configure.ac#L235-L244))

### Makefile Structure

The generated [Makefile.in 1-620](https://github.com/tenstorrent/sfpi/blob/ed989b9d/Makefile.in#L1-L620) defines build targets and dependencies:

**Component Build Order:**

**Key Makefile Variables:**

| Variable | Definition | Purpose |
| --- | --- | --- |
| `NEWLIB_TUPLE` | `riscv-$(MFC)-elf` | Target triple ([Makefile.in 58](https://github.com/tenstorrent/sfpi/blob/ed989b9d/Makefile.in#L58-L58)) |
| `INSTALL_DIR` | `@prefix@` | Installation root ([Makefile.in 7](https://github.com/tenstorrent/sfpi/blob/ed989b9d/Makefile.in#L7-L7)) |
| `WITH_ARCH` | `--with-arch=$(with_arch)` | Architecture flag ([Makefile.in 31](https://github.com/tenstorrent/sfpi/blob/ed989b9d/Makefile.in#L31-L31)) |
| `WITH_ABI` | `--with-abi=$(with_abi)` | ABI flag ([Makefile.in 32](https://github.com/tenstorrent/sfpi/blob/ed989b9d/Makefile.in#L32-L32)) |
| `GCC_CHECKING_FLAGS` | `@gcc_checking@` | GCC validation level ([Makefile.in 56](https://github.com/tenstorrent/sfpi/blob/ed989b9d/Makefile.in#L56-L56)) |

### Build Script Operations

The [scripts/build.sh 1-236](https://github.com/tenstorrent/sfpi/blob/ed989b9d/scripts/build.sh#L1-L236) script orchestrates the build process:

**Build Script Data Flow:**

**Build Script Command-Line Options:**

| Option | Effect | Code Reference |
| --- | --- | --- |
| `--checking[=MODE]` | Set `gcc_checking` variable | [build.sh 25-26](https://github.com/tenstorrent/sfpi/blob/ed989b9d/build.sh#L25-L26) |
| `--dir=PATH` | Set `BUILD` directory | [build.sh 27](https://github.com/tenstorrent/sfpi/blob/ed989b9d/build.sh#L27-L27) |
| `--dejagnu` | Enable test infrastructure | [build.sh 28](https://github.com/tenstorrent/sfpi/blob/ed989b9d/build.sh#L28-L28) |
| `--gdb` | Include GDB in build | [build.sh 29](https://github.com/tenstorrent/sfpi/blob/ed989b9d/build.sh#L29-L29) |
| `--infra` | Build dejagnu + simulator | [build.sh 30](https://github.com/tenstorrent/sfpi/blob/ed989b9d/build.sh#L30-L30) |
| `--serial` | Single-threaded build (NCPUS=1) | [build.sh 31](https://github.com/tenstorrent/sfpi/blob/ed989b9d/build.sh#L31-L31) |
| `--test` | Run all tests | [build.sh 32](https://github.com/tenstorrent/sfpi/blob/ed989b9d/build.sh#L32-L32) |
| `--test-binutils` | Test only binutils | [build.sh 33](https://github.com/tenstorrent/sfpi/blob/ed989b9d/build.sh#L33-L33) |
| `--test-gcc` | Test only GCC | [build.sh 34](https://github.com/tenstorrent/sfpi/blob/ed989b9d/build.sh#L34-L34) |
| `--test-tt` | Tenstorrent-specific tests | [build.sh 35](https://github.com/tenstorrent/sfpi/blob/ed989b9d/build.sh#L35-L35) |
| `--tt-built` | Mark as official build | [build.sh 36](https://github.com/tenstorrent/sfpi/blob/ed989b9d/build.sh#L36-L36) |
| `--tt-version=VER` | Override version detection | [build.sh 37](https://github.com/tenstorrent/sfpi/blob/ed989b9d/build.sh#L37-L37) |

### Version Management

The version string is computed by [scripts/build.sh 48-107](https://github.com/tenstorrent/sfpi/blob/ed989b9d/scripts/build.sh#L48-L107) using a hierarchical approach:

1.   **Cached Version** - Read from `build/version` if present ([build.sh 49-50](https://github.com/tenstorrent/sfpi/blob/ed989b9d/build.sh#L49-L50))
2.   **Git Tags** - Use `git describe --tags` with specific patterns ([build.sh 53-58](https://github.com/tenstorrent/sfpi/blob/ed989b9d/build.sh#L53-L58))
3.   **Branch Name** - Append branch name if not on tagged commit ([build.sh 64-99](https://github.com/tenstorrent/sfpi/blob/ed989b9d/build.sh#L64-L99))
4.   **Remote Origin** - Prepend origin if not `tenstorrent` ([build.sh 101-106](https://github.com/tenstorrent/sfpi/blob/ed989b9d/build.sh#L101-L106))

The version is stored in `build/version` and embedded in package metadata via `--with-pkgversion` ([build.sh 131-135](https://github.com/tenstorrent/sfpi/blob/ed989b9d/build.sh#L131-L135)).

Sources: [scripts/build.sh 1-236](https://github.com/tenstorrent/sfpi/blob/ed989b9d/scripts/build.sh#L1-L236)[Makefile.in 1-620](https://github.com/tenstorrent/sfpi/blob/ed989b9d/Makefile.in#L1-L620)[configure.ac 1-302](https://github.com/tenstorrent/sfpi/blob/ed989b9d/configure.ac#L1-L302)

## 5.2 Configuration and Compilation

The configuration and compilation process follows a traditional cross-compilation workflow with platform-specific customizations for Tenstorrent SFPU support.

### Initial Setup

**1. Clone Repository and Initialize Submodules:**

`git clone https://github.com/tenstorrent/sfpi.gitcd sfpigit submodule update --init --recursive`
The repository includes the following submodules as defined in [.gitmodules](https://github.com/tenstorrent/sfpi/blob/ed989b9d/.gitmodules):

*   `gcc/` - Tenstorrent-enhanced RISC-V GCC
*   `binutils/` - Tenstorrent-enhanced RISC-V binutils
*   `newlib/` - C library for embedded systems
*   `qemu/` - RISC-V simulator (v7.2.15 per [build.sh 156](https://github.com/tenstorrent/sfpi/blob/ed989b9d/build.sh#L156-L156))
*   `riscv-dejagnu/` - Test framework

**2. Run Configuration Script:**

The [configure 1-4378](https://github.com/tenstorrent/sfpi/blob/ed989b9d/configure#L1-L4378) script (generated from [configure.ac 1-302](https://github.com/tenstorrent/sfpi/blob/ed989b9d/configure.ac#L1-L302)) probes the system and generates build files:

`./configure --prefix=$(pwd)/build/sfpi/compiler \            --with-mfc=tt \            --with-arch=rv32i \            --with-abi=ilp32 \            --enable-multilib \            --with-multilib-generator="rv32im_xttwh-ilp32-- rv32im_xttbh-ilp32--"`
**Configuration Phases:**

### Compilation Pipeline

**Invoke Build Script:**

The [scripts/build.sh 1-236](https://github.com/tenstorrent/sfpi/blob/ed989b9d/scripts/build.sh#L1-L236) script manages the compilation:

`scripts/build.sh [options]`
**Multi-Stage Compilation Process:**

The toolchain is built in multiple stages to resolve circular dependencies:

**Detailed Build Steps:**

The Makefile defines the following build targets with their dependencies:

| Target | Dependencies | Actions | Reference |
| --- | --- | --- | --- |
| `stamps/build-binutils-newlib` | `$(BINUTILS_SRCDIR)` | Configure with `--target=$(NEWLIB_TUPLE)`, build, install | [Makefile.in 235-251](https://github.com/tenstorrent/sfpi/blob/ed989b9d/Makefile.in#L235-L251) |
| `stamps/build-gcc-newlib-stage1` | binutils | Configure with `--enable-languages=c`, build gcc only | [Makefile.in 274-308](https://github.com/tenstorrent/sfpi/blob/ed989b9d/Makefile.in#L274-L308) |
| `stamps/build-newlib` | gcc-stage1 | Configure with `-D_POSIX_MODE`, build libc | [Makefile.in 310-325](https://github.com/tenstorrent/sfpi/blob/ed989b9d/Makefile.in#L310-L325) |
| `stamps/build-newlib-nano` | gcc-stage1 | Configure with nano options, optimized for size | [Makefile.in 327-349](https://github.com/tenstorrent/sfpi/blob/ed989b9d/Makefile.in#L327-L349) |
| `stamps/merge-newlib-nano` | newlib, newlib-nano | Copy nano libraries as `*_nano.a` variants | [Makefile.in 351-372](https://github.com/tenstorrent/sfpi/blob/ed989b9d/Makefile.in#L351-L372) |
| `stamps/build-gcc-newlib-stage2` | newlib-merged | Full C++ build with libstdc++ | [Makefile.in 374-410](https://github.com/tenstorrent/sfpi/blob/ed989b9d/Makefile.in#L374-L410) |

### Multilib Configuration

The multilib system allows a single toolchain to generate code for multiple architectures. This is controlled by the `--with-multilib-generator` configure option.

**Default Multilib Specification:**

```
rv32im_xttwh-ilp32--
rv32im_xttbh-ilp32--
```

This generates libraries for:

*   **Wormhole**: `rv32im` base + `_xttwh` extension
*   **Blackhole**: `rv32im` base + `_xttbh` extension

**Multilib Library Installation:**

The multilib selection occurs at compile time when GCC analyzes the `-march` flag and selects the corresponding library directory.

### Parallel Build Control

The build system supports parallel compilation via the `-j` flag:

`# Determine CPU countNCPUS=$(nproc)                     # scripts/build.sh:11 # Run parallel buildmake -C $BUILD -j$NCPUS            # scripts/build.sh:148`
To force single-threaded builds (useful for debugging):

`scripts/build.sh --serial          # Sets NCPUS=1`
### Build Artifacts

After successful compilation, the build directory contains:

**Build Directory Structure:**

| Directory | Contents | Purpose |
| --- | --- | --- |
| `build/sfpi/compiler/bin/` | `riscv32-tt-elf-gcc`, `riscv32-tt-elf-as`, etc. | Toolchain executables |
| `build/sfpi/compiler/lib/` | `libgcc.a`, multilib variants | Compiler runtime |
| `build/sfpi/compiler/libexec/` | `cc1`, `cc1plus`, `collect2` | GCC internal programs |
| `build/sfpi/compiler/riscv32-tt-elf/` | `include/`, `lib/` | Target headers and libraries |
| `build/build-gcc-newlib-stage2/` | Object files, build logs | Intermediate build artifacts |
| `build/stamps/` | Timestamp files | Build system state tracking |

Sources: [scripts/build.sh 1-236](https://github.com/tenstorrent/sfpi/blob/ed989b9d/scripts/build.sh#L1-L236)[Makefile.in 235-410](https://github.com/tenstorrent/sfpi/blob/ed989b9d/Makefile.in#L235-L410)[configure.ac 1-302](https://github.com/tenstorrent/sfpi/blob/ed989b9d/configure.ac#L1-L302)

## 5.3 Packaging and Release

The [scripts/release.sh 1-160](https://github.com/tenstorrent/sfpi/blob/ed989b9d/scripts/release.sh#L1-L160) script packages the built toolchain into distributable formats including tarballs, Debian packages, and RPM packages.

### Release Process Overview

**Release Script Workflow:**

### Release Artifacts

The release process generates the following artifacts:

**Tarball Package:**

*   **Filename**: `sfpi_${VERSION}_${ARCH}_${DIST}.txz` (per [scripts/sfpi-info.sh 110](https://github.com/tenstorrent/sfpi/blob/ed989b9d/scripts/sfpi-info.sh#L110-L110))
*   **Contents**: Full toolchain installation directory
*   **Compression**: XZ format (LZMA2 algorithm)
*   **Created at**: [release.sh 59](https://github.com/tenstorrent/sfpi/blob/ed989b9d/release.sh#L59-L59)

**Debian Package (.deb):**

*   **Filename**: `sfpi_${VERSION}_${ARCH}_${DIST}.deb`
*   **Package Manager**: APT/dpkg
*   **Install Location**: `/opt/tenstorrent/sfpi`
*   **Created via**: `fpm` tool at [release.sh 141-143](https://github.com/tenstorrent/sfpi/blob/ed989b9d/release.sh#L141-L143)

**RPM Package (.rpm):**

*   **Filename**: `sfpi_${VERSION}_${ARCH}_${DIST}.rpm`
*   **Package Manager**: DNF/yum
*   **Install Location**: `/opt/tenstorrent/sfpi`
*   **Created via**: `fpm` tool at [release.sh 145-149](https://github.com/tenstorrent/sfpi/blob/ed989b9d/release.sh#L145-L149)

**Checksum File:**

*   **Filename**: `sfpi_${VERSION}_${ARCH}_${DIST}.sha256`
*   **Contents**: SHA-256 checksums of all packages
*   **Format**: Output of `sha256sum -b`
*   **Generated at**: [release.sh 158](https://github.com/tenstorrent/sfpi/blob/ed989b9d/release.sh#L158-L158)

### Dependency Resolution

The release script automatically detects and records shared library dependencies:

**Dependency Detection Process:**

**Supported Dependencies:**

The release script recognizes these shared libraries ([release.sh 66-89](https://github.com/tenstorrent/sfpi/blob/ed989b9d/release.sh#L66-L89)):

| Shared Object | Debian Package | Fedora Package |
| --- | --- | --- |
| `libc.so*` | `libc6` | `glibc` |
| `libgmp.so*` | `libgmp10` | `gmp` |
| `libmpfr.so*` | `libmpfr6` | `mpfr` |
| `libmpc.so*` | `libmpc3` | `libmpc` |
| `libisl.so*` | `libisl23` | `isl` |
| `libexpat.so*` | `libexpat1` | `expat` |
| `libstdc++.so*` | `libstdc++6` | `libstdc++` |
| `libz.so*` | `zlib1g` | `zlib-ng-compat` |
| `libzstd.so*` | `libzstd1` | `libzstd` |

### Package Metadata

Packages include the following metadata:

**FPM Options** ([release.sh 94-104](https://github.com/tenstorrent/sfpi/blob/ed989b9d/release.sh#L94-L104)):

*   `--name`: `sfpi`
*   `--version`: `${tt_version}` (underscores/hyphens normalized)
*   `--architecture`: `native` (host architecture)
*   `--license`: `"Apache 2.0/GPL 2,3/LGPL 2.1"`
*   `--url`: `https://github.com/tenstorrent/sfpi`
*   `--description`: `"Tenstorrent SFPI compiler tools"`
*   `--maintainer`: Set by `--tt-built` flag
*   `--vendor`: Set by `--tt-built` flag

### Version Information

The [scripts/sfpi-info.sh 1-211](https://github.com/tenstorrent/sfpi/blob/ed989b9d/scripts/sfpi-info.sh#L1-L211) script provides centralized version management:

**sfpi-info.sh Modes:**

| Mode | Purpose | Usage |
| --- | --- | --- |
| `VERSION` | Generate variables from version string | `eval $(sfpi-info.sh VERSION 1.2.3)` |
| `SHELL` | Generate shell variable assignments | `eval $(sfpi-info.sh SHELL [pkg])` |
| `CMAKE` | Generate CMake set() commands | `sfpi-info.sh CMAKE [pkg]` |
| `CREATE` | Generate sfpi-version file from hashes | `sfpi-info.sh CREATE [dirs]` |
| `BUILD` | Clone and build toolchain | `sfpi-info.sh BUILD [dir]` |

**Generated Variables:**

`sfpi_version=1.2.3           # Version stringsfpi_arch=x86_64             # uname -msfpi_dist=debian             # Detected from /etc/os-releasesfpi_pkg=deb                 # Package format (deb/rpm/txz)sfpi_url=https://...         # Download URLsfpi_filename=sfpi_1.2.3_... # Base filenamesfpi_hash=abc123...          # SHA-256 checksumsfpi_hashtype=sha256         # Hash algorithm`
Sources: [scripts/release.sh 1-160](https://github.com/tenstorrent/sfpi/blob/ed989b9d/scripts/release.sh#L1-L160)[scripts/sfpi-info.sh 1-211](https://github.com/tenstorrent/sfpi/blob/ed989b9d/scripts/sfpi-info.sh#L1-L211)

## Creating a Release

After successfully building the toolchain, you can create a release package:

`scripts/release.sh [--dir=BUILD_DIR]`
The release script:

1.   Verifies that source hashes haven't changed since build start
2.   Creates a tarball and Debian package
3.   Generates checksums for verification

The release artifacts are named following the pattern `sfpi-$(uname -m)_$(uname -s).*` and placed in the build directory.

Sources: [README.md 93-103](https://github.com/tenstorrent/sfpi/blob/ed989b9d/README.md?plain=1#L93-L103)

## Common Issues and Troubleshooting

### Build Failures

| Issue | Possible Solution |
| --- | --- |
| Missing dependencies | Install required libraries (GMP, MPFR, MPC, etc.) |
| Permission denied | Run with proper permissions for the prefix directory or use `--prefix=$HOME/sfpi` |
| Build fails at GCC | Check that required libraries are available and correctly linked |
| Failed tests | Check if failures are expected (xfails) or indicate real issues |

### Using Multiple Build Directories

You can create multiple build configurations by using the `--dir` option:

`scripts/build.sh --dir=build-whscripts/build.sh --dir=build-bh --monolib --with-multilib-generator="rv32im_xttbh-ilp32--"`
This allows maintaining separate builds for different purposes or configurations.

Sources: [scripts/build.sh 30](https://github.com/tenstorrent/sfpi/blob/ed989b9d/scripts/build.sh#L30-L30)[README.md 56-57](https://github.com/tenstorrent/sfpi/blob/ed989b9d/README.md?plain=1#L56-L57)

## Conclusion

This document has covered the process of building the SFPI toolchain, running tests, and creating releases. The SFPI toolchain provides the necessary components for developing software targeting Tenstorrent RISC-V processors with SFPU support. For information about using the toolchain to write SFPI code, see [SFPI Architecture](https://deepwiki.com/tenstorrent/sfpi/2-core-sfpi-programming-interface) and [Core Interface Layer](https://deepwiki.com/tenstorrent/sfpi/2.1-vector-types-and-operations).

Dismiss
Refresh this wiki

Enter email to refresh
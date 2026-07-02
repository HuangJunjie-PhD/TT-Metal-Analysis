---
project: riscv_arch_tests
github: https://github.com/tenstorrent/riscv_arch_tests
deepwiki: https://deepwiki.com/tenstorrent/riscv_arch_tests/6-licensing)
---

# Licensing

Relevant source files
*   [LICENSE](https://github.com/tenstorrent/riscv_arch_tests/blob/e54223b0/LICENSE)
*   [LICENSE_understanding.txt](https://github.com/tenstorrent/riscv_arch_tests/blob/e54223b0/LICENSE_understanding.txt)

## Purpose and Scope

This document provides comprehensive information about the licensing terms that govern the RISC-V Architecture Tests repository. It covers the primary license, third-party dependencies, and important considerations regarding hardware implementations. The licensing information is critical for users who intend to modify, distribute, or implement the tests in hardware.

## Primary License: Apache License 2.0

The RISC-V Architecture Tests repository is licensed under the Apache License, Version 2.0. This is a permissive free software license that allows users to use, modify, and distribute the software under certain conditions.

## Repository License Structure

Sources: [LICENSE 1-190](https://github.com/tenstorrent/riscv_arch_tests/blob/e54223b0/LICENSE#L1-L190)[LICENSE_understanding.txt 1-4](https://github.com/tenstorrent/riscv_arch_tests/blob/e54223b0/LICENSE_understanding.txt#L1-L4)

### Key Terms and Permissions

The Apache License 2.0 grants users the following key permissions:

| Permission | Description |
| --- | --- |
| Use | You can use the software for any purpose |
| Modification | You can modify the software |
| Distribution | You can distribute original or modified versions |
| Sublicensing | You can apply different license terms to your modifications |
| Patent Grant | Contributors grant patent licenses to their contributions |

These permissions are perpetual, worldwide, non-exclusive, no-charge, and royalty-free.

Sources: [LICENSE 66-88](https://github.com/tenstorrent/riscv_arch_tests/blob/e54223b0/LICENSE#L66-L88)

### Redistribution Requirements

When redistributing the software, you must comply with the following requirements:

1.   Include a copy of the license with any distribution
2.   Add prominent notices to modified files indicating your changes
3.   Retain all copyright, patent, trademark, and attribution notices from the source code
4.   If a NOTICE file exists, include its contents with your distribution

Sources: [LICENSE 89-129](https://github.com/tenstorrent/riscv_arch_tests/blob/e54223b0/LICENSE#L89-L129)

## Third-Party Dependencies

The repository integrates third-party dependencies through Git submodules as defined in `.gitmodules`:

## Third-Party Dependency Integration

### Submodule License Requirements

| Component | Repository | License Location |
| --- | --- | --- |
| `whisper/` | SiFive Whisper ISS | Submodule-specific license |
| `spike/` | riscv-isa-sim | [https://github.com/riscv-software-src/riscv-isa-sim/blob/master/LICENSE](https://github.com/riscv-software-src/riscv-isa-sim/blob/master/LICENSE) |
| riscv-opcodes | Transitive dependency | [https://github.com/riscv/riscv-opcodes/blob/master/LICENSE](https://github.com/riscv/riscv-opcodes/blob/master/LICENSE) |

These dependencies are referenced but not distributed as part of the software, requiring separate license compliance.

Sources: [LICENSE 194-201](https://github.com/tenstorrent/riscv_arch_tests/blob/e54223b0/LICENSE#L194-L201)

## Hardware Implementation Considerations

An important clarification regarding hardware implementations is provided in the LICENSE_understanding.txt file:

## Tenstorrent Hardware Implementation Clarification

The `LICENSE_understanding.txt` file provides critical clarification: while the Apache 2.0 license covers the software components, **hardware implementation requires separate licensing considerations**.

### Key Distinction

*   **Software Usage**: Covered by Apache 2.0 license
*   **Hardware Implementation**: May require additional patent or IP licenses from Tenstorrent or others
*   **Programming Assistance**: The software assists in programming Tenstorrent products but does not grant hardware implementation rights

This separation ensures that software developers can freely use the test suite while hardware implementers understand additional licensing may be required.

Sources: [LICENSE_understanding.txt 1-4](https://github.com/tenstorrent/riscv_arch_tests/blob/e54223b0/LICENSE_understanding.txt#L1-L4)

## License Compliance

To ensure compliance with the licensing terms:

1.   Maintain all copyright and license notices when using or distributing the code
2.   Document any modifications made to the original code
3.   Review third-party dependency licenses before using them
4.   For hardware implementations, consider potential patent rights that may be required
5.   When in doubt about licensing implications, consult legal counsel specialized in open source and hardware IP

## License Applicability

## License Coverage by Component

Sources: [LICENSE 1-190](https://github.com/tenstorrent/riscv_arch_tests/blob/e54223b0/LICENSE#L1-L190)[LICENSE_understanding.txt 1-4](https://github.com/tenstorrent/riscv_arch_tests/blob/e54223b0/LICENSE_understanding.txt#L1-L4)

## Summary

The RISC-V Architecture Tests repository is licensed under the Apache License 2.0, which provides users with significant freedom to use, modify, and distribute the software. However, users should be aware that:

1.   Third-party dependencies have their own licenses
2.   Hardware implementations may require additional licensing of patent rights
3.   The software license does not automatically grant hardware implementation rights

These licensing terms are designed to allow for flexible use of the test suite while respecting intellectual property rights related to hardware implementations.

Dismiss
Refresh this wiki

Enter email to refresh
---
project: tt-burnin
github: https://github.com/tenstorrent/tt-burnin
deepwiki: https://deepwiki.com/tenstorrent/tt-burnin/7-legal-and-compliance
---

# Legal and Compliance

Relevant source files
*   [LICENSE](https://github.com/tenstorrent/tt-burnin/blob/809f293d/LICENSE)
*   [LICENSE_understanding.txt](https://github.com/tenstorrent/tt-burnin/blob/809f293d/LICENSE_understanding.txt)
*   [README.md](https://github.com/tenstorrent/tt-burnin/blob/809f293d/README.md?plain=1)

This document covers the legal and compliance aspects of the tt-burnin project, including software licensing, patent considerations, and regulatory compliance requirements for distribution and use of the burn-in testing utility.

For information about project packaging and distribution mechanisms, see [Build and Release](https://deepwiki.com/tenstorrent/tt-burnin/5-build-and-release). For details about project dependencies and their licensing implications, see [Dependencies and Setup](https://deepwiki.com/tenstorrent/tt-burnin/3.1-dependencies-and-setup).

## Software Licensing

The tt-burnin project is licensed under the Apache License 2.0, as specified in the [LICENSE 1-207](https://github.com/tenstorrent/tt-burnin/blob/809f293d/LICENSE#L1-L207) file. This license provides users with broad permissions for use, modification, and distribution while maintaining copyright and patent protections for contributors.

### License Terms and Permissions

The Apache 2.0 license grants the following permissions:

| Permission | Description | License Section |
| --- | --- | --- |
| **Use** | Run the software for any purpose | Section 2 |
| **Modify** | Create derivative works | Section 2 |
| **Distribute** | Share copies and modifications | Section 4 |
| **Private Use** | Use privately without disclosure | Implied |
| **Commercial Use** | Use for commercial purposes | Implied |

### License Obligations

Users and distributors must comply with the following requirements:

| Obligation | Requirement | License Section |
| --- | --- | --- |
| **Include License** | Provide copy of license with distributions | [LICENSE 95-96](https://github.com/tenstorrent/tt-burnin/blob/809f293d/LICENSE#L95-L96) |
| **State Changes** | Document modifications to original files | [LICENSE 98-99](https://github.com/tenstorrent/tt-burnin/blob/809f293d/LICENSE#L98-L99) |
| **Preserve Notices** | Retain copyright and attribution notices | [LICENSE 101-105](https://github.com/tenstorrent/tt-burnin/blob/809f293d/LICENSE#L101-L105) |
| **Include NOTICE** | Distribute NOTICE file if present | [LICENSE 107-122](https://github.com/tenstorrent/tt-burnin/blob/809f293d/LICENSE#L107-L122) |

### Copyright Notice

The project includes the following copyright notice as specified in [LICENSE 205-206](https://github.com/tenstorrent/tt-burnin/blob/809f293d/LICENSE#L205-L206):

```
Copyright (c) 2024 Tenstorrent AI ULC
```

Sources: [LICENSE 1-207](https://github.com/tenstorrent/tt-burnin/blob/809f293d/LICENSE#L1-L207)[README.md 105-107](https://github.com/tenstorrent/tt-burnin/blob/809f293d/README.md?plain=1#L105-L107)

## Patent and Hardware Licensing Considerations

Beyond the software license, the tt-burnin utility interacts with Tenstorrent hardware products, which introduces additional licensing considerations outlined in [LICENSE_understanding.txt 1-4](https://github.com/tenstorrent/tt-burnin/blob/809f293d/LICENSE_understanding.txt#L1-L4)

### Hardware and IP Rights Clarification

The licensing understanding document provides critical clarification for users:

> "For the avoidance of doubt, this software assists in programming Tenstorrent products. However, making, using, or selling hardware, models, or IP may require the license of rights (such as patent rights) from Tenstorrent or others."

This means that while the software itself is freely licensed under Apache 2.0, certain uses may require additional licensing:

| Activity | Software License | Additional Rights Required |
| --- | --- | --- |
| **Using tt-burnin software** | Apache 2.0 only | None |
| **Programming TT hardware** | Apache 2.0 only | None |
| **Making TT hardware** | Apache 2.0 + | Patent licenses from TT |
| **Selling TT hardware** | Apache 2.0 + | Patent licenses from TT |
| **Developing TT IP/models** | Apache 2.0 + | Patent licenses from TT |

### Patent License Grant

The Apache 2.0 license includes patent protection provisions in [LICENSE 74-88](https://github.com/tenstorrent/tt-burnin/blob/809f293d/LICENSE#L74-L88):

Sources: [LICENSE_understanding.txt 1-4](https://github.com/tenstorrent/tt-burnin/blob/809f293d/LICENSE_understanding.txt#L1-L4)[LICENSE 74-88](https://github.com/tenstorrent/tt-burnin/blob/809f293d/LICENSE#L74-L88)

## Compliance Requirements for Distribution

The tt-burnin project must comply with various compliance requirements depending on the distribution channel and target environment.

### Distribution Channel Compliance

### Dependency License Compatibility

The project must ensure license compatibility with all dependencies. The Apache 2.0 license is compatible with most open source licenses but has specific requirements:

| Dependency Type | License Compatibility | Notes |
| --- | --- | --- |
| **Permissive Licenses** | ✅ Compatible | MIT, BSD, Apache 2.0 |
| **Weak Copyleft** | ✅ Compatible | LGPL (linking only) |
| **Strong Copyleft** | ❌ Incompatible | GPL (would require relicensing) |
| **Proprietary** | ⚠️ Case-by-case | Requires license review |

Sources: [LICENSE 1-207](https://github.com/tenstorrent/tt-burnin/blob/809f293d/LICENSE#L1-L207)[pyproject.toml](https://github.com/tenstorrent/tt-burnin/blob/809f293d/pyproject.toml) (referenced for dependency management)

## Trademark and Attribution Requirements

### Trademark Usage Restrictions

The Apache 2.0 license specifically limits trademark usage in [LICENSE 139-142](https://github.com/tenstorrent/tt-burnin/blob/809f293d/LICENSE#L139-L142):

> "This License does not grant permission to use the trade names, trademarks, service marks, or product names of the Licensor, except as required for reasonable and customary use in describing the origin of the Work and reproducing the content of the NOTICE file."

### Required Attribution

The project maintains proper attribution through several mechanisms:

### Compliance Verification

The project implements compliance verification through its build and release processes:

| Verification Step | Implementation | Location |
| --- | --- | --- |
| **License File Present** | Build system check | Makefile, CI workflows |
| **Copyright Headers** | Automated scanning | CI workflows |
| **Package Metadata** | Build configuration | [pyproject.toml](https://github.com/tenstorrent/tt-burnin/blob/809f293d/pyproject.toml) |
| **Distribution Compliance** | Release workflows | [.github/workflows/](https://github.com/tenstorrent/tt-burnin/blob/809f293d/.github/workflows/) |

Sources: [LICENSE 139-142](https://github.com/tenstorrent/tt-burnin/blob/809f293d/LICENSE#L139-L142)[README.md 105-107](https://github.com/tenstorrent/tt-burnin/blob/809f293d/README.md?plain=1#L105-L107)[pyproject.toml](https://github.com/tenstorrent/tt-burnin/blob/809f293d/pyproject.toml) (for metadata)

## Warranty and Liability Disclaimers

The Apache 2.0 license includes comprehensive warranty disclaimers and liability limitations that users must understand:

### Warranty Disclaimer

As stated in [LICENSE 144-152](https://github.com/tenstorrent/tt-burnin/blob/809f293d/LICENSE#L144-L152) the software is provided "AS IS" without warranties:

*   No warranty of title, non-infringement, merchantability, or fitness for purpose
*   Users assume all risks of use
*   Contributors provide no warranties

### Limitation of Liability

The license limits contributor liability in [LICENSE 154-165](https://github.com/tenstorrent/tt-burnin/blob/809f293d/LICENSE#L154-L165):

*   No liability for direct, indirect, special, incidental, or consequential damages
*   Applies even if contributor was advised of possibility of damages
*   Covers damages from use or inability to use the work

### User Responsibilities

Users accepting the software must understand their responsibilities:

| Responsibility | Description | License Reference |
| --- | --- | --- |
| **Risk Assessment** | Determine appropriateness for intended use | [LICENSE 150-152](https://github.com/tenstorrent/tt-burnin/blob/809f293d/LICENSE#L150-L152) |
| **Compliance** | Follow all license terms and conditions | [LICENSE 90-130](https://github.com/tenstorrent/tt-burnin/blob/809f293d/LICENSE#L90-L130) |
| **Additional Liability** | May accept additional warranty/liability | [LICENSE 166-175](https://github.com/tenstorrent/tt-burnin/blob/809f293d/LICENSE#L166-L175) |

Sources: [LICENSE 144-175](https://github.com/tenstorrent/tt-burnin/blob/809f293d/LICENSE#L144-L175)

Dismiss
Refresh this wiki

Enter email to refresh
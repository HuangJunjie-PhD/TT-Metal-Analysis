---
project: tt-flash
github: https://github.com/tenstorrent/tt-flash
deepwiki: https://deepwiki.com/tenstorrent/tt-flash/11-license-and-legal
---

# License and Legal

Relevant source files
*   [.gitignore](https://github.com/tenstorrent/tt-flash/blob/60c06032/.gitignore)
*   [LICENSE](https://github.com/tenstorrent/tt-flash/blob/60c06032/LICENSE)
*   [LICENSE_understanding.txt](https://github.com/tenstorrent/tt-flash/blob/60c06032/LICENSE_understanding.txt)

This page documents the licensing terms for the tt-flash software, copyright ownership, and important clarifications regarding hardware and intellectual property rights. It covers the Apache 2.0 software license, redistribution requirements, and the distinction between software licensing and hardware/IP licensing.

For information about third-party dependencies and their licenses, see [Dependencies](https://deepwiki.com/tenstorrent/tt-flash/7-dependencies).

* * *

## Software License

tt-flash is licensed under the **Apache License, Version 2.0** (January 2004). The complete license text is located in [LICENSE 1-207](https://github.com/tenstorrent/tt-flash/blob/60c06032/LICENSE#L1-L207)

### Copyright

```
Copyright (c) 2024 Tenstorrent AI ULC
```

The copyright holder is **Tenstorrent AI ULC**, as stated in [LICENSE 205](https://github.com/tenstorrent/tt-flash/blob/60c06032/LICENSE#L205-L205)

**Sources**: [LICENSE 1-207](https://github.com/tenstorrent/tt-flash/blob/60c06032/LICENSE#L1-L207)

* * *

## License Overview

The following diagram illustrates the license structure and what rights it covers:

**Sources**: [LICENSE 1-207](https://github.com/tenstorrent/tt-flash/blob/60c06032/LICENSE#L1-L207)[LICENSE_understanding.txt 1-4](https://github.com/tenstorrent/tt-flash/blob/60c06032/LICENSE_understanding.txt#L1-L4)

* * *

## Key License Terms

### Granted Permissions

The Apache 2.0 license grants the following rights, as defined in [LICENSE 67-88](https://github.com/tenstorrent/tt-flash/blob/60c06032/LICENSE#L67-L88):

| Permission | Description | License Section |
| --- | --- | --- |
| **Copyright License** | Reproduce, prepare derivative works, display, perform, sublicense, and distribute | Section 2 ([LICENSE 67-72](https://github.com/tenstorrent/tt-flash/blob/60c06032/LICENSE#L67-L72)) |
| **Patent License** | Make, use, sell, import, and transfer the work | Section 3 ([LICENSE 74-88](https://github.com/tenstorrent/tt-flash/blob/60c06032/LICENSE#L74-L88)) |
| **Commercial Use** | Use in commercial products and services | Implicit in Sections 2-3 |
| **Modification** | Create and distribute modified versions | Section 2 ([LICENSE 67-72](https://github.com/tenstorrent/tt-flash/blob/60c06032/LICENSE#L67-L72)) |
| **Private Use** | Use privately without distribution | Implicit |

The patent license is irrevocable unless patent litigation is filed against the work ([LICENSE 84-88](https://github.com/tenstorrent/tt-flash/blob/60c06032/LICENSE#L84-L88)).

### Redistribution Requirements

When distributing tt-flash or derivative works, you must comply with [LICENSE 90-129](https://github.com/tenstorrent/tt-flash/blob/60c06032/LICENSE#L90-L129):

**Key Requirements** ([LICENSE 95-122](https://github.com/tenstorrent/tt-flash/blob/60c06032/LICENSE#L95-L122)):

1.   **License Copy**: Include a copy of the Apache 2.0 license ([LICENSE 95-96](https://github.com/tenstorrent/tt-flash/blob/60c06032/LICENSE#L95-L96))
2.   **Attribution Notices**: Retain all copyright, patent, trademark, and attribution notices from the source ([LICENSE 101-105](https://github.com/tenstorrent/tt-flash/blob/60c06032/LICENSE#L101-L105))
3.   **Modified File Notices**: Mark any modified files with prominent notices ([LICENSE 98-99](https://github.com/tenstorrent/tt-flash/blob/60c06032/LICENSE#L98-L99))
4.   **NOTICE File**: If a NOTICE file exists, include it in distributions ([LICENSE 107-122](https://github.com/tenstorrent/tt-flash/blob/60c06032/LICENSE#L107-L122))

**Sources**: [LICENSE 67-129](https://github.com/tenstorrent/tt-flash/blob/60c06032/LICENSE#L67-L129)

* * *

## Hardware and IP Considerations

### Critical Distinction

The Apache 2.0 license covers **only the tt-flash software**. A separate clarification file exists at [LICENSE_understanding.txt 1-4](https://github.com/tenstorrent/tt-flash/blob/60c06032/LICENSE_understanding.txt#L1-L4) that states:

```
For the avoidance of doubt, this software assists in programming Tenstorrent products.

However, making, using, or selling hardware, models, or IP may require 
the license of rights (such as patent rights) from Tenstorrent or others.
```

### Scope Boundaries

### What This Means

| Activity | Apache 2.0 Covers? | May Require Additional Rights? |
| --- | --- | --- |
| Use tt-flash to program Tenstorrent chips you own | ✅ Yes | No |
| Modify tt-flash source code | ✅ Yes | No |
| Distribute tt-flash software | ✅ Yes | No |
| Manufacture Tenstorrent hardware | ❌ No | Yes - from Tenstorrent |
| Create hardware models of Tenstorrent chips | ❌ No | Yes - from Tenstorrent |
| Use Tenstorrent patents in products | ❌ No | Yes - from Tenstorrent or patent holders |
| Redistribute firmware bundles | ❌ No | Depends on firmware license terms |

**Sources**: [LICENSE_understanding.txt 1-4](https://github.com/tenstorrent/tt-flash/blob/60c06032/LICENSE_understanding.txt#L1-L4)

* * *

## Warranty Disclaimer and Liability Limitation

### Disclaimer of Warranty

As stated in [LICENSE 144-152](https://github.com/tenstorrent/tt-flash/blob/60c06032/LICENSE#L144-L152) the software is provided **"AS IS"** without warranties:

> Unless required by applicable law or agreed to in writing, Licensor provides the Work (and each Contributor provides its Contributions) on an "AS IS" BASIS, WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied, including, without limitation, any warranties or conditions of TITLE, NON-INFRINGEMENT, MERCHANTABILITY, or FITNESS FOR A PARTICULAR PURPOSE.

This means:

*   No warranty of merchantability
*   No warranty of fitness for a particular purpose
*   No warranty of non-infringement
*   No warranty of title
*   Users assume all risks

### Limitation of Liability

[LICENSE 154-164](https://github.com/tenstorrent/tt-flash/blob/60c06032/LICENSE#L154-L164) limits contributor liability:

*   No liability for direct damages
*   No liability for indirect damages
*   No liability for special, incidental, or consequential damages
*   No liability for loss of goodwill, work stoppage, or computer failure
*   Applies to all contributors unless prohibited by law

**Sources**: [LICENSE 144-164](https://github.com/tenstorrent/tt-flash/blob/60c06032/LICENSE#L144-L164)

* * *

## Trademark Usage

The Apache 2.0 license **does not grant trademark rights** ([LICENSE 139-142](https://github.com/tenstorrent/tt-flash/blob/60c06032/LICENSE#L139-L142)):

> This License does not grant permission to use the trade names, trademarks, service marks, or product names of the Licensor, except as required for reasonable and customary use in describing the origin of the Work and reproducing the content of the NOTICE file.

### Permitted Trademark Use

| Use Case | Permitted? | Notes |
| --- | --- | --- |
| "Based on tt-flash by Tenstorrent" | ✅ Yes | Describing origin |
| Using Tenstorrent logo in derivative work | ❌ No | Requires separate permission |
| Saying "compatible with Tenstorrent chips" | ✅ Yes | Factual description |
| Implying endorsement by Tenstorrent | ❌ No | Not permitted |

**Sources**: [LICENSE 139-142](https://github.com/tenstorrent/tt-flash/blob/60c06032/LICENSE#L139-L142)

* * *

## Contribution Terms

### Default Terms

Any contribution submitted to tt-flash is assumed to be under Apache 2.0 terms ([LICENSE 131-137](https://github.com/tenstorrent/tt-flash/blob/60c06032/LICENSE#L131-L137)):

> Unless You explicitly state otherwise, any Contribution intentionally submitted for inclusion in the Work by You to the Licensor shall be under the terms and conditions of this License, without any additional terms or conditions.

### Contributor License Grants

When you contribute to tt-flash, you automatically grant:

1.   **Copyright License**: Allow Tenstorrent to use, modify, and distribute your contribution
2.   **Patent License**: License any patents you hold that are necessarily infringed by your contribution

These grants are **perpetual, worldwide, non-exclusive, no-charge, royalty-free, and irrevocable** ([LICENSE 67-88](https://github.com/tenstorrent/tt-flash/blob/60c06032/LICENSE#L67-L88)).

**Sources**: [LICENSE 131-137](https://github.com/tenstorrent/tt-flash/blob/60c06032/LICENSE#L131-L137)[LICENSE 67-88](https://github.com/tenstorrent/tt-flash/blob/60c06032/LICENSE#L67-L88)

* * *

## License Text Location

The complete license text is available in the repository:

### File Locations

| File | Purpose | Lines |
| --- | --- | --- |
| [LICENSE 1-207](https://github.com/tenstorrent/tt-flash/blob/60c06032/LICENSE#L1-L207) | Complete Apache 2.0 license text | 207 |
| [LICENSE_understanding.txt 1-4](https://github.com/tenstorrent/tt-flash/blob/60c06032/LICENSE_understanding.txt#L1-L4) | Hardware/IP clarification | 4 |
| `pyproject.toml` (referenced) | Package metadata with license field | - |
| `debian/copyright` (referenced) | Debian package copyright file | - |

**Sources**: [LICENSE 1-207](https://github.com/tenstorrent/tt-flash/blob/60c06032/LICENSE#L1-L207)[LICENSE_understanding.txt 1-4](https://github.com/tenstorrent/tt-flash/blob/60c06032/LICENSE_understanding.txt#L1-L4)

* * *

## Summary

### What You Can Do

✅ **Freely use tt-flash** to program Tenstorrent chips you own

 ✅ **Modify the source code** for your own purposes

 ✅ **Distribute tt-flash** (with license and attribution)

 ✅ **Use in commercial products** (software incorporating tt-flash)

 ✅ **Create derivative works** (with proper notices)

### What Requires Separate Licensing

⚠️ **Manufacturing Tenstorrent hardware**

 ⚠️ **Using Tenstorrent patents** in your products

 ⚠️ **Creating hardware models** of Tenstorrent designs

 ⚠️ **Using Tenstorrent trademarks** beyond fair use

### Key Obligations When Redistributing

1.   Include the Apache 2.0 license text
2.   Retain all copyright and attribution notices
3.   Mark any files you modify
4.   Provide the software "AS IS" without warranties

For questions about hardware licensing, patent licensing, or commercial arrangements, contact Tenstorrent directly.

**Sources**: [LICENSE 1-207](https://github.com/tenstorrent/tt-flash/blob/60c06032/LICENSE#L1-L207)[LICENSE_understanding.txt 1-4](https://github.com/tenstorrent/tt-flash/blob/60c06032/LICENSE_understanding.txt#L1-L4)

Dismiss
Refresh this wiki

Enter email to refresh
---
project: tt-installer
github: https://github.com/tenstorrent/tt-installer
deepwiki: https://deepwiki.com/tenstorrent/tt-installer/8-licensing-information
---

# Licensing Information

Relevant source files
*   [LICENSE](https://github.com/tenstorrent/tt-installer/blob/19fdd6a2/LICENSE)
*   [LICENSE_understanding.txt](https://github.com/tenstorrent/tt-installer/blob/19fdd6a2/LICENSE_understanding.txt)

This page provides detailed information about the licensing terms governing the Tenstorrent software stack installed via the tt-installer repository and its components. Understanding these licensing terms is crucial for compliance when using, modifying, or distributing Tenstorrent software.

## Primary License

The tt-installer repository is primarily licensed under the Apache License 2.0, a permissive open-source license that allows for free use, modification, and distribution of the software, subject to certain conditions.

Sources: [LICENSE 1-207](https://github.com/tenstorrent/tt-installer/blob/19fdd6a2/LICENSE#L1-L207)

### Key Apache License 2.0 Terms

The Apache License 2.0 grants users the following permissions and imposes these conditions:

| Permissions | Conditions | Limitations |
| --- | --- | --- |
| Commercial use | License and copyright notice | Trademark use |
| Modification | State changes | Liability |
| Distribution | Retain notices | Warranty |
| Patent use |  |  |
| Private use |  |  |

The license allows for free use, modification, and distribution of the software, while requiring that the license and copyright notices be preserved. Any modifications must be clearly marked as such. The license also includes a patent grant, providing protection against patent litigation for work covered by the license.

Sources: [LICENSE 67-88](https://github.com/tenstorrent/tt-installer/blob/19fdd6a2/LICENSE#L67-L88)[LICENSE 90-129](https://github.com/tenstorrent/tt-installer/blob/19fdd6a2/LICENSE#L90-L129)

## Additional Licensing Clarifications

Beyond the primary Apache 2.0 license, there are important clarifications regarding the use of Tenstorrent products:

As stated in the licensing understanding file, while this software assists in programming Tenstorrent products, making, using, or selling hardware, models, or intellectual property may require additional licensing of rights (such as patent rights) from Tenstorrent or others.

Sources: [LICENSE_understanding.txt 1-3](https://github.com/tenstorrent/tt-installer/blob/19fdd6a2/LICENSE_understanding.txt#L1-L3)

## Component Licensing Structure

The tt-installer repository installs multiple components that may have their own licensing terms. The following diagram illustrates the licensing relationship between the installer and the components it installs:

Each component installed by tt-installer (Kernel-Mode Driver, Firmware tools, tt-smi, tt-metalium container, etc.) may be subject to its own specific licensing terms. Users should consult the documentation for each individual component for detailed licensing information.

Sources: [LICENSE 1-207](https://github.com/tenstorrent/tt-installer/blob/19fdd6a2/LICENSE#L1-L207)

## Licensing Compliance Requirements

To maintain compliance with the tt-installer licensing terms, users must:

1.   Retain all copyright notices and license text when distributing the software
2.   Clearly document any modifications made to the original code
3.   Include the LICENSE file with any distribution of the software
4.   Be aware that component-specific licenses may impose additional requirements

Sources: [LICENSE 90-129](https://github.com/tenstorrent/tt-installer/blob/19fdd6a2/LICENSE#L90-L129)

## Patent Considerations

The Apache License 2.0 includes provisions related to patent rights. It grants users a patent license from contributors for their contributions. However, this patent license terminates if a user initiates patent litigation against a contributor regarding the work.

Additionally, as noted in the licensing understanding file, using Tenstorrent hardware or related models may require separate licensing of patent rights from Tenstorrent or other parties.

Sources: [LICENSE 74-88](https://github.com/tenstorrent/tt-installer/blob/19fdd6a2/LICENSE#L74-L88)[LICENSE_understanding.txt 3](https://github.com/tenstorrent/tt-installer/blob/19fdd6a2/LICENSE_understanding.txt#L3-L3)

## Disclaimer of Warranty and Limitation of Liability

In accordance with the Apache License 2.0, the software is provided "as is" without warranties of any kind. Contributors are not liable for damages arising from the use of the software. Users assume all risks associated with using the software.

Sources: [LICENSE 144-164](https://github.com/tenstorrent/tt-installer/blob/19fdd6a2/LICENSE#L144-L164)

## Copyright Information

The tt-installer repository includes the following copyright notice:

```
Copyright (c) 2025 Tenstorrent AI ULC
```

This indicates that Tenstorrent AI ULC holds the copyright to the tt-installer software.

Sources: [LICENSE 205](https://github.com/tenstorrent/tt-installer/blob/19fdd6a2/LICENSE#L205-L205)

## Third-Party Dependencies

When using tt-installer, be aware that it may install third-party software components that are subject to different licenses. While the tt-installer itself is under the Apache License 2.0, the components it installs may use different licenses. Users should review the documentation of each installed component for specific licensing information.

Dismiss
Refresh this wiki

Enter email to refresh
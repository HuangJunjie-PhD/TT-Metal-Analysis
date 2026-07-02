---
project: tenstorrent.github.io
github: https://github.com/tenstorrent/tenstorrent.github.io
deepwiki: https://deepwiki.com/tenstorrent/tenstorrent.github.io/1-overview
---

# Overview

Relevant source files
*   [.gitignore](https://github.com/tenstorrent/tenstorrent.github.io/blob/eda54884/.gitignore)
*   [README.md](https://github.com/tenstorrent/tenstorrent.github.io/blob/eda54884/README.md?plain=1)
*   [core/Makefile](https://github.com/tenstorrent/tenstorrent.github.io/blob/eda54884/core/Makefile)
*   [core/_static/assets/home/tt-lang.png](https://github.com/tenstorrent/tenstorrent.github.io/blob/eda54884/core/_static/assets/home/tt-lang.png)
*   [core/_static/assets/home/tt-metalium.png](https://github.com/tenstorrent/tenstorrent.github.io/blob/eda54884/core/_static/assets/home/tt-metalium.png)
*   [core/_static/assets/home/tt-mlir.png](https://github.com/tenstorrent/tenstorrent.github.io/blob/eda54884/core/_static/assets/home/tt-mlir.png)
*   [core/_static/assets/home/tt-nn.png](https://github.com/tenstorrent/tenstorrent.github.io/blob/eda54884/core/_static/assets/home/tt-nn.png)
*   [core/_templates/home.html](https://github.com/tenstorrent/tenstorrent.github.io/blob/eda54884/core/_templates/home.html)
*   [core/index.rst](https://github.com/tenstorrent/tenstorrent.github.io/blob/eda54884/core/index.rst)
*   [shared/_templates/feedback_widget.html](https://github.com/tenstorrent/tenstorrent.github.io/blob/eda54884/shared/_templates/feedback_widget.html)
*   [shared/_templates/layout.html](https://github.com/tenstorrent/tenstorrent.github.io/blob/eda54884/shared/_templates/layout.html)

This document provides a high-level overview of the Tenstorrent documentation repository, covering its purpose, structure, and main hardware and software components. It serves as the primary technical entry point for understanding the Tenstorrent ecosystem, which spans from bare-metal Tensix core programming to high-level neural network deployment.

## Purpose and Scope

The repository at [https](https://github.com/tenstorrent/tenstorrent.github.io/blob/eda54884/https#LNaN-LNaN) acts as the central hub for all Tenstorrent documentation [README.md 1-5](https://github.com/tenstorrent/tenstorrent.github.io/blob/eda54884/README.md?plain=1#L1-L5) It orchestrates a multi-project Sphinx build system that aggregates documentation for hardware systems, add-in boards, and the tiered software stack [README.md 52-54](https://github.com/tenstorrent/tenstorrent.github.io/blob/eda54884/README.md?plain=1#L52-L54)

### Repository Architecture

The repository is structured to manage multiple documentation projects simultaneously, using a shared theme and centralized build orchestration.

Sources: [README.md 52-58](https://github.com/tenstorrent/tenstorrent.github.io/blob/eda54884/README.md?plain=1#L52-L58)[core/Makefile 5-16](https://github.com/tenstorrent/tenstorrent.github.io/blob/eda54884/core/Makefile#L5-L16)[shared/_templates/layout.html 1-41](https://github.com/tenstorrent/tenstorrent.github.io/blob/eda54884/shared/_templates/layout.html#L1-L41)

## Hardware Components

Tenstorrent designs AI processors based on the Tensix core architecture. These are delivered as Add-In Boards (AIBs) or integrated into complete systems [core/_templates/home.html 243-269](https://github.com/tenstorrent/tenstorrent.github.io/blob/eda54884/core/_templates/home.html#L243-L269)

### Add-In Boards (AIBs)

The documentation covers multiple generations of Tensix processors:

*   **Blackhole™**: The latest generation featuring PCIe 5.0 and 800G networking [core/_templates/home.html 246-249](https://github.com/tenstorrent/tenstorrent.github.io/blob/eda54884/core/_templates/home.html#L246-L249)
*   **Wormhole™**: Current production generation used in multi-node mesh topologies [core/_templates/home.html 250-253](https://github.com/tenstorrent/tenstorrent.github.io/blob/eda54884/core/_templates/home.html#L250-L253)
*   **Grayskull™**: Legacy generation (deprecated) [core/_templates/home.html 314-317](https://github.com/tenstorrent/tenstorrent.github.io/blob/eda54884/core/_templates/home.html#L314-L317)

### Complete Systems

Documentation is provided for various form factors:

*   **TT-QuietBox™**: Liquid-cooled workstations designed for office environments [core/_templates/home.html 260-263](https://github.com/tenstorrent/tenstorrent.github.io/blob/eda54884/core/_templates/home.html#L260-L263)
*   **TT-LoudBox™**: High-performance air-cooled workstations and servers (e.g., T3000) [core/_templates/home.html 264-267](https://github.com/tenstorrent/tenstorrent.github.io/blob/eda54884/core/_templates/home.html#L264-L267)
*   **Rack-mount Servers**: High-density solutions like the T7000 [core/index.rst 23](https://github.com/tenstorrent/tenstorrent.github.io/blob/eda54884/core/index.rst#L23-L23)

Sources: [core/index.rst 18-23](https://github.com/tenstorrent/tenstorrent.github.io/blob/eda54884/core/index.rst#L18-L23)[core/_templates/home.html 243-269](https://github.com/tenstorrent/tenstorrent.github.io/blob/eda54884/core/_templates/home.html#L243-L269)

## Software Stack Layers

The Tenstorrent software stack is layered to support different developer needs, from automated model compilation to manual kernel optimization.

Sources: [core/index.rst 25-35](https://github.com/tenstorrent/tenstorrent.github.io/blob/eda54884/core/index.rst#L25-L35)[core/_templates/home.html 271-310](https://github.com/tenstorrent/tenstorrent.github.io/blob/eda54884/core/_templates/home.html#L271-L310)

### Key Software Entities

*   **TT-NN™**: A high-level neural network library providing Python and C++ APIs for optimized operations [core/_templates/home.html 274-281](https://github.com/tenstorrent/tenstorrent.github.io/blob/eda54884/core/_templates/home.html#L274-L281)
*   **TT-Metalium™**: The low-level programming SDK for direct Tensix core and NoC (Network-on-Chip) management [core/_templates/home.html 282-289](https://github.com/tenstorrent/tenstorrent.github.io/blob/eda54884/core/_templates/home.html#L282-L289)
*   **TT-Forge**: A compiler stack utilizing MLIR to lower models from PyTorch, TensorFlow, and JAX [core/index.rst 30](https://github.com/tenstorrent/tenstorrent.github.io/blob/eda54884/core/index.rst#L30-L30)
*   **TT-KMD**: The Kernel Mode Driver that manages PCIe communication and memory mapping [core/index.rst 34](https://github.com/tenstorrent/tenstorrent.github.io/blob/eda54884/core/index.rst#L34-L34)

Sources: [core/index.rst 25-35](https://github.com/tenstorrent/tenstorrent.github.io/blob/eda54884/core/index.rst#L25-L35)[core/_templates/home.html 271-310](https://github.com/tenstorrent/tenstorrent.github.io/blob/eda54884/core/_templates/home.html#L271-L310)

## Documentation System Implementation

The documentation site is built using a distributed model where project-specific docs reside in their own repositories but are unified under the `docs.tenstorrent.com` domain [README.md 59-64](https://github.com/tenstorrent/tenstorrent.github.io/blob/eda54884/README.md?plain=1#L59-L64)

### Data Flow for Builds

1.   **Orchestration**: The `build_docs.py` script at the repo root triggers Sphinx builds for local folders (like `core` and `forge`) [README.md 18-22](https://github.com/tenstorrent/tenstorrent.github.io/blob/eda54884/README.md?plain=1#L18-L22)
2.   **Shared Assets**: Projects import CSS and HTML templates from the `shared/` directory to maintain visual consistency [README.md 72-74](https://github.com/tenstorrent/tenstorrent.github.io/blob/eda54884/README.md?plain=1#L72-L74)
3.   **Deployment**: GitHub Actions executes the build on every push to `main`, outputting the static site to the `output/` directory, which is then served via GitHub Pages [README.md 65-69](https://github.com/tenstorrent/tenstorrent.github.io/blob/eda54884/README.md?plain=1#L65-L69)

### Navigation and Feedback

*   **TOC Mapping**: The `core/index.rst` file defines the primary navigation sidebar using `.. toctree::` directives [core/index.rst 9-50](https://github.com/tenstorrent/tenstorrent.github.io/blob/eda54884/core/index.rst#L9-L50)
*   **Feedback Loop**: A custom `feedback_widget.html` is injected into the layout, allowing users to report inaccuracies directly to the documentation team [shared/_templates/feedback_widget.html 1-43](https://github.com/tenstorrent/tenstorrent.github.io/blob/eda54884/shared/_templates/feedback_widget.html#L1-L43)

Sources: [README.md 18-22](https://github.com/tenstorrent/tenstorrent.github.io/blob/eda54884/README.md?plain=1#L18-L22)[README.md 52-64](https://github.com/tenstorrent/tenstorrent.github.io/blob/eda54884/README.md?plain=1#L52-L64)[core/index.rst 9-50](https://github.com/tenstorrent/tenstorrent.github.io/blob/eda54884/core/index.rst#L9-L50)[shared/_templates/layout.html 36-41](https://github.com/tenstorrent/tenstorrent.github.io/blob/eda54884/shared/_templates/layout.html#L36-L41)

## Support and Resources

The documentation links to several external community and support platforms:

*   **Bounty Program**: A structured program for rewarding community contributions to the open-source stack [core/index.rst 49](https://github.com/tenstorrent/tenstorrent.github.io/blob/eda54884/core/index.rst#L49-L49)
*   **Developer Hub**: Central location for demos, vLLM server configurations, and model-specific guides [core/index.rst 13-16](https://github.com/tenstorrent/tenstorrent.github.io/blob/eda54884/core/index.rst#L13-L16)
*   **Discord**: Real-time community support and developer interaction [core/_templates/home.html 338](https://github.com/tenstorrent/tenstorrent.github.io/blob/eda54884/core/_templates/home.html#L338-L338)

Sources: [core/index.rst 37-50](https://github.com/tenstorrent/tenstorrent.github.io/blob/eda54884/core/index.rst#L37-L50)[core/_templates/home.html 331-345](https://github.com/tenstorrent/tenstorrent.github.io/blob/eda54884/core/_templates/home.html#L331-L345)

Dismiss
Refresh this wiki

Enter email to refresh
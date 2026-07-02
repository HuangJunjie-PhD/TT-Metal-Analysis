---
project: tt-emule
github: https://github.com/tenstorrent/tt-emule
deepwiki: https://deepwiki.com/tenstorrent/tt-emule/1-tt-emule-overview
---

# tt-emule Overview

Relevant source files
*   [.claude/references/structure.yaml](https://github.com/tenstorrent/tt-emule/blob/a57cf072/.claude/references/structure.yaml)
*   [.claude/skills/uplift/SKILL.md](https://github.com/tenstorrent/tt-emule/blob/a57cf072/.claude/skills/uplift/SKILL.md?plain=1)
*   [BUILD_GUIDE.md](https://github.com/tenstorrent/tt-emule/blob/a57cf072/BUILD_GUIDE.md?plain=1)
*   [CMakeLists.txt](https://github.com/tenstorrent/tt-emule/blob/a57cf072/CMakeLists.txt)
*   [GETTING_STARTED.md](https://github.com/tenstorrent/tt-emule/blob/a57cf072/GETTING_STARTED.md?plain=1)
*   [IMPLEMENTATION_REPORT.md](https://github.com/tenstorrent/tt-emule/blob/a57cf072/IMPLEMENTATION_REPORT.md?plain=1)
*   [README.md](https://github.com/tenstorrent/tt-emule/blob/a57cf072/README.md?plain=1)
*   [docs/QUASAR_MATMUL_REPORT.md](https://github.com/tenstorrent/tt-emule/blob/a57cf072/docs/QUASAR_MATMUL_REPORT.md?plain=1)
*   [include/tt_emule/host_api.hpp](https://github.com/tenstorrent/tt-emule/blob/a57cf072/include/tt_emule/host_api.hpp)
*   [src/host_api.cpp](https://github.com/tenstorrent/tt-emule/blob/a57cf072/src/host_api.cpp)
*   [src/kernel_runner.cpp](https://github.com/tenstorrent/tt-emule/blob/a57cf072/src/kernel_runner.cpp)

tt-emule is a standalone C++ software emulator for Tenstorrent device-level APIs. It models the multi-core execution model—including per-core L1 SRAM, banked DRAM, circular-buffer (CB) synchronization, Dataflow Buffer (DFB) synchronization, NOC communication, semaphores, and the DEST register file—entirely on the host CPU.

The emulator allows kernels to be developed, run, and debugged without Tenstorrent silicon [IMPLEMENTATION_REPORT.md 1-8](https://github.com/tenstorrent/tt-emule/blob/a57cf072/IMPLEMENTATION_REPORT.md?plain=1#L1-L8) It integrates into `tt-metal` at the User-Mode Driver (UMD) boundary, enabling real `ttnn` and Direct-to-Metal (D2M) workloads to execute unmodified on the host by routing execution through `execute_program_emulated` when `-DTT_METAL_USE_EMULE=ON` is specified [IMPLEMENTATION_REPORT.md 112-117](https://github.com/tenstorrent/tt-emule/blob/a57cf072/IMPLEMENTATION_REPORT.md?plain=1#L112-L117)

### Core Capabilities and Limitations

| Hardware Concept | Emulation Strategy |
| --- | --- |
| **Core L1 SRAM** | `MAP_32BIT``mmap` slots from a shared `L1Pool`[README.md 29](https://github.com/tenstorrent/tt-emule/blob/a57cf072/README.md?plain=1#L29-L29) |
| **DRAM** | Per-bank `mmap` regions with proper NOC address routing [README.md 30](https://github.com/tenstorrent/tt-emule/blob/a57cf072/README.md?plain=1#L30-L30) |
| **Execution Threads** | OS threads (e.g., 3 threads for Wormhole/Blackhole; up to 12 for Quasar) [README.md 31-32](https://github.com/tenstorrent/tt-emule/blob/a57cf072/README.md?plain=1#L31-L32) |
| **Synchronization** | Mutex + condition variables (`CBSyncState`) or MPMC tile-counters (`DFBSyncState`) [README.md 33-34](https://github.com/tenstorrent/tt-emule/blob/a57cf072/README.md?plain=1#L33-L34) |
| **Compute Engine** | Private DEST register file modeled as `float[16][1024]`[IMPLEMENTATION_REPORT.md 106-108](https://github.com/tenstorrent/tt-emule/blob/a57cf072/IMPLEMENTATION_REPORT.md?plain=1#L106-L108) |
| **NOC Communication** | Synchronous `memcpy` for reads/writes; multicast via per-core address maps [README.md 36](https://github.com/tenstorrent/tt-emule/blob/a57cf072/README.md?plain=1#L36-L36) |

**What is NOT emulated:**

*   Real RISC-V ISA or cycle-accurate timing [README.md 44](https://github.com/tenstorrent/tt-emule/blob/a57cf072/README.md?plain=1#L44-L44)
*   Actual asynchronous NOC (all DMA is synchronous) [README.md 45](https://github.com/tenstorrent/tt-emule/blob/a57cf072/README.md?plain=1#L45-L45)
*   Ethernet/dispatch fabric or hardware timing of tile layout conversions [README.md 46-49](https://github.com/tenstorrent/tt-emule/blob/a57cf072/README.md?plain=1#L46-L49)

### System Architecture: Host to Kernel Bridge

The following diagram illustrates how the host-side C++ entities in `tt_emule` map to the emulated hardware components and the JIT-compiled kernel environment.

**System Entity Mapping**

Sources: [include/tt_emule/device.hpp 81-91](https://github.com/tenstorrent/tt-emule/blob/a57cf072/include/tt_emule/device.hpp#L81-L91)[include/tt_emule/program.hpp 142-146](https://github.com/tenstorrent/tt-emule/blob/a57cf072/include/tt_emule/program.hpp#L142-L146)[src/kernel_runner.cpp 16-25](https://github.com/tenstorrent/tt-emule/blob/a57cf072/src/kernel_runner.cpp#L16-L25)[include/tt_emule/l1_pool.hpp 136-141](https://github.com/tenstorrent/tt-emule/blob/a57cf072/include/tt_emule/l1_pool.hpp#L136-L141)

* * *

### Supported Architectures

tt-emule supports three primary Tenstorrent architectures, each with distinct threading and synchronization models.

| Architecture | Target Core | Threads per Core | Primary Sync Mechanism |
| --- | --- | --- | --- |
| **Wormhole (N150)** | Tensix | 3 (Reader, Compute, Writer) | Circular Buffers (CB) |
| **Blackhole (P100)** | Tensix | 3 (Reader, Compute, Writer) | Circular Buffers (CB) |
| **Quasar (Q1)** | Tensix Neo | Up to 12 (8 DM + 4 Compute) | Dataflow Buffers (DFB) |

For details on architecture-specific constraints and thread models, see [Architecture Overview & Supported Targets](https://deepwiki.com/tenstorrent/tt-emule/1.2-architecture-overview-and-supported-targets).

Sources: [README.md 19-24](https://github.com/tenstorrent/tt-emule/blob/a57cf072/README.md?plain=1#L19-L24)[IMPLEMENTATION_REPORT.md 94-102](https://github.com/tenstorrent/tt-emule/blob/a57cf072/IMPLEMENTATION_REPORT.md?plain=1#L94-L102)

* * *

### Repository Layout

The codebase is divided into host-side management, the JIT-facing hardware abstraction layer (shim), and testing infrastructure.

*   **`include/tt_emule/`**: Host-side types and API (e.g., `Device`, `Core`, `Program`, `Buffer`) [README.md 61-73](https://github.com/tenstorrent/tt-emule/blob/a57cf072/README.md?plain=1#L61-L73)
*   **`include/jit_hw/`**: Kernel-side stubs and APIs (e.g., `cb_api.h`, `dfb_api.h`, `compute/`, `dataflow/`) [README.md 76-85](https://github.com/tenstorrent/tt-emule/blob/a57cf072/README.md?plain=1#L76-L85)
*   **`src/`**: Implementation of host APIs and the `kernel_runner` which handles thread spawning and JIT lifecycle [README.md 86-88](https://github.com/tenstorrent/tt-emule/blob/a57cf072/README.md?plain=1#L86-L88)
*   **`scripts/`**: Regression and test orchestration scripts [IMPLEMENTATION_REPORT.md 131-138](https://github.com/tenstorrent/tt-emule/blob/a57cf072/IMPLEMENTATION_REPORT.md?plain=1#L131-L138)

**Core Execution Flow**

Sources: [src/host_api.cpp 122-124](https://github.com/tenstorrent/tt-emule/blob/a57cf072/src/host_api.cpp#L122-L124)[src/kernel_runner.cpp 36-45](https://github.com/tenstorrent/tt-emule/blob/a57cf072/src/kernel_runner.cpp#L36-L45)[src/kernel_runner.cpp 16-21](https://github.com/tenstorrent/tt-emule/blob/a57cf072/src/kernel_runner.cpp#L16-L21)[IMPLEMENTATION_REPORT.md 112-117](https://github.com/tenstorrent/tt-emule/blob/a57cf072/IMPLEMENTATION_REPORT.md?plain=1#L112-L117)

* * *

### Integration with tt-metal

tt-emule is not built standalone; it is compiled as part of `tt-metal`. The integration relies on a "Zero Fake Headers" philosophy, meaning tests link against real `tt-metal` libraries and use real headers, with emulation injected at the UMD boundary [IMPLEMENTATION_REPORT.md 72-77](https://github.com/tenstorrent/tt-emule/blob/a57cf072/IMPLEMENTATION_REPORT.md?plain=1#L72-L77)

**Key Integration Points:**

*   **CMake**: Enabled via `-DTT_METAL_USE_EMULE=ON`[GETTING_STARTED.md 42](https://github.com/tenstorrent/tt-emule/blob/a57cf072/GETTING_STARTED.md?plain=1#L42-L42)
*   **UMD**: Emulation is routed through the `SWEmuleChip` which implements the `IDevice` interface [IMPLEMENTATION_REPORT.md 79-83](https://github.com/tenstorrent/tt-emule/blob/a57cf072/IMPLEMENTATION_REPORT.md?plain=1#L79-L83)
*   **JIT**: Kernels are compiled using `clang-20` and loaded into the process space, allowing direct access to mmap'd L1 memory [BUILD_GUIDE.md 19](https://github.com/tenstorrent/tt-emule/blob/a57cf072/BUILD_GUIDE.md?plain=1#L19-L19)[IMPLEMENTATION_REPORT.md 113-115](https://github.com/tenstorrent/tt-emule/blob/a57cf072/IMPLEMENTATION_REPORT.md?plain=1#L113-L115)

For a step-by-step guide on setting up the environment and building the project, see [Getting Started & Build Guide](https://deepwiki.com/tenstorrent/tt-emule/1.1-getting-started-and-build-guide).

Sources: [IMPLEMENTATION_REPORT.md 72-88](https://github.com/tenstorrent/tt-emule/blob/a57cf072/IMPLEMENTATION_REPORT.md?plain=1#L72-L88)[GETTING_STARTED.md 33-51](https://github.com/tenstorrent/tt-emule/blob/a57cf072/GETTING_STARTED.md?plain=1#L33-L51)

Dismiss
Refresh this wiki

Enter email to refresh
---
project: tt-emule
github: https://github.com/tenstorrent/tt-emule
deepwiki: https://deepwiki.com/tenstorrent/tt-emule/2-core-emulation-architecture
---

# Core Emulation Architecture

Relevant source files
*   [.claude/references/structure.yaml](https://github.com/tenstorrent/tt-emule/blob/a57cf072/.claude/references/structure.yaml)
*   [BUILD_GUIDE.md](https://github.com/tenstorrent/tt-emule/blob/a57cf072/BUILD_GUIDE.md?plain=1)
*   [GETTING_STARTED.md](https://github.com/tenstorrent/tt-emule/blob/a57cf072/GETTING_STARTED.md?plain=1)
*   [IMPLEMENTATION_REPORT.md](https://github.com/tenstorrent/tt-emule/blob/a57cf072/IMPLEMENTATION_REPORT.md?plain=1)
*   [docs/QUASAR_MATMUL_REPORT.md](https://github.com/tenstorrent/tt-emule/blob/a57cf072/docs/QUASAR_MATMUL_REPORT.md?plain=1)
*   [include/jit_hw/api/compute/common_globals.h](https://github.com/tenstorrent/tt-emule/blob/a57cf072/include/jit_hw/api/compute/common_globals.h)
*   [include/tt_emule/device.hpp](https://github.com/tenstorrent/tt-emule/blob/a57cf072/include/tt_emule/device.hpp)
*   [tests/CMakeLists.txt](https://github.com/tenstorrent/tt-emule/blob/a57cf072/tests/CMakeLists.txt)

The `tt-emule` system provides a high-fidelity software model of Tenstorrent hardware architectures, including Wormhole, Blackhole, and Quasar (Tensix Neo). It is designed to plug into `tt-metal` at the UMD (User-Mode Driver) boundary, allowing the full software stack—from `ttnn` and `tt-mlir` down to individual RISC-V kernels—to execute on a host CPU without silicon [IMPLEMENTATION_REPORT.md 1-8](https://github.com/tenstorrent/tt-emule/blob/a57cf072/IMPLEMENTATION_REPORT.md?plain=1#L1-L8)

## Object Model: Device, Core & Program

The emulation runtime is built around a hierarchy of host-side C++ objects that mirror the hardware topology. The primary entry point is the `SWEmuleChip` (exposed via the `tt_emule::Device` class), which manages a collection of `tt_emule::Core` objects [include/tt_emule/device.hpp 81-91](https://github.com/tenstorrent/tt-emule/blob/a57cf072/include/tt_emule/device.hpp#L81-L91)

*   **Device (`SWEmuleChip`):** Implements the `IDevice` interface. It manages the global memory layout (DRAM and L1) and provides the API for buffer allocation and command queue management [src/host_api.cpp 2-5](https://github.com/tenstorrent/tt-emule/blob/a57cf072/src/host_api.cpp#L2-L5)
*   **Core (`tt_emule::Core`):** Represents a single physical tile on the chip. Depending on its `CoreRole` (WORKER or DRAM), it manages a segment of mmap'd memory representing L1 SRAM or a DRAM bank [include/tt_emule/device.hpp 38-67](https://github.com/tenstorrent/tt-emule/blob/a57cf072/include/tt_emule/device.hpp#L38-L67)
*   **Program (`tt_emule::Program`):** A container for kernels and their configurations. It includes `KernelDescriptor` objects for DM (Data Movement) and Compute processors, as well as configurations for Circular Buffers (CBs) or Dataflow Buffers (DFBs) [include/tt_emule/program.hpp 142-145](https://github.com/tenstorrent/tt-emule/blob/a57cf072/include/tt_emule/program.hpp#L142-L145)

For details on the object lifecycle and host API, see [Device, Core & Program Model](https://deepwiki.com/tenstorrent/tt-emule/2.1-device-core-and-program-model).

### Natural Language to Code Entity Mapping: Host Model

Sources: [include/tt_emule/device.hpp 53-91](https://github.com/tenstorrent/tt-emule/blob/a57cf072/include/tt_emule/device.hpp#L53-L91)[include/tt_emule/program.hpp 142-145](https://github.com/tenstorrent/tt-emule/blob/a57cf072/include/tt_emule/program.hpp#L142-L145)

## Memory Emulation & Address Translation

`tt-emule` employs a "host-pointer-everywhere" convention. All device memory is owned by `Core` objects and backed by `mmap`.

*   **L1 SRAM:** Worker cores use `MAP_32BIT` to ensure that L1 addresses fit within a 32-bit `uint32_t`, matching hardware pointer widths [include/tt_emule/device.hpp 195-201](https://github.com/tenstorrent/tt-emule/blob/a57cf072/include/tt_emule/device.hpp#L195-L201)
*   **DRAM:** Emulated as `Core` objects with a `DRAM` role. These are accessed via 64-bit host pointers to accommodate large memory spaces [include/tt_emule/device.hpp 97-108](https://github.com/tenstorrent/tt-emule/blob/a57cf072/include/tt_emule/device.hpp#L97-L108)
*   **L1Pool:** Manages the allocation of these mmap slots to ensure isolation between cores [include/tt_emule/l1_pool.hpp 138-141](https://github.com/tenstorrent/tt-emule/blob/a57cf072/include/tt_emule/l1_pool.hpp#L138-L141)

For details on memory mapping and the `MEM_ZEROS` contract, see [Memory Emulation: L1, DRAM & Address Translation](https://deepwiki.com/tenstorrent/tt-emule/2.2-memory-emulation:-l1-dram-and-address-translation).

## Thread Model & JIT Pipeline

The emulator models hardware concurrency by spawning multiple host threads per core. The specific model depends on the target architecture:

| Architecture | Threads per Core | Roles |
| --- | --- | --- |
| **Wormhole / Blackhole** | 3 | NOC Reader, Compute, NOC Writer |
| **Quasar (Neo)** | Up to 12 | 8 Data Movement (DM0-7), 4 Compute (E0-3) |

Kernels are JIT-compiled using a pipeline that rewrites RISC-V specific code (such as CSR accesses) into C++ compatible with the host. The compiled `.so` files are `dlopen`'d, and thread-local state (like `__processor_id` and `__rt_args`) is injected before execution [src/kernel_runner.cpp 38-46](https://github.com/tenstorrent/tt-emule/blob/a57cf072/src/kernel_runner.cpp#L38-L46)

For details on the compilation pipeline and thread-local storage, see [Thread Model & Kernel Execution](https://deepwiki.com/tenstorrent/tt-emule/2.3-thread-model-and-kernel-execution).

### Natural Language to Code Entity Mapping: Execution Model

Sources: [src/kernel_runner.cpp 36-48](https://github.com/tenstorrent/tt-emule/blob/a57cf072/src/kernel_runner.cpp#L36-L48)[docs/QUASAR_MATMUL_REPORT.md 57-61](https://github.com/tenstorrent/tt-emule/blob/a57cf072/docs/QUASAR_MATMUL_REPORT.md?plain=1#L57-L61)

## Quasar / Tensix Neo Specifics

Quasar emulation introduces significant changes to the synchronization model. Unlike the SPSC (Single-Producer Single-Consumer) Circular Buffers used in Wormhole, Quasar utilizes **Dataflow Buffers (DFBs)**. These are MPMC (Multi-Producer Multi-Consumer) FIFOs built on `TileCounter` primitives [docs/QUASAR_MATMUL_REPORT.md 23-24](https://github.com/tenstorrent/tt-emule/blob/a57cf072/docs/QUASAR_MATMUL_REPORT.md?plain=1#L23-L24)

*   **Shared L1:** All 12 threads in a Neo core share a 4 MB L1 region [docs/QUASAR_MATMUL_REPORT.md 19-22](https://github.com/tenstorrent/tt-emule/blob/a57cf072/docs/QUASAR_MATMUL_REPORT.md?plain=1#L19-L22)
*   **DFB Sync:** Uses `posted` and `acked` atomic counters with condition variables (`data_cv`, `space_cv`) to manage tile-level flow control [docs/QUASAR_MATMUL_REPORT.md 67-72](https://github.com/tenstorrent/tt-emule/blob/a57cf072/docs/QUASAR_MATMUL_REPORT.md?plain=1#L67-L72)

For details on Neo core threading and DFB access patterns, see [Quasar / Tensix Neo Emulation](https://deepwiki.com/tenstorrent/tt-emule/2.4-quasar-tensix-neo-emulation).

## Integration with tt-metal

The emulator is activated within `tt-metal` by the `-DTT_METAL_USE_EMULE=ON` CMake flag [BUILD_GUIDE.md 118](https://github.com/tenstorrent/tt-emule/blob/a57cf072/BUILD_GUIDE.md?plain=1#L118-L118) This triggers a dispatch branch in the metal runtime that routes `EnqueueProgram` calls to the `execute_program_emulated` runner instead of the hardware Command Queue [IMPLEMENTATION_REPORT.md 112-117](https://github.com/tenstorrent/tt-emule/blob/a57cf072/IMPLEMENTATION_REPORT.md?plain=1#L112-L117)

Sources: [IMPLEMENTATION_REPORT.md 92-117](https://github.com/tenstorrent/tt-emule/blob/a57cf072/IMPLEMENTATION_REPORT.md?plain=1#L92-L117)[include/tt_emule/device.hpp 33-193](https://github.com/tenstorrent/tt-emule/blob/a57cf072/include/tt_emule/device.hpp#L33-L193)[include/tt_emule/program.hpp 142-160](https://github.com/tenstorrent/tt-emule/blob/a57cf072/include/tt_emule/program.hpp#L142-L160)[docs/QUASAR_MATMUL_REPORT.md 15-36](https://github.com/tenstorrent/tt-emule/blob/a57cf072/docs/QUASAR_MATMUL_REPORT.md?plain=1#L15-L36)

Dismiss
Refresh this wiki

Enter email to refresh
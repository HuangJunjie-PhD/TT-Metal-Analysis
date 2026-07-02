---
project: tt-emule
github: https://github.com/tenstorrent/tt-emule
deepwiki: https://deepwiki.com/tenstorrent/tt-emule/7-glossary
---

# Glossary

Relevant source files
*   [.claude/references/structure.yaml](https://github.com/tenstorrent/tt-emule/blob/a57cf072/.claude/references/structure.yaml)
*   [.github/known-failures-d2m-blackhole.txt](https://github.com/tenstorrent/tt-emule/blob/a57cf072/.github/known-failures-d2m-blackhole.txt)
*   [.github/known-failures-d2m-wormhole.txt](https://github.com/tenstorrent/tt-emule/blob/a57cf072/.github/known-failures-d2m-wormhole.txt)
*   [.github/workflows/nightly-metal-upstream.yml](https://github.com/tenstorrent/tt-emule/blob/a57cf072/.github/workflows/nightly-metal-upstream.yml)
*   [.github/workflows/pr-metal-regression.yml](https://github.com/tenstorrent/tt-emule/blob/a57cf072/.github/workflows/pr-metal-regression.yml)
*   [BUILD_GUIDE.md](https://github.com/tenstorrent/tt-emule/blob/a57cf072/BUILD_GUIDE.md?plain=1)
*   [GETTING_STARTED.md](https://github.com/tenstorrent/tt-emule/blob/a57cf072/GETTING_STARTED.md?plain=1)
*   [IMPLEMENTATION_REPORT.md](https://github.com/tenstorrent/tt-emule/blob/a57cf072/IMPLEMENTATION_REPORT.md?plain=1)
*   [docs/DFB_EMULATION.md](https://github.com/tenstorrent/tt-emule/blob/a57cf072/docs/DFB_EMULATION.md?plain=1)
*   [docs/QUASAR_EMULATION.md](https://github.com/tenstorrent/tt-emule/blob/a57cf072/docs/QUASAR_EMULATION.md?plain=1)
*   [docs/QUASAR_MATMUL_REPORT.md](https://github.com/tenstorrent/tt-emule/blob/a57cf072/docs/QUASAR_MATMUL_REPORT.md?plain=1)
*   [docs/kernel-api-layers.md](https://github.com/tenstorrent/tt-emule/blob/a57cf072/docs/kernel-api-layers.md?plain=1)
*   [include/jit_hw/api/compute/bfp4.h](https://github.com/tenstorrent/tt-emule/blob/a57cf072/include/jit_hw/api/compute/bfp4.h)
*   [include/jit_hw/api/compute/bfp8.h](https://github.com/tenstorrent/tt-emule/blob/a57cf072/include/jit_hw/api/compute/bfp8.h)
*   [include/jit_hw/api/compute/common.h](https://github.com/tenstorrent/tt-emule/blob/a57cf072/include/jit_hw/api/compute/common.h)
*   [include/jit_hw/api/compute/compute_kernel_api.h](https://github.com/tenstorrent/tt-emule/blob/a57cf072/include/jit_hw/api/compute/compute_kernel_api.h)
*   [include/jit_hw/api/compute/eltwise_unary/clamp.h](https://github.com/tenstorrent/tt-emule/blob/a57cf072/include/jit_hw/api/compute/eltwise_unary/clamp.h)
*   [include/jit_hw/api/compute/experimental/fill_arange.h](https://github.com/tenstorrent/tt-emule/blob/a57cf072/include/jit_hw/api/compute/experimental/fill_arange.h)
*   [include/jit_hw/api/compute/topk_xl.h](https://github.com/tenstorrent/tt-emule/blob/a57cf072/include/jit_hw/api/compute/topk_xl.h)
*   [include/jit_hw/api/dataflow/dataflow_api.h](https://github.com/tenstorrent/tt-emule/blob/a57cf072/include/jit_hw/api/dataflow/dataflow_api.h)
*   [include/jit_hw/llk_pack.h](https://github.com/tenstorrent/tt-emule/blob/a57cf072/include/jit_hw/llk_pack.h)
*   [scripts/run_ttnn_pytests_blackhole.sh](https://github.com/tenstorrent/tt-emule/blob/a57cf072/scripts/run_ttnn_pytests_blackhole.sh)
*   [scripts/run_ttnn_pytests_wormhole.sh](https://github.com/tenstorrent/tt-emule/blob/a57cf072/scripts/run_ttnn_pytests_wormhole.sh)
*   [tt-metal-pin.txt](https://github.com/tenstorrent/tt-emule/blob/a57cf072/tt-metal-pin.txt)

This page provides definitions for codebase-specific terms, hardware concepts, and jargon used within the `tt-emule` project. It serves as a technical reference for understanding how Tenstorrent hardware features are mapped to software emulation.

## Core Emulation Terms

### SWEmuleChip

The primary host-side representation of a Tenstorrent device. It implements the `tt::tt_metal::IDevice` interface and manages the lifecycle of emulated cores and memory [include/tt_emule/device.hpp 91](https://github.com/tenstorrent/tt-emule/blob/a57cf072/include/tt_emule/device.hpp#L91-L91) It acts as the container for all `Core` objects and the `L1Pool`.

### Core

An object representing a single functional unit on the device, which can be a Worker Tenstorrent core or a DRAM bank [include/tt_emule/device.hpp 88](https://github.com/tenstorrent/tt-emule/blob/a57cf072/include/tt_emule/device.hpp#L88-L88) Each `Core` owns its memory region (L1 or DRAM bank) backed by `mmap`[IMPLEMENTATION_REPORT.md 79-83](https://github.com/tenstorrent/tt-emule/blob/a57cf072/IMPLEMENTATION_REPORT.md?plain=1#L79-L83)

### L1Pool

A memory management system that allocates large contiguous blocks of host memory using `MAP_32BIT` to emulate the 32-bit address space of Tenstorrent L1 SRAM [include/tt_emule/l1_pool.hpp 141](https://github.com/tenstorrent/tt-emule/blob/a57cf072/include/tt_emule/l1_pool.hpp#L141-L141) It allows kernels to use host pointers directly while maintaining a fixed offset relationship that mimics hardware [include/jit_hw/api/dataflow/dataflow_api.h 92-100](https://github.com/tenstorrent/tt-emule/blob/a57cf072/include/jit_hw/api/dataflow/dataflow_api.h#L92-L100)

### JIT Kernel

Kernels written in C++ that are compiled "Just-In-Time" into shared objects (`.so`) by the host compiler (e.g., clang-20) [IMPLEMENTATION_REPORT.md 112-117](https://github.com/tenstorrent/tt-emule/blob/a57cf072/IMPLEMENTATION_REPORT.md?plain=1#L112-L117) These are loaded via `dlopen` and executed by the `emulated_program_runner`.

## Dataflow and Synchronization

### Circular Buffer (CB)

A FIFO-based synchronization primitive used by Wormhole and Blackhole architectures to move data between NOC reader, compute, and NOC writer threads [include/tt_emule/circular_buffer.hpp 72](https://github.com/tenstorrent/tt-emule/blob/a57cf072/include/tt_emule/circular_buffer.hpp#L72-L72) In emulation, it uses `CBSyncState` containing mutexes and condition variables to manage producer-consumer flow [include/tt_emule/cb_sync_state.hpp 62](https://github.com/tenstorrent/tt-emule/blob/a57cf072/include/tt_emule/cb_sync_state.hpp#L62-L62)

### Dataflow Buffer (DFB)

The Quasar-specific replacement for Circular Buffers. DFBs are MPMC (Multi-Producer Multi-Consumer) tile-counter FIFOs [include/tt_emule/dataflow_buffer.hpp 78](https://github.com/tenstorrent/tt-emule/blob/a57cf072/include/tt_emule/dataflow_buffer.hpp#L78-L78) They allow multiple Dataflow Engines (DMs) and Compute Engines to synchronize over a shared L1 memory space [docs/DFB_EMULATION.md](https://github.com/tenstorrent/tt-emule/blob/a57cf072/docs/DFB_EMULATION.md?plain=1)

### NOC Address

A 64-bit encoded address used for Network-on-Chip communication. In `tt-emule`, these are resolved back to host pointers using `__emule_resolve_noc_addr`[include/jit_hw/api/dataflow/dataflow_api.h 67](https://github.com/tenstorrent/tt-emule/blob/a57cf072/include/jit_hw/api/dataflow/dataflow_api.h#L67-L67) The encoding includes X/Y coordinates and a local offset [include/jit_hw/api/dataflow/dataflow_api.h 156-157](https://github.com/tenstorrent/tt-emule/blob/a57cf072/include/jit_hw/api/dataflow/dataflow_api.h#L156-L157)

### Multicast (MCAST)

A NOC operation that writes data to a rectangular range of cores simultaneously. Emulated via `__emule_multicast_write`, which iterates through the target core range and performs local memory copies [include/jit_hw/api/dataflow/dataflow_api.h 73-74](https://github.com/tenstorrent/tt-emule/blob/a57cf072/include/jit_hw/api/dataflow/dataflow_api.h#L73-L74)

## Compute Concepts

### DST Register File

A thread-local scratchpad memory for compute operations. In emulation, it is modeled as a `float` array of 16 tiles (for bf16) or 8 tiles (for fp32) [include/jit_hw/api/compute/common.h 144-148](https://github.com/tenstorrent/tt-emule/blob/a57cf072/include/jit_hw/api/compute/common.h#L144-L148) It follows an `acquire` ->`compute` ->`commit` ->`pack` lifecycle [include/tt_emule/dst_register_file.hpp 104-107](https://github.com/tenstorrent/tt-emule/blob/a57cf072/include/tt_emule/dst_register_file.hpp#L104-L107)

### nfaces

A hardware-specific tile layout format where a 32x32 tile is divided into four 16x16 faces. `tt-emule` provides Look-Up Tables (LUTs) like `nfaces_to_rowmajor` to convert between the host's row-major layout and the device's expected format during pack/unpack [include/jit_hw/llk_pack.h 26](https://github.com/tenstorrent/tt-emule/blob/a57cf072/include/jit_hw/llk_pack.h#L26-L26)

### SFPU (Special Function Processing Unit)

The hardware unit responsible for non-linear math operations (e.g., `exp`, `log`, `sigmoid`). In `tt-emule`, these are implemented as scalar C++ loops over the `__emule_dst` array [include/jit_hw/api/compute/common.h 11-12](https://github.com/tenstorrent/tt-emule/blob/a57cf072/include/jit_hw/api/compute/common.h#L11-L12)

## System Mapping Diagram

The following diagram bridges the high-level system concepts to the specific C++ entities and files that implement them.

### Entity Relationship Diagram

**Sources:**[include/tt_emule/device.hpp 88-91](https://github.com/tenstorrent/tt-emule/blob/a57cf072/include/tt_emule/device.hpp#L88-L91)[include/tt_emule/l1_pool.hpp 141](https://github.com/tenstorrent/tt-emule/blob/a57cf072/include/tt_emule/l1_pool.hpp#L141-L141)[IMPLEMENTATION_REPORT.md 112-117](https://github.com/tenstorrent/tt-emule/blob/a57cf072/IMPLEMENTATION_REPORT.md?plain=1#L112-L117)[include/jit_hw/api/compute/common.h 148](https://github.com/tenstorrent/tt-emule/blob/a57cf072/include/jit_hw/api/compute/common.h#L148-L148)[include/tt_emule/cb_sync_state.hpp 62](https://github.com/tenstorrent/tt-emule/blob/a57cf072/include/tt_emule/cb_sync_state.hpp#L62-L62)

## Data Formats and Conversion

| Term | Definition | Implementation Pointer |
| --- | --- | --- |
| **BFP8** | Block Floating Point 8: A format with a shared 8-bit exponent per 16 elements. | [include/jit_hw/api/compute/bfp8.h](https://github.com/tenstorrent/tt-emule/blob/a57cf072/include/jit_hw/api/compute/bfp8.h) |
| **BFP4** | Block Floating Point 4: A format with a shared exponent and 4-bit mantissas. | [include/jit_hw/api/compute/bfp4.h](https://github.com/tenstorrent/tt-emule/blob/a57cf072/include/jit_hw/api/compute/bfp4.h) |
| **Tilize** | The process of converting row-major data into Tenstorrent's tiled/nfaces format. | [include/jit_hw/llk_pack.h 14](https://github.com/tenstorrent/tt-emule/blob/a57cf072/include/jit_hw/llk_pack.h#L14-L14) |
| **Untilize** | The process of converting tiled data back into row-major host format. | [include/jit_hw/llk_pack.h 96](https://github.com/tenstorrent/tt-emule/blob/a57cf072/include/jit_hw/llk_pack.h#L96-L96) |
| **Pack** | The stage where compute results in DST are converted to a specific format and written to a CB. | [include/jit_hw/llk_pack.h 2](https://github.com/tenstorrent/tt-emule/blob/a57cf072/include/jit_hw/llk_pack.h#L2-L2) |

### Data Flow: DST to Circular Buffer

This diagram illustrates the implementation of the `llk_pack` pipeline, showing how data is transformed from the internal `__emule_dst` representation to the output buffer.

**Sources:**[include/jit_hw/llk_pack.h 14-89](https://github.com/tenstorrent/tt-emule/blob/a57cf072/include/jit_hw/llk_pack.h#L14-L89)[include/jit_hw/api/compute/common.h 148](https://github.com/tenstorrent/tt-emule/blob/a57cf072/include/jit_hw/api/compute/common.h#L148-L148)

## Infrastructure and Testing

### D2M (Direct-to-Metal)

A compiler flow (part of `tt-mlir`) that generates Tenstorrent kernels directly. `tt-emule` is used to verify these kernels via `run_d2m_regression.sh`[docs/historical_reports/D2M_REGRESSION_REPORT.md](https://github.com/tenstorrent/tt-emule/blob/a57cf072/docs/historical_reports/D2M_REGRESSION_REPORT.md?plain=1)

### Pinning

The practice of locking `tt-emule` to specific versions of `tt-metal` and `tt-mlir` using `tt-metal-pin.txt` and `tt-mlir-pin.txt` to ensure CI stability [tt-metal-pin.txt 1-11](https://github.com/tenstorrent/tt-emule/blob/a57cf072/tt-metal-pin.txt#L1-L11)

### Allowlist

A file (e.g., `.github/known-failures-quasar.txt`) containing known test failures that are permitted to pass CI, typically used for features still under development [PR Metal Regression 7-8](https://github.com/tenstorrent/tt-emule/blob/a57cf072/PR%20Metal%20Regression#L7-L8)

**Sources:**

*   [include/tt_emule/device.hpp](https://github.com/tenstorrent/tt-emule/blob/a57cf072/include/tt_emule/device.hpp)
*   [include/tt_emule/l1_pool.hpp](https://github.com/tenstorrent/tt-emule/blob/a57cf072/include/tt_emule/l1_pool.hpp)
*   [include/tt_emule/circular_buffer.hpp](https://github.com/tenstorrent/tt-emule/blob/a57cf072/include/tt_emule/circular_buffer.hpp)
*   [include/tt_emule/dataflow_buffer.hpp](https://github.com/tenstorrent/tt-emule/blob/a57cf072/include/tt_emule/dataflow_buffer.hpp)
*   [include/jit_hw/api/compute/common.h](https://github.com/tenstorrent/tt-emule/blob/a57cf072/include/jit_hw/api/compute/common.h)
*   [include/jit_hw/api/dataflow/dataflow_api.h](https://github.com/tenstorrent/tt-emule/blob/a57cf072/include/jit_hw/api/dataflow/dataflow_api.h)
*   [include/jit_hw/llk_pack.h](https://github.com/tenstorrent/tt-emule/blob/a57cf072/include/jit_hw/llk_pack.h)
*   [IMPLEMENTATION_REPORT.md](https://github.com/tenstorrent/tt-emule/blob/a57cf072/IMPLEMENTATION_REPORT.md?plain=1)
*   [tt-metal-pin.txt](https://github.com/tenstorrent/tt-emule/blob/a57cf072/tt-metal-pin.txt)

Dismiss
Refresh this wiki

Enter email to refresh
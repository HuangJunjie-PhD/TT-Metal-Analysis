---
project: tt-emule
github: https://github.com/tenstorrent/tt-emule
deepwiki: https://deepwiki.com/tenstorrent/tt-emule/3-jit-kernel-api-(jit_hw-headers)
---

# JIT Kernel API (jit_hw Headers)

Relevant source files
*   [.claude/references/structure.yaml](https://github.com/tenstorrent/tt-emule/blob/a57cf072/.claude/references/structure.yaml)
*   [docs/api-injection-points.md](https://github.com/tenstorrent/tt-emule/blob/a57cf072/docs/api-injection-points.md?plain=1)
*   [docs/kernel-api-layers.md](https://github.com/tenstorrent/tt-emule/blob/a57cf072/docs/kernel-api-layers.md?plain=1)
*   [include/jit_hw/experimental/llk_pack_block_api.h](https://github.com/tenstorrent/tt-emule/blob/a57cf072/include/jit_hw/experimental/llk_pack_block_api.h)
*   [include/jit_hw/jit_kernel_stubs.hpp](https://github.com/tenstorrent/tt-emule/blob/a57cf072/include/jit_hw/jit_kernel_stubs.hpp)
*   [include/jit_hw/llk_math_binary_api.h](https://github.com/tenstorrent/tt-emule/blob/a57cf072/include/jit_hw/llk_math_binary_api.h)
*   [include/jit_hw/llk_unpack_AB_api.h](https://github.com/tenstorrent/tt-emule/blob/a57cf072/include/jit_hw/llk_unpack_AB_api.h)
*   [include/jit_hw/noc_nonblocking_api.h](https://github.com/tenstorrent/tt-emule/blob/a57cf072/include/jit_hw/noc_nonblocking_api.h)

The `include/jit_hw/` directory serves as the primary shim layer for all JIT-compiled kernels in `tt-emule`. When a kernel is compiled, this directory is placed first in the include path (`-I`), allowing `tt-emule` to intercept silicon-native headers (like `dataflow_api.h` or `compute_kernel_api.h`) and replace them with x86-compatible emulated implementations [docs/api-injection-points.md 36-41](https://github.com/tenstorrent/tt-emule/blob/a57cf072/docs/api-injection-points.md?plain=1#L36-L41)

## Injection Model & Architecture

`tt-emule` uses a multi-layer injection model to bridge the gap between silicon-native code and the x86 host. The JIT API primarily operates at **Injection Point 2**, providing header-only mocks that resolve at JIT compile time [docs/api-injection-points.md 34-41](https://github.com/tenstorrent/tt-emule/blob/a57cf072/docs/api-injection-points.md?plain=1#L34-L41)

### API Layers

Compute kernels are categorized into four layers based on their abstraction level. This taxonomy determines how `tt-emule` provides fidelity [docs/kernel-api-layers.md 22-30](https://github.com/tenstorrent/tt-emule/blob/a57cf072/docs/kernel-api-layers.md?plain=1#L22-L30):

| Layer | Name | Description | Emulation Strategy |
| --- | --- | --- | --- |
| **Layer 1** | **PoR API** | High-level tile-ops (`add_tiles`, `cb_push_back`). | Direct scalar/float math over row-major arrays [docs/kernel-api-layers.md 31-38](https://github.com/tenstorrent/tt-emule/blob/a57cf072/docs/kernel-api-layers.md?plain=1#L31-L38) |
| **Layer 1.5** | **Config Capture** | Direct PACK/UNPACK engine configuration. | Capture config into thread-local state (TLS) for later use [docs/kernel-api-layers.md 50-58](https://github.com/tenstorrent/tt-emule/blob/a57cf072/docs/kernel-api-layers.md?plain=1#L50-L58) |
| **Layer 2** | **LLK Shims** | Low-level kernel calls (`llk_math_eltwise_*`). | Thin shims matching the semantic effect on DEST/CB state [docs/kernel-api-layers.md 66-76](https://github.com/tenstorrent/tt-emule/blob/a57cf072/docs/kernel-api-layers.md?plain=1#L66-L76) |
| **Layer 3** | **HW/Instr** | Raw `sfpi::` vectors or `TTI_*` instructions. | Generally avoided; requires re-expressing at a higher layer [docs/kernel-api-layers.md 78-86](https://github.com/tenstorrent/tt-emule/blob/a57cf072/docs/kernel-api-layers.md?plain=1#L78-L86) |

**Sources:**[docs/kernel-api-layers.md 22-86](https://github.com/tenstorrent/tt-emule/blob/a57cf072/docs/kernel-api-layers.md?plain=1#L22-L86)[docs/api-injection-points.md 34-41](https://github.com/tenstorrent/tt-emule/blob/a57cf072/docs/api-injection-points.md?plain=1#L34-L41)

## Kernel Entry & Thread-Local State

Every JIT kernel is wrapped in a generated C++ file that defines the entry point and links to the host process via `extern "C"` bridge functions. The `jit_kernel_stubs.hpp` header is included at the top of every JIT unit to provide essential environment definitions [include/jit_hw/jit_kernel_stubs.hpp 6-13](https://github.com/tenstorrent/tt-emule/blob/a57cf072/include/jit_hw/jit_kernel_stubs.hpp#L6-L13)

### Host-to-JIT Bridge

The following diagram illustrates how the host process injects state into the JIT-compiled kernel threads:

**Kernel Thread State Injection**

**Sources:**[include/jit_hw/jit_kernel_stubs.hpp 98-117](https://github.com/tenstorrent/tt-emule/blob/a57cf072/include/jit_hw/jit_kernel_stubs.hpp#L98-L117)[src/kernel_runner.cpp 38-46](https://github.com/tenstorrent/tt-emule/blob/a57cf072/src/kernel_runner.cpp#L38-L46)

## API Domains

The JIT API is partitioned into several functional domains, each documented in detail on their respective child pages.

### 1. Circular Buffer (CB) & Dataflow Buffer (DFB)

The CB API provides the synchronization primitives used by Wormhole/Blackhole kernels (`cb_reserve_back`, `cb_push_back`). For Quasar, this extends to Dataflow Buffers (DFBs).

*   **Key Entity:**`CBSyncState` manages the mutex/condvar for SPSC/MPMC synchronization [include/tt_emule/cb_sync_state.hpp 62](https://github.com/tenstorrent/tt-emule/blob/a57cf072/include/tt_emule/cb_sync_state.hpp#L62-L62)
*   **Details:** See [Circular Buffer & Dataflow Buffer APIs](https://deepwiki.com/tenstorrent/tt-emule/3.1-circular-buffer-and-dataflow-buffer-apis).

### 2. NOC & Dataflow

Provides the `noc_async_read` and `noc_async_write` surface. In `tt-emule`, these are implemented as synchronous `memcpy` operations via `__emule_resolve_noc_addr`[include/jit_hw/jit_kernel_stubs.hpp 120](https://github.com/tenstorrent/tt-emule/blob/a57cf072/include/jit_hw/jit_kernel_stubs.hpp#L120-L120)

*   **Key Entity:**`Noc` class and `noc_nonblocking_api.h` shims for counter tracking [include/jit_hw/noc_nonblocking_api.h 33-37](https://github.com/tenstorrent/tt-emule/blob/a57cf072/include/jit_hw/noc_nonblocking_api.h#L33-L37)
*   **Details:** See [NOC & Dataflow API](https://deepwiki.com/tenstorrent/tt-emule/3.2-noc-and-dataflow-api).

### 3. Compute & Tile Operations

Implements the DEST register file and the math operations performed on tiles.

*   **Key Entity:**`DstRegisterFile` models the Tensix DEST as a thread-local float array [include/tt_emule/dst_register_file.hpp 107](https://github.com/tenstorrent/tt-emule/blob/a57cf072/include/tt_emule/dst_register_file.hpp#L107-L107)
*   **Details:** See [Compute API: DST Register File & Tile Operations](https://deepwiki.com/tenstorrent/tt-emule/3.3-compute-api:-dst-register-file-and-tile-operations).

### 4. SFPU Operations

Provides scalar C++ implementations for Special Function Processing Unit ops like `exp`, `log`, `sigmoid`, and `sqrt`.

*   **Details:** See [SFPU Unary & Binary Operations](https://deepwiki.com/tenstorrent/tt-emule/3.4-sfpu-unary-and-binary-operations).

### 5. Pack/Unpack Pipeline

Handles the movement of data between Circular Buffers and the DEST register file, including format conversions (e.g., BFP8, Float32) and tilization [docs/kernel-api-layers.md 50-58](https://github.com/tenstorrent/tt-emule/blob/a57cf072/docs/kernel-api-layers.md?plain=1#L50-L58)

*   **Details:** See [Pack, Unpack, Tilize & Untilize](https://deepwiki.com/tenstorrent/tt-emule/3.5-pack-unpack-tilize-and-untilize).

### 6. Experimental & Specialized APIs

Contains high-performance shims for complex operations like TopK, MoE (Mixture of Experts) gates, and RMSNorm [docs/kernel-api-layers.md 40-48](https://github.com/tenstorrent/tt-emule/blob/a57cf072/docs/kernel-api-layers.md?plain=1#L40-L48)

*   **Details:** See [Experimental & Specialized Compute APIs](https://deepwiki.com/tenstorrent/tt-emule/3.6-experimental-and-specialized-compute-apis).

## Code-to-System Mapping

The following diagram maps high-level emulation concepts to the specific code entities in the `jit_hw` layer:

**API Entity Mapping**

**Sources:**[include/tt_emule/cb_sync_state.hpp 62](https://github.com/tenstorrent/tt-emule/blob/a57cf072/include/tt_emule/cb_sync_state.hpp#L62-L62)[include/tt_emule/dfb_sync_state.hpp 101](https://github.com/tenstorrent/tt-emule/blob/a57cf072/include/tt_emule/dfb_sync_state.hpp#L101-L101)[include/jit_hw/jit_kernel_stubs.hpp 112-124](https://github.com/tenstorrent/tt-emule/blob/a57cf072/include/jit_hw/jit_kernel_stubs.hpp#L112-L124)[include/tt_emule/dst_register_file.hpp 107](https://github.com/tenstorrent/tt-emule/blob/a57cf072/include/tt_emule/dst_register_file.hpp#L107-L107)[include/jit_hw/llk_math_binary_api.h 25](https://github.com/tenstorrent/tt-emule/blob/a57cf072/include/jit_hw/llk_math_binary_api.h#L25-L25)

Dismiss
Refresh this wiki

Enter email to refresh
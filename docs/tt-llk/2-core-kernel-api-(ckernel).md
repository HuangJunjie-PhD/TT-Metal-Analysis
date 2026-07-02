---
project: tt-llk
github: https://github.com/tenstorrent/tt-llk
deepwiki: https://deepwiki.com/tenstorrent/tt-llk/2-core-kernel-api-(ckernel)
---

# Core Kernel API (ckernel)

Relevant source files
*   [tests/helpers/include/ckernel_helper.h](https://github.com/tenstorrent/tt-llk/blob/366f58e2/tests/helpers/include/ckernel_helper.h)
*   [tests/helpers/ld/memory.blackhole.ld](https://github.com/tenstorrent/tt-llk/blob/366f58e2/tests/helpers/ld/memory.blackhole.ld)
*   [tests/helpers/ld/memory.wormhole.ld](https://github.com/tenstorrent/tt-llk/blob/366f58e2/tests/helpers/ld/memory.wormhole.ld)
*   [tt_llk_blackhole/common/inc/ckernel.h](https://github.com/tenstorrent/tt-llk/blob/366f58e2/tt_llk_blackhole/common/inc/ckernel.h)
*   [tt_llk_blackhole/common/inc/ckernel_mutex_guard.h](https://github.com/tenstorrent/tt-llk/blob/366f58e2/tt_llk_blackhole/common/inc/ckernel_mutex_guard.h)
*   [tt_llk_blackhole/common/inc/ckernel_structs.h](https://github.com/tenstorrent/tt-llk/blob/366f58e2/tt_llk_blackhole/common/inc/ckernel_structs.h)
*   [tt_llk_wormhole_b0/common/inc/ckernel.h](https://github.com/tenstorrent/tt-llk/blob/366f58e2/tt_llk_wormhole_b0/common/inc/ckernel.h)
*   [tt_llk_wormhole_b0/common/inc/ckernel_instr_params.h](https://github.com/tenstorrent/tt-llk/blob/366f58e2/tt_llk_wormhole_b0/common/inc/ckernel_instr_params.h)
*   [tt_llk_wormhole_b0/common/inc/ckernel_mutex_guard.h](https://github.com/tenstorrent/tt-llk/blob/366f58e2/tt_llk_wormhole_b0/common/inc/ckernel_mutex_guard.h)
*   [tt_llk_wormhole_b0/common/inc/ckernel_structs.h](https://github.com/tenstorrent/tt-llk/blob/366f58e2/tt_llk_wormhole_b0/common/inc/ckernel_structs.h)

## Purpose and Scope

The `ckernel` namespace provides the foundational C++ interface for programming Tensix cores on Tenstorrent AI accelerators. It offers direct hardware abstraction, low-level control primitives, and synchronization mechanisms for coordinating the three-stage unpacker-math-packer pipeline. This API layer sits directly above the hardware TTI (Tensix Instruction Interface) and provides the building blocks used by higher-level LLK operations.

For higher-level operation interfaces (unpacker, math, packer operations), see pages [4](https://deepwiki.com/tenstorrent/tt-llk/4-unpacker-system), [5](https://deepwiki.com/tenstorrent/tt-llk/5-math-unit-operations), and [6](https://deepwiki.com/tenstorrent/tt-llk/6-packer-system). For micro-operation programming patterns, see [7](https://deepwiki.com/tenstorrent/tt-llk/7-micro-operation-programming-(mops)).

**Sources:**[tt_llk_wormhole_b0/common/inc/ckernel.h 1-502](https://github.com/tenstorrent/tt-llk/blob/366f58e2/tt_llk_wormhole_b0/common/inc/ckernel.h#L1-L502)[tt_llk_blackhole/common/inc/ckernel.h 1-717](https://github.com/tenstorrent/tt-llk/blob/366f58e2/tt_llk_blackhole/common/inc/ckernel.h#L1-L717)

## Namespace Overview

The `ckernel` namespace encapsulates all low-level kernel programming primitives. It is defined in architecture-specific headers:

*   **Wormhole B0:**`tt_llk_wormhole_b0/common/inc/ckernel.h`
*   **Blackhole:**`tt_llk_blackhole/common/inc/ckernel.h`
*   **Quasar:**`tt_llk_quasar/common/inc/ckernel.h`

All functions in this namespace are typically marked `inline` for performance, with critical paths using `__attribute__((always_inline))` to guarantee inlining.

**Sources:**[tt_llk_wormhole_b0/common/inc/ckernel.h 40-502](https://github.com/tenstorrent/tt-llk/blob/366f58e2/tt_llk_wormhole_b0/common/inc/ckernel.h#L40-L502)[tt_llk_blackhole/common/inc/ckernel.h 48-717](https://github.com/tenstorrent/tt-llk/blob/366f58e2/tt_llk_blackhole/common/inc/ckernel.h#L48-L717)

## Global Hardware Pointers

The `ckernel` namespace exposes several memory-mapped pointers that provide direct access to Tensix hardware resources:

### Key Pointers

| Pointer | Type | Base Address | Purpose |
| --- | --- | --- | --- |
| `reg_base` | `volatile uint32_t*` | `TENSIX_CFG_BASE` | MMIO register access |
| `pc_buf_base` | `volatile uint32_t*` | `PC_BUF_BASE` | PC buffer for synchronization |
| `regfile` | `volatile uint32_t*` | `REGFILE_BASE` | GPR register file access |
| `mailbox_base[4]` | `volatile uint32_t*` | `MAILBOX0-3` | Inter-thread communication |
| `instrn_buffer` | `volatile uint32_t*` | N/A | Instruction buffer array |
| `debug_mailbox_base` | `volatile uint16_t*` | N/A | Debug event recording |

These pointers are initialized at kernel startup and remain valid throughout execution. They provide the foundation for all hardware interaction.

**Sources:**[tt_llk_wormhole_b0/common/inc/ckernel.h 57-76](https://github.com/tenstorrent/tt-llk/blob/366f58e2/tt_llk_wormhole_b0/common/inc/ckernel.h#L57-L76)[tt_llk_blackhole/common/inc/ckernel.h 65-84](https://github.com/tenstorrent/tt-llk/blob/366f58e2/tt_llk_blackhole/common/inc/ckernel.h#L65-L84)[tests/helpers/include/ckernel_helper.h 14-24](https://github.com/tenstorrent/tt-llk/blob/366f58e2/tests/helpers/include/ckernel_helper.h#L14-L24)

## Configuration State Management

The `ckernel` namespace maintains two critical global state variables for ping-pong buffering:

### State Variables

| Variable | Type | Values | Purpose |
| --- | --- | --- | --- |
| `cfg_state_id` | `uint32_t` | 0 or 1 | Configuration context selector |
| `dest_offset_id` | `uint32_t` | 0 or 1 | Destination register bank selector |

These variables enable overlapped execution by maintaining separate configuration contexts and destination register banks. Hardware can execute operations on one context while software configures the other.

### State Switching Functions

*   **`flip_cfg_state_id()`**: Toggles `cfg_state_id` between 0 and 1, updates hardware register
*   **`update_dest_offset_id()`**: Toggles `dest_offset_id` between 0 and 1
*   **`reset_cfg_state_id()`**: Resets `cfg_state_id` to 0
*   **`reset_dest_offset_id()`**: Resets `dest_offset_id` to 0
*   **`cfg_addr(cfg_addr32)`**: Translates register address based on current `cfg_state_id`
*   **`get_dest_buffer_base()`**: Returns destination register offset based on `dest_offset_id`

**Sources:**[tt_llk_wormhole_b0/common/inc/ckernel.h 69-256](https://github.com/tenstorrent/tt-llk/blob/366f58e2/tt_llk_wormhole_b0/common/inc/ckernel.h#L69-L256)[tt_llk_blackhole/common/inc/ckernel.h 77-264](https://github.com/tenstorrent/tt-llk/blob/366f58e2/tt_llk_blackhole/common/inc/ckernel.h#L77-L264)

## Core Primitive Categories

The `ckernel` namespace provides primitives organized into functional categories:

### Synchronization Primitives

| Function | Purpose | Blocking Behavior |
| --- | --- | --- |
| `tensix_sync()` | Wait until Tensix pipeline is idle | Blocks until all operations complete |
| `mop_sync()` | Wait until MOP operations complete | Blocks until MOP queue drains |
| `semaphore_post(idx)` | Release software semaphore | Non-blocking |
| `semaphore_get(idx)` | Acquire software semaphore | Non-blocking (sets flag) |
| `semaphore_read(idx)` | Read semaphore state | Non-blocking |

### Thread Semaphores (T6)

Template-based functions for hardware semaphore coordination with optional stall conditions:

*   **`t6_semaphore_init(idx, min, max)`**: Initialize semaphore bounds
*   **`t6_semaphore_post<WaitRes>(idx)`**: Post semaphore, optionally stall on resource
*   **`t6_semaphore_get<WaitRes>(idx)`**: Get semaphore, optionally stall on resource
*   **`t6_semaphore_wait_on_max<WaitRes>(idx)`**: Stall until semaphore reaches max
*   **`t6_semaphore_wait_on_zero<WaitRes>(idx)`**: Stall until semaphore reaches zero
*   **`t6_mutex_acquire(idx)`**: Atomic mutex acquisition
*   **`t6_mutex_release(idx)`**: Atomic mutex release

The `WaitRes` template parameter specifies what resource to wait on (e.g., `p_stall::UNPACK`, `p_stall::PACK`, `p_stall::MATH`).

**Sources:**[tt_llk_wormhole_b0/common/inc/ckernel.h 83-186](https://github.com/tenstorrent/tt-llk/blob/366f58e2/tt_llk_wormhole_b0/common/inc/ckernel.h#L83-L186)[tt_llk_blackhole/common/inc/ckernel.h 91-194](https://github.com/tenstorrent/tt-llk/blob/366f58e2/tt_llk_blackhole/common/inc/ckernel.h#L91-L194)

### Configuration Register Access

| Function | Purpose | State-Aware |
| --- | --- | --- |
| `cfg_write(addr, data)` | Write configuration register | Yes (uses `cfg_state_id`) |
| `cfg_read(addr)` | Read configuration register | Yes (uses `cfg_state_id`) |
| `cfg_rmw(addr, shamt, mask, val)` | Read-modify-write configuration register | Yes |
| `cfg_reg_rmw_tensix<Addr, Shamt, Mask>(val)` | Template RMW using byte-wise TTI instructions | Yes |
| `get_cfg_pointer()` | Get pointer to current config context | Yes |
| `get_cfg16_pointer()` | Get 16-bit pointer to current config context | Yes |

All configuration access functions automatically account for the current `cfg_state_id`, ensuring writes target the correct configuration context.

**Sources:**[tt_llk_wormhole_b0/common/inc/ckernel.h 189-385](https://github.com/tenstorrent/tt-llk/blob/366f58e2/tt_llk_wormhole_b0/common/inc/ckernel.h#L189-L385)[tt_llk_blackhole/common/inc/ckernel.h 197-393](https://github.com/tenstorrent/tt-llk/blob/366f58e2/tt_llk_blackhole/common/inc/ckernel.h#L197-L393)

### Mailbox Operations

| Function | Purpose | Blocking |
| --- | --- | --- |
| `mailbox_write(thread, data)` | Write to thread mailbox | Non-blocking |
| `mailbox_read(thread)` | Read from thread mailbox | Blocking |
| `mailbox_not_empty(thread)` | Check if mailbox has data | Non-blocking |

Mailboxes enable communication between the three TRISC threads (UNPACK, MATH, PACK) running on a Tensix core.

**Sources:**[tt_llk_wormhole_b0/common/inc/ckernel.h 387-401](https://github.com/tenstorrent/tt-llk/blob/366f58e2/tt_llk_wormhole_b0/common/inc/ckernel.h#L387-L401)[tt_llk_blackhole/common/inc/ckernel.h 395-409](https://github.com/tenstorrent/tt-llk/blob/366f58e2/tt_llk_blackhole/common/inc/ckernel.h#L395-L409)

### Utility Operations

| Function | Purpose |
| --- | --- |
| `zeroacc()` | Clear destination accumulator registers |
| `zerosrc()` | Clear source A and B registers |
| `wait(cycles)` | Busy-wait for specified number of cycles |
| `read_wall_clock()` | Read 64-bit wall clock timestamp |
| `init_prng_seed(seed)` | Initialize PRNG for SFPU operations |

**Sources:**[tt_llk_wormhole_b0/common/inc/ckernel.h 268-464](https://github.com/tenstorrent/tt-llk/blob/366f58e2/tt_llk_wormhole_b0/common/inc/ckernel.h#L268-L464)[tt_llk_blackhole/common/inc/ckernel.h 276-672](https://github.com/tenstorrent/tt-llk/blob/366f58e2/tt_llk_blackhole/common/inc/ckernel.h#L276-L672)

### Debug and Profiling Support

| Function | Purpose |
| --- | --- |
| `record_mailbox_value(value)` | Record debug event to scratch mailbox |
| `record_mailbox_value_with_index(idx, value)` | Record event at specific index |
| `clear_mailbox_values(value)` | Initialize debug mailbox |
| `record_kernel_runtime(runtime)` | Record 64-bit kernel runtime |

The debug mailbox provides a scratch space for recording kernel events and performance data without interfering with normal operation.

**Sources:**[tt_llk_wormhole_b0/common/inc/ckernel.h 409-448](https://github.com/tenstorrent/tt-llk/blob/366f58e2/tt_llk_wormhole_b0/common/inc/ckernel.h#L409-L448)[tt_llk_blackhole/common/inc/ckernel.h 417-456](https://github.com/tenstorrent/tt-llk/blob/366f58e2/tt_llk_blackhole/common/inc/ckernel.h#L417-L456)

## TTI Instruction Interface

Configuration register access uses TTI (Tensix Instruction Interface) instructions, exposed through macros and inline assembly:

### Common TTI Instructions

| Instruction | Purpose | Defined In |
| --- | --- | --- |
| `TTI_STALLWAIT(stall, waitres)` | Stall execution until resource ready | `ckernel_ops.h` |
| `TTI_SEMPOST(sem)` | Post to hardware semaphore | `ckernel_ops.h` |
| `TTI_SEMGET(sem)` | Get from hardware semaphore | `ckernel_ops.h` |
| `TTI_SEMWAIT(waitres, sem, mode)` | Wait on semaphore condition | `ckernel_ops.h` |
| `TTI_SEMINIT(max, min, sem)` | Initialize semaphore | `ckernel_ops.h` |
| `TTI_ATGETM(idx)` | Atomic mutex get | `ckernel_ops.h` |
| `TTI_ATRELM(idx)` | Atomic mutex release | `ckernel_ops.h` |
| `TT_SETC16(addr, val)` | Set 16-bit configuration value | `ckernel_common_ops.h` |
| `TT_RMWCIB0-3(mask, data, addr)` | Read-modify-write byte 0-3 | `ckernel_common_ops.h` |
| `TT_ZEROACC(mode, addr_mod, ...)` | Clear destination accumulator | `ckernel_common_ops.h` |
| `TTI_ZEROSRC(...)` | Clear source registers | `ckernel_ops.h` |
| `TTI_MOP(type, count, zmask)` | Execute MOP sequence | `ckernel_ops.h` |

These instructions compile directly to RISC-V custom instructions that control Tensix hardware.

**Sources:**[tt_llk_wormhole_b0/common/inc/ckernel.h 7-12](https://github.com/tenstorrent/tt-llk/blob/366f58e2/tt_llk_wormhole_b0/common/inc/ckernel.h#L7-L12)

## Architecture-Specific Differences

While the core `ckernel` API is consistent across architectures, Blackhole introduces several hardware enhancements reflected in additional API functions:

### Blackhole-Specific Features

#### TTSYNC Automatic Synchronization

Blackhole hardware can automatically track dependencies between TRISC memory accesses and Tensix instructions, eliminating many manual synchronization calls:

When enabled, hardware automatically stalls conflicting operations, reducing need for explicit `tensix_sync()` or `sync_regfile_write()` calls.

**Sources:**[tt_llk_blackhole/common/inc/ckernel.h 461-489](https://github.com/tenstorrent/tt-llk/blob/366f58e2/tt_llk_blackhole/common/inc/ckernel.h#L461-L489)

#### Instruction Gathering and Replay Buffers

Blackhole supports recording instruction sequences into replay buffers for efficient repeated execution:

These features use the `lltt` (Low-Level Tensix Tools) library for replay buffer management. See [7.3](https://deepwiki.com/tenstorrent/tt-llk/7.3-replay-buffers-and-instruction-recording) for detailed replay buffer programming patterns.

**Sources:**[tt_llk_blackhole/common/inc/ckernel.h 491-549](https://github.com/tenstorrent/tt-llk/blob/366f58e2/tt_llk_blackhole/common/inc/ckernel.h#L491-L549)

#### CSR Monitoring

Blackhole exposes additional status monitoring through CSR (Control and Status Register) reads:

These CSRs provide visibility into hardware pipeline state for advanced optimization and debugging.

**Sources:**[tt_llk_blackhole/common/inc/ckernel.h 551-659](https://github.com/tenstorrent/tt-llk/blob/366f58e2/tt_llk_blackhole/common/inc/ckernel.h#L551-L659)

#### Sign-Magnitude Conversion

Blackhole provides specialized format conversion for SFPU operations:

This function wraps `TTI_SFPCAST` with a required `TTI_SFPSETSGN` workaround for a Blackhole RTL bug.

**Sources:**[tt_llk_blackhole/common/inc/ckernel.h 679-684](https://github.com/tenstorrent/tt-llk/blob/366f58e2/tt_llk_blackhole/common/inc/ckernel.h#L679-L684)

## Constants and Configuration

The `ckernel` namespace defines several important constants:

### Hardware Limits

### Destination Register Capacity

The template function `get_dest_max_tiles<>` calculates destination register capacity:

This function accounts for register size reduction in 32-bit accumulation mode and different tile shapes.

**Sources:**[tt_llk_wormhole_b0/common/inc/ckernel.h 49-499](https://github.com/tenstorrent/tt-llk/blob/366f58e2/tt_llk_wormhole_b0/common/inc/ckernel.h#L49-L499)[tt_llk_blackhole/common/inc/ckernel.h 57-714](https://github.com/tenstorrent/tt-llk/blob/366f58e2/tt_llk_blackhole/common/inc/ckernel.h#L57-L714)

## Instruction Parameter Definitions

The `ckernel_instr_params.h` header defines parameter constants for TTI instructions, organized into `p_*` structures:

### Key Parameter Structures

| Structure | Purpose | Example Constants |
| --- | --- | --- |
| `p_stall` | Stall and wait conditions | `UNPACK`, `PACK`, `MATH`, `SRCA_VLD` |
| `p_setrwc` | Read/write counter control | `SET_AB`, `CLR_ALL`, `CR_ABD` |
| `p_unpacr` | Unpacker configuration | `RAREFYB_ENABLE`, `AUTO_INC_CONTEXT` |
| `p_setadc` | Address counter setup | `UNP_A`, `UNP_B`, `PAC` |
| `p_zeroacc` | Destination clear modes | `CLR_ALL`, `CLR_HALF`, `CLR_16` |
| `p_zerosrc` | Source clear modes | `CLR_A`, `CLR_B`, `CLR_AB` |
| `p_elwise` | Element-wise operation params | `SRCB_BCAST_COL`, `DEST_ACCUM_EN` |
| `p_sfpu` | SFPU register and constant IDs | `LREG0-7`, `LCONST_0`, `LCONST_1` |

These constants provide type-safe, named parameters for low-level TTI instructions used throughout the LLK codebase.

**Sources:**[tt_llk_wormhole_b0/common/inc/ckernel_instr_params.h 1-422](https://github.com/tenstorrent/tt-llk/blob/366f58e2/tt_llk_wormhole_b0/common/inc/ckernel_instr_params.h#L1-L422)

## Usage Patterns

### Basic Initialization Pattern

### Configuration Ping-Pong Pattern

### Synchronization Pattern

### Mailbox Communication Pattern

**Sources:**[tt_llk_wormhole_b0/common/inc/ckernel.h 229-251](https://github.com/tenstorrent/tt-llk/blob/366f58e2/tt_llk_wormhole_b0/common/inc/ckernel.h#L229-L251)[tt_llk_blackhole/common/inc/ckernel.h 237-259](https://github.com/tenstorrent/tt-llk/blob/366f58e2/tt_llk_blackhole/common/inc/ckernel.h#L237-L259)

## Summary

The `ckernel` namespace provides the essential primitives for Tensix core programming:

*   **Hardware Abstraction**: Direct memory-mapped access to configuration registers, PC buffer, register files, and mailboxes
*   **State Management**: Dual-context configuration and destination register ping-pong buffering
*   **Synchronization**: Blocking and non-blocking primitives for pipeline coordination
*   **Communication**: Inter-thread mailbox operations
*   **Debugging**: Event recording and performance measurement infrastructure
*   **Architecture Portability**: Common API with architecture-specific extensions

All higher-level LLK operations (unpacker, math, packer) build upon these primitives. Understanding `ckernel` is essential for implementing new operations or optimizing kernel performance.

**Sources:**[tt_llk_wormhole_b0/common/inc/ckernel.h 1-502](https://github.com/tenstorrent/tt-llk/blob/366f58e2/tt_llk_wormhole_b0/common/inc/ckernel.h#L1-L502)[tt_llk_blackhole/common/inc/ckernel.h 1-717](https://github.com/tenstorrent/tt-llk/blob/366f58e2/tt_llk_blackhole/common/inc/ckernel.h#L1-L717)[tests/helpers/include/ckernel_helper.h 1-25](https://github.com/tenstorrent/tt-llk/blob/366f58e2/tests/helpers/include/ckernel_helper.h#L1-L25)

Dismiss
Refresh this wiki

Enter email to refresh
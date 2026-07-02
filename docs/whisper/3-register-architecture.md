---
project: whisper
github: https://github.com/tenstorrent/whisper
deepwiki: https://deepwiki.com/tenstorrent/whisper/3-register-architecture
---

# Register Architecture

Relevant source files
*   [CsRegs.cpp](https://github.com/tenstorrent/whisper/blob/733fd921/CsRegs.cpp)
*   [CsRegs.hpp](https://github.com/tenstorrent/whisper/blob/733fd921/CsRegs.hpp)
*   [CsrFields.hpp](https://github.com/tenstorrent/whisper/blob/733fd921/CsrFields.hpp)
*   [Hart.cpp](https://github.com/tenstorrent/whisper/blob/733fd921/Hart.cpp)
*   [Hart.hpp](https://github.com/tenstorrent/whisper/blob/733fd921/Hart.hpp)
*   [VecRegs.cpp](https://github.com/tenstorrent/whisper/blob/733fd921/VecRegs.cpp)
*   [VecRegs.hpp](https://github.com/tenstorrent/whisper/blob/733fd921/VecRegs.hpp)
*   [vector.cpp](https://github.com/tenstorrent/whisper/blob/733fd921/vector.cpp)

## Purpose and Scope

This page provides an overview of the register file organization in Whisper's RISC-V hart implementation. It covers the four primary register file types (integer, floating-point, vector, and control/status registers) and their integration within the `Hart` execution unit. For detailed documentation on Control and Status Registers, see [Control and Status Registers (CSRs)](https://deepwiki.com/tenstorrent/whisper/3.1-control-and-status-registers-(csrs)). For vector register specifics, see [Vector Register File](https://deepwiki.com/tenstorrent/whisper/3.2-vector-register-file). For performance counter implementation, see [Performance Counters](https://deepwiki.com/tenstorrent/whisper/3.3-performance-counters).

**Sources:**[Hart.hpp 88-99](https://github.com/tenstorrent/whisper/blob/733fd921/Hart.hpp#L88-L99)[Hart.cpp 88-115](https://github.com/tenstorrent/whisper/blob/733fd921/Hart.cpp#L88-L115)

* * *

## Register File Organization

Each `Hart` instance contains four distinct register files, initialized during hart construction and managed throughout instruction execution:

**Sources:**[Hart.hpp 88-99](https://github.com/tenstorrent/whisper/blob/733fd921/Hart.hpp#L88-L99)[Hart.cpp 88-115](https://github.com/tenstorrent/whisper/blob/733fd921/Hart.cpp#L88-L115)

* * *

## Integer Registers

The integer register file is implemented by the `IntRegs` class and contains 32 general-purpose registers (x0-x31 or, with the E extension, x0-x15). Register x0 is hardwired to zero. The template parameter `URV` determines register width: `uint32_t` for RV32, `uint64_t` for RV64.

| Method | Description |
| --- | --- |
| `peekIntReg(unsigned reg, URV& val)` | Read register value |
| `pokeIntReg(unsigned reg, URV val)` | Write register value |
| `intRegName(unsigned regIx)` | Get register name (ABI or numeric) |

The integer register file is tied to the hart during construction and accessed during instruction decode and execute phases. Register x0 writes are silently discarded.

**Sources:**[Hart.hpp 168-226](https://github.com/tenstorrent/whisper/blob/733fd921/Hart.hpp#L168-L226)[Hart.cpp 91](https://github.com/tenstorrent/whisper/blob/733fd921/Hart.cpp#L91-L91)

* * *

## Floating-Point Registers

The `FpRegs` class implements 32 floating-point registers (f0-f31) storing 64-bit values. These registers support F (single-precision), D (double-precision), and Zfh (half-precision) extensions. Values are stored in their native format or NaN-boxed for narrower types.

| Method | Description |
| --- | --- |
| `peekFpReg(unsigned reg, uint64_t& val)` | Read raw register bits |
| `peekUnboxedFpReg(unsigned reg, uint64_t& val)` | Read with NaN unboxing |
| `pokeFpReg(unsigned reg, uint64_t val)` | Write register value |
| `fpRegName(unsigned regIx)` | Get register name (ABI or numeric) |

Floating-point status is tracked via the `FCSR` CSR, which includes rounding mode (`FRM`) and exception flags (`FFLAGS`). The `FS` field in `MSTATUS` tracks the dirty state of the FP register file.

**Sources:**[Hart.hpp 186-242](https://github.com/tenstorrent/whisper/blob/733fd921/Hart.hpp#L186-L242)[Hart.cpp 93](https://github.com/tenstorrent/whisper/blob/733fd921/Hart.cpp#L93-L93)

* * *

## Vector Registers

The vector register file is implemented by the `VecRegs` class and contains 32 architectural vector registers (v0-v31). Each register's physical width is configurable (VLEN), ranging from 128 to 4096 bits. Vector operations are parameterized by:

*   **SEW** (Selected Element Width): 8, 16, 32, 64, 128, 256, 512, or 1024 bits
*   **LMUL** (Group Multiplier): 1/8, 1/4, 1/2, 1, 2, 4, or 8
*   **VL** (Vector Length): Number of elements to process

**Configuration:** Vector register configuration is validated against legal SEW/LMUL combinations stored in `legalConfigs_`. The `config()` method establishes register count, bytes per register, and allowable element widths.

For detailed vector register organization and element access patterns, see [Vector Register File](https://deepwiki.com/tenstorrent/whisper/3.2-vector-register-file).

**Sources:**[VecRegs.hpp 124-162](https://github.com/tenstorrent/whisper/blob/733fd921/VecRegs.hpp#L124-L162)[VecRegs.cpp 25-48](https://github.com/tenstorrent/whisper/blob/733fd921/VecRegs.cpp#L25-L48)[Hart.cpp 92](https://github.com/tenstorrent/whisper/blob/733fd921/Hart.cpp#L92-L92)

* * *

## Control and Status Registers (CSRs)

CSRs are managed by the `CsRegs` class, which maintains up to 4096 CSR entries indexed by `CsrNumber`. Each CSR is represented by a `Csr<URV>` object containing:

*   Current value
*   Reset value
*   Write mask (bits modifiable by CSR instructions)
*   Poke mask (bits modifiable by hardware/external poke)
*   Read mask (bits visible on read)
*   Privilege mode requirement
*   Implementation status

### CSR Number Space

CSRs are addressed using a 12-bit number (0x000-0xFFF). The upper two bits encode privilege level requirements:

| Bits [11:10] | Privilege Mode | Address Range |
| --- | --- | --- |
| 00 | User | 0x000-0x3FF |
| 01 | Supervisor | 0x400-0x7FF |
| 10 | Reserved | 0x800-0xBFF |
| 11 | Machine | 0xC00-0xFFF |

Bits [9:8] encode read/write permissions:

| Bits [9:8] | Access |
| --- | --- |
| 00, 01, 10 | Read-Write |
| 11 | Read-Only |

### CSR Access Methods

| Method | Description |
| --- | --- |
| `read(CsrNumber num, PrivilegeMode mode, URV& value)` | Read CSR with privilege check |
| `write(CsrNumber num, PrivilegeMode mode, URV value)` | Write CSR with privilege check and mask |
| `peek(CsrNumber csr, URV& val)` | Read CSR without privilege check |
| `poke(CsrNumber csr, URV val)` | Write CSR with poke mask |
| `isReadable(CsrNumber csr, PrivilegeMode mode)` | Check read accessibility |
| `isWriteable(CsrNumber csr, PrivilegeMode mode)` | Check write accessibility |

### Tied CSRs

Certain CSRs are "tied" to hart member variables for performance, avoiding indirect access through the CSR array:

This optimization allows direct manipulation of performance counters during instruction execution without CSR array lookups.

For comprehensive CSR documentation including field definitions, delegation mechanisms, and interrupt handling, see [Control and Status Registers (CSRs)](https://deepwiki.com/tenstorrent/whisper/3.1-control-and-status-registers-(csrs)).

**Sources:**[CsRegs.hpp 33-127](https://github.com/tenstorrent/whisper/blob/733fd921/CsRegs.hpp#L33-L127)[CsRegs.cpp 32-49](https://github.com/tenstorrent/whisper/blob/733fd921/CsRegs.cpp#L32-L49)[Hart.cpp 250-308](https://github.com/tenstorrent/whisper/blob/733fd921/Hart.cpp#L250-L308)

* * *

## Register Access During Instruction Execution

Register access follows a consistent pattern during the instruction execution pipeline:

**Sources:**[Hart.cpp](https://github.com/tenstorrent/whisper/blob/733fd921/Hart.cpp#LNaN-LNaN)

* * *

## Register State Persistence

Register values are maintained across instruction execution. Key mechanisms include:

1.   **Reset:**`Hart::reset()` clears integer and vector register files, resets CSRs to initial values
2.   **Context Switching:** CSRs like `MSTATUS`, `MEPC`, `MCAUSE` preserve hart state during traps
3.   **Tied Variables:** Frequently-accessed CSRs (counters, status) bypass the CSR array via pointers
4.   **Change Tracking:** Vector register writes are tracked in `lastWrittenReg_` for instruction tracing

**Sources:**[Hart.cpp 757-860](https://github.com/tenstorrent/whisper/blob/733fd921/Hart.cpp#L757-L860)[VecRegs.cpp 198-204](https://github.com/tenstorrent/whisper/blob/733fd921/VecRegs.cpp#L198-L204)

Dismiss
Refresh this wiki

Enter email to refresh
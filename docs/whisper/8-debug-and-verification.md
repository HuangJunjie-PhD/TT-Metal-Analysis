---
project: whisper
github: https://github.com/tenstorrent/whisper
deepwiki: https://deepwiki.com/tenstorrent/whisper/8-debug-and-verification
---

# Debug and Verification

Relevant source files
*   [Mcm.cpp](https://github.com/tenstorrent/whisper/blob/733fd921/Mcm.cpp)
*   [Mcm.hpp](https://github.com/tenstorrent/whisper/blob/733fd921/Mcm.hpp)
*   [Memory.cpp](https://github.com/tenstorrent/whisper/blob/733fd921/Memory.cpp)
*   [Memory.hpp](https://github.com/tenstorrent/whisper/blob/733fd921/Memory.hpp)
*   [PmaManager.cpp](https://github.com/tenstorrent/whisper/blob/733fd921/PmaManager.cpp)
*   [PmaManager.hpp](https://github.com/tenstorrent/whisper/blob/733fd921/PmaManager.hpp)
*   [README.md](https://github.com/tenstorrent/whisper/blob/733fd921/README.md?plain=1)
*   [Triggers.cpp](https://github.com/tenstorrent/whisper/blob/733fd921/Triggers.cpp)
*   [Triggers.hpp](https://github.com/tenstorrent/whisper/blob/733fd921/Triggers.hpp)
*   [printTrace.cpp](https://github.com/tenstorrent/whisper/blob/733fd921/printTrace.cpp)

This page provides an overview of Whisper's debug and verification capabilities. These features enable developers to trace program execution, set breakpoints, and verify memory ordering semantics for both functional debugging and lock-step verification against hardware implementations.

For detailed information about specific subsystems:

*   Debug trigger configuration and breakpoint mechanisms: see [Debug Triggers and Breakpoints](https://deepwiki.com/tenstorrent/whisper/8.1-debug-triggers-and-breakpoints)
*   Instruction execution trace logging: see [Instruction Tracing](https://deepwiki.com/tenstorrent/whisper/8.2-instruction-tracing)
*   Memory consistency model validation: see [Memory Ordering Verification](https://deepwiki.com/tenstorrent/whisper/8.3-memory-ordering-verification)

## Verification Architecture Overview

Whisper provides three complementary verification and debugging subsystems that operate at different abstraction levels:

**Sources:**[Triggers.hpp 301-480](https://github.com/tenstorrent/whisper/blob/733fd921/Triggers.hpp#L301-L480)[printTrace.cpp 48-506](https://github.com/tenstorrent/whisper/blob/733fd921/printTrace.cpp#L48-L506)[Mcm.hpp 210-495](https://github.com/tenstorrent/whisper/blob/733fd921/Mcm.hpp#L210-L495)[Mcm.cpp 11-2744](https://github.com/tenstorrent/whisper/blob/733fd921/Mcm.cpp#L11-L2744)

## Debug Triggers

Debug triggers provide hardware-assisted breakpoint and watchpoint functionality. The `Triggers` class manages multiple trigger instances that can monitor instruction execution, memory accesses, and exception events.

### Trigger Types and Components

**Sources:**[Triggers.hpp 301-480](https://github.com/tenstorrent/whisper/blob/733fd921/Triggers.hpp#L301-L480)[Triggers.hpp 44-221](https://github.com/tenstorrent/whisper/blob/733fd921/Triggers.hpp#L44-L221)[Triggers.cpp 25-69](https://github.com/tenstorrent/whisper/blob/733fd921/Triggers.cpp#L25-L69)

### Trigger Matching and Actions

The trigger subsystem performs match checks at different pipeline stages:

| Match Function | Timing | Description |
| --- | --- | --- |
| `instAddrTriggerHit()` | Before/After | Matches instruction address with configurable size |
| `instOpcodeTriggerHit()` | Before/After | Matches instruction opcode value |
| `ldStAddrTriggerHit()` | Before/After | Matches load/store address with size |
| `ldStDataTriggerHit()` | Before/After | Matches load/store data value |
| `icountTriggerFired()` | After | Fires after instruction count reaches zero |
| `expTriggerHit()` | N/A | Matches exception cause number |

Available trigger actions defined by `TriggerAction` enumeration:

*   `RaiseBreak`: Raise breakpoint exception
*   `EnterDebug`: Enter debug mode
*   `StartTrace`: Begin trace logging
*   `StopTrace`: Stop trace logging
*   `EmitTrace`: Emit trace packet
*   `External0/External1`: External signal actions

**Sources:**[Triggers.hpp 28-39](https://github.com/tenstorrent/whisper/blob/733fd921/Triggers.hpp#L28-L39)[Triggers.cpp 281-477](https://github.com/tenstorrent/whisper/blob/733fd921/Triggers.cpp#L281-L477)

## Instruction Tracing

Instruction tracing records execution history including program counter, opcode, register modifications, memory accesses, and CSR changes. Traces can be output in human-readable or CSV format for post-processing.

### Trace Data Flow

**Sources:**[printTrace.cpp 269-506](https://github.com/tenstorrent/whisper/blob/733fd921/printTrace.cpp#L269-L506)[printTrace.cpp 589-830](https://github.com/tenstorrent/whisper/blob/733fd921/printTrace.cpp#L589-L830)

### Trace Record Format

Standard trace format (human-readable):

```
#<tag> <hart> <mode> <pc> <opcode> <resource> <addr> <value> <disassembly>
```

Where:

*   `tag`: Instruction sequence number
*   `hart`: Hart index
*   `mode`: Privilege mode (M/S/U/HS/VS/VU/D)
*   `pc`: Program counter
*   `opcode`: Instruction encoding
*   `resource`: Resource type (r=int reg, f=fp reg, v=vec reg, c=CSR, m=memory)
*   `addr`: Resource address/number
*   `value`: New value
*   `disassembly`: Instruction assembly

Multiple resource changes are concatenated with `+` line continuation.

CSV format headers:

```
pc, inst, modified regs, source operands, memory, inst info, privilege, trap, disassembly, hartid
```

**Sources:**[printTrace.cpp 101-209](https://github.com/tenstorrent/whisper/blob/733fd921/printTrace.cpp#L101-L209)[printTrace.cpp 589-830](https://github.com/tenstorrent/whisper/blob/733fd921/printTrace.cpp#L589-L830)

### Page Table Walk Tracing

When `tracePtw_` is enabled, the trace includes detailed page table walk information for both instruction fetch and data accesses:

The trace output format for page table walks:

```
iptw:  // Instruction fetch page table walk
  +
  gva: 0x<addr>
  +
  gpa: 0x<addr>
  +
   pa: 0x<addr>=0x<pte>, ma=<attributes>
```

**Sources:**[printTrace.cpp 212-266](https://github.com/tenstorrent/whisper/blob/733fd921/printTrace.cpp#L212-L266)[printTrace.cpp 484-504](https://github.com/tenstorrent/whisper/blob/733fd921/printTrace.cpp#L484-L504)

## Memory Ordering Verification

The Memory Consistency Model (MCM) subsystem verifies that out-of-order memory operations preserve program semantics according to RISC-V Weak Memory Ordering (RVWMO) or Total Store Order (TSO) rules.

### MCM Core Data Structures

**Sources:**[Mcm.hpp 23-208](https://github.com/tenstorrent/whisper/blob/733fd921/Mcm.hpp#L23-L208)[Mcm.hpp 210-495](https://github.com/tenstorrent/whisper/blob/733fd921/Mcm.hpp#L210-L495)[Mcm.cpp 11-51](https://github.com/tenstorrent/whisper/blob/733fd921/Mcm.cpp#L11-L51)

### PPO Rule Verification

The MCM enforces Preserved Program Order (PPO) rules defined by the RISC-V memory model specification:

| Rule | Description | Checker Function |
| --- | --- | --- |
| R1 | Store-store ordering, overlapping addresses | `ppoRule1()` |
| R2 | Load/AMO to load/store/AMO, overlapping addresses, same hart | `ppoRule2()` |
| R3 | Store to load/store/AMO, overlapping addresses | `ppoRule3()` |
| R4 | Fence ordering constraints | `ppoRule4()` |
| R5 | Address dependency ordering | `ppoRule5()` |
| R6 | Data dependency ordering | `ppoRule6()` |
| R7 | Control dependency ordering | `ppoRule7()` |
| R8 | Syntactic dependency through address | `ppoRule8()` |
| R9 | Syntactic dependency through data | `ppoRule9()` |
| R10 | Acquire RCpc annotation ordering | `ppoRule10()` |
| R11 | Release RCpc annotation ordering | `ppoRule11()` |
| R12 | Acquire RCsc annotation ordering | `ppoRule12()` |
| R13 | Release RCsc annotation ordering | `ppoRule13()` |

**Sources:**[Mcm.hpp 344-382](https://github.com/tenstorrent/whisper/blob/733fd921/Mcm.hpp#L344-L382)[Mcm.cpp 1565-2744](https://github.com/tenstorrent/whisper/blob/733fd921/Mcm.cpp#L1565-L2744)

### Memory Operation Lifecycle

**Sources:**[Mcm.cpp 115-261](https://github.com/tenstorrent/whisper/blob/733fd921/Mcm.cpp#L115-L261)[Mcm.cpp 929-1049](https://github.com/tenstorrent/whisper/blob/733fd921/Mcm.cpp#L929-L1049)[Mcm.cpp 1234-1307](https://github.com/tenstorrent/whisper/blob/733fd921/Mcm.cpp#L1234-L1307)[Mcm.cpp 2947-3207](https://github.com/tenstorrent/whisper/blob/733fd921/Mcm.cpp#L2947-L3207)

### Store-to-Load Forwarding

The MCM tracks register production times and instruction dependencies to validate store-to-load forwarding:

**Sources:**[Mcm.cpp 545-663](https://github.com/tenstorrent/whisper/blob/733fd921/Mcm.cpp#L545-L663)[Mcm.cpp 3532-3577](https://github.com/tenstorrent/whisper/blob/733fd921/Mcm.cpp#L3532-L3577)[Mcm.cpp 3610-3695](https://github.com/tenstorrent/whisper/blob/733fd921/Mcm.cpp#L3610-L3695)

### TSO vs RVWMO Mode

Whisper supports two memory ordering models:

**RVWMO (RISC-V Weak Memory Ordering):**

*   Relaxed memory ordering
*   Full PPO rule checking (R1-R13)
*   Out-of-order loads and stores allowed
*   Store buffer not guaranteed to drain in order

**TSO (Total Store Order):**

*   Stronger ordering guarantees
*   All loads appear to execute in program order
*   All stores appear to execute in program order
*   Simplified rule checking

Mode selection: `Mcm::enableTso(bool flag)`

**Sources:**[Mcm.hpp 300-302](https://github.com/tenstorrent/whisper/blob/733fd921/Mcm.hpp#L300-L302)[Mcm.cpp 3860-3960](https://github.com/tenstorrent/whisper/blob/733fd921/Mcm.cpp#L3860-L3960)

Dismiss
Refresh this wiki

Enter email to refresh
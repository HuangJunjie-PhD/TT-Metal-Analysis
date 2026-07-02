---
project: tt-exalens
github: https://github.com/tenstorrent/tt-exalens
deepwiki: https://deepwiki.com/tenstorrent/tt-exalens/6-risc-v-debugging-system
---

# RISC-V Debugging System

Relevant source files
*   [test/ttexalens/unit_tests/core_simulator.py](https://github.com/tenstorrent/tt-exalens/blob/046c35eb/test/ttexalens/unit_tests/core_simulator.py)
*   [test/ttexalens/unit_tests/test_risc_debug.py](https://github.com/tenstorrent/tt-exalens/blob/046c35eb/test/ttexalens/unit_tests/test_risc_debug.py)
*   [ttexalens/hardware/baby_risc_debug.py](https://github.com/tenstorrent/tt-exalens/blob/046c35eb/ttexalens/hardware/baby_risc_debug.py)
*   [ttexalens/hardware/blackhole/baby_risc_debug.py](https://github.com/tenstorrent/tt-exalens/blob/046c35eb/ttexalens/hardware/blackhole/baby_risc_debug.py)
*   [ttexalens/hardware/memory_block.py](https://github.com/tenstorrent/tt-exalens/blob/046c35eb/ttexalens/hardware/memory_block.py)
*   [ttexalens/hardware/quasar/baby_risc_debug.py](https://github.com/tenstorrent/tt-exalens/blob/046c35eb/ttexalens/hardware/quasar/baby_risc_debug.py)
*   [ttexalens/hardware/quasar/functional_neo_block.py](https://github.com/tenstorrent/tt-exalens/blob/046c35eb/ttexalens/hardware/quasar/functional_neo_block.py)
*   [ttexalens/hardware/risc_debug.py](https://github.com/tenstorrent/tt-exalens/blob/046c35eb/ttexalens/hardware/risc_debug.py)
*   [ttexalens/hardware/wormhole/baby_risc_debug.py](https://github.com/tenstorrent/tt-exalens/blob/046c35eb/ttexalens/hardware/wormhole/baby_risc_debug.py)

The RISC-V Debugging System provides low-level control and introspection capabilities for RISC-V cores on Tenstorrent devices. This system enables halting, stepping, continuing execution, reading/writing memory and registers, setting watchpoints, and loading executable code.

This page documents the debug architecture, hardware interface, execution control mechanisms, and memory access patterns. For higher-level debugging features like callstack unwinding and symbolic variable access, see sections 3.7 (Debugging and Callstacks) and 3.8 (Symbolic Variable Access). For ELF file parsing and management, see section 3.5 (ELF Management).

## Debug Architecture Overview

The RISC-V debugging system consists of multiple layers that abstract hardware-specific details while providing uniform access to debug capabilities across different chip architectures.

### Core Debug Classes

**Core Debug Class Hierarchy**

Sources: [ttexalens/hardware/baby_risc_debug.py 468-480](https://github.com/tenstorrent/tt-exalens/blob/046c35eb/ttexalens/hardware/baby_risc_debug.py#L468-L480)[ttexalens/hardware/baby_risc_debug.py 218-240](https://github.com/tenstorrent/tt-exalens/blob/046c35eb/ttexalens/hardware/baby_risc_debug.py#L218-L240)

### Virtual Register Interface

The debug hardware provides a virtual register interface accessed through physical debug registers. Commands are issued by writing to control registers and results are read from status registers.

| Virtual Register | Address | Purpose |
| --- | --- | --- |
| `REG_STATUS` | 0 | Core status (halted, watchpoint hit, ebreak) |
| `REG_COMMAND` | 1 | Command register (halt, step, continue, etc.) |
| `REG_COMMAND_ARG_0` | 2 | First command argument |
| `REG_COMMAND_ARG_1` | 3 | Second command argument |
| `REG_COMMAND_RETURN_VALUE` | 4 | Command return value |
| `REG_HW_WATCHPOINT_SETTINGS` | 5 | Watchpoint configuration |
| `REG_HW_WATCHPOINT_0-7` | 10-17 | Watchpoint addresses |

Sources: [ttexalens/hardware/baby_risc_debug.py 25-39](https://github.com/tenstorrent/tt-exalens/blob/046c35eb/ttexalens/hardware/baby_risc_debug.py#L25-L39)

**Hardware Register Access Pattern**

Sources: [ttexalens/hardware/baby_risc_debug.py 262-290](https://github.com/tenstorrent/tt-exalens/blob/046c35eb/ttexalens/hardware/baby_risc_debug.py#L262-L290)[ttexalens/hardware/baby_risc_debug.py 397-403](https://github.com/tenstorrent/tt-exalens/blob/046c35eb/ttexalens/hardware/baby_risc_debug.py#L397-L403)

### BabyRiscInfo Configuration

The `BabyRiscInfo` dataclass contains all core-specific configuration including memory layout, register addresses, and feature flags.

**Key Fields:**

*   `risc_name`: Core identifier (e.g., "brisc", "trisc0", "erisc")
*   `risc_id`: Numeric identifier used in register addressing
*   `l1`: L1 memory block configuration
*   `data_private_memory`: Private data memory block (accessible only via debug interface)
*   `code_private_memory`: Private code memory block
*   `max_watchpoints`: Number of hardware watchpoints (typically 8)
*   `reset_flag_shift`: Bit position in reset register
*   `default_code_start_address`: Default PC on reset
*   `branch_prediction_register`: Register for controlling branch prediction
*   `debug_hardware_present`: Whether debug hardware is available

Sources: [ttexalens/hardware/baby_risc_info.py 1-100](https://github.com/tenstorrent/tt-exalens/blob/046c35eb/ttexalens/hardware/baby_risc_info.py#L1-L100)

## Execution Control

The debugging system provides fine-grained control over RISC-V execution state through halt, step, and continue operations.

**Execution State Machine**

Sources: [ttexalens/hardware/baby_risc_debug.py 299-368](https://github.com/tenstorrent/tt-exalens/blob/046c35eb/ttexalens/hardware/baby_risc_debug.py#L299-L368)

### Core Control Operations

#### Halt Operation

The `halt()` method stops execution and enters debug mode. The core status is checked to confirm halting succeeded.

`# Implementation in BabyRiscDebugHardwaredef halt(self):    if self.is_halted():        util.WARN(f"Halt: {self.risc_info.risc_name} core is already halted")        return    self._halt_command()    if not self.is_halted():        raise RiscHaltError(self.risc_info.risc_name, self.risc_info.noc_block.location)`
**Command Sequence:**

1.   Check if already halted
2.   Write `COMMAND_DEBUG_MODE | COMMAND_HALT` to `REG_COMMAND`
3.   Verify halted status by reading `REG_STATUS`

Sources: [ttexalens/hardware/baby_risc_debug.py 299-306](https://github.com/tenstorrent/tt-exalens/blob/046c35eb/ttexalens/hardware/baby_risc_debug.py#L299-L306)

#### Step Operation

The `step()` method executes a single instruction while remaining in debug mode. Platform-specific implementations may execute multiple steps to work around hardware issues.

**Blackhole Workaround:**

`def step(self):    # Blackhole requires executing step twice due to hardware bug    super().step()    super().step()`
**Wormhole Workaround:**

`def step(self):    # Disable branch prediction as hardware workaround    if self.baby_risc_info.branch_prediction_register is not None:        self.set_branch_prediction(False)    return super().step()`
Sources: [ttexalens/hardware/blackhole/baby_risc_debug.py 15-18](https://github.com/tenstorrent/tt-exalens/blob/046c35eb/ttexalens/hardware/blackhole/baby_risc_debug.py#L15-L18)[ttexalens/hardware/wormhole/baby_risc_debug.py 26-30](https://github.com/tenstorrent/tt-exalens/blob/046c35eb/ttexalens/hardware/wormhole/baby_risc_debug.py#L26-L30)

#### Continue Operation

The `cont()` method resumes normal execution from a halted state. Different platforms may require different approaches:

*   **Standard**: Write `COMMAND_DEBUG_MODE | COMMAND_CONTINUE`
*   **Wormhole (functional workers)**: Disable branch prediction, then continue
*   **Wormhole (erisc)**: Use `continue_without_debug()` to avoid lockup
*   **Continue without debug**: Write only `COMMAND_CONTINUE` (no debug mode bit)

Sources: [ttexalens/hardware/baby_risc_debug.py 341-348](https://github.com/tenstorrent/tt-exalens/blob/046c35eb/ttexalens/hardware/baby_risc_debug.py#L341-L348)[ttexalens/hardware/wormhole/baby_risc_debug.py 13-24](https://github.com/tenstorrent/tt-exalens/blob/046c35eb/ttexalens/hardware/wormhole/baby_risc_debug.py#L13-L24)

### Reset Control

Reset management is core to execution control, allowing cores to be stopped for loading code or recovering from errors.

`def set_reset_signal(self, value: bool):    """Assert (1) or deassert (0) the reset signal"""    reset_reg = self.__read(self.RISC_DBG_SOFT_RESET0)    reset_reg = (reset_reg & ~(1 << self.baby_risc_info.reset_flag_shift)) | \                (value << self.baby_risc_info.reset_flag_shift)    self.__write(self.RISC_DBG_SOFT_RESET0, reset_reg)`
Sources: [ttexalens/hardware/baby_risc_debug.py 509-518](https://github.com/tenstorrent/tt-exalens/blob/046c35eb/ttexalens/hardware/baby_risc_debug.py#L509-L518)

### Status Reading

The `read_status()` method returns a `BabyRiscDebugStatus` object containing:

| Field | Bit Mask | Description |
| --- | --- | --- |
| `is_halted` | `STATUS_HALTED (0x1)` | Core is halted |
| `is_pc_watchpoint_hit` | `STATUS_PC_WATCHPOINT_HIT (0x2)` | PC watchpoint triggered |
| `is_memory_watchpoint_hit` | `STATUS_MEMORY_WATCHPOINT_HIT (0x4)` | Memory watchpoint triggered |
| `is_ebreak_hit` | `STATUS_EBREAK_HIT (0x8)` | EBREAK instruction hit |
| `watchpoints_hit` | `STATUS_WATCHPOINT_0_HIT << i` | Individual watchpoint status (8 total) |

Sources: [ttexalens/hardware/baby_risc_debug.py 41-46](https://github.com/tenstorrent/tt-exalens/blob/046c35eb/ttexalens/hardware/baby_risc_debug.py#L41-L46)[ttexalens/hardware/baby_risc_debug.py 192-201](https://github.com/tenstorrent/tt-exalens/blob/046c35eb/ttexalens/hardware/baby_risc_debug.py#L192-L201)

## Memory and Register Access

The debugging system provides two memory access paths: NOC-based access for L1 memory and debug-interface access for private memory regions.

**Memory Access Architecture**

Sources: [ttexalens/hardware/baby_risc_debug.py 701-750](https://github.com/tenstorrent/tt-exalens/blob/046c35eb/ttexalens/hardware/baby_risc_debug.py#L701-L750)

### General Purpose Register (GPR) Access

The RISC-V architecture provides 32 general-purpose registers (x0-x31) plus the program counter (PC). Register x0 is hardwired to zero.

**Register Name Mapping:**

| Index | ABI Name | Description |
| --- | --- | --- |
| 0 | zero | Hardwired zero |
| 1 | ra | Return address |
| 2 | sp | Stack pointer |
| 10-11 | a0-a1 | Function arguments/return values |
| 32 | pc | Program counter |

**Reading GPRs:**

`def read_gpr(self, reg_index) -> int:    if not 0 <= reg_index <= 32:        raise ValueError(f"Invalid register index {reg_index}")    self.__riscv_write(REG_COMMAND_ARG_0, reg_index)    self.__riscv_write(REG_COMMAND, COMMAND_DEBUG_MODE + COMMAND_READ_REGISTER)    return self.__riscv_read(REG_COMMAND_RETURN_VALUE)`
**Writing GPRs:**

`def write_gpr(self, reg_index, value):    self.__riscv_write(REG_COMMAND_ARG_1, value)    self.__riscv_write(REG_COMMAND_ARG_0, reg_index)    self.__riscv_write(REG_COMMAND, COMMAND_DEBUG_MODE + COMMAND_WRITE_REGISTER)`
Sources: [ttexalens/hardware/baby_risc_debug.py 69-173](https://github.com/tenstorrent/tt-exalens/blob/046c35eb/ttexalens/hardware/baby_risc_debug.py#L69-L173)[ttexalens/hardware/baby_risc_debug.py 397-409](https://github.com/tenstorrent/tt-exalens/blob/046c35eb/ttexalens/hardware/baby_risc_debug.py#L397-L409)

### PC Access Platform Differences

Reading the program counter (register 32) varies by platform due to hardware limitations:

**Blackhole/Quasar:** Read from debug bus signal

`def read_gpr(self, register_index: int) -> int:    if register_index != 32:        return super().read_gpr(register_index)    else:        return int(self.noc_block.debug_bus.read_signal(self.risc_info.risc_name + "_pc"))`
**Wormhole:** Same debug bus approach

`def read_gpr(self, register_index: int) -> int:    if register_index != 32:        return super().read_gpr(register_index)    else:        return int(self.noc_block.debug_bus.read_signal(self.risc_info.risc_name + "_pc"))`
Sources: [ttexalens/hardware/blackhole/baby_risc_debug.py 20-25](https://github.com/tenstorrent/tt-exalens/blob/046c35eb/ttexalens/hardware/blackhole/baby_risc_debug.py#L20-L25)[ttexalens/hardware/wormhole/baby_risc_debug.py 32-37](https://github.com/tenstorrent/tt-exalens/blob/046c35eb/ttexalens/hardware/wormhole/baby_risc_debug.py#L32-L37)

### Memory Read/Write Operations

Memory access through the debug interface requires the core to be halted. Operations are word-aligned (4 bytes).

**Reading Memory:**

`def read_memory(self, addr) -> int:    if self.enable_asserts:        self.assert_halted()    self.__riscv_write(REG_COMMAND_ARG_0, addr)    self.__riscv_write(REG_COMMAND, COMMAND_DEBUG_MODE + COMMAND_READ_MEMORY)    return self.__riscv_read(REG_COMMAND_RETURN_VALUE)`
**Writing Memory:**

`def write_memory(self, addr, value):    if self.enable_asserts:        self.assert_halted()    self.__riscv_write(REG_COMMAND_ARG_1, value)    self.__riscv_write(REG_COMMAND_ARG_0, addr)    self.__riscv_write(REG_COMMAND, COMMAND_DEBUG_MODE + COMMAND_WRITE_MEMORY)`
Sources: [ttexalens/hardware/baby_risc_debug.py 411-427](https://github.com/tenstorrent/tt-exalens/blob/046c35eb/ttexalens/hardware/baby_risc_debug.py#L411-L427)

### Unaligned Memory Access

The `read_memory_bytes()` and `write_memory_bytes()` methods support unaligned access by performing read-modify-write operations for partial words.

**Unaligned Write Strategy:**

1.   For leading partial word: Read, modify relevant bytes, write back
2.   For complete words: Direct write
3.   For trailing partial word: Read, modify relevant bytes, write back

**Platform-Specific Optimizations:**

**Blackhole:** Can use NOC access for accessible regions

`def write_memory_bytes(self, address: int, data: bytes):    noc_address = self.baby_risc_info.translate_to_noc_address(address)    if noc_address is not None and not self.is_in_reset():        self.risc_info.noc_block.location.noc_write(noc_address, data, use_4B_mode=True)    else:        super().write_memory_bytes(address, data)`
Sources: [ttexalens/hardware/baby_risc_debug.py 696-785](https://github.com/tenstorrent/tt-exalens/blob/046c35eb/ttexalens/hardware/baby_risc_debug.py#L696-L785)[ttexalens/hardware/blackhole/baby_risc_debug.py 38-47](https://github.com/tenstorrent/tt-exalens/blob/046c35eb/ttexalens/hardware/blackhole/baby_risc_debug.py#L38-L47)

### Private Memory Access Context Manager

The `ensure_private_memory_access()` context manager enables access to private memory by temporarily taking the core out of reset if necessary.

`@contextmanagerdef ensure_private_memory_access(self):    if not self.is_in_reset():        with self.debug_hardware.ensure_halted():            yield    else:        start_address = self.baby_risc_info.get_code_start_address(self.register_store)        saved_bytes = self.__read(start_address)        try:            self.__write(start_address, 0x6F)  # JAL x0, 0 (endless loop)            self.set_reset_signal(False)            with self.debug_hardware.ensure_halted():                yield            self.set_reset_signal(True)        finally:            self.__write(start_address, saved_bytes)`
Sources: [ttexalens/hardware/baby_risc_debug.py 573-603](https://github.com/tenstorrent/tt-exalens/blob/046c35eb/ttexalens/hardware/baby_risc_debug.py#L573-L603)

## Watchpoints and Breakpoints

Hardware watchpoints enable breaking on PC addresses or memory access patterns. Up to 8 watchpoints are typically available per core.

### Watchpoint Types

| Type | Setting Value | Trigger Condition |
| --- | --- | --- |
| PC Breakpoint | `HW_WATCHPOINT_BREAKPOINT (0x0)` | PC matches address |
| Memory Read | `HW_WATCHPOINT_READ (0x1)` | Read from address |
| Memory Write | `HW_WATCHPOINT_WRITE (0x2)` | Write to address |
| Memory Access | `HW_WATCHPOINT_ACCESS (0x3)` | Read or write |

All watchpoints require `HW_WATCHPOINT_ENABLED (0x8)` bit to be set.

Sources: [ttexalens/hardware/baby_risc_debug.py 60-66](https://github.com/tenstorrent/tt-exalens/blob/046c35eb/ttexalens/hardware/baby_risc_debug.py#L60-L66)

**Watchpoint Configuration Sequence**

Sources: [ttexalens/hardware/baby_risc_debug.py 429-453](https://github.com/tenstorrent/tt-exalens/blob/046c35eb/ttexalens/hardware/baby_risc_debug.py#L429-L453)

### Setting Watchpoints

**PC Breakpoint:**

`def set_watchpoint_on_pc_address(self, id, address):    self.__set_watchpoint(id, address, HW_WATCHPOINT_ENABLED + HW_WATCHPOINT_BREAKPOINT)`
**Memory Read Watchpoint:**

`def set_watchpoint_on_memory_read(self, id, address):    self.__set_watchpoint(id, address, HW_WATCHPOINT_ENABLED + HW_WATCHPOINT_READ)`
**Memory Write Watchpoint:**

`def set_watchpoint_on_memory_write(self, id, address):    self.__set_watchpoint(id, address, HW_WATCHPOINT_ENABLED + HW_WATCHPOINT_WRITE)`
**Memory Access Watchpoint:**

`def set_watchpoint_on_memory_access(self, id, address):    self.__set_watchpoint(id, address, HW_WATCHPOINT_ENABLED + HW_WATCHPOINT_ACCESS)`
Sources: [ttexalens/hardware/baby_risc_debug.py 442-452](https://github.com/tenstorrent/tt-exalens/blob/046c35eb/ttexalens/hardware/baby_risc_debug.py#L442-L452)

### Watchpoint State Inspection

The `read_watchpoints_state()` method returns the configuration of all watchpoints:

`def read_watchpoints_state(self) -> list[BabyRiscDebugWatchpointState]:    settings = self.__riscv_read(REG_HW_WATCHPOINT_SETTINGS)    watchpoints = []    for i in range(self.risc_info.max_watchpoints):        watchpoints.append(            BabyRiscDebugWatchpointState.from_value(                (settings >> (i * 4)) & HW_WATCHPOINT_MASK            )        )    return watchpoints`
**BabyRiscDebugWatchpointState fields:**

*   `enabled`: Watchpoint is active
*   `on_access`: Triggers on any access (read or write)
*   `on_read`: Triggers on read
*   `on_write`: Triggers on write

Sources: [ttexalens/hardware/baby_risc_debug.py 454-459](https://github.com/tenstorrent/tt-exalens/blob/046c35eb/ttexalens/hardware/baby_risc_debug.py#L454-L459)[ttexalens/hardware/baby_risc_debug.py 204-215](https://github.com/tenstorrent/tt-exalens/blob/046c35eb/ttexalens/hardware/baby_risc_debug.py#L204-L215)

### EBREAK Detection

The `ebreak` instruction causes immediate halting. The status can be checked to distinguish ebreak from other halt causes:

`def is_ebreak_hit(self) -> bool:    return self.read_status().is_ebreak_hit`
When `ebreak` is hit, the PC points to the instruction immediately after the `ebreak` (PC + 4).

Sources: [ttexalens/hardware/baby_risc_debug.py 42](https://github.com/tenstorrent/tt-exalens/blob/046c35eb/ttexalens/hardware/baby_risc_debug.py#L42-L42)[test/ttexalens/unit_tests/test_risc_debug.py 428-457](https://github.com/tenstorrent/tt-exalens/blob/046c35eb/test/ttexalens/unit_tests/test_risc_debug.py#L428-L457)

## Platform-Specific Debug Implementations

Each Tenstorrent architecture has unique debug hardware characteristics requiring platform-specific implementations.

### Blackhole-Specific Features

**Double-Step Workaround:**

`class BlackholeBabyRiscDebug(BabyRiscDebug):    def step(self):        # Hardware bug requires executing step twice        super().step()        super().step()`
**Optimized Memory Access:** Blackhole can access some private memory regions through NOC when the core is running, avoiding the slower debug interface.

**TRISC2 Private Memory Issue:** Addresses at offsets > 4 bytes within 16-byte blocks cannot be accessed in TRISC2 private memory due to hardware bug #528.

`def assert_trisc2_address(self, address: int):    if self.risc_info.risc_name == "trisc2" and address % 16 > 4:        raise TTException(            f"Accessing trisc2 private memory address 0x{address:08x} "            "does not work due to blackhole bug."        )`
Sources: [ttexalens/hardware/blackhole/baby_risc_debug.py 11-53](https://github.com/tenstorrent/tt-exalens/blob/046c35eb/ttexalens/hardware/blackhole/baby_risc_debug.py#L11-L53)

### Wormhole-Specific Features

**Branch Prediction Workaround:** Wormhole requires disabling branch prediction during step and continue operations to avoid hardware issues.

`class WormholeBabyRiscDebug(BabyRiscDebug):    def cont(self):        if self.baby_risc_info.branch_prediction_register is not None:            self.set_branch_prediction(False)            super().cont()        else:            # For erisc: continue without debug to avoid lockup            self.debug_hardware.continue_without_debug()`
**ERISC Limitations:** Ethernet RISC cores on Wormhole lack branch prediction control and must use `continue_without_debug()`.

Sources: [ttexalens/hardware/wormhole/baby_risc_debug.py 9-37](https://github.com/tenstorrent/tt-exalens/blob/046c35eb/ttexalens/hardware/wormhole/baby_risc_debug.py#L9-L37)

### Quasar-Specific Features

**NEO Block Structure:** Quasar has NEO blocks containing multiple TRISC cores (TRISC0-TRISC3), each with separate debug hardware.

**Instruction Cache Flush:** Quasar requires flushing the PC after invalidating instruction cache:

`class QuasarBabyRiscDebug(BabyRiscDebug):    def invalidate_instruction_cache(self):        pc = self.get_pc()        register = self.register_store.get_register_description(            "RISCV_IC_INVALIDATE_InvalidateAll"        )        self._write_register(register, 0)        self._write_register(register, 1 << (3 - self.risc_info.risc_id))        # Flush PC to activate instruction cache        self.debug_hardware.flush(pc)`
**NEO-Specific PC Reading:** PC is read from NEO block's debug bus rather than NOC block's debug bus.

`def read_gpr(self, register_index: int) -> int:    if register_index != 32:        return super().read_gpr(register_index)    else:        return int(self.neo_block.debug_bus.read_signal(            self.risc_info.risc_name + "_pc"        ))`
Sources: [ttexalens/hardware/quasar/baby_risc_debug.py 9-35](https://github.com/tenstorrent/tt-exalens/blob/046c35eb/ttexalens/hardware/quasar/baby_risc_debug.py#L9-L35)[ttexalens/hardware/quasar/functional_neo_block.py 58-200](https://github.com/tenstorrent/tt-exalens/blob/046c35eb/ttexalens/hardware/quasar/functional_neo_block.py#L58-L200)

### Platform Comparison Table

| Feature | Blackhole | Wormhole | Quasar |
| --- | --- | --- | --- |
| Step Operation | 2x step required | Branch prediction disabled | Standard |
| Continue Operation | Standard | Branch prediction disabled / no debug | Standard |
| NOC-based Private Access | Available (some regions) | Not available | Not available |
| PC Reading | Debug bus | Debug bus | NEO debug bus |
| Instruction Cache | Standard invalidate | Standard invalidate | Requires PC flush |
| Core Structure | Single RISC per block | Single RISC per block | 4 TRISCs per NEO |

Sources: [ttexalens/hardware/blackhole/baby_risc_debug.py 11-53](https://github.com/tenstorrent/tt-exalens/blob/046c35eb/ttexalens/hardware/blackhole/baby_risc_debug.py#L11-L53)[ttexalens/hardware/wormhole/baby_risc_debug.py 9-37](https://github.com/tenstorrent/tt-exalens/blob/046c35eb/ttexalens/hardware/wormhole/baby_risc_debug.py#L9-L37)[ttexalens/hardware/quasar/baby_risc_debug.py 9-35](https://github.com/tenstorrent/tt-exalens/blob/046c35eb/ttexalens/hardware/quasar/baby_risc_debug.py#L9-L35)

## ELF Loader Implementation

The `ElfLoader` class handles loading executable code into RISC-V cores, managing address translation and private memory access.

**ELF Loading Architecture**

Sources: [ttexalens/elf_loader.py 12-233](https://github.com/tenstorrent/tt-exalens/blob/046c35eb/ttexalens/elf_loader.py#L12-L233)

### Loading Process

The ELF loading process consists of several stages:

1.   **Verify core is in reset**
2.   **Parse sections to load** (`.init`, `.text`, `.ldm_data`, `.gcov_info`)
3.   **Determine address remapping** for `loader_data` and `loader_code`
4.   **Write sections to memory**
5.   **Verify writes** (optional)
6.   **Set code start address** to `.init` section

`def load_elf(self, elf_file: ParsedElfFile, verify_write: bool = True,              return_start_address: bool = False) -> int | None:    # Risc must be in reset    assert self.risc_debug.is_in_reset()        # Load sections avoiding private memory    init_section_address = self.load_elf_sections(        elf_file,         loader_data=".loader_init",         loader_code=".loader_code",        verify_write=verify_write    )        if return_start_address:        return init_section_address    else:        self.risc_debug.set_code_start_address(init_section_address)        return None`
Sources: [ttexalens/elf_loader.py 201-217](https://github.com/tenstorrent/tt-exalens/blob/046c35eb/ttexalens/elf_loader.py#L201-L217)

### Address Remapping

The `remap_address()` method translates private memory addresses when using loader sections:

`def remap_address(self, address: int, loader_data: int | None,                   loader_code: int | None):    data_private_memory = self.risc_debug.get_data_private_memory()    if ElfLoader.__inside_private_memory(data_private_memory, address):        if loader_data is not None:            return address - data_private_memory.address.private_address + loader_data        return address        code_private_memory = self.risc_debug.get_code_private_memory()    if ElfLoader.__inside_private_memory(code_private_memory, address):        if loader_code is not None:            return address - code_private_memory.address.private_address + loader_code        return address        return address`
This allows ELF files compiled for private memory addresses to be loaded into L1 memory by providing alternative load addresses through `.loader_init` and `.loader_code` sections.

Sources: [ttexalens/elf_loader.py 124-141](https://github.com/tenstorrent/tt-exalens/blob/046c35eb/ttexalens/elf_loader.py#L124-L141)

### Memory Access Path Selection

The loader automatically selects the appropriate memory access method:

**Private Memory (no NOC address):**

`def write_block_through_debug(self, address, data):    self.mem_access.write(address, data)`
**L1 Memory (NOC accessible):**

`def write_block(self, address, data: bytes):    private_data = self.risc_debug.get_data_private_memory()    private_code = self.risc_debug.get_code_private_memory()        if (inside_private_data and no_noc_address) or \       (inside_private_code and no_noc_address):        self.write_block_through_debug(address, data)    else:        self.location.noc_write(address, data)`
Sources: [ttexalens/elf_loader.py 60-102](https://github.com/tenstorrent/tt-exalens/blob/046c35eb/ttexalens/elf_loader.py#L60-L102)

### Run ELF Operation

The `run_elf()` method combines loading with execution:

`def run_elf(self, elf_file: ParsedElfFile, verify_write: bool = True):    # Ensure core is in reset    if not self.risc_debug.is_in_reset():        self.risc_debug.set_reset_signal(True)        # Load ELF    self.load_elf(elf_file, verify_write=verify_write)        # Take core out of reset    self.risc_debug.set_reset_signal(False)    assert not self.risc_debug.is_in_reset()        # Verify execution started correctly    if self.risc_debug.can_debug():        assert not self.risc_debug.is_halted() or \               self.risc_debug.is_ebreak_hit()`
Sources: [ttexalens/elf_loader.py 219-233](https://github.com/tenstorrent/tt-exalens/blob/046c35eb/ttexalens/elf_loader.py#L219-L233)

### JAL Instruction Generation

The loader can generate JAL (Jump And Link) instructions for creating jump tables or boot code:

`@staticmethoddef get_jump_to_offset_instruction(offset: int, rd: int = 0) -> int:    """Generate JAL instruction for given offset (-2^20 to 2^20-1)"""    if offset < -(2**20) or offset >= 2**20:        raise ValueError("Offset out of range")        offset &= 0x1FFFFF    jal_offset = (        ((offset >> 20) & 0x1) << 31 |        ((offset >> 12) & 0xFF) << 12 |        ((offset >> 11) & 0x1) << 20 |        ((offset >> 1) & 0x3FF) << 21    )    return jal_offset | (rd << 7) | 0x6F`
Sources: [ttexalens/elf_loader.py 25-58](https://github.com/tenstorrent/tt-exalens/blob/046c35eb/ttexalens/elf_loader.py#L25-L58)

### High-Level API Integration

The library provides convenience functions for ELF operations:

**load_elf():**

`def load_elf(    elf_file: str | ParsedElfFile,    location: str | OnChipCoordinate | list[str | OnChipCoordinate],    risc_name: str,    neo_id: int | None = None,    device_id: int = 0,    context: Context | None = None,    return_start_address: bool = False,    verify_write: bool = True,) -> None | int | list[int]`
**run_elf():**

`def run_elf(    elf_file: str | ParsedElfFile,    location: str | OnChipCoordinate | list[str | OnChipCoordinate],    risc_name: str,    neo_id: int | None = None,    device_id: int = 0,    context: Context | None = None,    verify_write: bool = True,) -> None`
Both functions support:

*   Single location, list of locations, or "all" cores
*   Automatic ELF parsing if file path is provided
*   Cross-device operation via `device_id` parameter

Sources: [ttexalens/tt_exalens_lib.py 375-502](https://github.com/tenstorrent/tt-exalens/blob/046c35eb/ttexalens/tt_exalens_lib.py#L375-L502)

This wiki is featured in the [repository](https://github.com/tenstorrent/tt-exalens/blob/main/README.md)

Dismiss
Refresh this wiki

Enter email to refresh

---
project: tt-exalens
github: https://github.com/tenstorrent/tt-exalens
deepwiki: https://deepwiki.com/tenstorrent/tt-exalens/7-advanced-features
---

# Advanced Features

Relevant source files
*   [riscv-src/globals_test.cc](https://github.com/tenstorrent/tt-exalens/blob/046c35eb/riscv-src/globals_test.cc)
*   [test/ttexalens/unit_tests/test_debug_symbols.py](https://github.com/tenstorrent/tt-exalens/blob/046c35eb/test/ttexalens/unit_tests/test_debug_symbols.py)
*   [test/ttexalens/unit_tests/test_ttexalens_init.py](https://github.com/tenstorrent/tt-exalens/blob/046c35eb/test/ttexalens/unit_tests/test_ttexalens_init.py)
*   [ttexalens/cli.py](https://github.com/tenstorrent/tt-exalens/blob/046c35eb/ttexalens/cli.py)
*   [ttexalens/cli_commands/gdb.py](https://github.com/tenstorrent/tt-exalens/blob/046c35eb/ttexalens/cli_commands/gdb.py)
*   [ttexalens/elf/__init__.py](https://github.com/tenstorrent/tt-exalens/blob/046c35eb/ttexalens/elf/__init__.py)
*   [ttexalens/elf/die.py](https://github.com/tenstorrent/tt-exalens/blob/046c35eb/ttexalens/elf/die.py)
*   [ttexalens/elf/frame.py](https://github.com/tenstorrent/tt-exalens/blob/046c35eb/ttexalens/elf/frame.py)
*   [ttexalens/elf/parsed.py](https://github.com/tenstorrent/tt-exalens/blob/046c35eb/ttexalens/elf/parsed.py)
*   [ttexalens/elf/variable.py](https://github.com/tenstorrent/tt-exalens/blob/046c35eb/ttexalens/elf/variable.py)
*   [ttexalens/gdb/gdb_client.py](https://github.com/tenstorrent/tt-exalens/blob/046c35eb/ttexalens/gdb/gdb_client.py)
*   [ttexalens/gdb/gdb_communication.py](https://github.com/tenstorrent/tt-exalens/blob/046c35eb/ttexalens/gdb/gdb_communication.py)
*   [ttexalens/gdb/gdb_server.py](https://github.com/tenstorrent/tt-exalens/blob/046c35eb/ttexalens/gdb/gdb_server.py)
*   [ttexalens/uistate.py](https://github.com/tenstorrent/tt-exalens/blob/046c35eb/ttexalens/uistate.py)

This page provides an overview of TTExaLens's advanced capabilities that go beyond basic memory and register access. These features are used for deep firmware debugging: attaching a standard GDB client to RISC-V cores, accessing hardware over a network, navigating compiled ELF debug symbols, and sampling specialized hardware signals. Each subsection here summarizes one capability and links to its dedicated sub-page for full API details.

For general memory and register operations see pages [3.3](https://deepwiki.com/tenstorrent/tt-exalens/3.3-memory-access-operations) and [3.4](https://deepwiki.com/tenstorrent/tt-exalens/3.4-register-access). For the core RISC-V execution control primitives (halt, step, continue) see [6](https://deepwiki.com/tenstorrent/tt-exalens/6-risc-v-debugging-system).

* * *

## Feature Map

The following diagram shows how the advanced features relate to each other and to the rest of the system.

**Advanced Features Dependency Map**

Sources: [ttexalens/gdb/gdb_server.py 55-94](https://github.com/tenstorrent/tt-exalens/blob/046c35eb/ttexalens/gdb/gdb_server.py#L55-L94)[ttexalens/gdb/gdb_data.py 1-42](https://github.com/tenstorrent/tt-exalens/blob/046c35eb/ttexalens/gdb/gdb_data.py#L1-L42)[ttexalens/server.py 41-80](https://github.com/tenstorrent/tt-exalens/blob/046c35eb/ttexalens/server.py#L41-L80)[ttexalens/elf/parsed.py 145-205](https://github.com/tenstorrent/tt-exalens/blob/046c35eb/ttexalens/elf/parsed.py#L145-L205)[ttexalens/elf/variable.py 20-52](https://github.com/tenstorrent/tt-exalens/blob/046c35eb/ttexalens/elf/variable.py#L20-L52)[ttexalens/elf/frame.py 102-132](https://github.com/tenstorrent/tt-exalens/blob/046c35eb/ttexalens/elf/frame.py#L102-L132)

* * *

## GDB Server Integration

TTExaLens can expose RISC-V cores as GDB processes through an implementation of the GDB Remote Serial Protocol. This allows standard GDB clients (including `riscv-tt-elf-gdb` from the SFPI toolchain) to attach, inspect registers, read memory, set watchpoints, and unwind callstacks.

**GDB Server Component Diagram**

Sources: [ttexalens/gdb/gdb_server.py 55-93](https://github.com/tenstorrent/tt-exalens/blob/046c35eb/ttexalens/gdb/gdb_server.py#L55-L93)[ttexalens/gdb/gdb_communication.py 34-218](https://github.com/tenstorrent/tt-exalens/blob/046c35eb/ttexalens/gdb/gdb_communication.py#L34-L218)[ttexalens/gdb/gdb_data.py 10-41](https://github.com/tenstorrent/tt-exalens/blob/046c35eb/ttexalens/gdb/gdb_data.py#L10-L41)

Key classes and their roles:

| Class | File | Role |
| --- | --- | --- |
| `GdbServer` | `gdb/gdb_server.py` | Main server thread; handles GDB RSP messages, exposes RISC-V cores as processes |
| `GdbProcess` | `gdb/gdb_data.py` | Maps a single RISC-V core (`RiscDebug` + `MemoryAccess`) to a GDB process ID |
| `GdbThreadId` | `gdb/gdb_data.py` | Encodes `process_id` and `thread_id` as per the multiprocess GDB RSP extension |
| `ServerSocket` | `gdb/gdb_communication.py` | Accepts incoming TCP connections from a GDB client |
| `ClientSocket` | `gdb/gdb_communication.py` | Wraps a connected socket with `read`/`write` helpers |
| `GdbInputStream` | `gdb/gdb_communication.py` | Parses the GDB RSP framing (checksum, escaping, ack) from raw socket bytes |
| `GdbMessageParser` | `gdb/gdb_communication.py` | Provides methods (`parse`, `parse_hex`, `parse_thread_id`) to decode a single RSP message |
| `GdbMessageWriter` | `gdb/gdb_communication.py` | Builds and sends framed RSP responses |
| `GdbFileServer` | `gdb/gdb_file_server.py` | Serves file open/read/close operations requested by the GDB client |

The `GdbServer.available_processes` property scans all non-reset `RiscDebug` instances on all devices and assembles them into `GdbProcess` objects. Each process exposes a `thread_id` (`GdbThreadId`) following the RSP multiprocess convention.

The CLI command `gdb start [<port>]` / `gdb stop` in [ttexalens/cli_commands/gdb.py 1-50](https://github.com/tenstorrent/tt-exalens/blob/046c35eb/ttexalens/cli_commands/gdb.py#L1-L50) delegates to `UIState.start_gdb` / `UIState.stop_gdb`.

For the full GDB server API and protocol details, see [7.1](https://deepwiki.com/tenstorrent/tt-exalens/7.1-gdb-server-integration).

* * *

## Remote Device Access

TTExaLens can serve hardware access over a network so that a client machine can debug a remote device without a local PCI connection.

**Remote Access Architecture**

Sources: [ttexalens/server.py 41-290](https://github.com/tenstorrent/tt-exalens/blob/046c35eb/ttexalens/server.py#L41-L290)[ttexalens/umd_api.py 44-146](https://github.com/tenstorrent/tt-exalens/blob/046c35eb/ttexalens/umd_api.py#L44-L146)

The `TTExaLensServer` class registers `UmdApi` and `FileAccessApi` objects with a Pyro5 daemon. Clients call `connect_to_server(server_host, port)` which returns a `(UmdApi, FileAccessApi)` tuple backed by Pyro5 proxies. The `FileAccessApiWrapper` in [ttexalens/server.py 245-264](https://github.com/tenstorrent/tt-exalens/blob/046c35eb/ttexalens/server.py#L245-L264) handles binary file transfer by calling `get_binary_content` on the remote side (which returns serializable `bytes`) and wrapping the result in `io.BytesIO`.

Complex return types from `UmdApi` are registered on-the-fly via `TTExaLensServer._wrap_object` and returned as additional Pyro5 proxies. A fixed set of UMD types (`tt_umd.ARCH`, `tt_umd.CoreCoord`, etc.) are registered with custom Pyro5 serializers in [ttexalens/server.py 147-230](https://github.com/tenstorrent/tt-exalens/blob/046c35eb/ttexalens/server.py#L147-L230)

The functions `start_server(port, context)` and `connect_to_server(server_host, port)` in [ttexalens/server.py 234-290](https://github.com/tenstorrent/tt-exalens/blob/046c35eb/ttexalens/server.py#L234-L290) are the primary entry points.

For complete documentation, see [7.2](https://deepwiki.com/tenstorrent/tt-exalens/7.2-remote-device-access).

* * *

## ELF and DWARF Parsing

The ELF/DWARF subsystem parses compiled ELF binaries (with DWARF debug info) to expose symbol names, types, global variables, source locations, and call frame information to the rest of the debugger.

**ELF Parsing Class Hierarchy**

Sources: [ttexalens/elf/parsed.py 145-368](https://github.com/tenstorrent/tt-exalens/blob/046c35eb/ttexalens/elf/parsed.py#L145-L368)[ttexalens/elf/die.py 44-300](https://github.com/tenstorrent/tt-exalens/blob/046c35eb/ttexalens/elf/die.py#L44-L300)[ttexalens/elf/frame.py 102-132](https://github.com/tenstorrent/tt-exalens/blob/046c35eb/ttexalens/elf/frame.py#L102-L132)

The entry point is `read_elf(file_ifc, elf_file_path, load_address, require_debug_symbols)` in [ttexalens/elf/parsed.py 353-368](https://github.com/tenstorrent/tt-exalens/blob/046c35eb/ttexalens/elf/parsed.py#L353-L368) It returns a `ParsedElfFile` (or `ParsedElfFileWithOffset` when a load address is given). All expensive properties (`variables`, `types`, `symbols`, `file_line`, `frame_info`) use `@cached_property` and are computed on first access.

`ElfDie` in [ttexalens/elf/die.py 44-300](https://github.com/tenstorrent/tt-exalens/blob/046c35eb/ttexalens/elf/die.py#L44-L300) wraps a raw pyelftools `DIE`. Key cached properties:

| Property | Description |
| --- | --- |
| `name` | Demangled symbol name |
| `path` | Fully-qualified name including namespace/class scope |
| `address` | Absolute address of the variable or member offset |
| `size` | Size in bytes, resolved through typedefs and arrays |
| `resolved_type` | Follows `typedef`/`const`/`volatile` chains to the underlying type |
| `dereference_type` | For pointer types, the pointed-to type |
| `array_element_type` | For array types, the element type |

`find_die_by_name` in [ttexalens/elf/parsed.py 207-231](https://github.com/tenstorrent/tt-exalens/blob/046c35eb/ttexalens/elf/parsed.py#L207-L231) performs a hierarchical lookup using `::` as a separator, allowing names like `"MyNamespace::MyClass::field"`.

For full API documentation see [7.3](https://deepwiki.com/tenstorrent/tt-exalens/7.3-elf-and-dwarf-parsing).

* * *

## Symbolic Variable System

`ElfVariable` in [ttexalens/elf/variable.py 20-617](https://github.com/tenstorrent/tt-exalens/blob/046c35eb/ttexalens/elf/variable.py#L20-L617) provides a Python object that maps directly to a live firmware variable in device memory. It is constructed from an `ElfDie` (the type descriptor) and an address, and takes a `MemoryAccess` object for all reads and writes.

**ElfVariable Access Patterns**

Sources: [ttexalens/elf/variable.py 20-617](https://github.com/tenstorrent/tt-exalens/blob/046c35eb/ttexalens/elf/variable.py#L20-L617)

Key behaviors:

*   **Attribute access** (`var.field`): `__getattr__` calls `get_member`. If the variable is a pointer type, it automatically dereferences before searching. For unnamed unions and C++ inheritance hierarchies, `_resolve_unnamed_struct_union_member` and `_resolve_inheritance_member` are called as fallbacks ([ttexalens/elf/variable.py 53-90](https://github.com/tenstorrent/tt-exalens/blob/046c35eb/ttexalens/elf/variable.py#L53-L90)).
*   **Array iteration**: `__len__` reads the `DW_AT_upper_bound` attribute; `__iter__` yields successive `ElfVariable` elements; `as_list()` and `as_value_list()` return Python lists.
*   **Cached snapshot**: `read()` calls `read_bytes()` once and returns a new `ElfVariable` backed by a `CachedReadMemoryAccess`, so subsequent member accesses do not generate additional device reads.
*   **Operator overloads**: All arithmetic and bitwise operators resolve by calling `read_value()` and performing the operation on the Python result. They return Python scalars, not `ElfVariable` instances. The `__index__` method allows an `ElfVariable` to be used directly as a Python list index.
*   **Error propagation**: `TimeoutDeviceRegisterError`, `RiscHaltError`, and `RestrictedMemoryAccessError` are explicitly re-raised rather than swallowed by the generic exception handlers in `__str__`, `__repr__`, `__hash__`, and `__format__`.

For the full `ElfVariable` API reference, see [7.4](https://deepwiki.com/tenstorrent/tt-exalens/7.4-symbolic-variable-system).

* * *

## Callstack Unwinding

Callstack reconstruction uses DWARF Call Frame Information (CFI) stored in the `.debug_frame` section of ELF files. The classes in `ttexalens/elf/frame.py` implement this.

| Class | Role |
| --- | --- |
| `FrameInfoProvider` | Parses all FDE (Frame Description Entry) records from the DWARF CFI section; maps PC ranges to FDEs |
| `FrameInfoProviderWithOffset` | Wraps `FrameInfoProvider` and applies a load-address offset before lookup |
| `FrameDescription` | For a specific PC + FDE, decodes the CFI table and implements `read_register` and `read_previous_cfa` |
| `FrameInspection` | Represents one stack frame; reads live registers from `RiscDebug` for the top frame, or from `FrameDescription` for callers |

`FrameInfoProvider.get_frame_description(pc, risc_debug)` in [ttexalens/elf/frame.py 116-119](https://github.com/tenstorrent/tt-exalens/blob/046c35eb/ttexalens/elf/frame.py#L116-L119) linearly scans the FDE list for a match.

`FrameDescription.read_previous_cfa` in [ttexalens/elf/frame.py 51-70](https://github.com/tenstorrent/tt-exalens/blob/046c35eb/ttexalens/elf/frame.py#L51-L70) computes the Canonical Frame Address (CFA) needed to locate saved registers on the stack. It uses the CFI rule `cfa_location.reg + cfa_location.offset` for the top frame, and iteratively applies saved register rules for deeper frames.

The high-level library functions `callstack` and `top_callstack` combine `FrameInfoProvider` with `RiscDebug.get_pc()` and `RiscDebug.read_gpr()` to produce a list of `CallstackEntry` objects.

For full documentation including the library API, see [7.5](https://deepwiki.com/tenstorrent/tt-exalens/7.5-callstack-unwinding).

* * *

## Tensix Core Debugging

In addition to RISC-V core debugging, TTExaLens can inspect the Tensix ALU subsystem — specifically its register files (SRCA, SRCB, and DSTACC accumulators). This is handled by `TensixDebug` and the `debug_tensix` module, which use a different access path from general RISC-V register access.

This capability is exposed through the `dump-regfile` CLI command (`dr`) and `dump-tensix-state`. For details, see [7.6](https://deepwiki.com/tenstorrent/tt-exalens/7.6-tensix-core-debugging).

* * *

## Code Coverage Extraction

The `coverage` function in `tt_exalens_lib` reads GCOV-compatible coverage counters from firmware ELFs compiled with coverage instrumentation (the `coverage` CMake build type). Counters are stored in firmware L1 memory in GCOV format and extracted using the ELF symbol table to locate them.

For the full API and build system integration details, see [7.7](https://deepwiki.com/tenstorrent/tt-exalens/7.7-code-coverage-extraction).

* * *

## Debug Bus System

The debug bus system allows sampling of internal hardware signals that are not directly addressable via NOC reads. Signal descriptors are stored in `DebugBusSignalStore`, which maps human-readable signal names to `DebugBusSignalDescription` records containing hardware selector fields (`rd_sel`, `daisy_sel`, `sig_sel`, `mask`).

Signals can be read directly or grouped for batch sampling via L1 memory using the `SignalGroupSample` / `L1MemReg2` mechanism. The CLI command `debug-bus` exposes this capability interactively. Hardware-specific signal maps are defined for Wormhole and Blackhole.

For full documentation of signal descriptors and sampling, see [7.8](https://deepwiki.com/tenstorrent/tt-exalens/7.8-debug-bus-system).

* * *

## Subsystem Cross-Reference

The table below maps each advanced feature to its primary implementation files and the sub-page with complete documentation.

| Feature | Primary Files | Detail Page |
| --- | --- | --- |
| GDB Server | `ttexalens/gdb/gdb_server.py`, `gdb/gdb_communication.py`, `gdb/gdb_data.py` | [7.1](https://deepwiki.com/tenstorrent/tt-exalens/7.1-gdb-server-integration) |
| Remote Access | `ttexalens/server.py`, `ttexalens/umd_api.py` | [7.2](https://deepwiki.com/tenstorrent/tt-exalens/7.2-remote-device-access) |
| ELF/DWARF Parsing | `ttexalens/elf/parsed.py`, `elf/die.py`, `elf/dwarf.py` | [7.3](https://deepwiki.com/tenstorrent/tt-exalens/7.3-elf-and-dwarf-parsing) |
| Symbolic Variables | `ttexalens/elf/variable.py` | [7.4](https://deepwiki.com/tenstorrent/tt-exalens/7.4-symbolic-variable-system) |
| Callstack Unwinding | `ttexalens/elf/frame.py` | [7.5](https://deepwiki.com/tenstorrent/tt-exalens/7.5-callstack-unwinding) |
| Tensix Debugging | `ttexalens/debug_tensix.py` | [7.6](https://deepwiki.com/tenstorrent/tt-exalens/7.6-tensix-core-debugging) |
| Code Coverage | `ttexalens/tt_exalens_lib.py` (coverage function) | [7.7](https://deepwiki.com/tenstorrent/tt-exalens/7.7-code-coverage-extraction) |
| Debug Bus | `ttexalens/hardware/debug_bus*.py` | [7.8](https://deepwiki.com/tenstorrent/tt-exalens/7.8-debug-bus-system) |

Dismiss
Refresh this wiki

Enter email to refresh

---
project: tt-tvm
github: https://github.com/tenstorrent/tt-tvm
deepwiki: https://deepwiki.com/tenstorrent/tt-tvm/5-tensorir-(tir)-system
---

# TensorIR (TIR) System

Relevant source files
*   [include/tvm/script/ir_builder/tir/frame.h](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/include/tvm/script/ir_builder/tir/frame.h)
*   [include/tvm/script/ir_builder/tir/ir.h](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/include/tvm/script/ir_builder/tir/ir.h)
*   [include/tvm/tir/op.h](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/include/tvm/tir/op.h)
*   [include/tvm/tir/schedule/schedule.h](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/include/tvm/tir/schedule/schedule.h)
*   [python/tvm/script/ir_builder/tir/frame.py](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/python/tvm/script/ir_builder/tir/frame.py)
*   [python/tvm/script/ir_builder/tir/ir.py](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/python/tvm/script/ir_builder/tir/ir.py)
*   [python/tvm/tir/__init__.py](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/python/tvm/tir/__init__.py)
*   [python/tvm/tir/op.py](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/python/tvm/tir/op.py)
*   [python/tvm/tir/schedule/__init__.py](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/python/tvm/tir/schedule/__init__.py)
*   [python/tvm/tir/schedule/schedule.py](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/python/tvm/tir/schedule/schedule.py)
*   [src/runtime/object.cc](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/src/runtime/object.cc)
*   [src/script/ir_builder/tir/frame.cc](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/src/script/ir_builder/tir/frame.cc)
*   [src/script/ir_builder/tir/ir.cc](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/src/script/ir_builder/tir/ir.cc)
*   [src/script/ir_builder/tir/utils.h](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/src/script/ir_builder/tir/utils.h)
*   [src/tir/op/op.cc](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/src/tir/op/op.cc)
*   [src/tir/schedule/analysis.h](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/src/tir/schedule/analysis.h)
*   [src/tir/schedule/analysis/analysis.cc](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/src/tir/schedule/analysis/analysis.cc)
*   [src/tir/schedule/concrete_schedule.cc](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/src/tir/schedule/concrete_schedule.cc)
*   [src/tir/schedule/concrete_schedule.h](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/src/tir/schedule/concrete_schedule.h)
*   [src/tir/schedule/error.cc](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/src/tir/schedule/error.cc)
*   [src/tir/schedule/error.h](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/src/tir/schedule/error.h)
*   [src/tir/schedule/primitive.h](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/src/tir/schedule/primitive.h)
*   [src/tir/schedule/primitive/cache_read_write.cc](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/src/tir/schedule/primitive/cache_read_write.cc)
*   [src/tir/schedule/primitive/get_block_loop.cc](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/src/tir/schedule/primitive/get_block_loop.cc)
*   [src/tir/schedule/schedule.cc](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/src/tir/schedule/schedule.cc)
*   [src/tir/schedule/traced_schedule.cc](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/src/tir/schedule/traced_schedule.cc)
*   [src/tir/schedule/traced_schedule.h](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/src/tir/schedule/traced_schedule.h)
*   [src/tir/schedule/transform.cc](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/src/tir/schedule/transform.cc)
*   [src/tir/schedule/transform.h](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/src/tir/schedule/transform.h)
*   [src/tir/schedule/utils.h](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/src/tir/schedule/utils.h)
*   [src/tir/transforms/legalize_packed_calls.cc](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/src/tir/transforms/legalize_packed_calls.cc)
*   [tests/python/unittest/test_aot_legalize_packed_call.py](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/tests/python/unittest/test_aot_legalize_packed_call.py)
*   [tests/python/unittest/test_tir_base.py](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/tests/python/unittest/test_tir_base.py)
*   [tests/python/unittest/test_tir_op_types.py](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/tests/python/unittest/test_tir_op_types.py)
*   [tests/python/unittest/test_tir_ops.py](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/tests/python/unittest/test_tir_ops.py)
*   [tests/python/unittest/test_tir_schedule_cache_read_write.py](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/tests/python/unittest/test_tir_schedule_cache_read_write.py)
*   [tests/python/unittest/test_tir_schedule_reindex.py](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/tests/python/unittest/test_tir_schedule_reindex.py)
*   [tests/python/unittest/test_tir_schedule_utilities.py](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/tests/python/unittest/test_tir_schedule_utilities.py)
*   [tests/python/unittest/test_tvmscript_error_report.py](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/tests/python/unittest/test_tvmscript_error_report.py)
*   [tests/python/unittest/test_tvmscript_ir_builder_tir.py](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/tests/python/unittest/test_tvmscript_ir_builder_tir.py)
*   [tests/python/unittest/test_tvmscript_roundtrip.py](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/tests/python/unittest/test_tvmscript_roundtrip.py)
*   [tests/python/unittest/test_tvmscript_syntax_sugar.py](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/tests/python/unittest/test_tvmscript_syntax_sugar.py)
*   [tests/python/unittest/test_tvmscript_type.py](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/tests/python/unittest/test_tvmscript_type.py)

TensorIR (TIR) is TVM's low-level intermediate representation for tensor programs. It provides a fine-grained, imperative representation of computational kernels with explicit loop structures, buffer allocations, and memory hierarchies. TIR sits below [Relay IR](https://deepwiki.com/tenstorrent/tt-tvm/4-relay-ir-system) in the compilation pipeline and serves as the primary interface for kernel-level optimization and scheduling before code generation.

For high-level graph optimization, see [Relay IR System](https://deepwiki.com/tenstorrent/tt-tvm/4-relay-ir-system). For schedule-based auto-tuning, see [Auto-Tuning System](https://deepwiki.com/tenstorrent/tt-tvm/8-auto-tuning-system). For code generation from TIR, see [Code Generation Backends](https://deepwiki.com/tenstorrent/tt-tvm/6-code-generation-backends).

* * *

## Purpose and Scope

TensorIR provides:

*   **Explicit loop representation** with multi-dimensional iteration spaces
*   **Fine-grained scheduling primitives** for loop transformations (split, fuse, reorder, etc.)
*   **Buffer management** with storage scopes and layout transformations
*   **Schedule API** for both manual optimization and automated search
*   **TVMScript DSL** for human-readable TIR program definition
*   **IR Builder** for programmatic TIR construction

Sources: [python/tvm/tir/__init__.py 1-105](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/python/tvm/tir/__init__.py#L1-L105)[include/tvm/tir/schedule/schedule.h 1-50](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/include/tvm/tir/schedule/schedule.h#L1-L50)

* * *

## Architecture Overview

**TensorIR System Architecture**: This diagram shows the complete TensorIR system, from input sources through core representation, scheduling interface, transformation primitives, to optimized output.

Sources: [python/tvm/tir/schedule/schedule.py 109-181](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/python/tvm/tir/schedule/schedule.py#L109-L181)[src/tir/schedule/concrete_schedule.h 34-232](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/src/tir/schedule/concrete_schedule.h#L34-L232)[src/tir/schedule/traced_schedule.h 30-111](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/src/tir/schedule/traced_schedule.h#L30-L111)

* * *

## Core TIR Constructs

### PrimFunc

`PrimFunc` is the top-level container for a TensorIR function. It encapsulates parameters, buffer declarations, function attributes, and the function body.

| Component | Type | Description |
| --- | --- | --- |
| `params` | `Array<Var>` | Function parameters (handles to buffers) |
| `body` | `Stmt` | Function body (typically a `BlockRealize`) |
| `ret_type` | `Type` | Return type |
| `buffer_map` | `Map<Var, Buffer>` | Mapping from parameter vars to buffer objects |
| `attrs` | `DictAttrs` | Function attributes (e.g., "tir.noalias") |

Sources: [python/tvm/tir/__init__.py 48](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/python/tvm/tir/__init__.py#L48-L48)

### Block

`Block` represents a computational unit with explicit iteration space, read/write regions, and optional initialization.

| Component | Type | Description |
| --- | --- | --- |
| `iter_vars` | `Array<IterVar>` | Block variables (data parallel, reduction, etc.) |
| `reads` | `Array<BufferRegion>` | Input buffer regions |
| `writes` | `Array<BufferRegion>` | Output buffer regions |
| `body` | `Stmt` | Block body (computation statements) |
| `init` | `Optional<Stmt>` | Initialization statement for reductions |
| `name_hint` | `String` | Block name for identification |

Sources: [python/tvm/tir/stmt.py](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/python/tvm/tir/stmt.py) (referenced in imports), [tests/python/unittest/test_tvmscript_roundtrip.py 36-85](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/tests/python/unittest/test_tvmscript_roundtrip.py#L36-L85)

### Buffer

`Buffer` represents a multi-dimensional array with explicit shape, data type, and storage properties.

| Property | Type | Description |
| --- | --- | --- |
| `data` | `Var` | Pointer to buffer data |
| `shape` | `Array<PrimExpr>` | Buffer dimensions |
| `dtype` | `DataType` | Element data type |
| `strides` | `Array<PrimExpr>` | Optional strides |
| `elem_offset` | `PrimExpr` | Element offset |
| `scope` | `String` | Storage scope ("global", "shared", "local") |
| `axis_separators` | `Array<IntImm>` | Layout axis separators |

Sources: [python/tvm/tir/__init__.py 25](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/python/tvm/tir/__init__.py#L25-L25)[python/tvm/script/ir_builder/tir/ir.py 92-142](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/python/tvm/script/ir_builder/tir/ir.py#L92-L142)

### IterVar

`IterVar` represents an iteration variable with a domain and iteration type.

**Iteration Types**:

*   `kDataPar`: Data parallel (spatial)
*   `kCommReduce`: Commutative reduction
*   `kOrdered`: Ordered iteration
*   `kOpaque`: Opaque (no specific semantics)
*   `kUnrolled`: Explicitly unrolled
*   `kVectorized`: SIMD vectorized
*   `kParallelized`: Thread-level parallelism
*   `kThreadIndex`: Thread index binding

Sources: [python/tvm/tir/__init__.py 31](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/python/tvm/tir/__init__.py#L31-L31)

* * *

## Schedule API

The Schedule API provides a Python-friendly interface for transforming TIR programs through scheduling primitives.

**Schedule Class Structure**: The Schedule API architecture showing the class hierarchy, core components, random variables, and transformation categories.

Sources: [python/tvm/tir/schedule/schedule.py 109-181](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/python/tvm/tir/schedule/schedule.py#L109-L181)[src/tir/schedule/concrete_schedule.h 34-75](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/src/tir/schedule/concrete_schedule.h#L34-L75)[include/tvm/tir/schedule/schedule.h 100-220](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/include/tvm/tir/schedule/schedule.h#L100-L220)

### Schedule Construction

**Parameters**:

*   `mod`: `Union[PrimFunc, IRModule]` - The function/module to schedule
*   `seed`: `Optional[int]` - Random seed for sampling (default: device random)
*   `debug_mask`: `Union[str, int]` - Debug verification level ("none", "all", or bitmask)
*   `error_render_level`: `str` - Error detail level ("detail", "fast", "none")
*   `enable_check`: `bool` - Enable prerequisite checks (default: True)

Sources: [python/tvm/tir/schedule/schedule.py 124-180](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/python/tvm/tir/schedule/schedule.py#L124-L180)[src/tir/schedule/concrete_schedule.cc 29-45](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/src/tir/schedule/concrete_schedule.cc#L29-L45)[src/tir/schedule/traced_schedule.cc 27-44](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/src/tir/schedule/traced_schedule.cc#L27-L44)

### Random Variables (RV)

Random variables are symbolic references to TIR constructs that remain valid across transformations.

| Type | Purpose | Example Usage |
| --- | --- | --- |
| `BlockRV` | References a `Block` | `block = sch.get_block("matmul")` |
| `LoopRV` | References a `For` loop | `i, j, k = sch.get_loops(block)` |
| `ExprRV` | References an integer expression | `factors = sch.sample_perfect_tile(i, n=3)` |

**Symbol Table Mapping**:

*   `BlockRV` → `StmtSRef` (pointing to `Block`)
*   `LoopRV` → `StmtSRef` (pointing to `For`)
*   `ExprRV` → `IntImm` (integer value)

Sources: [python/tvm/tir/schedule/schedule.py 42-70](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/python/tvm/tir/schedule/schedule.py#L42-L70)[include/tvm/tir/schedule/schedule.h 51-99](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/include/tvm/tir/schedule/schedule.h#L51-L99)[src/tir/schedule/concrete_schedule.h 238-291](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/src/tir/schedule/concrete_schedule.h#L238-L291)

* * *

## Loop Transformation Primitives

### split

Splits a loop into multiple consecutive loops.

**Requirements**:

1.   Loop has no annotations or thread bindings
2.   Loop starts with 0
3.   At most one factor can be `None` (auto-inferred)
4.   Product of factors ≥ loop extent

Sources: [python/tvm/tir/schedule/schedule.py 737-817](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/python/tvm/tir/schedule/schedule.py#L737-L817)[src/tir/schedule/concrete_schedule.cc 400-504](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/src/tir/schedule/concrete_schedule.cc#L400-L504)[src/tir/schedule/primitive.h 201-210](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/src/tir/schedule/primitive.h#L201-L210)

### fuse

Fuses consecutive loops into a single loop.

**Requirements**:

1.   Loops have no annotations or thread bindings
2.   Loop (i+1) is the only child of loop i
3.   All loops start with 0
4.   No loop domain depends on another fused loop

Sources: [python/tvm/tir/schedule/schedule.py 675-734](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/python/tvm/tir/schedule/schedule.py#L675-L734)[src/tir/schedule/concrete_schedule.cc 389-398](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/src/tir/schedule/concrete_schedule.cc#L389-L398)

### reorder

Reorders loops in a loop chain.

**Requirements**:

1.   Loops form a chain (connected via parent-child relationships)
2.   No outer loop domain depends on inner loop variables
3.   All blocks under the loops have affine bindings
4.   Block variables are data parallel or reduction

Sources: [python/tvm/tir/schedule/schedule.py 820-875](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/python/tvm/tir/schedule/schedule.py#L820-L875)[src/tir/schedule/concrete_schedule.cc 506-511](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/src/tir/schedule/concrete_schedule.cc#L506-L511)

### merge

Merges multiple parallel loops at the same level into one.

**Requirements**:

1.   Loops are under the same scope
2.   Loops have no annotations or thread bindings
3.   Loops have the same extent and start with 0
4.   From each loop to LCA, inner loop is the only child

Sources: [python/tvm/tir/schedule/schedule.py 600-672](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/python/tvm/tir/schedule/schedule.py#L600-L672)[src/tir/schedule/concrete_schedule.cc 378-387](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/src/tir/schedule/concrete_schedule.cc#L378-L387)

* * *

## Cache Stage Transformations

### cache_read

Inserts a cache read stage that reads from a buffer and writes to a newly allocated cache buffer.

**Process**:

1.   Allocates a new cache buffer in specified storage scope
2.   Creates a cache block that copies data from original buffer
3.   Rewrites consumer blocks to read from cache buffer

**Use Cases**:

*   Load data into shared memory (GPU)
*   Create local copies for vectorization
*   Stage data for DMA transfers

Sources: [python/tvm/tir/schedule/schedule.py 1273-1314](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/python/tvm/tir/schedule/schedule.py#L1273-L1314)[src/tir/schedule/primitive/cache_read_write.cc 1-2000](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/src/tir/schedule/primitive/cache_read_write.cc#L1-L2000)

### cache_write

Inserts a cache write stage that writes to a cache buffer and then copies to the original buffer.

**Process**:

1.   Allocates a new cache buffer in specified storage scope
2.   Rewrites producer block to write to cache buffer
3.   Creates a cache block that copies data to original buffer

Sources: [python/tvm/tir/schedule/schedule.py 1316-1357](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/python/tvm/tir/schedule/schedule.py#L1316-L1357)[src/tir/schedule/primitive/cache_read_write.cc 1-2000](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/src/tir/schedule/primitive/cache_read_write.cc#L1-L2000)

* * *

## Compute Location Transformations

### compute_at

Moves a block's computation under a specific loop.

**Requirements**:

*   Block is a complete block (all iter vars are data parallel, dominant writer)
*   Target loop is in a valid compute-at location

**Effect**: Changes computation location to improve data locality and enable fusion.

Sources: [python/tvm/tir/schedule/schedule.py 1636-1672](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/python/tvm/tir/schedule/schedule.py#L1636-L1672)[src/tir/schedule/concrete_schedule.cc 676-681](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/src/tir/schedule/concrete_schedule.cc#L676-L681)

### reverse_compute_at

Moves a block's computation to be computed at the position of its consumer.

Sources: [python/tvm/tir/schedule/schedule.py 1674-1710](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/python/tvm/tir/schedule/schedule.py#L1674-L1710)[src/tir/schedule/concrete_schedule.cc 683-689](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/src/tir/schedule/concrete_schedule.cc#L683-L689)

### compute_inline

Inlines a block's computation into its consumers.

**Requirements**:

*   Block is a trivial binding (simple element-wise operation)
*   Block has no reduction

Sources: [python/tvm/tir/schedule/schedule.py 1712-1745](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/python/tvm/tir/schedule/schedule.py#L1712-L1745)[src/tir/schedule/concrete_schedule.cc 691-694](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/src/tir/schedule/concrete_schedule.cc#L691-L694)

* * *

## Schedule State Management

**ScheduleState Data Structure**: Internal state management showing the IRModule, statement reference tree, and block metadata.

Sources: [python/tvm/tir/schedule/state.py](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/python/tvm/tir/schedule/state.py) (referenced), [src/tir/schedule/concrete_schedule.h 42-54](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/src/tir/schedule/concrete_schedule.h#L42-L54)

### StmtSRef Tree

The `StmtSRef` (Statement Reference) tree maintains a parallel tree structure to the AST, providing O(1) parent access and stable references across transformations.

**Properties**:

*   Each statement has at most one `StmtSRef`
*   `StmtSRef` nodes store parent pointers for upward traversal
*   Sequence index enables efficient sibling access
*   Tree is reconstructed after transformations via `Copy()`

Sources: [src/tir/schedule/concrete_schedule.cc 49-102](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/src/tir/schedule/concrete_schedule.cc#L49-L102)

### BlockInfo Cached Flags

Schedule state caches expensive-to-compute properties for blocks:

| Flag | Purpose | Validation |
| --- | --- | --- |
| `affine_binding` | All iter values are affine expressions | Required for many transformations |
| `region_cover` | Block's region covers all accessed data | Ensures complete computation |
| `stage_pipeline` | Scope has no WAR/opaque dependencies | Enables certain optimizations |

**Verification**: `VerifyCachedFlags()` recomputes and validates all cached flags.

Sources: [src/tir/schedule/analysis/analysis.cc 1-500](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/src/tir/schedule/analysis/analysis.cc#L1-L500)

* * *

## ConcreteSchedule vs TracedSchedule

TVM provides two schedule implementations with different trade-offs:

| Aspect | ConcreteSchedule | TracedSchedule |
| --- | --- | --- |
| **Execution** | Direct transformation | Records then executes |
| **Trace** | None | Full instruction trace |
| **Performance** | Fast (no overhead) | Slower (trace recording) |
| **Serialization** | Not supported | JSON serialization |
| **Replay** | Not supported | Full replay from trace |
| **Use Case** | Manual scheduling | Auto-tuning, search |

**Execution Modes**: Comparison between ConcreteSchedule (direct transformation) and TracedSchedule (trace-based transformation with replay capability).

### TracedSchedule Trace Format

Each instruction in the trace contains:

*   **kind**: Instruction type (e.g., "Split", "Fuse", "CacheRead")
*   **inputs**: Input random variables
*   **attrs**: Instruction attributes (e.g., factors for split)
*   **outputs**: Output random variables
*   **decision**: Optional sampling decision (for deterministic replay)

Example trace entry for `split`:

Sources: [src/tir/schedule/traced_schedule.cc 46-116](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/src/tir/schedule/traced_schedule.cc#L46-L116)[src/tir/schedule/traced_schedule.h 30-111](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/src/tir/schedule/traced_schedule.h#L30-L111)

* * *

## TVMScript DSL

TVMScript is a Python-embedded DSL for writing TIR programs in a readable syntax. It provides decorators and context managers for defining PrimFuncs.

### Basic Syntax

### TVMScript Constructs

| Construct | Purpose | Example |
| --- | --- | --- |
| `@T.prim_func` | Define a PrimFunc | `@T.prim_func def func(...): ...` |
| `T.Buffer` | Buffer type annotation | `A: T.Buffer((128, 128), "float32")` |
| `T.handle` | Generic buffer handle | `A: T.handle` |
| `T.match_buffer` | Match handle to buffer | `A = T.match_buffer(a, [128, 128])` |
| `T.grid()` | Multi-dimensional loop | `for i, j in T.grid(M, N): ...` |
| `T.block()` | Define a block | `with T.block("name"): ...` |
| `T.axis.remap()` | Bind block iter vars | `vi, vj = T.axis.remap("SS", [i, j])` |
| `T.axis.spatial()` | Spatial axis | `vi = T.axis.spatial(128, i)` |
| `T.axis.reduce()` | Reduction axis | `vk = T.axis.reduce(64, k)` |
| `T.reads()` | Read region annotation | `T.reads(A[vi, vk])` |
| `T.writes()` | Write region annotation | `T.writes(C[vi, vj])` |
| `T.init()` | Initialization block | `with T.init(): C[vi, vj] = 0` |
| `T.serial()` | Serial loop | `for i in T.serial(0, 128): ...` |
| `T.parallel()` | Parallel loop | `for i in T.parallel(0, 128): ...` |
| `T.vectorized()` | Vectorized loop | `for i in T.vectorized(0, 8): ...` |
| `T.unroll()` | Unrolled loop | `for i in T.unroll(0, 4): ...` |

### Round-Trip Conversion

TVMScript supports bidirectional conversion between text and IR:

Sources: [tests/python/unittest/test_tvmscript_roundtrip.py 1-1000](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/tests/python/unittest/test_tvmscript_roundtrip.py#L1-L1000)[python/tvm/script/ir_builder/tir/ir.py 1-800](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/python/tvm/script/ir_builder/tir/ir.py#L1-L800)

* * *

## IR Builder API

The IR Builder provides a programmatic way to construct TIR without writing TVMScript strings. It uses context managers to build the IR tree.

### Basic IR Construction

### IR Builder Contexts

| Context | Purpose | Usage |
| --- | --- | --- |
| `T.block()` | Block scope | `with T.block("name"): ...` |
| `T.grid()` | Multi-dim iteration | `with T.grid(M, N) as (i, j): ...` |
| `T.serial()` | Serial for loop | `with T.serial(0, N) as i: ...` |
| `T.parallel()` | Parallel for loop | `with T.parallel(0, N) as i: ...` |
| `T.let()` | Let binding | `with T.let(var, value): ...` |
| `T.Assert()` | Assertion | `with T.Assert(cond, msg): ...` |
| `T.allocate()` | Buffer allocation | `T.allocate([1024], "float32", "global")` |

### Low-Level Operations

IR Builder provides access to TIR operators:

Sources: [python/tvm/script/ir_builder/tir/ir.py 1-2000](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/python/tvm/script/ir_builder/tir/ir.py#L1-L2000)[src/script/ir_builder/tir/ir.cc 1-1000](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/src/script/ir_builder/tir/ir.cc#L1-L1000)

* * *

## TIR Operators

TIR provides a comprehensive set of operators for tensor computation.

### Arithmetic Operators

| Operator | C++ | Python | Description |
| --- | --- | --- | --- |
| Add | `tir::Add` | `a + b` | Addition |
| Sub | `tir::Sub` | `a - b` | Subtraction |
| Mul | `tir::Mul` | `a * b` | Multiplication |
| Div | `tir::Div` | `a / b` | Division |
| Mod | `tir::Mod` | `a % b` | Modulo |
| FloorDiv | `tir::FloorDiv` | `tir.floordiv(a, b)` | Floor division |
| FloorMod | `tir::FloorMod` | `tir.floormod(a, b)` | Floor modulo |

### Comparison Operators

| Operator | C++ | Python | Description |
| --- | --- | --- | --- |
| EQ | `tir::EQ` | `a == b` | Equal |
| NE | `tir::NE` | `a != b` | Not equal |
| LT | `tir::LT` | `a < b` | Less than |
| LE | `tir::LE` | `a <= b` | Less or equal |
| GT | `tir::GT` | `a > b` | Greater than |
| GE | `tir::GE` | `a >= b` | Greater or equal |

### Mathematical Functions

### Intrinsic Operations

| Function | Purpose |
| --- | --- |
| `call_extern()` | Call external C function |
| `call_intrin()` | Call TVM intrinsic |
| `call_llvm_intrin()` | Call LLVM intrinsic |
| `call_packed()` | Call packed function |
| `tvm_storage_sync()` | Memory synchronization |
| `tvm_thread_allreduce()` | Thread-level reduction |
| `tvm_load_matrix_sync()` | Tensor Core matrix load |
| `tvm_mma_sync()` | Tensor Core matrix multiply-accumulate |

### SIMD Operations

Sources: [python/tvm/tir/op.py 1-1500](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/python/tvm/tir/op.py#L1-L1500)[include/tvm/tir/op.h 1-1500](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/include/tvm/tir/op.h#L1-L1500)[src/tir/op/op.cc 1-1000](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/src/tir/op/op.cc#L1-L1000)

* * *

## Error Reporting and Validation

TensorIR provides comprehensive error checking with detailed error messages.

### Error Rendering Levels

| Level | Symbol | Description |
| --- | --- | --- |
| Detail | `"detail"` | Full error with TIR context and locations |
| Fast | `"fast"` | Simple error message without rendering |
| None | `"none"` | No error message |

### ScheduleError Types

**Error Hierarchy**: Schedule error types with detailed diagnostics.

### Common Error Checks

**Complete Block Validation**:

1.   All block vars are data parallel
2.   Dominant: block is the only writer of its output
3.   No overlap between read and write buffers

**Reduction Block Validation**:

1.   Block has `init` statement
2.   All block bindings are quasi-affine
3.   Block vars are data parallel or reduction
4.   Dominant: only writer of outputs
5.   Reduction vars don't index output buffers

**Affine Binding Check**:

*   All iter values are affine expressions of loop variables
*   Required for most scheduling primitives

Sources: [src/tir/schedule/analysis/analysis.cc 238-406](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/src/tir/schedule/analysis/analysis.cc#L238-L406)[python/tvm/tir/schedule/schedule.py 37-40](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/python/tvm/tir/schedule/schedule.py#L37-L40)

### Error Message Example

When a schedule error occurs with `error_render_level="detail"`:

```
ScheduleError: The block is not a complete block - it violates condition #2.
Definition of a complete block:
1) All block vars are data parallel
2) Dominant: the block is the only writer of its output, dominating the reader of its output buffers
3) No overlap between the buffers the block reads and writes

IRModule:
  <rendered TIR with highlighted error location>
```

Sources: [src/tir/schedule/concrete_schedule.cc 209-227](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/src/tir/schedule/concrete_schedule.cc#L209-L227)

* * *

## Debug and Verification

### Debug Masks

Schedule state supports verification at various levels controlled by debug masks:

| Mask | Value | Checks |
| --- | --- | --- |
| `kVerifySRefTree` | `0x1` | SRef tree consistency |
| `kVerifyCachedFlags` | `0x2` | Cached flags validity |

### Verification Functions

*   `VerifySRefTree()`: Validates that the SRef tree matches the IRModule AST
*   `VerifyCachedFlags()`: Recomputes and validates all cached block properties
*   State verification runs automatically after each transformation when debug masks are enabled

Sources: [python/tvm/tir/schedule/state.py](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/python/tvm/tir/schedule/state.py) (referenced), [src/tir/schedule/analysis.h 42-57](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/src/tir/schedule/analysis.h#L42-L57)

* * *

## Integration with Other Systems

### From Relay to TIR

Relay operators are lowered to TIR through the [Strategy System](https://deepwiki.com/tenstorrent/tt-tvm/4.6-strategy-system):

1.   Relay pass `LowerTE` converts Relay ops to TIR
2.   Each operator selects a target-specific strategy
3.   Strategy provides TIR compute and schedule templates
4.   Result is a TIR PrimFunc

### TIR to Code Generation

TIR programs are compiled to executable code through [Code Generation Backends](https://deepwiki.com/tenstorrent/tt-tvm/6-code-generation-backends):

1.   TIR PrimFunc is passed to target-specific codegen
2.   LLVM backend: TIR → LLVM IR → machine code
3.   CUDA backend: TIR → CUDA C++ → PTX
4.   VM backend: TIR → bytecode instructions

### Auto-Tuning Integration

TIR Schedule API integrates with [Auto-Tuning](https://deepwiki.com/tenstorrent/tt-tvm/8-auto-tuning-system):

1.   TracedSchedule records transformation sequence
2.   Trace serialized to JSON for reproducibility
3.   Auto-tuner explores schedule space
4.   Best schedules loaded from tuning logs

Sources: High-level architecture diagrams

* * *

## Usage Patterns

### Manual Scheduling Workflow

### Template-Based Scheduling

### Auto-Tuning Integration

Sources: Auto-tuning documentation

* * *

## Performance Considerations

### Schedule State Copy

*   `sch.copy()` performs deep copy of entire state including SRef tree
*   Expensive operation: O(n) where n is number of statements
*   TracedSchedule avoids copies by replaying trace instead
*   Use ConcreteSchedule for one-shot manual optimization

### Symbol Table Overhead

*   Each random variable lookup requires hash table access
*   For tight loops, cache `sch.get(rv)` results
*   ConcreteSchedule symbol table is `Map<ObjectRef, ObjectRef>`
*   TracedSchedule adds instruction recording overhead

### Debug Mode Performance

*   `debug_mask="all"` adds significant overhead
*   Full verification after each transformation
*   Disable for production: `debug_mask="none"`
*   Enable selectively during development

Sources: [src/tir/schedule/concrete_schedule.cc 47-207](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/src/tir/schedule/concrete_schedule.cc#L47-L207)

* * *

## Key Source Files

### Schedule Interface

*   [python/tvm/tir/schedule/schedule.py 1-2000](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/python/tvm/tir/schedule/schedule.py#L1-L2000) - Main Python Schedule API
*   [include/tvm/tir/schedule/schedule.h 1-800](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/include/tvm/tir/schedule/schedule.h#L1-L800) - C++ Schedule interface
*   [src/tir/schedule/schedule.cc 1-500](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/src/tir/schedule/schedule.cc#L1-L500) - Schedule implementation glue

### Schedule Implementations

*   [src/tir/schedule/concrete_schedule.h 1-300](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/src/tir/schedule/concrete_schedule.h#L1-L300) - ConcreteScheduleNode header
*   [src/tir/schedule/concrete_schedule.cc 1-1500](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/src/tir/schedule/concrete_schedule.cc#L1-L1500) - ConcreteSchedule implementation
*   [src/tir/schedule/traced_schedule.h 1-200](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/src/tir/schedule/traced_schedule.h#L1-L200) - TracedScheduleNode header
*   [src/tir/schedule/traced_schedule.cc 1-1000](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/src/tir/schedule/traced_schedule.cc#L1-L1000) - TracedSchedule implementation

### Transformation Primitives

*   [src/tir/schedule/primitive.h 1-600](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/src/tir/schedule/primitive.h#L1-L600) - Primitive declarations
*   [src/tir/schedule/primitive/cache_read_write.cc 1-2000](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/src/tir/schedule/primitive/cache_read_write.cc#L1-L2000) - Cache stage primitives
*   [src/tir/schedule/primitive/get_block_loop.cc 1-500](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/src/tir/schedule/primitive/get_block_loop.cc#L1-L500) - Block/loop getters

### Analysis and Validation

*   [src/tir/schedule/analysis.h 1-400](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/src/tir/schedule/analysis.h#L1-L400) - Analysis function declarations
*   [src/tir/schedule/analysis/analysis.cc 1-1000](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/src/tir/schedule/analysis/analysis.cc#L1-L1000) - Core analysis implementations

### TVMScript and IR Builder

*   [python/tvm/script/ir_builder/tir/ir.py 1-2000](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/python/tvm/script/ir_builder/tir/ir.py#L1-L2000) - Python IR Builder API
*   [src/script/ir_builder/tir/ir.cc 1-1000](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/src/script/ir_builder/tir/ir.cc#L1-L1000) - C++ IR Builder implementation
*   [tests/python/unittest/test_tvmscript_roundtrip.py 1-1500](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/tests/python/unittest/test_tvmscript_roundtrip.py#L1-L1500) - TVMScript examples

### TIR Operators

*   [python/tvm/tir/op.py 1-1500](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/python/tvm/tir/op.py#L1-L1500) - Python operator wrappers
*   [include/tvm/tir/op.h 1-1500](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/include/tvm/tir/op.h#L1-L1500) - C++ operator declarations
*   [src/tir/op/op.cc 1-1000](https://github.com/tenstorrent/tt-tvm/blob/02ad9c28/src/tir/op/op.cc#L1-L1000) - Operator implementations

Dismiss
Refresh this wiki

Enter email to refresh
---
project: riscv-ocelot
github: https://github.com/tenstorrent/riscv-ocelot
deepwiki: https://deepwiki.com/tenstorrent/riscv-ocelot/4-instruction-fetch-pipeline
---

# Instruction Fetch Pipeline

Relevant source files
*   [docs/sections/decode-stage.rst](https://github.com/tenstorrent/riscv-ocelot/blob/920fd904/docs/sections/decode-stage.rst)
*   [docs/sections/execution-stages.rst](https://github.com/tenstorrent/riscv-ocelot/blob/920fd904/docs/sections/execution-stages.rst)
*   [docs/sections/future-work.rst](https://github.com/tenstorrent/riscv-ocelot/blob/920fd904/docs/sections/future-work.rst)
*   [docs/sections/issue-units.rst](https://github.com/tenstorrent/riscv-ocelot/blob/920fd904/docs/sections/issue-units.rst)
*   [docs/sections/load-store-unit.rst](https://github.com/tenstorrent/riscv-ocelot/blob/920fd904/docs/sections/load-store-unit.rst)
*   [docs/sections/memory-system.rst](https://github.com/tenstorrent/riscv-ocelot/blob/920fd904/docs/sections/memory-system.rst)
*   [docs/sections/physical-realization.rst](https://github.com/tenstorrent/riscv-ocelot/blob/920fd904/docs/sections/physical-realization.rst)
*   [docs/sections/reg-file-bypass-network.rst](https://github.com/tenstorrent/riscv-ocelot/blob/920fd904/docs/sections/reg-file-bypass-network.rst)
*   [docs/sections/rename-stage.rst](https://github.com/tenstorrent/riscv-ocelot/blob/920fd904/docs/sections/rename-stage.rst)
*   [docs/sections/reorder-buffer.rst](https://github.com/tenstorrent/riscv-ocelot/blob/920fd904/docs/sections/reorder-buffer.rst)
*   [src/main/scala/ifu/fetch-buffer.scala](https://github.com/tenstorrent/riscv-ocelot/blob/920fd904/src/main/scala/ifu/fetch-buffer.scala)
*   [src/main/scala/ifu/fetch-target-queue.scala](https://github.com/tenstorrent/riscv-ocelot/blob/920fd904/src/main/scala/ifu/fetch-target-queue.scala)
*   [src/main/scala/ifu/frontend.scala](https://github.com/tenstorrent/riscv-ocelot/blob/920fd904/src/main/scala/ifu/frontend.scala)

The Instruction Fetch Pipeline is responsible for fetching instructions from memory, performing branch prediction, and delivering a stream of instructions to the backend of the processor. This document describes the overall organization, stages, and key components of BOOM's instruction fetch pipeline.

For information about branch prediction mechanisms specifically, see [Branch Prediction](https://deepwiki.com/tenstorrent/riscv-ocelot/4.1-branch-prediction).

## Fetch Pipeline Overview

The BOOM Frontend consists of a 4-stage instruction fetch pipeline that fetches instructions from the instruction cache, performs branch prediction, and prepares fetched instructions for the backend. These stages work together to maintain a steady flow of instructions while speculating on branches to minimize pipeline stalls.

The instruction fetch pipeline operates independently from the backend execution pipeline, allowing it to run ahead and buffer instructions to keep the execution units busy.

Sources: [src/main/scala/ifu/frontend.scala 344-405](https://github.com/tenstorrent/riscv-ocelot/blob/920fd904/src/main/scala/ifu/frontend.scala#L344-L405)[src/main/scala/ifu/frontend.scala 441-508](https://github.com/tenstorrent/riscv-ocelot/blob/920fd904/src/main/scala/ifu/frontend.scala#L441-L508)

## Key Components

The Instruction Fetch Pipeline is composed of several key components that work together to deliver instructions to the backend execution units.

Sources: [src/main/scala/ifu/frontend.scala 324-340](https://github.com/tenstorrent/riscv-ocelot/blob/920fd904/src/main/scala/ifu/frontend.scala#L324-L340)[src/main/scala/ifu/frontend.scala 333-343](https://github.com/tenstorrent/riscv-ocelot/blob/920fd904/src/main/scala/ifu/frontend.scala#L333-L343)

### BoomFrontend

The `BoomFrontend` is the top-level module that encapsulates the entire instruction fetch pipeline, connecting the instruction cache, TLB, branch predictor, and other components.

Sources: [src/main/scala/ifu/frontend.scala 297-304](https://github.com/tenstorrent/riscv-ocelot/blob/920fd904/src/main/scala/ifu/frontend.scala#L297-L304)[src/main/scala/ifu/frontend.scala 324-341](https://github.com/tenstorrent/riscv-ocelot/blob/920fd904/src/main/scala/ifu/frontend.scala#L324-L341)

### FetchTargetQueue (FTQ)

The FetchTargetQueue (FTQ) is a critical structure that tracks fetch addresses and branch prediction information for in-flight fetch packets. It maintains a queue of fetch targets along with prediction metadata to allow branch recovery and provide PC information to the execution units.

Key functions of the FTQ include:

1.   Storing fetch PC values and branch prediction metadata
2.   Providing PC values to the execution units for branch target calculation
3.   Enabling quick redirection on branch misprediction
4.   Supporting branch prediction updates from the backend

Sources: [src/main/scala/ifu/fetch-target-queue.scala 98-102](https://github.com/tenstorrent/riscv-ocelot/blob/920fd904/src/main/scala/ifu/fetch-target-queue.scala#L98-L102)[src/main/scala/ifu/fetch-target-queue.scala 141-202](https://github.com/tenstorrent/riscv-ocelot/blob/920fd904/src/main/scala/ifu/fetch-target-queue.scala#L141-L202)

### FetchBuffer

The FetchBuffer serves as an interface between the frontend and the backend. It converts fetch packets into micro-ops that can be processed by the decode stage.

Sources: [src/main/scala/ifu/fetch-buffer.scala 29-32](https://github.com/tenstorrent/riscv-ocelot/blob/920fd904/src/main/scala/ifu/fetch-buffer.scala#L29-L32)[src/main/scala/ifu/fetch-buffer.scala 40-53](https://github.com/tenstorrent/riscv-ocelot/blob/920fd904/src/main/scala/ifu/fetch-buffer.scala#L40-L53)

## Pipeline Stages in Detail

### F0: Next PC Select Stage

The Next PC Select stage determines the next PC to fetch, selecting from several sources:

*   Normal incremented PC
*   Predicted branch/jump target from branch predictor
*   Redirect from branch misprediction
*   Exception/interrupt vector
*   Reset vector (on startup)

This stage initializes PC-related registers and sends the selected PC to the instruction cache.

Sources: [src/main/scala/ifu/frontend.scala 344-376](https://github.com/tenstorrent/riscv-ocelot/blob/920fd904/src/main/scala/ifu/frontend.scala#L344-L376)

### F1: ICache Access Stage

The ICache Access stage sends the PC from F0 to the instruction TLB for virtual-to-physical address translation and to the instruction cache to begin the cache access. This stage also performs early branch prediction, potentially redirecting the fetch path before the cache access completes.

Key operations:

*   Virtual address translation via TLB
*   Branch prediction for instructions in the current fetch group
*   Early redirection based on branch prediction

Sources: [src/main/scala/ifu/frontend.scala 377-436](https://github.com/tenstorrent/riscv-ocelot/blob/920fd904/src/main/scala/ifu/frontend.scala#L377-L436)

### F2: ICache Response Stage

The ICache Response stage receives the cache response and performs more accurate branch prediction based on the fetched instructions. If a branch is predicted taken, it will redirect the F0 and F1 stages.

This stage handles:

*   Processing the instruction cache response
*   Detecting exceptions from the TLB
*   Refining branch prediction with fetched instruction data
*   Generating redirects for predicted branches

Sources: [src/main/scala/ifu/frontend.scala 441-508](https://github.com/tenstorrent/riscv-ocelot/blob/920fd904/src/main/scala/ifu/frontend.scala#L441-L508)

### F3: Fetch Buffer Stage

The F3 stage processes fetched instructions, decodes branch instructions, and performs final branch prediction before sending the fetch packet to the FetchBuffer. It also handles:

*   Return address stack updates for call/return instructions
*   Branch mask generation for each instruction
*   Control flow instruction (CFI) identification and prediction
*   Global history updates based on branch predictions

Sources: [src/main/scala/ifu/frontend.scala 516-840](https://github.com/tenstorrent/riscv-ocelot/blob/920fd904/src/main/scala/ifu/frontend.scala#L516-L840)

### F4: Final Preparation Stage

The F4 stage represents the final stage before instructions are sent to the decode stage. It handles:

*   Shadow fallback (SFB) optimizations
*   Final fetch packet preparation
*   Enqueuing to the FetchBuffer and FetchTargetQueue
*   Branch predictor correction updates

Sources: [src/main/scala/ifu/frontend.scala 859-932](https://github.com/tenstorrent/riscv-ocelot/blob/920fd904/src/main/scala/ifu/frontend.scala#L859-L932)

## Instruction Flow

The flow of instructions through the fetch pipeline involves several data structures and state machines. Below is a diagram of how instructions move through the fetch pipeline:

Sources: [src/main/scala/ifu/frontend.scala 344-941](https://github.com/tenstorrent/riscv-ocelot/blob/920fd904/src/main/scala/ifu/frontend.scala#L344-L941)

## Branch Prediction and Speculation

The instruction fetch pipeline uses an aggressive branch prediction scheme to speculate beyond branches and maintain high instruction throughput. Several mechanisms work together to implement this:

### Branch Mask Tracking

Each instruction in the pipeline is tagged with a "branch mask" indicating which predicted branches it depends on. When a branch is resolved by the backend, it broadcasts its tag to all in-flight instructions:

*   If correctly predicted, instructions clear that bit in their branch mask
*   If mispredicted, instructions with the corresponding branch mask bit set are killed

Sources: [src/main/scala/ifu/frontend.scala 237-248](https://github.com/tenstorrent/riscv-ocelot/blob/920fd904/src/main/scala/ifu/frontend.scala#L237-L248)[docs/sections/execution-stages.rst 128-146](https://github.com/tenstorrent/riscv-ocelot/blob/920fd904/docs/sections/execution-stages.rst#L128-L146)

### Global History Tracking

The `GlobalHistory` class maintains branch prediction history, including:

*   History of recent branches (taken/not-taken)
*   Return address stack pointer
*   Information about branch outcomes seen in the current fetch packet

This history information is used by the branch predictor and is updated speculatively as branches are predicted, then corrected if a misprediction occurs.

Sources: [src/main/scala/ifu/frontend.scala 45-128](https://github.com/tenstorrent/riscv-ocelot/blob/920fd904/src/main/scala/ifu/frontend.scala#L45-L128)

### Recovery from Misprediction

When a branch is mispredicted, several actions occur:

1.   The frontend is redirected to the correct target PC
2.   The FetchBuffer is cleared
3.   The FetchTargetQueue provides the correct branch history state
4.   The pipeline is restarted from the correct path

The FetchTargetQueue plays a crucial role in branch misprediction recovery by storing the PC and branch history state for each fetch group.

Sources: [src/main/scala/ifu/frontend.scala 945-1006](https://github.com/tenstorrent/riscv-ocelot/blob/920fd904/src/main/scala/ifu/frontend.scala#L945-L1006)[src/main/scala/ifu/fetch-target-queue.scala 215-265](https://github.com/tenstorrent/riscv-ocelot/blob/920fd904/src/main/scala/ifu/fetch-target-queue.scala#L215-L265)

## Memory Systems Interface

The instruction fetch pipeline interacts with the memory system through several interfaces:

### TLB Interface

The Translation Lookaside Buffer (TLB) translates virtual addresses to physical addresses and checks permissions. Key operations include:

*   Address translation requests in F1 stage
*   Handling page faults and access exceptions
*   Supporting sfence instructions to flush the TLB

Sources: [src/main/scala/ifu/frontend.scala 387-404](https://github.com/tenstorrent/riscv-ocelot/blob/920fd904/src/main/scala/ifu/frontend.scala#L387-L404)

### ICache Interface

The instruction cache provides the actual instruction data. The frontend issues requests in F1 and receives responses in F2. Key interactions include:

*   Sending PC requests to the ICache
*   Receiving instruction data
*   Handling cache misses and refills

Sources: [src/main/scala/ifu/frontend.scala 370-372](https://github.com/tenstorrent/riscv-ocelot/blob/920fd904/src/main/scala/ifu/frontend.scala#L370-L372)[src/main/scala/ifu/frontend.scala 456-457](https://github.com/tenstorrent/riscv-ocelot/blob/920fd904/src/main/scala/ifu/frontend.scala#L456-L457)

## Conclusion

The BOOM instruction fetch pipeline is designed to maintain high throughput while handling complex branch prediction and speculation recovery. Its multi-stage design allows for early branch prediction and efficient instruction delivery to the backend, which is critical for the out-of-order execution model used in BOOM.

The integration of the FetchTargetQueue and branch prediction mechanisms allows the pipeline to speculate deeply while maintaining the ability to recover efficiently from mispredictions. This design represents a balance between aggressive speculation for performance and efficient recovery mechanisms to minimize the cost of incorrect speculation.

Dismiss
Refresh this wiki

Enter email to refresh
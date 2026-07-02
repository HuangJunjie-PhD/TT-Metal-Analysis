---
project: whisper
github: https://github.com/tenstorrent/whisper
deepwiki: https://deepwiki.com/tenstorrent/whisper/7-io-and-interrupt-architecture
---

# I/O and Interrupt Architecture

Relevant source files
*   [CsRegs.cpp](https://github.com/tenstorrent/whisper/blob/733fd921/CsRegs.cpp)
*   [CsRegs.hpp](https://github.com/tenstorrent/whisper/blob/733fd921/CsRegs.hpp)
*   [CsrFields.hpp](https://github.com/tenstorrent/whisper/blob/733fd921/CsrFields.hpp)
*   [HartConfig.cpp](https://github.com/tenstorrent/whisper/blob/733fd921/HartConfig.cpp)
*   [System.cpp](https://github.com/tenstorrent/whisper/blob/733fd921/System.cpp)
*   [System.hpp](https://github.com/tenstorrent/whisper/blob/733fd921/System.hpp)
*   [iommu/Ats.hpp](https://github.com/tenstorrent/whisper/blob/733fd921/iommu/Ats.hpp)
*   [iommu/Iommu.cpp](https://github.com/tenstorrent/whisper/blob/733fd921/iommu/Iommu.cpp)
*   [iommu/Iommu.hpp](https://github.com/tenstorrent/whisper/blob/733fd921/iommu/Iommu.hpp)
*   [iommu/tests/test_iotinval_commands.cpp](https://github.com/tenstorrent/whisper/blob/733fd921/iommu/tests/test_iotinval_commands.cpp)

## Overview

This page documents Whisper's I/O and interrupt subsystems. It covers the interrupt controllers (ACLINT, IMSIC, APLIC), the IOMMU for device address translation and isolation, and the memory-mapped device infrastructure including UARTs and block devices.

For information about CSR-level interrupt handling and privilege modes, see [Control and Status Registers (CSRs)](https://deepwiki.com/tenstorrent/whisper/3.1-control-and-status-registers-(csrs)). For memory protection and PMP/PMA mechanisms, see [Physical Memory and Protection](https://deepwiki.com/tenstorrent/whisper/4.1-physical-memory-and-protection). For hypervisor interrupt virtualization, see [Hypervisor Extension (H)](https://deepwiki.com/tenstorrent/whisper/5.2-hypervisor-extension-(h)).

* * *

## Interrupt Architecture Overview

Whisper implements the RISC-V Advanced Interrupt Architecture (AIA) with support for message-signaled interrupts (MSI), wired interrupts, and timer/software interrupts. The interrupt subsystem consists of three main controllers that work together to deliver interrupts to harts.

### Interrupt Flow Diagram

**Sources:**[System.hpp 24-33](https://github.com/tenstorrent/whisper/blob/733fd921/System.hpp#L24-L33)[CsRegs.cpp 175-224](https://github.com/tenstorrent/whisper/blob/733fd921/CsRegs.cpp#L175-L224)[CsRegs.cpp 626-654](https://github.com/tenstorrent/whisper/blob/733fd921/CsRegs.cpp#L626-L654)

* * *

## ACLINT: Core Local Interrupts

ACLINT (Advanced Core Local Interruptor) provides machine-level timer interrupts (MTIP) and software interrupts (MSIP/SSIP). It is a simplified interrupt controller for basic timer and inter-processor interrupts.

### ACLINT Configuration

ACLINT devices are configured through the JSON configuration file and mapped into the system memory space. The configuration specifies separate regions for MSWI (machine software interrupts), SSWI (supervisor software interrupts), and MTIMER (machine timers).

**Configuration Parameters:**

*   `mswi_addr`: Base address for machine software interrupt region
*   `sswi_addr`: Base address for supervisor software interrupt region
*   `mtimer_addr`: Base address for machine timer region
*   `per_hart`: Spacing between per-hart registers

**CSR Integration:**

The ACLINT controller updates interrupt pending bits directly in the `MIP` CSR:

*   **MIP.MTIP** (bit 7): Machine timer interrupt pending
*   **MIP.MSIP** (bit 3): Machine software interrupt pending
*   **MIP.SSIP** (bit 1): Supervisor software interrupt pending (if S-mode enabled)

The SSTC extension (Supervisor Timer Compare) allows supervisor-level timer interrupts through the `STIMECMP` CSR, which is managed by ACLINT in coordination with the `MENVCFG.STCE` bit.

**Sources:**[HartConfig.cpp 2230-2299](https://github.com/tenstorrent/whisper/blob/733fd921/HartConfig.cpp#L2230-L2299)[CsRegs.cpp 783-855](https://github.com/tenstorrent/whisper/blob/733fd921/CsRegs.cpp#L783-L855)

* * *

## IMSIC: Incoming MSI Controller

The IMSIC (Incoming Message-Signaled Interrupt Controller) implements the AIA specification for MSI delivery to harts. Each hart has dedicated machine-mode and supervisor-mode interrupt files for receiving MSIs.

### IMSIC Architecture

### IMSIC Registers and Files

Each IMSIC interrupt file contains multiple indirect registers accessed through `MISELECT/MIREG` (machine) or `SISELECT/SIREG` (supervisor):

**Indirect Registers (via MISELECT/SISELECT):**

*   `eidelivery` (0x70): Interrupt delivery enable
*   `eithreshold` (0x72): Interrupt priority threshold
*   `eip[0-63]` (0x80-0xBF): Interrupt pending bits
*   `eie[0-63]` (0xC0-0xFF): Interrupt enable bits

**Direct CSRs:**

*   `MTOPEI`/`STOPEI`: Returns the highest-priority pending-and-enabled interrupt ID in bits [26:16] and [10:0]. Reading this CSR also claims the interrupt, clearing the corresponding pending bit.
*   `MTOPI`/`STOPI`: Read-only version that does not claim the interrupt.

**Memory-Mapped MSI Target:**

Devices write to IMSIC interrupt files to deliver MSIs:

*   Machine file address: `mbase + hartid * mstride`
*   Supervisor file address: `sbase + hartid * sstride`
*   Guest file address: `gbase + (hartid * gstride) + (guest_index * 0x1000)`

**Configuration:**

The IMSIC is configured through the system JSON file with `imsic` object:

*   `machine_base`: Base address for machine-mode interrupt files
*   `machine_stride`: Offset between consecutive hart machine files
*   `supervisor_base`: Base address for supervisor-mode interrupt files
*   `supervisor_stride`: Offset between consecutive hart supervisor files
*   `guests`: Number of guest interrupt files per hart (for hypervisor extension)
*   `guest_base`: Base address for guest interrupt files
*   `guest_stride`: Offset between harts for guest files
*   `ids_per_file`: Number of interrupt IDs per file (typically 63 or 255)

**Sources:**[System.hpp 298-312](https://github.com/tenstorrent/whisper/blob/733fd921/System.hpp#L298-L312)[CsRegs.cpp 528-583](https://github.com/tenstorrent/whisper/blob/733fd921/CsRegs.cpp#L528-L583)[CsRegs.cpp 626-655](https://github.com/tenstorrent/whisper/blob/733fd921/CsRegs.cpp#L626-L655)[HartConfig.cpp 2301-2362](https://github.com/tenstorrent/whisper/blob/733fd921/HartConfig.cpp#L2301-L2362)

* * *

## APLIC: Platform-Level Interrupt Controller

APLIC (Advanced Platform-Level Interrupt Controller) manages wired interrupt sources from platform devices. It can deliver interrupts either as wired signals to hart interrupt pins or by forwarding them as MSIs to the IMSIC.

### APLIC Modes

APLIC operates in one of two modes:

1.   **MSI Delivery Mode**: Interrupts are converted to MSI writes targeting IMSIC files
2.   **Direct Mode**: Interrupts are wired directly to external interrupt pins (SEIP/MEIP)

The mode is configured through the `domaincfg` register and can be set per interrupt domain (machine or supervisor).

### APLIC Internal Structure

### APLIC Register Interface

APLIC provides a memory-mapped register interface for interrupt configuration and control:

**Domain Configuration:**

*   `domaincfg` (0x0000): Domain configuration including delivery mode (DM bit)

**Per-Source Registers (indexed by source ID):**

*   `sourcecfg[i]` (0x0004 + i*4): Source configuration (delegate bit, trigger mode)
*   `target[i]` (0x3004 + i*4): Target hart and interrupt ID for MSI mode
*   `setip[i]` (0x1C00 + i*4): Set interrupt pending (software injection)
*   `setipnum` (0x1CDC): Set pending by interrupt number
*   `in_clrip[i]` (0x1D00 + i*4): Clear interrupt pending
*   `clripnum` (0x1DDC): Clear pending by interrupt number
*   `setie[i]` (0x1E00 + i*4): Set interrupt enable
*   `setienum` (0x1EDC): Enable by interrupt number
*   `clrie[i]` (0x1F00 + i*4): Clear interrupt enable
*   `clrienum` (0x1FDC): Disable by interrupt number

**MSI Generation:**

*   `genmsi` (0x3000): Generate MSI (write target address and data)

**IDC (Interrupt Delivery Control) per hart:**

*   `idelivery[i]` (0x4000 + i*32): Enable interrupt delivery to hart i
*   `iforce[i]` (0x4004 + i*32): Force external interrupt to hart i
*   `ithreshold[i]` (0x4008 + i*32): Interrupt priority threshold for hart i
*   `topi[i]` (0x4018 + i*32): Top pending interrupt for hart i
*   `claimi[i]` (0x401C + i*32): Claim top interrupt for hart i

**Auto-Forward to MSI:**

APLIC can be configured to automatically forward all interrupts as MSIs rather than using wired delivery. This is controlled by the `autoForwardViaMsi` flag in the System configuration:

`system.setAplicAutoForwardViaMsi(true);`
**Sources:**[System.hpp 247-252](https://github.com/tenstorrent/whisper/blob/733fd921/System.hpp#L247-L252)[HartConfig.cpp 2364-2434](https://github.com/tenstorrent/whisper/blob/733fd921/HartConfig.cpp#L2364-L2434)

* * *

## IOMMU: I/O Memory Management Unit

The IOMMU provides device address translation, isolation, and interrupt remapping for PCIe and other I/O devices. It implements the RISC-V IOMMU specification with support for device contexts, process contexts, and ATS (Address Translation Services).

### IOMMU Capabilities

The IOMMU supports multiple capabilities as reported in the `capabilities` register:

**Translation Modes:**

*   Sv32, Sv39, Sv48, Sv57 (first-stage virtual address translation)
*   Sv32x4, Sv39x4, Sv48x4, Sv57x4 (second-stage guest physical address translation)
*   Bare mode (pass-through, no translation)

**Features:**

*   `ats`: PCIe Address Translation Services support
*   `t2gpa`: Two-stage translation (first and second stage)
*   `msi_flat`: Flat MSI address space
*   `hpm`: Hardware Performance Monitoring counters
*   `pas`: Physical address size (bits 33:28)
*   `igs`: Interrupt generation support (MSI/WSI/Both)

**Sources:**[iommu/Iommu.hpp 49-86](https://github.com/tenstorrent/whisper/blob/733fd921/iommu/Iommu.hpp#L49-L86)

### IOMMU System Architecture

**Sources:**[iommu/Iommu.hpp 1-323](https://github.com/tenstorrent/whisper/blob/733fd921/iommu/Iommu.hpp#L1-L323)[iommu/Iommu.cpp 1-1442](https://github.com/tenstorrent/whisper/blob/733fd921/iommu/Iommu.cpp#L1-L1442)

### Device and Process Contexts

The IOMMU uses a two-level directory structure to locate per-device and per-process translation contexts.

#### Device Directory Table (DDT)

The Device Directory Table is rooted at the address specified in the `DDTP` register. The IOMMU walks the DDT using the device ID (DID) to locate a device context.

**DDTP Register Fields:**

*   `iommu_mode`: Directory depth (Off/Bare/Level1/Level2/Level3)
*   `ppn`: Physical page number of root directory table
*   `busy`: Set during directory table walk

**Device Context Structure (`DeviceContext`):**

*   `tc`: Translation control (enable bits, translation modes)
*   `iohgatp`: Second-stage (guest) page table root
*   `ta`: Translation attributes
*   `fsc`: First-stage context address (for process directory lookup)
*   `msiptp`: MSI page table pointer (for MSI address translation)
*   `msi_addr_mask`/`msi_addr_pattern`: MSI address filtering

**Sources:**[iommu/Iommu.hpp 99-127](https://github.com/tenstorrent/whisper/blob/733fd921/iommu/Iommu.hpp#L99-L127)[iommu/DeviceContext.hpp 1-198](https://github.com/tenstorrent/whisper/blob/733fd921/iommu/DeviceContext.hpp#L1-L198)

#### Process Directory Table (PDT)

If the device context has processes enabled (`tc.pdtv=1`), the IOMMU walks the Process Directory Table using the process ID (PID) to locate a process context.

**Process Context Structure (`ProcessContext`):**

*   `ta`: Translation attributes
*   `iosatp`: First-stage page table root (supervisor address translation)
*   `pscid`: Process soft-context ID (for TLB tagging)

**Sources:**[iommu/ProcessContext.hpp 1-113](https://github.com/tenstorrent/whisper/blob/733fd921/iommu/ProcessContext.hpp#L1-L113)

### Address Translation Flow

**Translation Request Structure:**

`struct IommuRequest {  unsigned devId;        // Device ID  bool hasProcId;        // Process ID valid?  unsigned procId;       // Process ID  uint64_t iova;         // IO virtual address  Ttype type;            // Transaction type  PrivilegeMode privMode;// Privilege mode  unsigned size;         // Size in bytes};`
**Sources:**[iommu/Iommu.hpp 361-404](https://github.com/tenstorrent/whisper/blob/733fd921/iommu/Iommu.hpp#L361-L404)[iommu/Iommu.cpp 600-895](https://github.com/tenstorrent/whisper/blob/733fd921/iommu/Iommu.cpp#L600-L895)

### Command Queue

The IOMMU command queue allows software to manage translation caches and coordinate with devices supporting ATS. Commands are written to the queue in memory and processed by the IOMMU.

**Command Queue CSRs:**

*   `CQB`: Command queue base (PPN and log2 size)
*   `CQH`: Command queue head (read-only, updated by IOMMU)
*   `CQT`: Command queue tail (written by software to submit commands)
*   `CQCSR`: Command queue control and status

**Command Types:**

| Command | Opcode | Function | Purpose |
| --- | --- | --- | --- |
| `IOTINVAL.VMA` | 1 | 0 | Invalidate first-stage TLB entries |
| `IOTINVAL.GVMA` | 1 | 1 | Invalidate second-stage TLB entries |
| `IOFENCE.C` | 2 | 0 | Command queue fence |
| `IODIR.INVAL_DDT` | 3 | 0 | Invalidate device directory cache |
| `IODIR.INVAL_PDT` | 3 | 1 | Invalidate process directory cache |
| `ATS.INVAL` | 4 | 0 | Send ATS invalidation to device |
| `ATS.PRGR` | 4 | 1 | Send page request group response |

**Command Processing:**

Commands are fetched from memory when software updates `CQT`. The IOMMU processes commands in order and updates `CQH` to indicate completion:

`void Iommu::processCommandQueue() {  while (cqh_ != cqt_) {    Command cmd = fetchCommandAtIndex(cqh_);    if (cmd.isIotinval())      processIotinvalCommand(cmd.iotinval);    else if (cmd.isIofence())      processIofenceCommand(cmd.iofence);    // ... other command types    cqh_ = (cqh_ + 1) % cqb_.capacity();  }}`
**Sources:**[iommu/Iommu.hpp 324-331](https://github.com/tenstorrent/whisper/blob/733fd921/iommu/Iommu.hpp#L324-L331)[iommu/Ats.hpp 1-298](https://github.com/tenstorrent/whisper/blob/733fd921/iommu/Ats.hpp#L1-L298)[iommu/Iommu.cpp 1160-1350](https://github.com/tenstorrent/whisper/blob/733fd921/iommu/Iommu.cpp#L1160-L1350)

### Fault Queue

When the IOMMU encounters a translation fault or other error, it records the fault in the fault queue and optionally generates an MSI to notify software.

**Fault Queue CSRs:**

*   `FQB`: Fault queue base (PPN and log2 size)
*   `FQH`: Fault queue head (written by software to consume faults)
*   `FQT`: Fault queue tail (read-only, updated by IOMMU)
*   `FQCSR`: Fault queue control and status

**Fault Record Structure (`FaultRecord`):**

`struct FaultRecord {  uint64_t iotval2;     // Additional fault info  uint64_t iotval;      // Faulting address  uint64_t reserved0;  uint32_t custom;      // Custom fault code  Ttype ttyp;           // Transaction type  uint32_t did;         // Device ID  uint32_t pid;         // Process ID  bool pv;              // Process ID valid  bool priv;            // Privileged access  CauseCode cause;      // Fault cause};`
**Fault Causes:**

*   `INST_ADDR_MISALIGNED`: Instruction fetch misaligned
*   `INST_ACCESS_FAULT`: Instruction fetch access fault
*   `ILLEGAL_INST`: Illegal instruction
*   `LOAD_ADDR_MISALIGNED`: Load misaligned
*   `LOAD_ACCESS_FAULT`: Load access fault
*   `STORE_ACCESS_FAULT`: Store/AMO access fault
*   `INST_PAGE_FAULT`: Instruction page fault
*   `LOAD_PAGE_FAULT`: Load page fault
*   `STORE_PAGE_FAULT`: Store/AMO page fault
*   `INST_GUEST_PAGE_FAULT`: Instruction guest-page fault
*   `LOAD_GUEST_PAGE_FAULT`: Load guest-page fault
*   `STORE_GUEST_PAGE_FAULT`: Store/AMO guest-page fault

**Sources:**[iommu/FaultQueue.hpp 1-156](https://github.com/tenstorrent/whisper/blob/733fd921/iommu/FaultQueue.hpp#L1-L156)[iommu/Iommu.cpp 896-950](https://github.com/tenstorrent/whisper/blob/733fd921/iommu/Iommu.cpp#L896-L950)

### Page Request Queue and ATS

For devices supporting PCIe ATS (Address Translation Services), the IOMMU provides a page request queue to handle page faults from devices.

**Page Request Queue CSRs:**

*   `PQB`: Page request queue base
*   `PQH`: Page request queue head (written by software)
*   `PQT`: Page request queue tail (read-only, updated by IOMMU)
*   `PQCSR`: Page request queue control and status

**Page Request Structure:**

`union PageRequest {  struct {    uint64_t pid;       // Process ID    uint64_t pv;        // PID valid    uint64_t priv;      // Privilege mode    uint64_t exec;      // Execute request    uint64_t did;       // Device ID    uint64_t r;         // Read permission    uint64_t w;         // Write permission    uint64_t l;         // Last in group    uint64_t prgi;      // Page request group index    uint64_t address;   // Faulting address  } bits;};`
**ATS Protocol Flow:**

1.   Device experiences page fault, sends Page Request message to IOMMU
2.   IOMMU records request in page request queue
3.   IOMMU generates MSI to notify software
4.   Software services page (makes it resident)
5.   Software issues `ATS.PRGR` command via command queue
6.   IOMMU sends Page Request Group Response to device
7.   Device retries original transaction

**Sources:**[iommu/Iommu.hpp 332-357](https://github.com/tenstorrent/whisper/blob/733fd921/iommu/Iommu.hpp#L332-L357)[iommu/Ats.hpp 29-53](https://github.com/tenstorrent/whisper/blob/733fd921/iommu/Ats.hpp#L29-L53)[iommu/Iommu.cpp 1351-1395](https://github.com/tenstorrent/whisper/blob/733fd921/iommu/Iommu.cpp#L1351-L1395)

### MSI Page Table

The IOMMU can translate MSI writes from devices to ensure they target valid IMSIC interrupt files. This is controlled through the MSI Page Table (MSIPTP) in the device context.

**MSI Translation:**

When a device writes an MSI, the IOMMU:

1.   Checks if MSI address matches `msi_addr_pattern` (masked by `msi_addr_mask`)
2.   If MSIPTP is valid, walks the MSI page table to translate the address
3.   Validates that the translated address targets a valid IMSIC interrupt file
4.   Forwards the MSI write to the translated address

**Sources:**[iommu/MsiPte.hpp 1-90](https://github.com/tenstorrent/whisper/blob/733fd921/iommu/MsiPte.hpp#L1-L90)[iommu/Iommu.cpp 951-1040](https://github.com/tenstorrent/whisper/blob/733fd921/iommu/Iommu.cpp#L951-L1040)

### IOMMU Register Interface

The IOMMU provides a memory-mapped register interface (typically at `0x30000000`):

**Capability and Control:**

*   `capabilities` (0x000): IOMMU capabilities (read-only)
*   `fctl` (0x008): Features control
*   `ddtp` (0x010): Device directory table pointer
*   `ipsr` (0x054): Interrupt pending and status

**Queue Configuration:**

*   `cqb/cqh/cqt/cqcsr` (0x018-0x048): Command queue
*   `fqb/fqh/fqt/fqcsr` (0x028-0x04C): Fault queue
*   `pqb/pqh/pqt/pqcsr` (0x038-0x050): Page request queue

**Performance Monitoring:**

*   `iocountinh` (0x05C): Counter inhibit
*   `iocountovf` (0x058): Counter overflow
*   `iohpmcycles` (0x060): Cycle counter
*   `iohpmctr[1-31]` (0x068-0x15F): Performance counters
*   `iohpmevt[3-31]` (0x160-0x257): Event selectors

**Debug Translation:**

*   `tr_req_iova` (0x258): Translation request IOVA
*   `tr_req_ctl` (0x260): Translation request control
*   `tr_response` (0x268): Translation response

**MSI Configuration:**

*   `icvec` (0x2F8): Interrupt vector configuration
*   MSI configuration table (0x300-0x3FF): 16 MSI configuration entries

**Sources:**[iommu/Iommu.cpp 38-154](https://github.com/tenstorrent/whisper/blob/733fd921/iommu/Iommu.cpp#L38-L154)[iommu/Iommu.cpp 217-289](https://github.com/tenstorrent/whisper/blob/733fd921/iommu/Iommu.cpp#L217-L289)

* * *

## Memory-Mapped I/O Devices

Whisper provides an extensible framework for memory-mapped I/O devices. Devices implement the `IoDevice` interface and register with the memory subsystem to intercept reads and writes to their address ranges.

### IoDevice Interface

**IoDevice Interface Methods:**

`class IoDevice {  virtual bool read(uint64_t addr, unsigned size, uint64_t& data) = 0;  virtual bool write(uint64_t addr, unsigned size, uint64_t data) = 0;  virtual std::string type() const = 0;  virtual void enable() { enabled_ = true; }  virtual void disable() { enabled_ = false; }};`
**Device Registration:**

Devices are created by the `System` class and registered with the `Memory` object:

`memory_->registerIoDevice(devicePtr);ioDevs_.push_back(devicePtr);`
**Sources:**[System.cpp 103-208](https://github.com/tenstorrent/whisper/blob/733fd921/System.cpp#L103-L208)[Memory.cpp 1180-1220](https://github.com/tenstorrent/whisper/blob/733fd921/Memory.cpp#L1180-L1220)

### UART Devices

Whisper supports two UART implementations for console I/O.

#### UART 8250

The `Uart8250` class implements a 16550-compatible UART with interrupt support. It can be configured with different register shifts and connected to various I/O channels.

**UART 8250 Registers:**

| Offset | Name | Description |
| --- | --- | --- |
| 0x00 | RBR/THR/DLL | Receive/Transmit Buffer, Divisor Latch Low |
| 0x01 | IER/DLH | Interrupt Enable, Divisor Latch High |
| 0x02 | IIR/FCR | Interrupt ID, FIFO Control |
| 0x03 | LCR | Line Control |
| 0x04 | MCR | Modem Control |
| 0x05 | LSR | Line Status |
| 0x06 | MSR | Modem Status |
| 0x07 | SCR | Scratch |

**I/O Channels:**

The UART can be connected to different channels:

*   `stdio`: Standard input/output (FDChannel with stdin/stdout)
*   `pty`: Pseudo-TTY for terminal emulation
*   `unix:<path>`: Unix domain socket server
*   Multiple channels can be combined with `;` separator for forked output

**Interrupt Generation:**

The UART can generate interrupts through APLIC when:

*   Received data is available (RDA)
*   Transmit holding register is empty (THRE)
*   Receiver line status change (RLS)
*   Modem status change (MSR)

**Configuration:**

`system.defineUart("uart8250", addr, size, iid, "stdio", regShift);`
Where:

*   `addr`: Base address of UART registers
*   `size`: Size of register region
*   `iid`: Interrupt identity (APLIC source ID)
*   Channel type: `"stdio"`, `"pty"`, or `"unix:<path>"`
*   `regShift`: Register spacing (0=byte, 2=word, etc.)

**Sources:**[System.cpp 103-208](https://github.com/tenstorrent/whisper/blob/733fd921/System.cpp#L103-L208)[Uart8250.hpp 1-167](https://github.com/tenstorrent/whisper/blob/733fd921/Uart8250.hpp#L1-L167)

#### UART SiFive (UARTSF)

The `Uartsf` class implements the SiFive UART with a simpler register set:

**UARTSF Registers:**

| Offset | Name | Description |
| --- | --- | --- |
| 0x00 | txdata | Transmit data |
| 0x04 | rxdata | Receive data |
| 0x08 | txctrl | Transmit control |
| 0x0C | rxctrl | Receive control |
| 0x10 | ie | Interrupt enable |
| 0x14 | ip | Interrupt pending |
| 0x18 | div | Baud rate divisor |

**Sources:**[Uartsf.hpp 1-76](https://github.com/tenstorrent/whisper/blob/733fd921/Uartsf.hpp#L1-L76)

### Block Devices

Whisper supports virtio-blk devices for block I/O, typically used for disk emulation.

**VirtIO Block Device:**

The `Virtio::Blk` class implements the VirtIO block device specification with:

*   Virtqueue for request/response handling
*   Support for read, write, and flush operations
*   Backing file for persistent storage
*   Interrupt generation via APLIC or MSI

**Configuration:**

Block devices are configured through the PCI subsystem as virtio-pci devices:

`auto pci = std::make_shared<Pci>(mem);auto blkDev = std::make_shared<Virtio::Blk>(pci, backingFile);pci->addDevice(deviceId, blkDev);`
**Sources:**[pci/virtio/Blk.hpp 1-120](https://github.com/tenstorrent/whisper/blob/733fd921/pci/virtio/Blk.hpp#L1-L120)

### PCI Infrastructure

Whisper provides basic PCI infrastructure for device enumeration and configuration:

**PCI Configuration Space:**

Each PCI device has a 256-byte configuration space with:

*   Vendor ID / Device ID (0x00)
*   Command / Status (0x04)
*   Class Code / Revision (0x08)
*   BARs (Base Address Registers) (0x10-0x24)
*   Interrupt Line / Interrupt Pin (0x3C)

**PCI Memory Mapping:**

Devices map their registers and buffers through BARs, which the PCI subsystem translates to physical addresses:

`pci->configureBar(deviceId, barIndex, baseAddr, size);`
**Sources:**[pci/Pci.hpp 1-200](https://github.com/tenstorrent/whisper/blob/733fd921/pci/Pci.hpp#L1-L200)

* * *

## System Configuration Example

Here is an example JSON configuration combining interrupt controllers, IOMMU, and I/O devices:

`{  "imsic": {    "machine_base": "0x28000000",    "machine_stride": "0x1000",    "supervisor_base": "0x29000000",     "supervisor_stride": "0x1000",    "guests": 5,    "guest_base": "0x2A000000",    "guest_stride": "0x8000",    "ids_per_file": 255  },    "aplic": {    "addr": "0x0c000000",    "sources": 1024,    "mmode": true,    "smode": true  },    "aclint": {    "mswi_addr": "0x02000000",    "sswi_addr": "0x02003000",    "mtimer_addr": "0x02004000",    "per_hart": "0x1000"  },    "iommu": {    "addr": "0x30000000",    "size": "0x10000",    "capabilities": {      "sv39": true,      "sv48": true,      "ats": true,      "msi_flat": true,      "pas": 56    }  },    "uart": [    {      "type": "uart8250",      "addr": "0x10000000",      "size": "0x1000",      "interrupt_id": 10,      "channel": "stdio"    }  ]}`
**Sources:**[HartConfig.cpp 2230-2434](https://github.com/tenstorrent/whisper/blob/733fd921/HartConfig.cpp#L2230-L2434)

* * *

## Summary

Whisper's I/O and interrupt architecture provides a comprehensive implementation of RISC-V interrupt delivery mechanisms (ACLINT, IMSIC, APLIC) and device virtualization (IOMMU). The modular design allows for flexible configuration of interrupt controllers, device address translation, and memory-mapped I/O devices including UARTs and block storage. The IOMMU provides full support for PCIe ATS, MSI translation, and two-stage address translation for guest OS and hypervisor scenarios.

Dismiss
Refresh this wiki

Enter email to refresh
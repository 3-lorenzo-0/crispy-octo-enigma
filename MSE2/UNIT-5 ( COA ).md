
|                                        |                                                                                                                                                                                                                            |
| -------------------------------------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| **Question**                           | **Answer**                                                                                                                                                                                                                 |
| Define parallel processing.            | Parallel processing is the simultaneous execution of multiple instructions or tasks using more than one processing unit to increase computational throughput and speed.                                                    |
| What is pipelining?                    | Pipelining is a technique where instruction execution is divided into discrete stages, and multiple instructions are overlapped — each in a different stage simultaneously — to increase throughput.                       |
| What is instruction-level parallelism? | Instruction-level parallelism (ILP) is the degree to which instructions in a program can be executed simultaneously without affecting the correct result — exploited by pipelining and superscalar processors.             |
| Define throughput in pipelining.       | Pipeline throughput is the number of instructions completed per unit time. In an ideal pipeline, throughput is 1 instruction per clock cycle (after the pipeline fills).                                                   |
| What is speedup in pipelining?         | Speedup is the ratio of execution time without pipelining to execution time with pipelining. Ideal speedup for a k-stage pipeline executing n instructions is n×k / (k+n-1), approaching k for large n.                    |
| What is a multiprocessor system?       | A multiprocessor system is a computer with two or more processors that share memory and I/O resources and cooperate to execute programs faster.                                                                            |
| Define SMP.                            | Symmetric Multiprocessor (SMP) is a multiprocessor system where all processors are identical, run the same OS, and have equal access to shared memory, with load balancing handled by the OS.                              |
| What is a pipeline hazard?             | A pipeline hazard is any condition that prevents the next instruction from executing in the next clock cycle, reducing pipeline efficiency. Types: data hazard, control hazard, structural hazard.                         |
| List two types of pipeline hazards.    | Data hazard (instruction depends on result of a prior incomplete instruction) and Control hazard (branch instruction — next PC not known until branch is resolved).                                                        |
| What is a structural hazard?           | A structural hazard occurs when two instructions in the pipeline require the same hardware resource simultaneously — e.g., two instructions both needing the memory unit at the same time.                                 |
| What is a data hazard?                 | A data hazard occurs when an instruction needs a value (from a register or memory) that has not yet been produced or written by a preceding instruction still in the pipeline.                                             |
| What is a control hazard?              | A control hazard (branch hazard) occurs because a branch instruction changes the program counter, but the new PC value is not known until the branch is evaluated — instructions fetched in the meantime may be incorrect. |

## Classification of Parallel Structures

Parallel structures are classified based on **how processors access memory** and **how they communicate**. There are **four main categories**:

---

### 1. Shared-Memory Multiprocessor (General Model) — Fig 2.1

All processors are connected to **multiple memory modules** through an interconnection network, forming a **single globally shared address space**. Any processor can access any memory location directly.

```
┌──────┐ ┌──────┐ ┌──────┐ ┌──────┐
│ CPU1 │ │ CPU2 │ │ CPU3 │ │ CPU4 │
└──┬───┘ └──┬───┘ └──┬───┘ └──┬───┘
   └─────────┴─INTERCONNECTION-┴────┘
   ┌──────┐ ┌──────┐ ┌──────┐ ┌──────┐
   │ MEM1 │ │ MEM2 │ │ MEM3 │ │ MEM4 │
   └──────┘ └──────┘ └──────┘ └──────┘
     ↑── Globally shared address space ──↑
```

- **Memory Access:** Any processor can read/write any memory module directly through the interconnection network.
- **Communication:** Implicit — through shared variables. No explicit message passing required.
- **Scalability:** Limited — memory contention across the network degrades performance as processors increase.
- **Programming Complexity:** Simple — standard threading models (OpenMP) apply. No need to manage data distribution.

---

### 2. UMA — Uniform Memory Access — Fig 2.2

A specific type of shared-memory system where **all processors experience identical (uniform) memory access latency**, regardless of which processor accesses which memory module. Typically implemented using a shared bus or crossbar switch.

```
┌──────┐  ┌──────┐  ┌──────┐
│ CPU1 │  │ CPU2 │  │ CPU3 │
└──┬───┘  └──┬───┘  └──┬───┘
   └──────────┴──────────┘
        SHARED BUS / CROSSBAR
   ┌──────────────────────┐
   │     SHARED MEMORY    │
   │   (same latency for  │
   │      all CPUs)       │
   └──────────────────────┘
```

- **Memory Access:** Completely symmetric — every processor sees the same memory latency at all times.
- **Communication:** Through shared variables; no message passing needed.
- **Scalability:** Poor — the shared bus becomes a severe bottleneck beyond **8–16 processors**.
- **Programming Complexity:** Simple. Programmers need not worry about data placement since all memory is equidistant.

---

### 3. NUMA — Non-Uniform Memory Access — Fig 2.3

Each processor has its own **local memory** (fast access). It can also access **remote memory** of other processors through an interconnect, but at **higher latency**. Access time is therefore non-uniform — it depends on whether the memory is local or remote.

```
┌─────────────────┐         ┌─────────────────┐
│ CPU1            │         │ CPU2            │
│ ↓ FAST (local)  │         │ ↓ FAST (local)  │
│ Local Mem 1     │◄─SLOW──►│ Local Mem 2     │
└────────┬────────┘         └────────┬────────┘
         └──────── INTERCONNECT ─────┘
```

- **Memory Access:** Local memory = fast; remote memory = slow (must traverse the interconnect).
- **Communication:** Shared variables, but programs must be **NUMA-aware** — data should be placed in the local memory of the processor using it to avoid remote access penalties.
- **Scalability:** Good — no single shared bus bottleneck. Scales to **hundreds of processors** (64–1024 cores in large servers).
- **Programming Complexity:** Moderate. The model resembles shared memory but requires NUMA-aware memory allocation for good performance.

---

### 4. Distributed-Memory System — Fig 2.4

Each processor has its own **completely private memory**. Processors **cannot directly access each other's memory**. All data sharing must happen through **explicit message passing** over an interconnection network.

```
┌─────────────┐    send/recv    ┌─────────────┐
│ CPU1 | Mem1 │◄───── MSG ─────►│ CPU2 | Mem2 │
└──────┬──────┘                 └──────┬──────┘
       └──────── NETWORK ──────────────┘
```

- **Memory Access:** Strictly private — a processor can only directly access its own local memory. Remote data must be explicitly requested via messages.
- **Communication:** Explicit **send() and receive()** calls using the Message Passing Interface (MPI). All inter-processor data exchange must be deliberately coded.
- **Scalability:** Excellent — no globally shared resource, so the system scales to **thousands of nodes** (supercomputers, cloud platforms, Beowulf clusters).
- **Programming Complexity:** Complex. Developers must manage data distribution, communication, synchronization, and avoid issues like deadlock. Requires expertise in MPI programming.

---



## Architecture of a Symmetric Multiprocessor (SMP)

---

### Definition

**Symmetric Multiprocessor (SMP)** is a multiprocessor system where all processors are **identical**, each runs under the **same operating system**, and all have **equal access** to shared memory and I/O resources. There is no master processor — all are peers.

---

### Figure 4.1 — SMP Architecture

```
┌───────────┐ ┌───────────┐ ┌───────────┐ ┌───────────┐
│   CPU 1   │ │   CPU 2   │ │   CPU 3   │ │   CPU 4   │
│ (L1 Cache)│ │ (L1 Cache)│ │ (L1 Cache)│ │ (L1 Cache)│
└─────┬─────┘ └─────┬─────┘ └─────┬─────┘ └─────┬─────┘
      │              │              │              │
──────┴──────────────┴──────────────┴──────────────┴──────
                     SHARED SYSTEM BUS
──────┬──────────────┬──────────────┬──────────────┬──────
      │              │              │              │
┌─────┴──────┐ ┌─────┴──────┐ ┌────┴──────┐ ┌────┴─────┐
│  SHARED    │ │  SHARED    │ │  SHARED   │ │   I/O    │
│  MEMORY 1  │ │  MEMORY 2  │ │  MEMORY 3 │ │SUBSYSTEM │
└────────────┘ └────────────┘ └───────────┘ └──────────┘
```

All CPUs connect to all memory modules and I/O through a **single shared system bus**. Every processor has equal, symmetric access to every resource.

---

### Six Key Properties of SMP

**1. Identical Processors** All CPUs are of the same type, speed, and capability. Any processor can execute any process — they are fully interchangeable. There is no designated master or slave processor; all participate equally in computation.

**2. Shared Memory** A single globally shared memory is accessible by all processors with **uniform access time** (UMA model). All processors see the same memory address space, meaning a variable written by CPU1 can be read by CPU2 directly without any message passing.

**3. Single Operating System Instance** One copy of the OS runs across all processors. The OS kernel manages all CPUs, schedules processes onto any available processor, handles interrupts from any CPU, and allocates resources system-wide. This gives a **single-system image** — the system appears as one powerful computer rather than many separate ones.

**4. Load Balancing** The OS scheduler automatically distributes processes across all available CPUs. If one processor becomes idle while another is overloaded, the OS migrates work to balance the load. This happens transparently without programmer intervention.

**5. Cache Coherence** Each CPU has its own private cache (L1, sometimes L2). When multiple processors cache the same memory location and one modifies it, the other caches hold **stale (outdated) data**. A cache coherence protocol (such as MESI) is used to ensure all processors always see consistent, up-to-date values.

**6. Shared I/O** All processors share the same I/O devices — disks, network cards, peripherals. Any processor can initiate an I/O operation. The OS coordinates access to prevent conflicts.

**MESI Cache Coherence Protocol**
**Simple Example — CPU1 and CPU2 sharing variable X:**

**Step 1 — CPU1 reads X**

> CPU1 fetches X = 10 from memory. State → **Exclusive (E)** — only CPU1 has it.

**Step 2 — CPU2 also reads X**

> CPU2 fetches X = 10 from memory. Both caches now hold X = 10. State → **Shared (S)** for both CPU1 and CPU2.

**Step 3 — CPU1 writes X = 20**

> CPU1 sends an **Invalidate signal** on the bus. CPU2 receives it → its copy is marked **Invalid (I)** → stale! CPU1's state → **Modified (M)**, value = 20.

**Step 4 — CPU2 reads X again**

> CPU2 sees its copy is **Invalid** → requests X from bus. CPU1 writes X = 20 back to memory first. CPU2 fetches the updated X = 20. ✓ Correct value!

| State         | Meaning                                                                   |
| ------------- | ------------------------------------------------------------------------- |
| **Modified**  | Cache has modified copy; memory is stale. Only this cache has valid copy. |
| **Exclusive** | Cache has clean copy; no other cache has it.                              |
| ** Shared**   | Multiple caches have clean, identical copies.                             |
| **Invalid**   | This cache line is stale or absent. Must refetch.                         |

|   |   |
|---|---|
|**Advantages**|**Limitations**|
|Simple programming model — shared memory, standard threads (pthreads, OpenMP)|Memory bus bottleneck — all CPUs share one bus, limiting scalability|
|Load balancing handled automatically by OS|Cache coherence overhead grows with processor count|
|Familiar single-system image — one OS manages all CPUs|Typically limited to 2–32 processors in practice|
|Any CPU can run any process — flexible workload distribution|Adding more than ~32 processors shows diminishing returns due to bus contention|

## Pipeline Hazards

A **pipeline hazard** is any condition that prevents the next instruction from executing in its expected clock cycle, reducing pipeline efficiency and potentially causing incorrect results.

---

### Type 1 — Data Hazard

**Definition:** Occurs when an instruction depends on the result of a previous instruction that has not yet completed its write-back stage.

**Cause:** Instructions execute in overlapping stages, so a result written in the WB stage may not be available when the next instruction needs it in the ID stage.

**Three sub-types:**

|Type|Full Name|Description|
|---|---|---|
|**RAW**|Read After Write|Instruction J reads a register that instruction I hasn't written yet. Most common.|
|**WAR**|Write After Read|Instruction J writes a register before instruction I has read it. Rare in simple pipelines.|
|**WAW**|Write After Write|Two instructions write to the same register; second write must happen after first. Rare in in-order pipelines.|

**RAW Example (Figure 8.1):**

```
I1: ADD R1, R2, R3   → R1 written in WB stage (cycle 5)
I2: SUB R4, R1, R5   → R1 needed in ID stage (cycle 3) — NOT READY!
```

**Timing Diagram:**

```
Cycle:    1    2    3    4    5
I1: ADD  [IF] [ID] [EX] [MEM][WB] ← R1 written here
I2: SUB       [IF] [ID]  ↑        ← needs R1 here → HAZARD!
```

**Effect:** Without hazard handling, I2 read the old incorrect value of R1, producing wrong results. Pipeline must stall or use forwarding.

---

### Type 2 — Control Hazard (Branch Hazard)

**Definition:** Occurs when the pipeline encounters a branch instruction and does not know the next instruction address until the branch is fully evaluated.

**Cause:** The pipeline fetches the next sequential instructions before knowing whether the branch is taken or not. If the branch is taken, those fetched instructions are wrong and must be discarded.

**Branch Example (Figure 8.4):**

```
Addr 100: BEQ R1, R2, 200  → branch resolved in EX (cycle 3)
Addr 104: ADD R3, R4, R5   → fetched speculatively — WRONG
Addr 200: MUL R9, R10, R11 → actual branch target
```

**Timing Diagram:**

```
Cycle:        1    2    3    4    5
BEQ          [IF] [ID] [EX] ← branch resolved here
Addr 104:         [IF] [ID] [XX] ← FLUSHED
Addr 200:              [IF] [ID] [EX] ← correct fetch
```

**Effect:** If branch is taken, instructions at 104 and 108 must be **flushed** and the pipeline restarts from address 200 — causing a **branch penalty of 2 wasted cycles** in a 5-stage pipeline.

---

### Type 3 — Structural Hazard

**Definition:** Occurs when two instructions simultaneously require the same hardware resource and the hardware does not support concurrent access.

**Cause:** Limited hardware resources — for example, a single shared memory unit being needed by two different pipeline stages at the same time.

**Example:**

```
I1: LOAD R1  → in MEM stage → accessing Data Memory
I2: ADD R3   → in IF stage  → accessing Instruction Memory
Both need memory simultaneously → CONFLICT!
```

**Timing Diagram:**

```
Cycle:       1    2    3    4    5
I1: LOAD    [IF] [ID] [EX] [MEM][WB]
I2: ADD          [IF] [ID] [---][EX] ← stall (bubble inserted)
``````

If there is only **one shared memory unit**, both instructions cannot access it simultaneously — a conflict occurs.

**Effect:** One instruction must stall for one cycle, waiting for the resource to become free, introducing a bubble in the pipeline and reducing throughput.

**Solution:** Use separate **Instruction Cache (I-Cache)** and **Data Cache (D-Cache)** — known as the Harvard architecture. Since both are independent units, the IF and MEM stages no longer compete for the same memory, eliminating this structural hazard entirely.

## Parallel Processing vs Pipelining — Comparison

|   |   |   |
|---|---|---|
|**Feature**|**Parallel Processing**|**Pipelining**|
|Definition|Multiple processors execute different tasks or instructions simultaneously|Single processor divides instruction execution into overlapping stages|
|Hardware needed|Multiple complete CPUs / processing units|Single CPU with multiple pipeline stages and pipeline registers|
|Type of parallelism|Task/process parallelism or data parallelism|Instruction-level parallelism (ILP)|
|Speedup source|Multiple instructions complete simultaneously in different processors|Multiple instructions overlap in different stages of the same processor|
|Scalability|Can scale to hundreds or thousands of processors|Limited by pipeline stages (typically 5–20 stages)|
|Hazards|Inter-processor communication latency, synchronisation|Data hazards, control hazards, structural hazards|
|Programming|Requires parallel programming (threads, MPI)|Transparent to programmer — handled by hardware|
|Examples|SMP servers, HPC clusters, GPU CUDA cores|Classic 5-stage RISC pipeline (MIPS, ARM), superscalar processors|

## Methods to Resolve Pipeline Hazards

---

### Data Hazard Resolution

#### 1. Operand Forwarding (Figure 8.2)

**Definition:** Instead of waiting for the result to be written back to the register file (WB stage), the result is forwarded directly from the pipeline register where it is available to the stage that needs it.

**Example:**

```
I1: ADD R1, R2, R3  → result available after EX stage (cycle 3)
I2: SUB R4, R1, R5  → needs R1 at EX stage (cycle 4)
```

**Timing Diagram:**

```
Cycle:   1    2    3    4    5    6    7
I1 ADD  [IF] [ID] [EX] [MEM][WB]
                   ↓ forward result directly
I2 SUB       [IF] [ID] [EX] [MEM][WB]
                        ↑
                   Receives R1 via forwarding path — No stall!
```

**How it works:** Multiplexers at ALU inputs select the result directly from EX/MEM or MEM/WB pipeline registers instead of waiting for the register file to be updated.

---

#### 2. Pipeline Stall / Bubble Insertion (Figure 8.3)

**Definition:** When forwarding cannot fully resolve a hazard (e.g. load-use hazard), the pipeline inserts a stall — a NOP (No Operation) bubble — to delay the dependent instruction by one cycle.

**Example:**

```
I1: LOAD R1, 100(R2) → data arrives from memory in MEM stage (cycle 4)
I2: ADD R3, R1, R4   → needs R1 at EX stage (cycle 4) — 1 cycle too early!
```

**Timing Diagram:**

```
Cycle:    1    2    3    4    5    6    7
I1 LOAD  [IF] [ID] [EX] [MEM][WB]
                         ↓ forward after MEM
I2 ADD        [IF] [ID] [NOP][EX] [MEM][WB]
                          ↑
                     Bubble inserted — I2 delayed by 1 cycle
```

**Effect:** 1 wasted cycle but correct result guaranteed. After the stall, forwarding from MEM/WB supplies R1 to I2's EX stage.

### Control Hazard Resolution

#### 1. Branch Prediction

**Definition:** The processor predicts whether a branch will be taken or not, and fetches instructions accordingly. If the prediction is wrong, fetched instructions are flushed.

|Strategy|Description|Accuracy|
|---|---|---|
|**Static — Predict Not Taken**|Always assume branch is not taken; fetch sequentially.|~50–65%|
|**Static — Predict Taken**|Always assume branch is taken; fetch from target.|~55–70%|
|**Dynamic — 2-bit Counter**|Uses past branch history to predict future behaviour.|~90%+|

**2-bit Saturating Counter — 4 states:**

```
Strongly Not Taken (00) → Weakly Not Taken (01)
        ↑                          ↓
Strongly Taken    (11) ← Weakly Taken    (10)

Two consecutive mispredictions needed to change prediction.
```

**Effect:** Reduces branch penalty significantly. Wrong prediction → flush and restart.

---

2. **Delayed Branching**

**Definition:** The processor always executes the instruction immediately after the branch (called the **delay slot**) regardless of whether the branch is taken. The compiler fills this slot with a useful instruction.

**Example:**

```
Without delayed branch (1 wasted cycle):
BEQ R1, R2, LOOP
NOP              ← wasted cycle

With delayed branch (compiler fills slot):
ADD R3, R4, R5   ← moved here from before branch — always executes
BEQ R1, R2, LOOP ← branch
                 ← ADD already executed usefully — zero wasted cycles!
```

**Effect:** Branch penalty eliminated when compiler successfully fills the delay slot. Used in MIPS architecture (1 delay slot).

### Structural Hazard Resolution

#### 1. Separate I-Cache and D-Cache (Harvard Architecture)

**Definition:** Use independent Instruction Cache (I-Cache) for the IF stage and Data Cache (D-Cache) for the MEM stage so both can operate simultaneously without conflict.

```
Before (one memory):         After (separate caches):
IF  → [MEMORY] ← MEM        IF → [I-Cache]    MEM → [D-Cache]
      CONFLICT!                   No conflict — both work in parallel!
```

**Effect:** Eliminates the most common structural hazard entirely. Used in all modern processors.

---

#### 2. Pipelining Functional Units

**Definition:** If a non-pipelined functional unit (e.g. floating-point divider) causes structural hazards when two instructions need it simultaneously, the unit is fully pipelined so it can accept a new instruction every cycle.

**Effect:** Multiple instructions can use the same functional unit in overlapping cycles — structural hazard eliminated.

## Throughput and Speedup in Pipelining

---

### Definitions

**Throughput:** Number of instructions completed per unit time. In an ideal pipeline, one instruction completes every clock cycle after the pipeline is full.

**Speedup:** Ratio of execution time without pipelining to execution time with pipelining. Tells how much faster the pipeline is compared to sequential execution.

---

### Formulas (Section 7.2)

**Key terms:**

```
k = number of pipeline stages
n = number of instructions
τ = clock cycle time (time per stage)
```

**Without Pipelining:**

```
Total Time = n × k × τ
```

**With Pipelining:**

```
Total Time = (k + n - 1) × τ
```

**Speedup:**

```
Speedup = (n × k) / (k + n - 1)

For large n (n >> k): Speedup ≈ k  (ideal speedup)
```

**Throughput:**

```
Non-pipelined  → 1 instruction per (k × τ)
Pipelined      → 1 instruction per τ  (after pipeline fills)
```

---

### Example 1 — Basic Calculation

**Given:** k = 5 stages, n = 100 instructions, τ = 2 ns

**Without Pipelining:**

```
T = n × k × τ
T = 100 × 5 × 2 = 1000 ns
```

**With Pipelining:**

```
T = (k + n - 1) × τ
T = (5 + 100 - 1) × 2 = 104 × 2 = 208 ns
```

**Speedup:**

```
Speedup = 1000 / 208 ≈ 4.81
(Close to ideal speedup of 5 — difference is pipeline fill time)
```

**Throughput:**

```
Throughput = 1 instruction per 2 ns = 500 MIPS
```

---

### Example 2 — Speedup Approaches k as n Grows

**Given:** k = 5 stages, τ = 1 ns

```
n = 10  : Speedup = (10×5) / (5+10-1)  = 50/14   = 3.57
n = 100 : Speedup = (100×5)/ (5+100-1) = 500/104  = 4.81
n = 1000: Speedup = (1000×5)/(5+1000-1)= 5000/1004 = 4.98
n → ∞  : Speedup → 5  (ideal speedup = k)
```

**Conclusion:** As n increases, the pipeline fill overhead (k-1 cycles) becomes negligible and speedup approaches the number of stages k.

---

### Figure 7.1 — Throughput Comparison

```
Non-Pipelined (k=5, τ=1ns):
┌────────────────────┐
│ ADD: IF-ID-EX-M-WB │ → completes at cycle 5
└────────────────────┘
┌────────────────────┐
│ SUB: IF-ID-EX-M-WB │ → completes at cycle 10
└────────────────────┘
1 instruction every 5 cycles = 200 MIPS

Pipelined (k=5, τ=1ns):
Cycle:  1    2    3    4    5    6    7
ADD    [IF] [ID] [EX] [MEM][WB]
SUB         [IF] [ID] [EX] [MEM][WB]
AND              [IF] [ID] [EX] [MEM][WB]
                                ↑
                    1 instruction completes every cycle = 1000 MIPS
```

**Ratio: 1000 / 200 = 5× improvement in throughput**

---

### Key Distinction

> Pipelining improves **throughput** (instructions per second) but does NOT reduce **latency** (time to complete one instruction). A single instruction still takes k clock cycles — pipelining's benefit only shows when executing many instructions. 


## Basic Concept of Instruction Pipelining

---

### Definition

**Pipelining** is a technique where instruction execution is divided into discrete stages, and multiple instructions are overlapped — each in a different stage simultaneously — to increase throughput.

**Analogy:** Like an assembly line in a factory — instead of completing one car before starting the next, multiple cars are at different stages simultaneously, dramatically increasing output.

---

### Figure 6.1 — Non-Pipelined vs Pipelined

**Without Pipelining:**

```
Cycle:  1    2    3    4    5    6    7    8    9    10   11   12   13   14   15
ADD    [IF] [ID] [EX] [MEM][WB]
SUB                             [IF] [ID] [EX] [MEM][WB]
AND                                                        [IF] [ID] [EX] [MEM][WB]

3 instructions = 15 cycles
```

**With Pipelining:**

```
Cycle:  1    2    3    4    5    6    7
ADD    [IF] [ID] [EX] [MEM][WB]
SUB         [IF] [ID] [EX] [MEM][WB]
AND              [IF] [ID] [EX] [MEM][WB]

3 instructions = 7 cycles
After pipeline fills → 1 instruction completes every cycle!
```

---

### The Five Pipeline Stages (Section 6.3)

|Stage|Full Name|Operation|
|---|---|---|
|**IF**|Instruction Fetch|Fetch next instruction from memory using PC. Increment PC.|
|**ID**|Instruction Decode|Decode opcode, read source registers, sign-extend immediates.|
|**EX**|Execute|ALU performs arithmetic/logic operation or computes memory address.|
|**MEM**|Memory Access|Load: read from data memory. Store: write to data memory.|
|**WB**|Write Back|Write ALU result or loaded data back to destination register.|

---

### Figure 6.2 — Pipeline with Inter-Stage Registers

Between every two adjacent stages there are **pipeline registers** (inter-stage latches) that hold intermediate results until the next stage is ready.

```
     ┌────┐  ┌───────┐  ┌────┐  ┌───────┐  ┌────┐  ┌───────┐  ┌─────┐  ┌───────┐  ┌────┐
     │ IF │→ │ IF/ID │→ │ ID │→ │ ID/EX │→ │ EX │→ │ EX/MEM│→ │ MEM │→ │ MEM/WB│→ │ WB │
     └────┘  └───────┘  └────┘  └───────┘  └────┘  └───────┘  └─────┘  └───────┘  └────┘
```

- **IF/ID** — holds fetched instruction and updated PC
- **ID/EX** — holds decoded instruction, register values, control signals
- **EX/MEM** — holds ALU result, branch target, control signals
- **MEM/WB** — holds data from memory or ALU result for write back

Each stage takes exactly **1 clock cycle**. All pipeline registers are clocked simultaneously — data moves one stage per cycle.

---

### Key Distinction — Throughput vs Latency

> Pipelining improves **throughput** (instructions completed per second) but does **NOT** reduce **latency** (time to complete one instruction). A single instruction still takes k = 5 clock cycles from start to finish — the same as without pipelining. The benefit only appears when executing **many instructions** continuously.

```
Latency  (1 instruction) = 5 cycles  → same with or without pipeline
Throughput (many instructions) = 1 instruction/cycle → 5× better with pipeline
```
# Isadora: Automated Hardware Information Flow Specification Mining

## Main Goal

### Technical Description

Create an automated methodology that combines information flow tracking (IFT) with specification mining to produce a human-readable information flow specification of a hardware design.

### Human-Readable Description

Create a system that can:

* Detect suspicious or security-relevant patterns in hardware
* Track how information moves through a circuit
* Communicate those patterns in a readable way through generated specifications or visualizations

---

# Core Problem

## Technical Description

A naive application of trace-based mining to an IFT-instrumented design creates two major issues:

1. Instrumented designs become extremely large and overwhelm the miner.
2. The miner discovers meaningless properties over tracking signals and original design signals that cannot be transformed back into useful information flow properties.

## Human-Readable Description

Existing systems that analyze hardware traces struggle because:

* The amount of generated data becomes too large to process efficiently.
* The system identifies meaningless patterns that do not actually help explain hardware behavior or security risks.
* These meaningless patterns are difficult or impossible to communicate to humans.

---

# Proposed Solution

## Technical Description

To solve these issues, the process is separated into two stages:

1. Identifying source-sink flow pairs in the design
2. Mining the conditions that govern those flows

The approach synchronizes both stages using clock-cycle time.

## Human-Readable Description

Instead of analyzing everything at once:

1. Record which hardware components exchange information.

   * Example: Point A sends information to Point B.
2. Analyze the conditions under which those connections occur.
3. Synchronize everything using the hardware clock cycle.

This makes the analysis more manageable and easier to interpret.

---

# Key Technologies Used

## Isadora Workflow

Isadora uses:

* Tortuga for hardware security instrumentation
* Questa Advanced Simulator to simulate the hardware and generate traces
* Daikon to infer flow conditions and invariants
* Python to manage the workflow, flow analysis, and postprocessing

---

# Information Flow Tracking (IFT)

## Definition

Information Flow Tracking (IFT) is a security verification technique that monitors how information moves through a hardware design.

## Security Purpose

IFT helps verify whether sensitive information:

* Flows where it should
* Does not flow where it should not
* Follows the hardware's intended security policy

This helps evaluate the security posture of the hardware design.

---

# Isadora Overview

## Key Characteristics

* Fully automated
* Works on SoCs and CPUs
* Generates human-readable information flow specifications
* Uses IFT at the register-transfer level (RTL)
* Can theoretically operate at the bit level, though this is computationally expensive

## Security Impact

The generated flow specifications helped identify security-relevant behaviors and potential vulnerabilities.

---

# Simulated Hardware Environment

The researchers tested Isadora using:

* A simulated hardware design
* An access control module
* A multicontroller system
* A multi-peripheral system with a known security policy
* A RISC-V design

---

# Comparison: Tortuga vs Isadora

## Tortuga

* Existing security design tool
* Requires significant manual specification writing
* Security guarantees are only as strong as the manually provided properties

## Isadora

* Automates the process
* Automatically discovers information flow properties
* Uses simulation traces and invariant mining to infer behavior

---

# Four Major Phases of Isadora

## 1. Generating Traces

### Technical Description

The hardware design is instrumented with IFT logic and executed in simulation.

This produces a trace set containing:

* Every design signal value
* Every tracking signal value
* Values for every clock cycle

### Human-Readable Description

The system takes snapshots of the hardware state during every moment of execution.

---

## 2. Identifying Flows

### Technical Description

The trace set is analyzed to identify:

* Every information flow between signals
* Every pair of signals where no flow occurs

### Human-Readable Description

The system determines:

* Which components exchanged information
* Which components never exchanged information

Essentially, it identifies all Point A → Point B communication patterns.

---

## 3. Mining for Flow Conditions

### Technical Description

Daikon is used to infer predicates that describe the conditions under which flows occur.

### Human-Readable Description

The system learns:

* Under what circumstances information transfers happen
* What hardware states allow or block communication

These conditions act like logical rules or gates.

---

## 4. Postprocessing

### Technical Description

The final stage:

* Removes redundant predicates
* Combines predicates with flow traces
* Produces readable information flow specifications

### Human-Readable Description

The system cleans up the results and generates concise, understandable security rules.

---

# Types of Properties Generated

## No-Flow Properties

No information is allowed to flow between two design elements.

Example:

* Sensitive data must never reach an unprivileged register.

---

## Conditional-Flow Properties

Information may flow only under specific conditions.

Example:

* Data may transfer only when a permission signal is enabled.

These are especially important for security validation.

---

## Unconditional-Flow Properties

Information always flows between two design elements regardless of hardware state.

These are generally less interesting from a security perspective.

---

# Detailed IFT Operation

## Tracking Signals

Each signal `s` is paired with a tracking signal `s^T`.

### Process

1. One or more source signals are selected.
2. Their tracking signals are set to nonzero.
3. All other tracking signals are set to zero.
4. As information propagates through the design, tracking signals propagate as well.

If information from signal `s` reaches another signal:

* The receiving signal's tracking value changes from zero to nonzero.

---

# Flow Discovery Process

## Source Trace Analysis

For each signal `s`:

* IFT tracks the flow of information from a single tracking signal `s^T`
* The trace `τsrc` is searched for moments when a tracking signal changes from 0 to 1

This indicates that information has reached that signal.

---

## Tuple Representation

Flows are stored as tuples:

```text
(src, s, {i0, i1, ...})
```

Meaning:

* Information from `src` reached signal `s`
* The flow occurred at times `i0, i1, ...`

Multiple tuples may exist because hardware resets or state changes can reset tracking values.

---

# Dynamic Invariant Detection

## Purpose

Isadora uses dynamic invariant detection to infer:

* Conditions under which flows occur
* Behavioral rules of the hardware

## Daikon's Role

Daikon analyzes trace slices and infers predicates describing:

* Design state before the flow
* Design state during the flow

---

# Trace Slicing

To isolate conditions causing a flow:

1. Isadora identifies all times where information flowed from source to destination.
2. It extracts two-clock-cycle trace slices around each flow event.
3. These slices are fed into Daikon.

This helps isolate the exact conditions associated with the flow.

---

# Multi-Source and Multi-Sink Flows

Isadora also analyzes entire unsliced traces to discover:

* Multi-source flows
* Multi-sink flows

These represent groups of flows that:

* Occur simultaneously
* Share the same conditions

This reduces redundancy and improves readability.

---

# Implementation Details

## Technologies

| Tool                      | Purpose                                  |
| ------------------------- | ---------------------------------------- |
| Tortuga                   | Hardware security instrumentation        |
| Questa Advanced Simulator | Hardware simulation and trace generation |
| Daikon                    | Invariant and predicate mining           |
| Python                    | Workflow management and postprocessing   |

---

# Grammar and Logic Notes

## Register Definition

```text
r ∈ {x, y, z}
```

Meaning:

* `r` is a register
* It can take one of three possible states

---

## Previous State

```text
prev(r)
```

Meaning:

* The value of register `r` during the previous clock cycle

---

## No-Flow Property

```text
r1 ⇏ r2
```

Meaning:

* No information flows from `r1` to `r2`

---

## Conditional Flow Example

```text
¬e → (r1 ⇏ r2)
```

Meaning:

* If `e` is false, no information may flow from `r1` to `r2`
* Information flow is only allowed when `e` is true

---

## Predicate Example

```text
e = b ∧ e | b
```

Meaning:

* `e` occurs only if `b` is true
* If `b` is true, then `e` is true

---

## Complex Predicate Example

```text
b = r ∈ cardinal{x, y, z} | r1 = r2 | r1 ≠ r2 | r = prev(r)
```

### Simplified Interpretation

* `b` depends on register `r`
* `r` can only be in a small set of states
* The system may require equality or inequality between sub-signals
* `r` remains unchanged from the previous clock cycle

---

# Registers and Verilog Notes

## Verilog Register

A register is a hardware storage element that:

* Holds a value
* Retains that value until overwritten

Analogy:

* Like writing on a whiteboard until something new replaces it

---

# No-Flow Security Meaning

If a tracking signal `s^T` remains zero:

* No information flow occurred

This is useful for:

* Preventing unprivileged influence over sensitive state
* Preventing information leakage

---

# Testbenches

A testbench is:

* A controlled simulation environment
* Used to verify hardware functionality and behavior

---

# Trace Set Definition

A trace set records:

* Signal values
* Tracking signal values
* Hardware behavior across clock cycles

Essentially, it captures the hardware's execution history.

---

# Heatmap Notes

## Observations

Designer-provided assertions existed for red-outlined boxes.

Detected violations:

* Flows occurred between `S PORT` and `CNFG`

Satisfied assertions:

* Flows occurred between `M INT` and `M PORT`

Interesting observation:

* Infrequent flows into `S PORT`
* `S PORT` is used by the trusted entity to configure the ACW

---

# PicoRV32 Signal Categories

| Label | Meaning                 |
| ----- | ----------------------- |
| out   | Output registers        |
| int   | Internal registers      |
| mem   | Memory interface        |
| ins   | Instruction registers   |
| dec   | Decoder                 |
| dbg   | Debug signals and state |
| msm   | Main state machine      |

The study found that:

* Eight of ten sampled properties encoded CWE-defined security behavior.

---

# ACW Signal Groups

## GLOB

Global ports:

* Clock
* Reset
* Interrupt lines

---

## S PORT

AXI secondary interface ports.

Used by the trusted entity to configure the ACW.

---

## C PORT

Connections to non-AXI controller ports.

Used to configure the controller for traffic generation.

---

## M PORT

AXI main interface ports.

Carries traffic between the peripheral and the ACW control mechanism.

---

## CNFG

Configuration signals.

Responsible for:

* Managing configuration
* Storing ACW configuration state

---

## M INT

AXI M interface ports of the controller.

Carries traffic between:

* The ACW control mechanism
* The controller

If allowed by ACW configuration:

* Traffic may flow between `M INT` and `M PORT`

---

## CTRL

Control logic signals.

Represents the hardware control mechanism.

---

# Overall Conceptual Summary

Isadora is an automated hardware security analysis system that combines:

1. Information Flow Tracking (IFT)
2. Dynamic invariant detection
3. Trace mining
4. Simulation-based analysis

Its goal is to automatically discover and describe how information moves through hardware systems.

Instead of requiring engineers to manually specify all security rules, Isadora:

* Simulates hardware behavior
* Tracks information movement
* Detects communication paths
* Learns the conditions under which flows occur
* Produces readable security specifications

The major innovation is separating:

* Flow discovery
* Flow condition mining

and synchronizing them using clock-cycle timing, which makes large-scale hardware analysis more manageable and interpretable.

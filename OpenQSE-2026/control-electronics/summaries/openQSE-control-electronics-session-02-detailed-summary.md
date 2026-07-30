# OpenQSE Control Electronics Session 02 Detailed Summary

Source: `openQSE-control-electronics-session-02-transcript.docx`  
Extracted meeting header: `QCUF-20260724_140045-Meeting Recording`  
Meeting recording date in transcript: July 24, 2026, 6:00 PM  
Duration in transcript: 2h 6m 37s  
Focus requested: milestones and deliverables

## Transcript Note

The source is an automated transcript and contains recognition errors. This summary normalizes clear technical terms where the context supports it, including OpenQSE, QPU, QEC, FTQC, NISQ, QDMI, HWLOC, Sys-SAGE, Q1ASM, QICK, FPGA, CPU, GPU, HPC, IQM, LBL/Berkeley, Fermilab, Riverlane, and Qblox. Speaker labels appear unreliable in several room-discussion segments, so the summary focuses on content rather than precise attribution.

## Executive Summary

Session 02 focused on the interface between control electronics and the broader OpenQSE runtime/software architecture. The central point was that a quantum computer should not be treated as a single black box: the chip, cryogenic/fridge environment, cabling, control electronics, decoders, processors, calibration services, and HPC interfaces all exchange data with different latency, bandwidth, and correctness requirements.

The group converged on the need for a common language and lightweight interface model before attempting a full implementation. The near-term deliverable is not a finished open control stack. The first deliverables should be a state-of-the-art catalog, terminology/constraint definitions, input-output and performance metadata, and a minimum control/runtime interface specification that can connect to OpenQSE, QDMI, compilers, runtime systems, QEC decoders, and future testbeds.

The most concrete milestone path was: form a control/runtime working group, define the current interface landscape, identify what can be standardized, build a blueprint for how a generic control stack plugs into the OpenQSE architecture, and later test that blueprint using a reference implementation, emulator/digital twin, or available open control hardware.

## Meeting Purpose

The session was framed as a working-group seed discussion inside OpenQSE. Its purpose was to explore what OpenQSE can realistically define at the boundary between:

- Higher-level software architecture, compilers, runtime, and HPC systems.
- Control electronics and control processors.
- QEC decoders and fast feedback paths.
- Physical QPU hardware and logical/algorithmic abstractions.

The facilitator emphasized that nothing was settled and that the session should produce questions, milestones, and homework rather than a finished design.

## Control Electronics Context

The session began with a high-level overview of quantum control. A QPU was described as more than a quantum chip: the physical system includes a fridge or other environment, cabling, control electronics, timing/synchronization logic, readout paths, and interfaces to classical processors.

The control stack is responsible for making algorithms run as intended across repeated shots. It takes user-level tasks and device information such as calibration data, then produces timed control operations and collects measurement results. It also exposes or hides lower-level capabilities that may be useful for calibration, QEC, feed-forward, and performance tuning.

The presenter highlighted the wide latency spectrum in control:

- Some reflex-like hardware operations happen in real time close to the QPU.
- Some feedback paths can tolerate near-real-time behavior.
- Some workflow-level decisions can run at normal application or HPC time scales.

This led to an early theme: the community needs precise terminology for "real time," "near time," "soft real time," and non-real-time workflow interactions.

## Data Flow and Interfaces

The control-stack diagram described multiple data paths rather than one generic API. It distinguished NISQ-style application flows from QEC/FTQC-style flows.

Core data flows discussed:

- User or application selects the task to run.
- Device and qubit information, including calibration data, enters the control stack.
- The control stack generates pulse/gate schedules and timing.
- The control stack retrieves measurement information and processes readout.
- QEC workflows may send syndrome or measurement data to an external decoder.
- Decoding may run on FPGA, GPU, CPU, or a nearby rack of classical processors.
- Decoder output may feed back into the control processor because schedules and pulse timing can change.
- NISQ workflows may often reuse a circuit template and update parameters, while QEC/FTQC workflows can require deeper feedback into the control schedule.

The discussion repeatedly returned to where decoding should run:

- Close to the control electronics when interconnect latency dominates.
- In HPC nodes or colocated accelerators when the task can tolerate higher latency.
- In a mixed architecture where only latency-critical pieces stay close to the QPU and slower/global pieces run elsewhere.

Deliverable implication: OpenQSE needs to describe each interface by data size, timing constraints, bandwidth, locality, and correctness impact, not just by naming the connected components.

## Topology and Scaling Questions

The group identified topology as a central open issue. A control stack may not always map one-to-one to a single QPU.

Topologies discussed:

- One controller controlling one QPU.
- One controller controlling multiple QPUs or modules.
- Multiple controllers coordinating one logical QPU.
- Multiple physical QPUs appearing as one logical quantum computer.
- Modular systems where quantum state is distributed across chips/modules and real-time coordination is required.
- Multiple independent QPUs, where orchestration may belong to a higher-level workload manager instead of the control layer.

The group distinguished between:

- A single logical QPU internally partitioned across multiple control units.
- Multiple unrelated QPUs that should be scheduled by a workflow/HPC manager.
- Future "multicore-like" quantum systems where several physical systems appear as one computer behind an abstraction layer.

For modular or partitioned systems, participants highlighted the need for synchronization and coordination among multiple controllers. A "real-time host" above real-time controllers was proposed as one possible architectural layer.

Deliverable implication: a control/runtime interface must expose enough topology and affinity information for scheduling and validation, while hiding vendor-specific implementation details where possible.

## Metadata, Discovery, and Minimum Spec

A recurring proposal was a lightweight manifest or metadata specification. The format was not important; JSON was mentioned as an example. The value is to give software a common way to ask, "What am I connected to?"

Candidate metadata fields included:

- Modality or technology type.
- Number of physical and/or logical qubits.
- Whether the system is a single QPU, multiple modules, or multiple controllers.
- Basic topology, zones, modules, or coupling information.
- Calibration status or quality/probability information, where vendors are willing to expose it.
- Interconnect and locality information between racks/controllers/modules.
- Minimal failure/status bits from the control system.
- Supported instruction/interface level.
- Configuration options for control electronics or QEC code.

QDMI was discussed as a relevant existing mechanism for querying device information. The group noted that QDMI can normalize device-specific information after a driver query, but the session explored whether some commonality should exist lower in the stack as well.

HWLOC and Sys-SAGE were mentioned as useful analogies from classical systems. HWLOC exposes classical topology such as sockets, cores, and threads. Sys-SAGE was described as combining HWLOC and QDMI information into a hybrid tree/graph for classical/quantum systems.

Deliverable implication: OpenQSE should avoid reinventing QDMI/HWLOC, but should define what extra control-stack metadata is needed and where it belongs.

## Standardization Scope

The group did not define one final standard. Instead, it identified multiple possible standardization layers:

- Terminology: consistent names for control-stack concepts across vendors.
- Constraints: precise meaning of real-time, near-time, deadlines, correctness failure, and performance degradation.
- Interface inputs and outputs: what enters and leaves the control layer.
- Data formats: measurement data, syndrome data, failure/status information, and configuration data.
- Performance metrics: latency, bandwidth, throughput, jitter, program size, memory limits, and data locality.
- Fast-feedback protocols: how decoder/control/runtime components exchange time-sensitive information.
- Compiler-facing interface: what the compiler or symbolic-processing layer sees.
- Side interfaces: control-to-decoder, FPGA-to-FPGA, GPU/CPU accelerator paths, and RDMA-like protocols.
- Validation/testing interfaces: enough specification for functional and conformance testing.

Several participants argued that a low-level control interface should be analogous to RISC-V: low-level enough to be precise and useful, while letting vendors keep proprietary implementation details below the interface. Q1ASM was discussed as one existing low-level example, but not automatically the standard for everyone.

## Vendor and Adoption Considerations

The group acknowledged that vendor participation is hard because control stacks often contain proprietary "secret sauce." Calibration methods, noise models, control timing, and hardware behavior may be competitively sensitive.

Potential incentives for vendors:

- Reduce duplicated engineering at a layer where everyone is solving similar problems.
- Enable third-party tools, telemetry tools, testing tools, and analysis tools.
- Make systems easier to plug into broader OpenQSE software.
- Support community research on QEC and control without each vendor building a full software ecosystem alone.
- Enable validation, verification, and eventual government procurement/certification requirements.
- Provide a controlled place to expose useful information without revealing internals.

Potential risks:

- Vendors may not expose enough metadata to make the interface useful.
- A standard that is too intrusive may be rejected.
- Opening development too broadly too early could create noise and low-quality contributions.
- A specification without a usable implementation may not gain traction.

## Candidate Testbeds and Reference Platforms

The group discussed several potential ways to test or prototype a control/runtime interface:

- A software-only emulator or digital twin running on a normal Linux system.
- A simulator that mimics signatures of control hardware without requiring a physical control stack.
- LBL/Berkeley work involving an IQM chip integrated with local control electronics.
- Fermilab's open quantum control kit, discussed as a customizable Python/Jupyter-based option.
- IQM on-prem systems, though opening the control box may have warranty or vendor constraints.
- QEC quantum testbeds where control stacks, a Riverlane box, and IQM-like hardware may be integrated.
- Local or national-lab test environments once the software-only stage is mature enough.

The group leaned toward making progress in software first, then engaging hardware vendors once the value proposition is clearer.

## Proposed Milestones

### Milestone 1: State-of-the-Art Catalog and Minimum Interface Inventory

Build a comprehensive catalog of current control-stack physical and software interfaces. This should identify what vendors and open projects can share, what is already available, and what information is needed by OpenQSE.

Expected deliverables:

- Catalog of current control-stack interfaces.
- Current state-of-the-art physical/software interface map.
- Input/output map for the control layer.
- Initial terminology glossary.
- Initial constraint taxonomy: real time, near time, soft real time, non-real time.
- Minimum metadata/spec candidate, possibly aligned with QDMI.
- Initial performance metrics list.
- Vendor/project interest list.

### Milestone 2: Boundary and Standardization Candidate Definition

Use the catalog to decide which interfaces and data elements can realistically be standardized.

Expected deliverables:

- List of standardization candidates.
- Required versus optional metadata fields.
- Measurement/syndrome/failure/configuration data format candidates.
- Control configuration API candidates.
- Feedback/feed-forward data requirements.
- Topology, locality, and affinity representation requirements.
- Identification of vendor-specific items that should remain below the standard.

### Milestone 3: OpenQSE Control-Stack Blueprint

Define how a generic control stack would plug into the OpenQSE architecture and the level above it.

Expected deliverables:

- Blueprint for integrating control electronics into OpenQSE runtime/software architecture.
- Compiler-facing interface description.
- Runtime/HPC-facing interface description.
- Decoder/fast-feedback side-interface description.
- Fast-feedback protocol requirements.
- Performance KPIs the interface must satisfy.
- First version of a control/runtime specification.

The discussion placed this as a possible SC 2027-scale target for a first specification, with the caveat that paper deadlines would require earlier concrete artifacts.

### Milestone 4: Reference Implementation or Digital Twin

Build and test a reference implementation when the interface definition is mature enough.

Expected deliverables:

- Work breakdown for implementation.
- Decision on emulator/digital twin versus hardware-backed testbed.
- Prototype implementation of the minimum interface.
- Test workflows for NISQ, QEC feedback, and control configuration paths.
- Validation/conformance tests.
- Gap analysis against real hardware needs.

The group emphasized that this milestone requires people, funding, and engineering skill that are not yet committed.

### Milestone 5: Community Engagement and Recruiting

Because OpenQSE is open source/open development, the group discussed the need to recruit engineers, students, DOE labs, and vendors.

Expected deliverables:

- Regular control/runtime working group meeting.
- Slack channel or other coordination space.
- Cross-attendance with the compiler working group.
- Outreach to LBL/Berkeley, Fermilab/QICK, IQM contacts, QEC testbed teams, and other DOE labs.
- Possible IEEE Quantum Week awareness/outreach milestone.
- Funding and contributor plan for implementation work.

## Immediate Action Items

- Set up a regular control/runtime working group meeting.
- Use Slack to coordinate scheduling and invite interested participants who could not attend.
- Avoid conflicts with existing OpenQSE working groups so people can participate across groups.
- Create or use a control-focused Slack channel.
- Recruit at least one representative to attend the compiler working group and keep interfaces aligned.
- Begin Milestone 1: catalog existing control-stack interfaces, metadata, input/output paths, and performance constraints.
- Break each milestone into subtasks so the group can reason about staffing and schedule.
- Identify which interfaces are owned by this working group versus compiler/runtime/QEC groups.
- Reach out to LBL/Berkeley, Fermilab/QICK, IQM, Riverlane-related efforts, and other possible testbed contributors.
- Decide whether the first technical artifact is a document, a lightweight spec, an emulator, or a small software prototype.

## Timeline Discussed

- Near term: set up the working group and begin Milestone 1.
- Next few weeks: define concrete action items, meeting cadence, and work breakdown.
- End-of-year style planning: create a work structure and milestone breakdown.
- SC 2027-scale target: first version of a control/runtime specification or blueprint was discussed.
- Publication caveat: SC full-paper deadlines would require results much earlier, around spring; workshop papers later in the year may be more realistic.
- Community engagement: IEEE Quantum Week was mentioned as a possible venue to socialize the effort and recruit contributors.

## Open Questions and Risks

- What exact layer should OpenQSE standardize: compiler-facing interface, decoder side interface, metadata manifest, control ISA, or all of these in stages?
- What is the minimum useful metadata set vendors will actually expose?
- How much topology and calibration data can be shared without exposing proprietary control details?
- Should commonality be pushed below QDMI, or should QDMI remain the normalizing layer?
- Which interfaces belong to the control/runtime group, and which belong to compiler, runtime, QEC, or hardware groups?
- What should the reference implementation run on: emulator, digital twin, open control kit, IQM/LBL testbed, or another platform?
- Who will fund and staff implementation work?
- How can OpenQSE recruit enough engineers or students without opening the process too broadly too early?

## Bottom Line

The session produced a practical path for the control electronics/runtime interface effort. Start with language, constraints, interface inventory, and minimal metadata. Use that to define standardization candidates and an OpenQSE control-stack blueprint. Then build a reference implementation or digital twin once the interface is concrete enough and the group has people, funding, and a test platform.

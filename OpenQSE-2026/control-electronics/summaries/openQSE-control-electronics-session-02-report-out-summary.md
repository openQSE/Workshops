# OpenQSE Control Electronics Session 02 Report-Out Summary

## Slide 1: Bottom Line

Key message: OpenQSE needs a shared control/runtime interface model before building a full open control stack.

- Topic: interface between control electronics, runtime, compilers, QEC decoders, and HPC systems.
- A QPU is not a black box: control, decoding, calibration, timing, and data movement all matter.
- Near-term deliverables should be terminology, interface inventory, metadata, constraints, and a blueprint.
- Reference implementation comes later, likely through emulator/digital twin or an available open testbed.

Speaker note: The session focused on what OpenQSE can define above vendor-specific control details.

---

## Slide 2: Control Stack Reality

Key message: Different control paths have different latency and correctness requirements.

- NISQ flows can often reuse circuit templates and update parameters.
- QEC/FTQC flows may require decoder feedback into control scheduling.
- Decoding can run on FPGA, GPU, CPU, nearby racks, or HPC resources depending on latency.
- Some data paths are real time; others are near-time or normal workflow-time.
- Control interfaces need data size, bandwidth, locality, latency, and correctness semantics.

Speaker note: "Tight coupling" needs to be broken into concrete categories.

---

## Slide 3: Architecture Questions

Key message: Scaling control is a software architecture problem.

- Topology may be 1:1, 1:N, N:M, modular, or multicore-like.
- Multiple controllers may need synchronization above real-time controllers.
- One logical QPU may hide multiple modules or controllers.
- Multiple independent QPUs should likely be handled by workflow/resource managers.
- Interfaces must expose enough topology/affinity without exposing proprietary internals.

Speaker note: The group discussed a possible real-time host layer over real-time controllers.

---

## Slide 4: What To Standardize

Key message: Start small and standardize what enables useful integration.

- Terminology for control-stack concepts.
- Constraint taxonomy: real time, near time, soft real time, non-real time.
- Minimum metadata: modality, qubit counts, topology/modules, calibration/status, supported interface.
- Data formats: measurements, syndrome data, failure/status bits, configuration data.
- Performance metrics: latency, bandwidth, jitter, throughput, program size, memory/locality.
- Interfaces to compiler/runtime above and decoder/feedback paths beside the control stack.

Speaker note: QDMI, HWLOC, and Sys-SAGE are relevant prior art; OpenQSE should extend rather than duplicate them.

---

## Slide 5: Milestones

Key message: The proposed path is staged and evidence-driven.

- Milestone 1: Catalog state-of-the-art control interfaces and define minimum metadata.
- Milestone 2: Identify standardization candidates and required/optional fields.
- Milestone 3: Produce an OpenQSE control-stack blueprint and first interface spec.
- Milestone 4: Build a reference implementation, emulator, or digital twin and test it.
- Milestone 5: Grow participation through regular meetings, compiler-group coordination, and lab/vendor outreach.

Speaker note: SC 2027 was discussed as a possible target for a first specification or blueprint.

---

## Slide 6: Immediate Deliverables

Key message: The group can start without waiting for hardware access.

- Create the control/runtime working group meeting and Slack coordination channel.
- Invite missing participants and align with compiler/runtime/QEC working groups.
- Begin the Milestone 1 catalog: inputs, outputs, metadata, latency paths, and metrics.
- Break milestones into subtasks and identify staffing/funding gaps.
- Evaluate candidate testbeds: emulator/digital twin, LBL/Berkeley, Fermilab QICK, IQM, QEC testbed.
- Decide first artifact: document, lightweight spec, software prototype, or emulator.

Speaker note: The implementation path needs engineers, students, and funding; papers can help recruit contributors.

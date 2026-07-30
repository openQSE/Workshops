# OpenQSE Runtime Main Points

Source files reviewed:

- `openQSE-runtime-session-01-detailed-summary.md`
- `openQSE-runtime-session-01-report-out-summary.md`

## 1. What Are the Main Points Discussed?

The meeting focused on how OpenQSE should define software architecture and runtime interfaces for hybrid quantum-HPC systems. A major theme was that future workflows will need to coordinate CPUs, GPUs, FPGAs, QPUs, compilers, schedulers, control systems, and QEC decoders rather than treating the QPU as an isolated black box.

The session used a fragment-based quantum chemistry workflow as a concrete reference case. The workflow combines classical HPC preprocessing, fragment-level quantum execution, VQE-style hybrid loops, and classical postprocessing. This made it useful for discussing runtime needs across loose coupling, medium hybrid coupling, and future tighter QEC or feed-forward execution.

The group discussed the need for a runtime layer that can represent hybrid applications as hierarchical task graphs, express dependencies and timing constraints, manage heterogeneous resources, reduce idle time, and expose useful device capability and telemetry to algorithm developers.

Several design boundaries were debated: logical versus physical QPU abstractions, application-level runtime versus device-side runtime, workflow orchestration versus low-level dispatch, and compiler responsibilities versus runtime responsibilities.

The group also discussed metadata and discovery. OpenQSE may need a lightweight way to describe modality, qubit counts, topology, modules, calibration or status information, supported instruction levels, locality, latency, bandwidth, and device/runtime capabilities.

There was strong interest in staged, evidence-driven work: form focused working groups, catalog existing systems, compare prototypes on shared workflows, define terminology and constraints, and only then move toward interface specifications or reference implementations.

## 2. What Conclusions Did They Reach Regarding the Main Points?

The main conclusion was that OpenQSE should not begin by writing a final abstract standard. The group favored first creating working groups, gathering existing prototypes and interface descriptions, defining shared terminology, and using real workflows to learn what an interface actually needs to support.

For the runtime architecture, the group concluded that the first useful deliverable should be a practical path toward a standard: a prototype inventory, an experimental matrix, runnable reference workflows, runtime criteria, an object-model sketch, and a compiler/runtime boundary document.

The fragment-based FMO/VQE chemistry workflow was identified as a strong first reference application because it naturally exercises classical HPC work, parallel fragment execution, hybrid quantum-classical loops, and possible future tighter-coupling extensions.

The group concluded that runtime abstractions should be layered. Application users should usually see logical resources, while algorithm developers, QEC developers, runtime developers, and device researchers may need deeper capability, telemetry, or physical-device information.

The group also concluded that "tight integration" needs more precise language. Loose coupling, medium hybrid coupling, soft real-time behavior, and hard real-time control or QEC feedback should be treated as distinct categories with different latency, locality, bandwidth, and correctness requirements.

Existing runtime, workflow, and resource-management work should be studied and reused where appropriate. OpenQSE should extend or connect existing mechanisms rather than duplicate them unnecessarily.

The near-term action plan is to set up a runtime working group, create coordination channels, recruit missing participants, catalog existing systems, define milestones and staffing needs, and decide whether the first external artifact should be a document, lightweight specification, white paper, demo, emulator, or reference workflow.

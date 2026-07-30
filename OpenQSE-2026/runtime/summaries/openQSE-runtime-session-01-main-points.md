# OpenQSE Runtime Session 01 Main Points

Source: `openQSE-runtime-session-01-transcript.docx`

## Main Points

- The session focused on runtime architecture and interfaces for hybrid quantum-HPC systems.
- A fragment molecular orbital VQE workflow was used as the main reference application.
- The group discussed loose coupling, medium hybrid coupling, and future tighter QEC or feed-forward execution.
- The runtime layer was framed as an application-facing execution contract for hierarchical task graphs with dependencies, timing constraints, data movement, and resource needs.
- Runtime responsibilities include resource-aware dispatch, placement, synchronization, capability discovery, launch overhead reduction, and coordination with compilers, SDKs, resource managers, and admission-control systems.
- Boundaries between workflow engines, application runtimes, device-side runtimes, compiler responsibilities, and low-level control systems remain important design questions.

## Conclusions

- OpenQSE should not begin by writing a final abstract runtime standard.
- The group should start from runnable prototypes, shared workflows, experimental matrices, and concrete measurements.
- The FMO/VQE chemistry workflow is a strong first reference application.
- Runtime abstractions should be layered, with logical resources for most application users and deeper capability/telemetry hooks for algorithm, runtime, QEC, and device developers.
- The compiler/runtime boundary should be documented as a near-term deliverable.

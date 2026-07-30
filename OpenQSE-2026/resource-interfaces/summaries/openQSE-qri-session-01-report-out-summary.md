# OpenQSE QRI Session 01 Report-Out Summary

Source transcript: `openQSE-qri-session-01-transcript.docx`

Session 01 focused on aligning the group around the scope of quantum resource interfaces for HPC environments. The main goal was not to finalize a standard in the session, but to agree on an iterative path: gather stakeholder requirements, propose interface behavior, test it in real systems, and refine toward a shared specification.

Key highlights:

- The group agreed that terminology matters. A "quantum resource" should usually mean more than the QPU alone; it includes the required classical control, preprocessing, access, and service components needed to make the QPU usable.
- Availability, health, calibration, authentication, cost, accounting, and queue behavior were identified as core issues that make quantum resources different from conventional HPC resources.
- There was a clear separation-of-concerns theme: workload managers need availability, usage, and policy-relevant status, while detailed time-series device telemetry is more appropriate for monitoring, analytics, and operations systems.
- QDMI was presented as an open, C-based, typed interface for querying quantum devices and submitting jobs, with future work moving toward a more modular version that supports different hardware modalities and provider/orchestrator models.
- QRMI was presented as a workload-manager-facing resource management layer, initially integrated with Slurm and under evaluation or deployment at several sites, with future work on accounting, credential handling, and dynamic resource discovery.
- QFW was presented as a vendor-neutral execution and experimentation framework that can sit above QDMI, QRMI, simulators, and hardware services, with workstreams for a common shim/interface, libfabric transport, benchmarking, and local admission/scheduling.
- Benchmarking was identified as a key bridge from discussion to evidence. The group wants measurements of interface overhead, scheduling behavior, correctness, runtime behavior, accounting, and usage patterns.
- Several concrete future artifacts were discussed, including a testbed, papers on usage patterns and interface comparisons, an eventual resource specification, and operations/monitoring requirements.

Overall, Session 01 established the technical landscape and surfaced the major design tensions: what the interface should own, what the workload manager should own, how to expose enough status without overloading schedulers, and how to build a testbed that produces data for future specifications.

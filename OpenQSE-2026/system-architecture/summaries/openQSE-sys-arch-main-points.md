# OpenQSE System Architecture Main Points

Sources: `openQSE-sys-arch-session-01-detailed-summary.md`, `openQSE-sys-arch-session-01-report-out-summary.md`, `openQSE-sys-arch-session-02-detailed-summary.md`, `openQSE-sys-arch-session-02-report-out-summary.md`

## 1. What were the main points discussed?

- The Workflows and System Architecture group should translate hybrid quantum-classical applications into concrete system, resource, scheduling, and integration requirements.
- Hybrid workflow analysis needs to identify which stages use QPUs, leadership HPC, nearby classical resources, GPUs, simulators, control systems, and QPU-local decoding hardware.
- AWS and ORNL framed integration in terms of classical compute cost, quantum compute cost, communication cost, latency, bandwidth, locality, and feasibility constraints.
- ORNL proposed a tiered quantum-HPC architecture with leadership HPC connected to quantum facilities, smaller co-located classical resources near QPUs, and very low-latency QPU-local compute for control or QEC.
- Representative workflows included credit-risk analysis, SQD/QSCI chemistry, adaptive quantum simulation, dynamic-circuit state preparation, generative quantum eigensolver workflows, and quantum chemistry embedding.
- The group repeatedly discussed resource phase changes, queue delays, co-scheduling, idle QPU/GPU/HPC allocations, dynamic resource estimation, and the telemetry needed to make scheduling practical.
- NISQ, partial fault-tolerant, and full fault-tolerant workflows may coexist, with different assumptions about logical encodings, sampling rates, rotations, state preparation, feedback, and resource needs.
- Quantum error correction was treated as its own quantum-classical workflow involving syndrome extraction, decoding, correction or frame updates, and strict timing constraints.
- QEC decoder requirements were discussed across FPGAs, GPUs, CPUs, ASIC-like hardware, LDPC codes, surface codes, rare hard syndrome events, and heterogeneous decoder hierarchies.
- Laura Schulz's keynote connected OpenQSE to the DOE quantum supercomputing blueprint, the 2028 early fault-tolerant target, Genesis/American Science Cloud federation, ECP lessons, and ecosystem readiness.
- Federation topics included identity, trust, policy reciprocity, data movement, governance, cross-institution workflows, and the need to align quantum with HPC and AI infrastructure.
- User personas and user teams were discussed as a way to connect workflows to real support, abstraction, access, and architecture needs.
- Non-performance requirements included reproducibility, traceability, provenance, governance, security, data classification, role-based access, sustainability telemetry, and long-term support.
- Cross-workstream report-outs covered compiler IR versus interchange formats, resource-management interfaces such as QRMI/QDMI/QFW, runtime architecture, control software, fast feedback, and reference implementations.
- The group discussed selecting standard workflow examples, especially quantum chemistry, to drive end-to-end co-design, metrics, benchmarking, and requirements shared across workstreams.

## 2. What conclusions were reached regarding the main points?

- Quantum-HPC success will depend on ecosystem readiness, not qubit technology alone. Software, compilers, standards, benchmarks, support, access models, and governance must be prepared before early fault-tolerant hardware arrives.
- "Tight integration" should not be defined only by latency or co-location. It also includes bandwidth, data volume, resource co-scheduling, reliability, observability, identity, policy, administration, software coordination, and user experience.
- Many application-level workflows are loosely coupled in the algorithmic sense, but they still require robust orchestration, scheduling, telemetry, and resource-management support.
- A tiered architecture is the clearest working model: leadership HPC for large application stages, nearby facility compute for selected hybrid loops and configurable experiments, and QPU-local hardware for control and QEC paths.
- "Classical compute" must be split into distinct categories, especially QEC/control compute versus application-level classical compute, because their latency, locality, and ownership requirements differ.
- QEC is the strongest driver of hard real-time quantum-classical integration. Its requirements depend on hardware modality, code family, decoder algorithm, logical scale, and whether the workflow is logical memory or logical computation.
- Near-term and research systems should not assume QEC can always be hidden. Power users and QEC researchers may need configurable codes, decoders, reliability targets, and access to QPU-adjacent classical resources.
- Future architectures must handle phase-changing workflows where QPUs, simulators, GPUs, and HPC nodes are needed at different times. Holding all resources wastes scarce capacity, but releasing resources can introduce queue and restart costs.
- QPU telemetry and resource estimation are foundational. Schedulers and orchestrators need vendor-agnostic ways to reason about availability, calibration, circuit properties, expected runtime, queue state, and failure or reallocation events.
- Reproducibility will be difficult for quantum workflows that cannot be classically verified or independently rerun on equivalent systems. Traceability and provenance are more realistic near-term targets.
- User requirements should be persona-driven and team-aware. Early power users, QEC researchers, workflow developers, and later domain scientists will need different abstractions, access levels, and support models.
- Federation is both a technical and policy problem. OpenQSE should reuse Genesis and broader cyberinfrastructure where possible while identifying requirements that are genuinely quantum-specific.
- Shared vocabulary is a prerequisite for useful specifications. The workstreams need common definitions for workflow, application, architecture, orchestration, integration, coupling, latency, resource estimation, scheduling, IR, and interchange format.
- Quantum chemistry is the leading candidate for an end-to-end co-design workflow because it aligns with expected early fault-tolerant science targets and exposes application, compiler, runtime, scheduling, telemetry, and persona issues.
- The agreed path is evidence-driven and iterative: define terminology, select representative workflows, survey existing prototypes, profile decoder and runtime behavior, collect testbed data, coordinate workstream requirements, and produce papers, draft specifications, reference implementations, and recurring working meetings.

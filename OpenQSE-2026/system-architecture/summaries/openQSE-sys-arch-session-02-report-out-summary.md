# OpenQSE System Architecture Session 02 Report-Out Summary

Source transcript: `openQSE-sys-arch-session-02-transcript.docx`

## Main Point

Session 02 showed that quantum error correction is the strongest driver for tight hardware-software integration, while the broader OpenQSE effort needs shared terminology, traceability, user-persona-driven workflows, and cross-workstream coordination. The session ended with report-outs from the compiler, workflows/system architecture, resource interface, and software architecture groups.

## What Was Discussed

- Peter Grozkowski introduced quantum error correction as a continuous quantum-classical workflow involving syndrome extraction, decoding, correction, timing constraints, and QPU-adjacent classical compute.
- The room discussed whether future users can ignore QEC or whether near-term power users need configurable error-correction codes, decoders, and decoding hardware.
- QEC/application co-design was raised as important for future fault-tolerant applications.
- Decoder latency, hardware hierarchy, LDPC decoding, rare hard syndrome events, and heterogeneous decoder architectures were discussed in detail.
- Laura Schulz led a shortened discussion on non-performance requirements: reproducibility, traceability, governance, identity, user personas, and sustainability telemetry.
- The workflows/system architecture group discussed its role as a cross-cutting requirements coordinator.
- Final readouts covered compiler IR and interchange formats, resource management interfaces, runtime architecture, and control software architecture.

## Key Takeaways

- QEC creates hard real-time integration requirements that are different from most application-level workflows.
- "Classical compute" must be split into QEC/control classical compute and application-level classical compute.
- Tight integration should include latency, bandwidth, co-location, policy, administration, observability, identity, and software coordination.
- Decoder requirements depend on code family, hardware modality, scale, and application.
- Traceability may be a more realistic near-term goal than full reproducibility for hard quantum workflows.
- Quantum chemistry was proposed as a strong end-to-end workflow candidate for early fault-tolerant co-design.

## Presentation Bullets

- QEC is a real-time quantum-classical workflow and a major architecture driver.
- Systems may need a hierarchy: innermost FPGA/ASIC decoding, nearby configurable compute, and larger HPC for application workflows.
- OpenQSE needs shared definitions for workflow, application, architecture, orchestration, integration, coupling, and latency.
- User personas should include both individuals and integrated expert teams.
- Workstreams converged on surveys, prototype inventories, internal specifications, reference implementations, and recurring meetings.

## Suggested Next Actions

- Define integration terminology across technical and policy dimensions.
- Select a persona-dependent quantum chemistry workflow for end-to-end analysis.
- Profile decoder requirements across QEC codes, modalities, and hardware options.
- Define traceability metadata for hybrid quantum-HPC workflows.
- Coordinate compiler, resource interface, runtime, and control-stack requirements around shared workflow examples.

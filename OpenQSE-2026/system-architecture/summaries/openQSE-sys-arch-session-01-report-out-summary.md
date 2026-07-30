# OpenQSE System Architecture Session 01 Report-Out Summary

Source transcript: `openQSE-sys-arch-session-01-transcript.docx`

## Main Point

Session 01 established the Workflows and System Architecture working group as the place where hybrid quantum-classical applications are translated into concrete system requirements. The group used ORNL's proposed quantum-HPC facility model and three application examples to show that "tight integration" cannot be reduced to a single latency number. Scheduling, resource estimation, data movement, telemetry, and operational policy are all part of the architecture problem.

## What Was Discussed

- The working group will analyze hybrid application workflows, identify quantum and classical resource needs, and turn those into architecture requirements.
- AWS introduced a performance-modeling approach that decomposes workflows into classical compute, quantum compute, and communication costs.
- ORNL described a tiered architecture: leadership HPC systems connected to quantum facilities, plus smaller classical/HPC resources co-located near QPUs for control, decoding, and selected hybrid workflows.
- The room discussed whether current workflows really need HPC, whether end-user applications require tight integration, and whether tight integration requires co-location.
- Application examples covered credit risk analysis, SQD/QSCI for chemistry, adaptive quantum simulation, and generative quantum eigensolver workflows.
- Repeated themes were co-scheduling, resource idle time, QPU telemetry, dynamic resource allocation, bandwidth, data volume, and shared terminology.

## Key Takeaways

- Many application workflows look loosely coupled at the application level, but still require robust orchestration and scheduling.
- Fault-tolerant execution changes assumptions from NISQ-era workflows, especially for sampling rates, logical encodings, rotations, and resource needs.
- Hybrid workflows often move through phases that need different resources, which can leave expensive QPU, GPU, or HPC allocations idle.
- QPU telemetry and resource estimation are essential for practical scheduling.
- The group needs shared definitions for workflow, application, architecture, orchestration, tight integration, loose integration, latency, and co-location.

## Presentation Bullets

- OpenQSE needs workflow-driven system requirements.
- A tiered quantum-HPC model is emerging: leadership HPC, quantum facility, and QPU-local classical resources.
- "Tight integration" includes latency, bandwidth, data volume, scheduling, policy, and reliability assumptions.
- Near-term workflows may be loosely coupled, but still expose serious orchestration and resource-management gaps.
- Quantum chemistry and materials workflows are strong candidates for deeper end-to-end studies.

## Suggested Next Actions

- Define shared workflow and integration terminology.
- Select representative workflows for end-to-end resource estimates.
- Identify required QPU telemetry for scheduling and orchestration.
- Coordinate with compiler, resource interface, and software architecture workstreams.

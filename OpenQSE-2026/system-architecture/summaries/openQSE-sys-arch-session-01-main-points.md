# OpenQSE System Architecture Session 01 Main Points

Source: `openQSE-sys-arch-session-01-transcript.docx`

## Main Points

- The session established the Workflows and System Architecture working group as the place where hybrid quantum-classical applications are translated into concrete system requirements.
- ORNL's proposed quantum-HPC facility model and several application examples showed that tight integration cannot be reduced to one latency number.
- Scheduling, resource estimation, data movement, telemetry, and operational policy are all part of the architecture problem.
- The group discussed performance modeling that separates classical compute, quantum compute, and communication costs.
- A tiered architecture emerged: leadership HPC systems connected to quantum facilities, plus smaller classical/HPC resources near QPUs for control, decoding, and selected hybrid workflows.
- Repeated themes included co-scheduling, resource idle time, QPU telemetry, dynamic resource allocation, bandwidth, data volume, and shared terminology.

## Conclusions

- OpenQSE needs workflow-driven system requirements.
- Many workflows are loosely coupled at the application level but still expose serious orchestration and resource-management gaps.
- QPU telemetry and resource estimation are essential for practical scheduling.
- The group should define shared workflow and integration terminology and select representative workflows for end-to-end resource estimates.

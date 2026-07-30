# OpenQSE System Architecture Session 02 Main Points

Source: `openQSE-sys-arch-session-02-transcript.docx`

## Main Points

- The session showed that quantum error correction is a major driver for tight hardware-software integration.
- QEC was framed as a continuous quantum-classical workflow involving syndrome extraction, decoding, correction, timing constraints, and QPU-adjacent classical compute.
- The group discussed configurable error-correction codes, decoders, decoding hardware, and QEC/application co-design.
- Decoder latency, hardware hierarchy, LDPC decoding, rare hard syndrome events, and heterogeneous decoder architectures were discussed in detail.
- Non-performance requirements included reproducibility, traceability, governance, identity, user personas, and sustainability telemetry.
- Final report-outs tied system architecture to compiler IRs, resource management interfaces, runtime architecture, and control software architecture.

## Conclusions

- Classical compute should be split into QEC/control classical compute and application-level classical compute.
- Tight integration should include latency, bandwidth, co-location, policy, administration, observability, identity, and software coordination.
- Traceability may be a more realistic near-term goal than full reproducibility for hard quantum workflows.
- Quantum chemistry was proposed as a strong end-to-end workflow candidate for early fault-tolerant co-design.

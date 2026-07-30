# OpenQSE Compilers Session 01 Main Points

Source: `openQSE-compilers-session-01-transcript.docx`

## 1. What Were the Main Points Discussed?

- The compiler working group should support OpenQSE goals around portability, interoperability, neutral interfaces, and HPC-QC integration.
- The compiler layer should act as middleware between quantum SDKs and QPU/vendor systems, translating between different representations where needed.
- The group discussed the difference between compiler IRs and interchange formats.
- FTQC compilation was identified as a central issue because it differs substantially from NISQ compilation.
- The group discussed how responsibilities should be divided among SDKs, OpenQSE middleware, QPU-side compilation, and runtime systems.
- Several compiler infrastructure approaches were compared, including Qiskit, MLIR-based dialects, Hugr-like graph representations, and higher-level abstract-machine ideas.
- The group discussed dynamic circuits, mid-circuit measurement, feed-forward, real-time versus near-time execution, QEC-aware compilation, and PBC/PPR-style FTQC representations.

## 2. What Conclusions Were Reached?

- Use "compiler" as the broad term for the OpenQSE workstream rather than relying on "transpiler."
- Treat compiler IRs as primarily in-memory representations used during compilation, and interchange formats as serialized formats used between systems.
- Treat MLIR as a framework for defining IRs, not as a single IR itself.
- The OpenQSE compiler effort should not replace all SDK or vendor compiler work; it should define interfaces, translation paths, adapters, and middleware responsibilities.
- FTQC and QEC requirements should be included in the working group's scope and in the planned survey paper.
- The reference implementation should start modestly by connecting existing components and grow as the group learns more.
- The survey paper should inventory existing compiler IRs, passes, dynamic-circuit capabilities, and FTQC/QEC requirements to determine where common infrastructure is possible.

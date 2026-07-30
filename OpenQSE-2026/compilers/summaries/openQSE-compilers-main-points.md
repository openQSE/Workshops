# OpenQSE Compilers Main Points

Sources: `openQSE-compilers-session-01-report-out-summary.md`, `openQSE-compilers-session-01-detailed-summary.md`, `openQSE-compilers-session-01-main-points.md`, `openQSE-compilers-session-02-report-out-summary.md`, `openQSE-compilers-session-02-detailed-summary.md`, `openQSE-compilers-session-02-main-points.md`

## Session 01: Compiler Scope, Terminology, and Runtime Boundaries

### 1. What were the main points discussed?

- The compiler working group was framed as middleware supporting OpenQSE portability, interoperability, neutral interfaces, and HPC-QC integration.
- The compiler layer should sit between user-facing SDKs and QPU/vendor-facing execution systems, translating between SDK outputs, compiler IRs, circuit interchange formats, and execution formats.
- The group clarified the distinction between compiler IRs and interchange formats. Compiler IRs are primarily in-memory representations used during passes, while interchange formats are serialized formats used between tools or systems.
- OpenQASM 3 was discussed as an example interchange format, while MLIR dialects such as CUDA-Q Quake were discussed as compiler IR examples. MLIR itself was treated as a framework for defining IRs, not as a single IR.
- The group discussed boundaries among SDK compilation, OpenQSE middleware compilation, QPU/vendor-side compilation, and runtime behavior.
- FTQC compilation was identified as central because it differs sharply from NISQ compilation in cost models, representations, QEC requirements, and hardware assumptions.
- FTQC topics included T-gate and magic-state costs, QEC-aware representations, lattice-surgery or PBC/PPR-style representations, runtime QEC operations, and differences between stationary and moving-qubit systems.
- The group discussed hybrid quantum-classical functionality, including mid-circuit measurement, feed-forward, dynamic circuits, control flow, and real-time versus near-time execution.
- Several compiler infrastructure directions were compared, including Qiskit, MLIR-based dialects, Hugr-like graph representations, Julia-based high-level IR work, and higher-level quantum abstract-machine ideas.
- A lightning talk argued that circuits are too low-level as a final programming abstraction and that better quantum abstract machines are needed for usability, portability, and layer-by-layer refinement.

### 2. What conclusions were reached?

- Use "compiler" as the broad term for the OpenQSE workstream rather than relying on "transpiler."
- Treat compiler IRs as representations used inside compilation pipelines and interchange formats as serialized representations exchanged between systems.
- Treat MLIR as infrastructure for defining IRs and dialects, not as a single quantum IR.
- The OpenQSE compiler effort should not replace all SDK or vendor compiler work. It should define interfaces, translation paths, adapters, middleware responsibilities, and reusable integration points.
- The reference implementation should start modestly by connecting existing components and defining interfaces rather than attempting to implement a complete compiler stack from scratch.
- FTQC and QEC requirements belong in the compiler working group's scope, including in the planned survey paper.
- OpenQSE should not treat circuits as simple static gate lists if it wants portable support for hybrid quantum-classical execution.
- The compiler/runtime boundary remains open. Some compilation can happen ahead of execution, while runtime QEC or latency-sensitive behavior may need to occur close to the control system.
- The group should use the survey paper to inventory compiler IRs, passes, dynamic-circuit capabilities, FTQC/QEC requirements, and opportunities for shared compiler infrastructure.

## Session 02: Compiler Gaps, Hardware Interfaces, Testing, and Milestones

### 1. What were the main points discussed?

- The second session included lightning talks on an MLIR-based FTQC compiler and functional coverage testing for OpenQASM 3.
- The MLIR-based FTQC compiler discussion covered custom dialects, logical qubits, error-correction code assignment, syndrome insertion, gate decomposition, magic-state or distillation constructs, resource estimation, physical IR generation, backend-specific lowering, runtime integration, and QDMI-style device interfaces.
- The group discussed the need for topology-aware physical mapping, calibration-aware optimization, hardware characterization, and whether hardware-specific passes should live in OpenQSE middleware, vendor tooling, or both.
- The OpenQASM 3 testing talk showed that high line and branch coverage can still miss important language behavior. AST-based functional coverage can expose gaps across syntax, optional fields, values, and combinations of language constructs.
- The group identified format translation as a major OpenQSE compiler gap because resource interfaces are unlikely to define every circuit format, while SDKs and QPUs will continue to use different input and output representations.
- The group discussed whether OpenQSE should identify preferred compiler IRs and preferred interchange formats.
- Participants agreed that quantum compilation is inherently multi-IR, so preferred IRs should apply mainly at interface boundaries or major compilation stages rather than inside every pass.
- The group discussed standard compiler stage definitions, including mapping, lowering, synthesis, backend generation, and how stages can be replaced, extended, or customized.
- Application-specific and hardware-specific compiler customization was discussed as necessary for current quantum workflows, while reusable shared passes should be encouraged when common patterns emerge.
- The compiler group identified coordination needs with resource-interface, runtime, and software-architecture groups, especially around hardware constraints, target properties, compiler/runtime boundaries, and preferred interchange formats.
- Proposed milestones covered a six-month survey paper followed by twelve-month interface specifications and a prototype reference implementation.

### 2. What conclusions were reached?

- Quantum compilation will remain multi-IR and multi-format. OpenQSE should identify preferred IRs at standard boundaries while allowing internal compiler flexibility.
- OpenQSE should identify one or more preferred interchange formats where possible, while still supporting adapters and translators for multiple SDK and vendor formats.
- FTQC must remain in scope, but the first twelve-month reference implementation should be FTQC-aware rather than a complete FTQC compiler.
- Compiler stages should have clear conceptual definitions so users and developers can replace, extend, or customize parts of a pipeline without rewriting everything.
- Application-specific and hardware-specific passes are necessary, but repeated custom patterns should be candidates for reusable, parameterized compiler passes.
- The compiler group should state what hardware constraint information it needs without dictating the exact serialization format owned by other groups.
- Required target information may include qubit counts, supported gates, and target identity. Optional or custom information may include connectivity, per-qubit properties, error rates, calibration data, topology, modality-specific constraints, logical error rates, QEC capabilities, and supported runtime operations.
- The group accepted the six-month survey paper as the near-term milestone. The survey should cover quantum compiler IRs, passes, compiler testing, commonality and divergence across tools, FTQC/QEC requirements, scalability, and criteria for selecting preferred IRs.
- The following six months should produce requirements input for architecture documents, a compiler component/interface specification, and a prototype reference implementation.
- The working group should meet every other week for one hour, with early meetings focused on candidate compiler and IR presentations such as Qiskit, Hugr, MLIR-based compiler work, and other SDK/tool owner perspectives.

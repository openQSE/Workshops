# OpenQSE Compilers Session 01 Detailed Summary

Source: `openQSE-compilers-session-01-transcript.docx`

Note: This summary was generated from an automated meeting transcript; obvious transcription errors were normalized where the meaning was clear.

## Session Framing

The morning session opened with Michael Ferguson leading the OpenQSE compiler working group. The session goals were to align on the compiler workstream scope, begin collecting material for a survey paper, establish shared terminology, identify key gaps, and start converging on working group goals, milestones, deliverables, and participants.

The compiler workstream was framed as a middleware effort inside the broader OpenQSE architecture. The main OpenQSE goals relevant to the compiler group are portability, interoperability, neutral interfaces, and HPC-QC integration. The compiler group is expected to define compiler components that support these goals and to specify how those components interact with SDKs, resource interfaces, runtime systems, and QPU/vendor-side services.

## Scope of the Compiler Workstream

The working group discussed a compiler component positioned between user-facing SDKs and QPU-facing execution services. SDKs may already perform circuit construction, high-level transformations, native-gate lowering, or user-controlled passes. QPU providers may also perform late-stage compilation, calibration-aware mapping, topology-specific routing, QPU-specific optimization, and possibly QEC-related compilation. OpenQSE's compiler component should fill the middleware gap rather than replace everything that SDKs or vendors already do.

The proposed OpenQSE compiler responsibilities include:

- defining compiler components in the OpenQSE reference architecture;
- choosing preferred circuit interchange formats and preferred compiler IRs for OpenQSE reference work;
- translating between SDK outputs and QPU/vendor-facing inputs;
- creating adapters for SDKs;
- exposing clear interfaces to resource interfaces, runtime systems, and other workstreams;
- developing a reference implementation that initially glues existing components together rather than attempting to implement a complete compiler stack from scratch.

The group emphasized that OpenQSE should be able to add new SDKs and QPUs over time. The compiler layer therefore needs to be flexible enough to translate between different circuit formats and compilation expectations.

## Terminology Alignment

The group spent time clarifying terms so later work can be more precise.

A compiler IR was described as a representation used during compilation and optimization passes, usually as in-memory data structures. Examples included CUDA-Q's Quake MLIR dialect and other compiler-internal dialects or graph structures.

A circuit interchange format was described as a serialized format used to send circuits or programs between tools, SDKs, services, or QPUs. OpenQASM 3 was discussed as an example of this category.

MLIR was discussed as a framework for defining IRs and compiler dialects, not as a single quantum IR by itself. Participants noted that MLIR provides utilities, dialect infrastructure, and pass-writing conventions, but that pass sharing still generally requires agreement on the same dialect or compatible interfaces.

The group leaned toward using "compiler" as the broad umbrella term for quantum circuit/program transformation. "Transpiler" was treated as historically important in Qiskit and common in the community, but less desirable as the general OpenQSE term. More specific words should be used for specific stages when needed, such as mapping, lowering, synthesis, or hardware synthesis.

## Key Technical Problems

The session identified several compiler problems that OpenQSE needs to address.

One problem is providing a consistent level of hybrid quantum-classical functionality across different SDKs, QPUs, and vendor systems. Static OpenQASM 2-style circuits are comparatively easy to move around; hybrid quantum-classical programs with mid-circuit measurement, feed-forward, conditionals, loops, and classical computation are much harder to support portably.

Another problem is translating between many input formats and many output formats. Different SDKs and vendors expose different representations, and OpenQSE cannot assume that everyone will use a single format.

A third problem is defining the boundary between SDK compilation, OpenQSE middleware compilation, QPU-side compilation, and runtime behavior. For a given workflow, some compilation may happen in the SDK, some on an HPC node or OpenQSE service, and some inside the QPU provider's service.

FTQC compilation was identified as a major concern. Participants agreed that FTQC compilation is fundamentally different from near-term/NISQ compilation, while many other OpenQSE workstreams may not differ as sharply between NISQ and fault-tolerant systems.

## Reference Architecture Concepts

Michael presented a compiler architecture in which multiple SDKs feed adapters, adapters translate into an OpenQSE compiler IR, compiler passes operate on that IR, and the system emits one or more interchange formats or vendor-facing formats for execution.

The group discussed the likely responsibilities of each side of the workflow:

- SDK side: circuit/program construction, Hamiltonian-to-circuit generation, native-gate compilation, user-controlled transformations, and possibly higher-level optimizations.
- OpenQSE compiler side: translation between interchange formats, optional native-gate compilation, algorithmic-to-physical-qubit mapping, FTQC/QEC-aware compilation, and user-controllable transformations where needed.
- QPU/vendor side: device-specific native-gate lowering, calibration-aware mapping, topology/routing, optimization around native gates and constraints, and possibly error-correction compilation.

The reference implementation was expected to start modestly. Rather than being responsible for all possible compiler functionality, it should connect existing components, define interfaces, and grow toward a reusable open compiler ecosystem.

## Community Compiler Ecosystem

One proposed role for the compiler working group is broader than OpenQSE itself: building a community around reusable compiler passes and open compiler infrastructure. Participants discussed the value of having an agreed IR or at least compatible IRs so that passes can be shared across tools, vendors, and research projects.

The group also acknowledged a tension: every compiler effort tends to create its own representation because that is often easier locally, but too many unrelated IRs prevent meaningful pass reuse. The survey paper is intended to identify whether there is enough commonality in existing IRs and passes to support a shared ecosystem.

## Classical Compiler Background

The session included a review of classical compiler architecture using LLVM as the reference example. A classical pipeline was described as source language frontends lowering into LLVM IR, optimization passes operating on LLVM IR, and backends generating target-specific machine code.

The value of LLVM IR was described as enabling reuse: many languages can share optimization passes, and many hardware targets can share frontend work. This analogy was used to motivate why a shared or preferred IR could be useful for OpenQSE.

MLIR was described as a multi-level IR framework in the LLVM ecosystem. It allows dialects, lowering between dialects, and mixing dialects in one program. Quantum examples mentioned included PennyLane/Catalyst and CUDA-Q/Quake. Its ability to combine classical and quantum dialects was presented as potentially relevant for hybrid quantum-classical programs.

## NISQ Compilation Discussion

For NISQ systems, the group discussed a simplified pipeline from a logical circuit to native gates, mapping onto physical qubits, and device-specific optimization.

Participants noted that the real pipeline is more complex than a simple decomposition-then-mapping sequence. Mapping can influence which native gates are available because hardware may be heterogeneous across qubits or couplers. Optimization is also needed after lowering to native gates because native-gate decomposition can be expensive or non-optimal.

## FTQC Compilation Discussion

A substantial part of the morning was devoted to why FTQC compilation differs from NISQ compilation.

For fault-tolerant systems, arbitrary rotations typically need approximation into a fault-tolerant gate set such as Clifford+T. Optimization objectives also change: on NISQ devices one may optimize CNOTs or error-prone two-qubit gates, while in FTQC the expensive resource may be T gates, magic states, or distillation capacity.

For stationary-qubit architectures, such as surface-code-oriented systems, Michael sketched a possible path from logical circuits to Clifford+T, then to poly-product rotations or similar forms, then to QEC code-deformation representations, floorplans or microarchitectures with magic-state factories, runtime QEC operations, and eventually physical gates or pulses.

For moving-qubit or QLDPC-style architectures, the group discussed the need to represent operations at the level of QEC blocks, logical qubit movement, transversal operations across blocks, and physical movement constraints. The transcript suggests that the right IRs for these systems remain research questions.

The group discussed whether some compilation should happen ahead of time and some at runtime. Runtime QEC operations may need to be handled close to the control system, while earlier compiler stages may need global circuit information. This boundary is still unclear.

## Mid-Circuit Measurement and Feed-Forward

Michael presented mid-circuit measurement and feed-forward as important capabilities for future algorithms. Participants discussed examples where mid-circuit measurement and classical feed-forward can provide major algorithmic savings, including large speedups in some cases and reduced rotation counts in Quantum Fourier Transform-style circuits.

The discussion reinforced that OpenQSE should not treat circuits as simple static gate lists if the goal is portable support for hybrid quantum-classical execution.

## Lightning Talk on Quantum Abstract Machines

Santiago gave a lightning talk focused on lessons from classical computing history and the need for better quantum abstract machines.

The talk emphasized that compilers should satisfy programmer needs, including iterative development, predictable turnaround, readable programs, and support for idioms that appear repeatedly across programs. The argument was that good abstract machines historically solve much of the algorithmic usability problem, while current quantum abstract machines are often closer to simulators than good programming targets.

The talk highlighted the performance-expressiveness tradeoff and argued that quantum compiler architecture should preserve enough expressiveness for algorithm discovery while still enabling optimization. The speaker also argued for opportunistic refinement across layers: different layers should be able to make progress independently while still exposing well-defined interfaces when lower-level detail is needed.

Circuits were described as too low-level to be the final programming abstraction. The speaker argued for annotation semantics, representation independence, compositionality, and better high-level concepts for quantum-classical programming. Large-scale FTQC may shift some work from traditional compilation toward generation, validation, runtime mediation, and infrastructure-aware execution.

The talk included a quantum abstract machine project implemented in Rust. It includes a notion of microarchitecture, quantum and hybrid instructions operating on variables rather than individual qubits, a compact binary, runtime or VM execution, calibration-aware choices, and processor flags for quantum regions such as superposition, interference, and entanglement. The speaker said it had been demonstrated on IBM hardware.

## Qiskit, MLIR, and Other IR Discussions

The post-break discussion compared several compiler infrastructure directions.

Matt from IBM described Qiskit as already having a similar architecture to the proposed OpenQSE compiler layer: a vendor-agnostic compiler framework, an internal IR for near-term circuits, an IR-agnostic pass manager, generic passes, and backend-specific code generation. He described Qiskit as able to take higher-level representations, lower them into circuits, optimize them, and target hardware.

For FTQC, Matt noted that some higher-level logical optimizations may carry over from NISQ compilation, but much of the middle and lower pipeline is new. Qiskit is moving toward a new FTQC middle IR that is more PBC/PPR-like at its core, with additional structure to support optimization and implementation efficiency.

There was a detailed debate about MLIR. The ORNL/MLIR position was that MLIR provides standardized infrastructure for dialects, passes, transformations, and reuse, and that it can support custom quantum dialects as well as classical control-flow dialects. The IBM/Qiskit concern was that LLVM/MLIR has high maintenance cost, is built around classical compiler assumptions, often forces qubits into memory-like semantics, and may not scale or perform well for quantum-specific workloads. Qiskit instead uses Rust data structures with C and Python APIs, allowing passes to be written from different languages while keeping a performant core.

ORNL also discussed a Julia-based high-level IR effort intended to make pass writing more expressive and easier than working directly in MLIR. The group noted that language-integrated IRs can be productive, but may make pass sharing harder if they become too tied to a host language.

## Dynamic Circuits and Runtime Constraints

The group discussed dynamic circuits, control flow, and real-time versus near-time computation.

Hugr from Quantinuum was discussed as a graph representation oriented toward richer dynamic circuits and embedded classical control. Participants contrasted this with Qiskit's DAG circuit, which began as a static circuit representation and later gained control-flow support.

There was agreement that real-time execution within the coherence time of a circuit remains fundamentally constrained. Unbounded loops or arbitrary general-purpose computation cannot be treated as real-time circuit behavior. Participants distinguished real-time circuit execution from near-time classical processing, such as runtime services or coprocessing outside the coherence-time-critical path.

The group expected that this split between real-time and near-time behavior will continue even in fault-tolerant systems, because fault tolerance reduces errors but does not remove execution-time constraints.

## PPR/PBC, QEC, and FTQC Requirements

Participants discussed poly-product rotations, PBC/PPR-like forms, and how they interact with conditionals, measurements, and control flow. The group treated this as active research rather than a settled engineering problem.

The upcoming Qiskit FTQC IR was described as PBC-like at its core, with additional features added for optimization and implementation. Future versions may add basic block and control-flow graph structure, including ways to outline repeated blocks to reduce program size.

The group discussed possible parallelism in large fault-tolerant machines. Parallel basic blocks, magic-state factory bottlenecks, architecture-specific synchronization, and the entanglement structure of the algorithm can all affect how much parallelism can be exploited.

The group concluded that the survey paper should probably include at least some coverage of QEC strategies and FTQC architecture assumptions, because those assumptions define what an IR must represent and what compilation passes must do.

## Decisions and Working Agreements

The morning session produced several tentative decisions or working agreements:

- Use "compiler" as the broad term for the OpenQSE workstream.
- Distinguish compiler IRs from interchange formats.
- Treat MLIR as a framework for defining IRs, not as a single IR.
- Prefer precise stage names such as mapping, lowering, synthesis, or hardware synthesis when discussing specific transformations.
- Include FTQC and QEC requirements in the compiler working group's scope.
- Use the survey paper to inventory existing quantum compiler IRs, passes, dynamic-circuit capabilities, and FTQC/QEC requirements.
- Keep the reference implementation modest at first, focused on connecting components and defining interfaces rather than replacing existing SDK and vendor compilers.

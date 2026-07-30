# OpenQSE Compilers Session 02 Detailed Summary

Source: `openQSE-compilers-session-02-transcript.docx`

Note: This summary was generated from an automated meeting transcript; obvious transcription errors were normalized where the meaning was clear.

## Session Framing

The afternoon session resumed the OpenQSE compiler working group with a smaller group than originally expected. Michael Ferguson reintroduced the group, recapped that the morning had covered working group scope and terminology, and adjusted the schedule around lightning talks, gap discussion, milestones, and the final report-out.

Participants included representatives or contributors connected to HPE, ORNL, IBM/Qiskit, Amazon Braket, Quantinuum/Hugr discussions, and university or intern researchers. The transcript includes many automatic transcription errors in names, so this summary focuses on roles and topics rather than attempting to reconstruct every attendee name.

## Lightning Talk: MLIR-Based FTQC Compiler

The first lightning talk presented work on an FTQC compiler intended to become open source. The compiler uses MLIR-style infrastructure with custom dialects and transformation passes. The goal is to support domain-specific customization, optimization, lowering, analysis, and backend-specific generation for fault-tolerant quantum compilation.

The compiler can take high-level quantum input, apparently including OpenQASM-like input and MLIR text or IR, and pass it through a pipeline of transformations. For FTQC, the team is defining dialects for logical qubits and error-correction-related concepts. The representation can associate qubits or operations with specific error-correction codes, such as surface-code-oriented forms and other codes under investigation.

The described pass pipeline includes:

- assigning or representing error-correction codes;
- generating error-correction structures;
- inserting syndrome-related operations;
- decomposing gates;
- introducing magic-state or distillation-related constructs;
- lowering into a physical IR;
- running analysis passes such as resource estimation;
- producing backend-specific formats such as JSON.

The speaker described interoperability aspirations with other IRs such as QIR, while noting that full interoperability is not currently implemented. The compiler is being connected to a runtime layer and QDMI-style device interfaces. One example path lowers to an IQM-specific JSON format and uses vendor tooling or Qiskit routing where the FTQC compiler does not yet have its own topology-aware mapping pass.

Future work includes adding a compiler pass that can account for hardware topology and physical mapping directly, reducing reliance on vendor-specific routing. Participants discussed the role of calibration data, hardware characterization, and whether all hardware-aware optimization should live in the compiler or whether vendor-provided passes should be reused.

The speaker argued that MLIR's value is standardization: it provides a way to define custom dialects, data structures, transformations, and passes without building all compiler infrastructure from scratch. MLIR also provides reusable dialects for classical control flow and conditions, which may help represent hybrid quantum-classical programs. The group also noted that some lowerings may need extensions because not every target format maps one-to-one from an MLIR representation.

## Lightning Talk: Functional Coverage for OpenQASM 3

The second lightning talk focused on language testing rather than compiler architecture. The speaker presented a functional coverage method for OpenQASM 3.

The motivating example was a bug triggered by a negative pi rotation around X. The parser or AST walker treated `pi` as a string in one path and then attempted to negate it. Traditional unit tests had not covered that exact language case. The speaker also noted that customer privacy constraints prevent the service team from inspecting arbitrary submitted customer circuits, so they need systematic test generation rather than relying on real customer programs.

The key argument was that line coverage and branch coverage of compiler implementation code are not enough. A compiler can have very high source-code coverage while still failing to exercise many meaningful language constructs and combinations.

The method maps the AST into a SQL database. As test programs are parsed, AST nodes, parent-child relationships, fields, optional values, scalar values, lists, and enumerated values are recorded. Coverage points are generated from the AST schema and from shapes such as whether an optional initializer is present or absent. Values can be bucketed, such as distinguishing positive and negative integers or known integer widths.

The system also supports cross coverage, where the test suite is evaluated against combinations of language features. For example, binary expressions can be checked across operators and left-hand/right-hand operand types. The speaker said much of the coverage growth came from binary expressions, which produced thousands of relevant coverage points.

The results were substantial. The same codebase reportedly had about 99.2% line coverage and roughly 98% branch coverage, but only about 5.5% language coverage under this functional coverage scheme. Adding roughly 400 AI-generated tests over about two days increased language coverage to about 92.6% and found 46 new bugs.

The approach is intended to be language-agnostic. The base coverage table can be generated from an AST definition or grammar, and the speaker expects it could apply to other languages described by an extended grammar or by an AST schema. Future uses include filling remaining gaps, constrained fuzzing, recursive-function or semantic checks, and possible open sourcing. The SQL database was described as compact and easy to merge or parallelize.

## Gap Analysis

After the lightning talks, the group returned to compiler working group gaps and goals. Michael reviewed the working group scope from the shared Confluence page: define the middleware compiler component, specify interfaces, develop a reference implementation, create SDK adapters, and perform circuit compilation or translation needed to connect OpenQSE components.

The group reiterated that OpenQSE should not rebuild all existing SDK and vendor compiler functionality. Instead, it should define what the middleware compiler layer does and where it interacts with SDKs, QPU services, resource interfaces, runtime systems, and architecture documents.

One major gap is that resource interfaces such as QRI, QDMI, or related evolving interfaces are unlikely to define all circuit formats. SDKs will emit different formats, and QPUs or vendor services will consume different formats. The compiler working group therefore needs to handle translation between SDK outputs and QPU-facing inputs.

Another gap is the lack of a standard cross-vendor compiler portability path. Qiskit was discussed as a strong de facto ecosystem with broad adapter gravity, but the group did not want OpenQSE to rely only on one vendor-associated stack by default. The OpenQSE compiler needs to support a variety of SDKs, IRs, target formats, and backend constraints.

The group also discussed the need for clear boundaries between SDK compilation, middleware compilation, QPU-side compilation, and runtime behavior. This boundary may vary by workflow and by target device.

## Preferred IRs and Interchange Formats

The group discussed whether OpenQSE should identify preferred IRs and preferred interchange formats.

Participants agreed that quantum compilation is inherently multi-IR. A full compiler may use several internal representations, and individual passes may have local representations. Therefore, a preferred IR should not mean one universal representation for every pass. Instead, preferred IRs should be considered at standard interface boundaries or major compilation stages.

The morning distinction between compiler IR and interchange format was reaffirmed. A compiler IR is primarily in-memory and used during compilation. An interchange format is serialized and used to communicate between systems or tools.

For interchange formats, the group leaned toward identifying one or more preferred formats while still supporting multiple inputs and outputs. The purpose of a preferred format would be to nudge the broader ecosystem toward less translation work, not to prevent adapters or volunteers from adding support for other formats.

Participants also raised a concern that interchange formats should make quantum/classical boundaries clear. A useful format should allow the system to understand what is quantum, what is classical, where classical processing is expected to occur, and whether branching or multiple subtasks should be handled inside one submission or decomposed into separate tasks.

## FTQC Scope

The group agreed that FTQC compilation is in scope for the compiler working group. A participant asked whether the reference implementation should start as an FTQC compiler or as a more conventional compiler.

The answer was pragmatic: the reference implementation should be built with FTQC in mind, but the group should not promise a complete FTQC compiler within 12 months. OpenQSE may need to support some NISQ-style or current execution cases earlier. The prototype should move toward FTQC-aware architecture without overcommitting to full FTQC capability.

The group also noted that the reference implementation may eventually be replaced or substantially revised, so the first prototype should be treated as a way to learn, define interfaces, and build momentum.

## Transformation Stages and Customization

The group discussed whether OpenQSE should define standard compiler transformations. One proposal was to provide rigorous descriptions of stages such as mapping, lowering, synthesis, or similar transformations, without necessarily mandating one implementation.

Participants were comfortable with conceptual definitions if they are precise enough to be meaningful. The discussion aligned with a staged compiler model similar to Qiskit, where stages have defined expectations and can be replaced or extended. This would provide extension points without requiring users to rewrite an entire pipeline.

The group also discussed whether applications and algorithms need custom compiler passes. The consensus was yes. Current quantum compilation is still nascent, and cutting-edge applications often benefit from bespoke passes that exploit the structure of a particular algorithm and the constraints of a particular target. Examples included chemistry or SQD-style workflows where mapping or optimization is customized for the circuit and hardware topology.

At the same time, the group wanted a way to make bespoke work discoverable and reusable. If many teams write nearly identical custom passes, the ecosystem should be able to recognize that and factor them into shared, parameterized passes.

## Coordination With Other Working Groups

The compiler group identified several areas where it needs input from other OpenQSE working groups.

From the quantum resource interface or device/resource-related group, the compiler group needs an abstract representation of hardware constraints relevant to compilation. The discussion referenced a useful classification into required, optional, and custom information.

Required information might include number of qubits, supported gates, and basic target identity. Optional information might include per-qubit gates, per-qubit properties, connectivity, per-gate properties, error rates, calibration data, and topology. Custom information might include modality-specific constraints or vendor-specific information that requires corresponding custom compiler passes.

For FTQC, the compiler may also need information about logical error rates, available error-correction capabilities, whether a backend can compile parts of a circuit ahead of time, and what QEC or runtime operations are supported.

The group did not want to over-specify the serialization format for this resource data. JSON, YAML, protobuf, OpenQASM defcals, or API-based forms were all mentioned or implied, but the conclusion was that the compiler group should state what information it needs rather than dictate exactly how another group must encode it.

The group also needs coordination with the runtime working group to define the compiler/runtime boundary. Some work may need to happen before submission, while other work may happen at runtime or close to the control system. The software architecture group also needs compiler input on preferred interchange formats.

## Milestones and Deliverables

The proposed six-month milestone is a draft survey paper. The survey should cover quantum compiler IRs and passes, how existing tools test compilers, commonality and divergence across tools, FTQC and QEC requirements, and criteria for selecting one or more OpenQSE preferred IRs. The group also discussed including scalability, circuit size, and payload size if possible, especially because very large FTQC programs may produce enormous intermediate or gate-sequence representations.

The following six months should focus on draft specifications for compiler interfaces and a prototype reference implementation. The group intentionally avoided promising a specific level of quality or capability for the prototype because the effort is starting from an early stage.

The 12-month deliverables are:

- requirements input for broader OpenQSE architecture documents;
- a specification of the OpenQSE compiler component and its interfaces;
- a prototype reference implementation.

Telemetry, logging, and error reporting were acknowledged as important system capabilities, but the group decided not to make them central compiler survey topics at this stage.

## Working Group Process and Participation

The group discussed how often to meet and who could participate. Michael proposed meeting every other week for one hour. Participants considered that cadence reasonable: slower would make it hard to produce a six-month survey draft, while faster might be difficult for participants to sustain.

This cadence gives roughly 12 meetings before the six-month survey paper milestone. The first meetings are expected to gather information for the survey paper and compare candidate compiler/IR approaches.

Possible early meeting topics include:

- a Qiskit presentation explaining its compiler architecture and whether it could satisfy OpenQSE reference implementation needs;
- a Quantinuum/Hugr presentation explaining its graph representation and dynamic-circuit capabilities;
- an MLIR-based compiler presentation building on the ORNL FTQC compiler work;
- additional presentations from Braket or other SDK/tool owners.

Participants from IBM/Qiskit, Amazon Braket, ORNL, and interested individuals indicated possible involvement, though some participation would need to be confirmed through the relevant teams. Reference implementation staffing and ownership were deferred until later.

## Report-Out Preparation

Near the end of the session, the group prepared its report-out. The main points to report were:

- the compiler group clarified terminology around compiler IRs, interchange formats, MLIR, and the term "compiler";
- the group identified key gaps around format translation, cross-vendor portability, hybrid quantum-classical support, FTQC requirements, hardware constraint information, and compiler/runtime/resource-interface boundaries;
- the group agreed that preferred IRs and interchange formats are useful goals, but the compiler stack will remain multi-IR and multi-format;
- the group accepted the proposed six-month and twelve-month milestones as reasonable;
- the group planned biweekly meetings and initial presentations to drive the survey paper and later interface/reference implementation work.

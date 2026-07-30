# OpenQSE Compilers Session 02 Report-Out Summary

Source: `openQSE-compilers-session-02-transcript.docx`

Note: This summary was generated from an automated meeting transcript; obvious transcription errors were normalized where the meaning was clear.

## Highlights

The afternoon compiler working group session focused on lightning talks, gap analysis, milestones, and process. The first lightning talk presented an MLIR-based FTQC compiler effort using custom dialects and passes for logical qubits, error-correction code assignment, syndrome insertion, gate decomposition, resource estimation, physical IR generation, and backend-specific lowering. The implementation currently integrates with runtime and vendor tooling, including QDMI-style interfaces and vendor routing, while future work aims to add topology-aware physical mapping directly in the compiler.

The second lightning talk presented a functional coverage approach for OpenQASM 3 testing. The method maps AST structures into a SQL database and generates coverage points over syntax, field presence, values, and cross-products of language constructs. It showed that high source-code coverage can still miss large areas of language behavior: the team moved from about 5.5% language coverage to 92.6% by adding roughly 400 AI-generated tests, finding 46 bugs.

The group then identified OpenQSE compiler gaps and goals. A major gap is that resource interfaces are unlikely to define all circuit formats, while SDKs and QPUs will continue to use different input and output representations. The compiler group therefore needs to define translation paths, preferred IRs and interchange formats, and interfaces that support cross-vendor portability without assuming a single vendor stack.

The group agreed that quantum compilation is inherently multi-IR. Preferred IRs should apply at standard interface or stage boundaries, not necessarily inside every pass. Preferred interchange formats should be identified where possible, but the system should still accept and emit multiple formats. The group also agreed that FTQC must remain in scope, even if the first 12-month reference implementation only provides a modest, FTQC-aware prototype rather than a complete FTQC compiler.

Proposed milestones were accepted as reasonable: produce a draft survey paper in six months covering quantum compiler IRs, passes, testing, FTQC/QEC requirements, and criteria for selecting IRs; then spend the following six months drafting interface specifications and building a prototype reference implementation. The 12-month deliverables are requirements input to architecture documents, a compiler component/interface specification, and a reference implementation.

The working group planned to meet every other week for one hour. Early meetings should include presentations on candidate compiler/IR directions such as Qiskit, Quantinuum's Hugr, and MLIR-based compiler work, while also feeding the survey paper.

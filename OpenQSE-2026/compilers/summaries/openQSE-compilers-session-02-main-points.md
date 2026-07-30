# OpenQSE Compilers Session 02 Main Points

Source: `openQSE-compilers-session-02-transcript.docx`

## 1. What Were the Main Points Discussed?

- The afternoon session included lightning talks on an MLIR-based FTQC compiler and functional coverage testing for OpenQASM 3.
- The MLIR-based compiler discussion covered custom dialects, FTQC passes, error-correction code assignment, syndrome insertion, resource estimation, backend lowering, runtime integration, and hardware-aware mapping.
- The testing talk showed that normal line and branch coverage can miss important language behavior, while AST-based functional coverage can expose gaps and bugs.
- The group discussed OpenQSE compiler gaps, especially translation among SDK outputs, compiler IRs, interchange formats, and QPU/vendor input formats.
- The group discussed whether OpenQSE should identify preferred IRs and preferred interchange formats.
- The group discussed compiler stage boundaries, application-specific compiler customization, hardware constraint information, and coordination with resource-interface and runtime working groups.
- The group reviewed proposed six-month and twelve-month milestones and discussed participation cadence.

## 2. What Conclusions Were Reached?

- Quantum compilation is inherently multi-IR, so OpenQSE should identify preferred IRs mainly at interface or stage boundaries rather than require one IR everywhere.
- OpenQSE should identify one or more preferred interchange formats where possible, while still supporting multiple formats through adapters and translators.
- FTQC must remain in scope, but the first reference implementation should be FTQC-aware rather than a complete FTQC compiler.
- Compiler stages should have clear conceptual definitions so stages can be replaced, extended, or customized without rewriting an entire pipeline.
- Application-specific and hardware-specific compilation customization is necessary, but reusable passes should be encouraged when common patterns emerge.
- The compiler group needs hardware constraint information from resource-interface work, including required, optional, and custom target properties.
- The group accepted a draft survey paper as the six-month milestone and compiler interface specifications plus a prototype reference implementation as the twelve-month direction.
- The working group should meet every other week for one hour, with early meetings focused on candidate IR/compiler presentations and survey-paper input.

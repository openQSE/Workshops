# OpenQSE Compilers Session 01 Report-Out Summary

Source: `openQSE-compilers-session-01-transcript.docx`

Note: This summary was generated from an automated meeting transcript; obvious transcription errors were normalized where the meaning was clear.

## Highlights

The morning compiler working group session established the initial scope, terminology, and technical framing for the OpenQSE compiler effort. Michael Ferguson framed the compiler component as middleware that supports OpenQSE portability and HPC-QC integration by translating between SDK outputs, compiler intermediate representations, circuit interchange formats, and QPU-facing execution formats.

The group discussed the need to choose preferred representations for OpenQSE work, while still supporting multiple SDKs and vendors. A useful distinction was accepted between a compiler IR, which is primarily used in memory during compiler passes, and an interchange format, which is serialized and used to move circuits between tools or systems. OpenQASM 3 was discussed as an example interchange format, while MLIR dialects such as CUDA-Q Quake were discussed as examples of compiler IRs. MLIR itself was treated as a framework for defining IRs rather than as a single IR.

The group agreed that FTQC needs to be central to the compiler work. Participants discussed why FTQC compilation differs from NISQ compilation, including T-gate and magic-state costs, QEC-aware representations, lattice-surgery or PBC/PPR-style representations, runtime QEC operations, and hardware-modality differences between stationary and moving qubit systems.

Several open gaps were identified: consistent hybrid quantum-classical functionality across SDKs and vendors, translation between diverse input/output formats, the boundary between SDK compilation, OpenQSE middleware compilation, QPU-side compilation, and runtime responsibilities, and what information compilers need from resource interfaces.

The morning session also included background discussion on classical compiler architecture, LLVM/MLIR, NISQ compilation, FTQC compilation pipelines, and a lightning talk arguing for better quantum abstract machines and higher-level programming abstractions. The session ended with terminology alignment: use "compiler" broadly, avoid relying on "transpiler" as the main term, distinguish IRs from interchange formats, and treat final hardware-specific lowering as a more specific stage.

# OpenQSE System Architecture Session 02 Detailed Summary

Source transcript: `openQSE-sys-arch-session-02-transcript.docx`

## 1. Opening And Session Context

### 1.1 Start of substantive discussion

- The sanitized technical transcript starts with the QEC workflow discussion.
- The afternoon session focused on discussion rather than lecture-only delivery.

### 1.2 Topic shift

- The afternoon shifted from application workflows toward quantum error correction, non-performance requirements, cross-workstream coordination, and final report-outs.

## 2. Peter Grozkowski: Quantum Error Correction And Workflows

### 2.1 Purpose of the QEC talk

- Peter Grozkowski gave a lightning talk connecting quantum error correction to the workflow and system architecture discussion.
- The goal was to make QEC accessible to attendees who do not work on it daily.
- The talk connected low-level QEC loops to larger system integration requirements.

### 2.2 Basic QEC idea

- Quantum error correction uses many noisy physical qubits to encode fewer logical qubits.
- Logical information is spread nonlocally so errors can be detected and corrected.
- Codes are commonly parameterized by:
  - Number of physical qubits.
  - Number of logical qubits.
  - Code distance.
- Larger distance improves tolerance but costs more resources.

### 2.3 No universal best code

- Peter emphasized that there is no best code for all systems.
- Code choice depends on hardware resources, noise type, gate locality, and logical operation costs.
- Even superconducting systems can require different codes if their noise mechanisms differ.
- This means QEC code choice affects architecture.

### 2.4 QEC loop

- The QEC loop runs repeatedly for the lifetime of the experiment.
- The system defines logical qubits over many physical qubits.
- Syndrome measurements are performed to detect errors.
- In many architectures, syndrome measurements are implemented through data qubits, ancilla qubits, two-qubit gates, and ancilla readout.
- Syndrome data is passed to classical decoding infrastructure.
- The decoder converts syndrome information into corrections or frame updates.

### 2.5 Decoder role

- For small codes, decoding may be a simple lookup table.
- For larger codes, decoding becomes a substantial classical computation.
- The decoder must process syndrome data quickly enough to keep errors from overwhelming the computation.
- Correction may be applied physically or tracked virtually, depending on the algorithm and hardware.

### 2.6 Logical memory versus logical computation

- Logical memory only preserves a logical state.
- For memory experiments, decoding and correction can sometimes be deferred until the end.
- Logical computation requires gates between QEC cycles.
- Clifford gates allow more deferral.
- Non-Clifford gates require at least real-time decoding up to the point of the operation.
- This raises latency constraints even when physical correction can be deferred.

### 2.7 Timing requirements

- Timing depends on architecture, code, and hardware modality.
- Relevant time scales include:
  - Qubit coherence.
  - Gate and initialization time.
  - Syndrome extraction time.
  - Classical decoding time.
  - Pulse generation time if active correction is applied.
  - Data movement between QPU and decoder.
- Peter argued that QEC requires more specialized hardware than many application workflows.

### 2.8 Classical hardware for QEC

- The talk discussed FPGAs, GPUs, CPUs, and possible ASIC-like hardware.
- FPGAs can provide very low latency for some decoders.
- GPUs may provide throughput but can suffer from latency and data-movement overhead.
- CPUs can be surprisingly competitive for some decoding cases.
- The right hardware depends on the code, syndrome size, decoder algorithm, and modality.

### 2.9 Surface-code experiment example

- Peter described a recent superconducting surface-code experiment from China.
- The experiment used a distance-3 surface-code patch.
- It had 9 data qubits and 8 ancilla qubits.
- Syndrome measurements were passed to a central feedback module.
- The central module used an FPGA-based neural network decoder.
- In one experiment, corrections were passed to pulse generators and applied actively.

### 2.10 Reported timing in the example

- Classical-only cycle time was roughly 0.5 microseconds.
- Quantum syndrome extraction was roughly 1.2 microseconds.
- Total cycle time was just under 2 microseconds.
- Active correction improved fidelity relative to doing nothing.

### 2.11 QEC as a workflow issue

- QEC does not directly fall under the morning application workflows.
- Applications run above QEC.
- However, QEC is itself an interleaved quantum-classical workflow.
- It creates very strong requirements on classical compute, network latency, and hardware proximity.
- Peter asked whether future applications can ignore QEC or whether near-term systems will need application/QEC co-design.

## 3. Discussion Of QEC, Locality, And Co-Design

### 3.1 Using syndrome data beyond correction

- A participant asked whether syndrome data could also support calibration.
- This was treated as a broader example of using QEC information for system-level goals beyond correction alone.

### 3.2 Long-term user abstraction

- Peter suggested that in the long term, users may program logical circuits without knowing QEC details.
- The hardware and software stack would decide how to run those logical circuits.
- This is analogous to modern programmers not usually writing assembly.

### 3.3 Near-term configurability

- Participants questioned whether QEC details can really be hidden in the near or mid term.
- Power users may need to specify:
  - Required logical accuracy.
  - QEC code.
  - Decoder.
  - Hardware used for decoding.
  - Application-specific correction strategy.
- This was compared to configurable reliability modes in classical systems.

### 3.4 Distinguishing types of classical compute

- Laura emphasized that "classical compute" needs to be separated into categories.
- One category is classical compute for error correction and control.
- Another category is classical compute for the application workflow.
- If application-specific QEC creates dependencies between these categories, application-side classical resources may need tighter locality than expected.

### 3.5 Application-specific QEC

- Participants discussed application/QEC co-design.
- Hamiltonian simulation may benefit from QEC codes that exploit application structure such as translation invariance.
- LDPC-code co-design for Hamiltonian simulation was mentioned.
- Some participants argued this is likely how important future fault-tolerant applications should be developed.

### 3.6 Vendor exposure of QEC stack

- The room recognized that vendors may not want to expose their QEC layers.
- However, QEC researchers need to experiment with codes and decoders on real hardware.
- If QEC is hard-coded into vendor FPGA or ASIC layers, researchers may be limited to simulations.
- The group discussed whether OpenQSE should recommend architectures that allow configurable QEC research.

### 3.7 Hierarchical classical architecture near QPUs

- A proposed hierarchy emerged:
  - Very low-latency hardware for the innermost QEC loop.
  - A nearby small or medium classical/HPC resource for configurable decoding and QEC experiments.
  - Larger HPC resources at the facility or campus level for application workflows.
- The group wanted large-scale HPC experts to evaluate whether this model works for real scientific workloads.

### 3.8 Time-scale hierarchy

- The group identified several distinct time scales:
  - Tens to hundreds of nanoseconds for syndrome-to-decoder paths in fast systems.
  - Microseconds for nearby tightly connected classical resources.
  - Milliseconds to seconds or longer for application-level workflows.
- These time scales vary by modality.
- Superconducting and spin qubits may impose faster requirements.
- Trapped ions and neutral atoms may tolerate different latencies.

## 4. Tight Coupling, Integration, And Vocabulary

### 4.1 Need to define terms

- The QEC discussion returned repeatedly to terminology.
- Participants noted that "tightly coupled" and "loosely coupled" mean different things to different communities.
- Some people use tight coupling to mean low latency.
- Others use tight integration to include policy, administration, identity, and workflow cohesion.

### 4.2 Latency is not enough

- The group agreed that latency alone is insufficient.
- Bandwidth, data volume, resource co-scheduling, common administration, network reliability, software-update coordination, and security policy can all make or break a workflow.

### 4.3 Genesis integration language

- Laura referenced Genesis Summit discussions where "tight integration" meant infrastructure coordination across many layers.
- That included technical, policy, administrative, and user-experience dimensions.
- The group considered moving away from "coupling" and using more precise language around integration and latency.

### 4.4 Action around vocabulary

- Participants agreed that the workstream should help define common vocabulary.
- This vocabulary should support discussions across applications, workflows, architecture, orchestration, resource management, compilers, and software architecture.

## 5. Decoder Hardware And Scaling Discussion

### 5.1 Surface code versus LDPC decoding

- Participants noted that different error-correcting codes impose different decoding requirements.
- Surface-code decoding is relatively well studied.
- LDPC-code decoding requirements, especially hardware requirements, need more profiling.

### 5.2 Decoder performance profiling

- The group needs performance profiles for decoder algorithms.
- For each code and hardware type, OpenQSE should understand:
  - Latency.
  - Throughput.
  - Hardware footprint.
  - Data movement.
  - Variability in runtime.
  - Failure behavior.

### 5.3 Hardware options

- FPGAs may be fastest for some low-latency decoding.
- GPUs may be useful for heavier or more parallel decoding but can suffer from invocation and data-transfer latency.
- ASICs or probabilistic/specialized computing may be needed for very hard events.
- CPUs may remain relevant in some cases.

### 5.4 Rare hard syndrome events

- Masoud Mohseni discussed rare syndrome events.
- At small scale, most errors may be easy to decode.
- At large scale and deep circuit depth, rare events happen frequently enough to matter.
- Some decoding instances may map to hard computational problems in worst-case scenarios.

### 5.5 Heterogeneous decoder hierarchy

- Participants described a multi-tier decoder architecture:
  - Lightweight pre-decoders for common easy syndromes.
  - Intermediate resources for moderate cases.
  - Heavy specialized hardware for rare hard cases.
- The architecture must route decoding work based on estimated syndrome hardness.
- This has implications for facility design and QPU-adjacent compute sizing.

### 5.6 Facility design estimates

- Laura asked for rough estimates of latency and compute requirements for particular codes, logical qubit counts, and distances.
- The motivation was facility planning: data centers and quantum facilities take years to design.
- Participants referenced estimates that decoding for around 100 logical qubits could require large classical throughput under some assumptions.
- Vendor-specific estimates may exist internally but may not be easy to share.

## 6. Non-Performance Requirements Discussion

### 6.1 Shift to non-performance topics

- Laura led a shortened discussion on service, support, governance, policy, reproducibility, traceability, and user needs.
- She asked how hybrid quantum-HPC workflows can be tracked across institutions and environments.

### 6.2 Reproducibility challenge

- Participants agreed that reproducibility is difficult for hard quantum simulations.
- If a problem is hard enough to need quantum hardware, it may not be classically verifiable.
- If only one specialized system can run it, independent reproduction may be impossible.

### 6.3 Traceability as first target

- The group suggested traceability and documentation as more realistic near-term goals.
- Systems should record:
  - Hardware used.
  - Calibration state.
  - Software stack.
  - Compiler path.
  - Workflow steps.
  - Data products.
  - Resource allocations.
  - Policies and environment details.

### 6.4 Verification approaches

- Several verification categories were discussed:
  - Test smaller systems with brute-force state-vector simulation.
  - Reduce or dilute T gates and compare to efficient simulations.
  - Use tensor-network or entanglement-truncation approximations.
  - Validate predictions experimentally in a lab.
  - Consider AI or foundation-model-based approaches in the future.

### 6.5 Standard problem portfolio

- Participants suggested selecting a portfolio of standard problems.
- The portfolio should cover different workflow behaviors.
- It should include known reference results where possible.
- This would support benchmarking, reproducibility discussions, and cross-system comparison.

### 6.6 Identity, access, and roles

- Token-based authentication, JWT-style mechanisms, and role-based access were discussed.
- User personas could map to access roles.
- Data classification could determine which users or groups see which architecture layers or data products.

### 6.7 Sustainability telemetry

- Sustainability reporting was raised as another non-performance requirement.
- Classical HPC systems may already expose power and energy telemetry.
- Quantum vendors may expose similar information.
- Dashboards could report energy usage for hybrid workflows across HPC and QPU components.

## 7. User Personas And User Teams

### 7.1 Persona work

- Laura returned to the need for quantum user personas.
- She asked whether other groups have structured persona work.

### 7.2 Team-level users

- A participant from Alice and Bob argued that in real quantum-HPC programs, the "user" is often a team.
- Example roles in one chemistry-oriented team included:
  - Fault-tolerant algorithm designers.
  - Tensor-network experts for initial-state preparation.
  - Classical computing experts for separating weakly and strongly correlated regions.
  - Domain scientists.
- These roles depend on each other and should be considered as an integrated user team.

### 7.3 ORNL user data

- ORNL participants mentioned QCUP user surveys.
- These surveys collect information about user knowledge level, application frameworks, and desired applications.
- Ryan Landfield was mentioned as a contact for that effort.

### 7.4 Persona implications

- The same application may have different workflows depending on user maturity.
- A power user may work close to the hardware.
- A later domain scientist may use higher-level interfaces, libraries, or workflow tools.
- These differences should inform architecture and abstraction choices.

## 8. Cross-Workstream Coordination

### 8.1 Other workstreams

- The group discussed overlap with:
  - Software architecture.
  - Quantum resource/resource interface.
  - Compiler infrastructure.

### 8.2 Common language as first coordination need

- Participants said the workstreams are not always speaking the same language.
- Ambiguous terms include:
  - Workflow.
  - Application.
  - Architecture.
  - Orchestration.
  - Integration.
  - Coupling.
  - Tight versus loose.
  - Latency.
  - Resource estimation.
  - Scheduling.

### 8.3 Extracting workflows from applications

- The group noted that algorithm experts naturally focus on algorithm details.
- The workflow group needs to abstract those details into workflow patterns.
- Two different applications may have the same workflow pattern.
- Conversely, the same application may create different workflows for different personas.

### 8.4 Candidate end-to-end workflow

- Quantum chemistry was proposed as a first end-to-end workflow candidate.
- This aligns with the DOE blueprint's expected early fault-tolerant success area around 2028-2029.
- The workflow should be analyzed for:
  - Power users close to the hardware.
  - Next-wave users needing more abstraction.
  - Different resource and scheduling needs.
  - Compiler and runtime requirements.
  - System placement and telemetry.

### 8.5 Role of the workflow group

- The group may act as a cross-cutting coordinator.
- It can gather requirements from applications.
- It can feed scheduling needs to the resource interface group.
- It can feed hardware and dynamic circuit requirements to the compiler group.
- It can feed runtime and system boundary requirements to software architecture.
- It can gather feedback from those groups and refine workflow requirements.

### 8.6 Metrics

- Participants argued for metrics beyond qubit count and fidelity.
- End-to-end workflow metrics should include:
  - Classical compute.
  - Quantum compute.
  - Communication.
  - Scheduling delay.
  - Idle time.
  - Telemetry state.
  - Reproducibility metadata.
  - Administrative or policy integration.
- QDMI-style telemetry and dashboards may support this.

## 9. Compiler Working Group Report Out

### 9.1 Terminology decisions

- The compiler group distinguished compiler IR from interchange format.
- Compiler IR is used during compilation, often as an in-memory circuit representation.
- Interchange format is a serialized circuit representation used to connect systems.
- The group prefers "compile" over "transpile" as the general term.

### 9.2 Identified gaps

- Fault-tolerant quantum compilation has not been fully demonstrated.
- Each QPU vendor may require a different circuit format as input.
- Different SDKs produce different circuit formats as output.

### 9.3 Scope

- The compiler group scope includes:
  - Fault-tolerant quantum computing.
  - Quantum error correction.
  - Hybrid quantum-classical circuits.
- The group will not assume all circuits are unitary.

### 9.4 Customizable compilation

- Cutting-edge algorithms may need customized compiler passes.
- The group expects a coarse-grained pipeline with extension points or plugins.
- LLVM was used as an analogy for a general compiler with customization points.

### 9.5 Preferred IR and interchange formats

- The group aims to define OpenQSE preferred compiler IRs.
- It also aims to define preferred circuit interchange formats.
- Multiple IRs may be needed, especially for fault-tolerant quantum computing.

### 9.6 Hardware constraint information

- Compilers need abstract hardware constraints from resource or circuit-launching interfaces.
- Required information may include:
  - Number of qubits.
  - Modality.
  - Accepted interchange formats.
- Optional information may include:
  - Per-qubit noise.
  - Supported operations.
- Custom information may support vendor-specific extensions.

### 9.7 Compiler milestones

- Six-month milestone: survey paper on compilers, IRs, passes, and QEC-driven requirements.
- Later 12-month milestone: draft compiler interface specification and prototype reference implementation.
- The group plans biweekly meetings.

## 10. Workflows/System Architecture Report Out

### 10.1 Session structure reported

- The workstream summarized its day:
  - Introductory framing.
  - Provocative statements.
  - Three workflow examples.
  - QEC lightning talk.
  - Non-performance requirements.
  - Cross-workstream discussion.

### 10.2 Key observations

- Terminology is a pervasive issue.
- Integration should not be defined by latency alone.
- The group struggled to distinguish applications, workflows, architectures, and orchestration.
- QEC creates system-level hardware-software integration requirements.
- QPU telemetry and calibration data may support QEC resource estimates.

### 10.3 Proposed role and milestones

- The workstream may own an end-to-end co-design workflow example.
- Quantum chemistry is the likely first candidate.
- The workflow should be analyzed across user personas.
- The group should gather and share requirements with the other workstreams.

## 11. Resource Management And Interface Report Out

### 11.1 Workstream focus

- The group discussed existing resource management interface efforts.
- QRMI and QDMI were presented as active communities.
- QFW was described as ORNL's framework for the quantum-HPC ecosystem.

### 11.2 Nomenclature

- A nomenclature discussion reached agreement on:
  - Quantum resource.
  - Workload manager.
  - Workflow.

### 11.3 Stakeholder input

- The group heard from workload manager developers and operations teams.
- Slurm HRES was discussed as a new feature relevant to quantum resource integration.
- Flux from Livermore was described as a hierarchical scheduler.
- PBS, IBM LSF, Grid Engine, Kubernetes, and Flux integrations were mentioned in relation to QMI/QRMI efforts.
- Operations perspectives were also included.

### 11.4 Methodology

- The long-term goal is a standard, community-driven interface for integrating quantum systems.
- The proposed process is:
  - Talk to stakeholders.
  - Document requirements.
  - Let interface developers produce APIs.
  - Evaluate through QFW and other testbeds.
  - Iterate.

### 11.5 Milestones

- Establish recurring workstream meetings.
- Use QFW to collect usage patterns and later benchmarking data.
- Produce papers and an internal draft specification around early next year.
- Evaluate Slurm HRES.
- Move from qualitative analysis of QDMI/QMI to quantitative analysis.

### 11.6 Questions raised

- Does ORNL's IQM system have enough realistic usage patterns to evaluate interface tradeoffs?
- Should device information formats be vendor-defined and flexible or standardized?

## 12. Software Architecture Report Out

### 12.1 Runtime architecture session

- The morning software architecture session was led by AMD.
- The group proposed an evidence-driven architecture specification process.
- They want to inventory existing runnable prototypes.
- They want to evaluate workflows across low-latency/high-latency and low-HPC/high-HPC quadrants.
- They want real applications to inform architecture.

### 12.2 Runtime requirements

- The group discussed runtime layers that are application-facing and target heterogeneous architectures.
- Users should ideally see logical targets.
- Physical details still need to be available for performance and practical execution.
- The group discussed what belongs in compiled objects versus runtime parameters.

### 12.3 Runtime milestones

- Form a small working group.
- Classify existing prototypes by layer.
- Set experimental quadrants.
- Deploy reference prototypes.
- Produce a white paper and possible demos.

### 12.4 Control software architecture session

- The afternoon focused on control electronics and control software.
- The goal is to reframe the control stack as a software challenge.
- Rather than hand-tuning laboratory instruments, the stack should be software-defined.
- Dynamic compilers, APIs, data locality, and intelligent control are central.

### 12.5 Control stack ownership

- The group sees its scope as:
  - Physical and software interfaces.
  - Fast feedback protocols.
  - Scaling data bottleneck analysis.
  - Handing intermediate representations to the compiler group.

### 12.6 Control stack questions

- Where is the line between pulse-level access and logical QEC primitives?
- What must happen on chip?
- What can happen asynchronously in software?
- How can open vendor-neutral contracts avoid proprietary lock-in?

### 12.7 Control stack milestones

- Survey what exists.
- Document latency, bandwidth, and integration requirements.
- Define hardware-software boundaries.
- Specify fast feedback protocols.
- Build a reference implementation on a real hardware testbed.
- Create a Slack channel, recurring meetings, and contributor outreach.

## 13. Closing And Future Meeting Cadence

### 13.1 Acknowledgements

- The organizers thanked ORNL staff and leadership for the venue and support.
- The group recognized the momentum created by the in-person event.

### 13.2 Future in-person meetings

- Participants discussed meeting around major conferences.
- SC in Chicago was suggested as a possible gathering point.
- Options included meeting before or during SC.
- A Google poll was planned to collect input.
- The goal would be working-group execution time and cross-workstream synchronization, not just status updates.

### 13.3 Other opportunities

- IEEE Quantum Week was mentioned as an informal near-term opportunity.
- A Sunday, November 15 workshop at SC on software framework and workload management for the quantum-HPC ecosystem was announced.
- A possible OpenQSE special session was mentioned.

## 14. Overall Themes From Session 2

- QEC is the clearest driver of very tight quantum-classical integration.
- QEC requirements are modality-, code-, decoder-, and application-dependent.
- OpenQSE needs shared language before it can standardize interfaces.
- Traceability is an urgent and realistic near-term goal for hard hybrid workflows.
- User personas should include both individuals and integrated teams.
- Quantum chemistry is a practical first candidate for end-to-end co-design analysis.
- Workstreams need coordinated deliverables around compilers, resource interfaces, runtime architecture, and control software.

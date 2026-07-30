# OpenQSE Runtime Session 01 Detailed Summary

Source: `openQSE-runtime-session-01-transcript.docx`  
Meeting recording: July 24, 2026, 1:50 PM  
Duration: 2h 41m 18s  
Focus requested: milestones and deliverables

## Transcript Note

The source is an automated meeting transcript and contains recognition errors. This summary normalizes clear technical terms where the context supports it, including QPU, QEC, Qiskit, Slurm, CUDA/HIP, FMO2, VQE, pCCD, GAMESS, FCIDUMP, FPGA, and ISA circuits. A few project or interface names were unclear in the transcript, so they are described functionally rather than treated as confirmed names.

## Executive Summary

The meeting used a concrete quantum chemistry workflow to ground a broader discussion about quantum-HPC software architecture and runtime interfaces. The application showed why future systems need to support heterogeneous CPU/GPU/FPGA/QPU execution, multiple coupling regimes, hierarchical task graphs, resource-aware scheduling, and enough telemetry for algorithm/runtime co-design.

The group aligned on a pragmatic path: do not start by standardizing a final vendor-neutral runtime API in the abstract. Instead, define a small working group, collect and deploy existing prototypes, run common experiments against them, and use that evidence to derive runtime criteria, object models, and interface boundaries.

The strongest deliverable theme was a reference-prototype plus experimental-framework effort. Participants repeatedly proposed a "bake-off" or hackathon-like activity where multiple runtime/workflow approaches can be compared against shared target workflows, including low/high latency and low/high HPC intensity cases. A white paper or technical deployment, potentially targeted around SC/arXiv timing, was discussed as a practical report-out artifact.

## Meeting Structure

The substantive agenda had three parts:

1. An application use case: fragment molecular orbital based VQE workflows for fragment-based quantum chemistry.
2. A software architecture overview: where a runtime layer sits in an open quantum-HPC stack.
3. A heterogeneous device runtime proposal: device-side runtime concepts inspired by CUDA/HIP/HSA and HPC/ML runtimes.

The discussion then moved into milestones, deliverables, criteria, and how to compare multiple existing or emerging implementations.

## Application Use Case: Fragment-Based Quantum Chemistry

The application presentation focused on distributed quantum computing for molecular clusters. The motivation is that chemically relevant systems will exceed the capacity of near-term and even mid-term quantum computers, so classical fragmentation and embedding methods are needed to split large chemistry problems into smaller units that can be processed in parallel.

The core method discussed was the fragment molecular orbital method. A chemical system is partitioned into non-overlapping fragments at the monomer level, with dimers and potentially higher-order terms used to capture inter-fragment interactions. In the FMO2 case, total energy is reconstructed from monomer energies plus dimer corrections. The preprocessing is handled classically, using a self-consistent electrostatic embedding calculation, with GAMESS mentioned as the classical chemistry code producing fragment Hamiltonians.

The near-term quantum refinement uses VQE-like workflows on the fragment Hamiltonians. The presenter described a pair coupled-cluster doubles style ansatz, which reduces the qubit requirement by encoding fermion pairs and simplifies measurements to all-Z, all-X, and all-Y bases. This reduces the quantum workload to a small number of circuit measurement bases per fragment and allows expectation values to be reused for classical orbital optimization.

The workflow has a natural hybrid structure:

1. Define the chemical system and proposed fragments.
2. Run classical FMO Hartree-Fock preprocessing on HPC resources.
3. Emit embedded monomer/dimer Hamiltonians, with FCIDUMP mentioned as the file format.
4. Map each fragment Hamiltonian, gradient, and Hessian onto qubits.
5. Partition observables into commuting sets and build parameterized ansatz circuits.
6. Initialize quantum backends, virtual or physical, with coupling maps.
7. Compile and transport circuits through routing, layout optimization, gate optimization, and error suppression such as twirling.
8. Execute the VQE loop using QPU estimators, expectation values, error mitigation, and classical optimizers.
9. Run classical orbital optimization using the harvested expectation values.
10. Reconstruct global energies and properties from the fragment results.

Current and near-term application milestones mentioned:

- Prior or ongoing studies of helium clusters and salt droplets.
- Extension work toward rare-earth element solvation.
- Integration into Amir's quantum framework for resource management and scheduling.
- Exploration of parallel execution across heterogeneous QPUs, with Quantum Brilliance mentioned in that context.

Deliverable implication: this workflow is a useful reference application for evaluating runtime layers because it contains multiple coupling regimes. The preprocessing and postprocessing are loosely coupled HPC tasks. The fragment-level VQE loops require medium coupling between classical and quantum resources. Future fault-tolerant variants with mid-circuit measurement and feed-forward would exercise tighter timing and synchronization constraints.

## Application Needs and Pain Points

The application-side discussion emphasized that the difficult part is not only expressing chemistry in quantum form, but translating classical domain knowledge into quantum software efficiently. Chemists often want a black-box workflow that produces useful results, but algorithm developers still need enough information to understand operation cost, timing, error behavior, and resource limits.

Key pain points:

- Classical algorithms do not map cleanly onto quantum hardware without careful adaptation.
- Naive ansatz and circuit mappings can explode gate counts and parameter counts.
- Current tools such as Qiskit help, but cannot fully automate the translation from chemistry workflow to efficient quantum-HPC execution.
- Application developers need software to expose useful constraints and capabilities without forcing them into unnecessary device-level details.
- Algorithm developers need feedback from lower layers, such as timing data, telemetry, operation costs, and which operations are "hard" on a given target.

The group discussed at least three coupling regimes:

- Loose coupling: classical preprocessing, fragment generation, distributed fragment submission, and final energy/property reconstruction. These do not require strict timing.
- Medium coupling: VQE-style hybrid loops, where classical optimizers react to quantum measurements. Time to solution matters, but this is not hard real-time execution.
- Hard or soft real-time coupling: QEC, mid-circuit measurement, feed-forward, and some future FTQC algorithms. These require much tighter synchronization and may involve device-side or control-side runtime support.

Deliverable implication: any report-out should make clear that "tight integration" is not one thing. The runtime work needs a shared vocabulary for loose, medium, soft real-time, and hard real-time coupling.

## Software Architecture and Runtime Layer

The architecture presentation framed the runtime layer as an application-facing execution contract. Its purpose is to shield application developers from system management complexity while still letting them express timing constraints, dependencies, data movement, and resource needs.

The proposed runtime view is hierarchical, not a flat graph down to every gate, pulse, FPGA action, or physical qubit. At the runtime layer, graph nodes are more likely to be quantum operators, quantum circuits, classical kernels/operators, or larger tasks. Lower layers can then handle QPU mesh mapping, sub-circuit placement, pulse/control details, or hardware-specific scheduling.

The runtime is expected to coordinate with:

- Application and algorithm layers built with multiple SDKs or models.
- Resource managers and job admission systems.
- Classical nodes and quantum access nodes.
- CPU, GPU, FPGA, and QPU resources.
- Compiler pipelines for circuit transformation, splitting, layout, and device-specific optimization.
- Lower-level scheduling policies for QPU meshes or per-node execution.

Primary benefits discussed:

- Avoiding idle time for expensive quantum and HPC resources.
- Enabling portability across SDKs and backends.
- Representing hybrid applications as task graphs with dependencies and constraints.
- Supporting resource-aware submission by device type.
- Dynamically responding to bandwidth, latency, overload, and admission-control conditions.

Open architecture challenges:

- Define a runtime object model for heterogeneous devices.
- Decide how graph operators map to runtime objects and device resources.
- Define what information flows up to the application and down to the compiler.
- Decide where compiler responsibilities end and runtime responsibilities begin.
- Support both precompiled binary workflows and cases where runtime/compiler co-optimization requires recompilation.
- Avoid designing only for one application example while still grounding the work in real workflows.

## Open Heterogeneous Runtime Proposal

The runtime proposal focused on a device-side runtime, not a Slurm-level job scheduler. The runtime starts after resources have been reserved and focuses on how work gets placed, dispatched, synchronized, and executed on CPUs, GPUs, FPGAs, QPUs, and related accelerators.

The motivating observation was that quantum and HPC stacks do not currently share a standard device runtime or task interface, even though many abstractions overlap with successful GPU/HPC/ML runtimes.

Runtime concepts borrowed from CUDA/HIP/HSA-style systems included:

- Kernels: programs or work units targeted to devices.
- Streams: ordered sequences of work, with multiple streams enabling concurrency.
- Events and synchronization points: mechanisms for waiting until required results are ready.
- Agents: devices or device partitions, such as CPUs, GPUs, DSPs, or QPUs.
- User-mode queues: queues where work packets are placed.
- Packets: descriptions of the work, kernel signature, parameters, and target.
- Signals: readiness notifications to begin work.
- Memory allocation and transfers: including scratchpad-like resources and qubit/resource allocation analogies.
- Graph capture: a way to reduce repeated launch/setup overhead when the workflow structure is known.

The transcript clearly covered two proposed runtime knobs in detail:

- Target: device discovery, capability discovery, and compatibility with the architecture. The group debated whether this should expose logical QPU capabilities, physical QPU details, or both.
- Dispatch: how work is launched, including latency-oriented dispatch versus throughput/bandwidth-oriented dispatch. A persistent worker pool was discussed as a low-latency pattern for QEC-style workloads.

Other repeatedly discussed runtime concerns included allocation, memory/data movement, synchronization, capability queries, resource placement, and compiler/runtime coordination. The original slide apparently listed six knobs, but only target and dispatch were fully visible in the transcript.

## Main Design Debates

### Logical Versus Physical Device Abstractions

The group spent significant time on whether application-visible targets should be logical QPUs/logical qubits or physical devices/physical qubits. The dominant view was layered:

- Application users should usually target logical abstractions.
- Algorithm developers need cost and telemetry from lower layers.
- Kernel/runtime developers and QEC implementers need access to physical capabilities.
- Device/calibration researchers may need even lower-level pulse or physical qubit access.

The discussion acknowledged that current QPUs differ from GPUs: quantum devices vary strongly by vendor and technology, calibration changes matter, physical qubit quality changes over time, and QPUs are scarce relative to CPU/GPU nodes. That makes a purely GPU-like abstraction incomplete, but the group still saw value in adopting GPU runtime lessons.

### Application Runtime Versus Device/QEC Runtime

There was an unresolved scope question: should this effort focus on application-level workflow/runtime interfaces, device-side runtime interfaces, or QEC/control-adjacent runtime behavior? The group seemed to converge on the need for layers rather than a single abstraction.

One practical proposal was to focus the OpenQSE effort first on the application/logical/resource graph layer, while acknowledging that lower layers must exist for latency-critical QEC and control-oriented work. Low-level pulse/control systems such as ARTIQ were mentioned as important but probably outside the immediate focus.

### Compiler Versus Runtime Boundary

The group distinguished compiler and runtime responsibilities:

- The compiler can reorganize a graph when operations are independent and correctness is preserved.
- The runtime should optimize launch, setup, placement, dispatch, and resource use when it is time for work to execute.
- Some workflows can carry precompiled binaries with embedded options.
- Some optimizations require compiler/runtime cooperation and may require recompilation.

This boundary remains a deliverable item because it affects API shape, binary packaging, capability discovery, and performance portability.

### Workflow Engine Versus Lower-Level Runtime

Several existing systems or approaches were discussed as potentially complementary rather than mutually exclusive. A workflow engine can handle graph-level orchestration and resource reservation through systems like Slurm, while a lower-level runtime can manage fine-grained dispatch and resource use after allocation.

Deliverable implication: the group should compare existing approaches by layer and responsibility instead of treating them as direct competitors.

## Converged Milestone Path

The strongest consensus was around a staged process:

### Milestone 1: Form a Focused Runtime Working Group

Create a single-threaded owner or small working group under the OpenQSE/QSC software architecture effort. Its initial job is to turn the discussion into concrete criteria, deadlines, and prototype-comparison activities.

Open items:

- Name the owner.
- Define membership from AMD, Continuum/Quantinuum, ORNL/OpenQSE, and other interested participants.
- Decide whether the group is producing a white paper, runnable demo, or both.

### Milestone 2: Inventory Existing Runtime and Workflow Prototypes

Collect what already exists before defining a final standard. Systems mentioned included AMD's runtime prototype, a Continuum/Quantinuum workflow/runtime effort, IBM-related approaches, and other emerging systems.

Expected deliverables:

- Prototype inventory.
- Links to documentation/tutorials.
- Maturity assessment: open source, internal prototype, deployable, documentation-only, or unavailable.
- Layer classification: workflow orchestration, application runtime, device runtime, compiler/runtime bridge, QEC/control support.

### Milestone 3: Define an Experimental Matrix

Rather than standardize from one application, define experiments across multiple quadrants or dimensions.

Candidate dimensions:

- Low latency versus high latency tolerance.
- Low HPC intensity versus high HPC intensity.
- Loose coupling versus medium hybrid coupling versus hard/soft real-time coupling.
- Throughput/bandwidth dispatch versus latency-oriented dispatch.
- Logical resource graph versus physical device-aware execution.

Candidate workflows:

- Fragment-based chemistry workflow, especially FMO/VQE.
- CPU/GPU/QPU hybrid workflow with clear data dependencies.
- QEC or mid-circuit measurement/feed-forward workflow.
- Small latency-sensitive workflow to test persistent dispatch or resident workers.

### Milestone 4: Deploy and Run Reference Prototypes

The group favored running prototypes before locking down the interface. The goal is to make systems executable somewhere, expose them to users, and learn from actual behavior.

Expected deliverables:

- Runnable reference prototypes.
- Shared test workflows.
- Runtime behavior reports.
- Gap analysis against the experimental matrix.
- A possible hackathon or bake-off style comparison.

### Milestone 5: Derive Runtime Criteria and Interface Boundaries

After prototype comparison, extract common layers, interfaces, and requirements.

Expected deliverables:

- Runtime criteria for contributors.
- Conceptual runtime object model.
- Vendor-neutral API sketch or interface document.
- Compiler/runtime boundary description.
- Logical/physical abstraction policy.
- Capability discovery and telemetry requirements.
- Dispatch model requirements, including latency and throughput modes.

### Milestone 6: Produce Report-Out Artifact

The group discussed two possible outward-facing deliverables:

- A white paper describing systems, layers, criteria, and experiments.
- A technical deployment or demo if prototypes are mature enough.

A target around SC and arXiv was mentioned, but no firm date was finalized in the transcript.

## Proposed Action Items

- Assign a working group owner for runtime criteria and prototype comparison.
- Ask each interested team to contribute a short system description, status, documentation link, and layer classification.
- Have AMD share the current status and open-source path for its heterogeneous runtime prototype.
- Have Continuum/Quantinuum share the workflow/runtime tutorial and clarify how it overlaps or layers with the AMD proposal.
- Capture IBM and other relevant approaches as comparison points, especially where they chose lower-level interfaces over workflow orchestration.
- Select two to four target workflows for the experimental matrix.
- Define the first reference prototype deadline and what "runnable" means.
- Draft a runtime criteria document before attempting a final API spec.
- Decide whether the near-term report-out is a white paper, demo, or both.
- Establish a shared vocabulary for loose, medium, soft real-time, and hard real-time coupling.
- Document which low-level control/pulse concerns are out of immediate scope, while preserving hooks for latency constraints.

## Open Questions and Risks

- Scope: the discussion spans workflow orchestration, application runtime, device runtime, compiler interfaces, QEC, and control electronics. The working group needs crisp boundaries.
- Vendor abstraction: physical QPU capability descriptions are hard to standardize and may change frequently.
- User layering: the runtime must support simple application users, expert algorithm developers, kernel/runtime developers, and device researchers without forcing one interface on all of them.
- Prototype maturity: some systems are further along than others; some may not be open source yet.
- Resource support: several participants noted that leadership buy-in and outside pressure would help allocate time to this work.
- Terminology: multiple terms were used inconsistently, especially around "tight coupling," runtime layer boundaries, and interface names.
- Timing: SC/arXiv was discussed as a possible target, but no firm milestone calendar was finalized.

## Bottom Line

The main deliverable is not yet a final runtime standard. The main deliverable should be a staged path to one: a small working group, an inventory of existing prototypes, a shared experimental matrix, runnable reference implementations, and a criteria/API document derived from real comparisons. The FMO/VQE chemistry workflow is a strong first reference application because it exercises classical HPC preprocessing, parallel fragment execution, hybrid quantum-classical loops, and future tighter-coupling extensions.

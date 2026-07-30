# OpenQSE System Architecture Session 01 Detailed Summary

Source transcript: `openQSE-sys-arch-session-01-transcript.docx`

## 1. Opening And Purpose Of The Workflows/System Architecture Session

### 1.1 Working group identity

- Peter Grozkowski opened the session and introduced the Workflows and System Architecture working group within OpenQSE.
- The working group is led by Johannes Blaschke, who was not present.
- The purpose of the session was to introduce the working group, explain its goals, and then use application examples to drive discussion.

### 1.2 Core working group questions

- How do different hybrid quantum-classical application workflows map onto available resources?
- Which parts of a workflow require quantum hardware, classical HPC, local classical compute, GPUs, simulators, or control hardware?
- What constraints matter for those resources?
- The group specifically called out latency, throughput, synchronization, bandwidth, and resource constraints.
- A major goal is to translate workflow analysis into system requirements and feed those requirements to the other OpenQSE workstreams.

### 1.3 Need for community convergence

- The speakers noted that many institutions and companies are thinking about HPC-quantum integration.
- Different groups have different hardware, operating models, and assumptions.
- OpenQSE should look for commonalities without forcing one architecture on everyone.
- Desired convergence areas include terminology, concepts, primitives, reusable workflow patterns, and shared ways of reporting requirements.

## 2. AWS Framing: From Applications To Integration Requirements

### 2.1 Deployment and geolocation questions

- Sebastian from AWS framed the architecture problem around deployment and integration.
- The group needs to ask whether geolocation requirements exist and why.
- Possible reasons include round-trip latency constraints, data movement, control needs, policy, or other overlooked constraints.
- If systems need to be co-located, the next question is how tightly they need to be integrated.

### 2.2 Interconnect and stack abstraction

- The group discussed whether wide-area networks, local-area networks, or high-performance interconnects are sufficient for different workflows.
- The speakers emphasized that deployment is only one part of the question.
- The group also needs to understand how the layers of the quantum-classical stack should be abstracted.
- These abstraction decisions will influence system integration requirements.

### 2.3 AWS performance model

- AWS described a collaboration with NVIDIA, NERSC, and NASA Ames on a performance model for hybrid quantum-classical workflows.
- The model decomposes hybrid compute cycles into classical compute costs, quantum compute costs, and communication costs.
- At the application level, the model uses a computation-to-communication ratio.
- This ratio helps distinguish compute-heavy workflows from communication-sensitive workflows.
- At the real-time level, the model provides feasibility constraints rather than only time-to-solution estimates.

### 2.4 Initial performance-model insight

- For compute-sensitive or compute-intensive applications, the reported performance benefit of co-location may be small.
- As compute cost decreases or communication sensitivity increases, latency and communication become more important.
- Tight integration remains crucial for real-time tasks such as quantum error correction.
- AWS invited the working group to adopt or build on this model for OpenQSE studies.

## 3. Session Agenda And Provocative Questions

### 3.1 Planned session structure

- The morning agenda included:
  - A short introduction.
  - ORNL's perspective on quantum-HPC system architecture.
  - Three application/workflow talks.
- The afternoon agenda was expected to include:
  - A quantum error correction lightning talk.
  - A discussion led by Laura Schulz on non-performance requirements.
  - Discussion of workstream overlap and milestones.

### 3.2 Discussion-heavy format

- The moderators emphasized that the session was intended to be discussion-heavy.
- Speakers were asked to leave time for 15 to 20 minutes of discussion after most talks.
- Automated notes were expected to preserve the technical discussion.

### 3.3 Provocative statements used to seed discussion

- The group presented deliberately provocative statements:
  - Current user-land hybrid workflows may not actually need HPC.
  - There may be little evidence that end-user applications require tight integration.
  - Tight integration for quantum-classical systems may not require co-location.
- These statements were intended to test assumptions and force more precise definitions.

## 4. ORNL/QSC Architecture Perspective

### 4.1 Quantum and classical computing as complementary

- Chris presented ORNL and Quantum Science Center material prepared by Scott Ashley.
- The ORNL/QSC view is that quantum computing does not replace classical computing.
- Quantum and classical systems are complementary.
- Quantum computers will need some tightly coupled classical compute for syndrome decoding, error correction, pulse control, and related fault-tolerant tasks.

### 4.2 Unknowns in QPU-local classical compute

- The classical technology needed near quantum hardware remains an open question.
- Candidate technologies include FPGAs, GPUs, CPUs, and other specialized accelerators.
- The right mix depends on control, decoding, pulse generation, application needs, and future algorithm development.

### 4.3 ORNL's proposed tiered architecture

- ORNL is considering a model where leadership-scale HPC systems connect to a quantum facility.
- Frontier exists today, LUX was described as being installed, and Discovery was mentioned for 2028.
- The connection between large HPC and quantum facilities may use ESNet or other connectivity mechanisms.
- A smaller HPC or classical computing resource may be located within or near the quantum facility.
- That smaller resource may support quantum control, syndrome decoding, mid-circuit calculations, iterative correction, or other hybrid application needs.

### 4.4 Existing ORNL quantum testbeds

- ORNL already has on-premises quantum testbeds, including Quantum Brilliance and IQM.
- ORNL wants to use these systems and local clusters to explore real quantum-HPC workflows.
- This work fits with the IRI and American Science Cloud/Genesis connected-facility vision.

### 4.5 Latency regimes

- ORNL described multiple possible latency regimes:
  - Frontier or leadership HPC over ESNet: tens to hundreds of milliseconds.
  - Co-located HPC near quantum hardware for non-QEC workflows: tens of microseconds.
  - QPU-local compute for very tight control or QEC: sub-microsecond interaction.
- These regimes are technically possible with different network and hardware technologies, but the use cases need to justify them.

### 4.6 Information ORNL wants from application teams

- Logical qubit counts.
- Circuit depths.
- Shot counts.
- Logical encodings.
- Whether one or multiple logical encodings are needed.
- Classical FLOP and memory requirements.
- What runs on leadership HPC versus co-located resources.
- How much interaction is needed between quantum and classical components.
- Latency and bandwidth requirements at each stage.

### 4.7 Workflow patterns presented

- Single-shot open-loop workflow:
  - Classical modeling/simulation prepares a quantum task.
  - Circuits are compiled and sent to the QPU.
  - Results return later for classical processing.
- Batch or pipeline workflow:
  - Multiple related quantum jobs are sent, possibly for materials or molecules.
  - Results are returned and processed as a batch.
- Variational or iterative loop:
  - Classical and quantum steps alternate repeatedly.
  - Common in NISQ-era algorithms.
- Potential mid-circuit or real-time workflows:
  - Classical computation may influence later quantum operations.
  - These may require more local or lower-latency resources.

## 5. Discussion After ORNL Presentation

### 5.1 NISQ versus fault-tolerant workflow assumptions

- A participant asked whether the ORNL workflow patterns came from analysis of target applications.
- Chris explained that ORNL had worked with scientific computing groups and application teams, but much of the current information is NISQ-dominant.
- The group has not fully explored how workflows change with fault-tolerant quantum computing.

### 5.2 Logical encodings and sampling difficulty

- Participants asked why near-term application patterns become more complicated with logical encodings.
- The response focused on sampling rates and logical operation times.
- Logical encodings may make individual shots much slower.
- Some systems may require minutes or hours per shot depending on operation count and encoding overhead.
- High-rate sampling may become infeasible for some methods.

### 5.3 VQE and fault-tolerant limitations

- VQE may become less attractive or require major changes at larger scale.
- Barren plateaus were mentioned as one issue.
- Fine-grained arbitrary rotations used in many VQE implementations are harder in fault-tolerant systems.
- Logical rotations and large rotation synthesis remain challenging.

### 5.4 Operational impact of waiting

- Some participants questioned whether longer quantum wait times are simply an implementation inconvenience.
- ORNL emphasized that waiting is operationally significant.
- Leadership systems run many simultaneous applications.
- A large HPC allocation cannot sit idle for long while waiting for a QPU result.
- QPUs are also scarce and expensive and should not wait on classical resources.
- The group distinguished prioritization from load balancing.

### 5.5 Meaning of tight coupling

- The room discussed whether tight coupling is qualitative or quantitative.
- Latency is one factor, but not the only one.
- Bandwidth and data volume may be just as important.
- Frontier can generate enormous internal data volumes, but external network links are much smaller.
- A high-volume workflow may need local classical compute near the QPU even if latency is not the main issue.
- Reliability and assumed availability also matter.
- An MPI-style application assumes reliable communication among resources; looser quantum-classical systems may require different failure handling.

### 5.6 Partial fault-tolerant quantum computing

- Partial FTQC was introduced as an intermediate regime between NISQ and full FTQC.
- In partial FTQC, some operations are protected fault tolerantly and some expensive rotation operations may use post-selection approaches.
- This can reduce physical qubit requirements for material science and quantum chemistry.
- The tradeoff is runtime and post-selection overhead.
- Partial FTQC suggests future workflows may coordinate NISQ, partial FTQC, and full FTQC components.

### 5.7 Multi-modality of future application pipelines

- Alice and Bob's architecture was mentioned as efficient for fault tolerance but not necessarily for variational algorithms on its physical gate set.
- This raised the possibility of multi-modal application pipelines.
- The issue is not just coupling HPC to one QPU.
- The pipeline may need coordination between NISQ-style components, fault-tolerant components, and classical HPC resources.

### 5.8 Embedding and locality

- Participants discussed quantum chemistry and materials embedding.
- Strongly correlated or localized regions may be handled by quantum methods.
- Larger weakly correlated environments may be handled classically.
- This mirrors QMMM-style chemistry approaches.
- Locality and length scales of entanglement were identified as important algorithmic and workflow drivers.

## 6. Smriti Bajaj: Credit Risk Analysis And SQD/QSCI

### 6.1 Credit risk analysis motivation

- Smriti Bajaj from Dell presented credit risk analysis as a workflow example.
- The goal was to connect portfolio risk calculations to concrete system requirements.
- Credit risk management estimates potential losses from borrower defaults.
- Outputs include expected loss, value at risk, and conditional value at risk.
- Classical Monte Carlo is used today but may require long compute cycles for low error.
- Quantum amplitude estimation was presented as a potential quadratic speedup.

### 6.2 Credit risk workflow

- Data is ingested.
- Portfolios are constructed.
- Default probabilities are computed for assets.
- Probability distributions are encoded into quantum circuits through an uncertainty model.
- Quantum tasks are dispatched to a QPU or emulator.
- Classical tasks consume quantum outputs to compute risk measures.
- Dell's Quantum Intelligent Orchestrator identifies task dependencies and dispatches work to CPU, GPU, or QPU resources.

### 6.3 Credit risk coupling pattern

- The workflow was described as loosely coupled.
- Communication happens in batches.
- There is no tight inner feedback loop.
- However, orchestration must respect task dependencies.

### 6.4 Demonstrated Dell infrastructure

- The demonstration used Slurm.
- Management VMs ran Slurm control and orchestration services.
- Worker VMs handled classical pre- and post-processing.
- A GPU partition used NVIDIA L40s.
- The team scaled examples from small asset counts to a 10-asset case corresponding to about 31 qubits in emulation.
- Parallel execution improved CPU/GPU utilization by identifying independent tasks.

### 6.5 SQD/QSCI motivation

- The talk then moved to sampled quantum diagonalization, also called QSCI in some chemistry contexts.
- SQD/QSCI aims to estimate molecular ground-state energies.
- The QPU acts as a sampler.
- Classical systems do much of the heavy solving through filtering, subspace construction, Hamiltonian projection, and diagonalization.
- The method is robust to noisy QPU samples.

### 6.6 SQD/QSCI workflow

- Classical side prepares Hamiltonians and circuits.
- QPU samples bitstrings.
- Classical side filters and recovers valid configurations.
- A subspace is constructed.
- The Hamiltonian is projected.
- Diagonalization is performed, which is a major HPC-heavy cost.
- If convergence is not achieved, the process repeats.

### 6.7 SQD/QSCI coupling pattern

- SQD/QSCI was described as loosely coupled but iterative.
- It differs from the credit-risk batch workflow because results may trigger another round.
- Quantum sampling and classical diagonalization are key bottlenecks.
- GPU acceleration, OpenMP offload, fragmentation, embedding, and trimmed SQD were discussed as improvement paths.

### 6.8 Discussion of workflow state and architecture model

- Participants asked whether state is preserved in the quantum stack between iterations.
- The response was that the state must be prepared again, using updated classical information.
- This prompted a comparison between microservice-style and monolithic workflows.
- Smriti described the current orchestrator approach as closer to monolithic for these examples.

### 6.9 Discussion of quantum advantage

- A participant asked whether a quantum advantage had been quantified.
- The answer was cautious.
- For credit risk, the focus was on implementing and orchestrating a quantum amplitude estimation workflow.
- For SQD, the focus was on identifying speedup opportunities and communication patterns.
- Another participant argued that shallow UCJ/LUCJ circuits used in some recent chemistry experiments may be classically simulable.
- The point was that deeper circuits or fault-tolerant systems may be needed for true quantum advantage.

### 6.10 Scheduling and telemetry discussion

- Smriti identified dynamic resource changes after job submission as a scheduling challenge.
- Slurm does not generally support changing allocations on the fly.
- Failures, network issues, or QPU availability may require re-estimating resources.
- Participants asked what information should come from the QPU to support reallocation.
- Candidate telemetry included T1/T2 times, gate counts, qubit counts, circuit depth, and expected compute time.
- Resource estimation could be hard-coded, classical, or machine-learning-based.
- The group noted that vendor-agnostic protocols are needed for passing these metrics between orchestrators, schedulers, and QPU controllers.

## 7. Namit: Adaptive Simulation, State Preparation, And Dynamic Circuits

### 7.1 Application target

- Namit shifted from circuit knitting to adaptive quantum algorithms for strongly interacting electronic systems.
- The Fermi-Hubbard model was used as the benchmark problem.
- The model is important for correlated materials and strongly interacting electrons.
- It is a natural target for quantum simulation.

### 7.2 Resource challenge

- Even optimized approaches may require around a million T gates and hundreds of thousands to millions of qubits.
- Similar resource challenges appear in NMR, catalyst design, and quantum chemistry.
- The speaker argued that the community must extract every possible performance improvement.

### 7.3 Baseline workflow

- Classical HPC optimizes an initial tensor-network or MPS-like state.
- The state is sent to the QPU.
- The QPU performs state preparation, time dynamics, phase estimation, and measurement.
- Classical post-processing produces one data point.
- The process repeats many times for a phase diagram.

### 7.4 State preparation as a hidden cost

- Initial state preparation is often ignored or hidden in resource estimates.
- For strongly correlated chemistry, state preparation can require extremely large Toffoli or T-gate counts.
- The speaker argued that "state preparation" may be too mild a term because it can be a major workflow stage.

### 7.5 Dynamic circuits and feedforward

- Dynamic circuits allow measurement and feedforward during circuit execution.
- These can reduce state-preparation depth.
- GHZ state preparation was cited as an example where measurement-based methods reduce depth from linear to constant.
- Similar ideas can accelerate MPS or tensor-network state preparation.

### 7.6 Adaptive phase-diagram discovery

- Rather than sampling a phase diagram uniformly, the algorithm should sample densely near phase boundaries and sparsely in stable regions.
- QPU measurement results inform where the next sample should be taken.
- Classical compute prepares the next initial state and optimization parameters.
- Feedback may occur on seconds-to-minutes timescales for some fault-tolerant workflows.
- Co-scheduling still matters because queue delays could dominate iteration time.

### 7.7 Tighter mid-circuit computation use case

- The speaker then discussed mid-circuit measurement and real-time classical computation.
- QFT-plus-measurement and conditional rotation synthesis were presented as examples.
- Existing control systems may not be designed to perform nontrivial classical computation during circuit execution.
- GridSynth-style synthesis was discussed as a microsecond-scale operation.
- This suggests a need for a small classical compute resource tightly connected to the QPU, plus larger HPC for slower pre/post-processing.

### 7.8 Discussion themes

- Participants debated whether state preparation should be treated as input generation or as a major subroutine.
- Tight coupling was again challenged because one example tolerated seconds-to-minutes feedback while another required microseconds.
- The group discussed hiding QEC and decoding complexity from application scientists where possible.
- However, the architecture and compiler stack still need to account for that complexity.
- Participants discussed parameterized workflows where resource allocation scales with problem size.

## 8. Ty/Tyler From AWS: Generative Quantum Eigensolver

### 8.1 GQE motivation

- Ty presented the generative quantum eigensolver as a workflow example.
- The setting was quantum chemistry and electronic structure.
- Classical electronic structure theory uses configurations and excitation operators.
- VQE with unitary coupled cluster singles and doubles uses parameterized excitation operators.

### 8.2 GQE algorithm concept

- GQE uses a classical generative model to propose candidate circuits or ansatze.
- The model selects from a pool of single and double excitation operators.
- Parameters are discretized, expanding the operator pool.
- The model is trained to choose operator combinations that produce low energies.
- The approach brings machine-learning tooling into quantum circuit generation.

### 8.3 GQE workflow phases

- Classical resource A computes Hamiltonian elements.
- An initial training dataset is generated.
- Many circuits are evaluated, either on QPUs or simulators.
- Circuit evaluation can be embarrassingly parallel.
- If circuits are small, CPU nodes may be enough.
- If circuits are larger, GPU simulators or HPC resources may be needed.
- The generative model is initialized and trained, often requiring GPU-accelerated resources.
- During training, the model proposes ansatze, circuits are evaluated, and the model is updated until convergence.

### 8.4 Systems challenge

- GQE changes resource needs across phases.
- Hamiltonian computation, circuit simulation or QPU execution, model training, and inference may require different resources.
- At scale, many classical and quantum resources could be reserved but idle while another phase runs.
- This is a worst-case pattern for facilities.

### 8.5 Discussion of barriers and batching

- Participants asked whether all quantum evaluations must finish before training starts.
- The base implementation appears batch-oriented.
- Smaller batches might allow model updates to begin earlier.
- A more streaming implementation could reduce idle time.
- Schedulers need to express these constraints.

### 8.6 Resource scaling factors

- Classical compute depends on molecule/system size.
- Basis set choice affects the number of virtual orbitals.
- Active-space selection can reduce cost but may lose correlation accuracy.
- Operator pool size depends on system size, basis set, and parameter discretization.
- Training may involve hundreds to thousands of iterations and batches around 1,000 circuits.
- GPU availability is a practical bottleneck because ML workloads already create high demand.

### 8.7 Scheduling and facility operations

- Participants discussed whether QPU allocations should be held open or repeatedly acquired and released.
- Holding resources avoids queue delay but wastes scarce capacity.
- Releasing resources avoids idle time but may add restart and queue overhead.
- Time-based or prediction-based scheduling was suggested, though classical HPC has long struggled with accurate runtime prediction.
- Checkpointing can soften prediction errors in classical HPC, but hybrid workflows introduce new costs.

### 8.8 Final Session 01 themes

- Tight coupling may mean milliseconds or less for some participants, but definitions varied.
- Co-location may be influenced by data governance as well as performance.
- Algorithm-specific optimizations such as qubit-operator pools or perturbative corrections were discussed, but the main issue remained resource management.
- The session ended with agreement that workflow-aware scheduling and resource estimation are central problems.

## 9. Overall Themes From Session 1

- The group repeatedly returned to terminology.
- Application-level looseness does not remove the need for orchestration.
- Facility operations are as important as algorithm diagrams.
- QPU telemetry, scheduling, and resource estimation are foundational.
- Fault tolerance, partial fault tolerance, and NISQ may coexist in future workflows.
- A portfolio of representative workflows is needed to turn discussion into architecture requirements.

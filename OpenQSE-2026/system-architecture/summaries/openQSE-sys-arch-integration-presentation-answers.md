# OpenQSE System Architecture Integration Discussion: Presentation Answers

## 1. Main Points Discussed

### Workflow-Driven System Architecture

The group focused on using real hybrid quantum-classical workflows to derive system architecture requirements. The repeated message was that OpenQSE should not start with a fixed architecture and force applications into it. Instead, it should examine application workflows, identify where quantum and classical resources are used, and derive requirements for latency, bandwidth, scheduling, telemetry, co-location, and abstraction layers.

### Meaning Of Tight Integration

The group repeatedly returned to the problem that "tight integration" and "loose integration" were not consistently defined. Some participants used tight integration to mean low-latency communication. Others used it to include common scheduling, co-allocation, data locality, shared administrative control, identity, policy alignment, software compatibility, and workflow reliability.

The group agreed that integration should not be reduced to latency alone.

### Application-Level HPC/QC Integration Beyond QEC

The group recognized that QEC clearly requires real-time classical compute near the QPU, but they also discussed non-QEC application-level cases that may require tighter integration or co-allocation.

Key examples included:

- Adaptive quantum simulation, where QPU results guide the next classical preparation step.
- Mid-circuit adaptive logic, such as QFT-plus-measurement and real-time rotation synthesis.
- Generative Quantum Eigensolver workflows, where QPU/simulator execution, GPU-heavy model training, and classical Hamiltonian calculations alternate.
- SQD/QSCI workflows, where the QPU acts as a sampler and HPC performs expensive post-processing such as diagonalization.

### Tiered HPC/QC Architecture

ORNL described a tiered model:

- Large leadership HPC systems, such as Frontier, connected to quantum facilities.
- Smaller classical or HPC resources co-located near QPUs for control, decoding, and selected hybrid workloads.
- Very low-latency QPU-local hardware for QEC/control functions.

The group discussed that different workflows may need different parts of this hierarchy.

### Scheduling, Co-Allocation, And Resource Utilization

The group emphasized that hybrid workflows may require multiple scarce resources at different times. A major problem is that classical HPC, GPUs, and QPUs may sit idle while another stage of the workflow runs. This is especially important because leadership HPC allocations and QPU time are both expensive and scarce.

The group discussed the need for:

- Co-scheduling or co-allocation of HPC and QC resources.
- Time-based or prediction-based scheduling.
- Better resource estimation.
- Dynamic or adaptive resource allocation.
- Avoiding long queues between workflow stages.

### QPU Telemetry And Resource Estimation

Several discussions focused on the need for QPU telemetry to support scheduling and orchestration. Useful information may include calibration data, T1/T2 times, gate counts, circuit depth, qubit availability, expected execution time, and remaining allocation or budget.

### User Personas And Ecosystem Readiness

Laura Schulz emphasized that different users require different levels of abstraction. Early power users may work close to the hardware, while later domain scientists will need higher-level tools, services, and support.

The group also discussed that "the user" may actually be a team, including domain scientists, algorithm developers, compiler experts, and classical computing experts.

### Non-Performance Requirements

The group discussed governance, identity, trust, data management, reproducibility, traceability, policy alignment, access control, and sustainability. These were treated as architecture-relevant requirements, not secondary concerns.

## 2. Conclusions Reached Regarding The Main Points

### Conclusion: Integration Must Be Workflow-Specific

The group did not conclude that all application workflows need tight HPC/QC integration. Instead, they concluded that integration requirements depend on the workflow pattern.

Batch workflows may tolerate loose coupling. Adaptive workflows may require co-scheduling. Mid-circuit adaptive workflows may require low-latency classical compute near the QPU.

### Conclusion: Tight Integration Has Multiple Domains

The group effectively identified multiple timing/integration domains:

| Domain | Approximate Timing | Example | Integration Need |
|---|---:|---|---|
| Real-time QEC/control | nanoseconds to microseconds | Syndrome decoding, active feedback | QPU-local FPGA/ASIC/control hardware |
| Mid-circuit application logic | microseconds to milliseconds | QFT-plus-measurement, real-time rotation synthesis | Small tightly connected classical/HPC node |
| Adaptive application loop | seconds to minutes | Adaptive Fermi-Hubbard or phase-diagram discovery | Co-scheduled HPC/QC resources |
| Iterative hybrid workflow | minutes to hours, workflow-dependent | GQE, VQE-like loops, SQD/QSCI | Coordinated scheduling and resource lifecycle management |
| Batch/offload workflow | loose timing | Credit risk, batched sampling | Orchestration and dependency tracking |

### Conclusion: Non-QEC Tighter Integration Is Plausible And Needs Study

The group agreed that QEC is the clearest real-time case, but they also identified non-QEC application-level cases where tighter integration could matter.

The strongest non-QEC case was mid-circuit adaptive computation, where measurement outcomes require nontrivial classical computation before the circuit can continue. QFT-plus-measurement and rotation synthesis were discussed as examples where current control systems may not be enough, and a nearby compute node may be needed.

Another important case was adaptive quantum simulation. This may not need microsecond feedback, but it may still require HPC and QC to be co-allocated or co-scheduled so that neither side waits in a long queue between iterations.

### Conclusion: Co-Allocation May Matter Even Without Co-Location

The group distinguished co-location from co-allocation.

Some workflows may not require the HPC and QPU to be physically next to each other, but they may still require the resources to be available at the same time. For example, adaptive simulation may tolerate seconds-to-minutes communication but still fail operationally if each iteration waits hours in a queue.

### Conclusion: A Tiered Architecture Is Likely Needed

The discussions supported a tiered architecture:

- QPU-local real-time hardware for QEC/control.
- Nearby small or medium classical/HPC resources for low-latency application logic, configurable decoding research, and fast feedback.
- Larger HPC resources for heavy pre-processing, simulation, optimization, diagonalization, training, and post-processing.

### Conclusion: Better Definitions Are Required

The group concluded that shared terminology is a prerequisite for progress. They need clearer definitions of:

- Application.
- Workflow.
- Architecture.
- Orchestration.
- Tight integration.
- Loose integration.
- Coupling.
- Co-location.
- Co-allocation.
- Latency domain.

### Conclusion: Quantum Chemistry Is A Good Candidate For End-To-End Study

The group suggested using quantum chemistry as an initial end-to-end workflow candidate, especially because the DOE quantum supercomputing blueprint identifies chemistry embedding and fragmented workflows as likely early success areas for early fault-tolerant systems.

## 3. Performance Metrics Discussed For Integration

### Latency

Latency was the most frequently discussed metric, but the group emphasized that it is not sufficient by itself.

Latency domains mentioned included:

- Sub-microsecond or nanosecond-scale latency for QEC/control paths.
- Tens of microseconds for tightly connected classical resources near the QPU.
- Millisecond-level latency for some tightly coupled hybrid workflows.
- Seconds-to-minutes feedback loops for adaptive application workflows.
- Tens to hundreds of milliseconds for wide-area HPC-to-quantum facility links.

### Bandwidth

Bandwidth was discussed as a separate integration metric from latency. The group noted that data volume may be the harder problem in some workflows.

One example was that leadership HPC systems can generate enormous internal data volumes, while external facility links are much more limited.

### Data Volume

The amount of data exchanged between HPC and QC was identified as a major architectural driver. A workflow with high data volume may need local compute near the QPU even if its latency requirements are not extreme.

### Computation-To-Communication Ratio

AWS introduced a performance model that uses a computation-to-communication ratio to classify workflows.

This helps distinguish:

- Compute-heavy workflows, where co-location may provide little performance benefit.
- Communication-sensitive workflows, where tighter integration or co-location may matter.

### Time To Solution

Time to solution was discussed for end-to-end hybrid workflows. The group noted that architecture choices should be evaluated based on total workflow runtime, not just QPU execution time.

### QPU Execution Time And Shot Time

Participants discussed that logical encodings can make shots much slower. Some future fault-tolerant workflows may involve minutes or even hours per shot depending on operation count and encoding overhead.

### Classical Compute Time

The group discussed classical compute time for:

- Hamiltonian construction.
- Initial state optimization.
- Tensor-network preparation.
- Circuit simulation.
- ML model training.
- SQD/QSCI diagonalization.
- Post-processing.
- Runtime circuit synthesis.

### Queue Time And Scheduling Delay

Queue time was treated as a major performance factor. Even if a workflow only needs seconds-to-minutes communication, it can break down if the next HPC or QPU step waits hours or days in a queue.

### Resource Idle Time

Idle time was a recurring metric. The group repeatedly noted that QPUs, GPUs, and leadership HPC allocations should not sit idle while another workflow stage runs.

### Resource Utilization

The group discussed utilization of CPU, GPU, QPU, and simulator resources. Dell's orchestration example showed improved utilization through dependency-aware parallel execution.

### Runtime Predictability

Predicted runtime was discussed as important for hybrid scheduling. Participants noted that HPC has struggled with accurate runtime prediction for years, but hybrid workflows make the problem more important because of scarce QPU resources.

### Scaling Metrics

Several scaling metrics were discussed:

- Number of logical qubits.
- Number of physical qubits.
- Circuit depth.
- Number of shots.
- Number of T gates or Toffoli gates.
- Logical operation count.
- Operator pool size.
- Basis set size.
- Active-space size.
- Number of workflow iterations.

### Telemetry-Based Metrics

Telemetry was discussed as a performance input for scheduling and resource estimation:

- T1/T2 times.
- Calibration state.
- Gate counts.
- Gate fidelities.
- Circuit depth.
- Qubit availability.
- Estimated QPU runtime.
- Remaining allocation or budget.

## 4. Non-Performance Metrics Discussed

### Governance

Governance was discussed as a major non-performance requirement. Hybrid workflows may cross institutions, facilities, vendors, and policy domains. The group asked who owns decisions when a QPU is at one institution and the HPC system is at another.

### Policy Alignment

Policy alignment was treated as part of integration. Different facilities may have different allocation policies, access rules, maintenance windows, software update practices, and security requirements.

### Identity And Access Management

The group discussed single sign-on, trust, authentication, authorization, token-based access, and cross-lab reciprocity. The technical mechanisms may resemble cloud identity systems, but the difficult part is policy trust between institutions.

### Data Governance

Data governance was discussed as both a policy and architecture concern. The group did not settle whether it should be implemented mainly as policy, an architectural layer, or both.

### Trust Between Facilities

Federated workflows require one facility to trust another facility's users, credentials, jobs, and data handling. This was described as a key issue for Genesis/American Science Cloud style federation.

### Reproducibility

Reproducibility was identified as difficult for hard quantum workflows. If a result requires a unique or scarce quantum system, it may not be independently reproducible in the normal sense.

### Traceability

Traceability was identified as a more realistic near-term requirement than full reproducibility. Workflows should record:

- Hardware used.
- Calibration state.
- Software stack.
- Compiler path.
- Runtime configuration.
- Input data.
- Workflow steps.
- Scheduling and allocation records.
- Output data and metadata.

### Observability

The group discussed observability as necessary for operating hybrid workflows. Users and facilities need visibility into workflow state, resource state, failures, and performance.

### User Personas

User personas were a major non-performance topic. The group emphasized that requirements depend on whether the user is:

- A hardware-level quantum expert.
- A power user.
- A domain scientist.
- A workflow user using higher-level tools.
- A multi-person team combining quantum, HPC, compiler, and domain expertise.

### Service And Support Requirements

Laura Schulz emphasized that support models matter. Users need documentation, support, training, onboarding, and workflow services appropriate to their expertise level.

### Administrative Integration

Administrative integration was discussed as part of tight integration. Even if systems are technically connected, workflows may fail if facilities have incompatible policies, update schedules, security rules, or operational practices.

### Software Compatibility And Update Coordination

The group noted that workflows can break if software stacks, APIs, or packages are updated on one side of a federated system but not another.

### Allocation Fairness And Scarce Resource Management

Because QPUs are scarce, allocation policy is a non-performance metric. The group discussed prioritization, fairness, avoiding wasted QPU time, and matching resource use to scientific value.

### Sustainability And Energy Telemetry

Sustainability reporting was discussed as a possible non-performance requirement. Hybrid workflows may eventually need to report energy or power usage across HPC and QPU components.

### Vendor Neutrality

The software architecture and control discussions emphasized vendor-neutral contracts and avoiding proprietary lock-in.

### Ecosystem Readiness

A major non-performance theme was readiness of the whole ecosystem:

- Software.
- Compilers.
- Benchmarks.
- Standards.
- User support.
- Access systems.
- Governance.
- Integration with HPC and AI infrastructure.

## Summary Slide

The discussion did not conclude that every application requires real-time HPC/QC integration. It did conclude that OpenQSE needs to recognize multiple integration domains. QEC is the hard real-time case, but application-level workflows can still require tighter integration through co-scheduling, co-allocation, low-latency mid-circuit computation, data locality, and workflow-aware resource management.

The key message for presentation:

**Integration is not one number. It is a workflow-dependent combination of latency, bandwidth, co-allocation, data movement, scheduling, telemetry, policy, and user abstraction.**

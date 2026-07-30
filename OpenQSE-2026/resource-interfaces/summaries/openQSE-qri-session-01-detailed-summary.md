# OpenQSE QRI Session 01 Detailed Summary

Source transcript: `openQSE-qri-session-01-transcript.docx`

## Session Purpose

The session opened with introductions from participants across IBM Research, ORNL, Siemens/Altair, HPE, MQC/Munich Quantum Software, Pasqal, NVIDIA/SchedMD, and other organizations involved in quantum/HPC integration. The organizer framed the day around two broad goals: understand the current state of quantum resource interface work and gather stakeholder requirements from the people who will need to build, operate, schedule, monitor, and use these systems.

The group acknowledged that a standard interface for quantum systems in data centers would not be finalized in a single day. Instead, the proposed method was iterative: listen to stakeholders, collect requirements, put forward candidate interfaces, run experiments, and use the resulting evidence to refine toward a specification. Desired outputs included milestones, software artifacts, experiments, testbeds, and papers that could move the community from concepts to measurable behavior.

## Stakeholders and Scope

The discussion placed the quantum resource interface in the middle of a larger ecosystem. Stakeholders included quantum system vendors, quantum applications, SDKs and compilers, workload managers, workflow managers, operations teams, monitoring and analytics teams, and site administrators. Participants noted that the diagram was intentionally interface-centric and that the real graph has many more edges between stakeholders.

The group also noted that compilers may deserve explicit treatment even if they are often grouped under SDKs. Compiler and application needs matter because compilation may depend on device-specific connectivity, calibration, topology, supported gates, error rates, and other properties that can change over time.

## Nomenclature Discussion

A major early topic was shared language. Terms such as quantum computer, quantum device, quantum resource, QPU, and quantum computing resource were being used differently across OpenQSE, QDMI, and QRMI discussions. The group converged on the practical view that, from a usage and scheduling perspective, a quantum resource is not just the QPU hardware. It also includes the classical computer, controller, API endpoint, and preprocessing or service infrastructure required to make the QPU usable.

This raised an abstraction concern. Some participants supported treating the QPU plus required classical services as a single resource because users and workload managers cannot meaningfully use the QPU alone. Others cautioned that too much abstraction can hide details needed for monitoring, operations, and diagnosis. The resulting position was that the interface may expose a unified quantum resource while still preserving the ability to inspect important internal components when needed.

Several resource characteristics were called out as different from conventional HPC resources:

- Deployment can vary. A quantum resource may be shared across a cluster, tied to an appliance, attached to a particular site, or exposed through a cloud provider.
- Availability is not binary or constant. Calibration, maintenance, drift, and health state can make the resource temporarily unusable.
- Authentication and access control are central. Vendor APIs and institutional security requirements often determine who may use a resource and how credentials are passed.
- Cost and accounting need explicit treatment. Participants distinguished usage cost, lost opportunity cost, blocking time, and wastage.
- Queuing can happen in more than one place. A workload manager queue and a vendor or device queue can both exist, producing the "double queue" problem.

## Scheduling, Telemetry, and Accounting

The group spent significant time separating what a scheduler needs from what monitoring and analytics systems need. For pure scheduling, participants argued that the workload manager generally needs summarized policy-relevant information: whether the resource is available, how much QPU time or blocked time was used, and whether a scheduling policy depends on a particular metric. It does not normally need high-frequency time-series data for every qubit or detailed machine state.

At the same time, operations, analytics, and predictive modeling groups do need richer telemetry. They may want health trends, calibration histories, usage traces, and data useful for training models that estimate future behavior. The conclusion was not that telemetry is unimportant, but that it should not necessarily be forced through the workload manager. Accounting, monitoring, analytics, and scheduling are related but distinct consumers of resource data.

Participants also discussed the need to attach meaningful metadata to jobs. This may include the calibration state at job submission and execution, device health, status, resource usage, and possibly warnings that previous compiled work has become stale because a device was recalibrated.

## Workflows and Workload Managers

The nomenclature discussion also clarified terms such as scheduler, resource manager, workload manager, and workflow manager. The scheduler decides when and where pending work runs. The resource manager tracks resources and their inventory or state. The workload manager combines scheduling and resource management. Workflow managers define task graphs, dependencies, execution order, data handling, and recovery.

Hybrid workflows were described as combinations of classical and quantum work, often structured as DAGs or iterative loops. Examples included VQE, QAOA, preprocessing, quantum execution, and postprocessing. Agentic workflows were raised as an emerging concern, but the group did not have enough concrete quantum/HPC agent workflow experience to turn that into requirements during this session.

## QDMI Status and Direction

QDMI was presented as an open-source, non-proprietary, C-based interface intended to provide a low-overhead and type-safe way to interact with quantum devices. The basic problem QDMI addresses is that a user or workflow may have multiple possible quantum devices, each with different availability, topology, calibration data, constraints, and submission mechanisms. Without a common interface, users and SDKs have to maintain separate stacks for each device.

QDMI currently supports three major interaction modes:

- Session establishment, including access control and connection setup.
- Querying device properties and constraints, including standardized and custom fields.
- Job submission and result handling.

The implementation model includes client, driver, and device layers. Providers implement a dynamic shared library at the device layer, and users interact through the driver layer. QDMI has demonstrated an end-to-end path involving Qiskit, IQM, and Slurm integration, including a Slurm/SPANK plugin. The IQM deployment model discussed included classical control/storage nodes associated with the quantum device, while also leaving open the possibility of on-premises or cloud access models.

Future QDMI work includes tighter compiler integration, support for more hardware and SDKs, more direct low-level support, and the ability to expose broader provider or cloud services. The proposed QDMI version 2 direction is a more modular architecture: a slim core interface with context, session, and module concepts, plus extension modules for QPUs, providers, orchestrators, hardware technologies, telemetry, and custom capabilities.

Participants emphasized that QDMI is not limited to superconducting systems. Examples mentioned included neutral atoms, ion traps, other superconducting systems, and simulator or classical resources.

## QRMI Status and Direction

QRMI was presented as a resource-management interface intended to bridge HPC workload managers and quantum vendors. The motivation is that HPC sites need a familiar and manageable way to expose quantum devices to users, while quantum vendors may not naturally integrate with HPC workload-management conventions.

The current QRMI work includes:

- A simple library interface.
- Slurm integration through a SPANK plugin.
- Unified job submission for classical and quantum work.
- A provider model that lets vendors implement functionality behind a common schema.
- Deployments or evaluations across multiple organizations and data centers, including work with IBM, Pasqal, and IQM evaluations.

A key operational problem is that quantum computers are not always available. The initial Slurm integration operated mainly during the job execution phase, which meant jobs could be dispatched to nodes before discovering that the quantum resource was unavailable. To reduce wasted allocations, QRMI explored using Slurm dynamic license mechanisms and a quantum resource monitor. The monitor polls the quantum API and sets the effective license count to zero when the device is unavailable, keeping jobs pending rather than dispatching them into an unavailable resource.

The group discussed the limitations of polling, including race conditions between polling, scheduler state, and actual device availability. Typical polling intervals such as 10 seconds were discussed, but the correct interval depends on load, API behavior, calibration duration, and scheduling cost.

PBS integration was also discussed. PBS runjob hooks can run before a job starts, allowing external availability checks before resources are reserved. This may provide a better pre-dispatch integration point than mechanisms that operate only after scheduling.

Future QRMI work includes accounting support, better credential management, and dynamic resource discovery. Credential handling was specifically identified as a security issue because passing user API tokens through environment variables can expose them through system tools.

## QFW Status and Role

QFW was presented as an execution framework for running quantum applications against simulators or hardware services, with a goal of providing a consistent workflow from laptop/container development to Slurm cluster execution. It was also framed as an experimentation platform where transport, authentication, benchmarking, scheduler behavior, and interface designs can be tested without forcing premature standardization.

QFW components discussed included:

- DEFW, a distributed execution framework and RPC mechanism connecting clients to services.
- QPMD services that stand in front of execution targets such as simulators or QPU resources.
- QHW, a schema intended to normalize hardware/resource information for applications and compilers.
- Application-facing APIs that protect users from vendor and interface churn.

QFW's principles were vendor neutrality, HPC-native operation, a common path from simulator to hardware, and open collaboration. The intended success criterion is that applications can be written against QFW once and then run against different vendors or backends without repeated application rewrites.

Five active QFW workstreams were described:

- A shim layer in front of QDMI and QRMI to identify common behavior and eventually drive convergence toward a common QRI specification.
- A libfabric transport experiment to evaluate Slingshot/RDMA-friendly communication and scalable client/server interaction.
- A C-based API or contract as an initial common interface surface.
- Benchmarking infrastructure that records measurements through QFW logs and extracts JSON and human-readable reports.
- Local admission and scheduling work, including separate repositories for admission and scheduling components.

The shim strategy was explicitly described as transitional. The goal is not to create another permanent interface, but to use an implementation to discover the differences, missing pieces, and user needs that can later inform a proper specification.

## Benchmarking and Evidence

Benchmarking was repeatedly identified as a necessary next step. Participants want measurements not only of quantum algorithm performance but also of middleware and scheduling behavior. Desired measurements include end-to-end runtime, correctness, interface overhead, scheduling latency, accounting behavior, transport overhead, queue wait time, and effects of different resource models.

The group discussed reusing existing benchmarking efforts such as FQT, Benchpress, Qiskit-related benchmarks, and MQS benchmarking work, while adding instrumentation in the layers that QRI/QFW owns. Participants also noted that benchmarking must account for hardware modality. For example, ion traps and neutral atoms may operate at much slower shot rates than superconducting devices, making network latency less important for some systems but still important for scale, resource usage, or high-concurrency scenarios.

OpenTelemetry-style formats were mentioned as a possible future path for making measurements compatible with broader observability tools.

## Operational and Site Integration Concerns

Several operations questions surfaced near the end of the session. Data storage and telemetry storage were highlighted as important. ORNL participants described existing large-scale telemetry storage practices for HPC systems and raised the question of where quantum-related telemetry, job metadata, calibration data, and outputs should live.

The group also discussed the boundary between a quantum appliance and the HPC site-managed environment. If a quantum system remains a black-box appliance, site integration can be lighter. If it is placed on the site's high-speed network, runs Slurm daemons, mounts the parallel file system, or requires site secrets, then it effectively becomes part of the site's operational responsibility. That boundary has major implications for security, file systems, network configuration, support, and ownership.

## Candidate Milestones and Artifacts

The session identified several candidate outputs:

- A QFW/IQM testbed and expansion to additional systems.
- Collection of usage and overhead data across QDMI and QRMI.
- One or more papers on usage patterns, benchmarking, and QDMI/QRMI comparison.
- An eventual quantum resource interface specification, potentially analogous in spirit to an MPI specification.
- Requirements or position documents for operations, monitoring, and analytics.
- Clearer owners for each milestone or artifact.

Session 01 closed with the sense that the community had made progress on defining the problem space, especially around terminology, resource boundaries, scheduling needs, and the need for measured evidence before standardization.

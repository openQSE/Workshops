# OpenQSE QRI Session 02 Detailed Summary

Source transcript: `openQSE-qri-session-02-transcript.docx`

## Session Purpose

Session 02 continued the morning's interface discussion but shifted toward stakeholder requirements and production implications. The main speakers covered Slurm/SchedMD directions, Flux and graph-based scheduling, Siemens HPC middleware perspectives, IBM LSF integration, and ORNL operations requirements. The session ended with a milestone and artifact discussion intended to produce concrete follow-up work.

## Slurm and Workload Manager Requirements

The Slurm-focused discussion identified the main problem areas for integrating QPUs into workload-managed HPC environments: user interface, scheduling, authorization, interaction with the QPU, teardown, avoiding jobs that should not run, and overall orchestration.

A central architectural question was how tightly a workload manager should integrate with a QPU or QPU controller. Running Slurm daemons close to or on the quantum control system can enable tight integration, but it changes the operational boundary. If a Slurm daemon runs there, the site may need to provide keys, file systems, networking, and other site-managed configuration. That makes the quantum appliance part of the supercomputing software stack rather than a vendor-managed black box.

Current community patterns were described as relying on Slurm licenses for scheduling and SPANK plugins for user interface and job integration. This approach works, but it creates multiple integration and configuration points. SPANK was called out as especially hard to distribute, maintain, upgrade, and iterate because it links into Slurm components and creates GPL and deployment concerns for vendor code. The recommendation was to leave SPANK primarily to site administrators and to provide alternatives for vendor-facing integration.

The scheduling requirements split into two major QPU usage models:

- Mutual exclusion, where one user or job has the QPU for a long interval.
- Time sharing, where multiple users or tasks share the QPU with internal biasing or scheduling.

Both need to be supported. Mutual exclusion maps naturally to allocation time and blocking cost. Time sharing may better support accelerator-like usage but creates more complicated usage, priority, and accounting questions. In either model, usage must feed billing, accounting, and fair-share priority.

Slurm resource models were compared. Consumable resources are not a good fit because QPUs are not standard node-local CPU or memory resources. GRES can be useful if a QPU is tied to particular nodes, but it does not naturally represent a cluster-wide shared QPU. Licenses are a better conceptual fit because they represent a global count of a resource. HRES, Slurm's hierarchical resource model, was presented as a promising direction because it can represent arbitrary cluster-attached resources and relationships, such as a QPU available to a rack or a subset of nodes.

The planned HRES/Lua work was a major focus. The intent is to provide a Lua-based, stackable plugin mechanism for HRES resources in the November 2026 Slurm release cycle. Important planned features include:

- Per-resource Lua plugins, allowing different QPUs or vendors to define different behavior.
- Inline administrator configuration and scoped user arguments.
- A polling maintenance hook that can adjust resource availability, such as setting a QPU count to zero during maintenance or calibration.
- A pre-run hook after scheduling but before allocation, useful for fresh scoped credentials or external authorization.
- Node and task prolog/epilog hooks for setup, service start, environment variables, and teardown.
- A post-run hook for cleanup.

The pre-run credential model was highlighted as a way to avoid long-lived user tokens. Instead of requiring users to create tokens that survive long queue waits, the scheduler can request fresh authorization only when a job is actually about to run, scoped to the job location and duration.

The group also discussed external jobs: representing work that is part of a workflow but not actually running on cluster compute nodes, such as a QPU-side execution or cloud-side task. This was not committed for the immediate Slurm release, but it was identified as a potentially important future mechanism for modeling quantum workflows and avoiding invisible external queues.

## Flux and Graph-Based Scheduling

Vanessa Sochat presented work from Lawrence Livermore on hybrid quantum/classical workload management using graph-based scheduling. The presentation began from application patterns, arguing that scheduler design should follow application design. Four quantum application patterns were described:

- Single evaluation: build a circuit, send it to a device, receive a result.
- Iterative loops such as QAOA or VQE: classical computation and quantum execution repeat until convergence.
- Batch or embarrassingly parallel work: many independent circuits run and later synchronize.
- Fan-out: a quantum result feeds multiple postprocessing consumers.

The central scheduling problem is that quantum systems often have external vendor queues. If a user allocates classical resources and then waits in a quantum queue, expensive CPUs or GPUs can sit idle. This was described as the two-queue problem. It becomes more severe for iterative algorithms where quantum and classical work alternate many times.

The presentation distinguished scheduling integration from execution integration. Scheduling integration makes quantum a first-class schedulable resource and avoids wasted allocations. Execution integration provides wrappers, environment setup, API calls, and user convenience. Production systems need both.

Flux was presented as a framework with hierarchical job management and graph-based scheduling. Its scheduler, Fluxion, models resources as nodes and edges in a graph. Because the graph can represent arbitrary resources, quantum devices can be modeled alongside racks, nodes, sockets, or other custom resources. This graph model is useful for converged computing and future custom resource types.

The first implementation path used Kubernetes through Fluence, a custom scheduler plugin originally developed for gang scheduling. In the quantum design, a leader pod submits quantum work and a sidecar monitors the external queue. When the quantum resource is ready, the sidecar untaints or releases the classical workers so they start close to the quantum execution time. Experiments showed substantially lower idle time than default scheduling, and additional logic could select devices based on queue depth or cost.

The next design moves the idea into Flux Core. It adds quantum resources as a subgraph and uses a held reservation for classical resources. A scout job monitors the external quantum queue. When quantum is ready, the scout fires an owner-only RPC to release the hold, allowing classical compute to start. Session information can be shared through a file system or preferably through Flux's key-value store.

Workflow tools were discussed as complementary. Tools such as Snakemake can package vendor-specific logic, define a DAG, validate environments, and submit steps to a workload manager. However, they cannot co-schedule quantum and classical resources by themselves because they still submit to an executor or workload manager. The desired model is workflow tools for orchestration and workload managers for co-scheduling.

Feedback to QRMI included a request for a stronger acquire model: an acquire call that actually gets a foot in the queue and returns a session ID decoupled from application submission. Polling that ID is usable today, but event-driven notifications would be better long term.

## Responsibility Boundaries

A discussion followed about whether resource management interfaces, workload managers, and workflow managers risk overlapping responsibilities. The group generally agreed that resource interfaces such as QDMI and QRMI should expose the API calls, status, and resource information that schedulers and workflows need. They should not become full schedulers or workflow managers.

The workload manager should own allocation and scheduling decisions. Workflow tools should own DAG construction, packaging, and orchestration logic. Resource interfaces should make quantum resources visible and controllable enough for those layers to do their jobs. Participants cautioned against pushing all responsibility into the scheduler or bloating resource interfaces into systems that try to do everything.

## Siemens HPC Middleware Perspective

Siemens presented its HPC portfolio in the context of Altair, Univa, PBS, Grid Engine, and Runtime Design Automation capabilities. Quantum was framed as a new class of HPC resource that should be integrated using established workload, workflow, telemetry, and licensing expertise.

The QRMI runjob-hook approach was seen as a good initial pattern because it is familiar to admins and enables reservation checks before dispatch. Suggested improvements included finer allocation granularity, tighter workflow/workload coordination, richer metrics and capabilities from QPUs, and movement toward QPU pooling.

The Siemens perspective emphasized decomposing hybrid workflows into small, discrete units of work. Smaller tasks improve packing, reduce idle time, and allow classical and quantum resources to turn over more efficiently. A dynamic resource scheduler was proposed as a user-space layer between workflow tools and underlying workload managers. It can scale up classical preprocessing tasks, launch quantum tasks, and coordinate postprocessing without requiring every site to modify its core workload manager.

Multi-phase jobs were discussed as another pattern. A single job could move through phases with different resource requirements, returning to the queue and reacquiring different resources for preprocessing, QPU execution, and postprocessing. This could avoid holding large classical allocations during low-resource quantum-execution phases.

To avoid too much overhead from very fine task decomposition, Siemens proposed batch jobs with individual task tracking. A persistent interpreter or worker can process many small tasks without reacquiring and releasing the quantum resource for every task, while still preserving detailed accounting for individual work items.

Rich resource descriptions were highlighted as essential. Users should be able to request capabilities rather than a single named QPU, especially as sites move toward pools of heterogeneous QPUs. Siemens also described HERO, an EDA hardware-emulator scheduling product, as an analogy for a possible QPU pool manager called by workflow tools.

## IBM LSF and QRMI Integration

IBM presented LSF integration work for hybrid quantum/classical workflows. The team built open-source integration scripts using LSF hooks and QRMI to execute tasks on IBM quantum processors. They also created a resource information plugin that can collect QPU information and feed it into the LSF scheduler.

The example workload was sample-based quantum diagonalization (SQD) from Qiskit add-ons, used to estimate molecular ground-state energy. The team decomposed a monolithic notebook/script into four stages:

- Map classical inputs to a quantum problem.
- Optimize the problem for a target QPU topology.
- Execute on the QPU and collect results.
- Postprocess the output.

These stages were submitted as separate LSF jobs with dependencies so that stage `i + 1` starts only after stage `i` completes successfully. Data was exchanged through the parallel file system. This decomposition lets the workload manager assign appropriate resources to each stage. For example, stages one and four are CPU bound, while the QPU execution stage may mainly hold a placeholder slot while waiting for quantum execution.

The LSF integration uses an `ESUB.qpu` submission hook to parse quantum-specific arguments, validate or augment job metadata, and apply site policies before the job enters LSF. A `JobStarter.qpu` component runs immediately before the workload starts on the execution host. It queries IBM Quantum APIs through Qiskit/REST interfaces, identifies devices available to the user, checks topology and operational characteristics, selects an appropriate QPU based on requirements and queue length, and exports QRMI environment variables for the workload.

Open issues and feedback areas included:

- Accounting: what QPU was used, how much it cost, whether it was used well, and where those records should live.
- Pending time estimation: queue depth alone does not reliably predict how long a QPU task will wait.
- Placement: topology, coherence, error rates, and other device characteristics should influence selection.
- Scheduler integration: LSF's plugin architecture may support more advanced placement in the future, but user feedback is needed.

## ORNL Operations Requirements

Michael presented ORNL operational requirements and lessons from the NCCS/OLCF operations perspective. The key message was that production quantum/HPC integration is not only a scheduler or API problem; it also affects accounts, allocations, security, data services, acceptance, user support, documentation, licensing, downtime procedures, and reporting.

The accounts team cares about user volume, application volume, restricted users, citizenship or export-control rules, user agreements, and what can legally be publicized about the machine. Even publishing error rates or device status may require legal and vendor review. They also need clear allocation policies: whether quantum time is allocated through existing programs, new programs, node hours, core hours, qubit hours, wall time, or some other unit.

The software services development team manages systems such as RATS, ORNL's resource and allocation tracking system. RATS tracks projects, allocations, users, file system quotas, Unix groups, system access, and Slurm policy. For quantum integration, RATS needs to know what units are charged and how penalties or queue priority should be applied when users exceed allocations.

Security and enclave behavior were raised as practical blockers. Production HPC systems and quantum testbeds may both be in moderate enclaves, but that does not automatically mean they can communicate. API and token behavior is also a concern. Even on-premises quantum systems may still behave like cloud resources if users must log into a GUI to generate API tokens. Operations teams would prefer that user-facing experience to be abstracted and managed securely.

Data and platform services raised Kubernetes and cloud-style provisioning questions, including whether hybrid environments will combine Slurm-managed and Kubernetes-managed resources, and how internal container services such as Slate might fit.

System acceptance and user environment teams raised questions about how to accept statistical quantum systems. HPC acceptance often relies on reproducibility and benchmark thresholds. Quantum devices produce statistical results, may have different modality-dependent behavior, and may require different acceptance criteria for vendor acceptance, science engagement acceptance, and production operations acceptance. The term "acceptance" itself may need careful definition.

Software licensing was identified as a lesson learned from prior quantum systems. Proprietary emulators or licensed components can create site policy and security issues even if the rest of the stack is open source. Operations teams prefer open source where possible and need legal/security review before exposing licensed software to users.

User assistance was described as the "glue" across the process. The user support team needs to be looped into calibration expectations, documentation, training, change management, incident communication, and quantum-specific support pathways. The group may need something like a quantum center of excellence or vendor support path, similar to existing HPC vendor support arrangements.

Downtime and health status were also major themes. Quantum resources may go up and down often due to calibration or health changes. Operations must communicate that without overwhelming users. The impact on non-quantum users depends on how resources are partitioned and co-scheduled. QPU health may resemble file system health more than node health: not simply up or down, but degraded, hanging, partially available, or dependent on workload behavior.

Additional open questions included where production ownership transfers from quantum/HPC integration teams to operations, what scale is actually required for Frontier or future leadership systems, and how node sharing will affect co-scheduling, resource accounting, and security.

## Wrap-Up and Milestones

The closing discussion focused on concrete artifacts. The group agreed that smaller, focused working meetings are needed outside broad OpenQSE calls so workload-manager and interface topics do not get drowned out by unrelated quantum discussions.

Proposed next steps included:

- Michael and Tim should collate operations, monitoring, and telemetry concerns into a requirements document.
- Interface teams such as QDMI and QRMI should respond to those requirements with plans or explanations, even if no immediate interface change is needed.
- QFW testbed work should define milestones for usage-pattern measurement, transport benchmarking, admission control, and scheduler behavior.
- Data collection should support joint papers, including QDMI/QRMI comparisons and computational pattern analysis.
- The Slurm HRES/Lua work is expected in the November 2026 release timeframe, with possible community evaluation or technical communication afterward.
- A private draft specification could be circulated around January or February 2027 for feedback from interface, compiler, workload manager, and stakeholder groups before any public specification.
- Quantitative analysis should target the first quarter of 2027.
- The group should evaluate polling concepts, add other workload managers or workflow tools to the QFW testbed, and establish real workflows rather than only infrastructure tests.
- The testbed should target IQM/QFW integration and ideally expand toward three systems, depending on ORNL and partner availability.

The session closed with broad agreement that the day produced more questions than final answers, but also a concrete path: requirements documents, focused meetings, testbed measurements, early drafts, and joint artifacts that can turn interface debates into evidence-based design.

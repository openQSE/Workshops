# OpenQSE QRI Main Points

Sources: `openQSE-qri-session-01-report-out-summary.md`, `openQSE-qri-session-02-report-out-summary.md`, `openQSE-qri-session-01-detailed-summary.md`, `openQSE-qri-session-02-detailed-summary.md`

## 1. What were the main points discussed?

- Definition and scope of a quantum resource in HPC environments.
- Separation of responsibilities between quantum interfaces, workload managers, workflow managers, monitoring systems, and operations teams.
- How schedulers should represent and manage QPUs, including availability, health, calibration, authentication, queueing, and accounting.
- QDMI, QRMI, and QFW as complementary interface/framework efforts.
- Hybrid quantum/classical workflow scheduling, especially the problem of classical resources waiting idle while quantum work sits in a separate queue.
- Slurm, Flux, PBS, and LSF integration approaches for exposing QPUs to production HPC users.
- Mutual-exclusion versus time-shared QPU use models.
- Credential handling, security, export restrictions, user onboarding, allocation units, software licensing, and operational support.
- Benchmarking, telemetry, usage-pattern collection, and testbeds needed to produce evidence for future interface specifications.
- Follow-up artifacts, including requirements documents, testbed milestones, papers, and a future draft quantum resource interface specification.

## 2. What conclusions were reached regarding the main points?

- A quantum resource should usually be treated as more than the QPU itself. It includes the control systems, APIs, classical services, and access mechanisms required to make the QPU usable.
- No single layer should own everything. Resource interfaces should expose status and control, workload managers should schedule and allocate resources, workflow tools should manage task graphs, and operations systems should handle monitoring, policy, support, and reporting.
- Schedulers need concise, policy-relevant status such as availability, authorization, usage, queue state, and accounting data. Detailed telemetry should remain available, but it should mainly serve monitoring, analytics, operations, and predictive modeling rather than burdening the scheduler.
- QDMI, QRMI, and QFW are not final standards yet. They should be used as practical experiments and testbed components to discover shared requirements and guide convergence toward a future specification.
- Production systems must support both exclusive QPU allocations and time-shared QPU execution. Both models need accounting, fair-share policy, billing, and usage tracking.
- The "two queue" problem is one of the central scheduling issues. Classical resources should not be allocated too early if quantum work is still waiting in a vendor or device queue.
- Workflow tools such as Snakemake are useful for packaging and orchestration, but they cannot solve quantum/classical co-scheduling by themselves. Workload managers still need first-class quantum resource awareness.
- Slurm HRES/Lua hooks, PBS runjob hooks, Flux graph scheduling, and LSF/QRMI hooks were all seen as promising integration paths, with different strengths depending on site architecture.
- Credential handling must improve. The group favored scoped, short-lived authorization issued close to job start time instead of long-lived user tokens passed through insecure mechanisms.
- Benchmarking and measured testbed data are required before standardization. The group wants evidence about scheduling latency, interface overhead, queue behavior, correctness, usage patterns, accounting, and operational impact.
- Operations requirements are essential, not secondary. Successful quantum/HPC integration must address accounts, allocations, security, acceptance testing, downtime communication, user support, licensing, and effects on non-quantum users.
- The group did not try to finalize a standard during these sessions. The agreed path is iterative: gather requirements, run testbeds, collect measurements, hold focused working meetings, produce papers and requirements documents, and circulate an early private draft specification around early 2027.

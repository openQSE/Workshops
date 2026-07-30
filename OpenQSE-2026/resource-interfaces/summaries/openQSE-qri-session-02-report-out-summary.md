# OpenQSE QRI Session 02 Report-Out Summary

Source transcript: `openQSE-qri-session-02-transcript.docx`

Session 02 focused on stakeholder requirements from workload managers, workflow systems, enterprise HPC middleware, LSF integration, and ORNL operations. The discussion moved from interface concepts into practical scheduling, allocation, accounting, credentials, acceptance, and production-readiness concerns.

Key highlights:

- Slurm requirements centered on modeling QPUs as external or cluster-wide resources, avoiding brittle SPANK-based vendor integrations, and using HRES/Lua hooks for availability, pre-run authorization, lifecycle setup, cleanup, and accounting.
- The group distinguished mutual-exclusion QPU use from time-shared QPU use. Both models must be supported, and usage must feed accounting, billing, and fair-share policy.
- Flux presented graph-based scheduling for hybrid quantum/classical workflows. The key problem is the "two queue" issue: classical resources can sit idle while a quantum vendor queue waits. Flux/Fluence approaches use scout or sidecar processes to coordinate quantum readiness with classical allocation.
- Workflow tools such as Snakemake can package task logic and DAGs, but they cannot solve co-scheduling alone; workload managers still need to coordinate classical and quantum resources.
- Siemens described quantum as a new HPC resource type and emphasized granular workflow decomposition, dynamic resource scheduling, multi-phase jobs, task batching, rich resource descriptions, and eventual QPU pooling.
- IBM/LSF described QRMI-based integration scripts for IBM Quantum, including job submission hooks, QPU selection, and a decomposed SQD workflow with classical, QPU, and postprocessing stages.
- ORNL operations raised production concerns: user onboarding, export/security restrictions, allocation units, RATS integration, reporting, API/token UX, acceptance testing for statistical quantum systems, software licensing, user support, downtime communications, and impacts on non-quantum users.
- The wrap-up identified concrete next steps: focused QRI working meetings, an operations requirements document, QFW testbed milestones, usage-pattern data collection, joint papers, Slurm HRES/Lua evaluation, a private draft specification around early 2027, and expansion toward real workflows and multiple systems.

Overall, Session 02 clarified that interfaces, workload managers, workflow tools, and operations systems must cooperate without collapsing responsibilities into one layer. The strongest consensus was that production quantum/HPC integration needs better accounting, credential handling, availability signaling, and measured testbed data.

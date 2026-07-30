# OpenQSE QRI Session 02 Main Points

Source: `openQSE-qri-session-02-transcript.docx`

## Main Points

- The session focused on practical requirements from workload managers, workflow systems, enterprise HPC middleware, LSF integration, and ORNL operations.
- Slurm requirements centered on modeling QPUs as external or cluster-wide resources while avoiding brittle vendor-specific integrations.
- The group distinguished mutual-exclusion QPU use from time-shared QPU use, with both needing accounting, billing, and fair-share support.
- Flux and workflow-system discussions highlighted the two-queue problem, where classical resources can sit idle while a quantum vendor queue waits.
- Production concerns included onboarding, export/security restrictions, allocation units, reporting, API/token UX, acceptance testing, software licensing, support, and downtime communication.
- Next steps included focused QRI meetings, operations requirements, QFW testbed milestones, usage data, Slurm HRES/Lua evaluation, and a private draft specification around early 2027.

## Conclusions

- Interfaces, workload managers, workflow tools, and operations systems must cooperate without collapsing responsibilities into one layer.
- Production quantum/HPC integration needs stronger accounting, credential handling, availability signaling, and measured testbed data.

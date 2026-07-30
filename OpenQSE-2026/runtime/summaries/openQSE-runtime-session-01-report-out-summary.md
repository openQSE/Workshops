# OpenQSE Runtime Session 01 Report-Out Summary

## Slide 1: Bottom Line

Key message: The group aligned on an evidence-driven path toward quantum-HPC runtime interfaces.

- Do not start with a final abstract standard.
- Start with runnable prototypes, shared workflows, and an experimental matrix.
- Use results to derive runtime criteria, object models, and interface boundaries.
- Primary deliverables: working group, prototype inventory, reference experiments, criteria/API document, and white paper or demo.

Speaker note: The meeting produced more alignment on process and milestones than on a finished technical specification.

---

## Slide 2: Reference Application

Key message: Fragment-based quantum chemistry provides a concrete workflow for runtime evaluation.

- Application: FMO/VQE workflow for molecular clusters.
- Classical HPC preprocessing creates embedded monomer/dimer Hamiltonians.
- Quantum execution refines fragment energies using compact ansatz circuits.
- Classical postprocessing reconstructs global energies and properties.
- Current milestones: helium clusters/salt droplets, rare-earth solvation extension, integration with resource management/scheduling framework.

Speaker note: This workflow is useful because it spans loose classical preprocessing, medium-coupled hybrid loops, and possible future tight feed-forward/FTQC behavior.

---

## Slide 3: Runtime Architecture Need

Key message: The runtime layer should be an application-facing execution contract over heterogeneous resources.

- Represent hybrid workloads as hierarchical task graphs.
- Coordinate CPU, GPU, FPGA, QPU, and QPU mesh resources.
- Express dependencies, timing constraints, data movement, and resource needs.
- Interoperate with SDKs, compilers, resource managers, and admission control.
- Avoid idle time and expose enough capability/telemetry for algorithm co-design.

Speaker note: The graph should not be flat down to every gate or pulse. Lower layers can handle device-specific mapping and control.

---

## Slide 4: Main Design Tensions

Key message: The architecture must support layered users and layered runtimes.

- Logical vs physical: applications prefer logical targets; kernel/QEC/device developers need physical detail.
- Workflow vs device runtime: orchestration and low-level dispatch may be complementary layers.
- Compiler vs runtime: compiler can transform graphs; runtime optimizes launch, placement, setup, and dispatch.
- Latency vs throughput: dispatch needs different modes for QEC-style latency and application-level time-to-solution.
- Open scope question: OpenQSE should likely focus first on application/logical/resource graph interfaces.

Speaker note: The group recognized that QPUs are not GPUs, but GPU runtime lessons remain valuable.

---

## Slide 5: Proposed Milestones

Key message: The near-term plan should create comparable evidence across implementations.

- Milestone 1: Assign a small runtime working group and owner.
- Milestone 2: Inventory existing prototypes and classify them by layer.
- Milestone 3: Define experimental quadrants: latency, HPC intensity, coupling level, and dispatch mode.
- Milestone 4: Deploy reference prototypes and run common workflows.
- Milestone 5: Extract runtime criteria, object model, API sketch, and compiler/runtime boundaries.
- Milestone 6: Produce white paper and/or technical demo, with SC/arXiv discussed as a possible target.

Speaker note: A hackathon or bake-off style comparison was proposed as a practical way to evaluate multiple approaches.

---

## Slide 6: Immediate Deliverables

Key message: The next report-out should turn discussion into concrete artifacts.

- Working group charter and named owner.
- Prototype inventory with status, docs, openness, and deployment readiness.
- Two to four reference workflows, including FMO/VQE and a latency-sensitive case.
- Experimental matrix and acceptance criteria for "runnable" prototypes.
- First draft of runtime criteria and logical/physical abstraction policy.
- Decision on white paper, demo, or both.

Speaker note: The first useful output is a baseline framework that contributors can target, not a bespoke solution for one application.

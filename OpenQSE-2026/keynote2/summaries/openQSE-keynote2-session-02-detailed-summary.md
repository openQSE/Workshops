# OpenQSE Keynote 2 Session 02 Detailed Summary

Source transcript: `openQSE-keynote2-session-02-transcript.docx`

## 1. Keynote Context

### 1.1 Speaker and framing

- The second part of the day included a keynote by Laura Schulz.
- The talk was framed as a broader ecosystem view rather than a narrow technical session.
- Laura said the working groups had spent months zooming into technical details.
- This talk was intended to zoom out and connect those details to national and ecosystem-level developments.

### 1.2 Active external context

- Laura described the week as unusually active for quantum and scientific computing.
- Events mentioned included QCUF, OpenQSE, the DOE Quantum PI/Oscar PI meeting in Maryland, the Genesis Summit, and a major global quantum summit in Chicago.
- The talk placed OpenQSE in the larger quantum-HPC ecosystem context.

## 2. DOE Quantum Supercomputing Blueprint

### 2.1 Blueprint release

- Laura highlighted the newly released DOE quantum supercomputing blueprint.
- She had served on the committee that worked on the document for roughly nine months.
- The blueprint was described as a national-lab-driven planning document for the next phase of quantum computing.

### 2.2 2028 target

- The blueprint includes a threshold target of at least 100 logical qubits.
- The system should run about 10^4 to 10^5 hard logical operations by 2028.
- Laura acknowledged that this target may seem ambitious to some participants.
- The target is meant as a threshold for early fault-tolerant progress.

### 2.3 Competencies around the system

- The blueprint is not only about building hardware.
- The community must build competencies around the system.
- Needed competencies include:
  - Programming expertise.
  - Early system software.
  - Compilation toolchains.
  - Standardization.
  - Benchmarks.
  - Ability to execute target applications.

### 2.4 Breakthrough science

- The third pillar is using early fault-tolerant systems to achieve new science.
- The blueprint includes a roadmap for logical qubit growth into the 2030s.
- Laura highlighted application thresholds mentioned in the document.
- Quantum chemistry embedding and fragmented workflows were presented as likely early success areas around the 2028 timeframe.

### 2.5 Quantum chemistry and embedding examples

- Laura discussed quantum chemistry fragmented embedding.
- The pattern is to run a large-scale simulation classically while offloading selected high-value parts to quantum.
- The quantum component can improve accuracy or resolution for a difficult subproblem.
- Results are then pulled back into the overall simulation.
- This pattern was connected to work by Peter Coveney, Argonne, Oak Ridge, and other teams.

## 3. Ecosystem Readiness Before Hardware Readiness

### 3.1 Risk of reaching hardware target without usability

- Laura described a discussion from the DOE Quantum PI meeting.
- Some participants focused on letting companies do what they need to do to hit the 2028 hardware threshold.
- Laura accepted that this may be necessary for hardware progress.
- However, she emphasized that once a system reaches the threshold, users will immediately ask how to use it.
- If the ecosystem is not ready, the community will lose momentum and user engagement.

### 3.2 OpenQSE role

- OpenQSE was presented as a mechanism to build the ecosystem before the hardware arrives.
- Laura noted that some DOE quantum community members were not aware of OpenQSE.
- She suggested the project needs more visibility, especially among more pure quantum software and DOE quantum groups.

### 3.3 Exascale Computing Project analogy

- Laura compared the situation to the Exascale Computing Project.
- ECP was a large, multi-year effort to prepare software, applications, and users for exascale systems.
- She had reviewed ECP lessons learned and discussed the topic with Mike Heroux and Lois Kaufman.
- ECP participants have practical experience and lessons that could help OpenQSE.

### 3.4 Potential white paper

- Laura suggested a white paper or similar study comparing OpenQSE ecosystem needs to ECP lessons.
- One missing element in the blueprint, in her view, was a concrete plan for long-term software support after the initial hardware target.
- ECP showed that software readiness and post-delivery support must be planned early.

## 4. Genesis, American Science Cloud, And Federation

### 4.1 Genesis Summit themes

- Laura connected the talk to the Genesis Summit held earlier that week.
- She summarized Gina's remarks on federation and shifting burden from scientists to infrastructure.
- The goal is not one centralized system but a federated ecosystem of systems.

### 4.2 No single policy or central system

- Federation means systems will be distributed.
- There will not be one policy, one implementation, or one owner.
- Systems will belong to different institutions and operate under different technical and administrative models.
- This creates nontrivial technical, policy, and governance challenges.

### 4.3 Scarce quantum resources

- Quantum systems will be scarce.
- There may be only a few within the DOE complex and a few regional or national systems.
- Scarcity makes scheduling, access, allocation, and idle-time protection especially important.
- End-to-end workflows may need to cross institutional boundaries.

### 4.4 Identity, trust, and data movement

- Federation requires trust and identity mechanisms.
- Single sign-on was mentioned as part of Genesis.
- The community must consider trust for data passing, authentication, authorization, and cross-lab reciprocity.
- OpenQSE should identify what is quantum-specific versus what Genesis and DOE cyberinfrastructure already cover.

### 4.5 DOE, regional, and company differences

- DOE federation is only one layer.
- Regional ecosystems and company-owned systems may have different requirements.
- OpenQSE should consider how workflows cross DOE, regional, university, and industry boundaries.

## 5. Three Ecosystems Framework

### 5.1 Infrastructure ecosystem

- The infrastructure ecosystem includes geography, networking, co-locality, resource ownership, policy, scheduling, and allocation.
- Laura used the Chicago ecosystem as an example.
- The Illinois Quantum Microelectronics Park, SiQuantum, universities, Argonne, Fermilab, NSF hubs, and industry efforts form a dense quantum ecosystem.
- The question is how to run end-to-end workflows across this environment.

### 5.2 Chicago example details

- The Chicago area has quantum hardware initiatives and HPC assets.
- Industry and national labs are both involved.
- Questions include:
  - Where are systems physically located?
  - What networking connects them?
  - What software environment supports them?
  - How are access, allocation, scheduling, and resource management handled across institutions?
  - How is idle time protected when resources are scarce?

### 5.3 Technical ecosystem

- The technical ecosystem includes all components required for end-to-end operation.
- Laura listed:
  - Workflow orchestration.
  - Scheduling.
  - Observability.
  - Telemetry.
  - Reproducibility.
  - Data management.
  - Security.
  - Governance.
  - Policy.
- HPC and quantum communities still have cultural boundaries.
- AI is becoming a third ecosystem that will also need integration.

### 5.4 Community ecosystem

- The community ecosystem is about users and how they actually work.
- Laura described prior persona work for HPC users at LRZ.
- Example categories included clickers, librarians, power users, and bare-metal users.
- She has also been thinking about AI user personas.
- Quantum needs the same kind of structured user analysis.

## 6. User Personas

### 6.1 Why personas matter

- The phrase "users want this" is not meaningful without defining the user.
- Many current quantum users are close to the hardware.
- They understand qubits and low-level details.
- Future domain scientists should not need that level of hardware knowledge.
- Workflow requirements must be tied to user types.

### 6.2 Quantum persona work

- Laura referenced work with colleagues in Germany on quantum user personas.
- The initial work was based on interviews at IEEE Quantum Week and a survey.
- The sample size was small.
- The community should expand and refine this work.

### 6.3 Relationship to workflow group

- Laura argued that the workflow group should assign user personas to workflows.
- The same application may require different workflows depending on user expertise.
- Power users, early adopters, and later domain scientists will need different abstractions and support.
- Persona-driven requirements will help define the right architecture.

## 7. Comparing Quantum, HPC, And AI By Layer

### 7.1 Hardware resources

- Laura proposed comparing resources across quantum, HPC, and AI.
- Hardware questions include:
  - What partitions are needed?
  - What file systems are required?
  - What simulator profiles do quantum users need?
  - What latency and networking are required?
  - What access models are appropriate?

### 7.2 Software platforms

- HPC has schedulers and MPI.
- Quantum needs analogous or compatible scheduling and runtime concepts.
- AI frameworks are also part of the larger ecosystem.
- Genesis design decisions may affect future quantum integration.
- OpenQSE should stay close to Genesis development so quantum requirements are not missed.

### 7.3 Services and support

- Services and support are key to making systems usable.
- Different user personas will need different support models.
- Support requirements should be part of architecture thinking, not an afterthought.

### 7.4 Governance and policy

- Laura asked what happens when a QPU is at one institution and the HPC system is at another.
- Which system is the manager and which is the worker?
- How does a quantum science center or instrument center receive jobs from a remote HPC system?
- Does this affect scheduling and resource management?
- What happens if calibration drift or another QPU-side event disrupts an HPC allocation?

## 8. Main Takeaway

- Laura's main takeaway was that usable quantum advantage will come from ecosystem design, not qubit technology alone.
- Hardware vendors have a difficult job, but system usability depends on the surrounding ecosystem.
- OpenQSE should think about:
  - Users and user needs.
  - Locality and co-locality.
  - Federation and integration.
  - HPC and AI alignment.
  - Avoiding siloed quantum-only solutions.

## 9. Q&A And Discussion

### 9.1 Single sign-on and identity

- A participant asked about user identity across federated national lab systems.
- The answer was that technical mechanisms resemble cloud identity and service-mesh approaches.
- AMSEC identity documents were mentioned.
- The harder part is policy reciprocity across labs.
- One lab must trust another lab's identity and access decisions.
- DOE is actively paying attention to the issue.

### 9.2 European federation examples

- Laura mentioned EuroHPC efforts to federate HPC and AI systems.
- Quantum systems are being integrated through data centers.
- She hopes to learn from European examples of reconciling quantum and HPC access.

### 9.3 Quantum persona data source

- A participant asked where the quantum persona work came from.
- Laura said it came from a German group, including doctoral students and Wolfgang Maurer.
- The work involved interviews at IEEE Quantum Week and likely a survey.
- The data is early and needs expansion.

### 9.4 Organizational readiness and bottlenecks

- A participant asked about bottlenecks given different levels of organizational readiness.
- Laura answered that communication is the core challenge.
- Ecosystem alignment must be repeated because new groups and efforts continually appear.
- The environment is changing across hardware, software, fault tolerance, HPC, AI, and policy.

### 9.5 Blueprint feedback

- Laura displayed the blueprint QR code again.
- She said Section 5 focuses on ecosystem and integration components.
- She invited feedback, especially from this group, because many issues in the blueprint overlap with OpenQSE work.

### 9.6 Data governance and security

- A participant asked whether data governance should be policy or an OpenQSE architecture layer.
- Laura did not give a definitive answer.
- She suggested following Genesis where appropriate and identifying quantum-specific needs.
- The discussion noted that AI has developed control-plane and governance-layer ideas.
- Quantum may need similar concepts.

## 10. Technical Transition To Workstreams

- The session ended by sending participants back to workstream rooms.
- Software architecture was called out as switching to control electronics.
- Participants were encouraged to join that group if interested.

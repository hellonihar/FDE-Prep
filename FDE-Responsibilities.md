# Forward Deployed Engineer (FDE) — AI Implementation Responsibilities Reference

**Version:** 2.1
**Last Updated:** 2026-10-21
**Tags:** #forward-deployed-engineer #AI #LLM #RAG #deployment #FDE #career-reference

---

## 1. Role Overview

A **Forward Deployed Engineer (FDE)** in AI implementation is an embedded builder who bridges the gap between frontier AI models and production-grade systems within customer environments. Unlike traditional deployment engineers, the AI FDE doesn't just install infrastructure — they **design, build, evaluate, and optimize** AI applications (RAG pipelines, agentic workflows, fine-tuned models) directly alongside customer teams, compressing adoption cycles from months to weeks.

**Core mandate:** Turn AI research breakthroughs into working production systems at strategic customers, capture field learnings as eval-driven feedback that changes product and model roadmaps, and codify repeatable patterns that scale beyond any single deployment.

**What this role is NOT:**
- Not a pure research scientist (you ship products, not papers)
- Not a pure solutions architect (you write production code, not just diagrams)
- Not a pure consultant (you build and own the system, not just advise)
- Not a pure MLOps engineer (you operate at the customer's edge, not just internal infra)

The AI FDE is a **hybrid role** requiring simultaneous competency in **ML engineering, full-stack development, data engineering, systems integration, and customer-facing communication** — plus the autonomy to operate independently in ambiguous, high-stakes customer environments.

**The FDE operates at the frontier of enterprise AI deployments**, sitting between Research/Product and the customer's production reality. You are the eyes, hands, and voice of the AI platform in the field.

---

## 2. Core Responsibility Pillars

The role decomposes into seven pillars:

| # | Pillar | One-Line Summary |
|---|--------|-----------------|
| A | Discovery, Scoping & Architecture Design | Understand the problem before touching a keyboard |
| B | Infrastructure & GPU/Edge Provisioning | Build the foundation that inference depends on |
| C | Model Deployment & Inference Stack | Deploy models and build the AI application layer |
| D | Evaluation, Red-Teaming & Go-Live | Prove it works — and prove it's safe |
| E | Production Monitoring, Debugging & Optimization | Keep it running, make it faster, make it cheaper |
| F | Playbooks, Reusable Patterns & Knowledge Transfer | Make the next deployment 10x easier |
| G | Feedback Loop to Research & Product | Change the model and product with field truth |

---

## 3. Detailed Task Breakdown by Pillar

### Pillar A: Discovery, Scoping & Architecture Design

> **Phase playbook:** [AI-SDLC Phase 0 — Discovery & Scoping](AI-SDLC/00-discovery-and-scoping.md) covers the same ground as a gated workflow with artifacts, a gate checklist, and a full tool reference. This section is the *how* — procedures, templates, and worked cases.

<details>
<summary><b>18 core tasks — detailed breakdown</b> (click to expand)</summary>

#### Case Index

Five composite business cases are used throughout this section. Each is invented but grounded in a real engagement shape; the numbers stay consistent across every task they appear in, so you can trace one engagement end to end.

| # | Case | Sector & Scale | The Ask | Hard Constraint | Instructive Because |
|---|------|----------------|---------|-----------------|--------------------|
| **1** | **Northwind Mutual** | P&C insurer · 40k claims/yr · 400 adjusters | "Cut claims handling cost 30%" | Medical PII inside claim files, state regulators, US-only residency | Free-text adjuster notes are the hidden cost centre |
| **2** | **Meridian Bank** | Regional bank · 60k KYC files · 240 branches | "Clear the KYC backlog without raising regulatory findings" | SEC/FINRA, immutable audit trail, no external API for PII | A bounded classification task, not a chat assistant |
| **3** | **Corvid** | B2B SaaS · 12k tickets/day · 340 agents | "Deflect 40% of tier-1 without raising CSAT" | <4s latency, $0.04/request ceiling, EU data residency | The cost ceiling forces routing; agentic is out of scope for v1 |
| **4** | **St. Aldwyn Health** | 6 hospitals · 1.1M encounters/yr | "Discharge summaries in 1 day, not 9" | HIPAA + BAA, on-prem only, physician verifies every output | Safety-critical, human-in-the-loop, no cloud API permitted |
| **5** | **Halden & Rowe** | 220-law firm · 8 offices | "First-pass NDA/MSA review for 30 junior lawyers" | Attorney-client privilege, per-matter isolation walls | A global-index plan gets caught and redesigned before it leaks |

#### Task Checklist

- [ ] Lead technical discovery sessions with customer ML, data, and platform engineering teams
- [ ] Understand the customer's business problem, workflow, and success criteria in domain terms
- [ ] Define measurable success metrics: accuracy, latency (TTFT, tokens/sec), cost-per-request, hallucination rate, user adoption, task completion rate
- [ ] Assess data readiness: corpus quality, volume, freshness, PII/sensitive content, labeling status, access patterns
- [ ] Evaluate RAG vs. fine-tuning vs. prompting vs. agentic approaches — recommend the right technique for the problem
- [ ] Design the target architecture: cloud vs. edge vs. hybrid; open vs. closed models; single vs. multi-model routing
- [ ] Select vector database and embedding strategy based on scale, latency, and query patterns (Pinecone, Weaviate, Qdrant, Milvus, pgvector)
- [ ] Assess security and compliance requirements: data residency, SOC2, HIPAA, GDPR, EU AI Act, customer-specific policies
- [ ] Identify integration points: customer APIs, databases, enterprise systems (Salesforce, ServiceNow, SAP, Confluence, SharePoint), identity providers (Okta, Azure AD)
- [ ] Map stakeholders: executive sponsor, technical POC, end-users, IT/security, legal/compliance
- [ ] Define the phased delivery plan: POC → pilot → production, with clear exit criteria per phase
- [ ] Identify risks: data quality, model capability gaps, integration complexity, timeline, hardware lead times
- [ ] Scope the engagement: workstreams, deliverables, dependencies, customer effort required
- [ ] Participate in pre-sales technical validation: demos, reference architectures, POC proposals
- [ ] Review and validate SOW statements against actual technical requirements
- [ ] Prepare architecture diagrams and technical design documents for customer review
- [ ] Align internal teams (Product, Research, Solutions Architecture) on the deployment plan
- [ ] Conduct data exploratory analysis: sample documents, measure embedding quality, test baseline retrieval

---

#### A1. Lead technical discovery sessions with customer ML, data, and platform engineering teams

**Goal.** Understand how the work actually gets done today, and what constrains it, before you design anything.

**How to do it.**

1. **Run at least three separate sessions, not one.** Operators (people who do the work), data/platform engineers (people who own the systems), and the decision-maker (who signs and pays). Mixing them produces polite, high-level answers from everyone.
2. **Use a fixed agenda and send it in advance.** 60 min operators: walk one real case end to end. 60 min data/platform: source systems, volume, access, freshness, existing infra. 30 min sponsor: business outcome, budget, decision rights, deadline. Agenda discipline is what keeps 90 minutes from becoming 45 minutes of discovery and 45 minutes of your architecture.
3. **In the operator session, do not present.** Ask them to screen-share one live case and narrate the decisions they make. You are recording *decisions*, not steps — "I check the loss history first because the first reported date is often wrong" is the requirement; "I open the system" is not.
4. **Capture the baseline by observation, not by asking.** Time them on three cases. Ask for last quarter's numbers rather than their estimate of them. Estimates are optimistic by 2–3x and nobody is lying.
5. **Read your notes back at the end of each session.** "You said approvals above $5k need a second adjuster — did I get that right?" Corrections are cheap now and expensive in Phase 4.
6. **Send a one-page summary after each session** with decisions captured, open questions, and owners. The summary is the artifact; the meeting is just how you produce it.

**Artifacts.** Discovery notes template (session | date | attendees | decisions observed | constraints | baseline numbers | open questions | owner); stakeholder/attendee list; baseline measurement sheet.

**Worked example — Corvid.** Four sessions with a 340-agent support org. The operator session produced the most valuable finding of the entire engagement: watching twelve tickets get handled showed that 31% of "tier-1" tickets were pure status checks, and that agents were answering them from **Zendesk macros**, not from the help center. That single fact reshaped the architecture (macros became a retrieval source) and the plan (the deflection target was redefined from "40% of tickets" to "40% of *answerable* tickets"). The sponsor session added another constraint nobody had written down: the 4-second latency ceiling came from a CSAT survey answer, not from an SLA document — and it was therefore not negotiable.

**Watch out for.** Sponsor-only discovery. Presenting your architecture and getting agreement — that is a pitch, not a session. Accepting "we want a chatbot" as the problem statement. Letting the loudest engineer define the job. Missing the sessions with the people who will absorb the system's mistakes.

**Done when.** You can state the problem in the operators' own words, back it with three baseline numbers, and have every attendee signed off on the notes.

---

#### A2. Understand the customer's business problem, workflow, and success criteria in domain terms

**Goal.** Restate the request as a job with an owner, a unit of work, and a measurable outcome — in the customer's vocabulary, not yours.

**How to do it.**

1. **Write the job in one sentence:** "When *X* happens, the system does *Y* so that *Z* changes." If you cannot finish the sentence, the problem is not understood yet.
2. **Map the workflow as a swimlane**: trigger → human steps → system steps → decision points → approval gates → exception path. The exception path is the part that is always missing and always the part that matters. "What happens when the answer isn't in the knowledge base?" is a question the workflow must answer.
3. **Define the unit of work** — one ticket, one claim, one file, one shift. Everything downstream (metrics, load, cost) is a multiple of this.
4. **Identify who absorbs errors today.** The human who currently catches a bad answer sets the real tolerance for imperfection. Ask the operators directly: "if this got the answer wrong, who would catch it, and how often?" If the answer is "nobody," you have a safety problem, not a quality problem.
5. **Ban AI vocabulary when talking to operators.** No "embedding," "vector," "agent," "RAG." Say "it looks up the policy," "it drafts the response," "it can take actions." The restatement should be writable in their words without a glossary.
6. **Test the restatement by having two people from different roles repeat it back.** If a support lead and a security engineer come back with materially different versions, your restatement is still too abstract.

**Artifacts.** Problem statement canvas (trigger | user | job | success measure | unacceptable outcome | constraints); workflow swimlane; domain glossary; error-absorption map.

**Worked example — Corvid.** The ask was "a support chatbot." The job, restated: *"When a customer opens a ticket, give them the correct answer — status, how-to, or policy — in one message, so they don't need a human, and so our agents spend their time on real problems rather than status lookups."* Baseline captured during discovery: 900 tickets/day, 41% were status or plan questions, 68% of those were answerable from existing help-center content, median first response 47 minutes, CSAT 4.3/5. The "unacceptable outcome" the team agreed on was not an incorrect answer — it was an incorrect answer that a customer acted on (a refund promise they never received). That one sentence changed what Phase 4 had to test.

**Worked example — Northwind Mutual.** The CFO's ask was "cut claims handling cost 30%." The job was different: *"When a claim is opened, give the adjuster a first-pass triage — right desk, right priority, and a summary of the facts — so they spend their time on judgement, not on reading."* Baseline: 400 adjusters, 40k claims/year, 38 minutes of touch time per claim, of which roughly 55% was writing narrative notes from free text. The 30% cost target was unreachable through triage alone; it was reachable through note summarisation plus better routing, which is why the scope had to be widened deliberately rather than discovered in Phase 3.

**Watch out for.** Restating the problem in the sponsor's vocabulary. Dropping the exception path. Treating "chatbot" as the job. Ignoring who catches errors — and therefore under-specifying the guardrail requirements. Building a solution to a problem nobody has.

**Done when.** Two people from different roles can repeat the job statement back to you, and the swimlane includes the failure path.

---

#### A3. Define measurable success metrics

**Goal.** Turn "it should be good" into thresholds a machine can check and a business can argue about.

**How to do it.**

1. **Pick one primary metric that moves the business** (deflection rate, handling time, review throughput), plus three to four **guardrail metrics that must not degrade** (accuracy, hallucination rate, CSAT, escalation correctness). If a metric can rise while the business gets worse, it is a guardrail, not a success metric.
2. **Write each metric as a full definition:** metric | exact numerator and denominator | measurement method | threshold | evaluation point | owner. "Accuracy" with no denominator is not a metric; it is a topic.
3. **Derive thresholds from the baseline, not from ambition.** If human first-pass routing accuracy is 94%, a threshold of 92% means "no worse than a trained human"; 98% means you have no evidence you can reach it.
4. **Add the cost metric, defined per successful task** — not per request. A cheap request that answers wrong and gets retried or escalated is not cheap.
5. **Specify the measurement instrument now.** Who labels correctness, with what rubric, on how many samples, how often. If you cannot describe how you will measure it, you cannot gate on it later.
6. **Write abort criteria** — the conditions under which the project stops rather than continues. This is the most protective paragraph you will write in Phase 0.
7. **Get it signed by the person who owns the outcome,** not by the FDE and not only by the technical owner.

**Artifacts.** Metrics sheet (metric | definition | method | threshold | evaluation point | owner); threshold derivation note; instrumentation requirements list; abort criteria.

**Worked example — Corvid.** "Deflect 40% of tier-1 without raising CSAT" became: deflection = *ticket closed with no human touch AND no reopen within 7 days*, measured on a weekly production report. The reopen clause was the whole point — without it, a deflected-but-wrong ticket inflates the number, and the team would have celebrated a regression. Supporting metrics: answer accuracy ≥ 88% on the agreed 300-case gold set; CSAT no worse than 4.3 ± 0.1; p95 latency ≤ 4s; cost ≤ $0.04 per request and ≤ $0.07 per successful resolution; hallucination rate ≤ 2% with 100% of answers citing approved KB content. Abort criterion: if deflection < 20% at pilot week 3 with accuracy ≥ 88%, the KB repair dependency is not being met and we re-scope rather than continue.

**See also — Klarna (2024 launch, revised 2025).** Klarna reported a large share of customer-service chats handled by an AI assistant and ~$40M in projected savings, then later reported quality concerns and a return to human hiring. The lesson for Phase 0: when deflection is the headline metric, define it with a quality term *before* the pilot, because a deflection metric alone will drive the system toward confident wrongness.

**Watch out for.** Undefined denominators. A single composite score that hides which dimension regressed. Success metrics with no measurement method. No abort criteria. Measuring adoption while the thing is still a demo.

**Done when.** Every threshold has a definition, method, owner, and evaluation point, and at least one metric can already be computed from data the customer has today.

---

#### A4. Assess data readiness

**Goal.** Know whether the data can actually support the answers — before an architecture is committed to it.

**How to do it.**

1. **Build a source register:** source | system of record | format | volume | update cadence | owner | sensitivity | access method | licensing. If a source has no named owner, it is not a source; it is a hope.
2. **Classify every source by what it can support:** knowledge (corpus for retrieval), exemplars (behaviour for fine-tuning or few-shot), features (structured filters), ground truth (labels for evaluation). Most engagements need all four and only have the first.
3. **Audit a stratified sample** — 100 to 500 documents per source, spanning formats, ages, and owners: extractable, complete, duplicate, structured, current, contradictory?
4. **Measure freshness** and compare the age distribution against the freshness the use case actually requires (pricing docs daily, HR policy monthly, archive never).
5. **Run the answerability test:** take 50 real questions and ask an SME which source would answer each one. Every question with no source is a *capability gap*, not a data gap — and it must be recorded as such, because it will never be fixed by a better index.
6. **Establish the retrieval ceiling** with a baseline hit@k on a labelled subset, so the Phase 4 targets are set against reality rather than optimism.
7. **Write the readiness report:** dimension | green/amber/red | evidence | remediation | owner | effort.

**Artifacts.** Source register; sampling audit sheet; answerability matrix (question type | source | answerable? | gap type); data readiness report.

**Worked example — Meridian Bank.** The register revealed that only 11,200 of the 60,000 KYC files had machine-readable structured fields; the remaining 48,800 were scans from 2015–2019 with no text layer, requiring an OCR tier and a decision to exclude pre-2019 files from the "current policy" index entirely. More importantly, the register found that Suspicious Activity Report narratives lived in a mail archive, **not** in the KYC system — a source nobody had listed, and the one containing the firm's own precedent language. That single missed source would have made the whole project 30% less capable. A separate finding: 11% of customer files were in Spanish or Portuguese following a 2021 acquisition, which forced a multilingual embedding check and a translation-review policy — both discovered in Phase 1 rather than in Phase 4.

**Watch out for.** Trusting a curated export over the system of record. Not separating exemplars from knowledge. Skipping the OCR check on scanned PDFs. Treating "we have the data" as "the data is usable." Missing the labels you will need for evaluation — they rarely live where you assume.

**Done when.** For the top 20 question types you can name the source that answers them, and you have hit@k numbers for the chosen configuration.

---

#### A5. Evaluate RAG vs. fine-tuning vs. prompting vs. agentic approaches

**Goal.** Choose one technique and be able to defend it against the two you rejected, with evidence rather than taste.

**How to do it.**

1. **Classify the gap before choosing the tool.** Where does the missing capability live?
   - *In the weights* → better model, more context, reasoning.
   - *In a corpus you can retrieve* → RAG.
   - *In behaviour or examples* → fine-tuning, or few-shot prompting if the volume is small.
   - *In actions* → tools, which means agentic.
   - *Nowhere* → capability gap. No technique fixes it; narrow the task or change the success criteria.
2. **Apply the decision table:** prompting-only for general knowledge and reasoning; RAG for live, private, citable knowledge; fine-tuning for behaviour and format with labelled examples; agentic for multi-step work requiring real actions.
3. **Price the operational burden, not just the build effort.** Iteration cost runs prompting < RAG < fine-tuning ≈ agentic. Operational and review burden runs roughly the other way: RAG is easy to change, a fine-tuned model is a versioned artifact you must re-evaluate, and an agentic system is a distributed system with a failure surface you have to staff.
4. **Test the two finalists on the same 50 labelled cases**, same corpus, same judge. Compare quality, latency, and cost together — a technique that wins on quality by 4 points and loses on latency by 900ms is a different decision than a clean win.
5. **Probe the capability gap explicitly.** Run the hardest ten cases against the strongest available model. If it also fails, you have a capability or workflow problem, and no retrieval strategy will save it.
6. **Write the ADR with a revisit trigger:** "if accuracy stays below X after N iterations, we switch to Y." A revisit trigger written in Phase 0 is what prevents the sunk-cost argument in Phase 6.

**Artifacts.** Decision table; candidate side-by-side scorecard; ADR template (context | options | choice | rationale | cost | revisit trigger); capability-gap log.

**Worked example — Northwind Mutual.** The gap was *knowledge* (a 900-page claims manual plus 2.1M closed claim files), so RAG was the leading candidate. Prompting-only was rejected because policy and coverage language require exact citations and a wrong coverage interpretation carries regulatory exposure. Fine-tuning was rejected because the manual changes monthly and every manual change would require a retrain and a full re-evaluation. Agentic was rejected because claim routing is regulated and irreversible — so the design routes to a *desk* with a confidence threshold and escalates below it, rather than letting an agent decide. The decisive evidence was a 50-case bake-off: RAG at 91% routing accuracy, prompt-only at 58%, and the frontier-model-with-no-retrieval probe at 41% — proving the corpus was doing the work and the model was not the bottleneck.

**See also — JPMorgan COiN (2017) and BloombergGPT (2023).** COiN applied NLP to bounded commercial-document review and the firm reported roughly 360,000 hours of lawyer time saved annually — the pattern is that a *bounded* classification task is where this technology has historically paid off, not open-ended chat. Bloomberg built a model on its own proprietary financial corpus and deployed it inside the Terminal: the case for fine-tuning or domain pretraining on proprietary data when residency and corpus justify the cost.

**Watch out for.** Choosing from benchmark reputation rather than corpus shape. Evaluating retrieval and generation only jointly, so you cannot tell which one is failing. Assuming a bigger model fixes retrieval. Reaching for agentic by default. Forgetting that fine-tuning turns your model into a versioned artifact that now needs pinning, rollback, and re-evaluation on every change.

**Done when.** One technique is chosen, two are documented as rejected with reasons, and the choice was made from a measurement on the same labelled set rather than from a preference.

---

#### A6. Design the target architecture

**Goal.** Produce a topology that satisfies the constraints discovered in Phases 0–1 and can actually be built in Phase 2.

**How to do it.**

1. **Apply the constraints before the preferences.** Data residency, latency floor, connectivity, ops capability, and budget ceiling eliminate options before anyone gets to argue for one. Write these down first — they are the design's real inputs.
2. **Choose the topology:** cloud, edge, or hybrid. Rule of thumb: if data cannot leave the premises, topology is decided. If a site needs sub-200ms inference with unreliable connectivity, edge is decided. Hybrid means two failure domains and two sets of operational skills.
3. **Choose model sourcing with four tests:** may the data legally leave? does the task need frontier capability? does the customer have the ops capability to run a model? what is the break-even volume at your measured token rate? The last question is the one that most often reverses the answer to the third.
4. **Design for three models from day one:** primary, fallback, and a router that sends simple tasks to a small model. Retrofitting routing and fallback after a cost surprise is expensive, because it touches every call site.
5. **Draw the data flow with trust boundaries** and label each boundary with the authentication mechanism that enforces it. A boundary with no mechanism is an assumption.
6. **Decide what is stateful:** session state, index, caches, audit log, user feedback. Where each lives, how it is backed up, and how it is restored.
7. **Write an ADR for each decision:** topology, model sourcing, vector store, state store, egress policy.

**Artifacts.** Constraint-to-decision matrix; context and container diagrams; ADR set (topology, model sourcing, vector store, state store, egress).

**Worked example — Halden & Rowe.** The original plan was a single global contract index plus an agentic review workflow. It failed on two counts. First, per-matter privilege walls require index isolation: one global index plus semantic retrieval means one matter's privileged text can surface inside another matter's review, and the firm cannot evidence that it did not. Second, agentic write actions could file or circulate documents without partner approval, which conflicts with the firm's professional duties. The revised design: one index namespace per matter, created at matter open and destroyed at retention expiry, with a CI test that *attempts* a cross-namespace query and must fail. The second decision was model sourcing — no external API, because of the confidentiality duty, so the deployment must be self-hosted, which multiplies the Phase 2 effort and makes the customer's infrastructure team a hard prerequisite. That consequence is an engagement-scoping fact discovered in Phase 0, not a surprise in Phase 2.

**Watch out for.** Choosing the topology by preference or by what your team knows. Deferring the fallback and router decision. Treating "we'll use an API" as final before legal has answered. Drawing diagrams without trust boundaries. Choosing hybrid without acknowledging that you now need two operational runbooks.

**Done when.** Every constraint from Phase 0 maps to a specific decision, and every one of those decisions has an ADR.

---

#### A7. Select vector database and embedding strategy

**Goal.** Pick the storage and the embedding model so that retrieval quality, latency, and cost all fit — and so you can rebuild the index when either changes.

**How to do it.**

1. **Start from retrieval requirements, not from a vendor shortlist.** Corpus size and growth, retrieval latency budget, the filter story (tenant, date, document type, ACL), residency, ops capability, and whether you will run hybrid search.
2. **Test the filter story first.** Post-filtering after approximate nearest-neighbour retrieval silently destroys recall on filtered queries. If you need "only documents from 2024, for tenant X, of type Y", the store must support it natively — and you should verify this with a query, not a documentation page.
3. **Choose the embedding model on your labelled set, not on a leaderboard.** Shortlist by quality on *your* questions, dimension, cost, licence terms, self-host availability, language coverage, and residency. A general embedding model frequently beats a "domain-specific" one on a narrow corporate corpus.
4. **Do the storage arithmetic.** Vectors ≈ chunks × dimension × 4 bytes, plus index overhead of roughly 0.5–1.0x. Then project it forward five years at your growth rate. This number surprises people and it changes vendor choices.
5. **Plan for hybrid now if the corpus contains rare or identifier-like terms** — part numbers, error codes, statute references, case IDs, surnames. Dense retrieval is weak exactly there, and BM25 is strong. Add a reranker on top; reranking is usually the single biggest quality-per-effort win available.
6. **Write down the versioning contract.** Every chunk records its embedding model *and version*; the index records its build. The serving layer must refuse to answer a query when the embedding version does not match the index. Silent embedding/index mismatch is one of the most expensive failures in AI systems, and it is entirely preventable.
7. **Decide the rebuild budget.** Full rebuild time × rebuild frequency = operational cost. If a full rebuild takes nine hours, you cannot re-embed weekly, and that constrains how you experiment in Phase 3.

**Artifacts.** Vector DB decision matrix (requirement | weighted options | chosen | why); embedding benchmark sheet (model | dim | hit@k | $/M tokens | licence | self-host | notes); capacity and cost model; embedding/index versioning contract.

**Worked example — Meridian Bank.** Two very different collections, so two very different answers. The policy corpus (400 pages, ~2,900 chunks) went to a managed serverless store — fast to stand up, negligible ops, and the sensitivity was low. The KYC file corpus (60k files, ~4.2M chunks after chunking) went self-hosted for residency reasons, with the hardware sized from the arithmetic: 4.2M × 1024 dim × 4 bytes ≈ 17 GB of vectors, plus roughly 1.0x index overhead. The embedding bake-off ran three candidates against 200 labelled KYC questions and produced a surprise: a mid-size general-purpose embedding model won at hit@5 0.78 while a domain-specific model with better public benchmark scores reached only 0.74 — the firm's document style defeated the benchmark. Hybrid was mandatory because SAR narrative numbers and case IDs are recall-critical, and BM25 handles them better than dense vectors.

**Watch out for.** Picking the vector database by familiarity. Not checking filter support until after indexing millions of chunks. Comparing embeddings on public benchmarks only. Not versioning, which leaves you unable to explain a silent quality drop after a model refresh. Assuming your chosen embedding model will be available at the same price and licence terms in twelve months.

**Done when.** The decision matrix shows requirements → chosen option → reason, and you have hit@k numbers for the chosen embedding on your own labelled questions.

---

#### A8. Assess security and compliance requirements

**Goal.** Surface the constraints that will reshape the architecture — while changing the architecture is still cheap.

**How to do it.**

1. **Enumerate everything that crosses a boundary:** user prompts, retrieved context, model outputs, logs, traces, embeddings, evaluation datasets, telemetry, and fine-tuning data. Review each one for residency, retention, deletion, and who can see it in the UI.
2. **Map the regulatory overlay.** SOC 2 (access control, logging, change management), HIPAA plus a BAA, GDPR and UK GDPR (lawful basis, erasure, DPIA), EU AI Act (risk classification and its documentation obligations), GLBA and PCI for financial data, and sector rules such as state insurance regulators or ABA Model Rules 1.6 on confidentiality and 5.1/5.3 on competence and supervision. Note which ones are legally required and which are the customer's policy — they are different obstacles with different escape hatches.
3. **Identify who accepts residual risk, in writing.** For AI deployments it is almost always the customer, and the acceptance has to be documented — an email from the business sponsor is worth more than silence.
4. **Translate every requirement into an architectural constraint:** where compute runs, whether egress is permitted, what may be logged, key management, identity, tenant isolation, and the required audit trail.
5. **Name the AI-specific risks explicitly** and pair each with a control: hallucination with legal or financial consequence, prompt injection through retrieved content, PII leakage into logs, bias in a decision that affects a person, model and data licence provenance, and provider training on your inputs.
6. **Write the questions for the customer's legal and privacy teams** and get the answers in writing before the build. A clean, numbered list gets a better answer than a vague request to "review our approach".
7. **Record the model and data licence inventory.** Model weights licence, embedding model terms, and the contractual right to use the corpus for embeddings and fine-tuning are all things you inherit from the customer and can breach without noticing.

**Artifacts.** Data-flow-and-classification table; compliance matrix (requirement | applies? | control | evidence | owner); architecture constraint list; DPIA input; model/data licence inventory.

**Worked example — St. Aldwyn Health.** Discharge-summary generation touches PHI, which triggered HIPAA and a BAA requirement — and the health system had a standing policy that PHI never leaves its network. That single policy decided the topology in Phase 0: on-premise inference, no cloud API. The downstream consequence was significant and was surfaced early on purpose: on-premise inference tripled the infrastructure effort and put GPU procurement on the critical path. Discovering that in Phase 0 changed the plan; discovering it in Phase 2 would have changed the schedule. The clinical safety constraint was equally binding: the system may *summarise what the clinician documented* but may never generate a medication recommendation or an interpretation. That one sentence became a guardrail rule, a golden-set rule, and an eval metric — a hallucinated-medication rate that must be exactly zero, not "low".

**See also — Air Canada (February 2024).** The airline's site chatbot gave a customer incorrect bereavement-fare advice, and the Canadian Civil Resolution Tribunal held the airline responsible for what its chatbot had said. The operational lesson is that a chatbot's output is an attributable statement made by the business, so the design must constrain it to answer only from approved policy content, cite it, and escalate compensation and policy-exception questions to a human.

**Watch out for.** Treating compliance as legal's problem rather than as an input to your architecture. Leaving "no PHI leaves the network" implicit until the infrastructure is designed. Not asking whether the provider trains on your inputs. Assuming a BAA exists because the vendor is a healthcare company. Forgetting retention on traces, logs and eval datasets — a frequent and quiet leak, and often the one an examiner finds.

**Done when.** Every data flow has a residency, retention, and access answer, and each applicable regulatory requirement maps to a control you will implement and be able to evidence.

---

#### A9. Identify integration points

**Goal.** Know every system the application must read or write, how identity and authorization flow through it, and what you will do when each one fails.

**How to do it.**

1. **Separate reads from writes.** Reads are reversible; writes are not. Every write path deserves ten times the scrutiny of a read path, and anything that moves money, sends messages, changes records, or files documents should have a named human approval step.
2. **For each system, capture the contract:** system | auth method | data shape | volume | latency | availability | API owner | rate limits | error semantics | cost per call. "Error semantics" is the field people skip and the one that breaks agentic workflows: does the API return an error object, or a 200 with an error body, or a timeout that looks like success?
3. **Map identity end to end.** How does the user's identity reach the downstream system, and does the downstream enforce access — or are you relying on your own filter? The hardest part is almost always authorization, not authentication.
4. **Choose the integration pattern per system:** API (default), MCP server where the customer already runs one, event stream for live state, batch extract for read-heavy corpora, file drop for legacy systems you cannot integrate with.
5. **Write a contract test per integration before you build against it:** timeout, malformed response, 5xx, partial failure, rate-limit response, and schema drift. These tests are cheap in Phase 0 and they define the agentic failure behaviour later.
6. **Budget the downstream's capacity.** An agentic loop can call a customer API hundreds of times per user action. Confirm the rate limits, and design backpressure — otherwise your system becomes the thing that takes down their service.
7. **Name what you will not integrate with yet.** Scope discipline here is what keeps a Phase 0 estimate from becoming a Phase 3 rewrite.

**Artifacts.** Integration surface sheet (per-system table); identity and authorization flow diagram; contract test list; sequence diagram for the two or three critical paths.

**Worked example — Northwind Mutual.** Reads: the claims system via nightly extract into object storage, plus an on-demand API call for the claim currently being worked. Writes: exactly one — a "suggested desk" flag carrying a status of *unreviewed*, so no routing ever happens without a human. Identity: OIDC through the customer's SSO, with claims carrying adjuster ID and branch so that search and downstream calls are scoped per user. Deliberately excluded: any write to reserves or payments, and the financial ledger — irreversible, regulated, and outside the ask. Auto-approval of claims under $500 was explicitly out of scope for the same reason, and saying that out loud in Phase 0 prevented it being sold to the CFO as a Phase 2 item.

**Worked example — Corvid.** Reads: Zendesk for tickets, Confluence for help-centre content, and the Zendesk macros that discovery revealed were the real source of agent answers; Salesforce for account and plan context so answers reflect the customer's actual tier. Writes: a macro-based reply and a ticket status change — both reversible, both human-reviewed during the pilot. Slack was deliberately not integrated: the tool was for internal conversation, and every integration is a surface you must monitor forever.

**Watch out for.** Omitting write paths from the security review because they felt like plumbing. Assuming the customer API can absorb your volume. Building on undocumented endpoints. Planning for a schema change that the customer did not know was coming. Underestimating identity — and forgetting that agentic systems act with the *system's* privileges, not the user's.

**Done when.** Every integration has a named owner, an auth method, a contract test, and a rate-limit budget — and every write path has a named human approval step.

---

#### A10. Map stakeholders

**Goal.** Know who decides, who can block, who operates, who adopts, and who must merely be satisfied — before any of them surprise you in week six.

**How to do it.**

1. **List the five roles every AI engagement has:** economic buyer, technical decision maker, domain owner (the SMEs), operational owner (who inherits the on-call), and end user. Then add the veto roles — security, legal, privacy, records management, and regulatory — who can end the project late and cheaply.
2. **For each person, write:** what they care about | what they control | what they fear | what a win looks like for them | engagement cadence | what you need from them (time, data, access, budget).
3. **Start the veto conversations first.** A security review begun in Phase 0 is a scheduling problem; the same review begun in Phase 4 is a schedule failure.
4. **Map influence against interest** and plan engagement accordingly. High influence and low interest is the group that kills projects by not answering an email.
5. **Define the reporting artifact per audience:** executives get business metrics and risk; technical reviewers get eval results and architecture; operations get runbooks and alert definitions; end users get a plain statement of what the system does and does not do.
6. **Set a decision cadence and a change-control path** — who may approve a scope change, how it is priced, and what it does to dates. Write this into the plan; do not leave it to a late renegotiation.

**Artifacts.** Stakeholder map (stakeholder | role | cares about | controls | fears | win | cadence | ask from them); engagement plan per audience; veto list; escalation path.

**Worked example — Corvid.** The economic buyer was the VP Support, whose stated goal was deflection and headcount avoidance — a combination that quietly guarantees the project is not supported, because the people asked to adopt it are the ones whose roles it endangers. Reframing the goal as *redeploying agents to retention and escalations* was the difference between a funded programme and a stalled one. The technical decision maker was the staff engineer who owned the Zendesk customisation and would review every pull request — which made them the person who could kill the project technically. The SMEs were six team leads who would author goldens and repair help-centre content. The vetoes were security (SOC 2 scope, EU residency) and legal (pricing policy in the help centre had to be legally accurate). The operator session surfaced one more stakeholder nobody had listed: a strong internal resistance to "AI answering customers," which was a labour-relations issue, not a technology issue. Engagement plan: monthly business review, weekly technical sync, twice-weekly SME office hours, security review scheduled before Phase 2 begins.

**Watch out for.** Assuming the person who asks is the person who decides. Missing the person who inherits the on-call. Reading one team's objection as the organisation's position. Not making the operational owner an early partner — they are the ones paged at 2am. Leaving SME calendar time unreserved until the golden set is needed and therefore late.

**Done when.** Every stakeholder has an owner, a cadence, and a named ask, and the veto list has already been engaged once.

---

#### A11. Define the phased delivery plan: POC → pilot → production

**Goal.** Produce a plan where each stage has a scope, an owner, and a written exit criterion — so that "we're ready for production" is a measurement rather than an opinion.

**How to do it.**

1. **Define the three stages by their question, not their feature list.** POC asks *can it work at all?* Pilot asks *does it work well enough?* Production asks *does it meet its SLOs reliably?* Write the question at the top of each stage.
2. **Write exit criteria as numbers tied to your Phase 0 metrics.** "Quality is acceptable" is not a gate. "≥85% routing accuracy on the 150-case gold set and p95 ≤ 6s" is a gate.
3. **Scope the POC ruthlessly.** One workflow, one user group, curated data, no write actions, no SLAs, no customer-facing exposure. Every week you add to a POC is a week of production you have not built — and production is where the schedule risk actually lives.
4. **Build the pilot to be representative:** real users, the real corpus, the real integrations — but with humans in the loop for every consequence.
5. **Define the production gate in advance:** eval thresholds met, load test passed, red-team findings closed, monitoring live, rollback drilled, runbooks written, team trained.
6. **Identify the long-lead items now** — GPU procurement, security review, legal review, SME availability, procurement paperwork. These set the critical path, and they are the items that arrive late.
7. **Re-baseline after the POC, in writing.** AI estimation error is large and structural, not random. A re-estimate that is not documented becomes a quiet scope negotiation in month three.

**Artifacts.** Phase plan (stage | scope | users | deliverables | exit criteria | dates | owner | dependencies); long-lead items tracker; re-baseline note.

**Worked example — Corvid.** POC (2 weeks): 300 tickets per week sampled from the prior quarter, a curated 400-article help-centre subset, read-only, five internal users, no customer traffic. Exit criteria: ≥80% correct on 150 tickets, p95 ≤ 6s, ≤$0.05 per request. Pilot (4 weeks): all inbound email tickets, 20% of traffic, every AI reply human-reviewed before sending, which is what made the hallucination rate measurable rather than anecdotal. Exit criteria: ≥35% deflection with no CSAT movement and hallucination rate ≤2%. Production: monitoring live, red-team blockers closed, rollback drilled, support trained, EU region cut over. The long-lead items found in Phase 0 were the actual schedule drivers, and none of them were the model: the SOC 2 scope change for AI components (3 weeks), the EU region build (2 weeks), and the Zendesk custom app review (2 weeks).

**Watch out for.** A POC that is secretly a production build. Exit criteria with no numbers. A pilot with so little traffic it proves nothing. No re-baseline after the POC. Leaving "it must be reliable" unmeasured until launch day.

**Done when.** Every stage has numeric exit criteria, an owner, and a date, and the long-lead items have owners and expected durations.

---

#### A12. Identify risks

**Goal.** Catch the things that kill the project while there is still time to act on them.

**How to do it.**

1. **Sweep six families systematically** so you do not only find the risks you were already thinking about: data quality, model capability, integration complexity, timeline and lead times, security and compliance, cost, and — the one that gets forgotten — organizational adoption.
2. **For each risk write:** description | likelihood | impact | early warning signal | mitigation | contingency | owner | review date.
3. **Make the early warning observable.** A metric or an event, not a feeling. "Data quality risk" has no warning signal; "hit@5 below 0.60 on the 200-case labelled set" does.
4. **Always include three risks people forget:** SME time, review latency from security and legal, and whether end users will change their behaviour to accommodate the system.
5. **Write capability-gap mitigations that are not "try a bigger model".** The realistic options are: decompose the task, add a deterministic tool, narrow the scope, or renegotiate the success criteria. Pick one and name it.
6. **Give every High risk a contingency, not only a mitigation.** A mitigation reduces likelihood; a contingency tells you what you will do when it happens anyway.
7. **Review the register at every phase gate** and carry unresolved High risks forward explicitly, in writing, to the sponsor.

**Artifacts.** Risk register template (risk | family | likelihood | impact | early warning | mitigation | contingency | owner | review date); capability-gap log; top-ten risks one-pager for the executive.

**Worked example — St. Aldwyn Health.** The top risk was hallucinated clinical content inside a discharge summary that a physician might miss under time pressure. Mitigation: sentence-level attribution to source note spans, with the source highlighted in the review UI so verification is fast and obvious. Contingency: disable generation and fall back to an extractive-only templated summary. The second risk was more subtle — physician distrust. If adoption stayed below 30%, the workflow would not change, and no amount of accuracy would produce value. Mitigation: physicians saw and edited a source-linked draft with time-saved measured in the UI, starting with a single service line that had a champion. Contingency: narrow the deployment to that service line. The third risk was operational rather than clinical: internal GPU procurement on a hospital network had an 8–12 week lead time, and it sat on the critical path behind on-premise residency — so it was escalated in Phase 0, not Phase 2.

**See also — Klarna (2024 launch, revised 2025).** Reported large AI-handled chat volumes and ~$40M in projected savings, followed later by quality concerns and renewed human hiring. The Phase 0 lesson: a cost-led optimisation puts quality under structural pressure, so watch quality metrics *during* the pilot, not only after launch.

**Watch out for.** Risks with no owner. Mitigation lists that are really hopes. "We will fix the data later." Leaving the organizational and adoption risk out of a register that is otherwise purely technical. Registers that die after the kickoff meeting. Risks with no early warning signal — because you cannot act on what you cannot see.

**Done when.** Every High risk has an observable early warning signal, a named owner, and both a mitigation and a contingency, and the register was reviewed at the last gate.

---

#### A13. Scope the engagement

**Goal.** Agree explicitly on what you will deliver, what the customer must do, and what the engagement costs both sides.

**How to do it.**

1. **Break the work into workstreams and deliverables** with estimates in days per deliverable — not per task. A deliverable is something the customer can use ("a working retrieval index with a refresh job") rather than an activity ("configure chunking").
2. **Itemise the customer's effort.** This is the most commonly missing line in an AI SOW: SME hours for golden sets and content repair, data owner hours for corpus fixes, platform team days for infrastructure, security review days, legal review days, and access provisioning time. AI engagements fail on customer-side capacity far more often than on vendor-side skill.
3. **Write the assumptions explicitly:** corpus X available by date Y, SME Z available for N hours per week, inference environment provisioned by W, security review completed by V. Every assumption that breaks is a schedule event, so name them now.
4. **List what is out of scope.** This is the most valuable paragraph in the document, because it is what everyone actually works from. Put it in the SOW, not in a meeting.
5. **Choose a commercial structure that matches the uncertainty.** Fixed-scope is a poor fit for a project whose core content is quality tuning. A phased structure — a separately priced POC with a conversion option into production — lets the customer exit after the POC without either side pretending the first stage was the whole thing.
6. **Attach acceptance criteria to every deliverable,** tying back to the Phase 0 metrics.
7. **Plan the end state:** what the customer is left holding — runbooks, the evaluation harness, model access, keys, and a trained team.

**Artifacts.** Workstream and deliverable table; customer effort table; assumptions log; out-of-scope list; acceptance criteria; commercial options memo.

**Worked example — Corvid.** The original SOW read "deploy an AI chatbot" — one deliverable, no effort table, no out-of-scope list. The rewritten version had nine deliverables across four workstreams (discovery and design, data and KB preparation, application build, evaluation and go-live) and an itemised customer effort table: six team leads at 4 hours per week for goldens and KB repair across 24 weeks (~96 hours), the platform team at 15 days for the EU region and the Zendesk app, security at 10 days, and support operations at 8 hours for training and briefing. Out of scope: autonomous ticket closure, refunds and order changes, non-English languages, voice, mobile, and any write to billing. Commercially: the POC priced separately with an explicit conversion option into the pilot.

**Watch out for.** SOWs written as product features instead of deliverables with acceptance criteria. No customer effort table. No out-of-scope list. Assuming the customer has unlimited SME time. Pricing a fixed scope for a project whose scope is inherently uncertain.

**Done when.** Every deliverable has an estimate, an acceptance criterion, and a named owner on each side, and the customer effort is quantified in the document.

---

#### A14. Participate in pre-sales technical validation

**Goal.** Prove feasibility honestly, collect the disqualifying information you still need, and avoid making a promise you cannot keep.

**How to do it.**

1. **Reproduce the customer's actual problem on their actual data sample** — not a canned demo on a clean, curated corpus. A demo on a curated corpus is a promise you will be held to on a messy one.
2. **Include a known-failure case in the demo and show how the system handles it.** Buyers trust a system that says "I don't know" and escalates more than one that never visibly fails.
3. **Show the guardrails and the escalation path,** not only the happy path. This is also what shortens procurement, because security teams ask about it anyway.
4. **Quote only numbers you measured and can reproduce:** retrieval hit rate, accuracy on N cases, latency, cost per request. Be ready to rerun the demo script on demand.
5. **Reconcile every claim the demo makes against the Phase 0 success criteria,** and drop the ones you cannot support. A demo that implies a capability the criteria do not contain is a future change request.
6. **Use the demo to harvest the disqualifying information** — real data volume, compliance constraints, ops capability, actual deadline — rather than just to impress. Ask for a sample of their messiest data and let the demo expose the problem early.
7. **Capture the objections.** The questions buyers ask in the demo are the best market intelligence you will get, and they belong in the positioning brief.

**Artifacts.** Demo script and environment (reproducible and seeded); demo-to-criteria reconciliation; objection log; pre-sales technical validation checklist.

**Worked example — Halden & Rowe.** The partner-facing demo showed a clause-flagging assistant on one synthetic MSA and one real, redacted NDA (with permission). The design deliberately included three states in the demo: the clause flagged with its source sentence highlighted; the below-threshold state, where the assistant asks instead of asserting; and the partner approval step. The conversation that mattered was a partner asking whether the assistant could be pointed at the whole deal room for a live matter. The answer had to be no — each matter gets its own index — and disclosing that in the demo was far better than discovering it as a privilege incident after signature. The demo also set the boundary that the firm would later defend: the assistant flags and cites; it never asserts a conclusion about a matter.

**Watch out for.** Demos that run only on curated data. Hiding the failure states. Quoting a quality number from a run you cannot reproduce. Letting the demo create expectations the Phase 0 criteria do not support. Failing to write down objections.

**Done when.** The demo runs on customer-shaped data, shows a failure state, and every claim it makes maps to a written criterion you are willing to be held to.

---

#### A15. Review and validate SOW statements against actual technical requirements

**Goal.** Find the gap between what was sold and what is technically achievable — before the contract is signed, not after it is a dispute.

**How to do it.**

1. **Classify every line of the SOW** as one of: deliverable, assumption, constraint, or acceptance criterion. Most damaging clauses are stated as vague deliverables and quietly contain assumptions inside them.
2. **Test each for feasibility** against the Phase 0 and Phase 1 findings: can it be built with the data that actually exists? inside the latency budget? under the compliance constraints? with the customer effort the plan assumes? with the model's actual capability?
3. **Flag the four recurring categories of problem:** impossible with current model capability; impossible with the data as it exists; possible but dependent on something you do not control (a single customer engineer, a pending policy decision); possible but scheduled as though reviews take no time.
4. **Rewrite the language so quality criteria are testable.** "The system will understand complex contracts" must become a metric, a method, a threshold, and a review cadence. Anything left as "as mutually agreed" will be renegotiated under pressure at acceptance time.
5. **Attach the measurement method and the judge** to every acceptance criterion — who labels, with what rubric, on how many cases.
6. **Put change control in writing:** who may approve a change, how it is priced, and what happens to the dates. Without it, every quality iteration becomes a free scope negotiation.
7. **Get the customer's technical owner to sign the technical annex,** not only procurement. Procurement will sign a promise about accuracy; the technical owner is the person who has to live with it.

**Artifacts.** SOW feasibility checklist; issue log (clause | problem | risk | proposed rewrite); technical assumptions annex; change-control clause; acceptance criteria annex.

**Worked example — Corvid.** The draft said the AI assistant would "answer customer questions accurately and reduce ticket volume by 40%". Three problems in one sentence. "Accurately" had no threshold and no method. "Reduce ticket volume" counted deflected-but-wrong tickets, which inflated the number and hid the risk. And nothing assigned the case where the knowledge base itself is wrong — a customer-owned defect that would otherwise be attributed to the AI. Proposed rewrite: *deflection = ticket closed with no human touch and no reopen within 7 days, reported weekly; answer accuracy ≥ 88% on the agreed 300-case gold set, evaluated by the judge pipeline calibrated against human labels and reviewed monthly; all answers are grounded exclusively in approved help-centre content and cite it, so an incorrect article produces a traceable, attributable error rather than an unsourced answer.* That last clause converted a liability into a design requirement.

**Watch out for.** Reviewing the SOW only for price and not for achievability. Accepting "mutually agreed benchmark" for quality metrics. Leaving quality criteria to a later change request. Not flagging that customer-side dependencies — KB repair, goldens, infrastructure — are on their critical path. Treating legal review as a formality rather than as a lead-time item with an owner.

**Done when.** Every clause is classified, every feasibility problem has a proposed rewrite, and the customer's technical owner has signed the assumptions annex.

---

#### A16. Prepare architecture diagrams and technical design documents

**Goal.** Produce documents that let a reader who has never met you understand, review, and operate the system without you in the room.

**How to do it.**

1. **Draw four views, not one:** context (users, systems, trust boundaries), container (services, stores, model endpoints), data flow (sequences for the two or three critical paths, with security-relevant steps marked), and deployment (network, regions, zones, failover). One diagram cannot carry all four questions.
2. **Use a notation that renders inside your repo** — Mermaid in Markdown is the safest default — and keep diagram source in version control next to the code so diagrams are reviewable in a pull request.
3. **Write the TDD in the standard order:** purpose and scope → goals and non-goals → architecture overview → component design with interface contracts → data model and flows → model, prompt and retrieval specification → non-functional requirements → failure modes and handling → observability → deployment and operations → test and acceptance → open questions → appendices (ADRs).
4. **Specify the AI-specific parts precisely**, because this is where most design documents are vague: exact retrieval configuration, the prompt version and where it lives, tool schemas, guardrail rules, the evaluation set definition, and the behaviour below a confidence threshold.
5. **Include a failure-mode table:** what can fail | how it is detected | system behaviour | user-facing result | operator action. This table is what turns a design into something an operations team can actually run.
6. **Lead with a one-page summary** (diagrams, key decisions, NFRs) and push detail into linked sections and ADRs, so both an executive and an engineer can use the same document.
7. **Get it reviewed by two readers:** the customer's technical owner, and someone who will operate it but did not build it. The second review is what catches the missing runbook steps.

**Artifacts.** Mermaid diagram set (context, container, data flow, deployment); TDD outline; NFR table (requirement | target | how measured | where enforced); failure-mode table; ADR index; diagram source.

**Worked example — Halden & Rowe.** The TDD's most important section was the per-matter isolation model: for each matter, a separate index namespace, a separate encryption context, and a separate audit stream, with two hard rules — no retrieval query may span namespaces, and a CI test that *attempts* a cross-namespace query and must fail. The failure-mode table included the cases that matter in practice: retrieval returns nothing above threshold, so the system responds "insufficient basis for this clause — escalate to partner" rather than emitting a flag; a document-store tool call fails, so the agent stops rather than guessing; the firm's clause library is updated mid-matter, so the library version is snapshotted at matter open and pinned for the life of that matter. None of these are exotic; all of them are the difference between a usable system and a liability.

**Watch out for.** Diagrams that only show the happy path. A data flow with no trust boundaries marked. NFRs with no measurement method. Omitting the AI-specific specification — retrieval config, prompt version, thresholds. A failure-mode table that lists only technical faults and not "the model was confidently wrong". Documents written once and never updated; put them in the repository and review them at every gate.

**Done when.** A reader who has never met the system can explain how a request is served, where each boundary is, what the SLOs are, and what happens when it fails.

---

#### A17. Align internal teams (Product, Research, Solutions Architecture)

**Goal.** Get Product, Research, and Solutions Architecture behind a plan that is buildable, and that gives them something useful back.

**How to do it.**

1. **Write a one-page brief per team:** what we committed to the customer, the architecture we intend, what we need from them with dates, and what we will send back — evidence, data, and failure cases. Alignment without a specific ask produces enthusiasm and nothing else.
2. **To Research, send reproducible failure cases, not impressions.** Package 50–100 anonymised real cases with the expected behaviour and the observed behaviour, and ask for a capability assessment plus any known limitations of the models you plan to use. A case that reproduces is a case that gets fixed; a complaint is a case that gets deprioritised.
3. **To Product, bring evidence-backed requests:** problem | evidence | frequency | impact | proposed solution. Crucially, diagnose whether the gap is a prompt, a model, a tooling, or a data problem — filing a model capability request when the real gap is a corpus wastes a quarter and earns you a reputation for asking for the wrong thing.
4. **To Solutions Architecture, ask what already exists before you build.** The most common wasted week in AI delivery is reinventing a deployment template, Helm chart, connector, or evaluation harness that someone already wrote. Ask early; the answer is frequently "we have one for exactly this".
5. **Align on boundaries** — what this deployment will not do — so customer expectations set in Phase 0 are not quietly contradicted by a roadmap promise later.
6. **Set a recurring sync and a shared tracker,** and review the plan at each gate with them in the room.

**Artifacts.** Internal brief per team; capability-gap submission (case | observed | expected | impact | workarounds); product request template; reusable-module inventory; shared plan tracker.

**Worked example — Northwind Mutual.** The capability gap was structured extraction from long free-text adjuster narratives: the frontier model handled the *summary* well but not the extraction of coverage limits, dates, and claim circumstances, which the downstream claims system needed as fields. Forty anonymised narratives were packaged with expected structured fields; Research confirmed the gap, pointed to a structured-output function-calling path plus a domain fine-tune option, and flagged which of the two was realistic inside the timeline. The product request filed upstream was for a claims-domain extraction profile — with evidence attached, which is what makes requests get prioritised. Meanwhile Solutions Architecture pointed out an existing object-storage plus pgvector reference deployment that removed two weeks of infrastructure work. That template was not in the FDE playbook, so it was added — the discovery cost ten minutes and saved two weeks, and it is the argument for asking this question every single time.

**Watch out for.** Asking Product for a feature when the real gap is a prompt or a corpus. Sending Research impressions instead of reproducible cases. Not asking Solutions Architecture what already exists. Aligning verbally and not in writing. Having no feedback loop back to them after go-live, which is why the next engagement starts from zero.

**Done when.** Each internal team has a named contact, a written ask with dates, and a defined way to receive feedback — and the capability gaps are documented as reproducible cases.

---

#### A18. Conduct data exploratory analysis

**Goal.** Measure the data's fitness for the chosen technique before you build the pipeline, so that later failures are not misdiagnosed as model failures.

**How to do it.**

1. **Take a stratified sample** of 100–500 documents spanning formats, ages, owners, and quality — and keep it permanently, because this sample becomes the backbone of your evaluation set.
2. **Measure extractability:** parse success rate, character counts, table preservation, OCR confidence, and the rate of empty or garbage documents.
3. **Measure duplication and supersession:** content-hash duplicates, near-duplicates, and how many versions of the same document exist — with a rule for which one is current. Unresolved supersession is the classic cause of confidently wrong answers.
4. **Measure freshness:** the age distribution per source, compared against the freshness the use case requires.
5. **Measure structural quality:** heading depth, section counts, list and table ratios, code or configuration content, and language mix. Language mix is routinely missed and routinely expensive.
6. **Run a baseline retrieval bake-off:** two or three embedding candidates against two or three chunking configurations, scored on hit@k and MRR over the labelled set. Do this in a notebook — it is measurement, not a system.
7. **Run the no-retrieval baseline:** the same cases, the model alone. That number is the floor you must beat and it tells you how much the corpus is actually contributing. Skipping it leaves you unable to distinguish "the model is weak" from "the retrieval is weak".
8. **Report as a table with green / amber / red per dimension** and the top three remediations, each with an owner and an effort estimate.

**Artifacts.** Reproducible EDA notebook; extractability, duplication, and freshness tables; baseline retrieval benchmark; no-retrieval baseline result; remediation list.

**Worked example — Meridian Bank.** A 200-file stratified sample produced the numbers that shaped everything downstream: extractability 71% (48,800 scanned PDFs with no text layer, needing OCR), duplicate rate 6% (template KYC forms existing in three fixed versions), freshness 22% of files older than three years, and 11% non-English content following the 2021 acquisition — which triggered a multilingual embedding check, a Spanish-language gold set, and a translation-review policy, all found here rather than in Phase 4. The retrieval bake-off on 150 labelled questions: hit@5 = 0.58 with the default 512-token chunking, 0.71 after heading-aware chunking, 0.79 with hybrid plus reranking. The no-retrieval baseline on the same 150 questions was 0.31 — so the corpus was contributing 48 points, and the remaining 21-point gap was retrieval, not model capability. That single number is what prevented a procurement argument for a larger model.

**Watch out for.** Sampling one convenient folder instead of stratifying. Skipping the no-retrieval baseline. Measuring extraction but not duplication or supersession. Not checking language mix. Throwing the sample away — the sample you discard is the evaluation set you will wish you had in Phase 4.

**Done when.** You can put a number on extractability, duplication, freshness, and baseline retrieval quality, and you know the top three remediations.

---

#### Documented Cases Referenced

These are publicly reported cases used throughout Pillar A. They are cited for the *decision pattern* each illustrates, not as templates to copy.

| Case | Year | Point it supports | Used in |
|------|------|-------------------|---------|
| **Air Canada chatbot** — regulator held the airline liable for incorrect fare advice given by its chatbot | 2024 | Output is an attributable business statement; constrain, cite, and escalate | A8, A15 |
| **Morgan Stanley AI Assistant** — large curated content base with evaluation scoring; ~98% advisor-team adoption reported | 2024 | Adoption comes from curation and workflow fit, not model capability | A1, A2, A3 |
| **JPMorgan COiN** — NLP applied to bounded commercial-document review; ~360,000 hours/year of lawyer time reported saved | 2017 | Bounded classification is where this technology pays; measure time saved | A2, A5 |
| **Klarna** — large AI chat deflection and ~$40M reported savings, later quality concerns and renewed human hiring | 2024–25 | Cost-led optimisation pressures quality; define quality terms before the pilot | A3, A12, A13 |
| **BloombergGPT** — LLM built on proprietary financial data and deployed inside the Terminal | 2023 | Domain pretraining or fine-tuning when corpus and residency justify the cost | A5, A6 |
| **GitHub Copilot controlled study** — developers completed a scoped task ~55.8% faster with the assistant | 2023 | Measure productivity on a defined task, not on anecdote | A3, A13 |

</details>

---

### Pillar B: Infrastructure & GPU/Edge Provisioning

<details>
<summary><b>18 core tasks</b> (click to expand)</summary>

- [ ] Provision and configure GPU compute: cloud instances (A100, H100, L40S, T4), on-prem clusters, or edge devices (Jetson Orin, HEBO)
- [ ] Deploy and manage Kubernetes clusters for model serving (EKS, GKE, AKS, OpenShift)
- [ ] Configure model serving infrastructure: KServe, KubeRay, Triton Inference Server, Ray Serve
- [ ] Set up inference servers: vLLM, SGLang, TensorRT-LLM, TGI (Text Generation Inference), Ollama, llama.cpp
- [ ] Configure GPU scheduling and resource management: MIG, time-slicing, NVIDIA GPU Operator, Run:ai
- [ ] Establish networking: VPC design, private endpoints, VPC peering, service mesh, load balancers
- [ ] Configure TLS/mTLS, certificate management, and service-to-service authentication
- [ ] Set up private AI model endpoints (Azure Private Endpoint, AWS PrivateLink, GCP Private Service Connect)
- [ ] Configure model registry and artifact management (MLflow Model Registry, Vertex AI Model Registry, Weights & Biases)
- [ ] Set up container registries and image management for inference containers
- [ ] Configure storage: model weights (S3, GCS, Blob), vector DB persistence, dataset caching, NVMe for hot paths
- [ ] Implement autoscaling: HPA for inference pods, GPU node autoscaling, queue-based scaling for batch
- [ ] Configure monitoring infrastructure: Prometheus, Grafana, NVIDIA DCGM Exporter, node exporters
- [ ] Set up CI/CD pipelines for model and application deployment (GitHub Actions, ArgoCD, Flux)
- [ ] Provision edge compute for low-latency or air-gapped deployments (NVIDIA Jetson, Intel NUC, ruggedized servers)
- [ ] Configure 5G/LTE/Wi-Fi connectivity for edge deployments; test failover scenarios
- [ ] Implement secret management: HashiCorp Vault, AWS Secrets Manager, Azure Key Vault, Kubernetes Secrets
- [ ] Validate infrastructure: GPU health checks, network throughput, storage IOPS, cold-start benchmarks

</details>

---

### Pillar C: Model Deployment & Inference Stack

<details>
<summary><b>20 core tasks</b> (click to expand)</summary>

- [ ] Deploy open-source LLMs: Llama, Mistral, Qwen, DeepSeek, Phi, Gemma — configure quantization and serving parameters
- [ ] Integrate proprietary/closed models: OpenAI GPT, Anthropic Claude, Google Gemini, Cohere — configure API keys, rate limits, fallbacks
- [ ] Configure model parameters: temperature, top-p, max tokens, stop sequences, system prompts per use case
- [ ] Implement model routing and fallback logic: primary model → fallback model → error handling
- [ ] Build RAG pipelines: document ingestion → parsing → chunking → embedding → indexing → retrieval → reranking → generation
- [ ] Configure embedding models: OpenAI text-embedding-3, Cohere Embed, open-source (BGE, E5, GTE) — match to latency/cost/corpus requirements
- [ ] Implement chunking strategies: fixed-size, semantic, recursive, parent-child, contextual — optimize for retrieval quality
- [ ] Build retrieval logic: dense (vector), sparse (BM25), hybrid, multi-query, HyDE, reranking (Cohere Rerank, BGE Reranker, ColBERT)
- [ ] Design and build agentic workflows: multi-step reasoning, tool use, planning, reflection loops (LangGraph, CrewAI, ADK, AutoGen)
- [ ] Build MCP (Model Context Protocol) servers for customer tool and data source integration
- [ ] Implement function calling / tool use: API wrappers, database queries, search, code execution, customer system actions
- [ ] Configure context management: conversation history, sliding window, summarization, memory persistence
- [ ] Implement guardrails: input validation, output filtering, NeMo Guardrails, Llama Guard, Presidio (PII), custom validators
- [ ] Build structured output handling: JSON mode, schema validation, function call parsing, error recovery
- [ ] Implement streaming responses: SSE, WebSocket, token streaming for UX
- [ ] Configure caching: response cache (Redis), embedding cache, semantic cache for similar queries
- [ ] Build multi-modal capabilities when required: vision (images, documents), audio, video processing
- [ ] Implement fine-tuning workflows when needed: SFT, DPO, RFT — dataset preparation, training job submission, model evaluation
- [ ] Set up model versioning and A/B testing infrastructure: canary deployments, traffic splitting, version comparison
- [ ] Optimize inference performance: batch size tuning, KV cache optimization, prefix caching, speculative decoding, quantization (AWQ, GPTQ, FP8)

</details>

---

### Pillar D: Evaluation, Red-Teaming & Go-Live

<details>
<summary><b>18 core tasks</b> (click to expand)</summary>

- [ ] Design and implement evaluation frameworks: golden sets, regression suites, continuous eval pipelines
- [ ] Define evaluation metrics per use case: correctness, faithfulness, relevance, completeness, coherence, toxicity, bias
- [ ] Build LLM-as-judge evaluation pipelines: rubric design, judge model selection, calibration against human ratings
- [ ] Curate golden evaluation datasets with customer domain experts: representative queries, expected outputs, edge cases
- [ ] Implement automated eval harnesses: batch evaluation, CI-triggered eval, scheduled regression testing
- [ ] Conduct red-teaming exercises: prompt injection, jailbreak attempts, PII leakage, harmful content generation, adversarial inputs
- [ ] Test for failure modes: hallucination, refusal behavior, sycophancy, instruction following, multilingual performance
- [ ] Perform latency and throughput load testing: concurrent users, tokens/sec, TTFT, cold-start time, autoscaling behavior
- [ ] Conduct cost analysis: cost-per-request, cost-per-user, token usage patterns, optimization opportunities
- [ ] Test integration endpoints: API contracts, error handling, timeout behavior, retry logic, rate limiting
- [ ] Execute acceptance testing against customer success criteria: side-by-side comparison with baseline, human evaluation
- [ ] Run canary deployments for initial production traffic: monitor metrics, compare against baseline, rollback triggers
- [ ] Conduct stakeholder demos: executive summary view, technical deep-dive view, live interaction session
- [ ] Validate guardrails under adversarial conditions: jailbreak resistance, PII filtering, content policy enforcement
- [ ] Document evaluation results in customer-facing format: metrics, charts, findings, recommendations
- [ ] Obtain formal customer sign-off on deployment acceptance criteria
- [ ] Define production SLOs: latency targets, availability, error rates, quality thresholds
- [ ] Prepare go-live checklist: monitoring active, alerts configured, rollback plan ready, on-call roster set

</details>

---

### Pillar E: Production Monitoring, Debugging & Optimization

<details>
<summary><b>20 core tasks</b> (click to expand)</summary>

- [ ] Implement observability stack: LLM tracing (LangSmith, Langfuse, Arize, Phoenix), metrics, logging, distributed tracing
- [ ] Instrument RAG pipelines: track retrieval quality, chunk relevance, embedding drift, retrieval latency per query
- [ ] Instrument agent workflows: step-by-step traces, tool call success/failure, reasoning chains, token attribution per step
- [ ] Build dashboards: real-time quality metrics, latency percentiles, token usage, cost tracking, error rates
- [ ] Configure alerting: quality degradation, latency spikes, error rate breaches, cost anomalies, model drift
- [ ] Debug production issues: trace failed queries, identify retrieval failures, isolate tool call errors, reproduce edge cases
- [ ] Perform root cause analysis on quality degradation: data drift, model version changes, embedding model mismatches, pipeline regressions
- [ ] Optimize latency: reduce retrieval time (index tuning, query optimization), reduce generation time (prompt compression, smaller models for simple queries)
- [ ] Optimize cost: model routing (small model for simple, large for complex), prompt compression, caching strategies, batch processing
- [ ] Optimize quality: prompt engineering iterations, retrieval tuning (top-k, score thresholds), reranking improvements, few-shot example updates
- [ ] Implement feedback loops: user thumbs up/down, correction capture, implicit signals (edit rate, regeneration rate)
- [ ] Monitor data quality: ingestion pipeline health, document freshness, embedding index integrity, chunk coverage
- [ ] Handle model version updates: evaluate new model versions against golden sets, plan migration, manage rollback
- [ ] Manage incident response: triage, communicate with customer, escalate to internal engineering, conduct post-mortems
- [ ] Perform preventive maintenance: index rebuilds, cache clearing, dependency updates, security patches
- [ ] Support batch/offline workloads: document processing, bulk embedding, periodic re-indexing, data pipeline backfills
- [ ] Conduct regular quality reviews with customer stakeholders: metrics review, feedback collection, improvement planning
- [ ] Document and fix recurring issues: file bugs, create workarounds, contribute patches, update runbooks
- [ ] Manage capacity planning: GPU utilization, storage growth, vector DB scaling, traffic forecasting
- [ ] Provide remote support between on-site visits: async debugging, scheduled check-ins, emergency escalation paths

</details>

---

### Pillar F: Playbooks, Reusable Patterns & Knowledge Transfer

<details>
<summary><b>16 core tasks</b> (click to expand)</summary>

- [ ] Codify deployment playbooks: step-by-step guides per deployment pattern (RAG, agents, fine-tuning, multimodal)
- [ ] Build reusable infrastructure modules: Terraform, Helm charts, Kubernetes manifests, Docker Compose templates
- [ ] Create reusable application components: RAG pipeline modules, agent templates, eval harnesses, monitoring dashboards
- [ ] Develop deployment templates: reference architectures for common verticals (legal, support, code, search)
- [ ] Author and maintain architecture decision records (ADRs): capture "why" behind technical choices for future reference
- [ ] Create customer-facing documentation: setup guides, API references, configuration guides, troubleshooting runbooks
- [ ] Develop training materials: customer engineer onboarding, model operations, prompt engineering, debugging techniques
- [ ] Deliver hands-on training: customer ML engineers, data scientists, platform teams on system operation and debugging
- [ ] Build internal knowledge base: deployment patterns, lessons learned, common pitfalls, tool comparisons
- [ ] Create evaluation templates: golden set creation guide, eval framework templates, red-teaming checklists
- [ ] Document integration patterns: MCP server templates, API connector libraries, data ingestion recipes
- [ ] Produce reusable prompt libraries: system prompts, few-shot examples, chain-of-thought templates per use case
- [ ] Share deployment patterns with the broader FDE team: brown-bags, pattern libraries, case studies
- [ ] Review and improve deployment processes based on cumulative experience across multiple customers
- [ ] Package customer-specific work into reusable blocks for the shared internal library
- [ ] Contribute to open-source tools: bug fixes, feature PRs, documentation improvements to vLLM, LangChain, LangGraph, etc.

</details>

---

### Pillar G: Feedback Loop to Research & Product

<details>
<summary><b>12 core tasks</b> (click to expand)</summary>

- [ ] Collect structured field data: model failure modes, edge cases, capability gaps, latency bottlenecks, cost pain points
- [ ] Capture customer feature requests: SDK improvements, model capabilities, platform features, integration connectors
- [ ] Document specific model weaknesses: hallucination patterns, instruction-following gaps, domain knowledge deficits, reasoning failures
- [ ] Build eval datasets from real customer queries (anonymized, permissioned) for internal model evaluation
- [ ] Partner with Research on evaluation design: define metrics, build test suites, interpret results, prioritize model improvements
- [ ] Partner with Product on roadmap input: translate field patterns into prioritized feature requests with evidence
- [ ] Participate in model evaluation cycles: test new model versions against customer workloads, report findings
- [ ] Identify repeatable field patterns: common integration challenges, recurring architecture decisions, friction points
- [ ] Convert friction points into product feature requests: formal, evidence-based submissions to Product/Engineering
- [ ] Contribute to platform gap analysis: where the product is missing capabilities that customers need
- [ ] Share anonymized deployment learnings across the FDE team: what works, what doesn't, emerging patterns
- [ ] Participate in product design reviews: provide field perspective on upcoming features, UX decisions, API design

</details>

---

## 4. Day in the Life: AI Deployment Week

### On-Site Deployment Week (Example — POC-to-Pilot engagement)

| Day | Theme | Key Activities |
|-----|-------|---------------|
| **Mon** | Discovery & Architecture | Stakeholder kickoff with customer ML/platform teams; understand business problem and success criteria; review available data sources and systems; define evaluation metrics; present proposed architecture; align on phased delivery plan |
| **Tue** | Infrastructure & Data Pipeline | Provision GPU compute and Kubernetes; set up model serving infrastructure; connect to customer data sources; build document ingestion pipeline; configure vector DB and embedding pipeline; validate data flow end-to-end |
| **Wed** | AI Application Build | Deploy models (open-source or API); build RAG pipeline: chunking, embedding, retrieval, reranking, generation; configure system prompts and few-shot examples; integrate with customer systems via APIs/MCP; implement guardrails |
| **Thu** | Evaluation & Optimization | Build eval framework with golden sets; run LLM-as-judge evaluation; red-team for injection/jailbreak/PII; load test for latency and throughput; iterate on prompt engineering and retrieval tuning; optimize cost and latency |
| **Fri** | Demo, Go-Live & Handoff | Stakeholder demo with live interaction; present evaluation results and metrics; resolve remaining issues; set up production monitoring and alerts; hand off documentation and credentials; conduct knowledge transfer session; plan pilot phase |

### Remote Week (Between Deployments)

| Day | Theme | Key Activities |
|-----|-------|---------------|
| **Mon** | Issue Triage & Monitoring | Review production dashboards and alerts; triage open issues; provide remote debugging support; follow up on customer questions |
| **Tue** | Engineering Feedback | File bugs and feature requests with Research/Product; contribute eval data to model improvement; review new model versions against customer workloads |
| **Wed** | Playbook & Tooling | Update deployment playbooks and templates; improve reusable components; write automation scripts; contribute to internal library |
| **Thu** | Meetings & Knowledge Share | Sync with internal teams on field feedback; FDE team pattern sharing session; customer success alignment; product roadmap review |
| **Fri** | Prep & Learning | Prepare for next deployment: data exploration, architecture planning; study new AI tools and techniques; catch up on industry developments |

---

## 5. Skills & Tools Matrix

### Technical Skills

| Domain | Skills | Tools & Technologies |
|--------|--------|---------------------|
| **AI/ML Engineering** | LLM prompting & engineering; RAG architecture; agentic workflows; fine-tuning (SFT, DPO, RFT); evaluation design; model selection & routing; guardrails & safety | OpenAI API, Anthropic API, Google Gemini, vLLM, SGLang, TensorRT-LLM, Hugging Face, LangGraph, LangChain, CrewAI, LlamaIndex, MCP |
| **Inference & Optimization** | Model serving; quantization (AWQ, GPTQ, FP8); KV cache optimization; batching; speculative decoding; GPU profiling | vLLM, TGI, Ollama, llama.cpp, NVIDIA NIM, Triton, KServe, Ray Serve |
| **Data & Vector Systems** | Document ingestion & parsing; chunking strategies; embedding models; vector search; hybrid retrieval; reranking; data quality | Pinecone, Weaviate, Qdrant, Milvus, Chroma, pgvector, Elasticsearch, Cohere Rerank, BGE |
| **Software Engineering** | Python (advanced); TypeScript/JavaScript; API design (REST, gRPC, GraphQL); async programming; testing; CI/CD | Python, FastAPI, Node.js, React, pytest, GitHub Actions, ArgoCD |
| **Cloud & Infrastructure** | Kubernetes; GPU provisioning; networking; infrastructure-as-code; secret management; observability | AWS, GCP, Azure, Kubernetes, Terraform, Helm, Docker, Prometheus, Grafana |
| **MLOps & Observability** | Model registry; experiment tracking; LLM tracing; drift detection; quality monitoring; cost tracking | MLflow, Weights & Biases, LangSmith, Langfuse, Arize, Phoenix |
| **Data Engineering** | ETL/ELT pipelines; API integration; database querying; stream processing; data governance | Apache Airflow, dbt, Spark, SQL, Kafka, customer APIs |
| **Responsible AI & Safety** | Red-teaming; guardrails implementation; PII detection; bias assessment; content filtering; compliance | NeMo Guardrails, Llama Guard, Presidio, Fairlearn, LLM Guard |
| **Enterprise Integration** | Identity/SSO; enterprise API patterns; legacy system integration; data connectivity | Okta, Azure AD, Salesforce, ServiceNow, SAP, SharePoint, Confluence |
| **Networking & Security** | VPC design; TLS/mTLS; VPN; firewall rules; zero-trust networking | AWS VPC, GCP VPC, WireGuard, Cloudflare, Istio |

### Soft Skills

| Skill | Why It Matters |
|-------|---------------|
| **Technical Communication** | Explain RAG failure modes to a VP; discuss token economics with a CFO; debug with an ML engineer |
| **Customer Empathy** | Understand the business problem behind the AI ask; manage expectations about model capabilities and limitations |
| **Autonomy & Judgment** | Make architecture decisions on-site without engineering support; know when to escalate vs. solve independently |
| **Ambiguity Tolerance** | Navigate undefined requirements, evolving customer needs, and fast-moving model capabilities |
| **Problem-Solving Under Pressure** | Debug production issues when customer executives are watching; stay methodical during incidents |
| **Teaching & Enablement** | Train customer teams on AI operations; explain prompting strategies; build confidence in the system |
| **Cross-Functional Collaboration** | Work with Research, Product, Sales, Customer Success, and customer teams simultaneously |
| **Executive Presence** | Present architecture decisions and tradeoffs to C-suite; demo confidently in high-stakes meetings |
| **Product Thinking** | Translate customer needs into product features; see beyond the immediate ask to the underlying workflow |
| **Written Documentation** | Produce clear architecture docs, runbooks, and eval reports that others can follow without you |

---

## 6. Cross-Functional Interfaces

### Internal Stakeholders

| Team | Interaction Pattern | Topics |
|------|-------------------|--------|
| **Research / Model Team** | Continuous eval feedback loop | Model failure modes, capability gaps, eval data, model selection guidance |
| **Product / Platform** | Feature requests, roadmap input | SDK improvements, platform gaps, integration needs, UX feedback |
| **ML Platform / MLOps** | Infrastructure collaboration | Inference stack, model registry, CI/CD, observability standards |
| **Safety / Trust & Safety** | Red-teaming, guardrails | Safety review, content policy, responsible AI, compliance requirements |
| **Solutions Architecture** | Joint discovery, handoff | Architecture review, reference designs, POC scoping |
| **Sales / Business Development** | Pre-sales technical support | Technical validation, POC demos, RFP responses, deal support |
| **Customer Success** | Account-level coordination | Deployment handoffs, escalation paths, customer satisfaction |
| **Leadership** | Periodic reporting | Deployment status, field trends, resource needs, strategic input |

### External Stakeholders

| Stakeholder | Interaction Pattern | Topics |
|-------------|-------------------|--------|
| **Customer ML/Data Engineering** | Daily during deployment | Data pipelines, model integration, eval design, debugging |
| **Customer Platform/IT** | Setup and integration | Infrastructure, networking, security review, identity, compliance |
| **Customer Executive Sponsors** | Kickoff, demo, sign-off | Business outcomes, metrics, roadmap alignment, risk |
| **Customer End-Users** | Feedback collection | UX feedback, workflow fit, quality perception, adoption barriers |
| **Customer Legal/Compliance** | Compliance review | Data privacy, AI governance, EU AI Act, usage policies |
| **Third-Party Vendors** | Integration coordination | Model APIs, vector DBs, data connectors, evaluation tools |

---

## 7. AI Deployment Lifecycle Phases

```
Phase 0        Phase 1        Phase 2        Phase 3        Phase 4        Phase 5        Phase 6
Discovery  →   Data &       →   Infra &     →   Model &    →   Eval &     →   Go-Live   →   Production
& Scoping      Readiness       Model Deploy   RAG/Agent     Red-Team      & Handoff    Optimization
                   Assessment     Stack Build    Build         & Load Test
```

| Phase | FDE Role | Key Outputs |
|-------|----------|-------------|
| **0 — Discovery & Scoping** | Technical discovery, architecture design, success metrics definition | Architecture doc, success criteria, phased plan, risk register |
| **1 — Data & Readiness Assessment** | Data exploration, quality assessment, integration point mapping | Data readiness report, integration plan, baseline metrics |
| **2 — Infrastructure & Model Deploy** | GPU provisioning, inference stack setup, model deployment | Running inference stack, model endpoints, monitoring baseline |
| **3 — RAG/Agent Build** | RAG pipeline construction, agentic workflow development, guardrails | Working AI application, integration with customer systems |
| **4 — Eval & Red-Team** | Evaluation framework, red-teaming, load testing, optimization | Eval results, red-team report, performance benchmarks |
| **5 — Go-Live & Handoff** | Production deployment, monitoring setup, customer training | Production system, trained team, documentation package |
| **6 — Production Optimization** | Monitoring, debugging, continuous improvement, feedback loop | Resolved issues, optimized performance, product feedback |

---

## 8. AI Deployment Context Variations

> The core responsibilities above apply universally. However, the emphasis shifts based on deployment context:

### RAG vs. Fine-Tuning vs. Agentic

| Aspect | RAG (Retrieval-Augmented Generation) | Fine-Tuning (SFT/DPO/RFT) | Agentic (Multi-Step) |
|--------|-------------------------------------|--------------------------|---------------------|
| Primary challenge | Data quality, retrieval accuracy | Dataset quality, training compute | Orchestration, tool reliability |
| Data requirements | Large corpus, structured documents | Labeled training examples, preference pairs | Tool definitions, workflow specs |
| Evaluation focus | Faithfulness, relevance, retrieval quality | Task accuracy, generalization, regression | Task completion, step efficiency, error recovery |
| Infrastructure | Vector DB, embedding pipeline, inference | GPU training cluster, training framework | Inference + tool execution, state management |
| Iteration speed | Fast (update index, change prompts) | Slow (retrain cycle) | Medium (modify workflow, update tools) |

### Cloud vs. Edge vs. Hybrid

| Aspect | Cloud | Edge | Hybrid |
|--------|-------|------|--------|
| GPU availability | Elastic, on-demand | Constrained (Jetson, Orin) | Split workload |
| Latency requirements | Flexible | Strict (real-time) | Mixed |
| Data gravity | Data in cloud | Data on-prem | Synchronized |
| Connectivity | Always-on | Intermittent/air-gapped | Sync + fallback |
| Model selection | Larger models OK | Smaller/quantized models | Route by context |

### Open vs. Closed Models

| Aspect | Open-Source (Llama, Mistral, Qwen) | Closed/API (GPT, Claude, Gemini) |
|--------|------------------------------------|--------------------------------|
| Customization | Fine-tuning, full control | Prompt engineering, limited |
| Cost model | Infrastructure + ops | Per-token API pricing |
| Data privacy | Full control (on-prem) | Data sent to provider |
| Latency | Tunable (batching, quantization) | Network-dependent |
| Capability | Rapidly improving | Latest frontier capabilities |
| Operations burden | Higher (self-managed) | Lower (managed API) |

### POC → Pilot → Production

| Aspect | POC (Proof of Concept) | Pilot | Production |
|--------|----------------------|-------|------------|
| Scope | Single use case, narrow data | Real users, representative data | Full workload, SLAs |
| Infrastructure | Ephemeral, minimal | Pre-production, realistic | Production-grade, monitored |
| Evaluation | Informal, demo-driven | Structured eval, golden sets | Continuous eval, regression testing |
| Success criteria | "Does it work at all?" | "Does it work well enough?" | "Does it meet SLOs reliably?" |
| FDE involvement | High (hands-on build) | Medium (guidance + review) | Low (monitoring + optimization) |

### Vertical: Legal / Code / Support / R&D

| Vertical | Unique Aspects |
|----------|---------------|
| **Legal** | Citation accuracy, jurisdiction awareness, privilege protection, contract analysis, e-discovery compliance |
| **Software Engineering** | Code generation, repo indexing, AST-aware retrieval, CI/CD integration, developer experience |
| **Customer Support** | Ticket routing, response drafting, knowledge base freshness, CSAT measurement, agent augmentation |
| **R&D / Research** | Literature review, hypothesis generation, experimental design, technical depth, citation management |
| **Healthcare** | HIPAA compliance, clinical accuracy, medical terminology, regulatory review, safety-critical validation |
| **Finance** | Regulatory compliance, audit trails, hallucination tolerance (zero), explainability, risk management |

---

## 9. Career Progression Framework

| Level | Scope | Autonomy | Typical Travel | Key Differentiators |
|-------|-------|----------|---------------|---------------------|
| **Junior FDE** (0-2 yrs) | Assists on deployments; executes defined tasks | Low — follows guidance from senior FDE | 30-50% | Learns the AI stack and deployment processes; builds foundational skills; shadowing and supervised execution |
| **FDE** (2-5 yrs) | Owns end-to-end deployments independently | Medium — makes most technical decisions on-site | 50-70% | Independent deployment ownership; designs RAG/agent architecture; handles standard troubleshooting; represents company to customer |
| **Senior FDE** (5-8 yrs) | Leads complex, multi-site, or first-of-kind deployments | High — architecture decisions, novel solutions | 50-70% | Mentors junior FDEs; authors playbooks; interfaces directly with Research on model improvements; handles escalated issues; shapes product roadmap |
| **Lead / Principal FDE** (8+ yrs) | Program-level ownership; shapes deployment strategy | Very High — sets standards and methodology | 30-50% | Defines deployment framework; drives tooling/process improvements; cross-functional leadership; industry representation; influences model/product direction |

### Skill Progression Trajectory

```
Junior FDE                          Senior FDE                        Principal FDE
    │                                   │                                  │
    ├─ Deploy standard RAG              ├─ Design RAG/agent architecture  ├─ Define deployment standards
    ├─ Run eval scripts                 ├─ Build eval frameworks          ├─ Build deployment methodology
    ├─ Debug retrieval failures         ├─ Diagnose cross-stack issues   ├─ Predict failure modes pre-deployment
    ├─ Learn inference stack            ├─ Optimize latency and cost     ├─ Shape model/product roadmap
    ├─ Follow deployment playbooks      ├─ Author new playbooks          ├─ Influence industry best practices
    └─ Execute under guidance           ├─ Lead POC-to-production        └─ Set company-wide FDE strategy
                                        └─ Mentor junior FDEs
```

---

## 10. Industry Context: AI Company Types

> The AI FDE role manifests differently depending on the company type:

| Company Type | Examples | FDE Focus |
|--------------|----------|-----------|
| **Foundation Model Labs** | OpenAI, Anthropic, Google DeepMind, Cohere, Mistral | Model deployment, API integration, eval design, safety red-teaming, customer-specific fine-tuning, frontier capability access |
| **AI Platform** | Databricks, Dataiku, Scale AI, Weights & Biases, LangChain | Platform onboarding, custom pipeline building, model training on platform, MLOps setup, integration with customer stack |
| **Enterprise AI Apps** | Glean, Harvey, Sierra, Moveworks, Abridge, Perplexity | Vertical-specific RAG/agents, workflow integration, domain customization, end-user training, adoption metrics |
| **Cloud Providers** | AWS (Bedrock), Azure (AI Foundry), GCP (Vertex AI) | Platform deployment, GPU optimization, enterprise integration, migration from competitor platforms, reference architectures |
| **Consulting / SIs** | Deloitte, Accenture, Palantir, McKinsey (QuantumBlack) | Multi-client, varied stacks, accelerator development, executive engagement, change management |
| **AI-Native Startups** | Fireworks, Together AI, Groq, Baseten, Modal | Inference optimization, model serving, cost reduction, performance benchmarking, developer experience |
| **Industry-Specific AI** | Abridge (healthcare), Harvey (legal), Codeium (code), Jasper (marketing) | Domain data pipelines, compliance, specialized evaluation, workflow-specific agents, vertical GTM |

---

## 11. Personal Reflection / Customization

> Use this section to tailor the reference to your own context.

### My Current Focus Areas
- [ ] Area 1: ________________________________
- [ ] Area 2: ________________________________
- [ ] Area 3: ________________________________

### Model Deployment Checklist (Customized)
- [ ] Model selected and benchmarked for use case
- [ ] Quantization and serving parameters optimized
- [ ] Fallback model configured
- [ ] Inference latency validated against SLO
- [ ] Cost per request measured and within budget

### RAG Architecture Decision Record Template
| Decision | Options Considered | Choice | Rationale | Date |
|----------|-------------------|--------|-----------|------|
| Vector DB | | | | |
| Embedding Model | | | | |
| Chunking Strategy | | | | |
| Reranker | | | | |
| Retrieval Mode | | | | |

### Eval Framework Template
| Metric | Target | Current | Measurement Method | Status |
|--------|--------|---------|-------------------|--------|
| Faithfulness | | | LLM-as-judge | |
| Relevance | | | LLM-as-judge + human | |
| Latency (p95) | | | Tracing | |
| Cost/request | | | Token accounting | |
| Hallucination rate | | | Golden set regression | |

### Red-Teaming Findings Log
| Date | Attack Type | Vulnerability Found | Mitigation | Status |
|------|------------|-------------------|------------|--------|
| | | | | |

### Skill Gaps to Address
| Skill | Current Level | Target Level | Action Plan |
|-------|--------------|--------------|-------------|
| | | | |
| | | | |
| | | | |

### Key Lessons Learned (Field Notes)
| Date | Customer/Situation | Lesson | Action Taken |
|------|-------------------|--------|--------------|
| | | | |
| | | | |

---

*This document is a living reference. Update it as you gain field experience and as the AI FDE role evolves.*

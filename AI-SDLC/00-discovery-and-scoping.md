# Phase 0 — Discovery & Scoping

[← AI SDLC index](AI-SDLC.md) · **Phase 0 of 7** · Next: [Phase 1 — Data & Readiness Assessment](01-data-readiness-assessment.md)

**Goal:** Convert a vague business request into a scoped, architected, measurable AI program — with an explicit technique choice and a written exit criterion for every stage.

**Exit criterion:** Architecture document, success criteria, phased plan, and risk register signed off by the executive sponsor and technical owner.

---

## 0.1 Why This Phase Exists

Most failed AI projects are not technology failures — they are *scope* failures that were never caught. The organization asked for "an AI assistant for our support team," and the build optimized for answering questions. But the real job was *deflecting* tickets without making things worse, which is a completely different system: it needs write access to ticket state, an abstention path, escalation rules, and a metric on resolution rate rather than answer quality.

Skipping Phase 0 does not save time. It relocates the cost to Phase 4 (when eval reveals the wrong thing was built) or Phase 6 (when adoption is zero and no one knows why).

**Three questions Phase 0 must answer:**
1. What job is this system doing, and how do we know it is doing it well?
2. Which technique fits — prompting, RAG, fine-tuning, agentic — and why?
3. Who owns it, who operates it, and what does it cost?

---

## 0.2 Objectives

- Understand the business problem, the current workflow, and the human in the loop
- Define measurable success criteria in both quality and business terms
- Select the AI technique and justify it against alternatives
- Design the target architecture and record the trade-offs
- Identify data sources, integration points, and security constraints
- Map stakeholders and communication cadence
- Build the phased delivery plan with explicit gates
- Register risks with mitigations and early warning signals
- Scope the engagement: deliverables, dependencies, and customer effort required

---

## 0.3 Entry Criteria

- [ ] A business sponsor exists and can make prioritization calls
- [ ] The current workflow is documented or can be described in one page
- [ ] Access (at least read) to candidate data sources and their owners
- [ ] A rough volume estimate exists (requests/day, corpus size, user count)
- [ ] Known constraints are surfaced (data residency, air-gapped, budget cap)

> If the sponsor cannot say what happens if this project fails, stop and find one. A project with no accountable owner will drift indefinitely.

---

## 0.4 Key Activities

### A. Problem framing

- Run technical discovery sessions with customer ML, data, platform, and business engineering teams
- Restate the request as a **job to be done**: "Given a support ticket, produce a draft resolution and route it to the right queue in under 30 seconds"
- Map the current state: who does it today, with what tools, in how long, at what quality
- Identify the baseline — if a human does this today, capture their accuracy and time; if a rule engine does, capture its hit rate
- Separate the *must* from the *nice*: the single highest-value workflow wins; breadth is a later phase
- Write down explicit non-goals — they are the most valuable line in the document

### B. Success criteria definition

Define metrics the business cares about *and* metrics the system can control:

| Category | Metric Examples | Who Cares |
|----------|-----------------|-----------|
| Quality | Task accuracy, faithfulness, citation correctness, abstention rate | Product, domain expert |
| Reliability | Success rate, fallback rate, retry rate, timeout rate | Engineering, SRE |
| Performance | TTFT p50/p95, tokens/sec, end-to-end p95 latency | End user, SRE |
| Cost | Cost per request, cost per successful task, monthly spend ceiling | Finance, exec |
| Adoption | Weekly active users, task completion rate, CSAT, opt-out rate | Product, exec |
| Safety | Injection resistance rate, PII leak rate, policy violation rate | Security, legal |

Write each as `metric ≥ threshold under <condition>`. "The system should be accurate" is not a criterion; "≥85% answer correctness on the 200-case golden set, measured against SME rubric" is.

### C. Technique selection — prompting vs. RAG vs. fine-tuning vs. agentic

| Technique | Choose it when | Avoid it when | Primary risk |
|-----------|----------------|---------------|---------------|
| **Prompting only** | Knowledge is general and current; task is reasoning-shaped | Domain facts must be exact | Hallucination on specifics |
| **RAG** | Large corpus of live/private knowledge; answers must cite sources | Corpus is tiny (<100 docs) or the task is pure transformation | Retrieval quality — not generation — is the ceiling |
| **Fine-tuning** | Behavior/style/domain format must be learned; labeled data exists | You have knowledge but not examples of behavior | Slow iteration; needs retrain for every change |
| **Agentic** | Multi-step work with tool calls and branching | Task is a single call; tool set is unreliable | Orchestration errors, runaway loops, cost |
| **Hybrid** | Most real systems: RAG for knowledge + tools for action + prompt tuning for format | — | Integration complexity |

Decision rule of thumb: **RAG is the default.** Reach for fine-tuning when you have examples of desired behavior and the corpus alone is not enough; reach for agents when the work requires actions, not just answers.

### D. Target architecture design

- **Deployment topology:** cloud vs. edge vs. hybrid. Choose by data gravity, latency floor, connectivity, and GPU availability. Hybrid means you now own two failure domains.
- **Model sourcing:** open-weight (Llama, Mistral, Qwen, Gemma) vs. API (GPT, Claude, Gemini). Decide on capability ceiling, data residency, ops burden, and total cost per request.
- **Multi-model routing:** plan for a primary + fallback + task-based router (small model for simple, large for hard) from day one — retrofitting routing after cost surprises is painful.
- **Retrieval stack:** vector DB (Pinecone, Weaviate, Qdrant, Milvus, pgvector, Elasticsearch), embedding model, chunking strategy, reranker, hybrid vs. dense-only.
- **Serving stack:** managed API, or vLLM / SGLang / TensorRT-LLM / TGI on Kubernetes (KServe, Ray Serve).
- **Integration surface:** which customer systems (Salesforce, ServiceNow, SAP, Confluence, SharePoint), which IdP (Okta, Entra ID), which APIs, in and out.
- **Security posture:** authn/authz on the AI layer itself, private endpoints, secret handling, PII boundaries, tenant isolation.

Record every non-obvious choice as an **ADR** (see §0.6). Six months later the only question anyone will ask is *why*, and the answer must outlive the people who were in the room.

### E. Stakeholder & governance mapping

| Stakeholder | What they control | What they need from you | Engagement |
|-------------|------------------|------------------------|------------|
| Executive sponsor | Budget, priority, go/no-go | Business case + risk posture | Weekly, 30 min |
| Technical POC | Architecture veto, engineering time | Design + evidence | Daily |
| Domain SMEs | Golden sets, rubrics | Time to contribute batches | Weekly workshop |
| Data owners | Corpus access, freshness | Data requirements + freshness ask | Weekly |
| Platform / IT | Infra, network, security review | Infra spec + network diagram | Weekly + formal review |
| Security / Legal / Privacy | Compliance, residency, retention | Data flow, threat model, DPIA inputs | Gate review |
| End users | Reality check, adoption | Workflow fit, feedback channel | Prototype testing |
| Support / Ops | On-call, runbooks, escalation | Runbooks + alerts ownership | Pre-go-live |

### F. Risk register

| Risk | Likelihood | Impact | Early Warning | Mitigation | Owner |
|------|-----------|--------|---------------|------------|-------|
| Corpus quality insufficient | | | Low retrieval hit@k in Phase 1 | Extra curation, chunk rework | |
| Model capability gap for task | | | Low baseline accuracy | Task decomposition, model swap | |
| Integration complexity (SSO, write-back) | | | Slip in API contract | Early contract tests | |
| GPU/infra lead time | | | Quota not approved | Book capacity in Phase 2 immediately | |
| PII/compliance blocker late | | | Legal review scheduled late | Compliance review in Phase 0 | |
| No SME time for evals | | | Calendar not blocked | Contract golden-set hours in SOW | |
| Cost ceiling exceeded | | | Token estimate > budget | Early token estimate + routing | |

### G. Phased delivery plan

Define POC → Pilot → Production with a scope, an owner, and an **exit criterion** for each. Include what deliberately stays out of the POC — the most common POC failure is trying to prove production readiness in two weeks.

### H. Engagement scoping

- Workstreams and deliverables
- Dependencies (customer effort, procurement, hardware)
- SOW validation — check stated requirements against actual technical needs; flag infeasible commitments early
- Pre-sales technical validation: demos, reference architectures, POC proposals

---

## 0.5 Inputs → Outputs

| Input | Source | Output | Consumer |
|-------|--------|--------|----------|
| Business request | Sponsor | Problem statement + non-goals | All phases |
| Current workflow | Ops + SMEs | Baseline metrics, value map | Phases 1, 4 |
| Data source list | Data owners | Data requirements + access plan | Phase 1 |
| Constraints | Security, platform, legal | Architecture constraints | Phases 1–2 |
| Volume/cost assumptions | Ops + finance | Capacity and budget model | Phase 2 |
| Existing platform standards | Platform team | Reference architecture | Phases 2–3 |
| Golden set candidates (seed) | SMEs | Seed eval cases (expanded in Phase 4) | Phase 4 |

---

## 0.6 Artifacts & Templates

| Artifact | Purpose | Template Sketch |
|----------|---------|-----------------|
| **Architecture document** | System design + trade-offs | Context → goals/non-goals → components → data flow → deployment topology → SLOs → cost model → risks → ADRs → open questions |
| **Success criteria sheet** | Definition of done | Metric \| threshold \| measurement method \| owner \| phase verified |
| **Phase plan** | POC→Pilot→Prod with gates | Stage \| scope \| deliverables \| exit criteria \| dates \| owner |
| **Risk register** | Live risk management | Risk \| likelihood \| impact \| early warning \| mitigation \| owner \| status |
| **ADR** | Decision record | Context → options considered → decision → rationale → consequences → date |
| **Stakeholder map** | Who decides what | Stakeholder \| role \| interest \| influence \| cadence |
| **Solution design (RAG/agent)** | Pipeline spec | Ingestion → parse → chunk → embed → index → retrieve → rerank → generate → guard → serve |

---

## 0.7 Tooling Reference

| Need | Options |
|------|---------|
| Architecture / diagrams | Mermaid in Markdown, Excalidraw, Lucidchart, draw.io, architecture-as-code |
| ADRs | adr-tools, log4brains, Markdown in `docs/adr/` |
| Risk / project tracking | Jira, Linear, Asana, risk register sheet |
| Prompt / notebook scratchpad | Jupyter, VS Code, LangSmith, promptfoo |
| Architecture reference | Public reference architectures, cloud provider solution blueprints |
| Stakeholder mapping | Miro, FigJam, Lucidchart |
| Cost modeling | Token calculators, spreadsheet models, provider pricing pages |

---

## 0.8 Metrics for This Phase

| Metric | How Measured | Target |
|--------|--------------|--------|
| Success criteria agreed & written | Sign-off | 100% before build starts |
| Non-goals documented | Document review | Yes |
| Technique choice justified against ≥2 alternatives | ADR review | Yes |
| Risk register entries with named owners | Register audit | All High/Medium risks |
| Phase plan has exit criteria per stage | Plan review | Yes |
| Stakeholder map coverage | Coverage vs. RACI | All decision owners named |
| Discovery → build start | Elapsed time |  |

---

## 0.9 Exit Criteria — Gate to Phase 1

- [ ] Problem statement describes a job-to-be-done, in business terms, with the human-in-the-loop defined
- [ ] Non-goals written and acknowledged
- [ ] Success criteria are measurable, with thresholds and measurement methods
- [ ] Baseline (current human/rule performance) captured
- [ ] Technique selected, with ≥2 alternatives documented and rejected with reasons
- [ ] Architecture document reviewed, ADRs recorded for all major decisions
- [ ] Data sources, integration points, and constraints identified
- [ ] Deployment topology and model sourcing decided (incl. fallback)
- [ ] Risk register populated; every High risk has an owner and mitigation
- [ ] POC → Pilot → Production plan with exit criteria agreed
- [ ] Stakeholder map complete; exec sponsor has committed to the plan

> **Gate decision owner:** executive sponsor + technical lead. Sign-off recorded in the architecture document.

---

## 0.10 Common Failure Modes & Anti-Patterns

| Symptom | Root Cause | Fix |
|---------|------------|-----|
| "It answers questions but users don't use it" | Optimized for the request as stated, not the workflow | Redo problem framing with the operator, not the sponsor |
| Team debates RAG vs. fine-tuning for weeks | No evaluation plan to arbitrate on evidence | Build a 50-case eval set in Phase 0/1 and measure both |
| POC is a 6-month project | Production criteria applied at POC stage | Explicitly define POC scope; move SLO work to Pilot |
| Security review happens at month 5 | Compliance not in the phase plan | Compliance is a Phase 0 output, not a Phase 4 event |
| No success criteria, "the demo will tell us" | Metrics deferred to avoid hard conversations | Write thresholds now; the conversation gets easier, not harder |
| Architecture chosen by novelty | Trend-driven selection | Score options against constraint list from §0.2 |
| Sponsor disappears after kickoff | No governance cadence agreed | Cadence and reporting template set in the plan |

---

## 0.11 Role Breakdown

| Role | Responsibility in Phase 0 |
|------|----------------------------|
| **FDE** | Runs discovery, designs architecture, drives technique selection, maintains risk register, is the customer-facing integrator |
| **Executive Sponsor** | Owns the problem statement, resolves priority conflicts, approves budget and go/no-go |
| **Technical POC** | Reviews architecture, commits engineering time, flags existing platform constraints |
| **Domain SME** | Describes the workflow, flags edge cases, validates the problem framing |
| **Data Owner** | Confirms availability, ownership, and freshness commitments |
| **Platform / IT** | Confirms infra feasibility, network path, security review path |
| **Security / Legal** | States residency, retention, and compliance constraints up front |
| **ML Engineer** | Advises on technique feasibility and rough cost/latency envelope |

---

## 0.12 Practical Field Notes

- Ask the operator, not the sponsor: "show me how you do this today" surfaces 80% of the requirements the sponsor forgot.
- Write the **abort criteria** too — "we stop if accuracy is below 60% after three iteration cycles" prevents a slow, quiet death.
- Always cost a managed-API variant against self-hosted before deciding on infra. The gap is usually smaller than expected and the ops savings are usually larger.
- Get the security questionnaire early; its questions often reshape the architecture, and late answers mean rework.
- Reserve SME calendar time for golden sets *now*. It is the single most commonly under-resourced dependency in AI delivery.
- Keep the architecture doc short enough that people actually read it. If it is over ~10 pages, move detail into linked ADRs.

---

## 0.13 Related Pages

- [AI SDLC index](AI-SDLC.md)
- Next: [Phase 1 — Data & Readiness Assessment](01-data-readiness-assessment.md)
- Previous phase: none — this is where it starts
- Source reference: [FDE-Responsibilities.md — Pillar A (Discovery, Scoping & Architecture Design)](../FDE-Responsibilities.md) and [§8 AI Deployment Context Variations](../FDE-Responsibilities.md)
- **Deeper dive:** [FDE-Responsibilities.md Pillar A](../FDE-Responsibilities.md) expands the same 18 discovery tasks as step-by-step procedures with templates and five worked business cases (Northwind Mutual, Meridian Bank, Corvid, St. Aldwyn Health, Halden & Rowe) plus six publicly documented reference cases.

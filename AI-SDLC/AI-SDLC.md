# AI Software Development Lifecycle (AI SDLC)

**Version:** 1.0
**Last Updated:** 2026-10-21
**Tags:** #AI-SDLC #lifecycle #LLM #RAG #agents #evaluation #deployment #governance

---

## 1. What Is the AI SDLC?

The **AI SDLC** is the end-to-end process for taking a business problem to a production AI application — and keeping it trustworthy once it is live. It borrows the shape of classic software delivery (plan → build → test → deploy → operate) but inverts three of its assumptions:

| Classic SDLC Assumption | AI SDLC Reality |
|---|---|
| A passing test proves the feature works | A passing test proves *one* path works; quality is a distribution, so it is measured with **evals** against golden sets |
| Source code is the only artifact that ships | **Model + prompt + retrieval index + tool schema** ship too, and each is versioned and can be a regression source |
| Feedback arrives at the next release | Feedback (bad answers, drift, cost) must flow **back** into prompts, indexes, evals, and training data continuously |

**Why the phases matter:** each phase exists to retire a specific class of risk *before* it becomes an incident. Skipping Phase 1 because "the data looks fine" surfaces in Phase 4 as an unrecoverable retrieval problem. Skipping Phase 4 because "the demo was great" surfaces in Phase 6 as a quality incident with no rollback.

**Scope of this guide:** the 7-phase lifecycle below covers a customer-facing AI application (RAG, agentic, or fine-tuned). Research/training of new foundation models is a different lifecycle and is out of scope.

---

## 2. Lifecycle at a Glance

```
Phase 0        Phase 1        Phase 2        Phase 3        Phase 4        Phase 5        Phase 6
Discovery  →   Data &       →   Infra &     →   Model &    →   Eval &     →   Go-Live   →   Production
& Scoping      Readiness       Model Deploy   RAG/Agent     Red-Team      & Handoff    Optimization
                Assessment     Stack Build    Build         & Load Test
```

Each phase has a **gate**: a written exit criterion that must be met before effort moves forward. Gates are the only thing keeping AI delivery from becoming an unbounded demo loop.

---

## 3. Phase Index

| Phase | Name | One-Line Goal | Primary Gate | Page |
|-------|------|---------------|--------------|------|
| **0** | Discovery & Scoping | Understand the problem before touching a keyboard | Signed-off success criteria + architecture | [Phase 0 — Discovery & Scoping](00-discovery-and-scoping.md) |
| **1** | Data & Readiness Assessment | Know exactly what you have, what it lacks, and how fresh it is | Data readiness report with baseline metrics | [Phase 1 — Data & Readiness Assessment](01-data-readiness-assessment.md) |
| **2** | Infrastructure & Model Deploy | Stand up a reproducible inference stack that meets the SLO | Working endpoint passing load + health checks | [Phase 2 — Infrastructure & Model Deployment](02-infrastructure-and-model-deployment.md) |
| **3** | RAG / Agent Build | Build the application layer on top of the model | Working app integrated with customer systems | [Phase 3 — RAG / Agent Build](03-rag-agent-build.md) |
| **4** | Evaluation & Red-Teaming | Prove it works *and* prove it is safe | Eval thresholds met + red-team blockers closed | [Phase 4 — Evaluation & Red-Teaming](04-evaluation-and-red-teaming.md) |
| **5** | Go-Live & Handoff | Ship it, watch it, and transfer ownership | Production system + trained team + runbooks | [Phase 5 — Go-Live & Handoff](05-go-live-and-handoff.md) |
| **6** | Production Optimization | Keep quality up while latency and cost come down | Continuous eval green + monthly improvement cycle | [Phase 6 — Production Optimization](06-production-optimization.md) |

---

## 4. Phase Summary

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

## 5. Cross-Cutting Concerns

These are not phases — they run **through every phase** and are owned by someone in each phase. A phase that "completes" without them is not actually complete.

| Concern | Runs Through | Core Question |
|---------|--------------|---------------|
| **Evaluation** | Phases 0–6 | How do we know it is good, and how do we prove it to someone else? |
| **Safety, Security & Privacy** | Phases 0–6 | What is the worst thing this system can do, and is it prevented? |
| **Cost & Latency** | Phases 2–6 | What does one request cost, and does it land inside the SLO? |
| **Data & Index Lifecycle** | Phases 1, 3, 6 | Where does knowledge come from, and how do we know it is current? |
| **Observability** | Phases 2–6 | When a user reports a bad answer, can we reconstruct exactly what happened? |
| **Change Management** | Phases 0, 5, 6 | Who owns this system after we leave? |

### 5.1 Governance Gates

| Gate | When | Decision Owner | Question |
|------|------|----------------|----------|
| **Architecture approval** | End of Phase 0 | Exec sponsor + tech lead | Does the plan solve the stated problem at acceptable risk and cost? |
| **Data approval** | End of Phase 1 | Data owner + compliance | Is the data usable, lawful, fresh enough, and correctly scoped? |
| **Readiness review** | End of Phase 2 | Tech lead + platform owner | Is the stack reproducible, monitored, and inside the latency budget? |
| **Quality & safety sign-off** | End of Phase 4 | Product owner + security | Do eval thresholds hold and are red-team blockers closed? |
| **Go/No-Go** | End of Phase 5 | Exec sponsor + customer owner | Is production healthy and is the receiving team able to operate it? |

### 5.2 Non-Negotiables

- **Never ship without a golden set.** Without a regression baseline, every change is a gamble and every improvement is unprovable.
- **Never let prompt/index/model changes bypass eval.** Any change to prompt, chunking, embedding model, retrieval params, or model version is a release and must pass the same gate.
- **Never promise a latency/quality number that was not measured under load.** Lab numbers are not SLOs.
- **Never leave a phase without a named owner for the next phase.** Handoffs fail at ownership boundaries, not technical ones.

---

## 6. Maturity Track: POC → Pilot → Production

The 7 phases repeat at increasing fidelity across three maturity stages. Phase *content* stays the same; rigor and blast radius change.

| Aspect | POC (Proof of Concept) | Pilot | Production |
|--------|-------------------------|--------|------------|
| **Purpose** | Does it work at all? | Does it work well enough? | Does it meet SLOs reliably? |
| **Scope** | Single use case, narrow data | Real users, representative data | Full workload, SLAs |
| **Infrastructure** | Ephemeral, minimal | Pre-production, realistic | Production-grade, monitored |
| **Evaluation** | Informal, demo-driven | Structured eval, golden sets | Continuous eval, regression testing |
| **Success criteria** | "It answers the question" | Quality bar + latency bar | SLOs + cost + reliability + compliance |
| **Data** | Curated samples | Full corpus, live pipeline | Live pipeline + freshness SLAs |
| **Failure handling** | Manual intervention | Defined fallbacks | Automated fallback + alerting + runbook |
| **Who operates** | The builder | Builder + shadowing customer team | Customer team, FDE on-call |
| **Typical duration** | Days | Weeks | Ongoing |

**Gate between stages:** the POC→Pilot gate requires a written golden set and a quality number. The Pilot→Production gate requires eval thresholds met, load test passed, red-team blockers closed, monitoring live, and a rollback plan.

---

## 7. Roles & Responsibilities

| Role | Owns | Primary Phases |
|------|------|----------------|
| **Product Owner / Exec Sponsor** | Business problem, success criteria, budget, go/no-go | 0, 5 |
| **FDE / Solutions Engineer** | End-to-end delivery, architecture, eval design, customer interface | 0–6 |
| **ML Engineer** | Model selection, fine-tuning, embedding strategy, eval implementation | 2, 3, 4 |
| **Data Engineer** | Ingestion, parsing, chunking, index freshness, lineage | 1, 3, 6 |
| **Platform / SRE** | Infra, autoscaling, observability, SLOs, incident response | 2, 5, 6 |
| **Security / Privacy / Legal** | Threat modeling, data residency, compliance review | 0, 4, 5 |
| **Domain Expert / Customer SME** | Golden sets, rubric calibration, correctness judgment | 0, 4 |
| **Customer Engineering** | Integration, identity, deployment environment, run operations | 1, 2, 5, 6 |
| **Research / Model Team** | Model capability gaps, new model eval, safety research | 4, 6 |

---

## 8. Lifecycle Metrics

Targets are intentionally blank — fill them per engagement. The value is in writing them down **before** the build, not after.

| Phase | Metric | How Measured | Target |
|-------|--------|--------------|--------|
| 0 | Problem clarity score (SMEs agree on the ask) | Structured review |  |
| 0 | Success criteria agreed | Sign-off |  |
| 1 | Corpus usable-record ratio | Sampling audit |  |
| 1 | Document freshness | Ingestion timestamp vs. source |  |
| 1 | Baseline retrieval hit@k | Offline retrieval eval |  |
| 2 | Time to first token (p95) | Load test / tracing |  |
| 2 | Throughput (concurrent req/s) | Load test |  |
| 2 | Cold-start time | Deployment test |  |
| 3 | Answer faithfulness | LLM-as-judge on golden set |  |
| 3 | Tool-call success rate | Agent trace analysis |  |
| 4 | Task accuracy / answer correctness | Golden set vs. rubric |  |
| 4 | Hallucination rate | Groundedness eval |  |
| 4 | Injection resistance rate | Red-team suite |  |
| 4 | P95 latency at target concurrency | Load test |  |
| 4 | Cost per request | Token + infra accounting |  |
| 5 | Deployment lead time | Plan → live |  |
| 5 | Rollback readiness | Uncontested drill |  |
| 6 | User satisfaction (CSAT / thumbs) | Feedback signals |  |
| 6 | Weekly quality regression delta | Continuous eval |  |
| 6 | Cost per successful task | Accounting |  |
| 6 | Mean time to resolve | Incident log |  |

---

## 9. Common Failure Modes by Phase

| Phase | Symptom | Root Cause | Fix |
|-------|---------|------------|-----|
| 0 | "We built the wrong thing" | Problem stated as a feature, not a job | Rewrite the ask as a workflow + measurable outcome |
| 0 | Scope creep mid-build | No written exit criteria | Lock Phase 0 gate in writing |
| 1 | Retrieval returns nothing useful | Chunking ignores document structure | Re-chunk by semantic/section boundaries |
| 1 | Index goes stale silently | No freshness SLAs | Add ingestion freshness metric + alert |
| 2 | Latency spikes under load | No batching, no autoscaling policy | Tune batching, set HPA + queue |
| 2 | GPU cost exceeds model API cost | Self-hosting a workload that fits an API | Recompute cost/req; prefer managed API |
| 3 | Agent loops / burns tokens | No step budget or termination guard | Add max-steps, timeout, and cost caps |
| 3 | Answers cite wrong sections | Hybrid retrieval without reranking | Add cross-encoder reranking |
| 4 | "It looked good in the demo" | No golden set; eval by vibes | Curate 100+ representative cases |
| 4 | Judge disagrees with humans | Uncalibrated judge | Calibrate judge against human labels |
| 5 | Production alerts nobody owns | Handoff without on-call roster | Name owner + escalation path per alert |
| 6 | Quality decays after launch | Corpus + prompt drift, no continuous eval | Schedule eval + re-index cadences |
| 6 | Bill shock | Unbounded context / no caching | Add caching, routing, and token budgets |

---

## 10. Navigation

- **Start here:** Phase 0 — [Discovery & Scoping](00-discovery-and-scoping.md)
- **Related reference:** [FDE-Responsibilities.md](../FDE-Responsibilities.md) — role definition, pillar-by-pillar task breakdown, skills matrix, and career framing this lifecycle is derived from.
- **Phase pages:** [0](00-discovery-and-scoping.md) · [1](01-data-readiness-assessment.md) · [2](02-infrastructure-and-model-deployment.md) · [3](03-rag-agent-build.md) · [4](04-evaluation-and-red-teaming.md) · [5](05-go-live-and-handoff.md) · [6](06-production-optimization.md)

---

*This is a living reference. Update gates, metrics, and thresholds as the models, tooling, and your own field experience evolve.*

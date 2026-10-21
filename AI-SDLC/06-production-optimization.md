# Phase 6 — Production Optimization

[← AI SDLC index](AI-SDLC.md) · **Phase 6 of 7** · Previous: [Phase 5 — Go-Live & Handoff](05-go-live-and-handoff.md) · Next: [AI SDLC index](AI-SDLC.md)

**Goal:** Keep quality from decaying while latency and cost come down — and turn production signal into a repeatable improvement loop.

**Exit criterion:** Continuous eval green against the golden baseline, an active improvement cycle with owners, and a documented feedback path from production to product and model teams.

---

## 6.1 Why This Phase Exists

Launch is the midpoint, not the finish. AI systems decay in ways traditional software does not, because their inputs change without a deploy: the corpus drifts, user phrasing shifts, model providers update their endpoints, and the business changes around the tool. A system that scored 88% at launch and 71% six weeks later is not broken — it is unmonitored.

Three failure patterns dominate:
1. **Silent decay.** Nothing breaks technically; answers just get worse. Without continuous evaluation, the first signal is a customer complaint.
2. **Cost creep.** Latency and spend grow as usage grows, because every new feature adds context, every request uses the biggest model, and nothing forces efficiency.
3. **Feedback that dies.** Problems get described in Slack and never reach a backlog, an owner, or the model team.

Phase 6 converts production into a **feedback engine**: measure → attribute → fix → verify → ship → measure again.

**The question this phase answers:** *is the system still good, and is it getting better for the right reasons?*

---

## 6.2 Objectives

- Maintain quality against a live baseline with continuous evaluation
- Detect and attribute degradation fast (corpus, prompt, model, traffic, dependencies)
- Optimize latency and cost without quality regression
- Run a disciplined incident and improvement process
- Capture user feedback as structured, actionable signal
- Feed field learnings to product and model teams
- Maintain the corpus and index freshness as a scheduled discipline
- Plan for model/provider changes and deprecations

---

## 6.3 Entry Criteria

- [ ] Phase 5 exit criteria met (production live, monitored, handed off)
- [ ] Continuous eval pipeline running against production samples
- [ ] Baseline metrics recorded from Phase 4 for comparison
- [ ] On-call and incident process live
- [ ] Improvement backlog exists with owners and a review cadence
- [ ] Feedback capture mechanism shipped (in-product or operational)

---

## 6.4 Key Activities

### A. Continuous evaluation & monitoring

- **Production eval:** sample real traffic (anonymized), score with the calibrated judge, track metrics as a time series
- **Golden regression:** nightly run of the golden set; a drop is a release-blocking signal for the next change
- **Drift signals:** input distribution shift (query length, topic mix, language), output distribution change (refusal rate, answer length, citation rate), retrieval score distribution
- **Retrieval monitoring:** hit@k proxies on live queries, score distributions, no-result rate, chunk coverage, index freshness
- **Business metrics:** adoption, task completion, escalation rate, CSAT, deflection — quality in the language the business uses
- **Synthetic probes:** a fixed question set run on a schedule to detect silent breakage between deploys

**Alerting on quality:** thresholds should be relative to the baseline (e.g. faithfulness < baseline − 5 points over 24h), not absolute, so they survive model upgrades.

### B. Debugging & root cause analysis

| Symptom | Investigate |
|---------|-------------|
| Quality dropped at a timestamp | Deploy timeline: prompt, model, retrieval config, index, dependencies |
| Quality dropped gradually | Corpus drift, embedding version mismatch, embedding model provider change, upstream source format change |
| Some queries fail, most work | Filter/tenant-specific path, long-context cases, specific source documents, tool failure subset |
| Latency rose | Provider degradation, context growth, index slowdown, autoscaling saturation, tool latency, retry amplification |
| Cost rose | Context growth, model mix drift, caching hit-rate drop, agent step inflation, retry rate |
| Answers cite old content | Index freshness failure, CDC gap, dedupe policy picking the wrong version |

**Method:** reproduce from a trace (Phase 3/5 instrumentation pays off here), bisect the timeline, change one variable, re-measure. Use the golden set as the control group.

### C. Incident management

- Severity model (Sev-1: safety/data/availability; Sev-2: major quality degradation; Sev-3: single-feature issue)
- Incident process: detect → triage → mitigate (rollback first, diagnose later) → communicate → resolve → postmortem
- Mitigations before root cause: rollback prompt/model/config, disable a feature flag, shed traffic, increase abstention threshold, switch to a fallback model
- Blameless postmortem focused on system conditions, not individuals; every action item must be an owned, dated change to the product, process, or monitoring
- Feed postmortem action items back into the backlog and into the deployment checklist

### D. Latency optimization

| Lever | Effect | Risk |
|-------|--------|------|
| Prompt compression | Fewer tokens → faster prefill | Drops instructions; re-eval required |
| Context reduction (fewer chunks, better filtering) | Fewer tokens, better signal | Lower recall if over-tuned |
| Smaller model for simple queries | Large latency and cost win | Quality drop on the hard tail — measure by tier |
| Caching (exact + semantic) | Near-zero latency for repeats | Stale or wrong-hit answers; scope carefully |
| Streaming | Lower perceived latency | No effect on total latency |
| Batch mode | Throughput up | Latency up; async workloads only |
| Prefix caching | Faster prefill on shared prefixes | Requires stable prefixes; infra support |
| Speculative decoding | Faster decode | Setup cost, model-dependent gains |
| Parallel tool calls | Lower agent wall time | More load on downstream systems |
| Precompute / pre-embed | Removes embedding latency from the request path | Staleness of precomputed artifacts |

Order of operations: measure → attribute tokens/time to a stage → change one lever → re-eval quality and latency together. Never take a latency win that silently costs quality.

### E. Cost optimization

- **Cost model:** cost per request = (input tokens × input price + output tokens × output price) + infra + vector DB + observability
- **Cost per successful task** is the metric that matters — a cheap request that fails and gets retried is not cheap
- Levers: model routing by task complexity, prompt compression, caching, batch for offline workloads, context budget caps, max output tokens caps, cheaper embedding model, auto-scaling floors, spot/preemptible for non-latency-critical jobs, trace off-sampling
- Quotas and budgets per tenant/team; alert on spend anomaly and cost-per-request regression
- Log cost per request, per tenant, and per workflow step — attribution is what makes optimization possible

### F. Corpus & index maintenance

- **Freshness SLAs** per source class, enforced by alerts
- Scheduled incremental re-index; full rebuild cadence for low-change corpora
- **Dedup & version policy review**: prefer-latest rules, removal of superseded documents
- **Coverage audits**: new source systems, new document types, orphan documents, embedding-model migrations
- **Index health checks**: vector count, dimension match, null/empty chunks, deleted-but-still-present documents
- **Chunking re-tuning** as the corpus changes; every re-chunk requires a full re-embed and re-eval

### G. Model & provider lifecycle

- Track deprecation notices, version changes, price changes, and behavior changes from providers
- **Always re-eval a new model version against the golden set before accepting it** — provider "improvements" frequently regress domain-specific behavior
- Canary new versions on production traffic; compare quality, latency, cost, and refusal behavior
- Keep a fallback model ready and tested; provider outages are a scheduled risk, not a surprise
- Track model inventory, versions, and contracts for audit

### H. Feedback loops & continuous improvement

| Signal | Source | Route |
|--------|--------|-------|
| Thumbs up/down, CSAT | In-product | Feedback store → backlog triage |
| Corrections & rewrites | User editing output | Sampled review → backlog |
| Regeneration rate | Product analytics | Implicit dissatisfaction signal |
| Escalations to humans | Support | Root-cause review → backlog or capability gap |
| Support tickets | Support system | Categorized → backlog + corpus gap analysis |
| Silent abandonment | Funnel analytics | UX or quality gap |
| Field failure cases | FDE + support | Permanent addition to the golden set |
| Model weaknesses observed | Engineering | Report to Research/Model team with evidence |
| Feature gaps | Customer + support | Product backlog with evidence |

**Cadence:** weekly quality review (metrics, incidents, top failure modes), monthly improvement planning (prioritized backlog), quarterly golden-set refresh, and a monthly customer-facing performance report.

### I. Field → product → model feedback

- Collect structured, anonymized field data: failure modes, edge cases, capability gaps, latency bottlenecks, cost pain points
- Convert friction into evidence-backed feature requests (problem, frequency, impact, proposed solution)
- Partner with the model team: share eval sets and failure labels so capability gaps are reproducible
- Document repeatable patterns in the deployment playbook so the next engagement is faster

### J. Capacity planning & preventive maintenance

- GPU/storage/vector DB growth forecasting; scale thresholds and lead times
- Dependency and version upgrade cadence (scheduled, tested, not reactive)
- Security patch cadence; certificate and secret rotation
- Cache clearing, index rebuilds, backup/restore verification
- Disaster recovery drills for the AI stack specifically: index rebuild time, model cold start, degraded-mode behavior

---

## 6.5 Inputs → Outputs

| Input | Source | Output | Consumer |
|-------|--------|--------|----------|
| Production traffic | Phase 5 | Continuous eval signal, drift detection | Improvement cycle |
| Golden set + harness | Phase 4/5 | Regression baseline | Every change |
| Incident records | Phase 6 | Postmortems with owned actions | Backlog, checklist |
| Cost & latency telemetry | Phase 2/5 | Optimization backlog | Platform, finance |
| User feedback | Product | Prioritized improvement backlog | Product owner |
| Model version changes | Provider | Re-eval results, upgrade decision | Model team |

---

## 6.6 Artifacts & Templates

| Artifact | Purpose | Template Sketch |
|----------|---------|-----------------|
| **Production scorecard** | The health of the system | Metric \| baseline (Phase 4) \| current \| 30d trend \| target \| status |
| **Drift report** | What changed and why | Signal \| expected \| observed \| onset \| suspected cause \| action |
| **Incident report** | Postmortem | Summary \| impact \| timeline \| root cause \| mitigation \| actions (owner/date) \| learnings |
| **Optimization backlog** | Latency & cost work | Item \| lever \| expected latency win \| expected cost win \| quality risk \| eval result \| owner \| status |
| **Model upgrade evaluation** | Compare versions | Model \| metric \| old \| new \| delta \| verdict \| rollout plan |
| **Index health report** | Corpus integrity | Metric \| value \| expected \| status \| last refresh \| next refresh |
| **Feedback log** | Structured user signal | Date \| source \| category \| summary \| frequency \| severity \| routed to \| status |
| **Field learnings / ADR updates** | Compound knowledge | Learning \| pattern \| applicability \| playbook update |
| **Monthly customer report** | Shared accountability | Metrics \| wins \| issues \| actions taken \| roadmap \| support asks |

---

## 6.7 Tooling Reference

| Need | Options |
|------|---------|
| Production eval | Braintrust, LangSmith, Langfuse, Arize Phoenix, Galileo, W&B, Arize AX |
| Drift detection | Evidently, NannyML, whylogs, Great Expectations, deepchecks |
| LLM observability | Langfuse, Arize Phoenix, Helicone, Braintrust, Datadog LLM Observability, AWS CloudWatch GenAI |
| Cost tooling | LiteLLM proxy, OpenLLMetry, ccusage, Langfuse cost tracking, cloud cost explorer |
| Caching | Redis, GPTCache, LiteLLM cache, semantic cache (Redis + embeddings) |
| Model routing | LiteLLM, RouteLLM, Portkey, OpenRouter |
| Prompt management | Langfuse, promptfoo, Agenta, Git + CI |
| Feature flags | LaunchDarkly, Unleash, OpenFeature, Flagsmith |
| Incident management | incident.io, PagerDuty, Jira Service Management, incident.io timelines |
| Feedback capture | Langfuse feedback, Argilla, Label Studio, in-product widgets |
| Quality dashboards | Grafana, Metabase, custom, Hex/Deepnote notebooks |

---

## 6.8 Metrics for This Phase

| Metric | How Measured | Target |
|--------|--------------|--------|
| Production quality vs. Phase 4 baseline | Continuous eval | Within agreed band |
| Golden set regression | Nightly run | 0 regressions |
| Quality alert rate / false positive rate | Alert log |  |
| Mean time to detect quality degradation | Alert → ack |  |
| Mean time to resolve (Sev-1/2) | Incident log |  |
| Freshness SLA compliance | Index monitoring |  |
| p95 latency | Production tracing |  |
| Cost per request | Accounting |  |
| Cost per successful task | Accounting |  |
| Cache hit rate | Cache metrics |  |
| Routing distribution (model mix) | Routing logs |  |
| Golden set growth from field failures | Set changelog |  |
| Feedback items actioned within SLA | Backlog |  |
| Incident action items closed on time | Action tracker |  |
| Adoption / active users | Product analytics |  |
| CSAT | Feedback survey |  |

---

## 6.9 Exit Criteria — Steady State (This Phase Has No Exit)

Phase 6 does not end. It is the operating mode. "Completion" means the following are true and *sustainably* true:

- [ ] Continuous eval runs automatically and compares against the Phase 4 baseline
- [ ] Quality, latency, cost, and freshness alerts fire to named owners
- [ ] Every incident has a postmortem with owned, dated action items
- [ ] Field failures are permanently added to the golden set
- [ ] A weekly quality review and a monthly improvement planning cycle are running
- [ ] Latency and cost optimizations are shipped with paired quality measurements
- [ ] Model/provider changes are re-evaluated before adoption
- [ ] Index freshness SLAs are met and enforced by alerts
- [ ] Feedback is captured, categorized, and routed with response SLAs
- [ ] Field learnings are fed to product and model teams with reproducible evidence
- [ ] Capacity plans and DR drills are current

---

## 6.10 Common Failure Modes & Anti-Patterns

| Symptom | Root Cause | Fix |
|---------|------------|-----|
| Quality decayed, discovered by users | No continuous eval | Sample production traffic nightly; alert vs. baseline |
| Index stale, answers out of date | No freshness SLA | Per-source freshness SLA + alert + scheduled re-index |
| Embedding mismatch, all retrieval broken | Model re-deployed without re-index | Record embedding version per chunk; block deploy on mismatch |
| Cost doubled over a quarter | Context growth, no routing, no caching | Token budget caps, routing, caching, cost-per-request alerting |
| Latency crept up | Prefix changed, caching disabled, provider slower | Track TTFT vs. decode separately; re-enable prefix caching |
| Provider upgrade broke quality | Accepted without re-eval | Mandatory golden-set re-eval + canary before promotion |
| New model version ignored by agent | Tool schema drift | Contract-test tools against the new version |
| Golden set becomes unrepresentative | Never refreshed | Quarterly refresh; sample from live traffic |
| Backlog grows, nothing ships | No prioritization or capacity | WIP limits, monthly planning, time-boxed optimization sprints |
| Feedback lost in Slack | No capture path | Structured feedback store with routing and SLAs |
| "AI is not accurate enough" | Symptom, not diagnosis | Run attribution: retrieval vs. prompt vs. model vs. tool |
| Postmortems without actions | Actions not owned or tracked | Every action has an owner, a date, and a ticket |

---

## 6.11 Role Breakdown

| Role | Responsibility in Phase 6 |
|------|----------------------------|
| **FDE** | Owns the improvement loop, debugging, field feedback, customer relationship, playbook updates |
| **ML Engineer** | Quality attribution, model upgrade evaluation, reranking/prompt optimization, optimization experiments |
| **Data Engineer** | Corpus freshness, index health, re-chunking, coverage audits, embedding migrations |
| **Platform / SRE** | Capacity, cost control, autoscaling, incidents, dependency upgrades, DR |
| **Product Owner** | Prioritization, adoption, roadmap, user communication |
| **Customer Ops / Support** | First-line incident response, ticket categorization, user feedback quality |
| **Security / Compliance** | Patch cadence, secret rotation, audit trail, drift of data handling |
| **Research / Model Team** | Receives reproducible failure cases, capability gap triage, new model eval |
| **Customer Engineering** | Owns integration changes, credential rotation, environment changes |

---

## 6.12 Practical Field Notes

- **Alert on deltas, not absolutes.** Providers change models, corpora change, baselines shift. Relative thresholds survive upgrades; absolute ones cause alert fatigue.
- Keep the golden set frozen and *grow* it. Editing existing cases to make a run pass is how teams lose their ability to detect regressions.
- Every production failure should become a permanent test. Over a quarter, that backlog is worth more than any single optimization.
- Measure cost per **successful** task. Per-request cost rewards cheap, wrong answers.
- Do quality and latency optimizations **together**. Shipping them separately means you never learn which change hurt quality.
- Set a hard token budget per request and per agent run. It is the cheapest reliability control in AI systems.
- Keep a **fallback model** warm and tested. Provider outages are the most likely 3am page.
- Ship small, often, and gate everything with the golden suite. High release frequency with fast eval is how AI teams stay fast without getting worse.
- Write the monthly report honestly, including regressions. Trust survives bad news; it does not survive surprises.

---

## 6.13 Related Pages

- [AI SDLC index](AI-SDLC.md)
- Previous: [Phase 5 — Go-Live & Handoff](05-go-live-and-handoff.md)
- Loop back to: [Phase 0 — Discovery & Scoping](00-discovery-and-scoping.md) — new requirements start the cycle again
- Source reference: [FDE-Responsibilities.md — Pillars E, F, and G](../FDE-Responsibilities.md) and [§11 Personal Reflection / Customization](../FDE-Responsibilities.md)
- **Deeper dive:** [FDE-Responsibilities.md Pillar E](../FDE-Responsibilities.md) expands the 20 production-operations tasks as step-by-step procedures with templates, the same five worked business cases (Northwind Mutual, Meridian Bank, Corvid, St. Aldwyn Health, Halden & Rowe), and thirteen publicly documented reference cases.
- **Deeper dive:** [FDE-Responsibilities.md Pillar G](../FDE-Responsibilities.md) expands the 12 feedback-loop tasks — structured field data, feature-request capture, model weakness records, permissioned eval datasets, research partnership on evaluation design, roadmap input, model evaluation cycles, repeatable field patterns, evidence-backed feature requests, platform gap analysis, anonymized team sharing, and product design reviews — as step-by-step procedures with the same five worked business cases and fifteen publicly documented reference cases.

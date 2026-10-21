# Phase 5 — Go-Live & Handoff

[← AI SDLC index](AI-SDLC.md) · **Phase 5 of 7** · Previous: [Phase 4 — Evaluation & Red-Teaming](04-evaluation-and-red-teaming.md) · Next: [Phase 6 — Production Optimization](06-production-optimization.md)

**Goal:** Ship the system to real users safely, make sure it is watched, and transfer genuine operational ownership to the customer team.

**Exit criterion:** Production system serving live traffic under monitoring, alerts routed to named owners, rollback proven, and the receiving team demonstrated able to operate it unaided.

---

## 5.1 Why This Phase Exists

Go-live is where the *project* ends and the *system* begins. The most common failure at this boundary is not technical — it is that "handoff" meant a document was uploaded and a meeting was held, while the knowledge of how the thing actually works stayed with the people who built it.

Three specific hazards:
1. **Unmonitored production.** Traffic arrives, no one is watching, the first quality or latency problem is discovered by a customer, not an alert.
2. **Unowned alerts.** Alerts route to a mailbox nobody reads. This creates false confidence and alert fatigue simultaneously.
3. **Undemonstrated handoff.** The customer's team has never debugged it, never rolled it back, never tuned it. At 2am they will call you — and that is not a handoff, it is a permanent dependency.

**The question this phase answers:** *can this system run without me, and will someone notice if it stops working?*

---

## 5.2 Objectives

- Deploy to production with zero/low user disruption
- Activate full observability: metrics, traces, logs, dashboards, alerts
- Define and enforce production SLOs
- Prove rollback works before you need it
- Transfer operational ownership: runbooks, training, on-call
- Establish the operational cadence: reviews, feedback, improvement backlog
- Complete compliance and security artifacts

---

## 5.3 Entry Criteria

- [ ] Phase 4 exit criteria met (quality + safety sign-off obtained)
- [ ] Production environment provisioned with parity to the validated environment
- [ ] Secrets, identity, and network paths verified in production
- [ ] Dashboards and alerts configured (not just installed)
- [ ] Runbooks written and reviewed
- [ ] On-call roster populated with trained responders
- [ ] Customer team scheduled for training and knowledge transfer

---

## 5.4 Key Activities

### A. Release planning

- **Release strategy:** canary (1% → 10% → 50% → 100%) vs. blue/green vs. direct cutover. Use canary when a rollback path exists and quality is judgeable per response.
- **Release record:** what is deployed — model version, prompt version, retrieval config, index version, guardrail config, code version. All of it, in one place.
- **Traffic plan:** internal users → friendly customers → everyone, with a defined hold point between each
- **Communication plan:** who tells whom what, and what the user-facing message says
- **Freeze window:** no unrelated changes during the initial stabilization period
- **Explicit abort criteria:** rollback triggers written down *before* launch (error rate, latency, quality drop, safety event)

### B. Production deployment

- Deploy via the same CI/CD path used in Phase 2 — no manual steps, no "works on my machine"
- Promote the exact artifacts validated in Phase 4 (image digest, model version, prompt version)
- Migrate the index: either re-embed into the production index or promote the validated one; verify count and spot-check content
- Validate production configuration parity: model ID, region, endpoint type (private vs. public), timeout, retry, max_tokens, temperature, safety settings
- Verify authn/authz, tenant isolation, and rate limits in production configuration
- Confirm PII redaction is active in logs and traces (production often logs more than staging — check)

### C. Observability activation

| Layer | What Must Be Live Before Traffic |
|-------|-----------------------------------|
| **Dashboards** | Request rate, error rate, TTFT p50/p95/p99, end-to-end latency, token usage, cost/day, quality metrics, tool-call success, agent step distribution |
| **Alerts** | Latency breach, error rate breach, quality regression, cost anomaly, guardrail spike, index freshness breach, tool failure rate, model fallback triggered |
| **Logs** | Structured, correlated by request ID, with sensitive-field redaction |
| **Traces** | End-to-end per request, with retrieval and tool spans retained long enough to debug |
| **Synthetic checks** | Periodic automated probe (fixed question set) that alerts if the system degrades silently |
| **Business alerting** | Escalation volume, deflection rate, adoption, CSAT — the signals the business actually cares about |

Every alert needs: a threshold that is meaningful, an owner, a severity, a runbook link, and a route that reaches a human. An alert without an owner is a log line.

### D. SLOs and error budgets

- Define SLOs from the Phase 0 targets: availability, latency, quality, cost
- Compute the error budget: allowed downtime / bad responses per period
- Define burn-rate alerts (multi-window, multi-burn-rate) so you alert on *how fast* you are consuming the budget, not just when it is gone
- Define the policy: what happens when the budget is exhausted (feature flags off? traffic shed? freeze releases?)

### E. Rollback and release safety

- **Prove it:** trigger a real rollback in production (or a production-like environment) before launch — not just the runbook
- Rollback paths: code version, prompt version, model version, retrieval config, index version, guardrail config. Ideally each is independently switchable via feature flag
- Canary analysis comparing candidate vs. baseline on quality and cost, per request
- Automatic rollback triggers wired to alerts

### F. Documentation & runbooks

| Document | Purpose |
|----------|---------|
| Architecture overview | How the system works, one page, current (not the Phase 0 design) |
| Runbook: common failures | Symptom → diagnosis → fix → escalation |
| Runbook: rollback | Exact steps, triggers, verification |
| Runbook: model/index refresh | How to swap a model or re-index safely |
| Operations guide | Dashboards, alerts, what they mean, who to page |
| Troubleshooting guide | Common errors and fixes with real commands |
| API reference | Endpoints, schemas, errors, rate limits |
| Cost guide | How spend is computed, how to reduce it |
| Compliance pack | Data flows, residency, PII handling, model inventory, risk register |

### G. Knowledge transfer & training

- **Hands-on sessions, not slide decks:** the customer team reproduces a deployment, tunes a prompt, reads a trace, and fixes an alert
- **Train the failure path first:** give them a deliberately broken build and let them diagnose it
- **Shadow the on-call:** customer team watches live alerts for a week, then takes the pager
- **Certification:** a checklist that the receiving team demonstrably completes — deploy a prompt change, run the eval suite, interpret a dashboard, execute a rollback
- **Escalation path:** to whom and how fast, with a defined response time, and when to involve the vendor

### H. Handoff & ownership transfer

| Item | From | To | Evidence |
|------|------|----|----------|
| Infrastructure | FDE | Customer platform | Access granted, documented, tested by them |
| Model endpoints | FDE | Customer ML | Keys/roles rotated to customer-managed |
| Golden set & eval harness | FDE | Customer ML | Harness runs in their CI, they own the thresholds |
| Dashboards & alerts | FDE | Customer ops | Alert routing tested end to end |
| Runbooks | FDE | Customer ops | Reviewed and signed off by customer ops |
| Backlog & roadmap | FDE | Product owner | Improvement backlog seeded with known items |
| Incident process | FDE | Customer ops | On-call roster live, escalation path published |

**Ownership is not transferred when a document is delivered.** It is transferred when the receiving team demonstrates the capability.

### I. Compliance & audit close-out

- Final data-flow review against what was approved in Phase 0/1
- Model inventory record: which models are used, where, for what, under what agreement
- Privacy impact assessment (DPIA) if in scope
- Vendor/subprocessor confirmation for anything reaching a third-party API
- Audit trail for prompt and configuration changes (who changed what, when, why)
- Retention policies applied to traces, logs, and eval data

### J. Launch communication & stabilization

- Stakeholder demo with live interaction and honest caveats
- Support team briefed: what the AI does, what it doesn't, how to escalate
- End-user enablement: in-product guidance, expectations, feedback channel
- **Stabilization period** (1–2 weeks): heightened monitoring, daily quality review, fast rollback readiness, all changes frozen except fixes

---

## 5.5 Inputs → Outputs

| Input | Source | Output | Consumer |
|-------|--------|--------|----------|
| Validated build | Phase 4 | Production deployment | Phase 6 |
| Eval results | Phase 4 | Quality baseline for production comparison | Phase 6 |
| SLO targets | Phase 0 | SLOs + error budget policy | Phase 6 |
| Runbooks (draft) | Phase 3/4 | Reviewed runbooks | Customer ops |
| Training material | Phase 3 | Knowledge transfer session | Customer team |
| Compliance requirements | Phase 0/1 | Compliance pack | Legal/audit |

---

## 5.6 Artifacts & Templates

| Artifact | Purpose | Template Sketch |
|----------|---------|-----------------|
| **Release plan** | What happens when | Strategy \| traffic ramp \| hold points \| abort criteria \| comms \| freeze |
| **Release record** | Exactly what shipped | Version matrix: code \| model \| prompt \| embedding \| chunking \| top-k \| guardrails \| index |
| **Go-live checklist** | Nothing missed | Item \| owner \| status \| evidence \| sign-off |
| **SLO & error budget sheet** | Reliability contract | SLO \| target \| window \| budget \| burn alerts \| exhaustion policy |
| **Alert catalog** | Every alert is owned | Alert \| condition \| severity \| threshold \| route \| owner \| runbook link |
| **Dashboard index** | Where to look | Dashboard \| audience \| panels \| refresh \| notes |
| **Runbook** | Operational procedure | Symptom/alert → impact → diagnosis steps → fix → verification → escalation |
| **Training curriculum + sign-off** | Prove capability | Session \| topics \| hands-on task \| attendee \| assessed? |
| **Handoff checklist** | Ownership transfer | Item \| from \| to \| evidence \| accepted by \| date |
| **Stabilization report** | First weeks | Date \| incidents \| metric deltas \| changes made \| decisions |

---

## 5.7 Tooling Reference

| Need | Options |
|------|---------|
| Deployment | GitHub Actions, ArgoCD, Flux, Harness, Spinnaker, Argo Rollouts |
| Progressive delivery | Argo Rollouts, Flagger, LaunchDarkly, Istio traffic splitting |
| Feature flags | LaunchDarkly, Unleash, OpenFeature, Flagsmith, env-config flags |
| Dashboards | Grafana, Datadog, New Relic, Chronosphere, Datadog LLM Observability |
| Alerting | Prometheus Alertmanager, PagerDuty, Opsgenie, Grafana OnCall, incident.io |
| Synthetic monitoring | Blackbox Exporter, Checkly, Datadog Synthetics, custom probes |
| Runbooks | Confluence/Notion runbook pages, GitBook, Backstage TechDocs, Rundeck |
| Error budgets | Sloth, Pyrra, Nobl9, Google SRE workbook sheets |
| Training | Internal wiki + recorded sessions, hands-on labs, pair sessions |
| Incident management | incident.io, PagerDuty, Jira Service Management |

---

## 5.8 Metrics for This Phase

| Metric | How Measured | Target |
|--------|--------------|--------|
| Release lead time (approval → live) | Plan vs. actual |  |
| Rollback time | Drill |  |
| Rollback drill success rate | Drills run | 100% |
| Alert coverage (SLOs with alerts) | Catalog audit | 100% of SLOs |
| Alert ownership | Catalog audit | 100% named |
| False-positive rate on quality alerts | Alert log |  |
| Mean time to acknowledge | PagerDuty/incident log |  |
| Training completion | Curriculum sign-off | 100% of receiving team |
| Handoff items accepted with evidence | Handoff checklist | 100% |
| Incident count in stabilization window | Incident log |  |
| Change freeze violations | Change log | 0 |
| Adoption in first 30 days | Active users |  |

---

## 5.9 Exit Criteria — Gate to Phase 6

- [ ] Production deployed with the exact validated artifacts; release record published
- [ ] Traffic ramp completed with abort criteria defined and respected
- [ ] Dashboards live and reviewed with the customer team
- [ ] All P0/P1 alerts firing to named owners, tested by triggering each one
- [ ] SLOs and error budget policy signed off
- [ ] Rollback exercised successfully in a production-like environment; triggers wired
- [ ] Runbooks written, reviewed, and linked from every alert
- [ ] Receiving team completed hands-on training and demonstrated: deploy a prompt change, run the eval suite, read a dashboard, execute a rollback
- [ ] Handoff checklist complete with customer sign-off on each item
- [ ] Compliance pack final; model inventory recorded
- [ ] Stabilization period complete with no unresolved Sev-1/Sev-2
- [ ] Support and end-user enablement delivered
- [ ] Improvement backlog seeded with known items and owners

> **Gate decision owner:** executive sponsor + customer system owner. If the customer cannot operate it, you have not handed off — you have deployed.

---

## 5.10 Common Failure Modes & Anti-Patterns

| Symptom | Root Cause | Fix |
|---------|------------|-----|
| Alert fired but nobody responded | No owner, no route | Named owner + tested route per alert |
| Rollback needed, but impossible | Rollback never rehearsed | Drill rollback in Phase 2 and again pre-launch |
| Quality dropped after launch, discovered by users | No production eval / synthetic probes | Continuous eval + synthetic check on a fixed question set |
| Support can't answer user questions about the AI | End-user comms skipped | Brief support with scope, limits, escalation path |
| Handoff claimed, customer still calls the vendor for everything | Capability not demonstrated | Hands-on certification checklist; shadow then own |
| Index in production differs from validated | Prod re-embed differed | Promote the validated index; verify counts and spot-check |
| Prod slower than staging | Config drift (region, endpoint type, timeouts) | Config parity check as a deploy step |
| Cost spikes after launch | No budget alert, no routing | Cost alert + routing + token caps per request |
| Too many changes during stabilization | No freeze | Enforce freeze; fixes only, via change record |
| Secrets still with the builder | Access not rotated | Rotate and hand over credentials; revoke builder access |

---

## 5.11 Role Breakdown

| Role | Responsibility in Phase 5 |
|------|----------------------------|
| **FDE** | Deploys, verifies, documents, trains, and stays on call through stabilization |
| **Platform / SRE** | Production deployment, dashboards, alerts, SLO setup, rollback mechanics |
| **Customer Ops / SRE** | Receives monitoring, validates runbooks, takes the on-call rotation |
| **Customer ML / Data** | Receives the golden set, eval harness, prompt registry, model access |
| **Product Owner** | Go/no-go, user comms, adoption plan |
| **Support Lead** | Briefing, escalation handling, feedback capture |
| **Security / Legal** | Final compliance review, sign-off on residual risk |
| **Executive Sponsor** | Go/no-go, stakeholder communication, expectation setting |

---

## 5.12 Practical Field Notes

- **Test the alert path, not just the alert.** Trigger each P1 alert and confirm a human receives it in under the agreed window.
- Do the rollback drill in the production-like environment with the customer watching. It converts anxiety into capability.
- Hand over the **eval harness**, not just the eval results. If they cannot re-run it, the system will silently rot.
- Freeze changes for the first two weeks. Every incident in week one is usually a config or integration issue introduced during the build, and a freeze surfaces them faster.
- Give the receiving team a real (small) incident to debug during training. A clean environment teaches nothing.
- Publish one "what this system does not do" page to users. It reduces support load more than any amount of accuracy tuning.
- Keep the builder's access **open but audited** for the first month, then revoke. Revoking early breaks support; revoking late creates an audit finding.
- Record the first 30 days of production quality against the Phase 4 baseline. That delta is the most valuable report you will deliver post-launch.

---

## 5.13 Related Pages

- [AI SDLC index](AI-SDLC.md)
- Previous: [Phase 4 — Evaluation & Red-Teaming](04-evaluation-and-red-teaming.md)
- Next: [Phase 6 — Production Optimization](06-production-optimization.md)
- Source reference: [FDE-Responsibilities.md — Pillar D (go-live) and Pillar F (knowledge transfer)](../FDE-Responsibilities.md)
- **Deeper dive:** [FDE-Responsibilities.md Pillar D](../FDE-Responsibilities.md) expands the go-live tasks — acceptance testing, canary rollout, stakeholder demos, SLOs, customer sign-off, and the go-live gate — as step-by-step procedures with the five worked business cases and twelve publicly documented reference cases.

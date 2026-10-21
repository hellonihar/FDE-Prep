# Phase 4 — Evaluation & Red-Teaming

[← AI SDLC index](AI-SDLC.md) · **Phase 4 of 7** · Previous: [Phase 3 — RAG / Agent Build](03-rag-agent-build.md) · Next: [Phase 5 — Go-Live & Handoff](05-go-live-and-handoff.md)

**Goal:** Turn "it works" into measurable evidence — a scored evaluation suite, a passed safety bar, and proven performance under load.

**Exit criterion:** Eval thresholds met against the golden set, all high-severity red-team findings closed, load test passed, and results documented in a form the customer can act on.

---

## 4.1 Why This Phase Exists

The demo is the single most misleading artifact in AI delivery. It is curated, short, in-domain, and performed by the person who built the system. Production is none of those things. The gap between the demo and production is where every incident lives.

Phase 4 is the discipline of replacing confidence with measurement. It answers three questions:

1. **Quality:** how good is it, measured against cases someone with domain expertise has judged correct?
2. **Safety:** what is the worst it can do, and have we closed the dangerous paths?
3. **Performance:** does it hold up under real load, at acceptable cost?

Without this phase, "quality" is a matter of opinion, and the only way to discover a regression is for a user to report it in production.

**The question this phase answers:** *what is the evidence, and can the customer read it and agree?*

---

## 4.2 Objectives

- Build a golden evaluation set representative of real traffic
- Define metrics and thresholds per use case
- Implement an automated eval harness that runs on every change
- Calibrate LLM-as-judge against human judgment
- Red-team the system across known AI attack classes
- Load test latency, throughput, and cost under realistic load
- Produce a customer-facing evaluation report
- Obtain formal sign-off on quality and safety

---

## 4.3 Entry Criteria

- [ ] Phase 3 exit criteria met (working app, instrumented, integrated)
- [ ] Labeled sample set from Phase 1 available
- [ ] Target question set from Phase 0 available
- [ ] SMEs' calendar time blocked for golden-set curation
- [ ] Safety policy and threat model from Phase 0 available
- [ ] Success thresholds agreed (from Phase 0, or renegotiated explicitly)

---

## 4.4 Key Activities

### A. Golden set construction

The golden set is the backbone of everything downstream. Build it carefully.

| Property | Guidance |
|----------|----------|
| Size | 100–300 cases for a narrow use case; 500+ for broad or regulated |
| Coverage | Common cases, edge cases, multi-turn, unanswerable, adversarial-lite, long-context, multi-tenant |
| Source | Real (anonymized, permissioned) user queries — not synthetic imagination |
| Labels | Expected answer or expected behavior, written by domain SMEs |
| Rubric | For open-ended answers: what counts as correct, partial credit, and "must not say" constraints |
| Versioning | Frozen, versioned, with a changelog; never edited to make a run pass |
| Refresh | Reviewed every quarter; new failure cases are added permanently |

**Label formats** (pick per case type):
- **Exact/target answer** — for extraction, classification, and factual lookups
- **Acceptable-answer set** — multiple valid answers allowed
- **Rubric + LLM judge** — for explanatory answers
- **Required assertions** — must cite source X, must not mention Y, must escalate
- **Binary pass/fail** — for safety and policy cases

> Golden sets must include **unanswerable** questions. Without them you cannot measure abstention, and hallucination rate becomes unmeasurable.

### B. Metric design

| Metric | Applies To | Method |
|--------|-----------|--------|
| Answer correctness | All | Golden set vs. rubric |
| Faithfulness / groundedness | RAG | LLM judge: is every claim supported by the context? |
| Citation correctness | RAG | Citation resolves to a source that supports the claim |
| Retrieval hit@k / MRR / nDCG | RAG | Offline retrieval eval against labels |
| Context precision | RAG | Share of retrieved chunks that were relevant |
| Abstention accuracy | All | On unanswerable cases, does it correctly refuse? |
| Hallucination rate | All | Unsupported claims / total claims |
| Instruction following | All | Constraint violations |
| Task completion | Agents | Did the goal state get reached? |
| Tool-call accuracy | Agents | Correct tool, correct args, correct order |
| Step efficiency | Agents | Steps vs. optimal path |
| Error recovery | Agents | Recovers from a failed tool call |
| Refusal correctness | All | Refuses what it should, complies with what it should |
| Toxicity / bias / PII leakage | All | Classifiers + red-team suite |
| Latency p50/p95/p99 | All | Load test + production tracing |
| Cost per request | All | Token accounting |

### C. LLM-as-judge implementation

- **Judge model choice:** stronger than the model under test, deterministic settings (temperature 0), and a judge that is not the same model you are evaluating
- **Rubric design:** explicit, binary or small-scale criteria; ask one question per criterion rather than a composite "rate 1–10"
- **Calibration:** label ~100 cases by hand, measure judge-human agreement (target high agreement on binary criteria), iterate the rubric until agreement is acceptable
- **Bias control:** shuffle answer order for pairwise comparisons; use multiple judges for high-stakes decisions; randomize and blind the judge
- **Known failure modes:** verbosity bias (longer answers rated higher), position bias, self-preference (judge favoring its own family of models), rubric drift
- **Always keep a human-labeled subset** as the anchor; if judge-human agreement drops, the judge broke, not the system

### D. Eval harness & automation

- **Batch harness:** run all cases, record per-case results and aggregate metrics
- **CI integration:** every prompt, retrieval-config, model-version, or dependency change triggers the eval suite; block the merge/deploy on regression
- **Scheduled runs:** nightly full suite; hourly smoke subset for fast signal
- **Production eval:** sample real traffic continuously, score with the judge, compare against the golden-set baseline
- **Reporting:** per-case diffs (what changed vs. last run), aggregate trend, regression flags
- **Trace-based analysis:** attribute failures to retrieval / prompt / model / tooling, so fixes go to the right layer

### E. Red-teaming

Run a structured adversarial exercise against the deployed application, not just the prompt.

| Attack Class | Test |
|--------------|------|
| **Prompt injection** | Direct ("ignore previous instructions"), indirect (instructions embedded in retrieved documents or tool output), encoded/obfuscated, multi-turn, role-play |
| **Jailbreak** | Persona override, hypothetical framing, competing system-prompt claims, translation, token-smuggling |
| **Data exfiltration** | Prompting the system to reveal its system prompt, config, or other users' data |
| **PII leakage** | Asking for sensitive fields; inferring sensitive attributes |
| **Tool abuse** | Parameter injection, path traversal, SQL injection through tool args, SSRF via a fetch tool, destructive-action attempts |
| **Privilege escalation** | Requesting elevated scopes, other tenants' resources |
| **Denial of wallet** | Loops, huge outputs, deliberately expensive requests |
| **Content policy** | Harmful, hateful, self-harm, sexual, illegal content |
| **Misinformation** | Leading questions, false premises, fabricated citations |

**Method:** pair red-teaming with the application engineers (adversarial context beats a solo attacker), write every finding as a reproducible test case, add the blockers to the golden set permanently, and re-run the full suite on every subsequent release.

**Finding severity model:**

| Severity | Definition | Required Action |
|----------|-----------|-----------------|
| **Critical** | Data exfiltration, privilege escalation, complete guardrail bypass | Block release; fix + retest |
| **High** | Material safety or policy violation with a plausible user path | Fix before go-live |
| **Medium** | Quality/policy degradation with limited impact | Fix or accept with owner + date |
| **Low** | Cosmetic or edge-case robustness | Log to backlog |

### F. Load & performance testing

- Ramp concurrency to find the degradation knee; test beyond it deliberately
- Scenarios: steady state, burst, cold start, long-context heavy, tool-heavy agentic
- Measure: TTFT, inter-token latency, end-to-end p95/p99, throughput, error rate, queue depth
- **Cost under load:** tokens/second × request rate = spend; extrapolate to monthly volume
- Sustained soak test to expose leaks (memory growth, connection exhaustion, index bloat)
- Resilience: kill the primary model instance, verify fallback; kill the vector DB, verify degradation mode; throttle downstream APIs, verify backpressure
- Mixed traffic: 80% simple / 20% complex — routing only pays off if you test the mix

### G. Customer-facing reporting

| Section | Content |
|---------|---------|
| Executive summary | What was evaluated, what the numbers are, what the caveats are |
| Methodology | Set size, composition, judge, calibration method, known limitations |
| Results | Metric table vs. thresholds vs. baseline, with trend |
| Failure analysis | Top failure modes with traces and root cause |
| Red-team findings | Findings table with severity, status, mitigations |
| Performance | Latency/throughput/cost under defined load profiles |
| Recommendations | Ship / iterate / stop, with the specific next steps |

**Be explicit about limitations.** A 200-case golden set from one source is a strong signal and a weak guarantee. Stating the coverage honestly is what makes the rest of the report trustworthy.

### H. Iteration & tuning

Evaluate → analyze → fix the layer that failed → re-evaluate. Attribution rules:

| Failure | Fix in |
|---------|--------|
| Correct doc not retrieved | Retrieval config: chunking, filters, top-k, hybrid, reranker |
| Right doc retrieved, wrong chunk | Chunking boundaries, parent-child, context packing |
| Right chunk, poor answer | Prompt, few-shot, model choice, context order |
| Tool picked wrong | Tool description, arg schema, few-shot for tool selection |
| Guardrail missed | Guardrail rules, classifier threshold, layer placement |

Record every change with its eval delta. This log becomes the strongest artifact in the customer conversation and the seed of the Phase 6 optimization backlog.

---

## 4.5 Inputs → Outputs

| Input | Source | Output | Consumer |
|-------|--------|--------|----------|
| Working application | Phase 3 | Something to evaluate | Phase 5 |
| Labeled sample set | Phase 1 | Seed of the golden set | Phase 6 |
| Target questions | Phase 0 | Coverage checklist | Phase 5 |
| Safety policy | Phase 0 | Red-team scope | Phase 5 |
| SLOs from Phase 0 | Phase 0 | Pass/fail thresholds | Phase 5 |
| Deployment | Phase 2 | Load test environment | Phase 5 |

---

## 4.6 Artifacts & Templates

| Artifact | Purpose | Template Sketch |
|----------|---------|-----------------|
| **Golden set spec** | How the set is built | Sources \| sampling method \| size \| label format \| rubric \| refresh cadence \| owner |
| **Golden set** | The cases | Case ID \| query \| context/refs \| expected \| rubric \| category \| severity |
| **Eval framework** | How it runs | Metric \| method (exact/judge/human) \| threshold \| dataset \| frequency \| owner |
| **Eval results report** | The evidence | Metric \| baseline \| current \| delta \| threshold \| pass/fail \| trend |
| **Judge calibration sheet** | Trust in the judge | 100 human-labeled cases, judge output, agreement %, disagreement analysis |
| **Red-team report** | Safety evidence | Finding ID \| class \| severity \| reproduction \| impact \| mitigation \| retest \| status |
| **Load test report** | Performance evidence | Scenario \| concurrency \| TTFT \| E2E p95 \| throughput \| errors \| cost |
| **Regression suite** | CI guardrail | Test name \| asserts \| golden case IDs \| introduced by |
| **Change log (prompt/config)** | Traceability | Date \| change \| layer \| eval delta \| shipped? \| owner |

---

## 4.7 Tooling Reference

| Need | Options |
|------|---------|
| Eval frameworks | promptfoo, DeepEval, Ragas, TruLens, Giskard, LangSmith evaluations, Phoenix evals, Braintrust, Galileo, Weights & Biases evals |
| LLM-as-judge | Judge models via API, Prometheus evals, G-Eval, rubric-based judges, pairwise ranking |
| Red-teaming | garak, PyRIT, Mindgard, Rebuff, VigilLLM, NVIDIA garak, internal suites |
| Guardrail testing | NeMo Guardrails, Llama Guard, custom suites, HackerOne-style internal programs |
| Data labeling | Label Studio, Argilla, Scale, Snorkel, Amazon Mechanical Turk |
| Load testing | k6, Locust, Artillery, JMeter, Vegeta, Locust + custom harness |
| Tracing / analysis | LangSmith, Langfuse, Arize Phoenix, Braintrust, OpenTelemetry |
| Safety scanning | garak, PyRIT, promptfoo red-team modules, NeMo Guardrails scanners |
| Dashboards | Grafana, Metabase, custom eval dashboards |

---

## 4.8 Metrics for This Phase

| Metric | How Measured | Target |
|--------|--------------|--------|
| Golden set size / coverage | Set audit |  |
| Human-judge agreement (calibration) | 100-case calibration |  |
| Task correctness | Golden set |  |
| Faithfulness / groundedness | LLM judge |  |
| Citation correctness | Automated + manual |  |
| Hallucination rate | Groundedness eval |  |
| Abstention accuracy (unanswerables) | Golden set |  |
| Retrieval hit@k | Offline eval |  |
| Instruction-following rate | Judge + assertions |  |
| Tool-call success rate | Trace analysis |  |
| Agent task completion rate | Golden set |  |
| Critical/High red-team findings open | Red-team tracker | **0** |
| Injection resistance rate | Red-team suite |  |
| p95 latency at target concurrency | Load test |  |
| Cost per request | Accounting |  |
| Regression suite coverage | CI audit |  |

---

## 4.9 Exit Criteria — Gate to Phase 5

- [ ] Golden set built, frozen, versioned, with documented composition and coverage
- [ ] Golden set includes unanswerable cases, edge cases, and adversarial cases
- [ ] Metrics defined with thresholds agreed by the product owner
- [ ] LLM-as-judge calibrated against ≥100 human-labeled cases; agreement documented
- [ ] Eval harness automated and running in CI on every relevant change
- [ ] Eval results meet all agreed thresholds on the golden set
- [ ] All Critical and High red-team findings fixed and retested; zero open
- [ ] Medium findings either fixed or accepted with an owner and due date
- [ ] Red-team cases added permanently to the regression suite
- [ ] Load test passed at target concurrency; p95 latency and cost within budget
- [ ] Resilience tested: primary failure → fallback works; downstream degradation handled
- [ ] Customer-facing evaluation report delivered and discussed
- [ ] Formal customer sign-off on quality and safety criteria

> **Gate decision owner:** product owner + security + exec sponsor. This is the last gate before the system faces real users.

---

## 4.10 Common Failure Modes & Anti-Patterns

| Symptom | Root Cause | Fix |
|---------|------------|-----|
| "The eval says it's great, users disagree" | Golden set is synthetic or unrepresentative | Use real, anonymized queries; measure coverage against live traffic |
| Judge is too generous | Verbosity bias, composite rubric | Binary criteria, calibration against humans, blind pairwise judging |
| Golden set edited after a failure | Test set treated as a scratchpad | Version the set; add new cases, never mutate failing ones |
| Only average quality measured | No unanswerable or edge cases | Include abstention, edge, and multi-turn categories |
| Security tested only on the prompt | Real attacks come through data and tools | Red-team the whole application, including retrieved content |
| Load test passes, prod latency bad | Unrepresentative context sizes | Test with real prompts and real retrieved context |
| Red-team is a one-off exercise | No re-test loop | Add findings to the regression suite; re-run per release |
| Eval suite too slow to run in CI | Full-fidelity suite on every commit | Tiered strategy: fast subset per commit, full suite nightly |
| No baseline to compare against | First eval run treated as truth | Capture a baseline at the first green run; compare deltas forever after |
| Blocking regressions are argued, not fixed | No owner or no threshold | Assign metric owners and enforce thresholds in CI |

---

## 4.11 Role Breakdown

| Role | Responsibility in Phase 4 |
|------|----------------------------|
| **FDE** | Builds the eval harness, runs the suite, correlates failures to layers, drives red-team with engineers |
| **ML Engineer** | Judge implementation and calibration, metric definitions, failure attribution |
| **Domain SME / Customer** | Golden set labeling, rubric authoring, correctness adjudication |
| **Security Engineer** | Threat model, red-team design, finding severity, fix verification |
| **Backend / Platform Engineer** | Load test harness, resilience tests, perf fixes |
| **Product Owner** | Threshold negotiation, eval report acceptance, sign-off |
| **Legal / Compliance** | Review of policy test coverage and evidence for audit |
| **Support Ops** | Real query samples, real incident scenarios, adoption feedback |

---

## 4.12 Practical Field Notes

- The golden set is the most valuable asset you will build. Budget SME time for it like production code.
- Include 20% **hard or ambiguous** cases in the set. A set of easy cases measures nothing.
- Evaluate the **retrieval stage separately** from generation. Joint metrics hide which layer is broken.
- Keep a **regression suite of ~50 fast cases** for CI and the full set for nightly. Speed decides whether people actually run it.
- Judge on a rubric with 2–4 binary assertions per case. It is faster to adjudicate and far more consistent.
- Red-team in pairs. Engineers who built the pipeline know its weak spots — use that knowledge as a weapon, and ask them to attack their own code.
- Write the "must not say" constraints into golden cases; prohibited content is a testable assertion, not a vibe.
- Reuse real failure cases from production forever. Every incident should add a permanent test.
- Present the evaluation report with the failures in it. A report showing only strengths will be trusted once.

---

## 4.13 Related Pages

- [AI SDLC index](AI-SDLC.md)
- Previous: [Phase 3 — RAG / Agent Build](03-rag-agent-build.md)
- Next: [Phase 5 — Go-Live & Handoff](05-go-live-and-handoff.md)
- Source reference: [FDE-Responsibilities.md — Pillar D (Evaluation, Red-Teaming & Go-Live)](../FDE-Responsibilities.md) and [§11 Eval Framework Template](../FDE-Responsibilities.md)

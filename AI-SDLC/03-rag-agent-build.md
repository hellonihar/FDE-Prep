# Phase 3 — RAG / Agent Build

[← AI SDLC index](AI-SDLC.md) · **Phase 3 of 7** · Previous: [Phase 2 — Infrastructure & Model Deployment](02-infrastructure-and-model-deployment.md) · Next: [Phase 4 — Evaluation & Red-Teaming](04-evaluation-and-red-teaming.md)

**Goal:** Build the application layer — retrieval, prompting, orchestration, tools, and guardrails — on top of the deployed model, integrated with the customer's systems.

**Exit criterion:** A working AI application that answers real questions end to end, with guardrails in place, instrumented for tracing, and wired to customer systems.

---

## 3.1 Why This Phase Exists

This is where the model becomes a *product*. Everything before it produced capability; everything after it measures and sustains it. Phase 3 is where most of the engineering happens and where most of the "AI quality" is actually determined.

The uncomfortable truth of RAG: **generation quality is usually downstream of retrieval quality**. Teams spend weeks tuning prompts against a retrieval index that returns the wrong documents. The fix is almost never in the prompt — it is in chunking, metadata filters, hybrid search, reranking, or query rewriting.

The uncomfortable truth of agents: **they fail in the orchestration, not the reasoning**. The model is fine; the loop has no termination condition, the tool schema is ambiguous, the tool returns an error the agent cannot interpret, and the run continues until it hits a token budget. Agent reliability is an engineering problem.

**The question this phase answers:** *does the system do the job from Phase 0, correctly, reliably, and observably?*

---

## 3.2 Objectives

- Build and tune the retrieval pipeline
- Design the prompt architecture and context assembly
- Implement the RAG generation path with citations and abstention
- Build the agentic workflow where required: tools, state, planning, termination
- Implement guardrails: input validation, output filtering, PII, policy
- Integrate with customer systems via APIs and MCP
- Instrument every step so Phase 4 can measure and Phase 6 can debug
- Handle failures explicitly rather than hoping the model avoids them

---

## 3.3 Entry Criteria

- [ ] Phase 2 exit criteria met (model serving, observability, load tested)
- [ ] Phase 1 data readiness accepted (corpus, chunking, embeddings decided)
- [ ] Ingestion pipeline running with a populated index
- [ ] Labeled sample set available for iterative checks
- [ ] Target question set from Phase 0 available

---

## 3.4 Key Activities

### A. Ingestion pipeline implementation

```
Connectors → Parse → Clean → Chunk → Embed → Index → (Serve)
```

- **Parsing:** layout-aware parsing; table extraction; OCR confidence thresholds; drop low-confidence docs to a review queue rather than indexing garbage
- **Cleaning:** boilerplate removal, header/footer stripping, dedupe, language detection
- **Chunking:** per Phase 1 decision — attach metadata to every chunk: source, title, section path, date, tenant, ACL, content hash, embedding version
- **Embedding:** batch with backoff; record model version; handle rate limits and re-run safely (idempotent)
- **Indexing:** upsert by content hash; support update and delete propagation; verify deletion actually removes vectors
- **Freshness:** scheduled incremental re-index for changed sources

### B. Retrieval pipeline

| Stage | Options | Note |
|-------|---------|------|
| Query understanding | Query rewrite, decomposition, HyDE, metadata filter extraction, spell/keyword normalization | Filters from the query ("only 2024 contracts") matter as much as semantic match |
| Retrieval | Dense (vector), sparse (BM25), hybrid | Hybrid wins on rare terms, IDs, error codes, names |
| Filtering | Metadata/ACL filters, tenant filters, date filters | Always enforce ACL server-side, never in the prompt |
| Reranking | Cohere Rerank, BGE Reranker, ColBERT | Typically the single biggest quality jump per unit of effort |
| Top-k selection | k, score threshold, diversity/MMR | Tune against the labeled set, not vibes |
| Context assembly | Order, dedupe, pack to budget, keep citations resolvable | Losing citations during packing breaks Phase 4 evaluation |

**Tune against the labeled set.** Measure hit@k before and after each change; keep a small config experiment log (chunk size × top-k × rerank on/off × hybrid).

### C. Prompt architecture & context assembly

- **System prompt:** role, task, constraints, refusal rules, citation requirement, output format
- **Few-shot examples:** 3–8 high-quality examples covering format and edge cases (select from the eval set — never from user data that could contain PII)
- **Context packing:** budget tokens for context vs. instructions vs. output; reserve output budget explicitly
- **Grounding rules:** instruct the model to answer only from context and cite; instruct it to say it does not know
- **Abstention:** an explicit "not answerable from the sources" path — the most important behavior for hallucination control
- **Output structure:** JSON schema, function-calling shapes, or markdown; validate server-side
- **Versioning:** treat every prompt change as a release candidate for Phase 4 eval

### D. Generation & post-processing

- Streaming (SSE/WebSocket) for perceived latency
- Structured output enforcement + schema validation + repair/retry loop
- Citation resolution: map generated citations back to chunk IDs and document URLs
- Post-processing: deduplication, formatting, localization, policy filtering, PII redaction
- Fallback path: if retrieval returns nothing above threshold, route to a human or a clarifying question rather than forcing an answer

### E. Agentic workflows (where required)

Only build an agent when the task genuinely requires multi-step work with tool use. A single-call RAG is cheaper, faster, and far easier to evaluate.

| Component | Design Decision |
|-----------|-----------------|
| **Orchestration framework** | LangGraph, CrewAI, ADK, AutoGen, or a hand-rolled state machine |
| **State** | Conversation history, working memory, artifacts; sliding window vs. summarization vs. external store |
| **Tools** | Function-calling schemas: name, description, typed args, error semantics, idempotency |
| **Tool design** | Narrow, unambiguous tools beat one giant "query_system" tool; include dry-run/confirm for destructive actions |
| **Termination** | Max steps, max tokens, max wall-clock, success detector, and a defined "give up" state |
| **Error handling** | Tool errors returned to the agent as structured results it can reason about — not exceptions that kill the run |
| **Human-in-the-loop** | Approval gates for consequential actions (writes, sends, deletes) |
| **MCP** | Model Context Protocol servers for customer tool and data-source integration |
| **Guardrails** | Per-step checks on inputs, tool arguments, and outputs — not just at the edges |

**Reliability patterns:** idempotent tool calls, checkpointing between steps, replayable traces, deterministic-ish step budgets, and a fallback to a simpler single-shot path when the agent exceeds budget.

### F. Guardrails & safety layers

| Layer | What It Protects | Implementation |
|-------|------------------|----------------|
| **Input validation** | Malformed/oversized/homoglyph input | Schema, length caps, encoding normalization |
| **Prompt injection defense** | Instructions embedded in retrieved/user content | Content separation, instruction hierarchy, injection detection, output filtering, least-privilege tools |
| **Output filtering** | Toxicity, harmful content, policy violations | Content safety classifiers, NeMo Guardrails, Llama Guard, custom validators |
| **PII protection** | Sensitive data in/out | Presidio in/out, provider-side redaction, no PII in logs/traces |
| **Topic/tenant boundaries** | Cross-tenant or off-topic access | Server-side filtering, allowlist topics, hard refusals |
| **Human approval** | High-impact actions | Approval node in the workflow |

> Defense in depth: assume retrieval can be poisoned and that tool arguments can be manipulated. Validate server-side, never only in the prompt.

### G. System integration

- Customer system connections: Salesforce, ServiceNow, SAP, Confluence, SharePoint, internal APIs
- Identity: propagate user identity and tenant so downstream systems and the index enforce access correctly
- Write-back: draft creation, ticket updates, notifications — with audit logging and idempotency
- API contract: request/response schema, streaming, error codes, rate limits, timeouts, retry semantics
- Contract tests for each integration: timeout, malformed response, partial failure, retry storm
- Rate limiting and backpressure against downstream systems — a slow agent can DDoS a customer API

### H. Instrumentation

Every hop emits a trace span: retrieval query → filters → candidate IDs + scores → reranked results → assembled context (token count) → prompt (version) → model call (model, version, tokens, latency) → output (citations, guardrail verdicts). Tool calls get their own spans with arguments (redacted) and results. Without this, Phase 4 cannot attribute a failure and Phase 6 cannot debug one.

### I. Application packaging

- API service (FastAPI, Node, etc.) with clear separation: app layer / retrieval layer / model client
- Config management: prompts, thresholds, model IDs, feature flags — all externalized and versionable
- Feature flags to enable rollback of individual components
- Local dev environment (docker-compose) that mirrors production
- Basic unit + integration tests on non-LLM logic (parsers, filters, formatters)

---

## 3.5 Inputs → Outputs

| Input | Source | Output | Consumer |
|-------|--------|--------|----------|
| Corpus + index | Phase 1 | Populated, versioned index | Phase 4, 6 |
| Model endpoint | Phase 2 | Application calling a healthy model | Phase 4 |
| Target questions | Phase 0 | Working flow on real inputs | Phase 4 |
| Customer API contracts | Phase 0 | Integrated tools | Phase 5 |
| Safety policy | Phase 0/4 | Guardrail layer | Phase 5, 6 |
| Tracing infra | Phase 2 | End-to-end traces | Phase 4, 6 |

---

## 3.6 Artifacts & Templates

| Artifact | Purpose | Template Sketch |
|----------|---------|-----------------|
| **Solution design doc** | How the app works | Flow diagram → components → prompt strategy → retrieval config → tools → guardrails → failure modes |
| **Retrieval config sheet** | Tunable parameters | Chunk size/overlap \| embedding model+version \| top-k \| hybrid weights \| rerank model \| filters \| thresholds — with the eval delta for each change |
| **Prompt registry** | Versioned prompts | ID \| purpose \| system prompt \| few-shot set \| version \| owner \| last eval result |
| **Tool contract** | Tool specification | Name \| description \| args schema \| returns \| error semantics \| idempotent? \| approval required? \| owner |
| **Agent state diagram** | Loop and termination | States → transitions → entry/exit → budget per path → failure path |
| **Guardrail matrix** | Coverage of risks | Threat \| layer \| control \| test case \| owner \| status |
| **Integration contract** | API behavior | Endpoint \| auth \| schema \| timeout \| retry \| rate limit \| error codes |
| **Prompt change log** | Traceability | Date \| prompt ID \| change \| rationale \| eval delta \| deployed? |

---

## 3.7 Tooling Reference

| Need | Options |
|------|---------|
| Orchestration | LangGraph, CrewAI, ADK, AutoGen, LlamaIndex, Haystack, semantic-kernel |
| RAG frameworks | LlamaIndex, LangChain, Haystack, DSPy, txtai |
| Reranking | Cohere Rerank, BGE Reranker, ColBERT, mixedbread, Jina |
| Vector DBs | Pinecone, Weaviate, Qdrant, Milvus, pgvector, Chroma, Elasticsearch |
| Tool protocol | MCP servers, OpenAPI function specs, GraphQL tooling |
| Guardrails | NeMo Guardrails, Llama Guard, Guardrails AI, Presidio, Azure AI Content Safety |
| Observability | LangSmith, Langfuse, Arize Phoenix, OpenTelemetry, Braintrust |
| Prompt management | Langfuse, promptfoo, Agenta, plain Git + CI |
| Caching | Redis (semantic + response cache), GPTCache, LiteLLM proxy |
| Serving | FastAPI, Node/Express, LiteLLM (routing/proxy layer) |
| Feature flags | LaunchDarkly, Unleash, OpenFeature, env-based flags |

---

## 3.8 Metrics for This Phase

| Metric | How Measured | Target |
|--------|--------------|--------|
| Retrieval hit@k (dev set) | Labeled set |  |
| Context token usage (p50/p95) | Trace analysis |  |
| Answer groundedness (dev) | LLM-as-judge |  |
| Citation correctness rate | Manual + automated |  |
| Abstention rate on unanswerable | Dev set |  |
| Tool-call success rate | Trace analysis |  |
| Agent steps per task (avg) | Trace analysis |  |
| Agent budget-exceeded rate | Trace analysis |  |
| Guardrail block rate (by category) | Guardrail logs |  |
| End-to-end p95 latency (dev) | Tracing |  |
| Cost per request (dev) | Token accounting |  |
| Index coverage / chunk count | DB stats |  |
| Ingestion success rate | Pipeline logs |  |

---

## 3.9 Exit Criteria — Gate to Phase 4

- [ ] Ingestion pipeline runs end to end, idempotently, with update and delete propagation verified
- [ ] Retrieval pipeline implemented with documented config; hit@k measured on the labeled set
- [ ] Reranking in place (or explicitly justified as unnecessary)
- [ ] Prompt architecture documented, versioned, with system prompt + few-shot + abstention path
- [ ] Citations resolve to source documents and URLs
- [ ] Output validated against a schema; malformed output handled (not crashed on)
- [ ] Guardrails implemented for input, output, PII, injection, and access boundaries
- [ ] Agentic workflow (if any) has max-steps, timeout, cost cap, and a defined failure state
- [ ] Tool contracts documented; destructive actions gated behind approval
- [ ] Customer system integrations working, with contract tests for timeout/error/retry
- [ ] End-to-end tracing active for every hop, with request-ID correlation
- [ ] Prompts, thresholds, and model IDs externalized and switchable
- [ ] Feature flags in place for rollback of retrieval, prompt, and model independently

> **Gate decision owner:** FDE + ML engineer. Phase 4 is where these choices get scored, so nothing needs to be "perfect" here — but everything must be *instrumented*.

---

## 3.10 Common Failure Modes & Anti-Patterns

| Symptom | Root Cause | Fix |
|---------|------------|-----|
| Answers fluent but wrong | Retrieval returns irrelevant context | Fix chunking/filters/reranking before touching the prompt |
| Works on demos, fails on real traffic | Demo used short, clean, in-domain questions | Rehearse on the Phase 0 target question set and real query logs |
| Citations fabricated | Model free-forms citation text | Constrain output schema; resolve citations from retrieved chunk IDs, never from generated text |
| Agent loops forever | No termination condition | Max steps + timeout + cost cap + explicit success detector |
| Agent takes destructive actions | Tools are too powerful and ungated | Narrow tools, least privilege, approval nodes, idempotency |
| Injection via retrieved document | Corpus is user-writable and trusted | Sanitize content, separate instructions from data, restrict tools, filter outputs |
| Guardrails block legitimate traffic | Rules written against imagined inputs | Build guardrail test cases from real traffic and tune |
| PII leaking into traces/logs | Logging raw prompts by default | Redact at the logging layer, not by policy |
| Tool errors crash the run | Exceptions instead of structured results | Return structured tool errors the model can act on |
| Cross-tenant leakage | Filtering done in the prompt | Enforce ACL server-side in retrieval and tool calls |
| Works only in notebooks | Not reproducible, no versioning | Promote to a service with config, tests, and CI |

---

## 3.11 Role Breakdown

| Role | Responsibility in Phase 3 |
|------|----------------------------|
| **FDE** | Builds the application, tunes retrieval, integrates customer systems, owns the working demo |
| **ML Engineer** | Prompt architecture, retrieval/reranking design, quantization-aware tuning, model behavior |
| **Data Engineer** | Ingestion pipeline, chunking implementation, index freshness, embedding throughput |
| **Backend Engineer** | API service, integrations, contract tests, caching, auth |
| **Platform / SRE** | Runtime config, feature flags, scaling the app tier, environment parity |
| **Security Engineer** | Guardrail design, injection defense, threat model review, secret handling |
| **Domain SME** | Few-shot examples, prompt review, "does this read like the expert would write it" |
| **Product Owner** | Scope calls on features vs. safety, acceptance of UX behavior |

---

## 3.12 Practical Field Notes

- Tune retrieval against a labeled set from day one. Without it, every change is an argument, not a measurement.
- Put the **routing layer in front of the model** (LiteLLM or similar). Model swapping, retry, budget limits, and caching all become config instead of code.
- Use semantic caching carefully — it helps on repetitive support traffic and *hurts* on contextual queries. Measure it, do not assume it.
- Reserve output tokens explicitly. Default max_tokens silently truncates answers and produces broken JSON.
- Write the system prompt as a **versioned artifact in git**, reviewed like code. Prompt changes are the most common cause of regressions.
- Keep an "assistant refuses" path that is graceful. Forcing an answer when nothing is found is how hallucination incidents start.
- Instrument tool calls with a cost attribution per step. When the bill spikes, you need to know which step ran away.
- Test the unhappy path deliberately: empty retrieval, tool timeout, malformed output, injected instruction, oversized context.

---

## 3.13 Related Pages

- [AI SDLC index](AI-SDLC.md)
- Previous: [Phase 2 — Infrastructure & Model Deployment](02-infrastructure-and-model-deployment.md)
- Next: [Phase 4 — Evaluation & Red-Teaming](04-evaluation-and-red-teaming.md)
- Source reference: [FDE-Responsibilities.md — Pillar C (RAG pipelines, agents, guardrails)](../FDE-Responsibilities.md) and [§8 RAG vs Fine-Tuning vs Agentic](../FDE-Responsibilities.md)
- **Deeper dive:** [FDE-Responsibilities.md Pillar C](../FDE-Responsibilities.md) expands the 20 model-deployment tasks as step-by-step procedures with templates, the same five worked business cases (Northwind Mutual, Meridian Bank, Corvid, St. Aldwyn Health, Halden & Rowe), and eleven publicly documented reference cases.

# Forward Deployed Engineer (FDE) — AI Implementation Responsibilities Reference

**Version:** 2.0
**Last Updated:** 2026-10-20
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

<details>
<summary><b>18 core tasks</b> (click to expand)</summary>

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

# Phase 2 — Infrastructure & Model Deployment

[← AI SDLC index](AI-SDLC.md) · **Phase 2 of 7** · Previous: [Phase 1 — Data & Readiness Assessment](01-data-readiness-assessment.md) · Next: [Phase 3 — RAG / Agent Build](03-rag-agent-build.md)

**Goal:** Stand up a reproducible, observable inference stack that serves the chosen model inside the agreed latency, cost, and residency envelope.

**Exit criterion:** Model endpoint serving production-shaped traffic, passing health checks and load tests, with monitoring active and a rollback path proven.

---

## 2.1 Why This Phase Exists

Phase 2 turns "we have chosen a model" into "there is a URL that returns tokens with a known latency profile." It is the phase where infrastructure decisions become irreversible in practice: GPU quotas, cluster topology, and network paths take weeks to obtain, and a bad choice there constrains every phase after it.

The most expensive mistake in AI delivery is **over-provisioning for the wrong thing**. A team rents 8× H100 to serve 200 requests/day at 40% average utilization, and then discovers the workload is embarrassingly batchable and the same service would cost 30× less as a managed API or a single L40S. That gap between provisioned capacity and actual demand is where AI infrastructure budgets go to die.

**The question this phase answers:** *what does it cost to serve 1,000 real requests, and does it land inside the SLO with headroom?*

---

## 2.2 Objectives

- Decide hosting topology and obtain capacity
- Deploy and tune the inference stack
- Implement model routing, fallbacks, and version control
- Establish networking, identity, and secret management
- Instrument the stack with observability from day one
- Measure real latency/throughput/cost under load
- Document a reproducible build path (IaC) so the environment can be rebuilt

---

## 2.3 Entry Criteria

- [ ] Phase 1 exit criteria met (data readiness accepted)
- [ ] Model choice made with a documented rationale (Phase 0/2 ADR)
- [ ] Capacity requirement estimated from volume + latency targets
- [ ] Data residency requirements known — decides region and whether self-hosting is even legal
- [ ] Network path requirements agreed with platform/security
- [ ] GPU quota / cloud budget approved (this can have multi-week lead time — start early)

---

## 2.4 Key Activities

### A. Capacity planning

- **Volume model:** peak concurrent users, requests/second, tokens/request (input + output), tokens/second per replica
- **Latency budget:** decompose the target into retrieval, prompt assembly, prefill (TTFT), decode, and network
- **Headroom:** design for peak × 1.5–2× plus failover capacity (N+1)
- **Batching:** does the workload support continuous batching, or is it latency-sensitive request/response?
- **Context length:** what context does the task actually need? Long context is billed, slow, and often hurts quality
- **Cost model:** compare three shapes — managed API, single-node self-hosted, multi-node self-hosted — at your volume; write the break-even volume down

| At ~X requests/day | Managed API | Single node self-hosted | Cluster self-hosted |
|---|---|---|---|
| Low volume (hundreds) | Usually wins | Rarely wins | Never wins |
| Mid volume | Compare closely | Often wins | Rarely |
| High/bursty (100k+) | Compare on committed-use pricing | Sometimes | Often wins |
| Strict residency / air-gap | n/a (unless dedicated endpoint) | Wins | Wins |

### B. Serving stack selection & deployment

| Approach | Best For | Trade-offs |
|----------|----------|------------|
| **Managed API** (GPT/Claude/Gemini) | Fastest path, frontier quality, low ops | Per-token cost, data leaves premises, less control over inference params |
| **vLLM / SGLang** | General-purpose high-throughput serving on GPU | Needs GPU ops, tuning for best results |
| **TensorRT-LLM** | Lowest latency on NVIDIA, NVIDIA-specific | Inflexible, longer build cycles |
| **TGI** | Straightforward HuggingFace serving | Less tuning headroom than vLLM/SGLang |
| **llama.cpp / Ollama** | CPU, edge, Jetson, very small models | Lower quality at scale |
| **Managed inference platforms** (Baseten, Fireworks, Together, Modal, AWS Bedrock, Azure AI Foundry, Vertex AI) | Managed scaling, multi-model, pay-per-use | Less infra control, platform lock-in |

- Deploy inference on Kubernetes (EKS/GKE/AKS/OpenShift) with KServe, KubeRay, or Ray Serve
- Configure GPU scheduling: MIG (partitioning), time-slicing, NVIDIA GPU Operator, Run:ai
- Containerize the inference image; pin model weights, container digest, and CUDA version
- Model registry: MLflow, Vertex AI Model Registry, Weights & Biases — so "which model is live" has an answer
- Version pinning: model version, quantization, serving parameters (temperature, max tokens, stop), system prompt version, embedding model version

### C. Inference tuning (do this before load testing)

- **Quantization:** FP8 / AWQ / GPTQ / INT8 — measure quality delta on the Phase 1 labeled set, not just throughput gain
- **Batching:** continuous batching for throughput; tune `max_num_seqs`
- **KV cache:** enable prefix caching for repeated system prompts; size the cache
- **Speculative decoding:** for generation-heavy workloads with a small draft model
- **Tensor/pipeline parallelism:** only when the model does not fit on one GPU
- **Speculative/correctness notes:** record every changed parameter as an ADR — it changes quality

### D. Model routing, fallback & versioning

- **Primary → fallback → error handling** chain, with the trigger condition for each (latency, error rate, token-limit, safety refusal)
- **Task-based routing:** small model for classification/extraction, large model for reasoning — this is the single biggest cost lever
- **Canary:** serve a traffic percentage from a new model version, compare on quality + cost, then promote
- **Version pinning:** never let a provider silently change the model under you — pin versions and re-eval before accepting upgrades

### E. Networking, identity & secrets

- VPC design, private endpoints (Azure Private Endpoint, AWS PrivateLink, GCP Private Service Connect)
- TLS/mTLS and certificate management; service-to-service auth
- Load balancers and ingress
- Secret management: Vault, AWS Secrets Manager, Azure Key Vault, Kubernetes Secrets — no keys in code or images
- AI-layer authn/authz: the app is a first-class API — enforce tenant/user scoping and propagate identity to tools
- Model API key rotation and per-environment separation

### F. Observability baseline

Set up **before** the app exists, so Phase 3 and 6 inherit it:

- Metrics: Prometheus + Grafana; GPU metrics via NVIDIA DCGM Exporter; node exporters
- LLM tracing: LangSmith, Langfuse, Arize, Phoenix — capture prompt, retrieved context, model output, tokens, latency per call
- Token accounting per request and per tenant (this becomes the cost dashboard)
- Logs: structured, with request IDs that also appear in traces (correlation is what makes debugging possible)
- Alerts skeleton: latency, error rate, GPU utilization/memory, queue depth, token spend rate

### G. Load & cost characterization

- Ramp concurrency gradually: find the knee where latency starts degrading
- Measure: TTFT p50/p95/p99, inter-token latency, throughput (req/s, tok/s), error rate, queue depth
- Test cold start (model load) and warm behavior separately
- Test with realistic prompt lengths and real context — synthetic short prompts hide memory pressure
- Compute measured cost per request; compare against the Phase 0 budget

### H. Reproducibility

- Everything in IaC: Terraform, Helm charts, Kustomize, Docker Compose for local dev
- CI/CD for infra and model deployment: GitHub Actions, ArgoCD, Flux
- Documented runbook: how to rebuild the environment from scratch, how to roll back, how to scale

---

## 2.5 Inputs → Outputs

| Input | Source | Output | Consumer |
|-------|--------|--------|----------|
| Volume + latency targets | Phase 0 | Capacity plan, cost model | Phase 5, 6 |
| Model choice + ADR | Phase 0 | Deployment config, pinned versions | Phase 3 |
| Residency constraints | Phase 1 | Region/site selection | Phase 5 |
| Network requirements | Platform | VPC, endpoints, ingress | Phase 5 |
| Labeled sample set | Phase 1 | Quantization quality check | Phase 4 |
| Budget ceiling | Sponsor | Spend guardrails, alerts | Phase 6 |

---

## 2.6 Artifacts & Templates

| Artifact | Purpose | Template Sketch |
|----------|---------|-----------------|
| **Infrastructure design doc** | The build spec | Topology diagram → compute → network → storage → identity → observability → deployment flow |
| **Capacity & cost model** | Justifies the spend | Volume assumptions → replicas → GPU type → utilization → $/month → $/request → API comparison |
| **Model deployment manifest** | Reproducible deployment | Image digest, model ID + version, weights URI, serving params, quantization, HPA, probes |
| **Inference tuning log** | Every parameter change | Date → parameter → old/new → quality delta → throughput delta → decided by |
| **ADR: hosting decision** | Records the big choice | Managed API vs. self-hosted; region; scaling model; rollback |
| **Fallback & routing policy** | Failure behavior | Condition → action → owner → max blast radius |
| **Runbook: deploy/rollback/scale** | Operational procedure | Pre-checks → deploy → verify → rollback triggers → steps |
| **Monitoring config** | Dashboards & alerts | Panel list, alert list with thresholds, owner, runbook link |

---

## 2.7 Tooling Reference

| Need | Options |
|------|---------|
| Inference servers | vLLM, SGLang, TensorRT-LLM, TGI, llama.cpp, Ollama, NVIDIA NIM |
| Serving frameworks | KServe, Ray Serve, KubeRay, Triton |
| Orchestration | Kubernetes (EKS, GKE, AKS, OpenShift), Nomad |
| Cloud GPU | AWS p4d/p5, Azure ND H100, GCP A3/A100, CoreWeave, Lambda |
| Edge | NVIDIA Jetson Orin, Jetson AGX, Intel NUC, ruggedized servers |
| IaC | Terraform, OpenTofu, Pulumi, Crossplane |
| Packaging | Docker, containerd, model container registries (ECR, ACR, GCR) |
| Secrets | HashiCorp Vault, AWS Secrets Manager, Azure Key Vault, SOPS |
| Metrics | Prometheus, Grafana, NVIDIA DCGM Exporter, VictoriaMetrics |
| Tracing | LangSmith, Langfuse, Arize Phoenix, OpenTelemetry, Tempo |
| Registry | MLflow, Vertex AI Model Registry, Weights & Biases |

---

## 2.8 Metrics for This Phase

| Metric | How Measured | Target |
|--------|--------------|--------|
| TTFT p50 / p95 | Load test |  |
| Inter-token latency p95 | Load test |  |
| End-to-end p95 latency | Load test |  |
| Throughput (req/s, tok/s) | Load test |  |
| Max concurrent users served within SLO | Ramp test |  |
| Error rate | Load test |  |
| Cold-start time | Deploy test |  |
| GPU utilization (avg / peak) | DCGM |  |
| Cost per request (measured) | Token + infra accounting |  |
| Fallback trigger rate | Routing logs |  |
| Deployment time (infra → serving) | CI/CD log |  |
| Rebuild-from-scratch time | Runbook drill |  |

---

## 2.9 Exit Criteria — Gate to Phase 3

- [ ] Model deployed with pinned version, image digest, and serving parameters
- [ ] Endpoint healthy behind the production ingress path, with authn/authz enforced
- [ ] Fallback chain configured and tested (fail the primary deliberately, confirm fallback)
- [ ] Observability live: metrics, traces, structured logs, request-ID correlation
- [ ] Load test completed at target concurrency with results inside the Phase 0 latency budget
- [ ] Cost per request measured and within the agreed budget
- [ ] Quantization/serving changes validated against the Phase 1 labeled set — quality delta accepted
- [ ] IaC in place; environment rebuild documented and rehearsed
- [ ] Secrets externalized; no credentials in code, images, or notebooks
- [ ] Runbook written for deploy, scale, rollback, and model swap
- [ ] Capacity plan and cost model reviewed with platform and finance

> **Gate decision owner:** technical lead + platform owner.

---

## 2.10 Common Failure Modes & Anti-Patterns

| Symptom | Root Cause | Fix |
|---------|------------|-----|
| Latency fine in test, bad in production | Synthetic short prompts hid context/memory pressure | Load test with real prompt lengths and real context |
| GPU underutilized, bills high | Provisioned for peak, traffic is bursty | Queue-based batching, scale-to-zero for async, right-size after measurement |
| Self-hosting costs more than the API | Small volume, high ops overhead | Recompute break-even; prefer managed unless residency forbids it |
| Model silently upgraded, quality dropped | No version pinning or re-eval on upgrade | Pin versions; re-eval before accepting a provider upgrade |
| OOM under load | Context length or batch size too aggressive | Cap max context, tune `max_num_seqs`, enable prefix caching |
| Secrets in images / notebooks | Fast path over security | Vault + secret refs; scan images in CI |
| No rebuild path | Manual env built once | IaC + rehearsed rebuild |
| Traces exist but are unusable | No request-ID correlation | Standardize request IDs across logs, traces, and spans |
| Rollback untested | Rollback assumed, never drilled | Drill it in Phase 2 while stakes are low |

---

## 2.11 Role Breakdown

| Role | Responsibility in Phase 2 |
|------|----------------------------|
| **FDE** | Owns stack choice, tuning, load testing, runbooks, customer-facing capacity discussion |
| **ML Engineer** | Model selection validation, quantization assessment, serving parameter tuning |
| **Platform / SRE** | Cluster, GPU scheduling, networking, autoscaling, capacity, cost controls |
| **Security / Platform** | VPC and private endpoint review, secret management, identity integration |
| **Data Engineer** | Vector DB deployment, storage, embedding pipeline capacity (see Phase 1) |
| **Finance / Sponsor** | Budget approval, spend guardrails, break-even sign-off |
| **Customer Infra Team** | Environment access, quota, network path, eventual operational ownership |

---

## 2.12 Practical Field Notes

- Measure cost per request **at your actual volume**, not per million tokens. Per-token pricing hides the fixed-cost problem.
- Always test with a real system prompt and real retrieved context. Prompt length is where the memory pressure hides.
- Put the fallback chain in place early and *break the primary on purpose* once. Untested fallbacks are fiction.
- Prefer prefix caching aggressively — shared system prompts are free tokens if you enable it.
- Tag GPU nodes and index spend by environment so finance can see what the POC costs. It usually shocks people, usefully.
- Write the rollback runbook and rehearse it in this phase. Phase 5 is the wrong time to discover the previous version's image digest is gone.
- Pin the container image by digest, not by tag. Tags move.

---

## 2.13 Related Pages

- [AI SDLC index](AI-SDLC.md)
- Previous: [Phase 1 — Data & Readiness Assessment](01-data-readiness-assessment.md)
- Next: [Phase 3 — RAG / Agent Build](03-rag-agent-build.md)
- Source reference: [FDE-Responsibilities.md — Pillars B (Infrastructure) and C (Model Deployment)](../FDE-Responsibilities.md) and [§8 Deployment Context Variations](../FDE-Responsibilities.md)

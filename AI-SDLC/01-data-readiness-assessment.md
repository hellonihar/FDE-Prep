# Phase 1 — Data & Readiness Assessment

[← AI SDLC index](AI-SDLC.md) · **Phase 1 of 7** · Previous: [Phase 0 — Discovery & Scoping](00-discovery-and-scoping.md) · Next: [Phase 2 — Infrastructure & Model Deployment](02-infrastructure-and-model-deployment.md)

**Goal:** Know exactly what data exists, how good it is, how fresh it is, and how it will reach the system — before any infrastructure is committed.

**Exit criterion:** Data readiness report accepted by the data owner, integration plan agreed with platform engineering, and baseline retrieval/quality metrics recorded.

---

## 1.1 Why This Phase Exists

AI applications are **data products wearing an application costume**. The model is a rented capability; the corpus is the actual product. If the corpus is incomplete, stale, or structured in a way that fights the retrieval strategy, no amount of prompt tuning or a bigger model will fix it.

A concrete failure: a legal RAG assistant scored 92% in the demo using hand-picked contracts. In production, the corpus included 40,000 auto-renewal notices, superseded policy PDFs, and emails containing client-confidential material. The system's answers were frequently *correctly retrieved from the wrong document*. Nobody had looked at what was actually in the corpus.

Phase 1 is where you find that — cheaply, before GPUs are ordered and code is written.

**The question this phase answers:** *if the model were perfect, would the data still be enough to answer the question correctly?*

---

## 1.2 Objectives

- Inventory every data source that could answer the target questions
- Assess quality, coverage, structure, and freshness
- Identify access, licensing, residency, and privacy constraints
- Test baseline retrieval performance before choosing embeddings and chunking
- Map the ingestion path and every integration point
- Quantify data volume, growth rate, and cost to serve
- Produce a remediation plan for data gaps with owners

---

## 1.3 Entry Criteria

- [ ] Phase 0 exit criteria met (architecture and technique direction known)
- [ ] Named data owners for each candidate source
- [ ] Access credentials or sandbox available for at least a sample
- [ ] Target question set drafted (10–50 representative queries)
- [ ] Data residency / compliance constraints known from Phase 0

---

## 1.4 Key Activities

### A. Source inventory

For each candidate source, capture:

| Attribute | Why It Matters |
|-----------|-----------------|
| Source system | System of record vs. derived copy |
| Format | PDF, DOCX, HTML, ticket export, database rows, wikis, code, images, audio |
| Volume | Document count, total bytes, growth rate per month |
| Update cadence | Batch nightly, real-time stream, sporadic/manual |
| Owner | Who fixes the source when it is wrong |
| Access method | API, SQL, SFTP, export, scrape, MCP |
| Auth model | Service account, SSO-scoped, customer-credentialed |
| Sensitivity | PII, PHI, privileged, export-controlled, financial |
| Residency | Where the data physically lives today |
| Licensing | Contractual right to use for this purpose (including for embeddings) |
| Quality smells | Duplicates, watermarks, scanned images with no text layer, templates, boilerplate |

### B. Quality assessment

Run a **sampling audit** — stratified random sample, typically 100–500 documents per source, reviewed by a human:

- **Extractability:** can text be extracted losslessly? OCR quality on scans; table structure preserved?
- **Completeness:** are documents truncated? Are attachments included? Are embedded links followed?
- **Duplication:** near-duplicate boilerplate, versioning (is v1 or v3 in the corpus?), email threads
- **Structure:** headings, tables, lists, code blocks, cross-references — structure the chunker must preserve
- **Metadata:** author, date, tenant, ACL, document type — and whether it is trustworthy
- **Language/mix:** multilingual content, code-switching, OCR garbage, encoding damage
- **Contradictions:** superseded documents still present; conflicting policy versions
- **Coverage vs. target questions:** can this source actually answer the questions from Phase 0?

### C. Freshness & volatility analysis

- How stale can this data be allowed to be? (Answer defines a freshness SLA, e.g. "pricing docs ≤24h, HR policies ≤30d")
- What is the current age distribution of the corpus?
- What is the update path — event-driven, nightly batch, on-demand?
- Who is accountable when a source goes stale?

### D. Baseline retrieval test

Before selecting an embedding model or chunking strategy, measure the ceiling:

1. Take the 10–50 target questions from Phase 0
2. Label the **known-correct source documents** for each (with SME help)
3. Ingest a sample corpus with a simple default strategy
4. Measure **hit@k** (is a correct document in the top-k?) and **MRR / nDCG** for ranking quality
5. Vary: chunk size, overlap, dense vs. hybrid (BM25), top-k

This tells you the actual retrieval ceiling. If baseline hit@5 is 40%, no generation prompt will produce good answers — the ceiling is retrieval, not the model. Fix the corpus/chunking before buying a bigger model.

### E. Chunking strategy selection

| Strategy | Use When | Risk |
|----------|----------|------|
| Fixed-size | Plain, uniform text (logs, notes) | Splits mid-thought, loses context |
| Recursive character | General-purpose default | Still ignores semantics |
| Semantic | Boundaries between topics, variable length | Slower, needs sentence embeddings |
| Structural / heading-based | Structured docs (policies, manuals, wikis) | Requires clean heading extraction |
| Parent-child | Small child for retrieval, large parent for context | More index entries, two-level lookup |
| Contextual retrieval | Long documents where each chunk needs its source context | Extra LLM pass over corpus at index time |

Also decide early: **metadata to attach to each chunk** (source, date, tenant, ACL, section path, doc hash) — retrofitting metadata means re-embedding everything.

### F. Embedding model evaluation

- Compare candidate embeddings on the **baseline retrieval test**, not on a leaderboard
- Consider: quality, dimensionality (storage & latency), inference cost, license, self-host vs. API, language coverage, data residency
- Record the embedding model version in the index metadata — mismatched embedding/index versions are a classic silent-failure source

### G. Ingestion & integration design

- Ingestion pipeline: connectors → parse → clean → chunk → embed → index
- Parsing stack: document parsing library, OCR service, table extraction, layout-aware parsing
- Idempotency: re-running the pipeline must not duplicate documents
- Deduplication strategy: content hash, similarity-based
- Deletion/propagation: when a source document is deleted or updated, the index must follow (GDPR/right-to-delete, stale policy)
- Change data capture (CDC) vs. full re-index: CDC is faster but far more complex
- **ACL propagation:** if the system is multi-tenant, per-document access control must survive into the index

### H. Data access, privacy & legal

- PII inventory and masking strategy (Presidio, or provider-side redaction)
- Retention & right-to-delete path, end to end (source → index → logs → traces → eval sets)
- Whether data may be sent to a third-party model API, and under what agreement
- Embeddings considered derived data — confirm contractual position
- Residency: does inference happen in a region where the data is allowed to be?
- Licensing: retrieval of customer documents into an AI index for a specific purpose

### I. Data readiness reporting

Produce a written report (this becomes a Phase gate artifact):

| Dimension | Rating | Evidence |
|-----------|--------|----------|
| Coverage | Green / Amber / Red | % of target questions answerable from corpus |
| Quality | | Sampling audit results |
| Freshness | | Age distribution vs. required SLA |
| Structure | | Extractability %, table preservation, OCR confidence |
| Access | | Connector feasibility, auth, lead times |
| Privacy & compliance | | PII, residency, retention, licensing |
| Cost & scale | | Storage, embedding cost, re-index cost |

---

## 1.5 Inputs → Outputs

| Input | Source | Output | Consumer |
|-------|--------|--------|----------|
| Target questions | Phase 0 | Question set (seed for golden set) | Phases 3, 4 |
| Source inventory | Data owners | Source register | Phase 3, 6 |
| Sample documents | Data owners | Sampling audit findings | Phase 3 |
| Known-correct labels | SMEs | Labeled retrieval test set | Phases 3, 4 |
| Residency & retention policy | Legal | Compliance constraints | Phases 2, 5 |
| Access credentials | Platform | Working connector | Phase 3 |
| Freshness requirement | Product | Freshness SLA | Phase 6 |

---

## 1.6 Artifacts & Templates

| Artifact | Purpose | Template Sketch |
|----------|---------|-----------------|
| **Data readiness report** | The gate document | Source register → quality rating → freshness → baseline metrics → gaps → remediation plan → risks |
| **Source register** | Every source, one row | Source \| format \| volume \| cadence \| owner \| sensitivity \| residency \| access method \| status |
| **Sampling audit sheet** | Quality findings | Sample ID \| source \| extractability \| completeness \| duplication \| structure issues \| notes |
| **Integration plan** | How data moves | Source → connector → parser → transform → index \| auth \| failure handling \| owner |
| **Baseline metrics sheet** | The before-picture | hit@1/3/5/10 \| MRR \| nDCG \| per-config table (chunk size × overlap × dense/hybrid) |
| **PII inventory** | Sensitive data map | Field \| location \| sensitivity \| masking approach \| retention \| owner |
| **Data gap remediation plan** | Fix or accept | Gap \| impact \| severity \| fix \| effort \| owner \| due date |

---

## 1.7 Tooling Reference

| Need | Options |
|------|---------|
| Parsing / OCR | Unstructured, LlamaParse, Azure Document Intelligence, Google Document AI, Tika, PyMuPDF, pypdf, Docling, Marker |
| OCR | Tesseract, AWS Textract, Azure OCR, Google Vision, PaddleOCR |
| Vector DB | Pinecone, Weaviate, Qdrant, Milvus, pgvector, Chroma, Elasticsearch, Vespa |
| Embeddings | OpenAI text-embedding-3, Cohere Embed, Voyage, BGE, E5, GTE, jina, Nomic |
| Rerankers | Cohere Rerank, BGE Reranker, ColBERT, mixedbread |
| Sparse / hybrid | Elasticsearch, OpenSearch, Weaviate hybrid, BM25 implementations |
| PII | Presidio, AWS Comprehend, Google DLP, Azure AI Content Safety |
| Data quality / lineage | Great Expectations, Deequ, DataHub, OpenLineage, dbt tests |
| Sampling / audit | SQL + notebooks, Label Studio, Argilla |

---

## 1.8 Metrics for This Phase

| Metric | How Measured | Target |
|--------|--------------|--------|
| Document extractability rate | Parsing audit |  |
| Corpus duplication rate | Hash + similarity scan |  |
| Corpus freshness (p50 / p95 age) | Timestamp analysis |  |
| Baseline retrieval hit@5 | Labeled retrieval test |  |
| Baseline MRR / nDCG | Retrieval eval |  |
| Target questions answerable from corpus | SME review |  |
| Ingestion throughput | Pipeline test |  |
| Re-index time (full corpus) | Pipeline test |  |
| Storage footprint per document | DB stats |  |
| PII fields unmasked in index | Security scan | 0 in prod-scoped data |

---

## 1.9 Exit Criteria — Gate to Phase 2

- [ ] Every candidate source has an entry in the source register with a named owner
- [ ] Sampling audit completed per source; quality rating assigned
- [ ] Freshness SLA defined per source class and agreed with the owner
- [ ] Baseline retrieval test run with labeled set; hit@k and MRR recorded
- [ ] Chunking strategy selected and justified against the baseline results
- [ ] Embedding model selected and validated on the baseline test
- [ ] Ingestion pipeline designed end to end, including update and deletion propagation
- [ ] ACL / tenancy strategy defined if multi-tenant
- [ ] PII inventory complete; masking approach defined and tested on samples
- [ ] Residency, retention, and licensing questions answered in writing by legal
- [ ] Data gaps documented with severity, owner, and remediation plan
- [ ] Data readiness report reviewed and accepted by the data owner and technical lead

> **Gate decision owner:** data owner + compliance lead. Report is a required Phase 4 input — the golden set cannot be built without the labeled retrieval set.

---

## 1.10 Common Failure Modes & Anti-Patterns

| Symptom | Root Cause | Fix |
|---------|------------|-----|
| Retrieval quality poor, blamed on the model | Corpus/chunking never measured | Baseline retrieval test in Phase 1 |
| Duplicate or superseded docs dominate results | No dedup, no versioning policy | Content hash + prefer-latest rule + dedupe pass |
| Scanned PDFs yield garbage chunks | OCR not in scope | Add OCR + confidence threshold; exclude low-confidence docs |
| Index stale within days of launch | No CDC and no freshness monitoring | Incremental re-index + freshness alert |
| Permissions leak across tenants | ACL not propagated to chunks | ACL field on every chunk + filter at query time |
| Embedding model swap breaks everything later | Version not recorded | Store embedding version + model ID in index metadata |
| Huge corpus, tiny useful subset | No coverage analysis | Cluster/sample by topic; tag and weight sources |
| Legal blocks the project in Phase 4 | Compliance not in Phase 1 | Legal review of source register before build |
| Re-index takes a week | Full re-embed on every change | Incremental embedding + content-hash cache |

---

## 1.11 Role Breakdown

| Role | Responsibility in Phase 1 |
|------|----------------------------|
| **FDE** | Runs the audit, drives source discovery, builds the baseline retrieval test, writes the readiness report |
| **Data Engineer** | Connector design, pipeline architecture, freshness/CDC strategy, lineage |
| **Data Owner / Steward** | Confirms accuracy, freshness commitments, fixes upstream data quality |
| **Domain SME** | Labels known-correct documents, defines what "answerable" means |
| **ML Engineer** | Embedding evaluation, reranking strategy, retrieval metric selection |
| **Platform / IT** | Access paths, SSO-scoped access, network path to data stores |
| **Security / Privacy / Legal** | PII, residency, retention, licensing, deletion obligations |
| **Product Owner** | Accepts the coverage/freshness trade-off and its business cost |

---

## 1.12 Practical Field Notes

- Start with **50 questions**, not 5,000 documents. Question-first beats corpus-first — it tells you what to even keep.
- Most "the AI can't answer that" complaints are corpus coverage problems wearing a model costume.
- Keep a **sample of 200 labeled documents** forever — it becomes the eval backbone in Phase 4 and the regression baseline in Phase 6.
- Watch for scan-of-a-print-out documents and email screenshots. They silently destroy chunk quality.
- Ask for the **raw source of truth** (database, repo, wiki), not a manually curated export. Curated exports go stale and lose structure.
- Time-box the data assessment. If it runs past two weeks, the corpus is too big to explore linearly — switch to sampling by topic.
- Record the exact embedding model name **and version** in the index. "text-embedding-3" is not a version.

---

## 1.13 Related Pages

- [AI SDLC index](AI-SDLC.md)
- Previous: [Phase 0 — Discovery & Scoping](00-discovery-and-scoping.md)
- Next: [Phase 2 — Infrastructure & Model Deployment](02-infrastructure-and-model-deployment.md)
- Source reference: [FDE-Responsibilities.md — Pillar A (data readiness tasks)](../FDE-Responsibilities.md)

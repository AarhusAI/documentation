---
title: RAG architecture
parent: Technical documentation
---

# RAG architecture

Retrieval-augmented generation (RAG) works in AarhusAI with external ingestion and retrieval outside of
[Open WebUI](https://github.com/open-webui/open-webui) into two dedicated microservices that share a single
[Qdrant](https://qdrant.tech/) index:

- [ingestion-service](https://github.com/AarhusAI/ingestion-service) - extracts, chunks, embeds and **writes** documents
  to Qdrant.
- [retrieval-agent](https://github.com/AarhusAI/retrieval-agent) - **reads** Qdrant at query time, optionally wrapping
  the search in an LLM-driven reasoning loop.

## Table of contents

1. [Overview](#1-overview)
2. [Architecture at a glance](#2-architecture-at-a-glance)
3. [The shared Qdrant contract](#3-the-shared-qdrant-contract)
4. [Ingestion path](#4-ingestion-path)
5. [Retrieval path](#5-retrieval-path)
6. [Configuration touchpoints](#6-configuration-touchpoints)
7. [Operations and health](#7-operations-and-health)
8. [Reference](#8-reference)

## 1. Overview

Previously Open WebUI ran the whole RAG flow internally: it extracted text (optionally through an external document
loader), chunked it, embedded it, stored vectors in its own per-knowledge collections, and ran a fixed linear retrieval
at query time.

Why the change:

- **Control over extraction and chunking.** The ingestion-service runs a [Haystack v2](https://haystack.deepset.ai/)
  pipeline with pluggable extraction engines (`tika`, `kreuzberg`, `pypdf`, `docling`, `unstructured`) and chunking
  strategies (token, markdown, sentence, …) selected by configuration. A content-based `auto` mode additionally routes
  layout-bound documents (flowcharts, diagrams, scanned forms) to a multimodal **vision** path (`vision-llm` /
  `hybrid-diagram`) while everything else takes a plain text extractor.
- **Hybrid search and reranking.** Ingestion can write a sparse vector alongside the dense one, letting retrieval use
  Qdrant's native RRF hybrid query plus optional cross-encoder reranking.
- **Agentic retrieval.** The retrieval-agent can wrap search in a [PydanticAI](https://ai.pydantic.dev/) loop that
  rewrites queries, decomposes them, grades relevance, and retries - or fall back to a fast linear pipeline.
- **Decoupling.** Either service can be swapped, scaled, or reconfigured without touching the Open WebUI fork.

> The older "document ingestion route" described in [Deployment](./installation/deployment.md) was only a text
> *extraction* proxy - Open WebUI still embedded and stored the result itself. The ingestion-service replaces that by
> taking over embedding and storage as well.

## 2. Architecture at a glance

Both services are standalone FastAPI microservices. They never call each other their only shared state is the Qdrant
index.

```mermaid
flowchart TB
  user([user])

  subgraph owb["Open WebUI (patched fork)"]
    upload["file upload /<br/>knowledge base"]
    chat["chat / RAG query"]
  end

  subgraph ingest_box["ingestion-service - PUT /api/v1/ingest"]
    ING["Haystack pipeline<br/>extract → chunk → embed → write"]
  end

  subgraph retrieve_box["retrieval-agent - POST /search"]
    RET["linear or agentic<br/>retrieval pipeline"]
  end

  S3[("S3 / MinIO<br/>raw files")]
  SIDE["Tika / Kreuzberg<br/>text extraction sidecars"]
  GOT["Gotenberg<br/>office→PDF render sidecar"]
  VLM["Vision LLM endpoint<br/>(multimodal, VISION_LLM_*)"]
  EMB["Embedding endpoint<br/>(embed.itkdev.dk / TEI / fastembed)"]
  LLM["LiteLLM proxy<br/>(agent + query generation)"]
  RRK["Reranker endpoint<br/>(embed.itkdev.dk /v1/rerank)"]
  QD[("Qdrant<br/>single collection: ingestion_files<br/>dense + optional sparse vectors")]

  user --> owb
  upload -->|"upload then {bucket,key}"| S3
  upload -->|"EXTERNAL_INGESTION_ENGINE=external"| ING
  chat -->|"RAG_EXTERNAL_RETRIEVAL_API_KEY"| RET

  ING -->|fetch by key| S3
  ING -->|HTTP extract| SIDE
  ING -.vision engines.-> GOT
  ING -.vision engines.-> VLM
  ING -->|embed docs| EMB
  ING -->|write points| QD

  RET -->|embed query| EMB
  RET -.->|agent decisions| LLM
  RET -.->|rerank candidates| RRK
  RET -->|search points| QD
```

Solid arrows are always-on; dashed arrows are optional. On the ingestion side, Gotenberg and the Vision LLM endpoint
are only used when a vision engine is selected (`vision-llm`, `hybrid-diagram`, or an `auto`-routed diagram). On the
retrieval side, the LLM is only consulted in agentic mode or when query generation is enabled, and the reranker only
when `ENABLE_RERANKING` is enabled.

## 3. The shared Qdrant contract

This is the most important thing to get right. Both services talk to the **same physical Qdrant collection**
(`ingestion_files` by default) but neither discovers the other's schema - the compatibility is entirely a matter of
matching configuration. A mismatch produces *silently wrong* results (garbage similarity scores, empty hits) rather
than errors.

The settings that must agree across ingestion-service and retrieval-agent:

| Setting | ingestion-service | retrieval-agent | Why it must match |
| --- | --- | --- | --- |
| Qdrant instance | `QDRANT_URI` | `QDRANT_URI` | Same store, or retrieval reads nothing |
| Collection | `QDRANT_INDEX` (`ingestion_files`) | `QDRANT_INDEX` (`ingestion_files`) | Must name the same physical collection |
| Dense model | `EMBEDDING_MODEL` | `EMBEDDING_MODEL` | Different models → incomparable vector spaces |
| Doc/query prefix | `EMBEDDING_PREFIX_DOC` (e.g. `"passage: "`) | `EMBEDDING_PREFIX_QUERY` (e.g. `"query: "`) | e5/nomic models expect their trained prefix |
| Sparse model | `SPARSE_EMBEDDING_MODEL` (when sparse on) | `SPARSE_QUERY_MODEL` (when hybrid on) | Sparse query vectors must match the indexed ones |

**Multitenancy.** Every chunk is written with a `meta.collection_name` payload (e.g. `file-abc`, or a knowledge-base
name) and Qdrant is configured for per-tenant subgraphs keyed on that field (`hnsw_config={"m": 0, "payload_m": 16}`).
The ingestion-service bootstraps the supporting keyword payload indexes at startup: `meta.collection_name` (the tenant
key, `is_tenant=True`), `meta.collection_type`, and `meta.languages` (ISO 639-1 codes surfaced by Kreuzberg, enabling
language filtering). At query time the retrieval-agent passes `collection_names` and filters on
`meta.collection_name ∈ collection_names`, so one Open WebUI knowledge base never bleeds into another even though they
share one physical collection.

> The dense embedding model is the contract that breaks most quietly. If ingestion indexed with
> `intfloat/multilingual-e5-large` and `"passage: "` prefixes, the retrieval-agent must query with the *same* model and
> the matching `"query: "` prefix - otherwise scores are meaningless but no error is raised.

## 4. Ingestion path

Open WebUI uploads the raw file to S3/MinIO first, then calls `PUT /api/v1/ingest` with the bucket and key (a multipart
body is the fallback for direct uploads). The S3 path is preferred because it keeps large files off the FastAPI
worker's heap. Requests are gated before any work happens: a per-`file_id` lock serializes concurrent ingests of the
same file, `MAX_UPLOAD_BYTES` (100 MB) caps both multipart parts and S3 fetches (checked via `head_object`), the
`S3_ALLOWED_BUCKETS` allow-list restricts which buckets may be read, and the caller's `collection_name` is validated
against the `user_id` / `file_id` it claims.

The request handler hands the file to a Haystack v2 pipeline built once at startup and cached as module state:

```mermaid
flowchart LR
  F["raw file<br/>(tempfile)"] --> C["Converter<br/>EXTRACTION_ENGINE"]
  C -->|Documents| S["Splitter<br/>CHUNK_SPLIT_BY"]
  S -->|chunks + meta| DE["Dense embedder<br/>EMBEDDING_PROVIDER"]
  DE -. optional .-> SE["Sparse embedder<br/>ENABLE_SPARSE_EMBEDDINGS"]
  DE --> W["Writer<br/>QdrantDocumentStore"]
  SE --> W
  W --> QD[("Qdrant")]

  classDef optional stroke-dasharray:5 5;
  class SE optional;
```

1. **Convert** - turn the raw file into Haystack `Document`s. The engine is chosen by `EXTRACTION_ENGINE`: `tika` and
   `kreuzberg` are HTTP sidecars in the parent stack, `pypdf` is in-process (PDF-only), `docling`/`unstructured` need
   optional dependencies, and `vision-llm`/`hybrid-diagram` are the multimodal vision engines. `auto` is not an engine
   but a routing *mode* that picks one per document. Kreuzberg additionally surfaces document metadata (title, authors,
   languages) and renders tables as Markdown. See [Vision extraction and content-based
   routing](#vision-extraction-and-content-based-routing) below.
2. **Chunk** - slice documents by `CHUNK_SPLIT_BY` (deployed default `markdown`; code default `token`). `token` mode
   measures chunk size in the embedding model's actual HuggingFace tokens (important for e5-large's 512-token cap once
   the `"passage: "` prefix is added); `markdown` mode splits on heading hierarchy first, records the heading
   breadcrumb in `meta.headers`, then token-packs each section over `CHUNK_SIZE`; `word`/`sentence`/`passage` use
   Haystack's built-in splitter. `CHUNK_SIZE` (400) and `CHUNK_OVERLAP` (80) bound chunk length, and the tokenizer is
   `TOKENIZER_MODEL` (falling back to `EMBEDDING_MODEL`) pinned to `TOKENIZER_REVISION` in deployments. Every chunk gets
   a sequential `meta.split_id`.
3. **Embed (dense)** - required. `EMBEDDING_PROVIDER` selects `openai-compat` (the current `embed.itkdev.dk` path),
   `fastembed` (in-process), or `tei`. `EMBEDDING_PREFIX_DOC` is prepended to each chunk, and `EMBEDDING_DIM` (1024 for
   e5-large) sizes the dense vector Qdrant allocates.
4. **Embed (sparse)** - both deployed stacks run `ENABLE_SPARSE_EMBEDDINGS=true`, so a second named vector
   (`text-sparse`, BM42 via `Qdrant/bm42-all-minilm-l6-v2-attentions`) is added per chunk, letting retrieval use
   Qdrant's native RRF hybrid query instead of client-side BM25. Dense-only is the code default; when disabled the
   writer sees dense-only chunks.
5. **Write** - `DocumentWriter` backed by `QdrantDocumentStore` writes one point per chunk carrying the dense vector
   (`text-dense`, and `text-sparse` when enabled) as named vectors.

**Idempotency.** With `overwrite=true` (the default), all existing points whose `meta.file_id` matches the request are
deleted before the new chunks are written; the same delete runs as teardown if any stage throws, and the whole
delete-then-write is guarded by the per-`file_id` lock so two concurrent requests can't interleave. So a
`status: true` response means the file is *fully* indexed, any other outcome means its chunks are absent (no partial
writes leak), and retrying the same `file_id` never duplicates vectors. Open WebUI's reindex action relies on this. The
success body is `{status: true, collection_name, chunks_count}`; a failure returns `{status: false, error, code}` where
`code` is one of `EXTRACTION_FAILED`, `EMBEDDING_FAILED`, `SPARSE_EMBEDDING_FAILED`, `QDRANT_WRITE_FAILED`,
`S3_FETCH_FAILED`, `INVALID_REQUEST`, or `PIPELINE_FAILED`.

### Vision extraction and content-based routing

Three engines cover documents whose meaning lives in their *layout* rather than their text — flowcharts, diagrams, and
scanned forms that a plain text extractor would flatten or drop:

- **`vision-llm`** renders the document to page images and reconstructs it with a multimodal LLM. Office formats are
  converted to PDF by the **Gotenberg** sidecar; the PDF is rasterized locally (pypdfium2) at `VISION_LLM_DPI` (150),
  capped at `VISION_LLM_MAX_PAGES` (20); the pages go to `VISION_LLM_API_BASE_URL` (model `VISION_LLM_MODEL`) in a
  single call. A prompt *profile* shapes the output — `diagram` (lanes/phases/steps plus a Mermaid flowchart),
  `general` (faithful full-page Markdown), or `ocr` (plain-text transcription), with `diagram-topology` and `figure`
  used internally by the hybrid engine. `VISION_LLM_LANGUAGE_HINT` (Danish) keeps the source language verbatim, and
  output exceeding `VISION_LLM_MAX_TOKENS` fails the extraction rather than truncating silently.
- **`hybrid-diagram`** (docx only) pairs the *authoritative* native text read straight from the package XML (verbatim
  labels, nothing OCR-guessed) with a vision-inferred Mermaid graph. It picks `diagram-topology` for a *vector*
  flowchart (labels are Word shapes) or `figure` for a *raster* PNG diagram (labels are pixels); a non-docx input falls
  through to plain `vision-llm`. It wraps `vision-llm`, so it needs the same `VISION_LLM_*` + Gotenberg config. This is
  the default diagram engine for `auto`.
- **`auto`** routes each document by inspecting it (`detectors.py`). A `.docx` is sent to
  `EXTRACTION_ROUTER_DIAGRAM_ENGINE` (default `hybrid-diagram`) when either signal fires; everything else goes to
  `EXTRACTION_ROUTER_DEFAULT` (`kreuzberg` in deployments):
  - **Vector flowchart** - drawing/text-box shapes ≥ `EXTRACTION_ROUTER_MIN_TEXTBOXES` (20) **and** drawing-to-body
    word ratio ≥ `EXTRACTION_ROUTER_DRAWING_RATIO` (2.0).
  - **Raster figure** - body images ≥ `EXTRACTION_ROUTER_MIN_BODY_IMAGES` (1) with the largest `<wp:extent>` display
    area ≥ `EXTRACTION_ROUTER_MIN_IMAGE_EMU` (`1_500_000_000_000` EMU² ≈ 1.79 in²). The area floor — not a count —
    rejects decoration; header/footer logos are excluded for free because only `word/document.xml` is read. The opt-in
    `EXTRACTION_ROUTER_MIN_IMAGE_WORD_RATIO` (0 = off) adds an image-area-to-body-words guard for corpora with large
    decorative hero photos.

```mermaid
flowchart TD
  A[document] --> B{".docx?"}
  B -- no --> D[EXTRACTION_ROUTER_DEFAULT<br/>kreuzberg]
  B -- yes --> C{"vector-flowchart<br/>OR raster-figure<br/>signal?"}
  C -- no --> D
  C -- yes --> E[EXTRACTION_ROUTER_DIAGRAM_ENGINE<br/>hybrid-diagram]
  E --> F{"vector vs raster"}
  F -- vector --> G[diagram-topology profile]
  F -- raster --> H[figure profile]
```

**Seeing what was chosen.** The `/api/v1/extract` response echoes the `engine` and `profile` used, every chunk written
to Qdrant carries `meta.extractor` / `meta.vision_profile`, and `DEBUG=true` logs the per-document routing decision
(detector signals → engine/profile) to the container logs.

There is also a developer-facing `POST /api/v1/extract` probe that runs only the converter and returns the raw
extracted documents - useful for comparing extraction engines without a full ingest. It takes an optional `engine`
override (any concrete engine, but not `auto`) and an optional `profile` (accepted only by the vision engines), and
responds with `{status, engine, profile, documents}`.

## 5. Retrieval path

Open WebUI calls `POST /search` (authenticated with `RAG_EXTERNAL_RETRIEVAL_API_KEY`) with either explicit `queries` or
the raw chat `messages`, plus the `collection_names` to search and a `k`. The response is one document list per query,
each with a parallel `distances` list -- higher always means more relevant, but the score *scale* depends on the active
path: normalized cosine in `[0, 1]` for plain dense search, Qdrant's raw RRF fusion scores when hybrid is enabled, and
the cross-encoder's relevance scores when reranking is enabled. `ENABLE_AGENTIC_RAG` selects which pipeline runs.

### Linear mode (`ENABLE_AGENTIC_RAG=false`)

A fast, deterministic pipeline:

```mermaid
flowchart TD
  A[POST /search] --> A1{messages +<br/>query generation?}
  A1 -- yes --> A2[LLM: generate<br/>optimized queries]
  A1 -- no --> A3[explicit queries /<br/>last user message]
  A2 --> B[Embed dense<br/>+ optional sparse]
  A3 --> B
  B --> D{hybrid?}
  D -- no --> Cdense[Qdrant dense search]
  D -- yes --> S{collection has<br/>text-sparse?}
  S -- yes --> Cnative[Qdrant Query API<br/>server-side RRF]
  S -- no --> Cbm25[dense search +<br/>client-side BM25 RRF]
  Cdense --> F{rerank?}
  Cnative --> F
  Cbm25 --> F
  F -- yes --> G[cross-encoder rerank]
  F -- no --> H[dedup by MD5 + limit k]
  G --> H
  H --> I[Response]
```

The hybrid branch is capability-aware: if the collection carries a `text-sparse` vector (because ingestion wrote one),
it uses Qdrant's server-side RRF; otherwise it falls back to scrolling the filtered content into memory for client-side
BM25 fusion. When reranking is on, the initial fetch is `k × INITIAL_RETRIEVAL_MULTIPLIER` candidates, rescored down
to `k` by an external `/v1/rerank` endpoint (default `embed.itkdev.dk`); if that call fails the pipeline falls back to
the unranked candidates rather than erroring. Results are deduplicated by content hash.

### Agentic mode (`ENABLE_AGENTIC_RAG=true`)

A PydanticAI tool-calling loop that reuses the *same* retrieval helpers but lets an LLM drive query formulation and
relevance grading:

```mermaid
flowchart TD
  A[POST /search] --> B[Build prompt from last<br/>N conversation messages]
  B --> C[Agent: generate 1-2 queries]
  C --> D{retrieve tool called?}
  D -- no --> FB[parse fallback queries<br/>+ direct vector search]
  D -- yes --> H["retrieve(queries) tool<br/>embed → search → ± rerank"]
  H --> H4[return truncated previews<br/>full results kept side-channel]
  H4 --> K{any result on-topic?}
  K -- yes --> M[dedup full results + limit k]
  K -- no --> N{iter < AGENT_MAX_ITERATIONS?}
  N -- yes --> C
  N -- no --> M
  FB --> M
  M --> O[Response<br/>full text from side-channel]
```

Key behaviours:

- The agent is told to generate 1–2 queries, call `retrieve` once, **accept** if *any* returned document is on-topic,
  and only **retry** with rewritten queries when results are completely off-topic.
- A **side-channel** (`AgentDeps.full_results`) accumulates the full retrieval results across iterations, while the tool
  only returns truncated previews to the LLM (`AGENT_PREVIEW_K` items, `AGENT_TOOL_PREVIEW_CHARS` each) so the context
  window doesn't balloon on retries. The final response uses the full text, not the previews.
- If the model emits queries as plain text instead of a tool call, a **fallback** parses them and runs a direct vector
  search (rerank skipped).
- `AGENT_TIMEOUT` is wall-clock; on timeout whatever was already retrieved is returned (partial results, not a 500).
- Agentic mode adds roughly 2–5× latency and 2–4× token cost per query.

## 6. Configuration touchpoints

All settings are environment variables. The tables below show only the cross-service and Open WebUI-facing ones; each
repo's `.env.example` is the full list.

### Open WebUI → the two services

| Open WebUI variable | Points at | Must equal |
| --- | --- | --- |
| `EXTERNAL_INGESTION_ENGINE=external` | ingestion-service | - (switch that enables external ingestion) |
| `EXTERNAL_INGESTION_API_KEY` | ingestion-service | ingestion-service `API_KEY` |
| `RAG_EXTERNAL_RETRIEVAL_API_KEY` | retrieval-agent | retrieval-agent `API_KEY` |

In the parent `docker-compose`, a single `INGESTION_API_KEY` is forked into the ingestion-service's `API_KEY` and Open
WebUI's `EXTERNAL_INGESTION_API_KEY`; likewise `RETRIEVAL_API_KEY` is forked into the retrieval-agent's `API_KEY` and
Open WebUI's `RAG_EXTERNAL_RETRIEVAL_API_KEY` - you do not set those by hand. The retrieval-agent's *agent LLM* key is
separate (`RETRIEVAL_AGENT_API_KEY` → `AGENT_API_KEY`).

### ingestion-service (selected)

Defaults below are the values the deployed AarhusAI stacks set; where they differ from the service's own
code/`.env.example` default, the Notes column says so.

| Variable | Default | Notes |
| --- | --- | --- |
| `API_KEY` | *(required)* | Must equal Open WebUI's `EXTERNAL_INGESTION_API_KEY` |
| `QDRANT_INDEX` | `ingestion_files` | Physical collection; must match retrieval-agent |
| `EMBEDDING_MODEL` | `intfloat/multilingual-e5-large` | Must match retrieval-agent |
| `EMBEDDING_DIM` | `1024` | Dense vector size; must match the model and retrieval-agent |
| `EMBEDDING_PREFIX_DOC` | `"passage: "` | Doc-side prefix (keep the trailing space) |
| `EXTRACTION_ENGINE` | `auto` (dev) / `kreuzberg` (server) | Full set `tika`/`pypdf`/`kreuzberg`/`docling`/`unstructured`/`vision-llm`/`hybrid-diagram`/`auto`; code default `tika` |
| `EXTRACTION_ROUTER_DEFAULT` | `kreuzberg` | Non-diagram engine, consulted only when `EXTRACTION_ENGINE=auto` |
| `EXTRACTION_ROUTER_DIAGRAM_ENGINE` | `hybrid-diagram` | Diagram engine, consulted only when `EXTRACTION_ENGINE=auto` |
| `VISION_LLM_API_BASE_URL` | *(empty)* | Multimodal endpoint; empty disables the vision engines |
| `VISION_LLM_MODEL` | *(endpoint-specific)* | Model served by the vision endpoint (`gemma4-nvfp4` code default) |
| `GOTENBERG_URL` | `http://gotenberg:3000` | office→PDF sidecar for the vision engines |
| `CHUNK_SPLIT_BY` | `markdown` | `token`/`markdown`/`word`/`sentence`/`passage`; code default `token` |
| `CHUNK_SIZE` | `400` | Chunk length (HF tokens in token/markdown modes) |
| `CHUNK_OVERLAP` | `80` | Overlap between chunks |
| `TOKENIZER_REVISION` | *(pinned)* | Deploys pin the e5-large tokenizer SHA; empty = Hub HEAD |
| `ENABLE_SPARSE_EMBEDDINGS` | `true` | Adds the sparse vector enabling native hybrid retrieval; code default `false` |
| `SPARSE_EMBEDDING_MODEL` | `Qdrant/bm42-all-minilm-l6-v2-attentions` | BM42; must match retrieval-agent when hybrid is on |
| `S3_ALLOWED_BUCKETS` | `openwebui` | Allow-list of buckets the service may fetch from (empty = unenforced) |

The vision engines (`vision-llm` / `hybrid-diagram`) and the Gotenberg sidecar run on the dev stack
(`EXTRACTION_ENGINE=auto`); the server stack uses plain `kreuzberg` and ships neither. Both stacks enable sparse
embeddings and `markdown` chunking, so the code defaults (`tika`, dense-only, `token`) describe an unconfigured service
rather than either deployment.

### retrieval-agent (selected)

| Variable | Default | Notes |
| --- | --- | --- |
| `API_KEY` | *(required)* | Must equal Open WebUI's `RAG_EXTERNAL_RETRIEVAL_API_KEY` |
| `QDRANT_INDEX` | `ingestion_files` | Must match ingestion-service |
| `EMBEDDING_MODEL` | `intfloat/multilingual-e5-large` | Must match ingestion-service |
| `EMBEDDING_PREFIX_QUERY` | `"query: "` | Query-side prefix |
| `ENABLE_HYBRID_SEARCH` | `false` | Native sparse+dense when available, else BM25 fallback |
| `ENABLE_RERANKING` | `false` | Cross-encoder rerank stage |
| `ENABLE_QUERY_GENERATION` | `true` | LLM query generation from chat (linear pipeline) |
| `ENABLE_AGENTIC_RAG` | `false` | Route to the agentic loop instead of the linear pipeline |
| `AGENT_MODEL` | `gpt-4o-mini` | Model for agent decisions / query generation |

## 7. Operations and health

Both services expose the same probe pair:

- `GET /health` - liveness; 200 whenever the process is up.
- `GET /health/ready` - readiness; 503 until dependencies are reachable. The ingestion-service additionally waits for
  its Haystack pipeline to warm up (with sparse enabled — the deployed norm — the BM42 embedder pulls its ~80 MB model
  from HuggingFace, cached in the `ingestion_model_cache` volume so it survives restarts); the retrieval-agent verifies
  Qdrant connectivity. This keeps Docker/Kubernetes from routing traffic during cold start. Readiness gates only the
  pipeline warm-up and Qdrant — Gotenberg and the Vision LLM endpoint are *not* probed, so a vision dependency being
  down surfaces per-request as an `EXTRACTION_FAILED` rather than blocking startup.

## 8. Reference

- ingestion-service - <https://github.com/AarhusAI/ingestion-service>
- retrieval-agent - <https://github.com/AarhusAI/retrieval-agent>
- [Gotenberg](https://gotenberg.dev/) - office→PDF render sidecar used by the vision extraction engines
- [Patches](./patches.md) - the Open WebUI patches applied in this stack

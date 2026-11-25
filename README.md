# ISA: Independent Sales Agent (Engineering Memoir)

A write-up of a conversational sales agent I designed and built single-handedly in 12 weeks (April to July 2026) as an AI Engineer at TECH University.

This repository contains **documentation only**. The source code is proprietary and is not included. The memoir has no secrets, credentials, hostnames, or company sales content. It covers the engineering: what was built, why it was decided that way, and what I learned.

**Read the memoir:** [ISA-projetc-memoir](./ISA-project-memoir.pdf)

## What it is

A WhatsApp-based sales agent for an online higher-education institution's degree programs. Turn by turn, it:

- keeps memory of the conversation,
- scores the lead's purchase intent,
- decides which sales phase the conversation is in (opening, discovery, recommendation, objections, closing),
- composes each reply using RAG over three knowledge bases (program dossiers, FAQs, sales manual).

## Stack

Python 3.11 · LangGraph · LangChain · Qdrant · OpenAI embeddings (text-embedding-3-large) · FastAPI · PostgreSQL checkpointer · Streamlit · Docker · Azure Container Apps · Elastic

## Architecture at a glance

- **Two layers:** an online runtime (API + graph, latency-sensitive) and an offline ingestion pipeline (data sources to JSONL to vector store).
- **Graph per turn:** planner, parallel retrievers (FAQ, sales manual, program content, pricing), then a single synthesizer that writes the reply.
- **Memory:** verbatim window of recent messages plus a background-generated running summary.
- **Robustness at the API boundary:** idempotency by message ID, per-thread locking and typed errors (503/502/504/500).
- **Generic RAG by contract:** adding a new knowledge base takes a config plus an adapter, with no engine changes.

## Key numbers

| | |
|---|---|
| Duration | 12 weeks, solo |
| Code | ~9,700 lines of Python |
| Tests | 163, offline and deterministic |
| Documentation | 10 Architecture Decision Records |
| LLM calls per turn | up to 3 synchronous + 1 background |
| Capacity (burst) | ~110 concurrent turns; the bottleneck was LLM provider capacity, not the agent |

## Main lessons

1. **The LLM classifies, the code dispatches.** Ask the model for judgments over closed domains and derive behaviour deterministically, so the system stays auditable and testable.
2. **Put robustness at the boundary.** Idempotency, serialization, and error mapping belong in the API, not in the graph.
3. **Design for hard limits.** Budget the context deterministically instead of truncating blindly.
4. **Degrade to empty, not to 500.** A failing retriever should not kill a turn.
5. **Chunk by document structure, not by token count.**
6. **Schedule the security review.** One day of systematic review found issues that weeks of reactive patching would have missed.
7. **Measure for real:** actual gateway usage for cost, bursts for capacity, full-cycle timing for latency.

## About me

Blas Sicilia, Data & AI Engineer and MSc student in Artificial Intelligence at SDU, based in Odense, Denmark.

- Email: blas.sicilia@gmail.com
- LinkedIn: https://www.linkedin.com/in/blas-sicilia-117b48258/

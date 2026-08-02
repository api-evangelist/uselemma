---
name: uselemma-debug-agent-traces
description: >-
  Find and debug a failing AI-agent execution in Lemma: search traces with
  structural/behavioral filters or semantic similarity, load the trace with
  its spans, walk the thread, and link the trace to detected issues.
api: openapi/uselemma-platform-api-openapi-original.json
operations: [search_traces, semantic_search_traces, get_trace, list_trace_spans, get_thread_traces, list_trace_issue_occurrences, get_ingest_status]
generated: '2026-07-21'
method: generated
source: openapi/uselemma-platform-api-openapi-original.json + conventions/uselemma-conventions.yml
---

# Debug agent traces in Lemma

All requests go to `https://api.uselemma.ai` with `Authorization: Bearer <lma_... project API key>`.
Every search is project-scoped: pass `project_id=<proj_...>`.

1. **Find candidate traces** — `search_traces` (`GET /traces/search`) with
   behavioral filters: `error_only=true`, `agent_name`, `start`/`end`,
   `min_duration_ms`/`max_duration_ms`, `min_spans`/`max_spans`, `min_tokens`,
   `limit`. For "find runs like this" queries use `semantic_search_traces`
   (`GET /traces/semantic-search`), which matches over embedded trace summaries.
2. **Load the trace** — `get_trace` (`GET /traces/{trace_id}`) returns the
   trace with spans and stats. `list_trace_spans`
   (`GET /traces/{trace_id}/spans`) returns the ordered span tree alone —
   generations carry model/prompt/completion, tools carry args/results.
3. **Walk the conversation** — `get_thread_traces`
   (`GET /traces/{trace_id}/thread`) returns every ready trace in the thread,
   oldest first (empty for standalone traces); `get_thread_trace_neighbors`
   gives just previous/next.
4. **Cross-reference detected issues** — `list_trace_issue_occurrences`
   (`GET /traces/{trace_id}/issue_occurrences`) shows which known issues this
   trace evidences.
5. **If a trace is missing** — `get_ingest_status`
   (`GET /traces/ingest-status?otel_trace_id=...`) reports enqueued, ingested,
   ready, or not_found for the delivery pipeline.

Remember the contract: one agent execution = one trace; re-sending the same
ingest payload with the same span IDs is idempotent (see
`conventions/uselemma-conventions.yml`).

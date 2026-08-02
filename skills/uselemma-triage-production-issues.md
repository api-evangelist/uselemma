---
name: uselemma-triage-production-issues
description: >-
  Triage the AI-agent issues Lemma has detected for a project: list open
  issues, pull one issue's evidence and root-cause context, export it as
  markdown for a fix ticket, and close it out when resolved.
api: openapi/uselemma-platform-api-openapi-original.json
operations: [list_issues, get_issue, get_issue_metric_series, get_issue_chat_context, get_issue_markdown, set_issue_status]
generated: '2026-07-21'
method: generated
source: openapi/uselemma-platform-api-openapi-original.json + conventions/uselemma-conventions.yml
---

# Triage production issues in Lemma

All requests go to `https://api.uselemma.ai` with `Authorization: Bearer <lma_... project API key>`.
Missing credentials return `401`; objects outside your tenant return `404`.

1. **List the open issues** — `list_issues` (`GET /issues?project_id=<proj_...>`).
   Filter with `status` to focus on unresolved. For a dashboard-ready view use
   `list_dashboard_issues` instead.
2. **Inspect one issue** — `get_issue` (`GET /issues/{issue_id}`). The
   `validation` field is `null` until a validation row exists; when present it
   carries `status`, `confidence`, `evidenceSummary`, `checkedTools`, `caveats`.
3. **Check the trend** — `get_issue_metric_series`
   (`GET /issues/{issue_id}/metric-series`) returns zero-filled daily
   occurrence buckets for the last 30 days: is it growing or fading?
4. **Pull full debugging context** — `get_issue_chat_context`
   (`GET /issues/{issue_id}/chat-context`) returns the issue, recent
   occurrences, aggregate stats, and per-trace bundles (trace + stats + spans).
5. **Export for a ticket or PR** — `get_issue_markdown`
   (`GET /issues/{issue_id}/markdown`) builds ready-to-paste markdown.
6. **Close it out** — `set_issue_status` (`PATCH /issues/{issue_id}`).
   `resolved` and `dismissed` close the issue and append close feedback.

Errors: `400` invalid parameters/body, `401` bad credentials, `404` unknown or
cross-tenant issue (see `errors/uselemma-problem-types.yml`).

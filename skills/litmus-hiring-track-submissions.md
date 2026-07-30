---
name: Track Litmus submissions for a role
description: >-
  Review a role's hiring pipeline in Litmus — check stage counts, list recent
  submissions, and pull one submission's full report and artifacts.
api: openapi/litmus-hiring-openapi.yml
mcp: mcp/litmus-hiring-mcp.yml
operations: [listRoles, getSubmission]
mcp_tools: [list_submissions, get_submission, get_pipeline_status, list_candidates, get_candidate_status]
---

# Track Litmus submissions for a role

Use this skill to monitor a technical-hiring pipeline on the Litmus platform.

## Authentication
- REST: send `Authorization: Bearer litmus_sk_...` (create the key in the
  dashboard under Settings -> API Keys). A 401 means the key is missing,
  malformed, or revoked.
- MCP: connect the hosted server at `https://litmushiring.com/mcp/<org-id>/`
  (keep the trailing slash) via Clerk OAuth or the same API key.

## Steps
1. Find the role. Call `listRoles` (`GET /api/v1/roles`) — or the MCP tool
   `list_candidates` / `get_pipeline_status` — to identify the `role_id`.
2. Read the pipeline. Call MCP `get_pipeline_status(role_id)` for disjoint
   stage counts (invited, in_progress, pending_review, advanced, rejected,
   expired) to spot bottlenecks.
3. List submissions. Call MCP `list_submissions(role_id=..., status=..., limit,
   cursor)`; page with `nextCursor` until it is null (results are ordered by
   submitted-at descending).
4. Inspect one submission. Call `getSubmission` (`GET /api/v1/submissions/{id}`)
   or MCP `get_submission(submission_id)` for the candidate, assessment, role,
   report summary, and artifact URLs (`zipUrl`, `videoUrl`, `videoTranscript`).

## Rules
- Webhooks are not available; poll `getSubmission` or `list_submissions` to
  detect new results. Do not assume push delivery.
- Rate limiting is currently unrestricted, but avoid excessive polling.
- All MCP tool calls are audited in the dashboard.

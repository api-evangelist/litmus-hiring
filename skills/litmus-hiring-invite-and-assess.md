---
name: Author an assessment and invite a candidate
description: >-
  Create a project-based technical assessment from files in Litmus and invite a
  candidate to complete it for an open role.
api: openapi/litmus-hiring-openapi.yml
mcp: mcp/litmus-hiring-mcp.yml
operations: [listRoles, createInvite]
mcp_tools: [upload_assessment, get_assessment_files, update_assessment_files]
---

# Author an assessment and invite a candidate

Use this skill to stand up a technical assessment and send an invitation on the
Litmus platform.

## Authentication
- Send `Authorization: Bearer litmus_sk_...` (organization-scoped key from
  Settings -> API Keys). A 401 means missing/malformed/revoked credentials.

## Steps
1. Pick the role. Call `listRoles` (`GET /api/v1/roles`) to get the target
   `role_id`.
2. Create the assessment. Call MCP `upload_assessment(name, files, language?,
   time_limit?, role_id?)` — it returns `{ assessmentId }` and is available
   immediately as a draft.
3. (Optional) Revise files. Read with `get_assessment_files(assessment_id)`,
   then `update_assessment_files(assessment_id, files, expected_version?)`.
   Pass `expected_version` for optimistic concurrency — a mismatch means
   someone else changed the file set; re-read and retry.
4. Invite the candidate. Call `createInvite` (`POST /api/v1/invites`) with the
   candidate and role details.

## Rules
- `POST /invites` is NOT idempotent — Litmus documents no idempotency-key
  contract. Do not blindly retry a failed invite; confirm state first (poll
  candidates/submissions) to avoid double-inviting.
- Verify the candidate's progress by polling submissions; webhooks are not
  available.

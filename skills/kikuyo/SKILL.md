---
name: kikuyo
description: Manage Kikuyo organizations, projects, feedback items, statuses, and comments through the Kikuyo MCP server tools. Use when triaging user feedback, listing or filtering feature requests, submitting feedback, updating statuses, or replying to feedback threads in Kikuyo. Don't use for local `kikuyo` CLI shell commands unless MCP tools are unavailable.
---

# Kikuyo MCP Workflow

Operate Kikuyo feedback boards through the `kikuyo` MCP server tools (`list_orgs`, `get_org`, `list_projects`, `get_project`, `list_feedback`, `get_feedback`, `create_feedback`, `edit_feedback`, `update_feedback_status`, `list_comments`, `create_comment`).

## Procedure

1. **Resolve identifiers before reading or mutating.**
   - `org` and `project` parameters accept either a slug (e.g. `digitalvibes`, `kikuyo`) or a UUID.
   - If the target org or project is unknown, call `list_orgs` first, then `list_projects` for that org. Never guess slugs from repository names without verifying via `list_orgs` / `list_projects`.
2. **Inspect full item context before mutating.**
   - `list_feedback` returns summary items without full details or discussion context.
   - Before calling `update_feedback_status`, `edit_feedback`, or `create_comment`, call `get_feedback` (and `list_comments` when replying or triaging) using the item's UUID `id`.
3. **Use only valid status values.**
   - `status` must be one of: `pending`, `planned`, `in_progress`, `completed`, `rejected`.
   - Do not invent aliases such as `open`, `done`, `closed`, or `archived`.
4. **Prevent duplicates when filing feedback.**
   - Before calling `create_feedback`, call `list_feedback` on the target project and check whether a matching request already exists. If an identical item exists, report its UUID, title, and status instead of creating a duplicate.
5. **Respect threading limits on comments.**
   - Kikuyo comments support a single reply level. When passing `parent_id` to `create_comment`, pass a top-level comment UUID from `list_comments`, never a nested reply's UUID.
6. **Respect edit permissions.**
   - `edit_feedback` only succeeds for the item creator or an org admin/owner. If `edit_feedback` returns a 403 error, leave a clarifying comment via `create_comment` or update status if authorized rather than retrying.

## Examples

### Resolving project before listing or filing feedback

- **Bad:** Calling `list_feedback({ org: "my-org", project: "ios-app" })` directly from a repo folder name.
- **Why:** Repository names often differ from Kikuyo org/project slugs, causing 404 API errors or filing feedback into the wrong board.
- **Better:** Call `list_orgs()` -> `list_projects({ org: "acme" })` -> `list_feedback({ org: "acme", project: "acme-ios", status: "pending" })`.
- **Not a finding:** Calling `list_feedback` directly when the user's prompt explicitly provides both the exact org slug and project slug.

### Updating status after shipping a feature

- **Bad:** Calling `update_feedback_status({ id: "...", status: "done" })` without reading the item.
- **Why:** `"done"` fails schema validation (valid value is `"completed"`), and skipping `get_feedback` risks closing an item whose body describes a broader scope than what shipped.
- **Better:** Call `get_feedback({ id })` to confirm the shipped change satisfies the request, then `update_feedback_status({ id, status: "completed" })`.

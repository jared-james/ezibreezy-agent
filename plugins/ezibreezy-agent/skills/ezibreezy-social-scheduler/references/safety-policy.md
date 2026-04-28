# Safety Policy

Use this policy before EziBreezy mutations.

## Default Policy

- Prefer drafts. Set `saveAsDraft: true` for new content unless the user explicitly asks to schedule or publish.
- Prefer read-only discovery before mutation: auth, workspace, integration, capabilities, options, and relevant current state.
- Never schedule without explicit date, time, and timezone.
- Never publish immediately from ambiguous wording such as "post this" unless the user clearly means publish now.
- Never use `--yes` on behalf of the user until the current task includes explicit confirmation for the exact action.

## Non-Negotiable Agent Rules

- Default ambiguous content creation to `saveAsDraft: true`.
- Never schedule without an explicit date, time, and timezone in the current task or user-provided source of truth.
- Never immediate-publish without explicit publish-now intent in the current task.
- Never send inbox replies, moderation actions, approval notifications, client review emails, or bulk read-all without confirmation.
- Never delete content, failed inbox messages, grid items, media, media folders/tags, or hashtag groups without confirmation.
- Always inspect integration capabilities before setting platform-specific fields.
- Always inspect dynamic options when a capability field includes `optionKey`.
- Always upload local media through EziBreezy before attaching it to posts.
- Always return created/changed IDs and status after mutations.
- Treat API keys, CLI login tokens, bearer tokens, refresh tokens, presigned URLs, and raw provider payloads as sensitive.

## Confirmation Required

Ask for confirmation immediately before:

- `content:publish`, `content:retry`, `content:archive`, `content:restore`, `content:delete`.
- `content:schedule` if the requested date/time/timezone or target integration is ambiguous.
- `content:request-approval`, `content:resubmit`, `content:withdraw`, `content:reopen-client-review`.
- `client-reviews:send`, `client-reviews:resend`, `client-reviews:cancel`, `client-reviews:override`.
- `inbox:reply`, `inbox:moderate`, `inbox:retry-message`, `inbox:delete-failed`, `inbox:read-all`.
- `media:delete`, `media:bulk-delete`, `media:bulk-archive`, `media:bulk-move`, `media:bulk-tag`, `media:bulk-untag`.
- `media:folders:delete`, `media:tags:delete`, `hashtags:delete`.
- `grid:delete`, `grid:promote`, `grid:reorder`, `grid:remove-cover`.
- `analytics:report`.

The confirmation prompt should include action, IDs, workspace, integration, scheduled time if any, recipient/reviewer impact if any, and whether the effect is public, destructive, or broad. Do not infer confirmation from older conversation context if the target IDs, integration, or effect changed.

## CLI-Enforced `--yes`

Only append `--yes` after confirmation for:

- `content:request-approval`
- `content:resubmit`
- `content:publish`
- `client-reviews:send`
- `client-reviews:resend`
- `analytics:report`
- `inbox:read-all`
- `inbox:reply`
- `inbox:moderate`
- `inbox:retry-message`
- `inbox:delete-failed`
- `media:bulk-delete`
- `grid:delete`
- `grid:promote`

Some risky commands do not require `--yes`; the agent still must confirm them.

## Credential Handling

- Prefer `EZIBREEZY_API_KEY` and `EZIBREEZY_API_URL` environment variables for automation.
- For hosted MCP, prefer the MCP client's browser authentication flow. Browser-authenticated MCP has been validated with Codex and Claude Code.
- Use `EZIBREEZY_API_KEY` for MCP only when browser authentication is not supported or for server-side automation.
- Do not use stored browser CLI tokens as the MCP auth path.
- Browser-approved MCP grants can be revoked in EziBreezy **Settings -> Developer**.
- Prefer browser login for local human sessions.
- Do not ask the user to paste API keys, CLI login tokens, OAuth codes, bearer tokens, refresh tokens, presigned URLs, provider tokens, scopes, or raw provider payloads into chat.
- Do not print token values from files or command output.
- Treat media view/download URLs as sensitive, especially if presigned or temporary.
- If a command output includes sensitive values, summarize the non-sensitive state instead of echoing the raw payload.

## External Visibility

Public or externally visible actions include publishing content, retrying failed publishes, sending inbox replies, moderating comments/messages, sending or resending approval/client-review notifications, and analytics/report exports that leave the local machine or are shared with clients.

If the user asks for one of these but omits business-critical details, ask a targeted question instead of guessing.

## Generated Media

- Create, choose, or upload assets separately before scheduling or publishing.
- Inspect uploaded media IDs and integration requirements before attaching media to content.
- Do not represent generated media as approved client assets unless the user says it is approved.
- For platforms requiring media/title/options, resolve those requirements before scheduling or publishing.

## Bulk Actions

- Treat any operation over multiple content, media, inbox, grid, reviewer, or analytics items as broad.
- Confirm the count and explicit ID list or filter before execution.
- Avoid `{}` filters for bulk actions unless the user explicitly confirms the full scope.

## Mutation Output

After every mutation, report:

- Created or changed IDs.
- Workspace ID and integration ID when applicable.
- Resulting content/media/inbox/grid/approval status when available.
- Scheduled time with timezone when scheduling.
- External effect, such as public publish, sent reply, reviewer notification, moderation, report file, or deletion.
- Next review or recovery step if the action failed or remains pending.

# MCP Tools

Use this reference only when the current agent environment already has the hosted EziBreezy MCP server connected as `ezibreezy`.

Hosted endpoint:

```text
https://api.ezibreezy.com/mcp
```

Authentication:

- Preferred: use your MCP client's **Authenticate** flow. It opens EziBreezy in the browser and asks the user to approve access. This has been validated with Codex and Claude Code.
- Browser-approved MCP access uses 6-hour access tokens with 90-day rotating refresh tokens. Organization admins can revoke active AI tool connections in EziBreezy **Settings -> Developer**.
- Fallback: use `EZIBREEZY_API_KEY` only when the MCP client does not support browser authentication, or for server-side automation.

CLI login is separate. `ezibreezy auth:login` signs in the CLI, not MCP.

Do not paste API keys, OAuth codes, bearer tokens, refresh tokens, presigned URLs, or provider payloads into chat or project files.

## Operating Order

1. Prefer connected MCP tools for agent-native reads and supported low-risk mutations.
2. Fall back to `@ezibreezy/cli` when MCP is not connected or does not expose the needed workflow.
3. Use the public API directly only when both MCP and CLI are insufficient.
4. Keep all normal safety rules: draft-first, explicit timezone for scheduling, capability inspection before platform-specific settings, and confirmation before externally visible, destructive, or broad actions.

## Tool Map

| MCP tool                       | CLI fallback                                                                                                                                                                       | Notes                                                                                                                                                                                |
| ------------------------------ | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| `list_workspaces`              | `ezibreezy workspaces:list`                                                                                                                                                        | Read-only workspace discovery.                                                                                                                                                       |
| `list_integrations`            | `ezibreezy integrations:list --workspace <workspaceId>`                                                                                                                            | Read-only connected account list.                                                                                                                                                    |
| `get_integration_capabilities` | `ezibreezy integrations:capabilities --workspace <workspaceId> --integration <integrationId>`                                                                                      | Required before platform-specific content settings.                                                                                                                                  |
| `get_integration_options`      | `ezibreezy integrations:options --workspace <workspaceId> --integration <integrationId> --key <optionKey>`                                                                         | Fetches dynamic option keys returned by capabilities, such as boards, creator info, locations, catalogs, products, or YouTube categories.                                            |
| `create_draft_content`         | `ezibreezy content:create --workspace <workspaceId> --json create-content.json`                                                                                                    | MCP forces draft behavior. CLI payload should set `saveAsDraft: true`.                                                                                                               |
| `list_content`                 | `ezibreezy content:list --workspace <workspaceId>`                                                                                                                                 | Read-only public content statuses.                                                                                                                                                   |
| `get_content`                  | `ezibreezy content:get --workspace <workspaceId> --id <contentId>`                                                                                                                 | Read-only sanitized content detail.                                                                                                                                                  |
| `get_content_counts`           | `ezibreezy content:failed-count --workspace <workspaceId>`, `content:pending-approval-count`                                                                                       | Read-only failed content count, plus pending approval count when requested on an Agency or Scale workspace plan.                                                                     |
| `get_content_history`          | `ezibreezy content:history --workspace <workspaceId> --id <contentId>`                                                                                                             | Read-only approval/client-review activity timeline. Requires an Agency or Scale workspace plan. Does not comment, react, decide, request, or withdraw.                               |
| `list_approval_history`        | `ezibreezy content:approval-history --workspace <workspaceId>`                                                                                                                     | Read-only resolved approval/client-review history. Requires an Agency or Scale workspace plan.                                                                                       |
| `get_approval_history_detail`  | `ezibreezy content:approval-history:internal --workspace <workspaceId> --record <recordId>` or `content:approval-history:client-batch --workspace <workspaceId> --batch <batchId>` | Read-only internal approval or client-batch history detail. Requires an Agency or Scale workspace plan.                                                                              |
| `list_client_review_batches`   | `ezibreezy client-reviews:open --workspace <workspaceId>`                                                                                                                          | Read-only open unsent client review batches for admin/editor users. Requires an Agency or Scale workspace plan. Does not create, send, resend, cancel, override, comment, or delete. |
| `get_client_review_batch`      | `ezibreezy client-reviews:get --workspace <workspaceId> --batch <batchId>`                                                                                                         | Read-only client review batch summary. Requires an Agency or Scale workspace plan. Does not mutate the batch.                                                                        |
| `list_taxonomy`                | `ezibreezy taxonomy:tags --workspace <workspaceId>`, `taxonomy:pillars`, `taxonomy:formats`                                                                                        | Read-only tags, pillars, and formats for planning.                                                                                                                                   |
| `list_hashtag_groups`          | `ezibreezy hashtags:list --workspace <workspaceId>`                                                                                                                                | Read-only saved hashtag groups.                                                                                                                                                      |
| `schedule_content`             | `ezibreezy content:schedule --workspace <workspaceId> --id <contentId> --scheduled-at <isoWithTimezone>`                                                                           | Requires explicit ISO 8601 timezone such as `2026-05-01T10:00:00+10:00`.                                                                                                             |
| `update_content_notes`         | `ezibreezy content:notes --workspace <workspaceId> --id <contentId> --notes <html>`                                                                                                | Replaces the rich-text notes attached to a content item. Notes are internal — never published. Pass an empty string to clear.                                                        |
| `upload_media`                 | `ezibreezy media:upload <file> --workspace <workspaceId>`                                                                                                                          | MCP creates an upload session; client uploads bytes, then calls `complete_media_upload`.                                                                                             |
| `complete_media_upload`        | `ezibreezy media:upload <file> --workspace <workspaceId>`                                                                                                                          | Finalizes an MCP upload session.                                                                                                                                                     |
| `list_media`                   | `ezibreezy media:list --workspace <workspaceId>`                                                                                                                                   | Read-only media discovery with safe metadata and filters.                                                                                                                            |
| `list_media_folders`           | `ezibreezy media:folders:list --workspace <workspaceId>`                                                                                                                           | Read-only media folder discovery.                                                                                                                                                    |
| `list_media_tags`              | `ezibreezy media:tags:list --workspace <workspaceId>`                                                                                                                              | Read-only media tag discovery.                                                                                                                                                       |
| `get_media`                    | `ezibreezy media:get --workspace <workspaceId> --id <mediaId>`                                                                                                                     | Read-only safe media metadata.                                                                                                                                                       |
| `get_media_view_url`           | `ezibreezy media:view-url --workspace <workspaceId> --id <mediaId>`                                                                                                                | Treat returned URLs as sensitive and short-lived.                                                                                                                                    |
| `list_grid_items`              | `ezibreezy grid:list --workspace <workspaceId> --integration <integrationId>`                                                                                                      | Read-only grid planner items. Does not promote, reorder, or delete.                                                                                                                  |
| `get_analytics_summary`        | `ezibreezy analytics:summary --workspace <workspaceId> --days 30`                                                                                                                  | Read-only sanitized metrics. Does not generate PDF reports.                                                                                                                          |
| `get_analytics_health`         | `ezibreezy analytics:health --workspace <workspaceId> --integration <integrationId>`                                                                                               | Read-only sanitized integration analytics health.                                                                                                                                    |
| `get_analytics_posts`          | `ezibreezy analytics:posts --workspace <workspaceId> --integration <integrationId>`, `analytics:posts-aggregate`                                                                   | Read-only sanitized post analytics for one integration or an aggregate set.                                                                                                          |
| `get_post_analytics`           | `ezibreezy analytics:post --workspace <workspaceId> --id <contentId>`                                                                                                              | Read-only sanitized analytics for a single published content item.                                                                                                                   |
| `get_analytics_demographics`   | `ezibreezy analytics:demographics --workspace <workspaceId> --integration <integrationId>`                                                                                         | Read-only sanitized audience demographics.                                                                                                                                           |
| `list_inbox_threads`           | `ezibreezy inbox:threads --workspace <workspaceId>`                                                                                                                                | Read-only thread summaries. Does not mark read, reply, moderate, retry, or delete.                                                                                                   |
| `get_inbox_thread_messages`    | `ezibreezy inbox:messages --workspace <workspaceId> --thread <threadId>`                                                                                                           | Read-only sanitized thread detail and messages. Does not mark read.                                                                                                                  |
| `get_inbox_stats`              | `ezibreezy inbox:stats --workspace <workspaceId>`                                                                                                                                  | Read-only inbox counts.                                                                                                                                                              |
| `publish_now`                  | `ezibreezy content:publish --workspace <workspaceId> --id <contentId> --yes`                                                                                                       | High-risk external action. Requires `confirmationText: "publish_now:<contentId>"` or server-enabled dangerous auto-confirm.                                                          |
| `reply_to_inbox_thread`        | `ezibreezy inbox:reply --workspace <workspaceId> --thread <threadId> --json reply.json --yes`                                                                                      | High-risk external action. Requires `confirmationText: "inbox_reply:<threadId>"` or server-enabled dangerous auto-confirm.                                                           |

## High-Risk Confirmation

High-risk MCP tools require exact confirmation text unless server-side dangerous auto-confirm is enabled for that action.

- `publish_now`: pass `confirmationText` as `publish_now:<contentId>`.
- `reply_to_inbox_thread`: pass `confirmationText` as `inbox_reply:<threadId>`.
- `dangerouslyAutoConfirm: true` is only honored when server policy allows the action, such as `EZIBREEZY_MCP_DANGEROUS_AUTO_CONFIRM_ACTIONS=publish_now,inbox_reply`.
- Tool input alone must not be treated as user permission. If confirmation is missing, ask the user to confirm the exact action and target.

## Not In MCP V1

Use CLI or API fallback, with the safety policy, for:

- Analytics report generation.
- Approval/client-review sends, resends, requests, withdrawals, decisions, comments, participant updates, overrides, reopens, cancels, and deletes.
- Inbox notes, read-state mutations, moderation, retries, and deletes.
- Retry publishing, delete/archive/restore content, boost/ad-spend tools, or broad media mutations.

## Example Sequence

Create a draft through MCP when connected:

1. `list_workspaces`
2. `list_integrations` with `workspaceId`
3. `get_integration_capabilities` with `workspaceId` and `integrationId`
4. `get_integration_options` if capabilities returns a required or useful `optionKey`
5. `list_media`, `list_media_folders`, or `list_media_tags` if existing assets need to be selected
6. `create_draft_content` with `workspaceId`, `integrationId`, `body`, and optional supported media/settings

Schedule an existing draft through MCP:

1. `get_content`
2. `get_integration_capabilities`
3. `schedule_content` with `scheduledAt` including explicit timezone

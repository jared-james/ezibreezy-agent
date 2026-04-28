# MCP Tools

Use this reference only when the current agent environment already has the hosted EziBreezy MCP server connected as `ezibreezy`.

Hosted endpoint:

```text
https://api.ezibreezy.com/mcp
```

Authentication:

```text
Authorization: Bearer $EZIBREEZY_API_KEY
```

Do not paste API keys, bearer tokens, presigned URLs, or provider payloads into chat or project files.

## Operating Order

1. Prefer connected MCP tools for agent-native reads and supported low-risk mutations.
2. Fall back to `@ezibreezy/cli` when MCP is not connected or does not expose the needed workflow.
3. Use the public API directly only when both MCP and CLI are insufficient.
4. Keep all normal safety rules: draft-first, explicit timezone for scheduling, capability inspection before platform-specific settings, and confirmation before externally visible, destructive, or broad actions.

## Tool Map

| MCP tool | CLI fallback | Notes |
| --- | --- | --- |
| `list_workspaces` | `ezibreezy workspaces:list` | Read-only workspace discovery. |
| `list_integrations` | `ezibreezy integrations:list --workspace <workspaceId>` | Read-only connected account list. |
| `get_integration_capabilities` | `ezibreezy integrations:capabilities --workspace <workspaceId> --integration <integrationId>` | Required before platform-specific content settings. |
| `create_draft_content` | `ezibreezy content:create --workspace <workspaceId> --json create-content.json` | MCP forces draft behavior. CLI payload should set `saveAsDraft: true`. |
| `list_content` | `ezibreezy content:list --workspace <workspaceId>` | Read-only public content statuses. |
| `get_content` | `ezibreezy content:get --workspace <workspaceId> --id <contentId>` | Read-only sanitized content detail. |
| `schedule_content` | `ezibreezy content:schedule --workspace <workspaceId> --id <contentId> --scheduled-at <isoWithTimezone>` | Requires explicit ISO 8601 timezone such as `2026-05-01T10:00:00+10:00`. |
| `upload_media` | `ezibreezy media:upload <file> --workspace <workspaceId>` | MCP creates an upload session; client uploads bytes, then calls `complete_media_upload`. |
| `complete_media_upload` | `ezibreezy media:upload <file> --workspace <workspaceId>` | Finalizes an MCP upload session. |
| `get_media` | `ezibreezy media:get --workspace <workspaceId> --id <mediaId>` | Read-only safe media metadata. |
| `get_media_view_url` | `ezibreezy media:view-url --workspace <workspaceId> --id <mediaId>` | Treat returned URLs as sensitive and short-lived. |
| `get_analytics_summary` | `ezibreezy analytics:summary --workspace <workspaceId> --days 30` | Read-only sanitized metrics. Does not generate PDF reports. |
| `list_inbox_threads` | `ezibreezy inbox:threads --workspace <workspaceId>` | Read-only thread summaries. Does not mark read, reply, moderate, retry, or delete. |

## Not In MCP V1

Use CLI or API fallback, with the safety policy, for:

- Dynamic integration option lookup such as `integrations:options`.
- Taxonomy, hashtags, grid planner, approvals, client reviews, and analytics report generation.
- Inbox messages, notes, replies, read-state mutations, moderation, retries, and deletes.
- Publish-now, retry publishing, delete/archive/restore content, boost/ad-spend tools, or broad media mutations.

## Example Sequence

Create a draft through MCP when connected:

1. `list_workspaces`
2. `list_integrations` with `workspaceId`
3. `get_integration_capabilities` with `workspaceId` and `integrationId`
4. `create_draft_content` with `workspaceId`, `integrationId`, `body`, and optional supported media/settings

Schedule an existing draft through MCP:

1. `get_content`
2. `get_integration_capabilities`
3. `schedule_content` with `scheduledAt` including explicit timezone

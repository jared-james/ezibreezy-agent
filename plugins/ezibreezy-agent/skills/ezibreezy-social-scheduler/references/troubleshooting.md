# Troubleshooting

Use this when CLI commands fail or return validation/provider errors.

## Contents

- Auth Failures
- Missing Workspace
- Missing Or Unusable Integration
- JSON Validation Errors
- Timezone Errors
- Platform Requirement Errors
- Agency Plan Or Role Errors
- Rate Limits And Provider Errors
- Output And Debugging

## Auth Failures

Run:

```bash
ezibreezy auth:status
ezibreezy auth:whoami
```

Credential precedence is:

1. `--api-key`
2. `EZIBREEZY_API_KEY`
3. Stored CLI login token

For automation, prefer env vars. For local sessions, use:

```bash
ezibreezy auth:login
```

Browser login tokens expire after 7 days and are stored per API URL.

For hosted MCP, first use the MCP client's **Authenticate** action. It should open EziBreezy in the browser, ask the user to approve access, and return to the MCP client.

If browser auth is not supported by the MCP client, use the API-key fallback. Set `EZIBREEZY_API_KEY` in the shell or client secret storage. Do not put the key in `.mcp.json`, `config.toml`, command history, screenshots, or chat.

CLI login is separate. `ezibreezy auth:login` signs in the CLI only; it does not create an MCP token.

## Missing Workspace

Run:

```bash
ezibreezy workspaces:list
```

If a workspace is missing, the current credential likely does not belong to the expected organization or lacks access.

## Missing Or Unusable Integration

Run:

```bash
ezibreezy integrations:list --workspace <workspaceId>
ezibreezy integrations:capabilities --workspace <workspaceId> --integration <integrationId>
```

Status meanings:

- `connected`: safe to use.
- `requires_reauth`: ask the user to reconnect the social account.
- `disabled`: ask the user to enable or choose another integration.
- `auth_incomplete`: ask the user to finish provider authorization.

If capabilities says an option key is unsupported, refetch capabilities for the exact integration ID; option keys are platform-specific.

## JSON Validation Errors

Common fixes:

- Ensure payload files are valid JSON, not JavaScript.
- Ensure IDs are UUID strings; grid planner IDs must be UUIDv4.
- Include at least one field for update payloads.
- For create content, include `integrationId` and non-empty `body`.
- Keep content body fields at or below 25000 characters.
- Keep content `mediaIds` to 10 or fewer, then apply stricter platform `maxMedia` from capabilities.
- For approval/client-review comments, include `body` or `attachmentId`.
- For inbox replies, include `content` or `attachment`.
- For grid create, include `mediaIds` for `visual_only` or `linkedContentId` for `linked_post`.
- For analytics reports, include `integrationId` or unique `integrationIds`.

## Timezone Errors

`scheduledAt` must be ISO 8601 with an explicit timezone:

```json
{ "scheduledAt": "2026-05-01T10:00:00+10:00" }
```

Valid examples include offsets like `+10:00` and UTC `Z`. Do not use vague dates such as "tomorrow morning" in payloads.

## Platform Requirement Errors

Run capabilities before retrying:

```bash
ezibreezy integrations:capabilities --workspace <workspaceId> --integration <integrationId>
```

Then check:

- `requiresMedia`: upload/select media first.
- `requiresTitle`: include a `title`.
- `maxMedia`: reduce attached media.
- `postTypes`: choose a supported `postType`.
- `mediaTypes`: choose supported uploaded media.
- `settings.fields`: move platform-specific values into `settings` using supported keys.
- `optionKey`: fetch dynamic options before choosing values.

Known strict cases:

- Pinterest requires media, title, and `settings.boardId`.
- YouTube requires one video and title.
- TikTok requires media, title, and account-specific privacy options.
- Instagram requires media.

## Agency Plan Or Role Errors

Approval history, client reviews, approval decisions/comments, pending approval counts, and analytics PDF reports can require an agency workspace plan and specific roles.

If the command says agency-plan only or role denied, report the plan/role blocker and do not retry with different payloads unless the user changes workspace/account.

## Rate Limits And Provider Errors

If a command fails with rate limit or provider errors:

- Do not immediately loop retries.
- Preserve the command, workspace, integration, content/media/message ID, provider/platform, and error code/message in the response.
- For provider auth errors, recheck integration status.
- For transient provider errors, ask whether to retry later or use `content:retry`/`inbox:retry-message` only after confirmation.

## Output And Debugging

Successful commands write JSON to stdout. Errors write structured JSON to stderr and exit non-zero.

For list commands, use filters to reduce noise:

```bash
ezibreezy content:list --workspace <workspaceId> --status scheduled
ezibreezy inbox:threads --workspace <workspaceId> --read unread
ezibreezy media:list --workspace <workspaceId> --type image --favorite
```

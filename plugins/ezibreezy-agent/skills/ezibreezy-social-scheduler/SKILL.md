---
name: ezibreezy-social-scheduler
description: Operate EziBreezy social scheduling, publishing, content management, media library, approvals, analytics, inbox, grid planner, hashtags, taxonomy, and EziBreezy CLI/API automation. Use when an agent needs to discover EziBreezy workspaces or integrations, create drafts, schedule posts, publish content, manage media, inspect analytics, handle approvals or client reviews, process inbox work, or automate social operations safely through EziBreezy.
---

# EziBreezy Social Scheduler

Use this skill to operate EziBreezy safely through the repo-supported automation surface.

## Operating Order

1. Run `ezibreezy auth:status` before EziBreezy work.
2. Prefer the EziBreezy CLI for reads and mutations.
3. Use direct public API calls only when the CLI cannot perform the task.
4. Use MCP tools only when they are already connected in the current agent environment.
5. Prefer environment credentials such as `EZIBREEZY_API_KEY`; never ask users to paste API keys, login tokens, presigned URLs, or raw provider payloads into chat.

## Discovery Workflow

Before creating, scheduling, publishing, or updating content:

1. Identify the workspace with `ezibreezy workspaces:list` or the user-provided workspace ID.
2. Identify the integration with `ezibreezy integrations:list --workspace <id>`.
3. Inspect platform requirements with `ezibreezy integrations:capabilities --workspace <id> --integration <id>`.
4. If a capability field includes a dynamic option key, fetch options with `ezibreezy integrations:options`.
5. Inspect existing media, taxonomy, hashtags, content, analytics, inbox, or grid planner state as needed for the requested workflow.

## Mutation Defaults

- Create drafts by default. For content creation, set `saveAsDraft: true` unless the user explicitly asked to schedule or publish.
- Do not schedule unless the user provides an explicit date, time, and timezone.
- Do not publish immediately unless the user explicitly requests publish-now behavior in the current task.
- Upload or select media through EziBreezy before attaching it to content.
- Inspect integration capabilities before platform-specific settings, and inspect dynamic options when a capability field includes `optionKey`.
- Return created or changed IDs, workspace, integration, status, scheduled time, and the next review step after every mutation.

## Safety Rules

Require explicit confirmation immediately before any action that is externally visible, destructive, or broad:

- Publish, retry publishing, archive, restore, delete, or schedule content when intent is ambiguous.
- Send, resend, withdraw, resubmit, or request approvals or client reviews.
- Send inbox replies, moderate inbox messages, retry failed inbox messages, delete failed messages, or mark threads read in bulk.
- Delete content, media, failed inbox messages, grid planner items, folders, tags, or hashtag groups.
- Bulk-delete media, bulk-move media, bulk-tag or bulk-untag media.
- Promote grid items to content, reorder large grids, or remove covers when the result is unclear.
- Generate or export analytics reports that may create files, notify users, or consume plan-limited resources.

If confirmation is missing, summarize the exact action, target IDs, workspace, integration, and expected external effect, then ask for confirmation before proceeding.

## References

Load only the reference needed for the current task:

- `references/command-reference.md` for CLI command groups and recommended sequences.
- `references/content-payloads.md` for JSON payload shapes, timezone handling, UUID fields, and examples.
- `references/integration-capabilities.md` for platform requirements, capability fields, media limits, and dynamic options.
- `references/safety-policy.md` for confirmation boundaries and credential handling.
- `references/troubleshooting.md` for auth, validation, integration, timezone, plan, rate-limit, and provider errors.

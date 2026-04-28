# Command Reference

Curated from `packages/cli/README.md` and `packages/cli/src/commands/*`.

## Contents

- Discovery
- Safe Reads
- Draft And Low-Risk Mutations
- Scheduling And Publishing
- Destructive Or Broad Actions
- Approvals And Client Reviews
- Analytics Reports
- Core Workflow Playbooks
- Common Sequences

## Discovery

Start here before any mutation:

```bash
ezibreezy auth:status
ezibreezy auth:login
ezibreezy auth:whoami
ezibreezy auth:logout
ezibreezy workspaces:list
ezibreezy integrations:list --workspace <workspaceId>
ezibreezy integrations:capabilities --workspace <workspaceId> --integration <integrationId>
ezibreezy integrations:options --workspace <workspaceId> --integration <integrationId> --key <optionKey>
```

Use MCP `get_integration_options` when connected. Use CLI `integrations:options --json <file>` as the fallback when the option key needs input, such as YouTube region, location search, or Instagram catalog/product search.

## Safe Reads

These commands read state and are usually safe to run after auth/workspace discovery:

```bash
ezibreezy content:list --workspace <workspaceId>
ezibreezy content:get --workspace <workspaceId> --id <contentId>
ezibreezy content:failed-count --workspace <workspaceId>
ezibreezy media:list --workspace <workspaceId>
ezibreezy media:get --workspace <workspaceId> --id <mediaId>
ezibreezy media:download-url --workspace <workspaceId> --id <mediaId>
ezibreezy media:view-url --workspace <workspaceId> --id <mediaId>
ezibreezy media:folders:list --workspace <workspaceId> --parent-id root
ezibreezy media:folders:get --workspace <workspaceId> --id <folderId>
ezibreezy media:folders:path --workspace <workspaceId> --id <folderId>
ezibreezy media:tags:list --workspace <workspaceId>
ezibreezy taxonomy:tags --workspace <workspaceId>
ezibreezy taxonomy:pillars --workspace <workspaceId>
ezibreezy taxonomy:formats --workspace <workspaceId>
ezibreezy hashtags:list --workspace <workspaceId>
ezibreezy analytics:summary --workspace <workspaceId> --days 30
ezibreezy analytics:health --workspace <workspaceId> --integration <integrationId>
ezibreezy analytics:posts --workspace <workspaceId> --integration <integrationId>
ezibreezy analytics:posts-aggregate --workspace <workspaceId>
ezibreezy analytics:post --workspace <workspaceId> --id <contentId>
ezibreezy analytics:demographics --workspace <workspaceId> --integration <integrationId>
ezibreezy inbox:threads --workspace <workspaceId>
ezibreezy inbox:messages --workspace <workspaceId> --thread <threadId>
ezibreezy inbox:stats --workspace <workspaceId>
ezibreezy grid:list --workspace <workspaceId> --integration <integrationId>
```

Agency-plan reads:

```bash
ezibreezy content:pending-approval-count --workspace <workspaceId>
ezibreezy content:approval-history --workspace <workspaceId>
ezibreezy content:approval-history:client-batch --workspace <workspaceId> --batch <batchId>
ezibreezy content:approval-history:internal --workspace <workspaceId> --record <recordId>
ezibreezy content:history --workspace <workspaceId> --id <contentId>
ezibreezy client-reviews:open --workspace <workspaceId>
ezibreezy client-reviews:get --workspace <workspaceId> --batch <batchId>
```

## Draft And Low-Risk Mutations

Prefer draft creation unless the user explicitly asks to schedule or publish:

```bash
ezibreezy content:create --workspace <workspaceId> --json create-content.json
ezibreezy content:update --workspace <workspaceId> --id <contentId> --json update-content.json
ezibreezy content:notes --workspace <workspaceId> --id <contentId> --notes "<p>Planning notes</p>"
ezibreezy content:workflow --workspace <workspaceId> --id <contentId> --status drafting
ezibreezy media:upload ./image.png --workspace <workspaceId>
ezibreezy media:update --workspace <workspaceId> --id <mediaId> --json media-update.json
ezibreezy media:folders:create --workspace <workspaceId> --json media-folder.json
ezibreezy media:folders:update --workspace <workspaceId> --id <folderId> --json media-folder.json
ezibreezy media:folders:move --workspace <workspaceId> --id <folderId> --json media-folder-move.json
ezibreezy media:tags:create --workspace <workspaceId> --json media-tag.json
ezibreezy media:tags:update --workspace <workspaceId> --id <tagId> --json media-tag.json
ezibreezy media:tags:attach --workspace <workspaceId> --id <mediaId> --json media-tag-ids.json
ezibreezy media:tags:detach --workspace <workspaceId> --id <mediaId> --json media-tag-ids.json
ezibreezy hashtags:create --workspace <workspaceId> --json hashtags.json
ezibreezy hashtags:update --workspace <workspaceId> --id <hashtagGroupId> --json hashtags.json
ezibreezy inbox:note --workspace <workspaceId> --thread <threadId> --json inbox-note.json
ezibreezy grid:create --workspace <workspaceId> --json grid-item.json
ezibreezy grid:update --workspace <workspaceId> --item <itemId> --json grid-update.json
ezibreezy grid:replace-media --workspace <workspaceId> --item <itemId> --json grid-media.json
ezibreezy grid:set-cover --workspace <workspaceId> --item <itemId> --json grid-cover.json
```

## Scheduling And Publishing

These need explicit user intent. Scheduling also needs date, time, and timezone:

```bash
ezibreezy content:schedule --workspace <workspaceId> --id <contentId> --scheduled-at "2026-05-01T10:00:00+10:00"
ezibreezy content:publish --workspace <workspaceId> --id <contentId>
ezibreezy content:retry --workspace <workspaceId> --id <contentId>
```

`content:publish` and `content:retry` do not require `--yes` in the CLI, but agents should still confirm because they can publish externally.

## Destructive Or Broad Actions

Confirm before running any of these:

```bash
ezibreezy content:archive --workspace <workspaceId> --id <contentId>
ezibreezy content:restore --workspace <workspaceId> --id <contentId>
ezibreezy content:delete --workspace <workspaceId> --id <contentId>
ezibreezy media:archive --workspace <workspaceId> --id <mediaId>
ezibreezy media:delete --workspace <workspaceId> --id <mediaId>
ezibreezy media:folders:delete --workspace <workspaceId> --id <folderId>
ezibreezy media:tags:delete --workspace <workspaceId> --id <tagId>
ezibreezy media:bulk-archive --workspace <workspaceId> --json media-ids.json
ezibreezy media:bulk-move --workspace <workspaceId> --json media-bulk-move.json
ezibreezy media:bulk-tag --workspace <workspaceId> --json media-bulk-tags.json
ezibreezy media:bulk-untag --workspace <workspaceId> --json media-bulk-tags.json
ezibreezy hashtags:delete --workspace <workspaceId> --id <hashtagGroupId>
ezibreezy inbox:read --workspace <workspaceId> --thread <threadId>
ezibreezy inbox:unread --workspace <workspaceId> --thread <threadId>
ezibreezy grid:reorder --workspace <workspaceId> --json grid-reorder.json
ezibreezy grid:remove-cover --workspace <workspaceId> --item <itemId>
```

CLI-enforced `--yes`:

```bash
ezibreezy media:bulk-delete --workspace <workspaceId> --json media-ids.json --yes
ezibreezy inbox:read-all --workspace <workspaceId> --json inbox-read-all.json --yes
ezibreezy inbox:reply --workspace <workspaceId> --thread <threadId> --json inbox-reply.json --yes
ezibreezy inbox:moderate --workspace <workspaceId> --message <messageId> --json inbox-moderate.json --yes
ezibreezy inbox:retry-message --workspace <workspaceId> --message <messageId> --yes
ezibreezy inbox:delete-failed --workspace <workspaceId> --message <messageId> --yes
ezibreezy grid:delete --workspace <workspaceId> --item <itemId> --yes
ezibreezy grid:promote --workspace <workspaceId> --item <itemId> --json grid-promote.json --yes
```

## Approvals And Client Reviews

Agency-plan commands:

```bash
ezibreezy content:comment --workspace <workspaceId> --id <contentId> --json comment.json
ezibreezy content:comment-attachments:init --workspace <workspaceId> --json attachment.json
ezibreezy content:comment-attachments:complete --workspace <workspaceId> --attachment <attachmentId>
ezibreezy content:comment-attachments:abort --workspace <workspaceId> --attachment <attachmentId>
ezibreezy content:activity:react --workspace <workspaceId> --activity <activityId> --json reaction.json
ezibreezy content:activity:unreact --workspace <workspaceId> --activity <activityId> --emoji thumbs-up
ezibreezy content:decision --workspace <workspaceId> --id <contentId> --json decision.json
ezibreezy content:withdraw --workspace <workspaceId> --id <contentId>
ezibreezy content:reopen-client-review --workspace <workspaceId> --id <contentId>
ezibreezy client-reviews:create --workspace <workspaceId> --json client-review.json
ezibreezy client-reviews:add-post --workspace <workspaceId> --batch <batchId> --json post-id.json
ezibreezy client-reviews:remove-post --workspace <workspaceId> --batch <batchId> --json post-id.json
ezibreezy client-reviews:cancel --workspace <workspaceId> --batch <batchId>
ezibreezy client-reviews:override --workspace <workspaceId> --batch <batchId> --json override.json
ezibreezy client-reviews:update-participant --workspace <workspaceId> --batch <batchId> --json participant.json
ezibreezy client-reviews:comment --workspace <workspaceId> --batch <batchId> --json comment.json
```

CLI-enforced `--yes` because people may be notified:

```bash
ezibreezy content:request-approval --workspace <workspaceId> --id <contentId> --json approval.json --yes
ezibreezy content:resubmit --workspace <workspaceId> --id <contentId> --json approval.json --yes
ezibreezy client-reviews:send --workspace <workspaceId> --batch <batchId> --yes
ezibreezy client-reviews:resend --workspace <workspaceId> --batch <batchId> --yes
```

## Analytics Reports

Analytics reads are safe. PDF generation is agency-plan only and requires `--yes`:

```bash
ezibreezy analytics:report --workspace <workspaceId> --provider instagram --json analytics-report.json --output report.pdf --yes
```

## Core Workflow Playbooks

Use these playbooks when a user asks for a full workflow rather than one command.

### Workspace Discovery

Goal: identify the authenticated user and usable workspace.

1. Run `ezibreezy auth:status`.
2. If signed out in a local human session, run `ezibreezy auth:login`; use `EZIBREEZY_API_KEY` for automation.
3. If signed in, run `ezibreezy auth:whoami`.
4. Run `ezibreezy workspaces:list`.
5. If the user did not name a workspace, choose only when there is a single clear workspace; otherwise ask which workspace to use.
6. Return workspace ID, workspace name if present, auth mode if visible, and any missing-access blocker.

### Integration Discovery

Goal: identify a connected social account and its platform requirements.

1. Run `ezibreezy integrations:list --workspace <workspaceId>`.
2. Filter out integrations with status other than `connected` unless the task is troubleshooting.
3. Run `ezibreezy integrations:capabilities --workspace <workspaceId> --integration <integrationId>` for the target account.
4. If any selected settings field has `optionKey`, call MCP `get_integration_options`, or run `ezibreezy integrations:options` as the CLI fallback.
5. Return integration ID, platform, username/name, status, post types, media requirements, title requirement, max media, settings fields, and option values used.

### Media Workflow

Goal: select, upload, inspect, and organize media before content mutation.

1. Discover workspace and integration if media is for a post.
2. List existing media when the user asks to reuse assets:
   `ezibreezy media:list --workspace <workspaceId>`.
3. Upload local files before attaching them:
   `ezibreezy media:upload ./asset.png --workspace <workspaceId>`.
4. Inspect selected/uploaded media:
   `ezibreezy media:get --workspace <workspaceId> --id <mediaId>`.
5. Use temporary URLs only when needed for preview/download:
   `media:view-url` or `media:download-url`.
6. Update metadata with `media:update --json`.
7. Organize with folder and tag commands when requested.
8. Confirm before deletes, bulk moves/tags, or archive operations.
9. Return media IDs, filenames, media type, folder/tag changes, and whether the media satisfies integration capabilities.

### Content Workflow

Goal: create or edit content safely, defaulting to draft.

1. Discover workspace and integration.
2. Run capabilities and dynamic options before platform-specific settings.
3. Inspect relevant existing content with `content:list` or `content:get` when updating.
4. Upload/select media first if needed.
5. Create new content with `content:create --json` and `saveAsDraft: true` unless schedule/publish is explicit.
6. Update content with `content:update --id <contentId> --json <file>`; include only fields that should change.
7. Schedule only when date, time, and timezone are explicit:
   `content:schedule --scheduled-at "2026-05-01T10:00:00+10:00"`.
8. Set workflow state only to `idea` or `drafting` with `content:workflow`.
9. Return content ID, status, workflow status if known, workspace, integration, scheduled time, media IDs, and next review step.

### Publishing Workflow

Goal: handle publish-now, retry, archive, restore, and delete without accidental external effects.

1. Read the content with `content:get`.
2. Confirm workspace, integration, content ID, current status, and desired action.
3. For publish-now, confirm explicit current-task intent, then run `content:publish`.
4. For retry, confirm external retry intent, then run `content:retry`.
5. For archive/restore/delete, confirm the exact content ID and effect, then run the matching command.
6. Return resulting status or deletion marker, content ID, workspace, integration, and any provider error.

### Approval Workflow

Goal: manage internal approval and client review work in agency workspaces.

1. Confirm the workspace has agency-plan access if commands fail with plan errors.
2. Inspect pending work with `content:pending-approval-count`, `content:approval-history`, `content:history`, or client-review reads.
3. Request or resubmit internal approval with `content:request-approval --yes` or `content:resubmit --yes` only after confirmation because people may be notified.
4. Submit decisions with `content:decision --json`; include `reason` for rejection or override.
5. Add comments or attachments with `content:comment`, `content:comment-attachments:init`, `complete`, or `abort`.
6. Create client review batches with `client-reviews:create`.
7. Add/remove posts, update participants, comment, override, or cancel as requested.
8. Send/resend client reviews with `--yes` only after confirmation.
9. Return content IDs, approval record IDs, batch IDs, reviewer impact, decision state, and next reviewer/client step.

### Taxonomy And Hashtags Workflow

Goal: reuse or maintain labels that shape content planning.

1. Read taxonomy before tagging content:
   `taxonomy:tags`, `taxonomy:pillars`, and `taxonomy:formats`.
2. Read hashtag groups with `hashtags:list`.
3. Create or update hashtag groups with JSON only when the user asks to manage reusable groups.
4. Confirm before deleting a hashtag group.
5. Return selected tag IDs, pillar ID, format ID, hashtag group ID, and how they map to the content payload.

### Analytics Workflow

Goal: read performance data and generate reports safely.

1. Use `analytics:summary --days <1-90|all>` for workspace or integration overview.
2. Use `analytics:health --integration <id>` for account health.
3. Use `analytics:posts --integration <id>` for a single account's post list.
4. Use `analytics:posts-aggregate --integration-ids <ids>` for cross-account comparisons.
5. Use `analytics:post --id <contentId>` for one published content item.
6. Use `analytics:demographics --integration <id>` for audience data.
7. Generate PDF reports with `analytics:report --yes` only after confirmation; report generation is agency-plan only and can be expensive.
8. Return date range, integrations included, key metric fields requested, report path if generated, and any plan/provider blocker.

### Inbox Workflow

Goal: read inbox work first, then apply replies, notes, read-state, moderation, or retries safely.

1. Read threads with `inbox:threads --workspace <workspaceId>` using filters such as `--read unread`.
2. Read messages for a selected thread with `inbox:messages --thread <threadId>`.
3. Read stats with `inbox:stats` when triaging volume.
4. Mark individual threads read/unread only after the target thread is clear.
5. Add internal notes with `inbox:note --json`.
6. Send public replies with `inbox:reply --yes` only after confirmation.
7. Moderate public content with `inbox:moderate --yes` only after confirmation.
8. Retry or delete failed outgoing messages with `--yes` only after confirmation.
9. Use `inbox:read-all --yes` only after confirming the filter and expected count/scope.
10. Return thread IDs, message IDs, public/private effect, moderation action, and retry/delete state.

### Grid Planner Workflow

Goal: manage visual planning items and promote them to content drafts.

1. List the grid for one integration:
   `ezibreezy grid:list --workspace <workspaceId> --integration <integrationId>`.
2. Create visual-only items with uploaded media IDs and `kind: "visual_only"`.
3. Create linked-post items with `kind: "linked_post"` and `linkedContentId`.
4. Update notes or aspect ratio with `grid:update`.
5. Replace visual media with `grid:replace-media`.
6. Set or remove covers with `grid:set-cover` or `grid:remove-cover`; confirm remove-cover if the result is unclear.
7. Reorder with `grid:reorder` only when the payload contains the full item ID order for the integration.
8. Promote to a content draft with `grid:promote --yes` only after confirmation.
9. Delete with `grid:delete --yes` only after confirmation.
10. Return item IDs, integration ID, media IDs, linked content ID, order changes, promoted draft ID if present, and next planning step.

## Common Sequences

Create a draft:

```bash
ezibreezy auth:status
ezibreezy workspaces:list
ezibreezy integrations:list --workspace <workspaceId>
ezibreezy integrations:capabilities --workspace <workspaceId> --integration <integrationId>
ezibreezy content:create --workspace <workspaceId> --json create-content.json
```

Schedule an existing draft:

```bash
ezibreezy content:get --workspace <workspaceId> --id <contentId>
ezibreezy integrations:capabilities --workspace <workspaceId> --integration <integrationId>
ezibreezy content:schedule --workspace <workspaceId> --id <contentId> --scheduled-at "2026-05-01T10:00:00+10:00"
```

Upload media then create a draft:

```bash
ezibreezy media:upload ./asset.png --workspace <workspaceId>
ezibreezy media:get --workspace <workspaceId> --id <mediaId>
ezibreezy content:create --workspace <workspaceId> --json create-content.json
```

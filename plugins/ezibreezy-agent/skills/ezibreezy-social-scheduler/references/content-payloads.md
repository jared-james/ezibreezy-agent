# Content Payloads

Curated from CLI schemas and `packages/cli/README.md`.

## Contents

- General Rules
- Content Create
- Thread Content
- Schedule Existing Content
- Immediate Publish
- Media
- Approvals
- Client Reviews
- Inbox
- Grid Planner
- Analytics Report

## General Rules

- Use UUID strings for workspace, integration, content, media, approver, batch, folder, label, pillar, format, campaign, and campaign phase IDs.
- Grid planner IDs must be UUIDv4 strings.
- Use ISO 8601 timestamps with explicit timezone for scheduling, for example `2026-05-01T10:00:00+10:00` or `2026-05-01T00:00:00Z`.
- Upload local media through EziBreezy first, then attach it with `contentMedia[].mediaId`.
- Base content create/update targets one `integrationId` and one integration-scoped content row. Multi-platform composition should fan out to separate grouped rows.
- For drafts, omit `intent` or include `intent: "draft"`.
- `content:update` uses the create-content shape except `intent`, and at least one field must be present.

## Content Create

Minimal draft:

```json
{
  "integrationId": "00000000-0000-4000-8000-000000000001",
  "captions": {
    "canonical": "Launch post copy"
  },
  "intent": "draft"
}
```

Scheduled create payload, only when the user provided an explicit date/time/timezone:

```json
{
  "integrationId": "00000000-0000-4000-8000-000000000001",
  "captions": {
    "canonical": "Launch post copy"
  },
  "contentMedia": [
    {
      "mediaId": "00000000-0000-4000-8000-000000000002"
    }
  ],
  "scheduledAt": "2026-05-01T10:00:00+10:00",
  "postType": "post"
}
```

Platform-aware payload with taxonomy:

```json
{
  "integrationId": "00000000-0000-4000-8000-000000000001",
  "internalTitle": "Launch planning card",
  "publishTitle": "Launch title",
  "captions": {
    "canonical": "Launch post copy"
  },
  "contentMedia": [
    {
      "mediaId": "00000000-0000-4000-8000-000000000002",
      "order": 0,
      "cover": {
        "thumbOffsetMs": 2500
      }
    }
  ],
  "postType": "reel",
  "settings": {
    "privacyStatus": "private"
  },
  "pillarIds": ["00000000-0000-4000-8000-000000000003"],
  "formatIds": ["00000000-0000-4000-8000-000000000004"],
  "labelIds": ["00000000-0000-4000-8000-000000000005"],
  "campaignId": "00000000-0000-4000-8000-000000000006",
  "campaignPhaseId": "00000000-0000-4000-8000-000000000007",
  "priority": "medium",
  "intent": "draft"
}
```

Supported content fields:

- `integrationId`: required UUID.
- `internalTitle`: optional internal planning title, max 500 characters. Never published.
- `publishTitle`: optional provider-facing title, max 500 characters. Required before scheduling/publishing when capabilities say `requiresTitle`.
- `notes`: optional rich-text HTML, max 100000 characters. Internal - never published. Use to attach planning context, links, or meeting notes to a content item.
- `captions`: optional object with `canonical` publish text and optional platform overrides.
- `contentMedia`: optional uploaded media relationships, max 10 before stricter platform limits.
- `scheduledAt`: optional ISO 8601 with timezone.
- `settings`: optional object for platform fields from capabilities.
- `postType`: optional `post`, `reel`, `story`, or `thread`.
- `firstComment`, `shareToFeed`: optional publish behavior fields.
- `contentMedia[].crops`, `contentMedia[].userTags`, `contentMedia[].productTags`, `contentMedia[].cover.coverMediaId`, `contentMedia[].cover.thumbOffsetMs`: optional media metadata where platform capabilities allow it.
- `threadMessages`: optional array for thread-style content, max 20 items.
- `pillarIds`, `formatIds`, `labelIds`: optional UUID arrays.
- `campaignId`, `campaignPhaseId`: optional campaign UUIDs; use `null` to clear campaign links on update.
- `assigneeIds`: optional UUID array.
- `priority`: optional `urgent`, `high`, `medium`, or `low`.
- `intent`: optional `draft`, `schedule`, or `publish_now`. Omitted intent defaults to `draft`.

## Thread Content

Use `postType: "thread"` only when the integration capabilities support threads.

```json
{
  "integrationId": "00000000-0000-4000-8000-000000000001",
  "captions": {
    "canonical": "Thread starter"
  },
  "postType": "thread",
  "threadMessages": [
    {
      "captions": {
        "canonical": "Second post in the thread"
      }
    },
    {
      "captions": {
        "canonical": "Third post with media"
      },
      "contentMedia": [
        {
          "mediaId": "00000000-0000-4000-8000-000000000002"
        }
      ]
    }
  ],
  "intent": "draft"
}
```

`threadMessages` accepts up to 20 items; each item uses `captions.canonical` and can include `contentMedia`.

## Schedule Existing Content

CLI command:

```bash
ezibreezy content:schedule --workspace <workspaceId> --id <contentId> --scheduled-at "2026-05-01T10:00:00+10:00"
```

Equivalent payload shape:

```json
{
  "scheduledAt": "2026-05-01T10:00:00+10:00"
}
```

## Immediate Publish

Publishing uses an existing content ID, not a JSON payload:

```bash
ezibreezy content:publish --workspace <workspaceId> --id <contentId> --yes
```

Confirm first because this is externally visible. The CLI requires `--yes` for publish-now.

## Published Caption Edit

This edits the provider caption for an already-published Facebook item:

```bash
ezibreezy content:published-caption --workspace <workspaceId> --id <contentId> --json published-caption.json --yes
```

Payload shape:

```json
{
  "message": "Updated Facebook caption"
}
```

Confirm first because this changes externally visible provider content. The CLI requires `--yes`.

## Media

Upload files first:

```bash
ezibreezy media:upload ./image.png --workspace <workspaceId>
```

Supported upload extensions:

- Images: `.jpg`, `.jpeg`, `.png`, `.gif`, `.webp`, `.avif`, max 50 MB.
- Videos: `.mp4`, `.mov`, max 512 MB.

Update media:

```json
{
  "filename": "campaign-hero.png",
  "altText": "Campaign hero image",
  "isFavorite": true,
  "folderId": null
}
```

Folders and labels:

```json
{ "name": "Launch assets" }
```

```json
{ "name": "Approved", "color": "#22cc88" }
```

Move folder to root:

```json
{ "parentId": null }
```

Bulk media IDs:

```json
{ "mediaIds": ["00000000-0000-4000-8000-000000000001"] }
```

Bulk label payload:

```json
{
  "mediaIds": ["00000000-0000-4000-8000-000000000001"],
  "labelIds": ["00000000-0000-4000-8000-000000000002"]
}
```

Media constraints:

- Update payloads must include at least one field.
- Folder/label names are required when creating.
- Label colors must be hex colors.
- Bulk media IDs require 1 to 100 IDs.
- Label ID lists require 1 to 50 IDs.

## Approvals

Internal approval request:

```json
{
  "approverIds": ["00000000-0000-4000-8000-000000000001"],
  "policy": "any"
}
```

Approval decision:

```json
{
  "decision": "reject",
  "reason": "Please revise the CTA."
}
```

`decision` is `approve` or `reject`; `reason` is required when rejecting or using `override: true`.

Approval comment:

```json
{
  "body": "Looks good to me."
}
```

Comment attachment init:

```json
{
  "contentId": "00000000-0000-4000-8000-000000000001",
  "audience": "internal",
  "filename": "feedback.png",
  "contentType": "image/png",
  "fileSize": 123456
}
```

Allowed attachment content types: `image/jpeg`, `image/png`, `image/gif`, `image/webp`.

Reaction:

```json
{ "emoji": "thumbs-up" }
```

## Client Reviews

Client-only review batch:

```json
{
  "contentIds": ["00000000-0000-4000-8000-000000000001"],
  "workflowMode": "client",
  "client": {
    "reviewers": [{ "name": "Client Reviewer", "email": "reviewer@example.com" }],
    "policy": "all",
    "inviteNote": "Please review when you have a moment.",
    "batchLabel": "May launch"
  }
}
```

Internal then client review:

```json
{
  "contentIds": ["00000000-0000-4000-8000-000000000001"],
  "workflowMode": "internal_client",
  "internal": {
    "approverIds": ["00000000-0000-4000-8000-000000000002"],
    "policy": "any"
  },
  "client": {
    "reviewers": [{ "name": "Client Reviewer", "email": "reviewer@example.com" }],
    "policy": "all"
  }
}
```

Other client-review payloads:

```json
{ "contentId": "00000000-0000-4000-8000-000000000001" }
```

```json
{
  "contentId": "00000000-0000-4000-8000-000000000001",
  "reason": "Approved outside the portal."
}
```

```json
{
  "participantId": "00000000-0000-4000-8000-000000000001",
  "email": "reviewer@example.com",
  "name": "Updated Name"
}
```

## Inbox

Public reply:

```json
{
  "content": "Thanks for reaching out.",
  "parentMessageId": "00000000-0000-4000-8000-000000000001"
}
```

Attachment reply:

```json
{
  "attachment": {
    "type": "image",
    "url": "https://cdn.example.com/reply.png"
  }
}
```

Internal note:

```json
{ "content": "Follow up with the team before replying." }
```

Moderation:

```json
{ "action": "hide" }
```

Allowed moderation actions: `hide`, `unhide`, `delete`.

Read-all filter:

```json
{
  "platform": "instagram",
  "status": "open",
  "integrationId": "00000000-0000-4000-8000-000000000001",
  "messageType": "comment"
}
```

Use `{}` only when the user explicitly wants all matching threads marked read.

## Grid Planner

Visual-only item:

```json
{
  "integrationId": "00000000-0000-4000-8000-000000000001",
  "kind": "visual_only",
  "mediaIds": ["00000000-0000-4000-8000-000000000002"],
  "coverMediaId": "00000000-0000-4000-8000-000000000002",
  "note": "Plan this visual",
  "settings": { "aspectRatio": 1 }
}
```

Linked content item:

```json
{
  "integrationId": "00000000-0000-4000-8000-000000000001",
  "kind": "linked_post",
  "linkedContentId": "00000000-0000-4000-8000-000000000003"
}
```

Update item:

```json
{
  "integrationId": "00000000-0000-4000-8000-000000000001",
  "note": "Updated planning note",
  "settings": { "aspectRatio": 1 }
}
```

Reorder requires the complete item order for the integration:

```json
{
  "integrationId": "00000000-0000-4000-8000-000000000001",
  "itemIds": ["00000000-0000-4000-8000-000000000004"]
}
```

Replace media:

```json
{
  "integrationId": "00000000-0000-4000-8000-000000000001",
  "mediaIds": ["00000000-0000-4000-8000-000000000002"]
}
```

Set cover:

```json
{
  "integrationId": "00000000-0000-4000-8000-000000000001",
  "mediaId": "00000000-0000-4000-8000-000000000005"
}
```

Promote:

```json
{ "integrationId": "00000000-0000-4000-8000-000000000001" }
```

Grid constraints:

- `visual_only` requires `mediaIds`.
- `linked_post` requires `linkedContentId`.
- `mediaIds` and `itemIds` must be unique.
- `coverMediaId` can be a UUIDv4 string or null.
- `settings.aspectRatio` can be null or a number from 0.1 to 10.
- Replace-media accepts 1 to 10 media IDs.

## Analytics Report

PDF report payload:

```json
{
  "integrationIds": ["00000000-0000-4000-8000-000000000001"],
  "days": 30,
  "titlePage": {
    "enabled": true,
    "title": "Monthly report",
    "description": "Performance summary"
  },
  "sections": {
    "demographics": true,
    "topPosts": true
  }
}
```

Rules:

- Include `integrationId` or unique `integrationIds`.
- `integrationIds` can contain 1 to 20 UUIDs.
- `days` is `all` or an integer from 1 to 90.
- `titlePage.title` is max 200 characters.
- `titlePage.description` is max 1000 characters.

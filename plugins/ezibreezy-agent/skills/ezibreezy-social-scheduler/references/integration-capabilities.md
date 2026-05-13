# Integration Capabilities

Quick-glance reference for the live capability catalog in `src/integrations/integration-capability-catalog.ts`.

## Contents

- Commands
- Response Shape
- Common Settings Fields
- Platform Matrix
- Platform Settings And Options
- Agent Rules

## Commands

List integrations:

```bash
ezibreezy integrations:list --workspace <workspaceId>
```

Get safe publishing requirements:

```bash
ezibreezy integrations:capabilities --workspace <workspaceId> --integration <integrationId>
```

Fetch dynamic options returned by capabilities. Prefer MCP `get_integration_options` when connected; use the CLI fallback when MCP is unavailable:

```bash
ezibreezy integrations:options --workspace <workspaceId> --integration <integrationId> --key <optionKey>
```

Use `--json <file>` for option bodies:

```json
{ "regionCode": "AU" }
```

Supported option body fields:

- `regionCode`: optional string, max 2 characters.
- `query`: optional string, max 255 characters.
- `catalogId`: optional string, max 255 characters.

## Response Shape

Capabilities returns:

- `integration`: `id`, `platform`, `username`, `name`, and `status`.
- `content`: `postTypes`, `mediaTypes`, `maxMedia`, `supportsThreads`, `requiresMedia`, and `requiresTitle`.
- `rules`: live plain-text platform publishing rules from the backend. Treat this as the source of truth for platform-specific requirements.
- `settings.fields`: supported platform settings.
- `options`: dynamic option keys available for that integration.
- `capabilities`: curated provider capability data from the connection, when present.

Integration status is one of:

- `connected`
- `requires_reauth`
- `disabled`
- `auth_incomplete`

Do not mutate content through an integration unless its status is `connected`.

## Settings And Media Metadata

`settings.fields` contains provider publish settings only. Do not place media metadata or editor state in `settings`.

Use `contentMedia` for per-media metadata:

- `contentMedia[].crops`: optional crop data keyed by platform.
- `contentMedia[].userTags`: optional user tags where supported.
- `contentMedia[].productTags`: optional product tags where supported.
- `contentMedia[].cover.coverMediaId`: optional uploaded media ID for a cover/thumbnail where supported.
- `contentMedia[].cover.thumbOffsetMs`: optional thumbnail offset; provider execution maps this to platform API fields.

## Platform Matrix

| Platform        | Post types              | Media types             | Max media | Threads | Requires media | Requires title |
| --------------- | ----------------------- | ----------------------- | --------: | ------- | -------------- | -------------- |
| `x`             | `post`, `thread`        | `image`, `gif`, `video` |         4 | yes     | no             | no             |
| `linkedin`      | `post`                  | `image`, `video`        |        10 | no      | no             | no             |
| `instagram`     | `post`, `reel`, `story` | `image`, `video`        |        10 | no      | yes            | no             |
| `facebook`      | `post`, `reel`, `story` | `image`, `video`        |        10 | no      | no             | no             |
| `facebook-page` | `post`, `reel`, `story` | `image`, `video`        |        10 | no      | no             | no             |
| `threads`       | `post`, `thread`        | `image`, `video`        |        20 | yes     | no             | no             |
| `tiktok`        | `post`, `reel`          | `image`, `video`        |        35 | no      | yes            | yes            |
| `youtube`       | `post`, `reel`          | `video`                 |         1 | no      | yes            | yes            |
| `pinterest`     | `post`                  | `image`, `video`        |         1 | no      | yes            | yes            |

Unknown platforms fall back to common post types `post`, `reel`, `story`, and `thread`; media types `image`, `video`; max media 10; no required media/title; no thread support.

## Platform Settings And Options

### X

Settings:

- `xCommunityUrl`: optional string. Community ID is extracted from the URL.

Options: none.

### LinkedIn

Settings: none.

Options: none.

### Instagram

Settings:

- `locationId`: optional Instagram location ID.
- `collaborators`: optional string array of usernames or IDs.

Options:

- `locations`: requires `query`.
- `instagram-catalogs`: no input required.
- `instagram-products`: requires `catalogId`; optional `query`.

Fetch flow for products:

```bash
# MCP preferred: call get_integration_options with optionKey instagram-catalogs, then instagram-products.
ezibreezy integrations:options --workspace <workspaceId> --integration <integrationId> --key instagram-catalogs
ezibreezy integrations:options --workspace <workspaceId> --integration <integrationId> --key instagram-products --json product-options.json
```

### Facebook And Facebook Page

Settings:

- `facebookLinkUrl`: optional feed link URL. Required when `facebookCtaType` is a CTA button.
- `facebookCtaType`: optional CTA button type. Use `NO_BUTTON` or omit for no CTA.
- `facebookLocationId`: optional Facebook Page location ID.

Options: none.

### Threads

Settings:

- `locationId`: optional string, use option key `locations`.
- `topicTag`: optional string.
- `linkAttachment`: optional string URL.

Options:

- `locations`: requires `query`.

### TikTok

Settings:

- `privacy_level`: string, use option key `tiktok-creator-info`.
- `disable_comment`: boolean.
- `disable_duet`: boolean.
- `disable_stitch`: boolean.
- `brand_content_toggle`: boolean.
- `brand_organic_toggle`: boolean.
- `is_aigc`: boolean.
- `photo_cover_index`: number.
- `auto_add_music`: boolean.
- `tiktok_post_to_drafts`: boolean.

Options:

- `tiktok-creator-info`: no input required.

### YouTube

Settings:

- `privacyStatus`: `public`, `unlisted`, or `private`.
- `tags`: string array.
- `categoryId`: string, use option key `youtube-video-categories`.
- `madeForKids`: boolean.
- `notifySubscribers`: boolean.
- `embeddable`: boolean.
- `license`: `youtube` or `creativeCommon`.

Options:

- `youtube-video-categories`: optional `regionCode`, for example `US` or `AU`.

### Pinterest

Settings:

- `boardId`: required string, use option key `pinterest-boards`.
- `link`: optional outbound link string.
- `altText`: optional media alt text string.

Options:

- `pinterest-boards`: no input required.

## Agent Rules

- Call `integrations:capabilities` before platform-specific settings.
- Read and follow the live `rules` string returned by capabilities; do not rely on static prose for platform-specific publishing requirements.
- When a settings field has `optionKey`, call MCP `get_integration_options` before choosing a value, or use CLI `integrations:options` as the fallback.
- If `requiresMedia` is true, attach uploaded media before scheduling or publishing.
- If `requiresTitle` is true, include `publishTitle` before scheduling or publishing.
- Respect `maxMedia` when building `contentMedia`.
- Use the top-level `postType` only. Do not send platform-specific post type override settings.
- Do not expose tokens, scopes, stored provider settings, or raw provider payloads; capabilities and options are the safe discovery surface.

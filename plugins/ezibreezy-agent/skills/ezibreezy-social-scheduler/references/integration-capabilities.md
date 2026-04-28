# Integration Capabilities

Curated from `src/public-api/controllers/public-integrations.controller.ts`.

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

Fetch dynamic options returned by capabilities:

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
- `settings.fields`: supported platform settings.
- `options`: dynamic option keys available for that integration.
- `capabilities`: curated provider capability data from the connection, when present.

Integration status is one of:

- `connected`
- `requires_reauth`
- `disabled`
- `auth_incomplete`

Do not mutate content through an integration unless its status is `connected`.

## Common Settings Fields

Most platforms support:

- `mediaCrops`: object, optional per-media crop data keyed by media ID and platform.
- `coverUrl`: string, optional media cover or thumbnail URL where supported.

## Platform Matrix

| Platform | Post types | Media types | Max media | Threads | Requires media | Requires title |
| --- | --- | --- | ---: | --- | --- | --- |
| `x` | `post`, `thread` | `image`, `gif`, `video` | 4 | yes | no | no |
| `linkedin` | `post`, `article` | `image`, `video` | 10 | no | no | no |
| `instagram` | `post`, `reel`, `story` | `image`, `video` | 10 | no | yes | no |
| `facebook` | `post`, `reel`, `story` | `image`, `video` | 10 | no | no | no |
| `facebook-page` | `post`, `reel`, `story` | `image`, `video` | 10 | no | no | no |
| `threads` | `post`, `thread` | `image`, `video` | 10 | yes | no | no |
| `tiktok` | `post`, `reel` | `image`, `video` | 35 | no | yes | yes |
| `youtube` | `post`, `reel` | `video` | 1 | no | yes | yes |
| `pinterest` | `post` | `image`, `video` | 1 | no | yes | yes |

Unknown platforms fall back to common post types `post`, `reel`, `story`, `thread`, `article`; media types `image`, `video`; max media 10; no required media/title; no thread support.

## Platform Settings And Options

### X

Settings:

- `xCommunityUrl`: optional string. Community ID is extracted from the URL.

Options: none.

### LinkedIn

Settings: common settings only.

Options: none.

### Instagram

Settings:

- `locationId`: optional string, use option key `locations`.
- `collaborators`: optional string array of usernames or IDs.
- `userTags`: optional object or array.
- `productTags`: optional object, use option key `instagram-products`.

Options:

- `locations`: requires `query`.
- `instagram-catalogs`: no input required.
- `instagram-products`: requires `catalogId`; optional `query`.

Fetch flow for products:

```bash
ezibreezy integrations:options --workspace <workspaceId> --integration <integrationId> --key instagram-catalogs
ezibreezy integrations:options --workspace <workspaceId> --integration <integrationId> --key instagram-products --json product-options.json
```

### Facebook And Facebook Page

Settings:

- `facebookPostType`: optional string override.

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
- `video_cover_timestamp_ms`: number.
- `photo_cover_index`: number.
- `auto_add_music`: boolean.
- `tiktok_post_to_drafts`: boolean.

Options:

- `tiktok-creator-info`: no input required.

Always fetch creator info before selecting `privacy_level`.

### YouTube

Settings:

- `youtubePostType`: `video` or `short`.
- `privacyStatus`: `public`, `unlisted`, or `private`.
- `tags`: string array.
- `categoryId`: string, use option key `youtube-video-categories`.
- `madeForKids`: boolean.
- `notifySubscribers`: boolean.
- `embeddable`: boolean.
- `license`: `youtube` or `creativeCommon`.
- `thumbnailUrl`: string for non-short videos.

Options:

- `youtube-video-categories`: optional `regionCode`, for example `US` or `AU`.

### Pinterest

Settings:

- `boardId`: required string, use option key `pinterest-boards`.
- `link`: optional outbound link string.
- `altText`: optional media alt text string.

Options:

- `pinterest-boards`: no input required.

Always fetch boards before creating or scheduling a Pinterest post.

## Agent Rules

- Call `integrations:capabilities` before platform-specific settings.
- When a settings field has `optionKey`, call `integrations:options` before choosing a value.
- If `requiresMedia` is true, attach uploaded media before scheduling or publishing.
- If `requiresTitle` is true, include `title` before scheduling or publishing.
- Respect `maxMedia` even though the generic content schema allows up to 10 media IDs.
- Do not expose tokens, scopes, stored provider settings, or raw provider payloads; capabilities and options are the safe discovery surface.

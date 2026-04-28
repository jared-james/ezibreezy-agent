# EziBreezy Agent

Installable agent skills and plugins for safely operating EziBreezy through the public API, `@ezibreezy/cli`, and the hosted MCP endpoint.

This package includes the `ezibreezy-social-scheduler` skill for social scheduling, publishing, content management, media library work, approvals, analytics, inbox, grid planning, hashtags, taxonomy, and automation workflows.

Tested with `@ezibreezy/cli >= 0.8.3`.

Hosted MCP compatibility: `https://api.ezibreezy.com/mcp` with browser sign-in for OAuth-capable MCP clients. API-key auth is still available as a fallback for automation and older clients.

## What This Installs

- A portable Agent Skills-standard skill at `skills/ezibreezy-social-scheduler`.
- A Codex plugin manifest for installing the skill in Codex.
- A Claude Code plugin manifest and marketplace for installing the skill in Claude Code.

The plugin is CLI-first by default. MCP can be connected separately for clients that support Streamable HTTP MCP servers. This repo does not include secrets and does not need access to the private EziBreezy application repositories.

## Prerequisites

- Node.js 22 or newer.
- An EziBreezy account with access to at least one workspace.
- For CLI use: either an EziBreezy API key or a browser-approved CLI login.
- For MCP use: an MCP client that supports browser authentication, or an API key for fallback setup.

Check the CLI:

```bash
npx @ezibreezy/cli auth:status
npx @ezibreezy/cli auth:login
npx @ezibreezy/cli workspaces:list
```

For automation, prefer environment variables.

macOS / Linux:

```bash
export EZIBREEZY_API_KEY="ezb_live_..."
export EZIBREEZY_API_URL="https://api.ezibreezy.com"
```

PowerShell:

```powershell
$env:EZIBREEZY_API_KEY="ezb_live_..."
$env:EZIBREEZY_API_URL="https://api.ezibreezy.com"
```

Do not put raw API keys in URLs, prompts, git history, or shared chat.

## Hosted MCP Setup

EziBreezy exposes a hosted MCP endpoint for agent-native tool calls:

```text
https://api.ezibreezy.com/mcp
```

The plugin bundles the current MCP server config in `plugins/ezibreezy-agent/.mcp.json`, so installing the plugin in Claude Code or Codex auto-wires the server. Open your MCP client and choose **Authenticate** for EziBreezy. Your browser opens EziBreezy, asks for approval, and returns to the MCP client.

MCP browser authentication has been validated with Codex and Claude Code. EziBreezy issues 6-hour access tokens and 90-day rotating refresh tokens, so approved clients can stay connected without copying secrets. Organization admins can revoke active AI tool connections from EziBreezy **Settings -> Developer**.

### Manual Setup

If you are not using the plugin, configure MCP directly.

Codex `~/.codex/config.toml`:

```toml
[mcp_servers.ezibreezy]
url = "https://api.ezibreezy.com/mcp"
```

Claude Code:

```bash
claude mcp add --transport http ezibreezy https://api.ezibreezy.com/mcp
```

Or a project `.mcp.json`:

```json
{
  "mcpServers": {
    "ezibreezy": {
      "type": "http",
      "url": "https://api.ezibreezy.com/mcp"
    }
  }
}
```

### API-Key Fallback

Use this only when your MCP client does not support browser sign-in yet, or for server-side automation.

macOS / Linux:

```bash
export EZIBREEZY_API_KEY="ezb_live_..."
```

PowerShell:

```powershell
$env:EZIBREEZY_API_KEY="ezb_live_..."
```

Codex fallback:

```toml
[mcp_servers.ezibreezy]
url = "https://api.ezibreezy.com/mcp"
bearer_token_env_var = "EZIBREEZY_API_KEY"
```

Claude Code fallback:

```bash
claude mcp add --transport http ezibreezy https://api.ezibreezy.com/mcp \
  --header "Authorization: Bearer $EZIBREEZY_API_KEY"
```

Do not put raw API keys in URLs, prompts, git history, shared project files, screenshots, or chat.

## Hosted MCP Vs CLI

- Use MCP when your agent client already has `ezibreezy` connected and you want agent-native reads or low-risk actions such as listing workspaces, inspecting integrations, creating drafts, scheduling explicit posts, upload-session media workflows, analytics summaries, or inbox thread reads.
- Use the CLI for the broadest supported surface, local files, full media upload commands, taxonomy, hashtags, grid planner, approvals, reports, and any workflow where MCP is not connected.
- Use the public API directly only when the CLI and MCP do not cover the required workflow.
- CLI login and MCP login are separate. `ezibreezy auth:login` signs in the CLI only. MCP uses browser sign-in through your MCP client, or the API-key fallback above.
- Browser-approved MCP connections can be revoked in EziBreezy **Settings -> Developer**. Reconnect from the MCP client with **Authenticate** if access is revoked or expires.

## Install In Codex

Add this repository as a Codex plugin marketplace:

```bash
codex plugin marketplace add jared-james/ezibreezy-agent
```

Then open the plugin browser and install `ezibreezy-agent`:

```text
/plugins
```

After installation, invoke the skill explicitly:

```text
$ezibreezy-social-scheduler list my workspaces and connected integrations
```

## Install In Claude Code

From inside Claude Code, add this repository as a plugin marketplace:

```text
/plugin marketplace add jared-james/ezibreezy-agent
/plugin install ezibreezy-agent@ezibreezy-agent
/reload-plugins
```

Plugin skills are namespaced in Claude Code. Invoke it with:

```text
/ezibreezy-agent:ezibreezy-social-scheduler list my workspaces and connected integrations
```

## Manual Local Skill Install

If your agent client does not support plugin marketplaces yet, copy the skill folder into the client-specific user skill directory.

Codex:

```text
~/.agents/skills/ezibreezy-social-scheduler
```

Claude Code:

```text
~/.claude/skills/ezibreezy-social-scheduler
```

## Example JSON Payload

Create `create-content.json`:

```json
{
  "integrationId": "00000000-0000-4000-8000-000000000001",
  "body": "Launch post copy",
  "saveAsDraft": true
}
```

Then run:

```bash
npx @ezibreezy/cli content:create --workspace <workspaceId> --json create-content.json
```

## Safety Model

The skill defaults to drafts, inspects workspace and integration capabilities before mutations, and requires explicit confirmation before externally visible, destructive, or broad actions.

## MCP Tool Coverage

See `plugins/ezibreezy-agent/skills/ezibreezy-social-scheduler/references/mcp-tools.md` for the current hosted MCP tool map and CLI/API fallbacks.

## Links

- API docs: https://api.ezibreezy.com/docs
- Hosted MCP: https://api.ezibreezy.com/mcp
- CLI package: https://www.npmjs.com/package/@ezibreezy/cli
- EziBreezy: https://ezibreezy.com

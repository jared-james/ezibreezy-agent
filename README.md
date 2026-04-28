# EziBreezy Agent

Installable agent skills and plugins for safely operating EziBreezy through the public API, `@ezibreezy/cli`, and the optional hosted MCP endpoint.

This package includes the `ezibreezy-social-scheduler` skill for social scheduling, publishing, content management, media library work, approvals, analytics, inbox, grid planning, hashtags, taxonomy, and automation workflows.

Tested with `@ezibreezy/cli >= 0.8.3`.

Hosted MCP compatibility: `https://api.ezibreezy.com/mcp` with bearer API key auth. The hosted MCP endpoint is optional and is not a replacement for the CLI.

## What This Installs

- A portable Agent Skills-standard skill at `skills/ezibreezy-social-scheduler`.
- A Codex plugin manifest for installing the skill in Codex.
- A Claude Code plugin manifest and marketplace for installing the skill in Claude Code.

The plugin is CLI-first by default. MCP can be connected separately for clients that support Streamable HTTP MCP servers. This repo does not include secrets and does not need access to the private EziBreezy application repositories.

## Prerequisites

- Node.js 22 or newer.
- An EziBreezy account with access to at least one workspace.
- Either an EziBreezy API key or a browser-approved CLI login.

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

From v0.3.0 the plugin bundles the MCP server config in `plugins/ezibreezy-agent/.mcp.json`, so installing the plugin in Claude Code or Codex auto-wires the server. You only need to set `EZIBREEZY_API_KEY` in the environment that launches your agent client.

macOS / Linux:

```bash
export EZIBREEZY_API_KEY="ezb_live_..."
```

PowerShell:

```powershell
$env:EZIBREEZY_API_KEY="ezb_live_..."
```

To persist on Windows so all new shells inherit it:

```powershell
[Environment]::SetEnvironmentVariable("EZIBREEZY_API_KEY", "ezb_live_...", "User")
```

If the env var is unset when the plugin loads, the MCP entry will appear as "needs authentication" rather than failing the install. Set the key, restart your agent client, and the entry connects.

### Manual Setup

If you are not using the plugin, configure MCP directly.

Codex `~/.codex/config.toml`:

```toml
[mcp_servers.ezibreezy]
url = "https://api.ezibreezy.com/mcp"
bearer_token_env_var = "EZIBREEZY_API_KEY"
```

Claude Code:

```bash
claude mcp add --transport http ezibreezy https://api.ezibreezy.com/mcp \
  --header "Authorization: Bearer $EZIBREEZY_API_KEY"
```

Or a project `.mcp.json`:

```json
{
  "mcpServers": {
    "ezibreezy": {
      "type": "http",
      "url": "https://api.ezibreezy.com/mcp",
      "headers": {
        "Authorization": "Bearer ${EZIBREEZY_API_KEY}"
      }
    }
  }
}
```

Do not put raw API keys in URLs, prompts, git history, shared project files, or screenshots.

## Hosted MCP Vs CLI

- Use MCP when your agent client already has `ezibreezy` connected and you want agent-native reads or low-risk actions such as listing workspaces, inspecting integrations, creating drafts, scheduling explicit posts, upload-session media workflows, analytics summaries, or inbox thread reads.
- Use the CLI for the broadest supported surface, local files, full media upload commands, taxonomy, hashtags, grid planner, approvals, reports, and any workflow where MCP is not connected.
- Use the public API directly only when the CLI and MCP do not cover the required workflow.
- Do not use stored browser CLI tokens as the recommended MCP auth path; use `EZIBREEZY_API_KEY`.

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

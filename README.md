# EziBreezy Agent

Installable agent skills and plugins for safely operating EziBreezy through the public API and `@ezibreezy/cli`.

This package includes the `ezibreezy-social-scheduler` skill for social scheduling, publishing, content management, media library work, approvals, analytics, inbox, grid planning, hashtags, taxonomy, and automation workflows.

Tested with `@ezibreezy/cli >= 0.8.3`.

## What This Installs

- A portable Agent Skills-standard skill at `skills/ezibreezy-social-scheduler`.
- A Codex plugin manifest for installing the skill in Codex.
- A Claude Code plugin manifest and marketplace for installing the skill in Claude Code.

The plugin is CLI-first. It does not include secrets and does not need access to the private EziBreezy application repositories.

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

## Links

- API docs: https://api.ezibreezy.com/docs
- CLI package: https://www.npmjs.com/package/@ezibreezy/cli
- EziBreezy: https://ezibreezy.com


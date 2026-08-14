# Catalyst by Zoho - Codex Plugin

A Codex plugin that bundles:

- Eight **regional OAuth MCP definitions** for direct Catalyst infrastructure management through `CatalystbyZoho_*` tools, including tables, buckets, cache, and other project resources.
- A full set of **agent skills** giving Codex deep knowledge of Catalyst by Zoho services, SDKs, CLI, architecture patterns, pricing, deployment guidance, and targeted Catalyst migration guidance such as File Store to Stratus, legacy Client to Slate, and Development to Production promotion.
- A **`SessionStart` hook** that detects Catalyst workspaces, injects active org/project/environment context, warns about live resources, and checks local prerequisites.
- Codex plugin metadata, branding, and marketplace configuration for installing from the Codex plugin marketplace or directly from GitHub.

## Installation

### From the Codex Plugin Marketplace

Install the published plugin directly from the Codex curated marketplace:

```bash
codex plugin add catalyst-by-zoho@openai-curated
```

After installation, start a new Codex task, explicitly select the Catalyst account's data center, and restart once more so the regional MCP tools are loaded.

### Directly From GitHub

Add this repository as a Git-backed plugin marketplace, then install the plugin from it:

```bash
codex plugin marketplace add https://github.com/catalystbyzoho/codex-plugin --ref main
codex plugin add catalyst-by-zoho@catalyst-by-zoho-marketplace
```

Use this path when you want the latest plugin source from GitHub instead of the curated marketplace snapshot. After installation, start a new Codex task.

## Structure

```text
.agents/plugins/marketplace.json             Marketplace entry for GitHub/direct installs
catalyst-by-zoho/.codex-plugin/plugin.json   Codex plugin manifest - links skills/ and .mcp.json
catalyst-by-zoho/.mcp.json                   Disabled regional OAuth MCP definitions
catalyst-by-zoho/assets/                     Plugin logos and branding assets
catalyst-by-zoho/hooks/hooks.json            SessionStart hook declaration
catalyst-by-zoho/hooks/catalyst-context.js   Catalyst workspace context and prerequisite hook
catalyst-by-zoho/scripts/switch-dc.js         Safe Codex plugin-policy DC selector
catalyst-by-zoho/skills/                     17 skill folders (routing, DC selection, and service skills)
```

## Regional MCP Servers

The plugin bundles one literal OAuth endpoint per supported regional data center through `catalyst-by-zoho/.mcp.json`. Every endpoint is disabled until the user explicitly selects a DC:

```json
{
  "catalyst-in": {
    "url": "https://catalyst.zohomcp.in/mcp/message",
    "auth": "oauth",
    "enabled": false
  }
}
```

The `catalyst-switch-dc` skill updates only the supported plugin enablement policy in `~/.codex/config.toml`, disables every other Catalyst region, and requires a restart. It never edits the managed plugin bundle or OAuth credentials.

Once the selected endpoint is authorized, it exposes Zoho MCP meta-tools such as `ZohoMCP_getSchema`, `ZohoMCP_executeTool`, `ZohoMCP_listTools`, and `ZohoMCP_getFeatures`. Catalyst operations are invoked through `ZohoMCP_executeTool` using `CatalystbyZoho_*` tool names.

## Data Centers

Catalyst MCP supports these data centers: `US`, `EU`, `IN`, `AU`, `CA`, `SA`, `JP`, and `UAE`.

Ask Codex to **"Switch Catalyst MCP to `<DC>`."** The `catalyst-switch-dc` skill displays the exact endpoint and persists an exclusive selection. Restart Codex and complete regional OAuth before using MCP. Never continue Catalyst MCP operations in the session that changed the DC.

### Migrating from plugin 0.0.2

Version 0.1.0 replaces the registered `.app.json` connection with bundled regional MCP definitions. After updating:

1. If the former Catalyst app/connector still appears in Codex settings, disconnect it so it cannot expose a second `ZohoMCP_*` tool set.
2. Ask Codex to switch Catalyst MCP to the account's explicit DC.
3. Restart Codex and complete OAuth for the selected regional endpoint.

## Hooks

`catalyst-by-zoho/hooks/hooks.json` declares a `SessionStart` hook:

```json
{
  "hooks": {
    "SessionStart": [
      {
        "hooks": [
          {
            "type": "command",
            "command": "node \"${PLUGIN_ROOT}/hooks/catalyst-context.js\"",
            "timeout": 15
          }
        ]
      }
    ]
  }
}
```

When a Codex task starts in a Catalyst CLI workspace, the hook reads `.catalystrc`, surfaces the active org, project, environment, domain, timezone, and selected MCP DC, and adds a visible warning that MCP operations affect live cloud resources. It blocks MCP guidance when zero or multiple regions are enabled and checks local prerequisites such as Node.js, Catalyst CLI, CLI login state, declared runtimes, and Docker for custom runtimes.

## Skills

| Skill | Covers |
|-------|--------|
| `catalyst-by-zoho` | Routing skill - full service catalog, architecture guidance, entry point |
| `catalyst-basics` | Project setup, CLI, environments, IDs, Development to Production promotion |
| `catalyst-functions` | Serverless functions (all 7 types), API Gateway, Security Rules |
| `catalyst-appsail` | AppSail PaaS, Docker, managed runtimes |
| `catalyst-slate` | Frontend hosting, Git deploy, legacy Client to Slate migration |
| `catalyst-datastore` | Data Store and ZCQL |
| `catalyst-stratus` | Object storage, signed URLs, S3/GCS and File Store migration guidance |
| `catalyst-nosql` | NoSQL document storage |
| `catalyst-cache` | In-memory key-value cache |
| `catalyst-authentication` | Auth and OAuth Connections |
| `catalyst-signals` | Event bus, publishers, rules |
| `catalyst-smartbrowz` | Headless browser, PDF/screenshot generation |
| `catalyst-zia` | Zia AI services and QuickML |
| `catalyst-sdk` | SDKs - Node.js, Web, Python, Java, Android, iOS, Flutter |
| `catalyst-pricing` | Free tier, pay-as-you-go rates, cost estimation |
| `catalyst-zoho-mcp` | `CatalystbyZoho_*` MCP tool usage and setup |
| `catalyst-switch-dc` | Explicit regional endpoint selection and safe Codex policy updates |

Each skill's `SKILL.md` is loaded by Codex when the request matches a relevant trigger phrase. Skill content is sourced from the official [catalystbyzoho/agent-skills](https://github.com/catalystbyzoho/agent-skills) repository and adapted for Codex plugin packaging.

## Verifying the Plugin

```bash
codex plugin list | grep catalyst-by-zoho
```

In a new Codex task, ask:

```text
What Catalyst skills are available?
```

Codex should show the Catalyst by Zoho routing skill and the focused `catalyst-*` service skills. After selecting one DC, restarting, and completing OAuth, Codex should also have access to the `ZohoMCP_*` meta-tools for Catalyst MCP operations.

To verify the hook, start a new Codex task from a Catalyst CLI workspace that contains `.catalystrc`; the task should receive Catalyst workspace context and a live-resource warning at session start.

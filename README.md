# Catalyst by Zoho - Codex Plugin

A Codex plugin that bundles:

- A **Catalyst by Zoho connector** for MCP-backed workflows and direct Catalyst infrastructure management through `CatalystbyZoho_*` tools, including tables, buckets, cache, and other project resources.
- A full set of **agent skills** giving Codex deep knowledge of Catalyst by Zoho services, SDKs, CLI, architecture patterns, pricing, deployment guidance, and targeted Catalyst migration guidance such as File Store to Stratus, legacy Client to Slate, and Development to Production promotion.
- A **`SessionStart` hook** that detects Catalyst workspaces, injects active org/project/environment context, warns about live resources, and checks local prerequisites.
- Codex plugin metadata, branding, and marketplace configuration for installing from the Codex plugin marketplace or directly from GitHub.

## Installation

### From the Codex Plugin Marketplace

Install the published plugin directly from the Codex curated marketplace:

```bash
codex plugin add catalyst-by-zoho@openai-curated
```

After installation, start a new Codex task so the Catalyst by Zoho skills and connector tools are loaded.

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
catalyst-by-zoho/.codex-plugin/plugin.json   Codex plugin manifest - links skills/ and .app.json
catalyst-by-zoho/.app.json                   Codex app/connector binding for Catalyst by Zoho
catalyst-by-zoho/assets/                     Plugin logos and branding assets
catalyst-by-zoho/hooks/hooks.json            SessionStart hook declaration
catalyst-by-zoho/hooks/catalyst-context.js   Catalyst workspace context and prerequisite hook
catalyst-by-zoho/skills/                     16 skill folders (routing skill + 15 service-specific skills)
```

## Connector

The plugin links to the Catalyst by Zoho Codex connector through `catalyst-by-zoho/.app.json`:

```json
{
  "apps": {
    "catalyst-by-zoho": {
      "id": "asdk_app_6a16bcb9a37081919b0db1d81010fb2f"
    }
  }
}
```

When the connector is enabled and authorized in Codex, it exposes Zoho MCP meta-tools such as `ZohoMCP_getSchema`, `ZohoMCP_executeTool`, `ZohoMCP_listTools`, and `ZohoMCP_getFeatures`. Catalyst operations are then invoked through `ZohoMCP_executeTool` using `CatalystbyZoho_*` tool names.

## Data Centers

Catalyst MCP supports these data centers: `US`, `EU`, `IN`, `AU`, `CA`, `SA`, `JP`, and `UAE`.

To change the connected data center, update the Catalyst by Zoho connector configuration in Codex and reconnect the task. The skill `catalyst-zoho-mcp` includes data-center guidance and setup references.

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

When a Codex task starts in a Catalyst CLI workspace, the hook reads `.catalystrc`, surfaces the active org, project, environment, domain, and timezone, and adds a visible warning that MCP operations affect live cloud resources. It also checks for common local prerequisites such as Node.js, Catalyst CLI, CLI login state, declared runtimes, and Docker for custom runtimes.

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
| `catalyst-zoho-mcp` | `CatalystbyZoho_*` MCP tool usage and connector setup |

Each skill's `SKILL.md` is loaded by Codex when the request matches a relevant trigger phrase. Skill content is sourced from the official [catalystbyzoho/agent-skills](https://github.com/catalystbyzoho/agent-skills) repository and adapted for Codex plugin packaging.

## Verifying the Plugin

```bash
codex plugin list | grep catalyst-by-zoho
```

In a new Codex task, ask:

```text
What Catalyst skills are available?
```

Codex should show the Catalyst by Zoho routing skill and the focused `catalyst-*` service skills. If connector tools are enabled, Codex should also have access to the `ZohoMCP_*` meta-tools for Catalyst MCP operations.

To verify the hook, start a new Codex task from a Catalyst CLI workspace that contains `.catalystrc`; the task should receive Catalyst workspace context and a live-resource warning at session start.

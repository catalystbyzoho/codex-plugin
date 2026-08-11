# Using Catalyst Skills with Codex

> Shared steps — Installation, getting your Zoho MCP URL (MCP Setup Step 1), and the pre-flight checklist — are in `setup-common.md`. This file covers only what's specific to Codex.

## Skill Activation

Codex loads these skills from the installed Catalyst by Zoho plugin. To verify:

1. Open Codex in your Catalyst project directory.
2. Ask: "What Catalyst skills are available?"
3. Codex should list the Catalyst by Zoho skill index plus the focused Catalyst service skills.

## MCP Setup — Step 2: Add the MCP server to Codex

First complete **Step 1** in `setup-common.md` to get your Zoho MCP URL. In Codex, enable the Catalyst by Zoho connector/plugin and provide the DC-specific MCP URL when the connector setup asks for it:

```text
https://catalyst.zohomcp.com/mcp/message
```

Use the DC-specific host from `setup-common.md`; the US URL above is only the default example.

After saving, restart or reconnect the Codex task. Confirm MCP is connected by looking for the `ZohoMCP_*` meta-tools (`ZohoMCP_getSchema`, `ZohoMCP_executeTool`, `ZohoMCP_listTools`, `ZohoMCP_getFeatures`) in the available tool list. The `CatalystbyZoho_*` operations are not listed as tools — they are `tool_name` values passed to `ZohoMCP_executeTool`.

## Common Errors (Codex)

See `setup-common.md` for errors common to all clients. Codex-specific:

| Error | Cause | Fix |
|-------|-------|-----|
| Catalyst skills not appearing | Plugin not installed or the task was opened before installation | Install or update the Catalyst by Zoho plugin, then open a new Codex task |
| MCP tools not appearing | Connector not authorized or task has not reconnected | Reconnect the Catalyst connector, complete browser authorization, then restart the Codex task |

# agent-mcp

[Autonoma](https://autonoma.app) is an agentic end-to-end testing platform: you describe tests in natural language, and Autonoma runs them against a live preview deployment of your app on every pull request, with AI handling element selection, assertions, and self-healing.

This repo is a marketplace plugin listing for Autonoma's remote MCP server. It doesn't contain the server's source (that lives in [Autonoma-AI/autonoma](https://github.com/Autonoma-AI/autonoma), a public mirror of our main app); this repo only points coding agents at the hosted server, so it can be listed on Cursor's and Codex's plugin marketplaces.

## What the MCP server does

When Autonoma flags a problem on a pull request, this connects your coding agent to the live evidence instead of making you copy it out of a dashboard:

| Tool | What it does |
| --- | --- |
| `list_apps` | Lists the repos you can debug across your organizations |
| `get_deploy_status` | Per-service deploy status, endpoints, and the latest build outcome for a PR's preview |
| `get_endpoints` | The preview URL, a suggested SDK URL, and one entry per service |
| `get_build_logs` / `get_app_logs` | Build or runtime log lines, from the tail (a failure) or head (startup) |
| `diagnose_deploy` | All the evidence in one call, plus a rule-based failure classification |
| `get_secret_status` | The env-var surface per app: topology connections and secret-backed vars (masked, never the value) |
| `set_secret` | Sets or removes a secret env var's value and applies the minimal rebuild/restart |
| `edit_previewkit_config` | Changes structural config (build path, Dockerfile, port, health check, connections) and rebuilds |
| `wait_for_deploy` | Blocks until a rebuild/redeploy settles, then reports the outcome |

Every tool takes your repo as `owner/repo`; the per-PR tools also take the PR number. Your agent infers the repo from `git remote` on its own - you don't need to tell it. Auth is OAuth 2.0: your client opens a browser to sign into Autonoma once; every call after that is scoped to your own orgs and repos.

Full tool reference, manual per-client setup instructions, and troubleshooting: https://docs.autonoma.app/mcp
Server source (the `apps/api/src/mcp/` directory): https://github.com/Autonoma-AI/autonoma

## Layout

```
.cursor-plugin/plugin.json   Cursor Marketplace manifest
mcp.json                     MCP server config Cursor's manifest points to (remote, URL-based)
.codex-plugin/plugin.json    Codex Marketplace manifest
.mcp.json                    MCP server config Codex's manifest points to (via the mcp-remote bridge)
```

Both manifests describe the same remote server; each marketplace just expects its own manifest filename and config shape.

## Submitting

- **Cursor Directory** (community): https://cursor.directory/plugins/autonoma - **published**.
- **Cursor Marketplace** (official): https://cursor.com/marketplace/publish - paste this repo's URL. Manual review, plugin must stay open source.
- **Codex**: anyone can already add this repo directly as a local marketplace (see below) - no review needed, just not centrally discoverable. The official OpenAI Apps/plugin directory (shared between ChatGPT and Codex) is a heavier submission at `platform.openai.com/plugins`: requires an org with "Apps Management: Write" access, verified developer/business identity, domain verification (a token at `https://autonoma.app/.well-known/openai-apps-challenge`), tool `readOnlyHint`/`openWorldHint`/`destructiveHint` annotations, and exactly 5 positive + 3 negative written test cases.

Before submitting anywhere, add a logo asset and double check `privacyPolicyURL` / `termsOfServiceURL` in `.codex-plugin/plugin.json` resolve to real pages.

## Local testing

Verified working end to end against production with both CLIs:

**Cursor** - install the server directly (this is also what "Add to Cursor" on a marketplace listing ultimately writes):

```bash
# ~/.cursor/mcp.json
{ "mcpServers": { "autonoma": { "url": "https://autonoma.app/v1/mcp/debug" } } }

cursor agent mcp login autonoma   # opens the OAuth browser flow
cursor agent mcp list-tools autonoma
cursor agent --print --force --approve-mcps "Use the autonoma MCP server to check the preview deploy status for <owner/repo> PR <n>."
```

**Codex** - add this repo as a local marketplace, then install the plugin:

```bash
codex plugin marketplace add /path/to/agent-mcp
codex plugin add autonoma@autonoma
codex mcp list   # confirms the bundled server registered
codex             # interactive; approve the MCP tool call when prompted
```

Note: `cursor agent --plugin-dir` does **not** surface a plugin's bundled MCP server into `cursor agent mcp list` - use the `~/.cursor/mcp.json` route above to actually exercise the server.

## License

MIT, see [LICENSE](./LICENSE).

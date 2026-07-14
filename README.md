# agent-mcp

Marketplace plugin listing for [Autonoma](https://autonoma.app)'s remote MCP server. This repo does not contain the server itself (that lives in the Autonoma API); it only points coding agents at it, so it can be listed on Cursor's and Codex's plugin marketplaces.

## What this connects to

`https://api.autonoma.app/v1/mcp/debug` - Streamable HTTP, OAuth 2.0. When Autonoma flags a problem on a pull request, this server's tools let a coding agent read the live evidence (deploy status, build/app logs, a rule-based diagnosis, secret status) and fix the cause: set a missing secret value or edit the preview's build/wiring config, then confirm the fix - all from inside your own repo.

Full tool reference and manual setup instructions for every client: https://docs.autonoma.app/mcp

## Layout

```
.cursor-plugin/plugin.json   Cursor Marketplace manifest
mcp.json                     MCP server config Cursor's manifest points to (remote, URL-based)
.codex-plugin/plugin.json    Codex Marketplace manifest
.mcp.json                    MCP server config Codex's manifest points to (via the mcp-remote bridge)
```

Both manifests describe the same remote server; each marketplace just expects its own manifest filename and config shape.

## Submitting

- **Cursor Marketplace**: https://cursor.com/marketplace/publish - paste this repo's URL. Manual review, plugin must stay open source.
- **Cursor Directory** (community): https://cursor.directory/plugins/new
- **Codex Marketplace**: submit this repo's URL from the "Submit Plugin" page in Codex. Automated checks, then manual review.

Before submitting, add a logo asset and double check `privacyPolicyURL` / `termsOfServiceURL` in `.codex-plugin/plugin.json` resolve to real pages.

## Local testing

Point your client's plugin/dev-install flow at this repo path to test before publishing:

- Cursor: `cursor.com/docs/plugins` covers local plugin installs.
- Codex: `codex plugin install <path-to-this-repo>` (see Codex plugin docs for the current command name).

## License

MIT, see [LICENSE](./LICENSE).

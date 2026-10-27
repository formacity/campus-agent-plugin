# Campus agent plugin

Two manually invoked workflows for [Forma Campus](https://campus.forma.city): Monday **Weekly Goals** and Friday **Ship & Show**. They bundle presentation coaching, examples and the existing Campus MCP connection. Installation does not grant account access; Campus authorization happens separately in your agent.

## Install

### Claude Code

In a Claude Code session:

```text
/plugin install campus --marketplace formacity/campus-agent-plugin
```

Or from a terminal with Claude Code 2.1.292 or later:

```sh
claude plugin install campus --marketplace formacity/campus-agent-plugin
```

On older versions, add the marketplace first with `/plugin marketplace add formacity/campus-agent-plugin`, then `/plugin install campus@formacity`. Reload plugins or start a new session after installation. Run `/campus:monday` or `/campus:friday` with your request.

### Codex

In a terminal with Codex CLI 0.160.0 or later:

```sh
codex plugin marketplace add formacity/campus-agent-plugin
codex plugin add campus@formacity
```

Start a new Codex chat after installation. Invoke `$campus:monday` or `$campus:friday` from the skills picker. The same package can be installed through the Codex desktop plugin directory after registering the marketplace. Custom marketplace availability can depend on your client version and organization policy.

### Connect Campus

When your client requests Campus authorization, use its native OAuth flow and sign in with your own Campus account. Select presentation access: read, edit and save; offline access permits token refresh. These skills need no admin permissions. Plugin connections may need their own authorization even if you previously connected Campus directly.

Keep existing MCP connections and approval rules. The bundled connection points to `https://campus.forma.city/api/mcp`. It does not run a local server or bundle credentials. Use the plugin-provided Campus connection for these workflows; do not issue the same edit through two connections.

Try a read-only request first: "Use Campus to show my available Monday and Friday sessions. Do not change anything." Then, for example, invoke Monday with "Help me sharpen the goal in chat", or Friday with "Update my presentation for [the exact session date] using these results."

**Campus edits save immediately.** For accounts with cohort access, edits are visible to the cohort. Other members' decks remain owner-only. The skills begin with reading and coaching; they change Campus only when your request authorizes an edit.

## Updates

We release new guidance by changing the plugin version and pushing it to this repository. The hosted MCP updates independently; updating the app does not update installed skill files.

For Claude Code, enable auto-update for **formacity** under `/plugin` → Marketplaces. It is off by default for custom marketplaces. To update manually in a terminal:

```sh
claude plugin marketplace update formacity
claude plugin update campus@formacity
```

Reload plugins or start a new session to use updated files.

For Codex, refresh the marketplace and installed plugin explicitly:

```sh
codex plugin marketplace upgrade formacity
codex plugin add campus@formacity
```

Start a new chat after updating. This repository does not promise an automatic update schedule for Codex custom marketplaces.

## Package and development

`plugins/campus/` contains one shared copy of the skills and guidance. Its Claude-compatible manifest also loads in Codex. The two small catalogs at `.claude-plugin/marketplace.json` and `.agents/plugins/marketplace.json` point to that same package; they contain no duplicated skill content. This public repository contains no Campus app source, member data or credentials.

Manual invocation is configured separately for each client: `disable-model-invocation` in skill frontmatter for Claude Code, and `policy.allow_implicit_invocation: false` in each skill's `agents/openai.yaml` for Codex.

Validate locally:

```sh
claude plugin validate .
claude plugin validate plugins/campus
```

For an isolated install rehearsal, set `CLAUDE_CONFIG_DIR` or `CODEX_HOME` to a fresh temporary directory before running the install commands with an absolute local marketplace path. Do not replace your everyday client configuration. A local install confirms packaging; it does not prove OAuth, model invocation, editing or rendering.

Before a release, inspect both skills and their relative guidance links, validate the manifests, and rehearse clean Git-backed installs in both clients. Bump `plugins/campus/.claude-plugin/plugin.json` for every release, commit, tag `vX.Y.Z` and push the commit and tag together. Users track the default branch unless they deliberately pin a ref. Include no private examples, signed URLs or local configuration.

The package contains no hooks, executables or background jobs. To remove it, use `claude plugin uninstall campus@formacity` or `codex plugin remove campus@formacity`. Revoke its Campus grant separately in Campus → MCP → My connections if you also want to remove account access.

Format references: [Claude Code plugins](https://code.claude.com/docs/en/plugins-reference), [Claude marketplace updates](https://code.claude.com/docs/en/plugins/host-marketplace#keep-users-up-to-date), [Codex plugin packaging](https://developers.openai.com/plugins/build/plugins).

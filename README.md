# Campus agent plugin

One connection to [Forma Campus](https://campus.forma.city), with two manually invoked presentation skills: Monday **Weekly Goals** and Friday **Ship & Show**. The same connection supports optional admin access for eligible accounts. Installing the plugin does not grant account access.

## Using the skills

Invoke Monday or Friday in the conversation where your agent knows your work. It uses that context and, when connected, your Campus presentation to suggest a draft, clarify important gaps and improve the result. You can also start a fresh chat and supply the context. It does not have automatic access to your whole week or other apps.

The skills run only when you invoke them. They help prepare content in chat; a request to create or update your Campus deck authorizes the edit. You can keep the work in chat without signing in. The current coaching principles are a first draft, pending alignment with the cohort lead.

## Install

Start in [Campus → MCP](https://campus.forma.city/desktop?app=agent-connections). Choose Claude Code or Codex, copy the install prompt and paste it into that agent. It installs the plugin, checks discovery and guides native sign-in. Setup is complete only after a read-only Campus request succeeds.

For manual installation:

### Claude Code

In a Claude Code session:

```text
/plugin install campus --marketplace formacity/campus-agent-plugin
```

Or from a terminal with Claude Code 2.1.292 or later:

```sh
claude plugin install campus --marketplace formacity/campus-agent-plugin
```

On older versions, add the marketplace first with `/plugin marketplace add formacity/campus-agent-plugin`, then `/plugin install campus@formacity`. If the skills are missing after installation, reload plugins or start a new session. Invoke `/campus:monday` or `/campus:friday` with your request.

### Codex

In a terminal with Codex CLI 0.160.0 or later:

```sh
codex plugin marketplace add formacity/campus-agent-plugin
codex plugin add campus@formacity
```

Invoke `$campus:monday` or `$campus:friday` from the skills picker. If they are missing after installation, start a new chat; no restart is needed when they already work. The same package can be installed through the Codex desktop plugin directory after registering the marketplace. Custom marketplace availability can depend on your client version and organization policy.

### Connect Campus

Use native OAuth to sign in with your Campus account. The presentation skills need read, edit and save access; offline access permits token refresh. Admins can optionally approve additional permissions on this same connection. Other members cannot grant or use admin access. Changing permissions requires reauthorization; it does not require another MCP entry. Existing standalone connections remain untouched.

For admin setup, use the Campus install prompt even if the plugin is already installed. It reuses the plugin and offers reauthorization when needed. In Codex, admin tool approval overrides belong under `plugins."campus@formacity".mcp_servers.campus.tools.<tool>.approval_mode = "prompt"`, not a new `mcp_servers` entry. Claude Code requires interactive per-call approval. If the client cannot enforce these approvals, leave admin write permissions ungranted. The Monday/Friday skills do not perform admin operations.

If sign-in expires or fails, restart native authorization for the same connection. Retain safe error details without exposing tokens or authorization URLs. Distinguish plugin installation, pending sign-in and verified access; never treat configuration alone as a successful connection.

Keep existing MCP connections and approval rules. The bundled connection points to `https://campus.forma.city/api/mcp`. It does not run a local server or bundle credentials. Use the plugin-provided Campus connection for these workflows; do not issue the same edit through two connections.

Try a read-only request first: "Use Campus to show my available Monday and Friday sessions. Do not change anything." Then, for example, invoke Monday with "Help me sharpen the goal in chat", or Friday with "Update my presentation for [the exact session date] using these results."

**Campus edits save immediately.** For accounts with cohort access, edits are visible to the cohort. Other members' decks remain owner-only. The skills change Campus only when your request authorizes an edit, then preview the result. There is no separate publication step.

## Updates

We release new guidance by changing the plugin version and pushing it to this repository. The hosted MCP updates independently; updating the app does not update installed skill files.

From 0.1.3, invoking either skill asks your agent to compare its installed version with the public GitHub manifest. A newer version gets one update offer per version per conversation. Updates need your agreement; presentation work continues if you decline or the check is unavailable. This uses the agent’s existing read-only network tools, with no Campus sign-in, telemetry or background process. Versions 0.1.2 and earlier need one manual update to gain this check.

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

For an isolated install rehearsal, set `CLAUDE_CONFIG_DIR` or `CODEX_HOME` to a fresh temporary directory before running the install commands with an absolute local marketplace path. Do not replace your everyday client configuration. A local install confirms packaging; it does not prove OAuth, model invocation, editing or rendering. Check behavior with an existing-context invocation, an empty chat, a narrow revision request, a declined suggestion, and an unfinished Friday goal. The agent should produce useful content without inventing facts, repeatedly questioning the member or editing Campus without a request. Check signed-in editing and preview separately with an authorized presentation.

Before a release, inspect both skills and their relative guidance links, validate the manifests, and rehearse clean Git-backed installs in both clients. Bump `plugins/campus/.claude-plugin/plugin.json` for every release, commit, tag `vX.Y.Z` and push the commit and tag together. Users track the default branch unless they deliberately pin a ref. Include no private examples, signed URLs or local configuration.

The package contains no hooks, executables or background jobs. To remove it, use `claude plugin uninstall campus@formacity` or `codex plugin remove campus@formacity`. Revoke its Campus grant separately in Campus → MCP → My connections if you also want to remove account access.

Format references: [Claude Code plugins](https://code.claude.com/docs/en/plugins-reference), [Claude marketplace updates](https://code.claude.com/docs/en/plugins/host-marketplace#keep-users-up-to-date), [Codex plugin packaging](https://developers.openai.com/plugins/build/plugins).

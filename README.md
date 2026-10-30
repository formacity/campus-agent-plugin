# Campus agent plugin

Agent entry point. Follow the user's request: explain, install, update or modify the package.

Two manually invoked presentation skills: Monday **Weekly Goals** and Friday **Ship & Show**, plus one authenticated Campus MCP connection. Shared by Claude Code and Codex.

| Identifier | Value |
| --- | --- |
| Repository | `https://github.com/formacity/campus-agent-plugin` |
| Marketplace / plugin | `formacity` / `campus@formacity` |
| Package / version source | [`plugins/campus/`](plugins/campus/) / [manifest](plugins/campus/.claude-plugin/plugin.json) |
| Bundled MCP | `https://campus.forma.city/api/mcp`. Remote Streamable HTTP, native OAuth |
| Campus connection management | [Campus → Agents](https://campus.forma.city/desktop?app=agent-connections) |

## Install

Complete the missing steps. Do not send the user elsewhere to copy another installation prompt.

### 1. Inspect

Identify the current client; inspect its version, installed plugins and marketplaces. Ask only if the client is unclear. Reuse a matching installation and working authorization.

Report conflicting marketplace sources or local development copies before changing them. Preserve other plugins, connections, credentials and approval rules. Use the bundled connection; add no standalone MCP entry, proxy or local server.

### 2. Install for this client

**Claude Code** (CLI 2.1.292+):

```sh
claude plugin install campus --marketplace formacity/campus-agent-plugin
```

If this version lacks `--marketplace`, register the source if missing, then install:

```sh
claude plugin marketplace add formacity/campus-agent-plugin
claude plugin install campus@formacity
```

**Codex** (CLI 0.160.0+):

```sh
codex plugin marketplace add formacity/campus-agent-plugin
codex plugin add campus@formacity
```

Skip registration if the matching marketplace exists. Codex desktop uses this same package. If CLI or marketplace support is unavailable, give the required client update or native UI step.

### 3. Verify loading

Check the installed version and both skills in the client's catalog or picker. They are manual-only: absence from automatic suggestions is not a failure. Do not invoke a skill just to test installation.

In Codex, skills installed during this turn may become available on the next turn in the same chat. If this turn cannot see them, do not infer that a new chat is required; ask the user to try them on the next turn here. Skill discovery and MCP availability are separate: continue authorization in this chat when the client allows it. Suggest a reload or new chat only if the client requires it or the skills remain unavailable on a later turn. Use supported listings and discovery, not generated protocols or internal debug APIs.

### 4. Connect

Discover the bundled server's actual client-side name. Reuse working authorization; otherwise start native OAuth on that connection now. Ask the user to complete browser consent; do not end setup with only 'sign-in may be needed'.

Presentation editing needs `presentations:read,presentations:draft,presentations:save`; read-only use needs `presentations:read`. Optional `offline_access` permits token refresh. See [Admin access](#admin-access) for additional permissions.

Codex: `codex mcp login <discovered server name> --scopes <requested scopes>`. Claude Code: native MCP authentication. Keep credentials in native storage; never request or expose secrets or OAuth callback URLs.

Check the login process result after consent. If the browser cannot open, give the native authentication step. On failure or expiry, retain the installation and restart sign-in on the same connection. Report safe error details; on resume, check the previous result or try a read first.

### 5. Verify access

Call `campus_presentations_context` once, or an authorized admin read if presentation access was declined. Make no Campus changes during setup.

Report **connected** only after a successful read. If the current chat cannot access the MCP tool, explain the verified limitation and the exact step needed to finish the read. Finish with the two skill invocations.

## Use the skills

| Client | Monday | Friday |
| --- | --- | --- |
| Claude Code | `/campus:monday` | `/campus:friday` |
| Codex | `$campus:monday` | `$campus:friday` |

Follow the installed skill and its relative guidance when invoked. Use available context without implying access to the member's whole week or other apps.

Prepare in chat without sign-in. A request to create or update a Campus deck authorizes edits; a bare invocation does not. Edits autosave immediately: cohort-visible for accounts with cohort access, otherwise owner-only. No separate publish step. Follow [Campus operations](plugins/campus/guidance/campus.md) for editing and preview.

## Updates

Inspect the installation and marketplace source first. If Campus is absent, use [Install](#install). Do not replace a conflicting source or local development copy.

When the user requests or accepts an update, run the relevant commands in the existing installation scope:

**Claude Code**

```sh
claude plugin marketplace update formacity
claude plugin update campus@formacity
```

Keep the existing installation scope (`--scope` when needed). Follow the client's reload instruction.

**Codex**

```sh
codex plugin marketplace upgrade formacity
codex plugin add campus@formacity
```

Start a new chat to load updated skills. Verify the installed version; do not confuse updated files with reloaded instructions. Preserve connections, credentials, approval rules and auto-update preferences. Request no additional Campus permissions.

Skills from 0.1.3 onward follow the [update-check instructions](plugins/campus/guidance/updates.md): compare with the public manifest, offer a newer version once per conversation, and continue if declined or unavailable. Older installations need a manual update to gain that check. Hosted MCP deployments and installed skill updates are independent.

## Admin access

Use the same bundled connection. Admin eligibility is enforced by Campus, not by the plugin. Adding permissions requires native reauthorization, not another MCP entry. Use the account-specific setup prompt in [Campus → Agents](https://campus.forma.city/desktop?app=agent-connections) for the current admin scopes and protected tool list. Do not disconnect working access without explaining why reauthorization requires it.

- **Codex:** protected tools need `approval_mode = "prompt"` under `plugins."campus@formacity".mcp_servers.campus.tools.<tool>`. Effective `approvals_reviewer` must be `"user"`; `"auto_review"` can approve calls without a human prompt. A config file alone does not prove the active policy.
- **Claude Code:** preserve `anthropic/requiresUserInteraction=true` and interactive per-call approval. Do not add allow rules or perform admin writes headlessly.

If human review is unavailable or unverified, leave admin write permissions ungranted and do not use existing admin write access. Do not change global approval settings or perform a write to test prompting. Monday/Friday skills require no admin operations.

## Remove

On request, run `claude plugin uninstall campus@formacity` in its existing scope or `codex plugin remove campus@formacity`. Revoke account access separately in Campus → Agents → My connections.

## Work on this repository

- Edit the shared package under `plugins/campus/`. Both [Claude](.claude-plugin/marketplace.json) and [Codex](.agents/plugins/marketplace.json) catalogs point there; do not duplicate skill content.
- Preserve manual invocation: `disable-model-invocation: true` in each `SKILL.md`; `policy.allow_implicit_invocation: false` in each `agents/openai.yaml`.
- Keep examples fictional. Include no member data, credentials, signed URLs or local configuration. The package has no hooks, executables or background jobs.
- Validate manifests and relative guidance links. Keep this README's `#updates` anchor: installed skills link to it.

```sh
claude plugin validate .
claude plugin validate plugins/campus
```

Rehearse installs in fresh temporary `CLAUDE_CONFIG_DIR` and `CODEX_HOME` directories. Check real skill outputs with existing context, an empty chat, a narrow edit, a declined suggestion and unfinished Friday work. Packaging checks do not prove OAuth, tool execution or rendering; verify those separately when affected, using authorized isolated data.

For a package release, bump the [manifest](plugins/campus/.claude-plugin/plugin.json), commit, tag `vX.Y.Z`, push the commit and tag together, and verify fresh Git-backed installs in both clients. Users track the default branch unless pinned. README-only changes outside the package do not require a plugin version bump.

Client references: [Claude plugins](https://code.claude.com/docs/en/plugins-reference), [Codex plugin packaging](https://developers.openai.com/plugins/build/plugins).

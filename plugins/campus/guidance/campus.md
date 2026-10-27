# Working with Campus

## Connection and authority

Use the Campus MCP tools already available in this client. Tool names below are base names; clients can add a plugin or connection prefix. This plugin bundles the production endpoint, `https://campus.forma.city/api/mcp`. Do not start a server or invent a different endpoint.

If tools are missing, explain that the client needs the Campus plugin enabled and a new session or plugin reload. If authentication is required, use the client's native Campus connection flow and let the user complete browser sign-in or consent. Keep tokens in native storage; never request or print tokens, cookies, passwords or callback URLs. Do not change existing connections or weaken approval settings.

Presentation reading needs `presentations:read`. Editing needs `presentations:read`, `presentations:draft` and `presentations:save`, since edits save immediately. Uploads need read and draft. `offline_access` permits refresh. An older grant may need new consent; do not assume it acquired new permissions. These workflows need no admin permissions and never use people, points, invitations or lighting tools.

Read and coach first. Editing is authorized when the user asks to create or update their Campus presentation, including a clear request made with the skill invocation. For advice, review or ambiguous "help me prepare", work in chat until the user requests a Campus edit. Do not add a separate confirmation ritual when the request already authorizes editing. Before the first edit, state that Campus saves each change immediately and members with cohort access share it with their cohort; other members' decks remain owner-only. Preserve the client's native approval requirements.

## Select and read

1. Call `campus_presentations_context` with `{}`. Use its account identity, scopes and `sessions`. The MCP cannot read the user's device, calendar or other apps; use those only if separately available and authorized.
2. Select `kind: "monday"` or `"friday"` and the exact `sessionDate` returned by Campus. If there is more than one plausible week, ask which; do not assume the computer's date identifies the intended cohort session. A Friday's matching Monday has the same returned `week` value.
3. Call `campus_presentation_read` with `{kind, sessionDate}`. Use `draft`, then `saved`, then `starter` as the current deck. Empty weeks return a starter without creating a record. Preserve `openUrl` for the user.
4. Read actual questions, hints, answers and slide IDs. Templates are editable by admins; saved decks retain their own questions. Never regenerate an existing deck from the latest template or assume a fixed question count.

## Edit safely

Use `campus_presentation_edit` with:

- `kind` and `sessionDate` from the selected session;
- `expectedDraftRevision` and `expectedSavedRevision` from the latest read;
- a fresh UUID `requestId` for a new operation;
- `templateRevision: starter.templateRevision` on the first edit of a new presentation;
- `answers` containing the existing starter question's `slideId` and only the fields being changed, typically `{slideId, text, fontSize: "auto"}`.

From a read response, use `draft?.revision ?? 0` for `expectedDraftRevision` and `saved?.revision ?? 0` for `expectedSavedRevision`. Do not derive revisions from dates or increment them yourself. An edit returns `workspace`, `draftRevision` and `savedRevision`; use those returned revisions for the next operation, or read again whenever the current state is uncertain. Keep starter questions. Use `extraSlides` only as needed, with `action` add/update/move/remove and IDs from read. Read back generated IDs before addressing a newly added slide. Preserve unrelated text and media.

Omitted fields stay unchanged. `images` and `videos` replace their entire respective lists; retain existing entries when adding one. At most six media items fit on a slide. Videos are supported YouTube links. Do not add an arbitrary link as a video.

Edits atomically update working and saved copies. There is no private staging or later publication gate. `campus_presentation_save` usually just confirms an already saved revision; it is not a way to keep earlier edits private. Never call a saved presentation private unless the account's actual visibility supports that claim.

On `CONFLICT`, read the deck again and compare the intervening changes. Reapply compatible intent with the new revisions; ask when the same content conflicts. Never overwrite silently. Retry the exact same operation with its original request ID only when its outcome is uncertain; do not change payload under that ID or blindly repeat uploads. Authorization failures need native reauthorization, not repeated edits or broader permissions.

## Evidence and media

Use only evidence the user supplied or authorized you to read. A local file is accessible only if this client can actually read its bytes. Do not claim that a chat attachment or link was inspected when it was not.

For an accessible JPG, PNG or WebP within the tool's 5 MB limit, reserve via `campus_presentation_upload` using `{kind, sessionDate, name, contentType, size}`. PUT the exact bytes to its returned URL with the returned headers within five minutes, call `campus_presentation_finish_upload` with its `assetId`, then attach `{assetId, alt}` via edit. Never log or persist signed URLs. If upload is unavailable, give the exact `openUrl` and ask the user to use Add image in Campus.

## Preview and report

Call `campus_presentation_preview` for the selected session after edits. Inspect its layout report and rendered images. Use `fontSize: "auto"`, shorter wording, sensible media placement or an appropriate extra slide to resolve clipping, unreadable text and overlaps; do not silently delete content to make it fit. Preserve intended meaning. Preview again after a layout correction.

Layout warnings do not prevent saving. If rendering fails or this client cannot display the returned images, say what was checked and what remains unverified. Do not claim a visually verified deck from a successful edit alone. Finish with a concise description of changes, the session date and `openUrl`; say explicitly if no Campus changes were made.

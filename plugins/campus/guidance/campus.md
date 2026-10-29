# Campus operations

Use the installed Campus MCP connection. Base tool names below may have a client prefix. Use native sign-in when needed; never request credentials, change connections or broaden permissions. If access fails, explain it once and continue useful preparation in chat.

## Read

1. Call `campus_presentations_context`. Honor the requested date/week; otherwise use the relevant `defaultSessions` entry when available and consistent with the request. Match a returned session. Resolve relative dates using `today`/`timeZone`; ask only if selection remains ambiguous. Defaults may be past after the schedule ends.
2. Call `campus_presentation_read` with `{kind, sessionDate}`. Use `draft ?? saved ?? starter`, including its actual prompts, answers and `slides[].id`. Reading an empty session creates no record. Preserve `openUrl`.
3. For Friday, read Monday with the same returned `week` when available. Do not modify it. A missing goal does not block Friday.

## Edit

A bare invocation or "help me prepare" means prepare in chat. A request to create, fill in or update the Campus presentation authorizes those changes, including an earlier request; do not ask again. Preserve native approvals. Use only media authorized for inclusion.

Before the first edit, briefly explain that edits save immediately, with cohort or owner-only access according to `visibility`. If omitted by an older server, state the conditional rule: members with cohort access share edits; others keep them owner-only. There is no private staging step.

Resolve material content choices in chat. Apply related changes together where practical; preserve unrelated content, media and starter questions. Do not save speculative alternatives or invented placeholders.

Call `campus_presentation_edit` with:

- The selected `kind` and `sessionDate`.
- `expectedDraftRevision: draft?.revision ?? 0` and `expectedSavedRevision: saved?.revision ?? 0` from the latest read.
- A new UUID `requestId` per operation.
- `templateRevision: starter.templateRevision` for a new deck.
- `answers` with the slide's `id` as `slideId` and changed fields only, e.g. `{slideId, text, fontSize: "auto"}`.

Use current returned `workspace` revisions or reread before subsequent edits. On replay, top-level revisions describe the earlier operation. Address `extraSlides` by returned IDs; obtain a new slide's ID before editing it.

Omitted fields stay unchanged. `images`/`videos` replace their respective lists; retain existing entries when adding. Six media items fit per slide. `mediaLayout` resets manual placement. Read [media handling](media.md) when adding images or videos. Do not put spoken notes or demo instructions into slide answers.

Edits already save; `campus_presentation_save` is normally unnecessary. On `CONFLICT`, reread and reconcile; ask about competing changes to the same content. Retry an uncertain operation only with its identical payload and request ID within 24 hours; after that, read and reconcile first. Never blindly repeat uploads. Authorization failures require native reauthorization, not broader permissions. Reading requires read scope; edits require read/draft/save; uploads require read/draft. No admin tools are needed.

## Preview

Preview the whole deck with `campus_presentation_preview`; inspect images and the layout report. Check returned revisions, when supplied, against the edited deck. Reread if another edit intervened. Recheck affected slides after local corrections, or the whole deck after structural changes.

Apply the quality standard to the screen: understandable at a glance, every word earning its place. Preserve meaning and legibility when fixing overflow; never silently delete substance or shrink text to hide a problem. Warnings do not prevent saving.

Finish with the date, saved status, editor link and material unresolved issues. If rendering or visual inspection failed, say so. Never claim chat content was saved or an uninspected deck was visually checked.

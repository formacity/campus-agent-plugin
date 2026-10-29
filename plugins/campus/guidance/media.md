# Media

Use real, authorized evidence. If the client cannot read image bytes or upload them, give the presentation's `openUrl` for adding the image in Campus and continue preparing the content.

For JPG, PNG or WebP within 5 MB:

1. `campus_presentation_upload`: pass `{kind, sessionDate, name, contentType, size}` with actual MIME type and byte count.
2. PUT the bytes to the returned URL with its headers within five minutes. Never log or persist signed URLs.
3. `campus_presentation_finish_upload`: pass `assetId`.
4. Attach `{assetId, alt}` through edit, preserving existing images. Uploading alone does not attach or save the image in the deck.

`videos` accepts YouTube URLs, not arbitrary URLs or local files. Do not publish a video to make it embeddable. Show other demos separately; use a fallback when helpful. A demo proves only what it shows.

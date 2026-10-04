# Downstream rich-draft fork

This repository is a GitHub fork of `ni-c/imap-mcp`.

## Purpose

The branch `lesekita-v0.5.1` is the pinned source used by the dedicated
LeseKita mailbox runtime. It starts from upstream release `v0.5.1`, commit
`a959cd351c84f5e73b573feb1e5af19b3c0a0141`.

The downstream change is deliberately technical and generic:

- keep all upstream IMAP read/search/filing behavior;
- keep the original plain-text `save_draft`;
- add `save_rich_draft` for complete caller-supplied HTML drafts;
- accept an explicit plain-text fallback;
- accept bounded inline PNG/JPEG/GIF CID images;
- require every supplied inline image to be referenced by HTML and every CID reference to resolve;
- reject remote HTTP(S) image sources;
- preserve native reply threading through `In-Reply-To` and `References`;
- retain the upstream security property that the MCP server **cannot send mail**.

## Deliberate non-responsibility

The MCP does **not** own brand identity, signature text, logo selection, signature
CID naming, or business approval.

For LeseKita those belong to `www-operations`, specifically the dedicated
LeseKita outbound-mail skill and its canonical signature asset/profile.

That mirrors the WWW architecture: the business layer composes the approved HTML
and chooses the canonical image; the mail transport only persists the exact draft.

## Safety bounds

`save_rich_draft`:

- uses the configured IMAP user as sender; no caller-selected From;
- allows at most 5 inline images;
- allows only PNG, JPEG and GIF inline parts;
- caps one inline image at 1 MiB and all inline images at 2 MiB total;
- requires safe filenames and CID tokens;
- rejects unresolved or unused CIDs;
- rejects remote HTTP(S) image sources;
- validates canonical base64 before APPEND;
- has no send, SMTP, reply-send or forward-send capability.

## Operational review gate

The technical server does not replace business approval. The WWW consumer process
owns the LeseKita sequence:

`Dennis exact-text approval -> Pia Slack approval -> native rich draft -> separate human send`

## Deployment authority

The Homelab rebuild/deployment record is maintained in
`Margok1987/dennis-heimserver-repo` under
`services/imap-mcp-save-the-children/`.

The deployed Docker image must pin an exact commit from this fork. Never build a
productive image from a floating branch head.

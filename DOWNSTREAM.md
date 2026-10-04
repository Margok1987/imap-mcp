# LeseKita downstream fork

This repository is a GitHub fork of `ni-c/imap-mcp`.

## Purpose

The branch `lesekita-v0.5.1` is the pinned source for the dedicated
`pia.loeber-wille@lesekitas.de` mailbox used by the Wörter.Wunder.Welten /
Save the Children LeseKitas workflow.

It starts from upstream release `v0.5.1`, commit
`a959cd351c84f5e73b573feb1e5af19b3c0a0141`.

The downstream change is deliberately narrow:

- keep all upstream IMAP read/search/filing behavior;
- keep the original plain-text `save_draft`;
- add `save_lesekita_draft` for this one mailbox identity;
- append the complete fixed LeseKita signature server-side;
- generate multipart text/plain + text/html MIME;
- embed exactly one canonical signature image with CID
  `lesekita-pia-signature-logo-v1`;
- pin and verify the signature image by SHA-256
  `1dd7a6d74947d2615cf868bc19cc020fa9f022b94cf6e44630fb03cc9407adb3`;
- preserve native reply threading through `In-Reply-To` and `References`;
- retain the upstream security property that the MCP server **cannot send mail**.

The business body is supplied as plain text. The caller does not supply signature HTML,
a CID, a logo path, or a sender identity. Those are fixed by the downstream operation.

## Identity boundary

`save_lesekita_draft` refuses execution unless the configured IMAP user is exactly:

`pia.loeber-wille@lesekitas.de`

This is intentional. The tool is not a general HTML-mail composer.

## Canonical asset

`assets/lesekita-pia-signature-v1.jpg`

Source: extracted from an existing sent message in Pia's real LeseKita mailbox.

Verified properties:

- MIME: `image/jpeg`
- dimensions: 1571 x 830
- bytes: 240123
- SHA-256: `1dd7a6d74947d2615cf868bc19cc020fa9f022b94cf6e44630fb03cc9407adb3`

Runtime code re-hashes the file before composing a LeseKita draft and fails closed on
mismatch.

## Operational review gate

This repository enforces technical draft semantics only. The WWW consumer process owns
business approval:

`Dennis exact-text approval -> Pia Slack approval -> native LeseKita draft -> separate human send`

The MCP itself exposes no send operation.

## Deployment authority

The Homelab rebuild/deployment record is maintained in
`Margok1987/dennis-heimserver-repo` under
`services/imap-mcp-save-the-children/`.

The deployed Docker image must pin an exact commit from this fork. Never build a
productive image from a floating branch head.

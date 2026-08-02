# CLAUDE.md - blueprints

Exported content, not authored content - there is nothing to write here by hand.

## Conventions

- Layout: `<project-slug>/<timestamp>-<packet-id>/` - see `README.md`.
- A Blueprint is immutable once exported. Never edit one in place; a changed source produces a new, separately-timestamped Blueprint alongside the old one, never a patch to it.
- The format spec lives in [`flynns-arcade`](https://github.com/kenmclennan/flynns-arcade) (`design-packet-spec.md` as of this writing - due a rename to match "Blueprint"). Read it before writing an export tool against this repo, not this file - this file is conventions for the repo, not the format.

## What does not belong here

Anything hand-drafted. A Blueprint is either exported by tooling or it doesn't belong in this repo yet.

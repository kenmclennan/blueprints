# CLAUDE.md - blueprints

Exported content, not authored content - there is nothing to write here by hand.

## Conventions

- Layout: `<project-slug>/<timestamp>-<blueprint-id>/` - see `README.md`.
- **The design content is immutable once exported.** Never edit `user-stories/`, `wireframes/`, `design-system/`, `architecture/`, `domain-model/`, `api-contracts/`, `quality-attributes/`, `user-flows/`, `glossary/` or `principles/` in place; a changed source produces a new, separately-timestamped Blueprint alongside the old one, never a patch to it.
- **`plan/` is a living document inside that frozen design.** Revise it in place - a work item re-scoped, a cut changed, a gap found. A wrong plan is never a reason to re-export a design that has not changed.
- The format spec lives in [`flynns-arcade`](https://github.com/kenmclennan/flynns-arcade) as `blueprint-spec.md`. Read it before writing an export tool against this repo, not this file - this file is conventions for the repo, not the format.

## What does not belong here

Anything hand-drafted. A Blueprint is either exported by tooling or it doesn't belong in this repo yet.

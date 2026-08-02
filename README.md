# Blueprints

Exported Blueprints - self-contained, browsable packages that hold a design and the delivery plan for building it, together, indivisibly. One Blueprint is the complete handoff artifact from "what should we build and why" to "here is the sequenced, independently-deliverable work that builds it."

> "I wrote you, kiddo." - Kevin Flynn

## What a Blueprint is

A Blueprint supersedes what earlier design docs in this ecosystem called a "Design Packet" - it's a Design Packet (the nine-category design content: user stories, wireframes, design system, architecture, user flows, api contracts, quality attributes, domain model, glossary) plus the delivery plan decomposed from it, unified into one immutable snapshot. Every work item traces back to the story that motivated it via ordinary links inside the same bundle - no dead references, nothing to duplicate, because everything the plan cites is already sitting right there.

A Blueprint's job ends at "here are the work items, sequenced, ready to be picked up." What happens after that - specs, code, review - belongs to whatever build pipeline picks the work up, not to this repo.

**The format itself is still being finalised.** The authoritative spec currently lives as `design-packet-spec.md` in [`flynns-arcade`](https://github.com/kenmclennan/flynns-arcade) - the design half of what a Blueprint contains - and hasn't yet been updated for the unified (design + plan) shape this repo is named for. Treat this repo as empty and provisional until that catches up.

## Layout

```
<project-slug>/
  <timestamp>-<packet-id>/
```

One folder per project (matching how specs already work in this ecosystem), timestamp-first inside it so a project's Blueprints sort chronologically - migration-style. See the spec (once it reflects the Blueprint rename) for the full structure inside each one.

## What does not belong here

Hand-authored content. Everything in here is either exported by tooling or reviewed and approved after being exported - this repo is not a place to draft a design or a plan by hand.

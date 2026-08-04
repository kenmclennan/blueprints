---
type: "domain-model"
title: "Domain Model"
description: "Why this design needs its own todo/needs-attention/active/queued vocabulary, and exactly how it maps back to lightcycle's canonical state and lane terms."
tags: []
---

## Domain Model - Operator Monitors the Pipeline

This design mostly _observes_ lightcycle's existing domain rather than inventing one - node, theme, item, step, artifact, role, state, and lane are all already canonically defined in lightcycle's own `docs/ontology.md` (linked as this slice's Glossary) and `docs/state-lifecycle.md` (linked alongside it). This artifact isn't a restatement of either. It exists for the one thing this design genuinely adds: its own derived display vocabulary, and that vocabulary's precise relationship back to the engine's real terms.

## Why a translation is needed at all

The engine's own model is `state` (`backlogged -> ready -> in_progress -> done`) plus `lane`, a derived view over `(state, role)`, stated exactly in `docs/state-lifecycle.md`'s own `lane_for` table:

| state       | role  | lane    |
| ----------- | ----- | ------- |
| ready       | human | inbox   |
| ready       | agent | queue   |
| in_progress | any   | active  |
| backlogged  | any   | blocked |
| done        | any   | done    |

Nothing in this design's stories or wireframes uses those lane names directly - they use `todo`, `needs-attention`, `active`, and `queued` instead. Read in isolation, that looks like unexplained renaming (exactly the kind of drift `ontology.md` warns against). Read against the table above, it's not renaming - `needs-attention` is a genuinely new concept this design introduces: a single display bucket that merges two distinct engine lanes (`inbox` and `blocked`). That distinction is worth being explicit about, since a builder implementing this from the wireframes alone has no way to recover it.

## The mapping

| TUI term            | Engine term(s) it derives from        | Relationship                                                                                                                                                                                                                                                                                                                                                                                                                                                                                      |
| ------------------- | ------------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| **todo** (Backlog)  | `state = backlogged`                  | Direct rename, no merge - `todo` is presentation-friendlier than `backlogged`, same set.                                                                                                                                                                                                                                                                                                                                                                                                          |
| **queued**          | `lane = queue`                        | Direct rename, no merge.                                                                                                                                                                                                                                                                                                                                                                                                                                                                          |
| **active**          | `lane = active`                       | Direct rename, no merge.                                                                                                                                                                                                                                                                                                                                                                                                                                                                          |
| **needs-attention** | `lane = inbox` **∪** `lane = blocked` | Not a rename - a new derived concept. The engine treats these as two distinct lanes (a human gate vs. a blocked step, per the table above); this design deliberately merges them into one visual bucket at the top of the list, then re-splits them inside that bucket via the dependency indicator (`●` vs `●⛓`) and the escalation reason shown in the node hub. The merge is the UX decision ("nothing needing me gets buried"); the re-split is what keeps the merge from losing information. |

## What this design adds to the engine's own domain

`tui-architecture.md`'s Change 1 - `Artifact` gaining `internal` (boolean) and `kind` (string) - is also, strictly, a domain model change: two new fields on an existing entity. It's not restated here; the architecture doc's own treatment is complete (write points, resolution order, why neither field can be inferred from the value's shape) and a second copy here would only drift from it. This artifact is the one to update if a future change alters the todo/needs-attention/active/queued mapping above; Change 1's own field-level detail stays owned by the architecture doc.

---

← Back to [Domain Model](index.md)

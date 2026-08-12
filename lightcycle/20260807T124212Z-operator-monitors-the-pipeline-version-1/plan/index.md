---
type: "plan"
title: "Delivery Plan"
description: "55 work items across 6 milestones, decomposed from this Blueprint's 51 user stories plus 10 engine-foundation items the design mandates (one added during this plan's own required adversarial review). First real use of blueprint-spec.md's proposed plan/ convention."
---

## Delivery Plan - Operator monitors the pipeline - Version 1

Decomposed per `decomposing-designs` (Principles 1-7). Every work item traces to one or more
[User Stories](../user-stories/index.md) and/or a named engine gap this design's own
[Architecture](../architecture/tui-architecture.md) and [API Contracts](../api-contracts/api-contracts.md)
artifacts declare as part of this design's delivery, not filed separately.

**Frontmatter convention for `plan/WI-###.md`** (this Blueprint's own first use of the proposed plan
mechanism, so the shape is fixed here rather than left open): `type: work-item`, `title`, `description`
(scope statement, same discipline as a category concept file), `milestone`, `delivers` (list of
`user-stories/<id>.md` paths this item's Acceptance Criteria are sourced from - omitted for a pure
engine-foundation item), `depends_on` (list of `WI-###` ids).

**Self-sufficiency choice**: each `WI-###.md` carries its Acceptance Criteria copied verbatim and its
Source References inlined with concrete excerpts, per SKILL.md Principle 7 and the Output Format - not
just a link into `user-stories/`. This Blueprint's own "reference, don't duplicate" proposal is not used
here: these files are meant to be lifted directly into a lightcycle item's `brief` artifact at pickup
time, at which point they leave the Blueprint and lose access back to it. A link-only work item would be
dead the moment it's filed.

**Verification**: this plan went through all 7 passes in `references/verification.md`, including a fresh-
eyes Pass 7 adversarial review - see [verification.md](verification.md) for the full report and every
fix it produced (including WI-055, added post-hoc to close a real gap the review found in Milestone 2).

## Filing a work item with lightcycle (BDD flow)

Each `WI-###.md` is written to be lifted directly into lightcycle's own pipeline, in dependency order
(never ahead of what its own `depends_on` list names as merged):

1. **File the item**, under its milestone's parent theme if one exists (a milestone is a natural theme
   boundary, though nothing in this plan mandates creating one): `lc new item --title "<WI title>"`.
2. **Attach the work item's own file as its brief**: `lc attach <item-id> brief plan/WI-###.md
   --replace`. The whole file - Brief, Acceptance Criteria, Source References with their excerpts,
   Constraints, Open Questions, Deliverable - becomes the brief the `spec-writer` step reads; nothing here
   depends on the builder having live access back to this Blueprint (Principle 7), so this is safe once
   the item leaves this repo.
3. **Activate it** under the BDD workflow: the theme carries `--workflow lightcycle/bdd-driven`, items
   inherit it, so activation is just `lc set <item-id> --state active`. The flow is three gated phases:
   `spec-writer` turns the Brief + Acceptance Criteria into a formal spec on a spec PR (gate 1);
   `feature-writer` freezes the spec into gherkin scenarios on a feature PR (gate 2); `implement-features`
   writes code that makes every frozen scenario pass, through CI and review to the code PR (gate 3).
   Every work item's Acceptance Criteria are already Given/When/Then, matching this repo's own
   `pytest-bdd` convention (`tests/feature/*.feature`) - `feature-writer` has scenario-ready criteria to
   freeze directly, not prose to reinterpret into scenarios.
4. **Never skip an item's `depends_on`** - a dependency listed as `WI-###` must already be merged (its
   spec-phase and code-phase both through `await-merge`) before the dependent item is activated. This
   plan's own dependency graph (Pass 3/Pass 7, see [verification.md](verification.md)) is what makes that
   safe to follow mechanically rather than re-deriving build order by hand.

## Milestones

### Milestone 0 - Engine Foundation

**Goal:** Land every engine-side read/write-model gap the design's Architecture and API Contracts
artifacts identify as required substrate, before any TUI code depends on it. No operator-visible
behavior yet - each item is a legitimate technical work item per `decomposition.md`'s "Technical work
items" section, justified by an explicit design mandate (tui-architecture.md's Change 1-4, or a named
API Contracts gap), not by "the next item needs it."

| ID | Title | Depends on |
| --- | --- | --- |
| [WI-001](WI-001.md) | `Artifact` gains `internal` and `kind` fields | - |
| [WI-002](WI-002.md) | Read-only pool-running-state use case | - |
| [WI-003](WI-003.md) | Read-only breaker-state use case | - |
| [WI-004](WI-004.md) | Trace/hierarchy read path carries step role | - |
| [WI-005](WI-005.md) | Elapsed-time-since-claim computation | - |
| [WI-006](WI-006.md) | Blocking-dependency-id resolution on the live read path | - |
| [WI-007](WI-007.md) | Durable done-step log resolution | - |
| [WI-008](WI-008.md) | Backlog per-project counts (all projects at once) | - |
| [WI-009](WI-009.md) | Dependency-rule + `adapters/` doc reword; Textual added as a runtime dependency | - |
| [WI-055](WI-055.md) | Read-only planned-step projection from the pinned workflow graph | - |

**Exit criteria:** every WI-001..009 and WI-055 merged; `pytest tests/unit tests/integration` green;
`CLAUDE.md`'s zero-runtime-deps and `adapters/` wording match what Milestone 1 is about to build against.

WI-055 is numbered out of sequence deliberately - it was added during this plan's own Pass 7 adversarial
review, after WI-001..054 were already numbered, to avoid renumbering 25 already-reviewed files. It
belongs in Milestone 0 despite the number; see its own file for why.

### Milestone 1 - Priority List (landing screen)

**Goal:** The dashboard launches, connects, and the landing screen (needs-attention / active / queued,
footer, navigation, quit) is fully real and usable on its own - the TUI is releasable as a minimal but
complete situational-awareness surface even if nothing past this milestone ever shipped.

| ID | Title | Depends on |
| --- | --- | --- |
| [WI-010](WI-010.md) | TUI app scaffold: launch, connect, render, footer frame | WI-009 |
| [WI-011](WI-011.md) | Needs-attention items pinned to top | WI-010 |
| [WI-012](WI-012.md) | Active items with elapsed time | WI-010, WI-005 |
| [WI-013](WI-013.md) | Queued items at the bottom | WI-010 |
| [WI-014](WI-014.md) | Scroll the list with arrow keys | WI-011, WI-012, WI-013 |
| [WI-015](WI-015.md) | Keep my selection on an item as it moves | WI-014 |
| [WI-016](WI-016.md) | See a quiet message when there's no work at all | WI-011, WI-012, WI-013 |
| [WI-017](WI-017.md) | See a dependency indicator on blocked items | WI-011, WI-006 |
| [WI-018](WI-018.md) | See which project an item belongs to | WI-010 |
| [WI-019](WI-019.md) | Hear an alert when something needs me | WI-011 |
| [WI-020](WI-020.md) | Footer: pool running indicator | WI-010, WI-002 |
| [WI-021](WI-021.md) | Footer: engine version | WI-010 |
| [WI-022](WI-022.md) | Footer: upgrade available indicator | WI-010 |
| [WI-023](WI-023.md) | Footer: claude availability indicator + reclaim display | WI-010, WI-003 |
| [WI-024](WI-024.md) | Footer: contextual shortcut bar | WI-016 |
| [WI-025](WI-025.md) | Quit the dashboard | WI-010 |

**Exit criteria:** all Milestone 1 items merged; `lc tui` (or equivalent entry point) launches against a
real store and renders a correct, live-updating, fully scrollable and browsable priority list end to end
- correct groups, footer, bell, quit. **Not yet in scope: opening an item into its own detail view** (that
is WI-026, Milestone 2) - the footer's own contextual shortcut bar (WI-024) correctly reflects this by
omitting "enter/open" from its shortcut list until Milestone 2 lands, since that mechanism is inherently
state-driven, not a defect to route around. Pilot-driven tests cover every state (normal, empty,
claude-unavailable).

### Milestone 2 - Node Hub & Hierarchy

**Goal:** Any node (theme, item, step) opens into the tabbed hub; the Hierarchy tab is fully explorable.
Log and Artifacts tabs land as empty/placeholder targets of the tab cycle until Milestones 3-4 fill them.
**The Hierarchy tab renders both real (filed) steps and, via WI-055, an item's remaining planned
(not-yet-filed) steps** - every wireframe state for this tab shows a real item's full step sequence, which
the live engine only ever files one step at a time; WI-055 closes that gap. A planned row is selectable
but opens nothing (WI-036/WI-037's own no-op handling).

| ID | Title | Depends on |
| --- | --- | --- |
| [WI-026](WI-026.md) | Open the item detail view + return to the list without losing position | WI-014, WI-018 |
| [WI-027](WI-027.md) | Hub header: current step, role, elapsed | WI-026, WI-004, WI-005 |
| [WI-028](WI-028.md) | Land on the tab that matches the item's status | WI-026 |
| [WI-029](WI-029.md) | Cycle tabs with `[` and `]` | WI-028 |
| [WI-030](WI-030.md) | Hierarchy: root-down rendering + depth indentation | WI-028, WI-055 |
| [WI-031](WI-031.md) | Hierarchy: node id/state/role/dependency indicator | WI-030, WI-004, WI-006, WI-055 |
| [WI-032](WI-032.md) | Hierarchy: content indicator per node | WI-030, WI-001 |
| [WI-033](WI-033.md) | Hierarchy: scroll with the keyboard | WI-030 |
| [WI-034](WI-034.md) | Hierarchy: keep ancestry pinned while scrolling | WI-033 |
| [WI-035](WI-035.md) | Hierarchy: current node highlighted at actual depth | WI-030 |
| [WI-036](WI-036.md) | Open a node from the hierarchy + return without losing position | WI-030, WI-004, WI-027, WI-055 |
| [WI-037](WI-037.md) | Hierarchy: `a`/`l` shortcuts jump to Artifacts/Log | WI-030, WI-029, WI-055 |
| [WI-038](WI-038.md) | See why an item is escalated + jump to the blocker and back | WI-027, WI-006 |

**Exit criteria:** every node type opens into a correct hub; Hierarchy tab fully matches
`wireframe-hub.html`'s hierarchy/hierarchy-scrolled states; `[`/`]` and Tab coexist without collision.

### Milestone 3 - Artifacts & Artifact Viewer

**Goal:** The Artifacts tab and its four artifact-kind viewers are real; every artifact the engine can
produce is openable, with a text fallback for any kind the TUI doesn't recognise.

| ID | Title | Depends on |
| --- | --- | --- |
| [WI-039](WI-039.md) | Artifacts tab: list attached artifacts, always-present, empty state | WI-029, WI-001 |
| [WI-040](WI-040.md) | Select an artifact to open it + return without losing position | WI-039 |
| [WI-041](WI-041.md) | View a text artifact full-screen + unknown-kind fallback | WI-040, WI-001 |
| [WI-042](WI-042.md) | Follow a URL artifact | WI-040, WI-001 |
| [WI-043](WI-043.md) | Open a file-path artifact in its application | WI-040, WI-001 |
| [WI-044](WI-044.md) | View a list artifact as a scrollable list | WI-040, WI-001 |

**Exit criteria:** every `kind` value (`text`, `url`, `filepath`, `list`, and unrecognised) has a working,
tested viewer path; the Artifacts tab is present and correct on every node type from Milestone 2.

### Milestone 4 - Live Log

**Goal:** The Log tab streams a running step's output live and reads a finished step's output
statically, matching the contextual landing logic Milestone 2 already built.

| ID | Title | Depends on |
| --- | --- | --- |
| [WI-045](WI-045.md) | Live log tail as written + auto-scroll + return without losing position | WI-028 |
| [WI-046](WI-046.md) | No live log message (human step / not started) | WI-045 |
| [WI-047](WI-047.md) | Scroll back through the live buffer without stopping the tail | WI-045 |
| [WI-048](WI-048.md) | Step finishes while watching (finished indicator retained) | WI-045 |
| [WI-049](WI-049.md) | Done step's historical log | WI-045, WI-007 |

**Exit criteria:** every Log-tab state in `wireframe-hub.html` (active-log, log-finished, done-item's
static log, no-log) is real and tested against a fake worker/log fixture.

### Milestone 5 - Backlog

**Goal:** The second top-level screen - todo items not yet activated, filterable by project - is real
and reachable from anywhere via Tab.

| ID | Title | Depends on |
| --- | --- | --- |
| [WI-050](WI-050.md) | Backlog screen: todo list + project column + Ctrl-U/D scroll | WI-010, WI-018 |
| [WI-051](WI-051.md) | Switch to the backlog with a tab or shortcut | WI-050, WI-025 |
| [WI-052](WI-052.md) | Explore a backlog item's context in the hierarchy + return without losing position | WI-050, WI-030 |
| [WI-053](WI-053.md) | Filter the backlog by project via a picker | WI-050, WI-008 |
| [WI-054](WI-054.md) | Quiet message when the backlog is empty (overall vs. filtered) | WI-050, WI-008 |

**Exit criteria:** Backlog is reachable and fully navigable from every screen in Milestones 1-4;
`decomposing-designs`' own coverage check (every one of this Blueprint's 51 stories traces to a WI here)
passes - see the traceability table below.

## Traceability - every story to its work item

| Story | Work item |
| --- | --- |
| [See the dashboard connect and render](../user-stories/61c7bcfe-647f-412b-9ea2-cdde9a0dec6a.md) | WI-010 |
| [See whether the pool is running](../user-stories/34603aed-522d-4835-9836-00b99abf31ea.md) | WI-020 |
| [See which engine version is running](../user-stories/131f76a9-7b9b-4d70-889c-5c0e9c9a0d3d.md) | WI-021 |
| [See when a newer version is available](../user-stories/2a051776-fce5-47ac-bef5-ceddf14045d7.md) | WI-022 |
| [Quit the dashboard](../user-stories/204b730c-94db-43f2-b0e0-4c685e37a038.md) | WI-025 |
| [See a contextual shortcut bar in the footer](../user-stories/75374a1f-589b-421e-b5be-2191407b5d9a.md) | WI-024 |
| [See whether Claude is available to process work](../user-stories/02c20dfb-88af-4b91-887b-6c9f1fa660aa.md) | WI-023 |
| [See needs-attention items pinned to the top](../user-stories/7da675a5-6a5a-4ff9-a824-a92b519e17fe.md) | WI-011 |
| [See active items with elapsed time](../user-stories/5bf2d324-a842-4390-aa68-32928145a70b.md) | WI-012 |
| [See queued items at the bottom](../user-stories/019d51d5-4952-4e0e-bb5d-748173e5e069.md) | WI-013 |
| [Scroll the list with arrow keys](../user-stories/3c7be8c5-633b-4049-b925-1311fb82abc9.md) | WI-014 |
| [Hear an alert when something needs me](../user-stories/5d060c74-c1a8-40d5-934a-0b1a4f382cb3.md) | WI-019 |
| [See a quiet message when there's no work at all](../user-stories/bdbc1948-c482-400b-843d-9ba46b792ed1.md) | WI-016 |
| [Keep my selection on an item as it moves](../user-stories/f4c18d86-2dbd-40ae-9d41-5fc98cea4347.md) | WI-015 |
| [See a dependency indicator on blocked items](../user-stories/fecce582-e41a-4490-9158-48c2eab2c7db.md) | WI-017 |
| [See which project an item belongs to](../user-stories/1ccf2602-259e-4889-844e-fe766119f43b.md) | WI-018 |
| [Open the item detail view](../user-stories/18fdcc37-373a-4b2c-a045-18a1c20e0fc3.md) | WI-026 |
| [See the item's current step, role, and elapsed time](../user-stories/45d7d629-f736-4875-8470-f928558a88ea.md) | WI-027 |
| [Return to the list without losing position](../user-stories/265befe7-7a49-4219-8f4c-8aabee2f201d.md) | WI-026 |
| [See why an item is escalated](../user-stories/4bee4625-16c6-4f37-a7af-01893eba78ea.md) | WI-038 |
| [Land on the tab that matches the item's status](../user-stories/74dd1219-8a28-4c16-a293-83a82b4db930.md) | WI-028 |
| [Cycle tabs with \[ and \]](../user-stories/3480e340-d1f3-44ac-a0d8-e6ef269f6d13.md) | WI-029 |
| [See which nodes have content](../user-stories/9c53daa8-4b1a-425e-a3be-4f4a2ba53849.md) | WI-032 |
| [See the hierarchy from its root node down](../user-stories/f448a52c-0755-4ded-bea1-7992a79555e4.md) | WI-030 |
| [See each node's id, status, and role in the hierarchy](../user-stories/833b6039-ab42-4b6a-b1d7-e757b9dc9233.md) | WI-031 |
| [Scroll the hierarchy with the keyboard](../user-stories/92e81697-0e0b-4290-ac26-ba46ca3f680d.md) | WI-033 |
| [Open a node from the hierarchy](../user-stories/ccd1d6ac-5812-43f6-a9bc-4cdfb667fa57.md) | WI-036 |
| [Return to the hierarchy tab without losing position](../user-stories/ded8dbd1-c1ef-45c0-97df-562b72a52e91.md) | WI-036 |
| [Keep ancestry visible while I scroll](../user-stories/eaa87e0b-0a4f-4c35-b3ff-d372ec1a286f.md) | WI-034 |
| [Jump straight to a node's artifacts or log from the hierarchy](../user-stories/8f6e008e-f9d8-4c6d-a061-02da7002bd42.md) | WI-037 |
| [See the current node highlighted in the hierarchy](../user-stories/3b552243-e146-426c-b0e1-2c3c0d2b3801.md) | WI-035 |
| [See the artifacts attached to a node](../user-stories/da91328b-2935-4b00-8444-63bd41cf0263.md) | WI-039 |
| [Select an artifact to open it](../user-stories/0bbd3f0a-5167-4316-90be-e9cca4fd0d3e.md) | WI-040 |
| [Return to the node without losing position](../user-stories/40b0de53-450b-4312-bb08-4d57c11a08ed.md) | WI-040 |
| [View a text artifact full-screen](../user-stories/1135fcfe-14a6-41d6-934c-b91e58c5072d.md) | WI-041 |
| [Follow a URL artifact](../user-stories/37bbed50-e813-4968-b9cf-3d16383afda9.md) | WI-042 |
| [Open a file-path artifact in its application](../user-stories/c70a4260-66c0-4e47-836e-b08e2792fefb.md) | WI-043 |
| [View a list artifact as a scrollable list](../user-stories/e01a64c6-91e2-40b0-bae2-0ce1f6b50430.md) | WI-044 |
| [Return to the artifact list without losing position](../user-stories/9f5a722e-3850-4886-802f-4a38914784a7.md) | WI-041, WI-044 |
| [See the live log tail as it's written](../user-stories/28a501f3-8018-418b-b0f0-f068a928eb2c.md) | WI-045 |
| [See that a step has no live log to stream](../user-stories/8ff7f352-1a1b-4f3f-b557-2a5cd164a8ff.md) | WI-046 |
| [Scroll back through the live buffer](../user-stories/36a4fe94-2feb-4b79-a740-6a18fa6893c1.md) | WI-047 |
| [See when the step finishes while I'm watching](../user-stories/a0a38358-9b11-4966-abd8-17edf7fd5987.md) | WI-048 |
| [Return to the node without losing position (log)](../user-stories/2f58b727-2ff9-4f23-b2d5-96e4c1b63d19.md) | WI-045 |
| [See a done step's historical log](../user-stories/3c609f13-cc69-4772-92a3-e2966d4a234e.md) | WI-049 |
| [See todo items not yet activated](../user-stories/a0c35689-d78b-4ee0-9cde-bab92a9648af.md) | WI-050 |
| [Explore a backlog item's context in the hierarchy](../user-stories/7f92f015-2d19-449f-bb47-f389e6dc9691.md) | WI-052 |
| [Return to the backlog without losing position](../user-stories/572ae397-7fb6-453c-832a-80700e1bd483.md) | WI-052 |
| [Switch to the backlog with a tab or shortcut](../user-stories/5fa1445f-dc04-4322-b1be-e00496e8d0d9.md) | WI-051 |
| [Filter the backlog by project](../user-stories/b4b5ce79-8c4b-4fb4-a4c9-1a040e4c6bb7.md) | WI-053 |
| [See a quiet message when the backlog is empty](../user-stories/addfdbfd-a67a-43f5-a343-dd2f45af15ff.md) | WI-054 |

51 of 51 stories traced. 9 engine-foundation items (WI-001..009) trace to Architecture Change 1-4 and
named API Contracts gaps rather than a story - see each item's own Source References.

---

← Back to [Operator monitors the pipeline - Version 1](../index.md)

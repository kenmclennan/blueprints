---
type: "post-mortem"
title: "Delivery Post-Mortem"
description: "What worked and what did not in delivering this Blueprint through an autonomous agent pipeline. Written as the delivery ran, not reconstructed afterwards."
tags: []
---

# Delivery Post-Mortem - Operator Monitors the Pipeline

**Status: draft, written while the delivery is still running.** Passes for WI-009 through WI-012 and the final `audit-design` are still to come, and the `lc retro` digest is not yet folded in. It is being written as things happen rather than reconstructed at the end, because the most instructive material - what a pass cost before a tool existed versus after - stops being recoverable once it is thirty steps back in a trace.

## What this was

One `lc` item, `LC-290.1`, on the `blueprint-delivery` workflow: a loop that reads this Blueprint's `plan/`, picks the next undelivered work item, and sends it through feature-writing, code, and three human gates, then returns to pick the next. The item stays open for the life of the Blueprint. Earlier work items (WI-001 to WI-004) were delivered under a different item before this loop existed; `LC-290.1` delivered WI-005, WI-006 and WI-007, then ran the design audit that filed WI-008 to WI-012.

## What the loop actually cost

Steps per delivered work item, counted from the item's own trace:

| Work item | Steps | `implement-features` rounds | `review-code` rounds |
| --- | --- | --- | --- |
| WI-005 - hub shell and tabs | 42 | 7 | 7 |
| WI-006 - Log tab | 23 | 3 | 3 |
| WI-007 - Artifacts tab and viewer | 32 | 3 | 3 |

The drop between the first row and the other two is the single largest effect observed in this delivery, and it has one cause (below). The reason WI-007 cost more steps than WI-006 despite the same number of review rounds is separate and is also below: escalations that should never have fired.

## What went well

### Making a rendered frame a deliverable, not a disposition

WI-005's seven review rounds were the low point. Reading them back, one round found a behaviour defect and four rejected on test-assertion rigor while explicitly confirming the production code correct - and meanwhile three defects visible in the screen's first frame survived every round. The step files already said "verify against rendered output", twice, in strong words. It made no difference: **a disposition gets satisfied by rendering whatever is nearest the question in hand.** One round rendered a single widget to clear the finding in front of it and never rendered the screen.

Two changes were made together:

- `implement-features` gained a `render` artifact it must produce - one frame per design state the pass touched, whole screen edge to edge, with the comparison against the wireframe written out. A deliverable, not a check.
- A screen-render harness (`tests/support/screen_render.py`, `bash tests/render.sh <state>`) made rendering a whole composited screen cheaper than rendering a widget. Before it, `widget.render_line(0)` was one line and compositing a screen was thirty, and the cheaper thing is what got done.

Rounds went from seven to three on the next work item and stayed there. Within minutes of existing, the harness surfaced two defects that had shipped green (LC-291, LC-292).

### The audit at the end earned its place

`audit-design` found five real gaps by rendering the built screens and colour-extracting frames, not by reading code - including a background colour (`#272727`) that appears nowhere in the token table, and a status line that is simply never called on two screens. It also respected the brief's do-not-refile list. Five work items came out of it that no story would have produced.

### Verifying the reviewer, not just the code

Every agent verdict that reached a human gate was checked rather than taken. Two mattered:

- A `review-code` pass claimed the toast copy "matches verbatim" against the wireframe. It did not - the design splits a caption across two lines and the implementation moved it, which is what caused a wrap the render artifact then explained away as a terminal-width effect the picture could not show.
- A `review-code` rejection diagnosed an intermittent test as a race against a newly added resize path. Run in isolation it was deterministic, six times out of six, and the cause was an off-by-one in page arithmetic. Sending the author after a race would have cost a round, and with the rejection cap already tripped, a wasted round escalates to a human immediately.

## What did not

### A pass is not a thing the system knows about

This is the single recurring cause behind most of what went wrong, and it surfaced independently in at least six places. The workflow loops - one item covers many work items - but almost every piece of state is scoped per item and carries forward for the item's life:

- **The review-rounds cap counts every rejection ever.** It fired four times. Once correctly (four agent rejections in a row, no human in the loop) and three times spuriously, each on a single rejection against a count banked weeks earlier. Two of the three ended a round the human had personally started with a PR comment - so the gate whose purpose is to put a human in an unattended loop fired because a human had already acted. (LC-303)
- **Step titles inherit from the first step in the chain, forever.** A human change request three passes back created a step titled "consolidated test-quality sweep - see PR #349 comment", and every step since inherited that body. Steps building the Log tab read as the stale sweep in the inbox, the queue and the TUI. In a looping workflow the body cannot be right for more than one work item, so after the first pass it is guaranteed wrong. (LC-301)
- **The retro can never fire.** Its cadence counts reflections on _closed_ items, and this workflow's item deliberately never closes. **81 reflections** accumulated across 103 steps - fourteen from `implement-features`, thirteen from `review-code` - and the mechanism built to turn agent feedback into engine improvements counted zero of them, silently, for the entire delivery. (LC-302)
- **Artifacts are per-item but their meaning is per-pass.** The content-pin guard compares a PR's head against a pin labelled by _phase_, so when `open-pr` replaced a stale merged PR with a new one, the guard compared two different PRs and reported five files "dropped" that had simply merged with the old one. It routed the step to a human, and `route_to_human` overwrites the role with `human` with no way back through the CLI. (LC-310)

### The harness could not see the defect class it was built for

Two defects were reported from the running TUI: two scrollbars, and a hierarchy list that stopped nine rows short of its true last step. Both had one cause - two `DataTable`s with no `height` rule, defaulting to `auto`, growing past the viewport so the _screen_ became scrollable and the overflow was clipped where the table's own scrolling could not reach.

The instructive part is that `hub#hierarchy-scrolled` was **a registered render state that had been overflowing all along**. Every `render` artifact this Blueprint produced rendered it, and none could see it: `render_strips()` returns exactly `screen.size.height` strips, so an overflowing screen and a correct one produce byte-identical output. A guard - `screen.virtual_size.height <= screen.size.height` for every registered state - now closes it, and it failed on that state the moment it was written.

The general lesson is sharper than the fix: **a verification tool has its own blind spots, and they are invisible by construction.** The harness was added to stop defects hiding from checks, and immediately grew a category of defect that hid from it.

### Fixtures written to match the design prove nothing

A related self-indictment. The Log tab's demo fixtures were hand-written to match the wireframe, so both sides of the comparison came from the same fiction. A feature that renders raw stream-JSON to the operator passed three gates looking correct. (LC-309)

### Design that is right about the picture and silent about scale

Several gaps share this shape. The wireframes are accurate and were followed faithfully; they simply never showed the case that broke.

- The row grids fix the `id` column at `9ch`/`13ch`. The engine defaults a project's shortcode to the uppercased repo name, so ids are unbounded by construction - and `LC-1234.10` truncated to `LC-1234.1` is not a visibly-cut fragment, it is a complete-looking id belonging to a different node. (WI-008)
- `wireframe-hub.html#hierarchy` marks the _item_ row as current when cycling to the Hierarchy tab. Its example has 8 steps, so "highlight the item" and "show what you were watching" are the same row. At 88 steps they are 86 rows apart. (LC-311)
- The Hierarchy and Artifacts grids were **not in the design system at all** - they existed only in `COLUMN_GRIDS` in code, which is a large part of why their overflow behaviour was never designed.

### Human-facing surfaces read as machinery

The escalation gate arrived in the inbox as `code-review-rounds` in the title column and `code-review-roun/ds` wrapped in the step column, with nothing saying what decision was wanted - for a gate whose completion is destructive (it has no outgoing route, so closing it cascades and deletes every branch the item recorded, including the open PR's head). The operator was expected to infer all of that from a row that named an internal stage identifier twice. (LC-273, LC-277)

### The driver was the weakest link, and agents refusing to build caught it

WI-008 - the first remedial item - took **31 steps**: five `implement-features` rounds, four `review-code` rounds, two escalations, and four rounds lost outright. Every one of the four traces to the driver, not to the pipeline.

- The design was amended **twice after its scenarios had been frozen from it**. The second amendment deleted a sentence ("indented to where it starts in the unstacked grid") that three frozen scenarios still asserted verbatim, and replaced a formula (`2 * row.depth + 2`) that a fourth still hardcoded. A build could satisfy the current design or its own frozen contract, never both.
- A rework instruction was then issued telling the builder to do exactly that, *and* not to touch the tests carrying the old rule.
- When that instruction was withdrawn, it was withdrawn **only in the step's description**, not on the PR. `watch-ci` reads the PR thread, found an unresolved rejection against a green HEAD, emitted `ci-failed`, and the next builder re-derived the same impossible conflict from the same stale comment. A withdrawal has to travel by the channel the original travelled by.

What stopped it each time was an agent **refusing to build something it could prove was impossible**. Three blocks, three proofs, three times right - the last one a bare numeric contradiction (`2 * row.depth + 2` versus `6 + depth`, different at every depth but four) rather than an appeal to judgement. The `[designer]` convention and a step that can park itself as blocked were worth more here than any amount of reviewer diligence: a reviewer can only reject what was built, while a blocked step rejects the instruction.

The resolution was to stop trying to deliver it: the plan was split, the mechanism moved to WI-013 with its own scenarios to be cut from the current design, and WI-008 shipped the sizing that had been correct and mergeable for three rounds. **A settled improvement should not be held hostage to an unsettled one in the same item**, and noticing that took far too long.

### A test that cannot fail, four times

The same defect class appeared four separate times, each passing review because the assertion looked like it was checking the right thing:

1. Log-tab fixtures hand-written to match the wireframe, so both sides of the comparison came from one fiction (LC-309).
2. Four `@then` steps asserting through `table.get_cell(...)`, which returns the stored value regardless of paint width - structurally unable to fail against the silent truncation they existed to catch.
3. LC-310's regression test, which passed against the unfixed code: its fake returned an empty set for the one lookup the bug depends on. The spec specified that test, the brief asked for it, and review confirmed it "present and passing" - three gates checked that it existed and was green, none that it could fail.
4. LC-297's back-arrow fix, verified in the one state where the bug cannot occur ("left now closes the hub even when nothing is focused"), and still broken with the log pane focused.

The remedy that works is cheap and mechanical: **show the test failing against the unfixed code, and report both numbers.** Where that was demanded explicitly, it was done and it worked - one round reverted `atomic_column_width` to a 9-character cap and confirmed all four assertions failed. Where it was not demanded, a green run was accepted as proof four times.

## Numbers so far

|                                             |                            |
| ------------------------------------------- | -------------------------- |
| Steps on the item                           | 103                        |
| Work items delivered by this item           | 3 (WI-005, WI-006, WI-007) |
| Remedial work items filed by `audit-design` | 5 (WI-008 to WI-012)       |
| Agent reflections banked                    | 81                         |
| Reflections the retro counted               | 0                          |
| Engine and design findings filed            | 22 (LC-291 to LC-312)      |
| Spurious human escalations                  | 3 of 4                     |

## Still open at the time of writing

WI-009 to WI-012, a second `audit-design` pass, the `lc retro LC-290.1` digest, and the design decision on where the Hierarchy tab lands when the hub is opened on an item (LC-311).

---
title: "Delivery Plan"
type: "plan"
batch_size: "large"
target_repo: "kenmclennan/lightcycle"
planned_against_commit: "b2aa7b6"
---

# Delivery Plan

The work remaining to build [this design](../index.md), in delivery order. Planned at batch size **large**: items fuse unless an ordering, risk or releasability constraint forces a break.

Planned against `kenmclennan/lightcycle` at `b2aa7b6`. The codebase is the state - this plan holds only work not yet delivered, so a re-plan after any of it lands rewrites the plan rather than ticking anything off. If the target has moved since that commit, re-plan before building.

## Where the batch size was overruled

- **WI-001 stayed separate from WI-002** even though both are "make the priority list look right". The shared vocabulary is what every later screen renders into, so folding it into the first screen that consumes it would leave WI-004 and WI-005 inheriting a vocabulary that was never reviewed on its own.
- **WI-005, WI-006 and WI-007 stayed separate** rather than fusing into one node hub. Fused they exceed what a reviewer can hold in one sitting, and the two tabs carry independent technical risk (live file tailing, per-kind artifact dispatch) that is better surfaced separately.

## Milestones

### M1 - The priority list matches the design

The landing screen a launch actually lands on, rendered in the design's own vocabulary rather than the engine's.

1. [WI-001 - Shared visual vocabulary and screen chrome](WI-001.md)
2. [WI-002 - Priority list renders current work](WI-002.md)

**Exit criteria:** launching the dashboard shows the three groups in fixed order with the design's own glyphs, colours and column grid; every wireframed detail of the priority list screen is either present or recorded as an open question.

### M2 - Both list screens complete

The full footer, and the second top-level screen with the navigation contract that joins them.

1. [WI-003 - Footer status line and quitting](WI-003.md)
2. [WI-004 - Backlog screen](WI-004.md)

**Exit criteria:** both top-level screens render fully, the footer carries every status segment the design specifies on both, and `Tab` moves between them.

### M3 - The node hub

Everything reached by opening an item.

1. [WI-005 - Node hub and hierarchy tab](WI-005.md)
2. [WI-006 - Log tab](WI-006.md)
3. [WI-007 - Artifacts tab and artifact viewer](WI-007.md)

**Exit criteria:** every screen in [the screen flow](../user-flows/screen-flow-current.md) is reachable, every transition it authorises works, and back-navigation returns to the prior position from every depth.

### M4 - Row grid columns hold under realistic data

Filed by `audit-design` against the built M1-M3 screens, not planned up front - the fixed-width `id`/`role` columns those milestones already shipped wrap or silently truncate once real ids and role names exceed the token widths, which the engine's own unbounded shortcode rule permits.

1. [WI-008 - Row-grid columns hold under realistic id and role lengths](WI-008.md)

**Exit criteria:** no id or role cell in the Priority List, Backlog, or Hierarchy tab wraps a row onto a second line or drops a character silently, verified against both a short shortcode and the engine's own default, unshortened one.

---

← Back to [Operator monitors the pipeline - Version 1](../index.md)

---
type: "plan"
title: "Plan Verification"
description: "The 7 verification passes required by decomposing-designs' references/verification.md, run against this Blueprint's delivery plan before it is filed to build - what was checked, what was found, and how every finding was resolved."
---

## Plan Verification - Operator monitors the pipeline - Version 1

Run per `skills/decomposing-designs/references/verification.md`, after the plan (`plan/WI-001.md` through
`plan/WI-054.md`, later `plan/WI-055.md`) existed and before anything is filed to build. Passes 1-6 are
this plan's own self-review; Pass 7 is a fresh-eyes adversarial review, performed by a reviewer given only
the Blueprint and the plan, no other context from the planning process, instructed to break the plan
rather than confirm it - as the skill requires, since self-review alone is the weakest available check.

## Pass 1: Coverage Analysis

Traceability matrix: see [index.md](index.md)'s "Traceability - every story to its work item" table - all
51 stories map to a work item. Cross-checked by Pass 7 (all 51, not a sample) against each mapped item's
own Acceptance Criteria section: verbatim in every case, no paraphrasing or merging found.

Non-story requirements checked against the plan's coverage:

- Every named Architecture "Change 1-4" item and every named `api-contracts.md` "Genuine gap" - all
  traced to a Milestone 0 work item (WI-001 through WI-009), except one gap ("project" label resolution)
  that was under-covered - found and fixed in Pass 7, see Finding 2 below and its resolution.
- Quality Attributes' explicit open questions (store-scale degradation, store-unreadable-at-launch) were
  initially left unrecorded on any work item - a real coverage gap in the original decomposition, found
  during this plan's own self-review (not Pass 7) and fixed by adding `[designer]` open questions to
  WI-014 and WI-010 respectively, with source excerpts.
- The Hierarchy tab's assumption that every wireframe-shown step exists as a real, filed `Node` - found
  false during Pass 7 (Finding 1, below), resulting in WI-055 and edits to WI-030/031/036/037.

No requirement remains uncovered as of this document.

## Pass 2: Scope Control Analysis

Every work item traces to a source requirement:

- WI-010 through WI-054 (except WI-055): each `delivers` one or more real story ids.
- WI-001 through WI-009 and WI-055: each has no `delivers` value but carries an explicit Source Reference
  to an Architecture "Change N," a named `api-contracts.md` gap, or (WI-055 only) a direct wireframe/
  engine-source contradiction found in Pass 7 - all legitimate per `decomposition.md`'s "Technical work
  items" section, none justified only by "the next item needs it."

No work item was found with no traceable justification. None removed.

## Pass 3: Dependency Validation

Checked (and re-checked after Pass 7's fixes) for: circular dependencies (none - the full `depends_on`
graph across all 55 items is a DAG, verified by Pass 7 programmatically via DFS), hidden dependencies
(three found and fixed - see Pass 7 Findings 2 and 3 below), and milestone-ordering violations (none - no
work item depends on a later-milestone item).

One milestone-*boundary* imprecision was found in self-review, not a dependency-graph defect: Milestone
1's original exit criteria claimed a "fully-navigable" priority list, but opening an item (WI-026) is
Milestone 2's scope. Fixed by rewording the exit criteria to state plainly what Milestone 1 does and does
not include, and by correcting WI-024's own footer-shortcut-bar item to build only what's actually wired
up at its own point in the sequence (five shortcuts, not the wireframe's final six) rather than the
wireframe's fully-built-out state - see WI-024's own Constraints for the resolution in full.

## Pass 4: Incrementality Validation

Every work item was checked against all four conditions:

- **Independently implementable, buildable standalone from Acceptance Criteria + Source References
  alone**: yes for all 55 - every AC block is copied verbatim from its source story (or restated in
  Given/When/Then form with an explicit note, for the 10 engine-foundation items with no authoring
  story), every Source Reference carries a concrete excerpt, not a bare pointer.
- **Independently testable**: yes for all 55 - every item has a Verification section naming concrete,
  executable tests. One exception found and fixed: WI-032 originally listed a cross-check test that could
  not execute until a later-milestone item (WI-039) existed - corrected to be explicitly labeled a
  deferred follow-up, matching the pattern WI-024 and WI-037 already used correctly for the same kind of
  forward reference.
- **Integrable safely**: yes - no work item's own scope requires modifying another item's already-merged
  code in a way that would need coordination beyond normal dependency ordering.
- **Delivers observable behavior, or carries a source reference justifying it as technical work**: yes for
  all 55 (see Pass 2).

No work item was split or reclassified as a result of this pass, beyond the WI-032 fix above.

## Pass 5: Architecture Validation

Checked against `architecture/tui-architecture.md`'s stated component boundaries: TUI code lives in
`lightcycle/adapters/tui/` (WI-010), calls only `application/work/*`-layer use cases in-process (every
Milestone 1+ item), no new server or protocol (WI-010's own Constraints cite "Terminal-Native, No Server"
directly).

One boundary gap found in self-review: the architecture doc calls the cross-platform file-open mechanism
"a contained adapter," but WI-042 (URL) and WI-043 (file-path) as originally written didn't state this as
an explicit constraint - a builder could plausibly have written `webbrowser.open()`/`subprocess` calls
inline in screen widget code instead of behind a small driven adapter matching the `SqliteStore`/git
pattern. Fixed by adding this constraint explicitly to both items.

Quality attributes represented: Responsiveness (WI-010, and now with an explicit 10s-interval assertion -
see Pass 7 Finding 4 below), Resilience (WI-022's silent-degradation AC), Accessibility (icon+colour+label
bundled in every state-rendering item's own AC/tests, never colour-only), Security (a reasoned
non-requirement, no dedicated work needed).

## Pass 6: Design Fidelity Review

**Missing items** (requirements with no implementation path) found during this process, all now resolved:
project-label resolution's missing shared mechanism (Pass 7 Finding 2), the Hierarchy tab's planned-step
gap (Pass 7 Finding 1 / WI-055), two missing dependency edges (Pass 7 Finding 3), the untested staleness
bound (Pass 7 Finding 4). None remain open as missing - each either has a work item now, or is recorded as
an explicit `[designer]`/`[builder]` open question where the design itself doesn't settle it.

**Extra items** (planned work with no design justification): none found. Every one of the 55 work items
traces to either a real story or a named design mandate (see Pass 2).

## Pass 7: Adversarial Review

Performed by a fresh reviewer (a subagent with no context from the planning process), given only the
Blueprint and the plan, instructed to break it. Findings, as reported, most severe first:

### Finding 1 (CRITICAL) - The Hierarchy tab's premise assumed future steps are real rows

Every Hierarchy tab wireframe state (`#hierarchy`, `#blocked-dependency`, `#theme`, `#hierarchy-scrolled`)
shows an item's *entire* step sequence, including steps well past the current one, rendered as ordinary
rows with real-looking ids. Verified independently against the live engine (not just trusting the
reviewer's claim): `create_step`/`_insert_step_nocommit` are called exactly once per transition, from
exactly five call sites (`step_filing.py`, `next_step.py`, `add_item.py`, `retro_cadence.py`,
`monitor_prs.py`), never in a batch, never ahead of time. A step that hasn't been reached cannot exist as
a `nodes` row. WI-030/031/033/034/035/036/037 were all built assuming otherwise.

**Resolution:** added WI-055 (a new Milestone 0 read-only projection over the already-loaded `Flow`
graph, walking an item's pinned workflow along its normal-completion path to produce planned-but-unfiled
steps with positionally-computed ids), and updated WI-030 (renders real + planned rows together),
WI-031 (planned rows have no real state/role - flagged, with a new `[designer]` open question about the
missing design-system token for this case), WI-036 and WI-037 (selecting or jumping to a planned row is
now an explicit no-op, not an attempt to open something that doesn't exist). WI-055 itself carries a
`[designer]` open question on how "the normal-completion edge" is determined at a workflow branch point,
since neither the wireframe nor the workflow format documentation settles that generally.

### Finding 2 (HIGH) - Project-label resolution under-scoped; WI-050's premise partly wrong

`api-contracts.md` names project-label resolution (identity → display name) as a gap spanning three
screens. Six of its seven sibling gaps each became a Milestone 0 item; this one was left as an Open
Question on WI-018 alone, with no `depends_on` edge from WI-026 or WI-050 even though WI-018's own text
named both as consumers. Additionally, WI-050's Constraints claimed `BacklogUseCase`'s "project-attribution
logic" was "already correct" - true for selection/filtering/blank-when-unregistered, but verified false
for the *display value*: `HumanNodeRow.project` (via `project_of()`) is the raw `owner/name` registry
identity, not the short label ("lightcycle") the wireframes actually show.

**Resolution:** added the missing `depends_on` edges (WI-026 → WI-018, WI-050 → WI-018), and corrected
WI-050's Constraints to state explicitly that it must apply WI-018's resolution to the value
`BacklogUseCase` returns, not render it verbatim - otherwise the Backlog and Priority List would visibly
disagree on the same project's label.

### Finding 3 (HIGH) - WI-036 missing a dependency on WI-027

WI-036's own Constraints stated it "reuses WI-026/WI-027's header rendering," and its AC-2 requires a
step's ROLE field (WI-027's own deliverable) - but its `depends_on` list only named WI-030 and WI-004.
Both WI-027 and WI-036 sit in Milestone 2 with no dependency-graph edge between them, so a builder
following the graph strictly could build WI-036 first and ship a step-hub-from-hierarchy with no ROLE
field.

**Resolution:** added WI-027 (and WI-055, per Finding 1) to WI-036's `depends_on`.

### Finding 4 (MEDIUM) - The ~10s staleness bound asserted but never concretely tested

`quality-attributes.md` states a firm "~10 seconds plus render time" staleness bound, cited as grounding
throughout the plan, but every related test was phrased relatively ("updates without a full list
refresh") - the full test suite as originally written would pass unchanged if the poll interval were
quietly set to 60 seconds.

**Resolution:** added an explicit unit test to WI-010 (where the poll loop is established) asserting the
interval constant equals 10 seconds - the one place to pin it, since every later item's poll-driven
behavior reuses this same mechanism rather than declaring its own.

### Finding 5 (LOW-MEDIUM) - WI-032's own verification bullet could not execute at its own build time

WI-032 (Milestone 2) listed a cross-check test requiring WI-039 (Milestone 3) to exist, without the
"deferred, not part of this item's own build" framing WI-024 and WI-037 correctly used for the same kind
of forward reference elsewhere in the plan.

**Resolution:** reworded to match that established pattern.

### Finding 6 (LOW) - Design Packet self-consistency note (not a plan defect)

`user-flows/screen-flow-current.html` has no edge at all between the Priority List and Backlog nodes, even
though a real, storied transition exists between them (WI-051 / story `5fa1445f`). This is a gap in the
Blueprint's own design content, not in this plan - WI-051 cites the wireframes and keyboard-navigation
document directly, not this diagram, so no plan fix was needed. Recorded here per Pass 7's "cross-artifact
agreement" check, not actioned - `decomposing-designs` reads design content, it does not edit it.

### Checked and explicitly ruled out by Pass 7

Reported verbatim from the reviewer, since a pass finding nothing must state what was examined: all 51
stories' AC blocks verified verbatim (not sampled - all 51); dependency graph confirmed acyclic by DFS;
8 of 9 original Milestone-0 items confirmed legitimately grounded (the ninth gap is Finding 2); all 16
wireframe `#anchor` references confirmed to exist in both directions; no Source Reference found citing
example/mockup instance data as if it were durable content; all four `[designer]` open questions in the
plan (as of the point Pass 7 ran) checked against all four principles and quality-attributes.md, none
resolvable elsewhere; quality attributes checked individually for falsifiability (Responsiveness,
Resilience, Accessibility, Security all have or now have a real check - only the staleness sub-clause,
Finding 4, was empty). Design principles were explicitly not treated as falsifiable requirements, per the
skill's own instruction that doing so is a category error.

## Final Delivery Validation

- **Functional validation:** end-to-end coverage of every workflow (launch → priority list → hub →
  hierarchy/log/artifacts → viewer, and the backlog side-path) is distributed across Milestones 1-5's own
  Pilot-driven tests; no workflow lacks a work item.
- **Acceptance validation:** every story's AC block is copied verbatim into its work item(s) and named in
  that item's own Verification section.
- **Architecture validation:** confirmed in Pass 5 - module placement, in-process use-case calls, the
  contained-adapter boundary for external IO (WI-042/043), no new server.
- **Quality attribute validation:** Responsiveness, Resilience, Accessibility covered with real checks;
  Security is a reasoned non-requirement; the one gap found (staleness bound) is fixed (Finding 4).
- **Traceability validation:** all 51 stories traced (Pass 1/Pass 7); all 10 engine-foundation items
  grounded in a named design mandate (Pass 2/Pass 7).

### Design Conformance Decision

| Question | Answer | Evidence |
| --- | --- | --- |
| Has every requirement been delivered a work item? | YES | [index.md](index.md)'s traceability table (51/51 stories); Pass 1/Pass 6 above (engine-foundation gaps, all now covered including WI-055) |
| Has every acceptance criterion passed? | Not yet applicable | No code has been built yet - this plan has not been built against. This answer is for the build phase, not the planning phase. |
| Has every workflow been validated? | Planned, not yet executed | Each milestone's own exit criteria in [index.md](index.md) names its functional validation; execution is a build-phase activity |
| Have all quality attributes been verified? | Planned, not yet executed | See "Quality attribute validation" above - a real check now exists for each; execution is a build-phase activity |
| Does the delivered system conform to the architecture? | Planned, not yet executed | Pass 5 above; execution is a build-phase activity |
| Is every work item traceable to the design? | YES | Every `WI-###.md`'s own Source References section; Pass 2 above |
| Is every design artifact accounted for? | YES | All nine Blueprint categories plus principles read and cited across the plan (see individual WI Source References); no category found unrepresented |
| Has any unapproved functionality been introduced? | NO | Pass 2/Pass 6 "Extra items" - none found |

The plan itself is complete: every requirement traces to a work item, no unjustified work item exists, and
every finding from all 7 passes was either fixed or is recorded as an explicit, dispositioned open
question. The three answers marked "planned, not yet executed" are honest about what this document can and
cannot certify before the plan is actually built - re-run the Functional/Quality-attribute/Architecture
validation lines against the real, completed system once Milestones 0-5 ship, per SKILL.md's own
distinction between planning and building.

---

← Back to [Delivery Plan](index.md)

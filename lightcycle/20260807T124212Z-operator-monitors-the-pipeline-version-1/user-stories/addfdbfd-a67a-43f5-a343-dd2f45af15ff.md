---
type: "user-story"
title: "See a quiet message when the backlog is empty"
activity: "See what's coming next"
step: "See the todo backlog"
---

## See a quiet message when the backlog is empty

**Who:** Operator

**What:** When the backlog has nothing to show - overall, or under the current project filter - a calm message replaces the list instead of a blank screen.

**Why:** So an empty backlog, or an empty filtered view, reads as "nothing here" rather than "something's broken" - the same reassurance the priority list already gives.

**Activity:** See what's coming next - Know what's waiting in the backlog so nothing surprises me.

**Step:** See the todo backlog

**Status:** ready

### Acceptance Criteria

- Given no todo items exist under the current filter, when the backlog renders, then a calm message is shown in place of the list, not a blank area.
- Given the message is shown because a project filter is active, not because the backlog is empty overall, when I read the message, then it makes clear the filter is what's empty, so I know to check 'All' rather than assume there's no backlog work at all.
- Given an item later becomes available under the current filter, when that happens, then the message is replaced by the list showing that item.

### References

- [wireframe-backlog.html#empty (overall-empty, AC-1) and #empty-filtered (filter-empty, AC-2) - two distinct mocked states](../wireframes/wireframe-backlog.md)

### Context

Picking between the two messages needs more than the current filter's result set - it needs to know whether the backlog has anything at all, separately from whether it has anything under the active filter. `BacklogUseCase.execute` (lightcycle/application/work/backlog.py) only ever returns rows for the single `project` value it's given; there's no unfiltered count riding alongside a filtered one, so telling "empty overall" from "empty under this filter" means checking both, not just re-reading what the current filter already returned empty-handed.

---

← Back to [User Stories](index.md)

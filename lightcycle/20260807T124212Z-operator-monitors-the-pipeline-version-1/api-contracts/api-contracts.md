---
type: "api-contract"
title: "API Contracts"
description: "The gap between the engine's existing read-only use cases and what these screens need, stated as requirements to resolve rather than prescribed code."
tags: []
---

## API Contracts - Operator Monitors the Pipeline

No network API exists or is proposed - per the "Terminal-Native, No Server" principle, the TUI calls the engine's read-only use cases in-process. The contract here is that boundary: what the read surface must make available for each screen to work. This is not a DTO reference for the existing use cases (`status.py`, `trace.py`, and the rest) - those are real, working, self-documenting code with no drift risk, and restating their current shape here would only go stale. It covers the gap between what already exists and what these wireframes need, stated as requirements a builder resolves, not as prescribed code changes.

## Already covered - no new interface needed

Priority List and Backlog both read off `Node` (id, title, role, step, state, project, theme, `needs` [the blocking node's id], `since`, artifacts) plus `StatusUseCase.lanes`. Nothing about those two screens' base data requires new interface surface.

## Genuine gaps

- **"Needs-attention" has no engine-side equivalent.** The engine computes exactly four lanes - `inbox`, `active`, `queue`, `blocked` - never a merged fifth. This design's needs-attention bucket merges `inbox` and `blocked` for display (see the Domain Model artifact); the read surface must preserve that as a real design decision, not something a builder routes around. It must also never be sourced from `Node.attention`, an unrelated backlog-triage flag that happens to share the name. Whether the merge happens in a new use case or client-side in the TUI is open - reading the two raw lanes and merging in the TUI is the minimal option and requires no engine change at all.
- **Each step's role must be available from wherever the Node Hub reads its trace data.** `TraceUseCase`'s current `TraceNode` doesn't carry `role`, even though the `Node` its children are built from does. Extending `TraceNode`, switching to returning `Node` objects, or something else - build-time choice, not a design one.
- **Elapsed time needs a decision about where it's computed.** `Node.since`/`fired_at` exist; nothing formats "14m" / "45s" from them today. Computed once per poll tick in the use case keeps the TUI dumb and testable against fakes; computed in the TUI keeps the read-only use cases free of presentation concerns. Both are defensible - flagged so it's decided deliberately, not by accident of whichever screen gets built first.
- **`Artifact` needs `internal` and `kind`.** Already fully specified in `tui-architecture.md`'s Change 1 - not a new decision, just a real dependency: nothing artifact-related here can be built before that lands.
- **Every screen's "project" label needs resolving from identity to a display name, not just Backlog's.** `Node.project` is the raw registry identity (`owner/name`); the wireframes show a short label ("lightcycle") on the Priority List and Backlog rows and the Node Hub header alike. Resolved once, somewhere all three screens draw from, or per-screen - same shape of open question as elapsed time, same reason to flag rather than silently pick one, now multiplied across three call sites instead of one.
- **Breaker state has no read-only use case at all.** `is_open`/`reset_at` (`lightcycle/domain/pool/breaker.py`) are real and persisted (`logs/breaker.json`), but the only thing that reads them today is the pool loop itself - the only external surface is transient `lc start` log lines ("breaker opened until HH:MM:SS" / "breaker closed"), not a queryable current state. The footer's claude-availability indicator needs a new read-only use case (or an addition to an existing one, e.g. `StatusUseCase`) that returns `{is_open, reset_at}` on demand - not the log-line event, the state at rest.
- **The backlog's project-filter picker needs every registered project's count at once, not one filtered result at a time.** `BacklogUseCase.execute` (`application/work/backlog.py`) returns rows (optionally grouped) for whichever single `project` value it's given - there's no call that returns a count per registered project independent of which filter is active. The picker (`wireframe-backlog.html#picker-open`) needs exactly that, up front, before any filter is chosen. A new read path (a `counts()`-style method, or a batch call across every registered project) is needed - calling the existing use case once per project from the TUI would work but defeats the point of a single use-case-layer surface, and is the kind of workaround this section exists to head off.

---

← Back to [API Contracts](index.md)

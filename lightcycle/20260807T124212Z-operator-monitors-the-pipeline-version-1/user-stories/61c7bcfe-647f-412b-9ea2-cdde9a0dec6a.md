---
type: "user-story"
title: "See the dashboard connect and render"
activity: "Get oriented on the pipeline"
step: "Launch the dashboard"
---

## See the dashboard connect and render

**Who:** Operator

**What:** Launching the TUI immediately connects to the live lightcycle store and renders the current pipeline state.

**Why:** So there's no wait, and no need for a separate command, to confirm the dashboard is working.

**Activity:** Get oriented on the pipeline - See the whole pipeline at a glance - what's active, blocked, waiting on me, and queued - the moment it changes, as long as the dashboard is running.

**Step:** Launch the dashboard

**Status:** ready

### Acceptance Criteria

- Given the lightcycle store is reachable, when I launch the dashboard, then it renders the priority list within about a second.
- Given the dashboard has just launched, when it renders, then the footer status bar is visible immediately alongside the list, not as a separate loading step.

### References

- ["Responsiveness" - the ~1s render target and why it's achievable, alongside the staleness bound the 10s poll interval implies](../quality-attributes/quality-attributes.md)
- [wireframe-priority-list.html#normal](../wireframes/wireframe-priority-list.md)

### Context

store.db already runs in WAL journal mode with a 5-second busy_timeout (lightcycle/adapters/sqlite_store.py) - a TUI process opening its own connection at launch reads cleanly even while the pool is actively writing, with no locking or retry logic needed on the TUI side. That's what actually protects the ~1s render target on AC-1 when the pool is running, alongside the no-network-round-trip reasoning quality-attributes.md already gives - the more realistic threat to a fast first render is write contention from the pool, not network latency, and it's already handled at the store layer.

---

← Back to [User Stories](index.md)

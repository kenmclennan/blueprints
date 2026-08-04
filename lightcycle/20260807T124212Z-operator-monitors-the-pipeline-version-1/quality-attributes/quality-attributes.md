---
type: "quality-attribute"
title: "Quality Attributes"
description: "The non-functional commitments this design makes - responsiveness, accessibility, resilience, and security posture - plus one open question on store scale."
tags: []
---

## Quality Attributes - Operator Monitors the Pipeline

Non-functional targets this design commits to. Two of these were already asserted - a quantified AC on one story, a resolved decision in the architecture doc - with no shared rationale connecting them; this collects them in one place instead of leaving them as isolated numbers.

## Responsiveness

- **Initial render: within about a second of launch.** Stated on "See the dashboard connect and render" (AC-1). Achievable because the TUI reads the local store in-process (no network round trip) - see `tui-architecture.md`, "Engine interaction."
- **Staleness bound: at most ~10 seconds plus render time**, driven directly by the poll interval (`tui-architecture.md`, "Resolved decisions"). This is a deliberate tradeoff, not an oversight: the dashboard's job is situational awareness, not a real-time feed, and 10s keeps polling cheap against a single local SQLite file. If that tradeoff ever needs revisiting, it's a poll-interval change, not a new architecture.
- **Live log latency: no fixed bound, tied to file writes.** The hub's Log tab bypasses the poll loop entirely and tails a file offset directly (`tui-architecture.md`, "Staying live"), so new lines appear as fast as the worker writes them, independent of the 10s figure above.

## Accessibility

- **Status is never colour-only.** Every needs-attention/active/queued distinction pairs a colour with its own icon and label (see the Priority List and Hierarchy wireframes' `.icon` classes, and stories like "See needs-attention items pinned to the top" and "See each node's id, status, and role in the hierarchy") - already a real decision made across the wireframes, just not previously stated as a standing rule. Any future screen or state added to this design should keep that pairing rather than introduce a colour-only signal.
- **Terminal-native means terminal-inherited.** Font size, contrast, and screen-reader behaviour are the operator's own terminal and OS settings, not something the TUI controls or should try to override.

## Resilience

- **A failed upgrade check degrades silently, not loudly.** "See when a newer version is available" (AC-3): if the check can't complete, the footer just omits the indicator rather than erroring or blocking anything else. This is the one place the design reaches past the local store, and it already has a stated fallback.
- **Everything else is local, so the main failure mode is a missing or corrupt store, not a network failure.** No story or architecture text currently specifies dashboard behaviour if `store.db` is unreadable at launch - noted as an open question below rather than answered speculatively.

## Security

**No dedicated posture needed, and that's a reasoned position, not silence.** The TUI is read-only against a local SQLite file and log files the operator already owns (`tui-architecture.md`'s "Engine interaction": "no new protocol, no server"). There's no network-facing surface, no auth boundary, and no write path for this design to secure - see the "Terminal-Native, No Server" principle. The one network reach (the upgrade check, above) carries no credentials and degrades to silence on failure.

## Open question

- **Behaviour under a large or slow store isn't specified.** How the priority list, backlog, or hierarchy should degrade (if at all) once the live store holds substantially more than today's real counts (250 items, 13 backlog items) hasn't been designed. Flagged here rather than answered speculatively - the same discipline `tui-architecture.md` already applies to its own Change 4.

---

← Back to [Quality Attributes](index.md)

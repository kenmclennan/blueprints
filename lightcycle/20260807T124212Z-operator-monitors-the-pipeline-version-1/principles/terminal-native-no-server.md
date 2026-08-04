---
title: "Terminal-Native, No Server"
category: "architecture"
---

## Terminal-Native, No Server

**Category:** architecture

**Outcome:** Installing and running the TUI feels like the rest of lightcycle - a single command, zero extra services to stand up or secure.

**Strategy:**
Read state directly from the same store/log files `lc` already writes (`~/.lightcycle/store.db`, `logs/`), in-process or via the engine's read-only use cases - never through a new HTTP/websocket server. This keeps the door open for a web UI later as a genuinely separate, explicitly-scoped project rather than smuggling a server in through the TUI.

**Rationale:**
Matches lightcycle's zero-runtime-deps, single-operator ethos (see workspace CLAUDE.md's engine architecture) and avoids scope creep from the TUI accidentally becoming a client of a server nobody asked for yet.

---

← Back to [Design Principles](index.md)

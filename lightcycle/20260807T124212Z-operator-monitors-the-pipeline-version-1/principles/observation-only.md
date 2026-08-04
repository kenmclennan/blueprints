---
title: "Observation Only"
category: "product"
---

## Observation Only

**Category:** product

**Outcome:** An operator can trust the TUI as a window onto the pool - nothing they do inside it can claim, close, unblock, or reconfigure a node.

**Strategy:**
Every view reads from the store/logs only. Actions like clearing gates, claiming steps, writing briefs, or editing config stay on `lc driver` and the raw CLI. If a future need for mutation emerges, it gets its own principle and explicit scope decision - not a quiet addition to this one.

The guarantee is over pipeline state, not over the operator's machine. Opening a URL in a browser or a file in its default application hands control to another program deliberately, and what happens there is outside this principle's scope.

**Rationale:**
Keeps the first surface small and low-risk, and keeps the human-in-the-loop gates (spec PRs, await-merge) exactly where they are today - reviewed deliberately via the Driver, not fat-fingered from a dashboard.

---

← Back to [Design Principles](index.md)

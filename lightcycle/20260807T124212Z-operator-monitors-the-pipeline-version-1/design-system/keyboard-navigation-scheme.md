---
type: "design-system"
title: "Keyboard Navigation Scheme"
description: "The full keyboard contract, global and per-screen, and the reasoning behind key choices like [ / ] over Tab."
tags: []
---

## Keyboard Navigation Scheme — Operator Monitors the Pipeline

The canonical keyboard contract for the lightcycle TUI. Individual stories name their own specific
key inline in their acceptance criteria (e.g. "press Enter or →") - this document is the one place
that lays out the whole scheme and why it's shaped this way.

## Global — works on every screen

| Key                     | Action                                                                                                                                 |
| ----------------------- | -------------------------------------------------------------------------------------------------------------------------------------- |
| ↑ / ↓                   | Move the selection                                                                                                                     |
| **Enter** or **→**      | Open / confirm the selected row                                                                                                        |
| **Esc** or **←**        | Back, one level - always returns to exactly where you were (One Navigation Contract)                                                   |
| **Tab**                 | Jump to the other top-level view (Current work ↔ Backlog) - works from _any_ depth, not just from the two top-level screens themselves |
| **Ctrl+U** / **Ctrl+D** | Fast-scroll a long list or log, a screen at a time                                                                                     |
| **q** or **Ctrl+C**     | Quit                                                                                                                                   |

No vim-style `j`/`k` alternates - arrows only, deliberately, to keep the key set small. Fast-scroll is
Ctrl+U/Ctrl+D rather than PageUp/PageDown specifically because most Mac laptop keyboards have no
dedicated Page keys (Fn+Up/Down only) - Ctrl+U/Ctrl+D is available everywhere.

## Context-specific

**Backlog** — the one context-specific key on this screen

| Key   | Action                                                                                                                                                                                                                                                                                |
| ----- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| **f** | Open the project filter picker - a navigable list of registered projects (plus "All"); arrows to move, Enter to apply, Esc to cancel. Not a cycle: with more than a handful of projects, stepping through them one keypress at a time doesn't scale, so `f` opens a pick-list instead |

**Node hub** — any open node (theme, item, or step)

| Key   | Action                                            |
| ----- | ------------------------------------------------- |
| **]** | Next tab: Hierarchy → Log → Artifacts → Hierarchy |
| **[** | Previous tab, same cycle in reverse               |

Which tab you land on when a node first opens is contextual, not fixed - Log for an active node,
Artifacts for a needs-attention item on a human step, Hierarchy for one blocked on a dependency or still
queued. `[`/`]` only move you _from_ wherever you landed; they don't reset the default.

**Hierarchy tab** — while a node is highlighted within it (not yet opened)

| Key   | Action                                                                                                                                                          |
| ----- | --------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| **a** | Jump straight to that node's Artifacts tab, skipping its own contextual default                                                                                 |
| **l** | Jump straight to that node's Log tab - live if it's the active step, past/historical if done. No-op on a human step or a step that hasn't run (nothing to show) |

## Why arrows double as Enter/Esc

Left/right as open/back aliases (in addition to Enter/Esc) is a deliberate nod to the up/down-for-move,
left/right-for-depth pattern from column-based file browsers (Finder's column view, `ranger`, Miller
columns generally). It costs nothing here - no screen in this map uses left/right for anything else
(all lists are vertical), so there's no conflict to design around.

## Why tabs use [ / ] and not Tab

Tab is already the _global_ "jump to Backlog, from anywhere, any depth" key. If it also cycled a node's
tabs, the two meanings would collide the moment you wanted to jump to Backlog while a node was open -
which one wins? `[`/`]` (a common previous/next-tab convention) keeps Tab's global meaning untouched in
every context, including inside an open node.

## Why this lives here and not spread across every story

Each story's own acceptance criteria names _its own_ key concretely (Patton's stories should be
independently testable without needing a side reference to know what to build). But writing the full
rationale and the whole table out again on every one of the ~20 stories that touch navigation would
bloat the map past readability. This document is the one place for the complete picture; stories stay
lean, each naming only the one or two keys relevant to its own outcome.

---

← Back to [Design System](index.md)

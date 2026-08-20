---
type: "design-system"
title: "Design System Tokens"
description: "The shared colour, state-icon, grid, and typography tokens every wireframe in this design draws from."
tags: []
---

## Design System - Operator Monitors the Pipeline

Extracted 2026-08-02 from the CSS custom properties already declared, near-identically, across all four wireframes (`wireframe-priority-list.html`, `wireframe-backlog.html`, `wireframe-hub.html`, `wireframe-artifact-viewer.html`) - this document did not invent anything; it consolidates tokens that already existed, duplicated, into one citable source. This is the gap the design packet audit (2026-08-01) found: every wireframe agreed on the same values independently, but nothing named them as a shared system, so every future screen had to re-derive or re-guess them.

For keyboard, focus and interaction conventions (arrows, `Tab`, `Ctrl+U`/`Ctrl+D`, why `[`/`]` and not `Tab` for hub tabs), see `keyboard-navigation-scheme.md` - this document covers the visual half only: colour, state, layout convention.

## Colour tokens

| Token | Hex | Meaning | Declared in |
| --- | --- | --- | --- |
| `--bg` | `#0c0c0f` | Terminal container background - the bordered `.term` box every screen renders inside | All four wireframes |
| `--panel` | `#101014` | Overlay panel background - the picker (Backlog) and toast (Artifact Viewer) surfaces, raised above the terminal | All four |
| `--border` | `#3a3a42` | All border lines - panel border, dividers, footer top border | All four |
| `--text` | `#d8d8dc` | Primary text | All four |
| `--dim` | `#6e6e78` | Secondary/muted text - unselected icons, footer key hints, non-current log lines, dim labels | All four |
| `--cyan` | `#5fd7e0` | Active / selected / current state | All four |
| `--amber` | `#e0a95f` | Warning / dependency / attention-adjacent state | All four |
| `--red` | `#e05f6b` | Needs-attention / alert state | Priority List, Backlog, Hub - **not** the Artifact Viewer |
| `--selected-bg` | `#1c2a2c` | Selected row background | All four |

**`--red` is absent from the Artifact Viewer wireframe, and that is correct, not an oversight**: nothing in that screen ever shows a needs-attention marker - it renders one artifact at a time, already selected. A future artifact-viewer screen that needs a needs-attention treatment should add the token there rather than assume it's already available.

**The page itself (`body`) is a separate, hardcoded `#050506`, not a token** - one shade darker than `--bg`, so the bordered terminal reads as a distinct surface against the page behind it. Only the terminal container and the two overlay surfaces draw from the token set above; the page background was never meant to be themeable per-screen, so it was never extracted.

## State to icon/colour mapping

Canonical source: the priority list's row icons (`.icon.attn`, `.icon.dep`, `.icon.active`, `.icon.queued`).

| State | Icon | Colour token | Notes |
| --- | --- | --- | --- |
| Needs-attention | `●` | `--red` |  |
| Needs-attention, dependency-blocked | `●⛓` | `--red` icon + `--amber` chain-link | Dual icon, not a replacement - the red dot always stays, the amber chain-link is additive |
| Active | `▸` | `--cyan` |  |
| Queued | `○` | `--dim` |  |
| Selection cursor | `❯` | `--cyan` | Row grid's leftmost cell, every list screen |

This is the exact table `decomposing-designs`' own "Visual and rendered artifacts" fix required a shared block for, and the one the design packet audit found missing.

## Row grid conventions

Four list grids, one sizing rule. The Hierarchy and Artifacts grids are stated here for the first time - they existed only in code, which is a large part of why the overflow behaviour below was never designed.

| Screen        | Columns                                      |
| ------------- | -------------------------------------------- |
| Priority List | cursor, icon, id, project, title, step, time |
| Backlog       | cursor, id, project, title                   |
| Hierarchy tab | icon, content, id, title, role               |
| Artifacts tab | type, value                                  |

Groups within a list (needs-attention / active / queued) are separated by a blank row, not a section header or rule line - the icon and colour differentiate the group, not a label.

### Every column is one of three kinds

| Kind | Columns | Width | Overflow |
| --- | --- | --- | --- |
| **Glyph** | cursor, icon, content | Fixed - 2ch, 4ch, 2ch | Cannot overflow |
| **Atomic** | id, project, step, role, type, time | The longest value in the list, recomputed each tick | Never - see The breakpoint |
| **Flexible** | title, value | The remaining budget, never below 24ch | Wraps |

**An atomic cell is never truncated and never wrapped.** These are identifiers, and a cut identifier is worse than a wide column: `LC-1234.10` truncated to `LC-1234.1` is not a visibly-cut fragment, it is a complete-looking id that belongs to a different node. An ellipsis does not rescue it either - `LIGHTCYCLE-3.1` and `LIGHTCYCLE-3.1.1` both render as `LIGHTCYCLE-3…`, so two distinct nodes still read identically. Nor can a wider fixed width fix it, because a project's shortcode is unbounded by construction: the engine defaults it to the uppercased repo name, so `LIGHTCYCLE-3.1.1` is a legitimate sixteen-character id and any constant only moves the cliff.

**Atomic widths are measured across the whole list, not the visible window.** Sizing to the rows currently on screen makes columns change width as the operator scrolls, and a grid that shifts under the eye is harder to read than one that is merely wide. Measuring the whole list means a column moves only when the data moves, which the poll tick already bounds.

**A flexible cell wraps.** Titles, descriptions and log lines are prose: a wrapped title is still readable, and a title is never the thing an operator reads back, types, or quotes to someone else.

### The breakpoint

A row cannot always be a row. Stack when:

```
sum(glyph widths) + sum(atomic content widths) + sum(flexible minimums) > row budget
```

Both halves of that sum are load-bearing. Without the flexible minimum, a single outsized id would content-size its own column and crush the title to nothing while no rule was ever violated - the minimum is what turns that into a stacked row instead of an unreadable one.

Stacked, a row keeps its identifying fields on a first line and moves its prose to a continuation line. The first line is still a grid and still obeys every rule above; only the flexible column moves.

#### Priority List

```
❯ ●  LC-290.1.90  lightcycle  code-review-rounds                14m
     Deliver the operator-monitors-the-pipeline Blueprint
```

Cursor, icon, id, project and step stay on the first line; `time` stays with them, right-aligned, because it is short and it is the signal that says how long this has been waiting. The title moves underneath, indented to where it starts in the unstacked grid.

#### Backlog

```
❯ LC-310  lightcycle
  Content-pin guard false-positives across a replaced PR
```

#### Hierarchy tab

```
○ •  LC-290.1.86                                    implement-features
       Deliver the operator-monitors-the-pipeline Blueprint
```

**The continuation line inherits the row's own depth indent, plus a further 2ch.** Indentation is what carries ancestry on this screen, so a continuation line set at a fixed offset would align a child's prose with its parent's and read as a sibling row. The extra 2ch keeps the continuation visibly subordinate to its own first line.

#### Artifacts tab

```
branch (feature)
  feat/LC-290.1-code-3-deliver-the-operator-monitors-the
```

Only two columns, so this grid reaches the breakpoint last - `type` plus the 24ch minimum has to exceed the budget before it stacks.

### The floor

Stacking has its own minimum: the glyph columns plus the widest atomic value on the first line. Below that, no arrangement of this design works, and the screen must say so rather than render a corrupted grid - a single centred message naming the width needed, in `--dim`, with the footer still present so the operator can still quit. This is the state the design previously did not have.

## Typography

`ui-monospace, "SF Mono", "Cascadia Code", "JetBrains Mono", Consolas, monospace` - identical stack declared in all four wireframes' `body` rule. No secondary font; weight and size variation (e.g. bold for the active tab-strip entry, uppercase tracking for picker headers) carries all typographic emphasis.

## Footer

Two-line footer, present on every screen: a status line (pool/version left-aligned, upgrade-available right-aligned, when present) above a contextual shortcut-keys line. Selected row background is `--selected-bg`; the footer itself sets no background of its own, so it shows the same `--bg` as the rest of the terminal, never `--panel`.

---

← Back to [Design System](index.md)

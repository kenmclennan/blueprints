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
| `--dim` | `#6e6e78` | Secondary/muted text - unselected icons, footer key hints, dim labels. **Not log lines** - see "The Log tab reads at full contrast" | All four |
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

## The node hub's header is a fixed summary, never a document

Every field in the hub header is one line, and **the description is at most two**, wrapped and then clipped. Nothing an operator can put in a node makes the header grow.

The header is chrome: it says which node you are looking at. The tabs beneath it are the screen. A header that can outgrow its own frame takes the tab strip and the whole tab body with it - reported live, where a description holding a design document made the header 42 rows on a 28-row terminal and the Log tab, though selected, had nowhere left to draw.

Two lines is what this design already draws; the bound simply makes it true at every length. The full text is not reachable from this screen and does not need to be - the hub is read-only situational awareness, not a reader.

A state exercising a description far longer than two lines belongs in the rendered set, or the bound is a claim nothing checks.

## The scrollbar is a position indicator, not a control

This is a keyboard TUI: scrolling is the arrow keys, `ctrl-u`/`ctrl-d`, and nothing else. The scrollbar is never clicked, never dragged, never focused. Its whole job is to say where in the buffer you are.

So it recedes. The **track is `--bg`** - it disappears into the frame, leaving no visible gutter down the side of a list. The **thumb is `--dim`** - present enough to find at a glance, quiet enough not to compete with the content it sits beside.

No other colour appears in a scrollbar. It has no hover, active, or focused variant, because none of those states can be reached.

## The Log tab reads at full contrast

Every log line renders in `--text`. The log is not chrome subordinate to the header; it is the thing an operator opened the tab to read, and a screenful of `--dim` is what dim was never chosen for - the token was picked against a four-line illustration, where subordinating the log was the right trade, and it inverts at real length.

So brightness carries no meaning in the log, and the live line is distinguished the way the wireframe already draws it - by the cyan block cursor `▌` at its end, present only while the tail is live. That is the only "now" cue; do not reintroduce a brightness difference alongside it.

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

**A stacked row is not a grid row.** It is one full-width cell holding two composed lines: the identifying fields, each padded to its atomic width so they still line up down the list, and beneath them the prose. The column layout is not used for a stacked row at all.

That is a statement about mechanism, and it is deliberate. A row's columns are laid out side by side with one width each and no spanning, so there is no width you can give a flexible column that puts its prose on a line of its own - widen it and the atomic columns that follow it are simply pushed off the frame. On the Priority List and the Hierarchy tab the flexible column is not last (`step`/`time` and `role` follow it), so that is not a hypothetical: it clips `role` to a single character. The columns are the wrong instrument for a two-line row, which is what "a row cannot always be a row" means in practice.

**The continuation line's indent is the glyph columns' width, minimum 2ch** - plus the row's own depth indent on the Hierarchy tab, where indentation carries ancestry and a fixed offset would align a child's prose with its parent's.

| Grid          | Glyph columns          | Continuation indent |
| ------------- | ---------------------- | ------------------- |
| Priority List | cursor 2 + icon 4      | 6                   |
| Backlog       | cursor 2               | 2                   |
| Hierarchy tab | icon 4 + content 2     | 6 + the row's depth indent |
| Artifacts tab | none                   | 2                   |

It is emphatically **not** where the flexible column starts in the unstacked grid. That offset is most of the row, and on a grid whose flexible column is last it is the whole first line - which would make the stacked layout cost exactly as much width as the unstacked one and so never be reachable at any terminal width.

The prose therefore gets the row budget less that small indent, and the 24ch flexible minimum holds in the stacked layout exactly as in the unstacked one. If it cannot, the grid is at its floor.

#### Priority List

```
❯ ●  LC-290.1.90  lightcycle  code-review-rounds                14m
      Deliver the operator-monitors-the-pipeline Blueprint
```

Cursor, icon, id, project and step stay on the first line, each padded to its atomic width; `time` stays with them, right-aligned, because it is short and it is the signal that says how long this has been waiting. The title moves underneath, at the continuation indent.

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

The continuation indent here is the glyph width plus the row's own depth indent, so a child's prose never aligns with its parent's.

#### Artifacts tab

```
branch (feature)
  feat/LC-290.1-code-3-deliver-the-operator-monitors-the
```

Only two columns, so this grid reaches the breakpoint last - `type` plus the 24ch minimum has to exceed the budget before it stacks.

### The floor

Stacking has its own minimum, and it is whichever of its two lines needs more room:

```
floor = max(sum(glyph widths) + sum(atomic content widths),   the first line
            continuation indent + flexible minimum)            the continuation line
```

The second term is the one that is easy to forget and the one that bites. A stacked row's first line can be narrow - it holds only identifiers - while its continuation line still needs the full 24ch to be prose rather than a column of fragments. A floor set from the first line alone leaves a band of widths above it where the screen insists it can render and produces mush.

Below the floor no arrangement of this design works, and the screen must say so rather than render a corrupted grid - a single centred message in `--dim`, with the footer still present so the operator can still quit.

**The message names the terminal width the operator must set**, not the grid's own internal budget. Borders, padding and the scrollbar all sit between the two, so a message quoting the internal number names a width that still does not work when the operator resizes to it.

## Typography

`ui-monospace, "SF Mono", "Cascadia Code", "JetBrains Mono", Consolas, monospace` - identical stack declared in all four wireframes' `body` rule. No secondary font; weight and size variation (e.g. bold for the active tab-strip entry, uppercase tracking for picker headers) carries all typographic emphasis.

## Footer

Two-line footer, present on every screen: a status line (pool/version left-aligned, upgrade-available right-aligned, when present) above a contextual shortcut-keys line. Selected row background is `--selected-bg`; the footer itself sets no background of its own, so it shows the same `--bg` as the rest of the terminal, never `--panel`.

---

← Back to [Design System](index.md)

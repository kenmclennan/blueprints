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

| Token           | Hex       | Meaning                                                                                                         | Declared in                                               |
| --------------- | --------- | --------------------------------------------------------------------------------------------------------------- | --------------------------------------------------------- |
| `--bg`          | `#0c0c0f` | Terminal container background - the bordered `.term` box every screen renders inside                            | All four wireframes                                       |
| `--panel`       | `#101014` | Overlay panel background - the picker (Backlog) and toast (Artifact Viewer) surfaces, raised above the terminal | All four                                                  |
| `--border`      | `#3a3a42` | All border lines - panel border, dividers, footer top border                                                    | All four                                                  |
| `--text`        | `#d8d8dc` | Primary text                                                                                                    | All four                                                  |
| `--dim`         | `#6e6e78` | Secondary/muted text - unselected icons, footer key hints, non-current log lines, dim labels                    | All four                                                  |
| `--cyan`        | `#5fd7e0` | Active / selected / current state                                                                               | All four                                                  |
| `--amber`       | `#e0a95f` | Warning / dependency / attention-adjacent state                                                                 | All four                                                  |
| `--red`         | `#e05f6b` | Needs-attention / alert state                                                                                   | Priority List, Backlog, Hub - **not** the Artifact Viewer |
| `--selected-bg` | `#1c2a2c` | Selected row background                                                                                         | All four                                                  |

**`--red` is absent from the Artifact Viewer wireframe, and that is correct, not an oversight**: nothing in that screen ever shows a needs-attention marker - it renders one artifact at a time, already selected. A future artifact-viewer screen that needs a needs-attention treatment should add the token there rather than assume it's already available.

**The page itself (`body`) is a separate, hardcoded `#050506`, not a token** - one shade darker than `--bg`, so the bordered terminal reads as a distinct surface against the page behind it. Only the terminal container and the two overlay surfaces draw from the token set above; the page background was never meant to be themeable per-screen, so it was never extracted.

## State to icon/colour mapping

Canonical source: the priority list's row icons (`.icon.attn`, `.icon.dep`, `.icon.active`, `.icon.queued`).

| State                               | Icon | Colour token                        | Notes                                                                                     |
| ----------------------------------- | ---- | ----------------------------------- | ----------------------------------------------------------------------------------------- |
| Needs-attention                     | `●`  | `--red`                             |                                                                                           |
| Needs-attention, dependency-blocked | `●⛓` | `--red` icon + `--amber` chain-link | Dual icon, not a replacement - the red dot always stays, the amber chain-link is additive |
| Active                              | `▸`  | `--cyan`                            |                                                                                           |
| Queued                              | `○`  | `--dim`                             |                                                                                           |
| Selection cursor                    | `❯`  | `--cyan`                            | Row grid's leftmost cell, every list screen                                               |

This is the exact table `decomposing-designs`' own "Visual and rendered artifacts" fix required a shared block for, and the one the design packet audit found missing.

## Row grid conventions

| Screen        | Grid (CSS `grid-template-columns`) | Columns                                      |
| ------------- | ---------------------------------- | -------------------------------------------- |
| Priority List | `2ch 4ch 9ch 10ch 1fr 16ch 8ch`    | cursor, icon, id, project, title, step, time |
| Backlog       | `2ch 9ch 10ch 1fr`                 | cursor, id, project, title                   |

Groups within a list (needs-attention / active / queued) are separated by a blank row, not a section header or rule line - the icon and colour differentiate the group, not a label.

## Typography

`ui-monospace, "SF Mono", "Cascadia Code", "JetBrains Mono", Consolas, monospace` - identical stack declared in all four wireframes' `body` rule. No secondary font; weight and size variation (e.g. bold for the active tab-strip entry, uppercase tracking for picker headers) carries all typographic emphasis.

## Footer

Two-line footer, present on every screen: a status line (pool/version left-aligned, upgrade-available right-aligned, when present) above a contextual shortcut-keys line. Selected row background is `--selected-bg`; the footer itself sets no background of its own, so it shows the same `--bg` as the rest of the terminal, never `--panel`.

---

← Back to [Design System](index.md)

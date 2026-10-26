# Minesweeper — Specification

**Purpose.** A specification for a classic Minesweeper game implemented as a static
client-side web app.

## Overview

- **What it is.** Minesweeper: reveal every safe cell without hitting a mine, using
  flags to mark mines.
- **Technology.** Plain HTML5, CSS, and vanilla JavaScript. No build step, no
  framework, no backend, no dependencies.
- **How to run.** Open `index.html` in a modern web browser.
- **Status.** This specification describes the **current implementation** and is the
  source of truth. Where the implementation diverged from the original brief, the
  change is recorded in [`decisions.md`](decisions.md).

## Documents

| File | Area |
|------|------|
| [`gameplay.md`](gameplay.md) | Board, reveal, flood fill, adjacency, flagging, win/lose |
| [`difficulty.md`](difficulty.md) | Difficulty levels and board scaling |
| [`interface.md`](interface.md) | Layout, controls, dialogs, styling, accessibility |
| [`timer.md`](timer.md) | Elapsed-time counter |
| [`architecture.md`](architecture.md) | Files, data model, key functions, algorithms |
| [`statistics.md`](statistics.md) | Per-difficulty results persisted in localStorage |
| [`decisions.md`](decisions.md) | Decision log and superseded requirements |
| [`testing.md`](testing.md) | Acceptance scenarios and automated coverage |

## Conventions

- **Requirement IDs** are stable and area-prefixed so they can be referenced from
  decisions and tests:
  - `GP-#` gameplay
  - `DF-#` difficulty
  - `UI-#` interface
  - `TM-#` timer
  - `AR-#` architecture
  - `ST-#` statistics
  - `DC-#` decisions
  - `TS-#` testing scenarios
- Behavioural specs use a `Requirement | Acceptance criteria` table.
  Descriptive documents (architecture, decisions, testing) use prose and tables
  where that fits better.
- Present-tense, implementation-neutral wording; no code is reproduced except where
  a formula or data shape must be precise.

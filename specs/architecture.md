# Architecture

**Purpose.** The structure of the codebase: files, data model, key functions, and the
algorithms behind the game.

**Status.** Current (source of truth).

## Files

| File | Role |
|------|------|
| `index.html` | Page markup: top bar, status bar, board, and the two dialog overlays. |
| `style.css` | Layout, board grid, cell states, dialog/overlay styling, number colours. |
| `script.js` | All game state, logic, rendering, and event wiring. |

There is no build step, framework, or server: everything runs in the browser from
these three files.

## Data model

| Name | Type | Meaning |
|------|------|---------|
| `board` | `rows × cols` array | `board[r][c] = { mine, revealed, flagged, adjacent }`. |
| `cells` | `rows × cols` array | The DOM element for each cell, parallel to `board`. |
| `cols`, `rows`, `mineCount` | numbers | Per-game dimensions and mine count, set by [difficulty](difficulty.md). |
| `state` | string | `idle` \| `playing` \| `lost` \| `won`. |
| `minesPlaced` | boolean | Whether mines have been placed (lazy placement). |
| `flagCount` | number | Number of currently flagged cells. |
| `exploded` | `{ r, c }` \| null | The mine the player hit, if any. |
| `timerId`, `startTime`, `elapsedSeconds` | numbers | Timer state (see [timer](timer.md)). |
| `stats` | object | Per-difficulty results (`played`, `wins`, `losses`, `bestTime`), loaded from and saved to `localStorage` (see [statistics](statistics.md)). |
| `currentKey` | string \| null | Difficulty key of the game in progress. |

## Key functions

**Difficulty & setup**

- `buildDifficultyButtons` — builds the three dialog buttons from the difficulty config.
- `startGame(key)` — applies a difficulty, resets state, builds the board, resets the
  timer, renders, and closes the dialogs.
- `createCells` — (re)builds the DOM grid for the current dimensions.
- `updateCellSize` — computes and applies the auto-scaled cell size (see
  [difficulty](difficulty.md)).

**Board generation**

- `placeMines(safeR, safeC)` — places mines lazily, excluding the first-clicked cell.
- `computeAdjacents` — counts mines around each clear cell.
- `forEachNeighbor(r, c, cb)` — visits the up-to-8 in-bounds neighbours of a cell.

**Game actions**

- `revealCell(r, c)` — handles a left-click reveal.
- `chordReveal(r, c)` — handles a double-click chord on a numbered cell (GP-24 … GP-30).
- `floodFill(r, c)` — reveals connected blank regions (GP-9).
- `toggleFlag(r, c)` — handles a right-click flag toggle.
- `checkWin` — evaluates the exact-match win condition (GP-18).
- `lose(r, c)` / `win()` — enter the end states and show the result dialog.
- `endGame(kind)` — stops the timer, updates the board, and schedules the delayed result dialog (GP-31 … GP-33).

**Statistics**

- `loadStats` / `saveStats` — read and write the `minesweeper.stats.v1` `localStorage`
  entry, tolerating unavailable/corrupt storage.
- `recordGameStarted(key)` / `recordWin(key, seconds)` / `recordLoss(key)` — update the
  per-difficulty counters and best time.
- `statsText(key)` / `updateStatsLines()` — build and refresh the statistics lines on
  the difficulty buttons.

**Presentation & timer**

- `render` — syncs the DOM cells with the model.
- `showResult(kind)` / `closeResultDialog` — drive the result dialog.
- `openDifficultyDialog` / `closeDifficultyDialog` — drive the difficulty dialog.
- `formatTime` / `startTimer` / `stopTimer` / `updateTimerDisplay` — the timer.

## Algorithms

| ID | Requirement | Acceptance criteria |
|----|-------------|---------------------|
| AR-1 | Mine placement uses a partial Fisher–Yates shuffle to select `mineCount` distinct cells from all cells except the safe first cell. | Placement is uniform and produces distinct mine cells. |
| AR-2 | Flood fill is an iterative stack-based traversal (no recursion) that reveals reachable zero-adjacency cells and their numbered border, skipping flagged and mine cells. | Large blank regions are revealed without stack overflow. |
| AR-3 | Adjacency is computed as an 8-directional neighbour count. | Counts match a manual recount. |
| AR-4 | Rendering is a full pass over the grid that resets each cell's classes and content from the model. | The DOM always reflects the model after a change. |
| AR-5 | A chord reuses the normal reveal path for each neighbourhood cell it opens, so cells opened by a chord behave identically to manually revealed cells (including flood fill and the loss check). | Chorded cells reveal exactly as if clicked. |
| AR-6 | End-of-game presentation is centralised in an `endGame(kind)` helper: it renders the final board, stops the timer, disables the New game button, and schedules the result dialog with `setTimeout(..., RESULT_DELAY_MS)` using a named constant (~1000 ms). `startGame` clears any pending timeout and re-enables New game. | The board updates immediately; the dialog appears only after the delay; a stale timeout never fires over a new game or the difficulty dialog. |
| AR-7 | Statistics live in one versioned `localStorage` key (`minesweeper.stats.v1`) as JSON. Reads/writes are wrapped in `try/catch`, and loaded data is merged over defaults, so unavailable or corrupt storage degrades to empty statistics rather than breaking the game. | The game runs with zeroed stats when storage is blocked or invalid. |

## Event wiring

- One delegated `click` handler on the board performs reveals.
- One delegated `contextmenu` handler (with `preventDefault`) performs flag toggles.
- One delegated `dblclick` handler on the board performs chord reveals.
- Window `resize` recomputes the cell size.
- The difficulty buttons, `New game`, and `Play again` are wired to the dialog helpers.

All board input is guarded by `state === "playing"`, so no interaction is possible
before a game starts or after it ends.

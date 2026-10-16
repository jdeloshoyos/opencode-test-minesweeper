# Difficulty

**Purpose.** How board size and mine count are chosen, and how the board is scaled to
fit the screen.

**Status.** Current (source of truth).

## Levels

| Level | Key | Grid (`cols × rows`) | Mines | Description |
|-------|-----|----------------------|-------|-------------|
| Easy | `easy` | 10 × 10 | 10 | A gentle warm-up. |
| Intermediate | `intermediate` | 16 × 16 | 40 | A balanced challenge. |
| Hard | `hard` | 30 × 16 | 99 | Expert level. |

Notes:

- Grid dimensions are stated as `cols × rows` (width × height), matching the labels
  shown to the player. **Hard is a wide board: 30 columns by 16 rows.**

## Requirements

| ID | Requirement | Acceptance criteria |
|----|-------------|---------------------|
| DF-1 | The player chooses a difficulty before every game. | On load, and whenever a new game is started, the difficulty dialog is shown until a level is picked. |
| DF-2 | Choosing a level sets the board's rows, columns, and mine count to that level's values. | The resulting board matches the table above. |
| DF-3 | The current difficulty is displayed in the status bar. | The status bar shows the chosen level's label. |
| DF-4 | The three levels above are offered, each with its grid/mine summary as a description. | The difficulty dialog shows three buttons with the summaries above. |

## Auto-scaling

| ID | Requirement | Acceptance criteria |
|----|-------------|---------------------|
| DF-5 | The cell size scales so the whole board fits the viewport without scrolling. | Larger boards (notably Hard) render with smaller cells; the board fits within the window. |
| DF-6 | Cell size is clamped between a minimum of 16 px and a maximum of 34 px. | No cell is smaller than 16 px or larger than 34 px. |
| DF-7 | The number font size scales with the cell size (about 55% of the cell, with a 10 px floor). | Numbers remain legible at every cell size. |
| DF-8 | Scaling is recomputed when the window is resized. | Resizing the window re-fits the board. |

## Scaling formula

Cell size is derived from the available viewport area and the board dimensions, then
clamped:

```
availableWidth  = min(viewportWidth  − 48, 1200)
availableHeight = viewportHeight − 190

byWidth  = (availableWidth  − (cols − 1) × gap) / cols
byHeight = (availableHeight − (rows − 1) × gap) / rows

cellSize = floor(min(byWidth, byHeight))     // clamped to [16, 34]
cellFont = max(10, round(cellSize × 0.55))
```

`gap` is the 1 px gap between cells.

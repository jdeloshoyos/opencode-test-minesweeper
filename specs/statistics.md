# Statistics

**Purpose.** Per-difficulty results that persist between sessions using the browser's
`localStorage` (no backend).

**Status.** Current (source of truth).

## Stored data

A single `localStorage` key, `minesweeper.stats.v1`, holds JSON keyed by difficulty:

```json
{
  "easy":         { "played": 36, "wins": 15, "losses": 5,  "bestTime": 313 },
  "intermediate": { "played": 0,  "wins": 0,  "losses": 0,  "bestTime": null },
  "hard":         { "played": 0,  "wins": 0,  "losses": 0,  "bestTime": null }
}
```

- `played` — games started for that difficulty.
- `wins` — games won.
- `losses` — games lost.
- `bestTime` — the fastest winning time in whole seconds, or `null` if there has been
  no win yet.

## Recording rules

| ID | Requirement | Acceptance criteria |
|----|-------------|---------------------|
| ST-1 | Statistics are kept independently for each difficulty. | Easy, Intermediate and Hard each have their own counters and best time. |
| ST-2 | `played` increments when a game starts — that is, when a difficulty is chosen and the board is built — not on the first move. | Starting a game raises `played` immediately; abandoning it without a move still counts as played. |
| ST-3 | `wins` increments once when a game is won, and `losses` increments once when a game is lost. | Each finished game affects exactly one of the two counters, exactly once. |
| ST-4 | `bestTime` is updated on a win only, when the winning time is strictly lower than the stored best (or no best exists). Losses never change it. | The stored best is the minimum winning time; a slower or equal win leaves it unchanged. |
| ST-5 | Wins and losses may sum to less than `played`, because games abandoned before finishing are counted as played but not won or lost. | Abandoning a game raises `played` only. |

## Persistence

| ID | Requirement | Acceptance criteria |
|----|-------------|---------------------|
| ST-6 | Statistics are saved to `localStorage` after every change and loaded at startup, so they survive reloads and future visits. | Reloading the page shows the same counters and best times. |
| ST-7 | Storage access is failure-tolerant: if `localStorage` is unavailable, throws, or contains corrupt JSON, the app falls back to empty statistics and continues normally. | The game still runs with zeroed stats when storage cannot be read or parsed. |
| ST-8 | Loaded statistics are merged with defaults, so missing or malformed fields fall back to zero / no best time. | A partial or partially invalid blob never breaks the app. |

## Display

| ID | Requirement | Acceptance criteria |
|----|-------------|---------------------|
| ST-9 | Each difficulty button shows a statistics line: `<n> games played, <n> wins (<p>%), <n> losses (<p>%) - best time: <HH:MM:SS>`. Singular/plural is respected (`1 game played`, `1 win`, `1 loss`). | The line matches the format, e.g. `4 games played, 2 wins (50%), 1 loss (25%) - best time: 00:01:32`. |
| ST-10 | When a difficulty has no win yet, the best-time segment shows `--`. | The line reads `… - best time: --`. |
| ST-11 | The statistics lines are refreshed each time the difficulty dialog opens, so they reflect the current session. | After completing games, reopening the dialog shows updated values. |
| ST-12 | On a winning game, the result dialog shows a dedicated **"New best time!"** line when that win sets or improves the difficulty's best time; the line is absent otherwise (including on losses). | First wins and record-breaking wins show the line; slower wins and losses do not. |
| ST-13 | Win and loss percentages are relative to games played: `wins% = wins / played` and `losses% = losses / played`. | With 4 played, 2 wins and 1 loss, the line shows `2 wins (50%)` and `1 loss (25%)`. |
| ST-14 | Percentages show at most one decimal place, rounded half-up; an integer result omits the decimal (`50%`, not `50.0%`), and a zero denominator shows `0%`. | `2/3` renders `66.7%`, `2/4` renders `50%`, and zero games played renders `0%`. |

Because `played` includes abandoned games, `wins% + losses%` can be less than 100%.

## Notes

- Statistics are **per browser/device**, since `localStorage` is not synchronized.
- Clearing site data resets the statistics.

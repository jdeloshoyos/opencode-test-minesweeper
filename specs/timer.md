# Timer

**Purpose.** The elapsed game-time counter shown in the status bar.

**Status.** Current (source of truth).

| ID | Requirement | Acceptance criteria |
|----|-------------|---------------------|
| TM-1 | The timer displays elapsed game time in `HH:MM:SS` format, zero-padded (e.g. `00:00:00`, `00:01:23`, `01:01:01`). | The displayed text always matches `HH:MM:SS`. |
| TM-2 | The timer shows `00:00:00` before the game begins. | On load and immediately after a new game starts, the display is `00:00:00`. |
| TM-3 | The timer starts on the player's first move — either the first reveal or the first flag toggle. | The timer begins running only after the first move; idle time before that is not counted. |
| TM-4 | While running, the timer advances in whole seconds, measured against a wall-clock baseline so it does not drift. | The displayed seconds track real elapsed time accurately. |
| TM-5 | The timer stops when the game ends (win or loss) and holds the final time. | After the game ends, the displayed time no longer changes. |
| TM-6 | Starting a new game resets the timer to `00:00:00` and stops it. | A new game shows `00:00:00` and the timer is not running until the next first move. |
| TM-7 | The timer is shown directly beside the mines-left counter in the status bar, labelled "Time". | Both counters appear together at the top. |

## Behaviour notes

- The underlying interval samples roughly every 200 ms and recomputes
  `elapsedSeconds = floor((now − startTime) / 1000)`, so the display reflects whole
  seconds without accumulating drift.
- The final time is captured for display in the [result dialog](interface.md) (UI-8).

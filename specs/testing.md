# Testing

**Purpose.** Acceptance scenarios mapped to the requirements, plus the current
automated coverage.

**Status.** Current (source of truth).

## Manual acceptance scenarios

| ID | Scenario | Expected result | Requirements |
|----|----------|-----------------|--------------|
| TS-1 | Load the app. | The difficulty dialog is shown; no board input is possible yet. | DF-1, UI-5, UI-11 |
| TS-2 | Choose **Easy**. | A 10 × 10 board appears; mines-left shows 10; timer shows `00:00:00`. | DF-2, DF-3, UI-2, TM-2 |
| TS-3 | Left-click any cell as the first move. | The cell is never a mine; the timer starts; blank regions flood-fill; numbered borders show correct counts. | GP-9, GP-10, GP-21, GP-22, TM-3 |
| TS-4 | Right-click a covered cell. | A flag appears and mines-left decreases; right-click again removes it and the counter increases. | GP-11, GP-14, UI-13 |
| TS-5 | Right-click the board repeatedly. | No browser context menu appears. | GP-13 |
| TS-6 | Left-click a mine. | Loss: the result dialog shows "Game over" (red) with difficulty + time; all mines are revealed; the hit mine is highlighted; wrong flags are marked. | GP-15, GP-16, GP-17, UI-6, UI-7, UI-8, UI-14, UI-15 |
| TS-7 | Flag exactly the mines. | Win: the result dialog shows "You won!" (green) with difficulty + time. | GP-18, UI-6, UI-7 |
| TS-8 | Flag a clear cell, then flag every mine. | No win until the wrong flag is removed; removing it wins. | GP-18 |
| TS-9 | Click **Play again** in the result dialog. | The result dialog closes and the difficulty dialog opens. | UI-9, UI-12 |
| TS-10 | Click **New game** mid-game. | The difficulty dialog opens; choosing a level starts a fresh board and resets the timer. | UI-4, DF-1, TM-6 |
| TS-11 | Choose **Hard**. | A 30 × 16 board with 99 mines fits the window via smaller cells. | DF-2, DF-5, DF-6 |
| TS-12 | Resize the window. | The board re-fits; cells stay within 16–34 px. | DF-8 |
| TS-13 | After a win/loss, click and right-click the board. | No changes occur. | GP-20 |
| TS-14 | Press Escape or click the backdrop while a dialog is open. | The dialog stays open. | UI-11 |
| TS-15 | Double-click a revealed numbered cell whose adjacent flags equal its number. | The adjacent covered, unflagged cells are all revealed. | GP-24, GP-25, GP-26 |
| TS-16 | Double-click a revealed numbered cell whose adjacent flags do **not** equal its number. | Nothing happens; the board is unchanged. | GP-25 |
| TS-17 | With a wrong flag leaving a neighbouring mine unflagged, chord the cell so the unflagged mine is opened. | Loss occurs, exactly as if the mine had been clicked. | GP-28, UI-6 |
| TS-18 | Double-click a covered cell, a revealed blank cell, or any cell after the game has ended. | Nothing happens. | GP-29 |
| TS-19 | Chord a numbered cell that has a flagged neighbour. | The flagged neighbour stays covered and flagged; the unflagged neighbours are revealed. | GP-27 |
| TS-20 | Left-click a mine. | The board updates immediately: all mines are revealed, the hit mine is highlighted, and wrong flags are marked — all before any dialog appears. | GP-31 |
| TS-21 | Observe the moment the game ends and the following second. | The result dialog is not shown at the instant the game ends; it appears about one second later. | GP-32 |
| TS-22 | Watch the timer across the end of the game. | The timer is frozen from the instant the game ends, including during the pause before the dialog. | GP-33, TM-5 |
| TS-23 | Press **New game** during the one-second pause. | Nothing happens — the difficulty dialog does not open. After the result dialog is dismissed and a new game starts, New game works again. | UI-20 |
| TS-24 | Open the app for the first time (empty storage) and view the difficulty dialog. | Each difficulty shows `0 games played, 0 wins (0%), 0 losses (0%) - best time: --`. | ST-9, ST-10, ST-14 |
| TS-25 | Start a game, abandon it with New game, then win one and lose one. | `played` counts all three; only the finished games add a win or a loss, so wins + losses may be less than played. | ST-2, ST-3, ST-5 |
| TS-26 | Win a game, then win again more slowly, then beat the record. | Best time is set on the first win; the slower win does not change it; the faster win updates it. The "New best time!" line shows only for the first and record-breaking wins. | ST-4, ST-12 |
| TS-27 | Reload the page after playing some games. | The counters and best times are unchanged; they persist across reloads. | ST-6 |
| TS-28 | With 4 games played (2 wins, 1 loss), view the difficulty line; then check a ratio like 2 wins / 3 played. | The line reads `4 games played, 2 wins (50%), 1 loss (25%) - best time: …`, and the 2/3 case renders `66.7%` (one decimal, half-up; integers drop the decimal). | ST-9, ST-13, ST-14 |

## Automated coverage

A headless Node harness runs `script.js` against a minimal DOM stub and asserts:

- **All three difficulties:** correct `rows × cols` board and DOM grid, exact mine
  count, mine count shown in the counter, difficulty label set, dialog closed on
  start, fresh state, timer reset.
- **Gameplay:** safe first click; flood fill never reveals a mine and is closed around
  blanks; adjacency counts match an independent recount; exact-match win when all
  mines are flagged; a wrong flag blocks the win; loss reveals all mines and marks the
  hit one; input is ignored after the game ends.
- **Timer:** `HH:MM:SS` formatting for several values; reset on new game; running after
  the first move; stopped on win and loss.
- **Dialogs:** difficulty dialog open on load with three buttons; result dialog hidden
  on load and while playing; shown with the correct title/colour and a summary
  containing the difficulty and time on win and loss; Play again hides the result and
  opens the difficulty dialog; `startGame` hides both.
- **Chord (double-click):** fires only when adjacent flags equal the number; opens all
  adjacent covered, unflagged cells and leaves flagged neighbours untouched; a chord
  over correct flags does not lose; chording onto an unflagged mine loses; nothing
  happens on a covered cell, a blank cell, or after the game has ended; flags and the
  counter are unchanged; verified across all three difficulties.
- **End-of-game delay:** on win and loss the dialog is not shown synchronously (hidden
  at t=0) and appears only after the delay; the board is updated immediately (all mines
  revealed, hit mine marked); the timer is stopped at the end condition; the New game
  button is disabled during the pause and re-enabled on a new game; a pending dialog is
  cleared if a new game starts first; board input is ignored during the pause.
- **Statistics:** counters accumulate correctly (played on start, wins/losses on end);
  best time keeps the lowest winning time; "new best" is true only for first/improving
  wins; the stats-line text matches the expected format including `--` when there is no
  best; percentages are `count / played`, rounded half-up to at most one decimal with
  integers shown without a decimal and `0%` for zero games; stats are written to and read
  from `localStorage` (with corrupt data falling back to empty); the difficulty stats
  lines refresh when the dialog opens.

## Not covered by automation

- Visual fit/auto-scaling and styling (verified manually in a browser).
- Native context-menu suppression and real user input flows (including the actual
  double-click gesture).

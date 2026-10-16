# Gameplay

**Purpose.** The core rules and mechanics of the game.

**Status.** Current (source of truth).

## Board and cells

| ID | Requirement | Acceptance criteria |
|----|-------------|---------------------|
| GP-1 | The board is a rectangular grid of `rows × cols` covered cells, where the dimensions and mine count are set by the chosen [difficulty](difficulty.md). | After a difficulty is chosen, the board has exactly `rows` rows and `cols` columns. |
| GP-2 | Every cell starts covered and unflagged. | On a new game, no cell is revealed and no cell is flagged. |
| GP-3 | Each cell is in exactly one of three states: covered, revealed, or flagged. | The rendered cell reflects one of these states only. |
| GP-4 | Each cell tracks whether it holds a mine and how many of its neighbours hold mines. | Revealed clear cells can report their adjacent-mine count. |

## Revealing (left click)

| ID | Requirement | Acceptance criteria |
|----|-------------|---------------------|
| GP-5 | Left-clicking a covered, unflagged cell reveals it. | The clicked cell becomes revealed. |
| GP-6 | Left-clicking is ignored on an already-revealed cell, a flagged cell, or after the game has ended. | Board state does not change. |
| GP-7 | If the revealed cell contains a mine, the game is lost. | The game ends with the loss outcome (GP-15). |
| GP-8 | If the revealed cell does not contain a mine, it is revealed as cleared. | The cell shows either blank (zero adjacent mines) or its adjacent-mine number. |
| GP-9 | When a cleared cell with zero adjacent mines is revealed, the game flood-fills: it reveals all reachable zero-adjacency cells and the numbered cells bordering them. | No zero-adjacency cell remains covered within a contiguous blank region; numbered border cells are revealed; flagged cells are never revealed; mines are never revealed. |
| GP-10 | A revealed cleared cell that has one or more adjacent mines shows the count of adjacent mines (vertical, horizontal, or diagonal). | The displayed number equals the number of mines among the cell's up-to-8 neighbours. |

## Flagging (right click)

| ID | Requirement | Acceptance criteria |
|----|-------------|---------------------|
| GP-11 | Right-clicking a covered cell toggles a flag on it. | The cell shows a flag when flagged, and stops showing it when unflagged. |
| GP-12 | Flagging is ignored on a revealed cell or after the game has ended. | Board state does not change. |
| GP-13 | The browser's native context menu is suppressed over the board. | Right-clicking the board opens no context menu. |
| GP-14 | The "mines left" counter shows `mineCount − flaggedCount`. | Flagging decrements the counter; unflagging increments it. |

## Outcomes

| ID | Requirement | Acceptance criteria |
|----|-------------|---------------------|
| GP-15 | Loss occurs when a cell containing a mine is revealed. | The game enters the loss state. |
| GP-16 | On loss, all mines are revealed and the mine that was hit is highlighted distinctly. | Every mine cell becomes revealed; the hit mine has a distinct style. |
| GP-17 | On loss, incorrectly flagged clear cells are marked as wrong. | A flagged, non-mine cell is shown as a wrong flag rather than a normal flag. |
| GP-18 | The win condition is an **exact match**: every mine must be flagged and no non-mine cell may be flagged. | The game is won only when the set of flagged cells equals the set of mine cells. A wrong flag prevents a win until removed. |
| GP-19 | The win condition is evaluated after each reveal and each flag toggle, once mines have been placed. | Winning triggers immediately when the condition is met. |
| GP-20 | After the game ends (win or loss), further board input is ignored. | Clicking and right-clicking produce no changes. |

## Safe first click

| ID | Requirement | Acceptance criteria |
|----|-------------|---------------------|
| GP-21 | The first reveal is always safe: the player never hits a mine on their first left-click. | The first left-clicked cell never contains a mine. |
| GP-22 | Mines are placed lazily, at the moment of the first reveal, excluding the first-clicked cell. | Before the first reveal no mines exist on the board; afterwards exactly `mineCount` mines are placed and the first-clicked cell is clear. |
| GP-23 | Mine placement selects `mineCount` distinct cells uniformly at random from all cells except the first-clicked one. | The placed mines are distinct and exclude the first-clicked cell. |

## Chord reveal (double-click)

A chord opens the neighbourhood of a numbered cell the player has already deduced.

| ID | Requirement | Acceptance criteria |
|----|-------------|---------------------|
| GP-24 | A chord is triggered by double-left-clicking a revealed cell that shows a number (adjacent mines > 0). | Double-clicking such a cell is recognised as a chord request; double-clicking other cells is not. |
| GP-25 | A chord fires only when the number of adjacent flagged cells equals the cell's number. If they do not match, the chord does nothing. | With `flags == number` the chord proceeds; otherwise the board is unchanged. |
| GP-26 | When a chord fires, it reveals every adjacent cell that is still covered and unflagged. | All adjacent covered, unflagged cells become revealed. |
| GP-27 | Adjacent flagged cells are never revealed by a chord. | Flagged neighbours remain covered and flagged. |
| GP-28 | Each cell opened by a chord behaves exactly as a normal reveal: an unflagged mine ends the game (loss); a clear cell is revealed with its count; a zero-adjacency cell triggers flood fill (GP-9). | Chording onto an unflagged mine causes a loss; clear cells reveal exactly as if clicked. |
| GP-29 | A chord has no effect on a covered cell, a revealed blank (zero-adjacency) cell, or after the game has ended. | Nothing changes in those cases. |
| GP-30 | A chord changes no flags and does not affect the mines-left counter. | Flags and the counter are unchanged by a chord. A chord can open an unflagged mine and cause a loss; because it leaves flags unchanged, it cannot by itself produce a win. |

## End-of-game presentation

| ID | Requirement | Acceptance criteria |
|----|-------------|---------------------|
| GP-31 | When the game ends, the board is updated to show the outcome immediately (on loss: all mines revealed, the hit mine highlighted, wrong flags marked), before the result dialog appears. | The final board state is visible at the moment the game ends, ahead of the dialog. |
| GP-32 | The result dialog is shown only after a short delay (about 1 second) following the end, so the player can see the final board. | The dialog is not shown synchronously at the end; it appears once the delay elapses. |
| GP-33 | The timer stops the moment the end condition is triggered, regardless of the dialog delay. | The displayed time is frozen from the instant the game ends, including during the delay. |

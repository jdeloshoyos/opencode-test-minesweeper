# Interface

**Purpose.** The layout, controls, dialogs, styling, and accessibility of the app.

**Status.** Current (source of truth).

## Layout

| ID | Requirement | Acceptance criteria |
|----|-------------|---------------------|
| UI-1 | The top bar shows the app title and a **New game** button. | Both are visible while playing. |
| UI-2 | The status bar shows, left to right: the mines-left counter, the timer, and the current difficulty label. | All three are shown, with the timer directly beside the mines-left counter. |
| UI-3 | The board is rendered as a grid of square cells with a 1 px gap and a subtle border/padding. | Cells appear as a uniform grid. |
| UI-4 | The **New game** button starts a new game by reopening the difficulty dialog. | Clicking it shows the difficulty dialog. |

## Dialogs

Both dialogs share the same card appearance (`.dialog`) over a blurred, full-screen
backdrop (`.overlay`).

| ID | Requirement | Acceptance criteria |
|----|-------------|---------------------|
| UI-5 | The **difficulty dialog** is a modal over the play area with a title, a short intro, and three difficulty buttons (label + description). | On load it is shown; it lists all three levels. |
| UI-6 | The **result dialog** is a modal with the same appearance as the difficulty dialog, containing the outcome message, a summary line, and a **Play again** button. | On win/loss it appears with the correct message and a working Play again button. |
| UI-7 | The result message reads "You won!" on a win and "Game over" on a loss, styled green and red respectively. | Message text and colour match the outcome. |
| UI-8 | The result summary line shows the difficulty and the final elapsed time (e.g. `Hard · 00:01:23`). | The summary contains the level label and an `HH:MM:SS` time. |
| UI-9 | **Play again** dismisses the result dialog and returns to the difficulty dialog. | A difficulty is chosen before the next game. |
| UI-10 | The backdrop is a semi-transparent dark overlay that blurs what is behind it. | The play area behind a dialog is dimmed and blurred. |
| UI-11 | Dialogs are modal and non-dismissable: they cannot be closed by clicking the backdrop or pressing Escape. | The only way to proceed is the dialog's buttons. |
| UI-12 | Only one dialog is visible at a time. | Starting a game hides both; opening one hides the other. |

## Cell visuals

| ID | Requirement | Acceptance criteria |
|----|-------------|---------------------|
| UI-13 | A flagged cell shows a flag glyph (🚩). | Flagged cells display a flag. |
| UI-14 | A revealed mine shows a bomb glyph (💣); the mine that was hit is highlighted in red. | All mines are visible on loss; the hit one is distinct. |
| UI-15 | A wrong flag (on a clear cell, after a loss) is shown distinctly (❌). | Incorrect flags are marked. |
| UI-16 | Adjacent-mine numbers use the classic colour scheme (1 blue, 2 green, 3 red, … up to 8). | Each number 1–8 has its own colour. |

## Accessibility

| ID | Requirement | Acceptance criteria |
|----|-------------|---------------------|
| UI-17 | Each dialog is exposed as a modal dialog to assistive technology. | Dialogs use `role="dialog"`, `aria-modal="true"`, and an `aria-labelledby` pointing at the title. |
| UI-18 | Board interactions use native click and context-menu events, and text selection is disabled on the page. | Text is not accidentally selected while playing. |
| UI-19 | A chord is requested by double-left-clicking a revealed numbered cell. A single left-click on an already-revealed cell is ignored, so the two gestures do not conflict. | Double-clicking a numbered cell issues a chord; single-clicking revealed cells has no effect. The gesture is mouse-only (double-tap on touch devices is out of scope). |
| UI-20 | After the game ends, the result dialog appears ~1 second later. During that pause the board is non-interactive and the **New game** button is disabled, so the difficulty dialog cannot be opened until the result dialog has been shown and dismissed. The button is re-enabled when the next game starts. | The final board is visible for about a second before the dialog; clicking New game during the pause does nothing; New game works again once a new game is under way. |
| UI-21 | Each difficulty button shows a statistics line under its description: games played, wins, losses, and best time (see [statistics](statistics.md)). | The line is present on all three buttons and refreshes whenever the dialog opens. |
| UI-22 | The result dialog can show a green **"New best time!"** line on a winning game that sets or improves the record; it is hidden otherwise. | The line appears on a new best and is absent on losses or non-best wins. |

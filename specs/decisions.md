# Decisions

**Purpose.** A log of significant design decisions and where the implemented game
diverges from the original brief. This is a record of *why*, not a behavioural spec.

**Status.** Current (source of truth for rationale).

| ID | Decision | Rationale | Status |
|----|----------|-----------|--------|
| DC-1 | **Win = exact match.** The game is won only when every mine is flagged *and* no non-mine cell is flagged. | Matches the brief's "all mines are correctly marked"; a wrong flag must not count as correct. | Current |
| DC-2 | **Safe first click.** Mines are placed lazily on the first reveal, excluding that cell. | Guarantees the player never loses on their opening click. | Current |
| DC-3 | **Reveal all mines on loss**, highlighting the hit mine and marking wrong flags. | Standard Minesweeper feedback; makes the loss understandable. | Current |
| DC-4 | **Three files** (`index.html`, `style.css`, `script.js`). | Clean separation while remaining runnable by opening a single file. | Current |
| DC-5 | **Difficulty selection supersedes the original fixed board.** The original brief specified a fixed 15 × 15 grid with 15 mines; this is replaced by Easy/Intermediate/Hard. | Requested enhancement; the fixed size was an initial simplification. | Supersedes original |
| DC-6 | **Timer starts on the first move** (reveal or flag), not when the board appears. | Idle time before play should not count as game time. | Current |
| DC-7 | **Auto-scaled cells** to fit the viewport (16–34 px). | The Hard board (30 × 16) is far larger than the original fixed board and would otherwise overflow. | Current |
| DC-8 | **Outcome shown in a modal dialog** with the message, a summary (difficulty + time), and Play again. | Requested enhancement; replaces the earlier inline status message and standalone button. | Supersedes earlier UI |
| DC-9 | **Dialogs are modal and non-dismissable.** Only their buttons advance the flow. | A difficulty/outcome must be acknowledged; avoids an undefined state. | Current |
| DC-10 | **Flagging is a right-click toggle**, with the native context menu suppressed. | Standard Minesweeper control scheme. | Current |
| DC-11 | **The result dialog reuses the difficulty dialog's styling** (same card over a blurred backdrop). | Visual consistency, as requested. | Current |
| DC-12 | **Chording is gated on the flag count matching the cell's number** (classic behaviour), and is requested by double-clicking a numbered cell. | Requested feature. The initial description implied an unconditional chord (open unflagged neighbours whenever double-clicked); gating on `flags == number` is the standard Minesweeper rule and was chosen to require the player to have correctly flagged the cell first. | Current |
| DC-13 | **The result dialog is delayed by about a second** after the game ends, while the board is updated immediately (mines revealed on loss). The timer still stops at the moment the game ends, and **New game is disabled during the pause**. | So the player can see what happened (e.g. the mine they hit) before the modal covers it. Blocking New game during the pause keeps the flow deterministic — the result is always acknowledged before a new game can begin. | Current |
| DC-14 | **Results are kept in `localStorage`** (one versioned key) rather than any backend. `played` counts boards started, so abandoned games raise `played` without a win/loss; the best time is the lowest winning time. | Simplest persistence with no server. The trade-off is that stats are per-browser/device and reset when site data is cleared. Counting "played" on start (rather than first move) matches the requested "games started" wording and intentionally allows `wins + losses < played`. | Current |

## Superseded requirements timeline

1. **Original brief:** fixed 15 × 15 grid, 15 mines, no difficulty selection.
2. **Enhancement:** added an elapsed-time counter (`HH:MM:SS`).
3. **Enhancement (DC-5):** introduced difficulty levels, replacing the fixed 15 × 15
   board; added auto-scaling (DC-7) and a New-game affordance.
4. **Enhancement (DC-8):** moved the win/lose outcome into a result dialog, removing
   the inline status message and the standalone Play again button.
5. **Enhancement (DC-12):** added a double-click chord reveal, gated on the adjacent
   flag count matching the cell's number.
6. **Enhancement (DC-13):** added a ~1-second pause before the result dialog so the
   final board (revealed mines) is visible; the timer still stops immediately.
7. **Enhancement (DC-14):** added per-difficulty results persisted in `localStorage`,
   with stats shown on the difficulty buttons and a "new best time" notice on wins.
8. **This document set:** specs folder added as the source of truth for the above.

# Quadtris — Backlog

Ideas and features to build later. Add new items to the top of the list.
Effort tags: **[S]** small, **[M]** medium, **[L]** large.

*(Backlog is currently empty — everything below is shipped.)*

## Done

### Gameplay

- ✅ **Back-to-back bonus.** Consecutive "difficult" clears (multi-ring or
  cascade, with no plain single-ring clear in between) score ×1.5, with a
  BACK-TO-BACK banner.

- ✅ **More field sizes.** Added 19×19 (hectic) and 43×43 (huge);
  `GRID_SIZES` is now `[19, 25, 31, 37, 43]`.

- ✅ **Fatal-landing warning.** The ghost piece turns into a pulsing
  warn-colored outline whenever locking at the landing spot would end the
  run (disconnect or off-board), via `ghostFatal()`.

- ✅ **Soft-drop locks while grounded.** A fresh ↓ press during the
  lock-delay grace period commits the lock immediately (key repeat and
  touch soft-drop don't insta-lock).

- ✅ **Danger warning near the wall.** When the stack gets within 3 cells
  of any board edge, that edge shows a warn-colored gradient strip.

- ✅ **Game modes.** Start menu with **Classic**, **Zen** (no game over),
  **Sprint** (clear 10 rings, timed, best-time leaderboard), and **Daily**
  (date-seeded shared bag, fixed 31×31, shareable result string).

### Scoring & Progression

- ✅ **Speed-based score multiplier.** Chill ×0.8, Medium ×1, Brisk ×1.25,
  Fast ×1.5 — applied to all scoring via `addScore()` and shown next to
  the SCORE label.

- ✅ **Bomb-charge progress meter.** Pips (`●●○○○`) under the BOMB button
  and a `2/5` counter on the mobile label show progress to the next charge.

- ✅ **Save & resume runs.** The full run (grid, piece, bag, hold, score,
  bombs, RNG state, stats, elapsed time) auto-saves to localStorage on
  every lock and pause; the start menu offers RESUME RUN after a reload.

- ✅ **High score persistence.** Best score (best time for Sprint) stored
  per mode + field size; BEST shown in the header and "★ NEW BEST" on the
  game-over overlay.

- ✅ **Cascade combo bonus.** Chained clears within one settle multiply by
  chain depth (×2, ×3, …); longest chain is tracked in the run stats.

- ✅ **Perfect-clear bonus.** +1000 and a banner when a settle leaves the
  board empty except the core.

- ✅ **Drop scoring.** +2 per cell of hard-drop distance, +1 per
  soft-dropped cell.

- ✅ **End-of-run stats.** Game-over overlay shows pieces, holds, bombs
  used, longest chain, and time, plus a local top-10 table
  (score/rings/size/mode/date).

### UX & Interface

- ✅ **Explain the game over.** The overlay names the cause
  ("DISCONNECTED FROM CORE" / "HIT THE WALL") and the offending piece's
  cells are painted in the warn color on the final board.

- ✅ **Input buffering during animations.** The most recent
  move/rotate/hold pressed during a flash, collapse, or board spin is
  buffered and replayed when control returns.

- ✅ **Level-up feedback.** "LEVEL n" banner over the board plus a pulse
  on the LEVEL stat (level = 1 + rings/4).

- ✅ **Fullscreen toggle.** Buttons in the footer, the Tweaks panel, and
  the mobile HUD call `requestFullscreen()`.

- ✅ **Screen-reader basics.** An `aria-live` region announces clears,
  cascades, level-ups, and game over; glyph-only buttons have
  `aria-label`s; overlays are dialogs with initial focus.

- ✅ **Proper DAS/ARR movement.** In-game delayed auto shift (170ms delay,
  40ms repeat) driven by the rAF loop, replacing OS key-repeat; releasing
  one direction falls back to the other held key.

- ✅ **Ring outlines for all sizes.** All `N_RINGS` rings outlined faintly
  (no more hardcoded `[1,2,3,4]`); the incomplete ring closest to
  completion (≥75% filled) is highlighted in the accent color.

- ✅ **Restart access + size change on game over.** Two-tap RESTART button
  in the ACTIONS panel ("SURE?" confirm), RESTART/MENU on the pause
  overlay, and a field-size quick-pick on the game-over screen.

- ✅ **Auto-pause on tab blur.** `visibilitychange` and window blur pause
  the run and auto-save it; held movement keys are released on blur.

### Mobile

- ✅ **Swipe gestures.** Drag horizontally to move cell-by-cell, drag down
  to soft drop, flick down to hard drop, tap to rotate. Tweaks offers
  "Buttons + swipe" / "Buttons only" / "Swipe only" (the last hides the
  button bar).

### Tech & Code Health

- ✅ **Seeded PRNG.** mulberry32 threaded through game state
  (`S.rngState`), seeded from the date for Daily mode and randomly
  otherwise; enables deterministic tests and save/resume of the bag.

- ✅ **rAF-based game loop.** Single `requestAnimationFrame` loop with a
  delta-time accumulator drives gravity, lock delay, DAS/ARR, and the
  sprint clock; `setInterval` gravity and `scheduleLoop` are gone.

- ✅ **PWA + offline.** `manifest.webmanifest`, SVG + PNG icons, OG/meta
  tags, and a service worker (network-first for the shell so deploys show
  immediately, cache-first for fonts/icons). Installable from GitHub Pages.

- ✅ **Incremental rendering.** The board keeps one persistent div per
  cell and diffs a per-cell state key on update, mutating styles in place —
  no more full innerHTML rebuild of ~1000 cells per gravity tick.

- ✅ **Column-bomb escape mechanic.** The center-out rings + strict
  orthogonal-disconnect game-over make single-cell/diagonal holes easy to
  create and hard to escape. Rather than soften the death rule, add a bomb
  as a demolition/escape valve:
  - **Blast:** clears every non-core cell in one column (1 wide).
  - **Target:** the column under the current piece's pivot, shown with a
    faint aim strip.
  - **Acquisition:** key **B**, limited by charges — start with 3, earn +1
    every 5 rings cleared, capped at 5.
  - **After blast:** the board re-collapses toward the core (same
    center-attraction collapse, animated).
  - Exempt from the connectivity/game-over check; does not clear rings.

  Considered but not chosen: softening the death rule (8-connectivity /
  wall-only / grace), ~90% ring clears, monomino rescue piece, smarter
  collapse. Kept in reserve if the bomb alone isn't enough.

- ✅ **Show next piece on mobile.** The mobile layout drops the NEXT
  preview panel to make room for the touch controls — bring it back
  somewhere in the mobile view (e.g. a compact preview in the header).

  Implemented as a compact HUD in the header row (`HOLD` + `NEXT`
  preview tiles), shared with the new hold-piece feature.

- ✅ **Fix: rings completed by the cascade aren't cleared.** Only the ring
  completed by the piece that just locked is checked. If the
  center-attraction collapse afterward leaves another ring fully filled,
  it should also be detected and cleared — and since clearing it
  triggers another cascade, this needs to chain (re-check after every
  collapse until no ring is complete).

  Implemented via `settleRings()`, which re-checks the settled grid after
  every collapse and repeats clear+collapse until a pass finds no
  complete rings. The piece-lock score bonus is only awarded once, on
  the first pass.

- ✅ **Hold piece.** Like the original Tetris, let the player stash the
  current piece in a hold slot (once per piece, until it locks) and swap
  it back in later instead of playing the falling piece immediately.

  Bound to **C**, plus an ACTIONS-panel button and a touch-bar button on
  mobile. Resets on every fresh spawn.

- ✅ **Selectable field size at start.** Let the player pick the grid size
  before starting a run: 25x25, 31x31, or 37x37.

  A picker overlay shows on load (before the first piece spawns); the
  size can also be changed later from the Tweaks panel, which restarts
  the run at the new size.

- ✅ **Landing grace period (lock delay).** Like the original Tetris, don't
  lock a piece the instant it can't fall further — give it a brief grace
  period first, during which the player can rotate it once more before it
  settles.

  500ms grace period once a piece can't fall further; a successful
  move/rotate while grounded refreshes it once (capped, including across
  pause/resume, to prevent indefinite stalling).

- ✅ **Center-attraction collapse after a completed square.** When a square
  (ring) is completed and cleared, every remaining piece is attracted
  toward the central core following this rule:
  1. **Straight-line phase.** Any piece that is fully or partially in the
     same row or column as the core moves toward the core in a straight
     line (along that row/column) until it stops.
  2. **Diagonal phase.** Any piece left floating that is *not* fully or
     partially in the same row or column as the core moves diagonally
     toward the core until it becomes fully or partially aligned with the
     core's row or column.
  3. Once aligned, it then moves toward the core in a straight-line
     motion (as in phase 1).

  **Stopping condition:** a moving piece stops when it hits another filled
  cell (the central core also counts as solid).

  **Ordering:** process the pieces directly external to the cleared square
  first, then progressively outward (ring by ring). Within a given
  distance, resolve in clockwise order starting from the top (12 o'clock).

  This replaces / refines the current quadrant-based inward collapse.

  **Resolution:** per-phase — every piece completes the straight-line
  phase first, then every remaining off-axis piece completes the diagonal
  phase (two synchronized sweeps, each in the outward + clockwise-from-top
  order above).

  **Blocked diagonal:** if a diagonally-moving piece hits a filled cell
  before it aligns with the core's row/column, it stops there and stays
  off-axis (it may remain "floating" diagonally).

  **Animation:** the collapse must be animated (a fast animation), not an
  instant snap — pieces should be seen sliding toward the core along their
  straight and diagonal paths.

- ✅ **Game over on disconnected pieces reaching the bottom.** When a piece
  falls to the bottom (locks at the outer wall) without being connected —
  through a chain of filled cells — to the center core, it's game over.
  The intent: you must build *outward from the core*, so stacks that
  aren't anchored to the center are illegal and end the game.

# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project

Classic Tetris implemented in vanilla JavaScript with HTML5 Canvas and CSS. No dependencies, no build step, no package.json — three files (`index.html`, `style.css`, `game.js`) that run directly in a browser.

## Running the game

There is nothing to install or build.

```bash
open index.html              # macOS: open directly in browser
python3 -m http.server 8000  # or: serve locally and visit http://localhost:8000
```

There are no tests, lint configs, or package scripts in this repo.

## Architecture

All game logic lives in `game.js`, driven by a single `requestAnimationFrame` loop. Key pieces:

- **Board model**: `board` is a `ROWS × COLS` matrix; each cell is `0` (empty) or a color index `1–7` identifying a locked piece.
- **Pieces**: defined in `PIECES` as square matrices. Rotation is a transpose + row-reverse (`rotateCW`), not precomputed rotation states.
- **Collision** (`collide`): checks a shape against board bounds and already-locked cells.
- **Wall kicks** (`tryRotate`): on a blocked rotation, retries the same rotation shifted ±1/±2 columns before giving up.
- **Game loop** (`loop`): accumulates elapsed time (`dropAccum`) each frame; once it exceeds `dropInterval`, the current piece drops a row (or locks if it can't).
- **Locking + line clear**: `lockPiece` → `merge` (writes the piece into `board`) → `clearLines` (scans bottom-up, removes full rows, unshifts empty rows at top).
- **Scoring/level**: `LINE_SCORES = [0, 100, 300, 500, 800]` multiplied by `level`; hard drop awards 2 pts/cell, soft drop 1 pt/row. Level increments every 10 lines, which decreases `dropInterval` (`max(100, 1000 - (level-1)*90)` ms).
- **Ghost piece** (`ghostY`): projects the current piece straight down to its landing row, drawn at low alpha in `draw()`.
- **Rendering**: `draw()` redraws grid + locked board + ghost + current piece every frame onto the `#board` canvas; `drawNext()` renders the next-piece preview onto a separate canvas.

Flow: `init()` builds an empty board, seeds `next` via `randomPiece()`, calls `spawn()` to promote it to `current`, then starts `loop()`. If a freshly spawned piece immediately collides, `endGame()` fires and the Game Over overlay shows. All input (move/rotate/soft drop/hard drop/pause) is handled via `keydown` in `init()`.

## Tunable constants (top of `game.js`)

`COLS`, `ROWS`, `BLOCK` (cell pixel size), `COLORS`, `LINE_SCORES`, `dropInterval`. If `COLS`/`ROWS`/`BLOCK` change, update the `#board` canvas `width`/`height` in `index.html` to match (`COLS × BLOCK` by `ROWS × BLOCK`).

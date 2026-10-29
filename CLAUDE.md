# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project

Vanilla JS Tetris (HTML5 Canvas). No `package.json`, bundler, linter, or tests. UI text and README are in Spanish.

## Running

Open `index.html` directly, or serve statically: `python3 -m http.server 8000` and visit `http://localhost:8000`.

## Architecture

Three files: `index.html` (DOM, canvases), `style.css`, and `game.js` (all logic, loaded as a plain script with `'use strict'`; module-level `let` state, no classes).

- **Board**: `ROWS × COLS` matrix; `0` = empty, `1–7` = piece type, which indexes both `COLORS` and `PIECES` (index 0 is `null` in both).
- **Pieces**: square matrices (the cell value is the type index). Rotation is `rotateCW` (transpose + reverse); `tryRotate` applies simple kicks `[0,-1,1,-2,2]` horizontally only (not SRS).
- **Loop**: `loop(ts)` via `requestAnimationFrame` accumulates `dropAccum`, drops one row at `dropInterval`, and calls `draw()` each frame. Pause/game over use `cancelAnimationFrame(animId)`; resuming and `init()` restart the loop manually.
- **Lock flow**: `lockPiece()` → `merge()` → `clearLines()` → `spawn()`. `spawn()` calls `endGame()` if the new piece collides immediately. Scoring, level (every 10 lines) and `dropInterval = max(100, 1000 - (level-1)*90)` are updated in `clearLines()`.
- **Input**: single `keydown` listener (uses `e.code`); it ignores input while paused/game over and calls `updateHUD()` after every key.

## Gotchas

- Changing `COLS`, `ROWS`, or `BLOCK` requires updating `width`/`height` of `<canvas id="board">` in `index.html` (`COLS×BLOCK` by `ROWS×BLOCK`). The next-piece canvas is a fixed 120×120 (4×4 cells at 30px, hardcoded `NB` in `drawNext`).
- `index.html` element IDs (`board`, `next-canvas`, `score`, `lines`, `level`, `overlay`, `overlay-title`, `overlay-score`, `restart-btn`) are looked up at the top of `game.js`; renaming either side breaks the game.
- `init()` is both the startup and restart path (wired to the restart button), so any new state must be reset there.

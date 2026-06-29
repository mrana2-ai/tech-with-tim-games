# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project overview

Two standalone browser games — no build step, no dependencies, no server required. Each game is a single self-contained HTML file with embedded CSS and JS.

## Running the games

Open any file directly in a browser:
- `tictactoe.html` — Tic Tac Toe vs minimax AI
- `shooter.html` — Dead Zone top-down shooter

No `npm install`, no dev server, no compilation. Changes are visible immediately on browser refresh.

## Architecture

Both games follow the same pattern:

1. **Canvas setup** — full-window `<canvas>`, resizes on `window.resize`
2. **Input module** — `keys` object (keydown/keyup) + `mouse` object (mousemove/mousedown)
3. **Audio module** — Web Audio API; `initAudio()` must be called from a user gesture (click/keypress) due to browser autoplay policy. Music is a procedurally scheduled loop; SFX are one-shot oscillator nodes.
4. **Entity classes** — plain JS classes with `update(dt)` and `draw()` methods; `dt` is seconds since last frame, capped at 0.05 to prevent spiral-of-death on tab blur
5. **Scene state machine** — `scene` string (`'menu' | 'game' | 'gameover'`); the main `loop()` dispatches `update` + `draw` per scene each frame
6. **Main loop** — `requestAnimationFrame` with manual delta-time (`lastT` variable)

### shooter.html specifics

- `WaveManager` owns the enemy array and spawn queue. Phases: `banner → spawning → fighting → clear`, then loops via `advance()`.
- Collision is simple AABB/circle radius checks (`dist2` squared-distance, no physics library).
- Pixel-art sprites are drawn with `ctx.fillRect` calls at fixed pixel scales (S=2–4); each sprite function draws centered at origin and relies on `ctx.save/translate/rotate/restore` by the caller.
- Screen shake is a global (`shakeX/Y/shakeDur/shakeMag`); applied as a `ctx.translate` around the world-draw pass only, not the HUD.
- Particles are a global flat array; pooling is not used (count stays low enough).

## Git workflow

Remote: `https://github.com/mrana2-ai/tech-with-tim-games` (branch `main`).  
Commit and push after every meaningful change. Use descriptive commit messages — what changed and why, not just what file.

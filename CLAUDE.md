# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Overview

A single-file tic-tac-toe web game. Everything (HTML, CSS, JS) lives in `index.html`. There is no build step, package manager, linter, or test suite.

It is deployed via GitHub Pages from `main` (remote: `github.com/akashcan/tic-tac-toe`). The file must stay named `index.html` at the repo root so Pages serves it at the site root.

## Running

Open `index.html` directly in a browser, or serve the directory (e.g. `python -m http.server`) and visit `http://localhost:8000`. The only external dependency is Google Fonts.

## Architecture (`index.html` `<script>`)

- **State**: a single global object `S` (built by `fresh()`) holds `cells`, `mode` (`"cpu"` | `"2p"`), `level` (0–2 index into `LEVELS`), `score` `{X, O, D}`, `starter`, `turn`, `over`, `win`. All handlers mutate `S` and then call `render()`.
- **Render**: `render(rebuild)` syncs the DOM from `S`. `rebuild=true` recreates the nine `.cell` buttons (inserted before the `#strike` SVG so the overlay stays on top); `false` only updates disabled state, labels, scoreboard, status text, and the win strike line. `play()` writes the new mark's SVG into the cell itself before calling `render(false)`, so the draw animation runs only for the new mark.
- **CPU**: the human is always X and the CPU is always O. `cpuMove()` plays a random square with probability `[0.85, 0.35, 0][level]`; otherwise it runs a full `minimax` (depth-weighted scores, random tie-breaking). CPU turns run on a `setTimeout`. While one is pending, `boardEl.dataset.wait` is set, and click/keyboard handlers check it to block input.
- **Rounds**: `newRound(keepStarter)` alternates `S.starter` unless `keepStarter` is true. Switching mode, changing difficulty, or resetting the score zeroes the score and resets the starter to X.
- **Input**: keys 1–9 map to cells in numpad layout (7-8-9 is the top row), and `N` starts a new round.

## Styling conventions

- Colors are CSS custom properties on `:root`. Dark mode is defined twice: under `@media (prefers-color-scheme: dark)` guarded by `:root:not([data-theme="light"])`, and again under `:root[data-theme="dark"]`. Keep both blocks in sync when changing colors.
- Board grid lines, X/O marks, and the win strike are inline SVGs in a 300×300 (board) or 100×100 (cell) viewBox, animated with `stroke-dashoffset`. `prefers-reduced-motion` turns these animations off.

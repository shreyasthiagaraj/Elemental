# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Game Description
- ELEMENTAL is a top-down elemental combat arena game inspired by Avatar: The Last Airbender. Players wield four elements (Fire, Water, Earth, Air) to survive endless waves of enemies. The game features twin-stick controls, element switching with combo effects, wave-based progression with perk selection, and gorgeous particle effects. Mobile-first design with desktop support.

## Shell Commands
- Never chain commands when the `cd` command is involved, Use separate parallel Bash tool calls instead — they run concurrently and don't trigger permission prompts.

## Running the Game

```bash
npm start          # Starts server on port 3000 (or PORT env var)
```

Open `http://localhost:3000` in a browser. No build step — the game is a single `index.html` served statically.

## Architecture

**Single-file browser game** with a Node.js static file server.

- `index.html` — Complete game: HTML, CSS, and all JavaScript in one file. Canvas-based 2D renderer, arena generation, four-element combat system, enemy AI, particle effects, wave system, perk upgrades, mobile touch controls.
- `server.js` — HTTP server serving static files, plus WebSocket relay (available for future multiplayer).

## Game Architecture (inside index.html)

**Single-player arena combat** with wave-based progression.

**Key systems:**
- **Elements**: Four elements defined in `ELEMENTS` object (fire, water, earth, air). Each has unique projectile behavior, damage, speed, and visual effects.
- **Combo system**: Switching elements triggers combo effects defined in `COMBO_EFFECTS` (e.g., Fire→Water = Steam Burst). Six unique combo types: aoe_slow, zone_dot, aoe_burst, tornado.
- **Special abilities**: Defined in `SPECIALS`. Fire/Water = radial projectile burst, Earth = AOE stun, Air = pulling cyclone. Charged by dealing damage.
- **Enemy types**: Six types in `ENEMY_TYPES` with distinct behaviors: chase (slime), ranged (fireImp), charge (stoneBrute), erratic/teleport (windWisp), stealth (shadow). Elite variants every 5 waves.
- **Wave system**: Progressive difficulty scaling enemy count, HP, damage, and speed. New enemy types unlock at waves 2-6.
- **Perk system**: 16 perks in `PERKS` array, pick 1 of 3 between waves. Stackable up to 3 times. Effects applied via `getPerkBonus()` and `getElemDmgMult()`.
- **Arena**: Circular arena with stone pillars and 4 elemental shrines at cardinal positions.
- **Particles**: Pooled particle system (800 max) for trails, impacts, explosions, auras.
- **Mobile controls**: Dual virtual joysticks with touch-origin positioning. Element buttons, special button, dash button. Platform detected via user agent; CSS classes `mobile`/`desktop` toggle layouts.

## Development Notes

- No test suite or linter configured.
- All game logic is in a single `<script>` tag — search by function name (e.g., `fireProjectile`, `updateEnemies`, `switchElement`, `damageEnemy`).
- Syntax check: `node -e "new Function(require('fs').readFileSync('index.html','utf8').match(/<script>([\s\S]*)<\/script>/)[1])"` — validates JS without running the game.
- The game auto-detects mobile via user agent and exposes different control schemes and CSS layouts accordingly.
- Game state managed via global `gameState` variable: `menu`, `playing`, `dead`, `perks`, `waveIntro`.
- High score persisted to localStorage under key `elemental_highscore`.

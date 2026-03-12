# CLAUDE.md — VOID://ESCAPE

## Project Overview

**VOID://ESCAPE** is an interactive 3D escape room game built as a single self-contained HTML file. The player must solve a chain of puzzles to escape a sci-fi neural consciousness mapping experiment within a 10-minute time limit.

- **Repository**: `Yesthank/3descape`
- **Live file**: `index.html` (single monolithic file, ~874 lines)
- **No build step** — open `index.html` directly in a browser

## Tech Stack

- **Three.js v0.150.0** (CDN via jsDelivr) — 3D rendering, scene graph, materials
- **Vanilla JavaScript (ES6)** — no frameworks, no modules, no transpilation
- **Canvas API** — procedural texture generation (hex grid floor, wall patterns)
- **Web Audio API** — ambient sound, interactive tones, glitch effects
- **Inline CSS** — all styles embedded in `<style>` block

## File Structure

```
index.html          # Entire game (HTML + CSS + JS)
title-screen.png    # Screenshot: title screen
intro-screen.png    # Screenshot: intro narrative
game-room-3d.png   # Screenshot: in-game 3D room
1.txt               # Placeholder file
```

There is no `package.json`, no build config, no test framework, and no external dependencies besides Three.js via CDN.

## Code Architecture

The entire codebase lives inside `index.html` wrapped in an IIFE:

```
(function(){
  // ... all game code ...
})()
```

### Section Layout (in order)

| Section | Description |
|---------|-------------|
| `GAME STATE` | `GS` singleton object holding all mutable game state |
| `DOM REFS` | `$()` helper and cached element references |
| `AUDIO` | Web Audio context, tone generators, ambient sound |
| `THREE.JS SETUP` | Scene, camera, renderer, raycaster, fog |
| `TEXTURES` | `makeHexTex()`, `makeWallTex()` canvas procedural textures |
| `BUILD ROOM` | Floor, ceiling, walls, light strips |
| `BUILD OBJECTS` | Door, panels, desk, safe, artifacts, display cases |
| `LIGHTING` | Ambient, point, spot, and emergency lights |
| `PARTICLES` | Floating atmospheric dust particles |
| `UI HELPERS` | Notification popups, inventory display, modal management |
| `INTERACTION` | Raycasting, object click handlers, hover labels |
| `PUZZLE FRAMEWORK` | `openPuzzle()` dispatcher + 8 individual puzzle functions |
| `GAME FLOW` | Title screen, intro, timer, victory/gameover transitions |
| `GLITCH` | Random visual distortion effect |
| `ANIMATION LOOP` | `animate()` — main render loop with physics updates |
| `EVENTS` | Mouse/touch input, window resize |
| `INIT` | Startup calls: `buildRoom()`, `buildObjects()`, `buildLights()`, `buildParticles()`, `animate()` |

### Key Conventions

- **Variable naming**: Aggressively short — `$` for DOM lookup, `GS` for game state, single-letter params in helpers
- **Material factories**: `dark(color)` and `glow(color, emissive, intensity)` create `MeshStandardMaterial` instances
- **Interactive objects**: Set `mesh.userData = { interactive: true, label: "Name", action: "puzzleId" }`
- **Puzzle functions**: Named `pzWire`, `pzSafe`, `pzCipher`, `pzSimon`, `pzKeypad`, etc. — all render into a shared modal
- **No comments in code** — sections are delimited by `// ======= SECTION NAME =======` banners

## Game Phases

`title` → `intro` → `playing` → `victory` or `gameover`

## Puzzle Dependency Chain

```
Wire Puzzle → Power On → Terminal Unlock
Safe → Decryption Key
Terminal + Decryption Key → Cipher Puzzle
Cipher → Simon Says → Keycard
Keycard → Exit Keypad → Victory
```

Auxiliary: Mirror puzzle, Tablet (symbol reference), Note (document reading)

## 3D Room Layout

- **North wall (−Z)**: Emergency exit door, exit keypad, emergency light
- **East wall (+X)**: Electrical panel, glass display case with spinning artifact
- **South wall (+Z)**: Desk + terminal, project note, window with nebula view
- **West wall (−X)**: Bookshelf, mirror, wall safe
- **Center**: Pedestal with glowing orb (Simon Says trigger)

## Development Workflow

### Running the game

Open `index.html` in any modern browser with WebGL support. No server needed.

### Making changes

1. Edit `index.html` directly
2. Refresh browser to test
3. All game logic, styles, and markup are in this single file

### Testing

No automated tests. Test by playing through the puzzle chain in-browser:
1. Solve wire puzzle (match colored pairs) → power turns on
2. Open safe (code: `7391`) → get decryption key
3. Use terminal cipher (shift-13 Caesar) → unlock Simon Says
4. Complete Simon (3 rounds) → get keycard
5. Enter exit code (`820731`) → escape

### Browser Requirements

- WebGL (Three.js rendering)
- Web Audio API (sound effects)
- ES6 support (arrow functions, `const`/`let`, template literals)
- Touch events supported for mobile

## Guidelines for AI Assistants

- **Single-file architecture**: Do not split `index.html` into multiple files unless explicitly asked
- **Preserve minified style**: Keep variable names short and consistent with existing conventions (`GS`, `$`, single-letter params)
- **Section banners**: Maintain the `// ======= SECTION =======` comment structure when adding code
- **CDN dependency**: Three.js is loaded from `https://cdn.jsdelivr.net/npm/three@0.150.0/build/three.min.js` — do not bundle or inline it
- **No build tools**: This project intentionally has no build pipeline — do not introduce one
- **IIFE wrapper**: All JS must stay inside the existing IIFE
- **Test changes by describing browser behavior** — there are no automated tests to run
- **Puzzle solutions are hardcoded** — safe code `7391`, cipher shift `13`, exit code `820731`

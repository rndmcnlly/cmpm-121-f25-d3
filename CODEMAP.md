# CMPM 121 D3 Project - Code Map

## Project Overview

This is a **location-based game** built with TypeScript, Leaflet.js for mapping, and Vite as the build tool. The game displays an interactive map centered on UCSC campus where players can interact with "caches" that spawn in the neighborhood around a classroom location. Players accumulate points by clicking "poke" on cache popups.

## Technology Stack

| Technology | Purpose |
|------------|---------|
| **Deno** | JavaScript/TypeScript runtime |
| **Vite** | Build tool and dev server |
| **Leaflet** | Interactive map library |
| **TypeScript** | Type-safe JavaScript |
| **OpenStreetMap** | Map tile provider |

## Directory Structure

```
cmpm-121-f25-d3/
├── .devcontainer/          # VS Code Dev Container configuration
│   └── devcontainer.json   # Deno development environment setup
├── .githooks/              # Git hooks for quality checks
│   └── pre-commit          # Runs fmt, lint, check, build before commits
├── .github/                # GitHub configuration
│   └── copilot-instructions.md
├── .vscode/                # VS Code workspace settings
├── src/                    # Source code
│   ├── main.ts             # ⭐ Main application entry point
│   ├── style.css           # Page styling
│   ├── _leafletWorkaround.ts  # Fix for Leaflet marker images
│   └── _luck.ts            # Deterministic random number generator
├── index.html              # HTML entry point (minimal, don't modify)
├── deno.json               # Deno configuration & tasks
├── tsconfig.json           # TypeScript compiler options
├── vite.config.js          # Vite build configuration
├── setup-hooks.sh          # Script to enable Git hooks
└── README.md               # Project documentation
```

## Key Files

### `src/main.ts` - Main Application

The heart of the application. Contains:

- **UI Setup**: Creates `controlPanel`, `map`, and `statusPanel` div elements
- **Map Configuration**: Initializes Leaflet map centered at classroom coordinates
- **Player Marker**: Places a marker at the player's location
- **Cache System**: Spawns interactive cache rectangles based on `luck()` function
- **Game Logic**: Point collection via cache "poke" interactions

**Key Constants:**
```typescript
CLASSROOM_LATLNG    // Map center (36.997936, -122.057035)
GAMEPLAY_ZOOM_LEVEL // Fixed zoom level (19)
TILE_DEGREES        // Cache tile size (1e-4)
NEIGHBORHOOD_SIZE   // Spawn radius (8 tiles)
CACHE_SPAWN_PROBABILITY // 10% chance per tile
```

### `src/_luck.ts` - Deterministic Randomness ⚠️ DO NOT MODIFY

Provides the `luck(situation)` function that generates deterministic pseudo-random numbers (0-1) using MurmurHash. This ensures caches spawn at the same locations consistently.

### `src/_leafletWorkaround.ts` - Leaflet Fix ⚠️ DO NOT MODIFY

Fixes a known Leaflet bug where marker icons fail to load when using bundlers like Vite.

### `src/style.css` - Styling

Basic CSS for the three main UI panels:
- `#controlPanel` - Top panel (for future controls)
- `#map` - Map container (80vh height)
- `#statusPanel` - Bottom panel (displays points)

### `index.html` - HTML Entry ⚠️ MINIMIZE CHANGES

Minimal HTML that loads `src/main.ts`. Dynamic UI elements should be created programmatically in TypeScript rather than statically added to this HTML file.

## Configuration Files

### `deno.json` - Project Configuration

**Available Tasks:**
```bash
deno task dev      # Start development server
deno task build    # Build for production
deno task preview  # Preview production build
deno task check    # TypeScript type checking
deno task lint     # Run Deno linter
deno task fmt      # Format code
deno task fmt:check # Check formatting
deno task ci       # Full CI pipeline (fmt + lint + check + build)
```

**Dependencies (from `imports` in deno.json):**
- `leaflet` (^1.9.4) - Map library
- `@types/leaflet` (^1.9.21) - TypeScript types for Leaflet
- `murmur-32` (^1.0.0) - Hash function for deterministic randomness
- `geojson` (^0.5.0) - GeoJSON utilities
- `vite` (^7.1.12) - Build tool

### `vite.config.js` - Build Configuration

- Dev server runs on port 3000 with auto-open
- Production builds to `dist/` with source maps
- Base path configurable via `REPO_NAME` env variable

### `tsconfig.json` - TypeScript Configuration

Strict mode enabled with:
- `noUnusedLocals` / `noUnusedParameters`
- `noFallthroughCasesInSwitch`
- ES2020 target with DOM types

## Development Workflow

### Getting Started
```bash
# Install Deno if needed
# https://deno.land/

# Set up Git hooks (run once)
./setup-hooks.sh

# Start development server
deno task dev
```

### Pre-commit Checks

The `.githooks/pre-commit` hook automatically runs:
1. `deno fmt --check` - Code formatting
2. `deno lint` - Linting
3. `deno check` - Type checking
4. `deno task build` - Build verification

Bypass with `git commit --no-verify` (not recommended).

## Game Architecture

**Module Dependency Tree:**
```
index.html
    └── src/main.ts (entry point)
            ├── leaflet (map library)
            ├── style.css (page styles)
            ├── _leafletWorkaround.ts (marker icon fix)
            └── _luck.ts (deterministic random)
```

**UI Component Structure:**
```
document.body
    ├── #controlPanel (div) - Future game controls
    ├── #map (div) - Leaflet map container
    │       ├── Tile Layer (OpenStreetMap)
    │       ├── Player Marker
    │       └── Cache Rectangles (with popups)
    └── #statusPanel (div) - Points display
```

## Data Flow

1. **Map Initialization**: `main.ts` creates Leaflet map centered at classroom
2. **Cache Spawning**: Loops through neighborhood grid, uses `luck()` to determine spawn
3. **User Interaction**: Player clicks cache → popup shows → "poke" button clicked
4. **Point System**: Each poke decrements cache value, increments player points
5. **UI Update**: `statusPanel` displays accumulated points

## Files Students Should Modify

| File | Modification Level |
|------|-------------------|
| `src/main.ts` | ✅ Primary development file |
| `src/style.css` | ✅ Add custom styling |
| `README.md` | ✅ Replace with project documentation |
| `index.html` | ⚠️ Minimize changes |
| `src/_luck.ts` | ❌ Do not modify |
| `src/_leafletWorkaround.ts` | ❌ Do not modify |

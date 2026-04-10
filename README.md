# Stars Reborn — Design

**The authoritative developer reference for the Stars Reborn project.**

Read this repository before writing engine or UI code. Every game mechanic, data model decision, API contract, and architectural choice is documented here. When engine behavior or data structure is ambiguous, this repo is the answer — not the code.

---

## Purpose

Stars! (1995, Jeff Johnson & Jeff McBride) is a 4X strategy game with many interlocking systems, much of it reverse-engineered by the community over 30 years. This repository collects that knowledge, validates it against the original executable, and defines how each system will be implemented in the clone.

Documented here:

- Game mechanics (confirmed behavior of the original)
- Data model: structures, schemas, enumerations
- Engine API design: endpoint contracts, request/response shapes
- Architecture decisions and their rationale
- Development workflow: how to research, validate, and promote findings

---

## Repository Structure

```
stars-reborn-design/
└── docs/                  # Sphinx RST source — the primary content
    ├── index.rst
    ├── architecture.rst   # System architecture and repo relationships
    ├── mechanics/         # Game mechanics (one file per system)
    ├── schemas/           # Schema catalog (files live in stars-reborn-schemas)
    ├── gameplay/          # Player-facing gameplay documentation
    ├── reference/         # Lookup tables and constants
    ├── development/       # Dev workflow, tooling, contributing guide
    └── _build/            # Generated output (not checked in)
```

All content is Sphinx reStructuredText under `docs/`.

---

## Project Repository Structure

The Stars Reborn project spans six directories:

### `stars-reborn-design` (this repo)
Developer reference. Architecture decisions, game mechanics, data schemas, API contracts. **Read this first.**

### `stars-reborn-engine`
The Rust game engine. Exposes an HTTP API for all game operations. Authoritative source for runtime data model structures.

```
stars-reborn-engine/
├── engine/src/            # Rust source
│   ├── main.rs            # HTTP server entry point
│   ├── objects/           # Core data model types
│   ├── universe/          # Universe generation
│   ├── turn/              # Turn processing
│   ├── combat/            # Combat resolution
│   └── ai/                # AI player logic
└── legacy_rust/           # Prior implementation (reference only, not built)
```

API surfaces:
- `/model/` — read-only data model (tech tree, hull definitions, race traits, enumerations)
- `/game/` — stateful gameplay (create game, submit orders, process turn)

### `stars-reborn-ui`
The Python/PySide6 view layer. Pure HTTP client — no game logic, no independent copies of game data.

```
stars-reborn-ui/
├── src/
│   ├── main.py            # Entry point
│   ├── ui/                # UI components
│   └── legacy_ui/         # Prior UI iterations (reference only)
└── assets/
    └── images/            # Planet sprites and other art
```

All game data (tech tree, traits, components, valid ranges) is fetched from the engine's `/model/` endpoints at runtime.

### `stars-reborn-game`
Integration repository. Ties the engine and UI together for distribution.

```
stars-reborn-game/
├── README.md              # User-facing documentation
├── .github/workflows/     # CI/CD: builds engine + UI into .exe / AppImage
└── tests/                 # High-level integration tests
```

### `stars-reborn-schemas`
The JSON Schema definitions for all shared game objects and API contracts. Both
the engine and UI depend on this directly. Bundled into the app by
`stars-reborn-game` — no online connectivity required at runtime.

### `stars-reborn-research` (local only, not on GitHub)
Workspace for evaluating original game behavior — running the original executable, testing theories, analyzing binary formats. Open research questions are tracked here. Confirmed findings get documented in `stars-reborn-design`.

---

## How to Use This Repo

**Implementing a mechanic:** Read the relevant file in `docs/mechanics/` first. It defines the confirmed behavior and any known deviations from community documentation.

**Defining a data structure:** Check `docs/schemas/` for the catalog and design rationale. The actual schema files are in `stars-reborn-schemas`. Engine types and API response shapes derive from those definitions.

**Something is undocumented or ambiguous:** Research it in `stars-reborn-research`, validate against the original executable, then document the confirmed behavior here before implementing it.

**Building the docs:**

```bash
cd docs
pip install sphinx
make html
# Output: docs/_build/html/index.html
```

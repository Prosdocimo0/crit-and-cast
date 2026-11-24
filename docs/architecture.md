# Architecture

Crit & Cast is built in **Godot 4.7** with **typed GDScript**, using the GL Compatibility renderer so it can be exported to the web.

## The main rule

**The game rules never depend on visuals.**

- `core/` decides what happens.
- `scenes/` shows it.

The core can run without any scene open, which means it can be unit tested, the AI can simulate thousands of moves, and online play can reuse the same code later.

## Folder structure

```
crit-and-cast/
├── core/          # Game rules. Pure GDScript (RefCounted), no nodes
│   ├── effects/   # Small reusable effects: damage, heal, push, apply_status…
│   └── ai/        # Enemy decision-making
├── data/          # Content as Godot Resources (.tres)
│   ├── abilities/ # strike.tres, cleave.tres, fireball.tres…
│   ├── classes/   # barbarian.tres, mage.tres…
│   └── enemies/   # goblin.tres…
├── scenes/        # What the player sees. Nodes, sprites, tweens, UI
│   ├── battle/
│   ├── level_up/
│   ├── shop/
│   ├── main_menu/
│   └── ui/        # Shared widgets
├── autoload/      # Global singletons. Keep these few (e.g. run_manager.gd)
├── assets/        # Sprites, tilesets, sounds, fonts
├── tests/         # Unit tests for core/
└── docs/          # Documentation
```

Folders are created when the first file needs them.

## How a turn flows

```
Player clicks  →  scene builds an Action
               →  core: apply_action(state, action)  →  new state + list of events
               →  scene plays the events as animations
```

Example event list: `moved → rolled d20 (17) → hit → damage (8) → died`

- **Deterministic:** the same state plus the same action always gives the same result.
- **Seeded RNG:** the random seed lives inside the battle state, so any battle can be replayed.
- **Animations only show results.** They never change the game state.

## Content is data

Abilities, classes and enemies are **Resources** (`class_name Ability extends Resource` with `@export` fields), so new content is created in the editor's inspector.

An ability is a list of **effects**. Adding a new spell is usually just a new `.tres` file. Adding a new *effect type* is code, and each one unlocks many abilities.

## Who can depend on what

| Layer | Can use | Must not use |
|---|---|---|
| `core/` | other `core/` code, `data/` Resources | nodes, scenes, autoloads |
| `data/` | `core/` classes (types) | scenes |
| `scenes/` | `core/`, `data/`, `autoload/` | changing game state directly |
| `autoload/` | `core/`, `data/` | specific scenes |

## Conventions

- **Files and folders:** `snake_case` (`battle_state.gd`, `main_menu.tscn`)
- **Classes:** `PascalCase` with `class_name` (`BattleState`, `Ability`)
- **Typed GDScript everywhere:** `var hp: int = 10`, `func roll(sides: int) -> int:`
- **Nodes:** "call down, signal up." Parents call methods on children, and children emit signals to parents.
- **One scene, one script**, with the same name side by side (`unit_view.tscn` + `unit_view.gd`).

## Testing

- Unit tests for `core/` with GUT or gdUnit4 (to be chosen).
- Each new effect or rule gets a test.

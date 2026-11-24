# Git Workflow

## Branches

- **`main`** must always open in Godot and run without errors.
- Do work on short-lived branches and merge them into `main` when done:

| Prefix | Use for | Example |
|---|---|---|
| `feat/` | New features or content | `feat/barbarian-cleave` |
| `fix/` | Bug fixes | `fix/leap-through-walls` |
| `docs/` | Documentation | `docs/architecture` |
| `refactor/` | Restructuring, no behavior change | `refactor/effects-system` |
| `chore/` | Settings, tooling, CI | `chore/web-export` |

Small changes, like a typo or a number tweak, can go straight to `main`.

## Commit messages

Use [Conventional Commits](https://www.conventionalcommits.org/):

```
<type>(<scope>): <what changed, in the imperative>
```

Examples:

```
feat(core): add seeded d20 dice roller
feat(barbarian): add Cleave ability
fix(battle): prevent units from moving onto walls
test(core): cover critical hits on natural 20
docs: add architecture overview
chore: enable typed GDScript warnings
```

**Types:** `feat`, `fix`, `docs`, `test`, `refactor`, `perf`, `style`, `chore`, `ci`, `art` (assets)

**Scopes (optional):** `core`, `ai`, `battle`, `shop`, `level-up`, `ui`, or a class name like `barbarian` or `mage`.

Tips:
- One logical change per commit.
- Keep the subject line under ~70 characters. Add a body if the *why* isn't obvious.
- Small, frequent commits are better than one huge commit at the end of the week.

## Versions

[Semantic Versioning](https://semver.org/) starting at `0.x` while the game is in development. Each minor version is a playable milestone:

| Version | Milestone |
|---|---|
| `v0.1.0` | A Barbarian moves and attacks goblins on a grid |
| `v0.2.0` | Full battle: turns, enemy AI, win and lose |
| `v0.3.0` | A full run: level up, shop, multiple battles |
| `v0.4.0` | Mage class and spells |
| `v1.0.0` | First public release |

Bug-fix releases bump the last number (`v0.2.1`). Tag releases on `main`:

```bash
git tag -a v0.1.0 -m "Barbarian vs goblins"
git push origin v0.1.0
```

The version shown in the game is set in Project Settings → Application → Config → Version.

## What to commit in a Godot project

| Commit ✅ | Don't commit ❌ |
|---|---|
| `project.godot` | `.godot/` (editor cache, rebuilt automatically) |
| `.tscn`, `.tres`, `.gd` | Export builds (`/build`, `.exe`, web exports) |
| `.import` files | `export_credentials.cfg` (if it exists) |
| `.uid` files (Godot 4.4+, they keep references stable) | |
| `export_presets.cfg` (only if it contains no passwords) | |

## Godot-specific tips

- **Avoid editing the same scene on two branches.** `.tscn` files are text but merge badly.
- **Move or rename files inside the Godot editor**, not in the file manager, so references get updated.
- **Large binary assets** (big audio, many large images) may later need [Git LFS](https://git-lfs.com/). Not needed for small pixel art.

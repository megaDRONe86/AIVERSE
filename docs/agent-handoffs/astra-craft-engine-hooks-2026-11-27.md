# Generic hooks added for a portrait web game (branch `claude/project-thread-0y3pyf`), 2026-11-27

Four small, game-agnostic additions made while building an external data project (a portrait RTS that lives in its
own repository). Nothing game-specific entered the engine.

| Commit | Change | Paths | Documentation |
|---|---|---|---|
| `6e3700fd` | `game.json` `camera.bounds` `[xMin, zMin, xMax, zMax]`: the camera target stays in the box, also while dragging; equal min and max lock an axis | `engine/render/CameraBehavior.h`, `apps/client/GameClient.{h,cpp}` | `docs/game-format.md` (camera table) |
| `6e3700fd` | Web: mouse-up coordinates are relative to the canvas (buttons did not react when the canvas was not at the page corner) | `engine/platform/web/PlatformWeb.cpp` | none (bug fix) |
| `4680d6e6` | View scripts: `view.set_fog(bool)` switches the fog of war off for the local screen only (debug; the simulation is untouched) | `engine/view/game/GameViewScript.{h,cpp}`, `apps/client/GameClient.cpp` | `docs/scripting.md` |
| `ddf8a4b7` | Web: environment variable `AIVERSE_WINDOW_WIDTH=<points>`: the window is always that many points wide, so the UI scales with the canvas whatever its CSS size; pointer, touch and wheel coordinates are scaled the same way | `engine/platform/web/PlatformWeb.cpp` | this file |
| fog memory commit | `fogOfWar.remember` (visual key of rules / game.json): enemy structures seen once stay drawn as they were last seen until their spot is in sight again, also when destroyed meanwhile; neutral objects without a `Mobile` component (scenery, decals) are drawn wherever the map is explored. View only: `GameScene::fogMemory` is filled by `sceneFromSim`, the host sets `GameScene::fogRemember` from `VisibilityLook::remember` | `engine/view/VisibilityField.{h,cpp}`, `engine/view/game/GameView.{h,cpp}`, `apps/client/GameClient.cpp` | `docs/game-format.md` § Fog of war look |

Compatibility: all four are opt-in (a missing `bounds`, no `set_fog` call, no variable, `remember` false by default = previous behaviour). No
save, replay or network format changed; the simulation is not touched, checksum baselines are unchanged.

## Verification

- Linux release build (gcc, Ninja) of the branch: built, the external project's headless matches finish with a winner.
- Web build (Emscripten 3.1.50, `BINARYEN_CORES=1`, `ulimit -s 8000000`): built and exercised in headless Chromium
  through Playwright at several window sizes (clicks on HUD buttons, camera drag inside the bounds, fog switch).
- `./run-tests.sh`: **NOT RUN** (cloud container, no KINGSTON build volume). The main gate on the Mac is still due
  before this branch is merged into `main`.
- Metal / macOS, Android, iOS, Windows: **NOT RUN**. `CameraBehavior.h` and `GameViewScript` are platform
  independent; `PlatformWeb.cpp` is web only.
- Fog memory: checked on screenshots of the Linux client (scouts see an enemy base, leave, the buildings stay drawn
  under the fog; scenery is drawn on explored ground). The memory lives in the client's scene only: the editor's Game
  window and the lobby preview do not set `fogRemember` (NOT DONE). Planet maps (`sceneFromSphere`) are not covered.
- A pre-existing link failure of `aiverse_vivid_creator` on Linux (undefined `ZSTD_*` symbols) is unrelated to these
  changes; the client, headless and aiversebuild targets build.
- No unit tests were added for these hooks: tests for `camera.bounds` clamping, `view.set_fog` and the fog memory
  are the next step before the merge.

## For the 3.1.0 notes

`camera.bounds` in game.json; `view.set_fog`; `fogOfWar.remember`; web `AIVERSE_WINDOW_WIDTH` and the canvas-relative mouse fix. No
breaking changes.

# Generic hooks added for a portrait web game (branch `claude/project-thread-0y3pyf`), 2026-11-27

Three small, game-agnostic additions made while building an external data project (a portrait RTS that lives in its
own repository). Nothing game-specific entered the engine.

| Commit | Change | Paths | Documentation |
|---|---|---|---|
| `6e3700fd` | `game.json` `camera.bounds` `[xMin, zMin, xMax, zMax]`: the camera target stays in the box, also while dragging; equal min and max lock an axis | `engine/render/CameraBehavior.h`, `apps/client/GameClient.{h,cpp}` | `docs/game-format.md` (camera table) |
| `6e3700fd` | Web: mouse-up coordinates are relative to the canvas (buttons did not react when the canvas was not at the page corner) | `engine/platform/web/PlatformWeb.cpp` | none (bug fix) |
| `4680d6e6` | View scripts: `view.set_fog(bool)` switches the fog of war off for the local screen only (debug; the simulation is untouched) | `engine/view/game/GameViewScript.{h,cpp}`, `apps/client/GameClient.cpp` | `docs/scripting.md` |
| `ddf8a4b7` | Web: environment variable `AIVERSE_WINDOW_WIDTH=<points>`: the window is always that many points wide, so the UI scales with the canvas whatever its CSS size; pointer, touch and wheel coordinates are scaled the same way | `engine/platform/web/PlatformWeb.cpp` | this file |

Compatibility: all three are opt-in (a missing `bounds`, no `set_fog` call, no variable = previous behaviour). No
save, replay or network format changed; the simulation is not touched, checksum baselines are unchanged.

## Verification

- Linux release build (gcc, Ninja) of the branch: built, the external project's headless matches finish with a winner.
- Web build (Emscripten 3.1.50, `BINARYEN_CORES=1`, `ulimit -s 8000000`): built and exercised in headless Chromium
  through Playwright at several window sizes (clicks on HUD buttons, camera drag inside the bounds, fog switch).
- `./run-tests.sh`: **NOT RUN** (cloud container, no KINGSTON build volume). The main gate on the Mac is still due
  before this branch is merged into `main`.
- Metal / macOS, Android, iOS, Windows: **NOT RUN**. `CameraBehavior.h` and `GameViewScript` are platform
  independent; `PlatformWeb.cpp` is web only.
- No unit tests were added for these hooks: a test for `camera.bounds` clamping and one for `view.set_fog` are the
  next step before the merge.

## For the 3.1.0 notes

`camera.bounds` in game.json; `view.set_fog`; web `AIVERSE_WINDOW_WIDTH` and the canvas-relative mouse fix. No
breaking changes.

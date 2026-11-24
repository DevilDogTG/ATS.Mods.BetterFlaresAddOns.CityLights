# Tuning log

Levers live at the top of `tools/generate_citylights.py` (`LEVERS`, `SPRITE_PINS`). Each test build is logged here
with its values and the in-game verdict. At release this becomes the table of released values.

ETS2 1.0.0 released these levers after three rounds (T1 brightness + restore, T2 env sprites + BF Base restore,
T3 `tr_light_flares.sii` restore); see the ETS2 repo's `docs/tuning.md`. ATS starts from those values in one build
(ADR-0001, D6).

| Round | Build | Lever group | Values | What to check | Verdict |
|---|---|---|---|---|---|
| T1 | 1.0.0-dev.1 | everything ETS2 1.0.0 released + ATS sprite pins | `intensity` 1.0, `ambient` BF's, `restore` on, `env` on; `SPRITE_PINS` `lamp_flare` -> `lamp_4k`, `lamp_flare_green` -> `bf_gr` | City streets and interchanges at night vs BF alone (base brightness, BF sprites kept); restored fills (Kansas City underground, a player garage); a roadwork floodlight; Bay Bridge `omni.n.25m`; Mandalay Bay `parked.omni.light`; green lamps with the pinned `bf_gr` (Oakland towing ship, AN-124 depot); log clean | pending |

## Effect of T1 (generator report, 2026-11-24)
- 482 BF-styled lights at ATS paths: 359 raised to base-game intensity, 21 already at or above it (kept), 102 kept
  BF's (101 without an ATS twin, 1 other setup unit `omni.b.20m`).
- 149 restored units: 141 BF-styled from their `.always` sibling, 1 from a twin (`omni.n.25m` <- `omni.n.15m`),
  7 as base (`light.y.4m`, `light.y.6m`, `light.y.6m.no.flare`, `parked.omni.light`,
  `poor.garage.interior.o.light`, `light.fill.garage.strong.fadelong.40m`,
  `template.light.lamp.garage.strong.fadelong.deg130`).
- 17 env flares given BF's sprite: photographer flash x3 (`lamp_4k`, pinned), `flare.model.green` and
  `flare.model.green.plane` (`bf_gr`, pinned), white plane + plane blink (`lamp_4k`), red plane (`bf_r3`),
  `omni.lamp.y.3-9m` x6 (`lamp_2k`), `flare.light.n.11m.blink` (`lamp_2k`), `flare.light.n.9m.blink` (`lamp_6k`),
  `street.lamp16m` (`lamp_4k`).
- 16 env flares kept vanilla (no BF sprite): blue beacons x8, small green x3, small red x2, garage lamp / window,
  yellow garage light. 21 untouched env lights kept as base.
- 60 files shipped (52 City Lights + 8 env); none from BF Base.

# DriveDogs: Better Flares City Lights (ATS)

An ATS add-on for **Better Flares v4.9 - City Lights Addon** (by Avelium). Environment lights keep the Better
Flares look (flare sprites, colour shift, beam shape) but light the world about as brightly as the base game, the
lights City Lights drops on game 1.62 come back, and environment lights Better Flares never touches get the same
style.

ATS twin of [ETS2 CityLights 1.0.0](https://github.com/DevilDogTG/ETS2.Mods.BetterFlaresAddOns.CityLights): same
generator and design, with the ATS differences in [ADR-0001](docs/adr/ADR-0001-ats-twin-of-ets2-1.0.0.md).

**Status: `1.0.0-dev.1`** (brightness + 1.62 restore + Better Flares sprites on untouched environment flares, all in
one build). ATS 1.62 only, Better Flares Base 4.9.1 + City Lights 4.9.

## Load order
Top = highest priority in Mod Manager:
1. DriveDogs: Better Flares City Lights
2. DriveDogs: Better Flares More Visibility (optional)
3. Better Flares - Traffic Addon (optional)
4. Better Flares - Headlights.2 (optional)
5. Better Flares - Headlights.1 (optional)
6. Better Flares - City Lights Addon v4.9
7. Better Flares - Base v4.9.1

## What it changes
- **Brightness:** City Lights sets environment lights to a fraction of the base game's lumen (about 30% median).
  This mod puts each light back to its own base-game lumen and keeps Better Flares' sprite, hue / saturation shift,
  cone angles and flare scaling. On ATS that is 359 of the 482 City Lights lights at ATS paths; 21 are already at or
  above the base game, and 101 are ETS2-only units in shared files (no ATS twin, kept as Better Flares has them).
- **1.62 restore:** City Lights ships whole copies of `unit/hookup/lights/**` files made before 1.62 (87 at ATS
  paths), so 149 newer base units are missing while it is loaded: underground / indoor fill lights
  (`light.fill.*.always`, `template.light.lamp.*`, garage fills) and ATS-only lights (`light.y.4m`, `light.y.6m`,
  `light.y.6m.no.flare`, `omni.n.25m`, `parked.omni.light`, `poor.garage.interior.o.light`). The regenerated files
  carry the base game's full unit list, so they come back, styled like their Better Flares sibling or twin where one
  exists.
- **Untouched environment flares:** US street lamps in ATS-only files (`omni_lamp_y`, `street_lamp_16m`, blinking
  `lights_n_blink`), plane / obstruction lights, green lamps and photographers' camera flashes get the sprite Better
  Flares uses for the same vanilla sprite elsewhere. Blue beacons, small red / green lamps and the garage lamps have no
  Better Flares sprite and stay vanilla. Tunnels get no sprite (the base game has none).

Not here: headlights (MoreVisibility), traffic lights (DriveDogs World), vehicle interior / plate lights.

## How it works
The `src/unit/` files are **generated, not hand-edited**, from the extracted base game 1.62 and Better Flares 4.9.1,
so a game or Better Flares update is a regenerate, not a re-edit. Design: ETS2's ADR-0001 / ADR-0002, carried by
[ADR-0001](docs/adr/ADR-0001-ats-twin-of-ets2-1.0.0.md).

`tools/generate_citylights.py` takes each City Lights file at a base-game path and
- keeps every Better Flares unit's own block, raising only `diffuse_color` / `specular_color` intensity to
  `max(Better Flares, intensity x base game)` (`ambient_color` has its own lever, off by default);
- appends the base units City Lights' (and Better Flares Base's) copy lacks, styled from their Better Flares sibling
  or twin;
- gives base environment files no Better Flares pack ships the Better Flares sprite for each vanilla flare sprite
  (table learned from Better Flares at build time, with `SPRITE_PINS` over it on ATS);
- writes only files that differ from Better Flares', and fails on any `model:` it introduces that doesn't resolve.

| Lever (`LEVERS`) | Effect |
|---|---|
| `intensity` | diffuse / specular target as a fraction of the base game (`1.0` = base-game brightness, `None` = BF's) |
| `ambient` | ambient target, same rule (`None` = BF's) |
| `restore` | re-add the units City Lights and Better Flares Base drop on 1.62 |
| `env` | Better Flares sprites on environment flares it leaves vanilla |

Test rounds and their values: [`docs/tuning.md`](docs/tuning.md).

## Build
```
python tools/generate_citylights.py                    # regenerate src/unit/hookup (--notes lists kept / restored units and the sprite pins)
python tools/generate_citylights.py --neutral --out <dir>   # identity check: must equal Better Flares byte-for-byte
python tools/generate_cover.py                         # src/cover.jpg from logo.png + manifest version (also run by prePack)
```
Packed with the `pack-mod` workflow (`pack.config.json`: `drivedog_bf_citylights`, game `ats`). Output goes to
`output/` (ignored).

## Permission
Better Flares has no licence text. Its author allows add-ons that require the original mods. Referencing Better
Flares sprites is covered; shipping files derived from Better Flares' own is not yet cleared, so no public upload
before asking Avelium (SCS forum t=326854).

# ADR-0001: ATS twin of ETS2 CityLights 1.0.0

- Status: accepted (2026-11-24)
- Context: game ATS 1.62, Better Flares Base 4.9.1 + City Lights 4.9 (plain, not "with Fog"); source ETS2
  `DevilDogTG/ETS2.Mods.BetterFlaresAddOns.CityLights` 1.0.0 (`449831a`)

## Context
ETS2 CityLights 1.0.0 regenerates Better Flares City Lights' `unit/hookup/**` files from the base game and Better
Flares (BF): its ADR-0001 (base-game brightness on BF's own units, restore of the units BF's pre-1.62 copies drop)
and ADR-0002 (BF sprites on base environment flares no BF pack ships, the restore widened to BF Base, `FOREIGN`, the
twin rule, dangling BF models kept as BF ships them). It passed three test rounds (T1-T3) on ETS2.

BF 4.9.1 ships the same archives for both games. A dry run of the ETS2 generator on ATS 1.62 (2026-11-24) was clean
(no model failures), but ATS differs in what the generator learns and touches:
- **Thin sprite table.** The env module learns "vanilla sprite -> BF sprite" from BF units with a base twin; most
  votes win. Fewer BF units have an ATS twin, so `lamp_flare` flips to BF's orange `bf_o2` (2 votes vs `lamp_4k` 1;
  ETS2: `lamp_4k` 15 vs 2), turning photographers' camera flashes orange, and `lamp_flare_green` has no rule at all
  (ETS2: `bf_gr`, 5 votes), leaving green plane lights and `flare.model.green` vanilla.
- **ATS-only env files.** `omni_lamp_y.sii`, `street_lamp_16m.sii` and `lights_n_blink.sii` exist only in ATS and
  no BF pack ships them, so US street lamps there get BF street-lamp sprites (`lamp_2k`, `lamp_4k`, `lamp_6k`).
- **ETS2-only units.** 101 BF units in shared files have no ATS twin; they stay BF's (no base value to restore).
  56 BF files sit at paths ATS lacks; they are left to BF and not shipped.
- **ATS-only restored units.** Besides the 143 ETS2 also restores, `light.y.4m`, `light.y.6m`,
  `light.y.6m.no.flare`, `omni.n.25m` (twin of `omni.n.15m`), `parked.omni.light` and
  `poor.garage.interior.o.light`.
- **World `flare.lamp`.** DriveDogs World ATS edited `unit/hookup/lamp.sii`, which BF Base's own copy shadows.

## Decision
1. **Carry ETS2 ADR-0001 and ADR-0002 unchanged.** Same generator, same levers as ETS2 1.0.0 (`intensity` 1.0,
   `ambient` BF's, `restore` on, `env` on). `GAME = "ats"`: the version guard refuses ETS2. `BF_ROOT` follows the
   repo's game, so it reads the ATS extraction.
2. **`SPRITE_PINS` (D4).** BF's own ETS2 rules for the two thin entries are pinned over the learned table:
   `lamp_flare` -> `lamp_4k` (9, x1.6, 0-60 m) and `lamp_flare_green` -> `bf_gr` (1.5, x2.5, 0-80 m), checked
   equal to the table the generator learns from ETS2 base 1.62. Each pinned sprite must exist in a BF pack or the run
   fails; `--notes` prints learned vs pinned, so a BF update that changes its own rule shows up. Every other rule is
   ATS-learned (e.g. `lamp_flare_red` -> `bf_r3` x2.0, ETS2 x2.5: BF's own value on ATS paths, not pinned).
3. **`FOREIGN` stays empty (D5).** World ATS drops its shadowed `flare.lamp` edit (World `b445006`). If World ever
   ships `unit/hookup/lamp.sii` again, it goes into `FOREIGN` so this mod never ships it. This mod ships no
   `lamp.sii` (BF Base's copy drops nothing ATS has).
4. **One test build (D6).** `1.0.0-dev.1` carries every lever ETS2 released after T1-T3; the ATS playtest checks the
   ATS deltas (pinned sprites, ATS-only env files, ATS-only restored units).

## Consequences
- dev.1 ships 60 files: 52 regenerated City Lights files and 8 env files (`foto_01-03`, `green`,
  `lights/lights_n_blink`, `lights/omni_lamp_y`, `lights/plane_flares`, `lights/street_lamp_16m`). No BF Base file is
  shipped on ATS.
- Generator report: 359 lights raised, 21 already at target, 101 + 1 kept BF; 149 restored (141 sibling, 1 twin,
  7 base); 17 env flares given BF's sprite (15 learned + 2 green from the pin; the 3 flashes change sprite), 16 kept
  vanilla, 21 env lights kept base.
- Checks: `--neutral` equals BF byte-for-byte (157 files); a second run reproduces `src/`.
- US street lamps in the ATS-only files change look: a playtest item, not covered by the ETS2 rounds.
- Shipping BF-derived files is not cleared: GitHub release notes only, no `.scs`, no Workshop upload until Avelium
  answers (SCS forum t=326854).

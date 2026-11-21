## Release Notes

## [4.2.1-srcdslab]

First srcdslab release, based on upstream Weapon Restrict 4.2.0 by Dr!fter
([Drifter321/csgo-css-weapon-restrict@05ca3b7](https://github.com/Drifter321/csgo-css-weapon-restrict/commit/05ca3b7b)).
Plugin behaviour, cvars, commands, config files and natives are unchanged.

### Changed

- Moved the sources to the srcdslab layout (`addons/sourcemod/{scripting,translations,configs}`) and
  dropped the prebuilt `compiled/weapon_restrict.smx`.
- `#pragma semicolon 1` and `#pragma newdecls required` now come before every include, and the missing
  semicolons in `cstrike_weapons.inc` were added.
- `myinfo.url` points to this repository.

### Fixed

- Build with SourceMod 1.13: `cstrike_weapons.inc` used the result of `KeyValues.GetString()`, which is
  `void` (error 466). The CS:GO `items_game.txt` parser now calls it without testing the result. The native
  always returned 1, so behaviour is the same.

### Added

- Default `configs/restrict/` files from the upstream release package (`warmup.cfg`, `prewarmup.cfg`,
  `postwarmup.cfg`, `perplayerrestrict.txt`). Without them the plugin logs an error and disables the
  warmup and the per-player limits on every map.
- CI (SourceMod 1.12 and 1.13, translation checks), `latest` and tagged releases, Dependabot, README and
  GPL-3.0 license file.

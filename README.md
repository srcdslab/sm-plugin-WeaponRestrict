# Weapon Restrict

Restricts how many players of each team may buy or carry a weapon, with admin commands, an admin menu,
per-map configs, a warmup round and player-count based limits. Counter-Strike: Source (CS:GO code paths
are kept but not tested here).

This is the srcdslab fork of **Weapon Restrict** by **Dr!fter**
([Drifter321/csgo-css-weapon-restrict](https://github.com/Drifter321/csgo-css-weapon-restrict),
[AlliedModders thread](https://forums.alliedmods.net/showthread.php?t=95405)). It is based on the latest
upstream release, 4.2.0. The plugin file, cvars, commands, config files and natives keep their upstream
names, so existing configs keep working. See [CHANGELOG.md](CHANGELOG.md) for what changed.

## Requirements

- SourceMod 1.12 or newer (built and checked against 1.12 and 1.13).
- The `cstrike` and `sdkhooks` extensions bundled with SourceMod.

The plugin ships no gamedata of its own: it only uses the natives and forwards of the `cstrike` extension
(`CS_OnBuyCommand`, `CS_GetWeaponPrice`, `CS_AliasToWeaponID`, ...), SDKHooks and events. On a 64-bit
CS:S server it relies on the `linux64` entries of SourceMod's own `sm-cstrike.games` gamedata.

## Install

Copy from the release archive:

- `addons/sourcemod/plugins/weapon_restrict.smx`
- `addons/sourcemod/translations/WeaponRestrict.phrases.txt`
- `addons/sourcemod/configs/restrict/` (`warmup.cfg`, `prewarmup.cfg`, `postwarmup.cfg`,
  `perplayerrestrict.txt`)

`cfg/sourcemod/weapon_restrict.cfg` is generated on the first start.

## Configuration

Every weapon has one cvar per team, `sm_restrict_<weapon>_t` and `sm_restrict_<weapon>_ct`:
`-1` = unrestricted (default), `0` = restricted, a positive number = how many players of that team may
have it. Set them in `cfg/sourcemod/weapon_restrict.cfg`, for example:

```
// At most 2 AWPs per team, no auto-snipers
sm_restrict_awp_t "2"
sm_restrict_awp_ct "2"
sm_restrict_g3sg1_t "0"
sm_restrict_g3sg1_ct "0"
sm_restrict_sg550_t "0"
sm_restrict_sg550_ct "0"
```

Restricted weapons cannot be bought, and are refused on pickup while the team is at the limit
(`sm_allow_restricted_pickup`, `sm_allow_awp_pickup`). Players over the limit lose the extra weapons
(refunded) at round start.

| Cvar | Default | Description |
| --- | --- | --- |
| `sm_allow_restricted_pickup` | `0` | `0`: only allow picking up a restricted weapon while under the limit. `1`: always allow pickup. |
| `sm_allow_awp_pickup` | `1` | `0`: only allow AWP pickup while under the limit. `1`: use `sm_allow_restricted_pickup`. |
| `sm_weapon_restrict_immunity` | `0` | Admins can buy restricted weapons. |
| `sm_restricted_sound` | `sound/buttons/weapon_cant_buy.wav` | Sound played when a weapon is refused (empty to disable). |
| `sm_warmup_enable` | `1` | Run a warmup at map start with a weapon picked from `configs/restrict/warmup.cfg`. |
| `sm_warmup_time` | `45` | Warmup length in seconds (ends with `mp_restartgame 1`). |
| `sm_warmup_infinite` | `1` | Infinite grenades when the warmup weapon is a grenade. |
| `sm_warmup_disable_ff` | `1` | Disable friendly fire during warmup. |
| `sm_warmup_respawn` | `1` | Respawn players during warmup. |
| `sm_warmup_respawn_time` | `0.5` | Respawn delay during warmup. |
| `sm_perplayer_restrict` | `0` | Use the player-count based limits of `configs/restrict/perplayerrestrict.txt`. |
| `sm_perplayer_bots` | `1` | Count bots for the per-player limits. |
| `sm_perplayer_specs` | `1` | Count spectators for the per-player limits. |

Per-map configs: `addons/sourcemod/configs/restrict/<map>.cfg`, or `<prefix>_.cfg` (for example `de_.cfg`)
when no map config exists, run after the server configs. Each line is a server command.

`perplayerrestrict.txt` (used only with `sm_perplayer_restrict 1`) gives, per weapon, a `default` limit and
the limit to apply from a given player count:

```
"PerPlayer"
{
	"awp"
	{
		"default"	"0"	// 0 to 9 players: no AWP
		"10"		"1"	// 10 to 17 players: 1 per team
		"18"		"2"	// 18 players and more: 2 per team
	}
}
```

## Commands

| Command | Flag | Description |
| --- | --- | --- |
| `sm_restrict <weapon\|group\|all> <amount> [both\|ct\|t]` | `ADMFLAG_CONVARS` | Restrict a weapon or a group (`pistols`, `smgs`, `shotguns`, `rifles`, `snipers`, `grenades`, `armor`) until the map changes. `sm_restrict all` restricts everything. |
| `sm_unrestrict <weapon\|group\|all>` | `ADMFLAG_CONVARS` | Remove a restriction for both teams. |
| `sm_knives` / `sm_pistols` | `ADMFLAG_CONVARS` | Make the next round a knife or pistol round. |
| `sm_reload_restrictions` | `ADMFLAG_CONVARS` | Reload the cvars and drop admin overrides. |
| `sm_remove_restricted` | `ADMFLAG_CONVARS` | Remove weapons over the limits now. |
| `sm_dropc4` | `ADMFLAG_BAN` | Force the bomb carrier to drop the bomb. |
| `sm_perplayer_debug` | server | Print the per-player limits. |

The admin menu gets a "Weapon Restrict" category when `adminmenu` is loaded.

## API

`addons/sourcemod/scripting/include/restrict.inc` (library `weaponrestrict`) exposes natives and forwards
(`Restrict_OnCanBuyWeapon`, `Restrict_OnCanPickupWeapon`, ...) for other plugins.

## Credits

Weapon Restrict was written by Dr!fter. The default files under `configs/restrict/` are the ones of the
upstream release package. Like every SourceMod plugin, it is distributed under the GNU GPL v3
([LICENSE](LICENSE)).

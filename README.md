# Overlay Teleport Map

![Overlay Teleport Map](https://raw.githubusercontent.com/ChaliceChore/overlay-teleport-map/main/COVER.png)

Press a key to bring up the map over your game, move the cursor, click, and you
are there - with your car, your train, your parachute, or your jetpack. For GTA
San Andreas.

The map shows the same blips the pause map does: mission markers, shops,
safehouses, your girlfriends, every icon you would normally have to go looking
for. So you can see where you are going before you go.

## Or just use it as a better map

You do not have to teleport with it. Three settings turn it into a fast,
high-quality replacement for the pause map - hold a key, look, let go:

```txt
OpenKey=77        // M, right under your hand
HotkeyMode=-1     // hold to view, and teleporting is switched OFF
GameSpeed=0.0     // the world freezes while you look (already the default)
```

`HotkeyMode=-1` is map-only mode: clicking does nothing, and neither do the
mission or undo keys (more on them below).

The default artwork is 4K, with 1K, 2K and 8K also included (see below). It
zooms to 12.5x, so it reads far better than the pause map when you are hunting
for a shop or working out where a mission marker actually is - and it opens
instantly, without going through the menu.

## Requirements

- **GTA San Andreas 1.0 US** – This mod reads fixed memory addresses. It will
  not work on 1.01, 2.0, the Steam release or the Definitive Edition, and
  **it does not detect the wrong version** - it will simply misbehave.
- **[Essentials Pack](https://www.mixmods.com.br/2019/06/sa-essentials-pack/ "Go to Mixmods.com.br")**
  – It includes the 1.0 US `gta_sa.exe`, so it downgrades the game for you.

## Install

1. Extract the **`Overlay Teleport Map`** folder into the modloader folder.
2. Start the game and load a save.
3. Press **`** (backtick, above Tab) to open the map.

That is it. Optional extras are in the `.(optionals)` folder - see below.

### Main Files

```txt
cleo/OTM.cs           the mod
cleo/OTM.ini          settings - open it in any text editor
models/txd/MAP.txd    the overlay map artwork
models/txd/BLIPS.txd  the cursor, the blip dot, and all the map icons
```

**Both `.txd` files are required.** If either is missing or renamed the game
will crash when you open the overlay. A missing `OTM.ini` does not crash - the
mod falls back to its built-in defaults without telling you.

## Controls

| Control | What it does |
| --------- | -------------- |
| **`** (backtick) | Open and close the map. Change this with `OpenKey` |
| **Move mouse** | Move the cursor within the map |
| **Left click** | Teleport to the location where the cursor is pointing |
| **Mouse wheel** | Zoom in and out, toward the cursor |
| **Right click** | Reset the view - whole map, centred |
| **Middle mouse (hold)** | Drag to pan the map. Zoom in first - see below |
| **Insert** | Snap the cursor onto your current player position |
| **Spacebar** | Teleport straight to your mission or waypoint marker |
| **Backspace** | Undo - go back to where you were. Press again to return |
| **End** | Force a teleport through, if one ever hangs. You should never need it |

Everything except the open key is fixed and cannot be rebound.

**Panning only does something once you have zoomed in.** At full zoom-out the
map already fits the screen exactly, so there is nowhere to slide it to and a
drag has no visible effect. Zoom in a couple of notches and it works normally.

## Settings

Open **`cleo/OTM.ini`** in any text editor. Every setting is explained in the
file itself; the following are the more important ones.

| Setting | Default | What it does |
| --------- | --------- | -------------- |
| `OpenKey` | **192** (`` ` ``) | The key that opens the map. Uses Windows virtual-key codes. |
| `HotkeyMode` | 1 | **1** = press to open, press again to close. **0** = hold the key. **-1** = map-only mode. |
| `GameSpeed` | 0.0 | **0.0** = freeze the world while the map is up. **1.0** = world runs normally. Use any other value to speed up or slow down the game. |
| `PlayerCanMove` | 0 | **1** = let mouse movement move the camera while the map is open. **0** = freeze camera movement. |
| `MapSize` | 448.0 | Map height on screen. **448.0** is full screen; **do not** use a higher value. |
| `MapAlpha` | 255 | Map opacity, between 0 and 255. Lower it to see the game behind. |
| `ShowBlips` | 1 | Draw the game's map blips on the map. |
| `ShowModBlips` | 1 | Draw blips from supported mods - needs a helper, see below. |
| `MissionTeleport` | 1 | Highlight the active mission / waypoint marker on the overlay and allow teleporting straight to it. |
| `IgnoreWater` | 0 | **1** lets you teleport at the bottom of water bodies. **0** teleports you at the surface. |
| `MoveTrain` | 1 | **1** = bring the train with you if you are driving one. |

The settings are re-read every time you open the map, so you can edit the file,
reopen, and see the change without restarting the game.

## Optional extras

Everything in `.(optionals)` is genuinely optional - the mod works without any
of it. Each folder has its own README with instructions.

**`OVERLAY MAP`** - the map artwork in 1K, 2K, 4K and 8K. **4K is already
installed.** Only worth changing if you want a sharper map when zoomed right in,
or a smaller one for an older PC.

**`BUY PROPERTY MOD - HELPER`** - install if you use *Buy Property* by
Junior_Djjr, to see your properties on the overlay.

**`MORE RADAR ICONS - HELPER`** - install if you use *More Radar Icons* by
Junior_Djjr, to see its extra icons on the overlay.

**`DECLUTTERED OTM.INI`** - the same settings with the explanations stripped
out, all on one screen instead of spread over 180+ lines. For when you have read
the comments once.

The two helpers exist because those mods add their icons in a way the overlay
cannot see directly. Each one reads its mod's own settings and passes the icons
along. Neither does anything if the mod it supports is not installed.

## Known Issues and Limitations

### 1. Try not to arrive at a mission objective marker while the mission is talking to you

If a mission is playing a conversation while you travel, try not to reach or
teleport to the objective marker until it has finished. Teleporting itself is
fine - **arriving early** is what *might* break the mission, because arriving
cuts the dialogue short.

This is not specific to any one mission - any mission that talks to you can be
affected. An example of this is the **Drive-By** mission.
If you happen to find more such mission(s), report back by creating a GitHub
Issue using the appropriate template.

### 2. The camera tilts once when the overlay opens

Opening the overlay nudges the camera - a single step at `GameSpeed=0.0`, or a
smooth slide to horizontal at `GameSpeed=1.0`. It settles as soon as the map is
up and does not drift while you are looking at it.

I have tried **very hard** to fix this, but I cannot seem to find a solution.
If you are a modder and know how to fix it, share the solution in a GitHub
Issue and I will try to implement it.

### 3. Icons from other CLEO mods only appear if a helper exists for that mod

The overlay mirrors the game's own blip system, so it shows everything the pause
map shows for anything the game itself tracks. Some mods add their icons a
different way - through CLEO+ rather than through the game - and the overlay
cannot see those.

Two such mods are supported by the optional helpers included here: **More Radar Icons**
and **Buy Property**, both by Junior_Djjr. Install the matching helper and their
icons appear on the overlay too. Icons from any other CLEO+ mod will not appear,
and nothing is broken when they do not - that mod goes on working normally.

Where a helper is installed, its icons are treated exactly like the game's own:
unexplored areas hide them, and switching a category off in the pause map's
legend hides them here too. That is correct behaviour, not the overlay losing
blips.

If you want helper scripts for more CLEO mods, create a request through a GitHub
Issue using the appropriate template.

### 4. Four blip icons are drawn differently from the game's

The overlay ships its own icon set, and four of them are deliberately not the
vanilla artwork:

| Icon | What is different |
| ------ | ------------------- |
| **Boatyard** | The vanilla icon is solid black and vanishes against the map. I hate it. |
| **Tattoo** | Colours inverted to make its style match other blips |
| **Save** | Drawn slightly smaller, so it carries the same weight as the icons around it |
| **Player arrow** | Recentred, so it points from where you are instead of pivoting about its own middle |

**Nothing in the game changes** - your radar and pause map are untouched. This
only affects how those four look on the overlay.

### 5. A few things are not shown

Pickups, gang areas, airstrip markers and the like are left off the overlay,
because I have not really looked into adding them.

### 6. The map artwork is nudged a few pixels

The map comes from Junior_Djjr's *[Proper Radar](https://www.mixmods.com.br/2022/07/proper-radar/ "Go to Mixmods.com.br")*,
moved **5 px right and 7 px down** - a shift of roughly 0.1%. The game's own map
is very slightly out of true with the world, and this lines the overlay up so
that clicking a place puts you at that place.

Two things follow from it. The overlay will not match *Proper Radar*
pixel-for-pixel if you compare the two side by side. And because the correction
is one fixed offset applied to the whole map, a small error of a few metres can
still remain in places.

## Uninstall

Delete the `Overlay Teleport Map` folder from the modloader folder. Nothing is
written to your savegame.

## Troubleshooting

1. **The game crashes when I open the map**
One of the two `.txd` files is missing or renamed. Both `MAP.txd` and `BLIPS.txd`
must be present in `models/txd/`, spelled exactly like that.

2. **Nothing happens when I press the key**
Check `OpenKey` in `cleo/OTM.ini`. It is a Windows virtual-key code, not a letter -
192 is `` ` ``, 220 is `\`. Also check CLEO is installed and working.

3. **The map opens but there are no blips**
Check `ShowBlips=1` in the ini.

4. **Mod icons are missing**
Check `ShowModBlips=1`, and that you installed the matching helper from `.(optionals)`.

5. **Middle-mouse dragging does not pan the map**
You are fully zoomed out, where the map already fills the screen and there is
nowhere to pan to. Zoom in first.

6. **Icons vanish when I switch a category off in the pause map**
That is intended - the overlay follows the pause map's legend. Set
`IgnoreMapLegend=1` if you would rather it ignored the legend and always showed
everything.

## Credits

- **starblind94** - for the original mod that OTM is built on,
  *[Super Fast Teleporter](https://libertycity.net/files/gta-san-andreas/127554-super-fast-teleporter.html "Go to LibertyCity.net")*.
- **Junior_Djjr** - for a great deal of what this mod is built on:
  - the fixes to *Super Fast Teleporter*, and the CLEO+ groundwork that made the
    rest possible
  - **the map artwork**, which is taken from *Proper Radar* and
    realigned very slightly - see Known Issues 6
  - *More Radar Icons* and *Buy Property*, both supported here through the
    optional helper scripts

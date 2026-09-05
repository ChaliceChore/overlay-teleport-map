# More Radar Icons helper

**Optional add-on for Overlay Teleport Map.**
Install this only if you also use **More Radar Icons** by Junior_Djjr.

It makes that mod's extra map blips - shops, gyms, bars, train stations, casinos and the rest -
appear on the teleport overlay, so you can see and teleport to them like anything else.

## Why it is needed

Overlay Teleport Map draws the overlay by reading the game's own list of map blips. More Radar
Icons does not use that list - it adds its blips through CLEO+, which keeps a separate list the
overlay cannot see. Without this helper those blips simply are not there.

This helper rebuilds the icon set the **same three ways the mod does**: coordinates listed in its
ini, interior entrances looked up by name in the game's own data, and the police and hospital
respawn points. It never interferes with More Radar Icons itself.

## Requirements

- **Overlay Teleport Map** installed and working
- **More Radar Icons** by Junior_Djjr installed and working
- GTA: San Andreas **1.0 US**, with CLEO and CLEO+

## Install

1. Copy **`OTM_MoreRadarIcons_Helper.cs`** into your game's `CLEO` folder.
2. Open `CLEO\OTM.ini` and make sure this is set:

   ```txt
   ShowModBlips=1
   ```

3. Load a save. The blips appear the next time you open the overlay map.

## Uninstall

1. Simply delete the `.cs` file. Nothing else is affected.
2. Optionally, set `ShowModBlips=0` in `CLEO\OTM.ini`.

## What you will see

Whatever you have enabled in `CLEO\More Radar Icons.ini`. Switch a category off there and it
disappears from the overlay too - the helper reads the same file the mod does, so the two always
agree.

The six custom icons that come with More Radar Icons (sex shop, donuts, store, train, indoor track,
casino) use that mod's own artwork, loaded from its own file.

**The icons follow the pause map.** If an area is still unexplored (and `ShowUnexploredBlips=0`
in `CLEO\OTM.ini`), or you switch a category off in the pause map's legend, these icons hide
exactly as the game's own icons do. Note that the six custom icons count as **"Other"** in that
legend, and the fire icon does too - so switching off "Other" hides more than you might expect.
That is the game's categorisation, not a choice made here.

**Icons vanish while you are on a date.** Clubs, bars, restaurants and diners are hidden for the
duration, because More Radar Icons hides them too.

## Performance

Effectively free. It does its one-off setup when you load a save, then does nothing at all until
you open the overlay map - it refreshes once per opening and sleeps in between. It does not run
checks while you are playing.

At startup it waits for the game's interior data to finish loading before building its list -
normally about half a second. It waits for the data to actually settle rather than for a fixed
time, so a slower PC simply waits a little longer.

The script never shuts itself down, and that is deliberate: the overlay reads a list that lives in
this script's memory, and that memory is released if the script ends.

## If something looks wrong

**No icons at all:**

- Check `ShowModBlips=1` in `CLEO\OTM.ini`.
- Check More Radar Icons itself is working - do its icons show on the normal pause map?
- Close and reopen the overlay. The list refreshes on opening.

**A whole category is missing:**

- Check that category is `Enabled=1` in `CLEO\More Radar Icons.ini`.
- Check it is not switched off in the pause map's legend.
- Restaurants are a special case - see below.

**Restaurants (`[Rest]`) show nothing:**

- Expected on most saves. Those interiors are marked inaccessible in the game data until certain
  points in the story, and More Radar Icons skips them for the same reason.

**An interior I just unlocked has no icon:**

- Restart the game. The interior list is built once at load, and More Radar Icons works the same
  way - it will not show a newly opened interior until the next session either.

## Diagnostics

If you are reporting a problem, this produces a readable report - no recompiling needed.

1. Create an empty file called **`OTM_debug.txt`** in your `CLEO` folder.
2. Load a save and open the overlay once.
3. Two files appear in `CLEO`:
   - `OTM_MoreRadarIcons_Dump.csv` - every icon it found, with category, source and position
   - `OTM_MoreRadarIcons_Status.ini` - what it published

Send both. Delete `OTM_debug.txt` to turn it off again.

## Limits

- Up to **256 icons**. A typical install produces around 80.
- Interior icons are placed at the **centre** of the entrance area. More Radar Icons uses one
  corner instead, so a few icons may sit a metre or two from where that mod puts them.

## Note

*More Radar Icons is by Junior_Djjr. This helper is part of Overlay Teleport Map and is not
affiliated with it - it only reads that mod's configuration files and the game's own data.*

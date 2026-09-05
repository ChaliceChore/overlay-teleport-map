# Buy Property helper

**Optional add-on for Overlay Teleport Map.**
Install this only if you also use **Buy Property** by Junior_Djjr.

It makes that mod's property blips appear on the teleport overlay, so you can see and teleport to
your properties the same way you can to everything else.

## Why it is needed

Overlay Teleport Map draws the overlay by reading the game's own list of map blips. Buy
Property does not use that list - it adds its blips through CLEO+, which keeps a separate list the
overlay cannot see. Without this helper the properties simply are not there.

This helper works out where the blips belong from Buy Property's **own settings files**, and hands
that list to the overlay. It never interferes with Buy Property itself.

## Requirements

- **Overlay Teleport Map** installed and working
- **Buy Property** by Junior_Djjr installed and working
- GTA: San Andreas **1.0 US**, with CLEO and CLEO+

## Install

1. Copy **`OTM_BuyProperty_Helper.cs`** into your game's `CLEO` folder.
2. Open `CLEO\OTM.ini` and make sure this is set:

   ```txt
   ShowModBlips=1
   ```

3. Load a save. The property blips appear the next time you open the overlay map.

## Uninstall

1. Simply delete the `.cs` file. Nothing else is affected.
2. Optionally, set `ShowModBlips=0` in `CLEO\OTM.ini`.

## What you will see

| Icon | Meaning |
| ------ | --------- |
| Green house | For sale - you can buy it now |
| Red house | Not available yet - you have not progressed far enough |
| The property's own icon | You own it (some properties show nothing once bought - that is how the mod is configured) |

Buy a property and the icon updates the next time you open the map.

**The blips follow the pause map.** If an area is still unexplored (and `ShowUnexploredBlips=0`
in `CLEO\OTM.ini`), or you switch a category off in the pause map's legend, these blips hide
exactly as the game's own blips do. Property blips count as **"Other"** in that legend, not
"Locations" - that is the game's own categorisation, not a choice made here. If they disappear
when you switch off "Other", that is correct behaviour.

## Performance

Effectively free. It does its one-off setup when you load a save, then does nothing at all until
you open the overlay map - it refreshes once per opening and sleeps in between. It does not run
checks while you are playing.

The script never shuts itself down, and that is deliberate: the overlay reads a list that lives in
this script's memory, and that memory is released if the script ends.

## If something looks wrong

**No property blips at all:**

- Check `ShowModBlips=1` in `CLEO\OTM.ini`.
- Check Buy Property itself is working - do its blips show on the normal pause map?
- Close and reopen the overlay. The list refreshes on opening.

**Blips are missing after loading a different save:**

- Close and reopen the overlay once.

**A property still shows as "for sale" after buying it:**

- Open the overlay once more. The check runs when the map opens, not while you play.

**Properties you sold or a save you rolled back:**

- Loading an *earlier* save is detected and the list rebuilds. Loading a *later* save that owns
  fewer properties may leave stale blips until you restart the game.

## Diagnostics

If you are reporting a problem, this produces a readable report - no recompiling needed.

1. Create an empty file called **`OTM_debug.txt`** in your `CLEO` folder.
2. Load a save and open the overlay once.
3. Two files appear in `CLEO`:
   - `OTM_BuyProperty_Dump.csv` - every property it found, with position and icon
   - `OTM_BuyProperty_Status.ini` - what it published

Send both. Delete `OTM_debug.txt` to turn it off again.

## Limits

- Up to **64 properties**. A stock Buy Property install has around 17.
- Reads only `CLEO\Properties\*.ini`. The `SaveN` sub-folders are the mod's own uninstall
  bookkeeping and are correctly ignored.

## Note

*Buy Property is by Junior_Djjr. This helper is part of Overlay Teleport Map and is not
affiliated with it - it only reads that mod's configuration files.*

# Overlay Teleport Map - how it works

A plain-language walkthrough of the whole mod. No CLEO knowledge assumed.

**What this is for.** This file explains *what runs, in what order*. The source is the authority -
where this document and the source disagree, the source is right and this file is stale.

**Source:** `OTM.txt` plus the two optional helpers, `OTM_BuyProperty_Helper.txt` and
`OTM_MoreRadarIcons_Helper.txt` - the Sanny Builder sources the shipped `.cs` files are compiled
from.

## The three scripts

| Script | Required? | What it does |
| -------- | ----------- | -------------- |
| `OTM` | yes | the overlay map, the cursor, the blips, the teleport |
| `OTM_BuyProperty_Helper` | optional | publishes Buy Property's icon positions for OTM to draw |
| `OTM_MoreRadarIcons_Helper` | optional | the same for More Radar Icons |

The helpers exist because those two mods add icons through CLEO+, which never creates a radar
trace - so OTM's sweep cannot see them at all. Each helper rebuilds its mod's icon set from that
mod's **own inputs** (ini files, the game's interior pool, the pickup array) and hands OTM a table
in memory. Neither helper ever reads the other mod's script memory.

They communicate through four CLEO shared variables:

```txt
4260 / 4261   Buy Property      table address / record count
4262 / 4263   More Radar Icons  table address / record count
```

One record is 20 bytes: `float X, float Y, float Z, int icon, int flags`.

## Part 1 - OTM's lifecycle

```txt
ON GAME START
    name the thread, register the coordinate-readout text
    clear the undo-teleport memory
    publish "overlay is closed" for other scripts and the helpers

FOREVER
    :WAIT_OPEN
        wait one frame
        if the open hotkey is not pressed: keep waiting

        ---- MAP OPEN, runs once ----
        read every setting from cleo\OTM.ini
        load textures:
            dictionary MAP     -> the overlay map art
            dictionary BLIPS   -> cursor, blip dot, height triangles, 63 blip icons
            dictionary MRDICNS -> six extra icons, ONLY if the MRI helper is running
        work out the map's on-screen size and the aspect-ratio correction
        put the cursor where the last teleport left it
        freeze the game (GameSpeed) and, unless PlayerCanMove=1, the player
        build the two flag bitfields from the settings
        read the pause map's legend and remember which categories are switched off
        publish "overlay is open"

        :MAP_LOOP  ---- one pass per frame, see Part 2 ----

        ---- EXIT ----
        either the player clicked  -> :TELEPORT_AT_WORLD_POS  (Part 5)
        or     the map was closed  -> :MAP_CLOSE
        :RESTORE
            release the textures, unfreeze the game and the player,
            save the cursor position to [STATE], publish "overlay is closed"
```

**Why the settings are re-read on every open** rather than cached at startup: you can edit the ini,
reopen the map, and see the change without restarting the game. The cost is ~18 ini reads on the
open frame, which is a deliberate trade.

## Part 2 - the per-frame loop

Runs once per frame for as long as the map is up. Order matters throughout: this is a painter's
algorithm, so later draws land on top of earlier ones.

```txt
:MAP_LOOP
    ---- INPUT ----
    mouse wheel      -> :ZOOM   zoom toward the cursor, then :CLAMP_CENTRE
    right click      -> reset the view to the whole map, centred
    middle drag      -> pan the map, then :CLAMP_CENTRE
    mouse movement   -> move the cursor, then clamp it inside the visible map
    Spacebar         -> teleport to the mission/waypoint mark, if one was found this frame
    Backspace        -> undo: hop back to where you were before the last teleport

    ---- ONCE PER FRAME ----
    read the player's Z (used by the height indicator, so it is read once, not 175 times)
    freeze the camera's internal clock so the view cannot drift while the map is up
    blank the Fire and Aim inputs, if PlayerCanMove=1

    ---- DRAW, back to front ----
    :MAP        the overlay map
    :BLIPS      the game's own blips          (Part 3)
    :MODBLIPS   the two helper mods' icons    (Part 4)
    :ARROW      an arrow at the player's position
    :MARKER     the teleport cursor - LAST, so it is never hidden under a blip
```

**The Fire/Aim blanking is placed after the mod's own click test and before the branch.** Written
any earlier it would blank the very input the teleport test reads, and nothing would ever fire.

## Part 3 - the blip pipeline

The heart of the mod, and the part with the most history. It walks the game's radar-trace array -
a fixed 175 entries - and decides, for each one, whether to draw anything and what.

```txt
:BLIPS
    find the radar-trace array by reading the address out of the game's own code
        (so it works with or without fastman92's relocation)

    FOR each of the 175 slots:

        1.  IN USE?              empty slot                                    -> skip
        2.  MARK-ONLY EARLY-OUT  if only the Spacebar mark matters this frame and
                                 this blip has an icon, it cannot be the mark   -> skip
        3.  RESOLVE POSITION     a blip attached to an entity carries NO stored position;
                                 look up the car/ped/object and use where it is NOW.
                                 Pickups, spotlights and airstrips cannot be
                                 resolved safely                               -> skip
        4.  MISSION FILTER       contact-point blips are hidden while you are on a
                                 mission, exactly as the pause map hides them   -> skip
        5.  DOOR CORRECTION      a blip on an interior door draws at the door, not inside
        6.  HEIGHT CLASS         compare the blip's Z with the player's:
                                     more than 2 m above  -> up triangle
                                     more than 4 m below  -> down triangle
                                     otherwise            -> box
        7.  ZONE FILTER          if the area is still unexplored and
                                 ShowUnexploredBlips=0, the pause map clouds
                                 this blip - so do we                          -> skip
        8.  SPRITE TEST          does this blip have an icon, or is it a plain dot?
        9.  LEGEND GATE          :LEGEND_HIDDEN - is this icon's category switched
                                 off in the pause map's legend?                -> skip
        10. TRANSFORM + CLIP     world position -> screen position; off-map     -> skip
        11. DRAW
                icon blip     -> the icon, drawn with a NEGATIVE width to un-mirror it
                the mark      -> :MARK_TEST, then a cream square
                plain blip    -> the dot, or a height triangle, tinted with the
                                 blip's palette colour
```

### The legend classifier

```txt
:LEGEND_HIDDEN     in: a sprite id.  out: draw it, or not.

    if no category is hidden at all           -> draw
    if the sprite id is 0-4                   -> draw   (the game never gates these)

    which category does this icon belong to?
        7 named ids            -> Other    (includes both Buy Property icons, and FIRE)
        26 named ids           -> Contacts
        ids 100-105            -> Other    (More Radar Icons' six custom icons)
        anything else          -> Locations
    is that category switched off?             -> skip
```

The three named sets partition ids 5-63 exactly: **26 Locations + 26 Contacts + 7 Other = 59**.
That arithmetic is the cross-check that catches a dropped or duplicated id, which is why one
classifier is shared by both blip paths rather than copied.

## Part 4 - mod blips

```txt
:MODBLIPS
    if ShowModBlips is off -> nothing

    for each of the two helper tables (Buy Property, then More Radar Icons):
        read the table address and record count from the shared variables
        if there is no table, or the count is junk -> nothing
        clamp the count to what the helper could actually have allocated

        FOR each record:
            icon is -1        -> skip   (locked and hidden, or bought with no icon)
            LEGEND GATE       -> the same :LEGEND_HIDDEN as the game's own blips
            ZONE FILTER       -> the same unexplored-area test
            TRANSFORM + CLIP  -> off-map                                       -> skip
            DRAW the icon
```

**The count is treated as untrusted input.** It arrives over a shared variable in an id space
every CLEO script can write, and it sizes a memory walk - so it is bounds-checked, not believed.

**Mod blips get the same gates as the game's own**, which took three field corrections to get
right. The overlay is supposed to be a mirror of the pause map, and the pause map applies these
rules to mod icons too.

## Part 5 - the teleport

```txt
:TELEPORT_AT_WORLD_POS
    remember where we are now, for undo
    work out the ground height at the target
    if it is water and IgnoreWater=0 -> refuse

    remember whether the player has a jetpack   <- BEFORE the move; moving removes it
    move the player, and whatever they are in:
        on foot          -> just the player
        parachuting      -> keep the parachute
        in a car         -> the car comes too
        driving a train  -> the train comes too, unless MoveTrain=0
        in an RC car     -> handled separately
    re-apply the jetpack
    close the map and restore the game

:WAIT_FIRE_RELEASE
    do not let the click that teleported also count as a shot or a second teleport
```

---

## Part 6 - the helper scripts

Both follow the same shape.

```txt
ON GAME START
    wait until the player exists

    Buy Property:      start immediately - it reads ini files, which are ready at once
    More Radar Icons:  wait until the game's interior pool STOPS CHANGING
                       (sample it every 250 ms; build when two readings agree)
                       - because the pool is enumerated once, and an entry that is
                         missing at that moment is missing for the whole session

    BUILD, once:
        Buy Property      read CLEO\Properties\*.ini: position, price tier, icon
        More Radar Icons  read its ini, resolve interior names against the game's
                          pool, and read the police/hospital restart-point arrays
    publish the table address and record count

FOREVER
    wait for the overlay to be OPENED
        refresh every icon from live game state
        republish the table
```

**Why they refresh on map-open and not on a timer.** The table has exactly one reader - OTM's
blip pass - and that only runs while the overlay is up. A refresh with the map closed produces data
nobody looks at. And everything that varies (buying a property, mission progress, an interior
opening, a date starting) changes only while the map is *down*. So one sample per open is not an
approximation of a timer - it is complete.

### What each helper recomputes on every open

```txt
Buy Property        for each property:
                        already bought?  -> its own icon
                        not yet unlocked -> the red icon   (and no pickup search - a
                                            locked property cannot have been bought)
                        otherwise        -> the green "for sale" icon

                    "bought" is LATCHED once found, never re-tested, because the money
                    pickup is a payout that disappears while it respawns - polling it
                    would flip the icon back to "for sale" every time you collected
                    your own money. The latches reset if you load an earlier savegame.

More Radar Icons    for each icon:
                        its interior has closed  -> hide it
                        a date is running, and it is a club/bar/restaurant/diner
                                                 -> hide it
                        otherwise                -> show it
```

## The invariants

If you change anything, these are the properties that have to survive.

1. **The overlay mirrors the pause map.** Every filter exists because the pause map has it. When
   the two disagree, the pause map is right - it has settled four separate questions this way.
2. **The cursor draws last.** Anything drawn after it can cover it.
3. **Texture dictionary names are at most 7 characters**, and the name is the filename. A longer
   name is silently truncated into a name that does not exist, and a missing dictionary crashes
   the game.
4. **A missing texture *name* is silent** - it draws as a blank quad, with no error at all.
5. **The helpers must never terminate.** CLEO frees a script's memory when it ends, and OTM
   would be left reading a freed table.
6. **The blip loop's registers are scarce.** Several are reused with different meanings at
   different points in the loop; the source says which, and the comments there are load-bearing.

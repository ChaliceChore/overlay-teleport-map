# Overlay map resolution

**Optional.** Overlay Teleport Map already installs the **4K** map, which is the recommended
choice. You only need anything in here if you want a sharper map, or a smaller one.

These folders contain **nothing but the map artwork**. The blip icons, the cursor and everything
else live in a separate file that these do not touch, so changing resolution cannot affect anything
except how the map itself looks.

## Choose one

| Folder | File size | Good for |
| -------- | ----------- | ---------- |
| `1K` | 0.5 MB | Older PCs, integrated graphics, or if you are tight on video memory |
| `2K` | 2.0 MB | A modest step up from 1K at a quarter of 8K's cost |
| `4K (DEFAULT)` | 4.5 MB | **Already installed.** The right balance for most people |
| `8K` | 18.0 MB | Sharpest when zoomed right in, if your card has memory to spare |

**The difference only shows when you zoom in.** At the default zoom the whole state fits on screen
and every version looks much the same - the map is being shrunk to fit either way. Zoom in to place
a teleport precisely (the mod goes up to 12.5x) and the higher-resolution versions stay crisp while
the lower ones go soft.

If you are not sure, stay on 4K. If the map ever looks blurry when you zoom in, go up; if the game
is short on video memory, go down.

## How to install

1. Open the folder for the resolution you want.
2. Inside is a `models` folder. Copy it into your Overlay Teleport Map mod folder, replacing
   the `MAP.txd` that is already there.

That is the whole procedure - one file is replaced.

If you install the mod through **modloader**, copy it into the mod's own folder there. If you
installed it manually, the file belongs in `models/txd/`.

To go back, copy the `models` folder from `4K (DEFAULT)` the same way.

## Two things that will break it

**Do not rename the file.** It must stay exactly **`MAP.txd`**. The mod asks the game for a texture
collection called `MAP`, and that name is the filename - rename it and the game cannot find it,
which does not produce a warning. It crashes.

**Do not replace it with map artwork from somewhere else.** This image is aligned to the game's
world coordinates, and that alignment is what makes a click land where you clicked. Other map
images - even good ones at higher resolution - are not aligned the same way, and teleports will
land consistently off-target. The error is small enough to look like bad luck rather than a wrong
file, which is what makes it worth warning about.

## If something looks wrong

**The game crashes when you press the overlay key:**

- `MAP.txd` is missing or misnamed. Check the file is present and spelled exactly `MAP.txd`.

**The map is blank or black, but the blips and cursor are there:**

- The file is present but is not a valid map texture. Reinstall from one of these folders.

**Everything is fine but blurry when zoomed in:**

- You are on a lower resolution than you thought. Reinstall from `4K (DEFAULT)` or `8K`.

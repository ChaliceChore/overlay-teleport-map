# Decluttered OTM.ini

**Optional.** The same settings as the one already installed, with the explanations stripped out -
all 30 on one screen instead of spread over 180 lines.

For when you have read the comments once and would rather just see the values.

## Install

Copy **`OTM.ini`** from this folder into your `cleo` folder, replacing the one there.

To go back, copy `cleo/OTM.ini` from the main mod folder instead.

## What is different

**Nothing that the mod reads.** Same settings, same values, same order - the file is generated from
the normal one and checked key-for-key and value-for-value against it, so the two cannot disagree.

Only the per-setting explanations are gone. Two things are kept: the title at the top, and the list
of fixed keys under `[KEYS]` - those keys cannot be changed in the ini, so if that list were removed
there would be nowhere to find them.

If you want to know what a setting does, the full version is still in the main mod folder, and the
Readme covers the ones most people change.

## A word of warning

The explanations are the only place some limits are written down. `ZoomStep` has to be greater
than 1.0, `WorldSize` is a divisor and must not be 0, and `BlipSize` only accepts 1 to 8. The mod
falls back to a sane value rather than breaking, but you will get no warning that it ignored you.

Keep this copy only if you are comfortable editing settings without the notes next to them.

# GBA Game Patches

A collection of small IPS patches for Game Boy Advance games.

## Games

- [Astro Boy - Omega Factor](Astro%20Boy%20-%20Omega%20Factor)
- [Final Fantasy Tactics Advance](Final%20Fantasy%20Tactics%20Advance)
- [Gunstar Future Heroes](Gunstar%20Future%20Heroes)
- [Pokemon Emerald](Pokemon%20Emerald)
- [Pokemon FireRed](Pokemon%20FireRed)
- [Pokemon LeafGreen](Pokemon%20LeafGreen)
- [Pokemon Ruby](Pokemon%20Ruby)
- [Pokemon Sapphire](Pokemon%20Sapphire)

## Layout

Each game has its own folder containing a `Patch Notes.md` file and the `.ips` patches. Games with more than one supported ROM have a subfolder per ROM, named after the ROM the patches inside are for.

The patch notes for each game list the supported ROMs, what every patch does and how it works.

## Applying a patch

1. Check your ROM against the SHA-1 in the game's patch notes.
2. Open an IPS patcher such as [Rom Patcher JS](https://www.marcrobledo.com/RomPatcher.js/) or [Flips](https://github.com/Alcaro/Flips).
3. Select your ROM and the `.ips` file, then apply.
4. Keep an unpatched copy of your ROM.

To use more than one patch, apply them one after another to the same ROM, checking the patch notes first for any that shouldn't be combined.

## Disclaimer

No ROMs are included. You'll need your own copy of each game.

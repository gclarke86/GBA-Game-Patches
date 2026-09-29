# Astro Boy - Omega Factor - Patch Notes

## Supported ROMs

| ROM | SHA-1 |
|---|---|
| Astro Boy - Omega Factor (Europe) (En,Ja,Fr,De,Es,It) | `9a4fc3533bbdb28fd5945bd1ee7d84d992eee12f` |

## Patches

### Invincible

Permanently enables the game's leftover debug invincibility switch, so Astro Boy stays in his post-hit invincible state at all times, without blinking.

## Notes

ROM size and header checksum unchanged.

## ROM Offsets and Bytes Written

| Patch | Offset | Bytes |
|---|---|---|
| Invincible | `0x29E86` | `C0 46` |

## Assembly

### Invincible

The per-frame player update skips the invincibility block unless the debug MUTEKI byte (the switch behind the debug menu's `MUTEKI ON/OFF` line) is set. The branch is replaced with a NOP, so the block always runs.

```asm
08029E80  ldr   r0, =0x030027FA   ; MUTEKI debug byte
08029E82  ldrb  r0, [r0]
08029E84  cmp   r0, #0
08029E86  beq   0x08029EBC        ; 19 D0 -> C0 46 (nop)
08029E88  ...                     ; r4 = player + 0x124
08029E96  bl    0x080D8528        ; set bit 0x1000 at [player+0x124] = invincible
```

Normally bit `0x1000` is only held during the 120-frame post-hit countdown at `[player+0x390]`.

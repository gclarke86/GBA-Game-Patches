# Final Fantasy Tactics Advance - Patch Notes

## Supported ROMs

| ROM | SHA-1 |
|---|---|
| Final Fantasy Tactics Advance (Europe) (En,Fr,De,Es,It) | `9efaf328cbbcbc830be14940e42e4b92d90dfb58` |
| Final Fantasy Tactics Advance (Japan) | `fa9d23ee88c7fe24374337aca03eb864b5b991f4` |

## Patches

### Colour Mode (LCD B)

Makes LCD B the default colour mode at power-on instead of LCD A, so it doesn't need changing on the title screen every time the game starts.

### Colour Mode (TV)

Makes TV the default colour mode at power-on instead of LCD A, so it doesn't need changing on the title screen every time the game starts.

## Notes

Use one colour mode patch at a time. The option can still be changed by hand on the title screen.

ROM size and header checksum unchanged.

## ROM Offsets and Bytes Written

| Patch | Europe | Japan | Bytes |
|---|---|---|---|
| Colour Mode (LCD B) | `0x6DF4A0` | `0x4F3104` | `01` |
| Colour Mode (TV) | `0x6DF4A0` | `0x4F3104` | `02` |

## Assembly

### Colour Mode (LCD B) / Colour Mode (TV)

A data change, not a code change. The ROM holds a 4-byte block of default settings, and the game copies it into RAM when it sets up its options. The colour mode is the low 2 bits of the block's third byte:

| Value | Colour mode |
|---|---|
| `00` | LCD A (original) |
| `01` | LCD B |
| `02` | TV |

```asm
; Europe: default settings block at 0x6DF49E
; 02 00 [00] 00                  ; [00] at 0x6DF4A0 -> 01 (LCD B) or 02 (TV)

; option getter, case 0 (colour mode)
0800958C  ldrb  r0, [r4, #2]     ; third byte of settings
0800958E  lsls  r0, r0, #0x1E
08009590  lsrs  r0, r0, #0x1E    ; & 3
```

In the Japanese ROM the block is at `0x4F3102`, with the same layout.

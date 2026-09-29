# Gunstar Future Heroes - Patch Notes

## Supported ROMs

| ROM | SHA-1 |
|---|---|
| Gunstar Future Heroes (Europe) (En,Ja,Fr,De,Es,It) | `ce3ea76de11d4c8b7827578ff26c162241e7ab91` |

## Patches

### Invincible

Your HP never drops, you don't flinch or get knocked back when hit (on foot or in the ship stages), hits don't shrink your Supercharge Gauge, and boss crush attacks don't take hold.

### Slow Roulette

The dice roulette moves at a steady one slot per second instead of spinning fast and slowing down, so you can stop it on the slot you want.

## Notes

Invincible does not cover special hits that use a separate hurt state (0x1D). These haven't been tested.

ROM size and header checksum unchanged.

## ROM Offsets and Bytes Written

| Patch | Offset | Bytes |
|---|---|---|
| Invincible - Crush Damage | `0x10FE0` | `00 20` |
| Invincible - Crush Drain | `0x11058` | `C0 46` |
| Invincible - No Crushed State | `0x12362` | `D2 E0` |
| Invincible - No Hit Reaction | `0x12D14` | `60 D0` |
| Invincible - No Supercharge Gauge Loss | `0x18F54` | `C0 46 C0 46` |
| Invincible - No HP Loss | `0x18F5C` | `C0 46` |
| Invincible - Ship No Knockback | `0x3AF22` | `C0 46` |
| Slow Roulette | `0x6A914` | `3C 20 C0 46` |

## Assembly

### Invincible

HP is the halfword at `[player+0x162]`, in 1/16 units (2400 = 150 HP).

```asm
; No HP Loss / No Supercharge Gauge Loss (hit damage routine)
08018F54  bl    0x08018A4C        ; shrink Supercharge Gauge -> nop nop
08018F58  ldr   r1, [r5, #0x14]
08018F5A  ldrh  r0, [r1]
08018F5C  subs  r0, r0, r4        ; HP -= damage -> nop
08018F5E  strh  r0, [r1]

; No Hit Reaction (player hit handler)
08012D14  beq   0x08012D20        ; -> beq 0x08012DD8 (return 0, skip hurt/knockback state 0x17)

; No Crushed State (state change)
08012362  movs  r0, #0x15         ; enter crushed state -> b 0x0801250A (skip)

; Crush Damage / Crush Drain (crushed state 0x15 handler)
08010FE0  adds  r0, r2, #0        ; r0 = -320 (20 HP per hit) -> movs r0, #0
08011058  subs  r0, r2, #1        ; HP -= 1 every frame -> nop
```

No Hit Reaction only works together with No HP Loss. On its own the hit is never cleared, and HP drains every frame.

```asm
; Ship No Knockback (ship hit handling, when hit flag bit 0 of [ship+0x2C] is set)
0803AF1E  movs  r0, #0x40
0803AF20  strh  r0, [r1]          ; [ship+0x168] = 64-frame hit timer
0803AF22  strb  r2, [r6, #0xA]    ; ship state = 1 (hit) -> nop
0803AF24  movs  r0, #0
0803AF26  str   r0, [r6, #0x2C]   ; clear hit flag
0803AF2A  bl    0x08006E68        ; hit sound
```

The ship's hit sound and timer still run. It just never enters state 1.

### Slow Roulette

```asm
0806A914  bl    0x08081050        ; next step delay (double -> int) -> movs r0, #0x3C ; nop
0806A918  strh  r0, [r6]          ; delay = 60 frames
```

The original delay shrinks as the roulette nears its stopping slot, averaging about 8 frames per slot.

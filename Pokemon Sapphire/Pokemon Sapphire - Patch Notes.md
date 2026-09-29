# Pokemon Sapphire - Patch Notes

## Supported ROMs

| ROM | SHA-1 |
|---|---|
| Pokemon - Sapphire Version (USA, Europe) (Rev 1) | `4722efb8cd45772ca32555b98fd3b9719f8e60a9` |
| Pocket Monsters - Sapphire (Japan) | `3233342c2f3087e6ffe6c1791cd5867db07df842` |

## Patches

### Balls Always Catch

Every Poké Ball works like a Master Ball, so any wild Pokémon is caught on the first throw.

### Berry Glitch Fix

Fixes the berry glitch, where berries stop growing and daily events stop happening once the in-game clock passes its first year.

### Caves Always Lit

Dark caves are fully lit, so Flash is never needed.

### Cracked Floors Never Break

Cracked floors never crumble, so you can cross them on foot or at any bike speed without falling through.

### Enemies Can't Attack

Enemy Pokémon never use moves, in single and double battles. They can still switch out or use items.

### Feebas On Any Route 119 Tile

Every fishing spot on Route 119 counts as a Feebas tile. The usual 50% chance of Feebas per bite still applies.

### Fishing Auto Reel

After "Oh! A bite!", the fish is hooked automatically, so you never see "It got away...". You can still get "Not even a nibble...".

### Infinite Safari Balls

Safari Balls are never used up. Normally you get 30, and the Safari Game ends when they run out.

### Infinite Safari Steps

The Safari Zone step counter never goes down. Normally the Safari Game ends after 500 steps.

### Latias Stays On Route 110

Once released, Latias stays on Route 110 instead of roaming Hoenn. It appears in place of a wild encounter there, 1 time in 4.

### Mirage Island Always Appears

Mirage Island always appears on Route 130, and the NPC who watches for it always says "Oh! Oh my! I can see MIRAGE ISLAND today!".

### Muddy Slopes Always Passable

Muddy slopes no longer slide you back down, so you can climb them on foot or on either bike.

### No Water Currents

Strong sea currents no longer push you along.

### No Wild Encounters

No random wild battles when walking, using Surf or using Rock Smash. Fishing, Sweet Scent and scripted battles still work.

### Sealed Chamber No Party Check

The Sealed Chamber puzzle no longer needs Relicanth first and Wailord last in your party.

### Trainers Can't See You

Trainers never spot you or walk over to battle. Talking to a trainer still starts the battle.

### Walk Through Walls

You can walk through walls and other solid map tiles. People, map edges, ledges and water (Surf) still apply.

## Notes

No Wild Encounters also stops Latias appearing, because it only shows up in place of a wild encounter. Sweet Scent still works.

Latias Stays On Route 110 works on an existing save. The roamer's location isn't saved, so it moves to Route 110 the next time you change map.

Assembly listings are for the USA ROM. The Japanese ROM has the same code at the offsets below.

ROM size and header checksum unchanged.

## ROM Offsets and Bytes Written

| Patch | USA | Japan | Bytes |
|---|---|---|---|
| Balls Always Catch | `0x2B8C8` | `0x28AF4` | `C0 46` |
| Berry Glitch Fix - Loop Start | `0x919B` | `0x67A7` | `DB` |
| Berry Glitch Fix - Loop Repeat | `0x91BF` | `0x67CB` | `DA` |
| Caves Always Lit | `0x53CBC` | `0x50F10` | `00 21` |
| Cracked Floors Never Break - No Holes | `0x6A064` | `0x67390` | `70 47` |
| Cracked Floors Never Break - Any Speed | `0x6A162` | `0x6748E` | `80 42` |
| Enemies Can't Attack - Bit Setup | `0x14010` | `0x11318` | `01 21` |
| Enemies Can't Attack - Skip Enemy Moves | `0x14014` | `0x1131C` | `81 40 0A 40 02 D1 40 08 00 D2 11 E0` |
| Feebas On Any Route 119 Tile | `0x84B4C` | `0x819E8` | `CA E7` |
| Fishing Auto Reel | `0x5A65A` | `0x57896` | `C0 46` |
| Infinite Safari Balls | `0x14DB4` | `0x120BC` | `C0 46` |
| Infinite Safari Steps | `0xC823A` | `0xC32CE` | `C0 46` |
| Latias Stays On Route 110 - Start Location | `0x134308` | `0x12F06C` | `19 20` |
| Latias Stays On Route 110 - New Location | `0x134396` | `0x12F0FA` | `19 21` |
| Latias Stays On Route 110 - No Retry | `0x13439C` | `0x12F100` | `C0 46` |
| Latias Stays On Route 110 - No Wandering | `0x1343D8` | `0x12F13C` | USA: `0C F7 64 FD 19 20 78 70 29 E0`<br>Japan: `0E F7 F8 FF 19 20 78 70 29 E0` |
| Mirage Island Always Appears | `0x10D38E` | `0x10836A` | `01 20` |
| Muddy Slopes Always Passable | `0x58C58` | `0x55E88` | `1A E0` |
| No Water Currents - North | `0x570F4` | `0x54324` | `C0 46` |
| No Water Currents - South | `0x57108` | `0x54338` | `C0 46` |
| No Water Currents - West | `0x5711C` | `0x5434C` | `C0 46` |
| No Water Currents - East | `0x57130` | `0x54360` | `C0 46` |
| No Wild Encounters | `0x85066` | `0x81F02` | `C0 46` |
| Sealed Chamber No Party Check | `0x1474E0` | `0x14217C` | `01 20` |
| Trainers Can't See You | `0x84052` | `0x80EF6` | `C0 46` |
| Walk Through Walls | `0x56410` | `0x53640` | `00 21` |

## Assembly

### Balls Always Catch

Ball throw routine: the "is it a Master Ball?" check is removed, so every ball gets 4 shakes.

```asm
0802B8C4  ldrh  r0, [r5]          ; ball used
0802B8C6  cmp   r0, #1            ; Master Ball
0802B8C8  bne   0x0802B8CC        ; -> nop
0802B8CA  movs  r4, #4            ; 4 shakes = caught
```

### Berry Glitch Fix

Date-to-day-count routine: the year loop skipped year 0 (2000). Both checks change from `i > 0` to `i >= 0`.

```asm
08009196  subs  r4, r7, #1        ; i = year - 1
08009198  cmp   r4, #0
0800919A  ble   0x080091C0        ; -> blt (DD -> DB)
...
080091BA  subs  r4, #1            ; i--
080091BC  cmp   r4, #0
080091BE  bgt   0x0800919C        ; -> bge (DC -> DA)
```

### Caves Always Lit

Map load flash-level routine: the map's "cave" flag is read as 0, so every map loads lit.

```asm
08053CBA  ldr   r0, [pc, #0xC]    ; map header
08053CBC  ldrb  r1, [r0, #0x15]   ; cave flag -> movs r1, #0
08053CBE  cmp   r1, #0
```

### Cracked Floors Never Break

The routine that turns a cracked tile into a hole returns at once, and the per-step speed check always passes, so you never fall.

```asm
0806A064  push  {r4, r5, lr}      ; make hole -> bx lr

0806A15A  bl    0x080E6054        ; get player speed
0806A162  cmp   r0, #4            ; fastest Mach Bike speed? -> cmp r0, r0
0806A164  beq   0x0806A16E        ; skip "fall through" reset
```

Holes that are already open still drop you.

### Enemies Can't Attack

Battle "use move" action: the "attacker is absent" check is rebuilt to also skip any odd-numbered battler. Slots 1 and 3 are the enemies.

```asm
08014010  movs  r1, #1
08014012  ldrb  r0, [r7]          ; attacker
08014014  lsls  r1, r0            ; 1 << attacker
08014016  ands  r2, r1            ; absent flags
08014018  bne   0x08014020        ; absent -> skip move
0801401A  lsrs  r0, r0, #1        ; carry = attacker & 1
0801401C  bhs   0x08014020        ; enemy -> skip move
0801401E  b     0x08014044        ; otherwise use move
```

### Feebas On Any Route 119 Tile

Feebas check: the "does this tile match a Feebas spot?" branch becomes unconditional.

```asm
08084B4A  cmp   r1, r0            ; tile == Feebas spot?
08084B4C  beq   0x08084AE4        ; -> b 0x08084AE4
```

### Fishing Auto Reel

Fishing "wait for A" step: the "was A pressed?" test is removed, so the game acts as if A was pressed on the first frame of the bite.

```asm
0805A652  ldrh  r1, [r0, #0x2E]   ; new keys
0805A654  movs  r0, #1            ; A button
0805A656  ands  r0, r1
0805A658  cmp   r0, #0
0805A65A  beq   0x0805A662        ; not pressed -> nop
```

### Infinite Safari Balls

Safari Ball throw routine: the "balls left - 1" step is removed.

```asm
08014DB2  ldrb  r0, [r1]          ; Safari Balls left
08014DB4  subs  r0, #1            ; -> nop
08014DB6  strb  r0, [r1]
```

### Infinite Safari Steps

Safari Zone step routine: the "steps left - 1" step is removed.

```asm
080C8238  ldrh  r0, [r1]          ; steps left
080C823A  subs  r0, #1            ; -> nop
080C823C  strh  r0, [r1]
```

### Latias Stays On Route 110

Each place the roamer picks a new map now uses `0x19` (Route 110).

```asm
; roamer setup: starting map
08134308  ldrb  r0, [r1]          ; random map from roamer table -> movs r0, #0x19
0813430A  strb  r0, [r4, #1]

; roamer relocation: after battle / fleeing
08134396  ldrb  r1, [r1]          ; random map -> movs r1, #0x19
08134398  ldrb  r0, [r4, #1]
0813439A  cmp   r0, r1
0813439C  beq   0x0813437C        ; same map? pick again -> nop
0813439E  strb  r1, [r4, #1]

; roamer movement: on every map change
081343D8  bl    0x08040EA4        ; random number (kept), replaces neighbouring-route search
081343DC  movs  r0, #0x19
081343DE  strb  r0, [r7, #1]      ; roamer map = Route 110
081343E0  b     0x08134436        ; return
```

### Mirage Island Always Appears

Mirage Island check: the final `return FALSE` becomes `return TRUE`.

```asm
0810D38A  cmp   r5, #5
0810D38C  ble   0x0810D356        ; check next party slot
0810D38E  movs  r0, #0            ; -> movs r0, #1
```

### Muddy Slopes Always Passable

Muddy slope movement routine: jumps straight to the "no forced movement" exit.

```asm
08058C56  cmp   r0, #0x20         ; moving north?
08058C58  bne   0x08058C66        ; slide back -> b 0x08058C90 (return FALSE)
```

### No Water Currents

Current tile checks (one per direction): each check always returns FALSE.

```asm
080570F2  cmp   r0, #0x52         ; northward current tile
080570F4  beq   0x080570FA        ; -> nop
080570F6  movs  r0, #0            ; return FALSE
```

The same edit is made at `0x57108` (south, `0x53`), `0x5711C` (west, `0x51`) and `0x57130` (east, `0x50`).

### No Wild Encounters

Wild encounter dice roll: the "encounter happens" branch is removed, so it always returns FALSE.

```asm
0808505C  bl    0x081E0EB0        ; random % 1600
08085064  cmp   r0, r4            ; < encounter rate?
08085066  blo   0x0808506C        ; -> nop
08085068  movs  r0, #0            ; return FALSE
```

### Sealed Chamber No Party Check

Sealed Chamber party check: the final `return FALSE` becomes `return TRUE`.

```asm
081474AA  bne   0x081474E0        ; first Pokémon isn't Relicanth
081474CC  bne   0x081474E0        ; last Pokémon isn't Wailord
081474E0  movs  r0, #0            ; -> movs r0, #1
```

### Trainers Can't See You

Trainer sight check: a trainer's sight distance is ignored, so it always falls through to "not seen".

```asm
08084048  bl    0x08084078        ; get sight distance
08084050  cmp   r4, #0
08084052  bne   0x0808405C        ; seen -> nop
08084054  movs  r0, #0            ; return 0
```

### Walk Through Walls

Map collision routine: the tile's collision bits are replaced with 0.

```asm
0805640C  movs  r0, #0xC0
0805640E  lsls  r0, r0, #4        ; 0xC00
08056410  ands  r1, r0            ; collision bits -> movs r1, #0
08056412  lsrs  r0, r1, #0xA
```

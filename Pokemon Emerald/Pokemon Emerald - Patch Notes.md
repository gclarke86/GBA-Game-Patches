# Pokemon Emerald - Patch Notes

## Supported ROMs

| ROM | SHA-1 |
|---|---|
| Pokemon - Emerald Version (USA, Europe) | `f3ae088181bf583e55daf962a92bb46f4f1d07b7` |
| Pocket Monsters - Emerald (Japan) | `d7cf8f156ba9c455d164e1ea780a6bf1945465c2` |

## Patches

### Balls Always Catch

Every Poké Ball works like a Master Ball, so any wild Pokémon is caught on the first throw.

### Caves Always Lit

Dark caves are fully lit, so Flash is never needed.

### Cracked Floors Never Break

Cracked floors never crumble, so you can cross them on foot or at any bike speed without falling through.

### Enemies Can't Attack

Enemy Pokémon never use moves, in single and double battles. They can still switch out or use items.

### Feebas On Any Route 119 Tile

Every fishing spot on Route 119 counts as a Feebas tile. The usual 50% chance of Feebas per bite still applies.

### Fishing Auto Reel

After "Oh! A bite!", the fish is hooked automatically, so you never see "It got away…". You can still get "Not even a nibble…".

### Infinite Safari Balls

Safari Balls are never used up. Normally you get 30, and the Safari Game ends when they run out.

### Infinite Safari Steps

The Safari Zone step counter never goes down. Normally the Safari Game ends after 500 steps.

### Johto Starters Without Full Dex

Prof. Birch offers the Johto starters without you catching every Hoenn Pokémon. After getting the National Pokédex and leaving his lab, the starters are ready the next time you go back in.

### Mirage Island Always Appears

Mirage Island always appears on Route 130, and the NPC who watches for it always says "Oh! Oh my! I can see MIRAGE ISLAND today!".

### Muddy Slopes Always Passable

Muddy slopes no longer slide you back down, so you can climb them on foot or on either bike.

### No Water Currents

Strong sea currents no longer push you along.

### No Wild Encounters

No random wild battles when walking, using Surf or using Rock Smash. Fishing, Sweet Scent and scripted battles still work.

### Roamer Stays On Route 110

Once released, the roaming Latios or Latias (whichever you chose in the TV interview) stays on Route 110 instead of roaming Hoenn. It appears in place of a wild encounter there, 1 time in 4.

### Sealed Chamber No Party Check

The Sealed Chamber puzzle no longer needs Wailord first and Relicanth last in your party.

### Trainers Can't See You

Trainers never spot you or walk over to battle. Talking to a trainer still starts the battle.

### Walk Through Walls

You can walk through walls and other solid map tiles. People, map edges, ledges and water (Surf) still apply.

## Notes

No Wild Encounters also stops the roaming Latios or Latias appearing, because it only shows up in place of a wild encounter. Sweet Scent still works.

Roamer Stays On Route 110 works on an existing save. The roamer's location isn't saved, so it moves to Route 110 the next time you change map.

Johto Starters Without Full Dex only changes the check in Prof. Birch's lab. The diploma in Lilycove City still needs a complete Hoenn Pokédex.

Assembly listings are for the USA ROM. The Japanese ROM has the same code at the offsets below.

ROM size and header checksum unchanged.

## ROM Offsets and Bytes Written

| Patch | USA | Japan | Bytes |
|---|---|---|---|
| Balls Always Catch | `0x56610` | `0x56220` | `C0 46` |
| Caves Always Lit | `0x85498` | `0x84E00` | `00 21` |
| Cracked Floors Never Break - No Holes | `0x9E490` | `0x9DD68` | `70 47` |
| Cracked Floors Never Break - Any Speed | `0x9E58E` | `0x9DE66` | `80 42` |
| Enemies Can't Attack - Bit Setup | `0x3E0E0` | `0x3DD20` | `01 21` |
| Enemies Can't Attack - Skip Enemy Moves | `0x3E0E4` | `0x3DD24` | `81 40 0A 40 02 D1 40 08 00 D2 0F E0` |
| Feebas On Any Route 119 Tile | `0xB4A6E` | `0xB41C6` | `C9 E7` |
| Fishing Auto Reel | `0x8CBE6` | `0x8C556` | `C0 46` |
| Infinite Safari Balls | `0x3F00C` | `0x3EC4C` | `C0 46` |
| Infinite Safari Steps | `0xFC15E` | `0xFC9CE` | `C0 46` |
| Johto Starters Without Full Dex | `0x1F9CCD` | `0x1F1268` | `16 0D 80 01 00` |
| Mirage Island Always Appears | `0x13797E` | `0x1379EE` | `01 20` |
| Muddy Slopes Always Passable | `0x8AE28` | `0x8A78C` | `1A E0` |
| No Water Currents - North | `0x89120` | `0x88A84` | `C0 46` |
| No Water Currents - South | `0x89134` | `0x88A98` | `C0 46` |
| No Water Currents - West | `0x89148` | `0x88AAC` | `C0 46` |
| No Water Currents - East | `0x8915C` | `0x88AC0` | `C0 46` |
| No Wild Encounters | `0xB5162` | `0xB48BA` | `C0 46` |
| Roamer Stays On Route 110 - Start Location | `0x161C96` | `0x161BAA` | `19 20` |
| Roamer Stays On Route 110 - New Location | `0x161D34` | `0x161C48` | `19 21` |
| Roamer Stays On Route 110 - No Retry | `0x161D3A` | `0x161C4E` | `C0 46` |
| Roamer Stays On Route 110 - No Wandering | `0x161D7E` | `0x161C92` | USA: `0D F7 25 FC 19 20 78 70 2C E0`<br>Japan: `0D F7 DD F9 19 20 78 70 2C E0` |
| Sealed Chamber No Party Check | `0x1796F4` | `0x1795AC` | `01 20` |
| Trainers Can't See You | `0xB3D6C` | `0xB34C4` | `38 E0` |
| Walk Through Walls | `0x8820C` | `0x87B70` | `00 21` |

## Assembly

### Balls Always Catch

Ball throw routine: the "is it a Master Ball?" check is removed, so every ball gets 4 shakes.

```asm
0805660C  ldrh  r0, [r5]          ; ball used
0805660E  cmp   r0, #1            ; Master Ball
08056610  bne   0x08056614        ; -> nop
08056612  movs  r4, #4            ; 4 shakes = caught
```

### Caves Always Lit

Map load flash-level routine: the map's "cave" flag is read as 0, so every map loads lit.

```asm
08085496  ldr   r0, [pc, #0x10]   ; map header
08085498  ldrb  r1, [r0, #0x15]   ; cave flag -> movs r1, #0
0808549A  cmp   r1, #0
```

### Cracked Floors Never Break

The routine that turns a cracked tile into a hole returns at once, and the per-step speed check always passes, so you never fall.

```asm
0809E490  push  {r4, r5, lr}      ; make hole -> bx lr

0809E586  bl    0x0811A138        ; get player speed
0809E58E  cmp   r0, #4            ; fastest Mach Bike speed? -> cmp r0, r0
0809E590  beq   0x0809E59A        ; skip "fall through" reset
```

Holes that are already open still drop you.

### Enemies Can't Attack

Battle "use move" action: the "attacker is absent" check is rebuilt to also skip any odd-numbered battler. Slots 1 and 3 are the enemies.

```asm
0803E0E0  movs  r1, #1
0803E0E2  ldrb  r0, [r6]          ; attacker
0803E0E4  lsls  r1, r0            ; 1 << attacker
0803E0E6  ands  r2, r1            ; absent flags
0803E0E8  bne   0x0803E0F0        ; absent -> skip move
0803E0EA  lsrs  r0, r0, #1        ; carry = attacker & 1
0803E0EC  bhs   0x0803E0F0        ; enemy -> skip move
0803E0EE  b     0x0803E110        ; otherwise use move
```

### Feebas On Any Route 119 Tile

Feebas check: the "does this tile match a Feebas spot?" branch becomes unconditional.

```asm
080B4A6C  cmp   r1, r0            ; tile == Feebas spot?
080B4A6E  beq   0x080B4A04        ; -> b 0x080B4A04
```

### Fishing Auto Reel

Fishing "wait for A" step: the "was A pressed?" test is removed, so the game acts as if A was pressed on the first frame of the bite.

```asm
0808CBDE  ldrh  r1, [r0, #0x2E]   ; new keys
0808CBE0  movs  r0, #1            ; A button
0808CBE2  ands  r0, r1
0808CBE4  cmp   r0, #0
0808CBE6  beq   0x0808CBEE        ; not pressed -> nop
```

### Infinite Safari Balls

Safari Ball throw routine: the "balls left - 1" step is removed.

```asm
0803F00A  ldrb  r0, [r1]          ; Safari Balls left
0803F00C  subs  r0, #1            ; -> nop
0803F00E  strb  r0, [r1]
```

### Infinite Safari Steps

Safari Zone step routine: the "steps left - 1" step is removed.

```asm
080FC15C  ldrh  r0, [r1]          ; steps left
080FC15E  subs  r0, #1            ; -> nop
080FC160  strh  r0, [r1]
```

### Johto Starters Without Full Dex

A script change, not a code change. When you enter Prof. Birch's lab after getting the National Pokédex, its script asks "are all Hoenn Pokémon caught?" (special `0x151`). The patch replaces that call with "result = 1", so the answer is always yes.

```
; Birch's lab entry script
081F9CCD  26 0D 80 51 01     ; result = special 0x151 (all Hoenn Pokémon caught?) -> 16 0D 80 01 00 (result = 1)
081F9CD2  21 0D 80 01 00     ; compare result, 1
081F9CD7  06 01 E9 9C 1F 08  ; if equal, go to 0x081F9CE9 (Johto starters ready)
```

The same check at `0x2186E7` (the Lilycove City diploma) is left alone. In the Japanese ROM the special is `0x14E`.

### Mirage Island Always Appears

Mirage Island check: the final `return FALSE` becomes `return TRUE`.

```asm
0813797A  cmp   r5, #5
0813797C  ble   0x08137946        ; check next party slot
0813797E  movs  r0, #0            ; -> movs r0, #1
```

### Muddy Slopes Always Passable

Muddy slope movement routine: jumps straight to the "no forced movement" exit.

```asm
0808AE26  cmp   r0, #0x20         ; moving north?
0808AE28  bne   0x0808AE36        ; slide back -> b 0x0808AE60 (return FALSE)
```

### No Water Currents

Current tile checks (one per direction): each check always returns FALSE.

```asm
0808911E  cmp   r0, #0x52         ; northward current tile
08089120  beq   0x08089126        ; -> nop
08089122  movs  r0, #0            ; return FALSE
```

The same edit is made at `0x89134` (south, `0x53`), `0x89148` (west, `0x51`) and `0x8915C` (east, `0x50`).

### No Wild Encounters

Wild encounter dice roll: the "encounter happens" branch is removed, so it always returns FALSE.

```asm
080B5158  bl    0x082E7BE0        ; random % 2880
080B5160  cmp   r0, r4            ; < encounter rate?
080B5162  blo   0x080B5168        ; -> nop
080B5164  movs  r0, #0            ; return FALSE
```

### Roamer Stays On Route 110

Each place the roamer picks a new map now uses `0x19` (Route 110).

```asm
; roamer setup: starting map
08161C96  ldrb  r0, [r1]          ; random map from roamer table -> movs r0, #0x19
08161C98  strb  r0, [r4, #1]

; roamer relocation: after battle / fleeing
08161D34  ldrb  r1, [r1]          ; random map -> movs r1, #0x19
08161D36  ldrb  r0, [r4, #1]
08161D38  cmp   r0, r1
08161D3A  beq   0x08161D1A        ; same map? pick again -> nop
08161D3C  strb  r1, [r4, #1]

; roamer movement: on every map change
08161D7E  bl    0x0806F5CC        ; random number (kept), replaces neighbouring-route search
08161D82  movs  r0, #0x19
08161D84  strb  r0, [r7, #1]      ; roamer map = Route 110
08161D86  b     0x08161DE2        ; return
```

### Sealed Chamber No Party Check

Sealed Chamber party check: the final `return FALSE` becomes `return TRUE`.

```asm
081796C0  bne   0x081796F4        ; first Pokémon isn't Wailord
081796E0  bne   0x081796F4        ; last Pokémon isn't Relicanth
081796F4  movs  r0, #0            ; -> movs r0, #1
```

### Trainers Can't See You

Trainer sight check: the "not seen" branch becomes unconditional, so a trainer's sight distance is ignored.

```asm
080B3D60  bl    0x080B3DF0        ; get sight distance
080B3D6A  cmp   r6, #0
080B3D6C  beq   0x080B3DE0        ; not seen -> b 0x080B3DE0 (return 0)
```

### Walk Through Walls

Map collision routine: the tile's collision bits are replaced with 0.

```asm
08088208  movs  r0, #0xC0
0808820A  lsls  r0, r0, #4        ; 0xC00
0808820C  ands  r1, r0            ; collision bits -> movs r1, #0
0808820E  lsrs  r0, r1, #0xA
```

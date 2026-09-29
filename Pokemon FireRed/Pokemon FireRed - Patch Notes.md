# Pokemon FireRed - Patch Notes

## Supported ROMs

| ROM | SHA-1 |
|---|---|
| Pokemon - FireRed Version (USA, Europe) (Rev 1) | `dd5945db9b930750cb39d00c84da8571feebf417` |
| Pocket Monsters - FireRed (Japan) | `04139887b6cd8f53269aca098295b006ddba6cfe` |

## Patches

### Balls Always Catch

Every Poké Ball works like a Master Ball, so any wild Pokémon is caught on the first throw.

### Caves Always Lit

Dark caves are fully lit, so Flash is never needed.

### Enemies Can't Attack

Enemy Pokémon never use moves, in single and double battles. They can still switch out or use items.

### Fishing Auto Reel

When a fish bites, it's hooked automatically, so you never see "It got away…". You can still get "Not even a nibble…".

### Infinite Safari Balls

Safari Balls are never used up. Normally you get 30, and the Safari Game ends when they run out.

### Infinite Safari Steps

The Safari Zone step counter never goes down. Normally the Safari Game ends after 600 steps.

### No Cycling Road Slope

The Cycling Road slope no longer pulls you downhill, so you can stop or ride back up whenever you like.

### No Wild Encounters

No random wild battles when walking, using Surf or using Rock Smash. Fishing, Sweet Scent and scripted battles still work.

### Roamer Roar Fix

Fixes a bug where the roaming Pokémon disappears for good if it uses Roar or Whirlwind on you. It now only leaves after you defeat it, catch it or the battle ends in a draw. This is the same fix Nintendo made in the Nintendo Switch re-release. USA ROM only, as the Japanese ROM doesn't have the bug.

### Roamer Stays On Route 2

Once released, the roaming Raikou, Entei or Suicune (depending on your starter) stays on Route 2 instead of roaming Kanto. It appears in place of a wild encounter there, 1 time in 4.

### Trainers Can't See You

Trainers never spot you or walk over to battle. Talking to a trainer still starts the battle.

### Walk Through Walls

You can walk through walls and other solid map tiles. People, map edges, ledges and water (Surf) still apply.

## Notes

No Wild Encounters also stops the roaming Pokémon appearing, because it only shows up in place of a wild encounter. Sweet Scent still works.

Roamer Stays On Route 2 works on an existing save. The roamer's location isn't saved, so it moves to Route 2 the next time you change map.

Assembly listings are for the USA ROM. The Japanese ROM has the same code at the offsets below.

ROM size and header checksum unchanged.

## ROM Offsets and Bytes Written

| Patch | USA | Japan | Bytes |
|---|---|---|---|
| Balls Always Catch | `0x2D77C` | `0x2CF40` | `C0 46` |
| Caves Always Lit | `0x55CD0` | `0x5557C` | `00 21` |
| Enemies Can't Attack - Bit Setup | `0x15CDC` | `0x154DC` | `01 21` |
| Enemies Can't Attack - Skip Enemy Moves | `0x15CE0` | `0x154E0` | `81 40 0A 40 02 D1 40 08 00 D2 0F E0` |
| Fishing Auto Reel | `0x5D5EA` | `0x5CE96` | `C0 46` |
| Infinite Safari Balls | `0x16AF0` | `0x162EC` | `C0 46` |
| Infinite Safari Steps | `0xA0F2E` | `0xA21EE` | `C0 46` |
| No Cycling Road Slope | `0x5A1F0` | `0x59A98` | `00 20 70 47` |
| No Wild Encounters | `0x82BD2` | `0x827AA` | `C0 46` |
| Roamer Roar Fix | `0x15BC8` | - | `28 78 01 28 03 D0 07 28 01 D0 03 28 01 D1` |
| Roamer Stays On Route 2 - Start Location | `0x141DF6` | `0x1424A2` | `14 20` |
| Roamer Stays On Route 2 - New Location | `0x141E7E` | `0x14252A` | `14 21` |
| Roamer Stays On Route 2 - No Retry | `0x141E84` | `0x142530` | `C0 46` |
| Roamer Stays On Route 2 - No Wandering | `0x141ECA` | `0x142576` | USA: `03 F7 07 F8 03 20 38 70 14 20 78 70 28 E0`<br>Japan: `01 F7 89 FF 03 20 38 70 14 20 78 70 28 E0` |
| Trainers Can't See You | `0x81BC2` | `0x8179A` | `19 E0` |
| Walk Through Walls | `0x58E46` | `0x586EE` | `00 21` |

## Assembly

### Balls Always Catch

Ball throw routine: the "is it a Master Ball?" check is removed, so every ball gets 4 shakes.

```asm
0802D778  ldrh  r0, [r5]          ; ball used
0802D77A  cmp   r0, #1            ; Master Ball
0802D77C  bne   0x0802D780        ; -> nop
0802D77E  movs  r4, #4            ; 4 shakes = caught
```

### Caves Always Lit

Map load flash-level routine: the map's "cave" flag is read as 0, so every map loads lit.

```asm
08055CCE  ldr   r0, [pc, #0xC]    ; map header
08055CD0  ldrb  r1, [r0, #0x15]   ; cave flag -> movs r1, #0
08055CD2  cmp   r1, #0
```

### Enemies Can't Attack

Battle "use move" action: the "attacker is absent" check is rebuilt to also skip any odd-numbered battler. Slots 1 and 3 are the enemies.

```asm
08015CDC  movs  r1, #1
08015CDE  ldrb  r0, [r6]          ; attacker
08015CE0  lsls  r1, r0            ; 1 << attacker
08015CE2  ands  r2, r1            ; absent flags
08015CE4  bne   0x08015CEC        ; absent -> skip move
08015CE6  lsrs  r0, r0, #1        ; carry = attacker & 1
08015CE8  bhs   0x08015CEC        ; enemy -> skip move
08015CEA  b     0x08015D0C        ; otherwise use move
```

### Fishing Auto Reel

Fishing "wait for A" step: the "was A pressed?" test is removed, so the game acts as if A was pressed on the first frame of the bite.

```asm
0805D5E2  ldrh  r1, [r0, #0x2E]   ; new keys
0805D5E4  movs  r0, #1            ; A button
0805D5E6  ands  r0, r1
0805D5E8  cmp   r0, #0
0805D5EA  beq   0x0805D5F2        ; not pressed -> nop
```

### Infinite Safari Balls

Safari Ball throw routine: the "balls left - 1" step is removed.

```asm
08016AEE  ldrb  r0, [r1]          ; Safari Balls left
08016AF0  subs  r0, #1            ; -> nop
08016AF2  strb  r0, [r1]
```

### Infinite Safari Steps

Safari Zone step routine: the "steps left - 1" step is removed.

```asm
080A0F2C  ldrh  r0, [r1]          ; steps left
080A0F2E  subs  r0, #1            ; -> nop
080A0F30  strh  r0, [r1]
```

### No Cycling Road Slope

Cycling Road slope tile check: replaced with "return FALSE", so no tile counts as a slope tile.

```asm
0805A1F0  push  {lr}              ; -> movs r0, #0
0805A1F2  lsls  r0, r0, #0x18     ; -> bx lr
```

### No Wild Encounters

Wild encounter dice roll: the "encounter happens" branch is removed, so it always returns FALSE.

```asm
08082BC8  bl    0x081E46F4        ; random % 1600
08082BD0  cmp   r0, r4            ; < encounter rate?
08082BD2  blo   0x08082BD8        ; -> nop
08082BD4  movs  r0, #0            ; return FALSE
```

### Roamer Roar Fix

After a battle with the roamer, the game switches the roamer off if the outcome is odd. That wrongly includes 5, "player teleported away", which is what happens when the roamer uses Roar or Whirlwind on you. The check is rewritten to switch it off only on a win (1), a draw (3) or a catch (7).

```asm
; original
08015BC4  bl    0x08142060        ; save roamer HP and status
08015BC8  ldrb  r1, [r5]          ; battle outcome
08015BCA  movs  r0, #1
08015BCC  ands  r0, r1            ; outcome is odd?
08015BCE  cmp   r0, #0
08015BD0  bne   0x08015BD6
08015BD2  cmp   r1, #7            ; caught?
08015BD4  bne   0x08015BDA
08015BD6  bl    0x08142094        ; switch roamer off

; patched
08015BC8  ldrb  r0, [r5]          ; battle outcome
08015BCA  cmp   r0, #1            ; won?
08015BCC  beq   0x08015BD6
08015BCE  cmp   r0, #7            ; caught?
08015BD0  beq   0x08015BD6
08015BD2  cmp   r0, #3            ; drew?
08015BD4  bne   0x08015BDA
```

### Roamer Stays On Route 2

Each place the roamer picks a new map now uses `0x14` (Route 2). On every map change the map group is also set to `3` (Kanto towns and routes).

```asm
; roamer setup: starting map
08141DF6  ldrb  r0, [r1]          ; random map from roamer table -> movs r0, #0x14
08141DF8  strb  r0, [r5, #1]

; roamer relocation: after battle / fleeing
08141E7E  ldrb  r1, [r1]          ; random map -> movs r1, #0x14
08141E80  ldrb  r0, [r4, #1]
08141E82  cmp   r0, r1
08141E84  beq   0x08141E66        ; same map? pick again -> nop
08141E86  strb  r1, [r4, #1]

; roamer movement: on every map change
08141ECA  bl    0x08044EDC        ; random number (kept), replaces neighbouring-route search
08141ECE  movs  r0, #3
08141ED0  strb  r0, [r7]          ; roamer map group = 3
08141ED2  movs  r0, #0x14
08141ED4  strb  r0, [r7, #1]      ; roamer map = Route 2
08141ED6  b     0x08141F2A        ; return
```

### Trainers Can't See You

Trainer sight check: the "not seen" branch becomes unconditional, so a trainer's sight distance is ignored.

```asm
08081BB8  bl    0x08081C00        ; get sight distance
08081BC0  cmp   r7, #0
08081BC2  beq   0x08081BF8        ; not seen -> b 0x08081BF8 (return 0)
```

### Walk Through Walls

Map collision routine: the tile's collision bits are replaced with 0.

```asm
08058E42  movs  r0, #0xC0
08058E44  lsls  r0, r0, #4        ; 0xC00
08058E46  ands  r1, r0            ; collision bits -> movs r1, #0
08058E48  lsrs  r0, r1, #0xA
```

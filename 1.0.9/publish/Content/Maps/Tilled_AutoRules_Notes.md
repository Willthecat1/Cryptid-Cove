# Tilled Auto Rule Notes

This file documents how `Tilled_AutoRules.tmx` reads inputs and writes outputs.

## Layer roles (important)

Your rule map currently uses these exact layer names:
- `input_Tilling`
- `inputnot_Tilling`
- `output_Tilling`

Notation:
- `input_*` = tiles that must exist.
- `inputnot_*` = tiles that must NOT exist.
- `output_*` = tile to place when the input checks match.

## Neighbor bitmask notation

Use cardinal neighbors around the center tile `C`:

```
	N
W C E
	S
```

Bit values:
- N = 1
- E = 2
- S = 4
- W = 8

Mask formula:
- `mask = N + E + S + W`

Examples:
- mask `0`  = isolated tile (no NESW neighbors)
- mask `1`  = connected only north
- mask `3`  = connected north + east
- mask `10` = connected east + west
- mask `15` = connected all four sides

## Input layer notation cheatsheet

When building each rule block in `Tilled_AutoRules.tmx`:
- Put your "tilled marker" tile on `input_Tilling` where a tilled neighbor is required.
- Put your marker tile on `inputnot_Tilling` where a non-tilled neighbor is required.
- Leave a position empty on both layers if you do not care about that neighbor.

Practical rule-writing pattern:
1. Choose a mask (`0..15`).
2. For each direction (N/E/S/W), set either `input_Tilling` or `inputnot_Tilling`.
3. Set the center tile in `output_Tilling` to the art tile for that mask.
4. Repeat for all masks.

## Scalable tileset workflow

To scale this system cleanly as your tileset grows:
1. Keep one full 16-mask set per terrain family (for example: tilled soil set A, set B, winter variant).
2. Maintain a mapping table in your notes like `mask -> output gid` for each family.
3. Reuse the same input mask logic and only swap `output_Tilling` center tiles.
4. If you need corner-specific visuals, add a second pass rule file that checks diagonals after the first pass.

## Recommended mapping table template

Copy this into your own notes and fill your gids:

```
mask  0 -> gid ___
mask  1 -> gid ___
mask  2 -> gid ___
mask  3 -> gid ___
mask  4 -> gid ___
mask  5 -> gid ___
mask  6 -> gid ___
mask  7 -> gid ___
mask  8 -> gid ___
mask  9 -> gid ___
mask 10 -> gid ___
mask 11 -> gid ___
mask 12 -> gid ___
mask 13 -> gid ___
mask 14 -> gid ___
mask 15 -> gid ___
```

## Current output preset (active now)

The current `output_Tilling` centers are derived from your manual examples on the `Tilling` layer in `Tester.tmx`:

```
mask  0 -> gid 1
mask  1 -> gid 31
mask  2 -> gid 2
mask  3 -> gid 35
mask  4 -> gid 11
mask  5 -> gid 21
mask  6 -> gid 15
mask  7 -> gid 25
mask  8 -> gid 4
mask  9 -> gid 37
mask 10 -> gid 3
mask 11 -> gid 36
mask 12 -> gid 17
mask 13 -> gid 27
mask 14 -> gid 16
mask 15 -> gid 26
```

If a specific edge/corner looks wrong in-game, change only that mask center tile in `output_Tilling` and keep the input masks unchanged.

## Common gotchas

- `rules.txt` must live in `Content/Maps` and point to `Tilled_AutoRules.tmx`.
- Keep `input_*` and `inputnot_*` logic consistent; conflicting checks can make a rule never match.
- Start with NESW only. Add diagonals as a second pass once the base 16 masks are stable.

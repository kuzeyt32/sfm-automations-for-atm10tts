# SFM Grammar Reference (Official)

From [TeamDman/SuperFactoryManager](https://github.com/TeamDman/SuperFactoryManager) ANTLR grammar.

## Keywords (case insensitive)

| Category | Keywords |
|----------|----------|
| Triggers | `EVERY`, `TICKS`, `TICK`, `SECONDS`, `SECOND`, `GLOBAL`, `REDSTONE`, `PULSE`, `DO`, `END` |
| IO | `INPUT`, `OUTPUT`, `FROM`, `TO`, `RETAIN`, `EACH`, `EXCEPT`, `FORGET`, `EMPTY`, `SLOTS`, `SLOT` |
| Conditions | `IF`, `THEN`, `ELSE`, `HAS`, `TRUE`, `FALSE`, `NOT`, `AND`, `OR` |
| Comparison | `GT`, `LT`, `EQ`, `LE`, `GE`, `>`, `<`, `=`, `<=`, `>=` |
| Set Ops | `OVERALL`, `SOME`, `EVERY`, `EACH`, `ONE`, `LONE` |
| Sides | `TOP`, `BOTTOM`, `NORTH`, `EAST`, `SOUTH`, `WEST`, `LEFT`, `RIGHT`, `FRONT`, `BACK`, `NULL`, `SIDE` |
| Other | `WITH`, `WITHOUT`, `TAG`, `#`, `ROUND`, `ROBIN`, `BY`, `LABEL`, `BLOCK`, `NAME`, `WHERE` |

### Items that are keywords (must be quoted with `"`)
- `"redstone"` — REDSTONE is a keyword
- `"tick"`, `"ticks"`, `"second"`, `"seconds"` — if used as item names

## If/Else If Syntax

```
if boolexpr then
    block
else if boolexpr then
    block
else
    block
end
```

### Boolean expressions
- `TRUE` / `FALSE`
- `NOT boolexpr`
- `boolexpr AND boolexpr`
- `boolexpr OR boolexpr`
- `(boolexpr)` — parentheses for grouping
- **WARNING**: `and`/`or` have same precedence, use parentheses!

### Has conditions
```
[setOp] label HAS comparisonOp number [item] [with #tag]
```
- `setOp`: `OVERALL` (default), `SOME`, `EVERY`, `EACH`, `ONE`, `LONE`
- `overall` — counts items across ALL labeled inventories
- `each` — checks each inventory individually
- Examples:
  - `if Barrel has gt 32 iron_ingot then` (overall, default)
  - `if each Barrel has gt 32 iron_ingot then`

## Input/Output

### Input
```
INPUT [limit] [item] FROM [EACH] label [sidequalifier] [slotqualifier]
```
- `limit`: `quantity RETAIN number` or just `quantity` or just `RETAIN number`

### Output
```
OUTPUT [limit] [item] TO [EMPTY SLOTS IN] [EACH] label [sidequalifier] [slotqualifier]
```

### Retention
```
input retain 1 from a        — leave 1 in source
input 25 retain 1 from a     — move 25, leave 1 in source
output retain 1 to b          — keep exactly 1 in target
output retain 32 item to each Chamber slots 0  — keep 32 in slot 0
```

## Sides

Valid sides: `top`, `bottom`, `north`, `east`, `south`, `west`, `left`, `right`, `front`, `back`

Usage: `from a top side`, `to b bottom side slots 0`

## Slots

```
slots 0           — single slot
slots 0,1,3-4,5   — multiple slots, supports ranges
```

## Forget

```
forget              — clear buffer
forget label1       — clear buffer from specific label
forget a, b, c      — multiple labels
```

## Each

```
input 5 from each a     — 5 from EACH inventory labeled "a"
input 5 from a          — 5 total from all "a" inventories
output 1 to each b      — 1 to EACH inventory labeled "b"
```

## Round Robin

```
ROUND ROBIN BY LABEL    — distribute by label
ROUND ROBIN BY BLOCK    — distribute by block
```

## With/Without (Tag matching)

```
input item:: with #forge:gems from a
output item:: without #minecraft:swords to b
```

## Redstone

```
EVERY REDSTONE PULSE DO ... END
if REDSTONE then ...
if REDSTONE ge 5 then ...
```

## Comments

```
-- single line comment
```

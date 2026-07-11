# SFM + AE2 Inscriber Automation Guide

## SFM (Super Factory Manager) Syntax Reference

### Program Structure
- `Name "Program Name"` — names the program
- `Every tick do ... End` — runs every tick (20/sec), only for energy (FE)
- `Every 20 ticks do ... End` — runs once per second (for items/fluids)
- `Forget` — section divider inside Every block; clears input buffer; commands before first Forget are REQUIRED (halt on failure), each Forget section is optional (skip on failure)

### Commands
- `Input <item> from <Label>` — schedules items from container into buffer
- `Input <item1>, <item2> from <Label>` — comma-separated takes each independently
- `Output <item> to <Label>` — moves buffered items to container
- `Output retain N <item> to <Label> slots X` — keeps exactly N of item in slot X
- `Input fe:: from <Label>` — energy input
- `Output fe:: to <Label> <side>` — energy output (use `top side` not `top`)
- `each <Label>` — distributes across all containers with that label (round-robin)
- `Forget <label>` — clears input buffer from specific label

### Item Reference Format
- `<type>:<mod>:<item>` — full format (e.g., `item:ae2:silicon`)
- `fe::` — energy (any source)
- `item::` — any item, any mod
- `::` in a position means "match anything"
- Bare names work for common items: `gold_ingot`, `diamond`, `redstone`
- `mod:item` works: `ae2:silicon`, `ae2:printed_silicon`

### Conditionals (if statements)
- `if Label has <operator> N <item> then ... end`
- Operators: `lt`/`<`, `gt`/`>`, `le`/`<=`, `ge`/`>=`, `eq`/`=`
- Can nest: `if ... then if ... then ... end end`
- **IMPORTANT**: `has` uses `mod:item` format (e.g., `ae2:silicon`), NOT `item:mod:item`

### Key Behaviors
- `Forget` clears input buffer — items still in buffer when Forget runs are LOST
- Always ensure buffer is empty (output succeeded) before using Forget
- `retain` with different item type in slot: silently fails, item stays in buffer
- `Input` comma-separated: takes each item independently (not all-or-nothing)
- SFM is STATELESS — no variables, no counters between ticks
- Each trigger execution is independent

---

## AE2 Inscriber Mechanics

### Slot Layout (confirmed working in ATM10TTS)
| Slot | Position | Print_Inscriber | Assembly_Inscriber |
|------|----------|----------------|-------------------|
| 0 | Top | Universal Press (manual, stays forever) | Printed Circuit |
| 1 | Bottom | (unused) | Printed Silicon |
| 2 | Middle | Raw Material | Redstone |
| 3 | Output | Printed product → Barrel | Processor → Barrel |

### Universal Press
- ATM10TTS uses Universal Press (replaces all 4 individual presses)
- Place manually in slot 0 (top) of each Print_Inscriber
- Never needs to be removed or managed by SFM

---

## All Recipes

### Standard AE2 (7 recipes)

**Printing (Print_Inscriber — Universal Press in slot 0):**
| # | Material (slot 2) | Output | Item ID |
|---|------------------|--------|---------|
| 1 | Silicon | Printed Silicon | `ae2:printed_silicon` |
| 2 | Gold Ingot | Printed Logic Circuit | `ae2:printed_logic_processor` |
| 3 | Certus Quartz Crystal | Printed Calculation Circuit | `ae2:printed_calculation_processor` |
| 4 | Diamond | Printed Engineering Circuit | `ae2:printed_engineering_processor` |

**Assembly (Assembly_Inscriber — NO press):**
| # | Top (0) | Bottom (1) | Middle (2) | Output | ID |
|---|---------|------------|------------|--------|-----|
| 5 | Printed Logic | Printed Silicon | Redstone | Logic Processor | `ae2:logic_processor` |
| 6 | Printed Calc | Printed Silicon | Redstone | Calculation Processor | `ae2:calculation_processor` |
| 7 | Printed Eng | Printed Silicon | Redstone | Engineering Processor | `ae2:engineering_processor` |

### ExtendedAE (2 recipes)

**Printing:**
| # | Material (slot 2) | Output | Item ID |
|---|------------------|--------|---------|
| 8 | Entro Crystal | Printed Concurrent Circuit | `extendedae:concurrent_processor_print` |

**Assembly:**
| # | Top (0) | Bottom (1) | Middle (2) | Output | ID |
|---|---------|------------|------------|--------|-----|
| 9 | Printed Concurrent | Printed Silicon | Redstone | Concurrent Processor | `extendedae:concurrent_processor` |

---

## Code Structure Explanation

### Power Block (Every tick)
- Pulls FE from EnderCell
- Distributes to ALL inscribers via shared `Inscriber` label

### Main Block (Every 20 ticks)
1. **Extraction (REQUIRED)**: Pulls ALL finished items from slot 3 of both inscriber groups → Barrel
2. **Forget — Printing**: 5 `if` blocks, each checks:
   - Printed stock < 64 (don't overproduce)
   - Raw material >= 1 (only print if material exists)
   - Nested `if` because `and` operator precedence is unreliable in SFM
3. **Forget — Assembly**: 4 `if` blocks, each checks all 3 ingredients >= 1:
   - Individual `Input → Output` pairs (NOT comma-separated — only takes first item)
   - Each ingredient to its correct slot

### Labels Required
| Label | Blocks | Purpose |
|-------|--------|---------|
| `Inscriber` | ALL inscribers | Power distribution |
| `Print_Inscriber` | Inscribers WITH Universal Press (slot 0) | Printing circuits |
| `Assembly_Inscriber` | Inscribers WITHOUT press | Processor assembly |
| `Barrel` | Storage container | All item I/O |
| `EnderCell` | Power source | FE input |

### Raw Materials to Stock in Barrel
`ae2:silicon` · `gold_ingot` · `ae2:certus_quartz_crystal` · `diamond` · `redstone` · `extendedae:entro_crystal`

---

## Lessons Learned (Troubleshooting)

1. **Slot layout is critical** — AE2 inscriber slots don't match intuitive top/middle/bottom numbering. Test and verify.
2. **`has` format differs from `Input`/`Output`** — `has` uses `mod:item`; `Input`/`Output` can use bare names.
3. **Comma-separated Input is unreliable** — only takes the first matching item. Use individual Input/Output pairs.
4. **Forget clears buffer destructively** — always ensure buffer is empty before Forget.
5. **`retain` fails silently** — if slot has different item type, command does nothing. Useful for preventing cross-contamination.
6. **Nested `if` over `and`** — SFM's `and`/`or` have same precedence, use parentheses or nest.
7. **`each` scales automatically** — add more inscribers anytime without code changes.
8. **Universal Press stays forever** — place manually in slot 0, never manage with SFM.

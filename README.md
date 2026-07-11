# SFM Automations for ATM10 TTS

Super Factory Manager (SFM) automation scripts for **All The Mods 10 - To The Sky (ATM10TTS)**.

## Scripts

### [`inscriber_automation.sfm`](inscriber_automation.sfm)
AE2 Inscriber automation for all processor types:
- Printed Silicon, Logic, Calculation, Engineering circuits
- Logic, Calculation, Engineering, Concurrent processors
- Power distribution and auto-extraction

**Labels:** `Inscriber`, `Print_Inscriber`, `Assembly_Inscriber`, `Barrel`, `EnderCell`

### [`fluix_entro_crystal_automation.sfm`](fluix_entro_crystal_automation.sfm)
Reaction Chamber + Crusher automation for crystal production with multiplication loop:

| Recipe | Input | Output |
|--------|-------|--------|
| Charge Certus | 64× Certus Quartz Crystal | Charged Certus Quartz Crystal |
| Recover Certus | 16× Charged Certus + 16× Certus Dust | 64× Certus Quartz Crystal |
| Fluix Crystal | 32× Fluix Dust + 32× Charged Certus | Fluix Crystal |
| Entro Crystal | 32× Entro Dust + 32× Fluix Crystal | Entro Crystal |

Crushing loop: excess crystals → Crusher → dust → Reaction Chamber → more crystals.

**Labels:** `ReactionChamber`, `Crusher`, `Barrel`, `EnderCell`, `Sink`

## Requirements

- **Super Factory Manager (SFM)** mod
- **Applied Energistics 2 (AE2)**
- **ExtendedAE**
- ATM10TTS modpack (for correct recipes/slots)

## Setup

1. Place SFM Manager block
2. Label your machines with the matching labels (use SFM Label Gun)
3. Load the `.sfm` file into the Manager
4. Ensure power (FE) is connected to your EnderCell
5. For Fluix/Entro: place a Sink (Cooking for Blockheads) next to water for the Reaction Chamber

## Labels Reference

### Inscriber Automation
| Label | Machine | Notes |
|-------|---------|-------|
| `Barrel` | Storage container | All items in/out |
| `EnderCell` | Power source | FE input |
| `Inscriber` | ALL inscribers | Power only |
| `Print_Inscriber` | Inscribers WITH Universal Press | Slot 0 has press |
| `Assembly_Inscriber` | Inscribers WITHOUT press | Processor assembly |

### Crystal Automation
| Label | Machine | Notes |
|-------|---------|-------|
| `Barrel` | Storage container | All items in/out |
| `EnderCell` | Power source | FE input |
| `ReactionChamber` | Reaction Chamber(s) | Crystal production |
| `Crusher` | Crusher(s) | Crystal → Dust |
| `Sink` | Sink (Cooking for Blockheads) | Water source |

## Slot Layout (Reaction Chamber)

All recipes share slots 0 and 1 to ensure only one recipe type runs at a time:
- Slot 0: Primary ingredient
- Slot 1: Secondary ingredient (if applicable)
- Slot 10: Output (extracted via bottom side)

Crusher input via top side, output extracted via bottom side.

## Caps

All items capped at **100,000** (100k) to prevent overflow. Adjust `lt 100000` values in the code to change.

## Notes

- SFM processes items every 30 ticks (1.5 seconds)
- Each recipe has its own `Forget` block for mutual exclusion
- `retain` ensures exact item counts in machine slots
- Nested `if` statements used instead of `and` (SFM `and`/`or` precedence is unreliable)
- `has` conditions use `mod:item` format (e.g., `ae2:fluix_dust`)
- Extraction happens at END of cycle to prevent mixed-recipe clogging

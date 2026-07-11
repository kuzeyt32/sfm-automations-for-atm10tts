# SFM Automations for ATM10 TTS

Super Factory Manager (SFM) automation scripts for **All The Mods 10 - To The Sky (ATM10TTS)**.

## Folder Structure

```
├── codes/
│   ├── my-codes/           # My own SFM scripts
│   │   ├── inscriber_automation.sfm
│   │   ├── fluix_entro_crystal_automation.sfm
│   │   └── chamber_automation.sfm
│   └── other-creators/     # Scripts from other creators
│       └── energizing_orb_automation.sfm  (credit: The Voos)
├── documentation/
│   └── sfm_ae2_guide.md    # SFM + AE2 syntax reference
├── README.md
└── LICENSE
```

## My Codes

### [`codes/my-codes/inscriber_automation.sfm`](codes/my-codes/inscriber_automation.sfm)
AE2 Inscriber automation using **`else if` chains** — only one print/assembly job per cycle.

| Priority | Recipe | Machine |
|----------|--------|---------|
| 1 | Printed Silicon | Print_Inscriber |
| 2 | Printed Logic Circuit | Print_Inscriber |
| 3 | Printed Calculation Circuit | Print_Inscriber |
| 4 | Printed Engineering Circuit | Print_Inscriber |
| 5 | Printed Concurrent Circuit | Print_Inscriber |
| 6 | Logic Processor | Assembly_Inscriber |
| 7 | Calculation Processor | Assembly_Inscriber |
| 8 | Engineering Processor | Assembly_Inscriber |
| 9 | Concurrent Processor | Assembly_Inscriber |

**Labels:** `Inscriber`, `Print_Inscriber`, `Assembly_Inscriber`, `Barrel`, `EnderCell`

### [`codes/my-codes/fluix_entro_crystal_automation.sfm`](codes/my-codes/fluix_entro_crystal_automation.sfm)
Reaction Chamber + Crusher automation with **`else if` chain** — only one recipe per cycle.

| Priority | Recipe | Input | Retain |
|----------|--------|-------|--------|
| 1 | Certus Recovery | 16× Charged Certus + 16× Certus Dust | slots 0-1 |
| 2 | Charge Certus | 64× Certus Quartz Crystal | slot 0 |
| 3 | Fluix Crystal | 32× Fluix Dust + 32× Charged Certus | slots 0-1 |
| 4 | Entro Crystal | 32× Entro Dust + 32× Fluix Crystal | slots 0-1 |

Crushing loop: excess crystals → Crusher → dust → Reaction Chamber → more crystals.
All caps: **100,000** items.

**Labels:** `ReactionChamber`, `Crusher`, `Barrel`, `EnderCell`, `Sink`

### [`codes/my-codes/chamber_automation.sfm`](codes/my-codes/chamber_automation.sfm)
Generic chamber processor — takes 9 items, extracts `source_gem` outputs.

**Labels:** `Chamber`, `Barrel`

## Other Creators' Codes

### [`codes/other-creators/energizing_orb_automation.sfm`](codes/other-creators/energizing_orb_automation.sfm)
Energizing Orb automation — **credit: The Voos (YouTube)**. Uses `If`/`Else If` chain.

| Recipe | Input |
|--------|-------|
| Ender Core | ender_eye + dielectric_casing + capacitor_basic_tiny |
| Charged Certus | certus_quartz_crystal |
| Energized Steel | iron_ingot + gold_ingot |
| Blaze Powder | 4× blaze_powder |
| Blaze Rod | blaze_rod |
| Niotic Crystal | diamond |
| Spirited Crystal | emerald |
| Nitro Crystal | nether_star + 2× redstone_block + blazing_crystal_block |

**Labels:** `Orb`, `Barrel`

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

## SFM Syntax Notes

Key lessons learned from the [official SFM examples](https://github.com/TeamDman/SuperFactoryManager):

- **`else if` chains** — ensures only ONE recipe runs per cycle (cleaner than multiple Forget blocks)
- **`"redstone"`** — must be quoted because `redstone` is an SFM keyword
- **Keywords are case insensitive** — `if`/`If`/`IF` all work
- **`retain`** — works on both input (`input retain N`) and output (`output retain N`)
- **`each`** — distributes across all labeled inventories
- **Valid sides** — `top`, `bottom`, `north`, `south`, `east`, `west` (no `back`)

## Contributing

Pull requests welcome! If you have SFM automation scripts for ATM10TTS, feel free to add them under `codes/other-creators/` with credit.

## License

MIT — see [LICENSE](LICENSE)

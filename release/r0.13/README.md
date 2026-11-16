# A404 Bluetooth wallplate carrier: rev 0.13 PROTOTYPE DRAFT

KiCad 10.0.8. Project `dt-wp` (`dt-wp.kicad_sch` + `dt-wp.kicad_pcb` + `dt-wp.kicad_pro` + `dt-wp.kicad_dru`).
Board outline: 60 × 88 mm, R3 corners. **4 layers**, JLCPCB stackup **JLC04161H-7628**, 1.6 mm.

Still labelled a prototype draft: the checks at the bottom need hardware in hand.

## Sign-off status

| Check | Result |
|---|---|
| ERC (schematic) | 0 errors, 0 warnings |
| Schematic ↔ PCB parity | 0 issues |
| DRC unconnected | 0 |
| DRC errors | 1, accepted: mounting hole H2 sits inside the RT9400 (U2) drawn outline. See "Mounting holes". |
| DRC warnings | 11, all silkscreen cosmetic |
| 2 mm PoE isolation rule | 0 violations |
| Module footprints | A404, RT9400 and MiniKit all confirmed on the 1:1 printed fit-check |

## What changed from rev 0.12

1. **J5 pin 13 now connected to 3V3_ISO.** The Audiocom Rev1.3 hardware manual shows it as a second VCC pin; rev 0.12 left it open.
2. **Ethernet centre taps.** The A404's PHY is an RTL8201F. T1 pins 2 and 7 (PHY side) now each have a 100 nF capacitor to GND_ISO (C3, C4), the usual connection for this voltage-mode PHY. They are not tied to 3.3 V. Realtek's reference schematic could not be opened to confirm; see check 1 below.
3. **A404 reset pull-up option.** J5 pin 4 (nRESET) → R2 10 k (fitted) → R3 0805 0 Ω link (**not fitted**) → 3V3_ISO. With R3 open the pin floats exactly as in rev 0.12. Fit R3 only if the module will not start.
4. **Two M3 mounting holes** (3.2 mm, unplated) beside the pairing button: H1 left, H2 right and 5.5 mm lower.
5. **SW1** is now XKB TS-1103S-C-J (LCSC C561245), fitted by JLCPCB. The C&K part is unobtainable. Same pads.
6. Small reroutes to make room for H1: ESP32_3V3, I2S_LRCLK and the SW1 ground via.
7. D1 supply (3.3 V min vs RT9400 tolerance) downgraded from blocker to a note.
8. Front silkscreen: "PROTOTYPE / VK2EVA BT WP" panel with slashed hazard bands (lower left) and a "VK2EVA" block (upper right).

Unchanged: placement of all modules, J1, T1, the isolation scheme and the stackup.

## Mounting holes
- H1 at KiCad (116.3, 133.5): 11.7 mm left of the button centre.
- H2 at KiCad (139.7, 139.0): 11.7 mm right of the button centre and 5.5 mm below it. It cannot sit level with H1 because the Ethernet lines run there on an inner layer.
- H2 is inside the RT9400's drawn outline (the outline assumes a wide body). The module stands upright on its pins about 9 mm away, so a screw head should clear, but **check with the new fit-check print** with the RT9400 seated.
- Use **nylon M3 screws or standoffs**. H2 is 2.5 mm from cable-side copper on an inner layer; a metal screw would not short anything but would weaken the isolation margin.
- The Clipsal plate's own top fixing screw (84 mm centres) still lands behind the MiniKit. These two holes are for holding the PCB to a bracket; they do not solve that. Measure how far the plate screw reaches.

## J1 stays
Würth lists the WR-MJ SMT series (which includes 634108185321) as PoE++ compatible up to 100 W, rated 1.5 A per contact. This board draws well under 0.1 A. JLCPCB's "non-PoE" tag is a catalogue label, not a Würth rating.

## Checks before powering a built board
1. **Centre-tap bias (2 minutes, settles item 2).** Power an A404 alone: 3.3 V on J4.7 and J5.13, ground on J4.8. Measure DC from TXP (J4.5) to ground. About 1–2 V confirms the capacitor-to-ground wiring. Near 0 V would mean the taps need 3.3 V instead: remove C3/C4 and wire their T1-side pads to 3.3 V.
2. **Reset.** If the A404 does not boot on the carrier, bridge R3.
3. **Heights:** SW1 is 8.5 mm tall on the front; the RT9400 stands upright on the rear and is probably the tallest part in the wall cavity.
4. **Shield:** J1 shield is still unconnected (decision pending). No cable-side EMC termination network is fitted.
5. T1 (H2019NLT) centre-tap DC current rating was not checked against its datasheet. PoE current here is roughly 20–40 mA.

## Assembly
- **JLCPCB, top side only** (`JLC-BOM-top.csv` + `JLC-CPL-top.csv`): T1, D1, SW1, R1, R2, C2, C3, C4. R3 is left off. Check T1 (pin 1 top-left) and D1 rotation in JLCPCB's preview.
- **By hand:** J1 (rear, 1.27 mm pitch), C1 (+ lead in the square pad), JP1, and the three modules. The A404 and RT9400 solder in on their own pins; the MiniKit on 2.54 mm pin headers.
- JP1: bridge it to power the ESP32 from PoE. **Open it before connecting USB.**

## Files
`dt-wp-r0.13-gerbers-JLC.zip` (11 layers + PTH/NPTH drill + maps + job file), `BOM-full.csv`, `JLC-BOM-top.csv`, `JLC-CPL-top.csv`, `ERC-report.txt`, `DRC-report.txt`, `board-render-top-bottom.png`, `fit-check/` (1:1 printable dummy with the new holes).

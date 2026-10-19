# Board notes

- **Board outline bounding box:** 36.527 × 44.044 mm = **1608.80 mm²**. Single closed outline. Only the antenna section of the ESP32 module overhangs the top edge, as the rules allow; the module body is on the board.
- **Files:** `pcb_challenge_final.kicad_pcb`, `pcb_challenge_final.kicad_pro`, `pcb_challenge_final.kicad_dru` (KiCad 10 board, project and custom rules).

## Stack-up (1.6 mm FR4)
F.Cu signal + top parts / 0.1 prepreg / **In1.Cu GND** / 1.24 core / **In2.Cu +3.3V** / 0.1 prepreg / B.Cu signal + bottom parts.

The USB pair (D+/D−) runs on F.Cu only, with no vias. It sits entirely over one continuous In1 GND region, with no plane splits under it.

## Global rules (Board Setup → Constraints)
| Rule | Value |
|---|---|
| Track width | 0.15 mm |
| Clearance | 0.12 mm |
| Copper to board edge | 0.3 mm |
| Via drill | 0.3 mm |
| Via annular ring | 0.15 mm (0.6 mm vias) |
| Vias in pads | none |
| Flip-flop (XSON6) pad traces | 0.17 mm or less |

All vias are standard 0.3 mm / 0.6 mm through-hole vias (no blind, buried or micro vias). This keeps the board on a standard, low-cost 4-layer process: blind and buried vias need sequential lamination or depth-controlled drilling, which costs several times more, takes longer to make and adds failure points. It also keeps every via outside all IC courtyards on every layer.

## IC rule
- **As worded:** there are no vias inside any IC courtyard, and no other nets' traces inside an IC courtyard on that IC's own layer.
- **Stricter version we also follow:** no vias inside any IC courtyard on any layer.
- `pcb_challenge_final.kicad_dru` encodes these checks, so DRC enforces them.

## Readme issues 7 and 8 (applied ahead of the official board)
- **Issue 7:** Y2 pin 1 (oscillator EN) is connected to `en`.
- **Issue 8:** all four J3 shield (SH) pads are connected to Net-(J3-SHIELD) together with R6 and R24.

All named nets match the official board exactly; unused pins are left unconnected.

## DRC (KiCad 10.0.6, zones refilled)
- 0 unconnected items.
- The only errors are the known flip-flop `malformed_courtyard` errors from the provided XSON6 footprint.
- All footprints are the provided ones and are unmodified.
- The single ESP32 "footprint does not match library" warning comes from the 4-layer switch. The official board shows it too once set to 4 layers.
- The 27 "library not in configuration" warnings come from the embedded `reg`, `adder` and `3v3_ldo` footprints.
- **J3 hole clearance rule:** the unmodified library USB-C footprint puts pad B12 0.185 mm from its own NPTH locating peg. KiCad reports this intermittently as a hole-clearance error. `pcb_challenge_final.kicad_dru` relaxes hole clearance to 0.15 mm only between J3's own pad and hole pairs; everything else stays at 0.25 mm. If DRC is run without our rules file, this library item may appear intermittently.

## Silkscreen
Reference designators are hidden on the silkscreen, which the rules allow. Component outlines and polarity marks are kept.

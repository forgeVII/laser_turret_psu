# Laser Turret — Power Supply

Dedicated repo for the turret's custom 5 V power supply: as-built wiring, header
map, power budget, safety rules, physical build notes and reserved media slots.

**Demo short:** https://youtube.com/shorts/UFWoeOp6Qgk

The simulator, firmware and control/calibration notes live in the separate
[forgeVII/laser_turret](https://github.com/forgeVII/laser_turret) repo.

> Last updated: 2026-11-23. Photos, videos and social posts are reserved in
> `MEDIA.md` and added later.

## At a glance

| Rail | Converter | Load |
|---|---|---|
| fixed 5 V | MP1584 | ESP32 + Blue Pill logic |
| variable 5 V, CC 2.0 A | XL4015 | 2× SG90 servo + laser |

- Battery + adapter input, BMS + charger working.
- Both rails share one common ground.
- 5-pin header: `1` fixed 5 V, `2` variable 5 V, `3` ESP32 TX,
  `4` ESP32 RX, `5` GND.

## Docs

| File | Contents |
|---|---|
| `PSU.md` | canonical as-built doc — rails, header map, wiring, safety rules |
| `BUILD_NOTES.md` | physical faults found and fixed, bring-up and flashing gotchas |
| `MEDIA.md` | reserved slots for photos, videos and social links |

## Safety — short version

- Never power the whole turret from STM32 USB (measured 4.80 V, ~0.7 A).
- Never feed Blue Pill 5 V from the PSU and USB/ST-Link at the same time.
- No servo current through the Blue Pill 5 V pin.
- ESP32 + Blue Pill on the MP1584 rail; servos + laser on the XL4015 rail.
- Single common ground across both rails.

Full detail in [`PSU.md`](PSU.md).

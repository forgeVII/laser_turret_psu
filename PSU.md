# PSU — As Built

> Canonical power-supply reference for the laser turret.
> Control, firmware and simulator notes live in
> [forgeVII/laser_turret](https://github.com/forgeVII/laser_turret).
> Last updated: 2026-11-23

## 1. Hardware

| Part | Role |
|---|---|
| XL4015 variable buck | 5 V output, constant-current limit set to **2.0 A** — servos + laser rail |
| MP1584 fixed buck | fixed **5 V** rail — ESP32 + Blue Pill logic |
| Battery + adapter input | both power paths working |
| BMS + charger | installed and working |
| Common ground | single GND node across both rails |

Two independent 5 V rails share one ground. The higher-current load (servos,
laser) never passes through the logic rail.

## 2. PSU header map

| Pin | Function |
|---|---|
| 1 | fixed 5 V (MP1584) — ESP32 + Blue Pill |
| 2 | variable 5 V (XL4015, CC 2.0 A) — servos + laser |
| 3 | ESP32 TX (GPIO1) |
| 4 | ESP32 RX (GPIO3) |
| 5 | GND (common) |

## 3. Wiring

| From | To |
|---|---|
| ESP32 VIN/5V | header 1 |
| ESP32 GND | header 5 |
| ESP32 TX (GPIO1) | header 3 → Blue Pill **PA10** |
| ESP32 RX (GPIO3) | header 4 ← Blue Pill **PA9** |
| Blue Pill 5V | header 1 |
| Blue Pill GND | header 5 |
| Servo + and laser + | header 2 |
| All grounds | header 5 |
| Laser red/+ | header 2 |
| Laser − | BC547 collector |
| BC547 emitter | GND |
| Blue Pill PA2 → 1 kΩ | BC547 base |

BC547 orientation, flat face toward you, legs down: **C – B – E** (left / middle /
right).

CP2102, when used for debugging, is **data-only**: TX/RX/GND connected, its 5 V
pin not connected. Remove CP2102 signal wires from header 3/4 for normal running
or its TX contends with Blue Pill PA9.

## 4. Power budget

| Load | Peak |
|---|---|
| 2× SG90 servo + laser | ~1.54 A |
| ESP32 + Blue Pill | ~0.6 A |
| Total | ~11 W |

Measured USB from the STM32: **4.80 V, ~0.7 A** — nowhere near enough to run the
turret. Jitter investigation found **no voltage drop**; the XL4015 current limit
stays at 2.0 A.

## 5. Safety rules — never violate

- Never power the whole turret from STM32 USB.
- Never feed Blue Pill 5 V from the PSU and USB/ST-Link 3.3 V at the same time.
- No servo current through the Blue Pill 5 V pin.
- ESP32 + Blue Pill stay on the MP1584 rail; servos + laser stay on the XL4015 rail.
- Single common ground across both rails.
- `F` and the 2 s link watchdog force laser OFF and hold position — no auto-park.

## 6. Physical notes

- EN must be pressed at PSU power-on (ESP32 EN has a weak/missing pull-up). OTA
  reboots do not need it. Proposed hardware fix: 10 kΩ EN → 3.3 V plus 100 nF
  EN → GND, and verify 10 kΩ BOOT/GPIO0 → 3.3 V.
- Flashing with BOOT held during final reset leaves the ESP32 in download mode —
  press EN once with BOOT released.
- OTA is available: hostname `turret-bridge`, port 3232, password `turret`,
  target `192.168.4.1`. No box opening for later firmware updates.

See [`BUILD_NOTES.md`](BUILD_NOTES.md) for faults found during assembly.

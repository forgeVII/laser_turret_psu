# PSU Build Notes

Physical faults, bring-up checks and flashing gotchas recorded during assembly.
Wiring and rail details are in [`PSU.md`](PSU.md).

## 1. Physical faults found and fixed

1. **Ground continuity:** the GND near the ESP32 5 V pin was **not continuous**
   with the board's other grounds. Use the working GND.
2. **Shorted flash pins:** SD1 and SD0 were shorted on the ESP32 base, killing
   flash reads (`invalid header: 0x00000000`, RTCWDT reset loop). Fixed; boot is
   clean now.

## 2. Bring-up checklist

- Verify both rails at 5 V before connecting boards.
- XL4015 constant-current limit stays at **2.0 A** — do not raise it to chase
  servo jitter; the jitter investigation found **no voltage drop**.
- Confirm all grounds meet at header 5.
- Servos and laser on header 2; ESP32 and Blue Pill on header 1.
- Power on with EN pressed on first boot. OTA reboots do not need it.

## 3. Debug header / CP2102

Do not let the CP2102 dongle drive header 3/4 during normal operation — its TX
would contend with Blue Pill PA9 on ESP32 GPIO3. The dongle can stay on PC USB
if its UART pins are disconnected from the header. It is **data-only**: TX/RX/GND
connected, 5 V not connected.

## 4. Flashing

- ESP32 onboard USB (CP2102) uses DTR/RTS auto-reset — no manual BOOT/EN tapping.
- Holding BOOT through the final reset leaves the chip in download mode: press
  EN once with BOOT released.
- After the one final wired flash, updates go over OTA (`turret-bridge`, port
  3232) — no box opening.
- Proposed hardware robustness fix for EN: 10 kΩ EN → 3.3 V plus 100 nF EN → GND;
  also verify 10 kΩ BOOT/GPIO0 → 3.3 V.

## 5. Media

Photo/video/social slots are reserved in [`MEDIA.md`](MEDIA.md). Add files only
when the content is ready.

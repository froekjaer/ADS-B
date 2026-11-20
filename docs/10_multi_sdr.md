# 10 — Multiple USB SDRs and RF design

## Hardware

For **continuous** ADS-B, AIS and DSC, plan three independent RTL-SDRs:

| Stream | Receive frequencies | Recommended aerial |
|---|---|---|
| ADS-B | 1090 MHz | 1090 MHz ADS-B |
| AIS A/B | 161.975 / 162.025 MHz | Marine VHF / 162 MHz |
| DSC (VHF channel 70) | 156.525 MHz | Marine VHF |

A Pi 5 with proper power supply and active cooling is suitable for a trial system. Reception depends heavily on antenna placement, filtering and coaxial losses. Use proper outdoor grounding and lightning protection advice where appropriate.

## Check USB devices, one at a time

```bash
lsusb
rtl_test -t
rtl_eeprom
```

Power off/stop any software using the dongle before EEPROM operations. **Do not write a serial until you have physically isolated and identified the intended receiver.** Example with only the intended receiver connected:

```bash
rtl_eeprom -s adsb
```

Unplug/replug before verifying. Repeat with the other devices as `ais` and `dsc`. Serial programming may not be supported by every clone. If unsupported, stop and document physical USB-path assignment instead of guessing the selector.

```bash
rtl_eeprom
```

Only one process should open each SDR. The ADS-B Compose uses `ADSB_SDR_SERIAL`; AIS uses `receiver[0].serial` in `ais/config.json`.

## Troubleshooting

- `usb_claim_interface error`: another driver/process may already own the device.
- `lsusb` shows fewer receivers than expected: check power and cables.
- No live messages: check antenna, horizon, serial assignment, gain and service logs.
- Do **not** use blanket `privileged: true` as a first workaround.
- Do not connect unfiltered transmitters to sensitive RTL-SDR inputs; check antenna isolation and front-end overload.

The local Pi should not have three conflicting processes requesting the first default RTL-SDR.

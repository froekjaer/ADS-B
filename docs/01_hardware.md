# 01 — Hardware

## Minimum parts

- Raspberry Pi 4 or Raspberry Pi 5
- Raspberry Pi OS compatible microSD card (16 GB+ recommended)
- Reliable power supply
- RTL-SDR compatible USB receiver
- 1090 MHz ADS-B antenna
- Ethernet cable or Wi-Fi

## Recommended setup

For a Raspberry Pi 5, use the official 27 W USB-C supply and active cooling. The ADS-B workload is light, but stable power matters because the Pi and RTL-SDR share the same USB power budget.

Ethernet is recommended for a fixed feeder. Wi-Fi works, but Ethernet removes one source of instability.

## Antenna placement

ADS-B uses 1090 MHz and is strongly line-of-sight. Antenna height and an unobstructed horizon matter more than Raspberry Pi CPU performance.

Good practice:

1. Place the antenna as high and clear as practical.
2. Keep coaxial cable runs short, especially with thin/high-loss cable.
3. Use a purpose-built 1090 MHz antenna if possible.
4. Consider a 1090 MHz filter or LNA only after the basic receiver works reliably.

## Connect the receiver

With the Pi powered off:

1. Attach the antenna to the RTL-SDR.
2. Connect the RTL-SDR to a USB port.
3. Connect Ethernet if used.
4. Power on the Pi.

Do not connect two ADS-B decoder programs to the same RTL-SDR. In this project, only the Docker `ultrafeeder` container accesses the SDR.

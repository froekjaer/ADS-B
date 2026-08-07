# 02 — Install Raspberry Pi OS

## Recommended OS

Use **Raspberry Pi OS Lite (64-bit)**. A graphical desktop is not needed for a dedicated feeder.

## Flash the card

1. Install Raspberry Pi Imager on your PC or Mac.
2. Select your Raspberry Pi model.
3. Choose **Raspberry Pi OS Lite (64-bit)**.
4. Select the microSD card.
5. In the customization screen:
   - set a hostname, for example `adsb`
   - create a username and strong password
   - configure Wi-Fi if needed
   - set timezone
   - enable SSH
6. Write the card and insert it in the Pi.

## First login

Find the Pi on your network and connect over SSH:

```bash
ssh <username>@adsb.local
```

If mDNS does not work, use the IP address:

```bash
ssh <username>@<PI-IP>
```

## Update the system

```bash
sudo apt update
sudo apt full-upgrade -y
sudo reboot
```

Reconnect after the reboot.

## Install basic tools

```bash
sudo apt install -y git curl rtl-sdr
```

## Verify the RTL-SDR

List USB devices:

```bash
lsusb
```

Then test the RTL-SDR:

```bash
rtl_test -t
```

Press `Ctrl+C` when the test has confirmed the device is present.

If `rtl_test` reports that the device is busy or claimed by a kernel DVB driver, continue to the troubleshooting guide after Docker is installed.

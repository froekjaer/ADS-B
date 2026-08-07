# 08 — Troubleshooting

Work from the bottom of the architecture upward: hardware → readsb → Beast TCP → feeders.

## Useful overview

```bash
cd /opt/adsb
docker compose ps
docker compose logs --tail=100 ultrafeeder
sudo systemctl status fr24feed --no-pager
sudo systemctl status adsbexchange-feed --no-pager
```

## RTL-SDR not found

Check USB:

```bash
lsusb
```

Install RTL-SDR tools if needed:

```bash
sudo apt update
sudo apt install -y rtl-sdr
```

Stop the decoder before running a direct SDR test, because only one process can own the dongle:

```bash
cd /opt/adsb
docker compose stop ultrafeeder
rtl_test -t
```

Press `Ctrl+C`, then start the container again:

```bash
docker compose start ultrafeeder
```

## `usb_claim_interface` or device busy

A Linux DVB kernel driver may have claimed the RTL-SDR.

Check loaded modules:

```bash
lsmod | grep -E 'rtl28|rtl283|dvb_usb'
```

Temporarily remove common modules:

```bash
sudo rmmod dvb_usb_rtl28xxu rtl2832_sdr rtl2832 2>/dev/null || true
```

Then restart the decoder:

```bash
cd /opt/adsb
docker compose restart ultrafeeder
```

If this fixes the problem, blacklist the DVB driver permanently:

```bash
echo 'blacklist dvb_usb_rtl28xxu' | sudo tee /etc/modprobe.d/blacklist-rtl-sdr.conf
sudo reboot
```

## Container keeps restarting

```bash
cd /opt/adsb
docker compose ps
docker compose logs --tail=200 ultrafeeder
```

Common causes:

- wrong SDR serial in `.env`
- RTL-SDR already owned by another program
- USB/power problem
- malformed `.env` value

Validate the Compose configuration:

```bash
docker compose config
```

## Local map works but FR24 does not

First verify Beast port 30005:

```bash
ss -lnt | grep 30005
```

Then check FR24:

```bash
sudo systemctl status fr24feed --no-pager
sudo journalctl -u fr24feed -n 100 --no-pager
sudo cat /etc/fr24feed.ini
```

FR24 should use the existing Beast TCP receiver on `127.0.0.1:30005`, not the RTL-SDR directly.

## Local map works but ADSBexchange does not

```bash
sudo systemctl status adsbexchange-feed --no-pager
sudo journalctl -u adsbexchange-feed -n 100 --no-pager
```

Then use:

https://www.adsbexchange.com/myip/

If the feed client configuration is broken, rerun the current official ADSBexchange feed installer rather than replacing the decoder.

## Very short reception range

Check the RF side before changing software:

1. Antenna is intended for 1090 MHz.
2. Antenna is vertical and unobstructed.
3. Connectors are tight.
4. Coax loss is reasonable.
5. No unnecessary adapters/splitters.
6. Pi power supply is stable.

Start with `ADSB_GAIN=auto`. Manual gain tuning is an optimization, not a first-install requirement.

## Find the Pi temperature / throttling status

```bash
vcgencmd measure_temp
vcgencmd get_throttled
```

A healthy `get_throttled` result is normally:

```text
throttled=0x0
```

## Update everything

System packages:

```bash
sudo apt update
sudo apt full-upgrade -y
```

Decoder container:

```bash
cd /opt/adsb
docker compose pull
docker compose up -d
```

Always test the local decoder before troubleshooting external feeds.

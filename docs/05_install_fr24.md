# 05 — Add Flightradar24

Flightradar24 provides an official Linux/Raspberry Pi feeder installer and ARM64 binaries. We use that supported feeder on the Pi and point it at the Beast TCP stream from readsb.

Official page: https://www.flightradar24.com/share-your-data

## Before installing

Confirm readsb is running:

```bash
cd /opt/adsb
docker compose ps
ss -lnt | grep 30005
```

The Beast source is:

```text
Host: 127.0.0.1
Port: 30005
Format: Beast TCP
```

## Install FR24 feeder

Run the current official installer:

```bash
wget -qO- https://fr24.com/install.sh | sudo bash -s
```

The setup wizard asks for your email, receiver location and feed configuration.

When asked about receiver/input type, select/configure an existing **Beast TCP / Mode-S Beast network** source rather than allowing FR24 to take control of the RTL-SDR.

Use:

```text
Host/IP: 127.0.0.1
Port:    30005
```

The exact wording of the wizard can change between FR24 versions. The important result is that FR24 reads Beast data from `127.0.0.1:30005` and does not use the USB SDR directly.

## Important when feeding multiple networks

Flightradar24 currently advises users who share data with other networks to disable FR24 MLAT.

Set:

```text
MLAT="no"
MLAT-without-gps="no"
```

These settings can be changed via the FR24 web interface or `/etc/fr24feed.ini`.

## Restart and check

```bash
sudo systemctl restart fr24feed
sudo systemctl status fr24feed --no-pager
```

View recent logs:

```bash
sudo journalctl -u fr24feed -n 100 --no-pager
```

## Web status

From another machine on the LAN, open:

```text
http://<PI-IP>:8754
```

You should eventually see the FR24 link connected and aircraft being tracked.

## Configuration file

FR24 normally stores its configuration here:

```text
/etc/fr24feed.ini
```

Inspect it with:

```bash
sudo cat /etc/fr24feed.ini
```

Do not publish your FR24 sharing key.

## Reconfigure later

Use FR24's installed configuration tools or web UI. If reinstalling/upgrading, always preserve the design rule:

```text
RTL-SDR -> readsb -> Beast TCP 30005 -> fr24feed
```

FR24 must not become a second USB decoder.

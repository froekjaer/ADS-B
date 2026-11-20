# 09 — Ubuntu 24.04 on Raspberry Pi 5: preflight

This is an **extension** of the original Raspberry Pi OS guide, not a replacement. Run checks before installing anything.

## 1. Inspect

```bash
cat /etc/os-release
uname -m
lsusb
systemctl --type=service --state=running | grep -Ei 'dump1090|readsb|fr24|piaware|ais|dsc' || true
command -v docker && docker compose version || true
```

Expect Ubuntu 24.04 and `aarch64`. If existing SDR services run, do **not** install a second decoder that would compete for the same USB tuner.

## 2. Install prerequisites

```bash
sudo apt update
sudo apt install -y git curl ca-certificates usbutils rtl-sdr
```

For Docker, follow the maintained [official Ubuntu Engine instructions](https://docs.docker.com/engine/install/ubuntu/) for Ubuntu Noble/ARM64, including the Compose plugin. Do not mix `docker.io` and `docker-ce` installations. The convenience installer is unsuitable as an unquestioned production default.

Verify:

```bash
sudo docker version
sudo docker compose version
```

Docker-group membership grants root-equivalent capabilities; use `sudo docker` if you do not want to delegate those privileges.

## 3. Clone into a new directory (non-destructive)

```bash
git clone https://github.com/froekjaer/ADS-B.git ~/radio-hub
cd ~/radio-hub
cp .env.example .env
nano .env
```

Set **your actual** latitude, longitude, antenna altitude and ADS-B dongle serial. Never copy example coordinates into a live MLAT station.

## 4. Validate without starting

```bash
sudo docker compose config --quiet
sudo docker compose -f docker-compose.yml -f docker-compose.ais.yml config --quiet
```

The second validation assumes `ais/config.json` has been created per [AIS guide](11_ais.md). Validate before `up`; checks do not prove SDR compatibility or image runtime.

Proceed to [USB setup](10_multi_sdr.md), then the original [readsb installation](04_install_readsb.md).

# Raspberry Pi ADS-B Receiver

A simple, maintainable ADS-B receiver for Raspberry Pi using one RTL-SDR, `readsb` in Docker, and feeds to Flightradar24 and ADSBexchange.

This guide is written for a first-time Docker/Linux user and favors vendor-supported installation methods over fragile community feeder images.

## Architecture

```text
RTL-SDR USB (1090 MHz)
        │
        ▼
┌──────────────────────────────┐
│ Docker: ADS-B Ultrafeeder    │
│ (readsb + tar1090)           │
│ Beast TCP :30005             │
└──────────────┬───────────────┘
               │
        ┌──────┴──────┐
        │             │
        ▼             ▼
  Flightradar24   ADSBexchange
    feeder          feeder
```

The decoder owns the RTL-SDR. Feeders only consume the decoded Beast stream, so the SDR is never shared between processes.

## Recommended hardware

- Raspberry Pi 4 or Raspberry Pi 5
- Raspberry Pi OS Lite 64-bit
- Good quality power supply (official PSU recommended)
- microSD card, 16 GB or larger
- RTL-SDR compatible USB receiver
- 1090 MHz ADS-B antenna
- Ethernet recommended; Wi-Fi also works
- Optional 1090 MHz band-pass filter/LNA

## Installation order

1. [Hardware](docs/01_hardware.md)
2. [Install Raspberry Pi OS](docs/02_install_raspberry_pi_os.md)
3. [Install Docker](docs/03_install_docker.md)
4. [Install the readsb decoder](docs/04_install_readsb.md)
5. [Add Flightradar24](docs/05_install_fr24.md)
6. [Add ADSBexchange](docs/06_install_adsbexchange.md)
7. [Test everything](docs/07_testing.md)
8. [Troubleshooting](docs/08_troubleshooting.md)

## Quick start

After Raspberry Pi OS and Docker are installed:

```bash
sudo mkdir -p /opt/adsb
sudo chown "$USER":"$USER" /opt/adsb
cd /opt/adsb
```

Clone this repository:

```bash
git clone https://github.com/froekjaer/ADS-B.git .
```

Create your local configuration:

```bash
cp .env.example .env
nano .env
```

Then start the decoder:

```bash
docker compose up -d
```

Check it:

```bash
docker compose ps
docker compose logs --tail=50 ultrafeeder
```

Open the local map in a browser:

```text
http://<PI-IP>:8080
```

The Beast feed used by FR24 and ADSBexchange is available on TCP port `30005`.

## Design principles

- One SDR, one decoder.
- Feed many networks from the same decoded data.
- Keep secrets and station-specific values in `.env`.
- Prefer official feeder installers for FR24 and ADSBexchange.
- Avoid giving feeder software direct access to the RTL-SDR.
- Keep the installation easy to update and troubleshoot.

## Updating the decoder

```bash
cd /opt/adsb
docker compose pull
docker compose up -d
```

## Useful status commands

```bash
docker compose ps
docker compose logs --tail=100 ultrafeeder
sudo systemctl status fr24feed
sudo systemctl status adsbexchange-feed
```

## Sources / upstream documentation

- SDR-Enthusiasts ADS-B Ultrafeeder: https://github.com/sdr-enthusiasts/docker-adsb-ultrafeeder
- Flightradar24 data sharing: https://www.flightradar24.com/share-your-data
- ADSBexchange feeder instructions: https://www.adsbexchange.com/community/feeder-hub/connect-equipment/

## License

This repository is licensed under the Apache License 2.0. See [LICENSE](LICENSE).

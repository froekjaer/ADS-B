# Raspberry Pi Radio Hub — ADS-B, AIS and DSC

Receive-only radio station on **Raspberry Pi 5 + Ubuntu 24.04 ARM64**, extending the existing ADS-B project.

> **Implementation status:** ADS-B is the pre-existing design; AIS is a proposed, independently startable Docker service; DSC is a documented evaluation track (not a production-ready safety receiver). No end-to-end Pi/SDR hardware test has been performed by this repository update.

## Architecture

```text
RTL-SDR #1, 1090 MHz --> Ultrafeeder/readsb + tar1090 --> TCP Beast :30005 --> FR24 / ADSBexchange
RTL-SDR #2, 162 MHz  --> AIS-Catcher                --> local AIS viewer + optional NMEA feeds
RTL-SDR #3, 156.525  --> DSC evaluation/decoder     --> event log (not yet deployed)
```

**One SDR per live pipeline.** Do not point two independent USB decoders at the same dongle. For reliable identification, give each RTL2832U a distinct EEPROM serial after testing each dongle individually. Antennas must be suited to the respective bands.

## Start here (Ubuntu Pi 5)

1. [Ubuntu installation and preflight](docs/09_ubuntu_pi5.md)
2. [USB receivers, serials and RF](docs/10_multi_sdr.md)
3. [AIS-Catcher installation](docs/11_ais.md)
4. [DSC receiver research and acceptance tests](docs/12_dsc.md)
5. [Operations, security and tests](docs/13_operations.md)

The original [ADS-B guide](docs/04_install_readsb.md), [FR24](docs/05_install_fr24.md), [ADSBexchange](docs/06_install_adsbexchange.md) and [troubleshooting](docs/08_troubleshooting.md) remain available. Older docs assume Raspberry Pi OS; **follow the Ubuntu-specific instructions above** for your Pi 5.

## Deployment stages

- **Stage 0:** Inspect the host. No changes until `lsusb`, `uname -m`, Ubuntu version and existing services are understood.
- **Stage 1:** Start ADS-B decoder alone, confirm tar1090 and live Beast TCP.
- **Stage 2:** Start AIS with a **different dongle**; confirm local viewer and genuine AIS packets.
- **Stage 3:** Enable FR24 and ADSBexchange only after decoder tests. Sharing may expose receiver location.
- **Stage 4:** Investigate/test a separate channel-70 DSC receiver using recorded known-good signals before considering unattended operation.

## Repository files

- `docker-compose.yml`: existing ADS-B decoder (unchanged in this PR).
- `docker-compose.ais.yml`: optional AIS receiver (only started when explicitly included).
- `ais/config.json.example`: AIS serial and local viewer settings.
- `.env.example`: station-specific ADS-B values.
- `docs/`: beginner instructions and runbook.

## Safety

**Do not use this amateur receive-only SDR system as a distress-alerting service.** DSC decoding is experimental until independently validated, and even then does not replace approved marine radio/GMDSS equipment. Do not transmit on DSC channel 70, and respect applicable monitoring/data protection rules. Do not expose feeder ports or web UIs to the Internet without proper authentication/network controls.

## Sources

- [SDR-Enthusiasts Ultrafeeder](https://github.com/sdr-enthusiasts/docker-adsb-ultrafeeder)
- [AIS-Catcher Docker](https://jvde-github.github.io/AIS-catcher-docs/installation/docker/)
- [AIS-Catcher JSON configuration](https://jvde-github.github.io/AIS-catcher-docs/usage/json-configuration/)
- [SDRangel DSC demodulator](https://github.com/f4exb/sdrangel/blob/master/plugins/channelrx/demoddsc/readme.md)

Apache-2.0: see [LICENSE](LICENSE).

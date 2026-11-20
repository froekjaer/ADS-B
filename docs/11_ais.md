# 11 — AIS-Catcher: Ubuntu/Raspberry Pi 5

AIS-Catcher is an actively maintained AIS decoder with official ARM64 Docker support. See [upstream Docker manual](https://jvde-github.github.io/AIS-catcher-docs/installation/docker/) and [JSON schema](https://jvde-github.github.io/AIS-catcher-docs/usage/json-configuration/).

**Prerequisite:** a *second* SDR, physically identified with serial `ais`, and a marine VHF antenna.

## 1. Configure

```bash
cd ~/radio-hub
cp ais/config.json.example ais/config.json
nano ais/config.json
```

The example selects `receiver[0].serial = "ais"`, enables local viewer port 8100 and disables community sharing. For security, avoid publishing station coordinates or AIS data to external aggregators until you explicitly decide to do so.

## 2. Check syntax

```bash
python3 -m json.tool ais/config.json >/dev/null
sudo docker compose -f docker-compose.yml -f docker-compose.ais.yml config --quiet
```

## 3. Start just AIS

```bash
sudo docker compose -f docker-compose.yml -f docker-compose.ais.yml up -d ais-catcher
sudo docker logs --tail=100 ais-catcher
```

Open `http://<PI-LAN-IP>:8100/` from your local network. If no messages appear, first verify the SDR is correctly selected and has a suitable antenna; absence of vessels may also be due to location or reception.

## 4. Keep the two systems separate

ADS-B stays on `30005/tcp` and local map `8080/tcp`. AIS viewer uses `8100/tcp`. **Do not** give AIS-Catcher the ADS-B serial. Three services receiving RF require three dongles for independent operation in the proposed design.

## 5. Feed other AIS services (optional)

AIS-Catcher supports repeated outgoing UDP destinations and TCP/HTTP alternatives. See [official command line](https://jvde-github.github.io/AIS-catcher-docs/usage/cli/) and JSON docs. Do not confuse destination UDP output with an *incoming* port binding.

Example: AISHub has previously supplied `data.aishub.net:3235/udp` for another station, but **do not blindly reuse that station's assignment**. Obtain/confirm a destination for this receiver and document any privacy implications before enabling it.

## 6. Test and rollback

```bash
sudo docker compose -f docker-compose.yml -f docker-compose.ais.yml ps
sudo docker compose -f docker-compose.yml -f docker-compose.ais.yml logs --tail=100 ais-catcher
sudo docker compose -f docker-compose.yml -f docker-compose.ais.yml stop ais-catcher
```

Stopping AIS leaves ADS-B untouched.

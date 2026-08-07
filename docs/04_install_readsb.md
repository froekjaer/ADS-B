# 04 — Install and start readsb

The project uses the SDR-Enthusiasts **ADS-B Ultrafeeder** container. It contains `readsb` for decoding, plus the useful `tar1090` local map.

The important design choice is that **this container alone owns the RTL-SDR**. FR24 and ADSBexchange connect to its network output instead of touching the USB receiver.

## Check configuration

```bash
cd /opt/adsb
nano .env
```

Make sure latitude, longitude, altitude and SDR serial are correct.

## Start the decoder

```bash
docker compose pull
docker compose up -d
```

## Check container status

```bash
docker compose ps
```

You should see `ultrafeeder` as running.

## Check logs

```bash
docker compose logs --tail=100 ultrafeeder
```

Look for evidence that the RTL-SDR was found and readsb is receiving data.

## Open the local map

Find the Pi IP:

```bash
hostname -I
```

Then from another computer on the same network open:

```text
http://<PI-IP>:8080
```

Aircraft should begin appearing when signals are received.

## Verify Beast TCP output

The Compose file exposes readsb Beast data on TCP port `30005`.

Check that the Pi is listening:

```bash
ss -lnt | grep 30005
```

You should see a listening socket on port `30005`.

This is the interface used by the two feeder services:

```text
readsb/ultrafeeder -> TCP 30005 -> FR24
                               -> ADSBexchange
```

## Stop/start commands

```bash
cd /opt/adsb
docker compose stop
docker compose start
```

To recreate the container after configuration changes:

```bash
docker compose up -d --force-recreate
```

## Why Ultrafeeder instead of classic dump1090?

It uses the maintained `readsb` decoder, supports ARM64, includes a local map, and is designed for multiple downstream consumers. The architecture is still the same: one decoder produces Beast data and multiple feeders consume it.

Upstream: https://github.com/sdr-enthusiasts/docker-adsb-ultrafeeder

# 13 — Operation, testing, rollback and security

## Quick checks

```bash
cat /etc/os-release
uname -m
lsusb
cd ~/radio-hub
sudo docker compose ps
sudo docker compose logs --tail=60 ultrafeeder
sudo docker logs --tail=60 ais-catcher
sudo ss -lntup | grep -E ':(30005|8080|8100)\b' || true
```

Web UIs (LAN only): ADS-B `http://<PI-IP>:8080` and AIS `http://<PI-IP>:8100`.

## Safe startup order

1. Start ADS-B decoder and confirm SDR/Beast output.
2. Start AIS and confirm separate SDR.
3. Only then configure external feeders.
4. DSC remains a disabled evaluation track until acceptance tests pass.

## Updates

```bash
cd ~/radio-hub
git status
git pull --ff-only
sudo docker compose -f docker-compose.yml -f docker-compose.ais.yml config --quiet
sudo docker compose -f docker-compose.yml -f docker-compose.ais.yml pull
sudo docker compose -f docker-compose.yml -f docker-compose.ais.yml up -d
```

Avoid upgrading automatically without backup/rollback plan. Pin image versions/digests after initial validation. The commands above may recreate receivers and briefly interrupt traffic.

## Rollback

```bash
sudo docker compose -f docker-compose.yml -f docker-compose.ais.yml stop ais-catcher
```

This stops only AIS. For ADS-B see existing runbook. Preserve `.env` and `ais/config.json` (station-specific, not for Git) and record the last working image digests.

## Security

- Restrict UI ports to your own LAN/VPN; do not port-forward them.
- TCP 30005 should not be openly exposed to the Internet.
- USB device access gives containers access to receiver hardware: use only trusted images and explicit device selection.
- Keep credentials/keys out of Git and logs.
- External feeder programs may disclose approximate receiver location and aircraft/vessel activity.
- Verify retention and privacy/legal requirements before recording or sharing DSC event content.

## Evidence ledger

Document each real-world test with date, host OS, SDR serial/model, antenna, image digest, actual log excerpt, decoded result and remaining failures. `docker compose config --quiet` validates structure, **not live radio reception**.

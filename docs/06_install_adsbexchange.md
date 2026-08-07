# 06 — Add ADSBexchange

ADSBexchange provides an official feed-client installer for Raspberry Pi/Debian systems that already run `readsb`, `dump1090`, PiAware or another compatible decoder.

Official instructions: https://www.adsbexchange.com/community/feeder-hub/connect-equipment/

## Before installing

Confirm the readsb Beast output is available:

```bash
cd /opt/adsb
docker compose ps
ss -lnt | grep 30005
```

The local decoder source is:

```text
Host: 127.0.0.1
Port: 30005
Format: Beast TCP
```

## Install the ADSBexchange feed client

Run the current official installer:

```bash
curl -L -o /tmp/axfeed.sh https://www.adsbexchange.com/feed.sh
sudo bash /tmp/axfeed.sh
```

Follow the prompts.

The installer is designed to detect an existing decoder/feed source. If it asks for the decoder source, use the local Beast stream on port `30005` rather than giving it direct access to the RTL-SDR.

## Verify the feeder

Check the service:

```bash
sudo systemctl status adsbexchange-feed --no-pager
```

Check recent logs:

```bash
sudo journalctl -u adsbexchange-feed -n 100 --no-pager
```

From another device on the same public Internet connection, visit:

https://www.adsbexchange.com/myip/

The feeder should appear after data reaches ADSBexchange.

## Optional stats / account linking

ADSBexchange currently provides a separate stats installer:

```bash
curl -L -o /tmp/axstats.sh https://www.adsbexchange.com/stats.sh
sudo bash /tmp/axstats.sh
```

Their documentation states that the stats component is required if you want to link the receiver to your ADSBexchange account.

## Updating the ADSBexchange client

Use their current update script:

```bash
curl -L -o /tmp/axupdate.sh https://www.adsbexchange.com/feed-update.sh
sudo bash /tmp/axupdate.sh
```

## Architecture after installation

```text
RTL-SDR
   │
   ▼
readsb / ultrafeeder
   │ Beast TCP :30005
   ├──────────────► FR24 feeder
   └──────────────► ADSBexchange feeder
```

Only readsb accesses the USB receiver.

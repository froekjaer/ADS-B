# 07 — Testing the complete receiver

Use this checklist after readsb, FR24 and ADSBexchange are installed.

## 1. Check the RTL-SDR container

```bash
cd /opt/adsb
docker compose ps
```

Expected: `ultrafeeder` is `Up` / running.

Check logs:

```bash
docker compose logs --tail=100 ultrafeeder
```

There should be no repeated USB errors or restart loop.

## 2. Check the local aircraft map

Find the Pi IP address:

```bash
hostname -I
```

Open:

```text
http://<PI-IP>:8080
```

You should see aircraft if there is ADS-B traffic within radio range.

## 3. Check Beast TCP

```bash
ss -lnt | grep 30005
```

Expected: TCP port `30005` is listening.

A simple connection test is:

```bash
timeout 3 bash -c 'cat < /dev/null > /dev/tcp/127.0.0.1/30005' && echo "Port 30005 reachable"
```

## 4. Check Flightradar24

```bash
sudo systemctl status fr24feed --no-pager
sudo journalctl -u fr24feed -n 50 --no-pager
```

Web status:

```text
http://<PI-IP>:8754
```

Look for a connected FR24 link and tracked aircraft.

## 5. Check ADSBexchange

```bash
sudo systemctl status adsbexchange-feed --no-pager
sudo journalctl -u adsbexchange-feed -n 50 --no-pager
```

Then open from another device on the same Internet connection:

https://www.adsbexchange.com/myip/

## 6. Reboot test

A receiver is not finished until it survives a reboot.

```bash
sudo reboot
```

After reconnecting, verify:

```bash
cd /opt/adsb
docker compose ps
sudo systemctl is-active fr24feed
sudo systemctl is-active adsbexchange-feed
```

Then reopen the local map and feeder status pages.

## 7. Final acceptance checklist

- [ ] Pi boots without intervention
- [ ] `ultrafeeder` starts automatically
- [ ] RTL-SDR is detected
- [ ] Aircraft appear on the local map
- [ ] TCP 30005 is listening
- [ ] FR24 is connected
- [ ] ADSBexchange receives data
- [ ] Receiver survives a reboot
- [ ] Antenna latitude/longitude/altitude are accurate
- [ ] FR24 sharing key is not stored in GitHub

# 03 — Install Docker

This project uses Docker Compose for the ADS-B decoder.

## Install Docker Engine

Use Docker's installation script:

```bash
curl -fsSL https://get.docker.com -o get-docker.sh
sudo sh get-docker.sh
rm get-docker.sh
```

## Allow your user to run Docker

```bash
sudo usermod -aG docker "$USER"
```

Log out and back in, or reboot:

```bash
sudo reboot
```

## Verify Docker

After reconnecting:

```bash
docker --version
docker compose version
```

Then test:

```bash
docker run --rm hello-world
```

If you see Docker's `Hello from Docker!` message, Docker is working.

## Create the installation directory

```bash
sudo mkdir -p /opt/adsb
sudo chown "$USER":"$USER" /opt/adsb
cd /opt/adsb
```

Clone the project:

```bash
git clone https://github.com/froekjaer/ADS-B.git .
```

Create the local environment file:

```bash
cp .env.example .env
nano .env
```

Enter the receiver's real latitude, longitude and antenna altitude.

Example:

```text
FEEDER_TZ=Europe/Copenhagen
FEEDER_LAT=55.12345
FEEDER_LONG=9.12345
FEEDER_ALT_M=42
ADSB_SDR_SERIAL=00000001
ADSB_GAIN=auto
ADSB_SDR_PPM=0
```

Do not put FR24 keys or other credentials into the public repository.

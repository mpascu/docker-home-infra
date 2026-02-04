# 🏠 SmartHomeStack
A docker-compose stack to deploy a full homelab with media management, smart home automation, monitoring, and utility services. All persistent data is stored in the `./config` folder and Duplicati is provided for backups.

## 📁 Repository Structure

```
SmartHomeStack/
├── docker-compose.yml      # Main compose file (core infrastructure)
├── compose/                # Additional service modules
│   ├── ai.yml              # AI services (Open WebUI, Ollama)
│   ├── dashboards.yml      # Dashboards (Heimdall, Organizr, Homarr)
│   ├── ebooks.yml          # E-book management (Calibre, Calibre-web)
│   ├── nextcloud.yml       # File hosting (Nextcloud + MariaDB)
│   ├── immich.yml          # Photo management (Immich)
│   ├── smarthome.yml       # Smart home (Home Assistant, Zigbee, MQTT)
│   └── media.yml           # Media management (Plex, Sonarr, Radarr, etc.)
├── config/                 # Persistent data for all services (gitignored)
├── scripts/                # Helper scripts
│   └── Makefile            # Common operations (up, down, update, logs, etc.)
├── .env.example            # Environment variables template
└── README.md
```

## 📸 Screenshots

![Heimdall screenshot](https://raw.githubusercontent.com/mpascu/SmartHomeStack/master/heimdall.png)

![Organizr screenshot](https://raw.githubusercontent.com/mpascu/SmartHomeStack/master/organizr.png)

## 🧰 Tools included

### 🔧 Core Infrastructure (`docker-compose.yml`)
- [Traefik](https://traefik.io/): Reverse proxy with automatic HTTPS certificate management
- [Pi-hole](https://pi-hole.net/): DNS sinkhole that blocks ads and malware
- [PostgreSQL](https://www.postgresql.org/): Database for Restyaboard
- [Adminer](https://www.adminer.org/): Database management UI
- [Postfix](https://hub.docker.com/r/boky/postfix): SMTP relay for sending emails
- SFTP: File transfer server

### ☁️ File Hosting (`compose/nextcloud.yml`)
- [Nextcloud](https://nextcloud.com/): Self-hosted file sync and share platform
- [MariaDB](https://mariadb.org/): Database for Nextcloud

### 📷 Photo Management (`compose/immich.yml`)
- [Immich](https://immich.app/): Self-hosted photo and video backup solution with ML-powered features

### 📊 Dashboards (`compose/dashboards.yml`)
- [Heimdall](https://heimdall.site/): Application dashboard
- [Organizr](https://github.com/causefx/Organizr): Unified tab interface for all services
- [Homarr](https://homarr.dev/): Modern dashboard with Docker integration

### 🐳 Docker Management
- [Portainer](https://www.portainer.io/): Docker container management UI

### 📈 Monitoring & Maintenance
- [Netdata](https://www.netdata.cloud/): Real-time system performance monitoring
- [Uptime Kuma](https://github.com/louislam/uptime-kuma): Service uptime monitoring
- [Speedtest Tracker](https://github.com/henrywhitaker3/speedtest-tracker): Internet speed history
- [Watchtower](https://github.com/containrrr/watchtower): Automatic container updates
- [Duplicati](https://www.duplicati.com/): Encrypted backup scheduler
- [Crontab UI](https://github.com/alseambusher/crontab-ui): Web-based cron job manager

### 🔌 Remote Access & Utilities
- [Guacamole](https://guacamole.apache.org/): Web-based remote desktop (VNC, RDP, SSH)
- [Transmission](https://transmissionbt.com/): BitTorrent client
- [Restyaboard](https://restya.com/board): Kanban board

### 📚 E-books (`compose/ebooks.yml`)
- [Calibre](https://calibre-ebook.com/): E-book manager
- [Calibre-web](https://github.com/janeczku/calibre-web): Web interface for Calibre library

### 💡 Smart Home (`compose/smarthome.yml`)
- [Home Assistant](https://www.home-assistant.io/): Open source home automation
- [Zigbee2mqtt](https://www.zigbee2mqtt.io/): Zigbee device bridge
- [Mosquitto](https://mosquitto.org/): MQTT broker
- [ESPHome](https://esphome.io/): ESP device configuration and management
- [Music Assistant](https://music-assistant.io/): Music streaming server
- [Whisper](https://github.com/rhasspy/wyoming-whisper): Speech-to-text for voice control
- [Piper](https://github.com/rhasspy/piper): Text-to-speech engine
- [OpenWakeWord](https://github.com/rhasspy/wyoming-openwakeword): Wake word detection

### 🎬 Media Management (`compose/media.yml`)
- [Plex](https://www.plex.tv/): Media server and streamer
- [Tautulli](https://tautulli.com/): Plex monitoring and statistics
- [Ombi](https://ombi.io/): Media request management
- [Sonarr](https://sonarr.tv/): TV series manager
- [Radarr](https://radarr.video/): Movie manager
- [Lidarr](https://lidarr.audio/): Music manager
- [Bazarr](https://www.bazarr.media/): Subtitle manager
- [Jackett](https://github.com/Jackett/Jackett): Torrent indexer proxy

### 🤖 AI (`compose/ai.yml`)
- [Open WebUI](https://github.com/open-webui/open-webui): Chat interface for LLMs

## ✅ Prerequisites
- A domain name
- Docker and Docker Compose installed

## 🚀 Installation
1. Copy `.env.example` to `.env` and configure credentials and storage paths
2. Generate a secure key for Homarr: `openssl rand -hex 32`
3. Launch the stack:
   ```bash
   docker compose up -d
   ```

## 🛠️ Helper Commands
A Makefile is provided in `scripts/` for common operations:
```bash
cd scripts
make up        # Start all services
make down      # Stop all services
make logs      # Follow logs for all services
make update    # Pull latest images and restart
make status    # Show container status
make help      # Show all available commands
```

## ⚙️ Enabling/Disabling Services

### Optional compose files
The stack is split into multiple compose files in the `compose/` folder. By default, `smarthome.yml` and `ai.yml` are included. To enable media services, uncomment the `media.yml` include in `docker-compose.yml`:
```yaml
include:
  - compose/ai.yml
  - compose/smarthome.yml
  - compose/media.yml  # Uncomment to enable
```

### Disabling individual services
To disable a specific service without removing it from the compose file, add the `DONOTDEPLOY` profile:
```yaml
myservice:
  image: someimage
  profiles:
    - DONOTDEPLOY
  # ... rest of config
```
Services with this profile will be skipped during `docker compose up` unless explicitly requested.

## 🔨 Post-installation configuration

### Pi-hole DNS setup (Ubuntu/Debian)
Disable the system DNS resolver so Pi-hole can bind to port 53:
```bash
sudo systemctl disable systemd-resolved.service
sudo systemctl stop systemd-resolved.service
```
Edit NetworkManager config:
```bash
sudo nano /etc/NetworkManager/NetworkManager.conf
```
Add `dns=default` under `[main]`:
```
[main]
plugins=ifupdown,keyfile
dns=default
```
Rename the existing resolv.conf and restart NetworkManager:
```bash
sudo mv /etc/resolv.conf /etc/resolv.conf.bak
sudo service network-manager restart
```

### Nextcloud trusted domain
Add your domain to `config/nextcloud/config/config.php`:
- Under `'trusted_domains'`
- At `'overwrite.cli.url'`

For external storage and reverse proxy support, also add:
```php
'check_data_directory_permissions' => false,
'overwriteprotocol' => 'https',
```

### Media services security
⚠️ **Important:** Set up authentication on Sonarr, Radarr, and Lidarr immediately after first deployment, otherwise they will be exposed without password protection.

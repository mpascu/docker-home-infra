# docker-home-infra 
This repo consists mainly in a docker-composed file ready to spin up a bunch of useful media, file-sharing, programming, web tools, databases... All the persistent data is stored in the ./config folder and Duplicati is provided in order to backup all data.

## Screenshots

![heimdall sceenshot](https://github.com/mpascu/docker-home-infra/heimdall.png)

![Organizr sceenshot](https://github.com/mpascu/docker-home-infra/organizr.png)

## Tools included:

- [Traefik](https://traefik.io/): Reverse proxy to be in front of all web services
- [Nextcloud](https://nextcloud.com/): Nextcloud is the most deployed on-premises file share and collaboration platform. 
- [MariaDB](https://mariadb.org/): Database required by nextcloud
- [Organizr](https://github.com/causefx/Organizr): Organizr allows you to setup "Tabs" that will be loaded all in one webpage. 
- [Pi-hole](https://pi-hole.net/): The Pi-hole® is a DNS sinkhole that protects your devices from unwanted content, without installing any client-side software.
- [Heimdall](https://heimdall.site/): Heimdall Application Dashboard is a dashboard for all your web applications.
- [Calibre](https://calibre-ebook.com/): calibre is a powerful and easy to use e-book manager
- [Calibre-web](https://github.com/janeczku/calibre-web): Calibre-Web is a web app providing a clean interface for browsing, reading and downloading eBooks using an existing Calibre database.
- [Portainer](https://www.portainer.io/): allows you to easily build, manage and maintain Docker environments. 
- [Netdata](https://www.netdata.cloud/): Instantly diagnose slowdowns and anomalies in your infrastructure with thousands of metrics, interactive visualizations, and insightful health alarms.
- [Guacamole](https://guacamole.apache.org/): clientless, web-based remote desktop gateway. It supports standard protocols like VNC, RDP, and SSH.
- [Transmission](https://transmissionbt.com/): BitTorrent client.
- [Restyaboard](https://restya.com/board): Kanban board, management tool.
- [Duplicati](https://www.duplicati.com/): Backup tool
- [Postgresql](https://www.postgresql.org/): Database needed for Restyaboard
- [Adminer](https://www.adminer.org/): Database management tool.
- Bazarr, Sonarr, Radarr, Lidarr: download managers
- Jackett: indexer scrapping
- [Plex](https://www.plex.tv/): Media file organizer and streamer.
- [Watchtower](https://github.com/containrrr/watchtower): A process for automating Docker container base image updates.
- [Duplicati](https://www.duplicati.com/): Free backup software to store encrypted backups online. Configured to backup the persistent data of the other containers.
- sftp: FTP server
- Postfix: Mail sender
- [HomeAssistant](https://www.home-assistant.io/): Open source home automation that puts local control and privacy first. Powered by a worldwide community of tinkerers and DIY enthusiasts. 
- [OpenHab](https://www.openhab.org/): a vendor and technology agnostic open source automation software for your home.

## Prerequisites:
	- Some domain
	- Docker installed
	- Docker-compose installed

## Installation
 1. Create docker network
    ```bash
    docker network create nextcloud_network
    ```
 2. Setup the credentials and storage path in .env file
 3. Launch docker compose setup
    ```bash
    docker-compose up -d
    ```

## Post-instalation configuration

 1. First, disable and stop Ubuntu's DNS resolver using the following two commands, otherwise pi-hole can't start because port 53 will be in use:
    ```bash
    sudo systemctl disable systemd-resolved.service
    sudo systemctl stop systemd-resolved.service
    ```
    Open network manager configuration using the following command for editing:
    ```bash
	sudo nano /etc/NetworkManager/NetworkManager.conf
    ```
    Add dns=default under [main] so that the file contents look like what is shown below:
    ```
		[main]
		plugins=ifupdown,keyfile
		dns=default
    ```
	Then, remove or even better rename /etc/resolv.conf file (it is a symbolic link) using the the following command:
    ```bash
	sudo mv /etc/resolv.conf /etc/resolv.conf.bak

	restart your network manager using the following command.

	sudo service network-manager restart
    ```

 2. Set your domain as trusted domain on Nextcloud config
	If you try to access your nextcloud instance right now, you will receive a message telling that this is not a trusted domain. To fix this you should setup your domain in two parameters of the file "app/config/config.php": first one under 'trusted_domains' =>  and the other one at 'overwrite.cli.url'

    Setting up External Storage
    ```
    'check_data_directory_permissions' => false,
    ```
    Needed to go through the reverse proxy:
    ```
    ‘overwriteprotocol’ => ‘https’,
    ```

3. IMPORTANT: Remember to setup passwords at first deployment to sonarr, radarr and lidarr, otherwise they will be exposed to the internet without password. 

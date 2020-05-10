# docker-home-infra 
Docker compose file for setting up a bunch of useful file sharing, networking, programming web applications that you can run easily with docker.

Tools included:

- [Pi-hole](https://pi-hole.net/): The Pi-hole® is a DNS sinkhole that protects your devices from unwanted content, without installing any client-side software.
- [Gitpod](https://www.gitpod.io/): Gitpod is an online IDE for GitHub and GitLab that launches ready-to-code dev environments for any project with a single click.
- [Heimdall](https://heimdall.site/): Heimdall Application Dashboard is a dashboard for all your web applications.
- [Calibre](https://calibre-ebook.com/): calibre is a powerful and easy to use e-book manager
- [Calibre-web](https://github.com/janeczku/calibre-web): Calibre-Web is a web app providing a clean interface for browsing, reading and downloading eBooks using an existing Calibre database.
- [Portainer](https://www.portainer.io/): allows you to easily build, manage and maintain Docker environments. 
- [Netdata](https://www.netdata.cloud/): Instantly diagnose slowdowns and anomalies in your infrastructure with thousands of metrics, interactive visualizations, and insightful health alarms.
- [Restyaboard](https://restya.com/board): Kanban board, management tool.
- [Nextcloud](https://nextcloud.com/): Nextcloud is the most deployed on-premises file share and collaboration platform. 

# Prerequisites:
	- Some domain
	- Docker installed
	- Docker-compose installed

# Installation
- Create docker network
$ docker network create nextcloud_network
- Launch docker compose setup
docker-compose up -d

# Post-instalation 

	# First, disable and stop Ubuntu's DNS resolver using the following two commands, otherwise pi-hole can't start because port 53 will be in use:

		sudo systemctl disable systemd-resolved.service
		sudo systemctl stop systemd-resolved.service

		Next, open network manager configuration using the following command for editing:

		sudo nano /etc/NetworkManager/NetworkManager.conf

		#Add dns=default under [main] so that the file contents look like what is shown below:

		[main]
		plugins=ifupdown,keyfile
		dns=default

		Then remove or even better rename /etc/resolv.conf file (it is a symbolic link) using the the following command:

		sudo mv /etc/resolv.conf /etc/resolv.conf.bak

		restart your network manager using the following command.

		sudo service network-manager restart



# Add few lines to nginx reverse proxy, so uploads of big files doesn't get interrupted
	#Get a shell into nginx container
	sudo docker container exec -it 6c9 bash

	#Install nano to edit files
	apk add nano


	#Add to nano /etc/nginx/conf.d/default.conf
	client_max_body_size 16000m;
	proxy_max_temp_file_size 8192m;
	proxy_set_header Connection "Keep-Alive";
	
	#Reload nginx
	nginx -s reload


#Set your domain as trusted domain on Nextcloud config
	If you try to access your nextcloud instance right now, you will receive a message telling that this is not a trusted domain. To fix this you should setup your domain in two parameters of the file "app/config/config.php": first one under 'trusted_domains' =>  and the other one at 'overwrite.cli.url'

#Setting up External Storage
 'check_data_directory_permissions' => false,

# nginx
‘overwriteprotocol’ => ‘https’,

# Guide to run graphql api on Raspberry Pi #

Install and deploy a GraphQL API as part of a [GRANDstack](https://grandstack.io/) web application on a Raspberry Pi. Steps requiring specific changes, modifications, and updates have been provided. Official documentation has been referenced by links.

This guide assumes you currently have a GRANDstack application on your local machine. If not, you can get started with GRANDstack [here](https://grandstack.io/docs/getting-started-neo4j-graphql)!

## Equipment ##
* Raspberry Pi
	* version: 3+
	* RAM: 1 GB
* Micro SD Card
* Wifi Adapter
* MacBook Pro
	* Location of current GRANDstack application
	* To build docker images
	* [Sublime Text Editor](https://www.sublimetext.com/3)

## References ##
- [Raspberry Pi Documentation](https://www.raspberrypi.org/documentation/)
- [Yes, you can run Docker on Raspian](https://withblue.ink/2019/07/13/yes-you-can-run-docker-on-raspbian.html)
- [How to Install and Use Docker on Raspberry Pi](https://linuxize.com/post/how-to-install-and-use-docker-on-raspberry-pi/)
- [Getting started with Docker on your Raspberry Pi](https://blog.hypriot.com/getting-started-with-docker-on-your-arm-device/)

## Steps ##
1. On MacBook Pro
	1. [Install latest Raspberry Pi OS on SD Card](https://www.raspberrypi.org/documentation/installation/installing-images/)
		* **Raspbian Buster Lite** is perfect for just using the terminal!
1. On Pi
	1. Insert SD card into Pi, connect peripherals (monitor and keyboard) 
	1. [Update keyboard layout](https://scribles.net/changing-keyboard-layout-on-raspberry-pi/)
	1. [Connect to Wifi](https://www.raspberrypi.org/documentation/configuration/wireless/wireless-cli.md)
	1. [Enable SSH](https://www.raspberrypi.org/documentation/remote-access/ssh/)
	1. **Create SSH key for SSH'ing**
		1. **[Come back to later](https://www.raspberrypi.org/documentation/configuration/security.md)**
	1. **SSH Hardening (securing SSH)**
1. On MacBook Pro
	1. [Get and Install Docker](https://docs.docker.com/get-docker/)
		- In this guide I am using [Mac](https://docs.docker.com/docker-for-mac/install/).
	1. [Get and Install Docker Compose](https://docs.docker.com/compose/install/)
	1. [Sign up for or sign into Docker Hub](https://hub.docker.com/)
	1. [Create Docker Hub repository and set to private](https://docs.docker.com/docker-hub/repos/)
	1. Build docker image (MacBook Pro)
		1. Navigate to api folder locally
		1. Copy `API/Dockerfile` as `API/Dockerfile.pi`
			1. Open file and change `node` to `arm32v7/node`
				* [link](https://hub.docker.com/r/arm32v7/node/)
		1. Create docker image: `sudo docker build -t <your dockerhub username>/<your private repo>:custom-tag -f Dockerfile.pi .`
		1. Run `docker images` to make sure it was completed
	1. Push docker image to docker hub: 
		* `docker push <your dockerhub username>/<your private repo>:custom-tag`
		* You may be prompted for `<username>` and `<password>`. If docker is running locally and you are logged in you may not be prompted
1. On Pi
	1. Log in to Docker: 
		- `sudo docker login -u <username> -p <password>`
	1. Pull from Docker: 
		- `docker pull <you dockerhub username>/<your private repo>:custom-tag`
	1. `sudo docker-compose up -d`	
	1. Get your docker-compose.yml file from your github repository:
		- `wget https://raw.githubusercontent.com/<github username>/<github repo>/master/docker-compose.yml > docker-compose.yml`
	1. Update "docker-compose.yml"
		- `nano docker-compose.yml`
		- Update the "docker-compose.yml" to read: **how to format this???**
			
			>version: '3'
			> 
			>services:
			>  api:
			>    image: <your dockerhub username>/<your private repo>:custom-tag
			>	ports:
			>	  - 4000:4000
			>	environment:
			>	  - NEO4J_URI=bolt://<MAC ip address on lan>:4001/graphql
			>	  - NEO4J_USER=<neo4j username>
			>     - NEO4J_PASSWORD=<neo4j password>

	1. Start docker container:
		- `docker run --rm -it -e NEO4J_URI=bolt://<MAC ip address on lan> -e NEO4J_USER=<username> -e NEO4J_PASSWORD=<password> -p 4001:4001 <your dockerhub username>/<your private repo>:custom-tag`
	1. If you need to stop a container:
		1. Identify running docker containers:
			- `docker ps`
		1. To stop a docker container:
			- `docker stop <container id>`
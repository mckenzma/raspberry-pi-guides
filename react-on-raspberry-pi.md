# Guide to run React on Raspberry Pi #

## Equipment ##
* Raspberry Pi
	* version: 3+
	* RAM: 1GB
* Micro SD Card
* Wifi Adapter
* MacBook Pro
	* To build docker images

## References ##

## Steps ##
1. Install latest Raspian OS
	1. [Download](https://www.raspberrypi.org/downloads/raspbian/)
		* **Raspbian Buster Lite** is perfect for just using the terminal.
	1. Install Raspian on SD Card 
1. [Changing Keyboard Layout](https://scribles.net/changing-keyboard-layout-on-raspberry-pi/)
1. Setup network (connect to internet)
1. Get SSH access
1. Create SSH key for SSH'ing
	1. [Come back to later](https://www.raspberrypi.org/documentation/configuration/security.md)
1. SSH Hardening (securing SSH)
1. Install Docker and Docker Compose
1. Create account on Docker Hub
	1. Create Docker Hub Repo
	1. Set repo to private
1. Build docker image (MacBook Pro) **UPDATE HERE DOWN**
	1. Navigate to ui folder locally
	1. Copy UI/Dockerfile as UI/Dockerfile.pi
		1. Open file and change `node` to `arm32v7/node`
			* [link](https://hub.docker.com/r/arm32v7/node/)
	1. `sudo docker build` **is this needed?**
	1. Create docker image: `docker build -t <your dockerhub username>/<your private repo>:custom-tag -f Dockerfile.pi .`
	1. Run `docker images` to make sure it was completed
1. Push docker image to docker hub: `docker push <your dockerhub username>/<your private repo>:custom-tag`
	* You may be prompted for **username** and **password**
	* If docker is running locally and you are logged in you may not be prompted
1. On Pi
	1. Log in to Docker: `sudo docker login -u <username> -p <password>`
	1. Pull from Docker: `docker pull <you dockerhub username>/<your private repo>:custom-tag`
	1. `sudo docker-compose up -d` **is this needed?**
	1. `wget https://raw.githubusercontent.com/<github username>/<github repo>/master/docker-compose.yml > docker-compose.yml`
	1. **add info about editing this file**
	1. `docker run --rm -it -e NEO4J_URI=bolt://<MAC ip address on lan> -e NEO4J_USER=<username> -e NEO4J_PASSWORD=<password> -p 4001:4001 <your dockerhub username>/<your private repo>:custom-tag`
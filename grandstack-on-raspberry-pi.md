# Guide to run GRANDstack application on Raspberry Pi #

Install and deploy a React front-end, GraphQL API, and Neo4j database as part of a [GRANDstack](https://grandstack.io/) web application on a Raspberry Pi. Steps requiring specific changes, modifications, and updates have been provided. Official documentation has been referenced by links.

This guide assumes you have a GRANDstack application on your local machine. If not, you can get started with GRANDstack [here](https://grandstack.io/docs/getting-started-neo4j-graphql)! Additionally, this guide assumes basic understanding of working with a raspeberry pi.

## Equipment ##
* Raspberry Pi
  * Version 4
  * RAM: 8GB
* Micro SD Card: 64GB
* MacBook Pro
  * GRANDstack development machine
  * Build docker images
  * VSCode & Sublime Text Editor

## References ##
- [Raspberry Pi Documentation](https://www.raspberrypi.org/documentation/)
- [Yes, you can run Docker on Raspian](https://withblue.ink/2019/07/13/yes-you-can-run-docker-on-raspbian.html)
- [How to Install and Use Docker on Raspberry Pi](https://linuxize.com/post/how-to-install-and-use-docker-on-raspberry-pi/)
- [Getting started with Docker on your Raspberry Pi](https://blog.hypriot.com/getting-started-with-docker-on-your-arm-device/)

&nbsp; <!-- extra line break -->

# Steps #
## MacBook Pro (Development Machine) ##
1. [Install latest Raspberry Pi OS on SD Card](https://www.raspberrypi.org/documentation/installation/installing-images/). **Rasbian Buster Lite** is perfect for keeping the OS lite and just using the terminal!
1. [Get and Install Docker](https://docs.docker.com/get-docker/). In this guide I am using [Mac](https://docs.docker.com/docker-for-mac/install/).
1. [Get and Install Docker Compose](https://docs.docker.com/compose/install/)
1. [Sign up for and/or sign into Docker Hub](https://hub.docker.com/)
1. [Create Docker Hub repository and set to private](https://docs.docker.com/docker-hub/repos/)
1. Build docker image (MacBook Pro)
    1. Create `api` and `ui` docker images. The following steps should be done in both the `api` and `ui` directory. For whichever directory you are working in substitute either `api` or `ui` in where you see `<directory>`.
    1. Navigate to `<directory>` locally in GRANDstack project
    1. Make copy of `<directory>/Dockerfile` and save as `<directory>/Dockerfile.pi`
    1. Update `Dockerfile.pi` file changing `FROM node:10` to read as `FROM arm32v7/node:10`. Additional information regarding this step can be found [here](https://hub.docker.com/r/arm32v7/node/).
    1. Create docker image: `sudo docker build -t <your dockerhub username>/<your private repo>:custom-tag -f Dockerfile.pi .`
    1. Run `docker images` to make sure it was completed
1. Push docker image to docker hub: 
    1. `docker push <your dockerhub username>/<your private repo>:custom-tag`
    1. You may be prompted for `<username>` and `<password>`. If docker is running locally and you are logged in you may not be prompted

## Raspberry Pi ##

### Basic Pi Setup ###
1. Insert formatted SD card with selected Pi OS into Pi, connect peripherals (monitor and keyboard).
1. [Update keyboard layout](https://scribles.net/changing-keyboard-layout-on-raspberry-pi/)
1. [Connect to Wifi](https://www.raspberrypi.org/documentation/configuration/wireless/wireless-cli.md)
1. [Enable SSH](https://www.raspberrypi.org/documentation/remote-access/ssh/).
<!-- 
1. **Create SSH key for SSH'ing**
	1. **[Come back to later](https://www.raspberrypi.org/documentation/configuration/security.md)**
1. SSH Hardening (securing SSH)
	1. **come back to later**
-->

### Neo4j ###
1. Identify & Install Neo4j Version using [Neo4j Debian Packages](https://debian.neo4j.com/)
1. Install Java: Will need to install OpenJDK 11 runtime as stated [here](https://neo4j.com/docs/operations-manual/current/installation/linux/debian/?_ga=2.196841515.687545963.1608309763-1865895428.1586215484)
1. Update `neo4j.conf`
    1. Navigate to directory: `cd /etc/neo4j/`
    1. Modify `neo4j.conf`
        1. Update `dbms.connector` settings:

            ```
            dbms.default_listen_address=0.0.0.0
            dbms.default_advertised_address=<pi ip address>
            # Bolt connector
            dbms.connector.bolt.enabled=true
            #dbms.connector.bolt.tls_level=OPTIONAL
            dbms.connector.bolt.listen_address=0.0.0.0:7687
                
             # HTTP Connector. There can be zero or one HTTP connectors.
             dbms.connector.http.enabled=true
             dbms.connector.http.listen_address=0.0.0.0:7474
                
             # HTTPS Connector. There can be zero or one HTTPS connectors.
             dbms.connector.https.enabled=false
             #dbms.connector.https.listen_address=0.0.0.0:7473
            ```
        1. Update `dbms.security` settings:
            ```
            dbms.security.procedures.unrestricted=apoc.*
            dbms.security.procedures.whitelist=apoc.coll.*,apoc.load.*,apoc.*
            ```
            <!-- TODO: Address 0.0.0.0 for sensitive data -->  
1. Install APOC
    1. [Identify APOC release (.jar file) to install](https://github.com/neo4j-contrib/neo4j-apoc-procedures/releases/)
    1. Download APOC .jar file: `sudo wget https://github.com/neo4j-contrib/neo4j-apoc-procedures/releases/download/4.0.0.15/apic-4.0.0.15-all.jar`
    1. Copy file to `plugins` directory: `cp apoc-4.0.0.15.-all.jar /var/lib/neo4j/plugins/`
    1. Navigate to `plugins` directory: `cd /var/lib/neo4j/plugins/`
    1. `sudo chown neo4j:neo4j *.jar`
    1. start Neo4j service: `sudo neo4j start`
        - If running, restart Neo4j service: `systemctl restart neo4j`
        - To stop neo4j: `sudo neo4j stop`
<!-- TODO
1.Add step to connect external SSD
https://www.raspberrypi.org/documentation/configuration/external-storage.md
-->

### React & GraphQL ###
1. Install docker <!-- TODO: expand this -->
1. Log in to Docker: `sudo docker login -u <username> -p <password>`
1. Pull from Docker: `docker pull <you dockerhub username>/<your private repo>:custom-tag`
1. `sudo docker-compose up -d`	
1. Get your docker-compose.yml file from your github repository: `wget https://raw.githubusercontent.com/<github username>/<github repo>/master/docker-compose.yml > docker-compose.yml`
1. Update `docker-compose.yml` to read as follows:
    ```
    version: '3'
    
    services: 
      api:
        image: <docker username>/<docker private repo>:<custom tag>
        ports:
          - 4001:4001
        environment:
          - NEO4J_URL=bolt://<MAC ip address on lan>:7687
          - NEO4J_USER=<neo4j username>
          - NEO4J_PASSWORD=<neo4j password>
      ui:
        image: <docker username>/<docker private repo>:<custom tag>
        ports:
          - 3000:3000
        environment:
          - REACT_APP_GRAPHQL_URI=http://<MAC ip address on lan>:4001/graphql
          - REACT_APP_MAPBOX_TOKEN=<key>
    ```
1. Start docker container: `docker run --rm -it -e NEO4J_URI=bolt://<MAC ip address on lan> -e NEO4J_USER=<username> -e NEO4J_PASSWORD=<password> -p 4001:4001 <your dockerhub username>/<your private repo>:custom-tag`
1. To stop a container: 
    1. Identify running docker containers: `docker ps`
    1. To stop a docker container: `docker stop <container id>`



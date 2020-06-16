# Guide to run Neo4j #

## Equipment ##
* Raspberry Pi
	* version: 4
	* RAM: 4 GB
* Micro SD Card
* 
* MacBook Pro
	* To build docker images

## References ##

## Steps ##
1. Reflash latest Raspian OS
	1. Raspberry Pi 4 came with latest NOOBS
	1. Download
	1. Install Raspian on SD Card
1. Change Keyboard Layout
1. Setup network (connect to internet)
1. Get SSH access
1. Create SSH Key for SSH-ing
1. SSH Hardening (securing SSH)
1. Install Docker and Docker Compose
1. Create account on Docker Hub
	1. Create Docker Hub Repo
	1. Set repo to private
1. Build Docker image (MacBook Pro)
	1. Navigate to api folder locally
	1. ...
1. Push Docker image to docker hub
1. On Pi

update neo4.conf
navigate to folder `cd /etc/neo4j/`
update `neo4j.conf` file 
`dbms.connectors.default_listen_address=0.0.0.0
dbms.connector.https.listen_address=0.0.0.0:7473
dbms.connector.http.listen_address=0.0.0.0:7474
dbms.connector.bolt.listen_address=0.0.0.0:7687`
**later will want to address 0.0.0.0 for sensitive data** 
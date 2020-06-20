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
`dbms.default_listen_address=0.0.0.0
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
#dbms.connector.https.listen_address=0.0.0.0:7473`
**later will want to address 0.0.0.0 for sensitive data** 

**install apoc**
1. identify release to install @ https://github.com/neo4j-contrib/neo4j-apoc-procedures/releases/
	1. identify .jar file
1. from pi
	1. `sudo wget https://github.com/neo4j-contrib/neo4j-apoc-procedures/releases/download/4.0.0.15/apic-4.0.0.15-all.jar`
	1. `cp apoc-4.0.0.15.-all.jar /var/lib/neo4j/plugins/`
	1. is this needed?
		1. cd `/var/lib/neo4j/plugins/`
		1. `sudo chown neo4j:neo4j *.jar`
	1. `systemctl restart neo4j`
1. update `neo4j.conf`
	1. uncomment out this line: `dbms.security.procedures.unrestricted=apoc.*`
	1. update this line: `dbms.security.procedures.whitelist=apoc.coll.*,apoc.load.*,apoc.*`


# Guide to run Neo4j on Raspberry Pi #

Install and deploy a Neo4j database as part of a [GRANDstack](https://grandstack.io) web application on a Raspberry Pi. Steps requiring specific changes, modifications, and updates have been provided. Official documentation has been referenced by links.

## Equipment ##
* Raspberry Pi
	* version: 4
	* RAM: 4 GB
* Micro SD Card
* MacBook Pro
	* 

## References ##

## Steps ##
1. On MacBook Pro
	1. Install latest Raspian OS
		1. [[Install latest Raspberry Pi OS on SD Card](https://www.raspberrypi.org/documentation/installation/installing-images/)
			* **Raspbian Buster Lite** is perfect for just using the terminal!
1. On Pi
	1. Insert SD card into Pi, connect peripherals (monitor and keyboard) 
	1. [Update keyboard layout](https://scribles.net/changing-keyboard-layout-on-raspberry-pi/)
	1. [Connect to Wifi](https://www.raspberrypi.org/documentation/configuration/wireless/wireless-cli.md)
	1. [Enable SSH](https://www.raspberrypi.org/documentation/remote-access/ssh/)
	1. **Create SSH key for SSH'ing**
		1. **[Come back to later](https://www.raspberrypi.org/documentation/configuration/security.md)**
	1. SSH Hardening (securing SSH)
		1. **come back to later**
	1. Install Neo4j
		1. [Neo4j Debian Packages](https://debian.neo4j.com/)
	1. Update `neo4j.conf`
		1. Navigate to folder: `cd /etc/neo4j/`
		1. Update file using `sudo nano neo4j.conf` to modify the following lines:
			- `dbms.default_listen_address=0.0.0.0`
			- `dbms.default_advertised_address=<pi ip address>`
			- > `# Bolt connector`
			  > `dbms.connector.bolt.enabled=true`
			  > `#dbms.connector.bolt.tls_level=OPTIONAL`
			  > `dbms.connector.bolt.listen_address=0.0.0.0:7687`
			
			- > `# HTTP Connector. There can be zero or one HTTP connectors.`
			  > `dbms.connector.http.enabled=true`
			  > `dbms.connector.http.listen_address=0.0.0.0:7474`
			
			- > `# HTTPS Connector. There can be zero or one HTTPS connectors.`
			  > `dbms.connector.https.enabled=false`
			  > `#dbms.connector.https.listen_address=0.0.0.0:7473`
			  > **later will want to address 0.0.0.0 for sensitive data**
			
			- uncomment out this line: `dbms.security.procedures.unrestricted=apoc.*`
			- update this line: `dbms.security.procedures.whitelist=apoc.coll.*,apoc.load.*,apoc.*`
	1. Install APOC
		1. [Identify APOC release (.jar file) to install](https://github.com/neo4j-contrib/neo4j-apoc-procedures/releases/)
		1. Download APOC .jar file: `sudo wget https://github.com/neo4j-contrib/neo4j-apoc-procedures/releases/download/4.0.0.15/apic-4.0.0.15-all.jar`
		1. Copy file to `plugins` folder: `cp apoc-4.0.0.15.-all.jar /var/lib/neo4j/plugins/`
		1. Navigate to `plugins` folder: `cd /var/lib/neo4j/plugins/`
		1. `sudo chown neo4j:neo4j *.jar`
		1. Restart Neo4j service if running: `systemctl restart neo4j`; Or start Neo4j service: `sudo neo4j start`
			- To stop neo4j: `sudo neo4j stop`
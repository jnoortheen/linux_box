LinuxBox Configuration
======================
A linux server configured to run mobile_catalog(https://github.com/jnoortheen/mobile_catalogue) flask application on Apache2 and PostgreSQL.

Installation
============
install all the required packages using
```````````````````````````````````````````````````````````````
sudo apt-get install apache2 libapache2-mod-wsgi postgresql git
```````````````````````````````````````````````````````````````

Configuration Changes
=====================
-	Update all packages
	``````````````````
	# apt-get update 
	# apt-get upgrade
	``````````````````

-	Added a user called grader with sudoer rights
	`````````````````````````
	# adduser grader
	# usermod -aG sudo grader
	`````````````````````````

-	Enabling key based authentication for the new user
	-	generate a new key by on the local machine
	`````````````
	$ ssh-keygen
	`````````````
	-	update the public key to the server machine's ~/.ssh/authorized_keys file. Hereafter use your private key file to login to the server.

-	Configure ssh to use port 2200 instead of default 22 by changing the Port in config file
	``````````````````````````
	# vim /etc/ssh/sshd_config
	# sudo service ssh restart
	``````````````````````````

-	Firewall configuration to allow only selected ports
	`````````````````````````````
	# ufw default deny incoming
	# ufw default allow outgoing
	# ufw allow 2200/tcp
	# ufw allow 80/tcp
	# ufw allow 123/tcp
	# ufw deny 22/tcp
	# ufw deny 22
	# ufw enable
	# ufw status
	````````````````````````````

-	Configure a postgresql user and create a database which will be used by the application to connect.
	``````````````````````````````````````````````````````````
	$ sudo -u postgres psql
	psql> create user catalog with password catalog;
	psql> create database catalog;
	psql> grant all privileges on database catalog to catalog;
	``````````````````````````````````````````````````````````

-	Clone the catalog application repo and installing required libraries
	``````````````````````````````````````````````````````````````
	# cd /var/www/html
	# git clone https://github.com/jnoortheen/mobile_catalogue.git
	# apt-get install python-pip
	# pip install -r requirements.txt
	# apt-get install python-psycopg2
	# chmod 700 .git
	```````````````````````````````````````````````````````````````

-	Update the flask app configuration to point out to the correct database path with correct authentication details and then finally create the required tables as mentioned in the project readme file.
	`````````````````````
	# vim app.py
	# FLASK_APP=app.py
	# flask initdb
	`````````````````````

-	Configure Apache server to run 'mobile_catalog', flask application by inserting "WSGIScriptAlias / /var/www/html/mobile_catalogue/mobile_catalogue/catalog.wsgi" line to the below file just before where </VirtualHost> appears.
	`````````````````````````````````````````````````
	# vim /etc/apache2/sites-enabled/000-default.conf
	# service apache2 restart
	`````````````````````````````````````````````````

-	disable remote login for root user by editing /etc/ssh/sshd_config and enable only key based authentication for other users.
	```````````````````````````````
	$ sudo vim /etc/ssh/sshd_config	
	$ sudo service ssh restart
	```````````````````````````````

Server Details
==============
*	server ip	- 	54.69.89.19
*	ssh port 	- 	2200
*	public URL	-	http://ec2-54-69-89-19.us-west-2.compute.amazonaws.com

Resources
=========
https://www.digitalocean.com/community/tutorials/how-to-secure-postgresql-on-an-ubuntu-vps
https://help.ubuntu.com/community/SSH/OpenSSH/InstallingConfiguringTesting

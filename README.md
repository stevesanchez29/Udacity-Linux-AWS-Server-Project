1 IP address and SSH port
ssh to 18.188.3.89 port 2200 

2 URL
http://ec2-18-188-3-89.us-east-2.compute.amazonaws.com/

3 Summary of software installed and configuration changes made

Run the following commands to update all packages and install finger package
	$ sudo apt-get update
	$ sudo apt-get upgrade
	$ sudo apt-get install finger

Make public key usable and secure. 
	$ chmod 600 ~/.ssh/YourAWSKey.pem

Creat user grader
	$ sudo adduser grader

Create a new file under the sudoers directory: 
	$ sudo nano /etc/sudoers.d/grader. 

Fill that file with grader ALL=(ALL:ALL) ALL, then save it (control X, then type yes, then hit the return key 

on your keyboard)

Open a new Terminal window (Command+N) and input 
	$ ssh-keygen -f ~/.ssh/udacity_key.rsa

Read and copy public key
	$ cat ~/.ssh/udacity_key.rsa.pub

Move to grader's folder
	$ cd /home/grader

Create a .ssh directory: 
	$ mkdir .ssh

Create a file to store the public key: 
	$ touch .ssh/authorized_keys

Edit the authorized_keys file 
	$ nano .ssh/authorized_keys

Change the permission: 
	$ sudo chmod 700 /home/grader/.ssh and 
	$ sudo chmod 644 /home/grader/.ssh/authorized_keys

Change the owner from root to grader:
	 $ sudo chown -R grader:grader /home/grader/.ssh

Restart the ssh service: 	
	$ sudo service ssh restart

Log into the server as grader: 
	$ ssh -i ~/.ssh/udacity_key.rsa grader@13.58.109.116

Enforce the key-based authentication: 
	$ sudo nano /etc/ssh/sshd_config. Find the PasswordAuthentication line and change text after 

to no. 

After this, restart ssh again: 
	$ sudo service ssh restart

Change the ssh port from 22 to 2200, as required by Udacity: 
	$ sudo nano /etc/ssh/ssdh_config Find the Port line and change 22 to 2200. 

Restart ssh: 
	$ sudo service ssh restart

Disconnect the server by $ ~. and then log back through port 2200: 
	$ ssh -i ~/.ssh/udacity_key.rsa -p 2200 grader@13.58.109.116

Disable ssh login for root user, as required by Udacity: 
	$ sudo nano /etc/ssh/sshd_config. Find the PermitRootLogin line and edit to no. 

Restart ssh 	
	$ sudo service ssh restart

Configure UFW to fulfill the requirement:

	$ sudo ufw allow 2200/tcp
	$ sudo ufw allow 80/tcp
	$ sudo ufw allow 123/udp
	$ sudo ufw enable

Install required packages
	$ sudo apt-get install apache2
	$ sudo apt-get install libapache2-mod-wsgi python-dev
	$ sudo apt-get install git

Enable mod_wsgi
	$ sudo a2enmod wsgi 

Start the web server
	$ sudo service apache2 restart. (You should input the public IP address and you should see a 

page)

Set up the folder structure
	$ cd /var/www
	$ sudo mkdir catalog
	$ sudo chown -R grader:grader catalog
	$ cd catalog

Clone the project from Github: 
	$ git clone [your link] catalog

Create a .wsgi file: 
	$sudo nano catalog.wsgi

Add the following:

	import sys
	import logging
	logging.basicConfig(stream=sys.stderr)
	sys.path.insert(0, "/var/www/catalog/")

	from catalog import app as application
	application.secret_key = 'supersecretkey'

Rename the application.py to __init__.py

Install and start the virtual machine

	$ sudo pip install virtualenv
	$ sudo virtualenv venv
	$ source venv/bin/activate
	$ sudo chmod -R 777 venv

Install the Flask and other packages
	
$ sudo apt-get install python-pip
$ sudo pip install Flask
$ sudo pip install httplib2 oauth2client sqlalchemy psycopg2 sqlaclemy_utils requests render_template, 

redirect, psslib [anything else you have built within this application. 

The following from your pg_config file should be installed

	apt-get -qqy update
	apt-get -qqy install postgresql python-psycopg2
	apt-get -qqy install python-sqlalchemy
	apt-get -qqy install python-pip
	pip install --upgrade pip
	pip install werkzeug==0.8.3
	pip install flask==0.9
	pip install Flask-Login==0.1.3
	pip install oauth2client
	pip install requests
	pip install httplib2

Use the nano __init__.py command to change the client_secrets.json line to 

/var/www/catalog/catalog/client_secrets.json

Change the host to your Amazon Lightsail public IP address and port to 80
	app.run(host='0.0.0.0', port=5000)

Configure and enable the virtual host
	$ sudo nano /etc/apache2/sites-available/catalog.conf

Paste the following code and save
	<VirtualHost *:80>
    ServerName [YOUR PUBLIC IP ADDRESS]
    ServerAlias [YOUR AMAZON LIGHTSAIL HOST NAME]
    ServerAdmin admin@35.167.27.204
    WSGIDaemonProcess catalog python-

path=/var/www/catalog:/var/www/catalog/venv/lib/python2.7/site-packages
    WSGIProcessGroup catalog
    WSGIScriptAlias / /var/www/catalog/catalog.wsgi
    <Directory /var/www/catalog/catalog/>
        Order allow,deny
        Allow from all
    </Directory>
    Alias /static /var/www/catalog/catalog/static
    <Directory /var/www/catalog/catalog/static/>
        Order allow,deny
        Allow from all
    </Directory>
    ErrorLog ${APACHE_LOG_DIR}/error.log
    LogLevel warn
    CustomLog ${APACHE_LOG_DIR}/access.log combined
</VirtualHost>

Set up the database
	$ sudo apt-get install libpq-dev python-dev
	$ sudo apt-get install postgresql postgresql-contrib
	$ sudo su - postgres -i

Type $ psql to get into postgres command line


Create a user to create and set up the database. I name my database catalog with user catalog

	$ CREATE USER catalog WITH PASSWORD [your password];
	$ ALTER USER catalog CREATEDB;
	$ CREATE DATABASE catalog WITH OWNER catalog;
	Connect to database $ \c catalog
	$ REVOKE ALL ON SCHEMA public FROM public;
	$ GRANT ALL ON SCHEMA public TO catalog;
	Quit the postgrel command line: $ \c and then $ exit

Use sudo nano command to change all engine to engine = create_engine('postgresql://catalog:[your 

password]@localhost/catalog 

Initiate database 
	$python database_setup_version2.py

Populate menus
	$python lostsofmenus.py

Start Flask web server
	$python project3.py

Restart Apache server 
	$ sudo service apache2 restart


4 Third party resources used to complete the project
Udacity's FSND Forum
https://www.digitalocean.com/community/tutorials/how-to-deploy-a-flask-application-on-an-ubuntu-vps
https://www.digitalocean.com/community/tutorials/how-to-secure-postgresql-on-an-ubuntu-vps
https://github.com/chuanqin3/udacity-linux-configuration
https://github.com/stueken/FSND-P5_Linux-Server-Configuration
https://github.com/SteveWooding/fullstack-nanodegree-linux-server-config/blob/master/README.md

5 Ssh key pasted in notes to reviewer field and sudo password

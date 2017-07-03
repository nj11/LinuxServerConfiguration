# Linux-Server-Configuration-UDACITY
This is the Linux Server Confighuration project for "Full Stack Web Developer Nanodegree" on Udacity. 

In this project, a Linux virtual machine needs to be configurated to launch the Item Catalog website.

The Item catalog website has been deployed at the URL : http://ec2-34-200-253-54.compute-1.amazonaws.com/

## Tasks
1. Launch Amazon AWS LightSail Instance.
2. SSH to the server using your ubuntu account.
3. Configure the Uncomplicated Firewall (UFW) .
4. Change the SSH port from 22 to 2200(non-default).
5. Create a new user named grader with sudo permission.
6. Set ssh login using keys for grader user.
7. Update all currently installed packages
8. Configure the local timezone to UTC
9. Disallow any password based authentication
10. Install and configure Apache to serve a Python mod_wsgi application
11. Install and configure PostgreSQL database for the Item catalog Application
12. Install dependencies for Web application.
13. Install git, clone and setup your Catalog App project correctly on server so that it renders on the browser and all applicaion    
    functionality works as expect
14. Configure and Enable a New Virtual Host
15.Create .wsgi file
16.Restart Apache.
17.Test the Catalog Application.
18.References.
    
## Launch Amazin AWS LightSail instance 
 1.Create a Amazon AWS webservices account and launch Amazon Lightsail instance.
 2.In the networking tab on amazon AWS console -- setup ports for this server - Add   
   Custom/TCP/80,Custom/TCP/2220/Custom/UDP/123.
 
## Instruction to SSH to the server using your ubuntu account.
1. Download Private Key for the server instance generated .
2. Move the ssh key to your home directory for your terminal window.In my case i used git bash shell. 
3. In your terminal, type in
	```ssh -i keyfilename.pem ubuntu@ec2-34-200-230-143.compute-1.amazonaws.com```
4. Development Environment Information

	Public IP Address

	34.200.253.54
  Servername:http://ec2-34-200-253-54.compute-1.amazonaws.com/
  
 ## Configure the Uncomplicated Firewall (UFW)

Configure the Uncomplicated Firewall (UFW) to only allow incoming connections for SSH (port 2200), HTTP (port 80), and NTP (port 123)

sudo ufw allow 2200/tcp
sudo ufw allow 80/tcp
sudo ufw allow 123/udp
sudo ufw default allow outgoing
sudo ufw enable
	
## Change the SSH port from 22 to 2200
1. Use `sudo nano /etc/ssh/sshd_config` and then change Port 22 to Port 2200 , save & quit.
2. In the networking tab for web services console - remove 22 port for SSH. 
3. Reload SSH using `sudo service ssh restart`
4. Test by logging in to ubuntu account with port 2200
   ```ssh -i keyfilename.pem ubuntu@ec2-34-200-230-143.compute-1.amazonaws.com -p 2200```
   
   
 ## Create a new user named grader with sudo permission.
1. ```sudo adduser grader```
2  ```sudo cp /etc/sudoers.d/90-cloud-init-users /etc/sudoers.d/grader```
3. ```sudo nano /etc/sudoers.d/grader```
4. Edit line inside the file to reflect newly added user : grader ALL=(ALL) NOPASSWD:ALL .Save and exit.
   You can now login via user grader and make sure sudo commands works perfectly.

 
 ## Set ssh login using keys for grader user
1. generate keys on local machine using`ssh-keygen` ; then save the private key in `~/.ssh` on local machine
2. deploy this public key on generated on  developement enviroment

	On you virtual machine:
	```
	$ su - grader
	$ mkdir .ssh
	$ touch .ssh/authorized_keys
	$ sudo nano .ssh/authorized_keys
	```
	Copy the public key generated on your local machine to this file and save
	```
	$ chmod 700 .ssh
	$ chmod 600 .ssh/authorized_keys
	```
	
3. reload SSH using `service ssh restart`
4. now you can use ssh to login with the new user you created

	`ssh grader@ec2-34-200-253-54.compute-1.amazonaws.com -p 2200 -i keyfilename`
  
  
## Update all currently installed packages

	sudo apt-get update
	sudo apt-get upgrade

## Configure the local timezone to UTC
1. Configure the time zone `sudo dpkg-reconfigure tzdata`
2. It is already set to UTC.

## Disallow any password based authentication
1. Configure the time zone `sudo dpkg-reconfigure tzdata`
2. It is already set to UTC.

## Install and configure Apache to serve a Python mod_wsgi application
1. Install Apache `sudo apt-get install apache2`
2. Install mod_wsgi `sudo apt-get install python-setuptools libapache2-mod-wsgi`
3. `sudo a2enmod wsgi` 
3. Restart Apache `sudo service apache2 restart`

## Install and configure PostgreSQL
1. Install PostgreSQL `sudo apt-get install postgresql`
2. Check if no remote connections are allowed `sudo  nano /etc/postgresql/9.3/main/pg_hba.conf`
3. Login as user "postgres" `sudo su - postgres`
4. Get into postgreSQL shell `psql`

5. Create a new database named catalog  and create a new user named catalog in postgreSQL shell
	
	```
	postgres=# CREATE DATABASE catalog;
	postgres=# CREATE USER catalog;
	```
5. Set a password for user catalog
	
	```
	postgres=# ALTER ROLE catalog WITH PASSWORD 'password';
	```
6. Give user "catalog" permission to "catalog" application database
	
	```
	postgres=# GRANT ALL PRIVILEGES ON DATABASE catalog TO catalog;
	```
7. Quit postgreSQL `postgres=# \q`
8. Exit from user "postgres" 
	
	```
	exit
	```
 
## Install dependencies for Catalog application.
1.sudo pip install --upgrade oauth2client
2.sudo pip install requests
3.sudo pip install sqlalchemy
4.sudo pip install psycopg2
5.sudo pip install flask
6.sudo pip install werkzeug==0.9.6
7.sudo pip install flask==0.9

## Install git, clone and setup your Catalog App project.
1. Install Git using `sudo apt-get install git`
2. Clone the Catalog App to the virtual machine `sudo git clone https://github.com/nj11/fullstack-nanodegree-vm.git`.Project resides  
  under vagrant/catalog directory after cloning.
3. Use `cd /var/www` to move to the /var/www directory 
4. Create the application directory `sudo mkdir catalog`
5. Move inside this directory using `cd catalog`Create another directory catalog.
6. Copy all project code from checked out vagrant/catalog to the newly created /var/www/catalog/catalog directory.
7. Rename `catalogapp.py` to `__init__.py` using `sudo mv /var/www/catalog/catalog/catalogapp.py /var/www/catalog/catalog/__init__.py`
8. Edit `database_setup.py`, `lotsofcatalogitems.py` and change `engine = create_engine('sqlite:///itemcatalog.db')` to `engine = create_engine('postgresql://catalog:password@localhost/itemcatalog')`
9. Run the database_setup.py using `sudo python database_setup.py`
10.Populate mock data using `sudo python database_setup.py` 
11.Check all necessary tables data has been created as below :
    sudo su - postgres
    psql -d itemcatalog
    \d ( to see all tables created)
    Run select  * from categories; ( to make sure categories table has some data )


## Configure and Enable a New Virtual Host
1. Create catalog.conf to edit: `sudo nano /etc/apache2/sites-available/catalog.conf`
2. Add the following lines of code to the file to configure the virtual host. 
	
	```
	<VirtualHost *:80>
                ServerName  ec2-34-200-253-54.compute-1.amazonaws.com
                ServerAdmin <adminemailaddress.com>
                WSGIScriptAlias / /var/www/catalog/catalogapp.wsgi
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
	```
3. Enable the virtual host with the following command: `sudo a2ensite catalog`

## Create the .wsgi File
1. Create the .wsgi File under /var/www/catalog: 
	
	```
	cd /var/www/catalog
	sudo nano catalogapp.wsgi 
	```
2. Add the following lines of code to the flaskapp.wsgi file:
	
	```
	#!/usr/bin/python
	import sys
	import logging
	logging.basicConfig(stream=sys.stderr)
	sys.path.insert(0,"/var/www/catalog/")

	from FlaskApp import app as application
	application.secret_key = 'Add your secret key'
	```

## Restart Apache
1. Restart Apache `sudo service apache2 restart `

## Test the installed Catalog application.
Navigate to this page in your browser to make sure application works expected.

http://ec2-34-200-253-54.compute-1.amazonaws.com/

Server error logs will be generated here :
`sudo cat/var.log/apache2/error.log`

In case you see runtime errors like below  :
  client_secrets.json not found then edit the __init__.py file to give the full path to the file.Workaround in progress to fix this    
  issue.
  In google plus sign in if you see a error like origin_mismatch then make sure your public servername that was used in the catalog.conf 
  file is added to allowed origins in google project via console.After adding authorized origin in google project redownload the 
  client_secrets.json file and save it in project directory.The client_secrets file should have the new server name.
  

## References:

https://www.digitalocean.com/community/tutorials/how-to-install-and-use-postgresql-on-ubuntu-16-04

https://www.digitalocean.com/community/tutorials/how-to-deploy-a-flask-application-on-an-ubuntu-vps

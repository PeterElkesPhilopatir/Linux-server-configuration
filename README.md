# Linux Server Configuration
Udacity Full Stack Web Developer Nanodegree Project 6

## About
Deploy [Item-Catalog project](https://github.com/PeterElkesPhilopatir/ItemCatalog).

## Server Information
IP address: 35.196.25.56
SSH Port: 2200  
Username: grader  
URL site: <http://35.196.25.56/>

# Server Configuration:
- setup your server (Google ComuteEngine)
- download [Putty](https://www.chiark.greenend.org.uk/~sgtatham/putty/latest.html)
- open putty gen to generate a private key then save it.
- load the private key file.
- add user called 'grader'
`$ adduser grader`  
- give sudo privileges to grader 
`$ sudo visusdo`
- add `grader ALL=(ALL:ALL) ALL` below `root ALL=(ALL:ALL) ALL`  
- switch to grader `su grader`
- in grader home dir create .ssh dir `sudo mkdir .ssh`
- `$ cd .ssh` and create authorized_keys file `$ sudo nano authorized_keys` 
- copy the private key that you have generated to the authorized_keys file (this file may contain more than one)
- change ssh port from 22 to 2200 using `sudo nano /etc/ssh/sshd_config`
- restart ssh server `sudo service ssh restart`
- open putty desktop application 
- change port to 2200 
- in host name field `grader@35.196.25.56`
- Yout are logged (Y) 


# Firewall Configuration:
  ```
  $ sudo ufw allow 2200/tcp
  $ sudo ufw allow www  
  $ sudo ufw allow ntp
  ```
  enable firewall
  `sudo ufw enable`

## Change timezone to UTC
- Configure the local timezone to UTC:  
  `$ sudo dpkg-reconfigure tzdata`
- Select UTC


## Install and configure Apache to serve a Python mod_wsgi application

1. Install Apache2 web server:  
  `$ sudo apt-get install apache2`
2. Install mod_wsgi and python-setuptools for serving Python apps from Apache:  
  `$ sudo apt-get install python-setuptools libapache2-mod-wsgi`
3. Restart Apache:  
  `$ sudo service apache2 restart`

## Install git
 -`$ sudo apt-get install git`
 - goto `cd /var/www/`
 - clone project repo `sudo git clone https://github.com/PeterElkesPhilopatir/ItemCatalog.git`
 
## Install following packages

 ```
  $ sudo apt-get install python-setuptools
  $ sudo apt-get install python-psycopg2
  $ sudo pip install Flask
  $ sudo pip install oauth2client
  $ sudo pip install requests
  $ sudo pip install httplib2
  $ sudo pip install sqlalchemy
  $ sudo pip install facebook-sdk
  ```
Restart apache:  
  `$ sudo service apache2 restart`
  
# Install  PostgreSQL and configure it

- Install PostgreSQL database:   
  `$ sudo apt-get install postgresql postgresql-contrib`
- Create linux user for psql 
  `$ sudo adduser catalog` 
- Switch to user 
  `$ sudo su - postgre`
- Connect to postgres
  `$ psql`
- Create user and password fot this app 
  `$ CREATE USER catalog WITH PASSWORD 'yourpassword';`
- Create a database 
  `$ CREATE DATABASE catalog;`
- navigate to project dir 
  `$ cd /var/www/FlaskApp/ItemCatalog/ItemCatalog/`
- change the following line in catalog.py
  `engine = create_engine('sqlite:///catalog.db')`
  to `engine = create_engine('postgresql://catalog:yourpassword@localhost/catalog')`

- Setup your database 
  `$ python database_setup.py`
enter your data
  `$ python database_entry.py`

# Configure and Enable Virtual Host
- create a file name flask.wsgi
  `$ sudo nano flask.wsgi`
add these lines to it 

`#!/usr/bin/python
import sys
import logging
logging.basicConfig(stream=sys.stderr)
sys.path.insert(0,"/var/www/FlaskApp/ItemCatalog/ItemCatalog/")
from catalog import app as application
application.secret_key = 'peter super key'`

-create conf file 
`$ sudo nano /etc/apache2/sites-available/FlaskApp.conf`
then add these lines
`<VirtualHost *:80>  
  ServerName 35.196.25.56 
  ServerAdmin grader@35.196.25.56      
  WSGIScriptAlias / /var/www/FlaskApp/ItemCatalog/ItemCatalog.wsgi
  <Directory /var/www/FlaskApp/ItemCatalog/ItemCatalog/>
    Require all granted
  </Directory>
  Alias /static /var/www/FlaskApp/ItemCatalog/ItemCatalog/static
  <Directory /var/www/FlaskApp/ItemCatalog/ItemCatalog/static/>
    Require all granted
  </Directory>
  ErrorLog ${APACHE_LOG_DIR}/error.log
  LogLevel warn
  CustomLog ${APACHE_LOG_DIR}/access.log combined
</VirtualHost>`

- Restart Server 
  `$ sudo service apache2 restart`
- GO TO 
  <http://35.196.25.56/>

## If erros occurred check logs file
-  `sudo cat /var/log/apache2/error.log`

# Third-party Resources
- [LinxServerConfiguration] https://github.com/eakmotion/LinuxServerConfiguration
- [fullstack-nanodegree-linux-server-config] https://github.com/SteveWooding/fullstack-nanodegree-linux-server-config
- [FireWallRule] https://cloud.google.com/compute/docs/vpc/firewalls

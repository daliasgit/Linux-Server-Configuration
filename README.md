# linux-server-configuration


This is the fifth project for "Full Stack Web Developer Nanodegree" on Udacity.

### Project Overview

The project requires students to create a Linux Server using Amazon Lightsail. It requires hosting one of the previous completed projects with an SSH key to access the projects.

You can visit http://ec2-18-188-48-42.us-east-2.compute.amazonaws.com for the website deployed.

### Server Details
* Public IP Address: 18.188.48.42
* Accessible SSH port: 2200
* Passphress: grader

# Server Setup

Steps for configuration of Amazon Lightsail server using Ubuntu.

### Connect To Server
* ssh grader@18.188.48.42 -p 2200 -i ~/.ssh/LinuxCourse
* ssh -i ~/.ssh/LightsailDefaultPrivateKey-us-east-2.pem -p 2200 ubuntu@18.188.48.42

### Update all currently installed packages

   1. `ubuntu@ip-172-26-2-0:~$ sudo apt-get update`
   2. `ubuntu@ip-172-26-2-0:~$ sudo apt-get upgrade`
   Package configuration-> keep the local version currently installed ->ok

### Change the SSH port from 22 to 2200
 
   1. `ubuntu@ip-172-26-2-0:~$ sudo nano /etc/ssh/sshd_config` 
      Modify Port 22 to Port 2200
      `PermitRootLogin prohibit-password` to `PermitRootLogin no`
      `PasswordAuthentication no`
      control X -> y -> Enter
   2. Restart the service `uubuntu@ip-172-26-2-0:~$ sudo service ssh restart`


### Configure the Uncomplicated Firewall (UFW)

   Configure the Uncomplicated Firewall (UFW) to only allow incoming connections for SSH (port 2200), HTTP (port 80), and NTP (port 123)
   
   1. Block all incoming connections: `sudo ufw default deny incoming`
   2. Allow outgoing connections: `sudo ufw default allow outgoing`
   3. Allow SSH on port 2200:`sudo ufw allow 2200/tcp`
   4. Allow HTTP connection on port 80:`sudo ufw allow 80/tcp`
   5. Allow NTP connection on port 123:`sudo ufw allow 123/udp`
   6. Enable the firewall and check status:`sudo ufw enable` 

   7. Check if firewall is enabled by `sudo ufw status`

``` 
ubuntu@ip-172-26-6-47:~$ sudo ufw status

Status: active

To                         Action      From
--                         ------      ----
2200/tcp                   ALLOW       Anywhere                  
80/tcp                     ALLOW       Anywhere                  
123/udp                    ALLOW       Anywhere                  
2200/tcp (v6)              ALLOW       Anywhere (v6)             
80/tcp (v6)                ALLOW       Anywhere (v6)             
123/udp (v6)               ALLOW       Anywhere (v6)   
```

### Create a new user named grader  
   
   1. `ubuntu@ip-172-26-2-0:~$ sudo adduser grader`
   2. `ubuntu@ip-172-26-2-0:~$ sudo nano /etc/sudoers.d/grader` Add-> grader ALL=(ALL:ALL) ALL ->control X -> Y -> Enter

### Generate SSH key pair for grader
   
   * `dalias-mbp:~ Dalia$ ssh-keygen`
   Enter file in which to save the key (/Users/Dalia/.ssh/id_rsa): /Users/Dalia/.ssh/LinuxCourse

   * `ubuntu@ip-172-26-2-0:~$ su - grader`
   * `grader@ip-172-26-2-0:~$ mkdir .ssh`
   * `grader@ip-172-26-2-0:~$ touch .ssh/authorized_keys`

   * `dalias-mbp:~ Dalia$ cat .ssh/LinuxCourse.pub`

   * `grader@ip-172-26-2-0:~$ nano .ssh/authorized_keys` -> Add content -> control X -> y -> Enter

### Set file permissions:

   * `grader@ip-172-26-2-0:~$ chmod 700 .ssh`
   * `grader@ip-172-26-2-0:~$ chmod  644 .ssh/authorized_keys`

   Reload SSH using `sudo service ssh restart`

### Log in to grader account by

   * `dalias-mbp:~ Dalia$ ssh grader@52.14.19.237 -p 2200 -i ~/.ssh/LinuxCourse`

### Configure the local timezone to UTC
   
   * `grader@ip-172-26-2-07:~$ sudo dpkg-reconfigure tzdata` -> None of these -> UTC

# Application Deployment

Application deployment using Apache 2, WSGI, Flask, and PostGresSQL.   

### Install apache2 and libapache2-mod-wsgi modules
    
   1. Install Apache: `grader@ip-172-26-2-0:~$ sudo apt-get install apache2`
   2. Install mod_wsgi: `grader@ip-172-26-2-0:~$ sudo apt-get install libapache2-mod-wsgi`
   3. Restart Apache: `grader@ip-172-26-2-0:~$ sudo service apache2 restart`
   
   * Now when you visit the IP address or URL you will see the default Apache start up page.

### Install PostgreSQL

   1. Install PostgreSQL:
    * `grader@ip-172-26-2-0:~$ sudo apt-get install postgresql postgresql-contrib`

   2. Login as superuser postgres

    * `grader@ip-172-26-2-0:~$ sudo su - postgres`
  
   3. Get into postgreSQL shell 
    * `psql`

   4. Create a new database named catalog and create a new user named catalog in postgreSQL shell
      
    * `postgres=# CREATE DATABASE catalog;`
    * `postgres=# CREATE USER catalog;`

   5. Set a password for user catalog
      
    * `postgres=# ALTER ROLE catalog WITH PASSWORD 'password';`

   6. Give user "catalog" permission to "catalog" application database

    * `postgres=# GRANT ALL PRIVILEGES ON DATABASE catalog TO catalog;`
   
   7. Quit postgreSQL 
    * `postgres=# \q`

   8. Exit from user "postgres"

    * `postgres@ip-172-26-2-0:~$ exit`

### 14.3 Obtain hostname and enable google, facebook credentials
    
We will need the hostname to ease authentication with google and facebook. The host name can be obtained from the IP address using      `nslookup`:

```
grader@ip-172-26-2-0:~$ nslookup 18.188.48.42
Server:		172.26.0.2
Address:	172.26.0.2#53

Non-authoritative answer:
42.48.188.18.in-addr.arpa	name = ec2-18-188-48-42.us-east-2.compute.amazonaws.com.

Authoritative answers can be found from:

```

# Add client_secrets.json and fb_client_secrets.json files

1. Authenticate login through Google:
   * Create a new project on the Google API Console
   * Create an OAuth Client ID (under the Credentials tab), and make sure to add http://XX.XX.XX.XX and http://ec2-XX-XX-XX-XX.compute-1.amazonaws.com as authorized JavaScript origins
   * Add http://ec2-XX-XX-XX-XX.compute-1.amazonaws.com/login, http://ec2-XX-XX-XX-XX.compute-1.amazonaws.com/gconnect, and http://ec2-XX-XX-XX-XX.compute-1.amazonaws.com/oauth2callback as authorized redirect URIs
   * Google will provide a client ID and client secret for the project; download the JSON file, and copy and paste the contents into the client_secrets.json file

2. Authenitcate login through Facebook:
   * Create a new app at Facebook for Developers
   * Make http://XX.XX.XX.XX/ the site URL
   * Add the 'Facebook Login' product, and put http://XX.XX.XX.XX/ and http://ec2-XX-XX-XX-XX.compute-1.amazonaws.com/ as the Valid OAuth redirect URIs
   * Paste the following into the fb_client_secrets.json file:
   `{ "web": { "app_id": "INSERT_APP_ID", "app_secret": "INSERT_APP_SECRET" } }`

### Switch the database in the application from SQLite to PostgreSQL

Since the app previously used sqlite, Change the engine inside the .py files(__init__.py, database_setup.py, lotsofmenus.py) from engine = create_engine('sqlite:///catalog.db') to engine = create_engine('postgresql://catalog:catalog@localhost/catalog')

### Add Application From Github:

   * `grader@ip-172-26-2-0:~$ sudo apt-get install git`
   * `grader@ip-172-26-2-0:~$ cd /var/www`
   * `grader@ip-172-26-2-0:/var/www$ sudo git clone https://github.com/daliasgit/FlaskApp.git FlaskApp`
   * `grader@ip-172-26-2-0:/var/www$ ls`

     ```FlaskApp  html```
     
   * `grader@ip-172-26-2-0:/var/www$ sudo chown -R www-data:www-data /var/www/FlaskApp`
   * `grader@ip-172-26-2-0:/var/www$ ls`
     
     ```FlaskApp  html```
    
   * `grader@ip-172-26-2-0:/var/www$ sudo chmod 755 -R /var/www/FlaskApp`
   * `grader@ip-172-26-2-0:/var/www$ ls -al`
    
```
total 16
drwxr-xr-x  4 root     root     4096 Sep  8 15:34 .
drwxr-xr-x 14 root     root     4096 Sep  8 15:18 ..
drwxr-xr-x  4 www-data www-data 4096 Sep  8 15:34 FlaskApp
drwxr-xr-x  2 root     root     4096 Sep  8 15:18 html

```
    
   * `grader@ip-172-26-2-0:/var/www$ cd FlaskApp`
   * `grader@ip-172-26-2-0:/var/www/FlaskApp$ ls`
      ```catalog  README.md```
   * `grader@ip-172-26-2-0:/var/www/FlaskApp$ cd catalog`
   * `grader@ip-172-26-2-0:/var/www/FlaskApp/catalog$ ls`

```
client_secrets.json     __init__.py                 static
database_setup.py       lotsofmenus.py              templates
database_setup.pyc      __pycache__
fb_client_secrets.json  restaurantmenuwithusers.db

```

   * `grader@ip-172-26-2-0:/var/www/FlaskApp/catalog$ sudo apt-get install python-pip`
   * `grader@ip-172-26-2-0:/var/www/FlaskApp/catalog$ sudo -H pip install virtualenv `
   * `grader@ip-172-26-2-0:/var/www/FlaskApp/catalog$ sudo virtualenv venv`
   * `grader@ip-172-26-2-0:/var/www/FlaskApp/catalog$ source venv/bin/activate`
   * `(venv) grader@ip-172-26-2-0:/var/www/FlaskApp/catalog$ sudo -H pip install Flask`
   * `(venv) grader@ip-172-26-2-0:/var/www/FlaskApp/catalog$ sudo -H pip install httplib2 oauth2client sqlalchemy psycopg2 sqlalchemy_utils requests`
   * `(venv) grader@ip-172-26-2-0:/var/www/FlaskApp/catalog$ sudo nano catalog.wsgi`

```
#!/usr/bin/python
import sys
import logging
logging.basicConfig(stream=sys.stderr)
sys.path.insert(0,"/var/www/FlaskApp")

from catalog import app as application
application.secret_key = 'super_secret_key'

```

   * `grader@ip-172-26-2-0:~$ cd /etc/apache2/sites-available/`
   * `grader@ip-172-26-2-0:/etc/apache2/sites-available$ ls`

```000-default.conf  default-ssl.conf```

```
    <VirtualHost *:80>
                ServerName 18.188.48.42
                ServerAdmin ubuntu@18.188.48.42
                WSGIScriptAlias / /var/www/FlaskApp/catalog/catalog.wsgi
                <Directory /var/www/FlaskApp/catalog>
                        Order allow,deny
                        Allow from all
                </Directory>
                Alias /static /var/www/FlaskApp/catalog/static
                <Directory /var/www/FlaskApp/catalog/static/>
                        Order allow,deny
                        Allow from all
                </Directory>
                ErrorLog ${APACHE_LOG_DIR}/error.log
                LogLevel warn
                CustomLog ${APACHE_LOG_DIR}/access.log combined
</VirtualHost>

```



   * `(venv) grader@ip-172-26-2-0:/var/www/FlaskApp/catalog$ sudo a2dissite 000-default.conf`
   * `(venv) grader@ip-172-26-2-0:/var/www/FlaskApp/catalog$ sudo a2ensite FlaskApp.conf`
   * `(venv) grader@ip-172-26-2-0:/var/www/FlaskApp/catalog$ sudo servie apache2 restart`

### Log error:
   
   * 'grader@ip-172-26-2-0:~$  sudo tail -100 /var/log/apache2/error.log'


### To delete a file:
   
   * `sudo rm -r <File name>` 


### Reference:
   1. [DigitalOcean](https://www.digitalocean.com/community/tutorials/how-to-set-up-ssh-keys-on-ubuntu-1604)
   2. [Apache](http://terokarvinen.com/2017/write-python-3-web-apps-with-apache2-mod_wsgi-install-ubuntu-16-04-xenial-every-tiny-part-tested-separately)
   3. [DigitalOcean](https://www.digitalocean.com/community/tutorials/how-to-deploy-a-flask-application-on-an-ubuntu-vps)
   4. [postgreSQL](https://www.digitalocean.com/community/tutorials/how-to-secure-postgresql-on-an-ubuntu-vps#installation)
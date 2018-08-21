# linux-server-configuration


This is the fifth project for "Full Stack Web Developer Nanodegree" on Udacity.

In this project, a Linux virtual machine needs to be configurated to support the Item Catalog website.

You can visit http:// for the website deployed.
* Public IP Address: 52.14.19.237
* Accessible SSH port: 2200
* Passphress: grader

* ssh grader@52.14.19.237 -p 2200 -i ~/.ssh/LinuxCourse
* ssh -i ~/.ssh/LightsailDefaultPrivateKey-us-east-2.pem -p 2200 ubuntu@52.14.19.237

### Update all currently installed packages

   1. `ubuntu@ip-172-26-6-47:~$ sudo apt-get update`
   2. `ubuntu@ip-172-26-6-47:~$ sudo apt-get upgrade`
   Package configuration-> keep the local version currently installed ->ok

### Change the SSH port from 22 to 2200
 
   1. `ubuntu@ip-172-26-6-47:~$ sudo nano /etc/ssh/sshd_config` 
      Modify Port 22 to Port 2200
      `PermitRootLogin prohibit-password` to `PermitRootLogin no`
      `PasswordAuthentication no`
      control X -> y -> Enter
   2. Restart the service `uubuntu@ip-172-26-6-47:~$ sudo service ssh restart`


### Configure the Uncomplicated Firewall (UFW)

   Configure the Uncomplicated Firewall (UFW) to only allow incoming connections for SSH (port 2200), HTTP (port 80), and NTP (port 123)
   
   1. Block all incoming connections: `sudo ufw default deny incoming`
   2. Allow outgoing connections: `sudo ufw default allow outgoing`
   3. Allow SSH on port 2200:`sudo ufw allow 2200/tcp`
   4. Allow HTTP connection on port 80:`sudo ufw allow 80/tcp`
   5. Allow NTP connection on port 123:`sudo ufw allow 123/udp`
   6. Enable the firewall and check status:`sudo ufw enable` 

   7. Check if firewall is enabled by `sudo ufw status`

  ``` ubuntu@ip-172-26-6-47:~$ sudo ufw status

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
   
   1. `ubuntu@ip-172-26-6-47:~$ sudo adduser grader`
   2. `ubuntu@ip-172-26-6-47:~$ sudo nano /etc/sudoers.d/grader` Add-> grader ALL=(ALL:ALL) ALL ->control X -> Y -> Enter

### Generate SSH key pair for grader
   
   * `dalias-mbp:~ Dalia$ ssh-keygen`
   Enter file in which to save the key (/Users/Dalia/.ssh/id_rsa): /Users/Dalia/.ssh/LinuxCourse

   * `ubuntu@ip-172-26-6-47:~$ su - grader`
   * `grader@ip-172-26-6-47:~$ mkdir .ssh`
   * `grader@ip-172-26-6-47:~$ touch .ssh/authorized_keys`

   * `dalias-mbp:~ Dalia$ cat .ssh/LinuxCourse.pub`

   * `grader@ip-172-26-6-47:~$ nano .ssh/authorized_keys` -> Add content -> control X -> y -> Enter

### Set file permissions:

   * `grader@ip-172-26-6-47:~$ chmod 700 .ssh`
   * `grader@ip-172-26-6-47:~$ chmod  644 .ssh/authorized_keys`

   Reload SSH using `sudo service ssh restart`

### Log in to grader account by

    `dalias-mbp:~ Dalia$ ssh grader@52.14.19.237 -p 2200 -i ~/.ssh/LinuxCourse`

### Configure the local timezone to UTC
   
   `grader@ip-172-26-6-47:~$ sudo dpkg-reconfigure tzdata` -> None of these -> UTC

### Install apache2 and libapache2-mod-wsgi modules
    
   1. Install Apache: `grader@ip-172-26-6-47:~$ sudo apt-get install apache2`
   2. Install mod_wsgi: `grader@ip-172-26-6-47:~$ sudo apt-get install libapache2-mod-wsgi`
   3. Restart Apache: `grader@ip-172-26-6-47:~$ sudo service apache2 restart`
   * Now when you visit the IP address or URL you will see the default Apache start up page.

### Install PostgreSQL

   1. Install PostgreSQL: `grader@ip-172-26-6-47:~$ sudo apt-get install postgresql postgresql-contrib`

   2. Login as superuser postgres

      `grader@ip-172-26-6-47:~$ sudo su - postgres`
  
   3. Get into postgreSQL shell `psql`

   4. Create a new database named catalog and create a new user named catalog in postgreSQL shell
      
      `postgres=# CREATE DATABASE catalog;`
      `postgres=# CREATE USER catalog;`

   5. Set a password for user catalog
      
      `postgres=# ALTER ROLE catalog WITH PASSWORD 'password';`

   6. Give user "catalog" permission to "catalog" application database

      `postgres=# GRANT ALL PRIVILEGES ON DATABASE catalog TO catalog;`
   
   7. Quit postgreSQL `postgres=# \q`

   8. Exit from user "postgres"

      `postgres@ip-172-26-6-47:~$ exit`
 
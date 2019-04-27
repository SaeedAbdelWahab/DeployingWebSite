# DeployingWebSite

## Step 1 After configuring the instance to ssh using the instructions given, Update all the packages using these 2 commands 

```ssh
$sudo apt-get update
$sudo apt-get upgrade
````

## Step 2 Change the default ssh port to 2200 instead of 22
That is done by three steps. First edit the sshd config file by running the command `$sudo nano /etc/ssh/sshd_config` and change the default port to 2200 instead of 22. The second step is to *change the firewall settings from the lightsail website by adding a custom port besides the two ports of SSH and HTTP to listen on port 2200 and be tcp*. And finally run the command `$sudo service sshd restart`.

## Step 3 Configure the firewall
Configure the Uncomplicated Firewall (UFW) to only allow incoming connections for SSH (port 2200), HTTP (port 80), and NTP (port 123). That is dont by running the following commands

```ssh
$sudo ufw default deny incoming  
$sudo ufw default allow outgoing
$sudo ufw allow 2200/tcp
$sudo ufw allow www
$sudo ufw allow 123/udp 
$sudo ufw enable
```

You can run the command `$sudo ufw status` to determine the state of the ufw before and after running these commands. **Note** that you have to configure your ssh from your local machine terminal since the terminal provided by the website will be blocked out since it is sshing on port 22

## Step 4 Add the grader user
You can add the grader user by running the command 

```ssh
$sudo adduser grader
```

## Step 5 Add sudo persmission to grader
that is dony by running the command 

```ssh
$sudo cp /etc/sudoers.d/90-cloud-init-users /etc/sudoers.d/grader
$sudo nano /etc/sudoers.d/grader
```
and replace the `ubuntu ALL=(ALL) NOPASSWD:ALL` with `grader ALL=(ALL) NOPASSWD:ALL`

## Step 6 Create an SSH key pair for grader 
On your local machine run the command 

```ssh
$ssh-keygen
```
And on the server, switch to grader user and then change directory to his home by running the comand `cd ~`.
After that run these two commands
```ssh
$mkdir .ssh
$touch .ssh/authorized_keys
```
Then copy the contents of the public key created on your local server from the ssh-keygen step `filename.pub` and paste these contents on the server in the `.ssh/authorized_keys` file.
After that change the permissions of the .ssh and the authorized_keys by running the commands
```ssh
$chmod 700 .ssh
$chmod 644 .ssh/authorized_keys
```
After this step you should be able to ssh your server using the command 
```ssh
$ssh grader@INSTANCE_IP -p 2200 -i ~/.ssh/FILENAME
```
with replacing instance ip by the server ip and the file name with the public key on your local machine.

## Step 7 Configure the time zone

Run the command 

```ssh
$sudo dpkg-reconfigure tzdata
```
And go with the menu to change to the wanted timezone (UTC chosen in this project)

## Step 8 Install and configure Apache to serve a Python mod_wsgi application

```ssh
$sudo apt-get install apache2 
$sudo apt-get install libapache2-mod-wsgi python-dev
$sudo a2enmod wsgi
$sudo service apache2 start
```

## Step 9 Install and configure PostgreSQL
```ssh
$sudo apt-get install postgresql
```
To make sure that remote connections are disabled check this file `/etc/postgresql/9.5/main/pg_hba.conf`

To configure the catalog user, create the DB and configure it run the following commands

```ssh
$sudo su - postgres
psql
CREATE USER catalog WITH PASSWORD 'password';
ALTER USER catalog CREATEDB;
CREATE DATABASE catalog WITH OWNER catalog;
\c catalog
REVOKE ALL ON SCHEMA public FROM public;
GRANT ALL ON SCHEMA public TO catalog;
\q
exit
```
## Step 10 Install needed libraries
Install needed libraries
```ssh
$sudo apt-get install python-flask
$sudo apt-get install python-sqlalchemy
$sudo apt-get install python-psycopg2
$sudo apt-get install python-httplib2 python-oauth2client
$sudo apt-get install python-requests
```
## Step 11 Install Git and clone the ItemCatalog repo

To install git 

```ssh
$sudo apt-get install git
```
To clone the repo 
```ssh
$cd /var/www
$mkdir ItemCatalog
$cd ItemCatalog
$git clone https://github.com/SaeedAbdelWahab/ItemCatalog.git
$cd ItemCatalog
$git checkout psqlImplementation
```
The last checkout switched to branch which uses psql database instead of sqlite

## Step 12 Configure the server to run the website

First edit the file `/etc/apache2/sites-available/ItemCatalog.conf`
by running the command 
```ssh
$sudo nano /etc/apache2/sites-available/ItemCatalog.conf
```
And then paste this in it
```ssh
<VirtualHost *:80>
    ServerName 99.79.75.159
    ServerAdmin admin@mywebsite.com
    WSGIScriptAlias / /var/www/ItemCatalog/ItemCatalog.wsgi
    <Directory /var/www/ItemCatalog/ItemCatalog>
        Order allow,deny
        Allow from all
    </Directory>
    <Directory /var/www/ItemCatalog/ItemCatalog/static/>
        Order allow,deny
        Allow from all
    </Directory>
    ErrorLog ${APACHE_LOG_DIR}/error.log
    LogLevel warn
    CustomLog ${APACHE_LOG_DIR}/access.log combined
</VirtualHost>
```
After that run this command 
```ssh
$sudo nano /var/www/ItemCatalog/ItemCatalog.wsgi  
```
And paste this in it 
```ssh
#! /usr/bin/python
import sys
import logging
logging.basicConfig(stream=sys.stderr)
sys.path.insert(0,"/var/www/ItemCatalog/ItemCatalog/")

# home points to the home.py file
from main import app as application
application.secret_key = "somesecretsessionkey"
```
Then run this commands to create coloumns in the DB and populate it
```ssh
$python database.py
$python populateDB.py
```
Finally edit the line 
```ssh
CLIENT_ID = json.loads(open('client_secrets.json', 'r').read())['web'
                                                                ]['client_id']

```
into 
```ssh
CLIENT_ID = json.loads(open('/var/www/ItemCatalog/ItemCatalog/client_secrets.json', 'r').read())['web'
                                                                ]['client_id']

```
After that run these commands 
```ssh
$sudo a2enmod wsgi
$sudo apachectl restart
$sudo a2ensite ItemCatalog
$sudo service apache2 reload
$sudo /etc/init.d/apache2 reload
```
And when Done visite the ip address of your program and hopefully the site is up !

You can Debug errors by running
```ssh
$sudo nano /var/log/apache2/error.log
```


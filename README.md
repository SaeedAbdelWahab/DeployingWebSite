# DeployingWebSite

## Step 1 after configuring the instance to ssh using the instructions given, update all the packages using these 2 commands 

```ssh
$sudo apt-get update
$sudo apt-get upgrade
````

## Step 2 change the default ssh port to 2200 instead of 22
That is done by three steps. First edit the sshd config file by running the command `$sudo nano /etc/ssh/sshd_config` and change the default port to 2200 instead of 22. The second step is to *change the firewall settings from the lightsail website by adding a custom port besides the two ports of SSH and HTTP to listen on port 2200 and be tcp*. And finally run the command `$sudo service sshd restart`.

1 - update all the software using "sudo apt-get update" then "sudo apt-get upgrade on server"

2 - Download the private key on the local machine and change it's permissions, then use it to log in 
  to the deployed server

3 - Deny all incoming data through ports and allow all outgoinh using the 'ufw'. Then allo the http, ssh and the tcp on 2200 in order to be able to ssh through this port

4 - Add new user grader

5 - Give the sudo permissions to the grader by copying the file "/etc/sudoers.d/90-cloud-init-users" and editing it

6 - use ssh-keygen on my laptop to generate the public and private keys, then copy the public and place it on the server. Also change the permission of the .ssh directory created on the server to 700 while the authorized_keys files in that directory to 644

7 - run "sudo dpkg-reconfigure tzdata", select none of the above then choose UTC time zone to change the time to UTC

8 - Install apache using the command "sudo apt-get install apache2"

9 - Install wsgi using the command "sudo apt-get install libapache2-mod-wsgi"

10 - Add the following line "WSGIScriptAlias / /var/www/html/myapp.wsgi" just before closing thee tag "<VirtualHost *:80>"
in the file "/etc/apache2/sites-enabled/000-default.conf" then restart the apache server "sudo apache2ctl restart"

11 - install postgresSql

11 - Install flask and SQLAlchemy

12 - 

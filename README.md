# DeployingWebSite

## Step 1 after configuring the instance to ssh using the instructions given, update all the packages using these 2 commands 

```ssh
$sudo apt-get update
$sudo apt-get upgrade
````

## Step 2 change the default ssh port to 2200 instead of 22
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

## Step 4 adding the grader user
You can add the grader user by running the command 

```ssh
$sudo adduser grader
```

## Step 5 add sudo persmission to grader
that is dony by running the command 

```ssh
$sudo cp /etc/sudoers.d/90-cloud-init-users /etc/sudoers.d/grader
$sudo nano /etc/sudoers.d/grader
```
and replace the `ubuntu ALL=(ALL) NOPASSWD:ALL` with `grader ALL=(ALL) NOPASSWD:ALL`

## Step 6 Create an SSH key pair for grader 
On your local machine run the command 

```ssh
$keygen-ssh
```





6 - use ssh-keygen on my laptop to generate the public and private keys, then copy the public and place it on the server. Also change the permission of the .ssh directory created on the server to 700 while the authorized_keys files in that directory to 644

7 - run "sudo dpkg-reconfigure tzdata", select none of the above then choose UTC time zone to change the time to UTC

8 - Install apache using the command "sudo apt-get install apache2"

9 - Install wsgi using the command "sudo apt-get install libapache2-mod-wsgi"

10 - Add the following line "WSGIScriptAlias / /var/www/html/myapp.wsgi" just before closing thee tag "<VirtualHost *:80>"
in the file "/etc/apache2/sites-enabled/000-default.conf" then restart the apache server "sudo apache2ctl restart"

11 - install postgresSql

11 - Install flask and SQLAlchemy

12 - 

# DeployingWebSite

The steps till now conducted

1 - update all the software using "sudo apt-get update" then "sudo apt-get upgrade on server"

2 - Download the private key on the local machine and change it's permissions, then use it to log in 
  to the deployed server

3 - Deny all incoming data through ports and allow all outgoinh using the 'ufw'. Then allo the http, ssh and the tcp on 2200 in order to be able to ssh through this port

4 - Add new user grader

5 - Give the sudo permissions to the grader by copying the file "/etc/sudoers.d/90-cloud-init-users" and editing it

# Linux-Server-Configuration


This documents explains how to deploy your Python Flask application

## About
- Deploying system requires Linux virtual machine. I used [AWS Lightsail](https://aws.amazon.com/lightsail/)., but you can also use Aws EC2. 
- public link is http://54.90.149.89/


## Skills used for this project
- AWS lightsail
- Python Flask and WSGI
- [Apache2](https://httpd.apache.org/)
- Ubuntu 18.04 
- creating database by Postgress SQL


# Server Configuration Instruction

## 1. Creating a AWS Lightsail instance
----
- Go to **AWSLightSail** and create an OS Only **Ubuntu 18.04** instance.
- Select the lowest priced plan option (for this project), identify your instance and create.
- Download SSH key in your local machine. Name of the file is *LightsailDefaultKey-us-east-1.pem* 
- use connect, then Linux Machine will start 
- When you enter the virtual machine terminal , write the following command to start to update and upgrade all missing packages and security   `Sudo apt-get update` and  `sudo apt-get upgrade`
- 
## 2. SSH Keys
----
- this allows you to connect your virtual machine by your local machine Terminal or FTTP program
- Go to your AWS account and select SSH keys
- Create .ssh folder in your local machine terminal by `mkdir .ssh`. if you cannot try `sudo mkdir .ssh `.
- Download the default file, it woul be name like this **LightsailDefaultKey-us-east-1.pem**
- Save the file in your .ssh folder.
- Restric file permission by running chmod 600 LightsailDefaultPrivateKey-*.pem
- Change the file name to aws.rsa.
- To connect run in your terminal `ssh -i ~/.ssh/lightsail_key.rsa ubuntu@34.201.105.57`

## 3. CHANGE THE SERVER PORT
----
- Run in your terminal `sudo nano /etc/ssh/sshd_config ` edit `etc/ssh/sshd_config ` file.
- Edit the file changing **22 to 2200** in the 5th line.
- Save by `ctrl+S` and exit from nano with `ctrl+X`
- Restart SSH with `sudo service ssh restart`

## 4. ADD Rules in AWS Lighthouse Page

- it is important to add new countom port on AWS manage page under the **NETWORKING** tab

| Custom | TCP | 2200 |

| Custom | TCP | 5000 |

| Custom | TCP | 8000 |

| Custom | udp | 123  |


## 5. Setup Firewall 
----
- `sudo ufw status` **The UFW should be inactive.**
- `sudo ufw default deny incoming` **Deny any incoming traffic.**
- `sudo ufw default deny outgoing` **Enable outgoing traffic.**
- `udo ufw allow 2200/tcp # Allow incoming` **tcp packets on port 2200.**
- `sudo ufw allow 80/tcp`# **Allow HTTP traffic in.**
- `sudo ufw allow 123/udp` # **Allow incoming udp packets on port 123.**
- `sudo ufw deny 22` # Deny tcp and udp packets**
- `sudo ufw enable` **The output should be like this Command may disrupt existing ssh connections. Proceed with operation (y|n)? y Firewall is active and enabled on system startup**
- `sudo ufw status Status` . **check the port**
- Your output should like this
```
To                         Action      From
--                         ------      ----
Apache                     ALLOW       Anywhere                  
22/tcp                     ALLOW       Anywhere                  
2200/tcp                   ALLOW       Anywhere                  
123/udp                    ALLOW       Anywhere                  
80/tcp                     ALLOW       Anywhere                  
22                         DENY        Anywhere                  
2200                       ALLOW       Anywhere                  
Apache (v6)                ALLOW       Anywhere (v6)             
22/tcp (v6)                ALLOW       Anywhere (v6)             
2200/tcp (v6)              ALLOW       Anywhere (v6)             
123/udp (v6)               ALLOW       Anywhere (v6)             
80/tcp (v6)                ALLOW       Anywhere (v6)             
22 (v6)                    DENY        Anywhere (v6)             
2200 (v6)                  ALLOW       Anywhere (v6)             

```
## 6. Create Grader User
----
- Install finger to manage user by `sudo apt-get install finger`
- to create user `sudo adduser grader`
- Set password and details for user.
- check grader by `finger grader`
- Set the password and fill out info.
- Grant sudo access `sudo visudo`
- Add the following line after root `ALL=(ALL:ALL) ALL:`
- `grader ALL=(ALL:ALL) ALL`
- Save and close the file.
- check the sudo permissioon by `sudo -l`
- 
```
Matching Defaults entries for grader on ip-172-2X-14-XX:
    env_reset, mail_badpass,
    secure_path=/usr/local/sbin\:/usr/local/bin\:/usr/sbin\:/usr/bin\:/sbin\:/bin\:/snap/bin

User grader may run the following commands on ip-172-2X-14-XX:
    (ALL : ALL) ALL
    (ALL : ALL) ALL
```
## 7. CREATE SSH KEYPAIR FOR GRADER USING THE SSH-KEYGEN TOOL
----

### On .shh in your local terminal machine:
- open your terminal on your local machine
- Create keys `ssh-keygen`
- Choose a location for the tow files `~/.ssh/authorized_keys`
- Open the file and copy the content `sudo nano .ssh/authorized_keys`
- In the **grader** account, create the following directories:
- `sudo mkdir .ssh`
-` cd .ssh`
- `sudo nano authorized_keys`
- **Paste the public key into authorized_keys file. Save and close.**
- **Grant permissions**
```
sudo chmod 700 .ssh
sudo chmod 644 .ssh/authorized_keys
```
Close terminal sesssion and check grader account to test connectty
- `ssh -i ~/.ssh/grader grader@34.201.105.57 -p 2200`

## 8. Install Apache2
----
- `Install Apache2 & WSGI package`
- `sudo apt-get install apache2`
- `sudo apt-get install libapache2-mod-wsgi`
- Enable mod_wsgi: `sudo a2enmod wsgi`
- `sudo service apache2 start`
- Verify apache2 Ubuntu Default Page.

## 9. Install PostgreSQL Database and create tables 
----
- Install software `sudo apt-get install postgresql`
- Setting up ubuntu role:
- Switch to root user:
- `sudo -u postgres psql`
- alter user postgres with password 'password';
- Create role
- `create role catalog superuser;`
-  create database catalog
-  Create role catalog;
- Allow catalog role to create a db:
- `alter role catalog CREATEDB;`
- Create catalog database
- `create database catalog`
- Exit psql:
- `\q`
- change engine of database both application python file and creating database file in ypur python script **`engine = create_engine('postgresql://catalog:password@localhost/catalog')`**

## 10. Clone Project
----
- Install GIT `sudo apt-get install git`
- Create directory to save project cd /var/www `sudo mkdir webApp`
- create one more directory in cd /var/www/webApp `sudo mkdir webApp`
 totally you should create 2 directory under the www folder
`sudo chown -R grader:grader webApp`
- cd webApp/webApp
- git clone https://github.com/user/project.git
- rename the **app.py** file by   `sudo mv app.py    __init__py`

## 11. Install Missing Libraries
----
- Install the follwoing dependencies:
- `sudo -H apt-get install python-pip`
- `sudo -H pip install flask`
- `sudo -H pip install flask sqlalchemy`
- `sudo -H pip install --upgrade google-api-python-client oauth2client`
- `sudo -H pip install requests`
- `sudo -H pip install psycopg2-binary.`

## 12. Customise the Apache
----
- Configure Apache to handle requests using the **WSGI module.** there is already default set file in `/etc/apache2/sites-enabled/000-default.conf.` but we need to create new one
- Lets create a new file with:
`sudo nano /etc/apache2/sites-enabled/webApp.conf`

The /etc/apache2/sites-enabled/webApp.conf should now look like this:
```
<VirtualHost *:80>
                ServerName 54.90.129.96
                ServerAdmin harun.yenial@gmail.com
                WSGIScriptAlias / /var/www/webApp/webapp.wsgi
                <Directory /var/www/webApp/webApp/>
                        Order allow,deny
                        Allow from all
                </Directory>
                Alias /static /var/www/webApp/webApp/static
                <Directory /var/www/webApp/webApp/static/>
                        Order allow,deny
                        Allow from all
                </Directory>
                ErrorLog ${APACHE_LOG_DIR}/error.log
                LogLevel warn
                CustomLog ${APACHE_LOG_DIR}/access.log combined
</VirtualHost>
```
- Enable the virtual host with the following command:
- Reload Apache with
- `sudo service apache2 reload`. 
- Enable the virtual Host `sudo a2ensite webApp`

## 13. Writing WSGI Script
----
- Create and config the .wsgi file `sudo nano /var/www/webApp/webapp.wsgi`
- Add the following content

```
import sys
import logging
logging.basicConfig(stream=sys.stderr)
sys.path.insert(0,"/var/www/webApp/")
sys.path.insert(0,"/var/www/webApp/webApp")
from webApp import app as application
application.secret_key = 'secretkey'
```

- run your script to check any bugs before serving. `python webapp.wsgi`
- if there is a missing package use `sudo -H pip XXXX`
- `sudo service apache2 restart`

## Troubleshooting
----
- if you have port 500 error please run `sudo tail /var/log/apache2/error.log` and check the errors
- dont forget the change JSON file location in your script
- Google Authorization need valid domain name, **xip.io** extension of domain name is not accepted after 2020
- You can access the logs of the application on the apache logs, they are located, by default, on `/var/log/apache2/access.log` or `/var/log/apache2/error.log` for errors.You'll need sudo access to read the files.

## Resorces
- [How to Deploy a Flask App to a Linux Server  ](https://www.youtube.com/watch?v=YFBRVJPhDGY) 
- [How To Deploy a Flask Application on an Ubuntu VPS](https://www.digitalocean.com/community/tutorials/how-to-deploy-a-flask-application-on-an-ubuntu-vps )  
- [20 Basic Ubuntu Commands And Terminal Shortcuts Every Beginner Must Know ](https://techlog360.com/basic-ubuntu-commands-terminal-shortcuts-linux-beginner/) 
- https://aws.amazon.com/premiumsupport/knowledge-center/ec2-linux-fix-permission-denied-errors/
- https://aws.amazon.com/premiumsupport/knowledge-center/ec2-server-refused-our-key/
- [error handling](https://github.com/jungleBadger/-nanodegree-linux-server-troubleshoot)
- [public and private key handling](https://www.digitalocean.com/community/questions/ubuntu-16-04-creating-new-user-and-adding-ssh-keys) 
- [Linux cheat sheet](https://cheatography.com/davechild/cheat-sheets/linux-command-line/)
- [Go to the Support Web Site](https://support.west-wind.com)



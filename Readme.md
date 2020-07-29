# Linux-Server-Configuration


This documents explains how to deploy your Python Flask application

## About
- Deploying system requires Linux virtual machine. I used [AWS Lightsail](https://aws.amazon.com/lightsail/).
- public link is http://18.212.232.50.xip.io/
- ip 18.212.232.50


## Skills used for this project
- AWS lightsail
- Python Flask and WSGI
- [Apache2](https://httpd.apache.org/)
- Ubuntu 18.04 
- creating database by Psotgress SQL


# Server Configuration Instruction
-------------
## 1. Creating a AWS Lightsail instance
----
- Go to **AWSLightSail** and create an OS Only **Ubuntu 18.04** instance.
- Select the lowest priced plan option (for this project), identify your instance and create.
- Download SSH key in your local machine. Name of the file is *LightsailDefaultKey-us-east-1.pem* 
- use connect, then Linux Machine will start 
- When you enter the virtual machine terminal , write the following command to start to update and upgrade all missing packages and security   `Sudo apt-get update` and  `sudo apt-get upgrade`
- 
## 2. SSH Keys
- this allows you to connect your virtual machine by your local machine Terminal or FTTP program
- Go to your AWS account and select SSH keys
- Create .ssh folder in your local machine terminal by `mkdir .ssh`. if you cannot try `sudo mkdir .ssh `.
- Download the default file, it woul be name like this **LightsailDefaultKey-us-east-1.pem**
- Save the file in your .ssh folder.
- Restric file permission by running chmod 600 LightsailDefaultPrivateKey-*.pem
- Change the file name to aws.rsa.
- To connect run in your terminal `ssh -i ~/.ssh/lightsail_key.rsa ubuntu@18.212.232.50`

## 3. CHANGE THE SERVER PORT
- Run in your terminal `sudo nano /etc/ssh/sshd_config ` edit `etc/ssh/sshd_config ` file.
- Edit the file changing **22 to 2200** in the 5th line.
- Save by `ctrl+S` and exit from nano with `ctrl+X`
- Restart SSH with `sudo service ssh restart`

## 4. ADD Rules in AWS Lighthouse PAge
- it is important to add new countom port on AWS manage page under the **NETWORKING** tab
- | Custom | TCP | 2200 |
| Custom | TCP | 5000 |
| Custom | TCP | 8000 |
| Custom | udp | 123  |

## 5. Setup Firewall 

- `sudo ufw status` **The UFW should be inactive.**
- `sudo ufw default deny incoming` **Deny any incoming traffic.**
- `sudo ufw default deny outgoing` **Enable outgoing traffic.**
- `udo ufw allow 2200/tcp # Allow incoming` **tcp packets on port 2200.**
- `sudo ufw allow 80/tcp`# **Allow HTTP traffic in.**
- `sudo ufw allow 123/udp` # **Allow incoming udp packets on port 123.**
- `sudo ufw deny 22` # Deny tcp and udp packets**
- `sudo ufw enable` **The output should be like this Command may disrupt existing ssh connections. Proceed with operation (y|n)? y Firewall is active and enabled on system startup**
- `sudo ufw status Status` . **check the port**


## 6.  
Some basic Git commands are:
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

 


## Troubleshooting





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

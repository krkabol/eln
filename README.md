# ELNs testing
Electronic Lab Notebooks for testing purposes. Definitely not suitable for production, but provides isolated sandbox for individual research groups. The only goal is to deploy them quickly. Any feedback welcomed.

General points:
* based on Docker, for simple CPU/RAM usage use ```docker stats```
* http/self-signed certificates are a necessary limitation
* SMTP (emails) and scheduled task/message queue (cron, Celery Beat etc.) ignored
* if you run it on your own laptop, then the model call is https://localhost:8001; else if you run it on VM with IP address 192.168.xx.yy, then https://192.168.xx.yy:8001...
* Stack contains passwords in plain text etc. - please think first, then act

Commands:
* run them inside the specific ELN folder
* start: ```docker compose up -d --build```
* stop:  ```docker compose down```
* stop and delete data (of the specific ELN): ```docker compose down -v```. 

## eLabFTW
Instance exposed on port 8001. 

### startup
```shell
cd eLabFTW
docker-compose up -d # and wait(!)
```
The first initialization takes really long time, even more than 10(15) minutes. When you get bored, check from time to time ```docker logs elabftw```. Message 
> s6-rc: fatal: timed out 
> 
> s6-sudoc: fatal: unable to get exit status from server: Operation timed out"

is OK, the initialization is running and you have to wait. I first restarted the containers in this state - such an action led to a running instance, but with incomplete data in db tables - especially the "Default team" item missing in *teams* table, which makes the whole app impossible to use. 

Therefore - wait and periodically check logs ```docker logs elabftw``` until you reach this message:
> ✓ Installation successful! You can now start using your eLabFTW instance.
> 
> → Register your Sysadmin account here: https://localhost:8001/register.php

The first registered user becomes the superadmin. All other info in docs https://doc.elabftw.net/generalities.html. But in my tests, the link is unavailable.  I expect it has to deal with Nginx,  certificates and startup logic of the official container and have no exact explanation. Fortunately, the solution is simple - just restart containers ```docker compose down && docker compose up -d```.

## Kadi4Mat
Instance exposed on port 8002. 

Settings prepared for location https://localhost:8002 ("run and use on my laptop"). Not tested yet, but probably needs accommodate for specific IP the users will access to ("run on remote server"). If it's proven, edit listed locations before you start or after cleanup (```docker compose down -v```):
* Kadi4Mat/Dockerfile:12
* Kadi4Mat/kadi.conf:2,4,8
* Kadi4Mat/kadi.py:8

### startup
```shell
cd Kadi4Mat
docker-compose up -d --build
```
Should you need an admin role for a user, please execute ```docker exec -it kadi4mat bash```, you will step into the running container. Follow with ```kadi users sysadmin 1```, where "1" represents the ID of the user (incremental integer starting from 1, or you can check in GUI in Profile - *Account type: Local · Persistent ID: **XXX***). To exit container, execute ```exit``` - management of users is now available in the GUI using this user.

## RSpace
Based on https://github.com/rspace-os/rspace-docker
```shell
cd RSpace
docker-compose up -d --build
```
First startup takes again approx 10min. Login with the username *sysadmin1* and the password *sysWisc23!*

Instance exposed on port 8003, according the hostname see above and pay attention to:
* RSpace/deployment.properties:14

## Chemotion
Instance exposed on port 8004. Based on https://www.chemotion.net/docs/eln/install_configure/manual_install#only-when-installing-or-upgrading-to-version-141

Size of images is huge, download during the first run takes some minutes, startup is also very slow - sum approx 15 minutes for me.

login as admin (ADM:PleaseChangeYourPassword) or Sign up as a new user

### startup
I experienced a problem during the first start, but simple rerun fixed it:

```shell
cd Chemotion
docker compose up  # after several minutes eln reached "unhealthy" status and failed
docker compose down # stop everything but keep data already initialized
docker compose up -d # provide a second chance to initialize --> succeeded :-)
```

[//]: # (obligatory branding for EOSC.CZ)
<hr style="margin-top: 100px; margin-bottom: 20px">

<p style="text-align: left"> <img src="https://webcentrum.muni.cz/media/3831863/seda_eosc.png" alt="EOSC CZ Logo" height="90"> </p>
This project output was developed with financial contributions from the EOSC CZ initiative throught the project National Repository Platform for Research Data (CZ.02.01.01/00/23_014/0008787) funded by Programme Johannes Amos Comenius (P JAC) of the Ministry of Education, Youth and Sports of the Czech Republic (MEYS).

<p style="text-align: left"> <img src="https://webcentrum.muni.cz/media/3832168/seda_eu-msmt_eng.png" alt="EU and MŠMT Logos" height="90"> </p>

<h1 align="center"> Open Nebula </h1>
<h2 align="center"> Steps to Run </h2>

## Install apt Dependencies
```bash
sudo apt update
```
```bash
sudo apt install -y bash-completion bison debhelper default-jdk flex javahelper libmysql++-dev libsqlite3-dev libssl-dev \
libsystemd-dev libws-commons-util-java libxml2-dev libxslt1-dev libcurl4-openssl-dev libcurl4 libvncserver-dev \
postgresql-server-dev-all python3-setuptools libzmq3-dev python2 build-essential libcairo2-dev libjpeg-turbo8-dev \
libxmlrpc-core-c3-dev npm ronn ruby ruby-dev scons libxmlrpc-c++8-dev libtelnet-dev libwebsockets-dev libpulse-dev libvorbis-dev \
libwebp-dev libssl-dev libpango1.0-dev libswscale-dev libavcodec-dev libavutil-dev libavformat-dev libpng-dev libtool-bin \
libossp-uuid-dev libvncserver-dev freerdp2-dev libssh2-1-dev libaugeas-dev
```
---
## Clone Open Nebula one GitHub Repository
```bash
git clone https://github.com/OpenNebula/one.git
cd one
```
---
## Ruby gem Dependencies Installation
```bash
sudo gem install xmlrpc polyglot treetop parse-cron ffi-rzmq sinatra rqrcode rotp ipaddress ox highline rbvmomi git augeas sqlite3
```
#### Additional step for AMD64
```bash
sudo gem install curb zendesk_api
```
---
## Python version alternative update
```bash
sudo update-alternatives --install /usr/bin/python python /usr/bin/python2 1
```
---
## Node dependencies Installation
```bash
sudo npm i -g opennebula one bower grunt grunt-cli
```
--- 
## Compilation Command
```bash
# Main compilation command to compile opennebula
scons -j2 mysql=yes syslog=yes systemd=yes rubygems=yes sunstone=yes 
```
#### Optional Compilation Commands
```bash
# Compilation command to generate fireedge minified files
scons -j2 fireedge=yes

# Compilation command to install docker driver for open nebula
scons -j2 docker_machine=yes
```
---
## Build and Install Open Nebula
```bash
# Command to build opennebula CLI and their documentation
cd ~/one/share/man
sudo ./build.sh

# Command to Install opennebula
cd ~/one
sudo ./install.sh
```
---
## Link main.js for Sunstone
```bash
cd /usr/lib/one/sunstone/public
sudo ln -s ~/one/src/sunstone/public/main-dist.js main.js
```
---
## Link dist folder of Fireeedge
```bash
cd /usr/lib/one/fireedge/
sudo ln -s ~/one/src/fireedge/dist
```
---
## Create one_auth File
```bash
cd /var/lib/one/
ls -a
# If .one directory does not exist
sudo mkdir -p .one
cd .one
sudo vim one_auth
# In one_auth file store credentials in the form of username:password
```
---
## Set Environment Variables
```bash
export ONE_AUTH="/var/lib/one/.one/one_auth"
export ONE_XMLRPC=http://"Insert IP where frontend will run":2633
```
---
## Check Lockfile
```bash
ls /var/lock/
# if one directory does not exist
cd /var/lock/
sudo mkdir -p one
```
---
## Make the Following Changes in configuration Files

#### Opennebula deamon configuration file
```bash
cd /etc/one/
sudo vim oned.conf
uncomment the line "#DATASTORE_LOCATION  = /var/lib/one/datastores" by removing "#" in front of the line
```
#### Sunstone server configuration file
```bash
cd /etc/one/
sudo vim sunstone-server.conf
For host instead of 0.0.0.0 use "Insert IP where frontend will run"
```
---
## Start opennebula service
```bash
sudo one start
# If above command results in error due to ONE_AUTH not being set use the following command
sudo ONE_AUTH="/var/lib/one/.one/one_auth" one start
```
---
## Start Sunstone frontend GUI
```bash
sudo sunstone-server start
# If above command results in error due to ONE_AUTH not being set use the following command
sudo ONE_AUTH="/var/lib/one/.one/one_auth" sunstone-server start
```
---
## Open Sunstone frontend GUI
```
open IP "Insert IP where frontend will run":9869 in browser
```
---
## Setup Guacd
```bash
wget https://downloads.apache.org/guacamole/1.4.0/source/guacamole-server-1.4.0.tar.gz
tar -xvf guacamole-server-1.4.0.tar.gz
cd guacamole-server-1.4.0
./configure --with-init-dir=/etc/init.d
sudo make -i
sudo make -i install
sudo ldconfig
# check npm audit and install npm packages if required
```
---
## Start Fireedge frontend GUI
```bash
sudo fireedge-server start
```

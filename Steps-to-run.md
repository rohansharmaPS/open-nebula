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
libxmlrpc-core-c3-dev npm ronn ruby scons libxmlrpc-c++8-dev libtelnet-dev libwebsockets-dev libpulse-dev libvorbis-dev \
libwebp-dev libssl-dev libpango1.0-dev libswscale-dev libavcodec-dev libavutil-dev libavformat-dev libpng-dev libtool-bin \
libossp-uuid-dev libvncserver-dev freerdp2-dev libssh2-1-dev
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
cd ~/one/share/install_gems/
sudo ./install_gems
```
```bash
sudo gem install xmlrpc polyglot treetop parse-cron ffi-rzmq nokogiri treetop ronn
```
---
## Python version alternative update
```bash
sudo update-alternatives --install /usr/bin/python python /usr/bin/python2 1
```
---
## Node dependencies Installation
```bash
sudo npm i -g opennebula one bower grunt-cli
```
--- 
## Compilation Command
```bash
scons -j2 mysql=yes syslog=yes systemd=yes rubygems=yes sunstone=yes fireedge=yes docker_machine=yes
```
---
## Build and Install Open Nebula
```bash
cd ~/one/share/man
sudo ./build.sh
cd ~/one
sudo ./install.sh
```
---
## Create one_auth File
```bash
cd /var/lib/one/
ls
# if .one directory does not exist
sudo mkdir -p .one
cd .one
sudo vim one_auth
username:password
```
---
## Set Environment Variables
```bash
export ONE_AUTH="/var/lib/one/.one/one_auth"
export ONE_XMLRPC=http://0.0.0.0:2633
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
open IP 0.0.0.0:9869 in browser
```
---
## Setup Guacd
```
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

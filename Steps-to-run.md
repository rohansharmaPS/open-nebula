<h1 align="center"> Open Nebula </h1>

## Requirements
- 2 AWS EC2 Instances with Ubuntu 20.04 - one for Frontend other for Host

&nbsp;&nbsp;&nbsp;

---
<h1 align="center"> Steps to Run: Frontend Setup </h1>

&nbsp;&nbsp;&nbsp;
### Install apt Dependencies
```bash
sudo apt update
```
```bash
sudo apt install -y bash-completion bison debhelper default-jdk flex javahelper libmysql++-dev libsqlite3-dev libssl-dev \
libsystemd-dev libws-commons-util-java libxml2-dev libxslt1-dev libcurl4-openssl-dev libcurl4 libvncserver-dev \
postgresql-server-dev-all python3-setuptools libzmq3-dev python2 build-essential libcairo2-dev libjpeg-turbo8-dev \
libxmlrpc-core-c3-dev npm ronn ruby ruby-dev scons libxmlrpc-c++8-dev libtelnet-dev libwebsockets-dev libpulse-dev libvorbis-dev \
libwebp-dev libssl-dev libpango1.0-dev libswscale-dev libavcodec-dev libavutil-dev libavformat-dev libpng-dev libtool-bin \
libossp-uuid-dev libvncserver-dev freerdp2-dev libssh2-1-dev libaugeas-dev qemu qemu-utils
```
---
### Create oneadmin user and give it sudo permissions
```bash
sudo adduser oneadmin
#set any password
sudo usermod -aG sudo oneadmin
```
---
### Login to oneadmin user
```bash
su - oneadmin
#enter the password set above
```
---
### Clone Open Nebula one GitHub Repository
```bash
git clone https://github.com/OpenNebula/one.git
cd one
```
---
### Ruby gem Dependencies Installation
```bash
sudo gem install xmlrpc polyglot treetop parse-cron ffi-rzmq sinatra rqrcode rotp ipaddress ox highline rbvmomi git augeas sqlite3 curb zendesk_api
```
---
### Python version alternative update
```bash
sudo update-alternatives --install /usr/bin/python python /usr/bin/python2 1
```
---
### Node dependencies Installation
```bash
sudo npm i -g opennebula one bower grunt grunt-cli
```
--- 
### Compilation Command
#### Main compilation command to compile opennebula
```bash
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
### Build and Install Open Nebula
```bash
# Command to build opennebula CLI and their documentation
cd ~/one/share/man
sudo ./build.sh

# Command to Install opennebula
cd ~/one
sudo ./install.sh
```
---
### Link main.js for Sunstone if not present in /usr/lib/one/sunstone/public/dist/
```bash
ls /usr/lib/one/sunstone/public/dist/
#if main.js is not present
cd /usr/lib/one/sunstone/public/dist/
sudo ln -s ~/one/src/sunstone/public/dist/main.js main.js
```
---
### Link dist folder of Fireeedge
```bash
cd /usr/lib/one/fireedge/
sudo ln -s ~/one/src/fireedge/dist
```
---
### Claim the ownership of folders required for running opennebula
```bash
sudo chown -R oneadmin:oneadmin /usr/lib/one
sudo chown -R oneadmin:oneadmin /etc/one
sudo chown -R oneadmin:oneadmin /var/lib/one
sudo chown -R oneadmin:oneadmin /var/lock/one
sudo chown -R oneadmin:oneadmin /var/log/one
sudo chown -R oneadmin:oneadmin /var/run/one
```
---
### Create one_auth File
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
### Set Environment Variables
```bash
export CUR_IP=$(curl -s http://169.254.169.254/latest/meta-data/public-hostname)
export ONE_AUTH="/var/lib/one/.one/one_auth"
export ONE_XMLRPC=http://$CUR_IP:2633
```
---
### Check Lockfile
```bash
ls /var/lock/
# if one directory does not exist
cd /var/lock/
sudo mkdir -p one
```
---
### Make the Following Changes in configuration Files

#### Opennebula deamon configuration file
```bash
cd /etc/one/
echo $CUR_IP #Copy the output of this command
sudo vim oned.conf
change the line host: "0.0.0.0" to host: "output of echo command here"
uncomment the line "#DATASTORE_LOCATION  = /var/lib/one/datastores" by removing "#" in front of the line
```
#### Sunstone server configuration file
```bash
cd /etc/one/
echo $CUR_IP #Copy the output of this command
sudo vim sunstone-server.conf
#Replace localhost and 0.0.0.0 with output of echo command in the following lines
one_xmlrpc: http://localhost:2633/RPC2
host: "0.0.0.0"
oneflow_server: http://localhost:2474/
private_fireedge_endpoint: http://localhost:2616
public_fireedge_endpoint: http://localhost:2616
```
#### Monitor deamon configuration file
#### Scheduler deamon configuration file
#### Fireedge server configuration file
---
### Download and install firecracker driver
#### ARM64
```bash
wget https://github.com/firecracker-microvm/firecracker/releases/download/v1.1.1/firecracker-v1.1.1-aarch64.tgz
tar -xvf firecracker-v1.1.1-aarch64.tgz
cd release-v1.1.1-aarch64
sudo cp firecracker-v1.1.1-aarch64 /usr/bin/firecracker
```
#### AMD64
```bash
wget https://github.com/firecracker-microvm/firecracker/releases/download/v1.1.1/firecracker-v1.1.1-x86_64.tgz
tar -xvf firecracker-v1.1.1-x86_64.tgz
cd release-v1.1.1-x86_64
sudo cp firecracker-v1.1.1-x86_64 /usr/bin/firecracker
```
---

<h1 align="center"> Steps to Run: Host Setup </h1>

### Install apt Dependencies
```bash
sudo apt update
```
```bash
sudo apt install -y ruby ruby-dev
```
---
### Create oneadmin user and give it sudo permissions
```bash
sudo adduser oneadmin
#set any password
sudo usermod -aG sudo oneadmin
```
---
### Login to oneadmin user
```bash
su - oneadmin
#enter the password set above
```
---
### Ruby gem Dependencies Installation
```bash
sudo gem install sqlite3
```
---
### Download and install firecracker driver
#### ARM64
```bash
wget https://github.com/firecracker-microvm/firecracker/releases/download/v1.1.1/firecracker-v1.1.1-aarch64.tgz
tar -xvf firecracker-v1.1.1-aarch64.tgz
cd release-v1.1.1-aarch64
sudo cp firecracker-v1.1.1-aarch64 /usr/bin/firecracker
```
#### AMD64
```bash
wget https://github.com/firecracker-microvm/firecracker/releases/download/v1.1.1/firecracker-v1.1.1-x86_64.tgz
tar -xvf firecracker-v1.1.1-x86_64.tgz
cd release-v1.1.1-x86_64
sudo cp firecracker-v1.1.1-x86_64 /usr/bin/firecracker
```
---

## Note- Configure host first

<h1 align="center">Configure Passwordless ssh from Frontend to Host: Host Configuration</h1>

## Note- Run the following command as oneadmin

### Create Authorized keys file with public key
```bash
cd ~/.ssh
vi authorized_keys
#Copy and paste contents of /home/ubuntu/.ssh/authorized_keys and save (:wq)
```
---

<h1 align="center">Configure Passwordless ssh from Frontend to Host: Frontend Configuration</h1>

## Note- Run the following command as oneadmin

### Generate ssh public key
```bash
ssh-keygen -t rsa
#keep pressing enter for every feild
```
#### Create .ssh directory
```bash
cd ~
mkdir .ssh
```
---
### Create Authorized keys file with public key
```bash
cd ~/.ssh
vi authorized_keys
#Copy and paste contents of /home/ubuntu/.ssh/authorized_keys and save (:wq)
cat id_rsa.pub >> authorized_keys
```
---
### Create Known hosts file with keys of Frontend and host
```bash
cd ~/.ssh
ssh-keyscan "Frontend IP here" > known_hosts
ssh-keyscan "Host IP here" >> known_hosts
```
---
## Note- Before running below step be sure to scp the identity file for host to frontend

### SCP the contents inside .ssh folder of Frontend to host
```bash
scp -i "identity file here" ~/.ssh/* oneadmin@host-ip:/home/oneadmin
```
---
### Final step
```bash
ssh -i "identity file here" oneadmin@host-ip
move and replace the authorized_keys, id_rsa, id_rsa.pub and known_hosts file to ~/.ssh folder
exit
ssh oneadmin@host-ip #this command should not ask for password or result in public key denied
exit
```
---

<h1 align="center">Start Opennebula on Frontend</h1>

### Start opennebula service
```bash
one start
```
---
### Start Sunstone frontend GUI
```bash
sunstone-server start
```
---
### Open Sunstone frontend GUI
```bash
echo $CUR_IP #copy the output
In browser open IP: http://"output of echo command here":9869
```

<p align="center">
    <img src="https://github.com/rohansharmaPS/open-nebula/blob/main/images/sunstone-gui.png?raw=true" title="Sunstone GUI">
</p>

---
### Create Host

<p align="center">
    <img src="https://github.com/rohansharmaPS/open-nebula/blob/main/images/host-creation.png?raw=true" title="Host Creation">
</p>

<p align="center">
    <img src="https://github.com/rohansharmaPS/open-nebula/blob/main/images/list-of-hosts.png?raw=true" title="List of Hosts">
</p>

---
### Export Ubuntu 20.04 Image and VM template from Opennebula Marketplace
```bash
onemarketapp list
# Note the ID of Ubuntu 20.04 Usually it is 46 if different then replace 46 with that id number
onemarketapp export 46 "Ubuntu 20.04" -d 1
```

<p align="center">
    <img src="https://github.com/rohansharmaPS/open-nebula/blob/main/images/list-of-vm-templates.png?raw=true" title="List of VM Templates">
</p>

<p align="center">
    <img src="https://github.com/rohansharmaPS/open-nebula/blob/main/images/update-vm-template.png?raw=true" title="List of Hosts">
</p>

<p align="center">
    <img src="https://github.com/rohansharmaPS/open-nebula/blob/main/images/ubuntu-vm-template-detailed-info1.png?raw=true" title="Detailed VM Template">
</p>

<p align="center">
    <img src="https://github.com/rohansharmaPS/open-nebula/blob/main/images/ubuntu-vm-template-detailed-info2.png?raw=true" title="Detailed VM Template">
</p>

<p align="center">
    <img src="https://github.com/rohansharmaPS/open-nebula/blob/main/images/ubuntu-vm-template-detailed-info3.png?raw=true" title="Detailed VM Template">
</p>

<p align="center">
    <img src="https://github.com/rohansharmaPS/open-nebula/blob/main/images/opennebula-dashboard1.png?raw=true" title="Dashboard">
</p>

<p align="center">
    <img src="https://github.com/rohansharmaPS/open-nebula/blob/main/images/opennebula-dashboard2.png?raw=true" title="Dashboard">
</p>

<p align="center">
    <img src="https://github.com/rohansharmaPS/open-nebula/blob/main/images/opennebula-dashboard3.png?raw=true" title="Dashboard">
</p>

<h2 align="center">Optional Steps</h2>

### Setup Guacd
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
### Start Fireedge frontend GUI
```bash
sudo fireedge-server start
```

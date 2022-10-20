<h1 align="center"> Opennebula </h1>

## Requirements
- AWS EC2 Instance (a1.medium) for Frontend
- AWS Baremetal EC2 Instance (a1.metal) for Host

&nbsp;&nbsp;&nbsp;

---
<h1 align="center"> Steps to Run: Frontend Setup </h1>

&nbsp;&nbsp;&nbsp;
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
libossp-uuid-dev libvncserver-dev freerdp2-dev libssh2-1-dev libaugeas-dev net-tools qemu-utils genisoimage
```
---
### Clone Opennebula one GitHub Repository
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
### Compilation Commands
#### Main compilation command to compile opennebula
```bash
scons -j2 mysql=yes syslog=yes systemd=yes rubygems=yes sunstone=yes 
```

#### Fireedge Compilation Command
```bash
scons -j2 fireedge=yes
```
---
### Build and Install Opennebula
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
### Link dist folder of Fireeedge if not present in /usr/lib/one/fireedge/
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
mkdir -p .one
cd .one
vi one_auth
# In one_auth file store credentials in the form of username:password
```
---
### Set Environment Variables
```bash
#The following can be added to .bashrc to set the following Environment Variables permanently
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
vi oned.conf
Uncomment the line "#DATASTORE_LOCATION  = /var/lib/one/datastores" by removing "#" in front of the line
Comment the line "VM_RESTRICTED_ATTR = "EMULATOR" " by adding "#" in front of the line
#Replace localhost, 127.0.0.1 and 0.0.0.0 with output of echo command in the following lines
host: "0.0.0.0"
HM_MAD = [
    EXECUTABLE = "one_hm",
    ARGUMENTS = "-p 2101 -l 2102 -b 127.0.0.1"]
```
#### Sunstone server configuration file
```bash
cd /etc/one/
echo $CUR_IP #Copy the output of this command
vi sunstone-server.conf
#Replace localhost and 0.0.0.0 with output of echo command in the following lines
one_xmlrpc: http://localhost:2633/RPC2
host: "0.0.0.0"
oneflow_server: http://localhost:2474/
private_fireedge_endpoint: http://localhost:2616
public_fireedge_endpoint: http://localhost:2616
```
#### Monitor deamon configuration file
```bash
cd /etc/one/
echo $CUR_IP #Copy the output of this command
vi monitord.conf
#Replace 0.0.0.0 and auto with output of echo command in the following lines
NETWORK = [
    ADDRESS         = "0.0.0.0",
    MONITOR_ADDRESS = "auto",
    PORT    = 4124,
    THREADS = 8,
    PUBKEY  = "",
    PRIKEY  = ""
]
```
#### Scheduler deamon configuration file
```bash
cd /etc/one/
echo $CUR_IP #Copy the output of this command
vi sched.conf
#Replace localhost with output of echo command in the following lines
ONE_XMLRPC = "http://localhost:2633/RPC2"
```
#### Fireedge server configuration file
```bash
cd /etc/one/
echo $CUR_IP #Copy the output of this command
vi fireedge-server.conf
#Replace localhost and 0.0.0.0 with output of echo command in the following lines
host: '0.0.0.0'
one_xmlrpc: 'http://localhost:2633/RPC2'
```
#### KVM driver configuration file
```bash
cd /etc/one/vmm_exec
vi vmm_exec_kvm.conf
Uncomment the line "#EMULATOR = /usr/libexec/qemu-kvm" and change it to "EMULATOR = /usr/bin/kvm"
Change ARCH to aarch64 in the following line
OS = [
    ARCH = "x86_64"
]
```
---


<h1 align="center"> Steps to Run: Host Setup </h1>


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
### Install apt Dependencies
```bash
sudo apt update
```
```bash
sudo apt install -y ruby ruby-dev qemu-kvm libvirt-clients libvirt-daemon libvirt-daemon-system bridge-utils virtinst net-tools
sudo apt install cpu-checker -y > /dev/null 2>&1
```
---
### Ruby gem Dependencies Installation
```bash
sudo gem install sqlite3
```
---
### Check and Enable KVM on Host
#### Check KVM
```bash
sudo kvm-ok
```
#### Enable KVM
```bash
sudo systemctl start libvirtd
sudo systemctl enable libvirtd
sudo chown -R oneadmin:oneadmin /var/run/libvirt
virsh -c qemu:///system list
```
---
### Tranfer ownership of /var/lib to oneadmin instead of root for opennebula to be able to store data
```bash
sudo chown oneadmin:oneadmin /var/lib
```
---

&nbsp;&nbsp;&nbsp;

> **Note**
> Configure host first

&nbsp;&nbsp;&nbsp;

---

<h1 align="center">Configure Passwordless ssh from Frontend to Host: Host Configuration</h1>


&nbsp;&nbsp;&nbsp;

> **Note**
> Run the following commands as oneadmin

### Create Authorized keys file with public key
```bash
cd ~/.ssh
vi authorized_keys
#Copy and paste contents of /home/ubuntu/.ssh/authorized_keys and save (:wq)
```
---

<h1 align="center">Configure Passwordless ssh from Frontend to Host: Frontend Configuration</h1>


&nbsp;&nbsp;&nbsp;

> **Note**
> Run the following commands as oneadmin

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
### Start Fireedge
```bash
sudo fireedge-server start
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

- Enter the credentials that were stored in /var/lib/one/.one/one_auth in form username:password
- Once the credentials are verified you will see dashboard as shown below

<p align="center">
    <img src="https://github.com/rohansharmaPS/open-nebula/blob/main/images/opennebula-dashboard1.png?raw=true" title="Dashboard1">
</p>

---
### Create Host

- After Logging in to the GUI from Dashboard access Host tab
- Create Host with IP of Host as shown below

<p align="center">
    <img src="https://github.com/rohansharmaPS/open-nebula/blob/main/images/host-creation.png?raw=true" title="Host Creation">
</p>

- Once host is created it should be in ON state, if it is in Error state check logs in /var/log/one directory to resolve error

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

- Once the Ubuntu Image and template is exported access the Dashboard, you should see Ubuntu Image uploaded as shown below

<p align="center">
    <img src="https://github.com/rohansharmaPS/open-nebula/blob/main/images/opennebula-dashboard2.png?raw=true" title="Dashboard2">
</p>

- Access the Templates tab, go to VMs tab, you should be able to see the Ubuntu VM template

<p align="center">
    <img src="https://github.com/rohansharmaPS/open-nebula/blob/main/images/list-of-vm-templates.png?raw=true" title="List of VM Templates">
</p>

---
### Create Virtual Network
> On host run the following commands to get information of Linux bridge created by kvm 
```bash 
ifconfig virbr0 # Take note of IP and netmask 
ip route | grep virbr0 # This command will print the gateway being used 
grep "nameserver" /etc/resolv.conf # This command will print the DNS IP 
``` 
> - With the Above Information create Virtual Network by going into Virtual network tab on dashboard 
> - Click on Advanced and Paste the following template and make changes if required: 

``` 
NAME = "private" 
BRIDGE = "virbr0" 
BRIDGE_TYPE = "linux" 
DNS = "127.0.0.53" 
GATEWAY = "192.168.122.0" 
METHOD = "static" 
NETWORK_ADDRESS = "192.168.122.1" 
NETWORK_MASK = "255.255.255.0" 
OUTER_VLAN_ID = "" 
PHYDEV = "" 
SECURITY_GROUPS = "0" 
VLAN_ID = "" 
VN_MAD = "bridge" 
AR=[TYPE="IP4", IP="192.168.122.100", SIZE="100"] 
AR=[TYPE="IP4", IP="192.168.122.220", SIZE="10"] 
``` 
---
### Update the Ubuntu VM template as following: 
``` 
CONTEXT = [ 
  NETWORK = "YES", 
  SSH_PUBLIC_KEY = "$USER[SSH_PUBLIC_KEY]" ] 
CPU = "1" 
DISK = [ 
  IMAGE_ID = "6" ] 
EMULATOR = "/usr/bin/kvm" 
GRAPHICS = [ 
  LISTEN = "0.0.0.0", 
  TYPE = "vnc" ] 
LOGO = "images/logos/ubuntu.png" 
LXD_SECURITY_PRIVILEGED = "true" 
MEMORY = "768" 
OS = [ 
  ARCH = "aarch64" ] 
``` 
---

### Create a new Datastore in storage tab 

> **Note**
> The size of ubuntu image is 2.2Gb, so recommended size is atleast 3 Gb as shown below
> Set the Datastore Type as system and Storage backend as Filesystem - ssh mode as shown below

<p align="center">
    <img src="https://github.com/rohansharmaPS/open-nebula/blob/main/images/datastore-creation.png?raw=true" title="Create Datastore">
</p>

---
### Create new VM from VM tab 
- Select the ubuntu template 
- Specify it to run on the host we have created
- Specify it to use the network we created
- Specify it to use the datastore we created. 

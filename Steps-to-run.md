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
libxmlrpc-core-c3-dev npm ronn ruby-dev scons libxmlrpc-c++8-dev libtelnet-dev libwebsockets-dev libpulse-dev libvorbis-dev \
libwebp-dev libssl-dev libpango1.0-dev libswscale-dev libavcodec-dev libavutil-dev libavformat-dev libpng-dev libtool-bin \
libossp-uuid-dev libvncserver-dev freerdp2-dev libssh2-1-dev libaugeas-dev net-tools genisoimage augeas-lenses comerr-dev \
curl dmeventd fonts-lato git git-man ibverbs-providers ieee-data iputils-arping javascript-common jq krb5-multidev \
libaugeas0 libblas3 libboost-iostreams1.71.0 libc-ares2 libc-dev-bin libc6-dev libcrypt-dev libcurl4 libdevmapper-event1.02.1 \
liberror-perl libfreerdp-client2-2 libfreerdp2-2 libgfortran5 libgssrpc4 libibverbs1 libiscsi7 libjq1 libjs-jquery \
libkadm5clnt-mit11 libkadm5srv-mit11 libkdb5-9 libkrb5-dev liblapack3 liblvm2cmd2.03 libnode64 libnorm-dev libnorm1 \
libonig5 libossp-uuid16 libpgm-5.2-0 libpgm-dev libpq5 librados2 librbd1 librdmacm1 libreadline5 libruby2.7 libsodium-dev \
libsqlite3-0 libssh2-1 libvncclient1 libwinpr2-2 libzmq3-dev libzmq5 linux-libc-dev lvm2 manpages-dev nodejs nodejs-doc \
python3-netaddr python3-numpy qemu-block-extra qemu-utils rake ruby ruby-minitest ruby-net-telnet ruby-power-assert \
ruby-test-unit ruby-xmlrpc ruby2.7 rubygems-integration sharutils sqlite3 mysql-server openvswitch-switch
```
---
### Clone Opennebula one GitHub Repository
```bash
git clone https://github.com/OpenNebula/one.git
```
---
### Python version alternative update
```bash
sudo update-alternatives --install /usr/bin/python python /usr/bin/python2 1
```
---
### Node dependencies Installation
```bash
sudo npm i -g bower grunt grunt-cli
```
---
### Setup Guacd
```bash
wget https://downloads.apache.org/guacamole/1.4.0/source/guacamole-server-1.4.0.tar.gz
tar -xzf guacamole-server-1.4.0.tar.gz
cd guacamole-server-1.4.0
./configure --with-init-dir=/etc/init.d
sudo make
sudo make install
sudo ldconfig
# check npm audit and install npm packages if required
```
--- 
### Change directory
```bash
cd one
```
### Compilation Command
```bash
scons -j2 mysql=yes syslog=yes systemd=yes rubygems=yes sunstone=yes fireedge=yes
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
### Ruby gem Dependencies Installation
```bash
sudo /usr/share/one/install_gems
```

> **Note**
> Uninstall nokogiri version other than 1.10
> Use command : gem list | grep nokogiri to check if any other version is installed
> Use command gem uninstall nokogiri -v "version number" to uninstall other version

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
ls /usr/lib/one/fireedge/
#if dist folder not present
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
sudo chown -R oneadmin:oneadmin /var/lock
sudo chown -R oneadmin:oneadmin /var/log/one
sudo chown -R oneadmin:oneadmin /var/run/one
```
---
### Create one_auth File
```bash
cd ~
mkdir -p .one
cd .one
vi one_auth
# In one_auth file store credentials in the form of username:password
```
---
### Enable passwordless sudo
```bash
sudo visudo
#add the following line to end of file
oneadmin ALL=(ALL) NOPASSWD: ALL
```
---
### Add the following to end of ~/.bashrc file
```bash
export ONE_AUTH="/var/lib/one/.one/one_auth"
export CUR_IP=$(curl -s http://169.254.169.254/latest/meta-data/public-hostname)
export ONE_XMLRPC=http://$CUR_IP:2633
if [[ ! -e "/var/lock/one" ]]; then
    cd "/var/lock"
    mkdir one
fi
if [[ ! -e "/var/run/one" ]]; then
    cd "/var/run"
    sudo mkdir one
    sudo chown oneadmin:oneadmin one
fi
cd
```
---
### Make the Following Changes in configuration Files

#### Opennebula deamon configuration file
```bash
cd /etc/one/
echo $CUR_IP #Copy the output of this command
vi oned.conf
Uncomment the line "#DATASTORE_LOCATION  = /var/lib/one/datastores" by removing "#" in front of the line
Uncomment the line "#ONEGATE_ENDPOINT = http://frontend:5030" by removing "#" in front of the line and replace frontend with 0.0.0.0
```
#### KVM driver configuration file for ARM64
```bash
cd /etc/one/vmm_exec
vi vmm_exec_kvm.conf
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
sudo apt install -y bash-completion bison debhelper default-jdk flex javahelper libmysql++-dev libsqlite3-dev \
libxmlrpc-core-c3-dev libsystemd-dev libws-commons-util-java libxml2-dev libxslt1-dev libcurl4-openssl-dev libcurl4 \
libvncserver-dev python3-setuptools libzmq3-dev npm libssl-dev ruby ruby-dev qemu-kvm libvirt-clients libvirt-daemon \
libvirt-daemon-system bridge-utils virtinst net-tools cpu-checker build-essential openvswitch-switch
```
---
### Ruby gem Dependencies Installation
```bash
sudo gem install xmlrpc sqlite3
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
### Tranfer ownership of /var/lib and /var/tmp to oneadmin instead of root for opennebula to be able to store data
```bash
sudo chown oneadmin:oneadmin /var/lib
sudo chown oneadmin:oneadmin /var/tmp
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
### Start Fireedge
```bash
fireedge-server start
```
---
### Start Sunstone frontend GUI
```bash
sunstone-server start
```
---
### Start Onegate
```bash
onegate-server start
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
ifconfig ovsbr0 # Take note of IP and netmask 
ip route | grep ovsbr0 # This command will print the gateway being used 
grep "nameserver" /etc/resolv.conf # This command will print the DNS IP 
``` 
> - With the Above Information create Virtual Network by going into Virtual network tab on dashboard 
> - Click on Advanced and Paste the following template and make changes if required: 

``` 
NAME = "private" 
BRIDGE = "ovsbr0" 
BRIDGE_TYPE = "linux" 
DNS = "127.0.0.53" 
GATEWAY = "172.30.0.0" 
METHOD = "static" 
NETWORK_ADDRESS = "172.30.0.1" 
NETWORK_MASK = "255.255.255.0" 
OUTER_VLAN_ID = "" 
PHYDEV = "" 
SECURITY_GROUPS = "0" 
VLAN_ID = "" 
VN_MAD = "ovswitch" 
AR=[TYPE="IP4", IP="192.168.122.100", SIZE="100"] 
AR=[TYPE="IP4", IP="192.168.122.220", SIZE="10"] 
``` 
---
### Update the Ubuntu VM template as following for ARM64: 
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
x
<p align="center">
    <img src="https://github.com/rohansharmaPS/open-nebula/blob/main/images/datastore-creation.png?raw=true" title="Create Datastore">
</p>

---
### Create new VM from VM tab 
- Select the ubuntu template 
- Specify it to run on the host we have created
- Specify it to use the network we created
- Specify it to use the datastore we created. 

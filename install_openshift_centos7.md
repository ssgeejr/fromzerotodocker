#Installing OpenShift Origin on CentOS-7

.................................................
[[ THESE NOTES COME AFTER INSTALLING & CONFIGURING DOCKER
  -> and should be moved to the bottom of the page ]]
yum update -y
yum install -y centos-release-openshift-origin
yum install -y wget git net-tools bind-utils iptables-services bridge-utils ntp bash-completion origin-clients 

https://github.com/openshift/openshift-ansible
//--
Ansible >= 2.6.5, Ansible 2.7 is not yet supported and known to fail

Jinja >= 2.7 
pip install -U jinja

pyOpenSSL
python-lxml
yum install -y pyOpenSSL python-cryptography python-lxml

Metrics:
	httpd-tools
yum install -y httpd-tools

--//


config registries & enable selinux:
/etc/docker/daemon.json
{
  "selinux-enabled": true,
  "insecure-registries" : ["172.30.0.0/16", "skupoc.dev.sprint.com:5000"]
} 

# "insecure-registries" : ["172.30.0.0/16", "skupoc.dev.sprint.com:5000"]

```
oc cluster up --public-hostname=192.168.56.15 --http-proxy=http://proxy.ip:port --https-proxy=https://proxy.ip:port --no-proxy=[192.168.56.0/24,172.0.0. 0/8,192.168.56.15,192.168.56.15,localhost]
```

systemctl daemon-reload
systemctl restart docker


#test your configuration
```
docker network inspect -f "{{range .IPAM.Config }}{{ .Subnet }}{{end}}" bridge
```
# You should see: 172.17.0.0/16


If you are going to use a firewall:
```
systemctl enable firewalld
systemctl start firewalld
systemctl status firewalld
```

Create a new firewalld zone for the subnet and grant it access to the API and DNS ports:
```
firewall-cmd --permanent --new-zone dockerc
firewall-cmd --permanent --zone dockerc --add-source 172.17.0.0/16
firewall-cmd --permanent --zone dockerc --add-port 8443/tcp
firewall-cmd --permanent --zone dockerc --add-port 53/udp
firewall-cmd --permanent --zone dockerc --add-port 8053/udp
firewall-cmd --reload
```
 

Simple Install of OpenShift:
```
#yum install origin-clients	#already done
oc cluster up
or
oc cluster up --metrics
or
oc cluster up --public-hostname=144.229.218.215 --routing-suffix=cloud.vmx.id
```

Result:
```
Login to server ...
Creating initial project "myproject" ...
Server Information ...
OpenShift server started.

The server is accessible via web console at:
    https://127.0.0.1:8443

You are logged in as:
    User:     developer
    Password: <any value>

To login as administrator:
    oc login -u system:admin
```



https://confluence/display/STP/Prerequisites+for+Tools+Configuration
.............. move all above info to the bottom ...............


lvextend -l +100%FREE /dev/vg00/root


lvextend -L5G /dev/vg00/local
lvextend -L5G /dev/vg00/var

#### Add system drives [/var/cache, /opt/tools] '[OPENSHIFT SERVER]'

echo "***** CREATING LVM GROUP/VOLUME (/dev/sdd) & MOUNTING DRIVE (/dev/varvg/cachelv1) FOR /var/cache *****"
pvcreate /dev/sdd 
vgcreate varvg /dev/sdd
lvcreate --name cachelv1 -l 100%FREE varvg
mkfs.ext4 /dev/varvg/cachelv1
mkdir /var/cache
mount /dev/varvg/cachelv1 /var/cache
echo "/dev/varvg/cachelv1 /var/cache ext4 defaults 0 0" >> /etc/fstab

echo "***** CREATING LVM GROUP/VOLUME (/dev/sde) & MOUNTING DRIVE (/dev/optvg/toolslv1) FOR /opt/tools *****"
pvcreate /dev/sde 
vgcreate optvg /dev/sde
lvcreate --name toolslv1 -l 100%FREE optvg
mkfs.ext4 /dev/optvg/toolslv1
mkdir /opt/tools
mount /dev/optvg/toolslv1 /opt/tools
echo "/dev/optvg/toolslv1 /opt/tools ext4 defaults 0 0" >> /etc/fstab

```

  
## UNDER DEVELOPMENT ... aka: not complete
  
from: https://linoxide.com/linux-how-to/setup-openshift-origin-centos-7/
```
vi /etc/selinux/config
```

check/update settings to reflect  
SELINUXTYPE=targeted  

bring the system into full compliance  
```
yum update -y
```

Add the insecure registry by editing the file /etc/docker/daemon.json with following content  
```
{
    "insecure-registries" : [ "192.168.0.0/16" ]
}
```

update and restart the service  
```
sudo systemctl daemon-reload
sudo systemctl restart docker
```

Find the latest release  
```
https://github.com/openshift/origin/releases
```

download it 
```
wget https://github.com/openshift/origin/releases/download/v3.11.0/openshift-origin-client-tools-v3.11.0-0cbc58b-linux-64bit.tar.gz -O /openshift/openshift-origin-client-tools-v3.11.0-0cbc58b-linux-64bit.tar.gz

wget https://github.com/openshift/origin/releases/download/v3.11.0/openshift-origin-server-v3.11.0-0cbc58b-linux-64bit.tar.gz -O /openshift/openshift-origin-server-v3.11.0-0cbc58b-linux-64bit.tar.gz

```

extract, and implement binaries  
```
cd /openshift
tar -zxf openshift-origin-server-*.tar.gz

mv openshift-server* openshift-server

cd openshift-server

mv k* o* /usr/local/sbin/

```










Yum upgrade stuck resolution ... 

2
down vote
This is the way I did fix one CentOS 7 server with 471 dupes.

First I had to install yum utils:

yum install yum-utils
Have tried yum-complete-transaction and other stuff without luck, I gave up the transaction with:

yum-complete-transaction --cleanup-only
Then I got a sorted list of duplicated packages so I could identify older versions to remove filtering even and odd lines later:

package-cleanup --dupes | sort -u > dupes.out
Then I got a uninstall list from this sorted file this way:

cat dupes.out | grep -v 'Loaded plugins:' | sort -u | awk 'NR % 2 == 1' > uninstall.in
Then I removed from rpm database the old versions:

for f in `cat uninstall.in`; do rpm -e --nodeps -f --justdb $f; done
Finally I could continue on regular system upgrade:

yum upgrade




&&&&&&&&&&&& NOTES ******************

https://stackoverflow.com/questions/26166550/set-docker-opts-in-centos

https://sandro-keil.de/blog/docker-daemon-tuning-and-json-file-configuration/
samples

storage ..
{
  "storage-driver": "overlay2",
  "graph": "/var/lib/docker",
  "log-driver": "json-file",
  "log-opts": {
    "max-size": "10m",
    "max-file": "2"
  },
  "debug": false,
  "userns-remap": "1000:1000"
} 

security ...
{
  "debug": true,
  "tls": true,
  "tlscert": "/var/docker/server.pem",
  "tlskey": "/var/docker/serverkey.pem",
  "hosts": ["tcp://192.168.59.3:2376"]
}


/etc/docker/daemon.json
{
  "selinux-enabled": true,
  "graph": "/var/lib/docker",
  "log-driver": "json-file",
  "log-opts": {
    "max-size": "10m",
    "max-file": "2"
  },
  "debug": false,
  "userns-remap": "1000:1000"
} 







For CentOS 7 (RHEL 7):
good info:  https://stackoverflow.com/questions/26166550/set-docker-opts-in-centos

edit: /usr/lib/systemd/system/docker.service

add, under [Service]
EnvironmentFile=-/etc/sysconfig/docker
#ensure it is above the 'ExecStart' entry

Find the systemd docker.service unit file. Mine is located at: /usr/lib/systemd/system/docker.service
# /etc/sysconfig/docker
OPTIONS=--selinux-enabled --insecure-registry 172.30.0.0/16

<<<GO













































































































































































































































































































































































































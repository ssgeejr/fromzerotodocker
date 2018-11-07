# #This can be used if you KNOW you have access to the epel-release repo file 
# #otherwise, use the wget and direct install
#yum install -y epel-release


#### *as root*
#### create the devops user and enable ssh login _pro-tip: never use password, no matter the 'reason'_
```
adduser devops
cd /home/devops
mkdir .ssh

cat << EOF > /home/devops/.ssh/config
Host *
    StrictHostKeyChecking no
EOF

cat << EOF >> /home/devops/.ssh/authorized_keys
<<YOUR_PUBLIC_KEY>>
EOF

chown -R devops.devops /home/devops/.ssh
chmod 600 /home/devops/.ssh/*
chmod 700 /home/devops/.ssh
```

#### Enable password-less sudo for ansible ssh (optional)
```
echo "%devops ALL=(ALL) NOPASSWD: LOG_INPUT: ALL"  > /etc/sudoers.d/scm-devops
```

#### Add required repo's
```
wget https://dl.fedoraproject.org/pub/epel/epel-release-latest-7.noarch.rpm
yum install -y epel-release-latest-7.noarch.rpm
rm -f epel-release-latest-7.noarch.rpm
yum install -y ftp://bo.mirror.garr.it/1/slc/centos/7.1.1503/extras/x86_64/Packages/container-selinux-2.21-1.el7.noarch.rpm
```

#### yum install packages
```
yum install -y wget curl ansible git tmux python-pip python-dev build-essential lynx elinks tree zip unzip java-1.8.0-openjdk
```

#### grab the tmxu config 
```
wget https://raw.githubusercontent.com/ssgeejr/config/master/.tmux.conf -O /etc/tmux.conf
```

#### install docker
```
curl -fsSL https://get.docker.com/ | sh
```

#### update pip and install docker-compose
```
pip install -U pip
pip install --ignore-installed docker-compose
```

#### enable and restart docker 
```
systemctl start docker
systemctl enable docker
```

#### Add maven (only for build machines ... this should honestly be installed to your Jenkins docker image ... but, you do you)
```
cd /opt
mkdir tools
cd /opt/tools
wget http://www.trieuvan.com/apache/maven/maven-3/3.6.0/binaries/apache-maven-3.6.0-bin.tar.gz
tar -zxvf apache-maven-3.6.0-bin.tar.gz
rm -f apache-maven-3.6.0-bin.tar.gz
mv apache-maven-3.6.0 maven-3.6.0
update-alternatives --install /usr/bin/mvn maven /opt/tools/maven-3.6.0/bin/mvn 1

echo "M2_HOME=/opt/tools/maven-3.6.0" >> /etc/profile
echo "export M2_HOME" >> /etc/profile

. /etc/profile
```
  
_pro-tip:  create the folder; /opt/tools/repo, edit /opt/tools/maven-3.6.0/conf/settings.xml and add to the correct location. This is done to ensure your root directory or home directory does not fill up with library files_  
```
<localRepository>/opt/tools/repo</localRepository>
```


#### give the devops user docker permissions (this should be configured to match your openshift instance if you are going to use it as an orchestrator or k8s
```
usermod -aG docker devops
```

_pro-tip:  you will need to log out and back in for your new group settings to take effect_

Now it's time to **make it rain** and get your development groove on!  





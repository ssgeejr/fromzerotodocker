# LVM Drive Mapping


#### OpenShift Server:
```
sdb (openshift)    8:16   0   50G  0 disk
	/opt/openshift
sdb (docker)      8:32   0   20G  0 disk
	/var/lib/docker

Extended to 5G (existing)
vg00-local 253:9    0  256M  0 lvm  /usr/local
```

#### DB-Server: 
```
sdb (docker)      8:16   0   20G  0 disk
	/var/lib/docker
sdc (couchbase)   8:32   0   20G  0 disk
	/opt/couchbase
sdd (kafka)       8:48   0   20G  0 disk
	/opt/kafka
sde (redis)       8:64   0    5G  0 disk
	/opt/redis
```

_pro-tip: to force recognition of vmware drives without rebooting your VM:_
```
echo "- - -" > /sys/class/scsi_host/host0/scan
fdisk -l
lsblk
```


#### Add openshift drives '[OPENSHIFT SERVER]'
```
pvcreate /dev/sdb 
vgcreate openshiftvg /dev/sdb
lvcreate --name datalv1 -l 100%FREE openshiftvg
mkfs.ext4 /dev/openshiftvg/datalv1
mkdir /opt/openshift
mount /dev/openshiftvg/datalv1 /opt/openshift
echo "/dev/openshiftvg/datalv1 /opt/openshift ext4 defaults 0 0" >> /etc/fstab

systemctl stop docker
rm -rf /var/lib/docker
pvcreate /dev/sdc 
vgcreate dockervg /dev/sdc
lvcreate --name datalv1 -l 100%FREE dockervg
mkfs.ext4 /dev/dockervg/datalv1
mkdir /var/lib/docker
mount /dev/dockervg/datalv1 /var/lib/docker
echo "/dev/dockervg/datalv1 /var/lib/docker ext4 defaults 0 0" >> /etc/fstab
systemctl start docker

```

#### ensure that /opt drive is writable by the devops user and that /opt/openshift is owned by devops (debatable on the ownership, you do you)

```
chgrp devops /opt
chmod -R 775 /opt
chown -R devops:devops /opt/openshift
```


#### Add openshift drives '[DATABASE SERVER]'
```
echo "***** CREATING LVM GROUP/VOLUME (/dev/sdb) & MOUNTING DRIVE (/var/lib/docker) FOR DOCKER *****"
systemctl stop docker
rm -rf /var/lib/docker
pvcreate /dev/sdb 
vgcreate dockervg /dev/sdb
lvcreate --name datalv1 -l 100%FREE dockervg
mkfs.ext4 /dev/dockervg/datalv1
mkdir /var/lib/docker
mount /dev/dockervg/datalv1 /var/lib/docker
echo "/dev/dockervg/datalv1 /var/lib/docker ext4 defaults 0 0" >> /etc/fstab
systemctl start docker

echo "***** CREATING LVM GROUP/VOLUME (/dev/sdc) & MOUNTING DRIVE (/opt/couchbase) FOR COUCHBASE *****"
pvcreate /dev/sdc 
vgcreate couchbasevg /dev/sdc
lvcreate --name datalv1 -l 100%FREE couchbasevg
mkfs.ext4 /dev/couchbasevg/datalv1
mkdir /opt/couchbase
mount /dev/couchbasevg/datalv1 /opt/couchbase
echo "/dev/couchbasevg/datalv1 /opt/couchbase ext4 defaults 0 0" >> /etc/fstab

echo "***** CREATING LVM GROUP/VOLUME (/dev/sdd) & MOUNTING DRIVE (/opt/kafka) FOR KAFKA *****"
pvcreate /dev/sdd 
vgcreate kafkavg /dev/sdd
lvcreate --name datalv1 -l 100%FREE kafkavg
mkfs.ext4 /dev/kafkavg/datalv1
mkdir /opt/kafka
mount /dev/kafkavg/datalv1 /opt/kafka
echo "/dev/kafkavg/datalv1 /opt/kafka ext4 defaults 0 0" >> /etc/fstab

echo "***** CREATING LVM GROUP/VOLUME (/dev/sde) & MOUNTING DRIVE (/opt/redis) FOR REDIS *****"
pvcreate /dev/sde 
vgcreate redisvg /dev/sde
lvcreate --name datalv1 -l 100%FREE redisvg
mkfs.ext4 /dev/redisvg/datalv1
mkdir /opt/redis
mount /dev/redisvg/datalv1 /opt/redis
echo "/dev/redisvg/datalv1 /opt/redis ext4 defaults 0 0" >> /etc/fstab

```

#### ensure that /opt drive is writable by the devops user and that /opt/couchbase,redis,kafka is owned by devops (debatable on the ownership, you do you)

```
chgrp devops /opt
chmod -R 775 /opt
chown -R devops:devops /opt/couchbase
chown -R devops:devops /opt/redis
chown -R devops:devops /opt/kafka
```








### Final checks ... 
Your systems should look similar to this*

#### OpenShift Server
```
NAME                        MAJ:MIN RM  SIZE RO TYPE MOUNTPOINT
fd0                           2:0    1    4K  0 disk
sda                           8:0    0   60G  0 disk
├─sda1                        8:1    0  512M  0 part /boot
└─sda2                        8:2    0 59.5G  0 part
  ├─vg00-root               253:0    0    5G  0 lvm  /
  ├─vg00-swapA              253:1    0    2G  0 lvm  [SWAP]
  ├─vg00-swapB              253:2    0    2G  0 lvm  [SWAP]
  ├─vg00-etcoptopsware      253:3    0   52M  0 lvm  /etc/opt/opsware
  ├─vg00-opsware            253:4    0    5G  0 lvm  /var/opt/opsware
  ├─vg00-logopsware         253:5    0  256M  0 lvm  /var/log/opsware
  ├─vg00-log                253:6    0    1G  0 lvm  /var/log
  ├─vg00-var                253:7    0    1G  0 lvm  /var
  ├─vg00-usropenv           253:8    0    4G  0 lvm  /usr/openv
  ├─vg00-local              253:9    0  256M  0 lvm  /usr/local
  ├─vg00-tmp                253:10   0    1G  0 lvm  /tmp
  ├─vg00-tools              253:11   0    3G  0 lvm  /tools
  ├─vg00-systoolssars       253:12   0    5G  0 lvm  /systools/SARS
  ├─vg00-systoolsmonitoring 253:13   0    1G  0 lvm  /systools/monitoring
  ├─vg00-systools           253:14   0    1G  0 lvm  /systools
  ├─vg00-optopsware         253:15   0  512M  0 lvm  /opt/opsware
  ├─vg00-opt                253:16   0    1G  0 lvm  /opt
  └─vg00-home               253:17   0  256M  0 lvm  /home
sdb                           8:16   0   50G  0 disk
└─openshiftvg-datalv1       253:18   0   50G  0 lvm  /opt/openshift
sdc                           8:32   0   20G  0 disk
└─dockervg-datalv1          253:19   0   20G  0 lvm  /var/lib/docker
sr0                          11:0    1 1024M  0 rom
```


#### Database Server
```
NAME                        MAJ:MIN RM  SIZE RO TYPE MOUNTPOINT
fd0                           2:0    1    4K  0 disk
sda                           8:0    0   60G  0 disk
├─sda1                        8:1    0  512M  0 part /boot
└─sda2                        8:2    0 59.5G  0 part
  ├─vg00-root               253:0    0    5G  0 lvm  /
  ├─vg00-swapA              253:1    0    2G  0 lvm  [SWAP]
  ├─vg00-swapB              253:2    0    2G  0 lvm  [SWAP]
  ├─vg00-etcoptopsware      253:3    0   52M  0 lvm  /etc/opt/opsware
  ├─vg00-opsware            253:4    0    5G  0 lvm  /var/opt/opsware
  ├─vg00-logopsware         253:5    0  256M  0 lvm  /var/log/opsware
  ├─vg00-log                253:6    0    1G  0 lvm  /var/log
  ├─vg00-var                253:7    0    1G  0 lvm  /var
  ├─vg00-usropenv           253:8    0    4G  0 lvm  /usr/openv
  ├─vg00-local              253:9    0  256M  0 lvm  /usr/local
  ├─vg00-tmp                253:10   0    1G  0 lvm  /tmp
  ├─vg00-tools              253:11   0    3G  0 lvm  /tools
  ├─vg00-systoolssars       253:12   0    5G  0 lvm  /systools/SARS
  ├─vg00-systoolsmonitoring 253:13   0    1G  0 lvm  /systools/monitoring
  ├─vg00-systools           253:14   0    1G  0 lvm  /systools
  ├─vg00-optopsware         253:15   0  512M  0 lvm  /opt/opsware
  ├─vg00-opt                253:16   0    1G  0 lvm  /opt
  └─vg00-home               253:17   0  256M  0 lvm  /home
sdb                           8:16   0   20G  0 disk
└─dockervg-datalv1          253:18   0   20G  0 lvm  /var/lib/docker
sdc                           8:32   0   20G  0 disk
└─couchbasevg-datalv1       253:19   0   20G  0 lvm  /opt/couchbase
sdd                           8:48   0   20G  0 disk
└─kafkavg-datalv1           253:20   0   20G  0 lvm  /opt/kafka
sde                           8:64   0    5G  0 disk
└─redisvg-datalv1           253:21   0    5G  0 lvm  /opt/redis
sr0                          11:0    1 1024M  0 rom
```



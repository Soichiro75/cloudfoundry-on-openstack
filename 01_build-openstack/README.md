# cloudfoundry-on-openstack

## Helpful Links
- [RDO Packstack Quickstart](https://www.rdoproject.org/install/quickstart/)
- [Install Guide RDO](http://docs.openstack.org/liberty/ja/install-guide-rdo/)
- [docs.cloudfoundry](http://docs.cloudfoundry.org/deploying/openstack/index.html)
- [bosh-init on openstack](https://bosh.io/docs/init-openstack.html)


## Install RDO by Using Packstack

### Install CentOS 7.2 for RDO

- Install CentOS-7-x86_64-DVD-1511.iso with minimum configuration
 - Set Language English
 - Set Keyboard YOURKEYBOARD(Japanese)
 - Set date-time YOURTIMEZONE(Asia/Tokyo)
 - Need not to set up Network, Hostname, NTP, etc..


### Previously Set Up CentOS 7.2 for RDO

- Hostname

```
hostname
  localhost.localdomain

hostnamectl set-hostname openstack.ts.local

hostname
  openstack.ts.local

cat /etc/hostname
  openstack.ts.local
```


- IP Address

```

# confirm device name
#Note:  The below case DEVICE NAME is "enp2s0f0",
#       you should replace "enp2s0f0" to your DEVICE NAME in the following procedures.
nmcli d
  DEVICE    TYPE      STATE         CONNECTION
  enp2s0f0  ethernet  disconnected  --
  lo        loopback  unmanaged     --

nmcli d show enp2s0f0

# set up
nmcli c mod enp2s0f0 ipv4.addresses 192.168.101.1/24
nmcli c mod enp2s0f0 ipv4.gateway 192.168.101.254
nmcli c mod enp2s0f0 ipv4.method manual
nmcli c mod enp2s0f0 connection.autoconnect yes

# reflect the setting
nmcli c down enp2s0f0; nmcli c up enp2s0f0

# confirm the reflecting
nmcli d show enp2s0f0
  DEVICE    TYPE      STATE         CONNECTION
  enp2s0f0  ethernet  connected     enp2s0f0
  lo        loopback  unmanaged     --

ip addr show

ping 8.8.8.8
  Ctrl+C
```


- DNS

```
# set up
nmcli c mod enp2s0f0 ipv4.dns 8.8.8.8,8.8.4.4
nmcli c mod enp2s0f0 ipv4.dns-search ts.local

# confirm the setting
cat /etc/sysconfig/network-scripts/ifcfg-enp2s0f0
cat /etc/resolv.conf

# reflect the setting
nmcli c down enp2s0f0; nmcli c up enp2s0f0

# confirm the reflecting
ping www.google.com
  Ctrl + C
```


- LOCALE

```
# https://www.rdoproject.org/install/quickstart/ > Summary for the impatient

# prior confirmation
localectl status

localectl list-locales | grep -ie en_ | grep -ie utf8

# set up
localectl set-locale LANG=en_US.utf-8

cat /etc/locale.conf
  LANG=en_US.utf-8

vi /etc/environment
  LANG=en_US.utf-8
  LC_ALL=en_US.utf-8

# reflect the setting
exit
login

# reflect the reflecting
localectl status

locale
```

<!-- You don't need not to make disable SELinux, maybe.
https://www.rdoproject.org/install/quickstart/
- SELinux
//
```
[root@centos ~]# getenforce
Enforcing

[root@centos ~]# setenforce 0

[root@centos ~]# getenforce
Permissive

[root@centos ~]# vi /etc/sysconfig/selinux
(before):
SELINUX=enforcing

(after):
SELINUX=disabled
```
-->


- FireWall

```
# disable FireWall
systemctl stop firewalld
systemctl disable firewalld
```


- YumRepository

```
# set up
# add riken repository (because I'm in Japan)
vi /etc/yum.repos.d/CentOS-Base.repo
  [base-riken]
  name=CentOS-$releasever - Base-riken
  baseurl=http://ftp.riken.jp/Linux/centos/$releasever/os/$basearch/
  gpgcheck=1
  enabled=1
  gpgkey=file:///etc/pki/rpm-gpg/RPM-GPG-KEY-CentOS-7

# confirm there are Extras repositories
# On CentOS, the Extras repository provides the RPM that enables the OpenStack repository
grep -i Extras /etc/yum.repos.d/*

# reflect the setting and confirm the reflecting
yum clean all

yum list

# Options:
yum -y install openssh-server openssh-clients telnet wget man curl bind-utils git tree unzip
```


- TimeZone

```
# prior confirmation
timedatectl status
  Local time: Thu 2016-07-14 15:50:45 JST
  Universal time: Thu 2016-07-14 06:50:45 UTC
  RTC time: Thu 2016-07-14 06:50:45
  Time zone: Asia/Tokyo (JST, +0900)
  NTP enabled: n/a
  NTP synchronized: no
  RTC in local TZ: no
  DST active: n/a

timedatectl list-timezones | grep Tokyo
  Asia/Tokyo

# set up
timedatectl set-timezone Asia/Tokyo

# confirm the reflecting
timedatectl status
  Local time: Thu 2016-07-14 15:50:45 JST
  Universal time: Thu 2016-07-14 06:50:45 UTC
  RTC time: Thu 2016-07-14 06:50:45
  Time zone: Asia/Tokyo (JST, +0900)
  NTP enabled: n/a
  NTP synchronized: no
  RTC in local TZ: no
  DST active: n/a

date

```


- NTP

```
# set up and confirm
yum -y install chrony

rpm -qa | grep chrony
  chrony-2.1.1-1.el7.centos.x86_64

# if you have NTP Server you set up below
## vi /etc/chrony.conf
## server xxx.xxx.xxx

# reflect the setting

## if you installed ntpd, you should stop ntpd as below
## systemctl stop ntpd.service
## systemctl disable ntpd.service

systemctl enable chronyd.service

systemctl restart chronyd.service

systemctl list-unit-files --type service | egrep "(ntp|chronyd)"
  chronyd.service   enabled

chronyc sources
```


- Network System

```
# https://www.rdoproject.org/install/quickstart/ > Step0 Network
systemctl disable NetworkManager

systemctl stop NetworkManager

systemctl enable network

systemctl start network
```




### Install RDO

- Software Repositories

```
# set up the RDO repository to install OpenStack

# Below procedures is for CentOS not RHEL.
# Probably, it is more good to choice Liberty than mitaka.
# https://bosh.io/docs/init-openstack.html > supported releases: Liberty (actively tested)
# But I try to use mitaka
yum install -y centos-release-openstack-mitaka

# Update your current packages
# Maybe, it takes long time (In my case, 5 minutes)
yum update -y
```


- Install Packstack Installer

```
# Install Packstack Installer
yum install -y openstack-packstack
```


- Run Packstack to install OpenStack

```
# Run Packstack to install OpenStack
# Maybe, it takes long time (In my case, 25 minutes)
packstack --allinone
  Welcome to the Packstack setup utility
    (Omitted)
  **** Installation completed successfully ******

  Additional information:
  * A new answerfile was created in: /root/packstack-answers-20160714-161937.txt
  * Time synchronization installation was skipped. Please note that unsynchronized time on server instances might   be problem for some OpenStack components.
  * File /root/keystonerc_admin has been created on OpenStack client host 192.168.101.1. To use the command line  tools you need to source the file.
  * To access the OpenStack Dashboard browse to http://192.168.101.1/dashboard .
  Please, find your login credentials stored in the keystonerc_admin in your home directory.
  * To use Nagios, browse to http://192.168.101.1/nagios username: nagiosadmin, password: f94c599b45fd4fe3
  * Because of the kernel update the host 192.168.101.1 requires reboot.
  * The installation log file is available at: /var/tmp/packstack/20160714-161936-zA8aQQ/openstack-setup.log
  * The generated manifests are available at: /var/tmp/packstack/20160714-161936-zA8aQQ/manifests

```


### Access Test

- prior confirmation
```
# Check OpenStack login account
cat ~/keystonerc_admin
  unset OS_SERVICE_TOKEN
  export OS_USERNAME=admin
  export OS_PASSWORD=730cbb2788888888
  export OS_AUTH_URL=http://192.168.101.1:5000/v2.0
  export PS1='[\u@\h \W(keystone_admin)]\$ '
  export OS_TENANT_NAME=admin
  export OS_REGION_NAME=RegionOne
```

- Access
```
# Open by WebBrowser "http://$YOURIP/dashboard"　on your another PC
# admin/[Password is written in the above keystonerc_admin file]
http://192.168.101.1/dashboard
```

<img src="https://github.com/Soichiro75/cloudfoundry-on-openstack/blob/master/01_build-openstack/images/2016-07-14_01_LoginOpenStackDashBoard.png" width="320px" title="LoginOpenStackDashBoard">


<img src="https://github.com/Soichiro75/coudfoundry-on-openstack/blob/master/images/2016-07-14_02_OpenStackDashBoard.png" width="320px" title="OpenStackDashBoard">

```
# Open by WebBrowser "http://$YOURIP/nagios"　on your another PC
# Username and Password are written the above success message
# "username: nagiosadmin, password: f94c599b45fd4fe3"
http://192.168.101.1/nagios
```

<img src="https://github.com/Soichiro75/coudfoundry-on-openstack/blob/master/images/2016-07-14_03_LoginNagios.png" width="320px" title="LoginNagios">


<img src="https://github.com/Soichiro75/coudfoundry-on-openstack/blob/master/images/2016-07-14_04_NagiosDashBoard.png" width="320px" title="NagiosDashBoard">

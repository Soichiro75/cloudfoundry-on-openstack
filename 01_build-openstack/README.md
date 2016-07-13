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

### Previously Set Up

- Hostname

```
# hostname
localhost.localdomain

# hostnamectl set-hostname centos

# hostname
centos

# cat /etc/hostname
centos

```


- IP Address

```
nmcli d
nmcli d show ens160

nmcli c mod ens160 ipv4.method manual
nmcli c mod ens160 ipv4.addresses 10.10.10.2/24
nmcli c mod ens160 ipv4.gateway 10.10.10.1

systemctl restart network

nmcli d show ens160

ip addr show
```


- Network System

```
$ sudo systemctl disable NetworkManager
$ sudo systemctl stop NetworkManager
$ sudo systemctl enable network
$ sudo systemctl start network
```


- LOCALE

```
date
localectl status
localectl list-locales
localectl set-locale LANG=en_US.utf-8
cat /etc/locale.conf
  LANG=en_US.utf-8
  LC_ALL=en_US.utf-8

source /etc/locale.conf
date
```

- DNS

```
nmcli c mod ens160 ipv4.dns 8.8.8.8,8.8.4.4
nmcli c mod ens160 ipv4.dns-search hogehoge.com
cat /etc/resolv.conf
cat /etc/sysconfig/network-s/ifcfg-e
systemctl restart network
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

[root@centos ~]# systemctl stop firewalld

[root@centos ~]# systemctl disable firewalld
```


- YumRepository

```
#add yum riken
[base-riken]
name=CentOS-$releasever - Base-riken
baseurl=http://ftp.riken.jp/Linux/centos/$releasever/os/$basearch/
gpgcheck=1
enabled=1
gpgkey=file:///etc/pki/rpm-gpg/RPM-GPG-KEY-CentOS-7


grep -i ./* "Extras"
# On CentOS, the Extras repository provides the RPM that enables the OpenStack repository.


yum clean all
yum lists

# Options below:
yum -y install openssh-server openssh-clients telnet wget man curl bind-utils git tree unzip
```


- TimeZone

```
timedatectl status
timedatectl list-timezones
timedatectl set-timezone Asia/Tokyo
timedatectl status
```


- NTP

```
# yum -y install chrony

# rpm -qa | grep chrony
chrony-1.29.1-1.el7.centos.x86_64

vi /etc/chrony.conf
  server


# systemctl stop ntpd.service
# systemctl disable ntpd.service

systemctl enable chronyd.service
systemctl list-unit-files --type service | egrep "(ntp|chronyd)"
systemctl restart chronyd.service

chronyc sources
```



### Install RDO

- Software Repositories

```
yum install -y centos-release-openstack-mitaka
yum update -y
yum install -y openstack-packstack
```


- Install Packstack Installer

```
yum install -y openstack-packstack
```


- Run Packstack to install OpenStack

```
packstack --allinone
```


### Access Test

- Access
 - Check OpenStack login account this command `cat /root/keystonerc_admin`
 - http://$YOURIP/dashboard
  - admin/[Password is written in the above keystonerc_admin file]

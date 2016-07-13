# cloudfoundry-on-openstack

## Helpful Links
- [RDO Packstack Quickstart](https://www.rdoproject.org/install/quickstart/)
- [Install Guide RDO](http://docs.openstack.org/liberty/ja/install-guide-rdo/)
- [docs.cloudfoundry](http://docs.cloudfoundry.org/deploying/openstack/index.html)
- [bosh-init on openstack](https://bosh.io/docs/init-openstack.html)



## Install RDO by Using Packstack

### Set Up CentOS 7.2 for RDO

- Install CentOS-7-x86_64-DVD-1511.iso with minimum configuration
 - Language is English

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

# cloudfoundry-on-openstack

This document is still incomplete.

You can build Cloud Foundry test environment on single node OpenStack, if you can do following "Table of Contents" order.

In this documents,

Using Products:
  - HP ProLiant DL380 G6:
    - CPU:    2Processor (Quad-Core Xeon X5560 2.8GHz)
    - Memory: 88GB
    - HDD:    400GB(RAID0), 500GB(RAID5)
  - [CentOS-7-x86_64-1511](https://www.centos.org/download/)
  - [RDO](https://www.rdoproject.org/)
  - [PackStack](https://www.rdoproject.org/repos/)
  - [OpenStack Mitaka](http://www.openstack.org/software/mitaka/)


## Table of Contents

- [01_build-openstack](https://github.com/Soichiro75/cloudfoundry-on-openstack/tree/master/01_build-openstack): How to build single node OpenStack environment by using RDO Packstack.
- [02_previously-setup-openstack](https://github.com/Soichiro75/cloudfoundry-on-openstack/tree/master/02_previously-setup-openstack): How to setup OpenStack environment to deploy Cloud Foundry.


## Helpful Links
- [RDO Packstack Quickstart](https://www.rdoproject.org/install/quickstart/)
- [Install Guide RDO](http://docs.openstack.org/liberty/ja/install-guide-rdo/)
- [docs.cloudfoundry](http://docs.cloudfoundry.org/deploying/openstack/index.html)
- [bosh-init on openstack](https://bosh.io/docs/init-openstack.html)

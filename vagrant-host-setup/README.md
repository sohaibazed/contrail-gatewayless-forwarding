# Vagrant-Host-Setup

This repo provide instructions and scripts setting up BMS host machine for Vagrant & Ansible.

## CentOS 7.5 Vagrant Installation

Install CentOS 7.5 on the BMS host and following below steps:

```bash
host> git clone https://gitlab.com/sohaibazed/contrail-vagrant-deployer.git
host> cd contrail-vagrant-deployer/vagrant-host-setup
host> ./setup_vagrant_centos.sh
```
Note: "setup_vagrant_centos.sh" script will take care of installtion of VirtualBox 5.2, Vagrant, Ansible and JunOS python & ansible modules.

In case you would also like setting up bridge interfaces for the Host please use following config as reference.

### br0 interface config

```bash
cat /etc/sysconfig/network-scripts/ifcfg-br0
DEVICE=br0
BOOTPROTO=static
IPADDR=10.13.132.65
NETMASK=255.255.254.0
GATEWAY=10.13.132.1
DNS1=8.8.8.8
ONBOOT=yes
TYPE=Bridge
NM_CONTROLLED=no

cat /etc/sysconfig/network-scripts/ifcfg-eno0
NM_CONTROLLED=no
BOOTPROTO=none
DEVICE=eno0
TYPE=Ethernet
ONBOOT=yes
BRIDGE=br0
```

### br1 interface config

```bash
cat /etc/sysconfig/network-scripts/ifcfg-br1
DEVICE=br1
BOOTPROTO=static
IPADDR=192.168.100.10
NETMASK=255.255.255.0
ONBOOT=yes
TYPE=Bridge
NM_CONTROLLED=no

cat /etc/sysconfig/network-scripts/ifcfg-en1
NM_CONTROLLED=no
BOOTPROTO=none
DEVICE=eno1
TYPE=Ethernet
ONBOOT=yes
BRIDGE=br1
```
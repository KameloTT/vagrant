# Proxmox Cluster Installation

# Prerequisites

1. Automation is developed with using vagrant+ansible. So "Vagrant tool" has to be instlled in advance.
2. Vagrant uses "Virtualbox" hypervisor. So "Virtualbox" Hypervisor has to be installed in advance.


# Installation

```
vagrant up
```

Vagrant will deploy 3 VMs. First two VM's (pve1,pve2) has one management network and one private network(no ip).
Each VM has additional disk /dev/sdb for testing purposes.
Third VM (pve-nfs) is design for delived NFS share inside management network


# Post steps for Proxmox demo.

1. Login

```
https://192.168.56.101:8006 root/compaq
```

2. Create a new cluster(otus)

3. Login to second node 

```
https://192.168.56.102:8006   root/compaq
```

4. Add second node to cluster by copy joining info from first node and paste on second node

5. Setup ceph(datacenter level)

5.1 Create a new pool - pve1->pools->create pool1 replicas=2

6. Create monitor on second node(pve2-> ceph-monitoris)

7. Create osd one per node on /dev/sdb

8. Create nfs storage endpoint (in datacenter -> storage -> add -> nfs)

9. Download iso image (datacenter-pve->otus-nfs -left menu, click on pve1 arrow -> iso images). Go to google and type "apline image" 
https://dl-cdn.alpinelinux.org/alpine/v3.18/releases/x86_64/alpine-standard-3.18.4-x86_64.iso


10. Create linux bridge on both servers

First server:
Name: vmbr0
ipv4: 172.24.2.1/24
Bridge ports: eth1

Second server:
Name: vmbr0
ipv4: 172.24.2.2/24
Bridge ports: eth1

11. Install packages

systemctl stop pvedaemon.service;apt install libpve-network-perl ifupdown2 openvswitch-switch -y

12. Add vxlnax zone in Datacenter -> SDN.

name: vxlan
MTU: 1450
Type: vxlan

13. Add vnet

name: vnet1
zone: vxlan
tag: 10000

14. Create two new VMs with "vnet1" subnet and mtu 1500

15. Disable "kvm hardware virtualization" in VM settings

16. add ip addresses to VMs

First vm:
ip add add dev eth0 10.0.0.1/24
ip link set eth0 up

Second vm:
ip add add dev eth0 10.0.0.2/24
ip link set eth0 up

ping 10.0.0.1
ping 10.0.0.2


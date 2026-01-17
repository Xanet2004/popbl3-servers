This document describes the network topology used in this project and explains how to migrate
from a NAT-based topology (used in the [in-class activity](https://docs.google.com/document/d/1W8xz1h4jfhF81_PzgodD-vRKpa6XWWj4LW81x_kvXgE/edit?tab=t.0)) to an Internal Network (intnet)
topology, as required in POPBL3.

The goal is to reproduce the same logical network design but replacing NAT networks with
Internal Networks.

For a clearer network topology visualisation see: [Activity topology](https://drive.google.com/file/d/1PzRkdtsm8TTbWVU3UdtZ7XZOLFbScpd3/view)
# Index
- [Create NAT networks](/linux/vbox/networks-creation/nat-networks.md)
- [Create Internal networks](/linux/vbox/networks-creation/internal-networks.md)

## Migration from NAT Networks to Internal Networks

If the virtual machines were already connected to NAT Networks, the following changes are
required to migrate the topology to Internal Networks:

- NAT Networks are removed or detached from the virtual machines
- Internal Networks are created with the same logical segmentation
- Each network interface of every virtual machine is reattached to the corresponding
  Internal Network
- DHCP is disabled at the VirtualBox level, as it will be provided by KEA inside the servers

The logical topology (number of networks, machines and subnets) remains unchanged; only the
networking backend provided by VirtualBox is replaced.

## zaldua1zerb1 server from NAT Networks to Internal Networks

### Delete

```powershell title="natnetwork creation"
# Stop networks
VBoxManage natnetwork stop --netname "zaldua1"
VBoxManage natnetwork stop --netname "zaldua2"
VBoxManage natnetwork stop --netname "zaldua3"

# Delete networks
VBoxManage natnetwork remove --netname "zaldua1"
VBoxManage natnetwork remove --netname "zaldua2"
VBoxManage natnetwork remove --netname "zaldua3"
```

### Remove NAT Networks from Server interfaces

```powershell title="remove from zaldua1zerb1"
vboxmanage modifyvm "zaldua1zerb1" --nic6 none
vboxmanage modifyvm "zaldua1zerb1" --nic7 none
vboxmanage modifyvm "zaldua1zerb1" --nic8 none
```

> Note
> Allways use showvminfo to confirm changes on the machine

### Attach Internal networks on the Server

```powershell title="add intnet on zaldua1zerb1"
vboxmanage modifyvm "zaldua1zerb1" --nic6 intnet --mac-address6 000102030101 --intnet6 zaldua1
vboxmanage modifyvm "zaldua1zerb1" --nic7 intnet --mac-address7 000102030102 --intnet7 zaldua2
vboxmanage modifyvm "zaldua1zerb1" --nic8 intnet --mac-address8 000102030103 --intnet8 zaldua3
```

> Note
> Allways use showvminfo to confirm changes on the machine

### Add port forwarding if needed

```powershell title="port forwarding fro zaldua1zerb1"
VBoxManage modifyvm "zaldua1zerb1" --natpf1 "sshz1z1,tcp,,2204,192.168.42.4,22"
```

### Snapshot

Always take a snapshot to save the changes.

```powershell title="snapshot"
VBoxManage snapshot "zaldua1bez1" take "00_1_networks-configured" --description="This is the virtual machine after configuring the NICs."
```
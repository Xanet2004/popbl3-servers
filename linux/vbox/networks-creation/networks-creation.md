This document describes the network topology used in this project and explains how to migrate
from a NAT-based topology (used in the original exercise) to an Internal Network (intnet)
topology, as required in POPBL3.

The goal is to reproduce the same logical network design while replacing NAT networks with
Internal Networks, allowing full control over addressing, routing and network services
(DHCP, HA and DDNS) without relying on VirtualBox NAT.
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




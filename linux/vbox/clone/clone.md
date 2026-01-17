After installing the services we need to clone the servers and clients to finally get all devices running.

# Clonning

```powershell title="clone"
# zaldua1zerb1 -> zaldua1zerb2
VBoxManage clonevm "zaldua1zerb1" --basefolder "C:\Users\xanet\VirtualBox VMs\Linux\xanet-zaldua" --groups "/popbl/linux/zaldua2" --name "zaldua1zerb2" --snapshot "02_1_service-installations" --register

# zaldua1zerb1 -> zaldua2zerb1
VBoxManage clonevm "zaldua1zerb1" --basefolder "C:\Users\xanet\VirtualBox VMs\Linux\xanet-zaldua" --groups "/popbl/linux/zaldua2" --name "zaldua2zerb1" --snapshot "02_1_service-installations" --register

# zaldua1zerb1 -> zaldua3zerb1
VBoxManage clonevm "zaldua1zerb1" --basefolder "C:\Users\xanet\VirtualBox VMs\Linux\xanet-zaldua" --groups "/popbl/linux/zaldua2" --name "zaldua3zerb1" --snapshot "02_1_service-installations" --register

# zaldua1bez1 -> zaldua2bez1
VBoxManage clonevm "zaldua1bez1" --basefolder "C:\Users\xanet\VirtualBox VMs\Linux\xanet-zaldua" --groups "/popbl/linux/zaldua2" --name "zaldua2bez1" --snapshot "01_0_installed" --register

# zaldua1bez1 -> zaldua3bez1
VBoxManage clonevm "zaldua1bez1" --basefolder "C:\Users\xanet\VirtualBox VMs\Linux\xanet-zaldua" --groups "/popbl/linux/zaldua2" --name "zaldua3bez1" --snapshot "01_0_installed" --register
```

# Change NIC configurations

```powershell title="Change NIC configurations"
VBoxManage modifyvm "zaldua1zerb2" --macaddress7 000102030201 --intnet6 zaldua1 --nic7 none --nic8 none
VBoxManage modifyvm "zaldua2zerb1" --macaddress7 000102030301 --intnet6 zaldua2 --nic7 none --nic8 none
VBoxManage modifyvm "zaldua3zerb1" --macaddress7 000102030401 --intnet6 zaldua2 --nic7 none --nic8 none

VBoxManage modifyvm "zaldua2bez1" --macaddress7 000102030302 --intnet6 zaldua2 --nic7 none --nic8 none
VBoxManage modifyvm "zaldua3bez1" --macaddress7 000102030402 --intnet6 zaldua3 --nic7 none --nic8 none
```

> Note
> NAT1 MAC addresses change automatically so we don't need to update them. This is because we used a random MAC for NAT.

```powershell title="NAT port forwardings"
VBoxManage modifyvm "zaldua1zerb2" --natpf1 delete sshz1z1
VBoxManage modifyvm "zaldua1zerb2" --natpf1 "sshz1z2,tcp,,2205,192.168.42.5,22"

VBoxManage modifyvm "zaldua2zerb1" --natpf1 delete sshz1z1
VBoxManage modifyvm "zaldua2zerb1" --natpf1 "sshz2z1,tcp,,2245,192.168.4.5,22"

VBoxManage modifyvm "zaldua3zerb1" --natpf1 delete sshz1z1
VBoxManage modifyvm "zaldua3zerb1" --natpf1 "sshz3z1,tcp,,2213,192.168.1.133,22"
```

# Change internal configurations

## Servers

### Hostname

```powershell title="server terminal"
hostname zaldua1zerb2
```

```powershell title="zaldua1zerb2 - /etc/hostname"
zaldua1zerb2 # Replace with the actual hostname
```

```powershell title="/etc/hosts"
127.0.0.1   localhost
127.0.0.1   zaldua2zerb1 # Replace with the actual hostname
```

### Static IP

```powershell title="zaldua2zerb1 - /etc/network/interfaces"
source /etc/network/interfaces.d/*

# The loopback network interface
auto lo
iface lo inet loopback

# The primary network interface
allow-hotplug enp0s3
iface enp0s3 inet dhcp
# This is an autoconfigured IPv6 interface
iface enp0s3 inet6 auto

allow-hotplug enp0s17
iface enp0s17 inet static
   address 192.168.42.5/23
```

> Note
> Repeat the configuration changes for each server
## Clients

### Hostname

```powershell title="server terminal"
hostname zaldua2zerb1
```

```powershell title="zaldua1zerb2 - /etc/hostname"
zaldua1zerb2 # Replace with the actual hostname
```

```powershell title="/etc/hosts"
127.0.0.1   localhost
127.0.0.1   zaldua2zerb1 # Replace with the actual hostname
```

> Note
> Repeat the configuration changes for each client
# Snapshot

```powershell title="terminal - snapshot"
VBoxManage snapshot "zaldua1zerb2" take "00_1_cloned" --description="Cloned from zaldua1zerb1 02_1_service-installations and changed basic configurations."
VBoxManage snapshot "zaldua2zerb1" take "00_1_cloned" --description="Cloned from zaldua1zerb1 02_1_service-installations and changed basic configurations."
VBoxManage snapshot "zaldua3zerb1" take "00_1_cloned" --description="Cloned from zaldua1zerb1 02_1_service-installations and changed basic configurations."

VBoxManage snapshot "zaldua2bez1" take "00_1_cloned" --description="Cloned from zaldua1bez1 01_0_installed and changed basic configurations."
VBoxManage snapshot "zaldua3bez1" take "00_1_cloned" --description="Cloned from zaldua1bez1 01_0_installed and changed basic configurations."
```

> Note
> Poweroff the machines first to avoid future conflicts.
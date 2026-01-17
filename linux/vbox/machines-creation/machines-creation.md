# Creating zaldua1zerb1

```powershell title="zaldua1zerb1"
# MACHINE CONF
# zaldua1zerb1
VBoxManage createvm --name "zaldua1zerb1" --ostype "Debian_64" --register --basefolder "C:\Users\xanet\VirtualBox VMs\Linux\xanet-zaldua" --groups "/popbl/linux/zaldua1"

# Basic conf
VBoxManage modifyvm "zaldua1zerb1" --memory 2048 --cpus 2 --vram 20 --firmware bios --mouse usbtablet --audio-enabled off


# NICs
VBoxManage modifyvm "zaldua1zerb1" --nic1 nat
#vboxmanage modifyvm "zaldua1zerb1" --nic6 intnet --mac-address6 000102030101 --intnet6 zaldua1
#vboxmanage modifyvm "zaldua1zerb1" --nic7 intnet --mac-address7 000102030102 --intnet7 zaldua2
#vboxmanage modifyvm "zaldua1zerb1" --nic8 intnet --mac-address8 000102030103 --intnet8 zaldua3


# ISO controller
VBoxManage storagectl "zaldua1zerb1" --name "IDE" --add ide --controller PIIX4

# HDD controller
VBoxManage storagectl "zaldua1zerb1" --name "SATA" --add sata --controller IntelAhci --portcount 1  # we could use a higher port number to attach more than one disks

# ISO attach
VBoxManage storageattach "zaldua1zerb1" --storagectl "IDE" --port 0 --device 0 --type dvddrive --medium "C:\Users\xanet\SynologyDrive\Study\Mondragon\3. urtea\AzpiegiturakEtaSistemak\ISO\debian-13.2.0-amd64-netinst.iso"

# Create medium
VBoxManage createmedium disk --filename "C:\Users\xanet\VirtualBox VMs\Linux\xanet-zaldua\popbl\linux\zaldua1\zaldua1zerb1\zaldua1zerb1-disk" --size "120000" --format VDI
# HDD attach
VBoxManage storageattach "zaldua1zerb1" --storagectl "SATA" --port 0 --device 0 --type hdd --medium "C:\Users\xanet\VirtualBox VMs\Linux\xanet-zaldua\popbl\linux\zaldua1\zaldua1zerb1\zaldua1zerb1-disk.vdi"


# PORT FORWARDING
#VBoxManage modifyvm "zaldua1zerb1" --natpf1 "ssh1z1,tcp,,2204,192.168.42.4,22"


# SNAPSHOT
VBoxManage snapshot "zaldua1zerb1" take "00_0_machine-created" --description="This is the virtual machine just after creating it."


# RUN
VBoxManage startvm "zaldua1zerb1"
```

> Note
> Allways use showvminfo to confirm changes on the machine

Now we need to decide what network interfaces will our machine use.
- For NAT networks, see: [NAT networks](/linux/vbox/networks-creation/nat-networks.md)
- For Internal networks. see: [Internal networks](/linux/vbox/networks-creation/internal-networks.md)
- To change from using NAT networks to using Internal networks, see: [Networks Creation | Migration from NAT Networks to Internal Networks](/linux/vbox/networks-creation/networks-creation.md)

# Creating zaldua1bez1

```powershell title="zaldua1bez1"
# MACHINE CONF
# zaldua1bez1
VBoxManage createvm --name "zaldua1bez1" --ostype "Debian_64" --register --basefolder "C:\Users\xanet\VirtualBox VMs\Linux\xanet-zaldua" --groups "/popbl/linux/zaldua1"

# Basic conf
VBoxManage modifyvm "zaldua1bez1" --memory 2048 --cpus 1 --vram 20 --firmware bios --mouse usbtablet --audio-enabled off


# NICs
VBoxManage modifyvm "zaldua1bez1" --nic1 nat
vboxmanage modifyvm "zaldua1bez1" --nic6 intnet --mac-address6 000102030202 --intnet6 zaldua1


# ISO controller
VBoxManage storagectl "zaldua1bez1" --name "IDE" --add ide --controller PIIX4

# HDD controller
VBoxManage storagectl "zaldua1bez1" --name "SATA" --add sata --controller IntelAhci --portcount 1  # we could use a higher port number to attach more than one disks

# ISO attach
VBoxManage storageattach "zaldua1bez1" --storagectl "IDE" --port 0 --device 0 --type dvddrive --medium "C:\Users\xanet\SynologyDrive\Study\Mondragon\3. urtea\AzpiegiturakEtaSistemak\ISO\debian-13.2.0-amd64-netinst.iso"

# Create medium
VBoxManage createmedium disk --filename "C:\Users\xanet\VirtualBox VMs\Linux\xanet-zaldua\popbl\linux\zaldua1\zaldua1bez1\zaldua1bez1-disk" --size "120000" --format VDI
# HDD attach
VBoxManage storageattach "zaldua1bez1" --storagectl "SATA" --port 0 --device 0 --type hdd --medium "C:\Users\xanet\VirtualBox VMs\Linux\xanet-zaldua\popbl\linux\zaldua1\zaldua1bez1\zaldua1bez1-disk.vdi"


# PORT FORWARDING
#VBoxManage modifyvm "zaldua1bez1" --natpf1 "ssh1z1,tcp,,2204,192.168.42.4,22"


# SNAPSHOT
VBoxManage snapshot "zaldua1bez1" take "00_0_machine-created" --description="This is the virtual machine just after creating it."


# RUN
VBoxManage startvm "zaldua1bez1"
```
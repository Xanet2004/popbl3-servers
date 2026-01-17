VirtualBox lets us create internal networks. We can modify or delete them when needed.

The main difference compared to NAT networks is that we don’t need to create internal networks. VirtualBox creates them automatically.

# Set networks to our machines

```powershell title="set network"
vboxmanage modifyvm "zaldua1zerb1" --nic6 intnet --mac-address6 000102030101 --intnet6 zaldua1
vboxmanage modifyvm "zaldua1zerb1" --nic7 intnet --mac-address7 000102030102 --intnet7 zaldua2
vboxmanage modifyvm "zaldua1zerb1" --nic8 intnet --mac-address8 000102030103 --intnet8 zaldua3
```

> Note
> Allways use showvminfo to confirm changes on the machine
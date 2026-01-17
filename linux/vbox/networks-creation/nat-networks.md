VirtualBox lets us create NAT networks. We can modify or delete them when needed.

# Creation

```powershell title="natnetwork creation"
# Create networks
VBoxManage natnetwork add --netname "zaldua1" --network "192.168.42.0/23"
VBoxManage natnetwork add --netname "zaldua2" --network "192.168.4.0/22"
VBoxManage natnetwork add --netname "zaldua3" --network "192.168.1.128/25"

# Start networks
VBoxManage natnetwork start --netname "zaldua1"
VBoxManage natnetwork start --netname "zaldua2"
VBoxManage natnetwork start --netname "zaldua3"
```

# Port forwardings

```powershell title="port forwardings creation"
# Port forwardings
VBoxManage natnetwork modify --netname "zaldua1" --port-forward-4 "ssh1z1:tcp:[127.0.0.1]:2204:[192.168.42.4]:22"
VBoxManage natnetwork modify --netname "zaldua1" --port-forward-4 "ssh1z2:tcp:[127.0.0.1]:2205:[192.168.42.5]:22"
VBoxManage natnetwork modify --netname "zaldua2" --port-forward-4 "ssh2z1:tcp:[127.0.0.1]:2245:[192.168.4.5]:22"
VBoxManage natnetwork modify --netname "zaldua3" --port-forward-4 "ssh3z1:tcp:[127.0.0.1]:2213:[192.168.1.133]:22"
```

# Set networks to our machines

```powershell title="set network"
vboxmanage modifyvm "zaldua1zerb1" --nic6 natnetwork --mac-address6 000102030101 --nat-network6 zaldua1
vboxmanage modifyvm "zaldua1zerb1" --nic7 natnetwork --mac-address7 000102030102 --nat-network7 zaldua2
vboxmanage modifyvm "zaldua1zerb1" --nic8 natnetwork --mac-address8 000102030103 --nat-network8 zaldua3
```

> Note
> Allways use showvminfo to confirm changes on the machine
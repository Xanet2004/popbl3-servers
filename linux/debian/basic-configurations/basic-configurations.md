# Static IP

```powershell title="zaldua1zerb1 - /etc/network/interfaces"
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
   address 192.168.42.4/23

allow-hotplug enp0s18
iface enp0s18 inet static
   address 192.168.4.4/22

allow-hotplug enp0s19
iface enp0s19 inet static
   address 192.168.1.132/23
```

```powershell title="zaldua1zerb1 - reset networking"
systemctl restart networking
ip a
```

# SSH

```powershell title="terminal - ssh connect"
ssh -p 2204 user@localhost
```

# Snapshot

```powershell title="terminal - snapshot"
VBoxManage snapshot "zaldua1zerb1" take "02_0_basic-configuration" --description="Configured NICs."
```

> Note
> Poweroff the machines first to avoid future conflicts.
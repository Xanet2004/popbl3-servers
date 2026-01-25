# Disable current isc-dhcp-server

We are using KEA DHCP instead of ISC DHCP, so we need to disable it or remove it. We won't disable DNS though. KEA DDNS works with bind9.

```powershell title="disable isc-dhcp-server"
systemctl stop isc-dhcp-server
systemctl disable isc-dhcp-server
```

# Install KEA

```powershell title="KEA installation"
apt update
apt install -y kea-dhcp4-server kea-ctrl-agent kea-dhcp-ddns-server kea-admin
```

![[/linux/debian/services/kea/img/kea-01.png]]

```powershell title="check KEA status"
systemctl status kea-ctrl-agent.service
```

# Snapshot

```powershell title="snapshot"
VBoxManage snapshot "zaldua1zerb1" take "05_0_kea_installation" --description="This is the virtual machine after installing KEA packages."
VBoxManage snapshot "zaldua1zerb2" take "05_0_kea_installation" --description="This is the virtual machine after installing KEA packages."
VBoxManage snapshot "zaldua2zerb1" take "05_0_kea_installation" --description="This is the virtual machine after installing KEA packages."
```

> Note
> Poweroff the machines first to avoid future conflicts.
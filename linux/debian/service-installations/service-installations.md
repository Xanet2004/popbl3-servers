Instead of zaldua3zerb1, all the other 3 servers will be using both DHCP and DNS.
So we are installing it on zaldua1zerb1 and then cloning it to create the other servers.

> Note
> We will remove DHCP from zaldua3zerb1 later.

# Installation commands

```powershell title="install DHCP and DNS"
apt update
apt-get install isc-dhcp-server bind9 bind9-dnsutils
```

```powershell title="check DHCP and DNS services"
systemctl status isc-dhcp-server.service
systemctl status bind9
```

> Note
> DHCP server should throw an error but DNS should be running.
# Snapshot

```powershell title="snapshot"
VBoxManage snapshot "zaldua1zerb1" take "02_1_service-installations" --description="This is the virtual machine after installing DHCP and DNS."
```

> Note
> Poweroff the machines first to avoid future conflicts.
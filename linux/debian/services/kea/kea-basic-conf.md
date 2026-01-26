We will start without configuring HA. This is because I want to test a basic configuration before we start with more complicated configurations.

So, we are configuring each server as the primary server on each subnet. But we won't configure zaldua1zerb2 yet (nor zaldua3zerb1, but that server will never have DHCP).

## Basic structure:
- zaldua1zerb1:
	- zaldua1 dhcp
	- zaldua3 dhcp
- zaldua2zerb1:
	- zaldua2 dhcp
## zaldua1zerb1

### Backup

```powershell title="KEA backup"
cp /etc/kea/kea-dhcp4.conf /etc/kea/kea-dhcp4.conf.bak
```
### DHCP configuration

```json title="zaldua1zerb1 - /etc/kea/kea-dhcp4.conf"
{
  "Dhcp4": {
    "interfaces-config": {
      "interfaces": [ "enp0s17", "enp0s19" ]
    },

    "lease-database": {
      "type": "memfile",
      "persist": true,
      "name": "/var/lib/kea/kea-leases4.csv"
    },

    "authoritative": true,

    "renew-timer": 30,
    "rebind-timer": 45,
    "valid-lifetime": 60,
    "max-valid-lifetime": 720,

    "subnet4": [
      {
        "id": 1,
        "subnet": "192.168.42.0/23",
        "pools": [
          { "pool": "192.168.42.100-192.168.42.200" }
        ],
        "option-data": [
          { "name": "routers", "data": "192.168.42.2" },
          { "name": "broadcast-address", "data": "192.168.43.255" },
          { "name": "domain-name", "data": "zalduabat.eus" },
          { "name": "domain-name-servers", "data": "192.168.42.4, 192.168.42.5" }
        ],
        "reservations": [
          {
            "hw-address": "00:01:02:03:02:02",
            "ip-address": "192.168.42.10",
            "hostname": "zaldua1bez1"
          }
        ]
      },

      {
        "id": 2,
        "subnet": "192.168.1.128/25",
        "pools": [
          { "pool": "192.168.1.160-192.168.1.200" }
        ],
        "option-data": [
          { "name": "routers", "data": "192.168.1.129" },
          { "name": "broadcast-address", "data": "192.168.1.255" },
          { "name": "domain-name", "data": "zalduahiru.eus" },
          { "name": "domain-name-servers", "data": "192.168.1.132, 192.168.1.133" }
        ],
        "reservations": [
          {
            "hw-address": "00:01:02:03:04:02",
            "ip-address": "192.168.1.140",
            "hostname": "zaldua3bez1"
          }
        ]
      }
    ]
  }
}
```

## zaldua2zerb1

### Backup

```powershell title="KEA backup"
cp /etc/kea/kea-dhcp4.conf /etc/kea/kea-dhcp4.conf.bak
```
### DHCP configuration

```json title="zaldua2zerb1 - /etc/kea/kea-dhcp4.conf"
{
  "Dhcp4": {
    "interfaces-config": {
      "interfaces": [ "enp0s17" ]
    },

    "lease-database": {
      "type": "memfile",
      "persist": true,
      "name": "/var/lib/kea/kea-leases4.csv"
    },

    "authoritative": true,

    "renew-timer": 30,
    "rebind-timer": 45,
    "valid-lifetime": 60,
    "max-valid-lifetime": 720,

    "subnet4": [
      {
        "id": 10,
        "subnet": "192.168.4.0/22",
        "pools": [
          { "pool": "192.168.4.100-192.168.4.200" }
        ],
        "option-data": [
          { "name": "routers", "data": "192.168.4.1" },
          { "name": "broadcast-address", "data": "192.168.7.255" },
          { "name": "domain-name", "data": "zalduabi.eus" },
          { "name": "domain-name-servers", "data": "192.168.4.5, 192.168.4.4" }
        ],
        "reservations": [
          {
            "hw-address": "00:01:02:03:03:02",
            "ip-address": "192.168.4.10",
            "hostname": "zaldua2bez1"
          }
        ]
      }
    ]
  }
}
```

## zaldua1zerb2

As we said, we are disabling temporarily this server. Because it would be giving problems to the main server. We still are not configuring HA (failover for KEA) after all.
Or we can just poweroff the second server.

```powershell title="zaldua1zerb2 - disable KEA"
systemctl stop kea-dhcp4-server
systemctl disable kea-dhcp4-server
```

# Restart service

```powershell title="zaldua1zerb1 + zaldua2zerb1 - restart and status"
systemctl restart kea-dhcp4-server
```

```powershell title="check journalctl"
systemctl status kea-dhcp4-server
```

# Check logs

```powershell title="check logs"
journalctl -u kea-dhcp4-server -f
```

zaldua1zerb1:
![[/linux/debian/services/kea/img/kea-02.png]]
> Note
> zaldua1bez1 + zaldua3bez1 leased correctly

zaldua2zerb1:
![[/linux/debian/services/kea/img/kea-03.png]]
> Note
> zaldua2bez1 leased correctly

# Snapshot

```powershell title="snapshot"
VBoxManage snapshot "zaldua1zerb1" take "05_1_kea_basic_configuration" --description="This is the virtual machine after configuring a basic KEA DHCP."
VBoxManage snapshot "zaldua2zerb1" take "05_1_kea_basic_configuration" --description="This is the virtual machine after configuring a basic KEA DHCP."
```

> Note
> Poweroff the machines first to avoid future conflicts.
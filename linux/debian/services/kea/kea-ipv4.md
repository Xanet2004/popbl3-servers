# Tasks
- zaldua1zerb1 - Primary and Secondary
	- zaldua1zerb2 - Secondary
	- zaldua2zerb1 - Primary
- zaldua1zerb2 - Secondary
	- zaldua1zerb1 - Primary
- zaldua2zerb1 - Primary
	- zaldua1zerb1 - Secondary
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
      "interfaces": [ "enp0s17", "enp0s18", "enp0s19" ]
    },

    "control-socket": {
      "socket-type": "unix",
      "socket-name": "/run/kea/kea4-ctrl-socket"
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

    "hooks-libraries": [
      { "library": "/usr/lib/x86_64-linux-gnu/kea/hooks/libdhcp_lease_cmds.so" },
      {
        "library": "/usr/lib/x86_64-linux-gnu/kea/hooks/libdhcp_ha.so",
        "parameters": {
          "high-availability": [

            {
              "this-server-name": "zaldua1zerb1-zaldua1",
              "mode": "hot-standby",
              "heartbeat-delay": 10000,
              "max-response-delay": 10000,

              "peers": [
                {
                  "name": "zaldua1zerb1-zaldua1",
                  "url": "http://192.168.42.4:8001/",
                  "role": "primary",
                  "auto-failover": true
                },
                {
                  "name": "zaldua1zerb2-zaldua1",
                  "url": "http://192.168.42.5:8001/",
                  "role": "standby",
                  "auto-failover": true
                }
              ]
            },

            {
              "this-server-name": "zaldua1zerb1-zaldua2",
              "mode": "hot-standby",
              "heartbeat-delay": 10000,
              "max-response-delay": 10000,

              "peers": [
                {
                  "name": "zaldua2zerb1-zaldua2",
                  "url": "http://192.168.4.5:8002/",
                  "role": "primary",
                  "auto-failover": true
                },
                {
                  "name": "zaldua1zerb1-zaldua2",
                  "url": "http://192.168.4.4:8002/",
                  "role": "standby",
                  "auto-failover": true
                }
              ]
            }

          ]
        }
      }
    ],

    "subnet4": [
      {
        "id": 1,
        "subnet": "192.168.42.0/23",
        "user-context": { "ha-server-name": "zaldua1zerb1-zaldua1" },

        "pools": [ { "pool": "192.168.42.100-192.168.42.200" } ],
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
        "subnet": "192.168.4.0/22",
        "user-context": { "ha-server-name": "zaldua1zerb1-zaldua2" },

        "pools": [ { "pool": "192.168.4.100-192.168.4.200" } ],
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
      },

      {
        "id": 3,
        "subnet": "192.168.1.128/25",
        "user-context": { "ha-server-name": "zaldua1zerb1-zaldua1" },
        "pools": [ { "pool": "192.168.1.160-192.168.1.200" } ],
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

> 	Note
> 	- **Listens on** `enp0s17`, `enp0s18`, `enp0s19` (three internal networks).
> 	- Exposes a **local control socket** at `/run/kea/kea4-ctrl-socket` (used by Control Agent / HA to send commands).
> 	- Stores leases in a **memfile DB** and persists them to `/var/lib/kea/kea-leases4.csv`.
> 	- Runs as **authoritative DHCP**, with timers: `T1=30s`, `T2=45s`, `valid=60s`, `max-valid=720s`.
> 	- Loads two **hooks**:
> 	    - `lease_cmds`: enables lease management commands.
> 	    - `ha`: enables **High Availability (HA)** with **hot-standby** mode.
> 	- Defines **two HA relationships** (same Kea instance participates in both):
> 	    - **zaldua1 pair (port 8001):** `zaldua1zerb1` is **primary** ↔ `zaldua1zerb2` is **standby** over `192.168.42.0/23`.
> 	    - **zaldua2 pair (port 8002):** `zaldua2zerb1` is **primary** ↔ `zaldua1zerb1` is **standby** over `192.168.4.0/22`.
> 	- Configures **three IPv4 subnets**:
> 	    - **Subnet ID 1:** `192.168.42.0/23` pool `42.100-42.200`, options (router/DNS/domain/broadcast), reservation `zaldua1bez1 → 192.168.42.10`, bound to HA relationship `zaldua1zerb1-zaldua1`.
> 	    - **Subnet ID 2:** `192.168.4.0/22` pool `4.100-4.200`, reservation `zaldua2bez1 → 192.168.4.10`, bound to HA relationship `zaldua1zerb1-zaldua2`.>         
> 	    - **Subnet ID 3:** `192.168.1.128/25` pool `1.160-1.200`, reservation `zaldua3bez1 → 192.168.1.140` (no separate HA pair in your topology).

## zaldua1zerb2

### Backup

```powershell title="KEA backup"
cp /etc/kea/kea-dhcp4.conf /etc/kea/kea-dhcp4.conf.bak
```
### DHCP configuration

```json title="zaldua1zerb2 - /etc/kea/kea-dhcp4.conf"
{
  "Dhcp4": {
    "interfaces-config": { "interfaces": [ "enp0s17" ] },

    "control-socket": {
      "socket-type": "unix",
      "socket-name": "/run/kea/kea4-ctrl-socket"
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

    "hooks-libraries": [
      { "library": "/usr/lib/x86_64-linux-gnu/kea/hooks/libdhcp_lease_cmds.so" },
      {
        "library": "/usr/lib/x86_64-linux-gnu/kea/hooks/libdhcp_ha.so",
        "parameters": {
          "high-availability": [
            {
              "this-server-name": "zaldua1zerb2-zaldua1",
              "mode": "hot-standby",
              "heartbeat-delay": 10000,
              "max-response-delay": 10000,
              "peers": [
                {
                  "name": "zaldua1zerb1-zaldua1",
                  "url": "http://192.168.42.4:8001/",
                  "role": "primary",
                  "auto-failover": true
                },
                {
                  "name": "zaldua1zerb2-zaldua1",
                  "url": "http://192.168.42.5:8001/",
                  "role": "standby",
                  "auto-failover": true
                }
              ]
            }
          ]
        }
      }
    ],

    "subnet4": [
      {
        "id": 1,
        "subnet": "192.168.42.0/23",
        "user-context": { "ha-server-name": "zaldua1zerb2-zaldua1" },
        "pools": [ { "pool": "192.168.42.100-192.168.42.200" } ],
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
		"id": 3,
		 "subnet": "192.168.1.128/25",
		 "user-context": { "ha-server-name": "zaldua1zerb2-zaldua1" },
		 "pools": [],
		 "option-data": []
	  }
    ]
  }
}
```

> Note
> - **Listens only on** `enp0s17` (so it only serves the _zaldua1_ network interface on this VM).
> - Uses the **same local control socket** `/run/kea/kea4-ctrl-socket` for management/HA commands.
> - Lease DB is **memfile + persistent** at `/var/lib/kea/kea-leases4.csv`.
> - Runs as **authoritative**, with `T1=30s`, `T2=45s`, `valid=60s`, `max-valid=720s`.
> - Loads hooks:
>     - `lease_cmds` (lease commands)
>     - `ha` in **hot-standby** mode for the **zaldua1 pair**:
>         - Primary: `zaldua1zerb1` at `http://192.168.42.4:8001/`
>         - Standby (this server): `zaldua1zerb2` at `http://192.168.42.5:8001/`
> - Defines subnet(s):
>     - **Subnet ID 1:** `192.168.42.0/23` pool `42.100-42.200`, options (router/DNS/domain/broadcast), reservation `zaldua1bez1 → 192.168.42.10`, tied to HA relationship `zaldua1zerb2-zaldua1`.
>     - **Subnet ID 3 placeholder:** `192.168.1.128/25` has **no pools and no options**, so even if the subnet is declared, this server **won’t actually hand out leases there** (and it’s also not listening on the interface for that network anyway).

## zaldua2zerb1

### Backup

```powershell title="KEA backup"
cp /etc/kea/kea-dhcp4.conf /etc/kea/kea-dhcp4.conf.bak
```
### DHCP configuration

```json title="zaldua2zerb1 - /etc/kea/kea-dhcp4.conf"
{
  "Dhcp4": {
    "interfaces-config": { "interfaces": [ "enp0s17" ] },

    "control-socket": {
      "socket-type": "unix",
      "socket-name": "/run/kea/kea4-ctrl-socket"
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

    "hooks-libraries": [
      { "library": "/usr/lib/x86_64-linux-gnu/kea/hooks/libdhcp_lease_cmds.so" },
      {
        "library": "/usr/lib/x86_64-linux-gnu/kea/hooks/libdhcp_ha.so",
        "parameters": {
          "high-availability": [
            {
              "this-server-name": "zaldua2zerb1-zaldua2",
              "mode": "hot-standby",
              "heartbeat-delay": 10000,
              "max-response-delay": 10000,
              "peers": [
                {
                  "name": "zaldua2zerb1-zaldua2",
                  "url": "http://192.168.4.5:8002/",
                  "role": "primary",
                  "auto-failover": true
                },
                {
                  "name": "zaldua1zerb1-zaldua2",
                  "url": "http://192.168.4.4:8002/",
                  "role": "standby",
                  "auto-failover": true
                }
              ]
            }
          ]
        }
      }
    ],

    "subnet4": [
      {
        "id": 2,
        "subnet": "192.168.4.0/22",
        "user-context": { "ha-server-name": "zaldua2zerb1-zaldua2" },

        "pools": [ { "pool": "192.168.4.100-192.168.4.200" } ],
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

> Note
> - **Listens only on** `enp0s17` (serves the _zaldua2_ network on this VM).
> - Uses control socket `/run/kea/kea4-ctrl-socket` for management/HA.
> - Lease DB is **memfile + persistent** at `/var/lib/kea/kea-leases4.csv`.
> - Runs as **authoritative**, with `T1=30s`, `T2=45s`, `valid=60s`, `max-valid=720s`.
> - Loads hooks:
>     - `lease_cmds`
>     - `ha` in **hot-standby** mode for the **zaldua2 pair**:
>         - Primary (this server): `zaldua2zerb1` at `http://192.168.4.5:8002/`
>         - Standby: `zaldua1zerb1` at `http://192.168.4.4:8002/`
> - Defines **Subnet ID 2:** `192.168.4.0/22` pool `4.100-4.200`, options (router/DNS/domain/broadcast), reservation `zaldua2bez1 → 192.168.4.10`, tied to HA relationship `zaldua2zerb1-zaldua2`.

# Restart service

```powershell title="restart KEA"
systemctl restart kea-dhcp4-server
systemctl status kea-dhcp4-server
```

# High Availability

Now that we configured our KEA DHCP as the activity asks, we need to configure High Availability for a failover-like system.
This configuration shouldn't work by itself, so we are doing the snapshot after configuring HA. [KEA High Availability](/linux/debian/services/kea/kea-ha.md)
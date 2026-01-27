# Tasks
- **zaldua1zerb1**: 
	- **PRIMARY** of `zalduabat.eus` and `zalduahiru.eus`, 
	- **SECONDARY** of `zalduabi.eus`
- **zaldua1zerb2**: 
	- **SECONDARY** of `zalduabat.eus`
- **zaldua2zerb1**: 
	- **PRIMARY** of `zalduabi.eus`
- **zaldua3zerb1**: 
	- **SECONDARY** of `zalduahiru.eus`

# TSIG key

A DDNS protocol exchange can be conducted with or without a transaction signature, or TSIG. This configuration section allows the administrator to define the set of TSIG keys that may be used in such exchanges.

> Note
> This is **optional**, but very recommended for security reasons.

## zaldua1zerb1

We are creating the key on a master server and then copying it on the other servers too.

```powershell title="zaldua1zerb1 - create TSIG key"
mkdir -p /etc/bind/keys

tsig-keygen -a HMAC-SHA256 kea-ddns-key > /etc/bind/keys/kea-ddns-key.conf

chown root:bind /etc/bind/keys/kea-ddns-key.conf
chmod 640 /etc/bind/keys/kea-ddns-key.conf
chmod 750 /etc/bind/keys
```

> Note
> **BIND (`named`) runs as user `bind`** → needs read access

```powershell title="zaldua1zerb1 - /etc/bind/keys/kea-ddns-key.conf"
key "kea-ddns-key" {
  algorithm hmac-sha256;
  secret "+Xljx6box6nh3Ta1KnCO6/k9KoBf+eSvskUssCeyYp0=";
};
```

## Other DNS servers

Copy the same key to the other servers.

```powershell title="zaldua1zerb1 - create TSIG key"
mkdir -p /etc/bind/keys
```

```powershell title="dns servers - /etc/bind/keys/kea-ddns-key.conf"
key "kea-ddns-key" {
  algorithm hmac-sha256;
  secret "+Xljx6box6nh3Ta1KnCO6/k9KoBf+eSvskUssCeyYp0=";
};
```

```powershell title="other servers - TSIG permissions"
chown root:bind /etc/bind/keys/kea-ddns-key.conf
chmod 640 /etc/bind/keys/kea-ddns-key.conf
chmod 750 /etc/bind/keys
```

> Note
> **BIND (`named`) runs as user `bind`** → needs read access

## Add TSIG on DNS masters

Include the key and add the key on every master zones.
### zaldua1zerb1

```powershell title="zaldua1zerb1 and zaldua2zerb1 - /etc/bind/named.conf.local"
/ =====================
// zaldua1zerb1
// /etc/bind/named.conf.local
// =====================

include "/etc/bind/keys/kea-ddns-key.conf";

// PRIMARY (MASTER) - zalduabat.eus (zaldua1)
zone "zalduabat.eus" IN {
    type master;
    file "/etc/bind/zones/db.zalduabat.eus";
    allow-transfer { 192.168.42.5; };
    also-notify    { 192.168.42.5; };
    notify yes;
    allow-update { key "kea-ddns-key"; };
};

zone "42.168.192.in-addr.arpa" IN {
    type master;
    file "/etc/bind/zones/db.192.168.42";
    allow-transfer { 192.168.42.5; };
    also-notify    { 192.168.42.5; };
    notify yes;
    allow-update { key "kea-ddns-key"; };
};

zone "43.168.192.in-addr.arpa" IN {
    type master;
    file "/etc/bind/zones/db.192.168.43";
    allow-transfer { 192.168.42.5; };
    also-notify    { 192.168.42.5; };
    notify yes;
    allow-update { key "kea-ddns-key"; };
};

// PRIMARY (MASTER) - zalduahiru.eus (zaldua3)
zone "zalduahiru.eus" IN {
    type master;
    file "/etc/bind/zones/db.zalduahiru.eus";
    allow-transfer { 192.168.1.133; };
    also-notify    { 192.168.1.133; };
    notify yes;
    allow-update { key "kea-ddns-key"; };
};

zone "1.168.192.in-addr.arpa" IN {
    type master;
    file "/etc/bind/zones/db.192.168.1";
    allow-transfer { 192.168.1.133; };
    also-notify    { 192.168.1.133; };
    notify yes;
    allow-update { key "kea-ddns-key"; };
};

// SECONDARY (SLAVE) - zalduabi.eus (zaldua2)  (MASTER: zaldua2zerb1)
zone "zalduabi.eus" IN {
    type slave;
    masters { 192.168.4.5; }; // zaldua2zerb1
    file "/var/cache/bind/slave.db.zalduabi.eus";
};

zone "4.168.192.in-addr.arpa" IN {
    type slave;
    masters { 192.168.4.5; };
    file "/var/cache/bind/slave.db.192.168.4";
};

zone "5.168.192.in-addr.arpa" IN {
    type slave;
    masters { 192.168.4.5; };
    file "/var/cache/bind/slave.db.192.168.5";
};

zone "6.168.192.in-addr.arpa" IN {
    type slave;
    masters { 192.168.4.5; };
    file "/var/cache/bind/slave.db.192.168.6";
};

zone "7.168.192.in-addr.arpa" IN {
    type slave;
    masters { 192.168.4.5; };
    file "/var/cache/bind/slave.db.192.168.7";
};
```

> Note
> zaldua1zerb1 shouldn't allow-update on zaldua2 zone, this is because that zone should work as a slave. We are allowing this only on master zones.

### zaldua2zerb1

```powershell title="zaldua1zerb1 and zaldua2zerb1 - /etc/bind/named.conf.local"
// =====================
// zaldua2zerb1
// /etc/bind/named.conf.local
// =====================

include "/etc/bind/keys/kea-ddns-key.conf";

// PRIMARY (MASTER) - zalduabi.eus
zone "zalduabi.eus" IN {
    type master;
    file "/etc/bind/zones/db.zalduabi.eus";
    allow-transfer { 192.168.4.4; };
    also-notify    { 192.168.4.4; };
    notify yes;
    allow-update { key "kea-ddns-key"; };
};

// Reverse 192.168.4.0/22 => /24s 4,5,6,7
zone "4.168.192.in-addr.arpa" IN {
    type master;
    file "/etc/bind/zones/db.192.168.4";
    allow-transfer { 192.168.4.4; };
    also-notify    { 192.168.4.4; };
    notify yes;
    allow-update { key "kea-ddns-key"; };
};

zone "5.168.192.in-addr.arpa" IN {
    type master;
    file "/etc/bind/zones/db.192.168.5";
    allow-transfer { 192.168.4.4; };
    also-notify    { 192.168.4.4; };
    notify yes;
    allow-update { key "kea-ddns-key"; };
};

zone "6.168.192.in-addr.arpa" IN {
    type master;
    file "/etc/bind/zones/db.192.168.6";
    allow-transfer { 192.168.4.4; };
    also-notify    { 192.168.4.4; };
    notify yes;
    allow-update { key "kea-ddns-key"; };
};

zone "7.168.192.in-addr.arpa" IN {
    type master;
    file "/etc/bind/zones/db.192.168.7";
    allow-transfer { 192.168.4.4; };
    also-notify    { 192.168.4.4; };
    notify yes;
    allow-update { key "kea-ddns-key"; };
};
```

# Check DNS status

```powershell title="dns status"
systemctl restart bind9
systemctl status bind9
```

# DDNS Configuration
## zaldua1zerb1

```powershell title="KEA ddns backup"
cp /etc/kea/kea-dhcp-ddns.conf /etc/kea/kea-dhcp-ddns.conf.bak
```

```json title="zaldua1zerb1 - /etc/kea/kea-dhcp-ddns.conf"
{
  "DhcpDdns": {
    "ip-address": "127.0.0.1",
    "port": 53001,
    "ncr-protocol": "UDP",
    "ncr-format": "JSON",

    "tsig-keys": [
      {
        "name": "kea-ddns-key",
        "algorithm": "HMAC-SHA256",
        "secret": "+Xljx6box6nh3Ta1KnCO6/k9KoBf+eSvskUssCeyYp0="
      }
    ],

    "forward-ddns": {
      "ddns-domains": [
        {
          "name": "zalduabat.eus.",
          "key-name": "kea-ddns-key",
          "dns-servers": [ { "ip-address": "127.0.0.1", "port": 53 } ]
        },
        {
          "name": "zalduahiru.eus.",
          "key-name": "kea-ddns-key",
          "dns-servers": [ { "ip-address": "127.0.0.1", "port": 53 } ]
        },

        {
          "name": "zalduabi.eus.",
          "key-name": "kea-ddns-key",
          "dns-servers": [ { "ip-address": "192.168.4.5", "port": 53 } ]
        }
      ]
    },

    "reverse-ddns": {
      "ddns-domains": [
        {
          "name": "42.168.192.in-addr.arpa.",
          "key-name": "kea-ddns-key",
          "dns-servers": [ { "ip-address": "127.0.0.1", "port": 53 } ]
        },
        {
          "name": "43.168.192.in-addr.arpa.",
          "key-name": "kea-ddns-key",
          "dns-servers": [ { "ip-address": "127.0.0.1", "port": 53 } ]
        },
        {
          "name": "1.168.192.in-addr.arpa.",
          "key-name": "kea-ddns-key",
          "dns-servers": [ { "ip-address": "127.0.0.1", "port": 53 } ]
        },

        {
          "name": "4.168.192.in-addr.arpa.",
          "key-name": "kea-ddns-key",
          "dns-servers": [ { "ip-address": "192.168.4.5", "port": 53 } ]
        },
        {
          "name": "5.168.192.in-addr.arpa.",
          "key-name": "kea-ddns-key",
          "dns-servers": [ { "ip-address": "192.168.4.5", "port": 53 } ]
        },
        {
          "name": "6.168.192.in-addr.arpa.",
          "key-name": "kea-ddns-key",
          "dns-servers": [ { "ip-address": "192.168.4.5", "port": 53 } ]
        },
        {
          "name": "7.168.192.in-addr.arpa.",
          "key-name": "kea-ddns-key",
          "dns-servers": [ { "ip-address": "192.168.4.5", "port": 53 } ]
        }
      ]
    }
  }
}
```

> Note
> - This server runs **Kea DHCP-DDNS (D2)** locally on `127.0.0.1:53001` to process Name Change Requests (NCR) from Kea DHCP4.
> - D2 performs **signed DNS updates (TSIG)** against the **DNS masters** of each zone:
>     - `zalduabat.eus` and its reverse zones are updated locally (`127.0.0.1`) because BIND is master here.
>     - `zalduahiru.eus` and its reverse zone are updated locally (`127.0.0.1`) because BIND is master here.
>     - `zalduabi.eus` (where this server is a DNS slave) is updated on the real master `192.168.4.5`.
> - Slaves will receive the updated records automatically via zone transfer.

## zaldua1zerb2

```powershell title="KEA ddns backup"
cp /etc/kea/kea-dhcp-ddns.conf /etc/kea/kea-dhcp-ddns.conf.bak
```

```json title="zaldua1zerb2 - /etc/kea/kea-dhcp-ddns.conf"
{
  "DhcpDdns": {
    "ip-address": "127.0.0.1",
    "port": 53001,
    "ncr-protocol": "UDP",
    "ncr-format": "JSON",

    "tsig-keys": [
      {
        "name": "kea-ddns-key",
        "algorithm": "HMAC-SHA256",
        "secret": "+Xljx6box6nh3Ta1KnCO6/k9KoBf+eSvskUssCeyYp0="
      }
    ],

    "forward-ddns": {
      "ddns-domains": [
        {
          "name": "zalduabat.eus.",
          "key-name": "kea-ddns-key",
          "dns-servers": [ { "ip-address": "192.168.42.4", "port": 53 } ]
        }
      ]
    },

    "reverse-ddns": {
      "ddns-domains": [
        {
          "name": "42.168.192.in-addr.arpa.",
          "key-name": "kea-ddns-key",
          "dns-servers": [ { "ip-address": "192.168.42.4", "port": 53 } ]
        },
        {
          "name": "43.168.192.in-addr.arpa.",
          "key-name": "kea-ddns-key",
          "dns-servers": [ { "ip-address": "192.168.42.4", "port": 53 } ]
        }
      ]
    }
  }
}
```

> Note
> - This server runs **Kea DHCP-DDNS (D2)** locally on `127.0.0.1:53001` and receives NCR messages from Kea DHCP4.
> - Because BIND is **slave** for `zalduabat.eus` on this VM, D2 sends dynamic updates to the **master DNS** at `192.168.42.4` (TSIG-signed).
> - DNS replication to this server is handled by standard zone transfers from the master.

## zaldua2zerb1

```powershell title="KEA backup"
cp /etc/kea/kea-dhcp-ddns.conf /etc/kea/kea-dhcp-ddns.conf.bak
```

```json title="zaldua2zerb1 - /etc/kea/kea-dhcp-ddns.conf"
{
  "DhcpDdns": {
    "ip-address": "127.0.0.1",
    "port": 53001,
    "ncr-protocol": "UDP",
    "ncr-format": "JSON",

    "tsig-keys": [
      {
        "name": "kea-ddns-key",
        "algorithm": "HMAC-SHA256",
        "secret": "+Xljx6box6nh3Ta1KnCO6/k9KoBf+eSvskUssCeyYp0="
      }
    ],

    "forward-ddns": {
      "ddns-domains": [
        {
          "name": "zalduabi.eus.",
          "key-name": "kea-ddns-key",
          "dns-servers": [ { "ip-address": "127.0.0.1", "port": 53 } ]
        }
      ]
    },

    "reverse-ddns": {
      "ddns-domains": [
        { "name": "4.168.192.in-addr.arpa.", "key-name": "kea-ddns-key", "dns-servers": [ { "ip-address": "127.0.0.1", "port": 53 } ] },
        { "name": "5.168.192.in-addr.arpa.", "key-name": "kea-ddns-key", "dns-servers": [ { "ip-address": "127.0.0.1", "port": 53 } ] },
        { "name": "6.168.192.in-addr.arpa.", "key-name": "kea-ddns-key", "dns-servers": [ { "ip-address": "127.0.0.1", "port": 53 } ] },
        { "name": "7.168.192.in-addr.arpa.", "key-name": "kea-ddns-key", "dns-servers": [ { "ip-address": "127.0.0.1", "port": 53 } ] }
      ]
    }
  }
}
```

> Note
> - This server runs **Kea DHCP-DDNS (D2)** locally on `127.0.0.1:53001` to handle NCR from Kea DHCP4.
> - It updates `zalduabi.eus` and its reverse zones locally (`127.0.0.1:53`) because this server is the **DNS master** for that domain.
> - The secondary DNS (`192.168.4.4`) receives changes via zone transfer from the master.

# Update KEA DHCP4

## zaldua1zerb1

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
    
    "dhcp-ddns": {
	  "enable-updates": true,
	  "server-ip": "127.0.0.1",
	  "server-port": 53001,
	  "ncr-protocol": "UDP",
	  "ncr-format": "JSON"
	},
	
	"ddns-override-client-update": true,
	"ddns-override-no-update": true,
	"ddns-replace-client-name": "when-present",

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
        
        "ddns-qualifying-suffix": "zalduabat.eus.",

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
        
        "ddns-qualifying-suffix": "zalduabi.eus.",

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
        
		"ddns-qualifying-suffix": "zalduahiru.eus.",
        
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

> Note
> - Subnet 3 includes `ha-server-name` because the HA hook requires an HA relationship selector for any subnet served by this Kea instance.
> - This does **not** mean subnet 3 has a functional failover partner: the standby server is not connected to this network (no interface) and does not have an active pool for this subnet.
> - Therefore, only `zaldua1zerb1` can serve DHCP and update DNS for `zalduahiru.eus` in this subnet.

## zaldua1zerb2

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
    
    "dhcp-ddns": {
	  "enable-updates": true,
	  "server-ip": "127.0.0.1",
	  "server-port": 53001,
	  "ncr-protocol": "UDP",
	  "ncr-format": "JSON"
	},
	
	"ddns-override-client-update": true,
	"ddns-override-no-update": true,
	"ddns-replace-client-name": "when-present",


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
        
		"ddns-qualifying-suffix": "zalduabat.eus.",
		"ddns-generated-prefix": "dhcp-",
        
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
		 
		"ddns-qualifying-suffix": "zalduahiru.eus.",
		 
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
    
    "dhcp-ddns": {
	  "enable-updates": true,
	  "server-ip": "127.0.0.1",
	  "server-port": 53001,
	  "ncr-protocol": "UDP",
	  "ncr-format": "JSON"
	},
	
	"ddns-override-client-update": true,
	"ddns-override-no-update": true,
	"ddns-replace-client-name": "when-present",

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
        
		"ddns-qualifying-suffix": "zalduabi.eus.",

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
systemctl restart kea-ctrl-agent
systemctl restart kea-dhcp4-server
systemctl restart kea-dhcp-ddns-server
```

```powershell title="restart KEA"
systemctl status kea-ctrl-agent
systemctl status kea-dhcp4-server
systemctl status kea-dhcp-ddns-server
```

# Snapshot

```powershell title="snapshot"
VBoxManage snapshot "zaldua1zerb1" take "05_3_kea_ddns_configuration" --description="This is the virtual machine after configuring a KEA DHCP with HA and DDNS."
VBoxManage snapshot "zaldua1zerb2" take "05_3_kea_ddns_configuration" --description="This is the virtual machine after configuring a KEA DHCP with HA and DDNS."
VBoxManage snapshot "zaldua2zerb1" take "05_3_kea_ddns_configuration" --description="This is the virtual machine after configuring a KEA DHCP with HA and DDNS."
```

> Note
> Poweroff the machines first to avoid future conflicts.
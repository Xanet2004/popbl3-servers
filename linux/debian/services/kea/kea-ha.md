This is the KEA High Availability configuration for the activity.

# KEA HA vs ISC Failover

**KEA HA** is the **modern replacement** provided by ISC for Kea DHCP.

### How it works

- HA is implemented as a **Kea hook library** (`libdhcp_ha.so`)
- Servers do **not synchronize leases directly** with each other.
- Instead, both servers use a **shared backend**:
    - PostgreSQL
    - MySQL
    - (or Cassandra in some designs)

The database becomes the **single source of truth**.

### Operating modes

- **Hot-Standby**
- **Load-Balancing**

(Conceptually similar to ISC Failover, but implemented very differently.)

### Lease handling

- Leases are written to the **shared database**.
- Both servers read/write from the same backend.
- HA communication is mainly used for:
    - Health checks
    - State coordination
    - Deciding which server should answer requests

There is **no per-lease synchronization over the network** between servers.

### Characteristics

- Loose coupling between DHCP servers
- No custom failover protocol for lease replication
- Easier recovery after failures
- Can be extended to **more complex topologies**
- Better observability and control (REST API, hooks, logging)

---

## 3. Key architectural differences

| Aspect        | ISC DHCP Failover        | KEA High Availability           |
| ------------- | ------------------------ | ------------------------------- |
| Technology    | Legacy ISC DHCP          | Modern Kea DHCP                 |
| Lease sync    | Direct server-to-server  | Shared database                 |
| Communication | Custom failover protocol | Lightweight HA control messages |
| Coupling      | Tight                    | Loose                           |
| Scalability   | Fixed pair (2 servers)   | More flexible                   |
| Recovery      | Complex state machines   | Database-driven                 |
| Extensibility | Very limited             | Hook-based, modular             |

---

## 4. Why ISC Failover was abandoned

ISC DHCP Failover:

- Was hard to maintain
    
- Had complex and fragile state transitions
    
- Could desynchronize under network issues
    
- Could not scale or evolve easily
    

KEA HA was designed to:

- Be **simpler and more reliable**
    
- Use **standard databases**
    
- Separate DHCP logic from HA logic
    
- Fit modern infrastructure (containers, APIs, orchestration)
    

---

## 5. One-sentence summary (good for exams / docs)

> **ISC DHCP Failover synchronizes leases directly between two tightly coupled servers using a custom protocol, while KEA High Availability relies on a shared database as the single source of truth and uses lightweight coordination for failover decisions.**
# Configuration
## zaldua1zerb1

```powershell title="KEA backup"
cp /etc/kea/kea-ctrl-agent.conf /etc/kea/kea-ctrl-agent.conf.bak
```

```json title="zaldua1zerb1 - /etc/kea/kea-ctrl-agent.conf"
{
  "Control-agent": {
    "http-host": "0.0.0.0",
    "http-port": 8000,
    "control-sockets": {
      "dhcp4": {
	    "comment": "zaldua1zerb1",
        "socket-type": "unix",
        "socket-name": "/run/kea/kea4-ctrl-socket"
      }
    }
  }
}
```

> Note
> - Runs the **Control Agent REST API** bound to **all interfaces** (`http-host: 0.0.0.0`) on **TCP 8000**.
> - It does **not serve DHCP by itself**; it exposes an HTTP endpoint to send commands to Kea services.
> - For DHCPv4 commands, it connects to the local Kea DHCP4 process via the **UNIX control socket** `/run/kea/kea4-ctrl-socket`.
> - This means: clients/peers talk HTTP → Control Agent, and Control Agent talks locally → DHCP4 through the socket.

> Note
> Control agent will be listening at the port 8000, you want to use different ports on the configured DHCP zones not to interfere between them.

## zaldua1zerb2

```powershell title="KEA backup"
cp /etc/kea/kea-ctrl-agent.conf /etc/kea/kea-ctrl-agent.conf.bak
```

```json title="zaldua1zerb2 - /etc/kea/kea-ctrl-agent.conf"
{
  "Control-agent": {
    "http-host": "192.168.42.5",
    "http-port": 8000,
    "control-sockets": {
      "dhcp4": {
	    "comment": "zaldua1zerb2",
        "socket-type": "unix",
        "socket-name": "/run/kea/kea4-ctrl-socket"
      }
    }
  }
}
```

> Note
> - Exposes the Control Agent REST API **only on** `192.168.42.5` (so it’s reachable from the _zaldua1_ network) on **TCP 8000**.
> - For DHCPv4 management, it forwards commands to the local DHCP4 daemon using `/run/kea/kea4-ctrl-socket`.
> - If TCP 8000 is already used on this VM, you must change `http-port` here (and update any HA peer URLs that point to it).

## zaldua2zerb1

```powershell title="KEA backup"
cp /etc/kea/kea-ctrl-agent.conf /etc/kea/kea-ctrl-agent.conf.bak
```

```json title="zaldua2zerb1 - /etc/kea/kea-ctrl-agent.conf"
{
  "Control-agent": {
    "http-host": "192.168.4.5",
    "http-port": 8000,
    "control-sockets": {
      "dhcp4": {
	    "comment": "zaldua2zerb1",
        "socket-type": "unix",
        "socket-name": "/run/kea/kea4-ctrl-socket"
      }
    }
  }
}
```

> Note
> - Exposes the Control Agent REST API **only on** `192.168.4.5` (reachable from the _zaldua2_ network) on **TCP 8000**.
> - Uses `/run/kea/kea4-ctrl-socket` to control the local DHCP4 server.
> - Same rule: **one process per TCP port** on a VM—if something else is using 8000, change `http-port` and adjust HA peer URLs accordingly.

# Restart service

```powershell title="restart KEA"
systemctl restart kea-ctrl-agent
systemctl restart kea-dhcp4-server
```

```powershell title="restart KEA"
systemctl status kea-ctrl-agent
systemctl status kea-dhcp4-server
```

# Check logs

```powershell title="check logs"
journalctl -u kea-dhcp4-server -f
```

Now that we have KEA High Availability active this should be the correct behaviour:

## zaldua1

| zaldua1zerb1 | zaldua1zerb2 | zaldua1bez1 lease (HA) | zaldua3bez1 lease (no HA) |
| ------------ | ------------ | ---------------------- | ------------------------- |
| on           | on           | **zaldua1zerb1**       | **zaldua1zerb1**          |
| off          | on           | **zaldua1zerb2**       | **none**                  |

### A1 zaldua1zerb1 = ON |  zaldua1zerb2 = ON

zaldua1zerb1:
	zaldua1bez1:
![[/linux/debian/services/kea/img/kea-ha-01.png]]
	zaldua3bez1:
![[/linux/debian/services/kea/img/kea-ha-04.png]]
zaldua1zerb2:
![[/linux/debian/services/kea/img/kea-ha-03.png]]
### A2 zaldua1zerb1 = OFF |  zaldua1zerb2 = ON

zaldua1zerb2:
	zaldua1bez1:
![[/linux/debian/services/kea/img/kea-ha-05.png]]
> Note
> When 10 clients tries to connect to zaldua1zerb1, the secondary server will stop waiting for zaldua1zerb1. And it will take the lead.

## zaldua2

| zaldua2zerb1 | zaldua1zerb1 | zaldua2bez1 lease (HA) | zaldua3bez1 lease (no HA) |
| ------------ | ------------ | ---------------------- | ------------------------- |
| on           | on           | **zaldua2zerb1**       | **zaldua1zerb1**          |
| off          | on           | **zaldua1zerb1**       | **zaldua1zerb1**          |

### B1 zaldua2zerb1 = ON |  zaldua1zerb1 = ON

zaldua2zerb1:
	zaldua2bez1:
![[/linux/debian/services/kea/img/kea-ha-07.png]]

zaldua1zerb1:
![[/linux/debian/services/kea/img/kea-ha-08.png]]
> Note
> zaldua1zerb1 receives broadcast IP request but zaldua2zerb1 applies offers the address.
### B2 zaldua2zerb1 = OFF |  zaldua1zerb1 = ON

zaldua1zerb1:
	zaldua2bez1:
![[/linux/debian/services/kea/img/kea-ha-09.png]]
> Note
> Same as before, when 10 clients tries connecting with zaldua2zerb1, zaldua1zerb1 will stop trying to connect with the primary and it will take the lead.

# Snapshot

```powershell title="snapshot"
VBoxManage snapshot "zaldua1zerb1" take "05_2_kea_ha_configuration" --description="This is the virtual machine after configuring a KEA DHCP with HA."
VBoxManage snapshot "zaldua1zerb2" take "05_2_kea_ha_configuration" --description="This is the virtual machine after configuring a KEA DHCP with HA."
VBoxManage snapshot "zaldua2zerb1" take "05_2_kea_ha_configuration" --description="This is the virtual machine after configuring a KEA DHCP with HA."
```

> Note
> Poweroff the machines first to avoid future conflicts.
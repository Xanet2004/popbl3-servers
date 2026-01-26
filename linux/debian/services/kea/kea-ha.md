This is the KEA High Availability configuration for the activity.

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
        "socket-type": "unix",
        "socket-name": "/run/kea/kea4-ctrl-socket"
      }
    }
  }
}
```

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
        "socket-type": "unix",
        "socket-name": "/run/kea/kea4-ctrl-socket"
      }
    }
  }
}
```

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
        "socket-type": "unix",
        "socket-name": "/run/kea/kea4-ctrl-socket"
      }
    }
  }
}
```

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

zaldua1zerb1:

# Snapshot

```powershell title="snapshot"
VBoxManage snapshot "zaldua1zerb1" take "05_2_kea_basic_configuration" --description="This is the virtual machine after configuring a basic KEA DHCP."
VBoxManage snapshot "zaldua2zerb1" take "05_2_kea_basic_configuration" --description="This is the virtual machine after configuring a basic KEA DHCP."
```

> Note
> Poweroff the machines first to avoid future conflicts.
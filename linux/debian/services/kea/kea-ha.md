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
	    "comment": "zaldua1zerb1",
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
	    "comment": "zaldua1zerb2",
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
	    "comment": "zaldua2zerb1",
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

Now that we have KEA High Availability active this should be the correct behaviour:

## zaldua1

| zaldua1zerb1 | zaldua1zerb2 | zaldua1bez1 lease (HA) | zaldua3bez1 lease (no HA) |
| ------------ | ------------ | ---------------------- | ------------------------- |
| on           | on           | **zaldua1zerb1**       | **zaldua1zerb1**          |
| off          | on           | **zaldua1zerb2**       | **none**                  |
| off          | off          | **none**               | **none**                  |
### A1 zaldua1zerb1 = ON |  zaldua1zerb2 = ON

zaldua1zerb1:
![[Pasted image 20260126212555.png]]

zaldua1zerb2:
![[Pasted image 20260126212748.png]]
### A1 zaldua1zerb1 = OFF |  zaldua1zerb2 = ON

zaldua1zerb1:
![[Pasted image 20260126212555.png]]

zaldua1zerb2:
![[Pasted image 20260126212620.png]]
### A1 zaldua1zerb1 = OFF |  zaldua1zerb2 = OFF

zaldua1zerb1:
![[Pasted image 20260126212555.png]]

zaldua1zerb2:
![[Pasted image 20260126212620.png]]

## zaldua2

| zaldua2zerb1 | zaldua1zerb1 | zaldua2bez1 lease (HA) | zaldua3bez1 lease (no HA) |
| ------------ | ------------ | ---------------------- | ------------------------- |
| on           | on           | **zaldua2zerb1**       | **zaldua1zerb1**          |
| on           | off          | **zaldua2zerb1**       | **none**                  |
| off          | on           | **zaldua1zerb1**       | **zaldua1zerb1**          |
| off          | off          | **none**               | **none**                  |
### A1 zaldua2zerb1 = ON |  zaldua1zerb1 = ON

zaldua2zerb1:
![[Pasted image 20260126212555.png]]

zaldua1zerb1:
![[Pasted image 20260126212620.png]]

### A1 zaldua2zerb1 = ON |  zaldua1zerb1 = OFF

zaldua2zerb1:
![[Pasted image 20260126212555.png]]

zaldua1zerb1:
![[Pasted image 20260126212620.png]]
### A1 zaldua2zerb1 = OFF |  zaldua1zerb1 = ON

zaldua2zerb1:
![[Pasted image 20260126212555.png]]

zaldua1zerb1:
![[Pasted image 20260126212620.png]]
### A1 zaldua2zerb1 = OFF |  zaldua1zerb1 = OFF

zaldua2zerb1:
![[Pasted image 20260126212555.png]]

zaldua1zerb1:
![[Pasted image 20260126212620.png]]


# Snapshot

```powershell title="snapshot"
VBoxManage snapshot "zaldua1zerb1" take "05_2_kea_basic_configuration" --description="This is the virtual machine after configuring a basic KEA DHCP."
VBoxManage snapshot "zaldua2zerb1" take "05_2_kea_basic_configuration" --description="This is the virtual machine after configuring a basic KEA DHCP."
```

> Note
> Poweroff the machines first to avoid future conflicts.
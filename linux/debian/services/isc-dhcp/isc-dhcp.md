# Tasks
- zaldua1zerb1 - Primary and Secondary
	- zaldua1zerb2 - Secondary
	- zaldua2zerb1 - Primary
- zaldua1zerb2 - Secondary
	- zaldua1zerb1 - Primary
- zaldua2zerb1 - Primary
	- zaldua1zerb1 - Secondary

# zaldua1zerb1

```powershell title="/etc/default/isc-dhcp-server"
INTERFACESv4="enp0s17 enp0s18 enp0s19"
INTERFACESv6=""
```

```powershell title="/etc/dhcp/dhcpd.conf"
option domain-name "zalduabat.eus";

default-lease-time 600;
max-lease-time 7200;

ddns-update-style none;

authoritative;

failover peer "zaldua1-failover" {
  primary;
  address 192.168.42.4;
  port 647;
  peer address 192.168.42.5;
  peer port 647;
  max-response-delay 60;
  max-unacked-updates 10;
  mclt 1800;
  split 128;
}

failover peer "zaldua2-failover" {
  secondary;
  address 192.168.4.4;
  port 647;
  peer address 192.168.4.5;
  peer port 647;
  max-response-delay 60;
  max-unacked-updates 10;
  mclt 1800;
}

subnet 192.168.42.0 netmask 255.255.254.0 {
  pool {
    range 192.168.42.100 192.168.42.200;
    failover peer "zaldua1-failover";
  }
  option domain-name-servers 192.168.42.4, 192.168.42.5;
  option domain-name "zalduabat.eus";
  option routers 192.168.42.2;
  option broadcast-address 192.168.43.255;
  default-lease-time 60;
  max-lease-time 720;
}

subnet 192.168.4.0 netmask 255.255.252.0 {
  pool {
    range 192.168.4.100 192.168.4.200;
    failover peer "zaldua2-failover";
  }
  option domain-name-servers 192.168.4.5, 192.168.4.4;
  option domain-name "zalduabat.eus";
  option routers 192.168.4.1;
  option broadcast-address 192.168.7.255;
  default-lease-time 60;
  max-lease-time 720;
}

subnet 192.168.1.128 netmask 255.255.255.128 {
  pool {
    range 192.168.1.160 192.168.1.200;
  }
  option domain-name-servers 192.168.1.132, 192.168.1.133;
  option domain-name "zalduabat.eus";
  option routers 192.168.1.129;
  option broadcast-address 192.168.1.255;
  default-lease-time 60;
  max-lease-time 720;
}
```

## zaldua1zerb2

```powershell title="/etc/default/isc-dhcp-server"
INTERFACESv4="enp0s17"
INTERFACESv6=""
```

```powershell title="/etc/dhcp/dhcpd.conf"
option domain-name "zalduabat.eus";

default-lease-time 600;
max-lease-time 7200;

ddns-update-style none;

failover peer "zaldua1-failover" {
  secondary;
  address 192.168.42.5;
  port 647;
  peer address 192.168.42.4;
  peer port 647;
  max-response-delay 60;
  max-unacked-updates 10;
  mclt 1800;
}

subnet 192.168.42.0 netmask 255.255.254.0 {
  pool {
    range 192.168.42.100 192.168.42.200;
    failover peer "zaldua1-failover";
  }
  option domain-name-servers 192.168.42.4, 192.168.42.5;
  option domain-name "zalduabat.eus";
  option routers 192.168.42.2;
  option broadcast-address 192.168.43.255;
  default-lease-time 60;
  max-lease-time 720;
}
```

## zaldua2zerb1

```powershell title="/etc/default/isc-dhcp-server"
INTERFACESv4="enp0s17"
INTERFACESv6=""
```

```powershell title="/etc/dhcp/dhcpd.conf"
option domain-name "zalduabat.eus";

default-lease-time 600;
max-lease-time 7200;

ddns-update-style none;

authoritative;

failover peer "zaldua2-failover" {
  primary;
  address 192.168.4.5;
  port 647;
  peer address 192.168.4.4;
  peer port 647;
  max-response-delay 60;
  max-unacked-updates 10;
  mclt 1800;
  split 128;
}

subnet 192.168.4.0 netmask 255.255.252.0 {
  pool {
    range 192.168.4.100 192.168.4.200;
    failover peer "zaldua2-failover";
  }
  option domain-name-servers 192.168.4.5, 192.168.4.4;
  option domain-name "zalduabat.eus";
  option routers 192.168.4.1;
  option broadcast-address 192.168.7.255;
  default-lease-time 60;
  max-lease-time 720;
}
```

# Tests

## DHCP - Network zaldua1 (192.168.42.0/23) - Failover Pair (zaldua1zerb1 ↔ zaldua1zerb2)

### A1. Normal operation: lease provided by primary (zaldua1zerb1 → zaldua1bez1)

zaldua1zerb1:
![[/linux/debian/services/isc-dhcp/img/isc-dhcp (3).png]]

zaldua1zerb2:
![[/linux/debian/services/isc-dhcp/img/isc-dhcp (4).png]]
### A2. Failover: primary down, lease provided by secondary (zaldua1zerb2 → zaldua1bez1)

Condition: zaldua1zerb1 stopped.

zaldua1zerb1:
![[/linux/debian/services/isc-dhcp/img/isc-dhcp (5).png]]

zaldua1zerb2:
![[/linux/debian/services/isc-dhcp/img/isc-dhcp (6).png]]
## B) DHCP - Network zaldua3 (192.168.1.128/25) - Single DHCP Server (zaldua1zerb1 only)

### B1. Normal operation: lease provided by zaldua1zerb1 (→ zaldua3bez1)

zaldua1zerb1:
![[/linux/debian/services/isc-dhcp/img/isc-dhcp (7).png]]

## C) DHCP - Network zaldua2 (192.168.4.0/22) - Failover Pair (zaldua2zerb1 ↔ zaldua1zerb1)
### C1. Normal operation: lease provided by primary (zaldua2zerb1 → zaldua2bez1)

zaldua2zerb1:
![[/linux/debian/services/isc-dhcp/img/isc-dhcp (15).png]]

zaldua1zerb1:
![[/linux/debian/services/isc-dhcp/img/isc-dhcp (14).png]]

### C2. Failover: primary down, lease provided by secondary (zaldua1zerb1 → zaldua2bez1)

zaldua2zerb1:
![[/linux/debian/services/isc-dhcp/img/isc-dhcp (1).png]]

zaldua1zerb1:
![[/linux/debian/services/isc-dhcp/img/isc-dhcp (2).png]]

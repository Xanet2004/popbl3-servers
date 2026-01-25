# Tasks
- **zaldua1zerb1**: 
	- **PRIMARY** de `zalduabat.eus` y `zalduahiru.eus`, 
	- **SECONDARY** de `zalduabi.eus`
- **zaldua1zerb2**: 
	- **SECONDARY** de `zalduabat.eus`
- **zaldua2zerb1**: 
	- **PRIMARY** de `zalduabi.eus`
- **zaldua3zerb1**: 
	- **SECONDARY** de `zalduahiru.eus`

# DNS named.conf
## zaldua1zerb1

```powershell title="zaldua1zerb1 - /etc/bind/named.conf.options"
// =====================
// zaldua1zerb1
// /etc/bind/named.conf.options
// =====================

acl "lan" {
    localhost;
    192.168.42.0/23;
    192.168.4.0/22;
    192.168.1.128/25;
};

options {
    directory "/var/cache/bind";

    listen-on { 127.0.0.1; 192.168.42.4; 192.168.4.4; 192.168.1.132; };
    listen-on-v6 { none; };

    recursion yes;
    allow-recursion { lan; };
    allow-query     { lan; };

    allow-transfer { none; };

    forwarders { 10.0.2.3; 1.1.1.1; 8.8.8.8; };
    forward only;

    dnssec-validation auto;
    auth-nxdomain no;
    minimal-responses yes;
};
```

```powershell title="zaldua1zerb1 - /bind/named.conf.local"
// =====================
// zaldua1zerb1
// /etc/bind/named.conf.local
// =====================

// PRIMARY (MASTER) - zalduabat.eus (zaldua1)
zone "zalduabat.eus" IN {
    type master;
    file "/etc/bind/zones/db.zalduabat.eus";
    allow-transfer { 192.168.42.5; };
    also-notify    { 192.168.42.5; };
    notify yes;
};

zone "42.168.192.in-addr.arpa" IN {
    type master;
    file "/etc/bind/zones/db.192.168.42";
    allow-transfer { 192.168.42.5; };
    also-notify    { 192.168.42.5; };
    notify yes;
};

zone "43.168.192.in-addr.arpa" IN {
    type master;
    file "/etc/bind/zones/db.192.168.43";
    allow-transfer { 192.168.42.5; };
    also-notify    { 192.168.42.5; };
    notify yes;
};

// PRIMARY (MASTER) - zalduahiru.eus (zaldua3)
zone "zalduahiru.eus" IN {
    type master;
    file "/etc/bind/zones/db.zalduahiru.eus";
    allow-transfer { 192.168.1.133; };
    also-notify    { 192.168.1.133; };
    notify yes;
};

zone "1.168.192.in-addr.arpa" IN {
    type master;
    file "/etc/bind/zones/db.192.168.1";
    allow-transfer { 192.168.1.133; };
    also-notify    { 192.168.1.133; };
    notify yes;
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

## zaldua1zerb2

```powershell title="zaldua1zerb2 - /etc/bind/named.conf.options"
// =====================
// zaldua1zerb2
// /etc/bind/named.conf.options
// =====================

acl "lan" {
    localhost;
    192.168.42.0/23;
    192.168.4.0/22;
    192.168.1.128/25;
};

options {
    directory "/var/cache/bind";

    listen-on { 127.0.0.1; 192.168.42.5; };
    listen-on-v6 { none; };

    recursion yes;
    allow-recursion { lan; };
    allow-query     { lan; };

    allow-transfer { none; };

    forwarders { 10.0.2.3; 1.1.1.1; 8.8.8.8; };
    forward only;

    dnssec-validation auto;
    auth-nxdomain no;
    minimal-responses yes;
};
```

```powershell title="zaldua1zerb2 - /bind/named.conf.local"
// =====================
// zaldua1zerb2
// /etc/bind/named.conf.local
// =====================

zone "zalduabat.eus" IN {
    type slave;
    masters { 192.168.42.4; };  // zaldua1zerb1
    file "/var/cache/bind/slave.db.zalduabat.eus";
};

zone "42.168.192.in-addr.arpa" IN {
    type slave;
    masters { 192.168.42.4; };
    file "/var/cache/bind/slave.db.192.168.42";
};

zone "43.168.192.in-addr.arpa" IN {
    type slave;
    masters { 192.168.42.4; };
    file "/var/cache/bind/slave.db.192.168.43";
};
```

## zaldua2zerb1

```powershell title="zaldua2zerb1 - /etc/bind/named.conf.options"
// =====================
// zaldua2zerb1
// /etc/bind/named.conf.options
// =====================

acl "lan" {
    localhost;
    192.168.42.0/23;
    192.168.4.0/22;
    192.168.1.128/25;
};

options {
    directory "/var/cache/bind";

    listen-on { 127.0.0.1; 192.168.4.5; };
    listen-on-v6 { none; };

    recursion yes;
    allow-recursion { lan; };
    allow-query     { lan; };

    allow-transfer { none; };

    forwarders { 10.0.2.3; 1.1.1.1; 8.8.8.8; };
    forward only;

    dnssec-validation auto;
    auth-nxdomain no;
    minimal-responses yes;
};
```

```powershell title="zaldua2zerb1 - /bind/named.conf.local"
// =====================
// zaldua2zerb1
// /etc/bind/named.conf.local
// =====================

// PRIMARY (MASTER) - zalduabi.eus
zone "zalduabi.eus" IN {
    type master;
    file "/etc/bind/zones/db.zalduabi.eus";
    allow-transfer { 192.168.4.4; };
    also-notify    { 192.168.4.4; };
    notify yes;
};

// Reverse 192.168.4.0/22 => /24s 4,5,6,7
zone "4.168.192.in-addr.arpa" IN {
    type master;
    file "/etc/bind/zones/db.192.168.4";
    allow-transfer { 192.168.4.4; };
    also-notify    { 192.168.4.4; };
    notify yes;
};

zone "5.168.192.in-addr.arpa" IN {
    type master;
    file "/etc/bind/zones/db.192.168.5";
    allow-transfer { 192.168.4.4; };
    also-notify    { 192.168.4.4; };
    notify yes;
};

zone "6.168.192.in-addr.arpa" IN {
    type master;
    file "/etc/bind/zones/db.192.168.6";
    allow-transfer { 192.168.4.4; };
    also-notify    { 192.168.4.4; };
    notify yes;
};

zone "7.168.192.in-addr.arpa" IN {
    type master;
    file "/etc/bind/zones/db.192.168.7";
    allow-transfer { 192.168.4.4; };
    also-notify    { 192.168.4.4; };
    notify yes;
};
```
## zaldua3zerb1

```powershell title="zaldua1zerb1 - /etc/bind/named.conf.options"
// =====================
// zaldua3zerb1
// /etc/bind/named.conf.options
// =====================

acl "lan" {
    localhost;
    192.168.42.0/23;
    192.168.4.0/22;
    192.168.1.128/25;
};

options {
    directory "/var/cache/bind";

    listen-on { 127.0.0.1; 192.168.1.133; };
    listen-on-v6 { none; };

    recursion yes;
    allow-recursion { lan; };
    allow-query     { lan; };

    allow-transfer { none; };

    forwarders { 10.0.2.3; 1.1.1.1; 8.8.8.8; };
    forward only;

    dnssec-validation auto;
    auth-nxdomain no;
    minimal-responses yes;
};
```


```powershell title="zaldua3zerb1 - /bind/named.conf.local"
// =====================
// zaldua3zerb1
// /etc/bind/named.conf.local
// =====================

zone "zalduahiru.eus" IN {
    type slave;
    masters { 192.168.1.132; }; // zaldua1zerb1 (IP en red zaldua3)
    file "/var/cache/bind/slave.db.zalduahiru.eus";
};

zone "1.168.192.in-addr.arpa" IN {
    type slave;
    masters { 192.168.1.132; };
    file "/var/cache/bind/slave.db.192.168.1";
};
```

# DNS zones

## Create zones folder (optional)

```powershell title="create zones folder"
mkdir /etc/bind/zones
```

## zaldua1zerb1

### zalduabat.eus

```powershell title="zaldua1zerb1 - /etc/bind/zones/db.zalduabat.eus"
$TTL 86400
@   IN  SOA ns1.zalduabat.eus. admin.zalduabat.eus. (
        2026012501 ; serial
        3600       ; refresh
        900        ; retry
        604800     ; expire
        86400      ; negative ttl
)

; Nameservers
@   IN  NS  ns1.zalduabat.eus.
@   IN  NS  ns2.zalduabat.eus.

; Glue A records for NS
ns1 IN  A   192.168.42.4
ns2 IN  A   192.168.42.5

; Hosts (building 1)
zaldua1zerb1 IN A 192.168.42.4
zaldua1zerb2 IN A 192.168.42.5
zaldua1bez1  IN A 192.168.42.10

; Service aliases (opcionales pero útiles)
dhcp1 IN CNAME zaldua1zerb1
dhcp2 IN CNAME zaldua1zerb2
smb1  IN CNAME zaldua1zerb1
smb2  IN CNAME zaldua1zerb2
```

```powershell title="zaldua1zerb1 - /etc/bind/zones/db.192.168.42"
$TTL 86400
@   IN  SOA ns1.zalduabat.eus. admin.zalduabat.eus. (
        2026012501
        3600
        900
        604800
        86400
)

@   IN  NS  ns1.zalduabat.eus.
@   IN  NS  ns2.zalduabat.eus.

4   IN  PTR zaldua1zerb1.zalduabat.eus.
5   IN  PTR zaldua1zerb2.zalduabat.eus.
10  IN  PTR zaldua1bez1.zalduabat.eus.
```

```powershell title="zaldua1zerb1 - /etc/bind/zones/db.192.168.43"
$TTL 86400
@   IN  SOA ns1.zalduabat.eus. admin.zalduabat.eus. (
        2026012501
        3600
        900
        604800
        86400
)
    IN  NS  ns1.zalduabat.eus.
    IN  NS  ns2.zalduabat.eus.
```

### zalduahiru.eus

```powershell title="zaldua3zerb1 - /etc/bind/zones/db.zalduahiru.eus"
$TTL 86400
@   IN  SOA ns1.zalduahiru.eus. admin.zalduahiru.eus. (
        2026012501
        3600
        900
        604800
        86400
)

@   IN  NS  ns1.zalduahiru.eus.
@   IN  NS  ns2.zalduahiru.eus.

; ns1 = zaldua1zerb1 (master en esta red)
; ns2 = zaldua3zerb1 (slave)
ns1 IN  A   192.168.1.132
ns2 IN  A   192.168.1.133

; Hosts (building 3)
zaldua1zerb1 IN A 192.168.1.132
zaldua3zerb1 IN A 192.168.1.133
zaldua3bez1  IN A 192.168.1.140

; Service aliases (opcionales)
www  IN CNAME zaldua3zerb1
smb  IN CNAME zaldua3zerb1
dhcp IN CNAME zaldua1zerb1
```

```powershell title="zaldua3zerb1 - /etc/bind/zones/db.192.168.1"
$TTL 86400
@   IN  SOA ns1.zalduahiru.eus. admin.zalduahiru.eus. (
        2026012501
        3600
        900
        604800
        86400
)

@   IN  NS  ns1.zalduahiru.eus.
@   IN  NS  ns2.zalduahiru.eus.

132 IN PTR zaldua1zerb1.zalduahiru.eus.
133 IN PTR zaldua3zerb1.zalduahiru.eus.
140 IN PTR zaldua3bez1.zalduahiru.eus.
```

## zaldua2zerb1

### zalduabi.eus

```powershell title="zaldua2zerb1 - /etc/bind/zones/db.zalduabi.eus"
$TTL 86400
@   IN  SOA ns1.zalduabi.eus. admin.zalduabi.eus. (
        2026012501
        3600
        900
        604800
        86400
)

@   IN  NS  ns1.zalduabi.eus.
@   IN  NS  ns2.zalduabi.eus.

; ns1 = zaldua2zerb1 (master)
; ns2 = zaldua1zerb1 (slave en esta zona)
ns1 IN  A   192.168.4.5
ns2 IN  A   192.168.4.4

; Hosts (building 2)
zaldua2zerb1 IN A 192.168.4.5
zaldua1zerb1 IN A 192.168.4.4
zaldua2bez1  IN A 192.168.4.10

; Service aliases (opcionales)
www  IN CNAME zaldua2zerb1
smb  IN CNAME zaldua2zerb1
dhcp IN CNAME zaldua2zerb1
```

```powershell title="zaldua2zerb1 - /etc/bind/zones/db.192.168.4"
$TTL 86400
@   IN  SOA ns1.zalduabi.eus. admin.zalduabi.eus. (
        2026012501
        3600
        900
        604800
        86400
)

@   IN  NS  ns1.zalduabi.eus.
@   IN  NS  ns2.zalduabi.eus.

4   IN  PTR zaldua1zerb1.zalduabi.eus.
5   IN  PTR zaldua2zerb1.zalduabi.eus.
10  IN  PTR zaldua2bez1.zalduabi.eus.
```

```powershell title="zaldua2zerb1 - /etc/bind/zones/db.192.168.5"
$TTL 86400
@   IN  SOA ns1.zalduabi.eus. admin.zalduabi.eus. (
        2026012501
        3600
        900
        604800
        86400
)
    IN  NS  ns1.zalduabi.eus.
    IN  NS  ns2.zalduabi.eus.
```

```powershell title="zaldua2zerb1 - /etc/bind/zones/db.192.168.6"
$TTL 86400
@   IN  SOA ns1.zalduabi.eus. admin.zalduabi.eus. (
        2026012501
        3600
        900
        604800
        86400
)
    IN  NS  ns1.zalduabi.eus.
    IN  NS  ns2.zalduabi.eus.
```

```powershell title="zaldua2zerb1 - /etc/bind/zones/db.192.168.7"
$TTL 86400
@   IN  SOA ns1.zalduabi.eus. admin.zalduabi.eus. (
        2026012501
        3600
        900
        604800
        86400
)
    IN  NS  ns1.zalduabi.eus.
    IN  NS  ns2.zalduabi.eus.
```

# Check everything is correct

```powershell title="all servers - check configurations"
named-checkconf
```

If everything is okay, restart the service

```powershell title="all servers - restart bind9"
systemctl restart bind9
systemctl status bind9
```

# Snapshot

```powershell title="snapshot"
VBoxManage snapshot "zaldua1zerb1" take "04_0_dns" --description="This is the virtual machine after configuring DNS."
VBoxManage snapshot "zaldua1zerb2" take "04_0_dns" --description="This is the virtual machine after configuring DNS."
VBoxManage snapshot "zaldua3zerb1" take "04_0_dns" --description="This is the virtual machine after configuring DNS."
VBoxManage snapshot "zaldua3zerb1" take "04_0_dns" --description="This is the virtual machine after configuring DNS."
```

> Note
> Poweroff the machines first to avoid future conflicts.
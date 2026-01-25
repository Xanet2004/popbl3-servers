# Tasks
- zaldua1zerb1 - Master and Slave
	- zaldua1zerb2 - Slave
	- zaldua2zerb1 - Master
	- zaldua3zerb1 - Slave
- zaldua1zerb2 - Secondary
	- zaldua1zerb1 - Master
- zaldua2zerb1 - Master
	- zaldua1zerb1 - Slave
- zaldua3zerb1 - Secondary
	- zaldua1zerb1 - Master

# DNS zones
# Summary

zaldua1zerb1

# zaldua1zerb1

```powershell title="zaldua1zerb1 - /etc/bind/named.conf.options"
// =====================
// zaldua1zerb1  (192.168.42.4 | 192.168.4.4 | 192.168.1.132)
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
    allow-query { lan; };

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
// zaldua1zerb1  (192.168.42.4 | 192.168.4.4 | 192.168.1.132)
// =====================

acl "slaves_z1" {
    192.168.42.5;   // zaldua1zerb2
    192.168.1.133;  // zaldua3zerb1
};

zone "zalduabat.eus" IN {
    type master;
    file "/etc/bind/zones/db.zalduabat.eus";
    allow-transfer { "slaves_z1"; };
    also-notify    { "slaves_z1"; };
    notify yes;
};

zone "42.168.192.in-addr.arpa" IN {
    type master;
    file "/etc/bind/zones/db.192.168.42";
    allow-transfer { "slaves_z1"; };
    also-notify    { "slaves_z1"; };
    notify yes;
};

zone "43.168.192.in-addr.arpa" IN {
    type master;
    file "/etc/bind/zones/db.192.168.43";
    allow-transfer { "slaves_z1"; };
    also-notify    { "slaves_z1"; };
    notify yes;
};

zone "1.168.192.in-addr.arpa" IN {
    type master;
    file "/etc/bind/zones/db.192.168.1";
    allow-transfer { "slaves_z1"; };
    also-notify    { "slaves_z1"; };
    notify yes;
};

zone "4.168.192.in-addr.arpa" IN {
    type slave;
    masters { 192.168.4.5; };   // zaldua2zerb1
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

# zaldua1zerb2

```powershell title="zaldua1zerb2 - /etc/bind/named.conf.options"
// =====================
// zaldua1zerb2  (192.168.42.5)
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
    allow-query { lan; };

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
// zaldua1zerb2 (192.168.42.5)
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

zone "1.168.192.in-addr.arpa" IN {
    type slave;
    masters { 192.168.42.4; };
    file "/var/cache/bind/slave.db.192.168.1";
};
```

# zaldua2zerb1

```powershell title="zaldua2zerb1 - /etc/bind/named.conf.options"
// =====================
// zaldua2zerb1  (192.168.4.5)
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
    allow-query { lan; };

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
// zaldua2zerb1 (192.168.4.5)
// =====================

acl "slave_z1" {
    192.168.4.4;    // zaldua1zerb1 (IP en red zaldua2)
};

zone "4.168.192.in-addr.arpa" IN {
    type master;
    file "/etc/bind/zones/db.192.168.4";
    allow-transfer { "slave_z1"; };
    also-notify    { "slave_z1"; };
    notify yes;
};

zone "5.168.192.in-addr.arpa" IN {
    type master;
    file "/etc/bind/zones/db.192.168.5";
    allow-transfer { "slave_z1"; };
    also-notify    { "slave_z1"; };
    notify yes;
};

zone "6.168.192.in-addr.arpa" IN {
    type master;
    file "/etc/bind/zones/db.192.168.6";
    allow-transfer { "slave_z1"; };
    also-notify    { "slave_z1"; };
    notify yes;
};

zone "7.168.192.in-addr.arpa" IN {
    type master;
    file "/etc/bind/zones/db.192.168.7";
    allow-transfer { "slave_z1"; };
    also-notify    { "slave_z1"; };
    notify yes;
};
```

# zaldua3zerb1

```powershell title="zaldua1zerb1 - /etc/bind/named.conf.options"
// =====================
// zaldua3zerb1  (192.168.1.133)
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
    allow-query { lan; };

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
// zaldua3zerb1 (192.168.1.133)
// =====================

zone "zalduabat.eus" IN {
    type slave;
    masters { 192.168.1.132; }; // zaldua1zerb1 (IP en red zaldua3)
    file "/var/cache/bind/slave.db.zalduabat.eus";
};

zone "1.168.192.in-addr.arpa" IN {
    type slave;
    masters { 192.168.1.132; };
    file "/var/cache/bind/slave.db.192.168.1";
};
```

# Snapshot

```powershell title="snapshot"
VBoxManage snapshot "zaldua1zerb1" take "04_0_dns" --description="This is the virtual machine after configuring DNS."
```

> Note
> Poweroff the machines first to avoid future conflicts.

# Update DHCPs



# Snapshot

```powershell title="snapshot"
VBoxManage snapshot "zaldua1zerb1" take "04_1_dns-dhcp" --description="This is the virtual machine after reconfiguring DHCP for DDNS."
```

> Note
> Poweroff the machines first to avoid future conflicts.
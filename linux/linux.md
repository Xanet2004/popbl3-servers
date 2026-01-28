This document explains how to apply several changes based on the [in-class activity](https://docs.google.com/document/d/1W8xz1h4jfhF81_PzgodD-vRKpa6XWWj4LW81x_kvXgE/edit?tab=t.0).

Asked changes are the followings:

| Before (activity)     | After (POPBL3)             |
| --------------------- | -------------------------- |
| NAT networks          | Internal networks          |
| ISC DHCP              | KEA DHCP                   |
| ISC failover          | KEA High Availability (HA) |
| ISC DHCP ↔ DNS (DDNS) | KEA DHCP ↔ DNS (DDNS)      |
# Index
- VBox
	- [Machines Creation](/linux/vbox/machines-creation/machines-creation.md)
	- [Networks Creation](/linux/vbox/networks-creation/networks-creation.md) ---
		- [NAT networks](/linux/vbox/networks-creation/nat-networks.md)
		- [Internal networks](/linux/vbox/networks-creation/internal-networks.md)
	- [Machines Installation](/linux/debian/machines-installation/machines-installation.md)
	- [Basic Configurations](/linux/debian/basic-configurations/basic-configurations.md)
- Debian
	- [Service installations](/linux/debian/service-installations/service-installations.md)
	- [Cloning machines](/linux/vbox/clone/clone.md)
	- [DHCP](/linux/debian/services/isc-dhcp/isc-dhcp.md)
	- [DNS](/linux/debian/services/dns/dns.md)
	- [KEA](/linux/debian/services/kea/kea.md)
		- [KEA installation](/linux/debian/services/kea/kea-installation.md) ---
		- [KEA IPv4 Configuration](/linux/debian/services/kea/kea-ipv4.md) ---
		- [KEA High Availabity](/linux/debian/services/kea/kea-ha.md) ---
		- [KEA DDNS](/linux/debian/services/kea/kea-dhcp-dns.md) ---

# Project requisites
## Network

| **Name**   | zaldua1         | zaldua2        | zaldua3          |
| ---------- | --------------- | -------------- | ---------------- |
| **Subnet** | 192.168.42.0/23 | 192.168.4.0/22 | 192.168.1.128/25 |
| **DHCP**   | Disabled        | Disabled       | Disabled         |
## Machines

| Conf         | zaldua1zerb1               | zaldua1zerb2               | zaldua2zerb1               | zaldua3zerb1               | zaldua1bez1       | zaldua2bez1       | zaldua3bez1       |
| ------------ | -------------------------- | -------------------------- | -------------------------- | -------------------------- | ----------------- | ----------------- | ----------------- |
| CPU          | 2                          | 2                          | 2                          | 2                          | 1                 | 1                 | 1                 |
| RAM          | 2GB                        | 2GB                        | 2GB                        | 2GB                        | 2GB               | 2GB               | 2GB               |
| VRAM         | 20MB                       | 20MB                       | 20MB                       | 20MB                       | 20MB              | 20MB              | 20MB              |
| HDD          | 120GB                      | 120GB                      | 120GB                      | 120GB                      | 120GB             | 120GB             | 120GB             |
| Firmware     | BIOS                       | BIOS                       | BIOS                       | BIOS                       | BIOS              | BIOS              | BIOS              |
| Mouse        | usbtablet                  | usbtablet                  | usbtablet                  | usbtablet                  | usbtablet         | usbtablet         | usbtablet         |
| Audio        | No                         | No                         | No                         | No                         | No                | No                | No                |
|              |                            |                            |                            |                            |                   |                   |                   |
| Nat1         | nat                        | nat                        | nat                        | nat                        | nat               | nat               | nat               |
|              |                            |                            |                            |                            |                   |                   |                   |
| intnet6      | zaldua1                    | zaldua1                    | zaldua2                    | zaldua3                    | zaldua1           | zaldua2           | zaldua3           |
| ip           | 192.168.42.4/23            | 192.168.42.5/23            | 192.168.4.5/22             | 192.168.1.133/25           | 192.168.42.10/23  | 192.168.4.10/22   | 192.168.1.140/25  |
| mac6         | 00:01:02:03:01:01          | 00:01:02:03:02:01          | 00:01:02:03:03:01          | 00:01:02:03:04:01          | 00:01:02:03:02:02 | 00:01:02:03:03:02 | 00:01:02:03:04:02 |
|              |                            |                            |                            |                            |                   |                   |                   |
| intnet7      | zaldua2                    |                            |                            |                            |                   |                   |                   |
| ip           | 192.168.4.4/22             |                            |                            |                            |                   |                   |                   |
| mac7         | 00:01:02:03:01:02          |                            |                            |                            |                   |                   |                   |
|              |                            |                            |                            |                            |                   |                   |                   |
| intnet8      | zaldua3                    |                            |                            |                            |                   |                   |                   |
| ip           | 192.168.1.132/25           |                            |                            |                            |                   |                   |                   |
| mac8         | 00:01:02:03:01:03          |                            |                            |                            |                   |                   |                   |
|              |                            |                            |                            |                            |                   |                   |                   |
| port forward | ssh -p 2204 user@localhost | ssh -p 2205 user@localhost | ssh -p 2245 user@localhost | ssh -p 2213 user@localhost |                   |                   |                   |

# Services

## Initial services

| Conf             | zaldua1zerb1                                                                 | zaldua1zerb2                                 | zaldua2zerb1                             | zaldua3zerb1               |
| ---------------- | ---------------------------------------------------------------------------- | -------------------------------------------- | ---------------------------------------- | -------------------------- |
| Networking       | 192.168.42.4/23<br>192.168.4.4/22<br>192.168.1.132/25                        | 192.168.42.5/23                              | 192.168.4.5/22                           | 192.168.1.133/25           |
| SSH              | Port: 2204                                                                   | Port: 2205                                   | Port: 2245                               | Port: 2213                 |
| KEA DHCP with HA | Primary<br>- Secondary: zaldua1zerb2<br>Secondary<br>- Primary: zaldua2zerb1 | <br><br>Secondary<br>- Primary: zaldua1zerb1 | Primary<br>- Secondary: zaldua1zerb1     | ---                        |
| DNS              | Master<br>- Slave:<br>Slave<br>- Master:                                     | <br><br>Slave<br>- Master:                   | Master<br>- Slave:<br>Slave<br>- Master: | <br><br>Slave<br>- Master: |

## Final services

This document will explain how to migrate from using ISC-DHCP-SERVER to KEA DHCP

| Conf       | zaldua1zerb1                                                                                | zaldua1zerb2                                 | zaldua2zerb1                         | zaldua3zerb1                                |
| ---------- | ------------------------------------------------------------------------------------------- | -------------------------------------------- | ------------------------------------ | ------------------------------------------- |
| Networking | 192.168.42.4/23<br>192.168.4.4/22<br>192.168.1.132/25                                       | 192.168.42.5/23                              | 192.168.4.5/22                       | 192.168.1.133/25                            |
| SSH        | Port: 2204                                                                                  | Port: 2205                                   | Port: 2245                           | Port: 2213                                  |
| DHCP       | Primary<br>- Secondary: zaldua1zerb2<br>Secondary<br>- Primary: zaldua2zerb1                | <br><br>Secondary<br>- Primary: zaldua1zerb1 | Primary<br>- Secondary: zaldua1zerb1 | ---                                         |
| DNS        | Master<br>- Slave: zaldua1zerb2<br>- Slave: zaldua3zerb1<br>Slave<br>- Master: zaldua2zerb1 | <br><br><br>Slave<br>- Master: zaldua1zerb1  | Master<br>- Slave: zaldua1zerb1 <br> | <br><br><br>Slave<br>- Master: zaldua1zerb1 |

# Todo

Egin beharrekoak:
- 4.4.1 Barne sareak (intnet)
	- [x] NAT sareak erabili beharrean, “Ariketak egiteko topologia barne edo intnet sareak” irudiko topologia erabili
			- [x] Aldaketa burutzeko erabilitako VboxManage komando guztiak zerrendatu eta azaldu
- 4.4.2 KEA instalazioa
	- [x] Ondorengo zerbitzarietan IPv4, High Availability (HA) eta DDNS funtzioak eskaintzeko beharrezko KEA moduloen instalazioa burutu eta pauso guztiak, komando eta guzti, zerrendatu (4.3 atalean bezala)
		- abizena1zerb1
		- abizena1zerb2
		- abizena2zerb1
- 4.4.3 KEA IPv4 konfigurazioa
	- [x] ISC dhcp server erabiliz egin den azpisare konfigurazio berdina burutu
		- [x] Egin behar izan diren aldaketak eta jarraitutako pausoak azaldu
		- [x] Komandoak zerrendatu
- 4.4.4 KEA High Availability (HA)
	- [x] ISC dhcp server erabiliz egin den failover konfigurazio berdina burutu
		- [x] `KEAren HA eta ISCren failover funtzioen arteko ezberdintasunak azaldu`
		- [x] Egin behar izan diren aldaketak eta jarraitutako pausoak azaldu
		- [x] Komandoak zerrendatu
- 4.4.5 KEA DDNS
	- [x] ISC dhcp server erabiliz DDNS funtzioa eskaintzeko egin den Bind9 eta ISC dhcp server zerbitzuen arteko konfigurazio berdina KEA erabiliz burutu
		- [x] Egin behar izan diren aldaketak eta jarraitutako pausoak azaldu
		- [x] Komandoak zerrendatu
		- [x] Bezeroek zerbitzarien bitartez lortu beharko dute sare konfigurazioa eta ebatzi beharko dituzte dns eskariak
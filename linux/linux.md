# Index
- [Networks Creation](/linux/vbox/networks-creation/networks-creation.md) ---
	- [NAT networks](/linux/vbox/networks-creation/nat-networks.md)
	- [Internal networks](/linux/vbox/networks-creation/internal-networks.md)
- [Machines Creation](/linux/vbox/machines-creation/machines-creation.md)
- [Machines Installation](/linux/debian/machines-installation/machines-installation.md)
- [Basic Configurations](/linux/debian/basic-configurations.md)
- KEA Services Installation(/linux/debian/.md) ---
- KEA IPv4 Configuration(/linux/debian/.md) ---
- KEA High Availability(/linux/debian/.md) ---
- KEA DDNS(/linux/debian/.md) ---

# Project requisites
## Network

| **Name**   | zaldua1         | zaldua2        | zaldua3          |
| ---------- | --------------- | -------------- | ---------------- |
| **Subnet** | 192.168.42.0/23 | 192.168.4.0/22 | 192.168.1.128/25 |
| **DHCP**   | Disabled        | Disabled       | Disabled         |
## Machines

| Conf | abizena1zerb1 | abizena1zerb2 | abizena2zerb1 | abizena3zerb1 | abizena1bez1 | abizena2bez1 | abizena3bez1 |
| ---- | ------------- | ------------- | ------------- | ------------- | ------------ | ------------ | ------------ |
|      |               |               |               |               |              |              |              |
|      |               |               |               |               |              |              |              |
|      |               |               |               |               |              |              |              |
|      |               |               |               |               |              |              |              |
|      |               |               |               |               |              |              |              |
|      |               |               |               |               |              |              |              |
|      |               |               |               |               |              |              |              |
|      |               |               |               |               |              |              |              |
|      |               |               |               |               |              |              |              |
|      |               |               |               |               |              |              |              |
|      |               |               |               |               |              |              |              |
|      |               |               |               |               |              |              |              |
# Service changes

| Antes (documento) | Ahora (POPBL3)    |
| ----------------- | ----------------- |
| NAT networks      | Internal networks |
| ISC DHCP          | KEA DHCP          |
| ISC failover      | KEA HA            |
| ISC DDNS          | KEA DDNS          |
# Todo

Egin beharrekoak:
- 4.4.1 Barne sareak (intnet)
	- [ ] NAT sareak erabili beharrean, “Ariketak egiteko topologia barne edo intnet sareak” irudiko topologia erabili
		- [ ] Aldaketa burutzeko erabilitako VboxManage komando guztiak zerrendatu eta azaldu
- 4.4.2 KEA instalazioa
	- [ ] Ondorengo zerbitzarietan IPv4, High Availability (HA) eta DDNS funtzioak eskaintzeko beharrezko KEA moduloen instalazioa burutu eta pauso guztiak, komando eta guzti, zerrendatu (4.3 atalean bezala)
		- abizena1zerb1
		- abizena1zerb2
		- abizena2zerb1
- 4.4.3 KEA IPv4 konfigurazioa
	- [ ] ISC dhcp server erabiliz egin den azpisare konfigurazio berdina burutu
		- [ ] Egin behar izan diren aldaketak eta jarraitutako pausoak azaldu
		- [ ] Komandoak zerrendatu
- 4.4.4 KEA High Availability (HA)
	- [ ] ISC dhcp server erabiliz egin den failover konfigurazio berdina burutu
		- [ ] KEAren HA eta ISCren failover funtzioen arteko ezberdintasunak azaldu
		- [ ] Egin behar izan diren aldaketak eta jarraitutako pausoak azaldu
		- [ ] Komandoak zerrendatu
- 4.4.5 KEA DDNS
	- [ ] ISC dhcp server erabiliz DDNS funtzioa eskaintzeko egin den Bind9 eta ISC dhcp server zerbitzuen arteko konfigurazio berdina KEA erabiliz burutu
		- [ ] Egin behar izan diren aldaketak eta jarraitutako pausoak azaldu
		- [ ] Komandoak zerrendatu
		- [ ] Bezeroek zerbitzarien bitartez lortu beharko dute sare konfigurazioa eta ebatzi beharko dituzte dns eskariak
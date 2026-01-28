# Index
- [VMachine and Network creation](/windows/vmachine-creation/vmachine-network-creation.md)
- [VMachine installation](/windows/vmachine-installation/vmachine-installation.md)
- [Basic configuration](/windows/basic-configuration/basic-configuration.md)
- [Installing services](/windows/installing-services/installing-services.md)
- [Active Directory](/windows/active-directory/active-directory.md)
	- [Users and OUs](/windows/active-directory/users-and-ous.md)
	- [Client inside child domain](/windows/active-directory/client-inside-domain.md)
- [Checks AD DNS and Client](/windows/checks-ad-dns-client/checks-ad-dns-client.md)
- [Demo](/windows/demo/demo.md)
- [Presentation](/windows/presentation/presentation.md)

# Project requisites
## Network

All machines are on the same network:

| Property         | Configuration  |
| ---------------- | -------------- |
| **Network Name** | wpopblnetwork  |
| **Subnet**       | 192.168.2.0/24 |
| **DHCP**         | Disabled       |
## Machines

| Property             | Server 1            | Server 2            | Client            |
| -------------------- | ------------------- | ------------------- | ----------------- |
| **MAC Address**      | 00:01:02:03:04:11   | 00:01:02:03:04:12   | 00:01:02:03:04:13 |
| **IP Address**       | 192.168.2.101/24    | 192.168.2.102/24    | 192.168.2.200/24  |
| **Gateway**          | 192.168.2.1         | 192.168.2.1         | 192.168.2.1       |
| **CPU**              | 2                   | 2                   | 4                 |
| **RAM**              | 2048MB              | 2048MB              | 4096MB            |
| **OS**               | Windows Server 2025 | Windows Server 2025 | Windows 11        |
| **Username**         | Administrator       | Administrator       | Bezeroa           |
| **Password**         | 123456789aA@        | 123456789aA@        | 123456789aA@      |
| **Hostname**         | server1             | server2             | bezeroa           |
| **Domain**           | proiektuak.edu      | pbl.proiektuak.edu  | -                 |
| **Active Directory** | Yes                 | Yes                 | No                |
| **DNS**              | Yes                 | Yes                 | No                |

# Todo

Jarraitu beharreko pausuak: 
- [x] Server1 makina birtualean ondorengo konfigurazioa burutu beharko da: 
	- [x] Domeinuko kontrolagailu bezala lan egiteko Active Directory eta DNS-a instalatu 
	- [x] Domeinu berri bat sortu (proiektuak.edu) eta promozinatu domeinu kontrolatzailea izatera. 
	- [x] Lau erabiltzaile eta bi antolaketa unitate sortu. Erabiltzaile hauek binaka antolaketa unitate horien barruan sartuko dira. 
		- [x] Izenak eta pasahitzak zuek aukeratuko dituzue 
- [x] Server2 makina birtualean ondorengo konfigurazioa burutu beharko da: 
	- [x] Domeinuko kontrolagailu bezala lan egiteko Active Directory eta DNS-a instalatu 
	- [x] Server1-ek sortutako zuhaitz berdinean, domeinu berri bat sortu (pbl.proiektuak.edu) eta child domain izatera propmozionatu. 
- [x] Client makina birtualean ondorengo konfigurazioa burutu beharko da: 
	- [x] Ekipoa server2 makina birtualean sortutako child domeinuan (pbl.proiektuak.edu) sartu 
- [x] Egiaztapenak: 
	- [x] DNS erregistroak eta domeinu-kontrolatzaileen arteko komunikazioa ondo doazela ziurtatu 
	- [x] Child domain bat sortzean, Bi noranzko (Two-way) eta Iragankor (Transitive) konfiantza bat automatikoki sortzen da Root eta Child domeinuen artean. Konfiantza erlazio hori behar bezala sortu dela frogatu 
	- [x] Client makina birtualean, “proiektuak.edu” domeinuan sortutako erabiltzaileekin sesioa hasi daitekeela egiaztatu
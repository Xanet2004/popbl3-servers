## Windows

- Presentation
- Demo
- Fix machine installation paths for github

## Linux
- Kea
	- "topologia barne edo intnet sareak"
		- VboxManage komandoak
		- ALDAKETAK BURUTZEKO -> Jarri nola aldatu natnetwork sareetatik inet sareetara (erakutxi nat network sareak ezabatzen)
	- Instalazioak (abizena1zerb1, abizena1zerb2, abizena2zerb1)
		- IPv4
		- High Availability
		- DDNS
	- Konfigurazioak
		- Konfigurazio berdina lortu ber dia KEA erabiliz eta gero aldatu?
		- IPv4
			- ISC DHCP -> Sub dominios "azpisare **konf berdina**"
			- ALDAKETAK AZALDU
		- High Availability (HA)
			- ISC DHCP -> Failover "failover **berdina burutu**"
			- HA eta ISC ezberdintasunak azaldu
		- DDNS
			- DHCP + DDNS "konf **berdina burutu**"
			- Aldaketak azaldu
			- Zerbitzaria router moduan bezeroentzat
				- Bezeroek -> dns eta router moduan erabili zerbitzariak
- Demo
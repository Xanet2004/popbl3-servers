# Check DNS server is localhost

```powershell title="Check DNS server is localhost"
# View network adapters 
Get-NetAdapter

# Get network adapter configuration 
Get-NetIPConfiguration -InterfaceAlias "Ethernet"
```

![[/windows/checks-ad-dns-client/outputs/img/check-dns/check-dns-01.png]]

> Note
> ::1 means Server1 is using itself as the DNS resolver.

# Check DNS is running and using Server1 as resolver

```powershell title="Check DNS is installed and running"
# Check DNS is installed and running
Get-Service -Name DNS

# Check DNS translations
Resolve-DnsName -Name proiektuak.edu -Server (Get-ADDomainController).Hostname
```

![[/windows/checks-ad-dns-client/outputs/img/check-dns/check-dns-02.png]]

> Note
> DNS is running and Server1 is using its IP as the DNS resolver.
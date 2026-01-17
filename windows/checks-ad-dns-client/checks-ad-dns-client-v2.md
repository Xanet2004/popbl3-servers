This document contains the steps to verify that the Active Directory, DNS, child domain, and client configuration are correct.

# Index
1. Basic connectivity between DNS and Domain Controllers
2. Check if trust has been correctly created between root and child servers
# 1. Basic connectivity between DNS and Domain Controllers

# Active Directory

```powershell
# Check AD service state and DC availability

## Check AD is installed and running
Get-Service -Name NTDS

## Check domain controller is available
Test-Connection -ComputerName (Get-ADDomainController).Hostname
```

> Outputs: [Server1 - check connectivity](/windows/checks-ad-dns-client/outputs/server1-checkConnectivity.md)
### Summary
- Active Directory is correctly installed and enabled.
- The domain controller is available.

# DNS

```powershell
# Check DNS server is localhost

## View network adapters 
Get-NetAdapter

## Get network adapter configuration 
Get-NetIPConfiguration -InterfaceAlias "Ethernet"

---

# Check DNS is installed and running

## Get DNS
Get-Service -Name DNS

## Check DNS translations
Resolve-DnsName -Name proiektuak.edu -Server (Get-ADDomainController).Hostname
```

> Outputs: [Server1 - check connectivity](/windows/checks-ad-dns-client/outputs/server1-checkConnectivity.md)
### Summary
- DNS server is localhost.
- DNS translations are as expected.

# 2. Check if trust has been correctly created between root and child servers

```powershell title="server1 - check domain"
# AD Trust information
Get-ADTrust -Filter * | Format-List *

# Lightweight nltest checks (run on each DC)
nltest /domain_trusts
```

> Outputs: [Server1 - check domain](/windows/checks-ad-dns-client/outputs/server1-checkDomain.md)

### Summary
The automatic **two-way and transitive trust** between the root (`proiektuak.edu`) and child (`pbl.proiektuak.edu`) domains has been correctly created and verified.


This document contains the steps to verify that the Active Directory, DNS, child domain, and client configuration are correct.

# Index
1. Basic connectivity between DNS and Domain Controllers
2. Check if trust has been correctly created between root and child servers
3. Check client login

# 1. Basic connectivity between DNS and Domain Controllers

Verify that all DNS records exist and that communication between domain controllers is working.
# Active Directory

```powershell title="Check AD service state and DC availability"
# Check AD is installed and running
Get-Service -Name NTDS

# Check domain controller is available
Test-Connection -ComputerName (Get-ADDomainController).Hostname
```

> Outputs: [Check Active Directory](/windows/checks-ad-dns-client/outputs/check-active-directory.md)
### Summary
- Active Directory is correctly installed and enabled.
- The domain controller is available.

# DNS

```powershell title="Check DNS server is localhost"
# View network adapters 
Get-NetAdapter

# Get network adapter configuration 
Get-NetIPConfiguration -InterfaceAlias "Ethernet"
```

```powershell title="Check DNS is installed and running"
# Check DNS is installed and running
Get-Service -Name DNS

# Check DNS translations
Resolve-DnsName -Name proiektuak.edu -Server (Get-ADDomainController).Hostname
```

> Outputs: [Check DNS](/windows/checks-ad-dns-client/outputs/check-dns.md)
### Summary
- DNS server is localhost.
- DNS translations are as expected.

# 2. Check if trust has been correctly created between root and child servers

Ensure that a **two-way** and **transitive trust** was automatically created between the Root domain (`proiektuak.edu`) and the Child domain (`pbl.proiektuak.edu`).

```powershell title="Check trust"
# Check TRUST information
Get-ADTrust -Filter * | Format-List *

# Lightweight nltest checks (run on each DC)
nltest /domain_trusts
```

> Outputs: [Check AD TRUST](/windows/checks-ad-dns-client/outputs/check-active-directory-trust.md)
### Summary
The automatic **two-way and transitive trust** between the root (`proiektuak.edu`) and child (`pbl.proiektuak.edu`) domains has been correctly created and verified.

# 3. Check client login

Verify that the client VM can log in with users from the **Root domain** (`proiektuak.edu`) or the **Child domain** (`pbl.proiektuak.edu`) as appropriate.

```powershell title="Check domain"
# Check client domain
(Get-WmiObject Win32_ComputerSystem).Domain
```

```powershell title="Check domain with different sessions"
# ssh
ssh -p 1113 xetxezarreta@localhost

# Check simple local/domain info
whoami
```

> Outputs: [Check client login](/windows/checks-ad-dns-client/outputs/check-client-login.md)
### Summary
The client VM can successfully log in using **root domain users**, verifying that the trust and domain integration are working correctly.
# Check AD is running

```powershell title="Check AD service state and DC availability"
# Check AD is installed and running
Get-Service -Name NTDS
```

![[/windows/checks-ad-dns-client/outputs/img/check-active-directory/check-active-directory-01.png]]

> Note
> Active Directory is running correctly.
# Check DC availability

```powershell title="Check DC availability"
# Check domain controller is available
Test-Connection -ComputerName (Get-ADDomainController).Hostname
```

![[/windows/checks-ad-dns-client/outputs/img/check-active-directory/check-active-directory-02.png]]

> Note
> Domain Controller is available at 192.168.2.101
Install required Windows roles and features for AD and DNS.

This installations will be done in BOTH SERVERS.
# Index
- Active Directory
- DNS
- GPMC

# Active Directory

```powershell title="servers - ad installation"
Add-WindowsFeature -Name "ad-domain-services" -IncludeAllSubFeature -IncludeManagementTools
# Success Restart Needed Exit Code      Feature Result
# ------- -------------- ---------      --------------
# True    No             Success        {Active Directory Domain Services, Group P...
```

```powershell title="servers - gpmc status"
# Active Directory Domain Services (AD DS)
Get-Service -Name NTDS | Select-Object Status, Name, DisplayName
```

# DNS

```powershell title="servers - dns installation"
Add-WindowsFeature -Name "dns" -IncludeAllSubFeature -IncludeManagementTools
# Success Restart Needed Exit Code      Feature Result
# ------- -------------- ---------      --------------
# True    No             Success        {DNS Server, DNS Server Tools}
```

```powershell title="servers - gpmc status"
# DNS Server
Get-Service -Name DNS | Select-Object Status, Name, DisplayName
```

# GPMC

```powershell title="servers - gpmc installation"
Add-WindowsFeature -Name "gpmc" -IncludeAllSubFeature -IncludeManagementTools
# Success Restart Needed Exit Code      Feature Result
# ------- -------------- ---------      --------------
# True    No             NoChangeNeeded {}
```

```powershell title="servers - gpmc status"
# Group Policy Management Console (GPMC) - feature installed (not a service)
Get-WindowsFeature -Name GPMC | Select-Object DisplayName, InstallState
```
# Snapshot

```powershell title="stop computers"
Stop-Computer -Force
```

```powershell title="snapshots"
VBoxManage snapshot "server1" take "03_servicesInstalled" --description="This is the virtual machine after installing the required services (AD, DNS, GPC)."

VBoxManage snapshot "server2" take "03_servicesInstalled" --description="This is the virtual machine after installing the required services (AD, DNS, GPC)."
```
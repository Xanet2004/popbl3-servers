# Check Domain

```powershell title="client - check domain"
# Confirm client domain
(Get-WmiObject Win32_ComputerSystem).Domain
```

![[/windows/checks-ad-dns-client/outputs/img/check-client-login/check-client-login-01.png]]

> Note
> The client is joined to the child domain (pbl.proiektuak.edu), as shown by the Domain property.

# Local user

```powershell title="client - Bezeroa"
# ssh
ssh -p 1113 Bezeroa@localhost

# Show simple local/domain info
whoami
```

![[/windows/checks-ad-dns-client/outputs/img/check-client-login/check-client-login-02.png]]

> Note
> Local user 'Bezeroa' can log in, confirming basic local access works.

# Domain users

```powershell title="client - xetxezarreta"
# ssh
ssh -p 1113 xetxezarreta@localhost

# Show simple local/domain info
whoami
```

![[/windows/checks-ad-dns-client/outputs/img/check-client-login/check-client-login-03.png]]

```powershell title="client - igaritano"
# ssh
ssh -p 1113 igaritano@localhost

# Show simple local/domain info
whoami
```

![[/windows/checks-ad-dns-client/outputs/img/check-client-login/check-client-login-04.png]]

```powershell title="client - xzaldua"
# ssh
ssh -p 1113 xzaldua@localhost

# Show simple local/domain info
whoami
```

![[/windows/checks-ad-dns-client/outputs/img/check-client-login/check-client-login-05.png]]

```powershell title="client - imaritxalar"
# ssh
ssh -p 1113 imaritxalar@localhost

# Show simple local/domain info
whoami
```

![[/windows/checks-ad-dns-client/outputs/img/check-client-login/check-client-login-06.png]]

> Note
> Root domain users (xzaldua, xetxezarreta, ...) can log in, confirming cross-domain trust is functional.
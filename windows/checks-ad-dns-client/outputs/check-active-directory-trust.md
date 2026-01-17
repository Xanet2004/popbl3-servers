# Check domain trust information

```powershell title="server1 - Check domain trust"
# AD view
Get-ADTrust -Filter * | Format-List *
```

![[/windows/checks-ad-dns-client/outputs/img/check-active-directory-trust/check-active-directory-trust-01.png]]

> Note
> Get-ADTrust **shows a bi-directional trust** between the root and child domains, confirming two-way trust.
> DisallowTransivity is False and IntraForest is True, indicating **the trust is transitive** within the forest.

# Show domain trusts

```powershell title="Show domain trusts"
# Lightweight nltest checks (run on each DC)
nltest /domain_trusts
```

> Server1:

![[/windows/checks-ad-dns-client/outputs/img/check-active-directory-trust/check-active-directory-trust-02.png]]

> Server2:

![[/windows/checks-ad-dns-client/outputs/img/check-active-directory-trust/check-active-directory-trust-03.png]]

> Note
> nltest /domain_trusts shows direct inbound and outbound trust, **further confirming a functional two-way and transitive trust**.
